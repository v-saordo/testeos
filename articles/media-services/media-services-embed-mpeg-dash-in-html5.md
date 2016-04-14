<properties 
	pageTitle="Incrustación de un vídeo de transmisión por secuencias adaptativa MPEG-DASH en una aplicación HTML5 con DASH.js" 
	description="En este vídeo se muestra cómo incrustar un vídeo de transmisión por secuencias adaptativa MPEG-DASH en una aplicación HTML5 con DASH.js" 
	authors="Juliako" 
	manager="dwrede" 
	editor="" 
	services="media-services" 
	documentationCenter=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
 	ms.date="02/03/2016"  
	ms.author="juliako"/>


#Incrustación de un vídeo de transmisión por secuencias adaptativa MPEG-DASH en una aplicación HTML5 con DASH.js

##Información general

MPEG-DASH es una norma ISO para la transmisión por secuencias adaptativa de contenido de vídeo, lo que ofrece ventajas significativas para aquellos que desean ofrecer salida de streaming de vídeo adaptable de alta calidad. Con MPEG-DASH, la secuencia de vídeo se baja automáticamente a una definición inferior cuando la red está saturada. Esto reduce la probabilidad de que el usuario vea un vídeo "pausado" mientras el reproductor descarga los siguientes segundos para reproducirlos (también conocido como almacenamiento en búfer). A medida que se reduce la congestión de la red, el reproductor de vídeo a su vez volverá a una secuencia de mayor calidad. Esta capacidad para adaptar el ancho de banda necesario también produce una hora de inicio más rápida para el vídeo. Esto significa que los primeros segundos se pueden reproducir en un segmento de calidad inferior rápido para descargar y luego pasan a una calidad superior una vez se ha almacenado en búfer el contenido suficiente.

Dash.js es un reproductor de vídeo MPEG-DASH de código abierto escrito en JavaScript. Su objetivo es proporcionar un reproductor sólido entre plataformas que se pueda reutilizar libremente en aplicaciones que requieren reproducción de vídeo. Ofrece reproducción MPEG-DASH en cualquier explorador que admite las extensiones de origen multimedia (MSE) W3C hoy en día, es decir, Chrome, Microsoft Edge e IE11 (otros exploradores han indicado su intención de ser compatibles con MSE). Para obtener más información sobre DASH.js, vea el repositorio de GitHub dash.js.


##Creación de un reproductor de vídeo de streaming basado en explorador

Para crear una página web sencilla que muestre un reproductor de vídeo con los controles esperados como reproducir, pausa, rebobinar, etc., necesitará:

1. Crear una página HTML
1. Agregar la etiqueta de vídeo
1. Agregar el reproductor dash.js
1. Inicializar el reproductor
1. Agregar algún estilo CSS
1. Ver los resultados en un explorador que implemente MSE

La inicialización del reproductor se puede completar con tan solo unas líneas de código JavaScript. Con dash.js, es realmente así de sencillo incrustar vídeo MPEG-DASH en sus aplicaciones basadas en explorador.

##Creación de la página HTML

El primer paso es crear una página HTML estándar con el elemento <video>, guardar este archivo como basicPlayer.html, como se muestra en el ejemplo siguiente:
	
	<!DOCTYPE html>
	<html>
	  <head><title>Adaptive Streaming in HTML5</title></head>
	  <body>
	    <h1>Adaptive Streaming with HTML5</h1>
	    <video id="videoplayer" controls></video>
	  </body>
	</html>

##Adición del reproductor dash.js

Para agregar la implementación de referencia de dash.js a la aplicación, deberá incluir el archivo dash.all.js desde la 1.0 versión del proyecto dash.js. Este se debe guardar en la carpeta de JavaScript de la aplicación. Este archivo es un archivo de conveniencia que reúne todo el código de dash.js necesario en un solo archivo. Si echa un vistazo al repositorio dash.js, observará los archivos individuales, probará código y mucho más, pero si todo lo que quiere hacer es usar dash.js, el archivo dash.all.js es lo que necesita.

Para agregar el reproductor de dash.js a sus aplicaciones, agregue una etiqueta de script a la sección de encabezado del archivo basicPlayer.html:

	<!-- DASH-AVC/265 reference implementation -->
	< script src="js/dash.all.js"></script>


A continuación, cree una función para inicializar el reproductor cuando se cargue la página. Agregue el siguiente script después de la línea en la que carga dash.all.js:

	<script>
	// setup the video element and attach it to the Dash player
	function setupVideo() {
	  var url = "http://wams.edgesuite.net/media/MPTExpressionData02/BigBuckBunny_1080p24_IYUV_2ch.ism/manifest(format=mpd-time-csf)";
	  var context = new Dash.di.DashContext();
	  var player = new MediaPlayer(context);
	                  player.startup();
	                  player.attachView(document.querySelector("#videoplayer"));
	                  player.attachSource(url);
	}
	</script>

Esta función crea primero un elemento DashContext. Se usa para configurar la aplicación para un entorno de tiempo de ejecución específico. Desde un punto de vista técnico, define las clases que debería usar el marco de inserción de dependencias al construir la aplicación. En la mayoría de los casos, usará Dash.di.DashContext.

A continuación, cree una instancia de la clase principal del marco de dash.js, MediaPlayer. Esta clase contiene los métodos principales necesarios, como reproducción y pausa, administra la relación con el elemento de vídeo y también la interpretación del archivo de descripción de presentación multimedia (MPD) que describe el vídeo que se va a reproducir.

Se llama a la función startup() de la clase MediaPlayer para asegurarse de que el reproductor está listo para reproducir vídeo. Entre otras cuestiones, esta función garantiza que se han cargado todas las clases necesarias (según se definen por el contexto). Cuando el reproductor esté listo, puede asociar el elemento de vídeo mediante la función attachView(). Esto permite al MediaPlayer insertar la secuencia de vídeo en el elemento y controlar además la reproducción de controles según sea necesario.

Pase la URL del archivo MPD al MediaPlayer para que sepa cuál el vídeo que se espera reproducir. La función setupVideo() recién creada deberá ejecutarse una vez que la página se haya cargado por completo. Haga esto mediante el evento onload del elemento body. Cambie el elemento <body> a:

	<body onload="setupVideo()">

Por último, establezca el tamaño del elemento de vídeo mediante CSS. En un entorno de streaming adaptable, esto es especialmente importante porque el tamaño del vídeo que se reproduce puede cambiar a medida que la reproducción se adapta a las condiciones de red cambiantes. En esta sencilla demo simplemente debe forzar el elemento de vídeo para que sea el 80 % de la ventana del explorador disponible agregando el CSS siguiente a la sección inicial de la página:
	
	<style>
	video {
	  width: 80%;
	  height: 80%;
	}
	</style>

##Reproducción de un vídeo

Para reproducir un vídeo, dirija el explorador al archivo basicPlayback.html y haga clic en Reproducir en el reproductor de vídeo que se muestra.


##Rutas de aprendizaje de Servicios multimedia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Envío de comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##Otras referencias

[Desarrollo de aplicaciones para reproductor de vídeo](media-services-develop-video-players.md)

[Repositorio dash.js de GitHub](https://github.com/Dash-Industry-Forum/dash.js)

<!---HONumber=AcomDC_0211_2016-->