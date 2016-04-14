<properties
	pageTitle="Incorporación de notificaciones de inserción a la aplicación de Servicios móviles (Xamarin.iOS) - Servicios móviles"
	description="Obtenga información acerca de cómo usar notificaciones de inserción en aplicaciones Xamarin.iOS con Servicios móviles de Azure."
	documentationCenter="xamarin"
	authors="ysxu"
	manager="dwrede"
	services="mobile-services"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="02/11/2016"
	ms.author="yuaxu"/>

# Incorporación de notificaciones de inserción a la aplicación de Servicios móviles

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-get-started-push](../../includes/mobile-services-selector-get-started-push.md)]

##Información general

Este tema muestra cómo puede usar los Servicios móviles Azure para enviar notificaciones de inserción a una aplicación Xamarin.iOS 8. En este tutorial aprenderá a agregar notificaciones de inserción con el servicio de notificaciones de inserción de Apple (APNS) al proyecto [Introducción a Servicios móviles]. Cuando haya finalizado, el servicio móvil le enviará una notificación de inserción cada vez que se inserte un registro.

Este tutorial requiere lo siguiente:

+ Un dispositivo iOS 8 (no se pueden probar las notificaciones de inserción en el simulador de iOS)
+ Pertenencia al programa para desarrolladores de iOS
+ [Xamarin.iOS Studio]
+ [Componente de Servicios móviles de Azure]

>[AZURE.IMPORTANT] Debido a los requisitos de APNS, debe implementar y realizar una prueba de las notificaciones push en un dispositivo compatible con iOS (iPhone o iPad) en lugar de hacerlo en un emulador.

APNS usa certificados para autenticar el servicio móvil. Siga estas instrucciones para crear los certificados necesarios y cargarlos en su servicio móvil. Para consultar la documentación oficial de la característica APNS, consulte [Servicio de notificaciones push de Apple].

## <a name="certificates"></a>Generación del archivo de solicitud de firma de certificado

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

## <a name="register"></a>Registro de la aplicación para notificaciones de inserción

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

## <a name="profile"></a>Creación de un perfil de aprovisionamiento para la aplicación

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

## <a name="configure-mobileServices"></a>Configuración de Servicios móviles para enviar solicitudes de inserción

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

## <a name="configure-app"></a>Configuración de la aplicación Xamarin.iOS

1. En Xamarin.Studio, abra **Info.plist** y actualice el **identificador de agrupación de trabajos** con el identificador que ha creado anteriormente.

    ![][121]

2. Desplácese hacia abajo hasta **Modos en segundo plano** y active la casilla **Habilitar modos en segundo plano** y la casilla **Notificaciones remotas**.

    ![][122]

3. Haga doble clic en el proyecto en el Panel de soluciones para abrir las **Opciones de proyecto**.

4.  Elija **Registro de agrupación de trabajos iOS** en **Compilar** y seleccione la **identidad** y el **perfil de aprovisionamiento** correspondientes que acaba de configurar para este proyecto.

    ![][120]

    De esta forma, se garantiza que el proyecto de Xamarin usa el nuevo perfil para la firma de código. Para ver la documentación oficial de aprovisionamiento de dispositivos Xamarin, consulte [Aprovisionamiento de dispositivos Xamarin].

## <a name="add-push"></a>Incorporación de notificaciones de inserción a la aplicación

1. En Xamarin.Studio, abra el archivo AppDelegate.cs y agregue la siguiente propiedad:

        public string DeviceToken { get; set; }

2. Abra la clase **TodoItem** y agregue la siguiente propiedad:

        [JsonProperty(PropertyName = "deviceToken")]
        public string DeviceToken { get; set; }

3. En **QSTodoService**, reemplace la declaración de cliente existente para que sea:

        public MobileServiceClient client { get; private set; }

4. A continuación, agregue el siguiente método de modo que **AppDelegate** pueda adquirir el cliente más tarde para registrar notificaciones de inserción:

        public MobileServiceClient GetClient {
            get{
                return client;
            }
        }

5. En **AppDelegate**, reemplace el evento **FinishedLaunching**:

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

6. En **AppDelegate**, reemplace el evento **RegisteredForRemoteNotifications**:

        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            // Modify device token
            DeviceToken = deviceToken.Description;
            DeviceToken = DeviceToken.Trim ('<', '>').Replace (" ", "");

            // Get Mobile Services client
            MobileServiceClient client = QSTodoService.DefaultService.GetClient;

            // Register for push with Mobile Services
            IEnumerable<string> tag = new List<string>() { "uniqueTag" };
            var push = client.GetPush ();
            push.RegisterNativeAsync (DeviceToken, tag);
        }

7. En **AppDelegate**, reemplace el evento **ReceivedRemoteNotification**:

        public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
        {
            Debug.WriteLine(userInfo.ToString());
            NSObject inAppMessage;

            bool success = userInfo.TryGetValue(new NSString("inAppMessage"), out inAppMessage);

            if (success)
            {
                var alert = new UIAlertView("Got push notification", inAppMessage.ToString(), null, "OK", null);
                alert.Show();
            }
        }

8. En **QSTodoListViewController**, modifique la acción **OnAdd** para que el token del dispositivo se almacene en **AppDelegeate** y almacénelo en la clase **TodoItem** que se está agregando.

        string deviceToken = ((AppDelegate)UIApplication.SharedApplication.Delegate).DeviceToken;

        var newItem = new TodoItem()
        {
            Text = itemText.Text,
            Complete = false,
            DeviceToken = deviceToken
        };

Ahora su aplicación está actualizada para que sea compatible con las notificaciones push.

## <a name="update-scripts"></a>Actualización del script de inserción registrado en el Portal de Azure clásico

1. En el [Portal de Azure clásico], haga clic en la pestaña **Datos** y luego en la tabla **TodoItem**.

    ![][21]

2. En **todoitem**, haga clic en la pestaña **Script** y seleccione **Insertar**.

    ![][22]

    Se muestra la función que se invoca cuando se produce una inserción en la tabla **TodoItem**.

3. Reemplace la función de inserción por el siguiente código y, a continuación, haga clic en **Guardar**:

        function insert(item, user, request) {
            request.execute();
            // Set timeout to delay the notification, to provide time for the
            // app to be closed on the device to demonstrate toast notifications
            setTimeout(function() {
                push.apns.send("uniqueTag", {
                    alert: "Toast: " + item.text,
                    payload: {
                        inAppMessage: "Hey, a new item arrived: '" + item.text + "'"
                    }
                });
            }, 2500);
        }

    De esta forma, se registra un nuevo script de inserción, que usa el [objeto apns] para enviar una notificación de inserción (el texto insertado) al dispositivo especificado en la solicitud de inserción.

   >[AZURE.NOTE] Este script retrasa el envío de la notificación para proporcionarle tiempo para cerrar la aplicación y recibir una notificación del sistema.

## <a name="test"></a>Prueba de las notificaciones de inserción en su aplicación

1. Presione el botón **Run** (Ejecutar) para crear el proyecto e iniciar la aplicación en un dispositivo compatible con iOS. A continuación, haga clic en **OK** (Aceptar) para aceptar las notificaciones push.

    ![][23]

   >[AZURE.NOTE] Debe aceptar de forma explícita las notificaciones push desde su aplicación. Esta solicitud solo se produce la primera vez que se ejecuta la aplicación.

2. En la aplicación, escriba un texto significativo, como _Una nueva tarea de Servicios móviles_ y, a continuación, haga clic en el icono del signo más (**+**).

    ![][24]

3. Compruebe que se ha recibido la notificación y, a continuación, haga clic en **Aceptar** para descartarla.

    ![][25]

4. Repita el paso 2 y cierre de inmediato la aplicación. A continuación, compruebe que se muestra la siguiente notificación.

    ![][26]

Ha completado correctamente este tutorial.

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

[5]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step5.png
[6]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step6.png
[7]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step7.png

[9]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step9.png
[10]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step10.png

[17]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step17.png
[18]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-selection.png
[19]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-push-tab-ios.png
[20]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-push-tab-ios-upload.png
[21]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-portal-data-tables.png
[22]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-insert-script-push2.png
[23]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-quickstart-push1-ios.png
[24]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-quickstart-push2-ios.png
[25]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-quickstart-push3-ios.png
[26]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-quickstart-push4-ios.png
[28]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step18.png

[101]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-01.png
[102]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-02.png
[103]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-03.png
[104]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-04.png
[105]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-05.png
[106]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-06.png
[107]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-07.png
[108]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-08.png

[110]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-10.png
[111]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-11.png
[112]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-12.png
[113]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-13.png
[114]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-14.png
[115]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-15.png
[116]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-16.png
[117]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-17.png

[120]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-20.png
[121]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-21.png
[122]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-22.png

[Xamarin.iOS Studio]: http://xamarin.com/platform
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Servicio de notificaciones push de Apple]: http://go.microsoft.com/fwlink/p/?LinkId=272584
[Introducción a Servicios móviles]: mobile-services-ios-get-started.md

[Aprovisionamiento de dispositivos Xamarin]: http://developer.xamarin.com/guides/ios/getting_started/installation/device_provisioning/


[Portal de Azure clásico]: https://manage.windowsazure.com/
[objeto apns]: http://go.microsoft.com/fwlink/p/?LinkId=272333
[Componente de Servicios móviles de Azure]: http://components.xamarin.com/view/azure-mobile-services/
[completed example project]: http://go.microsoft.com/fwlink/p/?LinkId=331303
[Xamarin.iOS]: http://xamarin.com/download

<!---HONumber=AcomDC_0218_2016-->