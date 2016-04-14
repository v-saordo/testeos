<properties
	pageTitle="Vista previa de Azure AD B2C | Microsoft Azure"
	description="Creación de una API web de .NET con Azure Active Directory B2C, protegida con tokens de acceso de OAuth 2.0 para autenticación."
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
	ms.topic="hero-article"
	ms.date="12/22/2015"
	ms.author="dastrock"/>

# Versión preliminar de Azure Active Directory B2C: compilación de una API web de .NET

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Con Azure Active Directory (Azure AD) B2C, puede proteger una API web mediante el uso de tokens de acceso de OAuth 2.0. Estos tokens permiten a las aplicaciones cliente que usan Azure AD B2C autenticarse en la API. Este artículo le muestra cómo crear una aplicación "de tareas pendientes" de controlador de vista de modelos de .NET que incluya el registro, el inicio de sesión y la administración de perfiles de usuarios. La lista de tareas pendientes de cada usuario se almacenará por un servicio de la tarea. Se trata de una API web que permite a los usuarios autenticados crear y leer tareas en sus listas de tareas pendientes.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Creación de un directorio de Azure AD B2C

Para poder usar Azure AD B2C, debe crear un directorio o inquilino. Un directorio es un contenedor para todos los usuarios, las aplicaciones, los grupos, etc. Si aún no tiene uno, [cree un directorio B2C](active-directory-b2c-get-started.md) antes de continuar con esta guía.

## Creación de una aplicación

A continuación, debe crear una aplicación en su directorio B2C. Esto proporciona a Azure AD la información que necesita para comunicarse de forma segura con la aplicación. Para crear una aplicación, siga [estas instrucciones](active-directory-b2c-app-registration.md). Asegúrese de:

- Incluir una **aplicación web** o **API web** en la aplicación.
- Utilizar el **identificador uniforme de recursos de redirección** `https://localhost:44316/` para la aplicación web. Se trata de la ubicación predeterminada del cliente de aplicación web de este ejemplo de código.
- Copiar el **Id. de aplicación** asignado a la aplicación. Lo necesitará más adelante.

 [AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## Crear sus directivas

En Azure AD B2C, cada experiencia del usuario se define mediante una [directiva](active-directory-b2c-reference-policies.md). El cliente de este ejemplo de código contiene tres experiencias de identidad: registro, inicio de sesión y edición de perfil. Tendrá que crear una directiva de cada tipo, como se describe en el [artículo de referencia de las directivas](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Cuando cree las tres directivas, asegúrese de:

- Elegir **Registro de id. de usuario** o **Registro de correo electrónico** en la hoja de proveedores de identidades.
- Seleccionar **Nombre para mostrar** y otros atributos de registro en la directiva de registro.
- Elegir las notificaciones **Nombre para mostrar** e **Id. de objeto** como notificaciones de aplicación en todas las directivas. Puede elegir también otras notificaciones.
- Copiar el **Nombre** de cada directiva después de crearla. Necesitará estos nombres de directiva más adelante.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Cuando haya creado correctamente las tres directivas, estará listo para crear su aplicación.

## Descargar el código

El código de este tutorial [se mantiene en GitHub](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet). Para generar el ejemplo a medida que avance, puede [descargar un proyecto de esqueleto como archivo .zip](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet/archive/skeleton.zip). También puede clonar el esqueleto:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet.git
```

La aplicación completada también estará [disponible como archivo .zip](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet/archive/complete.zip) o en la rama `complete` del mismo repositorio.

Una vez descargado el código de ejemplo, abra el archivo .sln de Visual Studio para empezar. El archivo de solución contiene dos proyectos: `TaskWebApp` y `TaskService`. `TaskWebApp` es una aplicación web MVC con la que el usuario interactúa. `TaskService` es la API web del back-end de la aplicación que almacena la lista de tareas pendientes de cada usuario.

## Configurar la aplicación web de tarea

Cuando el usuario interactúa con `TaskWebApp`, el cliente envía solicitudes a Azure AD y recibe tokens que se pueden usar para llamar a la API web `TaskService`. Para iniciar la sesión del usuario y obtener tokens, debe proporcionar a `TaskWebApp` algo de información sobre la aplicación. En el proyecto `TaskWebApp`, abra el archivo `web.config` en la raíz del proyecto y reemplace los valores de la sección `<appSettings>`:

```
<appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:Tenant" value="{Enter the name of your B2C directory, e.g. contoso.onmicrosoft.com}" />
    <add key="ida:ClientId" value="{Enter the Application ID assigned to your app by the Azure Portal, e.g.580e250c-8f26-49d0-bee8-1c078add1609}" />
    <add key="ida:ClientSecret" value="{Enter the Application Secret you created in the Azure Portal, e.g. yGNYWwypRS4Sj1oYXd0443n}" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}{1}{2}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="[Enter your sign up policy name, e.g. b2c_1_sign_up]" />
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

Para obtener información sobre cómo una aplicación web como `TaskWebApp` utiliza Azure AD B2C, consulte [Crear una aplicación web de .NET](active-directory-b2c-devquickstarts-web-dotnet.md).

## Proteger la API

Cuando tenga un cliente que llame a la API en nombre de los usuarios, puede proteger `TaskService` con tokens de portador de OAuth 2.0. La API puede aceptar y validar tokens mediante la biblioteca de Open Web Interface for .NET (OWIN) de Microsoft.

### Instalar OWIN
Empiece instalando la canalización de autenticación de OAuth de OWIN:

```
PM> Install-Package Microsoft.Owin.Security.OAuth -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Security.Jwt -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService
```

### Escribir los detalles de B2C
Abra el archivo `web.config` en la raíz del proyecto `TaskService` y reemplace los valores de la sección `<appSettings>`. Estos valores se usarán en toda la API y la biblioteca de OWIN.

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

### Agregar una clase de inicio de OWIN
Agregue una clase de inicio de OWIN al proyecto `TaskService` llamado `Startup.cs`. Haga clic con el botón derecho en el proyecto, seleccione **Agregar** y **Nuevo elemento**; a continuación, busque OWIN.


```C#
// Startup.cs

// Change the class declaration to "public partial class Startup" - we’ve already implemented part of this class for you in another file.
public partial class Startup
{
	// The OWIN middleware will invoke this method when the app starts
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

### Configurar la autenticación de OAuth 2.0
Abra el archivo `App_Start\Startup.Auth.cs` e implemente el método `ConfigureAuth(...)`.

```C#
// App_Start\Startup.Auth.cs

public partial class Startup
{
	// These values are pulled from web.config
	public static string aadInstance = ConfigurationManager.AppSettings["ida:AadInstance"];
	public static string tenant = ConfigurationManager.AppSettings["ida:Tenant"];
	public static string clientId = ConfigurationManager.AppSettings["ida:ClientId"];
	public static string commonPolicy = ConfigurationManager.AppSettings["ida:PolicyId"];
	private const string discoverySuffix = ".well-known/openid-configuration";

	public void ConfigureAuth(IAppBuilder app)
	{   
		TokenValidationParameters tvps = new TokenValidationParameters
		{
			// This is where you specify that your API accepts tokens only from its own clients
			ValidAudience = clientId,
		};

		app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
		{   
			// This SecurityTokenProvider fetches the Azure AD B2C metadata and signing keys from the OpenID Connect metadata endpoint
			AccessTokenFormat = new JwtFormat(tvps, new OpenIdConnectCachingSecurityTokenProvider(String.Format(aadInstance, tenant, "v2.0", discoverySuffix, commonPolicy)))
		});
	}
}
```

### Proteger el controlador de la tarea
Cuando la aplicación esté configurada para utilizar la autenticación de OAuth 2.0, puede proteger su API web agregando una etiqueta `[Authorize]` al controlador de tareas. Este es el controlador donde tiene lugar cualquier manipulación de la lista de tareas pendientes, por lo que debe proteger todo el controlador en el nivel de clase. También puede agregar la etiqueta `[Authorize]` a determinadas acciones para un control específico.

```C#
// Controllers\TasksController.cs

[Authorize]
public class TasksController : ApiController
{
	...
}
```

### Obtener la información del usuario del token
`TasksController` almacena las tareas en una base de datos donde cada tarea tiene un usuario asociado que la "posee". El propietario se identifica por el **Id. de objeto** del usuario (por eso necesita agregar el Id. de objeto como una notificación de aplicación en todas sus directivas).

```C#
// Controllers\TasksController.cs

public IEnumerable<Models.Task> Get()
{
	string owner = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
	IEnumerable<Models.Task> userTasks = db.Tasks.Where(t => t.owner == owner);
	return userTasks;
}
```

## Ejecutar la aplicación de ejemplo

Por último, compile y ejecute `TaskWebApp` y `TaskService`. Regístrese en la aplicación con una dirección de correo electrónico o un nombre de usuario. Cree algunas tareas en la lista de tareas del usuario y observe cómo se conservan en la API incluso después de detener y reiniciar el cliente.

## Editar sus directivas

Una vez que haya protegido una API mediante el uso de Azure AD B2C, puede experimentar con las directivas de la aplicación y ver los efectos (o la ausencia de estos) en la API. Puede <!--add **identity providers** to the policies, allowing you users to sign into the Task Client using social accounts.  You can also -->manipular las notificaciones de aplicación de las directivas y modificar la información de usuario que está disponible en la API web. Las notificaciones que agregue estarán disponibles para la API web de MVC de .NET en el objeto `ClaimsPrincipal`, como se describió anteriormente en este artículo.

<!--

## Next steps

You can now move onto more advanced B2C topics. You may try:

[Call a web API from a web app]()

[Customize the UX of your B2C app]()

-->

<!---HONumber=AcomDC_0302_2016-->