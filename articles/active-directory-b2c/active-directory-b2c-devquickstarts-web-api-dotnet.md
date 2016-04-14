<properties
	pageTitle="Versión preliminar de Azure Active Directory B2C | Microsoft Azure"
	description="Creación de una aplicación web que llama a una API web mediante Azure Active Directory B2C."
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

# Versión preliminar de Azure AD B2C: llamada a una API web desde una aplicación web de .NET


<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Con Azure Active Directory (Azure AD) B2C, puede agregar eficaces características de administración de identidades autoservicio tanto a sus aplicaciones web como a las API web en pocos pasos. En este artículo se describe cómo crear una aplicación web de "lista de tareas pendientes" de controlador de vista de modelos (MV) .NET que llama a una API web .NET mediante tokens de portador de OAuth 2.0. Tanto la aplicación web como la API web usan Azure AD B2C para autenticar a los usuarios y administrar sus identidades.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

Este artículo no trata de la implementación de la administración de registros, inicios de sesión y perfiles con Azure AD B2C. Se centra en la llamada a las API web después de que el usuario ya está autenticado. Si aún no lo ha hecho, debe comenzar por el [tutorial de introducción a las aplicaciones web .NET](active-directory-b2c-devquickstarts-web-dotnet.md) para conocer los conceptos básicos de Azure AD B2C.

## Obtener un directorio de Azure AD B2C

Para poder usar Azure AD B2C, debe crear un directorio o inquilino. Un directorio es un contenedor de todos los usuarios, aplicaciones, grupos, etc. Si aún no tiene uno, [cree un directorio B2C](active-directory-b2c-get-started.md) antes de continuar con esta guía.

## Creación de una aplicación

A continuación, debe crear una aplicación en su directorio B2C. Esto proporciona a Azure AD la información que necesita para comunicarse de forma segura con la aplicación. En este caso, tanto la aplicación web como la API web se representarán mediante un **identificador de aplicación** único, ya que conforman una aplicación lógica. Para crear una aplicación, siga [estas instrucciones](active-directory-b2c-app-registration.md). Asegúrese de:

- Incluir una **aplicación web o una API web** en la aplicación.
- Escribir `https://localhost:44316/` como **URL de respuesta**. Es la dirección URL predeterminada para este ejemplo de código.
- Crear un **secreto de aplicación** para la aplicación y copiarlo. Lo necesitará más adelante. Tenga en cuenta que para poder usar este valor es preciso [incluirlo entre secuencias de escape de XML](https://www.w3.org/TR/2006/REC-xml11-20060816/#dt-escape).
- Copiar el **identificador de aplicación** asignado a la aplicación. También lo necesitará más adelante.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## Crear sus directivas

En Azure AD B2C, cada experiencia de usuario se define mediante una [directiva](active-directory-b2c-reference-policies.md). Esta aplicación web contiene tres experiencias de identidad: registro, inicio de sesión y edición de un perfil. Es preciso que cree una directiva de cada tipo, como se describe en el [artículo de referencia de las directivas](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Cuando cree las tres directivas, asegúrese de:

- Elegir el **nombre para mostrar** y los restantes atributos de registro en la directiva de registro.
- Elegir las notificaciones de aplicación **Nombre para mostrar** e **Id. de objeto** en todas las directivas. Puede elegir también otras notificaciones.
- Copiar el **nombre** de cada directiva después de crearla. Debe tener el prefijo `b2c_1_`. Necesitará esos nombres de directiva más adelante.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Una vez creadas las tres directivas, está listo para compilar la aplicación.

Tenga en cuenta que este artículo no trata sobre cómo usar las directivas que acaba de crear. Para obtener información acerca del funcionamiento de las directivas en Azure AD B2C, comience con el [tutorial de introducción a las aplicaciones web .NET](active-directory-b2c-devquickstarts-web-dotnet.md).

## Descargar el código

El código de este tutorial [se mantiene en GitHub](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet). Para compilar el ejemplo a medida que avance, puede [descargar un proyecto de esqueleto como un archivo .zip](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/skeleton.zip). También puede clonar el esqueleto:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet.git
```

La aplicación completada también estará [disponible como archivo .zip](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/complete.zip) o en la rama `complete` del mismo repositorio.

Una vez descargado el código de ejemplo, abra el archivo .sln de Visual Studio para empezar. Observará que hay dos proyectos en la solución: un proyecto `TaskService` y un proyecto `TaskWebApp`. El elemento `TaskWebApp` es el front-end de la aplicación web de Windows Presentation Foundation (WPF) con la que interactúa el usuario. `TaskService` es la API web del back-end de la aplicación que almacena la lista de tareas pendientes de cada usuario.

## Configurar el servicio de tarea

Cuando `TaskService` recibe una solicitud de `TaskWebApp`, busca un token de acceso válido para autenticar la solicitud. Para validar el token de acceso, debe proporcionar a `TaskService` información sobre la aplicación. En el proyecto `TaskService`, abra el archivo `web.config` en la raíz del proyecto y reemplace los valores de la sección `<appSettings>`:

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


En este artículo no se cubren los detalles de cómo proteger `TaskService`. Para saber de qué forma una API web autentica de forma segura las solicitudes con Azure AD B2C, consulte nuestro [artículo de introducción a la API web](active-directory-b2c-devquickstarts-api-dotnet.md).

## Configurar la aplicación web de tarea

Para que `TaskWebApp` se comunique con Azure AD B2C, es preciso especificar algunos parámetros comunes. En el proyecto `<appSettings>`, abra el archivo `TaskWebApp` en la raíz del proyecto y reemplace los valores de la sección `web.config`: Estos valores se usarán en toda la aplicación web.

```
<appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:Tenant" value="{Enter the name of your B2C directory, e.g. contoso.onmicrosoft.com}" />
    <add key="ida:ClientId" value="{Enter the Application Id assigned to your app by the Azure portal, e.g.580e250c-8f26-49d0-bee8-1c078add1609}" />
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

Hay también dos decoradores `[PolicyAuthorize]` en los que tiene que indicar el nombre de la directiva de inicio de sesión. El atributo `[PolicyAuthorize]` se usa para invocar una directiva concreta cuando el usuario intenta acceder a una página de la aplicación que requiere autenticación.

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

## Obtener tokens de acceso y llamar a la API de la tarea

En esta sección se describe cómo completar un intercambio de tokens de OAuth 2.0 en una aplicación web con bibliotecas y marcos de trabajo de Microsoft. Si no está familiarizado con los códigos de autorización y los tokens de acceso, puede aprender más cosas en la [referencia del protocolo OAuth 2.0](active-directory-b2c-reference-protocols.md).

### Obtener un código de autorización

El primer paso en una llamada de API de `TaskService` es autenticar al usuario y recibir un código de autorización de Azure AD. Puede recibir un código de autorización de Azure AD después de ejecutar cualquier directiva correctamente, por ejemplo, directivas de inicio de sesión, registro y edición de perfiles.

Para comenzar, instale el software intermedio de OpenID Connect, OWIN, mediante la Consola del Administrador de paquetes de Visual Studio. Vamos a usar OWIN para enviar las solicitudes de autenticación a Azure AD y administrar sus respuestas:

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect -ProjectName TaskWebApp
PM> Install-Package Microsoft.Owin.Security.Cookies -ProjectName TaskWebApp
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TaskWebApp
```

Abra el archivo `App_Start\Startup.Auth.cs`. Aquí es donde configuraremos la canalización de autenticación de OWIN y proporcionaremos los detalles de su directorio de B2C y la aplicación que ha creado:

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
			// endpoints from the OpenID Connect metadata endpoint. It is included in the PolicyAuthHelpers folder.
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

### Obtención de un token de acceso mediante el código de autorización

La aplicación web está configurada ahora para autenticar a los usuarios mediante su directorio B2C y para recibir códigos de autorización de Azure AD. El paso siguiente consiste en intercambiar códigos de autorización por tokens de acceso de Azure AD.

Siempre que sus aplicaciones web de .NET necesiten obtener tokens de acceso de Azure AD, puede usar la biblioteca de autenticación de Active Directory (ADAL). Aunque no tiene que usar ADAL en este proceso, le facilita la tarea al ocuparse de muchos de los detalles; por ejemplo, el envío de mensajes de autenticación OAuth 2.0, el almacenamiento en caché y la actualización de los tokens.

En primer lugar, instale ADAL en el proyecto de `TaskWebApp` mediante la Consola del Administrador de paquetes:

```
PM> Install-Package Microsoft.Experimental.IdentityModel.Clients.ActiveDirectory -ProjectName TaskWebApp -IncludePrerelease
```

A continuación, debe pasar el código de autorización a ADAL para que pueda enviarle tokens. El software intermedio de OpenID Connect, OWIN, le proporciona una notificación para que utilice este código de autorización. La notificación se enviará cada vez que la aplicación reciba un código de autorización de Azure AD. En `App_Start\Startup.Auth.cs`, implemente el controlador de notificaciones `OnAuthorizationCodeReceived` mediante ADAL:

```C#
// App_Start\Startup.Auth.cs

private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification notification)
{
	// The user's objectId is extracted from the claims provided in the id_token, and used to cache tokens in ADAL
	// The authority is constructed by appending your B2C directory's name to "https://login.microsoftonline.com/"
	// The client credential is where you provide your application secret, and it is used to authenticate the application to Azure AD
	string userObjectID = notification.AuthenticationTicket.Identity.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
	string authority = String.Format(CultureInfo.InvariantCulture, aadInstance, tenant, string.Empty, string.Empty);
	ClientCredential credential = new ClientCredential(clientId, clientSecret);

	// We don't care which policy is used to access the TaskService, so let's use the most recent policy as indicated in the sign-in token
	string mostRecentPolicy = notification.AuthenticationTicket.Identity.FindFirst(Startup.AcrClaimType).Value;

	// The Authentication Context is ADAL's primary class, which represents your connection to your B2C directory
	// ADAL uses an in-memory token cache by default. In this case, we've extended the default cache to use a simple per-user session cache
	AuthenticationContext authContext = new AuthenticationContext(authority, new NaiveSessionCache(userObjectID));

	// Here you ask for a token by using the web app's clientId as the scope, because the web app and service share the same clientId.
	// The token will be stored in the ADAL token cache for use in our controllers
	AuthenticationResult result = await authContext.AcquireTokenByAuthorizationCodeAsync(notification.Code, new Uri(redirectUri), credential, new string[] { clientId }, mostRecentPolicy);
}
```

### Obtener un token de acceso en los controladores

Después de adquirir un token de acceso para el back-end de `TaskService` y almacenarlo en la caché de tokens de ADAL, debe utilizarlo `TasksController` es responsable de comunicarse con la API de `TaskService` y envía solicitudes HTTP a la API para leer, crear y eliminar tareas. Antes de enviar la solicitud HTTP, obtenga un token de acceso de AAL:

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

		// Here you ask for a token by using the web app's clientId as the scope, because the web app and service share the same clientId.
		// AcquireTokenSilentAsync will return a token from the token cache and throw an exception if it cannot do so.
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

AAL almacena en caché los tokens, los actualiza cuando caducan y le indica mediante excepciones cuándo el usuario debe iniciar sesión de nuevo. Todo lo que tiene que hacer es llamar a `AuthenticationContext.AcquireTokenSilentAsync(...)` cada vez que necesite un token en la aplicación.

### Leer tareas de la API web

Cuando tenga un token, puede adjuntarlo a la solicitud `GET` HTTP en el encabezado `Authorization` para llamar de forma segura a `TaskService`:

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
			// and show the user an error that indicates that they might need to sign in again.
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

### Creación y eliminación de tareas de la API web

Siga el mismo patrón cuando envíe solicitudes `POST` y `DELETE` a `TaskService`. Llame a `AuthenticationContext.AcquireTokenSilentAsync(...)` y adjunte el token resultante a la solicitud en el encabezado `Authorization`. La acción de creación se implementa automáticamente. Puede intentar finalizar la acción de eliminación en `TasksController.cs`.

## Cierre de la sesión del usuario

Cuando el usuario cierre la sesión de la aplicación web, querrá borrar la caché de tokens de ADAL a fin de quitar los remanentes de la sesión autenticada del usuario:

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

## Ejecutar la aplicación de ejemplo

Por último, compile y ejecute `TaskClient` y `TaskService`. Regístrese e inicie sesión en la aplicación. Cree tareas para el usuario que inició sesión. Cierre la sesión y iníciela con otro usuario diferente. Cree tareas para ese usuario. Observe cómo se almacenan las tareas por usuario en la API, puesto que la API extrae la identidad del usuario del token de acceso que recibe.

Como referencia, el ejemplo completo [se proporciona como un archivo .zip](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/complete.zip). También puede clonarlo desde GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet.git```

<!--

## Next steps

You can now move on to more advanced B2C topics. You might try:

[Call a web API from a web app]()

[Customize the UX for a B2C app]()

-->

<!---HONumber=AcomDC_0302_2016-->