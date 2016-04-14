<properties
	pageTitle="Características de alta disponibilidad de HDInsight (Hadoop) basado en Linux | Microsoft Azure"
	description="Obtenga información acerca de cómo los clústeres de HDInsight basados en Linux mejoran la confiabilidad y la disponibilidad mediante el uso de un nodo principal adicional. Aprenderá cómo esto afecta a los servicios de Hadoop como Ambari y Hive, y cómo conectarse individualmente a cada nodo mediante SSH."
	services="hdinsight"
	editor="cgronlun"
	manager="paulettm"
	authors="Blackmist"
	documentationCenter=""
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="01/08/2016"
	ms.author="larryfr"/>

#Disponibilidad y fiabilidad de clústeres de Hadoop en HDInsight

Los clústeres Hadoop basados en Linux implementados por HDInsight de Azure usan un segundo nodo principal. Esto aumenta la disponibilidad y confiabilidad de los servicios de Hadoop y los trabajos que se ejecutan en Azure.

> [AZURE.NOTE] Los pasos descritos en este documento son específicos de los clústeres de HDInsight basados en Linux. Si usa un clúster basado en Windows, consulte [Disponibilidad y confiabilidad de clústeres Hadoop basados en Windows en HDInsight](hdinsight-high-availability.md) para obtener información específica de Windows.

##Descripción de los nodos principales

Algunas implementaciones de Hadoop tienen un único nodo principal que hospeda servicios y componentes que administran el error de los nodos de datos (trabajo) sin problemas. Pero las posibles interrupciones de servicios principales que se ejecutan en el nodo principal provocarían que el clúster dejase de funcionar.

Los clústeres de HDInsight proporcionan un nodo principal secundario, lo que permite a los servicios y componentes principales continuar ejecutándose en el nodo secundario si se produce un error en el principal.

> [AZURE.IMPORTANT] Ambos nodos principales están activos y en ejecución dentro del clúster al mismo tiempo. Algunos servicios, como HDFS o YARN solo están “activos” en un nodo principal en un determinado momento (y “en espera” en el otro nodo principal). Otros servicios como HiveServer2 o MetaStore de Hive están activos en ambos nodos principales al mismo tiempo.

Los nodos [ZooKeeper](http://zookeeper.apache.org/) (ZK) se usan para la elección del líder de los servicios principales en los nodos principales y para asegurarse de que los servicios, los nodos de datos (trabajo) y las puertas de enlace saben en qué nodo principal está activo un servicio principal.

## Acceso a los nodos principales

En general, todo acceso al clúster a través de las puertas de enlace públicas (web Ambari y API de REST) no se lleva a cabo por tener varios nodos principales. La solicitud se enruta al nodo principal activo y se atiende según corresponda.

Al tener acceso al clúster mediante SSH, efectuar la conexión a través del puerto 22 (el valor predeterminado de SSH) permitirá conectarse al nodo principal 0; la conexión a través del puerto 23 provocará la conexión al nodo principal 1.

### Nombres completos de dominio (FQDN) internos

Los nodos de un clúster de HDInsight tienen una dirección IP interna y el FQDN al que solo se puede acceder desde el clúster (por ejemplo, una sesión de SSH en el nodo principal o un trabajo que se ejecuta en el clúster). Al obtener acceso a servicios en el clúster mediante la dirección IP o FQDN interna, debe usar Ambari para comprobar la dirección IP o FQDN que se usará al obtener acceso al servicio.

Por ejemplo, el servicio de Oozie solo puede ejecutarse en un nodo principal y el uso del comando `oozie` desde una sesión de SSH requiere la dirección URL del servicio. Esto puede conseguirse en Ambari mediante el comando siguiente:

	curl -u admin:PASSWORD "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations?type=oozie-site&tag=TOPOLOGY_RESOLVED" | grep oozie.base.url

Esto devolverá un valor similar al siguiente, que contiene la dirección URL interna para usar con el comando `oozie`:

	"oozie.base.url": "http://hn0-CLUSTERNAME-randomcharacters.cx.internal.cloudapp.net:11000/oozie"

## Cómo comprobar el estado del servicio

La interfaz de usuario de la web de Ambari o la API de REST de Ambari pueden usarse para comprobar el estado de los servicios que se ejecutan en el nodo principal.

###API de REST de Ambari

Puede usar el siguiente comando para comprobar el estado de un servicio a través de la API de REST de Ambari:

	curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICENAME?fields=ServiceInfo/state

* Reemplace **CONTRASEÑA** por el usuario HTTP (admin.), la contraseña de la cuenta

* Reemplace **CLUSTERNAME** por el nombre del clúster.

* Reemplace **SERVICENAME** por el nombre del servicio para comprobar el estado de

Por ejemplo, para comprobar el estado del servicio **HDFS** en un clúster denominado **mycluster**, con la contraseña **contraseña**, deberá usar lo siguiente:

	curl -u admin:password https://mycluster.azurehdinsight.net/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state

La respuesta será similar a la siguiente:

	{
	  "href" : "http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8080/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state",
	  "ServiceInfo" : {
	    "cluster_name" : "mycluster",
	    "service_name" : "HDFS",
	    "state" : "STARTED"
	  }
	}

La dirección URL nos indica que el servicio se está ejecutando en el **nodo principal 0**.

El estado nos indica que el servicio se está ejecutando o se ha **INICIADO**.

Si no sabe qué servicios están instalados en el clúster, puede usar lo siguiente para recuperar una lista:

	curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services

####Componentes de servicio

Los servicios pueden contener componentes cuyo estado desea comprobar de forma individual. Por ejemplo, HDFS contiene el componente NameNode. Para ver información sobre un componente, el comando sería:

	curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component

Si no sabe qué componentes son proporcionados por un servicio, puede usar lo siguiente para recuperar una lista:

	curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component

###Interfaz de usuario web de Ambari

La interfaz de usuario web de Ambari se puede visualizar en https://CLUSTERNAME.azurehdinsight.net. Reemplace **CLUSTERNAME** por el nombre del clúster. Si se le solicita, introduzca las credenciales de usuario HTTP de su clúster. El nombre de usuario HTTP predeterminado es **admin** y la contraseña es la contraseña que especificó al crear el clúster.

Cuando llegue a la página de Ambari, se enumerarán los servicios instalados a la izquierda de la página.

![Servicios instalados](./media/hdinsight-high-availability-linux/services.png)

Hay una serie de iconos que pueden aparecer junto a un servicio para indicar el estado. Las alertas relacionadas con un servicio se pueden ver mediante el vínculo **Alertas** que se encuentra en la parte superior de la página. Puede seleccionar cada servicio para ver más información sobre él.

Mientras que la página del servicio proporciona información sobre el estado y la configuración de cada servicio, no proporciona información sobre en qué nodo principal se está ejecutando el servicio. Para ver esta información, use el vínculo **Hosts** de la parte superior de la página. Esto mostrará los hosts del clúster, incluidos los nodos principales.

![lista de hosts](./media/hdinsight-high-availability-linux/hosts.png)

Al seleccionar el vínculo de uno de los nodos principales se mostrarán los servicios y componentes que se ejecutan en ese nodo.

![Estado del componente](./media/hdinsight-high-availability-linux/nodeservices.png)

## Acceso a los archivos de registro en el nodo principal secundario

###SSH

Mientras está conectado a un nodo principal a través de SSH, los archivos de registro pueden encontrarse en **/var/log**. Por ejemplo, **/var/log/hadoop-yarn/yarn** contiene registros de YARN.

Cada nodo principal puede tener entradas de registro único, por lo que debe comprobar los registros en ambos.

###Ambari

> [AZURE.NOTE] Para acceder a los archivos de registro a través de Ambari se requiere un túnel SSH, ya que los sitios web de los servicios individuales no se exponen públicamente en Internet. Para obtener información sobre cómo usar un túnel SSH, vea [Uso de la tunelización SSH para acceder a la interfaz de usuario web de Ambari, ResourceManager, JobHistory, NameNode, Oozie y otras interfaces de usuario web](hdinsight-linux-ambari-ssh-tunnel.md).

En la interfaz de usuario web de Ambari, seleccione el servicio cuyos registros quiere ver (por ejemplo, YARN) y luego use **Vínculos rápidos** para seleccionar el nodo principal en el que quiere ver los registros.

![Uso de vínculos rápidos para ver los registros](./media/hdinsight-high-availability-linux/viewlogs.png)

## Configuración del tamaño del nodo principal ##

El tamaño del nodo principal solo se puede seleccionar durante la creación del clúster. Puede encontrar una lista de los diferentes tamaños de máquina virtual disponibles para HDInsight, incluido el núcleo, la memoria y el almacenamiento local para cada uno, en la [página de precios de HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

Al crear un nuevo clúster, puede especificar el tamaño de los nodos. A continuación se ofrece información sobre cómo especificar el tamaño mediante el [Portal de Azure][preview-portal], [Azure PowerShell][azure-powershell] y la [CLI de Azure][azure-cli]\:

* **Portal de Azure**: al crear un clúster, tiene la opción de establecer el tamaño (nivel de precios) de los nodos principal y de datos (trabajo) del clúster:

	![Imagen del asistente para creación de clústeres con selección del tamaño del nodo](./media/hdinsight-high-availability-linux/headnodesize.png)

* **CLI de Azure**: cuando se usa el comando `azure hdinsight cluster create`, puede establecer el tamaño del nodo principal mediante el parámetro `--headNodeSize`.

* **Azure PowerShell**: cuando se usa el cmdlet `New-AzureRmHDInsightCluster`, puede establecer el tamaño del nodo principal mediante el parámetro `-HeadNodeVMSize`.

##Pasos siguientes

En este documento ha aprendido cómo proporciona HDInsight de Azure alta disponibilidad para Hadoop. Use lo siguiente para obtener más información acerca de las cosas que se mencionan en este documento.

- [Referencia de REST de Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)

- [Instalación y configuración de la interfaz de la línea de comandos de Azure](../xplat-cli-install.md)

- [Instale y configure Azure PowerShell.](../powershell-install-configure.md)

- [Administración de HDInsight mediante Ambari](hdinsight-hadoop-manage-ambari.md)

- [Aprovisionamiento de clústeres de HDInsight basado en Linux](hdinsight-hadoop-provision-linux-clusters.md)

[preview-portal]: https://portal.azure.com/
[azure-powershell]: ../powershell-install-configure.md
[azure-cli]: ../xplat-cli-install.md

<!---HONumber=AcomDC_0128_2016-->