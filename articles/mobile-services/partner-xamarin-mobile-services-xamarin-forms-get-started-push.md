 <properties
	pageTitle="Incorporación de notificaciones push a la aplicación de Servicios móviles (Xamarin.Forms) - Servicios móviles"
	description="Obtenga información acerca de cómo usar notificaciones push en aplicaciones Xamarin.Forms con Servicios móviles de Azure."
	documentationCenter="xamarin"
	authors="wesmc7777"
	manager="dwrede"
	services="mobile-services"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.tgt_pltfrm="mobile-xamarin"
	ms.workload="mobile"
	ms.date="01/22/2016"
	ms.author="wesmc"/>

# Incorporación de notificaciones push a la aplicación de Xamarin.Forms

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../../includes/mobile-services-selector-get-started-push.md)]

##Información general

Este tutorial muestra cómo utilizar los Servicios móviles de Azure para enviar notificaciones push a iOS, Android y la aplicación de Windows Phone de la solución Xamarin.Forms. Comience por crear un servicio móvil. A continuación, tendrá que descargar un ejemplo de inicio, registrarse con los servicios de notificación push adecuados y agregar código a la solución para recibir notificaciones de esos servicios.

Al completar este tutorial, el servicio móvil le enviará una notificación push cada vez que un usuario agrega una tarea en una de las aplicaciones. Puede encontrar este ejemplo terminado: [ejemplo de notificación push de Xamarin.Forms Azure completada].

Este tutorial requiere lo siguiente:

+ Un dispositivo iOS 8 (no se pueden probar las notificaciones de inserción en el simulador de iOS)
+ Pertenencia al programa para desarrolladores de iOS
+ [Xamarin.iOS Studio]
+ [Componente de Servicios móviles de Azure]
+ Una cuenta de Google activa
+ [Componente cliente Servicio de mensajería en la nube de Google] Agregará este componente durante este tutorial.

En este tema:

1. [Creación de un servicio móvil nuevo](#create-service)
2. [Descarga y configuración del ejemplo de inicio](#download-starter-sample)
4. [Incorporación de notificaciones push a la aplicación de Xamarin.Forms.iOS](#iOS)
5. [Incorporación de notificaciones push a la aplicación de Xamarin.Forms.Android](#Android)
6. [Incorporación de notificaciones push a la aplicación de Xamarin.Forms.Windows](#Windows)
7. [Actualizar el script de inserción de la tabla de Azure para enviar notificaciones push a todas las aplicaciones](#all-apps)

## <a name="create-service"> </a>Creación de un servicio móvil

[AZURE.INCLUDE [mobile-services-create-new-service-data](../../includes/mobile-services-create-new-service-data.md)]

Para poder almacenar datos de aplicaciones en el nuevo servicio móvil, primero debe crear una tabla.

1. En el Portal de Azure clásico, haga clic en **Servicios móviles** y luego en el servicio móvil que acaba de crear.

2. Haga clic en la pestaña **Data** y, a continuación, en **+Create**.

    ![][123]

   	Se muestra el cuadro de diálogo **Crear nueva tabla**.

3. En **Nombre de tabla** escriba _TodoItem_ y haga clic en el botón de comprobación.

    ![][124]

  	Con esto se crea una tabla de almacenamiento **TodoItem** con el conjunto de permisos predeterminado, lo que significa que cualquier usuario de la aplicación podrá acceder a los datos de la tabla y modificarlos.

    > [AZURE.NOTE] El mismo nombre de tabla se utiliza en la guía de inicio rápido de Servicios móviles. Sin embargo, cada tabla se crea en un esquema que es específico de un servicio móvil determinado. De esta manera, se evita que colisionen los datos cuando varios servicios móviles utilizan la misma base de datos.

4. Haga clic en la nueva tabla **TodoItem** y compruebe que no haya filas de datos.

5. Haga clic en la pestaña **Columnas** y compruebe que solo hay una columna **id**, que se crea automáticamente.

  	Este es el requisito mínimo para una tabla en Servicios móviles.

    > [AZURE.NOTE] Cuando está habilitado el esquema dinámico en su servicio móvil, se crean columnas automáticamente cuando se envían objetos JSON al servicio móvil mediante una operación de inserción o de actualización.

Ahora ya está listo para utilizar el nuevo servicio móvil como almacenamiento de datos para la aplicación.

## <a name="download-starter-sample"></a>Descarga y configuración del ejemplo de inicio
Agregaremos notificaciones push a un ejemplo existente.

1. Descargue el ejemplo siguiente: [ejemplo de inicio de notificación push de Xamarin.Forms Azure].

2. En el [Portal de Azure clásico], haga clic en **Servicios móviles** y elija el servicio móvil. Haga clic en la pestaña **Panel** y anote el valor de la dirección **URL del sitio**. A continuación, haga clic en **Administrar claves** y tome nota de la **Clave de la aplicación**. Necesitará estos valores para obtener acceso al servicio móvil desde su código de aplicación.

3. En el proyecto **ToDoAzure(Portable)** de la solución, abra el archivo **Constants.cs**, reemplace `ApplicationURL` y `ApplicationKey` con la dirección URL del sitio y la clave de la aplicación que obtuvo en el paso anterior.

## <a name="iOS"></a>Incorporación de notificaciones push a la aplicación de Xamarin.Forms.iOS

Agregue notificaciones push a la aplicación de iOS con el Servicio de notificaciones push de Apple (APNS). Necesita una cuenta de Google activa y el [Componente del Cliente del Servicio de mensajería en la nube de Google].

>[AZURE.IMPORTANT] Debido a los requisitos del Servicio de notificaciones push de Apple (APNS), tiene que implementar y realizar una prueba de las notificaciones push en un dispositivo compatible con iOS (iPhone o iPad) en lugar de hacerlo en el emulador.

APNS usa certificados para autenticar el servicio móvil. Siga estas instrucciones para crear los certificados necesarios y cargarlos en su servicio móvil. Para consultar la documentación oficial de la característica APNS, consulte [Servicio de notificaciones push de Apple].

### <a name="certificates"></a>Generación del archivo de solicitud de firma de certificado

Primero debe generar el archivo de solicitud de firma de certificado (CSR) que Apple usa para generar un certificado firmado.

1. En Utilidades, ejecute la **herramienta de acceso a llaves**.

2. Haga clic en **Keychain Access** (Acceso a llaves), expanda **Certificate Assistant**, (Asistente para certificados) y, a continuación, haga clic en **Request a Certificate from a Certificate Authority...** (Solicitar un certificado de una entidad de certificación...).

    ![][5]

3. Especifique la **dirección de correo electrónico de usuario**, escriba un **valor de nombre común**, asegúrese de que se ha seleccionado la opción de **guardado en el disco** y, a continuación, haga clic en **Continuar**.

    ![][6]

4. Escriba un nombre para el archivo de solicitud de firma de certificado (CSR) en **Save As** (Guardar como), seleccione la ubicación en **Where** (Dónde) y, a continuación, haga clic en **Save** (Guardar).

    ![][7]

    Recuerde la ubicación que elija.

A continuación, se registrará la aplicación en Apple, se habilitarán las notificaciones de inserción y se cargará este archivo CSR exportado para crear un certificado de inserción.

### <a name="register"></a>Registro de la aplicación para notificaciones de inserción

Para poder enviar notificaciones de inserción a una aplicación iOS desde servicios móviles, debe registrar su aplicación con Apple y registrar las notificaciones de inserción.

1. Si aún no ha registrado su aplicación, diríjase al <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">portal de aprovisionamiento de iOS</a> del Centro para desarrolladores de Apple, inicie sesión con su identificador de Apple, haga clic en **Identifiers** (Identificadores) y luego en **App IDs** (Identificadores de aplicación). Para finalizar, haga clic en el signo **+** para registrar una nueva aplicación.

    ![][102]

2. Escriba un nombre para la aplicación en **Descripción**, escriba y recuerde el **Identificador de la agrupación de trabajos**, active la opción "Notificaciones push" en la sección "Servicios de aplicaciones" y, a continuación, haga clic en **Continuar**. En este ejemplo se usa el identificador **MobileServices.Quickstart** pero es posible que no pueda volver a usar ese mismo identificador, ya que los identificadores de aplicaciones deben ser exclusivos entre todos los usuarios. Por ese motivo, es recomendable que agregue su nombre completo o sus iniciales después del nombre de la aplicación.

    ![][103]

    De esta forma, se genera el identificador de la aplicación y se solicita que **envíe la información**. Haga clic en **Enviar**.

    ![][104]

    Una vez que haga clic en **Submit** (Enviar), verá la pantalla **Registration complete** (Registro completado), como se muestra a continuación. Haga clic en **Done** (Listo).

    ![][105]

3. Busque el identificador de la aplicación que acaba de crear y haga clic en su fila.

    ![][106]

    Al hacer clic en el id. de la aplicación aparecerán los detalles de la misma y su id. Haga clic en el botón **Settings** (Configuración).

    ![][107]

4. Desplácese a la parte inferior de la pantalla y haga clic en el botón **Create Certificate...** (Crear certificado...) en la sección **Development Push SSL Certificate** (Certificado SSL de inserción de desarrollo).

    ![][108]

    Se mostrará el asistente "Add iOS Certificate".

    Nota: en este tutorial se usa un certificado de desarrollo. Se usa el mismo proceso cuando se registra un certificado de producción. Asegúrese de que establece el mismo tipo de certificado cuando carga el certificado en Servicios móviles.

5. Haga clic en **Choose File** (Elegir archivo), vaya a la ubicación donde guardó anteriormente el archivo CSR y haga clic en **Generate** (Generar).

    ![][110]

6. Una vez que el portal haya creado el certificado, haga clic en el botón **Download** (Descargar) y en **Done** (Listo).

    ![][111]

    De esta forma, se descarga el certificado de firma y se guarda en su equipo en la carpeta de descargas.

    ![][9]

    Nota: de forma predeterminada, el certificado de desarrollo del archivo descargado tiene el nombre <strong>aps\_development.cer</strong>.

7. Haga doble clic en el certificado de inserción **aps\_development.cer** descargado.

    De esta forma, se instala un nuevo certificado en las llaves, como se muestra a continuación:

    ![][10]

    Nota: el nombre del certificado puede ser distinto, pero tendrá el prefijo de <strong>Apple Development iOS Push Notification Services:</strong> (Servicios de notificaciones de inserción de iOS de desarrollo de Apple:).

A continuación, usará este certificado para generar un archivo .p12 y cargarlo en Servicios móviles para permitir la autenticación con APNS.

### <a name="profile"></a>Creación de un perfil de aprovisionamiento para la aplicación

1. Vuelva al <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">Portal de aprovisionamiento de iOS</a>, seleccione **Provisioning Profiles** (Perfiles de aprovisionamiento), seleccione **All** (Todo) y, a continuación, haga clic en el botón **+** para crear un nuevo perfil. De esta forma, se iniciará el asistente para **Agregar perfil de aprovisionamiento de iOS**.

    ![][112]

2. Seleccione **iOS App Development** (Desarrollo de aplicaciones de iOS) en **Development** (Desarrollo) como tipo de perfil de aprovisionamiento y haga clic en **Continue** ( Continuar).

3. A continuación, seleccione el identificador de la aplicación para la aplicación Mobile Services Quickstart en la lista desplegable **App ID** y haga clic en **Continue** (Continuar).

    ![][113]

4. En la pantalla **Seleccionar certificados**, seleccione el certificado creado anteriormente y haga clic en **Continuar**.

    ![][114]

5. A continuación, seleccione en **Devices** los dispositivos que usará para la prueba y haga clic en **Continue** (Continuar).

    ![][115]

6. Para terminar, seleccione un nombre para el perfil en **Nombre de perfil**, haga clic en **Generar** y haga clic en **Listo**.

    ![][116]

    De esta forma, se creará un nuevo perfil de aprovisionamiento.

    ![][117]

7. En Xcode, abra el organizador, seleccione la vista de dispositivos, seleccione **Provisioning Profiles** (Perfiles de aprovisionamiento) en la sección **Library** (Biblioteca) del panel izquierdo y, a continuación, haga clic en el botón **Refresh** (Actualizar) en la parte inferior del panel intermedio.

### <a name="configure-mobileServices"></a>Configuración de Servicios móviles para enviar solicitudes de inserción

Una vez que haya registrado su aplicación con APNS y haya configurado su proyecto, debe configurar su servicio móvil para la integración con APNS.

1. En Keychain Access (Acceso a llaves), haga clic con el botón secundario en el nuevo certificado, haga clic en **Export** (Exportar), asigne un nombre al archivo, seleccione el formato **.p12** y haga clic en **Save** (Guardar).

    ![][28]

    Anote el nombre de archivo y la ubicación del certificado exportado.

2. Inicie sesión en el [Portal de Azure clásico], haga clic en **Servicios móviles** y luego haga clic en la aplicación.

    ![][18]

3. Haga clic en la pestaña **Inserción** y en **Cargar** en **Configuración de notificaciones de inserción de Apple**.

    ![][19]

    Se mostrará el cuadro de diálogo Upload Certificate.

4. Haga clic en **Archivo**, seleccione el archivo .p12 del certificado exportado, escriba la **Contraseña**, asegúrese de que selecciona el **Modo** correcto, haga clic en el icono de marca de verificación y haga clic en **Guardar**.

    ![][20]

El servicio móvil está configurado ahora para que funcione con APNS.

### <a name="configure-app"></a>Configuración de la aplicación Xamarin.iOS

1. En Xamarin.Studio o Visual Studio, abra **Info.plist** y actualice el **identificador de agrupación de trabajos** con el identificador que ha creado anteriormente.

    ![][121]

2. Desplácese hacia abajo hasta **Modos en segundo plano** y active la casilla **Habilitar modos en segundo plano** y la casilla **Notificaciones remotas**.

    ![][122]

3. Haga doble clic en el proyecto en el Panel de soluciones para abrir las **Opciones de proyecto**.

4.  Elija **Registro de agrupación de trabajos iOS** en **Compilar** y seleccione la **identidad** y el **perfil de aprovisionamiento** correspondientes que acaba de configurar para este proyecto.

    ![][120]

    De esta forma, se garantiza que el proyecto de Xamarin usa el nuevo perfil para la firma de código. Para ver la documentación oficial de aprovisionamiento de dispositivos Xamarin, consulte [Aprovisionamiento de dispositivos Xamarin].

### <a name="add-push"></a>Incorporación de notificaciones de inserción a la aplicación

1. En Xamarin.Studio o Visual Studio, expanda el proyecto **ToDoAzure.iOS**, abra la clase **AppDelegate** y, a continuación, reemplace el evento **FinishedLaunching** con el código siguiente:

        public override bool FinishedLaunching(UIApplication app, NSDictionary options)
        {
             // registers for push for iOS8
            var settings = UIUserNotificationSettings.GetSettingsForTypes(
                UIUserNotificationType.Alert
                | UIUserNotificationType.Badge
                | UIUserNotificationType.Sound,
                new NSSet());

            global::Xamarin.Forms.Forms.Init();
            instance = this;
            CurrentPlatform.Init();

            todoItemManager = new ToDoItemManager();
            App.SetTodoItemManager(todoItemManager);


            UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
            UIApplication.SharedApplication.RegisterForRemoteNotifications();

            LoadApplication(new App());
            return base.FinishedLaunching(app, options);
        }

6. En **AppDelegate**, reemplace el evento **RegisteredForRemoteNotifications**:

        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            // Modify device token
            string _deviceToken = deviceToken.Description;
            _deviceToken = _deviceToken.Trim('<', '>').Replace(" ", "");

            // Get Mobile Services client
            MobileServiceClient client = todoItemManager.GetClient();

            // Register for push with Mobile Services
            IEnumerable<string> tag = new List<string>() { "uniqueTag" };

            const string template = "{"aps":{"alert":"$(message)"}}";

            var expiryDate = DateTime.Now.AddDays(90).ToString
                (System.Globalization.CultureInfo.CreateSpecificCulture("es-ES"));

            var push = client.GetPush();

            push.RegisterTemplateAsync(_deviceToken, template, expiryDate, "myTemplate", tag);
        }

7. En **AppDelegate**, reemplace el evento **ReceivedRemoteNotification**:

        public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
        {
            NSObject inAppMessage;

            bool success = userInfo.TryGetValue(new NSString("inAppMessage"), out inAppMessage);

            if (success)
            {
                var alert = new UIAlertView("Got push notification", inAppMessage.ToString(), null, "OK", null);
                alert.Show();
            }
        }

Ahora su aplicación está actualizada para que sea compatible con las notificaciones push.

### <a name="update-scripts"></a>Actualización del script de inserción registrado en el Portal de Azure clásico

1. En el Portal de Azure clásico, haga clic en la pestaña **Datos** y luego en la tabla **TodoItems**.

    ![][21]

2. En **todoitem**, haga clic en la pestaña **Script** y seleccione **Insertar**.

    ![][22]

    Se muestra la función que se invoca cuando se produce una inserción en la tabla **TodoItem**.

3. Reemplace la función de inserción por el siguiente código y, a continuación, haga clic en **Guardar**:

          function insert(item, user, request) {
          // Execute the request and send notifications.
             request.execute({
             success: function() {
              // Create a template-based payload.
              var payload = '{ "message" : "New item added: ' + item.text + '" }';

              // Write the default response and send a notification
              // to all platforms.
              push.send(null, payload, {
                  success: function(pushResponse){
                  console.log("Sent push:", pushResponse);
                  // Send the default response.
                  request.respond();
                  },
                  error: function (pushResponse) {
                      console.log("Error Sending push:", pushResponse);
                       // Send the an error response.
                      request.respond(500, { error: pushResponse });
                      }
               });
              }
           });
          }

    Se registra un nuevo script de inserción, que envía una notificación push (el texto insertado) al dispositivo proporcionado en la solicitud de inserción.

   >[AZURE.NOTE] Este script retrasa el envío de la notificación para proporcionarle tiempo para cerrar la aplicación y recibir una notificación del sistema.

### <a name="test"></a>Prueba de las notificaciones de inserción en su aplicación

1. Presione el botón **Run** (Ejecutar) para crear el proyecto e iniciar la aplicación en un dispositivo compatible con iOS. A continuación, haga clic en **OK** (Aceptar) para aceptar las notificaciones push.

   >[AZURE.NOTE] Debe aceptar de forma explícita las notificaciones push desde su aplicación. Esta solicitud solo se produce la primera vez que se ejecuta la aplicación.

2. En la aplicación, haga clic en el botón **Agregar**, agregue un título de la tarea y, a continuación, haga clic en el botón **Guardar**.

3. Compruebe que se ha recibido la notificación y, a continuación, haga clic en **Aceptar** para descartarla.


Ha completado correctamente este tutorial.

## <a name="Android"></a>Incorporación de notificaciones push a la aplicación de Xamarin.Forms.Android

Agregue notificaciones push a la aplicación Android mediante el servicio de mensajería en la nube de Google (GCM). Necesita una cuenta de Google activa y el [Componente del Cliente del Servicio de mensajería en la nube de Google].

###<a id="register"></a>Habilitación del servicio de mensajería en la nube de Google

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

###<a id="configure"></a>Configuración del servicio móvil para enviar solicitudes de inserción

[AZURE.INCLUDE [mobile-services-android-configure-push](../../includes/mobile-services-android-configure-push.md)]

###<a id="update-scripts"></a>Actualización del script de inserción registrado para enviar notificaciones

>[AZURE.NOTE] En los pasos siguientes se muestra cómo actualizar el script registrado para la operación de inserción en la tabla TodoItem del Portal de Azure clásico. También puede acceder a este script de servicios móviles y editarlo directamente en Visual Studio, en el nodo de Azure del Explorador de servidores.

En el [Portal de Azure clásico], haga clic en la pestaña **Datos** y luego en la tabla **TodoItems**.

   ![][21]

2. En **todoitem**, haga clic en la pestaña **Script** y seleccione **Insertar**.

   ![][22]

Se muestra la función que se invoca cuando se produce una inserción en la tabla **TodoItem**.

3. Reemplace la función de inserción por el siguiente código y, a continuación, haga clic en **Guardar**:

          function insert(item, user, request) {
          // Execute the request and send notifications.
             request.execute({
             success: function() {
              // Create a template-based payload.
              var payload = '{ "message" : "New item added: ' + item.text + '" }';

              // Write the default response and send a notification
              // to all platforms.
              push.send(null, payload, {
                  success: function(pushResponse){
                  console.log("Sent push:", pushResponse);
                  // Send the default response.
                  request.respond();
                  },
                  error: function (pushResponse) {
                      console.log("Error Sending push:", pushResponse);
                       // Send the an error response.
                      request.respond(500, { error: pushResponse });
                      }
               });
              }
           });
          }


    Se registra un nuevo script de inserción, que envía una notificación push (el texto insertado) al dispositivo proporcionado en la solicitud de inserción.

   >[AZURE.NOTE] Este script retrasa el envío de la notificación para proporcionarle tiempo para cerrar la aplicación y recibir una notificación del sistema.


###<a id="configure-app"></a>Configuración del proyecto existente para notificaciones de inserción

1. En la vista de Solución, expanda la carpeta **Componentes** de la aplicación Xamarin.Android y asegúrese de que esté instalado el paquete de servicios móviles de Azure.

2. Haga clic en la carpeta **Componentes**, en **Obtener más componentes...**, busque el componente **Google Cloud Messaging Client** y agréguelo al proyecto.

1. Abra el archivo de proyecto MainActivity.cs y agregue la siguiente instrucción using a la clase:

		using Gcm.Client;


4.	En la clase **MainActivity**, agregue el código siguiente al método **OnCreate**, después de llamar al método **LoadApplication**:

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
                CreateAndShowDialog(new Exception("There was an error creating the Mobile Service. Verify the URL"), "Error");
            }
            catch (Exception e)
            {
                CreateAndShowDialog(e, "Error");
            }

Ahora **MainActivity** estará preparada para agregar notificaciones push.

###<a id="add-push"></a>Incorporación de código de notificaciones de inserción a la aplicación

5. En el proyecto ToDoAzure.Droid, cree una nueva clase en el proyecto denominado `GcmService`.

5. Agregue las siguientes instrucciones using a la clase **GcmService**:

		using Gcm.Client;
		using Microsoft.WindowsAzure.MobileServices;

6. Agregue las siguientes solicitudes de permisos entre las instrucciones **using** y la declaración **namespace**:

		[assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]

        //GET_ACCOUNTS is only needed for android versions 4.0.3 and below
        [assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]
        [assembly: UsesPermission(Name = "android.permission.INTERNET")]
        [assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]

7. En el archivo de proyecto **GcmService.cs** agregue la siguiente clase:

        [BroadcastReceiver(Permission = Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE }, Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK }, Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY }, Categories = new string[] { "@PACKAGE_NAME@" })]

        public class PushHandlerBroadcastReceiver : GcmBroadcastReceiverBase<GcmService>
        {

            public static string[] SENDER_IDS = new string[] { "<PROJECT_NUMBER>" };

        }

	En el código anterior, debe reemplazar _`<PROJECT_NUMBER>`_ por el número de proyecto asignado por Google al aprovisionar la aplicación en el portal para desarrolladores de Google.

8. En el archivo de proyecto GcmService.cs, agregue el código siguiente que define la clase **GcmService**:

         [Service]
         public class GcmService : GcmServiceBase
         {
             public static string RegistrationID { get; private set; }

             public GcmService()
                 : base(PushHandlerBroadcastReceiver.SENDER_IDS){}
         }


	Tenga en cuenta que esta clase se deriva de **GcmServiceBase** y que el atributo **Service** se debe aplicar a esta clase.

	>[AZURE.NOTE]La clase **GcmServiceBase** implementa los métodos **OnRegistered()**, **OnUnRegistered()**, **OnMessage()** y **OnError()**. Debe invalidar estos métodos en la clase **GcmService**.

5. Agregue el código siguiente a la clase **GcmService** que reemplaza al controlador de eventos **OnRegistered**.

        protected override void OnRegistered(Context context, string registrationId)
        {
            Log.Verbose(PushHandlerBroadcastReceiver.TAG, "GCM Registered: " + registrationId);
            RegistrationID = registrationId;

            createNotification("GcmService Registered...", "The device has been Registered, Tap to View!");

            MobileServiceClient client =  MainActivity.DefaultService.todoItemManager.GetClient;

            var push = client.GetPush();

            MainActivity.DefaultService.RunOnUiThread(() => Register(push, null));

        }

        public async void Register(Microsoft.WindowsAzure.MobileServices.Push push, IEnumerable<string> tags)
        {
            try
            {
                const string template = "{"data":{"message":"$(message)"}}";

                await push.RegisterTemplateAsync(RegistrationID, template, "mytemplate", tags);
            }
            catch (Exception ex)
            {
                System.Diagnostics.Debug.WriteLine(ex.Message);
                Debugger.Break();
            }
        }

	Este método usa el identificador de registro GCM devuelto para registrarse con Azure para las notificaciones de inserción.

10. Reemplace el método** OnMessage** en **GcmService** con el código siguiente:

        protected override void OnMessage(Context context, Intent intent)
        {
            Log.Info(PushHandlerBroadcastReceiver.TAG, "GCM Message Received!");

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

12. Al agregar el siguiente método se invalida **OnUnRegistered()** y **OnError()**, que son necesarios para compilar el proyecto.

		protected override void OnUnRegistered(Context context, string registrationId)
		{
			Log.Error("GcmService", "Unregistered RegisterationId : " + registrationId);
		}

        protected override void OnError(Context context, string errorId)
        {
            Log.Error(PushHandlerBroadcastReceiver.TAG, "GCM Error: " + errorId);
        }

###<a id="test"></a>Prueba de las notificaciones push en su aplicación

Puede probar la aplicación conectando directamente un teléfono Android con un cable USB o utilizando un dispositivo virtual en el emulador.

Cuando ejecute esta aplicación en el emulador, asegúrese de utilizar un dispositivo virtual Android (AVD) compatible con las API de Google.

> [AZURE.IMPORTANT] Para recibir notificaciones de inserción, debe configurar la cuenta de Google en el dispositivo virtual de Android (en el emulador, diríjase a **Settings** (Configuración) y haga clic en **Add Account** [Agregar cuenta]). Además, asegúrese de que el emulador esté conectado a Internet.

1. En **Tools** (Herramientas), haga clic en **Open Android Emulator Manager** (Abrir Administrador de emulador Android), seleccione su dispositivo y, a continuación, haga clic en **Edit** (Editar).

    ![][125]

2. Seleccione **Google APIs** (API de Google) en **Target** (Destino) y, a continuación, haga clic en **OK** (Aceptar).

    ![][126]

3. En la barra de herramientas de la parte superior, haga clic en **Run** (Ejecutar) y, a continuación, seleccione la aplicación. Esto inicia el emulador y ejecuta la aplicación.

  La aplicación recupera el valor *registrationId* de GCM y se registra con el Centro de notificaciones.

1. En la aplicación, agregue una nueva tarea.

2. Deslice el dedo hacia abajo desde la parte superior de la pantalla para abrir el Centro de notificaciones del dispositivo y visualizar la notificación.

	![][127]

## <a name="Windows"></a>Incorporación de notificaciones push a la aplicación de Xamarin.Forms.Windows

Esta sección muestra cómo utilizar los Servicios móviles de Azure para enviar notificaciones push a la aplicación de Windows Phone Silverlight que está incluida en la solución Xamarin.Forms.

###<a id="update-app"></a> Actualización de la aplicación para registrarse a fin de recibir notificaciones

Para que la aplicación pueda recibir notificaciones de inserción, debe registrar un canal de notificaciones.

1. En Visual Studio, abra el archivo App.xaml.cs y agregue la instrucción siguiente `using`:

        using Microsoft.Phone.Notification;

3. Agregue lo siguiente a App.xaml.cs:

        public static HttpNotificationChannel CurrentChannel { get; private set; }

        private void AcquirePushChannel()
        {
            CurrentChannel = HttpNotificationChannel.Find("MyPushChannel");

            if (CurrentChannel == null)
            {
                CurrentChannel = new HttpNotificationChannel("MyPushChannel");
                CurrentChannel.Open();
                CurrentChannel.BindToShellToast();
            }

            CurrentChannel.ChannelUriUpdated +=
                new EventHandler<NotificationChannelUriEventArgs>(async (o, args) =>
                {

                   // Register for notifications using the new channel
                    const string template =
                    "<?xml version="1.0" encoding="utf-8"?><wp:Notification " +
                    "xmlns:wp="WPNotification"><wp:Toast><wp:Text1>$(message)</wp:Text1></wp:Toast></wp:Notification>";

                    await client.GetPush()
                        .RegisterTemplateAsync(CurrentChannel.ChannelUri.ToString(), template, "mytemplate");
                });
        }

    Este código recupera el valor de ChannelURI de la aplicación desde el Servicio de notificación de inserción de Microsoft (MPNS) que usa Windows Phone 8.x "Silverlight" y luego lo registra para notificaciones de inserción.

	>[AZURE.NOTE]En este tutorial, el servicio móvil envía una notificación del sistema al dispositivo. Cuando envía una notificación de icono, debe llamar al método **BindToShellTile** en el canal.

4. En la parte superior del controlador de eventos **Application\_Launching** en App.xaml.cs, agregue la siguiente llamada al nuevo método **AcquirePushChannel**:

        AcquirePushChannel();

	Esto garantiza que se solicitará registro cada vez que se cargue la página. En la aplicación, es posible que solo desee realizar este registro de manera periódica para asegurarse de que el registro esté actualizado.

5. Presione la tecla **F5** para ejecutar la aplicación. Se muestra un cuadro de diálogo emergente con la clave de registro.

6.	En el Explorador de soluciones, expanda **Propiedades**, abra el archivo WMAppManifest.xml, haga clic en la pestaña **Funcionalidades** y asegúrese de que la funcionalidad **ID\_\_\_CAP\_\_\_PUSH\_NOTIFICATION** esté activada.

   	![Habilitar las notificaciones en VS](./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-app-enable-push-wp8.png)

   	Esto asegura que la aplicación puede generar notificaciones del sistema.

###<a id="update-scripts"></a> Actualización de scripts del servidor para enviar notificaciones de inserción

Finalmente, debe actualizar el script registrado para insertar la operación en la tabla TodoItem a fin de enviar notificaciones.

1. En el [Portal de Azure clásico], haga clic en la pestaña **Datos** y luego en la tabla **TodoItems**.

    ![][21]

2. En **todoitem**, haga clic en la pestaña **Script** y seleccione **Insertar**.

    ![][22]

    Se muestra la función que se invoca cuando se produce una inserción en la tabla **TodoItem**.

3. Reemplace la función de inserción por el siguiente código y, a continuación, haga clic en **Guardar**:

          function insert(item, user, request) {
          // Execute the request and send notifications.
             request.execute({
             success: function() {
              // Create a template-based payload.
              var payload = '{ "message" : "New item added: ' + item.text + '" }';

              // Write the default response and send a notification
              // to all platforms.
              push.send(null, payload, {
                  success: function(pushResponse){
                  console.log("Sent push:", pushResponse);
                  // Send the default response.
                  request.respond();
                  },
                  error: function (pushResponse) {
                      console.log("Error Sending push:", pushResponse);
                       // Send the an error response.
                      request.respond(500, { error: pushResponse });
                      }
               });
              }
           });
          }


    Se registra un nuevo script de inserción, que envía una notificación push (el texto insertado) al dispositivo proporcionado en la solicitud de inserción.

3. Haga clic en la pestaña **Inserción**, marque **Habilite las notificaciones de inserción no autenticadas** y, a continuación, haga clic en **Guardar**.

	Con esto se permite que el servicio móvil se conecte a MPNS en modo sin autenticar para enviar notificaciones de inserción.

	>[AZURE.NOTE]Este tutorial usa MPNS en modo sin autenticar. En este modo, MPNS limita el número de notificaciones que se pueden enviar a un canal de dispositivo. Para quitar esta restricción, debe generar y cargar un certificado haciendo clic en **Upload** (Cargar) y seleccionando el certificado. Para obtener más información sobre la generación del certificado, consulte [Configuración de un servicio web autenticado para enviar notificaciones push para Windows Phone].

###<a id="test"></a> Prueba de las notificaciones push en su aplicación

1. En Visual Studio, presione la tecla F5 para ejecutar la aplicación.

    >[AZURE.NOTE] Es posible que se produzca una excepción 401 Unauthorized RegistrationAuthorizationException al realizar pruebas en el emulador de Windows Phone. Esta excepción puede producirse durante la llamada `RegisterNativeAsync()` debido a la manera en el que el emulador de Windows Phone sincroniza su reloj con el equipo host. Puede resultar en un token de seguridad rechazado. Para solucionarlo, establezca manualmente el reloj del emulador antes de realizar las pruebas.

5. En la aplicación, cree una nueva tarea con el título **Hello push** y luego haga clic inmediatamente en el botón Inicio o Atrás para abandonar la aplicación.

  	Esto envía una solicitud de inserción al servicio móvil para almacenar el elemento agregado. Observe que el dispositivo recibe una notificación del sistema que dice **hello push**.

	![Notificación del sistema recibida](./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-quickstart-push5-wp8.png)

	>[AZURE.NOTE]No recibirá la notificación mientras permanezca en la aplicación. Para recibir una notificación del sistema mientras la aplicación esté activa, deberá gestionar el evento [ShellToastNotificationReceived](http://msdn.microsoft.com/library/windowsphone/develop/microsoft.phone.notification.httpnotificationchannel.shelltoastnotificationreceived(v=vs.105).aspx).

<!-- Anchors. -->
[Generate the certificate signing request]: #certificates
[Register your app and enable push notifications]: #register
[Create a provisioning profile for the app]: #profile
[Configure Mobile Services]: #configure-mobileServices
[Configure the Xamarin.iOS App]: #configure-app
[Update scripts to send push notifications]: #update-scripts
[Add push notifications to the app]: #add-push
[Insert data to receive notifications]: #test

<!-- Images. -->

[5]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-step5.png
[6]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-step6.png
[7]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-step7.png

[9]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-step9.png
[10]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-step10.png

[17]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-step17.png
[18]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-selection.png
[19]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-push-tab-ios.png
[20]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-push-tab-ios-upload.png
[21]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-portal-data-tables.png
[22]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-insert-script-push2.png
[23]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-quickstart-push1-ios.png
[24]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-quickstart-push2-ios.png
[25]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-quickstart-push3-ios.png
[26]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-quickstart-push4-ios.png
[28]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-step18.png

[101]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-01.png
[102]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-02.png
[103]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-03.png
[104]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-04.png
[105]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-05.png
[106]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-06.png
[107]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-07.png
[108]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-08.png

[110]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-10.png
[111]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-11.png
[112]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-12.png
[113]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-13.png
[114]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-14.png
[115]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-15.png
[116]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-16.png
[117]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-17.png

[120]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-20.png
[121]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-21.png
[122]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-22.png
[123]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-data-tab-empty.png
[124]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-create-todoitem-table.png
[125]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/notification-hub-create-android-app7.png
[126]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/notification-hub-create-android-app8.png
[127]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/notification-area-received.png


[Xamarin.iOS Studio]: http://xamarin.com/platform
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Servicio de notificaciones push de Apple]: http://go.microsoft.com/fwlink/p/?LinkId=272584
[Get started with Mobile Services]: mobile-services-ios-get-started.md

[Aprovisionamiento de dispositivos Xamarin]: http://developer.xamarin.com/guides/ios/getting_started/installation/device_provisioning/


[Portal de Azure clásico]: https://manage.windowsazure.com/
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333
[Componente de Servicios móviles de Azure]: http://components.xamarin.com/view/azure-mobile-services/
[completed example project]: http://go.microsoft.com/fwlink/p/?LinkId=331303
[Xamarin.iOS]: http://xamarin.com/download
[Componente cliente Servicio de mensajería en la nube de Google]: http://components.xamarin.com/view/GCMClient/
[Componente del Cliente del Servicio de mensajería en la nube de Google]: http://components.xamarin.com/view/GCMClient/
[ejemplo de inicio de notificación push de Xamarin.Forms Azure]: https://github.com/Azure/mobile-services-samples/tree/master/TodoListXamarinForms
[ejemplo de notificación push de Xamarin.Forms Azure completada]: https://github.com/Azure/mobile-services-samples/tree/master/GettingStartedWithPushXamarinForms

<!---HONumber=AcomDC_0211_2016-->