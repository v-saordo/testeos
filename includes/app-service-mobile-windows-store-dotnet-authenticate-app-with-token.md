
En el ejemplo anterior se mostró un inicio de sesión estándar, que requiere que el cliente se ponga en contacto con el proveedor de identidades y con el servicio de la aplicación cada vez que se inicia la aplicación. Este método no solo es ineficaz, sino que también puede enfrentarse a problemas relacionados con el uso si varios clientes inician la aplicación al mismo tiempo. Un método mejor es almacenar en caché el token de autorización devuelto por su servicio de aplicación e intentar usarlo primero antes de utilizar un inicio de sesión basado en proveedores.

>[AZURE.NOTE]Puede almacenar en caché el token emitido por los servicios de aplicaciones con independencia de si es una autenticación administrada por el cliente o por el servicio. Este tutorial utiliza la autenticación administrada por el servicio.

1. En el archivo de proyecto MainPage.xaml.cs, agregue las siguientes instrucciones **using**:

		using System.Linq;		
		using Windows.Security.Credentials;

2. Reemplace el método **AuthenticateAsync** por el código siguiente:

        private async System.Threading.Tasks.Task AuthenticateAsync()
        {
            string message;
            // This sample uses the Facebook provider.
            var provider = "Facebook";
              
            // Use the PasswordVault to securely store and access credentials.
            PasswordVault vault = new PasswordVault();
            PasswordCredential credential = null;

            while (credential == null)
            {
                try
                {
                    // Try to get an existing credential from the vault.
                    credential = vault.FindAllByResource(provider).FirstOrDefault();
                }
                catch (Exception)
                {
                    // When there is no matching resource an error occurs, which we ignore.
                }

                if (credential != null)
                {
                    // Create a user from the stored credentials.
                    user = new MobileServiceUser(credential.UserName);
                    credential.RetrievePassword();
                    user.MobileServiceAuthenticationToken = credential.Password;
                    
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
                            vault.Remove(credential);
                            credential = null;
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

                        // Create and store the user credentials.
                        credential = new PasswordCredential(provider,
                            user.UserId, user.MobileServiceAuthenticationToken);
                        vault.Add(credential);
                    }
                    catch (MobileServiceInvalidOperationException ex)
                    {
                        message = "You must log in. Login Required";
                    }
                }
                message = string.Format("You are now logged in - {0}", user.UserId);
                var dialog = new MessageDialog(message);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }

	En esta versión de **AuthenticateAsync**, la aplicación intenta usar las credenciales almacenadas en **PasswordVault** para acceder al servicio móvil. Se envía una consulta simple para verificar que el token almacenado no ha expirado. Cuando se devuelve un 401, se intenta llevar a cabo un inicio de sesión normal basado en proveedor. También se realiza un inicio de sesión normal cuando no hay ninguna credencial almacenada.

	>[AZURE.NOTE]Esta aplicación comprueba si hay tokens expirados durante el inicio de sesión, pero la expiración del token puede ocurrir después de la autenticación cuando la aplicación está en uso. Para obtener una solución a los errores de gestión de autorizaciones relativas a la expiración de tokens, vea la publicación [Almacenamiento en caché y gestión de los tokens expirados en el SDK administrado de Servicios móviles de Azure)](http://blogs.msdn.com/b/carlosfigueira/archive/2014/03/13/caching-and-handling-expired-tokens-in-azure-mobile-services-managed-sdk.aspx).

3. Reinicie la aplicación dos veces.

	Tenga en cuenta que cuando se inicia la primera vez, se requiere de nuevo un inicio de sesión con el proveedor. Sin embargo, la segunda vez se usan las credenciales almacenadas en caché y se omite el inicio de sesión.

<!---HONumber=Oct15_HO3-->