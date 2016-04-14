<properties
	pageTitle="Introducción a Servicios móviles para Xamarin.Android | Microsoft Azure"
	writer="craigd"
	description="Obtenga información acerca de cómo utilizar Servicios móviles de Azure con la aplicación Xamarin.Android."
	documentationCenter="xamarin"
	authors="lindydonna"
	manager="dwrede"
	editor=""
	services="mobile-services"/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-android"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="02/10/2016"
	ms.author="donnam"/>

# <a name="getting-started"></a>Introducción a Servicios móviles

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]&nbsp;

[AZURE.INCLUDE [mobile-services-hero-slug](../../includes/mobile-services-hero-slug.md)]

En este tutorial se indica cómo agregar un servicio back-end basado en la nube a una aplicación Xamarin.Android mediante Servicios móviles de Azure. Con este tutorial creará tanto un servicio móvil nuevo como una aplicación simple de *Lista de pendientes* que almacena datos de la aplicación en el servicio móvil nuevo.

Si prefiere ver un vídeo, el clip que aparece a continuación muestra los mismos pasos que este tutorial.

Vídeo: "Getting Started with Xamarin and Azure Mobile Services" (Introducción a Xamarin con Servicios móviles de Azure) con Craig Dunn, desarrollador evangelista de Xamarin (duración: 10:05 min)

> [AZURE.VIDEO getting-started-with-xamarin-and-mobile-services]

La siguiente captura de pantalla muestra la aplicación final:

![][0]

Para completar este tutorial es necesario [Xamarin.Android], que instala Xamarin Studio y un complemento de Visual Studio (en Windows) y la plataforma de Android más reciente. Se requiere el SDK de Android 4.2 o versiones posteriores.

El proyecto de inicio rápido descargado contiene el componente Servicios móviles de Azure para Xamarin.Android. Si bien este proyecto requiere Android 4.2 o versiones posteriores, el SDK para Servicios móviles tan solo requiere Android 2.2 o una versión posterior.

> [AZURE.IMPORTANT] Para completar este tutorial, deberá tener una cuenta de Azure. Si no dispone de ninguna cuenta, puede registrarse para obtener una versión de evaluación de Azure y conseguir hasta 10 servicios móviles gratuitos que podrá seguir usando incluso después de que finalice la evaluación. Para obtener más información, consulte [Evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A9C9624B5).

## <a name="create-new-service"> </a>Creación de un servicio móvil

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## Creación de una nueva aplicación Xamarin.Android

Después de crear el servicio móvil, puede seguir una sencilla introducción rápida en el Portal de Azure clásico para crear una aplicación o modificar una ya existente a fin de conectarla a dicho servicio.

En esta sección se creará una nueva aplicación Xamarin.Android que se conecta al servicio móvil.

1.  En el [Portal de Azure clásico], haga clic en **Servicios móviles** y luego en el servicio móvil que acaba de crear.

2. En la pestaña de inicio rápido, haga clic en **Xamarin.Android** en **Seleccionar plataforma** y expanda **Crear una nueva aplicación Android**.

	![][6]

	Con esto se muestran los tres sencillos pasos requeridos para crear una aplicación Xamarin.Android conectada al servicio móvil.

	![][7]

3. Haga clic en **Crear tabla TodoItem** para crear una tabla para almacenar los datos de la aplicación.

4. En **Descargar y ejecutar la aplicación**, haga clic en **Descargar**.

	De este modo se descarga el proyecto para la aplicación de _lista de pendientes_ de muestra que está conectada al servicio móvil. Guarde el archivo comprimido del proyecto en el equipo local y anote dónde lo guardó.

## Ejecución de la aplicación Android

La etapa final de este tutorial consiste en crear y ejecutar la aplicación nueva.

1. Desplácese a la ubicación donde se guardaron los archivos del proyecto comprimidos y expanda dichos archivos en el equipo.

2. En Xamarin Studio o Visual Studio, haga clic en **File** y, a continuación en **Open**. Posteriormente, desplácese a los archivos de ejemplo sin comprimir y seleccione **XamarinTodoQuickStart.Android.sln** para abrirlo.

3. Presione el botón **Run** para compilar el proyecto e iniciar la aplicación. Se le solicitará que seleccione un emulador o un dispositivo USB conectado.

	> [AZURE.NOTE] Para poder ejecutar el proyecto en el emulador de Android, debe definir al menos un dispositivo virtual Android (AVD). Use el administrador AVD para crear y administrar estos dispositivos.

4. En la aplicación, escriba un texto significativo, como _Realizar el tutorial_ y haga clic en **Add** (Agregar).

	![][10]

	Esta acción envía una solicitud POST al nuevo servicio móvil hospedado en Azure. Los datos de la solicitud se insertan en la tabla TodoItem. El servicio móvil devuelve los elementos almacenados en la tabla y los datos se muestran en la lista.

	> [AZURE.NOTE]
   	Puede revisar el código de acceso al servicio móvil para consultar e insertar datos; este se encuentra en el archivo de C# ToDoActivity.cs.

6. De nuevo en el [Portal de Azure clásico], haga clic en la pestaña **Datos** y luego en la tabla **TodoItems**.

	![][11]

	Esto le permite examinar los datos que la aplicación inserta en la tabla.

	![][12]

## <a name="next-steps"> </a>Pasos siguientes
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
[0]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-quickstart-completed-android.png
[2]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-create.png
[3]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-create-page1.png
[4]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-create-page2.png
[5]: ./media/partner-xamarin-mobile-services-android-get-started/obile-services-selection.png
[6]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-portal-quickstart-xamarin-android.png
[7]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-quickstart-steps-xamarin-android.png
[8]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-xamarin-project-android-xs.png
[9]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-xamarin-project-android-vs.png
[10]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-quickstart-startup-android.png
[11]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-data-tab.png
[12]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-data-browse.png
[13]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-services-diagram.png


<!-- URLs. -->
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-xamarin-android
[Introducción a la sincronización de datos sin conexión]: mobile-services-xamarin-android-get-started-offline-data.md
[Introducción a la autenticación]: /develop/mobile/tutorials/get-started-with-users-xamarin-android
[Introducción a las notificaciones push]: /develop/mobile/tutorials/get-started-with-push-xamarin-android
[Xamarin.Android]: http://xamarin.com/download
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Azure]: http://azure.microsoft.com/
[Portal de Azure clásico]: https://manage.windowsazure.com/

<!---HONumber=AcomDC_0211_2016-->