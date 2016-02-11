<properties 
 pageTitle="Administración de la expiración del contenido del servicio en la nube en la Red de entrega de contenido de Azure (CDN)" 
 description="" 
 services="cdn" 
 documentationCenter=".NET" 
 authors="camsoper" 
 manager="dwrede" 
 editor=""/>
<tags 
 ms.service="cdn" 
 ms.workload="media" 
 ms.tgt_pltfrm="na" 
 ms.devlang="dotnet" 
 ms.topic="article" 
 ms.date="12/02/2015" 
 ms.author="casoper"/>

#Administración de la expiración del contenido del servicio en la nube en la Red de entrega de contenido de Azure (CDN)

Los objetos que obtienen el máximo beneficio del almacenamiento en caché de la red CDN de Azure son aquellos a los que se accede frecuentemente durante su período de tiempo de vida (TTL). Un objeto permanece en la memoria caché durante el período TTL y, a continuación, se actualiza desde el servicio en la nube una vez transcurrido ese tiempo. A continuación, el proceso se repite.

Si no proporciona valores de caché, el período TTL de un objeto es 7 días.

Para contenido estático como imágenes y hojas de estilo, puede controlar la frecuencia de actualización incluyendo un archivo en la carpeta CDN que incluye el contenido y modificar la configuración **clientCache** para controlar el encabezado Cache-Control para su contenido. La configuración web.config afectará a todo lo que contenga la carpeta y todas las subcarpetas, a menos que invalide que se invalide en otra subcarpeta más abajo. Por ejemplo, puede establecer un período de tiempo de vida en la raíz para tener todo el contenido estático almacenado en caché durante 3 días, pero tiene una subcarpeta que tiene más contenido variable con una configuración de caché de 6 horas.

El siguiente código XML muestra un ejemplo de configuración **clientCache** para especificar una edad máxima de 3 días:

	<configuration> 
	  <system.webServer> 
	        <staticContent> 
	            <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00" /> 
	        </staticContent> 
	  </system.webServer> 
	</configuration>

La especificación de **UseMaxAge** agrega un encabezado Cache-Control: max-age=<nnn> a la respuesta basándose en el valor especificado en el atributo **CacheControlMaxAge**. El formato del intervalo de tiempo para el atributo **cacheControlMaxAge** es <days>.<hours>:<min>:<sec>. Para obtener más información sobre el nodo **clientCache**, vea [Caché de cliente <clientCache>](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache).

Para contenido devuelto desde las aplicaciones como páginas .aspx, puede establecer el comportamiento de almacenamiento en caché CDN mediante programación estableciendo la propiedad **HttpResponse.Cache**. Para obtener más información acerca de la propiedad **HttpResponse.Cache**, vea [Propiedad HttpResponse.Cache](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx) y [Clase HttpCachePolicy](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx).

Si desea almacenar en caché el contenido de la aplicación mediante programación, asegúrese de que dicho contenido está marcado como almacenable en caché estableciendo HttpCacheability en *Public*. Asimismo, asegúrese de que se ha establecido un validador de caché. El validador de caché puede ser un intervalo de tiempo de última modificación establecido llamando a SetLastModified, o un valor de etag establecido llamando a SetETag. Opcionalmente, también puede especificar un tiempo de expiración de caché llamando a SetExpires, o puede basarse en la heurística de caché predeterminada descrita anteriormente en este documento.

Por ejemplo, para almacenar en caché el contenido durante una hora, agregue lo siguiente:

            // Set the caching parameters.
            Response.Cache.SetExpires(DateTime.Now.AddHours(1));
            Response.Cache.SetCacheability(HttpCacheability.Public);
            Response.Cache.SetLastModified(DateTime.Now);

##Otras referencias

[Administración de la expiración del contenido del blob en la Red de entrega de contenido de Azure (CDN)](./cdn-manage-expiration-of-blob-content.md)

<!---HONumber=AcomDC_1203_2015-->