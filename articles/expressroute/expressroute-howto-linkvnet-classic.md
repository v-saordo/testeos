<properties 
   pageTitle="Vinculación de redes virtuales a circuitos ExpressRoute | Microsoft Azure"
   description="En este documento se proporciona información general de cómo vincular redes virtuales a circuitos ExpressRoute."
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
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
   ms.author="cherylmc" />

# Vinculación de la red virtual a circuitos ExpressRoute

> [AZURE.SELECTOR]
- [PowerShell - Classic](expressroute-howto-linkvnet-classic.md)
- [PowerShell - Resource Manager] (expressroute-howto-linkvnet-arm.md)  
- [Template - Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/ecad62c231848ace2fbdc36cbe3dc04a96edd58c/301-expressroute-circuit-vnet-connection)

En este artículo se ofrece información general de cómo vincular redes virtuales a circuitos ExpressRoute. Las redes virtuales pueden estar en la misma suscripción o formar parte de otra suscripción. Este artículo se aplican a redes virtuales que usan el modelo de implementación clásico. Si quiere vincular una red virtual que se implementó usando el modelo de implementación del Administrador de recursos de Azure, vea [Vinculación de la red virtual a circuitos ExpressRoute](expressroute-howto-linkvnet-arm.md).

[AZURE.INCLUDE [vpn-gateway-sm-rm](../../includes/vpn-gateway-sm-rm-include.md)]

## Requisitos previos de configuración

- Necesitará la versión más reciente de los cmdlets de Azure PowerShell. Puede descargar el módulo de PowerShell más reciente desde la sección de PowerShell en la [página de descargas de Azure](https://azure.microsoft.com/downloads/). Siga las instrucciones de la página [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md) para obtener instrucciones detalladas sobre cómo configurar el equipo para usar los módulos de Azure PowerShell. 
- Asegúrese de que ha revisado la página de [requisitos previos](expressroute-prerequisites.md), la página de [requisitos de enrutamiento](expressroute-routing.md) y la página de [flujos de trabajo](expressroute-workflows.md) antes de comenzar la configuración.
- Debe tener un circuito ExpressRoute activo. 
	- Siga las instrucciones para [crear un circuito ExpressRoute](expressroute-howto-circuit-classic.md) y habilite el circuito mediante el proveedor de conectividad. 
	- Asegúrese de que dispone de un emparejamiento privado de Azure configurado para el circuito. Consulte el artículo de [configuración del enrutamiento](expressroute-howto-routing-classic.md) para obtener instrucciones sobre el enrutamiento. 
	- El emparejamiento privado de Azure debe configurarse y el emparejamiento BGP entre la red y Microsoft debe estar activo para habilitar la conectividad de extremo a extremo.

Es posible vincular hasta 10 redes virtuales a un circuito ExpressRoute. Todos los circuitos ExpressRoute deben estar en la misma región geopolítica. Puede vincular un mayor número de redes virtuales en el circuito ExpressRoute si habilitó el complemento premium de ExpressRoute. Consulte las [preguntas más frecuentes](expressroute-faqs.md) para obtener más detalles sobre el complemento premium.

## Vinculación de una red virtual en la misma suscripción de Azure a un circuito ExpressRoute

Puede vincular una red virtual a un circuito ExpressRoute mediante el siguiente cmdlet. Asegúrese de que la puerta de enlace de red virtual se crea y está lista para vincular antes de ejecutar el cmdlet.

	C:\> New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
	Provisioned

## Vinculación de una red virtual en una suscripción de Azure diferente a un circuito ExpressRoute

Se puede compartir un circuito ExpressRoute entre varias suscripciones. En la ilustración siguiente se muestra un sencillo esquema de cómo funciona el uso compartido de circuitos ExpressRoute entre varias suscripciones. Cada una de las nubes más pequeñas dentro de la nube de gran tamaño se usa para representar las suscripciones que pertenecen a diferentes departamentos dentro de una organización. Cada departamento de la organización puede usar su propia suscripción para implementar sus servicios, pero puede compartir un único circuito ExpressRoute para volver a conectarse a la red local. Un solo departamento (en este ejemplo: TI) puede ser el propietario del circuito ExpressRoute. Otras suscripciones dentro de la organización pueden usar el circuito ExpressRoute.

>[AZURE.NOTE] Los cargos de conectividad y ancho de banda de un circuito dedicado recaerán en el propietario del circuito ExpressRoute. Todas las redes virtuales comparten el mismo ancho de banda.

![Conectividad entre suscripciones](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### Administración

El *propietario del circuito* es el administrador o coadministrador de la suscripción en la que se crea el circuito ExpressRoute. El propietario del circuito puede autorizar a los administradores o coadministradores de otras suscripciones (denominados *usuario del circuito* en el diagrama de flujo de trabajo) para que usen el circuito dedicado de su propiedad. Los usuarios del circuito autorizados para usar el circuito ExpressRoute de una organización pueden vincular la red virtual de su suscripción al circuito ExpressRoute una vez que estén autorizados.

El propietario del circuito tiene la capacidad de modificar y revocar las autorizaciones en cualquier momento. La revocación de una autorización dará como resultado la eliminación de todos los vínculos de la suscripción cuyo acceso se haya revocado.

### Operaciones del propietario del circuito 

#### Creación de una autorización
	
El propietario del circuito autoriza a los administradores de otras suscripciones para que usen el circuito especificado. En el ejemplo siguiente, el administrador del circuito (TI de Contoso) permite que el administrador de otra suscripción (Dev-Test), especificando su identificador de Microsoft, cree un vínculo de 2 redes virtuales al circuito. El cmdlet no envía correo electrónico al identificador de Microsoft especificado. El propietario del circuito debe notificar de forma explícita al propietario de la otra suscripción que la autorización se ha completado.

		PS C:\> New-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -Description "Dev-Test Links" -Limit 2 -MicrosoftIds 'devtest@contoso.com'
		
		Description         : Dev-Test Links 
		Limit               : 2 
		LinkAuthorizationId : ********************************** 
		MicrosoftIds        : devtest@contoso.com 
		Used                : 0

#### Revisión de las autorizaciones

El propietario del circuito puede revisar todas las autorizaciones emitidas en un circuito concreto ejecutando el siguiente cmdlet.

	PS C:\> Get-AzureDedicatedCircuitLinkAuthorization -ServiceKey: "**************************"
	
	Description         : EngineeringTeam 
	Limit               : 3 
	LinkAuthorizationId : #################################### 
	MicrosoftIds        : engadmin@contoso.com 
	Used                : 1 
	
	Description         : MarketingTeam 
	Limit               : 1 
	LinkAuthorizationId : @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@ 
	MicrosoftIds        : marketingadmin@contoso.com 
	Used                : 0 
	
	Description         : Dev-Test Links 
	Limit               : 2 
	LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&& 
	MicrosoftIds        : salesadmin@contoso.com 
	Used                : 2 
	

#### Actualización de autorizaciones

El propietario del circuito puede modificar las autorizaciones mediante el siguiente cmdlet.

	PS C:\> set-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -AuthorizationId "&&&&&&&&&&&&&&&&&&&&&&&&&&&&"-Limit 5
		
	Description         : Dev-Test Links 
	Limit               : 5 
	LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&& 
	MicrosoftIds        : devtest@contoso.com 
	Used                : 0


#### Eliminación de autorizaciones

El propietario del circuito puede revocar o eliminar las autorizaciones al usuario ejecutando el siguiente cmdlet.

	PS C:\> Remove-AzureDedicatedCircuitLinkAuthorization -ServiceKey "*****************************" -AuthorizationId "###############################"


### Operaciones del usuario del circuito

#### Revisión de las autorizaciones

El usuario del circuito puede revisar las autorizaciones mediante el siguiente cmdlet.

	PS C:\> Get-AzureAuthorizedDedicatedCircuit
		
	Bandwidth                        : 200
	CircuitName                      : ContosoIT
	Location                         : Washington DC
	MaximumAllowedLinks              : 2
	ServiceKey                       : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
	ServiceProviderName              : equinix
	ServiceProviderProvisioningState : Provisioned
	Status                           : Enabled
	UsedLinks                        : 0

#### Canjear autorizaciones de vínculo

El usuario del circuito puede ejecutar el siguiente cmdlet para canjear una autorización de vínculo.

	PS C:\> New-AzureDedicatedCircuitLink –servicekey "&&&&&&&&&&&&&&&&&&&&&&&&&&" –VnetName 'SalesVNET1' 
		
	State VnetName 
	----- -------- 
	Provisioned SalesVNET1

## Pasos siguientes

Para obtener más información acerca de ExpressRoute, consulte [P+F de ExpressRoute](expressroute-faqs.md).

<!---HONumber=AcomDC_0204_2016-->