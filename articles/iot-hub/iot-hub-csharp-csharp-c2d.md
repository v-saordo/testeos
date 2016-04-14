<properties
	pageTitle="Envío de mensajes de nube a dispositivo con el Centro de IoT | Microsoft Azure"
	description="Siga este tutorial para aprender a enviar mensajes de nube a dispositivo mediante el Centro de IoT de Azure con C#."
	services="iot-hub"
	documentationCenter=".net"
	authors="fsautomata"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="dotnet"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/29/2015"
     ms.author="elioda"/>

# Tutorial: cómo envío mensajes de nube a dispositivo con el Centro de IoT

## Introducción

El Centro de IoT de Azure es un servicio totalmente administrado que permite la comunicación bidireccional confiable y segura entre millones de dispositivos IoT y el back-end de una aplicación. El tutorial [Introducción al Centro de IoT] muestra cómo crear un Centro de IoT, aprovisionar la identidad de un dispositivo en él y codificar un dispositivo simulado que envía mensajes de dispositivo a nube.

Este tutorial se basa en [Introducción al Centro de IoT] y muestra cómo enviar mensajes de nube a dispositivo en un solo dispositivo, cómo solicitar confirmación de entrega (*comentarios*) del Centro de IoT y recibirla del back-end de nube de la aplicación.

Encontrará más información sobre los mensajes de nube a dispositivo en la [Guía para desarrolladores del Centro de IoT][IoT Hub Developer Guide - C2D].

Al final de este tutorial ejecutará dos aplicaciones de consola de Windows:

* **SimulatedDevice**, versión modificada de la aplicación creada en [Introducción al Centro de IoT], que se conecta al Centro de IoT y recibe mensajes de nube a dispositivo.
* **SendCloudToDevice**, que envía un mensaje de nube a dispositivo al dispositivo simulado a través del Centro de IoT y luego recibe su confirmación de entrega.

> [AZURE.NOTE] El centro de IoT ofrece compatibilidad con el SDK para muchas plataformas de dispositivos y lenguajes (incluido C, Java y Javascript), mediante los SDK de dispositivos IoT de Azure. Consulte el [Centro para desarrolladores de IoT de Azure] para obtener instrucciones paso a paso sobre cómo conectar el dispositivo al código de este tutorial y, en general, al Centro de IoT de Azure. Próximamente estarán disponibles SDK de servicios IoT de Azure para Java y Node.

Para completar este tutorial, necesitará lo siguiente:

+ Microsoft Visual Studio 2015

+ Una cuenta de Azure activa. <br/>En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, vea [Evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fes-ES%2Fdevelop%2Fiot%2Ftutorials%2Fc2d%2F target="\_blank").

[AZURE.INCLUDE [iot-hub-c2d-device-csharp](../../includes/iot-hub-c2d-device-csharp.md)]


[AZURE.INCLUDE [iot-hub-c2d-cloud-csharp](../../includes/iot-hub-c2d-cloud-csharp.md)]

## Pasos siguientes

En este tutorial, aprendió a enviar y recibir mensajes de nube a dispositivo. Puede seguir explorando las características del Centro de IoT y distintos escenarios con el tutorial siguiente:

- [Procesamiento de mensajes de dispositivo a nube] muestra cómo procesar de forma fiable la telemetría y los mensajes interactivos procedentes de los dispositivos.
- [Carga de archivos desde dispositivos], describe un patrón que hace uso de los mensajes de nube a dispositivo para facilitar la carga de archivos desde los dispositivos.

Información adicional sobre el Centro de IoT:

* [Información general sobre el centro de IoT]
* [Guía del desarrollador del centro de IoT]
* [Directrices sobre el centro de IoT]
* [Lenguajes y plataformas de dispositivos compatibles][Supported devices]
* [Centro para desarrolladores de Azure]

<!-- Images. -->

<!-- Links -->

[Get started with IoT Hub]: iot-hub-csharp-csharp-getstarted.md

[IoT Hub Developer Guide - C2D]: iot-hub-devguide.md#c2d

[Azure portal]: https://portal.azure.com/

[Send Cloud-to-Device messages with IoT Hub]: iot-hub-csharp-csharp-c2d.md
[Procesamiento de mensajes de dispositivo a nube]: iot-hub-csharp-csharp-process-d2c.md
[Carga de archivos desde dispositivos]: iot-hub-csharp-csharp-file-upload.md

[Información general sobre el centro de IoT]: iot-hub-what-is-iot-hub.md
[Directrices sobre el centro de IoT]: iot-hub-guidance.md
[Guía del desarrollador del centro de IoT]: iot-hub-devguide.md
[IoT Hub Supported Devices]: iot-hub-supported-devices.md
[Introducción al Centro de IoT]: iot-hub-csharp-csharp-getstarted.md
[Supported devices]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/tested_configurations.md
[Centro para desarrolladores de Azure]: http://www.azure.com/develop/iot
[Centro para desarrolladores de IoT de Azure]: http://www.azure.com/develop/iot

<!---HONumber=AcomDC_0128_2016-->