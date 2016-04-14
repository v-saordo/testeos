<properties 
	pageTitle="Conexión segura a los recursos de back-end desde un entorno del Servicio de aplicaciones" 
	description="Obtenga información acerca de cómo conectarse de forma segura a los recursos de back-end desde un entorno del Servicio de aplicaciones." 
	services="app-service" 
	documentationCenter="" 
	authors="ccompy" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/10/2016" 
	ms.author="stefsch"/>

# Conexión segura a los recursos de back-end desde un entorno del Servicio de aplicaciones #

## Información general ##
Dado que siempre se crea un entorno del Servicio de aplicaciones en una subred de una [red virtual][virtualnetwork] regional clásica "v1", las conexiones salientes de dicho entorno a otros recursos de back-end solo pueden fluir a través de la red virtual.

**Nota:** No se puede crear un Entorno del Servicio de aplicaciones en una red virtual "v2" administrada por ARM.

Por ejemplo, puede haber un servidor SQL Server que se ejecute en un clúster de máquinas virtuales con el puerto 1433 bloqueado. En el extremo puede incluirse una lista de control de acceso para permitir únicamente el acceso de otros recursos de la misma red virtual.

Otro ejemplo: los extremos con información confidencial pueden ejecutarse localmente y conectarse a Azure mediante conexiones [De sitio a sitio][SiteToSite] o [Azure ExpressRoute][ExpressRoute]. Como resultado, solo los recursos de las redes virtuales conectadas a túneles ExpressRoute o De sitio a sitio podrán tener acceso a los extremos locales.

En todos estos escenarios, las aplicaciones que se ejecutan en un entorno del Servicio de aplicaciones podrán conectarse de forma segura a los distintos servidores y recursos. El tráfico saliente de las aplicaciones que se ejecutan en un entorno del Servicio de aplicaciones hacia extremos privados de la misma red virtual (o conectadas a la misma red virtual) solamente fluirá a través de la red virtual. El tráfico saliente hacia extremos privados no fluirá a través de la red pública de Internet.

Existe una excepción en cuanto al tráfico saliente desde un Entorno del Servicio de aplicaciones hacia los puntos de conexión de una red virtual. Los Entornos del Servicio de aplicaciones no pueden acceder a los puntos de conexión de máquinas virtuales ubicadas en la **misma** subred que el Entorno del Servicio de aplicaciones. Normalmente, esto no debería causar ningún problema siempre y cuando el Entorno del Servicio de aplicaciones se implemente en una subred reservada para uso exclusivo del mismo.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## Requisitos de DNS y conectividad saliente ##
Tenga en cuenta que, para que un entorno del Servicio de aplicaciones funcione correctamente, necesita acceso de salida al Almacenamiento de Azure en todo el mundo, así como conectividad a la base de datos SQL en la misma región de Azure. Si se bloquea el acceso de salida a Internet en la red virtual, los entornos del Servicio de aplicaciones no podrán tener acceso a estos extremos de Azure.

El cliente también puede tener los servidores DNS personalizados configurados en la red virtual. Los entornos del Servicio de aplicaciones necesitan poder resolver los extremos de Azure en *.database.windows.net, *.file.core.windows.net y *.blob.core.windows.net.

También se recomienda configurar de antemano los servidores DNS personalizados de la red virtual antes de crear un entorno del Servicio de aplicaciones. Si se cambia la configuración de DNS de una red virtual al crear un entorno del Servicio de aplicaciones, se generará un error en el proceso de creación de dicho entorno. Si existe un servidor DNS personalizado en el otro extremo de una puerta de enlace de VPN y el servidor DNS es inaccesible o no está disponible, el proceso de creación del entorno de servicio de aplicaciones también producirá un error.

## Conexión a un servidor SQL Server
Una configuración común de SQL Server tiene un extremo que escucha en el puerto 1433:

![Extremo de SQL Server][SqlServerEndpoint]

Existen dos formas de restringir el tráfico a este extremo:


- [Listas de control de acceso de red][NetworkAccessControlLists] (ACL de red)

- [Grupos de seguridad de red][NetworkSecurityGroups]


## Restricción del acceso con una lista de control de acceso de red

El puerto 1433 se puede proteger mediante una lista de control de acceso de red. En el ejemplo siguiente se incluyen en una lista blanca las direcciones de cliente que proceden de una red virtual concreta y se bloquea el acceso al resto de clientes.

![Ejemplo de lista de control de acceso de red][NetworkAccessControlListExample]

Todas las aplicaciones que se ejecuten en el entorno del Servicio de aplicaciones en la misma red virtual que el servidor SQL Server pueden conectarse a la instancia de SQL Server mediante la dirección IP **interna de la red virtual** para la máquina virtual SQL Server.

La cadena de conexión de ejemplo siguiente hace referencia a SQL Server mediante su dirección IP privada.

    Server=tcp:10.0.1.6;Database=MyDatabase;User ID=MyUser;Password=PasswordHere;provider=System.Data.SqlClient

Aunque la máquina virtual tiene también un extremo público, los intentos de conexión mediante la dirección IP pública se rechazarán debido a la lista de control de acceso de red.

## Restricción del acceso con un grupo de seguridad de red
Un enfoque alternativo para proteger el acceso consiste en usar un grupo de seguridad de red. Los grupos de seguridad de red pueden aplicarse a máquinas virtuales individuales o a una subred que contenga máquinas virtuales.

En primer lugar, es necesario crear un grupo de seguridad de red:

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

Restringir el acceso exclusivamente al tráfico interno de la red virtual es muy sencillo con un grupo de seguridad de red. Las reglas predeterminadas de un grupo de este tipo solo permiten el acceso de otros clientes de la misma red virtual.

Como resultado, bloquear el acceso a SQL Server es tan sencillo como aplicar un grupo de seguridad de red con sus reglas predeterminadas a las máquinas virtuales que ejecutan SQL Server o a la subred que contiene las máquinas virtuales.

En el ejemplo siguiente se aplica un grupo de seguridad de red a la subred contenedora:

    Get-AzureNetworkSecurityGroup -Name "testNSGExample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-1'
    
El resultado final es un conjunto de reglas de seguridad que bloquean el acceso externo y permiten el acceso interno de la red virtual:

![Reglas de seguridad de red predeterminadas][DefaultNetworkSecurityRules]


## Introducción

Para empezar a trabajar con los entornos del Servicio de aplicaciones, vea [Introducción al entorno del Servicio de aplicaciones][IntroToAppServiceEnvironment].

Para obtener más detalles sobre cómo controlar el tráfico entrante en el entorno del Servicio de aplicaciones, vea [Control del tráfico entrante en un entorno del Servicio de aplicaciones][ControlInboundASE]

Para obtener más información acerca de la plataforma de Servicio de aplicaciones de Azure, consulte [Servicio de aplicaciones de Azure][AzureAppService].

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]
 

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[ControlInboundTraffic]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[SiteToSite]: https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[NetworkAccessControlLists]: https://azure.microsoft.com/documentation/articles/virtual-networks-acl/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[IntroToAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[ControlInboundASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/

<!-- IMAGES -->
[SqlServerEndpoint]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/SqlServerEndpoint01.png
[NetworkAccessControlListExample]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/NetworkAcl01.png
[DefaultNetworkSecurityRules]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/DefaultNetworkSecurityRules01.png

<!---HONumber=AcomDC_0211_2016-->