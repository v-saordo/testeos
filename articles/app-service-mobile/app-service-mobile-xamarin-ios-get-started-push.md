<properties 
	pageTitle="Incorporación de notificaciones push a la aplicación Xamarin iOS con Servicio de aplicaciones de Azure" 
	description="Obtenga información acerca de cómo usar Servicio de aplicaciones de Azure para enviar notificaciones push a su aplicación Xamarin.iOS." 
	services="app-service\mobile" 
	documentationCenter="xamarin" 
	authors="wesmc7777"
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-ios" 
	ms.devlang="dotnet" 
	ms.topic="article"
	ms.date="12/18/2015" 
	ms.author="wesmc"/>

# Incorporación de notificaciones push a la aplicación Xamarin.iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

##Información general

Este tutorial se basa en el [tutorial de inicio rápido de Xamarin.iOS](app-service-mobile-xamarin-ios-get-started.md), que debe completar primero. Agregará notificaciones push al proyecto de inicio rápido de Xamarin.iOS para que cada vez que se inserte un registro se envíe una notificación de inserción. Si no usa el proyecto de servidor de inicio rápido descargado, debe agregar el paquete de extensión de notificaciones push al proyecto. Para obtener más información acerca de los paquetes de extensión de servidor, consulte [Trabajar con el SDK del servidor back-end de .NET para Aplicaciones móviles de Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

##Requisitos previos

Para completar este tutorial, necesitará lo siguiente:

* Una cuenta de Azure activa. Si no dispone de ninguna cuenta, puede registrarse para obtener una versión de evaluación de Azure y conseguir hasta 10 back-ends de aplicaciones móviles gratuitas. Puede seguir usándolas incluso después de que finalice el período de evaluación. Consulte [Evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).

* Un equipo Mac con [Xamarin Studio] y [Xcode] v4.4 o posterior instalado. Puede ejecutar la aplicación Xamarin.iOS mediante Visual Studio en un equipo Windows si lo desea, pero es un poco más complicado porque tiene que conectarse a un equipo Mac en red. Si está interesado en hacerlo, consulte [Installing Xamarin.iOS on Windows] (Instalación de Xamarin.iOS en Windows).

* Un dispositivo iOS físico. El simulador de iOS no admite notificaciones push.

* Se requiere una [suscripción al programa para desarrolladores de Apple](https://developer.apple.com/programs/ios/) para registrarse en el Servicio de notificaciones push de Apple (APNS).

* Complete el [Tutorial de inicio rápido de Xamarin.iOS](app-service-mobile-xamarin-ios-get-started.md).


##Registro de la aplicación para notificaciones de inserción en el portal para desarrolladores de Apple

[AZURE.INCLUDE [Los Centros de notificaciones Xamarin permiten notificaciones push de Apple](../../includes/notification-hubs-xamarin-enable-apple-push-notifications.md)]

##Configuración de aplicaciones móviles para enviar notificaciones push

Con el fin de configurar la aplicación para enviar notificaciones, cree un nuevo centro y configúrelo para los servicios de notificación de plataforma que vaya a usar.

1. En el [Portal de Azure](https://portal.azure.com/), haga clic en **Examinar** > **Aplicaciones móviles** > la aplicación móvil >**Configuración** > **Móvil** > **Insertar** > **Centro de notificaciones** > **+ Centro de notificaciones**, proporcione un nombre y un espacio de nombres para el centro de notificaciones y después haga clic en el botón **Aceptar**.

	![](./media/app-service-mobile-xamarin-ios-get-started-push/mobile-app-configure-notification-hub.png)

2. En la hoja Crear Centro de notificaciones, haga clic en el botón **Crear**.

3. Haga clic en **Insertar** > **Apple (APN)** > **Cargar certificado**. Cargue el archivo de certificado de inserción. p12 que exportó anteriormente. No olvide seleccionar **Espacio aislado** si creó un certificado de inserción de desarrollo para desarrollo y pruebas. De lo contrario, elija **Producción**.

	![](./media/app-service-mobile-xamarin-ios-get-started-push/mobile-app-upload-apns-cert.png)

El servicio ahora está configurado para trabajar con las notificaciones push en iOS.

##Actualización del proyecto de servidor para enviar notificaciones push

[AZURE.INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

##Configuración del proyecto de Xamarin.iOS

[AZURE.INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

##Incorporación de notificaciones de inserción a la aplicación

1. En **QSTodoService**, agregue la siguiente propiedad para que **AppDelegate** pueda adquirir el cliente móvil:
        
            public MobileServiceClient GetClient {
            get
            {
                return client;
            }
            private set
            {
                client = value;
            }
        }

1. Agregue la siguiente instrucción `using` al principio del archivo **AppDelegate.cs**.

		using Microsoft.WindowsAzure.MobileServices;
		using Newtonsoft.Json.Linq;

2. En **AppDelegate**, reemplace el evento **FinishedLaunching**:

        public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
        {
            // registers for push for iOS8
            var settings = UIUserNotificationSettings.GetSettingsForTypes(
                UIUserNotificationType.Alert 
                | UIUserNotificationType.Badge 
                | UIUserNotificationType.Sound, 
                new NSSet());

            UIApplication.SharedApplication.RegisterUserNotificationSettings(settings); 
            UIApplication.SharedApplication.RegisterForRemoteNotifications();

            return true;
        }

3. En el mismo archivo, reemplace el evento **RegisteredForRemoteNotifications**: Con este código va a registrar una notificación de plantilla simple que se enviará a todas las plataformas admitidas por el servidor.
 
	Para más información sobre las plantillas con centros de notificaciones, vea [Plantillas](../notification-hubs/notification-hubs-templates.md).


        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            MobileServiceClient client = QSTodoService.DefaultService.GetClient;

            const string templateBodyAPNS = "{"aps":{"alert":"$(messageParam)"}}";

            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
            {
                {"body", templateBodyAPNS}
            };

            // Register for push with your mobile app
            var push = client.GetPush();
            push.RegisterAsync(deviceToken, templates);
        }


4. A continuación, reemplace el evento **DidReceivedRemoteNotification**:

        public override void DidReceiveRemoteNotification (UIApplication application, NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
        {
            NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;

            string alert = string.Empty;
            if (aps.ContainsKey(new NSString("alert")))
                alert = (aps [new NSString("alert")] as NSString).ToString();

            //show alert
            if (!string.IsNullOrEmpty(alert))
            {
                UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                avAlert.Show();
            }
        }

Ahora su aplicación está actualizada para que sea compatible con las notificaciones push.

## <a name="test"></a>Prueba de las notificaciones push en su aplicación

1. Presione el botón **Ejecutar** para crear el proyecto e iniciar la aplicación en un dispositivo compatible con iOS. A continuación, haga clic en **Aceptar** para aceptar las notificaciones push.
	
	> [AZURE.NOTE] Debe aceptar de forma explícita las notificaciones push desde su aplicación. Esta solicitud solo se produce la primera vez que se ejecuta la aplicación.

2. En la aplicación, escriba una tarea y luego haga clic en el icono de signo de suma (**+**).

3. Compruebe que se ha recibido la notificación y, a continuación, haga clic en **Aceptar** para descartarla.

4. Repita el paso 2 y cierre de inmediato la aplicación. A continuación, compruebe que se muestra una notificación.

Ha completado correctamente este tutorial.

<!-- Images. -->

<!-- URLs. -->
[Xamarin Studio]: http://xamarin.com/platform
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[Installing Xamarin.iOS on Windows]: http://developer.xamarin.com/guides/ios/getting_started/installation/windows/


 

<!---HONumber=AcomDC_0128_2016-->