<properties 
	pageTitle="Envío de notificaciones push a usuarios de aplicaciones universales de Windows autenticados." 
	description="Obtenga información sobre cómo enviar notificaciones de inserción desde Servicios móviles de Azure a usuarios específicos de su aplicación universal C# de Windows." 
	services="mobile-services,notification-hubs" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/11/2015" 
	ms.author="glenga"/>

# Envío de notificaciones de inserción a usuarios autenticados

[AZURE.INCLUDE [mobile-services-selector-push-users](../../includes/mobile-services-selector-push-users.md)]

##Información general
En este tema se muestra cómo enviar notificaciones de inserción a un usuario autenticado en cualquier dispositivo registrado. A diferencia del tutorial anterior [Incorporación de notificaciones de inserción a la aplicación], este tutorial cambia el servicio móvil para solicitar que un usuario se autentique antes de que el cliente pueda registrarse con el Centro de notificaciones para notificaciones push. El registro también se modifica para agregar una etiqueta basada en el identificador del usuario asignado. Por último, el script de servidor se actualiza para enviar la notificación solamente al usuario autenticado en lugar de a todos los registros.

Este tutorial le guiará en el siguiente proceso:

1. [Actualización del servicio para que requiera autenticación para el registro]
2. [Actualización de la aplicación para que inicie sesión antes del registro]
3. [Prueba de la aplicación]
 
Este tutorial es válido para aplicaciones de la Tienda Windows y la Tienda de Windows Phone.

##Requisitos previos 

Antes de comenzar este tutorial, debe haber realizado los siguientes tutoriales de Servicios móviles:

+ [Incorporación de autenticación a su aplicación]<br/>Agrega un requisito de inicio de sesión a la aplicación de ejemplo TodoList.

+ [Incorporación de notificaciones de inserción a la aplicación]<br/>Configura la aplicación de ejemplo TodoList para notificaciones push mediante los Centros de notificaciones.

Una vez que haya realizado ambos tutoriales, puede impedir que usuarios no autorizados se registren para notificaciones de inserción desde su servicio móvil.

##<a name="register"></a>Actualización del servicio para solicitar autenticación para registro

[AZURE.INCLUDE [mobile-services-javascript-backend-push-notifications-app-users](../../includes/mobile-services-javascript-backend-push-notifications-app-users.md)]

&nbsp;&nbsp;5. Reemplace la función de inserción por el siguiente código y, a continuación, haga clic en **Guardar**:

	function insert(item, user, request) {
    // Define a payload for the Windows Store toast notification.
    var payload = '<?xml version="1.0" encoding="utf-8"?><toast><visual>' +    
    '<binding template="ToastText01"><text id="1">' +
    item.text + '</text></binding></visual></toast>';

    // Get the ID of the logged-in user.
    var userId = user.userId;		

    request.execute({
        success: function() {
            // If the insert succeeds, send a notification to all devices 
	    	// registered to the logged-in user as a tag.
            	push.wns.send(userId, payload, 'wns/toast', {
                success: function(pushResponse) {
                    console.log("Sent push:", pushResponse);
	    			request.respond();
                    },              
                    error: function (pushResponse) {
                            console.log("Error Sending push:", pushResponse);
	    				request.respond(500, { error: pushResponse });
                        }
                    });
                }
            });
	}

&nbsp;&nbsp;Este script de inserción usa la etiqueta del identificador de usuario para enviar una notificación push (con el texto del elemento insertado) a todos los registros de aplicaciones de la Tienda Windows creados por el usuario que ha iniciado sesión.

##<a name="update-app"></a>Actualización de la aplicación para iniciar sesión antes del registro

[AZURE.INCLUDE [mobile-services-windows-store-dotnet-push-notifications-app-users](../../includes/mobile-services-windows-store-dotnet-push-notifications-app-users.md)]

##<a name="test"></a>Prueba de la aplicación

[AZURE.INCLUDE [mobile-services-windows-test-push-users](../../includes/mobile-services-windows-test-push-users.md)]

<!-- Anchors. -->
[Actualización del servicio para que requiera autenticación para el registro]: #register
[Actualización de la aplicación para que inicie sesión antes del registro]: #update-app
[Prueba de la aplicación]: #test
[Next Steps]: #next-steps


<!-- URLs. -->
[Incorporación de autenticación a su aplicación]: ../mobile-services-windows-store-dotnet-get-started-users.md
[Incorporación de notificaciones de inserción a la aplicación]: ../mobile-services-javascript-backend-windows-store-dotnet-get-started-push.md

<!---HONumber=AcomDC_1203_2015-->