<properties 
	pageTitle="Conexión con la cuenta de Servicios multimedia mediante la API de REST" 
	description="En este tema se muestra cómo conectarse a Servicios multimedia con la API de REST." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
 	ms.date="02/03/2016"  
	ms.author="juliako"/>


# Conexión con la cuenta de Servicios multimedia mediante la API de REST

> [AZURE.SELECTOR]
- [.NET](media-services-dotnet-connect_programmatically.md)
- [REST](media-services-rest-connect_programmatically.md)

En este tema se describe cómo obtener una conexión mediante programación a los Servicios multimedia de Microsoft Azure al programar con la API de REST de Servicios multimedia.

Hay dos elementos necesarios al obtener acceso a Servicios multimedia de Microsoft Azure: un token de acceso proporcionado por los Servicios de control de acceso (ACS) de Azure y el propio URI de Servicios multimedia. Puede usar los métodos que desee para crear estas solicitudes siempre que especifique los valores de encabezado adecuados y pase el token de acceso correctamente al llamar a Servicios multimedia.

En los pasos siguientes se describe el flujo de trabajo más común al usar la API de REST de Servicios multimedia para conectarse a Servicios multimedia:

1. Obtención de un token de acceso 
2. Conexión al URI de Servicios multimedia 

	>[AZURE.NOTE] Después de conectarse correctamente a https://media.windows.net, recibirá una redirección 301 especificando otro URI de Servicios multimedia. Debe realizar las llamadas posteriores al nuevo URI. Es posible que también reciba una respuesta HTTP/1.1 200 que contenga la descripción de metadatos de la API de ODATA.

3. Registre las llamadas de API posteriores en la nueva dirección URL.

	Por ejemplo, si después de intentar conectarse, obtiene lo siguiente:

		HTTP/1.1 301 Moved Permanently
		Location: https://wamsbayclus001rest-hs.cloudapp.net/api/

	Debe registrar las llamadas posteriores de API en https://wamsbayclus001rest-hs.cloudapp.net/api/.

##Obtención de un token de acceso

Para obtener acceso a Servicios multimedia directamente a través de la API de REST, recupere un token de acceso de ACS y úselo en todas las solicitudes HTTP que realice en el servicio. Este token es similar a otros tokens proporcionados por ACS basados en notificaciones de acceso proporcionadas en el encabezado de una solicitud HTTP y que usan el protocolo OAuth v2. No es necesario ningún otro requisito previo antes de conectarse directamente a Servicios multimedia.

En el ejemplo siguiente se muestra el encabezado y el cuerpo de solicitud HTTP que se usan para recuperar un token.

**Encabezado**:

	POST https://wamsprodglobal001acs.accesscontrol.windows.net/v2/OAuth2-13 HTTP/1.1
	Content-Type: application/x-www-form-urlencoded
	Host: wamsprodglobal001acs.accesscontrol.windows.net
	Content-Length: 120
	Expect: 100-continue
	Connection: Keep-Alive
	Accept: application/json

	
**Cuerpo**:

Debe probar los valores client\_id y client\_secret en el cuerpo de esta solicitud; client\_id y client\_secret se corresponden con los valores AccountName y AccountKey, respectivamente. Servicios multimedia le proporciona estos valores al configurar su cuenta.

Tenga en cuenta que el valor AccountKey de su cuenta de Servicios multimedia debe tener codificación URL (consulte [Percent-Encoding](http://tools.ietf.org/html/rfc3986#section-2.1) cuando se usa como el valor client\_secret en la solicitud de token de acceso.

	grant_type=client_credentials&client_id=ams_account_name&client_secret=URL_encoded_ams_account_key&scope=urn%3aWindowsAzureMediaServices


Por ejemplo:

	grant_type=client_credentials&client_id=amstestaccount001&client_secret=wUNbKhNj07oqjqU3Ah9R9f4kqTJ9avPpfe6Pk3YZ7ng%3d&scope=urn%3aWindowsAzureMediaServices


En el ejemplo siguiente se muestra la respuesta HTTP que contiene el token de acceso en el cuerpo de respuesta.

	HTTP/1.1 200 OK
	Cache-Control: no-cache, no-store
	Pragma: no-cache
	Content-Type: application/json; charset=utf-8
	Expires: -1
	request-id: a65150f5-2784-4a01-a4b7-33456187ad83
	X-Content-Type-Options: nosniff
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Thu, 15 Jan 2015 08:07:20 GMT
	Content-Length: 670
	
	{  
	   "token_type":"http://schemas.xmlsoap.org/ws/2009/11/swt-token-profile-1.0",
	   "access_token":"http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f19258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421330840&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=uf69n82KlqZmkJDNxhJkOxpyIpA2HDyeGUTtSnq1vlE%3d",
	   "expires_in":"21600",
	   "scope":"urn:WindowsAzureMediaServices"
	}
	

>[AZURE.NOTE]
Se recomienda almacenar en memoria caché los valores "access\_token" y "expires\_in" en un almacenamiento externo. Los datos del token se pueden recuperar más tarde desde el almacenamiento y se pueden reutilizar en las llamadas de API de REST de Servicios multimedia. Esto es especialmente útil para escenarios en que el token se puede compartir de forma segura entre varios procesos o equipos.

Asegúrese de supervisar el valor "expires\_in" del token de acceso y actualice las llamadas de API de REST con nuevos tokens según sea necesario.

###Conexión al URI de Servicios multimedia

El URI raíz de Servicios multimedia es https://media.windows.net/. Inicialmente, debe conectarse a este URI y, si obtiene una redirección 301 como respuesta, debe realizar las llamadas posteriores al nuevo URI. Además, no use ninguna lógica de redirección automática/seguimiento en las solicitudes. Los verbos HTTP y los cuerpos de solicitud no se enviarán al nuevo URI.

Tenga en cuenta que el URI raíz para cargar y descargar archivos de recursos es https://yourstorageaccount.blob.core.windows.net/, donde el nombre de la cuenta de almacenamiento es el mismo que usó al configurar la cuenta de Servicios multimedia.

En el ejemplo siguiente se muestra la solicitud HTTP al URI raíz de Servicios multimedia (https://media.windows.net/)). La solicitud obtiene una redirección 301 como respuesta. La solicitud posterior usa el nuevo URI (https://wamsbayclus001rest-hs.cloudapp.net/api/)).

**Solicitud HTTP**:
	
	GET https://media.windows.net/ HTTP/1.1
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f19258-6753-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421500579&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=ElVWXOnMVggFQl%2ft9vhdcv1qH1n%2fE8l3hRef4zPmrzg%3d
	x-ms-version: 2.11
	Accept: application/json
	Host: media.windows.net


**Respuesta HTTP**:
	
	HTTP/1.1 301 Moved Permanently
	Location: https://wamsbayclus001rest-hs.cloudapp.net/api/
	Server: Microsoft-IIS/8.5
	request-id: 987d7652-497a-44e5-b815-f492e02aef97
	x-ms-request-id: 987d7652-497a-44e5-b815-f492e02aef97
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Sat, 17 Jan 2015 07:44:53 GMT
	Content-Length: 164
	
	<html><head><title>Object moved</title></head><body>
	<h2>Object moved to <a href="https://wamsbayclus001rest-hs.cloudapp.net/api/">here</a>.</h2>
	</body></html>


**Solicitud HTTP** (con el nuevo URI):
			
	GET https://wamsbayclus001rest-hs.cloudapp.net/api/ HTTP/1.1
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f19258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421500579&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=ElVWXOnMVggFQl%2ft9vhdcv1qH1n%2fE8l3hRef4zPmrzg%3d
	x-ms-version: 2.11
	Accept: application/json
	Host: wamsbayclus001rest-hs.cloudapp.net


**Respuesta HTTP**:
	
	HTTP/1.1 200 OK
	Cache-Control: no-cache
	Content-Length: 1250
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Server: Microsoft-IIS/8.5
	request-id: 5f52ea9d-177e-48fe-9709-24953d19f84a
	x-ms-request-id: 5f52ea9d-177e-48fe-9709-24953d19f84a
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Sat, 17 Jan 2015 07:44:52 GMT
	
	{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata","value":[{"name":"AccessPolicies","url":"AccessPolicies"},{"name":"Locators","url":"Locators"},{"name":"ContentKeys","url":"ContentKeys"},{"name":"ContentKeyAuthorizationPolicyOptions","url":"ContentKeyAuthorizationPolicyOptions"},{"name":"ContentKeyAuthorizationPolicies","url":"ContentKeyAuthorizationPolicies"},{"name":"Files","url":"Files"},{"name":"Assets","url":"Assets"},{"name":"AssetDeliveryPolicies","url":"AssetDeliveryPolicies"},{"name":"IngestManifestFiles","url":"IngestManifestFiles"},{"name":"IngestManifestAssets","url":"IngestManifestAssets"},{"name":"IngestManifests","url":"IngestManifests"},{"name":"StorageAccounts","url":"StorageAccounts"},{"name":"Tasks","url":"Tasks"},{"name":"NotificationEndPoints","url":"NotificationEndPoints"},{"name":"Jobs","url":"Jobs"},{"name":"TaskTemplates","url":"TaskTemplates"},{"name":"JobTemplates","url":"JobTemplates"},{"name":"MediaProcessors","url":"MediaProcessors"},{"name":"EncodingReservedUnitTypes","url":"EncodingReservedUnitTypes"},{"name":"Operations","url":"Operations"},{"name":"StreamingEndpoints","url":"StreamingEndpoints"},{"name":"Channels","url":"Channels"},{"name":"Programs","url":"Programs"}]}
	 


>[AZURE.NOTE] Una vez que obtenga el nuevo URI, ese será el URI que debe usarse para comunicarse con Servicios multimedia.


##Rutas de aprendizaje de Servicios multimedia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Envío de comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0211_2016-->