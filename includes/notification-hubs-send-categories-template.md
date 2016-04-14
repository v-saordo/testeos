
En esta sección se muestra cómo enviar noticias de última hora como notificaciones de plantillas con etiquetas desde una aplicación de consola .NET.

Si usa Servicios móviles, consulte los tutoriales [Introducción a las notificaciones de inserción](mobile-services-dotnet-backend-windows-store-dotnet-get-started-push.md).

Si desea utilizar Java o PHP, consulte [Uso de Centro de notificaciones desde Java/PHP](../articles/notification-hubs/notification-hubs-java-backend-how-to.md). Puede enviar notificaciones desde cualquier back-end mediante la [Interfaz de REST de Centros de notificaciones](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx).

Omita los pasos 1-3 si creó la aplicación de consola para enviar notificaciones cuando completó [Introducción a los Centros de notificaciones][get-started].

1. En Visual Studio, cree una aplicación de consola en Visual C#: 

   	![][13]

2. En el menú principal de Visual Studio, haga clic sucesivamente en **Herramientas**, **Administrador de paquetes de la biblioteca** y **Consola del administrador de paquetes**, y luego, en la ventana de la consola, escriba lo siguiente y presione **Entrar**:

        Install-Package Microsoft.Azure.NotificationHubs
 	
	Así se agrega una referencia al SDK de Centros de notificaciones de Azure mediante el <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">paquete NuGet Microsoft.Azure.Notification Hubs</a>.

3. Abra el archivo Program.cs y agregue la siguiente instrucción `using`:

        using Microsoft.Azure.NotificationHubs;

4. En la clase `Program`, agregue el siguiente método o reemplácelo si ya existe:

        private static async void SendTemplateNotificationAsync()
        {
			// Define the notification hub.
		    NotificationHubClient hub = 
				NotificationHubClient.CreateClientFromConnectionString(
					"<connection string with full access>", "<hub name>");

            // Create an array of breaking news categories.
            var categories = new string[] { "World", "Politics", "Business", 
											"Technology", "Science", "Sports"};

            // Sending the notification as a template notification. All template registrations that contain 
			// "messageParam" and the proper tags will receive the notifications. 
			// This includes APNS, GCM, WNS, and MPNS template registrations.

            Dictionary<string, string> templateParams = new Dictionary<string, string>();

            foreach (var category in categories)
            {
                templateParams["messageParam"] = "Breaking " + category + " News!";            
                await hub.SendTemplateNotificationAsync(templateParams, category);
            }
		 }

	Este código envía una notificación de plantilla para cada una de las seis etiquetas en la matriz de cadenas. El uso de etiquetas ofrece la seguridad de que los dispositivos reciben notificaciones solo de las categorías registradas.

6. En el código anterior, reemplace los marcadores de posición `<hub name>` y `<connection string with full access>` por su nombre del centro de notificaciones y la cadena de conexión para *DefaultFullSharedAccessSignature* del panel de su centro de notificaciones.

7. Agregue las siguientes líneas al método **Main**:

         SendTemplateNotificationAsync();
		 Console.ReadLine();

8. Compile la aplicación de consola.

<!-- Anchors -->
[From a console app]: #console
[From Mobile Services]: #mobile-services
[Run the app and generate notifications]: #test-app

<!-- Images. -->
[13]: ./media/notification-hubs-back-end/notification-hub-create-console-app.png

[15]: ./media/notification-hubs-back-end/notification-hub-scheduler1.png
[16]: ./media/notification-hubs-back-end/notification-hub-scheduler2.png

<!-- URLs. -->
[get-started]: ../articles/notification-hubs/notification-hubs-windows-store-dotnet-get-started.md
[Use Notification Hubs to send notifications to users]: ../articles/tutorial-notify-users-mobileservices.md
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Store]: http://msdn.microsoft.com/library/jj927172.aspx
[Notification Hubs REST interface]: http://msdn.microsoft.com/library/windowsazure/dn223264.aspx

<!---HONumber=AcomDC_1217_2015-->