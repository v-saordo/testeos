<properties
 pageTitle="Escalado del Centro de IoT de Azure | Microsoft Azure"
 description="Describe cómo escalar el Centro de IoT de Azure."
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
 ms.date="01/20/2016"
 ms.author="elioda"/>

# Escalado del Centro de IoT

El Centro de IoT de Azure puede admitir hasta un millón de dispositivos conectados al mismo tiempo; para ello, se aumenta el número de unidades del nivel S1 o S2 del Centro de IoT a 2000. Para más información, vea [Precios del Centro de IoT][lnk-pricing].

Cada unidad del Centro de IoT permite un número determinado de dispositivos en el Registro y todos ellos se pueden conectar simultáneamente. Cada unidad también permite un número de mensajes diarios.

Para escalar correctamente su solución, debe tener en cuenta el uso particular que haga del Centro de IoT. En concreto, tenga en cuenta la capacidad de procesamiento máxima requerida para las siguientes categorías de operaciones:

* Mensajes de dispositivo a nube
* Mensajes de nube a dispositivo
* Operaciones de registro de identidad

Además de esta información sobre la capacidad de procesamiento, vea [Cuotas y limitaciones del Centro de IoT][] y diseñe su solución en consecuencia.

## Capacidad de procesamiento para los mensajes de dispositivo a nube y de nube a dispositivo

La mejor forma de dimensionar una solución de Centro de IoT es evaluar el tráfico en cada dispositivo.

Los mensajes de dispositivo a nube siguen estas directrices de capacidad de procesamiento sostenida.

| Nivel: | Capacidad de procesamiento sostenida | Velocidad de envío sostenida |
| ---- | -------------------- | ------------------- |
| S1 | Hasta 1111 KB/minuto por unidad<br/>(1,5 GB/día/unidad) | Promedio de 278 mensajes/minuto por unidad<br/>(400.000 mensajes/día por unidad) |
| S2 | Hasta 16 MB/minuto por unidad<br/>(22,8 GB/día/unidad) | Promedio de 4167 mensajes/minuto por unidad<br/>(6 millones de mensajes/día por unidad) |

El rendimiento de los mensajes de nube a dispositivo escala por dispositivo, y cada dispositivo recibe hasta 5 mensajes por minuto.

## Capacidad de procesamiento para las operaciones de registro de identidad

Las operaciones de registro de identidad del Centro de IoT no deberían ser operaciones en tiempo de ejecución porque tienen que ver principalmente con el aprovisionamiento de dispositivos.

Vea las cifras de rendimiento de ráfaga específicas en [Cuotas y limitaciones del Centro de IoT][].

## Clave de particionamiento

Aunque un único Centro de IoT puede escalarse a millones de dispositivos, a veces la solución requiere características de rendimiento específicas que un único Centro de IoT no puede garantizar. En ese caso, se recomienda realizar particiones de los dispositivos en varios centros de IoT. Varios centros de IoT suavizan las ráfagas de tráfico y obtienen el rendimiento necesario o las velocidades de funcionamiento necesarias.

## Pasos siguientes

Siga estos vínculos para obtener más información sobre el Centro de IoT de Azure:

- [Introducción al Centro de IoT (Tutorial)][lnk-get-started]
- [¿Qué es el Centro de IoT de Azure?][]

[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[Cuotas y limitaciones del Centro de IoT]: iot-hub-devguide.md#throttling

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[¿Qué es el Centro de IoT de Azure?]: iot-hub-what-is-iot-hub.md

<!---HONumber=AcomDC_0128_2016-->