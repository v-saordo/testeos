<properties
	pageTitle="Extensión Agente de IaaS de SQL Server | Microsoft Azure"
	description="En este tema se usan recursos creados con el modelo de implementación clásica y se describe la extensión del agente SQL Server, que permite que una máquina virtual con SQL Server en Azure use las características de automatización."
	services="virtual-machines"
	documentationCenter=""
	authors="jeffgoll"
	manager="jeffreyg"
   editor="monicar"    
   tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="10/02/2015"
	ms.author="jeffreyg"/>

# Extensión Agente de IaaS de SQL Server

Esta extensión permite que SQL Server en máquinas virtuales de Azure utilice determinados servicios, descritos en este artículo, que solo se puede usar con esta extensión instalada. Esta extensión se instala automáticamente para las imágenes de la galería de SQL Server en el Portal de Azure. Se puede instalar en cualquier máquina virtual de SQL Server en Azure que tenga instalado el agente invitado de máquina virtual de Azure.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo del Administrador de recursos.


## Requisitos previos
Requisitos para usar los cmdlets de Powershell:

- El SDK de línea de comandos de Azure más reciente está [disponible aquí](https://azure.microsoft.com/downloads/)

Requisitos para usar la extensión en la máquina virtual:

- Agente invitado de máquina virtual de Azure
- Windows Server 2012, Windows Server 2012 R2 o posterior
- SQL Server 2012, SQL Server 2014 o posterior

## Servicios disponibles con la extensión

- **Copias de seguridad automatizadas de SQL**: este servicio automatiza la programación de copias de seguridad de todas las bases de datos para la instancia predeterminada de SQL Server en la máquina virtual. Para obtener más información acerca de este servicio, consulte [Copia de seguridad automatizada para SQL Server en máquinas virtuales de Azure](virtual-machines-sql-server-automated-backup.md).
- **Aplicación de revisión automatizada de SQL**: este servicio le permite configurar una ventana de mantenimiento durante la cual tienen lugar las actualizaciones de la máquina virtual, de tal forma que puede evitar las actualizaciones durante las horas pico para la carga de trabajo. Para obtener más información acerca de este servicio, consulte [Aplicación de revisión automatizada para SQL Server en máquinas virtuales de Azure](virtual-machines-sql-server-automated-patching.md).

## Adición de la extensión con Powershell
Si aprovisiona la máquina virtual de SQL Server mediante el [Portal de Azure](https://portal.azure.com/), la extensión se instalará automáticamente. Para las máquinas virtuales de SQL Server aprovisionadas mediante el [Portal de Azure clásico](https://manage.windowsazure.com), o para las máquinas virtuales a las que usted aporta su propia licencia SQL, puede agregar esta extensión a una máquina virtual existente mediante el siguiente cmdlet de PowerShell de Azure.

**Set-AzureVMSqlServerExtension**

### Sintaxis

Set-AzureVMSqlServerExtension [-VM] <IPersistentVM> [[-Version] <string>] [-AutoBackupSettings <AutoBackupSettings>] [-AutoPatchingSetttings <AutoPatchingSetttings>] [-Confirm] [-WhatIf] [<CommonParameters>]

> [AZURE.NOTE] Se recomienda la omisión del parámetro -Version. Sin él, el valor predeterminado es la versión más reciente de la extensión.

### Ejemplo
	Get-AzureVM –ServiceName serviceName –Name vmName | Set-AzureVMSqlServerExtension –AutoBackupSettings $abs | Update-AzureVM**

## Comprobación del estado de la extensión
Si desea comprobar el estado de esta extensión y los servicios asociados a ella, puede utilizar cualquier portal. En los detalles de la máquina virtual existente, busque **Extensiones** en **Configuración**.

También puede utilizar el siguiente cmdlet de Azure PowerShell:

**Get-AzureVMSqlServerExtension**

### Sintaxis

Get-AzureVMSqlServerExtension [[-VM] <IPersistentVM>] [[-Version] <string>] [<parámetrosComunes>]

> [AZURE.NOTE] Puede omitir el parámetro -Version. Sin él, el valor predeterminado es la versión más reciente de la extensión.

### Ejemplo
	Get-AzureVM –ServiceName "service" –Name "vmname" | Get-AzureVMSqlServerExtension

## Eliminación de la extensión con Powershell   
Si desea quitar esta extensión de la máquina virtual, puede utilizar el siguiente cmdlet de Azure Powershell.

**Remove-AzureVMSqlServerExtension**

### Sintaxis
Remove-AzureVMSqlServerExtension -VM <IPersistentVM> [<CommonParameters>]

<!---HONumber=AcomDC_0128_2016-->