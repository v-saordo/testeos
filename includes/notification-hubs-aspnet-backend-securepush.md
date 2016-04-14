## Proyecto WebAPI

1. En Visual Studio, abra el proyecto **AppBackend** que creó en el tutorial **Notificación a usuarios**.
2. En Notifications.cs, reemplace toda la clase **Notifications** por el código siguiente. Asegúrese de sustituir los marcadores de posición por su cadena de conexión (con acceso total) para el Centro de notificaciones y el nombre del centro. Puede obtener estos valores en el [Portal de Azure clásico](http://manage.windowsazure.com). Este módulo representa ahora las diferentes notificaciones seguras que se enviarán. En una implementación completa, las notificaciones se almacenarán en una base de datos; en este caso, vamos a almacenarlas en la memoria para simplificar el proceso.

		public class Notification
	    {
	        public int Id { get; set; }
	        public string Payload { get; set; }
	        public bool Read { get; set; }
	    }
    
    
	    public class Notifications
	    {
	        public static Notifications Instance = new Notifications();
	        
	        private List<Notification> notifications = new List<Notification>();
	
	        public NotificationHubClient Hub { get; set; }
	
	        private Notifications() {
	            Hub = NotificationHubClient.CreateClientFromConnectionString("{conn string with full access}", 	"{hub name}");
	        }

	        public Notification CreateNotification(string payload)
	        {
	            var notification = new Notification() {
                Id = notifications.Count,
                Payload = payload,
                Read = false
            	};

            	notifications.Add(notification);

            	return notification;
	        }

	        public Notification ReadNotification(int id)
	        {
	            return notifications.ElementAt(id);
	        }
	    }

20. En NotificationsController.cs, reemplace el código dentro de la definición de clase **NotificationsController** por el código siguiente. Este componente dota al dispositivo de una ruta para recuperar la notificación de forma segura, y además ofrece una manera (para los fines de este tutorial) de desencadenar una inserción segura en sus dispositivos. Tenga en cuenta que al enviar la notificación al Centro de notificaciones, enviamos una notificación sin procesar solo con el identificador de la notificación (no el mensaje real):

		public NotificationsController()
        {
            Notifications.Instance.CreateNotification("This is a secure notification!");
        }

        // GET api/notifications/id
        public Notification Get(int id)
        {
            return Notifications.Instance.ReadNotification(id);
        }

        public async Task<HttpResponseMessage> Post()
        {
            var secureNotificationInTheBackend = Notifications.Instance.CreateNotification("Secure confirmation.");
            var usernameTag = "username:" + HttpContext.Current.User.Identity.Name;

            // windows
            var rawNotificationToBeSent = new Microsoft.Azure.NotificationHubs.WindowsNotification(secureNotificationInTheBackend.Id.ToString(),
                            new Dictionary<string, string> {
                                {"X-WNS-Type", "wns/raw"}
                            });
            await Notifications.Instance.Hub.SendNotificationAsync(rawNotificationToBeSent, usernameTag);

            // apns
            await Notifications.Instance.Hub.SendAppleNativeNotificationAsync("{"aps": {"content-available": 1}, "secureId": "" + secureNotificationInTheBackend.Id.ToString() + ""}", usernameTag);

            // gcm
            await Notifications.Instance.Hub.SendGcmNativeNotificationAsync("{"data": {"secureId": "" + secureNotificationInTheBackend.Id.ToString() + ""}}", usernameTag);


            return Request.CreateResponse(HttpStatusCode.OK);
        }


Tenga en cuenta que el método `Post` ahora no envía una notificación del sistema. Envía una notificación sin procesar que contiene solo el identificador de la notificación sin ningún tipo de contenido delicado. Además, asegúrese de comentar la operación de envío de las plataformas para las que no tiene credenciales configuradas en su Centro de notificaciones, ya que provocarán un error.

21. Ahora implementaremos de nuevo esta aplicación en un sitio web de Azure a fin de que sea accesible para todos los dispositivos. Haga clic con el botón derecho en el proyecto **AppBackend** y, a continuación, seleccione **Publicar**.

24. Seleccione Sitio web Azure como destino de publicación. Inicie sesión con su cuenta de Azure, seleccione un sitio web nuevo o existente y anote la propiedad **dirección URL de destino** en la pestaña **Conexión**. Más tarde en este tutorial haremos referencia a esta dirección URL como *extremo de backend*. Haga clic en **Publicar**.

<!---HONumber=AcomDC_1210_2015-->