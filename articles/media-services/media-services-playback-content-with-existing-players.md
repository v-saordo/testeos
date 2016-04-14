<properties 
	pageTitle="Reproducción del contenido" 
	description="En este tema se enumeran los reproductores existentes que puede usar para reproducir el contenido." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
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


#Reproducción de contenido con existentes

Servicios multimedia de Azure admite muchos formatos de streaming populares como Smooth Streaming, HTTP Live Streaming y MPEG-Dash. Este tema remite a reproductores existentes que puede usar para probar sus transmisiones.

>[AZURE.NOTE]Para reproducir contenido empaquetado o cifrado dinámicamente, asegúrese de obtener al menos una unidad de streaming para el extremo de streaming desde el que planea entregar el contenido. Para obtener información sobre el escalado de unidades de streaming, consulte: [Cómo escalar unidades de streaming](media-services-manage-origins.md#scale_streaming_endpoints).

###Reproductor de contenido de los Servicios multimedia del Portal de Azure clásico

El **Portal de Azure clásico** proporciona un reproductor de contenido que puede usar para probar el vídeo.

Haga clic en el vídeo deseado (asegúrese de que se ha [publicado](media-services-manage-content.md#publish)) y haga clic en el botón **Reproducir** situado en la parte inferior del portal.
 
Se aplican algunas consideraciones:

- El **REPRODUCTOR DE CONTENIDO DE SERVICIOS MULTIMEDIA** reproduce desde el extremo de streaming predeterminado. Si desea reproducir desde un extremo de streaming que no esté predeterminado, use otro reproductor. Por ejemplo, [Reproductor multimedia de Azure](http://amsplayer.azurewebsites.net/azuremediaplayer.html).
 

![AMSPlayer][AMSPlayer]

###Reproductor multimedia de Azure

Use el [Reproductor multimedia de Azure](http://amsplayer.azurewebsites.net/azuremediaplayer.html) para reproducir el contenido (libre o protegido) en cualquiera de los siguientes formatos:

- Smooth Streaming
- MPEG DASH
- HLS
- MP4 progresivo


###Flash Player

####Cifrado de AES con token 

[http://aestoken.azurewebsites.net]("http://aestoken.azurewebsites.net)

###Reproductores de Silverlight

####Supervisión

[http://smf.cloudapp.net/healthmonitor](http://smf.cloudapp.net/healthmonitor)

####PlayReady con token

[http://sltoken.azurewebsites.net](http://sltoken.azurewebsites.net)

### Reproductores DASH

[http://dashplayer.azurewebsites.net](http://dashplayer.azurewebsites.net)

[http://dashif.org](http://dashif.org)

###Otros

Para probar las direcciones URL de HLS también puede utilizar:

- **Safari** en un dispositivo iOS o
- **3ivx HLS Player** en Windows.

##Desarrollo de reproductores de vídeo

Para obtener información sobre cómo desarrollar sus propios reproductores, consulte [Desarrollo de reproductores de vídeo](media-services-develop-video-players.md)




##Rutas de aprendizaje de Servicios multimedia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Envío de comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

 
[AMSPlayer]: ./media/media-services-playback-content-with-existing-players/media-services-portal-player.png

<!---HONumber=AcomDC_0211_2016-->