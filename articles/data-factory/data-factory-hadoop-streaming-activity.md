<properties 
	pageTitle="Actividad de streaming de Hadoop" 
	description="Aprenda cómo puede usar la actividad de streaming de Hadoop en una factoría de datos de Azure para ejecutar programas de streaming de Hadoop en un clúster de HDInsight suyo propio o a petición." 
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
	ms.date="11/09/2015" 
	ms.author="spelluru"/>

# Actividad de streaming de Hadoop
Puede usar la actividad HDInsightStreamingActivity para invocar un trabajo de streaming de Hadoop desde una canalización de Factoría de datos de Azure. El siguiente fragmento de código JSON muestra la sintaxis para usar HDInsightStreamingActivity en un archivo JSON de canalización.

La actividad de streaming de HDInsight en una [canalización](data-factory-create-pipelines.md) de Factoría de datos ejecuta programas de streaming de Hadoop en [su propio](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) clúster de HDInsight o en uno basado en Windows/Linux [a petición](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Este artículo se basa en las [actividades de transformación de datos](data-factory-data-transformation-activities.md) que presenta una descripción general de la transformación de datos y las actividades de transformación admitidas.

## Ejemplo de JSON
El clúster de HDInsight se rellena automáticamente con los programas de ejemplo (wc.exe y cat.exe) y los datos (davinci.txt). De forma predeterminada, el nombre del contenedor usado por el clúster de HDInsight es el nombre del propio clúster. Por ejemplo, si el nombre del clúster es myhdicluster, el nombre del contenedor de blobs asociado sería myhdicluster.

	{
	    "name": "HadoopStreamingPipeline",
	    "properties": {
	        "description": "Hadoop Streaming Demo",
	        "activities": [
	            {
	                "type": "HDInsightStreaming",
	                "typeProperties": {
	                    "mapper": "cat.exe",
	                    "reducer": "wc.exe",
	                    "input": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
	                    "output": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
	                    "filePaths": [
	                        "<nameofthecluster>/example/apps/wc.exe",
	                        "<nameofthecluster>/example/apps/cat.exe"
	                    ],
	                    "fileLinkedService": "StorageLinkedService",
	                    "getDebugInfo": "Failure"
	                },
	                "outputs": [
	                    {
	                        "name": "StreamingOutputDataset"
	                    }
	                ],
	                "policy": {
	                    "timeout": "01:00:00",
	                    "concurrency": 1,
	                    "executionPriorityOrder": "NewestFirst",
	                    "retry": 1
	                },
	                "scheduler": {
	                    "frequency": "Day",
	                    "interval": 1
	                },
	                "name": "RunHadoopStreamingJob",
	                "description": "Run a Hadoop streaming job",
	                "linkedServiceName": "HDInsightLinkedService"
	            }
	        ],
	        "start": "2014-01-04T00:00:00Z",
	        "end": "2014-01-05T00:00:00Z"
	    }
	}

Tenga en cuenta lo siguiente:

1. Establezca **linkedServiceName** en el nombre del servicio vinculado que apunta al clúster de HDInsight en el que se va a ejecutar el trabajo de MapReduce de streaming.
2. Establezca el tipo de la actividad en **HDInsightStreaming**.
3. Para la propiedad **mapper**, especifique el nombre del ejecutable del asignador. En el ejemplo anterior, cat.exe es el ejecutable del asignador.
4. Para la propiedad **reducer**, especifique el nombre del ejecutable del reductor. En el ejemplo anterior, wc.exe es el ejecutable del reductor.
5. Para la propiedad **input** type, especifique el archivo de entrada (incluida la ubicación) para el asignador. En el ejemplo: "wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt": adfsample es el contenedor de blobs, example/data/Gutenberg es la carpeta y davinci.txt es el blob.
6. Para la propiedad **output** type, especifique el archivo de salida (incluida la ubicación) para el reductor. La salida del trabajo de streaming de Hadoop se escribirá en la ubicación especificada para esta propiedad.
7. En la sección **filePaths**, especifique las rutas de acceso para los archivos ejecutables del asignador y del reductor. En el ejemplo: "adfsample/example/apps/wc.exe", adfsample es el contenedor de blobs, example/apps es la carpeta y wc.exe es el ejecutable.
8. Para la propiedad **fileLinkedService**, especifique el servicio vinculado de Almacenamiento de vinculados que representa el almacenamiento de Azure que contiene los archivos especificados en la sección filePaths.
9. Para la propiedad **arguments**, especifique los argumentos para el trabajo de streaming.
10. La propiedad **getDebugInfo** es un elemento opcional. Cuando se establece en Failure, los registros solo se descargan en caso de error. Cuando se establece en All, los registros se descargan siempre, sea cual sea el estado de ejecución.

> [AZURE.NOTE]Como se muestra en el ejemplo, deberá especificar un conjunto de datos de salida para la actividad de streaming de Hadoop en la propiedad **outputs**. Esto es simplemente un conjunto de datos ficticio que es necesario para la programación de la canalización. No es necesario especificar ningún conjunto de datos de entrada para la actividad de la propiedad **inputs**.

	
## Ejemplo
La canalización de este tutorial ejecuta el programa de asignación/reducción de transmisión por secuencias del recuento de palabras en el clúster de HDInsight de Azure.

### Servicios vinculados

#### Servicio vinculado de almacenamiento
En primer lugar, cree un servicio vinculado para vincular el almacenamiento de Azure usado por el clúster de HDInsight de Azure con la Factoría de datos de Azure. Si copia/pega el código siguiente, no olvide reemplazar el nombre y la clave de la cuenta por el nombre y la clave de su Almacenamiento de Azure.

	{
	    "name": "StorageLinkedService",
	    "properties": {
	        "type": "AzureStorage",
	        "typeProperties": {
	            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
	        }
	    }
	}

#### Servicio vinculado de HDInsight de Azure
A continuación, cree un servicio vinculado para vincular el clúster de HDInsight de Azure con la Factoría de datos de Azure. Si copia/pegar el código siguiente, reemplace el nombre del clúster de HDInsight por el nombre de su clúster de HDInsight y cambie los valores de nombre de usuario y contraseña.
	
	{
	    "name": "HDInsightLinkedService",
	    "properties": {
	        "type": "HDInsight",
	        "typeProperties": {
	            "clusterUri": "https://<HDInsight cluster name>.azurehdinsight.net",
	            "userName": "admin",
	            "password": "**********",
	            "linkedServiceName": "StorageLinkedService"
	        }
	    }
	}

### Conjuntos de datos

#### Conjunto de datos de salida
La canalización de este ejemplo no toma ninguna entrada. Deberá especificar un conjunto de datos de salida para la actividad de transmisión por secuencias de HDInsight. Esto es simplemente un conjunto de datos ficticio que es necesario para la programación de la canalización.

	{
	    "name": "StreamingOutputDataset",
	    "properties": {
	        "published": false,
	        "type": "AzureBlob",
	        "linkedServiceName": "StorageLinkedService",
	        "typeProperties": {
	            "folderPath": "adftutorial/streamingdata/",
	            "format": {
	                "type": "TextFormat",
	                "columnDelimiter": ","
	            },
	        },
	        "availability": {
	            "frequency": "Day",
	            "interval": 1
	        }
	    }
	}

### Canalización

La canalización de este ejemplo tiene solo una actividad de tipo: **HDInsightStreaming**.

El clúster de HDInsight se rellena automáticamente con los programas de ejemplo (wc.exe y cat.exe) y los datos (davinci.txt). De forma predeterminada, el nombre del contenedor usado por el clúster de HDInsight es el nombre del propio clúster. Por ejemplo, si el nombre del clúster es myhdicluster, el nombre del contenedor de blobs asociado sería myhdicluster.

	{
	    "name": "HadoopStreamingPipeline",
	    "properties": {
	        "description": "Hadoop Streaming Demo",
	        "activities": [
	            {
	                "type": "HDInsightStreaming",
	                "typeProperties": {
	                    "mapper": "cat.exe",
	                    "reducer": "wc.exe",
	                    "input": "wasb://<blobcontainer>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
	                    "output": "wasb://<blobcontainer>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
	                    "filePaths": [
	                        "<blobcontainer>/example/apps/wc.exe",
	                        "<blobcontainer>/example/apps/cat.exe"
	                    ],
	                    "fileLinkedService": "StorageLinkedService"
	                },
	                "outputs": [
	                    {
	                        "name": "StreamingOutputDataset"
	                    }
	                ],
	                "policy": {
	                    "timeout": "01:00:00",
	                    "concurrency": 1,
	                    "executionPriorityOrder": "NewestFirst",
	                    "retry": 1
	                },
	                "scheduler": {
	                    "frequency": "Day",
	                    "interval": 1
	                },
	                "name": "RunHadoopStreamingJob",
	                "description": "Run a Hadoop streaming job",
	                "linkedServiceName": "HDInsightLinkedService"
	            }
	        ],
	        "start": "2014-01-04T00:00:00Z",
	        "end": "2014-01-05T00:00:00Z"
	    }
	}

<!---HONumber=AcomDC_1203_2015-->