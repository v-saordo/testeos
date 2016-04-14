<properties
	pageTitle="Introducción a Azure Mobile Engagement para iOS en Swift"
	description="Aprenda a usar Azure Mobile Engagement con los análisis y las notificaciones de inserción para aplicaciones iOS."
	services="mobile-engagement"
	documentationCenter="ios"
	authors="piyushjo"
	manager="dwrede"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="swift"
	ms.topic="get-started-article"
	ms.date="02/29/2016"
	ms.author="piyushjo" />

# Introducción a Azure Mobile Engagement para aplicaciones iOS en Swift

> [AZURE.SELECTOR]
- [Windows universal](mobile-engagement-windows-store-dotnet-get-started.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-get-started.md)
- [iOS | Obj C](mobile-engagement-ios-get-started.md)
- [iOS | Swift](mobile-engagement-ios-swift-get-started.md)
- [Android](mobile-engagement-android-get-started.md)
- [Cordova](mobile-engagement-cordova-get-started.md)

En este tema se muestra cómo utilizar Azure Mobile Engagement para conocer el uso de las aplicaciones y enviar notificaciones push a los usuarios segmentados a una aplicación de iOS. En este tutorial, puede crear una aplicación iOS vacía que recopile datos básicos y reciba notificaciones push mediante el sistema de notificaciones push de Apple (APN).

Este tutorial requiere lo siguiente:

+ XCode 6 o XCode 7, que se puede instalar desde la tienda de aplicaciones MAC.
+ [SDK de Mobile Engagement iOS]
+ Certificado de notificaciones push (.p12), que puede obtener en el centro de desarrolladores de Apple.

> [AZURE.NOTE] En este tutorial se usa Swift versión 2.0.

Completar este tutorial es un requisito previo para todos los tutoriales de Mobile Engagement para aplicaciones iOS.

> [AZURE.IMPORTANT] Completar este tutorial es un requisito previo para los demás tutoriales de Mobile Engagement para aplicaciones iOS. Para completarlo, debe tener una cuenta activa de Azure. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fes-ES%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Evaluación gratuita de Azure</a>.

##<a id="setup-azme"></a>Configuración de Mobile Engagement para una aplicación iOS

[AZURE.INCLUDE [Creación de la aplicación de Mobile Engagement en el portal](../../includes/mobile-engagement-create-app-in-portal.md)]

##<a id="connecting-app"></a>Conectar la aplicación al backend de Mobile Engagement

En este tutorial se presenta una "integración básica", que es el conjunto mínimo necesario para recopilar los datos y enviar una notificación de inserción. Toda la información sobre la integración se encuentra en la [Integración del SDK de Mobile Engagement para iOS](../mobile-engagement-ios-sdk-overview/).

Crearemos una aplicación básica con XCode para demostrar la integración:

###Creación de un nuevo proyecto iOS

[AZURE.INCLUDE [Creación de un nuevo proyecto iOS](../../includes/mobile-engagement-create-new-ios-app.md)]

###Conectar la aplicación al back-end de Mobile Engagement

1. Descargue el [SDK de iOS para Mobile Engagement].
2. Extraiga el archivo .tar.gz archivo en una carpeta del equipo.
3. Haga clic con el botón derecho del mouse en el proyecto y elija "Agregar archivos a...".

	![][1]

4. Navegue hasta la carpeta donde extrajo el SDK y seleccione la carpeta `EngagementSDK` y, luego, haga clic en Aceptar.

	![][2]

5. Abra la pestaña `Build Phases` y en el menú `Link Binary With Libraries` agregue los marcos tal como se muestra a continuación. **NOTE** Debe incluir `CoreLocation, CFNetwork, CoreTelephony, and SystemConfiguration`:

	![][3]

6. Para **XCode 7** - agregue `libxml2.tbd` en lugar de `libxml2.dylib`.

7. Cree un encabezado puente para poder usar las API de Objective-C de SDK mediante la selección de Archivo > Nuevo > Archivo > iOS > Origen > Archivo de encabezado.

	![][4]

8. Edite el archivo de encabezado puente para exponer el código Mobile Engagement Objective-C en el código Swift, agregue las importaciones siguientes:

		/* Mobile Engagement Agent */
		#import "AEModule.h"
		#import "AEPushMessage.h"
		#import "AEStorage.h"
		#import "EngagementAgent.h"
		#import "EngagementTableViewController.h"
		#import "EngagementViewController.h"
		#import "AEIdfaProvider.h"

9. En Configuración de compilación, asegúrese de que la compilación del encabezado puente Objective-C en Compilador de Swift - Generación de código tiene una ruta de acceso a este encabezado. Este es un ejemplo de ruta de acceso: **$(SRCROOT)/MySuperApp/MySuperApp-Bridging-Header.h (en función de la ruta de acceso)**

	![][6]

10. Vuelva al portal de Azure en la página *Información de conexión* de la aplicación y copie la cadena de conexión

	![][5]

11. Pegue la cadena de conexión en el delegado `didFinishLaunchingWithOptions`

		func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool
		{
  			[...]
				EngagementAgent.init("Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}")
  			[...]
		}

##<a id="monitor"></a>Habilitar supervisión en tiempo real

Para comenzar a enviar datos y asegurarse de que los usuarios estén activos, debe enviar al menos una pantalla (Actividad) al back-end de Mobile Engagement.

1. Abra el archivo **ViewController.swift** y reemplace la clase base de **ViewController** para que sea **EngagementViewController**:

	`class ViewController : EngagementViewController {`

##<a id="monitor"></a>Conexión de la aplicación con la supervisión en tiempo real

[AZURE.INCLUDE [Conectar la aplicación con la supervisión en tiempo real](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Habilitación de las notificaciones de inserción y mensajería en la aplicación

Mobile Engagement permite interactuar y llegar a los usuarios mediante notificaciones de inserción y mensajería en la aplicación en el contexto de las campañas. Este módulo se denomina REACH en el portal de Mobile Engagement. En las secciones siguientes se instalará la aplicación para recibirlos.

### Habilitación de la aplicación para recibir notificaciones push silenciosas

[AZURE.INCLUDE [mobile-engagement-ios-silent-push](../../includes/mobile-engagement-ios-silent-push.md)]

### Adición de la biblioteca de cobertura al proyecto

1. Haga clic con el botón derecho del mouse en el proyecto.
2. Seleccionar `Add file to ...`
3. Navegue hasta la carpeta donde extrajo el SDK.
4. Seleccione la carpeta `EngagementReach`
5. Haga clic en Agregar
6. Edite el archivo de encabezado puente para exponer encabezados Mobile Engagement Objective-C Reach y agregue las importaciones siguientes:

		/* Mobile Engagement Reach */
		#import "AEAnnouncementViewController.h"
		#import "AEAutorotateView.h"
		#import "AEContentViewController.h"
		#import "AEDefaultAnnouncementViewController.h"
		#import "AEDefaultNotifier.h"
		#import "AEDefaultPollViewController.h"
		#import "AEInteractiveContent.h"
		#import "AENotificationView.h"
		#import "AENotifier.h"
		#import "AEPollViewController.h"
		#import "AEReachAbstractAnnouncement.h"
		#import "AEReachAnnouncement.h"
		#import "AEReachContent.h"
		#import "AEReachDataPush.h"
		#import "AEReachDataPushDelegate.h"
		#import "AEReachModule.h"
		#import "AEReachNotifAnnouncement.h"
		#import "AEReachPoll.h"
		#import "AEReachPollQuestion.h"
		#import "AEViewControllerUtil.h"
		#import "AEWebAnnouncementJsBridge.h"

### Modifique su delegado de la aplicación

1. Dentro de `didFinishLaunchingWithOptions`, cree un módulo de cobertura y páselo a la línea existente de inicialización de Engagement:

		func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool {
			let reach = AEReachModule.moduleWithNotificationIcon(UIImage(named:"icon.png")) as! AEReachModule
			EngagementAgent.init("Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}", modulesArray:[reach])
			[...]
			return true
		}

###Habilite su aplicación para recibir notificaciones push de APN.
1. Agregue la siguiente línea al método `didFinishLaunchingWithOptions`:

		/* Ask user to receive push notifications */
		if #available(iOS 8.0, *)
		{
		   let settings = UIUserNotificationSettings(forTypes: [UIUserNotificationType.Alert, UIUserNotificationType.Badge, UIUserNotificationType.Sound], categories: nil)
		   application.registerUserNotificationSettings(settings)
		   application.registerForRemoteNotifications()
		}
		else
		{
		   application.registerForRemoteNotificationTypes([UIRemoteNotificationType.Alert, UIRemoteNotificationType.Badge, UIRemoteNotificationType.Sound])
		}

2. Agregue el método `didRegisterForRemoteNotificationsWithDeviceToken` de la siguiente forma:

		func application(application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: NSData)
		{
			EngagementAgent.shared().registerDeviceToken(deviceToken)
		}

3. Agregue el método `didReceiveRemoteNotification:fetchCompletionHandler:` de la siguiente forma:

		func application(application: UIApplication, didReceiveRemoteNotification userInfo: [NSObject : AnyObject], fetchCompletionHandler completionHandler: (UIBackgroundFetchResult) -> Void)
		{
			EngagementAgent.shared().applicationDidReceiveRemoteNotification(userInfo, fetchCompletionHandler:completionHandler)
		}

[AZURE.INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- URLs. -->
[SDK de Mobile Engagement iOS]: http://aka.ms/qk2rnj
[SDK de iOS para Mobile Engagement]: http://aka.ms/qk2rnj

<!-- Images. -->
[1]: ./media/mobile-engagement-ios-get-started/xcode-add-files.png
[2]: ./media/mobile-engagement-ios-get-started/xcode-select-engagement-sdk.png
[3]: ./media/mobile-engagement-ios-get-started/xcode-build-phases.png
[4]: ./media/mobile-engagement-ios-swift-get-started/add-header-file.png
[5]: ./media/mobile-engagement-ios-get-started/app-connection-info-page.png
[6]: ./media/mobile-engagement-ios-swift-get-started/add-bridging-header.png

<!---HONumber=AcomDC_0302_2016-->