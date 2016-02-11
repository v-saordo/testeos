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
	ms.date="10/21/2015" 
	ms.author="awills"/>

#  Script de PowerShell para crear un recurso de Application Insights

*Application Insights se encuentra en su versión de vista previa.*

Cuando desee supervisar una nueva aplicación —o una nueva versión de una aplicación— con [Application Insights para Visual Studio](https://azure.microsoft.com/services/application-insights/), configure un recurso nuevo en Microsoft Azure. Este recurso es donde se analizan y muestran los datos de telemetría procedentes de su aplicación.

Puede automatizar la creación de un nuevo recurso mediante PowerShell.

Por ejemplo, si está desarrollando una aplicación de dispositivo móvil, es probable que, en cualquier momento, haya varias versiones publicadas de la aplicación en uso por los clientes. No querrá obtener los resultados de telemetría mezclados de las diferentes versiones. Por tanto, hará que el proceso de compilación cree un nuevo recurso para cada compilación.

## Script para crear un recurso en Application Insights

*Salida*

* Nombre de la aplicación Insights = mytestapp
* IKey = 00000000-0000-0000-0000-000000000000

*Script de PowerShell*

```PowerShell

cls


##################################################################
# Set Values
##################################################################

#If running manually, comment this out before the first execution to login to the Azure Portal:

#Add-AzureAccount

#Set the name of the Application Insights Resource
$appInsightsName = "TestApp"

#Set the application name used for the value of the Tag "AppInsightsApp" - http://azure.microsoft.com/documentation/articles/azure-preview-portal-using-tags/
$applicationTagName = "MyApp"

#Set the name of the Resource Group to use.  By default will use the application name as a starter
$resourceGroupName = "MyAppResourceGroup"

##################################################################
# Create the Resource and Output the name and iKey
##################################################################
#Set the script to Resource Manager - http://azure.microsoft.com/documentation/articles/powershell-azure-resource-manager/
Switch-AzureMode AzureResourceManager

#Select the azure subscription
Select-AzureSubscription -SubscriptionName "MySubscription"

#Create the App Insights Resource
$resource = New-AzureResource -ResourceName $appInsightsName -ResourceGroupName $resourceGroupName -Tag @{ Name = "AppInsightsApp"; Value = $applicationTagName} -ResourceType "Microsoft.Insights/Components" -Location "Central US" -ApiVersion "2014-08-01" -PropertyObject @{"Type"="ASP.NET"} -Force -OutputObjectFormat New

#Give team owner access - http://azure.microsoft.com/documentation/articles/role-based-access-control-powershell/
New-AzureRoleAssignment -Mail "myTeam@fabrikam.com" -RoleDefinitionName Owner -Scope $resource.ResourceId | Out-Null

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
* [Establecimiento de alertas mediante PowerShell](app-insights-alerts.md#set-alerts-by-using-powershell)

 

<!---HONumber=AcomDC_1125_2015-->