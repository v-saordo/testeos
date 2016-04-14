<properties
	pageTitle="Introducción a Servicios móviles para aplicaciones de Android de Xamarin | Microsoft Azure"
	description="Siga este tutorial para empezar a usar Servicios móviles de Azure para el desarrollo de Xamarin Android."
	services="mobile-services"
	documentationCenter="xamarin"
	authors="lindydonna"
	manager="dwrede"
	editor="mollybos"/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-android"
	ms.devlang="dotnet"
	ms.topic="get-started-article"
	ms.date="01/14/2016"
	ms.author="donnam"/>

# <a name="getting-started"> </a>Introducción a Servicios móviles

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]&nbsp;

>[AZURE.TIP] Si no está familiarizado con el desarrollo para dispositivos móviles con Microsoft Azure, [empiece a trabajar con Aplicaciones móviles de Azure](../app-service-mobile/app-service-mobile-dotnet-backend-xamarin-android-get-started-preview.md) en lugar de Servicios móviles de Azure; Aplicaciones móviles le ofrece [ventajas adicionales](app-service-mobile-value-prop-migration-from-mobile-services-preview.md).

En este tutorial se muestra cómo agregar un servicio de back-end basado en la nube a una aplicación Xamarin Android usando Servicios móviles de Azure. Con este tutorial creará tanto un servicio móvil nuevo como una aplicación simple de _Lista de pendientes_ que almacena datos de la aplicación en el servicio móvil nuevo. El servicio móvil que cree utilizará los lenguajes .NET compatibles y recurrirá a Visual Studio para la lógica de negocios de servidor y para las tareas de administración. Si desea crear un servicio móvil que le permita escribir su lógica de negocios de servidor en JavaScript, consulte la [versión back-end de JavaScript] de este tema.

>[AZURE.NOTE] En este tema se muestra cómo crear un proyecto de servicio móvil desde el Portal de Azure clásico. Si usa Visual Studio 2013 con actualización 2, también puede agregar un proyecto de servicio móvil nuevo a una solución existente de Visual Studio. Para obtener más información, consulte [Inicio rápido: Incorporación de un servicio móvil (back-end de .NET).](http://msdn.microsoft.com/library/windows/apps/dn629482.aspx)

La siguiente captura de pantalla muestra la aplicación final:

![][0]

Completar este tutorial es un requisito previo para todos los tutoriales de Servicios móviles para aplicaciones Xamarin Android.

>[AZURE.NOTE] Para completar este tutorial, deberá tener una cuenta de Azure. Si no dispone de ninguna cuenta, puede registrarse para obtener una versión de evaluación de Azure y conseguir hasta 10 servicios móviles gratuitos que podrá seguir usando incluso después de que finalice la evaluación. Para obtener más información, consulte [Evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fes-ES%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-xamarin-android-get-started). Este tutorial requiere [Visual Studio Professional 2013](https://go.microsoft.com/fwLink/p/?LinkID=257546). Hay disponible una versión de prueba gratuita.

## Creación de un servicio móvil

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../../includes/mobile-services-dotnet-backend-create-new-service.md)]

## Creación de una aplicación Xamarin Android

Después de crear el servicio móvil, puede seguir un sencillo inicio rápido en el portal clásico para crear una aplicación nueva o modificar una existente para conectarla a dicho servicio.

En esta sección va a descargar una nueva aplicación Xamarin Android y un proyecto de servicio para su servicio móvil.

1. En el [portal clásico], haga clic en **Servicios móviles** y luego en el servicio móvil que acaba de crear.

2. En la pestaña de inicio rápido, haga clic en **Xamarin** en **Seleccionar plataforma** y, a continuación, expanda **Crear una nueva aplicación Xamarin**.

   	![][6]

   	Con esto se muestran los tres sencillos pasos necesarios para crear una aplicación Xamarin Android conectada al servicio móvil.

  	![][7]

3. Si todavía no lo ha hecho, descargue e instale [Visual Studio Professional 2013](https://go.microsoft.com/fwLink/p/?LinkID=257546) en el equipo local o máquina virtual.

4. Si aún no lo ha hecho, descargue e instale [Xamarin Studio] o Xamarin para Visual Studio.

5. En **Descargar y publicar el servicio en la nube**, seleccione **Android** y haga clic en **Descargar**.

  	De esta forma se descarga una solución que contiene proyectos tanto para el servicio móvil como para la aplicación _To do list_ de ejemplo conectada a su servicio móvil. Guarde el archivo comprimido del proyecto en el equipo local y anote dónde lo guardó.

6. Descargue su perfil de publicación, guarde el archivo descargado en el equipo local y tome nota de dónde lo guarda.

## Prueba del servicio móvil

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service](../../includes/mobile-services-dotnet-backend-test-local-service.md)]

## Publicación del servicio móvil

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../../includes/mobile-services-dotnet-backend-publish-service.md)]

## Ejecución de la aplicación Xamarin Android

La etapa final de este tutorial consiste en crear y ejecutar la aplicación nueva.

1. Navegue al proyecto de cliente en la solución del servicio móvil, en Visual Studio o en Xamarin Studio.

2. Presione el botón **Ejecutar** para compilar el proyecto e iniciar la aplicación. Se le solicitará que seleccione un emulador o un dispositivo USB conectado.

	> [AZURE.NOTE] Para poder ejecutar el proyecto en el emulador de Android, debe definir al menos un dispositivo virtual de Android (AVD). Use el administrador AVD para crear y administrar estos dispositivos.

3. En la aplicación, escriba un texto significativo, como _Realice el tutorial_. A continuación, haga clic en el icono de suma (**+**).

	![][10]

	Esta acción envía una solicitud POST al nuevo servicio móvil hospedado en Azure. Los datos de la solicitud se insertan en la tabla TodoItem. El servicio móvil devuelve los elementos almacenados en la tabla y los datos se muestran en la lista.

	> [AZURE.NOTE]
   	Puede revisar el código de acceso al servicio móvil para consultar e insertar datos; este se encuentra en el archivo de C# ToDoActivity.cs.

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
[0]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-quickstart-completed-android.png
[6]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-portal-quickstart-xamarin.png
[7]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-quickstart-steps-xamarin-android.png
[8]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-xamarin-project-android-vs.png
[9]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-xamarin-project-android-xs.png
[10]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-quickstart-startup-android.png

<!-- URLs. -->
[Introducción a la sincronización de datos sin conexión]: mobile-services-xamarin-android-get-started-offline-data.md
[Introducción a la autenticación]: mobile-services-dotnet-backend-xamarin-android-get-started-users.md
[Introducción a las notificaciones de inserción]: mobile-services-dotnet-backend-xamarin-android-get-started-push.md
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[JavaScript and HTML]: mobile-services-win8-javascript/
[Azure classic portal]: https://manage.windowsazure.com/
[portal clásico]: https://manage.windowsazure.com/
[versión back-end de JavaScript]: mobile-services-android-get-started.md
[Solución de problemas de un back-end de .NET de Servicios móviles]: mobile-services-dotnet-backend-how-to-troubleshoot.md


[Xamarin Studio]: http://xamarin.com/download
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532&clcid=0x409
[Xamarin for Windows]: https://go.microsoft.com/fwLink/?LinkID=330242&clcid=0x409

<!---HONumber=AcomDC_0128_2016-->