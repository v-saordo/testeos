<properties
   pageTitle="Requisitos de enrutamiento para ExpressRoute | Microsoft Azure"
   description="En eta página se especifican los requisitos detallados para configurar y administrar el enrutamiento en los circuitos de ExpressRoute."
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
   ms.date="03/03/2016"
   ms.author="cherylmc"/>


# Requisitos de enrutamiento de ExpressRoute  

Para conectar a los servicios en la nube de Microsoft mediante ExpressRoute, es preciso configurar y administrar el enrutamiento. Algunos proveedores de conectividad ofrecen la configuración y administración de enrutamiento como un servicio administrado. Consulte a su proveedor de conectividad para saber si ofrece este servicio. Si no lo hace, debe cumplir los requisitos que se describen a continuación.

Para ver una descripción de las sesiones de enrutamiento que es preciso configurar para facilitar la conectividad, consulte el artículo [Circuitos y dominios de enrutamiento](expressroute-circuit-peerings.md).

**Nota:** Microsoft no admite protocolos de redundancia de enrutador (HSRP o VRRP, por nombrar algunos) en configuraciones de alta disponibilidad. Confiamos en un par redundante de sesiones BGP por configuración entre pares para la alta disponibilidad.

## Direcciones IP para las configuraciones entre pares

Necesitará reservar algunos bloques de direcciones IP para configurar el enrutamiento entre la red y los enrutadores de perímetro empresarial de Microsoft (MSEE). En esta sección se proporciona una lista de requisitos y se describen las reglas de adquisición y uso de estas direcciones IP.

### Direcciones IP para la configuración entre pares privados de Azure

Para establecer la configuración entre pares se pueden usar direcciones IP privadas o direcciones IP públicas. El intervalo de direcciones que se utilice para configurar las rutas no debe solaparse con los que se usan para crear redes virtuales en Azure.

 - Debe reservar una subred /29 o dos subredes /30 para las interfaces de enrutamiento.
 - Las subredes usadas para el enrutamiento pueden ser direcciones IP privadas o direcciones IP públicas.
 - Las subredes no deben entrar en conflicto con el intervalo reservado por el cliente para su uso en la nube de Microsoft.
 - Si se usa una subred /29, se dividirá en dos /30 subredes. 
	 - La primera subred /30 se usará para el vínculo principal, mientras que la segunda subred /30 se usará para el vínculo secundario.
	 - Para cada uno de las subredes /30, debe usar la primera dirección IP de la subred /30 en el enrutador. Para configurar sesiones BGP, Microsoft usará la segunda dirección IP de la subred /30.
	 - Para que el [contrato de nivel de servicio de disponibilidad](https://azure.microsoft.com/support/legal/sla/) sea válido, es preciso configurar las dos sesiones BGP.  

#### Ejemplo de configuración entre pares privados

Si elige usar a.b.c.d/29 para establecer la configuración entre pares, se dividirá en dos subredes /30. En el siguiente ejemplo, veremos cómo se usa la subred a.b.c.d/29.

a.b.c.d/29 se divide en a.b.c.d/30 y a.b.c.d+4/30 y se pasa a Microsoft a través de las API de aprovisionamiento. Usará a.b.c.d+1 como IP VRF para el PE principal y Microsoft consumirá a.b.c.d+2 como IP VRF para el MSEE principal. Usará a.b.c.d+5 como IP VRF para el PE secundario y Microsoft usará a.b.c.d+6 como IP VRF para el MSEE secundario.

Considere el caso en que selecciona 192.168.100.128/29 para configurar el emparejamiento privado. 192.168.100.128/29 incluye direcciones desde 192.168.100.128 hasta 192.168.100.135, entre los que:

- 192\.168.100.128/30 se asignará a link1, donde el proveedor usa 192.168.100.129 y Microsoft usa 192.168.100.130.
- 192\.168.100.132/30 se asignará a link2, donde el proveedor usa 192.168.100.133 y Microsoft usa 192.168.100.134.

### Direcciones IP para configuración de pares públicos de Azure y de Microsoft

Para configurar las sesiones BGP, debe usar las direcciones IP públicas que posee. Microsoft debe poder comprobar la propiedad de las direcciones IP a través de los registros regionales de Internet y los registros de enrutamiento de Internet.

- Debe usar una única subred /29 o dos subredes /30 para establecer la configuración entre pares BGP para cada configuración entre pares por circuito ExpressRoute (si tiene más de uno). 
- Si se usa una subred /29, se dividirá en dos /30 subredes. 
	- La primera subred /30 se usará para el vínculo principal, mientras que la segunda subred /30 se usará para el vínculo secundario.
	- Para cada una de las subredes /30, debe usar la primera dirección IP de la subred /30 en el enrutador. Para configurar sesiones BGP, Microsoft usará la segunda dirección IP de la subred /30.
	- Para que el [contrato de nivel de servicio de disponibilidad](https://azure.microsoft.com/support/legal/sla/) sea válido, es preciso configurar las dos sesiones BGP.

Asegúrese de que la dirección IP y el número AS se registran en uno de los registros que se muestran a continuación.

- [ARIN](https://www.arin.net/)
- [APNIC](https://www.apnic.net/)
- [AFRINIC](https://www.afrinic.net/)
- [LACNIC](http://www.lacnic.net/)
- [RIPENCC](https://www.ripe.net/)
- [RADB](http://www.radb.net/)
- [ALTDB](http://altdb.net/)


## Cambio de ruta dinámica

El cambio de enrutamiento se realizará sobre el protocolo eBGP. Se establecen sesiones EBGP entre los MSEE y los enrutadores. La autenticación de sesiones de BGP no es un requisito. Si es necesario, se puede configurar un hash MD5. Consulte las secciones [Configuración del enrutamiento](expressroute-howto-routing-classic.md) y [Flujos de trabajo de aprovisionamiento de circuitos y estados de circuito](expressroute-workflows.md) para obtener información sobre la configuración de las sesiones BGP.

## Números de sistema autónomo

Microsoft usará AS 12076 para la configuración entre pares públicos de Azure, privados de Azure y de Microsoft. AS 65515 se ha reservado para el uso interno. Se admiten números AS de 16 y 32 bits. Puede usar números AS privados para la configuración entre pares privados de Azure. Debe usar públicas como números AS públicos registrados para la configuración entre pares públicos de Azure y de Microsoft.

No hay requisitos con respecto a la simetría de la transferencia de datos. Las rutas de reenvío y de retorno pueden atravesar pares de enrutadores diferentes. Las rutas idénticas deben anunciarse desde cualquiera de los lados en los distintos pares de circuito que le pertenezcan. No se requiere que las métricas de las rutas sean idénticas.

## Límites de agregación y prefijo de ruta

Se admiten hasta 4000 prefijos cuyo anuncio se ha recibido a través de la configuración de pares privados de Azure. Esta cifra puede aumentar hasta los 10.000 prefijos si está habilitado el complemento Premium de ExpressRoute. Se aceptan hasta 200 prefijos por sesión BGP para la configuración de pares públicos de Azure y de Microsoft.

Si el número de prefijos supera el límite, se eliminará la sesión BGP. Aceptaremos las rutas predeterminadas solo en el vínculo de la configuración de pares privados. El proveedor debe eliminar la ruta predeterminada y las direcciones IP privadas (RFC 1918) de las rutas de acceso de la configuración de pares públicos de Azure y de Microsoft.

## Enrutamiento de tránsito y enrutamiento entre regiones

ExpressRoute no puede configurarse como los enrutadores de tránsito. Tendrá que confiar en su proveedor de conectividad para los servicios de enrutamiento de tránsito.

## Anuncio de rutas predeterminadas

Las rutas predeterminadas solo se permiten en sesiones de configuración de pares privados de Azure. En ese caso, se enrutará todo el tráfico de las redes virtuales asociados a su red. El anuncio de rutas predeterminadas en configuración de pares privados dará como resultado el bloqueo de la ruta de acceso de Internet de Azure. Debe basarse en el perímetro de su compañía para enrutar el que proviene y se envía a Internet de los servicios hospedados en Azure.

 Para habilitar la conectividad con otros servicios de Azure y servicios de infraestructura, debe asegurarse de que uno de los siguientes elementos está en su lugar:

 - La configuración de pares públicos de Azure está habilitada para enrutar el tráfico a los extremos públicos.
 - Usa un enrutamiento definido por el usuario para permitir la conectividad a Internet a todas las subredes que requieran dicha conectividad.

**Nota:** el anuncio de rutas predeterminadas interrumpirá la activación de la licencia de Windows y de otras máquinas virtuales. Para solucionar este problema, siga las instrucciones que se indican [aquí](http://blogs.msdn.com/b/mast/archive/2015/05/20/use-azure-custom-routes-to-enable-kms-activation-with-forced-tunneling.aspx).

## Soporte técnico para las comunidades de BGP (próximamente)


Esta sección proporciona información general de cómo se usarán las comunidades de BGP con ExpressRoute. Microsoft anunciará rutas en las rutas de acceso de configuración de pares privados y de Microsoft con rutas etiquetadas con valores de la comunidad adecuada. La razón para hacerlo y los detalles de los valores de la comunidad se describen a continuación. Sin embargo, Microsoft no acepta los valores de comunidad etiquetados en las rutas anunciadas a Microsoft.

Si se conecta a Microsoft a través de ExpressRoute en cualquier ubicación de configuración de pares dentro de una región geopolítica, tendrá acceso a todos los servicios en la nube de Microsoft en todas las regiones que se encuentren dentro de los límites geopolíticos.

Por ejemplo, si se conectó a Microsoft en Ámsterdam a través de ExpressRoute, tendrá acceso a todos los servicios en la nube de Microsoft hospedados en Europa del Norte y Europa occidental.

Consulte la página [Partners de ExpressRoute de Azure y ubicaciones de emparejamiento](expressroute-locations.md) para obtener una lista detallada de las regiones geopolíticas, regiones de Azure asociadas y las ubicaciones de emparejamiento de ExpressRoute correspondientes.

Puede comprar más de un circuito ExpressRoute por región geopolítica. Tener varias conexiones ofrece importantes ventajas para la alta disponibilidad debido a la redundancia geográfica. En los casos en que tenga varios circuitos de ExpressRoute, recibirá el mismo conjunto de prefijos anunciados de Microsoft en las rutas de acceso de la configuración de pares públicos y de la configuración de pares de Microsoft. Esto significa que tendrá varias rutas de acceso desde su red a Microsoft. Potencialmente, esto puede provocar que se tomen decisiones de enrutamiento en la red que no sean óptimas. Como consecuencia, puede sufrir una conectividad con los diferentes servicios que no sea óptima.

Microsoft etiquetará los prefijos anunciados a través de la configuración de pares públicos y de la configuración de pares de Microsoft con los valores de comunidad de BGP adecuados que indican la región en que se hospedan. Puede confiar en los valores de la comunidad para tomar decisiones de enrutamiento adecuadas para ofrecer un enrutamiento óptimo a los clientes.

| **Región geopolítica** | **Región de Microsoft Azure** | **Valor de comunidad de BGP** |
|---|---|---|
| **Norteamérica** | | |
| | Este de EE. UU. | 12076:51004 |
| | Este de EE. UU. 2 | 12076:51005 |
| | Oeste de EE. UU. | 12076:51006 |
| | Centro-Norte de EE. UU | 12076:51007 |
| | Centro-Sur de EE. UU | 12076:51008 |
| | Central EE. UU.: | 12076:51009 |
| | Centro de Canadá | 12076:51020 |
| | Este de Canadá | 12076:51021 |
| **Sudamérica** | | |
| | Sur de Brasil | 12076:51014 |
| **Europa** | | |
| | Europa del Norte | 12076:51003 |
| | Europa occidental | 12076:51002 |
| | Norte del Reino Unido | 12076:51022 |
| | Sur del Reino Unido 2 | 12076:51023 |
| **Asia Pacífico** | | |
| | Asia oriental | 12076:51010 |
| | Sudeste asiático | 12076:51011 |
| **Japón** | | |
| | Este de Japón | 12076:51012 |
| | Oeste de Japón | 12076:51013 |
| **Australia** | | | 
| | Australia Oriental | 12076:51015 |
| | Sudeste de Australia | 12076:51016 |
| **India** | | |
| | Sur de India | 12076:51019 |
| | India occidental | 12076:51018 |
| | India central | 12076:51017 |

Todas las rutas anunciadas de Microsoft se etiquetarán con el valor de la comunidad adecuado.

>[AZURE.IMPORTANT] Los prefijos globales se etiquetarán con un valor adecuado de comunidad y se anunciarán solo cuando esté habilitado el complemento premium de ExpressRoute.


Además, Microsoft también etiquetará los prefijos en función del servicio al que pertenecen. Esto se aplica solo a la configuración de pares de Microsoft. La tabla siguiente proporciona una asignación de servicio al valor de la comunidad de BGP.

| **Servicio** |	**Valor de comunidad de BGP** |
|---|---|
| **Exchange** | 12076:5010 |
| **SharePoint** | 12076:5020 |
| **Skype Empresarial** | 12076:5030 |
| **CRM en línea** | 12076:5040 |
| **Otros servicios de Office 365** | 12076:5100 |


### Manipulación de preferencias de enrutamiento

Microsoft no admite los valores de las comunidades de BGP que defina. Se requiere que configure un par de sesiones BGP por emparejamiento para asegurarse de que se cumplen los requisitos del [contrato de nivel de servicio de disponibilidad ](https://azure.microsoft.com/support/legal/sla/). Sin embargo, puede configurar la red para que prefiera un vínculo a otro mediante el uso de técnicas de manipulación de ruta BGP estándar. Puede aplicar diferentes preferencias locales de BGP a cada vínculo para favorecer un vínculo sobre otro de la red a Microsoft. Puede anteponer la ruta de acceso AS en los anuncios de las rutas para modificar el flujo de tráfico de Microsoft a su red.

## Pasos siguientes

- Configure su conexión ExpressRoute.

	- [Creación y modificación de un circuito ExpressRoute mediante PowerShell](expressroute-howto-circuit-classic.md) o [Creación y modificación de un circuito ExpressRoute mediante Azure Resource Manager y PowerShell](expressroute-howto-circuit-arm.md)
	- [Creación y modificación del enrutamiento de un circuito ExpressRoute mediante PowerShell](expressroute-howto-routing-classic.md) o [Creación y modificación del enrutamiento para un circuito ExpressRoute mediante el Administrador de recursos de Azure y PowerShell](expressroute-howto-routing-arm.md)
	- [Vinculación de la red virtual a circuitos ExpressRoute](expressroute-howto-linkvnet-classic.md) o [Vinculación de redes virtuales a circuitos ExpressRoute](expressroute-howto-linkvnet-arm.md)

<!---HONumber=AcomDC_0309_2016-->