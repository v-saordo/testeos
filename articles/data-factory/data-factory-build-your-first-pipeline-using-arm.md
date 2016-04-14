<properties
	pageTitle="Creación de la primera Data Factory (plantilla ARM) | Microsoft Azure"
	description="En este tutorial, creará una canalización de la factoría de datos de Azure de ejemplo con la plantilla del Administrador de recursos de Azure."
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
	ms.topic="hero-article"
	ms.date="03/03/2016"
	ms.author="spelluru"/>

# Tutorial: Compilación de la primera Data Factory de Azure con la plantilla de Azure Resource Manager
> [AZURE.SELECTOR]
- [Información general del tutorial](data-factory-build-your-first-pipeline.md)
- [Uso del Editor de Data Factory](data-factory-build-your-first-pipeline-using-editor.md).
- [Uso de PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Uso de Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [Uso de la plantilla de Resource Manager](data-factory-build-your-first-pipeline-using-arm.md)


En este artículo, aprenderá a usar una plantilla del Administrador de recursos de Azure (ARM) para crear su primera Factoría de datos de Azure.


## Requisitos previos
Además de los requisitos previos que se enumeran en el tema de información general del tutorial, debe tener instalado lo siguiente:

- **Debe** leer la sección [Tutorial Overview](data-factory-build-your-first-pipeline.md) del artículo y completar los pasos de los requisitos previos antes de continuar. 
- **Instale Azure PowerShell**. Siga las instrucciones del artículo [Cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) para instalar la última versión de Azure PowerShell en su equipo.
- Este artículo no ofrece información general conceptual sobre el servicio Factoría de datos de Azure. Para más información del servicio, consulte [Introducción al servicio Factoría de datos de Azure](data-factory-introduction.md). 
- Para más información sobre las plantillas del Administrador de recursos de Azure (ARM), consulte [Creación de plantillas del Administrador de recursos de Azure](../resource-group-authoring-templates.md). 


## Paso 1: Creación de la plantilla de ARM

Cree un archivo JSON llamado **ADFTutorialARM.json** en la carpeta **C:\\ADFGetStarted** con el siguiente contenido:

> [AZURE.IMPORTANT] Cambie los valores de las variables **storageAccountName** y **storageAccountKey**. Cambie también el valor de **dataFactoryName** porque el nombre debe ser único.

La plantilla permite crear las siguientes entidades de Factoría de datos.

1. Una **factoría de datos** denominada **TutorialDataFactoryARM**. Una factoría de datos puede tener una o más canalizaciones. Una canalización puede tener una o más actividades. Por ejemplo, una actividad de copia para copiar datos desde un origen a un almacén de datos de destino o una actividad de Hive de HDInsight para ejecutar un script de Hive que transforme los datos de entrada para generar datos de salida. 
2. Dos **servicios vinculados**: **StorageLinkedService** y **HDInsightOnDemandLinkedService**. Estos servicios permiten vincular su cuenta de Almacenamiento de Azure y un clúster de HDInsight de Azure a petición con su factoría de datos. La cuenta de Almacenamiento de Azure contendrá los datos de entrada y salida de la canalización de este ejemplo. Para ejecutar el script de Hive especificado en la actividad de la canalización en este ejemplo, se usa el servicio vinculado de HDInsight. Debe identificar qué datos de almacén o servicios de proceso se usan en el escenario y vincular dichos servicios con la factoría de datos mediante la creación de servicios vinculados. 
3. Dos **conjuntos de datos** (entrada/salida): **AzureBlobInput** y **AzureBlobOutput**. Estos conjuntos de datos representan los datos de entrada y salida para el procesamiento de Hive. Estos conjuntos de datos hacen referencia al servicio **StorageLinkedService** que ha creado anteriormente en este tutorial. El servicio vinculado apunta a una cuenta de Almacenamiento de Azure y los conjuntos de datos especifican el contenedor, la carpeta y el nombre de archivo en el almacenamiento que contiene los datos de entrada y salida.   

Haga clic en la pestaña **Utilización del Editor de la Factoría de datos** para cambiar al artículo con detalles sobre las propiedades JSON usadas en esta plantilla.

	{
	    "contentVersion": "1.0.0.0",
	    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
	    "parameters": {
	    },
	    "variables": {
	        "dataFactoryName":  "TutorialDataFactoryARM",
	        "storageAccountName":  "<stroage account name>" ,
	        "storageAccountKey":  "<storage account key>",
	        "apiVersion": "2015-10-01",
	        "storageLinkedServiceName": "StorageLinkedService",
	        "hdInsightOnDemandLinkedServiceName": "HDInsightOnDemandLinkedService",
	        "blobInputDataset": "AzureBlobInput",
	        "blobOutputDataset": "AzureBlobOutput",
	        "singleQuote": "'"
	    },
	    "resources": [
	        {
	            "name": "[variables('dataFactoryName')]",
	            "apiVersion": "[variables('apiVersion')]",
	            "type": "Microsoft.DataFactory/datafactories",
	            "location": "westus",
	            "resources": [
	                {
	                    "dependsOn": [ "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]" ],
	                    "type": "linkedservices",
	                    "name": "[variables('storageLinkedServiceName')]",
	                    "apiVersion": "[variables('apiVersion')]",
	                    "properties": {
	                        "type": "AzureStorage",
	                        "typeProperties": {
	                            "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',variables('storageAccountName'),';AccountKey=',variables('storageAccountKey'))]"
	                        }
	                    }
	                },
	                {
	                    "dependsOn": [
	                        "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]",
	                        "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/linkedservices/', variables('storageLinkedServiceName'))]"
	                    ],
	                    "type": "linkedservices",
	                    "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
	                    "apiVersion": "[variables('apiVersion')]",
	                    "properties": {
	                        "type": "HDInsightOnDemand",
	                        "typeProperties": {
	                            "version": "3.2",
	                            "clusterSize": 1,
	                            "timeToLive": "00:30:00",
	                            "linkedServiceName": "StorageLinkedService"
	                        }
	                    }
	                },
	                {
	                    "dependsOn": [
	                        "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]",
	                        "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]"
	                    ],
	                    "type": "datasets",
	                    "name": "[variables('blobInputDataset')]",
	                    "apiVersion": "[variables('apiVersion')]",
						    "properties": {
						        "type": "AzureBlob",
						        "linkedServiceName": "StoraegLinkedService",
						        "typeProperties": {
						            "fileName": "input.log",
						            "folderPath": "adfgetstarted/inputdata",
						            "format": {
						                "type": "TextFormat",
						                "columnDelimiter": ","
						            }
						        },
						        "availability": {
						            "frequency": "Month",
						            "interval": 1
						        },
						        "external": true,
						        "policy": {}
						    }
	                    },
	                {
	                    "dependsOn": [
	                        "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]",
	                        "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]"
	                    ],
	                    "type": "datasets",
	                    "name": "[variables('blobOutputDataset')]",
	                    "apiVersion": "[variables('apiVersion')]",
						    "properties": {
						        "published": false,
						        "type": "AzureBlob",
						        "linkedServiceName": "StorageLinkedService",
						        "typeProperties": {
						            "folderPath": "adfgetstarted/partitioneddata",
						            "format": {
						                "type": "TextFormat",
						                "columnDelimiter": ","
						            }
						        },
						        "availability": {
						            "frequency": "Month",
						            "interval": 1
						        }
						    }
	                    },
	                    {
	                        "dependsOn": [
	                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]",
	                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]",
	                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/linkedServices/', variables('hdInsightOnDemandLinkedServiceName'))]",
	                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/datasets/', variables('blobInputDataset'))]",
	                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/datasets/', variables('blobOutputDataset'))]"
	                        ],
	                        "type": "datapipelines",
	                        "name": "[variables('dataFactoryName')]",
	                        "apiVersion": "[variables('apiVersion')]",
						    "properties": {
						        "description": "My first Azure Data Factory pipeline",
						        "activities": [
						            {
						                "type": "HDInsightHive",
						                "typeProperties": {
						                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
						                    "scriptLinkedService": "StorageLinkedService",
						                    "defines": {
		                        				"inputtable": "[concat('wasb://adfgetstarted@', variables('storageAccountName'), '.blob.core.windows.net/inputdata')]",
		                        				"partitionedtable": "[concat('wasb://adfgetstarted@', variables('storageAccountName'), '.blob.core.windows.net/partitioneddata')]"
						                    }
						                },
						                "inputs": [
						                    {
						                        "name": "AzureBlobInput"
						                    }
						                ],
						                "outputs": [
						                    {
						                        "name": "AzureBlobOutput"
						                    }
						                ],
						                "policy": {
						                    "concurrency": 1,
						                    "retry": 3
						                },
						                "scheduler": {
						                    "frequency": "Month",
						                    "interval": 1
						                },
						                "name": "RunSampleHiveActivity",
						                "linkedServiceName": "HDInsightOnDemandLinkedService"
						            }
						        ],
						        "start": "2014-02-01T00:00:00Z",
						        "end": "2014-02-02T00:00:00Z",
						        "isPaused": false
						    }
	                    }
	            ]
	        }
	    ]
	}


Tenga en cuenta lo siguiente:

- Data Factory crea un clúster de HDInsight **basado en Windows** con el anterior código JSON. También podría hacer que cree un clúster de HDInsight **basado en Linux**. Consulte la sección [Servicio vinculado a petición de HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) para obtener más información. 
- Puede usar **su propio clúster de HDInsight** en lugar de usar un clúster de HDInsight a petición. Consulte [Servicio vinculado de HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) para obtener más información.
- El clúster de HDInsight crea un **contenedor predeterminado** en el Almacenamiento de blobs especificado en el código JSON (**linkedServiceName**). HDInsight no elimina este contenedor cuando se elimina el clúster. Esto es así por diseño. Con el servicio vinculado de HDInsight a petición, se crea un clúster de HDInsight cada vez que un segmento debe procesarse, a menos que haya un clúster existente activo (**timeToLive**), y se elimina cuando se realiza el procesamiento.

	A medida que se procesen más segmentos, verá numerosos contenedores en su Almacenamiento de blobs de Azure. Si no los necesita para solucionar problemas de trabajos, puede eliminarlos para reducir el costo de almacenamiento. El nombre de estos contenedores siguen un patrón: "adf**nombreDeFactoríaDeDatos**-**nombreDeServicioVinculado**-marcaDeFechaYHora". Use herramientas como [Explorador de almacenamiento de Microsoft](http://storageexplorer.com/) para eliminar contenedores del Almacenamiento de blobs de Azure.

Consulte la sección [Servicio vinculado a petición de HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) para obtener más información.

> [AZURE.NOTE] Puede encontrar otro ejemplo de plantilla de ARM para crear Data Factory de Azure en [Github](https://github.com/Azure/azure-quickstart-templates/blob/master/101-data-factory-blob-to-sql/azuredeploy.json).

## Paso 2: Implementación de las entidades de Factoría de datos mediante la plantilla ARM

1. Inicie Azure PowerShell y ejecute el comando siguiente. Mantenga Azure PowerShell abierto hasta el final de este tutorial. Si lo cierra y vuelve a abrirlo, deberá ejecutar los comandos de nuevo.
	- Ejecute **Login-AzureRmAccount** y escriba el mismo nombre de usuario y contraseña que utilizó para iniciar sesión en el Portal de Azure.  
	- Ejecute **Get-AzureSubscription** para ver todas las suscripciones para esta cuenta.
	- Ejecute **Select-AzureSubscription SubscriptionName** para seleccionar la suscripción con la que quiere trabajar. Esta suscripción debe ser la misma que la usada en el Portal de Azure.
1. Ejecute el comando siguiente para implementar las entidades de Factoría de datos mediante la plantilla ARM que creó en el paso 1. 

		New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile C:\ADFGetStarted\ADFTutorialARM.json

## Supervisar la canalización
 
1.	Después de iniciar sesión en el [Portal de Azure](https://portal.azure.com/), haga clic en **Examinar** y seleccione **Factorías de datos**. ![Examinar todo -> Fábricas de datos](./media/data-factory-build-your-first-pipeline-using-arm/BrowseDataFactories.png)
2.	En la hoja **Factorías de datos**, haga clic en la factoría de datos (**TutorialFactoryARM**) que creó.	
2.	En la hoja **Factoría de datos** de su factoría de datos, haga clic en **Diagrama**. ![Icono Diagrama](./media/data-factory-build-your-first-pipeline-using-arm/DiagramTile.png)
4.	En la **Vista de diagrama**, verá información general de las canalizaciones y los conjuntos de datos empleados en este tutorial.
	
	![Vista de diagrama](./media/data-factory-build-your-first-pipeline-using-arm/DiagramView.png) 
8. En la Vista de diagrama, haga doble clic en el conjunto de datos **AzureBlobOutput**. Verá que el segmento se está procesando.

	![Dataset](./media/data-factory-build-your-first-pipeline-using-arm/AzureBlobOutput.png)
9. Cuando finalice el procesamiento, el segmento aparecerá con el estado **Listo**. Tenga en cuenta que la creación de un clúster de HDInsight a petición normalmente tarda algún tiempo (20 minutos aproximadamente). 

	![Dataset](./media/data-factory-build-your-first-pipeline-using-arm/SliceReady.png)	
10. Cuando el segmento se encuentre en el estado **Listo**, busque los datos de salida en la carpeta **partitioneddata** del contenedor **adfgetstarted** de Almacenamiento de blobs.  
 

<!---HONumber=AcomDC_0309_2016-->