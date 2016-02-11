<properties
	pageTitle="Vista previa de Azure AD B2C | Microsoft Azure"
	description="Cómo crear una aplicación web que llame a una API web con Azure AD B2C."
	services="active-directory-b2c"
	documentationCenter=".net"
	authors="dstrockis"
	manager="msmbaldwin"
	editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="dastrock"/>

# Vista previa de Azure AD B2C: llamar a una API web desde una aplicación web de .NET

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Con Azure AD B2C, puede agregar las características de administración de identidades de autoservicio eficaces a sus aplicaciones web y las API web en unos cuantos pasos. En este artículo se le mostrará cómo crear una aplicación web de .NET MVC "Lista de tareas pendientes" que llama a una API web .NET con tokens de portador de OAuth 2.0. Tanto la aplicación web como la API web usan Azure AD B2C para administrar identidades de usuario y autenticar usuarios.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

Este artículo no trata de la implementación de la administración de registros, inicios de sesión y perfiles con Azure AD B2C. Se centra en la llamada a las API web después de que el usuario ya está autenticado. Si no lo ha hecho ya, debe comenzar con el [tutorial de introducción a las aplicaciones web .NET](active-directory-b2c-devquickstarts-web-dotnet.md) para obtener información sobre los conceptos básicos de Azure AD B2C.

## 1\. Obtener un directorio de Azure AD B2C

Para poder usar Azure AD B2C, debe crear un directorio o inquilino. Un directorio es un contenedor para todos los usuarios, aplicaciones, grupos, etc. Si no tiene uno todavía, vaya a [crear un directorio de B2C](active-directory-b2c-get-started.md) antes de continuar.

## 2\. Creación de una aplicación

Ahora debe crear una aplicación en su directorio de B2C, que ofrece a Azure AD información que necesita para comunicarse de forma segura con su aplicación. Tanto la aplicación web como la API web se representarán mediante un **Id. de aplicación** único en este caso, ya que conforman una aplicación lógica. Para crear una aplicación, siga [estas instrucciones](active-directory-b2c-app-registration.md). Asegúrese de

- Incluir una **aplicación web/API web** en la aplicación.
- Escribir `https://localhost:44316/` como **dirección URL de respuesta**: es la dirección URL predeterminada para este ejemplo de código.
- Crear un **Secreto de aplicación ** para la aplicación y copiarlo. Lo necesitará en breve.
- Escribir el **Id. de aplicación** asignado a la aplicación. También lo necesitará en breve.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## 3\. Crear sus directivas

En Azure AD B2C, cada experiencia del usuario se define mediante una [**directiva**](active-directory-b2c-reference-policies.md). Esta aplicación web contiene tres experiencias de identidad: registro, inicio de sesión y editar perfil. Debe crear una directiva de cada tipo, como se describe en el [artículo de referencia de directiva](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Al crear sus tres directivas, asegúrese de:

- Seleccionar **Nombre para mostrar** y algunos otros atributos de registro en la directiva de registro.
- Elegir las notificaciones de aplicación **Nombre para mostrar** e **Id. de objeto** en todas las directivas. Puede elegir también otras notificaciones.
- Copiar el **Nombre** de cada directiva después de crearla. Debe tener el prefijo `b2c_1_`. Necesitará esos nombres de directivas en breve.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Cuando tenga tres directivas creadas correctamente, estará listo para crear su aplicación.

Tenga en cuenta que este artículo no trata de cómo usar las directivas que acaba de crear. Si quiere aprender cómo funcionan las directivas en Azure AD B2C, debe comenzar con el [tutorial de introducción a las aplicaciones web .NET](active-directory-b2c-devquickstarts-web-dotnet.md).

## 4\. Descargar el código

El código de este tutorial se conserva [en GitHub](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet). Para generar el ejemplo a medida que avance, puede [descargar un proyecto de esqueleto como .zip](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/skeleton.zip) o clonar el esqueleto:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet.git
```

La aplicación completada también estará [disponible como .zip](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/complete.zip) o en la rama `complete` del mismo repositorio.

Una vez descargado el código de ejemplo, abra el archivo `.sln` de Visual Studio para empezar. Observará que hay dos proyectos en la solución: un proyecto `TaskWebApp` y un proyecto `TaskService`. `TaskWebApp` es el front-end de la aplicación web WPF con la que el usuario interactúa. `TaskService` es la API web de back-end de la aplicación que almacena la lista de tareas pendientes de cada usuario.

## 5\. Configurar el servicio de tarea

Cuando `TaskService` recibe solicitudes de `TaskWebApp`, busca un token de acceso válido para autenticar la solicitud. Para validar el token de acceso, debe proporcionar a `TaskService` algo de información sobre la aplicación. En el proyecto `TaskService`, abra el archivo `web.config` en la raíz del proyecto y reemplace los valores de la sección `<appSettings>`:

```
<appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/{1}/{2}?p={3}" />
    <add key="ida:Tenant" value="{Enter the name of your B2C tenant - it usually looks like constoso.onmicrosoft.com}" />
    <add key="ida:ClientId" value="{Enter the Application ID assigned to your app by the Azure Portal}" />
    <add key="ida:PolicyId" value="{Enter the name of one of the policies you created, like `b2c_1_my_sign_in_policy`}" />
</appSettings>
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]


Este artículo no aborda los detalles de la protección de `TaskService`. Si quiere saber de qué forma una aplicación web autentica de forma segura las solicitudes con Azure AD B2C, consulte nuestro [artículo de introducción a la API web](active-directory-b2c-devquickstarts-api-dotnet.md).

## 6. Configurar la aplicación web de tarea

Para que `TaskWebApp` se comunique con Azure AD B2C, hay algunos parámetros comunes que debe proporcionar. En el proyecto `TaskWebApp`, abra el archivo `web.config` en la raíz del proyecto y reemplace los valores de la sección `<appSettings>`: Estos valores se usarán en toda la aplicación web.

```
<appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:Tenant" value="{Enter the name of your B2C directory, e.g. contoso.onmicrosoft.com}" />
    <add key="ida:ClientId" value="{Enter the Application Id assinged to your app by the Azure portal, e.g.580e250c-8f26-49d0-bee8-1c078add1609}" />
    <add key="ida:ClientSecret" value="{Enter the Application Secret you created in the Azure portal, e.g. yGNYWwypRS4Sj1oYXd0443n}" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}{1}{2}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="[Enter your sign up policy name, e.g.g b2c_1_sign_up" />
    <add key="ida:SignInPolicyId" value="[Enter your sign in policy name, e.g. b2c_1_sign_in]" />
    <add key="ida:UserProfilePolicyId" value="[Enter your edit profile policy name, e.g. b2c_1_profile_edit" />
    <add key="api:TaskServiceUrl" value="https://localhost:44332/" />
</appSettings>
```     

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Hay también dos decoradores `[PolicyAuthorize]` en los que tiene que indicar el nombre de la directiva de inicio de sesión. El atributo `[PolicyAuthorize]` se usa para invocar una directiva concreta cuando el usuario intenta acceder a una página en la aplicación que requiere autenticación.

```C#
// Controllers\HomeController.cs

[PolicyAuthorize(Policy = "{Enter the name of your sign in policy, e.g. b2c_1_my_sign_in}")]
public ActionResult Claims()
{
```

```C#
// Controllers\TasksController.cs

[PolicyAuthorize(Policy = "{Enter the name of your sign in policy, e.g. b2c_1_my_sign_in}")]
public class TasksController : Controller
{
```

## 7\. Obtener tokens de acceso y llamar a la API de la tarea

En esta sección se mostrará cómo completar un intercambio de tokens de OAuth 2.0 en una aplicación web con las bibliotecas y marcos de trabajo de Microsoft. Si no está familiarizado con los **códigos de autorización** y los **tokens de acceso**, puede ser una buena idea consultar la [Referencia del protocolo de OpenID Connect](active-directory-b2c-reference-protocols.md).

#### Obtener un código de autorización

El primer paso para llamar a la API web de `TaskService` es autenticar al usuario y recibir un **código de autorización** de Azure AD. Puede recibir un código de autorización de Azure AD después de que se ejecute cualquier directiva correctamente, incluyendo las directivas de inicio de sesión, registro y de edición de perfiles.

Para empezar, instale el middleware de OpenID Connect de OWIN mediante la Consola del administrador de paquetes de Visual Studio. Vamos a usar OWIN para enviar la solicitud de autenticación a Azure AD y controlar sus respuestas:

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect -ProjectName TaskWebApp
PM> Install-Package Microsoft.Owin.Security.Cookies -ProjectName TaskWebApp
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TaskWebApp
```

Abra el archivo `App_Start\Startup.Auth.cs`. Aquí es donde configuraremos la canalización de autenticación OWIN, ofreciendo los detalles de su directorio de B2C y la aplicación que ha creado:

```C#
// App_Start\Startup.Auth.cs

public partial class Startup
{
	public const string AcrClaimType = "http://schemas.microsoft.com/claims/authnclassreference";
	public const string PolicyKey = "b2cpolicy";
	public const string OIDCMetadataSuffix = "/.well-known/openid-configuration";

	// App config settings
	public static string clientId = ConfigurationManager.AppSettings["ida:ClientId"];
	public static string clientSecret = ConfigurationManager.AppSettings["ida:ClientSecret"];
	public static string aadInstance = ConfigurationManager.AppSettings["ida:AadInstance"];
	public static string tenant = ConfigurationManager.AppSettings["ida:Tenant"];
	public static string redirectUri = ConfigurationManager.AppSettings["ida:RedirectUri"];

	// B2C policy identifiers
	public static string SignUpPolicyId = ConfigurationManager.AppSettings["ida:SignUpPolicyId"];
	public static string SignInPolicyId = ConfigurationManager.AppSettings["ida:SignInPolicyId"];
	public static string ProfilePolicyId = ConfigurationManager.AppSettings["ida:UserProfilePolicyId"];

	public void ConfigureAuth(IAppBuilder app)
	{
		app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

		app.UseCookieAuthentication(new CookieAuthenticationOptions());

		OpenIdConnectAuthenticationOptions options = new OpenIdConnectAuthenticationOptions
		{
			// These are standard OpenID Connect parameters, with values pulled from web.config
			ClientId = clientId,
			RedirectUri = redirectUri,
			PostLogoutRedirectUri = redirectUri,
			Notifications = new OpenIdConnectAuthenticationNotifications
			{
				AuthenticationFailed = OnAuthenticationFailed,
				RedirectToIdentityProvider = OnRedirectToIdentityProvider,
				AuthorizationCodeReceived = OnAuthorizationCodeReceived,
			},
			Scope = "openid offline_access",

			// The PolicyConfigurationManager takes care of getting the correct Azure AD authentication
			// endpoints from the OpenID Connect metadata endpoint.  It is included in the PolicyAuthHelpers folder.
			ConfigurationManager = new PolicyConfigurationManager(
				String.Format(CultureInfo.InvariantCulture, aadInstance, tenant, "/v2.0", OIDCMetadataSuffix),
				new string[] { SignUpPolicyId, SignInPolicyId, ProfilePolicyId }),

			// This piece is optional - it is used for displaying the user's name in the navigation bar.
			TokenValidationParameters = new System.IdentityModel.Tokens.TokenValidationParameters
			{
				NameClaimType = "name",
			},
		};

		app.UseOpenIdConnectAuthentication(options);
	}
	...
}
```

#### Obtener un token de acceso con el código de autorización

La aplicación web está configurada ahora para autenticar al usuario con su directorio de B2C y para recibir un código de autorización de Azure AD. El siguiente paso es intercambiar este código de autorización para un token de acceso desde Azure AD.

Siempre que sus aplicaciones web de .NET tengan que obtener tokens de acceso de Azure AD, puede usar la **biblioteca de autenticación de Active Directory (ADAL)**. No tiene que usar ADAL para este proceso, pero ADAL lo facilita al encargarse de muchos detalles, como el envío de mensajes de autenticación de OAuth 2.0, el almacenamiento en caché y la actualización de los tokens.

En primer lugar, instale ADAL en el proyecto de `TaskWebApp` mediante la Consola del Administrador de paquetes una vez más:

```
PM> Install-Package Microsoft.Experimental.IdentityModel.Clients.ActiveDirectory -ProjectName TaskWebApp -IncludePrerelease
```

Ahora debe pasar el código de autorización a ADAL para que pueda enviarle tokens. El middleware de OpenID Connect de OWIN ofrece una notificación para que pueda usar este código de autorización; la notificación se desencadenará cada vez que la aplicación reciba un código de autorización de Azure AD. En `App_Start\Startup.Auth.cs`, implemente el controlador de notificaciones `OnAuthorizationCodeReceived` mediante ADAL:

```C#
// App_Start\Startup.Auth.cs

private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification notification)
{
	// The user's objectId is extracted from the claims provided in the id_token, and used to cache tokens in ADAL
	// The authority is constructed by appending your B2C directory's name to "https://login.microsoftonline.com/"
	// The client credential is where you provide your application secret, and is used to authenticate the application to Azure AD
	string userObjectID = notification.AuthenticationTicket.Identity.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
	string authority = String.Format(CultureInfo.InvariantCulture, aadInstance, tenant, string.Empty, string.Empty);
	ClientCredential credential = new ClientCredential(clientId, clientSecret);

	// We don't care which policy is used to access the TaskService, so let's use the most recent policy as indicated in the sign-in token
	string mostRecentPolicy = notification.AuthenticationTicket.Identity.FindFirst(Startup.AcrClaimType).Value;

	// The Authentication Context is ADAL's primary class, which represents your connection to your B2C directory
	// ADAL uses an in-memory token cache by default.  In this case, we've extended the default cache to use a simple per-user session cache
	AuthenticationContext authContext = new AuthenticationContext(authority, new NaiveSessionCache(userObjectID));

	// Here you ask for a token using the web app's clientId as the scope, since the web app and service share the same clientId.
	// The token will be stored in the ADAL token cache, for use in our controllers
	AuthenticationResult result = await authContext.AcquireTokenByAuthorizationCodeAsync(notification.Code, new Uri(redirectUri), credential, new string[] { clientId }, mostRecentPolicy);
}
```

#### Obtener un token de acceso en los controladores

Ahora que tenemos un token de acceso para el back-end de `TaskService` y lo almacenamos en la caché de tokens de ADAL, hemos de usarlo realmente. `TasksController` es responsable de comunicarse con la API de `TaskService` y envía solicitudes HTTP a la API para que lea, cree y elimine tareas. Antes de enviar una solicitud HTTP, obtenga un token de acceso de ADAL:

```C#
// Controllers\TasksController.cs

public async Task<ActionResult> Index()
{
	AuthenticationResult result = null;
	try
	{
		string userObjectID = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
		string authority = String.Format(CultureInfo.InvariantCulture, Startup.aadInstance, Startup.tenant, string.Empty, string.Empty);
		ClientCredential credential = new ClientCredential(Startup.clientId, Startup.clientSecret);

		// We don't care which policy is used to access the TaskService, so let's use the most recent policy
		string mostRecentPolicy = ClaimsPrincipal.Current.FindFirst(Startup.AcrClaimType).Value;

		// Here you ask for a token using the web app's clientId as the scope, since the web app and service share the same clientId.
		// AcquireTokenSilentAsync will return a token from the token cache, and throw an exception if it cannot do so.
		AuthenticationContext authContext = new AuthenticationContext(authority, new NaiveSessionCache(userObjectID));
		result = await authContext.AcquireTokenSilentAsync(new string[] { Startup.clientId }, credential, UserIdentifier.AnyUser, mostRecentPolicy);

		...
	}
	catch (AdalException ee)
	{
		// If ADAL could not get a token silently, show the user an error indicating they might need to sign in again.
		return new RedirectResult("/Error?message=An Error Occurred Reading To Do List: " + ee.Message + " You might need to log out and log back in.");
	}
	...
}
```

ADAL se encargará del almacenamiento en caché de los tokens, de su actualización cuando expiren y de indicarle cuándo debe el usuario iniciar sesión de nuevo generando excepciones. Todo lo que tiene que hacer es llamar a `AuthenticationContext.AcquireTokenSilentAsync(...)` cada vez que necesite un token en la aplicación.

#### Leer tareas de la API web

Ahora que tiene un token, puede asociarlo a la solicitud HTTP GET en el encabezado de `Authorization` para llamar de forma segura a `TaskService`:

```C#
// Controllers\TasksController.cs

public async Task<ActionResult> Index()
{
	...

	try
	{
		HttpClient client = new HttpClient();
		HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, serviceUrl + "/api/tasks");

		// Add the token acquired from ADAL to the request headers
		request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", result.Token);
		HttpResponseMessage response = await client.SendAsync(request);

		if (response.IsSuccessStatusCode)
		{
			String responseString = await response.Content.ReadAsStringAsync();
			JArray tasks = JArray.Parse(responseString);
			ViewBag.Tasks = tasks;
			return View();
		}
		else
		{
			// If the call failed with access denied, then drop the current access token from the cache,
			// and show the user an error indicating they might need to sign-in again.
			if (response.StatusCode == System.Net.HttpStatusCode.Unauthorized)
			{
				var todoTokens = authContext.TokenCache.ReadItems().Where(a => a.Scope.Contains(Startup.clientId));
				foreach (TokenCacheItem tci in todoTokens)
					authContext.TokenCache.DeleteItem(tci);

				return new RedirectResult("/Error?message=Error: " + response.ReasonPhrase + " You might need to sign in again.");
			}
		}

		return new RedirectResult("/Error?message=An Error Occurred Reading To Do List: " + response.StatusCode);
	}
	catch (Exception ex)
	{
		return new RedirectResult("/Error?message=An Error Occurred Reading To Do List: " + ex.Message);
	}
}

```

#### Crear y eliminar tareas de la API web

Puede seguir exactamente el mismo patrón en el envío de solicitudes POST y DELETE al `TaskService`. Solo tiene que llamar a `AuthenticationContext.AcquireTokenSilentAsync(...)` y asociar el token resultante a la solicitud en el encabezado de `Authorization`. La acción `Create` se implementó automáticamente. Intente finalizar usted mismo la acción `Delete` en `TasksController.cs`.

## 8\. Cerrar la sesión del usuario

Un detalle final. Cuando el usuario cierre la sesión de la aplicación web, querrá borrar la caché de tokens de ADAL para quitar los remanentes de la sesión autenticada del usuario:

```C#
// Controllers/AccountController.cs

public void SignOut()
{
	if (Request.IsAuthenticated)
	{
		// When the user signs out, clear their token cache in the process
		string userObjectID = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
		string authority = String.Format(CultureInfo.InvariantCulture, Startup.aadInstance, Startup.tenant, string.Empty, string.Empty);
		AuthenticationContext authContext = new AuthenticationContext(authority, new NaiveSessionCache(userObjectID));
		authContext.TokenCache.Clear();

		HttpContext.GetOwinContext().Authentication.SignOut(
		new AuthenticationProperties(
			new Dictionary<string, string>
			{
				{Startup.PolicyKey, ClaimsPrincipal.Current.FindFirst(Startup.AcrClaimType).Value}
			}), OpenIdConnectAuthenticationDefaults.AuthenticationType, CookieAuthenticationDefaults.AuthenticationType);
	}
}
```

## 9\. Ejecutar la aplicación de ejemplo

Por último, compile y ejecute tanto `TaskClient` como `TaskService`. Regístrese o inicie sesión en la aplicación y cree tareas para el usuario que ha iniciado sesión. Cierre sesión y vuelva a iniciarla como otro usuario, y cree tareas para ese usuario. Observe cómo se almacenan las tareas por usuario en la API, puesto que la API extrae la identidad del usuario del token de acceso que recibe.

Como referencia, el ejemplo finalizado [se proporciona en forma de archivo .zip aquí](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/complete.zip), aunque también puede clonarlo desde GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet.git```

<!--

## Next Steps

You can now move onto more advanced B2C topics.  You may want to try:

[Calling a Web API from a Web App >>]()

[Customizing the your B2C App's UX >>]()

-->

<!---HONumber=AcomDC_0128_2016-->