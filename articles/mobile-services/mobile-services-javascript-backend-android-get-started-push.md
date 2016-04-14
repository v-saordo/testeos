
<properties
	pageTitle="Introducción a las notificaciones push (JavaScript para Android) | Microsoft Azure"
	description="Obtenga información acerca de cómo usar Servicios móviles de Azure para enviar notificaciones de inserción a la aplicación JavaScript de Android."
	services="mobile-services, notification-hubs"
	documentationCenter="android"
	authors="RickSaling"
	writer="ricksal"
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


# Incorporación de notificaciones push a la aplicación Android para Servicios móviles

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../../includes/mobile-services-selector-get-started-push.md)]

## Resumen

Este tema muestra cómo puede utilizar Servicios móviles de Azure para enviar notificaciones push a su aplicación Android usando la mensajería en la nube de Google ("GCM"). Agregará notificaciones push al proyecto de inicio rápido, un requisito previo para este tutorial. Las notificaciones push se habilitan mediante el uso del Centro de notificaciones de Azure que se incluye en el servicio móvil. Cuando haya finalizado, el servicio móvil le enviará una notificación de inserción cada vez que se inserte un registro.

## Requisitos previos

[AZURE.INCLUDE [mobile-services-android-prerequisites](../../includes/mobile-services-android-prerequisites.md)]

## Código de ejemplo
Para ver el código fuente completo, vaya [aquí](https://github.com/Azure/mobile-services-samples/tree/master/GettingStartedWithPush).

## Habilitación del servicio de mensajería en la nube de Google

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

## Configuración de Servicios móviles para enviar solicitudes de inserción

[AZURE.INCLUDE [mobile-services-android-configure-push](../../includes/mobile-services-android-configure-push.md)]

## Incorporación de notificaciones de inserción a la aplicación



El siguiente paso es instalar los servicios de Google Play. El servicio de mensajería en la nube de Google tiene algunos requisitos mínimos en el nivel de API para desarrollo y prueba, que debe cumplir la propiedad **minSdkVersion** del manifiesto.

Si va a realizar pruebas con un dispositivo antiguo, consulte [Configuración del SDK de Google Play Services] para determinar el valor mínimo que puede configurar, y cómo configurarlo de forma adecuada.

###Incorporación de Google Play Services al proyecto

[AZURE.INCLUDE [Incorporación de Play Services](../../includes/mobile-services-add-google-play-services.md)]

###Incorporación de código

[AZURE.INCLUDE [mobile-services-android-getting-started-with-push](../../includes/mobile-services-android-getting-started-with-push.md)]


## Actualización del script de inserción registrado en el Portal de Azure clásico

[AZURE.INCLUDE [mobile-services-javascript-backend-android-push-insert-script](../../includes/mobile-services-javascript-backend-android-push-insert-script.md)]


## Prueba de las notificaciones push en su aplicación

Puede probar la aplicación conectando directamente un teléfono Android con un cable USB o utilizando un dispositivo virtual en el emulador.

###Configuración de un emulador de Android para realizar pruebas

Cuando ejecute esta aplicación en el emulador, asegúrese de utilizar un dispositivo virtual Android (AVD) compatible con las API de Google.

1. En el extremo derecho de la barra de herramientas, seleccione Administrador de dispositivos virtuales de Android, seleccione su dispositivo y haga clic en el icono de edición que se encuentra a la derecha.

	![](./media/mobile-services-javascript-backend-android-get-started-push/mobile-services-android-virtual-device-manager.png)

2. Seleccione **Cambiar** en la línea descriptiva del dispositivo, seleccione **API de Google** y, a continuación, haga clic en Aceptar.

   	![](./media/mobile-services-javascript-backend-android-get-started-push/mobile-services-android-virtual-device-manager-edit.png)

	Con esto se dirige el dispositivo virtual Android para que use las API de Google.

###Ejecución de la prueba

1. En el elemento de menú **Ejecutar**, haga clic en **Ejecutar aplicación** para iniciar la aplicación.

2. En la aplicación, escriba texto significativo como _A new Mobile Services task_ y, a continuación, haga clic en el botón **Add** (Agregar).

  	![](./media/mobile-services-javascript-backend-android-get-started-push/mobile-quickstart-push1-android.png)

3. Deslice el dedo hacia abajo desde la parte superior de la pantalla para abrir el Centro de notificaciones del dispositivo y visualizar la notificación.


Ha completado correctamente este tutorial.

## Solución de problemas

### Comprobación de la versión del SDK de Android

[AZURE.INCLUDE [Comprobar el SDK](../../includes/mobile-services-verify-android-sdk-version.md)]

## Pasos siguientes

<!---This tutorial demonstrated the basics of enabling an Android app to use Mobile Services and Notification Hubs to send push notifications. Next, consider completing the next tutorial, [Send push notifications to authenticated users], which shows how to use tags to send push notifications from a Mobile Service to only an authenticated user.

+ [Send broadcast notifications to subscribers]
	<br/>Learn how users can register and receive push notifications for categories they're interested in.

+ [Send template-based notifications to subscribers]
	<br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end.
-->

Puede obtener más información acerca de los Servicios móviles y los Centros de notificaciones en los siguientes temas:

* [Introducción a la autenticación] <br/>Aprenda a autenticar a los usuarios de su aplicación con distintos tipos de cuentas con los servicios móviles.

* [¿Qué son los Centros de notificaciones?] <br/>Obtenga más información sobre el funcionamiento de los Centros de notificaciones para entregar notificaciones a sus aplicaciones en todas las principales plataformas de cliente.

* [Depuración de aplicaciones de centros de notificaciones](http://go.microsoft.com/fwlink/p/?linkid=386630) </br>Obtenga instrucciones de resolución de problemas y depuración de soluciones de centros de notificaciones.

* [Cómo usar la biblioteca de cliente Android para servicios móviles] <br/>Obtenga más información acerca de cómo usar servicios móviles con Android.

* [Referencia de script de servidor de servicios móviles] <br/>Obtenga más información acerca de cómo implementar la lógica empresarial en el servicio móvil.


<!-- Anchors. -->
[Register your app for push notifications and configure Mobile Services]: #register
[Update the generated push notification code]: #update-scripts
[Insert data to receive notifications]: #test
[Next Steps]: #next-steps

<!-- Images. -->
[13]: ./media/mobile-services-windows-store-javascript-get-started-push/mobile-quickstart-push1.png
[14]: ./media/mobile-services-windows-store-javascript-get-started-push/mobile-quickstart-push2.png


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Get started with Mobile Services]: mobile-services-android-get-started.md
[Introducción a la autenticación]: mobile-services-android-get-started-users.md
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-js
[Push notifications to app users]: /develop/mobile/tutorials/push-notifications-to-users-js
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-js
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js
[Configuración del SDK de Google Play Services]: http://go.microsoft.com/fwlink/?LinkId=389801
[Azure classic portal]: https://manage.windowsazure.com/
[Cómo usar la biblioteca de cliente Android para servicios móviles]: mobile-services-android-how-to-use-client-library.md

[gcm object]: http://go.microsoft.com/fwlink/p/?LinkId=282645

[Referencia de script de servidor de servicios móviles]: http://go.microsoft.com/fwlink/?LinkId=262293
[¿Qué son los Centros de notificaciones?]: ../notification-hubs-overview.md
[Send broadcast notifications to subscribers]: ../notification-hubs-android-send-breaking-news.md
[Send template-based notifications to subscribers]: ../notification-hubs-android-send-localized-breaking-news.md

<!---HONumber=AcomDC_0121_2016-->