
#Desacoplamiento de un disco de datos de una máquina virtual con la interfaz de la línea de comandos

Cuando ya no necesite un disco de datos conectado a una máquina virtual, podrá desacoplarlo fácilmente. Esto quita el disco de la máquina virtual, pero no lo quita del almacenamiento. Si desea volver a usar los datos existentes en el disco, puede acoplarlo de nuevo a la misma máquina virtual (o a otra).

> [AZURE.NOTE]Una máquina virtual en Azure utiliza distintos tipos de discos, como un disco del sistema operativo, un disco temporal local y discos de datos opcionales. Se recomienda utilizar discos de datos para almacenar datos para una máquina virtual. Para obtener más información sobre los discos, consulte [Acerca de discos e imágenes](http://go.microsoft.com/fwlink/p/?LinkId=263439). Actualmente no es posible desacoplar un disco del sistema operativo.


1. Obtenga la lista de los discos acoplados a su máquina virtual:

        vm disk list <vm-name>

    Si se omite `<vm-name>`, obtendrá una lista de todos los discos en su suscripción.


2. Desacople un disco:

        vm disk detach <vm-name> <lun>

    `lun` identifica el disco que se va a desacoplar, y deberá ser un número que se encuentre en la lista de discos de la máquina virtual.

Tutorial de ejemplo, incluida la salida de terminal:

    ~$ azure vm disk list kmlinux
    info:    Executing command vm disk list
    + Fetching disk images
    + Getting virtual machines
    + Getting VM disks
    data:    Lun  Size(GB)  Blob-Name                               OS
    data:    ---  --------  --------------------------------------  -----
    data:         30        kmlinux-kmlinux-2015-02-05.vhd          Linux
    data:    1    5         kmlinux-f8ef0006ab182209.vhd
    data:    2    7         kmlinux-602362868dbb7439.vhd
    info:    vm disk list command OK
    ~$ azure vm disk detach kmlinux 2
    info:    Executing command vm disk detach
    + Getting virtual machines
    + Removing Data-Disk
    info:    vm disk detach command OK
    ~$ azure vm disk list kmlinux
    info:    Executing command vm disk list
    + Fetching disk images
    + Getting virtual machines
    + Getting VM disks
    data:    Lun  Size(GB)  Blob-Name                               OS
    data:    ---  --------  --------------------------------------  -----
    data:         30        kmlinux-kmlinux-2015-02-05.vhd          Linux
    data:    1    5         kmlinux-f8ef0006ab182209.vhd
    info:    vm disk list command OK

<!---HONumber=August15_HO6-->