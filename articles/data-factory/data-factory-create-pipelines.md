<properties 
	pageTitle="Creación de canalizaciones" 
	description="Descripción de las canalizaciones de Factoría de datos de Azure y procedimientos para crearlas con el fin mover y transformar datos para generar información que puede usarse para obtener una idea clara" 
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
	ms.topic="article" y
	ms.date="12/08/2015" 
	ms.author="spelluru"/>

# Descripción de canalizaciones y actividades
Este artículo ayuda a comprender las canalizaciones y actividades en Factoría de datos de Azure y cómo aprovecharlas para construir flujos de trabajo de extremo a extremo controlados por datos para su escenario o empresa. En este artículo se considera que ha visto los artículos [Información general](data-factory-introduction.md) y[Creación de conjuntos de datos](data-factory-create-datasets.md) antes.

## ¿Qué es una canalización?
**Una canalización es una agrupación lógica de actividades**. Se usan para agrupar actividades en una unidad que realiza una tarea. Para entender mejor las canalizaciones, deberá entender primero qué es una actividad.

### ¿Qué es una actividad?
Las actividades definen las acciones que se van a realizar en los datos. Cada actividad tiene cero o más [conjuntos de datos](data-factory-create-datasets.md) como entradas y genera uno o varios conjuntos de datos como salida. **Una actividad es una unidad de orquestación en Factoría de datos de Azure.**

Por ejemplo, puede utilizar una actividad de copia para organizar la copia de datos de un conjunto de datos a otro. De manera similar, puede usar una actividad de Hive que ejecute una consulta de Hive en un clúster de HDInsight de Azure para transformar o analizar los datos. Factoría de datos de Azure ofrece una amplia gama de actividades de [transformación de datos y análisis](data-factory-data-transformation-activities.md) y [movimiento de datos](data-factory-data-movement-activities.md). También puede crear una actividad personalizada de .NET para ejecutar su propio código.

Tenga en cuenta los dos conjuntos de datos siguientes:

**Base de datos SQL de Azure**

La tabla "MyTable" contiene una columna "timestampcolumn" que permite especificar la fecha y hora en la que se insertaron los datos en la base de datos.

	{
	  "name": "AzureSqlInput",
	  "properties": {
	    "type": "AzureSqlTable",
	    "linkedServiceName": "AzureSqlLinkedService",
	    "typeProperties": {
	      "tableName": "MyTable"
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

**Conjunto de datos del blob de Azure**

Los datos se copian a un blob nuevo cada hora con la ruta de acceso para el blob que refleja la fecha y hora específicas con granularidad de hora.

	{
	  "name": "AzureBlobOutput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
	      ],
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": "\t",
	        "rowDelimiter": "\n"
	      }
	    },
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}


La siguiente actividad de copia en la canalización copia los datos de SQL Azure en el almacenamiento de blobs de Azure. Toma la tabla de SQL Azure como el conjunto de datos de entrada con una frecuencia de cada hora y escribe los datos en el almacenamiento de blobs de Azure representado por el conjunto de datos "AzureBlobOutput". El conjunto de datos de salida también tiene una frecuencia de cada hora. Consulte la sección Programación y ejecución para comprender cómo se copian los datos durante la unidad de tiempo. Esta canalización tiene un período activo de 3 horas desde "2015-01-01T08:00:00" hasta "2015-01-01T11:00:00".

**Canalización:**
	
	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2015-01-01T08:00:00",
	    "end":"2015-01-01T11:00:00",
	    "description":"pipeline for copy activity",
	    "activities":[  
	      {
	        "name": "AzureSQLtoBlob",
	        "description": "copy activity",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": "AzureSQLInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureBlobOutput"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "SqlSource",
	            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
	          },
	          "sink": {
	            "type": "BlobSink"
	          }
	        },
	       "scheduler": {
	          "frequency": "Hour",
	          "interval": 1
	        },
	        "policy": {
	          "concurrency": 1,
	          "executionPriorityOrder": "OldestFirst",
	          "retry": 0,
	          "timeout": "01:00:00"
	        }
	      }
	     ]
	   }
	}

Ahora que tenemos una breve idea de lo que es una actividad, vamos volver a la canalización.
 
Una canalización es una agrupación lógica de actividades. Se usan para agrupar actividades en una unidad que realiza una tarea. **Una canalización también es la unidad de implementación y administración de las actividades.** Por ejemplo, puede que desee reunir las actividades relacionadas lógicamente como una canalización de forma que pueden estar en estado activo o en pausa juntos.

Un conjunto de datos de salida de una actividad en una canalización puede ser el conjunto de datos de entrada a otra actividad en la misma canalización o en otra diferente mediante la definición de las dependencias entre las actividades. En la sección [Programación y ejecución](#scheduling-and-execution) se trata este tema en detalle.

Los pasos típicos al crear una canalización en Factoría de datos de Azure son:

1.	Cree una factoría de datos (si no se ha creado).
2.	Cree un servicio vinculado para cada almacén de datos o proceso.
3.	Cree conjuntos de datos de entrada y salida.
4.	Cree una canalización con actividades que opere en los conjuntos de datos definidos anteriormente.

![Entidades de Factoría de datos](./media/data-factory-create-pipelines/entities.png)

Vamos a fijarnos un poco más en cómo se define una canalización.

## Anatomía de una canalización  

La estructura genérica de una canalización tiene el aspecto siguiente:

	{
	    "name": "PipelineName",
	    "properties": 
	    {
	        "description" : "pipeline description",
	        "activities":
	        [
	
	        ],
			"start": "<start date-time>",
			"end": "<end date-time>"
	    }
	}

La sección **activities** puede contener una o más actividades definidas. Cada actividad tiene la siguiente estructura de nivel superior:

	{
	    "name": "ActivityName",
	    "description": "description", 
	    "type": "<ActivityType>",
	    "inputs":  "[]",
	    "outputs":  "[]",
	    "linkedServiceName": "MyLinkedService",
	    "typeProperties":
	    {
	
	    },
	    "policy":
	    {
	    }
	    "scheduler":
	    {
	    }
	}

En la tabla siguiente se describen las propiedades dentro de las definiciones de actividad y la canalización JSON:

Etiqueta | Descripción | Obligatorio
--- | ----------- | --------
name | Nombre de la actividad o la canalización. Especifique un nombre que representa la acción que la actividad o la canalización está configurado para realizar<br/><ul><li>Número máximo de caracteres: 260</li><li>Debe comenzar con una letra, un número o un carácter de subrayado (_)</li><li>No se permiten caracteres siguientes: “.”, “+”, “?”, “/”, “<”,”>”,”*”,”%”,”&”,”:”,”\\”</li></ul> | Sí
description | Texto que describe para qué se usa la actividad o la canalización | Sí
type | Especifica el tipo de la actividad. Consulte los artículos [Actividades de movimiento de datos](data-factory-data-movement-activities.md) y [Actividades de transformación de datos](data-factory-data-transformation-activities.md) para diferentes tipos de actividades. | Sí
inputs | Tablas de entrada usadas por la actividad<p>// una tabla de entrada<br/>"inputs":  [ { "name": "inputtable1" } ],</p><p>// dos tablas de entrada <br/>"inputs":  [ { "name": "inputtable1" }, { "name": "inputtable2" } ],</p> | Sí
outputs | Tablas de salida usadas por la actividad.<p>// una tabla de salida<br/>"outputs":  [ { "name": “outputtable1” } ],</p><p>//dos tablas de salida<br/>"outputs":  [ { "name": “outputtable1” }, { "name": “outputtable2” } ],</p> | Sí
linkedServiceName | Nombre del servicio vinculado usado por la actividad. <p>Una actividad puede requerir que especifique el servicio vinculado que se vincula al entorno de proceso necesario.</p>| Sí para Actividad de HDInsight y Actividad de puntuación por lotes de Aprendizaje automático de Azure<p>No para todos los demás</p>
typeProperties | Las propiedades de la sección typeProperties dependen del tipo de la actividad. Consulte el artículo sobre cada actividad para obtener más información | No
policy | Directivas que afectan al comportamiento de la actividad en tiempo de ejecución. Si no se especifica, se usan las directivas predeterminadas. Desplácese a continuación para obtener detalles | No
start | Fecha y hora de inicio de la canalización. Debe estar en [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por ejemplo: 2014-10-14T16:32:41Z. <p>Las propiedades start y end juntas especifican un período activo para la canalización. Los segmentos de salida solo se producen en este período activo.</p> | No<p>Si se especifica un valor para la propiedad end, hay que especificar un valor para la propiedad start.</p><p>Los tiempos de inicio y finalización pueden estar vacíos para crear una canalización, pero ambos deben tener valores para establecer un período activo para que se ejecute la canalización. El período activo de una canalización también puede establecerse mediante el cmdlet Set-AzureDataFactoryPipelineActivePeriod</p>
End | Hora y fecha de finalización de la canalización. Si se especifica, debe estar en formato ISO. Por ejemplo: 2014-10-14T17:32:41Z <p>Si no se especifica, se calcula como "start + 48 horas". Para ejecutar la canalización de forma indefinida, especifique 9999-09-09 como el valor de la propiedad end.</p>| No <p>Si se especifica un valor para la propiedad start, hay que especificar un valor para la propiedad end.</p><p>Consulte las notas de la propiedad **start**.</p>
isPaused | Si está establecido en true, la canalización no se ejecutará. Valor predeterminado = false. Puede usar esta propiedad para habilitar o deshabilitar. | No
programador | La propiedad "scheduler" se usa para definir la programación deseada de la actividad. Sus subpropiedades son las mismas que las de la [propiedad availability en un conjunto de datos](data-factory-create-datasets.md#Availability). | No |   

### Tipos de actividad
Factoría de datos de Azure ofrece una amplia gama de actividades de [movimiento de datos](data-factory-data-movement-activities.md) y de [transformación de datos](data-factory-data-transformation-activities.md).

### Directivas
Las directivas afectan al comportamiento en tiempo de ejecución de una actividad, concretamente cuando se procesa el segmento de una tabla. En la tabla siguiente se proporciona los detalles.

Propiedad | Valores permitidos | Valor predeterminado | Descripción
-------- | ----------- | -------------- | ---------------
simultaneidad | Integer <p>Valor máximo: 10</p> | 1 | Número de ejecuciones simultáneas de la actividad.<p>Determina el número de ejecuciones paralelas de la actividad que pueden tener lugar en distintos segmentos. Por ejemplo, si una actividad tiene que recorrer un gran conjunto de datos disponibles, tener una mayor simultaneidad acelera el procesamiento de datos.</p> 
executionPriorityOrder | NewestFirst<p>OldestFirst</p> | OldestFirst | Determina el orden de los segmentos de datos que se están procesando.<p>Por ejemplo, si tiene 2 segmentos (que tienen lugar uno a las 4 p.m. y el otro a las 5 p.m.) y ambos están pendientes de ejecución. Si establece que executionPriorityOrder sea NewestFirst, se procesará primero el segmento de las 5 p.m. De forma similar, si establece que executionPriorityORder sea OldestFIrst, se procesará el segmento de las 4 p.m.</p> 
retry | Integer<p>El valor máximo puede ser 10</p> | 3 | Número de reintentos antes de que el procesamiento de datos del segmento se marque como error. La ejecución de la actividad de un segmento de datos se vuelve a intentar hasta el número de reintentos especificado. El reintento se realiza tan pronto como sea posible después del error.
timeout | TimeSpan | 00:00:00 | Tiempo de espera para la actividad. Ejemplo: 00:10:00 (implica un tiempo de espera 10 minutos)<p>Si no se especifica ningún valor o es 0, el tiempo de espera es infinito.</p><p>Si el tiempo de procesamiento de los datos en un segmento supera el valor de tiempo de espera, se cancela y el sistema vuelve a intentar el procesamiento. El número de reintentos depende de la propiedad retry. Cuando se excede el tiempo de espera, el estado será TimedOut.</p>
delay | TimeSpan | 00:00:00 | Especifica el retraso antes de iniciar el procesamiento de los datos del segmento.<p>La ejecución de la actividad de un segmento de datos se inicia después de que transcurra el retraso más allá del tiempo de ejecución esperado.</p><p>Ejemplo: 00:10:00 (implica un retraso de 10 minutos)</p>
longRetry | Integer<p>Valor máximo: 10</p> | 1 | El número de reintentos largos antes de que haya error en la ejecución de los segmentos.<p>Los intentos de longRetry se espacian de acuerdo con longRetryInterval. Por tanto, si necesita especificar un tiempo entre reintentos, utilice longRetry. Si se especifican Retry y longRetry, cada intento de longRetry incluirá el número de intentos de Retry y el número máximo de intentos será Retry * longRetry.</p><p>Por ejemplo, si tenemos lo siguiente en la directiva de la actividad:<br/>Retry: 3<br/>longRetry: 2<br/>longRetryInterval: 01:00:00<br/></p><p>Se supone que existe un solo segmento para ejecutar (el estado es PendingExecution) y la ejecución de la actividad no se puede realizar nunca. Inicialmente habría tres intentos consecutivos de ejecución. Después de cada intento, el estado del segmento sería Retry. Después de los 3 primeros intentos, el estado del segmento sería LongRetry.</p><p>Después de una hora (es decir, el valor de longRetryInteval), se produciría otro conjunto de 3 intentos consecutivos de ejecución. Después de eso, el estado del segmento sería Failed y ya no se realizarían más intentos. Por tanto, en total se realizaron 6 intentos.</p><p>Nota: si una ejecución se realiza correctamente, el estado del segmento sería Ready y no se realizaría ningún otro reintento.</p><p>longRetry puede usarse en situaciones donde llegan datos dependientes a horas no deterministas o el entorno general en el que se produce el procesamiento de datos es poco confiable. En esos casos es posible que realizar reintentos uno tras otro no ayude, mientras que hacerlo después de un intervalo de tiempo puede generar el resultado deseado.</p><p>Advertencia: no establezca valores altos para longRetry o longRetryInterval. Normalmente, los valores más altos implican otros problemas sistémicos que se eliminan con esto</p> 
longRetryInterval | TimeSpan | 00:00:00 | El retraso entre reintentos largos 

## Creación y administración de una canalización
Factoría de datos de Azure proporciona varios mecanismos para crear e implementar canalizaciones (que a su vez contienen una o varias actividades).

### Uso del Portal de Azure

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).
2. Navegue a la instancia de Factoría de datos de Azure en la que desea crear una canalización
3. Haga clic en el icono **Crear e implementar** en la lente **Resumen**. 
 
	![Icono Crear e implementar](./media/data-factory-create-pipelines/author-deploy-tile.png)

4. Haga clic en **Nueva canalización** en la barra de comandos.

	![Botón Nueva canalización](./media/data-factory-create-pipelines/new-pipeline-button.png)

5. Aparecerá la ventana del editor con una plantilla de canalización JSON.

	![Editor de canalizaciones](./media/data-factory-create-pipelines/pipeline-in-editor.png)

6. Una vez finalizada la creación de la canalización, haga clic en **Implementar** en la barra de comandos para implementar la canalización.

	**Nota:** durante la implementación, el servicio de Factoría de datos de Azure realiza algunas comprobaciones de validación para ayudar a solucionar algunos problemas comunes. Si se produce un error, se mostrará la información correspondiente. Realice las acciones correctivas y, a continuación, vuelva a implementar la canalización creada. Puede usar el editor para actualizar y eliminar una canalización.

### Uso del complemento Visual Studio
Puede usar Visual Studio para crear e implementar canalizaciones en Factoría de datos de Azure. Para obtener más información, consulte [Tutorial: Copia de datos de Almacenamiento de Azure en SQL de Azure (Visual Studio)](data-factory-get-started-using-vs.md).

### Uso de Azure PowerShell
Puede usar Azure PowerShell para crear canalizaciones en Factoría de datos de Azure. Digamos que ha definido la canalización JSON en un archivo c:\\DPWikisample.json. Puede cargarlo en su instancia de Factoría de datos de Azure, tal como se muestra en el ejemplo siguiente.

	New-AzureRmDataFactoryPipeline -ResourceGroupName ADF -Name DPWikisample -DataFactoryName wikiADF -File c:\DPWikisample.json

Para más información sobre este cmdlet, consulte el [cmdlet New-AzureDataFactoryPipeline](https://msdn.microsoft.com/library/mt619358.aspx).

### Uso de la API de REST
También puede crear e implementar la canalización mediante las API de REST. Este mecanismo se puede aprovechar para crear canalizaciones mediante programación. Para obtener más información sobre esto, consulte [Creación o actualización de una canalización](https://msdn.microsoft.com/library/azure/dn906741.aspx).

### Uso del SDK de .NET
También puede crear e implementar la canalización mediante el SDK de .NET. Este mecanismo se puede aprovechar para crear canalizaciones mediante programación. Para obtener más información sobre esto consulte [Creación, administración y supervisión de factorías de datos mediante programación](data-factory-create-data-factories-programmatically.md).


## Programación y ejecución
Hasta ahora se ha descrito lo que son las canalizaciones y las actividades. También se echó un vistazo a cómo se definen y se vio con detalle las actividades en Factoría de datos de Azure. Ahora veremos cómo se ejecutan.

Una canalización solo está activa entre su hora de inicio y de finalización. No se ejecuta antes de la hora de inicio ni después de la hora de finalización. Si la canalización está en pausa, no se ejecutará, independientemente de su hora de inicio y de finalización. Para que se ejecute una canalización, no debe estar en pausa.

De hecho, no se ejecuta la canalización. Se ejecutan las actividades de la canalización. No obstante, lo hacen en el contexto general de la canalización. Consulte [Programación y ejecución](data-factory-scheduling-and-execution.md) para comprender cómo funciona la programación y la ejecución en Factoría de datos de Azure.

## Administración y supervisión  
Una vez implementada una canalización, puede administrar y supervisar la canalizaciones, segmentos y ejecuciones. Obtenga más información sobre este tema aquí: [Supervisión y administración de canalizaciones](data-factory-monitor-manage-pipelines.md).

## Pasos siguientes

- Conozca [Programación y ejecución en Factoría de datos de Azure](data-factory-scheduling-and-execution.md).  
- Obtenga más información sobre las [capacidades de movimiento de datos](data-factory-data-movement-activities.md) y [transformación de datos](data-factory-data-transformation-activities.md) en Factoría de datos de Azure
- Conozca [Administración y supervisión Factoría de datos de Azure](data-factory-monitor-manage-pipelines.md).
- [Creación e implementación de su primera canalización](data-factory-build-your-first-pipeline.md). 


 

   













 
 


 

 

<!---HONumber=AcomDC_1210_2015-->
