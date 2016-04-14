<properties 
	pageTitle="Script de PowerShell para crear un recurso de Application Insights" 
	description="Automatice la creación de recursos de Application Insights." 
	services="application-insights" 
    documentationCenter="windows"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/19/2016" 
	ms.author="awills"/>

#  Script de PowerShell para crear un recurso de Application Insights

*Application Insights se encuentra en su versión de vista previa.*

Cuando desee supervisar una nueva aplicación —o una nueva versión de una aplicación— con [Application Insights para Visual Studio](https://azure.microsoft.com/services/application-insights/), configure un recurso nuevo en Microsoft Azure. Este recurso es donde se analizan y muestran los datos de telemetría procedentes de su aplicación.

Puede automatizar la creación de un nuevo recurso mediante PowerShell.

Por ejemplo, si está desarrollando una aplicación de dispositivo móvil, es probable que, en cualquier momento, haya varias versiones publicadas de la aplicación en uso por los clientes. No querrá obtener los resultados de telemetría mezclados de las diferentes versiones. Por tanto, hará que el proceso de compilación cree un nuevo recurso para cada compilación.

## Script para crear un recurso en Application Insights

Consulte las especificaciones de cmdlet pertinentes:

* [New-AzureRmResource](https://msdn.microsoft.com/library/mt652510.aspx)
* [New-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt678995.aspx)


*Script de PowerShell*

```PowerShell


###########################################
# Set Values
###########################################

# If running manually, uncomment before the first 
# execution to login to the Azure Portal:

# Add-AzureRmAccount

# Set the name of the Application Insights Resource

$appInsightsName = "TestApp"

# Set the application name used for the value of the Tag "AppInsightsApp" 
# - http://azure.microsoft.com/documentation/articles/azure-preview-portal-using-tags/
$applicationTagName = "MyApp"

# Set the name of the Resource Group to use.  
# Default is the application name.
$resourceGroupName = "MyAppResourceGroup"

###################################################
# Create the Resource and Output the name and iKey
###################################################

#Select the azure subscription
Select-AzureSubscription -SubscriptionName "MySubscription"

# Create the App Insights Resource

$resource = New-AzureRmResource `
  -ResourceName $appInsightsName `
  -ResourceGroupName $resourceGroupName `
  -Tag @{ Name = "AppInsightsApp"; Value = $applicationTagName} `
  -ResourceType "Microsoft.Insights/Components" `
  -Location "Central US" `
  -PropertyObject @{"Type"="ASP.NET"} `
  -Force

# Give owner access to the team

New-AzureRmRoleAssignment `
  -SignInName "myteam@fabrikam.com" `
  -RoleDefinitionName Owner `
  -Scope $resource.ResourceId 


#Display iKey
Write-Host "App Insights Name = " $resource.Name
Write-Host "IKey = " $resource.Properties.InstrumentationKey

```

## Qué hacer con el valor iKey

Cada recurso se identifica por su clave de instrumentación (iKey). El valor iKey es un resultado del script de creación de recursos. El script de compilación debe proporcionar el valor iKey al SDK de Application Insights insertado en la aplicación.

Hay dos maneras de hacer que el valor iKey esté disponible para el SDK:
  
* En [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md): 
 * `<instrumentationkey>`*ikey*`</instrumentationkey>`
* O bien en el [código de inicialización](app-insights-api-custom-events-metrics.md): 
 * `Microsoft.ApplicationInsights.Extensibility.
    TelemetryConfiguration.Active.InstrumentationKey = "`*iKey*`";`



## Consulte también

* [Crear Application Insights y recursos de pruebas web a partir de plantillas](app-insights-powershell.md)
* [Configurar la supervisión de diagnósticos de Azure con PowerShell](app-insights-powershell-azure-diagnostics.md) 
* [Establecimiento de alertas mediante PowerShell](app-insights-powershell-alerts.md)

 

<!---HONumber=AcomDC_0224_2016-->