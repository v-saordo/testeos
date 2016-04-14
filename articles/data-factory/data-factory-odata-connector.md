<properties 
	pageTitle="Movimiento de datos desde orígenes de OData | Factoría de datos de Azure" 
	description="Obtenga información sobre cómo mover datos desde orígenes de OData mediante Factoría de datos de Azure." 
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
	ms.date="02/18/2016" 
	ms.author="spelluru"/>

# Movimiento de datos de un origen de OData mediante Factoría de datos de Azure
En este artículo se describe cómo puede usar la actividad de copia en Factoría de datos de Azure para mover datos de un origen de OData a otro almacén de datos. Este artículo se basa en el artículo sobre [actividades de movimiento de datos](data-factory-data-movement-activities.md) que presenta una introducción general del movimiento de datos con la actividad de copia y las combinaciones del almacén de datos admitidas.

## Ejemplo: copia de datos de un origen de OData a un blob de Azure

En este ejemplo se muestra cómo copiar datos de un origen de OData en Almacenamiento de blobs de Azure. Sin embargo, se pueden copiar datos **directamente** en cualquiera de los receptores indicados [aquí](data-factory-data-movement-activities.md#supported-data-stores) mediante la actividad de copia de Factoría de datos de Azure.
 
El ejemplo consta de las siguientes entidades de factoría de datos:

1.	Un servicio vinculado de tipo [OData](#odata-linked-service-properties).
2.	Un servicio vinculado de tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3.	Un [conjunto de datos](data-factory-create-datasets.md) de entrada de tipo [ODataResource](#odata-dataset-type-properties).
4.	Un [conjunto de datos](data-factory-create-datasets.md) de salida de tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.	Una [canalización](data-factory-create-pipelines.md) con la actividad de copia que usa [RelationalSource](#odata-copy-activity-type-properties) y [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

En el ejemplo se copian los datos de la consulta en un origen de OData a un blob de Azure cada hora. Las propiedades JSON usadas en estos ejemplos se describen en las secciones que aparecen después de los ejemplos.

**Servicio vinculado de OData** En este ejemplo se usa la autenticación básica. Consulte la sección [Propiedades del servicio vinculado de OData](#odata-linked-service-properties) para conocer los diferentes tipos de autenticación que se pueden usar.

    {
		"name": "ODataLinkedService",
       	"properties": 
        {
        	"type": "OData",
           	"typeProperties": 
            {
            	"url": "http://services.odata.org/OData/OData.svc",
               "authenticationType": "Anonymous"
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

**Conjunto de datos de entrada de OData**

Si se establece "external": "true" y se especifica la directiva externalData, se informa al servicio Factoría de datos que la tabla es externa a la factoría de datos y que no se producen por ninguna actividad de la factoría de datos
	
    {
    	"name": "ODataDataset",
		"properties": 
		{
    		"type": "ODataResource",
    		"typeProperties": 
			{
     			"path": "Products" 
    		},
    		"linkedServiceName": "ODataLinkedService",
    		"structure": [],
    		"availability": {
        		"frequency": "Hour",
        		"interval": 1
    		},
			"external": true,
    		"policy": {
            	"retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
	            "maximumRetry": 3				
			}
		}
	}

Tenga en cuenta que la especificación de **path** en la definición del conjunto de datos es opcional.


**Conjunto de datos de salida de blob de Azure**

Los datos se escriben en un nuevo blob cada hora (frecuencia: hora, intervalo: 1). La ruta de acceso de la carpeta para el blob se evalúa dinámicamente según la hora de inicio del segmento que se está procesando. La ruta de acceso de la carpeta usa las partes year, month, day y hours de la hora de inicio.

	{
	    "name": "AzureBlobODataDataSet",
	    "properties": {
	        "type": "AzureBlob",
	        "linkedServiceName": "AzureStorageLinkedService",
	        "typeProperties": {
	            "folderPath": "mycontainer/odata/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

La canalización contiene una actividad de copia que está configurada para usar los conjuntos de datos de entrada y de salida y está programada para ejecutarse cada hora. En la definición de la canalización JSON, el tipo **source** se establece en **RelationalSource** y el tipo **sink** se establece en **BlobSink**. La consulta SQL especificada para la propiedad **query** selecciona los últimos datos (los más recientes) del origen de OData.
	
	{
	    "name": "CopyODataToBlob",
	    "properties": {
	        "description": "pipeline for copy activity",
	        "activities": [
	            {
	                "type": "Copy",
	                "typeProperties": {
	                    "source": {
	                        "type": "RelationalSource",
	                        "query": "?$select=Name, Description&$top=5",
	                    },
	                    "sink": {
	                        "type": "BlobSink",
	                        "writeBatchSize": 0,
	                        "writeBatchTimeout": "00:00:00"
	                    }
	                },
	                "inputs": [
	                    {
	                        "name": "ODataDataSet"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "AzureBlobODataDataSet"
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
	                "name": "ODataToBlob"
	            }
	        ],
	        "start": "2016-02-01T18:00:00Z",
	        "end": "2016-02-03T19:00:00Z"
	    }
	}


Tenga en cuenta que la especificación de **query** en la definición de la canalización es opcional. Dirección **URL** que el servicio Factoría de datos usa para recuperar los datos: dirección URL especificada en el servicio vinculado (obligatorio) + ruta de acceso especificada en el conjunto de datos (opcional) + consulta en la canalización (opcional).

## Propiedades del servicio vinculado de OData

En la tabla siguiente se proporciona la descripción de los elementos JSON específicos del servicio vinculado de OData.

| Propiedad | Descripción | Obligatorio |
| -------- | ----------- | -------- | 
| type | La propiedad type debe establecerse en: **OData** | Sí |
| url| Dirección URL del servicio de OData. | Sí |
| authenticationType | Tipo de autenticación que se usa para conectarse al origen de OData. Los valores posibles son: Anonymous y Basic. | Sí | 
| nombre de usuario | Especifique el nombre de usuario si usa la autenticación básica. | Sí (solo si usa la autenticación básica) | 
| contraseña | Especifique la contraseña de la cuenta de usuario especificada para el nombre de usuario. | Sí (solo si usa la autenticación básica) | 

### Uso de la autenticación básica

    {
        "name": "inputLinkedService",
       "properties": 
        {
            "type": "OData",
           	"typeProperties": 
            {
               "url": "http://services.odata.org/OData/OData.svc",
               "authenticationType": "Basic",
                "username": "username",
               "password": "password"
           }
       }
    }

### Uso de autenticación anónima
	
    {
		"name": "ODataLinkedService",
       	"properties": 
        {
            "type": "OData",
           "typeProperties": 
            {
               "url": "http://services.odata.org/OData/OData.svc",
               "authenticationType": "Anonymous"
           }
       }
    }



## Propiedades del tipo de conjunto de datos de OData

Para una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, vea el artículo [Creación de conjuntos de datos](data-factory-create-datasets.md). Las secciones como structure, availability y policy de un conjunto de datos JSON son similares en todos los tipos de conjunto de datos (SQL Azure, blob de Azure, tabla de Azure, etc.).

La sección **typeProperties** es diferente en cada tipo de conjunto de datos y proporciona información acerca de la ubicación de los datos en el almacén de datos. La sección typeProperties del conjunto de datos del tipo **ODataResource** (que incluye el conjunto de datos de OData) tiene las propiedades siguientes:

| Propiedad | Descripción | Obligatorio |
| -------- | ----------- | -------- |
| path | Ruta de acceso al recurso de OData | No | 

## Propiedades de tipo de actividad de copia de OData

Para obtener una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo [Creación de canalizaciones](data-factory-create-pipelines.md). Propiedades como nombre, descripción, tablas de entrada y salida, varias directivas, etc. están disponibles para todos los tipos de actividades.

Por otro lado, las propiedades disponibles en la sección typeProperties de la actividad varían con cada tipo de actividad y, en caso de la actividad de copia, varían en función de los tipos de orígenes y receptores.

En caso de actividad de copia si el origen es del tipo **RelationalSource** (que incluye OData), están disponibles las propiedades siguientes en la sección typeProperties:

| Propiedad | Descripción | Ejemplo | Obligatorio |
| -------- | ----------- | -------------- | -------- |
| query | Utilice la consulta personalizada para leer los datos. | "?$select=Name, Description&$top=5" | No | 

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### Asignación de tipos para OData

Como se mencionó en el artículo sobre [actividades del movimiento de datos](data-factory-data-movement-activities.md), la actividad de copia realiza conversiones automáticas de los tipos de origen a los tipos de receptor con el siguiente enfoque de dos pasos:

1. Conversión de tipos de origen nativos al tipo .NET
2. Conversión de tipo .NET al tipo del receptor nativo

Cuando se mueven datos desde almacenes de datos de OData, los tipos de datos de OData se asignan a tipos. NET.


[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

<!---HONumber=AcomDC_0224_2016-->