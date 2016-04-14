<properties
   pageTitle="Guía de planeación y diseño de redes virtuales de Azure | Microsoft Azure"
   description="Obtenga información sobre cómo planear y diseñar redes virtuales en Azure según los requisitos de aislamiento, conectividad y ubicación."
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/08/2016"
   ms.author="telmos" />

# Planeación y diseño de redes virtuales de Azure

Crear una red virtual con la cual experimentar es bastante sencillo, pero es probable que, con el tiempo, implemente varias redes virtuales para satisfacer las necesidades de producción que tiene su organización. Si aplica cierta planeación y diseño, podrá implementar redes virtuales y conectar los recursos que necesita de manera más eficaz. Si no conoce las redes virtuales, le recomendamos que [obtenga información sobre ellas](virtual-networks-overview.md) y aprenda [a implementar](virtual-networks-create-vnet-arm-pportal.md) una antes de continuar.

## Plan

Un conocimiento detallado de las suscripciones, las regiones y los recursos de red de Azure resulta fundamental para obtener éxito. Como punto de partida, puede usar la lista de consideraciones que aparece a continuación. Una vez que compra esas consideraciones, podrá definir los requisitos para el diseño de la red.

### Consideraciones

Antes de responder las preguntas relacionadas con la planeación que aparecen más adelante, considere lo siguiente:

- Todo lo que se crea en Azure consta de uno o más recursos. Una máquina virtual (VM) es un recurso, la interfaz de adaptador de red (NIC) que una máquina virtual usa es un recurso, la dirección IP pública que una NIC usa es un recurso, la red virtual a la que se conecta la NIC también lo es.
- Puede crear recursos dentro de una suscripción y una [región de Azure](https://azure.microsoft.com/regions/#services). Además, los recursos solo se pueden conectar a una red virtual que existe en la misma región y suscripción en que se encuentran. 
- Puede conectar redes virtuales entre sí mediante una [puerta de enlace de VPN](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md) de Azure. También puede conectar de esta manera redes virtuales entre regiones y suscripciones.
- Puede conectar redes virtuales a la red local mediante una de las [opciones de conectividad](../vpn-gateway/vpn-gateway-cross-premises-options.md) disponibles en Azure. 
- Es posible agrupar distintos recursos en los [grupos de recursos](../resource-group-overview.md#resource-groups), lo que permite facilitar la administración del recurso como una unidad. Un grupo de recursos puede contener recursos provenientes de varias regiones, siempre que estos pertenezcan a la misma suscripción.

### Definición de los requisitos

Use las siguientes preguntas como punto de partida para diseñar la red de Azure.

1. ¿Qué ubicaciones de Azure usará para hospedar las redes virtuales?
2. ¿Necesita proporcionar comunicación entre estas ubicaciones de Azure?
3. ¿Necesita proporcionar comunicación entre las redes virtuales de Azure y los centros de datos locales?
4. ¿Cuántas máquinas virtuales de infraestructura como servicio (IaaS), roles de servicios en la nube y aplicaciones web necesita para su solución?
5. ¿Necesita aislar el tráfico según los grupos de máquinas virtuales (es decir, servidores front-end web y servidores de base de datos back-end)?
6. ¿Necesita controlar el flujo de tráfico mediante aplicaciones virtuales?
7. ¿Los usuarios necesitan distintos conjuntos de permisos para los diferentes recursos de Azure?

### Descripción de las propiedades de las redes virtuales y las subredes

Los recursos de las redes virtuales y las subredes ayudan a definir un límite de seguridad para las cargas de trabajo que se ejecutan en Azure. Una red virtual se caracteriza por una colección de espacios de direcciones, también denominados bloques CIDR.

>[AZURE.NOTE] Los administradores de red ya están familiarizados con la notación CIDR. Si no está familiarizado con el formato CIDR, [puede obtener más información](http://whatismyipaddress.com/cidr).

Las Redes virtuales contienen las siguientes propiedades:

|Propiedad|Descripción|Restricciones|
|---|---|---|
|**name**|Nombre de red virtual|Cadena de hasta 80 caracteres. Puede incluir letras, números, caracteres de subrayado, puntos o guiones. Debe empezar por una letra o un número. Debe finalizar en una letra, un número o un carácter de subrayado. Puede incluir letras mayúsculas o minúsculas.|  
|**ubicación**|La ubicación de Azure (también conocida como región).|Debe ser una de las ubicaciones válidas de Azure.|
|**addressSpace**|Es la colección de prefijos de direcciones que conforman la red virtual en la notación CIDR.|Debe ser una matriz de bloques de direcciones CIDR válidos, incluidos intervalos de direcciones IP públicas.|
|**subredes**|Es la colección de subredes que constituyen la red virtual|Consulte la tabla de propiedades de subred que aparece a continuación.||
|**dhcpOptions**|El objeto que contiene una propiedad requerida única llamada **dnsServers**.||
|**dnsServers**|La matriz de servidores DNS que la red virtual usa. Si no se especifica ningún servidor, se usa la resolución interna de nombres de Azure.|Debe ser una matriz de hasta 10 servidores DNS por dirección IP.| 

Una subred es un recurso secundario de una red virtual que le ayudará a definir segmentos de espacios de direcciones dentro de un bloque CIDR mediante prefijos de direcciones IP. Las NIC pueden agregarse a subredes y conectarse a máquinas virtuales, lo cual le proporciona conectividad a varias cargas de trabajo.

Las subredes contienen las siguientes propiedades:

|Propiedad|Descripción|Restricciones|
|---|---|---|
|**name**|Nombre de subred|Cadena de hasta 80 caracteres. Puede incluir letras, números, caracteres de subrayado, puntos o guiones. Debe empezar por una letra o un número. Debe finalizar en una letra, un número o un carácter de subrayado. Puede incluir letras mayúsculas o minúsculas.|
|**ubicación**|La ubicación de Azure (también conocida como región).|Debe ser una de las ubicaciones válidas de Azure.|
|**addressPrefix**|Es el prefijo de dirección única que constituye la subred en la notación de CIDR|Debe ser un bloque CIDR único que forme parte de uno de los espacios de direcciones de la red virtual.|
|**networkSecurityGroup**|Es el grupo de seguridad de red aplicado a la subred|Consulte [Grupos de seguridad de red](resource-groups-networking.md#Network-Security-Group)|
|**routeTable**|Es la tabla de ruta que se aplica a la subred|Consulte [Enrutamiento definido por el usuario](resource-groups-networking.md#Route-table)|
|**ipConfigurations**|Es la colección de objetos de configuración IP que usan las NIC conectadas a la subred.|Consulte la [configuración de IP](../resource-groups-networking.md#IP-configurations).|

### Resolución de nombres

De manera predeterminada, la red virtual usa la [resolución de nombres proporcionada por Azure](virtual-networks-name-resolution-for-vms-and-role-instances.md#Azure-provided-name-resolution) para resolver los nombres dentro de la red virtual y en la red pública de Internet. Sin embargo, si conecta las redes virtuales a los centros de datos locales, necesita proporcionar [su propio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#Name-resolution-using-your-own-DNS-server) para resolver los nombres entre sus redes.

### Límites

Asegúrese de ver todos los [límites relacionados con servicios de redes en Azure](../azure-subscription-service-limits#networking-limits) antes de diseñar la solución. Algunos límites se pueden aumentar con la apertura de una incidencia de soporte técnico.

### Control de acceso basado en rol (RBAC)

Puede usar el [RBAC de Azure](../active-directory/role-based-access-built-in-roles.md) para controlar el nivel de acceso que distintos usuarios pueden tener a los diferentes recursos de Azure. De esta forma, puede separar el trabajo que realiza su equipo según sus necesidades.

En lo que respecta a las redes virtuales, los usuarios que cuentan con el rol **Colaborador de la red** tienen el control total sobre los recursos de la red virtual del Administrador de recursos de Azure. De manera similar, los usuarios que cuentan con el rol **Colaborador de la red clásica** tienen el control total sobre los recursos de la red virtual clásica.

>[AZURE.NOTE] También puede [crear sus propios roles](../active-directory/role-based-access-control-configure.md) para separar sus necesidades de administración.

## Diseño

Una vez que sepa las respuestas a las preguntas de la sección [Planeación](#Plan), revise los siguientes puntos antes de definir las redes virtuales.

### Cantidad de suscripciones y redes virtuales

Debe considerar la posibilidad de crear varias redes virtuales en los siguientes escenarios:

- **Las máquinas virtuales que deben existir en las distintas ubicaciones de Azure**. Las redes virtuales de Azure son regionales. No pueden abarcar ubicaciones. Por lo tanto, se necesita, como mínimo, una red virtual para cada ubicación de Azure en la que desea hospedar las máquinas virtuales.
- **Las cargas de trabajo que deben estar completamente aisladas entre sí**. Puede crear redes virtuales independientes, que incluso usen los mismos espacios de direcciones IP, para aislar las distintas cargas de trabajo entre sí. 
- **Evitar los límites de la plataforma**. Tal como se indica en la sección sobre [límites](#Limits), no se pueden tener más de 2048 máquinas virtuales en una sola red virtual. 

Tenga en cuenta que los límites ya indicados son por región, por suscripción. Esto significa que puede usar varias suscripciones para aumentar el límite de los recursos que puede mantener en Azure. Puede usar una VPN sitio a sitio o un circuito de ExpressRoute para conectar redes virtuales en distintas suscripciones.

### Patrones de diseño de redes virtuales y suscripciones

La siguiente tabla muestra algunos patrones de diseño comunes para usar las suscripciones y las redes virtuales.

|Escenario|Diagrama|Ventajas|Desventajas|
|---|---|---|---|
|Suscripción única, dos redes virtuales por aplicación.|![Suscripción única](./media/virtual-network-vnet-plan-design-arm/figure1.png)|Solo una suscripción que administrar.|Máximo de 25 aplicaciones por región de Azure. Necesita más suscripciones después de ese límite.|
|Una suscripción por aplicación, dos redes virtuales por aplicación.|![Suscripción única](./media/virtual-network-vnet-plan-design-arm/figure2.png)|Usa solo dos redes virtuales por suscripción.|Es más difícil de administrar cuando hay demasiadas aplicaciones.|
|Una suscripción por unidad de negocio, dos redes virtuales por aplicación.|![Suscripción única](./media/virtual-network-vnet-plan-design-arm/figure3.png)|Equilibrio entre la cantidad de suscripciones y las redes virtuales.|Máximo de 25 aplicaciones por unidad de negocio (suscripción).|
|Una suscripción por unidad de negocio, dos redes virtuales por grupo de aplicaciones.|![Suscripción única](./media/virtual-network-vnet-plan-design-arm/figure4.png)|Equilibrio entre la cantidad de suscripciones y las redes virtuales.|Las aplicaciones se deben aislar mediante subredes y NSG.|


### Cantidad de subredes

Debe considerar la posibilidad de tener varias subredes en una red virtual en los siguientes escenarios:

- **No hay direcciones IP privadas suficientes para todas las NIC de una subred**. Si el espacio de direcciones de subred no contiene direcciones IP suficientes para la cantidad de NIC de la subred, deberá crear varias subredes. Tenga en cuenta que Azure reserva 5 direcciones IP privadas desde cada subred que no se pueden usar: la primera y la última dirección del espacio de direcciones (para la dirección de subred y multidifusión) y 3 direcciones que se usarán internamente (para DHCP y DNS). 
- **Seguridad**. Puede usar subredes para separar los grupos de máquinas virtuales entre sí para las cargas de trabajo que tengan una estructura de varios niveles y aplicar distintos [grupos de seguridad de red (NSG)](virtual-networks-nsg.md#subnets) para esa subredes.
- **Conectividad híbrida**. Puede utilizar puertas de enlace de VPN y circuitos de ExpressRoute para [conectar](../vpn-gateway/vpn-gateway-cross-premises-options.md) las redes virtuales entre sí y con sus centros de datos locales. Las puertas de enlace de VPN y los circuitos de ExpressRoute requieren la creación de una subred propia.
- **Aplicaciones virtuales**. Puede utilizar una aplicación virtual, como un firewall, un acelerador de WAN o una puerta de enlace de VPN en una red virtual de Azure. Cuando lo haga, deberá [enrutar el tráfico](virtual-networks-udr-overview.md) a esas aplicaciones y aislarlas en su propia subred.

### Patrones de diseño de subredes y NSG

La siguiente tabla muestra algunos patrones de diseño comunes para usar las subredes.

|Escenario|Diagrama|Ventajas|Desventajas|
|---|---|---|---|
|Una sola subred, NSG por nivel de aplicación, por aplicación.|![Subred única](./media/virtual-network-vnet-plan-design-arm/figure5.png)|Solo una subred que administrar.|Varios NSG necesarios para aislar cada aplicación.|
|Una subred por aplicación, NSG por nivel de aplicación.|![Subred por aplicación](./media/virtual-network-vnet-plan-design-arm/figure6.png)|Menos NSG que administrar.|Varias subredes que administrar.|
|Una sola subred por nivel de aplicación, NSG por aplicación.|![Subred por nivel](./media/virtual-network-vnet-plan-design-arm/figure7.png)|Equilibrio entre la cantidad de subredes y los NSG.|Máximo de 100 NSG. 50 aplicaciones, si cada aplicación requiere 2 NSG distintos.|
|Una subred por nivel de aplicación, por aplicación, NSG por subred.|![Subred por nivel por aplicación](./media/virtual-network-vnet-plan-design-arm/figure8.png)|Posiblemente, una cantidad menor de NSG.|Varias subredes que administrar.|

## Ejemplo de diseño

Para mostrar cómo aplicar la información que aparece en este artículo, considere el escenario siguiente.

Trabaja para una empresa que tiene 2 centros de datos en Norteamérica y dos centros de datos en Europa. Identificó 6 aplicaciones distintas para el cliente que son mantenidas por 2 unidades de negocio diferentes que desea migrar a Azure como plan piloto. La arquitectura básica de las aplicaciones es la siguiente:

- App1, App2, App3 y App4 son aplicaciones web hospedadas en servidores Linux que ejecutan Ubuntu. Cada aplicación se conecta a un servidor de aplicaciones independiente que hospeda servicios RESTful en servidores Linux. Los servicios RESTful se conectan a una base de datos MySQL back-end.
- App5 y App6 son aplicaciones web hospedadas en servidores Windows que ejecutan Windows Server 2012 R2. Cada aplicación se conecta a una base de datos SQL Server back-end.
- Actualmente, todas las aplicaciones está hospedadas en uno de los centros de datos de la empresa en Norteamérica.
- Los centros de datos locales usan el espacio de direcciones 10.0.0.0/8.

Debe diseñar una solución de red virtual que cumple los siguientes requisitos:

- El consumo de recursos de las unidades de negocio no debería afectar a ninguna otra unidad de negocio.
- Deberá minimizar la cantidad de redes virtuales y subredes para facilitar la administración.
- Cada unidad de negocio debe tener una sola red virtual de prueba/desarrollo para usar para todas las aplicaciones.
- Cada aplicación se hospeda en 2 centros de datos de Azure distintos por continente (Norteamérica y Europa).
- Las aplicaciones están completamente aisladas entre sí.
- Los clientes pueden tener acceso a cada aplicación a través de Internet, mediante el protocolo HTTP.
- Los usuarios conectados a los centros de datos locales pueden tener acceso a cada aplicación mediante un túnel cifrado.
- La conexión a los centros de datos locales debe usar dispositivos VPN existentes.
- El grupo de redes de la empresa debe tener el control total de la configuración de la red virtual.
- Los desarrolladores de cada unidad de negocio solo deberían poder implementar máquinas virtuales en las subredes existentes.
- Todas las aplicaciones se migrarán tal como están a Azure ("levantar y mover").
- Las bases de datos de cada ubicación se deberán replicar en otras ubicaciones de Azure una vez al día.
- Cada aplicación deberá usar 5 servidores front-end web, 2 servidores de aplicaciones (cuando corresponda) y 2 servidores de bases de datos.

### Plan

Para comenzar a planear el diseño, deberá responder la pregunta que aparece en la sección [Definición de los requisitos](#Define-requirements), tal como se muestra a continuación.

1. ¿Qué ubicaciones de Azure usará para hospedar las redes virtuales?

	2 ubicaciones en Norteamérica y 2 ubicaciones en Europa. Debe elegirla según la ubicación física de los centros de datos locales existentes. De ese modo, la conexión desde las ubicaciones físicas a Azure tendrá una mejor latencia.

2. ¿Necesita proporcionar comunicación entre estas ubicaciones de Azure?

	Sí. Debido a que las bases de datos se deben replicar a todas las ubicaciones.

3. ¿Necesita proporcionar comunicación entre las redes virtuales de Azure y los centros de datos locales?

	Sí. Debido a que los usuarios conectados con los centros de datos locales deberían poder tener acceso a las aplicaciones a través de un túnel cifrado.
 
4. ¿Cuántas máquinas virtuales IaaS necesita para su solución?

	200 máquinas virtuales IaaS. App1, App2 y App3 requieren, cada una, 5 servidores web, 2 servidores de aplicaciones y 2 servidores de bases de datos. Hablamos de un total de 9 máquinas virtuales IaaS por aplicación, es decir, 36 máquinas virtuales IaaS. App5 y App6 requieren, cada una, 5 servidores web y 2 servidores de bases de datos. Hablamos de un total de 7 máquinas virtuales IaaS por aplicación, es decir, 14 máquinas virtuales IaaS. Por lo tanto, necesita 50 máquinas virtuales IaaS para todas las aplicaciones en cada una de las regiones de Azure. Debido a que necesitamos usar 4 regiones, serán 200 máquinas virtuales IaaS.

	También deberá proporcionar servidores DNS en cada red virtual o en los centros de datos locales para resolver los nombres entre las máquinas virtuales IaaS de Azure y la red local.

5. ¿Necesita aislar el tráfico según los grupos de máquinas virtuales (es decir, servidores front-end web y servidores de base de datos back-end)?

	Sí. Todas las aplicaciones deberán estar completamente aisladas entre sí y cada nivel de aplicación también deberá estar aislado.

6. ¿Necesita controlar el flujo de tráfico mediante aplicaciones virtuales?

	No. Se pueden usar aplicaciones virtuales para brindar más control sobre el flujo de tráfico, incluido el registro del plano de datos.

7. ¿Los usuarios necesitan distintos conjuntos de permisos para los diferentes recursos de Azure?

	Sí. El equipo de red necesita tener el control completo de la configuración de las redes virtuales, mientras que los desarrolladores solo debería poder implementar sus máquinas virtuales en las subredes existentes previamente.

### Diseño

Deberá cumplir con las suscripciones, redes virtuales, subredes y NSG especificados en el diseño. Si bien aquí los analizaremos, deberá obtener más información sobre los [NSG](virtual-networks-nsg.md) antes de finalizar el diseño.

**Cantidad de suscripciones y redes virtuales**

Los siguientes requisitos están relacionados con las suscripciones y las redes virtuales:

- El consumo de recursos de las unidades de negocio no debería afectar a ninguna otra unidad de negocio.
- Deberá minimizar la cantidad de redes virtuales y las subredes.
- Cada unidad de negocio debe tener una sola red virtual de prueba/desarrollo para usar para todas las aplicaciones.
- Cada aplicación se hospeda en 2 centros de datos de Azure distintos por continente (Norteamérica y Europa).

Según esos requisitos, necesitará una suscripción para cada unidad de negocio. De ese modo, el consumo de recursos de una unidad de negocio no se contará de cara a los límites de otras unidades de negocio. Además, dado que desea minimizar la cantidad de redes virtuales, deberá considerar la posibilidad de usar el patrón **una suscripción por unidad de negocio, dos redes virtuales por grupo de aplicaciones**, tal como aparece a continuación.

![Suscripción única](./media/virtual-network-vnet-plan-design-arm/figure9.png)

También deberá especificar el espacio de direcciones para cada red virtual. Debido a que necesita tener conectividad entre los centros de datos locales y las regiones de Azure, el espacio de direcciones que las redes virtuales de Azure usan no puede tener un conflicto con las instalaciones locales y el espacio de direcciones que usa cada red virtual no debería tener conflictos con ninguna otra red virtual existente. Puede usar los espacios de direcciones de la tabla siguiente para cumplir estos requisitos.

|**Suscripción**|**Red virtual**|**Región de Azure**|**Espacio de direcciones**|
|---|---|---|---|
|BU1|ProdBU1US1|Oeste de EE. UU.|172\.16.0.0/16|
|BU1|ProdBU1US2|Este de EE. UU.|172\.17.0.0/16|
|BU1|ProdBU1EU1|Europa del Norte|172\.18.0.0/16|
|BU1|ProdBU1EU2|Europa occidental|172\.19.0.0/16|
|BU1|TestDevBU1|Oeste de EE. UU.|172\.20.0.0/16|
|BU2|TestDevBU2|Oeste de EE. UU.|172\.21.0.0/16|
|BU2|ProdBU2US1|Oeste de EE. UU.|172\.22.0.0/16|
|BU2|ProdBU2US2|Este de EE. UU.|172\.23.0.0/16|
|BU2|ProdBU2EU1|Europa del Norte|172\.24.0.0/16|
|BU2|ProdBU2EU2|Europa occidental|172\.25.0.0/16|

**Cantidad de subredes y NSG**

Los siguientes requisitos están relacionados con las subredes y los NSG:

- Deberá minimizar la cantidad de redes virtuales y las subredes.
- Las aplicaciones están completamente aisladas entre sí.
- Los clientes pueden tener acceso a cada aplicación a través de Internet, mediante el protocolo HTTP.
- Los usuarios conectados a los centros de datos locales pueden tener acceso a cada aplicación mediante un túnel cifrado.
- La conexión a los centros de datos locales debe usar dispositivos VPN existentes.
- Las bases de datos de cada ubicación se deberán replicar en otras ubicaciones de Azure una vez al día.

Según esos requisitos, podría usar una subred por nivel de aplicación y usar NSG para filtrar el tráfico por aplicación. De ese modo, solo tiene 3 subredes en cada red virtual (front-end, nivel de aplicación y nivel de datos) y un NSG por aplicación por subred. En este caso, deberá considerar la posibilidad de usar el patrón de diseño **una subred por nivel de aplicación, NSG por aplicación**. La siguiente ilustración muestra cómo se usa el patrón de diseño que representa la red virtual **ProdBU1US1**.

![Una subred por nivel, un NSG por aplicación por nivel](./media/virtual-network-vnet-plan-design-arm/figure11.png)

Sin embargo, también necesita crear una subred adicional para la conectividad de VPN entre las redes virtuales y los centros de datos locales. También deberá especificar el espacio de direcciones para cada subred. La siguiente ilustración muestra una solución de ejemplo para la red virtual **ProdBU1US1**. Este escenario se podría replicar para cada red virtual. Cada color representa una aplicación distinta.

![Red virtual de ejemplo](./media/virtual-network-vnet-plan-design-arm/figure10.png)

**Control de acceso**

Los siguientes requisitos están relacionados con el control de acceso:

- El grupo de redes de la empresa debe tener el control total de la configuración de la red virtual.
- Los desarrolladores de cada unidad de negocio solo deberían poder implementar máquinas virtuales en las subredes existentes.

Según esos requisitos, podría agregar usuarios desde el equipo de red al rol **Colaborador de la red** integrado en cada suscripción y, además, crear un rol personalizado para los desarrolladores de aplicaciones en cada suscripción y otorgarles derechos para agregar máquinas virtuales a las subredes existentes.

## Pasos siguientes

- [Implementar una red virtual](virtual-networks-create-vnet-arm-template-click.md) en función de un escenario.
- Comprender cómo [equilibrar la carga](../load-balancer/load-balancer-overview.md) de las máquinas virtuales IaaS y [administrar el enrutamiento en varias regiones de Azure](../traffic-manager/traffic-manager-overview.md).
- Obtener más información sobre los [NSG y cómo planear y diseñar](virtual-networks-nsg.md) una solución de NSG.
- Obtener más información sobre las [opciones de conectividad local y redes virtuales](../vpn-gateway/vpn-gateway-cross-premises-options.md).  

<!---HONumber=AcomDC_0218_2016-->