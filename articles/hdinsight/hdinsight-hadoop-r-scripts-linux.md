<properties
	pageTitle="Instalación de R en HDInsight basado en Linux | Microsoft Azure"
	description="Obtenga información acerca de cómo instalar y usar R para personalizar los clústeres de Hadoop basados en Linux."
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/05/2016"
	ms.author="larryfr"/>

# Instalación y uso de R en clústeres de Hadoop para HDInsight

Puede instalar R en cualquier tipo de clúster de Hadoop en HDInsight mediante la personalización de clústeres de **acción de script**. Esto permite que los analistas y los científicos de datos usen R para implementar el eficaz marco de programación MapReduce/YARN para procesar grandes cantidades en datos en clústeres de Hadoop que están implementados en HDInsight.

> [AZURE.NOTE] Para realizar los pasos que se describen en este documento se requiere un clúster de HDInsight basado en Linux. Para obtener información sobre el uso de R con un clúster basado en Windows, vea [Instalación y uso de R en clústeres Hadoop de HDinsight (Windows)](hdinsight-hadoop-r-scripts.md)

## ¿Qué es R?

El <a href="http://www.r-project.org/" target="_blank">proyecto R para el cálculo estadístico</a> es un entorno y lenguaje de código abierto para el cálculo estadístico. R proporciona cientos de de funciones estadísticas integradas y su propio lenguaje de programación que combina aspectos de la programación funcional y orientada a objetos. También proporciona amplias capacidades gráficas. R es el entorno de programación preferido para los científicos y los estadísticos más profesionales de una amplia variedad de campos.

Los scripts de R se pueden ejecutar en clústeres de Hadoop en HDInsight que se han personalizado mediante la acción de script cuando se creó para instalar el entorno de R. R es compatible con el almacenamiento de blobs de Azure (WASB) para que los datos que se almacenan allí se puedan procesar mediante R en HDInsight.

## Funcionamiento del script

La acción de script usada para instalar R en el clúster de HDInsight instala los siguientes paquetes de Ubuntu, que proporcionan una instalación básica de R:

* [r base](http://packages.ubuntu.com/precise/r-base): paquete base de R de GNU.
* [r-base-dev](http://packages.ubuntu.com/precise/r-base-dev): paquetes auxiliares de R de GNU.

También se instalan los siguientes paquetes de RHadoop, que proporcionan integración con MapReduce y HDFS:

* [rmr2](https://github.com/RevolutionAnalytics/rmr2): permite a los desarrolladores de R usar MapReduce de Hadoop.
* [rhdfs](https://github.com/RevolutionAnalytics/rhdfs): permite a los desarrolladores de R usar Hadoop HDFS (WASB para HDInsight).

Además, se instalan los siguientes paquetes de R:

| Paquete de R | Qué proporciona |
| --------- | ---------------- |
| [rJava](https://cran.r-project.org/web/packages/rJava/index.html) | Una interfaz de bajo nivel entre R y Java |
| [Rcpp](https://cran.r-project.org/web/packages/Rcpp/index.html) | Integración de R y C++. |
| [RJSONIO](https://cran.r-project.org/web/packages/RJSONIO/index.html) | Serialización y deserialización de objetos R en JSON. |
| [bitops](https://cran.r-project.org/web/packages/bitops/index.html) | Funciones para las operaciones bit a bit en vectores de entero. |
| [digest](https://cran.r-project.org/web/packages/digest/index.html) | Creación de resúmenes de hash criptográficos de objetos de R. |
| [functional](https://cran.r-project.org/web/packages/functional/index.html) | Curry, Compose y otras funciones de orden superior |
| [reshape2](https://cran.r-project.org/web/packages/reshape2/index.html) | Reestructuración flexible y adición de datos. |
| [stringr](https://cran.r-project.org/web/packages/stringr/index.html) | Contenedores sencillos y coherentes para operaciones comunes de cadena. |
| [plyr](https://cran.r-project.org/web/packages/plyr/index.html) | Herramientas para dividir, aplicar y combinar datos. |
| [caTools](https://cran.r-project.org/web/packages/caTools/index.html) | Herramientas para mover estadísticas de ventana, GIF, Base64, AUC de ROC, etc. |
| [stringdist](https://cran.r-project.org/web/packages/stringdist/index.html) | Funciones de coincidencia aproximada de cadenas y distancia de cadenas. |

## Instalación de R mediante acciones de script

La acción de script [https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh) se usa para instalar R en un clúster de HDInsight. En esta sección se proporcionan instrucciones sobre cómo usar el script durante el aprovisionamiento del clúster mediante el Portal de Azure.

> [AZURE.NOTE] También puede usar Azure PowerShell o el SDK de .NET para HDInsight para crear un clúster mediante este script. Para obtener más información sobre el uso de estos métodos, consulte [Personalización de clústeres de HDInsight mediante acciones de script](hdinsight-hadoop-customize-cluster-linux.md).

1. Inicie el aprovisionamiento de un clúster siguiendo los pasos que se describen en [Aprovisionamiento de clústeres de HDInsight basado en Linux](hdinsight-hadoop-provision-linux-clusters.md#portal), pero no complete la operación.

2. En la hoja **Configuración opcional**, seleccione **Acciones de script** y proporcione la información siguiente:

	* __NOMBRE__: escriba un nombre descriptivo para la acción de script.
	* __URI DE SCRIPT__: https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh
	* __PRINCIPAL__: active esta opción.
	* __TRABAJO__: active esta opción.
	* __ZOOKEEPER__: active esta opción para instalar en el nodo Zookeeper.
	* __PARÁMETROS__: deje este campo en blanco.

3. En la parte inferior de **Acciones de scripts**, use el botón **Seleccionar** para guardar la configuración. Por último, use el botón **Seleccionar** situado en la parte inferior de la hoja **Configuración opcional** para guardar la información de configuración opcional.

4. Siga aprovisionando el clúster como se describe en [Aprovisionamiento de clústeres de HDInsight basados en Linux](hdinsight-hadoop-provision-linux-clusters.md#portal).

## Ejecutar scripts de R

Cuando termine de aprovisionar el clúster, siga estos pasos para usar R para realizar una operación de MapReduce en él.

1. Conéctese al clúster de HDInsight con SSH:

		ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

	Para obtener más información sobre el uso de SSH con HDInsight, vea lo siguiente:

	* [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Linux, Unix u OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

	* [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

2. En el símbolo del sistema de `username@hn0-CLUSTERNAME:~$`, escriba el siguiente comando para iniciar una sesión interactiva de R:

		R

3. Escriba el programa de R siguiente. De esta forma, se generan los números del 1 al 100 y, luego, se multiplican por 2.

		library(rmr2)
		ints = to.dfs(1:100)
		calc = mapreduce(input = ints, map = function(k, v) cbind(v, 2*v))

	La primera línea llama a rmr2 de la biblioteca de RHadoop, que se usa en las operaciones de MapReduce.

	La segunda línea genera valores del 1 al 100 y luego los almacena en el sistema de archivos de Hadoop mediante `to.dfs`.

	La tercera línea crea un proceso de MapReduce mediante la funcionalidad proporcionada por rmr2 y comienza el procesamiento. Debe ver cómo varias líneas se desplazan más allá a medida que comienza el procesamiento.

4. A continuación, use lo siguiente para ver la ruta de acceso temporal en la que se almacenó la salida de MapReduce:

		print(calc())

	Debe ser parecida a la siguiente `/tmp/file5f615d870ad2`. Para ver la salida real, use lo siguiente:

		print(from.dfs(calc))

	El resultado debe ser similar al siguiente:

		[1,]  1 2
		[2,]  2 4
		.
		.
		.
		[98,]  98 196
		[99,]  99 198
		[100,] 100 200

5. Para salir de R, escriba lo siguiente:

		q()


## Pasos siguientes

- [Instalación y uso de Hue en clústeres de HDInsight](hdinsight-hadoop-hue-linux.md) Hue es una interfaz de usuario web que simplifica la creación, la ejecución y el guardado de trabajos de Pig y Hive, así como el examen del almacenamiento predeterminado de su clúster de HDInsight.

- [Instalación y uso de Spark en clústeres de HDInsight][hdinsight-install-spark] para obtener instrucciones sobre cómo usar la personalización de clústeres para instalar y usar Spark en clústeres de Hadoop para HDInsight. Spark es un marco de procesamiento paralelo de código abierto que admite el procesamiento en memoria para mejorar el rendimiento de las aplicaciones analíticas de Big Data.

- [Instalación de Giraph en clústeres de HDInsight](hdinsight-hadoop-giraph-install.md). Use la personalización del clúster para instalar Giraph en clústeres de Hadoop para HDInsight. Giraph permite realizar un procesamiento gráfico con Hadoop y se puede usar con HDInsight de Azure.

- [Instalación de Solr en clústeres de HDInsight](hdinsight-hadoop-solr-install.md). Use la personalización del clúster para instalar Solr en clústeres de Hadoop para HDInsight. Solr le permite realizar potentes operaciones de búsqueda en los datos almacenados.

- [Instalación de Hue en clústeres de HDInsight](hdinsight-hadoop-hue-linux.md). Use la personalización del clúster para instalar Hue en clústeres de Hadoop para HDInsight. Hue es un conjunto de aplicaciones web que usan para interactuar con un clúster de Hadoop.

[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install-linux.md

<!---HONumber=AcomDC_0211_2016-->