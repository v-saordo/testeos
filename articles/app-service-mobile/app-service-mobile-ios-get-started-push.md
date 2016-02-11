<properties
	pageTitle="Incorporación de notificaciones push a la aplicación iOS con las Aplicaciones móviles de Azure"
	description="Obtenga información acerca de cómo usar las Aplicaciones móviles de Azure para enviar notificaciones push a su aplicación iOS."
	services="app-service\mobile"
	documentationCenter="ios"
	manager="dwrede"
	editor=""
	authors="krisragh"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="11/25/2015"
	ms.author="krisragh"/>


# Incorporación de notificaciones push a la aplicación iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

## Información general
En este tutorial, agregará notificaciones push al proyecto de [inicio rápido de iOS] para que cada vez que se inserte un registro, se envíe una notificación push. Este tutorial está basado en el tutorial de [inicio rápido de iOS], que debe completar primero. Si no usa el proyecto de servidor de inicio rápido descargado, debe agregar el paquete de extensión de notificaciones push al proyecto. Para obtener más información acerca de los paquetes de extensión de servidor, consulte [Trabajar con el SDK del servidor back-end de .NET para Aplicaciones móviles de Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

El [simulador de iOS no admite notificaciones push](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html), por lo que, para este tutorial, necesita un dispositivo iOS físico y una [suscripción al programa para desarrolladores de Apple](https://developer.apple.com/programs/ios/).

##<a name="create-hub"></a>Creación de un centro de notificaciones

[AZURE.INCLUDE [app-service-mobile-create-notification-hub](../../includes/app-service-mobile-create-notification-hub.md)]

## <a id="register"></a>Registro de aplicaciones para notificaciones push

[AZURE.INCLUDE [Habilitación de notificaciones de inserción de Apple](../../includes/enable-apple-push-notifications.md)]

## Configuración de Azure para enviar notificaciones push

[AZURE.INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

##<a id="update-server"></a>Actualización del proyecto de servidor para enviar notificaciones push

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-configure-push-apns](../../includes/app-service-mobile-dotnet-backend-configure-push-apns.md)]

## <a id="add-push"></a>Agregar notificaciones push a la aplicación

[AZURE.INCLUDE [app-service-mobile-add-push-notifications-to-ios-app.md](../../includes/app-service-mobile-add-push-notifications-to-ios-app.md)]

## <a id="test"></a>Prueba de las notificaciones push en la aplicación

[AZURE.INCLUDE [Prueba de las notificaciones push en la aplicación](../../includes/test-push-notifications-in-app.md)]

##<a id="more"></a>Más

* Las plantillas proporcionan flexibilidad para enviar inserciones multiplataforma e inserciones localizadas. [Uso de la biblioteca de cliente de iOS para Aplicaciones móviles de Azure](app-service-mobile-ios-how-to-use-client-library.md#templates) muestra cómo registrar plantillas.
* Las etiquetas permiten dirigirse a clientes segmentados con inserciones. [Trabajar con el SDK del servidor back-end de .NET para Aplicaciones móviles de Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#tags) muestra cómo agregar etiquetas a la instalación de un dispositivo.

<!-- Anchors.  -->
[Generate iOS certificate signing request]: #certificates
[Register your app and enable push notifications]: #register
[Create a provisioning profile for the app]: #profile
[Add push notifications to the app]: #add-push
[Configure your mobile backend to send push requests]: #configure
[Update the server to send push notifications]: #update-server
[Publish the mobile backend to Azure]: #publish-mobile-service
[Test your app]: #test-the-service

<!-- Images. -->

<!-- URLs. -->
[inicio rápido de iOS]: app-service-mobile-ios-get-started.md

<!----HONumber=AcomDC_1223_2015-->