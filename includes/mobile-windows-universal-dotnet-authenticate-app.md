
1. Abra el archivo de proyecto compartido MainPage.cs y agregue el siguiente fragmento de código a la clase MainPage:
	
		// Define a member variable for storing the signed-in user. 
        private MobileServiceUser user;

        // Define a method that performs the authentication process
        // using a Facebook sign-in. 
        private async System.Threading.Tasks.Task<bool> AuthenticateAsync()
        {
            string message;
            bool success = false;
            try
            {
                // Change 'MobileService' to the name of your MobileServiceClient instance.
                // Sign-in using Facebook authentication.
                user = await App.MobileService
                    .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
                message =
                    string.Format("You are now signed in - {0}", user.UserId);

                success = true;
            }
            catch (InvalidOperationException)
            {
                message = "You must log in. Login Required";
            }

            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
            return success;
        }

    Este código autentica al usuario con un inicio de sesión de Facebook. Si está usando un proveedor de identidades diferente al de Facebook, cambie el valor de **MobileServiceAuthenticationProvider** anterior por el valor de su proveedor.

3. Elimine o convierta en comentario la llamada al método **RefreshTodoItems** en el reemplazo del método **OnNavigatedTo** existente.

	Esto impide que los datos se carguen antes de que el usuario se haya autenticado. A continuación, agregará un botón **Iniciar sesión** a la aplicación que desencadena la autenticación.

4. Agregue el siguiente fragmento de código a la clase MainPage:

        private async void ButtonLogin_Click(object sender, RoutedEventArgs e)
        {
            // Login the user and then load data from the mobile app.
            if (await AuthenticateAsync())
            {
                // Hide the login button and load items from the mobile app.
                ButtonLogin.Visibility = Windows.UI.Xaml.Visibility.Collapsed;
                //await InitLocalStoreAsync(); //offline sync support.
                await RefreshTodoItems();
            }
        }
		
5. En el proyecto de aplicación de la Tienda Windows, abra el archivo de proyecto MainPage.xaml y agregue el siguiente elemento **Botón** inmediatamente antes del elemento que define el botón **Guardar**:

		<Button Name="ButtonLogin" Click="ButtonLogin_Click" 
                        Visibility="Visible">Sign in</Button>

6. En el proyecto de la aplicación de la Tienda de Windows Phone, agregue el siguiente elemento **Button** a **ContentPanel**, después del elemento **TextBlock**:

        <Button Grid.Row ="1" Grid.Column="1" Name="ButtonLogin" Click="ButtonLogin_Click" 
        	Margin="10, 0, 0, 0" Visibility="Visible">Sign in</Button>

8. Abra el archivo de proyecto App.xaml.cs compartido y agregue el siguiente código:

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

9. Presione la tecla F5 para ejecutar la aplicación de la Tienda Windows, haga clic en el botón **Iniciar sesión e inicie sesión** en la aplicación con el proveedor de identidad que haya elegido.

   	Cuando haya iniciado sesión correctamente, la aplicación debe ejecutarse sin errores, y podrá consultar su back-end y realizar actualizaciones de los datos.

10. Haga clic con el botón secundario en el proyecto de aplicación de la Tienda Windows Phone, haga clic en **Establecer como proyecto de inicio** y repita el paso anterior para comprobar que la aplicación de la Tienda Windows Phone también se ejecuta correctamente.

 

<!---HONumber=AcomDC_1203_2015-->