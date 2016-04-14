<properties
	pageTitle="Creación de una máquina virtual con Linux | Microsoft Azure"
	description="Aprenda a crear una máquina virtual con Linux o una máquina virtual con Ubuntu mediante una imagen de Azure y la interfaz de línea de comandos de Azure."
	keywords="máquina virtual con linux,máquina virtual linux,máquina virtual con ubuntu" 
	services="virtual-machines"
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager" />

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="10/21/2015"
	ms.author="rasquill"/>

# Creación de una máquina virtual con Linux

> [AZURE.SELECTOR]
- [Portal - Windows](virtual-machines-windows-tutorial.md)
- [PowerShell](virtual-machines-ps-create-preconfigure-windows-resource-manager-vms.md)
- [PowerShell - Template](virtual-machines-create-windows-powershell-resource-manager-template.md)
- [Portal - Linux](virtual-machines-linux-tutorial-portal-rm.md)
- [CLI](virtual-machines-linux-tutorial.md)

Crear una máquina virtual con Linux es muy sencillo desde la línea de comandos o desde el Portal. Este tutorial muestra cómo usar la interfaz de la línea de comandos (CLI) de Azure para Mac, Linux y Windows a fin de crear rápidamente una máquina virtual de servidor Ubuntu que se ejecuta en Azure, conectarse a ella mediante **ssh**, y crear y montar un disco nuevo. Este tema usa una máquina virtual de servidor Ubuntu, pero también puede crear máquinas virtuales Linux mediante [sus propias imágenes como plantillas](virtual-machines-linux-create-upload-vhd.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementación clásica.

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

## Tutorial en vídeo

Se trata del tutorial en formato de vídeo.

[AZURE.VIDEO building-a-linux-virtual-machine-tutorial]

## Instalación de la CLI de Azure

El primer paso es [instalar la CLI de Azure](../xplat-cli-install.md).

Bien. Ahora, asegúrese de que está en el modo de administrador de recursos escribiendo `azure config mode arm`.

Mejor aún. [Inicie sesión con su identificador profesional o educativo](../xplat-cli-connect.md#use-the-log-in-method) escribiendo `azure login` y siga las indicaciones para una experiencia de inicio de sesión interactiva en su cuenta de Azure.

> [AZURE.NOTE]Si tiene un identificador profesional o educativo y sabe que no tiene habilitada la autenticación en dos fases, puede usar `azure login -u` junto con el identificador profesional o educativo para iniciar una sesión sin una sesión interactiva. Si no tiene un identificador profesional o educativo, puede [crear uno desde su cuenta personal de Microsoft](resource-group-create-work-id-from-personal.md).

## Creación de la máquina virtual con Linux

Escriba `azure group create <my-group-name> westus` reemplazando _&lt;mi-nombre-de-grupo&gt;_ por un nombre de grupo que es único (puede utilizar una región diferente si lo desea). Debe ver algo parecido a lo siguiente:

	azure group create myuniquegroupname westus
	info:    Executing command group create
	+ Getting resource group myuniquegroupname
	+ Creating resource group myuniquegroupname
	info:    Created resource group myuniquegroupname
	data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myuniquegroupname
	data:    Name:                myuniquegroupname
	data:    Location:            westus
	data:    Provisioning State:  Succeeded
	data:    Tags:
	data:
	info:    group create command OK

Ahora cree la máquina virtual escribiendo `azure vm quick-create`, y recibirá indicaciones para especificar el resto de parámetros. Use el nombre del grupo de recursos que creó anteriormente y, para el valor **ImageURN**, utilice `canonical:ubuntuserver:14.04.2-LTS:latest`, de modo que su experiencia se parezca a lo siguiente. Tenga en cuenta que el comando `azure vm quick-create` solicita información básica necesaria para crear, hospedar y conectarse a una máquina virtual de Linux, incluido:

- el nombre del grupo de recursos y el nombre de la máquina virtual,
- una ubicación de implementación,
- el tipo de sistema operativo y la cadena URN de imagen,
- un nombre de usuario y una contraseña

y finalmente crea la infraestructura necesaria para hospedar la máquina virtual. Esto incluye:

- Una cuenta de Almacenamiento de Azure para el almacenamiento de disco duro virtual y los discos adicionales
- Una NIC para la máquina virtual
- Una red virtual con una subred
- Una dirección IP pública
- Un subdominio

		azure vm quick-create
		info:    Executing command vm quick-create
		Resource group name: myuniquegroupname
		Virtual machine name: myuniquevmname
		Location name: westus
		Operating system Type [Windows, Linux]: Linux
		ImageURN (format: "publisherName:offer:skus:version"): canonical:ubuntuserver:14.04.2-LTS:latest
		User name: ops
		Password: *********
		Confirm password: *********
		+ Looking up the VM "myuniquevmname"
		info:    Using the VM Size "Standard_D1"
		info:    The [OS, Data] Disk or image configuration requires storage account
		+ Retrieving storage accounts
		info:    Could not find any storage accounts in the region "westus", trying to create new one
		+ Creating storage account "cli3c0464f24f1bf4f014323" in "westus"
		+ Looking up the storage account cli3c0464f24f1bf4f014323
		+ Looking up the NIC "myuni-westu-1432328437727-nic"
		info:    An nic with given name "myuni-westu-1432328437727-nic" not found, creating a new one
		+ Looking up the virtual network "myuni-westu-1432328437727-vnet"
		info:    Preparing to create new virtual network and subnet
		/ Creating a new virtual network "myuni-westu-1432328437727-vnet" [address prefix: "10.0.0.0/16"] with subnet "myuni-westu-1432328437727-snet"+[address prefix: "10.0.1.0/24"]
		+ Looking up the virtual network "myuni-westu-1432328437727-vnet"
		+ Looking up the subnet "myuni-westu-1432328437727-snet" under the virtual network "myuni-westu-1432328437727-vnet"
		info:    Found public ip parameters, trying to setup PublicIP profile
		+ Looking up the public ip "myuni-westu-1432328437727-pip"
		info:    PublicIP with given name "myuni-westu-1432328437727-pip" not found, creating a new one
		+ Creating public ip "myuni-westu-1432328437727-pip"
		+ Looking up the public ip "myuni-westu-1432328437727-pip"
		+ Creating NIC "myuni-westu-1432328437727-nic"
		+ Looking up the NIC "myuni-westu-1432328437727-nic"
		+ Creating VM "myuniquevmname"
		+ Looking up the VM "myuniquevmname"
		+ Looking up the NIC "myuni-westu-1432328437727-nic"
		+ Looking up the public ip "myuni-westu-1432328437727-pip"
		data:    Id                              :/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myuniquegroupname/providers/Microsoft.Compute/virtualMachines/myuniquevmname
		data:    ProvisioningState               :Succeeded
		data:    Name                            :myuniquevmname
		data:    Location                        :westus
		data:    FQDN                            :myuni-westu-1432328437727-pip.westus.cloudapp.azure.com
		data:    Type                            :Microsoft.Compute/virtualMachines
		data:
		data:    Hardware Profile:
		data:      Size                          :Standard_D1
		data:
		data:    Storage Profile:
		data:      Image reference:
		data:        Publisher                   :canonical
		data:        Offer                       :ubuntuserver
		data:        Sku                         :14.04.2-LTS
		data:        Version                     :latest
		data:
		data:      OS Disk:
		data:        OSType                      :Linux
		data:        Name                        :cli3c0464f24f1bf4f0-os-1432328438224
		data:        Caching                     :ReadWrite
		data:        CreateOption                :FromImage
		data:        Vhd:
		data:          Uri                       :https://cli3c0464f24f1bf4f014323.blob.core.windows.net/vhds/cli3c0464f24f1bf4f0-os-1432328438224.vhd
		data:
		data:    OS Profile:
		data:      Computer Name                 :myuniquevmname
		data:      User Name                     :ops
		data:      Linux Configuration:
		data:        Disable Password Auth       :false
		data:
		data:    Network Profile:
		data:      Network Interfaces:
		data:        Network Interface #1:
		data:          Id                        :/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myuniquegroupname/providers/Microsoft.Network/networkInterfaces/myuni-westu-1432328437727-nic
		data:          Primary                   :true
		data:          MAC Address               :00-0D-3A-31-55-31
		data:          Provisioning State        :Succeeded
		data:          Name                      :myuni-westu-1432328437727-nic
		data:          Location                  :westus
		data:            Private IP alloc-method :Dynamic
		data:            Private IP address      :10.0.1.4
		data:            Public IP address       :191.239.51.1
		data:            FQDN                    :myuni-westu-1432328437727-pip.westus.cloudapp.azure.com
		info:    vm quick-create command OK

Su máquina virtual está en ejecución y esperando a que se conecte.

## Conexión a la máquina virtual Linux

Con máquinas virtuales Linux, normalmente se conecta con **ssh**.

> [AZURE.NOTE]En este tema se conecta a una máquina virtual mediante nombres de usuario y contraseñas. Para utilizar pares de claves públicas y privadas para comunicarse con la máquina virtual, consulte [Utilización de SSH con Linux en Azure](virtual-machines-linux-use-ssh-key.md). Puede modificar la conectividad de **SSH** de las máquinas virtuales creadas con el comando `azure vm quick-create` mediante el comando `azure vm reset-access` para restablecer completamente el acceso de **SSH**, agregar o quitar usuarios o agregar archivos de claves públicas para proteger el acceso. Este artículo usa el nombre de usuario y la contraseña con **SSH** para mayor brevedad.

Si no está familiarizado con la conexión con **ssh**, el comando toma la forma `ssh <username>@<publicdnsaddress> -p <the ssh port>`. En este caso, usamos el nombre de usuario y la contraseña del paso anterior y el puerto 22, que es el puerto **ssh** predeterminado.

	ssh ops@myuni-westu-1432328437727-pip.westus.cloudapp.azure.com -p 22
	The authenticity of host 'myuni-westu-1432328437727-pip.westus.cloudapp.azure.com (191.239.51.1)' can't be established.
	ECDSA key fingerprint is bx:xx:xx:xx:xx:xx:xx:xx:xx:x:x:x:x:x:x:xx.
	Are you sure you want to continue connecting (yes/no)? yes
	Warning: Permanently added 'myuni-westu-1432328437727-pip.westus.cloudapp.azure.com,191.239.51.1' (ECDSA) to the list of known hosts.
	ops@myuni-westu-1432328437727-pip.westus.cloudapp.azure.com's password:
	Welcome to Ubuntu 14.04.2 LTS (GNU/Linux 3.16.0-37-generic x86_64)

	 * Documentation:  https://help.ubuntu.com/

	  System information as of Fri May 22 21:02:32 UTC 2015

	  System load: 0.37              Memory usage: 2%   Processes:       207
	  Usage of /:  41.4% of 1.94GB   Swap usage:   0%   Users logged in: 0

	  Graph this data and manage this system at:
	    https://landscape.canonical.com/

	  Get cloud support with Ubuntu Advantage Cloud Guest:
	    http://www.ubuntu.com/business/services/cloud

	0 packages can be updated.
	0 updates are security updates.



	The programs included with the Ubuntu system are free software;
	the exact distribution terms for each program are described in the
	individual files in /usr/share/doc/*/copyright.

	Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
	applicable law.

	ops@myuniquevmname:~$

Ahora que está conectado a la máquina virtual, está listo para conectar un disco.

## Conexión y montaje de un disco

La asociación de un nuevo disco es algo que se hace rápidamente. Escriba simplemente `azure vm disk attach-new <myuniquegroupname> <myuniquevmname> <size-in-GB>` para crear y vincular un nuevo disco GB para la máquina virtual. Debe tener un aspecto similar al siguiente:

	azure vm disk attach-new myuniquegroupname myuniquevmname 5
	info:    Executing command vm disk attach-new
	+ Looking up the VM "myuniquevmname"
	info:    New data disk location: https://cliexxx.blob.core.windows.net/vhds/myuniquevmname-20150526-0xxxxxxx43.vhd
	+ Updating VM "myuniquevmname"
	info:    vm disk attach-new command OK


Ahora, busquemos el disco con `dmesg | grep SCSI` (el método utilizado para detectar el nuevo disco puede variar). En este caso, es similar a lo siguiente:

	dmesg | grep SCSI
	[    0.294784] SCSI subsystem initialized
	[    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
	[    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
	[    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
	[ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk

y en el caso de este tema, el disco `sdc` es el que queremos. Ahora, particionamos el disco con `sudo fdisk /dev/sdc`, suponiendo en su caso que el disco estaba `sdc`, lo convertimos en un disco principal en la partición 1 y aceptamos los demás valores predeterminados.

	sudo fdisk /dev/sdc
	Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
	Building a new DOS disklabel with disk identifier 0x2a59b123.
	Changes will remain in memory only, until you decide to write them.
	After that, of course, the previous content won't be recoverable.

	Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

	Command (m for help): n
	Partition type:
	   p   primary (0 primary, 0 extended, 4 free)
	   e   extended
	Select (default p): p
	Partition number (1-4, default 1): 1
	First sector (2048-10485759, default 2048):
	Using default value 2048
	Last sector, +sectors or +size{K,M,G} (2048-10485759, default 10485759):
	Using default value 10485759

Para crear la partición, escribimos `p` en el símbolo del sistema:

	Command (m for help): p

	Disk /dev/sdc: 5368 MB, 5368709120 bytes
	255 heads, 63 sectors/track, 652 cylinders, total 10485760 sectors
	Units = sectors of 1 * 512 = 512 bytes
	Sector size (logical/physical): 512 bytes / 512 bytes
	I/O size (minimum/optimal): 512 bytes / 512 bytes
	Disk identifier: 0x2a59b123

	   Device Boot      Start         End      Blocks   Id  System
	/dev/sdc1            2048    10485759     5241856   83  Linux

	Command (m for help): w
	The partition table has been altered!

	Calling ioctl() to re-read partition table.
	Syncing disks.

Y escribimos un sistema de archivos en la partición mediante el comando **mkfs**, especificando el tipo de sistema de archivos y el nombre del dispositivo. En este tema, estamos usando `ext4` y `/dev/sdc1` mostrados anteriormente:

	sudo mkfs -t ext4 /dev/sdc1
	mke2fs 1.42.9 (4-Feb-2014)
	Discarding device blocks: done
	Filesystem label=
	OS type: Linux
	Block size=4096 (log=2)
	Fragment size=4096 (log=2)
	Stride=0 blocks, Stripe width=0 blocks
	327680 inodes, 1310464 blocks
	65523 blocks (5.00%) reserved for the super user
	First data block=0
	Maximum filesystem blocks=1342177280
	40 block groups
	32768 blocks per group, 32768 fragments per group
	8192 inodes per group
	Superblock backups stored on blocks:
		32768, 98304, 163840, 229376, 294912, 819200, 884736

	Allocating group tables: done
	Writing inode tables: done
	Creating journal (32768 blocks): done
	Writing superblocks and filesystem accounting information: done

Cree un directorio para montar el nuevo sistema de archivos mediante `mkdir`:

	sudo mkdir /datadrive

Y monte el directorio con `mount`:

	sudo mount /dev/sdc1 /datadrive

El disco de datos está ahora listo para usarse como `/datadrive`.

	ls
	bin   datadrive  etc   initrd.img  lib64       media  opt   root  sbin  sys  usr  vmlinuz
	boot  dev        home  lib         lost+found  mnt    proc  run   srv   tmp  var

> [AZURE.NOTE]También se puede conectar a la máquina virtual de Linux mediante una clave SSH para la identificación. Para obtener más información, consulte [Utilización de SSH con Linux en Azure](virtual-machines-linux-use-ssh-key.md).

## Pasos siguientes

Recuerde que el nuevo disco no estará disponible normalmente en la máquina virtual si esta se reinicia a menos que escriba dicha información en su archivo [fstab](http://en.wikipedia.org/wiki/Fstab). Si lo desea, puede agregar varios discos más y [configurar RAID](virtual-machines-linux-configure-raid.md).

Para obtener más información sobre Linux en Azure, consulte:

- [Informática de código abierto y Linux en Azure](virtual-machines-linux-opensource.md)

- [Cómo utilizar la interfaz de línea de comandos de Azure](../virtual-machines-command-line-tools.md)

- [Implementación de una aplicación LAMP con la extensión CustomScript para Linux](virtual-machines-linux-script-lamp.md)

- [Extensión de máquina virtual Docker para Linux en Azure](virtual-machines-docker-vm-extension.md)

<!---HONumber=AcomDC_0114_2016-->