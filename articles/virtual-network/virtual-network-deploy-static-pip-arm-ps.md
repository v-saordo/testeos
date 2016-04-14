<properties 
   pageTitle="Implementar una máquina virtual con una dirección IP pública estática mediante PowerShell en el Administrador de recursos | Microsoft Azure"
   description="Aprender a implementar las máquinas virtuales con una dirección IP pública estática mediante PowerShell en el Administrador de recursos"
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
   ms.date="12/15/2015"
   ms.author="telmos" />

# Implementar una máquina virtual con una dirección IP pública estática mediante PowerShell

[AZURE.INCLUDE [virtual-network-deploy-static-pip-arm-selectors-include.md](../../includes/virtual-network-deploy-static-pip-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementación clásica.

[AZURE.INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## Paso 1: inicio del script

Puede descargar el script de PowerShell completo que ha usado [aquí](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/virtual-network-deploy-static-pip-arm-ps.ps1). Siga los pasos siguientes para cambiar el script para que funcione en su entorno.

1. Cambie los valores de las variables siguientes según los valores que desee usar para la implementación. Los valores siguientes se asignan al escenario usado en este documento.

		# Set variables resource group
		$rgName                = "IaaSStory"
		$location              = "West US"
		
		# Set variables for VNet
		$vnetName              = "WTestVNet"
		$vnetPrefix            = "192.168.0.0/16"
		$subnetName            = "FrontEnd"
		$subnetPrefix          = "192.168.1.0/24"
		
		# Set variables for storage
		$stdStorageAccountName = "iaasstorystorage"
		
		# Set variables for VM
		$vmSize                = "Standard_A1"
		$diskSize              = 127
		$publisher             = "MicrosoftWindowsServer"
		$offer                 = "WindowsServer"
		$sku                   = "2012-R2-Datacenter"
		$version               = "latest"
		$vmName                = "WEB1"
		$osDiskName            = "osdisk"
		$nicName               = "NICWEB1"
		$privateIPAddress      = "192.168.1.101"
		$pipName               = "PIPWEB1"
		$dnsName               = "iaasstoryws1"

## Paso 2: creación de los recursos necesarios para las máquinas virtuales

Antes de crear una máquina virtual, necesitará un grupo de recursos, red virtual, dirección IP pública y NIC que se usará con la máquina virtual.

1. Cree un nuevo grupo de recursos.

		New-AzureRmResourceGroup -Name $rgName -Location $location
		
2. Cree la red virtual y subred.

		$vnet = New-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name $vnetName `
		    -AddressPrefix $vnetPrefix -Location $location   
		
		Add-AzureRmVirtualNetworkSubnetConfig -Name $subnetName `
		    -VirtualNetwork $vnet -AddressPrefix $subnetPrefix
		
		Set-AzureRmVirtualNetwork -VirtualNetwork $vnet 

3. Cree el recurso de IP pública.

		$pip = New-AzureRmPublicIpAddress -Name $pipName -ResourceGroupName $rgName `
		    -AllocationMethod Static -DomainNameLabel $dnsName -Location $location

4. Cree la interfaz de red (NIC) para la máquina virtual en la subred creada anteriormente, con la dirección IP pública. Observe el primer cmdlet que recupera la red virtual de Azure; esto es necesario porque se ejecutó el comando **Set-AzureRmVirtualNetwork** para cambiar la red virtual existente.

		$vnet = Get-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
		$subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name $subnetName
		$nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName `
		    -Subnet $subnet -Location $location -PrivateIpAddress $privateIPAddress `
		    -PublicIpAddress $pip

5. Cree una cuenta de almacenamiento para hospedar la unidad del sistema operativo de la máquina virtual.

		$stdStorageAccount = New-AzureRmStorageAccount -Name $stdStorageAccountName `
		    -ResourceGroupName $rgName -Type Standard_LRS -Location $location

## Paso 3: creación de la máquina virtual 

Ahora que todos los recursos necesarios están en su lugar, puede crear una nueva máquina virtual.

1. Cree el objeto de configuración de la máquina virtual.

		$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize 

1. Obtenga las credenciales para la cuenta de administrador local de la máquina virtual.

		$cred = Get-Credential -Message "Type the name and password for the local administrator account."

2. Cree un objeto de configuración de máquina virtual.

		$vmConfig = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $vmName `
    		-Credential $cred -ProvisionVMAgent -EnableAutoUpdate

3. Establezca la imagen de sistema operativo para la máquina virtual.

		$vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -PublisherName $publisher `
    		-Offer $offer -Skus $sku -Version $version

4. Configure el disco del sistema operativo.

		$osVhdUri = $stdStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $osDiskName + ".vhd"
		$vmConfig = Set-AzureRmVMOSDisk -VM $vmConfig -Name $osDiskName -VhdUri $osVhdUri -CreateOption fromImage

5. Agregue la NIC a la máquina virtual.

		$vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id -Primary

6. Cree la máquina virtual.

		New-AzureRmVM -VM $vmConfig -ResourceGroupName $rgName -Location $location

7. Guarde el archivo de script.

## Paso 4: ejecución del script

Después de realizar los cambios necesarios y comprender el script anterior, ejecute el script.

1. Desde una consola de PowerShell o PowerShell ISE, ejecute el script anterior.
2. Después de unos minutos, se debería mostrar la salida siguiente.

		ResourceGroupName : IaaSStory
		Location          : westus
		ProvisioningState : Succeeded
		Tags              : 
		ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory
				
		AddressSpace      : Microsoft.Azure.Commands.Network.Models.PSAddressSpace
		DhcpOptions       : Microsoft.Azure.Commands.Network.Models.PSDhcpOptions
		Subnets           : {FrontEnd}
		ProvisioningState : Succeeded
		AddressSpaceText  : {
		                      "AddressPrefixes": [
		                        "192.168.0.0/16"
		                      ]
		                    }
		DhcpOptionsText   : {}
		SubnetsText       : [
		                      {
		                        "Name": "FrontEnd",
		                        "AddressPrefix": "192.168.1.0/24"
		                      }
		                    ]
		ResourceGroupName : IaaSStory
		Location          : westus
		ResourceGuid      : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
		Tag               : {}
		TagsTable         : 
		Name              : WTestVNet
		Etag              : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
		Id                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory/providers/Microsoft.Network/virtualNetworks/WTestVNet
		
		AddressSpace      : Microsoft.Azure.Commands.Network.Models.PSAddressSpace
		DhcpOptions       : Microsoft.Azure.Commands.Network.Models.PSDhcpOptions
		Subnets           : {FrontEnd}
		ProvisioningState : Succeeded
		AddressSpaceText  : {
		                      "AddressPrefixes": [
		                        "192.168.0.0/16"
		                      ]
		                    }
		DhcpOptionsText   : {
		                      "DnsServers": []
		                    }
		SubnetsText       : [
		                      {
		                        "Name": "FrontEnd",
		                        "Etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
		                        "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory/providers/Microsoft.Network/virtualNetworks/WTestVNet/subnets/FrontEnd",
		                        "AddressPrefix": "192.168.1.0/24",
		                        "IpConfigurations": [],
		                        "ProvisioningState": "Succeeded"
		                      }
		                    ]
		ResourceGroupName : IaaSStory
		Location          : westus
		ResourceGuid      : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
		Tag               : {}
		TagsTable         : 
		Name              : WTestVNet
		Etag              : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
		Id                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory/providers/Microsoft.Network/virtualNetworks/WTestVNet
				
		TrackingOperationId : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
		RequestId           : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
		Status              : Succeeded
		StatusCode          : OK
		Output              : 
		StartTime           : 1/12/2016 12:57:56 PM -08:00
		EndTime             : 1/12/2016 12:59:13 PM -08:00
		Error               : 
		ErrorText           : 

   

<!---HONumber=AcomDC_0114_2016-->