<properties
	pageTitle="Información general sobre la mensajería retransmitida del Bus de servicio | Microsoft Azure"
	description="Información general sobre Retransmisión de bus de servicio."
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
	ms.date="01/26/2016"
	ms.author="sethm"/>


# Mensajería retransmitida del Bus de servicio

El componente central del Bus de servicio es un servicio de retransmisión centralizado (pero con una carga muy equilibrada) que permite compilar aplicaciones híbridas que se ejecutan tanto en el centro de datos de Azure como en entornos empresariales locales. El servicio de retransmisión admite una variedad de diferentes protocolos de transporte y estándares de servicios web. Incluye SOAP, WS-* e incluso REST. La Retransmisión de bus de servicio facilita las aplicaciones híbridas, ya que permite exponer de forma segura los servicios Windows Communication Foundation (WCF) que se encuentran en una red corporativa en la nube pública sin tener que abrir una conexión de firewall y sin que sea necesario realizar cambios molestos en una infraestructura de red corporativa.

![Conceptos del relé](./media/service-bus-relay-overview/sb-relay-01.png)

El servicio de retransmisión admite mensajería unidireccional tradicional, mensajería de solicitud/respuesta y mensajería de punto a punto. También admite la distribución de eventos en el ámbito de internet para habilitar escenarios de publicación/suscripción y la comunicación de socket bidireccional para aumentar la eficacia punto a punto.

En el patrón de mensajería retransmitida, un servicio local se conecta al servicio de relé mediante un puerto de salida y crea un socket bidireccional para la comunicación enlazada a una dirección de encuentro concreta. Después el cliente puede comunicarse con el servicio local enviando mensajes al servicio de retransmisión destinados a la dirección de rendezvous. El servicio de retransmisión “retransmite” los mensajes al servicio local a través del socket bidireccional existente. El cliente no necesita una conexión directa al servicio local, no es necesario saber dónde reside el servicio, y el servicio local no necesita ningún puerto de entrada abierto en el firewall.

Inicie la conexión entre el servicio local y el servicio de retransmisión mediante un conjunto de enlaces de “retransmisión” WCF. En segundo plano, los enlaces de retransmisión se asignan a nuevos elementos de enlace de transporte diseñados para crear componentes de canal WCF que se integran con el Bus de servicio en la nube.

## Pasos siguientes

Para obtener información más detallada sobre la Retransmisión de Bus de servicio, vea los siguientes temas.

- [Información general sobre la arquitectura de Azure Service Bus](service-bus-fundamentals-hybrid-solutions.md)
- [Cómo usar el servicio de retransmisión del Bus de servicio](service-bus-dotnet-how-to-use-relay.md)

 

<!---HONumber=AcomDC_0218_2016-->