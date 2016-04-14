
<properties
   pageTitle="Introducción al uso de docker con enjambre en Azure"
   description="Describe cómo crear un grupo de máquinas virtuales con la extensión de máquina virtual de Docker y usar enjambre para crear un clúster de Docker."
   services="virtual-machines"
   documentationCenter="virtual-machines"
   authors="squillace"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="01/04/2016"
   ms.author="rasquill"/>

# Cómo usar docker con enjambre

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo del Administrador de recursos.


Este tema le mostrará una manera muy sencilla de usar [docker](https://www.docker.com/) con [swarm](https://github.com/docker/swarm) para crear un clúster administrado por swarm en Azure. Éste crea cuatro máquinas virtuales en Azure: una que actúe como el administrador de enjambre y tres como parte del clúster de los hosts docker. Cuando termine, puede usar enjambre para ver el clúster y comenzar a usar docker en él. Además, las llamadas de CLI de Azure en este tema utilizan el modo de administración de servicio (asm).

> [AZURE.NOTE] En este tema, usaremos docker con swarm y la CLI de Azure *sin* usar **docker-machine** para mostrar cómo funcionan en conjunto las distintas herramientas, aunque sigan siendo independientes. **docker-machine** tiene conmutadores **--swarm** que le permiten usar **docker-machine** para agregar nodos directamente a swarm. Para obtener un ejemplo, consulte la documentación de [docker-machine](https://github.com/docker/machine). Si no sabe cómo ejecutar **docker-machine** con las máquinas virtuales de Azure, consulte [Cómo usar una máquina docker con Azure](virtual-machines-docker-machine.md).

## Crear hosts docker con máquinas virtuales de Azure

En este tema creamos cuatro máquinas virtuales, pero puede usar tantas como quiera. Llame a las siguientes acciones con *&lt;password&gt;* reemplazada por la contraseña que haya elegido.

    azure vm docker create swarm-master -l "East US" -e 22 $imagename ops <password>
    azure vm docker create swarm-node-1 -l "East US" -e 22 $imagename ops <password>
    azure vm docker create swarm-node-2 -l "East US" -e 22 $imagename ops <password>
    azure vm docker create swarm-node-3 -l "East US" -e 22 $imagename ops <password>

Cuando haya terminado, debería poder usar **azure vm list** para ver sus máquinas virtuales de Azure:

    $ azure vm list | grep "swarm-[mn]"
    data:    swarm-master     ReadyRole           East US       swarm-master.cloudapp.net                               100.78.186.65
    data:    swarm-node-1     ReadyRole           East US       swarm-node-1.cloudapp.net                               100.66.72.126
    data:    swarm-node-2     ReadyRole           East US       swarm-node-2.cloudapp.net                               100.72.18.47  
    data:    swarm-node-3     ReadyRole           East US       swarm-node-3.cloudapp.net                               100.78.24.68  

## Instalar enjambre en la máquina virtual principal de enjambre

En este tema usamos el [modelo de instalación del contenedor de la documentación de docker swarm](https://github.com/docker/swarm#1---docker-image), pero también puede usar SSH en **swarm-master**. En este modelo, **swarm** se descarga como un contenedor de docker que ejecuta swarm. A continuación, llevamos a cabo este paso *de forma remota desde nuestro equipo portátil usando docker* para conectarnos a la máquina virtual de**swarm-master** e indicarle que use el comando de creación del identificador de clúster, **swarm create**. El identificador de clúster es la manera con la que **swarm** detecta a los miembros del grupo de swarm. (Además también puede clonar el repositorio y compilarlo usted mismo; gracias a ello tendrá el control total y podrá habilitar la depuración.)

    $ docker --tls -H tcp://swarm-master.cloudapp.net:2376 run --rm swarm create
    Unable to find image 'swarm:latest' locally
    511136ea3c5a: Pull complete
    a8bbe4db330c: Pull complete
    9dfb95669acc: Pull complete
    0b3950daf974: Pull complete
    633f3d9a9685: Pull complete
    bba5f98a0414: Pull complete
    defbc1ab4462: Pull complete
    92d78d321ff2: Pull complete
    swarm:latest: The image you are pulling has been verified. Important: image verification is a tech preview feature and should not be relied on to provide security.
    Status: Downloaded newer image for swarm:latest
    36731c17189fd8f450c395db8437befd

La última línea es el identificador de clúster, así que cópielo ya que tendrá que usarlo de nuevo cuando una el nodo de máquinas virtuales al maestro de enjambre para crear el "enjambre". En este ejemplo, el identificador de clúster es **36731c17189fd8f450c395db8437befd**.

> [AZURE.NOTE]Para que quede claro: usamos la instalación docker local para conectarnos a la máquina virtual de **swarm-master** en Azure e indicamos a**swarm-master** que descargue, instale y ejecute el comando **create**, el cual devolverá el identificador de clúster que usaremos más adelante para detectar otras cosas. <!-- --> Para confirmar esta acción, ejecute`docker -H tcp://`*&lt;hostname&gt;* ` images` para enumerar los procesos de contenedor en **swarm-master** y en otro nodo para compararlos (como ejecutamos el comando de enjambre anterior con el conmutador **--rm** el contenedor se quitó una vez terminada su tarea, así que si usa **docker ps -a**, éste no devolverá nada).


        $ docker --tls -H tcp://swarm-master.cloudapp.net:2376 images
        REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
        swarm               latest              92d78d321ff2        11 days ago         7.19 MB
        $ docker --tls -H tcp://swarm-node-1.cloudapp.net:2376 images
        REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
        $
<P />
> Si ya conoce **docker**, sabrá que el resto de nodos no tienen entradas, ya que no se ha descargado y ejecutado ninguna imagen todavía.

## Unir las máquinas virtuales de nodo a nuestro clúster docker

Para cada nodo, enumere la información de extremo usando la CLI de Azure. A continuación, hacemos esto para el host de docker **swarm-node-1** para así obtener el puerto del docker del nodo.

    $ azure vm endpoint list swarm-node-1
    info:    Executing command vm endpoint list
    + Getting virtual machines
    data:    Name    Protocol  Public Port  Private Port  Virtual IP      EnableDirectServerReturn  Load Balanced
    data:    ------  --------  -----------  ------------  --------------  ------------------------  -------------
    data:    docker  tcp       2376         2376          138.91.112.194  false                     No
    data:    ssh     tcp       22           22            138.91.112.194  false                     No
    info:    vm endpoint list command OK


Si usa **docker** y la opción `-H` para señalar al cliente de docker en su máquina virtual de nodo, una ese nodo al swarm que está creando pasando el identificador de clúster y el puerto de docker del nodo (este último usa **--addr**):

    $ docker --tls -H tcp://swarm-node-1.cloudapp.net:2376 run -d swarm join --addr=138.91.112.194:2376 token://36731c17189fd8f450c395db8437befd
    Unable to find image 'swarm:latest' locally
    511136ea3c5a: Pull complete
    a8bbe4db330c: Pull complete
    9dfb95669acc: Pull complete
    0b3950daf974: Pull complete
    633f3d9a9685: Pull complete
    bba5f98a0414: Pull complete
    defbc1ab4462: Pull complete
    92d78d321ff2: Pull complete
    swarm:latest: The image you are pulling has been verified. Important: image verification is a tech preview feature and should not be relied on to provide security.
    Status: Downloaded newer image for swarm:latest
    bbf88f61300bf876c6202d4cf886874b363cd7e2899345ac34dc8ab10c7ae924

Tiene buen aspecto. Para confirmar que **swarm** se está ejecutando en** swarm-node-1**, escriba:

    $ docker --tls -H tcp://swarm-node-1.cloudapp.net:2376 ps -a
        CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS              PORTS               NAMES
        bbf88f61300b        swarm:latest        "/swarm join --addr=   13 seconds ago      Up 12 seconds       2375/tcp            angry_mclean

Repítalo para el resto de nodos del clúster. En nuestro caso, lo hacemos para **swarm-node-2** y **swarm-node-3**.

## Empezar a administrar el clúster enjambre

    $ docker --tls -H tcp://swarm-master.cloudapp.net:2376 run -d -p 2375:2375 swarm manage token://36731c17189fd8f450c395db8437befd
    d7e87c2c147ade438cb4b663bda0ee20981d4818770958f5d317d6aebdcaedd5

y, a continuación, puede enumerar los nodos del clúster:

    ralph@local:~$ docker --tls -H tcp://swarm-master.cloudapp.net:2376 run --rm swarm list token://73f8bc512e94195210fad6e9cd58986f
    54.149.104.203:2375
    54.187.164.89:2375
    92.222.76.190:2375

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Pasos siguientes

Empiece a ejecutar cosas en su enjambre. Si necesita inspiración, consulte [https://github.com/docker/swarm/](https://github.com/docker/swarm/), o quizá prefiera un [vídeo](https://www.youtube.com/watch?v=EC25ARhZ5bI).

<!-- links -->

[docker-machine-azure]: virtual-machines-docker-machine.md
 

<!---HONumber=AcomDC_0107_2016-->