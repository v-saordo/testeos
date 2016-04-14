<properties
	pageTitle="Información general de los niveles de precios de mensajería Estándar y Premium del Bus de servicio | Microsoft Azure"
	description="Mensajería Premium y Estándar del Bus de servicio"
	services="service-bus"
	documentationCenter=".net"
	authors="djrosanova"
	manager="timlt"
	editor=""/>

<tags
	ms.service="service-bus"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="10/15/2015"
	ms.author="darosa"/>

# Niveles de mensajería Premium y Estándar del Bus de servicio 

La mensajería asincrónica del Bus de servicio, que incluye entidades de mensajería como colas y temas, combina capacidades de mensajería con una completa semántica de publicación-suscripción en la escala de nube. La mensajería asíncrona del Bus de servicio se utiliza como la red troncal de comunicación para muchas sofisticadas soluciones en la nube.

Con la introducción del nivel *Premium* de la mensajería del Bus de servicio, abordamos algunas de las solicitudes más comunes de los clientes en relación a la escala, el rendimiento y la disponibilidad para aplicaciones fundamentales. Aunque los conjuntos de características son prácticamente idénticos, estos dos niveles de mensajería del Bus de servicio de Azure están diseñados para utilizarse en distintas situaciones.

En la tabla siguiente, se resaltan algunas de las principales diferencias.

| Premium | Standard |
|---------------------------------------|--------------------------------|
| Capacidad de proceso elevada | Capacidad de proceso variable |
| Rendimiento predecible | Latencia variable |
| Precios previsibles | Precios según la variante de pago por uso |
| Posibilidad de escalar y reducir verticalmente la carga de trabajo | N/D |

La **mensajería Premium del Bus de servicio de Azure** proporciona aislamiento de recursos en el nivel de CPU y memoria para que cada carga de trabajo de cliente se ejecute de forma aislada. Este contenedor de recursos se llama *unidad de mensajería*. A cada espacio de nombres premium se le asigna al menos una unidad de mensajería. Puede comprar 1, 2 o 4 unidades de mensajería para cada espacio de nombres Premium del Bus de servicio. Una sola carga de trabajo o entidad puede abarcar varias unidades de mensajería y el número de unidades de mensajería puede cambiarse a voluntad, aunque la facturación se realiza con base en una tarificación diaria o de 24 horas. El resultado es un rendimiento predecible y repetible para su solución basada en el Bus de servicio.

Este rendimiento no es solo más predecible y presenta mayor disponibilidad, sino que también es más rápido. La mensajería Premium del Bus de servicio de Azure se basa en el motor de almacenamiento presentado en [los Centros de eventos de Azure](https://azure.microsoft.com/services/event-hubs/). Con la mensajería Premium, obtener el máximo rendimiento es mucho más rápido que en el nivel Estándar.

## Diferencias técnicas de la mensajería Premium

A continuación se presentan algunas diferencias existentes entre los niveles de mensajería Estándar y Premium.

### Entidades con particiones

Las entidades con particiones se admiten en la mensajería Premium, pero no funcionan de la misma forma que en los niveles Estándar y Básico de la mensajería del Bus de servicio. La mensajería Premium no utiliza SQL como almacén de datos y ya no tiene la posible competencia de recursos asociada a una plataforma compartida. Por consiguiente, no es necesario crear particiones. Además, se cambió la cantidad de particiones desde la cifra de 16 particiones en la mensajería Estándar a 2 particiones en Premium. Tener 2 particiones garantiza la disponibilidad y es un número más apropiado para el entorno de tiempo de ejecución Premium. Para obtener más información, consulte [Entidades de mensajería con particiones](service-bus-partitioning.md).

### Entidades exprés

Dado que la mensajería Premium se ejecuta en un entorno de tiempo de ejecución completamente aislado, ya no se requieren entidades exprés. Por lo tanto, las entidades exprés no se admiten en espacios de nombres Premium. Para obtener más información acerca de la característica exprés, consulte la propiedad [Microsoft.ServiceBus.Messaging.QueueDescription.EnableExpress](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enableexpress.aspx).

## Pasos siguientes

Para obtener más información sobre la mensajería de Bus de servicio, consulte los siguientes temas:

- [Introducción a la mensajería Premium del Bus de servicio de Azure (entrada de blog)](http://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
- [Introducción a la mensajería Premium del Bus de servicio de Azure (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
- [Introducción a la mensajería del Bus de servicio](service-bus-messaging-overview.md)
- [Información general sobre la arquitectura de Azure Service Bus](service-bus-fundamentals-hybrid-solutions.md)
- [Utilización de las colas del Bus de servicio](service-bus-dotnet-how-to-use-queues.md)

<!---HONumber=AcomDC_0218_2016-->