<properties 
   pageTitle="Circuitos ExpressRoute y dominios de enrutamiento | Microsoft Azure"
   description="Esta página proporciona información general sobre los circuitos ExpressRoute y los dominios de enrutamiento."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carolz"
   editor=""/>
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="01/16/2016"
   ms.author="cherylmc"/>

# Circuitos ExpressRoute y dominios de enrutamiento

 Para conectar su infraestructura local a Microsoft a través de un proveedor de conectividad, debe solicitar un *circuito ExpressRoute*. La ilustración siguiente proporciona una representación lógica de conectividad entre la WAN y Microsoft.

![](./media/expressroute-circuit-peerings/expressroute-basic.png)

## Circuitos ExpressRoute

Un *circuito ExpressRoute* representa una conexión lógica entre la infraestructura local y los servicios en la nube de Microsoft a través de un proveedor de conectividad. Puede solicitar varios circuitos ExpressRoute. Cada circuito puede estar en la misma región o en regiones diferentes, y puede estar conectado a su entorno local mediante proveedores de conectividad diferentes.

Los circuitos ExpressRoute no se asignan a entidades físicas. Un circuito se identifica de forma única mediante un GUID estándar denominado clave de servicio (s-key). La clave de servicio es la única parte de la información que se intercambia entre Microsoft, el proveedor de conectividad y usted. Por motivos de seguridad, la s-key no es un secreto. Hay una asignación 1:1 entre un circuito ExpressRoute y la s-key.

Un circuito ExpressRoute puede tener hasta tres emparejamientos: público de Azure, privado de Azure y Microsoft. Cada emparejamiento es un par de sesiones de BGP independientes configuradas cada una de ellas de forma redundante para una alta disponibilidad. Hay una asignación 1 (1 <= N <= 3) entre un circuito ExpressRoute y los dominios de enrutamiento. Un circuito ExpressRoute puede tener uno, dos o los tres emparejamientos habilitados por circuito ExpressRoute.
 
Cada circuito tiene un ancho de banda fijo (50 Mbps, 100 Mbps, 200 Mbps, 500 Mbps, 1 Gbps, 10 Gbps) y se asigna a un proveedor de conectividad y una ubicación de emparejamiento. El ancho de banda que seleccione se compartirá entre todos los emparejamientos del circuito.

### Cuotas, límites y limitaciones

Se aplican límites y cuotas predeterminados para cada circuito ExpressRoute. Para obtener la información más actualizada sobre las cuotas, consulte [Suscripción de Azure y límites, cuotas y restricciones de servicio](../azure-subscription-service-limits.md).

## Dominios de enrutamiento de ExpressRoute

Un circuito ExpressRoute tiene asociados varios dominios de enrutamiento: público de Azure, privado de Azure y Microsoft. Cada uno de los dominios de enrutamiento tiene una configuración idéntica en un par de enrutadores (configuración en activo-activo o de uso compartido de carga) de alta disponibilidad. Los servicios de Azure se clasifican como *público de Azure* y *privado de Azure* para representar los esquemas de direcciones IP.


![](./media/expressroute-circuit-peerings/expressroute-peerings.png)


### Emparejamiento privado

Los servicios de proceso de Azure, concretamente las máquinas virtuales (IaaS) y los servicios en la nube (PaaS), que se implementan en una red virtual, pueden estar conectados mediante el dominio de emparejamiento privado. El dominio de emparejamiento privado se considera una extensión confiable de la red principal en Microsoft Azure. Puede configurar una conectividad bidireccional entre la red principal y las redes virtuales de Azure (VNet). De esta forma podrá conectarse a máquinas virtuales y servicios en la nube directamente en sus direcciones IP privadas.

Puede conectar más de una red virtual al dominio de emparejamiento privado. Revise la [página de P+G](expressroute-faqs.md) para obtener información sobre los límites y las limitaciones. Para obtener información actualizada sobre los límites, visite la página [Suscripción de Azure y límites, cuotas y restricciones de servicio](../azure-subscription-service-limits.md). Consulte la página [Enrutamiento](expressroute-routing.md) para obtener información detallada sobre la configuración de enrutamiento.

### Emparejamiento público

Se ofrecen los servicios como Almacenamiento de Azure, Bases de datos SQL y Sitios web en direcciones IP públicas. Puede conectarse de forma privada a servicios hospedados en direcciones IP públicas (incluida las VIP de servicios en la nube) a través del dominio de enrutamiento de emparejamiento público. Puede conectar el dominio de emparejamiento público a la red perimetral y conectarse a todos los servicios de Azure en sus direcciones IP públicas desde la WAN sin tener que conectarse a través de Internet.

La conectividad siempre se inicia desde la WAN a los servicios de Microsoft Azure. Servicios de Microsoft Azure no podrá iniciar conexiones en la red a través de este dominio de enrutamiento. Una vez que se habilita el emparejamiento público, podrá conectarse a todos los servicios de Azure. No se permite elegir de forma selectiva servicios para los que anunciemos rutas. Puede revisar la lista de prefijos que anunciamos mediante este emparejamiento en la página [Intervalos de direcciones IP de centro de datos de Microsoft Azure](http://www.microsoft.com/download/details.aspx?id=41653). La página se actualiza semanalmente.

Puede definir filtros de ruta personalizados dentro de la red para usar solo las rutas que necesita. Consulte la página [Enrutamiento](expressroute-routing.md) para obtener información detallada sobre la configuración de enrutamiento. Puede definir filtros de ruta personalizados dentro de la red para usar solo las rutas que necesita.

Revise la [página de P+F](expressroute-faqs.md) para obtener más información sobre los servicios compatibles a través del dominio de enrutamiento de emparejamiento público.
 
### Emparejamiento de Microsoft

La conectividad con los demás servicios en línea de Microsoft (por ejemplo, servicios de Office 365) se llevará a cabo a través del emparejamiento de Microsoft. Permitimos la conectividad bidireccional entre la WAN y los servicios en la nube de Microsoft mediante el dominio de enrutamiento de emparejamiento de Microsoft. Solo debe conectarse a los servicios en la nube de Microsoft mediante direcciones IP públicas que pertenezcan a usted o a su proveedor de conectividad, y debe cumplir todas las reglas definidas. Para obtener más información, consulte la página [Requisitos previos de ExpressRoute](expressroute-prerequisites.md).

Para obtener más información sobre los servicios admitidos, los costos y los datos de configuración, consulte la [página de P+F](expressroute-faqs.md). Para obtener información sobre la lista de proveedores de conectividad que ofrecen soporte técnico de emparejamiento de Microsoft, consulte la página [Ubicaciones de ExpressRoute](expressroute-locations.md) .

## Comparación de dominios de enrutamiento

La tabla siguiente comparan los tres dominios de enrutamiento.

|**Emparejamiento privado**|**Emparejamiento público**|**Emparejamiento de Microsoft**|
|---|---|---|---|
|**Número máximo de prefijos admitidos por emparejamiento**|4000 de forma predeterminada, 10.000 con ExpressRoute Premium|200|200|
|**Intervalos de direcciones IP admitidas**|Cualquier dirección IPv4 válida de la WAN.|Direcciones IPv4 públicas propiedad suya o de su proveedor de conectividad.|Direcciones IPv4 públicas propiedad suya o de su proveedor de conectividad.|
|**Requisitos del número de sistema autónomo (AS)**|Números de sistema autónomo (AS) públicos y privados El cliente debe se propietario del número de sistema autónomo (AS) público. | Números de sistema autónomo (AS) públicos y privados El cliente debe se propietario del número de sistema autónomo (AS) público.| Solo números de sistema autónomo (AS) públicos. El número de sistema autónomo (AS) debe validarse con los registros de enrutamiento para validar la propiedad.|
|**Direcciones IP de la interfaz de enrutamiento**|Direcciones IP públicas y de RFC1918|Direcciones IP públicas registradas para los clientes en los registros de enrutamiento.| Direcciones IP públicas registradas para los clientes en los registros de enrutamiento.|
|**Compatibilidad con Hash MD5**| Sí|Sí|Sí|

Puede elegir habilitar uno o varios de los dominios de enrutamiento como parte de su circuito ExpressRoute. Puede elegir colocar todos los dominios de enrutamiento en la misma VPN si quiere combinarlos en un único dominio de enrutamiento. También puede colocarlos en diferentes dominios de enrutamiento, como en el diagrama. La configuración recomendada es que el emparejamiento privado esté conectado directamente a la red principal y que los vínculos de emparejamiento público y de Microsoft estén conectados a la red perimetral.
 
Si decide tener las tres sesiones de emparejamiento, necesita tres pares de sesiones de BGP (un par para cada tipo de emparejamiento). Los pares de sesión de BGP proporcionan un vínculo de alta disponibilidad. Si se va a conectar mediante proveedores de conectividad de capa 2, será responsable de configurar y administrar el enrutamiento. Para obtener más información, revise los [flujos de trabajo](expressroute-workflows.md) para configurar ExpressRoute.

## Pasos siguientes

- Busque un proveedor de servicios. Consulte [Ubicaciones y proveedores de servicios de ExpressRoute](expressroute-locations.md).
- Asegúrese de que se cumplen todos los requisitos previos. Consulte [Requisitos previos de ExpressRoute](expressroute-prerequisites.md).
- Configure su conexión ExpressRoute.
	- [Creación de un circuito ExpressRoute](expressroute-howto-circuit-classic.md)
	- [Configuración del enrutamiento (emparejamientos de circuitos)](expressroute-howto-routing-classic.md)
	- [Vinculación de una red virtual a un circuito ExpressRoute](expressroute-howto-linkvnet-classic.md)

<!---HONumber=AcomDC_0204_2016-->