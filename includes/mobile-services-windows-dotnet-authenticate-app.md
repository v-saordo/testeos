
1. Abra el archivo de proyecto MainPage.xaml.cs y agregue la siguiente instrucción using:

        using Windows.UI.Popups;

2. Agregue el siguiente fragmento de código a la clase MainPage:
	
        private MobileServiceUser user;
        private async System.Threading.Tasks.Task AuthenticateAsync()
        {
            while (user == null)
            {
                string message;
                try
                {
                    user = await App.MobileService
                        .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
                    message = 
                        string.Format("You are now logged in - {0}", user.UserId);
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

    De esta forma se crea una variable de miembro para el almacenamiento del usuario actual y un método para administrar el proceso de autenticación. El usuario se autentica mediante el inicio de sesión en Facebook. Si está usando un proveedor de identidades diferente al de Facebook, cambie el valor de **MobileServiceAuthenticationProvider** anterior por el valor de su proveedor.

3. Reemplace la anulación del método **OnNavigatedTo** existente por el siguiente método que llama al nuevo método **Authenticate**:

        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            await AuthenticateAsync();
            RefreshTodoItems();
        }
		
4. Presione la tecla F5 para ejecutar la aplicación e iniciar sesión en la aplicación con el proveedor de identidades seleccionado.

   	Cuando haya iniciado sesión correctamente, la aplicación debe ejecutarse sin errores y debe poder consultar a Servicios móviles y realizar actualizaciones de datos.

<!---HONumber=Oct15_HO3-->