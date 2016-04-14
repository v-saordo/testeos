<properties 
   pageTitle="Entidades de mensajería con particiones | Microsoft Azure"
   description="Describe cómo realizar la partición de las entidades de mensajería mediante el uso de varios agentes de mensajes."
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

# Entidades de mensajería con particiones

Bus de servicios de Azure emplea varios agentes de mensajes para procesar mensajes y varios almacenes de mensajería para almacenar mensajes. Un único agente de mensajes controla una cola o tema convencional, que se almacena en un almacén de mensajería. Bus de servicio también permite que las colas o temas se dividan entre varios agentes de mensajes y almacenes de mensajería. Esto significa que el rendimiento general de una cola o tema particionado ya no está limitado por el rendimiento de un solo agente o almacén de mensajería. Además, una interrupción temporal de un almacén de mensajería no hace que una cola o tema con particiones deje de estar disponible. Las colas y los temas con particiones pueden contener todas las características avanzadas del Bus de servicio, como la compatibilidad con transacciones y sesiones.

Para más información sobre los aspectos internos de Bus de servicio, consulte el tema [Arquitectura del Bus de servicio][].

## Temas y colas con particiones

Cada cola o tema con particiones consta de varios fragmentos. Cada fragmento se almacena en un almacén de mensajería diferente y se controla por un agente de mensajes diferente. Cuando se envía un mensaje a una cola o un tema con particiones, Bus de servicio asigna el mensaje a uno de los fragmentos. La selección se realiza de forma aleatoria por el Bus de servicio o por una clave de partición que el remitente puede especificar.

Cuando un cliente quiere recibir un mensaje de una cola con particiones, o de una suscripción de un tema con particiones, Bus de servicio consulta en todos los fragmentos si hay mensajes, luego devuelve al receptor el primer mensaje que se devuelve de cualquiera de los almacenes de mensajería. Bus de servicio almacena en caché los demás mensajes y los devuelve cuando recibe solicitudes de recepción adicionales. Un cliente de recepción no es consciente de las particiones; el comportamiento de cara al cliente de una cola o tema con particiones (por ejemplo, lectura, finalización, aplazamiento, mensajes fallidos y captura previa) es idéntico al comportamiento de una entidad regular.

No hay costos adicionales cuando se envía un mensaje a una cola o tema con particiones o cuando se recibe un mensaje de ellos.

## Habilitación de las particiones

Para usar colas o temas con particiones con Bus de servicio de Azure, use Azure SDK versión 2.2 o posterior, o especifique `api-version=2013-10` en sus solicitudes HTTP.

Puede crear colas y temas de Bus de servicio en tamaños de 1, 2, 3, 4 o 5 GB (el valor predeterminado es 1 GB). Con las particiones habilitadas, el Bus de servicio crea 16 particiones por cada GB que especifique. Por lo tanto, si crea una cola con un tamaño de 5 GB, con 16 particiones, el tamaño de cola máximo se convierte en (5 * 16) = 80 GB. Puede ver el tamaño máximo de la cola o tema con particiones examinando su entrada en el [Portal de Azure clásico][].

Hay varias maneras de crear una cola o tema con particiones. Al crear la cola o el tema desde la aplicación, puede habilitar las particiones para la cola o el tema estableciendo respectivamente las propiedades [QueueDescription.EnablePartitioning][] o [TopicDescription.EnablePartitioning][] en **true**. Estas propiedades deben establecerse en el momento de crear la cola o el tema. No es posible cambiar estas propiedades en una cola o tema existente. Por ejemplo:

```
// Create partitioned topic
NamespaceManager ns = NamespaceManager.CreateFromConnectionString(myConnectionString);
TopicDescription td = new TopicDescription(TopicName);
td.EnablePartitioning = true;
ns.CreateTopic(td);
```

También puede crear una cola o tema con particiones en Visual Studio o en el [Portal de Azure clásico][]. Al crear una nueva cola o tema en el portal, marque la opción **Habilitar particiones** en la pestaña **Configurar** de la ventana de la cola o del tema. En Visual Studio, haga clic en la casilla **Habilitar particiones** en el cuadro de diálogo **Nueva cola** o **Nuevo tema**.

## Uso de claves de partición

Cuando un mensaje se coloca en cola en una cola o tema con particiones, el Bus de servicio comprueba la presencia de una clave de partición. Si encuentra una, selecciona el fragmento basado en esa clave. Si no encuentra una clave de partición, selecciona el fragmento basado en un algoritmo interno.

### Uso de una clave de partición

En algunos escenarios, como sesiones o transacciones, es necesario que los mensajes se almacenen en un fragmento específico. Todos estos escenarios requieren el uso de una clave de partición. Todos los mensajes que usan la misma clave de partición se asignan al mismo fragmento. Si el fragmento no está disponible temporalmente, Bus de servicio devuelve un error.

En función del escenario, se usan diferentes propiedades de mensaje como clave de partición:

**SessionId**: si un mensaje tiene establecida la propiedad [BrokeredMessage.SessionId][], Bus de servicio usa esta propiedad como clave de partición. De esta manera, todos los mensajes que pertenecen a la misma sesión se gestionan por el mismo agente de mensajes. Esto permite que Bus de servicio garantice la ordenación de los mensajes así como la coherencia de los estados de la sesión.

**PartitionKey**: si un mensaje tiene establecida la propiedad [BrokeredMessage.PartitionKey][] pero no la propiedad [BrokeredMessage.SessionId][], Bus de servicio usa la propiedad [PartitionKey][] como clave de partición. Si el mensaje tiene establecidas las propiedades [SessionId][] y [PartitionKey][], ambas propiedades deben ser idénticas. Si la propiedad [PartitionKey][] se establece en un valor distinto del de la propiedad [SessionId][], Bus de servicio devuelve una excepción **InvalidOperationException**. La propiedad [PartitionKey][] debe usarse si un remitente envía mensajes transaccionales que no tienen en cuenta la sesión. La clave de partición asegura que todos los mensajes que se envían dentro de una transacción se gestionan por el mismo agente de mensajes.

**MessageId**: si la cola o tema tiene la propiedad [QueueDescription.RequiresDuplicateDetection][] establecida en **true** y las propiedades [BrokeredMessage.SessionId][] o [BrokeredMessage.PartitionKey][] no se establecieron, la propiedad [BrokeredMessage.MessageId][] sirve de clave de partición. (Tenga en cuenta que las bibliotecas de Microsoft .NET y AMQP asignan automáticamente un identificador de mensaje si la aplicación emisora no lo hace). En este caso, todas las copias del mismo mensaje se controlan por el mismo agente de mensajes. Esto permite que Bus de servicio detecte y elimine los mensajes duplicados. Si la propiedad [QueueDescription.RequiresDuplicateDetection][] no se establece en **true**, Bus de servicio no considera la propiedad [MessageId][] como una clave de partición.

### Sin uso de una clave de partición

En ausencia de una clave de partición, Bus de servicio distribuye los mensajes en modo round-robin a todos los fragmentos de una cola o tema con particiones. Si el fragmento elegido no está disponible, Bus de servicio asigna el mensaje a un fragmento diferente. De esta manera, la operación de envío se realiza correctamente a pesar de la no disponibilidad temporal de un almacén de mensajería.

Para dar a Bus de servicio el tiempo suficiente para poner en cola el mensaje en un fragmento diferente, el valor [MessagingFactorySettings.OperationTimeout][] especificado por el cliente que envía el mensaje debe ser superior a 15 segundos. Se recomienda establecer la propiedad [OperationTimeout][] en el valor predeterminado de 60 segundos.

Tenga en cuenta que una clave de partición “ancla” un mensaje a un fragmento específico. Si el almacén de mensajería que contiene este fragmento no está disponible, Bus de servicio devuelve un error. En ausencia de una clave de partición, Bus de servicio puede elegir un fragmento diferente y la operación se realiza correctamente. Por lo tanto, se recomienda no suministrar una clave de partición a menos que sea necesario.

## Temas avanzados: uso de transacciones con entidades con particiones

Los mensajes que se envían como parte de una transacción deben especificar una clave de partición. Puede ser una de las propiedades siguientes: [BrokeredMessage.SessionId][], [BrokeredMessage.PartitionKey][] o [BrokeredMessage.MessageId][]. Todos los mensajes que se envían como parte de la misma transacción deben especificar la misma clave de partición. Si intenta enviar un mensaje sin una clave de partición dentro de una transacción, Bus de servicio devuelve una excepción **InvalidOperationException**. Si intenta enviar varios mensajes dentro de la misma transacción que tienen claves de partición diferentes, Bus de servicio devuelve una excepción **InvalidOperationException**. Por ejemplo:

```
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    BrokeredMessage msg = new BrokeredMessage("This is a message");
    msg.PartitionKey = "myPartitionKey";
    messageSender.Send(msg); 
    ts.Complete();
}
committableTransaction.Commit();
```

Si se establece alguna de las propiedades que sirven de clave de partición, Bus de servicio ancla el mensaje a un fragmento específico. Este comportamiento se produce independientemente de si se usa una transacción o no. Se recomienda que no especifique una clave de partición si no es necesario.

## Uso de sesiones con entidades con particiones

Para enviar un mensaje transaccional a un tema o cola que tiene en cuenta la sesión, el mensaje debe tener establecida la propiedad [BrokeredMessage.SessionId][]. Si también se especifica la propiedad [BrokeredMessage.PartitionKey][], debe ser idéntica a la propiedad [SessionId][]. Si difieren, Bus de servicio devuelve una excepción **InvalidOperationException**.

A diferencia de las colas o temas regulares (sin particiones), no es posible usar una única transacción para enviar varios mensajes a diferentes sesiones. Si se intenta, Bus de servicio devuelve una excepción **InvalidOperationException**. Por ejemplo:

```
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    BrokeredMessage msg = new BrokeredMessage("This is a message");
    msg.SessionId = "mySession";
    messageSender.Send(msg); 
    ts.Complete();
}
committableTransaction.Commit();
```

## Reenvío automático de mensajes en entidades con particiones

Bus de servicio de Azure admite el reenvío automático de mensajes desde entidades con particiones, así como a ellas y entre ellas. Para habilitar el reenvío automático de mensajes, establezca la propiedad [QueueDescription.ForwardTo][] en la cola o suscripción de origen. Si el mensaje especifica una clave de partición ([SessionId][], [PartitionKey][] o [MessageId][]), esa clave de partición se usa para la entidad de destino.

## Limitaciones de las entidades con particiones

En su implementación actual, Bus de servicio impone las siguientes limitaciones en colas y temas con particiones:

-   Las colas y temas con particiones están disponibles a través de SBMP o HTTP/HTTPS, así como de AMQP.

-   Las colas y los temas con particiones no admiten el envío de mensajes que pertenecen a sesiones diferentes en una sola transacción.

-   Actualmente Bus de servicio permite hasta 100 colas o temas particionados por espacio de nombres. Cada cola o tema con particiones se tiene en cuenta para la cuota de 10.000 entidades por espacio de nombres.

-   Las colas y temas con particiones no están admitidos en Bus de servicio para Windows Server versiones 1.0 y 1.1.

## Pasos siguientes

Consulte la explicación en [Compatibilidad de AMQP 1.0 con los temas y las colas con particiones del Bus de servicio][] para más información acerca de las entidades de mensajería con particiones.

  [Arquitectura del Bus de servicio]: service-bus-architecture.md
  [Portal de Azure clásico]: http://manage.windowsazure.com
  [QueueDescription.EnablePartitioning]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enablepartitioning.aspx
  [TopicDescription.EnablePartitioning]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.enablepartitioning.aspx
  [BrokeredMessage.SessionId]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.sessionid.aspx
  [BrokeredMessage.PartitionKey]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.partitionkey.aspx
  [SessionId]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.sessionid.aspx
  [PartitionKey]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.partitionkey.aspx
  [QueueDescription.RequiresDuplicateDetection]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.requiresduplicatedetection.aspx
  [BrokeredMessage.MessageId]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx
  [MessageId]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx
  [QueueDescription.RequiresDuplicateDetection]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.requiresduplicatedetection.aspx
  [MessagingFactorySettings.OperationTimeout]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx
  [OperationTimeout]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx
  [QueueDescription.ForwardTo]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.forwardto.aspx
  [Compatibilidad de AMQP 1.0 con los temas y las colas con particiones del Bus de servicio]: service-bus-partitioned-queues-and-topics-amqp-overview.md

<!---HONumber=AcomDC_0107_2016-->