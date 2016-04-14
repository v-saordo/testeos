<properties
 pageTitle="Tutorial de la solución preconfigurada de supervisión remota | Microsoft Azure"
 description="Una descripción de la supervisión remota de la solución preconfigurada de IoT de Azure y su arquitectura."
 services=""
 suite="iot-suite"
 documentationCenter=""
 authors="stevehob"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-suite"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="03/02/2016"
 ms.author="stevehob"/>

# Tutorial de la solución preconfigurada de supervisión remota

## Introducción

La solución preconfigurada de supervisión remota del Conjunto de aplicaciones de IoT es una solución básica de supervisión de extremo a extremo para un escenario empresarial que opera en varias máquinas en ubicaciones remotas. La solución combina los servicios del Conjunto de aplicaciones de IoT de Azure para proporcionar una implementación genérica de la situación de negocio y es un punto de partida para aquellos clientes que planean implementar este tipo de solución de IoT para satisfacer sus propias necesidades de negocio específicas.

## Arquitectura lógica

El diagrama siguiente describe los componentes lógicos de la solución preconfigurada:

![](media/iot-suite-remote-monitoring-sample-walkthrough/remote-monitoring-architecture.png)


### Dispositivos simulados

En la solución preconfigurada, el dispositivo simulado representa un dispositivo de refrigeración (por ejemplo, el aire acondicionado del edificio o una unidad de tratamiento de aire en instalaciones). Cada dispositivo simulado envía los siguientes mensajes de telemetría al Centro de IoT:


| Message | Descripción |
|----------|-------------|
| Inicio | Cuando se inicia el dispositivo, envía un mensaje **device-info** que contiene información sobre sí mismo, como el identificador de dispositivo, los metadatos de dispositivo, una lista de los comandos que admite el dispositivo y la configuración actual del dispositivo. |


Los dispositivos simulados envían las siguientes propiedades de dispositivo como metadatos:

| Propiedad | Propósito |
|------------------------|--------- |
| Id. de dispositivo | Identificador que se proporciona o se asigna cuando se crea un dispositivo en la solución. |
| Fabricante | Fabricante del dispositivo |
| Número de modelo | Número de modelo del dispositivo |
| Número de serie | Número de serie del dispositivo |
| Firmware | Versión actual del firmware del dispositivo |
| Plataforma | Arquitectura de la plataforma del dispositivo |
| Procesador | Procesador que ejecuta el dispositivo |
| RAM instalada | Cantidad de RAM instalada en el dispositivo |
| Estado del centro habilitado | Propiedad del estado de Centro de IoT del dispositivo |
| Hora de creación | La hora en la que se creó el dispositivo en la solución |
| Hora actualizada | Última vez que se actualizaron las propiedades en el dispositivo |
| Latitud | Ubicación de la latitud del dispositivo |
| Longitud | Ubicación de la longitud del dispositivo |

El simulador propaga estas propiedades en los dispositivos simulados con valores de ejemplo. Cada vez que el simulador inicializa un dispositivo simulado, el dispositivo envía los metadatos previamente definidos al Centro de IoT. Tenga en cuenta que se sobrescribe cualquier actualización de metadatos realizada en el portal de dispositivo.


Los dispositivos simulados pueden controlar los siguientes comandos enviados desde un Centro de IoT:

| Comando | Descripción |
|------------------------|-----------------------------------------------------|
| PingDevice | Envía un _ping_ al dispositivo para comprobar si está activo. |
| StartTelemetry | Inicia el dispositivo que envía la telemetría. |
| StopTelemetry | Detiene el dispositivo que envía la telemetría. |
| ChangeSetPointTemp | Cambia el valor del punto fijo alrededor del cual se generan los datos aleatorios. |
| DiagnosticTelemetry | Desencadena el simulador de dispositivos para enviar un valor de telemetría adicional (externalTemp). |
| ChangeDeviceState | Cambia una propiedad de estado extendido del dispositivo y envía el mensaje de información de dispositivo desde el dispositivo. |


La confirmación de comandos del dispositivo se proporciona a través del Centro de IoT.


### Trabajos de Análisis de transmisiones de Azure


**Trabajo 1: información de dispositivo** filtra los mensajes de información del dispositivo desde la transmisión de mensajes entrantes y los envía a un punto de conexión de Centro de eventos. Un dispositivo envía mensajes de información de dispositivo al inicio y como respuesta a un comando **SendDeviceInfo**. Este trabajo utiliza la siguiente definición de consulta:

```
SELECT * FROM DeviceDataStream Partition By PartitionId WHERE  ObjectType = 'DeviceInfo'
```

**Trabajo 2: reglas** evalúa los valores de telemetría de temperatura y humedad entrantes según los umbrales por dispositivo. Los valores de umbral se establecen en el editor de reglas que se incluyen en la solución. Cada par de valor/dispositivo se almacena en la marca de tiempo de un blob que se lee en Análisis de transmisiones como **datos de referencia**. El trabajo compara cualquier valor no vacío con el umbral establecido para el dispositivo. Si supera la condición '>', el trabajo tendrá como resultado un evento de **alarma** que indica que se superó el umbral y proporciona los valores de dispositivo, valor y marca de tiempo. Este trabajo utiliza la siguiente definición de consulta:

```
WITH AlarmsData AS 
(
SELECT
     Stream.DeviceID,
     'Temperature' as ReadingType,
     Stream.Temperature as Reading,
     Ref.Temperature as Threshold,
     Ref.TemperatureRuleOutput as RuleOutput,
     Stream.EventEnqueuedUtcTime AS [Time]
FROM IoTTelemetryStream Stream
JOIN DeviceRulesBlob Ref ON Stream.DeviceID = Ref.DeviceID
WHERE
     Ref.Temperature IS NOT null AND Stream.Temperature > Ref.Temperature

UNION ALL

SELECT
     Stream.DeviceID,
     'Humidity' as ReadingType,
     Stream.Humidity as Reading,
     Ref.Humidity as Threshold,
     Ref.HumidityRuleOutput as RuleOutput,
     Stream.EventEnqueuedUtcTime AS [Time]
FROM IoTTelemetryStream Stream
JOIN DeviceRulesBlob Ref ON Stream.DeviceID = Ref.DeviceID
WHERE
     Ref.Humidity IS NOT null AND Stream.Humidity > Ref.Humidity
)

SELECT *
INTO DeviceRulesMonitoring
FROM AlarmsData

SELECT *
INTO DeviceRulesHub
FROM AlarmsData
```

**Trabajo 3: Telemetría** opera en el flujo de la telemetría del dispositivo entrante de dos formas distintas. La primera envía todos los mensajes de telemetría desde los dispositivos al almacenamiento de blobs persistente. La segunda calcula los valores de humedad medio, mínimo y máximo sobre una ventana deslizante de cinco minutos. Estos datos también se envían al almacenamiento de blobs. Este trabajo utiliza la siguiente definición de consulta:

```
WITH 
    [StreamData]
AS (
    SELECT
        *
    FROM 
      [IoTHubStream] 
    WHERE
        [ObjectType] IS NULL -- Filter out device info and command responses
) 

SELECT
    *
INTO
    [Telemetry]
FROM
    [StreamData]

SELECT
    DeviceId,
    AVG (Humidity) AS [AverageHumidity], 
    MIN(Humidity) AS [MinimumHumidity], 
    MAX(Humidity) AS [MaxHumidity], 
    5.0 AS TimeframeMinutes 
INTO
    [TelemetrySummary]
FROM
    [StreamData]
WHERE
    [Humidity] IS NOT NULL
GROUP BY
    DeviceId, 
    SlidingWindow (mi, 5)
```

### Procesador de eventos

**Procesador de eventos** controla los mensajes de información del dispositivo y las respuestas de los comandos. Usa:

- Los mensajes de información de dispositivo para actualizar el registro del dispositivo (almacenado en la base de datos de DocumentDB) con la información actual del dispositivo.
- Mensajes de respuesta de comando para actualizar el historial de comandos de dispositivo (almacenado en la base de datos de DocumentDB).

## Primeros pasos

Esta sección le guiará por los componentes de la solución, describe el caso práctico previsto y ofrece ejemplos.

### Panel de supervisión remota
Esta página de la aplicación web usa controles javascript de PowerBI (consulte [Repositorio de imágenes de PowerBI](https://www.github.com/Microsoft/PowerBI-visuals)) para visualizar los datos de salida de los trabajos de Análisis de transmisiones en el almacenamiento de blobs.


### Portal de administración de dispositivos

Esta aplicación web le permite:

- Aprovisionar un nuevo dispositivo que establece el identificador único del dispositivo y genera la clave de autenticación.
- Administrar las propiedades del dispositivo que incluyen las propiedades de visualización existentes y la actualización con nuevas propiedades.
- Enviar comandos a un dispositivo.
- Ver el historial de comandos de un dispositivo.

### Observación del comportamiento de la solución en la nube
Puede ver los recursos aprovisionados en el [Portal de Azure](https://portal.azure.com) y desplazándose al grupo de recursos con el nombre de la solución especificada.

![](media/iot-suite-remote-monitoring-sample-walkthrough/azureportal_01.png)

Cuando ejecuta el ejemplo por primera vez, hay cuatro dispositivos simulados preconfigurados:

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_01.png)

Puede usar el Portal de administración de dispositivos para agregar un nuevo dispositivo simulado:

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_02.png)

Inicialmente, el estado del nuevo dispositivo en el Portal de administración de dispositivos es **Pendiente**:

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_03.png)

Cuando la aplicación ha terminado de implementar el dispositivo simulado, verá que el estado del dispositivo cambia a **En ejecución** en el Portal de administración de dispositivos, tal como se muestra en la siguiente captura de pantalla. El trabajo de Análisis de transmisiones **DeviceInfo** envía información de estado del dispositivo en el Portal de administración de dispositivos.

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_04.png)

Mediante el portal de soluciones, puede enviar comandos como **ChangeSetPointTemp** al dispositivo:

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_05.png)

Cuando el dispositivo informa de que se ha ejecutado correctamente el comando, el estado cambia a **Terminado**:

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_06.png)

Con el portal de soluciones, se puede buscar dispositivos con características específicas, como un número de modelo:

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_07.png)

Puede deshabilitar un dispositivo y, después, quitarlo:

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_08.png)

<!---HONumber=AcomDC_0309_2016-->