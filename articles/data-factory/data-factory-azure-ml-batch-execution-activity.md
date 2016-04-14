<properties 
	pageTitle="Creación de canalizaciones predictivas con la actividad Ejecución de lotes de Aprendizaje automático de Azure | Microsoft Azure" 
	description="Describe cómo crear canalizaciones predictivas con Factoría de datos de Azure y Aprendizaje automático de Azure" 
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
	ms.date="02/06/2016" 
	ms.author="spelluru"/>

# Creación de canalizaciones predictivas con las actividades de Aprendizaje automático de Azure   
## Información general

> [AZURE.NOTE] Consulte los artículos [Introducción a la Factoría de datos de Azure  
](data-factory-introduction.md) y [Creación de su primera canalización](data-factory-build-your-first-pipeline.md) para empezar a trabajar rápidamente con el servicio Factoría de datos de Azure.

## Introducción

[Aprendizaje automático de Azure](https://azure.microsoft.com/documentation/services/machine-learning/) permite compilar, probar e implementar soluciones de análisis predictivo. Desde una perspectiva general, esto se realiza en tres pasos:

1. **Crear un experimento de entrenamiento**. Para ello use Estudio de aprendizaje automático de Azure que es un entorno de desarrollo visual de colaboración que se emplea para entrenar y probar un modelo de análisis predictivo con los datos de entrenamiento que le proporcione.
2. **Convertirlo en un experimento predictivo**. Una vez que el modelo se ha entrenado con datos existentes y está listo para usarlo para puntuar nuevos datos, debe preparar y simplificar el experimento para la puntuación.
3. **Implementarlo como un servicio web**. Puede publicar el experimento de puntuación como un servicio web de Azure. Los usuarios pueden enviar datos al modelo a través de este punto de conexión de servicio web y recibir las predicciones de resultado para el modelo.  

Factoría de datos de Azure permite crear fácilmente canalizaciones que aprovechan un servicio web de [Aprendizaje automático de Azure][azure-machine-learning] publicado para realizar análisis predictivos. Mediante la **actividad de ejecución de lotes** en una canalización de Factoría de datos de Azure, puede invocar un servicio web de Aprendizaje automático de Azure para realizar predicciones sobre los datos en el lote. Consulte la sección [Invocación de un servicio web de Aprendizaje automático de Azure mediante la actividad de ejecución de lotes](#invoking-an-azure-ml-web-service-using-the-batch-execution-activity) para obtener detalles.

Pasado algún tiempo, los modelos predictivos en los experimentos de puntuación de Aprendizaje automático de Azure tienen que volver a entrenarse con nuevos conjuntos de datos de entrada. Puede volver a entrenar un modelo de Aprendizaje automático de Azure de una canalización de Factoría de datos realizando los pasos siguientes:

1. Publicar el experimento de entrenamiento (experimento no predictivo) como un servicio web. Tiene que hacerlo en Estudio de aprendizaje automático de Azure, tal como hizo para exponer el experimento predictivo como un servicio web en el escenario anterior.
2. Usar la actividad de ejecución de lotes de Aprendizaje automático de Azure para invocar el servicio web para el experimento de entrenamiento. Básicamente, puede emplear la actividad de ejecución de lotes de Aprendizaje automático de Azure para invocar el servicio web de aprendizaje y el servicio web de puntuación. 
  
Después de terminar el entrenamiento, tiene que actualizar el servicio web de puntuación (experimento predictivo expuesto como un servicio web) con el modelo recién entrenado. Para ello, siga estos pasos:

1. Agregar un punto de conexión no predeterminado al servicio web de puntuación. No se puede actualizar el punto de conexión predeterminado del servicio web, por lo que tendrá que crear un nuevo punto de conexión no predeterminado mediante el Portal de Azure clásico. Consulte el artículo [Creación de puntos de conexión](../machine-learning/machine-learning-create-endpoint.md) para obtener información sobre los conceptos y procedimientos relacionados con el tema.
2. Actualizar los servicios vinculados ya existentes de Aprendizaje automático de Azure para puntuar para que usen el punto de conexión no predeterminado. Debe empezar a emplear el nuevo punto de conexión para usar el servicio web que está actualizado.
3. Use la **Actividad de recursos de actualización de Aprendizaje automático de Azure** para actualizar el servicio web con el modelo recién entrenado.  

Para obtener más detalles consulte la sección [Actualización de modelos de Aprendizaje automático de Azure mediante la actividad de recursos de actualización](#updating-azure-ml-models-using-the-update-resource-activity).

## Invocación de un servicio web de Aprendizaje automático de Azure mediante la actividad de ejecución de lotes

Factoría de datos de Azure se usa para coordinar el procesamiento y el movimiento de datos y, posteriormente, realizar la ejecución por lotes mediante Aprendizaje automático de Azure. Para ello, deberá hacer lo siguiente:

1. Crear un servicio vinculado de Aprendizaje automático de Azure. Necesitará lo siguiente:
	1. **URI de solicitud** para la API Ejecución de lotes. Para encontrar el URI de solicitud, haga clic en el vínculo **EJECUCIÓN DE LOTES** en la página de servicios web (que se muestra a continuación).
	1. **Clave de API** para el servicio web de Aprendizaje automático de Azure publicado. Para encontrar la clave de API, haga clic en el servicio web que ha publicado. 
 2. Use la actividad **AzureMLBatchExecution**.

	![Panel de aprendizaje automático](./media/data-factory-azure-ml-batch-execution-activity/AzureMLDashboard.png)

	![URI del lote](./media/data-factory-azure-ml-batch-execution-activity/batch-uri.png)


### Escenario: Experimentos mediante entradas y salidas de servicios web que hacen referencia a datos de Almacenamiento de blobs de Azure
En este escenario, el servicio web de Aprendizaje automático de Azure realiza predicciones mediante datos de un archivo de un almacenamiento de blobs de Azure y almacena los resultados de predicción en el almacenamiento de blobs. El siguiente JSON define una canalización de Factoría de datos de Azure con una actividad AzureMLBatchExecution. La actividad tiene el conjunto de datos **DecisionTreeInputBlob** como entrada y **DecisionTreeResultBlob** como salida. **DecisionTreeInputBlob** se pasa como entrada al servicio web mediante la propiedad JSON **webServiceInput** y **DecisionTreeResultBlob** se pasa como salida al servicio web mediante la propiedad JSON **webServiceOutputs**.

> [AZURE.NOTE] Los conjuntos de datos a los que hacen referencia las propiedades **webServiceInput** y **webServiceOutputs** (en **typeProperties**) también se deben incluir en las **entradas** y las **salidas** de la actividad.


	{
	  "name": "PredictivePipeline",
	  "properties": {
	    "description": "use AzureML model",
	    "activities": [
	      {
	        "name": "MLActivity",
	        "type": "AzureMLBatchExecution",
	        "description": "prediction analysis on batch input",
	        "inputs": [
	          {
	            "name": "DecisionTreeInputBlob"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "DecisionTreeResultBlob"
	          }
	        ],
	        "linkedServiceName": "MyAzureMLLinkedService",
            "typeProperties":
            {
                "webServiceInput": "DecisionTreeInputBlob ",
                "webServiceOutputs": {
                    "output1": "DecisionTreeResultBlob "
                }                
            },
	        "policy": {
	          "concurrency": 3,
	          "executionPriorityOrder": "NewestFirst",
	          "retry": 1,
	          "timeout": "02:00:00"
	        }
	      }
	    ],
	    "start": "2015-02-13T00:00:00Z",
	    "end": "2015-02-14T00:00:00Z"
	  }
	}

> [AZURE.NOTE] Solo las entradas y salidas de la actividad AzureMLBatchExecution pueden pasarse como parámetros al servicio web. Por ejemplo, en el fragmento JSON anterior, DecisionTreeInputBlob es una entrada a la actividad AzureMLBatchExecution, que se pasa como entrada al servicio web mediante el parámetro webServiceInput.

### Ejemplo

Este ejemplo utiliza Almacenamiento de Azure para almacenar los datos de entrada y salida.

Antes de continuar con este ejemplo y usar el Editor de Factoría de datos para crear artefactos de Factoría de datos (servicios vinculados, conjuntos de datos, canalizaciones), se recomienda que siga el tutorial [Introducción a la Factoría de datos de Azure][adf-build-1st-pipeline].
 

1. Cree un **servicio vinculado** para su **Almacenamiento de Azure**. Si los archivos de entrada y salida van a estar en diferentes cuentas de almacenamiento, necesitará dos servicios vinculados. Este es un ejemplo de JSON:

		{
		  "name": "StorageLinkedService",
		  "properties": {
		    "type": "AzureStorage",
		    "typeProperties": {
		      "connectionString": "DefaultEndpointsProtocol=https;AccountName=[acctName];AccountKey=[acctKey]"
		    }
		  }
		}

2. Cree el **conjunto de datos** de **entrada** de Factoría de datos de Azure. Tenga en cuenta que a diferencia de otros conjuntos de datos de Factoría de datos, estos deben contener tanto los valores **folderPath** como **fileName**. Puede utilizar la creación de particiones para hacer que cada ejecución de lotes (cada segmento de datos) se procese o produzca archivos de entrada y salida únicos. Probablemente necesitará incluir alguna actividad ascendente para transformar la entrada en el formato de archivo CSV y colocarlo en la cuenta de almacenamiento para cada segmento. En ese caso, no incluiría la configuración de **external** y **externalData** que se muestra en el ejemplo siguiente y su DecisionTreeInputBlob sería el conjunto de datos de salida de una actividad diferente.

		{
		  "name": "DecisionTreeInputBlob",
		  "properties": {
		    "type": "AzureBlob",
		    "linkedServiceName": "StorageLinkedService",
		    "typeProperties": {
		      "folderPath": "azuremltesting/input",
		      "fileName": "in.csv",
		      "format": {
		        "type": "TextFormat",
		        "columnDelimiter": ","
		      }
		    },
		    "external": true,
		    "availability": {
		      "frequency": "Day",
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
	
	Su archivo CSV de entrada debe tener la fila de encabezado de columna. Si está usando la **copia de actividad** para crear/mover el CSV en el Almacenamiento de blog, debe establecer la propiedad de receptor **blobWriterAddHeader** en **true**. Por ejemplo:
	
	     sink: 
	     {
	         "type": "BlobSink",     
	         "blobWriterAddHeader": true 
	     }
	 
	Si el archivo CSV no tiene la fila de encabezado, es posible que vea el siguiente error: **Error en actividad: error de lectura de la cadena. Token inesperado: StartObject. Path '', line 1, position 1**.
3. Cree el **conjunto de datos** de **salida** de Factoría de datos de Azure. En este ejemplo se usa la creación de particiones para crear una ruta de acceso de salida única para cada ejecución de segmento. Sin esto, la actividad sobrescribiría el archivo.

		{
		  "name": "DecisionTreeResultBlob",
		  "properties": {
		    "type": "AzureBlob",
		    "linkedServiceName": "StorageLinkedService",
		    "typeProperties": {
		      "folderPath": "azuremltesting/scored/{folderpart}/",
		      "fileName": "{filepart}result.csv",
		      "partitionedBy": [
		        {
		          "name": "folderpart",
		          "value": {
		            "type": "DateTime",
		            "date": "SliceStart",
		            "format": "yyyyMMdd"
		          }
		        },
		        {
		          "name": "filepart",
		          "value": {
		            "type": "DateTime",
		            "date": "SliceStart",
		            "format": "HHmmss"
		          }
		        }
		      ],
		      "format": {
		        "type": "TextFormat",
		        "columnDelimiter": ","
		      }
		    },
		    "availability": {
		      "frequency": "Day",
		      "interval": 15
		    }
		  }
		}

4. Cree un **servicio vinculado** de tipo **AzureMLLinkedService**; para ello, proporcione la clave de API y la URL de ejecución de lotes.
		
		{
		  "name": "MyAzureMLLinkedService",
		  "properties": {
		    "type": "AzureML",
		    "typeProperties": {
		      "mlEndpoint": "https://[batch execution endpoint]/jobs",
		      "apiKey": "[apikey]"
		    }
		  }
		}
5. Por último, cree una canalización que contenga una actividad **AzureMLBatchExecution**. Obtendrá la ubicación del archivo de entrada de los conjuntos de datos de entrada, llamará a la API de ejecución de lotes de Aprendizaje automático de Azure y copiará la salida de ejecución de lotes en el blob proporcionado en el conjunto de datos de salida. 

	> [AZURE.NOTE] La actividad AzureMLBatchExecution puede tener cero o más entradas y una o más salidas.

		{
		  "name": "PredictivePipeline",
		  "properties": {
		    "description": "use AzureML model",
		    "activities": [
		      {
		        "name": "MLActivity",
		        "type": "AzureMLBatchExecution",
		        "description": "prediction analysis on batch input",
		        "inputs": [
		          {
		            "name": "DecisionTreeInputBlob"
		          }
		        ],
		        "outputs": [
		          {
		            "name": "DecisionTreeResultBlob"
		          }
		        ],
		        "linkedServiceName": "MyAzureMLLinkedService",
                "typeProperties":
                {
                    "webServiceInput": "DecisionTreeInputBlob ",
                    "webServiceOutputs": {
                        "output1": "DecisionTreeResultBlob "
                    }                
                },
		        "policy": {
		          "concurrency": 3,
		          "executionPriorityOrder": "NewestFirst",
		          "retry": 1,
		          "timeout": "02:00:00"
		        }
		      }
		    ],
		    "start": "2015-02-13T00:00:00Z",
		    "end": "2015-02-14T00:00:00Z"
		  }
		}

	Las fechas y horas de **inicio** y **finalización** deben estar en [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por ejemplo: 2014-10-14T16:32:41Z. La hora de **finalización** es opcional. Si no especifica ningún valor para la propiedad **end**, se calcula como "**start + 48 horas**". Para ejecutar la canalización indefinidamente, especifique **9999-09-09** como valor de propiedad **end**. Para obtener más información sobre las propiedades JSON, vea [Referencia de scripting JSON](https://msdn.microsoft.com/library/dn835050.aspx).

	> [AZURE.NOTE] La especificación de la entrada para la actividad AzureMLBatchExecution es opcional.

### Escenario: Experimentos mediante módulos Lector y Escritor para hacer referencia a datos de varios almacenamientos

Otro escenario común al crear experimentos de Aprendizaje automático de Azure es usar módulos Lector y Escritor. El módulo lector se usa para cargar datos en un experimento y el módulo escritor para guardar los datos de los experimentos. Para obtener información sobre los módulos lector y escritor, consulte los temas [Lector](https://msdn.microsoft.com/library/azure/dn905997.aspx) y [Escritor](https://msdn.microsoft.com/library/azure/dn905984.aspx) en MSDN Library.

Al usar los módulos lector y escritor, es recomendable emplear un parámetro de servicio web para cada propiedad de estos módulos. Estos parámetros web permiten configurar los valores en tiempo de ejecución. Por ejemplo, podría crear un experimento con un módulo lector que usa una base de datos SQL de Azure: XXX.database.windows.net. Una vez implementado el servicio web, quiere habilitar los consumidores del servicio web con el fin de especificar otro servidor Azure SQL Server denominado YYY.database.windows.net. Puede usar un parámetro de servicio web para permitir que se configure este valor.

> [AZURE.NOTE] Las entradas y salidas de servicio web son diferentes de los parámetros de servicio web. En el primer escenario, ha visto cómo pueden especificarse una entrada y una salida para un servicio web de Aprendizaje automático de Azure. En este escenario, pasará parámetros para un servicio web que corresponden a las propiedades de los módulos lector y escritor.

Echemos un vistazo a un escenario de uso de parámetros de servicio web. Tiene un servicio web implementado de Aprendizaje automático de Azure que usa un módulo lector para leer datos de uno de los orígenes de datos de Aprendizaje automático de Azure admitidos (por ejemplo: Base de datos SQL de Azure). Después de realizar la ejecución de lotes, los resultados se escriben con un módulo escritor (Base de datos SQL de Azure). No hay entradas ni salidas de servicio web definidas en los experimentos. En este caso, se recomienda que configure los parámetros de servicio web relevantes para los módulos lector y escritor. De esta forma, se podrán configurar los módulos lector y escritor cuando se use la actividad AzureMLBatchExecution. Los parámetros de servicio web se especifican en la sección **globalParameters** de la actividad JSON como se indica a continuación.


	"typeProperties": {
		"globalParameters": {
			"Param 1": "Value 1",
			"Param 2": "Value 2"
		}
	}

También puede usar [Funciones de Factoría de datos](https://msdn.microsoft.com/library/dn835056.aspx) para pasar valores para los parámetros de servicio web como se muestra en el siguiente ejemplo:

	"typeProperties": {
    	"globalParameters": {
    	   "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    	}
  	}
 
> [AZURE.NOTE] Los parámetros de servicio web distinguen entre mayúsculas y minúsculas para garantizar que los nombres que especifica en JSON de actividad coinciden con los que muestra el servicio web.

### Uso de un módulo lector para leer datos de varios archivos de blob de Azure
Las canalizaciones de macrodatos (Pig, Hive, etc...) pueden generar uno o más archivos de salida sin extensiones. Por ejemplo, cuando se especifica una tabla externa de Hive, los datos de dicha tabla se pueden almacenar en el almacenamiento de blobs de Azure con el siguiente nombre 000000\_0. Puede usar el módulo lector en un experimento para leer varios archivos y usarlos para realizar predicciones.

Al usar el módulo lector en un experimento de Aprendizaje automático de Azure, puede especificar Blob de Azure como entrada. Los archivos en el almacenamiento de blobs de Azure pueden ser los archivos de salida (por ejemplo, 000000\_0) que se generan mediante un script de Pig y Hive en HDInsight. El módulo lector permite leer archivos (sin extensiones) mediante la configuración de la propiedad **Ruta al contenedor, directorio o blob** de dicho módulo para que señale el contenedor o la carpeta que contiene los archivos, como se muestra a continuación. Tenga en cuenta que el asterisco (*) **especifica que todos los archivos de la carpeta o contenedor (es decir, data/aggregateddata/year=2014/month-6/*)** se leerán como parte del experimento.

![Propiedades de Blob de Azure](./media/data-factory-create-predictive-pipelines/azure-blob-properties.png)


### Ejemplo 
#### Canalización con la actividad AzureMLBatchExecution con parámetros de servicio web

	{
	  "name": "MLWithSqlReaderSqlWriter",
	  "properties": {
	    "description": "Azure ML model with sql azure reader/writer",
	    "activities": [
	      {
	        "name": "MLSqlReaderSqlWriterActivity",
	        "type": "AzureMLBatchExecution",
	        "description": "test",
	        "inputs": [
	          {
	            "name": "MLSqlInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "MLSqlOutput"
	          }
	        ],
	        "linkedServiceName": "MLSqlReaderSqlWriterDecisionTreeModel",
            "typeProperties":
            {
                "webServiceInput": "MLSqlInput",
                "webServiceOutputs": {
                    "output1": "MLSqlOutput"
                }
	          	"globalParameters": {
	            	"Database server name": "<myserver>.database.windows.net",
		            "Database name": "<database>",
		            "Server user account name": "<user name>",
		            "Server user account password": "<password>"
	          	}              
            },
	        "policy": {
	          "concurrency": 1,
	          "executionPriorityOrder": "NewestFirst",
	          "retry": 1,
	          "timeout": "02:00:00"
	        },
	      }
	    ],
	    "start": "2015-02-13T00:00:00Z",
	    "end": "2015-02-14T00:00:00Z"
	  }
	}
 
En el ejemplo JSON anterior:

- El servicio web implementado de Aprendizaje automático de Azure usa un módulo lector y otro escritor para leer y escribir datos desde y hacia una base de datos SQL de Azure. Este servicio web expone los cuatro parámetros siguientes: nombre de servidor de base de datos, nombre de base de datos, nombre de cuenta de usuario de servidor y contraseña de cuenta de usuario de servidor.  
- Las fechas y horas de **inicio** y **finalización** deben estar en [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por ejemplo: 2014-10-14T16:32:41Z. La hora de **finalización** es opcional. Si no especifica ningún valor para la propiedad **end**, se calcula como "**start + 48 horas**". Para ejecutar la canalización indefinidamente, especifique **9999-09-09** como valor de propiedad **end**. Para obtener más información sobre las propiedades JSON, vea [Referencia de scripting JSON](https://msdn.microsoft.com/library/dn835050.aspx).

### Otros escenarios

#### Servicio web no requiere una entrada

Los servicios web de ejecución de lotes de Aprendizaje automático de Azure se pueden usar para ejecutar cualquier flujo de trabajo, por ejemplo, scripts R o Python, que puedan no requerir entradas. O bien, el experimento se podría configurar con un módulo Lector que no expone ningún GlobalParameters. En ese caso, la actividad AzureMLBatchExecution se configuraría de la siguiente manera:

	{
        "name": "scoring service",
        "type": "AzureMLBatchExecution",
        "outputs": [
            {
                "name": "myBlob"
            }
        ],
        "typeProperties": {
            "webServiceOutputs": {
                "output1": "myBlob"
            }              
         },
        "linkedServiceName": "mlEndpoint",
        "policy": {
            "concurrency": 1,
            "executionPriorityOrder": "NewestFirst",
            "retry": 1,
            "timeout": "02:00:00"
        }
    },
   

#### Servicio web no requiere entrada/salida
El servicio web de ejecución de lotes de Aprendizaje automático de Azure podría no tener configurada ninguna salida de servicio web. En este ejemplo, no hay ninguna entrada o salida de servicio web ni tampoco hay configurado ningún GlobalParameters. Tenga en cuenta que todavía hay una salida configurada en la actividad misma, pero que no se presenta como webServiceOutput.

	{
        "name": "retraining",
        "type": "AzureMLBatchExecution",
        "outputs": [
            {
                "name": "placeholderOutputDataset"
            }
        ],
        "typeProperties": {
         },
        "linkedServiceName": "mlEndpoint",
        "policy": {
            "concurrency": 1,
            "executionPriorityOrder": "NewestFirst",
            "retry": 1,
            "timeout": "02:00:00"
        }
    },

#### Servicio web usa lectores y escritores y la actividad solo se ejecuta cuando otras actividades se realizaron correctamente

Los módulos lector y escritor del servicio web de Aprendizaje automático de Azure se podrían configurar para ejecutarse con o sin GlobalParameters. Pero es posible que desee insertar las llamadas de servicio en una canalización de procesamiento que usa dependencias de conjunto de datos para invocar el servicio solo una vez que se completa algún procesamiento ascendente y no desencadenar ninguna otra acción una vez que se completa la ejecución de lotes. En ese caso, puede expresar las dependencias mediante entradas y salidas de la actividad, sin denominar a ninguna de ellas como entradas o salidas del servicio web.

	{
	    "name": "retraining",
	    "type": "AzureMLBatchExecution",
	    "inputs": [
	        {
	            "name": "upstreamData1"
	        },
	        {
	            "name": "upstreamData2"
	        }
	    ],
	    "outputs": [
	        {
	            "name": "downstreamData"
	        }
	    ],
	    "typeProperties": {
	     },
	    "linkedServiceName": "mlEndpoint",
	    "policy": {
	        "concurrency": 1,
	        "executionPriorityOrder": "NewestFirst",
	        "retry": 1,
	        "timeout": "02:00:00"
	    }
	},

Las **conclusiones** son:

-   Si el punto de conexión del experimento usa una propiedad webServiceInput, se representa con un conjunto de datos de blob y se incluye en las entradas de la actividad, así como también la propiedad webServiceInput. De lo contrario, se omite la propiedad webServiceInput. 
-   Si el punto de conexión del experimento usa una o más propiedades webServiceOutput, se representan con conjuntos de datos de blob y se incluyen en las salidas de la actividad, así como también en la propiedad webServiceOutputs (asignada mediante el nombre de cada salida del experimento). De lo contrario, se omite la propiedad webServiceOutputs.
-   Si el punto de conexión del experimento expone una o más propiedades globalParameters, se proporcionan en la propiedad globalParameters como pares clave-valor. De lo contrario, se omite la propiedad globalParameters. Las claves distinguen mayúsculas de minúsculas. [Las funciones de Factoría de datos de Azure](data-factory-scheduling-and-execution.md#data-factory-functions-reference) se pueden usar en los valores. 
- Es posible incluir conjuntos de datos adicionales en las propiedades de entradas y salidas de la actividad, sin que typeProperties de la actividad haga referencia a ellos. Estos regirán la ejecución mediante el uso de las dependencias de segmento, pero, de otra manera, la actividad AzureMLBatchExecution los omitirá. 


## Actualización de modelos de Aprendizaje automático de Azure mediante la actividad de recursos de actualización
Pasado algún tiempo, los modelos predictivos en los experimentos de puntuación de Aprendizaje automático de Azure tienen que volver a entrenarse con nuevos conjuntos de datos de entrada. Después de terminar con el nuevo entrenamiento, tendrá que actualizar el servicio web de puntuación con el modelo de Aprendizaje automático que volvió a entrenar. Los pasos más comunes para habilitar el nuevo entrenamiento y actualizar los modelos de Aprendizaje automático de Azure mediante los servicios web son:

1. Crear un experimento en [Estudio de aprendizaje automático de Azure](https://studio.azureml.net). 
2. Cuando esté satisfecho con el modelo, use Estudio de aprendizaje automático de Azure para publicar servicios web para el **experimento de entrenamiento** y puntuación / ** experimento predictivo**.

En la tabla siguiente se describen los servicios web empleados en este ejemplo. Consulte [Volver a entrenar modelos de aprendizaje automático mediante programación](../machine-learning/machine-learning-retrain-models-programmatically.md) para obtener información detallada.

| Tipo de servicio web | description 
| :------------------ | :---------- 
| **Servicio web de entrenamiento** | Recibe datos de entrenamiento y genera modelos entrenados. El resultado del entrenamiento es un archivo .ilearner en un almacenamiento de blobs de Azure. El **punto de conexión predeterminado** se crea automáticamente para cuando publique el experimento de entrenamiento como un servicio web. Puede crear más puntos de conexión, pero el ejemplo usa solo el punto de conexión predeterminado |
| **Servicio web de puntuación** | Recibe ejemplos de datos sin etiqueta y realiza predicciones. El resultado de la predicción puede presentarse en diversas formas, como un archivo .csv o como las filas de una Base de datos de SQL de Azure, dependiendo de la configuración del experimento. El punto de conexión predeterminado se crea automáticamente cuando se publica el experimento predictivo como un servicio web. Tendrá que crear el segundo **punto de conexión no predeterminado y actualizable** a través del [Portal de Azure clásico](https://manage.windowsazure.com). Puede crear más puntos de conexión, pero el ejemplo usa solo el punto de conexión no predeterminado actualizable. Consulte el artículo [Creación de puntos de conexión](../machine-learning/machine-learning-create-endpoint.md) para conocer los pasos necesarios para ello.       
 
La siguiente imagen muestra la relación entre los puntos de conexión de entrenamiento y de puntuación de Aprendizaje automático de Azure.

![Servicios web](./media/data-factory-azure-ml-batch-execution-activity/web-services.png)


Puede invocar el **servicio web de entrenamiento** mediante la **actividad de ejecución de lotes de Aprendizaje automático de Azure**. Es lo mismo que invocar un servicio web de Aprendizaje automático de Azure (servicio web de puntuación) para puntuar datos. Las secciones anteriores tratan en detalle cómo invocar un servicio web de Aprendizaje automático de Azure a partir de una canalización de Factoría de datos de Azure.
  
El **servicio web de puntuación** se puede invocar con la **Actividad de recursos de actualización de Aprendizaje automático de Azure** para actualizar el servicio web con el modelo recién entrenado. Como se mencionó en la tabla anterior, debe crear y usar el punto de conexión no predeterminado y actualizable. También debe actualizar todos los servicios vinculados existentes en Factoría de datos para que empleen el punto de conexión no predeterminado y, de ese modo, usen siempre el modelo con el entrenamiento más reciente.

El escenario siguiente proporciona información detallada con un ejemplo de entrenimiento y actualización de los modelos de Aprendizaje automático de Azure, desde una canalización de Factoría de datos de Azure.
 
### Escenario: entrenamiento y actualización de un modelo de Aprendizaje automático de Azure
Esta sección proporciona una canalización de ejemplo que usa la **Actividad de ejecución de lotes de Aprendizaje automático de Azure** para volver a entrenar un modelo y la **Actividad de recursos de actualización de Aprendizaje automático de Azure** para actualizar el modelo en el servicio web de puntuación. También proporciona fragmentos JSON para todos los servicios vinculados, conjuntos de datos y canalización en el ejemplo.

Esta es la vista de diagrama de la canalización de ejemplo. Como se puede apreciar, la Actividad de ejecución de lotes de Aprendizaje automático de Azure toma la entrada de entrenamiento y genera un resultado de entrenamiento (archivo iLearner). La Actividad de recursos de actualización de Aprendizaje automático de Azure toma este resultado de entrenamiento y actualiza el modelo en el punto de conexión de servicio web de puntuación. La Actividad de recursos de actualización no produce ningún resultado. El placeholderBlob es simplemente un conjunto de datos de salida ficticio que el servicio de Factoría de datos de Azure necesita para ejecutar la canalización.

![diagrama de canalización](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)


#### Servicio vinculado de almacenamiento de blobs de Azure:
Almacenamiento de Azure contiene los siguientes datos:

- datos de aprendizaje. Estos son los datos de entrada para el servicio web de entrenamiento de Aprendizaje automático de Azure.  
- archivo iLearner. Estos son los datos de salida del servicio web de entrenamiento de Aprendizaje automático de Azure. Es también es la entrada para la Actividad de recursos de actualización.  
   
Esta es la definición de JSON de ejemplo del servicio vinculado:

	{
		"name": "StorageLinkedService",
	  	"properties": {
	    	"type": "AzureStorage",
			"typeProperties": {
	    		"connectionString": "DefaultEndpointsProtocol=https;AccountName=name;AccountKey=key"
			}
		}
	}


#### Conjunto de datos de entrada de entrenamiento:
El siguiente conjunto de datos representa los datos de entrenamiento de entrada para el servicio web de entrenamiento de Aprendizaje automático de Azure. La Actividad de ejecución de lotes de Aprendizaje automático de Azure toma este conjunto de datos como entrada.

	{
	    "name": "trainingData",
	    "properties": {
	        "type": "AzureBlob",
	        "linkedServiceName": "StorageLinkedService",
	        "typeProperties": {
	            "folderPath": "labeledexamples",
	            "fileName": "labeledexamples.arff",
	            "format": {
	                "type": "TextFormat"
	            }
	        },
	        "availability": {
	            "frequency": "Week",
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

#### Conjunto de datos de salida de entrenamiento:
El siguiente conjunto de datos representa el archivo iLearner de salida del servicio web de entrenamiento de Aprendizaje automático de Azure. La Actividad de ejecución de lotes de Aprendizaje automático de Azure produce este conjunto de datos. Este conjunto de datos es también la entrada para la Actividad de recursos de actualización de Aprendizaje automático de Azure.

	{
	    "name": "trainedModelBlob",
	    "properties": {
	        "type": "AzureBlob",
	        "linkedServiceName": "StorageLinkedService",
	        "typeProperties": {
	            "folderPath": "trainingoutput",
	            "fileName": "model.ilearner",
	            "format": {
	                "type": "TextFormat"
	            }
	        },
	        "availability": {
	            "frequency": "Week",
	            "interval": 1
	        }
	    }
	}

#### Servicio vinculado para el punto de conexión de entrenamiento de Aprendizaje automático de Azure 
El siguiente fragmento JSON define un servicio vinculado de Aprendizaje automático de Azure que apunta al punto de conexión predeterminado del servicio web de entrenamiento.

	{	
		"name": "trainingEndpoint",
	  	"properties": {
	    	"type": "AzureML",
	    	"typeProperties": {
	    		"mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/xxx/services/--training experiment--/jobs",
	      		"apiKey": "myKey"
	    	}
	  	}
	}

En **Estudio de aprendizaje automático**, haga lo siguiente para obtener valores para **mlEndpoint** y **apiKey**:

1. Haga clic en **SERVICIOS WEB** en el menú de la izquierda.
2. En la lista de servicios web, haga clic en el **servicio web de entrenamiento**. 
3. Haga clic en Copiar junto al cuadro de texto **clave de API** para copiar la clave de API en el Portapapeles. Pegue la clave en el editor de JSON de Factoría de datos.
4. En **Estudio de aprendizaje automático de Azure**, haga clic en el vínculo **EJECUCIÓN DE LOTES**, copie el **identificador URI de solicitud** de la sección **Solicitud** y péguelo en el editor de JSON de Factoría de datos.   


#### Servicio vinculado para el punto de conexión de puntuación actualizable de Aprendizaje automático de Azure:
El siguiente fragmento JSON define un servicio vinculado de Aprendizaje automático de Azure que apunta al punto de conexión no predeterminado y actualizable del servicio web de puntuación.

	{
	    "name": "updatableScoringEndpoint2",
	    "properties": {
	        "type": "AzureML",
	        "typeProperties": {
	            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/xxx/services/--scoring experiment--/jobs",
	            "apiKey": "endpoint2Key",
	            "updateResourceEndpoint": "https://management.azureml.net/workspaces/xxx/webservices/--scoring experiment--/endpoints/endpoint2"
	        }
	    }
	}


Antes de crear e implementar un servicio vinculado de Aprendizaje automático de Azure, siga los pasos en [Creación de puntos de conexión](../machine-learning/machine-learning-create-endpoint.md) para crear un segundo punto de conexión (no predeterminada y actualizable) para el servicio web de puntuación.

Después de crear el punto de conexión no predeterminado actualizable, haga clic en <1>EJECUCIÓN DE LOTES</1> para obtener el valor del identificador URI para la propiedad JSON <2>mlEndpoint</2> y haga clic en el vínculo <3>ACTUALIZAR RECURSO</3> para obtener el valor del identificador URI para la propiedad JSON <4>updateResourceEndpoint</4>. La clave de API está en la página de punto de conexión (en la esquina inferior derecha).

![punto de conexión actualizable](./media/data-factory-azure-ml-batch-execution-activity/updatable-endpoint.png)

 
#### Conjunto de datos de salida de marcador de posición:
La Actividad de recursos de actualización de Aprendizaje automático de Azure no genera ningún resultado, pero en Factoría de datos de Azure es necesario tener un conjunto de datos de resultado para controlar la programación de la canalización, por lo que en este ejemplo se usa un conjunto de datos ficticios o de marcador de posición.

	{
	    "name": "placeholderBlob",
	    "properties": {
	        "availability": {
	            "frequency": "Week",
	            "interval": 1
	        },
	        "type": "AzureBlob",
	        "linkedServiceName": "StorageLinkedService",
	        "typeProperties": {
	            "folderPath": "any",
	            "format": {
	                "type": "TextFormat"
	            }
	        }
	    }
	}


#### Canalización
La canalización tiene dos actividades: **AzureMLBatchExecution** y **AzureMLUpdateResource**. La Actividad de ejecución de lotes de Aprendizaje automático de Azure toma los datos de entrenamiento como entrada y genera como resultado un archivo iLearner. La actividad invoca el servicio web de entrenamiento (el experimento de entrenamiento expuesto como servicio web) con los datos de entrenamiento de entrada y recibe el archivo ilearner desde el servicio web. El placeholderBlob es simplemente un conjunto de datos de salida ficticio que el servicio de Factoría de datos de Azure necesita para ejecutar la canalización.

![diagrama de canalización](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)


	{
	    "name": "pipeline",
	    "properties": {
	        "activities": [
	            {
	                "name": "retraining",
	                "type": "AzureMLBatchExecution",
	                "inputs": [
	                    {
	                        "name": "trainingData"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "trainedModelBlob"
	                    }
	                ],
	                "typeProperties": {
	                    "webServiceInput": "trainingData",
	                    "webServiceOutputs": {
	                        "output1": "trainedModelBlob"
	                    }              
	                 },
	                "linkedServiceName": "trainingEndpoint",
	                "policy": {
	                    "concurrency": 1,
	                    "executionPriorityOrder": "NewestFirst",
	                    "retry": 1,
	                    "timeout": "02:00:00"
	                }
	            },
	            {
	                "type": "AzureMLUpdateResource",
	                "typeProperties": {
	                    "trainedModelName": "Training Exp for ADF ML [trained model]",
	                    "trainedModelDatasetName" :  "trainedModelBlob"
	                },
	                "inputs": [
	                    {
	                        "name": "trainedModelBlob"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "placeholderBlob"
	                    }
	                ],
	                "policy": {
	                    "timeout": "01:00:00",
	                    "concurrency": 1,
	                    "retry": 3
	                },
	                "name": "AzureML Update Resource",
	                "linkedServiceName": "updatableScoringEndpoint2"
	            }
	        ],
	    	"start": "2015-02-13T00:00:00Z",
	   		"end": "2015-02-14T00:00:00Z"
	    }
	}


### Módulos Lector y Escritor

Un escenario común para el uso de parámetros de servicio web es el uso de Lectores y escritores SQL de Azure. El módulo de lector se utiliza para cargar datos en un experimento desde los servicios de administración de datos fuera de Estudio de aprendizaje automático de Azure y el módulo de escritor es para guardar datos de los experimentos en los servicios de administración de datos fuera de Estudio de aprendizaje automático de Azure.

Para obtener información acerca del lector/escritor SQL Azure/Blob, consulte los temas [Lector](https://msdn.microsoft.com/library/azure/dn905997.aspx) y [Escritor](https://msdn.microsoft.com/library/azure/dn905984.aspx) en MSDN Library. El ejemplo de la sección anterior utilizó el lector de Blob de Azure y el lector de Blob de Azure. En esta sección se trata el uso del lector SQL Azure y el escritor SQL Azure.


## Preguntas más frecuentes

**P:** Tengo varios archivos generados por medio de mis canalizaciones de macrodatos. ¿Puedo usar la actividad AzureMLBatchExecution para trabajar en todos los archivos?

**R:** Sí. Consulte **Uso de un módulo lector para leer datos de varios archivos de blob de Azure** para obtener más información.

## Actividad de puntuación de lotes de Aprendizaje automático de Azure
Si va a usar la actividad **AzureMLBatchScoring** para integrarse con Aprendizaje automático de Azure, se recomienda que use la actividad **AzureMLBatchExecution** más reciente.

La actividad AzureMLBatchExecution se introdujo en la versión de agosto de 2015 del SDK de Azure y Azure PowerShell.

Si desea continuar utilizando la actividad AzureMLBatchScoring, siga leyendo esta sección.

### Actividad de puntuación de lotes de Aprendizaje automático de Azure con Almacenamiento de Azure para entrada/salida 

	{
	  "name": "PredictivePipeline",
	  "properties": {
	    "description": "use AzureML model",
	    "activities": [
	      {
	        "name": "MLActivity",
	        "type": "AzureMLBatchScoring",
	        "description": "prediction analysis on batch input",
	        "inputs": [
	          {
	            "name": "ScoringInputBlob"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "ScoringResultBlob"
	          }
	        ],
	        "linkedServiceName": "MyAzureMLLinkedService",
	        "policy": {
	          "concurrency": 3,
	          "executionPriorityOrder": "NewestFirst",
	          "retry": 1,
	          "timeout": "02:00:00"
	        }
	      }
	    ],
	    "start": "2015-02-13T00:00:00Z",
	    "end": "2015-02-14T00:00:00Z"
	  }
	}

### Parámetros de servicio web
Agregue una sección **typeProperties** a la sección **AzureMLBatchScoringActivty** en la canalización JSON para especificar valores para los parámetros del servicio web en esa sección, como se muestra en el siguiente ejemplo:

	"typeProperties": {
		"webServiceParameters": {
			"Param 1": "Value 1",
			"Param 2": "Value 2"
		}
	}


También puede usar [Funciones de Factoría de datos](https://msdn.microsoft.com/library/dn835056.aspx) para pasar valores para los parámetros de servicio web como se muestra en el siguiente ejemplo:

	"typeProperties": {
    	"webServiceParameters": {
    	   "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    	}
  	}
 
> [AZURE.NOTE] Los parámetros de servicio web distinguen entre mayúsculas y minúsculas para garantizar que los nombres que especifica en JSON de actividad coinciden con los que muestra el servicio web.

## Otras referencias

- [Entrada de blog de Azure: Introducción a la factoría de datos de Azure y al Aprendizaje automático de Azure](https://azure.microsoft.com/blog/getting-started-with-azure-data-factory-and-azure-machine-learning-4/)





[adf-build-1st-pipeline]: data-factory-build-your-first-pipeline.md

[azure-machine-learning]: http://azure.microsoft.com/services/machine-learning/


 

<!---HONumber=AcomDC_0224_2016-->