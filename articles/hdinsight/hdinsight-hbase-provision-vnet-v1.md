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
   ms.date="12/02/2015"
   ms.author="jgao"/>

# Aprovisionamiento de clústeres de HBase en Red virtual de Azure

Aprenda a crear clústeres de HBase en Azure HDInsight en una [red virtual de Azure][1].

[AZURE.INCLUDE [hdinsight-azure-portal](../../includes/hdinsight-azure-portal.md)]

* [Aprovisionamiento de clústeres de HBase en Red virtual de Azure](hdinsight-hbase-provision-vnet.md)

Con la integración de red virtual, los clústeres de HBase se pueden implementar en la misma red que sus aplicaciones para que estas puedan comunicarse directamente con HBase. Entre las ventajas se incluye lo siguiente:

- Conectividad directa entre la aplicación web y los nodos del clúster de HBase, lo cual permite la comunicación mediante las API de llamada a procedimiento remoto (RPC) de Java de HBase.
- Rendimiento mejorado gracias a que el tráfico ya no tiene que examinar varias puertas de enlace y equilibradores de carga.
- La posibilidad de procesar información confidencial de una manera segura sin exponer un extremo público.

##Requisitos previos
Antes de empezar este tutorial, debe contar con lo siguiente:

- **Una suscripción de Azure**. Consulte [Obtención de una versión de evaluación gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Una estación de trabajo con Azure PowerShell**. Consulte [Install and use Azure PowerShell (Instalación y uso de Azure PowerShell)](https://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/). Para obtener más información, consulte [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md). Para ejecutar scripts de Azure PowerShell, debe ejecutar Azure PowerShell como administrador y establecer la directiva de ejecución en *RemoteSigned*. Consulte [Uso del cmdlet Set-ExecutionPolicy][2].

	Antes de ejecutar scripts de Azure PowerShell, asegúrese de estar conectado a su suscripción de Azure mediante el siguiente cmdlet:

		Add-AzureAccount

	Si tiene varias suscripciones a Azure, utilice el siguiente cmdlet para definir la suscripción actual:

		Select-AzureSubscription <AzureSubscriptionName>


##Aprovisionamiento de clústeres de HBase en una red virtual

Antes de aprovisionar un clúster de HBase, debe tener una red virtual de Azure.

**Para crear una red virtual usando el Portal de Azure clásico, siga estos pasos:**

1. Inicie sesión en el [Portal de Azure clásico][azure-portal].
2. En la esquina inferior izquierda, haga clic en **NUEVO**, **SERVICIOS DE RED**, **RED VIRTUAL** y, a continuación, en **CREACIÓN RÁPIDA**.
3. Escriba o seleccione los valores siguientes:

	- **Nombre**: el nombre de la red virtual.
	- **Espacio de direcciones**: elija un espacio de direcciones para la red virtual que sea lo bastante grande para proporcionar direcciones para todos los nodos del clúster. De lo contrario, se producirá un error en el aprovisionamiento. Para completar este tutorial, puede elegir cualquiera de las tres opciones.
	- **Número máximo de VM**: elija uno de los números máximos de máquinas virtuales (VM). Este valor determina el número de hosts (VM) posibles que se pueden crear en el espacio de direcciones. Para realizar este tutorial, es suficiente con **4096 [CIDR: /20]**.
	- **Ubicación**: la ubicación debe ser la misma que el clúster de HBase que va a crear
	- **Servidor DNS**: este tutorial usa un servidor de Sistema de nombres de dominio (DNS) interno proporcionado por Azure, de modo que pueda elegir **Ninguno**. Asimismo, se admiten configuraciones de red más avanzadas con servidores DNS personalizados. Para obtener instrucciones detalladas, consulte [Resolución de nombres (DNS)](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).
4. Haga clic en **CREAR UNA RED VIRTUAL** en la esquina inferior derecha. El nombre de la nueva red virtual aparecerá en la lista. Espere hasta que aparezca **Creado** en la columna de estado.
5. En el panel principal, haga clic en la red virtual que acaba de crear.
6. En la parte superior de la página, haga clic en **PANEL**.
7. En **vista rápida**, tome nota del identificador de red virtual. Lo necesitará al aprovisionar el clúster de HBase.
8. En la parte superior de la página, haga clic en **CONFIGURAR**.
9. En la parte inferior de la página, el nombre de subred predeterminado es **Subred-1**. Opcionalmente, puede cambiar el nombre de la subred o agregar una nueva subred para el clúster de HBase. Tome nota del nombre de subred; lo necesitará al aprovisionar el clúster.
10. Compruebe **CIDR(RECUENTO DE DIRECCIONES)** para la subred que se usará para el clúster. El recuento de direcciones debe ser mayor que el número de nodos de trabajo más siete (puerta de enlace: 2, nodos principal: 2, Zookeeper: 3). Por ejemplo, si necesita un clúster de HBase de 10 nodos, el recuento de direcciones para la subred debe ser mayor que 17 (10+7). De lo contrario, se producirá un error en la implementación.
11. Haga clic en **Guardar** en la parte inferior de la página, si ha actualizado los valores de subred.


**Para agregar una máquina virtual de servidor DNS a la red virtual, siga estos pasos:**

Un servidor DNS es opcional, pero es necesario en algunos casos. El procedimiento se ha documentado en [Configuración de DNS entre dos redes virtuales de Azure][hdinsight-hbase-replication-dns]. Básicamente, será necesario llevar a cabo estos pasos:

1. Agregar una máquina virtual de Azure a una red virtual
2. Configurar una dirección IP estática para la máquina virtual
3. Agregar el rol de servidor DNS a la máquina virtual
4. Asignar el servidor DNS a la red virtual


**Para crear una cuenta de almacenamiento de Azure y un contenedor de almacenamiento de blobs que será usado por el clúster, siga estos pasos:**

> [AZURE.NOTE] Los clústeres de HDInsight usan el almacenamiento de blobs de Azure para almacenar datos. Para obtener más información, consulte [Uso del almacenamiento de blobs de Azure con Hadoop en HDInsight](hdinsight-hadoop-use-blob-storage.md). Necesitará una cuenta de almacenamiento y un contenedor de almacenamiento de blobs. La ubicación de la cuenta de almacenamiento debe coincidir con la ubicación de la red virtual y del clúster.

Al igual que otros clústeres de HDInsight, el clúster de HBase requiere una cuenta de almacenamiento de Azure y un contenedor de almacenamiento de blobs como sistema de archivos predeterminado. La ubicación de la cuenta de almacenamiento debe coincidir con la ubicación de la red virtual y del clúster. Para obtener más información, consulte [Uso del almacenamiento de blobs de Azure con Hadoop en HDInsight][hdinsight-storage]. Cuando aprovisiona un clúster de HBase, tiene las opciones para crear unos nuevos o utilizar los existentes. Este procedimiento muestra cómo crear una cuenta de almacenamiento y un contenedor de almacenamiento de blobs mediante el Portal de Azure clásico.

1. Inicie sesión en el [Portal de Azure clásico][azure-portal].
2. Haga clic en **NUEVO** en la esquina inferior izquierda, seleccione **SERVICIOS DE DATOS**, **ALMACENAMIENTO** y luego haga clic en **CREACIÓN RÁPIDA**.

3. Escriba o seleccione los valores siguientes:

	- **URL**: el nombre de la cuenta de almacenamiento.
	- **UBICACIÓN**: la ubicación de la cuenta de almacenamiento. Asegúrese de que coincida con la ubicación de la red virtual. No se admiten grupos de afinidad.
	- **REPLICACIÓN**: con fines de evaluación, use **Localmente redundante** para reducir el coste.

4. Haga clic en **CREAR CUENTA DE ALMACENAMIENTO**. La nueva cuenta de almacenamiento aparecerá en la lista de almacenamiento.
5. Espere hasta que el **ESTADO** de la nueva cuenta de almacenamiento cambie a **En línea**.
6. Haga clic en la nueva cuenta de almacenamiento en la lista para seleccionarla.
7. Haga clic en **ADMINISTRAR CLAVES DE ACCESO** en la parte inferior de la página.
8. Anote el nombre de la cuenta de almacenamiento y la clave de acceso principal (o la secundaria; cualquiera de las dos sirve). Los necesitará más adelante en el tutorial.
9. En la parte superior de la página, haga clic en **CONTENEDOR**.
10. En la parte inferior de la página, haga clic en **AGREGAR**.
11. Escriba el nombre del contenedor. Este contenedor se usará como contenedor predeterminado del clúster de HBase. De forma predeterminada, el nombre del contenedor predeterminado coincide con el nombre del clúster. Conserve el campo **ACCESO** como **Privado**.  
12. Haga clic en la marca de verificación para crear el contenedor.

**Para aprovisionar un clúster de HBase usando el Portal de Azure clásico, siga estos pasos:**

> [AZURE.NOTE] Para obtener más información acerca del aprovisionamiento de un nuevo clúster de HBase usando Azure PowerShell, consulte [Aprovisionamiento de un clúster de HBase usando Azure PowerShell](#powershell).

1. Inicie sesión en el [Portal de Azure clásico][azure-portal].

2. Haga clic en **NUEVO** en la esquina inferior izquierda, elija **SERVICIOS DE DATOS**, **HDINSIGHT** y haga clic en **CREACIÓN PERSONALIZADA**.

3. Escriba un nombre de clúster, seleccione HBase como tipo de clúster, seleccione el sistema operativo Windows Server 2012, seleccione la versión de HDInsight y, a continuación, haga clic con el botón derecho.

	![Proporcionar detalles para el clúster de HBase][img-provision-cluster-page1]


	> [AZURE.NOTE] Para un clúster de HBase, Windows Server es la única opción de sistema operativo disponible.

4. En la página **Configurar clúster**, escriba o seleccione los valores siguientes:

	![Proporcionar detalles para el clúster de HBase](./media/hdinsight-hbase-provision-vnet/hbasewizard2.png)

	<table border='1'>
	<tr><th>Propiedad</th><th>Valor</th></tr>
	<tr><td>Nodos de datos</td><td>Seleccione el número de nodos de datos que desea implementar. Para propósitos de prueba, cree un clúster de un solo nodo. <br />El límite de tamaño del clúster varía según las suscripciones a Azure. Póngase en contacto con el servicio de soporte relacionado con la facturación de Azure para aumentar el límite.</td></tr>
	<tr><td>Región/Red virtual</td><td><p>Seleccione una región o una red virtual de Azure, si ha creado ya una. Para este tutorial, seleccione la red que creó anteriormente y, a continuación, seleccione una subred correspondiente. El nombre predeterminado es <b>Subred-1</b>.</p></td></tr>
	<tr><td>Tamaño de nodo principal</td><td><p>Seleccione un tamaño de máquina virtual para el nodo principal.</p></td></tr>
	<tr><td>Tamaño de nodo de datos</td><td><p>Seleccione un tamaño de máquina virtual para los nodos de datos.</p></td></tr>
	<tr><td>Tamaño de Zookeeper</td><td><p>Seleccione un tamaño de máquina virtual para el nodo Zookeeper.</p></td></tr>
</table>
>[AZURE.NOTE] En función de la elección de máquinas virtuales, su coste puede variar. HDInsight usa todas las máquinas virtuales de nivel estándar para los nodos del clúster. Para obtener información sobre cómo afectan los tamaños de máquinas virtuales a los precios, consulte <a href="http://azure.microsoft.com/pricing/details/hdinsight/" target="_blank">Precios de HDInsight</a>.

	Haga clic en el botón derecho.

5. Escriba el nombre de usuario y la contraseña que se va a usar para este clúster y, a continuación, haga clic con el botón derecho.

	![Proporcionar la cuenta de almacenamiento del clúster de HDInsight de Hadoop](./media/hdinsight-hbase-provision-vnet/hbasewizard3.png)

	<table border='1'>
	<tr><th>Propiedad</th><th>Valor</th></tr>
	<tr><td>Nombre de usuario HTTP</td>
		<td>Especifique el nombre del usuario del clúster de HDInsight.</td></tr>
	<tr><td>Contraseña HTTP/Confirmar contraseña</td>
		<td>Especifique la contraseña del usuario del clúster de HDInsight.</td></tr>
	<tr><td>Habilitar Escritorio remoto para un clúster</td>
		<td>Active esta casilla para especificar un nombre de usuario, una contraseña y una fecha de caducidad para un usuario de Escritorio remoto que puede conectarse en remoto a los nodos del clúster, cuando esté aprovisionado. También puede habilitar Escritorio remoto más adelante, cuando el clúster esté aprovisionado. Para obtener instrucciones, vea <a href="hdinsight-administer-use-management-portal/#rdp" target="_blank">Conexión a los clústeres de HDInsight con RDP</a>.</td></tr>
</table>

6. En la página **Cuenta de almacenamiento**, proporcione los siguientes valores:

    ![Proporcionar la cuenta de almacenamiento del clúster de HDInsight de Hadoop](./media/hdinsight-hbase-provision-vnet/hbasewizard4.png)

	<table border='1'>
	<tr><th>Propiedad</th><th>Valor</th></tr>
	<tr><td>Cuenta de almacenamiento</td>
		<td>Especifique la cuenta de almacenamiento de Azure que se usará como sistema de archivos predeterminado para el clúster de HDInsight. Puede elegir una de las tres opciones siguientes:
		<ul>
			<li><strong>Usar almacenamiento existente</strong></li>
			<li><strong>Crear nuevo almacenamiento</strong></li>
			<li><strong>Usar almacenamiento de otra suscripción</strong></li>
		</ul>
		</td></tr>
	<tr><td>Nombre de cuenta</td>
		<td><ul>
			<li>Si decidió utilizar almacenamiento existente, en <strong>Nombre de cuenta</strong>, seleccione una cuenta de almacenamiento existente. En la lista desplegable solamente aparecen las cuentas de almacenamiento ubicadas en el mismo centro de datos en el que eligió aprovisionar el clúster.</li>
			<li>Si eligió la opción <strong>Crear nuevo almacenamiento</strong> o  <strong>Usar almacenamiento de otra suscripción</strong>, debe proporcionar el nombre de la cuenta de almacenamiento.</li>
		</ul></td></tr>
	<tr><td>Clave de cuenta</td>
		<td>Si eligió la opción <strong>Usar almacenamiento de otra suscripción</strong>, especifique la clave de cuenta para esa cuenta de almacenamiento.</td></tr>
	<tr><td>Contenedor predeterminado</td>
		<td><p>Especifique el contenedor predeterminado en la cuenta de almacenamiento que se utiliza como sistema de archivos predeterminado para el clúster de HDInsight. Si eligió <strong>Usar almacenamiento existente</strong> para el campo <strong>Cuenta de almacenamiento</strong> y no existen contenedores en esa cuenta, el contenedor se creará de forma predeterminada con el mismo nombre que el del clúster. Si ya existe un contenedor con el nombre del clúster, se anexará un número de secuencia al nombre del contenedor. Por ejemplo, mycontainer1, mycontainer2 y así sucesivamente. Sin embargo, si la cuenta de almacenamiento existente tiene un contenedor con un nombre diferente al del clúster especificado, también puede usar ese contenedor.</p>
        <p>Si eligió crear un almacenamiento nuevo o usar almacenamiento de otra suscripción de Azure, debe especificar el nombre del contenedor predeterminado.</p>
    </td></tr>
	<tr><td>Cuentas de almacenamiento adicionales</td>
		<td>Si es necesario, especifique cuentas de almacenamiento adicionales para el clúster. HDInsight admite varias cuentas de almacenamiento. No hay límite en el número de cuentas de almacenamiento adicionales que un clúster puede usar. No obstante, si crea un clúster mediante el Portal de Azure clásico, tendrá un límite de siete debido a las restricciones de la interfaz de usuario. Por cada cuenta de almacenamiento adicional que especifique, se agregará una página <strong>Cuenta de almacenamiento</strong> adicional al asistente donde podrá especificar la información de la cuenta. Por ejemplo, en la captura de pantalla anterior, se selecciona una cuenta de almacenamiento adicional y, por tanto, se agrega una página adicional al asistente.</td></tr>
	</table>

	Haga clic en la flecha derecha.

7. En la página **Acciones de scripts**, seleccione la marca de verificación en la esquina inferior derecha. No haga clic en el botón para **agregar acción de script**, ya que este tutorial no requiere una configuración de clúster personalizada.

	![Configurar la acción de script para personalizar un clúster de HBase de HDInsight][img-provision-cluster-page5]

	> [AZURE.NOTE] Esta página puede utilizarse para personalizar el clúster durante la configuración. Para obtener más información, consulte [Personalización de clústeres de HDInsight mediante la acción de script](hdinsight-hadoop-customize-cluster.md).

Para comenzar a trabajar con el nuevo clúster de HBase, utilice los procedimientos que encontrará en [Introducción al uso de HBase con Hadoop en HDInsight](hdinsight-hbase-tutorial-get-started.md).

##Conexión al clúster de HBase aprovisionado en la red virtual mediante las API RPC de Java de HBase

1.	Aprovisione una máquina virtual de infraestructura como servicio (IaaS) en la misma red virtual de Azure y la misma subred. De este modo, tanto la máquina virtual como el clúster de HBase usan el mismo servidor DNS interno para resolver nombres de host. Para ello, debe elegir la opción **Desde la galería** y seleccionar la red virtual en lugar de un centro de datos. Para obtener instrucciones, consulte [Creación de una máquina virtual que ejecuta Windows Server](../virtual-machines/virtual-machines-windows-tutorial.md). Una imagen de Windows Server 2012 estándar con una VM pequeña es suficiente.

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

	> [AZURE.NOTE] Puede usar también Escritorio remoto para conectarse al clúster de HBase (se conectará al nodo principal) y ejecutar **ipconfig** desde el símbolo del sistema para obtener el sufijo DNS. Para obtener instrucciones sobre cómo habilitar el Protocolo de escritorio remoto (RDP) y conectarse al clúster mediante RDP, consulte [Administración de clústeres de Hadoop en HDInsight mediante el Portal de Azure clásico][hdinsight-admin-portal].
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

##Aprovisionamiento de un clúster de HBase mediante Azure PowerShell

**Para aprovisionar un clúster de HBase mediante Azure PowerShell, siga estos pasos:**

1. Abra el entorno de script integrado de Azure PowerShell (ISE):
2. Copie y pegue lo siguiente en el panel de scripts:

		$hbaseClusterName = "<HBaseClusterName>"
		$hadoopUserName = "<HBaseClusterUsername>"
		$hadoopUserPassword = "<HBaseClusterUserPassword>"
		$location = "<HBaseClusterLocation>"  #i.e. "West US"
		$clusterSize = <HBaseClusterSize>  
		$vnetID = "<AzureVirtualNetworkID>"
		$subNetName = "<AzureVirtualNetworkSubNetName>"
		$storageAccountName = "<AzureStorageAccountName>" # Do not use the full name here
		$storageAccountKey = "<AzureStorageAccountKey>"
		$storageContainerName = "<AzureBlobStorageContainer>"

		$password = ConvertTo-SecureString $hadoopUserPassword -AsPlainText -Force
		$creds = New-Object System.Management.Automation.PSCredential ($hadoopUserName, $password)

		New-AzureHDInsightCluster -Name $hbaseClusterName `
		                          -ClusterType HBase `
		                          -Version 3.1 `
		                          -Location $location `
		                          -ClusterSizeInNodes $clusterSize `
		                          -Credential $creds `
		                          -VirtualNetworkId $vnetID `
		                          -SubnetName $subNetName `
		                          -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
		                          -DefaultStorageAccountKey $storageAccountKey `
		                          -DefaultStorageContainerName $storageContainerName


3. Haga clic en **Ejecutar script** o pulse o **F5**.
4. Para validar el clúster, puede comprobar el clúster desde el Portal de Azure clásico o ejecutar el siguiente cmdlet de Azure PowerShell en el panel inferior:

	Get-AzureHDInsightCluster

##Pasos siguientes

En este tutorial, ha aprendido a aprovisionar un clúster de HBase. Para obtener más información, consulte:

- [Introducción a HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md)
- [Configuración de la replicación de HBase en HDInsight](hdinsight-hbase-geo-replication.md)
- [Aprovisionamiento de clústeres de Hadoop en HDInsight](hdinsight-provision-clusters.md)
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

[azure-portal]: https://management.windowsazure.com
[azure-create-storageaccount]: ../storage-create-storage-account.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md#rdp

[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx


[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter


[powershell-install]: ../powershell-install-configure.md


[hdinsight-customize-cluster]: hdinsight-hadoop-customize-cluster.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-storage-powershell]: hdinsight-hadoop-use-blob-storage.md#powershell
[hdinsight-analyze-flight-delay-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md
[hdinsight-hive-odbc]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-hbase-replication-dns]: hdinsight-hbase-geo-replication-configure-DNS.md

[img-dns-surffix]: ./media/hdinsight-hbase-provision-vnet/DNSSuffix.png
[img-primary-dns-suffix]: ./media/hdinsight-hbase-provision-vnet/PrimaryDNSSuffix.png
[img-provision-cluster-page1]: ./media/hdinsight-hbase-provision-vnet/hbasewizard1.png "Detalles de aprovisionamiento para el nuevo clúster de HBase"
[img-provision-cluster-page5]: ./media/hdinsight-hbase-provision-vnet/hbasewizard5.png "Uso de la acción de script para personalizar un clúster de HBase"

<!---HONumber=AcomDC_0218_2016-->