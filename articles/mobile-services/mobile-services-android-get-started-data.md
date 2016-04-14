<properties
	pageTitle="Introducción a los datos en Android (back-end JavaScript) | Microsoft Azure"
	description="Obtenga información sobre cómo comenzar a usar Servicios móviles para aprovechar los datos de su aplicación Android (back-end JavaScript)."
	services="mobile-services"
	documentationCenter="android"
	authors="RickSaling"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="01/20/2016"
	ms.author="ricksal"/>

# Incorporación de servicios móviles a una aplicación Android existente (back-end JavaScript)

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

## Resumen

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>Este tema muestra cómo utilizar Servicios móviles de Azure para aprovechar los datos en una aplicación de Android. En este tutorial descargará una aplicación que almacena datos en memoria, creará un servicio móvil nuevo, integrará la aplicación con el servicio móvil para que almacene y actualice datos en los Servicios móviles de Azure en lugar de hacerlo de manera local y usará el Portal de Azure clásico. para ver los cambios que se hicieron en los datos durante la ejecución de la aplicación.</p>

</div>


<div class="dev-onpage-video-wrapper">
<a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-With-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services" target="_blank" class="label">Ver el tutorial</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-android-get-started-data-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-With-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">(en inglés) Reproducir vídeo (en inglés)</span></a><span class="time">15:32</span></div>
</div>


<p>Este tutorial le ayuda a comprender con más detalle cómo los Servicios móviles de Azure pueden almacenar y recuperar datos desde una aplicación Android. De ese modo, este tutorial recorre mucho de los pasos ya completados para usted en el tutorial de inicio rápido de Servicios móviles. Si esta es la primera vez que usa los Servicios móviles, considere la posibilidad de completar antes el tutorial <a href="/es-ES/develop/mobile/tutorials/get-started-android">Introducción a los Servicios móviles</a>.</p>

## Requisitos previos

Para completar este tutorial, necesitará lo siguiente:

- una cuenta de Azure. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AED8DE357" target="_blank">Evaluación gratuita de Azure</a>.


- el [SDK de Android para Servicios móviles]
- el <a  href="https://developer.android.com/sdk/index.html" target="_blank">entorno de desarrollo integrado de Android Studio</a>, que incluye el SDK de Android y Android 4.2 o una versión más reciente. El proyecto GetStartedWithData descargado requiere Android 4.2 o una versión más reciente. No obstante, el SDK para Servicios móviles solo requiere Android 2.2 o una versión más reciente.

## Código de ejemplo

Para ver el código fuente completo, vaya <a href="https://github.com/Azure/mobile-services-samples/tree/master/GettingStartedWithData/AndroidStudio">aquí</a>.

## Descarga del proyecto GetStartedWithData

###Obtención del código de ejemplo

[AZURE.INCLUDE [download-android-sample-code](../../includes/download-android-sample-code.md)]

### Inspección y ejecución del código de ejemplo

[AZURE.INCLUDE [mobile-services-android-run-sample-code](../../includes/mobile-services-android-run-sample-code.md)]

## Crear un nuevo servicio móvil en el Portal de Azure clásico

[AZURE.INCLUDE [mobile-services-create-new-service-data](../../includes/mobile-services-create-new-service-data.md)]

## Agregar una tabla nueva al servicio móvil

[AZURE.INCLUDE [mobile-services-create-new-service-data-2](../../includes/mobile-services-create-new-service-data-2.md)]

## Actualización de la aplicación para usar el servicio móvil para el acceso a datos

[AZURE.INCLUDE [mobile-services-android-getting-started-with-data](../../includes/mobile-services-android-getting-started-with-data.md)]


## Prueba de la aplicación con su servicio móvil nuevo

Ahora que la aplicación se ha actualizado para usar los Servicios móviles para almacenamiento back-end, puede probarla con los Servicios móviles usando el emulador de Android o un teléfono Android.

1. En el menú **Ejecutar**, haga clic en **Ejecutar aplicación** para iniciar el proyecto.

	De este modo se ejecuta su aplicación, que se ha creado con el SDK de Android, y se usa la biblioteca del cliente para enviar una consulta que devuelve los elementos desde su servicio móvil.

5. Como antes, escriba texto descriptivo y, a continuación, haga clic en **Agregar**.

   	Esto envía un elemento nuevo como inserción al servicio móvil.

3. Inicie sesión en el [Portal de Azure clásico], haga clic en **Servicios móviles **y en su servicio móvil.

4. Haga clic en la pestaña **Datos** y, a continuación, en **Examinar**.

   	![][9]

   	Observe que la tabla **TodoItem** ahora contiene datos con valores de identificador generados por Servicios móviles y que se agregaron automáticamente columnas a la tabla para que coincida con la clase TodoItem de la aplicación.

Así concluye el tutorial **Introducción a los datos** para Android.

## Solución de problemas

### Comprobación de la versión del SDK de Android

[AZURE.INCLUDE [Comprobar el SDK](../../includes/mobile-services-verify-android-sdk-version.md)]



## Pasos siguientes

Este tutorial muestra los aspectos básicos de la habilitación de una aplicación Android para que funcione con los datos de los Servicios móviles.

A continuación, considere la realización de uno de los siguientes tutoriales que se basan en la aplicación GetStartedWithData que creó en este tutorial:

* [Validación y modificación de datos con scripts] <br/>Obtenga más información acerca del uso de scripts de servidor en Servicios móviles para validar y cambiar datos enviados desde su aplicación.

* [Limitación de consultas con paginación] <br/>Aprenda a utilizar la paginación en consultas para controlar la cantidad de datos que se manejan en una única solicitud.

Cuando haya completado la serie de datos, pruebe estos otros tutoriales de Android:

* [Introducción a la autenticación] <br/>Aprenda a autenticar a los usuarios de su aplicación.

* [Introducción a las notificaciones de inserción] <br/>Aprenda a enviar una notificación de inserción muy básica a la aplicación con Servicios móviles.

<!-- Anchors. -->
[Download the Android app project]: #download-app
[Create the mobile service]: #create-service
[Add a data table for storage]: #add-table
[Update the app to use Mobile Services]: #update-app
[Test the app against Mobile Services]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[8]: ./media/mobile-services-android-get-started-data/mobile-dashboard-tab.png
[9]: ./media/mobile-services-android-get-started-data/mobile-todoitem-data-browse.png
[12]: ./media/mobile-services-android-get-started-data/mobile-eclipse-project.png
[13]: ./media/mobile-services-android-get-started-data/mobile-quickstart-startup-android.png
[14]: ./media/mobile-services-android-get-started-data/mobile-services-import-android-workspace.png
[15]: ./media/mobile-services-android-get-started-data/mobile-services-import-android-project.png


<!-- URLs. -->
[Validación y modificación de datos con scripts]: /develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Limitación de consultas con paginación]: /develop/mobile/tutorials/add-paging-to-data-android
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-android
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-android
[Introducción a la autenticación]: /develop/mobile/tutorials/get-started-with-users-android
[Introducción a las notificaciones de inserción]: /develop/mobile/tutorials/get-started-with-push-android

[Portal de Azure clásico]: https://manage.windowsazure.com/
[SDK de Android para Servicios móviles]: http://aka.ms/Iajk6q
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkID=282122
[Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125

<!---HONumber=AcomDC_0121_2016-->