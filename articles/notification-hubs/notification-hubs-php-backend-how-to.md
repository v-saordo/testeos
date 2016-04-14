<properties 
	pageTitle="Uso de los Centros de notificaciones con PHP" 
	description="Obtenga información acerca de cómo usar los Centros de notificaciones de Azure desde un back-end de PHP." 
	services="notification-hubs" 
	documentationCenter="" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="php" 
	ms.devlang="php" 
	ms.topic="article" 
	ms.date="02/29/2016" 
	ms.author="wesmc"/>

# Uso de los Centros de notificaciones desde PHP
[AZURE.INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

Puede acceder a todas las características de los Centros de notificaciones desde un back-end de Java, PHP o Ruby usando la interfaz REST de Centros de notificaciones tal como se describe en el tema de MSDN [API de REST de Centros de notificaciones](http://msdn.microsoft.com/library/dn223264.aspx).

En este tema le mostraremos cómo:

* Crear un cliente REST para las características de Centros de notificaciones en PHP;
* Siga el [tutorial introductorio](notification-hubs-ios-get-started.md) para la plataforma móvil que elija, implementando la parte del back-end en PHP.

## Interfaz del cliente
La interfaz del cliente principal puede proporcionar los mismos métodos disponibles en el [SDK de Centros de notificaciones .NET](http://msdn.microsoft.com/library/jj933431.aspx), que le permitirá trasladar todos los tutoriales y ejemplos actualmente disponibles en este sitio directamente y con el que contribuye la comunidad en Internet.

Puede encontrar todo el código disponible en el [ejemplo de contenedor REST para PHP].

Por ejemplo, para crear un cliente:

	$hub = new NotificationHub("connection string", "hubname");	

Para enviar una notificación nativa de iOS:
	
	$notification = new Notification("apple", '{"aps":{"alert": "Hello!"}}');
	$hub->sendNotification($notification);

## Implementación
Si todavía no lo ha hecho, siga nuestro [tutorial de introducción] hasta la última sección en la que tiene que implementar el back-end. Asimismo, si lo desea, puede usar el código del [ejemplo de contenedor REST para PHP] e ir directamente a la sección [Finalización del tutorial](#complete-tutorial).

Puede encontrar todos los detalles para implementar un contenedor REST completo en [MSDN](http://msdn.microsoft.com/library/dn530746.aspx). En esta sección describiremos la implementación para PHP de los principales pasos requeridos para acceder a extremos REST de Centros de notificaciones:

1. Análisis de la cadena de conexión
2. Generación del token de autenticación
3. Realización de la llamada HTTP

### Análisis de la cadena de conexión

Esta es la clase principal que implementa el cliente, cuyo constructor analiza la cadena de conexión:

	class NotificationHub {
		const API_VERSION = "?api-version=2013-10";
	
		private $endpoint;
		private $hubPath;
		private $sasKeyName;
		private $sasKeyValue;
	
		function __construct($connectionString, $hubPath) {
			$this->hubPath = $hubPath;
	
			$this->parseConnectionString($connectionString);
		}
	
		private function parseConnectionString($connectionString) {
			$parts = explode(";", $connectionString);
			if (sizeof($parts) != 3) {
				throw new Exception("Error parsing connection string: " . $connectionString);
			}
	
			foreach ($parts as $part) {
				if (strpos($part, "Endpoint") === 0) {
					$this->endpoint = "https" . substr($part, 11);
				} else if (strpos($part, "SharedAccessKeyName") === 0) {
					$this->sasKeyName = substr($part, 20);
				} else if (strpos($part, "SharedAccessKey") === 0) {
					$this->sasKeyValue = substr($part, 16);
				}
			}
		}
	}


### Creación del token de seguridad
Los detalles de la creación del token de seguridad están disponibles [aquí](http://msdn.microsoft.com/library/dn495627.aspx). El siguiente método tiene que agregarse a la clase **NotificationHub** para crear el token basándose en el URI de la solicitud actual y en las credenciales extraídas de la cadena de conexión.

	private function generateSasToken($uri) {
		$targetUri = strtolower(rawurlencode(strtolower($uri)));

		$expires = time();
		$expiresInMins = 60;
		$expires = $expires + $expiresInMins * 60;
		$toSign = $targetUri . "\n" . $expires;

		$signature = rawurlencode(base64_encode(hash_hmac('sha256', $toSign, $this->sasKeyValue, TRUE)));

		$token = "SharedAccessSignature sr=" . $targetUri . "&sig="
					. $signature . "&se=" . $expires . "&skn=" . $this->sasKeyName;

		return $token;
	}

### Envío de una notificación
En primer lugar, definamos una clase que representa una notificación.

	class Notification {
		public $format;
		public $payload;
	
		# array with keynames for headers
		# Note: Some headers are mandatory: Windows: X-WNS-Type, WindowsPhone: X-NotificationType
		# Note: For Apple you can set Expiry with header: ServiceBusNotification-ApnsExpiry in W3C DTF, YYYY-MM-DDThh:mmTZD (for example, 1997-07-16T19:20+01:00).
		public $headers;
	
		function __construct($format, $payload) {
			if (!in_array($format, ["template", "apple", "windows", "gcm", "windowsphone"])) {
				throw new Exception('Invalid format: ' . $format);
			}
	
			$this->format = $format;
			$this->payload = $payload;
		}
	}

Esta clase es un contenedor para un cuerpo de notificación nativa, o un conjunto de propiedades en el caso de una notificación de plantilla, y un conjunto de encabezados que contienen formato (plataforma o plantilla nativa) y propiedades específicas de la plataforma (como la propiedad de expiración de Apple y los encabezados WNS).

Consulte la [documentación de las API de REST de Centros de notificaciones](http://msdn.microsoft.com/library/dn495827.aspx) y los formatos de las plataformas de notificación específicas para todas las opciones disponibles.

Con esta clase, ahora podemos escribir los métodos de envío de notificaciones dentro de la clase **NotificationHub**.

	public function sendNotification($notification, $tagsOrTagExpression="") {
		if (is_array($tagsOrTagExpression)) {
			$tagExpression = implode(" || ", $tagsOrTagExpression);
		} else {
			$tagExpression = $tagsOrTagExpression;
		}

		# build uri
		$uri = $this->endpoint . $this->hubPath . "/messages" . NotificationHub::API_VERSION;
		$ch = curl_init($uri);

		if (in_array($notification->format, ["template", "apple", "gcm"])) {
			$contentType = "application/json";
		} else {
			$contentType = "application/xml";
		}

		$token = $this->generateSasToken($uri);

		$headers = [
		    'Authorization: '.$token,
		    'Content-Type: '.$contentType,
		    'ServiceBusNotification-Format: '.$notification->format
		];

		if ("" !== $tagExpression) {
			$headers[] = 'ServiceBusNotification-Tags: '.$tagExpression;
		}

		# add headers for other platforms
		if (is_array($notification->headers)) {
			$headers = array_merge($headers, $notification->headers);
		}
		
		curl_setopt_array($ch, array(
		    CURLOPT_POST => TRUE,
		    CURLOPT_RETURNTRANSFER => TRUE,
		    CURLOPT_SSL_VERIFYPEER => FALSE,
		    CURLOPT_HTTPHEADER => $headers,
		    CURLOPT_POSTFIELDS => $notification->payload
		));

		// Send the request
		$response = curl_exec($ch);

		// Check for errors
		if($response === FALSE){
		    throw new Exception(curl_error($ch));
		}

		$info = curl_getinfo($ch);

		if ($info['http_code'] <> 201) {
			throw new Exception('Error sending notificaiton: '. $info['http_code'] . ' msg: ' . $response);
		}
	} 

Los métodos anteriores envían una solicitud POST HTTP al extremo /messages del centro de notificaciones, con el cuerpo y encabezados correctos para enviar la notificación.

##<a name="complete-tutorial"></a>Finalización del tutorial
Ahora puede completar el tutorial introductorio enviando la notificación desde un back-end de PHP.

Inicialice el cliente de Centros de notificaciones (reemplace la cadena de conexión y el nombre del centro tal como se indica en el [tutorial introductorio]):

	$hub = new NotificationHub("connection string", "hubname");	

Después, agregue el código de envío dependiendo de la plataforma móvil de destino.

### Tienda Windows y Windows Phone 8.1 (no Silverlight)

	$toast = '<toast><visual><binding template="ToastText01"><text id="1">Hello from PHP!</text></binding></visual></toast>';
	$notification = new Notification("windows", $toast);
	$notification->headers[] = 'X-WNS-Type: wns/toast';
	$hub->sendNotification($notification);

### iOS

	$alert = '{"aps":{"alert":"Hello from PHP!"}}';
	$notification = new Notification("apple", $alert);
	$hub->sendNotification($notification);

### Android
	$message = '{"data":{"msg":"Hello from PHP!"}}';
	$notification = new Notification("gcm", $message);
	$hub->sendNotification($notification);

### Silverlight para Windows Phone 8.0 y 8.1

	$toast = '<?xml version="1.0" encoding="utf-8"?>' .
		        '<wp:Notification xmlns:wp="WPNotification">' .
		           '<wp:Toast>' .
		                '<wp:Text1>Hello from PHP!</wp:Text1>' .
		           '</wp:Toast> ' .
		        '</wp:Notification>';
	$notification = new Notification("windowsphone", $toast);
	$notification->headers[] = 'X-WindowsPhone-Target : toast';
	$notification->headers[] = 'X-NotificationClass : 2';
	$hub->sendNotification($notification);


### Kindle Fire
	$message = '{"data":{"msg":"Hello from PHP!"}}';
	$notification = new Notification("adm", $message);
	$hub->sendNotification($notification);

La ejecución del código de PHP debe generar ahora una notificación que aparece en el dispositivo de destino.


## Pasos siguientes
En este tema hemos mostrado cómo crear un simple cliente REST en Java para Centros de notificaciones. Desde aquí puede:

* Descargar el [ejemplo de contenedor REST para PHP] completo, que contiene todo el código anterior.
* Continuar aprendiendo sobre la característica de etiquetado de Centros de notificaciones en el [tutorial Noticias de última hora]
* Obtener más información sobre notificaciones de inserción para usuarios individuales en el [tutorial Notificar a los usuarios]

Para obtener más información, consulte también el [Centro para desarrolladores de PHP](/develop/php/).

[ejemplo de contenedor REST para PHP]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-php
[tutorial de introducción]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
[tutorial introductorio]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
 

<!---HONumber=AcomDC_0302_2016-->