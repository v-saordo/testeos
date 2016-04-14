<properties
	pageTitle="Tutorial de últimas noticias localizadas sobre los Centros de notificaciones"
	description="Obtenga información sobre cómo usar Centros de notificaciones de Azure para enviar notificaciones de noticias de última hora localizadas."
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
	ms.date="12/15/2015" 
	ms.author="wesmc"/>

# Uso de los Centros de notificaciones para enviar noticias de última hora localizadas

> [AZURE.SELECTOR]
- [Windows Store C#](notification-hubs-windows-store-dotnet-send-localized-breaking-news.md)
- [iOS](notification-hubs-ios-send-localized-breaking-news.md)

##Información general

Este tema muestra cómo usar la característica de **plantilla** de los Centros de notificaciones de Azure para difundir notificaciones de noticias de última hora localizadas por lenguaje y dispositivo. En este tutorial comenzará con la aplicación de la Tienda Windows que se creó en el tutorial [Uso de los Centros de notificaciones para enviar noticias de última hora]. Una vez que lo haya completado, podrá registrarse en las categorías que le interesan, especificar un idioma para recibir las notificaciones y recibir solo notificaciones de inserción para las categorías seleccionadas en dicho idioma.


Este escenario tiene dos partes:

- La aplicación de la Tienda Windows permite que los dispositivos cliente especifiquen un idioma y se suscriban a distintas categorías de noticias de última hora.

- el back-end difunde las notificaciones, mediante las características de **etiqueta** y **plantilla** de los Centros de notificaciones de Azure.



##Requisitos previos

Debe haber completado el tutorial [Uso de Centros de notificaciones para enviar noticias de última hora] y debe tener disponible el código, porque este tutorial se basa directamente en ese código.

También necesita Visual Studio 2012 o posterior.


##Conceptos de plantilla

En el tutorial [Uso de Centros de notificaciones para enviar noticias de última hora] creó una aplicación que utilizó **etiquetas** para suscribirse a notificaciones para distintas categorías de noticias. Sin embargo, muchas aplicaciones están dirigidas a varios mercados y requieren localización. Esto significa que el contenido de las notificaciones mismas se debe localizar y entregar al conjunto de dispositivos correcto. En este tema podremos mostrar cómo usar la característica de **plantilla** de los Centros de notificaciones para entregar fácilmente notificaciones de noticias de última hora localizadas.

Nota: una forma de enviar notificaciones localizadas es crear varias versiones de cada etiqueta. Por ejemplo, para admitir inglés, francés y chino mandarín, necesitaríamos tres etiquetas distintas para noticias mundiales: "world\_en", "world\_fr" y "world\_ch". Luego tendríamos que enviar una versión localizada de las noticias mundiales a cada una de estas etiquetas. En este tema usamos plantillas para evitar la proliferación de etiquetas y el requisito de enviar varios mensajes.

A un alto nivel, las plantillas son una forma de especificar la manera en que un dispositivo específico debe recibir una notificación. La plantilla especifica el formato de carga exacto haciendo referencia a las propiedades que forman parte del mensaje enviado por el back-end de la aplicación. En nuestro caso, enviaremos un mensaje independiente de la configuración regional que contengan todos los idiomas compatibles:

	{
		"News_English": "...",
		"News_French": "...",
		"News_Mandarin": "..."
	}

Esto garantizará que los dispositivos se registren con una plantilla que hace referencia a la propiedad correcta. Por ejemplo, una aplicación de la Tienda Windows que quiere recibir un simple mensaje del sistema se registrará en la plantilla siguiente con las etiquetas correspondientes:

	<toast>
	  <visual>
	    <binding template="ToastText01">
	      <text id="1">$(News_English)</text>
	    </binding>
	  </visual>
	</toast>



Las plantillas son una característica muy eficaz de la que puede obtener más información en nuestro artículo [Plantillas](notification-hubs-templates.md).


##Interfaz de usuario de la aplicación

Ahora modificaremos la aplicación de noticias de última hora que creó en el tema [Uso de los Centros de notificaciones para enviar noticias de última hora] para enviar noticias de última hora localizadas con plantillas.

En la aplicación de la Tienda Windows:

Modifique el archivo MainPage.xaml para que incluya un cuadro combinado de configuración regional:

	<Grid Margin="120, 58, 120, 80"  
			Background="{StaticResource ApplicationPageBackgroundThemeBrush}">
        <Grid.RowDefinitions>
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition />
            <ColumnDefinition />
        </Grid.ColumnDefinitions>
        <TextBlock Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"  TextWrapping="Wrap" Text="Breaking News" FontSize="42" VerticalAlignment="Top"/>
        <ComboBox Name="Locale" HorizontalAlignment="Left" VerticalAlignment="Center" Width="200" Grid.Row="1" Grid.Column="0">
            <x:String>English</x:String>
            <x:String>French</x:String>
            <x:String>Mandarin</x:String>
        </ComboBox>
        <ToggleSwitch Header="World" Name="WorldToggle" Grid.Row="2" Grid.Column="0"/>
        <ToggleSwitch Header="Politics" Name="PoliticsToggle" Grid.Row="3" Grid.Column="0"/>
        <ToggleSwitch Header="Business" Name="BusinessToggle" Grid.Row="4" Grid.Column="0"/>
        <ToggleSwitch Header="Technology" Name="TechnologyToggle" Grid.Row="2" Grid.Column="1"/>
        <ToggleSwitch Header="Science" Name="ScienceToggle" Grid.Row="3" Grid.Column="1"/>
        <ToggleSwitch Header="Sports" Name="SportsToggle" Grid.Row="4" Grid.Column="1"/>
        <Button Content="Subscribe" HorizontalAlignment="Center" Grid.Row="5" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click" />
    </Grid>

##Compilación de la aplicación cliente de la Tienda Windows

1. En la clase Notifications, agregue un parámetro de configuración regional a los métodos *StoreCategoriesAndSubscribe* y *SubscribeToCategories*.

        public async Task<Registration> StoreCategoriesAndSubscribe(string locale, IEnumerable<string> categories)
        {
            ApplicationData.Current.LocalSettings.Values["categories"] = string.Join(",", categories);
            ApplicationData.Current.LocalSettings.Values["locale"] = locale;
            return await SubscribeToCategories(categories);
        }

        public async Task<Registration> SubscribeToCategories(string locale, IEnumerable<string> categories = null)
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

            if (categories == null)
            {
                categories = RetrieveCategories();
            }

            // Using a template registration. This makes supporting notifications across other platforms much easier.
            // Using the localized tags based on locale selected.
            string templateBodyWNS = String.Format("<toast><visual><binding template="ToastText01"><text id="1">$(News_{0})</text></binding></visual></toast>", locale);

            return await hub.RegisterTemplateAsync(channel.Uri, templateBodyWNS, "localizedWNSTemplateExample", categories);
        }

	Tenga en cuenta que, en lugar de llamar al método *RegisterNativeAsync*, llamaremos a *RegisterTemplateAsync*: estamos registrando un formato de notificación específico en el que la plantilla depende de la configuración regional. Asimismo, proporcionamos un nombre para la plantilla ("localizedWNSTemplateExample"), ya que es posible que queramos registrar más de una (por ejemplo, una para las notificaciones del sistema y otra de iconos) y es necesario asignarles un nombre para poder actualizarlas o eliminarlas.

	Tenga en cuenta que, si un dispositivo registra varias plantillas con la misma etiqueta, el envío de un mensaje destinado a dicha etiqueta dará como resultado la entrega de varias notificaciones al dispositivo (una para cada plantilla). Este comportamiento resulta útil cuando un mismo mensaje lógico debe dar como resultado varias notificaciones visuales que muestren, por ejemplo, tanto un distintivo como una notificación del sistema en una aplicación de la Tienda Windows.

2. Agregue el método siguiente para recuperar la configuración regional almacenada:

		public string RetrieveLocale()
        {
            var locale = (string) ApplicationData.Current.LocalSettings.Values["locale"];
            return locale != null ? locale : "English";
        }

3. En el archivo MainPage.xaml.cs, actualice el controlador de clics de botón; para ello, recupere el valor actual del cuadro combinado de configuración regional y ofrézcalo a la llamada a la clase Notifications, tal y como se muestra a continuación:

        private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
        {
            var locale = (string)Locale.SelectedItem;

            var categories = new HashSet<string>();
            if (WorldToggle.IsOn) categories.Add("World");
            if (PoliticsToggle.IsOn) categories.Add("Politics");
            if (BusinessToggle.IsOn) categories.Add("Business");
            if (TechnologyToggle.IsOn) categories.Add("Technology");
            if (ScienceToggle.IsOn) categories.Add("Science");
            if (SportsToggle.IsOn) categories.Add("Sports");

            var result = await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(locale,
				 categories);

            var dialog = new MessageDialog("Locale: " + locale + " Subscribed to: " + 
				string.Join(",", categories) + " on registration Id: " + result.RegistrationId);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }


4. Por último, en su archivo App.xaml.cs, asegúrese de actualizar su método `InitNotificationsAsync` para recuperar la configuración regional y usarla al suscribirse:

        private async void InitNotificationsAsync()
        {
            var result = await notifications.SubscribeToCategories(notifications.RetrieveLocale());

            // Displays the registration ID so you know it was successful
            if (result.RegistrationId != null)
            {
                var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }


##Envío de notificaciones localizadas desde el back-end

[AZURE.INCLUDE [notification-hubs-localized-back-end](../../includes/notification-hubs-localized-back-end.md)]






<!-- Anchors. -->
[Template concepts]: #concepts
[The app user interface]: #ui
[Building the Windows Store client app]: #building-client
[Send notifications from your back-end]: #send
[Next Steps]: #next-steps

<!-- Images. -->

<!-- URLs. -->
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs: ASP.NET]: /manage/services/notification-hubs/notify-users-aspnet
[Notify users with Notification Hubs: Mobile Services]: /manage/services/notification-hubs/notify-users
[Uso de Centros de notificaciones para enviar noticias de última hora]: /manage/services/notification-hubs/breaking-news-dotnet
[Uso de los Centros de notificaciones para enviar noticias de última hora]: /manage/services/notification-hubs/breaking-news-dotnet

[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-dotnet
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-dotnet
[Push notifications to app users]: /develop/mobile/tutorials/push-notifications-to-app-users-dotnet
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-dotnet
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js

[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for iOS]: http://msdn.microsoft.com/library/jj927168.aspx
[Notification Hubs How-To for Windows Store]: http://msdn.microsoft.com/library/jj927172.aspx

<!---HONumber=AcomDC_1217_2015-->