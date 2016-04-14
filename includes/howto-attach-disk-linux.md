
Para obtener más detalles acerca de los discos, consulte [Acerca de los discos y discos duros virtuales para máquinas virtuales](virtual-machines-disks-vhds.md).

<a id="attachempty"></a>
## Acoplamiento de un disco vacío
El acoplamiento de un disco vacío supone el método más sencillo de agregar un disco de datos, porque Azure crea el archivo .vhd en su lugar y lo almacena en la cuenta de almacenamiento.

1.  Abre la interfaz de la línea de comandos (CLI) de Azure para Mac, Linux y Windows y conéctate a tu suscripción de Azure. Para más información, consulta el tema [Conexión a Azure desde la CLI de Azure](../articles/xplat-cli-connect.md).

2.  Escribe `azure config
 	mode asm` para asegurarte de que estás en modo de administración de servicios de Azure, que es el valor predeterminado.

3.  Usa el comando `azure vm disk attach-new` para crear y asociar un disco nuevo, tal y como se muestra a continuación. Ten en cuenta que el _ubuntuVMasm_ se reemplazará por el nombre de la máquina Virtual de Linux que creaste en la suscripción. El número 30 es el tamaño del disco en GB en este ejemplo.

        azure vm disk attach-new ubuntuVMasm 30

4.	Una vez creado y conectado el disco de datos, este aparece en la salida de `azure vm disk list
    <virtual-machine-name>` de esta manera:

        $ azure vm disk list ubuntuVMasm
        info:    Executing command vm disk list
        + Fetching disk images
        + Getting virtual machines
        + Getting VM disks
        data:    Lun  Size(GB)  Blob-Name                         OS
        data:    ---  --------  --------------------------------  -----
        data:         30        ubuntuVMasm-2645b8030676c8f8.vhd  Linux
        data:    0    30        ubuntuVMasm-76f7ee1ef0f6dddc.vhd
        info:    vm disk list command OK

<a id="attachexisting"></a>
## Acoplamiento de un disco existente

El acoplamiento de un disco existente requiere que disponga de un .vhd disponible en la cuenta de almacenamiento.

1. 	Abre la interfaz de la línea de comandos (CLI) de Azure para Mac, Linux y Windows y conéctate a tu suscripción de Azure. Para más información, consulta el tema [Conexión a Azure desde la CLI de Azure](../articles/xplat-cli-connect.md).

2.  Asegárate de que estás en modo de administración de servicios de Azure, que es el valor predeterminado. Si cambiaste el modo de administración de recursos, simplemente vuelve atrás escribiendo `azure config mode asm`.

3.	Averigua si el disco duro virtual que quieres adjuntar se cargó en la suscripción de Azure utilizando:

        $azure vm disk list
    	info:    Executing command vm disk list
    	+ Fetching disk images
    	data:    Name                                          OS
    	data:    --------------------------------------------  -----
    	data:    myTestVhd                                     Linux
    	data:    ubuntuVMasm-ubuntuVMasm-0-201508060029150744  Linux
    	data:    ubuntuVMasm-ubuntuVMasm-0-201508060040530369
    	info:    vm disk list command OK

4.  Si no encuentras el disco que quieres usar, puedes cargar un VHD local a tu suscripción mediante `azure vm disk create` o `azure vm disk upload`. Este sería un ejemplo:

        $azure vm disk create myTestVhd2 .\TempDisk\test.VHD -l "East US" -o Linux
		info:    Executing command vm disk create
		+ Retrieving storage accounts
		info:    VHD size : 10 GB
		info:    Uploading 10485760.5 KB
		Requested:100.0% Completed:100.0% Running:   0 Time:   25s Speed:    82 KB/s
		info:    Finishing computing MD5 hash, 16% is complete.
		info:    https://portalvhdsq1s6mc7mqf4gn.blob.core.windows.net/disks/test.VHD was
		uploaded successfully
		info:    vm disk create command OK

	También puedes usar el comando `azure vm disk upload` para cargar un VHD a una cuenta de almacenamiento específica. [Aquí](virtual-machines-command-line-tools.md#commands-to-manage-your-azure-virtual-machine-data-disks) puedes obtener más información sobre los comandos para administrar los discos de datos de tu máquina virtual de Azure.

5.  Escribe el siguiente comando para adjuntar el VHD cargado que quieras a la máquina virtual:

		$azure vm disk attach ubuntuVMasm myTestVhd
		info:    Executing command vm disk attach
		+ Getting virtual machines
		+ Adding Data-Disk
		info:    vm disk attach command OK

	Asegúrate de reemplazar _ubuntuVMasm_ por el nombre de la máquina virtual y _myTestVhd_ por el disco duro virtual que quieres.

6.	Puedes comprobar si el disco está conectado a la máquina virtual con el comando `azure vm disk list
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


> [AZURE.NOTE]
Después de conectar un disco de datos, tendrá que iniciar sesión en la máquina virtual e inicializar el disco para que la máquina virtual pueda usar el disco para el almacenamiento.

<!---HONumber=AcomDC_0204_2016-->