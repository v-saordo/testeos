<properties 
	pageTitle="Información general y comparación de codificadores multimedia a petición de Azure" 
	description="En este tema se proporciona información general y una comparación de los codificadores multimedia a petición de Azure." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
 	ms.date="02/25/2016"  
	ms.author="juliako"/>

#Información general y comparación de codificadores multimedia a petición de Azure

##Información general sobre la codificación

Servicios multimedia de Azure ofrece varias opciones para la codificación de medios en la nube.

Cuando comience con Servicios multimedia, es importante comprender la diferencia entre códecs y formatos de archivo. Los códecs son el software que implementa los algoritmos de compresión/descompresión, mientras que los formatos de archivo son contenedores que contienen el vídeo comprimido.

Servicios multimedia proporciona paquetes dinámicos que permiten entregar contenido codificado MP4 de velocidad de bits adaptable o Smooth Streaming en formatos admitidos por Servicios multimedia (MPEG DASH, HLS, Smooth Streaming, HDS) sin tener que volver a empaquetar en estos formatos de streaming.

Para aprovecharse de los [paquetes dinámicos](media-services-dynamic-packaging-overview.md), deberá hacer lo siguiente:

- Codifique su archivo intermedio (origen) en un conjunto de archivos MP4 de velocidad de bits adaptable o archivos Smooth Streaming de velocidad de bits adaptable (los pasos de codificación se muestran más adelante en este tutorial).
- Obtenga al menos la unidad de streaming a petición para el extremo de streaming desde el que planea entregar el contenido. Para obtener más información, consulte [Escalación de unidades reservadas de streaming a petición](media-services-manage-origins.md#scale_streaming_endpoints/).

Servicios multimedia admite los siguientes codificadores a petición que se describen en este artículo:

- [Media Encoder Estándar](media-services-encode-asset.md#media-encoder-standard)
- [Flujo de trabajo del Codificador multimedia](media-services-encode-asset.md#media-encoder-premium-workflow)

En este artículo se ofrece una breve introducción a los codificadores multimedia a petición y se proporcionan vínculos a artículos con información más detallada. También se proporciona una comparación de los codificadores.

Tenga en cuenta que, de forma predeterminada, cada cuenta de Servicios multimedia puede tener una tarea de codificación activa a la vez. Puede reservar unidades de codificación que permiten la ejecución simultánea de varias tareas de codificación, una para cada unidad reservada de codificación que ha adquirido. Para obtener información, consulte [Escalado de unidades de codificación](media-services-portal-encoding-units.md).

##Media Encoder Estándar

###Modo de uso

[Codificación con Codificador multimedia estándar](media-services-dotnet-encode-with-media-encoder-standard.md)

###Formatos

[Códecs y formatos](media-services-media-encoder-standard-formats.md)

###Valores preestablecidos

Codificador multimedia estándar se configura mediante uno de los valores preestablecidos descritos [aquí](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

###Metadatos de entrada y salida

[Aquí](http://msdn.microsoft.com/library/azure/dn783120.aspx) se describen los metadatos de entrada de los codificadores.

[Aquí](http://msdn.microsoft.com/library/azure/dn783217.aspx) se describen los metadatos de salida de los codificadores.

###Generación de miniaturas

Para más información, consulte [Generación de miniaturas](media-services-custom-mes-presets-with-dotnet.md#thumbnails).

###Recorte de vídeos

Para más información, consulte [Recorte de un vídeo](media-services-custom-mes-presets-with-dotnet.md#trim_video).

###Creación de superposiciones

Para más información, consulte [Creación de una superposición](media-services-custom-mes-presets-with-dotnet.md#overlay).

###Consulte también

[El blog de Servicios multimedia](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/)
 
##Flujo de trabajo del Codificador multimedia

###Información general

[Introducción de la codificación Premium en Servicios multimedia de Azure](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/)

###Modo de uso

El flujo de trabajo del Codificador multimedia Premium se configura mediante flujos de trabajo complejos. Los archivos de flujo de trabajo pueden crearse y actualizarse con la herramienta [Diseñador de flujo de trabajo](media-services-workflow-designer.md).

[Uso de la codificación Premium en Servicios multimedia de Azure](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services/)

###Problemas conocidos

Si el vídeo de entrada no contiene subtítulos, el recurso de salida seguirá conteniendo un archivo TTML vacío.


##<a id="compare_encoders"></a>Comparación de codificadores

###<a id="billing"></a>Medidor de facturación usado por cada codificador

Nombre de procesador multimedia|Precios aplicables|Notas
---|---|---
**Media Encoder Estándar** |ENCODER|Las tareas de codificación se cobrarán en función del tamaño de los recursos de salida, en GB, a la velocidad especificada [aquí][1], bajo la columna CODIFICADOR.
**Flujo de trabajo del Codificador multimedia** |CODIFICADOR PREMIUM|Las tareas de codificación se cobrarán en función del tamaño de los activos de salida, en GB, a la velocidad especificada [aquí][1], bajo la columna CODIFICADOR PREMIUM.


En esta sección se comparan las funciones de codificación de **Codificador multimedia estándar** y **Flujo de trabajo Premium de Codificador multimedia**.


###Formatos de archivo/contenedor de entrada

Formatos de archivo/contenedor de entrada|Media Encoder Estándar|Flujo de trabajo del Codificador multimedia
---|---|---
Adobe® Flash® F4V |Sí|Sí
MXF/SMPTE 377M |Sí|Sí
GXF |Sí|Sí
Secuencias de transporte MPEG-2 |Sí|Sí
Secuencias de programa MPEG-2 |Sí|Sí
MPEG-4/MP4 |Sí|Sí
Windows Media/ASF |Sí|Sí
AVI (sin comprimir de 8 bits/10 bits)|Sí|Sí
3GPP/3GPP2 |Sí|No
Formato de archivo de streaming con velocidad de transmisión adaptable (PIFF 1.3)|Sí|No
[Microsoft Digital Video Recording(DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984)|Sí|No
Matroska/WebM |Sí|No
QuickTime (.mov) |Sí|No

###Códecs de vídeo de entrada

Códecs de vídeo de entrada|Media Encoder Estándar|Flujo de trabajo del Codificador multimedia
---|---|---
AVC 8 bits/10 bits, hasta 4:2:2, incluido AVCIntra |8 bits: 4:2:0 y 4:2:2|Sí
Avid DNxHD (en MXF) |Sí|Sí
DVCPro/DVCProHD (en MXF) |Sí|Sí
JPEG2000 |Sí|Sí
MPEG-2 (hasta 422 Perfil y Nivel alto; incluidas variantes como XDCAM, XDCAM HD, XDCAM IMX, CableLabs® y D10)|Hasta 422 Perfil|Sí
MPEG-1 |Sí|Sí
Windows Media Video/VC-1 |Sí|Sí
Canopus HQ/HQX |No|No
MPEG-4, parte 2 |Sí|No
[Theora](https://en.wikipedia.org/wiki/Theora) |Sí|No
Apple ProRes 422 |Sí|No
Apple ProRes 422 LT |Sí|No
Apple ProRes 422 HQ |Sí|No
Apple ProRes Proxy|Sí|No
Apple ProRes 4444 |Sí|No
Apple ProRes 4444 XQ |Sí|No

###Códecs de audio de entrada

Códecs de audio de entrada|Media Encoder Estándar|Flujo de trabajo del Codificador multimedia
---|---|---
AES (SMPTE 331M y 302M, AES3-2003) |No|Sí
Dolby® E |No|Sí
Dolby® Digital (AC3) |No|Sí
Dolby® Digital Plus (E-AC3) |No|Sí
AAC (AAC-LC, AAC-HE y AAC-HEv2; hasta 5.1)|Sí|Sí
MPEG Layer 2|Sí|Sí
MP3 (MPEG-1 Audio Layer 3)|Sí|Sí
Windows Media Audio|Sí|Sí
WAV/PCM|Sí|Sí
[FLAC](https://en.wikipedia.org/wiki/FLAC)</a>|Sí|No
[Opus](https://en.wikipedia.org/wiki/Opus_(audio_format) |Sí|No
[Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a>|Sí|No


###Formatos de archivo/contenedor de salida

Formatos de archivo/contenedor de salida|Media Encoder Estándar|Flujo de trabajo del Codificador multimedia
---|---|---
Adobe® Flash® F4V|No|Sí
MXF (OP1a, XDCAM y AS02)|No|Sí
DPP (incluido AS11)|No|Sí
GXF|No|Sí
MPEG-4/MP4|Sí|Sí
MPEG-TS|Sí|Sí
Windows Media/ASF|No|Sí
AVI (sin comprimir de 8 bits/10 bits)|No|Sí
Formato de archivo de streaming con velocidad de transmisión adaptable (PIFF 1.3)|No|Sí

###Códecs de vídeo de salida

Códecs de vídeo de salida|Media Encoder Estándar|Flujo de trabajo del Codificador multimedia
---|---|---
AVC (H.264; 8 bits; hasta Perfil alto, Nivel 5.2; 4K Ultra HD; AVC Intra)|Solo 8 bits 4:2:0|Sí
Avid DNxHD (en MXF)|No|Sí
DVCPro/DVCProHD (en MXF)|No|Sí
MPEG-2 (hasta 422 Perfil y Nivel alto; incluidas variantes como XDCAM, XDCAM HD, XDCAM IMX, CableLabs® y D10)|No|Sí
MPEG-1|No|Sí
Windows Media Video/VC-1|No|Sí
Creación de miniaturas JPEG|No|Sí

###Códecs de audio de salida

Códecs de audio de salida|Media Encoder Estándar|Flujo de trabajo del Codificador multimedia
---|---|---
AES (SMPTE 331M y 302M, AES3-2003)|No|Sí
Dolby® Digital (AC3)|No|Sí
Dolby® Digital Plus (E-AC3) hasta 7.1|No|Sí
AAC (AAC-LC, AAC-HE y AAC-HEv2; hasta 5.1)|Sí|Sí
MPEG Layer 2|No|Sí
MP3 (MPEG-1 Audio Layer 3)|No|Sí
Windows Media Audio|No|Sí


##Códigos de error  

En la tabla siguiente se indican los códigos de error que se podrían devolver en caso de que se encuentre un error durante la ejecución de la tarea de codificación. Para obtener detalles del error en el código .NET, use la clase [ErrorDetails](http://msdn.microsoft.com/library/microsoft.windowsazure.mediaservices.client.errordetail.aspx). Para obtener detalles del error en el código REST, use la API de REST [ErrorDetail](https://msdn.microsoft.com/library/jj853026.aspx).

ErrorDetail.Code|Posibles causas de error
-----|-----------------------
Desconocido| Error desconocido al ejecutar la tarea
ErrorDownloadingInputAssetMalformedContent|Categoría de errores en la que se incluyen errores en la descarga de recursos de entrada, como nombres de archivo no válidos, archivos de longitud cero, formatos incorrectos, etc.
ErrorDownloadingInputAssetServiceFailure|Categoría de errores en la que se incluyen problemas en el servicio, por ejemplo, errores de red o de almacenamiento durante la descarga.
ErrorParsingConfiguration|Categoría de errores en la que la tarea <see cref="MediaTask.PrivateData"/> (configuración) no es válida; por ejemplo, la configuración no es un valor preestablecido del sistema válido o contiene XML no válido.
ErrorExecutingTaskMalformedContent|Categoría de errores durante la ejecución de la tarea en la que problemas en los archivos multimedia de entrada provocan errores.
ErrorExecutingTaskUnsupportedFormat|Categoría de errores en la que el procesador de contenido multimedia no puede procesar los archivos proporcionados: formato de contenido multimedia no compatible o que no coincide con la configuración. Por ejemplo, intentar producir una salida de solo audio desde un activo que tenga solo vídeo.
ErrorProcessingTask|Categoría de otros errores que detecta el procesador de contenido multimedia durante el procesamiento de la tarea y que no están relacionados con el contenido.
ErrorUploadingOutputAsset|Categoría de errores al cargar el activo de salida.
ErrorCancelingTask|Categoría de errores en la que se incluyen errores al intentar cancelar la tarea.
TransientError|Categoría de errores en la que se incluyen problemas transitorios (p. ej., problemas de red temporales con Almacenamiento de Azure).


Para obtener ayuda del equipo de **Servicios multimedia**, abra una [incidencia de soporte técnico](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).



##Rutas de aprendizaje de Servicios multimedia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Envío de comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##Artículos relacionados

- [Realización de tareas de codificación avanzadas mediante la personalización de valores preestablecidos de Media Encoder Estándar](media-services-custom-mes-presets-with-dotnet.md)
- [Cuotas y limitaciones](media-services-quotas-and-limitations.md)

 
<!--Reference links in article-->
[1]: http://azure.microsoft.com/pricing/details/media-services/

<!---HONumber=AcomDC_0302_2016-->