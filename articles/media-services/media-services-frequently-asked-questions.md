<properties 
	pageTitle="Preguntas más frecuentes" 
	description="Preguntas más frecuentes (P+F)" 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
 	ms.date="03/01/2016"  
	ms.author="juliako"/>


#Preguntas más frecuentes  

##Información general

P: ¿Cómo se escala la indización?

R: las unidades reservadas son las mismas para las tareas de codificación y de indización. Siga las instrucciones de [Escalación de unidades reservadas de codificación](media-services-how-to-scale.md) **Tenga en cuenta** que el rendimiento del indizador no se ve afectado por tipo de unidad reservada.

P: He cargado, codificado y publicado un vídeo. ¿Cuál es el motivo por el que el vídeo no se reproduce cuando intento transmitirlo?

R: uno de los motivos más habituales es que no dispone de al menos una unidad de streaming reservada asignada en el extremo de streaming desde el que está intentando reproducir. Siga las instrucciones de [Escalación de unidades reservadas de streaming](media-services-how-to-scale.md)

P: ¿Puedo realizar una composición en una secuencia en directo?

R: no se ofrece actualmente la composición en secuencias en vivo en Servicios multimedia de Azure, por lo que tendrá que realizar una composición previa en el equipo.

P: ¿Puedo usar CDN de Azure con secuencia en directo?

R: servicios multimedia admite la integración con CDN de Azure (para obtener más información, consulte [Administración de extremos de streaming en una cuenta de Servicios multimedia](media-services-manage-origins.md#enable_cdn)). Puede usar el streaming en vivo con CDN. Servicios multimedia de Azure proporciona salidas de Smooth Streaming, HLS y MPEG-DASH. Todos estos formatos usan HTTP para transferir datos y obtener beneficios del almacenamiento en caché de HTTP. En la transmisión en vivo, los datos de audio/vídeo reales se dividen en fragmentos y los fragmentos individuales se almacenan en caché en CDN. Solo tienen que actualizarse los datos de manifiesto. CDN actualiza periódicamente los datos de manifiesto.

P: ¿Los servicios multimedia de Azure admiten el almacenamiento de imágenes?

R: si solo busca almacenar las imágenes JPEG o PNG, debe mantenerlas en Almacenamiento de blobs de Azure. No supone una ventaja si se colocan en la cuenta de Servicios multimedia a menos que desee mantenerlas asociadas a los recursos de vídeo o audio. O bien, si es posible que tenga que usar las imágenes como superposiciones en el codificador de vídeo. Codificador de Servicios multimedia es compatible con imágenes superpuestas en la parte superior de los vídeos, lo que hace que JPEG y PNG sean formatos de entrada compatibles. Para obtener más información, consulte [Creación de superposiciones](https://msdn.microsoft.com/library/azure/dn640496.aspx).

P: ¿Cómo puedo copiar recursos de una cuenta de Servicios multimedia a otra?

R: para copiar los recursos de una cuenta de Servicios multimedia a otro, use el método de extensión [IAsset.Copy](https://github.com/Azure/azure-sdk-for-media-services-extensions/blob/dev/MediaServices.Client.Extensions/IAssetExtensions.cs#L354) disponible en el repositorio [Extensiones del SDK de Servicios multimedia de Azure para .NET](https://github.com/Azure/azure-sdk-for-media-services-extensions/). Para obtener más información, consulte [esta](https://social.msdn.microsoft.com/Forums/azure/28912d5d-6733-41c1-b27d-5d5dff2695ca/migrate-media-services-across-subscription?forum=MediaServices) conversación del foro.

P: ¿Cómo puedo girar un vídeo durante el proceso de codificación?

R: [Media Encoder Estándar](media-services-dotnet-encode-with-media-encoder-standard.md) admite ángulos de rotación de 90, 180 o 270. El comportamiento predeterminado es "Auto", en el que se intentan detectar los metadatos de rotación del archivo MP4 o MOV entrante y la compensación. Incluya el siguiente elemento **Sources** en uno de los valores preestablecidos JSON definidos [aquí](http://msdn.microsoft.com/library/azure/mt269960.aspx):
	
	"Version": 1.0,
	"Sources": [
	{
	  "Streams": [],
	  "Filters": {
	    "Rotation": "90"
	  }
	}
	],
	"Codecs": [
	
	...

##Rutas de aprendizaje de Servicios multimedia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Envío de comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0302_2016-->