<properties
	pageTitle="Aprovisionamiento de clústeres de HBase en una red virtual | Microsoft Azure"
	description="Introducción al uso de HBase en HDInsight de Azure Aprenda a crear clústeres de HBase de HDInsight en Red virtual de Azure."
	keywords=""
	services="hdinsight,virtual-network"
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
   ms.date="02/17/2016"
   ms.author="jgao"/>

# Creación de clústeres de HBase en Red virtual de Azure 

Aprenda a crear clústeres de HBase en Azure HDInsight en una [red virtual de Azure][1].

Con la integración de red virtual, los clústeres de HBase se pueden implementar en la misma red que sus aplicaciones para que estas puedan comunicarse directamente con HBase. Entre las ventajas se incluye lo siguiente:

- Conectividad directa entre la aplicación web y los nodos del clúster de HBase, lo cual permite la comunicación mediante las API de llamada a procedimiento remoto (RPC) de Java de HBase.
- Rendimiento mejorado gracias a que el tráfico ya no tiene que examinar varias puertas de enlace y equilibradores de carga.
- La posibilidad de procesar información confidencial de una manera segura sin exponer un extremo público.

###Requisitos previos
Antes de empezar este tutorial, debe contar con lo siguiente:

- **Una suscripción de Azure**. Consulte [Obtención de una versión de evaluación gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Una estación de trabajo con Azure PowerShell**. Consulte [Install and use Azure PowerShell (Instalación y uso de Azure PowerShell)](https://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).

## Creación de un clúster de HBase en red virtual

En esta sección, creará un clúster de HBase basado en Linux en HDInsight con la [plantilla del Administrador de recursos de Azure](../resource-group-template-deploy.md). No es necesario tener experiencia en el uso de la plantilla del Administrador de recursos de Azure para seguir este tutorial. Para conocer otros métodos de creación de clústeres y la descripción de la configuración, consulte [Creación de clústeres de Hadoop basados en Windows en HDInsight](hdinsight-hadoop-provision-linux-clusters.md). Para obtener más información acerca de cómo utilizar la plantilla del Administrador de recursos de Azure para crear clústeres de Hadoop en HDInsight, consulte [Crear clústeres de Hadoop en HDInsight con plantillas del Administrador de recursos de Azure (Windows)](hdinsight-hadoop-create-windows-clusters-arm-templates.md).

1. Haga clic en la imagen siguiente para abrir una plantilla ARM en el Portal de Azure. La plantilla ARM se encuentra en un contenedor de blobs público. 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hbase-cluster-in-vnet.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/es-ES/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. En la hoja **Parámetros**, escriba lo siguiente:

    - **ClusterName**: escriba un nombre para el clúster de Hadoop que va a crear.
    - **Nombre de inicio de sesión y contraseña de clúster**: el nombre de inicio de sesión predeterminado es **admin**.
    - **Nombre de usuario y contraseña de SSH**: el nombre de usuario predeterminado es **sshuser**. Puede cambiarlo. 

    Muchas propiedades han sido codificadas de forma rígida en la plantilla. Por ejemplo:
    
    - Ubicación: Este de EE. UU.
    - Número de nodos de trabajo en el clúster: 4
    - Cuenta de almacenamiento predeterminada: <Cluster Name>almacén
    - Nombre de red virtual: <Cluster Name>- red virtual
    - Espacio de direcciones de red virtual: 10.0.0.0/16
    - Nombre de subred: predeterminado
    - Intervalo de direcciones de subred: 10.0.0.0/24

3. Haga clic en **Aceptar** para guardar los parámetros.
4. En la hoja **Implementación personalizada**, haga clic en el cuadro desplegable **Grupo de recursos** y, después, haga clic en **Nuevo** para crear un nuevo grupo de recursos. El grupo de recursos es un contenedor que agrupa al clúster, a la cuenta de almacenamiento dependiente y a otros recursos vinculados.
5. Haga clic en **Términos legales** y luego haga clic en **Crear**.
6. Haga clic en **Crear**. Verá un icono nuevo titulado **Envío de implementación para la implementación de plantilla**. Tarda aproximadamente 20 minutos en crear un clúster. Una vez creado el clúster, puede hacer clic en la hoja del clúster en el portal para abrirlo.

Después de completar el tutorial, quizá desee eliminar el clúster. Con HDInsight, los datos se almacenan en Almacenamiento de Azure, por lo que puede eliminar un clúster de forma segura cuando no está en uso. También se le cargará por un clúster de HDInsight aunque no esté en uso. Como en muchas ocasiones los cargos por el clúster son más que los cargos por el almacenamiento, desde el punto de vista económico tiene sentido eliminar clústeres cuando no estén en uso. Para obtener instrucciones sobre cómo eliminar un clúster, consulte [Administración de clústeres de Hadoop en HDInsight mediante el Portal de Azure](hdinsight-administer-use-management-portal.md#delete-clusters).

Para comenzar a trabajar con el nuevo clúster de HBase, utilice los procedimientos que encontrará en [Introducción al uso de HBase con Hadoop en HDInsight](hdinsight-hbase-tutorial-get-started.md).

##Conexión al clúster de HBase mediante las API de RPC de Java de HBase

1.	Cree una máquina virtual de infraestructura como servicio (IaaS) en la misma red virtual de Azure y la misma subred. De este modo, tanto la máquina virtual como el clúster de HBase usan el mismo servidor DNS interno para resolver nombres de host. Para ello, debe elegir la opción **Desde la galería** y seleccionar la red virtual en lugar de un centro de datos. Para obtener instrucciones, consulte [Creación de una máquina virtual que ejecuta Windows Server](../virtual-machines/virtual-machines-windows-tutorial.md). Una imagen de Windows Server 2012 estándar con una VM pequeña es suficiente.

2.	Cuando use una aplicación Java para conectarse a HBase en modo remoto, debe usar el nombre de dominio completo (FQDN). Para determinarlo, debe obtener el sufijo DNS específico de la conexión del clúster de HBase. Para ello, use Curl para consultar Ambari o Escritorio remoto para conectarse al clúster.

	* **Curl**: use el comando siguiente:

			curl -u <username>:<password> -k https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/hbase/components/hbrest

		En los datos de notación de objetos JavaScript (JSON) devueltos, busque la entrada "host\_name". Esta entrada contendrá el nombre de dominio completo (FQDN) de los nodos del clúster. Por ejemplo:

			...
			"host_name": "wordkernode0.<clustername>.b1.cloudapp.net
			...

		La parte del nombre de dominio que comienza con el nombre del clúster es el sufijo DNS. Por ejemplo, mycluster.b1.cloudapp.net.

	* **Azure PowerShell**: use el siguiente script de Azure PowerShell para registrar la función **Get-ClusterDetail**, que se puede usar para devolver el sufijo DNS:

			function Get-ClusterDetail(
			    [String]
			    [Parameter( Position=0, Mandatory=$true )]
			    $ClusterDnsName,
				[String]
			    [Parameter( Position=1, Mandatory=$true )]
			    $Username,
				[String]
			    [Parameter( Position=2, Mandatory=$true )]
			    $Password,
			    [String]
			    [Parameter( Position=3, Mandatory=$true )]
			    $PropertyName
				)
			{
			<#
			    .SYNOPSIS
			     Displays information to facilitate an HDInsight cluster-to-cluster scenario within the same virtual network.
				.Description
				 This command shows the following 4 properties of an HDInsight cluster:
				 1. ZookeeperQuorum (supports only HBase type cluster)
					Shows the value of HBase property "hbase.zookeeper.quorum".
				 2. ZookeeperClientPort (supports only HBase type cluster)
					Shows the value of HBase property "hbase.zookeeper.property.clientPort".
				 3. HBaseRestServers (supports only HBase type cluster)
					Shows a list of host FQDNs that run the HBase REST server.
				 4. FQDNSuffix (supports all cluster types)
					Shows the FQDN suffix of hosts in the cluster.
			    .EXAMPLE
			     Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperQuorum
			     This command shows the value of HBase property "hbase.zookeeper.quorum".
			    .EXAMPLE
			     Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperClientPort
			     This command shows the value of HBase property "hbase.zookeeper.property.clientPort".
			    .EXAMPLE
			     Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName HBaseRestServers
			     This command shows a list of host FQDNs that run the HBase REST server.
			    .EXAMPLE
			     Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName FQDNSuffix
			     This command shows the FQDN suffix of hosts in the cluster.
			#>

				$DnsSuffix = ".azurehdinsight.net"

				$ClusterFQDN = $ClusterDnsName + $DnsSuffix
				$webclient = new-object System.Net.WebClient
				$webclient.Credentials = new-object System.Net.NetworkCredential($Username, $Password)

				if($PropertyName -eq "ZookeeperQuorum")
				{
					$Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.quorum"
					$Response = $webclient.DownloadString($Url)
					$JsonObject = $Response | ConvertFrom-Json
					Write-host $JsonObject.items[0].properties.'hbase.zookeeper.quorum'
				}
				if($PropertyName -eq "ZookeeperClientPort")
				{
					$Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.property.clientPort"
					$Response = $webclient.DownloadString($Url)
					$JsonObject = $Response | ConvertFrom-Json
					Write-host $JsonObject.items[0].properties.'hbase.zookeeper.property.clientPort'
				}
				if($PropertyName -eq "HBaseRestServers")
				{
					$Url1 = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.rest.port"
					$Response1 = $webclient.DownloadString($Url1)
					$JsonObject1 = $Response1 | ConvertFrom-Json
					$PortNumber = $JsonObject1.items[0].properties.'hbase.rest.port'

					$Url2 = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/hbase/components/hbrest"
					$Response2 = $webclient.DownloadString($Url2)
					$JsonObject2 = $Response2 | ConvertFrom-Json
					foreach ($host_component in $JsonObject2.host_components)
					{
						$ConnectionString = $host_component.HostRoles.host_name + ":" + $PortNumber
						Write-host $ConnectionString
					}
				}
				if($PropertyName -eq "FQDNSuffix")
				{
					$Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/yarn/components/resourcemanager"
					$Response = $webclient.DownloadString($Url)
					$JsonObject = $Response | ConvertFrom-Json
					$FQDN = $JsonObject.host_components[0].HostRoles.host_name
					$pos = $FQDN.IndexOf(".")
					$Suffix = $FQDN.Substring($pos + 1)
					Write-host $Suffix
				}
			}

		Después de ejecutar el script de Azure PowerShell, use el siguiente comando para devolver el sufijo DNS usando la función **Get-ClusterDetail**. Especifique el nombre del clúster de HBase de HDInsight, el nombre y la contraseña de administrador cuando use este comando.

			Get-ClusterDetail -ClusterDnsName <yourclustername> -PropertyName FQDNSuffix -Username <clusteradmin> -Password <clusteradminpassword>

		Este código devolverá el sufijo DNS. Por ejemplo, **yourclustername.b4.internal.cloudapp.net**.

	> [AZURE.NOTE] Puede usar también Escritorio remoto para conectarse al clúster de HBase (se conectará al nodo principal) y ejecutar **ipconfig** desde el símbolo del sistema para obtener el sufijo DNS. Para obtener instrucciones acerca de cómo habilitar el Protocolo de escritorio remoto (RDP) y conectarse al clúster mediante RDP, consulte [Administración de clústeres de Hadoop en HDInsight mediante el Portal de Azure][hdinsight-admin-portal].
	>
	> ![hdinsight.hbase.dns.surffix][img-dns-surffix]


<!--
3.	Change the primary DNS suffix configuration of the virtual machine. This enables the virtual machine to automatically resolve the host name of the HBase cluster without explicit specification of the suffix. For example, the *workernode0* host name will be correctly resolved to workernode0 of the HBase cluster.

	To make the configuration change:

	1. RDP into the virtual machine.
	2. Open **Local Group Policy Editor**. The executable is gpedit.msc.
	3. Expand **Computer Configuration**, expand **Administrative Templates**, expand **Network**, and then click **DNS Client**.
	- Set **Primary DNS Suffix** to the value obtained in step 2:

		![hdinsight.hbase.primary.dns.suffix][img-primary-dns-suffix]
	4. Click **OK**.
	5. Reboot the virtual machine.
-->

Para comprobar que la máquina virtual puede comunicarse con el clúster de HBase, use el comando `ping headnode0.<dns suffix>` desde la máquina virtual. Por ejemplo, haga ping a headnode0.mycluster.b1.cloudapp.net

Para usar esta información en una aplicación Java, puede seguir los pasos que se indican en [Uso de Maven para crear aplicaciones Java que utilicen HBase con HDInsight (Hadoop)](hdinsight-hbase-build-java-maven.md) para crear una aplicación. Para que la aplicación se conecte a un servidor HBase remoto, modifique el archivo **hbase-site.xml** de este ejemplo para que use el FQDN de ZooKeeper. Por ejemplo:

	<property>
    	<name>hbase.zookeeper.quorum</name>
    	<value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
	</property>

> [AZURE.NOTE] Para obtener más información sobre la resolución de nombres en redes virtuales de Azure, incluido el uso de su propio servidor DNS, consulte [Resolución de nombres (DNS)](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

##Pasos siguientes

En este tutorial, ha aprendido a crear un clúster de HBase. Para obtener más información, consulte:

- [Introducción a HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md)
- [Configuración de la replicación geográfica de HBase en HDInsight](hdinsight-hbase-geo-replication.md)
- [Consulte Creación de clústeres de Hadoop en HDInsight.](hdinsight-provision-clusters.md)
- [Introducción al uso de HBase con Hadoop en HDInsight](hdinsight-hbase-tutorial-get-started.md)
- [Análisis de opiniones de Twitter con HBase en HDInsight](hdinsight-hbase-analyze-twitter-sentiment.md)
- [Información general sobre redes virtuales][vnet-overview]


[1]: http://azure.microsoft.com/services/virtual-network/
[2]: http://technet.microsoft.com/library/ee176961.aspx
[3]: http://technet.microsoft.com/library/hh847889.aspx

[hbase-get-started]: hdinsight-hbase-tutorial-get-started.md
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[vnet-overview]: ../virtual-network/virtual-networks-overview.md
[vm-create]: ../virtual-machines/virtual-machines-windows-tutorial.md

[azure-portal]: https://portal.azure.com
[azure-create-storageaccount]: ../storage-create-storage-account.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md#rdp

[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx


[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter


[powershell-install]: powershell-install-configure.md


[hdinsight-customize-cluster]: hdinsight-hadoop-customize-cluster.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-storage-powershell]: ../hdinsight-hadoop-use-blob-storage.md#powershell
[hdinsight-analyze-flight-delay-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md
[hdinsight-hive-odbc]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-hbase-replication-dns]: hdinsight-hbase-geo-replication-configure-DNS.md

[img-dns-surffix]: ./media/hdinsight-hbase-provision-vnet/DNSSuffix.png
[img-primary-dns-suffix]: ./media/hdinsight-hbase-provision-vnet/PrimaryDNSSuffix.png
[img-provision-cluster-page1]: ./media/hdinsight-hbase-provision-vnet/hbasewizard1.png "Detalles de aprovisionamiento para el nuevo clúster de HBase"
[img-provision-cluster-page5]: ./media/hdinsight-hbase-provision-vnet/hbasewizard5.png "Uso de la acción de script para personalizar un clúster de HBase"

[azure-preview-portal]: https://portal.azure.com

<!---HONumber=AcomDC_0218_2016-->