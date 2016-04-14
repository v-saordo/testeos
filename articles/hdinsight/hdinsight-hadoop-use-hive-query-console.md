<properties
   pageTitle="Uso de Hive de Hadoop en la consola de consulta en HDInsight | Microsoft Azure"
   description="Aprenderá a utilizar la consola de consulta basada en web para ejecutar consultas de Hive en un clúster de Hadoop de HDInsight desde el explorador."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"
	tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="02/16/2016"
   ms.author="larryfr"/>

# Ejecución de consultas de Hive mediante la consola de consulta

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

En este artículo, aprenderá a utilizar la consola de la consulta de HDInsight para ejecutar consultas de Hive en un clúster de Hadoop de HDInsight desde el explorador.

> [AZURE.NOTE] La consola de consulta solo está disponible en los clústeres de HDInsight basados en Windows.


##<a id="prereq"></a>Requisitos previos

Necesitará lo siguiente para completar los pasos de este artículo.

* Un clúster de Hadoop para HDInsight basado en Windows

* Un explorador web moderno

##<a id="run"></a>Ejecución de consultas de Hive mediante la consola de consulta

1. Abra un explorador web y navegue a \_\___https://CLUSTERNAME.azurehdinsight.net__, donde __CLUSTERNAME__ es el nombre del clúster de HDInsight. Si se le solicita, escriba el nombre de usuario y la contraseña que escribió cuando creó el clúster.


2. En los vínculos de la parte superior de la página, seleccione **Editor Hive**. Se muestra un formulario que puede utilizarse para introducir instrucciones de HiveQL que desea ejecutar en el clúster de HDInsight.

	![el editor de Hive](./media/hdinsight-hadoop-use-hive-query-console/queryconsole.png)

	Reemplace el texto `Select * from hivesampletable` por las siguientes instrucciones de HiveQL.

        set hive.execution.engine=tez;
        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    Estas instrucciones realizan las acciones siguientes:

    * **DROP TABLE**: elimina la tabla y el archivo de datos si la tabla ya existe.
    * **CREATE EXTERNAL TABLE**: crea una tabla "externa" nueva en Hive. Las tablas externas solo almacenan la definición de tabla en Hive; los datos quedan en la ubicación original.

    > [AZURE.NOTE] Las tablas externas se deben usar cuando espera que un origen externo, como por ejemplo un proceso de carga de datos automático, u otra operación MapReduce, actualice los datos subyacentes, pero siempre desea que las consultas de Hive usen los datos más recientes.
    >
    > La eliminación de una tabla externa **no** elimina los datos, solamente la definición de tabla.

    * **ROW FORMAT**: indica cómo se da formato a los datos de Hive. En este caso, los campos de cada registro se separan mediante un espacio.
    * **STORED AS TEXTFILE LOCATION**: indica a Hive dónde se almacenan los datos (el directorio de ejemplos/datos) y que se almacenen como texto.
    * **SELECT**: permite seleccionar un número de todas las filas donde la columna **t4** contiene el valor **[ERROR]**. Esto debe devolver un valor de **3** porque hay tres filas que contienen este valor.
    * **INPUT\_\_FILE\_\_NAME LIKE '%.log'**: indica a Hive que solo deberíamos devolver datos de archivos que terminan en .log. Esto restringe la búsqueda al archivo sample.log que contiene los datos y le impide que devuelva datos de otros archivos de datos de ejemplo que no coinciden con el esquema que hemos definido.

2. Haga clic en **Enviar**. La **sesión de trabajo** en la parte inferior de la página debe mostrar detalles del trabajo.

3. Cuando el campo **Estado** cambie a **Completado**, seleccione **Ver detalles** para el trabajo. En la página de detalles, **Salida del trabajo** contendrá `[ERROR]	3`. Puede utilizar el botón **Descargar** situado debajo de este campo para descargar un archivo que contiene la salida del trabajo.


##<a id="summary"></a>Resumen

Como puede ver, la consola de consulta proporciona una manera fácil de ejecutar consultas de Hive en un clúster de HDInsight, supervisar el estado del trabajo y recuperar la salida.

Para obtener más información acerca del uso de la consola de consulta de Hive para ejecutar trabajos de Hive, seleccione **Introducción** en la parte superior de la consola de consulta y, a continuación, use los ejemplos proporcionados. Cada ejemplo se recorre el proceso de uso de Hive para analizar datos, incluidas explicaciones de las instrucciones de HiveQL utilizadas en el ejemplo.

##<a id="nextsteps"></a>Pasos siguientes

Para obtener información general acerca de Hive en HDInsight:

* [Uso de Hive con Hadoop en HDInsight](hdinsight-use-hive.md)

Para obtener información sobre otras maneras en que puede trabajar con Hadoop en HDInsight:

* [Uso de Pig con Hadoop en HDInsight](hdinsight-use-pig.md)

* [Uso de MapReduce con Hadoop en HDInsight](hdinsight-use-mapreduce.md)

Si usa Tez con Hive, consulte los siguientes documentos para la información de depuración:

* [Use the Tez UI on Windows-based HDInsight](hdinsight-debug-tez-ui.md) (Uso de la IU de Tez en HDInsight basado en Windows)

* [Use the Ambari Tez view on Linux-based HDInsight](hdinsight-debug-ambari-tez-view.md) (Uso de la vista Tez de Ambari en HDInsight basado en Linux)

[1]: ../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md



[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md

[Powershell-install-configure]: powershell-install-configure.md
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx


[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png

<!---HONumber=AcomDC_0218_2016-->