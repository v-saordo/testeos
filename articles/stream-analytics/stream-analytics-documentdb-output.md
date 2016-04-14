<properties
	pageTitle="Salida de JSON para Análisis de transmisiones | Microsoft Azure"
	description="Obtenga información sobre cómo el Análisis de transmisiones puede tener como destino DocumentDB de Azure para la salida de JSON, para el archivado de datos y las consultas de latencia baja en datos de JSON no estructurados."
	keywords="Salida de JSON"
	documentationCenter=""
	services="stream-analytics,documentdb"
	authors="jeffstokes72"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="stream-analytics"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="data-services"
	ms.date="02/04/2016"
	ms.author="jeffstok"/>

# Tener como destino DocumentDB de Azure para la salida de JSON de Análisis de transmisiones

El Análisis de transmisiones puede tener como destino [DocumentDB de Azure](https://azure.microsoft.com/services/documentdb/) para la salida de JSON, habilitando el archivado de datos y las consultas de latencia baja en datos de JSON no estructurados. Obtenga información sobre la mejor manera de implementar esta integración.

Aquellos que no estén familiarizados con DocumentDB pueden comenzar por la [ruta de aprendizaje de DocumentDB](https://azure.microsoft.com/documentation/learning-paths/documentdb/).

## Aspectos básicos de DocumentDB como destino de salida
La salida de DocumentDB de Azure en el Análisis de transmisiones permite escribir los resultados del procesamiento de transmisiones como salida de JSON en sus colecciones de DocumentDB. Análisis de transmisiones no crea colecciones en la base de datos, ya no requiere que las cree por adelantado. Esto es para que los costes de facturación de las colecciones de DocumentDB sean transparentes para usted y para que pueda optimizar el rendimiento, la coherencia y la capacidad de las colecciones directamente mediante las [API de DocumentDB](https://msdn.microsoft.com/library/azure/dn781481.aspx). Se recomienda utilizar una base de datos de DocumentDB por trabajo de streaming para separar lógicamente las colecciones de un trabajo de streaming.

A continuación se detallan algunas de las opciones de la colección de DocumentDB.

## Ajustar la coherencia, la disponibilidad y la latencia

Para satisfacer las necesidades de su aplicación, DocumentDB permite optimizar la base de datos y las colecciones y buscar el equilibrio entre coherencia, disponibilidad y latencia. En función de los niveles de coherencia de lectura que requiera su situación en comparación con la latencia de lectura y escritura, puede elegir un nivel de coherencia u otro en la cuenta de base de datos. También de forma predeterminada, DocumentDB permite la indexación sincrónica en cada operación CRUD de la colección. Esta es otra opción útil para controlar el rendimiento de escritura o lectura en DocumentDB. Para obtener más información sobre este tema, revise el artículo [Cambio de los niveles de coherencia de la base de datos y las consultas](../articles/documentdb-consistency-levels.md).

## Elegir un nivel de rendimiento

Se pueden crear colecciones de DocumentDB a 3 niveles de rendimiento diferentes (S1, S2 o S3), que determinan el rendimiento disponible para las operaciones CRUD de esa colección. Además, el rendimiento se ve afectado por los niveles de indexación y coherencia de la colección. Consulte [este artículo](../articles/documentdb-performance-levels.md) para entender estos niveles de rendimiento en detalle.

## Upserts de Análisis de transmisiones

La integración del Análisis de transmisiones con DocumentDB permite insertar o actualizar registros en su colección de DocumentDB en función de una columna de identificador de documento determinada. Esto se conoce también como *Upsert*.

El Análisis de transmisiones utiliza un enfoque Upsert optimista, donde las actualizaciones solo se realizan cuando se produce un error en la inserción debido a un conflicto de identificador de documento. Esta actualización se realiza por el Análisis de transmisiones como una operación PATCH, por lo que permite actualizaciones parciales en el documento; es decir, la adición de nuevas propiedades o la sustitución de una propiedad existente se realiza de forma incremental. Tenga en cuenta que los cambios en los valores de las propiedades de la matriz en el resultado del documento JSON de toda la matriz se sobrescriben; es decir, la matriz no se combina.

## Creación de partición de datos en DocumentDB

Las colecciones de DocumentDB permiten particionar los datos según los patrones de consulta y los requisitos de rendimiento de la aplicación. Cada colección puede contener hasta 10 GB de datos (máximo) y actualmente no hay ninguna manera de escalar verticalmente (o desbordar) una colección. Para escalar horizontalmente, Análisis de transmisiones permite escribir a varias colecciones con un prefijo determinado (consulte los detalles de uso a continuación). Análisis de transmisiones utiliza la estrategia coherente de [resolución de la partición de hash](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.hashpartitionresolver.aspx) basada en la columna PartitionKey ofrecida por el usuario para crear particiones en sus registros de salida. El número de colecciones con el prefijo especificado en el inicio del trabajo de streaming se utiliza como el recuento de las particiones de salida en las que el trabajo escribe en paralelo (colecciones de DocumentDB = particiones de salida). En el caso de una sola colección S3 con una indexación diferida que realiza solo operaciones de inserción, se puede esperar en torno a 0,4 MB/s de rendimiento de escritura. El uso de varias colecciones puede permitirle lograr mayor rendimiento y capacidad.

Si desea aumentar el número de particiones en el futuro, puede que necesite detener su trabajo, volver a crear particiones de los datos de las colecciones existentes para crear nuevas colecciones y, a continuación, reiniciar el trabajo de Análisis de transmisiones. Se incluirá más información sobre el uso de PartitionResolver y la nueva creación de particiones junto con código de ejemplo en una publicación posterior. En el artículo [Creación de particiones de datos en DocumentDB](../articles/documentdb-partition-data.md#developing-a-partitioned-application) también se ofrecen detalles en este sentido.

## Configuración de DocumentDB para salida de JSON

La creación de DocumentDB como una salida en Análisis de transmisiones genera una solicitud de información, tal como se muestra a continuación. En esta sección se proporciona una explicación de la definición de propiedades.

![pantalla de salida de análisis de transmisiones de documentdb](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output.png)

-   **Alias de salida**: un alias para hacer referencia a esta salida en la consulta ASA.  
-   **Nombre de cuenta**: el nombre o el URI del punto de conexión de la cuenta de DocumentDB.  
-   **Clave de cuenta**: la clave de acceso compartido para la cuenta de DocumentDB.  
-   **Base de datos**: el nombre de la base de datos de DocumentDB.  
-   **Patrón de nombre de colección**: el patrón de nombre de colección para las colecciones que se usarán. El formato de nombre de la colección se pueden construir con el token opcional {partition}, donde las particiones comienzan desde 0. A continuación se muestran entradas válidas como ejemplo: 1) MyCollection: debe existir una colección denominada "MyCollection". 2) MyCollection{partición} – deben existir tales colecciones: "MyCollection0", "MyCollection1", "MyCollection2" y así sucesivamente.  
-   **Clave de partición**: el nombre del campo en los eventos de salida que se usa para especificar la clave de la salida de la creación de particiones entre colecciones. Para una salida de colección sencilla, se puede utilizar cualquier columna de salida arbitraria (por ejemplo, PartitionId).  
-   **Identificador de documento**: opcional. Nombre del campo de los eventos de salida utilizado para especificar la clave principal en la que se basan las operaciones de inserción o actualización.  

<!---HONumber=AcomDC_0204_2016-->