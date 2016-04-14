<properties
	pageTitle="Procedimiento de actualización del SDK de iOs de Azure Mobile Engagement"
	description="Actualizaciones y procedimientos más recientes para el SDK de iOS para Azure Mobile Engagement"
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="MehrdadMzfr"
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

#Procedimientos de actualización

Si ya integró una versión anterior de Engagement en la aplicación, debería tener en cuenta los siguientes puntos a la hora de actualizar el SDK.

Para cada nueva versión del SDK debe reemplazar primero (quitar y volver a importar en xcode) las carpetas EngagementSDK y EngagementReach.

##De 2.0.0 a 3.0.0
Soporte de iOS 4.X eliminado. A partir de esta versión, el destino de implementación de la aplicación debe ser como mínimo iOS 6.

Si usa Reach en la aplicación, debe agregar el valor `remote-notification` a la matriz `UIBackgroundModes` en el archivo Info.plist para recibir notificaciones remotas.

El método `application:didReceiveRemoteNotification:` debe reemplazarse por `application:didReceiveRemoteNotification:fetchCompletionHandler:` en el delegado de aplicación.

"AEPushDelegate.h" es una interfaz desusada y debe quitar todas las referencias. Esto incluye eliminar `[[EngagementAgent shared] setPushDelegate:self]` y los métodos delegados del delegado de la aplicación:

	-(void)willRetrieveLaunchMessage;
	-(void)didFailToRetrieveLaunchMessage;
	-(void)didReceiveLaunchMessage:(AEPushMessage*)launchMessage;

##De 1.16.0 a 2.0.0
A continuación se describe cómo migrar una integración del SDK desde el servicio Capptain que ofrece Capptain SAS en una aplicación con la tecnología de Azure Mobile Engagement. Si va a migrar desde una versión anterior, consulte el sitio web de Capptain para migrar a 1.16 en primer lugar luego aplique el siguiente procedimiento.

>[Azure.IMPORTANT] Capptain y Mobile Engagement no son los mismos servicios, y el procedimiento que se indica a continuación destaca únicamente cómo migrar la aplicación cliente. La migración del SDK en la aplicación NO migrará los datos desde los servidores Capptain a los servidores Mobile Engagement.

### Agente

El método `registerApp:` se ha reemplazado por el nuevo método `init:`. El delegado de la aplicación deben actualizarse como corresponda y usar una cadena de conexión:

			- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
			{
			  [...]
			  [EngagementAgent init:@"YOUR_CONNECTION_STRING"];
			  [...]
			}

Seguimiento de SmartAd se ha quitado del SDK y solo tiene que quitar todas las instancias de la clase `AETrackModule`

### Cambios del nombre de la clase

Como parte de la reconfiguración de marca, hay algunos nombres de clase/archivo que deben cambiarse.

Todas las clases precedidas de "CP" cambian su nombre introduciendo el prefijo "AE".

Ejemplo:

-   `CPModule.h` cambia su nombre a `AEModule.h`.

Todas las clases con el prefijo "Capptain" cambian su nombre para introducir el prefijo "Engagement".

Ejemplos:

-   La clase `CapptainAgent` cambia su nombre a `EngagementAgent`.
-   La clase `CapptainTableViewController` cambia su nombre a `EngagementTableViewController`.
-   La clase `CapptainUtils` cambia su nombre a `EngagementUtils`.
-   La clase `CapptainViewController` cambia su nombre a `EngagementViewController`.

<!---HONumber=AcomDC_0302_2016-->