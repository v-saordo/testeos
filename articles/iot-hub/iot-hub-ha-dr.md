<properties
 pageTitle="Alta disponibilidad y recuperación ante desastres del Centro de IoT | Microsoft Azure"
 description="Describe características que ayudan a crear soluciones de IoT con alta disponibilidad y con funcionalidad de recuperación ante desastres."
 services="iot-hub"
 documentationCenter=""
 authors="fsautomata"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="02/03/2016"
 ms.author="elioda"/>

# Alta disponibilidad y recuperación ante desastres del Centro de IoT

Como un servicio de Azure, el Centro de IoT proporciona alta disponibilidad (HA) usando redundancias en la región de Azure, sin que la solución tenga que realizar ningún trabajo adicional. Además, Azure ofrece una serie de características que ayudan a crear soluciones con funcionalidad de recuperación ante desastres o disponibilidad entre regiones, si es necesario. Debe diseñar y preparar las soluciones para que aprovechen estas características de recuperación ante desastres si desea proporcionar alta disponibilidad global entre regiones para dispositivos o usuarios. En el artículo [Orientación técnica de la continuidad del negocio de Azure][], se describen las características integradas de Azure para la continuidad empresarial y la recuperación ante desastres. El documento [Consideraciones sobre la alta disponibilidad y la recuperación ante desastres para las aplicaciones de Azure][] proporciona una guía de arquitectura enfocada a estrategias para que las aplicaciones de Azure logren alta disponibilidad y recuperación ante desastres.

## Recuperación ante desastres de Centro de IoT de Azure
Además de la alta disponibilidad dentro de una región, Centro de IoT implementa mecanismos de conmutación por error para recuperación ante desastres que no requieren intervención del usuario. La recuperación ante desastres del Centro de IoT se inicia automáticamente y tiene un objetivo de tiempo de recuperación (RTO) de 2 a 26 horas y los siguientes objetivos de punto de recuperación (RPO).

| Funcionalidad | RPO |
| ------------- | --- |
| Disponibilidad de servicio para las operaciones de registro y comunicación | Posible pérdida de CName |
| Datos de identidad en el registro de identidad del dispositivo | Pérdida de datos de 0-5 minutos |
| Mensajes de dispositivo a nube | Se pierden todos los mensajes no leídos |
| Mensajes de supervisión de operaciones | Se pierden todos los mensajes no leídos |
| Mensajes de nube a dispositivo | Pérdida de datos de 0-5 minutos |
| Cola de comentarios de nube a dispositivo | Se pierden todos los mensajes no leídos |

## Conmutación por error regional con el Centro de IoT

La descripción completa de las topologías de implementación de soluciones de IoT queda fuera del ámbito de este artículo pero, para fines de alta disponibilidad y recuperación ante desastres, tendremos en cuenta el modelo de implementación de *conmutación por error regional*.

En un modelo de conmutación por error regional, el back-end de la solución se ejecuta principalmente en una ubicación de centro de datos, pero se implementan un Centro de IoT y un back-end adicionales en otra ubicación de centro de datos para la conmutación por error, ya que puede suceder que el Centro de IoT del centro de datos principal sufra una interrupción o se pierda por algún motivo la conectividad de red entre el dispositivo y el centro de datos principal. Los dispositivos usan un punto de conexión de servicio secundario siempre que no se puede alcanzar la puerta de enlace principal. Con la capacidad de conmutación por error entre regiones, la disponibilidad de la solución puede mejorar más allá de la alta disponibilidad de una única región.

A grandes rasgos, para implementar un modelo de conmutación por error regional con un Centro de IoT, necesitará la siguiente.

* **Un Centro de IoT secundario y lógica de enrutamiento de dispositivo**: si se produce una interrupción del servicio en la región primaria, los dispositivos deben empezar a conectarse a la región secundaria. Como se conoce el estado de la mayoría de los servicios implicados, es habitual que los administradores de la solución desencadenen el proceso de conmutación por error entre regiones. La mejor forma de comunicar el nuevo extremo a los dispositivos, sin perder el control del proceso, es hacer que comprueben periódicamente un servicio de *conserje* para el extremo activo actual. El servicio de conserje puede ser una aplicación web simple que se replica y se mantiene accesible mediante técnicas de redirección de DNS (por ejemplo, con el [Administrador de tráfico de Azure][]).
* **Replicación del registro de identidades**: para que el Centro de IoT secundario pueda usarse, debe contener todas las identidades de dispositivo que se pueden conectar a la solución. La solución debe conservar copias de seguridad de replicación geográfica de las identidades de dispositivo y cargarlas en el Centro de IoT secundario antes de cambiar el punto de conexión activo de los dispositivos. La funcionalidad de exportación de identidades de dispositivo del Centro de IoT resulta muy útil en este contexto. Para obtener más información, consulte [Guía para desarrolladores del Centro de IoT: Registro de identidades][].
* **Lógica de combinación**: cuando la región primaria vuelve a estar disponible, todos los estados y datos que se crearon en el sitio secundario deben volver a migrarse a la región primaria. Esto tiene que ver principalmente con las identidades de dispositivo y los metadatos de aplicación, que deben combinarse con el Centro de IoT principal y con otros almacenes específicos de la aplicación en la región primaria. Para simplificar este paso, generalmente se recomienda usar operaciones idempotentes. Esto minimiza los efectos secundarios, no solo una posible distribución uniforme de eventos, sino también duplicados o entrega desordenada de eventos. Además, la lógica de aplicación debe diseñarse para que tolere posibles incoherencias o un estado "algo" desactualizado. Esto se debe al tiempo adicional que tarda el sistema en "reparar" según los objetivos de punto de recuperación (RPO). El siguiente artículo proporciona más información sobre este tema: [Failsafe: instrucciones para crear arquitecturas de nube resistentes][].

## Pasos siguientes

Siga estos vínculos para obtener más información sobre el Centro de IoT de Azure:

- [Introducción a los Centros de IoT (Tutorial)][lnk-get-started]
- [¿Qué es el Centro de IoT de Azure?][]

[Orientación técnica de la continuidad del negocio de Azure]: https://msdn.microsoft.com/library/azure/hh873027.aspx
[Consideraciones sobre la alta disponibilidad y la recuperación ante desastres para las aplicaciones de Azure]: https://msdn.microsoft.com/library/azure/dn251004.aspx
[Failsafe: instrucciones para crear arquitecturas de nube resistentes]: https://msdn.microsoft.com/library/azure/jj853352.aspx
[Administrador de tráfico de Azure]: https://azure.microsoft.com/documentation/services/traffic-manager/
[Guía para desarrolladores del Centro de IoT: Registro de identidades]: iot-hub-devguide.md#identityregistry

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[¿Qué es el Centro de IoT de Azure?]: iot-hub-what-is-iot-hub.md

<!---HONumber=AcomDC_0204_2016-->