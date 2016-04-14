<properties
	pageTitle="Preparar un disco duro virtual en Debian Linux| Microsoft Azure"
	description="Aprenda a crear archivos de VHD en Debian 7 y 8 para implementarlos en Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="SuperScottz"
	manager="timlt"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/01/2015"
	ms.author="mingzhan"/>




# Preparar VHD en Debian de Azure

## Requisitos previos
En esta sección, se supone que ya instaló en un sistema operativo Debian Linux desde un archivo .iso que obtuvo del [sitio web Debian](https://www.debian.org/distrib/) y que descargó a un disco duro virtual. Existen varias herramientas para crear archivos .vhd; Hyper-V es solo un ejemplo. Para obtener instrucciones acerca de cómo usar Hyper-V, consulte [Instalación del rol de Hyper-V y configuración de una máquina virtual](https://technet.microsoft.com/library/hh846766.aspx).


## Notas de instalación

- el reciente formato VHDX no se admite en Azure. Puede convertir el disco al formato VHD con el Administrador de Hyper-V o el cmdlet **convert-vhd**.
- Al instalar el sistema Linux se recomienda utilizar las particiones estándar en lugar de un LVM (que a menudo viene de forma predeterminada en muchas instalaciones). De este modo se impedirá que el nombre del LVM entre en conflicto con las máquinas virtuales clonadas, especialmente si en algún momento hace falta adjuntar un disco de SO a otra máquina virtual para solucionar problemas. LVM o RAID se pueden utilizar en discos de datos si así se prefiere.
- No cree una partición de intercambio en el disco del SO. El agente de Linux de Azure se puede configurar para crear un archivo de intercambio en el disco de recursos temporal. Puede encontrar más información al respecto en los pasos que vienen a continuación.
- El tamaño de todos los archivos VHD debe ser múltiplo de 1 MB.


## Debian 7.x y 8.x

1. En el Administrador de Hyper-V, seleccione la máquina virtual.

2. Haga clic en **Conectar** para abrir una ventana de consola de la máquina virtual.

3. Comente la línea de **deb cdrom** en `/etc/apt/source.list`, si configura la máquina virtual con un archivo ISO.

4. Edite el archivo `/etc/default/grub` y modifique el parámetro **GRUB\_CMDLINE\_LINUX** tal y como se muestra a continuación para incluir parámetros de kernel adicionales de Azure.

        GRUB_CMDLINE_LINUX="console=ttyS0 earlyprintk=ttyS0 rootdelay=300"

5. Recompile el sistema GRUB y ejecute:

        # sudo update-grub

6. Instale los paquetes de dependencia del agente Linux de Azure:

        # apt-get install -y git parted

7.	Instale el agente de Linux de Azure desde GitHub mediante las [instrucciones](virtual-machines-linux-update-agent.md) y elija la versión 2.0.14:

			# wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-2.0.14/waagent
			# chmod +x waagent
			# cp waagent /usr/sbin
			# /usr/sbin/waagent -install -verbose

8. Desaprovisione la máquina virtual, prepárela para aprovisionarla en Azure y ejecute:

        # sudo waagent –force -deprovision
        # export HISTSIZE=0
        # logout

9. Haga clic en** Acción -> Apagar** en el Administrador de Hyper-V. El VHD de Linux ya está listo para cargarse en Azure.

## Uso del script Credativ para crear un VHD en Debian

Tiene un script disponible en el sitio web Credativ que puede ayudarle a crear el disco duro virtual en Debian automáticamente. Puede descargarlo desde [aquí](https://gitlab.credativ.com/de/azure-manage) e instalarlo en su máquina virtual de Linux. Para crear un disco duro virtual en Debian (por ejemplo, en la versión Debian 7) ejecute:

        # azure_build_image --option release=wheezy --option image_prefix=lilidebian7 --option image_size_gb=30 SECTION

Si tiene cualquier problema para usar este script, envíe su problema a Credativ [aquí](https://gitlab.credativ.com/groups/de/issues).

## Pasos siguientes

Ya está listo para usar el disco duro virtual de Debian para crear nuevas máquinas virtuales de Azure. Si esta es la primera vez que está cargando el archivo .vhd en Azure, consulte los pasos 2 y 3 de [Creación y carga de un disco duro virtual que contiene el sistema operativo Linux](virtual-machines-linux-create-upload-vhd.md).

<!---HONumber=AcomDC_0211_2016-->