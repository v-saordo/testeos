<properties 
	pageTitle="Movimiento de datos hacia y desde el sistema de archivos | Factoría de datos de Azure" 
	description="Aprenda a mover datos hacia y desde el sistema de archivos local con Factoría de datos de Azure" 
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
	ms.date="11/09/2015" 
	ms.author="spelluru"/>

# Movimiento de datos hacia y desde el sistema de archivos local con Factoría de datos de Azure

En este artículo se describe cómo se puede usar la actividad de copia de la Factoría de datos para mover datos al sistema de archivos local, y desde este. Este artículo se basa en el artículo sobre [actividades de movimiento de datos](data-factory-data-movement-activities.md) que presenta una introducción general del movimiento de datos con la actividad de copia y las combinaciones del almacén de datos admitidas.

La Factoría de datos admite la conexión al sistema de archivos local, y desde este, a través de Data Management Gateway. Consulte el artículo sobre cómo [mover datos entre ubicaciones locales y la nube](data-factory-move-data-between-onprem-and-cloud.md) para obtener información acerca de Data Management Gateway, así como instrucciones paso a paso sobre cómo configurar la puerta de enlace.

> [AZURE.NOTE] 
Además de Data Management Gateway, no es necesario instalar otros archivos binarios para la comunicación con el sistema de archivos local, y desde este.
> 
> Vea [Solución de problemas de puerta de enlace](data-factory-move-data-between-onprem-and-cloud.md#gateway-troubleshooting) para obtener sugerencias sobre solución de problemas de conexión o puerta de enlace.

## Recurso compartido de archivos de Linux 

Realice los dos pasos siguientes para usar un recurso compartido de archivos de Linux con el servicio vinculado del servidor de archivos:

- Instale [Samba](https://www.samba.org/) en el servidor Linux.
- Instale y configure Data Management Gateway en un servidor de Windows. No se admite la instalación de una puerta de enlace en un servidor Linux. 
 
## Ejemplo: copiar datos de un sistema de archivos local a un blob de Azure

El ejemplo siguiente muestra:

1.	Un servicio vinculado de tipo [OnPremisesFileServer](data-factory-onprem-file-system-connector.md#onpremisesfileserver-linked-service-properties).
2.	Un servicio vinculado de tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)
3.	Un conjunto de [datos de entrada](data-factory-create-datasets.md) de tipo [FileShare](data-factory-onprem-file-system-connector.md#on-premises-file-system-dataset-type-properties).
4.	Un [conjunto de datos](data-factory-create-datasets.md) de salida de tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.	Una [canalización](data-factory-create-pipelines.md) con la actividad de copia que usa [FileSystemSource](data-factory-onprem-file-system-connector.md#file-share-copy-activity-type-properties) y [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties). 

En el siguiente ejemplo se copian los datos que pertenecen a una serie temporal del sistema de archivos local a un blob de Azure cada hora. Las propiedades JSON usadas en estos ejemplos se describen en las secciones que aparecen después de los ejemplos.

Como primer paso, configure la puerta de enlace de administración de datos según las instrucciones del artículo sobre cómo [mover datos entre las ubicaciones locales y la nube](data-factory-move-data-between-onprem-and-cloud.md).

**Servicio vinculado del servidor de archivos local:**

	{
	  "Name": "OnPremisesFileServerLinkedService",
	  "properties": {
	    "type": "OnPremisesFileServer",
	    "typeProperties": {
	      "host": "\\\Contosogame-Asia.<region>.corp.<company>.com",
	      "userid": "Admin",
	      "password": "123456",
	      "gatewayName": "mygateway"
	    }
	  }
	}

Para host, puede especificar **Local** o **localhost** si el recurso compartido de archivos se encuentra en la propia máquina de la puerta de enlace. Además, se recomienda usar la propiedad **encryptedCredential** en lugar de usar las propiedades **userid** y **password**. Vea [Servicio vinculado del sistema de archivos](#onpremisesfileserver-linked-service-properties) para más información sobre este servicio vinculado.

**Servicio vinculado de almacenamiento de blobs de Azure:**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**Conjunto de datos de entrada del sistema de archivos local:**

Los datos se extraen de un archivo nuevo cada hora y la ruta de acceso y el nombre de archivo reflejan la fecha y hora específicas con granularidad de hora.

Si se establece "external": true y se especifica la directiva externalData, se informa al servicio Factoría de datos que la tabla es externa a la factoría de datos y que no se produce por ninguna actividad de la factoría de datos.

	{
	  "name": "OnpremisesFileSystemInput",
	  "properties": {
	    "type": " FileShare",
	    "linkedServiceName": " OnPremisesFileServerLinkedService ",
	    "typeProperties": {
	      "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
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
	      ]
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
	      "folderPath": "mycontainer/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
	            "format": "%HH"
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

**Actividad de copia:**

La canalización contiene una actividad de copia que está configurada para usar los conjuntos de datos de entrada y de salida y está programada para ejecutarse cada hora. En la definición de JSON de canalización, el tipo **source** se establece en **FileSystemSource** y el tipo **sink** se establece en **BlobSink**.
	
	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2015-06-01T18:00:00",
	    "end":"2015-06-01T19:00:00",
	    "description":"Pipeline for copy activity",
	    "activities":[  
	      {
	        "name": "OnpremisesFileSystemtoBlob",
	        "description": "copy activity",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": "OnpremisesFileSystemInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureBlobOutput"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "FileSystemSource"
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

##Ejemplo: copiar datos de SQL Azure a un sistema de archivos local 

El ejemplo siguiente muestra:

1.	Un servicio vinculado de tipo AzureSqlDatabase.
2.	Un servicio vinculado de tipo OnPremisesFileServer.
3.	Un conjunto de datos de entrada de tipo AzureSqlTable. 
3.	Un conjunto de datos de salida de tipo FileShare.
4.	Una canalización con la actividad de copia que usa SqlSource y FileSystemSink.

El ejemplo copia los datos que pertenecen a una serie temporal desde una tabla de una base de datos SQL Azure a un sistema de archivos local cada hora. Las propiedades JSON usadas en estos ejemplos se describen en las secciones que aparecen después de los ejemplos.

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

**Servicio vinculado del servidor de archivos local:**

	{
	  "Name": "OnPremisesFileServerLinkedService",
	  "properties": {
	    "type": "OnPremisesFileServer",
	    "typeProperties": {
	      "host": "\\\Contosogame-Asia.<region>.corp.<company>.com",
	      "userid": "Admin",
	      "password": "123456",
	      "gatewayName": "mygateway"
	    }
	  }
	}

Para host, puede especificar **Local** o **localhost** si el recurso compartido de archivos se encuentra en la propia máquina de la puerta de enlace. Además, se recomienda usar la propiedad **encryptedCredential** en lugar de usar las propiedades **userid** y **password**. Vea [Servicio vinculado del sistema de archivos](#onpremisesfileserver-linked-service-properties) para más información sobre este servicio vinculado.

**Conjunto de datos de entrada SQL de Azure:**

El ejemplo supone que ha creado una tabla "MyTable" en SQL de Azure y que contiene una columna denominada "timestampcolumn" para los datos de series temporales.

Si se establece "external": "true" y se especifica la directiva externalData, se informa al servicio Factoría de datos que la tabla es externa a la factoría de datos y que no la genera ninguna actividad de la factoría de datos.

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

**Conjunto de datos de salida del sistema de archivos local:**

Los datos se copian a un archivo nuevo cada hora con la ruta de acceso para el blob que refleja la fecha y hora específicas con granularidad de hora.

	{
	  "name": "OnpremisesFileSystemOutput",
	  "properties": {
	    "type": "FileShare",
	    "linkedServiceName": " OnPremisesFileServerLinkedService ",
	    "typeProperties": {
	      "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
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
	            "format": "%HH"
	          }
	        }
	      ]
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

**Canalización con actividad de copia:** la canalización contiene una actividad de copia que está configurada para usar los conjuntos de datos de entrada y de salida y está programada para ejecutarse cada hora. En la definición de JSON de canalización, el tipo **source** se establece en **SqlSource** y el tipo **sink** en **FileSystemSink**. La consulta SQL especificada para la propiedad **SqlReaderQuery** selecciona los datos de la última hora que se van a copiar.

	
	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2015-06-01T18:00:00",
	    "end":"2015-06-01T20:00:00",
	    "description":"pipeline for copy activity",
	    "activities":[  
	      {
	        "name": "AzureSQLtoOnPremisesFile",
	        "description": "copy activity",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": "AzureSQLInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "OnpremisesFileSystemOutput"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "SqlSource",
	            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd}\\'', WindowStart, WindowEnd)"
	          },
	          "sink": {
	            "type": "FileSystemSink"
	          }
	        },
	       "scheduler": {
	          "frequency": "Hour",
	          "interval": 1
	        },
	        "policy": {
	          "concurrency": 1,
	          "executionPriorityOrder": "OldestFirst",
	          "retry": 3,
	          "timeout": "01:00:00"
	        }
	      }
	     ]
	   }
	}

## Propiedades del servicio vinculado OnPremisesFileServer

Un sistema de archivos local se puede vincular a una Factoría de datos de Azure con el servicio vinculado del servidor de archivos local. En la tabla siguiente se proporciona la descripción de los elementos JSON específicos del servicio vinculado del servidor de archivos local.

Propiedad | Descripción | Obligatorio
-------- | ----------- | --------
type | La propiedad type se debe establecer en **OnPremisesFileServer**. | Sí 
host | Nombre de host del servidor. Use ' \\ ' como carácter de escape como en el ejemplo siguiente: si el recurso compartido es: \\servername, especifique \\\servername.<p>Si el sistema de archivos es local en el equipo de puerta de enlace, use Local o localhost. Si el sistema de archivos está en un servidor que no es el equipo de puerta de enlace, use \\\servername.</p> | Sí
userid | Especifique el identificador del usuario que tiene acceso al servidor. | No (si elige encryptedCredential)
contraseña | Especifique la contraseña del usuario (identificador de usuario). | No (si elige encryptedCredential) 
encryptedCredential | Especifique las credenciales cifradas que puede obtener con la ejecución del cmdlet New-AzureRmDataFactoryEncryptValue<p>** Nota: ** tiene que usar Azure PowerShell, versión 0.8.14 o posterior para usar cmdlets como New-AzureRmDataFactoryEncryptValue con el parámetro type establecido en OnPremisesFileSystemLinkedService</p>. | No (si opta por especificar el identificador de usuario y la contraseña en texto sin formato)
gatewayName | Nombre de la puerta de enlace que debe usar el servicio Factoría de datos para conectarse al servidor de archivos local. | Sí

Vea [Configuración de credenciales y seguridad](data-factory-move-data-between-onprem-and-cloud.md#setting-credentials-and-security) para más información acerca de cómo configurar las credenciales para un origen de datos de Sybase local.

**Ejemplo: uso de nombre de usuario y contraseña en texto sin formato**
	
	{
	  "Name": "OnPremisesFileServerLinkedService",
	  "properties": {
	    "type": "OnPremisesFileServer",
	    "typeProperties": {
	      "host": "\\\Contosogame-Asia",
	      "userid": "Admin",
	      "password": "123456",
	      "gatewayName": "mygateway"
	    }
	  }
	}
	
**Ejemplo: uso de encryptedcredential**

	{
	  "Name": " OnPremisesFileServerLinkedService ",
	  "properties": {
	    "type": "OnPremisesFileServer",
	    "typeProperties": {
	      "host": "localhost",
	      "encryptedCredential": "WFuIGlzIGRpc3Rpbmd1aXNoZWQsIG5vdCBvbmx5IGJ5xxxxxxxxxxxxxxxxx",
	      "gatewayName": "mygateway"
	    }
	  }
	}

## Propiedades de tipo de conjunto de datos del sistema de archivos local

Para una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, vea el artículo [Creación de conjuntos de datos](data-factory-create-datasets.md). Las secciones como structure, availability y policy de un conjunto de datos JSON son similares en todos los tipos de conjunto de datos (SQL Azure, blob de Azure, tabla de Azure, sistema de archivos local, etc.).

La sección typeProperties es diferente en cada tipo de conjunto de datos y proporciona información acerca de la ubicación, formato, etc. de los datos en el almacén de datos. La sección typeProperties del conjunto de datos de tipo **FileShare** tiene las propiedades siguientes.

Propiedad | Descripción | Obligatorio
-------- | ----------- | --------
folderPath | Ruta de acceso a la carpeta. Ejemplo: myfolder<p>Use el carácter de escape ' \\ ' para los caracteres especiales de la cadena. Por ejemplo: para folder\\subfolder, especifique folder\\subfolder y para d:\\samplefolder, especifique d:\\samplefolder.</p><p>Puede combinarlo con **partitionBy** para tener rutas de acceso a carpetas basadas en las fechas y horas de inicio y finalización de los segmentos.</p> | Sí
fileName | Especifique el nombre del archivo en **folderPath** si quiere que la tabla haga referencia a un archivo específico de la carpeta. Si no especifica ningún valor para esta propiedad, la tabla apunta a todos los archivos de la carpeta.<p>Si no se especifica fileName para un conjunto de datos de salida, el nombre del archivo tendría este formato:</p><p>Data.<Guid>. txt (por ejemplo: : Data.0a405f8a 93ff 4c6f b3be f69616f1df7a.txt</p> | No
partitionedBy | partitionedBy se puede usar para especificar un folderPath dinámico, un nombre de archivo para datos de series temporales. Por ejemplo, folderPath se parametriza por cada hora de datos. | No
Formato | Se admiten dos tipos de formatos: **TextFormat** y **AvroFormat**. Deberá establecer la propiedad type en format en cualquiera de estos valores. Cuando el formato de forAvroFormatmat es TextFormat, puede especificar propiedades opcionales adicionales para format. Consulte la sección sobre formato a continuación para obtener más detalles. **La propiedad de formato no se admite actualmente para los sistemas de archivos locales. Deberá habilitarse en breve como aparece aquí.** | No
fileFilter | Especifique el filtro que se va a usar para seleccionar un subconjunto de archivos de folderPath, en lugar de todos los archivos. <p>Los valores permitidos son: * (varios caracteres) y ? (un solo carácter).</p><p>Ejemplo 1: "fileFilter": "*. log"</p>Ejemplo 2: "fileFilter": 2014-1-?. txt"</p><p>**Nota**: fileFilter es aplicable a un conjunto de datos de FileShare de entrada</p> | No
| compresión | Especifique el tipo y el nivel de compresión de los datos. Los tipos admitidos son: GZip y Deflate y BZip2 y los niveles admitidos son: óptimo y más rápido. Vea la sección [Compatibilidad de compresión](#compression-support) para más detalles. | No |

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

Si se establece el formato en **TextFormat**, es posible especificar las siguientes propiedades **opcionales** en la sección **Format** de la sección **typeProperties**.

Propiedad | Descripción | Obligatorio
-------- | ----------- | --------
columnDelimiter | Los caracteres que se usan como separador de columna en un archivo. El valor predeterminado es coma (,). | No
rowDelimiter | Los caracteres que se usan como separador sin formato en un archivo. El valor predeterminado es cualquiera de los siguientes: ["\\r\\n", "\\r", "\\n"]. | No
escapeChar | El carácter especial que se usa para anular el delimitador de columna que se muestra en el contenido. No hay ningún valor predeterminado. No debe especificar más de un carácter para esta propiedad.<p>Por ejemplo, si tiene la coma (,) como delimitador de columna, pero desea usar el carácter de coma en el texto (por ejemplo: "Hello, world"), puede definir '$' como carácter de escape y usar la cadena "Hello$, world" en el origen.</p><p>Tenga en cuenta que no se pueden especificar escapeChar y quoteChar a la vez para una tabla.</p> | No
quoteChar | El carácter especial que se usa para poner entre comillas el valor de la cadena. Los delimitadores de columna y fila entre comillas se tratarán como parte del valor de la cadena. No hay ningún valor predeterminado. No debe especificar más de un carácter para esta propiedad.<p>Por ejemplo, si tiene la coma (,) como delimitador de columna, pero desea usar el carácter de coma en el texto (por ejemplo: <Hello  world>), puede definir ‘"’ como comillas y usar la cadena <"Hello, world"> en el origen. Esta propiedad es aplicable a las tablas de entrada y de salida.</p><p>Tenga en cuenta que no se pueden especificar escapeChar y quoteChar a la vez para una tabla.</p> | No
nullValue | Los caracteres que se usan para representar un valor nulo en el contenido del archivo de blob. El valor predeterminado es “\\N”.> | No
encodingName | Especifique el nombre de codificación. Para obtener la lista de nombres de codificación válidos, vea: Propiedad Encoding.EncodingName. <p>Por ejemplo: windows-1250 o shift\_jis. El valor predeterminado es: UTF-8.</p> | No

#### Ejemplos:

En el ejemplo siguiente se muestran algunas de las propiedades de formato de **TextFormat**.

	"typeProperties":
	{
	    "folderPath": "MyFolder",
	    "fileName": "MyFileName"
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

Si se establece el formato en **AvroFormat**, no es preciso especificar propiedades en la sección Format de la sección typeProperties. Ejemplo:

	"format":
	{
	    "type": "AvroFormat",
	}
	
Para usar el formato Avro en una tabla de Hive posterior, consulte [Tutorial de Apache Hive](https://cwiki.apache.org/confluence/display/Hive/AvroSerDe).

[AZURE.INCLUDE [data-factory-compression](../../includes/data-factory-compression.md)]

## Propiedades de tipo de actividad de copia de recurso compartido de archivos

**FileSystemSource** admite las siguientes propiedades:

| Propiedad | Descripción | Valores permitidos | Obligatorio |
| -------- | ----------- | -------------- | -------- |
| recursive | Indica si los datos se leen de forma recursiva de las subcarpetas o solo de la carpeta especificada. | True, False (predeterminada)| No | 

**FileSystemSink** admite las siguientes propiedades:

| Propiedad | Descripción | Valores permitidos | Obligatorio |
| -------- | ----------- | -------------- | -------- |
| copyBehavior | Define el comportamiento de copia cuando el origen es BlobSource o FileSystem. | <p>Hay tres valores posibles para la propiedad copyBehavior. </p><ul><li>**PreserveHierarchy:** conserva la jerarquía de archivos en la carpeta de destino, es decir, la ruta de acceso relativa del archivo de origen a la carpeta de origen es idéntica a la ruta de acceso relativa del archivo de destino a la carpeta de destino.</li><li>**FlattenHierarchy:** todos los archivos de la carpeta de origen estarán en el primer nivel de la carpeta de destino. Los archivos de destino tendrán un nombre generado automáticamente. </li></ul> | No |

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

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]








 

<!---HONumber=AcomDC_0128_2016-->