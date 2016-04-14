<properties pageTitle="Configuración de la tunelización forzada para puertas de enlace de VPN mediante el Administrador de recursos | Microsoft Azure" description="Si tiene una red virtual con una puerta de enlace VPN entre entornos, puede redirigir o "forzar" todo el tráfico enlazado a Internet de nuevo a la ubicación local. Este artículo se aplica al modelo de implementación del Administrador de recursos. " services="vpn-gateway" documentationCenter="na" authors="cherylmc" manager="carolz" editor="" tags="azure-resource-manager"/>
<tags  
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/17/2015"
   ms.author="cherylmc" />

# Configuración de tunelización forzada mediante PowerShell y el Administrador de recursos de Azure

> [AZURE.SELECTOR]
- [PowerShell - Service Management](vpn-gateway-about-forced-tunneling.md)
- [PowerShell - Resource Manager](vpn-gateway-forced-tunneling-rm.md)

Este artículo se aplica a las redes virtuales y a las puertas de enlace de VPN creadas con el modelo de implementación del Administrador de recursos de Azure. Si desea configurar la tunelización forzada para redes virtuales que se crearon con Administración de servicios (lo que se conoce también como modelo de implementación clásica), consulte [Configuración de la tunelización forzada](vpn-gateway-about-forced-tunneling.md).

[AZURE.INCLUDE [vpn-gateway-sm-rm](../../includes/vpn-gateway-sm-rm-include.md)]

## Información acerca de la tunelización forzada

La tunelización forzada permite redirigir o forzar todo el tráfico vinculado a Internet de vuelta a su ubicación local a través de un túnel VPN de sitio a sitio para inspección y auditoría. Se trata de un requisito de seguridad crítico en la mayoría de las directivas de las empresas de TI. Sin la tunelización forzada, el tráfico vinculado a Internet desde las máquinas virtuales en Azure siempre atravesará desde la infraestructura de red de Azure directamente a Internet, sin la opción que permite inspeccionar o auditar el tráfico. Un acceso no autorizado a Internet puede provocar la divulgación de información u otros tipos de infracciones de seguridad.

El siguiente diagrama ilustra cómo funciona la tunelización forzada.

![Tunelización forzada](./media/vpn-gateway-forced-tunneling-rm/forced-tunnel.png)

En el ejemplo anterior, la subred Frontend no usa la tunelización forzada. Las cargas de trabajo de la subred Frontend pueden continuar para aceptar y responder a las solicitudes de los clientes directamente desde Internet. Las subredes Mid-tier y Backend usan la tunelización forzada. Las conexiones salientes desde estas dos subredes a Internet se fuerzan o redirigen a un sitio local a través de uno de los túneles VPN de sitio a sitio. Esto permite restringir e inspeccionar el acceso a Internet desde sus máquinas virtuales o servicios en la nube en Azure, al tiempo que continúa la habilitación de la arquitectura de varios niveles de servicio necesaria. También tiene la opción de aplicar la tunelización forzada a redes virtuales completas si no hay ninguna carga de trabajo a través de Internet en las redes virtuales.

## Requisitos y consideraciones

La tunelización forzada en Azure se configura a través de rutas definidas por el usuario de redes virtuales. La redirección del tráfico a un sitio local se expresa como una ruta predeterminada a la puerta de enlace de VPN de Azure. Para más información sobre las redes virtuales y las rutas definidas por el usuario, consulte [Rutas definidas por el usuario y reenvío de IP](../virtual-network/virtual-networks-udr-overview.md).

- Cada subred de la red virtual tiene una tabla de enrutamiento del sistema integrada. La tabla de enrutamiento del sistema tiene los siguientes tres grupos de rutas:

	- **Rutas de red virtual local**: directamente a las máquinas virtuales de destino en la misma red virtual.
	
	- **Rutas locales**: a la puerta de enlace de VPN de Azure.
	
	- **Ruta predeterminada**: directamente a Internet. Tenga en cuenta que los paquetes destinados a las direcciones IP privadas que no están cubiertos por las dos rutas anteriores se anularán.

-  Este procedimiento usa las rutas definidas por el usuario para crear una tabla de enrutamiento para agregar una ruta predeterminada y, a continuación, asociar la tabla de enrutamiento a las subredes de la red virtual para habilitar la tunelización forzada en esas subredes.

- La tunelización forzada debe asociarse a una red virtual que tenga una puerta de enlace de VPN basada en rutas. Deberá establecer un "sitio predeterminado" entre los sitios locales entre entornos conectados a la red virtual.

- Tenga en cuenta que la tunelización forzada ExpressRoute no se configura mediante este mecanismo, sino que se habilita mediante el anuncio de una ruta predeterminada a través de las sesiones de emparejamiento BGP de ExpressRoute. Consulte la [Documentación de ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) para más información.

## Configuración de la tunelización forzada

### Información general sobre la configuración

El procedimiento siguiente le ayudará a crear un grupo de recursos y una red virtual. A continuación, creará una puerta de enlace de VPN y configurará la tunelización forzada.

En el ejemplo, esta red virtual "MultiTier-VNet" tiene tres subredes: *Frontend*, *Midtier* y *Backend*, con cuatro conexiones entre entornos: *DefaultSiteHQ* y tres *Branches*. Los pasos del procedimiento establecerán *DefaultSiteHQ* como la conexión de sitio predeterminada para la tunelización forzada y configurarán las subredes *Midtier* y *Backend* para usar dicha tunelización.

	
### Antes de comenzar

Antes de comenzar con la configuración, comprueba que dispones de los elementos siguientes.

- Una suscripción de Azure. Si todavía no tiene una suscripción de Azure, puede activar sus [beneficios de suscripción a MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) o bien registrarse para obtener una [evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/).

- Cmdlets de Azure PowerShell (1.0 o posterior). Los cmdlets necesarios para esta configuración no están presentes en las versiones anteriores a la 1.0. Puede descargar e instalar esta versión desde la sección Windows PowerShell de la [página de descargas](https://azure.microsoft.com/downloads/). Si no está familiarizado con la instalación y configuración de PowerShell, vea [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md) para obtener más información.

### Pasos de configuración

1. En la consola de PowerShell, inicie sesión en su cuenta de Azure. El cmdlet le pedirá las credenciales de inicio de sesión para la cuenta de Azure. Después de iniciar la sesión, se descarga la configuración de la cuenta a fin de que esté disponible para Azure PowerShell.

		Login-AzureRmAccount 

2. Obtenga una lista de las suscripciones de Azure.

		Get-AzureRmSubscription

2. Especifique la suscripción que desea usar.

		Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
		
3. Cree un grupo de recursos.

		New-AzureRmResourceGroup -Name "ForcedTunneling" -Location "North Europe"

4. Cree una red virtual y especifique las subredes.

		$s1 = New-AzureRmVirtualNetworkSubnetConfig -Name "Frontend" -AddressPrefix "10.1.0.0/24"
		$s2 = New-AzureRmVirtualNetworkSubnetConfig -Name "Midtier" -AddressPrefix "10.1.1.0/24"
		$s3 = New-AzureRmVirtualNetworkSubnetConfig -Name "Backend" -AddressPrefix "10.1.2.0/24"
		$s4 = New-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix "10.1.200.0/28"
		$vnet = New-AzureRmVirtualNetwork -Name "MultiTier-VNet" -Location "North Europe" -ResourceGroupName "ForcedTunneling" -AddressPrefix "10.1.0.0/16" -Subnet $s1,$s2,$s3,$s4

5. Cree las puertas de enlace de red local.

		$lng1 = New-AzureRmLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.111" -AddressPrefix "192.168.1.0/24"
		$lng2 = New-AzureRmLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.112" -AddressPrefix "192.168.2.0/24"
		$lng3 = New-AzureRmLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.113" -AddressPrefix "192.168.3.0/24"
		$lng4 = New-AzureRmLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.114" -AddressPrefix "192.168.4.0/24"
		
6. Cree la tabla de enrutamiento y la regla de enrutamiento.

		New-AzureRmRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" –Location "North Europe"
		$rt = Get-AzureRmRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" 
		Add-AzureRmRouteConfig -Name "DefaultRoute" -AddressPrefix "0.0.0.0/0" -NextHopType VirtualNetworkGateway -RouteTable $rt
		Set-AzureRmRouteTable -RouteTable $rt


6. Asocie la tabla de enrutamiento creada anteriormente a las subredes Midtier y Back-end.

		$vnet = Get-AzureRmVirtualNetwork -Name "MultiTier-Vnet" -ResourceGroupName "ForcedTunneling"
		Set-AzureRmVirtualNetworkSubnetConfig -Name "MidTier" -VirtualNetwork $vnet -AddressPrefix "10.1.1.0/24" -RouteTable $rt
		Set-AzureRmVirtualNetworkSubnetConfig -Name "Backend" -VirtualNetwork $vnet -AddressPrefix "10.1.2.0/24" -RouteTable $rt
		Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

9. Cree la puerta de enlace con un sitio predeterminado. Este paso tarda algún tiempo en completarse, a veces 20 minutos o más, dado que va a crear y configurar la puerta de enlace. Aunque parece que solo hay unos pocos cmdlets en la consola, no hay mucho movimiento en segundo plano. Tenga en cuenta que el GatewayDefaultSite es el parámetro de cmdlet que permite que la configuración de enrutamiento forzada funcione, por lo que no debería pasarla por alto. Solo está disponible en PowerShell 1.0 o posterior.

		$pip = New-AzureRmPublicIpAddress -Name "GatewayIP" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -AllocationMethod Dynamic
		$gwsubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
		$ipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name "gwIpConfig" -SubnetId $gwsubnet.Id -PublicIpAddressId $pip.Id
		New-AzureRmVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -IpConfigurations $ipconfig -GatewayType Vpn -VpnType RouteBased -GatewayDefaultSite $lng1 -EnableBgp $false

10. Establezca la conexión VPN de sitio a sitio.

		$gateway = Get-AzureRmVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling"
		$lng1 = Get-AzureRmLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" 
		$lng2 = Get-AzureRmLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" 
		$lng3 = Get-AzureRmLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" 
		$lng4 = Get-AzureRmLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" 

		New-AzureRmVirtualNetworkGatewayConnection -Name "Connection1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng1 -ConnectionType IPsec -SharedKey "preSharedKey"
		New-AzureRmVirtualNetworkGatewayConnection -Name "Connection2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng2 -ConnectionType IPsec -SharedKey "preSharedKey"
		New-AzureRmVirtualNetworkGatewayConnection -Name "Connection3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng3 -ConnectionType IPsec -SharedKey "preSharedKey"
		New-AzureRmVirtualNetworkGatewayConnection -Name "Connection4" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng4 -ConnectionType IPsec -SharedKey "preSharedKey"

		Get-AzureRmVirtualNetworkGatewayConnection -Name "Connection1" -ResourceGroupName "ForcedTunneling"
		

<!---HONumber=AcomDC_0204_2016-->