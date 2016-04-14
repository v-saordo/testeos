<properties
   pageTitle="Uso de una máquina docker con Azure | Microsoft Azure"
   description="Aquí podrá ponerse al día rápidamente en cuanto al funcionamiento de Azure con una máquina Docker en Ubuntu mediante el modelo de implementación clásico."
   services="virtual-machines"
   documentationCenter="virtual-machines"
   authors="squillace"
   manager="timlt"
   editor="tysonn"
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="01/04/2016"
   ms.author="rasquill"/>

# Cómo usar una máquina docker con Azure

En este tema se describe cómo usar [Docker](https://www.docker.com/) con una [máquina](https://github.com/docker/machine) y la [CLI de Azure](https://github.com/Azure/azure-xplat-cli) para crear una máquina virtual de Azure que administre rápida y fácilmente los contenedores de Linux desde un equipo en el que se ejecute Ubuntu. Para mostrarle esto, el tutorial le indicará cómo implementar tanto la imagen de [busybox del concentrador de Docker](https://registry.hub.docker.com/_/busybox/) como la [imagen de nginx del concentrador de Docke ](https://registry.hub.docker.com/_/nginx/) y configurar el contenedor para enrutar las solicitudes web al contenedor de nginx. (La documentación de la **máquina** Docker indica cómo modificar esas instrucciones para adaptarlas a otras plataformas).


[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo del Administrador de recursos.


Existen algunos requisitos previos para completar el tutorial. Necesitará instalar lo siguiente:

1. [npm](https://docs.npmjs.com/) y [Node.js](http://nodejs.org/)
2. [La CLI de Azure](https://github.com/Azure/azure-xplat-cli)
3. Un [cliente de Docker](https://docs.docker.com/installation/)

Si instala estos elementos en el orden en que se muestran, su equipo con Ubuntu estará listo para el tutorial. (Este tutorial se puede aplicar en gran medida en cualquier otra distribución basada en dpkg como Debian. Si descubre requisitos o pasos adicionales, no dude en hacérnoslo saber.)

## Obtener (o crear) una máquina docker

La forma más rápida para comenzar con **docker-machine** es descargar la versión adecuada directamente desde la opción para [compartir la versión](https://github.com/docker/machine/releases). El equipo cliente de este tutorial ejecutaba Ubuntu en un equipo x64, así que la imagen que se ha usado es **docker-machine\_linux-amd64**.

También puede compilar su **docker-machine** mediante los pasos que se indican para [contribuir a la máquina](https://github.com/docker/machine#contributing). Tiene que poder descargar más de un 1 GB para realizar la compilación; gracias a ello podrá personalizar su experiencia de la forma que le sea más cómoda.

> [AZURE.NOTE] También puede crear un [vínculo simbólico](http://en.wikipedia.org/wiki/Symbolic_link) en su propia versión de la plataforma, pero en este tutorial se usará directamente el binario para mostrar claramente cada comportamiento. El resultado de esto, es que en lugar de comandos como `docker-machine env` tal y como muestra la documentación de **docker-machine**, este tutorial usa `docker-machine_linux-amd64 env`. Usted decide si crear un vínculo simbólico o si directamente prefiere usar el nombre binario, pero, si cambia el nombre que está usando, recuerde modificarlo en las instrucciones que tiene a continuación.

<br />

>  Sea cual sea el método que elija, debe llamar al binario directamente en la línea de comandos o colocarlo en una ruta de acceso como **/usr/local/bin**. Asegúrese de que está marcado como ejecutable escribiendo `chmod +x` &lt;*`binaryName`*&gt;, donde &lt;*`binaryName`*&gt; es el nombre del ejecutable de su máquina Docker. Este tutorial usa **docker-machine\_linux-amd64**.

## Crear los archivos de certificado y clave para docker, la máquina y Azure

Ahora debe crear los archivos de certificado y clave que Azure necesita para confirmar su identidad y permisos, así como aquellos que **docker-machine** necesita para comunicarse con su máquina virtual de Azure para crear y administrar contenedores de forma remota. Si ya tiene esos archivos en un directorio (quizá para usarlos con docker) puede reutilizarlos. De todos modos, la mejor práctica para probar **docker-machine** es crearlos en un directorio aparte e indicárselos a docker-machine.

> [AZURE.NOTE] Si prueba **docker-machine** una y otra vez, no olvide o volver a utilizar los mismos archivos de certificado y de clave. **docker-machine** crea un conjunto de certificados de cliente; también se puede examinar todo lo que se crea en `~/.docker/machine`. Si mueve estos certificados a otro equipo, necesitará mover también las carpetas de certificados de **docker-machine**. Esto será diferente si va a usar **docker-machine** en otra plataforma para ver, por ejemplo, cómo funciona todo.

Si tiene experiencia con las distribuciones de Linux, es posible que ya tenga esos archivos disponibles en un lugar específico para usarlos en el equipo; la [documentación de Docker HTTPS explica estos pasos perfectamente](https://docs.docker.com/articles/https/). Sin embargo, lo que mostramos a continuación es el modo más sencillo de dar este paso.

1. Cree una carpeta local y en la línea de comandos vaya a esa carpeta y escriba:

		openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem
		openssl pkcs12 -export -out mycert.pfx -in mycert.pem -name "My Certificate"

	Prepárese aquí escribir la contraseña de exportación para el certificado y capturarla para un uso futuro. A continuación, escriba:

		openssl x509 -inform pem -in mycert.pem -outform der -out mycert.cer

2. Cargue el archivo .cer de su certificado a Azure. En el [Portal de Azure clásico](https://manage.windowsazure.com), haga clic en **Configuración** en la parte inferior izquierda del área de servicios (se muestra a continuación)

	![][portalsettingsitem]

	y haga clic en **Certificados de administración**:

	![][managementcertificatesitem]

	a continuación, haga clic en **Cargar** (en la parte inferior de la página) ![][uploaditem] para cargar el archivo **mycert.cer** que creó en el paso anterior.

3. En el mismo panel **Configuración** del portal, haga clic en **Suscripciones** y capture el identificador de suscripción que se usa para crear la máquina virtual, ya que lo necesitará en el siguiente paso. (También puede buscar el identificador de suscripción en la línea de comandos usando el comando de CLI de Azure `azure account list`, que muestra el identificador de suscripción para cada suscripción que tenga en la cuenta).

4. Cree una máquina virtual host de Docker en Azure mediante el comando **docker-machine create**. Este comando requiere el identificador de suscripción que guardó en el paso anterior y la ruta de acceso al archivo **.pem** que creó en el paso 1. Este tema usa "machine-name" como nombre de Servicio en la nube de Azure (y su máquina virtual), pero deberá reemplazarlo por uno de su elección; a parte, recuerde usar su nombre de servicio en la nube en cualquier otro paso en el que necesite el nombre de la máquina virtual. (Recuerde que en este ejemplo estamos usando el nombre binario completo y no un vínculo simbólico de **docker-machine**).

		docker-machine_linux-amd64 create \
	    -d azure \
	    --azure-subscription-id="<subscription ID acquired above>" \
	    --azure-subscription-cert="mycert.pem" \
	    machine-name

	Como para los dos primeros pasos necesita crear una nueva máquina virtual, cargar el agente de Azure para Linux y actualizar la nueva máquina virtual, debería ver algo parecido a lo siguiente.

		INFO[0001] Creating Azure machine...
	    INFO[0049] Waiting for SSH...
	    modprobe: FATAL: Module aufs not found.
	    + sudo -E sh -c sleep 3; apt-get update
	    + sudo -E sh -c sleep 3; apt-get install -y -q linux-image-extra-3.13.0-36-generic
	    E: Unable to correct problems, you have held broken packages.
	    modprobe: FATAL: Module aufs not found.
	    Warning: tried to install linux-image-extra-3.13.0-36-generic (for AUFS)
	     but we still have no AUFS.  Docker may not work. Proceeding anyways!
	    + sleep 10
	    + [ https://get.docker.com/ = https://get.docker.com/ ]
	    + sudo -E sh -c apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 36A1D7869245C8950F966E92D8576A8BA88D21E9
	    gpg: requesting key A88D21E9 from hkp server keyserver.ubuntu.com
	    gpg: key A88D21E9: public key "Docker Release Tool (releasedocker) <docker@dotcloud.com>" imported
	    gpg: Total number processed: 1
	    gpg:               imported: 1  (RSA: 1)
	    + sudo -E sh -c echo deb https://get.docker.com/ubuntu docker main > /etc/apt/sources.list.d/docker.list
	    + sudo -E sh -c sleep 3; apt-get update; apt-get install -y -q lxc-docker
	    + sudo -E sh -c docker version
	    INFO[0368] "machine-name" has been created and is now the active machine.
	    INFO[0368] To point your Docker client at it, run this in your shell: $(docker-machine_linux-amd64 env machine-name)

    > [AZURE.NOTE] Ya que está creando una máquina virtual, es posible que tarde unos minutos en estar lista. Mientras espera, puede comprobar el estado de su nuevo host Docker escribiendo `azure vm list` mediante la CLI de Azure hasta que vea que sus máquinas virtuales tienen el estado **ReadyRole**.

5. Establezca las variables de entorno de docker y de la máquina para la sesión de terminal. La última línea de comentarios recomienda que ejecute de forma inmediata el comando **env** para exportar las variables de entorno necesarias para usar su cliente de docker directamente con una máquina específica.

		$(docker-machine_linux-amd64 env machine-name)

	Una vez hecho esto, no necesitará ningún comando especial para usar su cliente de docker local para conectarse a la máquina virtual que la **máquina** Docker creó.

	    $ docker info
	    Containers: 0
	    Images: 0
	    Storage Driver: devicemapper
	     Pool Name: docker-8:1-131736-pool
	     Pool Blocksize: 65.54 kB
	     Backing Filesystem: extfs
	     Data file: /dev/loop0
	     Metadata file: /dev/loop1
	     Data Space Used: 305.7 MB
	     Data Space Total: 107.4 GB
	     Metadata Space Used: 729.1 kB
	     Metadata Space Total: 2.147 GB
	     Udev Sync Supported: false
	     Data loop file: /var/lib/docker/devicemapper/devicemapper/data
	     Metadata loop file: /var/lib/docker/devicemapper/devicemapper/metadata
	     Library Version: 1.02.82-git (2013-10-04)
	    Execution Driver: native-0.2
	    Kernel Version: 3.13.0-36-generic
	    Operating System: Ubuntu 14.04.1 LTS
	    CPUs: 1
	    Total Memory: 1.639 GiB
	    Name: machine-name
	    ID: W3FZ:BCZW:UX24:GDSV:FR4N:N3JW:XOC2:RI56:IWQX:LRTZ:3G4P:6KJK
	    WARNING: No swap limit support

> [AZURE.NOTE] Este tutorial muestra cómo **docker-machine** crea una máquina virtual. De todos modos, puede repetir estos pasos para crear tantas máquinas como desee. Si decide hacer esto, la mejor manera de alternar entre máquinas virtuales con docker es usar el comando **env** insertado para establecer las variables del entorno de **docker** para cada comando individual. Por ejemplo, para usar**docker info** con una máquina virtual diferente, puede escribir `docker $(docker-machine env <VM name>) info` y el comando **env** rellenará la información de conexión de docker para usarla con esa máquina virtual.

## Estamos terminando. Vamos a ejecutar algunas aplicaciones de forma remota usando docker y las imágenes del concentrador Docker.

Ahora podrá usar docker de la forma habitual para crear una aplicación en el contenedor. La forma más fácil de hacer la demostración es [busybox](https://registry.hub.docker.com/_/busybox/):

	    $  docker run busybox echo hello world
	    Unable to find image 'busybox:latest' locally
	    511136ea3c5a: Pull complete
	    df7546f9f060: Pull complete
	    ea13149945cb: Pull complete
	    4986bf8c1536: Pull complete
	    busybox:latest: The image you are pulling has been verified. Important: image verification is a tech preview feature and should not be relied on to provide security.
	    Status: Downloaded newer image for busybox:latest
	    hello world

Sin embargo, es posible que quiera crear una aplicación que pueda ver inmediatamente en Internet, como por ejemplo [nginx](https://registry.hub.docker.com/_/nginx/), desde el [concentrador de Docker](https://registry.hub.docker.com/).

> [AZURE.NOTE] Recuerde que ha de usar la opción **-P** para que **docker** asigne puertos aleatorios a la imagen y **-d** para asegurarse de que el contenedor se ejecuta en segundo plano sin interrupción. (Si se olvida de hacer esto, iniciará nginx y éste se apagará inmediatamente. ¡No se olvide!)

	$ docker run --name machinenginx -P -d nginx
    Unable to find image 'nginx:latest' locally
    30d39e59ffe2: Pull complete
    c90d655b99b2: Pull complete
    d9ee0b8eeda7: Pull complete
    3225d58a895a: Pull complete
    224fea58b6cc: Pull complete
    ef9d79968cc6: Pull complete
    f22d05624ebc: Pull complete
    117696d1464e: Pull complete
    2ebe3e67fb76: Pull complete
    ad82b43d6595: Pull complete
    e90c322c3a1c: Pull complete
    4b5657a3d162: Pull complete
    511136ea3c5a: Already exists
    nginx:latest: The image you are pulling has been verified. Important: image verification is a tech preview feature and should not be relied on to provide security.
    Status: Downloaded newer image for nginx:latest
    5883e2ff55a4ba0aa55c5c9179cebb590ad86539ea1d4d367d83dc98a4976848

Para ver la aplicación en Internet, debe crear un extremo público en el puerto 80 para la máquina virtual de Azure y asignar ese puerto al puerto del contenedor nginx. Primero, use `docker ps -a` para buscar el contenedor y ver qué puertos tiene asignados **docker** para el puerto 80 del contenedor. (La información que se muestra a continuación está editada para mostrarle solo la información de los puertos; usted encontrará más información en su prueba.)

	$ docker ps -a
    IMAGE               PORTS
    nginx:latest        0.0.0.0:49153->80/tcp, 0.0.0.0:49154->443/tcp
    busybox:latest

Podemos ver que docker ha asignado el puerto 80 del contenedor al puerto 49153 de la máquina virtual. Ahora podemos usar la CLI de Azure para asignar el puerto público 80 del servicio en la nube externo al puerto 49153 de la máquina virtual. A continuación, docker se asegura de que el tráfico tcp entrante del puerto 49153 de la máquina virtual se enruta al contenedor nginx.

	$ azure vm endpoint create machine-name 80 49153
    info:    Executing command vm endpoint create
    + Getting virtual machines
    + Reading network configuration
    + Updating network configuration
    info:    vm endpoint create command OK

Abra su explorador favorito y échele un vistazo.

![][nginx]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Pasos siguientes
Vaya a la [guía de usuario de Docker](https://docs.docker.com/userguide/) y cree algunas aplicaciones en Microsoft Azure. O bien, puede jugar con [Docker Swarm en Azure] y así comprobar cómo puede usar [Swarm](https://github.com/docker/swarm) con Docker y Azure.

<!--Image references-->
[nginx]: ./media/virtual-machines-docker-machine/nginxondocker.png
[portalsettingsitem]: ./media/virtual-machines-docker-machine/portalsettingsitem.png
[managementcertificatesitem]: ./media/virtual-machines-docker-machine/managementcertificatesitem.png
[uploaditem]: ./media/virtual-machines-docker-machine/uploaditem.png

<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]: virtual-machines-windows-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: ../web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: ../storage-whatis-account.md
[Docker Swarm en Azure]: virtual-machines-docker-swarm.md

<!---HONumber=AcomDC_0128_2016-->