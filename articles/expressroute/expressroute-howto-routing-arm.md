<properties
   pageTitle="Cómo configurar el enrutamiento de un circuito ExpressRoute | Microsoft Azure"
   description="Este artículo le guiará por los pasos necesarios para crear y aprovisionar las configuraciones entre pares privados, públicos y de Microsoft de un circuito ExpressRoute. Este artículo también muestra cómo comprobar el estado, actualizar, o eliminar configuraciones entre pares en el circuito."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="hero-article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/04/2016"
   ms.author="cherylmc"/>

# Creación y modificación del enrutamiento para un circuito ExpressRoute mediante el Administrador de recursos de Azure y PowerShell

> [AZURE.SELECTOR]
[PowerShell - Classic](expressroute-howto-routing-classic.md)
[PowerShell - Resource Manager](expressroute-howto-routing-arm.md)

Este artículo le guiará por los pasos necesarios para crear y administrar la configuración de enrutamiento para un circuito ExpressRoute con los cmdlets de PowerShell y el modelo de implementación de Administrador de recursos de Azure. Los siguientes pasos también le mostrarán cómo comprobar el estado, actualizar, o eliminar y desaprovisionar las configuraciones entre pares para un circuito ExpressRoute. Si quiere crear o modificar el enrutamiento de un circuito de ExpressRoute con el modelo de implementación del **clásico**, consulte [Creación y modificación del enrutamiento de un circuito ExpressRoute mediante PowerShell](expressroute-howto-routing-classic.md).

[AZURE.INCLUDE [vpn-gateway-sm-rm](../../includes/vpn-gateway-sm-rm-include.md)]

## Requisitos previos de configuración

- Necesitará la versión más reciente de los módulos de Azure PowerShell, versión 1.0 o posterior. 
- Asegúrese de que ha revisado la página de [requisitos previos](expressroute-prerequisites.md), la de [requisitos de enrutamiento](expressroute-routing.md) y la página de [flujos de trabajo](expressroute-workflows.md) antes de comenzar la configuración.
- Debe tener un circuito ExpressRoute activo. Siga las instrucciones para [crear un circuito ExpressRoute](expressroute-howto-circuit-arm.md) y habilite el circuito a través del proveedor de conectividad antes de continuar. El circuito ExpressRoute debe estar en un estado habilitado y aprovisionado para poder ejecutar los cmdlets que se describen a continuación.

>[AZURE.IMPORTANT] Estas instrucciones se aplican solo a circuitos creados con proveedores de servicios que ofrece servicios de conectividad de capa 2. Si usa un proveedor de servicios que ofrece servicios administrados de nivel 3 (normalmente IPVPN, como MPLS), el mismo proveedor de conectividad configurará y administrará el enrutamiento. En estos casos no podrá crear ni administrar las configuraciones entre pares.

Puede configurar una, dos o las tres configuraciones entre pares (Azure privado, Azure público y Microsoft) para un circuito ExpressRoute. Puede establecer las configuraciones entre pares en cualquier orden. Pero tiene que asegurarse de que completa cada configuración entre pares de una en una.

## Configuración entre pares privados de Azure

Esta sección proporciona instrucciones sobre cómo crear, obtener, actualizar y eliminar la configuración entre pares privados de Azure para un circuito ExpressRoute.

### Creación de un emparejamiento privado de Azure

1. **Importe el módulo de PowerShell para ExpressRoute.**
	
 	Para comenzar a usar los cmdlets de ExpressRoute, debe instalar el programa de instalación de PowerShell más reciente desde la [Galería de PowerShell](http://www.powershellgallery.com/) e importar los módulos del Administrador de recursos de Azure en la sesión de PowerShell. Deberá ejecutar PowerShell como administrador.

	    Install-Module AzureRM

		Install-AzureRM

	Importe todos los módulos de AzureRM.* dentro del intervalo de versiones semánticas conocidas.

		Import-AzureRM

	También puede importar un módulo determinado en dicho intervalo.
		
		Import-Module AzureRM.Network 

	Inicie sesión en su cuenta.

		Login-AzureRmAccount

	Seleccione la suscripción en la que desea crear un circuito ExpressRoute.
		
		Select-AzureRmSubscription -SubscriptionId "<subscription ID>"

2. **Creación de un circuito ExpressRoute.**
	
	Siga las instrucciones para crear un [circuito ExpressRoute](expressroute-howto-circuit-arm.md) y habilite su aprovisionamiento a través del proveedor de conectividad.

	Si su proveedor de conectividad ofrece servicios administrados de nivel 3, puede solicitarle que habilite la configuración entre pares privados de Azure. En ese caso, no necesita seguir las instrucciones que aparecen en las secciones siguientes. Por otra parte, si su proveedor de conectividad no administra este enrutamiento, una vez que cree el circuito siga las instrucciones siguientes.

3. **Compruebe el circuito ExpressRoute para asegurarse de que está aprovisionado.**

	En primer lugar tiene que comprobar si el circuito ExpressRoute tiene los estados Aprovisionado y Habilitado. Observe el ejemplo siguiente.

		Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

	La respuesta tendrá un aspecto similar al siguiente ejemplo:

		Name                             : ExpressRouteARMCircuit
		ResourceGroupName                : ExpressRouteResourceGroup
		Location                         : westus
		Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
		Etag                             : W/"################################"
		ProvisioningState                : Succeeded
		Sku                              : {
		                                     "Name": "Standard_MeteredData",
		                                     "Tier": "Standard",
		                                     "Family": "MeteredData"
		                                   }
		CircuitProvisioningState         : Enabled
		ServiceProviderProvisioningState : Provisioned
		ServiceProviderNotes             : 
		ServiceProviderProperties        : {
		                                     "ServiceProviderName": "Equinix",
		                                     "PeeringLocation": "Silicon Valley",
		                                     "BandwidthInMbps": 200
		                                   }
		ServiceKey                       : **************************************
		Peerings                         : []


4. **Establecimiento de la configuración entre pares privados de Azure para el circuito.**

	Asegúrese de que tiene los elementos siguientes antes de continuar con los siguientes pasos:

	- Una subred /30 para el vínculo principal. No debe ser parte de ningún espacio de direcciones reservado para redes virtuales.
	- Una subred /30 para el vínculo secundario. No debe ser parte de ningún espacio de direcciones reservado para redes virtuales.
	- Un identificador VLAN válido para establecer esta configuración entre pares. Asegúrese de que ninguna otra configuración entre pares en el circuito usa el mismo identificador de VLAN.
	- Número de sistema autónomo (AS) para la configuración entre pares. Puede usar 2 bytes o 4 bytes como números AS. Puede usar un número AS privado para esta configuración entre pares. Asegúrese de que no usa 65515.
	- Un hash MD5 si opta por utilizar uno. **Esto es opcional**.
	
	Puede ejecutar el siguiente cmdlet para establecer la configuración entre pares privados de Azure para el circuito.

		Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -Circuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

		Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

	Puede usar el cmdlet siguiente si decide usar un hash MD5.

		Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -Circuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200  -SharedKey "A1B2C3D4"

		Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

	>[AZURE.IMPORTANT] Asegúrese de especificar su número AS como ASN de configuración entre pares, no como cliente ASN.

### Obtención de detalles del emparejamiento privado de Azure

Puede obtener detalles de configuración mediante el siguiente cmdlet

		$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

		Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -Circuit $ckt	


### Actualización del establecimiento de configuración del emparejamiento privado de Azure

Puede actualizar cualquier parte de la configuración mediante el siguiente cmdlet. En el ejemplo siguiente, se está actualizando el identificador de VLAN del circuito de 100 a 500.

	Set-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -Circuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

	Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


### Eliminación del emparejamiento privado de Azure

Puede quitar el establecimiento de configuración entre pares ejecutando el siguiente cmdlet.

>[AZURE.WARNING] Tiene que asegurarse de que todas las redes virtuales se desvinculan del circuito ExpressRoute antes de ejecutar este cmdlet.

	Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -Circuit $ckt
	Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt



## Configuración entre pares públicos de Azure

Esta sección proporciona instrucciones sobre cómo crear, obtener, actualizar y eliminar la configuración entre pares públicos de Azure para un circuito ExpressRoute.

### Creación de un emparejamiento público de Azure

1. **Importe el módulo de PowerShell para ExpressRoute.**
	
 	Para comenzar a usar los cmdlets de ExpressRoute, debe instalar el programa de instalación de PowerShell más reciente desde la [Galería de PowerShell](http://www.powershellgallery.com/) e importar los módulos del Administrador de recursos de Azure en la sesión de PowerShell. Deberá ejecutar PowerShell como administrador.

	    Install-Module AzureRM

		Install-AzureRM

	Importe todos los módulos de AzureRM.* dentro del intervalo de versiones semánticas conocidas.

		Import-AzureRM

	También puede importar un módulo determinado en dicho intervalo.
		
		Import-Module AzureRM.Network 

	Inicie sesión en su cuenta.

		Login-AzureRmAccount

	Seleccione la suscripción en la que desea crear un circuito ExpressRoute.
		
		Select-AzureRmSubscription -SubscriptionId "<subscription ID>"

2. **Creación de un circuito ExpressRoute**
	
	Siga las instrucciones para crear un [circuito ExpressRoute](expressroute-howto-circuit-arm.md) y habilite su aprovisionamiento a través del proveedor de conectividad.

	Si su proveedor de conectividad ofrece servicios administrados de nivel 3, puede solicitarle que habilite la configuración entre pares privados de Azure. En ese caso, no necesita seguir las instrucciones que aparecen en las secciones siguientes. Por otra parte, si su proveedor de conectividad no administra este enrutamiento, una vez que cree el circuito siga las instrucciones siguientes.

3. **Compruebe el circuito ExpressRoute para asegurarse de que está aprovisionado**

	En primer lugar tiene que comprobar si el circuito ExpressRoute tiene los estados Aprovisionado y Habilitado. Observe el ejemplo siguiente.

		Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

	La respuesta tendrá un aspecto similar al siguiente ejemplo:

		Name                             : ExpressRouteARMCircuit
		ResourceGroupName                : ExpressRouteResourceGroup
		Location                         : westus
		Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
		Etag                             : W/"################################"
		ProvisioningState                : Succeeded
		Sku                              : {
		                                     "Name": "Standard_MeteredData",
		                                     "Tier": "Standard",
		                                     "Family": "MeteredData"
		                                   }
		CircuitProvisioningState         : Enabled
		ServiceProviderProvisioningState : Provisioned
		ServiceProviderNotes             : 
		ServiceProviderProperties        : {
		                                     "ServiceProviderName": "Equinix",
		                                     "PeeringLocation": "Silicon Valley",
		                                     "BandwidthInMbps": 200
		                                   }
		ServiceKey                       : **************************************
		Peerings                         : []	

4. **Establecimiento de la configuración entre pares públicos de Azure para el circuito.**

	Asegúrese de que tiene la siguiente información antes de continuar:

	- Una subred /30 para el vínculo principal. Tiene que ser un prefijo IPv4 público válido.
	- Una subred /30 para el vínculo secundario. Tiene que ser un prefijo IPv4 público válido.
	- Un identificador VLAN válido para establecer esta configuración entre pares. Asegúrese de que ninguna otra configuración entre pares en el circuito usa el mismo identificador de VLAN.
	- Número de sistema autónomo (AS) para la configuración entre pares. Puede usar 2 bytes o 4 bytes como números AS. Tiene que usar un número AS público para esta configuración entre pares.
	- Un hash MD5 si opta por utilizar uno. **Esto es opcional**.
	
	Puede ejecutar el siguiente cmdlet para establecer la configuración entre pares públicos de Azure para el circuito.

		Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -Circuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100

		Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

	Puede usar el cmdlet siguiente si decide usar un hash MD5

		Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -Circuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100  -SharedKey "A1B2C3D4"

		Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


	>[AZURE.IMPORTANT] Asegúrese de especificar su número AS como ASN de configuración entre pares y no como cliente ASN.

### Obtención de detalles del emparejamiento público de Azure

Puede obtener detalles de configuración mediante el siguiente cmdlet

		$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

		Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -Circuit $ckt


### Actualización del establecimiento de configuración del emparejamiento público de Azure

Puede actualizar cualquier parte de la configuración mediante el siguiente cmdlet

	Set-AzureRmExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -Circuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 600 

	Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

En el ejemplo anterior se está actualizando el identificador de VLAN del circuito de 200 a 600.

### Eliminación del emparejamiento público de Azure

Puede quitar el establecimiento de configuración entre pares ejecutando el siguiente cmdlet

	Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -Circuit $ckt
	Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

## Emparejamiento de Microsoft

Esta sección proporciona instrucciones sobre cómo crear, obtener, actualizar y eliminar el establecimiento de configuración entre pares de Microsoft para un circuito ExpressRoute.

### Creación del emparejamiento de Microsoft

1. **Importe el módulo de PowerShell para ExpressRoute.**
	
 	Para comenzar a usar los cmdlets de ExpressRoute, debe instalar el programa de instalación de PowerShell más reciente desde la [Galería de PowerShell](http://www.powershellgallery.com/) e importar los módulos del Administrador de recursos de Azure en la sesión de PowerShell. Deberá ejecutar PowerShell como administrador.

	    Install-Module AzureRM

		Install-AzureRM

	Importe todos los módulos de AzureRM.* dentro del intervalo de versiones semánticas conocidas.

		Import-AzureRM

	También puede importar un módulo determinado en dicho intervalo.
		
		Import-Module AzureRM.Network 

	Inicie sesión en su cuenta.

		Login-AzureRmAccount

	Seleccione la suscripción en la que desea crear un circuito ExpressRoute.
		
		Select-AzureRmSubscription -SubscriptionId "<subscription ID>"

2. **Creación de un circuito ExpressRoute**
	
	Siga las instrucciones para crear un [circuito ExpressRoute](expressroute-howto-circuit-arm.md) y habilite su aprovisionamiento a través del proveedor de conectividad.

	Si su proveedor de conectividad ofrece servicios administrados de nivel 3, puede solicitarle que habilite la configuración entre pares privados de Azure. En ese caso, no necesita seguir las instrucciones que aparecen en las secciones siguientes. Por otra parte, si su proveedor de conectividad no administra este enrutamiento, una vez que cree el circuito siga las instrucciones siguientes.

3. **Compruebe el circuito ExpressRoute para asegurarse de que está aprovisionado**

	En primer lugar tiene que comprobar si el circuito ExpressRoute tiene los estados Aprovisionado y Habilitado. Observe el ejemplo siguiente.

		Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

	La respuesta tendrá un aspecto similar al siguiente ejemplo:

		Name                             : ExpressRouteARMCircuit
		ResourceGroupName                : ExpressRouteResourceGroup
		Location                         : westus
		Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
		Etag                             : W/"################################"
		ProvisioningState                : Succeeded
		Sku                              : {
		                                     "Name": "Standard_MeteredData",
		                                     "Tier": "Standard",
		                                     "Family": "MeteredData"
		                                   }
		CircuitProvisioningState         : Enabled
		ServiceProviderProvisioningState : Provisioned
		ServiceProviderNotes             : 
		ServiceProviderProperties        : {
		                                     "ServiceProviderName": "Equinix",
		                                     "PeeringLocation": "Silicon Valley",
		                                     "BandwidthInMbps": 200
		                                   }
		ServiceKey                       : **************************************
		Peerings                         : []	
4. **Establecimiento de la configuración entre pares de Microsoft para el circuito**

	Asegúrese de que tiene la siguiente información antes de empezar:

	- Una subred /30 para el vínculo principal. Debe ser un prefijo de IPv4 público válido que sea de su propiedad y esté registrado en un Registro regional de Internet (RIR) o un Registro de enrutamiento de Internet (IRR).
	- Una subred /30 para el vínculo secundario. Debe ser un prefijo de IPv4 público válido que sea de su propiedad y esté registrado en un Registro regional de Internet (RIR) o un Registro de enrutamiento de Internet (IRR).
	- Un identificador VLAN válido para establecer esta configuración entre pares. Asegúrese de que ninguna otra configuración entre pares en el circuito usa el mismo identificador de VLAN.
	- Número de sistema autónomo (AS) para la configuración entre pares. Puede usar 2 bytes o 4 bytes como números AS. Debe utilizar solamente números AS públicos. El número AS tiene que ser propiedad suya.
	- Prefijos anunciados: tiene que proporcionar una lista de todos los prefijos que planea anunciar en la sesión BGP. Se aceptan solo prefijos de direcciones IP públicas. Puede enviar una lista separada por comas si tiene pensado enviar un conjunto de prefijos. Estos prefijos tienen que estar registrados a su nombre en un Registro regional de Internet (RIR) o un Registro de enrutamiento de Internet (IRR).
	- Cliente ASN: si los prefijos anunciados están registrados en el número AS de configuración entre pares, puede especificar el número de AS en el que están registrados. **Esto es opcional**.
	- Nombre del enrutamiento del Registro: puede especificar el RIR o TIR en el que están registrados el número AS y los prefijos.
	- Un hash MD5 si opta por usar uno. **Esto es opcional.**
	
	Puede ejecutar el siguiente cmdlet para configurar el emparejamiento de Microsoft para el circuito.

		Add-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -Circuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "123.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

		Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


### Obtención de detalles del emparejamiento de Microsoft

Puede obtener detalles sobre la configuración mediante el siguiente cmdlet.

		$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

		Get-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -Circuit $ckt


### Actualización de la configuración de emparejamiento de Microsoft

Puede actualizar cualquier parte de la configuración mediante el siguiente cmdlet.

		Set-AzureRmExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -Circuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "124.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

		Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
		

### Eliminación del emparejamiento de Microsoft

Puede quitar el establecimiento de configuración entre pares ejecutando el siguiente cmdlet.

	Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -Circuit $ckt

	Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

## Pasos siguientes

Siguiente paso, [Vinculación de redes virtuales a circuitos ExpressRoute](expressroute-howto-linkvnet-arm.md).

-  Para más información sobre los flujos de trabajo de ExpressRoute, vea [Flujos de trabajo de ExpressRoute](expressroute-workflows.md).

-  Para más información sobre el emparejamiento de circuitos, vea [Circuitos y dominios de enrutamiento de ExpressRoute](expressroute-circuit-peerings.md).

-  Para más información sobre redes virtuales, vea [Información general sobre redes virtuales](../virtual-network/virtual-networks-overview.md).

<!---HONumber=AcomDC_0302_2016-->