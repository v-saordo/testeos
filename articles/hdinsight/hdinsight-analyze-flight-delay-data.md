<properties
	pageTitle="Análisis de datos de retraso de vuelos con Hadoop en HDInsight | Microsoft Azure"
	description="Obtenga información acerca de cómo usar un script de Windows PowerShell para crear el clúster de HDInsight, ejecutar un trabajo de Hive, ejecutar un trabajo de Sqool y eliminar el clúster."
	services="hdinsight"
	documentationCenter=""
	authors="mumian"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/01/2015"
	ms.author="jgao"/>

#Análisis de datos de retraso de vuelos con Hive en HDInsight

Hive ofrece un modo de ejecutar trabajos de MapReduce de Hadoop mediante un lenguaje de scripting de tipo SQL, denominado *[HiveQL][hadoop-hiveql]*, que se puede usar para resumir, consultar y analizar grandes volúmenes de datos.

> [AZURE.NOTE] Los pasos de este documento requieren un clúster de HDInsight basado en Windows. Para conocer el procedimiento que funciona con un clúster basado en Linux, vea [Análisis de datos de retraso de vuelos con Hive en HDInsight (Linux)](hdinsight-analyze-flight-delay-data-linux.md)

Una de las principales ventajas de HDInsight de Azure es la separación del almacenamiento de datos y el proceso. HDInsight usa el almacenamiento de blobs de Azure para el almacenamiento de datos. Un trabajo típico implica tres partes:

1. **Almacenar los datos en el almacenamiento de blobs de Azure.** Este puede ser un proceso continuo. Por ejemplo, los datos meteorológicos, datos de sensores, registros web y, en este caso, datos de retraso de vuelos se guardan en el almacenamiento de blobs de Azure.
2. **Ejecutar trabajos.** Cuando llega el momento de procesar los datos, ejecuta un script de Windows PowerShell (o una aplicación cliente) para crear un clúster de HDInsight, ejecutar los trabajos y eliminar el clúster. Los trabajos guardan los datos de salida en el almacenamiento de blobs de Azure. Los datos de salida se conservan incluso después de eliminar el clúster. De este modo, solo paga por lo que ha consumido.
3. **Recuperar la salida del almacenamiento de blobs** o, en este tutorial, exportar los datos a una base de datos SQL de Azure.

El diagrama siguiente muestra el escenario y la estructura de este tutorial:

![HDI.FlightDelays.flow][img-hdi-flightdelays-flow]

**Nota**: los números del diagrama corresponden a los títulos de sección. **M** significa el proceso principal. **A** representa el contenido del apéndice.

La parte principal del tutorial le muestra cómo usar un script de Windows PowerShell para realizar lo siguiente:

- Cree un clúster de HDInsight.
- Ejecute un trabajo de Hive en el clúster para calcular el retraso promedio en los aeropuertos. Los datos del retraso del vuelo se almacenan en una cuenta de almacenamiento de blobs de Azure.
- Ejecute un trabajo de Sqoop para exportar el resultado del trabajo de Hive a una base de datos SQL de Azure.
- Elimine el clúster de HDInsight.

En los apéndices, puede encontrar instrucciones para cargar los datos del retraso del vuelo, crear o cargar cadenas de consulta de Hive y preparar la base de datos SQL de Azure para el trabajo de Sqoop.

> [AZURE.NOTE] Los pasos descritos en este documento son específicos de los clústeres de HDInsight basados en Windows. Para conocer el procedimiento que funcionará con un clúster basado en Linux, vea [Análisis de datos de retraso de vuelos con Hive en HDInsight (Linux)](hdinsight-analyze-flight-delay-data-linux.md)

###Requisitos previos

Antes de empezar este tutorial, debe contar con lo siguiente:

- **Una suscripción de Azure**. Consulte [Obtención de una versión de evaluación gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Una estación de trabajo con Azure PowerShell**. Vea [Instalar Azure PowerShell 1.0 y versiones posteriores](hdinsight-administer-use-powershell.md#install-azure-powershell-10-and-greater).

**Archivos usados en este tutorial**

En este tutorial se usa el rendimiento puntual de los datos de vuelos de aerolíneas de [Research and Innovative Technology Administration, Bureau of Transportation Statistics o RITA][rita-website]. Una copia de los datos se han cargado en un contenedor de Almacenamiento de blobs de Azure con el permiso de acceso Blob público. Una parte del script de PowerShell copia los datos del contenedor de blobs público en el contenedor de blobs predeterminado del clúster. El script de HiveQL se copia también en el mismo contenedor de blobs. Si desea más información sobre cómo obtener o cargar los datos en su propia cuenta de Almacenamiento y cómo crear o cargar el archivo de scripts de HiveQL, consulte el [apéndice A](#appendix-a) y el [apéndice B](#appendix-b).

La siguiente tabla enumera los archivos utilizados en este tutorial:

<table border="1">
<tr><th>Archivos</th><th>Descripción</th></tr>
<tr><td>wasb://flightdelay@hditutorialdata.blob.core.windows.net/flightdelays.hql</td><td>Archivo de scripts de HiveQL que necesita el trabajo de Hive que va a ejecutar. Este script se ha cargado en una cuenta de almacenamiento de blobs de Azure con el público. El <a href="#appendix-b">apéndice B</a> tiene instrucciones para preparar y cargar este archivo a su propia cuenta de almacenamiento de blobs de Azure.</td></tr>
<tr><td>wasb://flightdelay@hditutorialdata.blob.core.windows.net/2013Data</td><td>Datos de entrada para los trabajos de Hive. Los datos se han cargado en una cuenta de almacenamiento de blobs de Azure con el acceso público. El <a href="#appendix-a">apéndice A</a> tiene instrucciones para obtener y cargar los datos en su propia cuenta de almacenamiento de blobs de Azure.</td></tr>
<tr><td>\tutorials\flightdelays\output</td><td>La ruta de acceso de salida para el trabajo de Hive. El contenedor predeterminado se usa para almacenar los datos de salida.</td></tr>
<tr><td>\tutorials\flightdelays\jobstatus</td><td>La carpeta de estado del trabajo de Hive en el contenedor predeterminado.</td></tr>
</table>


##Creación de clústeres y ejecución de trabajos de Hive/Sqoop

Hadoop MapReduce se está procesando por lotes. La forma más rentable de ejecutar un trabajo de Hive es crear un clúster para el trabajo y eliminar el trabajo una vez completado. El siguiente script cubre todo el proceso. Para obtener más información sobre la creación de un clúster de HDInsight y la ejecución de trabajos de Hive, consulte [Creación de clústeres de Hadoop en HDInsight][hdinsight-provision] y [Uso de Hive con HDInsight][hdinsight-use-hive].

**Para ejecutar las consultas de Hive con Azure PowerShell**

1. Cree una base de datos SQL y la tabla para el resultado del trabajo de Sqoop mediante las instrucciones del [Apéndice C](#appendix-c).
3. Abra Windows PowerShell ISE y ejecute el siguiente script:

		$subscriptionID = "<Azure Subscription ID>"
		$nameToken = "<Enter an Alias>" 
		
		###########################################
		# You must configure the follwing variables
		# for an existing Azure SQL Database
		###########################################
		$existingSqlDatabaseServerName = "<Azure SQL Database Server>"
		$existingSqlDatabaseLogin = "<Azure SQL Database Server Login>"
		$existingSqlDatabasePassword = "<Azure SQL Database Server login password>"
		$existingSqlDatabaseName = "<Azure SQL Database name>"
		
		$localFolder = "E:\Tutorials\Downloads" # A temp location for copying files. 
		$azcopyPath = "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy" # depends on the version, the folder can be different
		
		###########################################
		# (Optional) configure the following variables
		###########################################
		
		$namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

		$resourceGroupName = $namePrefix + "rg"
		$location = "EAST US 2"
		
		$HDInsightClusterName = $namePrefix + "hdi"
		$httpUserName = "admin"
		$httpPassword = "<Enter the Password>"
		
		$defaultStorageAccountName = $namePrefix + "store"
		$defaultBlobContainerName = $HDInsightClusterName # use the cluster name
		
		$existingSqlDatabaseTableName = "AvgDelays"
		$sqlDatabaseConnectionString = "jdbc:sqlserver://$existingSqlDatabaseServerName.database.windows.net;user=$existingSqlDatabaseLogin@$existingSqlDatabaseServerName;password=$existingSqlDatabaseLogin;database=$existingSqlDatabaseName"
		
		$hqlScriptFile = "/tutorials/flightdelays/flightdelays.hql" 
		
		$jobStatusFolder = "/tutorials/flightdelays/jobstatus"
		
		###########################################
		# Login
		###########################################
		try{
			$acct = Get-AzureRmSubscription
		}
		catch{
			Login-AzureRmAccount
		}
		Select-AzureRmSubscription -SubscriptionID $subscriptionID
		
		###########################################
		# Create a new HDInsight cluster
		###########################################
		
		# Create ARM group
		New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
		
		# Create the default storage account
		New-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName -Location $location -Type Standard_LRS
		
		# Create the default Blob container
		$defaultStorageAccountKey = Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName |  %{ $_.Key1 }
		$defaultStorageAccountContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey 
		New-AzureStorageContainer -Name $defaultBlobContainerName -Context $defaultStorageAccountContext 
		
		# Create the HDInsight cluster
		$pw = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
		$httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$pw)
		
		New-AzureRmHDInsightCluster `
			-ResourceGroupName $resourceGroupName `
			-ClusterName $HDInsightClusterName `
			-Location $location `
			-ClusterType Hadoop `
			-OSType Windows `
			-ClusterSizeInNodes 2 `
			-HttpCredential $httpCredential `
			-DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
			-DefaultStorageAccountKey $defaultStorageAccountKey `
			-DefaultStorageContainer $existingDefaultBlobContainerName 
		
		
		###########################################
		# Prepare the HiveQL script and source data
		###########################################
		
		# Create the temp location  
		New-Item -Path $localFolder -ItemType Directory -Force 
		
		# Download the sample file from Azure Blob storage
		$context = New-AzureStorageContext -StorageAccountName "hditutorialdata" -Anonymous
		$blobs = Get-AzureStorageBlob -Container "flightdelay" -Context $context
		#$blobs | Get-AzureStorageBlobContent -Context $context -Destination $localFolder
		
		# Upload data to default container
		
		$azcopycmd = "cmd.exe /C '$azcopyPath\azcopy.exe' /S /Source:'$localFolder' /Dest:'https://$defaultStorageAccountName.blob.core.windows.net/$defaultBlobContainerName/tutorials/flightdelays' /DestKey:$defaultStorageAccountKey"
		
		Invoke-Expression -Command:$azcopycmd
		
		###########################################
		# Submit the Hive job
		###########################################
		Use-AzureRmHDInsightCluster -ClusterName $HDInsightClusterName -HttpCredential $httpCredential
		$response = Invoke-AzureRmHDInsightHiveJob `
						-Files $hqlScriptFile `
						-DefaultContainer $defaultBlobContainerName `
						-DefaultStorageAccountName $defaultStorageAccountName `
						-DefaultStorageAccountKey $defaultStorageAccountKey `
						-StatusFolder $jobStatusFolder 
		
		write-Host $response
		
		
		###########################################
		# Submit the Sqoop job
		###########################################
		$exportDir = "wasb://$defaultBlobContainerName@$defaultStorageAccountName.blob.core.windows.net/tutorials/flightdelays/output"
		
		$sqoopDef = New-AzureRmHDInsightSqoopJobDefinition `
						-Command "export --connect $sqlDatabaseConnectionString --table $sqlDatabaseTableName --export-dir $exportDir --fields-terminated-by \001 "
		$sqoopJob = Start-AzureRmHDInsightJob `
						-ResourceGroupName $resourceGroupName `
						-ClusterName $hdinsightClusterName `
						-HttpCredential $httpCredential `
						-JobDefinition $sqoopDef #-Debug -Verbose
		
		Wait-AzureRmHDInsightJob `
			-ResourceGroupName $resourceGroupName `
			-ClusterName $HDInsightClusterName `
			-HttpCredential $httpCredential `
			-WaitTimeoutInSeconds 3600 `
			-Job $sqoopJob.JobId
		
		
		Get-AzureRmHDInsightJobOutput `
			-ResourceGroupName $resourceGroupName `
			-ClusterName $hdinsightClusterName `
			-HttpCredential $httpCredential `
			-DefaultContainer $existingDefaultBlobContainerName `
			-DefaultStorageAccountName $defaultStorageAccountName `
			-DefaultStorageAccountKey $defaultStorageAccountKey `
			-JobId $sqoopJob.JobId `
			-DisplayOutputType StandardError
		
		###########################################
		# Delete the cluster
		###########################################
		Remove-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName

6. Conéctese a la base de datos SQL y consulte los retrasos promedio de los vuelos por ciudad en la tabla AvgDelays:

	![HDI.FlightDelays.AvgDelays.Dataset][image-hdi-flightdelays-avgdelays-dataset]



---
##<a id="appendix-a"></a>Apéndice A: Carga de datos de retraso de vuelos en el almacenamiento de blobs de Azure
La carga del archivo de datos y los archivos de script de HiveQL (consulte el [Apéndice B](#appendix-b)) requiere planificación. La idea es almacenar los archivos de datos y el archivo HiveQL antes de crear un clúster de HDInsight y ejecutar el trabajo de Hive. Tiene dos opciones:

- **Use la misma cuenta de Almacenamiento de Azure que usará el clúster de HDInsight como sistema de archivos predeterminado.** Como el clúster de HDInsight tendrá la clave de acceso de la cuenta de Almacenamiento, no es necesario realizar cambios adicionales.
- **Use una cuenta de Almacenamiento de Azure diferente desde el sistema de archivos predeterminado del clúster de HDInsight.** Si este es el caso, debe modificar la parte de creación del script de PowerShell que se encuentra en [Creación de clústeres de HDInsight y ejecución de trabajos de Hive/Sqoop](#runjob) para vincular la cuenta de Almacenamiento como una cuenta de Almacenamiento adicional. Para instrucciones, vea [Creación de clústeres de Hadoop en HDInsight][hdinsight-provision]. De esta manera, el clúster de HDInsight conoce la clave de acceso de la cuenta de Almacenamiento.

>[AZURE.NOTE] La ruta de acceso al almacenamiento de blobs para el archivo de datos está codificada de forma rígida en el archivo de script de HiveQL. Debe actualizarse en consecuencia.

**Para descargar los datos de vuelo**

1. Diríjase a [Research and Innovative Technology Administration, Bureau of Transportation Statistics][rita-website].
2. En la página, seleccione los siguientes valores:

	<table border="1">
<tr><th>Nombre</th><th>Valor</th></tr>
<tr><td>Filter Year</td><td>2013 </td></tr>
<tr><td>Filter Period</td><td>January</td></tr>
<tr><td>Fields</td><td>*Year*, *FlightDate*, *UniqueCarrier*, *Carrier*, *FlightNum*, *OriginAirportID*, *Origin*, *OriginCityName*, *OriginState*, *DestAirportID*, *Dest*, *DestCityName*, *DestState*, *DepDelayMinutes*, *ArrDelay*, *ArrDelayMinutes*, *CarrierDelay*, *WeatherDelay*, *NASDelay*, *SecurityDelay*, *LateAircraftDelay* (borrar todos los demás campos)</td></tr>
</table>

3. Haga clic en **Descargar**.
4. Descomprima el archivo en la carpeta **C:\\Tutorials\\FlightDelay\\2013Data**. Cada archivo es un archivo CSV y tiene un tamaño aproximado de 60 GB.
5.	Cambie el nombre del archivo al nombre del mes que contiene los datos. Por ejemplo, el archivo que contiene los datos de enero se llamaría *January.csv*.
6. Repita los pasos 2 y 5 para descargar un archivo para cada uno de los 12 meses de 2013. Necesitará como mínimo un archivo para ejecutar el tutorial.  

**Para cargar los datos de retraso de vuelos en el almacenamiento de blobs de Azure**

1. Prepare los parámetros:

	<table border="1">
<tr><th>Nombre de la variable</th><th>Notas</th></tr>
<tr><td>$storageAccountName</td><td>La cuenta de Almacenamiento de Azure donde desea cargar los datos.</td></tr>
<tr><td>$blobContainerName</td><td>El contenedor de blobs en donde desea cargar los datos.</td></tr>
</table>
2. Abra Azure PowerShell ISE.
3. Pegue el siguiente script en el panel de scripts:

		[CmdletBinding()]
		Param(

		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure storage account name for creating a new HDInsight cluster. If the account doesn't exist, the script will create one.")]
		    [String]$storageAccountName,

		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure blob container name for creating a new HDInsight cluster. If not specified, the HDInsight cluster name will be used.")]
		    [String]$blobContainerName
		)

		#Region - Variables
		$localFolder = "C:\Tutorials\FlightDelay\2013Data"  # The source folder
		$destFolder = "tutorials/flightdelay/2013data"     #The blob name prefix for the files to be uploaded
		#EndRegion
		
		#Region - Connect to Azure subscription
		Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
		try{Get-AzureRmContext}
		catch{Login-AzureRmAccount}
		#EndRegion
		
		#Region - Validate user input
		Write-Host "`nValidating the Azure Storage account and the Blob container..." -ForegroundColor Green
		# Validate the Storage account
		if (-not (Get-AzureRmStorageAccount|Where-Object{$_.StorageAccountName -eq $storageAccountName}))
		{
			Write-Host "The storage account, $storageAccountName, doesn't exist." -ForegroundColor Red
			exit
		}
		else{
			$resourceGroupName = (Get-AzureRmStorageAccount|Where-Object{$_.StorageAccountName -eq $storageAccountName}).ResourceGroupName
		}
		
		# Validate the container
		$storageAccountKey = Get-AzureRmStorageAccountKey -StorageAccountName $storageAccountName -ResourceGroupName $resourceGroupName | %{$_.Key1}
		$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
		
		if (-not (Get-AzureStorageContainer -Context $storageContext |Where-Object{$_.Name -eq $blobContainerName}))
		{
			Write-Host "The Blob container, $blobContainerName, doesn't exist" -ForegroundColor Red
			Exit
		}
		#EngRegion
		
		#Region - Copy the file from local workstation to Azure Blob storage  
		if (test-path -Path $localFolder)
		{
			foreach ($item in Get-ChildItem -Path $localFolder){
				$fileName = "$localFolder\$item"
				$blobName = "$destFolder/$item"
		
				Write-Host "Copying $fileName to $blobName" -ForegroundColor Green
		
				Set-AzureStorageBlobContent -File $fileName -Container $blobContainerName -Blob $blobName -Context $storageContext
			}
		}
		else
		{
			Write-Host "The source folder on the workstation doesn't exist" -ForegroundColor Red
		}
		
		# List the uploaded files on HDInsight
		Get-AzureStorageBlob -Container $blobContainerName  -Context $storageContext -Prefix $destFolder
		#EndRegion




4. Presione **F5** para ejecutar el script.

Si decide usar un método diferente para cargar los archivos, asegúrese de que la ruta de acceso del archivo sea tutorials/flightdelay/data. La sintaxis para tener acceso a los archivos es:

	wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/tutorials/flightdelay/data

La ruta de acceso tutorials/flightdelay/data es la carpeta virtual que creó al cargar los archivos. Compruebe que haya 12 archivos, uno para cada mes.

>[AZURE.NOTE] Debe actualizar la consulta de Hive para leer desde la nueva ubicación.

> Debe configurar el permisos de acceso del contenedor para que sea público o enlazar la cuenta de Almacenamiento con el clúster de HDInsight. En caso contrario, la cadena de consulta de Hive no podrá acceder a los archivos de datos.

---
##<a id="appendix-b"></a>Apéndice B: Creación y carga de un script de HiveQL

Con Azure PowerShell, puede ejecutar varias instrucciones HiveQL a la vez, o empaquetar la instrucción de HiveQL en un archivo script. Esta sección muestra cómo crear un script de HiveQL y cargarlo en el almacenamiento de blobs de Azure usando PowerShell de Azure. Hive requiere que los scripts de HiveQL se almacenen en el almacenamiento de blobs de Azure.

El script de HiveQL realizará lo siguiente:

1. **Anular la tabla delays\_raw**, en caso de que la tabla ya exista.
2. **Crear la tabla externa de Hive delays\_raw** que apunta a la ubicación del almacenamiento de blobs con los archivos de retraso de los vuelos. Esta consulta especifica que los campos están delimitados por "," y que las líneas terminan en "\\n". Esto plantea un problema cuando los valores de campo contienen comas porque Hive no puede distinguir entre una coma que es un delimitador de campo y una que es parte de un valor de campo (que es el caso de los valores de campo de ORIGIN\_CITY\_NAME y DEST\_CITY\_NAME). Para solucionar este problema, la consulta crea columnas TEMP para contener datos que se dividen incorrectamente en columnas.  
3. **Anular la tabla de retrasos**, en caso de que la tabla ya exista.
4. **Crear la tabla de retrasos**. Es útil realizar una limpieza de los datos antes de realizar un procesamiento adicional. Esta consulta crea una tabla nueva, *delays*, a partir de la tabla delays\_raw. Tenga en cuenta que las columnas TEMP no se copian (como se mencionó anteriormente) y que la función **substring** se usa para quitar las comillas de los datos.
5. **Procesa el retraso promedio a causa del tiempo y agrupa los resultados por nombre de la ciudad.** También producirá los resultados en el almacenamiento de blobs. Tenga en cuenta que la consulta eliminará los apóstrofes de los datos y excluirá las filas donde el valor de **weather\_delay** es null. Esto es necesario porque Sqoop, que se usa más adelante en este tutorial, no trata estos valores correctamente de forma predeterminada.

Para obtener una lista completa de los comandos de HiveQL, consulte [Lenguaje de definición de datos de Hive][hadoop-hiveql]. Cada comando de HiveQL debe terminar con un punto y coma.

**Para crear un archivo de script de HiveQL**

1. Prepare los parámetros:

	<table border="1">
<tr><th>Nombre de la variable</th><th>Notas</th></tr>
<tr><td>$storageAccountName</td><td>La cuenta de Almacenamiento de Azure donde desea cargar el script de HiveQL.</td></tr>
<tr><td>$blobContainerName</td><td>El contenedor de blobs en donde desea cargar el script de HiveQL.</td></tr>
</table>
2. Abra Azure PowerShell ISE.

3. Copie y pegue el siguiente script en el panel de scripts:

		[CmdletBinding()]
		Param(

		    # Azure Blob storage variables
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure storage account name for creating a new HDInsight cluster. If the account doesn't exist, the script will create one.")]
		    [String]$storageAccountName,

		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure blob container name for creating a new HDInsight cluster. If not specified, the HDInsight cluster name will be used.")]
		    [String]$blobContainerName
		)

		#region - Define variables
		# Treat all errors as terminating
		$ErrorActionPreference = "Stop"
		
		# The HiveQL script file is exported as this file before it's uploaded to Blob storage
		$hqlLocalFileName = "e:\tutorials\flightdelay\flightdelays.hql"
		
		# The HiveQL script file will be uploaded to Blob storage as this blob name
		$hqlBlobName = "tutorials/flightdelay/flightdelays.hql"
		
		# These two constants are used by the HiveQL script file
		#$srcDataFolder = "tutorials/flightdelay/data"
		$dstDataFolder = "/tutorials/flightdelay/output"
		#endregion

		#Region - Connect to Azure subscription
		Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
		try{Get-AzureRmContext}
		catch{Login-AzureRmAccount}
		#EndRegion
		
		#Region - Validate user input
		Write-Host "`nValidating the Azure Storage account and the Blob container..." -ForegroundColor Green
		# Validate the Storage account
		if (-not (Get-AzureRmStorageAccount|Where-Object{$_.StorageAccountName -eq $storageAccountName}))
		{
			Write-Host "The storage account, $storageAccountName, doesn't exist." -ForegroundColor Red
			exit
		}
		else{
			$resourceGroupName = (Get-AzureRmStorageAccount|Where-Object{$_.StorageAccountName -eq $storageAccountName}).ResourceGroupName
		}
		
		# Validate the container
		$storageAccountKey = Get-AzureRmStorageAccountKey -StorageAccountName $storageAccountName -ResourceGroupName $resourceGroupName | %{$_.Key1}
		$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
		
		if (-not (Get-AzureStorageContainer -Context $storageContext |Where-Object{$_.Name -eq $blobContainerName}))
		{
			Write-Host "The Blob container, $blobContainerName, doesn't exist" -ForegroundColor Red
			Exit
		}
		#EngRegion
		
		#region - Validate the file and file path
		
		# Check if a file with the same file name already exists on the workstation
		Write-Host "`nvalidating the folder structure on the workstation for saving the HQL script file ..."  -ForegroundColor Green
		if (test-path $hqlLocalFileName){
		
			$isDelete = Read-Host 'The file, ' $hqlLocalFileName ', exists.  Do you want to overwirte it? (Y/N)'
		
			if ($isDelete.ToLower() -ne "y")
			{
				Exit
			}
		}
		
		# Create the folder if it doesn't exist
		$folder = split-path $hqlLocalFileName
		if (-not (test-path $folder))
		{
			Write-Host "`nCreating folder, $folder ..." -ForegroundColor Green
		
			new-item $folder -ItemType directory  
		}
		#end region
		
		#region - Write the Hive script into a local file
		Write-Host "`nWriting the Hive script into a file on your workstation ..." `
					-ForegroundColor Green
		
		$hqlDropDelaysRaw = "DROP TABLE delays_raw;"
		
		$hqlCreateDelaysRaw = "CREATE EXTERNAL TABLE delays_raw (" +
				"YEAR string, " +
				"FL_DATE string, " +
				"UNIQUE_CARRIER string, " +
				"CARRIER string, " +
				"FL_NUM string, " +
				"ORIGIN_AIRPORT_ID string, " +
				"ORIGIN string, " +
				"ORIGIN_CITY_NAME string, " +
				"ORIGIN_CITY_NAME_TEMP string, " +
				"ORIGIN_STATE_ABR string, " +
				"DEST_AIRPORT_ID string, " +
				"DEST string, " +
				"DEST_CITY_NAME string, " +
				"DEST_CITY_NAME_TEMP string, " +
				"DEST_STATE_ABR string, " +
				"DEP_DELAY_NEW float, " +
				"ARR_DELAY_NEW float, " +
				"CARRIER_DELAY float, " +
				"WEATHER_DELAY float, " +
				"NAS_DELAY float, " +
				"SECURITY_DELAY float, " +
				"LATE_AIRCRAFT_DELAY float) " +
			"ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' " +
			"LINES TERMINATED BY '\n' " +
			"STORED AS TEXTFILE " +
			"LOCATION 'wasb://flightdelay@hditutorialdata.blob.core.windows.net/2013Data';"
		
		$hqlDropDelays = "DROP TABLE delays;"
		
		$hqlCreateDelays = "CREATE TABLE delays AS " +
			"SELECT YEAR AS year, " +
				"FL_DATE AS flight_date, " +
				"substring(UNIQUE_CARRIER, 2, length(UNIQUE_CARRIER) -1) AS unique_carrier, " +
				"substring(CARRIER, 2, length(CARRIER) -1) AS carrier, " +
				"substring(FL_NUM, 2, length(FL_NUM) -1) AS flight_num, " +
				"ORIGIN_AIRPORT_ID AS origin_airport_id, " +
				"substring(ORIGIN, 2, length(ORIGIN) -1) AS origin_airport_code, " +
				"substring(ORIGIN_CITY_NAME, 2) AS origin_city_name, " +
				"substring(ORIGIN_STATE_ABR, 2, length(ORIGIN_STATE_ABR) -1)  AS origin_state_abr, " +
				"DEST_AIRPORT_ID AS dest_airport_id, " +
				"substring(DEST, 2, length(DEST) -1) AS dest_airport_code, " +
				"substring(DEST_CITY_NAME,2) AS dest_city_name, " +
				"substring(DEST_STATE_ABR, 2, length(DEST_STATE_ABR) -1) AS dest_state_abr, " +
				"DEP_DELAY_NEW AS dep_delay_new, " +
				"ARR_DELAY_NEW AS arr_delay_new, " +
				"CARRIER_DELAY AS carrier_delay, " +
				"WEATHER_DELAY AS weather_delay, " +
				"NAS_DELAY AS nas_delay, " +
				"SECURITY_DELAY AS security_delay, " +
				"LATE_AIRCRAFT_DELAY AS late_aircraft_delay " +
			"FROM delays_raw;"
		
		$hqlInsertLocal = "INSERT OVERWRITE DIRECTORY '$dstDataFolder' " +
			"SELECT regexp_replace(origin_city_name, '''', ''), " +
				"avg(weather_delay) " +
			"FROM delays " +
			"WHERE weather_delay IS NOT NULL " +
			"GROUP BY origin_city_name;"
		
		$hqlScript = $hqlDropDelaysRaw + $hqlCreateDelaysRaw + $hqlDropDelays + $hqlCreateDelays + $hqlInsertLocal
		
		$hqlScript | Out-File $hqlLocalFileName -Encoding ascii -Force
		#endregion
		
		#region - Upload the Hive script to the default Blob container
		Write-Host "`nUploading the Hive script to the default Blob container ..." -ForegroundColor Green
		
		# Create a storage context object
		$storageAccountKey = Get-AzureRmStorageAccountKey -StorageAccountName $storageAccountName -ResourceGroupName $resourceGroupName | %{$_.Key1}
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
		
		# Upload the file from local workstation to Blob storage
		Set-AzureStorageBlobContent -File $hqlLocalFileName -Container $blobContainerName -Blob $hqlBlobName -Context $destContext
		#endregion
		
		Write-host "`nEnd of the PowerShell script" -ForegroundColor Green

	Estas son las variables que se usan en el script:

	- **$hqlLocalFileName**: el script guarda el archivo de script de HiveQL en modo local antes de cargarlo en el almacenamiento de blobs. Este es el nombre del archivo. El valor predeterminado es <u>C:\\tutorials\\flightdelay\\flightdelays.hql</u>.
	- **$hqlBlobName**: este es el nombre del blob del archivo de script de HiveQL que se ha usado en el almacenamiento de blobs de Azure. El valor predeterminado es tutorials/flightdelay/flightdelays.hql. Puesto que el archivo se escribirá directamente en el almacenamiento de blobs de Azure, NO se pone "/" delante del nombre de blob. Si quiere tener acceso al archivo desde el almacenamiento de blobs, debe agregar "/" delante del nombre de archivo.
	- **$srcDataFolder** y **$dstDataFolder** - = "tutorials/flightdelay/data" = "tutorials/flightdelay/output"


---
##<a id="appendix-c"></a>Apéndice C: Preparación de la base de datos SQL de Azure para el resultado del trabajo de Sqoop
**Para preparar la base de datos SQL (combine esto con el script de Sqoop)**

1. Prepare los parámetros:

	<table border="1">
<tr><th>Nombre de la variable</th><th>Notas</th></tr>
<tr><td>$sqlDatabaseServerName</td><td>El nombre del servidor de Base de datos SQL de Azure. No escriba nada para crear un nuevo servidor.</td></tr>
<tr><td>$sqlDatabaseUsername</td><td>El nombre de inicio de sesión del servidor de Base de datos SQL de Azure. Si $sqlDatabaseServerName es un servidor existente, se utilizan el nombre y la contraseña de inicio de sesión para autenticarse con el servidor. En caso contrario, se utilizan para crear un nuevo servidor.</td></tr>
<tr><td>$sqlDatabasePassword</td><td>La contraseña de inicio de sesión del servidor de Base de datos SQL de Azure.</td></tr>
<tr><td>$sqlDatabaseLocation</td><td>Este valor se usa solo cuando se crea un nuevo servidor de base de datos de Azure.</td></tr>
<tr><td>$sqlDatabaseName</td><td>La Base de datos SQL usada para crear la tabla AvgDelays para el trabajo de Sqoop. Si se deja en blanco, se creará una base de datos denominada HDISqoop. El nombre de tabla para el resultado del trabajo de Sqoop es AvgDelays. </td></tr>
</table>
2. Abra Azure PowerShell ISE.
3. Copie y pegue el siguiente script en el panel de scripts:

		[CmdletBinding()]
		Param(
		
			# Azure Resource group variables
			[Parameter(Mandatory=$True,
					HelpMessage="Enter the Azure resource group name. It will be created if it doesn't exist.")]
			[String]$resourceGroupName,
		
			# SQL database server variables
			[Parameter(Mandatory=$True,
					HelpMessage="Enter the Azure SQL Database Server Name. It will be created if it doesn't exist.")]
			[String]$sqlDatabaseServer, 
		
			[Parameter(Mandatory=$True,
					HelpMessage="Enter the Azure SQL Database admin user.")]
			[String]$sqlDatabaseLogin,
		
			[Parameter(Mandatory=$True,
					HelpMessage="Enter the Azure SQL Database admin user password.")]
			[String]$sqlDatabasePassword,
		
			[Parameter(Mandatory=$True,
					HelpMessage="Enter the region to create the Database in.")]
			[String]$sqlDatabaseLocation,   #For example, West US.
		
			# SQL database variables
			[Parameter(Mandatory=$True,
					HelpMessage="Enter the database name. It will be created if it doesn't exist.")]
			[String]$sqlDatabaseName # specify the database name if you have one created. Otherwise use "" to have the script create one for you.
		)
		
		# Treat all errors as terminating
		$ErrorActionPreference = "Stop"
		
		#region - Constants and variables
		
		# IP address REST service used for retrieving external IP address and creating firewall rules
		[String]$ipAddressRestService = "http://bot.whatismyipaddress.com"
		[String]$fireWallRuleName = "FlightDelay"
		
		# SQL database variables
		[String]$sqlDatabaseMaxSizeGB = 10
		
		#SQL query string for creating AvgDelays table
		[String]$sqlDatabaseTableName = "AvgDelays"
		[String]$sqlCreateAvgDelaysTable = " CREATE TABLE [dbo].[$sqlDatabaseTableName](
					[origin_city_name] [nvarchar](50) NOT NULL,
					[weather_delay] float,
				CONSTRAINT [PK_$sqlDatabaseTableName] PRIMARY KEY CLUSTERED
				(
					[origin_city_name] ASC
				)
				)"
		#endregion
		
		#Region - Connect to Azure subscription
		Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
		try{Get-AzureRmContext}
		catch{Login-AzureRmAccount}
		#EndRegion
		
		#region - Create and validate Azure resouce group
		try{
			Get-AzureRmResourceGroup -Name $resourceGroupName
		}
		catch{
			New-AzureRmResourceGroup -Name $resourceGroupName -Location $sqlDatabaseLocation
		}
		
		#EndRegion
		
		#region - Create and validate Azure SQL database server
		try{
			Get-AzureRmSqlServer -ServerName $sqlDatabaseServer -ResourceGroupName $resourceGroupName}
		catch{
			Write-Host "`nCreating SQL Database server ..."  -ForegroundColor Green
		
			$sqlDatabasePW = ConvertTo-SecureString -String $sqlDatabasePassword -AsPlainText -Force
			$credential = New-Object System.Management.Automation.PSCredential($sqlDatabaseLogin,$sqlDatabasePW)
		
			$sqlDatabaseServer = (New-AzureRmSqlServer -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -SqlAdministratorCredentials $credential -Location $sqlDatabaseLocation).ServerName
			Write-Host "`tThe new SQL database server name is $sqlDatabaseServer." -ForegroundColor Cyan
		
			Write-Host "`nCreating firewall rule, $fireWallRuleName ..." -ForegroundColor Green
			$workstationIPAddress = Invoke-RestMethod $ipAddressRestService
			New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -FirewallRuleName "$fireWallRuleName-workstation" -StartIpAddress $workstationIPAddress -EndIpAddress $workstationIPAddress
		
			#To allow other Azure services to access the server add a firewall rule and set both the StartIpAddress and EndIpAddress to 0.0.0.0. Note that this allows Azure traffic from any Azure subscription to access the server.
			New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -FirewallRuleName "$fireWallRuleName-Azureservices" -StartIpAddress "0.0.0.0" -EndIpAddress "0.0.0.0"
		}
		
		#endregion
		
		#region - Create and validate Azure SQL database
		
		try {
			Get-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -DatabaseName $sqlDatabaseName
		}
		catch {
			Write-Host "`nCreating SQL Database, $sqlDatabaseName ..."  -ForegroundColor Green
			New-AzureRMSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -DatabaseName $sqlDatabaseName -Edition "Standard" -RequestedServiceObjectiveName "S1"
		}
		
		#endregion
		
		#region -  Execute an SQL command to create the AvgDelays table
		
		Write-Host "`nCreating SQL Database table ..."  -ForegroundColor Green
		$conn = New-Object System.Data.SqlClient.SqlConnection
		$conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"
		$conn.open()
		$cmd = New-Object System.Data.SqlClient.SqlCommand
		$cmd.connection = $conn
		$cmd.commandtext = $sqlCreateAvgDelaysTable
		$cmd.executenonquery()
		
		$conn.close()
		
		Write-host "`nEnd of the PowerShell script" -ForegroundColor Green

	>[AZURE.NOTE] El script usa un servicio de transferencia de estado representacional (REST), http://bot.whatismyipaddress.com, para recuperar la dirección IP externa. Esta dirección IP se usa para crear una regla de firewall para el servidor de Base de datos SQL.

	Estas son algunas de las variables que se usan en el script:

	- **$ipAddressRestService**: el valor predeterminado es http://bot.whatismyipaddress.com. Es un Es un servicio de REST de direcciones IP públicas para obtener la dirección IP externa. Puede usar los servicios que desee. La dirección IP externa recuperada con el servicio se usará para crear una regla de firewall para el servidor de Base de datos SQL de Azure, de forma que pueda acceder a la base de datos desde su estación de trabajo (usando el script de Windows PowerShell).
	- **$fireWallRuleName**: este es el nombre de la regla de firewall para el servidor de Base de datos de SQL Azure. El nombre predeterminado es <u>FlightDelay</u>. Puede cambiarlo si lo desea.
	- **$sqlDatabaseMaxSizeGB**: este valor se usa solo cuando se crea un nuevo servidor de Base de datos SQL de Azure. El valor predeterminado es 10 GB. 10 GB es suficiente para este tutorial.
	- **$sqlDatabaseName**: este valor se usa solo cuando se crea una nueva Base de datos SQL de Azure. El valor predeterminado es HDISqoop. Si cambia el nombre, debe actualizar el script de PowerShell de Sqoop según corresponda.

4. Presione **F5** para ejecutar el script.
5. Valide la salida del script. Asegúrese de que el script se ejecutó correctamente.

##<a id="nextsteps"></a> Pasos siguientes
Ahora sabe cómo cargar un archivo en el almacenamiento de blobs de Azure, cómo rellenar una tabla de Hive con los datos del almacenamiento de blobs de Azure, cómo ejecutar consultas de Hive y cómo usar Sqoop para exportar datos de HDFS a la Base de datos SQL de Azure. Para obtener más información, consulte los artículos siguientes:

* [Introducción a HDInsight][hdinsight-get-started]
* [Uso de Hive con HDInsight][hdinsight-use-hive]
* [Uso de Oozie con HDInsight][hdinsight-use-oozie]
* [Uso de Sqoop con HDInsight][hdinsight-use-sqoop]
* [Uso de Pig con HDInsight][hdinsight-use-pig]
* [Desarrollo de programas MapReduce de Java para HDInsight][hdinsight-develop-mapreduce]
* [Desarrollo de programas de streaming de Hadoop C# para HDInsight][hdinsight-develop-streaming]



[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[rita-website]: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
[powershell-install-configure]: powershell-install-configure.md

[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-develop-streaming]: hdinsight-hadoop-develop-deploy-streaming-jobs.md
[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce.md

[hadoop-hiveql]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL
[hadoop-shell-commands]: http://hadoop.apache.org/docs/r0.18.3/hdfs_shell.html

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx

[image-hdi-flightdelays-avgdelays-dataset]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.AvgDelays.DataSet.png
[img-hdi-flightdelays-run-hive-job-output]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.RunHiveJob.Output.png
[img-hdi-flightdelays-flow]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.Flow.png

<!---HONumber=AcomDC_0218_2016-->