<properties 
	pageTitle="Introducción al entorno del Servicio de aplicaciones" 
	description="Obtenga información sobre la característica de entorno del Servicio de aplicaciones que proporciona unidades de escalado dedicadas y seguras unidas en redes virtuales para ejecutar todas sus aplicaciones." 
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
	ms.date="01/05/2016"
	ms.author="stefsch"/>

# Introducción al entorno del Servicio de aplicaciones

## Información general ##
Un entorno del Servicio de aplicaciones es una opción de plan de servicio [Premium][PremiumTier] del Servicio de aplicaciones de Azure que proporciona un entorno plenamente aislado y dedicado para ejecutar de forma segura las aplicaciones del Servicio de aplicaciones de Azure a gran escala, lo que incluye [Aplicaciones web][WebApps], [Aplicaciones móviles][MobileApps] y [Aplicaciones de API][APIApps].

Los entornos del Servicio de aplicaciones son ideales para cargas de trabajo de aplicaciones que requieren:

- Muy grande escala
- Aislamiento y acceso a redes seguro

Los clientes pueden crear varios entornos del Servicio de aplicaciones en una o varias regiones de Azure. Esto hace que sean perfectos para los niveles de aplicación sin estado de escalado horizontal en el respaldo de cargas de trabajo RPS elevadas.

Los entornos del Servicios de aplicaciones están aislados para ejecutar únicamente las aplicaciones de un solo cliente, y siempre se implementan en una red virtual. Los clientes tienen un mayor control sobre el tráfico de red entrante y saliente de las aplicaciones, y las aplicaciones pueden establecer conexiones seguras de alta velocidad a los recursos corporativos locales a través de redes virtuales.

Para obtener información general del modo en que los Entornos del Servicio de aplicaciones permiten el acceso de red a alta escala y seguro, consulte el [vídeo Deep Dive de AzureCon][AzureConDeepDive] sobre Entornos del Servicio de aplicaciones.

Para profundizar en el escalado horizontal con varios Entornos del Servicio de aplicaciones, consulte el artículo sobre cómo configurar una [superficie de aplicaciones con distribución geográfica][GeodistributedAppFootprint].

Para ver cómo se ha configurado la arquitectura de seguridad mostrada en la inmersión en AzureCon, consulte el artículo sobre la implementación de una [arquitectura de seguridad en capas](app-service-app-service-environment-layered-security.md) con entornos del Servicio de aplicaciones.

Las aplicaciones que se ejecutan en entornos de aplicación de servicio pueden tener su acceso validado por dispositivos de subida como firewalls de aplicación web (WAF). En el artículo sobre la [configuración de un WAF para entornos de aplicación de servicio](app-service-app-service-environment-web-application-firewall.md) se trata este escenario.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## Recursos de proceso dedicados ##
Todos los recursos de proceso de un entorno del Servicio de aplicaciones están dedicados exclusivamente a una sola suscripción, y se puede configurar un entorno del Servicio de aplicaciones con hasta cincuenta recursos de proceso (50) para uso exclusivo de una única aplicación.

Un entorno de este tipo se compone de un grupo de recursos de proceso front-end y de entre uno a tres grupos de recursos de proceso de trabajo.

El grupo de servidores front-end contiene los recursos de proceso responsables de la terminación SSL y del equilibrio de carga automático de las solicitudes de aplicación dentro de un entorno del Servicio de aplicaciones.

Cada grupo de trabajo contiene recursos de proceso asignados a [Planes del Servicio de aplicaciones][AppServicePlan] que, a su vez, contienen una o más aplicaciones del Servicio de aplicaciones de Azure. Como en un entorno del Servicio de aplicaciones puede haber hasta tres grupos de trabajo distintos, tiene la flexibilidad de elegir diferentes recursos de proceso para cada grupo de trabajo.

Esto le permite, por ejemplo, crear un grupo de trabajo con recursos de proceso de menor eficacia para los planes del Servicio de aplicaciones destinados a aplicaciones de prueba o desarrollo. Un segundo (o incluso tercer) grupo de trabajo podría usar recursos de proceso más eficaces en los planes del Servicio de aplicaciones que ejecuten aplicaciones de producción.

Para obtener más detalles sobre la cantidad de recursos de proceso disponibles para los grupos de servidores front-end y los grupos de trabajo, consulte [Configuración de un entorno del Servicio de aplicaciones][HowToConfigureanAppServiceEnvironment].

Para obtener más detalles sobre los tamaños de los recursos de proceso disponibles admitidos en un entorno del Servicio de aplicaciones, consulte la página de [Precios de Servicio de aplicaciones][AppServicePricing] y revise las opciones disponibles para este tipo de entornos en el nivel de precios Premium.

## Compatibilidad con redes virtuales ##
Un entorno del Servicio de aplicaciones puede crearse en una red virtual regional clásica "v1" existente, o bien en una nueva red virtual regional clásica "v1" ([más información sobre redes virtuales][MoreInfoOnVirtualNetworks]). Puesto que los entornos de este tipo residen siempre en una red virtual regional y, más concretamente, en una subred de una red virtual regional, puede aprovechar las características de seguridad de las redes virtuales para controlar las comunicaciones de red entrantes y salientes.

Puede usar [grupos de seguridad de red][NetworkSecurityGroups] para restringir las comunicaciones de red entrantes a la subred donde reside el entorno del Servicio de aplicaciones. Esto le permite ejecutar aplicaciones tras dispositivos y servicios ascendentes, como firewalls de aplicaciones web y proveedores de SaaS de red.

Las aplicaciones, además, suelen requerir acceso a recursos corporativos, como bases de datos internas y servicios web. Un enfoque común consiste en poner estos extremos a disposición únicamente del flujo de tráfico de red interno de una red virtual de Azure. Una vez que un entorno del Servicio de aplicaciones se une a la misma red virtual que los servicios internos, las aplicaciones que se ejecutan en el entorno pueden tener acceso a ellos, incluidos los extremos accesibles mediante conexiones [De sitio a sitio][SiteToSite] y [Azure ExpressRoute][ExpressRoute].

Para obtener más detalles sobre cómo funcionan los entornos del Servicio de aplicaciones con redes virtuales y redes locales, consulte los siguientes artículos en [Información general sobre la arquitectura de red de los entornos del Servicio de aplicaciones][NetworkArchitectureOverview], [Cómo controlar el tráfico de entrada a un entorno del Servicio de aplicaciones][ControllingInboundTraffic] y [Conexión segura a los recursos de back-end desde un entorno del Servicio de aplicaciones][SecurelyConnectingToBackends].

**Nota:** no se puede crear un entorno del Servicio de aplicaciones en una red virtual "v2".

## Introducción

Para empezar a trabajar con los entornos del Servicio de aplicaciones, vea [Creación de un entorno del Servicio de aplicaciones][HowToCreateAnAppServiceEnvironment].

Para obtener más información acerca de la plataforma de Servicio de aplicaciones de Azure, consulte [Servicio de aplicaciones de Azure][AzureAppService].

Para obtener información general sobre la arquitectura de red del entorno del Servicio de aplicaciones, consulte el artículo [Información general sobre la arquitectura de red de los entornos del Servicio de aplicaciones][NetworkArchitectureOverview].

Para obtener información detallada sobre el uso de un entorno del Servicio de aplicaciones con ExpressRoute, consulte el siguiente artículo sobre [Detalles de configuración de red para entornos del Servicio de aplicaciones con ExpressRoute][NetworkConfigDetailsForExpressRoute].

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[PremiumTier]: http://azure.microsoft.com/pricing/details/app-service/
[MoreInfoOnVirtualNetworks]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePlan]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[HowToCreateAnAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[WebApps]: http://azure.microsoft.com/documentation/articles/app-service-web-overview/
[MobileApps]: http://azure.microsoft.com/documentation/articles/app-service-mobile-value-prop-preview/
[APIApps]: http://azure.microsoft.com/documentation/articles/app-service-api-apps-why-best-platform/
[LogicApps]: http://azure.microsoft.com/documentation/articles/app-service-logic-what-are-logic-apps/
[AzureConDeepDive]: https://azure.microsoft.com/documentation/videos/azurecon-2015-deploying-highly-scalable-and-secure-web-and-mobile-apps/
[GeodistributedAppFootprint]: https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-geo-distributed-scale/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[SiteToSite]: https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[HowToConfigureanAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment/
[ControllingInboundTraffic]: https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[SecurelyConnectingToBackends]: https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-securely-connecting-to-backend-resources/
[NetworkArchitectureOverview]: https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-architecture-overview/
[NetworkConfigDetailsForExpressRoute]: https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-configuration-expressroute/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/

<!-- IMAGES -->

<!---HONumber=AcomDC_0107_2016-->