<properties
	pageTitle="Creación de una máquina virtual de Windows | Microsoft Azure"
	description="Use las plantillas de Azure PowerShell y del Administrador de recursos para crear fácilmente una máquina virtual de Windows."
	services="virtual-machines"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/05/2016"
	ms.author="davidmu"/>

# Crear una máquina virtual de Windows con el Administrador de recursos y PowerShell

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementación clásica.

En este tema se describe cómo crear rápidamente una máquina virtual de Azure basada en Windows mediante el Administrador de recursos de Azure y PowerShell.

## Creación de la máquina virtual de Windows

Si ya ha instalado Azure PowerShell, debe tener la versión 1.0.0 (o posterior) de Azure PowerShell. Puede comprobar la versión de Azure PowerShell que ha instalado con este comando en el símbolo del sistema de Azure PowerShell.

	Get-Module azure | format-table version

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

En primer lugar, debe iniciar sesión en Azure con este comando.

	Login-AzureRmAccount

Especifique la dirección de correo electrónico de la cuenta de Azure y su contraseña en el cuadro de diálogo de inicio de sesión de Microsoft Azure.

A continuación, si tiene varias suscripciones de Azure, deberá establecer la suscripción de Azure. Para ver una lista de las suscripciones actuales, ejecute este comando.

	Get-AzureRmSubscription | sort SubscriptionName | Select SubscriptionName

Ahora, reemplace todo el contenido dentro de las comillas, incluidos los caracteres < and >, por el nombre de suscripción correcto y ejecute estos comandos.

	$subscrName="<subscription name>"
	Select-AzureRmSubscription -SubscriptionName $subscrName –Current

A continuación, deberá crear una cuenta de almacenamiento. Debe elegir un nombre único que contenga solo letras minúsculas y números. Se puede comprobar que el nombre de cuenta de almacenamiento sea único con este comando.

	Test-AzureName -Storage <Proposed storage account name>

Si este comando devuelve "False", el nombre propuesto es único.

Deberá especificar la ubicación de un centro de datos de Azure. Para obtener una lista de centros de datos de Azure, ejecute este comando.

	Get-AzureLocation | sort Name | Select Name

Ahora, copie el siguiente bloque de comandos de PowerShell en un editor de texto. Rellene la cuenta de almacenamiento y la ubicación elegidas, y reemplace todo el contenido dentro de las comillas, incluidos los caracteres < and >.

	$stName = "<chosen storage account name>"
	$locName = "<chosen Azure location name>"
	$rgName = "TestRG"
	New-AzureRmResourceGroup -Name $rgName -Location $locName
	$storageAcc = New-AzureRmStorageAccount -ResourceGroupName $rgName -Name $stName -Type "Standard_GRS" -Location $locName
	$singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name singleSubnet -AddressPrefix 10.0.0.0/24
	$vnet = New-AzureRmVirtualNetwork -Name TestNet -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
	$pip = New-AzureRmPublicIpAddress -Name TestPIP -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic = New-AzureRmNetworkInterface -Name TestNIC -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
	$cred = Get-Credential -Message "Type the name and password of the local administrator account."
	$vm = New-AzureRmVMConfig -VMName WindowsVM -VMSize "Standard_A1"
	$vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName MyWindowsVM -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm = Set-AzureRmVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
	$osDiskUri = $storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/WindowsVMosDisk.vhd"
	$vm = Set-AzureRmVMOSDisk -VM $vm -Name "windowsvmosdisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRmVM -ResourceGroupName $rgName -Location $locName -VM $vm

Por último, copie el conjunto de comandos anterior en el Portapapeles y, a continuación, haga clic con el botón secundario en el símbolo del sistema del Azure PowerShell abierto. Esto emitirá el conjunto de comandos como una serie de comandos de PowerShell, le solicitará el nombre y la contraseña de la cuenta de administrador local y creará la máquina virtual de Azure.

Este es un ejemplo de lo que puede ver:

	PS C:\> $stName="contosost"
	PS C:\> $locName="West US"
	PS C:\> $rgName="TestRG"
	PS C:\> New-AzureRmResourceGroup -Name $rgName -Location $locName
	VERBOSE: 12:45:15 PM - Created resource group 'TestRG' in location 'westus'


	ResourceGroupName : TestRG
	Location          : westus
	ProvisioningState : Succeeded
	Tags              :
	Permissions       :
	                    Actions  NotActions
	                    =======  ==========
	                    *

	ResourceId        : /subscriptions/fd92919d-eeca-4f5b-840a-e45c6770d92e/resourceGroups/TestRG


	PS C:\> $storageAcc=New-AzureRmStorageAccount -ResourceGroupName $rgName -Name $stName -Type "Standard_GRS" -Location $locName
	PS C:\> $singleSubnet=New-AzureRmVirtualNetworkSubnetConfig -Name singleSubnet -AddressPrefix 10.0.0.0/24
	PS C:\> $vnet=New-AzureRmVirtualNetwork -Name TestNet3 -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
	PS C:\> $pip = New-AzureRmPublicIpAddress -Name TestNIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	PS C:\> $nic = New-AzureRmNetworkInterface -Name TestNIC -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
	PS C:\> $cred = Get-Credential -Message "Type the name and password of the local administrator account."
	PS C:\> $vm = New-AzureRmVMConfig -VMName WindowsVM -VMSize "Standard_A1"
	PS C:\> $vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName MyWindowsVM -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	PS C:\> $vm = Set-AzureRmVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	PS C:\> $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
	PS C:\> $osDiskUri = $storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/MyWindowsVMosDisk.vhd"
	PS C:\> $vm = Set-AzureRmVMOSDisk -VM $vm -Name "windowsvmosdisk" -VhdUri $osDiskUri -CreateOption fromImage
	PS C:\> New-AzureRmVM -ResourceGroupName $rgName -Location $locName -VM $vm


	EndTime             : 4/28/2015 1:00:05 PM -07:00
	Error               :
	Output              :
	StartTime           : 4/28/2015 12:52:52 PM -07:00
	Status              : Succeeded
	TrackingOperationId : 45035a90-ea12-4e1e-87e7-2a5e9ed12c93
	RequestId           : 98c7b4fb-b26e-4a58-b17a-b0983d896aae
	StatusCode          : OK

## Pasos siguientes

Aprenda a administrar la máquina virtual que acaba de crear. Para ello, consulte [Administración de máquinas virtuales con el Administrador de recursos de Azure y con PowerShell](virtual-machines-deploy-rmtemplates-powershell.md).

<!---HONumber=AcomDC_0114_2016-->