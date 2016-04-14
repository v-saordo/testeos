<properties
	pageTitle="Instalación de Trend Micro Deep Security en una máquina virtual | Microsoft Azure"
	description="En este artículo se describe cómo instalar y configurar la seguridad de Trend Micro en una máquina virtual creada con el modelo de implementación clásica en Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/14/2015"
	ms.author="dkshir"/>


# Instalación y configuración de Trend Micro Deep Security como servicio en una máquina virtual de Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo del Administrador de recursos.


En este artículo se muestra cómo instalar y configurar Trend Micro Deep Security como servicio en una máquina virtual nueva o existente con Windows Server. Deep Security como servicio proporciona protección antimalware, firewall, sistema de prevención contra intrusiones y supervisión de integridad.

El cliente se instala como una extensión de seguridad usando el Agente de máquina virtual. En una nueva máquina virtual, instalará el agente de máquina virtual junto con el Agente de Deep Security. En una máquina virtual que no tenga el Agente de máquina virtual, primero necesitará descargarlo e instalarlo. Este artículo trata ambas situaciones.

Si tiene una suscripción existente de Trend Micro para una solución local, puede usarla para ayudar a proteger sus máquinas virtuales de Azure. Si todavía no es cliente, puede suscribirse para una prueba. Para obtener más información acerca de esta solución, consulte la publicación del blog de Trend Micro [Microsoft Azure VM Agent Extension For Deep Security (Extensión del agente de máquina virtual de Microsoft Azure para Deep Security)](http://go.microsoft.com/fwlink/p/?LinkId=403945).

## Instalación del Agente de Deep Security en una nueva máquina virtual

El [Portal de Azure clásico](http://manage.windowsazure.com) permite instalar el Agente de máquina virtual y la extensión de seguridad de Trend Micro cuando usa la opción **Desde la galería** para crear la máquina virtual. Este enfoque proporciona una forma sencilla de agregar protección desde Trend Micro si crea una sola máquina virtual.

La opción **Desde la galería** abre un asistente que le ayuda configurar la máquina virtual. Utilice la última página del asistente para instalar el Agente de máquina virtual y la extensión de seguridad de Trend Micro. Para obtener instrucciones generales, consulte [Creación de una máquina virtual que ejecuta Windows en el Portal de Azure clásico](virtual-machines-windows-tutorial-classic-portal.md). Cuando se encuentre en la última página del asistente, realice las acciones siguientes:

1.	En **Agente de máquina virtual**, active la casilla **Instalar Agente de VM**.

2.	En **Extensiones de seguridad**, active **Agente de Trend Micro Deep Security**.

	![Install the VM Agent and the Deep Security Agent](./media/virtual-machines-install-trend/InstallVMAgentandTrend.png)

3.	Haga clic en la marca de verificación para crear la máquina virtual.

## Instalación del Agente de Deep Security en una máquina virtual existente

Para llevar a cabo esta tarea, necesitará lo siguiente:

- El módulo Azure PowerShell, con una versión 0.8.2 o posterior, instalado en el equipo local. Puede comprobar la versión de Azure PowerShell que ha instalado con el comando **Get-Module azure | format-table version**. Para obtener instrucciones y un vínculo a la versión más reciente, consulte [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md).

- El agente de máquina virtual instalado en la MV de destino.

En primer lugar, compruebe que el agente de máquina virtual ya está instalado. Introduzca el nombre de servicio de nube y el nombre de la máquina virtual y, a continuación, ejecute los siguientes comandos en un símbolo de sistema de Azure PowerShell con nivel de administrador. Reemplace todo el contenido dentro de las comillas, incluidos los caracteres < and >.

	$CSName = "<cloud service name>"
	$VMName = "<virtual machine name>"
	$vm = Get-AzureVM -ServiceName $CSName -Name $VMName
	write-host $vm.VM.ProvisionGuestAgent

Si no conoce el nombre del servicio en la nube y de la máquina virtual, ejecute **Get-AzureVM** para mostrar esa información para todas las máquinas virtuales de su suscripción actual.

Si el comando **write-host** devuelve **True**, el agente de máquina virtual está instalado. Si devuelve **False**, consulte las instrucciones y un vínculo a la descarga en la publicación del blog de Azure [VM Agent and Extensions - Part 2 (Agente de máquina virtual extensiones: parte 2)](http://go.microsoft.com/fwlink/p/?LinkId=403947).

Si el agente de máquina virtual está instalado, ejecute estos comandos.

	$Agent = Get-AzureVMAvailableExtension TrendMicro.DeepSecurity -ExtensionName TrendMicroDSA

	Set-AzureVMExtension -Publisher TrendMicro.DeepSecurity –Version $Agent.Version -ExtensionName TrendMicroDSA -VM $vm | Update-AzureVM

## Pasos siguientes

El agente tarda unos minutos en empezar la ejecución cuando se instala. Después de ello, necesitará activar Deep Security en la máquina virtual de forma que pueda ser administrado por Deep Security Manager. Consulte lo siguiente para obtener instrucciones adicionales:

- Artículo de Trend sobre esta solución, [Instant-On Cloud Security for Microsoft Azure (Seguridad en la nube instantánea para Microsoft Azure)](http://go.microsoft.com/fwlink/?LinkId=404101)
- [Script de Windows PowerShell de ejemplo](http://go.microsoft.com/fwlink/?LinkId=404100) para configurar la máquina virtual
- [Instrucciones](http://go.microsoft.com/fwlink/?LinkId=404099) para el ejemplo

## Recursos adicionales

[Inicio de sesión en una máquina virtual con Windows Server]

[Características y extensiones de máquina virtual de Azure]


<!--Link references-->
[Inicio de sesión en una máquina virtual con Windows Server]: virtual-machines-log-on-windows-server.md
[Características y extensiones de máquina virtual de Azure]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409

<!---HONumber=AcomDC_0204_2016-->