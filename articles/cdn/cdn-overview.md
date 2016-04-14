<properties
	pageTitle="Información general de la red CDN de Azure"
	description="Obtenga información acerca de la Red de entrega de contenido (CDN) de Azure y de cómo usarla para ofrecer contenido con alto ancho de banda mediante el almacenamiento en caché de blobs y contenidos estáticos."
	services="cdn"
	documentationCenter=".NET"
	authors="camsoper"
	manager="erikre"
	editor=""/>

<tags
	ms.service="cdn"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/25/2016" 
	ms.author="casoper"/>

# Información general de la red de entrega de contenido (CDN) de Azure

La Red de entrega de contenido (CDN) de Azure almacena en caché blobs de Azure y contenido estático usado por servicios en la nube en ubicaciones colocadas estratégicamente para proporcionar el ancho de banda máximo para entregar contenido a los usuarios.

Si es un cliente de red de entrega de contenido existente, ahora puede administrar sus puntos de conexión de red de entrega de contenido a través del [Portal de Microsoft Azure](https://portal.azure.com).


CDN ofrece a los desarrolladores una solución global para entregar contenido de alto ancho de banda almacenando en caché el contenido en nodos físicos en todo el mundo. Para obtener una lista actualizada de las ubicaciones de nodos de la red CDN, consulte [Ubicaciones POP de la Red de entrega de contenido (CDN) de Azure](cdn-pop-locations.md).

Entre las ventajas de utilizar la red CDN para almacenar en memoria caché los datos de Azure se incluyen:

- Mejor rendimiento y experiencia de usuario para aquellos usuarios finales que están lejos de un origen de contenido y utilicen aplicaciones donde son necesarias muchas "conexiones a Internet" para cargar el contenido.
- Gran distribución para mejorar la administración de cargas instantáneas pesadas, por ejemplo, al comienzo de un evento de lanzamiento de un producto.


>[AZURE.IMPORTANT] Cuando crea o habilita un punto de conexión de red CDN, su propagación por todo el mundo puede tardar hasta 90 minutos.

Cuando una solicitud para un objeto se realiza primero a la red CDN, el objeto se recupera directamente desde la ubicación de origen del origen del objeto. Este origen puede ser una cuenta de almacenamiento de Azure, una aplicación web, un servicio de nube o cualquier origen personalizado que acepte solicitudes web públicas. Cuando una solicitud se realiza usando la sintaxis de red CDN, dicha solicitud se redirecciona al punto de conexión de red CDN más cercano a la ubicación desde la que la solicitud se realizó para proporcionar acceso al objeto. Si el objeto no se encuentra en ese punto de conexión, se recupera del servicio y se almacena en caché en el punto de conexión, donde se mantiene una configuración de tiempo de vida (TTL) para el objeto almacenado en caché.

## Características estándar

El nivel de red CDN estándar incluye las siguientes funciones:

- Fácil integración con servicios de Azure como aplicaciones web de [almacenamiento](cdn-create-a-storage-account-with-cdn.md) y servicios multimedia
- [Almacenamiento en caché de cadena de consulta](cdn-query-string.md)
- [Compatibilidad con nombre de dominio personalizado](cdn-map-content-to-custom-domain.md)
- [Filtrado por país](cdn-restrict-access-by-country.md)
- [Análisis esencial](cdn-analyze-usage-patterns.md)
- [Orígenes de contenido personalizados](cdn-how-to-use-cdn.md#caching-content-from-custom-origins)
- [Compatibilidad con HTTPS](cdn-how-to-use-cdn.md#accessing-cached-content-over-https)
- Equilibrio de carga
- Protección DDOS
- [Purga rápida](cdn-purge-endpoint.md)
- [Carga previa de activos](cdn-preload-endpoint.md)
- [Administración mediante API de REST](https://msdn.microsoft.com/library/mt634456.aspx)


## Características de la edición Premium

El nivel premium de la red de entrega de contenido incluye todas las características del nivel estándar, más estas características adicionales:

- [Motor de entrega de contenido personalizable, basado en reglas](cdn-rules-engine.md)
- [Informes de HTTP avanzados](cdn-advanced-http-reports.md)
- [Estadísticas en tiempo real](cdn-real-time-stats.md)

<!---HONumber=AcomDC_0302_2016-->