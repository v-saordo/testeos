<properties
   pageTitle="Pasos para configurar un circuito ExpressRoute mediante PowerShell | Microsoft Azure"
   description="Este artículo le guiará por los pasos necesarios para crear y aprovisionar un circuito ExpressRoute. También se muestra cómo comprobar el estado, actualizar, o eliminar y desaprovisionar un circuito ExpressRoute."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/04/2016"
   ms.author="cherylmc"/>

# Creación y modificación de un circuito ExpressRoute mediante PowerShell

> [AZURE.SELECTOR]
[PowerShell - Classic](expressroute-howto-circuit-classic.md)
[PowerShell - Resource Manager](expressroute-howto-circuit-arm.md)

Este artículo le guiará por los pasos necesarios para crear un circuito ExpressRoute con los cmdlets de PowerShell y el modelo de implementación **clásica**. Los siguientes pasos también le mostrarán cómo comprobar el estado, actualizar, o eliminar y desaprovisionar un circuito ExpressRoute. Si quiere crear y modificar un circuito de ExpressRoute con el modelo de implementación del **Administrador de recursos**, consulte [Creación y modificación de un circuito ExpressRoute mediante el Administrador de recursos de Azure y PowerShell](expressroute-howto-circuit-arm.md).

[AZURE.INCLUDE [vpn-gateway-sm-rm](../../includes/vpn-gateway-sm-rm-include.md)]


## Requisitos previos de configuración

- Necesitará la versión más reciente de los cmdlets de Azure PowerShell. Puede descargar el módulo de PowerShell más reciente desde la sección de PowerShell en la [página de descargas de Azure](https://azure.microsoft.com/downloads/). Siga las instrucciones de la página [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md) para obtener instrucciones detalladas sobre cómo configurar el equipo para usar los módulos de Azure PowerShell. 
- Asegúrese de que ha revisado la página [Requisitos previos](expressroute-prerequisites.md) y la página [Flujos de trabajo](expressroute-workflows.md) antes de comenzar la configuración.

## Para crear y aprovisionar un circuito ExpressRoute

1. **Importe el módulo de PowerShell para ExpressRoute.**

 	Tiene que importar los módulos de Azure y ExpressRoute en la sesión de PowerShell para poder usar los cmdlets de ExpressRoute. Ejecute los siguientes comandos para importar los módulos de Azure y ExpressRoute en la sesión de PowerShell.

	    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
	    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

2. **Obtenga la lista de proveedores, ubicaciones y anchos de banda admitidos.**

	Antes de crear un circuito ExpressRoute, necesitará una lista de proveedores de conectividad, ubicaciones admitidas y opciones de ancho de banda. El cmdlet de PowerShell *Get-AzureDedicatedCircuitServiceProvider* devuelve esta información que se usará más adelante en otros pasos. Cuando se ejecuta el cmdlet, el resultado tendrá un aspecto similar al siguiente ejemplo.

		PS C:\> Get-AzureDedicatedCircuitServiceProvider

		Name                 DedicatedCircuitLocations      DedicatedCircuitBandwidths                                                                                                                                                                                   
		----                 -------------------------      --------------------------                                                                                                                                                                                   
		Aryaka Networks      Silicon Valley,Washington      200Mbps:200, 500Mbps:500, 1Gbps:1000                                                                                                                                                                         
		                     DC,Singapore                                                                                                                                                                                                                                
		AT&T                 Silicon Valley,Washington DC   10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		AT&T Netbond         Washington DC,Silicon          10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		                     Valley,Dallas                                                                                                                                                                                                                               
		British Telecom      London,Amsterdam,Washington    10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		                     DC,Tokyo,Silicon Valley                                                                                                                                                                                                                     
		Colt Ethernet        Amsterdam,London               200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		Colt IPVPN           Amsterdam,London               10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		Comcast              Washington DC,Silicon Valley   200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		Equinix              Amsterdam,Atlanta,Chicago,Dall 200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		                     as,New York,Seattle,Silicon                                                                                                                                                                                                                 
		                     Valley,Washington                                                                                                                                                                                                                           
		                     DC,London,Hong Kong,Singapore,                                                                                                                                                                                                              
		                     Sydney,Tokyo,Sao Paulo,Los                                                                                                                                                                                                                  
		                     Angeles,Melbourne                                                                                                                                                                                                                           
		IIJ                  Tokyo                          10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		InterCloud           Washington                     200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		                     DC,London,Singapore,Amsterdam                                                                                                                                                                                                               
		Internet Solutions   London,Amsterdam               10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		– Cloud Connect                                                                                                                                                                                                                                                  
		Interxion            Amsterdam                      200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		Level 3              London,Chicago,Dallas,Seattle, 200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		Communications -     Silicon Valley,Washington DC                                                                                                                                                                                                                
		Exchange                                                                                                                                                                                                                                                         
		Level 3              London,Chicago,Dallas,Seattle, 10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		Communications -     Silicon Valley,Washington DC                                                                                                                                                                                                                
		IPVPN                                                                                                                                                                                                                                                            
		Megaport             Melbourne,Sydney               200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		NEXTDC               Melbourne                      200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		NTT Communications   Tokyo                          10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		Orange               Amsterdam,London,Silicon       10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		                     Valley,Washington DC,Hong Kong                                                                                                                                                                                                              
		PCCW Global Limited  Hong Kong                      10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		SingTel Domestic     Singapore                      10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		SingTel              Singapore                      10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		International                                                                                                                                                                                                                                                    
		Tata Communications  Hong Kong,Singapore,London,Ams 10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		                     terdam,Chennai,Mumbai                                                                                                                                                                                                                       
		TeleCity Group       Amsterdam,London               200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		Telstra Corporation  Sydney,Melbourne               10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		Verizon              London,Hong Kong,Silicon       10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		                     Valley,Washington DC                                                                                                                                                                                                                        
		Vodafone             London                         10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		Zayo Group           Washington DC                  200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
    	

3. **Creación de un circuito ExpressRoute.**

	En el ejemplo siguiente se muestra cómo crear un circuito ExpressRoute de 200 Mbps a través de Equinix en Silicon Valley. Si usa otro proveedor y otra configuración, sustituya la información al realizar la solicitud.

	A continuación se muestra un ejemplo de solicitud para una nueva clave de servicio:

		#Creating a new circuit
		$Bandwidth = 200
		$CircuitName = "MyTestCircuit"
		$ServiceProvider = "Equinix"
		$Location = "Silicon Valley"

		New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location -sku Standard -BillingType MeteredData 

	O bien, si desea crear un circuito ExpressRoute con el complemento Premium, use el ejemplo a continuación. Consulte la página [P+F de ExpressRoute](expressroute-faqs.md) para más información sobre el complemento Premium.

		New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location -sku Premium - BillingType MeteredData
	
	
	La respuesta contendrá la clave del servicio. Puede obtener una descripción detallada de todos los parámetros ejecutando lo siguiente:

		get-help new-azurededicatedcircuit -detailed 

4. **Obtenga una lista de todos los circuitos ExpressRoute.**

	Puede ejecutar el comando *Get-AzureDedicatedCircuit* para obtener una lista de todos los circuitos ExpressRoute que haya creado.

		#Getting service key
		Get-AzureDedicatedCircuit

	La respuesta tendrá un aspecto similar al siguiente ejemplo:

		Bandwidth                        : 200
		CircuitName                      : MyTestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : NotProvisioned
		Sku                              : Standard
		Status                           : Enabled

	Puede recuperar esta información en cualquier momento con el cmdlet *Get-AzureDedicatedCircuit*. Si se realiza la llamada sin parámetros, se obtendrá una lista de todos los circuitos. La clave de servicio se mostrará en el campo *ServiceKey*.

		PS C:\> Get-AzureDedicatedCircuit

		Bandwidth                        : 200
		CircuitName                      : MyTestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : NotProvisioned
		Sku                              : Standard
		Status                           : Enabled

	Puede obtener una descripción detallada de todos los parámetros ejecutando lo siguiente:

		get-help get-azurededicatedcircuit -detailed 

5. **Envíe la clave de servicio a su proveedor de conectividad para su aprovisionamiento.**

	Cuando se crea un nuevo circuito ExpressRoute, el circuito estará en el siguiente estado:
	
		ServiceProviderProvisioningState : NotProvisioned
		
		Status                           : Enabled

	*ServiceProviderProvisioningState* proporciona información sobre el estado actual de aprovisionamiento en la parte del proveedor del servicios y el estado proporciona el estado en Microsoft. Un circuito ExpressRoute tiene que estar en el siguiente estado para poder usarlo.

		ServiceProviderProvisioningState : Provisioned
		
		Status                           : Enabled

	El circuito pasará al estado siguiente cuando el proveedor de conectividad se encuentre en el proceso de habilitarlo.

		ServiceProviderProvisioningState : Provisioned
		
		Status                           : Enabled



5. **Compruebe periódicamente el estado y la condición de la clave del circuito.**

	Esto le permitirá saber cuándo ha habilitado el circuito el proveedor. Después de configurar el circuito, el parámetro *ServiceProviderProvisioningState* aparecerá como *Provisioned* (aprovisionado), tal como se muestra en el ejemplo siguiente.

		PS C:\> Get-AzureDedicatedCircuit

		Bandwidth                        : 200
		CircuitName                      : MyTestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled

6. **Cree la configuración de enrutamiento.**
	
	Consulte la página [Configuración de enrutamiento de circuitos ExpressRoute (crear y modificar emparejamientos de circuito)](expressroute-howto-routing-classic.md) para obtener instrucciones paso a paso.

7. **Vincule una red virtual a un circuito ExpressRoute.**

	A continuación, vincule una red virtual al circuito ExpressRoute. Consulte [Vinculación de circuitos ExpressRoute a redes virtuales](expressroute-howto-linkvnet-classic.md) para instrucciones paso a paso. Si necesita crear una red virtual con el modelo de implementación clásica de ExpressRoute, consulte [Configurar una red virtual en ExpressRoute](expressroute-howto-vnet-portal-classic.md) para obtener instrucciones.

##  Obtención del estado de un circuito ExpressRoute

Puede recuperar esta información en cualquier momento con el cmdlet *Get-AzureCircuit*. Si se realiza la llamada sin parámetros, se obtendrá una lista de todos los circuitos.

		PS C:\> Get-AzureDedicatedCircuit

		Bandwidth                        : 200
		CircuitName                      : MyTestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled

		Bandwidth                        : 1000
		CircuitName                      : MyAsiaCircuit
		Location                         : Singapore
		ServiceKey                       : #################################
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled

Puede obtener información sobre un circuito ExpressRoute específico, pasando la clave de servicio como un parámetro a la llamada.

		PS C:\> Get-AzureDedicatedCircuit -ServiceKey "*********************************"

		Bandwidth                        : 200
		CircuitName                      : MyTestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled


Puede obtener una descripción detallada de todos los parámetros ejecutando lo siguiente:

		get-help get-azurededicatedcircuit -detailed 

##  Modificación de un circuito ExpressRoute

Puede modificar determinadas propiedades de un circuito ExpressRoute sin afectar a la conectividad.

Puede realizar las siguientes acciones:

- Habilitar o deshabilitar el complemento ExpressRoute Premium en un circuito ExpressRoute sin tiempo de inactividad.
- Aumentar el ancho de banda de su circuito ExpressRoute sin tiempo de inactividad.

Consulte la página [Preguntas más frecuentes sobre ExpressRoute](expressroute-faqs.md) para obtener más información sobre los límites y limitaciones.

### Cómo habilitar el complemento ExpressRoute Premium

Puede habilitar el complemento ExpressRoute Premium en el circuito existente mediante el siguiente cmdlet de PowerShell:
	
		PS C:\> Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Premium
		
		Bandwidth                        : 1000
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Premium
		Status                           : Enabled

El circuito tendrá ahora las características del complemento ExpressRoute Premium habilitadas. Tenga en cuenta que la facturación de la capacidad del complemento Premium comienza en cuanto el comando se ejecuta correctamente.

### Cómo deshabilitar el complemento ExpressRoute Premium

Puede deshabilitar el complemento ExpressRoute Premium en el circuito existente mediante el siguiente cmdlet de PowerShell:
	
		PS C:\> Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Standard
		
		Bandwidth                        : 1000
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled

El complemento Premium ahora está deshabilitado para el circuito.

>[AZURE.IMPORTANT] Esta operación puede producir un error si está utilizando recursos superiores a lo que está permitido para el circuito estándar.
>
>- Debe asegurarse de que el número de redes virtuales vinculadas al circuito es inferior a 10 antes de realizar la degradación de Premium a estándar. Si no lo hace, se producirá un error en la solicitud de actualización y se le factura con las tarifas Premium.
- Tiene que desvincular todas las redes virtuales en otras regiones geopolíticas. Si no lo hace, se producirá un error en la solicitud de actualización y se le factura con las tarifas Premium.
- La tabla de enrutamiento tiene que tener menos de 4.000 rutas para la configuración entre pares privados. Si el tamaño de la tabla de ruta sobrepasa las 4000 rutas, la sesión BGP se anulará y no se volverá a habilitar hasta que el número de prefijos anunciados esté por debajo de 4.000.


### Cómo actualizar el ancho de banda de circuito ExpressRoute

Consulte la página [Preguntas más frecuentes sobre ExpressRoute](expressroute-faqs.md) para conocer las opciones de ancho de banda compatibles con su proveedor. Puede elegir cualquier tamaño mayor que el tamaño de su circuito existente. Cuando haya decidido el tamaño que necesita, puede usar el comando siguiente para cambiar el tamaño del circuito.

		PS C:\> Set-AzureDedicatedCircuitProperties -ServiceKey ********************************* -Bandwidth 1000
		
		Bandwidth                        : 1000
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled

El circuito habrá cambiado de tamaño en el lado de Microsoft. Debe ponerse en contacto con su proveedor de conectividad para actualizar las configuraciones de su parte para que coincidan con este cambio. Tenga en cuenta que le facturará la opción de ancho de banda actualizada desde este momento.

>[AZURE.IMPORTANT] No podrá reducir el ancho de banda de un circuito ExpressRoute sin interrupciones. Degradar de ancho de banda requiere que cancele el aprovisionamiento del circuito ExpressRoute y, a continuación, vuelva a aprovisionar un nuevo circuito ExpressRoute.

##  Eliminación y desaprovisionamiento de un circuito ExpressRoute

Puede eliminar el circuito ExpressRout con la ejecución del siguiente comando:

		PS C:\> Remove-AzureDedicatedCircuit -ServiceKey "*********************************"	

Tenga en cuenta que para realizar esta operación correctamente tiene que desvincular todas las redes virtuales de ExpressRoute. Si se produce un error en esta operación compruebe si tiene alguna red virtual vinculada al circuito.

Si está habilitado el estado de aprovisionamiento del proveedor de servicios del circuito ExpressRoute, el estado cambiará de habilitado a *deshabilitado*. Tiene que cooperar con su proveedor de servicios para desaprovisionar el circuito en su lado. Hasta que el proveedor de servicios finalice la cancelación del aprovisionamiento del circuito y nos envíe una notificación continuaremos reservando recursos y facturándole por ello.

Si el proveedor de servicios ha desaprovisionado el circuito (el estado de aprovisionamiento del proveedor de servicios está establecido en *no aprovisionado*) antes de ejecutar el cmdlet anterior, cancelaremos el aprovisionamiento del circuito y dejaremos de facturarle.

## Pasos siguientes

- [Configuración del enrutamiento](expressroute-howto-routing-classic.md)

<!---HONumber=AcomDC_0211_2016-->