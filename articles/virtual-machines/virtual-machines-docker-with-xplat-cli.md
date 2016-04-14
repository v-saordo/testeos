<properties
	pageTitle="Uso de la extensión de VM Docker para Linux en Azure"
	description="Describe Docker y las extensiones de máquinas virtuales de Azure y muestra cómo crear máquinas virtuales en Azure mediante programación que son host de Docker desde la línea de comandos usando la CLI de Azure."
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
	ms.date="01/04/2016"
	ms.author="rasquill"/>

# Uso de la extensión de la máquina virtual de Docker desde la interfaz de la línea de comandos de Azure (CLI de Azure)

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo del Administrador de recursos.



En este tema se describe cómo crear una máquina virtual con la extensión de VM Docker desde el modo de administración de servicio (asm) en la CLI de Azure en cualquier plataforma. [Docker](https://www.docker.com/) es uno de los enfoques de virtualización más populares que utilizan los [contenedores de Linux](http://en.wikipedia.org/wiki/LXC) en lugar de máquinas virtuales como una forma de aislar datos y calcular recursos compartidos. Puede usar la extensión de VM Docker para que el [agente Linux de Azure](virtual-machines-linux-agent-user-guide.md) cree una VM Docker que hospede cualquier número de contenedores para sus aplicaciones de Azure. Para ver un análisis de alto nivel de los contenedores y sus ventajas, consulte [Documento de alto nivel de Docker](http://channel9.msdn.com/Blogs/Regular-IT-Guy/Docker-High-Level-Whiteboard).


##Uso de la extensión de VM Docker con Azure
Para utilizar la extensión de VM Docker con Azure, debe instalar una versión de la [línea de comandos de Azure](https://github.com/Azure/azure-sdk-tools-xplat) (CLI de Azure) superior a la 0.8.6 (en el momento de redactar este documento la versión actual es la 0.8.10). Puede instalar CLI de Azure en Mac, Linux y Windows.


El proceso completo para usar Docker en Azure es simple:

+ Instale la CLI de Azure y sus dependencias en el equipo desde el que desee controlar Azure (en Windows, será una distribución de Linux que se ejecuta como una máquina virtual)
+ Utilice los comandos de Docker de la CLI de Azure para crear un host de Docker de VM en Azure
+ Utilice los comandos Docker locales para gestionar sus contenedores de Docker en su VM Docker en Azure.


### Instalación de la interfaz de la línea de comandos de Azure (CLI de Azure)

Para instalar y configurar la CLI de Azure, consulte [Instalación y configuración de la interfaz de la línea de comandos de Azure](../xplat-cli-install.md). Para confirmar la instalación, escriba `azure` en el símbolo del sistema y, tras un breve instante, debería ver el arte ASCII de la CLI de Azure, que enumera los comandos básicos que tiene a su disposición. Si la instalación ha funcionado correctamente, debería poder escribir `azure help vm` y ver que uno de los comandos que aparecen en la lista es "docker".

> [AZURE.NOTE]Docker tiene un programa de instalación de Windows, [Boot2Docker](https://docs.docker.com/installation/windows/), que también se puede utilizar para automatizar la creación de un cliente de docker que puede usar para trabajar con máquinas virtuales de Azure como hosts de docker.

### Conexión de la CLI de Azure a su cuenta de Azure
Antes de poder utilizar la CLI de Azure, debe asociar las credenciales de su cuenta de Azure con la CLI de Azure en su plataforma. La sección[ Conexión con su suscripción de Azure](../xplat-cli-connect.md) explica cómo descargar e importar su archivo **.publishsettings** o asociar su CLI de Azure con un identificador de organización.

> [AZURE.NOTE]Existen algunas diferencias de comportamiento al utilizar uno u otro método de autenticación, por eso debe asegurarse de leer el documento anterior para comprender las diferentes funcionalidades.

### Instalación de Docker y uso de la extensión de VM Docker para Azure
Siga las[ instrucciones de instalación de Docker](https://docs.docker.com/installation/#installation) para instalar Docker de forma local en su equipo.

Para usar Docker con una máquina virtual de Azure, la imagen de Linux usada para la máquina virtual debe tener el [agente de máquina virtual de Linux de Azure](virtual-machines-linux-agent-user-guide.md) instalado. Actualmente, solamente hay dos tipos de imágenes que proporcionan esto:

+ Una imagen Ubuntu de la galería de imágenes de Azure o

+ Una imagen de Linux personalizada que ha creado con el agente de máquina virtual de Linux de Azure instalado y configurado. Consulte [Agente de máquina virtual de Linux de Azure](virtual-machines-linux-agent-user-guide.md) para obtener más información sobre cómo crear una máquina virtual de Linux personalizada con el agente de máquina virtual de Azure.

### Uso de la galería de imágenes de Azure

Desde una sesión de Bash o Terminal, use el siguiente comando de la CLI de Azure para localizar la imagen de Ubuntu más reciente en la galería de máquina virtual que se usará:

`azure vm image list | grep Ubuntu-14_04`

y seleccione uno de los nombres de imagen, como `b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04-LTS-amd64-server-20140724-es-ES-30GB`; por último, use el siguiente comando para crear una nueva máquina virtual usando esa imagen.

```
azure vm docker create -e 22 -l "West US" <vm-cloudservice name> "b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04-LTS-amd64-server-20140724-es-ES-30GB" <username> <password>
```

donde:

+ *&lt;vm-cloudservice name&gt;* es el nombre de la máquina virtual que se convertirá en el equipo host del contenedor de Docker en Azure.

+  *&lt;username&gt;* es el nombre de usuario del usuario raíz predeterminado de la máquina virtual.

+ *&lt;password&gt;* es la contraseña de la cuenta de *username* que cumple los estándares de complejidad de Azure.

> [AZURE.NOTE]Actualmente, una contraseña debe tener al menos 8 caracteres, contener un carácter en minúscula y otro en mayúscula, un número y un carácter especial como uno de los siguientes: `!@#$%^&+=`. No, el punto al final de la frase anterior NO es un carácter especial.

Si el comando es correcto, debería ver algo como lo siguiente, dependiendo de los argumentos y opciones precisas que haya utilizado:

![](./media/virtual-machines-docker-with-xplat-cli/dockercreateresults.png)

> [AZURE.NOTE]Crear una máquina virtual puede llevarle unos minutos, pero después de aprovisionarla (el valor del estado es `ReadyRole`), el demonio de Docker (el servicio Docker) se inicia y puede conectarse al host del contenedor de Docker.

Para probar la VM Docker que ha creado en Azure, escriba

`docker --tls -H tcp://<vm-name-you-used>.cloudapp.net:2376 info`

donde *&lt;vm-name-you-used&gt;* es el nombre de la máquina virtual que ha usado en su llamada a `azure vm docker create`. Debe ver algo similar a lo siguiente, lo que significa que en su máquina virtual de host de Docker está activa y en funcionamiento en Azure y esperando sus comandos.

Ahora puede intentar conectarse mediante el cliente Docker para obtener información (en algunas configuraciones de cliente Docker, como por ejemplo las de Mac, deberá que usar `sudo`):

	sudo docker --tls -H tcp://testsshasm.cloudapp.net:2376 info
	Password:
	Containers: 0
	Images: 0
	Storage Driver: devicemapper
	Pool Name: docker-8:1-131781-pool
	Pool Blocksize: 65.54 kB
	Backing Filesystem: extfs
	Data file: /dev/loop0
	Metadata file: /dev/loop1
	Data Space Used: 1.821 GB
	Data Space Total: 107.4 GB
	Data Space Available: 28 GB
	Metadata Space Used: 1.479 MB
	Metadata Space Total: 2.147 GB
	Metadata Space Available: 2.146 GB
	Udev Sync Supported: true
	Deferred Removal Enabled: false
	Data loop file: /var/lib/docker/devicemapper/devicemapper/data
	Metadata loop file: /var/lib/docker/devicemapper/devicemapper/metadata
	Library Version: 1.02.77 (2012-10-15)
	Execution Driver: native-0.2
	Logging Driver: json-file
	Kernel Version: 3.19.0-28-generic
	Operating System: Ubuntu 14.04.3 LTS
	CPUs: 1
	Total Memory: 1.637 GiB
	Name: testsshasm
	WARNING: No swap limit support

Para asegurarse de que todo funciona correctamente, puede examinar la máquina virtual de la extensión Docker:

	azure vm extension get testsshasm
	info: Executing command vm extension get
	+ Getting virtual machines
	data: Publisher Extension name ReferenceName Version State
	data: -------------------- --------------- ------------------------- ------- ------
	data: Microsoft.Azure.E... DockerExtension DockerExtension 1.* Enable
	info: vm extension get command OK

### Autenticación de máquina virtual de host de Docker

Además de crear la VM Docker, el comando `azure vm docker create` también crea automáticamente los certificados necesarios para permitir a su equipo cliente de Docker conectarse al host del contenedor de Azure mediante HTTPS y los certificados se almacenan tanto en el equipo cliente como en el host, según corresponda. En los siguientes intentos, se vuelven a usar los certificados ya existentes y se comparten con el nuevo host.

De forma predeterminada, los certificados se colocan en `~/.docker` y Docker se configurará para ejecutarse en el puerto **2376**. Si desea utilizar un puerto o directorio diferente, entonces puede utilizar una de las siguientes opciones de línea de comando `azure vm docker create` para configurar su VM host de contenedor Docker para que utilice un puerto diferente o diferentes certificados para conectarse con los clientes:

```
-dp, --docker-port [port]              Port to use for docker [2376]
-dc, --docker-cert-dir [dir]           Directory containing docker certs [.docker/]
```

El daemon de Docker en el host está configurado para que realice escuchas y autenticaciones de las conexiones de cliente en el puerto especificado mediante los certificados generados por el comando `azure vm docker create`. El equipo cliente debe tener estos certificados para obtener acceso al host de Docker.

> [AZURE.NOTE]Un host de red que se ejecuta sin estos certificados será vulnerable a cualquiera que se pueda conectar al equipo. Antes de modificar la configuración predeterminada, asegúrese de que comprende los riesgos de sus equipos y aplicaciones.

## Pasos siguientes

Está preparado para ir a la [Guía del usuario de Docker] y usar su VM Docker. Para crear una máquina virtual con la funcionalidad Docker en el nuevo portal, consulte [Uso de la extensión de VM Docker con el portal].

<!--Anchors-->
[Subheading 1]: #subheading-1
[Subheading 2]: #subheading-2
[Subheading 3]: #subheading-3
[Next steps]: #next-steps

[How to use the Docker VM Extension with Azure]: #How-to-use-the-Docker-VM-Extension-with-Azure
[Virtual Machine Extensions for Linux and Windows]: #Virtual-Machine-Extensions-For-Linux-and-Windows
[Container and Container Management Resources for Azure]: #Container-and-Container-Management-Resources-for-Azure



<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]: virtual-machines-windows-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: ../web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: ../storage-whatis-account.md
[Uso de la extensión de VM Docker con el portal]: http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-portal/

[Guía del usuario de Docker]: https://docs.docker.com/userguide/
 

<!---HONumber=AcomDC_0107_2016-->