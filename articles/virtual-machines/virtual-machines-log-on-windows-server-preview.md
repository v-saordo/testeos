<properties
	pageTitle="Inicio de sesión en una máquina virtual de Windows Server | Microsoft Azure"
	description="Obtenga información sobre cómo iniciar sesión en una máquina virtual de Windows Server con el Portal de Azure y el modelo de implementación del Administrador de recursos."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="cynthn"/>

# Inicio de sesión en una máquina virtual con Windows Server 

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-machines-log-on-windows-server.md).

Usará el botón **Conectar** en el Portal de Azure para iniciar una sesión de Escritorio remoto. En primer lugar, se conectará a la máquina virtual y, a continuación, iniciará sesión.

## Conexión a la máquina virtual

1. Si aún no lo ha hecho, inicie sesión en el [Portal de Azure](https://portal.azure.com/).

2.	En el menú del Centro, haga clic en **Máquinas virtuales**.

3.	Seleccione la máquina virtual en la lista.

4. En la hoja de la máquina virtual, haga clic en **Conectar**.

	![Conexión a la máquina virtual](./media/virtual-machines-log-on-windows-server-preview/preview-portal-connect.png)

## Iniciar sesión en la nueva máquina virtual

[AZURE.INCLUDE [virtual-machines-log-on-win-server](../../includes/virtual-machines-log-on-win-server.md)]

## Solución de problemas

Si las sugerencias sobre el inicio de sesión no ayudan o no son lo que necesita, consulte [Solucionar problemas de conexiones de Escritorio remoto a una máquina virtual de Azure basada en Windows](virtual-machines-troubleshoot-remote-desktop-connections.md). En este artículo se le guiará a través del diagnóstico y la resolución de problemas comunes.

<!---HONumber=AcomDC_0128_2016-->