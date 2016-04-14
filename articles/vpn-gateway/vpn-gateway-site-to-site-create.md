<properties
   pageTitle="Creación de una red virtual con una conexión VPN de sitio a sitio mediante el Portal de Azure clásico | Microsoft Azure"
   description="Cree una red virtual con una conexión de puerta de enlace de VPN S2S para configuraciones entre entornos e híbridas con el modelo de implementación clásico."
   services="vpn-gateway"
   documentationCenter=""
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/04/2016"
   ms.author="cherylmc"/>

# Creación de una red virtual con una conexión VPN de sitio a sitio mediante el Portal de Azure clásico

> [AZURE.SELECTOR]
- [Azure Classic Portal](vpn-gateway-site-to-site-create.md)
- [PowerShell - Resource Manager](vpn-gateway-create-site-to-site-rm-powershell.md)

Este artículo le guiará a través de la creación de una red virtual y una conexión VPN de sitio a sitio con la red local. Se pueden utilizar conexiones de sitio a sitio para las configuraciones híbridas y entre locales. Este artículo se aplica al modelo de implementación **clásico**. Si desea crear una conexión de sitio a sitio mediante el modelo de implementación del **Administrador de recursos**, consulte [Crear una red virtual con una conexión VPN de sitio a sitio mediante PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md). Si desea conectar las redes virtuales, pero no va a crear una conexión a una ubicación local, consulte [Configurar una conexión de red virtual a red virtual en el Portal de Azure clásico](virtual-networks-configure-vnet-to-vnet-connection.md) o [Configuración de una conexión de red virtual a red virtual para redes virtuales en la misma suscripción mediante el Administrador de recursos de Azure y PowerShell](vpn-gateway-vnet-vnet-rm-ps.md).

**Información sobre los modelos de implementación de Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]
 
## Antes de empezar

Antes de comenzar con la configuración, comprueba que dispones de los elementos siguientes:

- Un dispositivo VPN compatible y alguien que pueda configurarlo. Consulta [Acerca de los dispositivos VPN](vpn-gateway-about-vpn-devices.md). Si no estás familiarizado con la configuración de tu dispositivo VPN o con los intervalos de direcciones IP, ubicados en la configuración de la red local, tendrás que trabajar con alguien que pueda proporcionar estos detalles por ti.

-  Una dirección IP pública externa para el dispositivo VPN. Esta dirección IP no puede estar detrás de un NAT.

- Una suscripción de Azure. Si todavía no tiene una suscripción de Azure, puede activar sus [beneficios de suscripción a MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) o bien registrarse para obtener una [evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/).


## Creación de la red virtual

1. Inicie sesión en el **Portal de Azure clásico**.

2. En la esquina inferior izquierda de la pantalla, haga clic en **Nuevo**. En el panel de navegación, haga clic en **Servicios de red** y, a continuación, haga clic en **Red virtual**. Haga clic en **Creación personalizada** para iniciar el Asistente para configuración.

3. Rellene la información en las páginas siguientes para crear la red virtual.

## Página de detalles de la red virtual

Escriba la siguiente información.

- **Nombre**: Nombre de la red virtual. Por ejemplo, *EastUSVNet*. Usará este nombre de red virtual al implementar las máquinas virtuales y las instancias PaaS, por lo que probablemente no querrá que este nombre sea muy complicado.
- **Ubicación**: La ubicación está directamente relacionada con la ubicación física (región) en la que desea que residan los recursos (máquinas virtuales). Por ejemplo, si desea que las máquinas virtuales que implementa en la red virtual se encuentren físicamente en el *Este de EE. UU.*, seleccione esa ubicación. No se puede cambiar la región asociada a la red virtual después de crearla.

## Página de servidores DNS y conectividad VPN

Especifique la siguiente información y, a continuación, haga clic en la flecha siguiente en el ángulo inferior derecho.

- **Servidores DNS**: escriba el nombre del servidor DNS y la dirección IP o seleccione un servidor DNS previamente registrado del menú contextual. Este valor no crea un servidor DNS; permite especificar los servidores DNS que desea usar para la resolución de nombres para esta red virtual.
- **Configurar VPN de sitio a sitio**: seleccione la casilla **Configurar una VPN de sitio a sitio**.
- **Red local**: una red local representa la ubicación física local. Puede seleccionar una red local que haya creado previamente o puede crear una nueva. Sin embargo, si decide utilizar una red local creada anteriormente, querrá ir a la página de configuración de **Redes locales** y asegurare de que la dirección IP del dispositivo VPN (dirección IPv4 pública) para el dispositivo VPN que se utiliza para esta conexión es precisa.

## Página de conectividad sitio a sitio

Si va a crear una nueva red local, verá la página **Conectividad de sitio a sitio**. Si desea usar una red local que creó anteriormente, esta página no aparecerá en el asistente y puede pasar a la siguiente sección.

Especifique la siguiente información y, a continuación, haga clic en la flecha siguiente.

- 	**Nombre**: el nombre que quiere usar para el sitio de red local.
- 	**Dirección IP del dispositivo VPN**: se trata de la dirección IPv4 pública del dispositivo VPN local que se va a utilizar para conectarse a Azure. El dispositivo VPN no se encuentra detrás de un NAT.
- 	**Espacio de direcciones**: incluidas la dirección IP de inicio y el CIDR (recuento de direcciones). Aquí se especifican los intervalos de direcciones que desea envían a través de la puerta de enlace de red virtual a su ubicación local. Si una dirección IP de destino se encuentra dentro de los intervalos que especifique aquí, se enrutará a través de la puerta de enlace de red virtual.
- 	**Agregar espacio de direcciones**: si tiene varios intervalos de direcciones que desea enviar a través de la puerta de enlace de red virtual, especifique aquí cada intervalo de direcciones adicional. Puede agregar o quitar rangos más adelante en la página **Red Local**.

## Página de espacios de direcciones de la red virtual

Especifique el intervalo de direcciones que desea usar para la red virtual. Estas son las direcciones IP dinámicas (DIPS) que se asignarán a las máquinas virtuales y a las demás instancias de rol implementadas en esta red virtual.

Es especialmente importante seleccionar un intervalo que no se superponga a ninguno de los intervalos usados para la red local. Necesitará coordinarse con el administrador de la red. Es posible que el administrador necesite definir un intervalo de direcciones IP desde el espacio de direcciones de la red local para que los use en la red virtual.

Escriba la información siguiente y, a continuación, haga clic en la marca de verificación situada en la esquina inferior derecha para configurar la red.

- **Espacio de direcciones**: incluidas la dirección IP de inicio y el recuento de direcciones. Compruebe que los espacios de direcciones especificados no se superponen con los espacios de direcciones que existen en la red local.
- **Agregar subred**: incluidas la dirección IP de inicio y el recuento de direcciones. No se necesitan subredes adicionales, pero puede que desee crear una subred independiente para las máquinas virtuales que tendrán DIP estáticas. O bien, puede que desee que las máquinas virtuales se encuentren en una subred independiente de las demás instancias de rol.
- **Agregar subred de puerta de enlace**: haga clic para agregar la subred de puerta de enlace. La subred de puerta de enlace solo se usa para la puerta de enlace de red virtual y es obligatoria para esta configuración.

Haga clic en la marca de verificación de la parte inferior derecha de la página y se empezará a crear la red virtual. Cuando termine, verá el mensaje **Creado** indicado en el **Estado** de la página **Redes** del Portal de Azure clásico. Una vez creada la red virtual, puede configurar la puerta de enlace de red virtual.

[AZURE.INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

## Configuración de la puerta de enlace de la red virtual

A continuación, configurará la puerta de enlace de la red virtual con el fin de crear una conexión segura de sitio a sitio. Consulte [Configuración de una puerta de enlace de VPN en el Portal de Azure clásico](vpn-gateway-configure-vpn-gateway-mp.md).

## Pasos siguientes

Puede agregar máquinas virtuales a la red virtual. Consulte [Creación de una máquina virtual personalizada](../virtual-machines/virtual-machines-create-custom.md).

Si desea configurar una conexión entre una red virtual clásica y una red virtual creada con el modo de Administrador de recursos de Azure, consulte [Conexión de redes virtuales clásicas a redes virtuales nuevas](../virtual-network/virtual-networks-arm-asm-s2s-howto.md).

<!---HONumber=AcomDC_0211_2016-->