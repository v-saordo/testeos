
En el ejemplo anterior se mostró un inicio de sesión estándar, que requiere que el cliente se ponga en contacto con el proveedor de identidades y con el servicio móvil cada vez que se inicia la aplicación. Este método no solo es ineficaz, sino que también puede enfrentarse a problemas relacionados con el uso si varios clientes inician la aplicación al mismo tiempo. Un método mejor es almacenar en caché el token de autorización devuelto por los servicios móviles e intentar usarlo primero antes de utilizar un inicio de sesión basado en proveedores.

>[AZURE.NOTE]Puede almacenar en caché el token emitido por los servicios móviles con independencia de si es una autenticación administrada por el cliente o por el servicio. Este tutorial utiliza la autenticación administrada por el servicio.

1. En el archivo de proyecto default.js, reemplace la función **Login** existente por el código siguiente:

        var credential = null;
        var vault = new Windows.Security.Credentials.PasswordVault();

        // Request authentication from Mobile Services using a Facebook login.
        var login = function () {
            return new WinJS.Promise(function (complete) {
                client.login("facebook").done(function (results) {
                    // Create a credential for the returned user.
                    credential = new Windows.Security.Credentials
                        .PasswordCredential("Facebook", results.userId,
                        results.mobileServiceAuthenticationToken);
                    vault.add(credential);
                    userId = results.userId;

                    refreshTodoItems();
                    var message = "You are now logged in as: " + userId;
                    var dialog = new Windows.UI.Popups.MessageDialog(message);
                    dialog.showAsync().done(complete);
                }, function (error) {
                    var dialog = new Windows.UI.Popups
                        .MessageDialog("An error occurred during login", "Login Required");
                    dialog.showAsync().done(complete);
                });
            });
        }

2. Reemplace la función **authenticate** existente por el siguiente código:

        var authenticate = function () {
            // Try to get a stored credential from the PasswordVault.                
            try{
                credential = vault.findAllByResource("Facebook").getAt(0);
            }
            catch (error) {
                // This is expected when there's no stored credential.
            }
            
            if (credential) {
                // Set the user from the returned credential.   
                credential.retrievePassword();
                client.currentUser = {
                    "userId": credential.userName,
                    "mobileServiceAuthenticationToken": credential.password
                };

                // Try to return an item now to determine if the cached credential has expired.
                todoTable.take(1).read()
                            .done(function () {
                                refreshTodoItems();
                            }, function (error) {
                                if (error.request.status === 401) {
                                    login(credential, vault).then(function () {
                                        if (!credential) {

                                            // Authentication failed, try again.
                                            authenticate();
                                        }
                                    });
                                }                                   
                            });
            } else {

                login().then(function () {
                    if (!credential) {
                        // Authentication failed, try again.
                        authenticate();
                    }
                });
            }
        }

	En esta versión de **authenticate**, la aplicación intenta usar las credenciales almacenadas en **PasswordVault** para acceder al servicio móvil. Se envía una consulta simple para verificar que el token almacenado no ha expirado. Cuando se devuelve un 401, se intenta llevar a cabo un inicio de sesión normal basado en proveedor. También se realiza un inicio de sesión normal cuando no hay ninguna credencial almacenada.

3. Reinicie la aplicación dos veces.

	Tenga en cuenta que cuando se inicia la primera vez, se requiere de nuevo un inicio de sesión con el proveedor. Sin embargo, la segunda vez se usan las credenciales almacenadas en caché y se omite el inicio de sesión.

<!---HONumber=Oct15_HO3-->