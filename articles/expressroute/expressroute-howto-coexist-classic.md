<properties
   pageTitle="Configuración de conexiones de ExpressRoute y VPN de sitio a sitio coexistentes | Microsoft Azure"
   description="Este artículo le guiará en la configuración de conexiones ExpressRoute y VPN de sitio a sitio que puedan coexistir en el modelo de implementación clásica."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/08/2016"
   ms.author="cherylmc"/>

# Configuración de conexiones coexistentes de ExpressRoute de sitio a sitio

Tener la posibilidad de configurar VPN de sitio a sitio y ExpressRoute tiene varias ventajas. Puede configurar una VPN de sitio a sitio como una ruta de acceso seguro de conmutación por error para ExressRoute, o bien usar VPN de sitio a sitio para conectarse a sitios que no forman parte de la red, pero que están conectados a través de ExpressRoute. En este artículo trataremos los pasos para configurar ambos escenarios. **Actualmente solo se puede crear esta configuración para redes virtuales mediante el modelo de implementación clásica**. Cuando tengamos documentación aplicable al modelo de implementación del Administrador de recursos, nos vincularemos a ella desde aquí.


**Información sobre los modelos de implementación de Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]


Los circuitos ExpressRoute deben configurarse previamente antes de seguir las instrucciones siguientes. Asegúrese de que ha seguido las guías para [crear un circuito ExpressRoute](expressroute-howto-circuit-classic.md) y [configurar el enrutamiento](expressroute-howto-routing-classic.md) antes de seguir los pasos que se indican a continuación.

## Límites y limitaciones

- **El enrutamiento de tránsito no se admite:** no se puede realizar un enrutamiento (mediante Azure) entre una red local conectada con una VPN de sitio a sitio y una red local conectada con ExpressRoute.
- **Punto a sitio no se admite:** no se pueden habilitar conexiones VPN de punto a sitio a la misma red virtual que esté conectada a ExpressRoute. VPN de punto a sitio y ExpressRoute no pueden coexistir en la misma red virtual.
- **No se puede habilitar la tunelización forzada en la puerta de enlace de VPN de sitio a sitio:** solo puede "forzar" todo el tráfico enlazado a Internet a la red local mediante ExpressRoute. 
- **Solo puertas de enlace de rendimiento alto o estándar:** debe usar una puerta de enlace de rendimiento alto o estándar para la puerta de enlace de ExpressRoute y la puerta de enlace de VPN de sitio a sitio. Para información sobre las SKU de puerta de enlace, consulte [SKU de puerta de enlace](../vpn-gateway/vpn-gateway-about-vpngateways.md).
- **Requisito de ruta estática**: si la red local está conectada a ExpressRoute y a una VPN de sitio a sitio, debe tener configurada una ruta estática en la red local para enrutar la conexión VPN de sitio a sitio a la red Internet pública.
- **Primero debe estar configurada la puerta de enlace de ExpressRoute:** debe crear la puerta de enlace de ExpressRoute antes de agregar la puerta de enlace de VPN de sitio a sitio.


## Diseños de configuración

### Configuración de una VPN de sitio a sitio como una ruta de acceso de conmutación por error para ExpressRoute

Puede configurar una conexión VPN de sitio a sitio como una copia de seguridad para ExpressRoute. Esto se aplica únicamente a las redes virtuales vinculadas a la ruta de acceso de emparejamiento privada de Azure. No hay ninguna solución de conmutación por error basada en VPN para servicios accesibles a través de emparejamientos de Microsoft y públicos de Azure. El circuito ExpressRoute siempre es el vínculo principal. Los datos fluirán a través de la ruta de acceso de VPN de sitio a sitio solo si se produce un error en el circuito ExpressRoute.

![Coexistencia](media/expressroute-howto-coexist-classic/scenario1.jpg)

### Configuración de una VPN de sitio a sitio para conectarse a sitios no conectados mediante ExpressRoute

Puede configurar la red para sitios que se conectan directamente a Azure mediante una VPN de sitio a sitio y otros que se conectan a través de ExpressRoute.

![Coexistencia](media/expressroute-howto-coexist-classic/scenario2.jpg)

>[AZURE.NOTE] No se puede configurar una red virtual como un enrutador de tránsito.

## Selección de los pasos a seguir

Hay dos conjuntos diferentes de los procedimientos entre los que elegir para configurar las conexiones que pueden coexistir. El procedimiento de configuración que seleccione dependerá de si ya tiene una red virtual existente a la que quiere conectarse o si desea crear una nueva red virtual.


- No tiene una red virtual y necesita crear una
	
	Si aún no tiene una red virtual, este procedimiento le guiará en la creación de una nueva red virtual mediante el modelo de implementación clásica y la creación de nuevas conexiones VPN de sitio a sitio y ExpressRoute. Para configurarla, siga los pasos que se describen en la sección del artículo [Creación de una nueva red virtual y conexiones coexistentes](#new).

- Ya tengo una red virtual con el modelo de implementación clásica

	Puede que ya tenga una red virtual con una conexión VPN de sitio a sitio o una conexión ExpressRoute existentes. La sección [Configuración de conexiones coexistentes para una red virtual existente](#add) le guiará en la eliminación de la puerta de enlace y la creación de nuevas conexiones VPN de sitio a sitio y ExpressRoute. Tenga en cuenta que al crear las nuevas conexiones, se deben completar los pasos en un orden muy específico. No utilice las instrucciones que aparecen en otros artículos para crear puertas de enlace y conexiones.

	En este procedimiento, para crear conexiones que puedan coexistir, tendrá que eliminar la puerta de enlace y luego configurar nuevas puertas de enlace. Esto significa que tendrá tiempo de inactividad para las conexiones entre entornos mientras elimina y vuelve a crear la puerta de enlace y las conexiones, pero no necesitará migrar las máquinas virtuales o servicios a una nueva red virtual. Las máquinas virtuales y los servicios podrán seguir comunicándose con el exterior a través del equilibrador de carga mientras configura la puerta de enlace si están configurados para ello.


## <a name ="new"/>Creación de una nueva red virtual y conexiones coexistentes

Este procedimiento le guiará en la creación de una red virtual y conexiones de sitio a sitio y ExpressRoute que coexistirán.

1. Necesitará instalar la versión más reciente de los cmdlets de PowerShell del Administrador de recursos de Azure. Consulte [Cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) para más información sobre cómo instalar los cmdlets de PowerShell. Tenga en cuenta que los cmdlets que se van a utilizar en esta configuración pueden ser ligeramente diferentes de aquellos con los que podría estar familiarizado. Asegúrese de usar los cmdlets especificados en estas instrucciones. 

2. Cree un esquema para la red virtual. Para más información sobre el esquema de configuración, consulte [Esquema de configuración de la red virtual de Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx).

	Al crear el esquema, asegúrese de que usa los valores siguientes:

	- La subred de puerta de enlace para la red virtual debe ser /27 o un prefijo más corto (como /26 o /25).
	- El tipo de conexión de puerta de enlace es "Dedicado".

		      <VirtualNetworkSite name="MyAzureVNET" Location="Central US">
		        <AddressSpace>
		          <AddressPrefix>10.17.159.192/26</AddressPrefix>
		        </AddressSpace>
		        <Subnets>
		          <Subnet name="Subnet-1">
		            <AddressPrefix>10.17.159.192/27</AddressPrefix>
		          </Subnet>
		          <Subnet name="GatewaySubnet">
		            <AddressPrefix>10.17.159.224/27</AddressPrefix>
		          </Subnet>
		        </Subnets>
		        <Gateway>
		          <ConnectionsToLocalNetwork>
		            <LocalNetworkSiteRef name="MyLocalNetwork">
		              <Connection type="Dedicated" />
		            </LocalNetworkSiteRef>
		          </ConnectionsToLocalNetwork>
		        </Gateway>
		      </VirtualNetworkSite>

3. Después de crear y configurar el archivo de esquema xml, cargue el archivo. Esto creará la red virtual.

	Use el cmdlet siguiente para cargar el archivo, reemplazando el valor por el suyo propio.

		Set-AzureVNetConfig -ConfigurationPath 'C:\NetworkConfig.xml'

4. <a name ="gw"/>Cree una puerta de enlace de ExpressRoute. Asegúrese de especificar GatewaySKU como *Standard* o *HighPerformance* y GatewayType como *DynamicRouting*.

	Use el ejemplo siguiente y sustituya los valores por los suyos propios.

		New-AzureVNetGateway -VNetName MyAzureVNET -GatewayType DynamicRouting -GatewaySKU HighPerformance

5. Vincule la puerta de enlace de ExpressRoute al circuito ExpressRoute. Una vez completado este paso, se ha establecido la conexión entre su red local y Azure a través de ExpressRoute.

		New-AzureDedicatedCircuitLink -ServiceKey <service-key> -VNetName MyAzureVNET

6. A continuación, cree la puerta de enlace de la VPN sitio a sitio. GatewaySKU debe ser *Standard* o *HighPerformance* y GatewayType debe ser *DynamicRouting*.

		New-AzureVirtualNetworkGateway -VNetName MyAzureVNET -GatewayName S2SVPN -GatewayType DynamicRouting -GatewaySKU  HighPerformance

	Para recuperar la configuración de la puerta de enlace de red virtual, incluido el identificador de la puerta de enlace y la dirección IP pública, use el cmdlet `Get-AzureVirtualNetworkGateway`.

		Get-AzureVirtualNetworkGateway

		GatewayId            : 348ae011-ffa9-4add-b530-7cb30010565e
		GatewayName          : S2SVPN
		LastEventData        :
		GatewayType          : DynamicRouting
		LastEventTimeStamp   : 5/29/2015 4:41:41 PM
		LastEventMessage     : Successfully created a gateway for the following virtual network: GNSDesMoines
		LastEventID          : 23002
		State                : Provisioned
		VIPAddress           : 104.43.x.y
		DefaultSite          :
		GatewaySKU           : HighPerformance
		Location             :
		VnetId               : 979aabcf-e47f-4136-ab9b-b4780c1e1bd5
		SubnetId             :
		EnableBgp            : False
		OperationDescription : Get-AzureVirtualNetworkGateway
		OperationId          : 42773656-85e1-a6b6-8705-35473f1e6f6a
		OperationStatus      : Succeeded

7. Cree una entidad de puerta de enlace de VPN de sitio local. Este comando no configura la puerta de enlace de VPN local. En su lugar, permite proporcionar la configuración de puerta de enlace local, como la dirección IP pública y el espacio de direcciones local, para que la puerta de enlace de VPN de Azure pueda conectarse a ella.

	> [AZURE.IMPORTANT] El sitio local de la VPN de sitio a sitio no está definido en netcfg. En su lugar, es preciso usar este cmdlet para especificar los parámetros del sitio local. No puede definirlos ni con el portal ni con el archivo netcfg.

	Use el ejemplo siguiente y reemplace los valores por los suyos propios:

	`New-AzureLocalNetworkGateway -GatewayName MyLocalNetwork -IpAddress <MyLocalGatewayIp> -AddressSpace <MyLocalNetworkAddress>`

	> [AZURE.NOTE] Si la red local tiene varias rutas, puede pasar todas ellas en una matriz. $MyLocalNetworkAddress = @("10.1.2.0/24","10.1.3.0/24","10.2.1.0/24")


	Para recuperar la configuración de la puerta de enlace de red virtual, lo que incluye el identificador de puerta de enlace y la dirección IP pública, use el cmdlet `Get-AzureVirtualNetworkGateway`. Consulte el ejemplo siguiente.

		Get-AzureLocalNetworkGateway

		GatewayId            : 532cb428-8c8c-4596-9a4f-7ae3a9fcd01b
		GatewayName          : MyLocalNetwork
		IpAddress            : 23.39.x.y
		AddressSpace         : {10.1.2.0/24}
		OperationDescription : Get-AzureLocalNetworkGateway
		OperationId          : ddc4bfae-502c-adc7-bd7d-1efbc00b3fe5
		OperationStatus      : Succeeded


8. Configure el dispositivo VPN local para que se conecte a la nueva puerta de enlace. Al configurar el dispositivo VPN, use la información que recuperó en el paso 6. Para obtener más información acerca de la configuración del dispositivo VPN, consulte [Configuración de dispositivos VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md).

9. Vincule la puerta de enlace de VPN sitio a sitio en Azure a la puerta de enlace local.

	En este ejemplo, connectedEntityId es el id. de puerta de enlace local, que se encuentra ejecutando `Get-AzureLocalNetworkGateway`. Para encontrar virtualNetworkGatewayId, use el cmdlet `Get-AzureVirtualNetworkGateway`. Después de este paso, se establece la conexión entre la red local y Azure a través de la conexión VPN sitio a sitio.


	`New-AzureVirtualNetworkGatewayConnection -connectedEntityId <local-network-gateway-id> -gatewayConnectionName Azure2Local -gatewayConnectionType IPsec -sharedKey abc123 -virtualNetworkGatewayId <azure-s2s-vpn-gateway-id>`

## <a name ="add"/> Configuración de conexiones coexistente para una red virtual ya existente

Si tiene una red virtual conectada a través de una conexión VPN sitio a sitio o ExpressRoute, para habilitar ambas conexiones para conectarse a la red virtual existente, primero debe eliminar la puerta de enlace existente. Esto significa que las instalaciones locales perderán la conexión a la red virtual a través de la puerta de enlace mientras trabaja en esta configuración.

**Antes de empezar la configuración:** compruebe que quedan suficientes direcciones IP en la red virtual, con el fin de que pueda aumentar el tamaño de la subred de puerta de enlace. Tenga en cuenta que tendrá que eliminar la puerta de enlace y volver a crearla aunque tenga suficientes direcciones IP. El motivo es que hay que dar cabida a las conexiones coexistentes.

1. Necesitará instalar la versión más reciente de los cmdlets de PowerShell del Administrador de recursos de Azure. Consulte [Cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) para más información sobre cómo instalar los cmdlets de PowerShell. Tenga en cuenta que los cmdlets que se van a utilizar en esta configuración pueden ser ligeramente diferentes de aquellos con los que podría estar familiarizado. Asegúrese de usar los cmdlets especificados en estas instrucciones. 

2. Elimine la puerta de enlace de la VPN sitio a sitio. Use el siguiente cmdlet, reemplazando los valores por los suyos propios.

	`Remove-AzureVNetGateway –VnetName MyAzureVNET`

3. Exporte el esquema de red virtual. Use el siguiente cmdlet de PowerShell, reemplazando los valores por los suyos propios.

	`Get-AzureVNetConfig –ExportToFile “C:\NetworkConfig.xml”`

4. Edite el esquema del archivo de configuración de red para que la subred de puerta de enlace sea /27 o un prefijo más corto (como /26 o /25). Consulte el ejemplo siguiente. Para más información sobre el esquema de configuración, consulte [Esquema de configuración de la red virtual de Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx).

          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.17.159.224/27</AddressPrefix>
          </Subnet>

5. Si la puerta de enlace anterior era una VPN de sitio a sitio, debe cambiar también el tipo de conexión a **Dedicado**.

		         <Gateway>
		          <ConnectionsToLocalNetwork>
		            <LocalNetworkSiteRef name="MyLocalNetwork">
		              <Connection type="Dedicated" />
		            </LocalNetworkSiteRef>
		          </ConnectionsToLocalNetwork>
		        </Gateway>

6. Ya tiene una red virtual sin puertas de enlace. Para crear nuevas puertas de enlace y completar las conexiones, puede continuar con el [Paso 4: Creación de una puerta de enlace de ExpressRoute](#gw), del conjunto de pasos anterior.

## Pasos siguientes

Para más información sobre ExpressRoute, consulte [P+F de ExpressRoute](expressroute-faqs.md).

<!---HONumber=AcomDC_0309_2016-->