<properties
	pageTitle="Administrar VM del Administrador de recursos de Azure | Microsoft Azure"
	description="Administración de máquinas virtuales con el Administrador de recursos de Azure, con plantillas y con PowerShell."
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
	ms.date="01/07/2016"
	ms.author="davidmu"/>

# Administración de máquinas virtuales con el Administrador de recursos de Azure y con PowerShell

> [AZURE.SELECTOR]
- [PowerShell](virtual-machines-deploy-rmtemplates-powershell.md)
- [CLI](virtual-machines-deploy-rmtemplates-azure-cli.md)

<br>


El uso de plantillas de Azure PowerShell y del Administrador de recursos te proporciona mucha eficacia y flexibilidad al administrar los recursos de Microsoft Azure. Puedes usar las tareas de este artículo para administrar los recursos de máquinas virtuales.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementación clásica.

Estas tareas solo usan PowerShell:

- [Visualización de información acerca de una máquina virtual](#displayvm)
- [Inicio de una máquina virtual](#start)
- [Detención de una máquina virtual](#stop)
- [Reinicio de una máquina virtual](#restart)
- [Eliminación de una máquina virtual](#delete)

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

## <a id="displayvm"></a>Visualización de información acerca de una máquina virtual

En el comando siguiente, reemplace el *nombre del grupo de recursos* por el nombre del grupo de recursos que contiene la máquina virtual, y el *nombre de la máquina virtual* por el nombre de la máquina, y ejecútelo:

	Get-AzureRmVM -ResourceGroupName "resource group name" -Name "VM name"

Entonces, se devuelve algo parecido a lo siguiente:


	ResourceGroupName        : rg1
	Id                       : /subscriptions/{subscription-id}/resourceGroups/
															rg1/providers/Microsoft.Compute/virtualMachines/vm1
	Name                     : vm1
	Type                     : Microsoft.Azure.Management.Compute.Models.VirtualMachineGetResponse
	Location                 : westus
	Tags                     : {}
	AvailabilitySetReference : null
	Extensions               : []
	HardwareProfile          :  {
																"VirtualMachineSize": "Standard_D1"
															}
	InstanceView             : null
	Location                 : westus
	Name                     : vm1
	NetworkProfile           :  {
																"NetworkInterfaces": [
																	{
																		"Primary": null,
																		"ReferenceUri": "/subscriptions/{subscription-id}/resourceGroups/
																		rg1/providers/Microsoft.Network/networkInterfaces/nc1"
																	}
																]
															}
	OSProfile                :  {
																"AdminPassword": null,
																"AdminUsername": "WinAdmin1",
																"ComputerName": "vm1",
																"CustomData": null,
																"LinuxConfiguration": null,
																"Secrets": [],
																"WindowsConfiguration": {
																	"AdditionalUnattendContents": [],
																	"EnableAutomaticUpdates": true,
																	"ProvisionVMAgent": true,
																	"TimeZone": null,
																	"WinRMConfiguration": null
																}
															}
	Plan                     : null
	ProvisioningState        : Succeeded
	StorageProfile           : 	{
																"DataDisks": [],
																"ImageReference": {
																	"Offer": "WindowsServer",
																	"Publisher": "MicrosoftWindowsServer",
																	"Sku": "2012-R2-Datacenter",
																	"Version": "latest"
																},
																"OSDisk": {
																	"OperatingSystemType": "Windows",
																	"Caching": "ReadWrite",
																	"CreateOption": "FromImage",
																	"Name": "osdisk",
																	"SourceImage": null,
																	"VirtualHardDisk": {
																		"Uri": "http://sa1.blob.core.windows.net/vhds/osdisk1.vhd"
																	}
																}
															}
	DataDiskNames            :  {}
	NetworkInterfaceIDs      : { /subscriptions/{subscription-id}/resourceGroups/
																rg1/providers/Microsoft.Network/networkInterfaces/nc1}

Si quieres ver un vídeo en el que se realiza esta tarea, mira aquí:

[AZURE.VIDEO displaying-information-about-a-virtual-machine-in-microsoft-azure-with-powershell]

## <a id="start"></a>Inicio de una máquina virtual

En el comando siguiente, reemplace el *nombre del grupo de recursos* por el nombre del grupo de recursos que contiene la máquina virtual, y el *nombre de la máquina virtual* por el nombre de la máquina, y ejecútelo:

	Start-AzureRmVM -ResourceGroupName "resource group name" -Name "VM name"

Entonces, se devuelve algo parecido a lo siguiente:

	Status              : Succeeded
	StatusCode          : OK
	RequestId           : 06935ddf-6e89-48d2-b46a-229493e3e9d1
	Output              :
	Error               :
	StartTime           : 4/28/2015 11:10:35 AM -07:00
	EndTime             : 4/28/2015 11:11:41 AM -07:00
	TrackingOperationId : c1aa0a70-4f4f-4d6c-a8ac-7ea35c004ce0

Si quieres ver un vídeo en el que se realiza esta tarea, mira aquí:

[AZURE.VIDEO start-stop-restart-and-delete-vms-in-microsoft-azure-with-powershell]

## <a id="stop"></a>Detención de una máquina virtual

En el comando siguiente, reemplace el *nombre del grupo de recursos* por el nombre del grupo de recursos que contiene la máquina virtual, y el *nombre de la máquina virtual* por el nombre de la máquina, y ejecútelo:

	Stop-AzureRmVM -ResourceGroupName "resource group name" -Name "VM name"

Se te pedirá una confirmación:

	Virtual machine stopping operation
	This cmdlet will stop the specified virtual machine. Do you want to continue?
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

Entonces, se devuelve algo parecido a lo siguiente:

	Status              : Succeeded
	StatusCode          : OK
	RequestId           : aac41de1-b85d-4429-9a3d-040b922d2e6d
	Output              :
	Error               :
	StartTime           : 4/28/2015 11:10:35 AM -07:00
	EndTime             : 4/28/2015 11:11:41 AM -07:00
	TrackingOperationId : e1705973-d266-467e-8655-920016145347

Si quieres ver un vídeo en el que se realiza esta tarea, mira aquí:

[AZURE.VIDEO start-stop-restart-and-delete-vms-in-microsoft-azure-with-powershell]

## <a id="restart"></a>Reinicio de una máquina virtual

En el comando siguiente, reemplace el *nombre del grupo de recursos* por el nombre del grupo de recursos que contiene la máquina virtual, y el *nombre de la máquina virtual* por el nombre de la máquina, y ejecútelo:

	Restart-AzureRmVM -ResourceGroupName "resource group name" -Name "VM name"

Entonces, se devuelve algo parecido a lo siguiente:

	Status              : Succeeded
	StatusCode          : OK
	RequestId           : 4b05891c-fdff-4c9a-89ca-e4f1d7691aed
	Output              :
	Error               :
	StartTime           : 1/5/2016 12:06:53 PM -08:00
	EndTime             : 1/5/2016 12:06:54 PM -08:00
	TrackingOperationId : 5aeeab89-45ab-41b9-84ef-9e9a7e732207


Si quieres ver un vídeo en el que se realiza esta tarea, mira aquí:

[AZURE.VIDEO start-stop-restart-and-delete-vms-in-microsoft-azure-with-powershell]

## <a id="delete"></a>Eliminación de una máquina virtual

En el comando siguiente, reemplace el *nombre del grupo de recursos* por el nombre del grupo de recursos que contiene la máquina virtual, y el *nombre de la máquina virtual* por el nombre de la máquina, y ejecútelo:

	Remove-AzureRmVM -ResourceGroupName "resource group name" –Name "VM name"

> [AZURE.NOTE] Puede usar el parámetro **–Force** para omitir la solicitud de confirmación.

Se te pedirá una confirmación si no usaste el parámetro -Force:

	Virtual machine removal operation
	This cmdlet will remove the specified virtual machine. Do you want to continue?
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

Entonces, se devuelve algo parecido a lo siguiente:

	Status              : Succeeded
	StatusCode          : OK
	RequestId           : 2d723b40-ce1f-4b11-a603-dc659a13b6f0
	Output              :
	Error               :
	StartTime           : 1/5/2016 12:10:28 PM -08:00
	EndTime             : 1/5/2016 12:12:12 PM -08:00
	TrackingOperationId : d138ab29-83bf-4948-9d13-dab87db1a639

Si quieres ver un vídeo en el que se realiza esta tarea, mira aquí:

[AZURE.VIDEO start-stop-restart-and-delete-vms-in-microsoft-azure-with-powershell]

<!---HONumber=AcomDC_0128_2016-->