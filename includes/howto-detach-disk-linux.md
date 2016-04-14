<properties writer="kathydav" editor="tysonn" manager="timlt" />


Cuando ya no necesite un disco de datos que se encuentra conectado a una máquina virtual, puede desconectarlo fácilmente. Esto quita el disco de la máquina virtual, pero no lo quita del almacenamiento. Si desea volver a usar los datos existentes en el disco, puede acoplarlo de nuevo a la misma máquina virtual (o a otra).

> [AZURE.NOTE] Una máquina virtual en Azure utiliza distintos tipos de discos, como un disco del sistema operativo, un disco temporal local y discos de datos opcionales. Se recomienda utilizar discos de datos para almacenar datos para una máquina virtual. Para obtener más información, vea [Acerca de los discos y discos duros virtuales para máquinas virtuales](virtual-machines-disks-vhds.md). No es posible desconectar un disco del sistema operativo a menos que también elimine la máquina virtual.

## Buscar el disco

Antes de poder desacoplar un disco de una máquina virtual, tienes que saber cuál es el número de unidad lógica (LUN), que es un identificador para el disco que se va a desacoplar. Para ello, sigue estos pasos:

1. 	Abre la interfaz de la línea de comandos (CLI) de Azure para Mac, Linux y Windows y conéctate a tu suscripción de Azure. Para más información, consulta el tema [Conexión a Azure desde la CLI de Azure](../articles/xplat-cli-connect.md).

2.  Escribe `azure config
 	mode asm` para asegurarte de que estás en modo de administración de servicios de Azure, que es el valor predeterminado.

3. 	Averigua qué discos están conectados a la máquina virtual utilizando `azure vm disk list
	<virtual-machine-name>` de la siguiente manera:

		$azure vm disk list ubuntuVMasm
		info:    Executing command vm disk list
		+ Fetching disk images
		+ Getting virtual machines
		+ Getting VM disks
		data:    Lun  Size(GB)  Blob-Name                         OS
		data:    ---  --------  --------------------------------  -----
		data:         30        ubuntuVMasm-2645b8030676c8f8.vhd  Linux
		data:    1    10        test.VHD
		data:    0    30        ubuntuVMasm-76f7ee1ef0f6dddc.vhd
		info:    vm disk list command OK

4. 	Anota el LUN o **número de unidad lógica** para el disco que quieres desacoplar.


## Desacoplar el disco

Cuando hayas encontrado el número LUN del disco, podrás desacoplarlo:

1. 	Desacopla el disco seleccionado de la máquina virtual ejecutando el comando `azure vm disk detach
 	<virtual-machine-name> <LUN>` de la siguiente manera:

		$azure vm disk detach ubuntuVMasm 0
		info:    Executing command vm disk detach
		+ Getting virtual machines
		+ Removing Data-Disk
		info:    vm disk detach command OK

2. 	Puedes comprobar si se desacopló el disco ejecutando este comando:

		$azure vm disk list ubuntuVMasm
		info:    Executing command vm disk list
		+ Fetching disk images
		+ Getting virtual machines
		+ Getting VM disks
		data:    Lun  Size(GB)  Blob-Name                         OS
		data:    ---  --------  --------------------------------  -----
		data:         30        ubuntuVMasm-2645b8030676c8f8.vhd  Linux
		data:    1    10        test.VHD
		info:    vm disk list command OK

El disco desacoplado permanece en el almacenamiento pero ya no estará acoplado a una máquina virtual.

<!---HONumber=AcomDC_0204_2016-->