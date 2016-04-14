
Para obtener más información sobre los discos, consulte [Acerca de los discos de máquina virtual en Azure](http://go.microsoft.com/fwlink/p/?LinkId=403697).

##<a id="cliattachempty"></a>Acoplamiento de un disco vacío
El acoplamiento de un disco vacío es la forma más sencilla de agregar un disco de datos. Ejecute el siguiente comando para acoplar un nuevo disco vacío:

    vm disk attach-new <vm-name> <size-in-gb> [blob-url]

Reemplace `vm-name` por el nombre de la máquina virtual, y `size-in-gb` por el tamaño del nuevo disco. Puede opcionalmente usar una dirección URL de blob como el último argumento para especificar de manera explícita el blob de destino que crear. Si no especifica una dirección URL de blob, un objeto de blob se generará automáticamente.

Ejecute el siguiente comando para comprobar que se ha creado el disco:

    vm disk list <vm-name>

El siguiente es un ejemplo de tutorial de los comandos anteriores, incluida la salida de terminal:

    ~$ azure vm disk attach-new pinkylinux 20 http://pinkylinux.blob.core.windows.net/vhds/pinkydisk1.vhd
    info:   Executing command vm disk attach-new
    + Getting virtual machines
    + Adding Data-Disk
    info:   vm disk attach-new command OK
    ~$ azure vm disk list pinkylinux
    info:    Executing command vm disk list
    + Fetching disk images
    + Getting virtual machines
    + Getting VM disks
    data:    Lun  Size(GB)  Blob-Name                               OS
    data:    ---  --------  --------------------------------------  -----
    data:         30        pinkylinux-pinkylinux-2015-02-05.vhd    Linux
    data:    0    5         pinkydisk1.vhd
    data:    1    20        pinkylinux-f8ef0006ab182209.vhd
    info:    vm disk list command OK

<!---HONumber=August15_HO6-->