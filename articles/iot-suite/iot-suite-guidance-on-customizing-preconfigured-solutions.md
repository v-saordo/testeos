<properties
	pageTitle="Personalización de las soluciones preconfiguradas | Microsoft Azure"
	description="Proporciona directrices sobre la personalización de ñas soluciones preconfiguradas del Conjunto de aplicaciones de IoT de Azure."
	services=""
    suite="iot-suite"
	documentationCenter=".net"
	authors="stevehob"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-suite"
     ms.devlang="dotnet"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/29/2015"
     ms.author="stevehob"/>

# Personalización de soluciones preconfiguradas

Las soluciones preconfiguradas proporcionadas con el conjunto de aplicaciones de IoT de Azure muestran los servicios del conjunto de aplicaciones que funcionan juntos para proporcionar una solución completa. Desde este punto de partida, existen diversos lugares donde puede ampliar y personalizar la solución para cada situación específica. Las secciones siguientes describen estos puntos de personalización comunes.

## Búsqueda del código fuente

El código fuente de su solución preconfigurada está disponible en Github en los repositorios siguientes:

- Supervisión remota: [https://www.github.com/Azure/azure-iot-remote-monitoring](https://github.com/Azure/azure-iot-remote-monitoring)

Este origen se proporciona para mostrar un patrón para implementar la funcionalidad básica de la supervisión remota con el conjunto de IoT de Azure.

## Cambio de las reglas previamente configuradas

La solución de supervisión remota incluye dos trabajos de [Análisis de transmisiones de Azure](https://azure.microsoft.com/services/stream-analytics/) para implementar la lógica de alarmas y telemetría que se muestran en el panel.

El primer trabajo selecciona todos los datos de la secuencia entrante de la telemetría y crea dos resultados diferentes. El trabajo se denominará **[nombre de la solución]-Telemetry**.

- El primer resultado toma todos los datos mediante `SELECT *` y los envía al almacenamiento de blobs. El almacenamiento de blobs es donde el panel lee los valores sin formato para crear sus gráficos.
- El segundo resultado realiza los cálculos `AVG()`, `MIN()` y `MAX()` en una ventana deslizante de 5 minutos. Estos datos se muestran en los discos del panel.

Mediante la interfaz de usuario de Análisis de transmisiones, puede editar directamente estos trabajos y modificar la lógica, o agregar lógica específica a su situación.

El segundo trabajo funciona en los valores de dispositivo a umbral creados en la página **Reglas** de la solución. Este trabajo consume como datos de referencia el valor de umbral establecido para cada dispositivo. Compara el valor de umbral para ver si es mayor que (`>`) el valor real. Este trabajo se puede modificar, por ejemplo, para cambiar el operador de comparación.

> [AZURE.NOTE] El panel de supervisión remota depende de datos específicos, así que la modificación de los trabajos puede provocar errores del panel.

## Adición de reglas propias

Además de cambiar los trabajos de Análisis de transmisiones de Azure preconfigurados, puede usar el Portal de Azure para agregar nuevos trabajos o nuevas consultas a los trabajos existentes.

## Personalización de dispositivos

Una de las actividades de extensión más comunes es trabajar con dispositivos específicos de su escenario. Existen varios métodos para trabajar con dispositivos. Entre estos se incluye la modificación de un dispositivo simulado para adaptarlo a su situación, o bien el uso del [SDK de dispositivo de IoT][] para conectar el dispositivo físico a la solución.

Para obtener una guía paso a paso sobre cómo agregar dispositivos a la solución preconfigurada de supervisión remota, consulte [Dispositivos de conexión del conjunto de aplicaciones de IoT](iot-suite-connecting-devices.md).

### Creación de dispositivo simulado propio

El código fuente de la solución de supervisión remota (mencionado anteriormente) incluye un simulador. NET. Este simulador es el que se aprovisiona como parte de la solución y se puede modificar para enviar distintos metadatos, telemetría o responder a distintos comandos.

Además, IoT de Azure proporciona un [SDK de C de ejemplo](https://github.com/Azure/azure-iot-sdks/c/serializer/samples/remote_monitoring) que está diseñado para funcionar con la solución preconfigurada de supervisión remota.

### Creación y uso de dispositivos propios (físicos)

Los [SDK de IoT de Azure](https://github.com/Azure/azure-iot-sdks) proporcionan bibliotecas que permiten conectar distintos tipos de dispositivos (lenguajes y sistemas operativos) a soluciones IoT.

## Pasos siguientes

Para obtener más información sobre los dispositivos de IoT, consulte el [sitio para desarrolladores de IoT de Azure](https://azure.microsoft.com/develop/iot/), donde encontrará documentación y vínculos.

[SDK de dispositivo de IoT]: https://azure.microsoft.com/documentation/articles/iot-hub-sdks-summary/

<!---HONumber=AcomDC_0128_2016-->