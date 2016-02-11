<properties 
	pageTitle="Uso de una aplicación de API del Servicio de aplicaciones de Azure desde un cliente .NET" 
	description="Aprenda a usar una aplicación de API desde un cliente .NET mediante el SDK del Servicio de aplicaciones." 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/08/2016" 
	ms.author="tdykstra"/>

# Uso de una aplicación de API del Servicio de aplicaciones de Azure desde un cliente .NET 

[AZURE.INCLUDE [app-service-api-v2-note](../../includes/app-service-api-v2-note.md)]

## Información general

Este tutorial muestra cómo usar el SDK del Servicio de aplicaciones para escribir código que llama a una [aplicación de API](app-service-api-apps-why-best-platform.md) configurada para el nivel de acceso **Público (anónimo)** o **Público (autenticado)**. En este artículo se tratan los escenarios siguientes:

- Llamar a una aplicación de API de acceso **Público (anónimo)** desde una aplicación de consola
- Llamar a una aplicación de API de acceso **Público (autenticado)** desde una aplicación de escritorio de Windows 

Cada una de las secciones de este tutorial es independiente: se pueden seguir las instrucciones del segundo escenario sin haber completado los pasos de la primera de ellas.

Para obtener información sobre cómo efectuar una llamada a una aplicación de API **interna**, consulte [Consumo de una aplicación de API interna desde un cliente .NET](app-service-api-dotnet-consume-internal.md).

## Requisitos previos

En el tutorial se supone que sabe cómo crear proyectos y agregarles código en Visual Studio y cómo [administrar Aplicaciones de API en el portal de vista previa de Azure](app-service-api-apps-manage-in-portal.md).

El proyecto y los ejemplos de código de este artículo se basan en el proyecto de Aplicaciones de API que se crea, implementa y protege en estos artículos:

- [Creación de una clave de API](app-service-dotnet-create-api-app.md)
- [Implementación de una aplicación de API](app-service-dotnet-deploy-api-app.md)
- [Protección de una aplicación API](../app-service-api-dotnet-add-authentication.md)

[AZURE.INCLUDE [install-sdk-2013-only](../../includes/install-sdk-2013-only.md)]

Este tutorial requiere la versión 2.6 o posterior de SDK de Azure para .NET.

## Llamada no autenticada desde una aplicación de consola

En esta sección se crea un proyecto de aplicación de consola y se agrega código que llama a una aplicación de API que no requiere autenticación.

### Configuración de la aplicación de API y creación del proyecto

1. Si aún no lo ha hecho, siga los pasos del tutorial [Implementación de una aplicación de API](app-service-dotnet-deploy-api-app.md) para implementar el proyecto de ejemplo ContactsList en una aplicación de API en su suscripción de Azure.

	En este tutorial se le indica que establezca el nivel acceso en el cuadro de diálogo de publicación de Visual Studio en **Disponible para cualquier persona**, que es lo mismo que **Público (anónimo)** en el portal. Sin embargo, si después del paso anterior realizó el tutorial [Protección de una aplicación de API](../app-service-api-dotnet-add-authentication.md), el nivel de acceso se ha establecido en **Público (autenticado)** y, en ese caso, deberá cambiarlo según lo indicado en el paso siguiente.

2. En el [Portal de vista previa de Azure](https://portal.azure.com/), en la hoja **Aplicación de API** de la aplicación de API que desea llamar, vaya a **Configuración > Configuración de la aplicación** y establezca **Nivel de acceso** en **Público (anónimo)**.

	![](./media/app-service-api-dotnet-consume/setpublicanon.png)
 
2. En Visual Studio, cree un proyecto de aplicación de consola.
 
### <a id="addclient"></a>Agregar código de cliente generado por el SDK del Servicio de aplicaciones

[AZURE.INCLUDE [app-service-api-dotnet-add-generated-client](../../includes/app-service-api-dotnet-add-generated-client.md)]

### Agregar código para llamar a la aplicación de API

Para llamar a la aplicación de API, lo único que debe hacer es crear un objeto de cliente y llamar a los métodos que hay en él, como en este ejemplo:

        var client = new ContactList();
        var contacts = client.Contacts.Get();

1. Abra *Program.cs* y agregue el código siguiente dentro del método `Main`.

		var client = new ContactsList();
		
		// Send GET request.
		var contacts = client.Contacts.Get();
		foreach (var c in contacts)
		{
		    Console.WriteLine("{0}: {1} {2}",
		        c.Id, c.Name, c.EmailAddress);
		}
		
		// Send POST request.
		client.Contacts.Post(new Models.Contact
		{
		    EmailAddress = "lkahn@contoso.com",
		    Name = "Loretta Kahn",
		    Id = 4
		});
		Console.WriteLine("Finished");
		Console.ReadLine();

3. Presione CTRL+F5 para ejecutar la aplicación.

	![Generación completa](./media/app-service-api-dotnet-consume/consoleappoutput.png)

## Llamada autenticada desde una aplicación de escritorio de Windows

En esta sección, creará un proyecto de aplicación de escritorio de Windows y agregará código que llama a una aplicación de API que requiere autenticación.

### Configuración de la aplicación de API y creación del proyecto

1. Siga el tutorial [Protección de una aplicación de API](../app-service-api-dotnet-add-authentication.md) para configurar una aplicación de API con nivel de acceso **Público (autenticado)**.

1. En Visual Studio, cree un proyecto de escritorio de Windows Forms.

2. En el diseñador de formularios, agregue los siguientes controles:

	* Un control de botón
	* Un control de cuadro de texto
	* Un control de explorador web

3. Establezca el control de cuadro de texto en varias líneas.

	El formulario debe tener un aspecto similar al ejemplo siguiente.

	![](./media/app-service-api-dotnet-consume/form.png)

### Agregar código de cliente generado por el SDK del Servicio de aplicaciones

3. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto (no en la solución) y seleccione **Agregar > Cliente de aplicación de API de Azure**. 

3. En el cuadro de diálogo **Agregar cliente de aplicación de API de Azure**, haga clic en **Descargar de la aplicación de API de Azure**.

5. En la lista desplegable, seleccione la aplicación de API que desea llamar y haga clic en **Aceptar**.

### Agregar código para llamar a la aplicación de API

5. En el Portal de vista previa de Azure, copie la dirección URL de la puerta de enlace de la aplicación de API. Usará este valor en el paso siguiente.

	![](./media/app-service-api-dotnet-consume/gatewayurl.png)

4. En el código fuente *Form1.cs*, agregue el siguiente código antes del constructor `Form1()` y reemplace el valor de GATEWAY\_URL por el valor que copió en el paso anterior. Asegúrese de incluir la barra diagonal final (/).

		private const string GATEWAY_URL = "https://resourcegroupnameb4f3d966dfa43b6607f30.azurewebsites.net/";
		private const string URL_TOKEN = "#token=";

4. En el diseñador de formularios, haga doble clic en el botón para agregar un controlador de clic y, en el método de controlador, agregue código para ir a la dirección URL de inicio de sesión para la puerta de enlace, por ejemplo:

		webBrowser1.Navigate(string.Format(@"{0}login/[authprovider]", GATEWAY_URL));

	Reemplace "[authprovider]" por el código del proveedor de servicios de identidad que configuró en la puerta de enlace, por ejemplo, "aad", "twitter", "google", "microsoftaccount" o "facebook". Por ejemplo:

		webBrowser1.Navigate(string.Format(@"{0}login/aad", GATEWAY_URL));

7. Agregue un controlador de eventos `DocumentCompleted` para el control de explorador web y agregue el siguiente código al método de controlador.

		if (e.Url.AbsoluteUri.IndexOf(URL_TOKEN) > -1)
		{
		    var encodedJson = e.Url.AbsoluteUri.Substring(e.Url.AbsoluteUri.IndexOf(URL_TOKEN) + URL_TOKEN.Length);
		    var decodedJson = Uri.UnescapeDataString(encodedJson);
		    var result = JsonConvert.DeserializeObject<dynamic>(decodedJson);
		    string userId = result.user.userId;
		    string userToken = result.authenticationToken;
		
		    var appServiceClient = new AppServiceClient(GATEWAY_URL);
		    appServiceClient.SetCurrentUser(userId, userToken);
		
		    var contactsListClient = appServiceClient.CreateContactsList();
		    var contacts = contactsListClient.Contacts.Get();
		    foreach (Contact contact in contacts)
		    {
		        textBox1.Text += contact.Name + " " + contact.EmailAddress + System.Environment.NewLine;
		    }
		    //appServiceClient.Logout();
		    //webBrowser1.Navigate(string.Format(@"{0}login/aad", GW_URL));
		}

	El código que haya agregado se ejecuta después de que el usuario inicie sesión con el control del explorador web. Después de iniciar sesión correctamente, la dirección URL de respuesta contiene el identificador y la contraseña del usuario. El código extrae estos valores de la dirección URL, los proporciona al objeto de cliente de Servicio de aplicaciones y usa el objeto para crear un objeto de cliente de aplicación de API. A continuación, puede llamar a la API llamando a métodos en este objeto de cliente de aplicación de API.

8. Presione CTRL+F5 para ejecutar la aplicación.

9. Haga clic en el botón y, cuando el control de explorador muestre una página de inicio de sesión, escriba las credenciales de un usuario autorizado para llamar a la aplicación de API.

	Azure le autentica y la aplicación llama a la aplicación de API y muestra la respuesta.

	![](./media/app-service-api-dotnet-consume/formaftercall.png)

### <a id="client-flow"></a>Flujo de servidor frente a flujo de cliente

En la aplicación de ejemplo se ilustra el [flujo de servidor](../app-service/app-service-authentication-overview.md#server-flow), lo que significa que la puerta de enlace obtiene el token de acceso del proveedor de identidades. Para el [flujo de cliente](../app-service/app-service-authentication-overview.md#client-flow), en que la aplicación cliente obtiene el token de acceso directamente del proveedor de identidades y lo envía a la puerta de enlace, se llama a `LoginAsync` en lugar de a `SetCurrentUser`.

En el ejemplo de código siguiente se supone que tiene el token de acceso del proveedor de identidades en una variable de cadena denominada `providerAccessToken` y el indicador de proveedor de identidades ("aad", "microsoftaccount", "googlean", "twitter" o "facebook") en una variable de cadena denominada `idProvider`:

		var appServiceClient = new AppServiceClient(GATEWAY_URL);
		var providerAccessTokenJSON = new JObject();
		providerAccessTokenJSON["access_token"] = providerAccessToken;
		var appServiceUser = await appServiceClient.LoginAsync(idProvider, providerAccessTokenJSON);

		var contactsListClient = appServiceClient.CreateContactsList();
		var contacts = contactsListClient.Contacts.Get();
		foreach (Contact contact in contacts)
		{
		    textBox1.Text += contact.Name + " " + contact.EmailAddress + System.Environment.NewLine;
		}

## Pasos siguientes

En este artículo se ha mostrado cómo usar una aplicación API desde un cliente .NET para aplicaciones de API establecidas en niveles de acceso **Público (autenticado)** y **Público (anónimo)**.

Para obtener más ejemplos de código que llama a una aplicación de API de clientes. NET, descargue la aplicación de ejemplo [Tarjetas de Azure](https://github.com/Azure-Samples/API-Apps-DotNet-AzureCards-Sample).

Para obtener información sobre cómo usar la autenticación en aplicaciones de API, consulte [Autenticación para aplicaciones de API y aplicaciones de móvil en el Servicio de aplicaciones de Azure](../app-service/app-service-authentication-overview.md).
 

<!---HONumber=AcomDC_0114_2016-->