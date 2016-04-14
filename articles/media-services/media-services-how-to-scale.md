<properties 
	pageTitle="Escalación de un Servicio multimedia" 
	description="Aprenda a escalar Servicios multimedia mediante la especificación del número de unidades reservadas de streaming a petición y unidades reservadas de codificación con las que desea aprovisionar la cuenta." 
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


#Escalación de un Servicio multimedia  

##Información general

Puede escalar **Servicios multimedia** mediante la especificación del número de **unidades reservadas de streaming** y **unidades reservadas de codificación** con las que desea aprovisionar la cuenta.

También puede escalar la cuenta de Servicios multimedia agregándole cuentas de almacenamiento. Cada cuenta de almacenamiento está limitada a 500 TB. Para ampliar el almacenamiento más allá del límite predeterminado, puede asociar varias cuentas de almacenamiento a una sola cuenta de Servicios multimedia.

Este tema contiene vínculos a temas relevantes.

##<a id="streaming_endpoins"></a>Unidades reservadas de streaming

Para obtener más información, consulte [Escalado de unidades de streaming](media-services-manage-origins.md#scale_streaming_endpoints)

##<a id="encoding_reserved_units"></a>Unidades reservadas de codificación

Para obtener información acerca de cómo ampliar unidades de codificación, consulte los siguientes temas **Portal** y **.NET**.

[AZURE.INCLUDE [media-services-selector-scale-encoding-units](../../includes/media-services-selector-scale-encoding-units.md)]

Tenga en cuenta que las unidades reservadas son las mismas para las tareas de codificación y de indización.

##<a id="storage"></a>Almacenamiento de escala

Para obtener más información, consulte [Administración de recursos de Servicios multimedia entre varias cuentas de almacenamiento](https://msdn.microsoft.com/library/azure/dn271889.aspx) y [Trabajo con almacenamiento de Azure](https://msdn.microsoft.com/library/azure/dn767951.aspx).

##Rutas de aprendizaje de Servicios multimedia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Envío de comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0211_2016-->