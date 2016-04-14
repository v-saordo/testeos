<properties
	pageTitle="Creación de una máquina virtual de SQL Server en PowerShell | Microsoft Azure"
	description="Ofrece pasos y scripts de PowerShell para crear una máquina virtual de Azure con imágenes de la galería de máquinas virtuales de SQL Server."
	services="virtual-machines"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar"
	tags="azure-service-management" />
<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="01/22/2016"
	ms.author="jroth" />

# Crear una máquina virtual de SQL Server en Azure (PowerShell)

> [AZURE.SELECTOR]
- [Classic portal](virtual-machines-provision-sql-server.md)
- [PowerShell](virtual-machines-sql-server-create-vm-with-powershell.md)
- [Azure Resource Manager portal](virtual-machines-sql-server-provision-resource-manager.md)


## Información general

En este artículo se ofrecen los pasos para crear una máquina virtual de SQL Server en Azure mediante los cmdlets de PowerShell.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo del Administrador de recursos.


## Instalación y configuración se PowerShell

1. Si no tiene una cuenta de Azure, visite [Evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).

2. [Instale los cmdlets más recientes de Azure PowerShell](../powershell-install-configure.md/#how-to-install-azure-powershell).

3. [Conecte PowerShell a su suscripción de Azure](../powershell-install-configure.md/#how-to-connect-to-your-subscription).

## Determinar su región de Azure de destino

La máquina virtual de SQL Server se hospedará en un servicio en la nube que reside en una región de Azure específica. Los siguientes pasos le ayudan a determinar la región, la cuenta de almacenamiento y servicio en la nube que se usará para el resto del tutorial.

1. Determine el centro de datos que quiere usar para hospedar su máquina virtual de SQL Server. Los siguientes comandos de PowerShell mostrará las regiones disponibles en detalle con una lista de resumen al final.

		Get-AzureLocation
		(Get-AzureLocation).Name

2.  Cuando haya identificado la ubicación que prefiera, establezca una variable denominada **$dcLocation** en dicha región.

		$dcLocation = "<region name>"

## Establecimiento de la cuenta de suscripción y almacenamiento

1. Determinar la suscripción de Azure que usará para la nueva máquina virtual.

		(Get-AzureSubscription).SubscriptionName

1. Asignar su suscripción de Azure de destino a la variable **$subscr**. Establézcala como su suscripción de Azure actual.

		$subscr="<subscription name>"
		Select-AzureSubscription -SubscriptionName $subscr –Current

1. Luego busque las cuentas de almacenamiento existentes. El siguiente script muestra todas las cuentas de almacenamiento que existen en la región que elija:

		(Get-AzureStorageAccount | where { $_.GeoPrimaryLocation -eq $dcLocation }).StorageAccountName

	>[AZURE.NOTE] Si necesita una nueva cuenta de almacenamiento, cree primero un nombre de cuenta de almacenamiento en minúsculas con el comando New-AzureStorageAccount, tal y como se muestra en el siguiente ejemplo: **New-AzureStorageAccount -StorageAccountName "<storage account name>" -Location $dcLocation**

1. Asigne el nombre de la cuenta de almacenamiento de destino en **$staccount**. A continuación, use **Set-AzureSubscription** para establecer la suscripción y la cuenta de almacenamiento actual.

		$staccount="<storage account name>"
		Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

## Seleccionar una imagen de máquina virtual de SQL Server

1. Averigüe la lista de imágenes de máquinas virtuales de SQL Server disponibles de la galería. Todas estas imágenes tendrán una propiedad **ImageFamily** que empieza por "SQL". La consulta siguiente muestra la familia de imágenes que tiene a su disposición que tiene SQL Server preinstalado.

		Get-AzureVMImage | where { $_.ImageFamily -like "SQL*" } | select ImageFamily -Unique | Sort-Object -Property ImageFamily

1. Cuando encuentre la familia de imágenes de la máquina virtual, podría haber varias imágenes publicadas en esta familia. Use el siguiente script para buscar el nombre de la imagen máquina virtual publicada más reciente para su familia de imágenes seleccionada (como **SQL Server 2014 SP1 Enterprise en Windows Server 2012 R2**):

		$family="<ImageFamily value>"
		$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

		echo "Selected SQL Server image name:"
		echo "   $image"

## Creación de la máquina virtual

Por último, cree la máquina virtual con la PowerShell:

1. Cree un servicio en la nube para hospedar la nueva máquina virtual. Tenga en cuenta que también es posible utilizar un servicio en la nube existente en su lugar. Cree una nueva variable **$svcname** con el nombre corto del servicio en la nube.

		$svcname = "<cloud service name>"
		New-AzureService -ServiceName $svcname -Label $svcname -Location $dcLocation

2. Especifique el nombre de la máquina virtual y un tamaño. Para obtener información sobre los tamaños de máquina virtual, consulte [Tamaños de máquina virtual para Azure](virtual-machines-size-specs.md).

		$vmname="<machine name>"
		$vmsize="<Specify a valid machine size>" # see the link to virtual machine sizes
		$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

3. Especifique la cuenta de administrador local y la contraseña.

		$cred=Get-Credential -Message "Type the name and password of the local administrator account."
		$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

4. Ejecute el script siguiente para crear la máquina virtual.

		New-AzureVM –ServiceName $svcname -VMs $vm1

>[AZURE.NOTE] Para obtener una explicación adicional y opciones de configuración, vea la sección **Generar su conjunto de comandos** en [Usar Azure PowerShell para crear y preconfigurar máquinas virtuales basadas en Windows](virtual-machines-ps-create-preconfigure-windows-vms.md).

## Script de PowerShell de ejemplo

El siguiente script ofrece un ejemplo de un script completo que crea una máquina virtual de **SQL Server 2014 SP1 Enterprise en Windows Server 2012 R2**. Si usa este script, debe personalizar las variables iniciales basadas en los pasos anteriores de este tema.

	# Customize these variables based on your settings and requirements:
	$dcLocation = "East US"
	$subscr="mysubscription"
	$staccount="mystorageaccount"
	$family="SQL Server 2014 SP1 Enterprise on Windows Server 2012 R2"
	$svcname = "mycloudservice"
	$vmname="myvirtualmachine"
	$vmsize="A5"

	# Set the current subscription and storage account
	# Comment out the New-AzureStorageAccount line if the account already exists
	Select-AzureSubscription -SubscriptionName $subscr –Current
	New-AzureStorageAccount -StorageAccountName $staccount -Location $dcLocation
	Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

	# Select the most recent VM image in this image family:
	$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

	# Create the new cloud service; comment out this line if cloud service exists already:
	New-AzureService -ServiceName $svcname -Label $svcname -Location $dcLocation

	# Create the VM config:
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

	# Set administrator credentials:
	$cred=Get-Credential -Message "Type the name and password of the local administrator account."
	$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

	# Create the SQL Server VM:
	New-AzureVM –ServiceName $svcname -VMs $vm1


## Conexión con el Escritorio remoto

1. Cree los archivos .RDP en la carpeta de documentos del usuario actual para iniciar estas máquinas virtuales para completar la instalación:

		$documentspath = [environment]::getfolderpath("mydocuments")
		Get-AzureRemoteDesktopFile -ServiceName $svcname -Name $vmname -LocalPath "$documentspath\vm1.rdp"

1. En el directorio de documentos, inicie el archivo RDP. Póngase en contacto con el nombre de usuario de administrador y la contraseña que se ofreció anteriormente (por ejemplo, si su nombre de usuario era VMAdmin, especifique "\\VMAdmin" como el usuario y ofrezca la contraseña).

		.\vm1.rdp

## Completar la configuración de la máquina de SQL Server para acceso remoto

Después de iniciar sesión en el equipo con el escritorio remoto, configure SQL Server según las instrucciones de los [Pasos para configurar la conectividad de SQL Server en una máquina virtual de Azure](virtual-machines-sql-server-connectivity.md#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).

## Pasos siguientes

Puede encontrar instrucciones adicionales para el aprovisionamiento de máquinas virtuales con PowerShell en la [documentación de máquinas virtuales](virtual-machines-ps-create-preconfigure-windows-vms.md). Para scripts adicionales relacionados con SQL Server y el Almacenamiento Premium, consulte [Uso del almacenamiento de Azure Premium con SQL Server en máquinas virtuales](virtual-machines-sql-server-use-premium-storage.md).

En muchos casos, el siguiente paso es migrar las bases de datos a esta nueva VM de SQL Server. Para obtener instrucciones sobre la migración de bases de datos, consulte [Migración de una base de datos a SQL Server en una máquina virtual de Azure](virtual-machines-migrate-onpremises-database.md).

Si también está interesado en saber cómo llevar a cabo estos pasos desde el Portal de Azure clásico, consulte [Aprovisionamiento de una máquina virtual de SQL Server en Azure](virtual-machines-provision-sql-server.md).

Además de estos recursos, le recomendamos que revise [otros temas relacionados con la ejecución de SQL Server en máquinas virtuales de Azure](virtual-machines-sql-server-infrastructure-services.md).

<!---HONumber=AcomDC_0128_2016-->