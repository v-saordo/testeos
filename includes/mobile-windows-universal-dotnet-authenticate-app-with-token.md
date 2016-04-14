
1. En el archivo de proyecto MainPage.xaml.cs, agregue las siguientes instrucciones **using**:

		using System.Linq;		
		using Windows.Security.Credentials;

2. Reemplace el método **AuthenticateAsync** por el código siguiente:

        private async System.Threading.Tasks.Task<bool> AuthenticateAsync()
        {
            string message;
            bool success = false;

            // This sample uses the Facebook provider.
            var provider = MobileServiceAuthenticationProvider.Facebook;

            // Use the PasswordVault to securely store and access credentials.
            PasswordVault vault = new PasswordVault();
            PasswordCredential credential = null;

            try
            {
                // Try to get an existing credential from the vault.
                credential = vault.FindAllByResource(provider.ToString()).FirstOrDefault();
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

                // Consider adding a check to determine if the token is 
                // expired, as shown in this post: http://aka.ms/jww5vp.

                success = true;
                message = string.Format("Cached credentials for user - {0}", user.UserId);
            }
            else
            {
                try
                {
                    // Login with the identity provider.
                    user = await App.MobileService
                        .LoginAsync(provider);

                    // Create and store the user credentials.
                    credential = new PasswordCredential(provider.ToString(),
                        user.UserId, user.MobileServiceAuthenticationToken);
                    vault.Add(credential);

                    success = true;
                    message = string.Format("You are now logged in - {0}", user.UserId);
                }
                catch (MobileServiceInvalidOperationException)
                {
                    message = "You must log in. Login Required";
                }
            }
            
            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();

            return success;
        }

	En esta versión de **AuthenticateAsync**, la aplicación intenta usar las credenciales almacenadas en **PasswordVault** para acceder al servicio. También se realiza un inicio de sesión normal cuando no hay ninguna credencial almacenada.

	>[AZURE.NOTE]Es posible que un token almacenado en la memoria caché haya expirado, y la expiración del token también puede ocurrir después de la autenticación cuando la aplicación está en uso. Para obtener información sobre cómo determinar si un token ha caducado, consulte [Buscar tokens de autenticación expirados](http://aka.ms/jww5vp). Para obtener una solución a los errores de gestión de autorizaciones relativas a la expiración de tokens, vea la publicación [Almacenamiento en caché y gestión de los tokens expirados en el SDK administrado de Servicios móviles de Azure)](http://blogs.msdn.com/b/carlosfigueira/archive/2014/03/13/caching-and-handling-expired-tokens-in-azure-mobile-services-managed-sdk.aspx).

3. Reinicie la aplicación dos veces.

	Tenga en cuenta que cuando se inicia la primera vez, se requiere de nuevo un inicio de sesión con el proveedor. Sin embargo, la segunda vez se usan las credenciales almacenadas en caché y se omite el inicio de sesión.

<!---HONumber=AcomDC_0211_2016-->