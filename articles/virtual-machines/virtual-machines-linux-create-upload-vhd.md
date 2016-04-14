<properties
	pageTitle="Creación y carga de un VHD de Linux | Microsoft Azure"
	description="Cree y cargue un disco duro virtual de Azure (VHD) con el modelo de implementación clásica que contenga el sistema operativo Linux."
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
	ms.date="01/22/2016"
	ms.author="dkshir"/>

# Creación y carga de un disco duro virtual que contiene el sistema operativo Linux

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo del Administrador de recursos.


En este artículo se muestra cómo puede crear y cargar un disco duro virtual (VHD) que podrá utilizar como imagen propia para crear máquinas virtuales en Azure. Aprenderá a preparar el sistema operativo que podrá utilizar para crear máquinas virtuales basadas en esa imagen.

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

Una máquina virtual de Azure ejecuta el sistema operativo basado en la imagen que elija durante la creación. Estas imágenes se almacenan en formato VHD en archivos .vhd en una cuenta de almacenamiento. Para obtener más detalles, consulte [Discos en Azure](virtual-machines-disks-vhds.md) e [Imágenes en Azure](virtual-machines-images.md).

Al crear la máquina virtual, puede personalizar parte de la configuración del sistema operativo para adaptarla a la aplicación que desea ejecutar. Para obtener instrucciones, consulte [Creación de una máquina virtual personalizada](virtual-machines-create-custom.md).

**Importante**: el contrato de nivel de servicio de la plataforma Azure se aplica a las máquinas virtuales que ejecutan el sistema operativo Linux solo cuando una de las distribuciones aprobadas se use con los detalles de la configuración según se indica en la sección “Versiones admitidas” en [Linux en distribuciones aprobadas por Azure](virtual-machines-linux-endorsed-distributions.md). Todas las distribuciones de Linux en la galería de imágenes de Azure son distribuciones aprobadas con la configuración requerida.


## Requisitos previos
En este artículo se supone que tiene los siguientes elementos:

- **Un certificado de administración**: ha creado un certificado de administración para la suscripción para la que desea cargar un VHD, y ha exportado el certificado a un archivo .cer. Para más información sobre cómo crear certificados de Azure, consulte [Información general sobre los certificados de Azure](../cloud-services/cloud-services-certs-create.md).

- **Sistema operativo Linux instalado en un archivo .vhd**: ha instalado un sistema operativo Linux compatible en un disco duro virtual. Existen varias herramientas para crear archivos .vhd; por ejemplo, puede utilizar una solución de virtualización como Hyper-V para crear el archivo .vhd e instalar el sistema operativo. Para obtener instrucciones, consulte [Instalación del rol de Hyper-V y configuración de una máquina Virtual](http://technet.microsoft.com/library/hh846766.aspx).

	**Importante**: el reciente formato VHDX no se admite en Azure. Puede convertir el disco al formato VHD con el Administrador de Hyper-V o el cmdlet Convert-VHD.

	Para ver una lista de las distribuciones aprobadas, consulte [Linux en distribuciones aprobadas por Azure](virtual-machines-linux-endorsed-distributions.md). Para obtener una lista general de las distribuciones de Linux, vea [Información sobre las distribuciones no aprobadas](virtual-machines-linux-create-upload-vhd-generic.md).

- **Interfaz de línea de comandos de Azure**: si usa el sistema operativo Linux para crear su imagen, usará la [Interfaz de comandos de Azure](virtual-machines-command-line-tools.md) para cargar el VHD.

- **Herramientas de Azure PowerShell**: el cmdlet `Add-AzureVhd` se puede usar también para cargar el VHD. Consulte [Descargas de Azure](https://azure.microsoft.com/downloads/) para descargar los cmdlets de Azure Powershell. Para obtener información de referencia, consulte [Add-AzureVhd](https://msdn.microsoft.com/library/azure/dn495173.aspx).

<a id="prepimage"> </a>
## Paso 1: Preparación de la imagen que se va a cargar

Azure admite varias distribuciones de Linux (consulte [Distribuciones aprobadas](virtual-machines-linux-endorsed-distributions.md)). Los artículos siguientes le guiarán a través del proceso de preparación de las distintas distribuciones de Linux admitidas en Azure:

- **[Distribuciones basadas en CentOS](virtual-machines-linux-create-upload-vhd-centos.md)**
- **[Debian Linux](virtual-machines-linux-create-upload-vhd-debian.md)**
- **[Oracle Linux](virtual-machines-linux-create-upload-vhd-oracle.md)**
- **[Red Hat Enterprise Linux](virtual-machines-linux-create-upload-vhd-redhat.md)**
- **[SLES y openSUSE](../virtual-machines-linux-create-upload-vhd-suse)**
- **[Ubuntu](virtual-machines-linux-create-upload-vhd-ubuntu.md)**
- **[Otras distribuciones no aprobadas](virtual-machines-linux-create-upload-vhd-generic.md)**

Consulte también las **[notas de instalación de Linux](virtual-machines-linux-create-upload-vhd-generic.md#linuxinstall)** para obtener más sugerencias sobre la preparación de imágenes de Linux para Azure.

Tras seguir los pasos de las guías enumeradas anteriormente, debería contar con un archivo VHD listo para cargarse en Azure.

<a id="connect"> </a>
## Paso 2: preparación de la conexión a Azure

Antes de cargar el archivo .vhd, debe establecer una conexión segura entre el equipo y la suscripción de Azure.


### Si usa la CLI de Azure

Los valores más recientes de la CLI de Azure pasan de forma predeterminada al modelo de implementación del Administrador de recursos, así que asegúrese de que esté en el modelo de implementación clásica mediante este comando:

		azure config mode asm  

Luego, use uno de los siguientes métodos de inicio de sesión para conectarse a su suscripción de Azure.

Use el método de Azure AD para iniciar sesión:

1. Abra una ventana de la CLI de Azure

2. Escriba:

	`azure login`

	Cuando se le pida, escriba su nombre de usuario y su contraseña.

**O bien**, use en su lugar un archivo PublishSettings:

1. Abra una ventana de la CLI de Azure

2. Escriba:

	`azure account download`

	Este comando abre una ventana de explorador y descarga automáticamente un archivo .publishsettings que contiene información y un certificado para su suscripción de Azure.

3. Guarde el archivo .publishsettings

4. Escriba:

	`azure account import <PathToFile>`

	Donde `<PathToFile>` es la ruta completa al archivo .publishsettings.

	Para obtener más información, consulte [Conectar a Azure desde la interfaz de la línea de comandos de Azure (CLI de Azure)](../xplat-cli-connect.md).


### Si usa Azure PowerShell

Use el método de Azure AD para iniciar sesión:

1. Abra una ventana de Azure PowerShell.

2. Escriba:

	`Add-AzureAccount`

	Cuando se le solicite, escriba su Id. de usuario de organización y la contraseña.

**O**, use los archivos PublishSettings en su lugar:

1. Abra una ventana de Azure PowerShell.

2. Escriba:

	`Get-AzurePublishSettingsFile`

	Este comando abre una ventana de explorador y descarga automáticamente un archivo .publishsettings que contiene información y un certificado para su suscripción de Azure.

3. Guarde el archivo .publishsettings.

4. Escriba:

	`Import-AzurePublishSettingsFile <PathToFile>`

	Donde `<PathToFile>` es la ruta completa al archivo .publishsettings.

	Para obtener más información, consulte [Instalación y configuración de Azure PowerShell](powershell-install-configure.md)

> [AZURE.NOTE] Se recomienda que use el método más reciente de Azure Active Directory para iniciar sesión en su suscripción de Azure desde la CLI de Azure o Azure PowerShell.

<a id="upload"> </a>
## Paso 3: cargar la imagen en Azure

Tendrá que cargar el archivo VHD a una cuenta de almacenamiento. Puede elegir una existente o crear una nueva. Para crear una cuenta de almacenamiento, vea [Creación de una cuenta de almacenamiento](../storage/storage-create-storage-account.md).

Cuando carga el archivo .vhd, puede colocarlo en cualquier parte del almacenamiento de blobs. En los siguientes ejemplos de comandos, **BlobStorageURL** es la URL de la cuenta de almacenamiento que planea usar, **YourImagesFolder** es el contenedor dentro del almacenamiento de blobs donde desea almacenar las imágenes. **VHDName** es la etiqueta que aparece tanto en el [Portal de Azure](http://portal.azure.com) como en el [Portal de Azure clásico](http://manage.windowsazure.com) para identificar el disco duro virtual. **PathToVHDFile** es la ruta de acceso completa y el nombre del archivo .vhd de su máquina.


### Si usa la CLI de Azure

Use la CLI de Azure para cargar la imagen con el siguiente comando:

		azure vm image create <ImageName> --blob-url <BlobStorageURL>/<YourImagesFolder>/<VHDName> --os Linux <PathToVHDFile>

Para obtener más información, consulte [Referencia de la CLI de Azure para el Administrador de servicios de Azure](virtual-machines-command-line-tools.md).


### Si usa PowerShell

Desde la ventana de Azure PowerShell que ha usado en el paso anterior, escriba:

		Add-AzureVhd -Destination <BlobStorageURL>/<YourImagesFolder>/<VHDName> -LocalFilePath <PathToVHDFile>

Para obtener más información, consulte [Add-AzureVhd](https://msdn.microsoft.com/library/azure/dn495173.aspx).


[Step 1: Prepare the image to be uploaded]: #prepimage
[Step 2: Prepare the connection to Azure]: #connect
[Step 3: Upload the image to Azure]: #upload

<!---HONumber=AcomDC_0204_2016-->