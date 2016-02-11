<properties 
	pageTitle="Movimiento de datos hacia y desde Blob de Azure | Factoría de datos de Azure" 
	description="Obtenga información acerca de cómo mover los datos hacia y desde el almacenamiento de blobs de Azure mediante Factoría de datos de Azure." 
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
	ms.date="01/26/2016" 
	ms.author="spelluru"/>

# Movimiento de datos hacia y desde Blob de Azure mediante Factoría de datos de Azure
En este artículo se describe cómo puede usar la actividad de copia en la Factoría de datos de Azure para mover datos a un blob de Azure desde otro almacén de datos y viceversa. Este artículo se basa en el artículo sobre [actividades de movimiento de datos](data-factory-data-movement-activities.md) que presenta una introducción general del movimiento de datos con la actividad de copia y las combinaciones del almacén de datos admitidas.

## Ejemplo: copia de datos de un blob de Azure a SQL de Azure
El ejemplo siguiente muestra:

1.	Un servicio vinculado de tipo [AzureSqlDatabase](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties).
2.	Un servicio vinculado de tipo [AzureStorage](#azure-storage-linked-service-properties).
3.	Un [conjunto de datos](data-factory-create-datasets.md) de entrada de tipo [AzureBlob](#azure-blob-dataset-type-properties).
4.	Un [conjunto de datos](data-factory-create-datasets.md) de salida de tipo [AzureSqlTable](data-factory-azure-sql-connector.md#azure-sql-dataset-type-properties).
4.	Una [canalización](data-factory-create-pipelines.md) con una actividad de copia que usa [BlobSource](#azure-blob-copy-activity-type-properties) y [SqlSink](data-factory-azure-sql-connector.md#azure-sql-copy-activity-type-properties).

El ejemplo copia los datos que pertenecen a una serie temporal desde un blob de Azure a una tabla de una base de datos SQL de Azure cada hora. Las propiedades JSON usadas en estos ejemplos se describen en las secciones que aparecen después de los ejemplos.

**Servicio vinculado SQL de Azure:**

	{
	  "name": "AzureSqlLinkedService",
	  "properties": {
	    "type": "AzureSqlDatabase",
	    "typeProperties": {
	      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
	    }
	  }
	}

**Servicio vinculado de Almacenamiento de Azure:**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

Factoría de datos de Azure admite dos tipos de servicios vinculados de Almacenamiento de Azure: **AzureStorage** y **AzureStorageSas**. En el primer caso, especifique la cadena de conexión que incluye la clave de cuenta. En el segundo, especifique el Uri de firma de acceso compartido (SAS). Vea la sección [Servicios vinculados](#linked-services) para más información.

**Conjunto de datos de entrada de blob de Azure:**

Los datos se seleccionan de un nuevo blob cada hora (frecuencia: hora, intervalo: 1). La ruta de acceso de la carpeta y el nombre de archivo para el blob se evalúan dinámicamente según la hora de inicio del segmento que se está procesando. La ruta de acceso de la carpeta usa la parte year, month y day de la hora de inicio y el nombre de archivo, la parte hour. El valor "external": "true" informa al servicio Factoría de datos que esta tabla es externa a la factoría y no la produce una actividad de la factoría de datos.

	{
	  "name": "AzureBlobInput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/",
	      "fileName": "{Hour}.csv",
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
	      ],
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": ",",
	        "rowDelimiter": "\n"
	      }
	    },
	    "external": true,
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    },
	    "policy": {
	      "externalData": {
	        "retryInterval": "00:01:00",
	        "retryTimeout": "00:10:00",
	        "maximumRetry": 3
	      }
	    }
	  }
	}

**Conjunto de datos de salida SQL de Azure:**

El ejemplo copia los datos a una tabla denominada "MyTable" en una base de datos SQL de Azure. Debe crear la tabla en la base de datos SQL de Azure con el mismo número de columnas que espera que contenga el archivo CSV de blob. Se agregan nuevas filas a la tabla cada hora.

	{
	  "name": "AzureSqlOutput",
	  "properties": {
	    "type": "AzureSqlTable",
	    "linkedServiceName": "AzureSqlLinkedService",
	    "typeProperties": {
	      "tableName": "MyOutputTable"
	    },
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}

**Canalización con actividad de copia:**

La canalización contiene una actividad de copia que está configurada para usar los conjuntos de datos de entrada y de salida y está programada para ejecutarse cada hora. En la definición de JSON de canalización, el tipo **source** se establece en **BlobSource** y el tipo **sink**, en **SqlSink**.

	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2014-06-01T18:00:00",
	    "end":"2014-06-01T19:00:00",
	    "description":"pipeline with copy activity",
	    "activities":[  
	      {
	        "name": "AzureBlobtoSQL",
	        "description": "Copy Activity",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": "AzureBlobInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureSqlOutput"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "BlobSource",
	            "blobColumnSeparators": ","
	          },
	          "sink": {
	            "type": "SqlSink"
	          }
	        },
	       "scheduler": {
	          "frequency": "Hour",
	          "interval": 1
	        },
	        "policy": {
	          "concurrency": 1,
	          "executionPriorityOrder": "OldestFirst",
	          "retry": 0,
	          "timeout": "01:00:00"
	        }
	      }
	      ]
	   }
	}

## Ejemplo: copia de datos SQL de Azure a un blob de Azure
El ejemplo siguiente muestra:

1.	Un servicio vinculado de tipo [AzureSqlDatabase](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties).
2.	Un servicio vinculado de tipo [AzureStorage](#azure-storage-linked-service-properties).
3.	Un[ conjunto de datos](data-factory-create-datasets.md) de entrada de tipo [AzureSqlTable](data-factory-azure-sql-connector.md#azure-sql-dataset-type-properties).
4.	Un [conjunto de datos](data-factory-create-datasets.md) de salida de tipo [AzureBlob](#azure-blob-dataset-type-properties).
4.	Una [canalización](data-factory-create-pipelines.md) con la actividad de copia que usa [SqlSource](data-factory-azure-sql-connector.md#azure-sql-copy-activity-type-properties) y [BlobSink](#azure-blob-copy-activity-type-properties).


El ejemplo copia los datos que pertenecen a una serie temporal desde una tabla de una base de datos SQL de Azure a un blob cada hora. Las propiedades JSON usadas en estos ejemplos se describen en las secciones que aparecen después de los ejemplos.

**Servicio vinculado SQL de Azure:**

	{
	  "name": "AzureSqlLinkedService",
	  "properties": {
	    "type": "AzureSqlDatabase",
	    "typeProperties": {
	      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
	    }
	  }
	}

**Servicio vinculado de Almacenamiento de Azure:**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

Factoría de datos de Azure admite dos tipos de servicios vinculados de Almacenamiento de Azure: **AzureStorage** y **AzureStorageSas**. En el primer caso, especifique la cadena de conexión que incluye la clave de cuenta. En el segundo, especifique el Uri de firma de acceso compartido (SAS). Vea la sección [Servicios vinculados](#linked-services) para más información.


**Conjunto de datos de entrada SQL de Azure:**

El ejemplo supone que ha creado una tabla "MyTable" en SQL de Azure y que contiene una columna denominada "timestampcolumn" para los datos de series temporales.

Si se establece "external": "true" y se especifica la directiva externalData, se informa al servicio Factoría de datos de Azure que la tabla es externa a la factoría de datos y que no se produce por ninguna actividad de la factoría de datos.

	{
	  "name": "AzureSqlInput",
	  "properties": {
	    "type": "AzureSqlTable",
	    "linkedServiceName": "AzureSqlLinkedService",
	    "typeProperties": {
	      "tableName": "MyTable"
	    },
	    "external": true,
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    },
	    "policy": {
	      "externalData": {
	        "retryInterval": "00:01:00",
	        "retryTimeout": "00:10:00",
	        "maximumRetry": 3
	      }
	    }
	  }
	}


**Conjunto de datos de salida de blob de Azure:**

Los datos se escriben en un nuevo blob cada hora (frecuencia: hora, intervalo: 1). La ruta de acceso de la carpeta para el blob se evalúa dinámicamente según la hora de inicio del segmento que se está procesando. La ruta de acceso de la carpeta usa las partes year, month, day y hours de la hora de inicio.

	
	{
	  "name": "AzureBlobOutput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}/",
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
	      ],
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": "\t",
	        "rowDelimiter": "\n"
	      }
	    },
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}

**Canalización con actividad de copia:**

La canalización contiene una actividad de copia que está configurada para usar los conjuntos de datos de entrada y de salida y está programada para ejecutarse cada hora. En la definición de la canalización JSON, el tipo **source** se establece en **SqlSource** y el tipo **sink**, en **BlobSink**. La consulta SQL especificada para la propiedad **SqlReaderQuery** selecciona los datos de la última hora que se van a copiar.


	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    	"start":"2014-06-01T18:00:00",
	    	"end":"2014-06-01T19:00:00",
	    	"description":"pipeline for copy activity",
	    	"activities":[  
	      		{
	        		"name": "AzureSQLtoBlob",
		    	    "description": "copy activity",
		    	    "type": "Copy",
		    	    "inputs": [
		    	      {
		    	        "name": "AzureSQLInput"
		    	      }
		    	    ],
		    	    "outputs": [
		    	      {
		    	        "name": "AzureBlobOutput"
		    	      }
		    	    ],
		    	    "typeProperties": {
		    	    	"source": {
		            		"type": "SqlSource",
			            	"SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
		          		},
		          		"sink": {
		            		"type": "BlobSink"
		          		}
		        	},
		       		"scheduler": {
		          		"frequency": "Hour",
		          		"interval": 1
		        	},
		        	"policy": {
		          		"concurrency": 1,
		          		"executionPriorityOrder": "OldestFirst",
		          		"retry": 0,
		          		"timeout": "01:00:00"
		        	}
		      	}
		     ]
		}
	}

## Servicios vinculados
Hay dos tipos de servicios vinculados que puede usar para vincular un almacenamiento de blobs de Azure a una Factoría de datos de Azure. Se trata del servicio vinculado **AzureStorage** y el servicio vinculado **AzureStorageSas**. El servicio vinculado de Almacenamiento de Azure proporciona a la factoría de datos acceso global al Almacenamiento de Azure. Mientras que el servicio vinculado de SAS (firma de acceso compartido) de Almacenamiento de Azure proporciona a la factoría de datos acceso restringido/controlado por tiempo al Almacenamiento de Azure. No existen otras diferencias entre estos dos servicios vinculados. Elija el servicio vinculado que se adapte a sus necesidades. En las siguientes secciones se ofrecen más detalles sobre estos dos servicios vinculados.

[AZURE.INCLUDE [data-factory-azure-storage-linked-services](../../includes/data-factory-azure-storage-linked-services.md)]

## Propiedades de tipo del conjunto de datos de Blob de Azure

Para obtener una lista completa de las secciones y propiedades JSON disponibles para definir conjuntos de datos, consulte el artículo [Creación de conjuntos de datos](data-factory-create-datasets.md). Las secciones como structure, availability y policy de un conjunto de datos JSON son similares en todos los tipos de conjunto de datos (SQL Azure, blob de Azure, tabla de Azure, etc.).

La sección **typeProperties** es diferente en cada tipo de conjunto de datos y proporciona información acerca de la ubicación, formato, etc. de los datos en el almacén de datos. La sección typeProperties del conjunto de datos de tipo **AzureBlob** tiene las propiedades siguientes.

| Propiedad | Descripción | Obligatorio |
| -------- | ----------- | -------- | 
| folderPath | Ruta de acceso para el contenedor y la carpeta en el almacenamiento de blobs. Ejemplo: myblobcontainer\\myblobfolder\\ | Sí |
| fileName | <p>Nombre del blob. fileName es opcional. </p><p>Si especifica fileName, la actividad (incluida la copia) funciona en el blob específico.</p><p>Cuando no se especifica fileName, la copia incluirá todos los blobs de folderPath para el conjunto de datos de entrada.</p><p>Cuando no se especifica fileName para un conjunto de datos de salida, el nombre del archivo generado tendrá el formato siguiente: Data.<Guid>.txt (por ejemplo: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt</p> | No |
| partitionedBy | partitionedBy es una propiedad opcional. Puede usarla para especificar un folderPath dinámico y un nombre de archivo para datos de series temporales. Por ejemplo, se puede parametrizar folderPath por cada hora de datos. Consulte la sección [Aprovechamiento de la propiedad partitionedBy](#Leveraging-partitionedBy-property) a continuación para obtener información detallada y ejemplos. | No
| formato | Se admiten dos tipos de formatos: **TextFormat** y **AvroFormat**. Deberá establecer la propiedad type en format en cualquiera de estos valores. Cuando el formato es TextFormat, puede especificar propiedades opcionales adicionales para format. Consulte la sección [Especificación de TextFormat](#specifying-textformat) a continuación para obtener más detalles. | No
| compresión | Especifique el tipo y el nivel de compresión de los datos. Los tipos admitidos son: GZip y Deflate y BZip2 y los niveles admitidos son: óptimo y más rápido. Vea la sección [Compatibilidad de compresión](#compression-support) para más detalles. | No |

### Uso de la propiedad partitionedBy
Tal como se mencionó anteriormente, puede especificar un folderPath dinámico y un nombre de archivo para los datos de series temporales con la sección **partitionedBy**, macros de la Factoría de datos y las variables del sistema: SliceStart y SliceEnd, que indican las horas de inicio y de finalización de un segmento de datos especificado.

Vea [Variables del sistema de la factoría de datos](data-factory-scheduling-and-execution.md#data-factory-system-variables) y [Referencia de las funciones de la factoría de datos](data-factory-scheduling-and-execution.md#data-factory-functions-reference) para obtener información sobre las funciones y las variables del sistema de la factoría de datos que puede usar en la sección partitionedBy.

Consulte los artículos [Creación de conjuntos de datos](data-factory-create-datasets.md) y [Programación y ejecución](data-factory-scheduling-and-execution.md) para conocer más detalles sobre los conjuntos de datos de series temporales, la programación y los segmentos.

#### Ejemplo 1

	"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
	"partitionedBy": 
	[
	    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
	],

En el anterior ejemplo {Slice} se reemplaza por el valor de la variable del sistema SliceStart de la Factoría de datos en el formato (YYYYMMDDHH) especificado. SliceStart hace referencia a la hora de inicio del segmento. folderPath es diferente para cada segmento. Por ejemplo: wikidatagateway/wikisampledataout/2014100103 o wikidatagateway/wikisampledataout/2014100104

#### Ejemplo 2

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


## Propiedades de tipo de actividad de copia de Blob de Azure  
Para obtener una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo [Creación de canalizaciones](data-factory-create-pipelines.md). Propiedades como nombre, descripción, tablas de entrada y salida, varias directivas, etc. están disponibles para todos los tipos de actividades.

Por otro lado, las propiedades disponibles en la sección typeProperties de la actividad varían con cada tipo de actividad y, en caso de la actividad de copia, varían en función de los tipos de orígenes y receptores.

**BlobSource** admite las siguientes propiedades en la sección **typeProperties**:

| Propiedad | Descripción | Valores permitidos | Obligatorio |
| -------- | ----------- | -------------- | -------- | 
| treatEmptyAsNull | Especifica si se debe tratar una cadena nula o vacía como un valor nulo. | TRUE<br/>FALSE | No |
| skipHeaderLineCount | Indica cuántas líneas deben omitirse. Es aplicable únicamente cuando el conjunto de datos de entrada usa **TextFormat**. | Entero de 0 a Máx. | No | 
| recursive | Indica si los datos se leen de forma recursiva de las subcarpetas o solo de la carpeta especificada. | True (valor predeterminado), False | No | 


**BlobSink** admite las siguientes propiedades en la sección **typeProperties**:

| Propiedad | Descripción | Valores permitidos | Obligatorio |
| -------- | ----------- | -------------- | -------- |
| blobWriterAddHeader | Especifica si se debe agregar el encabezado de definiciones de columna. | TRUE<br/>FALSE (valor predeterminado) | No |
| copyBehavior | Define el comportamiento de copia cuando el origen es BlobSource o FileSystem. | <p>Hay tres valores posibles para la propiedad copyBehavior. </p><ul><li>**PreserveHierarchy:** conserva la jerarquía de archivos en la carpeta de destino, es decir, la ruta de acceso relativa del archivo de origen a la carpeta de origen es idéntica a la ruta de acceso relativa del archivo de destino a la carpeta de destino.</li><li>**FlattenHierarchy:** todos los archivos de la carpeta de origen estarán en el primer nivel de la carpeta de destino. Los archivos de destino tendrán un nombre generado automáticamente. </li><li>**MergeFiles: (valor predetermina)** combina todos los archivos de la carpeta de origen a un archivo. Si se especifica el nombre de archivo/blob, el nombre de archivo combinado sería el nombre especificado; de lo contrario, sería el nombre de archivo generado automáticamente.</li></ul> | No |

### Ejemplos de recursive y copyBehavior
En esta sección se describe el comportamiento resultante de la operación de copia para diferentes combinaciones de valores recursive y copyBehavior.

recursive | copyBehavior | Comportamiento resultante
--------- | ------------ | --------
true | preserveHierarchy | <p>Para una carpeta de origen Folder1 con la siguiente estructura:</p> <p>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5</p>la carpeta de destino Folder1 tendrá la misma estructura que el origen<p>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5</p>.  
true | flattenHierarchy | <p>Para una carpeta de origen Folder1 con la siguiente estructura:</p> <p>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5</p>la carpeta Folder1 de destino tendrá la siguiente estructura: <p>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nombre generado automáticamente para File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nombre generado automáticamente para File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;nombre generado automáticamente para File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;nombre generado automáticamente para File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;nombre generado automáticamente para File5</p>
true | mergeFiles | <p>Para una carpeta de origen Folder1 con la siguiente estructura:</p> <p>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5</p>la carpeta Folder1 de destino tendrá la siguiente estructura: <p>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;el contenido de File1 + File2 + File3 + File4 + File 5 se combinará en un archivo con nombre de archivo generado automáticamente</p>
false | preserveHierarchy | <p>Para una carpeta de origen Folder1 con la siguiente estructura:</p> <p>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5</p>la carpeta de destino Folder1 tendrá la siguiente estructura<p>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/></p><p>Subfolder1 con File3, File4 y File5 no se recogen.</p>.
false | flattenHierarchy | <p>Para una carpeta de origen Folder1 con la siguiente estructura:</p> <p>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5</p>la carpeta de destino Folder1 tendrá la siguiente estructura<p>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;auto-generated name for File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nombre generado automáticamente para File2<br/></p><p>Subfolder1 con File3, File4 y File5 no se recogen.</p>.
false | mergeFiles | <p>Para una carpeta de origen Folder1 con la siguiente estructura:</p> <p>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5</p>la carpeta de destino Folder1 tendrá la siguiente estructura<p>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;el contenido de File1 + File2 se combinará en un archivo con nombre de archivo generado automáticamente. El nombre de archivo generado automáticamente para File1</p><p>Subfolder1 con File3, File4, and File5 no se recogen.</p>.

  


[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

[AZURE.INCLUDE [data-factory-type-conversion-sample](../../includes/data-factory-type-conversion-sample.md)]

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

<!---HONumber=AcomDC_0128_2016-->