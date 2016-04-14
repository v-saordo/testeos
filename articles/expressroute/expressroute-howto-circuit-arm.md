<properties
   pageTitle="Creación y modificación de un circuito ExpressRoute mediante Resource Manager y PowerShell | Microsoft Azure"
   description="Este artículo describe cómo crear y aprovisionar un circuito ExpressRoute. También muestra cómo comprobar el circuito, actualizarlo, o eliminarlo y desaprovisionarlo."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carolz"
   editor=""
   tags="azure-resource-manager"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/04/2015"
   ms.author="cherylmc"/>

# Creación y modificación de un circuito ExpressRoute mediante Resource Manager y PowerShell

   > [AZURE.SELECTOR]
   [PowerShell - Classic](expressroute-howto-circuit-classic.md)
   [PowerShell - Resource Manager](expressroute-howto-circuit-arm.md)

En este artículo se describe cómo crear un circuito Azure ExpressRoute mediante los cmdlets de Windows PowerShell y el modelo de implementación de Azure Resource Manager. Los siguientes pasos también le mostrarán cómo comprobar el estado del circuito, actualizarlo, o eliminarlo y desaprovisionarlo.

   [AZURE.INCLUDE [vpn-gateway-sm-rm](../../includes/vpn-gateway-sm-rm-include.md)]

## Requisitos previos de configuración

Para crear un circuito ExpressRoute, necesita:

- Obtener la versión más reciente de los módulos de Azure PowerShell, versión 1.0 o posterior. Para obtener instrucciones detalladas sobre cómo configurar el equipo para usar los módulos de Azure PowerShell, siga las instrucciones de la página [Cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md).
- Revise la página [Requisitos previos](expressroute-prerequisites.md) y la página [Flujos de trabajo](expressroute-workflows.md) antes de comenzar la configuración.

## Creación y aprovisionamiento de un circuito ExpressRoute

**Paso 1: Importar el módulo de PowerShell para ExpressRoute.**

Para comenzar a usar los cmdlets de ExpressRoute, debe instalar el programa de instalación de PowerShell más reciente desde la [Galería de PowerShell](http://www.powershellgallery.com/) e importar los módulos de Resource Manager en la sesión de PowerShell. Deberá ejecutar PowerShell como administrador.

```
Install-Module AzureRM

Install-AzureRM
```

Importe todos los módulos de AzureRM dentro del intervalo de versiones semánticas conocidas:

```
Import-AzureRM
```

También puede importar un módulo determinado dentro del intervalo de versiones semánticas conocidas:

```
Import-Module AzureRM.Network
```

Inicie sesión en su cuenta:

```
Login-AzureRmAccount
```

Seleccione la suscripción en la que desea crear un circuito ExpressRoute:

```
Select-AzureRmSubscription -SubscriptionId "<subscription ID>"   			
```

**Paso 2. Obtener la lista de proveedores, ubicaciones y anchos de banda admitidos.**

Antes de crear un circuito ExpressRoute, necesita una lista de proveedores de conectividad, ubicaciones admitidas y opciones de ancho de banda. El cmdlet *Get-AzureRmExpressRouteServiceProvider* de PowerShell devuelve esta información, que más tarde se usará en otros pasos.

```
PS C:\> Get-AzureRmExpressRouteServiceProvider
```

Compruebe si aparece su proveedor de conectividad. Tome nota de lo siguiente, porque lo necesitará más adelante cuando cree un circuito:

- Nombre
- PeeringLocations
- BandwidthsOffered

Ahora está listo para crear un circuito ExpressRoute.

**Paso 3. Crear un circuito ExpressRoute.**

Si todavía no tiene un grupo de recursos, debe crear uno antes de crear ExpressRoute. Para ello, ejecute el siguiente comando:

```
New-AzureRmResourceGroup -Name “ExpressRouteResourceGroup” -Location "West US"
```

En el ejemplo siguiente se muestra cómo crear un circuito ExpressRoute de 200 Mbps a través de Equinix en Silicon Valley. Si usa otro proveedor y otra configuración, sustituya esa información al realizar la solicitud. A continuación se muestra un ejemplo de solicitud para una nueva clave de servicio:

```
New-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup" -Location "West US" -SkuTier Standard -SkuFamily MeteredData -ServiceProviderName "Equinix" -PeeringLocation "Silicon Valley" -BandwidthInMbps 200
```

Asegúrese de que especifica el nivel y la familia correctos de SKU.

- El nivel de SKU determina si está habilitado un complemento estándar o premium de ExpressRoute. Puede especificar *estándar* para obtener la SKU estándar o *premium* para el complemento premium.
- La familia de SKU determina el tipo de facturación. Puede seleccionar *metereddata* para el plan de datos limitado y *unlimiteddata* para el plan de datos ilimitado. **Nota:** Después de crear un circuito, no podrá cambiar el tipo de facturación.

La respuesta contiene la clave del servicio. Puede obtener una descripción detallada de todos los parámetros ejecutando lo siguiente:

```
get-help New-AzureRmExpressRouteCircuit -detailed
```

**Paso 4: Obtener una lista de todos los circuitos ExpressRoute.**

Para obtener una lista de todos los circuitos ExpressRoute creados, ejecute el comando *Get-AzureRmExpressRouteCircuit*:

```
#Getting service key
Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```

La respuesta será similar al ejemplo siguiente:

```
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
CircuitProvisioningState          : Enabled
ServiceProviderProvisioningState  : NotProvisioned
ServiceProviderNotes              :
ServiceProviderProperties         : {
                                      "ServiceProviderName": "Equinix",
                                      "PeeringLocation": "Silicon Valley",
                                      "BandwidthInMbps": 200
                                    }
ServiceKey                        : **************************************
Peerings                          : []
```

Puede recuperar esta información en cualquier momento mediante el cmdlet *Get-AzureRmExpressRouteCircuit*. Si se realiza la llamada sin parámetros, se obtendrá una lista de todos los circuitos. La clave de servicio se mostrará en el campo *ServiceKey*.

```
Get-AzureRmExpressRouteCircuit
```

La respuesta será similar al ejemplo siguiente:

```
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
ServiceProviderProvisioningState : NotProvisioned
ServiceProviderNotes             :
ServiceProviderProperties        : {
                                     "ServiceProviderName": "Equinix",
                                     "PeeringLocation": "Silicon Valley",
                                     "BandwidthInMbps": 200
   		                           }
ServiceKey                       : **************************************
Peerings                         : []
```

Puede obtener una descripción detallada de todos los parámetros ejecutando lo siguiente:

```
get-help Get-AzureRmExpressRouteCircuit -detailed
```

**Paso 5: Enviar la clave de servicio al proveedor de conectividad para aprovisionamiento.**

Cuando se crea un nuevo circuito ExpressRoute, dicho circuito estará en el siguiente estado:

```
ServiceProviderProvisioningState : NotProvisioned

CircuitProvisioningState         : Enabled
```

*ServiceProviderProvisioningState* proporciona información sobre el estado actual de aprovisionamiento en el lado del proveedor de servicios y Status proporciona el estado en el lado de Microsoft. Para poder usar un circuito ExpressRoute, dicho circuito tiene que estar en el siguiente estado.

```
ServiceProviderProvisioningState : Provisioned

CircuitProvisioningState         : Enabled
```

El circuito cambiará al estado siguiente cuando el proveedor de conectividad se encuentre en el proceso de habilitarlo:

```
ServiceProviderProvisioningState : Provisioned

Status                           : Enabled
```

**Paso 6. Comprobar periódicamente el estado y la condición de la clave del circuito.**

La comprobación del estado y la condición de la clave de circuito le informa cuando el proveedor ha habilitado el circuito. Después de configurar el circuito, *ServiceProviderProvisioningState* aparece como *Provisioned* (aprovisionado), tal como se muestra en el ejemplo siguiente.

```
Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```

La respuesta será similar al ejemplo siguiente:

```
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

**Step 7.  Create your routing configuration.**

For step-by-step instructions, refer to the [ExpressRoute circuit routing configuration (create and modify circuit peerings)](expressroute-howto-routing-arm.md).

**Step 8.  Link a virtual network to an ExpressRoute circuit.**

Next, link a virtual network to your ExpressRoute circuit. You can use [this template](https://github.com/Azure/azure-quickstart-templates/tree/ecad62c231848ace2fbdc36cbe3dc04a96edd58c/301-expressroute-circuit-vnet-connection) when you work with the Resource Manager deployment mode. We're currently working on steps to accomplish this by using PowerShell.

## Get the status of an ExpressRoute circuit

You can retrieve this information at any time by using the *Get-AzureRmExpressRouteCircuit* cmdlet. Making the call with no parameters will list all circuits.

```
Get-AzureRmExpressRouteCircuit
```

The response will be similar to the following example:

```
Name : ExpressRouteARMCircuit ResourceGroupName : ExpressRouteResourceGroup Location : westus Id : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit Etag : W/"################################" ProvisioningState : Succeeded Sku : { "Name": "Standard\_MeteredData", "Tier": "Standard", "Family": "MeteredData" } CircuitProvisioningState : Enabled ServiceProviderProvisioningState : Provisioned ServiceProviderNotes : ServiceProviderProperties : { "ServiceProviderName": "Equinix", "PeeringLocation": "Silicon Valley", "BandwidthInMbps": 200 } ServiceKey : ************************************** Peerings :
```

You can get information on a specific ExpressRoute circuit by passing the resource group name and circuit name as a parameter to the call:

```
Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```

The response will look similar to the following example:

```
Name : ExpressRouteARMCircuit ResourceGroupName : ExpressRouteResourceGroup Location : westus Id : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit Etag : W/"################################" ProvisioningState : Succeeded Sku : { "Name": "Standard\_MeteredData", "Tier": "Standard", "Family": "MeteredData" } CircuitProvisioningState : Enabled ServiceProviderProvisioningState : Provisioned ServiceProviderNotes : ServiceProviderProperties : { "ServiceProviderName": "Equinix", "PeeringLocation": "Silicon Valley", "BandwidthInMbps": 200 } ServiceKey : ************************************** Peerings :
```

You can get detailed descriptions of all parameters by running the following:

```
get-help get-azurededicatedcircuit -detailed
```

## Modify an ExpressRoute circuit

You can modify certain properties of an ExpressRoute circuit without impacting connectivity.

You can do the following, with no downtime:

- Enable or disable an ExpressRoute premium add-on for your ExpressRoute circuit.
- Increase the bandwidth of your ExpressRoute circuit.

For more information on limits and limitations, refer to the [ExpressRoute FAQ](expressroute-faqs.md) page.

### Enable the ExpressRoute premium add-on

You can enable the ExpressRoute premium add-on for your existing circuit by using the following PowerShell snippet:

```
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Name = "Premium" $ckt.sku.Name = "Premium\_MeteredData"

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

The circuit will now have the ExpressRoute premium add-on features enabled. Note that Microsoft will begin billing you for the premium add-on capability as soon as the command has successfully run.

### Disable the ExpressRoute premium add-on

You can disable the ExpressRoute premium add-on for the existing circuit by using the following PowerShell cmdlet:

```
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Standard" $ckt.sku.Name = "Standard\_MeteredData"

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

The premium add-on is now disabled for the circuit.

Note that this operation can fail if you are using resources greater than what is permitted for the standard circuit.

- Before you downgrade from premium to standard, you must ensure that the number of virtual networks linked to the circuit is less than 10. If you don't do so, your update request fails and Microsoft will bill you at premium rates.
- You must unlink all virtual networks in other geopolitical regions. If you don't do so, your update request will fail and Microsoft will bill you at premium rates.
- Your route table must be less than 4,000 routes for private peering. If your route table size is greater than 4,000 routes, the BGP session drops and won't be reenabled until the number of advertised prefixes goes below 4,000.

### Update the ExpressRoute circuit bandwidth

For supported bandwidth options for your provider, check the [ExpressRoute FAQ](expressroute-faqs.md) page. You can pick any size greater than the size of your existing circuit. After you decide what size you need, use the following command to resize your circuit:

```
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.ServiceProviderProperties.BandwidthInMbps = 1000

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

Your circuit will be sized up on the Microsoft side. Then you must contact your connectivity provider to update configurations on their side to match this change. After you make this notification, Microsoft will begin billing you for the updated bandwidth option.

**Important**: You cannot reduce the bandwidth of an ExpressRoute circuit without disruption. Downgrading bandwidth requires you to deprovision the ExpressRoute circuit and then reprovision a new ExpressRoute circuit.

## Delete and deprovision an ExpressRoute circuit

You can delete your ExpressRoute circuit by running the following command:

```
Remove-AzureRmExpressRouteCircuit -ResourceGroupName "ExpressRouteResourceGroup" -Name "ExpressRouteARMCircuit" ```

Tenga en cuenta que para realizar esta operación correctamente tiene que desvincular todas las redes virtuales del circuito ExpressRoute. Si se produce un error en esta operación, compruebe si hay alguna red virtual vinculada al circuito.

Si el estado de aprovisionamiento del proveedor de servicios del circuito ExpressRoute está habilitado, la condición cambiará de habilitado a *deshabilitado*. Tiene que cooperar con su proveedor de servicios para desaprovisionar el circuito en su lado. Microsoft continuará reservando recursos y facturándole por ello hasta que el proveedor de servicios complete el desaprovisionamiento del circuito y nos lo notifique.

Si el proveedor de servicios ha desaprovisionado el circuito (el estado de aprovisionamiento del proveedor de servicios está establecido en *no aprovisionado*) antes de ejecutar el cmdlet anterior, Microsoft cancelará el aprovisionamiento del circuito y dejaremos de facturarle.

## Pasos siguientes

Después de crear el circuito, asegúrese de hacer lo siguiente:

- [Crear y modificar el enrutamiento para el circuito ExpressRoute](expressroute-howto-routing-arm.md)
- [Vincular la red virtual a su circuito ExpressRoute](expressroute-howto-linkvnet-arm.md)

<!---HONumber=AcomDC_0302_2016-->