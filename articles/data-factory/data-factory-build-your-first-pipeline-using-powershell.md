<properties
	pageTitle="Introducción a la Factoría de datos de Azure (Azure PowerShell)"
	description="En este tutorial, creará una canalización de la factoría de datos de Azure de ejemplo con Azure PowerShell."
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
	ms.date="12/18/2015"
	ms.author="spelluru"/>

# Introducción a la Factoría de datos de Azure (Azure PowerShell)
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-build-your-first-pipeline.md)
- [Using Data Factory Editor](data-factory-build-your-first-pipeline-using-editor.md)
- [Using PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Using Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [Using Resource Manager Template](data-factory-build-your-first-pipeline-using-arm.md)


En este artículo, aprenderá a usar Azure PowerShell para crear su primera factoría de datos de Azure.


## Requisitos previos
Además de los requisitos previos que se enumeran en el tema de información general del tutorial, debe tener instalado lo siguiente:

- **Debe** leer la sección [Tutorial Overview](data-factory-build-your-first-pipeline.md) del artículo y completar los pasos de los requisitos previos antes de continuar.
- **Azure PowerShell**. Siga las instrucciones del artículo [Cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) para instalar la última versión de Azure PowerShell en su equipo.
- (opcional) Este artículo no abarca todos los cmdlets de Factoría de datos. Vea [Referencia de cmdlets de factoría de datos](https://msdn.microsoft.com/library/dn820234.aspx) para obtener la documentación completa sobre los cmdlets de la factoría de datos. 

Si usa Azure PowerShell de una **versión inferior a 1.0**, deberá usar los cmdlets que se documentan [aquí][cmdlet-reference]. También debe ejecutar los comandos siguientes antes de usar los cmdlets de Factoría de datos:
 
1. Inicie Azure PowerShell y ejecute los comandos siguientes. Mantenga Azure PowerShell abierto hasta el final de este tutorial. Si lo cierra y vuelve a abrirlo, deberá ejecutar los comandos de nuevo.
	1. Ejecute **Add-AzureAccount** y escriba el mismo nombre de usuario y contraseña que utilizó para iniciar sesión en el Portal de Azure.
	2. Ejecute **Get-AzureSubscription** para ver todas las suscripciones para esta cuenta.
	3. Ejecute **Select-AzureSubscription** para seleccionar la suscripción con la que quiere trabajar. Esta suscripción debe ser la misma que la usada en el Portal de Azure.
4. Cambie al modo AzureResourceManager a medida que los cmdlets de Factoría de datos de Azure están disponibles en este modo: **Switch-AzureMode AzureResourceManager**.


## Paso 1: Creación de la factoría de datos

En este paso, utilice Azure PowerShell para crear una Factoría de datos de Azure llamada **FirstDataFactoryPSH**. Una factoría de datos puede tener una o más canalizaciones. Una canalización puede tener una o más actividades. Por ejemplo, una actividad de copia para copiar datos desde un origen a un almacén de datos de destino o una actividad de Hive de HDInsight para ejecutar un script de Hive que transforme los datos de entrada para generar datos de salida. Comencemos con la creación de la factoría de datos en este paso.

1. Inicie Azure PowerShell y ejecute el comando siguiente. Mantenga Azure PowerShell abierto hasta el final de este tutorial. Si lo cierra y vuelve a abrirlo, deberá ejecutar los comandos de nuevo.
	- Ejecute **Login-AzureRmAccount** y escriba el mismo nombre de usuario y contraseña que utilizó para iniciar sesión en el Portal de Azure.  
	- Ejecute **Get-AzureSubscription** para ver todas las suscripciones para esta cuenta.
	- Ejecute **Select-AzureSubscription<Name of the subscription>** para seleccionar la suscripción con la que quiere trabajar. Esta suscripción debe ser la misma que la usada en el Portal de Azure.
3. Cree un grupo de recursos de Azure con el nombre: **ADFTutorialResourceGroup** ejecutando el siguiente comando.

		New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

	En algunos de los pasos de este tutorial se supone que se usa el grupo de recursos denominado ADFTutorialResourceGroup. Si usa un grupo de recursos diferentes, deberá usarlo en lugar de ADFTutorialResourceGroup en este tutorial.
4. Ejecute el cmdlet **New-AzureRmDataFactory** para crear una factoría de datos con el nombre: **FirstDataFactoryPSH**.  

		New-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name FirstDataFactoryPSH –Location "West US"

	> [AZURE.IMPORTANT] El nombre de la Factoría de datos de Azure debe ser único de forma global. Si recibe el error: **El nombre de la factoría de datos "FirstDataFactoryPSH" no está disponible**, cambie el nombre (por ejemplo, suNombreFirstDataFactoryPSH). Use este nombre en lugar de ADFTutorialFactoryPSH mientras lleva a cabo los pasos de este tutorial. Consulte el tema [Factoría de datos: reglas de nomenclatura](data-factory-naming-rules.md) para las reglas de nomenclatura para los artefactos de Factoría de datos.
	> 
	> El nombre de la factoría de datos se puede registrar como un nombre DNS en el futuro y, por lo tanto, hacerse públicamente visible.

Antes de crear una canalización, debe crear algunas entidades de factoría de datos primero. En primer lugar debe crear servicios vinculados para vincular los almacenes de datos y procesos con su almacén de datos, a continuación, definir los conjuntos de datos de entrada y salida para representar los datos en los almacenes de datos vinculados y, finalmente, crear la canalización con una actividad que utilice estos conjuntos de datos.

## Paso 2: Creación de servicios vinculados 
En este paso, vinculará su cuenta de Almacenamiento de Azure y el clúster de HDInsight de Azure a petición con su factoría de datos. La cuenta de Almacenamiento de Azure contendrá los datos de entrada y salida de la canalización de este ejemplo. Para ejecutar el script de Hive especificado en la actividad de la canalización en este ejemplo, se usa el servicio vinculado de HDInsight. Debe identificar qué datos de almacén o servicios de proceso se usan en el escenario y vincular dichos servicios con la factoría de datos mediante la creación de servicios vinculados.

### Creación de un servicio vinculado de Almacenamiento de Azure
En este paso, vinculará su cuenta de Almacenamiento de Azure con su factoría de datos. Para este tutorial, usará la misma cuenta de Almacenamiento de Azure para almacenar los datos de entrada y salida y el archivo de script de HQL.

1. Cree un archivo JSON con el nombre StorageLinkedService.json en la carpeta C:\\ADFGetStarted con el siguiente contenido. Si todavía no existe, cree la carpeta ADFGetStarted.

		{
	    	"name": "StorageLinkedService",
	    	"properties": {
	        	"type": "AzureStorage",
	        	"description": "",
	        	"typeProperties": {
	            	"connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	        	}
	    	}
		}

	Reemplace **account name** por el nombre de la cuenta de Almacenamiento de Azure y **account key** por la clave de acceso de la cuenta de Almacenamiento de Azure. Para aprender a obtener una clave de acceso de almacenamiento, consulte [Visualización y copia de las claves de acceso de almacenamiento](https://azure.microsoft.com/documentation/articles/storage-create-storage-account/#view-copy-and-regenerate-storage-access-keys).

2. En Azure PowerShell, cambie a la carpeta ADFGetStarted.
3. Use el cmdlet **New-AzureRmDataFactoryLinkedService** para crear un servicio vinculado. Este cmdlet y otros cmdlets de Factoría de datos que usa en este tutorial requieren que pase los valores para los parámetros *ResourceGroupName* y *DataFactoryName*. Como alternativa, puede usar **Get-AzureRmDataFactory** para obtener un objeto **DataFactory** y pasarlo sin necesidad de escribir *ResourceGroupName* y *DataFactoryName* cada vez que ejecuta un cmdlet. Ejecute el comando siguiente para asignar el resultado del cmdlet **Get-AzureRmDataFactory** a una variable **$df**.

		$df=Get-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name FirstDataFactoryPSH

4. Ahora, ejecute el cmdlet **New-AzureRmDataFactoryLinkedService** para crear el servicio vinculado **StorageLinkedService**.

		New-AzureRmDataFactoryLinkedService $df -File .\StorageLinkedService.json

	Si no hubiera ejecutado el cmdlet **Get-AzureRmDataFactory** y asignado la salida a la variable **$df**, tendría que especificar valores para los parámetros *ResourceGroupName* y *DataFactoryName* de la siguiente forma.

		New-AzureRmDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName FirstDataFactoryPSH -File .\StorageLinkedService.json

	Si cierra Azure PowerShell en mitad del tutorial, tendrá que ejecutar el cmdlet **Get-AzureRmDataFactory** la próxima vez que inicie Azure PowerShell para completar el tutorial.

### Creación de un servicio vinculado de HDInsight de Azure
En este paso, vinculará un clúster de HDInsight a petición con la factoría de datos. El clúster de HDInsight se crea automáticamente en tiempo de ejecución y se elimina después de hacer el procesamiento y de permanecer inactivo durante el período de tiempo especificado. Puede usar su propio clúster de HDInsight en lugar de usar un clúster de HDInsight a petición. Consulte [Servicios vinculados de procesos](data-factory-compute-linked-services.md) para más información.

1. Cree un archivo JSON con el nombre **HDInsightOnDemandLinkedService**.json en la carpeta **C:\\ADFGetStarted** con el siguiente contenido.

		{
		  "name": "HDInsightOnDemandLinkedService",
		  "properties": {
		    "type": "HDInsightOnDemand",
		    "typeProperties": {
		      "version": "3.2",
		      "clusterSize": 1,
		      "timeToLive": "00:30:00",
		      "linkedServiceName": "StorageLinkedService"
		    }
		  }
		}

	En la siguiente tabla se ofrecen descripciones de las propiedades JSON que se usan en el fragmento de código:

	| Propiedad | Descripción |
	| :------- | :---------- |
	| Versión | Con esto se especifica que la versión de HDInsight se crea para que sea 3.2. | 
	| ClusterSize | Así se crea un clúster de HDInsight de un nodo. | 
	| TimeToLive | Especifica el tiempo de inactividad del clúster de HDInsight, antes de que se elimine. |
	| linkedServiceName | Especifica la cuenta de almacenamiento que se usará para almacenar los registros que genere HDInsight. |

	Tenga en cuenta lo siguiente:
	
	- Factoría de datos crea automáticamente un clúster de HDInsight **basado en Windows** con el anterior código JSON. También podría hacer que cree un clúster de HDInsight **basado en Linux**. Consulte la sección [Servicio vinculado a petición de HDInsight de Azure](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) para más información. 
	- Puede usar **su propio clúster de HDInsight** en lugar de usar un clúster de HDInsight a petición. Consulte [Servicio vinculado de HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) para más información.
	- El clúster de HDInsight crea un **contenedor predeterminado** en Almacenamiento de blobs especificado en el código JSON (**linkedServiceName**). HDInsight elimina este contenedor cuando se elimina el clúster. Esto es así por diseño. Con el servicio vinculado de HDInsight a petición, se crea un clúster de HDInsight cada vez que un segmento debe procesarse, a menos que haya un clúster existente activo (**timeToLive**), y se elimina cuando se realiza el procesamiento.
	
		A medida que se procesan más segmentos, verá numerosos contenedores en su Almacenamiento de blobs de Azure. Si no los necesita para solucionar problemas de trabajos, puede eliminarlos para reducir el costo de almacenamiento. El nombre de estos contenedores siguen un patrón: "adf**nombreDeFactoríaDeDatos**-**linkedservicename**-datetimestamp". Use herramientas como [Explorador de almacenamiento de Microsoft](http://storageexplorer.com/) para eliminar contenedores de Almacenamiento de blobs de Azure.

	Consulte la sección [Servicio vinculado a petición de HDInsight de Azure](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) para más información. 
2. Ejecute el cmdlet **New-AzureRmDataFactoryLinkedService** para crear el servicio vinculado llamado HDInsightOnDemandLinkedService.

		New-AzureRmDataFactoryLinkedService $df -File .\HDInsightOnDemandLinkedService.json


## Paso 3: Creación de conjuntos de datos
En este paso, creará conjuntos de datos que representen los datos de entrada y salida para el procesamiento de Hive. Estos conjuntos de datos hacen referencia al servicio **StorageLinkedService** que ha creado anteriormente en este tutorial. El servicio vinculado apunta a una cuenta de Almacenamiento de Azure y los conjuntos de datos especifican el contenedor, la carpeta y el nombre de archivo en el almacenamiento que contiene los datos de entrada y salida.

### Creación del conjunto de datos de entrada
1. Cree un archivo JSON con el nombre **InputTable.json** en la carpeta **C:\\ADFGetStarted** con el siguiente contenido:

		{
			"name": "AzureBlobInput",
		    "properties": {
		        "type": "AzureBlob",
		        "linkedServiceName": "StorageLinkedService",
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
		} 

	El código JSON anterior define un conjunto de datos denominado **AzureBlobInput**, que representa los datos de entrada de una actividad en la canalización. Además, especifica que los datos de entrada se deben colocar en el contenedor de blobs **adfgetstarted** y en la carpeta **inputdata**.

	En la siguiente tabla se ofrecen descripciones de las propiedades JSON que se usan en el fragmento de código:

	| Propiedad | Descripción |
	| :------- | :---------- |
	| type | La propiedad type se establece en AzureBlob porque los datos residen en Almacenamiento de blobs de Azure. |  
	| linkedServiceName | hace referencia a StorageLinkedService que creó anteriormente. |
	| fileName | Esta propiedad es opcional. Si omite esta propiedad, se seleccionan todos los archivos de folderPath. En este caso, se procesa solo el archivo input.log. |
	| type | Los archivos de registro están en formato de texto, por tanto, usaremos TextFormat. | 
	| columnDelimiter | Las columnas de los archivos de registro están delimitadas por , (coma). |
	| frecuencia/intervalo | La frecuencia está establecida en Mes y el intervalo es 1, lo que significa que los segmentos de entrada estarán disponibles cada mes. | 
	| external | Esta propiedad se establece en true si el servicio Factoría de datos no ha generado los datos de entrada. | 

2. Ejecute el comando siguiente en Azure PowerShell para crear el conjunto de datos Factoría de datos.

		New-AzureRmDataFactoryDataset $df -File .\InputTable.json

### Creación del conjunto de datos de salida
Ahora, va a crear el conjunto de datos de salida que representa los datos de salida almacenados en Almacenamiento de blobs de Azure.

1. Cree un archivo JSON con el nombre **OutputTable.json** en la carpeta **C:\\ADFGetStarted** con el siguiente contenido:

		{
		  "name": "AzureBlobOutput",
		  "properties": {
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
		}

	El código JSON anterior define un conjunto de datos denominado **AzureBlobOutput**, que representa los datos de salida de una actividad en la canalización. Además, especifica que los resultados se deben almacenar en el contenedor de blobs **adfgetstarted** y en la carpeta **partitioneddata**. La sección **availability** especifica que el conjunto de datos de salida se genera mensualmente.

2. Ejecute el comando siguiente en Azure PowerShell para crear el conjunto de datos Factoría de datos.

		New-AzureRmDataFactoryDataset $df -File .\OutputTable.json

## Paso 3: Creación de la primera canalización
En este paso, creará la primera canalización con una actividad **HDInsightHive**. Tenga en cuenta que el segmento de entrada está disponible mensualmente (frecuencia: mes, intervalo: 1), el segmento de salida se genera mensualmente y la propiedad de programador también se establece en mensual para la actividad (consulte a continuación). La configuración del conjunto de datos de salida y la del programador de la actividad deben coincidir. En este momento, el conjunto de datos de salida es lo que impulsa la programación, por lo que debe crear un conjunto de datos de salida incluso si la actividad no genera ninguna salida. Si la actividad no toma ninguna entrada, puede omitir la creación del conjunto de datos de entrada. Al final de esta sección se explican las propiedades usadas en el siguiente JSON.


1. Cree un archivo JSON con el nombre MyFirstPipelinePSH.json en la carpeta C:\\ADFGetStarted con el siguiente contenido:

	> [AZURE.IMPORTANT] Reemplace **storageaccountname** por el nombre de la cuenta de almacenamiento en JSON.
		
		{
		    "name": "MyFirstPipeline",
		    "properties": {
		        "description": "My first Azure Data Factory pipeline",
		        "activities": [
		            {
		                "type": "HDInsightHive",
		                "typeProperties": {
		                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
		                    "scriptLinkedService": "StorageLinkedService",
		                    "defines": {
		                        "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
		                        "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"
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

	En el fragmento de código JSON, se crea una canalización que consta de una sola actividad que usa Hive para procesar los datos en un clúster de HDInsight.
	
	El archivo de script de Hive, **partitionweblogs.hql**, se almacena en la cuenta de Almacenamiento de Azure (especificada mediante la propiedad scriptLinkedService, llamada **StorageLinkedService**) en una carpeta denominada **script** del contenedor **adfgetstarted**.

	La sección **defines** se usa para especificar la configuración de tiempo de ejecución que se pasará al script de Hive como valores de configuración de Hive (por ejemplo, ${hiveconf:inputtable}, ${hiveconf:partitionedtable}).

	Las propiedades **start** y **end** de la canalización especifican el período activo de esta.

	En el código JSON de la actividad, se especifica que el script de Hive se ejecuta en el proceso que especifica el servicio vinculado **linkedServiceName**: **HDInsightOnDemandLinkedService**.
2.  Confirme que el archivo **input.log** aparece en la carpeta **adfgetstarted/inputdata** en el Almacenamiento de blobs de Azure y ejecute el siguiente comando para implementar la canalización. Dado que las horas de **inicio** y **fin** están establecidas en el pasado y **isPaused** está establecido en false, la canalización (actividad en la canalización) se ejecutará inmediatamente después de implementar. 

		New-AzureRmDataFactoryPipeline $df -File .\MyFirstPipelinePSH.json
5. Enhorabuena, ya creó correctamente su primera canalización con Azure PowerShell.

### <a name="MonitorDataSetsAndPipeline"></a> Supervisión de los conjuntos de datos y la canalización
En este paso, se usará Azure PowerShell para supervisar lo que está ocurriendo en una factoría de datos de Azure.

1. Ejecute **Get-AzureRmDataFactory** y asigne el resultado a la variable **$df**.

		$df=Get-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name FirstDataFactoryPSH

2. Ejecute **Get-AzureRmDataFactorySlice** para obtener la información sobre todos los segmentos de **EmpSQLTable**, que es la tabla de salida de la canalización.

		Get-AzureRmDataFactorySlice $df -DatasetName AzureBlobOutput -StartDateTime 2014-02-01

	Observe que la StartDateTime que especifique aquí es la misma hora de inicio especificada en el JSON de la canalización. Debería ver una salida similar a la siguiente.

		ResourceGroupName : ADFTutorialResourceGroup
		DataFactoryName   : FirstDataFactoryPSH
		DatasetName       : AzureBlobOutput
		Start             : 2/1/2014 12:00:00 AM
		End               : 3/1/2014 12:00:00 AM
		RetryCount        : 0
		State             : InProgress
		SubState          :
		LatencyStatus     :
		LongRetryCount    : 0


3. Ejecute **Get-AzureRmDataFactoryRun** para más información de la actividad que se ejecuta para un segmento específico.

		Get-AzureRmDataFactoryRun $df -DatasetName AzureBlobOutput -StartDateTime 2014-02-01

	Debería ver una salida similar a la siguiente.
		
		Id                  : 0f6334f2-d56c-4d48-b427-d4f0fb4ef883_635268096000000000_635292288000000000_AzureBlobOutput
		ResourceGroupName   : ADFTutorialResourceGroup
		DataFactoryName     : FirstDataFactoryPSH
		DatasetName         : AzureBlobOutput
		ProcessingStartTime : 12/18/2015 4:50:33 AM
		ProcessingEndTime   : 12/31/9999 11:59:59 PM
		PercentComplete     : 0
		DataSliceStart      : 2/1/2014 12:00:00 AM
		DataSliceEnd        : 3/1/2014 12:00:00 AM
		Status              : AllocatingResources
		Timestamp           : 12/18/2015 4:50:33 AM
		RetryAttempt        : 0
		Properties          : {}
		ErrorMessage        :
		ActivityName        : RunSampleHiveActivity
		PipelineName        : MyFirstPipeline
		Type                : Script

	Puede seguir ejecutando este cmdlet hasta que vea que el segmento se encuentra en el estado **Listo** o **Con error**. Cuando el segmento se encuentre en el estado Listo, busque los datos de salida en la carpeta **partitioneddata** del contenedor **adfgetstarted** de Almacenamiento de blobs. Tenga en cuenta que la creación de un clúster de HDInsight a petición normalmente requiere algo de tiempo.

	![datos de salida](./media/data-factory-build-your-first-pipeline-using-powershell/three-ouptut-files.png)
## Pasos siguientes
En este artículo, creó una canalización con una actividad de transformación (actividad de HDInsight) que ejecuta un script de Hive en un clúster de HDInsight de Azure a petición. Para ver cómo se usa una actividad de copia para copiar datos de un blob de Azure en SQL Azure, consulte [Tutorial: Copia de datos de un blob de Azure a SQL Azure](./data-factory-get-started.md).

### Referencias
| Tema. | Descripción |
| :---- | :---- |
| [Referencia para cmdlets de Factoría de datos](https://msdn.microsoft.com/library/azure/dn820234.aspx) | Consulte la documentación completa sobre los cmdlets de Factoría de datos |
| [Procesos](data-factory-create-pipelines.md) | Este artículo ayuda a comprender las canalizaciones y actividades en Factoría de datos de Azure y cómo aprovecharlas para construir flujos de trabajo de extremo a extremo controlados por datos para su escenario o empresa. |
| [Conjuntos de datos](data-factory-create-datasets.md) | Este artículo le ayudará a comprender los conjuntos de datos en Factoría de datos de Azure.
| [Programación y ejecución](data-factory-scheduling-and-execution.md) | En este artículo se explican los aspectos de programación y ejecución del modelo de aplicación de Factoría de datos de Azure. |
| [Supervisión y administración de canalizaciones](data-factory-monitor-manage-pipelines.md) | En este artículo se describe cómo supervisar, administrar y depurar las canalizaciones. También se ofrece información sobre cómo crear alertas y recibir notificaciones cuando se produzcan errores. |

[cmdlet-reference]: https://msdn.microsoft.com/library/azure/dn820234(v=azure.98).aspx

<!---HONumber=AcomDC_0128_2016-->