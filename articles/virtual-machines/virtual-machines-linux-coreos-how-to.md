<properties
	pageTitle="Uso de CoreOS en Azure | Microsoft Azure"
	description="Describe CoreOS, cómo crear un clúster de máquinas virtuales de CoreOS en Azure en el modelo de implementación clásico y su uso básico."
	services="virtual-machines"
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure-services"
	ms.date="10/21/2015"
	ms.author="rasquill"/>

# Uso de CoreOS en Azure

En este tema se describe [CoreOS] y se muestra cómo crear un clúster de tres máquinas virtuales de CoreOS en Azure para que pueda comprender rápidamente el funcionamiento de este sistema operativo. Utiliza los elementos más básicos de las implementaciones de CoreOS y ejemplos de [CoreOS with Azure], el tutorial sobre [Tim Park's CoreOS Tutorial] y el tutorial sobre [CoreOS de Patrick Chanezon] para demostrar lo fácil que es entender la estructura básica de una implementación CoreOS y conseguir que un clúster de tres máquinas virtuales se ejecute correctamente.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager deployment model](https://azure.microsoft.com/documentation/templates/coreos-with-fleet-multivm/).


## CoreOS, clústeres y contenedores de Linux

CoreOS es una versión ligera de Linux diseñada para la creación rápida de clústeres potencialmente muy grandes de máquinas virtuales que utilizan contenedores de Linux como único mecanismo de empaquetado, incluidos contenedores [Docker]. CoreOS está diseñado para admitir lo siguiente:

+ un nivel de automatización muy elevado
+ una implementación de aplicaciones más sencilla y coherente
+ escalabilidad a nivel de aplicación y de sistema

A un alto nivel, las características de CoreOS que consiguen estos objetivos son las siguientes:

1. Sistema de un paquete: CoreOS ejecuta solo imágenes de contenedor de Linux que se ejecutan en contenedores de Linux para lograr velocidad, uniformidad y facilidad de explotación
2. Actualizaciones de sistema operativo que se llevan a cabo atómicamente de manera que los sistemas operativos se actualizan como una entidad única y pueden devolverse fácilmente a un estado conocido
3. Demonios (servicios) [etcd](https://github.com/coreos/etcd) y [fleet](https://github.com/coreos/fleet) para una comunicación y una administración dinámicas entre máquina virtual y clúster

Esta es una descripción muy general de CoreOS y sus características. Para obtener información más completa sobre CoreOS, consulte la [información general sobre CoreOS].

## Consideraciones sobre la seguridad
Actualmente, CoreOS supone que aquellos que pueden aplicar SSH en el clúster tienen permiso para administrarlo. El resultado es que, sin modificación, los clústeres de CoreOS incluyen capacidades excelentes para entornos de prueba y desarrollo, pero debería aplicar más medidas de seguridad en cualquier entorno de producción.

## Uso de CoreOS en Azure

En esta sección se describe cómo crear un servicio en la nube de Azure que contenga tres máquinas virtuales de CoreOS mediante la [Interfaz de la línea de comandos de Azure (CLI de Azure)]. Los pasos básicos son los siguientes:

1. Crear los certificados SSH y las claves para proteger la comunicación con la máquina virtual de CoreOS
2. Obtener el identificador etcd del clúster para la intercomunicación
3. Crear un archivo de configuración de la nube en formato [YAML]
4. Usar la CLI de Azure para crear un nuevo servicio en la nube de Azure y tres máquinas virtuales de CoreOS
5. Probar su clúster de CoreOS desde una máquina virtual de Azure
6. Probar su clúster de CoreOS desde localhost

### Crear claves públicas y privadas para la comunicación

Siga las instrucciones de [Utilización de SSH con Linux en Azure](virtual-machines-linux-use-ssh-key.md) para crear una clave pública y privada para SSH. (Los pasos básicos se encuentran en las instrucciones a continuación). Va a utilizar estas claves para conectar con las máquinas virtuales en el clúster para verificar que funcionan y que pueden comunicarse entre sí.

> [AZURE.NOTE] En este tema se da por supuesto que no tiene estas claves, y se requiere que cree los archivos `myPrivateKey.pem` y `myCert.pem` para una mayor claridad. Si ya tiene un par de claves pública y privada guardado en `~/.ssh/id_rsa`, basta con que escriba `openssl req -x509 -key ~/.ssh/id_rsa -nodes -days 365 -newkey rsa:2048 -out myCert.pem` para obtener el archivo .pem que necesita cargar en Azure.

1. En un directorio de trabajo, escriba `openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem` para crear la clave privada y el certificado X.509 asociado a ella.

2. Para afirmar que el propietario de la clave privada puede leer o escribir el archivo, escriba `chmod 600 myPrivateKey.key`.

Ahora debería tener un archivo `myPrivateKey.key` y otro `myCert.pem` en el directorio de trabajo.


### Obtener el identificador etcd del clúster

El demonio `etcd` de CoreOS requiere un identificador de detección para consultar automáticamente todos los nodos del clúster. Para recuperar el identificador de detección y guardarlo en un archivo `etcdid`, escriba

```
curl https://discovery.etcd.io/new | grep ^http.* > etcdid
```

### Crear un archivo de configuración de la nube

Aún en el mismo directorio de trabajo, con el editor de texto que prefiera, cree un archivo que contenga el texto siguiente y guárdelo como `cloud-config.yaml`. (Puede guardarlo con el nombre que quiera, pero, cuando cree las máquinas virtuales en el paso siguiente, tendrá que hacer referencia a este nombre de archivo en la opción **--custom-data** del comando **azure vm create**).

> [AZURE.NOTE] Recuerde escribir `cat etcdid` para recuperar el identificador de detección etcd del archivo `etcdid` que creó antes y sustituya `<token>` en el siguiente archivo `cloud-config.yaml` por el número generado en el archivo `etcdid`. Si no puede validar el clúster al final, es posible que este sea uno de los pasos que ha pasado por alto.

```
#cloud-config

coreos:
  etcd:
    # generate a new token for each unique cluster from https://discovery.etcd.io/new
    discovery: https://discovery.etcd.io/<token>
    # deployments across multiple cloud services will need to use $public_ipv4
    addr: $private_ipv4:4001
    peer-addr: $private_ipv4:7001
  units:
    - name: etcd.service
      command: start
    - name: fleet.service
      command: start
```

(Para obtener información más completa acerca del archivo de configuración de nube, consulte [Uso de configuración de la nube](https://coreos.com/docs/cluster-management/setup/cloudinit-cloud-config/) en la documentación de CoreOS).

### Usar la CLI de Azure para crear una nueva máquina virtual de CoreOS
<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

1. Instale la [Interfaz de la línea de comandos de Azure (CLI de Azure)] si aún no lo ha hecho e inicie sesión con un identificador profesional o educativo, o bien descargue un archivo .publishsettings e impórtelo en su cuenta.
2. Busque la imagen CoreOS. Para ubicar las imágenes disponibles en cualquier momento, escriba `azure vm image list | grep CoreOS` y obtendrá una lista de resultados similar a:

	datos: 2b171e93f07c4903bcad35bda10acf22\_\_CoreOS-Stable-522.6.0 Public Linux

3. Cree un servicio en la nube para su clúster básico escribiendo `azure service create <cloud-service-name>`, donde <*nombre-de-servicio-en-la-nube* es el nombre del servicio en la nube de CoreOS. En este ejemplo se usa el nombre **`coreos-cluster`**; tendrá que usar de nuevo el nombre que seleccionó al crear las instancias de máquina virtual de CoreOS dentro del servicio en la nube.

	Una nota: si revisa el trabajo que lleva realizado hasta ahora en el [Portal de Azure](https://portal.azure.com), comprobará que el nombre de su servicio en la nube es tanto un grupo de recursos como un dominio, tal y como se aprecia en la imagen siguiente:

	![][CloudServiceInNewPortal]

4. Conéctese a su servicio en la nube y cree en él una nueva máquina virtual de CoreOS usando el comando **azure vm create**. Pasará la ubicación de su certificado X.509 en la opción **--ssh-cert**. Cree su primera imagen de VM escribiendo lo siguiente (recuerde sustituir **coreos-cluster** por el nombre del servicio en la nube que ha creado):

	```
azure vm create --custom-data=cloud-config.yaml --ssh=22 --ssh-cert=./myCert.pem --no-ssh-password --vm-name=node-1 --connect=coreos-cluster --location="West US" 2b171e93f07c4903bcad35bda10acf22__CoreOS-Stable-522.6.0 core
```

5. Cree el segundo nodo repitiendo el comando del paso 4, reemplazando el valor de **--vm-name** por **node-2** y el valor del puerto **--ssh** por 2022.

6. Cree el tercer nodo repitiendo el comando del paso 4, reemplazando el valor de **--vm-name** por **node-3** y el valor del puerto **--ssh** por 3022.

Puede ver en la siguiente captura de pantalla el aspecto del clúster de CoreOS en el portal.

![][EmptyCoreOSCluster]

### Probar su clúster de CoreOS desde una máquina virtual de Azure

Para probar el clúster, asegúrese de que se encuentra en su directorio de trabajo y, a continuación, conéctese a **node-1** utilizando **ssh**, pasando la clave privada escribiendo lo siguiente:

	ssh core@coreos-cluster.cloudapp.net -p 22 -i ./myPrivateKey.key

Cuando se conecte, escriba `sudo fleetctl list-machines` para ver si el clúster ya identificó todas las máquinas virtuales del clúster. Debería recibir una respuesta similar a la siguiente:


	core@node-1 ~ $ sudo fleetctl list-machines
	MACHINE		IP		METADATA
	442e6cfb...	100.71.168.115	-
	a05e2d7c...	100.71.168.87	-
	f7de6717...	100.71.188.96	-


### Probar su clúster de CoreOS desde localhost

Por último, vamos a probar el clúster de CoreOS desde el cliente local de Linux. Es posible que pueda instalar**fleetctl** usando **npm** o que desee instalar **fleet** y compilar **fleetctl** por sí mismo en el cliente local. **fleet** requiere **golang**, por lo que puede que necesite instalar ese primero escribiendo:

`sudo apt-get install golang`

Luego clone el repositorio **fleet** desde github escribiendo lo siguiente:

`git clone https://github.com/coreos/fleet.git`

Compile **fleet** cambiando al directorio `fleet` y escriba

`./build`

Y por último, coloque **fleet** para un uso sencillo (dependiendo de su configuración, es posible que necesite o no utilizar **sudo**):

`cp bin/fleetctl /usr/local/bin`

Asegúrese de que **fleet** tenga acceso a su `myPrivateKey.key` en el directorio de trabajo, escribiendo lo siguiente:

`ssh-add ./myPrivateKey.key`

> [AZURE.NOTE] Si ya está usando la clave `~/.ssh/id_rsa`, agréguela con `ssh-add ~/.ssh/id_rsa`.

Ahora está listo para probar remotamente utilizando el mismo comando **fleetctl** que utilizó de **node-1**, pero pasando más argumentos remotos:

`fleetctl --tunnel coreos-cluster.cloudapp.net:22 list-machines`

Los resultados deberían ser exactamente los mismos:


	MACHINE		IP		METADATA
	442e6cfb...	100.71.168.115	-
	a05e2d7c...	100.71.168.87	-
	f7de6717...	100.71.188.96	-

## Pasos siguientes

Ahora debería tener un clúster de CoreOS de tres nodos ejecutándose en Azure. Desde aquí, puede explorar cómo crear clústeres más complejos y usar Docker y crear aplicaciones más interesantes. Para probar un par de ejemplos rápidos, vea [Introducción a Fleet en CoreOS de Azure].

<!--Anchors-->
[CoreOS, Clusters, and Linux Containers]: #intro
[Security Considerations]: #security
[How to use CoreOS on Azure]: #usingcoreos
[Subheading 3]: #subheading-3
[Next steps]: #next-steps

<!--Image references-->
[CloudServiceInNewPortal]: ./media/virtual-machines-linux-coreos-how-to/cloudservicefromnewportal.png
[EmptyCoreOSCluster]: ./media/virtual-machines-linux-coreos-how-to/endresultemptycluster.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[Interfaz de la línea de comandos de Azure (CLI de Azure)]: ../xplat-cli-install.md
[CoreOS]: https://coreos.com/
[información general sobre CoreOS]: https://coreos.com/using-coreos/
[CoreOS with Azure]: https://coreos.com/docs/running-coreos/cloud-providers/azure/
[Tim Park's CoreOS Tutorial]: https://github.com/timfpark/coreos-azure
[CoreOS de Patrick Chanezon]: https://github.com/chanezon/azure-linux/tree/master/coreos/cloud-init
[Docker]: http://docker.io
[YAML]: http://yaml.org/
[Introducción a Fleet en CoreOS de Azure]: virtual-machines-linux-coreos-fleet-get-started.md

<!---HONumber=AcomDC_0204_2016-->