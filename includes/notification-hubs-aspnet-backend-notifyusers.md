## Creación del proyecto WebAPI

El nuevo back-end ASP.NET WebAPI se creará en las secciones siguientes y tendrá tres propósitos principales:

1. **Autenticación de clientes**: más adelante se agregará un controlador de mensajes para autenticar las solicitudes de cliente y asociar al usuario con la solicitud.
2. **Registros de notificaciones de cliente**: más adelante agregará un controlador para manejar los registros nuevos de un dispositivo cliente para que reciba notificaciones. El nombre de usuario autenticado se agregará automáticamente al registro como una [etiqueta](https://msdn.microsoft.com/library/azure/dn530749.aspx).
3. **Envío de notificaciones a clientes**: más adelante, también agregará un controlador para proporcionar una forma en que un usuario desencadenará una inserción segura a los dispositivos y clientes asociados con la etiqueta. 

Los pasos siguientes muestran cómo crear el nuevo back-end de ASP.NET WebAPI:


> [AZURE.NOTE] **Importante**: antes de iniciar este tutorial, asegúrese de que tiene instalada la versión más reciente del Administrador de paquetes de NuGet. Para comprobarlo, inicie Visual Studio. En el menú **Herramientas**, haga clic en **Extensiones y actualizaciones**. Busque **Administrador de paquetes de NuGet para Visual Studio 2013** y asegúrese de que tiene la versión 2.8.50313.46 o posterior. Si no es así, desinstale e instale de nuevo el Administrador de paquetes de NuGet.
> 
> ![][B4]

> [AZURE.NOTE] Asegúrese de que ha instalado el [SDK de Azure](https://azure.microsoft.com/downloads/) para Visual Studio para la implementación de sitios web.

1. Inicie Visual Studio o Visual Studio Express. Haga clic en **Explorador de servidores** e inicie sesión en su cuenta de Azure. Visual Studio necesitará que inicies sesión para crear los recursos del sitio web en su cuenta.
2. En Visual Studio, haga clic en **Archivo** y, a continuación, en **Nuevo** y en **Proyecto**, expanda **Plantillas**, **Visual C#** y, a continuación, haga clic en **Web** y **Aplicación web ASP.NET**, escriba el nombre **AppBackendy**, a continuación, haga clic en **Aceptar**. 
	
	![][B1]

3. En el cuadro de diálogo **Nuevo proyecto ASP.NET**, haga clic en **API web** y, a continuación, en **Aceptar**.

	![][B2]

4. En el cuadro de diálogo **Configurar Microsoft Azure Web App**, elija una suscripción y un **Plan del servicio de aplicaciones** que usted ya creó. También puede elegir **Crear un nuevo plan de servicio de aplicaciones** y crear uno desde el cuadro de diálogo. No es necesario una base de datos para este tutorial. Una vez seleccionado el plan de servicio de aplicaciones, haga clic en **Aceptar** para crear el proyecto.

	![][B5]



## Autenticación de clientes en el back-end de WebAPI

En esta sección creará una nueva clase de controlador de mensajes llamada **AuthenticationTestHandler** para el back-end nuevo. Esta clase deriva de [DelegatingHandler](https://msdn.microsoft.com/library/system.net.http.delegatinghandler.aspx) y se agrega como controlador de mensajes, para que así pueda procesar todas las solicitudes entrantes en el back-end.



1. En el Explorador de soluciones, haga clic con el botón derecho en el proyecto **AppBackend**, haga clic en **Agregar** y, a continuación, haga clic en **Clase**. Asigne a la nueva clase el nombre **AuthenticationTestHandler.cs** y haga clic en **Agregar** para generar la clase. Para simplificar, esta clase se usa para autenticar a los usuarios con *Autenticación básica*. Tenga en cuenta que su aplicación puede utilizar cualquier esquema de autenticación.

2. En AuthenticationTestHandler.cs, agregue las siguientes instrucciones `using`:

        using System.Net.Http;
        using System.Threading;
        using System.Security.Principal;
        using System.Net;
        using System.Web;

3. En AuthenticationTestHandler.cs, reemplace la definición de clase `AuthenticationTestHandler` con lo siguiente:

	Este controlador autoriza la solicitud cuando se cumplen las tres condiciones siguientes: * La solicitud se incluye en un encabezado *Authorization*. * La solicitud emplea autenticación *básica*. * La cadena de nombre de usuario y la cadena de contraseña son la misma cadena.

	De lo contrario, se rechazará la solicitud. Este no es un enfoque de autorización y autenticación real. Es solo un ejemplo muy simple para este tutorial.

	Si el mensaje de solicitud es autenticado y autorizado por el `AuthenticationTestHandler`, el usuario de autenticación básica se adjuntará a la solicitud actual en el [HttpContext](https://msdn.microsoft.com/library/system.web.httpcontext.current.aspx). Otro controlador (RegisterController) usará después la información de usuario en HttpContext para agregar una [etiqueta](https://msdn.microsoft.com/library/azure/dn530749.aspx) a la solicitud de registro de notificación.

		public class AuthenticationTestHandler : DelegatingHandler
	    {
	        protected override Task<HttpResponseMessage> SendAsync(
	        HttpRequestMessage request, CancellationToken cancellationToken)
	        {
	            var authorizationHeader = request.Headers.GetValues("Authorization").First();
	
	            if (authorizationHeader != null && authorizationHeader
	                .StartsWith("Basic ", StringComparison.InvariantCultureIgnoreCase))
	            {
	                string authorizationUserAndPwdBase64 =
	                    authorizationHeader.Substring("Basic ".Length);
	                string authorizationUserAndPwd = Encoding.Default
	                    .GetString(Convert.FromBase64String(authorizationUserAndPwdBase64));
	                string user = authorizationUserAndPwd.Split(':')[0];
	                string password = authorizationUserAndPwd.Split(':')[1];
	
	                if (verifyUserAndPwd(user, password))
	                {
	                    // Attach the new principal object to the current HttpContext object
	                    HttpContext.Current.User =
	                        new GenericPrincipal(new GenericIdentity(user), new string[0]);
	                    System.Threading.Thread.CurrentPrincipal =
	                        System.Web.HttpContext.Current.User;
	                }
	                else return Unauthorized();
	            }
	            else return Unauthorized();
	
	            return base.SendAsync(request, cancellationToken);
	        }
	
	        private bool verifyUserAndPwd(string user, string password)
	        {
	            // This is not a real authentication scheme.
	            return user == password;
	        }
	
	        private Task<HttpResponseMessage> Unauthorized()
	        {
	            var response = new HttpResponseMessage(HttpStatusCode.Forbidden);
	            var tsc = new TaskCompletionSource<HttpResponseMessage>();
	            tsc.SetResult(response);
	            return tsc.Task;
	        }
	    }

	> [AZURE.NOTE] **Nota de seguridad**: la clase `AuthenticationTestHandler` no proporciona una autenticación verdadera. Se utiliza únicamente para simular una autenticación básica y no es segura. Debe implementar un mecanismo de autenticación seguro en las aplicaciones y servicios de producción.

4. Agregue el siguiente código al final del método `Register` en la clase **App\_Start/WebApiConfig.cs** para registrar el controlador de mensajes:

		config.MessageHandlers.Add(new AuthenticationTestHandler());

5. Guarde los cambios.

## Registro para recibir notificaciones mediante el back-end de WebAPI

En esta sección, agregaremos un nuevo controlador al back-end de WebAPI para gestionar las solicitudes de registro de un usuario y un dispositivo para recibir notificaciones mediante la biblioteca de cliente para centros de notificaciones. El controlador agregará una etiqueta de usuario al usuario que el `AuthenticationTestHandler` autenticó y adjuntó al HttpContext. La etiqueta tendrá el formato de cadena, `"username:<actual username>"`.


 

1. En el Explorador de soluciones, haga clic con el botón derecho en el proyecto **AppBackend** y, a continuación, seleccione **Administrar paquetes de NuGet**.

2. A la izquierda, haga clic en **En línea** y busque **Microsoft.Azure.NotificationHubs** en el cuadro **Buscar**.

3. En la lista de resultados, haga clic en **Centros de notificaciones de Microsoft Azure ** y luego en **Instalar**. Complete la instalación y, a continuación, cierre la ventana del administrador de paquetes de NuGet.

	Esto agrega una referencia al SDK de los Centros de notificaciones de Azure mediante el <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">paquete NuGet de Centros Microsoft.Azure.Notification</a>.

4. Ahora crearemos un nuevo archivo de clase que representa las distintas notificaciones seguras que se enviarán. En una implementación completa, las notificaciones se almacenan en una base de datos. Por simplicidad, este tutorial las almacena en memoria. En el Explorador de soluciones, haga clic con el botón derecho en la carpeta **Modelos**, haga clic en **Agregar** y, a continuación, haga clic en **Clase**. Después de asignar el nombre a la nueva clase **Notifications.cs**, haga clic en **Agregar** para generar la clase.

	![][B6]

5. En Notifications.cs, agregue la siguiente instrucción `using` en la parte superior del archivo:

        using Microsoft.Azure.NotificationHubs;

6. Reemplace la definición de clase `Notifications` por lo siguiente y asegúrese de reemplazar los dos marcadores de posición por la cadena de conexión (con acceso total) de su centro de notificaciones, y el nombre del centro (disponible en el [Portal de Azure clásico](http://manage.windowsazure.com)):

		public class Notifications
        {
            public static Notifications Instance = new Notifications();
        
            public NotificationHubClient Hub { get; set; }

            private Notifications() {
                Hub = NotificationHubClient.CreateClientFromConnectionString("<your hub's DefaultFullSharedAccessSignature>", 
																			 "<hub name>");
            }
        }



7. A continuación, creamos un nuevo controlador denominado **RegisterController**. En el Explorador de soluciones, haga clic con el botón derecho en la carpeta **Controladores**, a continuación en **Agregar** y, por último, en **Controlador**. Haga clic en el elemento **Controlador Web API 2 - Vacío** y, a continuación, en **Agregar**. Nombre a la nueva clase **RegisterController** y luego haga clic de nuevo en **Agregar** para generar el controlador.

	![][B7]

	![][B8]

8. En RegisterController.cs, agregue las siguientes instrucciones `using`:

        using Microsoft.Azure.NotificationHubs;
		using Microsoft.Azure.NotificationHubs.Messaging;
        using AppBackend.Models;
        using System.Threading.Tasks;
        using System.Web;

9. Agregue el siguiente código a la definición de clase `RegisterController`. Observe que, en este código, agregamos una etiqueta de usuario para el usuario adjunto a HttpContext. El usuario se autenticó y adjuntó a HttpContext a través del filtro de mensaje que agregamos, `AuthenticationTestHandler`. También puede realizar comprobaciones opcionales para comprobar que el usuario puede registrarse para las etiquetas solicitadas.

		private NotificationHubClient hub;

        public RegisterController()
        {
            hub = Notifications.Instance.Hub;
        }

        public class DeviceRegistration
        {
            public string Platform { get; set; }
            public string Handle { get; set; }
            public string[] Tags { get; set; }
        }

        // POST api/register
        // This creates a registration id
        public async Task<string> Post(string handle = null)
        {
            string newRegistrationId = null;
            
            // make sure there are no existing registrations for this push handle (used for iOS and Android)
            if (handle != null)
            {
                var registrations = await hub.GetRegistrationsByChannelAsync(handle, 100);

                foreach (RegistrationDescription registration in registrations)
                {
                    if (newRegistrationId == null)
                    {
                        newRegistrationId = registration.RegistrationId;
                    }
                    else
                    {
                        await hub.DeleteRegistrationAsync(registration);
                    }
                }
            }

            if (newRegistrationId == null) 
				newRegistrationId = await hub.CreateRegistrationIdAsync();

            return newRegistrationId;
        }

        // PUT api/register/5
        // This creates or updates a registration (with provided channelURI) at the specified id
        public async Task<HttpResponseMessage> Put(string id, DeviceRegistration deviceUpdate)
        {
            RegistrationDescription registration = null;
            switch (deviceUpdate.Platform)
            {
                case "mpns":
                    registration = new MpnsRegistrationDescription(deviceUpdate.Handle);
                    break;
                case "wns":
                    registration = new WindowsRegistrationDescription(deviceUpdate.Handle);
                    break;
                case "apns":
                    registration = new AppleRegistrationDescription(deviceUpdate.Handle);
                    break;
                case "gcm":
                    registration = new GcmRegistrationDescription(deviceUpdate.Handle);
                    break;
                default:
                    throw new HttpResponseException(HttpStatusCode.BadRequest);
            }

            registration.RegistrationId = id;
            var username = HttpContext.Current.User.Identity.Name;

            // add check if user is allowed to add these tags
            registration.Tags = new HashSet<string>(deviceUpdate.Tags);
            registration.Tags.Add("username:" + username);

            try
            {
                await hub.CreateOrUpdateRegistrationAsync(registration);
            }
            catch (MessagingException e)
            {
                ReturnGoneIfHubResponseIsGone(e);
            }

            return Request.CreateResponse(HttpStatusCode.OK);
        }

        // DELETE api/register/5
        public async Task<HttpResponseMessage> Delete(string id)
        {
            await hub.DeleteRegistrationAsync(id);
            return Request.CreateResponse(HttpStatusCode.OK);
        }

        private static void ReturnGoneIfHubResponseIsGone(MessagingException e)
        {
            var webex = e.InnerException as WebException;
            if (webex.Status == WebExceptionStatus.ProtocolError)
            {
                var response = (HttpWebResponse)webex.Response;
                if (response.StatusCode == HttpStatusCode.Gone)
                    throw new HttpRequestException(HttpStatusCode.Gone.ToString());
            }
        }

10. Guarde los cambios.

## Envío de notificaciones desde el back-end de WebAPI

En esta sección agregará un nuevo controlador que expone una forma de que los dispositivos cliente envíen una notificación basándose en la etiqueta de nombre de usuario; para ello, se usará la biblioteca de administración de servicios de Centros de notificaciones de Azure en el back-end WebAPI de ASP.NET.


1. Cree otro controlador nuevo llamado **NotificationsController**. Créelo del mismo modo que creó el **RegisterController** en la sección anterior.

2. En NotificationsController.cs, agregue las siguientes instrucciones `using`:

        using AppBackend.Models;
        using System.Threading.Tasks;
        using System.Web;

3. Agregue el método siguiente a la clase **NotificationsController**.

	Este código envía un tipo de notificación basado en el parámetro `pns` del Servicio de notificación de plataforma (PNS). El valor de `to_tag` se usa para definir la etiqueta *username* en el mensaje. Esta etiqueta debe coincidir con una etiqueta de nombre de usuario de un registro de un centro de notificaciones activo. El mensaje de notificación se extrae del cuerpo de la solicitud POST y se formatea para el PNS de destino.

	Dependiendo del Servicio de notificación de plataforma (PNS) que usan los dispositivos compatibles para recibir notificaciones, se admiten notificaciones diferentes con distintos formatos. Por ejemplo, en dispositivos Windows, puede usar una [notificación del sistema con WNS](https://msdn.microsoft.com/library/windows/apps/br230849.aspx) que no sea compatible directamente con otro PNS. Por lo tanto, el back-end deberá dar formato a la notificación en una notificación compatible para el PNS de dispositivos que tiene previsto admitir. Posteriormente, use la API de envío adecuada en la [clase NotificationHubClient](https://msdn.microsoft.com/library/azure/microsoft.azure.notificationhubs.notificationhubclient_methods.aspx)

        public async Task<HttpResponseMessage> Post(string pns, [FromBody]string message, string to_tag)
        {
            var user = HttpContext.Current.User.Identity.Name;
            string[] userTag = new string[2];
            userTag[0] = "username:" + to_tag;
            userTag[1] = "from:" + user;

            Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;
            HttpStatusCode ret = HttpStatusCode.InternalServerError;

            switch (pns.ToLower())
            {
                case "wns":
                    // Windows 8.1 / Windows Phone 8.1
                    var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" + 
                                "From " + user + ": " + message + "</text></binding></visual></toast>";
                    outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);
                    break;
                case "apns":
                    // iOS
                    var alert = "{"aps":{"alert":"" + "From " + user + ": " + message + ""}}";
                    outcome = await Notifications.Instance.Hub.SendAppleNativeNotificationAsync(alert, userTag);
                    break;
                case "gcm":
                    // Android
                    var notif = "{ "data" : {"message":"" + "From " + user + ": " + message + ""}}";
                    outcome = await Notifications.Instance.Hub.SendGcmNativeNotificationAsync(notif, userTag);
                    break;
            }

            if (outcome != null)
            {
                if (!((outcome.State == Microsoft.Azure.NotificationHubs.NotificationOutcomeState.Abandoned) ||
                    (outcome.State == Microsoft.Azure.NotificationHubs.NotificationOutcomeState.Unknown)))
                {
                    ret = HttpStatusCode.OK;
                }
            }

            return Request.CreateResponse(ret);
        }


4. Presione **F5** para ejecutar la aplicación y comprobar que hasta ahora todo es correcto. La aplicación debería iniciar un explorador web y mostrar la página principal de ASP.NET.

##Publicación del nuevo back-end de WebAPI

1. Ahora implementaremos esta aplicación en un sitio web de Azure a fin de que sea accesible para todos los dispositivos. Haga clic con el botón derecho en el proyecto **AppBackend** y, a continuación, seleccione **Publicar**.

2. Seleccione **Aplicaciones web de Microsoft Azure** como el destino de publicación.

    ![][B15]

3. Inicie sesión con su cuenta de Azure y seleccione una aplicación web nueva o existente.

    ![][B16]

4. Tome nota de la propiedad **Dirección URL de destino** en la pestaña **Conexión**. Más tarde en este tutorial haremos referencia a esta dirección URL como *extremo de backend*. Haga clic en **Publicar**.

    ![][B18]


[B1]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push1.png
[B2]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push2.png
[B3]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push3.png
[B4]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push4.png
[B5]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push5.png
[B6]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push6.png
[B7]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push7.png
[B8]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push8.png
[B14]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push14.png
[B15]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users15.PNG
[B16]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users16.PNG
[B18]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users18.PNG

<!---HONumber=AcomDC_0128_2016-->