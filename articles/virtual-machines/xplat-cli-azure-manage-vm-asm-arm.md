<properties
	pageTitle="Comandos de la CLI de Azure equivalentes para las tareas de máquina virtual | Microsoft Azure"
	description="Comandos equivalente de la CLI de Azure para crear y administrar máquinas virtuales de Azure en los modos Administrador de recursos de Azure y Administración de servicios de Azure"
	services="virtual-machines"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="command-line-interface"
	ms.workload="infrastructure-services"
	ms.date="12/14/2015"
	ms.author="danlep"/>


# Comandos equivalentes del Administrador de recursos y de Administración de servicios para las tareas de máquina virtual con la interfaz de la línea de comandos de Azure
Este artículo muestra la interfaz de línea de comandos equivalente de Microsoft Azure (CLI de Azure) para crear y administrar máquinas virtuales de Azure en Administración de servicios de Azure y Administrador de recursos de Azure. Utilícelo como una guía para migrar los scripts de un modo de comando a otro.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]



* Si aún no ha instalado la CLI de Azure y se ha conectado a su suscripción, consulte [Instalación de la CLI de Azure](../xplat-cli-install.md) y [Conexión a una suscripción de Azure desde la CLI de Azure](../xplat-cli-connect.md). Si desea usar los comandos de modo del Administrador de recursos, asegúrese de conectar con el método de inicio de sesión.

* Para empezar a usar el modo del Administrador de recursos en la CLI de Azure, es posible que tenga que cambiar los modos de comando. De forma predeterminada, la CLI se inicia en el modo de administración de servicio. Para cambiar al modo del Administrador de recursos, ejecute `azure config mode arm`. Para volver al modo de Administración de servicios, ejecute `azure config mode asm`.

* Para obtener ayuda en pantalla sobre los conozco y conocer las distintas opciones, escriba `azure <command> <subcommand> --help` o `azure help <command> <subcommand>`.

## Tareas de la máquina virtual
En la tabla siguiente, se comparan tareas comunes de la máquina virtual que se pueden realizar con los comandos de CLI de Azure en Administración de servicios y el Administrador de recursos. En el caso de muchos comandos del Administrador de recursos, es preciso agregar el nombre de un grupo de recursos existente.

> [AZURE.NOTE]En estos ejemplos no se incluyen las operaciones basadas en plantillas que se recomiendan generalmente para implementaciones de máquina virtual en el Administrador de recursos. Para información, vea [Usar la CLI de Azure con el Administrador de recursos de Azure](../xplat-cli-azure-resource-manager.md) e [Implementación y administración de máquinas virtuales con plantillas del Administrador de recursos de Azure y CLI de Azure](virtual-machines-deploy-rmtemplates-azure-cli.md).

Tarea | Administración de servicios | Resource Manager
-------------- | ----------- | -------------------------
Creación de la máquina virtual más básica | `azure vm create [options] <dns-name> <image> [userName] [password]` | `azure vm quick-create [options] <resource-group> <name> <location> <os-type> <image-urn> <admin-username> <admin-password>`<br/><br/>(Obtenga `image-urn` desde el comando `azure vm image list`. Vea [este artículo](resource-groups-vm-searching.md) para ejemplos.)
Creación de una máquina virtual Linux | `azure vm create [options] <dns-name> <image> [userName] [password]` | `azure  vm create [options] <resource-group> <name> <location> -y "Linux"`
Creación de una máquina virtual Windows | `azure vm create [options] <dns-name> <image> [userName] [password]` | `azure  vm create [options] <resource-group> <name> <location> -y "Windows"`
Enumeración de máquinas virtuales | `azure  vm list [options]` | `azure  vm list [options]`
Obtención información acerca de una máquina virtual | `azure  vm show [options] <vm_name>` | `azure  vm show [options] <resource_group> <name>`
Inicio de una máquina virtual | `azure vm start [options] <name>` | `azure vm start [options] <resource_group> <name>`
Detención de una máquina virtual | `azure vm shutdown [options] <name>` | `azure vm stop [options] <resource_group> <name>`
Cancelación de la asignación de una máquina virtual | No disponible | `azure vm deallocate [options] <resource-group> <name>`
Reinicio de una máquina virtual | `azure vm restart [options] <vname>` | `azure vm restart [options] <resource_group> <name>`
Eliminación de una máquina virtual | `azure vm delete [options] <name>` | `azure vm delete [options] <resource_group> <name>`
Captura de una máquina virtual | `azure vm capture [options] <name>` | `azure vm capture [options] <resource_group> <name>`
Creación de una máquina virtual a partir de una imagen del usuario | `azure  vm create [options] <dns-name> <image> [userName] [password]` | `azure  vm create [options] –q <image-name> <resource-group> <name> <location> <os-type>`
Creación de una máquina virtual a partir de un disco especializado | `azure  vm create [options]-d <custom-data-file> <dns-name> [userName] [password]` | `azue  vm create [options] –d <os-disk-vhd> <resource-group> <name> <location> <os-type>`
Incorporación de un disco de datos a una máquina virtual | `azure  vm disk attach [options] <vm-name> <disk-image-name>`, O BIEN <br/> `vm disk attach-new [options] <vm-name> <size-in-gb> [blob-url]` | `azure  vm disk attach-new [options] <resource-group> <vm-name> <size-in-gb> [vhd-name]`
Eliminación de un disco de datos de una máquina virtual | `azure  vm disk detach [options] <vm-name> <lun>` | `azure  vm disk detach [options] <resource-group> <vm-name> <lun>`
Incorporación de una extensión genérica a una máquina virtual | `azure  vm extension set [options] <vm-name> <extension-name> <publisher-name> <version>` | `azure  vm extension set [options] <resource-group> <vm-name> <name> <publisher-name> <version>`
Incorporación de la extensión de acceso a máquinas virtuales a una máquina virtual | No disponible | `azure vm reset-access [options] <resource-group> <name>`
Incorporación de la extensión de Docker a una máquina virtual | `azure  vm docker create [options] <dns-name> <image> <user-name> [password]` | `azure  vm docker create [options] <resource-group> <name> <location> <os-type>`
Incorporación de la extensión de Chef a una máquina virtual | `azure  vm extension get-chef [options] <vm-name>` | No disponible
Deshabilitación de una extensión de máquina virtual | `azure  vm extension set [options] –b <vm-name> <extension-name> <publisher-name> <version>` | No disponible
Eliminación de una extensión de máquina virtual | `azure  vm extension set [options] –u <vm-name> <extension-name> <publisher-name> <version>` | `azure  vm extension set [options] –u <resource-group> <vm-name> <name> <publisher-name> <version>`
Lista de extensiones de máquina virtual | `azure vm extension list [options]` | No disponible
Visualización de una imagen de máquina virtual | `azure vm image show [options]` | No disponible
Obtención del uso de los recursos de una máquina virtual | No disponible | `azure vm list-usage [options] <location>`
Obtención de todos los tamaños disponibles de la máquina virtual | No disponible | `azure vm sizes [options]`


## Pasos siguientes

* Para ejemplos adicionales de los comandos CLI, vea [Uso de la interfaz de la línea de comandos de Azure con la administración de servicios de Azure](virtual-machines-command-line-tools.md) y [Uso de la CLI de Azure con el Administrador de recursos de Azure](azure-cli-arm-commands.md).

<!---HONumber=AcomDC_1223_2015-->