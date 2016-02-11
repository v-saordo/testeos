<properties 
	pageTitle="Habilitar el diagnóstico en Servicios en la nube de Azure mediante PowerShell | Microsoft Azure" 
	description="Obtener información sobre cómo habilitar el diagnóstico en servicios en la nube mediante PowerShell" 
	services="cloud-services" 
	documentationCenter=".net" 
	authors="sbtron" 
	manager="" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/16/2015" 
	ms.author="saurabh"/>


# Habilitar el diagnóstico en Servicios en la nube de Azure mediante PowerShell

Puede recopilar datos de diagnóstico como registros de aplicaciones, contadores de rendimiento, etc., de un servicio en la nube mediante la extensión de Diagnósticos de Azure. En este artículo se describe cómo habilitar la extensión de Diagnósticos de Azure para un servicio en la nube mediante PowerShell. Para conocer los requisitos previos necesarios para este artículo, consulte [Cómo instalar y configurar Azure PowerShell](powershell-install-configure.md).

## Habilitar la extensión de diagnósticos como parte de la implementación de un servicio en la nube

Este enfoque para el tipo de integración continua de escenarios en los que se puede habilitar la extensión de diagnósticos. Puede habilitar la extensión de diagnóstico como parte de la implementación del servicio en la nube pasando el parámetro *ExtensionConfiguration* al cmdlet [New-AzureDeployment](https://msdn.microsoft.com/library/azure/mt589089.aspx). El parámetro *ExtensionConfiguration* toma una matriz de configuraciones de diagnóstico que se pueden crear mediante el cmdlet [New-AzureServiceDiagnosticsExtensionConfig](https://msdn.microsoft.com/library/azure/mt589168.aspx).

En el ejemplo siguiente se muestra cómo habilitar el diagnóstico de un servicio en la nube con un WebRole y WorkerRole, cada uno con una configuración de diagnóstico diferente.

	$service_name = "MyService"
	$service_package = "CloudService.cspkg"
	$service_config = "ServiceConfiguration.Cloud.cscfg"
	$diagnostics_storagename = "myservicediagnostics"
	$webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
	$workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

	$primary_storagekey = (Get-AzureStorageKey -StorageAccountName "$diagnostics_storagename").Primary
	$storage_context = New-AzureStorageContext -StorageAccountName $diagnostics_storagename -StorageAccountKey $primary_storagekey

	$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -Storage_context $storageContext -DiagnosticsConfigurationPath $webrole_diagconfigpath
	$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -StorageContext $storage_context -DiagnosticsConfigurationPath $workerrole_diagconfigpath
	  
	 
	New-AzureDeployment -ServiceName $service_name -Slot Production -Package $service_package -Configuration $service_config -ExtensionConfiguration @($webrole_diagconfig,$workerrole_diagconfig) 



## Habilitar la extensión de diagnóstico en un servicio en la nube existente

Puede usar el cmdlet [Set-AzureServiceDiagnosticsExtension](https://msdn.microsoft.com/library/azure/mt589140.aspx) para habilitar diagnósticos en un servicio en la nube que ya se está ejecutando.


	$service_name = "MyService"
	$diagnostics_storagename = "myservicediagnostics"
	$webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
	$workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"
	$primary_storagekey = (Get-AzureStorageKey -StorageAccountName "$diagnostics_storagename").Primary
	$storage_context = New-AzureStorageContext -StorageAccountName $diagnostics_storagename -StorageAccountKey $primary_storagekey
 
	Set-AzureServiceDiagnosticsExtension -StorageContext $storage_context -DiagnosticsConfigurationPath $webrole_diagconfigpath -ServiceName $service_name -Slot Production -Role "WebRole" 
	Set-AzureServiceDiagnosticsExtension -StorageContext $storage_context -DiagnosticsConfigurationPath $workerrole_diagconfigpath -ServiceName $service_name -Slot Production -Role "WorkerRole"
 

## Obtener la configuración actual de la extensión de diagnósticos
Use el cmdlet [Get-AzureServiceDiagnosticsExtension](https://msdn.microsoft.com/library/azure/mt589204.aspx) para obtener la configuración actual de diagnósticos para un servicio en la nube.
	
	Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"

## Eliminar la extensión de diagnósticos
Para desactivar el diagnóstico en un servicio en la nube, puede usar el cmdlet [Remove-AzureServiceDiagnosticsExtension](https://msdn.microsoft.com/library/azure/mt589183.aspx).

	Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"

Si ha habilitado la extensión de diagnóstico mediante *Set-AzureServiceDiagnosticsExtension* o *New-AzureServiceDiagnosticsExtensionConfig* sin el parámetro *Role*, podrá eliminar la extensión mediante *Remove-AzureServiceDiagnosticsExtension* sin el parámetro *Role*. Si se ha usado el parámetro *Role* al habilitar la extensión, entonces se debe utilizar también al quitar la extensión.

Para quitar la extensión de diagnóstico de cada rol individual:

	Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"


## Pasos siguientes

- Para obtener orientación adicional sobre el uso de diagnósticos de Azure y otras técnicas para solucionar problemas, consulte [Habilitación de diagnósticos en Servicios en la nube y Máquinas virtuales de Azure](cloud-services-dotnet-diagnostics.md).
- En el [Esquema de configuración de diagnósticos](https://msdn.microsoft.com/library/azure/dn782207.aspx) se explican las distintas opciones de configuración xml para la extensión de diagnósticos.
- Para obtener información sobre cómo habilitar la extensión de diagnósticos para las máquinas virtuales, consulte [Crear una máquina virtual de Windows con supervisión y diagnóstico mediante la plantilla de Administrador de recursos de Azure](virtual-machines-extensions-diagnostics-windows-template.md)  

<!---HONumber=Nov15_HO4-->