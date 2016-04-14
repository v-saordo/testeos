
1. En el Explorador de soluciones de Visual Studio, expanda la carpeta App\_Start y abra el archivo de proyecto WebApiConfig.cs.

2. Agregue el siguiente código al método Register después de la definición **ConfigOptions**:

        options.PushAuthorization = 
            Microsoft.WindowsAzure.Mobile.Service.Security.AuthorizationLevel.User;
 
	Esto aplica la autenticación del usuario antes de registrarse para notificaciones de inserción.

2. Haga clic con el botón secundario en el proyecto, haga clic en **Agregar** y, a continuación, haga clic en **Clase...**.

3. Asigne un nombre a la nueva clase `PushRegistrationHandler` vacía y haga clic en **Agregar**.

4. En la parte superior de la página de código, agregue las siguientes instrucciones **using**:

		using System.Threading.Tasks; 
		using System.Web.Http; 
		using System.Web.Http.Controllers; 
		using Microsoft.WindowsAzure.Mobile.Service; 
		using Microsoft.WindowsAzure.Mobile.Service.Notifications; 
		using Microsoft.WindowsAzure.Mobile.Service.Security; 

5. Reemplace la clase **PushRegistrationHandler** existente por el siguiente código:
 
	    public class PushRegistrationHandler : INotificationHandler
	    {
	        public Task Register(ApiServices services, HttpRequestContext context,
            NotificationRegistration registration)
	        {
	            try
	            {
	                // Perform a check here for user ID tags, which are not allowed.
	                if(!ValidateTags(registration))
	                {
	                    throw new InvalidOperationException(
	                        "You cannot supply a tag that is a user ID.");                    
	                }
	
	                // Get the logged-in user.
	                var currentUser = context.Principal as ServiceUser;
	
	                // Add a new tag that is the user ID.
	                registration.Tags.Add(currentUser.Id);
	
	                services.Log.Info("Registered tag for userId: " + currentUser.Id);
	            }
	            catch(Exception ex)
	            {
	                services.Log.Error(ex.ToString());
	            }
	                return Task.FromResult(true);
	        }
	
	        private bool ValidateTags(NotificationRegistration registration)
	        {
	            // Create a regex to search for disallowed tags.
	            System.Text.RegularExpressions.Regex searchTerm =
	            new System.Text.RegularExpressions.Regex(@"facebook:|google:|twitter:|microsoftaccount:",
	                System.Text.RegularExpressions.RegexOptions.IgnoreCase);
	
	            foreach (string tag in registration.Tags)
	            {
	                if (searchTerm.IsMatch(tag))
	                {
	                    return false;
	                }
	            }
	            return true;
	        }
		
	        public Task Unregister(ApiServices services, HttpRequestContext context, 
	            string deviceId)
	        {
	            // This is where you can hook into registration deletion.
	            return Task.FromResult(true);
	        }
	    }

	El método **Register** se invoca durante el registro. Esto permite agregar una etiqueta al registro que es el identificador del usuario que inició sesión. Se validan las etiquetas suministradas para evitar que un usuario se registre con el identificador de otro usuario Cuando se envía una notificación a este usuario, se recibe en este y otros dispositivos registrados que el usuario haya registrado.

6. Expanda la carpeta Controladores, abra el archivo de proyecto TodoItemController.cs, localice el método **PostTodoItem** y reemplace la línea de código que llama a **SendAsync** con el código siguiente:

        // Get the logged-in user.
		var currentUser = this.User as ServiceUser;
		
		// Use a tag to only send the notification to the logged-in user.
        var result = await Services.Push.SendAsync(message, currentUser.Id);

7. Vuelva a publicar el proyecto de servicio móvil.

Ahora, el servicio usa la etiqueta de identificador de usuario para enviar una notificación de inserción (con el texto del elemento insertado) a todos los registros que ha creado el usuario que inició sesión.
 

<!---HONumber=Oct15_HO3-->