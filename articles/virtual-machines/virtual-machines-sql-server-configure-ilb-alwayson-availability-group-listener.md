<properties
	pageTitle="Configuración de un agente de escucha con ILB para grupos de disponibilidad AlwaysOn | Microsoft Azure"
	description="En este tutorial se usan los recursos creados con el modelo de implementación clásica, y crea un agente de escucha de grupo de disponibilidad AlwaysOn en Azure mediante un equilibrador de carga interno (ILB)."
	services="virtual-machines"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar"
	tags="azure-service-management"/>
<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="02/03/2016"
	ms.author="jroth" />

# Configuración de un agente de escucha con ILB para grupos de disponibilidad AlwaysOn en Azure

> [AZURE.SELECTOR]
- [Internal Listener](virtual-machines-sql-server-configure-ilb-alwayson-availability-group-listener.md)
- [External Listener](virtual-machines-sql-server-configure-public-alwayson-availability-group-listener.md)

## Información general

En este tema se muestra cómo configurar un agente de escucha para un grupo de disponibilidad AlwaysOn mediante un **equilibrador de carga interno (ILB)**.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] Modelo del Administrador de recursos.


El grupo de disponibilidad puede contener réplicas que son solo locales, solo de Azure o abarcan ambas, locales y de Azure, para configuraciones híbridas. Las réplicas de Azure pueden residir en la misma región o en varias regiones mediante varias redes virtuales (VNet). En los pasos siguientes se supone que ya tiene [configurado un grupo de disponibilidad](virtual-machines-sql-server-alwayson-availability-groups-gui.md) pero no un agente de escucha.

## Instrucciones y limitaciones de los agentes de escucha internos
Tenga en cuenta las siguientes instrucciones acerca del agente de escucha del grupo de disponibilidad en Azure mediante ILB:

- El agente de escucha del grupo de disponibilidad es compatible con Windows Server 2008 R2, Windows Server 2012 y Windows Server 2012 R2.

- Solo se admite un agente de escucha de grupo de disponibilidad interno para cada servicio en la nube, ya que el agente de escucha se configura según el ILB y solo hay un ILB por cada servicio en la nube. Sin embargo, es posible crear varios agentes de escucha externos. Para obtener más información, consulte [Configurar un agente de escucha externo para grupos de disponibilidad AlwaysOn en Azure](virtual-machines-sql-server-configure-public-alwayson-availability-group-listener.md).

- No se puede crear un agente de escucha interno en el mismo servicio en la nube donde también haya creado un agente de escucha externo mediante el servicio en la nube público VIP.

## Determinar la accesibilidad del agente de escucha

[AZURE.INCLUDE [ag-listener-accessibility](../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

Este artículo se centra en la creación de un agente de escucha que usa un **equilibrador de carga interno (ILB)**. Si necesita un agente de escucha público o externo, consulte la versión de este artículo que indica los pasos necesarios para configurar un [agente de escucha externo](virtual-machines-sql-server-configure-public-alwayson-availability-group-listener.md)

## Creación de extremos de máquina virtual de carga equilibrada con Direct Server Return

En ILB, debe crear primero el equilibrador de carga interno. Esto se hace en el script siguiente.

[AZURE.INCLUDE [load-balanced-endpoints](../../includes/virtual-machines-ag-listener-load-balanced-endpoints.md)]

1. En el **ILB**, debe asignar una dirección IP estática. En primer lugar, examine la configuración actual de la red virtual ejecutando el comando siguiente:

		(Get-AzureVNetConfig).XMLConfiguration

1. Tome nota del nombre de la **Subred** que contenga las máquinas virtuales que hospeden las réplicas. Este se usará en el parámetro **$SubnetName** en el script.

1. A continuación, en la subred que contiene las máquinas virtuales que hospedan las réplicas, tome nota del nombre del elemento **VirtualNetworkSite** y del elemento **AddressPrefix** inicial. Busque una dirección IP disponible pasando ambos valores al comando **Test-AzureStaticVNetIP** y examinando el parámetro **AvailableAddresses**. Por ejemplo, si el nombre de la red virtual fuera *MyVNet* y tuviera un intervalo de direcciones de subred que se empezase por *172.16.0.128*, el siguiente comando mostraría las direcciones disponibles:

		(Test-AzureStaticVNetIP -VNetName "MyVNet"-IPAddress 172.16.0.128).AvailableAddresses

1. Elija una de las direcciones disponibles y úsela en el parámetro **$ILBStaticIP** del script siguiente.

3. Copie el siguiente script de PowerShell en un editor de texto y establezca los valores de variable que se ajusten a su entorno (observe que se han proporcionado los valores predeterminados para algunos parámetros). Tenga en cuenta que las implementaciones existentes que usan grupos de afinidad no pueden agregar ILB. Para obtener más información sobre requisitos de ILB, vea [Equilibrador de carga interno](../load-balancer/load-balancer-internal-overview.md). Además, si el grupo de disponibilidad abarca regiones de Azure, debe ejecutar el script una vez en cada centro de datos del servicio en la nube y los nodos que residen en ese centro de datos.

		# Define variables
		$ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
		$AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas
		$SubnetName = "<MySubnetName>" # subnet name that the replicas use in the VNet
		$ILBStaticIP = "<MyILBStaticIPAddress>" # static IP address for the ILB in the subnet
		$ILBName = "AGListenerLB" # customize the ILB name or use this default value

		# Create the ILB
		Add-AzureInternalLoadBalancer -InternalLoadBalancerName $ILBName -SubnetName $SubnetName -ServiceName $ServiceName -StaticVNetIPAddress $ILBStaticIP

		# Configure a load balanced endpoint for each node in $AGNodes using ILB
		ForEach ($node in $AGNodes)
		{
			Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -LBSetName "ListenerEndpointLB" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -InternalLoadBalancerName $ILBName -DirectServerReturn $true | Update-AzureVM
		}

1. Una vez configuradas las variables, copie el script del editor de texto en la sesión de Azure PowerShell para ejecutarlo. Si el mensaje todavía muestra >>, escriba ENTER de nuevo para asegurarse de que el script comienza a ejecutarse. Nota

>[AZURE.NOTE] El Portal de Azure clásico no es compatible en este momento con el equilibrador de carga interno, por lo que no verá el ILB o los puntos de conexión en el Portal de Azure clásico. Sin embargo, el parámetro **Get-AzureEndpoint** devuelve una dirección IP interna si el equilibrador de carga se está ejecutando en ella. De lo contrario, devuelve null.

## Comprobación de que KB2854082 está instalado si es necesario.

[AZURE.INCLUDE [kb2854082](../../includes/virtual-machines-ag-listener-kb2854082.md)]

## Apertura de los puertos de firewall en los nodos de grupo de disponibilidad

[AZURE.INCLUDE [firewall](../../includes/virtual-machines-ag-listener-open-firewall.md)]

## Creación del agente de escucha de grupo de disponibilidad

[AZURE.INCLUDE [firewall](../../includes/virtual-machines-ag-listener-create-listener.md)]

1. En ILB, debe usar la dirección IP del equilibrador de carga interno (ILB) creado anteriormente. Use el script siguiente para obtener esta dirección IP en PowerShell.

		# Define variables
		$ServiceName="<MyServiceName>" # the name of the cloud service that contains the AG nodes
		(Get-AzureInternalLoadBalancer -ServiceName $ServiceName).IPAddress

1. En una de las máquinas virtuales, copie el siguiente script de PowerShell en un editor de texto y establezca las variables en los valores que anotó anteriormente.

		# Define variables
		$ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
		$IPResourceName = "<IPResourceName>" # the IP Address resource name
		$ILBIP = “<X.X.X.X>” # the IP Address of the Internal Load Balancer (ILB)

		Import-Module FailoverClusters

		# If you are using Windows Server 2012 or higher, use the Get-Cluster Resource command. If you are using Windows Server 2008 R2, use the cluster res command. Both commands are commented out. Choose the one applicable to your environment and remove the # at the beginning of the line to convert the comment to an executable line of code.

		# Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
		# cluster res $IPResourceName /priv enabledhcp=0 address=$ILBIP probeport=59999  subnetmask=255.255.255.255

1. Una vez establecidas las variables, abra una ventana de Windows PowerShell con privilegios elevados, copie el script del editor de texto y péguelo en la sesión de Azure PowerShell para ejecutarlo. Si el mensaje todavía muestra >>, escriba ENTER de nuevo para asegurarse de que el script comienza a ejecutarse.

2. Repita este paso en cada máquina virtual. Este script configura el recurso de dirección IP con la dirección IP del servicio en la nube y establece otros parámetros como, por ejemplo, el puerto de sondeo. El recurso de dirección IP, una vez conectado, puede responder al sondeo en el puerto de sondeo del extremo con equilibrio de carga que se creó anteriormente en este tutorial.

## Conexión del agente de escucha

[AZURE.INCLUDE [Bring-Listener-Online](../../includes/virtual-machines-ag-listener-bring-online.md)]

## Elementos de seguimiento

[AZURE.INCLUDE [Follow-up](../../includes/virtual-machines-ag-listener-follow-up.md)]

## Prueba del agente de escucha del grupo de disponibilidad (dentro de la misma red virtual)

[AZURE.INCLUDE [Test-Listener-Within-VNET](../../includes/virtual-machines-ag-listener-test.md)]

## Pasos siguientes

[AZURE.INCLUDE [Listener-Next-Steps](../../includes/virtual-machines-ag-listener-next-steps.md)]

<!------HONumber=AcomDC_0204_2016---->
