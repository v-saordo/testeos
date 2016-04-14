<properties 
   pageTitle="Implementar una máquina virtual con una dirección IP pública estática mediante la CLI de Azure en el Administrador de recursos | Microsoft Azure"
   description="Aprender a implementar las máquinas virtuales con una dirección IP pública estática mediante la CLI de Azure en el Administrador de recursos"
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

# Implementar una máquina virtual con una dirección IP pública estática mediante la CLI de Azure

[AZURE.INCLUDE [virtual-network-deploy-static-pip-arm-selectors-include.md](../../includes/virtual-network-deploy-static-pip-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementación clásica.

[AZURE.INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

[AZURE.INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## Paso 1: inicio del script

Puede descargar el script de Bash completo que haya usado [aquí](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/virtual-network-deploy-static-pip-arm-cli.sh). Siga los pasos siguientes para cambiar el script para que funcione en su entorno.

1. Cambie los valores de las variables siguientes según los valores que desee usar para la implementación. Los valores siguientes se asignan al escenario usado en este documento.

		# Set variables for the new resource group
		rgName="IaaSStory"
		location="westus"
		
		# Set variables for VNet
		vnetName="TestVNet"
		vnetPrefix="192.168.0.0/16"
		subnetName="FrontEnd"
		subnetPrefix="192.168.1.0/24"
		
		# Set variables for storage
		stdStorageAccountName="iaasstorystorage"
		
		# Set variables for VM
		vmSize="Standard_A1"
		diskSize=127
		publisher="Canonical"
		offer="UbuntuServer"
		sku="14.04.2-LTS"
		version="latest"
		vmName="WEB1"
		osDiskName="osdisk"
		nicName="NICWEB1"
		privateIPAddress="192.168.1.101"
		username='adminuser'
		password='adminP@ssw0rd'
		pipName="PIPWEB1"
		dnsName="iaasstoryws1"

## Paso 2: creación de los recursos necesarios para las máquinas virtuales

Antes de crear una máquina virtual, necesitará un grupo de recursos, red virtual, dirección IP pública y NIC que se usará con la máquina virtual.

1. Cree un nuevo grupo de recursos.

		azure group create $rgName $location
		
2. Cree la red virtual y subred.
		
		azure network vnet create --resource-group $rgName \
		    --name $vnetName \
		    --address-prefixes $vnetPrefix \
		    --location $location
		azure network vnet subnet create --resource-group $rgName \
		    --vnet-name $vnetName \
		    --name $subnetName \
		    --address-prefix $subnetPrefix

3. Cree el recurso de IP pública.

		azure network public-ip create --resource-group $rgName \
		    --name $pipName \
		    --location $location \
		    --allocation-method Static \
		    --domain-name-label $dnsName 

4. Cree la interfaz de red (NIC) para la máquina virtual en la subred creada anteriormente, con la dirección IP pública. Observe que el primer conjunto de comandos que se usan para recuperar el **Id.** de la subred que creó anteriormente.

		subnetId="$(azure network vnet subnet show --resource-group $rgName \
		                --vnet-name $vnetName \
		                --name $subnetName|grep Id)"

		subnetId=${subnetId#*/}
		
		azure network nic create --name $nicName \
		    --resource-group $rgName \
		    --location $location \
		    --private-ip-address $privateIPAddress \
		    --subnet-id $subnetId \
		    --public-ip-name $pipName

>[AZURE.TIP]El primer comando anterior usa [grep](http://tldp.org/LDP/Bash-Beginners-Guide/html/sect_04_02.html) y [manipulación de cadenas](http://tldp.org/LDP/abs/html/string-manipulation.html) (más concretamente, la eliminación de subcadenas).

5. Cree una cuenta de almacenamiento para hospedar la unidad del sistema operativo de la máquina virtual.

		azure storage account create $stdStorageAccountName \
		    --resource-group $rgName \
		    --location $location --type LRS 

## Paso 3: creación de la máquina virtual 

Ahora que todos los recursos necesarios están en su lugar, puede crear una nueva máquina virtual.

1. Cree la máquina virtual.

		azure vm create --resource-group $rgName \
		    --name $vmName \
		    --location $location \
		    --vm-size $vmSize \
		    --subnet-id $subnetId \
		    --nic-names $nicName \
		    --os-type linux \
		    --image-urn $publisher:$offer:$sku:$version \
		    --storage-account-name $stdStorageAccountName \
		    --storage-account-container-name vhds \
		    --os-disk-vhd $osDiskName.vhd \
		    --admin-username $username \
		    --admin-password $password

2. Guarde el archivo de script.

## Paso 4: ejecución del script

Después de realizar los cambios necesarios y comprender el script anterior, ejecute el script.

1. Desde una consola de bash, ejecute el script anterior.

		sh myscript.sh

2. Después de unos minutos, se debería mostrar la salida siguiente.

		info:    Executing command group create
		info:    Getting resource group IaaSStory
		info:    Creating resource group IaaSStory
		info:    Created resource group IaaSStory
		data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/resourceGroups/IaaSStory
		data:    Name:                IaaSStory
		data:    Location:            westus
		data:    Provisioning State:  Succeeded
		data:    Tags: null
		data:
		info:    group create command OK
		info:    Executing command network vnet create
		info:    Looking up virtual network "TestVNet"
		info:    Creating virtual network "TestVNet"
		info:    Loading virtual network state
		data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/resourceGroups/IaaSStory/providers/Microsoft.Network/virtualNetworks/TestVNet
		data:    Name                            : TestVNet
		data:    Type                            : Microsoft.Network/virtualNetworks
		data:    Location                        : westus
		data:    ProvisioningState               : Succeeded
		data:    Address prefixes:
		data:      192.168.0.0/16
		info:    network vnet create command OK
		info:    Executing command network vnet subnet create
		info:    Looking up the subnet "FrontEnd"
		info:    Creating subnet "FrontEnd"
		info:    Looking up the subnet "FrontEnd"
		data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/resourceGroups/IaaSStory/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
		data:    Type                            : Microsoft.Network/virtualNetworks/subnets
		data:    ProvisioningState               : Succeeded
		data:    Name                            : FrontEnd
		data:    Address prefix                  : 192.168.1.0/24
		data:
		info:    network vnet subnet create command OK
		info:    Executing command network public-ip create
		info:    Looking up the public ip "PIPWEB1"
		info:    Creating public ip address "PIPWEB1"
		info:    Looking up the public ip "PIPWEB1"
		data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/resourceGroups/IaaSStory/providers/Microsoft.Network/publicIPAddresses/PIPWEB1
		data:    Name                            : PIPWEB1
		data:    Type                            : Microsoft.Network/publicIPAddresses
		data:    Location                        : westus
		data:    Provisioning state              : Succeeded
		data:    Allocation method               : Static
		data:    Idle timeout                    : 4
		data:    IP Address                      : 40.78.63.253
		data:    Domain name label               : iaasstoryws1
		data:    FQDN                            : iaasstoryws1.westus.cloudapp.azure.com
		info:    network public-ip create command OK
		info:    Executing command network nic create
		info:    Looking up the network interface "NICWEB1"
		info:    Looking up the public ip "PIPWEB1"
		info:    Creating network interface "NICWEB1"
		info:    Looking up the network interface "NICWEB1"
		data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/resourceGroups/IaaSStory/providers/Microsoft.Network/networkInterfaces/NICWEB1
		data:    Name                            : NICWEB1
		data:    Type                            : Microsoft.Network/networkInterfaces
		data:    Location                        : westus
		data:    Provisioning state              : Succeeded
		data:    Enable IP forwarding            : false
		data:    IP configurations:
		data:      Name                          : NIC-config
		data:      Provisioning state            : Succeeded
		data:      Public IP address             : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/resourceGroups/IaaSStory/providers/Microsoft.Network/publicIPAddresses/PIPWEB1
		data:      Private IP address            : 192.168.1.101
		data:      Private IP Allocation Method  : Static
		data:      Subnet                        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/resourceGroups/IaaSStory2/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
		data:
		info:    network nic create command OK
		info:    Executing command storage account create
		info:    Creating storage account
		info:    storage account create command OK
		info:    Executing command vm create
		info:    Looking up the VM "WEB1"
		info:    Using the VM Size "Standard_A1"
		info:    The [OS, Data] Disk or image configuration requires storage account
		info:    Looking up the storage account iaasstorystorage
		info:    Looking up the NIC "NICWEB1"
		info:    Creating VM "WEB1"
		info:    vm create command OK

<!---HONumber=AcomDC_0114_2016-->