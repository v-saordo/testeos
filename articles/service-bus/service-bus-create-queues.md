<properties 
   pageTitle="Creación de aplicaciones que usan colas del Bus de servicio | Microsoft Azure"
   description="Escritura de una aplicación sencilla basada en cola que usa del Bus de servicio."
   services="service-bus"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="tysonn" />
<tags 
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/28/2015"
   ms.author="sethm" />

# Creación de aplicaciones que usan colas del Bus de servicio

Este tema describe las colas del Bus de servicio y muestra cómo escribir una aplicación sencilla basada en cola que usa el Bus de servicio.

Considere un escenario del sector de la venta al por menor en el que los datos de ventas de los terminales de los puntos de venta (PDV) individuales deben enrutarse a un sistema de gestión de inventarios que usa esos datos para determinar cuándo hay que reponer las existencias. Esta solución usa la mensajería de Bus de servicio para la comunicación entre los terminales y el sistema de gestión de inventarios, tal como se muestra en la ilustración siguiente:

![Service-Bus-Queues-Img1](./media/service-bus-create-queues/IC657161.gif)

Cada terminal del PDV informa de sus datos de venta mediante el envío de mensajes a **DataCollectionQueue**, donde permanecen hasta que el sistema de gestión de inventarios los recupera. Este patrón se denomina a menudo *mensajería asincrónica*, ya que el terminal del PDV no tiene que esperar una respuesta del sistema de gestión de inventarios para continuar el procesamiento.

## Motivos para usar la cola

Antes de examinar el código necesario para configurar esta aplicación, considere las ventajas de usar una cola en este escenario, en lugar de que los terminales de los PDV se comuniquen directamente (de forma sincrónica) con el sistema de gestión de inventarios.

### Desacoplamiento temporal

Con el patrón de mensajería asincrónica, no es preciso que los productores y consumidores estén en línea al mismo tiempo. La infraestructura de mensajería almacena de forma confiable los mensajes hasta que la parte consumidora esté preparada para recibirlos. Esto permite desconectar los componentes de la aplicación distribuida, ya sea voluntariamente (por ejemplo, para realizar tareas de mantenimiento) o debido al bloqueo de algún componente, sin que afecte a todo el sistema. Además, es posible que la aplicación consumidora solo tenga que estar en línea durante determinados momentos del día. Por ejemplo, en este escenario minorista, el sistema de gestión de inventarios solo debe estar en línea después del final del día laborable.

### Redistribución de la carga

En muchas aplicaciones, la carga del sistema varía con el paso del tiempo, mientras que el tiempo de procesamiento requerido para cada unidad de trabajo suele ser constante. La intermediación entre consumidores y productores de mensajes con una cola significa que la aplicación consumidora (el trabajador) solo tiene que aprovisionarse para servir una carga media, en lugar de una carga máxima. La profundidad de la cola aumentará y se contraerá a medida que varíe la carga entrante, lo que permite ahorrar dinero directamente en función de la cantidad de infraestructura requerida para dar servicio a la carga de la aplicación.

![Service-Bus-Queues-Img2](./media/service-bus-create-queues/IC657162.gif)

### Equilibrio de carga

A medida que aumenta la carga, se pueden agregar más procesos de trabajo para que puedan leerse desde la cola de trabajo. Cada mensaje se procesa únicamente por uno de los procesos de trabajo. Además, este equilibrio de carga basado en la extracción permite un uso óptimo de los equipos de trabajo, aunque estos tengan distinta capacidad de procesamiento, ya que extraerán mensajes a su propia velocidad máxima. Este patrón con frecuencia se denomina patrón de consumo de competidor.

![Service-Bus-Queues-Img3](./media/service-bus-create-queues/IC657163.gif)

### Acoplamiento débil

El uso de colas de mensajes para intermediar entre los consumidores y productores de mensajes proporciona un acoplamiento intrínseco débil entre los componentes. Dado que los productores y consumidores no están relacionados entre sí, un consumidor puede actualizarse sin tener ningún efecto en el productor. Además, la topología de los mensajes puede evolucionar sin que ello afecte a los extremos existentes. Esto se tratará con mayor detalle cuando se expliquen la publicación y la suscripción.

## Visualización del código

En la sección siguiente se muestra cómo usar el Bus de servicio para compilar esta aplicación.

### Registro en una cuenta del Bus de servicio y suscripción

Para empezar a trabajar con el Bus de servicio, se necesita una cuenta de Azure. Si no tiene una, puede registrarse para obtener una versión de evaluación gratuita [aquí](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A85619ABF).

### Creación de un espacio de nombres de servicio

Una vez que tenga una suscripción, puede crear un nuevo espacio de nombres. Tendrá que asignar un nombre único al nuevo espacio de nombres en todas las cuentas de Bus de servicio. Cada espacio de nombres actúa como contenedor de un conjunto de entidades de Bus de servicio. Para obtener más información, consulte [Procedimientos: Creación o modificación de un espacio de nombres del servicio Bus de servicio](https://msdn.microsoft.com/library/azure/hh690931.aspx).

### Instalación del paquete NuGet.

Para usar el espacio de nombres del servicio Bus de servicio, una aplicación debe hacer referencia el ensamblado del Bus de servicio, en concreto Microsoft.ServiceBus.dll. Este ensamblado forma parte del SDK de Microsoft Azure y la descarga está disponible en la [página de descarga del SDK de Azure](https://azure.microsoft.com/downloads/). Sin embargo, el paquete NuGet de Bus de servicio es la forma más sencilla de obtener la API de Bus de servicio y configurar su aplicación con todas las dependencias del Bus de servicio. Para obtener más información acerca del uso del paquete NuGet y del bus de servicio, consulte [Using the NuGet Service Bus Package](https://msdn.microsoft.com/library/dn741354.aspx).

### Creación de la cola

Las operaciones de administración de las entidades de mensajería de Bus de servicio (colas y temas de publicación/suscripción) se realizan a través de la clase [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx). Bus de servicio usa un modelo de seguridad basado en la [firma de acceso compartido (SAS)](service-bus-sas-overview.md). La clase [TokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx) representa un proveedor de tokens de seguridad con métodos de generador integrados que devuelven algunos proveedores de tokens conocidos. Vamos a usar un método [CreateSharedAccessSignatureTokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.createsharedaccesssignaturetokenprovider.aspx) para retener las credenciales de SAS. A continuación, se construye la instancia de [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) con la dirección base del espacio de nombres de Bus de servicio y el proveedor de tokens.

La clase [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) proporciona métodos para crear, enumerar y eliminar entidades de mensajes. El siguiente código muestra cómo se crea la instancia de [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) y se usa para crear la cola **DataCollectionQueue**.

```
Uri uri = ServiceBusEnvironment.CreateServiceUri("sb", 
                "test-blog", string.Empty);
string name = "RootManageSharedAccessKey";
string key = "abcdefghijklmopqrstuvwxyz";
 
TokenProvider tokenProvider = 
    TokenProvider.CreateSharedAccessSignatureTokenProvider(name, key);
NamespaceManager namespaceManager = 
    new NamespaceManager(uri, tokenProvider);
namespaceManager.CreateQueue("DataCollectionQueue");
```

Tenga en cuenta que hay sobrecargas del método [CreateQueue](https://msdn.microsoft.com/library/azure/hh322663.aspx) que permiten ajustar las propiedades de la cola. Por ejemplo, puede configurar el valor predeterminado del período de vida (TTL) para los mensajes enviados a la cola.

### Envío de mensajes a la cola

En el caso de operaciones en tiempo de ejecución en entidades del Bus de servicio (por ejemplo, enviar y recibir mensajes), una aplicación debe crear primero un objeto [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx). De forma parecida a la clase [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx), la instancia de [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) se crea a partir de la dirección base del espacio de nombres del servicio y del proveedor de tokens.

```
 BrokeredMessage bm = new BrokeredMessage(salesData);
 bm.Label = "SalesReport";
 bm.Properties["StoreName"] = "Redmond";
 bm.Properties["MachineID"] = "POS_1";
```

Los mensajes enviados a las colas del bus de servicio y recibidos en ellas son instancias de la clase [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx). Esta clase consta de un conjunto de propiedades estándar (como [Label](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx) y [TimeToLive](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx)), un diccionario que se usa para conservar las propiedades de la aplicación y un cuerpo de datos de aplicación arbitrarios. Una aplicación puede establecer el cuerpo pasando cualquier objeto serializable (el siguiente ejemplo pasa un objeto **SalesData** que los datos de ventas del terminal del PDV), que usará [DataContractSerializer](https://msdn.microsoft.com/library/azure/system.runtime.serialization.datacontractserializer.aspx) para serializar el objeto. También se puede proporcionar un objeto [Stream](https://msdn.microsoft.com/library/azure/system.io.stream.aspx).

La manera más fácil de enviar mensajes a una cola determinada, en nuestro caso **DataCollectionQueue**, consiste en usar [CreateMessageSender](https://msdn.microsoft.com/library/azure/hh322659.aspx) para crear un objeto [MessageSender](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx) directamente desde la instancia de [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx).

```
MessageSender sender = factory.CreateMessageSender("DataCollectionQueue");
sender.Send(bm);
```

### Recepción de mensajes desde la cola

La forma más sencilla de recibir mensajes desde la cola es usar un objeto [MessageReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx), que se puede crear directamente desde [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) con [CreateMessageReceiver](https://msdn.microsoft.com/library/azure/hh322642.aspx). Los receptores de mensajes pueden funcionar en dos modos distintos: **ReceiveAndDelete** y **PeekLock**. [ReceiveMode](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx) se establece cuando se crea el receptor del mensaje como un parámetro de la llamada a [CreateMessageReceiver](https://msdn.microsoft.com/library/azure/hh322642.aspx).


Si se usa el modo **ReceiveAndDelete**, la operación de recepción consta de una sola fase; es decir, cuando el Bus de servicio recibe la solicitud, marca el mensaje como consumido y lo devuelve a la aplicación. El modo **ReceiveAndDelete** es el modelo más sencillo y funciona mejor en aquellos escenarios en que la aplicación puede tolerar que no se procese un mensaje en caso de error. Para entenderlo mejor, pongamos una situación en la que un consumidor emite la solicitud de recepción que se bloquea antes de procesarla. Dado que el Bus de servicio marcó el mensaje como consumido, cuando la aplicación se reinicie y empiece a consumir mensajes de nuevo, habrá perdido el mensaje que se consumió antes del bloqueo.

En el modo **PeekLock**, la recepción se convierte en una operación de dos fases que hace posible admitir aplicaciones que no pueden tolerar mensajes perdidos. Cuando el Bus de servicio recibe la solicitud, busca el siguiente mensaje que se va a consumir, lo bloquea para evitar que otros consumidores lo reciban y, a continuación, lo devuelve a la aplicación. Una vez que la aplicación termina de procesar el mensaje (o lo almacena de forma fiable para su futuro procesamiento), completa la segunda fase del proceso de recepción creando la llamada [Complete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) en el mensaje recibido. Cuando el Bus de servicio ve la llamada [Complete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx), marca el mensaje como consumido.

Hay otros dos resultados posibles. En primer lugar, si por cualquier motivo la aplicación no puede procesar el mensaje, puede llamar a [Abandon](https://msdn.microsoft.com/library/azure/hh181837.aspx) en el mensaje recibido (en lugar de [Complete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx)). Esto hará que el Bus de servicio desbloquee el mensaje y esté disponible para recibirse de nuevo, ya sea por el mismo consumidor o por otro consumidor que lo esté completando. En segundo lugar, hay un tiempo de espera asociado con el bloqueo y, si la aplicación no puede procesar el mensaje antes de que expire el tiempo de espera de bloqueo (por ejemplo, si la aplicación se bloquea), el Bus de servicio desbloqueará el mensaje y hará que esté disponible para recibirse de nuevo.

Tenga en cuenta si la aplicación se bloquea tras procesar el mensaje, pero antes la emisión de la solicitud de [Complete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx), el mensaje se volverá a entregar a la aplicación cuando esta se reinicie. Esto se suele denominar procesamiento *Al menos una vez*. Esto significa que cada mensaje se procesará al menos una vez, aunque en determinadas situaciones podría volver a entregarse el mismo mensaje. Si el escenario no tolera el procesamiento duplicado, hará falta lógica adicional en la aplicación para detectar duplicados, lo que puede con la propiedad [MessageId](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx) propiedad del mensaje. El valor de esta propiedad permanece constante en todos los intentos de entrega. Esto se conoce como procesamiento *Una sola vez*.

El código que se muestra aquí recibe y procesa un mensaje con el **PeekLock** modo, que es el valor predeterminado si no se proporciona explícitamente ningún valor [ReceiveMode](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx).

```
MessageReceiver receiver = factory.CreateMessageReceiver("DataCollectionQueue");
BrokeredMessage receivedMessage = receiver.Receive();
try
{
    ProcessMessage(receivedMessage);
    receivedMessage.Complete();
}
catch (Exception e)
{
    receivedMessage.Abandon();
}
```

### Uso del cliente de la cola

En los ejemplos anteriores de esta sección se crearon objetos [MessageSender](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx) y [MessageReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx) directamente desde [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) para enviar mensajes a la cola y recibirlos desde ella, respectivamente. Un enfoque alternativo es usar la clase [QueueClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx), que admite tanto operaciones de envío como de recepción, además de características más avanzadas como sesiones.

```
QueueClient queueClient = factory.CreateQueueClient("DataCollectionQueue");
queueClient.Send(bm);
            
BrokeredMessage message = queueClient.Receive();

try
{
    ProcessMessage(message);
    message.Complete();
}
catch (Exception e)
{
    message.Abandon();
} 
```

## Pasos siguientes

Ahora que ha aprendido los conceptos básicos de las colas, consulte [Creación de aplicaciones que usan temas y suscripciones del Bus de servicio](service-bus-create-topics-subscriptions.md) para continuar este tema sobre las capacidades de publicación/suscripción de la mensajería asincrónica de Bus de servicio.

<!---HONumber=AcomDC_0128_2016-->