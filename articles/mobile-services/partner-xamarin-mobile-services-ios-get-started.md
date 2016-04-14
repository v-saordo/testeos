<properties
	pageTitle="Introducción a Servicios móviles para aplicaciones de Xamarin iOS | Microsoft Azure"
	description="Siga este tutorial para empezar a usar Servicios móviles de Azure para el desarrollo de Xamarin iOS."
	services="mobile-services"
	documentationCenter="xamarin"
	authors="conceptdev"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-ios"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="02/10/2016"
	ms.author="craig.dunn@xamarin.com"/>

# <a name="getting-started"> </a>Introducción a Servicios móviles

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]&nbsp;

[AZURE.INCLUDE [mobile-services-hero-slug](../../includes/mobile-services-hero-slug.md)]

En este tutorial se muestra cómo agregar un servicio back-end basado en la nube a una aplicación Xamarin.iOS mediante Servicios móviles de Azure. Con este tutorial creará tanto un servicio móvil nuevo como una aplicación simple de *Lista de pendientes* que almacena datos de la aplicación en el servicio móvil nuevo.

Si prefiere ver un vídeo, el clip que aparece a continuación muestra los mismos pasos que este tutorial.

Vídeo: "Getting Started with Xamarin and Azure Mobile Services" (Introducción a Xamarin con Servicios móviles de Azure) con Craig Dunn, desarrollador evangelista de Xamarin (duración: 10:05 min)

> [AZURE.VIDEO getting-started-with-xamarin-and-mobile-services]

La siguiente captura de pantalla muestra la aplicación final:

![][0]

Para completar este tutorial es necesario XCode y [Xamarin Studio] para OS X o el complemento Xamarin Visual Studio para Visual Studio en Windows. El ejemplo se ejecutará en iOS 5.0 y más reciente.

> [AZURE.IMPORTANT] Para completar este tutorial, deberá tener una cuenta de Azure. Si no dispone de ninguna cuenta, puede registrarse para obtener una versión de evaluación de Azure y conseguir hasta 10 servicios móviles gratuitos que podrá seguir usando incluso después de que finalice la evaluación. Para obtener más información, consulte [Evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-new-service"> </a>Creación de un servicio móvil

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## Creación de una aplicación Xamarin.iOS

Después de crear el servicio móvil, puede seguir una sencilla introducción rápida en el Portal de Azure clásico para crear una aplicación o modificar una ya existente a fin de conectarla a dicho servicio.

En esta sección se creará una nueva aplicación Xamarin.iOS que se conecta al servicio móvil.

1.  En el [Portal de Azure clásico], haga clic en **Servicios móviles** y luego en el servicio móvil que acaba de crear.

2. En la pestaña de inicio rápido, haga clic en **Xamarin.iOS** en **Seleccionar plataforma** y expanda **Crear una nueva aplicación Xamarin.iOS**.

	![][6]

	Con esto se muestran los tres sencillos pasos requeridos para crear una aplicación Xamarin.iOS conectada al servicio móvil.

  	![][7]

3. Si todavía no lo ha hecho, descargue e instale Xcode (recomendamos que sea la última versión, Xcode 6.0, o una más reciente) y [Xamarin Studio].

4. Haga clic en **Crear tabla TodoItems** para crear una tabla donde almacenar los datos de la aplicación.

5. En **Descargar y ejecutar la aplicación**, haga clic en **Descargar**.

	De este modo se descarga el proyecto para la aplicación de _lista de pendientes_ de muestra que está conectada al servicio móvil y que hace referencia al componente de Servicios móviles de Azure para Xamarin.iOS. Guarde el archivo comprimido del proyecto en el equipo local y anote dónde lo guardó.

## Ejecución de la nueva aplicación Xamarin.iOS

La etapa final de este tutorial consiste en crear y ejecutar la aplicación nueva.

1. Vaya a la ubicación donde guardó los archivos comprimidos del proyecto, expándalos en su equipo y abra el archivo de la solución **XamarinTodoQuickStart.iOS.sln** con Xamarin Studio o Visual Studio.

	![][8]

	![][9]

2. Presione el botón **Ejecutar** para crear el proyecto e iniciar la aplicación en el emulador de iPhone, que es el valor predeterminado para este proyecto.

3. En la aplicación, escriba un texto significativo, como _Realice el tutorial_. A continuación, haga clic en el icono de suma (**+**).

	![][10]

	Esta acción envía una solicitud POST al nuevo servicio móvil hospedado en Azure. Los datos de la solicitud se insertan en la tabla TodoItem. El servicio móvil devuelve los elementos almacenados en la tabla y los datos se muestran en la lista.

	> [AZURE.NOTE] Puede revisar el código de acceso al servicio móvil para consultar e insertar datos; este se encuentra en el archivo de C# TodoService.cs.

4. De nuevo en el [Portal de Azure clásico], haga clic en la pestaña **Datos** y luego en la tabla **TodoItems**.

	![][11]

	Esto le permite examinar los datos que la aplicación inserta en la tabla.

	![][12]


## Pasos siguientes
Ahora que completó el inicio rápido, aprenda a realizar importantes tareas adicionales en los Servicios móviles:

* [Introducción a la sincronización de datos sin conexión] Obtenga información sobre cómo el inicio rápido usa la sincronización de datos sin conexión para mejorar la capacidad de respuesta y reforzar la solidez de la aplicación.

* [Introducción a la autenticación] Aprenda a autenticar a los usuarios de su aplicación con un proveedor de identidades.

* [Introducción a las notificaciones push] Aprenda a enviar una notificación push muy básica a la aplicación.




[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- Anchors. -->
[Getting started with Mobile Services]: #getting-started
[Create a new mobile service]: #create-new-service
[Define the mobile service instance]: #define-mobile-service-instance
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-completed-ios.png
[6]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-portal-quickstart-xamarin-ios.png
[7]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-steps-xamarin-ios.png
[8]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-xamarin-project-ios-xs.png
[9]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-xamarin-project-ios-vs.png
[10]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-data-tab.png
[12]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-data-browse.png


<!-- URLs. -->
[Introducción a la sincronización de datos sin conexión]: mobile-services-xamarin-ios-get-started-offline-data.md
[Introducción a la autenticación]: partner-xamarin-mobile-services-ios-get-started-users.md
[Introducción a las notificaciones push]: partner-xamarin-mobile-services-ios-get-started-push.md

[Xamarin Studio]: http://xamarin.com/download
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533

[Portal de Azure clásico]: https://manage.windowsazure.com/

<!---HONumber=AcomDC_0211_2016-->