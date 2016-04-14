<properties 
	pageTitle="Instalar y usar Giraph en clústeres de Hadoop en HDInsight | Microsoft Azure" 
	description="Obtenga información acerca de cómo personalizar el clúster de HDInsight con Giraph. Va a usar una opción de configuración de la acción de script para usar un script para instalar Giraph." 
	services="hdinsight" 
	documentationCenter="" 
	authors="nitinme" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/12/2016" 
	ms.author="nitinme"/>

# Instalar Giraph en clústeres de Hadoop de HDInsight y usar Giraph para procesar gráficos a gran escala

Obtenga información acerca de cómo personalizar un clúster de HDInsight basado en Windows con Giraph mediante la acción de script, y cómo usar Giraph para procesar gráficos a gran escala. Para obtener información sobre el uso de Giraph con un clúster basado en Linux, consulte [Instalación de Giraph en clústeres Hadoop de HDinsight (Linux)](hdinsight-hadoop-giraph-install-linux.md)
 
Puede instalar Giraph en cualquier tipo de clúster (Hadoop, Storm, HBase, Spark) en HDInsight de Azure mediante la *acción de script*. Hay un script de ejemplo para instalar Giraph en un clúster de HDInsight disponible desde un blob de almacenamiento de Azure de solo lectura en [https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1). El script de ejemplo solo funciona con el clúster de HDInsight versión 3.1. Para obtener más información acerca de las versiones de clústeres de HDInsight, consulte las [versiones de clústeres de HDInsight](hdinsight-component-versioning.md).

**Artículos relacionados**

- [Instalación de Giraph en clústeres de HDInsight](hdinsight-hadoop-giraph-install.md): instalar Giraph mediante el Portal de Azure
- [Instalación de Giraph en clústeres de Hadoop de HDInsight (Linux)](hdinsight-hadoop-giraph-install-linux.md)
- [Creación de clústeres de Hadoop en HDInsight](hdinsight-provision-clusters.md): información general sobre la creación de clústeres de HDInsight.
- [Personalización de un clúster de HDInsight mediante la acción de script][hdinsight-cluster-customize]\: información general sobre la personalización de clústeres de HDInsight mediante la acción de script.
- [Desarrollo de scripts de acción de script en HDInsight](hdinsight-hadoop-script-actions.md)


## ¿Qué es Giraph?

<a href="http://giraph.apache.org/" target="_blank">Apache Giraph</a> permite realizar un procesamiento gráfico mediante Hadoop, y se puede usar con HDInsight de Azure. Los gráficos modelan las relaciones entre los objetos, como las conexiones entre los enrutadores en una red de gran tamaño como Internet, o las relaciones entre personas de las redes sociales (conocidas en ocasiones como gráficos sociales). El procesamiento gráfico le permite razonar sobre las relaciones entre los objetos de un gráfico. En este sentido, le ayuda por ejemplo a:

- Identificar los posibles amigos según las relaciones actuales.
- Identificar la ruta más corta entre dos equipos de una red.
- Calcular la posición de las páginas web.
   
## Instalación de Giraph mediante el portal

[AZURE.INCLUDE [hdinsight-azure-portal](../../includes/hdinsight-azure-portal.md)]

* [Instalación de Giraph en clústeres de HDInsight](hdinsight-hadoop-giraph-install.md)

1. Comience a crear un clúster mediante la opción **CREACIÓN PERSONALIZADA**, tal como se describe en [Creación de clústeres de Hadoop en HDInsight mediante opciones personalizadas](hdinsight-provision-clusters.md#portal). 
2. En la página **Acciones de script** del asistente, haga clic en **Agregar acción de script** para proporcionar detalles acerca de la acción de script, tal como se muestra a continuación:

	![Uso de la acción de script para personalizar un clúster](./media/hdinsight-hadoop-giraph-install-v1/hdi-script-action-giraph.png "Uso de la acción de script para personalizar un clúster")
	
	<table border='1'>
	<tr><th>Propiedad</th><th>Valor</th></tr>
	<tr><td>Nombre</td>
		<td>Especifique un nombre para la acción de script. Por ejemplo, <b>Instalar Giraph</b>.</td></tr>
	<tr><td>URI de script</td>
		<td>Especifique el Identificador uniforme de recursos (URI) al script que se ha invocado para personalizar el clúster. Por ejemplo, <i>https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1</i></td></tr>
	<tr><td>Tipo de nodo</td>
		<td>Especifique los nodos en los que se ejecuta el script de personalización. Puede elegir <b>Todos los nodos</b>, <b>Solo nodos principales</b> o <b>Solo nodos de trabajo</b>.
	<tr><td>Parámetros</td>
		<td>Especifique los parámetros, si lo requiere el script. El script para instalar Giraph no requiere ningún parámetro, por lo que puede dejarlo en blanco.</td></tr>
</table>Puede agregar más de una acción de script para instalar varios componentes en el clúster. Después de haber agregado los scripts, haga clic en la marca de verificación para comenzar a crear el clúster.

También puede utilizar el script para instalar Giraph en HDInsight con PowerShell de Azure o el SDK de .NET de HDInsight. Más adelante en este tema se proporcionan instrucciones para estos procedimientos.

## Uso de Giraph

Usamos el ejemplo SimpleShortestPathsComputation para mostrar la implementación básica de <a href = "http://people.apache.org/~edwardyoon/documents/pregel.pdf">Pregel</a> para encontrar la ruta más corta entre objetos en un gráfico. Use los siguientes pasos para cargar los datos de ejemplo y el jar de muestra, ejecutar un trabajo mediante el ejemplo SimpleShortestPathsComputation y luego ver los resultados.

1. Cargue un archivo de datos de ejemplo en el almacenamiento de blobs de Azure. En la estación de trabajo local, cree un archivo nuevo denominado **tiny\_graph.txt**. Debe contener las siguientes líneas:

		[0,0,[[1,1],[3,3]]]
		[1,0,[[0,1],[2,2],[3,1]]]
		[2,0,[[1,2],[4,4]]]
		[3,0,[[0,3],[1,1],[4,4]]]
		[4,0,[[3,4],[2,4]]]

	Cargue el archivo tiny\_graph.txt en el almacenamiento principal del clúster de HDInsight. Para obtener instrucciones sobre cómo cargar datos, consulte [Carga de datos para trabajos de Hadoop en HDInsight](hdinsight-upload-data.md).

	Estos datos describen una relación entre los objetos de un gráfico dirigido, con el formato [source\_id, source\_value,[[dest\_id], [edge\_value],...]]. Cada línea representa una relación entre un objeto **source\_id** y uno más objetos **dest\_id**. El valor **edge\_value** (o peso) se puede considerar como la fuerza o la distancia de la conexión entre **source\_id** y **dest\_id**.

	Extrapolados, y usando el valor (o peso) como la distancia entre los objetos, los datos anteriores podrían parecerse a estos:

	![tiny\_graph.txt drawn as circles with lines of varying distance between](./media/hdinsight-hadoop-giraph-install-v1/giraph-graph.png)

	

4. Ejecute el ejemplo SimpleShortestPathsComputation. Utilice los siguientes cmdlets de PowerShell de Azure para ejecutar el ejemplo mediante el archivo tiny\_graph.txt como entrada. Para ello, será necesario que haya instalado y configurado [Azure PowerShell][powershell-install].

		$clusterName = "clustername"
		# Giraph examples jar
		$jarFile = "wasb:///example/jars/giraph-examples.jar"
		# Arguments for this job
		$jobArguments = "org.apache.giraph.examples.SimpleShortestPathsComputation",
		                "-ca", "mapred.job.tracker=headnodehost:9010",
		                "-vif", "org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat",
		                "-vip", "wasb:///example/data/tiny_graph.txt",
		                "-vof", "org.apache.giraph.io.formats.IdWithValueTextOutputFormat",
		                "-op",  "wasb:///example/output/shortestpaths",
		                "-w", "2"
		# Create the definition
		$jobDefinition = New-AzureHDInsightMapReduceJobDefinition
		  -JarFile $jarFile
		  -ClassName "org.apache.giraph.GiraphRunner"
		  -Arguments $jobArguments
		
		# Run the job, write output to the Azure PowerShell window
		$job = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $jobDefinition
		Write-Host "Wait for the job to complete ..." -ForegroundColor Green
		Wait-AzureHDInsightJob -Job $job
		Write-Host "STDERR"
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardError
		Write-Host "Display the standard output ..." -ForegroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardOutput

	En el ejemplo anterior, reemplace **clustername** por el nombre del clúster de HDInsight donde está instalado Giraph.

5. Vea los resultados. Cuando haya finalizado el trabajo, los resultados se almacenarán en dos archivos de salida en la carpeta \_\___wasb:///example/out/shotestpaths__. Los archivos se denominan __part-m-00001__ y __part-m-00002__. Realice los pasos siguientes para descargar y ver el resultado:

		$subscriptionName = "<SubscriptionName>"       # Azure subscription name
		$storageAccountName = "<StorageAccountName>"   # Azure Storage account name
		$containerName = "<ContainerName>"             # Blob storage container name

		# Select the current subscription
		Select-AzureSubscription $subscriptionName
		
		# Create the Storage account context object
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

		# Download the job output to the workstation
		Get-AzureStorageBlobContent -Container $containerName -Blob example/output/shortestpaths/part-m-00001 -Context $storageContext -Force
		Get-AzureStorageBlobContent -Container $containerName -Blob example/output/shortestpaths/part-m-00002 -Context $storageContext -Force

	De esta manera se creará la estructura de directorios __example/output/shortestpaths__ en el directorio actual de la estación de trabajo y se descargarán los dos archivos de salida a esa ubicación.

	Utilice el cmdlet __Cat__ para mostrar los contenidos de los archivos:

		Cat example/output/shortestpaths/part*

	La salida debe ser similar a la siguiente:


		0	1.0
		4	5.0
		2	2.0
		1	0.0
		3	1.0

	El ejemplo SimpleShortestPathComputation se ha codificado de forma rígida para comenzar por el identificador de objeto 1 y encontrar la ruta más corta a los demás objetos. Así que la salida se debe leer como `destination_id distance`, donde la distancia es el valor (o peso) de los bordes recorridos entre el identificador de objeto 1 y el identificador de destino.
	
	Visualizando esto, puede verificar los resultados recorriendo las rutas más cortas entre el Id. 1 y todos los demás objetos. Tenga en cuenta que la ruta más corta entre el Id. 1 y el Id. 4 es 5. Esta es la distancia total entre el <span style="color:orange">identificador 1 y 3</span>, y, a continuación, <span style="color:red">identificador 3 y 4</span>.

	![Drawing of objects as circles with shortest paths drawn between](./media/hdinsight-hadoop-giraph-install-v1/giraph-graph-out.png)







## Instalación de Giraph con Aure PowerShell

Consulte [Personalización de clústeres de HDInsight mediante la acción de script](hdinsight-hadoop-customize-cluster.md#call_scripts_using_powershell). El ejemplo muestra cómo instalar Spark con Azure PowerShell. Deberá personalizar el script para usar [https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1).

## Instalación de Giraph mediante .NET SDK

Consulte [Personalización de clústeres de HDInsight mediante la acción de script](hdinsight-hadoop-customize-cluster.md#call_scripts_using_azure_powershell). El ejemplo muestra cómo instalar Spark con .NET SDK. Deberá personalizar el script para usar [https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1).


## Consulte también

- [Instalación de Giraph en clústeres de HDInsight](hdinsight-hadoop-giraph-install.md): instalar Giraph mediante el Portal de Azure
- [Instalación de Giraph en clústeres de Hadoop de HDInsight (Linux)](hdinsight-hadoop-giraph-install-linux.md)
- [Creación de clústeres de Hadoop en HDInsight](hdinsight-provision-clusters.md): información general sobre la creación de clústeres de HDInsight.
- [Personalización de un clúster de HDInsight mediante la acción de script][hdinsight-cluster-customize]\: información general sobre la personalización de clústeres de HDInsight mediante la acción de script.
- [Desarrollo de scripts de acción de script con HDInsight](hdinsight-hadoop-script-actions.md).
- [Instalación y uso de Spark en clústeres de HDInsight][hdinsight-install-spark]\: ejemplo de acción de script sobre la instalación de Spark.
- [Instalación de R en clústeres de HDInsight][hdinsight-install-r]\: ejemplo de acción de script sobre la instalación de R.
- [Instalación de Solr en clústeres de HDInsight](hdinsight-hadoop-solr-install.md): ejemplo de acción de script sobre la instalación de Solr.

[tools]: https://github.com/Blackmist/hdinsight-tools
[aps]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/

[powershell-install]: ../powershell-install-configure.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md
 

<!---HONumber=AcomDC_0218_2016-->