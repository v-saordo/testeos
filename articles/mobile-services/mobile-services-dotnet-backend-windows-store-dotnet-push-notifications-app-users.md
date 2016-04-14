<properties
	pageTitle="Envío de notificaciones push a usuarios autenticados (Universal Windows 8.1) | Servicios móviles de Azure"
	description="Obtenga información acerca de cómo usar Servicios móviles de Azure para enviar notificaciones push a un usuario autenticado específico que esté ejecutando su aplicación de Universal Windows 8.1."
	services="mobile-services,notification-hubs"
	documentationCenter="windows"
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="01/13/2016"
	ms.author="glenga"/>

# Envío de notificaciones de inserción a usuarios autenticados

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-push-users](../../includes/mobile-services-selector-push-users.md)]

##Información general

En este tema se muestra cómo enviar notificaciones de inserción a un usuario autenticado en cualquier dispositivo registrado. A diferencia del tutorial de [notificaciones de inserción][Get started with push notifications] anterior, este tutorial cambia el dispositivo móvil para solicitar que un usuario se autentique antes de que el cliente pueda registrarse con el centro de notificaciones para notificaciones de inserción. El registro también se modifica para agregar una etiqueta basada en el identificador del usuario asignado. Por último, el script de servidor se actualiza para enviar la notificación solamente al usuario autenticado en lugar de a todos los registros.

Este tutorial es válido para aplicaciones de la Tienda Windows y la Tienda de Windows Phone.

##Requisitos previos

Antes de comenzar este tutorial, debe haber realizado los siguientes tutoriales de Servicios móviles:

+ [Introducción a la autenticación] Agregue un requisito de inicio de sesión a la aplicación de ejemplo TodoList.

+ [Introducción a las notificaciones de inserción] Configure la aplicación de ejemplo TodoList para notificaciones de inserción usando los Centros de notificaciones.

Una vez que haya realizado ambos tutoriales, puede impedir que usuarios no autorizados se registren para notificaciones de inserción desde su servicio móvil.

##<a name="register"></a>Actualización del servicio para solicitar autenticación para registro

[AZURE.INCLUDE [mobile-services-dotnet-backend-push-notifications-app-users](../../includes/mobile-services-dotnet-backend-push-notifications-app-users.md)]

##<a name="update-app"></a>Actualización de la aplicación para iniciar sesión antes del registro

[AZURE.INCLUDE [mobile-services-windows-store-dotnet-push-notifications-app-users](../../includes/mobile-services-windows-store-dotnet-push-notifications-app-users.md)]

##<a name="test"></a>Prueba de la aplicación

[AZURE.INCLUDE [mobile-services-windows-test-push-users](../../includes/mobile-services-windows-test-push-users.md)]



<!-- Anchors. -->
[Updating the service to require authentication for registration]: #register
[Updating the app to log in before registration]: #update-app
[Testing the app]: #test
[Next Steps]: #next-steps


<!-- URLs. -->
[Introducción a la autenticación]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-users.md
[Get started with push notifications]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-push.md
[Introducción a las notificaciones de inserción]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-push.md

<!---HONumber=AcomDC_0114_2016-->