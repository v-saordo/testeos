<properties
	pageTitle="Creación de un clúster Spark en HDInsight Linux y uso de Spark SQL desde Jupyter para análisis interactivos | Microsoft Azure"
	description="Instrucciones paso a paso sobre cómo crear rápidamente un clúster Apache Spark en HDInsight y luego usar Spark SQL en cuadernos de Jupyter para ejecutar consultas interactivas."
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
	ms.topic="get-started-article"
	ms.date="03/07/2016"
	ms.author="nitinme"/>


# Introducción: creación de clústeres Apache Spark en HDInsight de Azure (Linux) y ejecución de consultas interactivas mediante Spark SQL

Obtenga información sobre cómo crear un clúster Apache Spark en HDInsight y después usar cuadernos de [Jupyter](https://jupyter.org) para ejecutar consultas interactivas de Spark SQL en el clúster Spark.

   ![Introducción al uso de Apache Spark en HDInsight](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.getstartedflow.png "Tutorial de introducción al uso de Apache Spark en HDInsight. Pasos que se muestran: crear una cuenta de almacenamiento; crear un clúster; ejecutar instrucciones Spark SQL")

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

**Requisitos previos:**

- **Una suscripción de Azure**. Antes de comenzar este tutorial, debe tener una suscripción a Azure. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Un cliente de Shell seguro**: sistemas Linux, Unix y OS X ofrecen un cliente SSH mediante el comando `ssh`. Para sistemas Windows, se recomienda [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).
    
- **Claves de Shell seguro (SSH) (opcional)**: puede proteger la cuenta de SSH usada para conectarse al clúster mediante una contraseña o una clave pública. El uso de una contraseña le permite comenzar rápidamente, y debe usar esta opción si desea crear un clúster y ejecutar algunos trabajos de prueba rápidamente. El uso de una clave es un método más seguro, sin embargo, requiere configuración adicional. Puede ser conveniente usar este enfoque al crear un clúster en producción. En este artículo, se usa el enfoque de contraseña. Para obtener instrucciones sobre cómo crear y usar claves SSH con HDInsight, consulte los siguientes artículos:

	-  Desde un equipo con Linux: [Utilización de SSH con HDInsight basado en Linux (Hadoop) desde Linux, Unix u OS X](hdinsight-hadoop-linux-use-ssh-unix.md).
    
	-  Desde un equipo con Windows: [Utilización de SSH con HDInsight basado en Linux (Hadoop) desde Windows](hdinsight-hadoop-linux-use-ssh-windows.md).


## Creación de un clúster Spark en HDInsight Linux

En esta sección, se crea un clúster de HDInsight versión 3.3, que se basa en la versión 1.5.1 de Spark. Para obtener información acerca de las diferentes versiones de HDInsight y sus contratos de nivel de servicio, consulte la página [Control de versiones de componentes de HDInsight](hdinsight-component-versioning.md).

>[AZURE.NOTE] En este artículo, se proporcionan pasos para crear un clúster Apache Spark en HDInsight con los valores de configuración básica. Si quiere información sobre otras opciones de configuración del clúster (como usar almacenamiento adicional, una red virtual de Azure o una tienda de metadatos para Hive), vea [Creación de clústeres HDInsight Spark usando opciones personalizadas](hdinsight-hadoop-provision-linux-clusters.md).


**Para crear un clúster Spark**

1. Inicie sesión en el [Portal de vista previa de Azure](https://ms.portal.azure.com/).

2. Haga clic en **NUEVO**, en **Datos y análisis** y luego en **HDInsight**.

    ![Crear un nuevo clúster en el Portal de vista previa de Azure](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.createcluster.1.png "Crear un nuevo clúster en el Portal de vista previa de Azure")

3. Escriba un nombre en **Nombre de clúster**, seleccione **Spark** en **Tipo de clúster** y, en el menú desplegable **Sistema operativo de clúster**, seleccione **Linux** y luego seleccione la versión de Spark. Si está disponible, aparece una marca de verificación verde junto al nombre de clúster.

	![Especifique el tipo y el nombre del clúster](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.createcluster.2.png "Especifique el tipo y el nombre del clúster")

4. Si tiene más de una suscripción, haga clic en la entrada **Suscripción** para seleccionar la suscripción de Azure que se usará para el clúster.

5. Haga clic en **Grupo de recursos** para ver una lista de grupos de recursos existentes y seleccione dónde crear el clúster. También puede hacer clic en **Crear nuevo** y luego escribir el nombre del nuevo grupo de recursos. Aparece una marca de verificación verde para indicar si el nuevo nombre de grupo está disponible.

	> [AZURE.NOTE] Esta entrada se establece de manera predeterminada en uno de los grupos de recursos existentes, si hay alguno disponible.

6. Haga clic en **Credenciales** y escriba una contraseña para el usuario administrador. También debe especificar un **nombre de usuario de SSH**. En **Tipo de autenticación SSH**, haga clic en **CONTRASEÑA** y especifique una contraseña para el usuario de SSH. Haga clic en **Seleccionar** en la parte inferior para guardar la configuración de las credenciales.

	![Proporcione las credenciales del clúster](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.createcluster.3.png "Proporcione las credenciales del clúster")

    > [AZURE.NOTE] SSH sirve para tener acceso remoto a un clúster de HDInsight mediante una línea de comandos. El nombre de usuario y la contraseña que usa aquí se emplean al conectarse al clúster a través de SSH. Además, el nombre de usuario SSH tiene que ser único, ya que crea una cuenta de usuario en todos los nodos del clúster de HDInsight. A continuación se indican algunos de los nombres de cuenta reservados para su uso en los servicios que se ejecutan en el clúster y no pueden usarse como nombre de usuario SSH:
    >
    > root, hdiuser, storm, hbase, ubuntu, zookeeper, hdfs, yarn, mapred, hbase, hive, oozie, falcon, sqoop, admin, tez, hcat, hdinsight-zookeeper.

	Para obtener más información sobre el uso de SSH con HDInsight, vea uno de los siguientes artículos:

	* [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Linux, Unix u OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
	* [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Windows](hdinsight-hadoop-linux-use-ssh-windows.md)


7. Haga clic en **Origen de datos** para elegir un origen de datos existente para el clúster o crear uno nuevo. Cuando crea un clúster de Hadoop en HDInsight, especifica una cuenta de Almacenamiento de Azure. Se designa un contenedor de almacenamiento de blobs desde esa cuenta como el sistema de archivos predeterminado, como en el sistema de archivos distribuido de Hadoop (HDFS). De forma predeterminada, el clúster de HDInsight se crea en el mismo centro de datos que la cuenta de almacenamiento que especifica. Para obtener más información, vea [Uso del almacenamiento de blobs de Azure con HDInsight][hdinsight-storage]

	![Hoja Origen de datos](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.createcluster.4.png "Proporcione la configuración del origen de datos")

	Actualmente puede seleccionar una cuenta de almacenamiento de Azure como origen de datos para un clúster de HDInsight. Use lo siguiente para comprender las entradas de la hoja **Origen de datos**.

	- **Método de selección**: establézcalo en **De todas las suscripciones** para habilitar la exploración de cuentas de almacenamiento en todas sus suscripciones. Establézcalo en **Clave de acceso** si desea especificar el **Nombre de almacenamiento** y la **Clave de acceso** de una cuenta de almacenamiento existente.

	- **Seleccionar cuenta de almacenamiento o Crear nueva**: haga clic en **Seleccionar cuenta de almacenamiento** para buscar y seleccionar una cuenta de almacenamiento existente que desee asociar con el clúster. O bien, haga clic en **Crear nueva** para crear una nueva cuenta de almacenamiento. Use el campo que aparece para especificar el nombre de la cuenta de almacenamiento. Si el nombre está disponible, aparece una marca de verificación verde.

	- **Elegir contenedor predeterminado**: use esta opción para escribir el nombre del contenedor predeterminado que se usará para el clúster. Aunque se puede escribir cualquier nombre aquí, se recomienda usar el mismo nombre que el del clúster para que pueda reconocer fácilmente que el contenedor se usa para este clúster concreto.

	- **Ubicación**: región geográfica en la que se encuentra la cuenta de almacenamiento o donde se creará.

		> [AZURE.IMPORTANT] Al seleccionar la ubicación del origen de datos predeterminado también establece la ubicación del clúster de HDInsight. El origen de datos predeterminado y el clúster deben encontrarse en la misma región.

	Haga clic en **Seleccionar** para guardar la configuración del origen de datos.

8. Haga clic en **Planes de tarifa de nodo** para mostrar información sobre los nodos que se creen para este clúster. Establezca el número de nodos de trabajo que necesita para el clúster. El costo estimado del clúster se mostrará en la hoja.

	![Hoja Niveles de precios de nodo](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.createcluster.5.png "Especifique el número de nodos de clúster")

	Haga clic en **Seleccionar** para guardar la configuración de los precios del nodo.

9. En la hoja **Nuevo clúster de HDInsight**, asegúrese de que la opción **Anclar a Panel de inicio** está seleccionada y haga clic en **Crear**. Esto crea el clúster y agrega un icono para él en el panel de inicio de su Portal de Azure. El icono indicará que el clúster se está creando y cambiará para mostrar el icono de HDInsight cuando se haya completado el proceso.

	| Al crear | Creación completa |
	| ------------------ | --------------------- |
	| ![Indicador de creación en el Panel de inicio](./media/hdinsight-apache-spark-jupyter-spark-sql/provisioning.png) | ![Icono de clúster aprovisionado](./media/hdinsight-apache-spark-jupyter-spark-sql/provisioned.png) |

	> [AZURE.NOTE] El clúster tardará algo de tiempo en crearse, normalmente unos 15 minutos. Use el icono del Panel de inicio o la entrada **Notificaciones** de la izquierda de la página para comprobar el proceso de creación.

10. Una vez que termine la creación, haga clic en el icono del clúster Spark desde el panel de inicio para iniciar la hoja del clúster.

## <a name="jupyter"></a>Ejecución de consultas Spark SQL mediante un cuaderno de Jupyter

En esta sección, utilice un cuaderno de Jupyter para ejecutar consultas Spark SQL en un clúster Spark. De forma predeterminada el cuaderno de Jupyter incorpora un kernel **Python2**. Los clústeres de HDInsight Spark proporcionan dos kernels adicionales que puede utilizar con el cuaderno de Jupyter. Dichos componentes son:

* **PySpark** (para aplicaciones escritas con Python)
* **Spark** (para aplicaciones escritas con Scala)

En este artículo, usará el kernel de PySpark. En el artículo [Kernels disponibles para cuadernos de Jupyter con clústeres Spark en HDInsight (Linux)](hdinsight-apache-spark-jupyter-notebook-kernels.md#why-should-i-use-the-new-kernels) encontrará información detallada sobre las ventajas de usar el kernel de PySpark. Sin embargo, algunas de las ventajas principales de usar el kernel de PySpark son:

* No es necesario establecer los contextos de Spark, SQL ni Hive. Se establecen automáticamente para usted.
* Puede usar instrucciones mágicas de celda diferentes (como %%sql o %%hive) para ejecutar directamente las consultas de SQL o Hive, sin los fragmentos de código anteriores.
* Se visualiza automáticamente el resultado de las consultas de SQL o Hive.

### Creación de un cuaderno de Jupyter con el kernel de PySpark 

1. Desde el [Portal de vista previa de Azure](https://portal.azure.com/), en el panel de inicio, haga clic en el icono del clúster Spark (si lo ha fijado en el panel de inicio). También puede navegar hasta el clúster en **Examinar todo** > **Clústeres de HDInsight**.   

2. En la hoja del clúster Spark, haga clic en **Vínculos rápidos** y, luego, en la hoja **Panel de clúster**, haga clic en **Jupyter Notebook**. Cuando se le pida, escriba las credenciales del clúster.

	> [AZURE.NOTE] También puede comunicarse con el equipo Jupyter Notebook en el clúster si abre la siguiente dirección URL en el explorador. Reemplace __CLUSTERNAME__ por el nombre del clúster:
	>
	> `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Cree un nuevo notebook. Haga clic en **Nuevo** y, luego, en **PySpark**.

	![Crear un nuevo cuaderno de Jupyter](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.note.jupyter.createnotebook.png "Crear un nuevo cuaderno de Jupyter")

3. Se crea y se abre un nuevo cuaderno con el nombre Untitled.pynb. Haga clic en el nombre del cuaderno en la parte superior y escriba un nombre descriptivo.

	![Proporcionar un nombre para el cuaderno](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.note.jupyter.notebook.name.png "Proporcionar un nombre para el cuaderno")

4. Dado que creó un cuaderno con el kernel PySpark, no necesitará crear ningún contexto explícitamente. Los contextos Spark, SQL y Hive se crearán automáticamente al ejecutar la primera celda de código. Puede empezar por importar los tipos necesarios para este escenario. Para ello, pegue el siguiente fragmento de código en una celda y presione **MAYÚS + ENTRAR**.

		from pyspark.sql.types import *
		
	Cada vez que se ejecuta un trabajo en Jupyter, el título de la ventana del explorador web mostrará el estado **(Busy)** (Ocupado) junto con el título del cuaderno. También verá un círculo sólido junto al texto **PySpark** en la esquina superior derecha. Una vez completado el trabajo, cambiará a un círculo hueco.

	 ![Estado de un trabajo de cuaderno de Jupyter](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.jupyter.job.status.png "Estado de un trabajo de cuaderno de Jupyter")

4. Cargue los datos de ejemplo en una tabla temporal. Cuando crea un clúster Spark en HDInsight, el archivo de datos de ejemplo, **hvac.csv**, se copia en la cuenta de almacenamiento asociada en **\\HdiSamples\\HdiSamples\\SensorSampleData\\hvac**.

	En una celda vacía, pegue el siguiente ejemplo de código y presione **MAYÚS + ENTRAR**. Este ejemplo de código registra los datos en una tabla temporal llamada **hvac**.

		# Load the data
		hvacText = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
		
		# Create the schema
		hvacSchema = StructType([StructField("date", StringType(), False),StructField("time", StringType(), False),StructField("targettemp", IntegerType(), False),StructField("actualtemp", IntegerType(), False),StructField("buildingID", StringType(), False)])
		
		# Parse the data in hvacText
		hvac = hvacText.map(lambda s: s.split(",")).filter(lambda s: s[0] != "Date").map(lambda s:(str(s[0]), str(s[1]), int(s[2]), int(s[3]), str(s[6]) ))
		
		# Create a data frame
		hvacdf = sqlContext.createDataFrame(hvac,hvacSchema)
		
		# Register the data fram as a table to run queries against
		hvacdf.registerTempTable("hvac")

5. Dado que está usando un kernel de PySpark, puede ejecutar directamente una consulta SQL en la tabla temporal **hvac** que acaba de crear con la instrucción mágica `%%sql`. Para obtener más información sobre la instrucción mágica `%%sql`, así como otras instrucciones mágicas disponibles con el kernel de PySpark, vea [Kernels disponibles para cuadernos de Jupyter con clústeres Spark en HDInsight (Linux)](hdinsight-apache-spark-jupyter-notebook-kernels.md#why-should-i-use-the-new-kernels).
		
		%%sql
		SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = "6/1/13"")

5. Una vez que el trabajo se completa correctamente, se muestra de forma predeterminada el resultado tabular siguiente.

 	![Salida de tabla del resultado de la consulta](./media/hdinsight-apache-spark-jupyter-spark-sql/tabular.output.png "Salida de tabla del resultado de la consulta")

	También puede ver la salida en otras visualizaciones. Por ejemplo, un gráfico de área con la misma salida tendría el siguiente aspecto.

	![Gráfico de área del resultado de la consulta](./media/hdinsight-apache-spark-jupyter-spark-sql/area.output.png "Gráfico de área del resultado de la consulta")


6. Cuando haya terminado de ejecutar la aplicación, debe apagar el cuaderno para liberar los recursos. Para ello, en el menú **Archivo** del cuaderno, haga clic en **Cerrar y detener**. De esta manera se apagará y se cerrará el cuaderno.

##Eliminación del clúster

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


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

* [Kernels available for Jupyter notebook in Spark cluster for HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)

### Administración de recursos

* [Administración de recursos para el clúster Apache Spark en HDInsight de Azure](hdinsight-apache-spark-resource-manager.md)

### Problemas conocidos

* [Problemas conocidos de Apache Spark en HDInsight de Azure (Linux)](hdinsight-apache-spark-known-issues.md)


[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md

<!---HONumber=AcomDC_0309_2016-->