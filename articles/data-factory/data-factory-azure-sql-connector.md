<properties 
	pageTitle="Movimiento de datos hacia y desde SQL Azure | Factoría de datos de Azure" 
	description="Obtenga información acerca de cómo mover los datos hacia y desde la base de datos SQL de Azure mediante Factoría de datos de Azure." 
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
	ms.date="11/03/2015" 
	ms.author="spelluru"/>

# Movimiento de datos hacia y desde SQL Azure mediante Factoría de datos de Azure

En este artículo se describe cómo puede usar la actividad de copia en la Factoría de datos de Azure para mover datos a SQL de Azure desde otro almacén de datos y viceversa. Este artículo se basa en el artículo sobre [actividades de movimiento de datos](data-factory-data-movement-activities.md) que presenta una introducción general del movimiento de datos con la actividad de copia y las combinaciones del almacén de datos admitidas.

## Ejemplo: copia de datos SQL de Azure a un blob de Azure

El ejemplo siguiente muestra:

1. Un servicio vinculado de tipo [AzureSqlDatabase](#azure-sql-linked-service-properties).
2. Un servicio vinculado de tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties). 
3. Un [conjunto de datos](data-factory-create-datasets.md) de entrada de tipo [AzureSqlTable](#azure-sql-dataset-type-properties). 
4. Un [conjunto de datos](data-factory-create-datasets.md) de salida de tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4. Una [canalización](data-factory-create-pipelines.md) con la actividad de copia que usa [SqlSource](#azure-sql-copy-activity-type-properties) y [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

El ejemplo copia los datos que pertenecen a una serie temporal desde una tabla de una base de datos SQL de Azure a un blob cada hora. Las propiedades JSON usadas en estos ejemplos se describen en las secciones que aparecen después de los ejemplos.

**Servicio vinculado SQL de Azure**

	{
	  "name": "AzureSqlLinkedService",
	  "properties": {
	    "type": "AzureSqlDatabase",
	    "typeProperties": {
	      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
	    }
	  }
	}

Consulte la sección [Servicio vinculado SQL de Azure](#azure-sql-linked-service-properties) para obtener la lista de propiedades admitidas por este servicio vinculado.

**Servicio vinculado de almacenamiento de blobs de Azure**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

Consulte la sección [Blob de Azure](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) para obtener la lista de propiedades admitidas por este servicio vinculado.

**Conjunto de datos de entrada SQL de Azure**

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

Consulte la sección [Propiedades de tipo de conjunto de datos SQL de Azure](#azure-sql-dataset-type-properties) para obtener la lista de propiedades admitidas por este tipo de conjunto de datos.

**Conjunto de datos de salida de blob de Azure**

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

Consulte la sección [Propiedades de tipo de conjunto de datos Blob de Azure](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) para obtener la lista de propiedades admitidas por este tipo de conjunto de datos.

**Canalización con actividad de copia**

La canalización contiene una actividad de copia que está configurada para usar los conjuntos de datos de entrada y de salida y está programada para ejecutarse cada hora. En la definición de JSON de canalización, el tipo **source** se establece en **SqlSource** y el tipo **sink**, en **BlobSink**. La consulta SQL especificada para la propiedad **SqlReaderQuery** selecciona los datos de la última hora que se van a copiar.

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

> [AZURE.NOTE]En el ejemplo anterior, **sqlReaderQuery** se especifica para SqlSource. La actividad de copia ejecuta esta consulta en el origen de Base de datos SQL de Azure para obtener los datos.
>  
> Como alternativa, puede especificar un procedimiento almacenado mediante la especificación de **sqlReaderStoredProcedureName** y **storedProcedureParameters** (si el procedimiento almacenado toma parámetros).
>  
> Si no especifica sqlReaderQuery ni sqlReaderStoredProcedureName, las columnas definidas en la sección sobre la estructura del conjunto de datos JSON se usan para crear una consulta (seleccione column1, column2 en mytable) y ejecutarla en la base de datos SQL de Azure. Si la definición de conjunto de datos no tiene la estructura, se seleccionan todas las columnas de la tabla.


Consulte la sección [SqlSource](#sqlsource) y [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) para obtener la lista de propiedades admitidas por SqlSource y BlobSink.


## Ejemplo: copia de datos de un blob de Azure a SQL de Azure

El ejemplo siguiente muestra:

1.	Un servicio vinculado de tipo [AzureSqlDatabase](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties).
2.	Un servicio vinculado de tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3.	Un [conjunto de datos](data-factory-create-datasets.md) de entrada de tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.	Un [conjunto de datos](data-factory-create-datasets.md) de salida de tipo [AzureSqlTable](data-factory-azure-sql-connector.md#azure-sql-dataset-type-properties).
4.	Una [canalización](data-factory-create-pipelines.md) con la actividad de copia que usa [BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) y [SqlSink](data-factory-azure-sql-connector.md#azure-sql-copy-activity-type-properties).

El ejemplo copia los datos que pertenecen a una serie temporal desde un blob de Azure a una tabla de una base de datos SQL de Azure cada hora. Las propiedades JSON usadas en estos ejemplos se describen en las secciones que aparecen después de los ejemplos.


**Servicio vinculado SQL de Azure**
	
	{
	  "name": "AzureSqlLinkedService",
	  "properties": {
	    "type": "AzureSqlDatabase",
	    "typeProperties": {
	      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
	    }
	  }
	}

Consulte la sección [Servicio vinculado SQL de Azure](#azure-sql-linked-service-properties) para obtener la lista de propiedades admitidas por este servicio vinculado.

**Servicio vinculado de almacenamiento de blobs de Azure**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

Consulte la sección [Blob de Azure](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) para obtener la lista de propiedades admitidas por este servicio vinculado.

**Conjunto de datos de entrada de blob de Azure**

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

Consulte la sección [Propiedades de tipo de conjunto de datos Blob de Azure](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) para obtener la lista de propiedades admitidas por este tipo de conjunto de datos.

**Conjunto de datos de salida SQL de Azure**

El ejemplo copia los datos a una tabla denominada "MyTable" en SQL de Azure. Debe crear la tabla en SQL de Azure con el mismo número de columnas que espera que contenga el archivo CSV de blob. Se agregan nuevas filas a la tabla cada hora.

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

Consulte la sección [Propiedades de tipo de conjunto de datos SQL de Azure](#azure-sql-dataset-type-properties) para obtener la lista de propiedades admitidas por este tipo de conjunto de datos.

**Canalización con actividad de copia**

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

Consulte la sección [Sql Sink](#sqlsink) y [BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) para obtener la lista de propiedades admitidas por SqlSink y BlobSource.


## Propiedades del servicio vinculado SQL de Azure

En la tabla siguiente se proporciona la descripción de los elementos JSON específicos del servicio vinculado SQL de Azure.

| Propiedad | Descripción | Obligatorio |
| -------- | ----------- | -------- |
| type | La propiedad type debe establecerse en: AzureSqlDatabase | Sí |
| connectionString | Especifique la información necesaria para conectarse a la instancia de Base de datos SQL de Azure para la propiedad connectionString. | Sí |

**Nota:** tendrá que configurar [Firewall de Base de datos SQL de Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). Tendrá que configurar el servidor de base de datos para [permitir que los servicios de Azure tengan acceso al servidor](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). Además, si va a copiar datos a SQL de Azure desde fuera de Azure, incluidos orígenes de datos locales con puerta de enlace de la factoría de datos, debe configurar el intervalo de direcciones IP adecuado para el equipo que envía datos a SQL de Azure.

## Propiedades de tipo de conjunto de datos SQL de Azure

Para obtener una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, consulte el artículo [Creación de conjuntos de datos](data-factory-create-datasets.md). Las secciones como structure, availability y policy de un conjunto de datos JSON son similares en todos los tipos de conjunto de datos (SQL Azure, blob de Azure, tabla de Azure, etc.).

La sección typeProperties es diferente en cada tipo de conjunto de datos y proporciona información acerca de la ubicación de los datos en el almacén de datos. La sección **typeProperties** del conjunto de datos de tipo **AzureSqlTable** tiene las propiedades siguientes.

| Propiedad | Descripción | Obligatorio |
| -------- | ----------- | -------- |
| tableName | Nombre de la tabla en la instancia de Base de datos SQL de Azure a la que hace referencia el servicio vinculado. | Sí |

## Propiedades de tipo de actividad de copia SQL de Azure

Para obtener una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo [Creación de canalizaciones](data-factory-create-pipelines.md). Propiedades como nombre, descripción, tablas de entrada y salida, varias directivas, etc. están disponibles para todos los tipos de actividades.

> [AZURE.NOTE]La actividad de copia toma solo una entrada y genera una única salida.

Por otro lado, las propiedades disponibles en la sección typeProperties de la actividad varían con cada tipo de actividad y, en caso de la actividad de copia, varían en función de los tipos de orígenes y receptores.

### SqlSource

En caso de la actividad de copia si el origen es de tipo **SqlSource**, están disponibles las propiedades siguientes en la sección **typeProperties**:

| Propiedad | Descripción | Valores permitidos | Obligatorio |
| -------- | ----------- | -------------- | -------- |
| sqlReaderQuery | Utilice la consulta personalizada para leer los datos. | Cadena de consulta SQL. Por ejemplo: select * from MyTable. Si no se especifica, la instrucción SQL que se ejecuta: select from MyTable. | No |
| sqlReaderStoredProcedureName | Nombre del procedimiento almacenado que lee datos de la tabla de origen. | Nombre del procedimiento almacenado. | No |
| storedProcedureParameters | Parámetros del procedimiento almacenado. | Pares nombre-valor. Los nombres y las mayúsculas y minúsculas de los parámetros deben coincidir con las mismas características de los parámetros de procedimiento almacenado. | No |

Si se especifica **sqlReaderQuery** para SqlSource, la actividad de copia ejecuta la consulta en el origen de Base de datos SQL de Azure para obtener los datos.

Como alternativa, puede especificar un procedimiento almacenado mediante la especificación de **sqlReaderStoredProcedureName** y **storedProcedureParameters** (si el procedimiento almacenado toma parámetros).

Si no especifica sqlReaderQuery ni sqlReaderStoredProcedureName, las columnas definidas en la sección sobre la estructura del conjunto de datos JSON se usan para crear una consulta (seleccione column1, column2 en mytable) y ejecutarla en la base de datos SQL de Azure. Si la definición de conjunto de datos no tiene la estructura, se seleccionan todas las columnas de la tabla.

### Ejemplo de SqlSource

    "source": {
        "type": "SqlSource",
        "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
        "storedProcedureParameters": {
            "stringData": { "value": "str3" },
            "id": { "value": "$$Text.Format('{0:yyyy}', SliceStart)", "type": "Int"}
        }
    }

**Definición del procedimiento almacenado:**

	CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
	(
		@stringData varchar(20),
		@id int
	)
	AS
	SET NOCOUNT ON;
	BEGIN
	     select *
	     from dbo.UnitTestSrcTable
	     where dbo.UnitTestSrcTable.stringData != stringData
	    and dbo.UnitTestSrcTable.id != id
	END
	GO


### SqlSink 

**SqlSink** admite las siguientes propiedades:

| Propiedad | Descripción | Valores permitidos | Obligatorio |
| -------- | ----------- | -------------- | -------- |
| writeBatchTimeout | Tiempo de espera para que la operación de inserción por lotes se complete antes de que se agote el tiempo de espera. | (Unidad = intervalo de tiempo) Ejemplo: "00:30:00" (30 minutos). | No | 
| writeBatchSize | Inserta datos en la tabla SQL cuando el tamaño del búfer alcanza el valor writeBatchSize. | Entero. (unidad = recuento de filas) | No (Predeterminado = 10000)
| sqlWriterCleanupScript | Consulta especificada por el usuario para que la actividad de copia se ejecute de tal forma que se limpien los datos de un segmento específico. Consulte la sección sobre repetibilidad a continuación para obtener más detalles. | Una instrucción de consulta. | No |
| sliceIdentifierColumnName | Nombre de columna especificado por el usuario para que la rellene la actividad de copia con un identificador de segmentos generado automáticamente, que se usará para limpiar los datos de un segmento específico cuando se vuelva a ejecutar. Consulte la sección sobre repetibilidad a continuación para obtener más detalles. | Nombre de columna de una columna con el tipo de datos binarios (32). | No |
| sqlWriterStoredProcedureName | Nombre del procedimiento almacenado que actualiza e inserta (operación de upsert) datos en la tabla de destino. | Nombre del procedimiento almacenado. | No |
| storedProcedureParameters | Parámetros del procedimiento almacenado. | Pares nombre-valor. Los nombres y las mayúsculas y minúsculas de los parámetros deben coincidir con las mismas características de los parámetros de procedimiento almacenado. | No | 
| sqlWriterTableType | Nombre del tipo de tabla especificado por el usuario que se usará en el procedimiento almacenado anterior. La actividad de copia dispone que los datos que se mueven estén disponibles en una tabla temporal con este tipo de tabla. El código de procedimiento almacenado puede combinar los datos copiados con datos existentes. | Un nombre de tipo de tabla. | No |

#### Ejemplo de SqlSink

    "sink": {
        "type": "SqlSink",
        "writeBatchSize": 1000000,
        "writeBatchTimeout": "00:05:00",
        "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
        "sqlWriterTableType": "CopyTestTableType",
        "storedProcedureParameters": {
            "id": { "value": "1", "type": "Int" },
            "stringData": { "value": "str1" },
            "decimalData": { "value": "1", "type": "Decimal" }
        }
    }


[AZURE.INCLUDE [data-factory-type-repeatability-for-sql-sources](../../includes/data-factory-type-repeatability-for-sql-sources.md)]

[AZURE.INCLUDE [data-factory-sql-invoke-stored-procedure](../../includes/data-factory-sql-invoke-stored-procedure.md)]
 

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### Asignación de tipos para SQL Server y SQL de Azure

Como se mencionó en el artículo sobre [actividades de movimiento de datos](data-factory-data-movement-activities.md), la actividad de copia realiza conversiones automáticas de tipos de tipos de origen a tipos de receptor con el siguiente enfoque de dos pasos:

1. Conversión de tipos de origen nativos al tipo .NET
2. Conversión de tipo .NET al tipo del receptor nativo

Al mover datos a y desde SQL de Azure, SQL Server y Sybase, se usarán las siguientes asignaciones del tipo SQL al tipo .NET, y viceversa.

La asignación es igual que la asignación de tipo de datos de SQL Server para ADO.NET.

| Tipo de motor de base de datos SQL Server | Tipo .NET Framework |
| ------------------------------- | ------------------- |
| bigint | Int64 |
| binary | Byte |
| bit | Booleano |
| char | String, Char |
| fecha | DateTime |
| Datetime | DateTime |
| datetime2 | DateTime |
| Datetimeoffset | DateTimeOffset |
| Decimal | Decimal |
| FILESTREAM attribute (varbinary(max)) | Byte |
| Float | Doble |
| imagen | Byte | 
| int | Int32 | 
| money | Decimal |
| nchar | String, Char |
| ntext | String, Char |
| numeric | Decimal |
| nvarchar | String, Char |
| real | Single |
| rowversion | Byte |
| smalldatetime | DateTime |
| smallint | Int16 |
| smallmoney | Decimal | 
| sql\_variant | Object * |
| text | String, Char |
| Twitter en tiempo | TimeSpan |
| timestamp | Byte |
| tinyint | Byte |
| uniqueidentifier | Guid |
| varbinary | Byte |
| varchar | String, Char |
| xml | Xml |


[AZURE.INCLUDE [data-factory-type-conversion-sample](../../includes/data-factory-type-conversion-sample.md)]

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]




	 

<!---HONumber=Nov15_HO3-->