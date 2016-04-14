<properties 
	pageTitle="Referencia de la directiva de Administración de API de Azure" 
	description="Obtenga información acerca de las directivas disponibles para configurar Administración de API." 
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
	ms.date="02/16/2016" 
	ms.author="sdanie"/>

# Referencia de la directiva de Administración de API de Azure

Esta sección proporciona un índice de las directivas en la [Referencia de la directiva de Administración de API de Azure][]. Para obtener más información sobre cómo agregar y configurar directivas, consulte [Directivas en Administración de API][].

Las expresiones de directiva pueden utilizarse como valores de atributos o valores de texto en cualquiera de las directivas de Administración de API, a menos que la directiva especifique lo contrario. Algunas directivas como [Flujo de control][] y [Establecer variable][] se basan en expresiones de directiva. Para obtener más información, consulte [Directivas avanzadas][] y [Expresiones de directiva][].

## Índice de referencia de directiva

-	[Directivas de restricción de acceso][]
	-	[Activar encabezado HTTP][]\: aplica la existencia o el valor de un encabezado HTTP.
	-	[Limitar la frecuencia de llamadas por suscripción][]\: evita los picos de uso de la API limitando la frecuencia de llamadas, por suscripción.
	-	[Limitar la frecuencia de llamadas por clave](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey): evita los picos de uso de la API limitando la frecuencia de llamadas, por clave.
	-	[Restringir IP de autor de llamada][]\: filtra (permite/deniega) las llamadas de direcciones IP específicas o de intervalos de direcciones.
	-	[Establecer cuota de uso por suscripción][]\: le permite aplicar un volumen de llamadas renovables o permanentes o una cuota de ancho de banda por suscripción.
	-	[Establecer cuota de uso por clave](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey): le permite aplicar un volumen de llamadas renovables o permanentes o una cuota de ancho de banda por clave.
	-	[Validar JWT][]\: aplica la existencia y la validez de un JWT extraído de un encabezado HTTP especificado o un parámetro de consulta especificado.
-	[Directivas avanzadas][]
	-	[Flujo de control][]\: aplica condicionalmente instrucciones de directiva basadas en los resultados de la evaluación de [expresiones][] booleanas.
	-	[Reenviar solicitud][]\: reenvía la solicitud al servicio back-end.
	-	[Registro para el centro de eventos][]\: envía mensajes en el formato especificado a un destino de mensaje definido por una entidad del [registrador](https://msdn.microsoft.com/library/azure/mt592020.aspx#Logger).
	-	[Devolver respuesta](https://msdn.microsoft.com/library/azure/dn894085.aspx#ReturnResponse): anula la ejecución de la canalización y devuelve la respuesta especificada directamente al llamador.
	-	[Enviar solicitud unidireccional](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendOneWayRequest): envía una solicitud a la dirección URL especificada sin esperar una respuesta.
	-	[Enviar solicitud](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendRequest): envía una solicitud a la dirección URL especificada.
	-	[Establecer método de solicitud](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetRequestMethod): le permite cambiar el método HTTP de una solicitud.
	-	[Establecer estado](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetStatus): cambia el código de estado HTTP al valor especificado.
	-	[Establecer variable][]\: conserva un valor en una variable de [contexto][] con nombre para el acceso posterior.
	-	[Esperar](https://msdn.microsoft.com/library/azure/dn894085.aspx#Wait): espera a que se completen las directivas adjuntas Enviar solicitud, Obtener el valor de caché o Flujo de control antes de continuar.
-	[Directivas de autenticación][]
	-	[Autenticar con opción básica][]\: autenticar con un servicio de back-end mediante la autenticación básica.
	-	[Autenticar con certificado de cliente][]\: autenticar con un servicio de back-end mediante certificados de cliente.
-	[Directivas de almacenamiento en caché][] 
	-	[Obtener de caché][]\: realiza una búsqueda en caché y devuelve una respuesta en caché válida cuando esté disponible.
	-	[Almacenar en caché][]\: almacena en caché la respuesta de acuerdo con la configuración de control de caché especificada.
	-	[Obtener valor de caché](https://msdn.microsoft.com/library/azure/dn894086.aspx#GetFromCacheByKey): recupere un elemento almacenado en caché por clave.
	-	[Almacenar valor en caché](https://msdn.microsoft.com/library/azure/dn894086.aspx#StoreToCacheByKey): almacene un elemento en la memoria caché por clave.
-	[Directivas entre dominios][] 
	-	[Permitir llamadas entre dominios][]\: permite que la API sea accesible desde los clientes basados en explorador de Adobe Flash y Microsoft Silverlight.
	-	[CORS][]\: agrega soporte de uso compartido de recursos entre orígenes (CORS) a una operación o a una API para permitir llamadas entre dominios desde clientes basados en explorador.
	-	[JSONP][]\: agrega JSON con soporte de relleno (JSONP) a una operación o a una API para permitir llamadas entre dominios desde clientes basados en explorador de JavaScript.
-	[Directivas de transformación][] 
	-	[Convertir JSON a XML][] \: convierte el cuerpo de solicitud o respuesta de JSON a XML.
	-	[Convertir XML a JSON][] \: convierte el cuerpo de solicitud o respuesta de XML a JSON.
	-	[Buscar y reemplazar la cadena en el cuerpo][]\: encuentra una subcadena de solicitud o de respuesta y la reemplaza por una subcadena diferente.
	-	[Enmascarar URL en el contenido][]\: reescribe (enmascara) vínculos en el cuerpo de respuesta y en el encabezado de la ubicación para que apunten al vínculo equivalente a través de la puerta de enlace.
	-	[Establecer el servicio back-end][]\: cambia el servicio back-end para una solicitud entrante.
	-	[Establecer cuerpo][] -establece el cuerpo del mensaje para las solicitudes entrantes y salientes.
	-	[Establecer encabezado HTTP][]\: asigna un valor a un encabezado de respuesta o de solicitud existente o agrega un nuevo encabezado de este tipo.
	-	[Establecer el parámetro de cadena de consulta][]\: agrega, reemplaza el valor o elimina el parámetro de la cadena de consulta de la solicitud.
	-	[URL de reescritura][]\: convierte una URL de solicitud de su forma pública a la forma esperada por el servicio web.

## Pasos siguientes

Para obtener más información acerca de las expresiones de directivas, vea el siguiente vídeo.

> [AZURE.VIDEO policy-expressions-in-azure-api-management]

[Directivas de restricción de acceso]: https://msdn.microsoft.com/library/azure/dn894078.aspx
[Activar encabezado HTTP]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#CheckHTTPHeader
[Limitar la frecuencia de llamadas por suscripción]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#LimitCallRate
[Restringir IP de autor de llamada]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#RestrictCallerIPs
[Establecer cuota de uso por suscripción]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#SetUsageQuota
[Validar JWT]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#ValidateJWT

[Directivas avanzadas]: https://msdn.microsoft.com/library/azure/dn894085.aspx
[Flujo de control]: https://msdn.microsoft.com/library/azure/dn894085.aspx#choose
[Establecer variable]: https://msdn.microsoft.com/library/azure/dn894085.aspx#set_variable
[expresiones]: https://msdn.microsoft.com/library/azure/dn910913.aspx
[contexto]: https://msdn.microsoft.com/library/azure/ea160028-fc04-4782-aa26-4b8329df3448#ContextVariables
[Reenviar solicitud]: https://msdn.microsoft.com/library/azure/dn894085.aspx#ForwardRequest
[Registro para el centro de eventos]: https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub

[Directivas de autenticación]: https://msdn.microsoft.com/library/azure/dn894079.aspx
[Autenticar con opción básica]: https://msdn.microsoft.com/library/azure/061702a7-3a78-472b-a54a-f3b1e332490d#Basic
[Autenticar con certificado de cliente]: https://msdn.microsoft.com/library/azure/061702a7-3a78-472b-a54a-f3b1e332490d#ClientCertificate
[Directivas de almacenamiento en caché]: https://msdn.microsoft.com/library/azure/dn894086.aspx
[Obtener de caché]: https://msdn.microsoft.com/library/azure/8147199c-24d8-439f-b2a9-da28a70a890c#GetFromCache
[Almacenar en caché]: https://msdn.microsoft.com/library/azure/8147199c-24d8-439f-b2a9-da28a70a890c#StoreToCache

[Directivas entre dominios]: https://msdn.microsoft.com/library/azure/dn894084.aspx
[Permitir llamadas entre dominios]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#AllowCrossDomainCalls
[CORS]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#CORS
[JSONP]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#JSONP

[Directivas de transformación]: https://msdn.microsoft.com/library/azure/dn894083.aspx
[Convertir JSON a XML]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#ConvertJSONtoXML
[Convertir XML a JSON]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#ConvertXMLtoJSON
[Buscar y reemplazar la cadena en el cuerpo]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#Findandreplacestringinbody
[Enmascarar URL en el contenido]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#MaskURLSContent
[Establecer el servicio back-end]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetBackendService
[Establecer cuerpo]: https://msdn.microsoft.com/library/azure/dn894083.aspx#SetBody
[Establecer encabezado HTTP]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetHTTPheader
[Establecer el parámetro de cadena de consulta]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetQueryStringParameter
[URL de reescritura]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#RewriteURL



[Directivas en Administración de API]: api-management-howto-policies.md
[Referencia de la directiva de Administración de API de Azure]: https://msdn.microsoft.com/library/azure/dn894081.aspx

[Expresiones de directiva]: https://msdn.microsoft.com/library/azure/dn910913.aspx

 

<!---HONumber=AcomDC_0218_2016-->