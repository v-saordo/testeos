<properties 
	pageTitle="Movimiento de datos desde almacenes de datos ODBC | Factoría de datos de Azure" 
	description="Obtenga información sobre cómo mover datos desde almacenes de datos ODBC mediante Factoría de datos de Azure." 
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

# Movimiento de datos desde almacenes de datos ODBC mediante Factoría de datos de Azure
En este artículo se describe cómo se puede usar la actividad de copia en Factoría de datos de Azure para mover datos de un almacén de datos ODBC local a otro almacén de datos. Este artículo se basa en el artículo sobre [actividades de movimiento de datos](data-factory-data-movement-activities.md) que presenta una introducción general del movimiento de datos con la actividad de copia y las combinaciones del almacén de datos admitidas.

En la actualidad, Factoría de datos solo admite el movimiento de datos desde un almacén de datos ODBC local a otros almacenes de datos, pero no el movimiento inverso.


## Habilitación de la conectividad
El servicio Factoría de datos admite la conexión a orígenes ODBC locales mediante Data Management Gateway. Consulte el artículo sobre cómo [mover datos entre ubicaciones locales y la nube](data-factory-move-data-between-onprem-and-cloud.md) para obtener información acerca de Data Management Gateway, así como instrucciones paso a paso sobre cómo configurar la puerta de enlace. Es preciso que sacar provecho de la puerta de enlace para conectar con un almacén de datos ODBC, aunque esté hospedado en una máquina virtual de IaaS de Azure.

Aunque puede instalar la puerta de enlace en el mismo equipo local o en la máquina virtual de Azure como el almacén de datos ODBC, se recomienda instalar la puerta de enlace en una máquina independiente o una máquina virtual de IaaS de Azure independiente para evitar la contención de recursos y para mejorar el rendimiento. Al instalar la puerta de enlace en una máquina independiente, la máquina debería poder acceder a la máquina con el almacén de datos ODBC.

Aparte de Data Management Gateway, también debe instalar el controlador ODBC para el almacén de datos en la máquina de puerta de enlace.

> [AZURE.NOTE] Vea [Solución de problemas de puerta de enlace](data-factory-move-data-between-onprem-and-cloud.md#gateway-troubleshooting) para obtener sugerencias sobre solución de problemas de conexión o puerta de enlace.

## Ejemplo: copiar datos del almacén de datos ODBC a un blob de Azure

En este ejemplo, se muestra cómo copiar datos de un almacén de datos ODBC al Almacenamiento de blobs de Azure. Sin embargo, se pueden copiar datos **directamente** a cualquiera de los receptores indicados [aquí](data-factory-data-movement-activities.md#supported-data-stores) mediante la actividad de copia en Factoría de datos de Azure.
 
El ejemplo consta de las siguientes entidades de factoría de datos:

1.	Un servicio vinculado del tipo [OnPremisesOdbc](#odbc-linked-service-properties).
2.	Un servicio vinculado de tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3.	Un [conjunto de datos](data-factory-create-datasets.md) de entrada de tipo [RelationalTable](#odbc-dataset-type-properties).
4.	Un [conjunto de datos](data-factory-create-datasets.md) de salida de tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.	Una [canalización](data-factory-create-pipelines.md) con la actividad de copia que usa [RelationalSource](#odbc-copy-activity-type-properties) y [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

El ejemplo copia los datos del resultado de una consulta en un almacén de datos ODBC en un blob cada hora. Las propiedades JSON usadas en estos ejemplos se describen en las secciones que aparecen después de los ejemplos.

Como primer paso, configure la puerta de enlace de administración de datos según las instrucciones del artículo sobre cómo [mover datos entre las ubicaciones locales y la nube](data-factory-move-data-between-onprem-and-cloud.md).

**Servicio vinculado de ODBC** En este ejemplo se usa la autenticación básica. Consulte la sección [Propiedades del servicio vinculado de ODBC](#odbc-linked-service-properties) para conocer los diferentes tipos de autenticación que se pueden usar.

	{
	    "name": "OnPremOdbcLinkedService",
	    "properties":
	    {
	        "type": "OnPremisesOdbc",
	        "typeProperties":
	        {
	            "authenticationType": "Basic",
	            "connectionString": "Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;",
	            "userName": "username",
	            "password": "password",
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

**Conjunto de datos de entrada de ODBC**

El ejemplo asume que ha creado una tabla, "MyTable", en una base de datos ODBC y que contiene una columna denominada "timestampcolumn" para los datos de series temporales.

Si se establece "external": "true" y se especifica la directiva externalData, se informa al servicio Factoría de datos que la tabla es externa a la factoría de datos y que no se producen por ninguna actividad de la factoría de datos
	
	{
	    "name": "ODBCDataSet",
	    "properties": {
	        "published": false,
	        "type": "RelationalTable",
	        "linkedServiceName": "OnPremOdbcLinkedService",
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
	    "name": "AzureBlobOdbcDataSet",
	    "properties": {
	        "type": "AzureBlob",
	        "linkedServiceName": "AzureStorageLinkedService",
	        "typeProperties": {
	            "folderPath": "mycontainer/odbc/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
	    "name": "CopyODBCToBlob",
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
	                        "name": "OdbcDataSet"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "AzureBlobOdbcDataSet"
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
	                "name": "OdbcToBlob"
	            }
	        ],
	        "start": "2014-06-01T18:00:00Z",
	        "end": "2014-06-01T19:00:00Z"
	    }
	}



## Propiedades del servicio vinculado de ODBC

En la tabla siguiente se proporciona la descripción de los elementos JSON específicos del servicio vinculado de ODBC.

| Propiedad | Descripción | Obligatorio |
| -------- | ----------- | -------- | 
| type | La propiedad type debe establecerse en: **OnPremisesOdbc** | Sí |
| connectionString | La parte de la credencial de no acceso de la cadena de conexión, así como una credencial cifrada opcional. Vea los ejemplos siguientes. | Sí
| credential | La parte de la credencial de acceso de la cadena de conexión especificada en formato de valor de propiedad específico del controlador, por ejemplo "Uid=<user ID>;Pwd=<password>;RefreshToken=<secret refresh token>;". | No
| authenticationType | Tipo de autenticación que se usa para conectarse al almacén de datos ODBC. Los valores posibles son: Anonymous y Basic. | Sí | 
| nombre de usuario | Especifique el nombre de usuario si usa la autenticación básica. | No | 
| contraseña | Especifique la contraseña de la cuenta de usuario especificada para el nombre de usuario. | No | 
| gatewayName | Nombre de la puerta de enlace que el servicio Factoría de datos debe usar para conectarse al almacén de datos ODBC. | Sí |


Para más información sobre cómo configurar las credenciales de un almacén de datos ODBC local, consulte [Configuración de credenciales y seguridad](data-factory-move-data-between-onprem-and-cloud.md#setting-credentials-and-security).

### Uso de la autenticación básica

	{
	    "name": "odbc",
	    "properties":
	    {
	        "type": "OnPremisesOdbc",
	        "typeProperties":
	        {
	            "authenticationType": "Basic",
	            "connectionString": "Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;",
	            "userName": "username",
	            "password": "password",
	            "gatewayName": "mygateway"
	        }
	    }
	}

### Uso de la autenticación básica con credenciales cifradas
Las credenciales se pueden cifrar mediante el cmdlet [New-AzureRMDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx) (versión 1.0 de Azure PowerShell) o [New-AzureDataFactoryEncryptValue](https://msdn.microsoft.com/library/dn834940.aspx) (versión 0.9 o una versión anterior de Azure PowerShell).

	{
	    "name": "odbc",
	    "properties":
	    {
	        "type": "OnPremisesOdbc",
	        "typeProperties":
	        {
	            "authenticationType": "Basic",
	            "connectionString": "Driver={SQL Server};Server=myserver.database.windows.net; Database=TestDatabase;;EncryptedCredential=eyJDb25uZWN0...........................",
	            "gatewayName": "mygateway"
	        }
	    }
	}


### Uso de autenticación anónima

	{
	    "name": "odbc",
	    "properties":
	    {
	        "type": "OnPremisesOdbc",
	        "typeProperties":
	        {
	            "authenticationType": "Anonymous",
	            "connectionString": "Driver={SQL Server};Server={servername}.database.windows.net; Database=TestDatabase;",
	            "credential": "UID={uid};PWD={pwd}",
	            "gatewayName": "mygateway"
	        }
	    }
	}



## Propiedades del tipo de conjunto de datos ODBC

Para una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, vea el artículo [Creación de conjuntos de datos](data-factory-create-datasets.md). Las secciones como structure, availability y policy de un conjunto de datos JSON son similares en todos los tipos de conjunto de datos (SQL Azure, blob de Azure, tabla de Azure, etc.).

La sección **typeProperties** es diferente en cada tipo de conjunto de datos y proporciona información acerca de la ubicación de los datos en el almacén de datos. La sección typeProperties del conjunto de datos del tipo **RelationalTable** (que incluye el conjunto de datos ODBC) tiene las propiedades siguientes:

| Propiedad | Descripción | Obligatorio |
| -------- | ----------- | -------- |
| tableName | Nombre de la tabla del almacén de datos ODBC a la que hace referencia el servicio vinculado. | Sí | 

## Propiedades de tipo de actividad de copia de ODBC

Para obtener una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo [Creación de canalizaciones](data-factory-create-pipelines.md). Propiedades como nombre, descripción, tablas de entrada y salida, varias directivas, etc. están disponibles para todos los tipos de actividades.

Por otro lado, las propiedades disponibles en la sección typeProperties de la actividad varían con cada tipo de actividad y, en caso de la actividad de copia, varían en función de los tipos de orígenes y receptores.

En caso de actividad de copia si el origen es del tipo **RelationalSource** (que incluye ODBC), están disponibles las propiedades siguientes en la sección typeProperties:

| Propiedad | Descripción | Valores permitidos | Obligatorio |
| -------- | ----------- | -------------- | -------- |
| query | Utilice la consulta personalizada para leer los datos. | Cadena de consulta SQL. Por ejemplo: select * from MyTable. | Sí | 

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### Asignación de tipos para ODBC

Como se mencionó en el artículo sobre [actividades del movimiento de datos](data-factory-data-movement-activities.md), la actividad de copia realiza conversiones automáticas de los tipos de origen a los tipos de receptor con el siguiente enfoque de dos pasos:

1. Conversión de tipos de origen nativos al tipo .NET
2. Conversión de tipo .NET al tipo del receptor nativo

Al mover datos desde almacenes de datos ODBC, los tipos de datos ODBC se asignan a tipos de .NET, como se mencionó en el tema [Asignar tipos de datos ODBC](https://msdn.microsoft.com/library/cc668763.aspx).


[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

<!---HONumber=AcomDC_0204_2016-->