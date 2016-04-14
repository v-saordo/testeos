<properties
	pageTitle="Mover datos desde un servidor SQL Server local a SQL Azure con la Factoría de datos de Azure | Azure"
	description="Configure una canalización de ADF que componga dos actividades de migración de datos que se combinen para mover datos diariamente entre bases de datos locales y de nube."
	services="machine-learning"
	documentationCenter=""
	authors="fashah"
	manager="jacob.spoelstra"
	editor=""
	videoId=""
	scriptId="" />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/08/2016"
	ms.author="fashah;bradsev" />


# Mover datos desde un servidor SQL Server local a SQL Azure con la Factoría de datos de Azure

En este tema se muestra cómo mover datos desde una base de datos local de SQL Server a una base de datos de SQL Azure a través del almacenamiento de blobs de Azure mediante la Factoría de datos de Azure (ADF).

El **menú** a continuación vincula a temas en los que se describe cómo introducir datos en los entornos de destino en que sea posible almacenar y procesar datos durante el proceso de Cortana Analytics (CAPS).

[AZURE.INCLUDE [cap-ingest-data-selector](../../includes/cap-ingest-data-selector.md)]

## <a name="intro"></a>Introducción: ¿qué es la ADF y cuándo se debe usar para migrar datos?

La Factoría de datos de Azure es un servicio de integración de datos totalmente administrado basado en la nube que organiza y automatiza el movimiento y la transformación de datos. El concepto clave en el modelo de ADF es la canalización. Un canalización es una agrupación lógica de actividades, cada una de las cuales define las acciones que se deben realizar en los datos incluidos en los conjuntos de datos. Los servicios vinculados se usan para definir la información necesaria para que la Factoría de datos se conecte a los recursos de datos.

Con la ADF, los servicios de procesamiento de datos existentes se pueden componer en canalizaciones de datos altamente disponibles y administradas en la nube. Estas canalizaciones de datos se pueden programar para ingerir, preparar, transformar, analizar y publicar datos, mientras que la ADF administra y organiza todos los datos complejos y las dependencias de procesamiento. Las soluciones se pueden generar e implementar rápidamente en la nube. Para ello, se conecta una cantidad en aumento de orígenes de datos locales y de nube.

Considere el uso de la ADF cuando los datos deban migrarse continuamente en un escenario híbrido en el que se tenga acceso a recursos locales y de nube, y cuando los datos se transfieran o deban modificarse o tener lógica de negocios agregada mientras se migran. La ADF permite la programación y supervisión de trabajos mediante scripts JSON sencillos que administran el movimiento de datos de forma periódica. La ADF también tiene otras capacidades como la compatibilidad con operaciones complejas. Para obtener más información sobre la ADF, vea la documentación de [Factoría de datos de Azure (ADF)](https://azure.microsoft.com/services/data-factory/).

## <a name="scenario"></a>Escenario

Configuramos una canalización de ADF que compone dos actividades de migración de datos que se combinan para mover datos diariamente entre una base de datos SQL local y una base de datos SQL de Azure en la nube. Las dos actividades son:

* copiar datos de una base de datos SQL Server local a una cuenta de almacenamiento de blobs de Azure.
* copiar datos de la cuenta de almacenamiento de blobs de Azure a una base de datos SQL de Azure.

**Referencia**: los pasos mostrados aquí se han adaptado de un tutorial más detallado [Habilitación de canalizaciones para trabajar con datos locales](data-factory-use-onpremises-datasources.md) que proporciona el equipo de ADF, y las referencias a las secciones asociadas de ese tema se proporcionarán cuando corresponda.


## <a name="prereqs"></a>Requisitos previos
En este tutorial se asume que dispone de:

* Una **suscripción de Azure**. Si no tiene una suscripción, puede registrarse para obtener una [prueba gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Una **cuenta de almacenamiento de Azure**. En este tutorial, usará una cuenta de almacenamiento de Azure para almacenar los datos. Si no dispone de una cuenta de almacenamiento de Azure, vea el artículo [Creación de una cuenta de almacenamiento](storage-create-storage-account.md#create-a-storage-account). Tras crear la cuenta de almacenamiento, tendrá que obtener la clave de cuenta que se usa para tener acceso al almacenamiento. Vea [Vista, copia y regeneración de las claves de acceso de almacenamiento](storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).
* Acceso a una **base de datos SQL de Azure**. Si debe configurar una base de datos de SQL de Azure, [Introducción a Base de datos de SQL de Microsoft Azure](sql-database-get-started.md) proporciona información sobre cómo aprovisionar una nueva instancia de una base de datos de SQL de Azure.
* **Azure PowerShell** instalado y configurado de forma local. Para obtener instrucciones, consulte [Instalación y configuración de Azure PowerShell](powershell-install-configure.md).

> [AZURE.NOTE] En este procedimiento se usa el [Portal de Azure](https://ms.portal.azure.com/).

##<a name="upload-data"></a> Cargar los datos al servidor SQL Server local

Usamos el [conjunto de datos de taxis de Nueva York](http://chriswhong.com/open-data/foil_nyc_taxi/) para demostrar el proceso de migración. El conjunto de datos de taxis de Nueva York está disponible, como se especificó en esa publicación, en el almacenamiento de blobs de Azure [Datos de taxis de Nueva York](http://www.andresmh.com/nyctaxitrips/). Los datos tienen dos archivos, el archivo trip\_data.csv, que contiene detalles de carreras, y el archivo trip\_far.csv, que contiene detalles de la tarifa de cada carrera. En [Descripción del conjunto de datos de carreras de taxi de Nueva York](machine-learning-data-science-process-sql-walkthrough.md#dataset), se proporciona un ejemplo y una descripción de estos archivos.


Puede adaptar el procedimiento que se proporciona aquí para un conjunto de datos propios o seguir los pasos descritos para el uso del conjunto de datos de taxis de Nueva York. Para cargar el conjunto de datos de taxis de Nueva York en la base de datos de SQL Server local, siga el procedimiento descrito en [Importación masiva de datos en una base de datos de SQL Server](machine-learning-data-science-process-sql-walkthrough.md#dbload). Estas instrucciones son para un servidor SQL Server en una máquina virtual de Azure, pero el procedimiento para realizar la carga en el servidor SQL Server local es el mismo.

##<a name="create-adf"></a> Crear una factoría de datos de Azure

Las instrucciones para crear una nueva factoría de datos de Azure y un grupo de recursos en el [Portal de Azure](https://ms.portal.azure.com/) se proporcionan en [Crear una factoría de datos de Azure](data-factory-build-your-first-pipeline-using-editor.md#step-1-creating-the-data-factory). Ponga el nombre *adfdsp* a la nueva instancia de ADF y el nombre *adfdsprg* al grupo de recursos creado.

## Instalar y configurar Data Management Gateway

Para permitir que las canalizaciones de una factoría de datos de Azure trabajen con un servidor SQL Server local, debe agregar el servidor como un servicio vinculado a la factoría de datos. Para crear un servicio vinculado para el servidor SQL Server local, primero debe descargar e instalar Microsoft Data Management Gateway en el equipo local y configurar el servicio vinculado para que el origen de datos local use la puerta de enlace. Data Management Gateway serializa y deserializa los datos de origen y receptor en el equipo donde se hospeda.

Para obtener instrucciones y detalles de instalación en Data Management Gateway, vea [Habilitación de canalizaciones para trabajar con datos locales](data-factory-use-onpremises-datasources.md).


## <a name="adflinkedservices"></a>Crear servicios vinculados para conectarse a los recursos de datos

Un servicio vinculado define la información necesaria para que la Factoría de datos de Azure se conecte a un recurso de datos. El procedimiento paso a paso para crear servicios vinculados se proporciona en [Crear servicios vinculados](data-factory-use-onpremises-datasources.md#step-2-create-linked-services).

Tenemos tres recursos en este escenario para los que se necesitan servicios vinculados.

1. [Servicio vinculado para SQL Server local](#adf-linked-service-onprem-sql)
2. [Servicio vinculado para el almacenamiento de blobs de Azure](#adf-linked-service-blob-store)
3. [Servicio vinculado para la base de datos SQL de Azure](#adf-linked-service-azure-sql)


###<a name="adf-linked-service-onprem-sql"></a>Servicio vinculado para la base de datos SQL Server local
Para crear el servicio vinculado para el servidor SQL Server local, haga clic en el **Almacén de datos** en la página de aterrizaje de ADF del Portal de Azure clásico, seleccione *SQL* y especifique las credenciales para el *nombre de usuario* y la *contraseña* del servidor SQL Server local. Debe especificar el nombre del servidor como **nombre de la instancia de la barra diagonal inversa del nombre de servidor completo (nombre de servidor\\nombre de instancia)**. Nombre del servicio vinculado *adfonpremsql*.

###<a name="adf-linked-service-blob-store"></a>Servicio vinculado de blob
Para crear el servicio vinculado para la cuenta de almacenamiento de blobs de Azure, haga clic en el **Almacén de datos** en la página de aterrizaje de ADF del Portal de Azure clásico, seleccione *Cuenta de almacenamiento de Azure* y escriba la clave y el nombre de contenedor de la cuenta de almacenamiento de blobs de Azure. Nombre del servicio vinculado *adfds*.

###<a name="adf-linked-service-azure-sql"></a>Servicio vinculado para la base de datos SQL de Azure
Para crear el servicio vinculado para la base de datos SQL de Azure, haga clic en el **Almacén de datos** en la página de aterrizaje de ADF del Portal de Azure clásico, seleccione *SQL* y especifique las credenciales para el *nombre de usuario* y la *contraseña* de la base de datos SQL de Azure. El *nombre de usuario* debe ser *user@servername*.


##<a name="adf-tables"></a>Definir y crear tablas para especificar cómo tener acceso a los conjuntos de datos

Cree tablas que especifiquen la estructura, la ubicación y la disponibilidad de los conjuntos de datos con los siguientes procedimientos de scripts. Los archivos JSON se usan para definir las tablas. Para obtener más información sobre la estructura de estos archivos, vea [Conjuntos de datos](data-factory-create-datasets.md).

> [AZURE.NOTE]  Debe ejecutar el cmdlet `Add-AzureAccount` antes de ejecutar el cmdlet [New-AzureDataFactoryTable](https://msdn.microsoft.com/library/azure/dn835096.aspx) para confirmar que se selecciona la suscripción de Azure adecuada para la ejecución del comando. Para obtener documentación sobre este cmdlet, consulte [Add-AzureAccount](https://msdn.microsoft.com/library/azure/dn790372.aspx).

Las definiciones basadas en JSON de las tablas usan los siguientes nombres:

* el **nombre de la tabla** del servidor SQL Server local es *nyctaxi\_data*
* el **nombre del contenedor** de la cuenta de almacenamiento de blobs de Azure es *containername*  

Se necesitan tres definiciones de tabla para esta canalización de ADF:

1. [Tabla SQL local](#adf-table-onprem-sql)
2. [Tabla Blob](#adf-table-blob-store)
3. [Tabla SQL de Azure](#adf-table-azure-sql)

> [AZURE.NOTE]  Los siguientes procedimientos usan Azure PowerShell para definir y crear las actividades de ADF. Sin embargo, estas tareas también se pueden realizar en el portal de Azure. Para obtener detalles, consulte [Creación de conjuntos de datos de entrada y salida](data-factory-use-onpremises-datasources.md#step-3-create-input-and-output-datasets).

###<a name="adf-table-onprem-sql"></a>Tabla SQL local

La definición de tabla del servidor SQL Server local se especifica en el siguiente archivo JSON.

    	{
	    	"name": "OnPremSQLTable",
	    	"properties":
	    	{
		    	"location":
		    	{
		    	"type": "OnPremisesSqlServerTableLocation",
		    	"tableName": "nyctaxi_data",
		    	"linkedServiceName": "adfonpremsql"
		    	},
		    	"availability":
		    	{
		    	"frequency": "Day",
		    	"interval": 1,   
		    	"waitOnExternal":
		    	{
		    	"retryInterval": "00:01:00",
		    	"retryTimeout": "00:10:00",
		    	"maximumRetry": 3
		    	}

		    	}
	    	}
    	}
Tenga en cuenta que los nombres de columna no se han incluido aquí. Para subseleccionar los nombres de columna, inclúyalos aquí (para obtener detalles, vea la [documentación de ADF](data-factory-data-movement-activities.md)).

Copie la definición de JSON de la tabla en un archivo denominado *onpremtabledef.json* y guarde el archivo en una ubicación conocida (aquí se supone que es *C:\\temp\\onpremtabledef.json*). Cree la tabla en la ADF con el siguiente cmdlet de Azure PowerShell.

	New-AzureDataFactoryTable -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp –File C:\temp\onpremtabledef.json


###<a name="adf-table-blob-store"></a>Tabla Blob
La definición de la tabla para la ubicación del blob de salida se encuentra en la siguiente ubicación (este esquema asigna los datos ingeridos de una ubicación local a un blob de Azure):

	    {
		    "name": "OutputBlobTable",
		    "properties":
		    {
			    "location":
			    {
			    "type": "AzureBlobLocation",
			    "folderPath": "containername",
			    "format":
			    {
			    "type": "TextFormat",
			    "columnDelimiter": "\t"
			    },
			    "linkedServiceName": "adfds"
			    },
			    "availability":
			    {
			    "frequency": "Day",
			    "interval": 1
			    }
		    }
	    }

Copie la definición de JSON de la tabla en un archivo denominado *bloboutputtabledef.json* y guarde el archivo en una ubicación conocida (aquí se supone que es *C:\\temp\\bloboutputtabledef.json*). Cree la tabla en la ADF con el siguiente cmdlet de Azure PowerShell.

	New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\bloboutputtabledef.json  

###<a name="adf-table-azure-sq"></a>Tabla SQL de Azure
La definición de la tabla para la salida SQL de Azure se encuentra en la siguiente ubicación (este esquema asigna los datos procedentes del blob):

	{
	    "name": "OutputSQLAzureTable",
	    "properties":
	    {
	        "structure":
	        [
				{ "name": "column1", type": "String"},
				{ "name": "column2", type": "String"}                
	        ],
	        "location":
	        {
	            "type": "AzureSqlTableLocation",
	            "tableName": "your_db_name",
	            "linkedServiceName": "adfdssqlazure_linked_servicename"
	        },
	        "availability":
	        {
	            "frequency": "Day",
	            "interval": 1            
	        }
	    }
	}

Copie la definición de JSON de la tabla en un archivo denominado *AzureSqlTable.json* y guarde el archivo en una ubicación conocida (aquí se supone que es *C:\\temp\\AzureSqlTable.json*). Cree la tabla en la ADF con el siguiente cmdlet de Azure PowerShell.

	New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\AzureSqlTable.json  

##<a name="adf-pipeline"></a>Definición y creación de la canalización

Especifique las actividades que pertenecen a la canalización y cree la canalización con los siguientes procedimientos de scripts. Un archivo JSON se usa para definir las propiedades de la canalización.

* En el script se supone que el **nombre de la canalización** es *AMLDSProcessPipeline*.
* Además, tenga en cuenta que la periodicidad de la canalización que se va a ejecutar se establece como diaria y que se usa el tiempo de ejecución predeterminado para el trabajo (12 a.m. UTC).

> [AZURE.NOTE]  Los siguientes procedimientos usan Azure PowerShell para definir y crear la canalización de ADF. Sin embargo, esta tarea también se puede realizar en el portal de Azure. Para obtener detalles, consulte [Creación y ejecución de una canalización](../data-factory/data-factory-use-onpremises-datasources.md#step-4-create-and-run-a-pipeline).

Mediante las definiciones de tabla proporcionadas anteriormente, la definición de la canalización para la ADF se especifica de la siguiente manera:

		{
		    "name": "AMLDSProcessPipeline",
		    "properties":
		    {
		        "description" : "This pipeline has one Copy activity that copies data from an on-premise SQL to Azure blob",
		         "activities":
		        [
		            {
		                "name": "CopyFromSQLtoBlob",
		                "description": "Copy data from on-premise SQL server to blob",     
		                "type": "CopyActivity",
		                "inputs": [ {"name": "OnPremSQLTable"} ],
		                "outputs": [ {"name": "OutputBlobTable"} ],
		                "transformation":
		                {
		                    "source":
		                    {                               
		                        "type": "SqlSource",
		                        "sqlReaderQuery": "select * from nyctaxi_data"
		                    },
		                    "sink":
		                    {
		                        "type": "BlobSink"
		                    }   
		                },
		                "Policy":
		                {
		                    "concurrency": 3,
		                    "executionPriorityOrder": "NewestFirst",
		                    "style": "StartOfInterval",
		                    "retry": 0,
		                    "timeout": "01:00:00"
		                }       

		             },

					{
						"name": "CopyFromBlobtoSQLAzure",
						"description": "Push data to Sql Azure",		
						"type": "CopyActivity",
						"inputs": [ {"name": "OutputBlobTable"} ],
						"outputs": [ {"name": "OutputSQLAzureTable"} ],
						"transformation":
						{
							"source":
							{                               
								"type": "BlobSource"
							},
							"sink":
							{
								"type": "SqlSink",
								"WriteBatchTimeout": "00:5:00",				
							}			
						},
						"Policy":
						{
							"concurrency": 3,
							"executionPriorityOrder": "NewestFirst",
							"style": "StartOfInterval",
							"retry": 2,
							"timeout": "02:00:00"
						}
				     }
		        ]
		    }
		}

Copie esta definición de JSON de la canalización en un archivo denominado *pipelinedef.json* y guarde el archivo en una ubicación conocida (aquí se supone que es *C:\\temp\\pipelinedef.json*). Cree la canalización en la ADF con el siguiente cmdlet de Azure PowerShell.

	New-AzureDataFactoryPipeline  -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\pipelinedef.json

Confirme que puede ver la canalización en el ADF en la presentación del Portal de Azure clásico de la siguiente manera (al hacer clic en el diagrama).

![](media/machine-learning-data-science-move-sql-azure-adf/DJP1kji.png)

##<a name="adf-pipeline-start"></a>Inicio de la canalización
La canalización ya se puede ejecutar mediante el comando siguiente:

	Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp -StartDateTime startdateZ –EndDateTime enddateZ –Name AMLDSProcessPipeline

Los valores de los parámetros *startdate* y *enddate* deben reemplazarse por las fechas reales entre las que desea que se ejecute la canalización.

Una vez que se ejecuta la canalización, debe poder ver los datos que aparecen en el contenedor seleccionado para el blob, en este caso, un archivo por día.

Observe que no hemos aprovechado la funcionalidad que proporciona la ADF para canalizar los datos de forma incremental. Para obtener más detalles sobre cómo hacer esto y sobre otras capacidades que proporciona la ADF, consulte la [documentación de ADF](https://azure.microsoft.com/services/data-factory/).

<!---HONumber=AcomDC_0211_2016-->