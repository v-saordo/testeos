<properties
	pageTitle="Clusterice MySQL con conjuntos con equilibrio de carga | Microsoft Azure"
	description="Configuración de un clúster MySQL Linux con equilibrio de carga y alta disponibilidad creado con el modelo de implementación clásica en Azure"
	services="virtual-machines"
	documentationCenter=""
	authors="bureado"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/14/2015"
	ms.author="jparrel"/>

# Uso de conjuntos de carga equilibrada para crear clústeres en MySQL en Linux

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo del Administrador de recursos.


La finalidad de este artículo es explorar e ilustrar los diferentes enfoques disponibles para implementar servicios basados en Linux altamente disponibles en Microsoft Azure, explorando la alta disponibilidad de MySQL Server como base. En [Channel 9](http://channel9.msdn.com/Blogs/Open/Load-balancing-highly-available-Linux-services-on-Windows-Azure-OpenLDAP-and-MySQL) hay un vídeo disponible que ilustra este enfoque.

Describiremos una solución de alta disponibilidad de MySQL de un solo maestro y dos nodos independientes basada en DRBD, Corosync y Pacemaker. Solamente un nodo MySQL ejecuta en cada momento. La lectura y escritura desde el recurso DRBD también se limitan a un solo nodo en cada momento.

No se necesita una solución VIP como LVS porque usamos conjuntos con equilibrio de carga de Microsoft Azure para proporcionar tanto funcionalidad round robin como detección, eliminación y recuperación estable de extremos de VIP. VIP es una dirección IPv4 globalmente enrutable asignada por Microsoft Azure cuando se crea el servicio la nube.

Hay otras posibles arquitecturas para MySQL, como por ejemplo NBD Cluster, Percona y Galera, así como diversas soluciones middleware que incluyen, al menos, una disponible como máquina virtual en [VM Depot](http://vmdepot.msopentech.com). Mientras que estas soluciones pueden replicarse en unidifusión frente a multidifusión o difusión, y no se basan en almacenamiento compartido o interfaces de varias redes, los escenarios deben ser fáciles de implementar en Microsoft Azure.

Por supuesto, estas arquitecturas de clúster se pueden extender a otros productos como PostgreSQL y OpenLDAP de forma similar. Por ejemplo, este procedimiento de equilibro de carga independiente se probó correctamente con OpenLDAP multimaestro y puede verlo en nuestro blog de Channel 9.

## Preparación

Necesitará una cuenta de Microsoft Azure con una suscripción válida capaz de crear al menos dos (2) máquinas virtuales (en este ejemplo se usó XS), una red y una subred, un grupo de afinidad y un conjunto de disponibilidad, así como la capacidad de crear nuevos VHD en la misma región que el servicio en la nube y de acoplarlos a las máquinas virtuales de Linux.

### Entorno probado

- Ubuntu 13.10
  - DRBD
  - MySQL Server
  - Corosync y Pacemaker

### Grupo de afinidad

Para crear un grupo de afinidad para la solución, inicie sesión en el Portal de Azure clásico, desplácese hacia abajo hasta Configuración y créelo. Los recursos asignados creados más tarde se asignarán a este grupo de afinidad.

### Redes

Se crea una nueva red y, a su vez, se crea una subred dentro de la red. Hemos elegido una red 10.10.10.0/24 con tan solo una subred /24 dentro.

### Máquinas virtuales

La primera máquina virtual Ubuntu 13.10 se crea usando una imagen de la galería de Ubuntu refrendada llamada `hadb01`. En el proceso, se crea un nuevo servicio en la nube llamado hadb. Le llamamos de esta forma para ilustrar la naturaleza compartida y de carga equilibrada que tendrá el servicio cuando agreguemos más recursos. La creación de `hadb01` no presenta ningún problema y se completa mediante el portal. Se crea un extremo para SSH automáticamente y se selecciona nuestra red creada. También optamos por crear un nuevo conjunto de disponibilidad para las máquinas virtuales.

Una vez creada la primera máquina virtual (técnicamente, cuando se crea el servicio de nube) procedemos a crear la segunda máquina virtual, `hadb02`. Para la segunda máquina virtual, también usaremos la máquina virtual Ubuntu 13.10 desde la galería mediante el Portal, pero optaremos por usar un servicio en la nube existente, `hadb.cloudapp.net`, en lugar de crear uno nuevo. La red y el conjunto de disponibilidad se deben seleccionar automáticamente. También se creará un extremo SSH.

Una vez creadas ambas máquinas virtuales, tomaremos nota del puerto SSH para `hadb01` (TCP 22) y `hadb02` (automáticamente asignado por Azure)

### Almacenamiento acoplado

Acoplamos un nuevo disco a ambas máquinas virtuales y creamos nuevos discos de 5 GB en el proceso. Los discos se hospedarán en el contenedor VHD en uso para nuestros discos del sistema operativo principal. Una vez creados y acoplados los discos, no es necesario que reiniciemos Linux, ya que el kernel verá el nuevo dispositivo (normalmente `/dev/sdc`; puede comprobar `dmesg` para la salida).

En cada máquina virtual procederemos a crear una nueva partición mediante `cfdisk` (partición de Linux primaria) y escribiremos la nueva tabla de particiones. **No cree un sistema de archivos en esta partición **.

## Configuración del clúster

En ambas máquinas virtuales de Ubuntu, necesitamos usar APT para instalar Corosync, Pacemaker y DRBD. Uso de `apt-get`:

    sudo apt-get install corosync pacemaker drbd8-utils.

**No instale MySQL en este momento **. Los scripts de instalación de Debian y Ubuntu inicializarán un directorio de datos de MySQL en `/var/lib/mysql`, pero dado que el directorio se sustituirá por un sistema de archivos DRBD, necesitamos hacer esto más tarde.

En este momento también debemos comprobar (mediante `/sbin/ifconfig`) que ambas máquinas virtuales usan las direcciones de la subred 10.10.10.0/24 y que pueden ejecutar el comando ping entre ellas por nombre. Si lo desea, también puede usar `ssh-keygen` y `ssh-copy-id` para asegurarse de que ambas máquinas virtuales pueden comunicarse a través de SSH sin necesidad que contraseña.

### Configuración de DRBD

Crearemos un recurso DRBD que usa la partición `/dev/sdc1` subyacente para generar un recurso `/dev/drbd1` capaz de asumir el formato ext3 y usarse tanto en nodos principales como secundarios. Para hacer esto, abra `/etc/drbd.d/r0.res` y copie la siguiente definición de recurso. Haga esto en ambas máquinas virtuales:

    resource r0 {
      on `hadb01` {
        device  /dev/drbd1;
        disk   /dev/sdc1;
        address  10.10.10.4:7789;
        meta-disk internal;
      }
      on `hadb02` {
        device  /dev/drbd1;
        disk   /dev/sdc1;
        address  10.10.10.5:7789;
        meta-disk internal;
      }
    }

Después de hacer esto, inicialice el recurso mediante `drbdadm` en ambas máquinas virtuales:

    sudo drbdadm -c /etc/drbd.conf role r0
    sudo drbdadm up r0

Y por último, en la principal (`hadb01`) fuerce la propiedad (principal) del recurso DRBD:

    sudo drbdadm primary --force r0

Si examina el contenido de /proc/drbd (`sudo cat /proc/drbd`) en ambas máquinas virtuales, debe ver `Primary/Secondary` en `hadb01` y `Secondary/Primary` en `hadb02`, que concuerda con la solución en este punto. El disco de 5 GB se sincronizará a través de la red 10.10.10.0/24 sin coste alguno para los clientes.

Una vez sincronizado el disco, puede crear el sistema de archivos en `hadb01`. Para tareas de prueba usamos ext2 pero la siguiente instrucción creará un sistema de archivos ext3:

    mkfs.ext3 /dev/drbd1

### Montaje del recurso DRBD

En `hadb01` ahora estamos preparados para montar los recursos DRBD. Debian y productos derivados usan `/var/lib/mysql` como directorio de datos de MySQL. Dado que no hemos instalado MySQL, crearemos el directorio y montaremos el recurso DRBD. En `hadb01`:

    sudo mkdir /var/lib/mysql
    sudo mount /dev/drbd1 /var/lib/mysql

## Configuración de MySQL

Ahora estamos preparados para instalar MySQL en `hadb01`:

    sudo apt-get install mysql-server

Para `hadb02`, tiene dos opciones. Puede instalar mysql-server ahora, lo que creará /var/lib/mysql y se rellenará con un nuevo directorio de datos y, después, continuar para quitar el contenido. En `hadb02`:

    sudo apt-get install mysql-server
    sudo service mysql stop
    sudo rm –rf /var/lib/mysql/*

La segunda opción es conmutar por error a `hadb02` y después instalar mysql-server allí (los scripts de instalación tendrán en cuenta la instalación existente y no la tocarán)

En `hadb01`:

    sudo drbdadm secondary –force r0

En `hadb02`:

    sudo drbdadm primary –force r0
    sudo apt-get install mysql-server

Si no pretende conmutar por error DRBD ahora, la primera opción es más sencilla aunque posiblemente menos elegante. Después de configurar esto, puede comenzar a trabajar en su base de datos MySQL. En `hadb02` (o en cualquiera de los servidores activos, conforme a DRBD):

    mysql –u root –p
    CREATE DATABASE azureha;
    CREATE TABLE things ( id SERIAL, name VARCHAR(255) );
    INSERT INTO things VALUES (1, "Yet another entity");
    GRANT ALL ON things.* TO root;

**Advertencia**: Esta última instrucción deshabilita de forma eficaz la autenticación para el usuario raíz en esta tabla. Las instrucciones GRANT de producción deben reemplazar esto y solamente se incluye por motivos ilustrativos.

También necesita habilitar las redes para MySQL si desea realizar consultas desde fuera de las máquinas virtuales, que es la finalidad de esta guía. En ambas máquinas virtuales, abra `/etc/mysql/my.cnf`, busque `bind-address` y cámbielo de 127.0.0.1 a 0.0.0.0. Después de guardar el archivo, emita `sudo service mysql restart`en su principal actual.

### Creación del conjunto de carga equilibrada de MySQL

Volveremos al portal y buscaremos la máquina virtual `hadb01` y después Puntos de conexión. Crearemos un nuevo extremo, elegiremos MySQL (TCP 3306) en la lista desplegable y activaremos la casilla *Crear nuevo conjunto de carga equilibrada*. Llamaremos `lb-mysql` a nuestro extremo de carga equilibrada. Dejaremos la mayoría de las opciones como están, excepto el tiempo, que lo reduciremos a 5 (segundos, mínimo)

Cuando se haya creado el extremo, iremos a `hadb02`, Extremos y crearemos un nuevo extremo, pero elegiremos `lb-mysql`; después, seleccionaremos MySQL en el menú desplegable. También puede usar la CLI de Azure para este paso.

Llegados a este punto tenemos todo lo que necesitamos para una operación manual del clúster.

### Prueba del conjunto de carga equilibrada

Las pruebas se pueden realizar desde una máquina externa, mediante cualquier cliente MySQL así como aplicaciones (por ejemplo phpMyAdmin ejecutándose como un sitio web de Azure). En este caso, hemos usado la herramienta de línea de comandos de MySQL en otro cuadro de Linux:

    mysql azureha –u root –h hadb.cloudapp.net –e "select * from things;"

### Conmutación por error manual

Ahora puede simular conmutaciones por error cerrando MySQL, cambiando a la máquina virtual primaria de DRBD e iniciando MySQL de nuevo.

En hadb01:

    service mysql stop && umount /var/lib/mysql ; drbdadm secondary r0

Después en hadb02:

    drbdadm primary r0 ; mount /dev/drbd1 /var/lib/mysql && service mysql start

Una vez realizada la conmutación por error manualmente, puede repetir la consulta remota, que debe funcionar perfectamente.

## Configuración de Corosync

Corosync es la infraestructura de clúster subyacente que Pacemaker necesita para trabajar. Para usuarios de Heartbeat v1 y v2 (y otras metodologías como Ultramonkey) Corosync es una división de las funcionalidades CRM, mientras que la funcionalidad de Pacemaker es más similar a Hearbeat.

La restricción principal para Corosync en Azure es que Corosync prefiere comunicaciones multidifusión mejor que difusión y difusión mejor que unidifusión, pero las redes de Microsoft Azure solo admiten unidifusión.

Afortunadamente, Corosync tiene un modo de trabajo unidifusión y la única restricción real es que, dado que todos los nodos no se comunican entre sí de forma *automágica*, necesita definir dichos nodos en los archivos de configuración, incluidas sus direcciones IP. Podemos utilizar los archivos de ejemplo de Corosync para unidifusión y simplemente cambiar la dirección de enlace, las listas de nodos y el directorio de registro (Ubuntu usa `/var/log/corosync`, mientras que los archivos de ejemplo usan `/var/log/cluster`) y habilitar las herramientas de quórum.

**Tenga en cuenta la directiva `transport: udpu` siguiente y las direcciones IP definidas manualmente para los nodos**.

En `/etc/corosync/corosync.conf` para ambos nodos:

    totem {
      version: 2
      crypto_cipher: none
      crypto_hash: none
      interface {
        ringnumber: 0
        bindnetaddr: 10.10.10.0
        mcastport: 5405
        ttl: 1
      }
      transport: udpu
    }

    logging {
      fileline: off
      to_logfile: yes
      to_syslog: yes
      logfile: /var/log/corosync/corosync.log
      debug: off
      timestamp: on
      logger_subsys {
        subsys: QUORUM
        debug: off
        }
      }

    nodelist {
      node {
        ring0_addr: 10.10.10.4
        nodeid: 1
      }

      node {
        ring0_addr: 10.10.10.5
        nodeid: 2
      }
    }

    quorum {
      provider: corosync_votequorum
    }

Copiamos este archivo de configuración en ambas máquinas virtuales e iniciamos Corosync en los dos nodos:

    sudo service start corosync

Poco después de iniciar el servicio, el clúster se debe establecer en el anillo actual y se debe constituir el quórum. Podemos comprobar esta funcionalidad revisando registros o:

    sudo corosync-quorumtool –l

Debe aparecer una salida similar a la imagen que se muestra continuación:

![corosync-quorumtool -l sample output](media/virtual-machines-linux-mysql-cluster/image001.png)

## Configuración de Pacemaker

Pacemaker usa el clúster para supervisar recursos, definir el momento en el que las máquinas virtuales principales dejan de funcionar y cambiar aquellos recursos a las máquinas virtuales secundarias. Entre las diferentes posibilidades que existen, los recursos se pueden definir desde un conjunto de scripts disponibles o desde scripts LSB (de tipo init).

Queremos que Pacemaker "posea" el recurso DRBD, el punto de montaje y el servicio MySQL. Si Pacemaker puede activar y desactivar DRBD, montarlo y desmontarlo e iniciar y detener MySQL en el orden correcto cuando algo vaya mal con la máquina principal, nuestra configuración se habrá completado.

Cuando instalé por primera vez Pacemaker, la configuración debe ser suficientemente sencilla, algo como lo siguiente:

    node $id="1" hadb01
      attributes standby="off"
    node $id="2" hadb02
      attributes standby="off"

Compruébelo mediante la ejecución de `sudo crm configure show`. Ahora, cree un archivo (por ejemplo, `/tmp/cluster.conf`) con los siguiente recursos:

    primitive drbd_mysql ocf:linbit:drbd \
          params drbd_resource="r0" \
          op monitor interval="29s" role="Master" \
          op monitor interval="31s" role="Slave"

    ms ms_drbd_mysql drbd_mysql \
          meta master-max="1" master-node-max="1" \
            clone-max="2" clone-node-max="1" \
            notify="true"

    primitive fs_mysql ocf:heartbeat:Filesystem \
          params device="/dev/drbd/by-res/r0" \
          directory="/var/lib/mysql" fstype="ext3"

    primitive mysqld lsb:mysql

    group mysql fs_mysql mysqld

    colocation mysql_on_drbd \
           inf: mysql ms_drbd_mysql:Master

    order mysql_after_drbd \
           inf: ms_drbd_mysql:promote mysql:start

    property stonith-enabled=false

    property no-quorum-policy=ignore

Y cárguelo en la configuración (solamente necesita hacer esto en un nodo):

    sudo crm configure
      load update /tmp/cluster.conf
      commit
      exit

Asimismo, asegúrese de que Pacemaker se inicia durante el arranque en ambos nodos:

    sudo update-rc.d pacemaker defaults

Después de unos segundos, y usando `sudo crm_mon –L`, compruebe que uno de los nodos se ha convertido en el maestro para el clúster y se ejecuta en todos los recursos. Puede usar mount y ps para comprobar que los recursos se están ejecutando.

La siguiente captura de pantalla muestra `crm_mon` con un nodo detenido (salir usando Control-C):

![crm\_mon node stopped](media/virtual-machines-linux-mysql-cluster/image002.png)

Y esta captura de pantalla muestra ambos nodos, con uno maestro y otro esclavo:

![crm\_mon operational master/slave](media/virtual-machines-linux-mysql-cluster/image003.png)

## Prueba

Estamos preparados para llevar a cabo una simulación de conmutación por error automática. Existen dos formas de hacerlo: suave y dura. Para llevar a cabo la simulación por software, usaremos la función de cierre del clúster: ``crm_standby -U `uname -n` -v on``. Usando esto en el maestro, el esclavo asumirá el control. No olvide volver a desactivarlo (crm\_mon le indicará que un nodo se encuentra en espera si no es así).

Si optamos por la opción de hardware, lo que haremos será cerrar la máquina virtual principal (hadb01) mediante el Portal o cambiar el nivel de ejecución en dicha máquina virtual (es decir, detenerla o cerrarla); de esta forma ayudamos a Corosync y Pacemaker indicando el apagado del maestro. Podemos probar esto (útil para ventanas de mantenimiento) pero también podemos forzar el escenario simplemente congelando la máquina virtual.

## STONITH

Debe ser posible emitir un cierre de máquina virtual a través de la CLI de Azure en lugar de un script STONITH que controle un dispositivo físico. Puede usar `/usr/lib/stonith/plugins/external/ssh` como base y habilitar STONITH en la configuración del clúster. CLI de Azure se debe instalar globalmente y la configuración o el perfil de publicación se debe cargar para el usuario del clúster.

Código de ejemplo para el recurso disponible en [GitHub](https://github.com/bureado/aztonith). Necesita cambiar la configuración del clúster agregando lo siguiente a `sudo crm configure`:

    primitive st-azure stonith:external/azure \
      params hostlist="hadb01 hadb02" \
      clone fencing st-azure \
      property stonith-enabled=true \
      commit

**Nota:** el script no realiza comprobaciones ascendentes o descendentes. El recurso SSH original tenía 15 comprobaciones ping pero el tiempo de recuperación para una máquina virtual de Azure podría ser más variable.

## Limitaciones

Se aplican las siguientes limitaciones:

- El script de recursos DRBD linbit que administra DRBD como un recurso en Pacemaker usa `drbdadm down` al cerrar un nodo, aunque dicho nodo esté entrando en modo de espera. No se trata de la situación ideal porque el esclavo no sincronizará el recurso DRBD mientras se escribe en el maestro. Si no se produce un error en el maestro, el esclavo podrá asumir el control y el estado del sistema de archivos anterior. Hay dos formas posibles de resolver este problema:
  - Aplicando un comando `drbdadm up r0` en todos los nodos del clúster mediante un guardián local (sin clústeres), o bien,
  - Editar el script DRBD linbit, asegurándose de que no se llama a `down`, en `/usr/lib/ocf/resource.d/linbit/drbd`.
- El equilibrador de carga necesita al menos 5 segundos para responder, por lo que las aplicaciones deben ser compatibles con clústeres y ser más tolerantes en lo que al tiempo de espera se refiere; otras arquitecturas también pueden ser útiles, como por ejemplo colas en aplicación, middleware de consulta, etc.
- El ajuste de MySQL es necesario para garantizar que la escritura se realiza a un ritmo apropiado y las memorias caché se vacían en disco con la máxima frecuencia posible para minimizar la pérdida de memoria.
- El rendimiento de escritura dependerá de la interconexión de las máquinas virtuales en la conmutación virtual ya que este es el mecanismo que usa DRBD para replicar el dispositivo.

<!---HONumber=AcomDC_1203_2015-->