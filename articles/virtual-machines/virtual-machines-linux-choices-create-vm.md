<properties
	pageTitle="Diferentes formas de crear una máquina virtual Linux | Microsoft Azure"
	description="Enumera las distintas formas de crear una máquina virtual Linux en Azure y proporciona vínculos a instrucciones adicionales."
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""
	tags="azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure-services"
	ms.date="01/20/2016"
	ms.author="dkshir"/>

# Diferentes formas de crear una máquina virtual Linux

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Azure ofrece varias formas de crear una VM que se adapte a distintos usuarios y objetivos. En este artículo se resumen estas diferencias y las opciones que tiene para crear las máquinas virtuales de Linux.

También se pueden usar las plantillas del Administrador de recursos de Azure para crear y administrar una máquina virtual y sus distintos recursos como una sola unidad de implementación lógica. Las instrucciones para este enfoque se incluyen en los casos en que sea posible. Para obtener más información acerca del Administrador de recursos de Azure y cómo administrar los recursos como una sola unidad, consulte la [Información general][].

## Opciones de herramienta

### GUI: Portal de Azure clásico o Portal de Azure

La interfaz gráfica de usuario del portal es una manera fácil de probar una máquina virtual, especialmente si no tiene experiencia con Azure. Use el [Portal de Azure clásico](https://manage.windowsazure.com) o el [Portal de Azure](https://portal.azure.com) para crear la máquina virtual. Para obtener instrucciones generales, vea [Creación de una máquina virtual personalizada][] y seleccione cualquier imagen de Linux en la **Galería**. Tenga en cuenta que el [Portal de Azure clásico](https://manage.windowsazure.com) solo crea máquinas virtuales con el modelo de implementación clásica.

### Shell de comandos: CLI de Azure o Azure PowerShell

Si prefiere trabajar en un shell de comandos, elija entre la interfaz de la línea de comandos (CLI) de Azure para usuarios de Mac, Linux y Windows o Azure PowerShell, que tiene los cmdlets de Windows PowerShell para Azure y una consola personalizada.

Para crear máquinas virtuales en el modelo de implementación del Administrador de recursos mediante la CLI de Azure, consulte [Creación de una máquina virtual con Linux][]. Siga las fichas o selectores de artículos de esta página para leer las instrucciones de uso de Azure PowerShell y las plantillas.

Para el modelo de implementación clásico, consulte [Creación de una máquina virtual con Linux personalizada con Azure CLI](virtual-machines-linux-create-custom.md) y [Creación y preconfiguración de una máquina virtual Linux con Azure Powershell][]


### Entorno de desarrollo: Visual Studio

Visual Studio también admite la creación de máquinas virtuales de Azure. Para el modelo de implementación clásico, consulte [Creación de una máquina virtual para una aplicación web con Visual Studio][] Para el modelo de implementación del Administrador de recursos, consulte [Implementación de recursos de Azure mediante bibliotecas .NET de proceso, red y almacenamiento][]


## Sistema operativo y opciones de imagen

Elija una imagen basada en el sistema operativo que desea ejecutar. Azure y sus socios ofrecen muchas imágenes, algunas de los cuales incluyen aplicaciones y herramientas. O bien, use una de sus propias imágenes.


### Imágenes de Azure

En todos los artículos anteriores, se puede usar fácilmente una imagen de Azure existente para crear una máquina virtual y personalizarla para la red, el equilibrio de carga y mucho más. Los portales ofrecen una galería o lista de imágenes proporcionadas por Azure. Puede obtener listas similares usando la línea de comandos. Por ejemplo, en la CLI de Azure, ejecute `azure vm image list` para obtener una lista de todas las imágenes disponibles, por ubicación y publicador.


### Uso de su propia imagen

Use una imagen basada en una máquina virtual de Azure existente *capturando* esa VM o cargue una imagen suya, almacenada en un disco duro virtual (VHD). Para el modelo de implementación clásico, consulte [Captura de una máquina virtual clásica con Linux para usar como plantilla con la CLI][] y [Creación y carga de un disco duro virtual que contiene el sistema operativo Linux][]. Para el modelo de implementación del Administrador de recursos, consulte [Capturar una máquina virtual de Linux para usarla como plantilla del Administrador de recursos](virtual-machines-linux-capture-image-resource-manager.md).

## Pasos siguientes

[Inicio de sesión en la máquina virtual][]

[Acoplamiento de un disco de datos][]

## Recursos adicionales

[Entorno de prueba de la configuración base][]

[Entornos de prueba de nube híbrida de Azure][]

[Comandos equivalentes del Administrador de recursos y de Administración de servicios para las operaciones de máquina virtual con la CLI de Azure para Mac, Linux y Windows][]

<!-- LINKS -->
[Información general]: ../resource-group-overview.md

[Create a Virtual Machine Running Windows]: virtual-machines-windows-tutorial.md
[Create a Virtual Machine Running Linux]: virtual-machines-linux-tutorial.md

[Comandos equivalentes del Administrador de recursos y de Administración de servicios para las operaciones de máquina virtual con la CLI de Azure para Mac, Linux y Windows]: xplat-cli-azure-manage-vm-asm-arm.md
[Deploy and Manage Virtual Machines using Azure Resource Manager Templates and the Azure CLI]: virtual-machines-deploy-rmtemplates-azure-cli.md
[Deploy and Manage Virtual Machines using Azure Resource Manager Templates and PowerShell]: virtual-machines-deploy-rmtemplates-powershell.md
[Creación y preconfiguración de una máquina virtual Linux con Azure Powershell]: virtual-machines-ps-create-preconfigure-linux-vms.md

[How to Create a Custom Virtual Machine Running Linux in Azure]: virtual-machines-linux-create-custom.md
[Captura de una máquina virtual clásica con Linux para usar como plantilla con la CLI]: virtual-machines-linux-capture-image.md

[Creación y carga de un disco duro virtual que contiene el sistema operativo Linux]: virtual-machines-linux-create-upload-vhd.md

[Creación de una máquina virtual para una aplicación web con Visual Studio]: virtual-machines-dotnet-create-visual-studio-powershell.md
[Implementación de recursos de Azure mediante bibliotecas .NET de proceso, red y almacenamiento]: virtual-machines-arm-deployment.md

[Inicio de sesión en la máquina virtual]: virtual-machines-linux-how-to-log-on.md

[Acoplamiento de un disco de datos]: virtual-machines-linux-how-to-attach-disk.md

[Entorno de prueba de la configuración base]: virtual-machines-base-configuration-test-environment.md
[Entornos de prueba de nube híbrida de Azure]: virtual-machines-hybrid-cloud-test-environments.md

[Creación de una máquina virtual con Linux]: virtual-machines-linux-tutorial.md
[Creación de una máquina virtual personalizada]: virtual-machines-create-custom.md

<!---HONumber=AcomDC_0128_2016-->