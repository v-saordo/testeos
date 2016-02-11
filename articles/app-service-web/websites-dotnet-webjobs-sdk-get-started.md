<properties
	pageTitle="Creación de un WebJob .NET en el Servicio de aplicaciones de Azure | Microsoft Azure"
	description="Cree una aplicación de varios niveles utilizando ASP.NET MVC y Azure. El front-end se ejecuta en una aplicación web del Servicio de aplicaciones de Azure y el back-end se ejecuta como un WebJob. La aplicación usa Entity Framework, base de datos SQL, y colas y blobs de almacenamiento de Azure."
	services="app-service"
	documentationCenter=".net"
	authors="tdykstra"
	manager="wpickett"
	editor="mollybos"/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="12/14/2015"
	ms.author="tdykstra"/>

# Creación de un WebJob .NET en el Servicio de aplicaciones de Azure

Este tutorial muestra cómo escribir código para una aplicación de ASP.NET MVC 5 sencilla de niveles múltiples que usa el [SDK de WebJobs](websites-dotnet-webjobs-sdk.md).

El propósito del [SDK de WebJobs](websites-webjobs-resources.md) es simplificar el código que escriba para las tareas comunes que puede realizar un WebJob, como procesamiento de imágenes, procesamiento de colas, agregación de RSS, mantenimiento de archivos y enviar de correos electrónicos. El SDK de WebJobs tiene características integradas para trabajar con Almacenamiento de Azure y Bus de servicio, para programar tareas y controlar errores, y para muchos otros escenarios comunes. Además, se ha diseñado para ser extensible y hay un [repositorio de código abierto para las extensiones](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview).

La aplicación de ejemplo es un tablón de anuncios publicitario. Los usuarios pueden cargar imágenes de anuncios y un proceso back-end convierte las imágenes en miniaturas. La página de lista de anuncios muestra las miniaturas y la página de detalles de anuncios muestra la imagen con su tamaño completo. A continuación se muestra una captura de pantalla:

![Ad list](./media/websites-dotnet-webjobs-sdk-get-started/list.png)

Esta aplicación de ejemplo funciona con [Colas de Azure](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/queue-centric-work-pattern) y [Blobs de Azure](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/unstructured-blob-storage). El tutorial muestra cómo implementar la aplicación en [Servicio de aplicaciones de Azure](http://go.microsoft.com/fwlink/?LinkId=529714) y en [Base de datos SQL de Azure](http://msdn.microsoft.com/library/azure/ee336279).

## <a id="prerequisites"></a>Requisitos previos

En el tutorial se presupone que sabe cómo trabajar con proyectos de [ASP.NET MVC 5](http://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started) en Visual Studio.

El tutorial se escribió para Visual Studio 2013. Si no aún no tiene Visual Studio, se instalará automáticamente al instalar el SDK de Azure para .NET.

El tutorial se puede usar con Visual Studio 2015 pero, antes de ejecutar la aplicación localmente, tiene que cambiar la parte `Data Source` de la cadena de conexión LocalDB de SQL Server en los archivos Web.config y App.config de `Data Source=(localdb)\v11.0` a `Data Source=(LocalDb)\MSSQLLocalDB`.

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note2.md)]

## <a id="learn"></a>Temas que se abordarán

En el tutorial se muestra cómo realizar las siguientes tareas:

* Habilitar su equipo para desarrollar contenido de Azure mediante la instalación del SDK de Azure.
* Crear un proyecto de aplicación de consola que se implemente automáticamente como un WebJob de Azure cuando implemente el proyecto web asociado.
* Probar un back-end del SDK de WebJobs en modo local en el equipo de desarrollo.
* Publicar una aplicación con un back-end de WebJobs en una aplicación web en del Servicio de aplicaciones.
* Cargar archivos y almacenarlos en el servicio Blob de Azure.
* Usar el SDK de WebJobs de Azure para trabajar con colas y blobs de Almacenamiento de Azure.

## <a id="contosoads"></a>Arquitectura de la aplicación

La aplicación de ejemplo usa el [patrón de trabajo centrado en colas](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/queue-centric-work-pattern) para descargar el trabajo de uso intensivo de CPU de creación de miniaturas y pasarlo a un proceso back-end.

La aplicación almacena anuncios en una base de datos SQL, usando Entity Framework Code First para crear las tablas y acceder a los datos. Por cada anuncio, la base de datos almacena dos direcciones URL, una para la imagen a tamaño completo y otra para la miniatura.

![Ad table](./media/websites-dotnet-webjobs-sdk-get-started/adtable.png)

Cuando un usuario carga una imagen, la aplicación web la almacena en un [blob de Azure](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/unstructured-blob-storage) y la información del anuncio se guarda en la base de datos con una dirección URL que apunta al blob. Al mismo tiempo, escribe mensaje en una cola de Azure. En un proceso back-end que se ejecuta como un Webjob de Azure, el SDK de WebJobs sondea la cola en busca de mensajes nuevos. Cuando aparece un mensaje nuevo, el WebJob crea una miniatura para la imagen y actualiza el campo de base de datos de la dirección URL de la miniatura para ese anuncio. A continuación puede ver un diagrama que muestra cómo interactúan las partes de la aplicación:

![Contoso Ads architecture](./media/websites-dotnet-webjobs-sdk-get-started/apparchitecture.png)

[AZURE.INCLUDE [install-sdk](../../includes/install-sdk-2015-2013.md)]

Las instrucciones del tutorial se aplican al SDK de Azure para .NET 2.7.1 o posterior.

## <a id="storage"></a>Creación de una cuenta de almacenamiento de Azure

Una cuenta de almacenamiento de Azure proporciona recursos para almacenar datos de cola y blob en la nube. El SDK de WebJobs también usa esta cuenta para almacenar datos para el panel.

En una aplicación real, normalmente crea cuentas independientes para los datos de aplicación frente a los datos de registro, y cuentas diferentes para datos de prueba frente a datos de producción. Para este tutorial, usará solamente una cuenta.

1. Abra la ventana **Explorador de servidores** de Visual Studio.

2. Haga clic con el botón derecho en el nodo **Azure** y, después, haga clic en **Conectar con Microsoft Azure**.
![Conexión a Azure](./media/websites-dotnet-webjobs-sdk-get-started/connaz.png)

3. Inicie sesión con sus credenciales de Azure.

5. Haga clic con el botón derecho en **Almacenamiento** en el nodo de Azure y, a continuación, haga clic en **Crear cuenta de almacenamiento**. ![Crear una cuenta de almacenamiento](./media/websites-dotnet-webjobs-sdk-get-started/createstor.png)

3. En el cuadro de diálogo **Crear cuenta de almacenamiento**, escriba un nombre para la cuenta de almacenamiento.

	El nombre debe ser único (ninguna otra cuenta de almacenamiento de Azure puede tener el mismo nombre). Si el nombre especificado ya está en uso, tendrá la oportunidad de cambiarlo.

	La dirección URL para tener acceso a su cuenta de almacenamiento será *{name}*.core.windows.net.

5. Establezca la lista desplegable **Grupo de afinidad o región** en la región más cercana a usted.

	Esta configuración especifica el centro de datos de Azure que va a almacenar la cuenta de almacenamiento. En este tutorial, la opción que elija no tendrá mucha repercusión. Sin embargo, en una aplicación web de producción se recomienda que el servidor web y la cuenta de almacenamiento estén en la misma región a fin de minimizar la latencia y los cargos por concepto de salida de los datos. El centro de datos de la aplicación web (que creará posteriormente) debe estar lo más próximo posible a los exploradores que obtienen acceso a la aplicación web con el fin de minimizar la latencia.

6. Establezca la lista desplegable **Replicación** en **Localmente redundante**.

	Cuando se habilita la replicación geográfica para una cuenta de almacenamiento, el contenido almacenado se replica en un centro de datos secundario para habilitar la conmutación por error en caso de que se produzca un desastre importante en la ubicación principal. La replicación geográfica puede suponer costes adicionales. Lo normal es que no quiera pagar por el servicio de replicación geográfica para las cuentas de prueba y desarrollo. Para obtener más información, consulte [Creación, administración o eliminación de una cuenta de almacenamiento](../storage-create-storage-account/#replication-options).

5. Haga clic en **Crear**.

	![New storage account](./media/websites-dotnet-webjobs-sdk-get-started/newstorage.png)

## <a id="download"></a>Descarga de la aplicación

1. Descargue y descomprima la [solución completa](http://code.msdn.microsoft.com/Simple-Azure-Website-with-b4391eeb).

2. Inicie Visual Studio.

3. En el menú **Archivo**, seleccione **Abrir > Proyecto/Solución**, vaya a la ubicación donde descargó la solución y abra el archivo de la solución.

4. Presione CTRL+MAYÚS+B para compilar la solución.

	De forma predeterminada, Visual Studio restaura automáticamente el contenido del paquete NuGet, que no se incluyó en el archivo *.zip*. Si los paquetes no se restauran, instálelos manualmente mediante el cuadro de diálogo **Administrar paquetes NuGet para la solución** y haciendo clic en el botón **Restaurar** situado en la parte superior derecha.

5. En el **Explorador de soluciones**, asegúrese de que **ContosoAdsWeb** se encuentra seleccionado como proyecto de inicio.

## <a id="configurestorage"></a>Configuración de la aplicación para usar una cuenta de almacenamiento

1. Abra el archivo *Web.config* de la aplicación en el proyecto ContosoAdsWeb.

	El archivo contiene una cadena de conexión SQL y una cadena de conexión de almacenamiento de Azure para trabajar con blobs y colas.

	La cadena de conexión SQL apunta a una base de datos [SQL Server Express LocalDB](http://msdn.microsoft.com/library/hh510202.aspx).

	La cadena de conexión de almacenamiento es un ejemplo que tiene marcadores de posición para la clave de acceso y el nombre de la cuenta de almacenamiento. Se sustituirá por una cadena de conexión con el nombre y la clave de su cuenta de almacenamiento.

	<pre class="prettyprint">&lt;connectionStrings>
	  &lt;add name="ContosoAdsContext" connectionString="Data Source=(localdb)\v11.0; Initial Catalog=ContosoAds; Integrated Security=True; MultipleActiveResultSets=True;" providerName="System.Data.SqlClient" />
	  &lt;add name="AzureWebJobsStorage" connectionString="DefaultEndpointsProtocol=https;AccountName=<mark>[nombredecuenta]</mark>;AccountKey=<mark>[clavedeacceso]</mark>"/>
	&lt;/connectionStrings></pre>

	La cadena de conexión de almacenamiento se denomina AzureWebJobsStorage porque ese es el nombre que el SDK de WebJobs usa de forma predeterminada. Aquí se usa el mismo nombre, de modo que solo tenga que establecer el valor de una cadena de conexión en el entorno de Azure.

2. En el **Explorador de servidores**, haga clic con el botón secundario en la cuenta de almacenamiento en el nodo **Almacenamiento** y, a continuación, haga clic en **Propiedades**.

	![Haga clic en propiedades de cuenta de almacenamiento](./media/websites-dotnet-webjobs-sdk-get-started/storppty.png)

3. En la ventana **Propiedades**, haga clic en **Claves de cuenta de almacenamiento** y, a continuación, haga clic en el botón de puntos suspensivos.

	![New storage account](./media/websites-dotnet-webjobs-sdk-get-started/newstorage.png)

4. Copie la **cadena de conexión**.

	![Cuadro de diálogo Claves de cuenta de almacenamiento](./media/websites-dotnet-webjobs-sdk-get-started/cpak.png)

5. Reemplace la cadena de conexión de almacenamiento en el archivo *Web.config* por la cadena de conexión que acaba de copiar. Asegúrese de seleccionar todos los elementos dentro de las comillas, pero sin incluir las comillas antes de pegar.

6. Abra el archivo *App.config* del proyecto ContosoAdsWebJob.

	Este archivo tiene dos cadenas de conexión de almacenamiento, una para los datos de aplicación y otra para registro. Puede utilizar cuentas de almacenamiento independientes para los datos de aplicación y el registro, y usar [varias cuentas de almacenamiento para datos](https://github.com/Azure/azure-webjobs-sdk/blob/master/test/Microsoft.Azure.WebJobs.Host.EndToEndTests/MultipleStorageAccountsEndToEndTests.cs). Para este tutorial, usará solamente una única cuenta de almacenamiento. Las cadenas de conexión tienen marcadores de posición para las claves de la cuenta de almacenamiento. 
  	<pre class="prettyprint">&lt;configuration&gt;
    &lt;connectionStrings&gt;
        &lt;add name="AzureWebJobsDashboard" connectionString="DefaultEndpointsProtocol=https;AccountName=<mark>[accountname]</mark>;AccountKey=<mark>[accesskey]</mark>"/&gt;
        &lt;add name="AzureWebJobsStorage" connectionString="DefaultEndpointsProtocol=https;AccountName=<mark>[accountname]</mark>;AccountKey=<mark>[accesskey]</mark>"/&gt;
        &lt;add name="ContosoAdsContext" connectionString="Data Source=(localdb)\v11.0; Initial Catalog=ContosoAds; Integrated Security=True; MultipleActiveResultSets=True;"/&gt;
    &lt;/connectionStrings&gt;
        &lt;startup&gt;
            &lt;supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" /&gt;
    &lt;/startup&gt;
&lt;/configuration&gt;</pre>

	De forma predeterminada, el SDK de WebJobs busca cadenas de conexión llamadas AzureWebJobsStorage y AzureWebJobsDashboard. Como opción alternativa, puede [almacenar la cadena de conexión que desee y pasarla de forma explícita al objeto `JobHost`](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#config).

7. Reemplace ambas cadenas de conexión de almacenamiento por la cadena de conexión que copió anteriormente.

8. Guarde los cambios.

## <a id="run"></a>Ejecución de la aplicación de forma local

1. Para iniciar el front-end web de la aplicación, presione CTRL+F5.

	El explorador predeterminado se abre en la página principal. (El proyecto web se ejecuta porque lo ha creado en el proyecto de inicio.)

	![Página principal de Contoso Ads](./media/websites-dotnet-webjobs-sdk-get-started/home.png)

2. Para iniciar el back-end del WebJob de la aplicación, haga clic con el botón secundario en el proyecto ContosoAdsWebJob en el **Explorador de soluciones** y, a continuación, haga clic en **Depurar** > **Iniciar nueva instancia**.

	Una ventana de aplicación de consola se abre y muestra los mensajes de registro, indicando que el objeto WebJobs SDK JobHost ha empezado a ejecutarse.

	![Ventana de la aplicación de consola con el back-end en ejecución](./media/websites-dotnet-webjobs-sdk-get-started/backendrunning.png)

3. En el explorador, haga clic en **Crear un anuncio**.

4. Escriba algunos datos de prueba y seleccione una imagen para cargar; a continuación, haga clic en **Crear**.

	![Create page](./media/websites-dotnet-webjobs-sdk-get-started/create.png)

	La aplicación irá a la página Index, pero no mostrará una miniatura para el nuevo anuncio porque ese procesamiento todavía no se ha llevado a cabo.

	Transcurridos unos instantes, aparece un mensaje de registro en la ventana de la aplicación de consola que muestra que se ha recibido y se ha procesado un mensaje de cola.

	![Ventana de la aplicación de consola que muestra que se ha procesado un mensaje de cola](./media/websites-dotnet-webjobs-sdk-get-started/backendlogs.png)

5. Después de que aparezcan los mensajes de registro en la ventana de la aplicación de consola, actualice la página del índice para ver la miniatura.

	![Página de índice](./media/websites-dotnet-webjobs-sdk-get-started/list.png)

6. Haga clic en el vínculo **Detalles** correspondiente a su anuncio para ver la imagen a tamaño completo.

	![Details page](./media/websites-dotnet-webjobs-sdk-get-started/details.png)

Aunque haya ejecutado la aplicación en el equipo local y use una base de datos SQL Server ubicada en su equipo, la aplicación trabaja con colas y blobs en la nube. En la siguiente sección ejecutará la aplicación en la nube mediante una base de datos en la nube, así como blobs y colas en la nube.

## <a id="runincloud"></a>Ejecución de la aplicación en la nube

Llevará a cabo los pasos siguientes para ejecutar la aplicación en la nube:

* Impleméntela en aplicaciones Web. Visual Studio crea automáticamente una aplicación web en el Servicio de aplicaciones y una instancia de Base de datos SQL.
* Configure la aplicación web para que use una cuenta de almacenamiento y una base de datos SQL de Azure.

Después de crear algunos anuncios mientras ejecuta la aplicación en la nube, consulte el panel del SDK de WebJobs para ver las completas características de supervisión que ofrece.

### Implementación en aplicaciones web

1. Cierre el explorador y la ventana de la aplicación de consola.

2. En el **Explorador de soluciones**, haga clic con el botón secundario en el proyecto ContosoAdsWeb y, a continuación, haga clic en **Publicar**.

3. En el paso **Perfil** del asistente para **publicación web**, haga clic en **Aplicaciones web de Microsoft Azure**.

	![Seleccione el destino de la publicación de aplicación web de Azure](./media/websites-dotnet-webjobs-sdk-get-started/pubweb.png)

4. Inicie sesión en Azure si aún no hizo.

5. Haga clic en **Nuevo**.

	El cuadro de diálogo puede ser ligeramente diferente dependiendo de qué versión del SDK de Azure para .NET tenga instalado.

	![Haga clic en Nuevo](./media/websites-dotnet-webjobs-sdk-get-started/clicknew.png)

6. En el cuadro de diálogo **Crear aplicación web en Microsoft Azure**, especifique un nombre único en el cuadro **Nombre de la aplicación web**.

	La dirección URL completa consistirá en lo que escriba más .azurewebsites.net (como se muestra junto al cuadro de texto **Nombre de la aplicación web**). Por ejemplo, si el nombre de la aplicación web es ContosoAds, la URL será ContosoAds.azurewebsites.net.

7. En la lista desplegable [Plan de Servicio de aplicaciones](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md), elija **Crear nuevo plan de servicio de aplicaciones**. Escriba un nombre para el plan de Servicio de aplicaciones, como ContosoAdsPlan.

8. En la lista desplegable [Grupo de recursos](../resource-group-overview.md), elija **Crear nuevo grupo de recursos**.

9. Escriba un nombre para el grupo de recursos, como ContosoAdsGroup.

10. En la lista desplegable **Región**, elija la misma región que seleccionó para la cuenta de almacenamiento.

	Este valor especifica el centro de datos de Azure en el que se ejecutará la aplicación web. Al mantener la aplicación web y la cuenta de almacenamiento en el mismo centro de datos, se minimiza la latencia y los cargos por concepto de salida de los datos.

11. En la lista desplegable **Base de datos**, elija **Crear nuevo servidor**.

12. Escriba un nombre para el servidor de base de datos, por ejemplo, contosoadsserver + un número o su nombre para que el nombre de servidor sea único.

	El nombres de servidor debe ser único. Puede contener minúsculas, números y guiones, No puede contener un guion al final.

	Asimismo, si su suscripción ya tiene un servidor, puede seleccionarlo en la lista desplegable.

12. Complete los campos **Nombre de usuario de base de datos** y **Contraseña de base de datos** con los datos de un administrador.

	Si seleccionó **Nuevo servidor de Base de datos SQL**, no debe escribir un nombre y una contraseña existentes en estos campos, sino definir unos nuevos que volverá a utilizar más adelante para obtener acceso a la base de datos. Si seleccionó un servidor creado anteriormente, se le pedirá la contraseña de la cuenta de usuario administrativa que ya creó.

13. Haga clic en **Crear**.

	![Cuadro de diálogo Crear aplicación web en Microsoft Azure](./media/websites-dotnet-webjobs-sdk-get-started/newdb.png)

	Visual Studio crea la solución, el proyecto web, la aplicación web de Azure y la instancia de Base de datos SQL de Azure.

14. En el paso **Conexión** del asistente para **publicación web**, haga clic en **Siguiente**.

	![Paso Conexión](./media/websites-dotnet-webjobs-sdk-get-started/connstep.png)

15. En el paso **Configuración**, desactive la casilla **Usar esta cadena de conexión en tiempo de ejecución** y haga clic en **Siguiente**.

	![Settings step](./media/websites-dotnet-webjobs-sdk-get-started/settingsstep.png)

	No es necesario usar el cuadro de diálogo de publicación para establecer la cadena de conexión SQL porque puede hacerlo posteriormente en el entorno de Azure.

	Puede omitir las advertencias de esta página.

	* Generalmente, la cuenta de almacenamiento que se usa cuando se ejecuta en Azure es distinta a la que se usa en el modo de ejecución local, pero para este tutorial usaremos la misma en ambos entornos. Así pues, no es necesario cambiar la cadena de conexión AzureWebJobsStorage. Aunque deseara usar una cuenta de almacenamiento diferente en la nube, no es necesario transformar la cadena de conexión porque la aplicación usa una configuración del entorno de Azure cuando se ejecute en Azure. Veremos este procedimiento posteriormente en el tutorial.

	* En este tutorial, no se realizarán cambios en el modelo de datos usado para la base de datosContosoAdsContext, por lo que no es necesario usar migraciones de Entity Framework Code First para la implementación. Code First crea automáticamente una base de datos nueva la primera vez que la aplicación intente tener acceso a datos SQL.

	Para este tutorial, los valores predeterminados de las opciones de **Opciones de publicación de archivos** son correctos.

16. En el paso **Vista previa**, haga clic en **Iniciar vista previa**.

	![Haga clic en Iniciar vista previa](./media/websites-dotnet-webjobs-sdk-get-started/previewstep.png)

	Puede omitir la advertencia acerca de que no se van a publicar bases de datos. Entity Framework Code First crea la base de datos, no es necesario publicarla.

	En la ventana de vista previa se muestran los archivos binarios y de configuración del proyecto de WebJob que se copiarán en la carpeta *app\_data\\jobs\\continuous* de la aplicación web.

	![Archivos de WebJobs en la ventana de vista previa](./media/websites-dotnet-webjobs-sdk-get-started/previewwjfiles.png)

17. Haga clic en **Publicar**.

	Visual Studio implementa la aplicación y abre la URL de la página principal en el explorador.

	No podrá usar la aplicación hasta que establezca las cadenas de conexión en el entorno de Azure en la siguiente sección. Aparecerá una página de error o la página principal, en función de las opciones de creación de la aplicación web y de la base de datos elegidas anteriormente.

### Configure la aplicación web para que use una cuenta de almacenamiento y una base de datos SQL de Azure.

Un procedimiento recomendado de seguridad consiste en [evitar insertar información confidencial como cadenas de conexión en archivos que se almacenen en repositorios de código fuente](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#secrets). Azure proporciona una manera de hacerlo: puede establecer la cadena de conexión y otros valores de configuración en el entorno de Azure, y las API de configuración de ASP.NET recogen estos valores cuando la aplicación se ejecuta en Azure. Puede definir estos valores en Azure con el **Explorador de servidores**, el Portal de Azure, Windows PowerShell o la interfaz de la línea de comandos multiplataforma. Para obtener más información, consulte [Funcionamiento de las cadenas de aplicación y de las cadenas de conexión](/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/).

En esta sección se usa el **Explorador de servidores** para definir los valores de cadena de conexión en Azure.

7. En el **Explorador de servidores**, haga clic con el botón derecho en la aplicación web en **Azure > {su grupo de recursos}** y, a continuación, haga clic en **Ver configuración**.

	La ventana **Aplicación web de Azure** se abrirá en la pestaña **Configuración**.

9. Cambie el nombre de la cadena de conexión DefaultConnection a ContosoAdsContext.

	Azure creó esta cadena de conexión automáticamente cuando creó la aplicación con una base de datos asociada, por lo que ya tiene el valor de cadena de conexión correcto. Solo cambiará el nombre de lo que el código está buscando.

9. Agregue dos cadenas de conexión nuevas, llamadas AzureWebJobsStorage y AzureWebJobsDashboard. Establezca el tipo en Personalizado y establezca el valor de cadena de conexión en el mismo valor que usó anteriormente para los archivos *Web.config* y *App.config*. (Asegúrese de incluir la cadena de conexión completa, no solo la clave de acceso, sin comillas.)

	El SDK de WebJobs usa estas cadenas de conexión, una para los datos de la aplicación y otra para el registro. Como vimos anteriormente, el código del front-end web también usa la cadena de datos de la aplicación.

9. Haga clic en **Guardar**.

	![Cadenas de conexión en el Portal de Azure](./media/websites-dotnet-webjobs-sdk-get-started/azconnstr.png)

10. En el **Explorador de servidores**, haga clic con el botón derecho en la aplicación web y, a continuación, haga clic en **Detener**.

12. Una vez detenida la aplicación web, haga clic con el botón derecho nuevamente en la aplicación web y, a continuación, haga clic en **Iniciar**.

	El WebJob se inicia automáticamente al publicar, pero se detiene cuando se realiza un cambio en la configuración. Para reiniciarlo, puede reiniciar la aplicación web o el WebJob en el [Portal de Azure](http://go.microsoft.com/fwlink/?LinkId=529715). Generalmente, se recomienda reiniciar la aplicación web después de cambiar la configuración.

9. Actualice la ventana del explorador que contiene la URL de la aplicación web en la barra de direcciones.

	Se abre la página principal.

10. Cree un anuncio, tal y como hizo cuando ejecutó la aplicación de forma local.

	Se muestra la página del índice sin ninguna miniatura al principio.

11.	Actualice la página después de unos segundos y la miniatura aparece.

	Si no aparece la miniatura, tendrá que esperar un minuto para que el WebJob se reinicie. Si después de un cierto tiempo sigue sin ver la miniatura al actualizar la página, quizás el WebJob no se inició automáticamente. En ese caso, vaya a la pestaña WebJobs en la página [Portal clásico](https://manage.windowsazure.com) de la aplicación web y, a continuación, haga clic en **Iniciar**.

### Visualización del panel del SDK de WebJob

1. En el [Portal clásico](https://manage.windowsazure.com), seleccione la aplicación web.

2. Haga clic en la pestaña **WebJobs**.

3. Haga clic en la URL de la columna de registros correspondiente al WebJob.

	![Pestaña WebJobs](./media/websites-dotnet-webjobs-sdk-get-started/wjtab.png)

	Se abre una nueva pestaña de explorador con el panel del SDK de WebJobs. El panel indica que el WebJob se está ejecutando y muestra una lista de funciones en el código que el SDK de WebJobs ha desencadenado.

4. Haga clic en una de las funciones para ver más detalles sobre su ejecución

	![Panel del SDK de WebJobs](./media/websites-dotnet-webjobs-sdk-get-started/wjdashboardhome.png)

	![Panel del SDK de WebJobs](./media/websites-dotnet-webjobs-sdk-get-started/wjfunctiondetails.png)

	El botón de **función de reproducción** de esta página sirve para que el marco de trabajo del SDK de WebJobs llame de nuevo a la función y le permite cambiar los datos que se pasan primero a la función.

>[AZURE.NOTE]Cuando finalice las pruebas, elimine la aplicación web y la instancia de Base de datos SQL. La aplicación web es gratuita, pero la instancia de Base de datos SQL y la cuenta de almacenamiento tienen un costo (mínimo dado su pequeño tamaño). Asimismo, si deja la aplicación ejecutándose, cualquiera que encuentre su dirección URL puede crear y ver anuncios. En el Portal clásico, vaya a la pestaña **Panel** correspondiente a la aplicación web y, a continuación, haga clic en el botón **Eliminar** en la parte inferior de la página. A continuación, puede activar una casilla para eliminar la instancia de la Base de datos SQL al mismo tiempo. Si lo que desea es evitar temporalmente que otros tengan acceso a la aplicación, haga clic en **Detener**. En ese caso, se seguirán acumulando cargos para la cuenta de almacenamiento y la base de datos SQL. Puede seguir un procedimiento similar para eliminar la base de datos SQL y la cuenta de almacenamiento cuando ya no las necesite.

## <a id="create"></a>Creación de la aplicación desde cero

En esta sección realizará las siguientes tareas:

* Crear una solución de Visual Studio con un proyecto web.
* Agregar un proyecto de biblioteca de clases para la capa de acceso a datos que comparten el front-end y el back-end.
* Agregar un proyecto de aplicación de consola al back-end con la implementación de WebJobs habilitada.
* Agregar paquetes NuGet.
* Establecer preferencias del proyecto.
* Copiar los archivos de configuración y de código de la aplicación desde la aplicación descargada con la que trabajó en la sección anterior de este tutorial.
* Revisar las partes del código que funcionan con blobs y colas de Azure y el SDK de WebJobs.

### Creación de una solución de Visual Studio con un proyecto web y un proyecto de biblioteca de clases

1. En Visual Studio, elija **Nuevo** > **Proyecto** en el menú **Archivo**.

2. En el cuadro de diálogo **Nuevo proyecto**, elija **Visual C#** > **Web** > **Aplicación web ASP.NET**.

3. Asigne el nombre ContosoAdsWeb al proyecto, llame ContosoAdsWebJobsSDK a la solución (cambie el nombre de la solución si lo coloca en la misma carpeta que la solución descargada) y, a continuación, haga clic en **Aceptar**.

	![Nuevo proyecto](./media/websites-dotnet-webjobs-sdk-get-started/newproject.png)

5. En el cuadro de diálogo **Nuevo proyecto ASP.NET**, elija la plantilla MVC y desactive la casilla **Host en la nube** en **Microsoft Azure**.

	Al seleccionar **Host en la nube** se permite que Visual Studio cree automáticamente una nueva aplicación web de Azure y Base de datos SQL. Como ya los creó anteriormente, no es necesario hacerlo mientras crea el proyecto. Si desea crearlos, active la casilla de verificación. A continuación, puede configurar la nueva aplicación web y base de datos SQL al igual que lo hizo anteriormente cuando implementó la aplicación.

5. Haga clic en **Cambiar autenticación**.

	![Change Authentication](./media/websites-dotnet-webjobs-sdk-get-started/chgauth.png)

7. En el cuadro de diálogo **Cambiar autenticación**, elija **Sin autenticación** y haga clic en **Aceptar**.

	![Sin autenticación](./media/websites-dotnet-webjobs-sdk-get-started/noauth.png)

8. En el cuadro de diálogo **Nuevo proyecto ASP.NET**, haga clic en **Aceptar**.

	Visual Studio crea la solución y el proyecto web.

9. En el **Explorador de soluciones**, haga clic con el botón secundario en la solución (no en el proyecto) y elija **Agregar** > **Nuevo proyecto**.

11. En el cuadro de diálogo **Agregar nuevo proyecto**, elija **Visual C#** > **Escritorio de Windows** > plantilla de **biblioteca de clases**.

10. Asigne un nombre al proyecto *ContosoAdsCommon* y haga clic en **Aceptar**.

	Este proyecto incluirá el contexto de Entity Framework y el modelo de datos que usarán el front-end y el back-end. Como alternativa, podría definir las clases relacionadas con EF en el proyecto web y hacer referencia a ese proyecto desde el proyecto WebJob. Pero en ese caso, el proyecto WebJob tendría que hacer referencia a ensamblados web que no necesita.

### Incorporación de un proyecto de aplicación de consola con la implementación de WebJobs habilitada

1. Haga clic con el botón secundario en el proyecto web (no en la solución ni en el proyecto de biblioteca de clases) y, a continuación, haga clic en **Agregar** > **Nuevo proyecto WebJob de Azure**.

	![Selección del menú Nuevo proyecto WebJob de Azure](./media/websites-dotnet-webjobs-sdk-get-started/newawjp.png)

2. En el cuadro de diálogo **Agregar WebJob de Azure**, especifique ContosoAdsWebJob como **Nombre de proyecto** y **Nombre de WebJob**. Deje la opción **Modo de ejecución de WebJob** establecida en **Ejecutar continuamente**.

3.  Haga clic en **Aceptar**.

	Visual Studio crea una aplicación de consola que se configura para implementarse como un WebJob cada vez que implemente el proyecto web. Para ello, realiza las siguientes tareas después de crear el proyecto:

	* Agregue un archivo *webjob-publish-settings.json* en la carpeta Propiedades del proyecto WebJob.
	* Agregue un archivo *webjobs-list.json* en la carpeta Propiedades del proyecto web.
	* Instala el paquete NuGet Microsoft.Web.WebJobs.Publish en el proyecto WebJob.

	Para obtener más información acerca de estos cambios, consulte [Implementar trabajos web con Visual Studio](websites-dotnet-deploy-webjobs.md).

### Incorporación de paquetes NuGet

La plantilla new-project para un proyecto de WebJob instala automáticamente el paquete NuGet del SDK de WebJobs [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs) y sus dependencias.

Una de las dependencias de SDK de WebJobs que se instala automáticamente en el proyecto WebJob es la biblioteca de clientes de almacenamiento de Azure (SCL). Sin embargo, deberá agregarla al proyecto web para trabajar con blobs y colas.

1. Abra el cuadro de diálogo **Administrar paquetes de NuGet** correspondiente a la solución.

2. En el panel izquierdo, seleccione **Paquetes instalados**.

3. Busque el paquete *Almacenamiento de Azure* y haga clic en **Administrar**.

4. En el cuadro **Seleccionar proyectos**, active la casilla **ContosoAdsWeb** y, a continuación, haga clic en **Aceptar**.

	Los tres proyectos usan Entity Framework para trabajar con los datos de Base de datos SQL.

5. En el panel izquierdo, seleccione **En línea**.

6. Busque el paquete NuGet de *EntityFramework* e instálelo en los otros tres proyectos.


### Establecimiento de preferencias del proyecto

Tanto los proyectos web como de WebJob funcionan con la base de datos SQL, por lo que ambos hacen referencia al proyecto ContosoAdsCommon.

1. En el proyecto ContosoAdsWeb, establezca una referencia al proyecto ContosoAdsCommon. (Haga clic con el botón secundario en el proyecto ContosoAdsWeb y después en **Agregar** > **Referencia**. En el cuadro de diálogo **Administrador de referencias**, seleccione **Solución** > **Proyectos** > **ContosoAdsCommon** y, a continuación, haga clic en **Aceptar**).

1. En el proyecto ContosoAdsWebJob, establezca una referencia al proyecto ContosAdsCommon.

	El proyecto WebJob necesita referencias para trabajar con imágenes y para tener acceso a las cadenas de conexión.

3. En el proyecto ContosoAdsWebJob, establezca una referencia a `System.Drawing` y a `System.Configuration`.

### Incorporación de archivos de configuración y de código

En este tutorial no se explica cómo [crear controladores y vistas MVC usando scaffolding](http://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started), cómo [escribir código Entity Framework que funcione con bases de datos de SQL Server](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc) ni [los fundamentos de la programación asincrónica en ASP.NET 4.5](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices#async). Por ello, lo único que queda pendiente consiste en copiar archivos de configuración y de código desde la solución descargada a la nueva. Después de realizar este proceso, se muestran y explican las partes clave del código en las siguientes secciones.

Para agregar archivos a un proyecto o carpeta, haga clic con el botón secundario en dicho proyecto o carpeta y después en **Agregar** > **Elemento existente**. Seleccione los archivos que desee y haga clic en **Agregar**. Si se le pregunta si desea reemplazar los archivos existentes, haga clic en **Sí**.

1. En el proyecto ContosoAdsCommon, elimine el archivo *Class1.cs* y agregue en su lugar los siguientes archivos desde el proyecto descargado.

	- *Ad.cs*
	- *ContosoAdscontext.cs*
	- *BlobInformation.cs*<br/><br/>

2. En el proyecto ContosoAdsWeb, agregue los siguientes archivos desde el proyecto descargado.

	- *Web.config*
	- *Global.asax.cs*  
	- En la carpeta *Controladores*: *AdController.cs*
	- En la carpeta *Views\\Shared*: archivo *\_Layout.cshtml*.
	- En la carpeta *Views\\Home*: *Index.cshtml*.
	- En la carpeta *Views\\Ad* (cree primero la carpeta): cinco archivos *.cshtml*<br/><br/>.

3. En el proyecto ContosoAdsWebJob, agregue los siguientes archivos desde el proyecto descargado.

	- *App.config* (cambie el filtro de tipo de archivo a **Todos los archivos**)
	- *Program.cs*
	- *Functions.cs*

Ahora puede generar, ejecutar e implementar la aplicación como se indicó anteriormente en el tutorial. No obstante, antes de hacerlo, detenga el WebJob que esté aún ejecutándose en la primera aplicación web en el que lo implementó. De lo contrario, ese WebJob procesará los mensajes de cola creados localmente o mediante la aplicación que se ejecute en una aplicación web nueva, ya que todos usan la misma cuenta de almacenamiento.

## <a id="code"></a>Revisión del código de la aplicación

En la siguiente sección se explica el código relacionado para trabajar con el SDK de WebJobs y los blobs y las colas de Azure.

> [AZURE.NOTE]Para obtener el código específico del SDK de WebJobs, consulte las secciones [Program.cs y Functions.cs](#programcs).

### ContosoAdsCommon - Ad.cs

El archivo Ad.cs define una enumeración para categorías de anuncios y una clase de entidad POCO para información de anuncios.

		public enum Category
		{
		    Cars,
		    [Display(Name="Real Estate")]
		    RealEstate,
		    [Display(Name = "Free Stuff")]
		    FreeStuff
		}

		public class Ad
		{
		    public int AdId { get; set; }

		    [StringLength(100)]
		    public string Title { get; set; }

		    public int Price { get; set; }

		    [StringLength(1000)]
		    [DataType(DataType.MultilineText)]
		    public string Description { get; set; }

		    [StringLength(1000)]
		    [DisplayName("Full-size Image")]
		    public string ImageURL { get; set; }

		    [StringLength(1000)]
		    [DisplayName("Thumbnail")]
		    public string ThumbnailURL { get; set; }

		    [DataType(DataType.Date)]
		    [DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
		    public DateTime PostedDate { get; set; }

		    public Category? Category { get; set; }
		    [StringLength(12)]
		    public string Phone { get; set; }
		}

### ContosoAdsCommon - ContosoAdsContext.cs

La clase ContosoAdsContext especifica que la clase Ad se usa en una colección DbSet, cuyo Entity Framework se almacena en una base de datos SQL.

		public class ContosoAdsContext : DbContext
		{
		    public ContosoAdsContext() : base("name=ContosoAdsContext")
		    {
		    }
		    public ContosoAdsContext(string connString)
		        : base(connString)
		    {
		    }
		    public System.Data.Entity.DbSet<Ad> Ads { get; set; }
		}

La clase tiene dos constructores. El primero lo usa el proyecto web y especifica el nombre de una cadena de conexión que se almacena en el archivo Web.config o en el entorno en tiempo de ejecución de Azure. El segundo constructor le permite pasar la cadena de conexión real. El proyecto WebJob necesita este elemento porque no tiene ningún archivo Web.config. Anteriormente vio dónde se almacenaba esta cadena de conexión y posteriormente verá cómo el código recupera dicha cadena de conexión cuando crea instancias de la clase DbContext.

### ContosoAdsCommon - BlobInformation.cs

La clase `BlobInformation` se usa para almacenar información de almacenamiento acerca de un blob de imagen en un mensaje de cola.

		public class BlobInformation
		{
		    public Uri BlobUri { get; set; }

		    public string BlobName
		    {
		        get
		        {
		            return BlobUri.Segments[BlobUri.Segments.Length - 1];
		        }
		    }
		    public string BlobNameWithoutExtension
		    {
		        get
		        {
		            return Path.GetFileNameWithoutExtension(BlobName);
		        }
		    }
		    public int AdId { get; set; }
		}


### ContosoAdsWeb - Global.asax.cs

El código llamado desde el método `Application_Start` crea un contenedor de blob *images* y una cola *images* si todavía no existen. Esto garantiza que siempre que comience a usar una cuenta de almacenamiento nueva, la cola y el contenedor del blob necesarios se crea automáticamente.

El código obtiene acceso a la cuenta de almacenamiento mediante la cadena de conexión de almacenamiento del archivo *Web.config* o el entorno en tiempo de ejecución de Azure.

		var storageAccount = CloudStorageAccount.Parse
		    (ConfigurationManager.ConnectionStrings["AzureWebJobsStorage"].ToString());

Después obtiene una referencia al contenedor de blobs *images*, crea el contenedor si todavía no existe y establece permisos de acceso en el nuevo contenedor. De forma predeterminada, los nuevos contenedores solamente permiten a los clientes con credenciales de cuenta de almacenamiento acceder a blobs. La aplicación web necesita que los blobs sean públicos para que pueda mostrar imágenes usando direcciones URL que apunten a los blobs de imagen.

		var blobClient = storageAccount.CreateCloudBlobClient();
		var imagesBlobContainer = blobClient.GetContainerReference("images");
		if (imagesBlobContainer.CreateIfNotExists())
		{
		    imagesBlobContainer.SetPermissions(
		        new BlobContainerPermissions
		        {
		            PublicAccess = BlobContainerPublicAccessType.Blob
		        });
		}

El código similar obtiene una referencia a la cola *blobnamerequest* y crea una nueva cola. En este caso no es necesario cambios de permiso. En la sección [ResolveBlobName](#resolveblobname) que se incluye más adelante en el tutorial se explica por qué la cola que escribe la aplicación web se usa solo para obtener los nombres de blob y no para generar miniaturas.

		CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
		var imagesQueue = queueClient.GetQueueReference("blobnamerequest");
		imagesQueue.CreateIfNotExists();

### ContosoAdsWeb - \_Layout.cshtml

El archivo *\_Layout.cshtml* establece el nombre de aplicación en el encabezado y pie de página y crea una entrada de menú "Ads".

### ContosoAdsWeb - Views\\Home\\Index.cshtml

El archivo *Views\\Home\\Index.cshtml* muestra vínculos de categoría en la página principal. Los vínculos pasan el valor entero de la enumeración `Category` en una variable de cadena de consulta a la página de índice de anuncios.

		<li>@Html.ActionLink("Cars", "Index", "Ad", new { category = (int)Category.Cars }, null)</li>
		<li>@Html.ActionLink("Real estate", "Index", "Ad", new { category = (int)Category.RealEstate }, null)</li>
		<li>@Html.ActionLink("Free stuff", "Index", "Ad", new { category = (int)Category.FreeStuff }, null)</li>
		<li>@Html.ActionLink("All", "Index", "Ad", null, null)</li>

### ContosoAdsWeb - AdController.cs

En el archivo *AdController.cs*, el constructor llama al método `InitializeStorage` para crear objetos de biblioteca de cliente de almacenamiento de Azure que proporcionan una API para trabajar con blobs y colas.

Después, el código obtiene una referencia al contenedor de blobs *images* tal y como vio anteriormente en *Global.asax.cs*. Mientras hace eso, establece una [directiva de reintentos](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/transient-fault-handling) apropiada para una aplicación web. La directiva de reintentos de retroceso exponencial predeterminada podría bloquear la aplicación web durante más de un minuto en reintentos repetitivos para un error transitorio. La directiva de intentos especificada aquí espera 3 segundos después de cada reintento hasta 3 reintentos.

		var blobClient = storageAccount.CreateCloudBlobClient();
		blobClient.DefaultRequestOptions.RetryPolicy = new LinearRetry(TimeSpan.FromSeconds(3), 3);
		imagesBlobContainer = blobClient.GetContainerReference("images");

El código similar obtiene una referencia a la cola *images*.

		CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
		queueClient.DefaultRequestOptions.RetryPolicy = new LinearRetry(TimeSpan.FromSeconds(3), 3);
		imagesQueue = queueClient.GetQueueReference("blobnamerequest");

La mayoría del código del controlador es típico para trabajar con un modelo de datos de usando Entity Framework usando una clase DbContext. Una excepción es el método HttpPost `Create`, que carga un archivo y lo guarda en el almacenamiento de blobs. El enlazador de modelos proporciona un objeto [HttpPostedFileBase](http://msdn.microsoft.com/library/system.web.httppostedfilebase.aspx) al método.

		[HttpPost]
		[ValidateAntiForgeryToken]
		public async Task<ActionResult> Create(
		    [Bind(Include = "Title,Price,Description,Category,Phone")] Ad ad,
		    HttpPostedFileBase imageFile)

Si el usuario seleccionó un archivo para cargar, el código carga el archivo, lo guarda en un blob y actualiza el registro de base de datos Ad con una dirección URL que adjunta al blob.

		if (imageFile != null && imageFile.ContentLength != 0)
		{
		    blob = await UploadAndSaveBlobAsync(imageFile);
		    ad.ImageURL = blob.Uri.ToString();
		}

El código que no se carga se encuentra en el método `UploadAndSaveBlobAsync`. Crea un nombre GUID para el blob, carga y guarda el archivo y devuelve una referencia al blob guardado.

		private async Task<CloudBlockBlob> UploadAndSaveBlobAsync(HttpPostedFileBase imageFile)
		{
		    string blobName = Guid.NewGuid().ToString() + Path.GetExtension(imageFile.FileName);
		    CloudBlockBlob imageBlob = imagesBlobContainer.GetBlockBlobReference(blobName);
		    using (var fileStream = imageFile.InputStream)
		    {
		        await imageBlob.UploadFromStreamAsync(fileStream);
		    }
		    return imageBlob;
		}

Después de que el método HttpPost `Create` carga un blob y actualiza la base de datos, crea un mensaje de cola para informar a ese proceso back-end que una imagen está preparada para su conversión en una miniatura.

		BlobInformation blobInfo = new BlobInformation() { AdId = ad.AdId, BlobUri = new Uri(ad.ImageURL) };
		var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
		await thumbnailRequestQueue.AddMessageAsync(queueMessage);

El código para el método HttpPost `Edit`es similar, con la excepción de que si el usuario selecciona un archivo de imagen nuevo, cualquier blob que ya exista para este anuncio se debe eliminar.

		if (imageFile != null && imageFile.ContentLength != 0)
		{
		    await DeleteAdBlobsAsync(ad);
		    imageBlob = await UploadAndSaveBlobAsync(imageFile);
		    ad.ImageURL = imageBlob.Uri.ToString();
		}

A continuación se muestra el código que elimina blobs cuando elimina un anuncio:

		private async Task DeleteAdBlobsAsync(Ad ad)
		{
		    if (!string.IsNullOrWhiteSpace(ad.ImageURL))
		    {
		        Uri blobUri = new Uri(ad.ImageURL);
		        await DeleteAdBlobAsync(blobUri);
		    }
		    if (!string.IsNullOrWhiteSpace(ad.ThumbnailURL))
		    {
		        Uri blobUri = new Uri(ad.ThumbnailURL);
		        await DeleteAdBlobAsync(blobUri);
		    }
		}
		private static async Task DeleteAdBlobAsync(Uri blobUri)
		{
		    string blobName = blobUri.Segments[blobUri.Segments.Length - 1];
		    CloudBlockBlob blobToDelete = imagesBlobContainer.GetBlockBlobReference(blobName);
		    await blobToDelete.DeleteAsync();
		}

### ContosoAdsWeb - Views\\Ad\\Index.cshtml y Details.cshtml

El archivo *Index.cshtml* muestra miniaturas con otros datos de anuncio:

		<img  src="@Html.Raw(item.ThumbnailURL)" />

El archivo *Details.cshtml* muestra la imagen a tamaño completo:

		<img src="@Html.Raw(Model.ImageURL)" />

### ContosoAdsWeb - Views\\Ad\\Create.cshtml y Edit.cshtml

Los archivos *Create.cshtml* y *Edit.cshtml* especifican codificación de formularios que permite al controlador obtener el objeto `HttpPostedFileBase`.

		@using (Html.BeginForm("Create", "Ad", FormMethod.Post, new { enctype = "multipart/form-data" }))

Un elemento `<input>` indica al explorador que proporcione un cuadro de dialogo de selección.

		<input type="file" name="imageFile" accept="image/*" class="form-control fileupload" />

### <a id="programcs"></a>ContosoAdsWebJob: Program.cs

Cuando se inicia el WebJob, el método `Main` llama al método `JobHost.RunAndBlock` del SDK de WebJobs para comenzar la ejecución de funciones desencadenadas en el subproceso actual.

		static void Main(string[] args)
		{
		    JobHost host = new JobHost();
		    host.RunAndBlock();
		}

### <a id="generatethumbnail"></a>ContosoAdsWebJob - Functions.cs - Método GenerateThumbnail

El SDK de WebJobs llama a este método cuando se recibe un mensaje de cola. El método crea una miniatura y coloca la URL de la miniatura en la base de datos.

		public static void GenerateThumbnail(
		[QueueTrigger("thumbnailrequest")] BlobInformation blobInfo,
		[Blob("images/{BlobName}", FileAccess.Read)] Stream input,
		[Blob("images/{BlobNameWithoutExtension}_thumbnail.jpg")] CloudBlockBlob outputBlob)
		{
		    using (Stream output = outputBlob.OpenWrite())
		    {
		        ConvertImageToThumbnailJPG(input, output);
		        outputBlob.Properties.ContentType = "image/jpeg";
		    }

		    // Entity Framework context class is not thread-safe, so it must
		    // be instantiated and disposed within the function.
		    using (ContosoAdsContext db = new ContosoAdsContext())
		    {
		        var id = blobInfo.AdId;
		        Ad ad = db.Ads.Find(id);
		        if (ad == null)
		        {
		            throw new Exception(String.Format("AdId {0} not found, can't create thumbnail", id.ToString()));
		        }
		        ad.ThumbnailURL = outputBlob.Uri.ToString();
		        db.SaveChanges();
		    }
		}

* El atributo `QueueTrigger` ordena al SDK de WebJobs que llame a este método cuando se recibe un mensaje nuevo en la cola thumbnailrequest.

		[QueueTrigger("thumbnailrequest")] BlobInformation blobInfo,

	El objeto `BlobInformation` del mensaje de cola se deserializa automáticamente en el parámetro `blobInfo`. Cuando el método se completa, el mensaje de cola se elimina. Si se produce un error en el método antes de completarse, el mensaje de cola no se elimina, sino que expira transcurridos 10 minutos y el mensaje se libera para que se pueda recoger y procesarse de nuevo. Esta secuencia no se repite de manera indefinida cuando un mensaje provoca siempre una excepción. Después de cinco intentos erróneos al procesar un mensaje, este se mueve a una cola llamada {nombrecola}-poison. Se puede configurar el número máximo de intentos.

* Los dos atributos `Blob` proporcionan los objetos que están enlazados a los blobs: uno para el blob de imagen existente y otro para un nuevo blob en miniatura que crea el método.

		[Blob("images/{BlobName}", FileAccess.Read)] Stream input,
		[Blob("images/{BlobNameWithoutExtension}_thumbnail.jpg")] CloudBlockBlob outputBlob)

	Los nombres de blob proceden de las propiedades del objeto `BlobInformation` recibido en el mensaje de cola (`BlobName` y `BlobNameWithoutExtension`). Para obtener todas las funcionalidades de la biblioteca del cliente de almacenamiento, use la clase `CloudBlockBlob` para trabajar con blobs. Si desea reutilizar el código que escribió para trabajar con objetos `Stream`, use la clase `Stream`.

Para obtener más información acerca de cómo escribir funciones que utilizan atributos del SDK de WebJobs, consulte los siguientes recursos:

* [Uso del almacenamiento de colas de Azure con el SDK de WebJobs](websites-dotnet-webjobs-sdk-storage-queues-how-to.md)
* [Uso del almacenamiento de blobs de Azure con el SDK de WebJobs](websites-dotnet-webjobs-sdk-storage-blobs-how-to.md)
* [Uso del almacenamiento de tablas de Azure con el SDK de WebJobs](websites-dotnet-webjobs-sdk-storage-tables-how-to.md)
* [Uso del Bus de servicio de Azure con el SDK de trabajos web](websites-dotnet-webjobs-sdk-service-bus.md)

> [AZURE.NOTE]
>
> * Si la aplicación web se ejecuta en varias máquinas virtuales, se ejecutarán varios WebJobs de manera simultánea y, en algunos escenarios, esto puede hacer que los mismos datos se procesen varias veces. Esto no supone un problema cuando se usan los desencadenadores de colas, blobs y bus de servicio integrados. El SDK garantiza que las funciones se procesarán una sola vez para cada mensaje o blob.
>
> * Para obtener información sobre cómo implementar el cierre estable, consulte [Cierre estable](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#graceful).
>
> * El código del método `ConvertImageToThumbnailJPG` (no se muestra) usa clases del espacio de nombres `System.Drawing` por simplicidad. Sin embargo, las clases de este espacio de nombres se diseñaron para usarse con Windows Forms. No se admiten para usarse en un servicio de Windows o ASP.NET. Para obtener más información acerca de las opciones de procesamiento de imagen, consulte [Dynamic Image Generation](http://www.hanselman.com/blog/BackToBasicsDynamicImageGenerationASPNETControllersRoutingIHttpHandlersAndRunAllManagedModulesForAllRequests.aspx) (Generación de imagen dinámica) y [Deep Inside Image Resizing](http://www.hanselminutes.com/313/deep-inside-image-resizing-and-scaling-with-aspnet-and-iis-with-imageresizingnet-author-na) (Cambio de tamaño de imagen profunda).

## Pasos siguientes

En este tutorial, ha visto una aplicación sencilla de niveles múltiples que usa el SDK de WebJobs para el procesamiento de back-end. En esta sección se ofrecen algunas sugerencias para obtener más información acerca de las aplicaciones de múltiples niveles de ASP.NET y WebJobs.

### Características que faltan

La aplicación se ha conservado simple para un tutorial de introducción. En una aplicación real, implementaría la [inserción de dependencias](http://www.asp.net/mvc/tutorials/hands-on-labs/aspnet-mvc-4-dependency-injection), el [repositorio y la unidad de patrones de trabajo](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/advanced-entity-framework-scenarios-for-an-mvc-web-application#repo), y usaría [una interfaz de registro](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry#log), [migraciones de EF Code First](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application) para administrar los cambios de modelos de datos y la [resistencia de conexiones EF](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application) para administrar los errores de red transitorios.

### Escalado de WebJobs

WebJobs se ejecutan en el contexto de una aplicación web y no se pueden escalar por separado. Por ejemplo, si tiene una instancia de aplicación web estándar, solo tiene una instancia del proceso en segundo plano en ejecución. Esta instancia usa algunos de los recursos del servidor (CPU, memoria, etc.) que, de lo contrario, estarían disponibles para proporcionar contenido web.

Si el tráfico varía en función de la hora o el día de la semana, y si el procesamiento de back-end que deba realizar puede esperar, es posible programar los WebJobs para que se ejecuten en horas de menos tráfico. Si la carga sigue siendo demasiado alta para esa solución, puede ejecutar el back-end como un WebJob en una aplicación web independiente dedicada para ese propósito. A continuación, puede escalar la aplicación web back-end con independencia de la aplicación web front-end.

Para obtener más información, consulte [Escalado de Webjobs](websites-webjobs-resources.md#scale).

### Evitar tiempos de inactividad por tiempo de espera agotado en aplicaciones web

Para asegurarse de que sus WebJobs siempre estén siempre en ejecución en todas las instancias de la aplicación web, debe habilitar la característica [AlwaysOn](http://weblogs.asp.net/scottgu/archive/2014/01/16/windows-azure-staging-publishing-support-for-web-sites-monitoring-improvements-hyper-v-recovery-manager-ga-and-pci-compliance.aspx).

### Uso del SDK de WebJobs fuera de WebJobs

No es necesario que un programa que use el SDK de WebJobs se ejecute en Azure en un WebJob. Se puede ejecutar localmente, y también se puede ejecutar en otros entornos, como un rol de trabajador del servicio en la nube o un servicio de Windows. No obstante, solo puede tener acceso al panel del SDK de WebJobs a través de una aplicación web de Azure. Para usar el panel, debe conectar la aplicación web a la cuenta de almacenamiento que esté usando. Para ello, establezca la cadena de conexión AzureWebJobsDashboard en la pestaña **Configurar** del Portal clásico. A continuación, puede obtener acceso al panel utilizando la dirección URL siguiente:

https://{webappname}.scm.azurewebsites.net/azurejobs/#/functions

Para obtener más información, consulte [Obtención de un panel para desarrollo local con el SDK de WebJobs](http://blogs.msdn.com/b/jmstall/archive/2014/01/27/getting-a-dashboard-for-local-development-with-the-webjobs-sdk.aspx), pero tenga en cuenta que muestra un nombre de cadena de conexión antiguo.

### Más documentación sobre WebJobs

Para obtener más información, consulte [Recursos de documentación de WebJobs de Azure](http://go.microsoft.com/fwlink/?LinkId=390226).

<!-----HONumber=AcomDC_1217_2015-->