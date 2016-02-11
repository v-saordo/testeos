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
	ms.date="12/08/2015" 
	ms.author="yochay"/>

# Información general

Un entorno del Servicio de aplicaciones es una opción de plan de servicio [Premium][PremiumTier] del Servicio de aplicaciones de Azure que proporciona un entorno plenamente aislado y dedicado para ejecutar de forma segura las aplicaciones del Servicio de aplicaciones de Azure a gran escala, lo que incluye [Aplicaciones web][WebApps], [Aplicaciones móviles][MobileApps] y [Aplicaciones de API][APIApps].

Los entornos del Servicio de aplicaciones son ideales para cargas de trabajo de aplicaciones que requieren:

- Muy grande escala
- Aislamiento y acceso a redes seguro

Los clientes pueden crear varios entornos del Servicio de aplicaciones en una o varias regiones de Azure. Esto hace que sean perfectos para los niveles de aplicación sin estado de escalado horizontal en el respaldo de cargas de trabajo RPS elevadas.

Los entornos del Servicios de aplicaciones están aislados para ejecutar únicamente las aplicaciones de un solo cliente, y siempre se implementan en una red virtual. Los clientes tienen un mayor control sobre el tráfico de red entrante y saliente de la aplicación si usan [grupos de seguridad de red][NetworkSecurityGroups]. Las aplicaciones también pueden establecer conexiones seguras a alta velocidad por redes virtuales a los recursos corporativos locales.

Las aplicaciones suelen requerir acceso a recursos corporativos, como bases de datos internas y servicios web. Las aplicaciones que se ejecutan en entornos de Servicio de aplicaciones pueden tener acceso a los recursos accesibles mediante conexiones VPN [de sitio a sitio][SiteToSite] y [Azure ExpressRoute][ExpressRoute].

[AZURE.INCLUDE [app-service-blueprint-app-service-environment](../../includes/app-service-blueprint-app-service-environment.md)]

<!-- LINKS -->
[PremiumTier]: http://azure.microsoft.com/pricing/details/app-service/
[WebApps]: http://azure.microsoft.com/documentation/articles/app-service-web-overview/
[MobileApps]: http://azure.microsoft.com/documentation/articles/app-service-mobile-value-prop-preview/
[APIApps]: http://azure.microsoft.com/documentation/articles/app-service-api-apps-why-best-platform/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[SiteToSite]: https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/

<!---HONumber=AcomDC_0121_2016-->