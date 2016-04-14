<properties
	pageTitle="Aplicación automatizada de revisiones de SQL Server en máquinas virtuales | Microsoft Azure"
	description="Explica la característica Aplicación de revisión automatizada para Máquinas virtuales de SQL Server que se ejecuta en Azure."
	services="virtual-machines"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar"
	tags="azure-resource-manager" />
<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="02/03/2016"
	ms.author="jroth" />

# Aplicación de revisión automatizada para SQL Server en Máquinas virtuales de Azure

Aplicación de revisión automatizada establece una ventana de mantenimiento para Máquinas virtuales de Azure con SQL Server 2012 o 2014. Actualizaciones automatizadas solo puede instalarse durante esta ventana de mantenimiento. Para SQL Server, esto garantiza que se actualiza el sistema y que cualquier reinicio asociado se producto en el mejor momento posible para la base de datos. Depende del agente de Iaas de SQL Server.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo del Administrador de recursos.

## Configuración de Aplicación de revisión automatizada en el Portal de Azure

Puede usar el [Portal de Azure](http://go.microsoft.com/fwlink/?LinkID=525040&clcid=0x409) para configurar Aplicación de revisión automatizada cuando cree una nueva máquina virtual de SQL Server.

>[AZURE.NOTE] Aplicación de revisión automatizada se basa en el agente de Iaas de SQL Server. Para instalar y configurar el agente, debe disponer del agente de máquina virtual de Azure en la máquina virtual de destino. Las imágenes de la galería de la máquina virtual más recientes tienen esta opción habilitada de forma predeterminada, pero el agente de máquina virtual de Azure puede faltar en las máquinas virtuales existentes. Si usa su propia imagen de máquina virtual, también tendrá que instalar el agente de IaaS de SQL Server. Para obtener más información, consulte [Agente de máquina virtual y extensiones](https://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/).

La siguiente captura del Portal de Azure muestra estas opciones en **CONFIGURACIÓN OPCIONAL** | **APLICACIÓN DE REVISIÓN AUTOMATIZADA DE SQL**.

![Aplicación de revisión automática de SQL en el Portal de Azure](./media/virtual-machines-sql-server-automated-patching/IC778484.jpg)

Para las máquinas virtuales de SQL Server 2012 o 2014, seleccione la configuración **Aplicación de revisión automatizada** en la sección **Configuración** de las propiedades de la máquina virtual. En la ventana **Aplicación de revisión automatizada**, puede habilitar la característica, establecer la programación de mantenimiento y la hora de inicio y elegir la duración de la ventana de mantenimiento. Esto se muestra en la siguiente captura de pantalla.

![Configuración de Aplicación de revisión automatizada en el Portal de Azure](./media/virtual-machines-sql-server-automated-patching/IC792132.jpg)

>[AZURE.NOTE] Cuando habilita Aplicación de revisión automatizada por primera vez, Azure configura el agente de Iaas de SQL Server en segundo plano. Durante este tiempo, el Portal de Azure no mostrará que se ha configurado Aplicación de revisión automatizada. Espere unos minutos hasta que el agente se instale y configure. Después, el Portal de Azure mostrará la nueva configuración.

## Configuración de Aplicación de revisión automatizada con PowerShell

También puede usar PowerShell para configurar Aplicación de revisión automatizada.

En el ejemplo siguiente, se usa PowerShell para configurar Aplicación de revisión automatizada en una máquina virtual de SQL Server existente. El comando **New-AzureVMSqlServerAutoPatchingConfig** configura una nueva ventana de mantenimiento para actualizaciones automáticas.

    $aps = New-AzureVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoPatchingSettings $aps | Update-AzureVM

Según este ejemplo, la siguiente tabla describe el efecto práctico en la máquina virtual de Azure de destino:

|Parámetro|Efecto|
|---|---|
|**DayOfWeek**|Las revisiones instaladas cada jueves.|
|**MaintenanceWindowStartingHour**|Inicia las actualizaciones a las 11:00 a.m.|
|**MaintenanceWindowsDuration**|Las revisiones deben instalarse en un plazo de 120 minutos. Según la hora de inicio, deben haberse completado a las 1:00 p.m.|
|**PatchCategory**|La única configuración posible para este parámetro es “Importante”.|

La instalación y configuración del agente de Iaas de SQL Server puede tardar algunos minutos.

Para deshabilitar Aplicación de revisión automatizada, ejecute el script sin el parámetro -Enable en New-AzureVMSqlServerAutoPatchingConfig. Al igual que la instalación, la deshabilitación de Aplicación de revisión automatizada puede tardar algunos minutos.

## Deshabilitación y desinstalación del agente de IaaS de SQL Server

Si desea deshabilitar el agente de Iaas de SQL Server para Copia de seguridad automatizada y Aplicación de revisión, use el siguiente comando:

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -Disable | Update-AzureVM

Para desinstalar el agente de IaaS de SQL Server, use la siguiente sintaxis:

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension –Uninstall | Update-AzureVM

También puede desinstalar la extensión con el comando **Remove-AzureVMSqlServerExtension**:

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Remove-AzureVMSqlServerExtension | Update-AzureVM

## Compatibilidad

Los siguientes productos son compatibles con las características del agente de Iaas de SQL Server para Aplicación de revisión automatizada:

- Windows Server 2012

- Windows Server 2012 R2

- SQL Server 2012

- SQL Server 2014

## Pasos siguientes

Una característica relacionada de las máquinas virtuales de SQL Server es la [Copia de seguridad automatizada para SQL Server en Máquinas virtuales de Azure](virtual-machines-sql-server-automated-backup.md).

Revise otros [recursos para ejecutar SQL Server en Máquinas virtuales de Azure](virtual-machines-sql-server-infrastructure-services.md).

<!---HONumber=AcomDC_0204_2016-->