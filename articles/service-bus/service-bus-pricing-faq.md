<properties 
   pageTitle="Preguntas frecuentes de precios del Bus de servicio | Microsoft Azure"
   description="Respuestas a algunas preguntas frecuentes acerca de la estructura de precios del Bus de servicio."
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

# P+F de precios de Bus de servicio

En esta sección responde a algunas preguntas frecuentes acerca de la estructura de precios del Bus de servicio. También puede visitar [Preguntas más frecuentes de soporte técnico de Microsoft Azure](http://go.microsoft.com/fwlink/?LinkID=185083) para obtener información general sobre los precios de Microsoft Azure. Para obtener más información sobre los precios del Bus de servicio, consulte [Precios del Bus de servicio](https://azure.microsoft.com/pricing/details/service-bus/).

>[AZURE.NOTE] La estructura de precios para los Centros de eventos en el tema [Preguntas más frecuentes sobre la disponibilidad y el soporte técnico de los Centros de eventos](../event-hubs/event-hubs-availability-and-support-faq.md), con más información en el tema [Precios de los Centros de eventos](https://azure.microsoft.com/pricing/details/event-hubs/).

- [¿Cómo se cobra el Bus de servicio?](#how-do-you-charge-for-service-bus)
- [¿Qué uso del Bus de servicio está sujeto a la transferencia de datos? ¿Cuál no lo está?](#what-usage-of-service-bus-is-subject-to-data-transfer-what-is-not)
- [¿Qué es exactamente una "retransmisión" del Bus de servicio?](#what-exactly-is-a-service-bus-quotrelayquot)
- [¿Cómo se calcula el contador de horas de retransmisión?](#how-is-the-relay-hours-meter-calculated)
- [¿Qué ocurre si tengo más de un agente de escucha conectado a una retransmisión determinada?](#what-if-i-have-more-than-one-listener-connected-to-a-given-relay)
- [¿Cómo se calcula el contador de mensajes para retransmisiones?](#how-is-the-messages-meter-calculated-for-relays)
- [¿El Bus de servicio cobra por almacenamiento?](#does-service-bus-charge-for-storage)
- [¿El Bus de servicio tiene alguna cuota de uso?](#does-service-bus-have-any-usage-quotas)

## ¿Cómo se cobra el Bus de servicio?

Para obtener información acerca de los precios del Bus de servicio, consulte [Precios y facturación del Bus de servicio](https://msdn.microsoft.com/library/dn831889.aspx) y [Precios del Bus de servicio](https://azure.microsoft.com/pricing/details/service-bus/). Además de los precios indicados, se le cobrará por las transferencias de datos asociadas para salidas del centro de datos en el que se aprovisiona la aplicación. Puede encontrar más detalles en la sección [¿Qué uso del Bus de servicio está sujeto a la transferencia de datos? ¿Cuál no lo está?](#what-usage-of-service-bus-is-subject-to-data-transfer-what-is-not), a continuación.

## ¿Qué uso del Bus de servicio está sujeto a la transferencia de datos? ¿Cuál no lo está?

Cualquier transferencia de datos dentro de una determinada región de Azure se proporciona sin cargo alguno. Cualquier transferencia fuera de una región de datos está sujeta a cargos de salida a la tarifa de 0,15 USD por GB desde las regiones de Norteamérica y Europa y 0,20 USD por GB desde la región de Asia Pacífico. Cualquier transferencia de datos de entrada se proporciona sin cargo alguno.

## ¿Qué es exactamente una "retransmisión" del Bus de servicio?

Una retransmisión es una entidad del Bus de servicio que retransmite mensajes entre clientes y servicios web. La retransmisión proporciona el servicio con una dirección del Bus de servicio persistente y reconocible, una conectividad de confianza con capacidades transversales de firewall/NAT y características adicionales como el equilibrio de carga automático. Se crea una instancia de una retransmisión y se abre implícitamente en una determinada dirección del Bus de servicio (URL del espacio de nombres) siempre que un servicio WCF habilitado para la retransmisión, o "agente de escucha de retransmisión", se conecte primero a esa dirección. Las aplicaciones crean agentes de escucha de retransmisión mediante el uso de la API .NET del Bus de servicio administrada, que proporciona versiones especiales habilitadas para la retransmisión de los enlaces WCF estándar.

## ¿Cómo se calcula el contador de horas de retransmisión?

Las horas de retransmisión se facturan por la cantidad acumulada de tiempo durante el que cada Bus de servicio de retransmisión está "abierto" durante un período de facturación determinado. Se crea una instancia de una retransmisión y se abre implícitamente en una determinada dirección del Bus de servicio (URL del espacio de nombres del servicio) siempre que un servicio WCF habilitado para la retransmisión, o "agente de escucha de retransmisión", se conecte primero a esa dirección. La retransmisión solo se cierra cuando el último agente de escucha se desconecta de su dirección. Por tanto, para la facturación, una retransmisión se considera "abierta" desde el momento en que se conecta el primer agente de escucha de retransmisión, hasta el momento en que el último agente de escucha de retransmisión se desconecta de la dirección del Bus de servicio de dicha retransmisión. En otras palabras, una retransmisión se considera "abierta" cuando uno o más agentes de escucha de retransmisión están conectados a su dirección del Bus de servicio.

## ¿Qué ocurre si tengo más de un agente de escucha conectado a una retransmisión determinada?

En algunos casos, una sola retransmisión del Bus de servicio puede tener varios agentes de escucha conectados. Esto puede ocurrir con servicios de equilibrio de carga que usan los enlaces de WCF **netTCPRelay** o **HttpRelay**, o bien con agentes de escucha de eventos de difusión que usan el enlace WCF **netEventRelay**. Una retransmisión del Bus de servicio se considera "abierta" cuando al menos un agente de escucha de retransmisión está conectado a ella. La incorporación de agentes de escucha adicionales a una retransmisión abierta no cambia el estado de esa retransmisión de cara a la facturación. El número de remitentes de retransmisión (clientes que invocan o envían mensajes a retransmisiones) conectados a una retransmisión tampoco no tiene ningún efecto en el cálculo de horas de retransmisión.

## ¿Cómo se calcula el contador de mensajes para retransmisiones?

En general, los mensajes facturables se calculan para retransmisiones con el mismo método que se ha descrito anteriormente para entidades negociadas (colas, temas y suscripciones). Sin embargo, hay algunas diferencias importantes:

1. El envío de un mensaje a una retransmisión del Bus de servicio se trata como un envío "completo a través" al agente de escucha de retransmisión que recibe el mensaje, en lugar de un envío a la retransmisión del Bus de servicio seguido de una entrega al agente de escucha de retransmisión. Por lo tanto, una invocación de servicio de tipo solicitud-respuesta (de hasta 64 KB) a un agente de escucha de retransmisión producirá dos mensajes facturables: un mensaje facturable por la solicitud y un mensaje facturable por la respuesta (siempre que la respuesta sea también < = 64 KB). Esto difiere del uso de una cola para mediar entre un cliente y un servicio. En el último caso, el mismo patrón de solicitud-respuesta requeriría el envío de una solicitud a la cola, seguido de una eliminación de la cola/entrega de la cola al servicio, seguido de un envío de respuesta a otra cola y una eliminación de cola/entrega desde esa cola al cliente. Con las mismas suposiciones de tamaño (< = 64 KB) todo el tiempo, el patrón de cola promediado produciría cuatro mensajes facturables, dos veces el número facturado para implementar el mismo patrón mediante la retransmisión. Por supuesto, existen ventajas en el uso de colas para lograr este patrón, como la duración y la nivelación de la carga. Estas ventajas pueden justificar el gasto adicional.

2. Las retransmisiones que se abren mediante el enlace WCF **netTCPRelay** tratan los mensajes no como mensajes individuales, sino como un flujo de datos que fluye a través del sistema. En otras palabras, solo el remitente y el agente de escucha tienen visibilidad en el marco de los mensajes individuales enviados o recibidos mediante este enlace. Por lo tanto, para las retransmisiones con el enlace **netTCPRelay**, todos los datos se tratan como una secuencia con el fin de calcular los mensajes facturables. En este caso, el Bus de servicio calculará la cantidad total de datos enviados o recibidos a través de una retransmisión individual cada 5 minutos y divide ese total por 64 KB para determinar el número de mensajes facturables para la retransmisión en cuestión durante ese período de tiempo.

## ¿El Bus de servicio cobra por almacenamiento?

No, el Bus de servicio no cobra por almacenamiento. Sin embargo, hay una cuota que limita la cantidad máxima de datos que pueden persistir por cola/tema. Consulte la siguiente pregunta.

## ¿El Bus de servicio tiene alguna cuota de uso?

De forma predeterminada, para cualquier servicio en la nube, Microsoft establece una cuota de uso mensual agregada que se calcula en todas las suscripciones del cliente. Dado que entendemos que puede necesitar más de estos límites, póngase en contacto con el servicio de atención al cliente en cualquier momento para que podamos conocer sus necesidades y ajustar estos límites adecuadamente. Para el Bus de servicio, las cuotas de uso agregado son las siguientes:

- 5 millardos de mensajes

- 2 millones de horas de retransmisión

Aunque nos reservamos el derecho a deshabilitar una cuenta de cliente que ha superado sus cuotas de uso en un mes determinado, se proporcionará la notificación por correo electrónico y se realizarán varios intentos para ponerse en contacto con un cliente antes de llevar a cabo cualquier acción. Los clientes que superen estas cuotas todavía será responsables de los cargos que superen las cuotas.

Al igual que con otros servicios de Azure, el Bus de servicio aplica un conjunto de cuotas específicas para garantizar que hay un uso justo de los recursos. Las siguientes son las cuotas de uso que aplica el servicio:

- **Tamaño de cola/tema**: especifique el tamaño máximo de la cola o el tema al crear la cola o el tema. Esta cuota puede tener un valor de 1, 2, 3, 4 o 5 GB. Si se alcanza el tamaño máximo de la cola establecido, se rechazarán los mensajes entrantes adicionales y el código de llamada recibirá una excepción.

- **Número de conexiones simultáneas**
	- **Cola/tema/suscripción**: el número de conexiones TCP simultáneas en una cola/tema/suscripción está limitado a 100. Si se alcanza esta cuota, se rechazarán las solicitudes posteriores de conexiones adicionales y el código de llamada recibirá una excepción. Para cada generador de mensajes, el Bus de servicio mantiene una conexión TCP si cualquiera de los clientes creados por esa factoría de mensajes tiene una operación activa pendiente o ha completado una operación hace menos de 60 segundos. Las operaciones REST no se cuentan en las conexiones de TCP simultáneas.


- **Número de agentes de escucha simultáneos en una retransmisión**: el número de agentes de escucha **netTcpRelay** y **netHttpRelay ** en una retransmisión está limitada a 25 (1 para una retransmisión **NetOneway**).

- **Número de agentes de escucha de retransmisión simultáneos por espacio de nombres**: el Bus de servicio impone un límite de 2000 agentes de escucha de retransmisión simultáneos por espacio de nombres del servicio. Si se alcanza esta cuota, se rechazarán las solicitudes posteriores para abrir agentes de escucha de retransmisión y el código de llamada recibirá una excepción.

- **Número de temas/colas por espacio de nombres del servicio**: el número máximo de temas/colas (entidades con copia en almacenamiento duraderas) en un espacio de nombres del servicio se limita a 10 000. Si se alcanza esta cuota, se rechazarán las solicitudes posteriores para la creación de una nueva cola/tema en el espacio de nombres del servicio. En este caso, el [Portal de Azure clásico][] mostrará un mensaje de error o el código del cliente que llama recibirá una excepción, en función de si el intento de creación se realiza a través del portal o en el código del cliente.

- **Cuotas de tamaño de los mensajes**
	- **Cola/tema/suscripción**
		- **Tamaño de los mensajes**: cada mensaje se limita a un tamaño total de 256 KB, incluidos los encabezados del mensaje.
		- **Tamaño de encabezado de mensaje**: cada encabezado de mensaje se limita a 64 KB.

	- **Retransmisiones NetOneway y NetEvent**: cada mensaje se limita a un tamaño total de 64 KB, incluidos los encabezados del mensaje.
	- **Retransmisiones http y NetTcp**: el Bus de servicio no impone un límite superior en el tamaño de estos mensajes.

	Se rechazarán los mensajes que superen estas cuotas de tamaño y el código de llamada recibirá una excepción.

- **Número de suscripciones por tema**: el número máximo de suscripciones por tema se limita a 2000. Si se alcanza esta cuota, se rechazarán las solicitudes posteriores de creación de suscripciones adicionales al tema. En este caso, el [Portal de Azure clásico][] mostrará un mensaje de error o el código del cliente que llama recibirá una excepción, en función de si el intento de creación se realiza a través del portal o en el código del cliente.

- **Número de filtros SQL por tema**: el número máximo de filtros SQL por tema está limitado a 2000. Si se alcanza esta cuota, se rechazarán las solicitudes posteriores de creación de filtros adicionales en el tema y el código de llamada recibirá una excepción.

- **Número de filtros de correlación por tema**: el número máximo de filtros de correlación por tema se limita a 100 000. Si se alcanza esta cuota, se rechazarán las solicitudes posteriores de creación de filtros adicionales en el tema y el código de llamada recibirá una excepción.

Para obtener más información sobre las cuotas, consulte [Cuotas del Bus de servicio](service-bus-quotas.md).

## Pasos siguientes

Para obtener más información sobre la mensajería de Bus de servicio, consulte los siguientes temas:

- [Introducción a la mensajería Premium del Bus de servicio de Azure (entrada de blog)](http://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
- [Introducción a la mensajería Premium del Bus de servicio de Azure (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
- [Introducción a la mensajería del Bus de servicio](service-bus-messaging-overview.md)
- [Información general sobre la arquitectura de Bus de servicio de Azure](service-bus-fundamentals-hybrid-solutions.md)
- [Utilización de las colas del Bus de servicio](service-bus-dotnet-how-to-use-queues.md)

[Portal de Azure clásico]: http://manage.windowsazure.com

<!---HONumber=AcomDC_0204_2016-->