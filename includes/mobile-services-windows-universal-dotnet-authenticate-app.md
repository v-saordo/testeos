
1. Abra el archivo de proyecto compartido MainPage.cs y agregue el siguiente fragmento de código a la clase MainPage:
	
		// Define a member variable for storing the signed-in user. 
        private MobileServiceUser user;

		// Define a method that performs the authentication process
		// using a Facebook sign-in. 
        private async System.Threading.Tasks.Task AuthenticateAsync()
        {
            while (user == null)
            {
                string message;
                try
                {
					// Change 'MobileService' to the name of your MobileServiceClient instance.
					// Sign-in using Facebook authentication.
                    user = await App.MobileService
                        .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
                    message = 
                        string.Format("You are now signed in - {0}", user.UserId);
                }
                catch (InvalidOperationException)
                {
                    message = "You must log in. Login Required";
                }
                        
                var dialog = new MessageDialog(message);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }

    Este usuario se autentica con el inicio de sesión en Facebook. Si está usando un proveedor de identidades diferente al de Facebook, cambie el valor de **MobileServiceAuthenticationProvider** anterior por el valor de su proveedor.

3. Elimine o convierta en comentario la llamada al método **RefreshTodoItems** en el reemplazo del método **OnNavigatedTo** existente.

	Esto impide que los datos se carguen antes de que el usuario se haya autenticado.

	>[AZURE.NOTE]Para autenticarse correctamente desde una aplicación de la Tienda Windows Phone 8.1, debe llamar a LoginAsync después de llamar al método **OnNavigated** y después de que el evento **Loaded** de la página se haya generado. En este tutorial, esto se hace agregando un botón **Iniciar sesión** a la aplicación.

4. Agregue el siguiente fragmento de código a la clase MainPage:

        private async void ButtonLogin_Click(object sender, RoutedEventArgs e)
        {
            // Login the user and then load data from the mobile service.
            await AuthenticateAsync();

            // Hide the login button and load items from the mobile service.
            this.ButtonLogin.Visibility = Windows.UI.Xaml.Visibility.Collapsed;
            await RefreshTodoItems();
        }
		
5. En el proyecto de aplicación de la Tienda Windows, abra el archivo de proyecto MainPage.xaml y agregue el siguiente elemento **Botón** inmediatamente antes del elemento que define el botón **Guardar**:

		<Button Name="ButtonLogin" Click="ButtonLogin_Click" 
                        Visibility="Visible">Sign in</Button>

6. En el proyecto de la aplicación de Windows Phone, agregue los siguientes elementos **Button** justo antes del elemento que define el botón **Guardar**:

		<Button Grid.Row ="1" Grid.Column="1" Name="ButtonLogin" Click="ButtonLogin_Click" 
                        Visibility="Visible" Margin="10, 0, 0, 0">Sign in</Button> 

5. Abra el archivo de proyecto compartido App.xaml.cs y agregue la siguiente instrucción using si no existe ya:

        using Microsoft.WindowsAzure.MobileServices;  
 
6. En el archivo de proyecto App.xaml.cs, agregue el siguiente código:

        protected override void OnActivated(IActivatedEventArgs args)
        {
			// Windows Phone 8.1 requires you to handle the respose from the WebAuthenticationBroker.
            #if WINDOWS_PHONE_APP
            if (args.Kind == ActivationKind.WebAuthenticationBrokerContinuation)
            {
				// Completes the sign-in process started by LoginAsync.
				// Change 'MobileService' to the name of your MobileServiceClient instance. 
                App.MobileService.LoginComplete(args as WebAuthenticationBrokerContinuationEventArgs);
            }
            #endif

            base.OnActivated(args);
        }

	Si el método **OnActivated** ya existe, agregue el bloque de código `#if...#endif`.

8. Presione la tecla F5 para ejecutar la aplicación de la Tienda Windows, haga clic en el botón **Iniciar sesión** e inicie sesión en la aplicación con el proveedor de identidad que haya elegido.

   	Cuando haya iniciado sesión correctamente, la aplicación debe ejecutarse sin errores y debe poder consultar a Servicios móviles y realizar actualizaciones de datos.

9. Haga clic con el botón secundario en el proyecto de aplicación de la Tienda Windows Phone, haga clic en **Establecer como proyecto de inicio** y repita el paso anterior para comprobar que la aplicación de la Tienda Windows Phone también se ejecuta correctamente.

<!---HONumber=Nov15_HO4-->