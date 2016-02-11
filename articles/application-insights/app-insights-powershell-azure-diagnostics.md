<properties
    pageTitle="Uso de PowerShell para enviar Diagnósticos de Azure a Application Insights | Microsoft Azure"
    description="Configuración automática de Diagnósticos de Azure para canalización a Application Insights"
    services="application-insights"
    documentationCenter=".net"
    authors="sbtron"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
	ms.tgt_pltfrm="ibiza" 
    ms.devlang="na"
    ms.topic="get-started-article"
	ms.date="11/17/2015"
    ms.author="awills"/>

# Uso de PowerShell para enviar Diagnósticos de Azure a Application Insights

[Microsoft Azure](https://azure.com) puede [configurarse para que envíe Diagnósticos de Azure](app-insights-azure-diagnostics.md) a [Application Insights para Visual Studio](app-insights-overview.md). Los diagnósticos están relacionados con Servicios en la nube de Azure y Máquinas virtuales de Azure. Complementan la telemetría que se envía desde la aplicación mediante el SDK de Application Insights. Como parte de la automatización del proceso de creación de nuevos recursos en Azure, puede configurar diagnósticos mediante PowerShell.

## Habilitar la extensión de diagnósticos como parte de la implementación de un servicio en la nube

El cmdlet `New-AzureDeployment` tiene un parámetro `ExtensionConfiguration`, que toma una matriz de configuraciones de diagnósticos. Estas pueden crearse mediante el cmdlet `New-AzureServiceDiagnosticsExtensionConfig`. Por ejemplo:

```ps

    $service_package = "CloudService.cspkg"
    $service_config = "ServiceConfiguration.Cloud.cscfg"
    $diagnostics_storagename = "myservicediagnostics"
    $webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
    $workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

    $primary_storagekey = (Get-AzureStorageKey `
     -StorageAccountName "$diagnostics_storagename").Primary
    $storage_context = New-AzureStorageContext `
       -StorageAccountName $diagnostics_storagename `
       -StorageAccountKey $primary_storagekey

    $webrole_diagconfig = `
     New-AzureServiceDiagnosticsExtensionConfig `
      -Role "WebRole" -Storage_context $storageContext `
      -DiagnosticsConfigurationPath $webrole_diagconfigpath
    $workerrole_diagconfig = `
     New-AzureServiceDiagnosticsExtensionConfig `
      -Role "WorkerRole" `
      -StorageContext $storage_context `
      -DiagnosticsConfigurationPath $workerrole_diagconfigpath

    New-AzureDeployment `
      -ServiceName $service_name `
      -Slot Production `
      -Package $service_package `
      -Configuration $service_config `
      -ExtensionConfiguration @($webrole_diagconfig,$workerrole_diagconfig)

``` 

## Habilitar la extensión de diagnóstico en un servicio en la nube existente

En un servicio existente, use `Set-AzureServiceDiagnosticsExtension`.

```ps
 
    $service_name = "MyService"
    $diagnostics_storagename = "myservicediagnostics"
    $webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
    $workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"
    $primary_storagekey = (Get-AzureStorageKey `
         -StorageAccountName "$diagnostics_storagename").Primary
    $storage_context = New-AzureStorageContext `
        -StorageAccountName $diagnostics_storagename `
        -StorageAccountKey $primary_storagekey

    Set-AzureServiceDiagnosticsExtension `
        -StorageContext $storage_context `
        -DiagnosticsConfigurationPath $webrole_diagconfigpath `
        -ServiceName $service_name `
        -Slot Production `
        -Role "WebRole" 
    Set-AzureServiceDiagnosticsExtension `
        -StorageContext $storage_context `
        -DiagnosticsConfigurationPath $workerrole_diagconfigpath `
        -ServiceName $service_name `
        -Slot Production `
        -Role "WorkerRole"
```

## Obtener la configuración actual de la extensión de diagnósticos

```ps

    Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```


## Eliminar la extensión de diagnósticos

```ps

    Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

Si ha habilitado la extensión de diagnósticos mediante `Set-AzureServiceDiagnosticsExtension` o `New-AzureServiceDiagnosticsExtensionConfig` sin el parámetro Role, puede quitar la extensión mediante `Remove-AzureServiceDiagnosticsExtension` sin el parámetro Role. Si ha usado el parámetro Role al habilitar la extensión, debe utilizarlo también al quitar la extensión.

Para quitar la extensión de diagnóstico de cada rol individual:

```ps

    Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"
```


## Consulte también

* [Supervisión de aplicaciones de Servicios en la nube de Azure con Application Insights](app-insights-cloudservices.md)
* [Envío de Azure Diagnostics a Application Insights](app-insights-azure-diagnostics.md)
* [Automatización de la configuración de alertas](app-insights-powershell-alerts.md)

<!---HONumber=AcomDC_0128_2016-->