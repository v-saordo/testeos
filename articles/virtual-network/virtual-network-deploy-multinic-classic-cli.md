<properties 
   pageTitle="Implementación de máquinas virtuales con varias NIC mediante la CLI de Azure en el modelo de implementación clásico | Microsoft Azure"
   description="Aprenda cómo implementar las máquinas virtuales con varias NIC mediante la CLI de Azure en el modelo de implementación clásico"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/02/2016"
   ms.author="telmos" />

#Implementar máquinas virtuales con varias NIC (clásica) mediante la CLI de Azure

[AZURE.INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-multinic-intro-include.md](../../includes/virtual-network-deploy-multinic-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](virtual-network-deploy-multinic-arm-cli.md).

[AZURE.INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

Puesto que en este momento no puede tener máquinas virtuales con una sola NIC, ni máquinas virtuales con varias NIC en el mismo servicio en la nube, se implementarán los servidores back-end en un servicio en la nube distinto de todos los demás componentes. En los pasos siguientes usaremos un servicio en la nube denominado *IaaSStory* para los recursos principales, y *IaaSStory-back-end* para los servidores back-end.

## Requisitos previos

Antes de implementar los servidores back-end, debe implementar el servicio en la nube principal con todos los recursos necesarios para este escenario. Como mínimo, debe crear una red virtual con una subred para el back-end. Consulte [Crear una red virtual mediante la CLI de Azure](virtual-networks-create-vnet-classic-cli.md) para obtener información sobre cómo implementar una red virtual.

[AZURE.INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## Implementación de máquinas virtuales back-end

Las máquinas virtuales back-end dependen de la creación de los recursos mencionados anteriormente.

- **Cuenta de almacenamiento de discos de datos**. Para mejorar el rendimiento, los discos de datos en los servidores de base de datos usarán la tecnología de unidad de estado sólido (SSD), que requiere una cuenta de almacenamiento Premium. Asegúrese de que la ubicación de Azure que implementa admita el almacenamiento Premium.
- **NIC**. Cada VM tendrá dos NIC, una para el acceso de la base de datos y otra para la administración.
- **Conjunto de disponibilidad**. Todos los servidores de base de datos se agregarán al conjunto de disponibilidad único para asegurarse de que al menos una de las máquinas virtuales está activa y ejecutándose durante el mantenimiento. 

### Paso 1: inicio del script

Puede descargar el script de Bash completo que haya usado [aquí](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/virtual-network-deploy-multinic-classic-cli.sh). Siga los pasos siguientes para cambiar el script para que funcione en su entorno.

1. Cambie los valores de las variables siguientes en función de su grupo de recursos existente implementado anteriormente en [Requisitos previos](#Prerequisites).

		location="useast2"
		vnetName="WTestVNet"
		backendSubnetName="BackEnd"

2. Cambie los valores de las variables siguientes según los valores que desee usar para la implementación back-end.

		backendCSName="IaaSStory-Backend"
		prmStorageAccountName="iaasstoryprmstorage"
		image="0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1"
		avSetName="ASDB"
		vmSize="Standard_DS3"
		diskSize=127
		vmNamePrefix="DB"
		osDiskName="osdiskdb"
		dataDiskPrefix="db"
		dataDiskName="datadisk"
		ipAddressPrefix="192.168.2."
		username='adminuser'
		password='adminP@ssw0rd'
		numberOfVMs=2

### Paso 2: creación de los recursos necesarios para las máquinas virtuales

1. Cree un nuevo servicio en la nube para todas las máquinas virtuales de back-end. Observe cómo se usa la variable `$backendCSName` para el nombre del grupo de recursos, y `$location` para la región de Azure.

		azure service create --serviceName $backendCSName \
		    --location $location

2. Cree una cuenta de almacenamiento Premium para los discos de datos y sistemas operativos que usarán sus máquinas virtuales.

		azure storage account create $prmStorageAccountName \
		    --location $location \
		    --type PLRS 

### Paso 3: crear máquinas virtuales con varias NIC

1. Inicie un bucle para crear varias máquinas virtuales, según las variables `numberOfVMs`.

		for ((suffixNumber=1;suffixNumber<=numberOfVMs;suffixNumber++));
		do

2. Para cada máquina virtual, especifique el nombre y la dirección IP de cada una de las dos NIC.

		    nic1Name=$vmNamePrefix$suffixNumber-DA
		    x=$((suffixNumber+3))
		    ipAddress1=$ipAddressPrefix$x
		
		    nic2Name=$vmNamePrefix$suffixNumber-RA
		    x=$((suffixNumber+53))
		    ipAddress2=$ipAddressPrefix$x

4. Cree la máquina virtual. Tenga en cuenta que deberá usar el parámetro `--nic-config`, el cual contiene una lista de todas las NIC con nombre, subred y dirección IP.

		    azure vm create $backendCSName $image $username $password \
		        --connect $backendCSName \
		        --vm-name $vmNamePrefix$suffixNumber \
		        --vm-size $vmSize \
		        --availability-set $avSetName \
		        --blob-url $prmStorageAccountName.blob.core.windows.net/vhds/$osDiskName$suffixNumber.vhd \
		        --virtual-network-name $vnetName \
		        --subnet-names $backendSubnetName \
		        --nic-config $nic1Name:$backendSubnetName:$ipAddress1::,$nic2Name:$backendSubnetName:$ipAddress2::

5. Cree dos discos de datos por cada máquina virtual.

		    azure vm disk attach-new $vmNamePrefix$suffixNumber \
		        $diskSize \
		        vhds/$dataDiskPrefix$suffixNumber$dataDiskName-1.vhd
		
		    azure vm disk attach-new $vmNamePrefix$suffixNumber \
		        $diskSize \
		        vhds/$dataDiskPrefix$suffixNumber$dataDiskName-2.vhd
		done

### Paso 4: ejecución del script

Ahora que descargó y cambió el script según sus necesidades, ejecute el script para crear las máquinas virtuales de la base de datos back-end con varias NIC.

1. Guarde el script y ejecútelo desde su terminal de **Bash**. Verá el resultado inicial, tal como se muestra a continuación.

		info:    Executing command service create
		info:    Creating cloud service
		data:    Cloud service name IaaSStory-Backend
		info:    service create command OK
		info:    Executing command storage account create
		info:    Creating storage account
		info:    storage account create command OK
		info:    Executing command vm create
		info:    Looking up image 0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1
		info:    Looking up virtual network
		info:    Looking up cloud service
		info:    Getting cloud service properties
		info:    Looking up deployment
		info:    Creating VM

2. Después de unos minutos, la ejecución finalizará y verá el resto del resultado como se muestra a continuación.

		info:    OK
		info:    vm create command OK
		info:    Executing command vm disk attach-new
		info:    Getting virtual machines
		info:    Adding Data-Disk
		info:    vm disk attach-new command OK
		info:    Executing command vm disk attach-new
		info:    Getting virtual machines
		info:    Adding Data-Disk
		info:    vm disk attach-new command OK
		info:    Executing command vm create
		info:    Looking up image 0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1
		info:    Looking up virtual network
		info:    Looking up cloud service
		info:    Getting cloud service properties
		info:    Looking up deployment
		info:    Creating VM
		info:    OK
		info:    vm create command OK
		info:    Executing command vm disk attach-new
		info:    Getting virtual machines
		info:    Adding Data-Disk
		info:    vm disk attach-new command OK
		info:    Executing command vm disk attach-new
		info:    Getting virtual machines
		info:    Adding Data-Disk
		info:    vm disk attach-new command OK

<!---HONumber=AcomDC_0211_2016-->