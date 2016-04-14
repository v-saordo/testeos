<properties
   	pageTitle="Crear clústeres de Hadoop, HBase, Storm o Spark en Linux en HDInsight con la CLI de Azure | Microsoft Azure"
   	description="Aprenda a crear clústeres de Hadoop, HBase, Storm o Spark en Linux para HDInsight con la CLI de Azure."
   	services="hdinsight"
   	documentationCenter=""
   	authors="nitinme"
   	manager="paulettm"
   	editor="cgronlun"
	tags="azure-portal"/>

<tags
   	ms.service="hdinsight"
   	ms.devlang="na"
   	ms.topic="article"
   	ms.tgt_pltfrm="na"
   	ms.workload="big-data"
   	ms.date="12/08/2015"
   	ms.author="nitinme"/>

#Crear clústeres basados en Linux en HDInsight con Azure PowerShell

[AZURE.INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

Azure PowerShell es un potente entorno de scripting que puede usar para controlar y automatizar la implementación y la administración de sus cargas de trabajo en Azure. En este documento se ofrece información sobre cómo aprovisionar un clúster de HDInsight basado en Linux mediante Azure PowerShell, así como un script de ejemplo.

> [AZURE.NOTE] Azure PowerShell solo está disponible en clientes de Windows. Si está usando un cliente de Linux, Unix o Mac OS X, vea [Crear un clúster de HDInsight basado en Linux con la CLI de Azure](hdinsight-hadoop-create-linux-clusters-azure-cli.md) para obtener información sobre el uso de la CLI de Azure para crear un clúster.

###Requisitos previos

- **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- __Azure PowerShell__ Para obtener información sobre la configuración de una estación de trabajo para que ejecute cmdlets de HDInsight Windows PowerShell, consulte [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md). Para obtener más información sobre el uso de Azure PowerShell con HDInsight, consulte [Administración de HDInsight con PowerShell](hdinsight-administer-use-powershell.md). Para obtener una lista de los cmdlets de HDInsight Windows PowerShell, consulte [Documentación de referencia de los cmdlets de HDInsight](https://msdn.microsoft.com/library/azure/dn858087.aspx).

##Creación de clústeres

Los siguientes procedimientos son necesarios para aprovisionar un clúster de HDInsight con Azure PowerShell:

- Creación de un grupo de recursos de Azure
- Crear una cuenta de Almacenamiento de Azure
- Creación de un contenedor de blobs de Azure
- Creación de un clúster de HDInsight

Los dos parámetros más importantes que debe establecer para aprovisionar clústeres de Linux son donde especifica el tipo de sistema operativo y los detalles del usuario de SSH:

- Asegúrese de especificar el parámetro **-OSType** como **Linux**.
- Para usar SSH para las sesiones remotas en los clústeres, puede especificar la contraseña del usuario de SSH o la clave pública SSH. Si se especifica tanto la contraseña del usuario de SSH como la clave pública SSH, se omitirá la clave. Si desea usar la clave SSH en las sesiones remotas, debe especificar una contraseña SSH en blanco cuando se le solicite. Para obtener más información sobre el uso de SSH con HDInsight, vea uno de los siguientes artículos:
    
    * [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Linux, Unix u OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
    * [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

El script siguiente muestra cómo crear un nuevo clúster:

    ###########################################
    # Create required items, if none exist
    ###########################################

    # Sign in
    Add-AzureRmAccount

    # Select the subscription to use
    $subscriptionID = "<SubscriptionName>"        # Provide your Subscription Name
    Select-AzureRmSubscription -SubscriptionId $subscriptionID

    # Create an Azure Resource Group
    $resourceGroupName = "<ResourceGroupName>"      # Provide a Resource Group name
    $location = "<Location>"                        # For example, "West US"
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

    # Create an Azure Storage account
    $storageAccountName = "<StorageAcccountName>"   # Provide a Storage account name
    New-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -Location $location -Type Standard_GRS

    # Create an Azure Blob Storage container
    $containerName = "<ContainerName>"              # Provide a container name
    $storageAccountKey = Get-AzureRmStorageAccountKey -Name $storageAccountName -ResourceGroupName $resourceGroupName | %{ $_.Key1 }
    $destContext = New-AzureRmStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
    New-AzureRmStorageContainer -Name $containerName -Context $destContext

    ###########################################
    # Create an HDInsight Cluster
    ###########################################

    # Skip these variables if you just created them
    $resourceGroupName = "<ResourceGroupName>"      # Provide the Resource Group name
    $storageAccountName = "<StorageAcccountName>"   # Provide the Storage account name
    $containerName = "<ContainerName>"              # Provide the container name
    $storageAccountKey = Get-AzureStorageAccountKey -Name $storageAccountName -ResourceGroupName $resourceGroupName | %{ $_.Key1 }

    # Set these variables
    $clusterName = $containerName           		# As a best practice, have the same name for the cluster and container
    $clusterNodes = <ClusterSizeInNodes>    		# The number of nodes in the HDInsight cluster
    $credentials = Get-Credential
    $sshCredentials = Get-Credential

    # The location of the HDInsight cluster. It must be in the same data center as the Storage account.
    $location = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName | %{$_.Location}

    # Create a new HDInsight cluster
    New-AzureRmHDInsightCluster -ClusterName $clusterName -ResourceGroupName $resourceGroupName -HttpCredential $credentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName  -ClusterSizeInNodes $clusterNodes -ClusterType Hadoop -OSType Linux -Version "3.2" -SshCredential $sshCredentials

Los valores que se especifican para **$clusterCredentials** se usan para crear la cuenta de usuario de Hadoop para el clúster. Esta cuenta se usará para conectarse al clúster. Los valores que se especifican para **$sshCredentials** se usan para crear la cuenta de usuario de SSH para el clúster. Esta cuenta se usa para iniciar una sesión remota de SSH en el clúster y ejecutar trabajos.

> [AZURE.IMPORTANT] En este script, debe especificar el número de nodos de trabajo que se incluirán en el clúster. Si piensa usar más de 32 nodos de trabajo, en la creación de clústeres o cambiando el tamaño del clúster después de su creación, también debe especificar un tamaño de nodo principal con al menos 8 núcleos y 14 GB de RAM.
>
> Para obtener más información acerca de los tamaños de nodo y los costos asociados, consulte [Precios de HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

Puede tardar hasta 15 minutos en completarse el aprovisionamiento.

##Personalización de los clústeres

- Consulte [Customize HDInsight clusters using Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md#use-azure-powershell).
- Consulte [Personalización de clústeres de HDInsight mediante la acción de scripts (Windows)](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell).

##Pasos siguientes

Ahora que ya creó un clúster de HDInsight correctamente, use lo siguiente para aprender a trabajar con el clúster:

###Clústeres Hadoop

* [Uso de Hive con HDInsight](hdinsight-use-hive.md)
* [Uso de Pig con HDInsight](hdinsight-use-pig.md)
* [Uso de MapReduce con HDInsight](hdinsight-use-mapreduce.md)

###Clústeres HBase

* [Introducción a HBase en HDInsight](hdinsight-hbase-tutorial-get-started-linux.md)
* [Desarrollo de aplicaciones de Java para HBase en HDInsight](hdinsight-hbase-build-java-maven-linux)

###Clústeres Storm

* [Desarrollo de topologías de Java para Storm en HDInsight](hdinsight-storm-develop-java-topology.md)
* [Uso de componentes de Python en Storm en HDInsight](hdinsight-storm-develop-python-topology.md)
* [Implementación y supervisión de topologías con Storm en HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)

###Clústeres de Spark

* [Crear una aplicación independiente con Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Ejecutar trabajos de forma remota en un clúster de Spark mediante Livy](hdinsight-apache-spark-livy-rest-interface.md)
* [Spark with BI: Realizar el análisis de datos interactivos con Spark en HDInsight con las herramientas de BI](hdinsight-apache-spark-use-bi-tools.md)
* [Spark con aprendizaje automático: uso de Spark en HDInsight para predecir los resultados de la inspección de alimentos](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Streaming con Spark: uso de Spark en HDInsight para compilar aplicaciones de streaming en tiempo real](hdinsight-apache-spark-eventhub-streaming.md)

<!---HONumber=AcomDC_0224_2016-->