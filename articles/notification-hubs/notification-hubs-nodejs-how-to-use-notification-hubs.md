<properties
	pageTitle="Uso de los Centros de notificaciones con Node.js"
	description="Aprenda a usar los Centros de notificaciones para enviar notificaciones de inserción desde una aplicación Node.js."
	services="notification-hubs"
	documentationCenter="nodejs"
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="12/10/2015"
	ms.author="wesmc"/>

# Uso de los Centros de notificaciones desde Node.js
[AZURE.INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

##Información general

En esta guía se indica cómo usar los Centros de notificaciones desde aplicaciones Node.js. Los escenarios abordados incluyen el **envío de notificaciones a aplicaciones de Android, iOS, Windows Phone y la Tienda Windows**. Para obtener más información acerca de los centros de notificaciones, consulte la sección [Pasos siguientes](#next).

##¿Qué son los Centros de notificaciones?

Los Centros de notificaciones de Azure proporcionan una infraestructura multiplataforma escalable de fácil uso para enviar notificaciones de inserción a los dispositivos móviles. Para obtener más información, consulte [Centros de notificaciones de Azure](http://msdn.microsoft.com/library/windowsazure/jj927170.aspx).

##Creación de una aplicación Node.js

Cree una aplicación Node.js vacía. Para obtener instrucciones acerca de cómo crear una aplicación Node.js, consulte [Creación e implementación de una aplicación Node.js en un sitio web de Azure][nodejswebsite], [Servicio en la nube de Node.js][Node.js Cloud Service] (usando Windows PowerShell) o [Sitio web con WebMatrix].

##Configuración de su aplicación para usar el Centro de notificaciones

Para utilizar el Centro de notificaciones de Azure, es necesario descargar y usar el paquete azure de Node.js. Este paquete incluye un conjunto de prácticas bibliotecas que se comunican con los servicios REST.

### Uso del Administrador de paquetes para Node (NPM) para obtener el paquete

1.  Utilice una interfaz de línea de comandos como **PowerShell** (Windows), **Terminal** (Mac) o **Bash** (Unix) y vaya a la carpeta donde ha creado la aplicación de ejemplo.

2.  Escriba **npm install azure** en la ventana de comandos. Esto debería devolver la salida siguiente:

        azure@0.7.0 node_modules\azure
		|-- dateformat@1.0.2-1.2.3
		|-- xmlbuilder@0.4.2
		|-- node-uuid@1.2.0
		|-- mime@1.2.9
		|-- underscore@1.4.4
		|-- validator@0.4.28
		|-- tunnel@0.0.2
		|-- wns@0.5.3
		|-- xml2js@0.2.6 (sax@0.4.2)
		|-- request@2.16.6 (forever-agent@0.2.0, aws-sign@0.2.0, tunnel-agent@0.2.0, oauth-sign@0.2.0, json-stringify-safe@3.0.0, cookie-jar@0.2.0, node-uuid@1.4.0, qs@0.5.5, hawk@0.10.2, form-data@0.0.7)

3.  Puede ejecutar manualmente el comando **ls** o **dir** para comprobar si se ha creado la carpeta **node\_modules**. Dentro de dicha carpeta, encontrará el paquete **azure**, que contiene las bibliotecas necesarias para el acceso al Centro de notificaciones.

### Importación del módulo

Con un editor de texto, agregue el código siguiente en la parte superior del archivo **server.js** de la aplicación:

    var azure = require('azure');

### Configuración de una conexión de Centro de notificaciones de Azure

El objeto **NotificationHubService** le permite trabajar con los Centros de notificaciones. El código siguiente crea un objeto **NotificationHubService** para el Centro de notificaciones denominado **hubname**. Agréguelo cerca de la parte superior del archivo **server.js**, tras la instrucción para importar el módulo azure:

    var notificationHubService = azure.createNotificationHubService('hubname','connectionstring');

El valor de conexión **connectionstring** se puede obtener en el [Portal de Azure clásico] mediante los siguientes pasos:

1. En el [Portal de Azure clásico], seleccione **Bus de servicio** y luego seleccione el espacio de nombres que contiene el centro de notificaciones.

2. Seleccione **CENTROS DE NOTIFICACIONES** y, a continuación, elija el centro que desea usar.

3. Seleccione **Ver cadena de conexión** en la sección **vista rápida** y copie el valor de la cadena de conexión.

> [AZURE.NOTE]Para recuperar la cadena de conexión, también puede usar el cmdlet **Get-AzureSbNamespace** que proporciona Azure PowerShell o el comando **azure sb namespace show** con las herramientas de línea de comandos de Azure (CLI de Azure).

</div>

##Envío de notificaciones

El objeto **NotificationHubService** expone las siguientes instancias de objeto para enviar notificaciones a dispositivos y aplicaciones específicos:

* **Android**: use el objeto **GcmService**, disponible en **notificationHubService.gcm**
* **iOS**: use el objeto **ApnsService**, al que se puede obtener acceso en **notificationHubService.apns**
* **Windows Phone**: use el objeto **MpnsService**, disponible en **notificationHubService.mpns**
* **Aplicaciones de la Tienda Windows**: use el objeto **WnsService**, disponible en **notificationHubService.wns**

### Envío de notificaciones a aplicaciones de Android

El objeto **GcmService** proporciona un método **send** que se puede usar para enviar notificaciones a las aplicaciones de Android. El método **send** acepta los siguientes parámetros:

* Tags: identificador de etiqueta. Si no se proporciona ninguna, la notificación se enviará a todos los clientes.
* Payload: carga de cadena o JSON del mensaje.
* Callback: función de devolución de llamada.

Para obtener más información acerca del formato de carga, consulte la sección Carga de [Implementación del servidor de GCM](http://developer.android.com/google/gcm/server.html#payload).

El código siguiente usa la instancia **GcmService** expuesta por **NotificationHubService** para enviar un mensaje a todos los clientes.

	var payload = {
	  data: {
	    msg: 'Hello!'
	  }
	};
	notificationHubService.gcm.send(null, payload, function(error){
	  if(!error){
	    //notification sent
	  }
	});

### Envío de notificaciones a aplicaciones de iOS

El objeto **ApnsService** proporciona un método **send** que se puede usar para enviar notificaciones a las aplicaciones de iOS. El método **send** acepta los siguientes parámetros:

* Tags: identificador de etiqueta. Si no se proporciona ninguna, la notificación se enviará a todos los clientes.
* Payload: carga de cadena o JSON del mensaje.
* Callback: función de devolución de llamada.

Para obtener más información acerca del formato de carga, consulte la sección de carga de notificaciones de la [Guía de programación de notificaciones de inserción y locales](http://developer.apple.com/library/ios/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html).

El código siguiente usa la instancia **ApnsService** expuesta por **NotificationHubService** para enviar un mensaje de alerta a todos los clientes:

	var payload={
	    alert: 'Hello!'
	  };
	notificationHubService.apns.send(null, payload, function(error){
	  if(!error){
 	    // notification sent
      }
	});

### Envío de notificaciones a aplicaciones de Windows Phone

El objeto **MpnsService** proporciona un método **send** que se puede usar para enviar notificaciones a las aplicaciones de Windows Phone. El método **send** acepta los siguientes parámetros:

* Tags: identificador de etiqueta. Si no se proporciona ninguna, la notificación se enviará a todos los clientes.
* Payload: carga XML del mensaje.
* TargetName: 'toast' para notificaciones de sistema. 'token' para notificaciones de icono.
* NotificationClass: prioridad de la notificación. Consulte la sección sobre elementos de encabezados HTTP del tema [Introducción de notificaciones desde un servidor](http://msdn.microsoft.com/library/hh221551.aspx) para obtener los valores válidos.
* Options: encabezados de solicitud opcionales.
* Callback: función de devolución de llamada.

Para obtener una lista de valores TargetName, NotificationClass y opciones de encabezado válidos, consulte [Notificaciones de inserción desde un servidor](http://msdn.microsoft.com/library/hh221551.aspx).

El código siguiente usa la instancia **MpnsService** expuesta por **NotificationHubService** para enviar una alerta del sistema:

	var payload = '<?xml version="1.0" encoding="utf-8"?><wp:Notification xmlns:wp="WPNotification"><wp:Toast><wp:Text1>string</wp:Text1><wp:Text2>string</wp:Text2></wp:Toast></wp:Notification>';
	notificationHubService.mpns.send(null, payload, 'toast', 22, function(error){
	  if(!error){
	    //notification sent
	  }
	});

### Envío de notificaciones a aplicaciones de la Tienda Windows

El objeto **WnsService** proporciona un método **send** que se puede usar para enviar notificaciones a las aplicaciones de la Tienda Windows. El método **send** acepta los siguientes parámetros:

* Tags: identificador de etiqueta. Si no se proporciona ninguna, la notificación se enviará a todos los clientes.
* Payload: carga del mensaje XML.
* Type: tipo de notificación.
* Options: encabezados de solicitud opcionales.
* Callback: función de devolución de llamada.

Para obtener una lista de valores Type y encabezados de solicitud válidos, consulte [Encabezados de solicitud y respuesta de servicio de notificaciones de inserción](http://msdn.microsoft.com/library/windows/apps/hh465435.aspx).

El código siguiente usa la instancia **WnsService** expuesta por **NotificationHubService** para enviar una alerta del sistema:

	var payload = '<toast><visual><binding template="ToastText01"><text id="1">Hello!</text></binding></visual></toast>';
	notificationHubService.wns.send(null, payload , 'wns/toast', function(error){
	  if(!error){
 	    // notification sent
	  }
	});

## Pasos siguientes

Ahora que conoce los fundamentos de uso de los Centros de notificaciones, consulte estos vínculos para obtener más información.

-   Consulte la referencia MSDN: [Centros de notificaciones de Azure][].
-   Visite el repositorio del [SDK de Azure para Node] en GitHub.

  [SDK de Azure para Node]: https://github.com/WindowsAzure/azure-sdk-for-node
  [Next Steps]: #nextsteps
  [What are Service Bus Topics and Subscriptions?]: #what-are-service-bus-topics
  [Create a Service Namespace]: #create-a-service-namespace
  [Obtain the Default Management Credentials for the Namespace]: #obtain-default-credentials
  [Create a Node.js Application]: #Create_a_Nodejs_Application
  [Configure Your Application to Use Service Bus]: #Configure_Your_Application_to_Use_Service_Bus
  [How to: Create a Topic]: #How_to_Create_a_Topic
  [How to: Create Subscriptions]: #How_to_Create_Subscriptions
  [How to: Send Messages to a Topic]: #How_to_Send_Messages_to_a_Topic
  [How to: Receive Messages from a Subscription]: #How_to_Receive_Messages_from_a_Subscription
  [How to: Handle Application Crashes and Unreadable Messages]: #How_to_Handle_Application_Crashes_and_Unreadable_Messages
  [How to: Delete Topics and Subscriptions]: #How_to_Delete_Topics_and_Subscriptions
  [1]: #Next_Steps
  [Topic Concepts]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-topics-01.png
  [Portal de Azure clásico]: http://manage.windowsazure.com
  [image]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-03.png
  [2]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-04.png
  [3]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-05.png
  [4]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-06.png
  [5]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-07.png
  [SqlFilter.SqlExpression]: http://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [Azure Service Bus Notification Hubs]: http://msdn.microsoft.com/library/windowsazure/jj927170.aspx
  [SqlFilter]: http://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx
  [Sitio web con WebMatrix]: /develop/nodejs/tutorials/web-site-with-webmatrix/
  [Node.js Cloud Service]: ../cloud-services-nodejs-develop-deploy-app.md
[Previous Management Portal]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/previous-portal.png
  [nodejswebsite]: /develop/nodejs/tutorials/create-a-website-(mac)/
  [Node.js Cloud Service with Storage]: /develop/nodejs/tutorials/web-app-with-storage/
  [Node.js Web Application with Storage]: /develop/nodejs/tutorials/web-site-with-storage/

<!---HONumber=AcomDC_1217_2015-->