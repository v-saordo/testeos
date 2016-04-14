<properties 
   pageTitle="Colas, temas y suscripciones del Bus de servicio | Microsoft Azure"
   description="Información general de las entidades de mensajería del Bus de servicio."
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
   ms.date="12/09/2015"
   ms.author="sethm" />

# Colas, temas y suscripciones del Bus de servicio

El Bus de servicio de Microsoft Azure admite un conjunto de tecnologías basadas en la nube, middleware orientado a mensajes, incluidos una cola de mensajes de confianza y una mensajería de publicación/suscripción duradera. Estas capacidades de mensajería asíncrona pueden considerarse como características de mensajería desacopladas que admiten la publicación-suscripción, el desacoplamiento temporal y los escenarios de equilibrio de carga mediante el tejido de la mensajería del Bus de servicio. La comunicación desacoplada ofrece muchas ventajas; por ejemplo, los clientes y servidores pueden conectarse según sea necesario y realizar sus operaciones de forma asincrónica.

Las entidades de mensajería que forman el núcleo de las capacidades de mensajería asíncrona del Bus de servicio son las colas, los temas/suscripciones, las reglas/acciones y los Centros de eventos.

## Colas

Las colas ofrecen una entrega de mensajes FIFO (PEPS, primero en entrar, primero en salir) a uno o más destinatarios de la competencia. Es decir, normalmente los receptores reciben y procesan los mensajes en el orden en el que se agregaron a la cola y solo un destinatario del mensaje recibe y procesa cada uno de los mensajes. La principal ventaja del uso de colas es conseguir un "desacoplamiento temporal" de los componentes de la aplicación. En otras palabras, los productores (remitentes) y los consumidores (receptores) no tienen que enviar y recibir mensajes al mismo tiempo, ya que los mensajes se almacenan de forma duradera en la cola. El productor no tiene que esperar una respuesta del destinatario para continuar el proceso y el envío de más mensajes.

Una ventaja relacionada es la "nivelación de la carga", lo que permite a los productores y consumidores enviar y recibir mensajes con distintas velocidades. En muchas aplicaciones, la carga del sistema varía con el tiempo, mientras que el tiempo de procesamiento requerido por cada unidad de trabajo suele ser constante. La intermediación de productores y consumidores de mensajes con una cola implica que la aplicación consumidora solo necesita ser aprovisionada para administrar una carga promedio en lugar de una carga pico. La profundidad de la cola aumenta y se contrae a medida que varíe la carga entrante, lo que permite ahorrar dinero directamente en función de la cantidad de infraestructura requerida para dar servicio a la carga de la aplicación. A medida que aumenta la carga, se pueden agregar más procesos de trabajo para que puedan leerse desde la cola. Cada mensaje se procesa únicamente por uno de los procesos de trabajo. Es más, este equilibrio de carga basado en la extracción permite el uso óptimo de los equipos de trabajo aunque estos equipos difieran en términos de capacidad de procesamiento ya que extraerán mensajes a su frecuencia máxima propia. Este patrón con frecuencia se denomina “patrón de consumo de competidor”.

El uso de colas para intermediar entre los consumidores y productores de mensajes proporciona un acoplamiento no estricto inherente entre los componentes. Dado que los productores y consumidores no están relacionados entre sí, un consumidor puede actualizarse sin tener ningún efecto en el productor.

La creación de una cola es un proceso de varios pasos. Las operaciones de administración de las entidades de mensajería del Bus de servicio (colas y temas) se realizan mediante la clase [Microsoft.ServiceBus.NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx), que se construye al suministrar la dirección base del espacio de nombres del Bus de servicio y las credenciales de usuario. La clase [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) proporciona métodos para crear, enumerar y eliminar entidades de mensajes. Después de crear un objeto [Microsoft.ServiceBus.TokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx) a partir del nombre y la clave SAS y un objeto de administración del espacio de nombres del servicios, puede usar el método [Microsoft.ServiceBus.NamespaceManager.CreateQueue](https://msdn.microsoft.com/library/azure/hh293157.aspx) para crear la cola. Por ejemplo:

```
// Create management credentials
TokenProvider credentials = TokenProvider. CreateSharedAccessSignatureTokenProvider(sasKeyName,sasKeyValue);
// Create namespace client
NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", ServiceNamespace, string.Empty), credentials);
```

A continuación, puede crear un objeto de cola y una factoría de mensajes con el URI del Bus de servicio como argumento. Por ejemplo:

```
QueueDescription myQueue;
myQueue = namespaceClient.CreateQueue("TestQueue");
MessagingFactory factory = MessagingFactory.Create(ServiceBusEnvironment.CreateServiceUri("sb", ServiceNamespace, string.Empty), credentials); 
QueueClient myQueueClient = factory.CreateQueueClient("TestQueue");
```

A continuación, puede enviar mensajes a la cola. Por ejemplo, si tiene una lista de mensajes indirectos denominada `MessageList`; el código es similar al siguiente:

```
for (int count = 0; count < 6; count++)
{
    var issue = MessageList[count];
    issue.Label = issue.Properties["IssueTitle"].ToString();
    myQueueClient.Send(issue);
}
```

A continuación, puede recibir mensajes de la cola, como se indica a continuación:

```
while ((message = myQueueClient.Receive(new TimeSpan(hours: 0, minutes: 0, seconds: 5))) != null)
    {
        Console.WriteLine(string.Format("Message received: {0}, {1}, {2}", message.SequenceNumber, message.Label, message.MessageId));
        message.Complete();

        Console.WriteLine("Processing message (sleeping...)");
        Thread.Sleep(1000);
    }
```

Al usar el modo [ReceiveAndDelete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx), la operación de recepción consta de una sola fase; es decir, cuando el Bus de servicio recibe una solicitud, marca el mensaje como consumido y lo devuelve a la aplicación. El modo [ReceiveAndDelete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx) es el modelo más sencillo y funciona mejor para los escenarios en los que la aplicación puede tolerar no procesar un mensaje en caso de error. Para entenderlo mejor, pongamos una situación en la que un consumidor emite la solicitud de recepción que se bloquea antes de procesarla. Como el Bus de servicio marca el mensaje como consumido, cuando la aplicación se reinicie y empiece a consumir mensajes de nuevo, habrá perdido el mensaje que se consumió antes del bloqueo.

En el modo [PeekLock](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx), la operación de recepción se convierte en una operación de dos fases que hace posible admitir aplicaciones que no toleran la pérdida de mensajes. Cuando el Bus de servicio recibe una solicitud, busca el siguiente mensaje que se va a consumir, lo bloquea para impedir que otros consumidores lo reciban y, a continuación, lo devuelve a la aplicación. Una vez que la aplicación termina de procesar el mensaje (o lo almacena de forma fiable para su futuro procesamiento), completa la segunda fase del proceso de recepción creando la llamada [Complete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) en el mensaje recibido. Cuando el Bus de servicio ve la llamada [Complete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx), marca el mensaje como consumido.

Si por cualquier motivo la aplicación no puede procesar el mensaje, realice la llamada al método [Abandon](https://msdn.microsoft.com/library/azure/hh181837.aspx) del mensaje recibido (en lugar de [Complete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx)). Esto permite que el Bus de servicio desbloquee el mensaje y esté disponible para que el mismo consumidor u otro vuelvan a recibirlo. En segundo lugar, hay otro tiempo de espera asociado al bloqueo y, si la aplicación no puede procesar el mensaje antes de que finalice el tiempo de espera del bloqueo (por ejemplo, si la aplicación se bloquea), el Bus de servicio desbloquea el mensaje y hace que esté disponible para que pueda volver a recibirse.

Tenga en cuenta que en caso de que la aplicación se bloquee después de procesar el mensaje y antes de emitir la solicitud [Complete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx), el mensaje se volverá a entregar a la aplicación cuando esta se reinicie. Esto se suele denominar procesamiento * Al menos una vez *; es decir, cada mensaje se procesa al menos una vez. Sin embargo, en determinadas situaciones, es posible que se vuelva a entregar el mismo mensaje. Si el escenario no puede tolerar el procesamiento duplicado, se requiere una lógica adicional en la aplicación para detectar duplicados que se puedan conseguir de acuerdo con la propiedad **MessageId** del mensaje, que permanece constante en los intentos de entrega. Esto se conoce como procesamiento *Exactamente una vez*.

Para obtener más información y un ejemplo de cómo crear y enviar mensajes a y desde las colas, consulte el [Tutorial de .NET de mensajería asíncrona del Bus de servicio](https://msdn.microsoft.com/library/azure/hh367512.aspx).

## Temas y suscripciones

A diferencia de las colas, en las que un solo destinatario procesa cada mensaje, los temas y las suscripciones proporcionan una forma de comunicación de uno a varios mediante un patrón de *publicación/suscripción*. Es útil para escalar a un gran número de destinatarios, cada mensaje publicado se pone a disposición para cada suscripción registrada con el tema. Los mensajes se envían a un tema y se entregan a uno o más suscripciones asociadas, según las reglas de filtro que se pueden establecer por suscripción. Las suscripciones pueden usar filtros adicionales para restringir los mensajes que desean recibir. Los mensajes se envían a un tema de la misma manera que se envían a una cola, pero no se reciben mensajes del tema directamente. En su lugar, se reciben de suscripciones. Una suscripción al tema funciona de forma similar a una cola virtual que recibe copias de los mensajes que se enviaron al tema. Los mensajes se reciben de una suscripción exactamente de la misma forma en que se reciben de una cola.

Con fines de comparación, la funcionalidad de envío de mensajes de una cola se asigna directamente a un tema y su funcionalidad de recepción de mensajes a una suscripción. Entre otras cosas, esto significa que las suscripciones admiten los mismos patrones descritos anteriormente en esta sección con respecto a las colas: consumo de competidor, desacoplamiento temporal, nivelación de la carga y equilibrio de la carga.

La creación de un tema es similar a la creación de una cola, como se muestra en el ejemplo de la sección anterior. Cree el URI del servicio y, a continuación, use la clase [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) para crear el cliente de espacio de nombres. A continuación, puede crear un tema usando el método [CreateTopic](https://msdn.microsoft.com/library/azure/hh293080.aspx). Por ejemplo:

```
TopicDescription dataCollectionTopic = namespaceClient.CreateTopic("DataCollectionTopic");
```

A continuación, agregue las suscripciones que desee:

```
SubscriptionDescription myAgentSubscription = namespaceClient.CreateSubscription(myTopic.Path, "Inventory");
SubscriptionDescription myAuditSubscription = namespaceClient.CreateSubscription(myTopic.Path, "Dashboard");
```

A continuación, puede crear un cliente de tema. Por ejemplo:

```
MessagingFactory factory = MessagingFactory.Create(serviceUri, tokenProvider);
TopicClient myTopicClient = factory.CreateTopicClient(myTopic.Path)
```

El remitente del mensaje puede enviar y recibir mensajes desde y hacia el tema, tal como se muestra en la sección anterior. Por ejemplo:

```
foreach (BrokeredMessage message in messageList)
{
    myTopicClient.Send(message);
    Console.WriteLine(
    string.Format("Message sent: Id = {0}, Body = {1}", message.MessageId, message.GetBody<string>()));
}
```

De forma similar a las colas, los mensajes se reciben de una suscripción mediante un objeto [SubscriptionClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.aspx) en lugar de un [QueueClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx). Cree el cliente de suscripción y pase el nombre del tema, el nombre de la suscripción y (opcionalmente) el modo de recepción como parámetros. Por ejemplo, con la suscripción **Inventory**:

```
// Create the subscription client
MessagingFactory factory = MessagingFactory.Create(serviceUri, tokenProvider); 

SubscriptionClient agentSubscriptionClient = factory.CreateSubscriptionClient("IssueTrackingTopic", "Inventory", ReceiveMode.PeekLock);
SubscriptionClient auditSubscriptionClient = factory.CreateSubscriptionClient("IssueTrackingTopic", "Dashboard", ReceiveMode.ReceiveAndDelete); 

while ((message = agentSubscriptionClient.Receive(TimeSpan.FromSeconds(5))) != null)
{
    Console.WriteLine("\nReceiving message from Inventory...");
    Console.WriteLine(string.Format("Message received: Id = {0}, Body = {1}", message.MessageId, message.GetBody<string>()));
    message.Complete();
}          

// Create a receiver using ReceiveAndDelete mode
while ((message = auditSubscriptionClient.Receive(TimeSpan.FromSeconds(5))) != null)
{
    Console.WriteLine("\nReceiving message from Dashboard...");
    Console.WriteLine(string.Format("Message received: Id = {0}, Body = {1}", message.MessageId, message.GetBody<string>()));
}
```

### Reglas y acciones

En muchos escenarios, los mensajes que tienen características específicas deben procesarse de maneras diferentes. Para permitirlo, puede configurar suscripciones para buscar los mensajes que tengan las propiedades deseadas y, a continuación, realizar determinadas modificaciones en esas propiedades. Mientras que las suscripciones del Bus de servicios ven todos los mensajes enviados al tema, solo puede copiar un subconjunto de dichos mensajes a la cola de suscripción virtual. Esto se consigue mediante filtros de suscripción. Dichas modificaciones se denominan *acciones de filtrado*. Cuando se crea una suscripción, puede suministrar una expresión de filtro que opera en las propiedades del mensaje, las propiedades del sistema (por ejemplo, **Label**) y las propiedades de la aplicación personalizada (por ejemplo, **StoreName**.) La expresión de filtro SQL es opcional en este caso; sin una expresión de filtro SQL, cualquier acción de filtro definida en una suscripción se realizará en todos los mensajes de la suscripción.

Con el ejemplo anterior, para filtrar los mensajes que solo provengan de **Store1**, debe crear la suscripción Dashboard como sigue:

```
namespaceManager.CreateSubscription("IssueTrackingTopic", "Dashboard", new SqlFilter("StoreName = 'Store1'"));
```

Con este filtro de suscripción, los mensajes que tienen la propiedad `StoreName` establecida en `Store1` se copian en la cola virtual para la suscripción `Dashboard`.

Para más información sobre los valores de filtro posibles, vea la documentación de las clases [SqlFilter](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.aspx) y [SqlRuleAction](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlruleaction.aspx). Vea además el ejemplo [Mensajería asincrónica: filtros avanzados](http://code.msdn.microsoft.com/Brokered-Messaging-6b0d2749).

## Centros de eventos

[Centros de eventos](https://azure.microsoft.com/services/event-hubs/) es un servicio de procesamiento de eventos que se usa para ofrecer la entrada de telemetría y eventos en Azure a escala masiva, con una latencia baja y una alta confiabilidad. Este servicio, cuando se usa con otros servicios del flujo de trabajo, es especialmente útil en escenarios de Internet de las cosas, procesamiento del flujo de trabajo o de la experiencia del usuario y en instrumentación de aplicaciones.

Los Centros de eventos son una construcción de streaming de mensajes, y aunque parezca similar a las colas y los temas, tienen características muy distintas. Por ejemplo, los Centros de eventos no proporcionan TTL de mensajes, mensajes fallidos, transacciones o confirmaciones, ya que son características de mensajería asíncrona tradicionales, no características de streaming. Los Centros de eventos proporcionan otras características relacionadas con el flujo, como las particiones, la conservación del orden y la reproducción de la secuencia.

## Pasos siguientes

Consulte los siguientes temas avanzados para obtener más información y ejemplos del uso de las entidades de mensajería asíncrona del Bus de servicio.

- [Introducción a la mensajería del Bus de servicio](service-bus-messaging-overview.md)
- [Tutorial de .NET de mensajería asíncrona del Bus de servicio](service-bus-brokered-tutorial-dotnet.md)
- [Tutorial de REST de mensajería asíncrona del Bus de servicio](service-bus-brokered-tutorial-rest.md)
- [Guía de desarrolladores de Centros de eventos](../event-hubs/event-hubs-programming-guide.md)
- [Mensajería asíncrona: filtros avanzados](http://code.msdn.microsoft.com/Brokered-Messaging-6b0d2749)

<!---HONumber=AcomDC_0128_2016-->