<properties 
   pageTitle="Preguntas frecuentes sobre la puerta de enlace VPN de Virtual Network | Microsoft Azure"
   description="Preguntas más frecuentes sobre la puerta de enlace de VPN Preguntas más frecuentes sobre las conexiones entre locales de Virtual Network de Microsoft Azure, las conexiones híbridas de configuración y puertas de enlace VPN"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carolz"
   editor="" />
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/16/2015"
   ms.author="cherylmc" />

# Preguntas más frecuentes sobre la puerta de enlace de VPN

## Conexión a redes virtuales

### ¿Puedo conectar redes virtuales en diferentes regiones de Azure?
Sí. De hecho, no hay ninguna restricción de región. Puede conectar una red virtual a otra red virtual en la misma región o en una región distinta de Azure.

### ¿Puedo conectar redes virtuales en diferentes suscripciones?
Sí.

### ¿Puedo conectar a varios sitios desde una única red virtual?

Puede conectarse a varios sitios mediante el uso de Windows PowerShell y las API de REST de Azure. Consulte la sección de P+F [Conectividad de red virtual a red virtual y de multisitio](#multi-site-and-vnet-to-vnet-connectivity).
## ¿Cuáles son mis opciones de conexión entre locales?

Se admiten las siguientes conexiones entre locales:

- [Sitio a sitio](vpn-gateway-site-to-site-create.md): conexión VPN sobre IPsec (IKE v1 e IKE v2). Este tipo de conexión requiere un dispositivo VPN o RRAS.

- [Punto a sitio](vpn-gateway-point-to-site-create.md): conexión VPN sobre SSTP (Protocolo de túnel de sockets seguros). Esta conexión no requiere un dispositivo VPN.

- [Red virtual a red virtual](virtual-networks-configure-vnet-to-vnet-connection.md): este tipo de conexión es el mismo que el de la configuración de sitio a sitio. La conexión de red virtual a red virtual es una conexión VPN sobre IPsec (IKE v1 e IKE v2). No requiere un dispositivo VPN.

- [Multisitio](vpn-gateway-multi-site.md): esta es una variación de una configuración de sitio a sitio que le permite conectar varios sitios locales a una red virtual.

- [ExpressRoute](../expressroute/expressroute-introduction.md): ExpressRoute es una conexión directa a Azure desde la WAN, no a través de la red Internet pública. Si desea más información consulte la [ Información técnica de ExpressRoute ](../expressroute/expressroute-introduction.md) y [P+F de ExpressRoute](../expressroute/expressroute-faqs.md).

Para más información sobre las conexiones entre locales, consulte [Acerca de la conectividad segura entre locales](vpn-gateway-cross-premises-options.md).

### ¿Cuál es la diferencia entre una conexión de sitio a sitio y una de punto a sitio?

Las conexiones de **sitio a sitio** le permiten conectar cualquiera de los equipos ubicados en sus instalaciones locales con cualquier máquina virtual o instancia de rol dentro de la red virtual, dependiendo de cómo elija configurar el enrutamiento. Es ideal para una conexión entre locales que esté siempre disponible y una opción que se adapta bien a las configuraciones híbridas. Este tipo de conexión se basa en un dispositivo (de hardware o de software) VPN sobre IPsec, que se tiene que implementar en el perímetro de la red. Para crear este tipo de conexión, tendrá que tener el hardware de VPN necesario y una dirección IPv4 con orientación externa.

Las conexiones de **punto a sitio** le permiten conectarse desde un único equipo y desde cualquier lugar con cualquier dispositivo ubicado en la red virtual. Usa el cliente VPN incluido en Windows. Como parte de la configuración de punto a sitio, instale un certificado y un paquete de configuración de cliente VPN, que contiene la configuración que permite al equipo conectarse a cualquier máquina virtual o instancia de rol dentro de la red virtual. Es ideal si desea conectarse a una red virtual pero no se encuentra en una ubicación local. También es una buena opción cuando no tenga acceso a un hardware VPN o una dirección IPv4 con orientación externa, ya que ambos son necesarios para una conexión de sitio a sitio.

Nota: puede configurar la red virtual para utilizar las conexiones de sitio a sitio y de punto a sitio simultáneamente, siempre que cree la conexión de sitio a sitio mediante una puerta de enlace de enrutamiento dinámico.

Para más información, consulte [Acerca de la conectividad segura entre locales de redes virtuales](vpn-gateway-cross-premises-options.md).

### ¿Qué es ExpressRoute?

ExpressRoute le permite crear conexiones privadas entre los centros de datos de Microsoft y la infraestructura local o en un entorno de coubicación. Con ExpressRoute, se pueden establecer conexiones con servicios en la nube de Microsoft, como Microsoft Azure y Office 365 en una instalación de coubicación de asociado de ExpressRoute o se pueden conectar directamente desde la red WAN existente (como una VPN MPLS de un proveedor de servicios de red).

Las conexiones ExpressRoute ofrecen mejor confiabilidad y seguridad, mayor ancho de banda y una menor latencia que las conexiones a Internet típicas. En algunos casos, el uso de conexiones ExpressRoute para transferir datos entre la red local y Azure también puede aportar beneficios económicos importantes. Si ya ha creado una conexión entre instalaciones desde la red local, puede migrar a una conexión ExpressRoute manteniendo intacta la red virtual.

Consulte [P+F de ExpressRoute](../expressroute/expressroute-faqs.md) para obtener más detalles.

## Conexiones de sitio a sitio y dispositivos VPN

### ¿Qué tengo que tener en cuenta al seleccionar un dispositivo VPN?

Hemos validado un conjunto de dispositivos estándar VPN de sitio a sitio en colaboración con proveedores de dispositivos. [Aquí](vpn-gateway-about-vpn-devices.md) puede encontrar una lista de dispositivos VPN de compatibilidad conocida y sus correspondientes instrucciones de configuración o ejemplos, así como especificaciones del dispositivo. Todos los dispositivos dentro de las familias de dispositivos que aparecen como de compatibilidad conocida, deben funcionar con Red virtual. Con el fin de configurar el dispositivo VPN, consulte el ejemplo de configuración de dispositivo o el vínculo que corresponde a la familia de dispositivos adecuada.

### ¿Qué hago si tengo un dispositivo VPN que no está en la lista de dispositivos de compatibilidad conocida?

Si el dispositivo no aparece como un dispositivo VPN de compatibilidad conocida y desea utilizarlo para la conexión VPN, tendrá que comprobar que tiene las opciones de configuración admitidas de IPsec/IKE y los parámetros que aparecen [aquí](vpn-gateway-about-vpn-devices.md#devices-not-on-the-compatible-list). Los dispositivos que cumplen los requisitos mínimos deberían funcionar correctamente con las puertas de enlace de VPN. Póngase en contacto con el fabricante del dispositivo para obtener instrucciones adicionales de soporte técnico y configuración.

### ¿Por qué mi túnel de VPN basado en directivas deja de funcionar cuando el tráfico está inactivo?

Este es el comportamiento esperado para puertas de enlace de VPN basadas en directivas (también conocido como enrutamiento estático). Cuando el tráfico a través del túnel está inactivo durante más de 5 minutos, este túnel se cancelará. Pero en cuanto el tráfico comience a fluir en cualquier dirección, el túnel se restablecerá inmediatamente. Si tiene una puerta de enlace VPN basada en enrutamiento (también conocida como dinámica), no experimentará este comportamiento.

### ¿Puedo usar una VPN de software para conectarme a Azure?

Para la configuración de sitio a sitio entre locales se admiten los servidores de Windows Server 2012 con servicio de enrutamiento y acceso remoto (RRAS).

Otras soluciones VPN de software deben funcionar con nuestra puerta de enlace siempre que se ajusten a las implementaciones IPsec estándar de la industria. Póngase en contacto con el proveedor del software para obtener instrucciones de configuración y soporte técnico.

## Conexiones de punto a sitio

### ¿Qué sistemas operativos puedo usar para las conexiones de punto a sitio?

Se admiten los siguientes sistemas operativos:

- Windows 7 (solo versión de 64 bits)

- Windows Server 2008 R2

- Windows 8 (solo versión de 64 bits)

- Windows Server 2012

- Windows 10

### ¿Puedo usar cualquier cliente de software VPN de punto a sitio que admita SSTP?

No. La compatibilidad se limita solo a las versiones de sistema operativo de Windows enumeradas anteriormente.

### ¿Cuántos extremos de cliente VPN puedo tener en mi configuración punto a sitio?

Se admiten hasta 128 clientes VPN para poder conectarse al mismo tiempo a una red virtual.

### ¿Puedo usar mi propio CA raíz de PKI interna para la conectividad de punto a sitio?

Sí. Anteriormente, solo podían usarse certificados raíz autofirmados. Todavía puede cargar 20 certificados raíz.

### ¿Puedo atravesar servidores proxy y firewalls con la capacidad de punto a sitio?

Sí. Usamos SSTP (Protocolo de túnel de sockets de seguros) para el túnel a través de firewalls. Este túnel aparecerá como una conexión HTTPs.

### ¿Si reinicio un equipo cliente con configuración punto a sitio, se volverá a conectar la VPN de forma automática?

De forma predeterminada, el equipo cliente no volverá a establecer la conexión VPN automáticamente.

### ¿Admite la configuración punto a sitio la reconexión automática y el DDNS en los clientes VPN?

Las VPN de punto a sitio no admiten la reconexión automática y el DDNS.

### ¿Puedo tener configuraciones de sitio a sitio y de punto a sitio coexistiendo en la misma red virtual?

Sí. Ambas soluciones funcionarán si dispone de una puerta de enlace VPN con enrutamiento dinámico para la red virtual. No se admite la configuración punto a sitio en puertas de enlace de VPN de enrutamiento estático.

### ¿Puedo configurar un cliente de punto a sitio para conectarse a varias redes virtuales al mismo tiempo?

Sí, es posible. Pero las redes virtuales no pueden tener prefijos IP superpuestos y los espacios de dirección de punto a sitio no pueden superponerse en las redes virtuales.

### ¿Qué rendimiento puedo esperar en las conexiones de sitio a sitio o punto a sitio?

Es difícil de mantener el rendimiento exacto de los túneles VPN. IPsec y SSTP son protocolos VPN con mucho cifrado. El rendimiento también está limitado por la latencia y el ancho de banda entre sus instalaciones locales e Internet.

## Puertas de enlace

### ¿Qué es una puerta de enlace basada en directivas (de enrutamiento estático)?

Las puertas de enlace de enrutamiento estático implementan VPN basadas en directivas. Las VPN basadas en directivas cifran y dirigen los paquetes a través de túneles de IPsec basados en las combinaciones de prefijos de dirección entre su red local y la red virtual de Azure. Normalmente, la directiva (o selector de tráfico) se define como una lista de acceso en la configuración de la VPN.

### ¿Qué es una puerta de enlace basada en enrutamiento (de enrutamiento dinámico)?

Las puertas de enlace de enrutamiento dinámico implementan VPN basadas en enrutamiento. Las VPN basadas en enrutamiento utilizan "rutas" en la dirección IP de reenvío o en la tabla de enrutamiento para dirigir los paquetes a sus correspondientes interfaces de túnel. A continuación, las interfaces de túnel cifran o descifran los paquetes dentro y fuera de los túneles. La directiva o selector de tráfico para las VPN basadas en enrutamiento se configura como conectividad de tipo any-to-any (o caracteres comodín).

### ¿Puedo obtener mi dirección IP de puerta de enlace VPN antes de crearla?

No. Tiene que crear primero la puerta de enlace para obtener la dirección IP. Si elimina y vuelve a crear la puerta de enlace VPN, la dirección IP cambiará.

### ¿Cómo se autentica mi túnel VPN?

VPN de Azure usa autenticación PSK (clave previamente compartida). Se genera una clave previamente compartida (PSK) cuando se crea el túnel VPN. Puede cambiar la clave PSK generada automáticamente con la suya propia a través del cmdlet de PowerShell o la API de REST para establecer la clave precompartida.

### ¿Puedo usar la API para establecer la clave precompartida y configurar mi puerta de enlace de enrutamiento estático VPN?

Sí, el cmdlet de PowerShell y la API Establecer clave precompartida se pueden utilizar para configurar tanto la VPN de enrutamiento estático de Azure como las VPN de enrutamiento dinámico.

### ¿Puedo usar otras opciones de autenticación?

Las opciones están limitadas al uso de claves precompartidas (PSK) para la autenticación.

### ¿Qué es la "subred de puerta de enlace" y por qué es necesaria?

Existe un servicio de puerta de enlace que se ejecuta para habilitar la conectividad entre locales. Se necesitan 2 direcciones IP de su dominio de enrutamiento para poder habilitar el enrutamiento entre sus instalaciones y la nube. Tiene que especificar al menos/29 de subred para que sea posible elegir direcciones IP para configurar las rutas. Aunque puede crear una subred /29, comprenda que algunas características requieren un tamaño de puerta de enlace específico. Siga la lista de requisitos de subred de puerta de enlace para la característica que desea configurar.

Tenga en cuenta que no debe implementar máquinas virtuales o instancias de rol en la subred de puerta de enlace.

### ¿Cómo especifico qué tráfico pasa a través de la puerta de enlace VPN?

Si está usando el Portal de Azure clásico, agregue cada uno de los intervalos que quiera enviar a través de la puerta de enlace de la red virtual a la página Redes en Redes locales.

### ¿Puedo configurar una tunelización forzada?

Sí. Consulte [Configurar una tunelización forzada](vpn-gateway-about-forced-tunneling.md).

### ¿Puedo configurar mi propio servidor VPN en Azure y usarlo para conectar a mi propia red local?

Sí, puede implementar sus propias puertas de enlace o servidores VPN en Azure bien desde Azure Marketplace o creando sus propios enrutadores VPN. Tendrá que configurar las rutas definidas por el usuario en la red virtual para asegurarse de que el tráfico se enruta correctamente entre las redes locales y las subredes de la red virtual.

### ¿Por qué hay algunos puertos abiertos en mi puerta de enlace de VPN?

Son necesarios para la comunicación de la infraestructura de Azure. Están protegidos (bloqueados) mediante certificados de Azure. Sin los certificados apropiados, las entidades externas, incluidos los clientes de esas puertas de enlace, no podrán tener ningún efecto en esos puntos de conexión.

Una puerta de enlace de VPN es básicamente un dispositivo de hosts múltiples con una NIC que accede a la red privada del cliente y una NIC accesible desde la red pública. Las entidades de la infraestructura de Azure no pueden acceder a redes privadas de clientes por motivos de conformidad, por lo que necesitan usar puntos de conexión públicos para la comunicación de infraestructura. Los puntos de conexión públicos se analizan periódicamente mediante auditoría de seguridad de Azure.


### Más información acerca de los tipos de puerta de enlace, los requisitos y el rendimiento

Para más información, consulte [Acerca de las puertas de enlace de VPN](vpn-gateway-about-vpngateways.md).

## Conectividad multisitio y de red virtual a red virtual

### ¿Qué tipo de puertas de enlace admiten la conectividad de red virtual a red virtual y de multisitio?

Solo las VPN de enrutamiento dinámico.

### ¿Puedo conectar una red virtual con VPN de enrutamiento dinámico a otra red virtual con VPN de enrutamiento estático?

No, ambas redes virtuales TIENEN QUE usar VPN de enrutamiento dinámico.

### ¿Es seguro el tráfico de red virtual a red virtual?

Sí, se protege mediante cifrado IPsec/IKE.

### ¿El tráfico de red virtual a red virtual viaja a través de la red troncal de Azure?

Sí.

### ¿A cuántos sitios locales y redes virtuales se puede conectar una red virtual?

Máx. 10 combinados para las puertas de enrutamiento dinámico de nivel Básico y Estándar y 30 para las puertas de enlace VPN de alto rendimiento.

### ¿Puedo usar VPN de punto a sitio con mi red virtual con varios túneles VPN?

Sí, las VPN de punto a sitio (P2S) se pueden usar con las puertas de enlace VPN para conectarse a varios sitios locales y a otras redes virtuales.

### ¿Puedo configurar varios túneles entre mi red virtual y mi sitio local utilizando VPN multisitio?

No, no se admite túneles redundantes entre una red virtual Azure y un sitio local.

### ¿Puede haber espacios de direcciones superpuestos entre las redes virtuales conectadas y los sitios locales?

No. Los espacios de direcciones superpuestos harían que fallara la carga del archivo netcfg o la creación de una red virtual.

### ¿Tengo más ancho de banda con más VPN de sitio a sitio que si tengo una única red virtual?

No, todos los túneles VPN, incluyendo los de las VPN de punto a punto, comparten la misma puerta de enlace VPN de Azure y el ancho de banda disponible.

### ¿Puedo usar la puerta de enlace VPN de Azure para el tráfico en tránsito entre mis sitios locales o a otra red virtual?

El tráfico en tránsito a través de puerta de enlace de VPN de Azure es posible, pero se basa en espacios de direcciones definidos estáticamente en el archivo de configuración netcfg. BGP aún no se admite con instancias de Red virtual de Azure y puertas de enlace VPN. Sin BGP, definir manualmente los espacios de direcciones de tránsito en netcfg es difícil de hacer sin errores y no se recomienda.

### ¿Azure genera la misma clave precompartida de IPsec/IKE para todas mis conexiones VPN para la misma red virtual?

No, Azure de forma predeterminada genera distintas claves precompartidas para distintas conexiones VPN. Sin embargo, puede utilizar la API de REST para establecer la clave de la puerta de enlace VPN o el cmdlet PowerShell para establecer el valor de clave que prefiera. La clave TIENE QUE ser una cadena alfanumérica con una longitud de entre 1 y 128 caracteres.

### ¿Azure cobra por el tráfico entre redes virtuales?

Para el tráfico entre distintas redes virtuales de Azure, se cobra solo por el tráfico que atraviesa de una región de Azure a otra. Las tarifas se encuentran en la página [Precios de la puerta de enlace de VPN](https://azure.microsoft.com/pricing/details/vpn-gateway/) de Azure.


### ¿Puedo conectar una red virtual con VPN sobre IPsec a mi circuito de ExpressRoute?

Sí, este procedimiento se admite. Para más información, consulte [Configurar conexiones VPN ExpressRoute y sitio a sitio que coexistan](../expressroute/expressroute-howto-coexist-classic.md).

## Conectividad entre entornos y máquinas virtuales

### Si mi máquina virtual está en una red virtual y tengo una conexión entre locales, ¿cómo debo conectar a la máquina virtual?

Dispone de varias opciones. Si tiene RDP habilitado y ha creado un extremo, puede conectarse a la máquina virtual mediante la VIP. En ese caso, especificaría la VIP y el puerto al que desea conectarse. Tendrá que configurar el puerto en la máquina virtual para el tráfico. Normalmente, tendrá que ir al Portal de Azure clásico y guardar la configuración de la conexión RDP en el equipo. La configuración contendrá la información de conexión necesaria.

Si tiene configurada una red virtual con conectividad entre locales, puede conectarse a la máquina virtual mediante la DIP interna o una dirección IP privada. También puede conectarse a la máquina virtual mediante la DIP interna desde otra máquina virtual que se encuentre en la misma red virtual. Si se conecta desde una ubicación fuera de la red virtual no podrá usar RDP en la máquina virtual mediante la DIP. Por ejemplo, si tiene configurada una red virtual de punto a sitio y no establece una conexión desde su equipo, no podrá conectar a la máquina virtual mediante la DIP.

### ¿Si mi máquina virtual está en una red virtual con conectividad entre locales, todo el tráfico de mi máquina virtual pasa a través de esa conexión?

No. Únicamente el tráfico que tiene como destino una IP que se encuentra en los intervalos de direcciones IP de red local de la red virtual que haya especificado pasará a través de la puerta de enlace de red virtual. El tráfico que tenga una IP de destino ubicada dentro de la red virtual permanecerá en la red virtual. El resto del tráfico se envía a través del equilibrador de carga a las redes públicas, o si se usa la tunelización forzada, se envía a través de la puerta de enlace VPN de Azure. Si está solucionando problemas, es importante asegurarse de que en la red local tiene especificados todos los intervalos que desee enviar a través de la puerta de enlace. Compruebe que los intervalos de direcciones de red local no se superponen con ninguno de los intervalos de direcciones de la red virtual. Además, deberá comprobar que el servidor DNS que está utilizando está resolviendo el nombre a la dirección IP correcta.


## P+F de Red virtual

Consulte información adicional de redes virtuales adicionales en las [Preguntas frecuentes sobre redes virtuales](../virtual-network/virtual-networks-faq.md).

## Pasos siguientes

Puede ver más información sobre las puertas de enlace de VPN en la [Página de documentación de la puerta de enlace de VPN](https://azure.microsoft.com/documentation/services/vpn-gateway/).

 

<!---HONumber=AcomDC_0302_2016-->