<properties
   pageTitle="Creación de una conexión de puerta de enlace de VPN entre dos redes virtuales mediante Azure Resource Manager y PowerShell para redes virtuales | Microsoft Azure"
   description="Este artículo le guiará para conectar redes virtuales entre sí por medio de PowerShell y el Administrador de recursos de Azure."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/04/2016"
   ms.author="cherylmc"/>

# Configuración de una conexión entre dos redes virtuales mediante Azure Resource Manager y PowerShell

> [AZURE.SELECTOR]
- [Portal de Azure clásico](virtual-networks-configure-vnet-to-vnet-connection.md)
- [PowerShell - Azure Resource Manager](vpn-gateway-vnet-vnet-rm-ps.md)

Este artículo lo guiará por los pasos para crear una conexión entre redes virtuales mediante el modelo de implementación de **Resource Manager** y PowerShell. Las redes virtuales pueden estar en la misma región o en distintas, así como pertenecer a una única suscripción o a varias.

[AZURE.INCLUDE [vpn-gateway-table-vnet-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

**Información sobre los modelos de implementación de Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]


## Acerca de conexiones de red virtual a red virtual

La conexión de una red virtual a otra (VNet a VNet) es muy parecida a la conexión de una red virtual a la ubicación de un sitio local. Ambos tipos de conectividad usan una puerta de enlace de VPN de Azure para proporcionar un túnel seguro con IPsec/IKE. Las redes virtuales que se conecten pueden estar en regiones distintas. O en distintas suscripciones. Incluso puede combinar la comunicación de red virtual a red virtual con configuraciones de varios sitios. Esto permite establecer topologías de red que combinen la conectividad entre entornos con la conectividad entre redes virtuales, como se muestra en el diagrama siguiente.


![Acerca de las conexiones](./media/vpn-gateway-vnet-vnet-rm-ps/aboutconnections.png)
 
### ¿Por qué debería conectarse a redes virtuales?

Puede que desee conectar redes virtuales por las siguientes razones:

- **Presencia geográfica y redundancia geográfica entre regiones**
	- Puede configurar su propia replicación geográfica o sincronización con conectividad segura sin recurrir a los puntos de conexión a Internet.
	- Con el Equilibrador de carga y el Administrador de tráfico de Azure, puede configurar cargas de trabajo de alta disponibilidad con redundancia geográfica en varias regiones de Azure. Por ejemplo, puede configurar AlwaysOn de SQL con grupos de disponibilidad distribuidos en varias regiones de Azure.

- **Aplicaciones regionales de niveles múltiples con aislamiento o un límite administrativo**
	- Dentro de la misma región, se pueden configurar aplicaciones de niveles múltiples con varias redes virtuales conectadas entre sí para cumplir requisitos administrativos o de aislamiento.


### P+F sobre conexiones de red virtual a red virtual

- Las redes virtuales pueden estar en la misma región de Azure o en regiones distintas (ubicaciones).

- Un servicio en la nube o un extremo de equilibrio de carga NO PUEDE abarcar varias redes virtuales aunque estas estén conectadas entre sí.

- La conexión simultánea de varias redes virtuales de Azure no requiere puertas de enlace de VPN locales, a menos que sea necesaria la conectividad entre locales.

- VNet a VNet admite la conexión de redes virtuales. No admite la conexión de máquinas virtuales o servicios en la nube que NO estén en una red virtual.

- La conexión entre dos redes virtuales requiere puertas de enlace de VPN de Azure con tipos de VPN RouteBased (antes denominado enrutamiento dinámico).

- La conectividad de red virtual se puede usar simultáneamente con VPN de varios sitios. El número máximo de túneles VPN para una puerta de enlace de VPN de red virtual que se conecte a otras redes virtuales o a sitios locales será 10 con puertas de enlace estándares o predeterminadas o 30 con puertas de enlace de alto rendimiento.

- Los espacios de direcciones de las redes virtuales y de los sitios de red local no se pueden solapar. Los espacios de direcciones solapados provocarán un error al crear conexiones entre dos redes virtuales.

- No se admiten los túneles redundantes entre un par de redes virtuales.

- Todos los túneles de VPN de la red virtual comparten el ancho de banda disponible en la puerta de enlace de VPN de Azure y el mismo SLA de tiempo de actividad de puerta de enlace de VPN en Azure.

- El tráfico entre dos redes virtuales viaja por la red de Microsoft, no por Internet.

- El tráfico entre dos redes virtuales dentro de la misma región es gratuito en ambos sentidos; si las redes virtuales se encuentran en regiones diferentes, el tráfico se cobra según las tarifas de transferencia de datos de salida entre redes virtuales en función de las regiones de origen. Consulte los detalles en la [página de información de precios](https://azure.microsoft.com/pricing/details/vpn-gateway/).


## ¿Qué serie de pasos debo seguir?

En este artículo, verá diferentes series de pasos que se aplican a las redes virtuales creadas mediante el modelo de implementación de Resource Manager. Los pasos para configurar la conexión entre dos redes virtuales varían según si estas se encuentran en la misma suscripción o en suscripciones diferentes.

![Ambas conexiones](./media/vpn-gateway-vnet-vnet-rm-ps/differentsubscription.png)


En lo que respecta a los pasos de configuración, la diferencia clave entre ambos casos es si se pueden crear y configurar todos los recursos de red virtual y puerta de enlace en la misma sesión de PowerShell. En este documento se incluyen los pasos para ambos casos. En el gráfico anterior se muestra una conexión entre dos redes virtuales en la misma suscripción y otra entre redes virtuales de suscripciones diferentes.


- [Redes virtuales que residen en la misma suscripción](#samesub)
- [Redes virtuales que residen en suscripciones diferentes](#difsub)


## <a name ="samesub"/>Conexión de redes virtuales que están en la misma suscripción

Esta configuración se aplica a las redes virtuales que están en la misma suscripción, como se muestra en el diagrama siguiente:

![VNet2VNet en la misma suscripción](./media/vpn-gateway-vnet-vnet-rm-ps/samesubscription.png)

### Antes de empezar

- Compruebe que tiene una suscripción a Azure. Si todavía no la tiene, puede activar sus [créditos por suscripción a MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) o registrarse para obtener una [cuenta gratis](https://azure.microsoft.com/pricing/free-trial/).
	
- Necesitará instalar los cmdlets de PowerShell del Administrador de recursos de Azure. Consulte [Cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) para más información sobre cómo instalar los cmdlets de PowerShell.

### <a name ="Step1"/>Paso 1: Planeamiento de los intervalos de direcciones IP


Es importante decidir los intervalos que usará para establecer la configuración de red. Tenga en cuenta que hay que asegurarse de que ninguno de los intervalos de VNet o intervalos de red local se solapen.

En los pasos siguientes, se crearán dos redes virtuales junto con sus subredes de puerta de enlace y configuraciones correspondientes. Después se creará una conexión de puerta de enlace de VPN entre las redes dos virtuales.

En este ejercicio, utilice los siguientes valores para las redes virtuales:

**Valores para TestVNet1:**

- Nombre de red virtual: TestVNet1
- Grupo de recursos: TestRG1
- Ubicación: Este de EE. UU.
- TestVNet1: 10.11.0.0/16 y 10.12.0.0/16
- FrontEnd: 10.11.0.0/24
- Backend: 10.12.0.0/24
- GatewaySubnet: 10.12.255.0/27
- Servidor DNS: 8.8.8.8
- GatewayName: VNet1GW
- Dirección IP pública: VNet1GWIP
- VPNType: RouteBased
- Connection(1to4): VNet1toVNet4
- Connection(1to5): VNet1toVNet5
- ConnectionType: VNet2VNet


**Valores para TestVNet4:**

- Nombre de red virtual: TestVNet4
- TestVNet2: 10.41.0.0/16 y 10.42.0.0/16
- FrontEnd: 10.41.0.0/24
- Backend: 10.42.0.0/24
- GatewaySubnet: 10.42.255.0.0/27
- Grupo de recursos: TestRG4
- Ubicación: Oeste de EE. UU.
- Servidor DNS: 8.8.8.8
- GatewayName: VNet4GW
- Dirección IP pública: VNet4GWIP
- VPNType: RouteBased
- Conexión: VNet4toVNet1
- ConnectionType: VNet2VNet



### <a name ="Step2"/>Paso 2: Creación y configuración de TestVNet1

1. Declaración de las variables

	Para este ejercicio, comenzaremos declarando las variables. En el ejemplo siguiente, se declaran las variables con los valores para este ejercicio. Asegúrese de reemplazar los valores por los suyos propios cuando realice la configuración para el entorno de producción. Puede usar estas variables si está practicando los pasos para familiarizarse con este tipo de configuración. Modifique las variables y después copie y pegue todo en la consola de PowerShell.

		$Sub1          = "Replace_With_Your_Subcription_Name"
		$RG1           = "TestRG1"
		$Location1     = "East US"
		$VNetName1     = "TestVNet1"
		$FESubName1    = "FrontEnd"
		$BESubName1    = "Backend"
		$GWSubName1    = "GatewaySubnet"
		$VNetPrefix11  = "10.11.0.0/16"
		$VNetPrefix12  = "10.12.0.0/16"
		$FESubPrefix1  = "10.11.0.0/24"
		$BESubPrefix1  = "10.12.0.0/24"
		$GWSubPrefix1  = "10.12.255.0/27"
		$DNS1          = "8.8.8.8"
		$GWName1       = "VNet1GW"
		$GWIPName1     = "VNet1GWIP"
		$GWIPconfName1 = "gwipconf1"
		$Connection14  = "VNet1toVNet4"
		$Connection15  = "VNet1toVNet5"

2. Conexión con la suscripción 1

	Asegúrese de cambiar el modo de PowerShell para que use los cmdlets del Administrador de recursos. Para obtener más información, consulte [Uso de Windows PowerShell con el Administrador de recursos](../powershell-azure-resource-manager.md).

	Abre la consola de PowerShell y conéctate a tu cuenta. Use el siguiente ejemplo para ayudarle a conectarse:

		Login-AzureRmAccount

	Compruebe las suscripciones para la cuenta.

		Get-AzureRmSubscription 

	Especifique la suscripción que quiere usar.

		Select-AzureRmSubscription -SubscriptionName $Sub1

3. Creación de un nuevo grupo de recursos

		New-AzureRmResourceGroup -Name $RG1 -Location $Location1

4. Creación de las configuraciones de subred para TestVNet1

	En el ejemplo siguiente se crea una red virtual denominada TestVNet1 y tres subredes llamadas: GatewaySubnet, FrontEnd y Backend. Al reemplazar valores, es importante que siempre asigne el nombre GatewaySubnet a la subred de la puerta de enlace. Si usa otro, se producirá un error al crear la puerta de enlace.

	En el ejemplo siguiente, la subred de puerta de enlace usa el prefijo /27. Aunque técnicamente se puede crear una subred de puerta de enlace con un prefijo tan reducido como /29, no se recomienda hacerlo. Se recomienda usar uno algo más amplio, como /27 o /26, para poder aprovechar las ventajas de configuraciones existentes o futuras que requieran una subred de puerta de enlace más amplia.


		$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
		$besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
		$gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1


5. Creación de TestVNet1

		New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

6. Solicitar una dirección IP pública

	A continuación, solicitará que se asigne una dirección IP pública a la puerta de enlace que creará para la red virtual. No puede especificar la dirección IP que desea usar; se asigna una dinámicamente a la puerta de enlace. Utilizará esta dirección IP en la siguiente sección de configuración. Use el ejemplo siguiente. El método de asignación para esta dirección debe ser dinámico.

		$gwpip1    = New-AzureRmPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

7. Establecer la configuración de la puerta de enlace

	La configuración de puerta de enlace define la subred y la dirección IP pública. Use el ejemplo siguiente para crear la configuración de la puerta de enlace.

		$vnet1     = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
		$subnet1   = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
		$gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 -Subnet $subnet1 -PublicIpAddress $gwpip1

8. Creación de la puerta de enlace para TestVNet1

	En este paso, creará la puerta de enlace de red virtual para TestVNet1. Las configuraciones VNet a VNet requieren un VpnType RouteBased. Se tardan unos 30 minutos o algo más en crear una puerta de enlace.

		New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku Standard

### Paso 3: Creación y configuración de TestVNet4

Después de configurar TestVNet1, debe repetir los pasos para crear TestVNet4. Siga los pasos a continuación y reemplace los valores por los suyos propios cuando sea necesario. Este paso puede realizarse en la misma sesión de PowerShell porque está en la misma suscripción.

1. Declaración de las variables

	Asegúrese de reemplazar los valores por los que desea usar para su configuración.

		$RG4           = "TestRG4"
		$Location4     = "West US"
		$VnetName4     = "TestVNet4"
		$FESubName4    = "FrontEnd"
		$BESubName4    = "Backend"
		$GWSubName4    = "GatewaySubnet"
		$VnetPrefix41  = "10.41.0.0/16"
		$VnetPrefix42  = "10.42.0.0/16"
		$FESubPrefix4  = "10.41.0.0/24"
		$BESubPrefix4  = "10.42.0.0/24"
		$GWSubPrefix4  = "10.42.255.0/27"
		$DNS4          = "8.8.8.8"
		$GWName4       = "VNet4GW"
		$GWIPName4     = "VNet4GWIP"
		$GWIPconfName4 = "gwipconf4"
		$Connection41  = "VNet4toVNet1"

	Antes de continuar, asegúrese de que sigue conectado a la suscripción 1.

2. Creación de un nuevo grupo de recursos

		New-AzureRmResourceGroup -Name $RG4 -Location $Location4

3. Creación de las configuraciones de subred para TestVNet4

		$fesub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName4 -AddressPrefix $FESubPrefix4
		$besub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName4 -AddressPrefix $BESubPrefix4
		$gwsub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName4 -AddressPrefix $GWSubPrefix4

4. Creación de TestVNet4

		New-AzureRmVirtualNetwork -Name $VnetName4 -ResourceGroupName $RG4 -Location $Location4 -AddressPrefix $VnetPrefix41,$VnetPrefix42 -Subnet $fesub4,$besub4,$gwsub4

5. Solicitar una dirección IP pública

		$gwpip4    = New-AzureRmPublicIpAddress -Name $GWIPName4 -ResourceGroupName $RG4 -Location $Location4 -AllocationMethod Dynamic

6. Establecer la configuración de la puerta de enlace

		$vnet4     = Get-AzureRmVirtualNetwork -Name $VnetName4 -ResourceGroupName $RG4
		$subnet4   = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet4
		$gwipconf4 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName4 -Subnet $subnet4 -PublicIpAddress $gwpip4

7. Creación de la puerta de enlace de TestVNet4

		New-AzureRmVirtualNetworkGateway -Name $GWName4 -ResourceGroupName $RG4 -Location $Location4 -IpConfigurations $gwipconf4 -GatewayType Vpn -VpnType RouteBased -GatewaySku Standard

### Paso 4: Conexión de las puertas de enlace

1. Obtención de ambas puertas de enlace de red virtual

	En este ejemplo, como ambas puertas de enlace están en la misma suscripción, el paso puede realizarse en la misma sesión de PowerShell.

		$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
		$vnet4gw = Get-AzureRmVirtualNetworkGateway -Name $GWName4 -ResourceGroupName $RG4


2. Creación de la conexión de TestVNet1 a TestVNet4

	En este paso, creará la conexión de TestVNet1 a TestVNet4. Verá una clave compartida a la que se hace referencia en los ejemplos. Puede utilizar sus propios valores para la clave compartida. Lo importante es que la clave compartida coincida en ambas conexiones. Se tardará unos momentos en terminar de crear la conexión.

		New-AzureRmVirtualNetworkGatewayConnection -Name $Connection14 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet4gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'

3. Creación de la conexión de TestVNet4 a TestVNet1

	Este paso es similar al anterior, salvo en que se crea la conexión de TestVNet4 a TestVNet1. Asegúrese de que coincidan las claves compartidas.

		New-AzureRmVirtualNetworkGatewayConnection -Name $Connection41 -ResourceGroupName $RG4 -VirtualNetworkGateway1 $vnet4gw -VirtualNetworkGateway2 $vnet1gw -Location $Location4 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'

	Después de unos minutos, la conexión debería haberse establecido.

## <a name ="Verify"/>Comprobación de la conexión entre dos redes virtuales

En los ejemplos siguientes se muestra cómo comprobar la conexión. Asegúrese de cambiar los valores para que coincidan con los de su entorno.

### Para comprobar la conexión mediante el uso del Portal de Azure

Para comprobar una conexión VPN en el Portal de Azure, vaya a **Puertas de enlace de red virtual** -> **haga clic en el nombre de su puerta de enlace** -> **Configuración** -> **Conexiones**. Para ver más información en la hoja **Conexión**, seleccione el nombre de la conexión.


### Para comprobar la conexión mediante el uso de PowerShell

También es posible comprobar que la conexión se realizó correctamente con *Get-AzureRmVirtualNetworkGatewayConnection –Debug*. Puede usar el siguiente ejemplo y cambiar los valores para que coincidan con los suyos. Cuando se le pida, seleccione A para ejecutar todo.

	Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection1 -ResourceGroupName $RG1 -Debug

Cuando el cmdlet haya finalizado, desplázate para ver los valores. En el ejemplo de salida de PowerShell siguiente, el estado de conexión aparece como *Connected* y se ven los bytes de entrada y salida.



	AuthorizationKey           :
	VirtualNetworkGateway1     : Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
	VirtualNetworkGateway2     : Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
	LocalNetworkGateway2       :
	Peer                       :
	ConnectionType             : Vnet2Vnet
	RoutingWeight              : 0
	SharedKey                  : AzureA1b2C3
	ConnectionStatus           : Connected
	EgressBytesTransferred     : 1376
	IngressBytesTransferred    : 1232
	ProvisioningState          : Succeeded
	VirtualNetworkGateway1Text : "/subscriptions/<SubscriptionID>/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW"
	VirtualNetworkGateway2Text : "/subscriptions/<SubscriptionID>/resourceGroups/TestRG4/providers/Microsoft.Network/virtualNetworkGateways/VNet4GW"
	LocalNetworkGateway2Text   :
	PeerText                   :
	ResourceGroupName          : TestRG1
	Location                   : eastus
	ResourceGuid               : 173489d1-37e2-482c-b8b8-6ca69fc3e069
	Tag                        : {}
	TagsTable                  :
	Name                       : VNet1toVNet4
	Id                         : /subscriptions/<SubscriptionID>/resourceGroups/TestRG1/providers/Micr osoft.Network/connections/VNet1toVNet4

## <a name ="difsub"/>Conexión de redes virtuales que están en suscripciones diferentes

En los pasos de configuración siguientes se agrega una conexión adicional entre dos redes virtuales para conectar TestVNet1 a TestVNet5, que se encuentra en una suscripción diferente. La diferencia es que parte de los pasos de configuración se deben realizar en una sesión de PowerShell distinta en el contexto de la segunda suscripción, sobre todo cuando las dos suscripciones pertenecen a distintas organizaciones. Tras completar los pasos siguientes, la configuración resultante se muestra en este diagrama:

![VNet2VNet en distintas suscripciones](./media/vpn-gateway-vnet-vnet-rm-ps/differentsubscription.png)

Las instrucciones que siguen son continuación de los pasos anteriores ya explicados. Debe completar el [paso 1](#Step1) y el [paso 2](#Step2) para crear y configurar TestVNet1 y la puerta de enlace de VPN para TestVNet1. Si solo va a conectar redes virtuales en distintas suscripciones, no es necesario que se preocupe por los pasos 3 y 4 del ejercicio anterior; puede ir directamente al paso 5.

### Paso 5: Comprobación de los intervalos de direcciones IP adicionales

Es importante asegurarse de que el espacio de direcciones IP de la red virtual nueva, TestVNet5, no se solape con ninguno de los intervalos de red virtual o de puerta de enlace de red local.

En este ejemplo, las redes virtuales pueden pertenecer a distintas organizaciones. En este ejercicio, use los siguientes valores para TestVNet5:

**Valores para TestVNet5:**

- Nombre de red virtual: TestVNet5
- Grupo de recursos: TestRG5
- Ubicación: Japón Oriental
- TestVNet5: 10.51.0.0/16 y 10.52.0.0/16
- FrontEnd: 10.51.0.0/24
- Backend: 10.52.0.0/24
- GatewaySubnet: 10.52.255.0.0/27
- Servidor DNS: 8.8.8.8
- GatewayName: VNet5GW
- Dirección IP pública: VNet5GWIP
- VPNType: RouteBased
- Conexión: VNet5toVNet1
- ConnectionType: VNet2VNet

**Valores adicionales para TestVNet1:**

- Conexión: VNet1toVNet5


### Paso 6: Creación y configuración de TestVNet5

Este paso debe realizarse en el contexto de la nueva suscripción. Es posible que esta parte la realice el administrador de otra organización que posea la suscripción.

1. Declaración de las variables

	Asegúrese de reemplazar los valores por los que desea usar para su configuración.

		$Sub5          = "Replace_With_the_New_Subcription_Name"
		$RG5           = "TestRG5"
		$Location5     = "Japan East"
		$VnetName5     = "TestVNet5"
		$FESubName5    = "FrontEnd"
		$BESubName5    = "Backend"
		$GWSubName5    = "GatewaySubnet"
		$VnetPrefix51  = "10.51.0.0/16"
		$VnetPrefix52  = "10.52.0.0/16"
		$FESubPrefix5  = "10.51.0.0/24"
		$BESubPrefix5  = "10.52.0.0/24"
		$GWSubPrefix5  = "10.52.255.0/27"
		$DNS5          = "8.8.8.8"
		$GWName5       = "VNet5GW"
		$GWIPName5     = "VNet5GWIP"
		$GWIPconfName5 = "gwipconf5"
		$Connection51  = "VNet5toVNet1"

2. Conexión con la suscripción 5

	Abre la consola de PowerShell y conéctate a tu cuenta. Use el siguiente ejemplo para ayudarle a conectarse:

		Login-AzureRmAccount

	Compruebe las suscripciones para la cuenta.

		Get-AzureRmSubscription 

	Especifique la suscripción que quiere usar.

		Select-AzureRmSubscription -SubscriptionName $Sub5

3. Creación de un nuevo grupo de recursos

		New-AzureRmResourceGroup -Name $RG5 -Location $Location5

4. Creación de las configuraciones de subred para TestVNet4
	
		$fesub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName5 -AddressPrefix $FESubPrefix5
		$besub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName5 -AddressPrefix $BESubPrefix5
		$gwsub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName5 -AddressPrefix $GWSubPrefix5

5. Creación de TestVNet5

		New-AzureRmVirtualNetwork -Name $VnetName5 -ResourceGroupName $RG5 -Location $Location5 -AddressPrefix $VnetPrefix51,$VnetPrefix52 -Subnet $fesub5,$besub5,$gwsub5

6. Solicitar una dirección IP pública

		$gwpip5    = New-AzureRmPublicIpAddress -Name $GWIPName5 -ResourceGroupName $RG5 -Location $Location5 -AllocationMethod Dynamic


7. Establecer la configuración de la puerta de enlace

		$vnet5     = Get-AzureRmVirtualNetwork -Name $VnetName5 -ResourceGroupName $RG5
		$subnet5   = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet5
		$gwipconf5 = New-AzureRmVirtualNetworkGatewayIpConfig 

8. Creación de la puerta de enlace de TestVNet5

		New-AzureRmVirtualNetworkGateway -Name $GWName5 -ResourceGroupName $RG5 -Location $Location5 -IpConfigurations $gwipconf5 -GatewayType Vpn -VpnType RouteBased -GatewaySku Standard

### Paso 7: Conexión de las puertas de enlace

En este ejemplo, como las puertas de enlace están en suscripciones diferentes, hemos dividimos el paso en dos sesiones de PowerShell marcadas como [Suscripción 1] y [Suscripción 5].

1. **[Suscripción 1]** Obtención de la puerta de enlace de red virtual para la suscripción 1

	Asegúrese de iniciar sesión y conectarse a la suscripción 1.

		$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1

	Copie la salida de los siguientes elementos y envíesela al administrador de la suscripción 5 por correo electrónico u otro método.

		$vnet1gw.Name
		$vnet1gw.Id

	Estos dos elementos tendrán valores similares a la salida de ejemplo siguiente:

		PS D:> $vnet1gw.Name
		VNet1GW
		PS D:> $vnet1gw.Id
		/subscriptions/b636ca99-6f88-4df4-a7c3-2f8dc4545509/resourceGroupsTestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW

2. **[Suscripción 5]** Obtención de la puerta de enlace de red virtual para la suscripción 5

	Asegúrese de iniciar sesión y conectarse a la suscripción 5.

		$vnet5gw = Get-AzureRmVirtualNetworkGateway -Name $GWName5 -ResourceGroupName $RG5

	Copie la salida de los siguientes elementos y envíesela al administrador de la suscripción 1 por correo electrónico u otro método.

		$vnet5gw.Name
		$vnet5gw.Id

	Estos dos elementos tendrán valores similares a la salida de ejemplo siguiente:

		PS C:\> $vnet5gw.Name
		VNet5GW
		PS C:\> $vnet5gw.Id
		/subscriptions/66c8e4f1-ecd6-47ed-9de7-7e530de23994/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW

3. **[Suscripción 1]** Creación de la conexión de TestVNet1 a TestVNet5

	En este paso, creará la conexión de TestVNet1 a TestVNet5. La diferencia en este caso es que no se puede obtener $vnet5gw directamente porque está en una suscripción diferente. Debe crear un nuevo objeto de PowerShell con los valores que se le hayan proporcionado para la suscripción 1 en los pasos anteriores. Reemplace el nombre (Name), el identificador (Id) y la clave compartida por sus propios valores. Lo importante es que la clave compartida coincida en ambas conexiones. Se tardará unos momentos en terminar de crear la conexión.

	Asegúrese de conectarse a la suscripción 1.
	
		$vnet5gw = New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
		$vnet5gw.Name = "VNet5GW"
		$vnet5gw.Id   = "/subscriptions/66c8e4f1-ecd6-47ed-9de7-7e530de23994/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW"
		$Connection15 = "VNet1toVNet5"
		New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet5gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'

4. **[Suscripción 5]** Creación de la conexión de TestVNet5 a TestVNet1

	Este paso es similar al anterior, salvo en que se crea la conexión de TestVNet5 a TestVNet1. Aquí también se aplica el mismo proceso de creación de un objeto de PowerShell basándose en los valores obtenidos de la suscripción 1. En este paso, asegúrese de que las claves compartidas coincidan.

	Asegúrese de conectarse a la suscripción 5.

		$vnet1gw = New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
		$vnet1gw.Name = "VNet1GW"
		$vnet1gw.Id   = "/subscriptions/b636ca99-6f88-4df4-a7c3-2f8dc4545509/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW "
		New-AzureRmVirtualNetworkGatewayConnection -Name $Connection51 -ResourceGroupName $RG5 -VirtualNetworkGateway1 $vnet5gw -VirtualNetworkGateway2 $vnet1gw -Location $Location5 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'

5. Comprobación de la conexión

	Después de completar estos pasos, puede comprobar la conexión mediante los métodos descritos en [Comprobación de la conexión entre dos redes virtuales](#Verify).

## Pasos siguientes

Una vez completada la conexión, puede agregar máquinas virtuales a las redes virtuales. Consulte [Creación de una máquina virtual que ejecuta Windows en el Portal de Azure](../virtual-machines/virtual-machines-windows-tutorial.md) para ver los pasos.

<!---HONumber=AcomDC_0309_2016-->