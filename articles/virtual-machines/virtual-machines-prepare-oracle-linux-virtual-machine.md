<properties
pageTitle="Preparación de una máquina virtual Oracle Linux para Azure | Microsoft Azure"
description="Configuración paso a paso de una máquina virtual de Oracle con Linux en Microsoft Azure."
services="virtual-machines"
authors="bbenz"
documentationCenter="virtual-machines"
tags="azure-service-management,azure-resource-manager"
/>

<tags
ms.service="virtual-machines"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="vm-linux"
ms.workload="infrastructure-services"
ms.date="06/22/2015"
ms.author="bbenz" />

# Preparación de una máquina virtual Oracle Linux para Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


-   [Preparación de una máquina virtual Oracle Linux 6.4+ para Azure](virtual-machines-linux-create-upload-vhd-oracle.md)

-   [Preparación de una máquina virtual Oracle Linux 7.0+ para Azure](virtual-machines-linux-create-upload-vhd-oracle.md)

## Requisitos previos
En este artículo se supone que ya ha instalado un sistema operativo Oracle Linux en un disco duro virtual. Existen varias herramientas para crear archivos .vhd; por ejemplo, una solución de virtualización como Hyper-V. Para obtener instrucciones, consulte [Instalación de Hyper-V y creación de una máquina Virtual](http://technet.microsoft.com/library/hh846766.aspx).

**Notas sobre la instalación de Oracle Linux**

- El kernel compatible Red Hat de Oracle y su UEK3 (Unbreakable Enterprise Kernel) se admiten en Hyper-V y Azure. Para obtener los mejores resultados, asegúrese de actualizar el kernel a la versión más reciente mientras prepara el VHD de Oracle Linux.

- El UEK2 de Oracle no se admite en Hyper-V y Azure porque no incluye los controladores requeridos.

- el reciente formato VHDX no se admite en Azure. Puede convertir el disco al formato VHD con el Administrador de Hyper-V o el cmdlet Convert-VHD.

- Al instalar el sistema Linux se recomienda usar las particiones estándar en lugar de un LVM (que a menudo viene de forma predeterminada en muchas instalaciones). De este modo se impedirá que el nombre del LVM entre en conflicto con las máquinas virtuales clonadas, especialmente si en algún momento hace falta adjuntar un disco de SO a otra máquina virtual para solucionar problemas. LVM o [RAID](virtual-machines-linux-configure-raid.md) se pueden utilizar en discos de datos si así se prefiere.

- NUMA no se admite para tamaños de máquina virtual más grandes debido a un error en las versiones del kernel de Linux anteriores a la 2.6.37. Este problema afecta principalmente a las distribuciones que usan el kernel Red Hat 2.6.32 de canal de subida. La instalación manual del agente de Linux de Azure (waagent) deshabilitará automáticamente NUMA en la configuración GRUB para el kernel de Linux. Puede encontrar más información al respecto en los pasos que vienen a continuación.

- No cree una partición de intercambio en el disco del SO. El agente de Linux se puede configurar para crear un archivo de intercambio en el disco de recursos temporal. Puede encontrar más información al respecto en los pasos que vienen a continuación.

- El tamaño de todos los archivos VHD debe ser múltiplo de 1 MB.

- Asegúrese de que el repositorio `Addons` está habilitado. Elija editar el archivo `/etc/yum.repo.d/public-yum-ol6.repo`(Oracle Linux 6) o `/etc/yum.repo.d/public-yum-ol7.repo`(Oracle Linux) y cambie la línea `enabled=0` a `enabled=1` en **[ol6\_addons]** o **[ol7\_addons]** en este archivo.


## Oracle Linux 6.4+
Debe completar los pasos de configuración específicos del sistema operativo para que la máquina virtual se ejecute en Azure.

1. Seleccione la máquina virtual en el panel central del Administrador de Hyper-V.

2. Haga clic en **Conectar** para abrir la ventana de la máquina virtual.

3. Desinstale NetworkManager ejecutando el comando siguiente:

		# sudo rpm -e --nodeps NetworkManager

	>[AZURE.NOTE] si el paquete todavía no está instalado, se producirá un mensaje de error en este comando. Se espera que esto sea así.

4. Cree un archivo llamado **network** en el directorio /etc/sysconfig/ que contenga el texto siguiente:

	`NETWORKING=yes` `HOSTNAME=localhost.localdomain`

5.  Cree un archivo llamado **ifcfg-eth0** en el directorio /etc/sysconfig/network-scripts/ que contenga el texto siguiente:

		DEVICE=eth0
		ONBOOT=yes
		BOOTPROTO=dhcp
			TYPE=Ethernet
			USERCTL=no
			PEERDNS=yes
		IPV6INIT=no

6.  Mueva (o elimine) las reglas udev para impedir que se generen reglas estáticas para la interfaz Ethernet. Estas reglas causan problemas al clonar una máquina virtual en Azure o Hyper-V:

		# sudo mkdir -m 0700 /var/lib/waagent
		# sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/ 2>/dev/null
		# sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/ 2>/dev/null

7.  Asegúrese de que el servicio de red se inicia en el arranque ejecutando el comando siguiente:

		# chkconfig network on

8.  Instale python-pyasn1 ejecutando el comando siguiente:

		# sudo yum install python-pyasn1

9.  Modifique la línea de arranque de kernel de su configuración grub para que incluya parámetros de kernel adicionales para Azure. Para ello, abra "/boot/grub/menu.lst" en un editor de texto y asegúrese de que el kernel predeterminado incluye los parámetros siguientes:

		console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off

	Así también se asegurará de que todos los mensajes de la consola se envían al primer puerto serie, lo que puede ayudar al soporte técnico de Azure con los problemas de depuración de errores. Esto deshabilitará NUMA debido a un error en el kernel compatible Red Hat de Oracle.

	Además de lo anterior, se recomienda *quitar* los parámetros siguientes:

		rhgb quiet crashkernel=auto

	Los arranques gráfico y silencioso no resultan útiles en un entorno de nube, donde queremos que todos los registros se envíen al puerto serie.

	Es posible dejar la opción `crashkernel` configurada si así se desea, pero tenga en cuenta que este parámetro reducirá la cantidad de memoria disponible en la máquina virtual en 128 MB o más. Esto puede ser problemático en los tamaños más pequeños de máquina virtual.

10.  Asegúrese de que el servidor SSH se haya instalado y configurado para iniciarse en el tiempo de arranque. Este es normalmente el valor predeterminado.

11.  Instale el Agente de Linux de Azure ejecutando el comando siguiente:

		# sudo yum install WALinuxAgent

	Tenga en cuanta que al instalar el paquete WALinuxAgent se eliminarán los paquetes NetworkManager y NetworkManager-gnome, si es que aún no se han eliminado como se indica en el paso 2.

12.  No cree espacio de intercambio en el disco del SO.

	El Agente de Linux de Azure puede configurar automáticamente un espacio de intercambio usando el disco de recursos local que se adjunta a la máquina virtual después de aprovisionarse en Azure. Tenga en cuenta que el disco de recursos local es un disco *temporal* que debe vaciarse cuando la máquina virtual se desaprovisiona. Después de instalar el Agente de Linux de Azure (consulte el paso anterior), modifique apropiadamente los parámetros siguientes en /etc/waagent.conf:

		ResourceDisk.Format=y

		ResourceDisk.Filesystem=ext4

		ResourceDisk.MountPoint=/mnt/resource

		ResourceDisk.EnableSwap=y

		ResourceDisk.SwapSizeMB=2048 ## NOTE: set this to whatever you need it to be.

13.  Ejecute los comandos siguientes para desaprovisionar la máquina virtual y prepararla para aprovisionarse en Azure:

		# sudo waagent -force -deprovision
		# export HISTSIZE=0
		# logout

14.  Haga clic en** Acción -> Apagar** en el Administrador de Hyper-V. El VHD de Linux ya está listo para cargarse en Azure.

## Oracle Linux 7.0+
**Cambios en Oracle Linux 7**

Preparar una máquina virtual de Oracle Linux 7 para Azure es muy similar al proceso para Oracle Linux 6. Sin embargo, hay varias diferencias importantes que vale la pena tener en cuenta:

-   Tanto el kernel compatible Red Hat como el UEK3 de Oracle se admiten en Azure. Se recomienda el kernel UEK3.

-   El paquete NetworkManager ya no entra en conflicto con el agente de Linux de Azure. Este paquete está instalado de manera predeterminada y recomendamos que no se quite.

-   GRUB2 se utiliza ahora como cargador de arranque predeterminado; por ello, el proceso de edición de los parámetros de kernel ha cambiado (ver más adelante).

-   XFS es ahora el sistema de archivos predeterminado. El sistema de archivos ext4 se puede seguir utilizando si así se desea.

**Pasos de configuración**

1.  En el Administrador de Hyper-V, seleccione la máquina virtual.

2.  Haga clic en **Conectar** para abrir una ventana de consola de la máquina virtual.

3.  Cree un archivo llamado **network** en el directorio /etc/sysconfig/ que contenga el texto siguiente:

		NETWORKING=yes
		HOSTNAME=localhost.localdomain

4.  Cree un archivo llamado **ifcfg-eth0** en el directorio /etc/sysconfig/network-scripts/ que contenga el texto siguiente:

		DEVICE=eth0
		ONBOOT=yes
		BOOTPROTO=dhcp
		TYPE=Ethernet
		USERCTL=no
			PEERDNS=yes
		IPV6INIT=no

5.  Mueva (o elimine) las reglas udev para impedir que se generen reglas estáticas para la interfaz Ethernet. Estas reglas pueden causar problemas al clonar una máquina virtual en Microsoft Azure o Hyper-V:

		# sudo mkdir -m 0700 /var/lib/waagent
		# sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/ 2>/dev/null
		# sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/ 2>/dev/null

6.  Asegúrese de que el servicio de red se inicia en el arranque ejecutando el comando siguiente:

		# sudo chkconfig network on

7.  Instale el paquete python-pyasn1 ejecutando el comando siguiente:

		# sudo yum install python-pyasn1

8.  Ejecute el comando siguiente para borrar los metadatos de yum actuales e instalar las actualizaciones:

		# sudo yum clean all
		# sudo yum -y update

9.  Modifique la línea de arranque de kernel de su configuración grub para que incluya parámetros de kernel adicionales para Azure. Para ello, abra "/etc/default/grub" en un editor de texto y edite el parámetro GRUB\_CMDLINE\_LINUX; por ejemplo:

		GRUB\_CMDLINE\_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0"

	Así también se asegurará de que todos los mensajes de la consola se envían al primer puerto serie, lo que puede ayudar al soporte técnico de Azure con los problemas de depuración de errores. Además de lo anterior, se recomienda *quitar* los parámetros siguientes:

		rhgb quiet crashkernel=auto

	Los arranques gráfico y silencioso no resultan útiles en un entorno de nube, donde queremos que todos los registros se envíen al puerto serie.

	Es posible dejar la opción `crashkernel` configurada si así se desea, pero tenga en cuenta que este parámetro reducirá la cantidad de memoria disponible en la máquina virtual en 128 MB o más. Esto puede ser problemático en los tamaños más pequeños de máquina virtual.

10.  Cuando termine de editar "/etc/default/grub", ejecute el comando siguiente para volver a compilar la configuración de grub:

		# sudo grub2-mkconfig -o /boot/grub2/grub.cfg

11.  Asegúrese de que el servidor SSH se haya instalado y configurado para iniciarse en el tiempo de arranque. Este es normalmente el valor predeterminado.

12.  Instale el Agente de Linux de Azure ejecutando el comando siguiente:

		# sudo yum install WALinuxAgent

13.  No cree espacio de intercambio en el disco del SO.

	El Agente de Linux de Azure puede configurar automáticamente un espacio de intercambio usando el disco de recursos local que se adjunta a la máquina virtual después de aprovisionarse en Azure. Tenga en cuenta que el disco de recursos local es un disco *temporal* que debe vaciarse cuando la máquina virtual se desaprovisiona. Después de instalar el Agente de Linux de Azure (consulte el paso anterior), modifique apropiadamente los parámetros siguientes en /etc/waagent.conf:

		ResourceDisk.Format=y
		ResourceDisk.Filesystem=ext4
		ResourceDisk.MountPoint=/mnt/resource
		ResourceDisk.EnableSwap=y
		ResourceDisk.SwapSizeMB=2048 ## NOTE: Set this to whatever you need it to be.

14.  Ejecute los comandos siguientes para desaprovisionar la máquina virtual y prepararla para aprovisionarse en Azure:

		# sudo waagent -force -deprovision
		# export HISTSIZE=0
		# logout

15.  Haga clic en** Acción -> Apagar** en el Administrador de Hyper-V. El VHD de Linux ya está listo para cargarse en Azure.

<!---HONumber=AcomDC_0211_2016-->