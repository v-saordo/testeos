<properties
	pageTitle="Incorporación de notificaciones push a la aplicación universal Windows en tiempo de ejecución 8.1 | Aplicaciones móviles de Azure"
	description="Obtenga información acerca de cómo usar Aplicaciones móviles del Servicio de aplicaciones de Azure y Centros de notificaciones de Azure para enviar notificaciones push a la aplicación Windows."
	services="app-service\mobile,notification-hubs"
	documentationCenter="windows"
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="03/02/2016"
	ms.author="glenga"/>

# Incorporación de notificaciones push a la aplicación universal Windows en tiempo de ejecución 8.1

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

##Información general

En este tema se muestra cómo enviar notificaciones push a una aplicación universal Windows en tiempo de ejecución 8.1 con Aplicaciones móviles de Azure y Centros de notificaciones de Azure. En este escenario, cuando se agrega un nuevo elemento, el back-end de la aplicación móvil envía una notificación push a todas las aplicaciones Windows registradas con el Servicio de notificación de Windows (WNS).

Este tutorial se basa en el inicio rápido de aplicaciones móviles del Servicio de aplicaciones. Antes de empezar este tutorial, debe completar primero el tutorial de inicio rápido [Creación de una aplicación Windows](app-service-mobile-windows-store-dotnet-get-started.md). Si no usa el proyecto de servidor de inicio rápido descargado, debe agregar el paquete de extensión de notificaciones push al proyecto. Para obtener más información acerca de los paquetes de extensión de servidor, consulte [Trabajar con el SDK del servidor back-end de .NET para Aplicaciones móviles de Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

##Requisitos previos

Para completar este tutorial, necesitará lo siguiente:

* Una [cuenta Microsoft Store](http://go.microsoft.com/fwlink/p/?LinkId=280045) activa.
* [Visual Studio Community 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934)
* Complete el [tutorial de inicio rápido](app-service-mobile-windows-store-dotnet-get-started.md).


##<a name="create-hub"></a>Creación de un centro de notificaciones

[AZURE.INCLUDE [app-service-mobile-create-notification-hub](../../includes/app-service-mobile-create-notification-hub.md)]

##Registro de la aplicación para notificaciones push

Para poder enviar notificaciones push a las aplicaciones Windows desde Azure, debe enviar la aplicación a la Tienda Windows. Después, podrá configurar el proyecto de servidor para integrarlo con WNS.

[AZURE.INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]


##Configuración del back-end para enviar notificaciones push

[AZURE.INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]


##<a id="update-service"></a>Actualización del servidor para enviar notificaciones de inserción

Tras habilitar las notificaciones push en la aplicación, debe actualizar el back-end de la aplicación para enviarlas. Use el procedimiento a continuación que corresponda al tipo de proyecto de back-end: [back-end de .NET](#dotnet) o [back-end de Node.js](#nodejs).

### <a name="dotnet"></a>Proyecto de back-end de .NET

1. En Visual Studio, haga clic con el botón derecho en el proyecto de servidor, haga clic en **Administrar paquetes de NuGet**, busque Microsoft.Azure.NotificationHubs y después haga clic en **Instalar**. Esto instala la biblioteca de cliente de Centros de notificaciones.

2. Expanda **Controladores**, abra TodoItemController.cs y agregue las siguientes instrucciones using:

		using System.Collections.Generic;
		using Microsoft.Azure.NotificationHubs;
		using Microsoft.Azure.Mobile.Server.Config;

3. En el método **PostTodoItem**, agregue el código siguiente después de la llamada a **InsertAsync**:

	    // Get the settings for the server project.
	    HttpConfiguration config = this.Configuration;
	    MobileAppSettingsDictionary settings =
	        this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();

	    // Get the Notification Hubs credentials for the Mobile App.
	    string notificationHubName = settings.NotificationHubName;
	    string notificationHubConnection = settings
	        .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

	    // Create the notification hub client.
	    NotificationHubClient hub = NotificationHubClient
	        .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

	    // Define a WNS payload
	    var windowsToastPayload = @"<toast><visual><binding template=""ToastText01""><text id=""1"">"
	                            + item.Text + @"</text></binding></visual></toast>";
	    try
	    {
	        // Send the push notification.
	        var result = await hub.SendWindowsNativeNotificationAsync(windowsToastPayload);

	        // Write the success result to the logs.
	        config.Services.GetTraceWriter().Info(result.State.ToString());
	    }
	    catch (System.Exception ex)
	    {
	        // Write the failure result to the logs.
	        config.Services.GetTraceWriter()
	            .Error(ex.Message, null, "Push.SendAsync Error");
	    }

	Este código indica al centro de notificaciones que envíe una notificación push después de la inserción de un elemento nuevo.

4. Vuelva a publicar el proyecto de servidor.

### <a name="nodejs"></a>Proyecto de back-end de Node.js

1. Si no lo ha hecho todavía, [descargue el proyecto de inicio rápido](app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart) o utilice el [editor en línea del Portal de Azure](app-service-mobile-node-backend-how-to-use-server-sdk.md#online-editor).

2. Reemplace el código existente en el archivo todoitem.js por lo siguiente:

		var azureMobileApps = require('azure-mobile-apps'),
	    promises = require('azure-mobile-apps/src/utilities/promises'),
	    logger = require('azure-mobile-apps/src/logger');

		var table = azureMobileApps.table();

		table.insert(function (context) {
	    // For more information about the Notification Hubs JavaScript SDK,
	    // see http://aka.ms/nodejshubs
	    logger.info('Running TodoItem.insert');

	    // Define the WNS payload that contains the new item Text.
	    var payload = "<toast><visual><binding template=\ToastText01><text id="1">"
		                            + context.item.text + "</text></binding></visual></toast>";

	    // Execute the insert.  The insert returns the results as a Promise,
	    // Do the push as a post-execute action within the promise flow.
	    return context.execute()
	        .then(function (results) {
	            // Only do the push if configured
	            if (context.push) {
					// Send a WNS native toast notification.
	                context.push.wns.sendToast(null, payload, function (error) {
	                    if (error) {
	                        logger.error('Error while sending push notification: ', error);
	                    } else {
	                        logger.info('Push notification sent successfully!');
	                    }
	                });
	            }
	            // Don't forget to return the results from the context.execute()
	            return results;
	        })
	        .catch(function (error) {
	            logger.error('Error while running context.execute: ', error);
	        });
		});

		module.exports = table;

	Esta acción envía una notificación del sistema de WNS que contiene el item.text cuando se inserta un nuevo elemento todo.

2. Cuando edite el archivo en el equipo local, vuelva a publicar el proyecto de servidor.

##<a id="update-app"></a>Incorporación de notificaciones de inserción a la aplicación

1. Abra el archivo de proyecto **App.xaml.cs** compartido y agregue las siguientes instrucciones `using`:

		using System.Threading.Tasks;
        using Windows.Networking.PushNotifications;

2. En el mismo archivo, agregue la siguiente definición de método **InitNotificationsAsync** para la clase **aplicación**:

        private async Task InitNotificationsAsync()
        {
            // Get a channel URI from WNS.
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            // Register the channel URI with Notification Hubs.
            await App.MobileService.GetPush().RegisterAsync(channel.Uri);
        }

    Este código recupera el valor de ChannelURI de la aplicación desde WNS y, a continuación, lo registra con sus Aplicaciones móviles del Servicio de aplicaciones.

3. En la parte superior del controlador de eventos **OnLaunched**, en **App.xaml.cs**, agregue el modificador **async** a la definición del método y agregue la siguiente llamada al nuevo método **InitNotificationsAsync**, como en el siguiente ejemplo:

        protected async override void OnLaunched(LaunchActivatedEventArgs e)
        {
            await InitNotificationsAsync();

			// ...
		}

    Esto garantiza que el valor de ChannelURI de corta duración se registra cada vez que se inicia la aplicación.

    >[AZURE.NOTE] Si también tiene habilitada la autenticación, asegúrese de que el usuario inicia sesión antes de intentar registrarse para recibir notificaciones push. Para obtener más información, consulte [Autenticarse primero](https://github.com/Azure-Samples/app-service-mobile-windows-quickstart/blob/master/README.md#authenticate-first) en el ejemplo completo del inicio rápido.

4. En el Explorador de soluciones, haga doble clic en **Package.appxmanifest** de la aplicación Tienda Windows y, en **Notificaciones**, establezca **Capacidad de aviso** en **Sí**.

    En el menú **Archivo**, haga clic en **Guardar todo**.

5. Repita el paso anterior para el proyecto de la aplicación de la Tienda de Windows Phone.

La carpeta ahora ya está lista para recibir notificaciones.

##<a id="test"></a>Prueba de las notificaciones push en su aplicación

[AZURE.INCLUDE [app-service-mobile-windows-universal-test-push](../../includes/app-service-mobile-windows-universal-test-push.md)]


##<a id="more"></a>Más

* Las plantillas proporcionan flexibilidad para enviar inserciones multiplataforma e inserciones localizadas. El artículo [Uso del cliente administrado para Aplicaciones móviles de Azure](app-service-mobile-dotnet-how-to-use-client-library.md#how-to-register-push-templates-to-send-cross-platform-notifications) muestra cómo registrar plantillas.
* Las etiquetas permiten dirigirse a clientes segmentados con inserciones. [Trabajar con el SDK del servidor back-end de .NET para Aplicaciones móviles de Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#how-to-add-tags-to-a-device-installation-to-enable-push-to-tags) muestra cómo agregar etiquetas a la instalación de un dispositivo.

<!-- Anchors. -->

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com/

<!-- Images. -->
))) )

<!---HONumber=AcomDC_0302_2016-->