<properties 
   pageTitle="Implementar máquinas virtuales con varias NIC mediante PowerShell en el Administrador de recursos | Microsoft Azure"
   description="Aprenda a implementar máquinas virtuales con varias NIC mediante PowerShell en el Administrador de recursos"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/02/2016"
   ms.author="telmos" />

#Implementación de máquinas virtuales con varias NIC mediante PowerShell

[AZURE.INCLUDE [virtual-network-deploy-multinic-arm-selectors-include.md](../../includes/virtual-network-deploy-multinic-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-multinic-intro-include.md](../../includes/virtual-network-deploy-multinic-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-network-deploy-multinic-classic-ps.md).

[AZURE.INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

Puesto que en este momento no puede tener máquinas virtuales con una sola NIC y las máquinas virtuales con varias NIC en el mismo grupo de recursos, se implementarán los servidores back-end en un grupo de recursos distinto de todos los demás componentes. Los pasos siguientes usan un grupo de recursos denominado *IaaSStory* para el grupo de recursos principal y *IaaSStory-back-end* para los servidores back-end.

## Requisitos previos

Antes de implementar los servidores back-end, debe implementar el grupo de recursos principal con todos los recursos necesarios para este escenario. Para implementar estos recursos, siga estos pasos.

1. Vaya a [la página de plantilla](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC).
2. En la página de la plantilla, a la derecha de **Grupo de recursos primarios**, haga clic en **Implementar en Azure**.
3. Si es necesario, cambie los valores de parámetro y siga los pasos en el Portal de vista previa de Azure para implementar el grupo de recursos.

> [AZURE.IMPORTANT] Asegúrese de que los nombres de cuenta de almacenamiento sean únicos. No puede tener nombres de cuenta de almacenamiento duplicados en Azure.

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## Implementación de máquinas virtuales back-end

Las máquinas virtuales back-end dependen de la creación de los recursos mencionados anteriormente.

- **Cuenta de almacenamiento de discos de datos**. Para mejorar el rendimiento, los discos de datos en los servidores de base de datos usarán la tecnología de unidad de estado sólido (SSD), que requiere una cuenta de almacenamiento Premium. Asegúrese de que la ubicación de Azure que implementa admita el almacenamiento Premium.
- **NIC**. Cada VM tendrá dos NIC, una para el acceso de la base de datos y otra para la administración.
- **Conjunto de disponibilidad**. Todos los servidores de base de datos se agregarán al conjunto de disponibilidad único para asegurarse de que al menos una de las máquinas virtuales está activa y ejecutándose durante el mantenimiento.  

### Paso 1: inicio del script

Puede descargar el script de PowerShell completo que ha usado [aquí](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/arm/virtual-network-deploy-multinic-arm-ps.ps1). Siga los pasos siguientes para cambiar el script para que funcione en su entorno.

[AZURE.INCLUDE [powershell-preview-include.md](../../includes/powershell-preview-include.md)]

1. Cambie los valores de las variables siguientes en función de su grupo de recursos existente implementado anteriormente en [Requisitos previos](#Prerequisites).

		$existingRGName        = "IaaSStory"
		$location              = "West US"
		$vnetName              = "WTestVNet"
		$backendSubnetName     = "BackEnd"
		$remoteAccessNSGName   = "NSG-RemoteAccess"
		$stdStorageAccountName = "wtestvnetstoragestd"

2. Cambie los valores de las variables siguientes según los valores que desee usar para la implementación back-end.

		$backendRGName         = "IaaSStory-Backend"
		$prmStorageAccountName = "wtestvnetstorageprm"
		$avSetName             = "ASDB"
		$vmSize                = "Standard_DS3"
		$publisher             = "MicrosoftSQLServer"
		$offer                 = "SQL2014SP1-WS2012R2"
		$sku                   = "Standard"
		$version               = "latest"
		$vmNamePrefix          = "DB"
		$osDiskPrefix          = "osdiskdb"
		$dataDiskPrefix        = "datadisk"
		$nicNamePrefix         = "NICDB"
		$ipAddressPrefix       = "192.168.2."
		$numberOfVMs           = 2

3. Recupere los recursos existentes necesarios para su implementación.

		$vnet                  = Get-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $existingRGName
		$backendSubnet         = $vnet.Subnets|?{$_.Name -eq $backendSubnetName}
		$remoteAccessNSG       = Get-AzureRmNetworkSecurityGroup -Name $remoteAccessNSGName -ResourceGroupName $existingRGName
		$stdStorageAccount     = Get-AzureRmStorageAccount -Name $stdStorageAccountName -ResourceGroupName $existingRGName

### Paso 2: creación de los recursos necesarios para las máquinas virtuales

Debe crear un nuevo grupo de recursos, una cuenta de almacenamiento para los discos de datos y un conjunto de disponibilidad para todas las máquinas virtuales. Asimismo, también necesita las credenciales de cuenta de administrador local para cada máquina virtual. Para crear estos recursos, realice los siguientes pasos.

1. Cree un nuevo grupo de recursos.

		New-AzureRmResourceGroup -Name $backendRGName -Location $location

2. Cree una nueva cuenta de almacenamiento premium en el grupo de recursos creado anteriormente.

		$prmStorageAccount = New-AzureRmStorageAccount -Name $prmStorageAccountName `
			-ResourceGroupName $backendRGName -Type Premium_LRS -Location $location

3. Cree un conjunto de disponibilidad nuevo.

		$avSet = New-AzureRmAvailabilitySet -Name $avSetName -ResourceGroupName $backendRGName -Location $location

4. Consiga las credenciales de la cuenta de administrador local que usará para cada máquina virtual.

		$cred = Get-Credential -Message "Type the name and password for the local administrator account."

### Paso 3: creación de las NIC y máquinas virtuales back-end

Debe usar un bucle para crear tantas máquinas virtuales como desee y así crear las NIC y las máquinas virtuales dentro del bucle. Para crear las NIC y las máquinas virtuales, realice los siguientes pasos.

1. Inicie un bucle `for` para repetir los comandos que se usarán para crear una máquina virtual y dos NIC tantas veces como sea necesario, según el valor de la variable `$numberOfVMs`.

		for ($suffixNumber = 1; $suffixNumber -le $numberOfVMs; $suffixNumber++){

2. Cree la NIC usada para obtener acceso a la base de datos.
		
		    $nic1Name = $nicNamePrefix + $suffixNumber + "-DA"
		    $ipAddress1 = $ipAddressPrefix + ($suffixNumber + 3)
		    $nic1 = New-AzureRmNetworkInterface -Name $nic1Name -ResourceGroupName $backendRGName `
				-Location $location -SubnetId $backendSubnet.Id -PrivateIpAddress $ipAddress1

3. Cree la NIC usada para obtener acceso remoto. Compruebe que esta NIC tiene un GSN asociado a ella.

		    $nic2Name = $nicNamePrefix + $suffixNumber + "-RA"
		    $ipAddress2 = $ipAddressPrefix + (53 + $suffixNumber)
		    $nic2 = New-AzureRmNetworkInterface -Name $nic2Name -ResourceGroupName $backendRGName `
				-Location $location -SubnetId $backendSubnet.Id -PrivateIpAddress $ipAddress2 `
				-NetworkSecurityGroupId $remoteAccessNSG.Id

4. Cree un objeto `vmConfig`.

		    $vmName = $vmNamePrefix + $suffixNumber
		    $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avSet.Id

5. Cree dos discos de datos por máquina virtual. Tenga en cuenta que los discos de datos están en la cuenta de almacenamiento premium creada anteriormente.

		    $dataDisk1Name = $vmName + "-" + $dataDiskSuffix + "-1"    
		    $data1VhdUri = $prmStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $dataDisk1Name + ".vhd"
		    Add-AzureRmVMDataDisk -VM $vmConfig -Name $dataDisk1Name -DiskSizeInGB $diskSize `
				-VhdUri $data1VhdUri -CreateOption empty -Lun 0
		
		    $dataDisk2Name = $vmName + "-" + $dataDiskSuffix + "-2"    
		    $data2VhdUri = $prmStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $dataDisk2Name + ".vhd"
		    Add-AzureRmVMDataDisk -VM $vmConfig -Name $dataDisk2Name -DiskSizeInGB $diskSize `
				-VhdUri $data2VhdUri -CreateOption empty -Lun 1

6. Configure el sistema operativo y la imagen que se usará para la máquina virtual.
		    
		    $vmConfig = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
		    $vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -PublisherName $publisher -Offer $offer -Skus $sku -Version $version

7. Agregue las dos NIC creadas anteriormente al objeto `vmConfig`.

		    $vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic1.Id -Primary
		    $vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic2.Id

8. Cree el disco del sistema operativo y la máquina virtual. Compruebe que `}` finaliza el bucle `for`.

		    $osDiskName = $vmName + "-" + $osDiskSuffix
		    $osVhdUri = $stdStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $osDiskName + ".vhd"
		    $vmConfig = Set-AzureRmVMOSDisk -VM $vmConfig -Name $osDiskName -VhdUri $osVhdUri -CreateOption fromImage
		    New-AzureRmVM -VM $vmConfig -ResourceGroupName $backendRGName -Location $location
		}

### Paso 4: ejecución del script

Ahora que descargó y cambió el script según sus necesidades, ejecute el script para crear las máquinas virtuales de la base de datos de back-end con varias NIC.

1. Guarde el script y ejecútelo desde el símbolo del sistema **PowerShell** o desde **PowerShell ISE**. Verá el resultado inicial, tal como se muestra a continuación.

		ResourceGroupName : IaaSStory-Backend
		Location          : westus
		ProvisioningState : Succeeded
		Tags              : 
		Permissions       : 
		                    Actions  NotActions
		                    =======  ==========
		                    *                  
		                    
		ResourceId        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/IaaSStory-Backend

2. Después de unos minutos, rellene la petición de credenciales y haga clic en **Aceptar**. La salida siguiente representa una sola máquina virtual. Tenga en cuenta que todo el proceso tardará unos 8 minutos en completarse.

		ResourceGroupName            : 
		Id                           : 
		Name                         : DB2
		Type                         : 
		Location                     : 
		Tags                         : 
		TagsText                     : null
		AvailabilitySetReference     : Microsoft.Azure.Management.Compute.Models.AvailabilitySetReference
		AvailabilitySetReferenceText : {
		                                 "ReferenceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend/providers/
		                               Microsoft.Compute/availabilitySets/ASDB"
		                               }
		Extensions                   : 
		ExtensionsText               : null
		HardwareProfile              : Microsoft.Azure.Management.Compute.Models.HardwareProfile
		HardwareProfileText          : {
		                                 "VirtualMachineSize": "Standard_DS3"
		                               }
		InstanceView                 : 
		InstanceViewText             : null
		NetworkProfile               : 
		NetworkProfileText           : null
		OSProfile                    : 
		OSProfileText                : null
		Plan                         : 
		PlanText                     : null
		ProvisioningState            : 
		StorageProfile               : Microsoft.Azure.Management.Compute.Models.StorageProfile
		StorageProfileText           : {
		                                 "DataDisks": [
		                                   {
		                                     "Lun": 0,
		                                     "Caching": null,
		                                     "CreateOption": "empty",
		                                     "DiskSizeGB": 127,
		                                     "Name": "DB2-datadisk-1",
		                                     "SourceImage": null,
		                                     "VirtualHardDisk": {
		                                       "Uri": "https://wtestvnetstorageprm.blob.core.windows.net/vhds/DB2-datadisk-1.vhd"
		                                     }
		                                   }
		                                 ],
		                                 "ImageReference": null,
		                                 "OSDisk": null
		                               }
		DataDiskNames                : {DB2-datadisk-1}
		NetworkInterfaceIDs          : 
		RequestId                    : 
		StatusCode                   : 0
		
		
		ResourceGroupName            : 
		Id                           : 
		Name                         : DB2
		Type                         : 
		Location                     : 
		Tags                         : 
		TagsText                     : null
		AvailabilitySetReference     : Microsoft.Azure.Management.Compute.Models.AvailabilitySetReference
		AvailabilitySetReferenceText : {
		                                 "ReferenceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend/providers/
		                               Microsoft.Compute/availabilitySets/ASDB"
		                               }
		Extensions                   : 
		ExtensionsText               : null
		HardwareProfile              : Microsoft.Azure.Management.Compute.Models.HardwareProfile
		HardwareProfileText          : {
		                                 "VirtualMachineSize": "Standard_DS3"
		                               }
		InstanceView                 : 
		InstanceViewText             : null
		NetworkProfile               : 
		NetworkProfileText           : null
		OSProfile                    : 
		OSProfileText                : null
		Plan                         : 
		PlanText                     : null
		ProvisioningState            : 
		StorageProfile               : Microsoft.Azure.Management.Compute.Models.StorageProfile
		StorageProfileText           : {
		                                 "DataDisks": [
		                                   {
		                                     "Lun": 0,
		                                     "Caching": null,
		                                     "CreateOption": "empty",
		                                     "DiskSizeGB": 127,
		                                     "Name": "DB2-datadisk-1",
		                                     "SourceImage": null,
		                                     "VirtualHardDisk": {
		                                       "Uri": "https://wtestvnetstorageprm.blob.core.windows.net/vhds/DB2-datadisk-1.vhd"
		                                     }
		                                   },
		                                   {
		                                     "Lun": 1,
		                                     "Caching": null,
		                                     "CreateOption": "empty",
		                                     "DiskSizeGB": 127,
		                                     "Name": "DB2-datadisk-2",
		                                     "SourceImage": null,
		                                     "VirtualHardDisk": {
		                                       "Uri": "https://wtestvnetstorageprm.blob.core.windows.net/vhds/DB2-datadisk-2.vhd"
		                                     }
		                                   }
		                                 ],
		                                 "ImageReference": null,
		                                 "OSDisk": null
		                               }
		DataDiskNames                : {DB2-datadisk-1, DB2-datadisk-2}
		NetworkInterfaceIDs          : 
		RequestId                    : 
		StatusCode                   : 0
		
		
		EndTime             : 10/30/2015 9:30:03 AM -08:00
		Error               : 
		Output              : 
		StartTime           : 10/30/2015 9:22:54 AM -08:00
		Status              : Succeeded
		TrackingOperationId : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
		RequestId           : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
		StatusCode          : OK

<!---HONumber=AcomDC_0211_2016-->