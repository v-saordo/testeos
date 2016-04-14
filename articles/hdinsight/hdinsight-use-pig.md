<properties
   pageTitle="Uso de Pig de Hadoop en HDInsight | Microsoft Azure"
   description="Aprenda a usar Pig con Hadoop en HDInsight."
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
   ms.date="01/28/2016"
   ms.author="larryfr"/>

# Uso de Pig con Hadoop en HDInsight

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

[Apache Pig](http://pig.apache.org/) es una plataforma para crear programas de Hadoop mediante un lenguaje de procedimientos que se conoce como *Pig Latin*. Pig es una alternativa a Java para crear soluciones *MapReduce* y se incluye con HDInsight de Azure.

En este artículo, aprenderá a usar Pig con HDInsight.

##<a id="why"></a>¿Por qué usar Pig?

Uno de los desafíos del procesamiento de datos mediante el uso de MapReduce en Hadoop es la implementación de la lógica de procesamiento únicamente con un mapa y una función de reducción. Para el procesamiento complejo, a menudo debe interrumpir el procesamiento en varias operaciones de MapReduce que están encadenadas para lograr el resultado deseado.

En lugar de obligarle a usar solo las funciones de asignación y reducción, Pig permite definir los procesos como una serie de transformaciones por la que fluyen los datos para producir el resultado deseado.

El lenguaje de Pig Latin le permite describir el flujo de datos desde entrada sin formato, a través de una o varias transformaciones, para producir el resultado deseado. Los programas de Pig Latin siguen este patrón general:

- **Carga**: Los datos leídos que se van a manipular desde el sistema de archivos.
- **Transformación**: manipula los datos.
- **Volcado o almacén**: generar datos en la pantalla o almacenarlos para su procesamiento.

Pig Latin también admite las funciones definidas por el usuario (UDF), que le permiten invocar componentes externos que implementan la lógica que es difícil de modelar en Pig Latin.

Para obtener más información acerca de Pig Latin, consulte el [manual de referencia de Pig Latin 1 (en inglés)](http://pig.apache.org/docs/r0.7.0/piglatin_ref1.html) y el [manual de referencia de Pig Latin 2 (en inglés)](http://pig.apache.org/docs/r0.7.0/piglatin_ref2.html).

Para obtener un ejemplo del uso de UDF con Pig, vea los siguientes documentos:

* [Utilice DataFu con Pig en HDInsight](hdinsight-hadoop-use-pig-datafu-udf.md) - DataFu es una colección de UDF útiles mantenida por Apache

* [Uso de Python con Pig y Hive en HDInsight](hdinsight-python.md)

* [Uso de C# con Hive y Pig en HDInsight](hdinsight-hadoop-hive-pig-udf-dotnet-csharp.md)

##<a id="data"></a>Acerca de los datos de ejemplo

Este ejemplo usa un archivo de ejemplo *log4j*, que se almacena en **/example/data/sample.log** en el contenedor de almacenamiento de blobs. Cada registro del archivo consta de una línea de campos que contiene un campo `[LOG LEVEL]` para mostrar el tipo y la gravedad, por ejemplo:

	2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937

En el ejemplo anterior, el nivel de registro es ERROR.

> [AZURE.NOTE] También puede generar un archivo log4j con la herramienta de registro [Apache Log4j](http://en.wikipedia.org/wiki/Log4j) y luego cargarlo en el blob. Consulte [Carga de datos en HDInsight](hdinsight-upload-data.md) para obtener instrucciones. Para obtener más información sobre el uso de los blobs en el almacenamiento de Azure con HDInsight, consulte [Uso del almacenamiento de blobs de Azure con HDInsight](hdinsight-hadoop-use-blob-storage.md).

Los datos de ejemplo se almacenan en el almacenamiento de blobs de Azure, que HDInsight usa como el sistema de archivos predeterminado para clústeres de Hadoop. HDInsight puede acceder a archivos almacenados en blobs mediante el prefijo **wasb**. Por ejemplo, para tener acceso al archivo sample.log, use la siguiente sintaxis:

	wasb:///example/data/sample.log

Dado que WASB es el almacenamiento predeterminado para HDInsight, también puede acceder al archivo mediante **/example/data/sample.log** desde Pig Latin.

> [AZURE.NOTE] La sintaxis, ****wasb:///**, se usa para acceder a los archivos almacenados en el contenedor de almacenamiento predeterminado del clúster de HDInsight. Si especificó cuentas de almacenamiento adicionales cuando aprovisionó el clúster y desea acceder a los archivos almacenados en estas cuentas, puede acceder a los datos especificando el nombre de contenedor y la dirección de las cuentas de almacenamiento, por ejemplo:****wasb://mycontainer@mystorage.blob.core.windows.net/example/data/sample.log**.


##<a id="job"></a>Acerca del trabajo de ejemplo

El siguiente trabajo de Pig Latin carga el archivo **sample.log** desde el almacenamiento predeterminado para el clúster de HDInsight. A continuación, realiza una serie de transformaciones que generan un recuento de cuántas veces se ha producido cada nivel de registro en los datos de entrada. Los resultados se vuelcan en STDOUT.

	LOGS = LOAD 'wasb:///example/data/sample.log';
	LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
	FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
	GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
	FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
	RESULT = order FREQUENCIES by COUNT desc;
	DUMP RESULT;

En la siguiente imagen se muestra un desglose de lo que hace cada transformación a los datos.

![Representación gráfica de las transformaciones][image-hdi-pig-data-transformation]

##<a id="run"></a>Ejecutar el trabajo de Pig Latin

HDInsight puede ejecutar trabajos de Pig Latin mediante una variedad de métodos. Use la tabla siguiente para decidir cuál es el método adecuado para usted luego siga el vínculo para ver un tutorial.

| **Utilícelo** si desea... | ...un shell **interactivo** | ...procesamiento por **lotes** | ...con este **sistema operativo de clúster** | ...desde este **sistema operativo de cliente** |
|:--------------------------------------------------------------|:---------------------------:|:-----------------------:|:------------------------------------------|:-----------------------------------------|
| [SSH](hdinsight-hadoop-use-pig-ssh.md) | ✔ | ✔ | Linux | Linux, Unix, Mac OS X o Windows |
| [Curl](hdinsight-hadoop-use-pig-curl.md) | &nbsp; | ✔ | Linux o Windows | Linux, Unix, Mac OS X o Windows |
| [.NET SDK para Hadoop](hdinsight-hadoop-use-pig-dotnet-sdk.md) | &nbsp; | ✔ | Linux o Windows | Windows (por ahora) |
| [Windows PowerShell](hdinsight-hadoop-use-pig-powershell.md) | &nbsp; | ✔ | Linux o Windows | Windows |
| [Escritorio remoto](hdinsight-hadoop-use-pig-remote-desktop.md) | ✔ | ✔ | Windows | Windows |


## Ejecución de trabajos de Pig en Azure HDInsight mediante SQL Server Integration Services local

También puede usar SQL Server Integration Services (SSIS) para ejecutar un trabajo de Pig. El paquete de características de Azure para SSIS proporciona los siguientes componentes que funcionan con trabajos de Pig en HDInsight.


- [Tarea de Pig de Azure HDInsight][pigtask]
- [Administrador de conexiones de suscripción de Azure][connectionmanager]


Obtenga más información sobre el paquete de características de Azure para SSIS [aquí][ssispack].


##<a id="nextsteps"></a>Pasos siguientes

Ahora que aprendió a usar Pig con HDInsight, use los siguientes vínculos para explorar otras formas de trabajar con HDInsight de Azure.

* [Carga de datos en HDInsight][hdinsight-upload-data]
* [Uso de Hive con HDInsight][hdinsight-use-hive]
* [Uso de trabajos de MapReduce con HDInsight][hdinsight-use-mapreduce]

[check]: ./media/hdinsight-use-pig/hdi.checkmark.png

[apachepig-home]: http://pig.apache.org/
[putty]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html
[curl]: http://curl.haxx.se/
[pigtask]: http://msdn.microsoft.com/library/mt146781(v=sql.120).aspx
[connectionmanager]: http://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: http://msdn.microsoft.com/library/mt146770(v=sql.120).aspx

[hdinsight-storage]: hdinsight-use-blob-storage.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md#mapreduce-sdk

[Powershell-install-configure]: ../install-configure-powershell.md

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx

[image-hdi-log4j-sample]: ./media/hdinsight-use-pig/HDI.wholesamplefile.png
[image-hdi-pig-data-transformation]: ./media/hdinsight-use-pig/HDI.DataTransformation.gif
[image-hdi-pig-powershell]: ./media/hdinsight-use-pig/hdi.pig.powershell.png
[image-hdi-pig-architecture]: ./media/hdinsight-use-pig/HDI.Pig.Architecture.png

<!---HONumber=AcomDC_0218_2016-->