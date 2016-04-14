<properties 
	pageTitle="Kernels disponibles para cuadernos de Jupyter con clústeres de HDInsight Spark en Linux | Microsoft Azure" 
	description="Obtenga información sobre los kernels del cuaderno de Jupyter adicionales disponibles con el clúster de HDInsight Spark en Linux." 
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


# Kernels disponibles para cuadernos de Jupyter con clústeres Spark en HDInsight (Linux)

El clúster Apache Spark en HDInsight (Linux) incluye cuadernos de Jupyter que puede usar para probar las aplicaciones. De forma predeterminada el cuaderno de Jupyter incorpora un kernel **Python2**. Un kernel es un programa que ejecuta e interpreta el código. Los clústeres de HDInsight Spark proporcionan dos kernels adicionales que puede utilizar con el cuaderno de Jupyter. Dichos componentes son:

1. **PySpark** (para aplicaciones escritas con Python)
2. **Spark** (para aplicaciones escritas con Scala)

En este artículo, obtendrá información sobre cómo usar estos kernels y cuáles son los beneficios que obtiene de uso.

**Requisitos previos:**

Debe tener lo siguiente:

- Una suscripción de Azure. Consulte [How to get Azure Free trial for testing Hadoop in HDInsight (Obtención de una versión de prueba gratuita de Azure para probar Hadoop en HDInsight)](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Un clúster Apache Spark en HDInsight Linux. Para obtener instrucciones, vea [Creación de clústeres Apache Spark en HDInsight de Azure](hdinsight-apache-spark-jupyter-spark-sql.md).

## ¿Cómo puedo usar los kernels? 

1. Desde el [Portal de vista previa de Azure](https://portal.azure.com/), en el panel de inicio, haga clic en el icono del clúster Spark (si lo ha anclado al panel de inicio). También puede navegar hasta el clúster en **Examinar todo** > **Clústeres de HDInsight**.   

2. En la hoja del clúster Spark, haga clic en **Vínculos rápidos** y, luego, en la hoja **Panel de clúster**, haga clic en **Jupyter Notebook**. Cuando se le pida, escriba las credenciales del clúster.

	> [AZURE.NOTE] También puede comunicarse con el equipo Jupyter Notebook en el clúster si abre la siguiente dirección URL en el explorador. Reemplace __CLUSTERNAME__ por el nombre del clúster:
	>
	> `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Cree un nuevo cuaderno con los kernels nuevos. Haga clic en **New** (Nuevo) y, luego, en **Pyspark** o **Spark**. Debe utilizar el kernel de Spark para aplicaciones de Scala y kernel PySpark para aplicaciones de Python.

	![Crear un nuevo cuaderno de Jupyter](./media/hdinsight-apache-spark-jupyter-notebook-kernels/jupyter-kernels.png "Crear un nuevo cuaderno de Jupyter")

3. Esto debería abrir un nuevo cuaderno con el kernel seleccionado.

## ¿Por qué debo usar los kernels nuevos?

Utilizar los nuevos kernels aporta un par de beneficios.

1. **Contextos preestablecidos**. Con el kernel predeterminado, **Python2**, que está disponible con cuadernos de Jupyter, tiene que establecer explícitamente los contextos Spark, SQL o Hive para poder empezar a trabajar con la aplicación que desarrolla. Si usa los nuevos kernels (**PySpark** o **Spark**), estos contextos están disponibles de forma predeterminada. Estos contextos son:

	* **sc**: para el contexto Spark
	* **sqlContext**: para el contexto SQL
	* **hiveContext**: para el contexto Hive


	Por tanto, no tiene que ejecutar instrucciones como la siguiente para definir los contextos:

		###################################################
		# YOU DO NOT NEED TO RUN THIS WITH THE NEW KERNELS
		###################################################
		sc = SparkContext('yarn-client')
		sqlContext = SQLContext(sc)
		hiveContext = HiveContext(sc)

	En su lugar, puede utilizar directamente los contextos preestablecidos en la aplicación.
	
2. **Comandos mágicos de celda**. El kernel PySpark proporciona algunas "instrucciones mágicas" predefinidas, que son comandos especiales que se pueden llamar con `%%` (por ejemplo, `%%MAGIC` <args>). El comando mágico debe ser la primera palabra de una celda de código y permitir varias líneas de contenido. La palabra mágica debe ser la primera palabra en la celda. Si se agrega algo antes de la palabra mágica, incluso comentarios, se producirá un error. Para más información sobre instrucciones mágicas, consulte [aquí](http://ipython.readthedocs.org/en/stable/interactive/magics.html).

	La tabla siguiente muestra las diferentes instrucciones mágicas disponibles a través de los kernels.

	| Instrucción mágica | Ejemplo | Descripción |
	|-----------|---------------------------------|--------------|
	| help | `%%help` | Genera una tabla de todas las instrucciones mágicas disponibles con el ejemplo y la descripción |
	| info | `%%info` | Produce información de sesión del punto de conexión actual de Livy. |
	| configurar | `%%configure -f {"executorMemory": "1000M", "executorCores": 4`} | Configura los parámetros para crear una sesión. La marca force (-f) es obligatoria si ya se ha creado una sesión, que se quitará y se vuelve a crear. Consulte [Cuerpo de la solicitud de sesiones o POST de Livy](https://github.com/cloudera/livy#request-body) para obtener una lista de parámetros válidos. Los parámetros deben pasarse en forma de cadena JSON. |
	| sql | `%%sql -o <variable name>`<br> `SHOW TABLES` | Ejecuta una consulta SQL en el sqlContext. Si se pasa el parámetro `-o`, el resultado de la consulta se conserva en el contexto %%local de Python como trama de datos [Pandas](http://pandas.pydata.org/). |
	| hive | `%%hive -o <variable name>`<br> `SHOW TABLES` | Ejecuta una consulta de Hive en el hivelContext. Si se pasa el parámetro -o, el resultado de la consulta se conserva en el contexto %%local de Python como trama de datos [Pandas](http://pandas.pydata.org/). |
	| local | `%%local`<br>`a=1` | Todo el código de las líneas siguientes se ejecutará localmente. El código debe ser un código de Python válido. |
	| logs | `%%logs` | Genera los registros de la sesión actual de Livy. |
	| delete | `%%delete -f -s <session number>` | Elimina una sesión específica del punto de conexión actual de Livy. Tenga en cuenta que no se puede eliminar la sesión iniciada en el propio kernel. |
	| cleanup | `%%cleanup -f` | Elimina todas las sesiones del punto de conexión actual de Livy, incluida la sesión de este cuaderno. La marca force -f es obligatoria. |

3. **Visualización automática**. El kernel **Pyspark** visualiza automáticamente el resultado de las consultas de Hive y SQL. Tiene la posibilidad de elegir entre diferentes tipos de visualizaciones como tabla, circular, línea, área, barra.


## Consideraciones al utilizar los kernels nuevos

Sea cual sea el kernel usado (Python2, PySpark o Spark), dejar los cuadernos en ejecución consumirá los recursos del clúster. Con el cuaderno de Python2, como crea los contextos de manera explícita, también puede terminar los contextos al salir de la aplicación.

Sin embargo, con los kernels PySpark y Spark, como los contextos están preestablecidos, tampoco puede cerrar el contexto de manera explícita. Por tanto,si solo sale del cuaderno, es posible que el contexto continúe en ejecución, de manera que utilizará los recursos del clúster. Una buena práctica con los kernels PySpark y Spark sería utilizar la opción **Cerrar y detener** del menú **Archivo** del cuaderno. Esto termina el contexto y luego sale del cuaderno.


## Estos son algunos ejemplos:

Al abrir un cuaderno de Jupyter, verá dos carpetas disponibles en el nivel raíz.

* La carpeta **PySpark** tiene cuadernos de ejemplo que usan el nuevo kernel **Spark**.
* La carpeta **Scala** tiene cuadernos de ejemplo que usan el kernel **Spark** nuevo.

Puede abrir el cuaderno **00 - [READ ME FIRST] Spark Magic Kernel Features** (Características del kernel de instrucción mágica de Spark) de la carpeta **PySpark** o **Spark** para más información sobre las distintas instrucciones mágicas disponibles. Puede usar los demás cuadernos de ejemplo disponibles en las dos carpetas para aprender a lograr distintos escenarios con cuadernos de Jupyter con clústeres de HDInsight Spark.

## Comentarios

El nuevo kernel está en la fase de evolución y se desarrollará con el tiempo. También podría significar que las API podrían cambiar a medida que estos kernels maduran. Agradecemos cualquier comentario que tenga al utilizar estos nuevos kernels. Esto será muy útil para dar forma a la versión final de estos kernels. Puede dejar sus comentarios la sección **Comentarios** al final de este artículo.


## <a name="seealso"></a>Otras referencias


* [Introducción a Apache Spark en HDInsight de Azure](hdinsight-apache-spark-overview.md)

### Escenarios

* [Spark with BI: Realizar el análisis de datos interactivos con Spark en HDInsight con las herramientas de BI](hdinsight-apache-spark-use-bi-tools.md)

* [Creación de aplicaciones de Aprendizaje automático con Apache Spark en HDInsight de Azure](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Spark con aprendizaje automático: uso de Spark en HDInsight para predecir los resultados de la inspección de alimentos](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Streaming con Spark: uso de Spark en HDInsight para compilar aplicaciones de streaming en tiempo real](hdinsight-apache-spark-eventhub-streaming.md)

* [Análisis del registro del sitio web con Spark en HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### Creación y ejecución de aplicaciones

* [Crear una aplicación independiente con Scala](hdinsight-apache-spark-create-standalone-application.md)

* [Submit Spark jobs remotely using Livy with Spark clusters on HDInsight (Linux)](hdinsight-apache-spark-livy-rest-interface.md)

### Herramientas y extensiones

* [Use HDInsight Tools Plugin for IntelliJ IDEA to create and submit Spark Scala applications (Uso del complemento de herramientas de HDInsight para IntelliJ IDEA para crear y enviar aplicaciones Scala Spark)](hdinsight-apache-spark-intellij-tool-plugin.md)

* [Uso de cuadernos de Zeppelin con un clúster Spark en HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)

### Administración de recursos

* [Administración de recursos para el clúster Apache Spark en HDInsight de Azure](hdinsight-apache-spark-resource-manager.md)

<!---HONumber=AcomDC_0224_2016-->