<properties
	pageTitle="Envío de consultas de Hive a clústeres de Hadoop en la tecnología y procesos de análisis avanzado | Microsoft Azure"
	description="Procese datos de las tablas de subárbol con consultas de subárbol."
	services="machine-learning"
	documentationCenter=""
	authors="hangzh-msft"
	manager="paulettm" 
	editor="cgronlun"  />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/08/2016"
	ms.author="hangzh;bradsev" />

#<a name="heading"></a> Envío de consultas de Hive a clústeres de Hadoop de HDInsights en la tecnología y procesos de análisis avanzado 

En este documento se describen distintas formas de enviar consultas de subárbol a los clústeres de Hadoop administrados por un servicio HDInsight de Azure. Esta tarea forma parte del proceso de análisis de Cortana (CAP). Se tratan varias tareas de controversia de datos: generación de características y exploración de datos. Se presentan las consultas de Hive genéricas que muestran cómo explorar datos o generar características mediante Hive en un clúster de Hadoop de HDInsight de Azure. Estas consultas de subárbol usan las funciones definidas por el usuario (UDF de subárbol) que se proporcionan.

También se ofrecen ejemplos de consultas que son específicos de escenarios de [NYC Taxi Trip Data](http://chriswhong.com/open-data/foil_nyc_taxi/) en el [repositorio de Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts). Estas consultas ya tienen el esquema de datos especificado y están listas para enviarse para su ejecución.

En la última sección, se describen los parámetros que pueden usarse para ajustar y mejorar el rendimiento de las consultas de Hive.

## Requisitos previos
En este artículo se supone que ha:

* Creado una cuenta de almacenamiento de Azure. Si necesita instrucciones, consulte [Creación de una cuenta de almacenamiento de Azure](../hdinsight-get-started.md#storage).
* Aprovisionado un clúster de Hadoop personalizado con el servicio HDInsight. Si necesita instrucciones, consulte [Personalización de clústeres de Hadoop de HDInsight de Azure para análisis avanzado](machine-learning-data-science-customize-hadoop-cluster.md).
* Se han cargado los datos en tablas de subárbol en clústeres de Hadoop de HDInsight de Azure. De no ser así, siga [Crear y cargar datos en tablas de Hive](machine-learning-data-science-move-hive-tables.md) para cargar los datos en tablas de Hive primero.
* Habilitado el acceso remoto al clúster. Si necesita instrucciones, consulte [Acceso al nodo principal del clúster de Hadoop](machine-learning-data-science-customize-hadoop-cluster.md#headnode).


## <a name="submit"></a>Cómo enviar consultas de Hive
Las consultas de Hive se pueden enviar mediante el uso de las siguientes aplicaciones:

* **Consola de la línea de comandos de Hadoop** en el nodo principal del clúster
* **Bloc de notas de IPython**
* **Editor de Hive**
* Scripts de **PowerShell**

Las consultas de Hive son similares a SQL. A los usuarios familiarizados con SQL pueden encontrar la <a href="http://hortonworks.com/wp-content/uploads/downloads/2013/08/Hortonworks.CheatSheet.SQLtoHive.pdf" target="_blank">Hoja de referencia rápida de SQL a Hive</a>.

Al enviar una consulta de subárbol, también puede controlar el destino del resultado de las consultas de subárbol, ya sea en la pantalla o en un archivo local del nodo principal o en un blob de Azure.

### A través de la consola de la línea de comandos de Hadoop en el nodo principal del clúster de Hadoop

Si la consulta es compleja, enviar consultas de subárbol directamente desde el nodo principal del clúster de subárbol de Hadoop normalmente lleva a un procesamiento más rápido que su envío con un editor de subárbol o mediante scripts de PowerShell de Azure.

Iniciar sesión en el nodo principal del clúster de Hadoop, abrir la línea de comandos de Hadoop en el escritorio del nodo principal y escribir el comando

    cd %hive_home%\bin

Los usuarios disponen de tres maneras de enviar consultas de subárbol en la consola de la línea de comandos de Hadoop:

* directamente desde la línea de comandos de Hadoop
* usando archivos .hql
* desde la consola de comandos de subárbol

#### Enviar consultas de subárbol directamente desde la línea de comandos de Hadoop

Los usuarios pueden ejecutar comandos como

	hive -e "<your hive query>;

para enviar consultas de subárbol sencillas directamente en la línea de comandos de Hadoop. Este es un ejemplo, donde el cuadro rojo muestra el comando que envía la consulta de subárbol y el cuadro verde muestra el resultado de la consulta de subárbol.

![Creación del espacio de trabajo](./media/machine-learning-data-science-process-hive-tables/run-hive-queries-1.png)

#### Enviar consultas de subárbol en archivos .hql

Cuando la consulta de subárbol es más complicada y tiene varias líneas, no resulta práctico modificar consultas en la línea de comandos de Hadoop o la consola de comandos de subárbol. Una alternativa es usar un editor de texto en el nodo principal del clúster de Hadoop y guardar las consultas de subárbol en un archivo .hql de un directorio local del nodo principal. A continuación, puede enviarse la consulta de Hive del archivo .hql mediante el argumento `-f` en el comando `hive` de la siguiente manera:

	`hive -f "<path to the .hql file>"`


#### Suprimir la impresión de pantalla del estado de progreso de las consultas de subárbol

De forma predeterminada, una vez que se envía la consulta de subárbol de la consola de la línea de comandos de Hadoop, el progreso del trabajo de asignación/reducción se imprimirá en pantalla. Para suprimir la impresión de la pantalla de progreso del trabajo de asignación/reducción, puede utilizar el argumento `-S` (distingue entre mayúsculas y minúsculas) en la línea de comandos de la siguiente manera:

	hive -S -f "<path to the .hql file>"
	hive -S -e "<Hive queries>"

#### Envíe consultas de subárbol en la consola de comandos de subárbol.

Los usuarios pueden especificar también la consola de comandos de Hive ejecutando el comando `hive` desde la línea de comandos de Hadoop y después enviar consultas de Hive desde esta consola en el símbolo del sistema **hive>**. Aquí tiene un ejemplo.

![Creación del espacio de trabajo](./media/machine-learning-data-science-process-hive-tables/run-hive-queries-2.png)

En este ejemplo, los dos cuadros de color rojo resaltan los comandos que se utilizan para escribir en la consola de comandos de subárbol y la consulta de subárbol enviada en la consola de comandos de subárbol, respectivamente. El cuadro verde resalta el resultado de la consulta de subárbol.

Los ejemplos anteriores generan directamente los resultados de la consulta de subárbol en pantalla. Los usuarios también pueden escribir la salida en un archivo local del nodo principal o en un blob de Azure. A continuación, los usuarios pueden utilizar otras herramientas para analizar más el resultado de las consultas de subárbol.

#### Genere los resultados de consulta de subárbol en un archivo local.

Para generar los resultados de consultas de subárbol en un directorio local del nodo principal, los usuarios tienen que enviar la consulta de subárbol de la línea de comandos de Hadoop de la siguiente manera:

	`hive -e "<hive query>" > <local path in the head node>`


#### Generar los resultados de consulta de subárbol en un blob de Azure

Los usuarios también pueden generar resultados de consulta de subárbol en un blob de Azure, dentro del contenedor predeterminado del clúster de Hadoop. La consulta de subárbol para hacerlo tiene el siguiente aspecto:

	insert overwrite directory wasb:///<directory within the default container> <select clause from ...>

En el ejemplo siguiente, el resultado de la consulta de Hive se escribe en un directorio blob `queryoutputdir` dentro del contenedor predeterminado del clúster de Hadoop. Aquí solo debe proporcionar el nombre del directorio, sin el nombre del blob. Se producirá un error si proporciona los nombres de directorio y de blob, como *wasb:///queryoutputdir/queryoutput.txt*.

![Creación del espacio de trabajo](./media/machine-learning-data-science-process-hive-tables/output-hive-results-2.png)

El resultado de la consulta de subárbol se puede ver en el almacenamiento de blobs abriendo el contenedor predeterminado del clúster de Hadoop mediante la herramienta Explorador de almacenamiento de Azure (o equivalente). Puede aplicar el filtro (resaltado con un cuadro rojo) si desea recuperar un blob con letras especificadas en los nombres.

![Creación del espacio de trabajo](./media/machine-learning-data-science-process-hive-tables/output-hive-results-3.png)

### A través del Editor de subárboles o comandos de PowerShell de Azure

Los usuarios también pueden usar la consola de consultas (editor de subárbol) escribiendo la dirección URL con el formato

*https://&#60;Hadoop nombreDeClúster>.azurehdinsight.net/Home/HiveEditor*

en un explorador web. Tenga en cuenta que deberá indicar las credenciales de clúster de Hadoop para iniciar sesión. Además, puede [enviar trabajos de Hive mediante PowerShell](../hdinsight/hdinsight-submit-hadoop-jobs-programmatically.md#hive-powershell).


##<a name="hive-dataexploration"></a>Exploración de datos
A continuación presentamos algunos scripts de subárbol que se pueden usar para explorar los datos de las tablas de subárbol.

1. Obtención del número de observaciones por partición `SELECT <partitionfieldname>, count(*) from <databasename>.<tablename> group by <partitionfieldname>;`

2. Obtener el número de observaciones por día `SELECT to_date(<date_columnname>), count(*) from <databasename>.<tablename> group by to_date(<date_columnname>);`

3. Obtención de los niveles de una columna de categorías `SELECT  distinct <column_name> from <databasename>.<tablename>`

4. Obtención del número de niveles de combinación de dos columnas de categorías `SELECT <column_a>, <column_b>, count(*) from <databasename>.<tablename> group by <column_a>, <column_b>`

5. Obtención de la distribución para columnas numéricas `SELECT <column_name>, count(*) from <databasename>.<tablename> group by <column_name>`

6. Extraer registros de la combinación de dos tablas

	    SELECT
			a.<common_columnname1> as <new_name1>,
			a.<common_columnname2> as <new_name2>,
    		a.<a_column_name1> as <new_name3>,
    		a.<a_column_name2> as <new_name4>,
    		b.<b_column_name1> as <new_name5>,
    		b.<b_column_name2> as <new_name6>
    	FROM
    		(
    		SELECT <common_columnname1>,
    			<common_columnname2>,
				<a_column_name1>,
				<a_column_name2>,
			FROM <databasename>.<tablename1>
			) a
			join
			(
			SELECT <common_columnname1>,
    			<common_columnname2>,
				<b_column_name1>,
				<b_column_name2>,
			FROM <databasename>.<tablename2>
			) b
			ON a.<common_columnname1>=b.<common_columnname1> and a.<common_columnname2>=b.<common_columnname2>

##<a name="hive-featureengineering"></a>Generación de características

En esta sección se describen maneras de generar características mediante consultas de subárbol.

> [AZURE.NOTE] En las consultas de subárbol de esta sección se supone que los datos se han cargado en las tablas de subárbol de los clústeres Hadoop de HDInsight de Azure. De no ser así, siga el proceso para [crear y cargar datos en tablas de Hive](machine-learning-data-science-hive-tables.md) a fin de cargar datos en tablas de Hive en primer lugar.

Una vez que haya generado características adicionales, puede agregarlas como columnas a la tabla existente o crear una nueva tabla con las características adicionales y la clave principal, que se pueden combinar a continuación con la tabla original.

1. [Generación de características basada en frecuencia](#hive-frequencyfeature)
2. [Riesgos de las variables de categorías en la clasificación binaria](#hive-riskfeature)
3. [Extraer características de campos de fecha y hora](#hive-datefeatures)
4. [Extraer características del campo de texto](#hive-textfeatures)
5. [Calcular distancia entre las coordenadas GPS](#hive-gpsdistance)

###<a name="hive-frequencyfeature"></a>Generación de características basada en frecuencia

A menudo resulta útil calcular las frecuencias de los niveles de una variable de categoría o las frecuencias de determinadas combinaciones de niveles desde varias variables de categorías. Los usuarios pueden usar el siguiente script para calcular estas frecuencias:

		select
			a.<column_name1>, a.<column_name2>, a.sub_count/sum(a.sub_count) over () as frequency
		from
		(
			select
				<column_name1>,<column_name2>, count(*) as sub_count
			from <databasename>.<tablename> group by <column_name1>, <column_name2>
		)a
		order by frequency desc;


###<a name="hive-riskfeature"></a>Riesgos de las variables de categorías en la clasificación binaria

En la clasificación binaria, necesitamos convertir las variables de categorías no numéricas en características numéricas cuando los modelos que se utilizan solo toman características numéricas. Para ello, reemplace cada nivel no numérico por un riesgo numérico. En esta sección mostramos algunas consultas de subárbol genéricas que calculan los valores de riesgo (probabilidades de registro) de una variable de categoría.


	    set smooth_param1=1;
	    set smooth_param2=20;
	    select
	    	<column_name1>,<column_name2>,
			ln((sum_target+${hiveconf:smooth_param1})/(record_count-sum_target+${hiveconf:smooth_param2}-${hiveconf:smooth_param1})) as risk
	    from
	    	(
	    	select
	    		<column_nam1>, <column_name2>, sum(binary_target) as sum_target, sum(1) as record_count
	    	from
	    		(
	    		select
	    			<column_name1>, <column_name2>, if(target_column>0,1,0) as binary_target
	    		from <databasename>.<tablename>
	    		)a
	    	group by <column_name1>, <column_name2>
	    	)b

En este ejemplo, las variables `smooth_param1` y `smooth_param2` se establecen para suavizar los valores de riesgo calculados a partir de los datos. Los riesgos tienen un intervalo comprendido entre -Inf y Inf. Un riesgo > 0 indica que la probabilidad de que el destino sea igual a 1 es mayor que 0,5.

Después de calcularse la tabla de riesgos, los usuarios pueden asignar valores de riesgo a una tabla uniéndola a la tabla de riesgo. La consulta de combinación de subárbol se ha proporcionado en la sección anterior.

###<a name="hive-datefeatures"></a>Extraer características de campos de fecha y hora

El subárbol se incluye con un conjunto de UDF para el procesamiento de campos de fecha y hora. En el subárbol, el formato de fecha y hora predeterminado es 'aaaa-MM-dd 00:00:00 ' ('1970-01-01 12:21:32' por ejemplo). En esta sección mostramos ejemplos que extraen el día de un mes, el mes de un campo de fecha y hora, y otros ejemplos que convierten una cadena de fecha y hora en un formato distinto del predeterminado en una cadena de fecha y hora en el formato predeterminado.

    	select day(<datetime field>), month(<datetime field>)
		from <databasename>.<tablename>;

Esta consulta de Hive asume que *&#60;datetime field>* está en el formato de fecha y hora predeterminado.

Si un campo de fecha y hora no se encuentra en el formato predeterminado, deberá convertir el campo de fecha y hora en la marca de tiempo de Unix primero y, a continuación, convertir la marca de tiempo de Unix a una cadena de fecha y hora que se encuentra en el formato predeterminado. Cuando la fecha y hora se encuentra en el formato predeterminado, los usuarios pueden aplicar los UDF de fecha y hora incrustado para extraer características.

		select from_unixtime(unix_timestamp(<datetime field>,'<pattern of the datetime field>'))
		from <databasename>.<tablename>;

En esta consulta, si *&#60;datetime field>* sigue un patrón de tipo *03/26/2015 12:04:39*, *'&#60;pattern of the datetime field>'* debe ser `'MM/dd/yyyy HH:mm:ss'`. Para probarlo, los usuarios pueden ejecutar

		select from_unixtime(unix_timestamp('05/15/2015 09:32:10','MM/dd/yyyy HH:mm:ss'))
		from hivesampletable limit 1;

La tabla *hivesampletable* de esta consulta viene preinstalada en todos los clústeres de Hadoop de HDInsight de Azure de forma predeterminada cuando se aprovisionan los clústeres.


###<a name="hive-textfeatures"></a>Extracción de características de campos de texto

Cuando la tabla de subárbol tiene un campo de texto que contiene una cadena de palabras delimitadas por espacios, la consulta siguiente extrae la longitud de la cadena y el número de palabras de la cadena.

    	select length(<text field>) as str_len, size(split(<text field>,' ')) as word_num
		from <databasename>.<tablename>;

###<a name="hive-gpsdistance"></a>Cálculo de la distancia entre conjuntos de coordenadas de GPS

La consulta proporcionada en esta sección puede aplicarse directamente a los datos de carreras de taxi de Nueva York. El propósito de esta consulta es mostrar cómo aplicar una función matemática incrustada en el subárbol para generar características.

Los campos que se usan en esta consulta son las coordenadas GPS de ubicaciones de recogida y entrega, denominadas *pickup\_longitude*, *pickup\_latitude*, *dropoff\_longitude* y *dropoff\_latitude*. Las consultas que calculan la distancia directa entre las coordenadas de recogida y entrega son:

		set R=3959;
		set pi=radians(180);
		select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude,
			${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
			*${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
			*cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
			/sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
			+cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*
			pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance
		from nyctaxi.trip
		where pickup_longitude between -90 and 0
		and pickup_latitude between 30 and 90
		and dropoff_longitude between -90 and 0
		and dropoff_latitude between 30 and 90
		limit 10;

Las ecuaciones matemáticas que calculan la distancia entre dos coordenadas GPS pueden encontrarse en el sitio <a href="http://www.movable-type.co.uk/scripts/latlong.html" target="_blank">Movable Type Scripts</a> (Scripts de tipo movibles), creado por Peter Lapisu. En su Javascript, la función `toRad()` es simplemente *lat\_or\_lon*pi/180*, que convierte grados a radianes. Aquí, *lat\_or\_lon* es la latitud o la longitud. Debido a que Hive no proporciona la función `atan2`, pero sí la función `atan`, la función `atan2` se implementa en la función `atan` en la consulta de Hive anterior mediante la definición incluida en <a href="http://en.wikipedia.org/wiki/Atan2" target="_blank">Wikipedia</a>.

![test](./media/machine-learning-data-science-process-hive-tables/atan2new.png)

Se puede encontrar una lista completa de las UDF incrustadas de Hive en la sección **Funciones integradas** de la <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-MathematicalFunctions" target="_blank">wiki de Apache Hive</a>.

## <a name="tuning"></a> Temas avanzados: Ajustar parámetros de Hive para mejorar la velocidad de consulta

La configuración de parámetros predeterminados del clúster de subárbol podría no ser adecuada para las consultas de subárbol y los datos que estas consultas procesan. En esta sección se describen algunos parámetros que los usuarios pueden ajustar y que mejoran el rendimiento de las consultas de subárbol. Los usuarios necesitan agregar el parámetro que optimiza las consultas antes de las consultas de procesamiento de datos.

1. **Espacio de montón de Java**: para las consultas que implican la combinación de grandes conjuntos de datos, o el procesamiento de largos registros, un error habitual es **quedarse sin espacio en el montón**. Esto se puede ajustar estableciendo los parámetros *mapreduce.map.java.opts* y *mapreduce.task.io.sort.mb* en los valores deseados. Aquí tiene un ejemplo:

		set mapreduce.map.java.opts=-Xmx4096m;
		set mapreduce.task.io.sort.mb=-Xmx1024m;


	Este parámetro asigna 4 GB de memoria al espacio de montón de Java y también hace que la ordenación sea más eficiente al asignar más memoria para él. Es buena idea jugar con estas asignaciones si no hay ningún error de trabajo relacionado con el espacio en el montón.

2. **Tamaño de bloque de DFS**: este parámetro establece la unidad más pequeña de datos que el sistema de archivos almacena. Por ejemplo, si el tamaño de bloque DFS es 128 MB, a continuación, los datos de un tamaño menor que 128 MB, que también será el tamaño máximo, se almacenará en un solo bloque, mientras que a los datos mayores de 128 MB se les asignará bloques adicionales. Al elegir un tamaño de bloque muy pequeño se producirán grandes sobrecargas en Hadoop puesto que el nodo de nombre tiene que procesar muchas más solicitudes para buscar el bloque relevante relacionado con el archivo. Una configuración recomendada al tratar con datos de gigabytes (o mayores) es:

		set dfs.block.size=128m;

3. **Optimización de la operación de unión en Hive**: Aunque las operaciones de unión en el marco de asignación/reducción suelen tener lugar en la fase de reducción, en ocasiones se pueden obtener ganancias enormes mediante la programación de uniones en la fase de asignación (también denominada "mapjoins"). Para indicar al subárbol que haga esto siempre que sea posible, podemos establecer:

		set hive.auto.convert.join=true;

4. **Especificación del número de asignadores a Hive**: aunque Hadoop permite al usuario establecer el número de reductores, este no suele hacerlo. Un truco que permite cierto grado de control sobre este número es elegir las variables de Hadoop, *mapred.min.split.size* y *mapred.max.split.size*, puesto que el tamaño de cada tarea de asignación se determina mediante:

		num_maps = max(mapred.min.split.size, min(mapred.max.split.size, dfs.block.size))

	Normalmente, el valor predeterminado de *mapred.min.split.size* es 0, el de *mapred.max.split.size* es **Long.MAX** y el de *dfs.block.size* es 64 MB. Como podemos ver, dado el tamaño de los datos, el ajuste de estos parámetros mediante su "configuración" nos permite optimizar el número de asignadores que se usan.

5. A continuación se mencionan algunas otras **opciones avanzadas** más para optimizar el rendimiento de Hive. Estas permiten establecer la memoria asignada para asignar y reducir tareas, y pueden ser útiles para modificar el rendimiento. Tenga en cuenta que el valor de *mapreduce.reduce.memory.mb* no puede ser mayor que el tamaño de la memoria física de cada nodo de trabajo del clúster de Hadoop.

		set mapreduce.map.memory.mb = 2048;
		set mapreduce.reduce.memory.mb=6144;
		set mapreduce.reduce.java.opts=-Xmx8192m;
		set mapred.reduce.tasks=128;
		set mapred.tasktracker.reduce.tasks.maximum=128;



 

<!----HONumber=AcomDC_0211_2016-->