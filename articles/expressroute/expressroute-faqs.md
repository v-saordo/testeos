<properties
   pageTitle="P+F de ExpressRoute"
   description="P+F de ExpressRoute contiene información sobre servicios de Azure compatibles, costes, datos y conexiones, SLA, proveedores y ubicaciones, ancho de banda e información técnica adicional."
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
   ms.date="02/09/2016"
   ms.author="cherylmc"/>

# P+F de ExpressRoute


## ¿Qué es ExpressRoute?
ExpressRoute es un servicio de Azure que permite crear conexiones privadas entre los centros de datos de Microsoft y la infraestructura local o en una instalación de coubicación. Las conexiones ExpressRoute no se realizan sobre una conexión a Internet pública, y ofrecen más confiabilidad, velocidad y seguridad, y una menor latencia que las conexiones a Internet típicas.

### ¿Cuáles son las ventajas de usar ExpressRoute y conexiones de red privada?
Las conexiones ExpressRoute no se realizan sobre una conexión a Internet pública, y ofrecen más confiabilidad, velocidad y seguridad, y latencias coherentes e inferiores en comparación con las conexiones a Internet típicas. En algunos casos, el uso de conexiones ExpressRoute para transferir datos entre los dispositivos locales y Azure también puede aportar beneficios económicos importantes.

### ¿Qué servicios en la nube de Microsoft son compatibles con ExpressRoute?
ExpressRoute admite la mayoría de los servicios de Microsoft Azure hoy en día, incluido Office 365. Busque pronto actualizaciones de disponibilidad general.

### ¿Dónde está disponible el servicio?
Consulte esta página para la ubicación del servicio y la disponibilidad: [Asociados y ubicaciones de ExpressRoute](expressroute-locations.md).

### ¿Cómo puedo usar ExpressRoute para conectarme a Microsoft si no tengo asociaciones con uno de los socios de operadores de ExpressRoute?
Puede seleccionar un operador regional y conexiones Ethernet por tierra a una de las ubicaciones del proveedor de intercambio compatible. A continuación, puede explorar con Microsoft en la ubicación del proveedor. Compruebe la última sección de [Asociados y ubicaciones de ExpressRoute](expressroute-locations.md) para ver si su proveedor de servicio está presente en cualquiera de las ubicaciones de Exchange. A continuación, puede solicitar un circuito ExpressRoute a través del proveedor de servicio para conectarse a Azure.

### ¿Cuánto cuesta ExpressRoute?
Para obtener más información sobre los precios, consulte [Información sobre el precio](https://azure.microsoft.com/pricing/details/expressroute/).

### Si pago por un circuito ExpressRoute de un ancho de banda determinado, ¿la conexión VPN que adquiero de mi proveedor de servicios de red debe tener la misma velocidad?
No. Puede adquirir una conexión VPN de cualquier velocidad de su proveedor de servicios. Sin embargo, la conexión a Azure se limitará al ancho de banda de circuito ExpressRoute que compre.

### Si pago por un circuito ExpressRoute de un ancho de banda determinado, ¿puedo aumentar la velocidad si quiero?
Sí. Los circuitos ExpressRoute están configurados para admitir casos en los que puede aumentar hasta dos veces el límite de ancho de banda adquirido sin coste adicional. Consulte con su proveedor de servicios si son compatibles con esta capacidad.

### ¿Es posible usar la misma conexión de red privada con Red virtual y otros servicios de Azure simultáneamente?
Sí. Un circuito ExpressRoute, una vez que el programa de instalación le permita acceder a los servicios en una red virtual y otros servicios de Azure simultáneamente. Se conectará a redes virtuales a través de la ruta de acceso de emparejamiento privado y otros servicios a través de la ruta de acceso de emparejamiento público.

### ¿ExpressRoute ofrece un contrato de nivel de servicio (SLA)?
Consulte la [página de SLA de ExpressRoute](https://azure.microsoft.com/support/legal/sla/) para obtener más información.

## Servicios admitidos
La mayoría de los servicios de Azure son compatibles con ExpressRoute.

- La conectividad con las máquinas virtuales y servicios en la nube implementados en redes virtuales son compatibles con la ruta de acceso de emparejamiento privado.
- Sitios web de Azure es compatible con la ruta de acceso de emparejamiento público.
- Se admite Office 365 a través de la ruta de acceso de emparejamiento de Microsoft.
- Se puede obtener acceso a todos los demás servicios a través de la ruta de acceso de emparejamiento público. Las excepciones son las siguientes:

	**Los siguientes servicios no son compatibles:**

	- Servicio CDN
	- Pruebas de carga de Visual Studio Team Services
	- Multi-Factor Authentication

## Datos y conexiones

### ¿Hay límites en la cantidad de datos que se puede transferir mediante ExpressRoute?
No se establece un límite sobre la cantidad de transferencia de datos. Consulte [Información de precios](https://azure.microsoft.com/pricing/details/expressroute/) para obtener información sobre las tasas de ancho de banda.

### ¿Qué velocidades de conexión son compatibles con ExpressRoute?
Ofertas de ancho de banda compatibles:

|50 Mbps, 100 Mbps, 200 Mbps, 500 Mbps, 1 Gbps, 2 Gbps, 5 Gbps, 10 Gbps|

### ¿Qué proveedores de servicio están disponibles?
Consulte [Asociados y ubicaciones de ExpressRoute](expressroute-locations.md) para obtener la lista de proveedores de servicios y ubicaciones.

## Detalles técnicos

### ¿Cuáles son los requisitos técnicos para la conexión de mi ubicación local a Azure?
Consulte la [página de requisitos previos de ExpressRoute](expressroute-prerequisites.md) para conocer los requisitos.

### ¿Son las conexiones a ExpressRoute redundantes?
Sí. Cada circuito ExpressRoute tiene un par redundante de conexiones cruzadas configuradas para proporcionar una alta disponibilidad.

### ¿Se pierde conectividad si se produce un error en uno de mis vínculos de ExpressRoute?
No perderá conectividad si se produce un error en una de las conexiones cruzadas. Una conexión redundante está disponible para admitir la carga de la red. Asimismo, puede crear varios circuitos en una ubicación de emparejamiento diferente para lograr resistencia frente a errores.

### ¿Es necesario configurar ambos vínculos para conseguir que el servicio funcione?
Si se conecta a través de un partner que ofrece 3 servicios de capa, el partner se encargará de configurar vínculos redundantes en su nombre. Sin embargo, si ya está colocado en un proveedor de intercambio en la nube, debe configurar dos vínculos LAN a la plataforma de intercambio en la nube. Si se conecta al proveedor de nube a través de un vínculo WAN único desde su centro de datos privado, tendrá que finalizar el vínculo WAN en su propio enrutador y luego configurar dos vínculos LAN a la plataforma de intercambio en la nube.

### ¿Puedo extender una de mis VLAN a Azure mediante ExpressRoute?
No. No admitimos ampliaciones de conectividad de la capa 2 en Azure.

### ¿Se puede disponer de más de un circuito ExpressRoute en mi suscripción?
Sí. Puede disponer de más de un circuito ExpressRoute en su suscripción. El límite predeterminado del número de circuitos dedicados se establece en 10. Puede ponerse en contacto con Soporte técnico de Microsoft para aumentar el límite si es necesario.

### ¿Es posible tener circuitos ExpressRoute de otros proveedores de servicios?
Sí. Puede tener circuitos ExpressRoute de muchos otros proveedores de servicios. Cada circuito ExpressRoute estará asociado solo a un proveedor de servicios.

### ¿Cómo conecto mis redes virtuales a un circuito ExpressRoute?
Los pasos básicos se describen a continuación.

- Se debe establecer un circuito ExpressRoute y que el proveedor de servicios lo habilite.
- Usted o el proveedor deben configurar los emparejamientos BGP.
- Debe vincular la red virtual al circuito ExpressRoute.

Vea [Flujos de trabajo de ExpressRoute para aprovisionamiento de circuitos y estados de circuitos de ExpressRoute](expressroute-workflows.md) para obtener más información.

### ¿Hay límites de conectividad para mi circuito ExpressRoute?
Sí. La página [Asociados y ubicaciones de ExpressRoute](expressroute-locations.md) proporciona una visión general de los límites de conectividad para un circuito ExpressRoute. La conectividad de un circuito ExpressRoute está limitada a una única región geopolítica. La conectividad puede ampliarse a regiones geopolíticas cruzadas habilitando la característica Premium de ExpressRoute.

### ¿Se puede vincular más de una red virtual a un circuito ExpressRoute?
Sí. Es posible vincular hasta 10 redes virtuales a un circuito ExpressRoute.

### Tengo varias suscripciones a Azure que contienen redes virtuales. ¿Es posible conectar redes virtuales de diferentes suscripciones a un solo circuito ExpressRoute?
Sí. Puede autorizar hasta otras 10 suscripciones de Azure para usar un único circuito ExpressRoute. Este límite puede aumentarse al habilitar la característica Premium de ExpressRoute.

Para obtener más información, consulte [Uso compartido de un circuito ExpressRoute a través de varias suscripciones](expressroute-howto-linkvnet-arm.md).

### ¿Las redes virtuales se conectan al mismo circuito aislado entre sí?
No. Todas las redes virtuales vinculadas al mismo circuito ExpressRoute forman parte del mismo dominio de enrutamiento y no están aisladas entre sí desde una perspectiva de enrutamiento. Si necesita aislamiento de rutas, deberá crear un circuito ExpressRoute independiente.

### ¿Se puede conectar una red virtual a más de un circuito ExpressRoute?
Sí. Puede vincular una única red virtual única con hasta 4 circuitos ExpressRoute. Se deben solicitar a través de 4 ubicaciones diferentes.

### ¿Es posible obtener acceso a Internet desde mis redes virtuales conectadas a circuitos ExpressRoute?
Sí. Si no ha anunciado rutas predeterminadas (0.0.0.0/0) o prefijos de rutas de Internet a través de la sesión BGP, podrá conectarse a Internet desde una red virtual vinculada a un circuito ExpressRoute.

### ¿Es posible bloquear la conectividad a Internet a redes virtuales conectadas a circuitos ExpressRoute?
Sí. Puede anunciar rutas predeterminadas (0.0.0.0/0) para bloquear toda la conectividad de Internet a las máquinas virtuales implementadas en una red virtual y enrutar todo el tráfico de salida a través del circuito de ExpressRoute. Tenga en cuenta que si anuncia rutas predeterminadas, forzaremos el tráfico a los servicios ofrecidos a través del emparejamiento público (por ejemplo, Almacenamiento de Azure y Base de datos SQL) de nuevo a sus instalaciones. Tendrá que configurar los enrutadores para devolver el tráfico a Azure a través de la ruta de acceso de emparejamiento público o Internet.

### ¿Las redes virtuales vinculadas al mismo circuito ExpressRoute pueden comunicarse entre sí?
Sí. Las máquinas virtuales implementadas en redes virtuales conectadas al mismo circuito ExpressRoute pueden comunicarse entre sí.

### ¿Se puede usar conectividad de sitio a sitio para redes virtuales junto con ExpressRoute?
Sí. ExpressRoute puede coexistir con las VPN de sitio a sitio.

### ¿Es posible mover una red virtual de una configuración de sitio a sitio/punto a sitio para usar ExpressRoute?
Sí. Tendrá que crear una puerta de enlace de ExpressRoute dentro de la red virtual. Habrá un breve tiempo de inactividad asociado al proceso.

### ¿Qué es necesario para conectarse a Almacenamiento de Azure a través de ExpressRoute?
Debe establecer un circuito ExpressRoute y configurar rutas para el intercambio de tráfico público.

### ¿Hay límites en el número de rutas que puedo anunciar?
Sí. Aceptamos hasta 4000 prefijos de enrutamientos para el intercambio privado y 200 de cada para el intercambio público y de Microsoft. Puede aumentarlo a 10.000 enrutamientos para el intercambio privado si habilita la característica Premium en ExpressRoute.

### ¿Existen restricciones en los intervalos IP que puedo anunciar durante la sesión BGP?
Los prefijos anunciados a través de BGP deben ser/29 o mayor (/ 28 a /8).

Se filtrarán los prefijos privados (RFC1918) en la sesión BGP de interconexión pública.

### ¿Qué ocurre si supero los límites de BGP?
Se quitarán las sesiones BGP. Se restablecerán una vez que el recuento del prefijo esté por debajo del límite.

### ¿Cuál es el tiempo de espera de BGP de ExpressRoute? ¿Se puede ajustar?
El tiempo de espera es 180. Los mensajes de Keep-Alive se envían cada 60 segundos. Hay configuración fija en el lado de Microsoft que no se puede cambiar.

### Después de anunciar la ruta predeterminada (0.0.0.0/0) a mis redes virtuales, no se puede activar Windows en las máquinas virtuales de Azure. ¿Cómo lo soluciono?
Los pasos siguientes ayudarán a Azure a reconocer la solicitud de activación:

1. Establezca el intercambio público para el circuito de ExpressRoute.
2. Realice una búsqueda DNS y busque la dirección IP de **kms.core.windows.net**.
3. A continuación, realice uno de los dos pasos siguientes para que el servicio de administración de claves reconozca que la solicitud de activación procede de Azure y se respete la solicitud.
	- En la red local, enrute el tráfico destinado a la dirección IP (obtenida en el paso 2) a Azure mediante el intercambio de tráfico público.
	- Haga que el proveedor NSP devuelva el tráfico a Azure a través de la interconexión pública.

### ¿Es posible cambiar el ancho de banda de un circuito ExpressRoute?
Sí. Puede aumentar el ancho de banda de un circuito ExpressRoute sin necesidad de eliminarlo. Tendrá que realizar un seguimiento con su proveedor de conectividad para asegurarse de que actualizan los aceleradores en sus redes para admitir el aumento del ancho de banda. Sin embargo no podrá reducir el ancho de banda de un circuito ExpressRoute. Tener que reducir el ancho de banda significa eliminar y volver a crear un circuito ExpressRoute.

### ¿Cómo se cambia el ancho de banda de un circuito ExpressRoute?
Puede actualizar el ancho de banda del circuito ExpressRoute mediante el cmdlet de PowerShell y la API de circuito dedicado de actualización.

## ExpressRoute Premium

### ¿Qué es ExpressRoute Premium?
ExpressRoute Premium es la colección de características que se enumera a continuación.

 - Límite de tabla de enrutamiento aumentado de 4000 rutas a 10.000 rutas para el intercambio público y privado.
 - Mayor número de redes virtuales que puede conectarse al circuito ExpressRoute (el valor predeterminado es 10). Vea la tabla siguiente para obtener más información.
 - Conectividad global a través de la red principal de Microsoft. Ahora podrá vincular una red virtual en una región geopolítica con un circuito ExpressRoute en otra región. **Ejemplo:** puede vincular una red virtual creada en Europa occidental a un circuito ExpressRoute creado en Silicon Valley.
 - Conectividad con los servicios de Office 365 y CRM Online.

### ¿Cuántas redes virtuales puedo vincular a un circuito ExpressRoute si habilito ExpressRoute Premium?
La tabla siguiente proporciona los límites ampliados para el número de redes virtuales que se pueden vincular a un circuito ExpressRoute. El límite predeterminado es de 10.

**Límites para circuitos**

| **Tamaño del circuito** | **Número de vínculos de red virtual para la configuración predeterminada** | **Número de vínculos de red virtual con ExpressRoute Premium** |
|--------------|----------------------------------------|-----------------------------------------------|
| 50 Mbps | 10 | 10 |
| 100 Mbps | 10 | 20 | |
| 200 Mbps | 10 | 25 |
| 500 Mbps | 10 | 40 |
| 1 Gbps | 10 | 50 |
| 2 Gbps | 10 | 60 |
| 5 Gbps | 10 | 75 |
| 10 Gbps | 10 | 100 |



### ¿Cómo habilito ExpressRoute Premium?
Las características de ExpressRoute Premium pueden habilitarse cuando la característica está habilitada y se puede apagar actualizando el estado del circuito. Puede habilitar ExpressRoute Premium en tiempo de creación de circuito o puede llamar al cmdlet de PowerShell o de la API del circuito dedicado de actualización para habilitar ExpressRoute Premium.

### ¿Cómo deshabilito ExpressRoute Premium?
Puede deshabilitar ExpressRoute Premium llamando al cmdlet de Powershell/API del circuito dedicado de actualización. Debe asegurarse de que ha escalado las necesidades de conectividad para que cumplan con los límites predeterminados antes de deshabilitar ExpressRoute Premium. Se producirá un error en la solicitud para deshabilitar ExpressRoute Premium si se realiza la escalación de uso más allá de los límites predeterminados.

### ¿Puedo elegir y seleccionar las características que quiero del conjunto de características Premium?
No. No podrá seleccionar las características que necesita. Habilitaremos todas las características cuando active ExpressRoute Premium.

### ¿Cuánto cuesta ExpressRoute Premium?
Consulte [Información de precios](https://azure.microsoft.com/pricing/details/expressroute/) para ver el coste.

### ¿Debo pagar ExpressRoute Premium además de la tarifa de ExpressRoute Standard?
Sí. Las tarifas de ExpressRoute Premium se aplican a las tarifas de circuito ExpressRoute y a las tarifas que precisa el proveedor de conectividad de mayor nivel.

## ExpressRoute, servicios de Office 365 y CRM Online.

### ¿Cómo se puede crear un circuito ExpressRoute para conectarse a servicios de Office 365 y CRM Online?

1. Revise la [página de requisitos previos de ExpressRoute](expressroute-prerequisites.md) para asegurarse de que se cumplen los requisitos.
2. Revise la lista de proveedores de servicios y las ubicaciones en [Asociados y ubicaciones de ExpressRoute](expressroute-locations.md) para asegurarse de que se cumplen sus necesidades de conectividad.
3. Planee los requisitos de capacidad revisando [Planificación de red y ajuste de rendimiento de Office 365](http://aka.ms/tune/).
4. Siga los pasos indicados en los flujos de trabajo a continuación para configurar la conectividad: [Flujos de trabajo de ExpressRoute para aprovisionamiento de circuitos y estados de circuitos de ExpressRoute](expressroute-workflows.md).

>[AZURE.IMPORTANT] Asegúrese de que habilitó el complemento de ExpressRoute premium al configurar la conectividad con los servicios de Office 365 y CRM Online.

### ¿Pueden mis circuitos ExpressRoute existentes ser compatibles con la conectividad a los servicios de Office 365 y CRM Online?
Sí. El circuito ExpressRoute existente puede configurarse para admitir conectividad con los servicios de Office 365. Asegúrese de que tiene suficiente capacidad para conectarse a servicios de Office 365 y de que habilitó el complemento premium. [Planificación de red y ajuste del rendimiento de Office 365](http://aka.ms/tune/) le ayudará a planificar sus necesidades de conectividad. Además, vea [Creación y modificación de un circuito ExpressRoute](expressroute-howto-circuit-classic.md).

### ¿A qué servicios de Office 365 se puede acceder a través de la conexión ExpressRoute?

Consulte la página de [URL de Office 365 e intervalos de direcciones IP](http://aka.ms/o365endpoints) para obtener una lista actualizada de los servicios compatibles con ExpressRoute.

### ¿Cuánto cuesta ExpressRoute para servicios de Office 365 y CRM Online?
Servicios de Office 365 y CRM Online requiere que el complemento premium esté habilitado. La [página de información de precios](https://azure.microsoft.com/pricing/details/expressroute/) proporciona información sobre los costes de ExpressRoute.

### ¿En qué regiones es compatible ExpressRoute para Office 365?
Consulte [Asociados y ubicaciones de ExpressRoute](expressroute-locations.md) para obtener más información sobre la lista de asociados y ubicaciones en las que se admite ExpressRoute.

### ¿Es posible obtener acceso a Office 365 por Internet incluso si ExpressRoute se ha configurado ExpressRoute para mi organización?
Sí. Es posible obtener acceso a los extremos de servicio de Office 365 a través de Internet a pesar de que se haya configurado ExpressRoute para su red. Si está en una ubicación que está configurada para conectarse a servicios de Office 365 a través de ExpressRoute, se conectará a través de ExpressRoute.

<!---HONumber=AcomDC_0224_2016-->