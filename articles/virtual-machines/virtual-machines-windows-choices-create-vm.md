<properties
	pageTitle="Diferentes formas de crear una máquina virtual Windows | Microsoft Azure"
	description="Enumera las diferentes formas de crear una máquina virtual de Windows con el Administrador de recursos."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="index-page"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="infrastructure-services"
	ms.date="10/22/2015"
	ms.author="cynthn"/>

# Diferentes formas de crear una máquina virtual de Windows con el Administrador de recursos

Azure ofrece varias formas de crear una máquina virtual porque las máquinas virtuales son adecuadas para distintos usuarios y objetivos. Esto significa que tendrá tomar algunas decisiones sobre la máquina virtual y cómo crearla. Este artículo ofrece un resumen de estas opciones y vínculos a instrucciones.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementación clásica.

Recientemente se han incluido plantillas del Administrador de recursos de Azure como una manera de crear y administrar una máquina virtual y sus distintos recursos como una sola unidad de implementación lógica. Las instrucciones de este enfoque se incluyen a continuación, cuando sea posible. Para obtener más información sobre el Administrador de recursos de Azure y cómo administrar los recursos como una sola unidad, vea la [Información general][].

## Opciones de herramienta

### GUI: Portal de Azure

La interfaz gráfica de usuario del Portal de Azure clásico es una manera fácil de probar una máquina virtual, especialmente si no tiene experiencia con Azure. Use el Portal de Azure para crear la máquina virtual:

[Creación de una máquina virtual que ejecuta Windows][]

### Shell de comandos: CLI de Azure o Azure PowerShell

Si prefiere trabajar en un shell de comandos, elija entre la interfaz de la línea de comandos (CLI) de Azure para usuarios de Mac y Linux o Azure PowerShell, que tiene los cmdlets de Azure y una consola personalizada.

Para la CLI de Azure, consulte:

- [Comandos equivalentes del Administrador de recursos y de Administración de servicios para las operaciones de máquina virtual con la CLI de Azure para Mac, Linux y Windows][]
- [Implementación y administración de máquinas virtuales con plantillas del Administrador de recursos de Azure y CLI de Azure][]

Para Azure PowerShell, consulte:

- [Crear una máquina virtual de Windows con el Administrador de recursos y PowerShell][]
- [Creación de una máquina virtual de Windows con el Administrador de recursos de Azure y PowerShell][]
- [Implementación y administración de máquinas virtuales con plantillas del Administrador de recursos de Azure y PowerShell][]
- [Creación de una máquina virtual Windows con una plantilla del Administrador de recursos y PowerShell][]

### Entorno de desarrollo: Visual Studio

[Implementación de recursos de Azure mediante bibliotecas .NET de proceso, red y almacenamiento][]

## Sistema operativo y opciones de imagen

Elija una imagen basada en el sistema operativo que desea ejecutar. Azure y sus socios ofrecen muchas imágenes, algunas de los cuales incluyen aplicaciones y herramientas. O bien, use una de sus propias imágenes. Busque la imagen de la plataforma que necesite para su aplicación con la información de: [Seleccione y navegue por imágenes de máquina virtual de Azure con PowerShell y la CLI de Azure][].

<!-- LINKS -->
[Información general]: ../resource-group-overview.md

[Creación de una máquina virtual que ejecuta Windows]: virtual-machines-windows-tutorial.md

[Comandos equivalentes del Administrador de recursos y de Administración de servicios para las operaciones de máquina virtual con la CLI de Azure para Mac, Linux y Windows]: xplat-cli-azure-manage-vm-asm-arm.md
[Implementación y administración de máquinas virtuales con plantillas del Administrador de recursos de Azure y CLI de Azure]: virtual-machines-deploy-rmtemplates-azure-cli.md
[Creación de una máquina virtual de Windows con el Administrador de recursos de Azure y PowerShell]: virtual-machines-ps-create-preconfigure-windows-resource-manager-vms.md
[Implementación y administración de máquinas virtuales con plantillas del Administrador de recursos de Azure y PowerShell]: virtual-machines-deploy-rmtemplates-powershell.md
[Crear una máquina virtual de Windows con el Administrador de recursos y PowerShell]: virtual-machines-create-windows-powershell-resource-manager.md
[Creación de una máquina virtual Windows con una plantilla del Administrador de recursos y PowerShell]: virtual-machines-create-windows-powershell-resource-manager-template.md


[Seleccione y navegue por imágenes de máquina virtual de Azure con PowerShell y la CLI de Azure]: resource-groups-vm-searching.md
[Implementación de recursos de Azure mediante bibliotecas .NET de proceso, red y almacenamiento]: virtual-machines-arm-deployment.md

[Sign in to the virtual machine]: virtual-machines-log-on-windows-server.md

[Base configuration test environment]: virtual-machines-base-configuration-test-environment.md

[Azure hybrid cloud test environments]: virtual-machines-hybrid-cloud-test-environments.md

<!---HONumber=AcomDC_0204_2016-->