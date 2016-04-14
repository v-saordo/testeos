<properties
   pageTitle="Introducción a Red virtual de Azure (VNet)"
   description="Más información sobre redes virtuales (VNet) en Azure."
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/11/2015"
   ms.author="telmos" />

# Información general sobre redes virtuales

Una red virtual de Azure (VNet) es una representación de su propia red en la nube. Es un aislamiento lógico de la nube de Azure dedicada a su suscripción. Puede controlar por completo los bloques de direcciones IP, la configuración DNS, las directivas de seguridad y las tablas de rutas dentro de esta red. También puede segmentar aún más la red virtual en subredes e iniciar máquinas virtuales de IaaS de Azure (VM) o [Servicios en la nube (instancias de rol de PaaS)](../cloud-services/cloud-services-choose-me.md). Además, puede conectar la red virtual a su red local mediante uno de las [opciones de conectividad](../vpn-gateway/vpn-gateway-cross-premises-options.md) disponibles en Azure. En esencia, puede ampliar su red en Azure, con control total sobre bloques de direcciones IP con la ventaja de la escala empresarial que ofrece Azure.

Para entender mejor las redes virtuales, eche un vistazo en la figura siguiente, que muestra una red local simplificada.

![Red local](./media/virtual-networks-overview/figure01.png)

La siguiente figura muestra una red local conectada a Internet público a través de un enrutador. También puede ver un firewall entre el enrutador y una red perimetral que aloja un servidor DNS y una granja de servidores web. La granja de servidores web dispone de un equilibrio de carga mediante un equilibrador de carga de hardware que está expuesto a Internet y consume recursos de la subred interna. La subred interna se separa de la red perimetral mediante otro firewall y hospeda servidores de controlador de dominio de Active Directory, servidores de bases de datos y servidores de aplicaciones.

La misma red puede hospedarse en Azure como se muestra en la siguiente figura.

![Red virtual](./media/virtual-networks-overview/figure02.png)

Observe cómo la infraestructura de Azure toma el rol del enrutador, lo que le proporciona acceso desde su red virtual a Internet público sin necesidad de ninguna configuración. Los firewall pueden sustituirse por Grupos de seguridad de red (NSGs) aplicados a cada subred individual. Los equilibradores de carga físicos se sustituyen por equilibradores de carga internos y orientados a Internet en Azure.

>[AZURE.NOTE] Existen dos modos de implementación en Azure: clásica (también conocido como administración de servicios) y Administrador de recursos de Azure (ARM). Las redes virtuales clásicas pueden agregarse a un grupo de afinidad o creare como una red virtual regional. Si tiene una red virtual en un grupo de afinidad, se recomienda [migrarlo a una red virtual regional](virtual-networks-migrate-to-regional-vnet.md).

## Ventajas de la red virtual

- **Aislamiento**. Las redes virtuales están completamente aisladas entre sí. Esto le permite crear redes independientes para el desarrollo, la prueba y la producción que usan los mismos bloques de direcciones de CIDR.

- **Acceso a Internet público**. Todas las máquinas virtuales de IaaS e instancias de roles de PaaS en una red virtual pueden acceder a Internet público de forma predeterminada. Puede controlar el acceso mediante grupos de seguridad de red (NSG).

- **Acceso a máquinas virtuales en una red virtual**. Las instancias de rol de Paas y máquinas virtuales de Iaas se pueden iniciaren la misma red virtual y se pueden conectar entre sí con direcciones IP públicas, incluso si están en distintas subredes, sin necesidad de configurar una puerta de enlace o de usar direcciones IP públicas.

- **Resolución de nombres**. Azure proporciona una resolución de nombres interna para máquinas virtuales de Iaas e instancias de rol de Paas implementadas en la red virtual. También puede implementar sus propios servidores DNS y configurar la red virtual para usarlos.

- **Seguridad**. El tráfico de entrada y salida de las máquinas virtuales y las instancias de rol de PaaS de una red virtual pueden controlarse mediante grupos de seguridad de red.

- **Conectividad**. Las redes virtuales pueden conectarse entre sí, e incluso al centro de datos local usando una conexión VPN de sitio a sitio o una conexión ExpressRoute. Para más información sobre puertas de enlace de VPN, visite [Acerca de puertas de enlace de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). Para más información sobre ExpressRoute, visite [Introducción técnica de ExpressRoute](../expressroute/expressroute-introduction.md).

    >[AZURE.NOTE] Asegúrese de crear una red virtual antes de implementar instancias de rol de Paas o máquinas virtuales de Iaas en el entorno de Azure. Las máquinas virtuales basadas en ARM requieren una red virtual, y si no especifica una red virtual, Azure crea una predeterminada que puede tener un conflicto de bloque de dirección de CIDR con la red local. Esto hace que la conexión de la red virtual con la red local sea imposible.

## Subredes

La subred es un intervalo de direcciones IP en la red virtual; puede dividir una red virtual en varias subredes para la organización y la seguridad. Las instanacias de rol de PaaS y máquinas virtuales implementadas en subredes (iguales o distintas) dentro de una red virtual pueden comunicarse entre sí sin ninguna configuración adicional. También puede configurar tablas de rutas y NSG para una subred.

## Direcciones IP


Hay dos tipos de direcciones IP asignadas a recursos en Azure: *públicas* y *privadas*. Las direcciones IP públicas permiten a los recursos de Azure comunicarse con Internet y otros servicios de acceso público de Azure como [Caché en Redis de Azure](https://azure.microsoft.com/services/cache/), [Centros de eventos de Azure](https://azure.microsoft.com/documentation/services/event-hubs/). Las direcciones IP privadas permite la comunicación entre los recursos de una red virtual, junto con aquellos conectados a través de una VPN, sin usar direcciones IP enrutables por Internet.

Para más información sobre las direcciones IP en Azure, visite [Direcciones IP de red virtual](virtual-network-ip-addresses-overview-arm.md).

## Equilibradores de carga de Azure

Las máquinas virtuales y los servicios en la nube de una red virtual se pueden exponer a Internet a través de equilibradores de carga de Azure. Se puede equilibrar la carga de las aplicaciones de línea de negocio a las que se tienen acceso a través de Internet con el equilibrador de carga interno.

- **Equilibrador de carga externo**. Puede usar el equilibrador de carga externo para proporcionar una alta disponibilidad para instancias de roles de PaaS y máquinas virtuales de IaaS a las que puede obtenerse acceso dese Internet público.

- **Equilibrador de carga interno**. Puede usar un equilibrador de carga interno para proporcionar una alta disponibilidad para instancias de roles de PaaS y máquinas virtuales de IaaS a las que puede obtenerse acceso desde otros servicios en la red virtual.

Para más información sobre el equilibrio de carga en Azure, visite [Introducción de equilibrador de carga](../load-balancer/load-balancer-overview.md).

## Grupo de seguridad de red (NSG)

Puede crear NSG para controlar el acceso de entrada y salida para las interfaces de red (NIC), máquinas virtuales y subredes. Cada NSG contiene una o más reglas que especifican si el tráfico se aprueba o deniega según la dirección IP de origen, el puerto de origen, la dirección IP de destino y el puerto de destino. Para más información sobre los grupos de seguridad de red, visite [Qué es un grupo de seguridad de red](virtual-networks-nsg.md).

## Dispositivos virtuales

Un dispositivo virtual es otra máquina virtual en la red virtual que ejecuta un software basado en la función de dispositivo, como el firewall, la optimización de WAN o la detección de intrusiones. Puede crear una ruta en Azure para enrutar el tráfico de red virtual a través de un dispositivo virtual para usar sus capacidades.

Por ejemplo, pueden usarse NSG para proporcionar seguridad en la red virtual. Sin embargo, los NSG proporcionan una lista de control de acceso de capa 4 (ACL) para paquetes entrantes y salientes. Si desea usar un modelo de seguridad de capa 7, necesita usar un dispositivo de firewall.

Los dispositivos virtuales dependen de las [rutas definidas por el usuario y reenvío IP](virtual-networks-udr-overview.md).

## Límites
Hay límites en el número de redes virtuales permitidas en una suscripción. Consulte [Límites de redes de Azure](../azure-subscription-service-limits.md#networking-limits) para más información.

## Precios
No hay ningún coste adicional para el uso de redes virtuales en Azure. A las instancias de proceso iniciadas dentro de la red virtual se les cobrarán las tarifas estándar como se describe en [Precios de máquinas virtuales de Azure](https://azure.microsoft.com/pricing/details/virtual-machines/). A las [Puertas de enlace de VPN](https://azure.microsoft.com/pricing/details/vpn-gateway/) y a las [Direcciones IP públicas](https://azure.microsoft.com/pricing/details/ip-addresses/) usadas en la red virtual también se les cobrarán tarifas estándar.

## Pasos siguientes

- [Creación de una red virtual](virtual-networks-create-vnet-arm-pportal.md) y subredes
- [Creación de una máquina virtual en una red virtual](../virtual-machines/virtual-machines-windows-tutorial.md).
- Más información sobre [NSG](virtual-networks-nsg.md).
- Más información sobre [equilibradores de carga](../load-balancer/load-balancer-overview.md)
- [Reserva de una dirección IP interna](virtual-networks-reserved-private-ip.md)
- [Reserva de una dirección IP pública](virtual-networks-reserved-public-ip.md).
- Obtención de más información sobre [rutas definidas por el usuario y reenvío IP](virtual-networks-udr-overview.md).

<!---HONumber=AcomDC_0218_2016-->