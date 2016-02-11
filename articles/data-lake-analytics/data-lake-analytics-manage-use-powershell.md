<properties 
   pageTitle="Administración de Análisis de Azure Data Lake mediante Azure PowerShell | Azure" 
   description="Aprenda a administrar trabajos, orígenes de datos y usuarios de Análisis de Data Lake." 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="01/26/2016"
   ms.author="jgao"/>

# Administración de Análisis de Azure Data Lake mediante Azure PowerShell

[AZURE.INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Aprenda a administrar cuentas, orígenes de datos, usuarios y trabajos de Análisis de Azure Data Lake mediante Azure PowerShell. Para ver los temas de administración con otras herramientas, haga clic en el selector de pestañas de arriba.

**Requisitos previos**

Antes de empezar este tutorial, debe contar con lo siguiente:

- **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).


<!-- ################################ -->
<!-- ################################ -->


##Instalar Azure PowerShell 1.0 o versiones posteriores

Vea la sección de requisitos previos de [Uso de Azure PowerShell con Administrador de recursos de Azure](powershell-azure-resource-manager.md#prerequisites).
	
## Administrar cuentas

Antes de ejecutar un trabajo de Análisis de Data Lake, debe tener una cuenta de Análisis de Data Lake. A diferencia de HDInsight de Azure, no se paga por una cuenta de Análisis cuando no está ejecutando un trabajo. Solo se paga por el tiempo en que se ejecuta un trabajo. Para obtener más información, consulte [Información general sobre Análisis de Azure Data Lake](data-lake-analytics-overview.md).

###Creación de cuentas

	$resourceGroupName = "<ResourceGroupName>"
	$dataLakeStoreName = "<DataLakeAccountName>"
	$dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
	$location = "<Microsoft Data Center>"
	
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
		-Name $dataLakeAnalyticsAccountName `
		-ResourceGroupName $resourceGroupName `
		-Location $location `
		-DefaultDataLake $dataLakeStoreName
	
	Write-Host "The newly created Data Lake Analytics account ..."  -ForegroundColor Green
	Get-AzureRmDataLakeAnalyticsAccount `
		-ResourceGroupName $resourceGroupName `
		-Name $dataLakeAnalyticsAccountName  

También puede usar una plantilla del grupo de recursos de Azure. En el [Apéndice A](#appendix-a) puede encontrar una plantilla para crear una cuenta de Análisis de Data Lake y la cuenta de Almacén de Data Lake dependiente. Guarde la plantilla en un archivo con plantilla .json y después use el siguiente script de PowerShell para llamarlo:


	$AzureSubscriptionID = "<Your Azure Subscription ID>"
	
	$ResourceGroupName = "<New Azure Resource Group Name>"
	$Location = "EAST US 2"
	$DefaultDataLakeStoreAccountName = "<New Data Lake Store Account Name>"
	$DataLakeAnalyticsAccountName = "<New Data Lake Analytics Account Name>"
	
	$DeploymentName = "MyDataLakeAnalyticsDeployment"
	$ARMTemplateFile = "E:\Tutorials\ADL\ARMTemplate\azuredeploy.json"  # update the Json template path 
	
	Login-AzureRmAccount
	
	Select-AzureRmSubscription -SubscriptionId $AzureSubscriptionID
	
	# Create the resource group
	New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location
	
	# Create the Data Lake Analytics account with the default Data Lake Store account.
	$parameters = @{"adlAnalyticsName"=$DataLakeAnalyticsAccountName; "adlStoreName"=$DefaultDataLakeStoreAccountName}
	New-AzureRmResourceGroupDeployment -Name $DeploymentName -ResourceGroupName $ResourceGroupName -TemplateFile $ARMTemplateFile -TemplateParameterObject $parameters 

 
###Enumeración de cuentas

Enumerar cuentas de Análisis de Data Lake dentro de la suscripción actual

	Get-AzureRmDataLakeAnalyticsAccount
	
Salida:

	Id         : /subscriptions/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx/resourceGroups/learn1021rg/providers/Microsoft.DataLakeAnalytics/accounts/learn1021adla
	Location   : eastus2
	Name       : learn1021adla
	Properties : Microsoft.Azure.Management.DataLake.Analytics.Models.DataLakeAnalyticsAccountProperties
	Tags       : {}
	Type       : Microsoft.DataLakeAnalytics/accounts

Enumerar cuentas de Análisis de Data Lake dentro de un grupo de recursos específico

	Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName

Obtener detalles de una cuenta específica de Análisis de Data Lake

	Get-AzureRmDataLakeAnalyticsAccount -Name $adlAnalyticsAccountName

Probar la existencia de una cuenta específica de Análisis de Data Lake

	Test-AzureRmDataLakeAnalyticsAccount -Name $adlAnalyticsAccountName

El cmdlet devolverá o bien **True** o **False**.

###Eliminación de cuentas de Análisis de Data Lake

	$resourceGroupName = "<ResourceGroupName>"
	$dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
	
	Remove-AzureRmDataLakeAnalyticsAccount -Name $dataLakeAnalyticsAccountName 

Si elimina una cuenta de Análisis, no se eliminará la cuenta dependiente de Almacén de Data Lake. El ejemplo siguiente elimina la cuenta de Análisis de Data Lake y la cuenta predeterminada de Almacén de Data Lake.

	$resourceGroupName = "<ResourceGroupName>"
	$dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
	$dataLakeStoreName = (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticAccountName).Properties.DefaultDataLakeAccount

	Remove-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticAccountName 
	Remove-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName

<!-- ################################ -->
<!-- ################################ -->
## Administración de orígenes de datos de cuenta

Actualmente, Análisis de Data Lake admite los siguientes orígenes de datos:

- [Almacén de Azure Data Lake](data-lake-storage-overview.md)
- [Almacenamiento de Azure](storage-introduction.md)

Cuando crea una cuenta de Análisis, debe designar una cuenta de Almacén de Azure Data Lake para que sea la cuenta de almacenamiento predeterminada. La cuenta predeterminada de Almacén de Data Lake sirve para almacenar los registros de auditoría y de metadatos de trabajos. Una vez creada la cuenta de Análisis, puede agregar más cuentas de Almacén de Data Lake y/o cuentas de Almacenamiento de Azure.

### Búsqueda de la cuenta predeterminada de Almacén de Data Lake

	$resourceGroupName = "<ResourceGroupName>"
	$dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
	$dataLakeStoreName = (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticAccountName).Properties.DefaultDataLakeAccount


### Adición de más cuentas de almacenamiento de blobs de Azure

	$resourceGroupName = "<ResourceGroupName>"
	$dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
	$AzureStorageAccountName = "<AzureStorageAccountName>"
	$AzureStorageAccountKey = "<AzureStorageAccountKey>"
	
	Add-AzureRmDataLakeAnalyticsDataSource -ResourceGroupName $resourceGroupName -Account $dataLakeAnalyticAccountName -AzureBlob $AzureStorageAccountName -AccessKey $AzureStorageAccountKey

### Adición de más cuentas de Almacén de Data Lake

	$resourceGroupName = "<ResourceGroupName>"
	$dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
	$AzureDataLakeName = "<DataLakeStoreName>"
	
	Add-AzureRmDataLakeAnalyticsDataSource -ResourceGroupName $resourceGroupName -Account $dataLakeAnalyticAccountName -DataLake $AzureDataLakeName 

### Enumeración de orígenes de datos:

	$resourceGroupName = "<ResourceGroupName>"
	$dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"

	(Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticAccountName).Properties.DataLakeStoreAccounts
	(Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticAccountName).Properties.StorageAccounts
	


<!-- ################################ -->
<!-- ################################ -->
## Trabajos de administración

Debe tener una cuenta de Análisis de Data Lake para poder crear un trabajo. Para obtener más información, consulte [Administración de cuentas de Análisis de Data Lake](#manage-data-lake-analytics-accounts).

### Enumeración de trabajos

	$dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
	
	Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName
	
	Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName -State Running, Queued
	#States: Accepted, Compiling, Ended, New, Paused, Queued, Running, Scheduling, Starting
	
	Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName -Result Cancelled
	#Results: Cancelled, Failed, None, Successed 
	
	Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName -Name <Job Name>
	Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName -Submitter <Job submitter>

	# List all jobs submitted on January 1 (local time)
	Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName `
		-SubmittedAfter "2015/01/01"
		-SubmittedBefore "2015/01/02"	

	# List all jobs that succeeded on January 1 after 2 pm (UTC time)
	Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName `
		-State Ended
		-Result Succeeded
		-SubmittedAfter "2015/01/01 2:00 PM -0"
		-SubmittedBefore "2015/01/02 -0"

	# List all jobs submitted in the past hour
	Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName `
		-SubmittedAfter (Get-Date).AddHours(-1)

### Obtención de detalles del trabajo

	$dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
	Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName -JobID <Job ID>
	
### Envío de trabajos

	$dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"

	#Pass script via path
	Submit-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName `
		-Name $jobName `
		-ScriptPath $scriptPath

	#Pass script contents
	Submit-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName `
		-Name $jobName `
		-Script $scriptContents

> [AZURE.NOTE] La prioridad predeterminada de un trabajo es 1000 y el grado predeterminado de paralelismo para un trabajo es 1.


### Cancelación de trabajos

	Stop-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName `
		-JobID $jobID


## Administración de elementos de catálogo

El catálogo de U-SQL se usa para estructurar datos y código, para que puedan compartirse mediante scripts de U-SQL. El catálogo permite el mayor rendimiento posible con los datos en Azure Data Lake. Para obtener más información, consulte [Uso del catálogo de U-SQL](data-lake-analytics-use-u-sql-catalog.md).

###Enumeración de elementos del catálogo

	#List databases
	Get-AzureRmDataLakeAnalyticsCatalogItem `
		-Account $adlAnalyticsAccountName `
		-ItemType Database
	
	
	
	#List tables
	Get-AzureRmDataLakeAnalyticsCatalogItem `
		-Account $adlAnalyticsAccountName `
		-ItemType Table `
		-Path "master.dbo"

###Obtención de detalles de elementos del catálogo 

	#Get a database
	Get-AzureRmDataLakeAnalyticsCatalogItem `
		-Account $adlAnalyticsAccountName `
		-ItemType Database `
		-Path "master"
	
	#Get a table
	Get-AzureRmDataLakeAnalyticsCatalogItem `
		-Account $adlAnalyticsAccountName `
		-ItemType Table `
		-Path "master.dbo.mytable"

###Comprobación de un elemento del catálogo

	Test-AzureRmDataLakeAnalyticsCatalogItem  `
		-Account $adlAnalyticsAccountName `
		-ItemType Database `
		-Path "master"

###Creación del secreto del catálogo
	New-AzureRmDataLakeAnalyticsCatalogSecret  `
			-Account $adlAnalyticsAccountName `
			-DatabaseName "master" `
			-Secret (Get-Credential -UserName "username" -Message "Enter the password")

### Modificación del secreto del catálogo
	Set-AzureRmDataLakeAnalyticsCatalogSecret  `
			-Account $adlAnalyticsAccountName `
			-DatabaseName "master" `
			-Secret (Get-Credential -UserName "username" -Message "Enter the password")



###Eliminación del secreto del catálogo
	Remove-AzureRmDataLakeAnalyticsCatalogSecret  `
			-Account $adlAnalyticsAccountName `
			-DatabaseName "master"


## Uso de grupos del Administrador de recursos de Azure

Las aplicaciones normalmente se componen de muchos componentes,por ejemplo una aplicación web, base de datos, servidor de base de datos, almacenamiento y servicios de terceros. El Administrador de recursos de Azure (ARM) permite trabajar con los recursos de la aplicación como un grupo al que se hace referencia como Grupo de recursos de Azures Puede implementar, actualizar, supervisar o eliminar todos los recursos de la aplicación en una operación única y coordinada. Para la implementación se utiliza una plantilla, y esta plantilla puede trabajar en diferentes entornos, como pruebas, ensayo y producción. Puede aclarar la facturación de la organización consultando los costes acumulados de todo el grupo. Para obtener más información, consulte [Información general del Administrador de recursos de Azure](../resource-group-overview.md).

Un servicio de Análisis de Data Lake puede incluir los siguientes componentes:

- Cuenta de Análisis de Azure Data Lake
- Cuenta predeterminada y necesaria de Almacén de Azure Data Lake
- Cuentas adicionales de Almacén de Azure Data Lake
- Cuentas adicionales de Almacenamiento de Azure

Puede crear todos estos componentes en un grupo de ARM para que sean más fáciles de administrar.

![Cuenta y almacenamiento de Análisis de Azure Data Lake](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-arm-structure.png)

Una cuenta de Análisis de Data Lake y las cuentas de almacenamiento dependientes deben ubicarse en el mismo centro de datos de Azure. Sin embargo, el grupo de ARM puede encontrarse en otro centro de datos.

##Consulte también 

- [Información general de Análisis de Microsoft Azure Data Lake](data-lake-analytics-overview.md)
- [Introducción a Análisis de Data Lake mediante el Portal de Azure](data-lake-analytics-get-started-portal.md)
- [Administración de Análisis de Azure Data Lake mediante el Portal de Azure](data-lake-analytics-manage-use-portal.md)
- [Supervisión y solución de problemas de trabajos de Análisis de Azure Data Lake mediante el Portal de Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

##Apéndice A - Plantilla ARM de Análisis de Data Lake

La siguiente plantilla ARM puede utilizarse para implementar una cuenta de Análisis de Data Lake y su cuenta de Almacén de Data Lake dependiente. Guárdela como un archivo json y después use el script de PowerShell para llamar a la plantilla. Para obtener más información , consulte [Implementación de una aplicación con la plantilla de Administrador de recursos de Azure](../resource-group-authoring-templates.md) y [Creación de plantillas de Administrador de recursos de Azure](../resource-group-template-deploy.md#deploy-with-powershell).

	{
	  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
	  "contentVersion": "1.0.0.0",
	  "parameters": {
	    "adlAnalyticsName": {
	      "type": "string",
	      "metadata": {
	        "description": "The name of the Data Lake Analytics account to create."
	      }
	    },
	    "adlStoreName": {
	      "type": "string",
	      "metadata": {
	        "description": "The name of the Data Lake Store account to create."
	      }
	    }
	  },
	  "resources": [
	    {
	      "name": "[parameters('adlStoreName')]",
	      "type": "Microsoft.DataLakeStore/accounts",
	      "location": "East US 2",
	      "apiVersion": "2015-10-01-preview",
	      "dependsOn": [ ],
	      "tags": { }
	    },
	    {
	      "name": "[parameters('adlAnalyticsName')]",
	      "type": "Microsoft.DataLakeAnalytics/accounts",
	      "location": "East US 2",
	      "apiVersion": "2015-10-01-preview",
	      "dependsOn": [ "[concat('Microsoft.DataLakeStore/accounts/',parameters('adlStoreName'))]" ],
	      "tags": { },
	      "properties": {
	        "defaultDataLakeStoreAccount": "[parameters('adlStoreName')]",
	        "dataLakeStoreAccounts": [
	          { "name": "[parameters('adlStoreName')]" }
	        ]
	      }
	    }
	  ],
	  "outputs": {
	    "adlAnalyticsAccount": {
	      "type": "object",
	      "value": "[reference(resourceId('Microsoft.DataLakeAnalytics/accounts',parameters('adlAnalyticsName')))]"
	    },
	    "adlStoreAccount": {
	      "type": "object",
	      "value": "[reference(resourceId('Microsoft.DataLakeStore/accounts',parameters('adlStoreName')))]"
	    }
	  }
	}

<!---HONumber=AcomDC_0128_2016-->