<properties 
	pageTitle="Información general y escenarios comunes de Servicios multimedia de Azure" 
	description="En este tema se proporciona información general de Servicios multimedia de Azure." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako,anilmur" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="hero-article" 
	ms.date="02/02/2016"
	ms.author="juliako"/>

#Información general y escenarios comunes de Servicios multimedia de Azure

Servicios multimedia de Microsoft Azure es una plataforma extensible basada en la nube que permite a los desarrolladores crear aplicaciones de administración y entrega de contenido multimedia escalables. Servicios multimedia se basa en las API de REST, que permiten cargar, almacenar, codificar y empaquetar de forma segura contenido de audio o vídeo para la entrega bajo demanda y de streaming en vivo a varios clientes (por ejemplo, televisión, PC y dispositivos móviles).

Puede crear flujos de trabajo de un extremo a otro usando solamente Servicios multimedia. También puede usar componentes de terceros para algunas partes del flujo de trabajo. Por ejemplo, codifique mediante un codificador de terceros. A continuación, cargue, proteja, empaquete y entregue con Servicios multimedia.

Puede elegir entre transmisión del contenido en directo o entrega a petición. En este tema se muestran escenarios comunes para la entrega de contenido [en directo](media-services-overview.md#live_scenarios) o [a petición](media-services-overview.md#vod_scenarios). El tema también incluye vínculos a otros temas de interés.

## SDK y herramientas 

Para compilar soluciones de Servicios multimedia, puede usar:

- [API de REST de Servicios multimedia](https://msdn.microsoft.com/library/azure/hh973617.aspx)
- Uno de los SDK de cliente disponibles: 
	- [SDK de Servicios multimedia de Azure para .NET](https://github.com/Azure/azure-sdk-for-media-services) 
	- [SDK de Azure para Java](https://github.com/Azure/azure-sdk-for-java) 
	- [SDK de Azure para PHP](https://github.com/Azure/azure-sdk-for-php) 
	- [Servicios multimedia de Azure para Node.js](https://github.com/michelle-becker/node-ams-sdk/blob/master/lib/request.js) (es decir, una versión de un SDK de Node.js que no sea de Microsoft. Su mantenimiento corre a cargo de una comunidad y actualmente no tiene una cobertura del 100 % de las API de AMS). 
- Herramientas existentes: 
	- [Portal de Azure clásico](http://manage.windowsazure.com/) 
	- [Azure-Media-Services-Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (El Explorador de servicios multimedia de Azure (AMSE) es una aplicación Winforms/C# para Windows)

##Rutas de aprendizaje de Servicios multimedia

Puede ver las rutas de aprendizaje de Servicios multimedia de Azure aquí:

- [Flujo de trabajo de streaming en vivo de Servicios multimedia de Azure](https://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
- [Flujo de trabajo de streaming a petición de Servicios multimedia de Azure](https://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)

##Póster

[Aquí](https://azure.microsoft.com/documentation/infographics/media-services/) puede ver el póster de Servicios multimedia de Azure que representa los flujos de trabajo de AMS, desde la creación de medios hasta el consumo.

##Requisitos previos

Para empezar a usar Servicios multimedia de Azure, debe tener lo siguiente:
 
3. Una cuenta de Azure. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](azure.microsoft.com).
2. Una cuenta de Servicios multimedia de Azure. Use el Portal de Azure clásico, .NET o API de REST para crear la cuenta de Servicios multimedia de Azure. Para obtener más información, consulte [Creación de una cuenta](media-services-create-account.md).
3. (Opcional) Configurar el entorno de desarrollo. Elija .NET o API de REST para el entorno de desarrollo. Para obtener más información, consulte [Configuración del entorno](media-services-dotnet-how-to-use.md). 

	Además, aprenda a conectarse mediante programación [Conexión](media-services-dotnet-connect_programmatically.md).
4. (Recomendado) Asignar una o más unidades de escalado. Se recomienda asignar una o más unidades de escalado para las aplicaciones en el entorno de producción. Para obtener más información, vea [Administración de extremos de streaming](media-services-manage-origins.md).

##Introducción y conceptos

Para conocer los conceptos de Servicios multimedia de Azure, vea [Conceptos](media-services-concepts.md).

Para una serie de procedimientos en que se presentan todos los componentes principales de Servicios multimedia de Azure, vea los [tutoriales paso a paso de Servicios multimedia de Azure](https://docs.com/fukushima-shigeyuki/3439/english-azure-media-services-step-by-step-series). Esta serie tiene una excelente introducción de conceptos y utiliza la herramienta AMSE para demostrar las tareas AME. Tenga en cuenta que la herramienta AMSE es una herramienta de Windows. Esta herramienta admite la mayoría de las tareas que puede lograr mediante programación con el [SDK de AMS para .NET](https://github.com/Azure/azure-sdk-for-media-services), el [SDK de Azure para Java](https://github.com/Azure/azure-sdk-for-java) o el [SDK de Azure para PHP](https://github.com/Azure/azure-sdk-for-php).

##<a id="vod_scenarios"></a>Entrega de contenido multimedia a petición con Servicios multimedia de Azure: escenarios y tareas comunes

En esta sección se describe escenarios comunes y se proporcionan vínculos a temas importantes. En el diagrama siguiente se muestran las partes principales de la plataforma de Servicios multimedia que intervienen en la entrega de contenido a petición.

![Flujo de trabajo de VoD][vod-overview]


###Protección del contenido en almacenamiento y entrega de contenido multimedia en streaming sin cifrar

1. Cargue un archivo intermedio de alta calidad en un recurso.
	
	Se recomienda aplicar la opción de cifrado de almacenamiento a su recurso con el fin de proteger su contenido durante la carga y mientras se encuentra en reposo en el almacenamiento.
 
1. Codifique en conjunto de archivos MP4 de velocidades de bits adaptativas.

	Se recomienda aplicar la opción de cifrado de almacenamiento al recurso de salida con el fin de proteger su contenido mientras se encuentra en reposo.
	
1. Configure la directiva de entrega de recursos(usada por paquetes dinámicos).
	
	Si el recurso tiene el almacenamiento cifrado, **asegúrese** de configurar la directiva de entrega de recursos.

1. Publique el recurso mediante la creación de un localizador bajo demanda.

	Asegúrese de tener al menos una unidad de streaming reservada en el extremo de streaming desde el que desea transmitir el contenido.

1. Trasmita el contenido publicado.

###Protección del contenido en el almacenamiento, entrega de soportes de streaming cifrados dinámicamente  

Para poder usar el cifrado dinámico, primero debe obtener al menos una unidad reservada de streaming en el extremo de streaming desde la que desea transmitir el contenido cifrado.

1. Cargue un archivo intermedio de alta calidad en un recurso. Aplique una opción de cifrado de almacenamiento al recurso.
1. Codifique en conjunto de archivos MP4 de velocidades de bits adaptativas. Aplique una opción de cifrado de almacenamiento al recurso de salida.
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
1. Codifique en un solo archivo MP4.
1. Publique el recurso creando un localizador OnDemand o SAS.

	Si utiliza el localizador OnDemand, asegúrese de tener al menos una unidad reservada de streaming en el extremo de streaming desde el que tiene pensado descargar contenido progresivamente.

	Si utiliza un localizador SAS, el contenido se descarga desde el almacenamiento de blobs de Azure. En este caso, no necesita tener unidades reservadas de streaming.
  
1. Descargue contenido de forma progresiva.

###Vea también

- [Carga de contenido](media-services-manage-content.md#upload)
- [Obtención de un procesador multimedia](media-services-get-media-processor.md)
- [Codificación de contenido](media-services-manage-content.md#encode)
- [Supervisión de trabajos](media-services-portal-check-job-progress.md)
- [Indexación de contenido](media-services-manage-content.md#index)
- [Protección del contenido](media-services-manage-content.md#encrypt)
- [Protección de las publicaciones](media-services-manage-content.md#publish)
- [Escalación de la codificación](media-services-portal-encoding-units.md)

##<a id="live_scenarios"></a>Entrega de eventos de streaming en vivo con Servicios multimedia de Azure

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

	La codificación de secuencias en directo con Servicios multimedia está en **vista previa**.
- Un codificador en directo local envía contenido **RTMP** o **Smooth Streaming** (MP4 fragmentado) con varias velocidades de bits al canal. Puede usar los siguientes codificadores en directo que generan Smooth Streaming de varias velocidades de bits: Elemental, Envivio y Cisco. Los siguientes codificadores en directo generan RTMP: transcodificadores Tricaster, Telestream Wirecast y Adobe Flash Live. Las secuencias recopiladas pasan a través de **canales** sin más procesamiento. El codificador en directo también puede enviar una secuencia de una sola velocidad de bits a un canal que no está habilitado para la codificación en directo, pero esto no es recomendable. Cuando se solicita, Servicios multimedia entrega la secuencia a los clientes.


###Uso de canales habilitados para realizar la codificación en directo con Servicios multimedia de Azure


El diagrama siguiente muestra las partes principales de la plataforma AMS que intervienen en el flujo de trabajo de streaming en vivo, en la que se habilita un canal para realizar la codificación en directo con Servicios multimedia.

![Flujo de trabajo activo][live-overview1]

Para obtener más información, vea [Uso de canales habilitados para realizar la codificación en directo con Servicios multimedia de Azure](media-services-manage-live-encoder-enabled-channels.md).


###Uso de canales que reciben streaming en vivo con velocidad de bits múltiple de codificadores locales


En el diagrama siguiente se muestran las partes principales de la plataforma AMS que intervienen en el flujo de trabajo de streaming en vivo.

![Flujo de trabajo activo][live-overview2]

Para obtener más información, consulte [Uso de canales que reciben streaming en vivo con velocidad de bits múltiple de codificadores locales](media-services-manage-channels-overview.md).

##Consumo de contenido

Servicios multimedia de Azure proporciona las herramientas que necesita para crear aplicaciones cliente de reproductor enriquecidas y dinámicas para la mayoría de las plataformas, como dispositivos iOS, dispositivos Android, Windows, Windows Phone, Xbox y decodificadores (set-top boxes). El tema siguiente proporciona vínculos a los SDK y Player Framework que puede usar para desarrollar sus propias aplicaciones cliente que pueden consumir contenido multimedia en streaming desde Servicios multimedia.

[Desarrollo de aplicaciones de reproductor de vídeo](media-services-develop-video-players.md)

##Habilitación de CDN de Azure

Servicios multimedia admite la integración con CDN de Azure. Para obtener información sobre cómo habilitar CDN de Azure, consulte [Administración de extremos de streaming en una cuenta de Servicios multimedia](media-services-manage-origins.md#enable_cdn).

##Escalado de una cuenta de Servicios multimedia

Puede escalar **Servicios multimedia** mediante la especificación del número de **unidades reservadas de streaming** y **unidades reservadas de codificación** con las que desea aprovisionar la cuenta.

También puede escalar la cuenta de Servicios multimedia agregándole cuentas de almacenamiento. Cada cuenta de almacenamiento está limitada a 500 TB. Para ampliar el almacenamiento más allá del límite predeterminado, puede asociar varias cuentas de almacenamiento a una sola cuenta de Servicios multimedia.

[Este](media-services-how-to-scale.md) tema contiene vínculos a temas relevantes.

##Soporte técnico

El [Soporte técnico de Azure](https://azure.microsoft.com/support/options/) proporciona opciones de soporte técnico para Azure, incluido Servicios multimedia.

##Guía de patrones y prácticas

[Guía de patrones y prácticas](https://wamsg.codeplex.com/) [Documentación en línea](https://msdn.microsoft.com/library/dn735912.aspx) [Libro electrónico descargable](https://www.microsoft.com/download/details.aspx?id=42629)


##Envío de comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##Contrato de nivel de servicio (SLA)

- Garantizamos la disponibilidad del 99,9% de las transacciones de API de REST para la codificación de Servicios multimedia.
- Para el streaming, atenderemos correctamente las solicitudes de servicio con una garantía de disponibilidad del 99,9% para el contenido multimedia existente cuando se adquiera al menos una unidad de streaming.
- Para los canales en directo, garantizamos que los canales en ejecución tendrán una conectividad externa como mínimo el 99,9 % del tiempo.
- Para la protección de contenido, garantizamos que procesaremos correctamente las solicitudes clave como mínimo el 99,9% del tiempo.
- Para el indizador, atenderemos correctamente las solicitudes de tarea de indizador procesadas con una unidad reservada de codificación el 99,9% del tiempo.

	Para obtener más información, consulte el [Contrato de nivel de servicio (SLA) de Microsoft Azure](https://azure.microsoft.com/support/legal/sla/).

<!-- Images -->
[overview]: ./media/media-services-overview/media-services-overview.png
[vod-overview]: ./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png
[live-overview1]: ./media/media-services-live-streaming-workflow/media-services-live-streaming-new.png
[live-overview2]: ./media/media-services-live-streaming-workflow/media-services-live-streaming-current.png
 

<!---HONumber=AcomDC_0309_2016-->