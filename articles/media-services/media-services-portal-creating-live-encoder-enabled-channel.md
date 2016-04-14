<properties 
	pageTitle="Creación de canales que realizan codificación en directo de transmisiones de una sola velocidad de bits a transmisiones de varias velocidades de bits mediante el Portal de Azure clásico" 
	description="Este tutorial le guía por los pasos para crear un canal que reciba una transmisión en directo de una sola velocidad de bits y la codifique como una transmisión de varias velocidades de bits mediante el Portal de Azure clásico." 
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


#Creación de canales que realizan codificación en directo de transmisiones de una sola velocidad de bits a transmisiones de varias velocidades de bits mediante el Portal de Azure clásico

> [AZURE.SELECTOR]
- [Portal](media-services-portal-creating-live-encoder-enabled-channel.md)
- [.NET](media-services-dotnet-creating-live-encoder-enabled-channel.md)
- [REST API](https://msdn.microsoft.com/library/azure/dn783458.aspx)

Este tutorial le guía por los pasos para crear un **canal** que reciba una secuencia en directo de una sola velocidad de bits y la codifique como secuencia de varias velocidades de bits.

>[AZURE.NOTE]Para obtener información más detallada sobre los canales habilitados para la codificación en directo, consulte [Uso de canales que realizan la codificación en directo de una secuencia de una sola velocidad de bits a otra de varias velocidades](media-services-manage-live-encoder-enabled-channels.md).

##Escenario común de streaming en vivo

A continuación se indican los pasos generales para crear aplicaciones comunes de streaming en vivo.

>[AZURE.NOTE] Actualmente, la duración máxima recomendada de un evento en directo es de 8 horas. Si necesita ejecutar un canal durante largos períodos de tiempo, póngase en contacto con amslived en Microsoft.com.

1. Conecte una cámara de vídeo a un equipo. Inicie y configure un codificador local en directo que pueda generar una secuencia de una sola velocidad de bits en uno de los siguientes protocolos: RTMP, Smooth Streaming o RTP (MPEG-TS). Para obtener más información, consulte [Compatibilidad con RTMP de Servicios multimedia de Azure y codificadores en directo](http://go.microsoft.com/fwlink/?LinkId=532824).
	
	Este paso también puede realizarse después de crear el canal.

1. Cree e inicie un canal.

1. Recupere la URL de ingesta de canales.

	El codificador en directo usa la URL de ingesta para enviar la secuencia al canal.
1. Recupere la URL de vista previa de canal. 

	Use esta dirección URL para comprobar que el canal recibe correctamente la secuencia en vivo.

3. Cree un programa (que también creará un recurso).
1. Publique el programa (que creará un localizador a petición para el recurso asociado).  

	Asegúrese de tener al menos una unidad de streaming reservada en el extremo de streaming desde el que desea transmitir el contenido.
1. Inicie el programa cuando esté listo para iniciar el streaming y el archivo.
2. Si lo desea, puede señalar el codificador en directo para iniciar un anuncio. El anuncio se inserta en el flujo de salida.
1. Detenga el programa cuando quiera detener el streaming y el archivo del evento.
1. Elimine el programa (y, opcionalmente, elimine el recurso).   

##Apartados de este tutorial

En este tutorial, se utiliza el Portal de Azure clásico para realizar las tareas siguientes:

2.  Configure de extremos de streaming.
3.  Cree un canal que está habilitado para realizar la codificación en directo.
1.  Obtenga la URL de introducción para proporcionarla al codificador en directo. El codificador en directo utilizará esta dirección URL para introducir la secuencia en el canal.
1.  Crear un programa (y un recurso)
1.  Publicar el recurso y obtener las direcciones URL de streaming  
1.  Reproducir el contenido 
2.  Limpiar

##Requisitos previos
Los siguientes requisitos son necesarios para completar el tutorial.

- Para completar este tutorial, deberá tener una cuenta de Azure. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](azure.microsoft.com).
- Una cuenta de Servicios multimedia. Para crear una cuenta de Servicios multimedia, consulte [Creación de una cuenta](media-services-create-account.md).
- Una cámara web y un codificador que pueda enviar una secuencia en vivo de una sola velocidad de bits.

##Configuración de extremos de streaming con el Portal

Cuando se trabaja con los Servicios multimedia de Azure, uno de los escenarios más comunes es entregar streaming de velocidad de bits adaptable a los clientes. Con el streaming de velocidad de bits adaptable, el cliente puede cambiar a una secuencia de velocidad de bits mayor o menor que el vídeo mostrado, según el ancho de banda actual de la red, el uso de CPU y otros factores. Servicios multimedia admite las siguientes tecnologías de streaming adaptable: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH y HDS (únicamente para licenciatarios de Adobe PrimeTime/Access).

Cuando se trabaja con streaming en vivo, un codificador en directo local (en nuestro caso Wirecast) introduce una secuencia en vivo de velocidad de bits múltiple en el canal. Cuando un usuario solicita el stream, Servicios multimedia usa paquetes dinámicos para volver a empaquetar la secuencia de origen en la secuencia de velocidad de bits adaptativa solicitado (HLS, DASH o Smooth).

Para aprovechar al máximo el empaquetado dinámico, debe obtener al menos una unidad de streaming para el **extremo de streaming** desde el que va a entregar el contenido.

Para cambiar el número de unidades reservadas de streaming, haga lo siguiente:

1. En el [Portal de Azure clásico](https://manage.windowsazure.com/), haga clic en **Servicios multimedia**. A continuación, haga clic en el nombre del servicio multimedia.

2. Seleccione la página EXTREMOS DE STREAMING. Luego, haga clic en el extremo de streaming que desea modificar.

3. Para especificar el número de unidades de streaming, seleccione la pestaña ESCALA y desplace el control deslizante de **capacidad reservada**.

	![Página de escala](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-origin-scale.png)

4. Presione el botón GUARDAR para guardar los cambios.

	La asignación de cualquier nueva unidad puede tardar unos 20 minutos en finalizarse.

	 
	>[AZURE.NOTE] Actualmente, pasar de cualquier valor positivo de unidades de streaming a ninguno puede deshabilitar el streaming hasta una hora.
	>
	> Se utiliza el número más elevado de unidades especificadas durante el período de 24 horas al calcular el coste. Para obtener más información acerca del precio, consulte la página sobre [información del precio de Servicios multimedia](http://go.microsoft.com/fwlink/?LinkId=275107).

 
##Creación de un canal

1.	En el [Portal de Azure clásico](http://manage.windowsazure.com/), haga clic en Servicios multimedia y haga clic en el nombre de cuenta de Servicios multimedia.
2.	Seleccione la página CANALES.
3.	Seleccione +Agregar para agregar un nuevo canal.

Elija los tipos de codificación **Estándar**. Este tipo especifica que desea crear un canal que está habilitado para la codificación en directo. Lo que significa que la secuencia entrante de velocidad de bits única se envía al canal y se codifica en una secuencia de velocidad de bits múltiple mediante la configuración del codificador directo especificado. Para obtener más información, consulte [Uso de canales que realizan la codificación en directo de una secuencia de una sola velocidad de bits a otra de varias velocidades](media-services-manage-live-encoder-enabled-channels.md)

![standard0][standard0]

Para el tipo de codificación **Estándar**, las opciones del protocolo de introducción válidas son las siguientes:

- MP4 fragmentado de velocidad de bits única (Smooth Streaming)
- RTMP de velocidad de bits única
- RTP (MPEG-TS): secuencia de transporte MPEG-2 a través de RTP.

Para obtener una explicación detallada sobre cada protocolo, consulte [Uso de canales que realizan la codificación en directo de una secuencia de una sola velocidad de bits a otra de varias velocidades](media-services-manage-live-encoder-enabled-channels.md)

![standard1][standard1]

No se puede cambiar el protocolo de entrada mientras el canal o sus programas asociados se están ejecutando. Si necesita diferentes protocolos, debe crear canales independientes para cada protocolo de entrada.

En la página **Configuración de publicidad** puede especificar el origen de las señales de marcadores de anuncios. Al usar el Portal, solo puede seleccionar API, que indica el codificador en directo del canal debe escuchar una API de marcadores de anuncio asincrónica. Al usar el Portal, solo puede seleccionar API.

Para obtener más información, consulte [Uso de canales que realizan la codificación en directo de una secuencia de una sola velocidad de bits a otra de varias velocidades](media-services-manage-live-encoder-enabled-channels.md).

![standard2][standard2]

En la página **Valores preestablecidos de codificación**, puede seleccionar los valores preestablecidos del sistema. Actualmente, el único valor preestablecido del sistema que puede seleccionar es **Predeterminado 720p**.

![standard3][standard3]

En la página **Creación de canal** puede definir las direcciones IP permitidas para publicar vídeo en el canal. Las direcciones IP permitidas se pueden especificar como dirección IP individual (por ejemplo, ‘10.0.0.1’), un intervalo de direcciones IP mediante una dirección IP y una máscara de subred CIDR (por ejemplo, ‘10.0.0.1/22’) o un intervalo de direcciones IP mediante una dirección IP y una máscara de subred decimal con puntos (por ejemplo, ‘10.0.0.1(255.255.252.0)’).

Si no se especifican direcciones IP y no hay ninguna definición de regla, no se permitirá ninguna dirección IP. Para permitir las direcciones IP, cree una regla y establezca 0.0.0.0/0.


![standard4][standard4]

>[AZURE.NOTE] Actualmente en versión preliminar; el inicio del canal puede tardar hasta 30 minutos. El restablecimiento de canal puede tardar hasta 5 minutos.

Una vez creado el canal, puede seleccionar la pestaña **CODIFICADOR** donde puede ver las configuraciones de canales. También puede administrar los anuncios y las tabletas.

![standard5][standard5]

Para obtener más información, consulte [Uso de canales que realizan la codificación en directo de una secuencia de una sola velocidad de bits a otra de varias velocidades](media-services-manage-live-encoder-enabled-channels.md).


##Obtención de direcciones URL de introducción

Una vez creado el canal, obtendrá direcciones URL de introducción que se proporcionarán al codificador en directo. El codificador usa estas direcciones URL para introducir una secuencia en vivo.

![readychannel](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-ready-channel.png)


![ingesturls](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-ingest-urls.png)


##Creación y administración de un programa

###Información general

Un canal está asociado a programas que le permiten controlar la publicación y el almacenamiento de segmentos en una secuencia en vivo. Los canales administran los programas. La relación entre canales y programas es muy similar a los medios tradicionales, donde un canal tiene un flujo constante de contenido y un programa se enfoca algún evento programado en dicho canal.

Puede especificar la cantidad de horas que desea conservar el contenido grabado del programa en la configuración de la duración de **Ventana de archivo**. Este valor se puede establecer desde un mínimo de cinco minutos a un máximo de 25 horas. La duración de la ventana de archivo también indica el tiempo máximo que los clientes pueden buscar hacia atrás a partir de la posición en vivo actual. Los programas pueden transmitirse durante la cantidad de tiempo especificada, pero el contenido que escape de esa longitud de ventana se descartará continuamente. El valor de esta propiedad también determina durante cuánto tiempo los manifiestos de cliente pueden crecer.

Cada programa está asociado a un recurso. Para publicar el programa, debe crear un localizador a petición para el recurso asociado. Contar con este localizador le permitirá crear una dirección URL de streaming que puede proporcionar a sus clientes.

Un canal es compatible con hasta tres programas en ejecución simultánea, por lo que puede crear varios archivos del mismo flujo entrante. Esto le permite publicar y archivar distintas partes de un evento, según sea necesario. Por ejemplo, el requisito de su empresa es solo archivar seis horas de un programa, pero difundir solo los últimos diez minutos. Para lograrlo, necesita crear dos programas en ejecución simultánea. Un programa está definido para archivar seis horas del evento, pero no está publicado. El otro programa está definido para archivar durante diez minutos y este programa sí se publica.

No debe volver a usar programas existentes para eventos nuevos. En su lugar, cree e inicie un programa nuevo para cada evento como se describe en la sección Programación de aplicaciones de streaming en vivo.

Inicie el programa cuando esté listo para iniciar el streaming y el archivo. Detenga el programa cuando quiera detener el streaming y el archivo del evento.

Para eliminar contenido archivado, detenga y elimine el programa y, a continuación, elimine el recurso asociado. No se puede eliminar un recurso si se usa un programa; primero se debe eliminar el programa.

Incluso después de detener y eliminar el programa, los usuarios podrán transmitir el contenido archivado como un vídeo a petición siempre que no elimine el recurso.

Si desea conservar el contenido archivado, pero no hacerlo disponible para la transmisión, elimine el localizador de streaming.

###Creación/inicio/detención de programas

Cuando la secuencia fluye en el canal, puede comenzar el evento de streaming mediante la creación de un recurso, un programa y el localizador de streaming. Se archivará la secuencia y estará disponible a los usuarios a través del extremo de streaming.

Existen dos formas de iniciar un evento:

1. En la página **CANAL**, presione **AGREGAR** para agregar un nuevo programa.

	Especifique lo siguiente: nombre del programa, nombre de recurso, ventana de archivo y opción de cifrado.
	
	![createprogram](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-program.png)
	
	Si ha dejado **Publicar este programa ahora** activado, el programa creará las direcciones URL de publicación.
	
	Puede presionar **INICIO**, cuando esté preparado para transmitir por streaming el programa.

	Una vez iniciado el programa, puede presionar REPRODUCIR para empezar a reproducir el contenido.


	![createdprogram](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-created-program.png)

2. También, puede utilizar un acceso directo y presionar el botón **INICIAR STREAMING** situado en la página **CANAL**. Se creará un recurso, un programa y el localizador de streaming.

	El programa se denomina DefaultProgram y la ventana de archivo se establece en una hora.

	Puede reproducir el programa publicado desde la página CANAL.

	![channelpublish](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-channel-play.png)


Si hace clic en **DETENER STREAMING** en la página **CANAL**, el programa predeterminado se detiene y se elimina. El recurso seguirá estando allí y se puede publicar o cancelar su publicación desde la página **CONTENIDO**.

Si cambia a la página **CONTENIDO**, verá los recursos que se crearon para los programas.

![contentasset](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-content-assets.png)


##Reproducción de contenido

Para proporcionar al usuario una dirección URL que pueda utilizar para transmitir por streaming el contenido, primero tendrá que "publicar" el recurso (tal como se describió en la sección anterior) mediante la creación de un localizador (cuando se publica un recurso mediante el Portal, los localizadores se crean automáticamente). Los localizadores proporcionan acceso a los archivos contenidos en el recurso.

Dependiendo del protocolo de streaming que desee utilizar para reproducir el contenido, tendrá que modificar la dirección URL que obtiene del vínculo **URL DE PUBLICACIÓN** del canal\\programa.

El empaquetado dinámico se encargará de empaquetar la secuencia en vivo en el protocolo especificado.

De manera predeterminada, una dirección URL de streaming tiene el siguiente formato y se puede usar para reproducir los recursos de Smooth Streaming:

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Para generar una dirección URL de streaming de HLS, anexe (format=m3u8-aapl) a la dirección URL.

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

Para generar una dirección URL de streaming de MPEG DASH, anexe (format=mpd-time-csf) a la dirección URL.

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

Para obtener más información acerca de la entrega de contenido, consulte [Entregar de contenido](media-services-deliver-content-overview.md).

Puede reproducir secuencias de Smooth Streaming mediante el [reproductor AMS](http://amsplayer.azurewebsites.net/azuremediaplayer.html) o usar dispositivos iOS y Android para reproducir HLS versión 3.

##Limpieza

Si se realizan eventos de streaming y desea limpiar los recursos aprovisionados anteriormente, siga el procedimiento siguiente.

- Detenga la inserción de la secuencia en el codificador.
- Detenga el canal. Cuando se detiene el canal, no se incurrirá en ningún cargo. Cuando necesite iniciarlo de nuevo, tendrá la misma URL de introducción, por lo que no necesitará volver a configurar su codificador.
- Puede detener el extremo de streaming, a menos que desee seguir proporcionando el archivo de su evento en vivo como una secuencia a petición. Cuando el canal está en estado detenido, no se incurrirá en ningún cargo.
  

##Consideraciones

- Actualmente, la duración máxima recomendada de un evento en directo es de 8 horas. Si necesita ejecutar un canal durante largos períodos de tiempo, póngase en contacto con amslived en Microsoft.com.
- Asegúrese de tener al menos una unidad de streaming reservada en el extremo de streaming desde el que desea transmitir el contenido.


##Rutas de aprendizaje de Servicios multimedia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Envío de comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]



[standard0]: ./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-channel-standard0.png
[standard1]: ./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-channel-standard1.png
[standard2]: ./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-channel-standard2.png
[standard3]: ./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-channel-standard3.png
[standard4]: ./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-channel-standard4.png
[standard5]: ./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-channel-standard_encode.png

<!---HONumber=AcomDC_0211_2016-->