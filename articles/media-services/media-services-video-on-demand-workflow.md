<properties 
	pageTitle="Entrega de multimedia a petición con Servicios multimedia de Azure" 
	description="En este tema se describen escenarios comunes de entrega de contenido multimedia con Servicios multimedia de Azure." 
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


#Entrega de multimedia a petición con Servicios multimedia de Azure

##Información general

En este tema se describen los pasos de un flujo de trabajo típico de vídeo bajo demanda de Servicios multimedia de Azure (AMS). Cada paso se vincula a temas relevantes. En cuanto a las tareas que se pueden lograr con distintas tecnologías, hay botones que vinculan a la tecnología que usted elija (por ejemplo, .NET o REST).

Tenga en cuenta que puede integrar Servicios multimedia con los procesos y herramientas existentes. Por ejemplo, codifique el contenido in situ y, a continuación, cárguelo en Servicios multimedia para realizar la transcodificación en varios formatos y entregue a través de CDN de Azure o un CDN de otro proveedor.

En el diagrama siguiente se muestran las partes principales de la plataforma de Servicios multimedia que intervienen en el flujo de trabajo de vídeo bajo demanda.![Flujo de trabajo de VoD][vod-overview]

##<a id="vod_scenarios"></a>Escenarios comunes: Entrega de contenido multimedia bajo demanda

###Protección del contenido en almacenamiento y entrega de contenido multimedia en streaming sin cifrar

1. Cargue un archivo intermedio de alta calidad en un recurso.
	
	Se recomienda aplicar la opción de cifrado de almacenamiento a su recurso con el fin de proteger su contenido durante la carga y mientras se encuentra en reposo en el almacenamiento. 
1. Codifique en conjunto de archivos MP4 de velocidad de bits adaptable. 

	Se recomienda aplicar la opción de cifrado de almacenamiento al recurso de salida con el fin de proteger su contenido mientras se encuentra en reposo.
	
1. Configure la directiva de entrega de recursos(usada por paquetes dinámicos).
	
	Si el recurso tiene el almacenamiento cifrado, **asegúrese** de configurar la directiva de entrega de recursos.

1. Publique el recurso mediante la creación de un localizador bajo demanda.

	Asegúrese de tener al menos una unidad de streaming reservada en el extremo de streaming desde el que desea transmitir el contenido.

1. Trasmita el contenido publicado.

###Protección del contenido en el almacenamiento, entrega de soportes de streaming cifrados dinámicamente  

Para poder usar el cifrado dinámico, primero debe obtener al menos una unidad reservada de streaming en el extremo de streaming desde la que desea transmitir el contenido cifrado.

1. Cargue un archivo intermedio de alta calidad en un recurso. Aplique una opción de cifrado de almacenamiento al recurso.
1. Codifique en conjunto de archivos MP4 de velocidad de bits adaptable. Aplique una opción de cifrado de almacenamiento al recurso de salida.
1. Cree la clave de cifrado de contenido para el recurso que desee que se cifre dinámicamente durante la reproducción.
2. Configure la directiva de autorización de claves de contenido.
1. Configure la directiva de entrega de recursos (usada por el empaquetado y el cifrado dinámicos).
1. Publique el recurso mediante la creación de un localizador a petición.
1. Trasmita el contenido publicado. 

###de contenido

1. Cargue un archivo intermedio de alta calidad en un recurso.
1. Indice el contenido.

	El trabajo de indexación genera archivos que se pueden usar como Subtítulos (CC) en la reproducción de vídeo. También genera archivos que le permiten realizar búsquedas en vídeo y saltar a la ubicación exacta del vídeo.

1. Consuma contenido indizado.


###Entregar la descarga progresiva 

1. Cargue un archivo intermedio de alta calidad en un recurso.
1. Codifique en un solo MP4 o en un conjunto de archivos MP4 de velocidad de bits adaptable.
1. Publique el recurso creando un localizador OnDemand o SAS.

	Si utiliza el localizador OnDemand, asegúrese de tener al menos una unidad reservada de streaming en el extremo de streaming desde el que tiene pensado descargar contenido progresivamente.

	Si utiliza un localizador SAS, el contenido se descarga desde el almacenamiento de blobs de Azure. En este caso, no necesita tener unidades reservadas de streaming.
  
1. Descargue contenido de forma progresiva.

Este artículo contiene vínculos a temas que muestran cómo configurar su entorno de desarrollo y realizar las tareas mencionadas anteriormente.


##Conceptos

Para obtener información sobre los conceptos relacionados con la entrega de su contenido bajo demanda, vea [Conceptos sobre Servicios multimedia](media-services-concepts.md).

##Rutas de aprendizaje de Servicios multimedia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Envío de comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


[vod-overview]: ./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png
 

<!---HONumber=AcomDC_0211_2016-->