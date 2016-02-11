<properties 
	pageTitle="Llamada a una aplicación de API desde un cliente autenticado" 
	description="Aprenda a llamar a una aplicación de API de Azure desde un cliente de aplicación web autenticado por Azure Active Directory." 
	keywords="servicio de aplicaciones, servicio de aplicaciones de azure,autenticación,autenticación de azure,api,api de autenticación"
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

# Llamada a una aplicación de API de Azure desde un cliente de aplicación web autenticado por Azure Active Directory

## Información general

En este tutorial, se muestra cómo llamar una aplicación de API protegida por Azure Active Directory (AAD) desde una aplicación web también protegida por AAD. Cuando complete el tutorial, tendrá una aplicación web y una aplicación de API en ejecución en la suscripción de Azure. La aplicación web mostrará datos que se obtienen mediante la llamada a la aplicación de API, tal como se muestra en la captura de pantalla siguiente.

![](./media/app-service-api-authentication-client-flow/aboutpage.png)

Aprenderá a pasar credenciales de AAD desde la aplicación web a la aplicación de API para que la aplicación de API no se las vuelva a pedir al usuario para iniciar sesión.

Realizará los pasos siguientes:

- Crear una aplicación de API y configurarla para usar la autenticación de AAD.
- Crear una aplicación web y configurarla para usar la autenticación de AAD.
- Agregar código a la aplicación web para llamar a la aplicación de API protegida.
- Implementar la aplicación web y la aplicación de API en Azure.
- Probar para comprobar que la aplicación web puede llamar la aplicación de API.

### Autenticación de flujo de cliente

El código que agrega a la aplicación web implementará un proceso llamado autenticación de [flujo de cliente](../app-service/app-service-authentication-overview.md#client-flow). El diagrama siguiente ilustra el proceso que se realiza para obtener el token de acceso de AAD e intercambiarlo por un token de la aplicación de API (Zumo).

![](./media/app-service-api-authentication-client-flow/clientflow.png)

Si no está familiarizado con el rol de la puerta de enlace de la aplicación de API en la autenticación de las aplicaciones de API, consulte [Autenticación para aplicaciones de API y aplicaciones móviles](../app-service/app-service-authentication-overview.md).

## Requisitos previos

Antes de comenzar este tutorial, asegúrese de tener instalado [Visual Studio 2015](https://www.visualstudio.com/) y el [SDK de Azure para .NET](http://go.microsoft.com/fwlink/?linkid=518003). Las mismas instrucciones funcionarán también en Visual Studio 2013.

En este tutorial, se supone que sabe cómo trabajar con proyectos web en Visual Studio.

## Creación y protección de una aplicación de API con AAD

1. Cree o descargue un proyecto de aplicación de API.
 
	* Para crear un proyecto, siga las instrucciones que aparecen en [Creación de una aplicación de API](app-service-dotnet-create-api-app.md).

	* Para descargar un proyecto, obténgalo en el [repositorio ContactsList de GitHub](https://github.com/tdykstra/ContactsList).

	Ahora tiene un proyecto de API de web que devuelve datos de contacto.

	![](./media/app-service-api-authentication-client-flow/swaggerlocal.png)

2. Implemente el proyecto de aplicación de API en una aplicación de API nueva en Azure.

	Siga las instrucciones que aparecen en [Implementación de una aplicación de API](app-service-dotnet-deploy-api-app.md).

	En el [Portal de vista previa de Azure], la hoja **Definición de API** de la aplicación de API ahora muestra los métodos Get y Post del proyecto de API web que implementó.

	![](./media/app-service-api-authentication-client-flow/apiappinportal.png)

4. Proteja la aplicación de API con Azure Active Directory (AAD) como el proveedor de identidades.
 
	Siga las instrucciones para AAD que aparecen en [Protección de una aplicación de API](app-service-api-dotnet-add-authentication.md). No es necesario que realice la sección **Uso de Postman para enviar una solicitud Post** del tutorial.

	Cuando termine, la hoja **Azure Active Directory** de la aplicación de API en el [Portal de vista previa de Azure] tiene el id. de cliente de la aplicación de AAD y su inquilino de AAD.

	![](./media/app-service-api-authentication-client-flow/aadblade.png)

	Además, la pestaña **Configurar** de la aplicación de AAD en el [Portal de Azure] tiene la dirección URL del id. de la aplicación de la aplicación de API y la dirección URL de respuesta.

	![](./media/app-service-api-authentication-client-flow/aadconfig.png)

## Creación y protección de una aplicación web con AAD

En esta sección, descargará y configurará un proyecto web configurado para la autenticación de AAD. El proyecto tiene una página **Acerca de** que muestra información de notificaciones del usuario con sesión iniciada.

![](./media/app-service-api-authentication-client-flow/aboutpagestart.png)

Más adelante, modificará esta página al agregar una sección para mostrar información de contacto recuperada desde la aplicación de API.

1. Descargue el proyecto web desde el [repositorio WebApp-GroupClaims-DotNet](https://github.com/AzureADSamples/WebApp-GroupClaims-DotNet/).
 
2. Siga las instrucciones de **Ejecución del ejemplo** en el [archivo Léame](https://github.com/AzureADSamples/WebApp-GroupClaims-DotNet/blob/master/README.md), con las siguientes excepciones:
 
	* Puede utilizar Visual Studio 2015, a pesar de que el archivo Léame indica que se debe usar Visual Studio 2013. 

	* Use la aplicación de AAD que ya creó en lugar de crear una nueva.
 
	* Conserve el mismo **URI de id. de aplicación** que ya tiene para la aplicación de AAD. (No lo cambie al formato que se especifica en el archivo Léame).
	
	* Cambie otras configuración de la aplicación de AAD según se le indique; en concreto, establezca el inicio de sesión y las URL de respuesta en la URL base para la aplicación de ejemplo.

Como conserva el mismo URI del id. de la aplicación que creó para la aplicación de ID, puede usar el mismo token de acceso de AAD para la aplicación web y la aplicación de API. Si cambió el URI de id. de aplicación al formato indicado en el archivo Léame, podría funcionar para el acceso a la aplicación web, pero no para la aplicación de API. No sería posible pasar el token de AAD a la puerta de enlace de la aplicación de API para obtener un token de Zumo, debido a que la puerta de enlace espera un token para un URI de id. de aplicación compuesto de la dirección URL de la puerta de enlace más "/login/aad".

## Incorporación del código de cliente generado para la aplicación de API

En esta sección, agregará automáticamente código generado para una interfaz con tipo para llamar a la aplicación de API.

8.	En el **Explorador de soluciones** de Visual Studio, haga clic con el botón derecho en el proyecto WebApp-GroupClaims-DotNet y luego haga clic en **Agregar > Cliente de aplicación de API de Azure**.

9.	En el cuadro de diálogo **Agregar cliente de aplicación de API de Microsoft Azure**, seleccione la aplicación de API que protegió con AAD.

	Una vez completada la generación de código, verá una nueva carpeta en el **Explorador de soluciones**, con el nombre de la aplicación de API. Esta carpeta contiene el código que implementa las clases y los modelos de datos de cliente.

10. Corrija las referencias ambiguas que provoca el código generado en *ContactsList/ContactsExtensions.cs*: cambie las dos instancias de `Task.Factory.StartNew` a `System.Threading.Tasks.Task.Factory.StartNew`.
 
## Incorporación de código para intercambiar el token de AAD por un token de Zumo

1.	En HomeController.cs, agregue instrucciones `using` para el serializador JSON y el SDK de Servicio de aplicaciones.

		using Microsoft.Azure.AppService;
		using Newtonsoft.Json.Linq;

2. Agregue constantes para la dirección URL de la puerta de enlace y el URI de id. de aplicación de la aplicación AAD. Asegúrese de que la dirección URL de la puerta de enlace sea https, no http.

		private const string GATEWAY_URL = "https://clientflowgroupaeb4ae60b7cb4f3d966dfa43b6607f30.azurewebsites.net/";
		private const string APP_ID_URI = GATEWAY_URL + "login/aad";

2.	Agregue un método que obtiene un objeto de cliente para llamar a la aplicación de API.

		public async Task<AppServiceClient> GetAppServiceClient()
		{
		    var appServiceClient = new AppServiceClient(GATEWAY_URL);
		    string userObjectID = ClaimsPrincipal.Current.FindFirst
		        ("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
		
		    var authContext = new AuthenticationContext
		        (ConfigHelper.Authority, new TokenDbCache(userObjectID));
		
		    ClientCredential credential = new ClientCredential
		        (ConfigHelper.ClientId, ConfigHelper.AppKey);
		
		    // Get the AAD token.
		    AuthenticationResult result = authContext.AcquireToken(APP_ID_URI, credential);
		    var aadToken = new JObject();
		    aadToken["access_token"] = result.AccessToken;
		
		    // Send the AAD token to the gateway and get a Zumo token
		    var appServiceUser = await appServiceClient.LoginAsync
		        ("aad", aadToken).ConfigureAwait(false);
		
		    return appServiceClient;
		}

	En este código, `ConfigHelper.Authority` se resuelve en "https://login.microsoftonline.com/{su inquilino}"; por ejemplo: "https://login.microsoftonline.com/contoso.onmicrosoft.com".

2.	Agregue código inmediatamente antes de la instrucción `return View()` al final del método `About` para llamar a la aplicación de API. (En el paso siguiente, agregará código a la vista `About` para mostrar los datos devueltos).

		var appServiceClient = await GetAppServiceClient();
		var client = appServiceClient.CreateContactsList();
		var contacts = client.Contacts.Get();
		ViewData["contacts"] = contacts;

3. En *Views/Home/About.cshtml*, agregue código inmediatamente después del encabezado `h2` para mostrar la información de contacto.

		<h3>Contacts</h3>
		<table class="table table-striped table-bordered table-condensed table-hover">
		    <tr>
		        <th>Name</th>
		        <th>Email</th>
		    </tr>
		
		    @foreach (WebAppGroupClaimsDotNet.Models.Contact contact in (IList<WebAppGroupClaimsDotNet.Models.Contact>)ViewData["contacts"])
		    {
		        <tr>
		            <td>@contact.Name</td>
		            <td>@contact.EmailAddress</td>
		        </tr>
		    }
		
		</table>


3. En el archivo Web.config de la aplicación, agregue el siguiente redireccionamiento de enlace después de la etiqueta `<assemblyBinding>` de apertura.

		<dependentAssembly>
		  <assemblyIdentity name="System.Net.Http.Primitives" publicKeyToken="b03f5f7f11d50a3a" culture="neutral"/>
		  <bindingRedirect oldVersion="0.0.0.0-4.2.28.0" newVersion="4.2.28.0"/>
		</dependentAssembly>

	Sin este redireccionamiento de enlace, la aplicación presentará un error debido a que el SDK de Servicio de aplicaciones incluye una dependencia de la versión 1.5.0.0 de System.Net.Http.Primitives, pero el proyecto usa la versión 4.2.28.0.
 
3. Siga las instrucciones que aparecen en **Implementar este ejemplo en Azure** en el [archivo Léame](https://github.com/AzureADSamples/WebApp-GroupClaims-DotNet/).

5. Ejecute la aplicación.

	![](./media/app-service-api-authentication-client-flow/homepage.png)

6. Haga clic en **Iniciar sesión** y luego escriba las credenciales de un usuario en el dominio de AAD que usa para esta aplicación.

	![](./media/app-service-api-authentication-client-flow/signedin.png)

7. Haga clic en **Acerca de**.

	El código que agregó en la vista y el controlador Acerca de ejecuta y muestra que se autenticó en la aplicación de API y llamó a su método Get correctamente.

	![](./media/app-service-api-authentication-client-flow/aboutpage.png)

## Solución de problemas

### Errores HTTP 400 

Asegúrese de que la dirección URL de la puerta de enlace sea https, no http. Cuando se copia directamente desde el Portal de vista previa de Azure, es posible que se especifique http.
 
### Errores de SQL Server

La aplicación requiere acceso a una base de datos de SQL Server identificada en la cadena de conexión GroupClaimContext. Asegúrese de que la cadena de conexión en el sitio implementado apunte a una instancia de Base de datos SQL. Puede colocar la cadena de conexión correcta en el archivo Web.config implementado o en las opciones de configuración de tiempo de ejecución de Azure.

## Agradecimientos

Agradecemos a Govind S. Yadav ([@govindsyadav](https://twitter.com/govindsyadav)) su ayuda en el desarrollo de este tutorial.

## Pasos siguientes

Aprendió a realizar la autenticación de flujo de cliente para aplicaciones de API del Servicio de aplicaciones. Para información sobre otras formas de controlar la autenticación en aplicaciones de API, consulte [Autenticación de aplicaciones de API y aplicaciones móviles](../app-service/app-service-authentication-overview.md).

[Portal de Azure]: https://manage.windowsazure.com/
[Portal de vista previa de Azure]: https://portal.azure.com/

<!---HONumber=AcomDC_0114_2016-->