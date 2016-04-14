<properties 
	pageTitle="Uso de acción de script para instalar Apache Spark en HDInsight basado en Linux | Microsoft Azure" 
	description="Aprenda a instalar Spark en clústeres de HDInsight basados en Linux mediante acciones de script. Las acciones de script le permiten personalizar el clúster durante la creación; así, puede cambiar la configuración del clúster o instalar utilidades y servicios." 
	services="hdinsight" 
	documentationCenter="" 
	authors="Blackmist" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/05/2016" 
	ms.author="larryfr"/>

# Instalación y uso de Spark en clústeres Hadoop de HDInsight

En este documento, aprenderá a instalar Spark mediante una acción de script. La acción de script le permite ejecutar scripts para personalizar un clúster, conforme se crea el clúster. Para obtener más información, consulte [Personalización de un clúster de HDInsight mediante Generar acción de script][hdinsight-cluster-customize]. Una vez haya instalado Spark, aprenderá cómo ejecutar una consulta de Spark en clústeres de HDInsight.

> [AZURE.NOTE] HDInsight también proporciona Spark como tipo de clúster, lo que significa que ahora puede aprovisionar directamente un clúster de Spark sin modificar un clúster de Hadoop. Sin embargo, esto se limita actualmente a los clústeres basados en Windows. Con el tipo de clúster Spark, se obtiene un clúster de HDInsight versión 3.2 basado en Windows con Spark versión 1.3.1. Para obtener más información, vea [Introducción a Apache Spark en HDInsight](hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql.md).

## <a name="whatis"></a>¿Qué es Spark?

<a href="http://spark.apache.org/docs/latest/index.html" target="_blank">Apache Spark</a> es un marco de procesamiento paralelo de código abierto que admite el procesamiento en memoria para mejorar el rendimiento de las aplicaciones analíticas de Big Data. Las capacidades de cálculo en memoria de Spark lo convierten en una buena opción para algoritmos iterativos en los cálculos de gráficos y aprendizaje automático.

Spark también se puede usar para llevar a cabo el procesamiento de datos convencional basado en disco. Spark mejora el marco de MapReduce tradicional evitando escrituras en disco en las etapas intermedias. Además, Spark es compatible con el Sistema de archivos distribuido de Hadoop (HDFS) y el almacenamiento de blobs de Azure, por lo que se pueden procesar los datos existentes fácilmente a través de Spark.

Este tema proporciona instrucciones sobre cómo personalizar un clúster de HDInsight para instalar Spark.

## <a name="whatis"></a>¿Qué versión de Spark puedo instalar?

En este tema, se usa un script personalizado de acción de script para instalar Spark en un clúster de HDInsight. Este script instala Spark 1.5.1.

Puede modificar este script o crear su propio script para instalar otras versiones de Spark.

## Funcionamiento del script

Este script instala Spark versión 1.5.1 en `/usr/hdp/current/spark`.

> [AZURE.WARNING] Es posible que detecte que algunos binarios Spark 1.3.1 se instalan de forma predeterminada en el clúster de HDInsight. Estos no se deben usar, y se quitarán de la imagen del clúster de HDInsight en una futura actualización.

## <a name="install"></a>Instalación de Spark mediante acciones de script

Hay un script de ejemplo para instalar Spark en un clúster de HDInsight en un blob de almacenamiento de Azure de solo lectura que se encuentra en [https://hdiconfigactions.blob.core.windows.net/linuxsparkconfigactionv02/spark-installer-v02.sh](https://hdiconfigactions.blob.core.windows.net/linuxsparkconfigactionv02/spark-installer-v02.sh). Esta sección proporciona instrucciones sobre cómo usar el script de ejemplo al crear el clúster mediante el Portal de Azure.

> [AZURE.NOTE] También puede usar Azure PowerShell o el SDK de .NET para HDInsight para crear un clúster mediante este script. Para obtener más información sobre el uso de estos métodos, consulte [Personalización de clústeres de HDInsight mediante acciones de script](hdinsight-hadoop-customize-cluster-linux.md).

1. Comience a crear un clúster siguiendo los pasos que se describen en [Creación de clústeres de HDInsight basados en Linux](hdinsight-hadoop-create-linux-clusters-portal.md), pero no complete la operación.

2. En la hoja **Configuración opcional**, seleccione **Acciones de script** y proporcione la información siguiente:

	* __NOMBRE__: escriba un nombre descriptivo para la acción de script.
	* __URI DE SCRIPT__: https://hdiconfigactions.blob.core.windows.net/linuxsparkconfigactionv02/spark-installer-v02.sh
	* __PRINCIPAL__: active esta opción.
	* __TRABAJO__: desactive esta opción.
	* __ZOOKEEPER__: desactive esta opción.
	* __PARÁMETROS__: deje este campo en blanco.
    
    > [AZURE.NOTE] En el ejemplo de script de Spark solo se instalan componentes en los nodos principales, por lo que los demás tipos de nodo pueden estar desactivados.

3. En la parte inferior de **Acciones de script**, use el botón **Seleccionar** para guardar la configuración. Por último, use el botón **Seleccionar** situado en la parte inferior de la hoja **Configuración opcional** para guardar la información de configuración opcional.

4. Continúe creando el clúster, tal como se describe en [Creación de clústeres de HDInsight basados en Linux](hdinsight-provision-linux-clusters.md#portal).

## <a name="usespark"></a>¿Cómo uso Spark en HDInsight?

Spark proporciona las API en Scala, Python y Java. También puede usar el shell de Spark interactivo para ejecutar consultas de Spark. Una vez que el clúster ha terminado de crearse, use lo siguiente para conectarse a su clúster de HDInsight:

	ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
	
Para obtener más información sobre el uso de SSH con HDInsight, vea lo siguiente:

* [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Linux, Unix u OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

* [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

Una vez conectado, use las siguientes secciones para obtener pasos específicos sobre el uso de Spark:

- [Uso del shell de Spark para ejecutar consultas interactivas](#sparkshell)
- [Uso del shell de Spark para ejecutar consultas de Spark SQL](#sparksql) 
- [Uso de un programa de Scala independiente](#standalone)

###<a name="sparkshell"></a>Uso del shell de Spark para ejecutar consultas interactivas

1. Ejecute el siguiente comando para iniciar el shell de Spark:

		 /usr/hdp/current/spark/bin/spark-shell --master yarn

	Cuando finalice la ejecución del comando, debe obtener un símbolo de sistema de Scala:

		 scala>

5. En el símbolo de sistema de Scala, escriba la consulta de Spark que se muestra a continuación. Esta consulta cuenta la aparición de cada palabra en el archivo davinci.txt que está disponible en la ubicación /example/data/gutenberg/ en el almacenamiento de blobs de Azure asociado al clúster.

		val file = sc.textFile("/example/data/gutenberg/davinci.txt")
		val counts = file.flatMap(line => line.split(" ")).map(word => (word, 1)).reduceByKey(_ + _)
		counts.toArray().foreach(println)

6. La salida debe ser similar a la siguiente:

		(felt,,1)
		(axle-tree,1)
		(deals,9)
		(virtuous,4)

7. Escriba: q para salir del símbolo del sistema de Scala.

		:q

###<a name="sparksql"></a>Uso del shell de Spark para ejecutar consultas de Spark SQL

Spark SQL le permite usar Spark para ejecutar consultas relacionales expresadas en Lenguaje de consulta estructurado (SQL), HiveQL o Scala. En esta sección, veremos el uso de Spark para ejecutar una consulta de Hive en una tabla de Hive de ejemplo. La tabla de Hive usada en esta sección (llamada **hivesampletable**) está disponible de forma predeterminada cuando se crea un clúster.

1. Ejecute el siguiente comando para iniciar el shell de Spark:

		 /usr/hdp/current/spark/bin/spark-shell --master yarn

	Cuando finalice la ejecución del comando, debe obtener un símbolo de sistema de Scala:

		 scala>

2. En el símbolo del sistema de Scala, establezca el contexto de Hive. Esto es necesario para trabajar con consultas de Hive mediante Spark.

		val hiveContext = new org.apache.spark.sql.hive.HiveContext(sc)

	> [AZURE.NOTE]  Tenga en cuenta que `sc` en esta instrucción es el contexto de Spark predeterminado que se establece cuando se inicia el shell de Spark.

5. Ejecute una consulta de Hive mediante el contexto de Hive e imprima la salida en la consola. La consulta recupera los datos en dispositivos de una marca y limita el número de registros recuperados a 20.

		hiveContext.sql("""SELECT * FROM hivesampletable WHERE devicemake LIKE "HTC%" LIMIT 20""").collect().foreach(println)

6. Debe ver algo parecido a lo siguiente:

		[820,11:35:17,es-ES,Android,HTC,Inspire 4G,Louisiana,UnitedStates, 2.7383836,0,1]
		[1055,17:24:08,es-ES,Android,HTC,Incredible,Ohio,United States,18.0894738,0,0]
		[1067,03:42:29,es-ES,Windows Phone,HTC,HD7,District Of Columbia,United States,null,0,0]

7. Escriba: q para salir del símbolo del sistema de Scala.

		:q

### <a name="standalone"></a>Uso de un programa de Scala independiente

En esta sección se escribe una aplicación de Scala que cuenta el número de líneas que contienen las letras 'a' y 'b' en el archivo de datos de ejemplo (/example/data/gutenberg/davinci.txt.)

1. Use el comando siguiente para instalar la herramienta de compilación de Scala:

		echo "deb http://dl.bintray.com/sbt/debian /" | sudo tee -a /etc/apt/sources.list.d/sbt.list
		sudo apt-get update
		sudo apt-get install sbt
		
	Cuando se le pida, seleccione __Y__ para continuar.

2. Cree la estructura de directorio del proyecto de Scala:

		mkdir -p SimpleScalaApp/src/main/scala
		
3. Cree un nuevo archivo llamado __simple.sbt__, que contiene la información de configuración de este proyecto.

		cd SimpleScalaApp
		nano simple.sbt
		
	Use lo siguiente como contenido del archivo:

		name := "SimpleApp"
	
		version := "1.0"
	
		scalaVersion := "2.10.4"
	
		libraryDependencies += "org.apache.spark" %% "spark-core" % "1.2.0"


	> [AZURE.NOTE] Asegúrese de que conserva las líneas vacías entre cada entrada.
	
	Presione __Ctrl-X__ y luego __Y__ y __Entrar__ para guardar el archivo.

4. Use el siguiente comando para crear un nuevo archivo llamado __SimpleApp.scala__ en el directorio __SimpleScalaApp/src/main/scala__:

		nano src/main/scala/SimpleApp.scala

	Use lo siguiente como contenido del archivo:

		/* SimpleApp.scala */
		import org.apache.spark.SparkContext
		import org.apache.spark.SparkContext._
		import org.apache.spark.SparkConf
		
		object SimpleApp {
		  def main(args: Array[String]) {
		    val logFile = "/example/data/gutenberg/davinci.txt"			//Location of the sample data file on Azure Blob storage
		    val conf = new SparkConf().setAppName("SimpleApplication")
		    val sc = new SparkContext(conf)
		    val logData = sc.textFile(logFile, 2).cache()
		    val numAs = logData.filter(line => line.contains("a")).count()
		    val numBs = logData.filter(line => line.contains("b")).count()
		    println("Lines with a: %s, Lines with b: %s".format(numAs, numBs))
		  }
		}

	Presione __Ctrl-X__ y luego __Y__ y __Entrar__ para guardar el archivo.

5. En el directorio __SimpleScalaApp__, use el siguiente comando para compilar la aplicación y almacenarla en un archivo jar:

		sbt package

	Después de compilar la aplicación, verá un archivo **simpleapp\_2.10-1.0.jar** creado en el directorio \_\_SimpleScalaApp/target/scala-2.10**.

6. Escriba el siguiente comando para ejecutar el programa de SimpleApp.scala:


		/usr/hdp/current/spark/bin/spark-submit --class "SimpleApp" --master yarn target/scala-2.10/simpleapp_2.10-1.0.jar

4. Cuando el programa termine de ejecutarse, el resultado aparecerá en la consola.

		Lines with a: 21374, Lines with b: 11430

## Pasos siguientes

- [Instalación y uso de Hue en clústeres de HDInsight](hdinsight-hadoop-hue-linux.md) Hue es una interfaz de usuario web que simplifica la creación, la ejecución y el guardado de trabajos de Pig y Hive, así como el examen del almacenamiento predeterminado de su clúster de HDInsight.

- [Instalación de R en clústeres de HDInsight][hdinsight-install-r] proporciona instrucciones acerca de cómo usar la personalización del clúster para instalar y usar R en clústeres de Hadoop de HDInsight. R es un entorno y lenguaje de código abierto para computación estadística. Proporciona cientos de de funciones estadísticas integradas y su propio lenguaje de programación que combina aspectos de la programación funcional y orientada a objetos. También proporciona amplias capacidades gráficas.

- [Instalación de Giraph en clústeres de HDInsight](hdinsight-hadoop-giraph-install-linux.md). Use la personalización del clúster para instalar Giraph en clústeres de Hadoop para HDInsight. Giraph permite realizar un procesamiento gráfico mediante Hadoop, y se puede usar con HDInsight de Azure.

- [Instalación de Solr en clústeres de HDInsight](hdinsight-hadoop-solr-install-linux.md). Use la personalización del clúster para instalar Solr en clústeres de Hadoop para HDInsight. Solr le permite realizar potentes operaciones de búsqueda en los datos almacenados.

- [Instalación de Hue en clústeres de HDInsight](hdinsight-hadoop-hue-linux.md). Use la personalización del clúster para instalar Hue en clústeres de Hadoop para HDInsight. Hue es un conjunto de aplicaciones web que usan para interactuar con un clúster de Hadoop.



[hdinsight-install-r]: hdinsight-hadoop-r-scripts-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
 

<!---HONumber=AcomDC_0211_2016-->