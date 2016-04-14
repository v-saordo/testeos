<properties
	pageTitle="Introducción a Centros de notificaciones para aplicaciones Chrome | Microsoft Azure"
	description="En este tutorial aprenderá a usar Centros de notificaciones de Azure para enviar notificaciones push a una aplicación Chrome."
	services="notification-hubs"
	documentationCenter=""
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-chrome"
	ms.devlang="JavaScript"
	ms.topic="hero-article"
	ms.date="02/29/2016"
	ms.author="wesmc"/>

# Introducción a Centros de notificaciones para aplicaciones Chrome

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

Este tema muestra cómo puede utilizar los Centros de notificaciones de Azure para enviar notificaciones de inserción a una aplicación Chrome.

Una de las ventajas clave del uso de notificaciones de aplicaciones Chrome es que las notificaciones se muestran en el contexto del explorador Google Chrome. No es necesario tener abierta o en ejecución la aplicación Chrome en el explorador (aunque el propio explorador Chrome se debe estar ejecutando). También obtendrá una vista consolidada de todas las notificaciones en la ventana de notificaciones de Chrome.

>[AZURE.NOTE] Esta no es una notificación push genérica que se ejecuta en el explorador y es específica de las aplicaciones Chrome. Vea [Información general sobre las aplicaciones Chrome] para obtener más información. Las aplicaciones Chrome se conocían anteriormente como "Aplicaciones empaquetadas" y son diferentes de las "Aplicaciones hospedadas" más sencillas. Vea [Aplicaciones web instalables] para ver la diferencia. Las aplicaciones Chrome también se pueden ejecutar en dispositivos móviles (Android y iOS) con Apache Cordova. Vea [Aplicaciones de Chrome en dispositivos móviles] para obtener más información.

En este tutorial, creará una aplicación Chrome que reciba notificaciones push mediante el servicio de mensajería en la nube de Google (GCM). Cuando termine el tutorial, podrá difundir notificaciones push a todos los usuarios de Chrome que tengan instalada esta aplicación Chrome.

Este tutorial le guiará a través de estos pasos básicos para habilitar las notificaciones de inserción:

* [Habilitación del servicio de mensajería en la nube de Google](#register)
* [Configuración de su Centro de notificaciones](#configure-hub)
* [Conexión de la aplicación Chrome con el centro de notificaciones](#connect-app)
* [Envío de notificaciones a la aplicación Chrome](#send)
* [Pasos siguientes](#next-steps)

En este tutorial se demuestra el escenario de difusión sencillo con centros de notificaciones. La configuración de GCM y de Centros de notificaciones de Azure es idéntica a la configuración para Android ya que el [Servicio de mensajería en la nube de Google para Chrome] está en desuso y el mismo GCM ahora admite dispositivos Android e instancias de Chrome.

Asegúrese de seguir esta sección junto con los tutoriales de la sección de pasos siguientes para saber cómo usar los centros de notificaciones para abordar usuarios y grupos de dispositivos específicos.

>[AZURE.NOTE] Para completar este tutorial, deberá tener una cuenta de Azure activa. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fes-ES%2Fdocumentation%2Farticles%notification-hubs-chrome-get-started%2F).

##<a id="register"></a>Habilitación del servicio de mensajería en la nube de Google

1. Diríjase al sitio web de la [consola en la nube de Google], inicie sesión con las credenciales de su cuenta de Google y, luego, haga clic en botón **Create Project** (Crear proyecto). Proporcione un **nombre de proyecto** adecuado y, luego, haga clic en el botón **Create** (Crear).

   	![][1]

2. Tome nota del **número del proyecto** en la página de **proyectos** para el proyecto que acaba de crear. Úselo como **id. de remitente de GCM** en la aplicación Chrome para registrarse con GCM.

   	![][2]

3. En el panel de la izquierda, haga clic en **APIs & auth** (API y autenticación), y luego desplácese hacia abajo y haga clic en el botón de alternancia para habilitar **Google Cloud Messaging for Android** (Servicio de mensajería en la nube de Google para Android). No es necesario habilitar el **servicio de mensajería en la nube de Google para Chrome**.

   	![][3]

4. En el panel izquierdo, haga clic en **Credentials** (Credenciales) -> **Create New Key** (Crear nueva clave) -> **Server Key** (Clave de servidor) -> **Create** (Crear).

   	![][4]

5. Anote el valor de la **clave de API** del servidor. La configurará después en el centro de notificaciones para que pueda enviar notificaciones push a GCM.

   	![][5]

##<a id="configure-hub"></a>Configuración del centro de notificaciones

1. Inicie sesión en el [Portal de Azure clásico] y, luego, haga clic en **+NUEVO** en la parte inferior izquierda de la pantalla.

2. Haga clic en **Servicios de aplicaciones** -> **Bus de servicio** -> **Centro de notificaciones** -> **Creación rápida**. Escriba un nombre para su centro de notificaciones, seleccione la región deseada y, a continuación, haga clic en **Crear una nueva base de datos central de notificaciones**.

   	![][6]

4. Vaya al centro de notificaciones que acaba de crear. Haga clic en el espacio de nombres que contiene el centro de notificaciones (normalmente ***nombre del centro de notificaciones*-ns**).

   	![][7]

5. Haga clic en la pestaña **Centros de notificaciones** en la parte superior.

   	![][8]

6. Haga clic en la pestaña **Configurar** en la parte superior.

   	![][9]

7. En la pestaña **Configurar**, desplácese hacia abajo hasta la **configuración del servicio de mensajería en la nube de google**, escriba el valor de la **clave de API** obtenido en la sección anterior y luego haga clic en **Guardar**.

   	![][10]

8. Haga clic en la pestaña **Panel** en la parte superior y, luego, haga clic en **Información de conexión** en la parte inferior.

   	![][11]

9. Tome nota del valor **DefaultListenSharedAccessSignature** (que será necesario en la aplicación Chrome para registrarse con el centro de notificaciones) y del valor **DefaultFullSharedAccessSignature** (que necesitará para enviar notificaciones)

   	![][12]

El Centro de notificaciones está ahora configurado para funcionar con GCM y tiene las cadenas de conexión necesarias para una configuración adicional.

##<a id="connect-app"></a>Conexión de la aplicación Chrome con el centro de notificaciones

###Creación de una nueva aplicación Chrome

El siguiente ejemplo se basa en el [ejemplo de GCM de la aplicación Chrome] y usa la forma recomendada para crear una aplicación Chrome. En las secciones siguientes, nos centraremos en los pasos relacionados con Centros de notificaciones de Azure. Se recomienda que descargue el origen de esta aplicación Chrome del [ejemplo del centro de notificaciones de la aplicación Chrome].

La aplicación Chrome se crea con JavaScript y puede usar su editor de texto preferido para su creación. A continuación se muestra cuál será el aspecto de esta aplicación Chrome.

   	![][15]

2. Cree una carpeta y asígnele el nombre **ChromePushApp**o uno de su elección.

3. Descargue la **biblioteca cryto-js** desde [biblioteca crypto-js] en esta carpeta. Esta carpeta de biblioteca contendrá dos subcarpetas: **components** y **rollups**.

4. Cree un archivo **manifest.json**. Todas las aplicaciones Chrome están respaldadas por un archivo de manifiesto que describe los metadatos de la aplicación y, en particular, los permisos disponibles para ella.

		{
		  "name": "NH-GCM Notifications",
		  "description": "Chrome platform app.",
		  "manifest_version": 2,
		  "version": "0.1",
		  "app": {
		    "background": {
		      "scripts": ["background.js"]
		    }
		  },
		  "permissions": ["gcm", "storage", "notifications", "https://*.servicebus.windows.net/*"],
		  "icons": { "128": "gcm_128.png" }
		}

	Observe el elemento **permissions** que especifica que esta aplicación Chrome puede recibir notificaciones push de GCM. También debe especificar el URI de Centros de notificaciones de Azure donde la aplicación Chrome realizará una llamada REST para registrarse. Este usa un archivo de icono, gcm\_128.png, que encontrará en el origen reutilizado del ejemplo original de GCM. Puede usar cualquier imagen que desee.

5. Cree un archivo llamado **background.js** con el código siguiente:

		// Returns a new notification ID used in the notification.
		function getNotificationId() {
		  var id = Math.floor(Math.random() * 9007199254740992) + 1;
		  return id.toString();
		}

		function messageReceived(message) {
		  // A message is an object with a data property that
		  // consists of key-value pairs.

		  // Concatenate all key-value pairs to form a display string.
		  var messageString = "";
		  for (var key in message.data) {
		    if (messageString != "")
		      messageString += ", "
		    messageString += key + ":" + message.data[key];
		  }
		  console.log("Message received: " + messageString);

		  // Pop up a notification to show the GCM message.
		  chrome.notifications.create(getNotificationId(), {
		    title: 'GCM Message',
		    iconUrl: 'gcm_128.png',
		    type: 'basic',
		    message: messageString
		  }, function() {});
		}

		var registerWindowCreated = false;

		function firstTimeRegistration() {
		  chrome.storage.local.get("registered", function(result) {

		    registerWindowCreated = true;
		    chrome.app.window.create(
		      "register.html",
		      {  width: 520,
		         height: 500,
		         frame: 'chrome'
		      },
		      function(appWin) {}
		    );
		  });
		}

		// Set up a listener for GCM message event.
		chrome.gcm.onMessage.addListener(messageReceived);

		// Set up listeners to trigger the first-time registration.
		chrome.runtime.onInstalled.addListener(firstTimeRegistration);
		chrome.runtime.onStartup.addListener(firstTimeRegistration);

	Este es el archivo que muestra el HTML de la ventana de la aplicación de Chrome (**register.html**) y también define el controlador **messageReceived** para controlar la notificación push entrante.

6. Cree un archivo llamado **register.html** que define la interfaz de usuario de la aplicación Chrome. Observe que en este ejemplo se usa *CryptoJS v3.1.2*. Si descargó cualquier otra versión, corrija la ruta de acceso de src del script.

		<html>

		<head>
		<title>GCM Registration</title>
		<script src="register.js"></script>
		<script src="CryptoJS v3.1.2/rollups/hmac-sha256.js"></script>
		<script src="CryptoJS v3.1.2/components/enc-base64-min.js"></script>
		</head>

		<body>

		Sender ID:<br/><input id="senderId" type="TEXT" size="20"><br/>
		<button id="registerWithGCM">Register with GCM</button>
		<br/>
		<br/>
		<br/>

		Notification Hub Name:<br/><input id="hubName" type="TEXT" style="width:400px"><br/><br/>
		Connection String:<br/><textarea id="connectionString" type="TEXT" style="width:400px;height:60px"></textarea>

		<br/>

		<button id="registerWithNH" disabled="true">Register with Azure Notification Hubs</button>

		<br/>
		<br/>

		<textarea id="console" type="TEXT" readonly style="width:500px;height:200px;background-color:#e5e5e5;padding:5px"></textarea>

		</body>

		</html>

7. Cree un archivo llamado **register.js** con el código siguiente. Este archivo especifica el script que subyace a **register.html**. Las aplicaciones Chrome no permiten la ejecución insertada, por lo que es necesario crear un script de copia de seguridad independiente para la interfaz de usuario.

		var registrationId = "";
		var hubName        = "", connectionString = "";
		var originalUri    = "", targetUri = "", endpoint = "", sasKeyName = "", sasKeyValue = "", sasToken = "";

		window.onload = function() {
		   document.getElementById("registerWithGCM").onclick = registerWithGCM;  
		   document.getElementById("registerWithNH").onclick = registerWithNH;
		   updateLog("You have not registered yet. Please provider sender ID and register with GCM and then with  Notification Hubs.");
		}

		function updateLog(status) {
		  currentStatus = document.getElementById("console").innerHTML;
		  if (currentStatus != "") {
		    currentStatus = currentStatus + "\n\n";
		  }

		  document.getElementById("console").innerHTML = currentStatus  + status;
		}

		function registerWithGCM() {
		  var senderId = document.getElementById("senderId").value.trim();
		  chrome.gcm.register([senderId], registerCallback);

		  // Prevent register button from being clicked again before the registration finishes.
		  document.getElementById("registerWithGCM").disabled = true;
		}

		function registerCallback(regId) {
		  registrationId = regId;
		  document.getElementById("registerWithGCM").disabled = false;

		  if (chrome.runtime.lastError) {
		    // When the registration fails, handle the error and retry the
		    // registration later.
		    updateLog("Registration failed: " + chrome.runtime.lastError.message);
		    return;
		  }

		  updateLog("Registration with GCM succeeded.");
		  document.getElementById("registerWithNH").disabled = false;

		  // Mark that the first-time registration is done.
		  chrome.storage.local.set({registered: true});
		}

		function registerWithNH() {
		  hubName = document.getElementById("hubName").value.trim();
		  connectionString = document.getElementById("connectionString").value.trim();
		  splitConnectionString();
		  generateSaSToken();
		  sendNHRegistrationRequest();
		}

		// From http://msdn.microsoft.com/library/dn495627.aspx
		function splitConnectionString()
		{
		  var parts = connectionString.split(';');
		  if (parts.length != 3)
		  throw "Error parsing connection string";

		  parts.forEach(function(part) {
		    if (part.indexOf('Endpoint') == 0) {
		    endpoint = 'https' + part.substring(11);
		    } else if (part.indexOf('SharedAccessKeyName') == 0) {
		    sasKeyName = part.substring(20);
		    } else if (part.indexOf('SharedAccessKey') == 0) {
		    sasKeyValue = part.substring(16);
		    }
		  });

		  originalUri = endpoint + hubName;
		}

		function generateSaSToken()
		{
		  targetUri = encodeURIComponent(originalUri.toLowerCase()).toLowerCase();
		  var expiresInMins = 10; // 10 minute expiration

		  // Set expiration in seconds.
		  var expireOnDate = new Date();
		  expireOnDate.setMinutes(expireOnDate.getMinutes() + expiresInMins);
		  var expires = Date.UTC(expireOnDate.getUTCFullYear(), expireOnDate
		    .getUTCMonth(), expireOnDate.getUTCDate(), expireOnDate
		    .getUTCHours(), expireOnDate.getUTCMinutes(), expireOnDate
		    .getUTCSeconds()) / 1000;
		  var tosign = targetUri + '\n' + expires;

		  // Using CryptoJS.
		  var signature = CryptoJS.HmacSHA256(tosign, sasKeyValue);
		  var base64signature = signature.toString(CryptoJS.enc.Base64);
		  var base64UriEncoded = encodeURIComponent(base64signature);

		  // Construct authorization string.
		  sasToken = "SharedAccessSignature sr=" + targetUri + "&sig="
		                  + base64UriEncoded + "&se=" + expires + "&skn=" + sasKeyName;
		}

		function sendNHRegistrationRequest()
		{
		  var registrationPayload =
		  "<?xml version="1.0" encoding="utf-8"?>" +
		  "<entry xmlns="http://www.w3.org/2005/Atom">" +
		      "<content type="application/xml">" +
		          "<GcmRegistrationDescription xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect">" +
		              "<GcmRegistrationId>{GCMRegistrationId}</GcmRegistrationId>" +
		          "</GcmRegistrationDescription>" +
		      "</content>" +
		  "</entry>";

		  // Update the payload with the registration ID obtained earlier.
		  registrationPayload = registrationPayload.replace("{GCMRegistrationId}", registrationId);

		  var url = originalUri + "/registrations/?api-version=2014-09";
		  var client = new XMLHttpRequest();

		  client.onload = function () {
		    if (client.readyState == 4) {
		      if (client.status == 200) {
		        updateLog("Notification Hub Registration succesful!");
		        updateLog(client.responseText);
		      } else {
		        updateLog("Notification Hub Registration did not succeed!");
		        updateLog("HTTP Status: " + client.status + " : " + client.statusText);
		        updateLog("HTTP Response: " + "\n" + client.responseText);
		      }
		    }
		  };

		  client.onerror = function () {
		        updateLog("ERROR - Notification Hub Registration did not succeed!");
		  }

		  client.open("POST", url, true);
		  client.setRequestHeader("Content-Type", "application/atom+xml;type=entry;charset=utf-8");
		  client.setRequestHeader("Authorization", sasToken);
		  client.setRequestHeader("x-ms-version", "2014-09");

		  try {
		      client.send(registrationPayload);
		  }
		  catch(err) {
		      updateLog(err.message);
		  }
		}

	El script anterior tiene los beneficios siguientes:
	- *window.onload* define los eventos de clic de botón de los dos botones de la interfaz de usuario. Uno se registra con GCM y el otro usa el identificador de registro que se devolvió después del registro con GCM para registrarse con Centros de notificaciones de Azure.
	- *updateLog* es la función que define una función de registro simple.
	- *registerWithGCM* es el primer controlador de clic de botón que hace que la llamada de **chrome.gcm.register** a GCM registre esta instancia de la aplicación Chrome.
	- *registerCallback* es la función de devolución de llamada que se invoca cuando se devuelve la llamada de registro GCM anterior.
	- *registerWithNH* es el identificador de clic del segundo botón, que se registra con los Centros de notificaciones. Obtiene los valores **hubName** y **connectionString** (que el usuario especificó) y crea la llamada a la API de REST de registro de los Centros de notificaciones.
	- *splitConnectionString* y *generateSaSToken* son la implementación de Javascript de la creación de un token SaS que debe enviarse en todas las llamadas de la API de REST. Para más información, vea [Conceptos](http://msdn.microsoft.com/library/dn495627.aspx).
	- *sendNHRegistrationRequest* es la función que realiza una llamada HTTP REST.
	- *registrationPayload* define la carga XML del registro. Para obtener más información, consulte [Creación de una API de REST para el registro en el Centro de notificaciones]. Actualizamos el identificador de registro en ella con lo que hemos recibido de GCM.
	- *client* es una instancia de **XMLHttpRequest** que usamos para realizar la solicitud HTTP POST. Tenga en cuenta que actualizamos el encabezado **Authorization** con **sasToken**. La finalización correcta de esta llamada registrará esta instancia de la aplicación Chrome con Centros de notificaciones de Azure.


Debería ver la siguiente vista de la carpeta al final: ![][21]

###Configuración y prueba de la aplicación Chrome

1. Abra el explorador Chrome. Abra las **extensiones de Chrome** y habilite el **modo de desarrollador**.

   	![][16]

2. Haga clic en **Load unpacked extension** (Cargar extensión descomprimida) y vaya a la carpeta donde creó los archivos. Opcionalmente, también puede usar la **herramienta para desarrolladores de aplicaciones y extensiones Chrome**. Esta herramienta es una aplicación Chrome en sí misma (se instala desde el almacén web de Chrome) y proporciona las capacidades de depuración avanzadas para el desarrollo de aplicaciones Chrome.

   	![][17]

3. Si la aplicación Chrome se crea sin errores, verá que aparece.

   	![][18]

4. Escriba el **número de proyecto** que obtuvo anteriormente de la **consola en la nube de Google**, como el identificador del remitente, y haga clic en **Register with GCM** (Registrar con GCM). Debe ver el mensaje **Registration with GCM succeeded** (El registro en GCM se realizó correctamente).

   	![][19]

5. Escriba su **nombre del centro de notificaciones** y el valor de **DefaultListenSharedAccessSignature** obtenido anteriormente del Portal y haga clic en **Register with Azure Notification Hub** (Registrarse en el Centro de notificaciones de Azure). Debe ver el mensaje **Notification Hub Registration succesful!** (El registro en el centro de notificaciones se realizó correctamente) y los detalles de la respuesta de registro, que contienen el identificador de registro de Centros de notificaciones de Azure.

   	![][20]

##<a name="send"></a>Envío de notificaciones a la aplicación Chrome

En este tutorial enviará notificaciones con una aplicación de consola .NET. Sin embargo, puede enviar notificaciones mediante Centros de notificaciones desde cualquier back-end a través de la <a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">interfaz de REST</a>.

Para ver un ejemplo de cómo enviar notificaciones desde un back-end de Servicios móviles de Azure integrado en Centros de notificaciones, consulte [Incorporación de notificaciones push a la aplicación de Servicios móviles](../mobile-services/mobile-services-dotnet-backend-windows-universal-dotnet-get-started-push.md).
  
Para ver un ejemplo de cómo enviar notificaciones con las API de REST, vea "Uso de Centros de notificaciones desde Java/PHP/Python" ([Java](notification-hubs-java-backend-how-to.md) | [PHP](notification-hubs-php-backend-how-to.md) | [Python](notification-hubs-python-backend-how-to.md)).

1. En Visual Studio, en el menú **Archivo**, seleccione **Nuevo** y, luego, **Proyecto**. En **Visual C#**, haga clic en **Windows** y **Aplicación de consola** y, luego, haga clic en **Aceptar**. Esto crea un nuevo proyecto de aplicación de consola.

2. En el menú **Tools**, haga clic en **Library Package Manager** y, a continuación, en **Package Manager Console**. Esto muestra la Consola del Administrador de paquetes.

3. En la ventana de la consola, ejecute el siguiente comando:

        Install-Package Microsoft.Azure.NotificationHubs

   	Esta acción agrega una referencia al SDK de Bus de servicio de Azure con el paquete <a href="http://nuget.org/packages/  WindowsAzure.ServiceBus/">WindowsAzure.ServiceBus de NuGet</a>.

4. Abra el archivo **Program.cs** y agregue la siguiente instrucción `using`:

        using Microsoft.Azure.NotificationHubs;

5. En la clase **Program**, agregue el siguiente método.

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            String message = "{"data":{"message":"Hello Chrome from Azure Notification Hubs"}}";
            await hub.SendGcmNativeNotificationAsync(message);
        }

   	Asegúrese de reemplazar el marcador de posición **hub name** por el nombre del centro de notificaciones que aparece en el portal en la pestaña *Centros de notificaciones*. Además, reemplace el marcador de posición de la cadena de conexión por la cadena de conexión llamada **DefaultFullSharedAccessSignature** que obtuvo en la sección "Configuración del centro de notificaciones".

	>[AZURE.NOTE] Asegúrese de usar la cadena de conexión con acceso **Total**, no con acceso **Escuchar**. La cadena de acceso de **escucha** no tiene permisos para enviar notificaciones.

5. Agregue las siguientes líneas al método **Main**:

         SendNotificationAsync();
		 Console.ReadLine();

6. Asegúrese de que el explorador Chrome esté abierto. La aplicación Chrome no necesita abrirse para esto. Debería ver el siguiente cuadro emergente de notificación en el escritorio.

   	![][13]

7. También puede ver todas las notificaciones mediante la ventana de notificaciones de Chrome en la barra de tareas (en Windows) cuando Chrome se está ejecutando.

   	![][14]

## <a name="next-steps"> </a>Pasos siguientes

En este sencillo ejemplo, difundirá notificaciones a la aplicación Chrome. Obtenga más información sobre Centros de notificaciones en [Introducción a los centros de notificaciones]. Para dirigirse a usuarios específicos, consulte el tutorial [Uso de Centros de notificaciones para notificar a los usuarios]. Si desea segmentar a sus usuarios por grupos de interés, puede leer [Uso de Centros de notificaciones para enviar noticias de último minuto].

<!-- Images. -->
[1]: ./media/notification-hubs-chrome-get-started/GoogleConsoleCreateProject.PNG
[2]: ./media/notification-hubs-chrome-get-started/GoogleProjectNumber.png
[3]: ./media/notification-hubs-chrome-get-started/EnableGCM.png
[4]: ./media/notification-hubs-chrome-get-started/CreateServerKey.png
[5]: ./media/notification-hubs-chrome-get-started/ServerKey.png
[6]: ./media/notification-hubs-chrome-get-started/CreateNH.png
[7]: ./media/notification-hubs-chrome-get-started/NHNamespace.png
[8]: ./media/notification-hubs-chrome-get-started/NamespaceConfigure.png
[9]: ./media/notification-hubs-chrome-get-started/NHConfigure.png
[10]: ./media/notification-hubs-chrome-get-started/NHConfigureGCM.png
[11]: ./media/notification-hubs-chrome-get-started/NHDashboard.png
[12]: ./media/notification-hubs-chrome-get-started/NHConnString.png
[13]: ./media/notification-hubs-chrome-get-started/ChromeNotification.png
[14]: ./media/notification-hubs-chrome-get-started/ChromeNotificationWindow.png
[15]: ./media/notification-hubs-chrome-get-started/ChromeApp.png
[16]: ./media/notification-hubs-chrome-get-started/ChromeExtensions.png
[17]: ./media/notification-hubs-chrome-get-started/ChromeLoadExtension.png
[18]: ./media/notification-hubs-chrome-get-started/ChromeAppLoaded.png
[19]: ./media/notification-hubs-chrome-get-started/ChromeAppGCM.png
[20]: ./media/notification-hubs-chrome-get-started/ChromeAppNH.png
[21]: ./media/notification-hubs-chrome-get-started/FinalFolderView.png

<!-- URLs. -->
[ejemplo del centro de notificaciones de la aplicación Chrome]: http://google.com
[consola en la nube de Google]: http://cloud.google.com/console
[Portal de Azure clásico]: https://manage.windowsazure.com/
[Introducción a los centros de notificaciones]: http://msdn.microsoft.com/library/jj927170.aspx
[Información general sobre las aplicaciones Chrome]: https://developer.chrome.com/apps/about_apps
[ejemplo de GCM de la aplicación Chrome]: https://github.com/GoogleChrome/chrome-app-samples/tree/master/samples/gcm-notifications
[Aplicaciones web instalables]: https://developers.google.com/chrome/apps/docs/
[Aplicaciones de Chrome en dispositivos móviles]: https://developer.chrome.com/apps/chrome_apps_on_mobile
[Creación de una API de REST para el registro en el Centro de notificaciones]: http://msdn.microsoft.com/library/azure/dn223265.aspx
[biblioteca crypto-js]: http://code.google.com/p/crypto-js/
[GCM with Chrome Apps]: https://developer.chrome.com/apps/cloudMessaging
[Servicio de mensajería en la nube de Google para Chrome]: https://developer.chrome.com/apps/cloudMessagingV1
[Uso de Centros de notificaciones para notificar a los usuarios]: notification-hubs-aspnet-backend-windows-dotnet-notify-users.md
[Uso de Centros de notificaciones para enviar noticias de último minuto]: notification-hubs-windows-store-dotnet-send-breaking-news.md

<!---HONumber=AcomDC_0302_2016-->
