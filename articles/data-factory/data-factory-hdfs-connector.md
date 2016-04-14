<properties 
	pageTitle="Movimiento de datos desde HDFS local | Factoría de datos de Azure" 
	description="Obtenga información acerca de cómo mover datos desde HDFS local mediante Factoría de datos de Azure" 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/19/2016" 
	ms.author="spelluru"/>

# Movimiento de datos desde HDFS local mediante Factoría de datos de Azure
En este artículo se describe cómo se puede usar la actividad de copia en Factoría de datos de Azure para mover datos de HDFS local a otro almacén de datos. Este artículo se basa en el artículo sobre [actividades de movimiento de datos](data-factory-data-movement-activities.md) que presenta una introducción general del movimiento de datos con la actividad de copia y las combinaciones del almacén de datos admitidas.

En la actualidad, Factoría de datos solo admite el movimiento de datos desde HDFS local a otros almacenes de datos, pero no el movimiento inverso.


## Habilitación de la conectividad
El servicio Factoría de datos admite la conexión a HDFS local mediante Data Management Gateway. Consulte el artículo sobre cómo [mover datos entre ubicaciones locales y la nube](data-factory-move-data-between-onprem-and-cloud.md) para obtener información acerca de Data Management Gateway, así como instrucciones paso a paso sobre cómo configurar la puerta de enlace. Es preciso que sacar provecho de la puerta de enlace para conectar con HDFS, aunque esté hospedado en una máquina virtual de IaaS de Azure.

Aunque puede instalar la puerta de enlace en el mismo equipo local o en la máquina virtual de Azure como HDFS, se recomienda instalar la puerta de enlace en una máquina independiente o una máquina virtual de IaaS de Azure independiente para evitar la contención de recursos y para mejorar el rendimiento. Al instalar la puerta de enlace en una máquina independiente, la máquina debería poder acceder a la máquina con HDFS.

## Ejemplo: copiar datos de HDFS local a un blob de Azure

En este ejemplo, se muestra cómo copiar datos de un sistema HDFS local al Almacenamiento de blobs de Azure. Sin embargo, se pueden copiar datos **directamente** a cualquiera de los receptores indicados [aquí](data-factory-data-movement-activities.md#supported-data-stores) mediante la actividad de copia en Factoría de datos de Azure.
 
El ejemplo consta de las siguientes entidades de factoría de datos:

1.	Un servicio vinculado del tipo [OnPremisesHdfs](#hdfs-linked-service-properties).
2.	Un servicio vinculado de tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3.	Un conjunto de [datos de entrada](data-factory-create-datasets.md) de tipo [FileShare](#hdfs-dataset-type-properties).
4.	Un [conjunto de datos](data-factory-create-datasets.md) de salida de tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.	Una [canalización](data-factory-create-pipelines.md) con la actividad de copia que usa [FileSystemSource](#hdfs-copy-activity-type-properties) y [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

El ejemplo copia los datos del resultado de una consulta en HDFS local en un blob cada hora. Las propiedades JSON usadas en estos ejemplos se describen en las secciones que aparecen después de los ejemplos.

Como primer paso, configure la puerta de enlace de administración de datos según las instrucciones del artículo sobre cómo [mover datos entre las ubicaciones locales y la nube](data-factory-move-data-between-onprem-and-cloud.md).

**Servicio vinculado de HDFS** En este ejemplo se usa la autenticación de Windows. Consulte la sección [Propiedades del servicio vinculado de HDFS](#hdfs-linked-service-properties) para conocer los diferentes tipos de autenticación que se pueden usar.

	{
	    "name": "HDFSLinkedService",
	    "properties":
	    {
	        "type": "Hdfs",
	        "typeProperties":
	        {
	            "authenticationType": "Windows",
	            "userName": "Administrator",
	            "password": "password",
	            "url" : "http://<machine>:50070/webhdfs/v1/",
	            "gatewayName": "mygateway"
	        }
	    }
	}

**Servicio vinculado de Almacenamiento de Azure**

	{
	  "name": "AzureStorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**Conjunto de datos de entrada de HDFS** Este conjunto de datos hace referencia a la carpeta DataTransfer/UnitTest/ de HDFS. La canalización copia todos los archivos de su carpeta en el destino.

Si se establece "external": "true" y se especifica la directiva externalData (opcional), se informa al servicio Factoría de datos que la tabla es externa a la factoría de datos y que no lo ha producido ninguna actividad de la factoría de datos.
	
	{
	    "name": "InputDataset",
	    "properties": {
	        "type": "FileShare",
	        "linkedServiceName": "HDFSLinkedService",
	        "typeProperties": {
	            "folderPath": "DataTransfer/UnitTest/"
	        },
	        "external": true,
	        "availability": {
	            "frequency": "Hour",
	            "interval":  1
	        }
	    }
	}




**Conjunto de datos de salida de blob de Azure**

Los datos se escriben en un nuevo blob cada hora (frecuencia: hora, intervalo: 1). La ruta de acceso de la carpeta para el blob se evalúa dinámicamente según la hora de inicio del segmento que se está procesando. La ruta de acceso de la carpeta usa las partes year, month, day y hours de la hora de inicio.

	{
	    "name": "OutputDataset",
	    "properties": {
	        "type": "AzureBlob",
	        "linkedServiceName": "AzureStorageLinkedService",
	        "typeProperties": {
	            "folderPath": "mycontainer/hdfs/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
	            "format": {
	                "type": "TextFormat",
	                "rowDelimiter": "\n",
	                "columnDelimiter": "\t"
	            },
	            "partitionedBy": [
	                {
	                    "name": "Year",
	                    "value": {
	                        "type": "DateTime",
	                        "date": "SliceStart",
	                        "format": "yyyy"
	                    }
	                },
	                {
	                    "name": "Month",
	                    "value": {
	                        "type": "DateTime",
	                        "date": "SliceStart",
	                        "format": "%M"
	                    }
	                },
	                {
	                    "name": "Day",
	                    "value": {
	                        "type": "DateTime",
	                        "date": "SliceStart",
	                        "format": "%d"
	                    }
	                },
	                {
	                    "name": "Hour",
	                    "value": {
	                        "type": "DateTime",
	                        "date": "SliceStart",
	                        "format": "%H"
	                    }
	                }
	            ]
	        },
	        "availability": {
	            "frequency": "Hour",
	            "interval": 1
	        }
	    }
	}



**Canalización con actividad de copia**

La canalización contiene una actividad de copia que está configurada para usar los conjuntos de datos de entrada y de salida y está programada para ejecutarse cada hora. En la definición de JSON de canalización, el tipo **source** se establece en **FileSystemSource** y el tipo **sink** se establece en **BlobSink**. La consulta SQL especificada para la propiedad **query** selecciona los datos de la última hora que se van a copiar.
	
	{
	    "name": "pipeline",
	    "properties":
	    {
	        "activities":
	        [
	            {
	                "name": "HdfsToBlobCopy",
	                "inputs": [ {"name": "InputDataset"} ],
	                "outputs": [ {"name": "OutputDataset"} ],
	                "type": "Copy",
	                "typeProperties":
	                {
	                    "source":
	                    {
	                        "type": "FileSystemSource"
	                    },
	                    "sink":
	                    {
	                        "type": "BlobSink"
	                    }
	                },
	                "policy":
	                {
	                    "concurrency": 1,
	                    "executionPriorityOrder": "NewestFirst",
	                    "retry": 1,
	                    "timeout": "00:05:00"
	                }
	            }
	        ],
	        "start": "2014-06-01T18:00:00Z",
	        "end": "2014-06-01T19:00:00Z"
	    }
	}



## Propiedades del servicio vinculado de HDFS

En la tabla siguiente se proporciona la descripción de los elementos JSON específicos del servicio vinculado de HDFS.

| Propiedad | Descripción | Obligatorio |
| -------- | ----------- | -------- | 
| type | La propiedad type debe establecerse en: **Hdfs** | Sí | 
| URL | Dirección URL a HDFS | Sí |
| encryptedCredential | Salida de [New-AzureRMDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx) de la credencial de acceso. | No |
| userName | Nombre de usuario para la autenticación de Windows | Sí (para la autenticación de Windows)
| contraseña | Contraseña para la autenticación de Windows | Sí (para la autenticación de Windows)
| authenticationType | Windows o de forma anónima. | Sí |
| gatewayName | Nombre de la puerta de enlace que el servicio Factoría de datos debe usar para conectarse a HDFS. | Sí |   

Para más información acerca de cómo configurar las credenciales para un sistema HDFS local, consulte [Configuración de credenciales y seguridad](data-factory-move-data-between-onprem-and-cloud.md#setting-credentials-and-security).

### Uso de autenticación anónima

	{
	    "name": "hdfs",
	    "properties":
	    {
	        "type": "Hdfs",
	        "typeProperties":
	        {
	            "authenticationType": "Anonymous",
	            "userName": "hadoop",
	            "url" : "http://<machine>:50070/webhdfs/v1/",
	            "gatewayName": "mygateway"
	        }
	    }
	}


### Uso de autenticación de Windows
	
	{
	    "name": "hdfs",
	    "properties":
	    {
	        "type": "Hdfs",
	        "typeProperties":
	        {
	            "authenticationType": "Windows",
	            "userName": "Administrator",
	            "password": "password",
	            "url" : "http://<machine>:50070/webhdfs/v1/",
	            "gatewayName": "mygateway"
	        }
	    }
	}
 


## Propiedades del tipo de conjunto de datos de HDFS

Para una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, vea el artículo [Creación de conjuntos de datos](data-factory-create-datasets.md). Las secciones como structure, availability y policy de un conjunto de datos JSON son similares en todos los tipos de conjunto de datos (SQL Azure, blob de Azure, tabla de Azure, etc.).

La sección **typeProperties** es diferente en cada tipo de conjunto de datos y proporciona información acerca de la ubicación de los datos en el almacén de datos. La sección typeProperties del conjunto de datos del tipo **FileShare** (que incluye el conjunto de datos de HDFS) tiene las propiedades siguientes:

Propiedad | Descripción | Obligatorio
-------- | ----------- | --------
folderPath | Ruta de acceso a la carpeta. Ejemplo: myfolder<p>Use el carácter de escape ' \\ ' para los caracteres especiales de la cadena. Por ejemplo: para folder\\subfolder, especifique folder\\subfolder y para d:\\samplefolder, especifique d:\\samplefolder.</p><p>Puede combinarlo con **partitionBy** para tener rutas de acceso a carpetas basadas en las fechas y horas de inicio y finalización de los segmentos.</p> | Sí
fileName | Especifique el nombre del archivo en **folderPath** si quiere que la tabla haga referencia a un archivo específico de la carpeta. Si no especifica ningún valor para esta propiedad, la tabla apunta a todos los archivos de la carpeta.<p>Si no se especifica fileName para un conjunto de datos de salida, el nombre del archivo tendría este formato:</p><p>Data.<Guid>. txt (por ejemplo: : Data.0a405f8a 93ff 4c6f b3be f69616f1df7a.txt</p> | No
partitionedBy | partitionedBy se puede usar para especificar un folderPath dinámico, un nombre de archivo para datos de series temporales. Por ejemplo, folderPath se parametriza por cada hora de datos. | No
fileFilter | Especifique el filtro que se va a usar para seleccionar un subconjunto de archivos de folderPath, en lugar de todos los archivos. <p>Los valores permitidos son: * (varios caracteres) y ? (un solo carácter).</p><p>Ejemplo 1: "fileFilter": "*. log"</p>Ejemplo 2: "fileFilter": 2014-1-?. txt"</p><p>**Nota**: fileFilter es aplicable a un conjunto de datos de FileShare de entrada</p> | No
| compresión | Especifique el tipo y el nivel de compresión de los datos. Los tipos admitidos son: **GZip**, **Deflate** y **BZip2** y los niveles admitidos son: **óptimo** y **más rápido**. Tenga en cuenta que esta vez no se admite la configuración de compresión de los datos que se encuentran en **AvroFormat**. Vea la sección [Compatibilidad de compresión](#compression-support) para más detalles. | No |
| formato | Se admiten dos tipos de formatos: **TextFormat** y **AvroFormat**. Deberá establecer la propiedad type en format en cualquiera de estos valores. Cuando el formato es TextFormat, puede especificar propiedades opcionales adicionales para format. Consulte la sección [Especificación de TextFormat](#specifying-textformat) a continuación para obtener más detalles. | No


> [AZURE.NOTE] filename y fileFilter no pueden usarse simultáneamente.


### Uso de la propiedad partitionedBy

Como ya se ha indicado, con partitionedBy se puede especificar un folderPath dinámico, un nombre de archivo para datos de series temporales. Esto se puede hacer con las macros de Factoría de datos y las variables de sistema SliceStart y SliceEnd, que indican el periodo de tiempo lógico de un segmento de datos especificado.

Consulte los artículos [Creación de conjuntos de datos](data-factory-create-datasets.md), [Programación y ejecución con Factoría de datos](data-factory-scheduling-and-execution.md), y [Creación de canalizaciones](data-factory-create-pipelines.md) para conocer más detalles sobre los conjuntos de datos de series temporales, la programación y los segmentos.

#### Muestra 1:

	"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
	"partitionedBy": 
	[
	    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
	],

En el anterior ejemplo {Slice} se reemplaza por el valor de la variable del sistema SliceStart de la Factoría de datos en el formato (YYYYMMDDHH) especificado. SliceStart hace referencia a la hora de inicio del segmento. folderPath es diferente para cada segmento. Por ejemplo: wikidatagateway/wikisampledataout/2014100103 o wikidatagateway/wikisampledataout/2014100104.

#### Ejemplo 2:

	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
	"fileName": "{Hour}.csv",
	"partitionedBy": 
	 [
	    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
	    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
	    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
	    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
	],

En el ejemplo anterior, year, month, day y time de SliceStart se extraen en variables independientes que se usan en las propiedades folderPath y fileName.

### Especificación de TextFormat

Si se establece el formato en **TextFormat**, puede especificar las siguientes propiedades **opcionales** en la sección **format**.

| Propiedad | Descripción | Obligatorio |
| -------- | ----------- | -------- |
| columnDelimiter | El carácter que se usa como separador de columnas en un archivo. Actualmente solo se permite un carácter. Esta etiqueta es opcional. El valor predeterminado es coma (,). | No |
| rowDelimiter | El carácter que se usa como separador de filas en un archivo. Actualmente solo se permite un carácter. Esta etiqueta es opcional. El valor predeterminado es cualquiera de los siguientes: ["\\r\\n", "\\r", "\\n"]. | No |
| escapeChar | <p>El carácter especial que se usa para anular el delimitador de columna que se muestra en el contenido. Esta etiqueta es opcional. No hay ningún valor predeterminado. No debe especificar más de un carácter para esta propiedad.</p><p>Por ejemplo, si tiene la coma (,) como delimitador de columna, pero desea usar el carácter de coma en el texto (por ejemplo: "Hello, world"), puede definir '$' como carácter de escape y usar la cadena "Hello$, world" en el origen.</p><p>Tenga en cuenta que no se pueden especificar escapeChar y quoteChar a la vez para una tabla.</p> | No | 
| quoteChar | <p>El carácter especial se usa para poner entre comillas el valor de la cadena. Los delimitadores de columna y fila entre comillas se tratarán como parte del valor de la cadena. Esta etiqueta es opcional. No hay ningún valor predeterminado. No debe especificar más de un carácter para esta propiedad.</p><p>Por ejemplo, si tiene la coma (,) como delimitador de columna, pero desea usar el carácter de coma en el texto (por ejemplo: <Hello  world>), puede definir ‘"’ como comillas y usar la cadena <"Hello, world"> en el origen. Esta propiedad es aplicable a las tablas de entrada y de salida.</p><p>Tenga en cuenta que no se pueden especificar escapeChar y quoteChar a la vez para una tabla.</p> | No |
| nullValue | <p>Los caracteres que se usan para representar un valor nulo en el contenido del archivo de blob. Esta etiqueta es opcional. El valor predeterminado es “\\N”.</p><p>Por ejemplo, basándose en el ejemplo anterior , "NaN" en el blob se traducirá como valor nulo al copiar en, por ejemplo, SQL Server.</p> | No |
| encodingName | Especifique el nombre de codificación. Para obtener la lista de nombres de codificación válidos, vea: Propiedad [Encoding.EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx). Por ejemplo: windows-1250 o shift\_jis. El valor predeterminado es: UTF-8. | No | 

#### Muestras
En el ejemplo siguiente se muestran algunas de las propiedades de formato de TextFormat.

	"typeProperties":
	{
	    "folderPath": "mycontainer/myfolder",
	    "fileName": "myblobname"
	    "format":
	    {
	        "type": "TextFormat",
	        "columnDelimiter": ",",
	        "rowDelimiter": ";",
	        "quoteChar": """,
	        "NullValue": "NaN"
	    }
	},

Para usar escapeChar, en lugar de quoteChar, reemplace la línea con quoteChar por la siguiente:

	"escapeChar": "$",

### Especificación de AvroFormat
Si se establece el formato en AvroFormat, no es preciso especificar propiedades en la sección Format de la sección typeProperties. Ejemplo:

	"format":
	{
	    "type": "AvroFormat",
	}

Para usar el formato Avro en una tabla de Hive, puede consultar [Tutorial de Apache Hive](https://cwiki.apache.org/confluence/display/Hive/AvroSerDe).

[AZURE.INCLUDE [data-factory-compression](../../includes/data-factory-compression.md)]

## Propiedades de tipo de actividad de copia de HDFS

Para obtener una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo [Creación de canalizaciones](data-factory-create-pipelines.md). Propiedades como nombre, descripción, tablas de entrada y salida, varias directivas, etc. están disponibles para todos los tipos de actividades.

Por otro lado, las propiedades disponibles en la sección typeProperties de la actividad varían con cada tipo de actividad y, en caso de la actividad de copia, varían en función de los tipos de orígenes y receptores.

En caso de actividad de copia si el origen es del tipo **FileSystemSource**, estarán disponibles las propiedades siguientes en la sección typeProperties:

**FileSystemSource** admite las siguientes propiedades:

| Propiedad | Descripción | Valores permitidos | Obligatorio |
| -------- | ----------- | -------------- | -------- |
| recursive | Indica si los datos se leen de forma recursiva de las subcarpetas o solo de la carpeta especificada. | True, False (predeterminada)| No |

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

<!---HONumber=AcomDC_0224_2016-->