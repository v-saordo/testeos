<properties
	pageTitle="Información general sobre la mensajería del Bus de servicio | Microsoft Azure"
	description="Mensajería de Bus de servicio: entrega flexible de datos en la nube"
	services="service-bus"
	documentationCenter=".net"
	authors="sethmanheim"
	manager="timlt"
	editor=""/>

<tags
	ms.service="service-bus"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="get-started-article"
	ms.date="12/09/2015"
	ms.author="sethm"/>


# Mensajería de Bus de servicio: entrega flexible de datos en la nube

La mensajería de Bus de servicio de Azure es un servicio confiable de entrega de información. El propósito de este servicio es facilitar la comunicación. Cuando dos o más partes quieren intercambiar información, necesitan un mecanismo de comunicación. La mensajería de Service Bus es un mecanismo de comunicación asíncrona o de terceros. Esto es similar a un servicio postal en el mundo físico. Los servicios postales facilitan el envío distintos tipos de cartas y paquetes, con una variedad de garantías de entrega, a cualquier lugar del mundo.

La mensajería del Bus de servicio es similar al servicio postal de entrega de cartas, ya que trata de la entrega flexible de información entre el remitente y el destinatario. El servicio de mensajería garantiza que la información se entrega, incluso si las dos partes no están nunca conectados al mismo tiempo o si no están disponibles en el mismo momento exacto. De esta manera, la mensajería es similar al envío de una carta, mientras que la comunicación asíncrona es similar a la realización de una llamada telefónica (o como era la realización de llamadas, antes de la creación de servicios de identificación de llamada o llamada en espera, que son mucho más similares a la mensajería asíncrona).

El remitente del mensaje también puede requerir una variedad de características de entrega, incluidas transacciones, detección de duplicados, expiración basado en el tiempo y el procesamiento por lotes. Estos patrones también tienen analogías postales: repetición de la entrega, requerimiento de firma, cambio de dirección o devolución de llamada.

Bus de servicio admite dos patrones de mensajería distintos: mensajería *retransmitida* y mensajería *asincrónica*.

## Mensajería retransmitida

El componente de [retransmisión](service-bus-relay-overview.md) del Bus de servicio es un servicio centralizado (pero con gran equilibrio de carga) que admite una gran variedad de diferentes protocolos de transporte y estándares de servicios web. Incluye SOAP, WS-* e incluso REST. El [servicio de retransmisión](service-bus-dotnet-how-to-use-relay.md) ofrece una variedad de opciones de conectividad de retransmisión diferentes y puede ayudar a facilitar la negociación de conexiones directas de punto a punto cuando sea posible. El Bus de servicio está optimizado para los desarrolladores de .NET que usan Windows Communication Foundation (WCF), tanto en términos de rendimiento como de facilidad de uso, y ofrece acceso completo a su servicio de retransmisión a través de interfaces SOAP y REST. Esto permite que cualquier entorno de programación SOAP o REST se integre con el Bus de servicio.

El servicio de retransmisión admite mensajería unidireccional tradicional, mensajería de solicitud/respuesta y mensajería de punto a punto. También admite la distribución de eventos en el ámbito de Internet para habilitar escenarios de publicación-suscripción y la comunicación de socket bidireccional para aumentar la eficacia punto a punto. En el patrón de mensajería retransmitida, un servicio local se conecta al servicio de relé mediante un puerto de salida y crea un socket bidireccional para la comunicación enlazada a una dirección de encuentro concreta. Después el cliente puede comunicarse con el servicio local enviando mensajes al servicio de retransmisión destinados a la dirección de rendezvous. El servicio de retransmisión “retransmite” los mensajes al servicio local a través del socket bidireccional existente. El cliente no necesita una conexión directa al servicio local ni es necesario saber dónde reside el servicio, y el servicio local no necesita ningún puerto de entrada abierto en el firewall.

Debe iniciar la conexión entre el servicio local y el servicio de retransmisión mediante un conjunto de enlaces de “retransmisión” WCF. En segundo plano, los enlaces de retransmisión se asignan a elementos de enlace de transporte diseñados para crear componentes de canal WCF que se integran con el Bus de servicio en la nube.

La mensajería retransmitida ofrece muchas ventajas, pero requiere que tanto el servidor como el cliente estén en línea al mismo tiempo para enviar y recibir mensajes. Esto no es óptimo para la comunicación de estilo HTTP, en la que puede que las solicitudes no sean normalmente de larga duración, ni para los clientes que solo se conectan ocasionalmente, como exploradores, aplicaciones móviles, etc. La mensajería asíncrona admite la comunicación desacoplada y tiene sus propias ventajas; los clientes y servidores se pueden conectar cuando sea necesario y realizar sus operaciones de forma asincrónica.

## Mensajería asíncrona

A diferencia del esquema de mensajería retransmitida, la [mensajería asincrónica](service-bus-fundamentals-hybrid-solutions.md) puede considerarse asincrónica o "temporalmente desacoplada". Los productores (remitentes) y consumidores (receptores) no tienen que estar en línea al mismo tiempo. La infraestructura de mensajería almacena de forma fiable los mensajes en un "agente" (como una cola) hasta que la parte consumidora esté preparada para recibirlos. De esta forma los componentes de la aplicación distribuida se pueden desconectar, ya sea voluntariamente, por ejemplo, para mantenimiento, o debido a un bloqueo del componente, sin que afecte a todo el sistema. Además, es posible que la aplicación receptora solo tenga que estar en línea durante determinadas horas del día, por ejemplo, como un sistema de administración de inventario que solo es necesario ejecutarse al final del día laborable.

Los componentes principales de la infraestructura de mensajería asíncrona del Bus de servicio son colas, temas y suscripciones. La principal diferencia es que los temas admiten capacidades de publicación o suscripción que se pueden usar el sofisticado enrutamiento basado en contenido y entrega lógica, incluido el envío a varios destinatarios. Estos componentes permiten nuevos escenarios de mensajería asincrónicos, como desacoplamiento temporal, publicación/suscripción y equilibrio de carga. Para más información sobre estas entidades de mensajería, vea [Colas, temas y suscripciones del Bus de servicio](service-bus-queues-topics-subscriptions.md).

Al igual que con la infraestructura de mensajería retransmitida, se ofrece la capacidad de mensajería asíncrona para los programadores de WCF y de .NET Framework y también a través de REST.

## Pasos siguientes

Para obtener más información sobre la mensajería de Bus de servicio, consulte los siguientes temas:

- [Colas, temas y suscripciones del bus de servicio](service-bus-queues-topics-subscriptions.md)
- [Elementos fundamentales del Bus de servicio](service-bus-fundamentals-hybrid-solutions.md)
- [Arquitectura del Bus de servicio](service-bus-architecture.md)
- [Utilización de las colas del Bus de servicio](service-bus-dotnet-how-to-use-queues.md)
- [Cómo usar temas de Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
 

<!---HONumber=AcomDC_0218_2016-->