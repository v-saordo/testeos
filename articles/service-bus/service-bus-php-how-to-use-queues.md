<properties 
	pageTitle="Uso de colas del Bus de servicio con PHP | Microsoft Azure" 
	description="Obtenga información acerca de cómo usar las colas del Bus de servicio en Azure. Ejemplos de código escritos en PHP." 
	services="service-bus" 
	documentationCenter="php" 
	authors="sethmanheim" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="service-bus" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="01/26/2016" 
	ms.author="sethm"/>

# Utilización de las colas del Bus de servicio

[AZURE.INCLUDE [servicio de bus de selector de colas](../../includes/service-bus-selector-queues.md)]

En esta guía se muestra cómo usar las colas del Bus de servicio. Los ejemplos están escritos en PHP y utilizan el [SDK de Azure para PHP](../php-download-sdk.md). Entre los escenarios proporcionados se incluyen los siguientes: **creación de colas**, **envío y recepción de mensajes** y **eliminación de colas**.

[AZURE.INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## Creación de una aplicación PHP

El único requisito a la hora de crear una aplicación PHP para obtener acceso al servicio BLOB de Azure es que el código haga referencia a clases del [SDK de Azure para PHP](../php-download-sdk.md) dentro del código. Puede utilizar cualquier herramienta de desarrollo para crear la aplicación, o bien el Bloc de notas.

> [AZURE.NOTE] La instalación de PHP debe tener también la [extensión OpenSSL](http://php.net/openssl) instalada y habilitada.

En esta guía, utilizará funciones del servicio a las que se puede llamar desde una aplicación PHP localmente o bien mediante código a través de un rol web, rol de trabajo o sitio web de Azure.

## Obtención de las bibliotecas de clientes de Azure

[AZURE.INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## Configuración de la aplicación para usar el Bus de servicio

Para usar las API de la cola del Bus de servicio de Azure, siga estos pasos:

1. Hacer referencia al archivo autocargador mediante la instrucción [require\_once][require_once].
2. Hacer referencia a todas las clases que utilice.

En el siguiente ejemplo se muestra cómo incluir el archivo autocargador y hacer referencia a la clase **ServicesBuilder**.

> [AZURE.NOTE] En este ejemplo (así como en otros ejemplos de este artículo), se asume que ha instalado las bibliotecas de clientes PHP para Azure mediante el compositor. Si las instaló las bibliotecas manualmente o como un paquete PEAR, debe hacer referencia al archivo autocargador **WindowsAzure.php**.

	require_once 'vendor\autoload.php';
	use WindowsAzure\Common\ServicesBuilder;

En los ejemplos que aparecen a continuación, la instrucción `require_once` aparecerá siempre, pero solo se hará referencia a las clases necesarias para la ejecución del ejemplo.

## Configuración de una conexión del Bus de servicio

Para crear una instancia de un cliente del Bus de servicio, primero debe disponer de una cadena de conexión válida con el siguiente formato:

	Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]

Donde el **Endpoint** tiene generalmente el formato `https://[yourNamespace].servicebus.windows.net`.

Para crear cualquier cliente de servicio de Azure, debe usar la clase **ServicesBuilder**. Puede:

* pasarle directamente la cadena de conexión, o bien
* utilizar **CloudConfigurationManager (CCM)** para buscar la cadena de conexión en varios orígenes externos:
	* De manera predeterminada, admite un origen externo: variables de entorno.
	* Para agregar nuevos orígenes, amplíe la clase **ConnectionStringSource**.

En los ejemplos descritos aquí, la cadena de conexión se pasará directamente.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;

	$connectionString = "Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]";

	$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);


## Creación de una cola

Las operaciones de administración para las colas del Bus de servicio pueden realizarse mediante la clase **ServiceBusRestProxy**. Un objeto **ServiceBusRestProxy** se construye a través del método Factory Method **ServicesBuilder::createServiceBusService** con una cadena de conexión adecuada que encapsule los permisos de token para administrarlo.

En el ejemplo siguiente se muestra cómo crear una instancia de un **ServiceBusRestProxy** y llamar a **ServiceBusRestProxy->createQueue** para crear una cola llamada `myqueue` en un espacio de nombres de servicio `MySBNamespace`:

    require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\ServiceBus\Models\QueueInfo;

	// Create Service Bus REST proxy.
		$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
	
	try	{
		$queueInfo = new QueueInfo("myqueue");
		
		// Create queue.
		$serviceBusRestProxy->createQueue($queueInfo);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/windowsazure/dd179357
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

> [AZURE.NOTE] Puede usar el método `listQueues` en los objetos `ServiceBusRestProxy` para comprobar si ya existe una cola con un nombre especificado en un espacio de nombres de servicio.

## Envío de mensajes a una cola

Para enviar un mensaje a una cola del Bus de servicio, la aplicación llamará al método **ServiceBusRestProxy->sendQueueMessage**. El código siguiente muestra cómo enviar un mensaje a la cola `myqueue` que hemos creado anteriormente en el espacio de nombres de servicio `MySBNamespace`.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\ServiceBus\Models\BrokeredMessage;

	// Create Service Bus REST proxy.
	$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
		
	try	{
		// Create message.
		$message = new BrokeredMessage();
		$message->setBody("my message");
	
		// Send message.
		$serviceBusRestProxy->sendQueueMessage("myqueue", $message);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/windowsazure/hh780775
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

Los mensajes enviados a las colas del bus de servicio y recibidos en ellas son instancias de la clase **BrokeredMessage**. Los objetos **BrokeredMessage** tienen un conjunto de métodos estándar (por ejemplo, **getLabel**, **getTimeToLive**, **setLabel** y **setTimeToLive**) y las propiedades que se usan para mantener las propiedades específicas de la aplicación personalizada y un cuerpo de datos de aplicaciones arbitrarias.

Las colas del Bus de servicio admiten mensajes con un tamaño máximo de 256 KB (el encabezado, que incluye las propiedades estándar y personalizadas de la aplicación, puede tener como máximo un tamaño de 64 KB). No hay límite para el número de mensajes que contiene una cola, pero hay un tope para el tamaño total de los mensajes contenidos en una cola. Este límite superior para el tamaño de la cola es de 5 GB.

## Recepción de mensajes de una cola

La mejor forma de recibir mensajes desde una cola es usar un método **ServiceBusRestProxy->receiveQueueMessage**. Los mensajes se pueden recibir de dos modos distintos: **ReceiveAndDelete** (predeterminado) y **PeekLock**.

Al usar el modo **ReceiveAndDelete**, la operación de recepción consta de una sola fase; es decir, cuando el bus de servicio recibe una solicitud de lectura para un mensaje de la cola, marca el mensaje como consumido y lo devuelve a la aplicación. El modo **ReceiveAndDelete** es el modelo más sencillo y funciona mejor para los escenarios en los que una aplicación puede tolerar no procesar un mensaje en caso de error. Para entenderlo mejor, pongamos una situación en la que un consumidor emite la solicitud de recepción que se bloquea antes de procesarla. Como el Bus de servicio habrá marcado el mensaje como consumido, cuando la aplicación se reinicie y empiece a consumir mensajes de nuevo, habrá perdido el mensaje que se consumió antes del bloqueo.

En el modo **PeekLock**, la recepción de un mensaje se convierte en una operación de dos etapas, lo que hace posible admitir aplicaciones que no pueden tolerar la pérdida de mensajes. Cuando el Bus de servicio recibe una solicitud, busca el siguiente mensaje que se va a consumir, lo bloquea para impedir que otros consumidores lo reciban y, a continuación, lo devuelve a la aplicación. Una vez que la aplicación termina de procesar el mensaje (o lo almacena de forma confiable para su futuro procesamiento), completa la segunda fase del proceso de recepción pasando el mensaje recibido a **ServiceBusRestProxy->deleteMessage**. Cuando el bus de servicio ve la llamada **deleteMessage**, marca el mensaje como consumido y lo elimina de la cola.

En el ejemplo que aparece a continuación, se indica cómo se puede procesar y recibir un mensaje usando el modo **PeekLock** (no es el modo predeterminado).

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\ServiceBus\Models\ReceiveMessageOptions;

	// Create Service Bus REST proxy.
	$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
		
	try	{
		// Set the receive mode to PeekLock (default is ReceiveAndDelete).
		$options = new ReceiveMessageOptions();
		$options->setPeekLock();
		
		// Receive message.
		$message = $serviceBusRestProxy->receiveQueueMessage("myqueue", $options);
		echo "Body: ".$message->getBody()."<br />";
		echo "MessageID: ".$message->getMessageId()."<br />";
		
		/*---------------------------
			Process message here.
		----------------------------*/
		
		// Delete message. Not necessary if peek lock is not set.
		echo "Message deleted.<br />";
		$serviceBusRestProxy->deleteMessage($message);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here:
		// http://msdn.microsoft.com/library/windowsazure/hh780735
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

## Actuación ante errores de la aplicación y mensajes que no se pueden leer

El Bus de servicio proporciona una funcionalidad que le ayuda a superar sin problemas los errores de la aplicación o las dificultades para procesar un mensaje. Si por cualquier motivo una aplicación de recepción no puede procesar el mensaje, entonces realiza la llamada al método **unlockMessage** en el mensaje recibido (en lugar del método **deleteMessage**). Esto hará que el Bus de servicio desbloquee el mensaje de la cola y esté disponible para que pueda volver a recibirse, ya sea por la misma aplicación que lo consume o por otra.

También hay un tiempo de espera asociado con un mensaje bloqueado en la cola y, si la aplicación no puede procesar el mensaje antes de que finalice el tiempo de espera del bloqueo (por ejemplo, si la aplicación sufre un error), entonces el Bus de servicio desbloquea el mensaje automáticamente y hace que esté disponible para que pueda volver a recibirse.

En caso de que la aplicación se bloquee después de procesar el mensaje y antes de emitir la solicitud **deleteMessage**, entonces el mensaje se volverá a entregar a la aplicación cuando esta se reinicie. Esta posibilidad habitualmente se denomina **Al menos un procesamiento**, es decir, cada mensaje se procesará al menos una vez; aunque en determinadas situaciones podría volver a entregarse el mismo mensaje. Si el escenario no puede tolerar el procesamiento duplicado,entonces se recomienda la incorporación de lógica adicional a la aplicación para administrar la entrega de mensajes duplicados. Esto suele conseguirse usando el método **getMessageId** del mensaje, que permanecerá constante en todos los intentos de entrega.

## Pasos siguientes

Ahora que ya conoce los aspectos básicos de las colas de Bus de servicio, vea [Colas, temas y suscripciones][] para obtener más información.

Para obtener más información, consulte también el [Centro para desarrolladores de PHP](/develop/php/).

[Colas, temas y suscripciones]: service-bus-queues-topics-subscriptions.md
[require_once]: http://php.net/require_once

 

<!---HONumber=AcomDC_0128_2016-->