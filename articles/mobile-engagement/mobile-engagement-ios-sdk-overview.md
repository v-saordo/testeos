<properties
	pageTitle="Información general del SDK de iOS de Azure Mobile Engagement"
	description="Actualizaciones y procedimientos más recientes para el SDK de iOS para Azure Mobile Engagement"
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="dwrede"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="02/29/2016"
	ms.author="MehrdadMzfr" />

#SDK de iOS para Azure Mobile Engagement

Comience aquí a obtener todos los detalles sobre cómo integrar Azure Mobile Engagement en una aplicación de iOS. Si primero quiere probarlo, asegúrese de seguir nuestro [tutorial de 15 minutos](mobile-engagement-ios-get-started.md)

Haga clic para ver el [contenido del SDK](mobile-engagement-ios-sdk-content.md)

##Procedimientos de integración
1. Comience aquí: [Cómo integrar Mobile Engagement en su aplicación de iOS](mobile-engagement-ios-integrate-engagement.md)

2. Para las notificaciones: [Integración de cobertura (notificaciones) en su aplicación iOS](mobile-engagement-ios-integrate-engagement-reach.md)

3. Implementación del plan de etiquetas: [Uso de la API de etiquetado de Mobile Engagement avanzada en su aplicación iOS](mobile-engagement-ios-use-engagement-api.md)


##Notas de la versión

###3\.2.1 (12/11/2015)

-   Se ha corregido el retraso que se produce cuando se desencadena una nueva instancia de aplicación por una notificación con vínculos profundos. 

Para la versión anterior, consulte las [notas de la versión completas](mobile-engagement-ios-release-notes.md)

##Procedimientos de actualización

Si ya integró una versión anterior de Engagement en la aplicación, debería tener en cuenta los siguientes puntos a la hora de actualizar el SDK.

Es posible que tenga que seguir varios procedimientos si se perdió varias versiones del SDK, consulte la sección completa [Procedimientos de actualización](mobile-engagement-ios-upgrade-procedure.md).

Para cada nueva versión del SDK debe reemplazar primero (quitar y volver a importar en xcode) las carpetas EngagementSDK y EngagementReach.

###De 2.0.0 a 3.0.0
Soporte de iOS 4.X eliminado. A partir de esta versión, el destino de implementación de la aplicación debe ser como mínimo iOS 6.

Si usa Reach en la aplicación, debe agregar el valor `remote-notification` a la matriz `UIBackgroundModes` en el archivo Info.plist para recibir notificaciones remotas.

El método `application:didReceiveRemoteNotification:` debe reemplazarse por `application:didReceiveRemoteNotification:fetchCompletionHandler:` en el delegado de aplicación.

"AEPushDelegate.h" es una interfaz desusada y debe quitar todas las referencias. Esto incluye eliminar `[[EngagementAgent shared] setPushDelegate:self]` y los métodos delegados del delegado de la aplicación:

	-(void)willRetrieveLaunchMessage;
	-(void)didFailToRetrieveLaunchMessage;
	-(void)didReceiveLaunchMessage:(AEPushMessage*)launchMessage;

<!---HONumber=AcomDC_0302_2016-->