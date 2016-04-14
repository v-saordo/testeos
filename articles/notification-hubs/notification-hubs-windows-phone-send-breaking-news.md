<properties
	pageTitle="Uso de los Centros de notificaciones para enviar noticias de última hora (Windows Phone)"
	description="Use Centros de notificaciones de Azure para usar la etiqueta en registros para enviar noticias de última hora a una aplicación de Windows Phone."
	services="notification-hubs"
	documentationCenter="windows"
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows-phone"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="12/15/2015" 
	ms.author="wesmc"/>

# Uso de los Centros de notificaciones para enviar noticias de última hora

[AZURE.INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]


##Información general

Este tema muestra cómo puede usar los Centros de notificaciones de Azure para difundir notificaciones de noticias de última hora en una aplicación Silverlight de Windows Phone 8.0/8.1. Si su objetivo es una aplicación de la Tienda Windows o de Windows Phone 8.1, consulte la versión [Windows Universal](notification-hubs-windows-store-dotnet-send-breaking-news.md). Cuando lo complete, podrá registrar las categorías de noticias de última hora en las que esté interesado y recibir solo notificaciones de inserción para esas categorías. Este escenario es un patrón común para muchas aplicaciones en las que las notificaciones tienen que enviarse a grupos de usuarios que han mostrado previamente interés en ellas, por ejemplo, lectores RSS, aplicaciones para aficionados a la música, etc.

Los escenarios de difusión se habilitan mediante la inclusión de una o más _etiquetas_ cuando se crea un registro en el Centro de notificaciones. Cuando las notificaciones se envían a una etiqueta, todos los dispositivos registrados para la etiqueta recibirán la notificación. Puesto que las etiquetas son cadenas simples, no tendrán que aprovisionarse antes. Para información sobre las etiquetas, consulte [Expresiones de etiqueta y enrutamiento de los Centros de notificaciones](notification-hubs-routing-tag-expressions.md).

##Requisitos previos

Este tema se basa en la aplicación que creó en [Introducción a los Centros de notificaciones]. Antes de comenzar este tutorial, debe haber completado la [Introducción a los Centros de notificaciones].

##Adición de una selección de categorías a la aplicación

El primer paso es agregar los elementos de la interfaz de usuario a la página principal existente que permiten al usuario seleccionar las categorías para registrar. Las categorías seleccionadas por un usuario se almacenan en el dispositivo. Cuando la aplicación se inicia, se crea un registro de dispositivos en el Centro de notificaciones con las categorías seleccionadas como etiquetas.

1. Abra el archivo de proyecto MainPage.xaml y, a continuación, reemplace los elementos **Grid** denominados `TitlePanel` y `ContentPanel` por el código siguiente:

        <StackPanel x:Name="TitlePanel" Grid.Row="0" Margin="12,17,0,28">
            <TextBlock Text="Breaking News" Style="{StaticResource PhoneTextNormalStyle}" Margin="12,0"/>
            <TextBlock Text="Categories" Margin="9,-7,0,0" Style="{StaticResource PhoneTextTitle1Style}"/>
        </StackPanel>

        <Grid Name="ContentPanel" Grid.Row="1" Margin="12,0,12,0">
            <Grid.RowDefinitions>
                <RowDefinition Height="auto"/>
                <RowDefinition Height="auto" />
                <RowDefinition Height="auto" />
                <RowDefinition Height="auto" />
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition />
                <ColumnDefinition />
            </Grid.ColumnDefinitions>
            <CheckBox Name="WorldCheckBox" Grid.Row="0" Grid.Column="0">World</CheckBox>
            <CheckBox Name="PoliticsCheckBox" Grid.Row="1" Grid.Column="0">Politics</CheckBox>
            <CheckBox Name="BusinessCheckBox" Grid.Row="2" Grid.Column="0">Business</CheckBox>
            <CheckBox Name="TechnologyCheckBox" Grid.Row="0" Grid.Column="1">Technology</CheckBox>
            <CheckBox Name="ScienceCheckBox" Grid.Row="1" Grid.Column="1">Science</CheckBox>
            <CheckBox Name="SportsCheckBox" Grid.Row="2" Grid.Column="1">Sports</CheckBox>
            <Button Name="SubscribeButton" Content="Subscribe" HorizontalAlignment="Center" Grid.Row="3" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click" />
        </Grid>

2. En el proyecto, cree una nueva clase llamada **Notifications**, agregue el modificador **public** en la definición de clase y, a continuación, agregue las siguientes instrucciones **using** al nuevo archivo de código:

		using Microsoft.Phone.Notification;
		using Microsoft.WindowsAzure.Messaging;
		using System.IO.IsolatedStorage;
		using System.Windows;

3. Agregue el siguiente código a la nueva clase **Notifications**:

        private NotificationHub hub;

        // Registration task to complete registration in the ChannelUriUpdated event handler
        private TaskCompletionSource<Registration> registrationTask;

        public Notifications(string hubName, string listenConnectionString)
        {
            hub = new NotificationHub(hubName, listenConnectionString);
        }

        public IEnumerable<string> RetrieveCategories()
        {
            var categories = (string)IsolatedStorageSettings.ApplicationSettings["categories"];
            return categories != null ? categories.Split(',') : new string[0];
        }

        public async Task<Registration> StoreCategoriesAndSubscribe(IEnumerable<string> categories)
        {
            var categoriesAsString = string.Join(",", categories);
            var settings = IsolatedStorageSettings.ApplicationSettings;
            if (!settings.Contains("categories"))
            {
                settings.Add("categories", categoriesAsString);
            }
            else
            {
                settings["categories"] = categoriesAsString;
            }
            settings.Save();

            return await SubscribeToCategories();
        }

        public async Task<Registration> SubscribeToCategories()
        {
            registrationTask = new TaskCompletionSource<Registration>();

            var channel = HttpNotificationChannel.Find("MyPushChannel");

            if (channel == null)
            {
                channel = new HttpNotificationChannel("MyPushChannel");
                channel.Open();
                channel.BindToShellToast();
                channel.ChannelUriUpdated += channel_ChannelUriUpdated;

				// This is optional, used to receive notifications while the app is running.
                channel.ShellToastNotificationReceived += channel_ShellToastNotificationReceived;
            }

            // If channel.ChannelUri is not null, we will complete the registrationTask here.  
			// If it is null, the registrationTask will be completed in the ChannelUriUpdated event handler.
            if (channel.ChannelUri != null)
            {
                await RegisterTemplate(channel.ChannelUri);
            }
            
            return await registrationTask.Task;
        }

        async void channel_ChannelUriUpdated(object sender, NotificationChannelUriEventArgs e)
        {
            await RegisterTemplate(e.ChannelUri);
        }

        async Task<Registration> RegisterTemplate(Uri channelUri)
        {
            // Using a template registration to support notifications across platforms.
            // Any template notifications that contain messageParam and a corresponding tag expression
            // will be delivered for this registration.

            const string templateBodyMPNS = "<wp:Notification xmlns:wp="WPNotification">" +
                                                "<wp:Toast>" +
                                                    "<wp:Text1>$(messageParam)</wp:Text1>" +
                                                "</wp:Toast>" +
                                            "</wp:Notification>";

			// The stored categories tags are passed with the template registration.

            registrationTask.SetResult(await hub.RegisterTemplateAsync(channelUri.ToString(), 
				templateBodyMPNS, "simpleMPNSTemplateExample", this.RetrieveCategories()));

            return await registrationTask.Task;
        }

		// This is optional. It is used to receive notifications while the app is running.
        void channel_ShellToastNotificationReceived(object sender, NotificationEventArgs e)
        {
            StringBuilder message = new StringBuilder();
            string relativeUri = string.Empty;

            message.AppendFormat("Received Toast {0}:\n", DateTime.Now.ToShortTimeString());

            // Parse out the information that was part of the message.
            foreach (string key in e.Collection.Keys)
            {
                message.AppendFormat("{0}: {1}\n", key, e.Collection[key]);

                if (string.Compare(
                    key,
                    "wp:Param",
                    System.Globalization.CultureInfo.InvariantCulture,
                    System.Globalization.CompareOptions.IgnoreCase) == 0)
                {
                    relativeUri = e.Collection[key];
                }
            }

            // Display a dialog of all the fields in the toast.
            System.Windows.Deployment.Current.Dispatcher.BeginInvoke(() => 
            { 
                MessageBox.Show(message.ToString()); 
            });
        }


    Esta clase usa el almacenamiento aislado para almacenar las categorías de noticias que este dispositivo va a recibir. También contiene métodos para registrar estas categorías mediante un registro de notificaciones de [plantilla](notification-hubs-templates.md).


4. En el archivo de proyecto App.xaml.cs, agregue la siguiente propiedad a la clase **App**. Reemplace los marcadores de posición `<hub name>` y `<connection string with listen access>` por el nombre del centro de notificaciones y la cadena de conexión de *DefaultListenSharedAccessSignature* que obtuvo anteriormente.

		public Notifications notifications = new Notifications("<hub name>", "<connection string with listen access>");

	> [AZURE.NOTE]Puesto que las credenciales que se distribuyen con una aplicación de cliente no son normalmente seguras, solo debe distribuir la clave para el acceso de escucha con la aplicación cliente. El acceso de escucha permite a la aplicación el registro de notificaciones, pero los registros existentes no pueden modificarse y las notificaciones no se pueden enviar. La clave de acceso completo se usa en un servicio back-end protegido para el envío de notificaciones y el cambio de registros existentes.

5. En MainPage.xaml.cs, agregue la siguiente línea:

		using Windows.UI.Popups;

6. En el archivo de proyecto MainPage.xaml.cs, agregue el siguiente método:

		private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
		{
		  var categories = new HashSet<string>();
		  if (WorldCheckBox.IsChecked == true) categories.Add("World");
		  if (PoliticsCheckBox.IsChecked == true) categories.Add("Politics");
		  if (BusinessCheckBox.IsChecked == true) categories.Add("Business");
		  if (TechnologyCheckBox.IsChecked == true) categories.Add("Technology");
		  if (ScienceCheckBox.IsChecked == true) categories.Add("Science");
		  if (SportsCheckBox.IsChecked == true) categories.Add("Sports");
	
		  var result = await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(categories);
	
		  MessageBox.Show("Subscribed to: " + string.Join(",", categories) + " on registration id : " +
			 result.RegistrationId);
		}

	Este método crea una lista de categorías y usa la clase **Notifications** para almacenar la lista en el almacenamiento local y registrar las etiquetas correspondientes en el centro de notificaciones. Cuando se modifican las categorías, el registro vuelve a crearse con las nuevas categorías.

La aplicación ahora puede almacenar un conjunto de categorías en el almacenamiento local en el dispositivo y registrarse en el Centro de notificaciones siempre que el usuario cambie la selección de categorías.

##Registro de notificaciones

Estos pasos permiten registrar el Centro de notificaciones en el inicio mediante las categorías que se han almacenado en el almacén local.

> [AZURE.NOTE]Puesto que el URI de canal asignado por el Servicio de notificaciones push de Microsoft (MPNS) puede cambiar en cualquier momento, debe registrar las notificaciones con frecuencia para evitar errores de notificación. En este ejemplo se registra la notificación cada vez que se inicia la aplicación. En las aplicaciones que se ejecutan con frecuencia, más de una vez al día, es posible que pueda omitir el registro para conservar el ancho de banda si pasa menos de un día del registro previo.


1. Abra el archivo App.xaml.cs, agregue el modificador **async** al método **Application\_Launching** y reemplace el código de registro de los Centros de notificaciones que agregó en [Introducción a los Centros de notificaciones] con el siguiente código:

        private async void Application_Launching(object sender, LaunchingEventArgs e)
        {
            var result = await notifications.SubscribeToCategories();

            if (result != null)
                System.Windows.Deployment.Current.Dispatcher.BeginInvoke(() =>
                {
                    MessageBox.Show("Registration Id :" + result.RegistrationId, "Registered", MessageBoxButton.OK);
                });
        }

	De esta forma, se garantiza que cada vez que la aplicación se inicia, se recuperan las categorías del almacenamiento local y se solicita un registro de estas categorías.

2. En el archivo de proyecto MainPage.xaml.cs, agregue el siguiente código que implementa el método **OnNavigatedTo**:

		protected override void OnNavigatedTo(NavigationEventArgs e)
		{
		    var categories = ((App)Application.Current).notifications.RetrieveCategories();

		    if (categories.Contains("World")) WorldCheckBox.IsChecked = true;
		    if (categories.Contains("Politics")) PoliticsCheckBox.IsChecked = true;
		    if (categories.Contains("Business")) BusinessCheckBox.IsChecked = true;
		    if (categories.Contains("Technology")) TechnologyCheckBox.IsChecked = true;
		    if (categories.Contains("Science")) ScienceCheckBox.IsChecked = true;
		    if (categories.Contains("Sports")) SportsCheckBox.IsChecked = true;
		}

	De esta forma, se actualiza la página principal según el estado de las categorías guardadas anteriormente.

La aplicación está ahora completa y puede almacenar un conjunto de categorías en el almacenamiento local del dispositivo usado para registrarse en el Centro de notificaciones cuando el usuario cambie la selección de categorías. A continuación, definiremos un back-end que pueda enviar notificaciones de categorías a esta aplicación.

##Envío de notificaciones con etiquetas

[AZURE.INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

##Ejecución de la aplicación y generación de notificaciones

1. En Visual Studio, presione F5 para compilar e iniciar la aplicación.

	![][1]

	Tenga en cuenta que la interfaz de usuario de la aplicación ofrece un conjunto de elementos de alternancia que le permite seleccionar las categorías a las que suscribirse.

2. Habilite uno o más elementos de alternancia de las categorías y, a continuación, haga clic en **Suscribirse**.

	La aplicación convierte las categorías seleccionadas en etiquetas y solicita un nuevo registro de dispositivo para las etiquetas seleccionadas al Centro de notificaciones. Las categorías registradas se devuelven y se muestran en un cuadro de diálogo.

	![][2]

3. Después de recibir una confirmación de que se han completado sus categorías, ejecute la aplicación de consola para enviar notificaciones de cada categoría. Compruebe que solo recibe una notificación para las categorías a las que se ha suscrito.

	![][3]

Ha completado este tema.

<!--##Next steps

In this tutorial we learned how to broadcast breaking news by category. Consider completing one of the following tutorials that highlight other advanced Notification Hubs scenarios:

+ [Use Notification Hubs to broadcast localized breaking news]

	Learn how to expand the breaking news app to enable sending localized notifications.

+ [Notify users with Notification Hubs]

	Learn how to push notifications to specific authenticated users. This is a good solution for sending notifications only to specific users.
-->

<!-- Anchors. -->
[Add category selection to the app]: #adding-categories
[Register for notifications]: #register
[Send notifications from your back-end]: #send
[Run the app and generate notifications]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[1]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-breakingnews.png
[2]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-registration.png
[3]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-toast.png



<!-- URLs.-->
[Introducción a los Centros de notificaciones]: /manage/services/notification-hubs/get-started-notification-hubs-wp8/
[Use Notification Hubs to broadcast localized breaking news]: ../breakingnews-localized-wp8.md
[Notify users with Notification Hubs]: /manage/services/notification-hubs/notify-users/
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Phone]: ??

<!---HONumber=AcomDC_1217_2015-->