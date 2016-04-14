<properties 
	pageTitle="Movimiento de datos de DB2 | Factoría de datos de Azure" 
	description="Obtenga información acerca de cómo mover los datos de la base de datos de DB2 mediante Factoría de datos de Azure" 
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

# Movimiento de datos de DB2 mediante Factoría de datos de Azure
En este artículo se describe cómo puede usar la actividad de copia en Factoría de datos de Azure para mover datos de DB2 a otro almacén de datos. Este artículo se basa en el artículo sobre [actividades de movimiento de datos](data-factory-data-movement-activities.md) que presenta una introducción general del movimiento de datos con la actividad de copia y las combinaciones del almacén de datos admitidas.

La factoría de datos admite la conexión a orígenes de DB2 locales mediante Data Management Gateway. Consulte el artículo sobre cómo [mover datos entre ubicaciones locales y la nube](data-factory-move-data-between-onprem-and-cloud.md) para obtener información acerca de Data Management Gateway, así como instrucciones paso a paso sobre cómo configurar la puerta de enlace.

**Nota:** Tiene que aprovechar la puerta de enlace para conectar con DB2, incluso si está hospedado en máquinas virtuales de IaaS de Azure. Si está intentando conectarse a una instancia de DB2 hospedada en la nube, también puede instalar la instancia de puerta de enlace en la máquina virtual de IaaS.

Factoría de datos solo admite actualmente el movimiento de datos desde DB2 a otros almacenes de datos, pero no desde otros almacenes de datos a DB2.

## Instalación 

Para que Data Management Gateway se conecte a la base de datos DB2, es preciso instalar [IBM DB2 Data Server Drive](http://go.microsoft.com/fwlink/p/?LinkID=274911) en el mismo sistema que Data Management Gateway.

Se conocen varios problemas notificados por IBM acerca de la instalación de IBM DB2 Data Server Driver en Windows 8, donde se necesitan pasos de instalación adicionales. Para obtener más información sobre IBM DB2 Data Server Driver en Windows 8, consulte [http://www-01.ibm.com/support/docview.wss?uid=swg21618434](http://www-01.ibm.com/support/docview.wss?uid=swg21618434).

> [AZURE.NOTE] Vea [Solución de problemas de puerta de enlace](data-factory-move-data-between-onprem-and-cloud.md#gateway-troubleshooting) para obtener sugerencias sobre solución de problemas de conexión o puerta de enlace.


## Ejemplo: Copiar datos de DB2 a un blob de Azure

En este ejemplo, se muestra cómo copiar datos de una base de datos DB2 local a un Almacenamiento de blobs de Azure. Sin embargo, se pueden copiar datos **directamente** a cualquiera de los receptores indicados [aquí](data-factory-data-movement-activities.md#supported-data-stores) mediante la actividad de copia en Factoría de datos de Azure.
 
El ejemplo consta de las siguientes entidades de factoría de datos:

1.	Un servicio vinculado de tipo [OnPremisesDb2](data-factory-onprem-db2-connector.md#db2-linked-service-properties).
2.	Un servicio vinculado de tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties). 
3.	Un [conjunto de datos](data-factory-create-datasets.md) de entrada de tipo [RelationalTable](data-factory-onprem-db2-connector.md#db2-dataset-type-properties).
4.	Un [conjunto de datos](data-factory-create-datasets.md) de salida de tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties). 
5.	Una [canalización](data-factory-create-pipelines.md) con la actividad de copia que usa [RelationalSource](data-factory-onprem-db2-connector.md#db2-copy-activity-type-properties) y [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties). 

El ejemplo copia cada hora los datos de un resultado de consulta de la base de datos DB2 en un blob. Las propiedades JSON usadas en estos ejemplos se describen en las secciones que aparecen después de los ejemplos.

Como primer paso, configure la puerta de enlace de administración de datos según las instrucciones del artículo sobre cómo [mover datos entre las ubicaciones locales y la nube](data-factory-move-data-between-onprem-and-cloud.md).

**Servicio vinculado DB2:**

	{
	    "name": "OnPremDb2LinkedService",
	    "properties": {
	        "type": "OnPremisesDb2",
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
	        "type": "AzureStorageLinkedService",
			"typeProperties": {
	        	"connectionString": "DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"
			}
	    }
	}

**Conjunto de datos de entrada de DB2**

El ejemplo supone que ha creado una tabla "MyTable" en DB2 y que contiene una columna denominada "timestamp" para los datos de serie temporal.

Si se establece "external": true y se especifica la directiva externalData, se indica a Factoría de datos que la tabla es externa a la factoría de datos y que no se produce por ninguna actividad de la factoría de datos. Tenga en cuenta que **type** se establece en **RelationalTable**.

	{
	    "name": "Db2DataSet",
	    "properties": {
	        "type": "RelationalTable",
	        "linkedServiceName": "OnPremDb2LinkedService",
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

Los datos se escriben en un nuevo blob cada hora (frecuencia: hora, intervalo: 1). La ruta de acceso de la carpeta para el blob se evalúa dinámicamente según la hora de inicio del segmento que se está procesando. La ruta de acceso de la carpeta usa las partes year, month, day y hours de la hora de inicio.

	{
	    "name": "AzureBlobDb2DataSet",
	    "properties": {
	        "type": "AzureBlob",
	        "linkedServiceName": "AzureStorageLinkedService",
	        "typeProperties": {
	            "folderPath": "mycontainer/db2/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Canalización con actividad de copia:**

La canalización contiene una actividad de copia que está configurada para usar los conjuntos de datos de entrada y de salida y está programada para ejecutarse cada hora. En la definición de la canalización JSON, el tipo **source** se establece en **RelationalSource** y el tipo **sink** se establece en **BlobSink**. La consulta SQL especificada para la propiedad **query** selecciona los datos de la tabla Orders.

	{
	    "name": "CopyDb2ToBlob",
	    "properties": {
	        "description": "pipeline for copy activity",
	        "activities": [
	            {
	                "type": "Copy",
	                "typeProperties": {
	                    "source": {
	                        "type": "RelationalSource",
	                        "query": "select * from "Orders""
	                    },
	                    "sink": {
	                        "type": "BlobSink"
	                    }
	                },
	                "inputs": [
	                    {
	                        "name": "Db2DataSet"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "AzureBlobDb2DataSet"
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
	                "name": "Db2ToBlob"
	            }
	        ],
	        "start": "2014-06-01T18:00:00Z",
	        "end": "2014-06-01T19:00:00Z"
	    }
	}


## Propiedades del servicio vinculado de DB2

En la tabla siguiente se proporciona la descripción de los elementos JSON específicos del servicio vinculado de DB2.

| Propiedad | Descripción | Obligatorio |
| -------- | ----------- | -------- | 
| type | La propiedad type debe establecerse en: **OnPremisesDB2** | Sí |
| server | Nombre del servidor DB2. | Sí |
| database | Nombre de la base de datos DB2. | Sí |
| schema | Nombre del esquema de la base de datos. | No |
| authenticationType | Tipo de autenticación usado para conectarse a la base de datos DB2. Los valores posibles son: Anonymous, Basic y Windows. | Sí |
| nombre de usuario | Especifique el nombre de usuario si usa la autenticación Basic o Windows. | No |
| contraseña | Especifique la contraseña de la cuenta de usuario especificada para el nombre de usuario. | No |
| gatewayName | Nombre de la puerta de enlace que debe usar el servicio Factoría de datos para conectarse a la base de datos DB2 local. | Sí |

Consulte [Configuración de credenciales y seguridad](data-factory-move-data-between-onprem-and-cloud.md#setting-credentials-and-security) para obtener más información acerca de cómo configurar las credenciales para un origen de datos de DB2 local.


## Propiedades de tipo de conjunto de datos de DB2

Para una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, vea el artículo [Creación de conjuntos de datos](data-factory-create-datasets.md). Las secciones como structure, availability y policy de un conjunto de datos JSON son similares en todos los tipos de conjunto de datos (SQL Azure, blob de Azure, tabla de Azure, etc.).

La sección typeProperties es diferente en cada tipo de conjunto de datos y proporciona información acerca de la ubicación de los datos en el almacén de datos. La sección typeProperties del conjunto de datos de tipo RelationalTable (que incluye el conjunto de datos de DB2) tiene las propiedades siguientes.

| Propiedad | Descripción | Obligatorio |
| -------- | ----------- | -------- | 
| tableName | Nombre de la tabla en la instancia de base de datos DB2 a la que hace referencia el servicio vinculado. | No (si se especifica **query** de **RelationalSource**) |

## Propiedades de tipo de actividad de copia de DB2

Para obtener una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo [Creación de canalizaciones](data-factory-create-pipelines.md). Propiedades como nombre, descripción, tablas de entrada y salida, varias directivas, etc. están disponibles para todos los tipos de actividades.

Por otro lado, las propiedades disponibles en la sección typeProperties de la actividad varían con cada tipo de actividad y, en caso de la actividad de copia, varían en función de los tipos de orígenes y receptores.

En caso de la actividad de copia si el origen es de tipo **RelationalSource** (que incluye DB2), están disponibles las propiedades siguientes en la sección typeProperties:


| Propiedad | Descripción | Valores permitidos | Obligatorio |
| -------- | ----------- | -------- | -------------- |
| query | Utilice la consulta personalizada para leer los datos. | Cadena de consulta SQL. Por ejemplo: select * from MyTable. | No (si se especifica **tableName** de **dataset**)|

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## Asignación de tipos para DB2
Como se mencionó en el artículo sobre [actividades de movimiento de datos](data-factory-data-movement-activities.md), la actividad de copia realiza conversiones automáticas de tipos, de los tipos de origen a los tipos de receptor con el siguiente enfoque de dos pasos:

1. Conversión de tipos de origen nativos al tipo .NET
2. Conversión de tipo .NET al tipo del receptor nativo

Al mover datos a DB2, se usarán las asignaciones siguientes de tipo DB2 a tipo .NET.

Tipo de base de datos DB2 | Tipo .NET Framework 
----------------- | ------------------- 
SmallInt | Int16
Entero | Int32
BigInt | Int64
Real | Single
Doble | Doble
Float | Doble
Decimal | Decimal
DecimalFloat | Decimal
Numeric | Decimal
Date | Datetime
Hora | TimeSpan
Timestamp | DateTime
Xml | Byte
Char | String
VarChar | String
LongVarChar | String
DB2DynArray | String
Binary | Byte
VarBinary | Byte
LongVarBinary | Byte
Graphic | String
VarGraphic | String
LongVarGraphic | String
Clob | String
Blob | Byte
DbClob | String
SmallInt | Int16
Entero | Int32
BigInt | Int64
Real | Single
Doble | Doble
Float | Doble
Decimal | Decimal
DecimalFloat | Decimal
Numeric | Decimal
Date | Datetime
Hora | TimeSpan
Timestamp | DateTime
Xml | Byte
Char | String


[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]


[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

<!---HONumber=AcomDC_0204_2016-->