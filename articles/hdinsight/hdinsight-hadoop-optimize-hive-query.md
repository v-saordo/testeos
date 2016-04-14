<properties
   pageTitle="Optimizar las consultas de Hive para una ejecución más rápida en HDInsight | Microsoft Azure"
   description="Aprenda a optimizar sus consultas de Hive para Hadoop en HDInsight."
   services="hdinsight"
   documentationCenter=""
   authors="rashimg"
   manager="mwinkle"
   editor="cgronlun"
   tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/28/2015"
   ms.author="rashimg"/>


# Optimizar consultas de Hive para Hadoop en HDInsight

De manera predeterminada, los clústeres de Hadoop no están optimizados para el rendimiento. En este artículo se tratan algunos de los métodos de optimización de rendimiento de Hive más comunes que se pueden aplicar a nuestras consultas.


[AZURE.INCLUDE [preview-portal](../../includes/hdinsight-azure-preview-portal.md)]


* [Optimizar consultas de Hive para Hadoop en HDInsight](hdinsight-hadoop-optimize-hive-query-v1.md).

##Escalar horizontalmente nodos de trabajo

El aumento del número de nodos de trabajo de un clúster puede aprovechar más asignadores y reductores para ejecutarse en paralelo. Hay dos maneras de aumentar la escala horizontal en HDInsight:

- En el momento de aprovisionamiento, puede especificar el número de nodos de trabajo mediante el Portal de Azure, PowerShell de Azure y la interfaz de la línea de comandos entre plataformas. Para obtener más información, consulte [Aprovisionamiento de clústeres de HDInsight](hdinsight-provision-clusters.md). En la siguiente pantalla se muestra la configuración del nodo de trabajo en el portal de Azure:

	![scaleout\_1][image-hdi-optimize-hive-scaleout_1]

- En tiempo de ejecución, también puede escalar horizontalmente un clúster sin volver a crear uno. Esto se muestra a continuación. ![scaleout\_1][image-hdi-optimize-hive-scaleout_2]

Para obtener más detalles sobre las distintas máquinas virtuales admitidas por HDInsight, vea [precios HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

##Habilitar Tez

[Apache Tez](http://hortonworks.com/hadoop/tez/) es un motor de ejecución alternativa al motor de MapReduce:

![tez\_1][image-hdi-optimize-hive-tez_1]


Tez es más rápido porque:

- Ejecuta el grafo acíclico dirigido (DAG) como un único trabajo en el motor de MapReduce, el DAG que se expresa requiere que cada conjunto de asignadores vaya seguido de un conjunto de reductores. Esto hace que se ponga en marcha varios trabajos de MapReduce para cada consulta de Hive. Tez no tiene dicha restricción y puede procesar DAG complejo como un trabajo minimizando así la sobrecarga de inicio del trabajo.
- **Evita escrituras innecesarias** Debido a que se están poniendo en marcha varios trabajos para la misma consulta de Hive en el motor de MapReduce, la salida de cada trabajo se escribe en HDFS para los datos intermedios. Puesto que Tez minimiza el número de trabajos para cada consulta de Hive, puede evitar la escritura innecesaria.
- **Reduce retrasos de inicio** Tez puede reducir mejor el retraso de inicio al reducir el número de asignadores que necesita para iniciar y al mejorar también la optimización.
- **Reutiliza contenedores** Siempre que es posible, Tez puede reutilizar contenedores para asegurarse de que se reduce la latencia debido al reinicio de contenedores.
- **Técnicas de optimización continua** Tradicionalmente, la optimización se realizó durante la fase de compilación. Sin embargo, hay más información disponible acerca de las entradas que permiten una mejor optimización en tiempo de ejecución. Tez usa las técnicas de optimización continua que le permiten optimizar más el plan en la fase de tiempo de ejecución.

Para obtener más detalles sobre estos conceptos, haga clic en [aquí](http://hortonworks.com/hadoop/tez/).

Puede realizar cualquier consulta de Hive habilitada con Tez anteponiendo a la consulta la siguiente configuración:

	set hive.execution.engine=tez;

Para los clústeres de HDInsight basados en Windows, Tez debe estar habilitada en el momento del aprovisionamiento. El siguiente es un script de PowerShell de Azure de ejemplo para el aprovisionamiento de un clúster de Hadoop habilitado con Tez:


	$clusterName = "[HDInsightClusterName]"
	$location = "[AzureDataCenter]" #i.e. West US
	$dataNodes = 32 # number of worker nodes in the cluster

	$defaultStorageAccountName = "[DefaultStorageAccountName]"
	$defaultStorageContainerName = "[DefaultBlobContainerName]"
	$defaultStorageAccountKey = $defaultStorageAccountKey = Get-AzureStorageKey $defaultStorageAccountName.ToLower() | %{ $_.Primary }

	$hdiUserName = "[HTTPUserName]"
	$hdiPassword = "[HTTPUserPassword]"

	$hdiSecurePassword = ConvertTo-SecureString $hdiPassword -AsPlainText -Force
	$hdiCredential = New-Object System.Management.Automation.PSCredential($hdiUserName, $hdiSecurePassword)

	$hiveConfig = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightHiveConfiguration'
	$hiveConfig.Configuration = @{ "hive.execution.engine"="tez" }

	New-AzureHDInsightClusterConfig -ClusterSizeInNodes $dataNodes -HeadNodeVMSize Standard_D14 -DataNodeVMSize Standard_D14 |
	Set-AzureHDInsightDefaultStorage -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" -StorageAccountKey $defaultStorageAccountKey -StorageContainerName $defaultStorageContainerName |
	Add-AzureHDInsightConfigValues -Hive $hiveConfig |
	New-AzureHDInsightCluster -Name $clusterName -Location $location -Credential $hdiCredential

    
> [AZURE.NOTE] Los clústeres de HDInsight basados en Linux tienen Tez habilitada de forma predeterminada.
    

## Creación de particiones de Hive

La operación de E/S es el principal cuello de botella de rendimiento para ejecutar consultas de Hive. El rendimiento se puede mejorar si se puede reducir la cantidad de datos que deben leerse. De forma predeterminada, las consultas de Hive examinan tablas completas de Hive. Esto es excelente para las consultas como recorridos de tabla; sin embargo, para las consultas que sólo necesitan analizar una pequeña cantidad de datos (por ejemplo, consultas con filtrado), esto crea una sobrecarga innecesaria. La creación de particiones de Hive permite a las consultas de Hive tener acceso solo a la cantidad de datos necesaria en las tablas de Hive.

La creación de particiones de Hive se implementa mediante la reorganización de los datos sin procesar en nuevos directorios teniendo cada partición su propio directorio (donde la partición se define por el usuario). En el siguiente diagrama se ilustra la creación de particiones de una tabla de Hive por la columna *Año*. Se crea un nuevo directorio para cada año.

![creación de particiones][image-hdi-optimize-hive-partitioning_1]

Algunas consideraciones de particiones:

- **No cree particiones insuficientes**: la creación de particiones en columnas con solo unos pocos valores puede generar muy pocas particiones. Por ejemplo, la creación de particiones por sexo solo creará dos particiones (hombres y mujeres); por tanto, solo se reduce la latencia a un máximo de la mitad.

- **No cree particiones excesivas**: en el otro extremo, la creación de una partición en una columna con un valor único (por ejemplo, userid) hará que varias particiones provoquen un gran esfuerzo en el namenode del clúster ya que tendrá que controlar la gran cantidad de directorios.

- **Evite el sesgo de datos**: elija su clave de creación de particiones con cuidado de manera que todas las particiones tengan el mismo tamaño. Un ejemplo es que la creación de particiones en *Estado* puede hacer que el número de registros en California 30 veces superior al de Vermont debido a la diferencia en la población.

Para crear una tabla de particiones, use la cláusula *Particionado por*:

    CREATE TABLE lineitem_part
    	(L_ORDERKEY INT, L_PARTKEY INT, L_SUPPKEY INT,L_LINENUMBER INT,
    	 L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
    	 L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
    	 L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE 	  	 STRING, L_SHIPINSTRUCT STRING, L_SHIPMODE STRING,
    	 L_COMMENT STRING)
    PARTITIONED BY(L_SHIPDATE STRING)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    STORED AS TEXTFILE;

Cuando se cree la tabla con particiones, puede crear las particiones estáticas o las particiones dinámicas.

- Las **particiones estáticas** indican que ya ha particionado datos en los directorios adecuados y que puede pedir particiones de Hive manualmente según la ubicación del directorio. Esto se muestra en el siguiente fragmento de código.

	    INSERT OVERWRITE TABLE lineitem_part
	    PARTITION (L_SHIPDATE = ‘5/23/1996 12:00:00 AM’)
	    SELECT * FROM lineitem 
	    WHERE lineitem.L_SHIPDATE = ‘5/23/1996 12:00:00 AM’

	    ALTER TABLE lineitem_part ADD PARTITION (L_SHIPDATE = ‘5/23/1996 12:00:00 AM’))
	    LOCATION ‘wasb://sampledata@ignitedemo.blob.core.windows.net/partitions/5_23_1996/'

- **La partición dinámica** significa que desea que Hive cree particiones automáticamente para usted. Dado que ya hemos creado la tabla de particiones desde la tabla de almacenamiento temporal, lo único que debemos hacer es insertar datos en la tabla con particiones, como se muestra a continuación:

	    SET hive.exec.dynamic.partition = true;
	    SET hive.exec.dynamic.partition.mode = nonstrict;
	    INSERT INTO TABLE lineitem_part
	    PARTITION (L_SHIPDATE)
	    SELECT L_ORDERKEY as L_ORDERKEY, L_PARTKEY as L_PARTKEY , 
	    	 L_SUPPKEY as L_SUPPKEY, L_LINENUMBER as L_LINENUMBER,
	     	 L_QUANTITY as L_QUANTITY, L_EXTENDEDPRICE as L_EXTENDEDPRICE,
	    	 L_DISCOUNT as L_DISCOUNT, L_TAX as L_TAX, L_RETURNFLAG as 	 	 L_RETURNFLAG, L_LINESTATUS as L_LINESTATUS, L_SHIPDATE as 	 	 L_SHIPDATE_PS, L_COMMITDATE as L_COMMITDATE, L_RECEIPTDATE as 	 L_RECEIPTDATE, L_SHIPINSTRUCT as L_SHIPINSTRUCT, L_SHIPMODE as 	 L_SHIPMODE, L_COMMENT as L_COMMENT, L_SHIPDATE as L_SHIPDATE FROM lineitem;

Para obtener más información, vea [Tablas con particiones](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-PartitionedTables).

##Usar el formato ORCFile

Hive admite diferentes formatos de archivo. Por ejemplo:

- **Texto**: este es el formato de archivo predeterminado y funciona con la mayoría de escenarios
- **Avro**: funciona bien para escenarios de interoperabilidad
- **ORC/Parquet**: idóneo para el rendimiento

El formato ORC (Optimized Row Columnar) es una manera muy eficaz de almacenar datos de Hive. En comparación con otros formatos, ORC tiene las siguientes ventajas:

- compatibilidad con tipos complejos incluidos DateTime y tipos complejos y semiestructurados
- hasta un 70 % de compresión
- indiza cada 10.000 filas, lo que permite omitir filas
- un gran descenso en la ejecución del tiempo de ejecución

Para habilitar el formato ORC, debe crear primero una tabla con la cláusula *Almacenados como ORC*:

    CREATE TABLE lineitem_orc_part
    	(L_ORDERKEY INT, L_PARTKEY INT,L_SUPPKEY INT, L_LINENUMBER INT,
    	 L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
    	 L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
    	 L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE STRING,
		 L_SHIPINSTRUCT STRING, L_SHIPMODE STRING, L_COMMENT 	 STRING)
    PARTITIONED BY(L_SHIPDATE STRING)
    STORED AS ORC;

A continuación, inserte datos en la tabla ORC desde la tabla de almacenamiento temporal. Por ejemplo:

    INSERT INTO TABLE lineitem_orc
    SELECT L_ORDERKEY as L_ORDERKEY, 
           L_PARTKEY as L_PARTKEY , 
    	   L_SUPPKEY as L_SUPPKEY,
		   L_LINENUMBER as L_LINENUMBER,
     	   L_QUANTITY as L_QUANTITY, 
		   L_EXTENDEDPRICE as L_EXTENDEDPRICE,
    	   L_DISCOUNT as L_DISCOUNT,
		   L_TAX as L_TAX,
           L_RETURNFLAG as L_RETURNFLAG,
		   L_LINESTATUS as L_LINESTATUS,
		   L_SHIPDATE as L_SHIPDATE,
		   L_COMMITDATE as L_COMMITDATE,
		   L_RECEIPTDATE as L_RECEIPTDATE, 
		   L_SHIPINSTRUCT as L_SHIPINSTRUCT,
		   L_SHIPMODE as L_SHIPMODE,
		   L_COMMENT as L_COMMENT
    FROM lineitem;

Puede leer más sobre el formato ORC [aquí](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC).

##Vectorización

La vectorización permite a Hive procesar un lote de 1024 filas juntas en lugar de procesar una fila cada vez. Esto significa que las operaciones sencillas se realizan con mayor rapidez porque se necesita ejecutar menos código interno.

Para habilitar la vectorización, anteponga a su consulta de Hive la siguiente configuración:

    set hive.vectorized.execution.enabled = true;

Para obtener más información, vea [Ejecución de consultas vectorizadas](https://cwiki.apache.org/confluence/display/Hive/Vectorized+Query+Execution).


##Otros métodos de optimización

Hay más métodos de optimización que puede considerar, por ejemplo:

- **Creación de depósitos de Hive:** una técnica que permite agrupar o segmentar grandes conjuntos de datos para optimizar el rendimiento de las consultas.
- **Optimización de combinación:** optimización de la planeación de la ejecución de consultas de Hive para mejorar la eficacia de las combinaciones y reducir la necesidad de sugerencias de usuario. Para obtener más información, vea [Optimización de combinación](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+JoinOptimization#LanguageManualJoinOptimization-JoinOptimization).
- **Aumentar reductores**

##<a id="nextsteps"></a> Pasos siguientes
En este artículo, ha aprendido varios métodos comunes de optimización de consultas de Hive. Para obtener más información, consulte los artículos siguientes:

- [Uso de Apache Hive en HDInsight](hdinsight-use-hive.md)
- [Análisis de datos de retraso de vuelos con Hive en HDInsight](hdinsight-analyze-flight-delay-data.md)
- [Análisis de datos de Twitter con Hive en HDInsight](hdinsight-analyze-twitter-data.md)
- [Análisis de datos de sensor mediante la consola de consultas de Hive en Hadoop con HDInsight](hdinsight-hive-analyze-sensor-data.md)
- [Uso de Hive con HDInsight para analizar registros de sitios web](hdinsight-hive-analyze-website-log.md)


[image-hdi-optimize-hive-scaleout_1]: ./media/hdinsight-hadoop-optimize-hive-query/scaleout_1.png
[image-hdi-optimize-hive-scaleout_2]: ./media/hdinsight-hadoop-optimize-hive-query/scaleout_2.png
[image-hdi-optimize-hive-tez_1]: ./media/hdinsight-hadoop-optimize-hive-query/tez_1.png
[image-hdi-optimize-hive-partitioning_1]: ./media/hdinsight-hadoop-optimize-hive-query/partitioning_1.png

<!---HONumber=AcomDC_0218_2016-->