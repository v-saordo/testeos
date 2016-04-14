<properties
	pageTitle="Depurar y analizar servicios de Hadoop con volcados de memoria | Microsoft Azure"
	description="Recopile automáticamente volcados de memoria para servicios de Hadoop y coloque dentro de la cuenta de almacenamiento de blobs de Azure para depuración y análisis."
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/04/2016"
	ms.author="jgao"/>


# Recopilar volcados de memoria en el almacenamiento de blobs para depurar y analizar servicios de Hadoop

[AZURE.INCLUDE [selector de volcado de memoria](../../includes/hdinsight-selector-heap-dump.md)]

Los volcados de montón contienen una instantánea de la memoria de la aplicación, incluidos los valores de variables en el momento en el que se creó el volcado de memoria. Por ello, estos volcados son realmente útiles a la hora de diagnosticar cualquier problema que hubiera ocurrido durante el tiempo de ejecución. Los volcados de memoria pueden recopilarse automáticamente para servicios Hadoop y se colocan en la cuenta de almacenamiento de blobs de Azure de un usuario en HDInsightHeapDumps/.

La recopilación de volcados de memoria para los distintos servicios debe habilitarse para los servicios en clústeres individuales. De forma predeterminada, esta característica está desactivada para un clúster. Los volcados de memoria pueden ser de gran tamaño, por lo que se recomienda supervisar la cuenta de almacenamiento de blobs en la que se van a guardar tras habilitar la recopilación.

> [AZURE.NOTE] La información de este artículo solo se aplica al HDInsight basado en Windows. Para obtener más información sobre el HDInsight basado en Linux, consulte [Habilitar volcados de memoria para servicios de Hadoop en el HDInsight basado en Linux](hdinsight-hadoop-collect-debug-heap-dump-linux.md)

## Servicios de volcados de memoria aptos

Puede habilitar los volcados de montón en los siguientes servicios:

*  **hcatalog** - tempelton
*  **hive** - hiveserver2, metastore, derbyserver
*  **mapreduce** - jobhistoryserver
*  **yarn** - resourcemanager, nodemanager, timelineserver
*  **hdfs** - datanode, secondarynamenode, namenode

## Elementos de configuración que habilitan los volcados de memoria

Para activar el volcado de memoria para un servicio, el usuario deberá definir los elementos de configuración adecuados en la sección de dicho servicio, que se especifica mediante **nombre\_servicio**.

	"javaargs.<service_name>.XX:+HeapDumpOnOutOfMemoryError" = "-XX:+HeapDumpOnOutOfMemoryError",
	"javaargs.<service_name>.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\Dumps<service_name>_%date:~4,2%%date:~7,2%%date:~10,2%%time:~0,2%%time:~3,2%%time:~6,2%.hprof"

El valor de **nombre\_servicio** puede ser cualquiera de los servicios enumerados anteriormente: tempelton, hiveserver2, metastore, derbyserver, jobhistoryserver, resourcemanager, nodemanager, timelineserver, datanode, secondarynamenode o namenode.

## Habilitar con Azure PowerShell

Por ejemplo, para activar los volcados de memoria mediante Azure PowerShell para jobhistoryserver, el usuario debe hacer lo siguiente:

	$MapRedConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightMapReduceConfiguration'

	$MapRedConfigValues.Configuration = @{ "javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError"="-XX:+HeapDumpOnOutOfMemoryError" ; "javaargs.jobhistoryserver.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof" }

## Habilitar con SDK para .NET

Por ejemplo, para activar los volcados de memoria mediante el SDK de .NET de HDInsight de Azure para jobhistoryserver, el usuario debe hacer lo siguiente:

	clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError", "-XX:+HeapDumpOnOutOfMemoryError"));

	clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:HeapDumpPath", "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof"));

<!---HONumber=AcomDC_0211_2016-->