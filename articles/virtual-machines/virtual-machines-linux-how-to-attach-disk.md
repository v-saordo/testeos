<properties
	pageTitle="Acoplamiento de un disco a una máquina virtual Linux | Microsoft Azure"
	description="Obtenga información sobre cómo acoplar un disco de datos a una máquina virtual de Azure que esté ejecutando Linux e inicialícelo para que esté listo para usarse."
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/07/2016"
	ms.author="dkshir"/>

# Acoplamiento de un disco de datos a una máquina virtual Linux

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo del Administrador de recursos.


Puede acoplar tanto discos vacíos como discos que contienen datos. En ambos casos, se trata realmente de archivos .vhd que residen en una cuenta de almacenamiento de Azure. También en ambos casos, una vez acoplado el disco, tendrá que inicializarlo para que esté listo para utilizarse.

> [AZURE.NOTE]Es recomendable utilizar uno o varios discos independientes para almacenar los datos de una máquina virtual. Al crear una máquina virtual de Azure, esta cuenta con un disco para el sistema operativo y un disco temporal. **No utilice el disco temporal para almacenar datos.** Como señala su nombre, esta ofrece únicamente almacenamiento temporal. No ofrece redundancia o copias de seguridad porque no reside en el almacenamiento de Azure. El Agente de Linux de Azure normalmente administra el disco temporal, y este se monta automáticamente en **/mnt/resource** (o **/mnt** en las imágenes de Ubuntu). Por otro lado, el kernel de Linux podría denominar al disco de datos de forma similar a `/dev/sdc`, y los usuarios necesitarán crear particiones, dar formato y montar ese recurso. Consulte la [Guía de usuario del Agente de Linux de Azure][Agent] para obtener más información.

[AZURE.INCLUDE [howto-attach-disk-windows-linux](../../includes/howto-attach-disk-linux.md)]

## Inicialización de un nuevo disco de datos en Linux

Puede usar las mismas instrucciones para inicializar varios discos de datos, usando el identificador de dispositivo adecuado, tal como se muestra a continuación.

1. Conexión a una máquina virtual. Para obtener más información, vea [Inicio de sesión en una máquina virtual con Linux][Logon].



2. A continuación deberá buscar el identificador de dispositivo para inicializar el disco de datos. Existen dos formas de hacerlo:

	a) En la ventana SSH, escriba el siguiente comando y especifique la contraseña de la cuenta que creó para administrar la máquina virtual:

			$sudo grep SCSI /var/log/messages

	Para las distribuciones de Ubuntu recientes, puede que necesite usar `sudo grep SCSI /var/log/syslog` porque el inicio de sesión en `/var/log/messages` puede deshabilitarse de forma predeterminada.

	Puede buscar el identificador del último disco de datos conectado en los mensajes que se muestran.

	![Obtener mensajes de disco](./media/virtual-machines-linux-how-to-attach-disk/DiskMessages.png)

	OR

	b) Use el comando `lsscsi` para conocer el identificador de dispositivo. `lsscsi` puede instalarse mediante `yum install lsscsi` (en distribuciones basadas en Red Hat) o mediante `apt-get install lsscsi` (en distribuciones basadas en Debian). Puede encontrar el disco que busca por su _lun_ o **número de unidad lógica**. Por ejemplo, el _lun_ de los discos que conectó puede verse fácilmente en `azure vm disk list <virtual-machine>`, de la manera siguiente:

			~$ azure vm disk list ubuntuVMasm
			info:    Executing command vm disk list
			+ Fetching disk images
			+ Getting virtual machines
			+ Getting VM disks
			data:    Lun  Size(GB)  Blob-Name                         OS
			data:    ---  --------  --------------------------------  -----
			data:         30        ubuntuVMasm-2645b8030676c8f8.vhd  Linux
			data:    1    10        test.VHD
			data:    2    30        ubuntuVMasm-76f7ee1ef0f6dddc.vhd
			info:    vm disk list command OK

	Compare esto con la salida de `lsscsi` para la misma máquina virtual de ejemplo:

			adminuser@ubuntuVMasm:~$ lsscsi
			[1:0:0:0]    cd/dvd  Msft     Virtual CD/ROM   1.0   /dev/sr0
			[2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
			[3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
			[5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc
			[5:0:0:1]    disk    Msft     Virtual Disk     1.0   /dev/sdd
			[5:0:0:2]    disk    Msft     Virtual Disk     1.0   /dev/sde

	El último número de la tupla en cada fila es el _lun_. Vea `man lsscsi` para obtener más información.

3. En la ventana SSH, escriba el siguiente comando para crear un nuevo dispositivo y, a continuación, especifique la contraseña de la cuenta:

		$sudo fdisk /dev/sdc

	>[AZURE.NOTE]En este ejemplo, es posible que tenga que utilizar `sudo -i` en algunas distribuciones si /sbin o /usr/sbin no se encuentran en su `$PATH`.


4. Cuando se le pida, escriba **n** para crear otra partición.


	![Crear un dispositivo nuevo](./media/virtual-machines-linux-how-to-attach-disk/DiskPartition.png)

5. Cuando se le pida, escriba **p** para que la partición sea la partición principal, escriba **1** para que sea la primera partición y luego escriba "enter" para aceptar el valor predeterminado para el cilindro. En algunos sistemas, puede mostrar los valores predeterminados del primer sector y el último sector, en lugar del cilindro. Puede aceptar estos valores predeterminados.


	![Crear partición](./media/virtual-machines-linux-how-to-attach-disk/DiskCylinder.png)



6. Escriba **p** para ver los detalles del disco en el que se va a crear la partición.


	![Enumerar la información del disco](./media/virtual-machines-linux-how-to-attach-disk/DiskInfo.png)



7. Escriba **w** para escribir la configuración del disco.


	![Escribir los cambios del disco](./media/virtual-machines-linux-how-to-attach-disk/DiskWrite.png)

8. Cree el sistema de archivos en la partición nueva. Anexe el número de partición (1) al identificador del dispositivo. Por ejemplo, escriba el siguiente comando y luego especifique la contraseña de la cuenta:

		# sudo mkfs -t ext4 /dev/sdc1

	![Crear sistema de archivos](./media/virtual-machines-linux-how-to-attach-disk/DiskFileSystem.png)

	>[AZURE.NOTE]Tenga en cuenta que los sistemas SUSE Linux Enterprise 11 únicamente admiten acceso de solo lectura a sistemas de archivos ext4. Para estos sistemas, es recomendable dar formato al nuevo sistema de archivos como ext3 en vez de ext4.


9. Cree un directorio para montar el nuevo sistema de archivos. Como ejemplo, escriba el siguiente comando y, a continuación, especifique la contraseña de la cuenta:

		# sudo mkdir /datadrive


10. Escriba el siguiente comando para montar la unidad:

		# sudo mount /dev/sdc1 /datadrive

	El disco de datos está ahora listo para usarse como **/datadrive**.


11. Agregue la nueva unidad a /etc/fstab:

	Para asegurarse de que la unidad se vuelve a montar automáticamente después de reiniciar, debe agregarse al archivo /etc/fstab. Además, se recomienda encarecidamente que se use el UUID (identificador único global) en /etc/fstab para hacer referencia a la unidad en lugar de solo el nombre del dispositivo (es decir, /dev/sdc1). Para buscar el UUID de la unidad nueva, puede usar la utilidad **blkid**:

		# sudo -i blkid

	El resultado será similar al siguiente:

		/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
		/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
		/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"


	>[AZURE.NOTE]La edición incorrecta del archivo **/etc/fstab** puede tener como resultado un sistema que no se pueda arrancar. Si no está seguro, consulte la documentación de distribución para obtener información sobre cómo editar correctamente ese archivo. También se recomienda realizar una copia de seguridad del archivo /etc/fstab antes de editarlo.

	Después abra el archivo **/etc/fstab** en un editor de texto. Tenga en cuenta que /etc/fstab es un archivo del sistema, por lo que deberá utilizar `sudo` para editarlo, por ejemplo:

		# sudo vi /etc/fstab

	En este ejemplo usaremos el valor de UUID para el nuevo dispositivo **/dev/sdc1** que se creó en los pasos anteriores y el punto de montaje **/datadrive**: Agregue la siguiente línea al final del archivo **/etc/fstab**:

		UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults   1   2

	O bien, en sistemas basados en SUSE Linux, es posible que tenga que utilizar un formato diferente:

		/dev/disk/by-uuid/33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext3   defaults   1   2

	Ahora puede probar que el sistema de archivos está correctamente montado con solo desmontar y volver a montar el sistema de archivos, es decir, usando el punto de montaje de ejemplo `/datadrive` que se creó en los pasos anteriores:

		# sudo umount /datadrive
		# sudo mount /datadrive

	Si el comando `mount` genera un error, compruebe la sintaxis correcta del archivo /etc/fstab. Si se crean particiones o unidades de datos adicionales, tendrá que especificarlas también en /etc/fstab por separado.

	Debe hacer que se pueda escribir en unidad mediante este comando:

		# sudo chmod go+w /datadrive

>[AZURE.NOTE]Posteriormente, la eliminación de un disco de datos sin editar fstab podría provocar un error en el inicio de la máquina virtual. Si ocurre habitualmente, la mayoría de distribuciones proporcionan las opciones de fstab `nofail` y/o `nobootwait` que permitirán que el sistema se inicie incluso si el disco no se monta al arrancar. Consulte la documentación de su distribución para obtener más información sobre estos parámetros.

## Recursos adicionales
[Inicio de sesión en una máquina virtual con Linux][Logon]

[Desconexión de un disco de una máquina virtual de Linux](virtual-machines-linux-how-to-detach-disk.md)

[Uso de la CLI de Azure con la API de administración de servicios](virtual-machines-command-line-tools.md)

<!--Link references-->
[Agent]: virtual-machines-linux-agent-user-guide.md
[Logon]: virtual-machines-linux-how-to-log-on.md

<!---HONumber=AcomDC_0114_2016-->