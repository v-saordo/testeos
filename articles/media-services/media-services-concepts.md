<properties 
	pageTitle="Conceptos de Servicios multimedia de Azure" 
	description="Este tema proporciona información general sobre los conceptos de Servicios multimedia de Azure." 
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
	ms.date="02/25/2016" 
	ms.author="juliako"/>

#Conceptos de Servicios multimedia de Azure 

Este tema proporciona información general sobre los conceptos más importantes de Servicios multimedia.

##<a id="assets"></a>Activos y almacenamiento

###Recursos

Un [recurso](https://msdn.microsoft.com/library/azure/hh974277.aspx) contiene archivos digitales (como vídeos, audio, imágenes, colecciones de miniaturas, pistas de texto y subtítulos) y metadatos de estos archivos. Una vez que los archivos digitales se cargan en un recurso, se pueden utilizar en los flujos de trabajo de codificación y streaming en Servicios multimedia.

Un recurso se asigna a un contenedor de blobs en la cuenta de almacenamiento de Azure y los archivos del recurso se almacenan como blobs en ese contenedor.

Cuando se decide qué contenido multimedia cargar y almacenar en un recurso, se aplican las siguientes consideraciones:

- Un recurso debe contener solo una instancia única de contenido multimedia. Por ejemplo, una edición única de un episodio de televisión, una película o un anuncio.
- Un recurso no puede contener varias representaciones o ediciones de un archivo audiovisual. Un ejemplo de uso inadecuado de un recurso sería intentar almacenar más de un episodio de televisión, anuncio o varios ángulos de cámara desde una sola producción dentro de un recurso. Almacenar varias representaciones o ediciones de un archivo audiovisual en un recurso puede generar dificultades en el envío de trabajos de codificación, en la transmisión y protección de la entrega del recurso más adelante en el flujo de trabajo.  

###Archivo de recursos 
Un [AssetFile](https://msdn.microsoft.com/library/azure/hh974275.aspx) representa un archivo de vídeo o audio real almacenado en un contenedor de blobs. Un archivo de recursos siempre está asociado con un recurso y un recurso puede contener uno o varios archivos. La tarea de Servicios multimedia produce un error si un objeto de archivo de recursos no está asociado a un archivo digital de un contenedor de blobs.

La instancia de **AssetFile** y el archivo multimedia real son dos objetos distintos. La instancia de AssetFile contiene metadatos sobre el archivo multimedia, mientras que el archivo multimedia contiene el contenido multimedia real.

No debe intentar cambiar el contenido de los contenedores de blobs que generó Servicios multimedia sin usar las API de Servicios multimedia.

###Opciones de cifrado de recursos

Según el tipo de contenido que desea cargar, almacenar y entregar, Servicios multimedia proporciona varias opciones de cifrado de las que puede elegir.

**Ninguno**: no se utiliza cifrado. Este es el valor predeterminado. Tenga en cuenta que al utilizar esta opción el contenido no está protegido en tránsito o en reposo en el almacenamiento.

Si planifica entregar un MP4 mediante una descarga progresiva, use esta opción para cargar el contenido.

**StorageEncrypted**: use esta opción para cifrar el contenido no cifrado de manera local mediante el cifrado AES de 256 bits y, a continuación, cargarlo al almacenamiento de Azure donde se almacena cifrado en reposo. Los recursos protegidos con el cifrado de almacenamiento se descifran automáticamente y se colocan en un sistema de archivos cifrados antes de la codificación y, opcionalmente, se vuelven a cifrar antes de volver a cargarlos como un nuevo recurso de salida. El caso de uso principal para el cifrado de almacenamiento es cuando desea proteger los archivos multimedia de entrada de alta calidad con un sólido cifrado en reposo en disco.

Para entregar a un recurso cifrado de almacenamiento, debe configurar la directiva de entrega del recurso para que Servicios multimedia sepa cómo desea entregar el contenido. Antes de poder transmitir el recurso, el servidor de streaming quita el cifrado de almacenamiento y transmite el contenido usando la directiva de entrega especificada (por ejemplo, AES, PlayReady o sin cifrado).

**CommonEncryptionProtected**: utilice esta opción si desea cifrar contenido o cargar contenido ya cifrado con cifrado común o DRM de PlayReady (por ejemplo, Smooth Streaming protegido con DRM de PlayReady).

**EnvelopeEncryptedProtected**: utilice esta opción si desea proteger HTTP Live Streaming (HLS) cifrado (o cargar uno ya protegido) con estándar de cifrado avanzado (AES). Tenga en cuenta que si carga HLS ya cifrado con AES, se debe haber cifrado con Transform Manager.

###Directiva de acceso 

Un [AccessPolicy](https://msdn.microsoft.com/library/azure/hh974297.aspx) define los permisos (como lectura, escritura y lista) y la duración de acceso a un recurso. Normalmente pasaría un objeto AccessPolicy a un localizador que luego se usaría para tener acceso a los archivos contenidos en un recurso.


###Contenedor de blobs

Un contenedor de blobs proporciona una agrupación de un conjunto de blobs. Los contenedores de blobs se usan en Servicios multimedia como punto limítrofe para el control de acceso y localizadores de firma de acceso compartido (SAS) en los recursos. Una cuenta de almacenamiento de Azure puede contener una cantidad ilimitada de contenedores de blobs. un contenedor puede almacenar un número ilimitado de blobs.

>[AZURE.NOTE]No debe intentar cambiar el contenido de los contenedores de blobs que generó Servicios multimedia sin usar las API de Servicios multimedia.

###<a id="locators"></a>Localizadores

Los [localizador](https://msdn.microsoft.com/library/azure/hh974308.aspx)es proporcionan un punto de entrada para tener acceso a los archivos que se encuentran en un recurso. Se usa una directiva de acceso para definir los permisos y la duración en que un cliente tiene acceso a un recurso determinado. Los localizadores pueden tener de varias a una relación con una directiva de acceso, de manera tal que distintos localizadores pueden proporcionar distintas horas de inicio y tipos de conexión a distintos clientes, mientras que todos usan la misma configuración de permiso y duración; sin embargo, debido a una restricción en la directiva de acceso compartido definida por los servicios de almacenamiento de Azure, no puede tener más de cinco localizadores únicos asociados con un recurso determinado a la vez.

Servicios multimedia admite dos tipos de localizadores: los localizadores OnDemandOrigin, que se usan para hacer streaming de elementos multimedia (por ejemplo, MPEG DASH, HLS o Smooth Streaming) o para descargarelementos multimedia de manera progresiva, y los localizadores SAS URL, que se usan para cargar o descargar archivos multimedia hacia y desde el almacenamiento de Azure.

Observe que el permiso de lista (AccessPermissions.List) no se debe usar al crear un localizador OnDemandOrigin.

###Cuenta de almacenamiento

Todo el acceso a Almacenamiento de Azure se realiza a través de una cuenta de almacenamiento. Una cuenta de Servicios multimedia se puede asociar con una o más cuentas de almacenamiento. Una cuenta puede contener una cantidad ilimitada de contenedores, siempre que su tamaño total no supere los 500 TB por cuenta de almacenamiento. Servicios multimedia proporciona herramientas del nivel de SDK que le permiten administrar varias cuentas de almacenamiento y equilibrar la carga de la distribución de sus recursos durante la carga a estas cuentas según métricas o una distribución aleatoria. Para obtener más información, consulte Uso de [Almacenamiento de Azure](https://msdn.microsoft.com/library/azure/dn767951.aspx).

##Trabajos y tareas

Un [trabajo](https://msdn.microsoft.com/library/azure/hh974289.aspx) se usa normalmente para procesar (por ejemplo, indexar o codificar) una presentación de audio/vídeo. Si procesa varios vídeos, cree un trabajo para cada vídeo que se va a codificar.

Un trabajo contiene metadatos acerca del procesamiento que se realizará. Cada trabajo contiene una o varias [tareas](https://msdn.microsoft.com/library/azure/hh974286.aspx) que especifican una tarea de procesamiento atómica, sus recursos de entrada, recursos de salida, un procesador de multimedia y su configuración asociada. Las tareas dentro de un trabajo se pueden encadenar en conjunto, donde el recurso de salida de una de las tareas se indica como el recurso de entrada de la tarea siguiente. De este modo, un trabajo puede contener todos los procesos necesarios para una presentación multimedia.

##<a id="encoding"></a>Codificación 

Servicios multimedia de Azure ofrece varias opciones para la codificación de medios en la nube.

Cuando comience con Servicios multimedia, es importante comprender la diferencia entre códecs y formatos de archivo. Los códecs son el software que implementa los algoritmos de compresión/descompresión, mientras que los formatos de archivo son contenedores que contienen el vídeo comprimido.

Servicios multimedia proporciona paquetes dinámicos que permiten entregar contenido codificado MP4 de velocidad de bits adaptable o Smooth Streaming en formatos admitidos por Servicios multimedia (MPEG DASH, HLS, Smooth Streaming, HDS) sin tener que volver a empaquetar en estos formatos de streaming.

Para aprovecharse de los [paquetes dinámicos](media-services-dynamic-packaging-overview.md), deberá hacer lo siguiente:

- Codifique su archivo intermedio (origen) en un conjunto de archivos MP4 de velocidad de bits adaptable o archivos Smooth Streaming de velocidad de bits adaptable (los pasos de codificación se muestran más adelante en este tutorial).
- Obtenga al menos la unidad de streaming a petición para el extremo de streaming desde el que planea entregar el contenido. Para obtener más información, consulte [Escalación de unidades reservadas de streaming a petición](media-services-manage-origins.md#scale_streaming_endpoints/).

Servicios multimedia admite los siguientes codificadores a petición que se describen en este artículo:

- [Media Encoder Estándar](media-services-encode-asset.md#media-encoder-standard)
- [Flujo de trabajo del Codificador multimedia](media-services-encode-asset.md#media-encoder-premium-workflow)

Para obtener información acerca de los codificadores compatibles, consulte [Codificadores](media-services-encode-asset.md).


##Streaming en directo

En Servicios multimedia de Azure, un canal representa una canalización para procesar contenido de streaming en directo. Los canales reciben el flujo de entrada en directo de dos maneras posibles:

- Un codificador local en directo envía contenido RTMP o Smooth Streaming (MP4 fragmentado) de varias velocidades de bits al canal. Puede usar los siguientes codificadores en directo que generan Smooth Streaming de varias velocidades de bits: Elemental, Envivio y Cisco. Los siguientes codificadores en directo generan RTMP: transcodificadores Tricaster, Telestream Wirecast y Adobe Flash Live. Las secuencias tomadas pasan a través de canales sin más procesamiento. Cuando se solicita, Servicios multimedia entrega la secuencia a los clientes.

- Una secuencia de una sola velocidad de bits (con uno de los siguientes formatos: RTP (MPEG-TS), RTMP o Smooth Streaming (MP4 fragmentado)) se envía al canal habilitado para realizar la codificación en directo con Servicios multimedia. Después, el canal codifica en directo la secuencia entrante de una sola velocidad de bits en una secuencia de vídeo de varias velocidades de bits (adaptable). Cuando se solicita, Servicios multimedia entrega la secuencia a los clientes.

###Canal

En Servicios multimedia, los [canal](https://msdn.microsoft.com/library/azure/dn783458.aspx)es son los responsables de procesar el contenido de streaming en vivo. Un canal proporciona un extremo de entrada (dirección URL de introducción) que luego se brinda a un transcodificador en vivo. El canal recibe flujos de entrada en vivo desde el transcodificador en vivo y las deja a disposición del streaming a través de uno o más StreamingEndpoints. Los canales también proporcionan un extremo de vista previa (dirección URL de vista previa) que se puede utilizar para obtener una vista previa y validar el flujo antes de mayor procesamiento y entrega.

Puede obtener la dirección URL de introducción y la dirección URL de vista previa cuando crea el canal. Para obtener estas direcciones URL, el canal no puede encontrarse en el estado iniciado. Cuando está listo para comenzar a insertar datos desde un transcodificador en vivo al canal, este debe estar iniciado. Una vez que el transcodificar en vivo comienza a introducir datos, puede tener una vista previa de la transmisión.

Cada cuenta de Servicios multimedia puede contener varios canales, varios programas y varios StreamingEndpoints. Según las necesidades de ancho de banda y seguridad, los servicios de StreamingEndpoint pueden dedicarse a uno o más canales. Puede extraer cualquier StreamingEndpoint de cualquier canal.


###Programa 

Un [programa](https://msdn.microsoft.com/library/azure/dn783463.aspx) le permite controlar la publicación y almacenamiento de segmentos en una secuencia en directo. Los canales administran los programas. La relación entre canales y programas es muy similar a los medios tradicionales, donde un canal tiene un flujo constante de contenido y un programa se enfoca algún evento programado en dicho canal. Puede especificar la cantidad de horas que desea conservar el contenido grabado del programa en la configuración de la propiedad **ArchiveWindowLength**. Este valor se puede establecer desde un mínimo de cinco minutos a un máximo de 25 horas.

ArchiveWindowLength también indica el tiempo máximo que los clientes pueden buscar hacia atrás a partir de la posición en vivo actual. Los programas pueden transmitirse durante la cantidad de tiempo especificada, pero el contenido que escape de esa longitud de ventana se descartará continuamente. El valor de esta propiedad también determina durante cuánto tiempo los manifiestos de cliente pueden crecer.

Cada programa está asociado a un recurso. Para publicar el programa, debe crear un localizador para el recurso asociado. Contar con este localizador le permitirá crear una dirección URL de streaming que puede proporcionar a sus clientes.

Un canal es compatible con hasta tres programas en ejecución simultánea, por lo que puede crear varios archivos del mismo flujo entrante. Esto le permite publicar y archivar distintas partes de un evento, según sea necesario. Por ejemplo, el requisito de su empresa es solo archivar seis horas de un programa, pero difundir solo los últimos diez minutos. Para lograrlo, necesita crear dos programas en ejecución simultánea. Un programa está definido para archivar seis horas del evento, pero no está publicado. El otro programa está definido para archivar durante diez minutos y este programa sí se publica.


Para más información, consulte:

- [Uso de canales habilitados para realizar la codificación en directo con Servicios multimedia de Azure](media-services-manage-live-encoder-enabled-channels.md)
- [Uso de canales que reciben streaming en vivo con velocidad de bits múltiple de codificadores locales](media-services-manage-channels-overview.md)
- [Cuotas y limitaciones](media-services-quotas-and-limitations.md).  

##Protección del contenido

###Cifrado dinámico

Servicios multimedia de Azure le permite proteger su contenido multimedia desde el momento en que deja el equipo a través de almacenamiento, procesamiento y entrega. Servicios multimedia permite entregar el contenido cifrado de forma dinámica con Estándar de cifrado avanzado (AES) (mediante claves de cifrado de 128 bits) y cifrado común (CENC) mediante DRM de PlayReady o Widevine. Los Servicios multimedia también proporcionan un servicio para entregar claves AES y licencias de PlayReady a clientes autorizados.

Actualmente puede cifrar los formatos de streaming siguientes: HLS, MPEG DASH y Smooth Streaming. No puede cifrar el formato de streaming HDS ni descargas progresivas.

Si desea que Servicios multimedia cifren un recurso, debe asociar una clave de cifrado (CommonEncryption o EnvelopeEncryption) con su recurso y, además, configurar directivas de autorización para la clave.

Para transmitir un activo de cifrado de almacenamiento, debe configurar la directiva de entrega del activo para especificar cómo desea entregarlo.

Cuando un reproductor solicita una transmisión, los Servicios multimedia usan la clave especificada para cifrar de forma dinámica el contenido mediante un cifrado de sobre (con AES) o un cifrado común (con PlayReady o Widevine). Para descifrar la secuencia, el reproductor solicitará la clave del servicio de entrega de claves. Para decidir si el usuario está o no autorizado para obtener la clave, el servicio evalúa las directivas de autorización que especificó para la clave.


###Restricción de token

La directiva de autorización de clave de acceso podría tener una o más restricciones de autorización: abrir, restricción de token o restricción de IP. La directiva con restricción token debe ir acompañada de un token emitido por un Servicio de tokens seguros (STS). Servicios multimedia admite tokens en formato Token de web simple (SWT) y en formato Token de web JSON (JWT). Los Servicios multimedia no proporcionan Servicios de tokens seguros. Puede crear un STS personalizado o aprovechar el Servicio de control de acceso (ACS) de Microsoft Azure para emitir tokens. Se debe configurar el STS para crear un token firmado con las notificaciones de clave y emisión que especificó en la configuración de restricción de tokens. El servicio de entrega de claves de Servicios multimedia devolverá la clave solicitada (o licencia) al cliente si el token es válido y las reclamaciones del token coinciden con las configuradas para la clave (o licencia).

Al configurar la directiva de restricción de token, debe especificar los parámetros de clave de comprobación principal, emisor y público. La clave de comprobación principal contiene la clave con la que se firmó el token y el emisor es el servicio de tokens seguros que emite el token. El público (a veces denominado ámbito) describe la intención del token o del recurso cuyo acceso está autorizado por el token. El servicio de entrega de claves de los Servicios multimedia valida que estos valores del token coincidan con los valores de la plantilla.

Para más información, consulte los siguientes artículos.

[Información general sobre la protección del contenido](media-services-content-protection-overview.md) [Protección con AES-128](media-services-protect-with-aes128.md) [Protección con DRM](media-services-protect-with-drm.md)

##Entrega

###<a id="dynamic_packaging"></a>Empaquetado dinámico

Cuando se trabaja con Servicios multimedia, es aconsejable codificar los archivos intermedios en una conjunto MP4 de velocidad de bits adaptable y, a continuación, convertir el conjunto en el formato deseado con el [Empaquetado dinámico](media-services-dynamic-packaging-overview.md).


###Extremo de streaming

Un StreamingEndpoint representa un servicio de streaming que puede entregar contenido directamente a una aplicación de reproductor de cliente o a una red de entrega de contenido (CDN) para su posterior distribución (Servicios multimedia de Azure ahora proporciona la integración de CDN de Azure). La secuencia de salida de un servicio de StreamingEndpoint puede ser una secuencia en vivo o un recurso de vídeo bajo demanda en su cuenta de Servicios multimedia. Además, puede controlar la capacidad del servicio StreamingEndpoint para manejar las crecientes necesidades de ancho de banda mediante el ajuste de las unidades de escalado (también conocidas como unidades de streaming). Se recomienda asignar una o más unidades de escalado para las aplicaciones en el entorno de producción. Las unidades de escalado proporcionan capacidad de salida dedicada que puede adquirirse en incrementos de 200 Mbps y funcionalidad adicional que actualmente incluye el uso de empaquetado dinámico.

Se recomienda utilizar el cifrado dinámico o el empaquetado dinámico. Para usar estas características, debe tener al menos una unidad de streaming para el extremo desde el que va a realizar la transmisión. Para obtener más información, consulte [Escalado de unidades de streaming](media-services-manage-origins.md#scale_streaming_endpoints)

De forma predeterminada, puede disponer de hasta 2 canales en streaming en su cuenta de Servicios multimedia. Para solicitar un límite superior, consulte [Cuotas y limitaciones](media-services-quotas-and-limitations.md).

Solo se le cobrará cuando StreamingEndpoint esté en estado en ejecución.

###Directiva de entrega de recursos

Uno de los pasos del flujo de trabajo de entrega de contenido de Servicios multimedia consiste en configurar [directivas de entrega para los recursos](https://msdn.microsoft.com/library/azure/dn799055.aspx) que desea transmitir. La directiva de entrega de recursos indica a los Servicios multimedia cómo desea usted que se entregue el recurso: en qué protocolo de streaming se debe empaquetar de forma dinámica el recurso (por ejemplo, MPEG DASH, HLS, Smooth Streaming o todos) o si desea o no cifrar de forma dinámica el recurso y de qué manera (cifrado de sobre o común).

Si tiene un recurso cifrado de almacenamiento, antes de poder transmitir el recurso, el servidor de streaming quita el cifrado de almacenamiento y transmite el contenido usando la directiva de entrega especificada. Por ejemplo, para entregar el recurso cifrado con clave de cifrado del estándar de cifrado avanzado (AES), defina el tipo de directiva en DynamicEnvelopeEncryption. Para quitar el cifrado de almacenamiento y transmitir el recurso sin cifrar, establezca el tipo de directiva en NoDynamicEncryption.

###Descarga progresiva 

La descarga progresiva le permite comenzar a reproducir archivos multimedia antes de haber descargado todo el archivo. Solo puede descargar progresivamente un archivo MP4.

Tenga en cuenta que debe descifrar los recursos cifrados si desea que estén disponibles para la descarga progresiva.

Para proporcionar direcciones URL de descarga progresiva a los usuarios, primero debe crear un localizador OnDemandOrigin. Crear el localizador le brinda la ruta de acceso de base al recurso. Luego debe anexar el nombre del archivo MP4. Por ejemplo:

	http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

###Direcciones URL de streaming

Transmisión del contenido a los clientes. Para proporcionar direcciones URL de streaming a los usuarios, primero debe crear un localizador OnDemandOrigin. Crear el localizador le brinda la ruta de acceso de base al recurso que contiene el contenido que desea transmitir. Sin embargo, para poder transmitir este contenido, es necesario modificar aún más esta ruta de acceso. Para construir una dirección URL completa al archivo de manifiesto del streaming, debe concatenar el valor de la ruta de acceso del localizador y el nombre de archivo del manifiesto (filename.ism). Luego, anexe /Manifiesto y un formato adecuado (si corresponde) a la ruta de acceso del localizador.

También puede transmitir el contenido por una conexión SSL. Para ello, asegúrese de que las URL de streaming comienzan por HTTPS.

Tenga en cuenta que solo puede transmitir por SSL si se creó el extremo de streaming desde el que se entrega el contenido a partir del 10 de septiembre de 2014. Si las direcciones URL de streaming se basan en los extremos de streaming creados después del 10 de septiembre, la dirección URL contendrá "streaming.mediaservices.windows.net" (el formato nuevo). Las direcciones URL de streaming que contengan "origin.mediaservices.windows.net" (el formato anterior) no son compatibles con SSL. Si la dirección URL tiene un formato antiguo y desea poder transmitir a través de SSL, cree un extremo de streaming nuevo. Utilice direcciones URL creadas en función del nuevo extremo de streaming para transmitir el contenido a través de SSL.

En la siguiente lista se describen distintos formatos de streaming y aparecen ejemplos:

- Smooth Streaming

	{nombre de extremo de streaming-nombre de cuenta de servicios multimedia}.streaming.mediaservices.windows.net/{Id. de localizador}/{filename}.ism/Manifest
		
		http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest


- MPEG DASH

	{nombre de extremo de streaming-nombre de cuenta de servicios multimedia}.streaming.mediaservices.windows.net/{Id. de localizador}/{nombre de archivo}.ism/Manifest(formato=mpd-time-csf)
 
		http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf)



- Apple HTTP Live Streaming (HLS) V4

	{nombre de extremo de streaming-nombre de cuenta de servicios multimedia}.streaming.mediaservices.windows.net/{Id. de localizador}/{nombre de archivo}.ism/Manifest(formato=m3u8-aapl)

		http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl)



- Apple HTTP Live Streaming (HLS) V3

	{nombre de extremo de streaming-nombre de cuenta de servicios multimedia}.streaming.mediaservices.windows.net/{Id. de localizador}/{nombre de archivo}.ism/Manifest(formato=m3u8-aapl-v3)

		http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3)

- HDS (solo para licencias de Adobe PrimeTime/Access)

	{nombre de extremo de streaming-nombre de cuenta de servicios multimedia}.streaming.mediaservices.windows.net/{Id. de localizador}/{nombre de archivo}.ism/Manifest(formato=f4m-f4f)

		http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=f4m-f4f) 

 
##Rutas de aprendizaje de Servicios multimedia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Envío de comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0302_2016-->