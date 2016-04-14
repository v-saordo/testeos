<properties 
	pageTitle="Movimiento de datos desde Oracle | Factoría de datos de Azure" 
	description="Obtenga información acerca de cómo mover los datos hacia y desde la base de datos de Oracle local mediante Factoría de datos de Azure." 
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
	ms.date="02/01/2016" 
	ms.author="spelluru"/>

# Movimiento de datos desde Oracle local mediante Factoría de datos de Azure 

En este artículo se describe cómo se puede usar la actividad de copia de la Factoría de datos para mover datos de Oracle a otro almacén de datos. Este artículo se basa en el artículo sobre [actividades de movimiento de datos](data-factory-data-movement-activities.md) que presenta una introducción general del movimiento de datos con la actividad de copia y las combinaciones del almacén de datos admitidas.

## Instalación 
Para que el servicio Factoría de datos de Azure pueda conectarse a la base de datos de Oracle local, debe instalar lo siguiente:

- Data Management Gateway en el mismo equipo que hospede la base de datos o en un equipo independiente para evitar la competencia por los recursos con la base de datos. Data Management Gateway es un software que conecta orígenes de datos locales a servicios en la nube de forma segura y administrada. Consulte el artículo [Mover datos entre orígenes locales y la nube](data-factory-move-data-between-onprem-and-cloud.md) para obtener más información acerca de Data Management Gateway. 
- [Oracle Data Access Components (ODAC) para Windows](http://www.oracle.com/technetwork/topics/dotnet/downloads/index.html). Debe estar instalado en el equipo host en el que está instalada la puerta de enlace.

> [AZURE.NOTE] Vea [Solución de problemas de puerta de enlace](data-factory-move-data-between-onprem-and-cloud.md#gateway-troubleshooting) para obtener sugerencias sobre solución de problemas de conexión o puerta de enlace.

## Ejemplo: copia de datos de Oracle a un blob de Azure
En este ejemplo, se muestra cómo copiar datos de una base de datos Oracle local a un Almacenamiento de blobs de Azure. Sin embargo, se pueden copiar datos **directamente** a cualquiera de los receptores indicados [aquí](data-factory-data-movement-activities.md#supported-data-stores) mediante la actividad de copia en Factoría de datos de Azure.
 
El ejemplo consta de las siguientes entidades de factoría de datos:

1.	Un servicio vinculado de tipo [OnPremisesOracle](data-factory-onprem-oracle-connector.md#oracle-linked-service-properties).
2.	Un servicio vinculado de tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3.	Un [conjunto de datos](data-factory-create-datasets.md) de entrada de tipo [OracleTable](data-factory-onprem-oracle-connector.md#oracle-dataset-type-properties). 
4.	Un [conjunto de datos](data-factory-create-datasets.md) de salida de tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
5.	Una [canalización](data-factory-create-pipelines.md) con la actividad de copia que usa [OracleSource](data-factory-onprem-oracle-connector.md#oracle-copy-activity-type-properties) como origen y [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) como receptor.

El ejemplo copia datos de una tabla de una base de datos de Oracle local a un blob cada hora. Para obtener más información sobre las distintas propiedades que se usan en el ejemplo siguiente, consulte la documentación de las diferentes propiedades en las secciones de las secciones que aparecen después de los ejemplos.

**Servicio vinculado de Oracle:**

	{
	  "name": "OnPremisesOracleLinkedService",
	  "properties": {
	    "type": "OnPremisesOracle",
	    "typeProperties": {
	      "ConnectionString": "data source=<data source>;User Id=<User Id>;Password=<Password>;",
	      "gatewayName": "<gateway name>"
	    }
	  }
	}

**Servicio vinculado de almacenamiento de blobs de Azure:**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<Account key>"
	    }
	  }
	}

**Conjunto de datos de entrada de Oracle:**

El ejemplo supone que ha creado una tabla "MyTable" en Oracle que contiene una columna denominada "timestampcolumn" para los datos de serie temporales.

Si se establece "external": "true" y se especifica la directiva externalData, se indica a la factoría de datos que la tabla es externa a la factoría de datos y que no se produce por ninguna actividad de la factoría de datos.

	{
	    "name": "OracleInput",
	    "properties": {
	        "type": "OracleTable",
	        "linkedServiceName": "OnPremisesOracleLinkedService",
	        "typeProperties": {
	            "tableName": "MyTable"
	        },
	           "external": true,
	        "availability": {
	            "offset": "01:00:00",
	            "interval": "1",
	            "anchorDateTime": "2014-02-27T12:00:00",
	            "frequency": "Hour"
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

Los datos se escriben en un nuevo blob cada hora (frecuencia: hora, intervalo: 1). La ruta de acceso de la carpeta y el nombre de archivo para el blob se evalúan dinámicamente según la hora de inicio del segmento que se está procesando. La ruta de acceso de la carpeta usa las partes year, month, day y hours de la hora de inicio.
	
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


**Canalización con actividad de copia:**

La canalización contiene una actividad de copia que está configurada para usar los conjuntos de datos de entrada y de salida y está programada para ejecutarse cada hora. En la definición de la canalización JSON, el tipo **source** se establece en **RelationalSource** y el tipo **sink** se establece en **BlobSink**. La consulta SQL especificada para la propiedad **oracleReaderQuery** selecciona los datos de la última hora que se van a copiar.

	
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
	            "name": " OracleInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureBlobOutput"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "OracleSource",
	            "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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


Debe ajustar la cadena de consulta, según cómo se configuran las fechas en la base de datos de Oracle. Si aparece el siguiente mensaje de error:

	Message=Operation failed in Oracle Database with the following error: 'ORA-01861: literal does not match format string'.,Source=,''Type=Oracle.DataAccess.Client.OracleException,Message=ORA-01861: literal does not match format string,Source=Oracle Data Provider for .NET,'.

puede ser necesario cambiar la consulta, como se muestra a continuación (con la función to\_date):

	"oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= to_date(\\'{0:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\')  AND timestampcolumn < to_date(\\'{1:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\') ', WindowStart, WindowEnd)"


## Propiedades del servicio vinculado de Oracle

En la tabla siguiente se proporciona la descripción de los elementos JSON específicos al servicio vinculado de Oracle.

Propiedad | Descripción | Obligatorio
-------- | ----------- | --------
type | La propiedad type debe establecerse en: **OnPremisesOracle** | Sí
connectionString | Especifique la información necesaria para conectarse a la instancia de Base de datos de Oracle para la propiedad connectionString. | Sí 
gatewayName | Nombre de la puerta de enlace que se usará para conectarse al servidor de Oracle local | Sí

Consulte [Configuración de credenciales y seguridad](data-factory-move-data-between-onprem-and-cloud.md#setting-credentials-and-security) para obtener más información acerca de cómo configurar las credenciales para un origen de datos de Oracle local.
## Propiedades del tipo de conjunto de datos de Oracle

Para obtener una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, consulte el artículo [Creación de conjuntos de datos](data-factory-create-datasets.md). Las secciones como structure, availability y policy de un conjunto de datos JSON son similares en todos los tipos de conjunto de datos (Oracle, blob de Azure, tabla de Azure, etc.).
 
La sección typeProperties es diferente en cada tipo de conjunto de datos y proporciona información acerca de la ubicación de los datos en el almacén de datos. La sección typeProperties del conjunto de datos de tipo OracleTable tiene las propiedades siguientes.

Propiedad | Descripción | Obligatorio
-------- | ----------- | --------
tableName | Nombre de la tabla en la instancia de Base de datos de Oracle a la que hace referencia el servicio vinculado. | No (si se especifica **oracleReaderQuery** de **SqlSource**)

## Propiedades de tipo de actividad de copia de Oracle

Para obtener una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo [Creación de canalizaciones](data-factory-create-pipelines.md). Propiedades como nombre, descripción, tablas de entrada y salida, varias directivas, etc. están disponibles para todos los tipos de actividades.

**Nota:** la actividad de copia toma solo una entrada y genera una única salida.

Por otro lado, las propiedades disponibles en la sección typeProperties de la actividad varían con cada tipo de actividad y, en caso de la actividad de copia, varían en función de los tipos de orígenes y receptores.

En caso de la actividad de copia si el origen es de tipo **OracleSource**, están disponibles las propiedades siguientes en la sección **typeProperties**:

Propiedad | Descripción |Valores permitidos | Obligatorio
-------- | ----------- | ------------- | --------
oracleReaderQuery | Utilice la consulta personalizada para leer los datos. | Cadena de consulta SQL. 
Por ejemplo: select * from MyTable <p>Si no se especifica, la instrucción SQL que se ejecuta: select * from MyTable</p> | No (si se especifica **tableName** de **dataset**)

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### Asignación de tipos para Oracle

Como se mencionó en el artículo sobre [actividades de movimiento de datos](data-factory-data-movement-activities.md), la actividad de copia realiza conversiones automáticas de los tipos de origen a los tipos de receptor con el siguiente enfoque de dos pasos:

1. Conversión de tipos de origen nativos al tipo .NET
2. Conversión de tipo .NET al tipo del receptor nativo

Al mover datos de Oracle, se usarán las siguientes asignaciones del tipo de datos de Oracle al tipo de .NET, y viceversa.

Tipo de datos de Oracle | Tipo de datos de .NET Framework
---------------- | ------------------------
BFILE | Byte
BLOB | Byte
CHAR | String
CLOB | String
DATE | DateTime
FLOAT | Decimal
INTEGER | Decimal
INTERVAL YEAR TO MONTH | Int32
INTERVAL DAY TO SECOND | TimeSpan
LONG | String
LONG RAW | Byte
NCHAR | String
NCLOB | String
NUMBER | Decimal
NVARCHAR2 | String
RAW | Byte
ROWID | String
TIMESTAMP | DateTime
TIMESTAMP WITH LOCAL TIME ZONE | DateTime
TIMESTAMP WITH TIME ZONE | DateTime
UNSIGNED INTEGER | Number
VARCHAR2 | String
XML | String

## Sugerencias de solución de problemas

****Problema: ** se visualiza el siguiente **mensaje de error**: La actividad de copia detectó parámetros no válidos: "UnknownParameterName". Mensaje detallado: No se encuentra el proveedor de datos .NET Framework solicitado. Puede que no esté instalado".

**Causas posibles**

1. No se instaló el proveedor de datos de .NET Framework para Oracle.
2. El proveedor de datos de .NET Framework para Oracle se instaló en .NET Framework 2.0 y no se encuentra en las carpetas de .NET Framework 4.0. 

**Resolución o solución alternativa**

1. Si no ha instalado el proveedor de .NET para Oracle, [instálelo](http://www.oracle.com/technetwork/topics/dotnet/utilsoft-086879.html) e intente de nuevo este escenario. 
2. Si recibe el mensaje de error incluso después de instalar el proveedor, haga lo siguiente: 
	1. Abra la configuración de máquina de .NET 2.0 desde la carpeta: <system disk>:\\Windows\\Microsoft.NET\\Framework64\\v2.0.50727\\CONFIG\\machine.config.
	2. Busque **Proveedor de datos de Oracle para .NET**, y debería poder encontrar una entrada similar a la siguiente en **system.data** -> **DbProviderFactories**: "<add name="Oracle Data Provider for .NET" invariant="Oracle.DataAccess.Client" description="Oracle Data Provider for .NET" type="Oracle.DataAccess.Client.OracleClientFactory, Oracle.DataAccess, Version=2.112.3.0, Culture=neutral, PublicKeyToken=89b483f429c47342" />"
2.	Copie esta entrada en el archivo machine.config en la siguiente carpeta v4.0: <system disk>:\\Windows\\Microsoft.NET\\Framework64\\v4.0.30319\\Config\\machine.config, y cambie la versión a 4.xxx.x.x.
3.	Instale "<ODP.NET Installed Path>\\11.2.0\\client\_1\\odp.net\\bin\\4\\Oracle.DataAccess.dll" en la caché global de ensamblados (GAC) mediante la ejecución de "gacutil /i [provider path]".



[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

<!---HONumber=AcomDC_0224_2016-->