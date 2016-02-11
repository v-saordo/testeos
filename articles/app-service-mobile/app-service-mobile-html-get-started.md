<properties
	pageTitle="Introducción a los back-ends de aplicación móvil para las aplicaciones HTML/JavaScript | Aplicaciones móviles del Servicio de aplicaciones de Azure"
	description="Siga este tutorial para aprender a usar back-ends de aplicación móvil de Azure para el desarrollo de la aplicación web en HTML5 y JavaScript."
	services="app-service\mobile"
	documentationCenter=""
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-html5"
	ms.devlang="javascript"
	ms.topic="get-started-article"
	ms.date="11/18/2015"
	ms.author="glenga"/>


#Creación de una aplicación HTML

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)] 
&nbsp;  
<!--- [AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]-->

>[AZURE.IMPORTANT] Este tema no está admitido actualmente por Aplicaciones móviles porque se quitó temporalmente el inicio rápido para las aplicaciones HTML/JavaScript en el Portal de Azure. Tenemos previsto ponerlo próximamente. Gracias por su paciencia.

##Información general

En este tutorial se muestra cómo agregar un servicio back-end basado en la nube a una aplicación web HTML5/JavaScript. Para obtener más información, consulte [¿Qué son las aplicaciones móviles?](app-service-mobile-value-prop.md)

La siguiente captura de pantalla muestra la aplicación final:

![Captura de pantalla de la aplicación final](./media/app-service-mobile-html-get-started/mobile-quickstart-completed-html.png)

Completar este tutorial es un requisito previo para todos los tutoriales de aplicaciones móviles para aplicaciones HTML.

##Requisitos previos

Para completar este tutorial, necesitará lo siguiente:

* Una cuenta de Azure activa. Si no dispone de ninguna cuenta, puede registrarse para obtener una versión de evaluación de Azure y conseguir hasta 10 aplicaciones móviles gratuitas que podrá seguir usando incluso después de que finalice la evaluación. Para obtener más información, consulte [Evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).

* [Visual Studio Community 2013] o versión posterior.

>[AZURE.NOTE] Si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de registrarse para obtener una cuenta de Azure, vaya a [Prueba del Servicio de aplicaciones](https://tryappservice.azure.com/?appServiceName=mobile), donde podrá crear inmediatamente una aplicación móvil de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

##Creación de un nuevo back-end de aplicación móvil

Siga estos pasos para crear un nuevo back-end de aplicación móvil.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

Ahora ha aprovisionado un back-end de aplicación móvil de Azure que puede usarse por las aplicaciones del cliente móvil. Después, descargará un proyecto de servidor para un back-end de "lista de tareas" sencillo y lo publicará en Azure.

## Descarga del proyecto de servidor

1. En el [Portal de Azure], haga clic en **Examinar todo** > **Aplicaciones web** y, luego, haga clic en el back-end de aplicación móvil que acaba de crear. 

2. En el back-end de aplicación móvil, haga clic en **Todas las configuraciones** y, en **Aplicación móvil**, haga clic en **Inicio rápido** > **HTML/JavaScript**.

3. En **Descargar y ejecutar el proyecto de servidor**, dentro de **Crear una nueva aplicación**, haga clic en **Descargar**, extraiga los archivos de proyecto comprimidos en el equipo local y abra la solución en Visual Studio.

4. Compile el proyecto para restaurar los paquetes de NuGet.

##Activación de CORS en el proyecto de servidor

El uso compartido de recursos entre orígenes (CORS) es una manera de que la aplicación basada en la web indique desde qué dominios las solicitudes están seguras para que el explorador las permita. Debe agregar una entrada de CORS para cada sitio web que vaya a tener acceso a su back-end de aplicación móvil. La configuración de CORS se ajusta con los comportamientos estándar de API web de ASP.NET. Para obtener más información, consulte [Habilitación de solicitudes entre orígenes en API web de ASP.NET](http://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api#enable-cors).

De forma predeterminada, el proyecto de inicio rápido de cliente que descargará desde el portal se ejecuta en localhost en el puerto 8000. Por este motivo, la próxima vez, habilitará CORS para `http://localhost:8000` en el proyecto de servidor.

1. En el menú Herramientas de Visual Studio, haga clic en **Administrador de paquetes de NuGet** > **Consola del administrador de paquetes**, seleccione Nuget.org como el **origen del paquete** y ejecute el comando siguiente en la ventana de la consola:
 
		Install-Package Microsoft.AspNet.WebApi.Cors  

2. Abra el archivo de proyecto App\_Start/Startup.MobileApp.cs y agregue la siguiente instrucción using:

		using System.Web.Http.Cors;

3. Luego, agregue el código siguiente al método **Startup.ConfigureMobileApp** después de que se cree **HttpConfiguration** (*config*):

        // Enable CORS support for localhost port 8000, all headers and methods.
        var cors = new EnableCorsAttribute("http://localhost:8000", "*", "*");
        config.EnableCors(cors);

4. Guarde las actualizaciones.

Después, implementará el proyecto habilitado para CORS en Azure.

##Publicación del proyecto de servidor en Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service](../../includes/app-service-mobile-dotnet-backend-publish-service.md)]

##Descarga y ejecución del proyecto de cliente

1. De nuevo en la hoja del back-end de aplicación móvil, haga clic en **Todas las configuraciones** y, en **Aplicación móvil**, haga clic en **Inicio rápido** > **HTML/JavaScript**. 

2.  En **Descargar y ejecutar el proyecto de HTML/Javascript**, dentro de **Crear una nueva aplicación**, haga clic en **Descargar** y guarde los archivos de proyecto comprimidos en el equipo local.

3. Busque la ubicación donde guardó los archivos comprimidos del proyecto, expanda los archivos en su equipo e inicie uno de los archivos de comandos siguientes desde la subcarpeta **server**.

	+ **launch-windows** (equipos con Windows)
	+ **launch-mac.command** (equipos con Mac OS X)
	+ **launch-linux.sh** (equipos con Linux)

	> [AZURE.NOTE] En un equipo con Windows, escriba `R` cuando PowerShell le pida que confirme que desea ejecutar el script. Su explorador web podría advertirle de no ejecutar el script porque se ha descargado de Internet. Cuando esto ocurra, debe solicitar que el explorador continúe con la carga del script.

	De este modo se inicia un servidor web en su equipo local para hospedar la nueva aplicación.

4. Abra la URL <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a> en un explorador web para iniciar la aplicación. La aplicación cliente está configurada para conectarse al back-end de la aplicación móvil en Azure.

5. En la aplicación, escriba un texto significativo, como _Realice el tutorial_, en **Introducir nueva tarea** y, a continuación, haga clic en **Agregar**.

   	![Ejecución de la aplicación](./media/app-service-mobile-html-get-started/mobile-quickstart-startup-html.png)

   	Esta acción envía una solicitud POST al nuevo back-end de aplicación móvil hospedado en Azure. Los datos de la solicitud se insertan en la tabla TodoItem del esquema de la aplicación móvil. El servicio devuelve los elementos almacenados en la tabla y se muestran los datos en la segunda columna de la aplicación.

	> [AZURE.TIP] Puede revisar el código de acceso al servicio móvil para consultar e insertar datos; este se encuentra en el archivo app.js.

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs. -->
[Get started with authentication]: app-service-mobile-windows-store-dotnet-get-started-users.md
[Mobile App SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Portal de Azure]: https://portal.azure.com/

[Visual Studio Community 2013]: https://www.visualstudio.com/downloads
 

<!----HONumber=AcomDC_0128_2016-->