<properties 
	pageTitle="Especificación de la introducción en directo de MP4 fragmentado de Servicios multimedia de Azure" 
	description="En esta especificación se describe el protocolo y el formato para inserción de streaming en vivo basada en MP4 fragmentado para Servicios multimedia de Microsoft Azure. Servicios multimedia de Microsoft Azure ofrece streaming en vivo que permite a los clientes la transmisión de eventos en directo y difundir contenido en tiempo real con Microsoft Azure como la plataforma de nube. En este documento también se describen los prácticas recomendadas en la creación de mecanismos de introducción en directo sólidos y altamente redundantes." 
	services="media-services" 
	documentationCenter="" 
	authors="cenkdin,juliako" 
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

#Especificación de la introducción en directo de MP4 fragmentado de Servicios multimedia de Azure

En esta especificación se describe el protocolo y el formato para inserción de streaming en vivo basada en MP4 fragmentado para Servicios multimedia de Microsoft Azure. Servicios multimedia de Microsoft Azure ofrece streaming en vivo que permite a los clientes la transmisión de eventos en directo y difundir contenido en tiempo real con Microsoft Azure como la plataforma de nube. En este documento también se describen los prácticas recomendadas en la creación de mecanismos de introducción en directo sólidos y altamente redundantes.


##1\. Notación de cumplimiento

Las palabras clave "DEBE", "NO DEBE", "SE REQUIERE", "TENDRÁ QUE", "NO DEBERÁ", "DEBERÍA", "NO DEBERÍA", "RECOMENDADO", "PUEDE" y "OPCIONAL" de este documento se deben interpretar como se describe en RFC 2119.

##2\. Diagrama del servicio 

En el diagrama siguiente se muestra la arquitectura de alto nivel del servicio de streaming en vivo en Servicios multimedia de Microsoft Azure:

1.	El codificador en directo inserta fuentes en canales que se crearon y aprovisionaron mediante el SDK de Servicios multimedia de Microsoft Azure.
2.	Los canales, los programas y el extremo de streaming de Servicios multimedia de Microsoft Azure controlan todas las funcionalidades de streaming en vivo incluidas la introducción, el formato, DVR de nube, la seguridad, la escalabilidad y la redundancia.
3.	Opcionalmente, los clientes podrían optar por implementar una capa CDN entre el extremo de streaming y los extremos de cliente.
4.	Los extremos de cliente transmiten por secuencias desde el extremo de streaming mediante protocolos de transmisión por secuencias adaptativa HTTP (por ejemplo, Smooth Streaming, DASH, HDS o HLS).

![imagen1][image1]


##3\. Formato de secuencia de bits: MP4 fragmentado ISO 14496-12

El formato de ingesta de streaming activa que se describe en este documento se basa en [ISO-14496-12]. Consulte [[MS SSTR]](http://msdn.microsoft.com/library/ff469518.aspx) para obtener una explicación detallada del formato MP4 fragmentado y de las extensiones tanto para archivos de vídeo a la carta como para la introducción de streaming en vivo.

###Definiciones de formato de introducción en directo 

A continuación se muestra una lista de definiciones de formato especial que se aplican a la introducción en directo en Servicios multimedia de Microsoft Azure:

1. El cuadro 'ftyp', LiveServerManifestBox y 'moov' DEBE enviarse con cada solicitud (HTTP POST). DEBEN enviarse al principio de la secuencia y cada vez que el codificador deba volver a conectarse para reanudar la introducción de secuencias. Consulte la sección 6 en [1] para obtener más detalles.
2. La sección 3.3.2 en [1] define un cuadro opcional denominado StreamManifestBox para la ingesta activa. Debido a la lógica de enrutamiento del equilibrador de carga de Microsoft Azure, el uso de este cuadro está en desuso y NO DEBERÍA estar presente cuando se introduce en Servicios multimedia de Microsoft Azure. Si esta casilla está presente, Servicios multimedia de Azure la omite en el modo silencioso.
3. El TrackFragmentExtendedHeaderBox definido en 3.2.3.2 en [1] DEBE estar presente para cada fragmento.
4. Se debe usar la versión 2 de TrackFragmentExtendedHeaderBox para generar los segmentos multimedia con direcciones URL idénticas en varios centros de datos. SE REQUIERE el campo de índice de fragmento para conmutación por error entre centros de datos de formatos de streaming basados en índices como Apple HTTP Live Streaming (HLS) y MPEG-DASH basado en índices. Para habilitar la conmutación por error entre centros de datos, el índice de fragmentos DEBE sincronizarse entre varios codificadores y aumentar en 1 para cada fragmento de medios sucesivo, incluso entre reinicios o errores de codificador.
5. En la sección 3.3.6 en [1] se define el cuadro denominado MovieFragmentRandomAccessBox ('mfra') que se PUEDE enviar al final de la introducción en directo para indicar EOS (final de secuencia) al canal. Debido a la lógica de introducción de Servicios multimedia de Azure, el uso de EOS (final de la secuencia) están en desuso y el cuadro 'mfra' de la introducción en directo NO DEBERÍA enviarse. Si se envía, Servicios multimedia de Azure la omite en el modo silencioso. Se recomienda usar [Restablecer canal](https://msdn.microsoft.com/library/azure/dn783458.aspx#reset_channels) para restablecer el estado del punto de introducción y también se recomienda usar [Detener el programa](https://msdn.microsoft.com/library/azure/dn783463.aspx#stop_programs) para finalizar una presentación y una secuencia.
6. La duración del fragmento MP4 DEBERÍA ser constante, con el fin de reducir el tamaño de los manifiestos de cliente y de mejorar la heurística de descarga del cliente mediante el uso de etiquetas de repetición. La duración PUEDE fluctuar para compensar las velocidades de fotogramas de no enteros.
7. La duración de fragmentos MP4 DEBERÍA estar comprendida aproximadamente entre 2 y 6 segundos.
8. Las marcas de tiempo de fragmentos MP4 e índices (TrackFragmentExtendedHeaderBox fragment\_absolute\_time y fragment\_index) DEBERÍAN llegar en orden ascendente. Aunque Servicios multimedia de Azure es resistente a fragmentos duplicados, tiene una capacidad limitada para reordenar fragmentos de acuerdo con la escala de tiempo multimedia.

##4\. Formato de protocolo: HTTP

La introducción en directo basada en MP4 fragmentado de ISO para Servicios multimedia de Microsoft Azure usa una solicitud HTTP POST de larga ejecución estándar para transmitir datos multimedia codificados empaquetados en formato MP4 fragmentado al servicio. Cada HTTP POST envía una secuencia de bits MP4 completa fragmentada ("Secuencia") desde el comienzo con cuadros iniciales (cuadro ‘ftyp’, “Live Server Manifest Box” y ‘moov’) y continuando con una secuencia de fragmentos (cuadros ‘moof’ y ‘mdat’). Consulte la sección 9.2 de [1] para la sintaxis URL para la solicitud HTTP POST. Un ejemplo de la dirección URL de POST es:

	http://customer.channel.mediaservices.windows.net/ingest.isml/streams(720p)

###Requisitos

Estos son los requisitos detallados:

1. El codificador DEBERÍA empezar la difusión enviando una solicitud HTTP POST con un "cuerpo" vacío (longitud de contenido cero) con la misma URL de introducción. Esto puede ayudar a detectar con rapidez si el extremo de introducción activo es válido y si hay alguna autenticación u otras condiciones necesarias. Por protocolo HTTP, el servidor no podrá devolver la respuesta HTTP hasta que se reciba la solicitud completa incluido el POST del cuerpo. Dada la naturaleza de larga duración del evento en directo, sin este paso,es posible que el codificador no sea capaz de detectar ningún error hasta que finalice el envío de todos los datos.
2. El codificador debe controlar los errores o los desafíos de autenticación como resultado de (1). Si (1) se realiza correctamente con una respuesta 200, continúe.
3. El codificador DEBE iniciar una nueva solicitud HTTP POST con la secuencia MP4 fragmentada. La carga DEBE empezar con los cuadros de encabezado seguidos de fragmentos. Tenga en cuenta que el cuadro 'ftyp', "Live Server Manifest Box" y 'moov' (en este orden) DEBE enviarse con cada solicitud, aunque el codificador deba volver a conectarse porque se canceló la solicitud anterior antes del final de la secuencia. 
4. El codificador DEBE usar una codificación de transferencia fragmentada para cargar, ya que es imposible predecir la longitud del contenido completa del evento en directo.
5. Cuando se termina el evento, después de enviar el último fragmento, el codificador DEBE finalizar correctamente la secuencia de mensajes de la codificación de transferencia fragmentada (la mayoría de las pilas de cliente HTTP la controlan automáticamente). El codificador debe esperar que el servicio devuelva el código de respuesta final y luego finalizar la conexión. 
6. El codificador NO DEBE usar el nombre Events() como se describe en 9.2 en [1] para la ingesta activa en Servicios multimedia de Microsoft Azure.
7. Si la solicitud HTTP POST finaliza o se agota el tiempo antes del final de la secuencia con un error TCP, el codificador DEBE emitir una nueva solicitud POST con una nueva conexión y seguir los requisitos anteriores con el requisito adicional de que el codificador debe reenviar los dos fragmentos de MP4 anterior de cada pista en la secuencia anteriores y reanudar sin introducir discontinuidades en la escala de tiempo multimedia. El reenvío de los dos últimos fragmentos de MP4 para cada pista garantiza que no hay ninguna pérdida de datos. En otras palabras, si una secuencia contiene tanto una pista de audio como una de vídeo, y se produce un error en la solicitud POST actual, el codificador debe volver a conectarse y reenviar los últimos dos fragmentos de la pista de audio, que se enviaron correctamente anteriormente, y los dos últimos fragmentos para la pista de vídeo, que se enviaron correctamente anteriormente, para asegurarse de que no hay ninguna pérdida de datos. El codificador DEBE mantener un búfer de “reenvío” de fragmentos de medios, que vuelve a enviar al volver a conectarse.

##5\. Escala de tiempo 

[[MS SSTR]](https://msdn.microsoft.com/library/ff469518.aspx) describe el uso de la "Escala de tiempo" para SmoothStreamingMedia (sección 2.2.2.1), StreamElement (sección 2.2.2.3), StreamFragmentElement(2.2.2.6) y LiveSMIL (sección 2.2.7.3.1). Si el valor de la escala de tiempo no está presente, el valor predeterminado usado es 10.000.000 (10 MHz). Aunque la especificación de formato de Smooth Streaming no bloquea el uso de otros valores de escala de tiempo, la mayoría de las implementaciones del codificador usa este valor predeterminado (10 MHz) para generar datos de introducción de Smooth Streaming. Debido a la característica de [paquetes dinámicos de Azure Media](media-services-dynamic-packaging-overview.md), se recomienda usar la escala de tiempo de 90 kHz para secuencias de vídeo y de 44,1 o 48,1 kHz para secuencias de audio. Si se usan valores de escala de tiempo diferentes para distintas secuencias, se DEBE enviar la escala de tiempo de nivel de secuencia. Consulte [[MS-SSTR]](https://msdn.microsoft.com/library/ff469518.aspx).

##6\. Definición de "secuencia"  

"Secuencia" es la unidad básica de operación en la introducción en directo para crear la presentación en directo, la conmutación por error de transmisión de secuencias y los escenarios de redundancia. "Secuencia" se define como una secuencia de bits única de MP4 fragmentado que puede contener una sola pista o varias pistas. Una presentación en directo completa podría contener una o varias secuencias en función de la configuración de los codificadores en directo. En los ejemplos siguientes se ilustran varias opciones de uso de secuencias para crear una presentación en directo completa.

**Ejemplo:**

El cliente desea crear una presentación de streaming en vivo que incluye las siguientes velocidades de bits de audio y vídeo:

Vídeo: 3000 kbps, 1500 kbps, 750 kbps

Audio: 128 kbps

###Opción 1: todas las pistas de una secuencia

En esta opción, un único codificador genera todas las pistas de audio/vídeo y se agrupan en una secuencia de bits de MP4 fragmentado que luego se envía mediante una sola conexión de HTTP POST. En este ejemplo, solo hay una secuencia para esta presentación en directo:

![imagen2][image2]

###Opción 2: Cada pista de una secuencia independiente

En esta opción, los codificadores solo colocan una pista en cada secuencia de bits de MP4 de fragmento y registran todas las secuencias a través de varias conexiones HTTP independientes. Esto se podría realizar con un codificador o varios. Desde el punto de vista de la introducción en directo, esta presentación en directo se compone de cuatro secuencias.

![imagen3][image3]

###Opción 3: Agrupar pista de audio con la pista de vídeo de velocidad de bits más baja en una secuencia

En esta opción, el cliente elige agrupar la pista de audio con la pista de vídeo de velocidad de bits más baja en una secuencia de bits de MP4 de fragmento y dejar las demás pistas de vídeo, siendo cada una de ellas su propia secuencia.


![imagen4][image4]


###Resumen

Lo que se muestra anteriormente NO es una lista exhaustiva de todas las opciones de introducción posibles para este ejemplo. De hecho, cualquier agrupación de pistas en secuencias es compatible con la introducción activa. Los clientes y los proveedores de codificación pueden elegir sus propias implementaciones en función de la complejidad de ingeniería, de la capacidad del codificador, y de las consideraciones de redundancia y conmutación por error. Sin embargo, se debe tener en cuenta que en la mayoría de los casos solo hay una pista de audio para toda la presentación activa, por lo que es importante garantizar el buen estado de la secuencia de introducción que contiene la pista de audio. Esta consideración a menudo da como resultado la colocación de la pista de audio en su propia secuencia (como en la opción 2) o su agrupación con la pista de vídeo de velocidad de bits más baja (como en la opción 3). También para una mejor redundancia y tolerancia a errores, el envío de la misma pista de audio en dos secuencias diferentes (la opción 2 con pistas de audio redundantes) o la agrupación de la pista de audio con al menos dos de las pistas de vídeo de velocidad de bits más bajas (opción 3 con audio agrupado en al menos dos secuencias de vídeo) es muy recomendable para la introducción en directo en Servicios multimedia de Microsoft Azure.

##7\. Conmutación por error del servicio 

Dada la naturaleza del streaming en vivo, resulta fundamental una buena compatibilidad de conmutación por error para garantizar la disponibilidad del servicio. Servicios multimedia de Microsoft Azure se ha diseñado para controlar los diversos tipos de errores, incluidos los errores de red, los errores de servidor, los problemas de almacenamiento, etc. Cuando se usa junto con la lógica de conmutación por error adecuada desde el lado del codificador en directo, el cliente puede lograr un servicio de transmisión directo altamente confiable desde la nube.

En esta sección, trataremos los escenarios de conmutación por error de servicio. En este caso, el error se produce en algún lugar dentro del servicio y se manifiesta como un error de red. Estas son algunas recomendaciones para la implementación del codificador para controlar la conmutación por error del servicio:


1. Use un tiempo de espera de 10 segundos para establecer la conexión TCP. Si un intento de establecer la conexión tarda más de 10 segundos, anule la operación e inténtelo de nuevo. 
2. Use un tiempo de espera breve para enviar la solicitud HTTP de fragmentos de mensajes. Si la duración del fragmento de destino MP4 es N segundos, use un tiempo de espera de envío entre N y 2N segundos; por ejemplo, use un tiempo de espera de 6 a 12 segundos si la duración del fragmento MP4 es de 6 segundos. Si se produce un tiempo de espera, restablezca la conexión, abra una nueva conexión y reanude la introducción de secuencias en la nueva conexión. 
3. Mantenga un búfer gradual con los dos últimos fragmentos, para cada pista, que se enviaron correctamente y por completo al servicio. Si la solicitud HTTP POST de una secuencia finaliza o se agota el tiempo de espera antes del final de la secuencia, abra una nueva conexión y comience otra solicitud HTTP POST, reenvíe los encabezados de secuencia, reenvíe los últimos dos fragmentos para cada pista y reanude la secuencia sin introducir una discontinuidad en la escala de tiempo multimedia. Esto reducirá la posibilidad de pérdida de datos.
4. Se recomienda que el codificador NO limite el número de reintentos para establecer una conexión ni reanude el streaming cuando se produce un error TCP.
5. Después de un error TCP:
	1. Se DEBE cerrar la conexión actual y se DEBE crear una nueva conexión para una nueva solicitud HTTP POST.
	2. La nueva URL HTTP POST DEBE ser la misma que la URL de POST inicial.
	3. El nuevo POST HTTP DEBE incluir encabezados de secuencia (cuadro ‘ftyp’, “Live Server Manifest Box” y ‘moov’) idénticos a los encabezados de secuencia del POST inicial.
	4. Se DEBEN reenviar los dos últimos fragmentos enviados para cada pista y reanudar el streaming sin introducir una discontinuidad en la escala de tiempo multimedia. Las marcas de tiempo de fragmento MP4 deben aumentar continuamente, incluso a través de solicitudes HTTP POST.
6. El codificador DEBE finalizar la solicitud HTTP POST si no se envíen datos a una velocidad acorde con la duración de fragmento MP4. Una solicitud HTTP POST que no envía datos puede impedir que los Servicios multimedia de Azure se desconecten rápidamente del codificador en el caso de una actualización del servicio. Por este motivo, el HTTP POST para pistas dispersas (señal de anuncio) DEBERÍA ser de actividad breve, finalizando tan pronto como se envíe el fragmento disperso.

##8\. Conmutación por error de codificador

La conmutación por error del codificador es el segundo tipo de escenario de conmutación por error que se debe tratar para la entrega de streaming en directo integral. En este escenario, la condición de error se produjo en el lado del codificador.

![imagen5][image5]


A continuación, se muestran las expectativas del extremo de introducción en directo cuando se produce la conmutación por error del codificador:

1. Se DEBERÍA crear una nueva instancia de codificador para continuar con el streaming, como se muestra en el diagrama anterior (secuencia para vídeo de 3000 k con línea discontinua).
2. El nuevo codificador DEBE usar la misma URL para las solicitudes HTTP POST que la instancia con errores.
3. La solicitud POST del nuevo codificador DEBE incluir los mismos cuadros de encabezado MP4 fragmentados que la instancia con errores.
4. El codificador nuevo DEBE estar sincronizado correctamente con todos los demás codificadores en ejecución para la misma presentación en directo para generar los ejemplos de audio y vídeo sincronizados con los límites de fragmentos alineados.
5. La nueva secuencia DEBE ser semánticamente equivalente a la secuencia anterior e intercambiable en el nivel de encabezado y fragmento.
6. El nuevo codificador DEBERÍA intentar minimizar la pérdida de datos. fragment\_absolute\_time y fragment\_index de los fragmentos multimedia DEBERÍAN aumentar desde el punto en que se detuvo el codificador. fragment\_absolute\_time and fragment\_index DEBERÍAN aumentar de forma continua, pero se puede introducir una discontinuidad en caso necesario. Servicios multimedia de Azure omitirá fragmentos que ya ha recibido y procesado, por lo que es mejor equivocarse en el lado del envío de los fragmentos en lugar de introducir discontinuidades en la escala de tiempo mutimedia. 

##9\. Redundancia del codificador 

Para determinados eventos en directo críticos que demandan una disponibilidad incluso mayor y una calidad de experiencia, se recomienda emplear codificadores redundantes de activo-activo para lograr una conmutación por error perfecta sin pérdida de datos.

![imagen6][image6]

Como se ilustra en el diagrama anterior, hay dos grupos de codificadores que insertan dos copias de cada secuencia de manera simultánea en el servicio en directo. Esta configuración se admite porque Servicios multimedia de Microsoft Azure tiene la capacidad de filtrar fragmentos duplicados según el id. de secuencia y la marca de tiempo del fragmento. La secuencia en directo resultante y el archivo será una copia única de todas las secuencias que es la mejor agregación posible de los dos orígenes. Por ejemplo, en un caso extremo hipotético, siempre y cuando haya un codificador (no tiene que ser el mismo) en ejecución en un momento dado en el tiempo para cada secuencia, la secuencia en directo resultante desde el servicio será continua sin pérdida de datos.

El requisito para este escenario es prácticamente el mismo que los requisitos en el caso de conmutación por error del codificador con la excepción de que el segundo conjunto de codificadores se ejecutan al mismo tiempo que los codificadores principales.

##10\. Redundancia de servicios  

Para la distribución global altamente redundante, a veces es necesario tener copia de seguridad de varias regiones para controlar los desastres regionales. Expandiendo la topología de la "Redundancia del codificador", los clientes pueden elegir tener una implementación de servicio redundante en una región distinta conectada con el segundo conjunto de codificadores. Los clientes también podrían trabajar con un proveedor de CDN para implementar un GTM (Administrador de tráfico global) delante de las dos implementaciones de servicio para enrutar el tráfico de cliente sin problemas. Los requisitos para los codificadores son los mismos que el caso de la "Redundancia del codificador", con la única excepción de que el segundo conjunto de codificadores se debe apuntar a otro extremo de introducción en directo. En el diagrama siguiente se muestra esta configuración:

![imagen7][image7]

##11\. Tipos especiales de formatos de introducción 

En esta sección se describen algunos tipos especiales de formatos de introducción en directo que están diseñados para controlar algunos escenarios concretos.

###Pista dispersa

Cuando se entrega una presentación de streaming en vivo con la experiencia de cliente enriquecida, a menudo es necesario transmitir eventos sincronizados de tiempo o señales en banda con los datos multimedia principales. Un ejemplo de esto es la inserción de anuncios activos dinámicos. Este tipo de señalización de eventos es diferente del streaming de audio y vídeo regular debido a su naturaleza dispersa. Es decir, los datos de señalización no se suelen producir de manera continua y el intervalo puede ser difícil de predecir. El concepto de pista dispersa se diseñó de manera específica para la introducción y la difusión de datos de señalización en banda.

A continuación se muestra una implementación recomendada para introducir pistas dispersas:

1. Cree una secuencia de bits MP4 fragmentada independiente que solo contenga secuencia de bits que solo contenga pistas dispersas sin pistas de audio y vídeo.
2. En el "Live Server Manifest Box" según se define en la sección 6 en [1], use el parámetro "parentTrackName" para especificar el nombre de la pista principal. Consulte la sección 4.2.1.2.1.2 en [1] para obtener más detalles.
3. En el "Live Server Manifest Box", manifestOutput se DEBE establecer en "true".
4. Dada la naturaleza dispersa del evento de señalización, se recomienda que:
	1. Al principio del evento en directo, el codificador envía los cuadros de encabezados iniciales al servicio que permitirían que el servicio registre la pista dispersa en el manifiesto del cliente.
	2. El codificador DEBERÍA finalizar la solicitud HTTP POST cuando no se están enviando datos. Un HTTP POST de larga ejecución que no envía datos puede evitar que Servicios multimedia de Azure se desconecte rápidamente del codificador en el caso de un reinicio del servidor o actualización del servicio, ya que el servidor multimedia se bloqueará temporalmente en una operación de recepción en el socket. 
	3. Durante el tiempo en el que no estén disponibles los datos de señalización, el codificador DEBERÍA cerrar la solicitud HTTP POST. Mientras la solicitud POST está activa, el codificador DEBERÍA enviar datos 
	4. Al enviar fragmentos dispersos, el codificador puede establecer el encabezado Content-Length explícito si está disponible.
	5. Al enviar un fragmento disperso con una nueva conexión, el codificador DEBERÍA empezar a enviar desde los cuadros iniciales seguidos de los fragmentos nuevos. Se trata de controlar el caso donde se produjo la conmutación por error y se está estableciendo la nueva conexión dispersa a un nuevo servidor que no ha visto antes la pista dispersa.
	6. El fragmento de pista dispersa estará disponible para el cliente cuando el fragmento de pista principal correspondiente que tenga un valor de marca de tiempo igual o superior se ponga a disposición del cliente. Por ejemplo, si el fragmento disperso tiene una marca de tiempo de t=1000, se espera después de que el cliente vea la marca de tiempo de fragmentos de vídeo 1000 o superior (suponiendo que el nombre de la pista principal es vídeo), puede descargar el fragmento disperso t=1000. Tenga en cuenta que la señal real podría usarse perfectamente para una posición diferente en la escala de tiempo de presentación para su propósito designado. En el ejemplo anterior, es posible que el fragmento disperso de t=1000 tenga una carga XML para insertar un anuncio en una posición que es unos segundos posterior.
	7. La carga del fragmento de pista dispersa puede encontrarse en varios formatos diferentes (por ejemplo, XML o texto o binario, etc.) según los diferentes escenarios. 


###Pista de audio redundante

En un escenario de transmisión por secuencias adaptativa HTTP típico (por ejemplo, Smooth Streaming o DASH), a menudo solo hay una pista de audio en toda la presentación. A diferencia de las pistas de vídeo que tienen varios niveles de calidad para que el cliente elija a partir de las condiciones de error, la pista de audio puede ser un punto de error único si se interrumpe la introducción de la secuencia que contiene la pista de audio.

Para solucionar este problema, Servicios multimedia de Microsoft Azure admite la introducción en directo de las pistas de audio redundantes. La idea es que la misma pista de audio se puede enviar varias veces en secuencias diferentes. Aunque el servicio solo registrará la pista de audio una vez en el manifiesto del cliente, puede usar las pistas de audio redundantes como copias de seguridad para recuperar fragmentos de audio si la pista de audio principal está teniendo problemas. Para introducir pistas de audio redundantes, el codificador debe:

1. Cree la misma pista de audio en varias secuencias de bits de fragmento MP4. Las pistas de audio redundantes DEBEN ser semánticamente equivalentes con el mismo número exacto de marcas de tiempo de fragmento e intercambiables en el nivel de encabezado y fragmento.
2. Asegúrese de que la entrada de "audio" en el Live Server Manifest (sección 6 en [1]) sea la misma para todas las pistas de audio redundantes.

A continuación se muestra una implementación recomendada de las pistas de audio redundantes:

1. Envíe cada pista de audio única en una secuencia de manera individual. Envíe además una secuencia redundante para cada una de estas secuencias de pista de audio, donde la segunda secuencia sea diferente de la primera únicamente por el identificador de la URL de HTTP POST: {protocolo}://{dirección de servidor}/{ruta de acceso a punto de publicación}/Streams({identificador}).
2. Use secuencias independientes para enviar las dos velocidades de bits de vídeos más bajas. Cada una de estas secuencias también DEBERÍA contener una copia de cada pista de audio única. Por ejemplo, cuando se admiten varios idiomas, estas secuencias DEBERÍAN contener pistas de audio para cada idioma.
3. Use instancias de servidor independientes (de codificador) y envíe las secuencias redundantes mencionadas en (1) y (2). 



##Rutas de aprendizaje de Servicios multimedia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Envío de comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


[image1]: ./media/media-services-fmp4-live-ingest-overview/media-services-image1.png
[image2]: ./media/media-services-fmp4-live-ingest-overview/media-services-image2.png
[image3]: ./media/media-services-fmp4-live-ingest-overview/media-services-image3.png
[image4]: ./media/media-services-fmp4-live-ingest-overview/media-services-image4.png
[image5]: ./media/media-services-fmp4-live-ingest-overview/media-services-image5.png
[image6]: ./media/media-services-fmp4-live-ingest-overview/media-services-image6.png
[image7]: ./media/media-services-fmp4-live-ingest-overview/media-services-image7.png

 

<!---HONumber=AcomDC_0211_2016-->