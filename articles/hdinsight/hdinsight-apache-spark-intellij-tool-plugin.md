 <properties 
	pageTitle="Creación de aplicaciones Spark en Scala con el complemento de HDInsight para IntelliJ IDEA | Microsoft Azure" 
	description="Obtenga información sobre cómo crear una aplicación independiente Spark para ejecutarla en clústeres de HDInsight Spark." 
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
	ms.date="02/05/2016" 
	ms.author="nitinme"/>


# Uso del complemento Herramientas de HDInsight para IntelliJ IDEA para crear y enviar aplicaciones Spark en Scala (Linux)

En este artículo se proporcionan instrucciones paso a paso sobre cómo desarrollar aplicaciones Spark escritas en Scala y enviarlas a un clúster de HDInsight Spark mediante el complemento de HDInsight para IntelliJ IDEA. Puede usar el complemento de varias maneras diferentes:

* Para desarrollar y enviar una aplicación Spark en Scala en un clúster de HDInsight Spark
* Para acceder a los recursos del clúster de Azure HDInsight Spark
* Para desarrollar y ejecutar localmente una aplicación Spark en Scala

>[AZURE.IMPORTANT] Esta herramienta se puede utilizar para crear y enviar solicitudes solo para un clúster de HDInsight Spark en Linux.


**Requisitos previos**

* Una suscripción de Azure. Consulte [How to get Azure Free trial for testing Hadoop in HDInsight (Obtención de una versión de prueba gratuita de Azure para probar Hadoop en HDInsight)](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Un clúster Apache Spark en HDInsight Linux. Para obtener instrucciones, vea [Creación de clústeres Apache Spark en HDInsight de Azure](hdinsight-apache-spark-jupyter-spark-sql.md).
* Kit de desarrollo de Oracle Java. Puede instalarlo desde [aquí](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
* IntelliJ IDEA. En este artículo se usa la versión 15.0.1. Puede instalarlo desde [aquí](https://www.jetbrains.com/idea/download/). 


## Instale el complemento de Scala para IntelliJ IDEA.

Si la instalación de IntelliJ IDEA no pide habilitar el complemento de Scala, inicie IntelliJ IDEA y siga los pasos siguientes para instalar el complemento:

1. Inicie IntelliJ IDEA y, en la pantalla de bienvenida, haga clic en **Configure** (Configurar) y luego en **Plugins** (Complementos).

	![Habilitar complemento de Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/enable-scala-plugin.png)

2. En la siguiente pantalla, haga clic en **Install JetBrains plugin** (Instalar complemento de JetBrains) en la esquina inferior izquierda. En el cuadro de diálogo **Browse JetBrains Plugins** (Examinar complementos de JetBrains) que se abre, busque Scala y luego haga clic en **Install** (Instalar).

	![Instalar complemento de Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/install-scala-plugin.png)

3. Después de que el complemento se instala correctamente, se le pedirá que reinicie el IDE. Para omitir ese paso por ahora.

## Instalación del complemento Herramientas de HDInsight para IntelliJ IDEA

1. Si vuelve a la pantalla de inicio de sesión de IntelliJ IDEA, haga clic en **Configure** (Configurar) y luego en **Plugins** (Complementos).

2. En la siguiente pantalla, haga clic en **Browse Repositories** (Examinar repositorios) en la esquina inferior izquierda. En el cuadro de diálogo **Browse Repositories** (Explorar repositorios), busque **HDInsight**, seleccione **Microsoft Azure HDInsight Tools for IntelliJ** (Herramientas de HDInsight de Microsoft Azure para IntelliJ) y luego haga clic en **Install** (Instalar).

3. Cuando se le solicite, haga clic en el botón **Restart IntelliJ IDEA** (Reiniciar IntelliJ IDEA) para reiniciar el IDE.

## Ejecución de una aplicación Spark en Scala en un clúster de HDInsight Spark

1. Inicie IntelliJ IDEA y cree un nuevo proyecto. En el cuadro de diálogo del nuevo proyecto, realice las siguientes selecciones y después haga clic en **Next** (Siguiente).

	![Crear aplicación Spark en Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)

	* En el panel izquierdo, seleccione **HDInsight**.
	* En el panel derecho, seleccione **Spark on HDInsight (Scala)** (Spark en HDInsight (Scala)).
	* Haga clic en **Siguiente**.

2. En la siguiente ventana, proporcione los detalles del proyecto.

	* Proporcione un nombre de proyecto y la ubicación del proyecto.
	* En **Project SDK** (SDK de proyecto), asegúrese de proporcionar una versión de Java mayor que 7.
	* En **Scala SDK** (SDK de Scala), haga clic en **Create** (Crear), haga clic en **Download** (Descargar) y luego seleccione la versión de Scala que desea usar. **Asegúrese de no usar la versión 2.11.x**. En este ejemplo se usa la versión **2.10.6**.
	
		![Crear aplicación Spark en Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-scala-version.png)

	* Para **Spark SDK** (SDK de Spark), descargue y use el SDK desde [aquí](http://go.microsoft.com/fwlink/?LinkID=723585&clcid=0x409).

		![Crear aplicación Spark en Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-scala-project-details.png)

	* Haga clic en **Finalizar**

3. Defina la estructura del proyecto para crear un artefacto (jar empaquetado) que, en última instancia, contendrá el código que se ejecuta en el clúster.

	1. En el menú **File** (Archivo), haga clic en **Project Structure** (Estructura del proyecto).
	2. En el cuadro de diálogo **Project Structure** (Estructura de proyecto), haga clic en **Artifacts** (Artefactos) y, después, haga clic en el signo más. En el cuadro de diálogo emergente, haga clic en **JAR** y luego haga clic en **Empty** (Vacío).

		![Crear archivo JAR](./media/hdinsight-apache-spark-intellij-tool-plugin/create-jar-1.png)

	3. Escriba un nombre para el archivo JAR (por ejemplo, **MyClusterApp**). En el panel de elementos disponibles, haga clic con el botón derecho en **'MyClusterApp' compile output** (Salida de compilación de 'MyClusterApp') y luego haga clic en **Put into Output Root** (Colocar en la raíz de salida).

		![Crear archivo JAR](./media/hdinsight-apache-spark-intellij-tool-plugin/create-jar-2.png)
	
	4. Haga clic en **Apply** (Aplicar) y luego en **OK** (Aceptar).

4. Agregue el código fuente de aplicación.

	1. En **Project Explorer** (Explorador de proyectos), haga clic con el botón derecho en **src**, seleccione **New** (Nueva) y luego haga clic en **Scala class** (Clase de Scala).

		![Agregar código fuente](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png)

	2. En el cuadro de diálogo **Create New Scala Class** (Crear nueva clase de Scala), proporcione un nombre, para **Kind** (Variante) seleccione **Object** (Objeto) y luego haga clic en **OK** (Aceptar).

		![Agregar código fuente](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png)

	3. En el archivo **MyClusterApp.scala**, pegue el siguiente código. Este código lee los datos de HVAC.csv (disponible en todos los clústeres de HDInsight Spark), recupera las filas que solo tienen un dígito en la séptima columna del CSV y escribe el resultado en **/HVACOut** bajo el contenedor de almacenamiento predeterminado para el clúster.


			import org.apache.spark.SparkConf
			import org.apache.spark.SparkContext
			
			object MyClusterApp{
			  def main (arg: Array[String]): Unit = {
			    val conf = new SparkConf().setAppName("MyClusterApp")
			    val sc = new SparkContext(conf)
			
			    val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
			
			    //find the rows which have only one digit in the 7th column in the CSV
				val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)
			
			    rdd1.saveAsTextFile("wasb:///HVACOut")
			  }
			
			}

5. Ejecute la aplicación en un clúster de HDInsight Spark.

	1. En **Project Explorer** (Explorador de proyectos), haga clic con el botón derecho en el nombre de proyecto y luego seleccione **Submit Spark Application to HDInsight** (Enviar aplicación Spark a HDInsight).

		![Enviar aplicación Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)

	2. Se le pedirá que escriba las credenciales de la suscripción de Azure. En el cuadro de diálogo **Spark Submission** (Envío de Spark), proporcione los siguientes valores.

		![Enviar aplicación Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-2.png)

		* Para **Spark clusters (Linux only)** (Clústeres de Spark (solo Linux)), seleccione el clúster de HDInsight Spark en el que desea ejecutar la aplicación.

		* En el menú desplegable **Build Artifacts** (Artefactos de compilación) debe aparecer el nombre JAR especificado en los pasos anteriores.

		* En el cuadro de texto **Main class name** (Nombre de clase principal), haga clic en los puntos suspensivos (![puntos suspensivos](./media/hdinsight-apache-spark-intellij-tool-plugin/ellipsis.png) ), seleccione la clase principal del código fuente de aplicación y luego haga clic en **OK** (Aceptar).

			![Enviar aplicación Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-3.png)
	
		* Dado que el código de aplicación de este ejemplo no requiere ningún argumento de línea de comandos ni archivos o JAR de referencia, puede dejar vacíos los demás cuadros de texto.
	
		* Haga clic en **Enviar**.

	3. La pestaña **Spark Submission** (Envío de Spark) de la parte inferior de la ventana debe empezar a mostrar el progreso.

	En la siguiente sección, obtendrá información sobre cómo acceder a la salida de trabajo con el complemento de HDInsight para IntelliJ IDEA.


## Acceso y administración de clústeres de HDInsight Spark mediante el complemento de HDInsight para IntelliJ

Puede realizar una serie de operaciones con el complemento de HDInsight.

### Acceso al contenedor de almacenamiento para el clúster

1. En el menú **View** (Ver), seleccione **Tool Windows** (Ventanas de herramientas) y luego haga clic en **HDInsight Explorer** (Explorador de HDInsight). Si se le solicita, introduzca las credenciales para acceder a su suscripción de Azure.

2. Expanda el nodo raíz **HDInsight** para ver una lista de los clústeres disponibles de HDInsight Spark.

3. Expanda el nombre del clúster para ver la cuenta de almacenamiento y el contenedor de almacenamiento predeterminado para el clúster.

	![Acceder a almacenamiento de clúster](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-access-storage.png)

4. Haga clic en el nombre del contenedor de almacenamiento asociado con el clúster. En el panel derecho, verá una carpeta llamada **HVACOut**. Haga doble clic para abrir la carpeta y verá archivos **part-***. Abra uno de esos archivos para ver el resultado de la aplicación.

### Acceso al servidor de historial de Spark

1. En el **Explorador de HDInsight**, haga clic con el botón derecho en el nombre de clúster de Spark y luego seleccione **Open Spark History UI** (Abrir IU de historial de Spark). Cuando se le pida, escriba las credenciales de administrador para el clúster. Debe haberlas especificado al aprovisionar el clúster.

2. En el panel del Servidor de historial de Spark, puede buscar la aplicación que acaba de terminar de ejecutar con la utilización del nombre de aplicación. En el código anterior, definió el nombre de aplicación con `val conf = new SparkConf().setAppName("MyClusterApp")`. Por lo tanto, el nombre de aplicación Spark era **MyClusterApp**.

### Inicio del portal de Ambari

En el **Explorador de HDInsight**, haga clic con el botón derecho en el nombre de clúster de Spark y luego seleccione **Open Cluster Management Portal (Ambari)** (Abrir portal de administración de clústeres (Ambari)). Cuando se le pida, escriba las credenciales de administrador para el clúster. Debe haberlas especificado al aprovisionar el clúster.

### Administración de suscripciones de Azure

De forma predeterminada, el complemento HDInsight enumera los clústeres de Spark de todas las suscripciones de Azure. Si es necesario, puede especificar las suscripciones para las que desea tener acceso al clúster. En el **Explorador de HDInsight**, haga clic con el botón derecho en el nodo raíz de **HDInsight** y luego haga clic en **Manage Subscriptions** (Administrar suscripciones). En el cuadro de diálogo, desactive las casillas de la suscripción en la que no quiera tener acceso y luego haga clic en **Close** (Cerrar). También puede hacer clic en **Sign Out** (Cerrar sesión) si desea cerrar sesión en su suscripción de Azure.


## Ejecución local de una aplicación Spark en Scala

Puede utilizar el complemento Herramientas de HDInsight para IntelliJ IDEA para ejecutar aplicaciones Spark en Scala localmente en su estación de trabajo. Normalmente, dichas aplicaciones no necesitan tener acceso a los recursos de clúster, como el contenedor de almacenamiento, y se pueden ejecutar y probar de forma local.

### Requisito previo

Mientras se ejecuta la aplicación Spark en Scala local en un equipo Windows, puede obtener una excepción, como se explica en [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356), que se produce por la ausencia del archivo WinUtils.exe en Windows. Para solucionar este error, debe [descargar el archivo ejecutable desde aquí](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) en una ubicación como **C:\\WinUtils\\bin**. A continuación, debe agregar una variable de entorno **HADOOP\_HOME** y establecer el valor de la variable en **C\\WinUtils**.

### Ejecución de una aplicación Spark en Scala local	 

1. Inicie IntelliJ IDEA y cree un nuevo proyecto. En el cuadro de diálogo del nuevo proyecto, realice las siguientes selecciones y después haga clic en **Next** (Siguiente).

	![Crear aplicación Spark en Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)

	* En el panel izquierdo, seleccione **HDInsight**.
	* En el panel derecho, seleccione **Spark on HDInsight (Scala)** (Spark en HDInsight (Scala)).
	* Haga clic en **Siguiente**.

2. En la siguiente ventana, proporcione los detalles del proyecto.

	* Proporcione un nombre de proyecto y la ubicación del proyecto.
	* En **Project SDK** (SDK de proyecto), asegúrese de proporcionar una versión de Java mayor que 7.
	* En **Scala SDK** (SDK de Scala), haga clic en **Create** (Crear), haga clic en **Download** (Descargar) y luego seleccione la versión de Scala que desea usar. **Asegúrese de no usar la versión 2.11.x**. En este ejemplo se usa la versión **2.10.6**.
	
		![Crear aplicación Spark en Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-scala-version.png)

	* Para **Spark SDK** (SDK de Spark), descargue y use el SDK desde [aquí](http://go.microsoft.com/fwlink/?LinkID=723585&clcid=0x409).
	* Haga clic en **Finalizar**

		![Crear aplicación Spark en Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-scala-local-project-details.png)

3. Defina la estructura del proyecto para crear un artefacto (jar empaquetado) que, en última instancia, contendrá el código que se ejecuta en el clúster.

	1. En el menú **File** (Archivo), haga clic en **Project Structure** (Estructura del proyecto).
	2. En el cuadro de diálogo **Project Structure** (Estructura de proyecto), haga clic en **Artifacts** (Artefactos) y, después, haga clic en el signo más. En el cuadro de diálogo emergente, haga clic en **JAR** y luego haga clic en **Empty** (Vacío).

		![Crear archivo JAR](./media/hdinsight-apache-spark-intellij-tool-plugin/create-jar-1.png)

	3. Escriba un nombre para el archivo JAR (por ejemplo, **MyLocalApp**). En el panel de elementos disponibles, haga clic con el botón derecho en **'MyLocalApp' compile output** (Salida de compilación de 'MyLocalApp') y luego haga clic en **Put into Output Root** (Colocar en la raíz de salida).

		![Crear archivo JAR](./media/hdinsight-apache-spark-intellij-tool-plugin/create-local-jar-2.png)
	
	4. Haga clic en **Apply** (Aplicar) y luego en **OK** (Aceptar).

4. Agregue el código fuente de aplicación.

	1. En **Project Explorer** (Explorador de proyectos), haga clic con el botón derecho en **src**, seleccione **New** (Nueva) y luego haga clic en **Scala class** (Clase de Scala).

		![Agregar código fuente](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-local-scala-code.png)

	2. En el cuadro de diálogo **Create New Scala Class** (Crear nueva clase de Scala), proporcione un nombre, para **Kind** (Variante) seleccione **Object** (Objeto) y luego haga clic en **OK** (Aceptar).

		![Agregar código fuente](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-local-scala-code-object.png)

	3. En el archivo **MyLocalApp.scala**, pegue el siguiente código. Este código lee un archivo de texto de entrada de ejemplo en el equipo e imprime el número de líneas de ese archivo de texto que contienen los caracteres "a" y "b".


			import org.apache.spark.SparkContext
			import org.apache.spark.SparkContext._
			import org.apache.spark.SparkConf
			
			object MyLocalApp {
			  def main(args: Array[String]) {
			    val logFile = "C:/users/nitinme/Desktop/commands.txt" // Should be some file on your system
			    val conf = new SparkConf().setAppName("MyLocalApp").setMaster("local[2]")
			    val sc = new SparkContext(conf)
			    val logData = sc.textFile(logFile, 2).cache()
			    val numAs = logData.filter(line => line.contains("a")).count()
			    val numBs = logData.filter(line => line.contains("b")).count()
			    println("Lines with a: %s, Lines with b: %s".format(numAs, numBs))
			  }
			}

5. Ejecute la aplicación localmente en la estación de trabajo. En el menú **Run** (Ejecutar), haga clic en **Run 'MyLocalApp'** (Ejecutar 'MyLocalApp'). Verá un resultado similar al siguiente en la pestaña **Run** (Ejecutar) en la parte inferior.

		...
		...
		Lines with a: 4, Lines with b: 4
		...
		...
		16/02/01 15:04:05 INFO SparkContext: Successfully stopped SparkContext
		16/02/01 15:04:05 INFO ShutdownHookManager: Shutdown hook called
		16/02/01 15:04:05 INFO ShutdownHookManager: Deleting directory C:\Users\nitinme\AppData\Local\Temp\spark-618dee33-45a3-4bce-a8fc-bf85663133b3
		
		Process finished with exit code 0


## Conversión de las aplicaciones IntelliJ IDEA existentes para usar el complemento de la herramienta de HDInsight

También puede convertir las aplicaciones existentes Spark en Scala creadas en IntelliJ IDEA para que sean compatibles con el complemento de la herramienta de HDInsight. Esto le permitirá utilizar la herramienta para enviar las aplicaciones a un clúster de HDInsight Spark. Para ello, puede seguir estos pasos:

1. Para una aplicación Spark en Scala existente creada con IntelliJ IDEA, abra el archivo .iml asociado.
2. En el nivel raíz, verá un elemento de **módulo** similar al siguiente:

		<module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">

3. Edite el elemento que se va a agregar `UniqueKey="HDInsightTool"` para que el elemento de **módulo** sea similar al siguiente:

		<module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">

4. Guarde los cambios. La aplicación ahora debe ser compatible con el complemento de la herramienta de HDInsight. Puede comprobarlo si hace clic con el botón derecho en el nombre de proyecto en el Explorador de proyectos. El menú emergente ahora debería tener la opción para **Enviar la aplicación Spark a HDInsight**.

## <a name="seealso"></a>Otras referencias


* [Introducción a Apache Spark en HDInsight de Azure](hdinsight-apache-spark-overview.md)

### Escenarios

* [Spark with BI: Realizar el análisis de datos interactivos con Spark en HDInsight con las herramientas de BI](hdinsight-apache-spark-use-bi-tools.md)

* [Creación de aplicaciones de Aprendizaje automático con Apache Spark en HDInsight de Azure](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Spark con aprendizaje automático: uso de Spark en HDInsight para predecir los resultados de la inspección de alimentos](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Streaming con Spark: uso de Spark en HDInsight para compilar aplicaciones de streaming en tiempo real](hdinsight-apache-spark-eventhub-streaming.md)

* [Análisis del registro del sitio web con Spark en HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### Creación y ejecución de aplicaciones

* [Submit Spark jobs remotely using Livy with Spark clusters on HDInsight (Linux)](hdinsight-apache-spark-livy-rest-interface.md)

### Herramientas y extensiones

* [Uso de cuadernos de Zeppelin con un clúster Spark en HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [Kernels available for Jupyter notebook in Spark cluster for HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)

### Administración de recursos

* [Administración de recursos para el clúster Apache Spark en HDInsight de Azure](hdinsight-apache-spark-resource-manager.md)

<!---HONumber=AcomDC_0302_2016-->