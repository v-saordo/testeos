<properties 
	pageTitle="Crear y cargar datos en tablas de Hive desde el almacenamiento de blobs de Azure | Microsoft Azure" 
	description="Crear tablas de subárbol y cargar datos de blob en tablas de subárbol" 
	services="machine-learning,storage" 
	documentationCenter="" 
	authors="hangzh-msft" 
	manager="jacob.spoelstra" 
	editor="cgronlun"  />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/08/2016" 
	ms.author="hangzh;bradsev" />

 
#Crear y cargar datos en tablas de subárbol desde el almacenamiento de blobs de Azure

## Introducción
En **este documento**, se presentan consultas genéricas de Hive que crean tablas de Hive y cargan datos desde el almacenamiento de blobs de Azure. También se ofrecen algunas instrucciones acerca de las particiones de tablas de subárbol y de cómo utilizar el formato ORC para mejorar el rendimiento de las consultas.

Este **menú** vincula a temas en los que se describe cómo introducir datos en los entornos de destino en que se pueden almacenar y procesar los datos durante el proceso de análisis de Cortana (CAPS).

[AZURE.INCLUDE [cap-ingest-data-selector](../../includes/cap-ingest-data-selector.md)]


## Requisitos previos
En este artículo se supone que ha:
 
* Creado una cuenta de almacenamiento de Azure. Si necesita instrucciones, consulte [Creación de una cuenta de almacenamiento de Azure](../hdinsight-get-started.md#storage). 
* Aprovisionado un clúster de Hadoop personalizado con el servicio HDInsight. Si necesita instrucciones, consulte [Personalización de clústeres de Hadoop de HDInsight de Azure para análisis avanzado](machine-learning-data-science-customize-hadoop-cluster.md).
* Habilitado el acceso remoto para el clúster, ha iniciado sesión y ha abierto la consola de la línea de comandos de Hadoop. Si necesita instrucciones, consulte [Acceso al nodo principal del clúster Hadoop](machine-learning-data-science-customize-hadoop-cluster.md#headnode). 

## Carga de datos en el almacenamiento de blobs de Azure
Si creó una máquina virtual de Azure siguiendo las instrucciones dadas en [Configuración de una máquina virtual de Azure para análisis avanzado](machine-learning-data-science-setup-virtual-machine.md), este archivo de script debió descargarse en el directorio *C:\\Users\\<nombre de usuario>\\Documents\\Data Science Scripts* de la máquina virtual. Estas consultas de subárbol solo requieren que conecte sus propios esquemas de datos y la configuración de almacenamiento de blobs de Azure en los campos adecuados para estar preparado para su envío.

Se supone que los datos de las tablas de Hive están en un formato tabular **sin comprimir** y que se han cargado los datos en el contenedor predeterminado (o en otro adicional) de la cuenta de almacenamiento que usa el clúster Hadoop.

Si desea practicar sobre _NYC Taxi Trip Data_, necesita descargar primero los 24 archivos <a href="http://www.andresmh.com/nyctaxitrips/" target="_blank">NYC Taxi Trip Data</a> (12 archivos de carreras y 12 de tarifas), **descomprimir** todos los archivos en archivos .csv y cargarlos en el contenedor predeterminado (o el contenedor adecuado) de la cuenta de almacenamiento de Azure que creó el procedimiento descrito en el tema [Personalización de clústeres Hadoop de HDInsight de Azure para tecnología y procesos de análisis avanzados](machine-learning-data-science-customize-hadoop-cluster.md). En esta [página](machine-learning-data-science-process-hive-walkthrough/#upload) encontrará el proceso de cargar los archivos .csv en el contenedor predeterminado de la cuenta de almacenamiento.

## <a name="submit"></a>Cómo enviar consultas de Hive
Las consultas de subárbol se pueden enviar mediante:

* la línea de comandos de Hadoop en el nodo principal del clúster
* el Bloc de notas de IPython
* el Editor de subárbol
* Scripts de PowerShell de Azure

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

en un explorador web. Tenga en cuenta que deberá indicar las credenciales de clúster de Hadoop para iniciar sesión.

Además, puede realizar la [Ejecución de consultas de Hive con PowerShell](../hdinsight/hdinsight-hadoop-use-hive-powershell.md).


## <a name="create-tables"></a> Creación de tablas y base de datos de Hive

Las consultas de Hive se comparten en el [repositorio de Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_db_tbls_load_data_generic.hql) y se pueden descargar desde allí.

Esta es la consulta de subárbol que crea una tabla de subárbol.

    create database if not exists <database name>;
	CREATE EXTERNAL TABLE if not exists <database name>.<table name>
	(
		field1 string, 
		field2 int, 
		field3 float, 
		field4 double, 
		...,
		fieldN string
	) 
	ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' lines terminated by '<line separator>' 
	STORED AS TEXTFILE LOCATION '<storage location>' TBLPROPERTIES("skip.header.line.count"="1");

Estas son las descripciones de los campos que los usuarios necesitan para conectar y otras configuraciones:

- **&#60;nombre de base de datos>**: nombre de la base de datos que los usuarios desean crear. Si los usuarios solo desean usar la base de datos predeterminada, se puede omitir la consulta *crear base de datos...* 
- **&#60;nombre de tabla>**: nombre de la tabla que los usuarios quieren crear en la base de datos especificada. Si los usuarios desean usar la base de datos predeterminada, puede hacer referencia directamente a la tabla *&#60;table name>* sin &#60;nombre de base de datos>.
- **&#60;separador de campos>**: separador que delimita los campos del archivo de datos que se cargará en la tabla de Hive. 
- **<separador de líneas>**: separador que delimita las líneas del archivo de datos. 
- **&#60;storage location>**: la ubicación de almacenamiento de Azure para guardar los datos de tablas de Hive. Si los usuarios no especifican *LOCATION &#60;ubicación de almacenamiento>*, la base de datos y las tablas se almacenan de forma predeterminada en el directorio *hive/warehouse/* del contenedor predeterminado del clúster Hive. Si un usuario desea especificar la ubicación de almacenamiento, la ubicación de almacenamiento debe estar dentro del contenedor predeterminado para la base de datos y las tablas. A esta ubicación tiene que hacerse referencia como ubicación relativa al contenedor predeterminado del clúster con el formato *'wasb:///&#60;directorio 1>/'* o *'wasb:///&#60;directorio 1>/&#60;directorio 2>/'*, etc. Después de ejecutar la consulta, se crearán los directorios relativos dentro del contenedor predeterminado. 
- **TBLPROPERTIES("skip.header.line.count"="1")**: si el archivo de datos tiene una línea de encabezado, los usuarios deben agregar esta propiedad **al final** de la consulta *create table*. De lo contrario, se cargará la línea de encabezado como registro en la tabla. Si el archivo de datos no tiene una línea de encabezado, se puede omitir esta configuración en la consulta. 

## <a name="load-data"></a>Carga de datos en tablas de Hive
Esta es la consulta de subárbol que carga datos en una tabla de subárbol.

    LOAD DATA INPATH '<path to blob data>' INTO TABLE <database name>.<table name>;

- **&#60;ruta de acceso a datos de blob>**: si el archivo de blob que se va a cargar en la tabla de Hive se encuentra en el contenedor predeterminado del clúster Hadoop de HDInsight, *&#60;ruta de acceso a datos de blob>* debe tener el formato *"wasb:///&#60;directorio de este contenedor>/&#60;nombre del archivo de blob>"*. El archivo blob también puede estar en un contenedor adicional del clúster de Hadoop de HDInsight. En este caso, *&#60;ruta de acceso a datos de blob>* debe tener el formato *"wasb://&#60;nombre del contenedor>@&#60;nombre de cuenta de almacenamiento>.blob.core.windows.net/&#60;nombre de archivo de blob>"*.

	>[AZURE.NOTE] Los datos blob que se van a cargar en la tabla de subárbol tienen que estar en el contenedor adicional o predeterminado de la cuenta de almacenamiento para el clúster de Hadoop. De lo contrario, la consulta *LOAD DATA* generará un error indicando que no puede obtener acceso a los datos.


## <a name="partition-orc"></a>Temas avanzados: tabla con particiones y datos de Hive de almacén en formato ORC

Si los datos son grandes, crear particiones de la tabla será beneficioso para las consultas que solo necesitan examinar algunas particiones de la tabla. Por ejemplo, es razonable crear particiones de los datos de registro de un sitio web por fechas.

Además de crear particiones de tablas de subárbol, también es beneficioso almacenar los datos de subárbol en el formato ORC. Para obtener más información acerca de la aplicación de formato ORC, consulte <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC#LanguageManualORC-ORCFiles" target="_blank">El uso de archivos ORC mejora el rendimiento cuando Hive está leyendo, escribiendo y procesando datos</a>.

### Tabla con particiones
Esta es la consulta de subárbol que crea una tabla con particiones y carga datos en ella.

    CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<table name>
	(field1 string,
	...
	fieldN string
	)
    PARTITIONED BY (<partitionfieldname> vartype) ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>'
		 lines terminated by '<line separator>' TBLPROPERTIES("skip.header.line.count"="1");
	LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<partitioned table name> 
		PARTITION (<partitionfieldname>=<partitionfieldvalue>);

Al consultar tablas con particiones, se recomienda agregar la condición de partición al **comienzo** de la cláusula `where`, ya que esto mejora la eficacia de la búsqueda de manera significativa.

    select 
		field1, field2, ..., fieldN
	from <database name>.<partitioned table name> 
	where <partitionfieldname>=<partitionfieldvalue> and ...;

### <a name="orc"></a>Almacenamiento de datos de Hive en formato ORC

Los usuarios no pueden cargar datos directamente desde el almacenamiento de blobs en tablas de subárbol que se almacenan en el formato ORC. Estos son los pasos que los usuarios deben llevar a cabo para cargar datos desde blobs de Azure en tablas de subárbol almacenadas en formato ORC.

1. Cree una tabla externa **STORED AS TEXTFILE** y cargue datos del almacenamiento de blobs en la tabla.

		CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<external textfile table name>
		(
			field1 string,
			field2 int,
			...
			fieldN date
		)
		ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' 
			lines terminated by '<line separator>' STORED AS TEXTFILE 
			LOCATION 'wasb:///<directory in Azure blob>' TBLPROPERTIES("skip.header.line.count"="1");

		LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<table name>;

2. Cree una tabla interna con el mismo esquema que la tabla externa del paso 1, con el mismo delimitador de campo, y almacene los datos de subárbol en el formato ORC.

		CREATE TABLE IF NOT EXISTS <database name>.<ORC table name> 
		(
			field1 string,
			field2 int,
			...
			fieldN date
		) 
		ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' STORED AS ORC;

3. Seleccionar datos desde la tabla externa del paso 1 e insertarlos en la tabla de ORC

		INSERT OVERWRITE TABLE <database name>.<ORC table name>
            SELECT * FROM <database name>.<external textfile table name>;

	>[AZURE.NOTE] Si la tabla TEXTFILE *&#60;nombre de base de datos>.&#60;nombre de la tabla de archivo de texto externo>* tiene particiones, en el PASO 3, el comando `SELECT * FROM <database name>.<external textfile table name>` seleccionará la variable de partición como un campo en el conjunto de datos devuelto. Si se inserta en *&#60;nombre de base de datos>.&#60;nombre de la tabla ORC>* se producirá un error ya que *&#60;nombre de base de datos>.&#60;nombre de la tabla ORC>* no tiene la variable de partición como un campo en el esquema de tabla. En este caso, los usuarios deben seleccionar específicamente los campos que se van a insertar en *<nombre de base de datos>.<nombre de la tabla ORC>* de la manera siguiente:

		INSERT OVERWRITE TABLE <database name>.<ORC table name> PARTITION (<partition variable>=<partition value>)
		   SELECT field1, field2, ..., fieldN
		   FROM <database name>.<external textfile table name> 
		   WHERE <partition variable>=<partition value>;

4. Es seguro quitar *&#60;nombre de la tabla de archivo de texto externo>* cuando use la consulta siguiente una vez insertados todos los datos en *&#60;nombre de base de datos>.&#60;nombre de la tabla ORC>*:

		DROP TABLE IF EXISTS <database name>.<external textfile table name>;

Después de seguir este procedimiento, debe tener una tabla con datos en el formato ORC lista para su uso.


##Las secciones de optimización deberían ir aquí.

En la última sección, se describen los parámetros que los usuarios pueden ajustar para que se pueda mejorar el rendimiento de las consultas de subárbol.

<!----HONumber=AcomDC_0211_2016-->