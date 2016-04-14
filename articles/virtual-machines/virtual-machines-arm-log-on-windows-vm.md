<properties
	pageTitle="Inicio de sesión en una máquina virtual de Windows | Microsoft Azure"
	description="Use el Portal de Azure para iniciar sesión en una máquina virtual de Windows creada con el modelo de implementación del Administrador de recursos."
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
	ms.date="02/03/2016"
	ms.author="cynthn"/>


# Inicie sesión en una máquina virtual de Windows mediante el Portal de Azure.


En el Portal de Azure, usará el botón **Conectar** para iniciar una sesión de Escritorio remoto e iniciar sesión en una VM de Windows.


[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-machines-log-on-windows-server.md).


## Conexión a la máquina virtual

1. Inicie sesión en el Portal de Azure.

2. Haga clic en **Máquinas virtuales** y después seleccione la máquina virtual.

3. En la barra de comandos situada en la parte inferior de la página, haga clic en **Conectar**.

	![Iniciar sesión en la nueva máquina virtual](./media/arm_log_on_windows_vm/rm_windows_connect.png)
	

4. Al hacer clic en **Conectar** se crea y descarga un archivo de Protocolo de escritorio remoto (archivo .rdp). Haga clic en **Abrir** para utilizar este archivo.

	![Iniciar sesión en la nueva máquina virtual](./media/arm_log_on_windows_vm/rm_connect_rdp_file.png)
	
5. En la ventana de Escritorio remoto, haga clic en **Conectar** para continuar.

	![Continuar con la conexión](./media/arm_log_on_windows_vm/rm_connect_unknown.png)

	
6. En la ventana de Seguridad de Windows, escriba las credenciales de la cuenta en la máquina virtual y luego haga clic en **Aceptar**.

 	En la mayoría de los casos, las credenciales son el nombre de usuario y la contraseña de la cuenta local que especificó al crear la máquina virtual. El dominio es el nombre de la máquina virtual especificado como *nombredevm*&#92;*nombredeusuario*.
	
	Si la máquina virtual pertenece a un dominio de su organización, asegúrese de que el nombre de usuario incluye el nombre de ese dominio con el formato *Dominio*&#92;*Nombredeusuario*. La cuenta debe estar en el grupo de administradores o tener privilegios de acceso remoto a la máquina virtual.
	
	Si la máquina virtual es un controlador de dominio, escriba el nombre de usuario y la contraseña de una cuenta de administrador de dominio para ese dominio.

7.	Haga clic en **Sí** para comprobar la identidad de la máquina virtual y finalizar el inicio de sesión.

	![Verificar la identidad de la máquina](./media/arm_log_on_windows_vm/rm_connect_cert.png)

## Pasos siguientes

Si surgen problemas al intentar conectarse, consulte [Solución de problemas de conexiones del Escritorio remoto a una máquina virtual de Azure con Windows](virtual-machines-troubleshoot-remote-desktop-connections.md).

<!---HONumber=AcomDC_0211_2016-->