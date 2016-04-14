<properties 
   pageTitle="Introducción a Análisis de Azure Data Lake mediante Azure PowerShell | Azure" 
   description="Aprenda a usar Azure PowerShell para crear una cuenta de Almacén de Data Lake, crear un trabajo de Análisis de Data Lake mediante U-SQL y enviar el trabajo." 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="12/01/2015"
   ms.author="jgao"/>

# Tutorial: Introducción a Análisis de Azure Data Lake mediante Azure PowerShell

[AZURE.INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Aprenda a usar Azure PowerShell para crear cuentas de Análisis de Azure Data Lake, definir trabajos de Análisis de Data Lake en [U-SQL](data-lake-analytics-u-sql-get-started.md) y enviar trabajos a cuentas de Análisis de Data Lake. Para obtener más información acerca de Análisis de Data Lake, consulte [Información general sobre Análisis de Azure Data Lake](data-lake-analytics-overview.md).

En este tutorial, desarrollará un trabajo que lee un archivo de valores separados por tabulaciones (TSV) y lo convierte en un otro de valores separados por comas (CSV). Para recorrer el mismo tutorial con otras herramientas compatibles, haga clic en las pestañas situadas en la parte superior de esta sección.

**Proceso básico de Análisis de Data Lake:**

![Diagrama de flujo de proceso de Análisis de Azure Data Lake](./media/data-lake-analytics-get-started-portal/data-lake-analytics-process.png)

1. Cree una cuenta de Análisis de Data Lake.
2. Prepare los datos de origen. Los trabajos de Análisis de Data Lake pueden leer datos desde cuentas del Almacén de Azure Data Lake o desde cuentas de almacenamiento de blobs de Azure.   
3. Desarrolle un script U-SQL.
4. Envíe un trabajo (script U-SQL) a la cuenta de Análisis de Data Lake. El trabajo lee desde el origen de datos, procesa los datos como se indica en el script U-SQL y después guarda la salida en una cuenta de Almacén de Data Lake o de almacenamiento de blobs.


**Requisitos previos**

Antes de empezar este tutorial, debe contar con lo siguiente:

- **Una suscripción de Azure**. Consulte [Obtención de una versión de evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/).
- **Una estación de trabajo con Azure PowerShell**. Vea la sección Requisitos previos de [Uso de Azure PowerShell con Administrador de recursos de Azure](powershell-azure-resource-manager.md#prerequisites).
	
##Creación de una cuenta de Análisis de Data Lake

Debe tener una cuenta de Análisis de Data Lake para poder ejecutar trabajos. Para crear una cuenta de Análisis de Data Lake, debe especificar lo siguiente:

- **Grupo de recursos de Azure**: se debe crear una cuenta de Análisis de Data Lake dentro de un grupo de recursos de Azure. El [Administrador de recursos de Azure](../resource-group-overview.md) permite trabajar con los recursos de la aplicación como un grupo. Puede implementar, actualizar o eliminar todos los recursos de la aplicación en una operación única y coordinada.  

	Para enumerar los grupos de recursos para la suscripción:
    
    	Get-AzureRmResourceGroup
    
	Para crear un nuevo grupo de recursos:

    	New-AzureRmResourceGroup `
			-Name "<Your resource group name>" `
			-Location "<Azure Data Center>" # For example, "East US 2"

- **Nombre de la cuenta de Análisis de Data Lake**
- **Ubicación**: uno de los centros de datos de Azure que admite Análisis de Data Lake.
- **Cuenta predeterminada de Data Lake**: cada cuenta de Análisis de Data Lake tiene una cuenta predeterminada de Data Lake.

	Para crear una nueva cuenta de Data Lake:

	    New-AzureRmDataLakeStoreAccount `
	        -ResourceGroupName "<Your Azure resource group name>" `
	        -Name "<Your Data Lake account name>" `
	        -Location "<Azure Data Center>"  # For example, "East US 2"

	> [AZURE.NOTE] El nombre de cuenta de Data Lake solo debe contener letras minúsculas y números.



**Para crear una cuenta de Análisis de Data Lake**

1. Abra PowerShell ISE en la estación de trabajo de Windows.
2. Ejecute el siguiente script:

		$resourceGroupName = "<ResourceGroupName>"
		$dataLakeStoreName = "<DataLakeAccountName>"
		$dataLakeAnalyticsName = "<DataLakeAnalyticsAccountName>"
		$location = "East US 2"
		
		Write-Host "Create a resource group ..." -ForegroundColor Green
		New-AzureRmResourceGroup `
			-Name  $resourceGroupName `
			-Location $location
		
		Write-Host "Create a Data Lake account ..."  -ForegroundColor Green
		New-AzureRmDataLakeStoreAccount `
			-ResourceGroupName $resourceGroupName `
			-Name $dataLakeStoreName `
			-Location $location 
		
		Write-Host "Create a Data Lake Analytics account ..."  -ForegroundColor Green
		New-AzureRmDataLakeAnalyticsAccount `
			-Name $dataLakeAnalyticsName `
			-ResourceGroupName $resourceGroupName `
			-Location $location `
			-DefaultDataLake $dataLakeStoreName
		
		Write-Host "The newly created Data Lake Analytics account ..."  -ForegroundColor Green
		Get-AzureRmDataLakeAnalyticsAccount `
			-ResourceGroupName $resourceGroupName `
			-Name $dataLakeAnalyticsName  

##Carga de datos a Data Lake

En este tutorial, va a procesar algunos registros de búsqueda. El registro de búsqueda se puede almacenar en el Almacén de Data Lake o en el almacenamiento de blobs de Azure.

Se ha copiado un archivo de registro de búsqueda de ejemplo en un contenedor de Blob de Azure público. Use el siguiente script de PowerShell para descargar el archivo en la estación de trabajo y después cargue el archivo a la cuenta predeterminada de Almacén de Data Lake de la cuenta de Análisis de Data Lake.

	$dataLakeStoreName = "<The default Data Lake Store account name>"
	
	$localFolder = "C:\Tutorials\Downloads" # A temp location for the file. 
	$storageAccount = "adltutorials"  # Don't modify this value.
	$container = "adls-sample-data"  #Don't modify this value.

	# Create the temp location	
	New-Item -Path $localFolder -ItemType Directory -Force 

	# Download the sample file from Azure Blob storage
	$context = New-AzureStorageContext -StorageAccountName $storageAccount -Anonymous
	$blobs = Azure\Get-AzureStorageBlob -Container $container -Context $context
	$blobs | Get-AzureStorageBlobContent -Context $context -Destination $localFolder

	# Upload the file to the default Data Lake Store account	
	Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $localFolder"SearchLog.tsv" -Destination "/Samples/Data/SearchLog.tsv"

El siguiente script de PowerShell muestra cómo obtener el nombre predeterminado de Almacén de Data Lake para una cuenta de Análisis de Data Lake:


	$resourceGroupName = "<ResourceGroupName>"
	$dataLakeAnalyticsName = "<DataLakeAnalyticsAccountName>"
	$dataLakeStoreName = (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticName).Properties.DefaultDataLakeAccount

>[AZURE.NOTE] El Portal de Azure proporciona una interfaz de usuario para copiar los archivos de datos de ejemplo en la cuenta predeterminada de Almacén de Data Lake. Para obtener instrucciones, consulte [Introducción a Análisis de Azure Data Lake mediante el Portal de Azure](data-lake-analytics-get-started-portal.md#upload-data-to-the-default-data-lake-store-account).

Análisis de Data Lake también puede acceder al almacenamiento de blobs de Azure. Para cargar datos al almacenamiento de blobs de Azure, consulte [Uso de Azure PowerShell con Almacenamiento de Azure](../storage/storage-powershell-guide-full.md).

##Envío de trabajos de Análisis de Data Lake

Los trabajos de Análisis de Data Lake se escriben en el lenguaje U-SQL. Para obtener más información sobre U-SQL, consulte [Introducción al lenguaje U-SQL](data-lake-analytics-u-sql-get-started.md) y [Referencia sobre el lenguaje U-SQL](http://go.microsoft.com/fwlink/?LinkId=691348).

**Para crear un script de trabajo de Análisis de Data Lake**

- Cree un archivo de texto con el siguiente script U-SQL y guarde el archivo de texto en la estación de trabajo:

        @searchlog =
            EXTRACT UserId          int,
                    Start           DateTime,
                    Region          string,
                    Query           string,
                    Duration        int?,
                    Urls            string,
                    ClickedUrls     string
            FROM "/Samples/Data/SearchLog.tsv"
            USING Extractors.Tsv();
        
        OUTPUT @searchlog   
            TO "/Output/SearchLog-from-Data-Lake.csv"
        USING Outputters.Csv();

	Este script U-SQL lee el archivo de datos de origen mediante **Extractors.Tsv()** y después crea un archivo csv mediante **Outputters.Csv()**.
    
    No modifique ninguna de las dos rutas a menos que copie el archivo de origen en una ubicación diferente. Análisis de Data Lake creará la carpeta de salida si no existe.
	
	Es más fácil usar rutas de acceso relativas para los archivos almacenados en cuentas predeterminadas de Data Lake. También puede usar rutas de acceso absolutas. Por ejemplo:
    
        adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
        
    Debe usar rutas de acceso absolutas para acceder a los archivos de cuentas de almacenamiento vinculadas. La sintaxis de los archivos almacenados en la cuenta de Almacenamiento de Azure vinculada es:
    
        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

    >[AZURE.NOTE] Los contenedores de blobs de Azure con permisos de acceso de contenedores públicos o blobs públicos no se admiten actualmente.
    
	
**Para enviar el trabajo**

1. Abra PowerShell ISE en la estación de trabajo de Windows.
2. Ejecute el siguiente script:

		$dataLakeAnalyticsName = "<DataLakeAnalyticsAccountName>"
		$usqlScript = "c:\tutorials\data-lake-analytics\copyFile.usql"
		
		Submit-AzureRmDataLakeAnalyticsJob -Name "convertTSVtoCSV" -AccountName $dataLakeAnalyticsName –ScriptPath $usqlScript 
		                
		While (($t = Get-AzureRmDataLakeAnalyticsJob -AccountName $dataLakeAnalyticsName -JobId $job.JobId).State -ne "Ended"){
			Write-Host "Job status: "$t.State"..."
			Start-Sleep -seconds 5
		}
		
		Get-AzureRmDataLakeAnalyticsJob -AccountName $dataLakeAnalyticsName -JobId $job.JobId

	En el script, el archivo de script U-SQL se almacena en c:\\tutorials\\data-lake-analytics\\copyFile.usql. Actualice la ruta de acceso de archivo en consecuencia.
 
Después de finalizar el trabajo, puede usar los siguientes cmdlets para mostrar el archivo y descargarlo:
	
	$resourceGroupName = "<Resource Group Name>"
	$dataLakeAnalyticName = "<Data Lake Analytic Account Name>"
	$destFile = "C:\tutorials\data-lake-analytics\SearchLog-from-Data-Lake.csv"
	
	$dataLakeStoreName = (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticName).Properties.DefaultDataLakeAccount
	
	Get-AzureRmDataLakeStoreChildItem -AccountName $dataLakeStoreName -path "/Output"
	
	Export-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "/Output/SearchLog-from-Data-Lake.csv" -Destination $destFile

## Consulte también

- Para ver el mismo tutorial con otras herramientas, haga clic en los selectores de pestañas en la parte superior de la página.
- Para ver una consulta más compleja, consulte [Análisis de registros de sitios web mediante Análisis de Azure Data Lake](data-lake-analytics-analyze-weblogs.md).
- Para empezar a desarrollar aplicaciones con U-SQL, consulte [Desarrollo de scripts U-SQL mediante Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Para obtener más información sobre U-SQL, consulte [Introducción al lenguaje U-SQL de Análisis de Azure Data Lake](data-lake-analytics-u-sql-get-started.md).
- Para las tareas de administración, consulte [Administración de Análisis de Azure Data Lake mediante el Portal de Azure](data-lake-analytics-manage-use-portal.md).
- Para obtener información general sobre Análisis de Data Lake, consulte [Información general sobre Análisis de Azure Data Lake](data-lake-analytics-overview.md).

<!---HONumber=AcomDC_0302_2016-->