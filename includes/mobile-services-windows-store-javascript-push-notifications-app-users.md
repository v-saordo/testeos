
A continuación, debe cambiar la manera en que se registran las notificaciones de inserción para asegurarse de que el usuario se autentique antes de que se intente el registro. Las actualizaciones de la aplicación cliente dependen de la forma en que haya implementado las notificaciones push.

###Utilización del Asistente de notificaciones push en Visual Studio 2013 Update 2 o una versión posterior.

En este método, el asistente generó nuevos archivos push.register.cs y service.js en su proyecto.

1. En el Explorador de soluciones de Visual Studio, abra el archivo de proyecto push.register.js y convierta en comentario o elimine la llamada a **addEventListener**. 

2. En el archivo de proyecto default.js, reemplace la función **login** existente por el código siguiente:
 
		// Request authentication from Mobile Services using a Facebook login.
		var login = function () {
		    return new WinJS.Promise(function (complete) {
		        client.login("facebook").done(function (results) {
		            userId = results.userId;
		            // Request a push notification channel.
		            Windows.Networking.PushNotifications
		                .PushNotificationChannelManager
		                .createPushNotificationChannelForApplicationAsync()
		                .then(function (channel) {
		                    // Register for notifications using the new channel
		                    client.push.registerNative(channel.uri);
		                });
		            refreshTodoItems();
		            var message = "You are now logged in as: " + userId;
		            var dialog = new Windows.UI.Popups.MessageDialog(message);
		            dialog.showAsync().done(complete);
		        }, function (error) {
		            userId = null;
		            var dialog = new Windows.UI.Popups
		                .MessageDialog("An error occurred during login", "Login Required");
		            dialog.showAsync().done(complete);
		        });
		    });
		}  

###Notificaciones push activadas manualmente		

En este método, agregó un código de registro desde el tutorial directamente en el archivo de proyecto default.js.

1. En el Explorador de soluciones de Visual Studio, abra el archivo de proyecto default.js y, en el controlador de eventos **onActivated**, encuentre la línea de código que llama a la función **createPushNotificationChannelForApplicationAsync**, que debería parecerse a lo siguiente:

		// Request a push notification channel.
		Windows.Networking.PushNotifications
		    .PushNotificationChannelManager
		    .createPushNotificationChannelForApplicationAsync()
		    .then(function (channel) {
		        // Register for notifications using the new channel
		        client.push.registerNative(channel.uri);
		    }); 
 
2. Mueva esta línea de código dentro de la función **login**, justo antes de la llamada a **refreshTodoItems**, de forma que la función **login** se parezca a esto:
 
		// Request authentication from Mobile Services using a Facebook login.
		var login = function () {
		    return new WinJS.Promise(function (complete) {
		        client.login("facebook").done(function (results) {
		            userId = results.userId;
		            // Request a push notification channel.
		            Windows.Networking.PushNotifications
		                .PushNotificationChannelManager
		                .createPushNotificationChannelForApplicationAsync()
		                .then(function (channel) {
		                    // Register for notifications using the new channel
		                    client.push.registerNative(channel.uri);
		                });
		            refreshTodoItems();
		            var message = "You are now logged in as: " + userId;
		            var dialog = new Windows.UI.Popups.MessageDialog(message);
		            dialog.showAsync().done(complete);
		        }, function (error) {
		            userId = null;
		            var dialog = new Windows.UI.Popups
		                .MessageDialog("An error occurred during login", "Login Required");
		            dialog.showAsync().done(complete);
		        });
		    });
		}  

<!---HONumber=Oct15_HO3-->