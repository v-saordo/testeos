



Cuando envía notificaciones de plantilla, solo es necesario proporcionar un conjunto de propiedades; en nuestro caso, enviaremos, por ejemplo, el conjunto de propiedades que contiene la versión localizada de las noticias de actualidad:

	{
		"News_English": "World News in English!",
    	"News_French": "World News in French!",
    	"News_Mandarin": "World News in Mandarin!"
	}


En esta sección se muestra cómo enviar notificaciones con una aplicación de consola

El código incluido se difunde tanto a los dispositivos de la Tienda Windows como a los iOS, dado que el back-end puede difundir a cualquiera de los dispositivos compatibles.


### Para enviar notificaciones mediante una aplicación de consola de C# 

Modifique el método `SendTemplateNotificationAsync` en la aplicación de consola que creó anteriormente con el código siguiente. Observe cómo en este caso no hay necesidad de enviar varias notificaciones para diferentes configuraciones regionales y plataformas.

        private static async void SendTemplateNotificationAsync()
        {
            // Define the notification hub.
            NotificationHubClient hub = 
				NotificationHubClient.CreateClientFromConnectionString(
					"<connection string with full access>", "<hub name>");

            // Sending the notification as a template notification. All template registrations that contain 
			// "messageParam" or "News_<local selected>" and the proper tags will receive the notifications. 
			// This includes APNS, GCM, WNS, and MPNS template registrations.
            Dictionary<string, string> templateParams = new Dictionary<string, string>();

            // Create an array of breaking news categories.
            var categories = new string[] { "World", "Politics", "Business", "Technology", "Science", "Sports"};
            var locales = new string[] { "English", "French", "Mandarin" };

            foreach (var category in categories)
            {
                templateParams["messageParam"] = "Breaking " + category + " News!";

                // Sending localized News for each tag too...
                foreach( var locale in locales)
                {
                    string key = "News_" + locale;

					// Your real localized news content would go here.
                    templateParams[key] = "Breaking " + category + " News in " + locale + "!";
                }

                await hub.SendTemplateNotificationAsync(templateParams, category);
            }
        }


Tenga en cuenta que esta simple llamada entregará la noticia localizada a **todos** los dispositivos, con independencia de la plataforma, puesto que el Centro de notificaciones crea y entrega la carga nativa correcta a todos los dispositivos suscritos a una etiqueta específica.

### Envío de la notificación con Servicios móviles

En el programador de servicios móviles, puede usar el siguiente script:

	var azure = require('azure');
    var notificationHubService = azure.createNotificationHubService('<hub name>', '<connection string with full access>');
    var notification = {
			"News_English": "World News in English!",
			"News_French": "World News in French!",
			"News_Mandarin", "World News in Mandarin!"
	}
	notificationHubService.send('World', notification, function(error) {
		if (!error) {
			console.warn("Notification successful");
		}
	});
	

<!---HONumber=AcomDC_1217_2015-->