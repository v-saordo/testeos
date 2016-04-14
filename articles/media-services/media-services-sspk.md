<properties 
	pageTitle="Licencias de Microsoft® Smooth Streaming Client Porting Kit" 
	description="Más información sobre cómo obtener licencias de Microsoft® Smooth Streaming Client Porting Kit." 
	services="media-services" 
	documentationCenter="" 
	authors="xpouyat,vsood" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/04/2016"  
	ms.author="xpouyat"/>

#Licencias de Microsoft® Smooth Streaming Client Porting Kit

##Información general

Microsoft Smooth Streaming Client Porting Kit (**SSPK** para abreviar) es una implementación del cliente de Smooth Streaming que está optimizada para ayudar a los fabricantes de dispositivos incrustados y los operadores de cable y dispositivos móviles, los proveedores de servicios de contenido, los fabricantes de auriculares, los fabricantes de software independientes (ISV) y los proveedores de soluciones para crear productos y servicios para contenido de streaming adaptable en formato de Smooth Streaming. SSPK es una implementación independiente del dispositivo y la plataforma del cliente de Smooth Streaming que el licenciatario puede portar a cualquier dispositivo y plataforma.

A continuación se incluye una arquitectura de alto nivel y el IIS Smooth Streaming Porting Kit es la implementación de Smooth Streaming Client proporcionada por Microsoft e incluye toda la lógica básica para la reproducción del contenido de Smooth Streaming. A continuación, los asociados los portan para un dispositivo o plataforma específicos mediante la implementación de interfaces adecuadas.

![SSPK](./media/media-services-sspk/sspk-arch.png)

##Descripción

La licencia de SSPK tiene unos términos que ofrecen un valor empresarial excelente. La licencia SSPK proporciona al sector:

- Código fuente de Smooth Streaming Porting Kit en C++ 
  - Implementa la funcionalidad Smooth Streaming Client
  - Agrega análisis de formato, heurística, lógica de almacenamiento en búfer, etc.
- API de aplicación del reproductor 
  -	Interfaces de programación para la interacción con una aplicación del Reproductor multimedia
- Interfaz de capa de abstracción de plataforma (PAL) 
  -	Interfaces de programación para la interacción con el sistema operativo (subprocesos, sockets)
- Interfaz de capa de abstracción de hardware (HAL) 
  -	Interfaces de programación para la interacción con el descodificadores de A/V de hardware (descodificación, representación)
- Interfaz de administración de derechos digitales (DRM) 
  -	Interfaces de programación para administrar DRM a través de la capa de abstracción de DRM (DAL)
  -	Microsoft PlayReady Porting Kit se distribuye por separado, pero se integra mediante esta interfaz. Para obtener más detalles acerca de las licencias de Microsoft PlayReady Device, haga clic en [aquí](http://www.microsoft.com/playready/licensing/device_technology.mspx#pddipdl).
- Ejemplos de implementación 
  -	Ejemplo de implementación de PAL para Linux
  -	Ejemplo de implementación de HAL para Linux

##Opciones de licencia

Microsoft Smooth Streaming Client Porting Kit está disponible para los licenciatarios con dos contratos de licencia distintos: uno para el desarrollo de productos provisional de Smooth Streaming Client Interim Products y otro para la distribución de Smooth Streaming Client Final Products a los usuarios finales.
 
- Para los fabricantes de conjuntos de chips, integradores de sistemas o fabricantes de software independientes (ISV) que requieren un kit de portabilidad de código fuente para desarrollar productos provisionales, debe ejecutarse una **licencia de productos provisionales** del Microsoft Smooth Streaming Client Porting Kit.
- Para los fabricantes de dispositivos o ISV que requieren derechos de distribución de Smooth Streaming Client Final Products a usuarios finales, debe ejecutarse una **licencia de productos finales** del Microsoft Smooth Streaming Client Porting Kit.

###Licencia de producto provisional de Microsoft Smooth Streaming Client

Con esta licencia, Microsoft ofrece un Smooth Streaming Client Porting Kit y los derechos de propiedad intelectual necesarios para desarrollar y distribuir Smooth Streaming Client Interim Products a otros licenciados de dispositivos del Smooth Streaming Client Porting Kit que distribuyan Smooth Streaming Client Final Products.

####Estructura de tarifas

Una cuota de licencia única de 50.000 USD proporciona acceso al Smooth Streaming Client Porting Kit.

###Licencia Microsoft Smooth Streaming Client Porting Kit Final Product

Con esta licencia, Microsoft ofrece todos los derechos de propiedad intelectual necesarios para recibir Smooth Streaming Client Interim Products de otros licenciados del Smooth Streaming Client Porting Kit y distribuir Smooth Streaming Client Final Products con marca de empresa a usuarios finales.

####Estructura de tarifas

La licencia Smooth Streaming Client Final Product se ofrece en un modelo de regalías como se indica a continuación:

- 0,10 USD por cada implementación de dispositivo enviada
- La regalía está limitada a 50.000 USD al año
- Sin regalía para las primeras 10.000 implementaciones de dispositivo cada año 

##Procedimiento de licencias y acceso al SSPK

Envíe un mensaje de correo electrónico [sspkinfo@microsoft.com](mailto:sspkinfo@microsoft.com) para cualquier consulta sobre todas las licencias.

Los licenciatarios de productos provisionales pueden acceder al [portal de distribución del SSPK](https://microsoft.sharepoint.com/teams/SSPKDOWNLOAD/).

Los licenciatarios de productos provisionales y finales del SSPK pueden enviar preguntas técnicas a [smoothpk@microsoft.com](mailto:smoothpk@microsoft.com).

##Licencias con contrato de producto provisional de Microsoft Smooth Streaming Client

- Adroit Business Solutions, Inc
- Advanced Digital Broadcast SA
- AirTies Kablosuz Iletism Sanayive Dis Ticaret A.S.
- Albis Technologies Ltd.
- Alticast Corporation
- Amazon Digital Services, Inc.
- Amlogic, Co., Ltd.
- AVC Multimedia Software Co., Ltd.
- EchoStar Purchasing Corporation
- Enseo, Inc.
- Guangdong OPPO Mobile Telecommunications Corp., Ltd.
- HANDAN BroadInfoCom Co., Ltd.
- Infomir GMBH
- Inside Secure
- Irdeto USA Inc.
- Liberty Global Services BV
- MediaTek Inc.
- MStar Co, Ltd
- Nintendo Co., Ltd.
- OpenTV, Inc.
- Research In Motion Limited
- Saffron Digital Limited
- Sichuan Changhong Electric Co., Ltd
- Tatung Technology Inc.
- Telechips Inc.
- Vestel Elektronik Sanayi ve Ticaret A.S.
- VisualOn, Inc.
- ZTE Corporation

##Licencias con contrato de producto final de Microsoft Smooth Streaming Client

- Advanced Digital Broadcast SA
- AirTies Kablosuz Iletism Sanayive Dis Ticaret A.S.
- Albis Technologies Ltd.
- Amazon Digital Services, Inc.
- AmTRAN Technology Co., Ltd.
- Arcadyan Technology Corporation
- ATMACA ELEKTRONİK SAN. VE TİC. A.Ş
- British Sky Broadcasting Limited
- CastPal Technology Inc., Shenzhen
- Compal Electronics, Inc.
- Dongguan Digital AV Technology Corp., Ltd.
- Enseo, Inc.
- Filmflex Movies Limited
- Guangdong OPPO Mobile Telecommunications Corp., Ltd.
- HANDAN BroadInfoCom Co., Ltd.
- Hisense International Co., Ltd
- Homecast Co.,Ltd
- Hon Hai Precision Industry Co., Ltd.
- Infomir GMBH
- Kaonmedia Co., Ltd.
- KDDI Corporation
- Nintendo Co., Ltd.
- Orange SA
- PIXELA Corporation
- Saffron Digital Limited
- Sagemcom Broadband SAS
- Sharp Corporation
- Shenzhen Coship Electronics CO., LTD
- Shenzhen Jiuzhou Electric Co.,Ltd
- Shenzhen Skyworth Digital Technology Co., Ltd
- Sichuan Changhong Electric Co., Ltd.
- Sky Deutschland Fernsehen GmbH & Co. KG
- SmarDTV S.A.
- SoftAtHome
- TCL Overseas Marketing (Macao Commercial Offshore) Limited
- Technicolor Delivery Technologies, SAS
- Toshiba Lifestyle Products & Services Corporation
- Virgin Media Limited
- VIZIO, Inc.
- Wistron Corporation
- WOOX Innovations Limited
- ZTE Corporation

##Rutas de aprendizaje de Servicios multimedia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Envío de comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0218_2016-->