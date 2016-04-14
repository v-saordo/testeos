<properties
	pageTitle="¿Qué son los Centros de eventos de Azure? | Microsoft Azure"
	description="Información general de los Centros de eventos de Azure"
	services="event-hubs"
	documentationCenter=".net"
	authors="nberdy"
	manager="timlt"
	editor=""/>

<tags
	ms.service="event-hubs"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="01/26/2016"
	ms.author="sethm"/>

# ¿Qué es Centros de eventos de Azure?

Centros de eventos de Azure es un servicio de introducción de datos altamente escalable que permite la introducción de millones de eventos por segundo para que pueda procesar y analizar grandes cantidades de datos generados por los dispositivos y aplicaciones conectados. Centros de eventos actúa como la "puerta principal" de una canalización de eventos y, una vez que los datos se recopilan en un centro de eventos, se pueden transformar y almacenar con cualquier proveedor de análisis en tiempo real o adaptadores de procesamiento por lotes/almacenamiento. Centros de eventos desacopla la producción de un flujo de eventos desde el consumo de los eventos, para que los consumidores de eventos pueden tener acceso a los eventos según su propia programación.

## Capacidades de los Centros de eventos

Centros de eventos es un servicio de procesamiento de eventos que ofrece introducción de telemetría y eventos en la nube a escala masiva, con una latencia baja y una alta confiabilidad. Este servicio es especialmente útil para:

* Instrumentación de aplicaciones
* La experiencia del usuario o el procesamiento de flujos de trabajo
* Escenarios de Internet de las cosas (IoT)

Algunas otras capacidades de Centros de eventos son el seguimiento del comportamiento en aplicaciones móviles, la información sobre el tráfico de granjas de servidores web, la captura de eventos en el juego de juegos de consola o los datos de telemetría recopilados de máquinas industriales o vehículos conectados.

A diferencia de [Temas y colas de Bus de servicio](../service-bus/service-bus-messaging-overview.md), los Centros de eventos se centran en ofrecer control de flujo de mensajes a escala. Las capacidades de los Centros de eventos se diferencian de las de los temas en que están fuertemente orientadas a escenarios de alto procesamiento y procesamiento de eventos. Como resultado, los Centros de eventos no implementan algunas de las capacidades de mensajería que están disponibles para los [temas](../service-bus/service-bus-fundamentals-hybrid-solutions.md#topics). Si necesita esas capacidades, los temas siguen siendo la opción óptima.

## Pasos siguientes

Para obtener información detallada acerca de los Centros de eventos, consulte los temas siguientes.

- [Información general de los Centros de eventos](event-hubs-overview.md)
- [Guía de programación de Centros de eventos](event-hubs-programming-guide.md)
- [Preguntas más frecuentes sobre la disponibilidad y el soporte técnico de los Centros de eventos](event-hubs-availability-and-support-faq.md)
- Empezar a trabajar con un [tutorial de Centros de eventos][]
- Una [aplicación de ejemplo completa que usa Centros de eventos][]

[tutorial de Centros de eventos]: event-hubs-csharp-ephcs-getstarted.md
[aplicación de ejemplo completa que usa Centros de eventos]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097

<!---HONumber=AcomDC_0218_2016-->