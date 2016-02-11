<properties 
	pageTitle="Movimiento de datos hacia y desde SQL Server | Factoría de datos de Azure" 
	description="Aprenda a mover los datos hacia y desde una base de datos SQL Server en un entorno local o en una máquina virtual de Azure mediante Factoría de datos de Azure." 
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

# Movimiento de los datos entre entornos locales de SQL Server o en IaaS (máquina virtual de Azure) mediante Factoría de datos de Azure

En este artículo se describe cómo puede usar la actividad de copia en Factoría de datos de Azure para mover datos a SQL Server desde otro almacén de datos y viceversa. Este artículo se basa en el artículo sobre [actividades de movimiento de datos](data-factory-data-movement-activities.md) que presenta una introducción general del movimiento de datos con la actividad de copia y las combinaciones del almacén de datos admitidas.

## Habilitación de la conectividad

Los conceptos y los pasos necesarios para conectar con SQL Server hospedado de forma local o en máquinas virtuales de IaaS de Azure (infraestructura como servicio) son los mismos. En ambos casos, deberá aprovechar Data Management Gateway para conectividad.

Consulte el artículo sobre cómo [mover datos entre ubicaciones locales y la nube](data-factory-move-data-between-onprem-and-cloud.md) para obtener información acerca de Data Management Gateway, así como instrucciones paso a paso sobre cómo configurar la puerta de enlace. La configuración de una instancia de puerta de enlace es un requisito previo para conectarse con SQL Server.

Aunque puede instalar la puerta de enlace en el mismo equipo local o una instancia de máquina virtual en la nube, como SQL Server, para mejorar el rendimiento, se recomienda instalarlos en equipos independientes o máquinas virtuales en la nube para evitar la contención de recursos.

## Ejemplo: copia de datos de SQL Azure a un blob de Azure

El ejemplo siguiente muestra:

1.	Un servicio vinculado de tipo [OnPremisesSqlServer](data-factory-sqlserver-connector.md#sql-server-linked-service-properties).
2.	Un servicio vinculado de tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3.	Un [conjunto de datos](data-factory-create-datasets.md) de entrada de tipo [SqlServerTable](data-factory-sqlserver-connector.md#sql-server-dataset-type-properties). 
4.	Un [conjunto de datos](data-factory-create-datasets.md) de salida de tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.	La [canalización](data-factory-create-pipelines.md) con la actividad de copia que usa [SqlSource](data-factory-sqlserver-connector.md#sql-server-copy-activity-type-properties) y [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

El ejemplo copia los datos que pertenecen a una serie temporal desde una tabla de una Base de datos SQL Server a un blob de Azure cada hora. Las propiedades JSON usadas en estos ejemplos se describen en las secciones que aparecen después de los ejemplos.

Como primer paso, configure la puerta de enlace de administración de datos según las instrucciones del artículo sobre cómo [mover datos entre las ubicaciones locales y la nube](data-factory-move-data-between-onprem-and-cloud.md).

**Servicio vinculado de SQL Server**

	{
	  "Name": "SqlServerLinkedService",
	  "properties": {
	    "type": "OnPremisesSqlServer",
	    "typeProperties": {
	      "connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
	      "gatewayName": "<gatewayname>"
	    }
	  }
	}

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

**Conjunto de datos de entrada de SQL Server**

El ejemplo supone que ha creado una tabla "MyTable" en SQL Server y que contiene una columna denominada "timestampcolumn" para los datos de serie temporal.

Si se establece "external": "true" y se especifica la directiva externalData, se informa al servicio Factoría de datos de Azure que la tabla es externa a la factoría de datos y que no se producen por ninguna actividad de la factoría de datos.

	{
	  "name": "SqlServerInput",
	  "properties": {
	    "type": "SqlServerTable",
	    "linkedServiceName": "SqlServerLinkedService",
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

**Conjunto de datos de salida de blob de Azure**

Los datos se escriben en un nuevo blob cada hora (frecuencia: hora, intervalo: 1). La ruta de acceso de la carpeta para el blob se evalúa dinámicamente según la hora de inicio del segmento que se está procesando. La ruta de acceso de la carpeta usa las partes year, month, day y hours de la hora de inicio.
	
	{
	  "name": "AzureBlobOutput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Canalización con actividad de copia**

La canalización contiene una actividad de copia que está configurada para usar los conjuntos de datos de entrada y de salida y está programada para ejecutarse cada hora. En la definición de la canalización JSON, el tipo **source** se establece en **SqlSource** y el tipo **sink**, en **BlobSink**. La consulta SQL especificada para la propiedad **SqlReaderQuery** selecciona los datos de la última hora que se van a copiar.


	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2014-06-01T18:00:00",
	    "end":"2014-06-01T19:00:00",
	    "description":"pipeline for copy activity",
	    "activities":[  
	      {
	        "name": "SqlServertoBlob",
	        "description": "copy activity",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": " SqlServerInput"
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

> [AZURE.NOTE] En el ejemplo anterior, **sqlReaderQuery** se especifica para SqlSource. La actividad de copia ejecuta esta consulta en el origen de Base de datos de SQL Server para obtener los datos.
>  
> Como alternativa, puede especificar un procedimiento almacenado mediante la especificación de **sqlReaderStoredProcedureName** y **storedProcedureParameters** (si el procedimiento almacenado toma parámetros).
>  
> Si no especifica sqlReaderQuery ni sqlReaderStoredProcedureName, las columnas definidas en la sección sobre la estructura del conjunto de datos JSON se usan para crear una consulta (seleccione column1, column2 en mytable) y ejecutarla en la base de datos SQL de Azure. Si la definición de conjunto de datos no tiene la estructura, se seleccionan todas las columnas de la tabla.


Consulte la sección [SqlSource](#sqlsource) y [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) para obtener la lista de propiedades admitidas por SqlSource y BlobSink.

## Ejemplo: copia de datos de un blob de Azure a SQL Server

El ejemplo siguiente muestra:

1.	El servicio vinculado de tipo [OnPremisesSqlServer](data-factory-sqlserver-connector.md#sql-server-linked-service-properties).
2.	El servicio vinculado de tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3.	Un [conjunto de datos](data-factory-create-datasets.md) de entrada de tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.	Un [conjunto de datos](data-factory-create-datasets.md) de salida de tipo [SqlServerTable](data-factory-sqlserver-connector.md#sql-server-dataset-type-properties).
4.	La [canalización](data-factory-create-pipelines.md) con la actividad de copia que usa [BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) y [SqlSink](data-factory-sqlserver-connector.md#sql-server-copy-activity-type-properties).

El ejemplo copia los datos que pertenecen a una serie temporal desde un blob de Azure a una tabla de una Base de datos SQL Server cada hora. Las propiedades JSON usadas en estos ejemplos se describen en las secciones que aparecen después de los ejemplos.

**Servicio vinculado de SQL Server**
	
	{
	  "Name": "SqlServerLinkedService",
	  "properties": {
	    "type": "OnPremisesSqlServer",
	    "typeProperties": {
	      "connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
	      "gatewayName": "<gatewayname>"
	    }
	  }
	}

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

**Conjunto de datos de entrada de blob de Azure**

Los datos se seleccionan de un nuevo blob cada hora (frecuencia: hora, intervalo: 1). La ruta de acceso de la carpeta y el nombre de archivo para el blob se evalúan dinámicamente según la hora de inicio del segmento que se está procesando. La ruta de acceso de la carpeta usa la parte year, month y day de la hora de inicio y el nombre de archivo, la parte hour. El valor "external": "true" informa al servicio Factoría de datos que esta tabla es externa a la factoría y no la produce una actividad de la factoría de datos.
	
	{
	  "name": "AzureBlobInput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
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
	
**Conjunto de datos de salida de SQL Server**

El ejemplo copia los datos a una tabla denominada "MyTable" en SQL Server. Debe crear la tabla en SQL Server con el mismo número de columnas que espera que contenga el archivo CSV de blob. Se agregan nuevas filas a la tabla cada hora.
	
	{
	  "name": "SqlServerOutput",
	  "properties": {
	    "type": "SqlServerTable",
	    "linkedServiceName": "SqlServerLinkedService",
	    "typeProperties": {
	      "tableName": "MyOutputTable"
	    },
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}

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
	            "name": " SqlServerOutput "
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

## Propiedades del servicio vinculado de SQL Server

En la tabla siguiente se proporciona la descripción de los elementos JSON específicos del servicio SQL Server vinculado.

| Propiedad | Descripción | Obligatorio |
| -------- | ----------- | -------- |
| type | La propiedad type se debe establecer en **OnPremisesSqlServer**. | Sí |
| connectionString | Especifique la información de connectionString necesaria para conectarse a la Base de datos SQL Server local mediante autenticación de SQL o autenticación de Windows. | Sí |
| gatewayName | Nombre de la puerta de enlace que debe usar el servicio Factoría de datos para conectarse a la Base de datos SQL Server local. | Sí |
| nombre de usuario | Especifique el nombre de usuario si usa la autenticación de Windows. | No |
| contraseña | Especifique la contraseña de la cuenta de usuario especificada para el nombre de usuario. | No |

Puede cifrar las credenciales con el cmdlet **New-AzureRmDataFactoryEncryptValue** y usarlas en la cadena de conexión, como se muestra en el ejemplo siguiente (propiedad **EncryptedCredential**):

	"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",

### Muestras

**JSON para usar autenticación de SQL**
	
	{
	    "name": "MyOnPremisesSQLDB",
	    "properties":
	    {
	        "type": "OnPremisesSqlLinkedService",
	        "connectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=False;User ID=<username>;Password=<password>;",
	        "gatewayName": "<gateway name>"
	    }
	}

**JSON para usar autenticación de Windows**

Si se especifican el nombre de usuario y la contraseña, la puerta de enlace los usará para suplantar la cuenta de usuario especificada para conectarse a la Base de datos SQL Server local. En caso contrario, la puerta de enlace se conectará directamente a SQL Server con el contexto de seguridad de puerta de enlace (su cuenta de inicio).

	{ 
	     "Name": " MyOnPremisesSQLDB", 
	     "Properties": 
	     { 
	         "type": "OnPremisesSqlLinkedService", 
	         "ConnectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=True;", 
	         "username": "<username>", 
	         "password": "<password>", 
	         "gatewayName": "<gateway name>" 
	     } 
	}

Consulte [Configuración de credenciales y seguridad](data-factory-move-data-between-onprem-and-cloud.md#setting-credentials-and-security) para obtener más información acerca de cómo configurar las credenciales para un origen de datos de SQL Server.

## Propiedades de tipo de conjunto de datos de SQL Server

Para obtener una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, consulte el artículo [Creación de conjuntos de datos](data-factory-create-datasets.md). Las secciones como structure, availability y policy de un conjunto de datos JSON son similares en todos los tipos de conjunto de datos (SQL Server, blob de Azure, tabla de Azure, etc.).

La sección typeProperties es diferente en cada tipo de conjunto de datos y proporciona información acerca de la ubicación de los datos en el almacén de datos. La sección **typeProperties** del conjunto de datos de tipo **SqlServerTable** tiene las propiedades siguientes.

| Propiedad | Descripción | Obligatorio |
| -------- | ----------- | -------- |
| tableName | Nombre de la tabla en la instancia de Base de datos de SQL Server a la que hace referencia el servicio vinculado. | Sí |

## Propiedades de tipo de actividad de copia de SQL Server

Para obtener una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo [Creación de canalizaciones](data-factory-create-pipelines.md). Propiedades como nombre, descripción, tablas de entrada y salida, varias directivas, etc. están disponibles para todos los tipos de actividades.

> [AZURE.NOTE] La actividad de copia toma solo una entrada y genera una única salida.

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

## Solución de problemas de conexión

1. Configure su SQL Server para que acepte conexiones remotas. Inicie **SQL Server Management Studio**, haga clic con el botón derecho en **servidor** y haga clic en **Propiedades**. Seleccione **Conexiones** en la lista y compruebe **Permitir conexiones remotas con este servidor**.
	
	![Habilitar conexiones remotas](.\media\data-factory-sqlserver-connector\AllowRemoteConnections.png)

	Vea [Establecer la opción de configuración del servidor Opciones de usuario](https://msdn.microsoft.com/library/ms191464.aspx) para pasos detallados. 
2. Inicie el **Administrador de configuración de SQL Server**. Expanda **Configuración de red de SQL Server** para la instancia que quiere y seleccione **Protocolos para MSSQLSERVER**. Debería ver protocolos en el panel derecho. Para habilitar TCP/TP, haga clic con el botón derecho en **TCP/IP** y haga clic en **Habilitar**.

	![Habilitar TCP/IP](.\media\data-factory-sqlserver-connector\EnableTCPProptocol.png)

	Vea [Habilitar o deshabilitar un protocolo de red de servidor](https://msdn.microsoft.com/library/ms191294.aspx) para detalles y maneras alternativas de habilitar el protocolo TCP/IP. 
3. En la misma ventana, haga doble clic en **TCP/IP** para iniciar la ventana **Propiedades de TCP/IP**.
4. Cambie a la pestaña **Direcciones IP**. Desplácese hacia abajo para ver la sección **IPAll**. Anote el **Puerto TCP** (el valor predeterminado es **1433**).
5. Cree una **regla del Firewall de Windows** en el equipo para permitir el tráfico entrante a través de este puerto.  
6. **Comprobar conexión**: use SQL Server Management Studio desde una máquina diferente para conectarse a SQL Server con el nombre completo. Por ejemplo: <machine>.<domain>.corp.<company>.com,1433.

	> [AZURE.IMPORTANT] 
	Vea [Ports and Security Considerations](data-factory-move-data-between-onprem-and-cloud.md#port-and-security-considerations) (Consideraciones de puertos y seguridad) para información detallada.
	>   
	> Vea [Solución de problemas de puerta de enlace](data-factory-move-data-between-onprem-and-cloud.md#gateway-troubleshooting) para obtener sugerencias sobre solución de problemas de conexión o puerta de enlace.

[AZURE.INCLUDE [data-factory-type-repeatability-for-sql-sources](../../includes/data-factory-type-repeatability-for-sql-sources.md)]


[AZURE.INCLUDE [data-factory-sql-invoke-stored-procedure](../../includes/data-factory-sql-invoke-stored-procedure.md)]


[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]


### Asignación de tipos para SQL Server y SQL de Azure

Como se mencionó en el artículo sobre [actividades de movimiento de datos](data-factory-data-movement-activities.md), la actividad de copia realiza conversiones automáticas de los tipos de origen a los tipos de receptor con el siguiente enfoque de dos pasos:

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

<!---HONumber=AcomDC_0128_2016-->