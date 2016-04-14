<properties
	pageTitle="Inicio de sesión en una máquina virtual | Microsoft Azure"
	description="Use el Portal de Azure clásico para iniciar sesión en una máquina virtual de Windows creada con el modelo de implementación clásica."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/05/2015"
	ms.author="cynthn"/>


# Inicio de sesión en una máquina virtual Windows mediante el Portal de Azure clásico



En el Portal de Azure clásico, usará el botón **Conectar** para iniciar una sesión de Escritorio remoto e iniciar sesión en una máquina virtual de Windows.

¿Desea conectarse a una máquina virtual Linux? Consulte [Inicio de sesión en una máquina virtual con Linux](virtual-machines-linux-how-to-log-on.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo del Administrador de recursos.


## Tutorial en vídeo

A continuación se facilita una guía detallada en vídeo de los pasos de este tutorial. También se tratan los extremos y los puertos públicos y privados utilizados para conectarse a una VM de Windows en Azure.

[AZURE.VIDEO logging-on-to-vm-running-windows-server-on-azure]


## Conexión a la máquina virtual

1. Inicie sesión en el portal clásico de Azure.

2. Haga clic en **Máquinas virtuales** y después seleccione la máquina virtual.

3. En la barra de comandos situada en la parte inferior de la página, haga clic en **Conectar**.

	![Iniciar sesión en la nueva máquina virtual](./media/virtual-machines-log-on-windows-server/connectwindows.png)
	
> [AZURE.TIP]Si el botón Conectar no está disponible, vea las sugerencias de solución de problemas al final de este artículo.

## Iniciar sesión en la nueva máquina virtual

[AZURE.INCLUDE [virtual-machines-log-on-win-server](../../includes/virtual-machines-log-on-win-server.md)]

## Sugerencias de solución de problemas

A continuación se indican algunas cosas que puede probar:

-	Si el botón **Conectar** está inactivo o tiene otros problemas con la conexión a Escritorio remoto, pruebe a restablecer la configuración. Desde el panel de la máquina virtual, en **Vista rápida**, haga clic en **Restablecer configuración remota**.
-	Si experimenta problemas con la contraseña, intente restablecerla. Desde el panel de la máquina virtual, en **Vista rápida**, haga clic en **Restablecer contraseña**.

Si esas sugerencias no funcionan o no son lo que necesita, vea [Solucionar problemas de conexiones de Escritorio remoto a una máquina virtual de Azure basada en Windows](virtual-machines-troubleshoot-remote-desktop-connections.md). En este artículo se le guiará a través del diagnóstico y la resolución de problemas comunes.

<!----HONumber=AcomDC_1203_2015-->