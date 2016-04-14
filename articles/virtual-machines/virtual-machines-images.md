<properties
	pageTitle="Acerca de las imágenes para las máquinas virtuales | Microsoft Azure"
	description="Obtenga información acerca de cómo se usan las imágenes con máquinas virtuales en Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/15/2016"
	ms.author="cynthn"/>

# Acerca de las imágenes para las máquinas virtuales

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo del Administrador de recursos.

Para proporcionar una nueva máquina virtual con un sistema operativo en Azure se usan imágenes. Una imagen también pueden tener uno o más discos de datos. Las imágenes están disponibles desde diferentes fuentes:

-	Azure ofrece imágenes en [Marketplace](https://azure.microsoft.com/gallery/virtual-machines/). Encontrará versiones recientes de Windows Server y distribuciones del sistema operativo Linux. Algunas imágenes también contienen aplicaciones, como SQL Server. Los suscriptores de Pago por uso de MSDN y Ventajas de MSDN tienen acceso a imágenes adicionales.
-	La comunidad de código abierto ofrece imágenes a través de [VM Depot](http://vmdepot.msopentech.com/List/Index).
-	También puede almacenar y usar sus propias imágenes en Azure, bien a través de una captura de una máquina virtual de Azure ya existente para usarla como imagen o a través de la carga de una imagen.

## Acerca de las imágenes de máquina virtual y las de sistema operativo

Pueden usarse dos tipos de imágenes en Azure: *imagen de la máquina virtual* e *imagen de SO*. Una imagen de máquina virtual incluye un sistema operativo y todos los discos conectados a una máquina virtual cuando se crea la imagen. Este es el nuevo tipo de imagen. Antes de la introducción de las imágenes de máquina virtual, una imagen en Azure podía tener sólo un sistema operativo generalizado sin discos adicionales. Una imagen de máquina virtual que contenga sólo un sistema operativo generalizado es básicamente igual que el tipo de imagen original, la imagen SO.

Puede crear sus propias imágenes a partir de una máquina virtual en Azure o una máquina virtual que se ejecute en cualquier sitio desde donde pueda copiar y cargar. Si desea usar una imagen para crear más de una máquina virtual, tendrá que prepararla para usarla como una imagen generalizándola. Para crear una imagen de Windows Server, ejecute el comando Sysprep en el servidor para generalizar antes de cargar el archivo .vhd. Para obtener más información sobre Sysprep, vea [Uso de Sysprep: Introducción](http://go.microsoft.com/fwlink/p/?LinkId=392030). Para crear una imagen de Linux, dependiendo de la distribución de software, necesitará ejecutar un conjunto de comandos que son específicos de la distribución, además de ejecutar el Agente de Linux de Azure.

## Trabajo con imágenes

Puede usar la Interfaz de la línea de comandos de Azure (CLI) para Mac, Linux y Windows o el módulo Azure PowerShell para administrar las imágenes disponibles para su suscripción de Azure. También puede usar el Portal de Azure clásico para algunas tareas de imagen, pero la línea de comandos le ofrece más opciones.

Para obtener información sobre cómo usar estas herramientas con implementaciones del Administrador de recursos, vea [Navegación y selección de imágenes de máquina virtual de Azure con PowerShell y la CLI de Azure](resource-groups-vm-searching.md).

Para obtener ejemplos del uso de las herramientas en una implementación clásica:

- Para la CLI, vea "Comandos para administrar las imágenes de máquina virtual de Azure" en [Uso de la CLI de Azure para Mac, Linux y Windows con el Administrador de recursos de Azure](virtual-machines-command-line-tools.md).
- Para Azure PowerShell, consulte la siguiente lista de comandos. Para obtener un ejemplo sobre cómo encontrar una imagen para crear una máquina virtual, vea "Paso 3: Determinación de ImageFamily" en [Uso de Azure PowerShell para crear y preconfigurar máquinas virtuales basadas en Windows](virtual-machines-ps-create-preconfigure-windows-vms.md).

-	**Obtener todas las imágenes**: `Get-AzureVMImage`devuelve una lista de todas las imágenes disponibles en su suscripción actual: las imágenes, así como las proporcionadas por Azure o socios. Esto significa que podría obtener una lista de gran tamaño. Los ejemplos siguientes muestra cómo obtener una lista más corta.
-	**Obtener familias de imagen**: `Get-AzureVMImage | select ImageFamily` obtiene una lista de familias de imágenes mostrando cadenas la propiedad **ImageFamily**.
-	**Obtener todas las imágenes en una familia concreta**: `Get-AzureVMImage | Where-Object {$_.ImageFamily -eq $family}`
-	**Buscar imágenes de máquina virtual**: `Get-AzureVMImage | where {(gm –InputObject $_ -Name DataDiskConfigurations) -ne $null} | Select -Property Label, ImageName` esto funciona mediante el filtrado de la propiedad DataDiskConfiguration, que solo se aplica a imágenes de máquina virtual. Este ejemplo también filtra la salida a sólo el nombre de la imagen y la etiqueta.
-	**Guardar una imagen generalizada**: `Save-AzureVMImage –ServiceName "myServiceName" –Name "MyVMtoCapture" –OSState "Generalized" –ImageName "MyVmImage" –ImageLabel "This is my generalized image"`
-	**Guardar una imagen especializada**: `Save-AzureVMImage –ServiceName "mySvc2" –Name "MyVMToCapture2" –ImageName "myFirstVMImageSP" –OSState "Specialized" -Verbose`
>[Azure.Tip] El parámetro OSState es necesario si desea crear una imagen de máquina virtual que incluya los discos de datos, además del disco del sistema operativo. Si no usa el parámetro, el cmdlet crea una imagen de sistema operativo. El valor del parámetro indica si la imagen es generalizada o especializada, en función de si se ha preparado el disco del sistema operativo para su reutilización.
-	**Eliminar una imagen**: `Remove-AzureVMImage –ImageName "MyOldVmImage"`


## Recursos adicionales

[Diferentes formas de crear una máquina virtual Linux](virtual-machines-linux-choices-create-vm.md)

[Diferentes formas de crear una máquina virtual de Windows](virtual-machines-windows-choices-create-vm.md)

<!---HONumber=AcomDC_0128_2016-->