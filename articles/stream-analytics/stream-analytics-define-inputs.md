<properties
	pageTitle="Conexión de datos: entradas de transmisiones de datos de una transmisión de eventos | Microsoft Azure"
	description="Obtenga información sobre cómo configurar una conexión de datos a Análisis de transmisiones que se denomina ";entradas";. Entre las entradas se incluyen una transmisión de datos de los eventos y también datos de referencia."
	keywords="transmisión de datos, conexión de datos, transmisión de eventos"
	services="stream-analytics"
	documentationCenter=""
	authors="jeffstokes72"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="stream-analytics"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="data-services"
	ms.date="02/22/2016"
	ms.author="jeffstok"/>

# Conexión de datos: obtenga información sobre las entradas de transmisiones de datos desde eventos para el Análisis de transmisiones

La conexión de datos para el Análisis de transmisiones es un flujo de datos de eventos desde un origen de datos. Se denomina "entrada". El Análisis de transmisiones tiene integración de primera clase con el Centro de eventos de orígenes de la transmisión de datos de Azure, el Centro de IoT y el almacenamiento de blobs que pueden proceder de la misma suscripción de Azure o de otra diferente como su trabajo de análisis.

## Tipos de entrada de datos: datos de referencia y de transmisión de datos
A medida que los datos se insertan en un origen de datos, el trabajo de Análisis de transmisiones los consume y los procesa en tiempo real. Las entradas se dividen en dos tipos distintos: entradas de flujo de datos y entradas de datos de referencia.

### Entradas de secuencia de datos
Un flujo de datos es una secuencia ilimitada de eventos que llegan a lo largo del tiempo. Los trabajos de Análisis de transmisiones deben incluir, al menos, una entrada de secuencia de datos para que el trabajo la consuma y transforme. Almacenamiento de blobs, Centros de eventos y Centros de IoT se admiten como orígenes de entrada de flujos de datos. Centros de eventos se usan para recopilar flujos de eventos desde varios dispositivos y servicios, como fuentes de actividades de medios sociales, información sobre acciones o datos de sensores. Los Centros de IoT están optimizados para recopilar datos de dispositivos conectados en escenarios del Internet de las cosas (IoT). El almacenamiento de blobs puede usarse como origen de entrada para la introducción de datos en masa como flujo.

### Datos de referencia
Análisis de transmisiones también admite un segundo tipo de entrada, conocido como datos de referencia. Se trata de datos auxiliares que son estáticos o que cambian con poca frecuencia y se usan normalmente para realizar correlaciones y búsquedas. Almacenamiento de blobs de Azure es el único origen de entrada admitido actualmente para los datos de referencia. Los blobs de origen de datos de referencia están limitados a 100 MB de tamaño. Para obtener información sobre cómo crear entradas de datos de referencia, consulte [Uso de datos de referencia](stream-analytics-use-reference-data.md).

## Crear una entrada de transmisión de datos con un Centro de eventos

Los [Centros de eventos de Azure](https://azure.microsoft.com/services/event-hubs/) son un servicio de introducción de eventos de suscripción-publicación altamente escalable. Puede recopilar de millones de eventos por segundo para que pueda procesar y analizar grandes cantidades de datos generados por los dispositivos y aplicaciones conectados. Es una de las entradas más usadas para Análisis de transmisiones. Centros de eventos y Análisis de transmisiones juntos proporcionan a los clientes una solución total para realizar análisis en tiempo real. Centros de eventos permiten a los clientes suministrar eventos a Azure en tiempo real y los trabajos de Análisis de transmisiones pueden procesarlos en tiempo real. Por ejemplo, los clientes pueden enviar clics en la web, lecturas del sensor, eventos de registro en línea en los centros de eventos y crear trabajos de Análisis de transmisiones para usar centros de eventos como las secuencias de datos de entrada para filtrar, agregar y correlacionar en tiempo real.

Es importante tener en cuenta que la marca de tiempo predeterminada de los eventos procedentes de los Centros de eventos de Análisis de transmisiones es la marca de tiempo del evento que ha llegado al Centro de eventos, que es EventEnqueuedUtcTime. Para procesar los datos como una transmisión mediante una marca de tiempo en la carga del evento, se debe usar la palabra clave [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx).

### Grupos de consumidores

Cada entrada de Centro de eventos de Análisis de transmisiones se debe configurar para tener su propio grupo de consumidores. Cuando un trabajo contiene varias entradas o autocombinación, es posible que más de un lector de bajada lea alguna de las entradas, lo que afecta la cantidad de lectores que existen en un solo grupo de consumidores. Para evitar superar el límite de Centro de eventos de 5 lectores por grupo de consumidores por partición, una práctica recomendable es designar un grupo de consumidores para cada trabajo de Análisis de transmisiones. Tenga en cuenta que también hay un límite de 20 grupos de consumidores por Centro de eventos. Para obtener detalles, consulte la [Guía de programación de Centros de eventos](../event-hubs/event-hubs-programming-guide.md).

## Configurar el Centro de eventos como transmisión de datos de entrada

La siguiente tabla explica cada propiedad en la pestaña de entrada de Centro de eventos con su descripción:

| NOMBRE DE PROPIEDAD | DESCRIPCIÓN |
|------|------|
| Alias de entrada | Nombre descriptivo que se usará en la consulta de trabajo para hacer referencia a esta entrada. |
| Espacio de nombres de Bus de servicio | Un espacio de nombres del bus de servicio es un contenedor para un conjunto de entidades de mensajería. Al crear un nuevo centro de eventos, también se crea un espacio de nombres del Bus de servicio. |
| Centro de eventos | El nombre de la entrada del Centro de eventos. |
| Nombre de la directiva del centro de eventos | La directiva de acceso compartido, que se puede crear en la pestaña Configuración de Centro de eventos. Cada directiva de acceso compartido tiene un nombre, los permisos establecidos y las claves de acceso. |
| Clave de la directiva de centro de eventos | La clave de acceso compartido usada para autenticar el acceso al espacio de nombres del Bus de servicio. |
| Grupo de consumidores del Centro de eventos (opcional) | El grupo de consumidores para introducir datos desde el centro de eventos. Si no se especifica, los trabajos de Análisis de transmisiones utilizan el grupo de consumidores predeterminado para introducir datos desde el centro de eventos. Se recomienda usar un grupo de consumidores distinto para cada trabajo de Análisis de transmisiones. |
| Formato de serialización de eventos | Para asegurarse de que las consultas funcionen como se espera, Análisis de transmisiones debe saber cuál es el formato de serialización (JSON, CSV o Avro) que usa para los flujos de datos entrantes. |
| Codificación | Por el momento, UTF-8 es el único formato de codificación compatible. |

Cuando los datos proceden de un origen de Centro de eventos, puede acceder a algunos de los campos de metadatos en la consulta de Análisis de transmisiones. En la tabla siguiente se enumeran los campos y su descripción.

| PROPIEDAD | DESCRIPCIÓN |
|------|------|
| EventProcessedUtcTime | Fecha y hora en que se procesó el evento por Análisis de transmisiones. |
| EventEnqueuedUtcTime | Fecha y la hora en que el Centro de eventos recibió el evento. |
| PartitionId | Identificador de partición de base cero para el adaptador de entrada. |

Por ejemplo, puede escribir una consulta como la siguiente:

````
SELECT
	EventProcessedUtcTime,
	EventEnqueuedUtcTime,
	PartitionId
FROM Input
````

## Crear una entrada de transmisión de datos del Centro de IoT

El centro de Iot de Azure es un servicio de introducción de eventos de suscripción-publicación altamente escalable optimizado para escenarios de IoT. Es importante tener en cuenta que la marca de tiempo predeterminada de los eventos procedentes de los Centros de IoT de Análisis de transmisiones es la marca de tiempo del evento que ha llegado al Centro de IoT, que es EventEnqueuedUtcTime. Para procesar los datos como una transmisión mediante una marca de tiempo en la carga del evento, se debe usar la palabra clave [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx).

### Grupos de consumidores

Cada entrada de Centro de IoT de Análisis de transmisiones se debe configurar para tener su propio grupo de consumidores. Cuando un trabajo contiene varias entradas o autocombinación, es posible que más de un lector de bajada lea alguna de las entradas, lo que afecta la cantidad de lectores que existen en un solo grupo de consumidores. Para evitar superar el límite de Centro de IoT de 5 lectores por grupo de consumidores por partición, una práctica recomendable es designar un grupo de consumidores para cada trabajo de Análisis de transmisiones.

## Configurar el Centro de IoT como transmisión de datos de entrada

La siguiente tabla explica cada propiedad en la pestaña de entrada de Centro de IoT con su descripción:

| NOMBRE DE PROPIEDAD | DESCRIPCIÓN |
|------|------|
| Alias de entrada | Nombre descriptivo que se usará en la consulta de trabajo para hacer referencia a esta entrada. |
| Centro de IoT | Un centro de IoT es un contenedor para un conjunto de entidades de mensajería. |
| Extremo | El nombre de su extremo del Centro de IoT. |
| Nombre de directiva de acceso compartido | La directiva de acceso compartido para conceder acceso al Centro de IoT. Cada directiva de acceso compartido tiene un nombre, los permisos establecidos y las claves de acceso. |
| Clave de directiva de acceso compartido | La clave de acceso compartido que se usa para autenticar el acceso al Centro de IoT. |
| Grupo de consumidores (opcional) | El grupo de consumidores para introducir datos desde el Centro de IoT. Si no se especifica, los trabajos de Análisis de transmisiones utilizan el grupo de consumidores predeterminado para introducir datos desde el Centro de IoT. Se recomienda usar un grupo de consumidores distinto para cada trabajo de Análisis de transmisiones. |
| Formato de serialización de eventos | Para asegurarse de que las consultas funcionen como se espera, Análisis de transmisiones debe saber cuál es el formato de serialización (JSON, CSV o Avro) que usa para los flujos de datos entrantes. |
| Codificación | Por el momento, UTF-8 es el único formato de codificación compatible. |

Cuando los datos proceden de un origen de Centro de IoT, puede acceder a algunos de los campos de metadatos en la consulta de Análisis de transmisiones. En la tabla siguiente se enumeran los campos y su descripción.

| PROPIEDAD | DESCRIPCIÓN |
|------|------|
| EventProcessedUtcTime | Fecha y la hora en que se produjo el evento. |
| EventEnqueuedUtcTime | La fecha y la hora en que el evento se recibió por el Centro de IoT. |
| PartitionId | Identificador de partición de base cero para el adaptador de entrada. |
| IoTHub.MessageId | Se usa para correlacionar la comunicación bidireccional en el Centro de IoT. |
| IoTHub.CorrelationId | Se usa en las respuestas a mensajes y en los comentarios en el Centro de IoT. |
| IoTHub.ConnectionDeviceId | El id. autenticado usado para enviar este mensaje, marcado en los mensajes servicebound por el Centro de IoT. |
| IoTHub.ConnectionDeviceGenerationId | El generationId del dispositivo autenticado usado para enviar este mensaje, marcado en los mensajes servicebound por el Centro de IoT. |
| IoTHub.EnqueuedTime | Hora en la que el Centro de IoT se recibió el mensaje. |
| IoTHub.StreamId | Propiedad de evento personalizado agregado por el dispositivo remitente. |

## Crear una entrada de transmisión de datos de Almacenamiento de blobs

El almacenamiento de blobs ofrece una solución rentable y escalable para aquellos escenarios con grandes cantidades de datos no estructurados para almacenar en la nube. Los datos del [almacenamiento de blobs](https://azure.microsoft.com/services/storage/blobs/) generalmente se consideran como datos "en reposo", pero Análisis de transmisiones puede procesarlos como una transmisión de datos. Un escenario común para las entradas de Almacenamiento de blobs con Análisis de transmisiones es el procesamiento de registros, donde la telemetría se captura desde un sistema y es necesario analizarla y procesarla para extraer datos significativos.

Es importante tener en cuenta que la marca de tiempo predeterminada de los eventos del almacenamiento de blobs en Análisis de transmisiones es la marca de tiempo cuando se modificó el blob por última vez, que es *isBlobLastModifiedUtcTime*. Para procesar los datos como una transmisión mediante una marca de tiempo en la carga del evento, se debe usar la palabra clave [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx).

> [AZURE.NOTE] Análisis de transmisiones no admite que se agregue contenido a un blob existente. Análisis de transmisiones solo visualizará un blob una vez, y no procesará los cambios que se realicen tras esta lectura. El procedimiento recomendado es cargar todos los datos una vez y no agregar eventos adicionales al almacén de blobs.

En la tabla siguiente se explica cada propiedad en la pestaña de entrada de almacenamiento de blobs con su descripción:

<table>
<tbody>
<tr>
<td>NOMBRE DE PROPIEDAD</td>
<td>DESCRIPCIÓN</td>
</tr>
<tr>
<td>Alias de entrada</td>
<td>Nombre descriptivo que se usará en la consulta de trabajo para hacer referencia a esta entrada.</td>
</tr>
<tr>
<td>Cuenta de almacenamiento</td>
<td>El nombre de la cuenta de almacenamiento donde se encuentran los archivos de blob.</td>
</tr>
<tr>
<td>Clave de cuenta de almacenamiento</td>
<td>La clave secreta asociada con la cuenta de almacenamiento.</td>
</tr>
<tr>
<td>Contenedor de almacenamiento
</td>
<td>Los contenedores proporcionan una agrupación lógica de los blobs almacenados en el servicio BLOB de Microsoft Azure. Cuando carga un blob al servicio BLOB, debe especificar un contenedor para ese blob.</td>
</tr>
<tr>
<td>Patrón del prefijo de la ruta de acceso [opcional]</td>
<td>La ruta de acceso de archivo para ubicar los blobs dentro del contenedor especificado. Dentro de la ruta, puede elegir especificar una o más instancias de las siguientes tres variables:<BR>{date}, {time},<BR>{partition}<BR>Ejemplo 1: cluster1/logs/{date}/{time}/{partition}<BR>Ejemplo 2: cluster1/logs/{date}<P>Tenga en cuenta que "*" no es un valor permitido para pathprefix. Solo se permiten <a HREF="https://msdn.microsoft.com/library/azure/dd135715.aspx">caracteres de Blob de Azure</a>.</td>
</tr>
<tr>
<td>Formato de fecha [opcional]</td>
<td>Si el token de fecha se usa en la ruta de acceso de prefijo, puede seleccionar el formato de fecha en el que se organizan los archivos. Ejemplo: AAAA/MM/DD</td>
</tr>
<tr>
<td>Formato de hora [opcional]</td>
<td>Si el token de hora se usa en la ruta de acceso de prefijo, puede seleccionar el formato de hora en el que se organizan los archivos. Actualmente, el único valor admitido es HH.</td>
</tr>
<tr>
<td>Formato de serialización de eventos</td>
<td>Para asegurarse de que las consultas funcionen como se espera, Análisis de transmisiones debe saber cuál es el formato de serialización (JSON, CSV o Avro) que usa para los flujos de datos entrantes.</td>
</tr>
<tr>
<td>Codificación</td>
<td>Por el momento, UTF-8 es el único formato de codificación compatible para CSV y JSON.</td>
</tr>
<tr>
<td>Delimitador</td>
<td>Análisis de transmisiones admite un número de delimitadores comunes para la serialización de datos en formato CSV. Los valores admitidos son la coma, punto y coma, espacio, tabulador y barra vertical.</td>
</tr>
</tbody>
</table>

Cuando los datos proceden de un origen de almacenamiento de blobs, puede acceder a algunos de los campos de metadatos de la consulta de Análisis de transmisiones. En la tabla siguiente se enumeran los campos y su descripción.

| PROPIEDAD | DESCRIPCIÓN |
|------|------|
| BlobName | Nombre del blob de entrada de donde procede el evento. |
| EventProcessedUtcTime | Fecha y hora en que se procesó el evento por Análisis de transmisiones. |
| BlobLastModifiedUtcTime | Fecha y la hora en que se modificó por última vez el blob. |
| PartitionId | Identificador de partición de base cero para el adaptador de entrada. |

Por ejemplo, puede escribir una consulta como la siguiente:

````
SELECT
	BlobName,
	EventProcessedUtcTime,
	BlobLastModifiedUtcTime
FROM Input
````


## Obtener ayuda
Para obtener más ayuda, pruebe nuestro [foro de Análisis de transmisiones de Azure](https://social.msdn.microsoft.com/Forums/es-ES/home?forum=AzureStreamAnalytics)

## Pasos siguientes
Ha obtenido información sobre las opciones de conexión de datos de Azure para sus trabajos de Análisis de transmisiones. Para obtener más información sobre Análisis de transmisiones, vea:

- [Introducción al uso de Análisis de transmisiones de Azure](stream-analytics-get-started.md)
- [Escalación de trabajos de Análisis de transmisiones de Azure](stream-analytics-scale-jobs.md)
- [Referencia del lenguaje de consulta de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referencia de API de REST de administración de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301

<!---HONumber=AcomDC_0224_2016-->