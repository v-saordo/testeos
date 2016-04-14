<properties 
	pageTitle="Uso de Apache Spark para crear aplicaciones de Aprendizaje automático en HDInsight | Microsoft Azure" 
	description="Instrucciones paso a paso para usar cuadernos con Apache Spark para crear aplicaciones de aprendizaje automático" 
	services="hdinsight" 
	documentationCenter="" 
	authors="nitinme" 
	manager="paulettm" 
	editor="cgronlun"
	tags="azure-portal"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/17/2016" 
	ms.author="nitinme"/>


# Aprendizaje automático: Análisis predictivo de datos de inspección de alimentos mediante MLlib con Spark en HDInsight (Linux)

> [AZURE.TIP] Este tutorial también está disponible como un cuaderno de Jupyter Notebook en un clúster Spark (Linux) que se crea en HDInsight. La experiencia del cuaderno le permite ejecutar los fragmentos de código de Python desde el propio cuaderno. Para realizar el tutorial desde dentro de un cuaderno, cree un clúster Spark, inicie un cuaderno de Jupyter Notebook (`https://CLUSTERNAME.azurehdinsight.net/jupyter`) y luego ejecute el cuaderno **Spark Machine Learning - Predictive analysis on food inspection data using MLLib.ipynb** en la carpeta **Python**.


Este artículo muestra cómo usar **MLLib**, las bibliotecas integradas de aprendizaje automático de Spark, para llevar a cabo un análisis predictivo sencillo en un conjunto de datos abierto. MLLib es una biblioteca básica de Spark que proporciona varias utilidades que ayudan en las tareas de aprendizaje automático, incluye utilidades que son adecuadas para:

* Clasificación

* Regresión

* Agrupación en clústeres

* Modelado de tema

* Descomposición en valores singulares (SVD) y Análisis de los componentes principales (PCA)

* Comprobación de hipótesis y cálculo de estadísticas de ejemplo

Este artículo presenta un enfoque sencillo de la *clasificación* a través de regresión logística.

## ¿Qué entendemos por clasificación y por regresión logística?

*Clasificación*, una tarea muy común en el aprendizaje automático, es el proceso de ordenación de datos de entrada en categorías. El trabajo de averiguar cómo asignar "etiquetas" a las entradas de datos que se proporcionen lo realiza un algoritmo de clasificación. Por ejemplo, imagine un algoritmo de aprendizaje automático que acepta información bursátil como entrada y divide las existencias en dos categorías: acciones que se deberían vender y acciones que se deberían conservar.

La regresión logística es el algoritmo que se usa para la clasificación. La API de regresión logística de Spark es útil para la *clasificación binaria*, o para la clasificación de datos de entrada en uno de los dos grupos. Para obtener más información acerca de la regresión logística, consulte [Wikipedia](https://en.wikipedia.org/wiki/Logistic_regression).

En resumen, el proceso de regresión logística genera una *función logística* que se puede usar para predecir la probabilidad de que un vector de entrada pertenezca a un grupo o al otro.

## ¿Cuál es la meta de este artículo?

Va a usar Spark para realizar un análisis predictivo sobre datos de inspección de alimentos (**Food\_Inspections1.csv**) que se han adquirido a través del [portal de datos de la ciudad de Chicago](https://data.cityofchicago.org/). Este conjunto de datos contiene información sobre los controles alimentarios que se realizaron en Chicago, incluida la información sobre cada establecimiento de alimentos que se inspeccionó, las infracciones que se encontraron (si existen) y los resultados de la inspección.

En los pasos siguientes, va a desarrollar un modelo para ver lo que es necesario para superar o no una inspección de alimentos.

## Empiece a crear una aplicación de aprendizaje automático mediante Spark MLlib

1. Desde el [Portal de vista previa de Azure](https://portal.azure.com/), en el panel de inicio, haga clic en el icono del clúster Spark (si lo ha anclado al panel de inicio). También puede navegar hasta el clúster en **Examinar todo** > **Clústeres de HDInsight**.   

2. En la hoja del clúster Spark, haga clic en **Vínculos rápidos** y, luego, en la hoja **Panel de clúster**, haga clic en **Jupyter Notebook**. Cuando se le pida, escriba las credenciales del clúster.

	> [AZURE.NOTE] También puede comunicarse con el equipo Jupyter Notebook en el clúster si abre la siguiente dirección URL en el explorador. Reemplace __CLUSTERNAME__ por el nombre del clúster:
	>
	> `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Cree un nuevo notebook. Haga clic en **New** (Nuevo) y luego en **PySpark**.

	![Crear un nuevo cuaderno de Jupyter](./media/hdinsight-apache-spark-machine-learning-mllib-ipython/hdispark.note.jupyter.createnotebook.png "Crear un nuevo cuaderno de Jupyter")

3. Se crea y se abre un nuevo cuaderno con el nombre Untitled.pynb. Haga clic en el nombre del cuaderno en la parte superior y escriba un nombre descriptivo.

	![Proporcionar un nombre para el cuaderno](./media/hdinsight-apache-spark-machine-learning-mllib-ipython/hdispark.note.jupyter.notebook.name.png "Proporcionar un nombre para el cuaderno")

3. Dado que creó un cuaderno con el kernel PySpark, no necesitará crear ningún contexto explícitamente. Los contextos Spark, SQL y Hive se crearán automáticamente al ejecutar la primera celda de código. Puede empezar a crear la aplicación de aprendizaje automático importando los tipos necesarios para este escenario. Para ello, coloque el cursor en la celda y presione **MAYÚS + ENTRAR**.


		from pyspark.ml import Pipeline
		from pyspark.ml.classification import LogisticRegression
		from pyspark.ml.feature import HashingTF, Tokenizer
		from pyspark.sql import Row
		from pyspark.sql.functions import UserDefinedFunction
		from pyspark.sql.types import *

## Construcción de una trama de datos de entrada

Ya tenemos un SQLContext que podemos usar para realizar las transformaciones de datos estructurados. La primera tarea consiste en cargar los datos de ejemplo ((**Food\_Inspections1.csv**)) en una *trama de datos* de SQL Spark. Los fragmentos de código siguientes consideran que los datos ya se han cargado en el contenedor de almacenamiento predeterminado asociado con el clúster Spark.

1. Dado que los datos sin procesar están en formato CSV, tiene que usar el contexto de Spark para extraer cada línea del archivo en memoria como texto no estructurado; a continuación, utilice la biblioteca CSV de Python para analizar cada línea individualmente. 


		def csvParse(s):
		    import csv
		    from StringIO import StringIO
		    sio = StringIO(s)
		    value = csv.reader(sio).next()
		    sio.close()
		    return value
		
		inspections = sc.textFile('wasb:///HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections1.csv')\
		                .map(csvParse)


2. Ahora tenemos el archivo CSV como un conjunto RDD. Vamos a recuperar una fila del RDD para comprender el esquema de los datos.


		inspections.take(1)


	Debe ver algo parecido a lo siguiente:

	    # -----------------
		# THIS IS AN OUTPUT
		# -----------------

		[['413707',
	      'LUNA PARK INC',
	      'LUNA PARK  DAY CARE',
	      '2049789',
	      "Children's Services Facility",
	      'Risk 1 (High)',
	      '3250 W FOSTER AVE ',
	      'CHICAGO',
	      'IL',
	      '60625',
	      '09/21/2010',
	      'License-Task Force',
	      'Fail',
	      '24. DISH WASHING FACILITIES: PROPERLY DESIGNED, CONSTRUCTED, MAINTAINED, INSTALLED, LOCATED AND OPERATED - Comments: All dishwashing machines must be of a type that complies with all requirements of the plumbing section of the Municipal Code of Chicago and Rules and Regulation of the Board of Health. OBSEVERD THE 3 COMPARTMENT SINK BACKING UP INTO THE 1ST AND 2ND COMPARTMENT WITH CLEAR WATER AND SLOWLY DRAINING OUT. INST NEED HAVE IT REPAIR. CITATION ISSUED, SERIOUS VIOLATION 7-38-030 H000062369-10 COURT DATE 10-28-10 TIME 1 P.M. ROOM 107 400 W. SURPERIOR. | 36. LIGHTING: REQUIRED MINIMUM FOOT-CANDLES OF LIGHT PROVIDED, FIXTURES SHIELDED - Comments: Shielding to protect against broken glass falling into food shall be provided for all artificial lighting sources in preparation, service, and display facilities. LIGHT SHIELD ARE MISSING UNDER HOOD OF  COOKING EQUIPMENT AND NEED TO REPLACE LIGHT UNDER UNIT. 4 LIGHTS ARE OUT IN THE REAR CHILDREN AREA,IN THE KINDERGARDEN CLASS ROOM. 2 LIGHT ARE OUT EAST REAR, LIGHT FRONT WEST ROOM. NEED TO REPLACE ALL LIGHT THAT ARE NOT WORKING. | 35. WALLS, CEILINGS, ATTACHED EQUIPMENT CONSTRUCTED PER CODE: GOOD REPAIR, SURFACES CLEAN AND DUST-LESS CLEANING METHODS - Comments: The walls and ceilings shall be in good repair and easily cleaned. MISSING CEILING TILES WITH STAINS IN WEST,EAST, IN FRONT AREA WEST, AND BY THE 15MOS AREA. NEED TO BE REPLACED. | 32. FOOD AND NON-FOOD CONTACT SURFACES PROPERLY DESIGNED, CONSTRUCTED AND MAINTAINED - Comments: All food and non-food contact equipment and utensils shall be smooth, easily cleanable, and durable, and shall be in good repair. SPLASH GUARDED ARE NEEDED BY THE EXPOSED HAND SINK IN THE KITCHEN AREA | 34. FLOORS: CONSTRUCTED PER CODE, CLEANED, GOOD REPAIR, COVING INSTALLED, DUST-LESS CLEANING METHODS USED - Comments: The floors shall be constructed per code, be smooth and easily cleaned, and be kept clean and in good repair. INST NEED TO ELEVATE ALL FOOD ITEMS 6INCH OFF THE FLOOR 6 INCH AWAY FORM WALL.  ',
	      '41.97583445690982',
	      '-87.7107455232781',
	      '(41.97583445690982, -87.7107455232781)']]


3. La salida anterior nos da una idea del esquema del archivo de entrada; el archivo incluye el nombre de cada establecimiento, el tipo de establecimiento, la dirección, los datos de las inspecciones y la ubicación, entre otras cosas. Seleccionemos algunas columnas que nos van a ser útiles para nuestro análisis predictivo y agrupemos los resultados como una trama de datos, que luego usaremos para crear una tabla temporal.


		schema = StructType([
        StructField("id", IntegerType(), False), 
        StructField("name", StringType(), False), 
        StructField("results", StringType(), False), 
        StructField("violations", StringType(), True)])

		df = sqlContext.createDataFrame(inspections.map(lambda l: (int(l[0]), l[1], l[12], l[13])) , schema)
		df.registerTempTable('CountResults')

4. Ahora tenemos una *trama de datos*, `df` en la que podemos realizar el análisis. También contamos con una llamada a la tabla temporal **CountResults**. Hemos incluido 4 columnas de interés en la trama de datos: **id**, **name**, **results** y **violations**.
	
	Vamos a obtener una pequeña muestra de los datos:

		df.show(5)

	Debe ver algo parecido a lo siguiente:

	    # -----------------
		# THIS IS AN OUTPUT
		# -----------------

		+------+--------------------+-------+--------------------+
	    |    id|                name|results|          violations|
	    +------+--------------------+-------+--------------------+
	    |413707|       LUNA PARK INC|   Fail|24. DISH WASHING ...|
	    |391234|       CAFE SELMARIE|   Fail|2. FACILITIES TO ...|
	    |413751|          MANCHU WOK|   Pass|33. FOOD AND NON-...|
	    |413708|BENCHMARK HOSPITA...|   Pass|                    |
	    |413722|           JJ BURGER|   Pass|                    |
	    +------+--------------------+-------+--------------------+

## Comprensión de los datos

1. Vamos a empezar a hacernos una idea de lo que contiene el conjunto de datos. Por ejemplo, ¿cuáles son los diferentes valores en la columna **results**?


	df.select('results').distinct().show()

	
	Debe ver algo parecido a lo siguiente:

	    # -----------------
		# THIS IS AN OUTPUT
		# -----------------
	
		+--------------------+
	    |             results|
	    +--------------------+
	    |                Fail|
	    |Business Not Located|
	    |                Pass|
	    |  Pass w/ Conditions|
	    |     Out of Business|
	    +--------------------+
    
2. Una visualización rápida puede ayudarnos a razonar sobre la distribución de estos resultados. Ya tenemos los datos en una tabla temporal **CountResults**. Puede ejecutar la siguiente consulta SQL en la tabla para una mejor descripción de cómo se distribuyen los resultados.

		%%sql -o countResultsdf
		SELECT results, COUNT(results) AS cnt FROM CountResults GROUP BY results

	La instrucción mágica `%%sql` seguida de `-o countResultsdf` garantiza que el resultado de la consulta persiste localmente en el servidor de Jupyter (normalmente, el nodo principal del clúster). El resultado se conserva como una trama de datos [Pandas](http://pandas.pydata.org/) con el nombre especificado **countResultsdf**.
	
	Debe ver algo parecido a lo siguiente:
	
	![Resultado de la consulta SQL](./media/hdinsight-apache-spark-machine-learning-mllib-ipython/query.output.png "Resultado de la consulta SQL")

	Para obtener más información sobre la instrucción mágica `%%sql`, así como otras instrucciones mágicas disponibles con el kernel de PySpark, consulte [Kernels disponibles en los cuadernos de Jupyter con clústeres de HDInsight Spark](hdinsight-apache-spark-jupyter-notebook-kernels.md#why-should-i-use-the-new-kernels).

3. También puede utilizar Matplotlib, una biblioteca que se usa para construir la visualización de datos, para crear un gráfico. Dado que el gráfico se debe crear a partir de la trama de datos **countResultsdf** persistente localmente, el fragmento de código debe comenzar con la instrucción mágica `%%local`. Esto garantiza que el código se ejecuta localmente en el servidor de Jupyter.

		%%local
		%matplotlib inline
		import matplotlib.pyplot as plt
		
		
		labels = countResultsdf['results']
		sizes = countResultsdf['cnt']
		colors = ['turquoise', 'seagreen', 'mediumslateblue', 'palegreen', 'coral']
		plt.pie(sizes, labels=labels, autopct='%1.1f%%', colors=colors)
		plt.axis('equal')

	Debe ver algo parecido a lo siguiente:

	![Salida de resultados](./media/hdinsight-apache-spark-machine-learning-mllib-ipython/output_13_1.png)


4. Vemos que una inspección puede tener cinco resultados diferentes:
	
	* Business not located (no se encontró el negocio) 
	* Fail (no superado)
	* Pass (pasado)
	* Pss w/ conditions (ha pasado con condiciones), y
	* Out of Business (negocio cerrado) 

	Vamos a desarrollar un modelo que pueda predecir el resultado de una inspección de alimentos, teniendo en cuenta las infracciones (violations). Puesto que la regresión logística es un método de clasificación binaria, tiene sentido agrupar los datos en dos categorías: **Fail** y **Pass**. "Pass w/ Conditions" sigue siendo una categoría Pass, por lo que cuando se entrena el modelo, consideraremos los dos resultados como equivalentes. Los datos con otros resultados ("Business Not Located", "Out of Business") no son útiles y por ello los eliminaremos de nuestro conjunto de aprendizaje. Esta eliminación no tiene importancia ya que estas dos categorías constituyen un porcentaje muy pequeño de los resultados.

5. Vamos a seguir y convertir nuestra trama de datos existente (`df`) en una trama de datos nueva, donde cada inspección se representa como un par de etiquetas de infracción. En nuestro caso, una etiqueta de `0.0` representa un resultado de "no superado", una etiqueta de `1.0` representa un resultado de "pasado" y una etiqueta de `-1.0` representa resultados que no sean los dos anteriores. Filtraremos esos otros resultados al calcular la nueva trama de datos.


		def labelForResults(s):
		    if s == 'Fail':
		        return 0.0
		    elif s == 'Pass w/ Conditions' or s == 'Pass':
		        return 1.0
		    else:
		        return -1.0
		label = UserDefinedFunction(labelForResults, DoubleType())
		labeledData = df.select(label(df.results).alias('label'), df.violations).where('label >= 0')


	Vamos a recuperar una fila de los datos con etiqueta para ver su aspecto.


		labeledData.take(1)


	Debe ver algo parecido a lo siguiente:
	
	    # -----------------
		# THIS IS AN OUTPUT
		# -----------------
	
		[Row(label=0.0, violations=u"41. PREMISES MAINTAINED FREE OF LITTER, UNNECESSARY ARTICLES, CLEANING  EQUIPMENT PROPERLY STORED - Comments: All parts of the food establishment and all parts of the property used in connection with the operation of the establishment shall be kept neat and clean and should not produce any offensive odors.  REMOVE MATTRESS FROM SMALL DUMPSTER. | 35. WALLS, CEILINGS, ATTACHED EQUIPMENT CONSTRUCTED PER CODE: GOOD REPAIR, SURFACES CLEAN AND DUST-LESS CLEANING METHODS - Comments: The walls and ceilings shall be in good repair and easily cleaned.  REPAIR MISALIGNED DOORS AND DOOR NEAR ELEVATOR.  DETAIL CLEAN BLACK MOLD LIKE SUBSTANCE FROM WALLS BY BOTH DISH MACHINES.  REPAIR OR REMOVE BASEBOARD UNDER DISH MACHINE (LEFT REAR KITCHEN). SEAL ALL GAPS.  REPLACE MILK CRATES USED IN WALK IN COOLERS AND STORAGE AREAS WITH PROPER SHELVING AT LEAST 6' OFF THE FLOOR.  | 38. VENTILATION: ROOMS AND EQUIPMENT VENTED AS REQUIRED: PLUMBING: INSTALLED AND MAINTAINED - Comments: The flow of air discharged from kitchen fans shall always be through a duct to a point above the roofline.  REPAIR BROKEN VENTILATION IN MEN'S AND WOMEN'S WASHROOMS NEXT TO DINING AREA. | 32. FOOD AND NON-FOOD CONTACT SURFACES PROPERLY DESIGNED, CONSTRUCTED AND MAINTAINED - Comments: All food and non-food contact equipment and utensils shall be smooth, easily cleanable, and durable, and shall be in good repair.  REPAIR DAMAGED PLUG ON LEFT SIDE OF 2 COMPARTMENT SINK.  REPAIR SELF CLOSER ON BOTTOM LEFT DOOR OF 4 DOOR PREP UNIT NEXT TO OFFICE.")]


## Creación de un modelo de regresión logística a partir de la trama de datos de entrada

Nuestra última tarea consiste en convertir los datos etiquetados a un formato que se pueda analizar con regresión logística. La entrada a un algoritmo de regresión logística debe ser un conjunto de *pares de vector de características de etiqueta*, donde el "vector de característica" es un vector de números que representa el punto de entrada de alguna manera. Por lo tanto, necesitamos una forma de convertir la columna "violations", que está semiestructurada y contiene un gran número de comentarios de texto sin formato, en una matriz de números reales que una máquina pueda comprender.

Un enfoque estándar en el aprendizaje automático para procesar el lenguaje natural consiste en asignar a cada palabra diferente un "índice" y, a continuación, pasar un vector al algoritmo de aprendizaje automático, de forma que el valor de cada índice contenga la frecuencia relativa de esa palabra en la cadena de texto.

MLLib proporciona una forma sencilla de realizar esta operación. En primer lugar, vamos a "tokenizar" cada cadena de infracciones para obtener las palabras individuales de cada cadena y, a continuación, usaremos un elemento `HashingTF` para convertir cada conjunto de tokens en un vector de características que puede pasarse al algoritmo de regresión logística para construir un modelo. Realizaremos todos estos pasos en secuencia mediante una "canalización".


	tokenizer = Tokenizer(inputCol="violations", outputCol="words")
	hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
	lr = LogisticRegression(maxIter=10, regParam=0.01)
	pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])
	
	model = pipeline.fit(labeledData)


## Evaluación del modelo en un conjunto de datos de prueba independiente

Podemos usar el modelo que hemos creado anteriormente para *predecir* cuáles serán los resultados de las nuevas inspecciones, basándose en las infracciones que se han observado. Este modelo se ha entrenado en el conjunto de datos **Food\_Inspections1.csv**. Vamos a usar un segundo conjunto de datos, **Food\_Inspections2.csv**, para *evaluar* la solidez de este modelo de datos nuevos. Este segundo conjunto de datos (**Food\_Inspections2.csv**) debe estar ya en el contenedor de almacenamiento predeterminado asociado con el clúster.

1. El fragmento de código siguiente crea una trama de datos nueva, **predictionsDf** que contiene la predicción generada por el modelo. El fragmento de código también crea una tabla temporal **Predictions** basada en la trama de datos.


		testData = sc.textFile('wasb:///HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections2.csv')\
	             .map(csvParse) \
	             .map(lambda l: (int(l[0]), l[1], l[12], l[13]))
		testDf = sqlContext.createDataFrame(testData, schema).where("results = 'Fail' OR results = 'Pass' OR results = 'Pass w/ Conditions'")
		predictionsDf = model.transform(testDf)
		predictionsDf.registerTempTable('Predictions')
		predictionsDf.columns


	Debe ver algo parecido a lo siguiente:
	
	    # -----------------
		# THIS IS AN OUTPUT
		# -----------------
		
		['id',
	     'name',
	     'results',
	     'violations',
	     'words',
	     'features',
	     'rawPrediction',
	     'probability',
	     'prediction']

2. Examine una de las predicciones. Ejecute este fragmento de código:

		predictionsDf.take(1)

	Verá la predicción de la primera entrada en el conjunto de datos de prueba.

3. El método `model.transform()` aplicará la misma transformación a cualquier dato nuevo con el mismo esquema y llegará a una predicción sobre cómo clasificar los datos. Podemos hacer algunas estadísticas muy sencillas para hacernos una idea del grado de precisión alcanzado por nuestras predicciones:


		numSuccesses = predictionsDf.where("""(prediction = 0 AND results = 'Fail') OR 
		                                      (prediction = 1 AND (results = 'Pass' OR 
		                                                           results = 'Pass w/ Conditions'))""").count()
		numInspections = predictionsDf.count()
		
		print "There were", numInspections, "inspections and there were", numSuccesses, "successful predictions"
		print "This is a", str((float(numSuccesses) / float(numInspections)) * 100) + "%", "success rate"

	El resultado tendrá un aspecto similar al siguiente:
	
	    # -----------------
		# THIS IS AN OUTPUT
		# -----------------
	
		There were 9315 inspections and there were 8087 successful predictions
	    This is a 86.8169618894% success rate


	El uso de la regresión logística con Spark nos ofrece un modelo preciso de la relación entre las descripciones de las infracciones en inglés y el hecho de si un negocio determinado superará o no una inspección de alimentos.

## Creación de una representación visual de la predicción

Podemos crear una visualización final que nos ayude a analizar los resultados de esta prueba:

1. Empezaremos por extraer los distintos resultados y predicciones de la tabla temporal **Predictions** creada anteriormente.

		%%sql -o predictionstable
		SELECT prediction, results FROM Predictions

2. En el fragmento de código anterior, **predictionstable** es la trama de datos local en el servidor de Jupyter que conserva el resultado de la consulta SQL. Ahora puede usar la instrucción mágica `%%local` para ejecutar los fragmentos de código siguientes en la trama de datos persistente localmente.

		%%local
		failSuccess = predictionstable[(predictionstable.prediction == 0) & (predictionstable.results == 'Fail')]['prediction'].count()
		failFailure = predictionstable[(predictionstable.prediction == 0) & (predictionstable.results <> 'Fail')]['prediction'].count()
		passSuccess = predictionstable[(predictionstable.prediction == 1) & (predictionstable.results <> 'Fail')]['prediction'].count()
		passFailure = predictionstable[(predictionstable.prediction == 1) & (predictionstable.results == 'Fail')]['prediction'].count()
		failSuccess,failFailure,passSuccess,passFailure

	El resultado tendrá un aspecto similar al siguiente:
	
		# -----------------
		# THIS IS AN OUTPUT
		# -----------------
	
		(276, 46, 1917, 261)

3. Por último, utilice el siguiente fragmento de código para generar el gráfico.

		%%local
		%matplotlib inline
		import matplotlib.pyplot as plt
		
		labels = ['True positive', 'False positive', 'True negative', 'False negative']
		sizes = [failSuccess, failFailure, passSuccess, passFailure]
		plt.pie(sizes, labels=labels, autopct='%1.1f%%')
		plt.axis('equal')
	
	Debería ver la siguiente salida.
	
	![Salida de predicciones](./media/hdinsight-apache-spark-machine-learning-mllib-ipython/output_26_1.png)


	En este gráfico, un resultado "positivo" hace referencia a la inspección de alimentos no superada, mientras que un resultado negativo hace referencia a una inspección pasada.

## Cierre del cuaderno

Cuando haya terminado de ejecutar la aplicación, debe cerrar el cuaderno para liberar los recursos. Para ello, en el menú **Archivo** del cuaderno, haga clic en **Cerrar y detener**. De esta manera se apagará y se cerrará el cuaderno.


## <a name="seealso"></a>Otras referencias


* [Introducción a Apache Spark en HDInsight de Azure](hdinsight-apache-spark-overview.md)

### Escenarios

* [Spark with BI: Realizar el análisis de datos interactivos con Spark en HDInsight con las herramientas de BI](hdinsight-apache-spark-use-bi-tools.md)

* [Creación de aplicaciones de Aprendizaje automático con Apache Spark en HDInsight de Azure](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Spark Streaming: Use Spark in HDInsight for building real-time streaming applications](hdinsight-apache-spark-eventhub-streaming.md)

* [Análisis del registro del sitio web con Spark en HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### Creación y ejecución de aplicaciones

* [Crear una aplicación independiente con Scala](hdinsight-apache-spark-create-standalone-application.md)

* [Submit Spark jobs remotely using Livy with Spark clusters on HDInsight (Linux)](hdinsight-apache-spark-livy-rest-interface.md)

### Herramientas y extensiones

* [Use HDInsight Tools Plugin for IntelliJ IDEA to create and submit Spark Scala applications (Uso del complemento de herramientas de HDInsight para IntelliJ IDEA para crear y enviar aplicaciones Scala Spark)](hdinsight-apache-spark-intellij-tool-plugin.md)

* [Uso de cuadernos de Zeppelin con un clúster Spark en HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [Kernels available for Jupyter notebook in Spark cluster for HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)

### Administración de recursos

* [Administración de recursos para el clúster Apache Spark en HDInsight de Azure](hdinsight-apache-spark-resource-manager.md)

<!---HONumber=AcomDC_0224_2016-->