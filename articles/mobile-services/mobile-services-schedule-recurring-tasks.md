<properties
	pageTitle="Programación de tareas back-end en un servicio móvil back-end de JavaScript | Microsoft Azure"
	description="Use el programador de Servicios móviles de Azure para definir trabajos back-end de JavaScript que se ejecutan según una programación."
	services="mobile-services"
	documentationCenter=""
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="02/11/2016"
	ms.author="glenga"/>

# Programación de trabajos periódicos en Servicios móviles

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


> [AZURE.SELECTOR]
- [.NET backend](mobile-services-dotnet-backend-schedule-recurring-tasks.md)
- [Javascript backend](mobile-services-schedule-recurring-tasks.md)

Este tema le muestra cómo usar la funcionalidad del programador de trabajos en el Portal de Azure clásico para definir el código de script de servidor que se ejecuta según la programación que establezca. En este caso, se realiza una comprobación periódica del script con un servicio remoto (Twitter) y se almacenan los resultados en una nueva tabla. Entre las demás tareas periódicas que pueden programarse se incluyen las siguientes:

+ Archivado de registros de datos antiguos o duplicados.
+ Solicitud y almacenamiento de datos externos, como tweets, entradas RSS e información de ubicación.
+ Procesamiento o cambio de tamaño de imágenes almacenadas.

Este tutorial le mostrará cómo usar el programador de trabajos para crear un trabajo programado que solicite datos de tweets de Twitter y almacene los tweets en una nueva tabla de actualizaciones:

##<a name="get-oauth-credentials"></a>Registro para obtener acceso a las API de Twitter v1.1 y almacenamiento de credenciales

[AZURE.INCLUDE [mobile-services-register-twitter-access](../../includes/mobile-services-register-twitter-access.md)]

##<a name="create-table"></a>Creación de la nueva tabla de actualizaciones

A continuación, tendrá que crear una nueva tabla en la que almacenar tweets.

2. En el [Portal de Azure clásico], haga clic en la pestaña **Datos** para el servicio móvil y haga clic en **+Crear**.

3. En **Nombre de tabla** escriba _Updates_ y haga clic en el botón de comprobación.

##<a name="add-job"></a>Creación de un nuevo trabajo programado

Ahora puede crear el trabajo programado que obtiene acceso a Twitter y almacena los datos de tweets en la nueva tabla de actualizaciones.

2. Haga clic en la pestaña **Programador** y, a continuación, en **+Crear**.

    >[AZURE.NOTE]Cuando ejecute el servicio móvil en el nivel <em>Gratis</em>, solo podrá ejecutar un trabajo programado a la vez. En los niveles de pago, puede ejecutar hasta diez trabajos programados a la vez.

3. En el cuadro de diálogo del programador, especifique _getUpdates_ para **Nombre de trabajo**, establezca el intervalo de programación y las unidades, y haga clic en el botón de comprobación.

   	De esta forma, se crea un nuevo trabajo con el nombre **getUpdates**.

4. Haga clic en el nuevo trabajo que acaba de crear, haga clic en la ficha **Script** y reemplace la función de marcador de posición **getUpdates** por el código siguiente:

		var updatesTable = tables.getTable('Updates');
		var request = require('request');
		var twitterUrl = "https://api.twitter.com/1.1/search/tweets.json?q=%23mobileservices&result_type=recent";

		// Get the service configuration module.
		var config = require('mobileservice-config');

		// Get the stored Twitter consumer key and secret.
		var consumerKey = config.twitterConsumerKey,
		    consumerSecret = config.twitterConsumerSecret
		// Get the Twitter access token from app settings.
		var accessToken= config.appSettings.TWITTER_ACCESS_TOKEN,
		    accessTokenSecret = config.appSettings.TWITTER_ACCESS_TOKEN_SECRET;

		function getUpdates() {
		    // Check what is the last tweet we stored when the job last ran
		    // and ask Twitter to only give us more recent tweets
		    appendLastTweetId(
		        twitterUrl,
		        function twitterUrlReady(url){
		            // Create a new request with OAuth credentials.
		            request.get({
		                url: url,
		                oauth: {
		                    consumer_key: consumerKey,
		                    consumer_secret: consumerSecret,
		                    token: accessToken,
		                    token_secret: accessTokenSecret
		                }},
		                function (error, response, body) {
		                if (!error && response.statusCode == 200) {
		                    var results = JSON.parse(body).statuses;
		                    if(results){
		                        console.log('Fetched ' + results.length + ' new results from Twitter');
		                        results.forEach(function (tweet){
		                            if(!filterOutTweet(tweet)){
		                                var update = {
		                                    twitterId: tweet.id,
		                                    text: tweet.text,
		                                    author: tweet.user.screen_name,
		                                    date: tweet.created_at
		                                };
		                                updatesTable.insert(update);
		                            }
		                        });
		                    }
		                } else {
		                    console.error('Could not contact Twitter');
		                }
		            });

		        });
		 }
		// Find the largest (most recent) tweet ID we have already stored
		// (if we have stored any) and ask Twitter to only return more
		// recent ones
		function appendLastTweetId(url, callback){
		    updatesTable
		    .orderByDescending('twitterId')
		    .read({success: function readUpdates(updates){
		        if(updates.length){
		            callback(url + '&since_id=' + (updates[0].twitterId + 1));
		        } else {
		            callback(url);
		        }
		    }});
		}

		function filterOutTweet(tweet){
		    // Remove retweets and replies
		    return (tweet.text.indexOf('RT') === 0 || tweet.to_user_id);
		}


   	Este script llama a la API de consulta de Twitter mediante las credenciales almacenadas para solicitar los tweets recientes que contienen el hashtag `#mobileservices`. Las respuestas y tweets duplicados se quitan de los resultados antes de almacenarse en la tabla.

    >[AZURE.NOTE]En este ejemplo se presupone que solo se insertan algunas filas en la tabla durante cada ejecución programada. En los casos en los que se inserten muchas filas en un bucle, es posible que se agoten las conexiones con el nivel gratis. En ese caso, debe realizar inserciones en los lotes. Para obtener más información, consulte [Realización de inserciones masivas](mobile-services-how-to-use-server-scripts.md#bulk-inserts).

6. Haga clic en **Ejecutar una vez** para probar el script.

   	De esta forma, se guarda y ejecuta el trabajo mientras se mantiene deshabilitado en el programador.

7. Haga clic en el botón de retroceso, en **Datos**, en la tabla **Updates**, en **Examinar** y compruebe que se han insertado los datos de Twitter en la tabla.

8. Haga clic en el botón de retroceso y en **Programador**, seleccione **getUpdates** y haga clic en **Habilitar**.

   	De esta forma, el trabajo se puede ejecutar según la programación especificada, en este caso cada hora.

Enhorabuena, ha creado correctamente un nuevo trabajo programado en el servicio móvil. Este trabajo se ejecutará como programado hasta que lo deshabilite o modifique.

## <a name="nextsteps"> </a>Otras referencias

* [Referencia del script del servidor de Servicios móviles] <br/>Obtenga más información acerca del registro y uso de scripts de servidor.

<!-- Anchors. -->
[Register for Twitter access and store credentials]: #get-oauth-credentials
[Create the new Updates table]: #create-table
[Create a new scheduled job]: #add-job
[Next steps]: #next-steps

<!-- Images. -->

<!-- URLs. -->
[Referencia del script del servidor de Servicios móviles]: http://go.microsoft.com/fwlink/?LinkId=262293
[WindowsAzure.com]: http://www.windowsazure.com/
[Portal de Azure clásico]: https://manage.windowsazure.com/
[Register your apps for Twitter login with Mobile Services]: /develop/mobile/how-to-guides/register-for-twitter-authentication
[Twitter Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[App settings]: http://msdn.microsoft.com/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7

<!---HONumber=AcomDC_0218_2016-->