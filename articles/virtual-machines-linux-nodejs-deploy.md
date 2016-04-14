<properties
   pageTitle="Implementación de una aplicación Node.js para máquinas virtuales de Linux en Azure"
   description="Aprenda a implementar una aplicación Node.js en las máquinas virtuales Linux en Azure."
   services=""
   documentationCenter="nodejs"
   authors="stepro"
   manager="dmitryr"
   editor=""/>

<tags
   ms.service="multiple"
   ms.devlang="nodejs"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/02/2016"
   ms.author="stephpr"/>

# Implementación de una aplicación Node.js para máquinas virtuales de Linux en Azure

Este tutorial muestra cómo implementar una aplicación Node.js en máquinas virtuales Linux que se ejecutan en Azure. Las instrucciones de este tutorial se pueden seguir en cualquier sistema operativo que sea capaz de ejecutar Node.js.

Aprenderá a:

- Bifurcar y clonar un repositorio de GitHub con una aplicación simple de tarea pendiente
- Crear y configurar dos máquinas virtuales Linux en Azure para ejecutar la aplicación
- Efectuar iteraciones en la aplicación enviando actualizaciones a la máquina virtual front-end web

> [AZURE.NOTE]
Para realizar este tutorial, necesita una cuenta de GitHub y una de Microsoft Azure, además de la posibilidad de usar Git desde una máquina de desarrollo.

> Si no tiene cuenta de GitHub, puede suscribirse para obtener una gratis [aquí](https://github.com/join).

> Si no tiene una cuenta de [Microsoft Azure](https://azure.microsoft.com/), puede registrarse para obtener una evaluación gratuita [aquí](https://azure.microsoft.com/pricing/free-trial/). Esto le llevará también a través del proceso de inicio de sesión una [cuenta de Microsoft](http://account.microsoft.com), en caso de que no disponga de una. Como alternativa, si tiene una suscripción de Visual Studio, puede [activar los beneficios de MSDN](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

> Si no tiene Git en la máquina de desarrollo y está utilizando un equipo Macintosh o Windows, instale Git desde [aquí](http://www.git-scm.com). Si usa Linux, instale Git mediante el mecanismo más adecuado para usted, como `sudo apt-get install git`.

## Bifurcación y clonación de la aplicación de tareas pendientes

La aplicación de tareas pendientes que se usa en este tutorial permite implementar un front-end web sencillo en una instancia de MongoDB que realiza un seguimiento de una lista de tareas. Después de iniciar sesión en GitHub, vaya [aquí](https://github.com/stepro/node-todo) para buscar la aplicación y bifurcarla mediante el vínculo en la parte superior derecha. Esto debería crear un repositorio en la cuenta con el nombre *accountname*/node-todo.

Ahora en la máquina de desarrollo, clone este repositorio:

    git clone https://github.com/accountname/node-todo.git

Usaremos este clon local del repositorio un poco más adelante al realizar cambios en el código fuente.

## Creación y configuración de las máquinas virtuales Linux

Azure ofrece un buen soporte para los procesos sin formato con las máquinas virtuales Linux. Esta parte del tutorial muestra la forma de establecer fácilmente dos máquinas virtuales de Linux e implementar la aplicación de tareas pendientes, ejecutando el front-end web en una de ellas y la instancia de MongoDB en la otra.

### Creación de máquinas virtuales

Para crear una nueva máquina virtual en Azure, lo más sencillo es usar el Portal de Azure. Haga clic en [aquí](https://portal.azure.com) para conectarse e iniciar el Portal de Azure en el explorador web. Cuando se haya cargado el Portal de Azure, realice los pasos siguientes:

- Haga clic en el vínculo "+ Nuevo".
- Elija la categoría "Proceso" y la opción "Ubuntu Server 14.04 LTS".
- Elija el modelo de implementación "Administrador de recursos" y haga clic en "Crear".
- Para rellenar los aspectos básicos, siga estas instrucciones:
  - Especifique un nombre que pueda identificar fácilmente más tarde.
  - Para este tutorial, elija la autenticación mediante contraseña.
  - Cree un nuevo grupo de recursos con un nombre identificable.
- Para el tamaño de la máquina virtual, "A1 estándar" es una buena opción para este tutorial.
- Para ver otras opciones, asegúrese de que el tipo de disco sea "Estándar" y acepte todos los valores predeterminados restantes.
- Ponga en marcha la creación en la página de resumen.

Realice el proceso anterior dos veces para crear dos máquinas virtuales Linux: una para el front-end web y otra para la instancia de MongoDB. La creación de las máquinas virtuales tardará entre 5 y 10 minutos.

### Asignación de una entrada DNS para las máquinas virtuales

De manera predeterminada, a las máquinas virtuales creadas en Azure se puede acceder solo a través de una dirección IP pública, como 1.2.3.4. Vamos a hacer que las máquinas se puedan identificar más fácilmente mediante la asignación de entradas de DNS.

Una vez que el portal indique que se han creado las máquinas virtuales, haga clic en el vínculo "Máquinas virtuales" en la barra de exploración izquierda y busque las máquinas. Para cada máquina:

- Busque la pestaña Essentials y haga clic en la dirección IP pública.
- En la configuración de la dirección IP pública, asigne una etiqueta de nombre DNS y guárdela.

El portal comprobará que el nombre especificado esté disponible. Después de guardar la configuración, las máquinas virtuales tendrán nombres de host similares a `machinename.region.cloudapp.azure.com`.

### Conexión a las máquinas virtuales

Al aprovisionar las máquinas virtuales, estas se configuraron para permitir las conexiones remotas a través de SSH. Este es el mecanismo que se utilizará para configurar las máquinas virtuales. Si está utilizando Windows para el desarrollo, debe obtener un cliente SSH en caso de que no disponga de uno. Una opción común es PuTTY, que puede descargar desde [aquí](http://www.chiark.greenend.org.uk/~sgtatham/putty/). Los sistemas operativos Macintosh y Linux vienen con una versión de SSH preinstalada.

### Configuración de la máquina virtual front-end web

Inicie sesión con SSH en la máquina front-end web que creó mediante PuTTY, la línea de comandos ssh u otra herramienta SSH que desee. Debería ver un mensaje de bienvenida seguido de un símbolo del sistema.

En primer lugar, debe asegurarse de que Git y el nodo estén instalados:

    sudo apt-get install -y git
    curl -sL https://deb.nodesource.com/setup_4.x | sudo -E bash -
    sudo apt-get install -y nodejs
    
Puesto que el front-end web de la aplicación depende de algunos módulos Node.js nativos, también hay que instalar el conjunto esencial de herramientas de compilación:

    sudo apt-get install -y build-essential

Por último, vamos a instalar una aplicación Node.js denominada *forever*, que facilita la ejecución de aplicaciones del servidor Node.js:

    sudo npm install -g forever
    
Estas son todas las dependencias que se necesitan en esta máquina virtual para poder ejecutar el front-end web de la aplicación, así que vamos a proceder con la ejecución. Para ello, primero crearemos un clon básico del repositorio de GitHub que bifurcó previamente para que pueda publicar fácilmente las actualizaciones para la máquina virtual (explicaremos este escenario de actualización más adelante) y, a continuación, tendrá que clonar el clon básico para proporcionar una versión del repositorio que se pueda ejecutar realmente.

Desde el directorio particular (~), ejecute los siguientes comandos (reemplace *accountname* con su nombre de cuenta de usuario de GitHub):

    git clone --bare https://github.com/accountname/node-todo.git
    git clone node-todo.git

Ahora acceda al directorio node-todo y ejecute estos comandos:

    npm install
    forever start server.js
    
El front-end web de la aplicación se está ejecutando ahora, sin embargo hay que realizar un paso más antes de que se pueda acceder a la aplicación desde un explorador web. La máquina virtual que creó está protegida por un recurso de Azure denominado *grupo de seguridad de red*, que se creó al aprovisionar la máquina virtual. Actualmente, este recurso solo permite que las solicitudes externas al puerto 22 se enruten a la máquina virtual, lo que permite la comunicación SSH con la máquina, pero nada más. Por tanto, para poder ver la aplicación de tareas pendientes, que está configurada para ejecutarse en el puerto 8080, este puerto también debe estar abierto.

Vuelva al Portal de Azure y siga estos pasos:

- Haga clic en "Grupos de recursos", en la barra de navegación izquierda.
- Elija el grupo de recursos que contiene la máquina virtual.
- En la lista resultante de recursos, elija el grupo de seguridad de red (el que tiene un icono de escudo).
- En las propiedades, elija "Reglas de seguridad de entrada".
- En la barra de herramientas, haga clic en "Agregar".
- Especifique un nombre como "default-allow-todo".
- Configure el protocolo como "TCP".
- Defina el intervalo de puertos de destino en "8080".
- Haga clic en Aceptar y espere a que se cree la regla de seguridad.

Después de crear esta regla de seguridad, la aplicación de lista de tareas estará visible públicamente en Internet y podrá acceder a ella, por ejemplo, mediante una dirección URL como:

    http://machinename.region.cloudapp.azure.com:8080

Observará que, aunque todavía no hemos configurado la máquina virtual de MongoDB, la aplicación de tarea pendiente parece ser bastante funcional. Esto es porque el repositorio de origen está codificado para usar una instancia de MongoDB implementada previamente. Una vez que hemos configurado la máquina virtual de MongoDB, volvemos y cambiamos el código fuente para nuestra instancia de MongoDB privada en su lugar.

### Configuración de la máquina virtual de MongoDB

Inicie sesión con SSH en la segunda máquina que creó mediante PuTTY, la línea de comandos ssh u otra herramienta SSH que desee. Después de ver el mensaje de bienvenida y el símbolo del sistema, instale MongoDB (estas instrucciones proceden de [aquí](https://docs.mongodb.org/master/tutorial/install-mongodb-on-ubuntu/)):

    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv EA312927
    echo "deb http://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.2 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.2.list
    sudo apt-get update
    sudo apt-get install -y mongodb-org

De forma predeterminada, MongoDB está configurado para que solo se pueda acceder localmente. Para este tutorial, configuraremos MongoDB para que se pueda acceder desde la máquina virtual de la aplicación. En un contexto sudo, abra el archivo /etc/mongod.conf y busque la sección `# network interfaces`. Cambie el valor de la configuración `net.bindIp` por `0.0.0.0`.

> [AZURE.NOTE]
Esta configuración atiende solo a los fines de este tutorial. **NO** constituye una práctica de seguridad recomendada y no se debe usar en entornos de producción.

Ahora, asegúrese de que el servicio MongoDB se haya iniciado:

    sudo service mongod restart

MongoDB funciona a través del puerto 27017 de manera predeterminada. Por lo tanto, de la misma manera que había que abrir el puerto 8080 en la máquina virtual front-end web, ahora hay que abrir el puerto 27017 en la máquina virtual de MongoDB.

Vuelva al Portal de Azure y siga estos pasos:

* Haga clic en "Grupos de recursos", en la barra de navegación izquierda.
* Elija el grupo de recursos que contiene la máquina virtual MongoDB.
* En la lista resultante de recursos, elija el grupo de seguridad de red (el que tiene un icono de escudo) con el mismo nombre que asignó a la máquina virtual MongoDB.
* En las propiedades, elija "Reglas de seguridad de entrada".
* En la barra de herramientas, haga clic en "Agregar".
* Especifique un nombre como "default-allow-mongo".
* Configure el protocolo como "TCP".
* Defina el intervalo de puertos de destino en "27017".
* Haga clic en Aceptar y espere a que se cree la regla de seguridad.

## Iteración en la aplicación de tareas pendientes
Hasta ahora, hemos aprovisionado dos máquinas virtuales Linux: una que ejecuta el front-end web de la aplicación y otra que ejecuta una instancia de MongoDB. Pero hay un problema: el front-end web no está utilizando realmente la instancia de MongoDB aprovisionada todavía. Para solucionar esto, vamos a actualizar el código de front-end web para utilizar una variable de entorno en lugar de una instancia codificada de forma rígida.

### Cambio de la aplicación de tareas pendientes

En la máquina de desarrollo donde clonó primero el repositorio node-todo, abra el archivo `node-todo/config/database.js` en el editor que desee y cambie el valor de la dirección URL del valor codificado de forma rígida como `mongodb://...` a `process.env.MONGODB`.

Confirme los cambios y distribúyalos al patrón de GitHub:

    git commit -am "Get MongoDB instance from env"
    git push origin master

Desafortunadamente, con esta acción no se publica el cambio en la máquina virtual front-end web. Vamos a hacer algunos cambios más en esa máquina virtual para habilitar un mecanismo sencillo pero eficaz con el que publicar las actualizaciones, de forma que pueda observar rápidamente el efecto de los cambios en el entorno de producción.

### Configuración de la máquina virtual front-end web
Recuerde que hemos creado previamente un clon básico del repositorio node-todo en la máquina virtual front-end web. Resulta que esta acción crea un nuevo Git remoto en el que se pueden insertar los cambios. Sin embargo, el hecho de realizar inserciones en este elemento remoto no ofrece el modelo de iteración rápido que necesitan los desarrolladores al trabajar con el código.

Lo que nos gustaría es que, al realizar una inserción en el repositorio remoto en la máquina virtual, la aplicación de tareas pendientes en ejecución se actualizara automáticamente. Por suerte, esto es fácil de conseguir con Git.

Git ofrece una serie de enlaces que se ejecutan en momentos concretos para reaccionar ante las acciones realizadas en el repositorio. Estas acciones se ejecutan mediante scripts de shell en la carpeta `hooks` del repositorio. El enlace que es más aplicable para el escenario de actualización automática es el evento `post-update`.

En una sesión SSH para la máquina virtual front-end web, cambie al directorio `~/node-todo.git/hooks` y agregue el siguiente contenido a un archivo denominado `post-update` (sustituya `machinename` y `region` con la información de la máquina virtual de MongoDB):

    #!/bin/bash
    
    forever stopall
    unset 'GIT_DIR'
    export MONGODB="mongodb://machinename.region.cloudapp.azure.com:27017/tododb"
    cd ~/node-todo && git fetch origin && git pull origin master && npm install && forever start ~/node-todo/server.js
    exec git update-server-info
    
Asegúrese de que este archivo sea ejecutable mediante el comando siguiente:

    chmod 755 post-update

Este script garantiza que la aplicación de servidor actual se haya detenido, que el código del repositorio clonado se haya actualizado a la versión más reciente, que se cumplan las dependencias actualizadas y que se reinicie el servidor. También garantiza que se haya configurado el entorno para recibir nuestra primera actualización de la aplicación para obtener la instancia de MongoDB desde una variable de entorno.

### Configuración de la máquina de desarrollo
Ahora vamos a obtener el equipo de desarrollo con un enlace a la máquina virtual front-end web. Esto es tan sencillo como agregar el repositorio básico a la máquina virtual como un equipo remoto. Para ello, ejecute el siguiente comando (sustituya *user* con el nombre de inicio de sesión de la máquina virtual front-end web y especifique lo que proceda en *machinename* y *region*):

    git remote add azure user@machinename.region.cloudapp.azure.com:node-todo.git

Esto es todo lo que se necesita para habilitar la inserción (o, en realidad, la publicación) de cambios en la máquina virtual front-end web.

### Publicación de actualizaciones

Vamos a publicar el único cambio que se ha hecho hasta el momento, de forma que la aplicación usará nuestra instancia MongoDB:

    git push azure master

Debería mostrarse una salida similar a esta:

    Counting objects: 4, done.
    Delta compression using up to 4 threads.
    Compressing objects: 100% (3/3), done.
    Writing objects: 100% (4/4), 406 bytes | 0 bytes/s, done.
    Total 4 (delta 1), reused 0 (delta 0)
    remote: info:    Forever stopped processes:
    remote: data:        uid  command         script    forever pid  id logfile                         uptime
    remote: data:    [0] 0Lyh /usr/bin/nodejs server.js 9064    9301    /home/username/.forever/0Lyh.log 0:0:3:17.487
    remote: From /home/username/node-todo
    remote:    5f31fd7..5bc7be5  master     -> origin/master
    remote: From /home/username/node-todo
    remote:  * branch            master     -> FETCH_HEAD
    remote: Updating 5f31fd7..5bc7be5
    remote: Fast-forward
    remote:  config/database.js | 2 +-
    remote:  1 file changed, 1 insertion(+), 1 deletion(-)
    remote: npm WARN package.json node-todo@0.0.0 No repository field.
    remote: npm WARN package.json node-todo@0.0.0 No license field.
    remote: warn:    --minUptime not set. Defaulting to: 1000ms
    remote: warn:    --spinSleepTime not set. Your script will exit if it does not stay up for at least 1000ms
    remote: info:    Forever processing file: /home/username/node-todo/server.js
    To username@machinename.region.cloudapp.azure.com:node-todo.git
    5f31fd7..5bc7be5  master -> master

Una vez finalizado este comando, actualice la aplicación en un explorador web. Podrá ver que la lista de tareas pendientes que se muestra aquí está vacía y ya no está enlazada a la instancia de MongoDB implementada compartida.

Para completar el tutorial, vamos a hacer otro cambio más visible. En la máquina de desarrollo, abra el archivo node-todo/public/index.html con el editor que desee. Busque el encabezado jumbotron y cambie el título de "I'm a Todo-aholic" a "I'm a Todo-aholic on Azure!".

Ahora vamos a confirmarlo:

    git commit -am "Azurify the title"

Esta vez, vamos a publicar el cambio en Azure antes de insertarlo en el repositorio de GitHub:

    git push azure master

Una vez finalizado este comando, actualice la página web y verá los cambios. Como el resultado es satisfactorio, inserte el cambio en el origen remoto:

    git push origin master

## Pasos siguientes
En este artículo, hemos explicado cómo implementar una aplicación Node.js en máquinas virtuales Linux que se ejecutan en Azure. Para obtener más información acerca de las máquinas virtuales de Linux en Azure, consulte [Introducción a Linux en Azure](/documentation/articles/virtual-machines-linux-introduction/).
    
Para más información sobre cómo desarrollar aplicaciones de Node.js en Azure, consulte el [Centro para desarrolladores de Node.js](/develop/nodejs/).

<!---HONumber=AcomDC_0211_2016-->