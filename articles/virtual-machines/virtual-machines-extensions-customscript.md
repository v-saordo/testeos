<properties
   pageTitle="Extensión de script personalizada en una máquina virtual Windows | Microsoft Azure"
   description="Automatizar tareas de configuración de máquina virtual de Azure mediante la extensión de script personalizada para ejecutar scripts de PowerShell en una máquina virtual remota de Windows"
   services="virtual-machines"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="08/06/2015"
   ms.author="kundanap"/>

# Extensión de script personalizada para máquinas virtuales Windows

Este artículo ofrece información general del uso de la extensión de script personalizada en máquinas virtuales Windows mediante los cmdlets de Azure PowerShell.

Las extensiones de máquina virtual (VM) se crean por Microsoft y editores de confianza de terceros para extender la funcionalidad de la máquina virtual. Para obtener información general de las extensiones de máquina virtual, vea [Características y extensiones de máquina virtual de Azure](virtual-machines-extensions-features.md).

Vínculo:
[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [Modelo del Administrador de recursos](virtual-machines-extensions-customscript%20-with%20template.md).


## Introducción a la extensión de script personalizada

La extensión de script personalizada para Windows le permite ejecutar scripts de PowerShell en una máquina virtual remota, sin iniciar sesión en ella. Las secuencias de comandos se pueden ejecutar después de aprovisionar la máquina virtual o en cualquier momento durante el ciclo de vida de la máquina virtual sin necesidad de abrir los puertos adicionales en la máquina virtual. El caso de uso más común de la extensión de script personalizada incluye la ejecución, la instalación y la configuración de software adicional en la máquina virtual después de su aprovisionamiento.

### Requisitos previos para ejecutar la extensión de script personalizada

1. Instale los cmdlets de Azure PowerShell versión 0.8.0 (o posterior) desde <a href="http://azure.microsoft.com/downloads" target="_blank">aquí</a>.
2. Si los scripts se ejecutan en una máquina virtual existente, asegúrese de que el Agente de máquina virtual está habilitado en la máquina virtual; si no lo está, siga las indicaciones de este <a href="https://msdn.microsoft.com/library/azure/dn832621.aspx" target="_blank">artículo</a> para instalar uno. (Si aprovisiona la máquina virtual de la galería de Azure, los agentes de VM se habilitan de forma predeterminada; no tiene que habilitarlos).
3. Cargue las secuencias de comandos que desea ejecutar en la máquina virtual para el almacenamiento de Azure. Las secuencias de comandos pueden proceder de un único contenedor de almacenamiento o de varios.
4. El script debe crearse de forma tal que el script de entrada que inicia la extensión inicie, a su vez, otros scripts.

## Escenarios de la extensión de script personalizada

### Cargar archivos en el contenedor predeterminado

Si los scripts se encuentran en el contenedor de almacenamiento de la cuenta predeterminada de su suscripción, el siguiente ejemplo le muestra cómo puede ejecutarlos en la máquina virtual. El ContainerName es donde se cargan los scripts. Puede comprobar la cuenta de almacenamiento predeterminada mediante el comando **Get-AzureSubscription –Default**.

En el ejemplo siguiente se crea una nueva máquina virtual, pero el mismo escenario se puede ejecutar en una máquina virtual existente.

    # create a new VM in Azure.
    $vm = New-AzureVMConfig -Name $name -InstanceSize Small -ImageName $imagename
    $vm = Add-AzureProvisioningConfig -VM $vm -Windows -AdminUsername $username -Password $password
    // Add Custom Script Extension to the VM. The container name refer to the storage container which contains the file.
    $vm = Set-AzureVMCustomScriptExtension -VM $vm -ContainerName $container -FileName 'start.ps1'
    New-AzureVM -ServiceName $servicename -Location $location -VMs $vm
    #  After the VM is created, the extension downloads the script from the storage location and executes it on the VM.

    # Viewing the  script execution output.
    $vm = Get-AzureVM -ServiceName $servicename -Name $name
    # Use the position of the extension in the output as index.
    $vm.ResourceExtensionStatusList[i].ExtensionSettingStatus.SubStatusList

### Cargar archivos a un contenedor de almacenamiento no predeterminado

Este escenario muestra cómo usar un almacenamiento no predeterminado en la misma suscripción o en una suscripción diferente para cargar archivos y scripts. Aquí vamos a usar una máquina virtual existente, pero se pueden realizar las mismas operaciones al crear una máquina virtual nueva.

        Get-AzureVM -Name $name -ServiceName $servicename | Set-AzureVMCustomScriptExtension -StorageAccountName $storageaccount -StorageAccountKey $storagekey -ContainerName $container -FileName 'file1.ps1','file2.ps1' -Run 'file.ps1' | Update-AzureVM

### Carga de secuencias de comandos en varios contenedores en diferentes cuentas de almacenamiento

  Si los archivos de scripts se almacenan en varios contenedores, para ejecutar dichos scripts, debe ofrecer la dirección URL de SAS completa para los archivos.

      Get-AzureVM -Name $name -ServiceName $servicename | Set-AzureVMCustomScriptExtension -StorageAccountName $storageaccount -StorageAccountKey $storagekey -ContainerName $container -FileUri $fileUrl1, $fileUrl2 -Run 'file.ps1' | Update-AzureVM


### Agregar la extensión de script personalizada desde el Portal de Azure

Vaya a la máquina virtual en el <a href="https://portal.azure.com/ " target="_blank">Portal de Azure </a> y especifique el archivo de scripts que se debe ejecutar para agregar la extensión.

  ![][5]


### Desinstalación de la extensión de script personalizada

La extensión de la secuencia de comandos personalizada se puede desinstalar de la máquina virtual mediante el siguiente comando.

      get-azureVM -ServiceName KPTRDemo -Name KPTRDemo | Set-AzureVMCustomScriptExtension -Uninstall | Update-AzureVM

### Uso de la extensión de scripts personalizada con plantillas

Vea la documentación [aquí](virtual-machines-extensions-customscript%20-with%20template.md) para obtener información sobre el uso de la extensión de scripts personalizados con plantillas del Administrador de recursos de Azure.

<!--Image references-->
[5]: ./media/virtual-machines-extensions-customscript/addcse.png

<!---HONumber=AcomDC_1223_2015-->