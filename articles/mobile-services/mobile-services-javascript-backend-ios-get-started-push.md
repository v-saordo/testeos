<properties
	pageTitle="Incorporación de notificaciones push a la aplicación (iOS) | Back-end de JavaScript"
	description="Obtenga información acerca de cómo usar Servicios móviles de Azure para enviar notificaciones de inserción a su aplicación iOS."
	services="mobile-services,notification-hubs"
	documentationCenter="ios"
	manager="dwrede"
	editor=""
	authors="krisragh"/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="01/12/2016"
	ms.author="krisragh"/>

# Incorporación de notificaciones push a una aplicación de iOS y un back-end de JavaScript

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../../includes/mobile-services-selector-get-started-push.md)]

En este tema se muestra cómo agregar notificaciones push al [proyecto de inicio rápido](mobile-services-ios-get-started.md), de modo que el servicio móvil envíe una notificación push cada vez que se inserte un registro. Debe completar primero el tutorial [Introducción a Servicios móviles].

> [AZURE.NOTE]El [simulador de iOS no admite notificaciones push](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html), por lo que debe usar un dispositivo iOS físico. También deberá suscribirse como [miembro del programa para desarrolladores de Apple](https://developer.apple.com/programs/ios/). Esta suscripción es de pago.

[AZURE.INCLUDE [Habilitación de notificaciones de inserción de Apple](../../includes/enable-apple-push-notifications.md)]


## <a id="configure"></a>Configuración de Azure para enviar notificaciones push

[AZURE.INCLUDE [Configuración de notificaciones push en Servicios móviles de Azure](../../includes/mobile-services-apns-configure-push.md)]

## <a id="update-scripts"></a>Actualización del script de back-end para enviar notificaciones push

* En el [Portal de Azure clásico], haga clic en la pestaña **Datos** y luego en **TodoItem**. En **TodoItem**, haga clic en la pestaña **Script** y seleccione **Insertar**. Se muestra la función que se invoca cuando se produce una inserción en la tabla **TodoItem**.

* Reemplace la función de inserción por el siguiente código y haga clic en **Guardar**. De esta forma, se registra un nuevo script de inserción, que usa el [objeto apns] para enviar una notificación de inserción (el texto insertado) al dispositivo especificado en la solicitud de inserción. Este script retrasa el envío de la notificación para proporcionarle tiempo para cerrar la aplicación y recibir una notificación del sistema.


```
        function insert(item, user, request) {
            request.execute();
            // Set timeout to delay the notification, to provide time for the
            // app to be closed on the device to demonstrate push notifications
            setTimeout(function() {
                push.apns.send(null, {
                    alert: "Alert: " + item.text,
                    payload: {
                        inAppMessage: "Hey, a new item arrived: '" + item.text + "'"
                    }
                });
            }, 2500);
        }
```

[AZURE.INCLUDE [Agregar notificaciones push a la aplicación](../../includes/add-push-notifications-to-app.md)]

[AZURE.INCLUDE [Prueba de las notificaciones push en la aplicación](../../includes/test-push-notifications-in-app.md)]


<!-- Anchors. -->


<!-- Images. -->
[5]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step5.png
[6]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step6.png
[7]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step7.png

[9]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step9.png
[10]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step10.png
[17]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step17.png
[18]: ./media/mobile-services-ios-get-started-push/mobile-services-selection.png
[19]: ./media/mobile-services-ios-get-started-push/mobile-push-tab-ios.png
[20]: ./media/mobile-services-ios-get-started-push/mobile-push-tab-ios-upload.png
[21]: ./media/mobile-services-ios-get-started-push/mobile-portal-data-tables.png
[22]: ./media/mobile-services-ios-get-started-push/mobile-insert-script-push2.png
[23]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push1-ios.png
[24]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push2-ios.png
[25]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push3-ios.png
[26]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push4-ios.png
[28]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step18.png

[101]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-01.png
[102]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-02.png
[103]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-03.png
[104]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-04.png
[105]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-05.png
[106]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-06.png
[107]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-07.png
[108]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-08.png

[110]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-10.png
[111]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-11.png
[112]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-12.png
[113]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-13.png
[114]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-14.png
[115]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-15.png
[116]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-16.png
[117]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-17.png

<!-- URLs.   -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Apple Push Notification Service]: http://go.microsoft.com/fwlink/p/?LinkId=272584
[Introducción a Servicios móviles]: mobile-services-ios-get-started.md
[Get started with authentication]: mobile-services-ios-get-started-users.md
[Portal de Azure clásico]: https://manage.windowsazure.com/
[objeto apns]: http://go.microsoft.com/fwlink/p/?LinkId=272333

[Mobile Services server script reference]: http://go.microsoft.com/fwlink/?LinkId=262293

[Send push notifications to authenticated users]: mobile-services-javascript-backend-ios-push-notifications-app-users.md
[What are Notification Hubs?]: ../notification-hubs-overview.md
[Send broadcast notifications to subscribers]: ../notification-hubs-ios-send-breaking-news.md
[Send template-based notifications to subscribers]: ../notification-hubs-ios-send-localized-breaking-news.md
[Mobile Services Objective-C how-to conceptual reference]: mobile-services-windows-dotnet-how-to-use-client-library.md

<!---HONumber=AcomDC_0114_2016-->