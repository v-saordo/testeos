<properties
	pageTitle="Personalización de los clústeres de HDInsight con bootstrap | Microsoft Azure"
	description="Obtenga información acerca de cómo personalizar clústeres de HDInsight con bootstrap."
	services="hdinsight"
	documentationCenter=""
	authors="mumian"
	manager="paulettm"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/06/2016"
	ms.author="jgao"/>

# Personalización de los clústeres de HDInsight con Bootstrap

[AZURE.INCLUDE [selector](../../includes/hdinsight-create-windows-cluster-selector.md)]

Es probable que en ocasiones desee establecer los valores de archivos de configuración, que incluyen lo siguiente:

- core-site.xml
- hdfs-site.xml
- mapred-site.xml
- yarn-site.xml
- hive-site.xml
- oozie-site.xml

Los clústeres no pueden conservar los cambios debido a la recreación de imágenes. Para obtener más información acerca de las recreación de imágenes, consulte [Role Instance Restarts Due to OS Upgrades](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx) (La instancia de rol se reinicia debido a actualizaciones del sistema operativo). Para mantener los cambios durante la vida útil de los clústeres, puede usar la personalización de clústeres de HDInsight durante el proceso de creación. Este es el método recomendado para cambiar las configuraciones de un clúster y persistir entre estos eventos de reinicio para el restablecimiento de imagen inicial de Azure. Estos cambios de configuración se aplican antes del inicio del servicio, por lo que no es necesario reiniciar los servicios.

Hay tres maneras de usar bootstrap:

- Uso de Azure PowerShell
- Uso del SDK de .NET
- Uso de una plantilla de ARM

Para obtener información acerca de cómo instalar componentes adicionales en el clúster de HDInsight durante la creación, vea:

- [Personalización de clústeres de HDInsight mediante la acción de scripts (Linux)](hdinsight-hadoop-customize-cluster-linux.md)
- [Personalización de clústeres de HDInsight mediante la acción de scripts (Windows)](hdinsight-hadoop-customize-cluster.md)

## Uso de Azure PowerShell

El siguiente código de PowerShell personaliza una configuración de Hive:

	# hive-site.xml configuration
	$hiveConfigValues = @{ "hive.metastore.client.socket.timeout"="90" }
	
	$config = New-AzureRmHDInsightClusterConfig `
		| Set-AzureRmHDInsightDefaultStorage `
			-StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
			-StorageAccountKey $defaultStorageAccountKey `
		| Add-AzureRmHDInsightConfigValues `
			-HiveSite $hiveConfigValues 
	
	New-AzureRmHDInsightCluster `
		-ResourceGroupName $existingResourceGroupName `
		-ClusterName $clusterName `
		-Location $location `
		-ClusterSizeInNodes $clusterSizeInNodes `
		-ClusterType Hadoop `
		-OSType Windows `
		-Version "3.2" `
		-HttpCredential $httpCredential `
		-Config $config 

En el [Anexo A](#hdinsight-hadoop-customize-cluster-bootstrap.md/appx-a:-powershell-sample) se puede encontrar un script de PowerShell completamente en uso.

**Para comprobar el cambio:**

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. En el panel izquierdo, haga clic en **Examinar** y luego haga clic en **Clústeres de HDInsight**.
3. Haga clic en el clúster que acaba de crear mediante el script de PowerShell.
4. Haga clic en el **Panel** desde la parte superior de la hoja para abrir la IU de Ambari.
5. Haga clic en **Hive** en el menú izquierdo.
6. Haga clic en **HiveServer2** en **Summary** (Resumen).
7. Seleccione la pestaña **Configs** (Configuraciones).
8. Haga clic en **Hive** en el menú izquierdo.
9. Seleccione la pestaña **Advanced** (Opciones avanzadas).
10. Desplácese hacia abajo y luego expanda **Advanced hive-site** (Sitio de Hive avanzado).
11. Busque **hive.metastore.client.socket.timeout** en la sección.

Otros ejemplos de cómo personalizar otros archivos de configuración:

	# hdfs-site.xml configuration
	$HdfsConfigValues = @{ "dfs.blocksize"="64m" } #default is 128MB in HDI 3.0 and 256MB in HDI 2.1

	# core-site.xml configuration
	$CoreConfigValues = @{ "ipc.client.connect.max.retries"="60" } #default 50

	# mapred-site.xml configuration
	$MapRedConfigValues = @{ "mapreduce.task.timeout"="1200000" } #default 600000

	# oozie-site.xml configuration
	$OozieConfigValues = @{ "oozie.service.coord.normal.default.timeout"="150" }  # default 120

Para más información, vea el blog de Azim Uddin titulado [Personalización de la creación de clústeres de HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/04/15/customizing-hdinsight-cluster-provisioning-via-powershell-and-net-sdk.aspx).

## Uso del SDK de .NET

Consulte [Crear clústeres basados en Linux en HDInsight con el SDK de .NET](hdinsight-hadoop-create-linux-clusters-dotnet-sdk#use-bootstrap).

## Uso de una plantilla de ARM de Azure

Puede usar bootstrap en una plantilla de ARM:

    "configurations": {
        …
        "hive-site": {
            "hive.metastore.client.connect.retry.delay": "5",
            "hive.execution.engine": "mr",
            "hive.security.authorization.manager": "org.apache.hadoop.hive.ql.security.authorization.DefaultHiveAuthorizationProvider"
        }
    }


![hdinsight hadoop customize cluster bootstrap arm template](./media/hdinsight-hadoop-customize-cluster-bootstrap/hdinsight-customize-cluster-bootstrap-arm.png)



## Consulte también

- En [Creación de clústeres de Hadoop en HDInsight][hdinsight-provision-cluster] se proporcionan instrucciones sobre cómo crear un clúster de HDInsight con otras opciones personalizadas.
- [Desarrollo de la acción de script con HDInsight][hdinsight-write-script]
- [Instalación y uso de Spark en clústeres de HDInsight][hdinsight-install-spark]
- [Instalación y uso de R en clústeres de Hadoop de HDInsight][hdinsight-install-r]
- [Instalación y uso de Solr en clústeres de Hadoop de HDInsight](hdinsight-hadoop-solr-install.md).
- [Instalación y uso de Giraph en clústeres de HDInsight](hdinsight-hadoop-giraph-install.md).

[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-write-script]: hdinsight-hadoop-script-actions.md
[hdinsight-provision-cluster]: hdinsight-provision-clusters.md
[powershell-install-configure]: powershell-install-configure.md


[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "Fases durante la creación del clúster"

## Anexo A: ejemplo de PowerShell

Este script de PowerShell crea un clúster de HDInsight y personaliza una configuración de Hive:

    ####################################
    # Set these variables
    ####################################
    #region - used for creating Azure service names
    $nameToken = "<ENTER AN ALIAS>" 
    #endregion

    #region - cluster user accounts
    $httpUserName = "admin"  #HDInsight cluster username
    $httpPassword = "<ENTER A PASSWORD>" #"<Enter a Password>"

    $sshUserName = "sshuser" #HDInsight ssh user name
    $sshPassword = "<ENTER A PASSWORD>" #"<Enter a Password>"
    #endregion

    ####################################
    # Service names and varialbes
    ####################################
    #region - service names
    $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

    $resourceGroupName = $namePrefix + "rg"
    $hdinsightClusterName = $namePrefix + "hdi"
    $defaultStorageAccountName = $namePrefix + "store"
    $defaultBlobContainerName = $hdinsightClusterName

    $location = "East US 2"
    #endregion

    # Treat all errors as terminating
    $ErrorActionPreference = "Stop"

    ####################################
    # Connect to Azure
    ####################################
    #region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{Login-AzureRmAccount}
    #endregion

    #region - Create an HDInsight cluster
    ####################################
    # Create dependent components
    ####################################
    Write-Host "Creating a resource group ..." -ForegroundColor Green
    New-AzureRmResourceGroup `
        -Name  $resourceGroupName `
        -Location $location

    Write-Host "Creating the default storage account and default blob container ..."  -ForegroundColor Green
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $defaultStorageAccountName `
        -Location $location `
        -Type Standard_GRS

    $defaultStorageAccountKey = Get-AzureRmStorageAccountKey `
                                    -ResourceGroupName $resourceGroupName `
                                    -Name $defaultStorageAccountName |  %{ $_.Key1 }
    $defaultStorageContext = New-AzureStorageContext `
                                    -StorageAccountName $defaultStorageAccountName `
                                    -StorageAccountKey $defaultStorageAccountKey
    New-AzureStorageContainer `
        -Name $defaultBlobContainerName `
        -Context $defaultStorageContext #use the cluster name as the container name

    ####################################
    # Create a configuration object
    ####################################
    $hiveConfigValues = @{ "hive.metastore.client.socket.timeout"="90" }
        
    $config = New-AzureRmHDInsightClusterConfig `
        | Set-AzureRmHDInsightDefaultStorage `
            -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
            -StorageAccountKey $defaultStorageAccountKey `
        | Add-AzureRmHDInsightConfigValues `
            -HiveSite $hiveConfigValues 

    ####################################
    # Create an HDInsight cluster
    ####################################
    $httpPW = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
    $httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$httpPW)

    $sshPW = ConvertTo-SecureString -String $sshPassword -AsPlainText -Force
    $sshCredential = New-Object System.Management.Automation.PSCredential($sshUserName,$sshPW)

    New-AzureRmHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $hdinsightClusterName `
        -Location $location `
        -ClusterSizeInNodes 1 `
        -ClusterType Hadoop `
        -OSType Linux `
        -Version "3.2" `
        -HttpCredential $httpCredential `
        -SshCredential $sshCredential `
        -Config $config

    ####################################
    # Verify the cluster
    ####################################
    Get-AzureRmHDInsightCluster -ClusterName $hdinsightClusterName

    #endregion

<!---HONumber=AcomDC_0218_2016-->