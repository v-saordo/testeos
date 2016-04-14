<properties 
	pageTitle="Uso de temas del Bus de servicio con PHP | Microsoft Azure" 
	description="Obtenga información acerca de cómo usar los temas del Bus de servicio con PHP en Azure." 
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
	ms.date="02/08/2016" 
	ms.author="sethm"/>


# Uso de temas/suscripciones del Bus de servicio

[AZURE.INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

En este artículo se indica cómo usar los temas y las suscripciones del Bus de servicio. Los ejemplos están escritos en PHP y utilizan el [SDK de Azure para PHP](../php-download-sdk.md). Entre los escenarios tratados se incluye la **creación de temas y suscripciones**, la **creación de filtros de suscripción**, el **envío de mensajes a un tema**, la **recepción de mensajes de una suscripción** y la **eliminación de temas y suscripciones**.

[AZURE.INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

## Creación de una aplicación PHP

El único requisito a la hora de crear una aplicación PHP para obtener acceso al servicio BLOB de Azure es que se haga referencia a clases del [SDK de Azure para PHP](../php-download-sdk.md) dentro del código. Puede utilizar cualquier herramienta de desarrollo para crear la aplicación, o bien el Bloc de notas.

> [AZURE.NOTE] La instalación de PHP debe tener también la [extensión OpenSSL](http://php.net/openssl) instalada y habilitada.

En este artículo, se describe cómo se usan las funciones del servicio a las que se puede llamar desde una aplicación PHP localmente o bien mediante código a través de un rol web, rol de trabajo o sitio web de Azure.

## Obtención de las bibliotecas de clientes de Azure

[AZURE.INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## Configuración de la aplicación para usar el Bus de servicio

Para usar las API del Bus de servicio:

1. Hacer referencia al archivo autocargador mediante la instrucción [require\_once][require-once].
2. Hacer referencia a todas las clases que utilice.

En el siguiente ejemplo se muestra cómo incluir el archivo autocargador y hacer referencia a la clase **ServiceBusService**.

> [AZURE.NOTE] En este ejemplo (así como en otros ejemplos de este artículo), se asume que ha instalado las bibliotecas de clientes PHP para Azure mediante el compositor. Si las instaló manualmente o como paquete PEAR, debe hacer referencia al archivo autocargador **WindowsAzure.php**.

```
require_once 'vendor\autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

En los ejemplos siguientes, la instrucción `require_once` aparecerá siempre, pero solo se hará referencia a las clases necesarias para la ejecución del ejemplo.

## Configuración de una conexión del Bus de servicio

Para crear una instancia de un cliente del Bus de servicio de Azure, primero debe disponer de una cadena de conexión válida con el siguiente formato:

```
Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]
```

Donde `Endpoint` tiene generalmente el formato `https://[yourNamespace].servicebus.windows.net`.

Para crear un cliente de cualquier servicio de Azure, debe usar la clase **ServicesBuilder**. Puede:

* pasarle directamente la cadena de conexión, o bien
* utilizar **CloudConfigurationManager (CCM)** para buscar la cadena de conexión en varios orígenes externos:
	* De manera predeterminada, admite un origen externo: variables de entorno.
	* Para agregar nuevos orígenes, amplíe la clase **ConnectionStringSource**.

En los ejemplos descritos aquí, la cadena de conexión se pasa directamente.

```
require_once 'vendor\autoload.php';

use WindowsAzure\Common\ServicesBuilder;
	
$connectionString = "Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]";

$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
```

## de un tema

Las operaciones de administración para los temas del Bus de servicio pueden realizarse mediante la clase **ServiceBusRestProxy**. Un objeto **ServiceBusRestProxy** se construye a través del método Factory Method **ServicesBuilder::createServiceBusService** con una cadena de conexión adecuada que encapsule los permisos de token para administrarlo.

En el ejemplo siguiente, se muestra cómo crear una instancia de **ServiceBusRestProxy** y llamar a **ServiceBusRestProxy->createTopic** para crear un tema llamado `mytopic` dentro de un espacio de nombres `MySBNamespace`:

```
require_once 'vendor\autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\TopicInfo;
	
// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
	
try	{		
	// Create topic.
	$topicInfo = new TopicInfo("mytopic");
	$serviceBusRestProxy->createTopic($topicInfo);
}
catch(ServiceException $e){
	// Handle exception based on error codes and messages.
	// Error codes and messages are here: 
	// http://msdn.microsoft.com/library/windowsazure/dd179357
	$code = $e->getCode();
	$error_message = $e->getMessage();
	echo $code.": ".$error_message."<br />";
}
```

> [AZURE.NOTE] Puede usar el método `listTopics` en los objetos `ServiceBusRestProxy` para comprobar si ya existe un tema con un nombre especificado en un espacio de nombres de servicio.

## una suscripción

Las suscripciones a temas también se crean con el método **ServiceBusRestProxy->createSubscription**. A las suscripciones se les puede asignar un nombre y pueden tener un filtro opcional que restrinja el conjunto de mensajes que pasan a la cola virtual de la suscripción.

### Creación de una suscripción con el filtro predeterminado (MatchAll)

El filtro predeterminado **MatchAll** se usa en caso de que no se haya especificado ninguno al crear una suscripción. Al usar el filtro **MatchAll**, todos los mensajes publicados en el tema se colocan en la cola virtual de la suscripción. En el ejemplo siguiente se crea una suscripción llamada "mysubscription" que usa el filtro predeterminado **MatchAll**.

```
require_once 'vendor\autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\SubscriptionInfo;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
	
try	{
	// Create subscription.
	$subscriptionInfo = new SubscriptionInfo("mysubscription");
	$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);
}
catch(ServiceException $e){
	// Handle exception based on error codes and messages.
	// Error codes and messages are here: 
	// http://msdn.microsoft.com/library/azure/dd179357
	$code = $e->getCode();
	$error_message = $e->getMessage();
	echo $code.": ".$error_message."<br />";
}
```

### Creación de suscripciones con filtros

También puede configurar filtros que le permitan especificar qué mensajes enviados a un tema deben aparecer dentro de una suscripción a un tema determinado. El tipo de filtro más flexible compatible con las suscripciones es **SqlFilter**, que implementa un subconjunto de SQL92. Los filtros de SQL operan en las propiedades de los mensajes que se publican en el tema. Para obtener más información sobre los SqlFilter, consulte [Propiedad SqlFilter.SqlExpression][sqlfilter].

> [AZURE.NOTE] Cada regla en una suscripción procesa los mensajes entrantes de forma independiente, lo que agrega los mensajes de resultados a la suscripción. Además, cada nueva suscripción dispone de objeto **Regla** predeterminado con un filtro que agrega todos los mensajes del tema a la suscripción. Para recibir solo mensajes que coincidan con su filtro, debe quitar la regla predeterminada. Puede eliminar la regla predeterminada mediante el método `ServiceBusRestProxy->deleteRule`.

En el ejemplo siguiente se crea una suscripción llamada **HighMessages** con un filtro **SqlFilter** que solo selecciona mensajes que tienen una propiedad **MessageNumber** personalizada mayor que 3 (consulte [Envío de mensajes a un tema](#send-messages-to-a-topic) para obtener más información sobre la incorporación de propiedades personalizadas a los mensajes):

```
$subscriptionInfo = new SubscriptionInfo("HighMessages");
$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

$serviceBusRestProxy->deleteRule("mytopic", "HighMessages", '$Default');

$ruleInfo = new RuleInfo("HighMessagesRule");
$ruleInfo->withSqlFilter("MessageNumber > 3");
$ruleResult = $serviceBusRestProxy->createRule("mytopic", "HighMessages", $ruleInfo);
```

Tenga en cuenta que este código requiere el uso de un espacio de nombres adicional: `WindowsAzure\ServiceBus\Models\SubscriptionInfo`.

Del mismo modo, en el ejemplo que aparece a continuación, se crea una suscripción llamada **LowMessages** con un **SqlFilter** que solo selecciona los mensajes con una propiedad **MessageNumber** menor o igual a 3:

```
$subscriptionInfo = new SubscriptionInfo("LowMessages");
$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

$serviceBusRestProxy->deleteRule("mytopic", "LowMessages", '$Default');

$ruleInfo = new RuleInfo("LowMessagesRule");
$ruleInfo->withSqlFilter("MessageNumber <= 3");
$ruleResult = $serviceBusRestProxy->createRule("mytopic", "LowMessages", $ruleInfo);
```

Ahora, cuando se envía un mensaje al tema `mytopic`, siempre se entrega a los destinatarios suscritos a la suscripción `mysubscription` y selectivamente a los destinatarios suscritos a las suscripciones `HighMessages` y `LowMessages` (en función del contenido del mensaje).

## de mensajes a un tema

Para enviar un mensaje a un tema del Bus de servicio, la aplicación llama al método **ServiceBusRestProxy->sendTopicMessage**. El código siguiente muestra cómo enviar un mensaje al tema `mytopic` que hemos creado anteriormente en el espacio de nombres de servicio `MySBNamespace`.

```
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
	$serviceBusRestProxy->sendTopicMessage("mytopic", $message);
}
catch(ServiceException $e){
	// Handle exception based on error codes and messages.
	// Error codes and messages are here: 
	// http://msdn.microsoft.com/library/azure/hh780775
	$code = $e->getCode();
	$error_message = $e->getMessage();
	echo $code.": ".$error_message."<br />";
}
```

Los mensajes enviados a los temas de Bus de servicio son instancias de la clase **BrokeredMessage**. Los objetos **BrokeredMessage** cuentan con un conjunto de métodos y propiedades estándar (como **getLabel**, **getTimeToLive**, **setLabel** y **setTimeToLive**), así como propiedades que pueden usarse para mantener propiedades personalizadas específicas de la aplicación. En el ejemplo siguiente se muestra cómo enviar cinco mensajes de prueba al tema `mytopic` creado anteriormente. El método **setProperty** se usa para agregar una propiedad personalizada (`MessageNumber`) a cada mensaje. Tenga en cuenta que el valor de la propiedad `MessageNumber` varía en cada mensaje (puede usar este valor para determinar qué suscripciones lo reciben, como se muestra en la sección [Creación de una suscripción](#create-a-subscription)):

```
for($i = 0; $i < 5; $i++){
	// Create message.
	$message = new BrokeredMessage();
	$message->setBody("my message ".$i);
			
	// Set custom property.
	$message->setProperty("MessageNumber", $i);
			
	// Send message.
	$serviceBusRestProxy->sendTopicMessage("mytopic", $message);
}
```

Las colas del Bus de servicio admiten mensajes con un tamaño máximo de 256 KB (el encabezado, que incluye las propiedades estándar y personalizadas de la aplicación, puede tener como máximo un tamaño de 64 KB). No hay límite para el número de mensajes que contiene una cola, pero hay un tope para el tamaño total de los mensajes contenidos en una cola. Este límite superior para el tamaño de la cola es de 5 GB. Para obtener más información sobre las cuotas, consulte [Colas de Bus de servicio y colas de Azure][].

## de mensajes de una suscripción

La forma principal de recibir mensajes de una suscripción es usar un método **ServiceBusRestProxy->receiveSubscriptionMessage**. Los mensajes recibidos pueden funcionar en dos modos distintos: **ReceiveAndDelete** (predeterminado) y **PeekLock**.

Al usar el modo **ReceiveAndDelete**, la operación de recepción consta de una sola fase; es decir, cuando el Bus de servicio recibe una solicitud de lectura para un mensaje en una suscripción, marca el mensaje como consumido y lo devuelve a la aplicación. El modo **ReceiveAndDelete** es el modelo más sencillo y funciona mejor para los escenarios en los que una aplicación puede tolerar no procesar un mensaje en caso de error. Para entenderlo mejor, pongamos una situación en la que un consumidor emite la solicitud de recepción que se bloquea antes de procesarla. Como el Bus de servicio habrá marcado el mensaje como consumido, cuando la aplicación se reinicie y empiece a consumir mensajes de nuevo, habrá perdido el mensaje que se consumió antes del bloqueo.

En el modo **PeekLock**, la recepción de un mensaje se convierte en una operación de dos etapas, lo que hace posible admitir aplicaciones que no pueden tolerar la pérdida de mensajes. Cuando el Bus de servicio recibe una solicitud, busca el siguiente mensaje que se va a consumir, lo bloquea para impedir que otros consumidores lo reciban y, a continuación, lo devuelve a la aplicación. Una vez que la aplicación termina de procesar el mensaje (o lo almacena de forma confiable para su futuro procesamiento), completa la segunda fase del proceso de recepción pasando el mensaje recibido a **ServiceBusRestProxy->deleteMessage**. Cuando el bus de servicio ve la llamada **deleteMessage**, marca el mensaje como consumido y lo elimina de la cola.

En el ejemplo que aparece a continuación, se indica cómo se puede recibir y procesar un mensaje usando el modo **PeekLock** (no es el modo predeterminado).

```
require_once 'vendor\autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\ReceiveMessageOptions;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
		
try	{
	// Set receive mode to PeekLock (default is ReceiveAndDelete)
	$options = new ReceiveMessageOptions();
	$options->setPeekLock();
	
	// Get message.
	$message = $serviceBusRestProxy->receiveSubscriptionMessage("mytopic", "mysubscription", $options);

	echo "Body: ".$message->getBody()."<br />";
	echo "MessageID: ".$message->getMessageId()."<br />";
		
	/*---------------------------
		Process message here.
	----------------------------*/
		
	// Delete message. Not necessary if peek lock is not set.
	echo "Deleting message...<br />";
	$serviceBusRestProxy->deleteMessage($message);
}
catch(ServiceException $e){
	// Handle exception based on error codes and messages.
	// Error codes and messages are here:
	// http://msdn.microsoft.com/library/azure/hh780735
	$code = $e->getCode();
	$error_message = $e->getMessage();
	echo $code.": ".$error_message."<br />";
}
```

## Actuación ante errores de la aplicación y mensajes que no se pueden leer

El Bus de servicio proporciona una funcionalidad que le ayuda a superar sin problemas los errores de la aplicación o las dificultades para procesar un mensaje. Si por cualquier motivo una aplicación de recepción no puede procesar el mensaje, entonces realiza la llamada al método **unlockMessage** en el mensaje recibido (en lugar del método **deleteMessage**). Esto hará que el Bus de servicio desbloquee el mensaje de la cola y esté disponible para que pueda volver a recibirse, ya sea por la misma aplicación que lo consume o por otra.

También hay un tiempo de espera asociado con un mensaje bloqueado en la cola y, si la aplicación no puede procesar el mensaje antes de que finalice el tiempo de espera del bloqueo (por ejemplo, si la aplicación sufre un error), entonces el Bus de servicio desbloquea el mensaje automáticamente y hace que esté disponible para que pueda volver a recibirse.

En caso de que la aplicación se bloquee después de procesar el mensaje y antes de emitir la solicitud **deleteMessage**, entonces el mensaje se volverá a entregar a la aplicación cuando esta se reinicie. Esta posibilidad habitualmente se denomina **Al menos un procesamiento**, es decir, cada mensaje se procesará al menos una vez; aunque en determinadas situaciones podría volver a entregarse el mismo mensaje. Si el escenario no puede tolerar el procesamiento duplicado, entonces los desarrolladores de la aplicación deberían agregar lógica adicional a las aplicaciones para solucionar la entrega de mensajes duplicados. Esto suele conseguirse usando el método **getMessageId** del mensaje, que permanecerá constante en todos los intentos de entrega.

## Eliminación de temas y suscripciones

Para eliminar un tema o una suscripción, use los métodos **ServiceBusRestProxy->deleteTopic** o **ServiceBusRestProxy->deleteSubscripton**, respectivamente. Tenga en cuenta que al eliminar un tema también se eliminan todas las suscripciones que estén registradas con él.

En el ejemplo siguiente se muestra cómo eliminar un tema denominado `mytopic` y las suscripciones registradas.

```
require_once 'vendor\autoload.php';

use WindowsAzure\ServiceBus\ServiceBusService;
use WindowsAzure\ServiceBus\ServiceBusSettings;
use WindowsAzure\Common\ServiceException;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
	
try	{		
	// Delete topic.
	$serviceBusRestProxy->deleteTopic("mytopic");
}
catch(ServiceException $e){
	// Handle exception based on error codes and messages.
	// Error codes and messages are here: 
	// http://msdn.microsoft.com/library/azure/dd179357
	$code = $e->getCode();
	$error_message = $e->getMessage();
	echo $code.": ".$error_message."<br />";
}
```

Si usa el método **deleteSubscription**, puede eliminar una suscripción de manera independiente:

```
$serviceBusRestProxy->deleteSubscription("mytopic", "mysubscription");
```

## Pasos siguientes

Ahora que ya conoce los aspectos básicos de las colas del Bus de servicio, vea [Colas, temas y suscripciones][] para obtener más información.

[Colas, temas y suscripciones]: service-bus-queues-topics-subscriptions.md
[sqlfilter]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
[require-once]: http://php.net/require_once
[Colas de Bus de servicio y colas de Azure]: service-bus-azure-and-service-bus-queues-compared-contrasted.md#capacity-and-quotas

<!---HONumber=AcomDC_0211_2016-->