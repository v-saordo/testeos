<properties 
	pageTitle="Administración de Búsqueda de Azure con scripts de PowerShell | Microsoft Azure | Servicio de búsqueda hospedado en la nube" 
	description="Administre el servicio Búsqueda de Azure en Microsoft Azure con scripts de PowerShell. Creación o actualización del servicio Búsqueda de Azure y administración de las claves de administración de Búsqueda de Azure"  
	services="search" 
	documentationCenter="" 
	authors="seansaleh" 
	manager="mblythe" 
	editor=""
	tags="azure-resource-manager"/>

<tags 
	ms.service="search" 
	ms.devlang="na" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="powershell" 
	ms.date="02/25/2016" 
	ms.author="seasa"/>

# Administración del servicio Búsqueda en Microsoft Azure con PowerShell
> [AZURE.SELECTOR]
- [Portal](search-manage.md)
- [PowerShell](search-manage-powershell.md)
- [API DE REST](search-get-started-management-api.md)

En este tema se describe los comandos de PowerShell para realizar muchas de las tareas de administración del servicio Búsqueda de Azure. Se le guiará por la creación de un servicio de búsqueda, su escalado y la administración de sus claves de API. Estos comandos equivalen a las opciones de administración disponibles en la [API de REST de administración de Búsqueda de Azure](http://msdn.microsoft.com/library/dn832684.aspx).

## Requisitos previos
 
- Debe tener Azure PowerShell 1.0 o versiones posteriores. Para obtener más información, consulte [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md).
- Debe iniciar sesión en su suscripción de Azure en PowerShell, tal y como se describe a continuación.

En primer lugar, debe iniciar sesión en Azure con este comando.

	Login-AzureRmAccount

Especifique la dirección de correo electrónico de la cuenta de Azure y su contraseña en el cuadro de diálogo de inicio de sesión de Microsoft Azure.

También puede [iniciar sesión de forma no interactiva con una entidad de servicio](../resource-group-authenticate-service-principal.md).

Si tiene varias suscripciones de Azure, deberá establecer la suscripción de Azure. Para ver una lista de las suscripciones actuales, ejecute este comando.

	Get-AzureRmSubscription | sort SubscriptionName | Select SubscriptionName

Para especificar la suscripción, ejecute el siguiente comando. En el ejemplo siguiente, el nombre de la suscripción es `ContosoSubscription`.

	Select-AzureRmSubscription -SubscriptionName ContosoSubscription

## Comandos para ayudarle a empezar a trabajar

	$serviceName = "your-service-name-lowercase-with-dashes"
	$sku = "free" # or "standard" for a paid service
	$location = "West US"
	# You can get a list of potential locations with
	# (Get-AzureRmResourceProvider -ListAvailable | Where-Object {$_.ProviderNamespace -eq 'Microsoft.Search'}).Locations
	$resourceGroupName = "YourResourceGroup" 
	# If you don't already have this resource group, you can create it with with 
	# New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

	# Register the arm provider idempotently. This must be done once per subscription
	Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Search" -Force

	# Create a new search service
	# This command will return once the service is fully created
	New-AzureRmResourceGroupDeployment `
		-ResourceGroupName $resourceGroupName `
		-TemplateUri "https://gallery.azure.com/artifact/20151001/Microsoft.Search.1.0.9/DeploymentTemplates/searchServiceDefaultTemplate.json" `
		-NameFromTemplate $serviceName `
		-Sku $sku `
		-Location $location `
		-PartitionCount 1 `
		-ReplicaCount 1
	
	# Get information about your new service and store it in $resource
	$resource = Get-AzureRmResource `
		-ResourceType "Microsoft.Search/searchServices" `
		-ResourceGroupName $resourceGroupName `
		-ResourceName $serviceName `
		-ApiVersion 2015-08-19
	
	# View your resource
	$resource
	
	# Get the primary admin api key
	$primaryKey = (Invoke-AzureRmResourceAction `
		-Action listAdminKeys `
		-ResourceId ($resource.ResourceId) `
		-ApiVersion 2015-08-19).PrimaryKey

	# Regenerate the secondary admin api Key
	$secondaryKey = (Invoke-AzureRmResourceAction `
		-ResourceType "Microsoft.Search/searchServices/regenerateAdminKey" `
		-ResourceGroupName $resourceGroupName `
		-ResourceName $serviceName `
		-ApiVersion 2015-08-19 `
		-Action secondary).SecondaryKey

	# Create a query key for read only access to your indexes
	$queryKeyDescription = "query-key-created-from-powershell"
	$queryKey = (Invoke-AzureRmResourceAction `
		-ResourceType "Microsoft.Search/searchServices/createQueryKey" `
		-ResourceGroupName $resourceGroupName `
		-ResourceName $serviceName `
		-ApiVersion 2015-08-19 `
		-Action $queryKeyDescription).Key

	# Delete query key
	Remove-AzureRmResource `
		-ResourceType "Microsoft.Search/searchServices/deleteQueryKey/$($queryKey)" `
		-ResourceGroupName $resourceGroupName `
		-ResourceName $serviceName `
		-ApiVersion 2015-08-19
		
	# Scale your service up
	# Note that this will only work if you made a non "free" service
	# This command will not return until the operation is finished
	# It can take 15 minutes or more to provision the additional resources
	$resource.Properties.ReplicaCount = 2
	$resource | Set-AzureRmResource
	
	# Delete your service
	# Deleting your service will delete all indexes and data in the service
	$resource | Remove-AzureRmResource
	
## Pasos siguientes
	
Ahora que el servicio está creado, puede realizar los pasos siguientes: crear un [índice](search-what-is-an-index.md), [consultar un índice](search-query-overview.md) y, por último, crear y administrar su propia aplicación de búsqueda que usan Búsqueda de Azure.

- [Creación de un índice de Búsqueda de Azure en el Portal de Azure](search-create-index-portal.md)

- [Consulta de un índice de Búsqueda de Azure mediante el Explorador de búsqueda en el Portal de Azure](search-explorer.md)

- [Configurar un indexador para cargar datos desde otros servicios](search-indexer-overview.md)

- [Cómo usar la Búsqueda de Azure en .NET](search-howto-dotnet-sdk.md)

- [Analizar el tráfico de Búsqueda de Azure](search-traffic-analytics.md)

<!---HONumber=AcomDC_0302_2016-->