<properties
	pageTitle="Introducción a la Factoría de datos de Azure (Visual Studio)"
	description="En este tutorial, creará una canalización de la factoría de datos de Azure de ejemplo mediante Visual Studio."
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

# Compilación de la primera canalización mediante Visual Studio
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-build-your-first-pipeline.md)
- [Using Data Factory Editor](data-factory-build-your-first-pipeline-using-editor.md)
- [Using PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Using Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [Using Resource Manager Template](data-factory-build-your-first-pipeline-using-arm.md)


En este artículo, aprenderá a usar Microsoft Visual Studio para crear su primera factoría de datos de Azure.

## Requisitos previos

1. **Debe** leer la sección [Tutorial Overview](data-factory-build-your-first-pipeline.md) del artículo y completar los pasos de los requisitos previos antes de continuar.
2. Este artículo no ofrece información general conceptual sobre el servicio Factoría de datos de Azure. Para más información del servicio, le recomendamos que consulte el artículo [Introducción al servicio Factoría de datos de Azure](data-factory-introduction.md).  

## Tutorial: Creación e implementación de las entidades de Factoría de datos con Visual Studio 

### Requisitos previos

Debe tener lo siguiente instalado en el equipo:

- Visual Studio 2013 o Visual Studio 2015.
- Descargue el SDK de Azure para Visual Studio 2013 o Visual Studio 2015. Vaya a la [página Descargas de Azure](https://azure.microsoft.com/downloads/) y haga clic en **VS 2013** o **VS 2015** en la sección **.NET**.
- Descargue el último complemento de Factoría de datos de Azure para Visual Studio : [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) o [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005). Si usa Visual Studio 2013, también puede actualizar el complemento mediante el proceso siguiente: haga clic en el menú **Herramientas** -> **Extensiones y actualizaciones** -> **En línea** -> **Galería de Visual Studio** -> **Herramientas de Factoría de datos de Microsoft Azure para Visual Studio** -> **Actualizar**. 
	
	

### Creación del proyecto de Visual Studio 
1. Inicie **Visual Studio 2013** o **Visual Studio 2015**. Haga clic en **Archivo**, seleccione **Nuevo** y, luego, haga clic en **Proyecto**. Debe aparecer el cuadro de diálogo **Nuevo proyecto**.  
2. En el cuadro de diálogo **Nuevo proyecto**, seleccione la plantilla **DataFactory** y haga clic en **Proyecto vacío de Factoría de datos**.   

	![Cuadro de diálogo Nuevo proyecto](./media/data-factory-build-your-first-pipeline-using-vs/new-project-dialog.png)

3. Escriba un **nombre** para el proyecto, la **ubicación** y un nombre para la **solución**; a continuación, haga clic en **Aceptar**.

	![Explorador de soluciones](./media/data-factory-build-your-first-pipeline-using-vs/solution-explorer.png)

### Crear servicios vinculados
Una factoría de datos puede tener una o más canalizaciones. Una canalización puede tener una o más actividades. Por ejemplo, una actividad de copia para copiar datos desde un origen a un almacén de datos de destino o una actividad de Hive de HDInsight para ejecutar un script de Hive que transforme los datos de entrada para generar datos de salida. Especificará el nombre y la configuración de la factoría de datos más adelante al publicar la solución de Factoría de datos.

En este paso, vinculará su cuenta de Almacenamiento de Azure y el clúster de HDInsight de Azure a petición con su factoría de datos. La cuenta de Almacenamiento de Azure contendrá los datos de entrada y salida de la canalización de este ejemplo. Para ejecutar el script de Hive especificado en la actividad de la canalización en este ejemplo, se usa el servicio vinculado de HDInsight. Debe identificar qué datos de almacén o servicios de proceso se usan en el escenario y vincular dichos servicios con la factoría de datos mediante la creación de servicios vinculados.

#### Creación de un servicio vinculado de Almacenamiento de Azure
En este paso, vinculará su cuenta de Almacenamiento de Azure con su factoría de datos. Para este tutorial, usará la misma cuenta de Almacenamiento de Azure para almacenar los datos de entrada y salida y el archivo de script de HQL.

4. Haga clic con el botón derecho en **Servicios vinculados** en el Explorador de soluciones, seleccione **Agregar** y haga clic en **Nuevo elemento**.      
5. En el cuadro de diálogo **Agregar nuevo elemento**, seleccione **Servicio vinculado de Almacenamiento de Azure** en la lista y haga clic en **Agregar**. 
3. Reemplace los valores de **accountname** y **accountkey** por el nombre de la cuenta de Almacenamiento de Azure y su clave. Para aprender a obtener una clave de acceso de almacenamiento, consulte [Visualización y copia de las claves de acceso de almacenamiento](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).

	![Servicio vinculado de Almacenamiento de Azure](./media/data-factory-build-your-first-pipeline-using-vs/azure-storage-linked-service.png)

4. Guarde el archivo **AzureStorageLinkedService1.json**.

#### Creación de un servicio vinculado de HDInsight de Azure
En este paso, vinculará un clúster de HDInsight a petición con la factoría de datos. El clúster de HDInsight se crea automáticamente en tiempo de ejecución y se elimina después de hacer el procesamiento y de permanecer inactivo durante el período de tiempo especificado. Puede usar su propio clúster de HDInsight en lugar de usar un clúster de HDInsight a petición. Consulte [Servicios vinculados de procesos](data-factory-compute-linked-services.md) para más información.

1. En el **Explorador de soluciones**, haga clic con el botón derecho en **Servicios vinculados**, seleccione **Agregar** y haga clic en **Nuevo elemento**.
2. Seleccione **Servicio vinculado de HDInsight a petición** y haga clic en **Agregar**. 
3. Reemplace el código **JSON** por el siguiente:

		{
		  "name": "HDInsightOnDemandLinkedService",
		  "properties": {
		    "type": "HDInsightOnDemand",
		    "typeProperties": {
		      "version": "3.2",
		      "clusterSize": 1,
		      "timeToLive": "00:30:00",
		      "linkedServiceName": "AzureStorageLinkedService1"
		    }
		  }
		}
	
	En la siguiente tabla se ofrecen descripciones de las propiedades JSON que se usan en el fragmento de código:
	
	Propiedad | Descripción
	-------- | -----------
	Versión | Con esto se especifica que la versión de HDInsight se crea para que sea 3.2. 
	ClusterSize | Así se crea un clúster de HDInsight de un nodo. 
	TimeToLive | Especifica el tiempo de inactividad del clúster de HDInsight, antes de que se elimine.
	linkedServiceName | Especifica la cuenta de almacenamiento que se usará para almacenar los registros que genere HDInsight.

	Tenga en cuenta lo siguiente:
	
	- Factoría de datos crea automáticamente un clúster de HDInsight **basado en Windows** con el anterior código JSON. También podría hacer que cree un clúster de HDInsight **basado en Linux**. Consulte la sección [Servicio vinculado a petición de HDInsight de Azure](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) para más información. 
	- Puede usar **su propio clúster de HDInsight** en lugar de usar un clúster de HDInsight a petición. Consulte [Servicio vinculado de HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) para más información.
	- El clúster de HDInsight crea un **contenedor predeterminado** en Almacenamiento de blobs especificado en el código JSON (**linkedServiceName**). HDInsight elimina este contenedor cuando se elimina el clúster. Esto es así por diseño. Con el servicio vinculado de HDInsight a petición, se crea un clúster de HDInsight cada vez que un segmento debe procesarse, a menos que haya un clúster existente activo (**timeToLive**), y se elimina cuando se realiza el procesamiento.
	
		A medida que se procesan más segmentos, verá numerosos contenedores en su Almacenamiento de blobs de Azure. Si no los necesita para solucionar problemas de trabajos, puede eliminarlos para reducir el costo de almacenamiento. El nombre de estos contenedores siguen un patrón: "adf**nombreDeFactoríaDeDatos**-**linkedservicename**-datetimestamp". Use herramientas como [Explorador de almacenamiento de Microsoft](http://storageexplorer.com/) para eliminar contenedores de Almacenamiento de blobs de Azure.

	Consulte la sección [Servicio vinculado a petición de HDInsight de Azure](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) para más información. 
4. Guarde el archivo **HDInsightOnDemandLinkedService1.json**.

### Creación de conjuntos de datos
En este paso, creará conjuntos de datos que representen los datos de entrada y salida para el procesamiento de Hive. Estos conjuntos de datos hacen referencia al servicio **AzureStorageLinkedService1** que creó anteriormente en este tutorial. El servicio vinculado apunta a una cuenta de Almacenamiento de Azure y los conjuntos de datos especifican el contenedor, la carpeta y el nombre de archivo en el almacenamiento que contiene los datos de entrada y salida.

#### Creación del conjunto de datos de entrada

1. En el **Explorador de soluciones**, haga clic con el botón derecho en **Tablas**, seleccione **Agregar** y haga clic en **Nuevo elemento**. 
2. Seleccione **Blob de Azure** en la lista, cambie el nombre del archivo a **OutputDataset.json** y haga clic en **Agregar**.
3. Reemplace el código **JSON** del editor por lo siguiente: 

	En el fragmento de código JSON, va a crear un conjunto de datos denominado **AzureBlobInput** que representa los datos de entrada para una actividad de la canalización. Además, va a especificar que los datos de entrada se coloquen en el contenedor de blobs llamado **adfgetstarted** y en la carpeta llamada **inputdata**.
		
		{
			"name": "AzureBlobInput",
		    "properties": {
		        "type": "AzureBlob",
		        "linkedServiceName": "AzureStorageLinkedService1",
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

	En la siguiente tabla se ofrecen descripciones de las propiedades JSON que se usan en el fragmento de código:

	| Propiedad | Descripción |
	| :------- | :---------- |
	| type | La propiedad type se establece en AzureBlob porque los datos residen en Almacenamiento de blobs de Azure. |  
	| linkedServiceName | Hace referencia al servicio AzureStorageLinkedService1 que creó anteriormente. |
	| fileName | Esta propiedad es opcional. Si omite esta propiedad, se seleccionan todos los archivos de folderPath. En este caso, se procesa solo el archivo input.log. |
	| type | Los archivos de registro están en formato de texto, por tanto, usaremos TextFormat. | 
	| columnDelimiter | Las columnas de los archivos de registro están delimitadas por , (coma). |
	| frecuencia/intervalo | La frecuencia está establecida en Mes y el intervalo es 1, lo que significa que los segmentos de entrada estarán disponibles cada mes. | 
	| external | Esta propiedad se establece en true si el servicio Factoría de datos no ha generado los datos de entrada. | 
	  
	
3. Guarde el archivo **InputDataset.json**.

 
#### Creación del conjunto de datos de salida
Ahora, va a crear el conjunto de datos de salida que representa los datos de salida almacenados en Almacenamiento de blobs de Azure.

1. En el **Explorador de soluciones**, haga clic con el botón derecho en **Tablas**, seleccione **Agregar** y haga clic en **Nuevo elemento**. 
2. Seleccione **Blob de Azure** en la lista, cambie el nombre del archivo a **OutputDataset.json** y haga clic en **Agregar**. 
3. Reemplace el código **JSON** del editor por lo siguiente: 

	En el fragmento de código JSON, cree un conjunto de datos llamado **AzureBlobOutput** y especifique la estructura de los datos que generará el script de Hive. Además, especifique que los resultados se almacenen en el contenedor de blobs llamado **adfgetstarted** y en la carpeta llamada **partitioneddata**. La sección **availability** especifica que el conjunto de datos de salida se genera mensualmente.
	
		{
		  "name": "AzureBlobOutput",
		  "properties": {
		    "type": "AzureBlob",
		    "linkedServiceName": "AzureStorageLinkedService1",
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

	Consulte la sección **Creación del conjunto de datos de entrada** para obtener las descripciones de estas propiedades. No establezca la propiedad externa en un conjunto de datos ya que este es generado por el servicio Factoría de datos.

4. Guarde el archivo **OutputDataset.json**.


### Creación de la primera canalización
En este paso, creará la primera canalización con una actividad **HDInsightHive**. Tenga en cuenta que el segmento de entrada está disponible mensualmente (frecuencia: mes, intervalo: 1), el segmento de salida se genera mensualmente y la propiedad de programador también se establece en mensual para la actividad (consulte a continuación). La configuración del conjunto de datos de salida y la del programador de la actividad deben coincidir. En este momento, el conjunto de datos de salida es lo que impulsa la programación, por lo que debe crear un conjunto de datos de salida incluso si la actividad no genera ninguna salida. Si la actividad no toma ninguna entrada, puede omitir la creación del conjunto de datos de entrada. Al final de esta sección se explican las propiedades usadas en el siguiente JSON.

1. En el **Explorador de soluciones**, haga clic con el botón derecho en **Canalizaciones**, seleccione **Agregar** y haga clic en **Nuevo elemento**. 
2. Seleccione **Canalización de transformación de Hive** en la lista y haga clic en **Agregar**. 
3. Reemplace el código **JSON** por el siguiente fragmento de código.

	> [AZURE.IMPORTANT] Reemplace **storageaccountname** por el nombre de la cuenta de almacenamiento.

		{
		    "name": "MyFirstPipeline",
		    "properties": {
		        "description": "My first Azure Data Factory pipeline",
		        "activities": [
		            {
		                "type": "HDInsightHive",
		                "typeProperties": {
		                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
		                    "scriptLinkedService": "AzureStorageLinkedService1",
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
	
	En el fragmento de código JSON, se crea una canalización que consta de una sola actividad que usa Hive para procesar los datos en un clúster de HDInsight.
	
	El archivo de script de Hive, **partitionweblogs.hql**, se almacena en la cuenta de Almacenamiento de Azure (especificada mediante la propiedad scriptLinkedService, llamada **AzureStorageLinkedService1**) en una carpeta denominada **script** del contenedor **adfgetstarted**.

	La sección **defines** se usa para especificar la configuración de tiempo de ejecución que se pasará al script de Hive como valores de configuración de Hive (por ejemplo, ${hiveconf:inputtable}, ${hiveconf:partitionedtable}).

	Las propiedades **start** y **end** de la canalización especifican el período activo de esta.

	En el código JSON de la actividad, se especifica que el script de Hive se ejecuta en el proceso que especifica el servicio vinculado **linkedServiceName**: **HDInsightOnDemandLinkedService**.

3. Guarde el archivo **HiveActivity1.json**.

### Adición de partitionweblogs.hql e input.log como una dependencia 

1. Haga clic con el botón derecho en **Dependencias** en la ventana del **Explorador de soluciones**, seleccione **Agregar** y haga clic en **Elemento existente**.  
2. Navegue hasta **C:\ADFGettingStarted**, seleccione los archivos **partitionweblogs.hql** e **input.log** y haga clic en **Agregar**. Se crearon estos dos archivos como parte de los requisitos previos de la sección [Tutorial Overview](data-factory-build-your-first-pipeline.md).

Al publicar la solución en el paso siguiente, se carga el archivo **partitionweblogs.hql** en la carpeta de scripts del contenedor de blobs **adfgetstarted**.

### Publicación e implementación de las entidades de Factoría de datos

18. Haga clic con el botón derecho en el proyecto en el Explorador de soluciones y haga clic en **Publicar**. 
19. Si ve el cuadro de diálogo **Iniciar sesión en tu cuenta Microsoft**, escriba sus credenciales para la cuenta que tiene la suscripción de Azure y haga clic en **Iniciar sesión**.
20. Debería ver el siguiente cuadro de diálogo:

	![Cuadro de diálogo Publicar](./media/data-factory-build-your-first-pipeline-using-vs/publish.png)

21. En la página Configurar la factoría de datos, haga lo siguiente:
	1. Seleccione la opción **Crear nueva factoría de datos**.
	2. Escriba **FirstDataFactoryUsingVS** como **nombre**. 
	
		> [AZURE.IMPORTANT] El nombre de la Factoría de datos de Azure debe ser único de forma global. Si recibe el error **El nombre "FirstDataFactoryUsingVS" de la factoría de datos no está disponible** al realizar la publicación, cambie el nombre (por ejemplo, a suNombreFirstDataFactoryUsingVS). Consulte el tema [Factoría de datos: reglas de nomenclatura](data-factory-naming-rules.md) para las reglas de nomenclatura para los artefactos de Factoría de datos.
		> 
		> El nombre de la factoría de datos se puede registrar como un nombre DNS en el futuro y, por lo tanto, hacerse públicamente visible.
	3. Seleccione la suscripción correcta en el campo **Suscripción**. 
	4. Seleccione el **grupo de recursos** para la factoría de datos que se va a crear. 
	5. Seleccione la **Región** de la factoría de datos. 
	6. Haga clic en **Siguiente** para cambiar a la página **Publicar elementos**. (Presione la tecla **TAB** para salir del campo Nombre si el botón **Siguiente** está deshabilitado). 
23. En la página **Publicar elementos**, asegúrese de que todas las entidades de Factorías de datos están seleccionadas y haga clic en **Siguiente** para cambiar a la página **Resumen**.     
24. Revise el resumen y haga clic en **Siguiente** para iniciar el proceso de implementación y ver el **Estado de implementación**.
25. En la página **Estado de implementación**, debería ver el estado del proceso de implementación. Cuando se haya completado la implementación, haga clic en Finalizar. 
 
## Paso 4: Supervisión de la canalización

6. Inicie sesión en el [Portal de Azure](https://portal.azure.com/) y realice lo siguiente:
	1. Haga clic en **Examinar** y seleccione **Factorías de datos**.
		![Examinar factorías de datos](./media/data-factory-build-your-first-pipeline-using-vs/browse-datafactories.png) 
	2. Seleccione **FirstDataFactoryUsingVS** en la lista de factorías de datos. 
7. En la página principal de la factoría de datos, haga clic en **Diagrama**.
  
	![Icono Diagrama](./media/data-factory-build-your-first-pipeline-using-vs/diagram-tile.png)
7. En la Vista de diagrama, verá información general de las canalizaciones y conjuntos de datos empleados en este tutorial.
	
	![Vista de diagrama](./media/data-factory-build-your-first-pipeline-using-vs/diagram-view-2.png) 
8. Para ver todas las actividades de la canalización, haga clic con el botón derecho en la canalización en el diagrama y haga clic en Abrir canalización. 

	![Menú Abrir canalización](./media/data-factory-build-your-first-pipeline-using-vs/open-pipeline-menu.png)
9. Confirme que la actividad de HDInsightHive está en la canalización. 
  
	![Vista Abrir canalización](./media/data-factory-build-your-first-pipeline-using-vs/open-pipeline-view.png)

	Para volver a la vista anterior, haga clic en **Factoría de datos** en el menú de la barra de ruta de navegación en la parte superior. 
10. En la **Vista de diagrama**, haga doble clic en el conjunto de datos **AzureBlobInput**. Confirme que el estado del segmento es **Listo**. Puede que el segmento tarde un par de minutos en aparecer con ese estado. Si esto no sucede después de esperar un tiempo, compruebe si el archivo de entrada (input.log) está en el contenedor (adfgetstarted) y en la carpeta (inputdata) correctos.

	![Segmento de entrada con estado Listo](./media/data-factory-build-your-first-pipeline-using-vs/input-slice-ready.png)
11. Haga clic en **X** para cerrar la hoja **AzureBlobInput**. 
12. En la **Vista de diagrama**, haga doble clic en el conjunto de datos **AzureBlobOutput**. Verá que el segmento se está procesando.

	![Dataset](./media/data-factory-build-your-first-pipeline-using-vs/dataset-blade.png)
9. Cuando finalice el procesamiento, el segmento aparecerá con el estado **Listo**.
	>[AZURE.IMPORTANT] La creación de un clúster de HDInsight a petición normalmente tarda algún tiempo (20 minutos aproximadamente).  

	![Dataset](./media/data-factory-build-your-first-pipeline-using-vs/dataset-slice-ready.png)
	
10. Cuando el segmento se encuentre en el estado **Listo**, busque los datos de salida en la carpeta **partitioneddata** del contenedor **adfgetstarted** de Almacenamiento de blobs.
 
	![datos de salida](./media/data-factory-build-your-first-pipeline-using-vs/three-ouptut-files.png)

## Uso del Explorador de servidores para revisar las entidades de Factoría de datos

1. En **Visual Studio** haga clic en **Vista** en el menú y haga clic en **Explorador de servidores**.
2. En la ventana Explorador de servidores, expanda **Azure** y **Factoría de datos**. Si aparece **Iniciar sesión en Visual Studio**, escriba la **cuenta** asociada a su suscripción de Azure y haga clic en **Continuar**. Escriba la **contraseña** y haga clic en **Iniciar sesión**. Visual Studio intenta obtener información acerca de todas las factorías de datos de Azure en su suscripción. El estado de esta operación se verá en la ventana **Lista de tareas de Factoría de datos**.

	![Explorador de servidores](./media/data-factory-build-your-first-pipeline-using-vs/server-explorer.png)
3. Puede hacer clic con el botón derecho en una factoría de datos y seleccionar **Exportar Factoría de datos a nuevo proyecto** para crear un proyecto de Visual Studio basado en una factoría de datos existente.

	![Exportación de la factoría de datos](./media/data-factory-build-your-first-pipeline-using-vs/export-data-factory-menu.png)

## Actualización de herramientas de Factoría de datos para Visual Studio

Para actualizar las herramientas de Factoría de datos de Azure para Visual Studio, haga lo siguiente:

1. Haga clic en el menú **Herramientas** y seleccione **Extensiones y actualizaciones**.
2. Seleccione **Actualizaciones** en el panel izquierdo y, luego, seleccione **Galería de Visual Studio**.
3. Seleccione **Herramientas de Factoría de datos de Azure para Visual Studio** y haga clic en **Actualizar**. Si no ve esta entrada, ya tiene la versión más reciente de las herramientas. 

Consulte [Supervisión y administración de canalizaciones de la Factoría de datos de Azure](data-factory-monitor-manage-pipelines.md) para obtener instrucciones sobre cómo usar el Portal de Azure para supervisar la canalización y los conjuntos de datos creados en este tutorial.
 

## Pasos siguientes
En este artículo, creó una canalización con una actividad de transformación (actividad de HDInsight) que ejecuta un script de Hive en un clúster de HDInsight a petición. Para ver cómo se usa una actividad de copia para copiar datos de un blob de Azure en SQL Azure, consulte [Tutorial: Copia de datos de un blob de Azure a SQL Azure](data-factory-get-started.md).
  

<!---HONumber=AcomDC_0128_2016-->