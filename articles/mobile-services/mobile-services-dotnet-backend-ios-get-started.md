<properties
	pageTitle="Introducción a Servicios móviles de Azure para aplicaciones de iOS"
	description="Siga este tutorial para empezar a usar Servicios móviles de Azure para el desarrollo de iOS."
	services="mobile-services"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="02/07/2016"
	ms.author="krisragh"/>

# <a name="getting-started"> </a>Introducción a Servicios móviles

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]&nbsp;

>[AZURE.TIP] Si no está familiarizado con el desarrollo para dispositivos móviles con Microsoft Azure, [empiece a trabajar con Aplicaciones móviles de Azure](../app-service-mobile/app-service-mobile-dotnet-backend-ios-get-started-preview.md) en lugar de Servicios móviles de Azure; esto le dará [ventajas adicionales](../app-service-mobile/app-service-mobile-value-prop-migration-from-mobile-services.md).

En este tutorial se muestra cómo agregar un servicio back-end basado en la nube a una aplicación de iOS con los Servicios móviles de Azure. Con este tutorial creará tanto un servicio móvil nuevo como una aplicación simple de _Lista de pendientes_ que almacena datos de la aplicación en el servicio móvil nuevo. El servicio móvil usa .NET y Visual Studio para la lógica empresarial del lado de servidor. Para crear un servicio móvil con lógica empresarial del lado de servidor en JavaScript, consulte la [versión back-end de JavaScript] de este tema.

> [AZURE.NOTE] Para completar este tutorial, deberá tener una cuenta de Azure. Si no dispone de ninguna cuenta, puede registrarse para obtener una versión de evaluación de Azure y acceder a [servicios móviles gratuitos que puede seguir usando incluso después de que finalice dicha evaluación](https://azure.microsoft.com/pricing/details/mobile-services/). Para obtener más información, consulte [Evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-ios-get-started%2F).

## <a name="create-new-service"> </a>Creación de un servicio móvil

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../../includes/mobile-services-dotnet-backend-create-new-service.md)]

## Descarga del servicio móvil y la aplicación en el equipo local

Ahora que ha creado el servicio móvil, descargue proyectos que puede ejecutar de manera local.

1. Haga clic en el servicio móvil que acaba de crear; luego, en la pestaña Inicio rápido, haga clic en **iOS**, en **Seleccionar plataforma** y expanda **Crear una nueva aplicación iOS**.

2. En su equipo Windows, haga clic en **Descargar** en **Descargar y publicar su servicio en la nube**. De este modo, se descarga el proyecto de Visual Studio que implementa el servicio móvil. Guarde el archivo comprimido del proyecto en el equipo local y anote dónde lo guardó.

3. En su Mac, haga clic en **Descargar** en **Descargar y ejecutar la aplicación**. De este modo se descarga el proyecto para la aplicación de _lista de pendientes_ de muestra que está conectada al servicio móvil, además del SDK de iOS para Servicios móviles. Guarde el archivo comprimido del proyecto en el equipo local y anote dónde lo guardó.

## Prueba del servicio móvil

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service](../../includes/mobile-services-dotnet-backend-test-local-service.md)]

## Publicación del servicio móvil

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../../includes/mobile-services-dotnet-backend-publish-service.md)]


## Ejecución de la nueva aplicación de iOS

[AZURE.INCLUDE [mobile-services-ios-run-app](../../includes/mobile-services-ios-run-app.md)]


## <a name="next-steps"> </a>Pasos siguientes

Con esto se muestra cómo ejecutar la nueva aplicación cliente contra el servicio móvil que se ejecuta en Azure. Antes de que pueda probar la aplicación iOS con el servicio móvil en ejecución en un equipo local, debe configurar el firewall y el servidor web para permitir el acceso desde su equipo de desarrollo iOS. Para obtener más información, consulte [Configuración del servidor web local para permitir conexiones a un servicio móvil local](mobile-services-dotnet-backend-how-to-configure-iis-express.md).

Aprenda a realizar tareas adicionales importantes en Servicios móviles:

* [Introducción a la sincronización de datos sin conexión] <br/>Aprenda a usar la sincronización de datos sin conexión para mejorar la capacidad de respuesta y reforzar la solidez de su aplicación.

* [Incorporación de autenticación a una aplicación existente] <br/>Aprenda a autenticar a los usuarios de su aplicación con un proveedor de identidades.

* [Incorporación de notificaciones push a una aplicación existente] <br/>Obtenga información sobre cómo enviar una notificación push muy básica a la aplicación.

* [Solución de problemas de un back-end de .NET de Servicios móviles] <br/>Obtenga información sobre cómo diagnosticar y corregir los problemas que pueden surgir con un back-end de .NET de Servicios móviles.

[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- Anchors. -->
[Getting started with Mobile Services]: #getting-started
[Create a new mobile service]: #create-new-service
[Define the mobile service instance]: #define-mobile-service-instance
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-completed-ios.png
[1]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-steps-vs.png

[6]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-portal-quickstart-ios.png
[7]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-steps-ios.png
[8]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-xcode-project.png

[10]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-data-browse.png


<!-- URLs. -->
[Introducción a la sincronización de datos sin conexión]: mobile-services-ios-get-started-offline-data.md
[Incorporación de autenticación a una aplicación existente]: mobile-services-dotnet-backend-ios-get-started-users.md
[Incorporación de notificaciones push a una aplicación existente]: mobile-services-dotnet-backend-ios-get-started-push.md
[Solución de problemas de un back-end de .NET de Servicios móviles]: mobile-services-dotnet-backend-how-to-troubleshoot.md
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[XCode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[versión back-end de JavaScript]: mobile-services-ios-get-started.md

<!----HONumber=AcomDC_0211_2016-->