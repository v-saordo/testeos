<properties 
	pageTitle="Movimiento de datos de MySQL | Factoría de datos de Azure" 
	description="Obtenga información acerca de cómo mover los datos de la base de datos de MySQL mediante Factoría de datos de Azure." 
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
	ms.date="11/12/2015" 
	ms.author="spelluru"/>

# Movimiento de datos de MySQL mediante Factoría de datos de Azure

En este artículo se describe cómo puede usar la actividad de copia en Factoría de datos de Azure para mover datos de MySQL a otro almacén de datos. Este artículo se basa en el artículo sobre [actividades de movimiento de datos](data-factory-data-movement-activities.md) que presenta una introducción general del movimiento de datos con la actividad de copia y las combinaciones del almacén de datos admitidas.

El servicio Factoría de datos admite la conexión a orígenes de MySQL locales mediante Data Management Gateway. Consulte el artículo sobre cómo [mover datos entre ubicaciones locales y la nube](data-factory-move-data-between-onprem-and-cloud.md) para obtener información acerca de Data Management Gateway, así como instrucciones paso a paso sobre cómo configurar la puerta de enlace.

**Nota:** tiene que aprovechar la puerta de enlace para conectar con MySQL, incluso si está hospedado en máquinas virtuales de IaaS de Azure. Si está intentando conectarse a una instancia de MySQL hospedada en la nube, también puede instalar la instancia de puerta de enlace en la máquina virtual de IaaS.

Factoría de datos solo admite actualmente el movimiento de datos desde MySQL a otros almacenes de datos, pero no de otros almacenes de datos a MySQL.

## Instalación 
Para que Data Management Gateway se conecte a la Base de datos MySQL, deberá instalar el [conector MySQL/Net 6.6.5 para Microsoft Windows](http://go.microsoft.com/fwlink/?LinkId=278885) en el mismo sistema que Data Management Gateway.

> [AZURE.NOTE] Vea [Solución de problemas de puerta de enlace](data-factory-move-data-between-onprem-and-cloud.md#gateway-troubleshooting) para obtener sugerencias sobre solución de problemas de conexión o puerta de enlace.

## Ejemplo: Copiar datos de MySQL a un blob de Azure

El ejemplo siguiente muestra:

1.	Un servicio vinculado de tipo [OnPremisesMySql](data-factory-onprem-mysql-connector.md#mysql-linked-service-properties).
2.	Un servicio vinculado de tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3.	Un [conjunto de datos](data-factory-create-datasets.md) de entrada de tipo [RelationalTable](data-factory-onprem-mysql-connector.md#mysql-dataset-type-properties).
4.	Un [conjunto de datos](data-factory-create-datasets.md) de salida de tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.	Una [canalización](data-factory-create-pipelines.md) con la actividad de copia que usa [RelationalSource](data-factory-onprem-mysql-connector.md#mysql-copy-activity-type-properties) y [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

El ejemplo copia cada hora los datos de un resultado de consulta de la base de datos MySQL en un blob. Las propiedades JSON usadas en estos ejemplos se describen en las secciones que aparecen después de los ejemplos.

Como primer paso, configure la puerta de enlace de administración de datos según las instrucciones del artículo sobre cómo [mover datos entre las ubicaciones locales y la nube](data-factory-move-data-between-onprem-and-cloud.md).

**Servicio vinculado de MySQL**

	{
	  "name": "OnPremMySqlLinkedService",
	  "properties": {
	    "type": "OnPremisesMySql",
	    "typeProperties": {
	      "server": "<server name>",
	      "database": "<database name>",
	      "schema": "<schema name>",
	      "authenticationType": "<authentication type>",
	      "userName": "<user name>",
	      "password": "<password>",
	      "gatewayName": "<gateway>"
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

**Conjunto de datos de entrada de MySQL**

El ejemplo supone que ha creado una tabla "MyTable" en MySQL y que contiene una columna denominada "timestampcolumn" para los datos de serie temporal.

Si se establece "external": "true" y se especifica la directiva externalData, se informa al servicio Factoría de datos que la tabla es externa a la factoría de datos y que no se producen por ninguna actividad de la factoría de datos
	
	{
	    "name": "MySqlDataSet",
	    "properties": {
	        "published": false,
	        "type": "RelationalTable",
	        "linkedServiceName": "OnPremMySqlLinkedService",
	        "typeProperties": {},
	        "availability": {
	            "frequency": "Hour",
	            "interval": 1
	        },
			"external": true,
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
	    "name": "AzureBlobMySqlDataSet",
	    "properties": {
	        "type": "AzureBlob",
	        "linkedServiceName": "AzureStorageLinkedService",
	        "typeProperties": {
	            "folderPath": "mycontainer/mysql/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

La canalización contiene una actividad de copia que está configurada para usar los conjuntos de datos de entrada y de salida y está programada para ejecutarse cada hora. En la definición de la canalización JSON, el tipo **source** se establece en **RelationalSource** y el tipo **sink** se establece en **BlobSink**. La consulta SQL especificada para la propiedad **query** selecciona los datos de la última hora que se van a copiar.
	
	{
	    "name": "CopyMySqlToBlob",
	    "properties": {
	        "description": "pipeline for copy activity",
	        "activities": [
	            {
	                "type": "Copy",
	                "typeProperties": {
	                    "source": {
	                        "type": "RelationalSource",
	                        "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
	                    },
	                    "sink": {
	                        "type": "BlobSink",
	                        "writeBatchSize": 0,
	                        "writeBatchTimeout": "00:00:00"
	                    }
	                },
	                "inputs": [
	                    {
	                        "name": "MySqlDataSet"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "AzureBlobMySqlDataSet"
	                    }
	                ],
	                "policy": {
	                    "timeout": "01:00:00",
	                    "concurrency": 1
	                },
	                "scheduler": {
	                    "frequency": "Hour",
	                    "interval": 1
	                },
	                "name": "MySqlToBlob"
	            }
	        ],
	        "start": "2014-06-01T18:00:00Z",
	        "end": "2014-06-01T19:00:00Z"
	    }
	}



## Propiedades del servicio vinculado de MySQL

En la tabla siguiente se proporciona la descripción de los elementos JSON específicos al servicio vinculado de MySQL.

| Propiedad | Descripción | Obligatorio |
| -------- | ----------- | -------- | 
| type | La propiedad type debe establecerse en: **OnPremisesMySql** | Sí |
| server | Nombre del servidor MySQL. | Sí |
| database | Nombre de la base de datos MySQL. | Sí | 
| schema | Nombre del esquema de la base de datos. | No | 
| authenticationType | Tipo de autenticación usado para conectarse a la Base de datos MySQL. Los valores posibles son: Anonymous, Basic y Windows. | Sí | 
| nombre de usuario | Especifique el nombre de usuario si usa la autenticación Basic o Windows. | No | 
| contraseña | Especifique la contraseña de la cuenta de usuario especificada para el nombre de usuario. | No | 
| gatewayName | Nombre de la puerta de enlace que debe usar el servicio Factoría de datos para conectarse a la Base de datos MySQL local. | Sí |

Consulte [Configuración de credenciales y seguridad](data-factory-move-data-between-onprem-and-cloud.md#setting-credentials-and-security) para obtener más información acerca de cómo configurar las credenciales para un origen de datos de MySQL local.

## Propiedades de tipo de conjunto de datos de MySQL

Para obtener una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, consulte el artículo [Creación de conjuntos de datos](data-factory-create-datasets.md). Las secciones como structure, availability y policy de un conjunto de datos JSON son similares en todos los tipos de conjunto de datos (SQL Azure, blob de Azure, tabla de Azure, etc.).

La sección **typeProperties** es diferente en cada tipo de conjunto de datos y proporciona información acerca de la ubicación de los datos en el almacén de datos. La sección typeProperties del conjunto de datos de tipo **RelationalTable** (que incluye el conjunto de datos de MySQL) tiene las propiedades siguientes:

| Propiedad | Descripción | Obligatorio |
| -------- | ----------- | -------- |
| tableName | Nombre de la tabla en la instancia de la Base de datos MySQL a la que hace referencia el servicio vinculado. | No (si se especifica **query** de **RelationalSource**) | 

## Propiedades de tipo de actividad de copia de MySQL

Para obtener una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo [Creación de canalizaciones](data-factory-create-pipelines.md). Propiedades como nombre, descripción, tablas de entrada y salida, varias directivas, etc. están disponibles para todos los tipos de actividades.

Por otro lado, las propiedades disponibles en la sección typeProperties de la actividad varían con cada tipo de actividad y, en caso de la actividad de copia, varían en función de los tipos de orígenes y receptores.

En caso de la actividad de copia si el origen es de tipo **RelationalSource** (que incluye MySQL), están disponibles las propiedades siguientes en la sección typeProperties:

| Propiedad | Descripción | Valores permitidos | Obligatorio |
| -------- | ----------- | -------------- | -------- |
| query | Utilice la consulta personalizada para leer los datos. | Cadena de consulta SQL. Por ejemplo: select * from MyTable. | No (si se especifica **tableName** de **dataset**) | 

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### Asignación de tipos para MySQL

Como se mencionó en el artículo sobre [actividades del movimiento de datos](data-factory-data-movement-activities.md), la actividad de copia realiza conversiones automáticas de los tipos de origen a los tipos de receptor con el siguiente enfoque de dos pasos:

1. Conversión de tipos de origen nativos al tipo .NET
2. Conversión de tipo .NET al tipo del receptor nativo

Al mover datos a MySQL, se usarán las asignaciones siguientes de tipos MySQL a tipos .NET.

| Tipo de Base de datos MySQL | Tipo .NET Framework |
| ------------------- | ------------------- | 
| bigint unsigned | Decimal |
| bigint | Int64 |
| bit | Decimal |
| blob | Byte |
| booleano | Booleano | 
| char | String |
| fecha | Datetime |
| datetime | Datetime |
| decimal | Decimal |
| double precision | Doble |
| double | Doble |
| enum | String |
| float | Single |
| int unsigned | Int64 |
| int | Int32 |
| integer unsigned | Int64 |
| integer | Int32 | 
| long varbinary | Byte |
| long varchar | String |
| longblob | Byte |
| longtext | String | 
| mediumblob | 	Byte | 
| mediumint unsigned | Int64 |
| mediumint | Int32 | 
| mediumtext | String |
| numeric | Decimal |
| real | Doble |
| set | String |
| smallint unsigned | Int32 |
| smallint | Int16 |
| text | String |
| Twitter en tiempo | TimeSpan |
| timestamp | Datetime |
| tinyblob | Byte |
| tinyint unsigned | Int16 | 
| tinyint | Int16 |
| tinytext | String | 
| varchar | String | 
| year | Int | 


[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

<!---HONumber=AcomDC_0128_2016-->