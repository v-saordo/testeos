<properties 
   pageTitle="Introducción a ExpressRoute | Microsoft Azure"
   description="Esta página proporciona información general del servicio ExpressRoute, incluido cómo funciona una conexión ExpressRoute."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carmonm"
   editor=""/>
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="01/16/2016"
   ms.author="cherylmc"/>

# Información técnica de ExpressRoute

Microsoft Azure ExpressRoute le permite ampliar sus redes de local en la nube de Microsoft a través de una conexión privada y dedicada que facilita un proveedor de conectividad. Con ExpressRoute, se pueden establecer conexiones con servicios en la nube de Microsoft, como Microsoft Azure, Office 365 y CRM Online. La conectividad puede ser desde una red de conectividad universal (IP VPN), una red Ethernet de punto a punto, o una conexión cruzada virtual a través de un proveedor de conectividad en una instalación de ubicación compartida. Las conexiones ExpressRoute no pasan por la red pública de Internet. Esto permite a las conexiones de ExpressRoute ofrecer más confiabilidad, más velocidad, menor latencia y mayor seguridad que las conexiones normales a través de Internet.

![](./media/expressroute-introduction/expressroute-basic.png)

**Entre las ventajas clave se incluyen la siguientes:**

- Conectividad de nivel 3 entre su red local y Microsoft Cloud a través de un proveedor de conectividad. La conectividad puede ser desde una red de conectividad universal (IP VPN), una red Ethernet de punto a punto, o una conexión cruzada virtual a través de un intercambio de Ethernet.
- Conectividad de servicios en la nube de Microsoft en todas las regiones dentro de la región geopolítica.
- Conectividad global a los servicios de Microsoft en todas las regiones con el complemento ExpressRoute Premium.
- Enrutamiento dinámico entre la red y Microsoft a través de protocolos estándar del sector (BGP).
- Redundancia integrada en todas las ubicaciones de configuración entre pares para una mayor confiabilidad.
- El tiempo de actividad de conexión [SLA](https://azure.microsoft.com/support/legal/sla/).
- QoS y compatibilidad para varias clases de servicio para aplicaciones especiales, como Skype Empresarial.

Consulte [P+F de ExpressRoute](expressroute-faqs.md) para obtener más detalles.

## ¿Cómo puedo conectar mi red a Microsoft mediante ExpressRoute?

Puede crear una conexión entre su red local y Microsoft Cloud de tres maneras diferentes

1. **Ubicación compartida en un intercambio en la nube.** Si está colocado en la misma instalación con un intercambio en la nube, puede solicitar conexiones cruzadas virtuales a Microsoft Cloud a través del intercambio de Ethernet del proveedor de la ubicación compartida. Los proveedores de ubicación compartida pueden ofrecer conexiones cruzadas de nivel 2, o conexiones cruzadas de nivel 3 administradas entre la infraestructura en la instalación de ubicación compartida y Microsoft Cloud.
2.	**Conexiones Ethernet de punto a punto.** Puede conectar sus centros de datos locales u oficinas a Microsoft Cloud a través de vínculos de Ethernet de punto a punto. Los proveedores de Ethernet de punto a punto pueden ofrecer conexiones de nivel 2 o de nivel 3 administradas entre el sitio y Microsoft Cloud.
3.	**Redes de conectividad universal (IPVPN).** Puede integrar la WAN con Microsoft Cloud. Los proveedores IPVPN (normalmente VPN MPLS) ofrecen conectividad universal entre los centros de datos y las sucursales. Microsoft Cloud puede estar conectada a la WAN de forma que parezca otra sucursal más. Los proveedores de WAN normalmente ofrecen una conectividad de nivel 3 administrada.

![](./media/expressroute-introduction/expressroute-connectivitymodels.png)

Las características y capacidades de ExpressRoute son todas idénticas en todos los modelos de conectividad anteriores. Los proveedores de conectividad pueden ofrecer uno o varios modelos de conectividad de la lista anterior. Puede trabajar en conjunción con su proveedor de conectividad para elegir el modelo que mejor le convenga.

## Características de ExpressRoute

ExpressRoute admite las siguientes características y capacidades.

### Conectividad de nivel 3

Microsoft usa el protocolo de enrutamiento dinámico estándar del sector (BGP) para intercambiar las rutas entre su red local, las instancias de Azure y las direcciones públicas de Microsoft. Establecemos varias sesiones BGP con su red para perfiles de tráfico diferentes. Puede encontrar más información en el artículo [Circuitos y dominios de enrutamiento de ExpressRoute](expressroute-circuit-peerings.md).

### Redundancia

Cada circuito ExpressRoute consta de dos conexiones a dos enrutadores de perímetro de Microsoft Enterprise (MSEEs) desde proveedor de conectividad o la red perimetral. Microsoft requiere conexión BGP dual desde el proveedor de conectividad o su sitio, uno a cada MSEE. Si lo desea tiene la opción de no implementar dispositivos redundantes o Ethernet de circuitos en su extremo. Sin embargo, los proveedores de conectividad usan dispositivos redundantes para garantizar que las conexiones se entregan a Microsoft de forma redundante. Una configuración de conectividad redundante de nivel 3 es un requisito para nuestro [SLA](https://azure.microsoft.com/support/legal/sla/) sea válido.

### Conectividad con los Servicios en la nube de Microsoft

Las conexiones ExpressRoute habilitan el acceso a los servicios siguientes.

- Servicios de Microsoft Azure
- Servicios de Microsoft Office 365
- Servicios de Microsoft CRM Online 
 
También puede visitar la página [P+F de ExpressRoute](expressroute-faqs.md) para obtener una lista de servicios admitidos a través de ExpressRoute.

### Conectividad a todas las regiones dentro de una región geopolítica

Puede conectarse a Microsoft en una de nuestras [ubicaciones de configuración entre pares](expressroute-locations.md) y tener acceso a todas las regiones dentro de la región geopolíticas.

Por ejemplo, si se conectó a Microsoft en Ámsterdam a través de ExpressRoute, tendrá acceso a todos los servicios en la nube de Microsoft hospedados en Europa septentrional y Europa occidental. Consulte el artículo [Asociados de ExpressRoute de Azure y ubicaciones de emparejamiento](expressroute-locations.md) para obtener información general sobre las regiones geopolíticas, regiones de Microsoft Cloud asociadas y las correspondientes ubicaciones de configuración entre pares de ExpressRoute.

### Conectividad global con el complemento ExpressRoute Premium

Puede habilitar la característica de complemento ExpressRoute Premium para ampliar la conectividad a través de límites geopolíticos. Por ejemplo, si se conectó a Microsoft en Ámsterdam a través de ExpressRoute, tendrá acceso a todos los servicios en la nube de Microsoft hospedados en todas las regiones del mundo (excluidas las nubes nacionales). Puede tener acceso a los servicios implementados en Sudamérica o Australia del mismo modo que accede a las regiones de Europa septentrional y occidental.

### Ecosistema de socios de conectividad enriquecida

ExpressRoute tiene un ecosistema de proveedores de conectividad y asociados integradores de sistema en constante crecimiento. Puede acudir al artículo [Ubicaciones y proveedores de servicios de ExpressRoute](expressroute-locations.md) para obtener la información más reciente.

### Conectividad con nubes nacionales

Microsoft administra entornos de nube aislados para regiones geopolíticas y segmentos de clientes especiales. Acuda a la página [Ubicaciones y proveedores de servicios de ExpressRoute](expressroute-locations.md) para obtener una lista de nubes y proveedores nacionales.

### Opciones de ancho de banda compatibles

Puede comprar los circuitos ExpressRoute para una amplia gama de anchos de banda. La lista de anchos de banda admitidos se enumeran a continuación. Asegúrese de consultar con su proveedor de conectividad para determinar la lista de anchos de banda admitidos que proporcionan.

- 50 Mbps
- 100 Mbps
- 200 Mbps
- 500 Mbps
- 1 Gbps
- 2 Gbps
- 5 Gbps
- 10 Gbps

### Escalado dinámico del ancho de banda

Tiene la posibilidad de aumentar el ancho de banda del circuito ExpressRoute (dentro de lo posible) sin necesidad de eliminar sus conexiones.

### Modelos flexibles de facturación

Puede elegir el modelo de facturación que mejor le convenga. Elija entre los modelos de facturación enumerados a continuación. Consulte la página [P+F de ExpressRoute](expressroute-faqs.md) para obtener información más detallada.

- **Datos ilimitados**. El circuito ExpressRoute se cobra según un precio mensual, y todas las transferencias de datos entrantes y salientes están incluidas de forma gratuita. 
- **Datos medidos**. El circuito ExpressRoute se cobra según un precio mensual. Todas las transferencias de datos de entrada son gratuitas. Las transferencias de datos de salida se cobran de acuerdo a un precio por GB de transferencia de datos. Las tasas de transferencia de datos varían según la región.
- **Complemento ExpressRoute Premium**. ExpressRoute Premium es un complemento que se agrega a un circuito ExpressRoute. El complemento ExpressRoute Premium ofrece las siguientes capacidades: 
	- Límites de ruta ampliados para las configuraciones entre pares públicos y privados de Azure, de 4.000 rutas a 10.000 rutas.
	- Conectividad global para los servicios. Un circuito ExpressRoute creado en cualquier región (excepto las nubes nacionales) tendrá acceso a los recurso de cualquier otra región del mundo. Por ejemplo, un circuito ExpressRoute aprovisionado en Silicon Valley puede acceder a una red virtual creada en Europa occidental .
	- Número de vínculos de red virtual por circuito ExpressRoute ampliado de 10 a un límite superior, que depende del ancho de banda del circuito.

## Pasos siguientes

- Obtenga información acerca de las conexiones ExpressRoute y dominios de enrutamiento. Consulte [Circuitos y dominios de enrutamiento de ExpressRoute](expressroute-circuit-peerings.md).
- Busque un proveedor de servicios. Consulte [Asociados de ExpressRoute de Azure y ubicaciones de emparejamiento](expressroute-locations.md).
- Asegúrese de que se cumplen todos los requisitos previos. Consulte [Requisitos previos de ExpressRoute](expressroute-prerequisites.md).
- Consulte los requisitos de [enrutamiento](expressroute-routing.md), [NAT](expressroute-nat.md) y [QoS](expressroute-qos.md).
- Configure su conexión ExpressRoute.
	- [Creación de un circuito ExpressRoute](expressroute-howto-circuit-classic.md)
	- [Configuración del enrutamiento](expressroute-howto-routing-classic.md)
	- [Vinculación de una red virtual a un circuito ExpressRoute](expressroute-howto-linkvnet-classic.md)

<!---HONumber=AcomDC_0302_2016-->