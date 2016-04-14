<properties
	pageTitle="Los Centros de notificaciones de Azure notifican a los usuarios con back-end de .NET"
	description="Obtenga información acerca de cómo enviar notificaciones de inserción seguras en Azure. Ejemplos de código escritos en C# con la API de .NET."
	documentationCenter="windows"
	authors="wesmc7777"
	manager="dwrede"
	services="notification-hubs"
	editor=""/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="02/29/2016"
	ms.author="wesmc"/>

#Los Centros de notificaciones de Azure notifican a los usuarios con back-end de .NET

[AZURE.INCLUDE [notification-hubs-selector-aspnet-backend-notify-users](../../includes/notification-hubs-selector-aspnet-backend-notify-users.md)]


##Información general

La compatibilidad con las notificaciones de inserción en Azure le permite tener acceso a una infraestructura multiplataforma y de escalamiento horizontal fácil de usar, que simplifica considerablemente la implementación de notificaciones de inserción tanto en aplicaciones de consumidor, como en aplicaciones empresariales para plataformas móviles. Este tutorial muestra cómo puede utilizar los Centros de notificaciones de Azure para enviar notificaciones de inserción a un usuario de aplicaciones determinado en un dispositivo concreto. Se usa un back-end de WebAPI de ASP.NET para autenticar a los clientes. Al usar el usuario del cliente autenticado, el back-end agregará automáticamente la etiqueta para el registro de notificaciones. Esta etiqueta se usará para que la envíe el back-end para generar notificaciones para un usuario específico. Para obtener más información sobre cómo registrarse para notificaciones con un back-end de aplicación, consulte el tema de orientación [Registro desde el back-end de la aplicación](http://msdn.microsoft.com/library/dn743807.aspx). Este tutorial se basa en el Centro de notificaciones y el proyecto que creó en el tutorial [Introducción a los Centros de notificaciones].

Este tutorial también es el requisito previo para el tutorial [Inserción segura]. Después de haber completado los pasos de este tutorial, puede continuar con el tutorial [Inserción segura], que muestra cómo modificar el código de este tutorial para enviar una notificación push de forma segura.





## Antes de empezar

Nos tomamos muy en serio sus comentarios. Si tiene dificultades para completar este tema o tiene recomendaciones para mejorar este contenido, agradecemos que escriba sus comentarios en la parte inferior de la página.

El código completo de este tutorial se puede encontrar en GitHub [aquí](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/NotifyUsers).



##Requisitos previos

Antes de comenzar este tutorial, debe haber realizado los siguientes tutoriales de Servicios móviles:

+ [Introducción a los Centros de notificaciones]<br/>Cree el Centro de notificaciones, reserve el nombre de la aplicación y regístrese para recibir notificaciones en este tutorial. En este tutorial se asume que ya ha completado estos pasos. En caso contrario, siga los pasos de [Introducción a los Centros de notificaciones](notification-hubs-windows-store-dotnet-get-started.md) (Tienda Windows), especialmente las secciones [Registro de la aplicación para la Tienda Windows](notification-hubs-windows-store-dotnet-get-started.md#register-your-app-for-the-windows-store) y [Configuración del Centro de notificaciones](notification-hubs-windows-store-dotnet-get-started.md#configure-your-notification-hub). En concreto, asegúrese de que ha especificado los valores de **SID del paquete** y **Secreto del cliente** en el portal, en la pestaña **Configurar** correspondiente a su Centro de notificaciones. Este procedimiento de configuración se describe en la sección [Configuración de su Centro de notificaciones](notification-hubs-windows-store-dotnet-get-started.md#configure-your-notification-hub). Este es un paso importante: si las credenciales del portal no coinciden con las especificadas para el nombre de la aplicación que elija, la notificación de inserción no tendrá lugar.




> [AZURE.NOTE] Si usa Servicios móviles como su servicio back-end, consulte la [versión de Servicios móviles](../mobile-services/mobile-services-dotnet-backend-windows-universal-dotnet-get-started-push.md) de este tutorial.




[AZURE.INCLUDE [notification-hubs-aspnet-backend-notifyusers](../../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## Actualización del código para el proyecto de cliente

En esta sección, se actualiza el código del proyecto que se ha completado para el tutorial [Introducción a los Centros de notificaciones]. Debe estar asociado con la tienda y configurada para el Centro de notificaciones. En esta sección, agregará código para llamar el nuevo back-end de WebAPI y lo usará para registrar y enviar notificaciones.

1. En Visual Studio, abra el la solución creada para el tutorial [Introducción a los Centros de notificaciones].

2. En el Explorador de soluciones, haga clic con el botón derecho en el proyecto **(Windows 8.1)** y, a continuación, haga clic en **Administrar paquetes de NuGet**.

3. A la izquierda, haga clic en **En línea**.

4. En el cuadro **Buscar**, escriba **Cliente http**.

5. En la lista de resultados, haga clic en **Bibliotecas de cliente HTTP de Microsoft** y, a continuación, haga clic en **Instalar**. Complete la instalación.

6. Nuevamente en el cuadro **Buscar** de NuGet, escriba **Json.net**. Instale el paquete **Json.NET** y, a continuación, cierre la ventana Administrador de paquetes NuGet.

7. Repita los pasos anteriores para el proyecto **(Windows Phone 8.1)** para instalar el paquete de NuGet **JSON.NET** para el proyecto de Windows Phone.


8. En el Explorador de soluciones, en el proyecto **(Windows Phone 8.1)**, haga doble clic en **MainPage.xaml** para abrirlo en el editor de Visual Studio.

9. En el código XML **MainPage.xaml**, sustituya la sección `<Grid>` por el siguiente código: Este código agrega un cuadro de texto de nombre de usuario y contraseña con el que el usuario se autenticará. También agrega cuadros de texto al mensaje de notificación y la etiqueta de nombre de usuario que debería recibir la notificación:

        <Grid>
            <Grid.RowDefinitions>
                <RowDefinition Height="Auto"/>
                <RowDefinition Height="*"/>
            </Grid.RowDefinitions>

            <TextBlock Grid.Row="0" Text="Notify Users" HorizontalAlignment="Center" FontSize="48"/>

            <StackPanel Grid.Row="1" VerticalAlignment="Center">
                <Grid>
                    <Grid.RowDefinitions>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                    </Grid.RowDefinitions>
                    <Grid.ColumnDefinitions>
                        <ColumnDefinition></ColumnDefinition>
                        <ColumnDefinition></ColumnDefinition>
                        <ColumnDefinition></ColumnDefinition>
                    </Grid.ColumnDefinitions>
                    <TextBlock Grid.Row="0" Grid.ColumnSpan="3" Text="Username" FontSize="24" Margin="20,0,20,0"/>
                    <TextBox Name="UsernameTextBox" Grid.Row="1" Grid.ColumnSpan="3" Margin="20,0,20,0"/>
                    <TextBlock Grid.Row="2" Grid.ColumnSpan="3" Text="Password" FontSize="24" Margin="20,0,20,0" />
                    <PasswordBox Name="PasswordTextBox" Grid.Row="3" Grid.ColumnSpan="3" Margin="20,0,20,0"/>

                    <Button Grid.Row="4" Grid.ColumnSpan="3" HorizontalAlignment="Center" VerticalAlignment="Center"
                                Content="1. Login and register" Click="LoginAndRegisterClick" Margin="0,0,0,20"/>

                    <ToggleButton Name="toggleWNS" Grid.Row="5" Grid.Column="0" HorizontalAlignment="Right" Content="WNS" IsChecked="True" />
                    <ToggleButton Name="toggleGCM" Grid.Row="5" Grid.Column="1" HorizontalAlignment="Center" Content="GCM" />
                    <ToggleButton Name="toggleAPNS" Grid.Row="5" Grid.Column="2" HorizontalAlignment="Left" Content="APNS" />

                    <TextBlock Grid.Row="6" Grid.ColumnSpan="3" Text="Username Tag To Send To" FontSize="24" Margin="20,0,20,0"/>
                    <TextBox Name="ToUserTagTextBox" Grid.Row="7" Grid.ColumnSpan="3" Margin="20,0,20,0" TextWrapping="Wrap" />
                    <TextBlock Grid.Row="8" Grid.ColumnSpan="3" Text="Enter Notification Message" FontSize="24" Margin="20,0,20,0"/>
                    <TextBox Name="NotificationMessageTextBox" Grid.Row="9" Grid.ColumnSpan="3" Margin="20,0,20,0" TextWrapping="Wrap" />
                    <Button Grid.Row="10" Grid.ColumnSpan="3" HorizontalAlignment="Center" Content="2. Send push" Click="PushClick" Name="SendPushButton" />
                </Grid>
            </StackPanel>
        </Grid>


10. En el Explorador de soluciones, en el proyecto **(Windows Phone 8.1)**, abra **MainPage.xaml** y reemplace la sección Windows Phone 8.1 `<Grid>` con el mismo código anterior. La interfaz tendrá un aspecto similar a la que se muestra a continuación.

	![][13]

11. En el Explorador de soluciones, abra el archivo **MainPage.xaml.cs** para los proyectos **(Windows 8.1)** y **(Windows Phone 8.1)**. Agregue las siguientes instrucciones `using` en la parte superior de ambos archivos:

		using System.Net.Http;
		using Windows.Storage;
		using System.Net.Http.Headers;
		using Windows.Networking.PushNotifications;
		using Windows.UI.Popups;
		using System.Threading.Tasks;

12. En **MainPage.xaml.cs** para los proyectos **(Windows 8.1)** y **(Windows Phone 8.1)**, agregue el miembro siguiente a la clase `MainPage`. Asegúrese de reemplazar `<Enter Your Backend Endpoint>` por el extremo de back-end obtenido anteriormente. Por ejemplo: `http://mybackend.azurewebsites.net`.

        private static string BACKEND_ENDPOINT = "<Enter Your Backend Endpoint>";



13. Agregue el código siguiente a la clase MainPage en **MainPage.xaml.cs** para los proyectos **(Windows 8.1)** y **(Windows Phone 8.1)**.

	El método `PushClick` es el controlador de clics para el botón **Enviar inserción**. Llama al back-end para desencadenar una notificación a todos los dispositivos con una etiqueta de nombre de usuario que coincida con el parámetro `to_tag`. El mensaje de notificación se envía como contenido JSON en el cuerpo de la solicitud.

	El método `LoginAndRegisterClick` es el controlador de clics para el botón **Iniciar sesión y registrarse**. Almacena el token de autenticación básico localmente (tenga en cuenta que esto representa cualquier token que usa el esquema de autenticación) y después usa `RegisterClient` para registrar las notificaciones que usan el back-end.


        private async void PushClick(object sender, RoutedEventArgs e)
        {
            if (toggleWNS.IsChecked.Value)
            {
                await sendPush("wns", ToUserTagTextBox.Text, this.NotificationMessageTextBox.Text);
            }
            if (toggleGCM.IsChecked.Value)
            {
                await sendPush("gcm", ToUserTagTextBox.Text, this.NotificationMessageTextBox.Text);
            }
            if (toggleAPNS.IsChecked.Value)
            {
                await sendPush("apns", ToUserTagTextBox.Text, this.NotificationMessageTextBox.Text);

            }
        }

        private async Task sendPush(string pns, string userTag, string message)
        {
            var POST_URL = BACKEND_ENDPOINT + "/api/notifications?pns=" +
                pns + "&to_tag=" + userTag;

            using (var httpClient = new HttpClient())
            {
                var settings = ApplicationData.Current.LocalSettings.Values;
                httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string)settings["AuthenticationToken"]);

                try
                {
                    await httpClient.PostAsync(POST_URL, new StringContent(""" + message + """,
                        System.Text.Encoding.UTF8, "application/json"));
                }
                catch (Exception ex)
                {
                    MessageDialog alert = new MessageDialog(ex.Message, "Failed to send " + pns + " message");
                    alert.ShowAsync();
                }
            }
        }

        private async void LoginAndRegisterClick(object sender, RoutedEventArgs e)
        {
            SetAuthenticationTokenInLocalStorage();

            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

            // The "username:<user name>" tag gets automatically added by the message handler in the backend.
            // The tag passed here can be whatever other tags you may want to use.
            try
            {
				// The device handle used will be different depending on the device and PNS. 
				// Windows devices use the channel uri as the PNS handle.
                await new RegisterClient(BACKEND_ENDPOINT).RegisterAsync(channel.Uri, new string[] { "myTag" });

                var dialog = new MessageDialog("Registered as: " + UsernameTextBox.Text);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
                SendPushButton.IsEnabled = true;
            }
            catch (Exception ex)
            {
                MessageDialog alert = new MessageDialog(ex.Message, "Failed to register with RegisterClient");
                alert.ShowAsync();
            }
        }

        private void SetAuthenticationTokenInLocalStorage()
        {
            string username = UsernameTextBox.Text;
            string password = PasswordTextBox.Password;

            var token = Convert.ToBase64String(System.Text.Encoding.UTF8.GetBytes(username + ":" + password));
            ApplicationData.Current.LocalSettings.Values["AuthenticationToken"] = token;
        }



14. En el Explorador de soluciones, en el proyecto **compartido**, abra el archivo **MainPage.cs**. Encuentre la llamada a `InitNotificationsAsync()` en el controlador de eventos `OnLaunched()`. Marque como comentario o elimine la llamada a `InitNotificationsAsync()`. El controlador del botón agregado anteriormente inicializará los registros de notificación.


        protected override void OnLaunched(LaunchActivatedEventArgs e)
        {
            //InitNotificationsAsync();


15. En el Explorador de soluciones, haga clic con el botón derecho en el proyecto **compartido**, a continuación en **Agregar **y, por último, en **Clase**. Asigne un nombre a la clase **RegisterClient.cs** y después haga clic en **Aceptar** para generar la clase.

	Esta clase contendrá las llamadas REST requeridas para ponerse en contacto con el back-end de la aplicación con la finalidad de registrar notificaciones push. También almacena localmente los *registrationIds* creados por el Centro de notificaciones tal como se detalla en [Registro desde el back-end de la aplicación](http://msdn.microsoft.com/library/dn743807.aspx). Tenga en cuenta que usa un token de autorización almacenado localmente cuando hace clic en el botón **Iniciar sesión y registrarse**.


16. Agregue las siguientes instrucciones `using` en la parte superior del archivo RegisterClient.cs:

		using Windows.Storage;
		using System.Net;
		using System.Net.Http;
		using System.Net.Http.Headers;
		using Newtonsoft.Json;
		using System.Threading.Tasks;
		using System.Linq;

17. Agregue el siguiente código a la definición de clase `RegisterClient`.

		private string POST_URL;

        private class DeviceRegistration
        {
            public string Platform { get; set; }
            public string Handle { get; set; }
            public string[] Tags { get; set; }
        }

        public RegisterClient(string backendEndpoint)
        {
            POST_URL = backendEndpoint + "/api/register";
        }

        public async Task RegisterAsync(string handle, IEnumerable<string> tags)
        {
            var regId = await RetrieveRegistrationIdOrRequestNewOneAsync();

            var deviceRegistration = new DeviceRegistration
            {
                Platform = "wns",
                Handle = handle,
                Tags = tags.ToArray<string>()
            };

            var statusCode = await UpdateRegistrationAsync(regId, deviceRegistration);

            if (statusCode == HttpStatusCode.Gone)
            {
                // regId is expired, deleting from local storage & recreating
                var settings = ApplicationData.Current.LocalSettings.Values;
                settings.Remove("__NHRegistrationId");
                regId = await RetrieveRegistrationIdOrRequestNewOneAsync();
                statusCode = await UpdateRegistrationAsync(regId, deviceRegistration);
            }

            if (statusCode != HttpStatusCode.Accepted)
            {
                // log or throw
				throw new System.Net.WebException(statusCode.ToString());
            }
        }

        private async Task<HttpStatusCode> UpdateRegistrationAsync(string regId, DeviceRegistration deviceRegistration)
        {
            using (var httpClient = new HttpClient())
            {
                var settings = ApplicationData.Current.LocalSettings.Values;
                httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string) settings["AuthenticationToken"]);

                var putUri = POST_URL + "/" + regId;

                string json = JsonConvert.SerializeObject(deviceRegistration);
                                var response = await httpClient.PutAsync(putUri, new StringContent(json, Encoding.UTF8, "application/json"));
                return response.StatusCode;
            }
        }

        private async Task<string> RetrieveRegistrationIdOrRequestNewOneAsync()
        {
            var settings = ApplicationData.Current.LocalSettings.Values;
            if (!settings.ContainsKey("__NHRegistrationId"))
            {
                using (var httpClient = new HttpClient())
                {
                    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string)settings["AuthenticationToken"]);

                    var response = await httpClient.PostAsync(POST_URL, new StringContent(""));
                    if (response.IsSuccessStatusCode)
                    {
                        string regId = await response.Content.ReadAsStringAsync();
                        regId = regId.Substring(1, regId.Length - 2);
                        settings.Add("__NHRegistrationId", regId);
                    }
                    else
                    {
						throw new System.Net.WebException(response.StatusCode.ToString());
                    }
                }
            }
            return (string)settings["__NHRegistrationId"];

        }

18. Guarde todos los cambios.


## Prueba de la aplicación

1. Inicie la aplicación en Windows 8.1 y Windows Phone 8.1. Para Windows Phone 8.1 puede ejecutar la instancia en el emulador o en un dispositivo real.

2. En la instancia de Windows 8.1 de la aplicación, especifique un **nombre de usuario** y una **contraseña**, tal como se muestra en la pantalla siguiente. Debe diferir del nombre de usuario y contraseña que escriba en Windows Phone.


3. Haga clic en **Iniciar sesión y registrarse** y compruebe que el cuadro de diálogo se muestre que ha iniciado sesión. Esto también le permitirá habilitar el botón **Enviar inserción**.

    ![][14]

4. En la instancia de Windows Phone 8.1, escriba una cadena de nombre de usuario en los campos **Nombre de usuario** y **Contraseña** y después haga clic en **Iniciar sesión y registrarse**.
5. Después, en el campo **Etiqueta de nombre de usuario destinatario**, especifique el nombre registrado en Windows 8.1. Escriba un mensaje de notificación y haga clic en **Enviar inserción**.

    ![][16]

6. Solo los dispositivos que se han registrado con la etiqueta de nombre de usuario coincidente reciben el mensaje de notificación.

	![][15]

## Pasos siguientes

* Si desea segmentar los usuarios por grupos de interés, consulte [Uso de los Centros de notificaciones para enviar noticias de última hora].
* Para obtener más información sobre el uso de Centros de notificaciones, consulte [Información general acerca de los Centros de notificaciones].



[9]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push9.png
[10]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push10.png
[11]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push11.png
[12]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push12.png
[13]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-wp-ui.png
[14]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-windows-instance-username.png
[15]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-notification-received.png
[16]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-wp-send-message.png



<!-- URLs. -->
[Introducción a los Centros de notificaciones]: notification-hubs-windows-store-dotnet-get-started.md
[Inserción segura]: notification-hubs-aspnet-backend-windows-dotnet-secure-push.md
[Uso de los Centros de notificaciones para enviar noticias de última hora]: notification-hubs-windows-store-dotnet-send-breaking-news.md
[Información general acerca de los Centros de notificaciones]: http://msdn.microsoft.com/library/jj927170.aspx

<!---HONumber=AcomDC_0302_2016-->