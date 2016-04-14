<properties
	pageTitle="Instalación de Symantec Endpoint Protection en una máquina virtual | Microsoft Azure"
	description="Obtenga información acerca de cómo instalar y configurar la extensión de seguridad de Symantec Endpoint Protection en una máquina virtual de Azure nueva o existente creada con el modelo de implementación clásica."
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

# Instalación y configuración de Endpoint Protection en una máquina virtual de Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo del Administrador de recursos.


En este artículo se muestra cómo instalar y configurar el cliente Symantec Endpoint Protection en una máquina virtual nueva o existente con Windows Server. Este es el cliente completo, que incluye servicios como protección contra virus y spyware, firewall y prevención de intrusiones.

El cliente se instala como una extensión de seguridad usando el Agente de máquina virtual. En una nueva máquina virtual, instalará el agente junto con el cliente del extremo. En una máquina virtual existente sin el agente, primero necesitará descargar e instalar dicho agente. Este artículo trata ambas situaciones.

Si tiene una suscripción existente de Symantec para una solución local, puede usarla para proteger sus máquinas virtuales de Azure. Si todavía no es cliente, puede suscribirse para una prueba. Para obtener más información acerca de esta solución, consulte[ Symantec Endpoint Protection en la plataforma de Microsoft Azure][Symantec]. Esta página también tiene vínculos a información de licencia e instrucciones para instalar el cliente si ya es un cliente de Symantec.

## Instalación de Symantec Endpoint Protection en una nueva máquina virtual

El [Portal de Azure clásico][Portal] permite instalar el Agente de máquina virtual y la extensión de seguridad de Symantec cuando usa la opción **Desde la galería** para crear la máquina virtual. Este enfoque proporciona una forma sencilla de agregar protección desde Symantec si crea una sola máquina virtual.

La opción **Desde la galería** abre un asistente que le ayuda configurar la máquina virtual. Utilice la última página del asistente para instalar el Agente de máquina virtual y la extensión de seguridad de Symantec.

Para obtener instrucciones generales, consulte [Creación de una máquina virtual que ejecuta Windows Server][Create]. Cuando llegue a la última página del asistente:

1.	En el agente de VM, la opción **Instalar agente de VM** ya debe estar activada.

2.	En Extensiones de seguridad, active la casilla **Symantec Endpoint Protection**.


	![Install the VM Agent and the Endpoint Protection Client](./media/virtual-machines-install-symantec/InstallVMAgentandSymantec.png)

3.	Haga clic en la marca de verificación en la parte inferior de la página para crear la máquina virtual.

## Instalación de Symantec Endpoint Protection en una máquina virtual existente

Antes de comenzar, necesitará lo siguiente:

- El módulo Azure PowerShell, versión 0.8.2 o posteriores, en el equipo de trabajo. Puede comprobar la versión de Azure PowerShell que ha instalado con el comando **Get-Module azure | format-table version**. Para obtener instrucciones y un vínculo a la versión más reciente, consulte [Instalación y configuración de Azure PowerShell][PS]. Asegúrese de iniciar sesión en su suscripción de Azure.

- El agente de máquina virtual que se ejecuta en la máquina virtual de Azure.

En primer lugar, compruebe que el agente de máquina virtual ya está instalado en la máquina virtual. Introduzca el nombre de servicio de nube y el nombre de la máquina virtual y, a continuación, ejecute los siguientes comandos en un símbolo de sistema de Azure PowerShell con nivel de administrador. Reemplace todo el contenido dentro de las comillas, incluidos los caracteres < and >.

> [AZURE.TIP]Si no conoce los nombres del servicio en la nube y de la máquina virtual, ejecute **Get-AzureVM** para mostrar los nombres de todas las máquinas virtuales de su suscripción actual.

	$CSName = "<cloud service name>"
	$VMName = "<virtual machine name>"
	$vm = Get-AzureVM -ServiceName $CSName -Name $VMName
	write-host $vm.VM.ProvisionGuestAgent

Si el comando **write-host** muestra **True**, el agente de la máquina virtual está instalado. Si muestra **False**, consulte las instrucciones y un vínculo a la descarga en la publicación del blog de Azure [VM Agent and Extensions - Part 2 (Agente de máquina virtual extensiones: parte 2)][Agent].

Si está instalado el agente de VM, ejecute estos comandos para instalar al agente de Symantec Endpoint Protection.

	$Agent = Get-AzureVMAvailableExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection

	Set-AzureVMExtension -Publisher Symantec –Version $Agent.Version -ExtensionName SymantecEndpointProtection -VM $vm | Update-AzureVM

Para comprobar que la extensión de seguridad de Symantec se ha instalado y está actualizada:

1.	Iniciar sesión en la nueva máquina virtual. Para obtener instrucciones, consulte [Inicio de sesión en una máquina virtual con Windows Server][Logon].
2.	Para Windows Server 2008 R2, haga clic en **Inicio > Symantec Endpoint Protection**. Para Windows Server 2012 o Windows Server 2012 R2, en la pantalla de inicio, escriba **Symantec** y, a continuación, haga clic en **Symantec Endpoint Protection**.
3.	En la pestaña **Estado** de la ventana **Status-Symantec Endpoint Protection**, aplique actualizaciones o reinicie en caso necesario.

## Recursos adicionales

[Inicio de sesión en una máquina virtual con Windows Server][Logon]

[Características y extensiones de máquina virtual de Azure][Ext]


<!--Link references-->
[Symantec]: http://go.microsoft.com/fwlink/p/?LinkId=403942

[Portal]: http://manage.windowsazure.com

[Create]: virtual-machines-windows-tutorial-classic-portal.md

[PS]: ../powershell-install-configure.md

[Agent]: http://go.microsoft.com/fwlink/p/?LinkId=403947

[Logon]: virtual-machines-log-on-windows-server.md

[Ext]: http://go.microsoft.com/fwlink/p/?linkid=390493

<!---HONumber=AcomDC_1203_2015-->