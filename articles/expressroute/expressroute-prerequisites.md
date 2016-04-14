<properties
   pageTitle="Requisitos previos para la adopción de ExpressRoute | Microsoft Azure"
   description="Esta página proporciona una lista de requisitos que deben cumplirse para poder solicitar un circuito ExpressRoute de Azure."
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


# Requisitos previos de ExpressRoute   

Para conectarse a Servicios en la nube de Microsoft con ExpressRoute, deberá comprobar que se han cumplido los requisitos enumerados en las secciones siguientes.

## Requisitos de cuenta

- Una cuenta de Microsoft Azure válida y activa Esto es necesario para configurar el circuito ExpressRoute. Los circuitos ExpressRoute son recursos dentro de suscripciones de Azure. Una suscripción de Azure es un requisito incluso si la conectividad está limitada a Servicios en la nube de Microsoft que no son de Azure, como los servicios de Office 365 y CRM Online.
- Una suscripción activa de Office 365 (si usa los servicios de Office 365). Vea la sección [Requisitos específicos de Office 365](#office-365-specific-requirements) de este artículo para obtener más información.

## Relación del proveedor de conectividad

- Una relación con un proveedor de conectividad de la lista de compatibilidad a través de la que debe facilitarse la conectividad. Debe tener una relación de negocios existente con el proveedor de conectividad. Deberá asegurarse de que el servicio que tiene con el proveedor de conectividad es compatible con ExpressRoute.
- Si desea utilizar un proveedor de conectividad que no está en la lista de admitidos, puede crear una conexión con Servicios en la nube de Microsoft a través de un intercambio.
	- Consulte con su proveedor de conectividad para ver si están presentes en cualquiera de las ubicaciones de intercambio que aparecen en la lista admitida.
	- Haga que el proveedor de conectividad amplíe su red a la ubicación de intercambio que elija.
	- Solicite un circuito ExpressRoute con el intercambio como proveedor de conectividad.

## Conectividad física entre la red y el proveedor de conectividad

Consulte la sección de modelos de conectividad para obtener detalles sobre los modelos de conectividad. Los clientes deben asegurarse de que su infraestructura local está conectada físicamente a la infraestructura del proveedor de servicio a través de uno de los modelos descritos.

## Requisitos de redundancia para la conectividad

No hay ningún requisito de redundancia sobre la conectividad física entre la infraestructura de cliente y la infraestructura del proveedor de servicio. Microsoft requiere redundancia en el nivel 3. Microsoft requiere la configuración del enrutamiento redundante entre el borde de Microsoft y la red del cliente a través del proveedor de servicio para cada uno de los emparejamientos que se van a habilitar. Si las sesiones de enrutamiento no se configuran de forma redundante, el contrato de nivel de servicio de disponibilidad de servicio se anulará.

## Consideraciones sobre las direcciones IP y el enrutamiento

Los clientes y los proveedores de conectividad son responsables de configurar sesiones BGP redundantes con la infraestructura de Microsoft Edge. Los clientes que optan por conectarse a través de proveedores VPN IP normalmente confiarán en los proveedores de conectividad para administrar configuraciones de enrutamiento. Los clientes coubicados con un intercambio o que se conecten a Microsoft a través de un proveedor de Ethernet punto a punto tendrán que configurar sesiones BGP redundantes por emparejamiento para cumplir los requisitos del contrato de nivel de servicio de disponibilidad. Los proveedores de conectividad pueden ofrecer esto como un servicio de valor de añadido. Consulte la tabla de dominios de enrutamiento en el artículo [Dominios de enrutamiento y circuitos ExpressRoute](expressroute-circuit-peerings.md) para obtener más información sobre los límites.

## Seguridad y firewalls

Consulte el documento [Servicios en la nube de Microsoft y seguridad de red](../best-practices-network-security.md) obtener para información sobre la seguridad y el uso de firewalls.

## Configuración NAT para emparejamientos de Microsoft y públicos de Azure

Consulte [Requisitos NAT de ExpressRoute](expressroute-nat.md) para obtener instrucciones detalladas sobre los requisitos y las configuraciones. Consulte con su proveedor de conectividad para ver si va a administrar la configuración NAT y la realizar la administración para usted. Normalmente, los proveedores de conectividad de nivel 3 administrarán NAT para usted.

## Requisitos específicos de Office 365

Revise los siguientes recursos para obtener más información acerca de los requisitos de Office 365.

- [Planificación de la red y ajuste del rendimiento para Office 365](http://aka.ms/tune)
- [Administración del tráfico de red de Office 365](https://support.office.com/article/Office-365-network-traffic-management-e1da26c6-2d39-4379-af6f-4da213218408)
- Consulte el artículo [Requisitos de calidad de servicio (QoS) de ExpressRoute ](expressroute-qos.md) para obtener instrucciones detalladas sobre los requisitos y configuraciones de QoS. Consulte a su proveedor de conectividad para saber si ofrece varias clases de servicio para su VPN. 

## Pasos siguientes

- Para obtener más información acerca de ExpressRoute, consulte [P+F de ExpressRoute](expressroute-faqs.md).
- Busque un proveedor de servicios. Vea [Asociados de ExpressRoute y ubicaciones de emparejamiento](expressroute-locations.md).
- Consulte los requisitos de [enrutamiento](expressroute-routing.md), [NAT](expressroute-nat.md) y [QoS](expressroute-qos.md).
- Configure su conexión ExpressRoute.
	- [Creación de un circuito ExpressRoute](expressroute-howto-circuit-classic.md)
	- [Configuración del enrutamiento](expressroute-howto-routing-classic.md)
	- [Vinculación de redes virtuales a circuitos ExpressRoute](expressroute-howto-linkvnet-classic.md)

<!---HONumber=AcomDC_0309_2016-->