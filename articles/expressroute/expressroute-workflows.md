<properties
   pageTitle="Flujo de trabajo para configurar un circuito ExpressRoute | Microsoft Azure"
   description="Esta página le guiará a través de los flujos de trabajo para configurar el circuito ExpressRoute y las configuraciones entre pares"
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carolz"
   editor="" />
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/21/2016"
   ms.author="cherylmc"/>

# Flujos de trabajo de ExpressRoute para aprovisionamiento de circuitos y estados de circuitos de ExpressRoute
Esta página le guiará a través del aprovisionamiento de servicios y de los flujos de trabajo de configuración del enrutamiento a alto nivel.

![](./media/expressroute-workflows/expressroute-circuit-workflow.png)

Tanto la ilustración como los pasos correspondientes siguientes muestran las tareas que se deben realizar para aprovisionar un circuito ExpressRoute de un extremo a otro.

1. Use PowerShell para configurar un circuito ExpressRoute. Para obtener más información, siga las instrucciones del artículo [Creación y modificación de circuitos de ExpressRoute](expressroute-howto-circuit-classic.md).

2. Solicite conectividad al proveedor de servicios. Este proceso varía. Para obtener más información sobre cómo solicitar conectividad, póngase en contacto con su proveedor de conectividad.

3. Asegúrese de que el circuito se ha aprovisionado correctamente, para lo que debe comprobar el estado de aprovisionamiento del circuito ExpressRoute a través de PowerShell.

4. Configure los dominios de enrutamiento. Si el proveedor de conectividad administra el nivel 3, configurará el enrutamiento del circuito. Si el proveedor de conectividad solo ofrece servicios de nivel 2, debe configurar el enrutamiento según las instrucciones que se describen en las páginas de [requisitos de enrutamiento](expressroute-routing.md) y [configuración de enrutamiento](expressroute-howto-routing-classic.md).

	-  Habilitar la configuración entre pares privados de Azure: debe habilitar esta configuración entre pares para conectarse a las máquinas virtuales/servicios en la nube implementados en redes virtuales.
	-  Habilitar la configuración entre pares públicos de Azure: debe habilitar la configuración entre pares públicos de Azure si desea conectarse a los servicios de Azure hospedados en direcciones IP públicas. Se trata de un requisito para acceder a los recursos de Azure si ha elegido habilitar el enrutamiento predeterminado para la configuración entre pares privados de Azure.
	-  Habilitar la configuración entre pares de Microsoft: debe habilitarlo para acceder a Office 365 y a los servicios en línea de CRM. 
	
	>[AZURE.IMPORTANT] Debe asegurarse de que usa un proxy o borde independientes para conectarse a Microsoft distinto del que usa para Internet. Si usa el mismo borde para ExpressRoute e Internet se producirá un enrutamiento asimétrico y causará interrupciones en la conectividad de la red.

	![](./media/expressroute-workflows/expressroute-routing-workflow.png)

5. Vinculación de redes virtuales a circuitos de ExpressRoute: puede vincular redes virtuales a un circuito ExpressRoute. Siga las instrucciones [para vincular redes virtuales](expressroute-howto-linkvnet-arm.md) al circuito. Dichas redes virtuales pueden estar en la misma suscripción de Azure que el circuito ExpressRoute, o bien pueden estar en una suscripción diferente.


## Estados de aprovisionamiento de circuitos de ExpressRoute

Cada circuito ExpressRoute tiene dos estados:

- Estado de aprovisionamiento de proveedor de servicio
- Estado

Estado representa el estado de aprovisionamiento de Microsoft. Esta propiedad puede estar en cualquiera de los siguientes estados: *Habilitado*, *Habilitando* o *Deshabilitando*. El circuito ExpressRoute debe estar en el estado Enabled para poder usarlo.

El estado de aprovisionamiento del proveedor de conectividad representa el estado del lado del proveedor de conectividad. Puede ser *No aprovisionado*, *Aprovisionando* o *Aprovisionado*. El circuito ExpressRoute debe estar en estado Aprovisionado para poder usarlo.

### Posibles estados de un circuito ExpressRoute

En esta sección se enumeran los posibles estados de un circuito ExpressRoute.

#### En tiempo de creación

Verá el circuito ExpressRoute en el estado que se describe a continuación en cuanto ejecute el cmdlet de PowerShell para crear el circuito ExpressRoute.

	ServiceProviderProvisioningState : NotProvisioned
	Status                           : Enabled


#### Cuando el proveedor de conectividad está en el proceso de aprovisionamiento del circuito

Verá el circuito ExpressRoute en el estado que se describe a continuación en cuanto pase la clave de servicio al proveedor de conectividad y hayan iniciado el proceso de aprovisionamiento.

	ServiceProviderProvisioningState : Provisioning
	Status                           : Enabled


#### Cuando el proveedor de conectividad haya completado el proceso de aprovisionamiento

Verá el circuito ExpressRoute en el estado que se describe a continuación en cuanto el proveedor de conectividad haya completado el proceso de aprovisionamiento.

	ServiceProviderProvisioningState : Provisioned
	Status                           : Enabled

Aprovisionado y habilitado es el único estado en que puede estar el circuito para poder usarlo. Si usa un proveedor de nivel 2, puede configurar el enrutamiento para el circuito solo cuando se encuentre en este estado.

#### Si el desaprovisionamiento lo inicia Microsoft

Verá el circuito ExpressRoute en el estado que se describe a continuación en cuanto ejecute el cmdlet de PowerShell para eliminar el circuito ExpressRoute.

	ServiceProviderProvisioningState : Provisioned
	Status                           : Disabling

Debe ponerse en contacto con su proveedor de conectividad para desaprovisionar el circuito ExpressRoute. **Importante:** Microsoft seguirá facturando el circuito hasta que se ejecute el cmdlet de PowerShell para desaprovisionarlo.

#### Si el desaprovisionamiento lo inicia el proveedor de servicios

Si solicitó al proveedor de servicios que desaprovisione primero el circuito ExpressRoute, verá el circuito establecido en el estado que se describe a continuación después de que el proveedor de servicios haya completado el proceso de desaprovisionamiento.


	ServiceProviderProvisioningState : NotProvisioned
	Status                           : Enabled

Si es necesario, puede volver a habilitarlo, o bien ejecutar los cmdlets de PowerShell para eliminar el circuito. **Importante:** Microsoft seguirá facturando el circuito hasta que se ejecute el cmdlet de PowerShell para desaprovisionarlo.


## Estado de configuración de sesión de enrutamiento

El estado de aprovisionamiento de BGP permite saber si la sesión BGP se ha habilitado en el borde de Microsoft. El estado debe habilitarse para poder usar la configuración entre pares.

Es importante comprobar el estado de sesión de BGP, especialmente para la configuración entre pares de Microsoft. Además del estado de aprovisionamiento de BGP, hay otro estado denominado *estado de prefijos públicos anunciados*. El estado de los prefijos públicos anunciados debe estar en estado *configurado*, tanto para que la sesión BGP esté activa como para que el enrutamiento funcione de un extremo a otro.

Si el estado de los prefijos públicos anunciados se establece en el estado de *validación necesaria*, la sesión BGP no se habilita, ya que los prefijos anunciados no coincidían con el número AS en ninguno de los registros de enrutamiento.

>[AZURE.IMPORTANT] Si el estado de los prefijos públicos anunciados es *validación manual*, debe abrir una incidencia de soporte técnico en el [servicio de soporte técnico de Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) y aportar una evidencia de que posee la dirección IP anunciada, junto con el número del sistema autónomo asociado.


## Pasos siguientes

- Configure su conexión ExpressRoute.

	- [Creación de un circuito ExpressRoute](expressroute-howto-circuit-arm.md)
	- [Configuración del enrutamiento](expressroute-howto-routing-arm.md)
	- [Vinculación de una red virtual a un circuito ExpressRoute](expressroute-howto-linkvnet-arm.md)

<!---HONumber=AcomDC_0211_2016-->