<properties 
   pageTitle="Implementación de máquinas virtuales con varias NIC mediante PowerShell en el modelo de implementación clásico | Microsoft Azure"
   description="Aprenda a implementar máquinas virtuales con varias NIC mediante PowerShell en el modelo de implementación clásico"
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

#Implementar máquinas virtuales con varias NIC (clásica) mediante PowerShell

[AZURE.INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-multinic-intro-include.md](../../includes/virtual-network-deploy-multinic-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](virtual-network-deploy-multinic-arm-ps.md).

[AZURE.INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

Puesto que en este momento no puede tener máquinas virtuales con una sola NIC, ni máquinas virtuales con varias NIC en el mismo servicio en la nube, se implementarán los servidores back-end en un servicio en la nube distinto de todos los demás componentes. En los pasos siguientes usaremos un servicio en la nube denominado *IaaSStory* para los recursos principales, y *IaaSStory-back-end* para los servidores back-end.

## Requisitos previos

Antes de implementar los servidores back-end, debe implementar el servicio en la nube principal con todos los recursos necesarios para este escenario. Como mínimo, debe crear una red virtual con una subred para el back-end. Consulte [Crear una red virtual mediante PowerShell](virtual-networks-create-vnet-classic-netcfg-ps.md) para obtener información sobre cómo implementar una red virtual.

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## Implementación de máquinas virtuales back-end

Las máquinas virtuales back-end dependen de la creación de los recursos mencionados anteriormente.

- **Subred de back-end**. Los servidores de bases de datos formarán parte de una subred independiente para separar el tráfico. El siguiente script debe tener su subred en una red virtual denominada *WTestVnet*.
- **Cuenta de almacenamiento en discos de datos**. Para mejorar el rendimiento, los discos de datos en los servidores de base de datos usarán la tecnología de unidad de estado sólido (SSD), que requiere una cuenta de almacenamiento Premium. Asegúrese de que la ubicación de Azure que implementa admita el almacenamiento Premium.
- **Conjunto de disponibilidad**. Todos los servidores de base de datos se agregarán al conjunto de disponibilidad único para asegurarse de que al menos una de las máquinas virtuales está activa y ejecutándose durante el mantenimiento. 

### Paso 1: inicio del script

Puede descargar el script de PowerShell completo que ha usado [aquí](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/virtual-network-deploy-multinic-classic-ps.ps1). Siga los pasos siguientes para cambiar el script para que funcione en su entorno.

1. Cambie los valores de las variables siguientes en función de su grupo de recursos existente implementado anteriormente en [Requisitos previos](#Prerequisites).

		$location              = "West US"
		$vnetName              = "WTestVNet"
		$backendSubnetName     = "BackEnd"

2. Cambie los valores de las variables siguientes según los valores que desee usar para la implementación back-end.

		$backendCSName         = "IaaSStory-Backend"
		$prmStorageAccountName = "iaasstoryprmstorage"
		$avSetName             = "ASDB"
		$vmSize                = "Standard_DS3"
		$diskSize              = 127
		$vmNamePrefix          = "DB"
		$dataDiskSuffix        = "datadisk"
		$ipAddressPrefix       = "192.168.2."
		$numberOfVMs           = 2

### Paso 2: creación de los recursos necesarios para las máquinas virtuales

Debe crear un nuevo servicio en la nube y una cuenta de almacenamiento para los discos de datos de todas las máquinas virtuales. Asimismo, también debe especificar una imagen y una cuenta de administrador local para las máquinas virtuales. Para crear estos recursos, realice los siguientes pasos.

1. Cree un nuevo servicio en la nube.

		New-AzureService -ServiceName $backendCSName -Location $location

2. Cree una cuenta de Almacenamiento premium.

		New-AzureStorageAccount -StorageAccountName $prmStorageAccountName `
		    -Location $location `
		    -Type Premium_LRS

3. Establezca la cuenta de almacenamiento creada anteriormente como la cuenta de almacenamiento actual para la suscripción.

		$subscription = Get-AzureSubscription `
		    | where {$_.IsCurrent -eq $true}  
		Set-AzureSubscription -SubscriptionName $subscription.SubscriptionName `
		    -CurrentStorageAccountName $prmStorageAccountName

4. Seleccione una imagen para la máquina virtual.

		$image = Get-AzureVMImage `
		    | where{$_.ImageFamily -eq "SQL Server 2014 RTM Web on Windows Server 2012 R2"} `
		    | sort PublishedDate -Descending `
		    | select -ExpandProperty ImageName -First 1

5. Establezca las credenciales de la cuenta de administrador local

		$cred = Get-Credential -Message "Enter username and password for local admin account"

### Paso 3: crear máquinas virtuales

Debe usar un bucle para crear tantas máquinas virtuales como desee y así poder crear las NIC y las máquinas virtuales dentro del bucle. Para crear las NIC y las máquinas virtuales, realice los siguientes pasos.

1. Inicie un bucle `for` para repetir los comandos que se usarán para crear una máquina virtual y dos NIC tantas veces como sea necesario, según el valor de la variable `$numberOfVMs`.

		for ($suffixNumber = 1; $suffixNumber -le $numberOfVMs; $suffixNumber++){

2. Cree un objeto `VMConfig` especificando la imagen, el tamaño y el conjunto de disponibilidad para la máquina virtual.

		    $vmName = $vmNamePrefix + $suffixNumber
		    $vmConfig = New-AzureVMConfig -Name $vmName `
		                    -ImageName $image `
		                    -InstanceSize $vmSize `
		                    -AvailabilitySetName $avSetName  

3. Aprovisione la máquina virtual como una máquina virtual de Windows.

		    Add-AzureProvisioningConfig -VM $vmConfig -Windows `
		        -AdminUsername $cred.UserName `
		        -Password $cred.Password

4. Establezca la NIC predeterminada y asígnela a una dirección IP estática.

		    Set-AzureSubnet -SubnetNames $backendSubnetName -VM $vmConfig
		    Set-AzureStaticVNetIP -IPAddress ($ipAddressPrefix+$suffixNumber+3) -VM $vmConfig

5. Agregue una segunda NIC para cada máquina virtual.

		    Add-AzureNetworkInterfaceConfig -Name ("RemoteAccessNIC"+$suffixNumber) `
		        -SubnetName $backendSubnetName `
		        -StaticVNetIPAddress ($ipAddressPrefix+(53+$suffixNumber)) `
		        -VM $vmConfig 

6. Cree dos discos de datos para cada máquina virtual.

		    $dataDisk1Name = $vmName + "-" + $dataDiskSuffix + "-1"    
		    Add-AzureDataDisk -CreateNew -VM $vmConfig `
		        -DiskSizeInGB $diskSize `
		        -DiskLabel $dataDisk1Name `
		        -LUN 0       
		
		    $dataDisk2Name = $vmName + "-" + $dataDiskSuffix + "-2"   
		    Add-AzureDataDisk -CreateNew -VM $vmConfig `
		        -DiskSizeInGB $diskSize `
		        -DiskLabel $dataDisk2Name `
		        -LUN 1

7. Cree cada máquina virtual y finalice el bucle.

		    New-AzureVM -VM $vmConfig `
		        -ServiceName $backendCSName `
		        -Location $location `
		        -VNetName $vnetName
		}

### Paso 4: ejecución del script

Ahora que descargó y cambió el script según sus necesidades, ejecute el script para crear las máquinas virtuales de la base de datos de back-end con varias NIC.

1. Guarde el script y ejecútelo desde el símbolo del sistema **PowerShell** o desde **PowerShell ISE**. Verá el resultado inicial, tal como se muestra a continuación.

		OperationDescription    OperationId                          OperationStatus
		--------------------    -----------                          ---------------
		New-AzureService        xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded      
		New-AzureStorageAccount xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded      
		                                                                            
		WARNING: No deployment found in service: 'IaaSStory-Backend'.

2. Rellene la información necesaria cuando le soliciten las credenciales y haga clic en **Aceptar**. Aparecerá una ventana de salida.

		New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
		New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded 

<!---HONumber=AcomDC_0211_2016-->