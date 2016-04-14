<properties 
	pageTitle="Servidor de trabajo de Apache Spark en HDInsight | Microsoft Azure" 
	description="Aprenda a usar el servidor de trabajo Spark para enviar y administrar trabajos de forma remota en un clúster Spark." 
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
	ms.date="12/08/2015" 
	ms.author="nitinme"/>


# Servidor de trabajo Spark en clústeres de HDInsight de Azure (Windows)

> [AZURE.NOTE] HDInsight ofrece ahora clústeres de Spark en Linux, que usa Livy para enviar trabajos de forma remota a un clúster de Spark. Para información sobre cómo usar Livy con HDInsight Spark clústeres en Linux, vea [trabajos Spark enviar remotamente con Livio con clústeres de Spark en HDInsight (Linux)](hdinsight-apache-spark-livy-rest-interface.md).

El clúster Apache Spark en HDInsight de Azure empaqueta el servidor de trabajo Spark como parte de la implementación del clúster. El servidor de trabajo Spark proporciona API de REST para crear contexto Spark, enviar una aplicación Spark al contexto, comprobar el estado del trabajo, eliminar el contexto, etc. En este artículo se proporcionan algunos ejemplos de cómo usar Curl para realizar algunas tareas comunes en un clúster Spark mediante un servidor de trabajo.

>[AZURE.NOTE] Para obtener documentación completa sobre el servidor de trabajo Spark, consulte [https://github.com/spark-jobserver/spark-jobserver](https://github.com/spark-jobserver/spark-jobserver).

## <a name="uploadjar"></a>Carga de un archivo jar en un clúster Spark

	curl.exe -k -u "<hdinsight user>:<user password>" --data-binary @<location of jar on the computer> https://<cluster name>.azurehdinsight.net/sparkjobserver/jars/<application name>

Ejemplo:
	
	curl.exe -k -u "myuser:myPass@word1" --data-binary @C:\mylocation\eventhubs-examples\target\spark-streaming-eventhubs-example-0.1.0-jar-with-dependencies.jar https://mysparkcluster.azurehdinsight.net/sparkjobserver/jars/streamingjar


##<a name="createcontext"></a>Creación de un nuevo contexto persistente en el servidor de trabajo

	curl.exe -k -u "<hdinsight user>:<user password>" -d "" "https://<cluster name>.azurehdinsight.net/sparkjobserver/contexts/<context name>?num-cpu-cores=<value>&memory-per-node=<value>"

Ejemplo:

	curl.exe -k -u "myuser:myPass@word1" -d "" "https://mysparkcluster.azurehdinsight.net/sparkjobserver/contexts/mystreaming?num-cpu-cores=4&memory-per-node=1024m"


##<a name="submitapp"></a>Envío de una aplicación al clúster

	curl.exe -k -u "<hdinsight user>:<user password>" -d @<input file name> "https://<cluster name>.azurehdinsight.net/sparkjobserver/jobs?appName=<app name>&classPath=<class path>&context=<context>"

Ejemplo:

	curl.exe -k -u "myuser:myPass@word1" -d @mypostdata.txt "https://mysparkcluster.azurehdinsight.net/sparkjobserver/jobs?appName=streamingjar&classPath=org.apache.spark.streaming.eventhubs.example.EventCountJobServer&context=mystreaming"

donde mypostdata.txt define la aplicación.


##<a name="submitapp"></a>Eliminación de un trabajo

	curl.exe -X DELETE -k -u "<hdinsight user>:<user password>" "https://<cluster name>.azurehdinsight.net/sparkjobserver/contexts/<context>"

Ejemplo:

	curl.exe -X DELETE -k -u "myuser:myPass@word1" "https://mysparkcluster.azurehdinsight.net/sparkjobserver/contexts/mystreaming"


##<a name="seealso"></a>Otras referencias

* [Introducción a Apache Spark en HDInsight de Azure](hdinsight-apache-spark-overview-v1.md)
* [Creación de un clúster Spark de HDInsight](hdinsight-apache-spark-provision-clusters.md)
* [Uso de herramientas de BI con Apache Spark en HDInsight de Azure](hdinsight-apache-spark-use-bi-tools-v1.md)
* [Uso de Spark en HDInsight para crear aplicaciones de aprendizaje automático](hdinsight-apache-spark-ipython-notebook-machine-learning-v1.md)
* [Streaming con Spark: procesamiento de eventos desde el Centro de eventos de Azure con Apache Spark en HDInsight](hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming.md)
* [Administración de recursos para el clúster Apache Spark en HDInsight de Azure](hdinsight-apache-spark-resource-manager.md)


[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md

<!---HONumber=AcomDC_0218_2016-->