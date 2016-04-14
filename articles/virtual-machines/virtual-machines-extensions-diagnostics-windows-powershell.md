<properties pageTitle="Uso de PowerShell para habilitar Diagnósticos de Azure en una máquina virtual con Windows | Microsoft Azure" services="virtual-machines" documentationCenter="" description="Aprenda a usar PowerShell para habilitar Diagnósticos de Azure en una máquina virtual con Windows" authors="sbtron" manager="" editor="""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/15/2015"
	ms.author="saurabh"/>


# Uso de PowerShell para habilitar Diagnósticos de Azure en una máquina virtual con Windows

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Diagnósticos de Azure es la funcionalidad de Azure que habilita la recopilación de datos de diagnóstico en una aplicación implementada. Puede usar la extensión de diagnósticos para recopilar datos de diagnóstico como registros de aplicaciones o contadores de rendimiento de una máquina virtual (VM) de Azure con Windows. En este artículo se describe cómo usar Windows PowerShell para habilitar la extensión de diagnósticos para una VM. Para conocer los requisitos previos necesarios para este artículo, consulte [Cómo instalar y configurar Azure PowerShell](powershell-install-configure.md).

## Habilite la extensión de diagnósticos si usa el modelo de implementación del Administrador de recursos

Puede habilitar la extensión de diagnósticos al crear una VM de Windows a través del modelo de implementación del Administrador de recursos de Azure añadiendo la configuración de extensiones a la plantilla del Administrador de recursos. Vea [Creación de una máquina virtual de Windows con supervisión y diagnóstico mediante la plantilla del Administrador de recursos de Azure](virtual-machines-extensions-diagnostics-windows-template.md).

Para habilitar la extensión de diagnósticos en una VM existente creada mediante el modelo de implementación del Administrador de recursos, puede usar el cmdlet de PowerShell [Set-AzureRMVMDiagnosticsExtension](https://msdn.microsoft.com/library/mt603499.aspx) tal como se muestra a continuación.


	$vm_resourcegroup = "myvmresourcegroup"
	$vm_name = "myvm"
	$diagnosticsconfig_path = "DiagnosticsPubConfig.xml"

	Set-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name -DiagnosticsConfigurationPath $diagnosticsconfig_path


*$diagnosticsconfig\_path* es la ruta de acceso del archivo que contiene la configuración de diagnóstico en XML como se describe en el [ejemplo](#sample-diagnostics-configuration) a continuación.

Si especifica el archivo de configuración de diagnósticos de un elemento **StorageAccount** con un nombre de cuenta de almacenamiento, el script *AzureRMVMDiagnosticsExtension conjunto* establecerá automáticamente la extensión de diagnósticos para enviar datos de diagnóstico a esa cuenta de almacenamiento. Para que esto funcione, la cuenta de almacenamiento necesita estar en la misma suscripción que la VM.

Si no se ha especificado ningún **StorageAccount** en la configuración de diagnóstico, es necesario pasar el parámetro *StorageAccountName* al cmdlet. Si el parámetro *StorageAccountName* no se especifica, el cmdlet siempre usará la cuenta de almacenamiento especificada en el parámetro y no la especificada en el archivo de configuración de diagnóstico.

Si la cuenta de almacenamiento de diagnóstico está en una suscripción distinta que la VM, es necesario pasar explícitamente los parámetros *StorageAccountName* y *StorageAccountKey* al cmdlet. El parámetro *StorageAccountKey* no es necesario cuando la cuenta de almacenamiento de diagnóstico está en la misma suscripción que el cmdlet puede consultar automáticamente y establecer el valor de clave cuando se habilita la extensión de diagnóstico. Sin embargo, si la cuenta de almacenamiento de diagnóstico está en una suscripción diferente, es posible que el cmdlet no pueda obtener la clave automáticamente y que sea necesario especificar explícitamente la clave a través del parámetro *StorageAccountKey*.

	Set-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name -DiagnosticsConfigurationPath $diagnosticsconfig_path -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key

Una vez habilitada la extensión de diagnósticos en una VM, puede obtener la configuración actual mediante el cmdlet [AzureRMVmDiagnosticsExtension Get](https://msdn.microsoft.com/library/mt603678.aspx).

	Get-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name

El cmdlet devuelve *PublicSettings*, que contiene la configuración XML en un formato codificado en Base64. Para leer el archivo XML, es necesario descodificarlo.

	$publicsettings = (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name).PublicSettings
	$encodedconfig = (ConvertFrom-Json -InputObject $publicsettings).xmlCfg
	$xmlconfig = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedconfig))
	Write-Host $xmlconfig

El cmdlet [Remove-AzureRMVmDiagnosticsExtension](https://msdn.microsoft.com/library/mt603782.aspx) puede usarse para quitar la extensión de diagnósticos de la máquina virtual.

## Habilite la extensión de diagnósticos si usa el modelo de implementación clásico

Puede usar el cmdlet [Set-AzureVMDiagnosticsExtension](https://msdn.microsoft.com/library/mt589189.aspx) para habilitar la extensión de diagnósticos en una VM creada mediante el modelo de implementación clásica. En el ejemplo siguiente se muestra cómo crear una nueva VM mediante el modelo de implementación clásica con la extensión de diagnósticos habilitada.

	$VM = New-AzureVMConfig -Name $VM -InstanceSize Small -ImageName $VMImage
	$VM = Add-AzureProvisioningConfig -VM $VM -AdminUsername $Username -Password $Password -Windows
	$VM = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $Config_Path -VM $VM -StorageContext $Storage_Context
	New-AzureVM -Location $Location -ServiceName $Service_Name -VM $VM

Para habilitar la extensión de diagnósticos en una VM existente creada mediante el modelo de implementación clásica, use el cmdlet [Get-AzureVM](https://msdn.microsoft.com/library/mt589152.aspx) en primer lugar para obtener la configuración de VM. A continuación, actualice la configuración de VM para incluir la extensión de diagnósticos mediante el cmdlet [Set-AzureVMDiagnosticsExtension](https://msdn.microsoft.com/library/mt589189.aspx). Por último, aplique la configuración actualizada a la VM mediante [Update-AzureVM](https://msdn.microsoft.com/library/mt589121.aspx).

	$VM = Get-AzureVM -ServiceName $Service_Name -Name $VM_Name
	$VM_Update = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $Config_Path -VM $VM -StorageContext $Storage_Context
	Update-AzureVM -ServiceName $Service_Name -Name $VM_Name -VM $VM_Update.VM

## Configuración de diagnósticos de ejemplo

Se puede utilizar el siguiente XML para la configuración pública de diagnósticos con los scripts anteriores. Esta configuración de ejemplo transferirá diversos contadores de rendimiento a la cuenta de almacenamiento de información de diagnósticos junto con los errores de la aplicación, seguridad y canales del sistema en los registros de eventos de Windows y los errores de los registros de infraestructura de diagnóstico.

La configuración debe actualizarse para incluir lo siguiente:

- El atributo *resourceID* del elemento **Métricas** tiene que actualizarse con el identificador de recurso de la VM.
	- El identificador de recursos puede crearse mediante el siguiente patrón: "/ subscriptions / {*identificador de la suscripción para la suscripción con la VM*} / ResourceGroups / {*El nombre del grupo de recursos para la máquina virtual*} / providers/Microsoft.Compute/virtualMachines/ {*El nombre de la VM*}".
	- Por ejemplo, si el identificador de suscripción para la suscripción donde se está ejecutando la VM es **11111111-1111-1111-1111-111111111111**, el nombre del grupo de recursos para el grupo de recursos es **MyResourceGroup** y el nombre de la VM es **MyWindowsVM**, el valor de *resourceID* sería:

		```
		<Metrics resourceId="/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/MyWindowsVM" >
		```

	- Para obtener más información sobre cómo se generan las métricas según la configuración de las métricas y los contadores de rendimiento, consulte la [tabla de métricas de Diagnósticos de Azure de almacenamiento](virtual-machines-extensions-diagnostics-windows-template.md#wadmetrics-tables-in-storage).

- El elemento **StorageAccount** tiene que actualizarse con el nombre de la cuenta de almacenamiento de diagnósticos.

	```
	<?xml version="1.0" encoding="utf-8"?>
	<PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
	    <WadCfg>
	      <DiagnosticMonitorConfiguration overallQuotaInMB="4096">
	        <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error"/>
	        <PerformanceCounters scheduledTransferPeriod="PT1M">
	      <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" unit="Percent">
	        <annotation displayName="CPU utilization" locale="es-ES"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Privileged Time" sampleRate="PT15S" unit="Percent">
	        <annotation displayName="CPU privileged time" locale="es-ES"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% User Time" sampleRate="PT15S" unit="Percent">
	        <annotation displayName="CPU user time" locale="es-ES"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\Processor Information(_Total)\Processor Frequency" sampleRate="PT15S" unit="Count">
	        <annotation displayName="CPU frequency" locale="es-ES"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\System\Processes" sampleRate="PT15S" unit="Count">
	        <annotation displayName="Processes" locale="es-ES"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\Process(_Total)\Thread Count" sampleRate="PT15S" unit="Count">
	        <annotation displayName="Threads" locale="es-ES"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\Process(_Total)\Handle Count" sampleRate="PT15S" unit="Count">
	        <annotation displayName="Handles" locale="es-ES"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\Memory\% Committed Bytes In Use" sampleRate="PT15S" unit="Percent">
	        <annotation displayName="Memory usage" locale="es-ES"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\Memory\Available Bytes" sampleRate="PT15S" unit="Bytes">
	        <annotation displayName="Memory available" locale="es-ES"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT15S" unit="Bytes">
	        <annotation displayName="Memory committed" locale="es-ES"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\Memory\Commit Limit" sampleRate="PT15S" unit="Bytes">
	        <annotation displayName="Memory commit limit" locale="es-ES"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\Memory\Pool Paged Bytes" sampleRate="PT15S" unit="Bytes">
	        <annotation displayName="Memory paged pool" locale="es-ES"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\Memory\Pool Nonpaged Bytes" sampleRate="PT15S" unit="Bytes">
	        <annotation displayName="Memory non-paged pool" locale="es-ES"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Time" sampleRate="PT15S" unit="Percent">
	        <annotation displayName="Disk active time" locale="es-ES"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Read Time" sampleRate="PT15S" unit="Percent">
	        <annotation displayName="Disk active read time" locale="es-ES"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Write Time" sampleRate="PT15S" unit="Percent">
	        <annotation displayName="Disk active write time" locale="es-ES"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Transfers/sec" sampleRate="PT15S" unit="CountPerSecond">
	        <annotation displayName="Disk operations" locale="es-ES"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Reads/sec" sampleRate="PT15S" unit="CountPerSecond">
	        <annotation displayName="Disk read operations" locale="es-ES"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Writes/sec" sampleRate="PT15S" unit="CountPerSecond">
	        <annotation displayName="Disk write operations" locale="es-ES"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
	        <annotation displayName="Disk speed" locale="es-ES"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Read Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
	        <annotation displayName="Disk read speed" locale="es-ES"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Write Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
	        <annotation displayName="Disk write speed" locale="es-ES"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Queue Length" sampleRate="PT15S" unit="Count">
	        <annotation displayName="Disk average queue length" locale="es-ES"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Read Queue Length" sampleRate="PT15S" unit="Count">
	        <annotation displayName="Disk average read queue length" locale="es-ES"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Write Queue Length" sampleRate="PT15S" unit="Count">
	        <annotation displayName="Disk average write queue length" locale="es-ES"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(_Total)\% Free Space" sampleRate="PT15S" unit="Percent">
	        <annotation displayName="Disk free space (percentage)" locale="es-ES"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(_Total)\Free Megabytes" sampleRate="PT15S" unit="Count">
	        <annotation displayName="Disk free space (MB)" locale="es-ES"/>
	      </PerformanceCounterConfiguration>
	    </PerformanceCounters>
		<Metrics resourceId="(Update with resource ID for the VM)" >
	        <MetricAggregation scheduledTransferPeriod="PT1H"/>
	        <MetricAggregation scheduledTransferPeriod="PT1M"/>
	    </Metrics>
	    <WindowsEventLog scheduledTransferPeriod="PT1M">
	      <DataSource name="Application!*[System[(Level = 1 or Level = 2)]]"/>
	      <DataSource name="Security!*[System[(Level = 1 or Level = 2)]"/>
	      <DataSource name="System!*[System[(Level = 1 or Level = 2)]]"/>
	    </WindowsEventLog>
	      </DiagnosticMonitorConfiguration>
	    </WadCfg>
	    <StorageAccount>(Update with diagnostics storage account name)</StorageAccount>
	</PublicConfig>
	```

## Pasos siguientes
- Para obtener orientación adicional sobre el uso de la funcionalidad Diagnósticos de Azure y otras técnicas para solucionar problemas, consulte [Habilitación de diagnósticos en Servicios en la nube y Máquinas virtuales de Azure](cloud-services-dotnet-diagnostics.md).
- En el [Esquema de configuración de diagnósticos](https://msdn.microsoft.com/library/azure/mt634524.aspx) se explican las distintas opciones de configuración XML para la extensión de diagnósticos.

<!---HONumber=AcomDC_0121_2016-->