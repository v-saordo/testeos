<properties 
	pageTitle="Detalles de configuración de red para trabajar con ExpressRoute" 
	description="Detalles de configuración de red para ejecutar entornos del Servicio de aplicaciones en las redes virtuales conectadas a un circuito de ExpressRoute." 
	services="app-service" 
	documentationCenter="" 
	authors="stefsch" 
	manager="nirma" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/05/2016" 
	ms.author="stefsch"/>

# Detalles de configuración de red para entornos del Servicio de aplicaciones con ExpressRoute 

## Información general ##
Los clientes pueden conectar un circuito de [Azure ExpressRoute][ExpressRoute] a su infraestructura de red virtual, lo que permite ampliar la red local a Azure. Se puede crear un entorno del Servicio de aplicaciones en una subred de esta infraestructura de [red virtual][virtualnetwork]. Las aplicaciones que se ejecutan en el entorno del Servicio de aplicaciones pueden establecer conexiones seguras con los recursos backend a los que solo se puede tener acceso través de la conexión ExpressRoute.

**Nota:** no se puede crear un entorno del Servicio de aplicaciones en una red virtual "v2". Sin embargo, los entornos del Servicio de aplicaciones solo funcionan actualmente con las redes virtuales clásicas "v1".

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## Conectividad de red necesaria ##
Existen requisitos de conectividad de red para entornos del Servicio de aplicaciones que no pueden cumplirse inicialmente en una red virtual conectada a ExpressRoute. Los entornos del Servicio de aplicaciones requieren todas las opciones siguientes para funcionar correctamente:


-  Conectividad de red saliente a los puntos de conexión de Almacenamiento de Azure en todo el mundo. Esto incluye los puntos de conexión situados en la misma región que el Entorno del Servicio de aplicaciones, así como los puntos de conexión de almacenamiento ubicados en **otras** regiones de Azure. Los puntos de conexión de Almacenamiento de Azure se resuelven en los dominios DNS siguientes: *table.core.windows.net*, *blob.core.windows.net*, *queue.core.windows.net* y *file.core.windows.net*.  
-  Conectividad de red saliente a los puntos de conexión de Base de datos SQL ubicados en la misma región que el entorno del Servicio de aplicaciones. Los puntos de conexión de la Base de datos SQL se resuelven en el dominio siguiente: *database.windows.net*.
-  Conectividad de la red saliente a los puntos de conexión del plano de administración de Azure (puntos de conexión ASM y ARM). Incluye conectividad saliente tanto a *management.core.windows.net* como a *management.azure.com*. 
-  Conectividad de red saliente a *ocsp.msocsp.com*. Es necesario para admitir la funcionalidad SSL.
-  La configuración de DNS para la red virtual debe ser capaz de resolver todos los puntos de conexión y dominios mencionados en los puntos anteriores. Si estos puntos de conexión no se pueden resolver, se producirá un error en los intentos de creación del entorno del Servicio de aplicaciones y los entornos del Servicio de aplicaciones existentes se marcarán como incorrectos.
-  Si existe un servidor DNS personalizado en el otro punto de conexión de una puerta de enlace de VPN, el servidor DNS debe estar accesible desde la subred que contiene el entorno de Servicio de aplicaciones. 
-  La ruta de acceso de la red saliente no puede atravesar los servidores proxy corporativos internos, ni puede forzar la tunelización a local. Si lo hace, se cambiará la dirección NAT en vigor del tráfico de red saliente del entorno del Servicio de aplicaciones. Al cambiar la dirección NAT del tráfico de red de salida de un entorno del Servicio de aplicaciones causará errores de conectividad a muchos de los puntos de conexión enumerados anteriormente. Lo que da como resultado un error al intentar la creación del entorno del Servicio de aplicaciones, y además, los entornos del Servicio de aplicaciones previamente correctos estarán marcados como incorrectos.  
-  El acceso de red entrante a los puertos necesarios para los entornos del Servicio de aplicaciones debe estar permitido, como se describe en este [artículo][requiredports].

Se cumplen los requisitos de DNS al asegurar que se configura y se mantiene una infraestructura DNS válida para la red virtual. Si por algún motivo se cambia la configuración de DNS después de haber creado un entorno del Servicio de aplicaciones, los desarrolladores pueden forzar a un entorno del Servicio de aplicaciones para recoger la nueva configuración de DNS. Si se desencadena un reinicio gradual del entorno mediante el icono "Reiniciar", ubicado en la parte superior de la hoja de administración del entorno del Servicio de aplicaciones en el [Portal de Azure][NewPortal], el entorno recogerá la nueva configuración de DNS.

Se pueden cumplir los requisitos de acceso de red entrante mediante la configuración de un [grupo de seguridad de red][NetworkSecurityGroups] en la subred del entorno del Servicio de aplicaciones, como se describe en este [artículo][requiredports].

## Habilitar la conectividad de red saliente para un entorno del Servicio de aplicaciones##
De forma predeterminada, un circuito de ExpressRoute recién creado anuncia una ruta predeterminada que permite la conectividad saliente de Internet. Con esta configuración, un entorno del Servicio de aplicaciones podrá conectarse a otros extremos de Azure.

Sin embargo, una configuración de cliente común es definir su propia ruta predeterminada (0.0.0.0/0) que fuerza a que el tráfico saliente de Internet fluya a nivel local. El flujo de tráfico interrumpe invariablemente entornos del Servicio de aplicaciones porque el tráfico saliente está bloqueado de forma local o NAT tendría un conjunto de direcciones irreconocibles que ya no funcionan con varios extremos de Azure.

La solución es definir una (o más) rutas definidas por el usuario (UDR) en la subred que contiene el entorno del Servicio de aplicaciones. Una ruta definida por el usuario define las rutas de subred específica que se respetarán en lugar de la ruta predeterminada.

Si es posible, se recomienda usar la configuración siguiente:

- La configuración de ExpressRoute anuncia 0.0.0.0/0 y, de manera predeterminada, fuerza la tunelización de todo el tráfico saliente a local.
- La UDR aplicada a la subred que contiene el entorno del Servicio de aplicaciones define 0.0.0.0/0 con un tipo de salto siguiente de Internet (un ejemplo se muestra posteriormente en este artículo).

El efecto combinado de estos pasos es que la UDR a nivel de subred tendrá prioridad sobre la tunelización forzada de ExpressRoute, lo que garantiza el acceso a Internet saliente desde el entorno del Servicio de aplicaciones.

**IMPORTANTE:** las rutas definidas en una UDR **deben** ser lo suficientemente específicas para que tengan prioridad sobre cualquier otra ruta anunciada por la configuración de ExpressRoute. En el ejemplo siguiente se usa el intervalo de direcciones 0.0.0.0/0 amplio y como tal puede reemplazarse accidentalmente por los anuncios de ruta con intervalos de direcciones más específicos.

**MUY IMPORTANTE:** los entornos del Servicio de aplicaciones no son compatibles con las configuraciones de ExpressRoute que **anuncian incorrectamente rutas entre la ruta de acceso de emparejamiento público y la ruta de acceso de emparejamiento privado**. Las configuraciones de ExpressRoute con el emparejamiento público configurado recibirán anuncios de ruta de Microsoft para un amplio conjunto de intervalos de direcciones IP de Microsoft Azure. Si estos intervalos de direcciones se comunican incorrectamente en la ruta de acceso de interconexión privada, el resultado final es que en todos los paquetes de red saliente desde la subred del entorno del Servicio de aplicaciones se forzará incorrectamente la tunelización a la infraestructura de red local del cliente. Este flujo de red interrumpirá los entornos del Servicio de aplicaciones. La solución a este problema consiste en detener rutas anunciadas entre la ruta de acceso de interconexión pública y la ruta de acceso de interconexión privada.

Se puede encontrar información de contexto sobre las rutas definidas por el usuario en esta [información general][UDROverview].

Los detalles sobre cómo crear y configurar las rutas definidas por el usuario están disponibles en este [procedimiento][UDRHowTo].

## Ejemplo de configuración de rutas definidas por el usuario para un entorno del Servicio de aplicaciones ##

**Requisitos previos**

1. Instale la última versión de Azure PowerShell desde la página [Descargas de Azure][AzureDownloads] (fecha: junio de 2015 o posterior). En "Herramientas de línea de comandos" hay un vínculo "Instalar" en "Windows Powershell" que instalará los cmdlets más recientes de PowerShell.

2. Se recomienda crear una única subred para que el entorno del Servicio de aplicaciones lo utilice de manera exclusiva. Esto garantiza que las rutas definidas por el usuario aplicadas a la subred solo abrirán el tráfico saliente para el entorno del Servicio de aplicaciones.
3. **Importante**: no implemente el entorno del Servicio de aplicaciones hasta **después** de haber completado los siguientes pasos de configuración. Esto garantiza que la conectividad de red saliente esté disponible antes de intentar implementar un entorno del Servicio de aplicaciones.

**Paso 1: Crear una tabla de ruta con nombre**

El fragmento de código siguiente crea una tabla de ruta llamada "DirectInternetRouteTable" en la región Oeste de EE.UU. de Azure:

    New-AzureRouteTable -Name 'DirectInternetRouteTable' -Location uswest

**Paso 2: Crear una o varias rutas en la tabla de ruta**

Necesitará agregar una o varias rutas a la tabla de ruta para habilitar el acceso de salida a Internet.

El enfoque recomendado para configurar el acceso saliente a Internet es definir una ruta para 0.0.0.0/0, tal como se muestra a continuación.
  
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 0' -AddressPrefix 0.0.0.0/0 -NextHopType Internet

Recuerde que 0.0.0.0/0 es un intervalo de direcciones amplio y como tal se reemplaza por intervalos de direcciones más específicos anunciados por ExpressRoute. Para repetir la recomendación anterior, debe usarse una UDR con una ruta 0.0.0.0/0 junto con una configuración de ExressRoute que solo anuncia 0.0.0.0/0 también.

Como alternativa, puede descargar una lista completa y actualizada de intervalos CIDR usados por Azure. El archivo XML que contiene todos los intervalos de direcciones IP de Azure está disponible en el [Centro de descarga de Microsoft][DownloadCenterAddressRanges].

Sin embargo, tenga en cuenta que estos intervalos cambian con el tiempo, por lo que se necesitan actualizaciones periódicas manuales en una UDR para mantenerla sincronizada. Además, como hay un límite superior de 100 rutas en una única UDR, necesitará "agrupar" los intervalos de direcciones IP de Azure para ajustarse al límite de 100 rutas, teniendo en cuenta que las rutas definidas de UDR deben ser más específicas que las rutas anunciadas por ExpressRoute.


**Paso 3: Asociar la tabla de ruta a la subred que contiene el entorno del Servicio de aplicaciones**

El último paso de configuración es asociar la tabla de ruta a la subred donde se implementará el entorno del Servicio de aplicaciones. El comando siguiente asocia "DirectInternetRouteTable" a la subred "ASESubnet" que, en última instancia, contendrá un entorno del Servicio de aplicaciones.

    Set-AzureSubnetRouteTable -VirtualNetworkName 'YourVirtualNetworkNameHere' -SubnetName 'ASESubnet' -RouteTableName 'DirectInternetRouteTable'


**Paso 4: Pasos finales**

Una vez que la tabla de ruta está enlazada a la subred, se recomienda que primero pruebe y confirme el efecto deseado. Por ejemplo, implemente una máquina virtual en la subred y confirme que:


- El tráfico saliente a los puntos de conexión de Azure y los que no son de Azure mencionados anteriormente en este artículo **no** fluye de manera descendente al circuito de ExpressRoute. Es muy importante comprobar este comportamiento, ya que si en el tráfico saliente de la subred se sigue forzando la tunelización local, siempre se producirá un error de creación del entorno del Servicio de aplicaciones. 
- Las búsquedas de DNS para los puntos de conexión mencionados anteriormente se están resolviendo correctamente. 

Una vez confirmados los pasos anteriores, deberá eliminar la máquina virtual porque la subred debe estar "vacía" en el momento en que se crea el entorno del Servicio de aplicaciones.
 
Ahora, ya puede continuar con la creación de un entorno del Servicio de aplicaciones.

## Introducción

Para empezar a trabajar con los entornos del Servicio de aplicaciones, vea [Introducción al entorno del Servicio de aplicaciones][IntroToAppServiceEnvironment].

Para obtener más información acerca de la plataforma de Servicio de aplicaciones de Azure, consulte [Servicio de aplicaciones de Azure][AzureAppService].

<!-- LINKS -->
[virtualnetwork]: http://azure.microsoft.com/services/virtual-network/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[requiredports]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[NetworkSecurityGroups]: http://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[UDROverview]: http://azure.microsoft.com/documentation/articles/virtual-networks-udr-overview/
[UDRHowTo]: http://azure.microsoft.com/documentation/articles/virtual-networks-udr-how-to/
[HowToCreateAnAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[AzureDownloads]: http://azure.microsoft.com/downloads/
[DownloadCenterAddressRanges]: http://www.microsoft.com/download/details.aspx?id=41653
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[IntroToAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[NewPortal]: https://portal.azure.com
 

<!-- IMAGES -->

<!---HONumber=AcomDC_0107_2016-->