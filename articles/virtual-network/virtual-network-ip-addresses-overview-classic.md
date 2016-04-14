<properties
   pageTitle="Direcciones IP públicas y privadas (clásicas) en Azure | Microsoft Azure"
   description="Información acerca de direcciones IP públicas y privadas en Azure"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn"
   tags="azure-service-management" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/11/2016"
   ms.author="telmos" />

# Direcciones IP (clásica) en Azure
Puede asignar direcciones IP a los recursos de Azure para que se comuniquen con otros recursos de Azure, la red local e Internet. Hay dos tipos de direcciones IP que puede usar en Azure: públicas y privadas.

Las direcciones IP públicas se usan para la comunicación con Internet, incluidos los servicios de acceso público de Azure.

Las direcciones IP privadas se utilizan para la comunicación dentro de una red virtual (VNet) de Azure, un servicio en la nube y la red local cuando se usa una puerta de enlace de VPN o un circuito ExpressRoute para ampliar la red a Azure.

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager deployment model](virtual-network-ip-addresses-overview-arm.md).

## Direcciones IP públicas
Las direcciones IP públicas permiten que los recursos de Azure se comuniquen con Internet y servicios de acceso público de Azure como [Caché en Redis de Azure](https://azure.microsoft.com/services/cache/), [Centros de eventos de Azure](https://azure.microsoft.com/services/event-hubs/), [Bases de datos SQL](../sql-database/sql-database-technical-overview.md) y [Almacenamiento de Azure](../storage/storage-introduction.md).

Una dirección IP pública se asocia a los siguientes tipos de recursos:

- Servicios en la nube
- Máquinas virtuales (VM) IaaS
- Instancias de rol PaaS
- Puertas de enlace de VPN
- Puertas de enlace de aplicaciones

### Método de asignación
Cuando se necesita asignar una dirección IP pública a un recurso de Azure, se asigna *dinámicamente* desde un grupo de dirección IP pública disponible dentro de la ubicación en la que se crea el recurso. Esta dirección IP se libera cuando se detiene el recurso. En caso de un servicio en la nube, esto sucede cuando se detienen todas las instancias de rol, lo que puede evitarse mediante el uso de una dirección IP *estática* (reservada) (consulte [Servicios en la nube](#Cloud-services)).

>[AZURE.NOTE] La lista de intervalos IP desde la que se asignan direcciones IP públicas a recursos de Azure está publicada en [Intervalos IP del centro de datos de Azure](https://www.microsoft.com/download/details.aspx?id=41653).

### Resolución de nombres de host DNS
Cuando se crea un servicio en la nube o una máquina virtual de IaaS, debe proporcionar un nombre DNS del servicio en la nube que sea único en todos los recursos de Azure. Esto crea una asignación en los servidores DNS administrados de Azure para *dnsname*. cloudapp.net a la dirección IP pública del recurso. Por ejemplo, cuando crea un servicio en la nube con un nombre DNS del servicio en la nube de **Contoso**, el nombre de dominio completo (FQDN) **contoso.cloudapp.net** se resolverá en una dirección IP pública (VIP) del servicio en la nube. Puede usar este FQDN para crear un registro CNAME de dominio personalizado que apunte a la dirección IP pública en Azure.

### Servicios en la nube
Un servicio en la nube siempre tiene una dirección IP pública a la que se conoce como una dirección IP virtual (VIP). Puede crear extremos en un servicio en la nube para asociar puertos diferentes en la dirección VIP a puertos internos en las máquinas virtuales e instancias de rol en el servicio en la nube.

Un servicio en la nube puede contener varias máquinas virtuales IaaS o instancias de rol PaaS, expuestas todas a través de la misma VIP de servicio en la nube. También puede asignar [varias direcciones VIP a un servicio en la nube](../load-balancer/load-balancer-multivip.md), que permite escenarios con varias VIP como un entorno multiempresa con sitios web basados en SSL.

Puede asegurarse de la dirección IP pública de un servicio en la nube sigue siendo el mismo, incluso cuando se detienen todas las instancias de rol, mediante el uso de una dirección IP pública *estática*, denominada [IP reservada](virtual-networks-reserved-public-ip.md). Puede crear un recurso IP estático (reservado) en una ubicación específica y asignarlo a cualquier servicio en la nube en esa ubicación. No puede especificar la dirección IP real para la IP reservada. Se asigna desde el grupo de direcciones IP disponibles en la ubicación que se crea. Esta dirección IP no se libera hasta que la elimine explícitamente.

Las direcciones IP públicas estáticas (reservadas) se usan habitualmente en los escenarios en los que hay un servicio en la nube:

- requiere que las reglas de firewall se configuren por los usuarios finales.
- depende de la resolución de nombres DNS externa, y una dirección IP dinámica requeriría actualizar registros A.
- consume servicios web externos que usan el modelo de seguridad basado en IP.
- usa certificados SSL vinculados a una dirección IP.

>[AZURE.NOTE] Cuando se crea una máquina virtual clásica, Azure crea un *servicio en la nube* de contenedor, que tiene una dirección IP virtual (VIP). Cuando la creación se realiza a través del portal, este configura un *punto de conexión* RDP o SSH predeterminado para que se pueda conectar a la máquina virtual a través de la VIP del servicio en la nube. Se puede reservar esta VIP del servicio en la nube lo que, efectivamente, brinda una dirección IP reservada para conectarse a la máquina virtual. Puede configurar más puntos de conexión para abrir puertos adicionales.

### Instancias de rol PaaS y máquinas virtuales IaaS
Puede asignar una dirección IP pública directamente a una [máquina virtual](../virtual-machines/virtual-machines-about.md) IaaS o una instancia de rol PaaS dentro de un servicio en la nube. Esto se conoce como dirección IP pública a nivel de instancia ([ILPIP](virtual-networks-instance-level-public-ip.md)). Esta dirección IP pública solo puede ser dinámica.

>[AZURE.NOTE] Esto es distinto de la VIP del servicio en la nube, que es un contenedor para las máquinas virtuales IaaS o las instancias de rol PaaS, debido a que un servicio en la nube puede contener varias máquinas virtuales IaaS o instancias de rol PaaS, expuestas todas a través de la misma VIP de servicio en la nube.

### Puertas de enlace de VPN
Se usa una [puerta de enlace de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) para conectar una VNet de Azure a otras VNet de Azure o redes locales. A una puerta de enlace de VPN se le asigna una dirección IP pública *dinámicamente*, que permite la comunicación con la red remota.

### Puertas de enlace de aplicaciones
La [puerta de enlace de aplicaciones](../application-gateway/application-gateway-introduction.md) de Azure puede utilizarse para el equilibrio de carga de Layer7 para distribuir el tráfico de red basado en HTTP. A la puerta de enlace de aplicaciones se le asigna una dirección IP pública *dinámicamente*, que sirve como dirección VIP con equilibrio de carga.

### En un vistazo
En la siguiente tabla, se muestra cada tipo de recurso con los métodos de asignación posibles (dinámico o estático) y la capacidad de asignar varias direcciones IP públicas.

|Recurso|Dinámica|Estática|Varias direcciones IP|
|---|---|---|---|
|Servicio en la nube|Sí|Sí|Sí|
|Instancia del rol PaaS o VM IaaS|Sí|No|No|
|Puerta de enlace de VPN|Sí|No|No|
|Puerta de enlace de aplicaciones|Sí|No|No|

## Direcciones IP privadas
Las direcciones IP privadas permiten que los recursos de Azure se comuniquen con otros recursos en un servicio en la nube o en una [red virtual](virtual-networks-overview.md), o en la red local a través de una puerta de enlace de VPN o un circuito ExpressRoute, sin usar una dirección IP accesible desde Internet.

En el modelo de implementación clásica de Azure, es posible asignar una dirección IP privada a los siguientes recursos de Azure:

- Instancias de rol PaaS y máquinas virtuales IaaS
- Equilibrador de carga interno
- Puerta de enlace de aplicaciones

### Instancias de rol PaaS y máquinas virtuales IaaS
Las máquinas virtuales (VM) creadas con el modelo de implementación clásica siempre se colocan en un servicio en la nube de forma parecida a las instancias de rol PaaS. El comportamiento de las direcciones IP privadas son, por lo tanto, similares para estos recursos.

Es importante tener en cuenta que un servicio en la nube puede implementarse de dos maneras:

- Como un servicio en la nube *independiente*, donde no está dentro de una red virtual.
- Como parte de una red virtual.

#### Método de asignación
En caso de un servicio en la nube *independiente*, los recursos obtienen una dirección IP privada asignada *dinámicamente* desde el intervalo de direcciones IP privada del centro de datos de Azure. Se puede utilizar solo para la comunicación con otras máquinas virtuales en el mismo servicio en la nube. Esta dirección IP puede cambiar cuando se detiene e inicia el recurso.

En el caso de un servicio en la nube implementado en una red virtual, los recursos obtienen direcciones IP privadas asignadas desde el intervalo de direcciones de las subredes asociadas (según lo especificado en su configuración de red). Esta dirección IP privada puede usarse para la comunicación entre todas las máquinas virtuales dentro de la red virtual.

Además, en caso de servicios en la nube dentro de una red virtual, se asigna una dirección IP privada *dinámicamente* (con DHCP) de forma predeterminada. Puede cambiar cuando se detiene e inicia el recurso. Para asegurarse de que la dirección IP sigue siendo la misma, debe establecer el método de asignación en *estático*, y proporcionar una dirección IP válida dentro del intervalo de direcciones correspondiente.

Las direcciones IP privadas estáticas se suelen usar para:

 - Máquinas virtuales que actúan como controladores de dominio o servidores DNS.
 - Máquinas virtuales que requieren reglas de firewall que usan direcciones IP.
 - Máquinas virtuales que ejecutan servicios a los que se accede desde otras aplicaciones a través de una dirección IP.

#### Resolución de nombre de host DNS internos
Todas las máquinas virtuales de Azure e instancias de rol PaaS se configuran con [servidores DNS administrados por Azure](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) de forma predeterminada, a menos que se configuren explícitamente servidores DNS personalizados. Estos servidores DNS proporcionan la resolución de nombres internos para las instancias de rol y máquinas virtuales que residen en la misma red virtual o servicio en la nube.

Cuando se crea una máquina virtual, se agrega a los servidores DNS administrados por Azure una asignación para el nombre de host a su dirección IP privada. En una máquina virtual con varias tarjetas NIC, el nombre de host se asigna a la dirección IP privada de la tarjeta NIC principal. Sin embargo, esta información de asignación está restringida a los recursos dentro del mismo servicio en la nube o red virtual.

En caso de un servicio en la nube *independiente*, podrá resolver nombres de host de todas las instancias de máquinas virtuales y roles solo dentro del mismo servicio en la nube. En el caso de un servicio en la nube dentro de una red virtual, podrá resolver nombres de host de todas las instancias de máquinas virtuales y roles dentro de la red virtual.

### Equilibradores de carga internos (ILB) y puertas de enlace de aplicaciones
Puede asignar una dirección IP privada a la configuración del **front-end** de un [equilibrador de carga interno de Azure](../load-balancer/load-balancer-internal-overview.md) (ILB) o una [puerta de enlace de aplicaciones de Azure](../application-gateway/application-gateway-introduction.md). Esta dirección IP privada actúa como punto de conexión interno, accesible solo a los recursos en su red virtual y a las redes remotas conectadas a la red virtual. Puede asignar una dirección IP privada estática o dinámica a la configuración del front-end. También puede asignar varias direcciones IP privadas para hacer posibles los escenarios con varias VIP.

### En un vistazo
En la siguiente tabla, se muestra cada tipo de recurso con los métodos de asignación posibles (dinámico o estático) y la capacidad de asignar varias direcciones IP privadas.

|Recurso|Dinámica|Estática|Varias direcciones IP|
|---|---|---|---|
|Máquina virtual (en un servicio en la nube *independiente*)|Sí|Sí|Sí|
|Instancia de rol PaaS (en un servicio en la nube *independiente*)|Sí|No|Sí|
|Instancia de rol PaaS o VM (en una red virtual)|Sí|Sí|Sí|
|Front-end de equilibrador de carga interno|Sí|Sí|Sí|
|Front-end de Puerta de enlace de aplicaciones|Sí|Sí|Sí|

## Límites

La tabla siguiente muestra los límites impuestos al direccionamiento IP en Azure por suscripción. Puede [ponerse en contacto con el soporte técnico](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para aumentar los límites predeterminados hasta alcanzar los límites máximos, según las necesidades empresariales.

||Límite predeterminado|Límite máximo|
|---|---|---|
|Direcciones IP públicas (dinámicas)|5|póngase en contacto con el soporte técnico|
|Direcciones IP públicas reservadas|20|póngase en contacto con el soporte técnico|
|VIP pública por implementación (servicio en la nube)|5|póngase en contacto con el soporte técnico|
|VIP privada (ILB) por implementación (servicio en la nube)|1|1|

Asegúrese de leer el conjunto completo de [límites de red](azure-subscription-service-limits.md#networking-limits) de Azure.

## Precios

En la mayoría de los casos, las direcciones IP públicas son gratis. El uso de direcciones IP públicas adicionales o estáticas implica un cargo nominal. Asegúrese de comprender la [estructura de precios de las direcciones IP públicas](https://azure.microsoft.com/pricing/details/ip-addresses/).

## Diferencias entre la implementación del Administración de recursos y la implementación clásica
A continuación, se muestra una comparación de las características de direccionamiento IP en el modelo de implementación del Administrador de recursos y el modelo de implementación clásica.

||Recurso|Clásico|Resource Manager|
|---|---|---|---|
|**Dirección IP pública**|VM|Se denomina ILPIP (sólo dinámica).|Se denomina dirección IP pública (dinámica o estática).|
|||Se asigna a una VM IaaS o una instancia de rol de PaaS.|Se asocia a la NIC de la VM.|
||Equilibrador de carga accesible desde Internet|Se denomina VIP (dinámica) o dirección IP reservada (estática).|Se denomina dirección IP pública (dinámica o estática).|
|||Se asigna a un servicio en la nube.|Se asocia a la configuración de front-end del equilibrador de carga.|
||||
|**Dirección IP privada**|VM|Se denomina DIP.|Se denomina dirección IP privada.|
|||Se asigna a una VM IaaS o una instancia de rol de PaaS.|Se asigna a la NIC de la VM.|
||Equilibrador de carga interno (ILB)|Se asigna al ILB (dinámico o estático).|Se asigna a la configuración de front-end del ILB (dinámico o estático).|

## Pasos siguientes
- [Implemente una máquina virtual con una dirección IP privada estática](virtual-networks-static-private-ip-classic-pportal.md) mediante el portal clásico.

<!---HONumber=AcomDC_0218_2016-->