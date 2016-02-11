<properties 
	pageTitle="Envío de notificaciones entre plataformas a un usuario específico de iOS" 
	description="Obtenga información acerca de cómo enviar notificaciones push a todos los dispositivos de un usuario específico."
	services="app-service\mobile,notification-hubs" 
	documentationCenter="ios" 
	authors="ysxu" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-dotnet" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="11/17/2015"
	ms.author="yuaxu"/>

# Envío de notificaciones entre plataformas a un usuario específico

[AZURE.INCLUDE [app-service-mobile-selector-push-users](../../includes/app-service-mobile-selector-push-users.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

En este tema se muestra cómo enviar notificaciones a todos los dispositivos registrados de un usuario concreto desde su back-end móvil. Introdujo el concepto de [plantillas], que proporciona a las aplicaciones cliente la libertad de especificar formatos de carga y marcadores de posición de variables en el registro. A continuación, envíe aciertos a todas las plataformas con estos marcadores de posición, y habilite las notificaciones multiplataforma.

> [AZURE.NOTE]Para obtener la inserción (push) cuando trabaja con clientes multiplataforma, es necesario completar este tutorial para cada plataforma que desea habilitar. Solo tendrá que hacer la [actualización de back-end móvil](#backend) una vez para los clientes que comparten el mismo back-end móvil.
 
##Requisitos previos 

Antes de iniciar este tutorial, debe haber completado estos tutoriales del Servicio de aplicaciones para cada plataforma cliente que desee que esté en funcionamiento:

+ [Introducción a la autenticación]<br/>Agrega un requisito de inicio de sesión a la aplicación de ejemplo TodoList.

+ [Introducción a las notificaciones de inserción]<br/>Configura la aplicación de ejemplo TodoList para notificaciones push.

##<a name="client"></a>Actualización del cliente para registrarlo para plantillas con el fin de controlar las inserciones multiplataforma

1. Mueva los fragmentos de registro de APN en **QSAppDelegate.m** **application:didFinishLaunchingWithOptions** a la llamada a **loginWithProvider** en **QSTodoListViewController.m** para que tenga lugar una vez completada la autenticación:

        [client loginWithProvider:@"facebook" controller:self animated:YES completion:^(MSUser *user, NSError *error) {
            [self refresh];
            
            // register iOS8 or previous devices for notifications
            if ([[UIApplication sharedApplication] respondsToSelector:@selector(registerUserNotificationSettings:)] && [[UIApplication sharedApplication] respondsToSelector:@selector(registerForRemoteNotifications)]) {
                [[UIApplication sharedApplication] registerForRemoteNotifications];
            }
            else {
                // Register for remote notifications
                [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
                 UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
            }
        }];

2. Reemplace la llamada **registerDeviceToken** en **application:didRegisterForRemoteNotificationsWithDeviceToken** con las siguientes opciones para trabajar con plantillas:

        NSDictionary *templates = @{
                               @"testNotificationTemplate": @{ @"body" : @{ @"aps" : @{ @"alert": @"$(message)" } } }
                               };
    
        // register with templates
        [client.push registerDeviceToken:deviceToken template:templates completion:^(NSError *error) {
            if (error != nil) {
                NSLog(@"Error registering for notifications: %@", error);
            }
        }];

	Es importante que se autentique al usuario antes de registrarse para las notificaciones push. Cuando un usuario autenticado se registra para las notificaciones push, se agrega automáticamente una etiqueta con el identificador de usuario.

##<a name="backend"></a>Actualización del servidor back-end de servicio para enviar notificaciones a un usuario específico

[AZURE.INCLUDE [app-service-mobile-push-notifications-to-users](../../includes/app-service-mobile-push-notifications-to-users.md)]

##<a name="test"></a>Prueba de la aplicación

Vuelva a publicar el proyecto de back-end móvil y ejecute cualquiera de las aplicaciones cliente que ha configurado. En el elemento de inserción, el back-end enviará notificaciones a todas las aplicaciones cliente en las que el usuario ha iniciado sesión.

<!-- URLs. -->
[Introducción a la autenticación]: app-service-mobile-ios-get-started-users.md
[Introducción a las notificaciones de inserción]: app-service-mobile-ios-get-started-push.md
[plantillas]: https://msdn.microsoft.com/library/dn530748.aspx
 

<!---HONumber=AcomDC_1210_2015--->