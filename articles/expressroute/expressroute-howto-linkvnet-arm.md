<properties 
   pageTitle="Vinculación de redes virtuales a circuitos ExpressRoute | Microsoft Azure"
   description="En este documento se proporciona información general de cómo vincular redes virtuales a circuitos ExpressRoute."
   services="expressroute"
   documentationCenter="na"
   authors="ganesr"
   manager="carolz"
   editor=""
   tags="azure-service-management"/>
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/16/2016"
   ms.author="ganesr" />

# Vinculación de redes virtuales a circuitos ExpressRoute

> [AZURE.SELECTOR]
- [PowerShell - Classic](expressroute-howto-linkvnet-classic.md)
- [PowerShell - Resource Manager] (expressroute-howto-linkvnet-arm.md)
- [Template - Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/ecad62c231848ace2fbdc36cbe3dc04a96edd58c/301-expressroute-circuit-vnet-connection)

En este artículo se ofrece información general de cómo vincular redes virtuales a circuitos ExpressRoute. Las redes virtuales pueden estar en la misma suscripción o formar parte de otra suscripción. Este artículo se aplica a redes virtuales implementadas que usan el modelo de implementación del Administrador de recursos. Si quiere vincular una red virtual que se implementó usando el modelo de implementación clásica, vea [Link a virtual network to an ExpressRoute circuit](expressroute-howto-linkvnet-classic.md) (Vincular una red virtual a un circuito de ExpressRoute).


**Información sobre los modelos de implementación de Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## Requisitos previos de configuración

- Necesitará la versión más reciente de los módulos de Azure PowerShell, versión 1.0 o posterior. Consulte [Cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) para obtener más información sobre cómo instalar los cmdlets de PowerShell. 
- Asegúrese de que ha revisado la página de [requisitos previos](expressroute-prerequisites.md), la de [requisitos de enrutamiento](expressroute-routing.md) y la página de [flujos de trabajo](expressroute-workflows.md) antes de comenzar la configuración.
- Tiene que tener un circuito ExpressRoute activo. 
	- Siga las instrucciones para [crear un circuito ExpressRoute](expressroute-howto-circuit-arm.md) y habilite el circuito mediante el proveedor de conectividad. 
	- Asegúrese de que dispone de un emparejamiento privado de Azure configurado para el circuito. Consulte el artículo de [configuración del enrutamiento](expressroute-howto-routing-arm.md) para obtener instrucciones sobre el enrutamiento. 
	- El emparejamiento privado de Azure debe configurarse y el emparejamiento BGP entre la red y Microsoft debe estar activo para habilitar la conectividad de extremo a extremo.
	- Debe crear y aprovisionar totalmente una red virtual y una puerta de enlace de red virtual. Siga las instrucciones para crear una [puerta de enlace de VPN](../articles/vpn-gateway-create-site-to-site-rm-powershell.md), pero asegúrese de usar `-GatewayType ExpressRoute`.

Es posible vincular hasta 10 redes virtuales a un circuito ExpressRoute. Todos los circuitos ExpressRoute deben estar en la misma región geopolítica. Puede vincular un mayor número de redes virtuales en el circuito ExpressRoute si habilitó el complemento premium de ExpressRoute. Consulte las [preguntas más frecuentes](expressroute-faqs.md) para obtener más detalles sobre el complemento premium.

## Conexión de una red virtual en la misma suscripción de Azure a un circuito ExpressRoute

Puede conectar una puerta de enlace de red virtual a un circuito ExpressRoute mediante el siguiente cmdlet. Asegúrese de que la puerta de enlace de red virtual se crea y está lista para vincular antes de ejecutar el cmdlet.

	$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
	$gw = Get-AzureRmVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
	$connection = New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "MyRG" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute

## Conexión de una red virtual en una suscripción de Azure diferente a un circuito ExpressRoute

Se puede compartir un circuito ExpressRoute entre varias suscripciones. En la ilustración siguiente se muestra un sencillo esquema de cómo funciona el uso compartido de circuitos ExpressRoute entre varias suscripciones. Cada una de las nubes más pequeñas dentro de la nube de gran tamaño se usa para representar las suscripciones que pertenecen a diferentes departamentos dentro de una organización. Cada departamento de la organización puede usar su propia suscripción para implementar sus servicios, pero puede compartir un único circuito ExpressRoute para volver a conectarse a la red local. Un solo departamento (en este ejemplo: TI) puede ser el propietario del circuito ExpressRoute. Otras suscripciones dentro de la organización pueden usar el circuito ExpressRoute.

>[AZURE.NOTE] Los cargos de conectividad y ancho de banda de un circuito dedicado recaerán en el propietario del circuito ExpressRoute. Todas las redes virtuales comparten el mismo ancho de banda.

![Conectividad entre suscripciones](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### Administración

El *propietario del circuito* es un usuario avanzado autorizado del recurso de circuito ExpressRoute. Puede crear autorizaciones que los *usuarios del circuito*, a su vez, pueden canjear. Los *usuarios del circuito* son propietarios de puertas de enlace de red virtual (que no se incluyen en la misma suscripción que el circuito ExpressRoute). Estos *usuarios del circuito* pueden canjear autorizaciones (una autorización por red virtual).

El *propietario del circuito* tiene la capacidad de modificar y revocar las autorizaciones en cualquier momento. La revocación de una autorización dará como resultado la eliminación de todas las conexiones de vínculos de la suscripción cuyo acceso se haya revocado.

### Operaciones del propietario del circuito 

#### Creación de una autorización
	
El propietario del circuito crea una autorización. Esto da lugar a la creación de una clave de autorización que puede usar un usuario del circuito para conectar sus puertas de enlace de red virtual al circuito ExpressRoute. Una autorización solo es válida para una conexión.

El fragmento de cmdlet siguiente muestra cómo crear una autorización.

		Add-AzureRmExpressRouteCircuitAuthorization -Circuit $circuit -Name "MyAuthorization1"
		Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit
		$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"

		$auth1 = Get-AzureRmExpressRouteCircuitAuthorization -Circuit $circuit -Name "MyAuthorization1"
		

La respuesta a esta contendrá la clave y el estado de la autorización.

		Name                   : MyAuthorization1
		Id                     : /subscriptions/&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/CrossSubTest/authorizations/MyAuthorization1
		Etag                   : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&& 
		AuthorizationKey       : ####################################
		AuthorizationUseStatus : Available
		ProvisioningState      : Succeeded

		

#### Revisión de las autorizaciones

El propietario del circuito puede revisar todas las autorizaciones emitidas en un circuito concreto ejecutando el siguiente cmdlet.

	$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
	$authorizations = Get-AzureRmExpressRouteCircuitAuthorization -Circuit $circuit
	

#### Adición de autorizaciones

El propietario del circuito puede agregar las autorizaciones mediante el siguiente cmdlet.

	$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
	Add-AzureRmExpressRouteCircuitAuthorization -Circuit $circuit -Name "MyAuthorization2"
	Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit
	
	$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
	$authorizations = Get-AzureRmExpressRouteCircuitAuthorization -Circuit $circuit

	
#### Eliminación de autorizaciones

El propietario del circuito puede revocar o eliminar las autorizaciones al usuario ejecutando el siguiente cmdlet.

	Remove-AzureRmExpressRouteCircuitAuthorization -Name "MyAuthorization2" -Circuit $circuit
	Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit	

### Operaciones del usuario del circuito

El usuario del circuito necesita el Id. de mismo nivel y una clave de autorización del propietario del circuito. La clave del circuito es similar a la que se enumera a continuación:


la clave de autorización es un GUID.

#### Canjear autorizaciones de conexión

El usuario del circuito puede ejecutar el siguiente cmdlet para canjear una autorización de vínculo.

	$id = "/subscriptions/********************************/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/MyCircuit"	
	$connection = New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "RemoteResourceGroup -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $id -ConnectionType ExpressRoute -AuthorizationKey "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"

#### Liberación de autorizaciones de conexión

Puede liberar una autorización eliminando la conexión que vincula el circuito ExpressRoute a la red virtual.

## Pasos siguientes

Para obtener más información acerca de ExpressRoute, consulte [P+F de ExpressRoute](expressroute-faqs.md).

<!---HONumber=AcomDC_0218_2016-->