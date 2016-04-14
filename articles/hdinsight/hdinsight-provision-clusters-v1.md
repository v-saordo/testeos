<properties 
   pageTitle="Aprovisionamiento personalizado de clústeres de Hadoop en HDInsight | Microsoft Azure" 
   description="Obtenga información sobre cómo aprovisionar clústeres de manera personalizada para HDInsight de Azure mediante el Portal de Azure clásico, Azure PowerShell, una línea de comandos o el SDK para .NET." 
   services="hdinsight" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="01/28/2016"
   ms.author="jgao"/>

#Aprovisionamiento de clústeres de Hadoop en HDInsight

Aprenda a planear el aprovisionamiento de los clústeres de HDInsight.

[AZURE.INCLUDE [hdinsight-azure-portal](../../includes/hdinsight-azure-portal.md)]

* [Aprovisionamiento de clústeres de Hadoop en HDInsight](hdinsight-provision-clusters.md) 

**Requisitos previos:**

Antes de empezar las instrucciones de este artículo, debe tener lo siguiente:

- Una suscripción de Azure. Consulte [How to get Azure Free trial for testing Hadoop in HDInsight (Obtención de una versión de prueba gratuita de Azure para probar Hadoop en HDInsight)](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).


## Opciones de configuración básica


- **Nombre del clúster**

	El nombre del clúster se utiliza para identificar un clúster. El nombre de clúster debe seguir las siguientes directrices:
	
	- El campo debe ser una cadena que contenga entre 3 y 63 caracteres
	- El campo solo puede contener letras, números y guiones.

- **Nombre de la suscripción**

	Un clúster de HDInsight está asociado a una suscripción a Azure.
 
- **Sistema operativos**

	Puede aprovisionar clústeres de HDInsight en uno de los dos sistemas operativos siguientes:
	- **HDInsight en Windows (Windows Server 2012 R2 Datacenter)**:
	- **HDInsight en Linux (Ubuntu 12.04 LTS para Linux)**: HDInsight proporciona la opción de configurar clústeres de Linux en Azure. Configure un clúster de Linux si conoce Linux o Unix, migrando desde una solución existente de Hadoop basado en Linux, o si desea una integración fácil con componentes del ecosistema de Hadoop creados para Linux. Para obtener más información, vea [Introducción a Hadoop en Linux en HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md). 


- **Versión de HDInsight**

	Se utiliza para determinar la versión de HDInsight que se utilizará para este clúster. Para obtener más información, consulte [Componentes y versiones de clústeres de Hadoop en HDInsight](https://go.microsoft.com/fwLink/?LinkID=320896&clcid=0x409)

- **Tipo de clúster** y **tamaño del clúster (también conocidos como nodos de datos)**

	HDInsight permite a los clientes implementar varios tipos de clúster para diferentes cargas de trabajo de análisis de datos. Los tipos de clústeres que se ofrecen hoy son:
	
	- Clústeres de Hadoop: para cargas de trabajo de consulta y análisis
	- Clústeres de HBase: para cargas de trabajo NoSQL
	- Clústeres de Storm: para cargas de trabajo de procesamiento de eventos en tiempo real
	- Clústeres de Spark: para procesamiento en memoria, consultas interactivas, transmisiones y cargas de trabajo de aprendizaje automático

	![Clústeres de HDInsight](./media/hdinsight-provision-clusters-v1/hdinsight.clusters.png)
 
	> [AZURE.NOTE] El *clúster de HDInsight de Azure* se llama también *clúster de Hadoop en HDInsight* o *clúster de HDInsight*. En algunas ocasiones, se usa indistintamente con el *clúster de Hadoop*. Todos estos nombres se refieren a los clústeres de Hadoop hospedados en el entorno de Microsoft Azure.

	Dentro de un tipo de clúster determinado, existen distintos roles para los diversos nodos, los que permiten que un cliente dimensione esos nodos en un rol determinado según los detalles de su carga de trabajo. Por ejemplo, un clúster de Hadoop puede aprovisionar sus nodos de trabajo con una gran cantidad de memoria si el tipo de análisis que se realiza requiere mucha memoria.

	![Roles de clúster de Hadoop en HDInsight](./media/hdinsight-provision-clusters-v1/HDInsight.Hadoop.roles.png)

	Los clústeres de Hadoop para HDInsight se implementan con dos roles:

	- Nodo principal (2 nodos)
	- Nodo de datos (al menos 1 nodo)

	![Roles de clúster de Hadoop en HDInsight](./media/hdinsight-provision-clusters-v1/HDInsight.HBase.roles.png)

	Los clústeres de HBase para HDInsight se implementan con tres roles:
	- Servidores principales (2 nodos)
	- Servidores de región (al menos 1 nodo)
	- Nodos maestro o Zookeeper (3 nodos)
	
	![Roles de clúster de Hadoop en HDInsight](./media/hdinsight-provision-clusters-v1/HDInsight.Storm.roles.png)

	Los clústeres de Storm para HDInsight se implementan con tres roles:
	- Nodos Nimbus (2 nodos)
	- Servidores de supervisor (al menos 1 nodo)
	- Nodos Zookeeper (3 nodos)
	

	![Roles de clúster de Hadoop en HDInsight](./media/hdinsight-provision-clusters-v1/HDInsight.Spark.roles.png)

	Los clústeres Spark para HDInsight se implementan con tres roles:
	- Nodo principal (2 nodos)
	- Nodo de trabajo (al menos 1 nodo)
	- Nodos Zookeeper (3 nodos) (gratis para nodos Zookeeper A1)

	El uso de esos nodos se factura a los clientes por la duración del clúster. La facturación comienza una vez que se crea un clúster y se detiene cuando se elimina el clúster (no es posible eliminar la asignación de los clústeres ni tampoco se pueden poner en espera) El tamaño del clúster afecta el precio del mismo. Con fines de aprendizaje, se recomienda utilizar 1 nodo de datos. Para más información sobre los precios de HDInsight, vea [HDInsight Precios](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).


	>[AZURE.NOTE] El límite de tamaño del clúster varía entre las suscripciones a Azure. Póngase en contacto con el servicio de soporte relacionado con la facturación para aumentar el límite.
	
- **Región/red virtual (conocida también como ubicación)**

	![Regiones de Azure](./media/hdinsight-provision-clusters-v1/Azure.regions.png)

	Si desea obtener una lista de las regiones admitidas, haga clic en la lista desplegable **Región** en los [precios de HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

- **Tamaño del nodo**

	![tamaños de nodos de vm de hdinsight](./media/hdinsight-provision-clusters-v1/hdinsight.node.sizes.png)

	Seleccione el tamaño de VM para los nodos. Para obtener más información, consulte [Tamaños de los servicios en la nube](cloud-services-sizes-specs.md).

	En función de la elección de las máquinas virtuales, el costo puede variar. HDInsight usa todas las máquinas virtuales de nivel estándar para los nodos del clúster. Para obtener información sobre cómo afectan los tamaños de máquinas virtuales a los precios, consulte <a href="http://azure.microsoft.com/pricing/details/hdinsight/" target="_blank">Precios de HDInsight</a>.


- **Usuarios de HDInsight**

	Los clústeres de HDInsight le permiten configurar dos cuentas de usuario durante el aprovisionamiento:

	- Usuario de HTTP. El nombre de usuario predeterminado es admin durante la configuración básica en el Portal de Azure clásico.
	- Usuario de RDP (clústeres de Windows): se usa para conectarse al clúster mediante RDP. Cuando crea la cuenta, debe establecer una fecha de caducidad que se encuentre dentro de 90 días a contar del día de hoy. 
	- Usuario SSH (clústeres de Linux): se usa para conectarse al clúster mediante SSH. Puede crear cuentas de usuario SSH adicional una vez que se cree el clúster siguiendo los pasos en [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Linux, Unix u OS X](hdinsight-hadoop-linux-use-ssh-unix.md).
  
 

- **Cuenta de Almacenamiento de Azure**


	El HDFS original usa muchos discos locales en el clúster. En su lugar, HDInsight utiliza el almacenamiento de blobs de Azure para el almacenamiento de datos. El almacenamiento de blobs de Azure es una solución de almacenamiento sólida y de uso general, que se integra sin problemas con HDInsight. A través de una interfaz del sistema de archivos distribuidos de Hadoop (HDFS), el conjunto completo de componentes de HDInsight puede operar directamente en datos estructurados o no estructurados en el almacenamiento de blobs. Almacenar los datos en un almacenamiento de blobs hace que elimine de forma segura los clústeres de HDInsight que se usan para los cálculos sin perder los datos del usuario.
	
	Durante la configuración, debe especificar una cuenta de almacenamiento de Azure y un contenedor de almacenamiento de blobs de Azure en la cuenta de almacenamiento de Azure. Algunos de los procesos de aprovisionamiento requieren que la cuenta de almacenamiento de Azure y el contenedor de almacenamiento de blobs se creen con antelación. El clúster utiliza el contenedor de almacenamiento de blobs como la ubicación de almacenamiento predeterminada. De manera opcional, puede especificar cuentas de almacenamiento de Azure adicionales (almacenamiento vinculado) al que podrá tener acceso el clúster. Además, el clúster también podrá tener acceso a cualquier contenedor de blobs que esté configurado con acceso de lectura público completo o con acceso de lectura público solo para blobs. Para obtener más información sobre el acceso restringido, consulte [Administración del acceso a los recursos de almacenamiento de Azure](storage-manage-access-to-resources.md).

	![Almacenamiento de HDInsight](./media/hdinsight-provision-clusters-v1/HDInsight.storage.png)
	
	>[AZURE.NOTE] Un contenedor de almacenamiento de blobs proporciona una agrupación de un conjunto de blobs, tal como se muestra en la imagen:
	
	![Almacenamiento de blobs de Azure](./media/hdinsight-provision-clusters-v1/Azure.blob.storage.jpg)
	  
	
	>[AZURE.WARNING] No comparta un contenedor de almacenamiento de blobs para varios clústeres, ya que no es compatible.
	
	Para obtener más información sobre el uso de almacenes de blobs secundarios, consulte [Uso del almacenamiento de blobs de Azure con HDInsight](hdinsight-hadoop-use-blob-storage.md).

- **Tienda de metadatos Hive/Oozie**

	La tienda de metadatos contiene metadatos de Hive y Oozie, como columnas, esquemas, particiones y tablas de Hive. El uso de la tienda de metadatos le ayuda a conservar sus metadatos de Hive y Oozie, por lo que no es necesario volver a crear tablas de Hive o trabajos de Oozie al aprovisionar un nuevo clúster. De forma predeterminada, Hive utiliza una base de datos SQL de Azure incrustada para almacenar esta información. La base de datos incrustada no puede conservar los metadatos cuando se elimina el clúster. Por ejemplo, tiene un clúster aprovisionado con una tienda de metadatos de Hive. Creó algunas tablas de Hive. Después de eliminar el clúster y de volverlo a crear con la misma tienda de metadatos de Hive, podrá ver las tablas de Hive que creó en el clúster original.

## Opciones de configuración avanzada

>[AZURE.NOTE] Esta sección se aplica actualmente solo a los clústeres de HDInsight basado en Windows.

### Personalización de clústeres mediante la personalización de clústeres de HDInsight

A veces desea configurar los archivos de configuración. Estos son algunos de ellos.

- core-site.xml
- hdfs-site.xml
- mapred-site.xml
- yarn-site.xml
- hive-site.xml
- oozie-site.xml

Los clústeres no pueden conservar los cambios debido a la recreación de imágenes. Para obtener más información, consulte [Role Instance Restarts Due to OS Upgrades](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx). Para mantener los cambios por toda la duración de los clústeres, puede utilizar la personalización de clústeres de HDInsight durante el proceso de aprovisionamiento.

El siguiente es un ejemplo de script de Azure PowerShell sobre cómo personalizar una configuración de Hive:

	# hive-site.xml configuration 
	$hiveConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightHiveConfiguration'
	$hiveConfigValues.Configuration = @{ "hive.metastore.client.socket.timeout"="90" } #default 60
	
	$config = New-AzureHDInsightClusterConfig `
	            -ClusterSizeInNodes $clusterSizeInNodes `
	            -ClusterType $clusterType `
	          | Set-AzureHDInsightDefaultStorage `
	            -StorageAccountName $defaultStorageAccount `
	            -StorageAccountKey $defaultStorageAccountKey `
	            -StorageContainerName $defaultBlobContainer `
	          | Add-AzureHDInsightConfigValues `
	            -Hive $hiveConfigValues 
	
	New-AzureHDInsightCluster -Name $clusterName -Location $location -Credential $credential -OSType Windows -Config $config

Otros ejemplos de cómo personalizar otros archivos de configuración:

	# hdfs-site.xml configuration
	$HdfsConfigValues = @{ "dfs.blocksize"="64m" } #default is 128MB in HDI 3.0 and 256MB in HDI 2.1
	
	# core-site.xml configuration
	$CoreConfigValues = @{ "ipc.client.connect.max.retries"="60" } #default 50
	
	# mapred-site.xml configuration
	$MapRedConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightMapReduceConfiguration'
	$MapRedConfigValues.Configuration = @{ "mapreduce.task.timeout"="1200000" } #default 600000
	
	# oozie-site.xml configuration
	$OozieConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightOozieConfiguration'
	$OozieConfigValues.Configuration = @{ "oozie.service.coord.normal.default.timeout"="150" }  # default 120
	
Para obtener más información, consulte el blog de Azim Uddin titulado [Personalización del aprovisionamiento de clústeres de HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/04/15/customizing-hdinsight-cluster-provisioning-via-powershell-and-net-sdk.aspx).




### Personalización de clústeres mediante la acción de script

Puede instalar componentes adicionales o personalizar la configuración del clúster mediante secuencias de comandos durante el aprovisionamiento. Tales scripts se invocan mediante la opción de **Acción de script**, una opción de configuración que se puede usar a partir de los cmdlets de Windows PowerShell de HDInsight, en el Portal de Azure clásico o el SDK de .NET para HDInsight. Para obtener más información, consulte [Personalización de un clúster de HDInsight mediante la acción de script](hdinsight-hadoop-customize-cluster.md).


### Uso de redes virtuales de Azure

[Red virtual de Azure](https://azure.microsoft.com/documentation/services/virtual-network/) permite crear una red segura y persistente que contenga los recursos necesarios para una solución. Una red virtual permite hacer lo siguiente:

* Conectar recursos en la nube en una red privada (solo en la nube)

	![Diagrama de la configuración solo en la nube](./media/hdinsight-provision-clusters-v1/hdinsight-vnet-cloud-only.png)

* Conectar sus recursos en la nube a la red de su centro de datos local (sitio a sitio o punto a sitio) usando una red privada virtual (VPN).

	La configuración sitio a sitio permite conectar varios recursos de su centro de datos a la red virtual de Azure usando una VPN de hardware o el servicio de enrutamiento y acceso remoto.

	![Diagrama de la configuración de sitio a sitio](./media/hdinsight-provision-clusters-v1/hdinsight-vnet-site-to-site.png)

	La configuración punto a sitio permite conectar un recurso específico a la red virtual de Azure usando una VPN de software

	![Diagrama de la configuración de punto a sitio](./media/hdinsight-provision-clusters-v1/hdinsight-vnet-point-to-site.png)

Para más información sobre el uso de HDInsight con una red virtual, como los requisitos de configuración específicos de la red virtual, consulte [Extensión de las funcionalidades de HDInsight con Red virtual de Azure](hdinsight-extend-hadoop-virtual-network.md).

## Herramientas de aprovisionamiento

- El Portal de Azure clásico
- Azure PowerShell
- .NET SDK
- CLI

### Usar el Portal de Azure clásico

Puede revisar la sección [opciones de configuración básica] y la sección [opciones de configuración avanzada] para ver las explicaciones de los campos.

**Para crear un clúster de HDInsight con la opción de creación personalizada**

1. Inicie sesión en el [Portal de Azure clásico][azure-management-portal].
2. Haga clic en **+ NUEVO** en la parte inferior de la página y después en **SERVICIOS DE DATOS**, **HDINSIGHT** y **CREACIÓN PERSONALIZADA**.
3. En la página **Detalles del clúster**, escriba o elija los valores siguientes:

	- Cluster Name
	- Subscription Name
	- Tipo de clúster
	- Sistema operativo
	- Versión de HDInsight

4. En la página **Configurar clúster** escriba o seleccione los valores siguientes:

	- Nodos de datos
	- Región/red virtual
	- Tamaño de nodo principal
	- Tamaño de nodo de datos

5. En la página **Configurar usuario de clúster** proporcione los siguientes valores:

	- Nombre de usuario HTTP
	- Contraseña HTTP/Confirmar contraseña
	- Habilitar Escritorio remoto para un clúster
	- Especificar la tienda de metadatos de Hive/Oozie

6. Si elige especificar la tienda de metadatos de Hive/Oozie en la pantalla anterior, en la página **Configurar la tienda de metadatos de Hive/Oozie**, indique los valores siguientes:

	- Base de datos de tienda de metadatos de Hive
	- Usuario de la base de datos
	- Contraseña del usuario de la base de datos
	- Base de datos de tienda de metadatos de Oozie
	- Usuario de la base de datos
	- Contraseña del usuario de la base de datos

7. En la página **Cuenta de almacenamiento**, proporcione los siguientes valores:

	- Cuenta de almacenamiento
	- Nombre de cuenta
	- Contenedor predeterminado
	- Cuentas de almacenamiento adicionales

8. En la página **Acciones de scripts**, haga clic en **agregar acción de script** para proporcionar información detallada sobre el script personalizado que desea ejecutar para personalizar un clúster durante su creación. Para obtener más información, consulte [Personalización de un clúster de HDInsight mediante la acción de script](hdinsight-hadoop-customize-cluster.md).

	- Nombre: especifique un nombre para la acción de script
	- URI de script: especifique el Identificador uniforme de recursos (URI) al script que se ha invocado para personalizar el clúster.
	- Tipo de nodo: especifique los nodos en los que se ejecuta el script de personalización. Puede elegir <b>Todos los nodos</b>, <b>Solo nodos principales</b> o <b>Solo nodos de datos</b>.
	- Parámetros: especifique los parámetros, si lo requiere el script.</td></tr>

	Puede agregar más de una acción de script para instalar varios componentes en el clúster. Una vez agregados los scripts, haga clic en la marca de verificación para iniciar el aprovisionamiento del clúster.

### Uso de Azure PowerShell
Azure PowerShell es un potente entorno de scripting que puede usar para controlar y automatizar la implementación y la administración de sus cargas de trabajo en Azure. Esta sección proporciona instrucciones sobre cómo aprovisionar un clúster de HDInsight con Azure PowerShell. Para obtener información sobre la configuración de una estación de trabajo para que ejecute cmdlets de HDInsight Windows PowerShell, consulte [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md). Para obtener más información sobre el uso de Azure PowerShell con HDInsight, consulte [Administración de HDInsight con PowerShell](hdinsight-administer-use-powershell.md). Para obtener una lista de los cmdlets de HDInsight Windows PowerShell, consulte [Documentación de referencia de los cmdlets de HDInsight](https://msdn.microsoft.com/library/azure/dn858087.aspx).

> [AZURE.NOTE] Si bien los scripts de esta sección se pueden usar para configurar un clúster de HDInsight en una red virtual de Azure, no crearán una red virtual de Azure. Para obtener información sobre la creación de una red virtual de Azure, consulte [Tareas de configuración de la red virtual](../services/virtual-machines/).

Los siguientes procedimientos son necesarios para aprovisionar un clúster de HDInsight con Azure PowerShell:

- Crear una cuenta de Almacenamiento de Azure
- Creación de un contenedor de blobs de Azure
- Creación de un clúster de HDInsight

Puede usar la consola de Windows PowerShell o el entorno de scripting integrado (ISE) de Windows PowerShell para ejecutar los scripts.

HDInsight utiliza contenedores de almacenamiento de blobs de Azure como sistemas de archivos predeterminados. Es preciso tener una cuenta de almacenamiento de Azure y un contenedor de almacenamiento para crear un clúster de HDInsight. Dicha cuenta debe ubicarse en el mismo centro de datos que el clúster de HDInsight.

**Para conectarse a su cuenta de Azure**

	Add-AzureAccount

Se le pedirá que escriba las credenciales de la cuenta de Azure.

**Para crear una cuenta de almacenamiento de Azure**

	$storageAccountName = "<StorageAcccountName>"	# Provide a Storage account name
	$location = "<MicrosoftDataCenter>"				# For example, "West US"

	# Create an Azure Storage account
	New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location

Si ya tiene una cuenta de almacenamiento pero no sabe su nombre ni su clave, puede usar los siguientes comandos de Windows PowerShell para recuperar dicha información:

	# List Storage accounts for the current subscription
	Get-AzureStorageAccount

	# List the keys for a Storage account
	Get-AzureStorageKey "<StorageAccountName>"

**Para crear un contenedor de almacenamiento de blobs de Azure**

	$storageAccountName = "<StorageAccountName>"	# Provide the Storage account name
	$containerName="<ContainerName>"				# Provide a container name

	# Create a storage context object
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
	$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName
	                                       -StorageAccountKey $storageAccountKey  

	# Create a Blob storage container
	New-AzureStorageContainer -Name $containerName -Context $destContext

Cuando tenga preparados la cuenta de almacenamiento y el contenedor de blobs, podrá proceder con la creación de un clúster.

**Para aprovisionar un clúster de HDInsight**

> [AZURE.NOTE] Los cmdlets de Azure PowerShell son la única forma recomendada de cambiar variables de configuración en un clúster de HDInsight. Los cambios que se realicen en los archivos de configuración de Hadoop mientras esté conectado al clúster a través de Escritorio remoto se pueden sobrescribir si se aplica una revisión al clúster. Los valores de configuración establecidos con Azure PowerShell sí se mantienen si se aplica una revisión al clúster.

- Ejecute los comandos siguientes en la ventana de la consola de Azure PowerShell:

		$subscriptionName = "<AzureSubscriptionName>"	  # The Azure subscription used for the HDInsight cluster to be created
	
		$storageAccountName = "<AzureStorageAccountName>" # HDInsight cluster requires an existing Azure Storage account to be used as the default file system
	
		$clusterName = "<HDInsightClusterName>"			  # The name for the HDInsight cluster to be created
		$clusterNodes = <ClusterSizeInNodes>              # The number of nodes in the HDInsight cluster
		$hadoopUserName = "<HadoopUserName>"              # User name for the Hadoop user. You will use this account to connect to the cluster and run jobs.
		$hadoopUserPassword = "<HadoopUserPassword>"
	
		$secPassword = ConvertTo-SecureString $hadoopUserPassword -AsPlainText -Force
		$credential = New-Object System.Management.Automation.PSCredential($hadoopUserName,$secPassword)
	
		# Get the storage primary key based on the account name
		Select-AzureSubscription $subscriptionName
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$containerName = $clusterName				# Azure Blob container that is used as the default file system for the HDInsight cluster
	
		# The location of the HDInsight cluster. It must be in the same data center as the Storage account.
		$location = Get-AzureStorageAccount -StorageAccountName $storageAccountName | %{$_.Location}
	
		# Create a new HDInsight cluster
		New-AzureHDInsightCluster -Name $clusterName -Credential $credential -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes -ClusterType Hadoop

	>[AZURE.NOTE] Los comandos $hadoopUserName y $hadoopUserPassword se usan para crear la cuenta de usuario de Hadoop para el clúster. Esta cuenta se usará para conectarse al clúster y ejecutar trabajos. Si usa la opción Creación rápida en el portal de Azure clásico para aprovisionar un clúster, el nombre de usuario de Hadoop predeterminado es "admin". No confunda esta cuenta con la cuenta de usuario del Protocolo de escritorio remoto (RDP). La cuenta de usuario RDP tiene que ser diferente de la cuenta de usuario de Hadoop. Para obtener más información, consulte [Administración de clústeres de Hadoop en HDInsight mediante el Portal de Azure clásico][hdinsight-admin-portal].

	El aprovisionamiento del clúster puede durar varios minutos en completarse.

	![HDI.CLI.Provision][image-hdi-ps-provision]

**Para aprovisionar un clúster de HDInsight usando opciones de configuración personalizada**

Durante el aprovisionamiento de un clúster, puede usar las demás opciones de configuración; por ejemplo, conectarse a más de un contenedor de almacenamiento de blobs de Azure, usar una red virtual o utilizar una base de datos SQL de Azure para tiendas de metadatos de Hive y Oozie. Esto le permite separar la vigencia de sus datos y metadatos de la vigencia del clúster.

> [AZURE.NOTE] Los cmdlets de Windows PowerShell son la única forma recomendada de cambiar variables de configuración en un clúster de HDInsight. Los cambios que se realicen en los archivos de configuración de Hadoop mientras esté conectado al clúster a través de Escritorio remoto se pueden sobrescribir si se aplica una revisión al clúster. Los valores de configuración establecidos con Azure PowerShell sí se mantienen si se aplica una revisión al clúster.

- Ejecute los comandos siguientes en la ventana de Windows PowerShell:

		$subscriptionName = "<SubscriptionName>"
		$clusterName = "<ClusterName>"
		$location = "<MicrosoftDataCenter>"
		$clusterNodes = <ClusterSizeInNodes>

		$storageAccountName_Default = "<DefaultFileSystemStorageAccountName>"
		$containerName_Default = "<DefaultFileSystemContainerName>"

		$storageAccountName_Add1 = "<AdditionalStorageAccountName>"

		$hiveSQLDatabaseServerName = "<SQLDatabaseServerNameForHiveMetastore>"
		$hiveSQLDatabaseName = "<SQLDatabaseDatabaseNameForHiveMetastore>"
		$oozieSQLDatabaseServerName = "<SQLDatabaseServerNameForOozieMetastore>"
		$oozieSQLDatabaseName = "<SQLDatabaseDatabaseNameForOozieMetastore>"

		# Get the virtual network ID and subnet name
		$vnetID = "<AzureVirtualNetworkID>"
		$subNetName = "<AzureVirtualNetworkSubNetName>"

		# Get the Storage account keys
		Select-AzureSubscription $subscriptionName
		$storageAccountKey_Default = Get-AzureStorageKey $storageAccountName_Default | %{ $_.Primary }
		$storageAccountKey_Add1 = Get-AzureStorageKey $storageAccountName_Add1 | %{ $_.Primary }

		$oozieCreds = Get-Credential -Message "Oozie metastore"
		$hiveCreds = Get-Credential -Message "Hive metastore"

		# Create a Blob storage container
		$dest1Context = New-AzureStorageContext -StorageAccountName $storageAccountName_Default -StorageAccountKey $storageAccountKey_Default  
		New-AzureStorageContainer -Name $containerName_Default -Context $dest1Context

		# Create a new HDInsight cluster
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes |
		    Set-AzureHDInsightDefaultStorage -StorageAccountName "$storageAccountName_Default.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Default -StorageContainerName $containerName_Default |
		    Add-AzureHDInsightStorage -StorageAccountName "$storageAccountName_Add1.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Add1 |
		    Add-AzureHDInsightMetastore -SqlAzureServerName "$hiveSQLDatabaseServerName.database.windows.net" -DatabaseName $hiveSQLDatabaseName -Credential $hiveCreds -MetastoreType HiveMetastore |
		    Add-AzureHDInsightMetastore -SqlAzureServerName "$oozieSQLDatabaseServerName.database.windows.net" -DatabaseName $oozieSQLDatabaseName -Credential $oozieCreds -MetastoreType OozieMetastore |
		        New-AzureHDInsightCluster -Name $clusterName -Location $location -VirtualNetworkId $vnetID -SubnetName $subNetName

	>[AZURE.NOTE] La base de datos SQL de Azure usada para la tienda de metadatos debe permitir la conectividad con otros servicios de Azure, incluido HDInsight de Azure. En el panel de la base de datos SQL de Azure, en el lado derecho, haga clic en el nombre de servidor. Este es el servidor en el que se ejecuta la instancia de base de datos SQL. Cuando se encuentre en la vista de servidor, haga clic en **Configurar** y luego, en **Servicios de Azure**, haga clic en **Sí** y en **Guardar**.

**Para enumerar los clústeres de HDInsight**

- Ejecute el siguiente comando en una ventana de consola de Azure PowerShell para enumerar el clúster de HDInsight y comprobar que dicho clúster se aprovisionó correctamente:

		Get-AzureHDInsightCluster -Name <ClusterName>


### Uso de la CLI de Azure

> [AZURE.NOTE] Desde el 29/8/2014 la CLI de Azure no se puede usar para asociar un clúster con una red virtual de Azure.

Otra opción para aprovisionar un clúster de HDInsight es la CLI de Azure. La CLI de Azure se implementa en Node.js y se puede usar en cualquier plataforma compatible con Node.js, entre las que se incluyen Windows, Mac y Linux. Puede instalar la CLI desde las siguientes ubicaciones:

- **SDK de Node.js**: <a href="https://www.npmjs.com/package/azure-mgmt-hdinsight" target="_blank">https://www.npmjs.com/package/azure-mgmt-hdinsight</a>
- **CLI de Azure**: <a href="https://github.com/azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz" target="_blank">https://github.com/azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz</a>  

Para obtener información general acerca de cómo usar la CLI de Azure, consulte [Instalación y configuración de la interfaz de la línea de comandos entre plataformas de Azure](../xplat-cli-install.md).

Las instrucciones que aparecen a continuación le guían en la instalación de la CLI de Azure en Linux y Windows y, a continuación, en el uso de la línea de comandos para aprovisionar un clúster.

- [Configurar la CLI de Azure para Linux](#clilin)
- [Configurar la CLI de Azure para Windows](#cliwin)
- [Aprovisionamiento de clústeres de HDInsight mediante la CLI de Azure](#cliprovision)

#### <a id="clilin"></a>Configuración de la CLI de Azure para Linux

Realice los siguientes procedimientos para configurar un equipo con Linux para que use la interfaz de la línea de comandos de Azure (CLI de Azure):

- Instalar la CLI de Azure mediante el uso del Administrador de paquetes de Node.js (NPM)
- Conectarse a su suscripción de Azure

**Para instalar la CLI de Azure mediante NPM**

1.	Abra una ventana del terminal en el equipo con Linux y ejecute el siguiente comando:

		sudo npm install -g https://github.com/azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz

2.	Ejecute el siguiente comando para comprobar la instalación:

		azure hdinsight -h

	Puede usar el modificador *-h* en diferentes niveles para mostrar la información de ayuda. Por ejemplo:

		azure -h
		azure hdinsight -h
		azure hdinsight cluster -h
		azure hdinsight cluster create -h

**Para conectarse a su suscripción de Azure**

Antes de usar la interfaz de línea de comandos, debe configurar la conectividad entre su estación de trabajo y Azure. La CLI de Azure usa la información de su suscripción de Azure para conectarse a su cuenta. Esta información se puede obtener de Azure en un archivo de configuración de publicación. El archivo de configuración de publicación puede importarse después como un valor de configuración local permanente que CLI de Azure usará para operaciones posteriores. Solo tiene que importar la configuración de publicación una vez.

> [AZURE.NOTE] El archivo de configuración de publicación contiene información confidencial. Microsoft recomienda eliminar el archivo o tomar medidas adicionales para cifrar la carpeta del usuario que contiene el archivo. En Windows, modifique las propiedades de la carpeta o use el Cifrado de unidad BitLocker.


1.	Abra una ventana del terminal.
2.	Ejecute el siguiente comando para iniciar sesión en su suscripción de Azure:

		azure account download

	![HDI.Linux.CLIAccountDownloadImport](./media/hdinsight-provision-clusters/HDI.Linux.CLIAccountDownloadImport.png)

	El comando inicia la página web para descargar el archivo de configuración de publicación desde ella. Si la página web no se abre, haga clic en el vínculo que aparece en la ventana del terminal para abrir la página del explorador e iniciar sesión en el portal.

3.	Descargue el archivo de configuración de publicación en el equipo.
5.	En la ventana del símbolo del sistema, ejecute el comando siguiente para importar el archivo de configuración de publicación:

		azure account import <path/to/the/file>


#### <a id="cliwin"></a>Configuración de la CLI de Azure para Windows

Realice los siguientes procedimientos para configurar cualquier equipo con Windows para que use la CLI de Azure:

- Instalar la CLI de Azure (mediante NPM o Windows Installer)
- Descargar e importar de la configuración de publicación de la cuenta de Azure


La CLI de Azure se puede instalar mediante NPM o Windows Installer. Microsoft recomienda realizar la instalación usando solamente una de las dos opciones.

**Para instalar la CLI de Azure mediante NPM**

1.	Vaya a **www.nodejs.org**.
2.	Haga clic en **INSTALL** (INSTALAR) y siga las instrucciones, usando la configuración predeterminada.
3.	Abra **Command Prompt** (Símbolo del sistema) (o *Azure Command Prompt* (Símbolo del sistema de Azure) o *Developer Command Prompt for VS2012 (Símbolo del sistema para desarrolladores de VS2012)*) desde su estación de trabajo.
4.	Ejecute el comando siguiente en la ventana del símbolo del sistema:

		npm install -g https://github.com/azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz

	> [AZURE.NOTE] Si recibe un error que indica que no se encuentra el comando NPM, compruebe que las rutas siguientes estén en la variable de entorno PATH: <i>C:\\Program Files (x86)\\nodejs;C:\\Users[username]\\AppData\\Roaming\\npm</i> o <i>C:\\Program Files\\nodejs;C:\\Users[username]\\AppData\\Roaming\\npm</i>

5.	Ejecute el siguiente comando para comprobar la instalación:

		azure hdinsight -h

	Puede usar el modificador *-h* en diferentes niveles para mostrar la información de ayuda. Por ejemplo:

		azure -h
		azure hdinsight -h
		azure hdinsight cluster -h
		azure hdinsight cluster create -h

**Para instalar la CLI de Azure con Windows Installer**

1.	Vaya a ****http://azure.microsoft.com/downloads/**.
2.	Desplácese hasta la sección **Herramientas de línea de comandos** y, a continuación, haga clic en **Interfaz de la línea de comandos de Azure** y siga el asistente del instalador de plataforma web.

**Para descargar e importar la configuración de publicación**

Antes de usar la interfaz de línea de comandos, debe configurar la conectividad entre su estación de trabajo y Azure. La CLI de Azure usa la información de su suscripción de Azure para conectarse a su cuenta. Esta información se puede obtener de Azure en un archivo de configuración de publicación. El archivo de configuración de publicación puede importarse después como un valor de configuración local permanente que CLI de Azure usará para operaciones posteriores. Solo tiene que importar la configuración de publicación una vez.

> [AZURE.NOTE] El archivo de configuración de publicación contiene información confidencial. Microsoft recomienda eliminar el archivo o tomar medidas adicionales para cifrar la carpeta del usuario que contiene el archivo. En Windows, modifique las propiedades de la carpeta o use BitLocker.


1.	Abra el **símbolo del sistema**.
2.	Ejecute el comando siguiente para descargar el archivo de configuración de publicación:

		azure account download

	![HDI.CLIAccountDownloadImport][image-cli-account-download-import]

	El comando inicia la página web para descargar el archivo de configuración de publicación desde ella.

3.	En el símbolo del sistema para guardar el archivo, haga clic en **Guardar** y proporcione una ubicación en la que se deba guardar el archivo.
5.	En la ventana del símbolo del sistema, ejecute el comando siguiente para importar el archivo de configuración de publicación:

		azure account import <path/to/the/file>

#### <a id="cliprovision"></a>Aprovisionamiento de clústeres de HDInsight mediante una CLI de Azure

Los siguientes procedimientos son necesarios para aprovisionar un clúster de HDInsight con la CLI de Azure:

- Crear una cuenta de Almacenamiento de Azure
- Aprovisionamiento de un clúster

**Para crear una cuenta de almacenamiento de Azure**

HDInsight utiliza contenedores de almacenamiento de blobs de Azure como sistemas de archivos predeterminados. Es preciso tener una cuenta de almacenamiento de Azure antes de crear un clúster de HDInsight. La cuenta de almacenamiento debe ubicarse en el mismo centro de datos.

- En la ventana del símbolo del sistema, ejecute el siguiente comando para crear una cuenta de almacenamiento de Azure:

		azure storage account create [options] <StorageAccountName>

	Cuando se le pida una ubicación, seleccione aquella en la que se puede aprovisionar el clúster de HDInsight. El almacenamiento debe encontrarse en la misma ubicación que el clúster de HDInsight. Actualmente, solo las regiones **Asia oriental**, **Sudeste de Asia**, **Norte de Europa**, **Oeste de Europa**, **Este de EE. UU.**, **Oeste de EE. UU.**, **Centro y norte de EE. UU.** y **Centro y sur de EE. UU.** pueden hospedar clústeres de HDInsight.

Para obtener información sobre la creación de una cuenta de almacenamiento de Azure mediante el Portal de Azure clásico, consulte [Creación, administración o eliminación de una cuenta de almacenamiento](../storage/storage-create-storage-account.md).

Si ya tiene una cuenta de almacenamiento pero no sabe su nombre ni su clave, puede usar los comandos siguientes para recuperar dicha información:

	-- Lists Storage accounts
	azure storage account list

	-- Shows information for a Storage account
	azure storage account show <StorageAccountName>

	-- Lists the keys for a Storage account
	azure storage account keys list <StorageAccountName>

Para saber cómo obtener la información mediante el portal de Azure clásico, vea la sección *Procedimiento: ver, copiar y regenerar claves de acceso de almacenamiento* de [Crear, administrar o eliminar una cuenta de almacenamiento](../storage/storage-create-storage-account.md).

Un clúster de HDInsight también requiere un contenedor dentro de una cuenta de almacenamiento. Si la cuenta de almacenamiento que proporciona todavía no tiene un contenedor, el comando *azure hdinsight cluster create* le pide un nombre de contenedor y también lo crea. Sin embargo, si opta por crear el contenedor antes, puede usar el comando siguiente:

	azure storage container create --account-name <StorageAccountName> --account-key <StorageAccountKey> [ContainerName]

Cuando tenga preparados la cuenta de almacenamiento y el contenedor de blobs, podrá proceder con la creación de un clúster.

**Para aprovisionar un clúster de HDInsight**

- En la ventana del símbolo del sistema, ejecute el comando siguiente:

		azure hdinsight cluster create --clusterName <ClusterName> --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey <storageAccountKey> --storageContainer <StorageContainerName> --dataNodeCount <NumberOfNodes> --location <DataCenterLocation> --userName <HDInsightClusterUsername> --password <HDInsightClusterPassword> --osType windows

	![HDI.CLIClusterCreation][image-cli-clustercreation]


**Para aprovisionar un clúster de HDInsight con un archivo de configuración**

Normalmente, se aprovisiona un clúster de HDInsight, se ejecutan los trabajos y, a continuación, se elimina el clúster para reducir los costes. La CLI de Azure le da la opción de guardar las configuraciones en un archivo para que pueda volver a usarlas cada vez que aprovisione un clúster.

- En la ventana del símbolo del sistema, ejecute los comandos siguientes:


		#Create the config file
		azure hdinsight cluster config create <file>

		#Add commands to create a basic cluster
		azure hdinsight cluster config set <file> --clusterName <ClusterName> --dataNodeCount <NumberOfNodes> --location "<DataCenterLocation>" --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey "<StorageAccountKey>" --storageContainer "<BlobContainerName>" --userName "<Username>" --password "<UserPassword>" --osType windows

		#If required, include commands to use additional Blob storage with the cluster
		azure hdinsight cluster config storage add <file> --storageAccountName "<StorageAccountName>.blob.core.windows.net"
		       --storageAccountKey "<StorageAccountKey>"

		#If required, include commands to use a SQL database as a Hive metastore
		azure hdinsight cluster config metastore set <file> --type "hive" --server "<SQLDatabaseName>.database.windows.net"
		       --database "<HiveDatabaseName>" --user "<Username>" --metastorePassword "<UserPassword>"

		#If required, include commands to use a SQL database as an Oozie metastore
		azure hdinsight cluster config metastore set <file> --type "oozie" --server "<SQLDatabaseName>.database.windows.net"
		       --database "<OozieDatabaseName>" --user "<SQLUsername>" --metastorePassword "<SQLPassword>"

		#Run this command to create a cluster by using the config file
		azure hdinsight cluster create --config <file>

	>[AZURE.NOTE] La base de datos SQL de Azure usada para la tienda de metadatos debe permitir la conectividad con otros servicios de Azure, incluido HDInsight de Azure. En el panel de la base de datos SQL de Azure, en el lado derecho, haga clic en el nombre de servidor. Este es el servidor en el que se ejecuta la instancia de base de datos SQL. Cuando se encuentre en la vista de servidor, haga clic en **Configurar** y luego, en **Servicios de Azure**, haga clic en **Sí** y en **Guardar**.


	![HDI.CLIClusterCreationConfig][image-cli-clustercreation-config]


**Para enumerar y mostrar los detalles del clúster**

- Use los comandos siguientes para enumerar y mostrar los detalles del clúster:

		azure hdinsight cluster list
		azure hdinsight cluster show <ClusterName>

	![HDI.CLIListCluster][image-cli-clusterlisting]


**Para eliminar un clúster**

- Use el comando siguiente para eliminar un clúster:

		azure hdinsight cluster delete <ClusterName>



### Uso del SDK de .NET de HDInsight
El SDK .NET de HDInsight proporciona bibliotecas de cliente .NET que facilitan el trabajo con HDInsight desde una aplicación .NET Framework.

Los siguientes procedimientos se deben realizar para aprovisionar un clúster de HDInsight con el SDK:

- Instalación del SDK de .NET de HDInsight
- Creación de un certificado autofirmado
- Creación de una aplicación de consola
- Ejecución de la aplicación


**Para instalar el SDK de .NET de HDInsight**

Puede instalar la compilación publicada más reciente del SDK desde [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started). Las instrucciones se mostrarán en el siguiente procedimiento.

**Para crear un certificado autofirmado**

Cree un certificado autofirmado, instálelo en su estación de trabajo y cárguelo en su suscripción de Azure. Para obtener instrucciones, consulte [Creación de un certificado autofirmado](http://go.microsoft.com/fwlink/?LinkId=511138).


**Para crear una aplicación de consola de Visual Studio**

1. Abra Visual Studio 2013 o 2015

2. En el menú **Archivo**, haga clic en **Nuevo** y, a continuación, en **Proyecto**.

3. En **Nuevo proyecto**, escriba o seleccione los siguientes valores:

	|Propiedad|Valor|
	|--------|-----|
	|Plantilla|Plantillas/Visual C#/Windows/Aplicación de consola|
	|Nombre|CrearClústerHDI|

4. Haga clic en **Aceptar** para crear el proyecto.

5. En el menú **Herramientas**, haga clic en **Administrador de paquetes NuGet** y, a continuación, en la **Consola del administrador de paquetes**.

6. Ejecute el siguiente comando en la consola para instalar los paquetes:

		Install-Package Microsoft.Azure.Common.Authentication -Pre
		Install-Package Microsoft.Azure.Management.HDInsight -Pre
		Install-Package Microsoft.Azure.Management.Resources -Pre

	Estos comandos agregan las bibliotecas y referencias .NET al proyecto de Visual Studio actual.

7. En el Explorador de soluciones, haga doble clic en **Program.cs** para abrirlo.
8. Reemplace el código por lo siguiente:

		using Microsoft.Azure;
		using Microsoft.Azure.Common.Authentication;
		using Microsoft.Azure.Common.Authentication.Factories;
		using Microsoft.Azure.Common.Authentication.Models;
		using Microsoft.Azure.Management.HDInsight;
		using Microsoft.Azure.Management.HDInsight.Models;
		using Microsoft.Azure.Management.Resources;

		namespace CreateHDICluster
		{
		    internal class Program
		    {
		        private static HDInsightManagementClient _hdiManagementClient;
		
		        private static Guid SubscriptionId = new Guid("<AZURE SUBSCRIPTION ID>");
		        private const string ResourceGroupName = "<AZURE RESOURCEGROUP NAME>";

		        private const string NewClusterName = "<HDINSIGHT CLUSTER NAME>";
		        private const int NewClusterNumNodes = <NUMBER OF NODES>;
		        private const string NewClusterLocation = "<LOCATION>";  // Must match the Azure Storage account location
		        private const HDInsightClusterType NewClusterType = HDInsightClusterType.Hadoop;
		        private const OSType NewClusterOSType = OSType.Windows;
		        private const string NewClusterVersion = "3.2";

		        private const string NewClusterUsername = "admin";
		        private const string NewClusterPassword = "<HTTP USER PASSWORD>";
		        private const string ExistingStorageName = "<STORAGE ACCOUNT NAME>.blob.core.windows.net";
		        private const string ExistingStorageKey = "<STORAGE ACCOUNT KEY>";
		        private const string ExistingContainer = "<DEFAULT CONTAINER NAME>"; 
		
		
		        private static void Main(string[] args)
		        {
		            var tokenCreds = GetTokenCloudCredentials();
		            var subCloudCredentials = GetSubscriptionCloudCredentials(tokenCreds, SubscriptionId);
		            
		            var resourceManagementClient = new ResourceManagementClient(subCloudCredentials);
		            resourceManagementClient.Providers.Register("Microsoft.HDInsight");
		
		            _hdiManagementClient = new HDInsightManagementClient(subCloudCredentials);
		
		            CreateCluster();
		        }
		
		        public static SubscriptionCloudCredentials GetTokenCloudCredentials(string username = null, SecureString password = null)
		        {
		            var authFactory = new AuthenticationFactory();
		
		            var account = new AzureAccount { Type = AzureAccount.AccountType.User };
		
		            if (username != null && password != null)
		                account.Id = username;
		
		            var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
		
		            var accessToken =
		                authFactory.Authenticate(account, env, AuthenticationFactory.CommonAdTenant, password, ShowDialog.Auto)
		                    .AccessToken;
		
		            return new TokenCloudCredentials(accessToken);
		        }
		
		        public static SubscriptionCloudCredentials GetSubscriptionCloudCredentials(SubscriptionCloudCredentials creds, Guid subId)
		        {
		            return new TokenCloudCredentials(subId.ToString(), ((TokenCloudCredentials)creds).Token);
		        }
		
		
		        private static void CreateCluster()
		        {
		            var parameters = new ClusterCreateParameters
		            {
		                ClusterSizeInNodes = NewClusterNumNodes,
		                Location = NewClusterLocation,
		                ClusterType = NewClusterType,
		                OSType = NewClusterOSType,
		                Version = NewClusterVersion,

		                UserName = NewClusterUsername,
		                Password = NewClusterPassword,

		                DefaultStorageAccountName = ExistingStorageName,
		                DefaultStorageAccountKey = ExistingStorageKey,
		                DefaultStorageContainer = ExistingContainer
		            };
		
		            _hdiManagementClient.Clusters.Create(ResourceGroupName, NewClusterName, parameters);
		        }
			}
		}
		
10. Reemplace los valores de miembro de clase.

**Para ejecutar la aplicación**

Mientras la aplicación está abierta en Visual Studio, presione **F5** para ejecutarla. Una ventana de consola se abrirá y mostrará el estado de la aplicación. La creación del clúster de HDInsight puede durar varios minutos.


##<a id="nextsteps"></a> Pasos siguientes
En este artículo, ha aprendido a aprovisionar un clúster de HDInsight de varias formas. Para obtener más información, consulte los artículos siguientes:

* [Introducción a HDInsight de Azure](hdinsight-hadoop-linux-tutorial-get-started.md): aprenda a empezar a trabajar con su clúster de HDInsight
* [Uso de Sqoop con HDInsight](hdinsight-use-sqoop.md): aprenda copiar datos entre HDInsight y Base de datos SQL o SQL Server
* [Administración de HDInsight con PowerShell](hdinsight-administer-use-powershell.md): aprenda a trabajar con HDInsight mediante PowerShell
* [Envío de trabajos de Hadoop mediante programación](hdinsight-submit-hadoop-jobs-programmatically.md): aprenda a enviar trabajos a HDInsight mediante programación
* [Documentación del SDK de HDInsight de Azure][hdinsight-sdk-documentation]\: descubra el SDK de HDInsight


[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx
[azure-management-portal]: https://manage.windowsazure.com

<!---HONumber=AcomDC_0302_2016-->