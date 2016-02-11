<properties 
	pageTitle="Uso de la red CDN | Microsoft Azure" 
	description="Obtenga información acerca del uso de la Red de entrega de contenido (CDN) de Azure para ofrecer contenido con alto ancho de banda mediante el almacenamiento en caché de blobs y contenidos estáticos." 
	services="cdn" 
	documentationCenter=".net" 
	authors="camsoper" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cdn" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="01/20/2016" 
	ms.author="casoper"/>


# Uso de la red CDN en Azure

La Red de entrega de contenido (CDN) de Azure es el bloque de creación fundamental para escalar cualquier aplicación HTTP en Azure. Ofrece a los clientes de Azure una solución global de almacenamiento en caché y entrega de contenido a los usuarios finales. Como resultado, en lugar de acudir al origen cada vez, las solicitudes de usuario se enrutan de manera inteligente al POP perimetral de la red CDN de mejor rendimiento. Esto aumenta considerablemente el rendimiento y experiencia del usuario. Para obtener una lista actualizada de las ubicaciones de nodos de la red CDN, consulte [Ubicaciones POP de la Red de entrega de contenido (CDN) de Azure](cdn-pop-locations.md).

Entre las ventajas de utilizar la red CDN para almacenar en memoria caché los datos de Azure se incluyen:

- Mejor rendimiento y experiencia para los usuarios finales que están lejos de un origen de contenido y utilizan aplicaciones donde son necesarias muchas solicitudes HTTP para cargar el contenido
- Distribución a gran escala para mejorar la administración de cargas instantáneas pesadas, por ejemplo, al comienzo de un acontecimiento como el lanzamiento de un producto.
- Al distribuir solicitudes de usuarios y ofrecer contenido desde POP perimetrales globales, se envía menos tráfico al origen.

>[AZURE.TIP]La red de entrega de contenido de Azure permite distribuir contenido desde diversos orígenes. Los orígenes integrados dentro de Azure incluyen el Servicio de aplicaciones, los Servicios en la nube, el Almacenamiento de blobs y los Servicios multimedia. También puede definir un origen personalizado utilizando cualquier dirección web accesible públicamente.

##Habilitación de la red de entrega de contenido

1. Cree un perfil de red de entrega de contenido con puntos de conexión que señalen al origen.

	Un perfil de red de entrega de contenido es una colección de puntos de conexión de red de entrega de contenido. Cada perfil contiene uno o más de estos puntos de conexión. Después de crear un perfil de red de entrega de contenido, puede crear un nuevo punto de conexión de red de entrega de contenido con el origen que ha elegido.
	
	>[AZURE.NOTE]De manera predeterminada, una sola suscripción de Azure está limitada a cuatro perfiles de red CDN. Cada perfil de red CDN está limitado a diez puntos de conexión de red CDN.
	>
	> Los precios de red de entrega de contenido se aplican en el nivel de perfil de red de entrega de contenido. Si quiere utilizar una combinación de características de red de entrega de contenido estándar y premium, necesitará varios perfiles de red de entrega de contenido.
	
	Si quiere un tutorial detallado sobre la creación de perfiles y puntos de conexión de red de entrega de contenido, consulte [Habilitación de la red de entrega de contenido para Azure](cdn-create-new-endpoint.md).
	
2. Configure la red de entrega de contenido.

	Puede habilitar varias características para el punto de conexión de res de entrega de contenido, como [almacenamiento en caché de directivas](cdn-caching-policy.md), [almacenamiento en caché de cadenas de consulta](cdn-query-string.md), [motor de reglas](cdn-rules-engine.md), etc. Para más información, consulte el menú **Administrar** de la izquierda.

3. Obtener acceso a su contenido de la red CDN

	Para obtener acceso al contenido almacenado en la memoria caché de la red CDN, use la URL de la red CDN que se le ha proporcionado en el portal. Por ejemplo, la dirección de un blob almacenado en caché será similar a la siguiente: `http://<identifier>.azureedge.net/<myPublicContainer>/<BlobName>`

## Almacenamiento en caché de contenido de almacenamiento de Azure

Una vez habilitada la red CDN en una cuenta de almacenamiento de Azure, cualquier blob que se encuentre en contenedores públicos y esté disponible para acceso anónimo se almacenará en caché a través de la red CDN . Solamente los blobs que estén públicamente disponibles se pueden almacenar en caché con CDN de Azure. Para que un blob esté públicamente disponible para acceso anónimo, debe denotar su contenedor como público. Una vez hecho eso, todos los blobs que se encuentren dentro de ese contenedor estarán disponibles para acceso de lectura anónimo. También tiene la opción de hacer públicos los datos de un contenedor o restringir el acceso solamente a los bloques que se encuentran dentro de él. Consulte [Administración del acceso a los recursos de almacenamiento de Azure](../storage/storage-manage-access-to-resources.md) para información sobre cómo administrar el control de acceso para contenedores y blobs.

Para obtener el máximo rendimiento, utilice la memoria caché perimetral de red CDN para entregar blobs con un tamaño inferior a 10 GB.

Cuando habilite el acceso a la red de entrega de contenido para una cuenta el almacenamiento, el Portal de administración le proporcionará un nombre de dominio de red de entrega de contenido con el siguiente formato: `http://<identifier>.azureedge.net/` Este nombre de dominio puede usarse para obtener acceso a los blobs de un contenedor público. Por ejemplo, en un contenedor público llamado Música de una cuenta de almacenamiento denominada MiCuenta, los usuarios pueden obtener acceso a los blobs de ese contenedor mediante el uso de cualquiera de las dos direcciones URL siguientes:

- **Dirección URL del servicio BLOB de Azure**: `http://myAccount.blob.core.windows.net/music/` 
- **Dirección URL de red CDN de Azure**: `http://<identifier>.azureedge.net/music/` 

## Almacenamiento en caché de contenido de Sitios web Azure

Puede habilitar la red CDN desde sus sitios web para almacenar en caché su contenido web, como imágenes, scripts y hojas de estilo. Consulte[ Integración de un sitio web de Azure con la red CDN de Azure](../app-service-web/cdn-websites-with-cdn.md).

Cuando habilite el acceso a la red de entrega de contenido para un sitio web, el Portal de administración le proporcionará un nombre de dominio de red de entrega de contenido con el siguiente formato: `http://<identifier>.azureedge.net/`. Este nombre de dominio se puede usar para recuperar objetos desde un sitio web. Por ejemplo, dado un contenedor público llamado cdn y un archivo de imagen llamado music.png, los usuarios pueden obtener acceso al objeto usando una de las dos direcciones URL siguientes:

- **Dirección URL del sitio web de azure**: `http://mySiteName.azurewebsites.net/cdn/music.png` 
- **Dirección URL de red CDN de Azure**: `http://<identifier>.azureedge.net/cdn/music.png`
 
## Almacenamiento en caché de contenido de servicios en la nube de Azure

Puede almacenar en caché objetos en la red CDN proporcionados por un servicio en la nube de Azure.

El almacenamiento en caché para servicios en la nube tiene las siguientes restricciones:


- La red CDN solamente se debe usar para almacenar en caché contenido estático.

	>[AZURE.WARNING]El almacenamiento en caché de contenido totalmente dinámico o muy volátil puede afectar negativamente al rendimiento o provocar problemas de contenido, todo ello con mayor costo.
- En el servicio la nube se debe implementar en una implementación de producción.
- El servicio en la nube debe proporcionar el objeto en el puerto 80 mediante HTTP.
- El servicio en la nube debe colocar el contenido que se va a almacenar en caché o que se va a proporcionar en la carpeta /cdn en dicho servicio.

Cuando habilite el acceso a la red de entrega de contenido para un servicio en la nube, el Portal de administración le proporcionará un nombre de dominio de red de entrega de contenido con el siguiente formato: `http://<identifier>.azureedge.net/` Este nombre de dominio se puede usar para recuperar objetos desde un servicio en la nube. Por ejemplo, dado un servicio en la nube llamado myHostedService y una página web ASP.NET llamada music.aspx que proporciona contenido, los usuarios pueden obtener acceso al objeto usando una de las dos direcciones URL siguientes:


- **Dirección URL del servicio en la nube de Azure**: `http://myHostedService.cloudapp.net/music.aspx` 
- **Dirección URL de red CDN de Azure**: `http://<identifier>.azureedge.net/music.aspx` 

## Almacenamiento en caché de contenido de orígenes personalizados

En la red de entrega de contenido se pueden almacenar en caché objetos que proporcionan las aplicaciones web accesibles públicamente.

El almacenamiento en caché de los orígenes personalizados tiene las siguientes restricciones:

- La red CDN solamente se debe usar para almacenar en caché contenido estático.

	>[AZURE.WARNING]El almacenamiento en caché de contenido totalmente dinámico o muy volátil puede afectar negativamente al rendimiento o provocar problemas de contenido, todo ello con mayor costo.
- El contenido en el origen personalizado debe estar hospedado en un servidor con una dirección IP pública. Los nodos perimetrales de la red de entrega de contenido son incapaces de recuperar activos de los servidores de intranet situados detrás de un firewall.

Cuando habilita el acceso a la red de entrega de contenido para una cuenta el almacenamiento, el Portal de Azure le proporciona un nombre de dominio de red de entrega de contenido con el siguiente formato: `http://<identifier>.azureedge.net/`. Este nombre de dominio se puede usar para recuperar objetos del origen personalizado. Por ejemplo, dado un sitio ubicado en www.contoso.com y una página web de ASP.NET llamada music.aspx que proporciona contenido, los usuarios pueden obtener acceso al objeto usando una de las dos direcciones URL siguientes:


- **Dirección URL de origen personalizado**: `http://www.contoso.com/music.aspx` 
- **Dirección URL de red CDN de Azure**: `http://<identifier>.azureedge.net/music.aspx` 

## Almacenamiento en caché de contenido específico con cadenas de consulta

Puede utilizar cadenas de consulta para diferenciar los objetos recuperados de un origen. Por ejemplo, si el origen muestra un gráfico que puede variar, puede pasar una cadena de consulta para recuperar el gráfico específico requerido. Por ejemplo:

`http://<identifier>.azureedge.net/chart.aspx?item=1`

Las cadenas de consulta se pasan como literales de cárdenas. Si tiene un servicio que toma dos parámetros, como `?area=2&item=1` y realiza las sucesivas llamadas al origen mediante `?item=1&area=2`, se almacenarán en caché dos copias del mismo objeto.

Para más información sobre el almacenamiento en caché de cadenas de consulta, consulte [Control del comportamiento de almacenamiento en caché de solicitudes de entrega de contenido con cadenas de consulta](cdn-query-string.md).

## Obtener acceso a contenido almacenado en caché a través de HTTPS

Azure le permite recuperar contenido desde la red CDN usando llamadas HTTPS. Esto le permite incorporar contenido almacenado en caché en la red CDN en páginas web seguras sin recibir advertencias acerca de los tipos de contenido de seguridad mixtos.

El acceso al contenido de la red CDN usando HTTPS tiene la siguiente restricciones:


- Debe utilizar el certificado proporcionado por la red CDN. No se admiten certificados de terceros.
- Debe usar el dominio de red CDN para obtener acceso el contenido. La compatibilidad con HTTPS no está disponible para nombres de dominio personalizados (CNAME) dado que la red CDN no admite certificados personalizados en este momento.

Para obtener más información acerca de la habilitación de HTTPS para contenido de red CDN, consulte [Habilitar la Red de entrega de contenido (CDN) para Azure](cdn-create-new-endpoint.md).


## Obtención de acceso a contenido almacenado en caché de con dominios personalizados

Puede asignar el extremo HTTP de la red CDN a un nombre de dominio personalizado y usar ese nombre para solicitar objetos desde dicha red.

Para obtener más información acerca de la asignación de un dominio personalizado, consulte [Asignación del contenido de la Red de entrega de contenido (CDN) a un dominio personalizado](cdn-map-content-to-custom-domain.md).

## Administración de la red CDN mediante programación

La red CDN de Microsoft Azure puede administrarse mediante programación con la [API de REST del proveedor de recursos de red CDN](https://msdn.microsoft.com/library/mt634456.aspx).


## Consulte también

- [Habilitación de la red de entrega de contenido para Azure](cdn-create-new-endpoint.md)
- [Información general de la red de entrega de contenido (CDN) de Azure](cdn-overview.md)
- [Depuración de un punto de conexión de red de entrega de contenido de Azure](cdn-purge-endpoint.md)
- [La API de REST del proveedor de recursos de red CDN](https://msdn.microsoft.com/library/mt634456.aspx)

<!---HONumber=AcomDC_0121_2016-->