<properties
	pageTitle="Aplicación híbrida en la nube/local (.NET) | Microsoft Azure"
	description="Obtenga información acerca de cómo crear una aplicación híbrida en la nube o local de .NET con la retransmisión del Bus de servicio de Azure."
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
	ms.topic="get-started-article"
	ms.date="10/07/2015"
	ms.author="sethm"/>

# Aplicación híbrida en la nube/local .NET mediante el relé del bus de servicio de Azure

## Introducción

Desarrollar aplicaciones híbridas en la nube con Microsoft Azure es muy sencillo con Visual Studio 2013 y el SDK de Azure para .NET gratuito. En este artículo se asume que no tiene ninguna experiencia previa con Azure. En menos de 30 minutos, dispondrá de una aplicación que utiliza varios recursos de Azure funcionando en la nube.

Aprenderá a:

-   Crear o adaptar un servicio web existente para su consumo por una solución web.
-   Utilizar el relé del bus de servicio de Azure para compartir datos entre una aplicación de Azure y un servicio web hospedado en otra parte.

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

## Cómo ayuda el relé del bus de servicio con soluciones híbridas

Las soluciones de negocio por lo general están compuestas por una combinación de código personalizado escrito para afrontar los nuevos y exclusivos requisitos empresariales, así como la funcionalidad existente proporcionada por soluciones y sistemas que ya están instalados.

Los arquitectos de soluciones están comenzando a utilizar la nube para abordar con más facilidad los requisitos de escala y reducir los costes operativos. De esta manera, se dan cuenta de que los activos de servicio existentes que les gustaría aprovechar como base de sus soluciones se encuentran dentro del firewall corporativo y no resulta sencillo para la solución en la nube el acceso a ellos. Muchos de los servicios internos no están construidos ni hospedados de una forma que se puedan exponer fácilmente en los servidores perimetrales de la red corporativa.

La Retransmisión de bus de servicio está diseñada para el caso de uso en que se toman los servicios web de Windows Communication Foundation (WCF) existentes y se permite el acceso seguro a los mismos a las soluciones que residen fuera del perímetro corporativo, sin necesidad de realizar cambios molestos en la infraestructura de la red corporativa. Dichos servicios de Retransmisión de bus de servicio aún se hospedan en el entorno existente, pero delegan la escucha de sesiones y solicitudes de entrada en el Bus de servicio hospedado en la nube. El Bus de servicio también protege dichos servicios del acceso no autorizado mediante el uso de la autenticación de [firma de acceso compartido](https://msdn.microsoft.com/library/dn170478.aspx) (SAS).

## Escenario de la solución

En este tutorial, se creará un sitio web de ASP.NET MVC que le permitirá ver una lista de los productos en la página del inventario de productos.

![][0]

En este tutorial se asume que la información de los productos se encuentra en un sistema local y se usa el relé del bus de servicio para obtener acceso a dicho sistema. Esto lo simula un servicio web que se ejecuta en una aplicación de consola simple y el respaldo lo proporciona un conjunto de productos en memoria. Dicha aplicación de consola se puede ejecutar en el equipo propio y el rol web se puede implementar en Azure. Al hacerlo, observará que el rol web que se ejecuta en el centro de datos de Azure llamará a su equipo, aunque casi seguro que este residirá al menos detrás de un firewall y una capa de traducción de direcciones de red (NAT).

Esta es una captura de pantalla de la página de inicio de la aplicación web finalizada.

![][1]

## Configuración del entorno de desarrollo

Antes de comenzar a desarrollar su aplicación de Azure, obtenga las herramientas y configure el entorno de desarrollo:

1.  Instale el SDK de Azure para .NET en [Obtener herramientas y SDK][].

2. 	Haga clic en **Instalar el SDK** para la versión de Visual Studio que está usando. En los pasos de este tutorial se usa Visual Studio 2013.

	![][42]

4.  Cuando se le solicite ejecutar o guardar el programa de instalación, haga clic en **Ejecutar**.

    ![][2]

5.  En el **instalador de plataforma web**, haga clic en **Instalar** y continúe con la instalación.

    ![][3]

6.  Cuando la instalación se complete, dispondrá de todo lo necesario para iniciar el desarrollo de la aplicación. El SDK incluye las herramientas que le permiten desarrollar fácilmente aplicaciones Azure en Visual Studio. Si no tiene instalado Visual Studio, el SDK también instala la versión gratuita Visual Studio Express.

## Creación de un espacio de nombres de servicio

Para comenzar a usar las características del bus de servicio en Azure, primero debe crear un espacio de nombres de servicio. Un espacio de nombres proporciona un contenedor con un ámbito para el desvío de recursos del bus de servicio en la aplicación.

Los espacios de nombres y las entidades de mensajería del Bus de servicio se pueden administrar a través del [Portal de Azure clásico][] o del Explorador de servidores de Visual Studio, pero los espacios de nombres solo se pueden crear en el portal.

### Creación de un espacio de nombres mediante el Portal de Azure clásico:

1.  Inicie sesión en el [Portal de Azure clásico][].

2.  En el panel de navegación izquierdo del Portal, haga clic en **Bus de servicio**.

3.  En el panel inferior del Portal, haga clic en **Crear**.

    ![][5]

4.  En el cuadro de diálogo **Agregar un nuevo espacio de nombres**, escriba un nombre de espacio de nombres. El sistema realiza la comprobación automáticamente para ver si el nombre está disponible. ![][6]

5.  Después de asegurarse de que el nombre de espacio de nombres está disponible, seleccione el país o región en el que debe hospedarse el espacio de nombres (asegúrese de que usa el mismo país o la misma región en los que está realizando la implementación de los recursos de proceso).

    > [AZURE.IMPORTANT] seleccione la *misma región* que vaya a seleccionar para la implementación de la aplicación. Con esto conseguirá el máximo rendimiento.

6.	Deje los demás campos del cuadro de diálogo con los valores predeterminados (**Mensajería** y **Nivel estándar**) y, a continuación, haga clic en la marca de verificación. El sistema crea ahora el espacio de nombres del servicio y lo habilita. Es posible que tenga que esperar algunos minutos mientras el sistema realiza el aprovisionamiento de los recursos para la cuenta.

	![][38]

El espacio de nombres que creó aparecerá en el Portal de Azure clásico, aunque puede tardar un poco en activarse. Espere hasta que el estado sea **Active** antes de continuar.

## Obtención de credenciales de administración predeterminadas para el espacio de nombres

Para realizar operaciones de administración en el nuevo espacio de nombres, como la creación de entidades de mensajería, debe obtener las credenciales para el espacio de nombres.

1.  En la ventana principal, haga clic en el nombre del espacio de nombres de servicio.

	![][39]

2.  Haga clic en **Información de conexión**.

	![][40]

3.  En el panel **Información de conexión de acceso**, encuentre la cadena de conexión que contiene la clave SAS y el nombre de la clave.

	![][45]

4.  Tome nota de estas credenciales o cópielas en el Portapapeles.

## Creación de un servidor local

En primer lugar, cree un sistema de catálogo de productos local (ficticio). Será bastante simple; puede considerar que representa un sistema de catálogo de productos local real con una superficie de servicio completa que se intenta integrar.

Este proyecto se inicia como una aplicación de consola de Visual Studio. El proyecto usa el paquete Service Bus NuGet para incluir las bibliotecas y los ajustes de configuración del bus de servicio. La extensión NuGet Visual Studio facilita la instalación y la actualización de las bibliotecas y las herramientas en Visual Studio y Visual Studio Express. El paquete NuGet del bus de servicio es la forma más sencilla de obtener la API del bus de servicio y configurar su aplicación con todas las dependencias del bus de servicio. Para obtener más información acerca del uso del paquete NuGet y del bus de servicio, consulte [Using the NuGet Service Bus Package][].

### Creación del proyecto

1.  Con privilegios de administrador, inicie Microsoft Visual Studio 2013 o Microsoft Visual Studio Express. Para iniciar Visual Studio con privilegios de administrador, haga clic con el botón secundario **Microsoft Visual Studio 2013 (o Microsoft Visual Studio Express)** y, a continuación, haga clic en **Ejecutar como administrador**.

2.  En Visual Studio, en el menú **Archivo**, haga clic en **Nuevo** y, a continuación, en **Proyecto**.

    ![][10]

3.  En **Plantillas instaladas**, en **Visual C#**, haga clic en **Aplicación de consola**. En el cuadro **Nombre**, escriba el nombre **ProductsServer**:

    ![][11]

4.  Haga clic en **Aceptar** para crear el proyecto **ProductsServer**.

5.  En el Explorador de soluciones, haga clic con el botón secundario en **ProductsServer** y, a continuación, en **Propiedades**.

6.  Haga clic en la pestaña **Application** de la izquierda y asegúrese de que **.NET Framework 4** o **.NET Framework 4.5** aparecen en la lista **Target framework**. Si no aparecen, selecciónelas en la lista y haga clic en **Yes** cuando se le solicite volver a cargar el proyecto.

    ![][12]

7.  Si ya ha instalado el administrador del paquete NuGet para Visual Studio, vaya al paso siguiente. De lo contrario, visite [NuGet][] y haga clic en [Install NuGet](http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c). Siga las indicaciones para instalar el administrador del paquete NuGet y, a continuación, reinicie Visual Studio.

7.  En el Explorador de soluciones, haga clic con el botón secundario en **References** y, a continuación, en **Manage NuGet Packages**.

8.  En la columna de la izquierda del cuadro de diálogo de **NuGet**, haga clic en **Online**.

9. 	En la columna de la derecha, haga clic en el cuadro **Buscar**, escriba "**Bus de servicio**" y, a continuación, seleccione el elemento **Bus de servicio de Microsoft Azure**. Haga clic en **Install** para completar la instalación y, a continuación, cierre el cuadro de diálogo.

    ![][13]

    Tenga en cuenta que ahora se hace referencia a los ensamblados del cliente requeridos.

9.  Agregue una clase nueva para el contrato de su producto. En el Explorador de soluciones, haga clic con el botón secundario en el proyecto **ProductsServer**, a continuación en **Add** y, por último, en **Class**.

    ![][14]

10. En el cuadro **Name**, escriba el nombre **ProductsContract.cs**. A continuación, haga clic en **Agregar**.

11. En **ProductsContract.cs**, sustituya la definición del espacio de nombres por el siguiente código, que define el contrato del servicio.

        namespace ProductsServer
        {
            using System.Collections.Generic;
            using System.Runtime.Serialization;
            using System.ServiceModel;

            // Define the data contract for the service
            [DataContract]
            // Declare the serializable properties.
            public class ProductData
            {
                [DataMember]
                public string Id { get; set; }
                [DataMember]
                public string Name { get; set; }
                [DataMember]
                public string Quantity { get; set; }
            }

            // Define the service contract.
            [ServiceContract]
            interface IProducts
            {
                [OperationContract]
                IList<ProductData> GetProducts();

            }

            interface IProductsChannel : IProducts, IClientChannel
            {
            }
        }

12. En Program.cs, sustituya la definición del espacio de nombres por el siguiente código, que agrega el servicio de perfil y su host:

        namespace ProductsServer
        {
            using System;
            using System.Linq;
            using System.Collections.Generic;
            using System.ServiceModel;

            // Implement the IProducts interface.
            class ProductsService : IProducts
            {

                // Populate array of products for display on website.
                ProductData[] products =
                    new []
                        {
                            new ProductData{ Id = "1", Name = "Rock",
                                             Quantity = "1"},
                            new ProductData{ Id = "2", Name = "Paper",
                                             Quantity = "3"},
                            new ProductData{ Id = "3", Name = "Scissors",
                                             Quantity = "5"},
                            new ProductData{ Id = "4", Name = "Well",
                                             Quantity = "2500"},
                        };

                // Display a message in the service console application
                // when the list of products is retrieved.
                public IList<ProductData> GetProducts()
                {
                    Console.WriteLine("GetProducts called.");
                    return products;
                }

            }

            class Program
            {
                // Define the Main() function in the service application.
                static void Main(string[] args)
                {
                    var sh = new ServiceHost(typeof(ProductsService));
                    sh.Open();

                    Console.WriteLine("Press ENTER to close");
                    Console.ReadLine();

                    sh.Close();
                }
            }
        }

13. En el Explorador de soluciones, haga doble clic en el archivo **App.config** para abrirlo en el editor de Visual Studio. Reemplace el contenido de **&lt;system.ServiceModel&gt;** por el siguiente código XML. Asegúrese de reemplazar *yourServiceNamespace* por el nombre de su espacio de nombres de servicio y *yourKey* por la clave SAS que recuperó del Portal de Azure clásico:

        <system.serviceModel>
          <extensions>
             <behaviorExtensions>
                <add name="transportClientEndpointBehavior" type="Microsoft.ServiceBus.Configuration.TransportClientEndpointBehaviorElement, Microsoft.ServiceBus, Version=2.6.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
              </behaviorExtensions>
              <bindingExtensions>
                 <add name="netTcpRelayBinding" type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Version=2.6.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
              </bindingExtensions>
          </extensions>
          <services>
             <service name="ProductsServer.ProductsService">
               <endpoint address="sb://yourServiceNamespace.servicebus.windows.net/products" binding="netTcpRelayBinding" contract="ProductsServer.IProducts"
        behaviorConfiguration="products"/>
             </service>
          </services>
          <behaviors>
             <endpointBehaviors>
               <behavior name="products">
                 <transportClientEndpointBehavior>
                    <tokenProvider>
                       <sharedAccessSignature keyName="RootManageSharedAccessKey" key="yourKey" />
                    </tokenProvider>
                 </transportClientEndpointBehavior>
               </behavior>
             </endpointBehaviors>
          </behaviors>
        </system.serviceModel>

14. Presione F6, o bien en el menú **Build**, haga clic en **Build Solution** para compilar la aplicación para comprobar la precisión del trabajo realizado hasta el momento.

## Crear una aplicación ASP.NET MVC

En esta sección se creará una aplicación ASP.NET simple que mostrará los datos recuperados del servicio de producto.

### Creación del proyecto

1.  Asegúrese de que se está ejecutando Visual Studio con privilegios de administrador. De lo contrario, para iniciar Visual Studio con privilegios de administrador, haga clic con el botón secundario **Microsoft Visual Studio 2013 (o Microsoft Visual Studio Express)** y, a continuación, haga clic en **Ejecutar como administrador**. El emulador de proceso de Microsoft Azure, descrito posteriormente en este artículo, requiere que se inicie Visual Studio con privilegios de administrador.

2.  En Visual Studio, en el menú **Archivo**, haga clic en **Nuevo** y, a continuación, en **Proyecto**.

3.  En **Installed Templates**, en **Visual C#**, haga clic en **ASP.NET Web Application**. Asigne al proyecto el nombre **ProductsPortal**. y, a continuación, haga clic en **Aceptar**.

    ![][15]

4.  En la lista **Select a template**, haga clic en **MVC** y, a continuación, en **Aceptar**.

    ![][16]

5.  En el Explorador de soluciones, haga clic con el botón secundario en **Modelos** y, luego, en **Agregar** y, por último, en **Clase**. En el cuadro **Name**, escriba el nombre **Product.cs**. A continuación, haga clic en **Agregar**.

    ![][17]

### Modificación de la aplicación web

1.  En el archivo Product.cs en Visual Studio, sustituya la definición del espacio de nombres existente por el código siguiente:

        // Declare properties for the products inventory.
        namespace ProductsWeb.Models
        {
            public class Product
            {
                public string Id { get; set; }
                public string Name { get; set; }
                public string Quantity { get; set; }
            }
        }

2.  En el archivo HomeController.cs en Visual Studio, sustituya la definición del espacio de nombres existente por el código siguiente:

        namespace ProductsWeb.Controllers
        {
            using System.Collections.Generic;
            using System.Web.Mvc;
            using Models;

            public class HomeController : Controller
            {
                // Return a view of the products inventory.
                public ActionResult Index(string Identifier, string ProductName)
                {
                    var products = new List<Product>
                        {new Product {Id = Identifier, Name = ProductName}};
                    return View(products);
                }

            }
        }

3.  En el Explorador de soluciones, expanda la carpeta Views\\Shared.

    ![][18]

4.  Haga doble clic en **\_Layout.cshtml** para abrirlo en el editor de Visual Studio.

5.  Cambie todas las ocurrencias de **My ASP.NET Application** por **LITWARE'S Products**.

6. Quite los vínculos **Página principal**, **Acerca de** y **Contacto**. En el siguiente ejemplo, elimine el código resaltado.

	![][41]

7.  En el Explorador de soluciones, expanda la carpeta Views\\Home.

    ![][20]

8.  Haga doble clic en **Index.cshtml** para abrirlo en el editor de Visual Studio. Reemplace todo el contenido del archivo con el código siguiente.

		@model IEnumerable<ProductsWeb.Models.Product>

		@{
    		ViewBag.Title = "Index";
		}

		<h2>Prod Inventory</h2>

		<table>
    		<tr>
        		<th>
            		@Html.DisplayNameFor(model => model.Name)
        		</th>
                <th></th>
        		<th>
            		@Html.DisplayNameFor(model => model.Quantity)
        		</th>
    		</tr>

		@foreach (var item in Model) {
    		<tr>
        		<td>
            		@Html.DisplayFor(modelItem => item.Name)
        		</td>
        		<td>
            		@Html.DisplayFor(modelItem => item.Quantity)
        		</td>
    		</tr>
		}

		</table>

9.  Para comprobar la precisión del trabajo realizado hasta el momento, puede presionar **F6** o **Ctrl+Mayús+B** para compilar el proyecto.


### Ejecución de la aplicación de forma local

Ejecute la aplicación para comprobar que funciona.

1.  Asegúrese de que **ProductsPortal** es el proyecto activo. Haga clic con el botón secundario en el nombre del proyecto en el Explorador de soluciones y seleccione **Set As Startup Project**.
2.  En **Visual Studio**, presione F5.
3.  La aplicación debería aparecer ejecutándose en un explorador:

    ![][21]

## Preparación de la aplicación para que se implemente en Azure

Cualquier aplicación se puede implementar en un servicio en la nube de Azure o en un sitio web de Azure. Para obtener más información sobre la diferencia entre sitios web y servicios en la nube, consulte [Modelos de ejecución de Azure][executionmodels]. Para obtener información sobre cómo implementar la aplicación en un sitio web de Azure, consulte [Implementación de una aplicación web ASP.NET en un sitio web de Azure](https://azure.microsoft.com/develop/net/tutorials/get-started/). Esta sección contiene los pasos detallados para implementar la aplicación en un servicio en la nube de Azure.

Para implementar una aplicación en un servicio en la nube, va a agregar a la solución un proyecto de implementación de un proyecto de servicio en la nube. El proyecto de implementación contiene información de configuración necesaria para ejecutar correctamente la aplicación en la nube.

1.  Para que la aplicación pueda implementarse en la nube, haga clic con el botón derecho en el proyecto **ProductsPortal** en el Explorador de soluciones, haga clic en **Convertir** y, a continuación, en **Convertir en proyecto de servicio en la nube de Microsoft Azure**.

    ![][22]

2.  Para probar la aplicación, presione F5.

3.  Al hacerlo, se iniciará el emulador de proceso de Azure. Dicho emulador utiliza el equipo local para emular la ejecución de la aplicación en Azure. Para confirmar que el emulador se ha iniciado, observe la bandeja del sistema:

       ![][23]

4.  El explorador seguirá mostrando la ejecución local de la aplicación y tendrá el mismo aspecto y funcionamiento que tenía cuando se ejecutó antes como aplicación ASP.NET MVC 4 regular.

## Combinación de todos los componentes

El siguiente paso es conectar el servidor de productos local con la aplicación ASP.NET MVC.

1.  Si no está abierto, vuelva a abrir en Visual Studio el proyecto **ProductsPortal** que ha creado en la sección "Creación de una aplicación ASP.NET MVC".

2.  Agregue el paquete NuGet a las referencias del proyecto de forma similar al paso de la sección "Creación de un servidor local". En el Explorador de soluciones, haga clic con el botón secundario en **References** y, a continuación, en **Manage NuGet Packages**.

3.  Busque "Bus de servicio" y seleccione el elemento **Bus de servicio de Microsoft Azure**. Después finalice la instalación y cierre este cuadro de diálogo.

4.  En el Explorador de soluciones, haga clic con el botón secundario en el proyecto **ProductsPortal** y, a continuación, haga clic en **Add** y, finalmente, en **Existing Item**.

5.  Desplácese al archivo **ProductsContract.cs** desde el proyecto de consola **ProductsServer**. Haga clic para resaltar ProductsContract.cs. Haga clic en la flecha abajo situada junto a **Agregar** y, a continuación, haga clic en **Agregar como vínculo**.

	![][24]

6.  Ahora abra el archivo **HomeController.cs** en el editor de Visual Studio y sustituya la definición del espacio de nombres por el código siguiente. Asegúrese de reemplazar *yourServiceNamespace* por el nombre de su espacio de nombres de servicio y *yourKey* por su clave de SAS. Esto permitirá que el cliente llame al servicio local y que se devuelva el resultado de la llamada.

            namespace ProductsWeb.Controllers
            {
                using System.Linq;
                using System.ServiceModel;
                using System.Web.Mvc;
                using Microsoft.ServiceBus;
                using Models;
                using ProductsServer;

                public class HomeController : Controller
                {
                    // Declare the channel factory.
                    static ChannelFactory<IProductsChannel> channelFactory;

                    static HomeController()
                    {
                        // Create shared secret token credentials for authentication.
                        channelFactory = new ChannelFactory<IProductsChannel>(new NetTcpRelayBinding(),
                            "sb://yourServiceNamespace.servicebus.windows.net/products");
                        channelFactory.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior {
                            TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                                "RootManageSharedAccessKey", "yourKey") });
                    }

                    public ActionResult Index()
                    {
                        using (IProductsChannel channel = channelFactory.CreateChannel())
                        {
                            // Return a view of the products inventory.
                            return this.View(from prod in channel.GetProducts()
                                             select
                                                 new Product { Id = prod.Id, Name = prod.Name,
                                                     Quantity = prod.Quantity });
                        }
                    }
                }
            }
7.  En el Explorador de soluciones, haga clic con el botón secundario en la solución **ProductsPortal** y, a continuación, haga clic en **Add** y, finalmente, en **Existing Project**.

8.  Desplácese al proyecto **ProductsServer** y haga doble clic en la solución **ProductsServer.csproj** para agregarla.

9.  En el Explorador de soluciones, haga clic con el botón secundario en la solución **ProductsPortal** y, a continuación, haga clic en **Properties**.

10. A la izquierda, haga clic en **Startup Project**. A la derecha, haga clic en **Multiple startup projects**. Asegúrese de que **ProductsServer**, **ProductsPortal.Azure** y **ProductsPortal** aparezcan, en ese orden, con **Start** establecido como acción para **ProductsServer** y **ProductsPortal.Azure**, y **None** establecido como acción para **ProductsPortal**.

      ![][25]

11. Aún en el cuadro de diálogo **Properties**, haga clic en **ProjectDependencies** a la izquierda.

12. En la lista **Projects**, haga clic en **ProductsServer**. Asegúrese de que **ProductsPortal** no esté seleccionado y que **ProductsPortal.Azure** sí lo esté. A continuación, haga clic en **Aceptar**:

    ![][26]

## Ejecución de la aplicación

1.  En el menú **Archivo** de Visual Studio, haga clic en **Guardar todo**.

2.  Presione F5 para compilar y ejecutar la aplicación. Primero debe iniciarse el servidor local (la aplicación de consola **ProductsServer**) y, a continuación, debe iniciarse la aplicación **ProductsWeb** en una ventana del explorador, tal como se muestra en la captura de pantalla siguiente. Esta vez verá que el inventario de productos muestra los datos recuperados del sistema local del servicio de productos.

    ![][1]

## Implementación de su aplicación en Azure

1.  Haga clic con el botón derecho en el proyecto **ProductsPortal** en el Explorador de soluciones y haga clic en **Publicar en Microsoft Azure**.

2.  Para ver todas sus suscripciones, es posible que tenga que iniciar sesión.

    Haga clic en **Iniciar sesión para ver más suscripciones**:

    ![][27]

3.  Inicie sesión con su cuenta de Microsoft.

8.  Haga clic en **Siguiente**. Si la suscripción no contiene servicios hospedados, se le pedirá que cree uno. El servicio hospedado actúa como contenedor para su aplicación en su suscripción de Azure. Escriba un nombre que identifique su aplicación y elija la región para la que debe optimizarse la aplicación (cabe esperar que los usuarios que obtengan acceso a ella desde esta región tengan tiempos de carga inferiores).

9.  Seleccione el servicio hospedado en el que desea publicar la aplicación. En la configuración restante, conserve los valores predeterminados, como se muestra a continuación. Haga clic en **Siguiente**.

    ![][33]

10. En la última página, haga clic en **Publish** para iniciar el proceso de implementación:

    ![][34] Esta operación durará aproximadamente cinco a siete minutos. Al ser la primera vez que realiza una publicación, Azure aprovisiona una máquina virtual (VM), endurece la seguridad, crea un rol web en la VM para hospedar la aplicación, implementa el código en dicho rol web y, por último, configura el equilibrador de carga y las redes a fin de que la aplicación esté disponible para el público.

11. Mientras la publicación esté en curso podrá supervisar la actividad en la ventana **Registro de actividad de Azure**, que suele estar acoplada a la parte inferior de Visual Studio o Visual Web Developer:

    ![][35]

12. Cuando la implementación haya finalizado, puede ver el sitio web haciendo clic en el vínculo **Dirección URL de sitio web** de la ventana de supervisión.

    ![][36]

    El sitio web depende del servidor local, por lo que debe ejecutar la aplicación **ProductsServer** localmente para que el sitio web funcione correctamente. Al realizar solicitudes en el sitio web en la nube, verá que las solicitudes entran en su aplicación de consola local, tal como indica la salida de "GetProducts called" que se muestra en la captura de pantalla siguiente.

    ![][37]

Para obtener más información sobre la diferencia entre sitios web y servicios en la nube, consulte [Modelos de ejecución de Azure][executionmodels].

## Detención y eliminación de una aplicación

Después de implementar su aplicación, puede deshabilitarla, por lo que puede compilar e implementar otras aplicaciones dentro de las 750 horas gratuitas al mes (31 días al mes) de tiempo del servidor.

Azure factura las instancias de rol web por hora consumida de tiempo de servidor. El tiempo de servidor se empieza a consumir una vez implementada su aplicación, incluso si las instancias no se están ejecutando y se encuentran detenidas. Una cuenta gratuita incluye 750 horas gratuitas al mes (31 días al mes) de tiempo de servidor de la máquina virtual para hospedar estas instancias de rol web.

Los siguientes pasos muestran cómo detener y eliminar su aplicación.

1.  Inicie sesión en el [Portal de Azure clásico][], haga clic en **Servicios en la nube** y, a continuación, en el nombre del servicio.

2.  Haga clic en la pestaña **Panel** y, a continuación, en **Detener** para suspender temporalmente la aplicación. Para volver a iniciarla, haga clic en **Iniciar**. Haga clic en **Eliminar** para quitar la aplicación totalmente de Azure sin la posibilidad de restaurarla.

	![][43]

## Pasos siguientes  

Para obtener más información sobre el bus de servicio, consulte los siguientes recursos:

* [Bus de servicio de Azure][sbwacom]  
* [Utilización de las colas del Bus de servicio][sbwacomqhowto]  


  [0]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hybrid.png
  [1]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
  [Obtener herramientas y SDK]: http://go.microsoft.com/fwlink/?LinkId=271920
  [NuGet]: http://nuget.org
  [2]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-3.png
  [3]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-42-webpi.png


  [Portal de Azure clásico]: http://manage.windowsazure.com
  [5]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/sb-queues-03.png
  [6]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/sb-queues-04.png



  [Using the NuGet Service Bus Package]: https://msdn.microsoft.com/library/azure/dn741354.aspx
  [10]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-1.png
  [11]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-con-1.png
  [12]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-con-3.png
  [13]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-13.png
  [14]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-con-4.png
  [15]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-2.png
  [16]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-4.png
  [17]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-7.jpg
  [18]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-10.jpg

  [20]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-11.png
  [21]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App1.png
  [22]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-21.png
  [23]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-22.png
  [24]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-12.png
  [25]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-13.png
  [26]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-14.png
  [27]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-33.png


  [30]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-36.png
  [31]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-37.png
  [32]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-38.png
  [33]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-39.png
  [34]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-40.png
  [35]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-41.png
  [36]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
  [37]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service1.png
  [38]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-27.png
  [39]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/sb-queues-09.png
  [40]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/sb-queues-06.png
  [41]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-40.png
  [42]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-41.png
  [43]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-43.png
  [45]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-45.png

  [sbwacom]: /documentation/services/service-bus/
  [sbwacomqhowto]: service-bus-dotnet-how-to-use-queues.md
  [executionmodels]: ../cloud-services/fundamentals-application-models.md

<!---HONumber=AcomDC_0128_2016-->