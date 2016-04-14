<properties
	pageTitle="Carga de un VHD de Windows para el Administrador de recursos | Microsoft Azure"
	description="Aprenda a cargar una imagen de máquina virtual basada en Windows para usar con el modelo de implementación del Administrador de recursos."
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/05/2016"
	ms.author="dkshir"/>

# Carga de una imagen de VM de Windows en Microsoft Azure para implementaciones del Administrador de recursos

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-machines-create-upload-vhd-windows-server.md).


En este artículo se muestra cómo puede cargar un disco duro virtual (VHD) con un sistema operativo basado en Windows, que podrá utilizar para crear nuevas máquinas virtuales de Windows utilizando el modelo de implementación del Administrador de recursos. Para obtener más detalles sobre discos y VHD en Microsoft Azure, consulte [Acerca de los discos y los discos duros virtuales para máquinas virtuales](virtual-machines-disks-vhds.md).



## Requisitos previos

En este artículo se supone que ya dispones de:

1. **Una suscripción a Azure**: si no tiene una, [abra una cuenta de Azure gratis](/pricing/free-trial/?WT.mc_id=A261C142F). Recibirá créditos para probar servicios de Azure de pago y, una vez que se agoten, podrá mantener la cuenta y usar servicios gratis de Azure, como sitios web. Nunca se va a hacer ningún cargo a tu tarjeta de crédito, a menos que cambies explícitamente la configuración. También puede [activar los beneficios de suscriptores de MSDN](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). La suscripción a MSDN le proporciona créditos todos los meses que puede usar para servicios de Azure de pago.

2. **Microsoft Azure PowerShell 1.0.x**: asegúrese de que ha instalado la versión 1.0.x de Microsoft Azure PowerShell. Si todavía no la ha instalado, lea [Cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md). Se recomienda usar la versión 1.0 y versiones superiores, ya que no se agregarán nuevas características del Administrador de recursos para las versiones anteriores de PowerShell. Lea [Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0/) para obtener más información sobre las diferentes versiones.

3. **Una máquina virtual con el sistema operativo Windows**: hay muchas herramientas para crear máquinas virtuales locales. Por ejemplo, puede usar el Administrador de Hyper-V para crear una máquina virtual e instalar el sistema operativo. Para obtener instrucciones, consulte [Instalación del rol de Hyper-V y configuración de una máquina Virtual](http://technet.microsoft.com/library/hh846766.aspx). Para obtener más información sobre qué sistemas operativos Windows se admiten, consulte [Soporte de software de servidor de Microsoft para las máquinas virtuales de Microsoft Azure](https://support.microsoft.com/kb/2721672).


## Comprobación de que la VM tiene el formato de archivo correcto

Microsoft Azure puede aceptar solo imágenes para [máquinas virtuales de generación 1](http://blogs.technet.com/b/ausoemteam/archive/2015/04/21/deciding-when-to-use-generation-1-or-generation-2-virtual-machines-with-hyper-v.aspx) guardadas en el formato de archivo VHD. El tamaño del VHD debe ser fijo y un número completo de MB. El tamaño máximo permitido para el VHD es de 1023 GB.

- El Administrador de Hyper-V normalmente guardará la imagen de VM en formato VHDX, que no se admite en Microsoft Azure. Puede convertirlo al formato VHD usando Hyper-V o el [cmdlet Convert-VHD de PowerShell](http://technet.microsoft.com/library/hh848454.aspx). Para conocer los pasos usando PowerShell, lea [Converting Hyper-V .vhdx to .vhd file formats (Conversión de los formatos de archivos .vhdx a .vhs de Hyper-V)](https://blogs.technet.microsoft.com/cbernier/2013/08/29/converting-hyper-v-vhdx-to-vhd-file-formats-for-use-in-windows-azure/). O en Hyper-V, seleccione el equipo local a la izquierda y luego, en el menú que aparece sobre él, haga clic en **Acciones** > **Editar disco...**. Desplácese por las pantallas haciendo clic en **Siguiente** y especificando estas opciones: Ruta de acceso para el archivo VHDX > **Convertir** > **VHD** > **Tamaño fijo** > Ruta de acceso para el nuevo archivo de VHD. Haga clic en **Finalizar** para cerrar.

- Si tiene una imagen de VM de Windows en [el formato de archivo VMDK](https://en.wikipedia.org/wiki/VMDK), puede convertirla a un formato VHD mediante [Microsoft Virtual Machine Converter](https://www.microsoft.com/download/details.aspx?id=42497). Lea el blog de [How to Convert a VMWare VMDK to Hyper-V VHD (Cómo convertir un VMDK de VMWare a VHD de Hyper-V)](http://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx) para obtener más información.


## Preparación del VHD para carga

En esta sección se muestra cómo generalizar la máquina virtual de Windows. Entre otras cosas, se elimina toda la información personal de la cuenta. Normalmente, deseará hacer esto si quiere usar esta imagen de máquina virtual para implementar rápidamente máquinas virtuales similares. Para obtener más información sobre Sysprep, vea [Uso de Sysprep: Introducción](http://technet.microsoft.com/library/bb457073.aspx).

1. Inicie de sesión en la máquina virtual basada en Windows

2. Abra una ventana del símbolo del sistema como administrador. Cambie el directorio a **%windir%\\system32\\sysprep** y, a continuación, ejecute `sysprep.exe`.

3. En el cuadro de diálogo **Herramienta de preparación del sistema**, haga lo siguiente:

	1. En **Acción de limpieza del sistema**, seleccione **Iniciar la Configuración rápida (OOBE)** y asegúrese de que la opción **Generalizar** está marcada.

	2. En **Shutdown Options**, seleccione **Shutdown**.

	3. Haga clic en **Aceptar**.

	![Iniciar Sysprep](./media/virtual-machines-upload-image-windows-resource-manager/sysprepgeneral.png)

</br>
## Creación o búsqueda de una cuenta de almacenamiento de Azure

Necesitará una cuenta de almacenamiento de Azure para cargar la imagen de VM. Puede usar una cuenta de almacenamiento existente o crear una nueva. Puede usar el portal o PowerShell para hacer esto.

### Uso del portal

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).

2. Haga clic en **Examinar** > **Cuentas de almacenamiento**.

3. Compruebe si existe una cuenta de almacenamiento que desee usar para cargar esta imagen. Anote el nombre de esta cuenta de almacenamiento. Puede continuar con la sección [Carga de la imagen de VM](#uploadvm) si utiliza una cuenta de almacenamiento existente.

4. Si desea crear una nueva cuenta de almacenamiento, haga clic en **Agregar** y escriba la información siguiente:

	1. En **Nombre**, escriba un nombre para la cuenta de almacenamiento. Debe contener entre 3 y 24 letras minúsculas y números únicamente. Este nombre se convierte en parte de la dirección URL que se va a utilizar para acceder a los blobs, archivos y otros recursos de la cuenta de almacenamiento.

	2. En **Tipo**, seleccione el tipo de la cuenta de almacenamiento que quiera crear. Para obtener más información, consulte [Acerca de las cuentas de almacenamiento de Azure](../storage/storage-create-storage-account.md).

	3. En **Grupo de recursos**, escriba el nombre del grupo de recursos. El portal creará un nuevo grupo de recursos si no puede encontrar uno existente con ese nombre.

	4. En **Ubicación**, elija la ubicación para la cuenta de almacenamiento.

	5. Haga clic en **Crear**. La cuenta aparece ahora en el panel **Cuentas de almacenamiento**.

		![Escribir los detalles de la cuenta de almacenamiento](./media/virtual-machines-upload-image-windows-resource-manager/portal_create_storage_account.png)

	6. Esto y los pasos siguientes muestran cómo crear un contenedor de blobs en esta cuenta de almacenamiento. Esto es opcional, ya que el comando de PowerShell para cargar la imagen también puede crear un nuevo contenedor de blobs para la imagen. Si no desea crearlo usted mismo, continúe con la sección [Carga de la imagen de VM](#uploadvm). En caso contrario, haga clic en **Blobs** en el icono **Servicios**.

		![Servicio BLOB](./media/virtual-machines-upload-image-windows-resource-manager/portal_create_blob.png)

	7. Cuando aparezca el panel de blobs, haga clic en **+ Contenedor** para crear un nuevo contenedor de almacenamiento de blobs. Escriba el nombre del contenedor y el tipo de acceso.

		![Crear un blob nuevo](./media/virtual-machines-upload-image-windows-resource-manager/portal_create_container.png)

  		> [AZURE.NOTE] De manera predeterminada, el contenedor es privado y solo puede acceder a él el propietario de la cuenta. Para permitir el acceso de lectura público a los blobs del contenedor, pero no a las propiedades y metadatos de dicho contenedor, use la opción **Blob**. Para permitir el acceso de lectura público completo para el contenedor y los blobs, use la opción **Contenedor**.

	8. El panel **Servicio BLOB** mostrará el nuevo contenedor de blobs. Anote la dirección URL de este contenedor, ya que la necesitará para el comando de PowerShell para cargar la imagen. Dependiendo de la longitud de la dirección URL y la resolución de la pantalla, la dirección URL puede aparecer parcialmente oculta; si esto sucede, maximice el panel, haciendo clic en el icono *Maximizar* situado en la esquina superior derecha.


### Uso de PowerShell

1. Abra Azure PowerShell 1.0.x e inicie sesión en la cuenta de Azure.

		Login-AzureRmAccount

	Este comando abrirá una ventana emergente para escribir sus credenciales de Azure.

2. Si el identificador de suscripción seleccionado de forma predeterminada es diferente del identificador con que desea trabajar, use cualquiera de las opciones siguientes para establecer la suscripción adecuada.

		Set-AzureRmContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"

	o

		Select-AzureRmSubscription -SubscriptionId "xxxx-xxxx-xxxx-xxxx"

	Puede encontrar las suscripciones que tiene la cuenta de Azure mediante el comando `Get-AzureRmSubscription`.

3. Busque las cuentas de almacenamiento disponibles bajo esta suscripción.

		Get-AzureRmStorageAccount

	Si desea utilizar una cuenta de almacenamiento existente, puede continuar con la sección [Carga de la imagen de VM](#uploadvm).

4. Si desea crear una nueva cuenta de almacenamiento para hospedar esta imagen, siga estos pasos:

	1. Asegúrese de que tiene un grupo de recursos para esta cuenta de almacenamiento. Averigüe todos los grupos de recursos de la suscripción mediante:

			Get-AzureRmResourceGroup

	2. Si desea crear un nuevo grupo de recursos, use este comando:

			New-AzureRmResourceGroup -Name YourResourceGroup -Location "West US"

	3. Cree una nueva cuenta de almacenamiento en este grupo de recursos mediante:

			New-AzureRmStorageAccount -ResourceGroupName YourResourceGroup -Name YourStorageAccountName -Location "West US" -Type "Standard_GRS"


</br> <a id="uploadvm"></a>
## Carga de la imagen de VM en la cuenta de almacenamiento

Use estos pasos en Azure PowerShell, para cargar la imagen de VM en la cuenta de almacenamiento. La imagen se cargará en un contenedor de almacenamiento de blobs en esta cuenta. Puede usar un contenedor existente o crear uno nuevo.

1. Inicie sesión en Azure PowerShell 1.0. x mediante `Login-AzureRmAccount` y asegúrese de que usa la suscripción correcta con `Set-AzureRmContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"`, como se mencionó en la sección anterior.

2. Agregue el VHD generalizado de Azure a la cuenta de almacenamiento mediante [AzureRmVhd agregar](https://msdn.microsoft.com/library/mt603554.aspx):

		Add-AzureRmVhd -ResourceGroupName YourResourceGroup -Destination "<StorageAccountURL>/<BlobContainer>/<TargetVHDName>.vhd" -LocalFilePath <LocalPathOfVHDFile>

	Donde: - **StorageAccountURL** es la dirección URL para la cuenta de almacenamiento. Normalmente tendrá este formato: `https://YourStorageAccountName.blob.core.windows.net`. Tenga en cuenta que debe utilizar el nombre de la cuenta de almacenamiento nueva o existente en lugar de **YourStorageAccountName**. - **BlobContainer** es el contenedor de blobs donde desea almacenar las imágenes. Si el cmdlet no encuentra un contenedor de blobs existente con este nombre, creará uno nuevo para usted. - **TargetVHDName** es el nombre con el que desea guardar la imagen. - **LocalPathOfVHDFile** es la ruta de acceso completa y el nombre del archivo .vhd en la máquina local.

	Una correcta ejecución de `Add-AzureRmVhd` tendrá este aspecto:

		C:\> Add-AzureRmVhd -ResourceGroupName testUpldRG -Destination https://testupldstore2.blob.core.windows.net/testblobs/WinServer12.vhd -LocalFilePath "C:\temp\WinServer12.vhd"
		MD5 hash is being calculated for the file  C:\temp\WinServer12.vhd.
		MD5 hash calculation is completed.
		Elapsed time for the operation: 00:03:35
		Creating new page blob of size 53687091712...
		Elapsed time for upload: 01:12:49

		LocalFilePath           DestinationUri
		-------------           --------------
		C:\temp\WinServer12.vhd https://testupldstore2.blob.core.windows.net/testblobs/WinServer12.vhd

	Este comando tardará algún tiempo en completarse, dependiendo de la conexión de red y el tamaño del archivo VHD.

</br>
## Implementación de una nueva VM a partir de la imagen capturada

Ahora puede usar la imagen cargada para crear una nueva VM de Windows. Estos pasos explican cómo usar Azure PowerShell y la imagen de VM cargada en los pasos anteriores para crear la VM en una nueva red virtual.

>[AZURE.NOTE] La imagen de máquina virtual debe estar presente en la misma cuenta de almacenamiento que la máquina virtual real que va a crear.

### Crear recursos de red

Use el siguiente script de PowerShell de ejemplo para configurar una red virtual y la NIC para la nueva máquina virtual. Use los valores para las variables representadas por el signo **$** según resulte apropiado para su aplicación.

	$pip = New-AzureRmPublicIpAddress -Name $pipName -ResourceGroupName $rgName -Location $location -AllocationMethod Dynamic

	$subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name $subnet1Name -AddressPrefix $vnetSubnetAddressPrefix

	$vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location -AddressPrefix $vnetAddressPrefix -Subnet $subnetconfig

	$nic = New-AzureRmNetworkInterface -Name $nicname -ResourceGroupName $rgName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id

### Creación de una máquina virtual nueva

El siguiente script de PowerShell muestra cómo establecer las configuraciones de máquina virtual y usar la imagen de VM cargada como origen para la nueva instalación. </br>

	#Enter a new username and password in the pop-up for the following
	$cred = Get-Credential

	#Get the storage account where the uploaded image is stored
	$storageAcc = Get-AzureRmStorageAccount -ResourceGroupName $rgName -AccountName $storageAccName

	#Set the VM name and size
	#Use "Get-Help New-AzureRmVMConfig" to know the available options for -VMsize
	$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A4"

	#Set the Windows operating system configuration and add the NIC
	$vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate

	$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id

	#Create the OS disk URI
	$osDiskUri = '{0}vhds/{1}{2}.vhd' -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName

	#Configure the OS disk to be created from image (-CreateOption fromImage) and give the URL of the uploaded image VHD for the -SourceImageUri parameter
	#You can find this URL in the result of the Add-AzureRmVhd cmdlet above
	$vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri $urlOfUploadedImageVhd -Windows

	#Create the new VM
	New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm

Por ejemplo, el flujo de trabajo podría ser algo parecido a esto:

		C:\> $pipName = "testpip6"
		C:\> $pip = New-AzureRmPublicIpAddress -Name $pipName -ResourceGroupName $rgName -Location $location -AllocationMethod Dynamic
		C:\> $subnet1Name = "testsub6"
		C:\> $nicname = "testnic6"
		C:\> $vnetName = "testvnet6"
		C:\> $subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name $subnet1Name -AddressPrefix $vnetSubnetAddressPrefix
		C:\> $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location -AddressPrefix $vnetAddressPrefix -Subnet $subnetconfig
		C:\> $nic = New-AzureRmNetworkInterface -Name $nicname -ResourceGroupName $rgName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
		C:\> $vmName = "testupldvm6"
		C:\> $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A4"
		C:\> $computerName = "testupldcomp6"
		C:\> $vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
		C:\> $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
		C:\> $osDiskName = "testupos6"
		C:\> $osDiskUri = '{0}vhds/{1}{2}.vhd' -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName
		C:\> $urlOfUploadedImageVhd = "https://testupldstore2.blob.core.windows.net/testblobs/WinServer12.vhd"
		C:\> $vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri $urlOfUploadedImageVhd -Windows
		C:\> $result = New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm
		C:\> $result
		RequestId IsSuccessStatusCode StatusCode ReasonPhrase
		--------- ------------------- ---------- ------------
		                         True         OK OK

Debe ver la VM recién creada en el [Portal de Azure](https://portal.azure.com) bajo **Examinar** > **Máquinas virtuales**, O usando los comandos de PowerShell siguientes:

	$vmList = Get-AzureRmVM -ResourceGroupName $rgName
	$vmList.Name


## Pasos siguientes

Para administrar la nueva máquina virtual con Azure PowerShell, lea [Administración de máquinas virtuales con el Administrador de recursos de Azure y con PowerShell](virtual-machines-deploy-rmtemplates-powershell.md).

<!---HONumber=AcomDC_0211_2016-->