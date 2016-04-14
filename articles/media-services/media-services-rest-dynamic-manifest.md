<properties 
	pageTitle="Creación de filtros con la API de REST de Servicios multimedia de Azure" 
	description="En este tema se describe cómo crear filtros para que su cliente pueda usarlos para el streaming de secciones específicas de una secuencia. Servicios multimedia crea manifiestos dinámicos para lograr este streaming selectivo."
	services="media-services" 
	documentationCenter="" 
	authors="Juliako,cenkdin" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="ne" 
	ms.topic="article" 
 	ms.date="02/03/2016"  
	ms.author="juliako"/>

#Creación de filtros con la API de REST de Servicios multimedia de Azure

> [AZURE.SELECTOR]
- [.NET](media-services-dotnet-dynamic-manifest.md)
- [REST](media-services-rest-dynamic-manifest.md)


A partir de la versión 2.11, los Servicios multimedia permiten definir filtros para los activos. Estos filtros son reglas del lado servidor que permitirán a los clientes elegir realizar acciones como: reproducir solo una sección de un vídeo (en lugar de reproducir el vídeo completo), o especificar solo un subconjunto de las representaciones de audio y vídeo que el dispositivo de su cliente puede controlar (en lugar de todas las copias asociadas al activo). Este filtrado de sus activos se archiva a través de los **manifiestos dinámicos** que se crean tras la solicitud del cliente para transmitir un vídeo en función de los filtros especificados.

Para obtener más información detallada relacionada con filtros y manifiesto dinámico, vea [Información general de manifiesto dinámico](media-services-dynamic-manifest-overview.md).

En este tema se muestra cómo usar las API de REST para crear, actualizar y eliminar filtros.

##Tipos usados para crear filtros

Al crear filtros, se usan los siguientes tipos:

- [Filter](http://msdn.microsoft.com/library/azure/mt149056.aspx)
- [AssetFilter](http://msdn.microsoft.com/library/azure/mt149053.aspx)
- [PresentationTimeRange](http://msdn.microsoft.com/library/azure/mt149052.aspx)
- [FilterTrackSelect y FilterTrackPropertyCondition](http://msdn.microsoft.com/library/azure/mt149055.aspx)



>[AZURE.NOTE] Al trabajar con la API de REST de Servicios multimedia, se aplican las consideraciones siguientes:
>
>Al obtener acceso a las entidades de Servicios multimedia, debe establecer los campos de encabezado específicos y los valores en las solicitudes HTTP. Para obtener más información, consulte [Configuración del desarrollo de la API de REST de Servicios multimedia](media-services-rest-how-to-use.md).

>Después de conectarse correctamente a https://media.windows.net, recibirá una redirección 301 especificando otro URI de Servicios multimedia. Debe realizar las llamadas subsiguientes al nuevo URI como se describe en [Conexión a Servicios multimedia con la API de REST](media-services-rest-connect_programmatically.md).


##Crear filtros

###Crear filtros globales

Para crear un filtro global, use las siguientes solicitudes HTTP:

####Solicitud HTTP

Encabezados de solicitud

	POST https://media.windows.net/API/Filters HTTP/1.1 
	DataServiceVersion:3.0 
	MaxDataServiceVersion: 3.0 
	Content-Type: application/json 
	Accept: application/json 
	Accept-Charset: UTF-8 
	Authorization: Bearer <token value> 
	x-ms-version: 2.11 
	x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
	Host:media.windows.net 

Request body

	{  
	   "Name":"GlobalFilter",
	   "PresentationTimeRange":{  
	      "StartTimestamp":"0",
	      "EndTimestamp":"9223372036854775807",
	      "PresentationWindowDuration":"12000000000",
	      "LiveBackoffDuration":"0",
	      "Timescale":"10000000"
	   },
	   "Tracks":[  
	      {  
	         "PropertyConditions":
	              [  
	            {  
	               "Property":"Type",
	               "Value":"audio",
	               "Operator":"Equal"
	            },
	            {  
	               "Property":"Bitrate",
	               "Value":"0-2147483647",
	               "Operator":"Equal"
	            }
	         ]
	      }
	   ]
	}




####Respuesta HTTP
	
	HTTP/1.1 201 Created 

###Crear AssetFilters locales

Para crear un AssetFilter local, use las siguientes solicitudes HTTP:

####Solicitud HTTP

Encabezados de solicitud

	POST https://media.windows.net/API/AssetFilters HTTP/1.1 
	DataServiceVersion: 3.0 
	MaxDataServiceVersion: 3.0 
	Content-Type: application/json 
	Accept: application/json 
	Accept-Charset: UTF-8 
	Authorization: Bearer <token value> 
	x-ms-version: 2.11 
	x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
	Host: media.windows.net  

Request body

	{   
	   "Name":"AssetFilter", 
	   "ParentAssetId":"nb:cid:UUID:536e555d-1500-80c3-92dc-f1e4fdc6c592", 
	   "PresentationTimeRange":{   
	      "StartTimestamp":"0", 
	      "EndTimestamp":"9223372036854775807", 
	      "PresentationWindowDuration":"12000000000", 
	      "LiveBackoffDuration":"0", 
	      "Timescale":"10000000" 
	   }, 
	   "Tracks":[   
	      {   
	         "PropertyConditions": 
	              [   
	            {   
	               "Property":"Type", 
	               "Value":"audio", 
	               "Operator":"Equal" 
	            }, 
	            {   
	               "Property":"Bitrate", 
	               "Value":"0-2147483647", 
	               "Operator":"Equal" 
	            } 
	         ] 
	      } 
	   ] 
	} 

####Respuesta HTTP 

	HTTP/1.1 201 Created 
	. . . 

##Enumerar filtros

###Obtener todos los **filtro**s globales en la cuenta de AMS

Para enumerar filtros, use las siguientes solicitudes HTTP:

####Solicitud HTTP
	 
	GET https://media.windows.net/API/Filters HTTP/1.1 
	DataServiceVersion:3.0 
	MaxDataServiceVersion: 3.0 
	Accept: application/json 
	Accept-Charset: UTF-8 
	Authorization: Bearer <token value> 
	x-ms-version: 2.11 
	Host: media.windows.net 
	
### Obtener **AssetFilter**s asociados a un recurso

####Solicitud HTTP

	GET https://media.windows.net/API/Assets('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592')/AssetFilters HTTP/1.1 
	DataServiceVersion: 3.0 
	MaxDataServiceVersion: 3.0 
	Accept: application/json 
	Accept-Charset: UTF-8 
	Authorization: Bearer <token value> 
	x-ms-version: 2.11 
	x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
	Host: media.windows.net 

###Obtener un **AssetFilter** según su id.

####Solicitud HTTP

	GET https://media.windows.net/API/AssetFilters('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592__%23%23%23__TestFilter') HTTP/1.1 
	DataServiceVersion: 3.0 
	MaxDataServiceVersion: 3.0 
	Accept: application/json 
	Accept-Charset: UTF-8 
	Authorization: Bearer <token value> 
	x-ms-version: 2.11 
	x-ms-client-request-id: 00000000


##Actualizar filtros
 
Use PATCH, PUT o MERGE para actualizar un filtro con nuevos valores de propiedad. Para obtener más información acerca de estas operaciones, vea [PATCH, PUT, MERGE](http://msdn.microsoft.com/library/dd541276.aspx).
 
Si actualiza un filtro, se pueden tardar hasta 2 minutos en que el extremo de streaming actualice las reglas. Si el contenido se proporcionó con este filtro (y se almacenó en caché en los servidores proxy y cachés de CDN), la actualización de este filtro puede generar errores del reproductor. Se recomienda borrar la memoria caché después de actualizar el filtro. Si esta opción no es posible, piense en usar un filtro diferente.
 
###Actualizar los filtros globales

Para actualizar un filtro global, use las siguientes solicitudes HTTP:

####Solicitud HTTP
 
Encabezados de solicitud:

	MERGE https://media.windows.net/API/Filters('filterName') HTTP/1.1 
	DataServiceVersion:3.0 
	MaxDataServiceVersion: 3.0 
	Content-Type: application/json 
	Accept: application/json 
	Accept-Charset: UTF-8 
	Authorization: Bearer <token value> 
	x-ms-version: 2.11 
	x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
	Host: media.windows.net 
	Content-Length: 384
	
Cuerpo de la solicitud:
	
	{ 
	   "Tracks":[   
	      {   
	         "PropertyConditions": 
	         [   
	            {   
	               "Property":"Type", 
	               "Value":"audio", 
	               "Operator":"Equal" 
	            }, 
	            {   
	               "Property":"Bitrate", 
	               "Value":"0-2147483647", 
	               "Operator":"Equal" 
	            } 
	         ] 
	      } 
	   ] 
	} 

###Actualización de AssetFilters locales

Para actualizar un filtro local, use las siguientes solicitudes HTTP:

####Solicitud HTTP

Encabezados de solicitud:

	MERGE https://media.windows.net/API/AssetFilters('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592__%23%23%23__TestFilter')  HTTP/1.1 
	DataServiceVersion: 3.0 
	MaxDataServiceVersion: 3.0 
	Content-Type: application/json 
	Accept: application/json 
	Accept-Charset: UTF-8 
	Authorization: Bearer <token value> 
	x-ms-version: 2.11 
	x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
	Host: media.windows.net 
	
Cuerpo de la solicitud:
	
	{ 
	   "Tracks":[   
	      {   
	         "PropertyConditions": 
	         [   
	            {   
	               "Property":"Type", 
	               "Value":"audio", 
	               "Operator":"Equal" 
	            }, 
	            {   
	               "Property":"Bitrate", 
	               "Value":"0-2147483647", 
	               "Operator":"Equal" 
	            } 
	         ] 
	      } 
	   ] 
	} 


##Eliminar filtros


###Eliminar filtros globales

Para eliminar un filtro local, use las siguientes solicitudes HTTP:
	
####Solicitud HTTP

	DELETE https://media.windows.net/api/Filters('GlobalFilter') HTTP/1.1 
	DataServiceVersion:3.0 
	MaxDataServiceVersion: 3.0 
	Accept: application/json 
	Accept-Charset: UTF-8 
	Authorization: Bearer <token value> 
	x-ms-version: 2.11 
	Host: media.windows.net 


###Eliminar AssetFilters locales

Para eliminar un AssetFilter local, use las siguientes solicitudes HTTP:

####Solicitud HTTP

	DELETE https://media.windows.net/API/AssetFilters('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592__%23%23%23__LocalFilter') HTTP/1.1 
	DataServiceVersion: 3.0 
	MaxDataServiceVersion: 3.0 
	Accept: application/json 
	Accept-Charset: UTF-8 
	Authorization: Bearer <token value> 
	x-ms-version: 2.11 
	Host: media.windows.net 

##Generar direcciones URL de streaming que usan filtros

Para obtener información sobre cómo publicar y entregar los activos, vea [Información general de entrega de contenido a clientes](media-services-deliver-content-overview.md).


En los ejemplos siguientes se muestra cómo agregar filtros a sus URL de streaming.

**MPEG DASH**

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf, filter=MyFilter)

**Apple HTTP Live Streaming (HLS) V4**

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl, filter=MyFilter)

**Apple HTTP Live Streaming (HLS) V3**

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3, filter=MyFilter)

**Smooth Streaming**

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyFilter)


**HDS**

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=f4m-f4f, filter=MyFilter)


##Rutas de aprendizaje de Servicios multimedia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Envío de comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##Otras referencias 

[Información general de manifiestos dinámicos](media-services-dynamic-manifest-overview.md)
 

 

<!---HONumber=AcomDC_0211_2016-->