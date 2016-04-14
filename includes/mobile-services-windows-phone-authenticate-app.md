1. Abra el archivo de proyecto mainpage.xaml.cs y agregue el siguiente fragmento de código a la clase MainPage:
	
        private MobileServiceUser user;
        private async Task Authenticate()
        {
            while (user == null)
            {
                string message;
                try
                {
                    user = await App.MobileServiceDotNetClient.LoginAsync(MobileServiceAuthenticationProvider.Twitter);
                    message = string.Format("You are now logged in - {0}", user.UserId);
                }
                catch (InvalidOperationException)
                {
                    message = "You must log in. Login Required";
                }

                var dialog = new MessageDialog(message);
                await dialog.ShowAsync();
            }
        }

    De esta forma se crea una variable de miembro para el almacenamiento del usuario actual y un método para administrar el proceso de autenticación. El usuario se autentica con el inicio de sesión de Twitter.

    >[AZURE.NOTE]Si está usando un proveedor de identidades diferente al de Twitter, cambie el valor de <strong>MobileServiceAuthenticationProvider</strong> anterior por el valor de su proveedor.</p> </div>

2. Elimine o convierta en comentario el método **OnNavigatedTo** existente y reemplácelo por el siguiente método que administra el evento **Loaded** para la página.

        async void MainPage_Loaded(object sender, RoutedEventArgs e)
        {
            await Authenticate();
            RefreshTodoItems();
        }

   	Este método llama al nuevo método **Authenticate**.

3. Reemplace el constructor MainPage con el siguiente código:

        // Constructor
        public MainPage()
        {
            InitializeComponent();
            this.Loaded += MainPage_Loaded;
        }

   	Este constructor también registra el controlador para el evento Loaded.
		
4. Presione la tecla F5 para ejecutar la aplicación e iniciar sesión en la aplicación con el proveedor de identidades seleccionado.

   	Cuando haya iniciado sesión correctamente, la aplicación debe ejecutarse sin errores y debe poder consultar a Servicios móviles y realizar actualizaciones de datos.

<!---HONumber=AcomDC_0211_2016-->