<properties
   pageTitle="Docker y Compose en una máquina virtual | Microsoft Azure"
   description="Introducción rápida al trabajo con Compose y Docker en máquinas virtuales de Azure"
   services="virtual-machines"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="11/16/2015"
   ms.author="danlep"/>

# Introducción a Docker y Compose para definir y ejecutar una aplicación de contenedores múltiples en una máquina virtual de Azure

En este artículo se muestra cómo empezar a usar Docker y [Compose](http://github.com/docker/compose) para definir y ejecutar una aplicación compleja en una máquina virtual de Linux en Azure. Con Compose (el sucesor de*Fig*), use un archivo de texto simple para definir una aplicación compuesta de varios contenedores de Docker. A continuación, gire la aplicación en un único comando que hace todo para ejecutarlo en la máquina virtual. Como ejemplo, en este artículo se muestra cómo configurar rápidamente un blog de WordPress con una base de datos SQL MariaDB de back-end, pero también puede utilizar Compose para configurar aplicaciones más complejas.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](https://azure.microsoft.com/documentation/templates/docker-wordpress-mysql/).


Si no tiene experiencia con Docker o con los contenedores, consulte la [pizarra de alto nivel de Docker](https://azure.microsoft.com/documentation/videos/docker-high-level-whiteboard/).

## Paso 1: Configuración de una máquina virtual de Linux como host de Docker

Puede utilizar una serie de procedimientos de Azure y las imágenes disponibles en Azure Marketplace para crear una máquina virtual de Linux y configurarla como host de Docker. Por ejemplo, consulte [Uso de la extensión de la máquina virtual de Docker desde la interfaz de la línea de comandos de Azure](virtual-machines-docker-with-xplat-cli.md), para obtener un procedimiento rápido y así poder crear una máquina virtual de Ubuntu con la extensión de la máquina virtual de Docker. Cuando utilice la a extensión de máquina virtual de Docker, la máquina virtual se configurará automáticamente como un host de Docker. En el ejemplo de este artículo se muestra cómo usar la [interfaz de línea de comandos de Azure para Mac, Linux y Windows](../xplat-cli-install.md) (la CLI de Azure) en modo de administración de servicios para crear la máquina virtual.

## Paso 2: Instalación de Compose

Después de que la máquina virtual de Linux se ejecute con Docker, conéctela desde el equipo cliente con SSH. Si fuese necesario, instale [Compose](https://github.com/docker/compose/blob/882dc673ce84b0b29cd59b6815cb93f74a6c4134/docs/install.md) ejecutando los dos comandos siguientes.

>[AZURE.TIP] Si usó la extensión de máquina virtual de Docker para crear la máquina virtual, Compose ya está instalado para usted. Omita estos comandos y vaya al paso 3. Solo tiene que instalar Compose si ha instalado Docker en la máquina virtual usted mismo.

```
$ curl -L https://github.com/docker/compose/releases/download/1.1.0/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose

$ chmod +x /usr/local/bin/docker-compose
```
>[AZURE.NOTE]Si recibe un error "Permiso denegado", el directorio /usr/local/bin en la máquina virtual probablemente no es modificable y necesitará instalar Compose como superusuario. Ejecute `sudo -i`; a continuación, los dos comandos anteriores; a continuación, `exit`.

Para probar la instalación de Compose, ejecute el siguiente comando.

```
$ docker-compose --version
```

Verá un resultado similar a `docker-compose 1.4.1`.


## Paso 3: Creación de un archivo de configuración docker-compose.yml

A continuación, deberá crear un archivo `docker-compose.yml`, que es simplemente un archivo de configuración para definir los contenedores de Docker para ejecutar la máquina virtual. El archivo especifica la imagen que se ejecutará en cada contenedor (o podría ser una compilación de un Dockerfile), variables de entorno y dependencias necesarias, puertos, vínculos entre contenedores, etc. Para obtener más información sobre la sintaxis del archivo yml, consulte [Referencia de docker-compose.yml](http://docs.docker.com/compose/yml/).

Cree un directorio de trabajo en su máquina virtual y utilice el editor de texto para crear `docker-compose.yml`. Para probar un ejemplo sencillo, copie el texto siguiente en el archivo. Esta configuración usa imágenes del [Registro de DockerHub](https://registry.hub.docker.com/_/wordpress/) para instalar WordPress (el sistema de administración de contenidos y blogs de código abierto) y una base de datos MariaDB de back-end vinculada.

 ```
 wordpress:
  image: wordpress
  links:
    - db:mysql
  ports:
    - 8080:80

db:
  image: mariadb
  environment:
    MYSQL_ROOT_PASSWORD: <your password>

```

## Paso 4: Inicio de los contenedores con Compose

En el directorio de trabajo de la máquina virtual, ejecute el comando siguiente.

```
$ docker-compose up -d

```

Se inician los contenedores de Docker especificados en `docker-compose.yml`.Verá una salida similar a la siguiente:

```
Creating wordpress_db_1...
Creating wordpress_wordpress_1...
```

>[AZURE.NOTE] Asegúrese de utilizar la opción **-d** al iniciar para que los contenedores se ejecuten continuamente en segundo plano.

Para comprobar que los contenedores están activos, escriba `docker-compose ps`. Debería ver algo parecido a lo siguiente:

```
Name             Command             State              Ports
-------------------------------------------------------------------------
wordpress_db_1     /docker-           Up                 3306/tcp
             entrypoint.sh
             mysqld
wordpress_wordpr   /entrypoint.sh     Up                 0.0.0.0:8080->80
ess_1              apache2-for ...                       /tcp
```

Ahora puede conectarse a WordPress directamente en la máquina virtual; para ello, vaya a `http://localhost:8080`. Si desea conectarse a la máquina virtual a través de Internet, primero configure un extremo HTTP en la máquina virtual que asigne el puerto público 80 al puerto privado 8080. Por ejemplo, en una implementación de Administración de servicios de Azure, ejecute el siguiente comando de la CLI de Azure:

```
$ azure vm endpoint create <machine-name> 80 8080

```

Ahora debería ver la pantalla de inicio de WordPress, donde se puede completar la instalación e iniciar la aplicación.

![Pantalla de inicio de WordPress][wordpress_start]


## Pasos siguientes

* Consulte la [referencia de la CLI de Compose](http://docs.docker.com/compose/reference/) y el [manual del usuario](http://docs.docker.com/compose/) para obtener más ejemplos acerca de la creación e implementación de aplicaciones con varios contenedores.
* Use una plantilla del Administrador de recursos de Azure, o bien una propia o una proporcionada por la [comunidad](https://azure.microsoft.com/documentation/templates/), para implementar una VM de Azure con Docker y una aplicación configurada con Compose. Por ejemplo, la plantilla [Implementación de un blog de WordPress con Docker](https://azure.microsoft.com/documentation/templates/docker-wordpress-mysql/) usa Docker y Compose para implementar rápidamente WordPress con un back-end de MySQL en una máquina virtual de Ubuntu.
* Pruebe a integrar Docker Compose con un clúster de [Docker Swarm](virtual-machines-docker-swarm.md). Consulte [Integración de Docker Compose/Swarm](https://github.com/docker/compose/blob/master/SWARM.md) para ver escenarios.

<!--Image references-->

[wordpress_start]: ./media/virtual-machines-docker-compose-quickstart/WordPress.png

<!---HONumber=AcomDC_0128_2016-->