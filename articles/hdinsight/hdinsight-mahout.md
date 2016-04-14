<properties
	pageTitle="Generación de recomendaciones mediante Mahout y HDInsight basado en Windows | Microsoft Azure"
	description="Aprenda a usar la biblioteca de aprendizaje automático de Apache Mahout para generar recomendaciones de películas con HDInsight basado en Windows (Hadoop)."
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/28/2016"
	ms.author="larryfr"/>

#Generación de recomendaciones de películas mediante Apache Mahout con Hadoop en HDInsight

[AZURE.INCLUDE [mahout-selector](../../includes/hdinsight-selector-mahout.md)]

Aprenda a usar la biblioteca de aprendizaje automático de [Apache Mahout](http://mahout.apache.org) con HDInsight de Azure para generar recomendaciones de películas con HDInsight.

> [AZURE.NOTE] Los pasos de este documento requieren un cliente Windows y un clúster de HDInsight basado en Windows. Para obtener información sobre el uso de Mahout en un cliente Linux, OS X de Unix, con un clúster de HDInsight basado en Linux, consulte [Generate movie recommendations by using Apache Mahout with Linux-based Hadoop in HDInsight (Generación de recomendaciones de película a través de Apache Mahout con Hadoop basado en Linux en HDInsight)](hdinsight-hadoop-mahout-linux-mac.md)


##<a name="learn"></a>Qué aprenderá

Mahout es una biblioteca de [aprendizaje automático][ml] para Apache Hadoop. Mahout contiene algoritmos para el procesamiento de datos, como filtrado, clasificación y agrupación en clústeres. En este artículo usará un motor de recomendaciones para generar recomendaciones de películas que se basan en películas que sus amigos han visto. También aprenderá a realizar clasificaciones con un bosque de decisiones. Esto le enseñará a:

* Cómo ejecutar trabajos de Mahout mediante Windows PowerShell

* Ejecutar trabajos de Mahout desde la línea de comandos de Hadoop

* Cómo instalar Mahout en clústeres de HDInsight 3.0 y HDInsight 2.0

	> [AZURE.NOTE] Mahout se proporciona con la versión 3.1 de HDInsight de los clústeres. Si va a usar una versión anterior de HDInsight, vea [Instalación de Mahout](#install) antes de continuar.

##requisitos previos

- **Un clúster de Hadoop basado en Windows en HDInsight**. Para obtener información sobre cómo crearlo, consulte [Hadoop tutorial: Get started with Hadoop and a Hive query in HDInsight on Windows (Tutorial de Hadoop: introducción a Hadoop y una consulta de Hive en in HDInsight en Windows)][getstarted].
- **Una estación de trabajo con Azure PowerShell**. Consulte [Instalar Azure PowerShell 1.0 y versiones posteriores](hdinsight-administer-use-powershell.md#install-azure-powershell-10-and-greater).


##<a name="recommendations"></a>Generación de recomendaciones mediante Windows PowerShell

> [AZURE.NOTE] Aunque el trabajo usado en esta sección funciona con Windows PowerShell, muchas de las clases proporcionadas con Mahout no funcionan actualmente con Windows PowerShell y se deben ejecutar mediante la línea de comandos de Hadoop. Para obtener una lista de las clases que no funcionan con Windows PowerShell, consulte la sección [Solución de problemas](#troubleshooting).
>
> Para ver un ejemplo del uso de la línea de comandos de Hadoop para ejecutar trabajos de Mahout, consulte [Clasificación de datos mediante la línea de comandos de Hadoop](#classify).

Una de las funciones que proporciona Mahout es un motor de recomendaciones. Este motor acepta datos en formato de `userID`, `itemId` y `prefValue` (la preferencia de los usuarios por el elemento). Mahout puede realizar entonces análisis de ocurrencias conjuntas para determinar: _los usuarios que tienen predilección por un elemento también la tienen por estos otros elementos_. Mahout determinará los usuarios con preferencias de elementos similares, lo que se puede usar para realizar recomendaciones.

A continuación se muestra un ejemplo muy sencillo que usa películas:

* __Ocurrencia conjunta__: a José, Alicia y Roberto les gusta _La Guerra de las galaxias_, _El imperio contraataca_ y _El retorno del Jedi_. Mahout determina que a los usuarios que les gusta alguna de estas películas también les gustan las otras dos.

* __Ocurrencia conjunta__: a Roberto y Alicia también les gusta _La amenaza fantasma_, _El ataque de los clones_ y _La venganza de los Sith_. Mahout determina que a los usuarios que les gustan las tres películas anteriores también les gustan estas tres.

* __Recomendación basada en similitud__: como a José le gustan las tres primeras películas, Mahout examina películas que a otros usuarios con preferencias similares les han gustado, pero que José no ha visto (gustado/valorado). En este caso, Mahout recomendaría _La amenaza fantasma_, _El ataque de los clones_ y _La venganza de los Sith_.

###Carga de los datos

Para su comodidad, [GroupLens Research][movielens] proporciona calificaciones de películas en un formato compatible con Mahout.

1. Descargue el archivo [MovieLens 100k][100k], que contiene 100.000 calificaciones de 1000 usuarios sobre 1700 películas.

2. Extraiga el archivo. Debe contener un directorio __ml-100k__ con muchos archivos de datos con el prefijo __u.__. El archivo que se analizará mediante Mahout es __u.data__. La estructura de datos de este archivo es `userID`, `movieID`, `userRating` y `timestamp`. A continuación se muestra un ejemplo de los datos:


		196	242	3	881250949
		186	302	3	891717742
		22	377	1	878887116
		244	51	2	880606923
		166	346	1	886397596


3. Cargue el archivo __u.data__ en __example/data/u.data__ en el clúster de HDInsight. El siguiente comando usa PowerShell para cargar los datos. Para ver otras formas de cargar archivos, vea [Carga de datos para trabajos de Hadoop en HDInsight][upload].

        # Put your cluster name below
        $clusterName="Your HDInsight cluster name"
        # Put the path to the u.data file below
        $fileToUpload="The path to the u.data file"
        
        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
            -ResourceGroupName $resourceGroup `
            | %{ $_.Key1 }
        
        #Create a storage content and upload the file
        $context = New-AzureStorageContext `
            -StorageAccountName $storageAccountName `
            -StorageAccountKey $storageAccountKey
            
        Set-AzureStorageBlobContent `
            -File $fileToUpload `
            -Blob "example/data/u.data" `
            -Container $container `
            -Context $context
    
    Esta acción carga el archivo __u.data__ en __example/data/u.data__ en el almacenamiento predeterminado del clúster. Después, puede tener acceso a estos datos usando el URI __wasb:///example/data/u.data__ de los trabajos de HDInsight.

###Ejecución del trabajo

Use el siguiente script de Windows PowerShell para ejecutar un trabajo que usa el motor de recomendaciones de Mahout con el archivo __u.data__ cargado anteriormente.

	# The HDInsight cluster name.
	$clusterName = "the cluster name"
    
    #Get HTTPS/Admin credentials for submitting the job later
    $creds = Get-Credential
    #Get the cluster info so we can get the resource group, storage, etc.
    $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroup = $clusterInfo.ResourceGroup
    $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
    $container=$clusterInfo.DefaultStorageContainer
    $storageAccountKey=Get-AzureRmStorageAccountKey `
        -Name $storageAccountName `
        -ResourceGroupName $resourceGroup `
        | %{ $_.Key1 }
            
    #Create a storage content and upload the file
    $context = New-AzureStorageContext `
        -StorageAccountName $storageAccountName `
        -StorageAccountKey $storageAccountKey
            
	# NOTE: The version number portion of the file path
	# may change in future versions of HDInsight.
	$jarFile =  "file:///C:/apps/dist/mahout-0.9.0.2.2.7.1-37/examples/target/mahout-examples-0.9.0.2.2.7.1-37-job.jar"
    #
	# If you are using an earlier version of HDInsight,
	# set $jarFile to the jar file you
	# uploaded.
	# For example,
	# $jarFile = "wasb:///example/jars/mahout-core-0.9-job.jar"

	# The arguments for this job
	# * input - the path to the data uploaded to HDInsight
	# * output - the path to store output data
	# * tempDir - the directory for temp files
	$jobArguments = "--similarityClassname", "recommenditembased", `
                    "-s", "SIMILARITY_COOCCURRENCE", `
	                "--input", "wasb:///example/data/u.data",
	                "--output", "wasb:///example/out",
	                "--tempDir", "wasb:///example/temp"

	# Create the job definition
	$jobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
	  -JarFile $jarFile `
	  -ClassName "org.apache.mahout.cf.taste.hadoop.item.RecommenderJob" `
	  -Arguments $jobArguments

	# Start the job
	$job = Start-AzureRmHDInsightJob `
        -ClusterName $clusterName `
        -JobDefinition $jobDefinition `
        -HttpCredential $creds

	# Wait on the job to complete
	Write-Host "Wait for the job to complete ..." -ForegroundColor Green
	Wait-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobId $job.JobId `
            -HttpCredential $creds
    # Download the output
    Get-AzureStorageBlobContent `
            -Blob example/out/part-r-00000 `
            -Container $container `
            -Destination output.txt `
            -Context $context
            
	# Write out any error information
	Write-Host "STDERR"
	Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $job.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

> [AZURE.NOTE] Los trabajos de Mahout no eliminan los datos temporales creados durante el procesamiento del trabajo. El parámetro `--tempDir` se especifica en el trabajo de ejemplo para aislar los archivos temporales en un directorio específico.

El trabajo de Mahout no devuelve la salida a STDOUT. En su lugar, lo almacena en el directorio de salida especificado como __part-r-00000__. El script descarga este archivo en __output.txt__ en el directorio actual de la estación de trabajo.

A continuación se muestra un ejemplo del contenido de este archivo:

	1	[234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
	2	[282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
	3	[284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
	4	[690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

La primera columna es `userID`. Los valores contenidos en '[' y ']' son `movieId`:`recommendationScore`.

###Visualización de la salida

Aunque puede que la salida generada esté bien para usarse en una aplicación, no es muy legible. Algunos de los restantes archivos que se han extraído previamente en la carpeta __ml-100k__ se pueden usar para resolver `movieId` como nombre de película, que es lo que hace el siguiente script de PowerShell.

	<#
	.SYNOPSIS
	    Displays recommendations for movies.
	.DESCRIPTION
	    Displays recommendations generated by Mahout
	    with HDInsight example in a human readable format.
	.EXAMPLE
	    .\Show-Recommendation -userId 4
	        -userDataFile "u.data"
	        -movieFile "u.item"
	        -recommendationFile "output.txt"
	#>

	[CmdletBinding(SupportsShouldProcess = $true)]
	param(
	    #The user ID
	    [Parameter(Mandatory = $true)]
	    [String]$userId,

	    [Parameter(Mandatory = $true)]
	    [String]$userDataFile,

	    [Parameter(Mandatory = $true)]
	    [String]$movieFile,

	    [Parameter(Mandatory = $true)]
	    [String]$recommendationFile
	)
	# Read movie ID & description into hash table
	Write-Host "Reading movies descriptions" -ForegroundColor Green
	$movieById = @{}
	foreach($line in Get-Content $movieFile)
	{
	    $tokens = $line.Split("|")
	    $movieById[$tokens[0]] = $tokens[1]
	}
	# Load movies user has already seen (rated)
	# into a hash table
	Write-Host "Reading rated movies" -ForegroundColor Green
	$ratedMovieIds = @{}
	foreach($line in Get-Content $userDataFile)
	{
	    $tokens = $line.Split("`t")
	    if($tokens[0] -eq $userId)
	    {
	        # Resolve the ID to the movie name
	        $ratedMovieIds[$movieById[$tokens[1]]] = $tokens[2]
	    }
	}
	# Read recommendations generated by Mahout
	Write-Host "Reading recommendations" -ForegroundColor Green
	$recommendations = @{}
	foreach($line in get-content $recommendationFile)
	{
	    $tokens = $line.Split("`t")
	    if($tokens[0] -eq $userId)
	    {
	        #Trim leading/treailing [] and split at ,
	        $movieIdAndScores = $tokens[1].TrimStart("[").TrimEnd("]").Split(",")
	        foreach($movieIdAndScore in $movieIdAndScores)
	        {
	            #Split at : and store title and score in a hash table
	            $idAndScore = $movieIdAndScore.Split(":")
	            $recommendations[$movieById[$idAndScore[0]]] = $idAndScore[1]
	        }
	        break
	    }
	}

	Write-Host "Rated movies" -ForegroundColor Green
	Write-Host "---------------------------" -ForegroundColor Green
	$ratedFormat = @{Expression={$_.Name};Label="Movie";Width=40}, `
	               @{Expression={$_.Value};Label="Rating"}
	$ratedMovieIds | format-table $ratedFormat
	Write-Host "---------------------------" -ForegroundColor Green

	write-host "Recommended movies" -ForegroundColor Green
	Write-Host "---------------------------" -ForegroundColor Green
	$recommendationFormat = @{Expression={$_.Name};Label="Movie";Width=40}, `
	                        @{Expression={$_.Value};Label="Score"}
	$recommendations | format-table $recommendationFormat

Para usar este script, debe haber extraído previamente la carpeta __ml - 100k__. A continuación se muestra un ejemplo de la ejecución del script:

	PS C:\> show-recommendation.ps1 -userId 4 -userDataFile .\ml-100k\u.data -movieFile .\ml-100k\u.item -recommendationFile .\output.txt

La salida debe ser similar a la siguiente:

	Reading movies descriptions
	Reading rated movies
	Reading recommendations
	Rated movies
	---------------------------
	Movie                                    Rating
	-----                                    ------
	Devil's Own, The (1997)                  1
	Alien: Resurrection (1997)               3
	187 (1997)                               2
	(lines ommitted)

	---------------------------
	Recommended movies
	---------------------------

	Movie                                    Score
	-----                                    -----
	Good Will Hunting (1997)                 4.6504064
	Swingers (1996)                          4.6862745
	Wings of the Dove, The (1997)            4.6666665
	People vs. Larry Flynt, The (1996)       4.834559
	Everyone Says I Love You (1996)          4.707071
	Secrets & Lies (1996)                    4.818182
	That Thing You Do! (1996)                4.75
	Grosse Pointe Blank (1997)               4.8235292
	Donnie Brasco (1997)                     4.6792455
	Lone Star (1996)                         4.7099237  

##<a name="classify"></a>Clasificación de datos mediante la línea de comandos de Hadoop

Uno de los métodos de clasificación disponibles con Mahout es compilar un [bosque aleatorio][forest]. Se trata de un proceso de varios pasos que supone el uso de datos de aprendizaje para generar un árbol de decisiones, que luego se usará para clasificar los datos. Para ello se utiliza la clase __org.apache.mahout.classifier.df.tools.Describe__ que se proporciona en Mahout. Actualmente se debe ejecutar con la línea de comandos de Hadoop.

###Carga de los datos

1. Descargue los siguientes archivos desde [The NSL-KDD Data Set](http://nsl.cs.unb.ca/NSL-KDD/).

  * [KDDTrain+.ARFF](http://nsl.cs.unb.ca/NSL-KDD/KDDTrain+.arff): el archivo del curso

  * [KDDTest+.ARFF](http://nsl.cs.unb.ca/NSL-KDD/KDDTest+.arff): los datos de prueba

2. Abra cada uno de los archivos y elimine las líneas de la parte superior que comienzan por '@'. Luego, guarde los archivos. Si no las elimina, recibirá mensajes de error cuando use estos datos con Mahout.

2. Cargue los archivos en __example/data__. Para ello, puede usar el siguiente script. Reemplace __CLUSTERNAME__ por el nombre del clúster de HDInsight. Reemplace FILENAME por el nombre del archivo que se va a cargar.

        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterName="CLUSTERNAME"
        $fileToUpload="FILENAME"
        $blobPath="example/data/FILENAME"
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
            -ResourceGroupName $resourceGroup `
            | %{ $_.Key1 }
        
        #Create a storage content and upload the file
        $context = New-AzureStorageContext `
            -StorageAccountName $storageAccountName `
            -StorageAccountKey $storageAccountKey
            
        Set-AzureStorageBlobContent `
            -File $fileToUpload `
            -Blob $blobPath `
            -Container $container `
            -Context $context

###Ejecución del trabajo

1. Este trabajo requiere la línea de comandos de Hadoop. Habilite el Escritorio remoto para el clúster de HDInsight y conéctese a él siguiendo las instrucciones dadas en [Conexión a los clústeres de HDInsight con RDP](hdinsight-administer-use-management-portal.md#rdp).

3. Después de conectar, use el icono de __línea de comandos de Hadoop__ para abrir la línea de comandos de Hadoop.

	![hadoop cli][hadoopcli]

3. Use el siguiente comando para generar el descriptor de archivos (__KDDTrain+.info__), que usa Mahout.

		hadoop jar "c:/apps/dist/mahout-0.9.0.2.2.7.1-37/examples/target/mahout-examples-0.9.0.2.2.7.1-37-job.jar" org.apache.mahout.classifier.df.tools.Describe -p "wasb:///example/data/KDDTrain+.arff" -f "wasb:///example/data/KDDTrain+.info" -d N 3 C 2 N C 4 N C 8 N 2 C 19 N L

	`N 3 C 2 N C 4 N C 8 N 2 C 19 N L` describe los atributos de los datos del archivo. Por ejemplo, L indica una etiqueta.

4. Compile un bosque de árboles de decisiones mediante el siguiente comando:

		hadoop jar c:/apps/dist/mahout-0.9.0.2.2.7.1-37/examples/target/mahout-examples-0.9.0.2.2.7.1-37-job.jar org.apache.mahout.classifier.df.mapreduce.BuildForest -Dmapred.max.split.size=1874231 -d wasb:///example/data/KDDTrain+.arff -ds wasb:///example/data/KDDTrain+.info -sl 5 -p -t 100 -o nsl-forest

    La salida de esta operación se almacena en el directorio __nsl-forest__, que está ubicado en el almacenamiento del clúster de HDInsight en __wasb://user/&lt;username>/nsl-forest/nsl-forest.seq. &lt;username> es el nombre de usuario utilizado para la sesión de Escritorio remoto. Este archivo no es legible para el ojo humano.

5. Para probar el bosque, clasifique el conjunto de datos __KDDTest+.arff__. Use el comando siguiente:

    	hadoop jar c:/apps/dist/mahout-0.9.0.2.2.7.1-37/examples/target/mahout-examples-0.9.0.2.2.7.1-37-job.jar org.apache.mahout.classifier.df.mapreduce.TestForest -i wasb:///example/data/KDDTest+.arff -ds wasb:///example/data/KDDTrain+.info -m nsl-forest -a -mr -o wasb:///example/data/predictions

    Este comando devuelve información resumida sobre el proceso de clasificación parecida a la siguiente.

	    14/07/02 14:29:28 INFO mapreduce.TestForest:

	    =======================================================
	    Summary
	    -------------------------------------------------------
	    Correctly Classified Instances          :      17560       77.8921%
	    Incorrectly Classified Instances        :       4984       22.1079%
	    Total Classified Instances              :      22544

	    =======================================================
	    Confusion Matrix
	    -------------------------------------------------------
	    a       b       <--Classified as
	    9437    274      |  9711        a     = normal
	    4710    8123     |  12833       b     = anomaly

	    =======================================================
	    Statistics
	    -------------------------------------------------------
	    Kappa                                       0.5728
	    Accuracy                                   77.8921%
	    Reliability                                53.4921%
	    Reliability (standard deviation)            0.4933

  Este trabajo también genera un archivo ubicado en __wasb:///example/data/predictions/KDDTest+.arff.out__. Sin embargo, este archivo no es legible por el ojo humano.

> [AZURE.NOTE] Los trabajos de Mahout no sobrescriben archivos. Si desea ejecutar estos trabajos de nuevo, debe eliminar los archivos creados por trabajos anteriores.

##<a name="troubleshooting"></a>Solución de problemas

###<a name="install"></a>Instalación de Mahout

Mahout se instala en clústeres de HDInsight 3.1, y se puede instalar manualmente en clústeres de HDInsight 3.0 o HDInsight 2.1 siguiendo estos pasos.

1. La versión de Mahout que utilice depende de la versión de HDInsight del clúster. Puede encontrar la versión de clúster en las propiedades del clúster en el portal de Azure.

  * __Para HDInsight 2.1__, puede descargar un archivo de almacenamiento Java (JAR) que contiene [Mahout 0.9](http://repo2.maven.org/maven2/org/apache/mahout/mahout-core/0.9/mahout-core-0.9-job.jar).

  * __Para HDInsight 3.0__, debe [compilar Mahout desde el origen][build] y especificar la versión de Hadoop proporcionada por HDInsight. Instale los requisitos previos que aparecen en la página de compilación, descargue el código fuente y use luego el siguiente comando para crear los archivos jar de Mahout:

			mvn -Dhadoop2.version=2.2.0 -DskipTests clean package

    	Una vez se complete la compilación, podrá encontrar el archivo JAR en __mahout\mrlegacy\target\mahout-mrlegacy-1.0-SNAPSHOT-job.jar__.

    	> [AZURE.NOTE] Cuando Mahout 1.0 esté disponible, podrá usar los paquetes de compilación previa con HDInsight 3.0.

2. Cargue el archivo jar en __example/jars__ en el almacenamiento predeterminado del clúster. Reemplace CLUSTERNAME en el siguiente script por el nombre de su clúster de HDInsight y FILENAME por la ruta de acceso al archivo __mahout-coure-0.9-job.jar__.

        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterName = "CLUSTERNAME"
        $fileToUpload = "FILENAME"
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
            -ResourceGroupName $resourceGroup `
            | %{ $_.Key1 }
        
        #Create a storage content and upload the file
        $context = New-AzureStorageContext `
            -StorageAccountName $storageAccountName `
            -StorageAccountKey $storageAccountKey
            
        Set-AzureStorageBlobContent `
            -File $fileToUpload `
            -Blob "example/jars/mahout-core-0.9-job.jar" `
            -Container $container `
            -Context $context

###No se pueden sobrescribir los archivos

Los trabajos de Mahout no limpian los archivos temporales creados durante el procesamiento. Además, los trabajos no sobrescriben los archivos de salida existentes.

Para evitar errores al ejecutar trabajos de Mahout, elimine los archivos temporales y de salida entre una ejecución y otra, o use nombres de directorio temporal y de salida exclusivos.

###No se encuentra el archivo JAR

Los clústeres de HDInsight 3.1 incluyen Mahout. La ruta y el nombre de archivo incluyen el número de la versión de Mahout instalada en el clúster. El script de ejemplo de Windows PowerShell de este tutorial usa una ruta que es válida a partir de noviembre de 2015, pero el número de la versión cambiará en futuras actualizaciones de HDInsight. Para determinar la ruta actual al archivo JAR de Mahout de su clúster, use el siguiente comando de Windows PowerShell y luego modifique el script para que haga referencia a la ruta de archivo devuelta:

	Use-AzureRmHDInsightCluster -ClusterName $clusterName
    #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
            -ResourceGroupName $resourceGroup `
            | %{ $_.Key1 }
    Invoke-AzureRmHDInsightHiveJob `
            -StatusFolder "wasb:///example/statusout" `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -Query '!${env:COMSPEC} /c dir /b /s ${env:MAHOUT_HOME}\examples\target*-job.jar'

###<a name="nopowershell"></a>Clases que no funcionan con Windows PowerShell

Los trabajos de Mahout que usan las siguientes clases devuelven diversos mensajes de error si se usan desde PowerShell.

* org.apache.mahout.utils.clustering.ClusterDumper
* org.apache.mahout.utils.SequenceFileDumper
* org.apache.mahout.utils.vectors.lucene.Driver
* org.apache.mahout.utils.vectors.arff.Driver
* org.apache.mahout.text.WikipediaToSequenceFile
* org.apache.mahout.clustering.streaming.tools.ResplitSequenceFiles
* org.apache.mahout.clustering.streaming.tools.ClusterQualitySummarizer
* org.apache.mahout.classifier.sgd.TrainLogistic
* org.apache.mahout.classifier.sgd.RunLogistic
* org.apache.mahout.classifier.sgd.TrainAdaptiveLogistic
* org.apache.mahout.classifier.sgd.ValidateAdaptiveLogistic
* org.apache.mahout.classifier.sgd.RunAdaptiveLogistic
* org.apache.mahout.classifier.sequencelearning.hmm.BaumWelchTrainer
* org.apache.mahout.classifier.sequencelearning.hmm.ViterbiEvaluator
* org.apache.mahout.classifier.sequencelearning.hmm.RandomSequenceGenerator
* org.apache.mahout.classifier.df.tools.Describe

Para ejecutar los trabajos que usan estas clases, conéctese al clúster de HDInsight y utilice la línea de comandos de Hadoop. Consulte [Clasificación de datos mediante la línea de comandos de Hadoop](#classify) para ver un ejemplo.

##Pasos siguientes

Ahora que ha aprendido a usar a Mahout, descubra otras formas de trabajar con datos en HDInsight:

* [Hive con HDInsight](hdinsight-use-hive.md)
* [Pig con HDInsight](hdinsight-use-pig.md)
* [MapReduce con HDInsight](hdinsight-use-mapreduce.md)

[build]: http://mahout.apache.org/developers/buildingmahout.html
[aps]: ../powershell-install-configure.md
[movielens]: http://grouplens.org/datasets/movielens/
[100k]: http://files.grouplens.org/datasets/movielens/ml-100k.zip
[getstarted]: hdinsight-hadoop-linux-tutorial-get-started.md
[upload]: hdinsight-upload-data.md
[ml]: http://en.wikipedia.org/wiki/Machine_learning
[forest]: http://en.wikipedia.org/wiki/Random_forest
[management]: https://manage.windowsazure.com/
[enableremote]: ./media/hdinsight-mahout/enableremote.png
[connect]: ./media/hdinsight-mahout/connect.png
[hadoopcli]: ./media/hdinsight-mahout/hadoopcli.png
[tools]: https://github.com/Blackmist/hdinsight-tools
 

<!---HONumber=AcomDC_0218_2016-->