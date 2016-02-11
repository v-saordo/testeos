<properties 
	pageTitle="Movimiento de datos de Sybase | Factoría de datos de Azure" 
	description="Obtenga información acerca de cómo mover los datos de la base de datos de Sybase mediante Factoría de datos de Azure." 
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

# Movimiento de datos de Sybase mediante Factoría de datos de Azure 

En este artículo se describe cómo puede usar la actividad de copia en Factoría de datos de Azure para mover datos de Sybase a otro almacén de datos. Este artículo se basa en el artículo sobre [actividades de movimiento de datos](data-factory-data-movement-activities.md) que presenta una introducción general del movimiento de datos con la actividad de copia y las combinaciones del almacén de datos admitidas.

El servicio Factoría de datos admite la conexión a orígenes de Sybase locales mediante Data Management Gateway. Consulte el artículo sobre cómo [mover datos entre ubicaciones locales y la nube](data-factory-move-data-between-onprem-and-cloud.md) para obtener información acerca de Data Management Gateway, así como instrucciones paso a paso sobre cómo configurar la puerta de enlace.

**Nota:** tiene que aprovechar la puerta de enlace para conectar con Sybase, incluso si está hospedado en máquinas virtuales de IaaS de Azure. Si intenta conectarse a una instancia de Sybase hospedada en la nube, también puede instalar la instancia de puerta de enlace en la máquina virtual de IaaS.

Factoría de datos solo admite actualmente el movimiento de datos desde Sybase a otros almacenes de datos, pero no desde otros almacenes de datos a Sybase.

## Instalación

Para que Data Management Gateway se conecte a la Base de datos Sybase, es preciso instalar el [proveedor de datos para Sybase](http://go.microsoft.com/fwlink/?linkid=324846) en el mismo sistema que Data Management Gateway.

> [AZURE.NOTE] Vea [Solución de problemas de puerta de enlace](data-factory-move-data-between-onprem-and-cloud.md#gateway-troubleshooting) para obtener sugerencias sobre solución de problemas de conexión o puerta de enlace.

## Ejemplo: copia de datos de Sybase a un blob de Azure

El ejemplo siguiente muestra:

1.	Un servicio vinculado de tipo [OnPremisesSybase](data-factory-onprem-sybase-connector.md#sybase-linked-service-properties).
2.	Un servicio vinculado de tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3.	Un [conjunto de datos](data-factory-create-datasets.md) de entrada de tipo [RelationalTable](data-factory-onprem-sybase-connector.md#sybase-dataset-type-properties).
4.	Un [conjunto de datos](data-factory-create-datasets.md) de salida de tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.	La [canalización](data-factory-create-pipelines.md) con la actividad de copia que usa [RelationalSource](data-factory-onprem-sybase-connector.md#sybase-copy-activity-type-properties) y [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

El ejemplo copia cada hora los datos de un resultado de consulta de la base de datos Sybase en un blob. Las propiedades JSON usadas en estos ejemplos se describen en las secciones que aparecen después de los ejemplos.

Como primer paso, configure la puerta de enlace de administración de datos según las instrucciones del artículo sobre cómo [mover datos entre las ubicaciones locales y la nube](data-factory-move-data-between-onprem-and-cloud.md).

**Servicio vinculado de Sybase:**

	{
	    "name": "OnPremSybaseLinkedService",
	    "properties": {
	        "type": "OnPremisesSybase",
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


**Conjunto de datos de entrada de Sybase:**

El ejemplo supone que ha creado una tabla "MyTable" en Sybase y que contiene una columna denominada "timestamp" para los datos de serie temporal.

Si se establece "external": true y se especifica la directiva externalData, se indica a Factoría de datos que la tabla es externa a la factoría de datos y que no se produce por ninguna actividad de la factoría de datos. Tenga en cuenta que el **tipo**del servicio vinculado se establece en: **RelationalTable**.
	
	{
	    "name": "SybaseDataSet",
	    "properties": {
	        "type": "RelationalTable",
	        "linkedServiceName": "OnPremSybaseLinkedService",
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
	    "name": "AzureBlobSybaseDataSet",
	    "properties": {
	        "type": "AzureBlob",
	        "linkedServiceName": "AzureStorageLinkedService",
	        "typeProperties": {
	            "folderPath": "mycontainer/sybase/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

La canalización contiene una actividad de copia que está configurada para usar los conjuntos de datos de entrada y de salida y está programada para ejecutarse cada hora. En la definición de la canalización JSON, el tipo **source** se establece en **RelationalSource** y el tipo **sink** se establece en **BlobSink**. La consulta SQL especificada para la propiedad **query** selecciona los datos de la tabla DBA.Orders de la base de datos.


	{
	    "name": "CopySybaseToBlob",
	    "properties": {
	        "description": "pipeline for copy activity",
	        "activities": [
	            {
	                "type": "Copy",
	                "typeProperties": {
	                    "source": {
	                        "type": "RelationalSource",
	                        "query": "select * from DBA.Orders"
	                    },
	                    "sink": {
	                        "type": "BlobSink"
	                    }
	                },
	                "inputs": [
	                    {
	                        "name": "SybaseDataSet"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "AzureBlobSybaseDataSet"
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
	                "name": "SybaseToBlob"
	            }
	        ],
	        "start": "2014-06-01T18:00:00Z",
	        "end": "2014-06-01T19:00:00Z"
	    }
	}


## Propiedades del servicio vinculado de Sybase

En la tabla siguiente se proporciona la descripción de los elementos JSON específicos al servicio vinculado de Sybase.

Propiedad | Descripción | Obligatorio
-------- | ----------- | --------
type | La propiedad type debe establecerse en: **OnPremisesSybase** | Sí
server | Nombre del servidor de Sybase. | Sí
database | Nombre de la base de datos Sybase. | Sí 
schema | Nombre del esquema de la base de datos. | No
authenticationType | Tipo de autenticación usado para conectarse a la base de datos Sybase. Los valores posibles son: Anonymous, Basic y Windows. | Sí
nombre de usuario | Especifique el nombre de usuario si usa la autenticación Basic o Windows. | No
contraseña | Especifique la contraseña de la cuenta de usuario especificada para el nombre de usuario. | No
gatewayName | Nombre de la puerta de enlace que debe usar el servicio Factoría de datos para conectarse a la base de datos de Sybase local. | Sí 

Consulte [Configuración de credenciales y seguridad](data-factory-move-data-between-onprem-and-cloud.md#setting-credentials-and-security) para obtener más información acerca de cómo configurar las credenciales para un origen de datos de Sybase local.

## Propiedades del tipo Base de datos Sybase

Para obtener una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, consulte el artículo [Creación de conjuntos de datos](data-factory-create-datasets.md). Las secciones como structure, availability y policy de un conjunto de datos JSON son similares en todos los tipos de conjunto de datos (SQL Azure, blob de Azure, tabla de Azure, etc.).

La sección typeProperties es diferente en cada tipo de conjunto de datos y proporciona información acerca de la ubicación de los datos en el almacén de datos. La sección **typeProperties** del conjunto de datos de tipo **RelationalTable** (que incluye el conjunto de datos de Sybase) tiene las propiedades siguientes.

Propiedad | Descripción | Obligatorio
-------- | ----------- | --------
tableName | Nombre de la tabla en la instancia de Base de datos Sybase a la que hace referencia el servicio vinculado. | No (si se especifica **query** de **RelationalSource**)

## Propiedades de tipo de actividad de copia de Sybase 

Para obtener una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo [Creación de canalizaciones](data-factory-create-pipelines.md). Propiedades como nombre, descripción, tablas de entrada y salida, varias directivas, etc. están disponibles para todos los tipos de actividades.

Por otro lado, las propiedades disponibles en la sección typeProperties de la actividad varían con cada tipo de actividad y, en caso de la actividad de copia, varían en función de los tipos de orígenes y receptores.

En caso de la actividad de copia si el origen es de tipo **RelationalSource** (que incluye Sybase), están disponibles las propiedades siguientes en la sección **typeProperties**:

Propiedad | Descripción | Valores permitidos | Obligatorio
-------- | ----------- | -------------- | --------
query | Utilice la consulta personalizada para leer los datos. | Cadena de consulta SQL. Por ejemplo: select * from MyTable. | No (si se especifica **tableName** de **dataset**)

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## Asignación de tipos para Sybase

Como se mencionó en el artículo sobre actividades de movimiento de datos, la actividad de copia realiza conversiones automáticas de los tipos de origen a los tipos de receptor con el siguiente enfoque de dos pasos:

1. Conversión de tipos de origen nativos al tipo .NET
2. Conversión de tipo .NET al tipo del receptor nativo

Sybase admite T-SQL y tipos de T-SQL. Para ver una tabla de asignación de tipos sql al tipo .NET, consulte el artículo [Conector SQL Azure](data-factory-azure-sql-connector.md).

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

<!---HONumber=AcomDC_0128_2016-->