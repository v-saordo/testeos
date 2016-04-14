<properties 
   pageTitle="Entidades de mensajería del Bus de servicio con reenvío automático | Microsoft Azure"
   description="Describe cómo encadenar una cola o una suscripción a otra cola o a otro tema que forme parte del mismo espacio de nombres."
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

# Encadenamiento de entidades de Bus de servicio con reenvío automático

La característica de *Reenvío automático* permite encadenar una cola o una suscripción a otra cola o a otro tema que forme parte del mismo espacio de nombres del servicio. Cuando está habilitado el reenvío automático, el Bus de servicio quita automáticamente los mensajes que se colocan en la primera cola o suscripción (origen) y los coloca en la segunda cola o en el segundo tema (destino). Tenga en cuenta que todavía se puede enviar un mensaje a la entidad de destino directamente. Asimismo, tenga en cuenta que no se puede encadenar una subcola (como una cola de correo devuelto) a otra cola o tema.

## Uso del reenvío automático

Puede habilitar el reenvío automático estableciendo las propiedades [QueueDescription.ForwardTo][] o [SubscriptionDescription.ForwardTo][] de los objetos [QueueDescription][] o [SubscriptionDescription][] para el origen, como en el siguiente ejemplo.

```
SubscriptionDescription srcSubscription = new SubscriptionDescription (srcTopic, srcSubscriptionName);
srcSubscription.ForwardTo = destTopic;
namespaceManager.CreateSubscription(srcSubscription));
```

La entidad de destino debe existir en el momento en que se creó la entidad de origen. Si la entidad de destino no existe, el Bus de servicio devuelve una excepción cuando se le pide que cree la entidad de origen.

Puede utilizar el reenvío automático para escalar horizontalmente un tema individual. El Bus de servicio limita el número de suscripciones de un tema determinado. Puede alojar suscripciones adicionales creando temas de segundo nivel. Tenga en cuenta que, incluso si no tiene la limitación del Bus de servicio sobre el número de suscripciones, el hecho de agregar un segundo nivel de temas puede mejorar el rendimiento general del tema.

![Escenario de reenvío automático][0]

También puede utilizar el reenvío automático para desacoplar los remitentes de los destinatarios. Por ejemplo, suponga que un sistema ERP consta de tres módulos: procesamiento de pedidos, administración de inventario y administración de relaciones con clientes. Cada uno de estos módulos genera mensajes que se ponen en cola en el tema correspondiente. Alice y Bob son representantes de ventas que están interesados en todos los mensajes relacionados con sus clientes. Para recibir estos mensajes, Alice y Bob crean una cola personal y una suscripción en cada uno de los temas ERP que reenvían automáticamente todos los mensajes a la cola correspondiente.

![Escenario de reenvío automático][1]

Si Alice se va de vacaciones, se llena su cola personal, en lugar del tema ERP. En este escenario, como un representante de ventas no ha recibido ningún mensaje, ninguno de los temas ERP alcanza la cuota.

## Consideraciones sobre el reenvío automático

Si la entidad de destino ha acumulado muchos mensajes y supera la cuota (o la entidad de destino está deshabilitada), la entidad de origen agrega los mensajes a la cola de correo devuelto hasta que haya espacio en el destino (o hasta que se vuelva a habilitar la entidad). Esos mensajes seguirán estando en la cola de correo devuelto, por lo que debe recibirlos y procesarlos explícitamente desde de la cola de correo devuelto.

Al encadenar temas individuales para obtener un tema compuesto con muchas suscripciones, se recomienda tener un número moderado de suscripciones en el tema del primer nivel y muchas suscripciones en los temas del segundo nivel. Por ejemplo, un tema de primer nivel con 20 suscripciones, cada una de ellas encadenada a un tema de segundo nivel con 200 suscripciones, ofrece un mayor rendimiento que un tema de primer nivel con 200 suscripciones, cada una de ellas encadenada a un tema de segundo nivel con 20 suscripciones.

El Bus de servicio factura una operación por cada mensaje reenviado. Por ejemplo, enviar un mensaje a un tema con 20 suscripciones, cada uno de ellos configurado para reenviar automáticamente mensajes a otra cola o tema, se factura como 21 operaciones si todas las suscripciones del primer nivel reciben una copia del mensaje.

Para crear una suscripción que esté encadenada a otra cola o tema, el creador de la suscripción debe tener permisos de **administrador** en la entidad de origen y en la de destino. Para enviar mensajes al tema de origen, solo se requieren permisos de **envío** en el tema de origen.

## Pasos siguientes

Para obtener información detallada sobre el reenvío automático, consulte los temas de referencia siguientes:

- [SubscriptionDescription.ForwardTo][]
- [QueueDescription][]
- [SubscriptionDescription][]

Para más información acerca de las mejoras de rendimiento de Bus de servicio, consulte [Entidades de mensajería con particiones][].

  [QueueDescription.ForwardTo]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.forwardto.aspx
  [SubscriptionDescription.ForwardTo]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptiondescription.forwardto.aspx
  [QueueDescription]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.aspx
  [SubscriptionDescription]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptiondescription.aspx
  [0]: ./media/service-bus-auto-forwarding/IC628631.gif
  [1]: ./media/service-bus-auto-forwarding/IC628632.gif
  [Entidades de mensajería con particiones]: service-bus-partitioning.md

<!---HONumber=AcomDC_0107_2016-->