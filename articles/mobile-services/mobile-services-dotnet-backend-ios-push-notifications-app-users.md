<properties
	pageTitle="Envío de notificaciones de inserción a usuarios autenticados (backend .NET)"
	description="Obtenga información acerca de cómo enviar notificaciones de inserción a específicos"
	services="mobile-services,notification-hubs"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="01/12/2016"
	ms.author="krisragh"/>

# Envío de notificaciones de inserción a usuarios autenticados

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-push-users](../../includes/mobile-services-selector-push-users.md)]

En este tema se muestra cómo enviar notificaciones de inserción a un usuario autenticado en iOS. Antes de comenzar este tutorial, complete en primer lugar [Introducción a la autenticación] e [Introducción a las notificaciones de inserción].

En este tutorial, se requiere que los usuarios se autentiquen primero, registrar con el centro de notificaciones para las notificaciones de inserción y actualizar scripts de servidor para enviar las notificaciones únicamente a los usuarios autenticados.

##<a name="register"></a>Actualización del servicio para solicitar autenticación para registro

[AZURE.INCLUDE [mobile-services-dotnet-backend-push-notifications-app-users](../../includes/mobile-services-dotnet-backend-push-notifications-app-users.md)]

##<a name="update-app"></a>Actualización de la aplicación para iniciar sesión antes del registro

[AZURE.INCLUDE [mobile-services-ios-push-notifications-app-users-login](../../includes/mobile-services-ios-push-notifications-app-users-login.md)]

##<a name="test"></a>Aplicación de prueba

[AZURE.INCLUDE [mobile-services-ios-push-notifications-app-users-test-app](../../includes/mobile-services-ios-push-notifications-app-users-test-app.md)]

<!-- Anchors. -->
[Updating the service to require authentication for registration]: #register
[Updating the app to log in before registration]: #update-app
[Testing the app]: #test
[Next Steps]: #next-steps


<!-- URLs. -->
[Introducción a la autenticación]: mobile-services-dotnet-backend-ios-get-started-users.md
[Introducción a las notificaciones de inserción]: mobile-services-dotnet-backend-ios-get-started-push.md
[Mobile Services .NET How-to Conceptual Reference]: /develop/mobile/how-to-guides/work-with-net-client-library

<!---HONumber=AcomDC_0114_2016-->