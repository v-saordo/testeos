
A continuación, debe cambiar la manera en que se registran las notificaciones de inserción para asegurarse de que el usuario se autentique antes de que se intente el registro. Las actualizaciones de la aplicación cliente dependen de la forma en que haya implementado las notificaciones de inserción.

###Utilización del Asistente para agregar notificaciones de inserción en Visual Studio 2013 Update 2 o una versión posterior.

En este método, el asistente generó un nuevo archivo push.register.cs en su proyecto.

1. En el Explorador de soluciones de Visual Studio, abra el archivo de proyecto app.xaml.cs y en el controlador de eventos **OnLaunched**, convierta en comentario o elimine la llamada al método **UploadChannel**. 

2. Abra el nuevo archivo de proyecto push.register.cs y reemplace el método **UploadChannel** por el código siguiente:

		public async static void UploadChannel()
		{
		    var channel = 
		        await Windows.Networking.PushNotifications.PushNotificationChannelManager
		        .CreatePushNotificationChannelForApplicationAsync();
		
		    try
		    {
		        // Create a native push notification registration.
		        await App.MobileService.GetPush().RegisterNativeAsync(channel.Uri);		        
		
		    }
		    catch (Exception exception)
		    {
		        HandleRegisterException(exception);
		    }
		}

	Con esto se garantiza que el registro se realiza con la misma instancia del cliente que tiene las credenciales de usuario autenticadas. De lo contrario, el registro producirá un error "No autorizado" (401).

3. Abra el archivo de proyecto compartido MainPage.cs y sustituya el controlador **ButtonLogin\_Click** por lo siguiente:

        private async void ButtonLogin_Click(object sender, RoutedEventArgs e)
        {
            // Login the user and then load data from the mobile service.
            await AuthenticateAsync();
			todolistPush.UploadChannel();

            // Hide the login button and load items from the mobile service.
            this.ButtonLogin.Visibility = Windows.UI.Xaml.Visibility.Collapsed;
            await RefreshTodoItems();
        }

	De esta forma, se garantiza que la autenticación se produce antes de que se intente el registro de inserción.

4. 	En el código anterior, debe reemplazar el nombre de la clase de inserción generada (`todolistPush`) por el nombre de la clase que ha generado el asistente, normalmente en el formato <code><em>mobile\_service</em>Push</code>.

###Notificaciones de inserción activadas manualmente		

En este método, agregó un código de registro desde el tutorial directamente en el archivo de proyecto app.xaml.cs.

1. En el Explorador de soluciones de Visual Studio, abra el archivo de proyecto app.xaml.cs y en el controlador de eventos **OnLaunched**, convierta en comentario o elimine la llamada a **InitNotificationsAsync**. 
 
2. Cambie la accesibilidad del método **InitNotificationsAsync** de `private` a `public` y agregue el modificador `static`.

3. Abra el archivo de proyecto compartido MainPage.cs y sustituya el controlador **ButtonLogin\_Click** por lo siguiente:

        private async void ButtonLogin_Click(object sender, RoutedEventArgs e)
        {
            // Login the user and then load data from the mobile service.
            await AuthenticateAsync();
			App.InitNotificationsAsync();

            // Hide the login button and load items from the mobile service.
            this.ButtonLogin.Visibility = Windows.UI.Xaml.Visibility.Collapsed;
            await RefreshTodoItems();
        }
	
	De esta forma, se garantiza que la autenticación se produce antes de que se intente el registro de inserción.

<!---HONumber=Oct15_HO3-->