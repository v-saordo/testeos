<properties
	pageTitle="Administración de clústeres de Hadoop en HDInsight con Azure PowerShell | Microsoft Azure"
	description="Vea cómo realizar tareas administrativas para clústeres de Hadoop en HDInsight usando Azure PowerShell."
	services="hdinsight"
	editor="cgronlun"
	manager="paulettm"
	tags="azure-portal"
	authors="mumian"
	documentationCenter=""/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/04/2016"
	ms.author="jgao"/>

# Administración de clústeres de Hadoop en HDInsight con PowerShell de Azure

[AZURE.INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Azure PowerShell es un potente entorno de scripting que puede usar para controlar y automatizar la implementación y la administración de sus cargas de trabajo en Azure. En este artículo, aprenderá a administrar clústeres de Hadoop en HDInsight de Azure con una consola local de PowerShell de Azure mediante el uso de Windows PowerShell. Para obtener más información acerca de los cmdlets de PowerShell de HDInsight, consulte [Documentación de referencia de los cmdlets de HDInsight][hdinsight-powershell-reference].



**Requisitos previos**

Antes de empezar este artículo, debe tener lo siguiente:

- **Una suscripción de Azure**. Consulte [Obtención de una versión de evaluación gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

##Instalar Azure PowerShell 1.0 y versiones posteriores

Primero debe desinstalar las versiones 0.9x.

Para comprobar la versión del PowerShell instalado:

	Get-Module *azure*
	
Para desinstalar la versión anterior, ejecute Programas y características en el panel de control.

Hay dos opciones principales para instalar Azure PowerShell.

- [Galería de PowerShell](https://www.powershellgallery.com/). Ejecute los siguientes comandos en el ISE de PowerShell con privilegios elevados o en la consola de Windows PowerShell con privilegios elevados:

		# Install the Azure Resource Manager modules from PowerShell Gallery
		Install-Module AzureRM
		Install-AzureRM
		
		# Install the Azure Service Management module from PowerShell Gallery
		Install-Module Azure
		
		# Import AzureRM modules for the given version manifest in the AzureRM module
		Import-AzureRM
		
		# Import Azure Service Management module
		Import-Module Azure

	Para más información, vea [Galería de PowerShell](https://www.powershellgallery.com/).

- [Instalador de plataforma web de Microsoft (WebPI)](http://aka.ms/webpi-azps). Si tiene Azure PowerShell 0.9.x instalado, se le pedirá que desinstale 0.9.x. Si ha instalado módulos de Azure PowerShell desde la Galería de PowerShell, el programa de instalación requiere que los módulos se quiten antes de la instalación para garantizar un entorno coherente de PowerShell de Azure. Para instrucciones, consulte [Instalación de Azure PowerShell 1.0 mediante WebPI](https://azure.microsoft.com/blog/azps-1-0/).

WebPI recibirá actualizaciones mensuales. La Galería de PowerShell recibirá actualizaciones de forma continua. Si se encuentra cómodo con la instalación de la Galería de PowerShell, ese será el primer canal para lo mejor y lo más reciente de Azure PowerShell.

##Creación de clústeres

El clúster de HDInsight requiere un grupo de recursos de Azure y un contenedor de blobs en una cuenta de almacenamiento de Azure:

- Un grupo de recursos de Azure es un contenedor lógico para los recursos de Azure. El grupo de recursos de Azure y el clúster de HDInsight no tienen que estar en la misma ubicación. Para obtener más información, vea [Uso de Azure PowerShell con el Administrador de recursos de Azure](../powershell-azure-resource-manager.md).
- HDInsight usa un contenedor de blobs de una cuenta de almacenamiento de Azure como sistema de archivos predeterminado. Es necesario tener una cuenta de Almacenamiento de Azure y un contenedor de almacenamiento antes de crear un clúster de HDInsight. La cuenta de almacenamiento predeterminada y el clúster de HDInsight deben estar en la misma ubicación.

[AZURE.INCLUDE [provisioningnote](../../includes/hdinsight-provisioning.md)]

**Para conectarse a Azure**

	Login-AzureRmAccount
	Get-AzureRmSubscription  # list your subscriptions and get your subscription ID
	Select-AzureRmSubscription -SubscriptionId "<Your Azure Subscription ID>"

**Select-AzureRMSubscription** se llama en caso de tener varias suscripciones.
	
**Para crear un nuevo grupo de recursos**

	New-AzureRmResourceGroup -name <New Azure Resource Group Name> -Location "<Azure Location>"  # For example, "EAST US 2"

**Para crear una cuenta de almacenamiento de Azure**

	New-AzureRmStorageAccount -ResourceGroupName <Azure Resource Group Name> -Name <Azure Storage Account Name> -Location "<Azure Location>" -Type <AccountType> # account type example: Standard_LRS for zero redundancy storage
	
	Don't use **Standard_ZRS** because it deson't support Azure Table.  HDInsight uses Azure Table to logging. For a full list of the storage account types, see [https://msdn.microsoft.com/library/azure/hh264518.aspx](https://msdn.microsoft.com/library/azure/hh264518.aspx).

[AZURE.INCLUDE [data center list](../../includes/hdinsight-pricing-data-centers-clusters.md)]


Para información sobre la creación de una cuenta de almacenamiento de Azure mediante el Portal de Azure, vea [Acerca de las cuentas de almacenamiento de Azure](../storage/storage-create-storage-account.md).

Si ya tiene una cuenta de Almacenamiento pero no sabe su nombre ni su clave, puede usar los comandos siguientes para recuperar dicha información:

	# List Storage accounts for the current subscription
	Get-AzureRmStorageAccount
	# List the keys for a Storage account
	Get-AzureRmStorageAccountKey -ResourceGroupName <Azure Resource Group Name> -name $storageAccountName <Azure Storage Account Name>

Para saber cómo obtener la información mediante el Portal, vea la sección "Vista, copia y regeneración de las claves de acceso de almacenamiento" de [Sobre las cuentas de almacenamiento de Azure](../storage/storage-create-storage-account.md).

**Para crear un contenedor de almacenamiento de Azure**

Azure PowerShell no puede crear un contenedor de blobs durante el proceso de aprovisionamiento de HDInsight. Puede crear uno con el siguiente script:

	$resourceGroupName = "<AzureResoureGroupName>"
	$storageAccountName = "<Azure Storage Account Name>"
	$storageAccountKey = Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccount |  %{ $_.Key1 }
	$containerName="<AzureBlobContainerName>"

	# Create a storage context object
	$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

	# Create a Blob storage container
	New-AzureStorageContainer -Name $containerName -Context $destContext

**Para crear un clúster**

Cuando tenga preparados la cuenta de almacenamiento y el contenedor de blobs, podrá proceder con la creación de un clúster.

	$resourceGroupName = "<AzureResoureGroupName>"

	$storageAccountName = "<Azure Storage Account Name>"
	$containerName = "<AzureBlobContainerName>"

	$clusterName = "<HDInsightClusterName>"
	$location = "<AzureDataCenter>"
	$clusterNodes = <ClusterSizeInNodes>

	# Get the Storage account key
	$storageAccountKey = Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName | %{ $_.Key1 }

	# Create a new HDInsight cluster
	New-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName `
		-ClusterName $clusterName `
		-Location $location `
		-DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
		-DefaultStorageAccountKey $storageAccountKey `
		-DefaultStorageContainer $containerName  `
		-ClusterSizeInNodes $clusterNodes

##Enumeración de clústeres
Use el comando siguiente para enumerar todos los clústeres en la suscripción actual:

	Get-AzureRmHDInsightCluster

##Presentación de clústeres

Use el comando siguiente para mostrar los detalles de un clúster específico en la suscripción actual:

	Get-AzureRmHDInsightCluster -ClusterName <Cluster Name>

##Eliminación de clústeres
Use el comando siguiente para eliminar un clúster:

	Remove-AzureRmHDInsightCluster -ClusterName <Cluster Name>

##Escalado de clústeres
La característica de escalado de clústeres permite cambiar la cantidad de nodos de trabajo que usa un clúster que se ejecuta en HDInsight de Azure sin necesidad de volver a crear el clúster.

>[AZURE.NOTE] Solo son compatibles los clústeres con la versión 3.1.3 de HDInsight, o superior. Si no está seguro de la versión del clúster, puede comprobar la página de propiedades. Consulte [Familiarizarse con la interfaz del portal del clúster](hdinsight-adminster-use-management-portal/#Get-familiar-with-the-cluster-portal-interface).

A continuación se muestra el efecto que tiene cambiar la cantidad de nodos de datos de cada tipo de clúster compatible con HDInsight:

- Hadoop

	Puede aumentar sin ningún problema la cantidad de nodos de trabajo en un clúster de Hadoop que se encuentre en ejecución, sin que afecte a ningún trabajo pendiente o en ejecución. También se pueden enviar trabajos nuevos mientras la operación está en curso. Los errores que puedan surgir en una operación de escalado se enfrentan oportunamente, por lo que el clúster siempre queda en estado funcional.

	Cuando se realiza la reducción vertical de un clúster de Hadoop al disminuir la cantidad de nodos de datos, se reinician algunos de los servicios del clúster. Esto provoca que todos los trabajos pendientes y en ejecución fallen al completarse la operación de escalado. Sin embargo, puede volver a enviar los trabajos una vez finalizada la operación.

- HBase

	Puede agregar nodos sin problemas al clúster de HBase mientras se encuentra en ejecución, así como eliminarlos. Los servidores regionales se equilibran automáticamente en unos pocos minutos tras completar la operación de escalado. Sin embargo, puede equilibrar manualmente los servidores regionales iniciando sesión en el nodo principal del clúster y ejecutando los comandos siguientes desde una ventana del símbolo del sistema:

		>pushd %HBASE_HOME%\bin
		>hbase shell
		>balancer

	Para obtener más información sobre el uso del shell HBase, consulte
- Storm

	Puede agregar o quitar sin problemas nodos de datos de su clúster de Storm mientras se encuentra en ejecución. Sin embargo, después de finalizar correctamente la operación de escalado, deberá volver a equilibrar la topología.

	Esto se puede realizar de dos formas:

	* La interfaz de usuario web de Storm
	* La herramienta de la interfaz de línea de comandos (CLI)

	Consulte la [documentación de Apache Storm](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) para obtener más detalles.

	La interfaz de usuario web de Storm se encuentra disponible en el clúster de HDInsight:

	![reequilibrio de escalado de storm de hdinsight](./media/hdinsight-administer-use-management-portal/hdinsight.portal.scale.cluster.storm.rebalance.png)

	El siguiente es un ejemplo de cómo usar el comando CLI para volver a equilibrar la topología de Storm:

		## Reconfigure the topology "mytopology" to use 5 worker processes,
		## the spout "blue-spout" to use 3 executors, and
		## the bolt "yellow-bolt" to use 10 executors

		$ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10

Para cambiar el tamaño del clúster de Hadoop con Azure PowerShell, ejecute el siguiente comando desde un equipo cliente:

	Set-AzureRmHDInsightClusterSize -ClusterName <Cluster Name> -TargetInstanceCount <NewSize>
	

##Concesión o revocación del acceso

Los clústeres de HDInsight tienen los siguientes servicios web HTTP (todos estos servicios tienen extremos RESTful):

- ODBC
- JDBC
- Ambari
- Oozie
- Templeton


De manera predeterminada, estos servicios se conceden para el acceso. Puede revocar/conceder el acceso. Para revocar:

	Revoke-AzureRmHDInsightHttpServicesAccess -ClusterName <Cluster Name>

Para conceder:

	$clusterName = "<HDInsight Cluster Name>"

	# Credential option 1
	$hadoopUserName = "admin"
	$hadoopUserPassword = "<Enter the Password>"
	$hadoopUserPW = ConvertTo-SecureString -String $hadoopUserPassword -AsPlainText -Force
	$credential = New-Object System.Management.Automation.PSCredential($hadoopUserName,$hadoopUserPW)

	# Credential option 2
	#$credential = Get-Credential -Message "Enter the HTTP username and password:" -UserName "admin"
	
	Grant-AzureRmHDInsightHttpServicesAccess -ClusterName $clusterName -HttpCredential $credential

>[AZURE.NOTE] Al conceder/revocar el acceso, restablecerá el nombre de usuario y la contraseña del clúster.

Esto también se puede hacer a través del Portal. Vea [Administración de HDInsight mediante el Portal de Azure][hdinsight-admin-portal].

##Actualización de las credenciales de usuario HTTP

Es el mismo procedimiento que [Concesión o revocación del acceso HTTP](#grant/revoke-access). Si al clúster se le ha concedido el acceso HTTP, primero debe revocarlo. Y después conceda el acceso con nuevas credenciales de usuario HTTP.


##Búsqueda de la cuenta de almacenamiento predeterminada

El siguiente script de Powershell muestra cómo obtener el nombre y la clave de la cuenta de almacenamiento predeterminada para un clúster.

	$clusterName = "<HDInsight Cluster Name>"
	
	$cluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
	$resourceGroupName = $cluster.ResourceGroup
	$defaultStorageAccountName = ($cluster.DefaultStorageAccount).Replace(".blob.core.windows.net", "")
	$defaultBlobContainerName = $cluster.DefaultStorageContainer
	$defaultStorageAccountKey = Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName |  %{ $_.Key1 }
	$defaultStorageAccountContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey 

##Búsqueda del grupo de recursos

En el modo ARM, cada clúster de HDInsight pertenece a un grupo de recursos de Azure. Para buscar el grupo de recursos:

	$clusterName = "<HDInsight Cluster Name>"
	
	$cluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
	$resourceGroupName = $cluster.ResourceGroup


##Envío de trabajos

**Para enviar trabajos de MapReduce**

Vea [Ejecución de ejemplos de Hadoop MapReduce en HDInsight basado en Windows](hdinsight-run-samples.md).

**Para enviar trabajos de Hive**

Vea [Ejecución de consultas de Hive con PowerShell](hdinsight-hadoop-use-hive-powershell.md).

**Para enviar trabajos de Pigs**

Vea [Ejecución de trabajos de Pig mediante PowerShell](hdinsight-hadoop-use-pig-powershell.md).

**Para enviar trabajos de Sqoop**

Consulte [Uso de Sqoop con HDInsight](hdinsight-use-sqoop.md).

**Para enviar trabajos de Oozie**

Vea [Uso de Oozie con Hadoop para definir y ejecutar un flujo de trabajo en HDInsight](hdinsight-use-oozie.md).

##Carga de archivos de datos al almacenamiento de blobs de Azure
Consulte [Carga de datos en HDInsight][hdinsight-upload-data].


## Otras referencias
* [Documentación de referencia de los cmdlets de HDInsight][hdinsight-powershell-reference]
* [Administración de HDInsight mediante el Portal de Azure][hdinsight-admin-portal]
* [Administración de HDInsight con la interfaz de línea de comandos][hdinsight-admin-cli]
* [Creación de clústeres de HDInsight][hdinsight-provision]
* [Carga de datos en HDInsight][hdinsight-upload-data]
* [Envío de trabajos de Hadoop mediante programación][hdinsight-submit-jobs]
* [Introducción a HDInsight de Azure][hdinsight-get-started]


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-provision-custom-options]: hdinsight-provision-clusters.md#configuration
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-flight]: hdinsight-analyze-flight-delay-data.md

[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx

[powershell-install-configure]: powershell-install-configure.md

[image-hdi-ps-provision]: ./media/hdinsight-administer-use-powershell/HDI.PS.Provision.png

<!---HONumber=AcomDC_0218_2016-->