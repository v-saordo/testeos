<properties
	pageTitle="Uso de los Centros de notificaciones para enviar noticias de última hora (Windows Universal)"
	description="Use Centros de notificaciones de Azure con etiquetas en el registro para enviar noticias de última hora a una aplicación universal de Windows."
	services="notification-hubs"
	documentationCenter="windows"
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>


<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="12/14/2015"
	ms.author="wesmc"/>

# Uso de los Centros de notificaciones para enviar noticias de última hora


[AZURE.INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]


##Información general

Este tema muestra cómo puede usar los Centros de notificaciones de Azure para difundir notificaciones de noticias de última hora en una aplicación (no Silverlight) de la Tienda Windows o de Windows Phone 8.1. Si su objetivo es Silverlight para Windows Phone 8.1, consulte la versión de [Windows Phone](notification-hubs-windows-phone-send-breaking-news.md). Cuando lo complete, podrá registrar las categorías de noticias de última hora en las que esté interesado y recibir solo notificaciones de inserción para esas categorías. Este escenario es un patrón común para muchas aplicaciones en las que las notificaciones tienen que enviarse a grupos de usuarios que han mostrado previamente interés en ellas, por ejemplo, lectores RSS, aplicaciones para aficionados a la música y así sucesivamente.

Los escenarios de difusión se habilitan mediante la inclusión de una o más _etiquetas_ cuando se crea un registro en el Centro de notificaciones. Cuando las notificaciones se envían a una etiqueta, todos los dispositivos registrados para la etiqueta recibirán la notificación. Puesto que las etiquetas son cadenas simples, no tendrán que aprovisionarse antes. Para información sobre las etiquetas, consulte [Expresiones de etiqueta y enrutamiento de los Centros de notificaciones](notification-hubs-routing-tag-expressions.md).

##Requisitos previos

Este tema se basa en la aplicación que creó en [Introducción a los Centros de notificaciones][get-started]. Antes de comenzar este tutorial, debe haber completado la [Introducción a los Centros de notificaciones][get-started].

##Adición de una selección de categorías a la aplicación

El primer paso es agregar los elementos de la interfaz de usuario a la página principal existente que permiten al usuario seleccionar las categorías para registrar. Las categorías seleccionadas por un usuario se almacenan en el dispositivo. Cuando la aplicación se inicia, se crea un registro de dispositivos en el Centro de notificaciones con las categorías seleccionadas como etiquetas.

1. Abra el archivo de proyecto MainPage.xaml y, a continuación, copie el siguiente código en el elemento **Grid**:

        <Grid>
            <Grid.RowDefinitions>
                <RowDefinition/>
                <RowDefinition/>
                <RowDefinition/>
                <RowDefinition/>
                <RowDefinition/>
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition/>
                <ColumnDefinition/>
            </Grid.ColumnDefinitions>
            <TextBlock Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"  TextWrapping="Wrap" Text="Breaking News" FontSize="42" VerticalAlignment="Top" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="World" Name="WorldToggle" Grid.Row="1" Grid.Column="0" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Politics" Name="PoliticsToggle" Grid.Row="2" Grid.Column="0" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Business" Name="BusinessToggle" Grid.Row="3" Grid.Column="0" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Technology" Name="TechnologyToggle" Grid.Row="1" Grid.Column="1" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Science" Name="ScienceToggle" Grid.Row="2" Grid.Column="1" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Sports" Name="SportsToggle" Grid.Row="3" Grid.Column="1" HorizontalAlignment="Center"/>
            <Button Name="SubscribeButton" Content="Subscribe" HorizontalAlignment="Center" Grid.Row="4" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click"/>
        </Grid>


2. Haga clic con el botón derecho en el proyecto **compartido** y agregue una nueva clase llamada **Notifications**, agregue el modificador **public** a la definición de clase y, a continuación, agregue las siguientes instrucciones **using** al nuevo archivo de código:

		using Windows.Networking.PushNotifications;
		using Microsoft.WindowsAzure.Messaging;
		using Windows.Storage;
		using System.Threading.Tasks;

3. Agregue el siguiente código a la nueva clase **Notifications**:

		private NotificationHub hub;

        public Notifications(string hubName, string listenConnectionString)
        {
            hub = new NotificationHub(hubName, listenConnectionString);
        }

        public async Task<Registration> StoreCategoriesAndSubscribe(IEnumerable<string> categories)
        {
            ApplicationData.Current.LocalSettings.Values["categories"] = string.Join(",", categories);
            return await SubscribeToCategories(categories);
        }

		public IEnumerable<string> RetrieveCategories()
        {
            var categories = (string) ApplicationData.Current.LocalSettings.Values["categories"];
            return categories != null ? categories.Split(','): new string[0];
        }

        public async Task<Registration> SubscribeToCategories(IEnumerable<string> categories = null)
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

            if (categories == null)
            {
                categories = RetrieveCategories();
            }

            // Using a template registration to support notifications across platforms.
			// Any template notifications that contain messageParam and a corresponding tag expression
			// will be delivered for this registration.

            const string templateBodyWNS = "<toast><visual><binding template="ToastText01"><text id="1">$(messageParam)</text></binding></visual></toast>";

            return await hub.RegisterTemplateAsync(channel.Uri, templateBodyWNS, "simpleWNSTemplateExample",
					categories);
        }

    Esta clase usa el almacenamiento local para almacenar las categorías de noticias que este dispositivo ha de recibir. Tenga en cuenta que en lugar de llamar al método *RegisterNativeAsync*, llamamos al método *RegisterTemplateAsync* para registrar las categorías mediante el registro de plantilla.
	
	Asimismo, proporcionamos un nombre para la plantilla ("simpleWNSTemplateExample"), ya que es posible que queramos registrar más de una (por ejemplo, una para las notificaciones del sistema y otra de iconos) y es necesario asignarles un nombre para poder actualizarlas o eliminarlas.

	Tenga en cuenta que, si un dispositivo registra varias plantillas con la misma etiqueta, el envío de un mensaje destinado a dicha etiqueta dará como resultado la entrega de varias notificaciones al dispositivo (una para cada plantilla). Este comportamiento resulta útil cuando un mismo mensaje lógico debe dar como resultado varias notificaciones visuales que muestren, por ejemplo, tanto un distintivo como una notificación del sistema en una aplicación de la Tienda Windows.

	Para más información sobre el uso de plantillas, vea [Plantillas](notification-hubs-templates.md).




4. En el archivo de proyecto App.xaml.cs, agregue la siguiente propiedad a la clase **App**:

		public Notifications notifications = new Notifications("<hub name>", "<connection string with listen access>");

	Esta propiedad se usa para crear y obtener acceso a una instancia de **Notifications**.

	En el código anterior, reemplace los marcadores de posición `<hub name>` y `<connection string with listen access>` por el nombre del centro de notificaciones y la cadena de conexión de *DefaultListenSharedAccessSignature* que obtuvo anteriormente.

	> [AZURE.NOTE]Puesto que las credenciales que se distribuyen con una aplicación de cliente no son normalmente seguras, solo debe distribuir la clave para el acceso de escucha con la aplicación cliente. El acceso de escucha permite a la aplicación el registro de notificaciones, pero los registros existentes no pueden modificarse y las notificaciones no se pueden enviar. La clave de acceso completo se usa en un servicio back-end protegido para el envío de notificaciones y el cambio de registros existentes.

5. En MainPage.xaml.cs, agregue la siguiente línea:

		using Windows.UI.Popups;

6. En el archivo de proyecto MainPage.xaml.cs, agregue el siguiente método:

		private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
        {
            var categories = new HashSet<string>();
            if (WorldToggle.IsOn) categories.Add("World");
            if (PoliticsToggle.IsOn) categories.Add("Politics");
            if (BusinessToggle.IsOn) categories.Add("Business");
            if (TechnologyToggle.IsOn) categories.Add("Technology");
            if (ScienceToggle.IsOn) categories.Add("Science");
            if (SportsToggle.IsOn) categories.Add("Sports");

            var result = await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(categories);

            var dialog = new MessageDialog("Subscribed to: " + string.Join(",", categories) + " on registration Id: " + result.RegistrationId);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }

	Este método crea una lista de categorías y usa la clase **Notifications** para almacenar la lista en el almacenamiento local y registrar las etiquetas correspondientes en el centro de notificaciones. Cuando se modifican las categorías, el registro vuelve a crearse con las nuevas categorías.

La aplicación ahora puede almacenar un conjunto de categorías en el almacenamiento local en el dispositivo y registrarse en el Centro de notificaciones siempre que el usuario cambie la selección de categorías.

##Registro de notificaciones

Estos pasos permiten registrar el Centro de notificaciones en el inicio mediante las categorías que se han almacenado en el almacén local.

> [AZURE.NOTE]Puesto que el URI de canal asignado por el servicio de notificaciones de inserción de Windows (WNS) puede cambiar en cualquier momento, debe registrar las notificaciones con frecuencia para evitar errores de notificación. En este ejemplo se registra la notificación cada vez que se inicia la aplicación. En las aplicaciones que se ejecutan con frecuencia, más de una vez al día, es posible que pueda omitir el registro para conservar el ancho de banda si pasa menos de un día del registro previo.

1. Abra el archivo App.xaml.cs y actualice el método **InitNotificationsAsync** para usar la clase `notifications` para suscribirse en función de las categorías.

		// *** Remove or comment out these lines *** 
	    //var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
	    //var hub = new NotificationHub("your hub name", "your listen connection string");
	    //var result = await hub.RegisterNativeAsync(channel.Uri);
	
	    var result = await notifications.SubscribeToCategories();

	De esta forma, se garantiza que cada vez que la aplicación se inicia, se recuperan las categorías del almacenamiento local y se solicita un registro de estas categorías. El método **InitNotificationsAsync** se creó como parte del tutorial [Introducción a los Centros de notificaciones][get-started].

3. En el archivo de proyecto MainPage.xaml.cs, agregue el siguiente código en el método *OnNavigatedTo*:

        protected override void OnNavigatedTo(NavigationEventArgs e)
        {
            var categories = ((App)Application.Current).notifications.RetrieveCategories();

            if (categories.Contains("World")) WorldToggle.IsOn = true;
            if (categories.Contains("Politics")) PoliticsToggle.IsOn = true;
            if (categories.Contains("Business")) BusinessToggle.IsOn = true;
            if (categories.Contains("Technology")) TechnologyToggle.IsOn = true;
            if (categories.Contains("Science")) ScienceToggle.IsOn = true;
            if (categories.Contains("Sports")) SportsToggle.IsOn = true;
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

	![][19]

4. Envíe una nueva notificación desde el back-end de alguna de las siguientes formas:

	+ **Aplicación de consola**: inicie la aplicación de consola.

	+ **Java/PHP:** ejecute su aplicación/script.

	Las notificaciones para las categorías seleccionadas aparecen como notificaciones del sistema.

	![][14]

##Pasos siguientes

En este tutorial hemos aprendido cómo difundir noticias de última hora por categoría. Considere la posibilidad de llevar a cabo uno de los siguientes tutoriales que destacan otros escenarios de Centros de notificaciones avanzados:

+ [Uso de los Centros de notificaciones para difundir noticias de última hora localizadas]

	Conozca cómo expandir la aplicación de noticias de última hora para habilitar el envío de notificaciones localizadas.



<!-- Anchors. -->
[Add category selection to the app]: #adding-categories
[Register for notifications]: #register
[Send notifications from your back-end]: #send
[Run the app and generate notifications]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[1]: ./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-breakingnews-win1.png

[14]: ./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-toast-2.png


[19]: ./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-reg-2.png

<!-- URLs.-->
[get-started]: /manage/services/notification-hubs/getting-started-windows-dotnet/
[Uso de los Centros de notificaciones para difundir noticias de última hora localizadas]: /manage/services/notification-hubs/breaking-news-localized-dotnet/
[Notify users with Notification Hubs]: /manage/services/notification-hubs/notify-users
[Mobile Service]: /develop/mobile/tutorials/get-started/
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Store]: http://msdn.microsoft.com/library/jj927172.aspx
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591

<!---HONumber=AcomDC_1217_2015-->