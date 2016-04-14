<properties
	pageTitle="Extensión de máquina virtual Docker para Linux en Azure"
	description="Describe Docker y los contenedores, las extensiones de máquinas virtuales de Azure y apunta a más recursos para crear contenedores de Docker tanto desde la CLI de Azure como desde el portal."
	services="virtual-machines"
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure-services"
	ms.date="10/21/2015"
	ms.author="rasquill"/>

# Extensión de máquina virtual Docker para Linux en Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

[Docker](https://www.docker.com/) es uno de los enfoques de virtualización más populares que usan los [contenedores de Linux](http://en.wikipedia.org/wiki/LXC), en lugar de máquinas virtuales, como una forma de aislar datos de aplicación y calcular recursos compartidos. Puede usar la [extensión de VM Docker](https://github.com/Azure/azure-docker-extension/blob/master/README.md) del [agente Linux de Azure](virtual-machines-linux-agent-user-guide.md) para crear una máquina virtual de Docker que hospede cualquier número de contenedores para sus aplicaciones de Azure.

En este tema se describe:

+ [Contenedores Docker y Linux]
+ [Uso de la extensión de VM Docker con Azure]
+ [Extensiones de máquina virtual para Linux y Windows]

Para crear máquinas virtuales con funcionalidad Docker ahora, consulte:

+ [Uso de la extensión de la máquina virtual de Docker desde la interfaz de la línea de comandos de Azure (CLI de Azure)]
+ [Uso de la extensión de máquina virtual de Docker con el Portal de Azure clásico]
+ [Cómo empezar a trabajar rápidamente con Docker en Azure Marketplace]

Para obtener más información acerca de la extensión y su funcionamiento, consulte la [Guía de usuario de la extensión de Docker](https://github.com/Azure/azure-docker-extension/blob/master/README.md).

## Contenedores Docker y Linux
[Docker](https://www.docker.com/) es uno de los enfoques de virtualización más populares que usan [contenedores de Linux](http://en.wikipedia.org/wiki/LXC) en lugar de máquinas virtuales como una forma de aislar datos y calcular recursos compartidos y proporciona otros servicios que le permiten generar o ensamblar aplicaciones rápidamente y distribuirlas entre otros contenedores Docker.

Los contenedores Docker y Linux no son [hipervisores](http://en.wikipedia.org/wiki/Hypervisor) como Windows Hyper-V y [KVM](http://www.linux-kvm.org/page/Main_Page) en Linux (existen muchos otros ejemplos). Los hipervisores virtualizan el sistema operativo subyacente para permitir a los sistemas operativos completos (llamados *máquinas virtuales*) ejecutarse dentro del hipervisor como si fuesen una aplicación.

Los enfoques de Docker y otros *contenedores* han reducido radicalmente tanto el tiempo de inicio consumido como la sobrecarga del procesamiento y almacenamiento necesaria usando características de aislamiento de procesos y archivos del kernel de Linux para exponer únicamente características de kernel en un contenedor aislado de otro modo.

En la siguiente tabla se describe a muy alto nivel el tipo de diferencias de características que existen entre hipervisores y contenedores de Linux. Tenga en cuenta que algunas características pueden ser más o menos deseables en función de sus propias necesidades de aplicación.

| Característica | Hipervisores | Contenedores |
| :------------- |-------------| ----------- |
| Aislamiento de procesos | Más o menos completo | Si se obtiene la raíz, el host del contenedor podría ponerse en peligro |
| Memoria en disco necesaria | Sistema operativo más aplicaciones completos | Solo requisitos de aplicación |
| Tiempo que se tarda en iniciar | Significativamente más largo: el arranque del SO más la carga de la aplicación | Significativamente más corto: solamente es necesario que se inicien las aplicaciones porque el kernel ya está en ejecución |
| Automatización de contenedores | Varía considerablemente dependiendo del sistema operativo y las aplicaciones | [Galería de imágenes de Docker](https://registry.hub.docker.com/); otros

Para ver un análisis de alto nivel de los contenedores y sus ventajas, consulte [Documento de alto nivel de Docker](http://channel9.msdn.com/Blogs/Regular-IT-Guy/Docker-High-Level-Whiteboard).

Para obtener más información sobre qué es Docker y cómo funciona en realidad, consulte [¿Qué es Docker?](https://www.docker.com/whatisdocker/)

#### Práctica recomendadas de seguridad para contenedores de Docker y Linux

Debido a que los contenedores comparten acceso al kernel del equipo host, si hay código malintencionado capaz de obtener la raíz es posible que también pueda obtener acceso no solo al equipo host si no también a los otros contenedores. Para proteger su sistema de contenedores con más garantías de las que ofrece la configuración predeterminada, [Docker recomienda](https://docs.docker.com/articles/security/) el uso de directivas de grupo o de [seguridad basada en roles](http://en.wikipedia.org/wiki/Role-based_access_control) adicionales, como [SELinux](http://selinuxproject.org/page/Main_Page) o [AppArmor](http://wiki.apparmor.net/index.php/Main_Page), por ejemplo, además de reducir todo lo posible las capacidades de kernel que se otorgan al contenedor. Además, hay muchos otros documentos en Internet que describen enfoques de seguridad mediante el uso de contenedores como Docker.

## Uso de la extensión de VM Docker con Azure

La extensión de VM Docker es un componente que se instala en la instancia de máquina virtual creada y que instala automáticamente el motor de Docker y administra la comunicación remota con la máquina virtual. Hay dos formas de instalar la extensión de VM: puede crear su máquina virtual con el portal de administración o crearla desde la interfaz de línea de comandos de Azure (CLI de Azure).

Puede usar el portal para agregar la extensión de VM Docker a cualquier máquina virtual de Linux compatible (actualmente, la única imagen que lo admite es la imagen de Ubuntu 14.04 LTS posterior a julio). No obstante, con la línea de comandos de la CLI de Azure puede instalar la extensión de VM Docker y crear y cargar los certificados de comunicación de Docker simultáneamente cuando cree la instancia de máquina virtual.

Para crear máquinas virtuales con funcionalidad Docker ahora, consulte:

+ [Uso de la extensión de la máquina virtual de Docker desde la interfaz de la línea de comandos de Azure (CLI de Azure)]
+ [Uso de la extensión de máquina virtual de Docker con el Portal de Azure clásico]

## Extensiones de máquina virtual para Linux y Windows
La [extensión de máquina virtual Docker para Azure](https://github.com/Azure/azure-docker-extension/blob/master/README.md) es solo una de las muchas extensiones de máquina virtual que ofrecen un comportamiento especial, y hay aún más en desarrollo. Por ejemplo, varias de las características de la [extensión del agente de máquina virtual de Linux](virtual-machines-linux-agent-user-guide.md) le permiten modificar y administrar la máquina virtual, incluidas las características de seguridad, las características de kernel y de red, etc. Por ejemplo la extensión VMAccess permite restablecer la contraseña del administrador o la clave SSH.

Para obtener una lista completa, consulte [Extensiones de VM de Azure](virtual-machines-extensions-features.md).

<!--Anchors-->
[Uso de la extensión de la máquina virtual de Docker desde la interfaz de la línea de comandos de Azure (CLI de Azure)]: http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-xplat-cli/
[Uso de la extensión de máquina virtual de Docker con el Portal de Azure clásico]: http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-portal/
[Cómo empezar a trabajar rápidamente con Docker en Azure Marketplace]: http://azure.microsoft.com/documentation/articles/virtual-machines-docker-ubuntu-quickstart/
[Contenedores Docker y Linux]: #Docker-and-Linux-Containers
[Uso de la extensión de VM Docker con Azure]: #How-to-use-the-Docker-VM-Extension-with-Azure
[Extensiones de máquina virtual para Linux y Windows]: #Virtual-Machine-Extensions-For-Linux-and-Windows

<!---HONumber=AcomDC_1217_2015-->