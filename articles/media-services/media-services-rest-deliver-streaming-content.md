<properties 
	pageTitle="Cómo entregar contenido de streaming de Servicios multimedia" 
	description="Aprenda a crear un localizador que se usa para generar una dirección URL de streaming. El código usa API de REST." 
	authors="Juliako" 
	manager="dwrede" 
	editor="" 
	services="media-services" 
	documentationCenter=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
 	ms.date="02/03/2016"  
	ms.author="juliako"/>


#Entrega de contenido de streaming

> [AZURE.SELECTOR]
- [.NET](media-services-deliver-streaming-content.md)
- [REST](media-services-rest-deliver-streaming-content.md)
- [Portal](media-services-manage-content.md#publish)

##Información general


Puede transmitir un conjunto de archivos MP4 de velocidad de bits adaptable creando un localizador de streaming a petición y compilando una dirección URL de streaming. El tema [Codificación de un recurso](media-services-rest-encode-asset.md) muestra cómo codificar en un conjunto de MP4 de velocidad de bits adaptable. Si el contenido está cifrado, configure la directiva de entrega de activos (como se describe en [este](media-services-rest-configure-asset-delivery-policy.md)tema) antes de crear un localizador.

También puede utilizar un localizador de streaming a petición para generar direcciones URL que señalen a archivos MP4 que se pueden descargar progresivamente.

En este tema se muestra cómo crear un localizador de streaming a petición para publicar el recurso y crear direcciones URL de streaming Smooth, MPEG DASH y HLS. También se muestra cómo generar direcciones URL de descarga progresiva.

En la sección [siguiente](#types) se muestran los tipos de enumeración cuyos valores se usan en las llamadas REST.
  
##Creación de un localizador de streaming a petición

Para crear el localizador de streaming a petición y obtener las direcciones URL, deberá hacer lo siguiente:


   1. Si se cifra el contenido, defina una directiva de acceso.
   2. Cree un localizador de streaming a petición.
   3. Si planea transmitir, obtenga el archivo de manifiesto de streaming (.ism) del recurso. 
   		
	Si planea la descarga progresiva, obtenga los nombres de los archivos MP4 del recurso. 
   4. Genere direcciones URL para el archivo de manifiesto o archivos MP4. 
   5. Tenga en cuenta que no se puede crear un localizador de transmisión mediante una AccessPolicy que incluye permisos de escritura o eliminación.


###Creación de una directiva de acceso

Solicitud:
		
	POST https://media.windows.net/api/AccessPolicies HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstest1&urn%3aSubscriptionId=zbbef702-e769-2233-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1424263184&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=NWE%2f986Hr5lZTzVGKtC%2ftzHm9n6U%2fxpTFULItxKUGC4%3d
	x-ms-version: 2.11
	x-ms-client-request-id: 6bcfd511-a561-448d-a022-a319a89ecffa
	Host: media.windows.net
	Content-Length: 68
	
	{"Name":"access policy","DurationInMinutes":43200.0,"Permissions":1}
	
Respuesta:
	
	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 311
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Location: https:/media.windows.net/api/AccessPolicies('nb%3Apid%3AUUID%3A69c80d98-7830-407f-a9af-e25f4b0d3e5f')
	Server: Microsoft-IIS/8.5
	request-id: a877528a-bdb4-4414-9862-273f8e64f882
	x-ms-request-id: a877528a-bdb4-4414-9862-273f8e64f882
	x-ms-client-request-id: 6bcfd511-a561-448d-a022-a319a89ecffa
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Wed, 18 Feb 2015 06:52:09 GMT
	
	{"odata.metadata":"https://media.windows.net/api/$metadata#AccessPolicies/@Element","Id":"nb:pid:UUID:69c80d98-7830-407f-a9af-e25f4b0d3e5f","Created":"2015-02-18T06:52:09.8862191Z","LastModified":"2015-02-18T06:52:09.8862191Z","Name":"access policy","DurationInMinutes":43200.0,"Permissions":1}

###Creación de un localizador de streaming a petición

Cree el localizador para el recurso especificado y la directiva de recursos.

Solicitud:
	
	POST https://media.windows.net/api/Locators HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstest1&urn%3aSubscriptionId=zbbef702-e769-2233-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1424263184&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=NWE%2f986Hr5lZTzVGKtC%2ftzHm9n6U%2fxpTFULItxKUGC4%3d
	x-ms-version: 2.11
	x-ms-client-request-id: ac159492-9a0c-40c3-aacc-551b1b4c5f62
	Host: media.windows.net
	Content-Length: 181
	
	{"AccessPolicyId":"nb:pid:UUID:1480030d-c481-430a-9687-535c6a5cb272","AssetId":"nb:cid:UUID:cc1e445d-1500-80bd-538e-f1e4b71b465e","StartTime":"2015-02-18T06:34:47.267872Z","Type":2}

Respuesta:
	
	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 637
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Location: https://media.windows.net/api/Locators('nb%3Alid%3AUUID%3Abe245661-2bbd-4fc6-b14f-9cf9a1492e5e')
	Server: Microsoft-IIS/8.5
	request-id: 5bd5864a-0afd-44c0-a67a-4044a2c9043b
	x-ms-request-id: 5bd5864a-0afd-44c0-a67a-4044a2c9043b
	x-ms-client-request-id: ac159492-9a0c-40c3-aacc-551b1b4c5f62
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Wed, 18 Feb 2015 06:58:37 GMT
	
	{"odata.metadata":"https://media.windows.net/api/$metadata#Locators/@Element","Id":"nb:lid:UUID:be245661-2bbd-4fc6-b14f-9cf9a1492e5e","ExpirationDateTime":"2015-03-20T06:34:47.267872+00:00","Type":2,"Path":"http://amstest1.streaming.mediaservices.windows.net/be245661-2bbd-4fc6-b14f-9cf9a1492e5e/","BaseUri":"http://amstest1.streaming.mediaservices.windows.net","ContentAccessComponent":"be245661-2bbd-4fc6-b14f-9cf9a1492e5e","AccessPolicyId":"nb:pid:UUID:1480030d-c481-430a-9687-535c6a5cb272","AssetId":"nb:cid:UUID:cc1e445d-1500-80bd-538e-f1e4b71b465e","StartTime":"2015-02-18T06:34:47.267872+00:00","Name":null}

###Creación de direcciones URL de streaming

Use el valor **Path** devuelto después de la creación del localizador para generar las direcciones URL Smooth, HLS y MPEG DASH.

Smooth Streaming: **Path** + nombre del archivo de manifiesto + "/manifest"

ejemplo:

	http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest

HLS: **Path** + nombre del archivo de manifiesto + "/manifest(format=m3u8-aapl)"

ejemplo:

	http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=m3u8-aapl)


DASH: **Path** + nombre del archivo de manifiesto + "/manifest(format=mpd-time-csf)"


ejemplo:

	http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=mpd-time-csf)


###Creación de direcciones URL de descarga progresiva

Use el valor **Path** devuelto después de la creación del localizador para generar la dirección URL de descarga progresiva.

URL: **Path** + nombre del archivo mp4 de recursos

ejemplo:

	http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

##<a id="types"></a>Tipos de enumeración

    [Flags]
    public enum AccessPermissions
    {
        None = 0,
        Read = 1,
        Write = 2,
        Delete = 4,
        List = 8,
    }

    public enum LocatorType
    {
        None = 0,
        Sas = 1,
        OnDemandOrigin = 2,
    }

##Rutas de aprendizaje de Servicios multimedia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Envío de comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##Consulte también

[Configuración de directivas de entrega de activos](media-services-rest-configure-asset-delivery-policy.md)

<!---HONumber=AcomDC_0211_2016-->