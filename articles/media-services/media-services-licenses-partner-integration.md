<properties 
	pageTitle="Uso de partners para entregar licencias de Widevine a Servicios multimedia de Azure" 
	description="En este artículo se describe cómo puede usar Servicios multimedia de Azure (AMS) para entregar una secuencia que se cifra dinámicamente por AMS con DRM tanto de PlayReady como Widevine. La licencia de PlayReady procede del servidor de licencias PlayReady de Servicios multimedia y la licencia de Widevine se entrega al servidor de licencias de castLabs." 
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

#Uso de partners para entregar licencias de Widevine a Servicios multimedia de Azure

##Información general

Servicios multimedia de Microsoft Azure le permite entregar MPEG-DASH protegido con DRM de Widevine, que se cifra según la especificación de cifrado común (CENC).

A partir del SDK de Servicios multimedia para .NET versión 3.5.2, Servicios multimedia permite configurar la plantilla de licencia Widevine y obtener licencias de Widevine. También puede usar los siguientes asociados de AMS para ayudarle a entregar licencias de Widevine: [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/), [castLabs](http://castlabs.com/company/partners/azure/).

##castLabs

Puede usar [castLabs](http://castlabs.com/company/partners/azure/) para entregar licencias Widevine. Para obtener más información, consulte [Uso de castLabs para entregar licencias de DRM a Servicios multimedia de Azure](media-services-castlabs-integration.md).

##Axinom

Puede usar [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/) para entregar licencias Widevine. Para obtener más información, consulte [Uso de Axinom para entregar licencias de DRM a Servicios multimedia de Azure](media-services-axinom-integration.md).


##Rutas de aprendizaje de Servicios multimedia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Envío de comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##Consulte también

[Uso de cifrado dinámico común de PlayReady o Widevine.](media-services-protect-with-drm.md)

[Blog de Mingfei](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/)

<!---HONumber=AcomDC_0211_2016-->