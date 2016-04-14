<properties
	pageTitle="Aprender qué es Hive y cómo usar HiveQL | Microsoft Azure"
	description="Obtenga información sobre Apache Hive y sobre cómo usarlo con Hadoop en HDInsight. Elija cómo ejecutar el trabajo de Hive y use HiveQL para analizar un archivo log4j de Apache de ejemplo."
	keywords="hiveql,what is hive"
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

# Usar Hive y HiveQL con Hadoop en HDInsight para analizar un archivo log4j de Apache de muestra

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]


En este tutorial, podrá aprender a usar Apache Hive en Hadoop en HDInsight y elegir cómo desea ejecutar el trabajo de Hive. También aprenderá sobre HiveQL y cómo analizar un archivo Apache log4j de ejemplo.

##<a id="why"></a>¿Qué es Hive y por qué usarlo?
[Apache Hive](http://hive.apache.org/) es un sistema de almacén de datos para Hadoop, que permite realizar resúmenes de datos, consultas y análisis de datos mediante HiveQL (una lenguaje de consultas similar a SQL). Hive se puede usar para explorar los datos de forma interactiva o para crear trabajos de procesamiento por lotes reutilizables.

Hive le permite proyectar la estructura del proyecto en datos que en gran medida no están estructurados. Después de definir la estructura, puede usar Hive para consultar esos datos sin conocimiento de Java o MapReduce. **HiveQL** (el lenguaje de consultas de Hive) permite escribir consultas con instrucciones similares a T-SQL.

Hive comprende cómo trabajar con datos estructurados y semiestructurados, como archivos de texto donde los campos están delimitados por caracteres específicos. Hive también admite **serializador/deserializadores (SerDe)** personalizados para datos estructurados irregularmente o complejos. Para obtener más información, consulte [Cómo usar un SerDe de JSON personalizado con HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight.aspx).

Hive también puede extenderse a través de **funciones definidas por el usuario (UDF)**. Una UDF le permite implementar la funcionalidad o la lógica que no se modela con facilidad en HiveQL. Para obtener un ejemplo del uso de UDF con Hive, vea lo siguiente:

* [Uso de Python con Hive y Pig en HDInsight](hdinsight-python.md)

* [Uso de C# con Hive y Pig en HDInsight](hdinsight-hadoop-hive-pig-udf-dotnet-csharp.md)

* [Cómo agregar una UDF de Hive personalizada a HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)


## Tablas internas frente a tablas externas de Hive.

Existen determinados aspectos que debe conocer en relación con la tabla interna y externa de Hive:

- El comando **CREATE TABLE** crea una tabla interna. El archivo de datos debe ubicarse en el contenedor predeterminado.
- El comando **CREATE TABLE** mueve el archivo de datos a la carpeta /hive/warehouse/<TableName>.
- El comando **CREATE EXTERNAL TABLE** crea una tabla externa. El archivo de datos puede estar ubicado fuera del contenedor predeterminado.
- El comando **CREATE EXTERNAL TABLE** no mueve el archivo de datos.
- El comando **CREATE EXTERNAL TABLE** no permite ninguna carpeta en LOCATION. Este es el motivo por el que el tutorial hace una copia del archivo sample.log.

Para obtener más información, consulte [HDInsight: introducción de tablas internas y externas de Hive][cindygross-hive-tables].


##<a id="data"></a>Acerca de los datos de ejemplo, un archivo log4j de Apache

Este ejemplo usa un archivo de ejemplo *log4j*, que se almacena en **/example/data/sample.log** en el contenedor de almacenamiento de blobs. Cada registro del archivo consta de una línea de campos que contiene un campo `[LOG LEVEL]` para mostrar el tipo y la gravedad, por ejemplo:

	2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937

En el ejemplo anterior, el nivel de registro es ERROR.

> [AZURE.NOTE] También puede generar un archivo log4j con la utilidad de registro [Apache Log4j](http://en.wikipedia.org/wiki/Log4j) y luego cargarlo en el contenedor de blobs. Consulte [Carga de datos en HDInsight](hdinsight-upload-data.md) para obtener instrucciones. Para obtener más información sobre el uso del almacenamiento de blobs de Azure con HDInsight, consulte [Uso del almacenamiento de blobs de Azure con HDInsight](hdinsight-hadoop-use-blob-storage.md).

Los datos de ejemplo se almacenan en el almacenamiento de blobs de Azure, que HDInsight usa como el sistema de archivos predeterminado. HDInsight puede acceder a archivos almacenados en blobs mediante el prefijo **wasb**. Por ejemplo, para tener acceso al archivo sample.log, use la siguiente sintaxis:

	wasb:///example/data/sample.log

Dado que el almacenamiento de blobs de Azure es el almacenamiento predeterminado para HDInsight, también puede acceder al archivo mediante **/example/data/sample.log** desde HiveQL.

> [AZURE.NOTE] La sintaxis, ****wasb:///**, se usa para acceder a los archivos almacenados en el contenedor de almacenamiento predeterminado del clúster de HDInsight. Si especificó cuentas de almacenamiento adicionales cuando aprovisionó el clúster y desea acceder a los archivos almacenados en estas cuentas, puede acceder a los datos especificando el nombre de contenedor y la dirección de las cuentas de almacenamiento, por ejemplo: ****wasb://mycontainer@mystorage.blob.core.windows.net/example/data/sample.log**.

##<a id="job"></a>Trabajo de ejemplo: proyectar columnas en datos delimitados

Las siguientes instrucciones de HiveQL proyectarán columnas en datos delimitados que se almacenan en el directorio ****wasb:///example/data**:

	DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

En el ejemplo anterior, las instrucciones de HiveQL realizan las acciones siguientes:

* **DROP TABLE**: elimina la tabla y el archivo de datos si la tabla ya existe.
* **CREATE EXTERNAL TABLE**: crea una tabla **externa** nueva en Hive. Las tablas externas solo almacenan la definición de tabla en Hive; los datos quedan en la ubicación original y en el formato original.
* **ROW FORMAT**: indica cómo se da formato a los datos de Hive. En este caso, los campos de cada registro se separan mediante un espacio.
* **STORED AS TEXTFILE LOCATION**: indica a Hive dónde se almacenan los datos (el directorio de ejemplos/datos) y que se almacenen como texto. Los datos pueden estar en un archivo o distribuidos en varios archivos dentro del directorio.
* **SELECT**: selecciona un número de todas las filas donde la columna **t4** contiene el valor **[ERROR]**. Esto debe devolver un valor de **3** porque hay tres filas que contienen este valor.
* **INPUT\_\_FILE\_\_NAME LIKE '%.log'**: indica a Hive que solo deberíamos devolver datos de archivos que terminan en .log. Esto restringe la búsqueda al archivo sample.log que contiene los datos y le impide que devuelva datos de otros archivos de datos de ejemplo que no coinciden con el esquema que hemos definido.

> [AZURE.NOTE] Las tablas externas se deben usar cuando espera que un origen externo, como por ejemplo un proceso de carga de datos automático, u otra operación MapReduce, actualice los datos subyacentes, pero siempre desea que las consultas de Hive usen los datos más recientes.
>
> La eliminación de una tabla externa **no** elimina los datos, solamente la definición de tabla.

Después de crear la tabla externa, las siguientes instrucciones sirven para crear una tabla **interna**.

	CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
	STORED AS ORC;
	INSERT OVERWRITE TABLE errorLogs
	SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';

Estas instrucciones realizan las acciones siguientes:

* **CREATE TABLE IF NOT EXISTS**: crea una tabla, si todavía no existe. Dado que no se usa la palabra clave **EXTERNAL**, se trata de una tabla interna, que se almacena en el almacenamiento de datos de Hive y es administrada completamente por Hive.
* **STORED AS ORC**: almacena los datos en el formato Optimized Row Columnar (ORC). Se trata de un formato altamente optimizado y eficiente para almacenar datos de Hive.
* **INSERT OVERWRITE ... SELECT**: selecciona filas de la tabla **log4jLogs** que contiene **[ERROR]** y luego inserta los datos en la tabla **errorLogs**.

> [AZURE.NOTE] A diferencia de las tablas externas, la eliminación de una tabla interna también eliminará los datos subyacentes.

##<a id="usetez"></a>Use Apache Tez para un mejor rendimiento

[Apache Tez](http://tez.apache.org) es un marco que permite que aplicaciones con uso intensivo de datos, como Hive, se ejecuten con mucha más eficacia a escala. En la última versión de HDInsight, Hive admite la ejecución en Tez. Tez está habilitado de manera predeterminada para los clústeres de HDInsight basados en Linux.

> [AZURE.NOTE] Tez actualmente está desactivado de forma predeterminada para clústeres de HDInsight basados en Windows y debe estar habilitado. Para aprovechar Tez, debe establecerse el siguiente valor en una consulta de Hive:
>
> ```set hive.execution.engine=tez;```
>
>Este valor puede enviarse por consulta insertándolo al comienzo de la consulta. También se puede establecer para que se active de manera predeterminada en un clúster estableciendo el valor de configuración en el momento de la creación del clúster. Puede encontrar más detalles en [Aprovisionamiento de clústeres de HDInsight](hdinsight-provision-clusters.md).

Los [documentos de diseño de Hive en Tez](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez) incluyen varios detalles sobre las opciones de implementación y las configuraciones de ajuste.

Para ayudar a depurar los trabajos que se ejecutaron mediante Tez, HDInsight proporciona las siguientes interfaces de usuario web que le permiten ver los detalles de los trabajos de Tez:

* [Use the Tez UI on Windows-based HDInsight](hdinsight-debug-tez-ui.md) (Uso de la IU de Tez en HDInsight basado en Windows)

* [Use the Ambari Tez view on Linux-based HDInsight](hdinsight-debug-ambari-tez-view.md) (Uso de la vista Tez de Ambari en HDInsight basado en Linux)

##<a id="run"></a>Elija cómo desea ejecutar el trabajo de HiveQL

HDInsight puede ejecutar trabajos de HiveQL mediante una variedad de métodos. Use la tabla siguiente para decidir cuál es el método adecuado para usted luego siga el vínculo para ver un tutorial.

| **Utilícelo** si desea... | ...un shell **interactivo** | ...procesamiento por **lotes** | ...con este **sistema operativo de clúster** | ...desde este **sistema operativo de cliente** |
|:--------------------------------------------------------------------------------|:---------------------------:|:-----------------------:|:------------------------------------------|:-----------------------------------------|
| [Vista de Hive](hdinsight-hadoop-use-hive-ambari-view.md) | ✔ | ✔ | Linux | Cualquiera (en función del explorador) |
| [Comando de Beeline (desde una sesión de SSH)](hdinsight-hadoop-use-hive-beeline.md) | ✔ | ✔ | Linux | Linux, Unix, Mac OS X o Windows |
| [Comando de Hive (desde una sesión de SSH)](hdinsight-hadoop-use-hive-ssh.md) | ✔ | ✔ | Linux | Linux, Unix, Mac OS X o Windows |
| [Curl](hdinsight-hadoop-use-hive-curl.md) | &nbsp; | ✔ | Linux o Windows | Linux, Unix, Mac OS X o Windows |
| [Consola de consultas](hdinsight-hadoop-use-hive-query-console.md) | &nbsp; | ✔ | Windows | Cualquiera (en función del explorador) |
| [Herramientas de HDInsight para Visual Studio](hdinsight-hadoop-use-hive-visual-studio.md) | &nbsp; | ✔ | Linux o Windows | Windows |
| [Windows PowerShell](hdinsight-hadoop-use-hive-powershell.md) | &nbsp; | ✔ | Linux o Windows | Windows |
| [Escritorio remoto](hdinsight-hadoop-use-hive-remote-desktop.md) | ✔ | ✔ | Windows | Windows |

## Ejecución de trabajos de Hive en Azure HDInsight mediante SQL Server Integration Services local

También puede usar SQL Server Integration Services (SSIS) para ejecutar un trabajo de Hive. El paquete de características de Azure para SSIS proporciona los siguientes componentes que funcionan con trabajos de Hive en HDInsight.


- [Tarea de Hive de Azure HDInsight][hivetask]
- [Administrador de conexiones de suscripción de Azure][connectionmanager]


Obtenga más información sobre el paquete de características de Azure para SSIS [aquí][ssispack].


##<a id="nextsteps"></a>Pasos siguientes

Ahora que aprendió qué es Hive y cómo usarlo con Hadoop en HDInsight, use los siguientes vínculos para explorar otras formas de trabajar con HDInsight de Azure.


- [Carga de datos en HDInsight][hdinsight-upload-data]
- [Uso de Pig con HDInsight][hdinsight-use-pig]
- [Uso de trabajos de MapReduce con HDInsight][hdinsight-use-mapreduce]

[check]: ./media/hdinsight-use-hive/hdi.checkmark.png

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/
[hivetask]: http://msdn.microsoft.com/library/mt146771(v=sql.120).aspx
[connectionmanager]: http://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: http://msdn.microsoft.com/library/mt146770(v=sql.120).aspx

[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md


[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-get-started.md

[Powershell-install-configure]: ../install-configure-powershell.md
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

[image-hdi-hive-powershell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
[image-hdi-hive-architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png


[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

<!---HONumber=AcomDC_0218_2016-->