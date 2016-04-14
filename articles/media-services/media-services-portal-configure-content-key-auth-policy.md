<properties 
	pageTitle="Configuración de la directiva de autorización de claves de contenido mediante el Portal" 
	description="Aprenda a configurar una directiva de autorización para una clave de contenido." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
 	ms.date="02/03/2016"  
	ms.author="juliako"/>



#Configuración de la directiva de autorización de claves de contenido 
[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../../includes/media-services-selector-content-key-auth-policy.md)]


##Información general

Los Servicios multimedia de Microsoft Azure le permiten entregar el contenido cifrado (de forma dinámica) con Estándar de cifrado avanzado (AES) (mediante claves de cifrado de 128 bits) y PlayReady DRM. Servicios multimedia también proporciona un **servicio de entrega de claves/licencias** desde el que los clientes pueden obtener una clave o una licencia para reproducir el contenido cifrado.

Este tema muestra cómo usar el **Portal de Azure clásico** para configurar la directiva de autorización de claves de contenido. La clave se puede usar posteriormente para cifrar el contenido de forma dinámica. Tenga en cuenta que actualmente puede cifrar los formatos de streaming siguientes: HLS, MPEG DASH y Smooth Streaming. No puede cifrar el formato de streaming HDS ni descargas progresivas.
 
Cuando un reproductor solicita una secuencia que se establece para cifrarse de forma dinámica, los Servicios multimedia usan la clave configurada para cifrar dinámicamente el contenido mediante cifrado AES o PlayReady. Para descifrar la secuencia, el reproductor solicitará la clave del servicio de entrega de claves. Para decidir si el usuario está o no autorizado para obtener la clave, el servicio evalúa las directivas de autorización que especificó para la clave.


Si planea tener varias claves de contenido o desea especificar una dirección URL del **servicio de entrega de claves/licencias** que no sea el servicio de entrega de claves de Servicios multimedia, use el SDK de Servicios multimedia para .NET o las API de REST.

[Configuración de la directiva de autorización de claves mediante el SDK de Servicios multimedia para .NET](media-services-dotnet-configure-content-key-auth-policy.md)

[Configuración de la directiva de autorización de claves mediante la API de REST de Servicios multimedia](media-services-rest-configure-content-key-auth-policy.md)

###Se aplican algunas consideraciones:

- Para poder usar el empaquetado dinámico y el cifrado dinámico, debe asegurarse de tener al menos una unidad reservada de streaming. Para obtener más información, consulte [Escalación de un servicio multimedia](media-services-manage-origins.md#scale_streaming_endpoints). 
- El recurso debe contener un conjunto de archivos MP4 de velocidad de bits adaptable o archivos Smooth Streaming de velocidad de bits adaptable. Para obtener más información, consulte [Codificación de un recurso](media-services-encode-asset.md).  
- El servicio de entrega de claves almacena en caché ContentKeyAuthorizationPolicy y sus objetos relacionados (opciones y restricciones de directiva) durante 15 minutos. Si crea una entidad ContentKeyAuthorizationPolicy y especifica el uso de una restricción "Token", pruébela y, a continuación, actualice la directiva a la restricción "Open"; la directiva tardará aproximadamente 15 minutos antes de cambiar a la versión "Open" de la misma.


##Procedimiento: configuración de la directiva de autorización de claves

Para configurar la directiva de autorización de claves, seleccione la página **PROTECCIÓN DE CONTENIDO**.
	
Servicios multimedia admite varias formas de autenticar a los usuarios que realizan solicitudes de clave. La directiva de autorización de claves de contenido puede tener restricciones de autorización **open**, **token** o **IP** (**IP** puede configurarse con REST o el SDK de .NET).

###Restricción open

La restricción **open** significa que el sistema entregará la clave a cualquier persona que realice una solicitud de clave. Esta restricción puede ser útil para realizar pruebas.

![OpenPolicy][open_policy]

###Restricción de token

Para elegir la directiva de token restringida, presione el botón **TOKEN**.

La directiva con restricción de **token** debe ir acompañada de un token emitido por un **Servicio de tokens seguros** (STS). Servicios multimedia admite tokens en formato **Token de web simple** ([SWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)) y en formato **Token de web JSON** (JWT). Para obtener información, consulte [Autenticación de token JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).

Servicios multimedia no proporciona **Servicios de tokens seguros**. Puede crear un STS personalizado o aprovechar el Servicio de control de acceso (ACS) de Microsoft Azure para emitir tokens. Se debe configurar el STS para crear un token firmado con las notificaciones de clave y emisión que especificó en la configuración de restricción de tokens. El servicio de entrega de claves de los Servicios multimedia devolverá la clave de cifrado al cliente si el token es válido y las notificaciones del token coinciden con las configuradas para la clave de contenido. Para obtener más información, consulte [Uso de Azure ACS para emitir tokens](http://mingfeiy.com/acs-with-key-services).

Al configurar la directiva con restricción **TOKEN**, debe establecer los valores de **clave de verificación**, **emisor** y **público**. La clave de comprobación principal contiene la clave con la que se firmó el token y el emisor es el servicio de tokens seguros que emite el token. El público (a veces denominado ámbito) describe la intención del token o del recurso cuyo acceso está autorizado por el token. El servicio de entrega de claves de los Servicios multimedia valida que estos valores del token coincidan con los valores de la plantilla.

###PlayReady

Al proteger su contenido con **PlayReady**, una de las cosas que debe especificar en la directiva de autorización es una cadena XML que defina la plantilla de licencia de PlayReady. De forma predeterminada, se establece la siguiente directiva:
		
	<PlayReadyLicenseResponseTemplate xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1">
	  <LicenseTemplates>
	    <PlayReadyLicenseTemplate><AllowTestDevices>true</AllowTestDevices>
	      <ContentKey i:type="ContentEncryptionKeyFromHeader" />
	      <LicenseType>Nonpersistent</LicenseType>
	      <PlayRight>
	        <AllowPassingVideoContentToUnknownOutput>Allowed</AllowPassingVideoContentToUnknownOutput>
	      </PlayRight>
	    </PlayReadyLicenseTemplate>
	  </LicenseTemplates>
	</PlayReadyLicenseResponseTemplate>

Puede hacer clic en el botón **Importar directiva xml** y proporcionar un archivo XML diferente que se ajuste al esquema XML definido [aquí](https://msdn.microsoft.com/library/azure/dn783459.aspx).


##Rutas de aprendizaje de Servicios multimedia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Envío de comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##Pasos siguientes
Ahora que ha configurado la directiva de autorización de claves de contenido, consulte el tema [Uso del Portal de Azure clásico para habilitar el cifrado](../media-services-manage-content#encrypt/).


[open_policy]: ./media/media-services-portal-configure-content-key-auth-policy/media-services-protect-content-with-open-restriction.png
[token_policy]: ./media/media-services-key-authorization-policy/media-services-protect-content-with-token-restriction.png

 

<!---HONumber=AcomDC_0211_2016-->