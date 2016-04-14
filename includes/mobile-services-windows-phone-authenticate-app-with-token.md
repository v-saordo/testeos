
En el ejemplo anterior se mostró un inicio de sesión estándar, que requiere que el cliente se ponga en contacto con el proveedor de identidades y con el servicio móvil cada vez que se inicia la aplicación. Este método no solo es ineficaz, sino que también puede enfrentarse a problemas relacionados con el uso si varios clientes inician la aplicación al mismo tiempo. Un método mejor es almacenar en caché el token de autorización devuelto por los servicios móviles e intentar usarlo primero antes de utilizar un inicio de sesión basado en proveedores.

>[AZURE.NOTE]Puede almacenar en caché el token emitido por los servicios móviles con independencia de si es una autenticación administrada por el cliente o por el servicio. Este tutorial utiliza la autenticación administrada por el servicio.

1. En el archivo de proyecto MainPage.xaml.cs, agregue las siguientes instrucciones **using**:

		using System.IO.IsolatedStorage;
		using System.Security.Cryptography;		

2. Reemplace el método **AuthenticateAsync** por el código siguiente:

        private async System.Threading.Tasks.Task AuthenticateAsync()
        {
            string message;
            // This sample uses the Facebook provider.
            var provider = "Facebook";

            // Provide some additional app-specific security for the encryption.
            byte [] entropy = { 1, 8, 3, 6, 5 };

            // Authorization credential.
            MobileServiceUser user = null;

            // Isolated storage for the app.
            IsolatedStorageSettings settings =
                IsolatedStorageSettings.ApplicationSettings;

            while (user == null)
            {
                // Try to get an existing encrypted credential from isolated storage.                    
                if (settings.Contains(provider))
                {
                    // Get the encrypted byte array, decrypt and deserialize the user.
                    var encryptedUser = settings[provider] as byte[];
                    var userBytes = ProtectedData.Unprotect(encryptedUser, entropy);
                    user = JsonConvert.DeserializeObject<MobileServiceUser>(
                        System.Text.Encoding.Unicode.GetString(userBytes, 0, userBytes.Length));
                }
                if (user != null)
                {
                    // Set the user from the stored credentials.
                    App.MobileService.CurrentUser = user;

                    try
                    {
                        // Try to return an item now to determine if the cached credential has expired.
                        await App.MobileService.GetTable<TodoItem>().Take(1).ToListAsync();
                    }
                    catch (MobileServiceInvalidOperationException ex)
                    {
                        if (ex.Response.StatusCode == System.Net.HttpStatusCode.Unauthorized)
                        {
                            // Remove the credential with the expired token.
                            settings.Remove(provider);
                            user = null;
                            continue;
                        }
                    }
                }
                else
                {
                    try
                    {
                        // Login with the identity provider.
                        user = await App.MobileService
                            .LoginAsync(provider);

                        // Serialize the user into an array of bytes and encrypt with DPAPI.
                        var userBytes = System.Text.Encoding.Unicode
                            .GetBytes(JsonConvert.SerializeObject(user));
                        byte[] encryptedUser = ProtectedData.Protect(userBytes, entropy);

                        // Store the encrypted user credentials in local settings.
                        settings.Add(provider, encryptedUser);
                        settings.Save();
                    }
                    catch (MobileServiceInvalidOperationException ex)
                    {
                        message = "You must log in. Login Required";
                    }
                }
                message = string.Format("You are now logged in - {0}", user.UserId);
                MessageBox.Show(message);
            }
        }

	En esta versión de **AuthenticateAsync**, la aplicación intenta usar las credenciales almacenadas cifradas en el almacenamiento local para acceder al servicio móvil. Se envía una consulta simple para verificar que el token almacenado no ha expirado. Cuando se devuelve un 401, se intenta llevar a cabo un inicio de sesión normal basado en proveedor. También se realiza un inicio de sesión normal cuando no hay ninguna credencial almacenada.

	>[AZURE.NOTE]Esta aplicación comprueba si hay tokens expirados durante el inicio de sesión, pero la expiración del token puede ocurrir después de la autenticación cuando la aplicación está en uso. Para obtener una solución a los errores de gestión de autorizaciones relativas a la expiración de tokens, vea la publicación [Almacenamiento en caché y gestión de los tokens expirados en el SDK administrado de Servicios móviles de Azure)](http://blogs.msdn.com/b/carlosfigueira/archive/2014/03/13/caching-and-handling-expired-tokens-in-azure-mobile-services-managed-sdk.aspx).
	
3. Reinicie la aplicación dos veces.

	Tenga en cuenta que cuando se inicia la primera vez, se requiere de nuevo un inicio de sesión con el proveedor. Sin embargo, la segunda vez se usan las credenciales almacenadas en caché y se omite el inicio de sesión.

<!---HONumber=Oct15_HO3-->