<properties
	pageTitle="Iniciar sesión en una VM de Linux en Azure | Microsoft Azure"
	description="Aprenda a iniciar sesión en una máquina virtual de Azure que ejecuta Linux utilizando un cliente Secure Shell (SSH)."
	services="virtual-machines"
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/08/2015"
	ms.author="rasquill"/>


#Inicio de sesión en una máquina virtual con Linux #

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager deployment model](virtual-machines-linux-tutorial-portal-rm.md).

Debe instalar un cliente de SSH en el equipo que desea usar para iniciar sesión en la máquina virtual. Existen muchos programas cliente de SSH donde elegir. Las siguientes son algunas posibles opciones:

- En un equipo que ejecute un sistema operativo Windows, quizás desee utilizar un cliente de SSH, como PuTTY. Para obtener más información, consulte la página de descarga [PuTTY Download Page](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) (Página de descarga de PuTTY).
- Para una máquina virtual que ejecuta un sistema operativo Linux, use un cliente de Shell seguro (SSH) para iniciar sesión; es difícil pensar en una distribución que aún no tenga esto instalado de forma predeterminada. Vea [Cómo usar SSH](virtual-machines-linux-use-ssh-key.md) para más información sobre Linux.


>[AZURE.NOTE]Para obtener consejos acerca de los requisitos y la solución de problemas, consulte [Conexión a una máquina virtual de Azure con RDP o SSH](http://go.microsoft.com/fwlink/p/?LinkId=398294).

Este procedimiento le muestra cómo usar el programa PuTTY para tener acceso a la máquina virtual.

1. Busque **Host Name** (Nombre de host) y **Port information** (Información de puerto) en el [Portal de administración](http://manage.windowsazure.com). Puede encontrar la información que necesita en el panel de la máquina virtual. Haga clic en el nombre de la máquina virtual y busque **Detalles de SSH** en la sección **Vista rápida** del panel.

	![Obtener detalles de SSH](./media/virtual-machines-linux-how-to-log-on/sshdetails.png)

2. Abra el programa PuTTY.

3. Escriba los valores en Host Name (Nombre de host) y Port information (Información de puerto) que recopiló en el panel y, a continuación, haga clic en **Open** (Abrir).

	![Abrir PuTTY](./media/virtual-machines-linux-how-to-log-on/putty.png)

4. Inicie sesión en la máquina virtual usando la cuenta que especificó cuando se creó la máquina. Para obtener más detalles sobre cómo crear una máquina virtual con el nombre de usuario y contraseña, consulte [Creación de una máquina virtual que ejecuta Linux](virtual-machines-linux-tutorial.md).

	![Iniciar sesión en la nueva máquina virtual](./media/virtual-machines-linux-how-to-log-on/sshlogin.png)

>[AZURE.NOTE]La extensión VMAccess le puede ayudar a restablecer la contraseña o clave de SSH si la ha olvidado. Si ha olvidado el nombre de usuario, puede utilizar la extensión para crear otro nuevo con autoridad sudo. Para obtener instrucciones, consulte [Restablecimiento de una contraseña o de SSH para máquinas virtuales de Linux].

Ahora puede trabajar con la máquina virtual igual que hace con cualquier otro servidor.

<!-- LINKS -->
[Restablecimiento de una contraseña o de SSH para máquinas virtuales de Linux]: http://go.microsoft.com/fwlink/p/?LinkId=512138

<!---HONumber=AcomDC_1217_2015-->