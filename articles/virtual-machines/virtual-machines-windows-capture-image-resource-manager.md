<properties
	pageTitle="Captura de una máquina virtual Windows en el Administrador de recursos | Microsoft Azure"
	description="Obtenga información sobre cómo capturar una imagen de una máquina virtual de Azure basada en Windows creada con el modelo de implementación del Administrador de recursos de Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/29/2016"
	ms.author="dkshir"/>


# Captura de una máquina virtual de Windows en el modelo de implementación del Administrador de recursos

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-machines-capture-image-windows-server.md).


En este artículo se muestra cómo utilizar Azure PowerShell para capturar una máquina virtual de Azure con Windows a fin de usarla para crear otras máquinas virtuales. Esta imagen incluye el disco del sistema operativo y cualquier otro disco de datos conectado a la máquina virtual. No incluye los recursos de redes virtuales que necesitará para crear una máquina virtual de Windows, por lo que necesitará configurarlos antes de crear otra máquina virtual que usa la imagen. Esta imagen también estará preparada para ser una [imagen generalizada de Windows](https://technet.microsoft.com/library/hh824938.aspx).


## Requisitos previos

En estos pasos se da por sentado que ya creó un máquina virtual de Azure con el modelo de implementación del Administrador de recursos, que configuró el sistema operativo, que adjuntó cualquier disco de datos y que realizó otras personalizaciones como la instalación de aplicaciones. Si no lo ha hecho aún, lea [Crear una máquina virtual de Windows con el Administrador de recursos y PowerShell](virtual-machines-create-windows-powershell-resource-manager.md). Puede crear fácilmente una máquina virtual de Windows con el [Portal de Azure](https://portal.azure.com). Lea [Creación de una máquina virtual de Windows en el Portal de Azure](virtual-machines-windows-tutorial.md).


## Preparación de la máquina virtual para la captura de imágenes

En esta sección se muestra cómo generalizar la máquina virtual de Windows. Entre otras cosas, se elimina toda la información personal de la cuenta. Normalmente, deseará hacer esto si quiere usar esta imagen de máquina virtual para implementar rápidamente máquinas virtuales similares.

1. Inicie sesión en una máquina virtual de Windows. En el [Portal de Azure](https://portal.azure.com), haga clic en **Examinar** > **Máquinas virtuales** > Su máquina virtual Windows > **Conectar**.

2. Abra una ventana de símbolo del sistema como administrador.

3. Cambie el directorio a `%windir%\system32\sysprep` y después ejecute sysprep.exe.

4. En el cuadro de diálogo **Herramienta de preparación del sistema**, haga lo siguiente:

	- En **Acción de limpieza del sistema**, seleccione **Iniciar la Configuración rápida (OOBE)** y asegúrese de que la opción **Generalizar** está marcada. Para obtener más información acerca del uso de Sysprep, consulte [Uso de Sysprep: Introducción](http://technet.microsoft.com/library/bb457073.aspx).

	- En **Opciones de apagado**, seleccione **Apagar**.

	- Haga clic en **Aceptar**.

	![Ejecute Sysprep](./media/virtual-machines-windows-capture-image-resource-manager/SysprepGeneral.png)

5.	Sysprep apaga la máquina virtual. Su estado cambia a **Detenido** en el Portal de Azure.


</br>
## Captura de la imagen de la máquina virtual

Puede capturar la máquina virtual Windows generalizada con la utilización de Azure PowerShell o de la nueva herramienta del Explorador del Administrador de recursos de Azure (ARM). En esta sección se muestran los pasos para ambos.

### Uso de PowerShell

En este artículo se supone que tiene instalada la versión Azure PowerShell 1.0.x. Se recomienda usar esta versión, ya que no se agregarán nuevas características del Administrador de recursos para las versiones anteriores de PowerShell. Lea [Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0/) para obtener más información sobre las diferencias de versión.

1. Abra Azure PowerShell 1.0.x e inicie sesión en la cuenta de Azure.

		Login-AzureRmAccount

	Este comando abrirá una ventana emergente para escribir sus credenciales de Azure.

2. Si el identificador de suscripción seleccionado de forma predeterminada es diferente del identificador con que desea trabajar, use cualquiera de las opciones siguientes para establecer la suscripción adecuada.

		Set-AzureRmContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"

	o

		Select-AzureRmSubscription -SubscriptionId "xxxx-xxxx-xxxx-xxxx"

	Puede encontrar las suscripciones de su cuenta de Azure mediante el comando `Get-AzureRmSubscription`.

3. Ahora debe desasignar los recursos utilizados por esta máquina virtual.

		Stop-AzureRmVM -ResourceGroupName YourResourceGroup -Name YourWindowsVM

	Verá que ha cambiado el estado de la máquina virtual en el Portal de Azure de **Detenido** a **Detenido (desasignado)**.

	>[AZURE.TIP] También puede averiguar el estado de la máquina virtual en PowerShell usando:</br> `$vm = Get-AzureRmVM -ResourceGroupName YourResourceGroup -Name YourWindowsVM -status`</br> `$vm.Statuses`</br> El campo **DisplayStatus** se corresponde con el **estado** que se muestra en el Portal de Azure.

4. A continuación, debe establecer el estado de la máquina virtual en _Generalizado_. Tenga en cuenta que debe hacer esto porque el paso de generalización anterior (`sysprep`) no lo hace de una manera que Azure pueda entender.

		Set-AzureRmVm -ResourceGroupName YourResourceGroup -Name YourWindowsVM -Generalized

	>[AZURE.NOTE] El estado generalizado establecido anteriormente no se mostrará en el portal. Sin embargo, puede comprobarlo con el comando Get-AzureRmVM como se ha indicado en la sugerencia anterior.

5. Capture la imagen de máquina virtual en un contenedor de almacenamiento de destino con este comando.

		Save-AzureRmVMImage -ResourceGroupName YourResourceGroup -VMName YourWindowsVM -DestinationContainerName YourImagesContainer -VHDNamePrefix YourTemplatePrefix -Path Yourlocalfilepath\Filename.json

	La variable `-Path` es opcional y puede utilizarse para guardar la plantilla JSON localmente. La variable `-DestinationContainerName` es el nombre del contenedor en que desea almacenar las imágenes. La dirección URL de la imagen almacenada será similar a `https://YourStorageAccountName.blob.core.windows.net/system/Microsoft.Compute/Images/YourImagesContainer/YourTemplatePrefix-osDisk.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`. Se creará en la misma cuenta de almacenamiento que la de la máquina virtual original.

	>[AZURE.NOTE] Para buscar la ubicación de la imagen, abra la plantilla de archivo JSON local. Vaya a la sección **recursos** > **storageProfile** > **osDisk** > **image** > **uri** para ver la ruta de acceso completa de la imagen. A partir de ahora, no hay ninguna manera fácil para comprobar estas imágenes en el portal, ya que el contenedor _sistema_ de la cuenta de almacenamiento está oculto. Por esta razón, aunque la variable `-Path` es opcional, definitivamente desea utilizarla para guardar la plantilla localmente y encontrar fácilmente la dirección URL de la imagen. Como alternativa, se puede averiguar mediante una herramienta denominada **Explorador de almacenamiento de Azure** que se explica en la sección siguiente.


### Uso del Explorador de recursos de Azure (vista previa)

El [Explorador de recursos de Azure (vista previa)](https://azure.microsoft.com/blog/azure-resource-explorer-a-new-tool-to-discover-the-azure-api/) es una herramienta nueva desarrollada para administrar recursos de Azure creados en el modelo de implementación del Administrador de recursos. Con esta herramienta, puede hacer lo siguiente de manera fácil:

- encontrar las API de administración de recursos de Azure,
- obtener documentación sobre API, y
- realizar llamadas API directamente en las suscripciones de Azure.

Para obtener más información sobre todo lo que puede hacer con esta poderosa herramienta, vea el vídeo en [Azure Resource Manager Explorer with David Ebbo](https://channel9.msdn.com/Shows/Azure-Friday/Azure-Resource-Manager-Explorer-with-David-Ebbo) (Explorador del Administrador de recursos de Azure con David Ebbo).

Puede utilizar el Explorador de recursos para capturar la máquina virtual, como una alternativa al método de PowerShell.

1. Abra el [sitio web del Explorador de recursos](https://resources.azure.com/) e inicie sesión en su cuenta de Azure.

2. En la parte superior derecha de la herramienta, seleccione **Lectura/escritura** para permitir operaciones _PUT_ y _POST_. Se establece en **Solo lectura** de forma predeterminada, lo que significa que de forma predeterminada solo se pueden realizar operaciones _GET_.

	![Lectura/escritura del Explorador de recursos](./media/virtual-machines-windows-capture-image-resource-manager/ArmExplorerReadWrite.png)

3. A continuación, busque la máquina virtual Windows. Puede escribir el nombre en el _cuadro de búsqueda_ en la parte superior de la herramienta, o podría navegar por el menú de la izquierda, siguiendo este itinerario **suscripciones** > su suscripción de Azure > **Grupos de recursos** > su grupo de recursos > **proveedores** > **Microsoft.Compute** > **Máquinas virtuales** > su máquina virtual Windows. Al hacer clic en la máquina virtual en el panel de navegación izquierdo, verá su plantilla en la parte derecha de la herramienta.

4. En la parte superior derecha de la página de la plantilla, debería ver las pestañas de las distintas operaciones disponibles para esta máquina virtual. Haga clic en la pestaña **Acciones (POST o DELETE)**.

	![Menú Acción del Explorador de recursos](./media/virtual-machines-windows-capture-image-resource-manager/ArmExplorerActionMenu.png)

5. Verá una lista de todas las acciones que puede realizar en la máquina virtual.

	![Acciones del Explorador de recursos](./media/virtual-machines-windows-capture-image-resource-manager/ArmExplorerActionItems.png)

6. Desasigne la máquina virtual; para ello, haga clic en el botón de acción **desasignar**. Cambiará el estado de la máquina virtual de **Detenido** a **Detenido (desasignado)**.

7. Marque la máquina virtual como generalizada; para ello, haga clic en el botón de acción de **generalizar**. Puede comprobar los cambios de estado haciendo clic en el menú **InstanceView** en el nombre de máquina virtual en el lado izquierdo y desplácese a la sección **estados** en el lado derecho.

8. En el botón de acción **capturar**, puede establecer los valores para capturar la imagen. Los valores rellenados podrían tener un aspecto similar al siguiente.

	![Captura del Explorador de recursos](./media/virtual-machines-windows-capture-image-resource-manager/ArmExplorerCaptureAction.png)

	Haga clic en el botón de acción **capturar** para capturar la imagen de la máquina virtual. Esto crea un nuevo VHD para la imagen como un archivo de plantilla JSON, que, a partir de ahora, no es accesible a través del Explorador de recursos o del [Portal de Azure](https://portal.azure.com).

9. Para tener acceso a la nueva imagen de VHD y a la plantilla, descargue e instale la herramienta de Azure para administrar recursos de almacenamiento, el [Explorador de almacenamiento de Azure](http://storageexplorer.com/). Instalará el Explorador de almacenamiento de Azure localmente en el equipo.

	- Abra el Explorador de almacenamiento e inicie sesión en la suscripción de Azure. Debe mostrar todas las cuentas de almacenamiento disponibles para su suscripción.

	- En el lado izquierdo, debe ver la cuenta de almacenamiento de la máquina virtual capturada en los pasos anteriores. Haga doble clic en el menú **sistema** situado debajo. Debería ver el contenido de la carpeta **sistema** en el lado derecho.

		![Sistema del Explorador de almacenamiento](./media/virtual-machines-windows-capture-image-resource-manager/StorageExplorer1.png)

	- Haga doble clic en **Microsoft.Compute** y luego en **Imágenes**, que mostrará todas las carpetas de la imagen. Haga doble clic en el nombre de la carpeta que especificó para la variable **destinationContainerName** mientras captura la imagen desde el Explorador de recursos. Mostrará el VHD así como el archivo de plantilla JSON.

	- Desde aquí, puede averiguar la dirección URL o descargar la plantilla o el VHD; para ello, debe hacer clic con el botón derecho en el elemento correspondiente.

		![Plantilla del Explorador de almacenamiento](./media/virtual-machines-windows-capture-image-resource-manager/StorageExplorer2.png)


## Implementación de una nueva máquina virtual desde la imagen capturada

Ahora puede usar la imagen capturada para crear una nueva máquina virtual Windows. Estos pasos explican cómo usar Azure PowerShell y la imagen de VM capturada en los pasos anteriores para crear la máquina virtual en una nueva red virtual.

>[AZURE.NOTE] La imagen de máquina virtual debe estar presente en la misma cuenta de almacenamiento que la máquina virtual real que va a crear.

### Crear recursos de red

Use el siguiente script de PowerShell de ejemplo para configurar una red virtual y la NIC para la nueva máquina virtual. Use los valores para las variables representadas por el signo **$** según resulte apropiado para su aplicación.

	$pip = New-AzureRmPublicIpAddress -Name $pipName -ResourceGroupName $rgName -Location $location -AllocationMethod Dynamic

	$subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name $subnet1Name -AddressPrefix $vnetSubnetAddressPrefix

	$vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location -AddressPrefix $vnetAddressPrefix -Subnet $subnetconfig

	$nic = New-AzureRmNetworkInterface -Name $nicname -ResourceGroupName $rgName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id

### Creación de una máquina virtual nueva

El siguiente script de PowerShell muestra cómo establecer las configuraciones de máquina virtual y usar la imagen capturada de la máquina virtual como origen para la nueva instalación. </br>

	#Enter a new username and password in the pop-up for the following
	$cred = Get-Credential

	#Get the storage account where the captured image is stored
	$storageAcc = Get-AzureRmStorageAccount -ResourceGroupName $rgName -AccountName $storageAccName

	#Set the VM name and size
	$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A2"

	#Set the Windows operating system configuration and add the NIC
	$vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate

	$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id

	#Create the OS disk URI
	$osDiskUri = '{0}vhds/{1}{2}.vhd' -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName

	#Configure the OS disk to be created from image (-CreateOption fromImage) and give the URL of the captured image VHD for the -SourceImageUri parameter.
	#We found this URL in the local JSON template in the previous sections.
	$vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri $urlOfCapturedImageVhd -Windows

	#Create the new VM
	New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm

Debería ver la máquina virtual recién creada en el [Portal de Azure](https://portal.azure.com), en **Examinar** > **Máquinas virtuales**, O con la utilización de los comandos de PowerShell siguientes:

	$vmList = Get-AzureRmVM -ResourceGroupName $rgName
	$vmList.Name


## Pasos siguientes

Para administrar la nueva máquina virtual con Azure PowerShell, lea [Administración de máquinas virtuales con el Administrador de recursos de Azure y con PowerShell](virtual-machines-deploy-rmtemplates-powershell.md).

<!---HONumber=AcomDC_0204_2016-->