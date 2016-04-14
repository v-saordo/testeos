<properties
 pageTitle="IoT Hub operations monitoring (Supervisión de operaciones de Centro de IoT)"
 description="Información general de la supervisión de operaciones del Centro de IoT de Azure, que permite a los usuarios supervisar el estado de las operaciones de su Centro de IoT en tiempo real."
 services="iot-hub"
 documentationCenter=""
 authors="nberdy"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="02/03/2016"
 ms.author="nberdy"/>

# Introducción a la supervisión de operaciones

La supervisión de operaciones del Centro de IoT permite a los usuarios supervisar el estado de las operaciones de su Centro de IoT en tiempo real. Centro de IoT realiza el seguimiento de eventos a través de varias categorías de operaciones, y los usuarios pueden optar por que los eventos de una o varias categorías se envíen a un punto de conexión de su Centro de IoT para su procesamiento. Los usuarios pueden supervisar los datos en busca de errores o configurar un procesamiento más complejo basado en modelos de datos.

El Centro de IoT supervisa cuatro categorías de eventos:

- Operaciones de identidad de dispositivos
- Telemetría de dispositivo
- Comandos de nube a dispositivo
- Conexiones

## Habilitación de la supervisión de operaciones

1. Cree un Centro de IoT. Puede encontrar instrucciones sobre cómo crear un Centro de IoT en la guía [Introducción][lnk-get-started].

2. Abra la hoja de su Centro de IoT. Desde ahí, haga clic en **Toda la configuración**, y, a continuación, haga clic en **Supervisión de operaciones**.

    ![][1]

3. Seleccione las categorías que desea supervisar y luego haga clic en **Guardar**. Los eventos están disponibles para su lectura desde el punto de conexión compatible con el Centro de eventos que aparece en **Configuración de supervisión**.

    ![][2]

## Categorías de eventos y su uso

Cada categoría de supervisión de operaciones realiza el seguimiento de un tipo diferente de interacción con el Centro de IoT, y cada categoría de supervisión tiene un esquema que define cómo se estructuran los eventos de esa categoría.

### Operaciones de identidad de dispositivos

La categoría de operaciones de identidad de dispositivo supervisa los errores que se producen cuando el usuario intenta crear, actualizar o eliminar una entrada en el registro de la identidad del Centro de IoT. El seguimiento de esta categoría resulta útil para los escenarios de aprovisionamiento.

    {
        "time": "UTC timestamp",
         "operationName": "create",
         "category": "DeviceIdentityOperations",
         "level": "Error",
         "statusCode": 4XX,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "durationMs": 1234,
         "userAgent": “userAgent”,
         "sharedAccessPolicy": "accessPolicy"
    }

### Telemetría de dispositivo

La categoría de telemetría del dispositivo realiza el seguimiento de los errores que se producen en el Centro de IoT y se relacionan con la canalización de telemetría. Esto incluye los errores que se producen al enviar eventos de telemetría (por ejemplo, la limitación) y recibir eventos de telemetría (por ejemplo, un lector no autorizado). Tenga en cuenta que esta categoría no puede detectar los errores causados por el código que se ejecuta en el propio dispositivo.

    {
         "messageSizeInBytes": 1234,
         "batching": 0,
         "protocol": "Amqp",
         "authType": "{"scope":"device","type":"sas","issuer":"iothub"}",
         "time": "UTC timestamp",
         "operationName": "ingress",
         "category": "DeviceTelemetry",
         "level": "Error",
         "statusCode": 4XX,
         "statusType": 4XX001,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "EventProcessedUtcTime": "UTC timestamp",
         "PartitionId": 1,
         "EventEnqueuedUtcTime": "UTC timestamp"
    }

### Comandos de nube a dispositivo

La categoría de comandos de nube a dispositivo realiza el seguimiento de los errores que se producen en el Centro de IoT y se relacionan con la canalización del comando de dispositivo. Esto incluye errores que se producen al enviar comandos (por ejemplo, un remitente no autorizado), recibir comandos (por ejemplo, el número de entregas superado) y recibir comentarios de comando (por ejemplo, comentarios caducados). Esta categoría no detecta errores de un dispositivo que trata incorrectamente un comando si el comando se ha entregado correctamente.

    {
         "messageSizeInBytes": 1234,
         "authType": "{"scope":"hub","type":"sas","issuer":"iothub"}",
         "deliveryAcknowledgement": 0,
         "protocol": "Amqp",
         "time": " UTC timestamp",
         "operationName": "ingress",
         "category": "C2DCommands",
         "level": "Error",
         "statusCode": 4XX,
         "statusType": 4XX001,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "EventProcessedUtcTime": "UTC timestamp",
         "PartitionId": 1,
         "EventEnqueuedUtcTime": “UTC timestamp"
    }

### Conexiones

La categoría de conexiones realiza el seguimiento de eventos provocados por dispositivos que se conectan o desconectan de un Centro de IoT. El seguimiento de esta categoría resulta útil para identificar intentos de conexión no autorizados y para realizar el seguimiento de las situaciones en que una conexión se pierde para los dispositivos en las áreas de conectividad deficiente.

    {
         "durationMs": 1234,
         "authType": "{"scope":"hub","type":"sas","issuer":"iothub"}",
         "protocol": "Amqp",
         "time": " UTC timestamp",
         "operationName": "deviceConnect",
         "category": "Connections",
         "level": "Error",
         "statusCode": 4XX,
         "statusType": 4XX001,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID"
    }

## Pasos siguientes

Ahora que vio información general sobre la supervisión de operaciones, siga estos vínculos para obtener más información:

- [Métricas de diagnóstico del Centro de IoT][lnk-diagnostic-metrics]
- [Escalado del Centro de IoT][lnk-scaling]
- [Alta disponibilidad y recuperación ante desastres del Centro de IoT][lnk-dr]

<!-- Links and images -->
[1]: media/iot-hub-operations-monitoring/enable-OM-1.png
[2]: media/iot-hub-operations-monitoring/enable-OM-2.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-diagnostic-metrics]: iot-hub-metrics.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

<!---HONumber=AcomDC_0204_2016-->