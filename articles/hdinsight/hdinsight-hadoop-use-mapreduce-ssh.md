<properties
   pageTitle="MapReduce y conexión de SSH con Hadoop en HDInsight | Microsoft Azure"
   description="Obtenga más información sobre cómo usar SSH para ejecutar trabajos de MapReduce mediante Hadoop en HDInsight."
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
   ms.date="01/08/2016"
   ms.author="larryfr"/>

# Uso de MapReduce con Hadoop en HDInsight con SSH

[AZURE.INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

En este artículo, aprenderá a usar Secure Shell (SSH) para conectarse a un clúster de Hadoop de HDInsight y después enviar trabajos de MapReduce mediante comandos de Hadoop.

> [AZURE.NOTE]Si ya está familiarizado con el uso de servidores de Hadoop basados en Linux, pero no conoce HDInsight, consulte [Información sobre el uso de HDInsight en Linux](hdinsight-hadoop-linux-information.md).

##<a id="prereq"></a>Requisitos previos

Para completar los pasos de este artículo, necesitará lo siguiente:

* Un clúster de HDInsight basado en Linux (Hadoop en HDInsight)

* Un cliente SSH. Los sistemas operativos Linux, Unix y Mac deben incluir un cliente SSH. Los usuarios de Windows deben descargar un cliente similar [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

##<a id="ssh"></a>Conexión con SSH

Conéctese con el nombre de dominio completo (FQDN) de su clúster de HDInsight mediante el comando SSH. El FQDN será el nombre que asignó al clúster, es decir, **.azurehdinsight.net**. Por ejemplo, lo siguiente conectaría con un clúster denominado **myhdinsight**:

	ssh admin@myhdinsight-ssh.azurehdinsight.net

**Si proporcionó una clave de certificado para la autenticación de SSH**, al crear el clúster de HDInsight, es posible que tenga que especificar la ubicación de la clave privada en el sistema cliente, por ejemplo:

	ssh admin@myhdinsight-ssh.azurehdinsight.net -i ~/mykey.key

**Si proporcionó una contraseña para la autenticación de SSH**, al crear el clúster de HDInsight, tendrá que proporcionar la contraseña cuando se le solicite.

Para obtener más información sobre el uso de SSH con HDInsight, consulte [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Linux, Unix u OS X (vista previa)](hdinsight-hadoop-linux-use-ssh-unix.md).

###PuTTY (clientes Windows)

Windows no proporciona ningún cliente SSH integrado. Se recomienda usar **PuTTY**, que se puede descargar en [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Para obtener más información sobre el uso de PuTTY, consulte [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Linux, Unix u OS X (vista previa)](hdinsight-hadoop-linux-use-ssh-windows.md).

##<a id="hadoop"></a>Uso de comandos Hadoop

1. Una vez conectado al clúster de HDInsight, utilice el siguiente comando **Hadoop** para iniciar un trabajo de MapReduce:

		hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount wasb:///example/data/gutenberg/davinci.txt wasb:///example/data/WordCountOutput

	Se inicia la clase **wordcount**, que está contenida en el archivo **hadoop-mapreduce-examples.jar**. Como entrada, usa el documento ****wasb://example/data/gutenberg/davinci.txt** y la salida se almacena en ****wasb:///example/data/WordCountOutput**.

	> [AZURE.NOTE]Para obtener más información sobre este trabajo de MapReduce y los datos de ejemplo, vea [Uso de MapReduce en Hadoop en HDInsight](hdinsight-use-mapreduce.md).

2. El trabajo emite detalles cuando se procesa y devuelve información similar a la siguiente cuando finaliza el trabajo:

		File Input Format Counters
        Bytes Read=1395666
		File Output Format Counters
        Bytes Written=337623

3. Cuando finalice el trabajo, use el siguiente comando para mostrar los archivos de salida almacenados en ****wasb://example/data/WordCountOutput**:

		hdfs dfs -ls wasb:///example/data/WordCountOutput

	Se deberían mostrar dos archivos, **\_SUCCESS** y **part-r-00000**. El archivo **part-r-00000** contiene la salida de este trabajo.

	> [AZURE.NOTE]Algunos trabajos de MapReduce pueden dividir los resultados entre varios archivos **part-r-####**. Si es así, utilice el sufijo #### para indicar el orden de los archivos.

4. Para ver la salida, use el comando siguiente:

		hdfs dfs -cat wasb:///example/data/WordCountOutput/part-r-00000

	Se muestra una lista de las palabras que están contenidas en el archivo ****wasb://example/data/gutenberg/davinci.txt**, junto con el número de veces que apareció cada palabra. El siguiente es un ejemplo de los datos que estarán contenidos en el archivo:

		wreathed        3
		wreathing       1
		wreaths 		1
		wrecked 		3
		wrenching       1
		wretched        6
		wriggling       1

##<a id="summary"></a>Resumen

Como se puede ver, los comando Hadoop proporcionan una manera fácil de ejecutar trabajos de MapReduce en un clúster de HDInsight y, a continuación, ver la salida del trabajo.

##<a id="nextsteps"></a>Pasos siguientes

Para obtener información general sobre los trabajos de MapReduce en HDInsight:

* [Uso de MapReduce en Hadoop de HDInsight](hdinsight-use-mapreduce.md)

Para obtener información sobre otras maneras de trabajar con Hadoop en HDInsight:

* [Uso de Hive con Hadoop en HDInsight](hdinsight-use-hive.md)

* [Uso de Pig con Hadoop en HDInsight](hdinsight-use-pig.md)

<!---HONumber=AcomDC_0114_2016-->