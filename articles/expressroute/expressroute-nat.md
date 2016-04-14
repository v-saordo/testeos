<properties
   pageTitle="Requisitos NAT para circuitos ExpressRoute | Microsoft Azure"
   description="En esta página se proporcionan requisitos detallados para configurar y administrar NAT para circuitos ExpressRoute."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carolz"
   editor=""/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/16/2016"
   ms.author="cherylmc"/>

# Requisitos de NAT ExpressRoute

Para conectarse a los Servicios en la nube de Microsoft mediante ExpressRoute, necesitará configurar y administrar NAT. Algunos proveedores de conectividad ofrecen la configuración y administración de NAT como un servicio administrado. Consulte a su proveedor de conectividad para saber si ofrece tal servicio. Si no lo hace, debe cumplir los requisitos que se describen a continuación.

Revise la página [Circuitos y dominios de enrutamiento ExpressRoute](expressroute-circuit-peerings.md) para obtener información general de los distintos dominios de enrutamiento. Para satisfacer los requisitos de dirección IP pública para emparejamiento de Microsoft y público de Azure, se recomienda configurar NAT entre la red y Microsoft. Esta sección proporciona una descripción detallada de la infraestructura NAT que necesita para configurar.

## Requisitos de NAT para el emparejamiento público de Azure

La ruta de acceso de emparejamiento público de Azure le permite conectarse a todos los servicios hospedados en Azure a través de sus direcciones IP públicas. Puede tratarse de todos los servicios que se enumeran en [P+F de ExpressRoute](expressroute-faqs.md) y los servicios hospedados por ISV en Microsoft Azure. La conectividad con servicios de Microsoft Azure en el emparejamiento público siempre se inicia desde la red a la red de Microsoft. Al tráfico destinado a Microsoft Azure en el emparejamiento público se le debe aplicar SNAT a direcciones IPv4 públicas válidas antes de que entre en la red de Microsoft. La ilustración siguiente proporciona una imagen de alto nivel de cómo NAT podría configurarse para satisfacer el requisito anterior.

![](./media/expressroute-nat/expressroute-nat-azure-public.png) 

### Anuncios de ruta y grupo de direcciones IP NAT

Debe asegurarse de que el tráfico se introduce en la ruta de acceso de emparejamiento público de Azure con la dirección IPv4 pública válida. Microsoft debe poder validar la propiedad del grupo de direcciones NAT IPv4 en un registro de Internet de enrutamiento regional (RIR) o en un registro de enrutamiento de Internet (IRR). Se realizará una comprobación basándose en el número AS de emparejamiento y las direcciones IP usadas para NAT. Consulte la página [Requisitos de enrutamiento de ExpressRoute](expressroute-routing.md) para obtener información sobre los registros de enrutamiento.
 
No hay restricciones en la longitud del prefijo de la dirección IP NAT anunciado a través de este emparejamiento. Debe supervisar el grupo NAT y asegurarse de que no tiene necesidad de sesiones NAT.

>[AZURE.IMPORTANT]El grupo de direcciones IP NAT anunciado a Microsoft no se debe anunciar a Internet. Esto interrumpirá la conectividad con otros servicios de Microsoft.

## Requisitos NAT para el emparejamiento de Microsoft

El emparejamiento de Microsoft le permite conectarse a Servicios en la nube de Microsoft que no se admiten a través de la ruta de acceso de emparejamiento público de Azure. La lista de servicios incluye servicios de Office 365, como Exchange Online, SharePoint Online, Skype Empresarial y CRM Online. Microsoft espera poder admitir conectividad bidireccional en el emparejamiento de Microsoft. Al tráfico destinado a los Servicios en la nube de Microsoft se le debe aplicar SNAT a direcciones IPv4 públicas válidas antes de que entre en la red de Microsoft. Al tráfico destinado a la red desde los Servicios en la nube de Microsoft se le debe aplicar SNAT antes de que entre en la red. La ilustración siguiente proporciona una imagen de alto nivel de cómo NAT se debe configurar para el emparejamiento de Microsoft.
 
![](./media/expressroute-nat/expressroute-nat-microsoft.png) 


#### Tráfico procedente de la red destinado a Microsoft

- Debe asegurarse de que el tráfico se introduce en la ruta de acceso de emparejamiento de Microsoft con una dirección IPv4 pública válida. Microsoft debe poder validar al propietario del grupo de direcciones NAT IPv4 en el registro de Internet de enrutamiento regional (RIR) o en un registro de enrutamiento de Internet (IRR). Se realizará una comprobación basándose en el número AS de emparejamiento y las direcciones IP usadas para NAT. Consulte la página [Requisitos de enrutamiento de ExpressRoute](expressroute-routing.md) para obtener información sobre los registros de enrutamiento.

- Las direcciones IP usadas para la configuración de emparejamiento público de Azure y otros circuitos ExpressRoute no se deben anunciar a Microsoft a través de la sesión BGP. No hay ninguna restricción en la longitud del prefijo de la dirección IP NAT anunciado a través de este emparejamiento.

	>[AZURE.IMPORTANT]El grupo de direcciones IP NAT anunciado a Microsoft no se debe anunciar a Internet. Esto interrumpirá la conectividad con otros servicios de Microsoft.

#### Tráfico procedente de Microsoft destinado a la red

- Algunos escenarios requieren que Microsoft inicie la conectividad con extremos de servicio hospedados dentro de la red. Un ejemplo típico del escenario sería la conectividad con los servidores ADFS hospedados en la red de Office 365. En tales casos, debe filtrar prefijos adecuados desde la red en el emparejamiento de tráfico de Microsoft. 

- Debe aplicar SNAT al tráfico destinado a direcciones IP dentro de la red desde Microsoft.

## Pasos siguientes

- Consulte los requisitos de [enrutamiento](expressroute-routing.md) y [QoS](expressroute-qos.md).
- Para obtener información sobre los flujos de trabajo, consulte [Flujos de trabajo de aprovisionamiento de circuitos y estados de circuito de ExpressRoute](expressroute-workflows.md).
- Configure su conexión ExpressRoute.

	- [Creación de un circuito ExpressRoute](expressroute-howto-circuit-classic.md)
	- [Configuración del enrutamiento](expressroute-howto-routing-classic.md)
	- [Vinculación de redes virtuales a circuitos ExpressRoute](expressroute-howto-linkvnet-classic.md)

<!---HONumber=AcomDC_0121_2016-->