<properties 
	pageTitle="Notas de la versión de Servicios multimedia" 
	description="Notas de la versión de Servicios multimedia" 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="media" 
	ms.devlang="dotnet" 
	ms.topic="article" 
 	ms.date="03/01/2016"
	ms.author="juliako"/>


# Notas de la versión de Servicios multimedia de Azure

Estas notas de la versión resumen los cambios realizados desde las versiones anteriores y los problemas conocidos.

>[AZURE.NOTE] Queremos recibir opiniones de nuestros clientes y centrarnos en la solución de los problemas que les afectan. Para informar de un problema o formular una pregunta, realice una entrada en el [foro de MSDN de Servicios multimedia de Azure].

- [Problemas actualmente conocidos](#issues)
- [Historial de versiones de API de REST](#rest_version_history)
- [Versión de febrero de 2016](#feb_changes16)
- [Versión de enero de 2016](#jan_changes_16)
- [Versión de diciembre de 2015](#dec_changes_15)
- [Versión de noviembre de 2015](#nov_changes_15)
- [Versión de octubre de 2015](#oct_changes_15)
- [Versión de septiembre de 2015](#september_changes_15)
- [Versión de agosto de 2015](#august_changes_15)
- [Versión de julio de 2015](#july_changes_15)
- [Versión de junio de 2015](#june_changes_15)
- [Versión de mayo de 2015](#may_changes_15)
- [Versión de abril de 2015](#april_changes_15)
- [Versión de marzo de 2015](#march_changes_15)
- [Versión de febrero de 2015](#february_changes_15)
- [Versión de enero de 2015](#january_changes_15)
- [Versión de diciembre de 2014](#december_changes_14)
- [Versión de noviembre de 2014](#november_changes_14)
- [Versión de octubre de 2014](#october_changes_14)
- [Versión de septiembre de 2014](#september_changes_14)
- [Versión de agosto de 2014](#august_changes_14)
- [Versión de julio de 2014](#july_changes_14)
- [Versión de mayo de 2014](#may_changes_14)
- [Versión de abril de 2014](#april_changes_14) 
- [Versiones de enero/febrero de 2014](#jan_feb_changes_14) 
- [Versión de diciembre de 2013](#december_changes_13)
- [Versión de noviembre de 2013](#november_changes_13)
- [Versión de agosto de 2013](#august_changes_13)
- [Versión de junio de 2013](#june_changes_13)
- [Versión de diciembre de 2012](#december_changes_12)
- [Versión de noviembre de 2012](#november_changes_12)
- [Versión de vista previa de junio de 2012](#june_changes_12)


##<a id="issues"></a>Problemas actualmente conocidos

### <a id="general_issues"></a>Problemas generales de Servicios multimedia

Problema|Descripción
---|---
Varios encabezados comunes HTTP no se proporcionan en la API de REST.|Si desarrolla aplicaciones de Servicios multimedia mediante la API de REST, encontrará que algunos campos de encabezado comunes HTTP (como CLIENT-REQUEST-ID, REQUEST-ID y RETURN-CLIENT-REQUEST-ID) no se admiten. Los encabezados se agregarán en una futura actualización.
Al codificar un activo con un nombre de archivo que contiene caracteres de escape (por ejemplo, %20), se muestra el error "MediaProcessor: archivo no encontrado".|Los nombres de archivos que se van a agregar a un activo y que luego se van a codificar deben contener caracteres alfanuméricos y espacios. El problema se corregirá en una futura actualización.
El método ListBlobs que es parte del SDK de almacenamiento de Azure, versión 3.x, no funciona correctamente.|Los Servicios multimedia generan URL de SAS basadas en la versión del [12-02-2012](http://msdn.microsoft.com/library/azure/dn592123.aspx). Si desea usar el SDK de almacenamiento de Azure para mostrar los blobs de un contenedor de blobs, utilice el método [CloudBlobContainer.ListBlobs](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobs.aspx) que es parte del SDK de almacenamiento de Azure, versión 2.x. El método ListBlobs que es parte del SDK de almacenamiento de Azure, versión 3.x, no funcionará correctamente.
El mecanismo de limitación de Servicios multimedia restringe el uso de recursos en las aplicaciones que realizan un número excesivo de solicitudes al servicio. El servicio puede devolver el código de estado HTTP de servicio no disponible (503).|Para obtener más información, consulte la descripción del código de estado HTTP 503 en el tema [Códigos de error de Azure Media Services](http://msdn.microsoft.com/library/azure/dn168949.aspx).
Al consultar entidades, hay un límite de 1000 entidades devueltas a la vez, porque la REST v2 pública limita los resultados de consulta a 1000. | Debe usar **Skip** y **Take** (.NET)/ **top** (REST) como se describe en [este ejemplo de .NET](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) y [este ejemplo de API de REST](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities). 


### <a id="dotnet_issues"></a>Problemas del SDK .NE de Servicios multimedia

Problema|Descripción
---|---
Los objetos de Servicios multimedia del SDK no se pueden serializar y, como resultado, no funcionan con el almacenamiento en caché de Azure.|Si intenta serializar el objeto AssetCollection del SDK para agregarlo al almacenamiento en caché de Azure, se produce una excepción.

##<a id="rest_version_history"></a>Historial de versiones de API de REST

Para obtener información sobre el historial de versiones de la API de REST de Servicios multimedia, consulte [Referencia de la API de REST de Servicios multimedia de Azure].

##<a id="feb_changes16"></a>Versión de febrero de 2016

La última versión del SDK de Servicios multimedia de Azure para .NET (3.5.3) contiene una corrección de errores relacionada con Widevine. El problema era: AssetDeliveryPolicy no se podía reutilizar para varios recursos cifrados con Widevine. Como parte de esta corrección de errores se ha agregado la siguiente propiedad al SDK: **WidevineBaseLicenseAcquisitionUrl**.
	
	Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
	    new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
	{
	    {AssetDeliveryPolicyConfigurationKey.WidevineBaseLicenseAcquisitionUrl,"http://testurl"},
	    
	};

##<a id="jan_changes_16"></a>Versión de enero de 2016

El nombre de las unidades reservadas de codificación se cambia para reducir la confusión con los nombres de codificador.

Los nombres de las unidades reservadas de codificación Básica, Estándar y Premium se cambian por unidades reservadas S1, S2 y S3, respectivamente. Los clientes que usan unidades reservadas de codificación Básica ahora verán S1 como etiqueta en el Portal de Azure (y en la factura), mientras que para las ediciones Estándar y Premium aparecerán las etiquetas S2 y S3, respectivamente.

##<a id="dec_changes_15"></a>Versión de diciembre de 2015

El equipo del SDK de Azure publicó una nueva versión del paquete [SDK de Azure para PHP](http://github.com/Azure/azure-sdk-for-php) que contiene las actualizaciones y nuevas características de los Servicios multimedia de Microsoft Azure. En concreto, el SDK de Servicios multimedia de Azure para PHP admite ahora las características más recientes de la [protección de contenido](media-services-content-protection-overview.md): el cifrado dinámico con AES y DRM (PlayReady y Widevine) con y sin ninguna restricción de tokens. También admite el escalado de [unidades de codificación](media-services-dotnet-encoding-units.md).

Para más información, consulte:

- El blog del [SDK de Servicios multimedia de Microsoft Azure para PHP](http://southworks.com/blog/2015/12/09/new-microsoft-azure-media-services-sdk-for-php-release-available-with-new-features-and-samples/).
- Los siguientes [ejemplos de código](http://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices) para obtener ayuda para empezar rápidamente:
	- **vodworkflow\_aes.php**: se trata de un archivo PHP que muestra cómo usar el cifrado dinámico AES-128 y el servicio de entrega de claves. Se basa en el ejemplo de .NET que se explica en [este](media-services-protect-with-aes128.md) artículo.
	- **vodworkflow\_aes.php**: se trata de un archivo PHP que muestra cómo usar el cifrado dinámico PlayReady y el servicio de entrega de licencias. Se basa en el ejemplo de .NET que se explica en [este](media-services-protect-with-drm.md) artículo.
	- **scale\_encoding\_units.php**: se trata de un archivo PHP que muestra cómo escalar la unidad reservada de codificación.


##<a id="nov_changes_15"></a>Versión de noviembre de 2015

Los Servicios multimedia de Azure ofrecen ahora el servicio de entrega de licencias en la nube. Para más detalles, vea [este blog de anuncios](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/). Vea también [este tutorial](media-services-protect-with-drm.md) y el [repositorio de GitHub](http://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm).

Tenga en cuenta que los servicios de entrega de licencias de Widevine proporcionados por Servicios de multimedia de Azure están en vista previa. Para obtener más información, consulte [este blog](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/).

##<a id="oct_changes_15"></a>Versión de octubre de 2015

Servicios multimedia de Azure (AMS) ahora también está disponible en los centros de datos siguientes: Sur de Brasil, India occidental, Sur de la India e India central. Ahora puede usar el Portal de Azure clásico para [crear cuentas de Servicios multimedia](media-services-create-account.md#create-a-media-services-account-using-quick-create) y realizar diversas tareas descritas [aquí](https://azure.microsoft.com/documentation/services/media-services/). Sin embargo, Codificación en directo no está habilitado en estos centros de datos. Además, no todos los tipos de unidades reservadas de codificación están disponibles en estos centros de datos.

- Sur de Brasil: solo están disponibles las unidades reservadas de codificación básicas y estándar
- India occidental, Sur de la India e India central: solo están disponibles unidades reservadas de codificación básicas


##<a id="september_changes_15"></a>Versión de septiembre de 2015 

- AMS ofrece ahora la capacidad de proteger tanto vídeo bajo demanda (VOD) como secuencias activas con la tecnología DRM modular de Widevine. Puede usar los siguientes asociados de servicios de entrega para ayudarle a entregar licencias de Widevine: [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/), [castLabs](http://castlabs.com/company/partners/azure/). Para más información, vea [este blog](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/).

	Puede usar el [.NET SDK de AMS](https://www.nuget.org/packages/windowsazure.mediaservices/) (a partir de la versión 3.5.1) o la API de REST para configurar AssetDeliveryConfiguration para usar Widevine.

- AMS agregó compatibilidad para vídeos ProRes de Apple. Ahora puede cargar sus archivos de vídeos de origen QuickTime que usan ProRes de Apple u otros códecs. Para más información, vea [este blog](https://azure.microsoft.com/blog/announcing-support-for-apple-prores-videos-in-azure-media-services/).

- Ahora puede usar Media Encoder Estándar para realizar recortes secundarios y extracción de archivos directas. Para más información, vea [este blog](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/).

- Se realizaron las siguientes actualizaciones de filtrado:

	- Ahora puede usar el formato Apple HTTP Live Streaming (HLS) con filtro solo de audio. Esta actualización le permite quitar la pista solo de audio mediante la especificación de (audio-only=false) en la dirección URL.
	- Al definir filtros para los activos, ahora tiene la posibilidad de combinar varios filtros (hasta 3) en una sola dirección URL.

	Para más información, vea [este blog](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).

- AMS admite ahora I-Frames en HLS v4. La compatibilidad con I-Frames optimiza las operaciones de avance rápido y rebobinado. De forma predeterminada, todas las salidas de HLS v4 incluyen la lista de reproducción de I-Frame (EXT-X-I-FRAME-STREAM-INF).
 
	Para más información, vea [este blog](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).

##<a id="august_changes_15"></a>Versión de agosto de 2015

- Ya están disponibles el SDK de Servicios multimedia de Azure para la versión de Java V0.8.0 y nuevos ejemplos. Para más información, consulte:

	- [Entrada de blog](http://southworks.com/blog/2015/08/25/microsoft-azure-media-services-sdk-for-java-v0-8-0-released-and-new-samples-available/)
	- [Repositorio de ejemplos de Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
- Actualización del Reproductor multimedia de Azure con compatibilidad con secuencias de audio múltiples. Para más información, consulte:
	- [Entrada de blog](https://azure.microsoft.com/blog/2015/08/13/azure-media-player-update-with-multi-audio-stream-support/)

##<a id="july_changes_15"></a>Versión de julio de 2015

- Anuncia la disponibilidad general de Media Encoder estándar. Para más información, vea [esta publicación del blog](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/).

	Media Encoder Estándar usa valores predefinidos que se describen en [esta](http://go.microsoft.com/fwlink/?LinkId=618336) sección. Tenga en cuenta que cuando se usa un valor preestablecido para codificaciones de 4k, debe obtener el tipo de unidad reservada **Premium**. Para obtener más información, consulte [Escalación de codificación](media-services-portal-encoding-units).
- Subtítulos en tiempo real con Servicios multimedia de Azure y el Reproductor. Para más información, vea [esta publicación del blog](https://azure.microsoft.com/blog/2015/07/08/live-real-time-captions-with-azure-media-services-and-player/).

###Actualizaciones del SDK .NET de Servicios multimedia

Ahora la versión del SDK de Servicios multimedia para .NET de Azure es la 3.4.0.0. En esta versión se ha agregado la siguiente funcionalidad:

- Se ha implementado la compatibilidad con archivos activos. Tenga en cuenta que no se puede descargar un recurso que contenga un archivo activo.
- Se ha implementado compatibilidad para filtros dinámicos.
- Se ha implementado compatibilidad una funcionalidad que permite a los usuarios a mantener el contenedor de almacenamiento al eliminar el recurso.
- Correcciones de errores relacionados con directivas de reintentos en canales.
- **Flujo de trabajo de Codificador multimedia Premium** habilitado.

##<a id="june_changes_15"></a>Versión de junio de 2015

###Actualizaciones del SDK .NET de Servicios multimedia

Ahora la versión del SDK .NET de Servicios multimedia de Azure es la 3.3.0.0. En esta versión se ha agregado la siguiente funcionalidad:

- Compatibilidad con la especificación de detección de OpenId Connect.
- Compatibilidad con el control de sustitución de claves en el lado del proveedor de identidades. 

Si está usando un proveedor de identidades que expone el documento de detección de OpenID Connect (igual que los siguientes proveedores: Azure Active Directory, Google y Salesforce), puede indicar a Servicios multimedia de Azure que obtenga las claves de firma para la validación del token JWT de la especificación de detección de OpenID Connect.

Para obtener más información, consulte [Uso de claves web JSON de la especificación de detección OpenID Connect para trabajar con la autenticación de token JWT en Servicios multimedia de Azure](http://gtrifonov.com/2015/06/07/using-json-web-keys-from-openid-connect-discovery-spec-to-work-with-jwt-token-authentication-in-azure-media-services/).


##<a id="may_changes_15"></a>Versión de mayo de 2015

Presentación de las nuevas características siguientes:

- [Una vista previa de codificación en directo con Media Services](media-services-manage-live-encoder-enabled-channels.md)
- [Manifiesto dinámico](media-services-dynamic-manifest-overview.md)
- [Una vista previa del procesador multimedia de Azure Media Hyperlapse](https://azure.microsoft.com/blog/?p=286281&preview=1&_ppp=61e1a0b3db)

##<a id="april_changes_15"></a>Versión de abril de 2015

###Actualizaciones generales de Servicios multimedia

- [Presentación de Reproductor multimedia de Azure](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/).
- A partir de Servicios multimedia REST 2.10, los canales configurados para introducir un protocolo RTMP, se crean con direcciones URL de ingesta principal y secundaria. Para obtener más información, consulte [Configuraciones de ingesta de canales](media-services-manage-channels-overview.md#channel_input)
- Actualizaciones de Azure Media Indexer
	- Compatibilidad con el idioma español
	- Nuevo formato de xml de configuración
	
	Para obtener más información, consulte [este blog](https://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/).
###Actualizaciones del SDK .NET de Servicios multimedia

Ahora la versión del SDK .NET de Servicios multimedia de Azure es la versión 3.2.0.0.

Estas son algunas actualizaciones destinadas a los clientes:
 
- **Cambios importantes**: **TokenRestrictionTemplate.Issuer** y **TokenRestrictionTemplate.Audience** cambiados a un tipo de cadena. 
- Las actualizaciones relacionadas con la creación personalizada de directivas de reintento. 
- Correcciones de errores relacionados con la carga/descarga de archivos. 
- La clase **MediaServicesCredentials** ahora acepta el extremo de control de acceso principal y secundario para la autenticación.



##<a id="march_changes_15"></a>Versión de marzo de 2015

### Actualizaciones generales de Servicios multimedia

- Servicios multimedia ofrece ahora integración de CDN de Azure. Para admitir la integración, se ha agregado la propiedad **CdnEnabled** a **StreamingEndpoint**. **CdnEnabled** puede utilizarse con las API de REST a partir de la versión 2.9 (para obtener más información, consulte [StreamingEndpoint](https://msdn.microsoft.com/library/azure/dn783468.aspx)). **CdnEnabled** puede utilizarse con el SDK .NET a partir de la versión 3.1.0.2 (para obtener más información, consulte [StreamingEndpoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mediaservices.client.istreamingendpoint(v=azure.10).aspx)).
- Anuncio del **flujo de trabajo Premium de Codificador multimedia**. Para obtener más información, consulte [Introducción de la codificación Premium en Servicios multimedia de Azure](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/).
 


##<a id="february_changes_15"></a>Versión de febrero de 2015

### Actualizaciones generales de Servicios multimedia

La versión de la API de REST de Servicios multimedia es ahora la 2.9. A partir de esta versión, puede habilitar la integración de CDN de Azure con extremos de streaming. Para obtener más información, consulte [StreamingEndpoint](https://msdn.microsoft.com/library/dn783468.aspx).

##<a id="january_changes_15"></a>Versión de enero de 2015

### Actualizaciones generales de Servicios multimedia

Anuncio de disponibilidad General (GA) de protección de contenido con cifrado dinámico. Para más información, consulte [Azure Media Services enhances streaming security with General Availability of DRM technology](https://azure.microsoft.com/blog/2015/01/29/azure-media-services-enhances-streaming-security-with-general-availability-of-drm-technology/) (Servicios multimedia de Azure mejoran la seguridad de streaming con la disponibilidad general de tecnología DRM).

###Actualizaciones del SDK .NET de Servicios multimedia

Ahora la versión del SDK .NET de Servicios multimedia de Azure es la 3.1.0.1.

Esta versión marcó el constructor Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization.TokenRestrictionTemplate como obsoleto. El nuevo constructor toma TokenType como argumento.

	TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);


##<a id="december_changes_14"></a>Versión de diciembre de 2014

###Actualizaciones generales de Servicios multimedia

- Se agregaron algunas actualizaciones y nuevas características al procesador multimedia del indizador de Azure. Para obtener más información, consulte [Azure Media Indexer Version 1.1.6.7 Release Notes](https://azure.microsoft.com/blog/2014/12/03/azure-media-indexer-version-1-1-6-7-release-notes/) (Notas de la versión de Azure Media Indexer versión 1.1.6.7).
- Se agregó una nueva API de REST que permite actualizar unidades reservadas de codificación: [EncodingReservedUnitType con REST](http://msdn.microsoft.com/library/azure/dn859236.aspx).
- Se agregó compatibilidad con CORS para el servicio de entrega de claves.
- Se realizaron mejoras en el rendimiento de la directiva de autorización de consultas.
- En el centro de datos de China, la [dirección URL de entrega de claves](http://msdn.microsoft.com/library/azure/ef4dfeeb-48ae-4596-ab28-44d6b36d8769#get_delivery_service_url) es ahora por cliente (al igual que en otros centros de datos).
- Se agregó la duración de destino automático de HLS. Cuando se realiza el streaming en vivo, HLS siempre se empaqueta dinámicamente. De forma predeterminada, Servicios multimedia calcula automáticamente la relación de empaquetado por segmento HLS (FragmentsPerSegment) según el intervalo de fotogramas clave (KeyFrameInterval), también denominado grupo de imágenes, GOP, que se recibe del codificador en vivo. Para obtener más información, consulte [Trabajo con el streaming en vivo de Servicios multimedia de Azure].
 
###Actualizaciones del SDK .NET de Servicios multimedia

- Ahora la versión del [SDK .NET de Servicios multimedia de Azure](http://www.nuget.org/packages/windowsazure.mediaservices/) es la 3.1.0.0.
- Se ha actualizado la dependencia del SDK de .Net a .NET Framework 4.5.
- Se agregó una nueva API que permite actualizar unidades reservadas de codificación. Para obtener más información, consulte [Actualización del tipo de unidad reservada y aumento de las unidades reservadas de codificación mediante .NET](http://msdn.microsoft.com/library/azure/jj129582.aspx).
- Se agregó compatibilidad con JWT (token web de JSON) para la autenticación de token. Para obtener más información, consulte [JWT token Authentication in Azure Media Services and Dynamic Encryption](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/) (Autenticación de token JWD en Servicios multimedia y cifrado dinámico de Azure).
- Se agregaron desplazamientos relativos para BeginDate y ExpirationDate en la plantilla de licencia de PlayReady.


##<a id="november_changes_14"></a>Versión de noviembre de 2014

- Servicios multimedia permite introducir un contenido de Smooth Streaming (FMP4) en vivo en una conexión SSL. Para introducir en SSL, asegúrese de actualizar la dirección URL de introducción a HTTPS. Para obtener más información acerca del streaming en vivo, consulte [Trabajo con el streaming en vivo de Servicios multimedia de Azure].
- Tenga en cuenta que, en la actualidad, no se puede introducir un stream en vivo RTMP por una conexión SSL.
- También puede transmitir el contenido por una conexión SSL. Para ello, asegúrese de que las URL de streaming comienzan por HTTPS.
- Tenga en cuenta que solo puede transmitir por SSL si se creó el extremo de streaming desde el que se entrega el contenido a partir del 10 de septiembre de 2014. Si las direcciones URL de streaming se basan en los extremos de streaming creados después del 10 de septiembre, la dirección URL contendrá "streaming.mediaservices.windows.net" (el formato nuevo). Las direcciones URL de streaming que contengan "origin.mediaservices.windows.net" (el formato anterior) no son compatibles con SSL. Si la dirección URL tiene un formato antiguo y desea poder transmitir a través de SSL, [cree un extremo de streaming nuevo](media-services-manage-origins.md). Utilice direcciones URL creadas en función del nuevo extremo de streaming para transmitir el contenido a través de SSL.
   
##<a id="october_changes_14"></a>Versión de octubre de 2014

### <a id="new_encoder_release"></a>Versión del codificador de Servicios multimedia

Anuncio de la nueva versión del codificador de Servicios multimedia de Azure. Con la última versión de Codificador multimedia de Azure solo se cobran los GB de salida, pero, por lo demás, el nuevo codificador es una característica compatible con el anterior codificador. Para obtener más información, consulte [Detalles de precios de Servicios multimedia].

### <a id="oct_sdk"></a>SDK .NET de Servicios multimedia 

La versión de las extensiones del SDK de Servicios multimedia para .NET es ahora la 2.0.0.3.

La versión del SDK de Servicios multimedia para .NET es ahora la 3.0.0.8.

Se han realizado los siguientes cambios:

- Refactorización de clases de directivas de reintento.
- Adición de una cadena de agente de usuario a los encabezados de solicitud http.
- Adición del paso de compilación de restauración de nuget.
- Corrección de pruebas del escenario para utilizar el certificado x509 del repositorio.
- Validación de la configuración al actualizar el canal y extremo de streaming.
 

### Nuevo repositorio de GitHub para hospedar las muestras de Servicios multimedia

Hay ejemplos en el [Azure Media Services samples GitHub repository](https://github.com/Azure/Azure-Media-Services-Samples) (repositorio de GitHub de ejemplo de Servicios multimedia de Azure).


##<a id="september_changes_14"></a>Versión de septiembre de 2014

La versión de los metadatos de REST de Servicios multimedia es ahora la 2.7. Para obtener más información sobre las últimas actualizaciones de REST, consulte [Referencia de la API de REST de Servicios multimedia de Azure].

El SDK de Servicios multimedia para .NET es ahora la versión 3.0.0.7.
 
### <a id="sept_14_breaking_changes"></a>Cambios importantes

* El nombre del **Origen** ha cambiado a [StreamingEndpoint].
* Un cambio en el comportamiento predeterminado al usar el **Portal de Azure clásico** para codificar y después publicar archivos MP4.

Anteriormente, cuando se usaba el Portal de Azure clásico para publicar un activo de vídeo de un solo archivo MP4, se creaba una dirección URL de SAS (las direcciones URL de SAS le permiten descargar el vídeo de un almacenamiento de blobs). Ahora, cuando usa el Portal de Azure clásico para codificar y luego publicar un activo de vídeo de un solo archivo MP4, la dirección URL generada apunta a un punto de conexión de streaming de Servicios multimedia de Azure. Este cambio no afecta a los vídeos MP4 que se cargan directamente en Servicios multimedia y se publican sin ser codificados por Servicios multimedia de Azure.

Actualmente, cuenta con las dos opciones siguientes para resolver el problema.

* Habilitar las unidades de streaming y usar el empaquetado dinámico para transmitir por secuencias el activo de .mp4 como una presentación Smooth Streaming.

* Crear una URL de SAS para descargar (o reproducir de forma progresiva) el .mp4. Para obtener más información sobre cómo crear un localizador de SAS, consulte [Entrega de contenido].


### <a id="sept_14_GA_changes"></a>Nuevas características/escenarios que forman parte de la versión GA

* **Procesador multimedia del indizador**. Para obtener más información, consulte [Indización de archivos multimedia con Azure Media Indexer].

* La entidad [StreamingEndpoint] ahora permite agregar nombres de dominio personalizados (host).

	Para poder usar un nombre de dominio personalizado como nombre del extremo de streaming de Servicios multimedia, debe agregar nombres de host personalizados a su extremo de streaming. Use las API de REST de Servicios multimedia o el SDK .NET para agregar nombres de host personalizados.
	
	Se aplican las siguientes consideraciones:
	
	* Debe tener la propiedad del nombre de dominio personalizado.
	
	* Y esta propiedad se debe validar mediante Servicios multimedia de Azure. Para validar el dominio, cree un CName que asigne <MediaServicesAccountId>.<parent domain> a verifydns.<mediaservices-dns-zone>.
	
	* Debe crear otro CName que asigne el nombre de host personalizado (por ejemplo, sports.contoso.com) al nombre de host de su StreamingEndpoint de Servicios multimedia (por ejemplo, amstest.streaming.mediaservices.windows.net).


	Para obtener más información, consulte la propiedad **CustomHostNames** en el tema [StreamingEndpoint].

### <a id="sept_14_preview_changes"></a>Nuevas características/escenarios que forman parte de la versión de vista previa pública

* Vista previa de Live Streaming. Para obtener más información, consulte [Trabajo con el streaming en vivo de Servicios multimedia de Azure].

* Servicio de entrega de claves. Para obtener más información, consulte [Uso del cifrado dinámico AES-128 y del servicio de entrega de claves].

* Cifrado dinámico AES. Para obtener más información, consulte [Uso del cifrado dinámico AES-128 y del servicio de entrega de claves].

* Servicio de entrega de licencias PlayReady. Para obtener más información, consulte [Uso del cifrado dinámico de PlayReady y servicio de entrega de licencias].

* Cifrado dinámico PlayReady. Para obtener más información, consulte [Uso del cifrado dinámico de PlayReady y servicio de entrega de licencias].

* Plantilla de licencias PlayReady de Servicios multimedia. Para obtener más información, consulte [Información general de plantillas de licencias de PlayReady de Servicios multimedia].

* Activos cifrados de almacenamiento de streaming. Para obtener más información, consulte [Streaming de contenido cifrado de almacenamiento].

##<a id="august_changes_14"></a>Versión de agosto de 2014

Cuando se codifica un activo, se produce un activo de salida tras la finalización del trabajo de codificación. Hasta esta versión, el codificador de Servicios multimedia de Azure producía metadatos sobre activos de salida. A partir de esta versión, el codificador también produce metadatos sobre activos de entrada. Para obtener más información, consulte los temas [Metadatos de entrada] y [Metadatos de salida].


##<a id="july_changes_14"></a>Versión de julio de 2014

Se han corregido los siguientes errores para Azure Media Services Packager y Encryptor:

* Solo se reproduce el audio cuando se transmite un activo de un archivo en vivo a HTTP Live Streaming: este error se ha corregido y ahora se reproduce tanto el audio como el vídeo.

* Al empaquetar un activo con HTTP Live Streaming y cifrado de sobre AES de 128 bits, las secuencias empaquetadas no se reproducen en dispositivos Android: este error se ha corregido y la secuencia empaquetada se reproduce en dispositivos Android compatibles con HTTP Live Streaming.

##<a id="may_changes_14"></a>Versión de mayo de 2014

### <a id="may_14_changes"></a>Actualizaciones generales de Servicios multimedia

Ahora puede usar el [empaquetado dinámico] para transmitir HTTP Live Streaming (HLS) v3. Para transmitir HLS v3, agregue el siguiente formato a la ruta del localizador de origen: *.ism/manifest(format=m3u8-aapl-v3). Para obtener más información, consulte el [blog de Nick Drouin].

El empaquetado dinámico admite ahora también la entrega de HLS (v3 y v4) cifrado con PlayReady basado en Smooth Streaming cifrado estáticamente con PlayReady. Para obtener información sobre cómo cifrar Smooth Streaming con PlayReady, consulte [Protección de streaming con velocidad de transmisión adaptable con PlayReady].

### <a name="may_14_donnet_changes"></a>Actualizaciones del SDK .NET de Servicios multimedia

Las siguientes mejoras se incluyen en la versión 3.0.0.5 del SDK .NET de Servicios multimedia:

* Mayor velocidad y resistencia para cargar/descargar activos multimedia.

* Mejoras en la gestión de excepciones transitorias y la lógica de reintento:

	* La detección de errores transitorios y la lógica de reintento se han mejorado en las excepciones ocasionadas al consultar, guardar cambios y cargar y descargar archivos. 
	
	* Al recibir excepciones web (por ejemplo, durante una solicitud de token de ACS), observará que ahora se producen con mayor rapidez errores graves.

Para obtener más información, consulte Lógica de reintento en el [SDK de Servicios multimedia para .NET].

##<a id="april_changes_14"></a>Versión del codificador de abril de 2014

### <a name="april_14_enocer_changes"></a>Actualizaciones del codificador de Servicios multimedia

* Se ha agregado compatibilidad con la ingesta de archivos AVI creados con el editor no lineal EDIUS de Grass Valley, donde el vídeo está comprimido ligeramente mediante el códec HQ/HQX de Grass Valley. Para obtener más información, consulte [Grass Valley anuncia EDIUS 7 Streaming a través de la nube].

* Se ha agregado compatibilidad con la especificación de la convención de nomenclatura para los archivos producidos por el codificador multimedia. Para obtener más información, consulte [Control de nombres de archivo de salida del codificador de Servicios multimedia].

* Se ha agregado compatibilidad con superposiciones de vídeo y/o audio. Para obtener más información, consulte [Creación de superposiciones].

* Se ha agregado compatibilidad con la unión de varios segmentos de vídeo. Para obtener más información, consulte [Unión de segmentos de vídeo].

* Se ha corregido un error relacionado con la transcodificación de MP4 en el que el audio se ha codificado con la capa 3 de nivel de audio de MPEG-1 (conocido como MP3).


##<a id="jan_feb_changes_14"></a>Versiones de enero/febrero de 2014

### <a name="jan_fab_14_donnet_changes"></a>SDK .NET de Servicios multimedia de Azure 3.0.0.1, 3.0.0.2 y 3.0.0.3

Los cambios en 3.0.0.1 y 3.0.0.2 incluyen los siguientes:

* Se han corregido los problemas relacionados con el uso de consultas LINQ con instrucciones OrderBy.

* Se han dividido las soluciones de prueba de [GitHub] en pruebas basadas en unidades y pruebas basadas en escenarios.

Para obtener más detalles acerca de los cambios, consulte [SDK .NET de Servicios multimedia de Azure 3.0.0.1 y 3.0.0.2].

Se han realizado los siguientes cambios en la versión 3.0.0.3:

* Se han actualizado las dependencias de almacenamiento de Azure para usar la versión 3.0.3.0. 

* Se ha corregido el problema de compatibilidad con versiones posteriores en las versiones 3.0.*.*.


##<a id="december_changes_13"></a>Versión de diciembre de 2013

### <a name="dec_13_donnet_changes"></a>SDK .NET de Servicios multimedia de Azure 3.0.0.0

>[AZURE.NOTE] Las versiones 3.0.x.x no son compatibles con las versiones 2.4.x.x.

La última versión del SDK de Servicios multimedia es ahora la 3.0.0.0. Puede descargar el último paquete de Nuget u obtener los bits de [GitHub].

A partir de la versión 3.0.0.0 del SDK de Servicios multimedia, puede reutilizar los tokens de [Servicio de control de acceso de Active Directory (ACS) de Azure]. Para obtener más información, consulte la sección "Reutilización de tokens de Access Control Service" del tema [Conexión con Servicios multimedia con el SDK de Servicios multimedia para .NET].

### <a name="dec_13_donnet_ext_changes"></a>Extensiones del SDK .NET de Servicios multimedia de Azure 2.0.0.0

Las extensiones del SDK .NET de Servicios multimedia de Azure son un conjunto de métodos de extensión y funciones auxiliares que simplificarán su código y facilitarán el desarrollo con Servicios multimedia de Azure. Puede obtener los bits más recientes en [Extensiones del SDK .NET de Servicios multimedia de Azure].

##<a id="november_changes_13"></a>Versión de noviembre de 2013

### <a name="nov_13_donnet_changes"></a>Cambios en el SDK .NET de Servicios multimedia de Azure

A partir de esta versión, el SDK de Servicios multimedia para .NET gestiona los errores transitorios que se pueden producir al realizar llamadas a la capa API de REST de Servicios multimedia.

##<a id="august_changes_13"></a>Versión de agosto de 2013

### <a name="aug_13_powershell_changes"></a>Cmdlets de PowerShell de Servicios multimedia incluidos en las herramientas del SDK de Azure

Los siguientes cmdlets de PowerShell de Servicios multimedia se incluyen ahora en [azure-sdk-tools].

* Get-AzureMediaServices 

	Por ejemplo: `Get-AzureMediaServicesAccount`.

* New-AzureMediaServicesAccount

	Por ejemplo: `New-AzureMediaServicesAccount -Name “MediaAccountName” -Location “Region” -StorageAccountName “StorageAccountName”`.

* New-AzureMediaServicesKey

	Por ejemplo: `New-AzureMediaServicesKey -Name “MediaAccountName” -KeyType Secondary -Force`.

* Remove-AzureMediaServicesAccount

	Por ejemplo: `Remove-AzureMediaServicesAccount -Name “MediaAccountName” -Force`.

##<a id="june_changes_13"></a>Versión de junio de 2013

### <a name="june_13_general_changes"></a>Cambios en Servicios multimedia de Azure

Los cambios mencionados en esta sección son actualizaciones incluidas en las versiones de Servicios multimedia de 2013.

* Posibilidad de vincular varias cuentas de almacenamiento a una cuenta de Servicios multimedia. 

	StorageAccount
	
	Asset.StorageAccountName and Asset.StorageAccount

* Posibilidad de actualizar Job.Priority.

* Entidades y propiedades relacionadas con notificaciones:

	JobNotificationSubscription
	
	NotificationEndPoint
	
	Trabajo

* Asset.Uri

* Locator.Name

### <a name="june_13_dotnet_changes"></a>Cambios en el SDK .NET de Servicios multimedia de Azure

Los siguientes cambios se incluyen en las versiones del SDK de Servicios multimedia de junio de 2013. El último SDK de Servicios multimedia está disponible en GitHub.

* A partir de la versión 2.3.0.0, el SDK de Servicios multimedia admite la vinculación de varias cuentas de almacenamiento a una cuenta de Servicios multimedia. Las siguientes API admiten esta característica:
	
	El tipo IStorageAccount.
	
	La propiedad Microsoft.WindowsAzure.MediaServices.Client.CloudMediaContext.StorageAccounts.
	
	La propiedad StorageAccount.
	
	La propiedad StorageAccountName.
	
	Para obtener más información, consulte [Administración de recursos de Servicios multimedia entre varias cuentas de almacenamiento].

* API relacionadas con notificaciones. A partir de la versión 2.2.0.0, tiene la posibilidad de escuchar notificaciones de almacenamiento de la cola de Azure. Para obtener más información, consulte [Control de notificaciones de trabajo de Servicios multimedia].
	
	La propiedad Microsoft.WindowsAzure.MediaServices.Client.IJob.JobNotificationSubscriptions.
	
	El tipo Microsoft.WindowsAzure.MediaServices.Client.INotificationEndPoint.
	
	El tipo Microsoft.WindowsAzure.MediaServices.Client.IJobNotificationSubscription.
	
	El tipo Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointCollection.
	
	El tipo Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointType.
	
	El tipo Microsoft.WindowsAzure.MediaServices.Client.NotificationJobState.

* Dependencia del SDK del cliente de almacenamiento de Azure 2.0 (Microsoft.WindowsAzure.StorageClient.dll).

* Dependencia de OData 5.5 (Microsoft.Data.OData.dll).


##<a id="december_changes_12"></a>Versión de diciembre de 2012

### <a name="dec_12_dotnet_changes"></a>Cambios en el SDK .NET de Servicios multimedia de Azure

* Intellisense: se ha agregado documentación de Intellisense que faltaba para muchos tipos.

* Microsoft.Practices.TransientFaultHandling.Core: se ha corregido un problema en el que el SDK tenía aún dependencia con una versión anterior de este ensamblado. El SDK hace referencia ahora a la versión 5.1.1209.1 de este ensamblado.

Correcciones de problemas encontrados en el SDK de noviembre de 2012:

* IAsset.Locators.Count: este recuento se notifica ahora correctamente en las nuevas interfaces IAsset después de que todos los localizadores se hayan eliminado.

* IAssetFile.ContentFileSize; este valor está ahora correctamente definido después de una carga por IAssetFile.Upload(filepath).

* IAssetFile.ContentFileSize: esta propiedad se puede establecer ahora al crear un archivo de activo. Anteriormente era de solo lectura.

* IAssetFile.Upload(filepath): se ha corregido un problema en el que el método de carga sincrónico mostraba el siguiente error al cargar varios archivos en el activo. El error era "El servidor no pudo autenticar la solicitud. Asegúrese de que el valor del encabezado de autenticación tenga el formato correcto, incluida la firma".

* IAssetFile.UploadAsync: se ha corregido un problema por el que no más de 5 archivos se podían cargar a la vez.

* AssetFile.UploadProgressChanged: este evento se proporciona ahora en el SDK.

* IAssetFile.DownloadAsync(string, BlobTransferClient, ILocator, CancellationToken): ahora se proporciona esta sobrecarga del método.

* IAssetFile.DownloadAsync: se ha corregido un problema en el que no más de 5 archivos se podían descargar a la vez.

* IAssetFile.Delete(): se ha corregido un problema en el que al llamar al método delete se puede producir una excepción si no se ha cargado ningún archivo para IAssetFile.

* Jobs: se ha corregido un problema en el que al encadenar una "tarea de MP4 a transmisiones por secuencias suaves" con una "tarea de protección de PlayReady" mediante una plantilla de trabajo, no se creaba ninguna tarea.

* EncryptionUtils.GetCertificateFromStore(): este método ya no produce una excepción de referencia nula debido a errores de búsqueda del certificado basados en problemas de configuración del certificado.

##<a id="november_changes_12"></a>Versión de noviembre de 2012

Los cambios mencionados en esta sección eran actualizaciones incluidas en el SDK de noviembre de 2012 (versión 2.0.0.0). Estos cambios pueden requerir la modificación o reescritura de cualquier código escrito para la versión del SDK de vista previa de junio de 2012.

* Recursos
	
	IAsset.Create(assetName) es la ÚNICA función de creación de activos. IAsset.Create ya no carga archivos como parte de la llamada al método. Use IAssetFile para cargar.
	
	El método IAsset.Publish y el valor de enumeración AssetState.Publish se han eliminado del SDK de servicios. Todo código que dependa de este valor se debe reescribir.

* FileInfo

	Esta clase se ha eliminado y se ha sustituido por IAssetFile.

	IAssetFiles

	IAssetFile sustituye a FileInfo y tiene un comportamiento diferente. Para usarla, cree una instancia del objeto IAssetFiles, seguida de una carga de archivos mediante el SDK de Servicios multimedia o el SDK de almacenamiento de Azure. Se pueden usar las siguientes sobrecargas de IAssetFile.Upload:

	* IAssetFile.Upload(filePath): un método sincrónico que bloquea el subproceso y solo se recomienda al cargar un único archivo.
	
	* IAssetFile.UploadAsync(filePath, blobTransferClient, locator, cancellationToken): un método asincrónico. Este es el mecanismo de carga preferido.

	Error conocido: mediante cancellationToken se cancelará realmente la carga; sin embargo, el estado de cancelación de las tareas puede ser cualquiera de una serie de estados. Debe capturar y gestionar correctamente las excepciones.

* Localizadores
	
	Las versiones específicas del origen se han eliminado. El context.Locators.CreateSasLocator(asset, accessPolicy) específico de SAS se marcará como obsoleto o se eliminará de la disponibilidad general. Consulte la sección Localizadores en Nuevas funcionalidades del comportamiento actualizado.


##<a id="june_changes_12"></a>Versión de vista previa de junio de 2012

La siguiente funcionalidad era nueva en la versión de noviembre del SDK.

* Eliminación de entidades

	Los objetos IAsset, IAssetFile, ILocator, IAccessPolicy y IContentKey se han eliminado ahora a nivel de objeto, es decir, IObject.Delete(), en lugar de exigir una eliminación en la colección, que es cloudMediaContext.ObjCollection.Delete(objInstance).

* Localizadores

	Los localizadores se deben crear ahora usando el método CreateLocator y se deben usar los valores de enumeración LocatorType.SAS o LocatorType.OnDemandOrigin como argumento para el tipo específico de localizador que desea crear.

	Se han agregado nuevas propiedades a los localizadores para que sea más fácil obtener URL utilizables para su contenido. Este rediseño de los localizadores tenía como fin proporcionar una mayor flexibilidad para la futura extensibilidad de terceros y aumentar la facilidad de uso de las aplicaciones cliente multimedia.

* Compatibilidad con el método asincrónico

	Se ha agregado compatibilidad asincrónica a todos los métodos.


##Rutas de aprendizaje de Servicios multimedia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Envío de comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[foro de MSDN de Servicios multimedia de Azure]: http://social.msdn.microsoft.com/forums/azure/home?forum=MediaServices
[Referencia de la API de REST de Servicios multimedia de Azure]: http://msdn.microsoft.com/library/azure/hh973617.aspx
[Detalles de precios de Servicios multimedia]: http://azure.microsoft.com/pricing/details/media-services/
[Metadatos de entrada]: http://msdn.microsoft.com/library/azure/dn783120.aspx
[Metadatos de salida]: http://msdn.microsoft.com/library/azure/dn783217.aspx
[Entrega de contenido]: http://msdn.microsoft.com/library/azure/hh973618.aspx
[Indización de archivos multimedia con Azure Media Indexer]: http://msdn.microsoft.com/library/azure/dn783455.aspx
[StreamingEndpoint]: http://msdn.microsoft.com/library/azure/dn783468.aspx
[Trabajo con el streaming en vivo de Servicios multimedia de Azure]: http://msdn.microsoft.com/library/azure/dn783466.aspx
[Uso del cifrado dinámico AES-128 y del servicio de entrega de claves]: http://msdn.microsoft.com/library/azure/dn783457.aspx
[Uso del cifrado dinámico de PlayReady y servicio de entrega de licencias]: http://msdn.microsoft.com/library/azure/dn783467.aspx
[Preview features]: http://azure.microsoft.com/services/preview/
[Información general de plantillas de licencias de PlayReady de Servicios multimedia]: http://msdn.microsoft.com/library/azure/dn783459.aspx
[Streaming de contenido cifrado de almacenamiento]: http://msdn.microsoft.com/library/azure/dn783451.aspx
[Azure Classic Portal]: https://manage.windowsazure.com
[empaquetado dinámico]: http://msdn.microsoft.com/library/azure/jj889436.aspx
[blog de Nick Drouin]: http://blog-ndrouin.azurewebsites.net/hls-v3-new-old-thing/
[Protección de streaming con velocidad de transmisión adaptable con PlayReady]: http://msdn.microsoft.com/library/azure/dn189154.aspx
[SDK de Servicios multimedia para .NET]: http://msdn.microsoft.com/library/azure/dn745650.aspx
[Grass Valley anuncia EDIUS 7 Streaming a través de la nube]: http://www.streamingmedia.com/Producer/Articles/ReadArticle.aspx?ArticleID=96351&utm_source=dlvr.it&utm_medium=twitter
[Control de nombres de archivo de salida del codificador de Servicios multimedia]: http://msdn.microsoft.com/library/azure/dn303341.aspx
[Creación de superposiciones]: http://msdn.microsoft.com/library/azure/dn640496.aspx
[Unión de segmentos de vídeo]: http://msdn.microsoft.com/library/azure/dn640504.aspx
[SDK .NET de Servicios multimedia de Azure 3.0.0.1 y 3.0.0.2]: http://www.gtrifonov.com/2014/02/07/windows-azure-media-services-.net-sdk-3.0.0.2-release/
[Servicio de control de acceso de Active Directory (ACS) de Azure]: http://msdn.microsoft.com/library/hh147631.aspx
[Conexión con Servicios multimedia con el SDK de Servicios multimedia para .NET]: http://msdn.microsoft.com/library/azure/jj129571.aspx
[Extensiones del SDK .NET de Servicios multimedia de Azure]: https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev
[azure-sdk-tools]: https://github.com/Azure/azure-sdk-tools
[GitHub]: https://github.com/Azure/azure-sdk-for-media-services
[Administración de recursos de Servicios multimedia entre varias cuentas de almacenamiento]: http://msdn.microsoft.com/library/azure/dn271889.aspx
[Control de notificaciones de trabajo de Servicios multimedia]: http://msdn.microsoft.com/library/azure/dn261241.aspx
 

<!---HONumber=AcomDC_0302_2016-->