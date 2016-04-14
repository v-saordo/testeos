<properties
	pageTitle="Aplicación .NET de niveles múltiples | Microsoft Azure"
	description="Un tutorial .NET que le ayuda a desarrollar una aplicación de varios niveles en Azure que usa colas del Bus de servicio para comunicarse entre los niveles."
	services="service-bus"
	documentationCenter=".net"
	authors="sethmanheim"
	manager="timlt"
	editor=""/>

<tags
	ms.service="service-bus"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="10/07/2015"
	ms.author="sethm"/>

# Aplicación de niveles múltiples .NET con colas del Bus de servicio de Azure

## Introducción

Desarrollar para Microsoft Azure es muy sencillo con Visual Studio y el SDK de Azure para .NET gratis. Si todavía no tiene Visual Studio, el SDK instalará automáticamente Visual Studio Express para que pueda comenzar a desarrollar gratis para Azure. En este artículo se asume que no tiene ninguna experiencia previa con Azure. Al finalizar este tutorial, tendrá una aplicación que usa varios recursos de Azure que se ejecutan en su entorno local y que muestra cómo funciona una aplicación de niveles múltiples.

Aprenderá a:

-   Cómo habilitar el equipo para el desarrollo de Azure con una única descarga e instalación.
-   Cómo utilizar Visual Studio para desarrollar para Azure.
-   Cómo crear una aplicación de niveles múltiples en Azure mediante el uso de roles web y de trabajo.
-   Cómo comunicarse entre niveles mediante las colas del bus de servicio.

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

En este tutorial compilará y ejecutará la aplicación de niveles múltiples en un servicio en la nube de Azure. El front-end será un rol web de ASP.NET MVC y el back-end será un rol de trabajo Puede crear la misma aplicación de niveles múltiples con el front-end como un proyecto web que se implementa en un sitio web de Azure en lugar de en un servicio en la nube. Para obtener instrucciones acerca de cómo realizar de manera diferente un front-end de sitio web de Azure, consulte la sección [Pasos siguientes](#nextsteps).

La siguiente captura de pantalla muestra la aplicación completada.

![][0]

> [AZURE.NOTE]Azure también proporciona funcionalidad de colas de almacenamiento. Para obtener más información sobre las colas de almacenamiento de Azure y las colas del bus de servicio, consulte [Colas de Microsoft Azure y Colas de Service Bus de Microsoft Azure: comparación y diferencias][sbqueuecomparison].

## Información general del escenario: comunicación entre roles

Para enviar una orden para su procesamiento, el componente de la interfaz de usuario de front-end, que se ejecuta en el rol web, debe interactuar con la lógica del nivel intermedio que se ejecuta en el rol de trabajo. En ese ejemplo se utiliza la mensajería asíncrona del bus de servicio para la comunicación entre los niveles.

El uso de la mensajería asíncrona entre los niveles de web e intermedio desacopla los dos componentes. Al contrario que en la mensajería directa (es decir, TCP o HTTP), el nivel web no se conecta directamente al nivel intermedio; por el contrario, inserta unidades de trabajo, como mensajes, en el Bus de servicio, que los conserva de manera confiable hasta que el nivel intermedio esté preparado para consumirlas y procesarlas.

El bus de servicio ofrece dos entidades para admitir la mensajería asíncrona: colas y temas. Con las colas, cada mensaje enviado a la cola lo consume un único receptor. Los temas admiten el patrón de publicación/suscripción, en el cual cada mensaje publicado está disponible para una suscripción registrada con el tema. Cada suscripción mantiene lógicamente su propia cola de mensajes. Las suscripciones también se pueden configurar con reglas de filtro que restringen el conjunto de mensajes pasados a la cola de suscripción a aquellos que coinciden con el filtro. En este ejemplo se usan las colas de bus de servicio.

![][1]

Este mecanismo de comunicación tiene varias ventajas sobre la mensajería directa:

-   **Desacoplamiento temporal.** Con el patrón de mensajería asincrónica, los productores y los consumidores no tienen que estar en línea al mismo tiempo. El bus de servicio almacena los mensajes de manera confiable hasta que la parte consumidora esté lista para recibirlos. De esta forma, los componentes de la aplicación distribuida se pueden desconectar, ya sea voluntariamente, por ejemplo, para mantenimiento, o debido a un bloqueo del componente, sin que afecte al sistema en su conjunto. Es más, puede que la aplicación consumidora solo necesite estar en línea durante determinados períodos del día.

-   **Redistribución de carga.** En muchas aplicaciones, la carga del sistema varía con el tiempo, mientras que el tiempo de procesamiento requerido para cada unidad de trabajo suele ser constante. La intermediación de productores y consumidores de mensajes con una cola implica que la aplicación consumidora (el trabajador) solo necesita ser aprovisionada para acomodar una carga promedio en lugar de una carga pico. La profundidad de la cola aumenta y se contrae a medida que varíe la carga entrante, lo que permite ahorrar dinero directamente en función de la cantidad de infraestructura requerida para dar servicio a la carga de la aplicación.

-   **Equilibrio de carga.** A medida que aumenta la carga, se pueden agregar más procesos de trabajo para que puedan leerse desde la cola. Cada mensaje se procesa únicamente por uno de los procesos de trabajo. Además, este equilibrio de carga permite el uso óptimo de las máquinas de trabajo aunque estas máquinas difieran en términos de capacidad de procesamiento, ya que extraerán mensajes a su frecuencia máxima propia. Este patrón con frecuencia se denomina patrón de *consumo de competidor*.

    ![][2]

En las secciones siguientes se explica el código que implementa esta arquitectura.

## Configuración del entorno de desarrollo

Antes de comenzar a desarrollar su aplicación de Azure, descargue las herramientas y configure el entorno de desarrollo:

1.  Para instalar el SDK de Azure para .NET, haga clic en el vínculo siguiente.

    [Obtención de herramientas y de SDK][]

2. 	Haga clic en el vínculo de la versión de Visual Studio que está utilizando. En los pasos de este tutorial se usa Visual Studio 2013.

	![][32]

4. 	Cuando se le solicite ejecutar o guardar el archivo de instalación, haga clic en **Ejecutar**.

    ![][3]

5.  En el instalador de plataforma web, haga clic en **Instalar** y continúe con la instalación.

    ![][33]

6.  Cuando la instalación se complete, dispondrá de todo lo necesario para iniciar el desarrollo de la aplicación. El SDK incluye herramientas que le permiten desarrollar aplicaciones de Azure en Visual Studio. Si no tiene instalado Visual Studio, instale también el programa gratuito Visual Studio Express para Web.

## Configuración del espacio de nombres de Bus de servicio

El siguiente paso es crear un espacio de nombres de servicio y obtener una clave de Firma de acceso compartido (SAS). Un espacio de nombres proporciona un límite de aplicación para cada aplicación que se expone a través del Bus de servicio. El sistema genera una clave SAS cuando se crea un espacio de nombres de servicio. La combinación del espacio de nombres y la clave SAS proporciona las credenciales de Bus de servicio para autenticar el acceso a una aplicación.

### Configuración de un espacio de nombres mediante el Portal de Azure clásico

1.  Inicie sesión en el [Portal de Azure clásico][].

2.  En el panel de navegación izquierdo del Portal, haga clic en **Bus de servicio**.

3.  En el panel inferior del Portal, haga clic en **Crear**.

    ![][6]

4.  En la página **Agregar un nuevo espacio de nombres**, escriba un nombre de espacio de nombres. El sistema realiza la comprobación automáticamente para ver si el nombre está disponible.

    ![][7]

5.  Después de asegurarse de que el nombre de espacio de nombres está disponible, seleccione el país o región en el que debe hospedarse el espacio de nombres (asegúrese de que usa el mismo país o la misma región en los que está realizando la implementación de los recursos de proceso). Además, asegúrese de seleccionar **Mensajería** en el espacio de nombres de campo **Tipo** y **Estándar** en el campo **Capa de mensajería**.

    > [AZURE.IMPORTANT]Seleccione la **misma región** que vaya a elegir para la implementación de la aplicación. Con esto conseguirá el máximo rendimiento.

6.  Haga clic en la marca de verificación Aceptar. El sistema crea ahora el espacio de nombres del servicio y lo habilita. Es posible que tenga que esperar algunos minutos mientras el sistema realiza el aprovisionamiento de los recursos para la cuenta.

	![][27]

7.  En la ventana principal, haga clic en el nombre del espacio de nombres de servicio.

	![][30]

8. Haga clic en **Información de conexión**.

	![][31]

9.  En el panel **Información de conexión de acceso**, encuentre la cadena de conexión que contiene la clave SAS y el nombre de la clave.

    ![][35]

10.  Tome nota de estas credenciales o cópielas en el Portapapeles.

## Creación de un rol web

En esta sección, va a crear el front-end de la aplicación. En primer lugar, va a crear las distintas páginas que mostrará la aplicación. Después, agregará el código para enviar los elementos a una cola del Bus de servicio y mostrar la información de estado sobre la cola.

### Creación del proyecto

1.  Con privilegios de administrador, inicie Microsoft Visual Studio 2013 o Microsoft Visual Studio Express. Para iniciar Visual Studio con privilegios de administrador, haga clic con el botón secundario **Microsoft Visual Studio 2013 (o Microsoft Visual Studio Express)** y, a continuación, haga clic en **Ejecutar como administrador**. El emulador de proceso de Azure, descrito posteriormente en este artículo, requiere que se inicie Visual Studio con privilegios de administrador.

    En Visual Studio, en el menú **Archivo**, haga clic en **Nuevo** y, a continuación, en **Proyecto**.

    ![][8]

2.  En **Plantillas instaladas**, en **Visual C#**, haga clic en **Nube** y, a continuación, en **Servicio en la nube de Azure**. Denomine el proyecto como **MultiTierApp** y, a continuación, haga clic en **Aceptar**.

    ![][9]

3.  En los roles de **.NET Framework 4.5**, haga doble clic en **Rol web de ASP.NET**.

    ![][10]

4.  Mantenga el cursor sobre **WebRole1** en **Solución del Servicio en la nube de Azure**, haga clic en el icono de lápiz y cambie el nombre del rol web a **FrontendWebRole** y, a continuación, haga clic en **Aceptar**. (Asegúrese de que escribe "Frontend" con "e" minúscula, no "FrontEnd").

    ![][11]

5.  En el cuadro de diálogo **Nuevo proyecto ASP.NET**, en la lista **Seleccionar una plantilla**, haga clic en **MVC** y, después, haga clic en **Aceptar**.

    ![][12]

6.  En el **Explorador de soluciones**, haga clic con el botón derecho en **Referencias** y, después, haga clic en **Administrar paquetes de NuGet** o **Agregar referencia de paquetes de biblioteca**.

7.  Seleccione **En línea** a la izquierda del cuadro de diálogo. Busque "**Bus de servicio**" y seleccione el elemento **Bus de servicio de Microsoft Azure**. Después finalice la instalación y cierre este cuadro de diálogo.

    ![][13]

8.  Tenga en cuenta que ahora se hace referencia a los ensamblados del cliente requeridos y que se han agregado algunos archivos de código nuevos.

9.  En el **Explorador de soluciones**, haga clic con el botón derecho en **Modelos**, en **Agregar** y, por último, en **Clase**. En el cuadro **Nombre**, escriba el nombre **OnlineOrder.cs**. A continuación, haga clic en **Agregar**.

### Especificación del código del rol web

En esta sección, creará las distintas páginas que va a mostrar la aplicación.

1.  En el archivo OnlineOrder.cs en Visual Studio, sustituya la definición del espacio de nombres existentes por el código siguiente:

        namespace FrontendWebRole.Models
        {
            public class OnlineOrder
            {
                public string Customer { get; set; }
                public string Product { get; set; }
            }
        }

2.  En el **Explorador de soluciones**, haga doble clic en **Controllers\\HomeController.cs**. Agregue las siguientes instrucciones **using** en la parte superior del archivo para incluir los espacios de nombres en el modelo que acaba de crear, así como el Bus de servicio.

        using FrontendWebRole.Models;
        using Microsoft.ServiceBus.Messaging;
        using Microsoft.ServiceBus;

3.  En el archivo HomeController.cs en Visual Studio, sustituya la definición del espacio de nombres existentes por el código siguiente. Este código contiene métodos para controlar el envío de elementos a la cola.

        namespace FrontendWebRole.Controllers
        {
            public class HomeController : Controller
            {
                public ActionResult Index()
                {
                    // Simply redirect to Submit, since Submit will serve as the
                    // front page of this application.
                    return RedirectToAction("Submit");
                }

                public ActionResult About()
                {
                    return View();
                }

                // GET: /Home/Submit.
                // Controller method for a view you will create for the submission
                // form.
                public ActionResult Submit()
                {
                    // Will put code for displaying queue message count here.

                    return View();
                }

                // POST: /Home/Submit.
                // Controller method for handling submissions from the submission
                // form.
                [HttpPost]
				// Attribute to help prevent cross-site scripting attacks and
				// cross-site request forgery.  
    			[ValidateAntiForgeryToken]
                public ActionResult Submit(OnlineOrder order)
                {
                    if (ModelState.IsValid)
                    {
                        // Will put code for submitting to queue here.

                        return RedirectToAction("Submit");
                    }
                    else
                    {
                        return View(order);
                    }
                }
            }
        }

4.  En el menú **Compilar**, haga clic en **Compilar solución** para probar la precisión del trabajo hasta ahora.

5.  Ahora, va a crear la vista para el método **Submit()** que creó antes. Haga clic con el botón derecho en el método **Submit()** y luego elija **Agregar vista**.

    ![][14]

6.  Se muestra un cuadro de diálogo para crear la vista. En la lista **Plantilla**, elija **Crear**. En la lista **Clase de modelo**, haga clic en la clase **OnlineOrder**.

    ![][15]

7.  Haga clic en **Agregar**.

8.  Ahora, cambie el nombre mostrado de la aplicación. En el **Explorador de soluciones**, haga doble clic en el archivo **Views\\Shared\\\\_Layout.cshtml** para abrirlo en el editor de Visual Studio.

9.  Reemplace todas las apariciones de **My ASP.NET Application** por **Productos de LITWARE**.

10. Quite los vínculos **Página principal**, **Acerca de** y **Contacto**. Elimine el código resaltado:

	![][28]

11. Finalmente, modifique la página de envío para incluir información sobre la cola. En el **Explorador de soluciones**, haga doble clic en el archivo **Views\\Home\\Submit.cshtml** para abrirlo en el editor de Visual Studio. Agregue la siguiente línea después de **&lt;h2>Submit&lt;/h2>**. Por el momento, **ViewBag.MessageCount** está vacío. Lo rellenará más adelante.

        <p>Current number of orders in queue waiting to be processed: @ViewBag.MessageCount</p>

12. Acaba de implementar su interfaz de usuario. Puede presionar **F5** para ejecutar la aplicación y confirmar que tiene el aspecto previsto.

    ![][17]

### Especificación del código para enviar elementos a una cola del bus de servicio

Ahora, agregue el código para enviar elementos a una cola. En primer lugar, va a crear una clase que contiene la información de conexión a la cola del Bus de servicio. A continuación, inicialice la conexión en Global.aspx.cs. Finalmente, actualice el código de envío que creó antes en HomeController.cs para enviar realmente los elementos a una cola del Bus de servicio.

1.  En el **Explorador de soluciones**, haga clic con el botón derecho en **FrontendWebRole** (haga clic con el botón derecho en el proyecto, no en el rol). Haga clic en **Agregar** y, a continuación, en **Clase**.

2.  Asigne a la clase el nombre QueueConnector.cs. Haga clic en **Add** para crear la clase.

3.  Ahora va a agregar código que encapsula la información de conexión e inicializará la conexión a una cola de Bus de servicio. En QueueConnector.cs, agregue el código siguiente y escriba valores para **Espacio de nombres** (el espacio de nombres del servicio) y **yourKey**, que es la clave SAS que obtuvo en el [Portal de Azure clásico][] anteriormente.

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Web;
        using Microsoft.ServiceBus.Messaging;
        using Microsoft.ServiceBus;

        namespace FrontendWebRole
        {
            public static class QueueConnector
            {
                // Thread-safe. Recommended that you cache rather than recreating it
                // on every request.
                public static QueueClient OrdersQueueClient;

                // Obtain these values from the portal.
                public const string Namespace = "your service bus namespace";

                // The name of your queue.
                public const string QueueName = "OrdersQueue";

                public static NamespaceManager CreateNamespaceManager()
                {
                    // Create the namespace manager which gives you access to
                    // management operations.
                    var uri = ServiceBusEnvironment.CreateServiceUri(
                        "sb", Namespace, String.Empty);
                    var tP = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                        "RootManageSharedAccessKey", "yourKey");
                    return new NamespaceManager(uri, tP);
                }

                public static void Initialize()
                {
                    // Using Http to be friendly with outbound firewalls.
                    ServiceBusEnvironment.SystemConnectivity.Mode =
                        ConnectivityMode.Http;

                    // Create the namespace manager which gives you access to
                    // management operations.
                    var namespaceManager = CreateNamespaceManager();

                    // Create the queue if it does not exist already.
                    if (!namespaceManager.QueueExists(QueueName))
                    {
                        namespaceManager.CreateQueue(QueueName);
                    }

                    // Get a client to the queue.
                    var messagingFactory = MessagingFactory.Create(
                        namespaceManager.Address,
                        namespaceManager.Settings.TokenProvider);
                    OrdersQueueClient = messagingFactory.CreateQueueClient(
                        "OrdersQueue");
                }
            }
        }

    Más adelante en este tutorial aprenderá a almacenar el nombre del **Espacio de nombres** y su valor de clave SAS en un archivo de configuración.

4.  Ahora, asegúrese de que se llama al método **Initialize**. En el **Explorador de soluciones**, haga doble clic en **Global.asax\\Global.asax.cs**.

5.  Agregue la siguiente línea en la parte inferior del método **Application\_Start**.

        FrontendWebRole.QueueConnector.Initialize();

6.  Por último, actualice el código web que creó anteriormente para enviar elementos a la cola. En el **Explorador de soluciones**, haga doble clic en **Controllers\\HomeController.cs**.

7.  Actualice el método **Submit()** de la forma siguiente para obtener el recuento de mensajes correspondiente a la cola.

        public ActionResult Submit()
        {
            // Get a NamespaceManager which allows you to perform management and
            // diagnostic operations on your Service Bus queues.
            var namespaceManager = QueueConnector.CreateNamespaceManager();

            // Get the queue, and obtain the message count.
            var queue = namespaceManager.GetQueue(QueueConnector.QueueName);
            ViewBag.MessageCount = queue.MessageCount;

            return View();
        }

8.  Actualice el método **Submit(OnlineOrder order)** de la forma siguiente para enviar información de pedido a la cola.

        public ActionResult Submit(OnlineOrder order)
        {
            if (ModelState.IsValid)
            {
                // Create a message from the order.
                var message = new BrokeredMessage(order);

                // Submit the order.
                QueueConnector.OrdersQueueClient.Send(message);
                return RedirectToAction("Submit");
            }
            else
            {
                return View(order);
            }
        }

9.  Ahora puede volver a ejecutar la aplicación. Cada vez que envía un pedido, el contador de mensajes aumenta.

    ![][18]

## Administrador de configuración de nube

El método [GetSetting][] de la clase [Microsoft.WindowsAzure.Configuration.CloudConfigurationManager][] permite leer valores de configuración del almacén de configuración para su plataforma. Por ejemplo, si el código se ejecuta en un rol web o en un rol de trabajo, el método [GetSetting][] lee el archivo ServiceConfiguration.cscfg y, si el código se ejecuta en una aplicación de consola estándar, el método [GetSetting][] lee el archivo app.config.

Si almacena una cadena de conexión para el espacio de nombres del Bus de servicio en un archivo de configuración, puede usar el método [GetSetting][] para leer una cadena de conexión que puede usar para crear una instancia de un objeto [NamespaceMananger][]. Puede usar una instancia [NamespaceManager][] para configurar el espacio de nombres del Bus de servicio mediante programación. Puede usar la misma cadena de conexión para crear instancias de los objetos de cliente (como el objeto [QueueClient][], [TopicClient][] y [EventHubClient][]) que puede usar para realizar operaciones de tiempo de ejecución, como enviar y recibir mensajes.

### Cadena de conexión

Para crear una instancia de un cliente (por ejemplo, una [QueueClient][] del bus de servicio), puede representar la información de configuración como una cadena de conexión. En el lado cliente, existe un método `CreateFromConnectionString()` que crea una instancia del tipo de cliente mediante el uso de esa cadena de conexión. Por ejemplo, dada la sección de configuración siguiente:

	<ConfigurationSettings>
    ...
    	<Setting name="Microsoft.ServiceBus.ConnectionString" value="Endpoint=sb://[yourServiceNamespace].servicebus.windows.net/;SharedSecretIssuer=RootManageSharedAccessKey;SharedSecretValue=[yourKey]" />
	</ConfigurationSettings>

El código siguiente recupera la cadena de conexión, crea una cola e inicializa la conexión a la cola.

	QueueClient Client;

	string connectionString =
     CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    var namespaceManager =
     NamespaceManager.CreateFromConnectionString(connectionString);

	if (!namespaceManager.QueueExists(QueueName))
    {
        namespaceManager.CreateQueue(QueueName);
    }

	// Initialize the connection to Service Bus queue.
	Client = QueueClient.CreateFromConnectionString(connectionString, QueueName);

El código de la sección siguiente utiliza la clase [CloudConfigurationManager][Microsoft.WindowsAzure.Configuration.CloudConfigurationManager].

## Creación del rol de trabajo

Ahora creará el rol de trabajo que procesa los envíos del pedido. Este ejemplo utiliza la plantilla de proyecto de Visual Studio de **Rol de trabajo con cola del Bus de servicio**. En primer lugar, utilice el Explorador de servidores en Visual Studio para obtener las credenciales requeridas.

1. Asegúrese de que se ha conectado Visual Studio a su cuenta de Azure.

2.  En Visual Studio, en el **Explorador de soluciones**, haga clic con el botón secundario en la carpeta **Roles** en el proyecto **MultiTierApp**.

3.  Haga clic en **Agregar** y, a continuación, en **Nuevo proyecto de rol de trabajo**. Aparecerá el cuadro de diálogo **Agregar nuevo proyecto de rol**.

	![][26]

4.  En el cuadro de diálogo **Agregar nuevo proyecto de rol**, haga clic en **Rol de trabajo con cola del Bus de servicio**.

	![][23]

5.  En el cuadro **Nombre**, asigne al proyecto el nombre de **OrderProcessingRole**. A continuación, haga clic en **Agregar**.

6.  En el **Explorador de servidores**, haga clic con el botón derecho en el espacio de nombres del servicio y, después, haga clic en **Propiedades**. En el panel **Propiedades** de Visual Studio, la primera entrada contiene una cadena de conexión que se rellena con el extremo del espacio de nombres que contiene las credenciales de autorización requeridas. Por ejemplo, vea la siguiente captura de pantalla. Haga doble clic en **ConnectionString** y, a continuación, presione **Ctrl+C** para copiar esta cadena en el Portapapeles.

	![][24]

7.  En el **Explorador de soluciones**, haga clic con el botón derecho en el **OrderProcessingRole** que creó en el paso 5 (asegúrese de hacer clic con el botón derecho en **OrderProcessingRole** en **Roles** y no en la clase). A continuación, haga clic en **Propiedades**.

8.  En la pestaña **Configuración** del cuadro de diálogo **Propiedades**, haga clic dentro del cuadro **Valor** para **Microsoft.ServiceBus.ConnectionString** y, a continuación, pegue el valor de extremo que copió en el paso 6.

	![][25]

9.  Cree una clase **OnlineOrder** para representar los pedidos cuando los procese desde la cola. Puede reutilizar una clase que ya ha creado. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto **OrderProcessingRole** (haga clic con el botón derecho en el proyecto, no en el rol). Haga clic en **Agregar** y, a continuación, en **Elemento existente**.

10. Busque **FrontendWebRole\\Models** en la subcarpeta y haga doble clic en **OnlineOrder.cs** para agregarlo a este proyecto.

11. En **WorkerRole.cs**, reemplace el valor de la variable **QueueName** en **WorkerRole.cs** de `"ProcessingQueue"` a `"OrdersQueue"` como se muestra en el código siguiente:

		// The name of your queue.
		const string QueueName = "OrdersQueue";

12. Agregue la siguiente instrucción using en la parte superior del archivo WorkerRole.cs.

		using FrontendWebRole.Models;

13. En la función `Run()`, dentro de la llamada `OnMessage`, agregue el siguiente código dentro de la cláusula `try`.

		Trace.WriteLine("Processing", receivedMessage.SequenceNumber.ToString());
		// View the message as an OnlineOrder.
		OnlineOrder order = receivedMessage.GetBody<OnlineOrder>();
		Trace.WriteLine(order.Customer + ": " + order.Product, "ProcessingMessage");
		receivedMessage.Complete();

14. Ha completado la aplicación. Puede probar la aplicación completa haciendo clic con el botón secundario en el proyecto MultiTierApp en el Explorador de soluciones, seleccionando **Establecer como proyecto de inicio** y, a continuación, presionando F5. Tenga en cuenta que el contador de mensajes no se aumenta, ya que el rol de trabajo procesa los elementos de la cola y los marca como finalizados. Puede ver el resultado de seguimiento de su rol de trabajo viendo la interfaz de usuario del emulador de proceso de Azure. Para ello, haga clic con el botón secundario en el icono del emulador del área de notificación de la barra de tareas y seleccione **Mostrar interfaz de usuario del emulador de proceso**.

    ![][19]

    ![][20]

## Pasos siguientes  

Para obtener más información sobre el bus de servicio, consulte los siguientes recursos:

* [Bus de servicio de Azure][sbmsdn]  
* [Página de servicio del Bus de servicio][sbwacom]  
* [Utilización de las colas del Bus de servicio][sbwacomqhowto]  

Para obtener más información sobre los escenarios de niveles múltiples o sobre cómo implementar una aplicación en un servicio en la nube, consulte:

* [Aplicación .NET de niveles múltiples utilizando tablas, colas y blobs de almacenamiento][mutitierstorage]  

Es posible que desee implementar el front-end de una aplicación de niveles múltiples en un sitio web de Azure en lugar de en un servicio en la nube de Azure. Para obtener más información sobre la diferencia entre sitios web y servicios en la nube, consulte [Modelos de ejecución de Azure][executionmodels].

Para implementar la aplicación que ha creado en este tutorial como un proyecto web estándar en lugar de como un rol web de servicio en la nube, siga los pasos de este tutorial con las diferentes siguientes:

1. Cuando crea el proyecto, elija la plantilla del proyecto **Aplicación web de ASP.NET MVC** en la categoría **Web** en lugar de la plantilla **Servicio en la nube** en la categoría **Nube**. A continuación, siga las mismas instrucciones para crear la aplicación MVC hasta que llegue a la sección **Administrador de configuración de nube**.

2. Cuando crea el rol de trabajo, créelo de una nueva solución independiente, similar a las instrucciones originales para el rol web. Tenga en cuenta, sin embargo, que está creando solo el rol de trabajo en el proyecto del servicio en la nube. A continuación, siga las mismas instrucciones para crear el rol de trabajo.

3. Puede probar por separado el front-end y el back-end, o bien puede ejecutar ambos simultáneamente en instancias separadas de Visual Studio.

Para obtener información sobre cómo implementar el front-end en un Sitio web de Azure, consulte [Creación de una aplicación web ASP.NET en el Servicio de aplicaciones de Azure](../app-service-web/web-sites-dotnet-get-started.md). Para obtener información sobre cómo implementar el back-end en un Servicio en la nube de Azure, consulte [Aplicación de niveles múltiples .NET con colas del Bus de servicio de Azure][mutitierstorage].


  [0]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-01.png
  [1]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-100.png
  [sbqueuecomparison]: service-bus-azure-and-service-bus-queues-compared-contrasted.md
  [2]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-101.png
  [Obtención de herramientas y de SDK]: http://go.microsoft.com/fwlink/?LinkId=271920
  [3]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-3.png


  [GetSetting]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager.getsetting.aspx
  [Microsoft.WindowsAzure.Configuration.CloudConfigurationManager]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager.aspx
  [NamespaceMananger]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx
  [NamespaceManager]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx

  [QueueClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx

  [TopicClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx

  [EventHubClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.aspx

  [Portal de Azure clásico]: http://manage.windowsazure.com
  [6]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-03.png
  [7]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-04.png
  [8]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-09.png
  [9]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-10.png
  [10]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-11.png
  [11]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-02.png
  [12]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-12.png
  [13]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-13.png
  [14]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-33.png
  [15]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-34.png
  [16]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-35.png
  [17]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-36.png
  [18]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-37.png

  [19]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-38.png
  [20]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-39.png
  [23]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/SBWorkerRole1.png
  [24]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/SBExplorerProperties.png
  [25]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/SBWorkerRoleProperties.png
  [26]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/SBNewWorkerRole.png
  [27]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-27.png
  [28]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-40.png
  [30]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-09.png
  [31]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-06.png
  [32]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-41.png
  [33]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-42-webpi.png
  [35]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/multi-web-45.png
  [sbmsdn]: http://msdn.microsoft.com/library/azure/ee732537.aspx
  [sbwacom]: /documentation/services/service-bus/
  [sbwacomqhowto]: service-bus-dotnet-how-to-use-queues.md
  [mutitierstorage]: https://code.msdn.microsoft.com/Windows-Azure-Multi-Tier-eadceb36
  [executionmodels]: ../cloud-services/fundamentals-application-models.md

<!---HONumber=AcomDC_1203_2015-->