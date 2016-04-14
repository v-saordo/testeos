<properties
   pageTitle="Consulta de Hive con herramientas de Hadoop para Visual Studio | Microsoft Azure"
   description="Obtenga información acerca de cómo usar Hadoop en HDInsight con las herramientas de Hadoop de Visual Studio."
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
   ms.date="02/05/2016"
   ms.author="larryfr"/>

#Ejecución de las consultas de Hive mediante las herramientas de HDInsight para Visual Studio

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

En este artículo, aprenderá a utilizar las herramientas de HDInsight para Visual Studio para enviar consultas de Hive a un clúster de HDInsight.

> [AZURE.NOTE] Este documento no ofrece una descripción detallada de cómo funcionan las instrucciones de HiveQL que se usan en los ejemplos. Para obtener información sobre HiveQL utilizado en este ejemplo, consulte [Uso de Hive con Hadoop en HDInsight](hdinsight-use-hive.md).

##<a id="prereq"></a>Requisitos previos

Necesitará lo siguiente para completar los pasos de este artículo.

* Un clúster de HDInsight (Hadoop en HDInsight) de Azure (Linux o basado en Windows)

* Visual Studio 2012 con [Update 4](http://www.microsoft.com/download/details.aspx?id=39305), Visual Studio 2013 con [Update 3](http://go.microsoft.com/fwlink/?LinkId=390465) o [Visual Studio Express 2013](http://www.microsoft.com/download/details.aspx?id=40769)

##<a id="run"></a> Ejecución de consultas de Hive mediante las herramientas de HDInsight para Visual Studio

1. Abra **Visual Studio** y seleccione **Nuevo** > **Proyecto** > **HDInsight** > **Aplicación de Hive**. Indique un nombre para este proyecto.

2. Abra el archivo **Script.hql** creado con este proyecto y péguelo en las siguientes instrucciones de HiveQL:

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

3. En la barra de herramientas, seleccione el **clúster de HDInsight** que desea usar para esta consulta y, a continuación, seleccione **Enviar** para ejecutar las instrucciones como un trabajo de Hive. El **resumen del trabajo de Hive** aparecerá y mostrará información sobre el trabajo en ejecución. Utilice el vínculo **Actualizar** para actualizar la información del trabajo, hasta que el **estado del trabajo** cambie a **Completado**.

4. Utilice el vínculo **Salida de trabajo** para ver el resultado de este trabajo. Debe mostrar `[ERROR] 3`, que es el valor devuelto por la instrucción SELECT.

5. También puede ejecutar consultas de Hive sin crear un proyecto. Con el **Explorador de servidores**, expanda **Azure** > **HDInsight**, haga clic con el botón derecho en el servidor de HDInsight y, a continuación, seleccione **Escribir una consulta de Hive**.

6. En el documento **temp.hql** que aparece, agregue las siguientes instrucciones de HiveQL.

        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';

    Estas instrucciones realizan las acciones siguientes:

    * **CREATE TABLE IF NOT EXISTS**: crea una tabla, si todavía no existe. Dado que la palabra clave **EXTERNAL** no se usa, se trata de una tabla interna, que se almacena en el almacenamiento de datos de Hive y es administrada completamente por Hive.

        > [AZURE.NOTE] A diferencia de las tablas **EXTERNAL**, la eliminación de una tabla interna también eliminará los datos subyacentes.

    * **STORED AS ORC**: almacena los datos en el formato Optimized Row Columnar (ORC). Se trata de un formato altamente optimizado y eficiente para almacenar datos de Hive.
    * **INSERT OVERWRITE ... SELECT**: selecciona filas de la tabla **log4jLogs** que contienen **[ERROR]** y, a continuación, inserta los datos en la tabla **errorLogs**.

7. En la barra de herramientas, seleccione **Enviar** para ejecutar el trabajo. Utilice el **estado del trabajo** para determinar que el trabajo se ha completado correctamente.

8. Para comprobar que el trabajo se ha completado y se ha creado una nueva tabla, utilice el **Explorador de servidores** y expanda **Azure** > **HDInsight** > el clúster de HDInsight > **Bases de datos de Hive** > y **Predeterminado**. Debería ver las tablas **errorLogs** y **log4jLogs**.

##<a id="summary"></a>Resumen

Como puede ver, las herramientas de HDInsight para Visual Studio proporcionan una manera fácil de ejecutar consultas de Hive en un clúster de HDInsight, de supervisar el estado del trabajo y de recuperar el resultado.

##<a id="nextsteps"></a>Pasos siguientes

Para obtener información general acerca de Hive en HDInsight:

* [Uso de Hive con Hadoop en HDInsight](hdinsight-use-hive.md)

Para obtener información sobre otras maneras en que puede trabajar con Hadoop en HDInsight:

* [Uso de Pig con Hadoop en HDInsight](hdinsight-use-pig.md)

* [Uso de MapReduce con Hadoop en HDInsight](hdinsight-use-mapreduce.md)

Para obtener más información acerca de las herramientas de HDInsight para Visual Studio:

* [Introducción a las herramientas de HDInsight para Visual Studio](../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md)


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

[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

[image-hdi-hive-powershell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
[image-hdi-hive-architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png

<!---HONumber=AcomDC_0218_2016-->