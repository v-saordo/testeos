<properties 
	pageTitle="Uso de herramientas de BI con Apache Spark en HDInsight | Microsoft Azure" 
	description="Instrucciones paso a paso para usar cuadernos con Apache Spark y crear esquemas de datos sin procesar, guardarlos como tablas de Hive y después usar herramientas de BI en la tabla de Hive para el análisis de datos" 
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


# Uso de herramientas de BI con Apache Spark en HDInsight de Azure (Linux)

Obtenga información sobre cómo usar Apache Spark en HDInsight de Azure para hacer lo siguiente:

* Tomar datos de ejemplo sin procesar y guardarlos como tabla de Hive;
* Usar herramientas de BI como Power BI y Tableau para analizar y visualizar los datos.

> [AZURE.TIP] Este tutorial también está disponible como un cuaderno de Jupyter en un clúster Spark (Linux) que se crea en HDInsight. La experiencia del cuaderno le permite ejecutar los fragmentos de código de Python desde el propio cuaderno. Para realizar el tutorial desde un cuaderno, cree un clúster Spark, inicie un cuaderno de Jupyter (`https://CLUSTERNAME.azurehdinsight.net/jupyter`) y luego ejecute el cuaderno **Uso de herramientas de BI con Apache Spark en HDInsight.ipynb** en la carpeta **Python**.

**Requisitos previos:**

Debe tener lo siguiente:

- Una suscripción de Azure. Consulte [How to get Azure Free trial for testing Hadoop in HDInsight (Obtención de una versión de prueba gratuita de Azure para probar Hadoop en HDInsight)](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Un clúster Apache Spark en HDInsight Linux. Para obtener instrucciones, vea [Creación de clústeres Apache Spark en HDInsight de Azure](hdinsight-apache-spark-jupyter-spark-sql.md).
- Un equipo con el controlador ODBC de Microsoft Spark instalado (necesario para que Spark en HDInsight trabaje con Tableau). Puede instalar el controlador desde [aquí](http://go.microsoft.com/fwlink/?LinkId=616229).
- Herramientas de BI como [Power BI](http://www.powerbi.com/) o [Tableau Desktop](http://www.tableau.com/products/desktop). Puede obtener una suscripción de vista previa gratuita de Power BI en [http://www.powerbi.com/](http://www.powerbi.com/).

##<a name="hivetable"></a>Guardado de los datos sin procesar como tabla de Hive

En esta sección, usamos el cuaderno de [Jupyter](https://jupyter.org) asociado con un clúster Apache Spark en HDInsight para ejecutar trabajos que procesan los datos de ejemplo sin procesar y los guardan como una tabla de Hive. Los datos de ejemplo corresponden a un archivo .csv (hvac.csv) que está disponible en todos los clústeres de manera predeterminada.

Una vez que los datos se guardan como tabla de Hive, en la sección siguiente, nos conectaremos a la tabla de Hive mediante herramientas de BI como Power BI y Tableau.

1. Desde el [Portal de vista previa de Azure](https://portal.azure.com/), en el panel de inicio, haga clic en el icono del clúster Spark (si lo ha anclado al panel de inicio). También puede navegar hasta el clúster en **Examinar todo** > **Clústeres de HDInsight**.   

2. En la hoja del clúster Spark, haga clic en **Vínculos rápidos** y, luego, en la hoja **Panel de clúster**, haga clic en **Jupyter Notebook**. Cuando se le pida, escriba las credenciales del clúster.

	> [AZURE.NOTE] También puede comunicarse con el equipo Jupyter Notebook en el clúster si abre la siguiente dirección URL en el explorador. Reemplace __CLUSTERNAME__ por el nombre del clúster:
	>
	> `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Cree un nuevo notebook. Haga clic en **Nuevo** y, luego, en **PySpark**.

	![Crear un nuevo cuaderno de Jupyter](./media/hdinsight-apache-spark-use-bi-tools/hdispark.note.jupyter.createnotebook.png "Crear un nuevo cuaderno de Jupyter")

3. Se crea y se abre un nuevo cuaderno con el nombre Untitled.pynb. Haga clic en el nombre del cuaderno en la parte superior y escriba un nombre descriptivo.

	![Proporcionar un nombre para el cuaderno](./media/hdinsight-apache-spark-use-bi-tools/hdispark.note.jupyter.notebook.name.png "Proporcionar un nombre para el cuaderno")

4. Dado que creó un cuaderno con el kernel PySpark, no necesitará crear ningún contexto explícitamente. Los contextos Spark, SQL y Hive se crearán automáticamente al ejecutar la primera celda de código. Puede empezar por importar los tipos necesarios para este escenario. Para ello, coloque el cursor en la celda y presione **MAYÚS + ENTRAR**.

		from pyspark.sql import *
		
	
5. Cargue los datos de ejemplo en una tabla temporal. Cuando crea un clúster Spark en HDInsight, el archivo de datos de ejemplo, **hvac.csv**, se copia en la cuenta de almacenamiento asociada en **\\HdiSamples\\HdiSamples\\SensorSampleData\\hvac**.

	En una celda vacía, pegue el siguiente fragmento de código y presione **MAYÚS + ENTRAR**. Este fragmento de código registra los datos en una tabla de Hive llamada **hvac**.


		# Create an RDD from sample data
		hvacText = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
		
		# Create a schema for our data
		Entry = Row('Date', 'Time', 'TargetTemp', 'ActualTemp', 'BuildingID')
		
		# Parse the data and create a schema
		hvacParts = hvacText.map(lambda s: s.split(',')).filter(lambda s: s[0] != 'Date')
		hvac = hvacParts.map(lambda p: Entry(str(p[0]), str(p[1]), int(p[2]), int(p[3]), int(p[6])))
		
		# Infer the schema and create a table       
		hvacTable = hiveCtx.createDataFrame(hvac)
		hvacTable.registerTempTable('hvactemptable')
		dfw = DataFrameWriter(hvacTable)
		dfw.saveAsTable('hvac')

5. Compruebe que la tabla se creó correctamente. Puede usar la instrucción mágica `%%hive` para ejecutar las consultas de Hive directamente. Para obtener más información sobre la instrucción mágica `%%hive`, así como otras instrucciones mágicas disponibles con el kernel de PySpark, vea [Kernels disponibles en los cuadernos de Jupyter con clústeres de HDInsight Spark](hdinsight-apache-spark-jupyter-notebook-kernels.md#why-should-i-use-the-new-kernels).

		%%hive
		SHOW TABLES

	Debería ver algo parecido a lo siguiente:

		+---------------+-----------+
		|      tableName|isTemporary|
		+---------------+-----------+
		|  hvactemptable|       true|
		|hivesampletable|      false|
		|           hvac|      false|
		+---------------+-----------+


	Las tablas que muestran false en la columna **isTemporary** son las únicas que son tablas de Hive que se almacenarán en la tienda de metadatos y a las que se puede acceder desde las herramientas de BI. En este tutorial, nos conectaremos a la tabla **hvac** que acabamos de crear.

6. Compruebe que la tabla contenga los datos previstos. En una celda vacía del cuaderno, copie el siguiente fragmento de código y presione **MAYÚS + ENTRAR**.

		%%hive
		SELECT * FROM hvac LIMIT 10
	
7. Ahora puede cerrar el cuaderno para liberar recursos. Para ello, en el menú **Archivo** del cuaderno, haga clic en **Cerrar y detener**. De esta manera se apagará y se cerrará el cuaderno.

##<a name="powerbi"></a>Uso de Power BI para analizar datos en la tabla de Hive

Una vez que haya guardado los datos como tabla de Hive, puede usar Power BI para conectarse a los datos y visualizarlos para crear informes, paneles, etc.

1. Inicie sesión en [Power BI](http://www.powerbi.com/).

2. En la pantalla de bienvenida, haga clic en **Databases & More** (Bases de datos y más).

	![Obtener datos en Power BI](./media/hdinsight-apache-spark-use-bi-tools/hdispark.powerbi.get.data.png "Obtener datos en Power BI")

3. En la siguiente pantalla, haga clic en **Spark en HDInsight de Azure** y luego en **Conectar**. Cuando se le pida, escriba la dirección URL del clúster (`mysparkcluster.azurehdinsight.net`) y las credenciales para conectarse al clúster.

	Una vez establecida la conexión, Power BI inicia la importación de datos desde el clúster Spark hacia HDInsight.

5. Power BI importa los datos y agrega un nuevo conjunto de datos de Spark en el encabezado **Conjuntos de datos**. Haga clic en el conjunto de datos para abrir una nueva hoja de cálculo para visualizar los datos. También puede guardar la hoja de cálculo como un informe. Para guardar una hoja de cálculo, en el menú **Archivo**, haga clic en **Guardar**.

	![Icono Spark en el panel de Power BI](./media/hdinsight-apache-spark-use-bi-tools/hdispark.powerbi.tile.png "Icono Spark en el panel de Power BI")

6. Tenga en cuenta que la lista **Fields** (Campos) a la derecha incluye la tabla **hvac** que creó antes. Expanda la tabla para ver sus campos, como se definieron antes en el cuaderno.

	  ![Lista de tablas de Hive](./media/hdinsight-apache-spark-use-bi-tools/hdispark.powerbi.display.tables.png "Lista de tablas de Hive")

7. Cree una visualización para mostrar la variación entre la temperatura objetivo y la real para cada edificio. Seleccione **Area Map** (Gráfico de área), mostrado en rojo, para visualizar los datos. Para definir el eje, arrastre y coloque el campo **BuildingID** en **Axis** (Eje) y los campos **ActualTemp**/**TargetTemp** en **Value** (Valor).

	![Crear visualizaciones](./media/hdinsight-apache-spark-use-bi-tools/hdispark.powerbi.visual1.png "Crear visualizaciones")


8. De manera predeterminada, la visualización muestra la suma de **ActualTemp** y **TargetTemp**. Para ambos campos, en la lista desplegable, seleccione **Average** (Media) para obtener un promedio de las temperaturas objetivo y reales de ambos edificios.

	![Crear visualizaciones](./media/hdinsight-apache-spark-use-bi-tools/hdispark.powerbi.visual2.png)
    
9. La visualización de datos debería parecerse a la siguiente. Mueva el cursor sobre la visualización para obtener información sobre herramientas con datos relevantes.

	![Crear visualizaciones](./media/hdinsight-apache-spark-use-bi-tools/hdispark.powerbi.visual3.png)

10. Haga clic en **Save** (Guardar) en el menú superior y proporcione un nombre para el informe. También puede anclar la visualización. Cuando se ancla una visualización, se almacenará en el panel para que pueda seguir el valor más reciente de un vistazo.

	Puede agregar tantas visualizaciones como quiera para el mismo conjunto de datos y anclarlas al panel para ver una instantánea de los datos. Además, los clústeres Spark en HDInsight están conectados a Power BI con conexión directa. Esto significa que Power BI siempre tiene la copia más actualizada del clúster, por lo que no es necesario programar actualizaciones del conjunto de datos.

##<a name="tableau"></a>Uso de Tableau Desktop para analizar los datos de la tabla de Hive
	
1. Inicie Tableau Desktop. En el panel izquierdo, en la lista del servidor al que va a conectarse, haga clic en **Spark SQL**. Si Spark SQL no aparece de forma predeterminada en el panel izquierdo, puede hacer clic en **More Servers** (Más servidores) para buscarlo. 

2. En el cuadro de diálogo de conexión de SQL Spark, proporcione los valores como se muestra a continuación y luego haga clic en **OK** (Aceptar).

	![Conectarse a un clúster Spark](./media/hdinsight-apache-spark-use-bi-tools/hdispark.tableau.connect.png "Conectarse a un clúster Spark")

	La lista desplegable de autenticación solo muestra **Servicio HDInsight de Microsoft Azure** como opción si ha instalado el [controlador ODBC de Microsoft Spark](http://go.microsoft.com/fwlink/?LinkId=616229) en el equipo.

3. En la siguiente pantalla, en la lista desplegable **Schema** (Esquema), haga clic en el icono **Find** (Buscar) y luego en **default** (predeterminado).

	![Buscar esquema](./media/hdinsight-apache-spark-use-bi-tools/hdispark.tableau.find.schema.png "Buscar esquema")

4. Para el campo **Table** (Tabla), vuelva a hacer clic en el icono **Find** (Buscar) para ver todas las tablas de Hive disponibles en el clúster. Debería ver la tabla **hvac** que creó antes con el cuaderno.

	![Buscar tablas](./media/hdinsight-apache-spark-use-bi-tools/hdispark.tableau.find.table.png "Buscar tablas")

5. Arrastre y coloque la tabla en el cuadro superior a la derecha. Tableau importa los datos y muestra el esquema como se resalta con el cuadro rojo.

	![Agregar tablas a Tableau](./media/hdinsight-apache-spark-use-bi-tools/hdispark.tableau.drag.table.png "Agregar tablas a Tableau")

6. Haga clic en la pestaña **Sheet1** (Hoja1) en la parte inferior izquierda. Realice una visualización que muestre el promedio de temperaturas objetivo y reales de todos los edificios para cada fecha. Arrastre **Date** y **Building ID** a **Columns** (Columnas) y **Actual Temp**/**Target Temp** a **Rows** (Filas). En **Marks** (Marcas), seleccione **Area** (Área) para usar una visualización del gráfico de área.

	 ![Agregar campos para la visualización](./media/hdinsight-apache-spark-use-bi-tools/hdispark.tableau.drag.fields.png "Agregar campos para la visualización")

7. De manera predeterminada, se muestran los campos de temperatura como agregado. Si desea mostrar el promedio de las temperaturas en su lugar, puede hacerlo en la lista desplegable, como se muestra a continuación.

	![Hacer el promedio de temperaturas](./media/hdinsight-apache-spark-use-bi-tools/hdispark.tableau.temp.avg.png "Hacer el promedio de temperaturas")

8. También puede superponer un mapa de temperatura al otro para hacerse una idea más clara de la diferencia entre las temperaturas reales y objetivo. Mueva el mouse a la esquina del gráfico de área inferior hasta que vea la forma del controlador resaltada en un círculo rojo. Arrastre el gráfico al otro gráfico de encima y suelte el mouse cuando vea la forma resaltada en el rectángulo rojo.

	![Combinar mapas](./media/hdinsight-apache-spark-use-bi-tools/hdispark.tableau.merge.png "Combinar mapas")

	 La visualización de datos debería cambiar a la siguiente:

	![Visualización](./media/hdinsight-apache-spark-use-bi-tools/hdispark.tableau.final.visual.png "Visualización")
	 
9. Haga clic en **Save** (Guardar) para guardar la hoja de cálculo. Puede crear paneles y agregarles una o varias hojas.

##<a name="seealso"></a>Otras referencias

* [Introducción a Apache Spark en HDInsight de Azure](hdinsight-apache-spark-overview.md)

### Escenarios

* [Spark con Aprendizaje automático: uso de Spark en HDInsight para analizar la temperatura de edificios con los de datos del sistema de acondicionamiento de aire](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

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


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md

<!---HONumber=AcomDC_0224_2016-->