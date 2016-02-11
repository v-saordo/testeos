<properties
	pageTitle="Implementación de una instancia del servicio Administración de API de Azure en varias regiones de Azure"
	description="Aprenda a implementar una instancia del servicio Administración de API de Azure en varias regiones de Azure." 
	services="api-management"
	documentationCenter=""
	authors="steved0x"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="api-management"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/07/2015"
	ms.author="sdanie"/>

# Implementación de una instancia del servicio Administración de API de Azure en varias regiones de Azure

Administración de API admite la implementación en varias regiones, lo que permite a los publicadores de API distribuir un único servicio de Administración de API en el número de regiones de Azure deseado. Esto ayuda a reducir la latencia de solicitud que perciben los usuarios de API distribuidos geográficamente y, además, mejora la disponibilidad del servicio en caso de que una región se quede sin conexión.

Al crear inicialmente un servicio de Administración de API, este contiene solo una [unidad][] y reside en una sola región de Azure, designada como región principal. Pueden agregarse otras regiones fácilmente a través del Portal de Azure clásico. El servidor puerta de enlace de Administración de API se implementa en cada región y el tráfico de llamada se enruta a la puerta de enlace más cercana. Cuando una región se queda sin conexión, el tráfico se redirige automáticamente a la siguiente puerta de enlace más cercana.

> [AZURE.IMPORTANT]La implementación en varias regiones solo está disponible en el nivel **[Premium][]**.

## <a name="add-region"> </a>Implementación de una instancia del servicio Administración de API en una nueva región

Para comenzar, haga clic en **Administrar** en el Portal de Azure clásico para el servicio Administración de API. De este modo, se abre el portal del publicador de Administración de API.

![Portal del publicador][api-management-management-console]

>Si todavía no ha creado una instancia del servicio Administración de API, consulte [Creación de una instancia del servicio de Administración de API][] en el tutorial [Introducción a la Administración de API de Azure][].

En el Portal de Azure clásico, navegue hasta la pestaña **Escala** de la instancia del servicio Administración de API.

![Pestaña Escala][api-management-scale-service]

Para implementar en una región nueva, haga clic en la lista desplegable situada bajo la región principal y elija una región de la lista.

![Agregar región][api-management-add-region]

Una vez seleccionada la región, elija el número de unidades para la nueva región en la lista desplegable de las unidades.

![Especificar unidades][api-management-select-units]

Una vez configuradas las regiones y las unidades deseadas, haga clic en **Guardar**.

## <a name="remove-region"> </a>Eliminación de una instancia del servicio Administración de API de una región

Para quitar una instancia del servicio Administración de API de una región, navegue hasta la pestaña **Escala** del Portal de Azure clásico para la instancia de dicho servicio.

![Pestaña Escala][api-management-scale-service]

Haga clic en la **X** que aparece a la derecha de la región que desea quitar.

![Quitar región][api-management-remove-region]

Una vez que se quiten las regiones deseadas, haga clic en **Guardar**.


[api-management-management-console]: ./media/api-management-howto-deploy-multi-region/api-management-management-console.png

[api-management-scale-service]: ./media/api-management-howto-deploy-multi-region/api-management-scale-service.png
[api-management-add-region]: ./media/api-management-howto-deploy-multi-region/api-management-add-region.png
[api-management-select-units]: ./media/api-management-howto-deploy-multi-region/api-management-select-units.png
[api-management-remove-region]: ./media/api-management-howto-deploy-multi-region/api-management-remove-region.png

[Creación de una instancia del servicio de Administración de API]: api-management-get-started.md#create-service-instance
[Introducción a la Administración de API de Azure]: api-management-get-started.md

[Deploy an API Management service instance to a new region]: #add-region
[Delete an API Management service instance from a region]: #remove-region

[unidad]: http://azure.microsoft.com/pricing/details/api-management/
[Premium]: http://azure.microsoft.com/pricing/details/api-management/

<!---HONumber=AcomDC_1210_2015-->