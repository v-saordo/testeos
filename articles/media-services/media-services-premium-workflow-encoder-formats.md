<properties 
	pageTitle="Códecs y formatos de flujo de trabajo del Codificador multimedia Premium" 
	description="En este tema se proporciona información general de códecs y formatos de formatos de flujo de trabajo del Codificador multimedia Premium" 
	services="media-services" 
	documentationCenter="" 
	authors="juliako,anilmur" 
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

#Códecs y formatos de flujo de trabajo del Codificador multimedia Premium


>[AZURE.NOTE]Si tiene preguntas sobre el codificador premium, envíe un correo a mepd en Microsoft.com.
>
>El procesador multimedia del flujo de trabajo premium del Codificador multimedia premium del que se habla en este tema no está disponible en China.

Este documento contiene una lista de los formatos de archivo de entrada y salida y los códecs que son compatibles con la vista previa pública del **Flujo de trabajo del Codificador multimedia premium**.

[Códecs y formatos de entrada del flujo de trabajo del Codificador multimedia Premium](#input_formats)

[Códecs y formatos de salida del flujo de trabajo del Codificador multimedia Premium](#output_formats)

El **flujo de trabajo del Codificador multimedia Premium** admite los subtítulos que se describen en [esta](#closed_captioning) sección.


##<a id="input_formats"></a>Códecs y formatos de entrada de flujo de trabajo del Codificador multimedia Premium

En la sección siguiente se enumeran los códecs y formatos de archivo que admite este procesador multimedia como entrada.

###Formatos de archivo/contenedor de entrada

- Adobe® Flash® F4V
- MXF/SMPTE 377M
- GXF
- Secuencias de transporte MPEG-2
- Secuencias de programa MPEG-2
- MPEG-4/MP4
- Windows Media/ASF
- AVI (sin comprimir de 8 bits/10 bits)

###Códecs de vídeo de entrada

- AVC 8 bits/10 bits, hasta 4:2:2, incluido AVCIntra
- Avid DNxHD (en MXF)
- DVCPro/DVCProHD (en MXF)
- JPEG2000
- MPEG-2 (hasta 422 Perfil y Nivel alto; incluidas variantes como XDCAM, XDCAM HD, XDCAM IMX, CableLabs® y D10)
- MPEG-1
- Windows Media Video/VC-1

###Códecs de audio de entrada

- AES (SMPTE 331M y 302M, AES3-2003)
- Dolby® E
- Dolby® Digital (AC3)
- AAC (AAC-LC, AAC-HE y AAC-HEv2; hasta 5.1)
- MPEG Layer 2
- MP3 (MPEG-1 Audio Layer 3)
- Windows Media Audio
- WAV/PCM
 
##<a id="output_format"></a>Códecs y formatos de salida de flujo de trabajo del Codificador multimedia Premium

En la sección siguiente se enumeran los códecs y formatos de archivo que se admiten como salida de este procesador multimedia.

###Formatos de archivo/contenedor de salida

- Adobe® Flash® F4V
- MXF (OP1a, XDCAM y AS02)
- DPP (incluido AS11)
- GXF
- MPEG-4/MP4
- Windows Media/ASF
- AVI (sin comprimir de 8 bits/10 bits)
- Formato de archivo de streaming con velocidad de transmisión adaptable (PIFF 1.3)
- MPEG-TS 


###Códecs de vídeo de salida

- AVC (H.264; 8 bits; hasta Perfil alto, Nivel 5.2; 4K Ultra HD; AVC Intra)
- Avid DNxHD (en MXF)
- DVCPro/DVCProHD (en MXF)
- MPEG-2 (hasta 422 Perfil y Nivel alto; incluidas variantes como XDCAM, XDCAM HD, XDCAM IMX, CableLabs® y D10)
- MPEG-1
- Windows Media Video/VC-1
- Creación de miniaturas JPEG

###Códecs de audio de salida

- AES (SMPTE 331M y 302M, AES3-2003)
- Dolby® Digital (AC3)
- Dolby® Digital Plus (E-AC3) hasta 7.1
- AAC (AAC-LC, AAC-HE y AAC-HEv2; hasta 5.1)
- MPEG Layer 2
- MP3 (MPEG-1 Audio Layer 3)
- Windows Media Audio

##<a id="closed_captioning"></a>Compatibilidad con subtítulos

En la entrada, el **flujo de trabajo del Codificador multimedia Premium** admite:

1. Archivos SCC
1. Archivos SMPTE-TT
1. CEA-608/CEA-708: llevados como datos de usuario (mensajes SEI de secuencias elementales H.264, ATSC/53 y SCTE20) o llevdosa como datos auxiliares en archivos MXF/GXF
1. Archivos de subtítulos STL

En la salida, están disponibles las siguientes opciones:

1. Traducción de CEA-608 a CEA-708
1. Transferencia CEA-608/CEA-708 (incrustado en mensajes SEI de secuencias elementales de H.264 o transportado como datos auxiliares en archivos MXF)
1. SCC
1. Texto sincronizado SMPTE (de origen CEA-608 por SMPTE RP2052; incluida la creación de archivos DFXP)
1. Archivo de subtítulos SRT
1. Secuencias de subtítulos DVB

Nota: no todos los formatos de salida anteriores se admiten para la entrega mediante streaming en Servicios multimedia de Azure.

##Problemas conocidos

Si el vídeo de entrada no contiene subtítulos, el recurso de salida seguirá conteniendo un archivo TTML vacío.


##Rutas de aprendizaje de Servicios multimedia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Envío de comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0211_2016-->