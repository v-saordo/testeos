<properties 
	pageTitle="Entrega de transmisión en directo con Servicios multimedia de Azure" 
	description="Este tema ofrece información general de los principales componentes que intervienen en el streaming en vivo." 
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


#Entrega de eventos de streaming en vivo con Servicios multimedia de Azure

##Información general

Cuando se trabaja con streaming en vivo, normalmente participan los siguientes componentes:

- Una cámara que se usa para difundir un evento.
- Un codificador de vídeo en directo que convierte las señales de la cámara en secuencias que se envían a un servicio de streaming en vivo. 
  
	Opcionalmente, varios codificadores en directo. Para determinados eventos en directo críticos que demandan una disponibilidad y una calidad de experiencia muy altas, se recomienda emplear codificadores redundantes activo-activo para lograr una conmutación por error perfecta sin pérdida de datos.
- Un servicio de streaming en vivo que permita hacer lo siguiente: 
	- Recopilar contenido en directo mediante varios protocolos de streaming en vivo (por ejemplo RTMP o Smooth Streaming). 
	- Codificar la secuencia en una secuencia con velocidad de bits adaptable.
	- Mostrar una vista previa de la secuencia en vivo.
	- Almacenar el contenido recibido para transmitirlo posteriormente (vídeo bajo demanda).
	- Entregar el contenido mediante protocolos de streaming comunes (por ejemplo, MPEG DASH, Smooth, HLS, HDS) directamente a sus clientes o a una red de entrega de contenido (CDN) para ampliar la distribución. 
	
		
**Servicios multimedia de Microsoft Azure** (AMS) permite recopilar, codificar, mostrar una vista previa, almacenar y entregar el contenido de streaming en vivo.

Al entregar contenido a sus clientes, el objetivo es entregar un vídeo de alta calidad a diversos dispositivos en condiciones de red diferentes. Para abordar la calidad y las condiciones de la red, use codificadores en directo para codificar la secuencia en una secuencia de vídeo de velocidad de bits múltiple (velocidad de bits adaptativa). Para abordar el streaming en diferentes dispositivos, use el [empaquetado dinámico](media-services-dynamic-packaging-overview.md) de Servicios multimedia para volver a empaquetar dinámicamente su secuencia para distintos protocolos. Los Servicios multimedia admiten la entrega de las siguientes tecnologías de transmisión de velocidad de bits adaptativa: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH y HDS (solo para licenciatarios de Adobe PrimeTime/Access).

En Servicios multimedia de Azure, los **canales**, **programas** y **extremos de streaming** controlan todas las funcionalidades de streaming en vivo, incluidas la recopilación, el formato, DVR, la seguridad, la escalabilidad y la redundancia.

Un **canal** representa una canalización para procesar contenido de streaming en vivo. Actualmente, un canal puede recibir secuencias de entrada en directo de la siguiente manera:


- Un codificador en directo local envía una secuencia de una sola velocidad de bits al canal que está habilitado para realizar codificación en directo con Servicios multimedia, con uno de los siguientes formatos: RTP (MPEG-TS), RTMP o Smooth Streaming (MP4 fragmentado). Después, el canal codifica en directo la secuencia entrante de una sola velocidad de bits en una secuencia de vídeo de varias velocidades de bits (adaptable). Cuando se solicita, Servicios multimedia entrega la secuencia a los clientes.

- Un codificador en directo local envía contenido **RTMP** o **Smooth Streaming** (MP4 fragmentado) con varias velocidades de bits al canal. Puede usar los siguientes codificadores en directo que generan Smooth Streaming de varias velocidades de bits: Elemental, Envivio y Cisco. Los siguientes codificadores en directo generan RTMP: transcodificadores Tricaster, Telestream Wirecast y Adobe Flash Live. Las secuencias recopiladas pasan a través de **canales** sin más procesamiento. El codificador en directo también puede enviar una secuencia de una sola velocidad de bits a un canal que no está habilitado para la codificación en directo, pero esto no es recomendable. Cuando se solicita, Servicios multimedia entrega la secuencia a los clientes.


##Uso de canales habilitados para realizar la codificación en directo con Servicios multimedia de Azure


El diagrama siguiente muestra las partes principales de la plataforma AMS que intervienen en el flujo de trabajo de streaming en vivo, en la que se habilita un canal para realizar la codificación en directo con Servicios multimedia.

![Flujo de trabajo activo][live-overview1]

Para obtener más información, vea [Uso de canales habilitados para realizar la codificación en directo con Servicios multimedia de Azure](media-services-manage-live-encoder-enabled-channels.md).


##Uso de canales que reciben streaming en vivo con velocidad de bits múltiple de codificadores locales


En el diagrama siguiente se muestran las partes principales de la plataforma AMS que intervienen en el flujo de trabajo de streaming en vivo.

![Flujo de trabajo activo][live-overview2]

Para obtener más información, consulte [Uso de canales que reciben streaming en vivo con velocidad de bits múltiple de codificadores locales](media-services-manage-channels-overview.md).



##Rutas de aprendizaje de Servicios multimedia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Envío de comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##Temas relacionados

[Conceptos de Servicios multimedia de Azure](media-services-concepts.md)

[Especificación de la introducción en directo de MP4 fragmentado de Servicios multimedia de Azure](media-services-fmp4-live-ingest-overview.md)





[live-overview1]: ./media/media-services-live-streaming-workflow/media-services-live-streaming-new.png

[live-overview2]: ./media/media-services-live-streaming-workflow/media-services-live-streaming-current.png
 

<!---HONumber=AcomDC_0211_2016-->