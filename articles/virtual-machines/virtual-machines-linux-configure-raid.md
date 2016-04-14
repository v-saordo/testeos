<properties 
	pageTitle="Configuración de RAID de software en una máquina virtual que ejecuta Linux | Microsoft Azure" 
	description="Aprenda a utilizar mdadm para configurar RAID en Linux en Azure." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="szarkos" 
	writer="szark" 
	manager="timlt" 
	editor=""
	tag="azure-service-management,azure-resource-manager" />

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/17/2015" 
	ms.author="szark"/>



# Configuración del software RAID en Linux
Es un escenario habitual usar el software RAID en máquinas virtuales con Linux en Azure para presentar varios discos de datos conectados como un único dispositivo RAID. Se puede utilizar normalmente para aumentar el rendimiento y permitir una capacidad de proceso mejorada en comparación con el uso de un solo disco.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]
 

## Conexión de discos de datos
Dos o más discos de datos vacíos se necesitarán habitualmente para configurar un dispositivo RAID. Este artículo no entrará en detalles acerca de cómo conectar discos de datos a una máquina virtual con Linux. Consulte el artículo de Microsoft Azure sobre la [conexión de un disco](storage-windows-attach-disk.md#attachempty) para obtener instrucciones detalladas acerca de cómo conectar un disco de datos vacío a una máquina virtual con Linux en Azure.

>[AZURE.NOTE]El tamaño ExtraSmall de la máquina virtual no admite más de un disco de datos conectado a la máquina virtual. Consulte [Tamaños de máquinas virtuales y servicios en la nube de Microsoft Azure](https://msdn.microsoft.com/library/azure/dn197896.aspx) para obtener información detallada acerca de los tamaños de máquinas virtuales y el número de discos de datos admitidos.


## Instalación de la utilidad mdadm

- **Ubuntu**

		# sudo apt-get update
		# sudo apt-get install mdadm

- **CentOS y Oracle Linux**

		# sudo yum install mdadm

- **SLES y openSUSE**

		# zypper install mdadm


## Creación de las particiones de disco
En este ejemplo, vamos a crear una única partición de disco en /dev/sdc. Por tanto, la partición de disco nueva se llamará /dev/sdc1.

- Inicie fdisk para empezar a crear las particiones.

		# sudo fdisk /dev/sdc
		Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
		Building a new DOS disklabel with disk identifier 0xa34cb70c.
		Changes will remain in memory only, until you decide to write them.
		After that, of course, the previous content won't be recoverable.

		WARNING: DOS-compatible mode is deprecated. It's strongly recommended to
				 switch off the mode (command 'c') and change display units to
				 sectors (command 'u').

- Presione "n" en el símbolo del sistema para crear una partición **n**ueva:

		Command (m for help): n

- A continuación, presione "p" para crear una partición **p**rincipal:

		Command action
			e   extended
			p   primary partition (1-4)
		p

- Presione "1" para seleccionar el número de la partición 1:

		Partition number (1-4): 1

- Seleccione el punto de partida de la partición nueva o presione `<enter>` para aceptar el valor predeterminado para colocar la partición al principio del espacio disponible en la unidad:

		First cylinder (1-1305, default 1):
		Using default value 1

- Seleccione el tamaño de la partición; por ejemplo, escriba "+10G" para crear una partición de 10 gigabytes. O simplemente presione `<enter>` para crear una única partición que extienda la unidad completa:

		Last cylinder, +cylinders or +size{K,M,G} (1-1305, default 1305): 
		Using default value 1305

- A continuación, cambie el identificador y el **t**ipo de la partición del identificador predeterminado "83" (Linux) por el identificador "fd" (Linux raid auto):

		Command (m for help): t
		Selected partition 1
		Hex code (type L to list codes): fd

- Por último, escriba la tabla de particiones en la unidad y cierre fdisk:

		Command (m for help): w
		The partition table has been altered!


## Creación de la matriz RAID

1. En el siguiente ejemplo, se "seccionan" (RAID nivel 0) tres particiones ubicadas en tres discos de datos independientes (sdc1, sdd1 y sde1):

		# sudo mdadm --create /dev/md127 --level 0 --raid-devices 3 \
		  /dev/sdc1 /dev/sdd1 /dev/sde1

En este ejemplo, después de ejecutar este comando, se creará un nuevo dispositivo RAID llamado **/dev/md127**. Tenga en cuenta también que, si estos discos de datos formaban parte anteriormente de otra matriz RAID inactiva, puede ser necesario agregar el parámetro `--force` al comando `mdadm`.


2. Cree el sistema de archivos en el nuevo dispositivo RAID.

	**CentOS, Oracle Linux, SLES 12, openSUSE y Ubuntu**

		# sudo mkfs -t ext4 /dev/md127

	**SLES 11**

		# sudo mkfs -t ext3 /dev/md127

3. **SLES 11 y openSUSE**: habilite boot.md y cree mdadm.conf.

		# sudo -i chkconfig --add boot.md
		# sudo echo 'DEVICE /dev/sd*[0-9]' >> /etc/mdadm.conf

	>[AZURE.NOTE]Es posible que sea necesario reiniciar después de realizar estos cambios en los sistemas SUSE. Este paso *no* es necesario en SLES 12.


## Incorporación del nuevo sistema de archivos a /etc/fstab

**Precaución:** la edición incorrecta del archivo /etc/fstab puede tener como resultado un sistema que no se pueda arrancar. Si no está seguro, consulte la documentación de distribución para obtener información sobre cómo editar correctamente ese archivo. También se recomienda realizar una copia de seguridad del archivo /etc/fstab antes de editarlo.

1. Cree el punto de montaje deseado para el nuevo sistema de archivos, por ejemplo:

		# sudo mkdir /data

2. Al editar /etc/fstab, el **UUID** debe usarse para hacer referencia al sistema de archivos en lugar de al nombre del dispositivo. Use la utilidad `blkid` para determinar el UUID del nuevo sistema de archivos:

		# sudo /sbin/blkid
		...........
		/dev/md127: UUID="aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee" TYPE="ext4"

3. Abra /etc/fstab en un editor de texto y agregue una entrada al nuevo sistema de archivos, por ejemplo:

		UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults  0  2

	O en **SLES 11 y openSUSE**:

		/dev/disk/by-uuid/aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext3  defaults  0  2

	A continuación, guarde y cierre /etc/fstab.

4. Pruebe que la entrada /etc/fstab sea correcta:

		# sudo mount -a

	Si este comando genera un mensaje de error, compruebe la sintaxis del archivo /etc/fstab.

	A continuación, ejecute el comando `mount` para garantizar el montaje del sistema de archivos:

		# mount
		.................
		/dev/md127 on /data type ext4 (rw)

5. (Opcional) Parámetros de arranque a prueba de errores

	**Configuración de fstab**

	Muchas distribuciones incluyen los parámetros de montaje `nobootwait` o `nofail` que pueden agregarse al archivo /etc/fstab. Estos parámetros admiten errores al montar un sistema de archivos concreto y permiten que el sistema Linux continúe iniciándose incluso aunque no pueda montar correctamente el sistema de archivos RAID. Consulte la documentación de su distribución para obtener más información sobre estos parámetros.

	Ejemplo (Ubuntu):

		UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults,nobootwait  0  2

	**Parámetros de inicio de Linux**

	Además de los parámetros anteriores, el parámetro de kernel `bootdegraded=true` puede permitir que el sistema se inicie incluso si RAID se percibe como dañado o degradado, por ejemplo si una unidad de datos se quita accidentalmente de la máquina virtual. De manera predeterminada, esto podría resultar en un sistema no iniciable.

	Consulte la documentación sobre la distribución para obtener información acerca de cómo editar correctamente los parámetros de kernel. Por ejemplo, en muchas distribuciones (CentOS, Oracle Linux y SLES 11), estos parámetros pueden agregarse manualmente al archivo "`/boot/grub/menu.lst`". En Ubuntu, este parámetro puede agregarse a la variable `GRUB_CMDLINE_LINUX_DEFAULT` en "/etc/default/grub".

 

<!---HONumber=AcomDC_1223_2015-->