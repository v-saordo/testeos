<properties
	pageTitle="Configuración de un agente de escucha externo para grupos de disponibilidad AlwaysOn | Microsoft Azure"
	description="Este tutorial le guiará a través de los pasos necesarios para crear un agente de escucha de grupo de disponibilidad AlwaysOn en Azure que sea accesible desde el exterior usando la dirección IP virtual pública del servicio en la nube asociado."
	services="virtual-machines"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar"
	tags="azure-service-management" />
<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="02/03/2016"
	ms.author="jroth" />

# Configuración de un agente de escucha externo para grupos de disponibilidad AlwaysOn en Azure

> [AZURE.SELECTOR]
- [Internal Listener](virtual-machines-sql-server-configure-ilb-alwayson-availability-group-listener.md)
- [External Listener](virtual-machines-sql-server-configure-public-alwayson-availability-group-listener.md)

En este tema se muestra cómo configurar un agente de escucha para un grupo de disponibilidad AlwaysOn al que se tiene acceso externo en Internet. Esto se hace posible asociando la dirección **IP Virtual pública (VIP)** del servicio en la nube al agente de escucha.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo del Administrador de recursos.


El grupo de disponibilidad puede contener réplicas que son solo locales, solo de Azure o abarcan ambas, locales y de Azure, para configuraciones híbridas. Las réplicas de Azure pueden residir en la misma región o en varias regiones mediante varias redes virtuales (VNet). En los pasos siguientes se supone que ya tiene [configurado un grupo de disponibilidad](virtual-machines-sql-server-alwayson-availability-groups-gui.md) pero no un agente de escucha.

## Instrucciones y limitaciones de los agentes de escucha externos

Tenga en cuenta las siguientes instrucciones acerca del agente de escucha del grupo de disponibilidad en Azure cuando se implementa con la dirección VIP pública del servicio en la nube:

- El agente de escucha del grupo de disponibilidad es compatible con Windows Server 2008 R2, Windows Server 2012 y Windows Server 2012 R2.

- La aplicación cliente debe residir en un servicio en la nube diferente al que contiene el grupo de disponibilidad de las máquinas virtuales. Azure no es compatible con Direct Server Return cuando el cliente y el servidor se encuentran en el mismo servicio en la nube.

- Los pasos descritos en este artículo le mostrarán de forma predeterminada cómo configurar un agente de escucha para usar la dirección IP Virtual (VIP) del servicio en la nube. De todos modos, es posible reservar y crear varias direcciones VIP para el servicio en la nube. Esto le permitirá usar los pasos de este artículo para crear varios agentes de escucha asociados a una VIP diferente. Para obtener información sobre cómo crear varias direcciones VIP, consulte [Varias direcciones VIP por cada servicio en la nube](load-balancer-multivip.md).

- Si crea un agente de escucha para un entorno híbrido, la red local debe tener conectividad a Internet pública así como a la VPN de sitio a sitio con la red virtual de Azure. Cuando se encuentra en la subred de Azure, el agente de escucha del grupo de disponibilidad solo está disponible con la dirección IP pública del servicio en la nube respectivo.

- No se puede crear un agente de escucha externo en el mismo servicio en la nube donde también haya creado un agente de escucha interno mediante el Equilibrador de carga interno (ILB).

## Determinar la accesibilidad del agente de escucha

[AZURE.INCLUDE [ag-listener-accessibility](../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

Este artículo se centra en la creación de un agente de escucha que usa **equilibrio de carga externo**. Si quiere un agente de escucha que sea privado para su red virtual, vea la versión de este artículo que indica los pasos necesarios para configurar un [agente de escucha con ILB](virtual-machines-sql-server-configure-ilb-alwayson-availability-group-listener.md)

## Creación de extremos de máquina virtual de carga equilibrada con Direct Server Return

El equilibrio de carga externo usa la dirección IP virtual pública del servicio en la nube que hospeda las máquinas virtuales. Por lo que en este caso no tiene que crear ni configurar el equilibrador de carga.

[AZURE.INCLUDE [load-balanced-endpoints](../../includes/virtual-machines-ag-listener-load-balanced-endpoints.md)]

1. Copie el siguiente script de PowerShell en un editor de texto y establezca los valores de variable que se ajusten a su entorno (se han proporcionado los valores predeterminados para algunos parámetros). Tenga en cuenta que si el grupo de disponibilidad abarca regiones de Azure, debe ejecutar el script una vez en cada centro de datos del servicio en la nube y los nodos que residen en ese centro de datos.

		# Define variables
		$ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
		$AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas

		# Configure a load balanced endpoint for each node in $AGNodes, with direct server return enabled
		ForEach ($node in $AGNodes)
		{
		    Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -Protocol "TCP" -PublicPort 1433 -LocalPort 1433 -LBSetName "ListenerEndpointLB" -ProbePort 59999 -ProbeProtocol "TCP" -DirectServerReturn $true | Update-AzureVM
		}

1. Una vez configuradas las variables, copie el script del editor de texto en la sesión de Azure PowerShell para ejecutarlo. Si el mensaje todavía muestra >>, escriba ENTER de nuevo para asegurarse de que el script comienza a ejecutarse.

## Comprobación de que KB2854082 está instalado si es necesario.

[AZURE.INCLUDE [kb2854082](../../includes/virtual-machines-ag-listener-kb2854082.md)]

## Apertura de los puertos de firewall en los nodos de grupo de disponibilidad

[AZURE.INCLUDE [firewall](../../includes/virtual-machines-ag-listener-open-firewall.md)]

## Creación del agente de escucha de grupo de disponibilidad

[AZURE.INCLUDE [firewall](../../includes/virtual-machines-ag-listener-create-listener.md)]

1. Para el equilibrio de carga externo, debe obtener la dirección IP virtual pública del servicio en la nube que contiene las réplicas. Inicie sesión en el Portal de Azure clásico. Navegue hasta el servicio en la nube que contiene la máquina virtual del grupo de disponibilidad. Abra la vista **Panel**.

3. Tome nota de la dirección que aparece en **Dirección IP virtual (VIP) pública**. Si la solución abarca redes virtuales, repita este paso para cada servicio en la nube que contenga una máquina virtual que hospeda una réplica.

4. En una de las máquinas virtuales, copie el siguiente script de PowerShell en un editor de texto y establezca las variables en los valores que anotó anteriormente.

		# Define variables
		$ClusterNetworkName = "<ClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
		$IPResourceName = "<IPResourceName>" # the IP Address resource name
		$CloudServiceIP = "<X.X.X.X>" # Public Virtual IP (VIP) address of your cloud service

		Import-Module FailoverClusters

		# If you are using Windows Server 2012 or higher, use the Get-Cluster Resource command. If you are using Windows Server 2008 R2, use the cluster res command. Both commands are commented out. Choose the one applicable to your environment and remove the # at the beginning of the line to convert the comment to an executable line of code.

		# Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$CloudServiceIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"OverrideAddressMatch"=1;"EnableDhcp"=0}
		# cluster res $IPResourceName /priv enabledhcp=0 overrideaddressmatch=1 address=$CloudServiceIP probeport=59999  subnetmask=255.255.255.255


1. Una vez establecidas las variables, abra una ventana de Windows PowerShell con privilegios elevados, copie el script del editor de texto y péguelo en la sesión de Azure PowerShell para ejecutarlo. Si el mensaje todavía muestra >>, escriba ENTER de nuevo para asegurarse de que el script comienza a ejecutarse.

1. Repita este paso en cada máquina virtual. Este script configura el recurso de dirección IP con la dirección IP del servicio en la nube y establece otros parámetros como, por ejemplo, el puerto de sondeo. El recurso de dirección IP, una vez conectado, puede responder al sondeo en el puerto de sondeo del extremo con equilibrio de carga que se creó anteriormente en este tutorial.

## Conexión del agente de escucha

[AZURE.INCLUDE [Bring-Listener-Online](../../includes/virtual-machines-ag-listener-bring-online.md)]

## Elementos de seguimiento

[AZURE.INCLUDE [Follow-up](../../includes/virtual-machines-ag-listener-follow-up.md)]

## Prueba del agente de escucha del grupo de disponibilidad (dentro de la misma red virtual)

[AZURE.INCLUDE [Test-Listener-Within-VNET](../../includes/virtual-machines-ag-listener-test.md)]

## Prueba del agente de escucha del grupo de disponibilidad (por Internet)

Para obtener acceso al agente de escucha desde el exterior de la red virtual, debe usar equilibrio de carga público o externo (descrito en este tema) en lugar de ILB, que es accesible únicamente dentro de la misma red virtual. En la cadena de conexión, especifique el nombre del servicio en la nube. Por ejemplo, si tiene un servicio en la nube denominado *mycloudservice*, la instrucción sqlcmd sería tal como sigue:

	sqlcmd -S "mycloudservice.cloudapp.net,<EndpointPort>" -d "<DatabaseName>" -U "<LoginId>" -P "<Password>"  -Q "select @@servername, db_name()" -l 15

A diferencia del ejemplo anterior, se debe usar autenticación de SQL, ya que el autor de la llamada no puede usar la autenticación de Windows por Internet. Para obtener más información, consulte [Grupo de disponibilidad AlwaysOn en la máquina virtual de Azure: escenarios de conectividad de cliente](http://blogs.msdn.com/b/sqlcat/archive/2014/02/03/alwayson-availability-group-in-windows-azure-vm-client-connectivity-scenarios.aspx). Al usar la autenticación de SQL, asegúrese de que crea el mismo inicio de sesión en ambas réplicas. Para obtener más información sobre cómo solucionar problemas de inicio de sesión con grupos de disponibilidad, consulte [Asignar inicios de sesión o usar un usuario de base de datos SQL contenido para conectar con otras réplicas y asignarlas a las bases de datos de disponibilidad](http://blogs.msdn.com/b/alwaysonpro/archive/2014/02/19/how-to-map-logins-or-use-contained-sql-database-user-to-connect-to-other-replicas-and-map-to-availability-databases.aspx).

Si las réplicas AlwaysOn están en subredes diferentes, los clientes tendrán que especificar **MultisubnetFailover=True** en la cadena de conexión. Esto se traduce en intentos de conexión en paralelo con las réplicas de las diferentes subredes. Tenga en cuenta que este escenario incluye una implementación de grupo de disponibilidad AlwaysOn entre regiones.

## Pasos siguientes

[AZURE.INCLUDE [Listener-Next-Steps](../../includes/virtual-machines-ag-listener-next-steps.md)]

<!---HONumber=AcomDC_0204_2016-->