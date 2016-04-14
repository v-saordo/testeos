<properties 
   pageTitle="¿Qué son las rutas definidas por el usuario y el reenvío IP?"
   description="Aprenda a utilizar rutas definidas por el usuario (UDR) y el reenvío IP para reenviar el tráfico a la red de aplicaciones virtuales de Azure."
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

# ¿Qué son las rutas definidas por el usuario y el reenvío IP?
Al agregar máquinas virtuales (VM) a una red virtual (VNet) en Azure, observará que las máquinas virtuales son capaces de comunicarse automáticamente con otras máquinas por toda la red. No es necesario que especifique una puerta de enlace, incluso si las máquinas virtuales están en subredes diferentes. Cuando existe una conexión híbrida de Azure a su centro de datos, puede aplicar esto a la comunicación de las máquinas virtuales en la red pública e incluso a su red local.

Este flujo de comunicación es posible porque Azure usa varias rutas del sistema que definen cómo fluye el tráfico IP. Las rutas del sistema controlan el flujo de comunicación de los escenarios siguientes:

- Desde dentro de la misma subred.
- Desde una subred a otra dentro de una red virtual.
- Desde máquinas virtuales a Internet.
- Desde una red virtual a otra red virtual a través de una puerta de enlace de VPN.
- Desde una red virtual a su red local a través de una puerta de enlace de VPN.

La siguiente ilustración muestra una configuración simple con una red virtual, dos subredes y algunas máquinas virtuales, junto con las rutas del sistema que permiten el flujo del tráfico IP.

![Rutas del sistema de Azure](./media/virtual-networks-udr-overview/Figure1.png)

Aunque el uso de rutas del sistema facilita el tráfico automáticamente para su implementación, hay casos en los que seguramente quiera controlar el enrutamiento de paquetes a través de una aplicación virtual. Puede hacerlo mediante la creación de rutas definidas por el usuario que especifiquen el próximo salto de los paquetes que fluyen a una subred específica para que así vayan a su dispositivo virtual y se habilite el reenvío IP de la máquina virtual que funciona como aplicación virtual.

La siguiente ilustración muestra un ejemplo de las rutas definidas por el usuario y el reenvío IP para forzar el echo de que los paquetes enviados de una subred a otra vayan a través de una aplicación virtual en una subred de terceros.

![Rutas del sistema de Azure](./media/virtual-networks-udr-overview/Figure2.png)

>[AZURE.IMPORTANT] Las rutas definidas por el usuario solo se aplican al tráfico que sale de una subred. Por ejemplo, no pueden crear rutas para especificar el modo de entrada del tráfico en una subred de Internet. Asimismo, la aplicación a la cual está enviando el tráfico no puede estar en la misma subred donde se origina ese tráfico. Recuerde siempre crear una subred independiente para sus aplicaciones.

## Enrutamiento
Los paquetes se enrutan sobre una red TCP/IP basada en una tabla de enrutamiento definida en cada nodo de la red física. Una tabla de enrutamiento es una colección de rutas individuales que se utiliza para decidir dónde reenviar los paquetes según la dirección IP de destino. Una ruta consta de lo siguiente:

- **Prefijo de dirección** El CIDR de destino al que se aplica la ruta, por ejemplo, 10.1.0.0/16.
- **Tipo de próximo salto**. El tipo de salto de Azure al que debe enviarse el paquete. Los valores posibles son:
	- **Local**. Representa la red virtual local. Por ejemplo, si tiene dos subredes, 10.1.0.0/16 y 10.2.0.0/16 en la misma red virtual, la ruta de cada subred de la tabla de enrutamiento tendrá un valor de próximo salto de *Local*.
	- **Puerta de enlace de VPN**. Representa una puerta de enlace de VPN S2S de Azure. 
	- **Internet**. Representa la puerta de enlace de Internet predeterminada proporcionada por la infraestructura de Azure. 
	- **Dispositivo virtual**. Representa un dispositivo virtual agregado a la red virtual de Azure.
	- **NULL**. Representa un agujero negro. Los paquetes enviados a un agujero negro no se reenviarán de ninguna manera.
- **Valor del próximo salto**. El valor del próximo salto contiene los paquetes de la dirección IP a la que se deben reenviar. Solo se permiten valores de próximo salto en las rutas donde el tipo de próximo salto es *Dispositivo virtual*.

## Rutas del sistema
Cada subred que se creó en una red virtual se asocia automáticamente a una tabla de enrutamiento que contiene las siguientes reglas de ruta de sistema:

- **Regla de red virtual local**: esta regla se crea automáticamente para cada subred de una red virtual. Especifica que hay un vínculo directo entre las máquinas virtuales en la red virtual y que no hay ningún salto intermedio.
- **Regla local**: esta regla se aplica a todo el tráfico destinado al intervalo de direcciones locales y usa la puerta de enlace de VPN como el próximo destino del salto.
- **Regla de Internet**: esta regla controla todo el tráfico destinado a la red pública y usa la puerta de enlace de Internet de infraestructura como el próximo salto para todo el tráfico destinado a Internet.

## Rutas definidas por el usuario
Para la mayoría de los entornos, sólo necesitará usar las rutas del sistema ya definidas por Azure. Sin embargo, puede que necesite crear una tabla de enrutamiento y agregar una o varias rutas en casos concretos, como:

- Tunelización forzada a Internet a través de la red local.
- Uso de dispositivos virtuales en el entorno de Azure.

En los escenarios anteriores, tendrá que crear una tabla de enrutamiento y agregarle las rutas definidas por el usuario. Puede tener varias tablas de enrutamiento y la misma tabla de enrutamiento puede asociarse a una o varias subredes. Y cada subred solo puede estar asociada a una tabla de enrutamiento única. Todas las máquinas virtuales y servicios de nube de una subred utilizan la tabla de enrutamiento asociada a esa subred.

Las subredes dependen de las rutas del sistema hasta que una tabla de enrutamiento se asocia a la subred. Una vez creada una asociación, el enrutamiento se realiza según la coincidencia de prefijo más larga (LPM) entre las rutas definidas por el usuario y las rutas predeterminadas. Si hay más de una ruta con la misma coincidencia LPM, se selecciona una ruta en función de su origen en el orden siguiente:

1. Ruta definida por el usuario
1. Ruta BGP (cuando se utiliza ExpressRoute)
1. Ruta del sistema

Para obtener información sobre cómo crear rutas definidas por el usuario, consulte [Cómo crear rutas y habilitar el reenvío IP en Azure](virtual-networks-udr-how-to.md#How-to-manage-routes).

>[AZURE.IMPORTANT] Las rutas definidas por el usuario solo se aplican a las máquinas virtuales de Azure y servicios de nube. Por ejemplo, si desea agregar un dispositivo virtual de firewall entre la red local y Azure, debe crear una ruta definida por el usuario para las tablas de enrutamiento de Azure que reenvían todo el tráfico del espacio de direcciones local al dispositivo virtual. Sin embargo, el tráfico entrante desde el espacio de direcciones local se propagará a través de la puerta de enlace de VPN o circuito ExpressRoute directamente en el entorno de Azure, omitiendo el dispositivo virtual.

## Rutas BGP
Si tiene una conexión de ExpressRoute entre la red local y Azure, puede habilitar BGP propagar las rutas de la red local a Azure. Estas rutas BGP se usan de la misma forma que las rutas del sistema y las rutas definidas por el usuario en cada subred de Azure. Para obtener más información, consulte [Introducción a ExpressRoute](../articles/expressroute/expressroute-introduction.md).

>[AZURE.IMPORTANT] Puede configurar el entorno de Azure para forzar la tunelización a través de la red local mediante la creación de una ruta definida por el usuario para la subred 0.0.0.0/0 que utiliza la puerta de enlace de VPN como el próximo salto. Sin embargo, esto solo funciona si se utiliza una puerta de enlace de VPN, no ExpressRoute. Para ExpressRoute, la tunelización forzada se configura a través de BGP.

## Reenvío IP
Como se describió anteriormente, una de las razones principales para crear una ruta definida por el usuario es reenviar el tráfico a un dispositivo virtual. Un dispositivo virtual no es más que una máquina virtual que ejecuta una aplicación utilizada para controlar el tráfico de red de alguna manera, como un firewall o un dispositivo NAT.

La máquina virtual de este dispositivo virtual debe ser capaz de recibir el tráfico entrante que no se dirige a sí mismo. Para permitir que una máquina virtual reciba el tráfico dirigido a otros destinos, debe habilitar el reenvío IP de la máquina virtual. Esta es una opción de configuración de Azure, no de la configuración del sistema operativo invitado.

## Pasos siguientes

- Obtenga información sobre cómo [crear rutas en el modelo de implementación del Administrador de recursos](virtual-network-create-udr-arm-template.md) y asociarlos a subredes. 
- Obtenga información sobre cómo [crear rutas en el modelo de implementación clásico](virtual-network-create-udr-classic-ps.md) y asociarlos a subredes.

<!---HONumber=AcomDC_0218_2016-->