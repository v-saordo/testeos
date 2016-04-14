<properties
	pageTitle="Acerca de los discos y los discos duros virtuales | Microsoft Azure"
	description="Conozca los aspectos básicos de los discos y los discos duros virtuales para las máquinas virtuales en Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/04/2015"
	ms.author="cynthn"/>

# Acerca de los discos y los discos duros virtuales para máquinas virtuales de Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]



Como cualquier otro equipo, las máquinas virtuales de Azure usan discos como un lugar para almacenar un sistema operativo, aplicaciones y datos. Todas las máquinas virtuales de Azure tienen al menos dos discos: un disco del sistema operativo y un disco temporal. El disco del sistema operativo se crea a partir de una imagen y el disco del sistema operativo y la imagen son discos duros en realidad virtuales (VHD) almacenados en una cuenta de almacenamiento de Azure. Las máquinas virtuales también pueden tener uno o más discos de datos que también se almacenan en discos duros virtuales.

## Disco del sistema operativo

Cada máquina virtual tiene un disco de sistema operativo acoplado. Está registrado como unidad SATA y etiquetado como la unidad C:. Este disco tiene una capacidad máxima de 1023 gigabytes (GB). Cuando Azure crea un disco de sistema operativo, se crean tres copias del disco para obtener una gran durabilidad. Además, si configura la máquina virtual para la replicación geográfica, el disco duro virtual también se replica en diferentes sitios situados a más de 400 millas de distancia.

##Disco temporal

El disco temporal se crea automáticamente. En las máquinas virtuales de Windows, este disco se etiqueta como unidad D:. En las máquinas virtuales de Linux, el disco es normalmente /dev/sdb y es formateado y montado en /mnt/resource por el agente de Linux de Azure.

>[AZURE.WARNING] No almacene datos en el disco temporal. Proporciona almacenamiento temporal para aplicaciones y procesos y está destinado únicamente a almacenar datos como archivos de páginas o de intercambio. Para reasignar este disco para una máquina virtual de Windows, vea [Cambio de la letra de unidad del disco temporal de Windows](virtual-machines-windows-change-drive-letter.md).

## Disco de datos

Un disco de datos es un disco duro virtual que se adjunta a una máquina virtual para almacenar los datos de la aplicación u otros datos que necesita mantener. Los discos de datos se registran como unidades SCSI y se etiquetan con una letra elegida por usted. Cada disco de datos tiene una capacidad máxima de 1023 GB. El tamaño de la máquina virtual determina cuántos discos de datos puede conectar y el tipo de almacenamiento que puede usar para hospedar los discos.

>[AZURE.NOTE] Para obtener más detalles acerca de las capacidades de las máquinas virtuales, consulte [Tamaños de máquinas virtuales](virtual-machines-size-specs.md).

Azure crea un disco del sistema operativo cuando se crea una máquina virtual desde una imagen. Si usa una imagen que incluye discos de datos, Azure también crea los discos de datos al crear la máquina virtual. (Puede usar una imagen de Azure, de un socio o una proporcionada por usted). De lo contrario, agregue discos de datos después de crear la máquina virtual.

Puede agregar discos de datos a una máquina virtual en cualquier momento adjuntando el disco a la máquina virtual. Puede usar un disco duro virtual cargado o copiado por usted en su cuenta de almacenamiento o uno creado por Azure para usted. Al adjuntar un disco de datos se asocia el archivo VHD de la cuenta de almacenamiento a la máquina virtual, colocando una “concesión” en el disco duro virtual para que no pueda eliminarse del almacenamiento mientras está conectado a una máquina virtual.

## Acerca de los discos duros virtuales

Los discos duros virtuales usados en Azure son archivos .vhd almacenados como blobs en páginas en una cuenta de almacenamiento estándar o premium de Azure. (El almacenamiento Premium está disponible en determinadas regiones). Para obtener información detallada sobre blobs en páginas, consulte [Introducción a los blobs en bloques y a los blobs en páginas](https://msdn.microsoft.com/library/ee691964.aspx). Para obtener más información acerca del Almacenamiento premium, consulte [Almacenamiento premium: Almacenamiento de alto rendimiento para cargas de trabajo de máquina virtual de Azure](../storage/storage-premium-storage.md).

Fuera de Azure, los discos duros virtuales pueden utilizar un formato VHD o VHDX. Se pueden fijar, expandir dinámicamente o diferenciar. Azure admite discos fijos, en formato VHD. El formato fijo coloca el disco lógico linealmente dentro del archivo, de manera que el desplazamiento de disco X se almacena en el desplazamiento de blob X. Un pequeño pie de página al final del blob describe las propiedades del VHD. El formato fijo a menudo desaprovecha el espacio porque la mayoría de discos contienen grandes rangos sin utilizar. Sin embargo, Azure almacena los archivos .vhd en un formato disperso; así pues, se beneficia de las ventajas de los discos fijos y dinámicos al mismo tiempo. Para obtener más información, consulte [Introducción a discos duros virtuales](https://technet.microsoft.com/library/dd979539.aspx).

Todos los archivos .vhd de Azure que desea usar como origen para crear discos o imágenes son de solo lectura. Cuando se crea un disco o una imagen, Azure hace copias de los archivos .vhd. Estas copias pueden ser de solo lectura o de lectura y escritura, en función de cómo use el disco duro virtual.

 Cuando se crea una máquina virtual desde una imagen, Azure crea un disco para la máquina virtual que es una copia del archivo .vhd de origen. Para proteger contra la eliminación accidental, Azure coloca una concesión en cada archivo .vhd de origen que se usa para crear una imagen, un disco del sistema operativo o un disco de datos.

Para poder eliminar un archivo .vhd de origen, deberá quitar la concesión eliminando el disco o la imagen. Para eliminar un archivo .vhd que está siendo usado por una máquina virtual como disco del sistema operativo, puede eliminar la máquina virtual, el disco del sistema operativo y el archivo .vhd de origen a la vez, eliminando la máquina virtual y todos los discos asociados. Sin embargo, la eliminación de un archivo .vhd que es un origen de un disco de datos requiere varios pasos en un orden determinado, desasociar el disco de la máquina virtual, eliminar el disco y, a continuación, eliminar el archivo .vhd.

>[AZURE.WARNING] Si elimina un archivo .vhd de origen del almacenamiento o elimina la cuenta de almacenamiento, Microsoft no puede recuperar esos datos por usted.

## Pasos siguientes

Máquinas virtuales Linux

-  [Conecte un disco y prepárelo para su uso](virtual-machines-linux-how-to-attach-disk.md)
-  [Captura de una máquina virtual Linux](virtual-machines-linux-capture-image.md)
-  [Desacople un disco](virtual-machines-linux-how-to-detach-disk.md)

Máquinas virtuales de Windows:

-  [Conecte un disco y prepárelo para su uso](storage-windows-attach-disk.md)
- [Cambio de la letra de unidad del disco temporal de Windows](virtual-machines-windows-change-drive-letter.md)
-  [Captura de una máquina virtual de Windows](virtual-machines-capture-image-windows-server.md)
-  [Desacople un disco](storage-windows-detach-disk.md)

<!---HONumber=AcomDC_0302_2016-->