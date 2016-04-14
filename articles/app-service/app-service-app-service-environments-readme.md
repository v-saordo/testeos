<properties 
	pageTitle="Entorno del Servicio de aplicaciones de Azure" 
	description="Obtenga información acerca de cómo funciona Servicio de aplicaciones." 
	keywords="entorno del servicio de aplicaciones, entorno del servicio de aplicaciones de azure"
	services="app-service" 
	documentationCenter="" 
	authors="yochay" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/18/2016" 
	ms.author="stefsch"/>

# Información general

Un entorno del Servicio de aplicaciones es una opción de plan de servicio [Premium][PremiumTier] del Servicio de aplicaciones de Azure que proporciona un entorno plenamente aislado y dedicado para ejecutar de forma segura las aplicaciones del Servicio de aplicaciones de Azure a gran escala, lo que incluye [Aplicaciones web][WebApps], [Aplicaciones móviles][MobileApps] y [Aplicaciones de API][APIApps].

Los entornos del Servicio de aplicaciones son ideales para cargas de trabajo de aplicaciones que requieren:

- Muy grande escala
- Aislamiento y acceso a redes seguro

Los clientes pueden crear varios entornos del Servicio de aplicaciones en una o varias regiones de Azure. Esto hace que sean perfectos para los niveles de aplicación sin estado de escalado horizontal en el respaldo de cargas de trabajo RPS elevadas.

Los entornos del Servicios de aplicaciones están aislados para ejecutar únicamente las aplicaciones de un solo cliente, y siempre se implementan en una red virtual. Los clientes tienen un mayor control sobre el tráfico de red entrante y saliente de la aplicación si usan [grupos de seguridad de red][NetworkSecurityGroups]. Las aplicaciones también pueden establecer conexiones seguras a alta velocidad por redes virtuales a los recursos corporativos locales.

Las aplicaciones suelen requerir acceso a recursos corporativos, como bases de datos internas y servicios web. Las aplicaciones que se ejecutan en entornos de Servicio de aplicaciones pueden tener acceso a los recursos accesibles mediante conexiones VPN [de sitio a sitio][SiteToSite] y [Azure ExpressRoute][ExpressRoute].

* [¿Qué es un entorno del Servicio de aplicaciones?](../app-service-web/app-service-app-service-environment-intro.md)
* [Creación de un Entorno del Servicio de aplicaciones](../app-service-web/app-service-web-how-to-create-an-app-service-environment.md)
* [Creación de aplicaciones en un Entorno del Servicio de aplicaciones](../app-service-web/app-service-web-how-to-create-a-web-app-in-an-ase.md)
* [Configuración de un entorno del Servicio de aplicaciones](../app-service-web/app-service-web-configure-an-app-service-environment.md) 
* [Escalado de aplicaciones en un Entorno del Servicio de aplicaciones](../app-service-web/app-service-web-scale-a-web-app-in-an-app-service-environment.md)
* [Arquitectura y seguridad de red](../app-service-web/app-service-app-service-environment-network-architecture-overview.md)

## Procedimientos

[AZURE.INCLUDE [app-service-blueprint-app-service-environment](../../includes/app-service-blueprint-app-service-environment.md)]


## Vídeos
[AZURE.VIDEO azurecon-2015-deploying-highly-scalable-and-secure-web-and-mobile-apps]

[AZURE.VIDEO microsoft-ignite-2015-running-enterprise-web-and-mobile-apps-on-azure-app-service]


<!-- LINKS -->
[PremiumTier]: http://azure.microsoft.com/pricing/details/app-service/
[WebApps]: http://azure.microsoft.com/documentation/articles/app-service-web-overview/
[MobileApps]: http://azure.microsoft.com/documentation/articles/app-service-mobile-value-prop-preview/
[APIApps]: http://azure.microsoft.com/documentation/articles/app-service-api-apps-why-best-platform/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[SiteToSite]: https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/

<!---HONumber=AcomDC_0224_2016-->