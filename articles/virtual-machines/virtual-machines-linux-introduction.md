<properties
	pageTitle="Introducción a Linux en Azure | Microsoft Azure"
	description="Aprenda a utilizar máquinas virtuales de Linux en Azure."
	services="virtual-machines"
	documentationCenter="python"
	authors="szarkos"
	manager="timlt"
	editor=""
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/01/2016"
	ms.author="szark"/>

#Introducción a Linux en Azure

En este tema se ofrece información general acerca de algunos aspectos relacionados con el uso de las máquinas virtuales con Linux en la nube de Azure. La implementación de una máquina virtual con Linux es un proceso sencillo cuando se usa una imagen de la galería.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## Autenticación: Nombres de usuario, contraseñas y claves SSH.

Al crear una máquina virtual Linux con el Portal de Azure clásico, se le pedirá que facilite un nombre de usuario, una contraseña o una clave pública SSH. La elección de un nombre de usuario para implementar una máquina virtual Linux en Azure está sujeta a la siguiente limitación: no se admiten los nombres de cuentas del sistema (UID <100) ya existentes en la máquina virtual, como por ejemplo, "root".


 - Consulte [Creación de una máquina virtual que ejecuta Linux](virtual-machines-linux-tutorial.md)
 - Consulte [Utilización de SSH con Linux en Azure](virtual-machines-linux-use-ssh-key.md)


## Obtención de privilegios de superusuario con el uso de `sudo`

La cuenta de usuario especificada durante la implementación de la instancia de máquina virtual en Azure es una cuenta con privilegios. El Agente de Linux de Azure configura esta cuenta para elevar privilegios a root (cuenta de superusuario) con la utilidad `sudo`. Después de iniciar sesión con esta cuenta de usuario, podrá ejecutar comandos como root con el uso del la sintaxis de comando

	# sudo <COMMAND>

También puede obtener un shell root con **sudo -s**.

- Consulte [Uso de privilegios raíz en máquinas virtuales con Linux en Azure](virtual-machines-linux-use-root-privileges.md)


## Configuración del firewall

Azure ofrece un filtro de paquetes de entrada que restringe la conectividad a los puertos especificados en el Portal de Azure clásico. De forma predeterminada, el único puerto permitido es SSH. Puede abrir el acceso a puertos adicionales de la máquina virtual con Linux con la configuración de puntos de conexión en el Portal de Azure clásico:

 - Consulte: [Configuración de extremos en una máquina virtual](virtual-machines-set-up-endpoints.md)

Las imágenes de Linux de la Galería de Azure no habilitan el firewall *iptables* de forma predeterminada. Si lo desea, el firewall puede configurarse para proporcionar filtrado adicional.


## Cambios del nombre de host

Al implementar inicialmente una instancia de una imagen de Linux, se le pide que facilite un nombre de host para la máquina virtual. Cuando la máquina virtual está en ejecución, este nombre de host se publica en los servidores DNS de la plataforma, a fin de que varias máquinas virtuales conectadas entre sí puedan realizar búsquedas de direcciones IP con el uso de nombres de host.

Si desea realizar cambios en el nombre de host después de la implementación de una máquina virtual, use el comando

	# sudo hostname <newname>

El Agente de Linux de Azure incluye la funcionalidad para detectar automáticamente este cambio de nombre y configurar correctamente la máquina virtual para que mantenga este cambio y publica este cambio en los servidores DNS de la plataforma.

 - [Guía de usuario del Agente de Linux de Azure](virtual-machines-linux-agent-user-guide.md)

### Cloud-Init
Las imágenes de **Ubuntu** y **CoreOS** usan cloud-init en Azure, que proporciona funcionalidades adicionales para arrancar una máquina virtual.

 - [Inyección de datos personalizados en una máquina virtual de Azure](virtual-machines-how-to-inject-custom-data.md)
 - [Custom Data and Cloud-Init on Microsoft Azure (Datos personalizados y cloud-init en Microsoft Azure)](https://azure.microsoft.com/blog/2014/04/21/custom-data-and-cloud-init-on-windows-azure/)
 - [Create Azure Swap Partitions Using Cloud-Init (Creación de particiones de intercambio de Azure con cloud-init)](https://wiki.ubuntu.com/AzureSwapPartitions)
 - [Uso de CoreOS en Azure](virtual-machines-linux-coreos-how-to.md)


## Captura de imagen de máquina virtual

Azure ofrece la capacidad de capturar el estado de una máquina virtual existente en una imagen que posteriormente puede usarse para implementar instancias adicionales de máquina virtual. El Agente de Linux de Azure puede usarse para la reversión de la personalización realizada durante el proceso de aprovisionamiento. Puede seguir los siguientes pasos para capturar una máquina virtual como una imagen:

1. Ejecute **waagent -deprovision** para deshacer la personalización del aprovisionamiento. O ejecute **waagent -deprovision+user** para eliminar opcionalmente la cuenta de usuario especificada durante el aprovisionamiento y todos los datos asociados.

2. Cierre o apague la máquina virtual.

3. Haga clic en *Capturar* en el Portal de Azure clásico o use Powershell o las herramientas de la CLI para capturar la máquina virtual como una imagen.

 - Consulte: [Captura de una máquina virtual de Linux para usar como plantilla](virtual-machines-linux-capture-image.md)


## Acoplamiento de discos

Las máquinas virtuales disponen de un *disco de recursos* local y temporal acoplado. Debido a que los datos de un disco de recursos podrían no resistir los diversos reinicios, muchas veces los usan aplicaciones y procesos que se ejecutan en la máquina virtual para un almacenamiento de datos transitorio y **temporal**. También se usan para almacenar archivos de intercambio o de paginación para el sistema operativo.

En Linux, el disco de recursos se administra generalmente mediante el Agente de Linux de Azure y se monta automáticamente en **/mnt/resource** (o **/mnt** en las imágenes de Ubuntu).


>[AZURE.NOTE] Tenga en cuenta que el disco de recursos es un disco **temporal**, que debe eliminarse y reformatearse cuando se reinicia la máquina virtual.

En Linux el kernel debe poner al disco de datos el nombre `/dev/sdc` y los usuarios tendrán que particionar ese recurso, darle formato y montarlo. Esto se trata paso a paso en el tutorial: [Acoplamiento de un disco de datos a una máquina virtual](virtual-machines-linux-how-to-attach-disk.md).

 - **Consulte también:** [Configuración del software RAID en Linux](virtual-machines-linux-configure-raid.md)

<!---HONumber=AcomDC_0204_2016-->