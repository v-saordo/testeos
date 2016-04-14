<properties
   pageTitle="Administración de las máquinas virtuales con Azure PowerShell | Microsoft Azure"
   description="Obtenga información acerca de los comandos que puede usar para automatizar las tareas de administración de sus máquinas virtuales."
   services="virtual-machines"
   documentationCenter="windows"
   authors="singhkay"
   manager="timlt"
   editor=""
   tags="azure-service-management"/>

   <tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="12/07/2015"
   ms.author="kasing"/>

# Administración de las máquinas virtuales con Azure PowerShell

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo del Administrador de recursos.


Muchas tareas que se realizan a diario para administrar las máquinas virtuales pueden automatizarse mediante cmdlets de Azure PowerShell. En este artículo se proporcionan comandos de ejemplo para las tareas más sencillas, así como vínculos a artículos que muestran los comandos para tareas más complejas.

>[AZURE.NOTE]Si no ha instalado y configurado todavía Azure PowerShell, obtenga instrucciones en el artículo [Cómo instalar y configurar Azure PowerShell](../install-configure-powershell.md).

## Uso de los comandos de ejemplo
Tendrá que reemplazar parte del texto en los comandos con texto que sea adecuado para su entorno. Los símbolos < and > indican texto que se debe reemplazar. Al reemplazar el texto, quite los símbolos pero deje las comillas en su lugar.

## Obtención de una máquina virtual
Es una tarea básica que utilizará a menudo. Utilícelo para obtener información acerca de una máquina virtual, realizar tareas en una máquina virtual y obtener el resultado para almacenarlo en una variable.

Para obtener información acerca de la VM, ejecute este comando y reemplace todo el contenido de las comillas, incluidos los caracteres < and >:

     Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

Para almacenar la salida en una variable $vm, ejecute:

    $vm = Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## Inicio de sesión en una máquina virtual Windows

Ejecute estos comandos:

>[AZURE.NOTE]Puede obtener el nombre de la máquina virtual y del servicio de nube en la presentación del comando **Get-AzureVM**.
>
	$svcName="<cloud service name>"
	$vmName="<virtual machine name>"
	$localPath="<drive and folder location to store the downloaded RDP file, example: c:\temp >"
	$localFile=$localPath + "" + $vmname + ".rdp"
	Get-AzureRemoteDesktopFile -ServiceName $svcName -Name $vmName -LocalPath $localFile -Launch

## Detención de una máquina virtual

Ejecute este comando:

    Stop-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

>[AZURE.IMPORTANT]Utilice este parámetro para mantener la IP virtual (VIP) del servicio de nube en caso de que sea la última máquina virtual en ese servicio en la nube. <br><br> Si utiliza el parámetro StayProvisioned, se le facturará por la máquina virtual.

## Inicio de una máquina virtual

Ejecute este comando:

    Start-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## Acoplamiento de un disco de datos
Esta tarea requiere unos pocos pasos. En primer lugar, use el cmdlet ****Add-AzureDataDisk**** para agregar el disco al objeto $vm. A continuación, use el cmdlet **Update-AzureVM** para actualizar la configuración de la VM.

También tendrá que decidir si desea adjuntar un disco nuevo o uno que contenga los datos. Para un disco nuevo, el comando crea el archivo .vhd y lo adjunta.

Para adjuntar un disco nuevo, ejecute este comando:

    Add-AzureDataDisk -CreateNew -DiskSizeInGB 128 -DiskLabel "<main>" -LUN <0> -VM <$vm> `
              | Update-AzureVM

Para adjuntar un disco de datos existente, ejecute este comando:

    Add-AzureDataDisk -Import -DiskName "<MyExistingDisk>" -LUN <0> `
              | Update-AzureVM

Para adjuntar discos de datos desde un archivo .vhd existente en el almacenamiento de blobs, ejecute este comando:

    Add-AzureDataDisk -ImportFrom -MediaLocation `
              "<https://mystorage.blob.core.windows.net/mycontainer/MyExistingDisk.vhd>" `
              -DiskLabel "<main>" -LUN <0> `
              | Update-AzureVM

## Creación de una máquina virtual Windows

Para crear una nueva máquina virtual basada en Windows en Azure, siga las instrucciones de [Uso de Azure PowerShell para crear y preconfigurar máquinas virtuales basadas en Windows](virtual-machines-ps-create-preconfigure-windows-vms.md). Este tema le guiará por la creación de un conjunto de comandos de Azure PowerShell que crea una máquina virtual Windows que puede configurarse previamente:

- Con pertenencia al dominio de Active Directory;
- Con discos adicionales;
- Como miembro de un conjunto de carga equilibrada;
- Con una dirección IP estática.

## Creación de una máquina virtual basada en Linux

Use las instrucciones de [Creación y preconfiguración de una máquina virtual Linux con Azure Powershell](virtual-machines-ps-create-preconfigure-linux-vms.md) para crear una nueva máquina virtual basada en Azure que está preconfigurada:

- Con discos adicionales;
- Como miembro de un conjunto de carga equilibrada;
- Con una dirección IP estática.

<!---HONumber=AcomDC_1217_2015-->