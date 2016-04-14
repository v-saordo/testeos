<properties 
	pageTitle="Creación de un clúster Spark en HDInsight y uso de Spark SQL desde Zeppelin y Jupyter para análisis interactivos | Azure" 
	description="Instrucciones paso a paso sobre cómo crear rápidamente un clúster Apache Spark en HDInsight y luego usar Spark SQL en cuadernos de Zeppelin y Jupyter para ejecutar consultas interactivas." 
	services="hdinsight" 
	documentationCenter="" 
	authors="nitinme" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/22/2015" 
	ms.author="nitinme"/>


# Inicio rápido: creación de Apache Spark en HDInsight y ejecución de consultas interactivas mediante Spark SQL (Windows)

[AZURE.INCLUDE [hdinsight-azure-portal](../../includes/hdinsight-azure-portal.md)]

* [Creación de Apache Spark en HDInsight y ejecución de consultas interactivas mediante Spark SQL](hdinsight-apache-spark-jupyter-spark-sql.md)

Obtenga información sobre cómo crear un clúster Apache Spark en HDInsight con la opción Creación rápida y después usar cuadernos de [Zeppelin](https://zeppelin.incubator.apache.org) y [Jupyter](https://jupyter.org) basados en web para ejecutar consultas interactivas de Spark SQL en el clúster Spark.


   ![Introducción al uso de Apache Spark en HDInsight](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/hdispark.getstartedflow.png "Tutorial de introducción al uso de Apache Spark en HDInsight. Pasos que se muestran: crear una cuenta de almacenamiento; crear un clúster; ejecutar instrucciones Spark SQL")

**Requisitos previos:**

Antes de comenzar este tutorial, debe tener una suscripción a Azure. Consulte [How to get Azure Free trial for testing Hadoop in HDInsight (Obtención de una versión de prueba gratuita de Azure para probar Hadoop en HDInsight)](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

##<a name="storage"></a>Creación de una cuenta de almacenamiento de Azure

Cuando se crea un clúster de HDInsight en HDInsight, se especifica una cuenta de Almacenamiento de Azure. Se designa un contenedor de almacenamiento de blobs concreto de esa cuenta como sistema de archivos predeterminado. De forma predeterminada, el clúster de HDInsight se crea en el mismo centro de datos que la cuenta de almacenamiento que especifica. Para obtener más información, consulte [Uso de almacenamiento de blobs de Azure con HDInsight][hdinsight-storage].


**Para crear una cuenta de almacenamiento de Azure**

1. Inicie sesión en el [Portal de Azure][azure-management-portal].
2. Haga clic en **NUEVO** en la esquina inferior izquierda y luego escriba los valores como se muestra en la imagen.

	![El Portal de Azure, donde puede usar Creación rápida para configurar una nueva cuenta de almacenamiento](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/hdispark.storageaccount.quickcreate.png "El Portal de Azure, donde puede usar Creación rápida para configurar una nueva cuenta de almacenamiento")

>[AZURE.NOTE]  Asegúrese de crear la cuenta de almacenamiento en una ubicación admitida para el clúster.

Elija la nueva cuenta de almacenamiento en la lista y haga clic en **Administrar claves de acceso** en la parte inferior de la página. Anote la **CLAVE DE ACCESO PRIMARIA** (o la **CLAVE DE ACCESO SECUNDARIA**, cualquiera de las claves funciona). Las necesitará más adelante en el tutorial. Para obtener más información, vea [Cómo crear una cuenta de almacenamiento][azure-create-storageaccount].
	
##Creación de un clúster de Spark en HDInsight

En esta sección, se crea un clúster de HDInsight versión 3.2, que se basa en la versión 1.3.1 de Spark. Para obtener información acerca de las diferentes versiones de HDInsight y sus contratos de nivel de servicio, consulte la página [Control de versiones de componentes de HDInsight](hdinsight-component-versioning.md).

>[AZURE.NOTE] En este artículo, se proporcionan pasos para crear un clúster Apache Spark en HDInsight con los valores de configuración básica. Si quiere información sobre otras opciones de configuración del clúster (como usar almacenamiento adicional, una red virtual de Azure o una tienda de metadatos para Hive), consulte [Aprovisionamiento de clústeres Apache Spark en HDInsight mediante opciones personalizadas](hdinsight-apache-spark-provision-clusters.md).


**Para crear un clúster Spark**

1. Inicie sesión en el [Portal de Azure][azure-management-portal]. 

2. Haga clic en **NUEVO** en la esquina inferior izquierda y luego escriba los valores como se muestra en la imagen.

	![Crear un clúster Spark en HDInsight](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/hdispark.quickcreatecluster.png "Crear un clúster Spark en HDInsight")


##<a name="zeppelin"></a>Ejecución de consultas Spark SQL interactivas con un cuaderno de Zeppelin

Después de haber creado un clúster, puede usar un cuaderno de Zeppelin basado en web para ejecutar consultas interactivas de Spark SQL en el clúster Spark de HDInsight. En esta sección, se usará un archivo de datos de ejemplo (hvac.csv) disponible de manera predeterminada en el clúster para ejecutar algunas consultas Spark SQL interactivas.

>[AZURE.NOTE] El cuaderno que crea cuando sigue las instrucciones más abajo también está disponible de manera predeterminada en el clúster. Después de iniciar Zeppelin, encontrará el cuaderno con el nombre **Zeppelin HVAC tutorial**.

1. En el panel izquierdo del [Portal de Azure][azure-management-portal], haga clic en **HDInsight** y luego haga clic en el clúster Spark que ha creado. En la página del clúster Spark, en el panel inferior, haga clic en **Zeppelin Notebook**. Cuando se le pida, escriba las credenciales del clúster.

	> [AZURE.NOTE] También puede comunicarse con su equipo portátil ligero Zeppelin en el clúster si abre la siguiente dirección URL en el explorador. Reemplace __CLUSTERNAME__ por el nombre del clúster.
	>
	> `https://CLUSTERNAME.azurehdinsight.net/zeppelin`

2. Cree un nuevo notebook. En el panel de encabezado, haga clic en **Notebook** y luego en **Create New Note** (Crear nota).

	![Crear un nuevo cuaderno de Zeppelin](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/hdispark.createnewnote.png "Crear un nuevo cuaderno de Zeppelin")

	En la misma página, en el encabezado **Notebook**, debería ver un nuevo cuaderno con un nombre que empiece por **Note XXXXXXXXX** (Nota XXXXXXXXX). Haga clic en el nuevo cuaderno.

3. En la página web del nuevo cuaderno, haga clic en el encabezado y cambie el nombre del cuaderno si quiere. Presione ENTRAR para guardar el cambio de nombre. Además, asegúrese de que el encabezado de cuaderno muestre el estado **Connected** (Conectado) en la esquina superior derecha.

	![Estado del cuaderno de Zeppelin](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/hdispark.newnote.connected.png "Estado del cuaderno de Zeppelin")

4. Cargue los datos de ejemplo en una tabla temporal. Cuando crea un clúster Spark en HDInsight, el archivo de datos de ejemplo, **hvac.csv**, se copia en la cuenta de almacenamiento asociada en **\\HdiSamples\\SensorSampleData\\hvac**.

	En el párrafo vacío que se crea de manera predeterminada en el nuevo cuaderno, pegue el siguiente fragmento.

		// Create an RDD using the default Spark context, sc
		val hvacText = sc.textFile("wasb:///HdiSamples/SensorSampleData/hvac/HVAC.csv")
		
		// Define a schema
		case class Hvac(date: String, time: String, targettemp: Integer, actualtemp: Integer, buildingID: String)
		
		// Map the values in the .csv file to the schema
		val hvac = hvacText.map(s => s.split(",")).filter(s => s(0) != "Date").map(
    		s => Hvac(s(0), 
            		s(1),
            		s(2).toInt,
            		s(3).toInt,
            		s(6)
        	)
		).toDF()
		
		// Register as a temporary table called "hvac"
		hvac.registerTempTable("hvac")
		
	Presione **MAYÚS + ENTRAR** o haga clic en el botón **Reproducir** del párrafo para ejecutar el fragmento de código. El estado en la esquina derecha del párrafo debería avanzar de READY (Listo), PENDING (Pendiente) o RUNNING (En ejecución) a FINISHED (Finalizado). El resultado se muestra en la parte inferior del mismo párrafo. La captura de pantalla es similar a la siguiente:

	![Crear una tabla temporal de datos sin procesar](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/hdispark.note.loaddataintotable.png "Crear una tabla temporal de datos sin procesar")

	También puede proporcionar un título para cada párrafo. En la esquina derecha, haga clic en el icono **Settings** (Configuración) y luego haga clic en **Show title** (Mostrar título).

5. Ahora puede ejecutar instrucciones Spark SQL en la tabla **hvac**. Pegue la siguiente consulta en un nuevo párrafo. La consulta recupera el identificador del edificio y la diferencia entre la temperatura objetivo y la real para cada edificio en una fecha determinada. Presione **MAYÚS + ENTRAR**.

		%sql
		select buildingID, (targettemp - actualtemp) as temp_diff, date 
		from hvac
		where date = "6/1/13" 

	La instrucción **%sql** al principio indica al cuaderno que use el intérprete Spark SQL. Puede consultar los intérpretes definidos en la pestaña **Interpreter** (Intérprete) en el encabezado del cuaderno.

	En la captura de pantalla siguiente se muestra el resultado.

	![Ejecutar una instrucción Spark SQL mediante el cuaderno](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/hdispark.note.sparksqlquery1.png "Ejecutar una instrucción Spark SQL mediante el cuaderno")

	 Haga clic en las opciones de presentación (resaltadas con el rectángulo) para alternar entre diferentes representaciones del mismo resultado. Haga clic en **Settings** (Configuración) para elegir qué constituye la clave y los valores de la salida. En la captura de pantalla anterior se usa **buildingID** como clave y la media de **temp\_diff** como valor.

	
6. También puede ejecutar instrucciones Spark SQL usando variables en la consulta. El siguiente fragmento de código muestra cómo definir una variable, **Temp**, en la consulta con los valores posibles con los que quiere hacer la consulta. Cuando ejecuta la consulta por primera vez, se rellena una lista desplegable automáticamente con los valores especificados para la variable.

		%sql
		select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff
		from hvac
		where targettemp > "${Temp = 65,65|75|85}" 

	Pegue este fragmento de código en un nuevo párrafo y presione **MAYÚS + ENTRAR**. En la captura de pantalla siguiente se muestra el resultado.

	![Ejecutar una instrucción Spark SQL mediante el cuaderno](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/hdispark.note.sparksqlquery2.png "Ejecutar una instrucción Spark SQL mediante el cuaderno")

	Para las consultas posteriores, puede seleccionar un nuevo valor en la lista desplegable y después volver a ejecutar la consulta. Haga clic en **Settings** (Configuración) para elegir qué constituye la clave y los valores de la salida. La captura de pantalla anterior usa **buildingID** como clave, la media de **temp\_diff** como valor y **targettemp** como grupo.

7. Reinicie el intérprete Spark SQL para salir de la aplicación. Haga clic en la pestaña **Interpreter** (Intérprete) en la parte superior y, para el intérprete Spark, haga clic en **Restart** (Reiniciar).

	![Reiniciar el intérprete Zeppelin](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/hdispark.zeppelin.restart.interpreter.png "Reiniciar el intérprete Zeppelin")

##<a name="jupyter"></a>Ejecución de consultas Spark SQL mediante un cuaderno de Jupyter

En esta sección, utilice un cuaderno de Jupyter para ejecutar consultas Spark SQL en un clúster Spark.

>[AZURE.NOTE] El cuaderno que crea cuando sigue las instrucciones más abajo también está disponible de manera predeterminada en el clúster. Después de iniciar Jupyter, encontrará este cuaderno por el nombre **HVACTutorial.ipynb**.

1. En el panel izquierdo del [Portal de Azure][azure-management-portal], haga clic en **HDInsight** y luego haga clic en el clúster Spark que ha creado. En la página del clúster Spark, en el panel inferior, haga clic en **Zeppelin Notebook**. Cuando se le pida, escriba las credenciales del clúster.

	> [AZURE.NOTE] También puede comunicarse con el equipo Jupyter Notebook en el clúster si abre la siguiente dirección URL en el explorador. Reemplace __CLUSTERNAME__ por el nombre del clúster:
	>
	> `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Cree un nuevo notebook. Haga clic en **New** (Nuevo) y, luego, en **Python2**.

	![Crear un nuevo cuaderno de Jupyter](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/hdispark.note.jupyter.createnotebook.png "Crear un nuevo cuaderno de Jupyter")

3. Se crea y se abre un nuevo cuaderno con el nombre Untitled.pynb. Haga clic en el nombre del cuaderno en la parte superior y escriba un nombre descriptivo.

	![Proporcionar un nombre para el cuaderno](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/hdispark.note.jupyter.notebook.name.png "Proporcionar un nombre para el cuaderno")

4. Importe los módulos requeridos y cree los contextos Spark y SQL. Pegue el siguiente fragmento de código en una celda vacía y presione **MAYÚS + ENTRAR**.

		from pyspark import SparkContext
		from pyspark.sql import SQLContext
		from pyspark.sql.types import *

		# Create Spark and SQL contexts
		sc = SparkContext('spark://headnodehost:7077', 'pyspark')
		sqlContext = SQLContext(sc)

	Cada vez que se ejecuta un trabajo en Jupyter, el título de la ventana del explorador web mostrará el estado **(Busy)** (Ocupado) junto con el título del cuaderno. También verá un círculo sólido junto al texto **Python 2** en la esquina superior derecha. Una vez completado el trabajo, cambiará a un círculo hueco.

	 ![Estado de un trabajo de cuaderno de Jupyter](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/hdispark.jupyter.job.status.png "Estado de un trabajo de cuaderno de Jupyter")

4. Cargue los datos de ejemplo en una tabla temporal. Cuando crea un clúster Spark en HDInsight, el archivo de datos de ejemplo, **hvac.csv**, se copia en la cuenta de almacenamiento asociada en **\\HdiSamples\\SensorSampleData\\hvac**.

	En una celda vacía, pegue el siguiente fragmento de código y presione **MAYÚS + ENTRAR**. Este fragmento registra los datos en una tabla temporal llamada **hvac**.


		# Load the data
		hvacText = sc.textFile("wasb:///HdiSamples/SensorSampleData/hvac/HVAC.csv")
		
		# Create the schema
		hvacSchema = StructType([StructField("date", StringType(), False),StructField("time", StringType(), False),StructField("targettemp", IntegerType(), False),StructField("actualtemp", IntegerType(), False),StructField("buildingID", StringType(), False)])
		
		# Parse the data in hvacText
		hvac = hvacText.map(lambda s: s.split(",")).filter(lambda s: s[0] != "Date").map(lambda s:(str(s[0]), str(s[1]), int(s[2]), int(s[3]), str(s[6]) ))
		
		# Create a data frame
		hvacdf = sqlContext.createDataFrame(hvac,hvacSchema)
		
		# Register the data fram as a table to run queries against
		hvacdf.registerAsTable("hvac")
		
		# Run queries against the table and display the data
		data = sqlContext.sql("select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = "6/1/13"")
		data.show()

5. Una vez que el trabajo se completa correctamente, se muestra el resultado siguiente.

		buildingID temp_diff date  
		4          8         6/1/13
		3          2         6/1/13
		7          -10       6/1/13
		12         3         6/1/13
		7          9         6/1/13
		7          5         6/1/13
		3          11        6/1/13
		8          -7        6/1/13
		17         14        6/1/13
		16         -3        6/1/13
		8          -8        6/1/13
		1          -1        6/1/13
		12         11        6/1/13
		3          14        6/1/13
		6          -4        6/1/13
		1          4         6/1/13
		19         4         6/1/13
		19         12        6/1/13
		9          -9        6/1/13
		15         -10       6/1/13

6. Reinicie el kernel para salir de la aplicación. En la barra de menús superior, haga clic en **Kernel**, en **Restart** (Reiniciar) y luego en **Restart** de nuevo cuando se le pida.

	![Reiniciar el kernel de Jupyter](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/hdispark.jupyter.restart.kernel.png "Reiniciar el kernel de Jupyter")


##<a name="seealso"></a>Otras referencias


* [Introducción a Apache Spark en HDInsight de Azure](hdinsight-apache-spark-overview-v1.md)
* [Creación de un clúster Spark de HDInsight](hdinsight-apache-spark-provision-clusters.md)
* [Uso de herramientas de BI con Apache Spark en HDInsight de Azure](hdinsight-apache-spark-use-bi-tools-v1.md)
* [Uso de Spark en HDInsight para crear aplicaciones de aprendizaje automático](hdinsight-apache-spark-ipython-notebook-machine-learning-v1.md)
* [Streaming con Spark: procesamiento de eventos desde el Centro de eventos de Azure con Apache Spark en HDInsight](hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming.md)
* [Administración de recursos para el clúster Apache Spark en HDInsight de Azure](hdinsight-apache-spark-resource-manager-v1.md)


[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md

<!---HONumber=AcomDC_0218_2016-->