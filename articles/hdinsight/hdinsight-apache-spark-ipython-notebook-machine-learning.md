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


# Creación de aplicaciones de Aprendizaje automático con Apache Spark en HDInsight de Azure (Linux)

Obtenga información acerca de cómo crear una aplicación de aprendizaje automático con un clúster Apache Spark en HDInsight. En este artículo se muestra cómo usar el cuaderno de Jupyter disponible con el clúster para compilar y probar la aplicación. La aplicación usa los datos de ejemplo de HVAC.csv, que está disponible en todos los clústeres de manera predeterminada.

**Requisitos previos:**

Debe tener lo siguiente:

- Una suscripción de Azure. Consulte [How to get Azure Free trial for testing Hadoop in HDInsight (Obtención de una versión de prueba gratuita de Azure para probar Hadoop en HDInsight)](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Un clúster Apache Spark en HDInsight Linux. Para instrucciones, vea [Creación de clústeres Apache Spark en HDInsight de Azure](hdinsight-apache-spark-jupyter-spark-sql.md). 

##<a name="data"></a>Visualización de los datos

Antes de empezar a crear la aplicación, intentemos comprender la estructura de los datos y el tipo de análisis que se realizará en ellos.

En este artículo, usamos el archivo de datos de ejemplo **HVAC.csv** que está disponible en todos los clústeres de HDInsight de manera predeterminada en **\\HdiSamples\\HdiSamples\\SensorSampleData\\hvac**. Descargue y abra el archivo CSV para hacerse una idea de cuáles son los datos.

![Instantánea de datos de HVAC](./media/hdinsight-apache-spark-ipython-notebook-machine-learning/hdispark.ml.show.data.png "Instantánea de los datos de HVAC")

Los datos muestran la temperatura objetivo y la temperatura real de un edificio con sistemas HVAC instalados. Supongamos que la columna **System** representa el id. del sistema y la columna **SystemAge**, el número de años que lleva el sistema HVAC instalado en el edificio.

Estos datos se usarán para predecir si un edificio será más cálido o frío en función de la temperatura objetivo, dados un identificador del sistema y la antigüedad del sistema.

##<a name="app"></a>Escritura de una aplicación de aprendizaje automático mediante Spark MLlib

En esta aplicación se usa una canalización Spark ML para realizar una clasificación de documentos. En la canalización, se divide el documento en palabras, se convierten las palabras en un vector numérico de característica y finalmente se genera un modelo de predicción que use los vectores de característica y las etiquetas. Realice los siguientes pasos para crear la aplicación:

1. Desde el [Portal de vista previa de Azure](https://portal.azure.com/), en el panel de inicio, haga clic en el icono del clúster Spark (si lo ha fijado en el panel de inicio). También puede navegar hasta el clúster en **Examinar todo** > **Clústeres de HDInsight**.   

2. En la hoja del clúster Spark, haga clic en **Vínculos rápidos** y, luego, en la hoja **Panel de clúster**, haga clic en **Jupyter Notebook**. Cuando se le pida, escriba las credenciales del clúster.

	> [AZURE.NOTE] También puede comunicarse con el equipo Jupyter Notebook en el clúster si abre la siguiente dirección URL en el explorador. Reemplace __CLUSTERNAME__ por el nombre del clúster:
	>
	> `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Cree un nuevo notebook. Haga clic en **Nuevo** y, luego, en **PySpark**.

	![Crear un nuevo cuaderno de Jupyter](./media/hdinsight-apache-spark-ipython-notebook-machine-learning/hdispark.note.jupyter.createnotebook.png "Crear un nuevo cuaderno de Jupyter")

3. Se crea y se abre un nuevo cuaderno con el nombre Untitled.pynb. Haga clic en el nombre del cuaderno en la parte superior y escriba un nombre descriptivo.

	![Proporcionar un nombre para el cuaderno](./media/hdinsight-apache-spark-ipython-notebook-machine-learning/hdispark.note.jupyter.notebook.name.png "Proporcionar un nombre para el cuaderno")

3. Dado que creó un cuaderno con el kernel PySpark, no necesitará crear ningún contexto explícitamente. Los contextos Spark, SQL y Hive se crearán automáticamente al ejecutar la primera celda de código. Puede empezar por importar los tipos que son necesarios para este escenario. Pegue el siguiente fragmento de código en una celda vacía y presione **MAYÚS + ENTRAR**.

		from pyspark.ml import Pipeline
		from pyspark.ml.classification import LogisticRegression
		from pyspark.ml.feature import HashingTF, Tokenizer
		from pyspark.sql import Row, SQLContext
		
		import os
		import sys
		from pyspark.sql.types import *
		
		from pyspark.mllib.classification import LogisticRegressionWithSGD
		from pyspark.mllib.regression import LabeledPoint
		from numpy import array
		
		
	 
4. Ahora, debe cargar los datos (hvac.csv), analizarlos y usarlos para entrenar el modelo. Para ello, se define una función que comprueba si la temperatura real del edificio es mayor que la temperatura objetivo. Si la temperatura real es mayor, el edificio está cálido, lo que viene indicado por el valor **1.0**. Si la temperatura real es menor, el edificio está frío, lo que se indica con el valor **0.0**.

	Pegue el siguiente fragmento de código en una celda vacía y presione **MAYÚS + ENTRAR**.

		
		# List the structure of data for better understanding. Becuase the data will be
		# loaded as an array, this structure makes it easy to understand what each element
		# in the array corresponds to

		# 0 Date
		# 1 Time
		# 2 TargetTemp
		# 3 ActualTemp
		# 4 System
		# 5 SystemAge
		# 6 BuildingID

		LabeledDocument = Row("BuildingID", "SystemInfo", "label")

		# Define a function that parses the raw CSV file and returns an object of type LabeledDocument
		
		def parseDocument(line):
    		values = [str(x) for x in line.split(',')]
    		if (values[3] > values[2]):
        		hot = 1.0
    		else:
        		hot = 0.0        
    
    		textValue = str(values[4]) + " " + str(values[5])
    
    		return LabeledDocument((values[6]), textValue, hot)

		# Load the raw HVAC.csv file, parse it using the function
		data = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

		documents = data.filter(lambda s: "Date" not in s).map(parseDocument)
		training = documents.toDF()


5. Configure la canalización de aprendizaje automático de Spark, que consta de tres fases: tokenizer, hashingTF e lr. Para más información sobre qué es una canalización y cómo funciona, vea <a href="http://spark.apache.org/docs/latest/ml-guide.html#how-it-works" target="_blank">Canalización de aprendizaje automático de Spark</a>.

	Pegue el siguiente fragmento de código en una celda vacía y presione **MAYÚS + ENTRAR**.

		tokenizer = Tokenizer(inputCol="SystemInfo", outputCol="words")
		hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
		lr = LogisticRegression(maxIter=10, regParam=0.01)
		pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])

6. Ajuste la canalización al documento de formación. Pegue el siguiente fragmento en una celda vacía y presione **MAYÚS + ENTRAR**.

		model = pipeline.fit(training)

7. Compruebe el documento de aprendizaje para controlar el progreso con la aplicación. Pegue el siguiente fragmento en una celda vacía y presione **MAYÚS + ENTRAR**.

		training.show()

	Esto dará un resultado similar al siguiente:

		+----------+----------+-----+
		|BuildingID|SystemInfo|label|
		+----------+----------+-----+
		|         4|     13 20|  0.0|
		|        17|      3 20|  0.0|
		|        18|     17 20|  1.0|
		|        15|      2 23|  0.0|
		|         3|      16 9|  1.0|
		|         4|     13 28|  0.0|
		|         2|     12 24|  0.0|
		|        16|     20 26|  1.0|
		|         9|      16 9|  1.0|
		|        12|       6 5|  0.0|
		|        15|     10 17|  1.0|
		|         7|      2 11|  0.0|
		|        15|      14 2|  1.0|
		|         6|       3 2|  0.0|
		|        20|     19 22|  0.0|
		|         8|     19 11|  0.0|
		|         6|      15 7|  0.0|
		|        13|      12 5|  0.0|
		|         4|      8 22|  0.0|
		|         7|      17 5|  0.0|
		+----------+----------+-----+


	Vuelva atrás y compruebe el resultado en el archivo CSV sin procesar. Por ejemplo, la primera fila del archivo CSV tiene estos datos:

	![Instantánea de datos de HVAC](./media/hdinsight-apache-spark-ipython-notebook-machine-learning/hdispark.ml.show.data.first.row.png "Instantánea de los datos de HVAC")

	Observe que la temperatura real es menor que la temperatura objetivo, lo que indica que el edificio está frío. Por lo tanto, en los resultados de formación, el valor de **label** en la primera fila es **0.0**, lo que significa que el edificio no está cálido.

8.  Prepare un conjunto de datos con el que ejecutar el modelo entrenado. Para ello, deberá pasar un id. del sistema y la antigüedad del sistema (representados en **SystemInfo** en los resultados de formación), y el modelo predirá si el edificio con ese id. y esa antigüedad del sistema es más cálido (indicado por 1.0) o frío (indicado por 0.0).

	Pegue el siguiente fragmento en una celda vacía y presione **MAYÚS + ENTRAR**.
		
		# SystemInfo here is a combination of system ID followed by system age
		Document = Row("id", "SystemInfo")
		test = sc.parallelize([(1L, "20 25"),
                      (2L, "4 15"),
                      (3L, "16 9"),
                      (4L, "9 22"),
                      (5L, "17 10"),
                      (6L, "7 22")]) \
    		.map(lambda x: Document(*x)).toDF() 

9. Por último, realice predicciones basadas en los datos de prueba. Pegue el siguiente fragmento de código en una celda vacía y presione **MAYÚS + ENTRAR**.

		# Make predictions on test documents and print columns of interest
		prediction = model.transform(test)
		selected = prediction.select("SystemInfo", "prediction", "probability")
		for row in selected.collect():
		    print row

10. Debería ver una salida similar a la siguiente:

		Row(SystemInfo=u'20 25', prediction=1.0, probability=DenseVector([0.4999, 0.5001]))
		Row(SystemInfo=u'4 15', prediction=0.0, probability=DenseVector([0.5016, 0.4984]))
		Row(SystemInfo=u'16 9', prediction=1.0, probability=DenseVector([0.4785, 0.5215]))
		Row(SystemInfo=u'9 22', prediction=1.0, probability=DenseVector([0.4549, 0.5451]))
		Row(SystemInfo=u'17 10', prediction=1.0, probability=DenseVector([0.4925, 0.5075]))
		Row(SystemInfo=u'7 22', prediction=0.0, probability=DenseVector([0.5015, 0.4985]))

	En la primera fila de la predicción, ve que, para un sistema HVAC con el id. 20 y una antigüedad de 25 años, el edificio estará cálido (**prediction=1.0**). El primer valor de DenseVector (0.49999) corresponde a la predicción 0.0 y el segundo, (0.5001), corresponde a la predicción 1.0. En la salida, aunque el segundo valor solo es levemente superior, el modelo muestra **prediction=1.0**.

11. Cuando haya terminado de ejecutar la aplicación, debe apagar el Notebook para liberar los recursos. Para ello, en el menú **Archivo** del cuaderno, haga clic en **Cerrar y detener**. De esta manera se apagará y se cerrará el Notebook.
	  	   

##<a name="anaconda"></a>Uso de la biblioteca scikit-learn de Anaconda para Aprendizaje automático

Los clústeres Apache Spark en HDInsight incluyen bibliotecas de Anaconda, entre ellas la biblioteca **scikit-learn** para el aprendizaje automático. La biblioteca también contiene diversos conjuntos de datos que puede usar para crear aplicaciones de ejemplo directamente a partir de un cuaderno de Jupyter. Para obtener ejemplos sobre cómo usar la biblioteca scikit-learn, consulte [http://scikit-learn.org/stable/auto\_examples/index.html](http://scikit-learn.org/stable/auto_examples/index.html).

##<a name="seealso"></a>Otras referencias

* [Introducción a Apache Spark en HDInsight de Azure](hdinsight-apache-spark-overview.md)

### Escenarios

* [Spark with BI: Realizar el análisis de datos interactivos con Spark en HDInsight con las herramientas de BI](hdinsight-apache-spark-use-bi-tools.md)

* [Spark con aprendizaje automático: uso de Spark en HDInsight para predecir los resultados de la inspección de alimentos](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Streaming con Spark: uso de Spark en HDInsight para compilar aplicaciones de streaming en tiempo real](hdinsight-apache-spark-eventhub-streaming.md)

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


[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-weblogs-sample]: hdinsight-hive-analyze-website-log.md
[hdinsight-sensor-data-sample]: hdinsight-hive-analyze-sensor-data.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md

<!---HONumber=AcomDC_0224_2016-->