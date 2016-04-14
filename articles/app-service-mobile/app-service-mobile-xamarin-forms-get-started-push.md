<properties
	pageTitle="Incorporación de notificaciones push a la aplicación Xamarin.Forms con el Servicio de aplicaciones de Azure"
	description="Obtenga información acerca de cómo usar el Servicio de aplicaciones de Azure para enviar notificaciones push a su aplicación Xamarin.Forms."
	services="app-service\mobile"
	documentationCenter="xamarin"
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="02/04/2016"
	ms.author="wesmc"/>

# Incorporación de notificaciones push a la aplicación de Xamarin.Forms

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

##Información general

Este tutorial se basa en el tutorial de inicio rápido [Creación de una aplicación Xamarin. Forms](app-service-mobile-xamarin-forms-get-started.md), que debe completar primero. Tendrá que agregar soporte a las notificaciones push que desea admitir en el proyecto de inicio rápido de Xamarin.Forms. Cada vez que se inserte un registro, se enviará una notificación push.

Si no usa el proyecto de servidor de inicio rápido descargado, debe agregar el paquete de extensión de notificaciones push al proyecto. Para obtener más información acerca de los paquetes de extensión de servidor, consulte [Trabajar con el SDK del servidor back-end de .NET para Aplicaciones móviles de Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

El [simulador de iOS no admite notificaciones push](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html), por lo que debe usar un dispositivo iOS físico. También tendrá que suscribirse como [miembro del programa para desarrolladores de Apple](https://developer.apple.com/programs/ios/).

##Requisitos previos

Para completar este tutorial, necesitará lo siguiente:

* Una cuenta de Azure activa. Si no dispone de ninguna cuenta, puede registrarse para obtener una versión de evaluación de Azure y conseguir hasta 10 aplicaciones móviles gratuitas. Puede seguir usándolas incluso después de que finalice el período de evaluación. Consulte [Evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).

* Un equipo Mac con [Xamarin Studio] y [Xcode] v4.4 o posterior instalado. Puede ejecutar la aplicación Xamarin.Forms mediante Visual Studio en un equipo Windows si lo desea, pero es un poco más complicado porque tiene que conectarse a un equipo Mac en red con el host de compilación Xamarin.iOS. Si está interesado en hacerlo, consulte [Installing Xamarin.iOS on Windows]

* Un dispositivo iOS físico. El simulador de iOS no admite notificaciones push.

* Complete el tutorial de inicio rápido [Creación de una aplicación Xamarin.Forms](app-service-mobile-xamarin-forms-get-started.md).

##Creación de un centro de notificaciones para su aplicación móvil

Con el fin de configurar la aplicación para enviar notificaciones, cree un nuevo centro de notificaciones y configúrelo para los servicios de notificación de plataforma que vaya a usar.

Estos pasos le guiarán para crear un nuevo centro de notificaciones. Si ya creó uno, puede simplemente seleccionarlo.

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/). Haga clic en **Examinar** > **Aplicaciones móviles** > su back-end > **Configuración** > **Móvil** > **Insertar** > **Centro de notificaciones** > **+ Centro de notificaciones**, proporcione un nombre y un espacio de nombres para el centro de notificaciones y después haga clic en el botón **Aceptar**.

	![](./media/app-service-mobile-xamarin-ios-get-started-push/mobile-app-configure-notification-hub.png)

2. En la hoja Crear Centro de notificaciones, haga clic en el botón **Crear**.


##Actualización del proyecto de servidor para enviar notificaciones push

[AZURE.INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]


##(Opcional) Configuración y ejecución del proyecto de Android

Esta sección trata de la ejecución del proyecto de Android de Xamarin para Android. Puede omitir esta sección si no está trabajando con dispositivos Android.


####Habilitación del servicio de mensajería en la nube de Google (GCM)


[AZURE.INCLUDE [mobile-services-enable-google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]


####Creación y configuración del Centro de notificaciones para GCM

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/). Haga clic en **Examinar** > **Aplicaciones móviles** > su aplicación móvil > **Configuración** > **Insertar** > **Google (GCM)**. Pegue la clave de API de servidor que creó anteriormente y haga clic en **Guardar**. El servicio ahora está configurado para trabajar con las notificaciones push en Android.

	![](./media/app-service-mobile-xamarin-forms-get-started-push/mobile-app-save-gcm-api-key.png)


####Incorporación de notificaciones push al proyecto droid

1. Haga clic con el botón derecho en la carpeta Componentes, haga clic en Obtener más componentes..., busque el componente **Servicio de mensajería en la nube de Google** y agréguelo al proyecto. Este componente ayuda a simplificar el trabajo con las notificaciones push con un proyecto Xamarrin Android.

2. Abra el archivo de proyecto MainActivity.cs y agregue la siguiente instrucción using al principio del archivo:

		using Gcm.Client;

3. Agregue el siguiente código al método `OnCreate` después de la llamada a `LoadApplication`.

	    try
	    {
	        // Check to ensure everything's setup right
	        GcmClient.CheckDevice(this);
	        GcmClient.CheckManifest(this);

	        // Register for push notifications
	        System.Diagnostics.Debug.WriteLine("Registering...");
	        GcmClient.Register(this, PushHandlerBroadcastReceiver.SENDER_IDS);
	    }
	    catch (Java.Net.MalformedURLException)
	    {
	        CreateAndShowDialog("There was an error creating the Mobile Service. Verify the URL", "Error");
	    }
	    catch (Exception e)
	    {
	        CreateAndShowDialog(e.Message, "Error");
	    }


4. Agregue el siguiente código para el método auxiliar `CreateAndShowDialog`.

		private void CreateAndShowDialog(String message, String title)
		{
			AlertDialog.Builder builder = new AlertDialog.Builder(this);

			builder.SetMessage (message);
			builder.SetTitle (title);
			builder.Create().Show ();
		}


5. En la clase `MainActivity`, agregue el código siguiente para exponer el elemento `MainActivity` actual para que se pueda ejecutar alguna IU en el subproceso de interfaz de usuario principal:

		// Create a new instance field for this activity.
		static MainActivity instance = null;

		// Return the current activity instance.
		public static MainActivity CurrentActivity
		{
		    get
		    {
		        return instance;
		    }
		}

6. Inicialice la variable `instance` al principio del método `MainActivity.OnCreate`.

		// Set the current instance of MainActivity.
		instance = this;

7. Agregue un nuevo archivo de clase al proyecto **Droid**. Asigne como nombre del nuevo archivo de clase **GcmService**.

8. Asegúrese de que las siguientes instrucciones `using` se incluyen al principio del archivo.

		using Gcm.Client;
		using Microsoft.WindowsAzure.MobileServices;
		using Android.App;
		using Android.Content;
		using System.Collections.Generic;
		using System.Diagnostics;
		using Android.Util;
		using Newtonsoft.Json.Linq;
		using System.Text;
		using System.Linq;

9. Agregue las siguientes solicitudes de permiso al principio del archivo, después de las instrucciones `using` y antes de la declaración `namespace`.

		[assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
		[assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
		[assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]

		//GET_ACCOUNTS is only needed for android versions 4.0.3 and below
		[assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]
		[assembly: UsesPermission(Name = "android.permission.INTERNET")]
		[assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]

10. Agregue la siguiente definición de clase al espacio de nombres. Reemplace **<PROJECT_NUMBER>** por el número de proyecto que anotó anteriormente.

		[BroadcastReceiver(Permission = Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
		[IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE }, Categories = new string[] { "@PACKAGE_NAME@" })]
		[IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK }, Categories = new string[] { "@PACKAGE_NAME@" })]
		[IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY }, Categories = new string[] { "@PACKAGE_NAME@" })]
		public class PushHandlerBroadcastReceiver : GcmBroadcastReceiverBase<GcmService>
		{
		    public static string[] SENDER_IDS = new string[] { "<PROJECT_NUMBER>" };
		}

11. Actualice la clase `GcmService` para usar el nuevo receptor de difusión.

		 [Service]
		 public class GcmService : GcmServiceBase
		 {
		     public static string RegistrationID { get; private set; }

		     public GcmService()
		         : base(PushHandlerBroadcastReceiver.SENDER_IDS){}
		 }


12. Agregue el código siguiente a la clase GcmService que reemplaza al controlador de eventos OnRegistered e implementa un método `Register`.

	Este código registrará un cuerpo de plantilla para recibir notificaciones de plantilla con el parámetro `messageParam`. Las notificaciones de plantilla permiten enviar notificaciones multiplataforma. Para más información, vea [Plantillas](https://msdn.microsoft.com/library/azure/dn530748.aspx).

		protected override void OnRegistered(Context context, string registrationId)
		{
		    Log.Verbose("PushHandlerBroadcastReceiver", "GCM Registered: " + registrationId);
		    RegistrationID = registrationId;

		    createNotification("GcmService Registered...", "The device has been Registered, Tap to View!");

            var push = TodoItemManager.DefaultManager.CurrentClient.GetPush();

		    MainActivity.CurrentActivity.RunOnUiThread(() => Register(push, null));

		}

        public async void Register(Microsoft.WindowsAzure.MobileServices.Push push, IEnumerable<string> tags)
        {
            try
            {
                const string templateBodyGCM = "{"data":{"message":"$(messageParam)"}}";

                JObject templates = new JObject();
                templates["genericMessage"] = new JObject
                {
                  {"body", templateBodyGCM}
                };

                await push.RegisterAsync(RegistrationID, templates);
                Log.Info("Push Installation Id", push.InstallationId.ToString());
            }
            catch (Exception ex)
            {
                System.Diagnostics.Debug.WriteLine(ex.Message);
                Debugger.Break();
            }
        }

13. Tiene que implementar `OnMessage` para controlar una notificación push entrante. En este código controlaremos la notificación y la enviaremos al administrador de notificaciones.

		protected override void OnMessage(Context context, Intent intent)
		{
		    Log.Info("PushHandlerBroadcastReceiver", "GCM Message Received!");

		    var msg = new StringBuilder();

		    if (intent != null && intent.Extras != null)
		    {
		        foreach (var key in intent.Extras.KeySet())
		            msg.AppendLine(key + "=" + intent.Extras.Get(key).ToString());
		    }

		    //Store the message
		    var prefs = GetSharedPreferences(context.PackageName, FileCreationMode.Private);
		    var edit = prefs.Edit();
		    edit.PutString("last_msg", msg.ToString());
		    edit.Commit();

		    string message = intent.Extras.GetString("message");
		    if (!string.IsNullOrEmpty(message))
		    {
		        createNotification("New todo item!", "Todo item: " + message);
		        return;
		    }

		    string msg2 = intent.Extras.GetString("msg");
		    if (!string.IsNullOrEmpty(msg2))
		    {
		        createNotification("New hub message!", msg2);
		        return;
		    }

		    createNotification("Unknown message details", msg.ToString());
		}

		void createNotification(string title, string desc)
		{
		    //Create notification
		    var notificationManager = GetSystemService(Context.NotificationService) as NotificationManager;

		    //Create an intent to show ui
		    var uiIntent = new Intent(this, typeof(MainActivity));

		    //Create the notification
		    var notification = new Notification(Android.Resource.Drawable.SymActionEmail, title);

		    //Auto cancel will remove the notification once the user touches it
		    notification.Flags = NotificationFlags.AutoCancel;

		    //Set the notification info
		    //we use the pending intent, passing our ui intent over which will get called
		    //when the notification is tapped.
		    notification.SetLatestEventInfo(this, title, desc, PendingIntent.GetActivity(this, 0, uiIntent, 0));

		    //Show the notification
		    notificationManager.Notify(1, notification);
		}

14. También tiene que implementar los controladores `OnUnRegistered` y `OnError` para el receptor.

		protected override void OnUnRegistered(Context context, string registrationId)
		{
			Log.Error("PushHandlerBroadcastReceiver", "Unregistered RegisterationId : " + registrationId);
		}

		protected override void OnError(Context context, string errorId)
		{
			Log.Error("PushHandlerBroadcastReceiver", "GCM Error: " + errorId);
		}



####Prueba de las notificaciones push en la aplicación de Android

1. En Visual Studio o Xamarin Studio, haga clic con el botón secundario en el proyecto **droid** y haga clic en **Establecer como proyecto de inicio**.

2. Presione el botón **Ejecutar** para crear el proyecto e iniciar la aplicación en un dispositivo compatible con iOS. A continuación, haga clic en **Aceptar** para aceptar las notificaciones push.

	> [AZURE.NOTE] Debe aceptar de forma explícita las notificaciones push desde su aplicación. Esta solicitud solo se produce la primera vez que se ejecuta la aplicación.

2. En la aplicación, escriba una tarea y luego haga clic en el icono de signo de suma (**+**).

3. Compruebe que se ha recibido la notificación y, a continuación, haga clic en **Aceptar** para descartarla.





##(Opcional) Configuración y ejecución del proyecto de iOS

Esta sección trata de la ejecución del proyecto de iOS de Xamarin para dispositivos iOS. Puede omitir esta sección si no está trabajando con dispositivos iOS.

[AZURE.INCLUDE [Los Centros de notificaciones Xamarin permiten notificaciones push de Apple](../../includes/notification-hubs-xamarin-enable-apple-push-notifications.md)]


####Configuración del Centro de notificaciones para APNS

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/). Haga clic en **Examinar** > **Aplicaciones móviles** > su aplicación móvil > **Configuración** > **Insertar** > **Apple (APNS)** > **Cargar certificado**. Cargue el archivo del certificado de inserción. p12 que exportó anteriormente. No olvide seleccionar **Espacio aislado** si creó un certificado de inserción de desarrollo para desarrollo y pruebas. De lo contrario, elija **Producción**. El servicio está configurado ahora para trabajar con las notificaciones push en iOS.

	![](./media/app-service-mobile-xamarin-ios-get-started-push/mobile-app-upload-apns-cert.png)


	A continuación realizará la configuración del proyecto de iOS en Xamarin Studio o Visual Studio.

[AZURE.INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]


####Incorporación de notificaciones push a la aplicación iOS

1. Agregue la siguiente instrucción `using` al principio del archivo **AppDelegate.cs**.

        using Microsoft.WindowsAzure.MobileServices;
		using Newtonsoft.Json.Linq;


2. En el proyecto de iOS, abra el archivo AppDelegate.cs y actualice `FinishedLaunching` para admitir notificaciones remotas como se indica a continuación.

		public override bool FinishedLaunching (UIApplication app, NSDictionary options)
		{
			global::Xamarin.Forms.Forms.Init ();

			Microsoft.WindowsAzure.MobileServices.CurrentPlatform.Init();

            // IMPORTANT: uncomment this code to enable sync on Xamarin.iOS
            // For more information, see: http://go.microsoft.com/fwlink/?LinkId=620342
            //SQLitePCL.CurrentPlatform.Init();

            // registers for push for iOS8
            var settings = UIUserNotificationSettings.GetSettingsForTypes(
                UIUserNotificationType.Alert
                | UIUserNotificationType.Badge
                | UIUserNotificationType.Sound,
                new NSSet());

            UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
            UIApplication.SharedApplication.RegisterForRemoteNotifications();

			LoadApplication (new App ());

			return base.FinishedLaunching (app, options);
		}


4. En el archivo AppDelegate.cs, agregue también una invalidación para el evento **RegisteredForRemoteNotifications** a fin de registrar las notificaciones:

        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            const string templateBodyAPNS = "{"aps":{"alert":"$(messageParam)"}}";

            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
                {
                  {"body", templateBodyAPNS}
                };

            // Register for push with your mobile app
            Push push = TodoItemManager.DefaultManager.CurrentClient.GetPush();
            push.RegisterAsync(deviceToken, templates);
        }

5. En el archivo AppDelegate.cs, agregue también una invalidación para el evento **DidReceivedRemoteNotification** a fin de controlar las notificaciones entrantes mientras se ejecuta la aplicación:

        public override void DidReceiveRemoteNotification(UIApplication application, NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
        {
            NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;

            string alert = string.Empty;
            if (aps.ContainsKey(new NSString("alert")))
                alert = (aps[new NSString("alert")] as NSString).ToString();

            //show alert
            if (!string.IsNullOrEmpty(alert))
            {
                UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                avAlert.Show();
            }
        }

Ahora su aplicación está actualizada para que sea compatible con las notificaciones push.

####Prueba de las notificaciones push en su aplicación de iOS

1. Haga clic con el botón derecho en el proyecto de iOS y haga clic en **Establecer como proyecto de inicio**.

2. Presione el botón **Ejecutar** o **F5** en Visual Studio para compilar el proyecto e iniciar la aplicación en un dispositivo compatible con iOS. Luego haga clic en **Aceptar** para aceptar las notificaciones push.

	> [AZURE.NOTE] Debe aceptar de forma explícita las notificaciones push desde su aplicación. Esta solicitud solo se produce la primera vez que se ejecuta la aplicación.

3. En la aplicación, escriba una tarea y luego haga clic en el icono de signo de suma (**+**).

4. Compruebe que se ha recibido la notificación y, a continuación, haga clic en **Aceptar** para descartarla.




##(Opcional) Configuración y ejecución del proyecto de Windows

Esta sección trata de la ejecución del proyecto de WinApp de Xamarin para dispositivos Windows. Puede omitir esta sección si no está trabajando con dispositivos Windows.


####Registro de la aplicación de Windows para notificaciones push con WNS

[AZURE.INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]


####Configuración del Centro de notificaciones para WNS

[AZURE.INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]


####Incorporación de notificaciones push a la aplicación de Windows

1. En Visual Studio, abra el archivo **App.xaml.cs** en el proyecto **WinApp**. Agregue las instrucciones `using` a continuación.

		using System.Threading.Tasks;
		using Windows.Networking.PushNotifications;
		using WesmcMobileAppGaTest;
		using Microsoft.WindowsAzure.MobileServices;
		using Newtonsoft.Json.Linq;

2. En el archivo App.xaml.cs, agregue el método `InitNotificationsAsync` siguiente. Este método obtiene el canal de notificación push y registra una plantilla para recibir notificaciones de plantilla desde el Centro de notificaciones. Se entregará a este cliente una notificación de plantilla que admita `messageParam`.

        private async Task InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            const string templateBodyWNS = "<toast><visual><binding template="ToastText01"><text id="1">$(messageParam)</text></binding></visual></toast>";

            JObject headers = new JObject();
            headers["X-WNS-Type"] = "wns/toast";

            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
                {
                  {"body", templateBodyWNS},
                  {"headers", headers} // Only needed for WNS & MPNS
                };

            await TodoItemManager.DefaultManager.CurrentClient.GetPush().RegisterAsync(channel.Uri, templates);
        }

3. En App.xaml.cs actualice el controlador de eventos `OnLaunched` con el atributo `async` y llame a `InitNotificationsAsync`

        protected async override void OnLaunched(LaunchActivatedEventArgs e)
        {
            Frame rootFrame = Window.Current.Content as Frame;

            // Do not repeat app initialization when the Window already has content,
            // just ensure that the window is active
            if (rootFrame == null)
            {
                // Create a Frame to act as the navigation context and navigate to the first page
                rootFrame = new Frame();
                // Set the default language
                rootFrame.Language = Windows.Globalization.ApplicationLanguages.Languages[0];
                rootFrame.NavigationFailed += OnNavigationFailed;
                Xamarin.Forms.Forms.Init(e);
                if (e.PreviousExecutionState == ApplicationExecutionState.Terminated)
                {
                    //TODO: Load state from previously suspended application
                }
                // Place the frame in the current Window
                Window.Current.Content = rootFrame;
            }

            if (rootFrame.Content == null)
            {
                // When the navigation stack isn't restored navigate to the first page,
                // configuring the new page by passing required information as a navigation
                // parameter
                rootFrame.Navigate(typeof(MainPage), e.Arguments);
            }
            // Ensure the current window is active
            Window.Current.Activate();

            await InitNotificationsAsync();
        }

4. En el Explorador de soluciones de Visual Studio, abra el archivo **Package.appxmanifest** y establezca **Capacidad de aviso** en **Sí** en **Notificaciones**.

5. Compile la aplicación y compruebe que no haya errores. Ahora debe registrar la aplicación cliente para las notificaciones de plantilla desde el back-end de aplicación móvil.


####Prueba de las notificaciones push en su aplicación de Windows

1. En Visual Studio, haga clic con el botón secundario en el proyecto de **WinApp** y haga clic en **Establecer como proyecto de inicio**.


2. Presione el botón **Ejecutar** para crear el proyecto e iniciar la aplicación en un dispositivo compatible con iOS. A continuación, haga clic en **Aceptar** para aceptar las notificaciones push.

	> [AZURE.NOTE] Debe aceptar de forma explícita las notificaciones push desde su aplicación. Esta solicitud solo se produce la primera vez que se ejecuta la aplicación.

3. En la aplicación, escriba una tarea y luego haga clic en el icono de signo de suma (**+**).

4. Compruebe que se ha recibido la notificación y, a continuación, haga clic en **Aceptar** para descartarla.



<!-- Images. -->

<!-- URLs. -->
[Xamarin Studio]: http://xamarin.com/platform
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[Installing Xamarin.iOS on Windows]: http://developer.xamarin.com/guides/ios/getting_started/installation/windows/
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333

<!---HONumber=AcomDC_0211_2016-->