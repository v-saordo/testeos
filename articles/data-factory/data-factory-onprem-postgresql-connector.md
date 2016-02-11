<properties 
	pageTitle="Movimiento de datos de PostgreSQL | Factoría de datos de Azure" 
	description="Obtenga información acerca de cómo mover los datos de la base de datos de PostgreSQL mediante Factoría de datos de Azure." 
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

# Movimiento de datos de PostgreSQL mediante Factoría de datos de Azure

En este artículo se describe cómo puede usar la actividad de copia en Factoría de datos de Azure para mover datos de PostgreSQL a otro almacén de datos. Este artículo se basa en el artículo sobre [actividades de movimiento de datos](data-factory-data-movement-activities.md) que presenta una introducción general del movimiento de datos con la actividad de copia y las combinaciones del almacén de datos admitidas.

El servicio Factoría de datos admite la conexión a orígenes de PostgreSQL locales mediante Data Management Gateway. Consulte el artículo sobre cómo [mover datos entre ubicaciones locales y la nube](data-factory-move-data-between-onprem-and-cloud.md) para obtener información acerca de Data Management Gateway, así como instrucciones paso a paso sobre cómo configurar la puerta de enlace.

**Nota:** tiene que aprovechar la puerta de enlace para conectar con PostgreSQL, incluso si está hospedado en máquinas virtuales de IaaS de Azure. Si intenta conectarse a una instancia de PostgreSQL hospedada en la nube, también puede instalar la instancia de puerta de enlace en la máquina virtual de IaaS.

Factoría de datos solo admite mover datos desde PostgreSQL a otros almacenes de datos, no desde otros almacenes de datos a PostgreSQL.

## Instalación 

Para que Data Management Gateway se conecte a la Base de datos PostgreSQL, es preciso instalar el [proveedor de datos Ngpsql para PostgreSQL](http://go.microsoft.com/fwlink/?linkid=282716) en el mismo sistema que Data Management Gateway.

> [AZURE.NOTE] Vea [Solución de problemas de puerta de enlace](data-factory-move-data-between-onprem-and-cloud.md#gateway-troubleshooting) para obtener sugerencias sobre solución de problemas de conexión o puerta de enlace.

## Ejemplo: copia de datos de PostgreSQL a un blob de Azure

El ejemplo siguiente muestra:

1.	Un servicio vinculado de tipo [OnPremisesPostgreSql](data-factory-onprem-postgresql-connector.md#postgresql-linked-service-properties).
2.	Un servicio vinculado de tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3.	Un [conjunto de datos](data-factory-create-datasets.md) de entrada de tipo [RelationalTable](data-factory-onprem-postgresql-connector.md#postgresql-dataset-type-properties).
4.	Un [conjunto de datos](data-factory-create-datasets.md) de salida de tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.	La [canalización](data-factory-create-pipelines.md) con la actividad de copia que usa [RelationalSource](data-factory-onprem-postgresql-connector.md#postgresql-copy-activity-type-properties) y [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties). 

El ejemplo copia cada hora los datos de un resultado de consulta de la base de datos PostgreSQL en un blob. Las propiedades JSON usadas en estos ejemplos se describen en las secciones que aparecen después de los ejemplos.

Como primer paso, configure la puerta de enlace de administración de datos según las instrucciones del artículo sobre cómo [mover datos entre las ubicaciones locales y la nube](data-factory-move-data-between-onprem-and-cloud.md).

**Servicio vinculado de PostgreSQL:**

	{
	    "name": "OnPremPostgreSqlLinkedService",
	    "properties": {
	        "type": "OnPremisesPostgreSql",
	        "typeProperties": {
	            "server": "<server>",
	            "database": "<database>",
	            "schema": "<schema>",
	            "authenticationType": "<authentication type>",
	            "username": "<username>",
	            "password": "<password>",
	            "gatewayName": "<gatewayName>"
	        }
	    }
	}

**Servicio vinculado de almacenamiento de blobs de Azure:**
		
	{
	  "name": "AzureStorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"
	    }
	  }
	}

**Conjunto de datos de entrada de PostgreSQL**

El ejemplo supone que ha creado una tabla "MyTable" en PostgreSQL y que contiene una columna denominada "timestamp" para los datos de serie temporal.

Si se establece "external": true y se especifica la directiva externalData, se indica a Factoría de datos que la tabla es externa a la factoría de datos y que no se produce por ninguna actividad de la factoría de datos.

	{
	    "name": "PostgreSqlDataSet",
	    "properties": {
	        "type": "RelationalTable",
	        "linkedServiceName": "OnPremPostgreSqlLinkedService",
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


**Conjunto de datos de salida de blob de Azure:**

Los datos se escriben en un nuevo blob cada hora (frecuencia: hora, intervalo: 1). La ruta de acceso de la carpeta y el nombre de archivo para el blob se evalúan dinámicamente según la hora de inicio del segmento que se está procesando. La ruta de acceso de la carpeta usa las partes year, month, day y hours de la hora de inicio.

	{
	    "name": "AzureBlobPostgreSqlDataSet",
	    "properties": {
	        "type": "AzureBlob",
	        "linkedServiceName": "AzureStorageLinkedService",
	        "typeProperties": {
	            "folderPath": "mycontainer/postgresql/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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


**Actividad de copia:**

La canalización contiene una actividad de copia que está configurada para usar los conjuntos de datos de entrada y de salida y está programada para ejecutarse cada hora. En la definición de la canalización JSON, el tipo **source** se establece en **RelationalSource** y el tipo **sink** se establece en **BlobSink**. La consulta SQL especificada para la propiedad **query** selecciona los datos de la tabla public.usstates de la base de datos PostgreSQL.
	
	{
	    "name": "CopyPostgreSqlToBlob",
	    "properties": {
	        "description": "pipeline for copy activity",
	        "activities": [
	            {
	                "type": "Copy",
	                "typeProperties": {
	                    "source": {
	                        "type": "RelationalSource",
	                        "query": "select * from public.usstates"
	                    },
	                    "sink": {
	                        "type": "BlobSink"
	                    }
	                },
	                "inputs": [
	                    {
	                        "name": "PostgreSqlDataSet"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "AzureBlobPostgreSqlDataSet"
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
	                "name": "PostgreSqlToBlob"
	            }
	        ],
	        "start": "2014-06-01T18:00:00Z",
	        "end": "2014-06-01T19:00:00Z"
	    }
	}


## Propiedades del servicio vinculado de PostgreSQL

En la tabla siguiente se proporciona la descripción de los elementos JSON específicos del servicio vinculado de PostgreSQL.

Propiedad | Descripción | Obligatorio
-------- | ----------- | --------
type | La propiedad type debe establecerse en: **OnPremisesPostgreSql** | Sí
server | Nombre del servidor de PostgreSQL. | Sí 
database | Nombre de la base de datos de PostgreSQL. | Sí 
schema | Nombre del esquema de la base de datos. | No 
authenticationType | Tipo de autenticación usado para conectarse a la base de datos de PostgreSQL. Los valores posibles son: Anonymous, Basic y Windows. | Sí 
nombre de usuario | Especifique el nombre de usuario si usa la autenticación Basic o Windows. | No 
contraseña | Especifique la contraseña de la cuenta de usuario especificada para el nombre de usuario. | No 
gatewayName | Nombre de la puerta de enlace que debe usar el servicio Factoría de datos para conectarse a la base de datos de PostgreSQL local. | Sí 

Consulte [Configuración de credenciales y seguridad](data-factory-move-data-between-onprem-and-cloud.md#setting-credentials-and-security) para obtener más información acerca de cómo configurar las credenciales para un origen de datos de PostgreSQL local.

## Propiedades de tipo de conjunto de datos de PostgreSQL

Para obtener una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, consulte el artículo [Creación de conjuntos de datos](data-factory-create-datasets.md). Las secciones como structure, availability y policy de un conjunto de datos JSON son similares en todos los tipos de conjunto de datos (SQL Azure, blob de Azure, tabla de Azure, etc.).

La sección typeProperties es diferente en cada tipo de conjunto de datos y proporciona información acerca de la ubicación de los datos en el almacén de datos. La sección typeProperties del conjunto de datos de tipo **RelationalTable** (que incluye el conjunto de datos de PostgreSQL) tiene las propiedades siguientes:

Propiedad | Descripción | Obligatorio
-------- | ----------- | --------
tableName | Nombre de la tabla en la instancia de Base de datos de PostgreSQL a la que hace referencia el servicio vinculado. | No (si se especifica **query** de **RelationalSource**) 

## Propiedades de tipo de actividad de copia de PostgreSQL

Para obtener una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo [Creación de canalizaciones](data-factory-create-pipelines.md). Propiedades como nombre, descripción, tablas de entrada y salida, varias directivas, etc. están disponibles para todos los tipos de actividades.

Por otro lado, las propiedades disponibles en la sección typeProperties de la actividad varían con cada tipo de actividad y, en caso de la actividad de copia, varían en función de los tipos de orígenes y receptores.

En caso de la actividad de copia si el origen es de tipo **RelationalSource** (que incluye PostgreSQL), están disponibles las propiedades siguientes en la sección typeProperties:

Propiedad | Descripción | Valores permitidos | Obligatorio
-------- | ----------- | -------------- | --------
query | Utilice la consulta personalizada para leer los datos. | Cadena de consulta SQL. Por ejemplo: select * from MyTable. | No (si se especifica **tableName** de **dataset**)

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## Asignación de tipos para PostgreSQL

Como se mencionó en el artículo sobre [actividades de movimiento de datos](data-factory-data-movement-activities.md), la actividad de copia realiza conversiones automáticas de los tipos de origen a los tipos de receptor con el siguiente enfoque de dos pasos:

1. Conversión de tipos de origen nativos al tipo .NET
1. Conversión de tipo .NET al tipo del receptor nativo

Al mover datos a PostgreSQL, se usarán las asignaciones siguientes de tipo PostgreSQL a tipo .NET.

Tipo de Base de datos de PostgreSQL |	Alias de PostgresSQL | Tipo .NET Framework
------------------------ | -------------------- | ---------------------
abstime | | Datetime
bigint | int8 | Int64
bigserial | serial8 | Int64
bit | | Byte, String
bit variable [(n)] | varbit | Byte, String
boolean | booleano | Booleano
Box | | Byte, String
bytea | | Byte, String
character [ (n) ] | char [ (n) ] | String
character varying [ (n) ] | varchar [ (n) ] | String
cid | | String
cidr | | String
circle | | Byte, String
fecha | | Datetime
daterange | | String
double precision| float8 | Doble
inet | | Byte, String
intarry | | String
int4range | | String
int8range | | String
integer | int, int4 | Int32
interval [ fields ] [ (p) ] | | TimeSpan
json | | String
jsonb | | Byte
line | | Byte, String
lseg | | Byte, String
macaddr | | Byte, String
money | | Decimal
numeric [ (p, s) ] | decimal [ (p, s) ] | Decimal
numrange | | String
oid | | Int32
path | | Byte, String
pg\_lsn | | Int64
point | | Byte, String
polygon | | Byte, String
real | float4 | Single
smallint | int2 | Int16
smallserial | serial2 | Int16
serial | serial4 | Int32
text | | String


[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

<!---HONumber=AcomDC_0128_2016-->