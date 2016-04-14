<properties
	pageTitle="Introducción a Servicios móviles de Azure para aplicaciones de iOS | Back-end de JavaScript"
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
	ms.topic="hero-article"
	ms.date="02/10/2016"
	ms.author="krisragh"/>

# <a name="getting-started"> </a>Introducción a Servicios móviles

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]

&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> Para ver la versión equivalente de este tema en Aplicaciones móviles, consulte [Creación de una aplicación iOS](../app-service-mobile/app-service-mobile-android-get-started.md).

En este tutorial se muestra cómo agregar un servicio back-end basado en la nube a una aplicación de iOS con los Servicios móviles de Azure.

Con este tutorial creará tanto un servicio móvil nuevo como una aplicación simple de _Lista de pendientes_ que almacena datos de la aplicación en el servicio móvil nuevo. El servicio móvil que se creará utiliza JavaScript para la lógica de negocios de servidor. Para crear un servicio móvil con lógica empresarial del lado de servidor en .NET, vea la [versión back-end de .NET] de este tema.

> [AZURE.NOTE] Para completar este tutorial, deberá tener una cuenta de Azure. Si no dispone de ninguna cuenta, puede registrarse para obtener una versión de evaluación de Azure y acceder a [servicios móviles gratuitos que puede seguir usando incluso después de que finalice dicha evaluación](https://azure.microsoft.com/pricing/details/mobile-services/). Para obtener más información, consulte [Evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fes-ES%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-ios%2F%20).

## <a name="create-new-service"> </a>Creación de un servicio móvil

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## Creación de una nueva aplicación iOS

Puede seguir un sencillo inicio rápido en el Portal de Azure clásico para crear una aplicación conectada al servicio móvil:

1. En el [Portal de Azure clásico], haga clic en **Servicios móviles** y luego en el servicio móvil que acaba de crear.

2. En la pestaña Inicio rápido, haga clic en **iOS** en **Elija una plataforma ** y expanda **Crear una nueva aplicación iOS**. Con esto se muestran los pasos requeridos para crear una aplicación iOS conectada a su servicio móvil.

3. Haga clic en **Crear tabla TodoItem** para crear una tabla para almacenar los datos de la aplicación.

4. En **Descargar y ejecutar la aplicación**, haga clic en **Descargar**. De este modo se descarga el proyecto para la aplicación de _lista de pendientes_ de muestra que está conectada al servicio móvil, además del SDK de iOS para Servicios móviles. Guarde el archivo comprimido del proyecto en el equipo local y anote dónde lo guardó.

## Ejecución de la nueva aplicación de iOS

[AZURE.INCLUDE [mobile-services-ios-run-app](../../includes/mobile-services-ios-run-app.md)]

<ol start="4"> <li><p>De nuevo en el [Portal de Azure clásico], haga clic en la pestaña **Datos** y luego en la tabla **TodoItem**. Esto le permite examinar los datos que la aplicación inserta en la tabla.<p></li></ol></p>

## <a name="next-steps"> </a>Pasos siguientes
Aprenda a realizar tareas adicionales importantes en Servicios móviles:

* [Introducción a la sincronización de datos sin conexión] <br/>Aprenda a usar la sincronización de datos sin conexión para mejorar la capacidad de respuesta y reforzar la solidez de su aplicación.

* [Incorporación de autenticación a una aplicación existente] <br/>Aprenda a autenticar a los usuarios de su aplicación con un proveedor de identidades.

* [Incorporación de notificaciones push a una aplicación existente] <br/>Aprenda a enviar una notificación push muy básica a la aplicación.

[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]


<!-- Anchors. -->
[Getting started with Mobile Services]: #getting-started
[Create a new mobile service]: #create-new-service
[Define the mobile service instance]: #define-mobile-service-instance
[Next Steps]: #next-steps

<!-- Images. -->
[6]: ./media/mobile-services-ios-get-started/mobile-portal-quickstart-ios.png
[7]: ./media/mobile-services-ios-get-started/mobile-quickstart-steps-ios.png
[8]: ./media/mobile-services-ios-get-started/mobile-xcode-project.png

[10]: ./media/mobile-services-ios-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/mobile-services-ios-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-ios-get-started/mobile-data-browse.png


<!-- URLs. -->
[Introducción a la sincronización de datos sin conexión]: mobile-services-ios-get-started-offline-data.md
[Incorporación de autenticación a una aplicación existente]: mobile-services-dotnet-backend-ios-get-started-users.md
[Incorporación de notificaciones push a una aplicación existente]: mobile-services-dotnet-backend-ios-get-started-push.md


[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Portal de Azure clásico]: https://manage.windowsazure.com/
[XCode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[versión back-end de .NET]: mobile-services-dotnet-backend-ios-get-started.md

<!---HONumber=AcomDC_0309_2016-->