<properties
	pageTitle="Creación y carga de un VHD de Linux en Azure"
	description="Aprenda a crear y cargar un disco duro virtual de Azure (VHD) que contiene un sistema operativo Linux."
	services="virtual-machines"
	documentationCenter=""
	authors="szarkos"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/22/2016"
	ms.author="szark"/>

# <a id="nonendorsed"> </a>Información para las distribuciones no aprobadas #

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


**Importante**: el contrato de nivel de servicio de la plataforma Azure se aplica a máquinas virtuales que ejecutan el SO Linux solo cuando se usa una de las [distribuciones aprobadas](virtual-machines-linux-endorsed-distributions.md). Todas las distribuciones de Linux que se ofrezcan en la galería de imágenes de Azure son distribuciones aprobadas con la configuración requerida.

- [Linux en distribuciones aprobadas por Azure](virtual-machines-linux-endorsed-distributions.md)
- [Compatibilidad con las imágenes de Linux en Microsoft Azure](http://support2.microsoft.com/kb/2941892)

Todas las distribuciones que se ejecutan en Azure tendrán que cumplir varios requisitos previos para poder ejecutarse correctamente en la plataforma. Este artículo no pretende ser exhaustivo, ya que cada distribución es diferente y es bastante posible que, aunque cumpla todos los criterios siguientes, todavía tenga que ajustar significativamente el sistema Linux para asegurarse de que se ejecuta correctamente en la plataforma.

Por eso recomendamos que empiece con una de nuestras [distribuciones aprobadas de Linux en Azure](virtual-machines-linux-endorsed-distributions.md) cuando sea posible. Los artículos siguientes le guiarán a través del proceso de preparación de las distintas distribuciones de Linux aprobadas que se admiten en Azure:

- **[Distribuciones basadas en CentOS](virtual-machines-linux-create-upload-vhd-centos.md)**
- **[Debian Linux](virtual-machines-linux-create-upload-vhd-debian.md)**
- **[Oracle Linux](virtual-machines-linux-create-upload-vhd-oracle.md)**
- **[Red Hat Enterprise Linux](virtual-machines-linux-create-upload-vhd-redhat.md)**
- **[SLES y openSUSE](../virtual-machines-linux-create-upload-vhd-suse)**
- **[Ubuntu](virtual-machines-linux-create-upload-vhd-ubuntu.md)**

El resto de este artículo se centrará en ofrecer orientaciones generales para ejecutar su distribución de Linux en Azure.


## <a id="linuxinstall"> </a>Notas generales sobre la instalación de Linux ##

- El formato VHDX no se admite en Azure, solo **VHD fijo**. Puede convertir el disco al formato VHD con el Administrador de Hyper-V o el cmdlet Convert-VHD.

- Al instalar el sistema Linux se recomienda utilizar las particiones estándar en lugar de un LVM (que a menudo viene de forma predeterminada en muchas instalaciones). De este modo se impedirá que el nombre del LVM entre en conflicto con las máquinas virtuales clonadas, especialmente si en algún momento hace falta adjuntar un disco de SO a otra máquina virtual para solucionar problemas. LVM o [RAID](virtual-machines-linux-configure-raid.md) se pueden utilizar en discos de datos si así se prefiere.

- NUMA no se admite para tamaños de máquina virtual más grandes debido a un error en las versiones del kernel de Linux anteriores a la 2.6.37. Este problema afecta principalmente a las distribuciones que usan el kernel Red Hat 2.6.32 de canal de subida. La instalación manual del agente de Linux de Azure (waagent) deshabilitará automáticamente NUMA en la configuración GRUB para el kernel de Linux.

- No cree una partición de intercambio en el disco del SO. El agente de Linux se puede configurar para crear un archivo de intercambio en el disco de recursos temporal. Puede encontrar más información al respecto en los pasos que vienen a continuación.

- El tamaño de todos los archivos VHD debe ser múltiplo de 1 MB.


### Instalación de Linux sin Hyper-V ###

En algunos casos, los instaladores de Linux pueden no incluir los controladores de Hyper-V en el disco RAM inicial (initrd o initramfs) a menos que detecte que está ejecutando un entorno de Hyper-V. Cuando utilice un sistema de virtualización diferente (Virtualbox, KVM, etc.) para preparar su imagen de Linux, puede que necesite volver a generar el initrd para asegurarse de que al menos los módulos kernel `hv_vmbus` y `hv_storvsc` están disponibles en el disco RAM inicial. Esto es un problema conocido al menos en sistemas basados en el nivel superior de distribución de Red Hat.

El mecanismo para volver a generar la imagen initrd o initramfs puede variar dependiendo de la distribución. Consulte la documentación de distribución o el soporte para conocer el procedimiento adecuado. Este es un ejemplo de cómo volver a generar initrd mediante la utilidad `mkinitrd`:

En primer lugar, realice una copia de seguridad de la imagen initrd existente:

	# cd /boot
	# sudo cp initrd-`uname -r`.img  initrd-`uname -r`.img.bak

A continuación, vuelva a generar el initrd con los módulos kernel `hv_vmbus` y `hv_storvsc`:

	# sudo mkinitrd --preload=hv_storvsc --preload=hv_vmbus -v -f initrd-`uname -r`.img `uname -r`


### Cambio de tamaño de los discos duros virtuales ###

Las imágenes VHD en Azure deben tener un tamaño virtual alineado con 1 MB. Normalmente, los discos duros virtuales creados con Hyper-V ya deben alinearse correctamente. Si el disco duro virtual no está alineado correctamente, recibirá un mensaje de error similar al siguiente cuando intente crear una *imagen* desde el disco duro virtual:

	"The VHD http://<mystorageaccount>.blob.core.windows.net/vhds/MyLinuxVM.vhd has an unsupported virtual size of 21475270656 bytes. The size must be a whole number (in MBs).”

Para solucionar este problema, puede cambiar el tamaño de la máquina virtual mediante la consola de administrador de Hyper-V o el del cmdlet de PowerShell [Resize-VHD](http://technet.microsoft.com/library/hh848535.aspx). Si no está ejecutando en un entorno de Windows, se recomienda usar qemu-img para convertir (si es necesario) y cambiar el tamaño del disco duro virtual:

> [AZURE.NOTE] Hay un problema conocido en versiones de qemu-img >= 2.2.1 que da como resultado un VHD con formato incorrecto. El problema se corregirá en una próxima versión de qemu-img. Por ahora es recomendable usar qemu-img versión 2.2.0 o inferior. Referencia: https://bugs.launchpad.net/qemu/+bug/1490611


 1. Cambiar el tamaño del disco duro virtual directamente mediante herramientas como `qemu-img` o `vbox-manage` puede dar como resultado un disco duro virtual que no puede arrancar. Por tanto, se recomienda convertir primero el disco duro virtual a una imagen de disco sin procesar. Si la imagen de VM ya se ha creado como imagen de disco sin procesar (el valor predeterminado para algunos hipervisores como KVM), puede omitir este paso:

		# qemu-img convert -f vpc -O raw MyLinuxVM.vhd MyLinuxVM.raw

 2. Calcular el tamaño necesario de la imagen de disco para asegurarse de que el tamaño virtual está alineado con 1MB. La siguiente secuencia de comandos de shell bash puede ayudar con esto. El script usa "`qemu-img info`" para determinar el tamaño virtual de la imagen de disco y, a continuación, calcula el tamaño con el bloque de 1 MB siguiente:

		rawdisk="MyLinuxVM.raw"
		vhddisk="MyLinuxVM.vhd"

		MB=$((1024*1024))
		size=$(qemu-img info -f raw --output json "$rawdisk" | \
		       gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

		rounded_size=$((($size/$MB + 1)*$MB))
		echo "Rounded Size = $rounded_size"

 3. Cambie el tamaño del disco sin procesar con $rounded\_size como se establece en la secuencia de comandos anterior:

		# qemu-img resize MyLinuxVM.raw $rounded_size

 4. Ahora, convierta el disco sin procesar a un disco duro virtual de tamaño fijo:

		# qemu-img convert -f raw -o subformat=fixed -O vpc MyLinuxVM.raw MyLinuxVM.vhd



## Requisitos para el kernel de Linux ##

Los controladores de los Servicios de integración de Linux (LIS) para Hyper-V y Azure contribuyen directamente en el kernel de Linux del canal de subida. Muchas de las distribuciones que incluyen una versión reciente del kernel de Linux (por ejemplo, 3.x) ya tendrán estos controladores disponibles, o de lo contrario ofrecerán versiones con modificaciones de versiones anteriores de estos controladores con sus kernel. Estos controladores se actualizan constantemente en el kernel del canal de subida con nuevas correcciones y características; por ello, se recomienda cuando sea posible la ejecución de una [distribución aprobada](virtual-machines-linux-endorsed-distributions.md) que incluya estas correcciones y actualizaciones.

Si ejecuta una variante de las versiones **6.0-6.3** de Red Hat Enterprise de Linux, tendrá que instalar los controladores de LIS más recientes para Hyper-V. Podrá encontrar los controladores [en esta ubicación](http://go.microsoft.com/fwlink/p/?LinkID=254263&clcid=0x409). En cuanto a **RHEL 6.4+** (y derivados), los controladores de LIS ya se incluyen con el kernel; por tanto, no se necesitan paquetes de instalación adicionales para ejecutar esos sistemas en Azure.

Si se requiere un kernel personalizado, es muy recomendable utilizar una versión de kernel más reciente (por ejemplo, **3.8+**). En el caso de las distribuciones o los proveedores que mantienen su propio kernel, es necesario cierto esfuerzo para modificar los controladores de LIS con una versión más antigua del kernel del canal de subida al kernel personalizado. Incluso si ya está ejecutando una versión de kernel relativamente reciente, es altamente recomendable mantenerse al tanto de las correcciones ascendentes de los controladores de LIS y realizar modificaciones con versiones anteriores en estos cuando sea necesario. La ubicación de los archivos de origen de controladores de LIS está disponible en el archivo [MAINTAINERS](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/tree/MAINTAINERS) del árbol de origen del kernel de Linux:

	F:	arch/x86/include/asm/mshyperv.h
	F:	arch/x86/include/uapi/asm/hyperv.h
	F:	arch/x86/kernel/cpu/mshyperv.c
	F:	drivers/hid/hid-hyperv.c
	F:	drivers/hv/
	F:	drivers/input/serio/hyperv-keyboard.c
	F:	drivers/net/hyperv/
	F:	drivers/scsi/storvsc_drv.c
	F:	drivers/video/fbdev/hyperv_fb.c
	F:	include/linux/hyperv.h
	F:	tools/hv/

Como mínimo, se sabe que la ausencia de los siguientes parches provocan problemas en Azure y, por tanto, deben incluirse en el kernel. Esta lista no es exhaustiva ni está completa para todas las distribuciones:

- [ata\_piix: Aplaza discos a los controladores de Hyper-V de manera predeterminada](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/ata/ata_piix.c?id=cd006086fa5d91414d8ff9ff2b78fbb593878e3c)
- [storvsc: Cuenta para paquetes en tránsito en la ruta de RESET](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/scsi/storvsc_drv.c?id=5c1b10ab7f93d24f29b5630286e323d1c5802d5c)
- [storvsc: Evita el uso de WRITE\_SAME](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=3e8f4f4065901c8dfc51407e1984495e1748c090)
- [storvsc: Deshabilita WRITE SAME para RAID y controladores del adaptador de host virtual](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=54b2b50c20a61b51199bedb6e5d2f8ec2568fb43)
- [storvsc: Corrección de desreferenciación del puntero NULL](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=b12bb60d6c350b348a4e1460cd68f97ccae9822e)
- [storvsc: Los errores de búfer de anillo pueden dar lugar a una inmovilización de E/S](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=e86fb5e8ab95f10ec5f2e9430119d5d35020c951)


## el Agente de Linux de Azure ##

El [agente de Linux de Azure](virtual-machines-linux-agent-user-guide.md) (waagent) se requiere para aprovisionar correctamente una máquina virtual de Linux en Azure. Puede obtener la versión más reciente, dejar constancia de problemas o enviar solicitudes de extracción en el [repositorio GitHub del agente de Linux](https://github.com/Azure/WALinuxAgent).

- El agente de Linux se publica bajo licencia Apache 2.0. Muchas distribuciones ya proporcionan paquetes RPM o deb para el agente, y en algunos casos se pueden instalar y actualizar con poco esfuerzo.

- El agente de Linux de Azure requiere Python v2.6+.

- El agente requiere también el módulo python-pyasn1. La mayoría de distribuciones lo proporcionan como un paquete independiente que se puede instalar.

- En algunos casos, el Agente de Linux de Azure puede no ser compatible con NetworkManager. Muchos de los paquetes RPM/Deb proporcionados por las distribuciones configuran NetworkManager como conflicto con el paquete waagent y, por lo tanto, desinstalarán NetworkManager al instalar el paquete del agente de Linux.


## Requisitos generales del sistema Linux ##

- Modifique la línea de arranque del kernel en GRUB o GRUB2 para incluir los parámetros siguientes. Así también se asegurará de que todos los mensajes de la consola se envíen al primer puerto serie, lo que puede ayudar al soporte técnico de Azure con los problemas de depuración de errores:

		console=ttyS0 earlyprintk=ttyS0 rootdelay=300

	Así también se asegurará de que todos los mensajes de la consola se envían al primer puerto serie, lo que puede ayudar al soporte técnico de Azure con los problemas de depuración de errores.

	Además de lo anterior, se recomienda *quitar* los parámetros siguientes (si existen):

		rhgb quiet crashkernel=auto

	Los arranques gráfico y silencioso no resultan útiles en un entorno de nube, donde queremos que todos los registros se envíen al puerto serie.

	Es posible dejar la opción `crashkernel` configurada si así se desea, pero tenga en cuenta que este parámetro reducirá la cantidad de memoria disponible en la máquina virtual en 128 MB o más, lo cual puede resultar problemático en tamaños de VM más reducidos.

- Instalación del agente de Linux de Azure

	El agente de Linux de Azure se requiere para aprovisionar una imagen de Linux en Azure. Muchas distribuciones proporcionan el agente como paquete RPM o Deb (el paquete suele llamarse "WALinuxAgent" o "walinuxagent"). El agente también se puede instalar manualmente siguiendo los pasos de la [Guía del agente de Linux](virtual-machines-linux-agent-user-guide.md).

- Asegúrese de que el servidor SSH se haya instalado y configurado para iniciarse en el tiempo de arranque. Este es normalmente el valor predeterminado.

- No cree un espacio de intercambio en el disco del sistema operativo.

	El Agente de Linux de Azure puede configurar automáticamente un espacio de intercambio utilizando el disco de recursos local que se adjunta a la máquina virtual después de aprovisionarse en Azure. Tenga en cuenta que el disco de recursos local es un disco *temporal* que debe vaciarse cuando la máquina virtual se desaprovisiona. Después de instalar el Agente de Linux de Azure (consulte el paso anterior), modifique apropiadamente los parámetros siguientes en /etc/waagent.conf:

		ResourceDisk.Format=y
		ResourceDisk.Filesystem=ext4
		ResourceDisk.MountPoint=/mnt/resource
		ResourceDisk.EnableSwap=y
		ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

- En "/etc/sudoers", debe quitar o convertir en comentario las líneas siguientes, si existen:

		Defaults targetpw
		ALL    ALL=(ALL) ALL

- Como paso final, ejecute los comandos siguientes para desaprovisionar la máquina virtual:

		# sudo waagent -force -deprovision
		# export HISTSIZE=0
		# logout

- A continuación, tendrá que apagar la máquina virtual y cargar el VHD en Azure.

<!---HONumber=AcomDC_0204_2016-->