<properties 
	pageTitle="Uso de castLabs para entregar licencias de Widevine a Servicios multimedia de Azure" 
	description="En este artículo se describe cómo puede usar Servicios multimedia de Azure (AMS) para entregar una secuencia que se cifra dinámicamente por AMS con DRM tanto de PlayReady como Widevine. La licencia de PlayReady procede del servidor de licencias PlayReady de Servicios multimedia y la licencia de Widevine se entrega al servidor de licencias de castLabs." 
	services="media-services" 
	documentationCenter="" 
	authors="Mingfeiy,willzhan,Juliako" 
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


#Uso de castLabs para entregar licencias de Widevine a Servicios multimedia de Azure

> [AZURE.SELECTOR]
- [Axinom](media-services-axinom-integration.md)
- [castLabs](media-services-castlabs-integration.md)

##Información general

En este artículo se describe cómo puede usar Servicios multimedia de Azure (AMS) para entregar una secuencia que se cifra dinámicamente por AMS con DRM tanto de PlayReady como Widevine. La licencia de PlayReady procede del servidor de licencias de PlayReady de Servicios multimedia y la licencia de Widevine se entrega por el servidor de licencias **castLabs**.

Para la reproducción de contenido en streaming protegido por CENC (PlayReady o Widevine), puede usar [Reproductor multimedia de Azure](http://amsplayer.azurewebsites.net/azuremediaplayer.html). Consulte el [documento AMP](http://amp.azure.net/libs/amp/latest/docs/) para obtener información detallada.

En el siguiente diagrama se muestra una arquitectura de integración de castLabs y Servicios multimedia de Azure de alto nivel.

![integración](./media/media-services-castlabs-integration/media-services-castlabs-integration.png)

##Configuración de sistema típico

- El contenido multimedia se almacena en AMS.
- Los id. de clave de claves de contenido se almacenan en castLabs y AMS.
- Tanto castLabs como AMS tienen la autenticación de tokens integrada. En las secciones siguientes se analizan los tokens de autenticación. 
- Cuando un cliente solicita transmitir el vídeo, el contenido se cifra dinámicamente con **cifrado común** (CENC) y se empaqueta dinámicamente por AMS a Smooth Streaming y DASH. También ofrecemos cifrado de streaming elemental de PlayReady M2TS para el protocolo de streaming HLS.
- La licencia de PlayReady se recupera del servidor de licencias de AMS y la licencia de Widevine se recupera del servidor de licencias de castLabs. 
- El reproductor multimedia decide automáticamente qué licencia capturar basándose en la capacidad de la plataforma del cliente. 

##Generación de tokens de autenticación para obtener una licencia

Tanto castLabs como AMS admiten el formato de token JWT (token web JSON) usado para autorizar una licencia.

###Token JWT en AMS 

En la tabla siguiente se describen los tokens JWT en AMS.

Emisor|Cadena de emisor desde el servicio de tokens seguro (STS) elegido
---|---
Público|Cadena de audiencia del STS usado
Notificaciones|Un conjunto de notificaciones
NotBefore|Iniciar la validez del token
Expira|Finalizar la validez del token
SigningCredentials|La clave que se comparte entre el servidor de licencias de PlayReady, el servidor de licencias de castLabs y STS; podría ser una clave simétrica o asimétrica.

###Token JWT en castLabs

En la tabla siguiente se describen los tokens JWT en castLabs.

Nombre|Descripción
---|---
optData|Cadena JSON que contiene información sobre el usuario. 
crt|Cadena JSON que contiene información sobre el activo, su información de licencia y derechos de reproducción.
iat|La fecha y hora actual en la época.
jti|Identificador único sobre este token (cada token solo puede usarse una vez en el sistema castLabs).

##Configuración de soluciones de ejemplo 

La [solución de ejemplo](https://github.com/AzureMediaServicesSamples/CastlabsIntegration) consta de dos proyectos:

-	Una aplicación de consola que puede usarse para establecer restricciones de DRM en un activo ya introducido, tanto para PlayReady como para Widevine.
-	Una aplicación web que distribuye tokens, que podrían considerarse como una versión MUY SIMPLIFICADA de un STS.


Para usar la aplicación de consola:

1.	Cambie el archivo app.config para configurar las credenciales de AMS, las credenciales de castLabs, la configuración de STS y la clave compartida.
2.	Cargue un activo en AMS.
3.	Obtenga el UUID del recurso cargado y cambie la línea 32 del archivo Program.cs:

		 var objIAsset = _context.Assets.Where(x => x.Id == "nb:cid:UUID:dac53a5d-1500-80bd-b864-f1e4b62594cf").FirstOrDefault();

4.	Use un AssetId para asignar un nombre al activo del sistema castLabs (línea 44 del archivo Program.cs).

	Debe establecer AssetId para **castLabs**; debe ser una cadena alfanumérica única.

5.	Ejecute el programa.


Para usar la aplicación web (STS):

1.	Cambie el archivo web.config para configurar el id. de comerciante de castlabs, la configuración de STS y la clave compartida.
2.	Implemente en Sitios web de Azure.
3.	Vaya al sitio web.

##Reproducir un vídeo

Para reproducir un vídeo cifrado con cifrado común (PlayReady o Widevine), puede usar el [Reproductor multimedia de Azure](http://amsplayer.azurewebsites.net/azuremediaplayer.html). Cuando se ejecuta la aplicación de consola, se reflejan el id. de clave de contenido y la dirección URL del manifiesto.

1.	Abra una pestaña nueva e inicie su STS: http://[yourStsName].azurewebsites.net/api/token/assetid/[yourCastLabsAssetId]/contentkeyid/[thecontentkeyid].
2.	Vaya al [Reproductor multimedia de Azure](http://amsplayer.azurewebsites.net/azuremediaplayer.html).
3.	Pegue la dirección URL de streaming.
4.	Haga clic en la casilla **Opciones avanzadas**.
5.	En el menú desplegable **Protección**, seleccione PlayReady o Widevine.
6.	Pegue el token que obtuvo de su STS en el cuadro de texto Token. 
	
	El servidor de licencias de castLab no necesita el prefijo “Bearer=” delante del token. Por tanto, quítelo antes de enviar el token.
7.	Actualice el reproductor.
8.	El vídeo se debe estar reproduciendo.


##Rutas de aprendizaje de Servicios multimedia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Envío de comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0211_2016-->