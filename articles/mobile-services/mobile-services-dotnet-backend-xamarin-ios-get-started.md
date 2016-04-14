<properties
	pageTitle="Introducción a Servicios móviles para aplicaciones de Xamarin iOS | Microsoft Azure"
	description="Siga este tutorial para empezar a usar Servicios móviles de Azure para el desarrollo de Xamarin iOS"
	services="mobile-services"
	documentationCenter="xamarin"
	authors="lindydonna"
	manager="dwrede"
	editor="mollybos"/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-ios"
	ms.devlang="dotnet"
	ms.topic="get-started-article"
	ms.date="02/10/2016"
	ms.author="donnam"/>

# <a name="getting-started"> </a>Introducción a Servicios móviles

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]
&nbsp;

>[AZURE.TIP] Si no está familiarizado con el desarrollo para dispositivos móviles con Microsoft Azure, [empiece a trabajar con Aplicaciones móviles de Azure](app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview.md) en lugar de Servicios móviles de Azure; Aplicaciones móviles le ofrece [ventajas adicionales](app-service-mobile-value-prop-migration-from-mobile-services-preview.md).

En este tutorial se muestra cómo agregar un servicio back-end basado en la nube a una aplicación Xamarin iOS usando Servicios móviles de Azure. Con este tutorial creará tanto un servicio móvil nuevo como una aplicación simple de _Lista de pendientes_ que almacena datos de la aplicación en el servicio móvil nuevo. El servicio móvil que cree utilizará los lenguajes .NET compatibles y recurrirá a Visual Studio para la lógica de negocios de servidor y para las tareas de administración. Si desea crear un servicio móvil que le permita escribir su lógica de negocios de servidor en JavaScript, consulte la [versión back-end de JavaScript] de este tema.

>[AZURE.NOTE]En este tema se muestra cómo crear un proyecto de servicio móvil desde el Portal de Azure clásico. Si usa Visual Studio 2013 con actualización 2, también puede agregar un proyecto de servicio móvil nuevo a una solución existente de Visual Studio. Para obtener más información, consulte [Inicio rápido: Incorporación de un servicio móvil (back-end de .NET).](http://msdn.microsoft.com/library/windows/apps/dn629482.aspx)

La siguiente captura de pantalla muestra la aplicación final:

![][0]


Completar este tutorial es un requisito previo para todos los tutoriales de Servicios móviles para aplicaciones Xamarin iOS.

>[AZURE.NOTE]Para completar este tutorial, deberá tener una cuenta de Azure. Si no dispone de ninguna cuenta, puede registrarse para obtener una versión de evaluación de Azure y conseguir hasta 10 servicios móviles gratuitos que podrá seguir usando incluso después de que finalice la evaluación. Para obtener más información, consulte <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fes-ES%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-xamarin-ios-get-started" target="_blank">Evaluación gratuita de Azure</a>. <br />Este tutorial requiere <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>. Hay disponible una versión de prueba gratuita.

## Creación de un servicio móvil

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../../includes/mobile-services-dotnet-backend-create-new-service.md)]

## Creación de una nueva aplicación Xamarin iOS

Después de crear el servicio móvil, puede seguir una sencilla introducción rápida en el Portal de Azure clásico para crear una aplicación o modificar una ya existente a fin de conectarla a dicho servicio.

En esta sección va a descargar una nueva aplicación Xamarin iOS y un proyecto de servicio para su servicio móvil.

1. En el [Portal de Azure clásico], haga clic en **Servicios móviles** y luego en el servicio móvil que acaba de crear.

2. En la pestaña de inicio rápido, haga clic en **Xamarin** en **Seleccionar plataforma** y, a continuación, expanda **Crear una nueva aplicación Xamarin**.

   	![][6]

   	Con esto se muestran los tres sencillos pasos necesarios para crear una aplicación Xamarin iOS conectada al servicio móvil.

  	![][7]

3. Si todavía no lo ha hecho, descargue e instale <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a> en el equipo local o máquina virtual.

4. Descargue e instale [Xcode] v4.4 o una versión posterior y [Xamarin Studio]. También puede usar Xamarin para Visual Studio.

5. En **Descargar y publicar el servicio en la nube**, seleccione **iOS** y haga clic en **Descargar**.

  	De esta forma, se descarga una solución que contiene proyectos tanto para el servicio móvil como para la aplicación _To do list_ de ejemplo conectada a su servicio móvil. Guarde el archivo comprimido del proyecto en el equipo local y anote dónde lo guardó.

6. Descargue su perfil de publicación, guarde el archivo descargado en el equipo local y tome nota de dónde lo guarda.

## Prueba del servicio móvil

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service](../../includes/mobile-services-dotnet-backend-test-local-service.md)]

## Publicación del servicio móvil

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../../includes/mobile-services-dotnet-backend-publish-service.md)]

## Ejecución de la aplicación Xamarin iOS

La etapa final de este tutorial consiste en crear y ejecutar la aplicación nueva.

1. Navegue al proyecto de cliente en la solución del servicio móvil, en Visual Studio o en Xamarin Studio.

	![][8]

	![][9]

2. Presione el botón **Ejecutar** para compilar el proyecto e iniciar la aplicación en el emulador de iPhone.

3. En la aplicación, escriba un texto significativo, como _Realice el tutorial_. A continuación, haga clic en el icono de suma (**+**).

	![][10]

	Esta acción envía una solicitud POST al nuevo servicio móvil hospedado en Azure. Los datos de la solicitud se insertan en la tabla TodoItem. El servicio móvil devuelve los elementos almacenados en la tabla y los datos se muestran en la lista.

>[AZURE.NOTE]Puede revisar el código que obtiene acceso al servicio móvil para consultar e insertar datos en el archivo de C# QSTodoService.cs.


## Pasos siguientes
Ahora que completó el inicio rápido, aprenda a realizar importantes tareas adicionales en los Servicios móviles:

* [Introducción a la sincronización de datos sin conexión] <br/>Obtenga información sobre cómo el inicio rápido usa la sincronización de datos sin conexión para mejorar la capacidad de respuesta y reforzar la solidez de la aplicación.

* [Introducción a la autenticación] <br/>Aprenda a autenticar a los usuarios de su aplicación con un proveedor de identidades.

* [Introducción a las notificaciones de inserción] <br/>Aprenda a enviar una notificación de inserción muy básica a la aplicación.

* [Solución de problemas de un back-end de .NET de Servicios móviles] <br/> Aprenda a diagnosticar y corregir los problemas que pueden surgir con un back-end de .NET de Servicios móviles.

[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- Anchors. -->
[Getting started with Mobile Services]: #getting-started
[Create a new mobile service]: #create-new-service
[Next Steps]: #next-steps



<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-quickstart-completed-ios.png
[6]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-portal-quickstart-xamarin-ios.png
[7]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-quickstart-steps-xamarin-ios.png
[8]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-xamarin-project-ios-vs.png
[9]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-xamarin-project-ios-xs.png
[10]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-quickstart-startup-ios.png

<!-- URLs. -->
[Introducción a la sincronización de datos sin conexión]: mobile-services-xamarin-ios-get-started-offline-data.md
[Introducción a la autenticación]: mobile-services-dotnet-backend-xamarin-ios-get-started-users.md
[Introducción a las notificaciones de inserción]: mobile-services-dotnet-backend-xamarin-ios-get-started-push.md
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[JavaScript and HTML]: mobile-services-win8-javascript/
[Portal de Azure clásico]: https://manage.windowsazure.com/
[versión back-end de JavaScript]: mobile-services-ios-get-started.md
[Solución de problemas de un back-end de .NET de Servicios móviles]: mobile-services-dotnet-backend-how-to-troubleshoot.md


[Xamarin Studio]: http://xamarin.com/download
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532&clcid=0x409
[Xamarin for Windows]: https://go.microsoft.com/fwLink/?LinkID=330242&clcid=0x409

<!----HONumber=AcomDC_0211_2016-->