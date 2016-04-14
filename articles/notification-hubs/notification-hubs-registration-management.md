<properties
	pageTitle="Administración de registros"
	description="En este tema se explica cómo registrar dispositivos en los centros de notificaciones para recibir notificaciones push."
	services="notification-hubs"
	documentationCenter=".net"
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="11/25/2015"
	ms.author="wesmc"/>

# Administración de registros

##Información general

En este tema se explica cómo registrar dispositivos en los centros de notificaciones para recibir notificaciones push. El tema describe los registros en un alto nivel y, luego, presenta los dos patrones principales para registrar dispositivos: el registro desde el dispositivo directamente al centro de notificaciones y el registro a través de un back-end de aplicación.


##Qué es el registro de dispositivos

El registro de dispositivos en un Centro de notificaciones se logra mediante un **Registro** o una **Instalación**.

#### Registros
Un registro es una subentidad de un centro de notificaciones y asocia el identificador del Servicio de notificaciones de plataforma (PNS) de un dispositivo con etiquetas y, posiblemente, una plantilla. El identificador del PNS puede ser un ChannelURI, un token de dispositivo o un identificador de registro de GCM. Las etiquetas se usan para enrutar las notificaciones al conjunto correcto de identificadores de dispositivos. Para obtener más información, consulte [Expresiones de etiqueta y enrutamiento](notification-hubs-routing-tag-expressions.md). Se utilizan plantillas para implementar la transformación por registro. Para obtener más información, consulte [Plantillas](notification-hubs-templates.md).

#### Instalaciones
Una Instalación es un registro mejorado que incluye un conjunto de propiedades relacionadas con la inserción. Sin embargo, es el mejor y más reciente enfoque al registro de los dispositivos.

Las siguientes son algunas ventajas clave de usar las instalaciones:

* Crear o actualizar una instalación es completamente idempotente. Por lo tanto, puede volver a intentarla sin preocuparse de obtener registros duplicados.
* El modelo de instalación facilita la realización de inserciones individuales, orientándose a un dispositivo específico. Una etiqueta de instalación **"$InstallationId:[IdentificadordeInstalación]"** se crea automáticamente con cada registro basado en instalación. Por lo tanto, puede llamar un envío a esta etiqueta para orientarse a un dispositivo específico sin tener que realizar ninguna codificación adicional.
* El uso de las instalaciones también le permite realizar actualizaciones parciales de registros. La actualización parcial de una instalación se solicita con un método PATCH a través del [estándar JSON-Patch](https://tools.ietf.org/html/rfc6902). Esto resulta especialmente útil cuando desea actualizar las etiquetas del registro. No es necesario desplegar todo el registro y reenviar todas las etiquetas anteriores.

Las instalaciones actualmente solo son compatibles con el [SDK del Centro de notificaciones para operaciones de back-end](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/). Consulte la [Clase de instalación](https://msdn.microsoft.com/library/azure/microsoft.azure.notificationhubs.installation.aspx) para obtener más información. Para registrarse desde un dispositivo cliente con un identificador de instalación sin back-end, deberá usar la [API de REST de los Centros de notificaciones](https://msdn.microsoft.com/library/mt621153.aspx) en este momento.

Una instalación puede contener las siguientes propiedades. Para obtener una lista completa de las propiedades de la instalación, consulte [Creación o sobrescritura de una instalación con REST](https://msdn.microsoft.com/library/azure/mt621153.aspx) o [Propiedades de la instalación](https://msdn.microsoft.com/library/azure/microsoft.azure.notificationhubs.installation_properties.aspx).

	// Example installation format to show some supported properties
	{
	    installationId: "",
	    expirationTime: "",
	    tags: [],
	    platform: "",
	    pushChannel: "",
	    ………
	    templates: {
	        "templateName1" : {
				body: "",
				tags: [] },
			"templateName2" : {
				body: "",
				// Headers are for Windows Store only
				headers: {
					"X-WNS-Type": "wns/tile" }
				tags: [] }
	    },
	    secondaryTiles: {
	        "tileId1": {
	            pushChannel: "",
	            tags: [],
	            templates: {
	                "otherTemplate": {
	                    bodyTemplate: "",
	                    headers: {
	                        ... }
	                    tags: [] }
	            }
	        }
	    }
	}

 

Es importante tener en cuenta que los registros y las instalaciones, junto con los identificadores de PNS que contienen, sí expiran. Puede establecer el período de vida en el Centro de notificaciones, hasta un máximo de 90 días. Este límite significa que se deben actualizar de forma periódica y, además, no deben ser el único lugar de almacenamiento para la información importante. Esta expiración automática también simplifica la limpieza cuando se desinstala la aplicación móvil.

Los registros y las instalaciones deben contener el identificador de PNS más reciente de cada dispositivo o canal. Debido a que los identificadores de PNS solo se pueden obtener en una aplicación cliente del dispositivo, un patrón es registrarse directamente en ese dispositivo con la aplicación cliente. Por otro lado, las consideraciones de seguridad y la lógica de negocios relacionadas con las etiquetas pueden requerir que administre el registro de dispositivos en el back-end de la aplicación.

#### Plantillas

Si desea usar [Plantillas](notification-hubs-templates.md), la instalación de dispositivo también contiene todas las plantillas asociadas con ese dispositivo en un formato JSON (consulte el ejemplo anterior). Los nombres de las plantillas ayudan a dirigirse a distintas plantillas para el mismo dispositivo.

Observe que cada nombre de plantilla está asignado al cuerpo de una plantilla y a un conjunto opcional de etiquetas. Además, cada plataforma puede tener propiedades de plantilla adicionales. En el caso de Tienda Windows (con WNS) y Windows Phone 8 (con MPNS), un conjunto de encabezados adicional puede formar parte de la plantilla. En el caso de APNs, puede establecer una propiedad de expiración en una constante o en una expresión de plantilla. Para obtener una lista completa de las propiedades de la instalación, consulte el tema [Creación o sobrescritura de una instalación con REST](https://msdn.microsoft.com/library/azure/mt621153.aspx).

#### Iconos secundarios de Aplicaciones de la Tienda Windows

En el caso de las aplicaciones cliente de la Tienda Windows, enviar notificaciones a los iconos secundarios es igual que enviarlos al icono principal. Esto también se admite en las instalaciones. Tenga en cuenta que los iconos secundarios tienen un ChannelUri distinto, que el SDK de la aplicación cliente administra sin problemas.

El diccionario SecondaryTiles utiliza el mismo TileId que se usa para crear el objeto SecondaryTiles en la aplicación de la Tienda Windows. Al igual que lo que ocurre con el ChannelUri principal, los ChannelUri de los iconos secundarios pueden cambiar en cualquier momento. Para mantener actualizadas las instalaciones en el centro de notificaciones, el dispositivo debe actualizarlas con los ChannelUri actuales de los iconos secundarios.


##Administración de registros desde el dispositivo

Cuando administra el registro del dispositivo desde las aplicaciones cliente, el back-end es el único responsable de enviar las notificaciones. Las aplicaciones cliente mantienen actualizados los identificadores de PNS y registran las etiquetas. La siguiente ilustración muestra este patrón.

![](./media/notification-hubs-registration-management/notification-hubs-registering-on-device.png)

El dispositivo primero recupera el identificador de PNS desde el PSN y, luego, se registra directamente con el centro de notificaciones. Una vez que el registro se realiza correctamente, el back-end de la aplicación puede enviar una notificación orientada a ese registro. Para obtener más información sobre cómo enviar notificaciones, consulte [Expresiones de etiqueta y enrutamiento](notification-hubs-routing-tag-expressions.md). Tenga en cuenta que, en este caso, solo usará derechos de escucha para tener acceso a los centros de notificaciones desde el dispositivo. Para obtener más información, consulte [Seguridad](notification-hubs-security.md).

El registro desde el dispositivo es el método más sencillo, pero presenta algunos inconvenientes. La primera desventaja es que una aplicación cliente solo puede actualizar sus etiquetas cuando la aplicación está activa. Por ejemplo, si un usuario tiene dos dispositivos que registran las etiquetas relacionadas con equipos deportivos, cuando el primer dispositivo registra una etiqueta adicional (por ejemplo, Seahawks), el segundo dispositivo no recibirá las notificaciones sobre los Seahawks hasta que la aplicación del segundo dispositivo se ejecute por segunda vez. De manera más general, cuando son varios los dispositivos que afectan las etiquetas, la administración de estas desde el back-end es una opción conveniente. La segunda desventaja de la administración de registros desde la aplicación cliente es que, debido a que es posible piratear las aplicaciones, proteger el registro de etiquetas específicas requiere un cuidado adicional, tal como se explica en la sección "Seguridad a nivel de etiqueta".



#### Ejemplo de código para el registro en un centro de notificaciones desde un dispositivo con una instalación 

En este momento, esta opción solo es compatible si se usa la [API de REST de Centros de notificaciones](https://msdn.microsoft.com/library/mt621153.aspx).

También puede utilizar el método PATCH con el [estándar JSON-Patch](https://tools.ietf.org/html/rfc6902) para actualizar la instalación.

	class DeviceInstallation
	{
	    public string installationId { get; set; }
	    public string platform { get; set; }
	    public string pushChannel { get; set; }
	    public string[] tags { get; set; }
	}

    private async Task<HttpStatusCode> CreateOrUpdateInstallationAsync(DeviceInstallation deviceInstallation,
		 string hubName, string listenConnectionString)
    {
        if (deviceInstallation.installationId == null)
            return HttpStatusCode.BadRequest;

        // Parse connection string (https://msdn.microsoft.com/library/azure/dn495627.aspx)
        ConnectionStringUtility connectionSaSUtil = new ConnectionStringUtility(listenConnectionString);
        string hubResource = "installations/" + deviceInstallation.installationId + "?";
        string apiVersion = "api-version=2015-04";

        // Determine the targetUri that we will sign
        string uri = connectionSaSUtil.Endpoint + hubName + "/" + hubResource + apiVersion;

        //=== Generate SaS Security Token for Authorization header ===
		// See, https://msdn.microsoft.com/library/azure/dn495627.aspx
        string SasToken = connectionSaSUtil.getSaSToken(uri, 60);

        using (var httpClient = new HttpClient())
        {
            string json = JsonConvert.SerializeObject(deviceInstallation);

            httpClient.DefaultRequestHeaders.Add("Authorization", SasToken);

            var response = await httpClient.PutAsync(uri, new StringContent(json, System.Text.Encoding.UTF8, "application/json"));
            return response.StatusCode;
        }
    }

    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    string installationId = null;
    var settings = ApplicationData.Current.LocalSettings.Values;

    // If we have not stored a installation id in application data, create and store as application data.
    if (!settings.ContainsKey("__NHInstallationId"))
    {
        installationId = Guid.NewGuid().ToString();
        settings.Add("__NHInstallationId", installationId);
    }

    installationId = (string)settings["__NHInstallationId"];

    var deviceInstallation = new DeviceInstallation
    {
        installationId = installationId,
        platform = "wns",
        pushChannel = channel.Uri,
        //tags = tags.ToArray<string>()
    };

    var statusCode = await CreateOrUpdateInstallationAsync(deviceInstallation, 
						"<HUBNAME>", "<SHARED LISTEN CONNECTION STRING>");

    if (statusCode != HttpStatusCode.Accepted)
    {
        var dialog = new MessageDialog(statusCode.ToString(), "Registration failed. Installation Id : " + installationId);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }
    else
    {
        var dialog = new MessageDialog("Registration successful using installation Id : " + installationId);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }

   

#### Ejemplo de código para el registro en un centro de notificaciones desde un dispositivo con un registro


Estos métodos crean o actualizan un registro para el dispositivo en el que se los llamaron. Esto significa que, para actualizar el identificador o las etiquetas, debe sobrescribir todo el registro. Recuerde que los registros son transitorios, por lo que siempre debe tener un almacenamiento confiable con las etiquetas actuales que necesita un dispositivo específico.


	// Initialize the Notification Hub
	NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(listenConnString, hubName);

	// The Device id from the PNS
    var pushChannel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    // If you are registering from the client itself, then store this registration id in device
	// storage. Then when the app starts, you can check if a registration id already exists or not before
	// creating.
	var settings = ApplicationData.Current.LocalSettings.Values;

	// If we have not stored a registration id in application data, store in application data.
	if (!settings.ContainsKey("__NHRegistrationId"))
	{
		// make sure there are no existing registrations for this push handle (used for iOS and Android)	
		string newRegistrationId = null;
		var registrations = await hub.GetRegistrationsByChannelAsync(pushChannel.Uri, 100);
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

		newRegistrationId = await hub.CreateRegistrationIdAsync();

        settings.Add("__NHRegistrationId", newRegistrationId);
	}
     
    string regId = (string)settings["__NHRegistrationId"];

    RegistrationDescription registration = new WindowsRegistrationDescription(pushChannel.Uri);
    registration.RegistrationId = regId;
    registration.Tags = new HashSet<string>(YourTags);

	try
	{
		await hub.CreateOrUpdateRegistrationAsync(registration);
	}
	catch (Microsoft.WindowsAzure.Messaging.RegistrationGoneException e)
	{
		// regId likely expired, delete from local storage and try again
		settings.Remove("__NHRegistrationId");
	}


## Administración de registros desde un back-end

Administrar los registros desde el back-end requiere escribir código adicional. La aplicación del dispositivo debe proporcionar el identificador de PNS actualizado al back-end cada vez que se inicie la aplicación (junto con las etiquetas y las plantillas) y, además, el back-end debe actualizar este identificador en el centro de notificaciones. La siguiente ilustración muestra este diseño.

![](./media/notification-hubs-registration-management/notification-hubs-registering-on-backend.png)

Las ventajas que presenta la administración de registros desde el back-end incluyen la capacidad de modificar etiquetas de los registros, incluso cuando la aplicación correspondiente en el dispositivo se encuentra inactiva, y de autenticar la aplicación cliente antes de agregar una etiqueta a su registro.


#### Ejemplo de código para el registro en un centro de notificaciones desde un back-end con una instalación

El dispositivo cliente todavía obtiene su identificador de PNS y las propiedades de instalación importantes, tal como antes, y llama a una API personalizada en el back-end que puede realizar el registro, autorizar etiquetas, etc. El back-end puede usar el [SDK del Centro de notificaciones para operaciones de back-end](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

También puede utilizar el método PATCH con el [estándar JSON-Patch](https://tools.ietf.org/html/rfc6902) para actualizar la instalación.
 

	// Initialize the Notification Hub
	NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(listenConnString, hubName);

    // Custom API on the backend
    public async Task<HttpResponseMessage> Put(DeviceInstallation deviceUpdate)
    {

        Installation installation = new Installation();
        installation.InstallationId = deviceUpdate.InstallationId;
        installation.PushChannel = deviceUpdate.Handle;
        installation.Tags = deviceUpdate.Tags;

        switch (deviceUpdate.Platform)
        {
            case "mpns":
                installation.Platform = NotificationPlatform.Mpns;
                break;
            case "wns":
                installation.Platform = NotificationPlatform.Wns;
                break;
            case "apns":
                installation.Platform = NotificationPlatform.Apns;
                break;
            case "gcm":
                installation.Platform = NotificationPlatform.Gcm;
                break;
            default:
                throw new HttpResponseException(HttpStatusCode.BadRequest);
        }


        // In the backend we can control if a user is allowed to add tags
        //installation.Tags = new List<string>(deviceUpdate.Tags);
        //installation.Tags.Add("username:" + username);

        await hub.CreateOrUpdateInstallationAsync(installation);

        return Request.CreateResponse(HttpStatusCode.OK);
    }


#### Ejemplo de código para el registro en un centro de notificaciones desde un dispositivo con un identificador de registro

Desde el back-end de la aplicación, puede ejecutar operaciones de tipo CRUD en los registros. Por ejemplo:

	var hub = NotificationHubClient.CreateClientFromConnectionString("{connectionString}", "hubName");
            
	// create a registration description object of the correct type, e.g.
	var reg = new WindowsRegistrationDescription(channelUri, tags);

	// Create
	await hub.CreateRegistrationAsync(reg);

	// Get by id
	var r = await hub.GetRegistrationAsync<RegistrationDescription>("id");

	// update
	r.Tags.Add("myTag");

	// update on hub
	await hub.UpdateRegistrationAsync(r);

	// delete
	await hub.DeleteRegistrationAsync(r);


El back-end debe controlar la simultaneidad entre las actualizaciones de los registros. Bus de servicio ofrece un control de simultaneidad optimista para la administración de registros. A nivel de HTTP, se implementa con el uso de ETag en las operaciones de administración de registros. Los SDK de Microsoft utilizan esta característica de manera transparente y generan una excepción si se rechaza una actualización por motivos de simultaneidad. El back-end de aplicación es responsable de controlar estas excepciones y de volver a intentar la actualización, si es necesario.

<!---HONumber=AcomDC_1210_2015-->