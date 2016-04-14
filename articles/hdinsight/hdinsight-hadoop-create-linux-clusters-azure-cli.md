<properties
   	pageTitle="Creación de clústeres de Hadoop, HBase o Storm basados en Linux en HDInsight mediante la CLI de Azure multiplataforma | Microsoft Azure"
   	description="Aprenda a crear clústeres de HDInsight basados en Linux con la CLI de Azure multiplataforma, las plantillas del Administrador de recursos de Azure y la API de REST de Azure. Puede especificar el tipo de clúster (Hadoop, HBase o Storm) o usar scripts para instalar componentes personalizados..."
   	services="hdinsight"
   	documentationCenter=""
   	authors="Blackmist"
   	manager="paulettm"
   	editor="cgronlun"
	tags="azure-portal"/>

<tags
   	ms.service="hdinsight"
   	ms.devlang="na"
   	ms.topic="article"
   	ms.tgt_pltfrm="na"
   	ms.workload="big-data"
   	ms.date="02/29/2016"
   	ms.author="larryfr"/>

#Crear clústeres basados en Linux en HDInsight con la CLI de Azure

[AZURE.INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

La CLI de Azure es una utilidad de línea de comandos multiplataforma que permite administrar los servicios de Azure. Se puede usar, junto con las plantillas de administración de recursos de Azure para crear un clúster de HDInsight, junto con las cuentas de almacenamiento asociadas y otros servicios.

Las plantillas de Administración de recursos de Azure son documentos JSON que describen un __grupo de recursos__ y todos los recursos contenidos en él (por ejemplo, HDInsight). Este enfoque basado en plantillas permite definir todos los recursos que se necesitan para HDInsight en una sola plantilla y administrar los cambios realizados en el grupo en conjunto a través de __implementaciones__ que aplican los cambios al grupo.

Los pasos de este documento recorren el proceso de creación de un nuevo clúster de HDInsight mediante la CLI de Azure y una plantilla:

> [AZURE.IMPORTANT] Los pasos de este documento usan el número predeterminado de nodos de trabajo (4) para un clúster de HDInsight. Si planea crear más de 32 nodos de trabajo, en la creación de clústeres o cambiando el tamaño del clúster después de la creación, debe seleccionar un tamaño de nodo principal con al menos 8 núcleos y 14 GB de RAM.
>
> Para obtener más información acerca de los tamaños de nodo y los costos asociados, consulte [Precios de HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

##Requisitos previos

- **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- __CLI de Azure__ Para obtener información sobre la instalación de la CLI, consulte [Instalación de la CLI de Azure](../xplat-cli-install.md).

##Iniciar sesión en su suscripción de Azure

Siga los pasos que se documentan en [Conexión a una suscripción de Azure desde la interfaz de la línea de comandos de Azure (CLI de Azure)](../xplat-cli-connect.md) y conéctese a su suscripción con el método __login__.

##Crear un clúster

Los siguientes pasos se deben realizar desde un símbolo del sistema, el shell o la sesión de terminal después de instalar y configurar la CLI de Azure.

1. Use el siguiente comando para autenticarse en su suscripción de Azure:

        azure login

    Se le solicitará que escriba su nombre y contraseña. Si tiene varias suscripciones de Azure, puede usar `azure account set <subscriptionname>` para establecer la suscripción que usarán los comandos de la CLI de Azure.

3. Cambie al modo de Administrador de recursos de Azure con el siguiente comando:

        azure config mode arm

4. Cree una plantilla para el clúster de HDInsight. A continuación encontrará algunas plantillas de ejemplo básico:

    * [Clúster basado en Linux, con una clave SSH pública](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-publickey)
    * [Clúster basado en Linux, con una contraseña para la cuenta SSH](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password)

    Ambas plantillas también crean la cuenta de Almacenamiento de Azure predeterminada que usa HDInsight.

    Los archivos que va a necesitar son __azuredeploy.json__ y __azuredeploy.parameters.json__. Copie estos archivos localmente para continuar.

5. Abra el archivo __azuredeploy.parameters.json__ en un editor y especifique los valores de los elementos en la sección `parameters`:

    * __location__: el centro de datos que se crean los recursos. En la sección `location` del archivo __azuredeploy.json__ puede obtener una lista de ubicaciones permitidas.
    * __clusterName__: el nombre del clúster de HDInsight. Este nombre debe ser único o se producirá un error en la implementación.
    * __clusterStorageAccountName__: el nombre de la cuenta de Almacenamiento de Azure que se creará para el clúster de HDInsight. Este nombre debe ser único o se producirá un error en la implementación.
    * __clusterLoginPassword__: la contraseña del usuario del administrador de clúster. Debe ser una contraseña segura, ya que se usa para el acceso a sitios web y a los servicios REST del clúster.
    * __sshUserName__: el nombre del primer usuario SSH para crear para este clúster. SSH se usará para acceder de forma remota al clúster con esta cuenta.
    * __sshPublicKey__: si usa la plantilla que requiere una clave pública SSH, debe agregarla en esta línea. Para obtener más información sobre cómo generar claves públicas y trabajar con ellas, consulte los siguientes artículos:

        * [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Linux, Unix u OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
        * [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

    * __sshPassword__: si usa la plantilla que requiere una contraseña SSH, debe agregarla en esta línea.

    Una vez que haya terminado, guarde el archivo y ciérrelo.

5. Utilice la siguiente instrucción para crear un grupo de recursos vacío. Reemplace __RESOURCEGROUPNAME__ por el nombre que desea usar para este grupo. Reemplace __UBICACIÓN__ por el centro de datos que en el que quiere crear su clúster de HDInsight:

        azure group create RESOURCEGROUPNAME LOCATION

    > [AZURE.NOTE] Si el nombre de la ubicación contiene espacios, escríbalo entre comillas. Por ejemplo, "Centro-Sur de EE.UU."

6. Utilice el siguiente comando para crear la implementación inicial de este grupo de recursos. Reemplace __PATHTOTEMPLATE__ por la ruta de acceso al archivo de plantilla __azuredeploy.json__. Reemplace __PATHTOPARAMETERSFILE__ por la ruta de acceso al archivo de plantilla __azuredeploy.parameters.json__. Reemplace __RESOURCEGROUPNAME__ por el nombre del grupo que creó en el paso anterior:

        azure group deployment create -f PATHTOTEMPLATE -e PATHTOPARAMETERSFILE -g RESOURCEGROUPNAME -n InitialDeployment

    Una vez aceptada la implementación, debería ver un mensaje similar a `group deployment create command ok`.

7. La implementación puede tardar un tiempo en completarse, unos 15 minutos. Puede ver información acerca de la implementación con el siguiente comando. Reemplace __RESOURCEGROUPNAME__ por el nombre del grupo de recursos que se usó en el paso anterior:

        azure group log show -l RESOURCEGROUPNAME

    Cuando finalice la implementación, el campo __Estado__ contendrá el valor __Correcto__. Si se produce un error durante la implementación, puede obtener más información sobre dicho error con el siguiente comando

        azure group log show -l -v RESOURCEGROUPNAME

##Pasos siguientes

Ahora que ya creó un clúster de HDInsight correctamente, use lo siguiente para aprender a trabajar con el clúster:

###Clústeres Hadoop

* [Uso de Hive con HDInsight](hdinsight-use-hive.md)
* [Uso de Pig con HDInsight](hdinsight-use-pig.md)
* [Uso de MapReduce con HDInsight](hdinsight-use-mapreduce.md)

###Clústeres HBase

* [Introducción a HBase en HDInsight](hdinsight-hbase-tutorial-get-started-linux.md)
* [Desarrollo de aplicaciones de Java para HBase en HDInsight](hdinsight-hbase-build-java-maven-linux.md)

###Clústeres Storm

* [Desarrollo de topologías de Java para Storm en HDInsight](hdinsight-storm-develop-java-topology.md)
* [Uso de componentes de Python en Storm en HDInsight](hdinsight-storm-develop-python-topology.md)
* [Implementación y supervisión de topologías con Storm en HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)

<!---HONumber=AcomDC_0302_2016-->