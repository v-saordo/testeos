<properties
   pageTitle="Carga de datos con Factoría de datos de Azure | Microsoft Azure"
   description="Más información sobre Factoría de datos de Azure"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""
   tags="azure-sql-data-warehouse"/>
<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="03/03/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# Carga de datos con la Factoría de datos de Azure

> [AZURE.SELECTOR]
- [Factoría de datos](sql-data-warehouse-get-started-load-with-azure-data-factory.md)
- [PolyBase](sql-data-warehouse-get-started-load-with-polybase.md)
- [BCP](sql-data-warehouse-load-with-bcp.md)

 En este tutorial se muestra cómo crear una canalización en Factoría de datos de Azure para mover datos desde el Blob de almacenamiento de Azure a Almacenamiento de datos SQL. Con los siguiente pasos, hará lo siguiente:

+ Configurar datos de ejemplo en un blob de Almacenamiento de Azure.
+ Conectarse a recursos en Factoría de datos de Azure.
+ Crear una canalización para mover datos de los blobs de Almacenamiento al Almacenamiento de datos SQL.

>[AZURE.VIDEO loading-azure-sql-data-warehouse-with-azure-data-factory]


## Antes de empezar

Para familiarizarse con Data Factory de Azure, consulte [Introducción al servicio Data Factory de Azure](../data-factory/data-factory-introduction.md).

### Creación o identificación de recursos

Antes de comenzar este tutorial, debe contar con los siguientes recursos.

   + **Blob de Almacenamiento de Azure**: en este tutorial se usa el blob de almacenamiento de Azure como origen de datos para la canalización de Data Factory de Azure, así que debe tener uno disponible para almacenar los datos de ejemplo. Si todavía no tiene una, aprenda a [crear una cuenta de almacenamiento](../storage/storage-create-storage-account/#create-a-storage-accoun/).

   + **Almacenamiento de datos SQL**: en este tutorial los datos se mueven desde el blob de almacenamiento de Azure hasta Almacenamiento de datos SQL, así que debe tener un almacén de datos en línea que esté cargado con los datos de ejemplo de AdventureWorksDW. Si no dispone de un almacén de datos, aprenda cómo se [aprovisiona uno](sql-data-warehouse-get-started-provision.md). Si tiene un almacén de datos pero no lo ha aprovisionado con los datos de ejemplo, puede [cargarlo manualmente](sql-data-warehouse-get-started-manually-load-samples.md).

   + **Data Factory de Azure**: Data Factory de Azure realizará la carga real, así que debe tener una factoría de datos que pueda usar para crear la canalización del movimiento de datos. Si aún no tiene una, aprenda cómo crearla en el paso 1 de [Introducción a Data Factory de Azure (Editor de Data Factory)](../data-factory/data-factory-build-your-first-pipeline-using-editor.md).

   + **AZCopy**: necesita AZCopy para copiar los datos de ejemplo desde el cliente local hasta el blob de almacenamiento de Azure. Para conocer las instrucciones de instalación, consulte la [documentación de AZCopy](../storage/storage-use-azcopy.md).

## Paso 1: Copia de los datos de ejemplo en el Blob de almacenamiento de Azure

Una vez que todos los componentes están listos, ya está preparado para copiar los datos de ejemplo en el Blob de almacenamiento de Azure.

1. [Descargue los datos de ejemplo](https://migrhoststorage.blob.core.windows.net/adfsample/FactInternetSales.csv). Estos datos agregarán otros tres años de datos de ventas a los datos de ejemplo de AdventureWorksDW.

2. Utilice este comando de AZCopy para copiar los tres años de datos en el Blob de almacenamiento de Azure.

````
AzCopy /Source:<Sample Data Location>  /Dest:https://<storage account>.blob.core.windows.net/<container name> /DestKey:<storage key> /Pattern:FactInternetSales.csv
````


## Paso 2: Conexión de los recursos a Factoría de datos de Azure

Ahora que los datos están en su sitio podemos crear la canalización de Factoría de datos de Azure para mover los datos desde el almacenamiento de blobs de Azure a Almacenamiento de datos SQL.

Para empezar, abra el [Portal de Azure](https://portal.azure.com/) y seleccione la factoría de datos en el menú de la izquierda.

### Paso 2.1: Creación del servicio vinculado

Vincule la cuenta de Almacenamiento de Azure y Almacenamiento de datos SQL a la factoría de datos.

1. En primer lugar, empiece el proceso de registro; para ello, haga clic en la sección "Servicios vinculados" de la factoría de datos y después haga clic en 'Nuevo almacén de datos'. Elija un nombre con el que registrar su almacenamiento de Azure, seleccione Almacenamiento de Azure como tipo y luego escriba el nombre y la clave de la cuenta.

2. Para registrar Almacenamiento de datos SQL, debe desplazarse hasta la sección 'Crear e implementar', luego seleccionar 'Nuevo almacén de datos' y, seguidamente, 'Almacenamiento de datos SQL de Azure'. Copie y pegue en esta plantilla y, a continuación, rellene su información específica.

    ````
    {
        "name": "<Linked Service Name>",
	    "properties": {
	        "description": "",
		    "type": "AzureSqlDW",
		    "typeProperties": {
		         "connectionString": "Data Source=tcp:<server name>.database.windows.net,1433;Initial Catalog=<server name>;Integrated Security=False;User ID=<user>@<servername>;Password=<password>;Connect Timeout=30;Encrypt=True"
	         }
        }
    }
    ````

### Paso 2.2: Definición del conjunto de datos

Después de crear los servicios vinculados, debemos definir los conjuntos de datos. Esto significa que hay que definir la estructura de los datos que se desplazan desde el almacenamiento al almacenamiento de datos. Puede leer más sobre creación.

1. Inicie este proceso desplazándose a la sección 'Crear e implementar' de Factoría de datos.

2. Haga clic en 'Nuevo conjunto de datos' y después en 'Almacenamiento de blobs de Azure' para vincular el almacenamiento a Factoría de datos. Puede usar el script siguiente para definir los datos de almacenamiento de blobs de Azure:

    ````
	{
	    "name": "<Dataset Name>",
		"properties": {
		    "type": "AzureBlob",
			"linkedServiceName": "<linked storage name>",
			"typeProperties": {
			    "folderPath": "<containter name>",
				"fileName": "FactInternetSales.csv",
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
    ````


3. Ahora definiremos también nuestro conjunto de datos para Almacenamiento de datos SQL. Comenzamos en la misma forma, haciendo clic en 'Nuevo conjunto de datos' y después en 'Almacenamiento de datos SQL Azure'.

    ````
    {
	    "name": "DWDataset",
		"properties": {
		    "type": "AzureSqlDWTable",
		    "linkedServiceName": "AzureSqlDWLinkedService",
		    "typeProperties": {
			    "tableName": "FactInternetSales"
			},
		    "availability": {
		        "frequency": "Hour",
			    "interval": 1
	        }
        }
    }
    ````

## Paso 3: Creación y ejecución de la canalización

Por último, vamos a configurar y ejecutar la canalización en Factoría de datos de Azure. Se trata de la operación que completará el movimiento real de datos. Puede encontrar una vista completa de las operaciones que se pueden completar con Almacenamiento de datos SQL y Factoría de datos de Azure [aquí](../data-factory/data-factory-azure-sql-data-warehouse-connector.md).

En la sección 'Crear e implementar', haga clic en 'Más comandos' y, a continuación, en 'Nueva canalización'. Después de crear la canalización, puede usar el código siguiente para transferir los datos al almacenamiento de datos:

````
{
    "name": "<Pipeline Name>",
    "properties": {
        "description": "<Description>",
        "activities": [
          {
            "type": "Copy",
    		"typeProperties": {
    		    "source": {
	    		    "type": "BlobSource",
	    			"skipHeaderLineCount": 1
	    	    },
	    		"sink": {
	    		    "type": "SqlDWSink",
	    		    "writeBatchSize": 0,
	    			"writeBatchTimeout": "00:00:10"
	    		}
	    	},
	    	"inputs": [
	    	  {
	    		"name": "<Storage Dataset>"
	    	  }
	    	],
	    	"outputs": [
	    	  {
	    	    "name": "<Data Warehouse Dataset>"
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
	    	"name": "Sample Copy",
	    	"description": "Copy Activity"
	      }
	    ],
	    "start": "<Date YYYY-MM-DD>",
	    "end": "<Date YYYY-MM-DD>",
	    "isPaused": false
    }
}
````

## Pasos siguientes

Para más información, vea lo siguiente para empezar:

- [Ruta de aprendizaje para Data Factory de Azure](https://azure.microsoft.com/documentation/learning-paths/data-factory/).
- [Movimiento de datos hacia y desde Almacenamiento de datos SQL de Azure mediante Factoría de datos de Azure](../data-factory/data-factory-azure-sql-data-warehouse-connector.md). Este es el tema de referencia principal para el uso de Factoría de datos de Azure con Almacenamiento de datos SQL de Azure.


En estos temas se proporciona información detallada sobre Factoría de datos de Azure. Se analiza Base de datos SQL de Azure o HDinsight, pero la información también se aplica a Almacenamiento de datos SQL de Azure.

- [Tutorial: Introducción a Data Factory de Azure](../data-factory/data-factory-build-your-first-pipeline.md). Este es el tutorial principal para el procesamiento de los datos con Factoría de datos de Azure. En él aprenderá a crear su primera canalización que emplea HDInsight para transformar y analizar los registros web mensualmente. Tenga en cuenta que no hay ninguna actividad de copia en este tutorial.
- [Tutorial: Copia de datos de un blob de Azure a SQL Azure](../data-factory/data-factory-get-started.md). En este tutorial, creará una canalización en Factoría de datos de Azure para copiar datos desde el Blob de almacenamiento de Azure hasta Base de datos SQL Azure.
- [Tutorial de un escenario real](../data-factory/data-factory-tutorial.md). Se trata de un tutorial detallado para el uso de Factoría de datos de Azure.

<!---HONumber=AcomDC_0309_2016-->