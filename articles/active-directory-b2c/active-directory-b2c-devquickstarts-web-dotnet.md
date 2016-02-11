<properties
	pageTitle="Vista previa de Azure AD B2C | Microsoft Azure"
	description="Cómo crear una aplicación web con inicio de sesión, registro y administración de perfiles con Azure AD B2C."
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
	ms.date="09/22/2015"
	ms.author="dastrock"/>

# Vista previa de Azure AD B2C: crear una aplicación web de .NET

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Con Azure AD B2C, puede agregar las características de administración de identidades de autoservicio eficaces a su aplicación web en unos cuantos pasos. Este artículo le mostrará cómo crear una aplicación web .NET MVC que incluye el registro de usuario, el inicio de sesión y la administración de perfiles. Incluirá compatibilidad para el registro y el inicio de sesión con un nombre de usuario o correo electrónico, así como cuentas sociales como Facebook y Google.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## 1\. Obtener un directorio de Azure AD B2C

Para poder usar Azure AD B2C, debe crear un directorio o inquilino. Un directorio es un contenedor para todos los usuarios, aplicaciones, grupos, etc. Si no tiene uno todavía, vaya a [crear un directorio de B2C](active-directory-b2c-get-started.md) antes de continuar.

## 2\. Creación de una aplicación

Ahora debe crear una aplicación en su directorio de B2C, que ofrece a Azure AD información que necesita para comunicarse de forma segura con su aplicación. Para crear una aplicación, siga [estas instrucciones](active-directory-b2c-app-registration.md). Asegúrese de

- Incluir una **aplicación web/API web** en la aplicación
- Escriba `https://localhost:44316/` como **dirección URL de respuesta**: es la dirección URL predeterminada para este ejemplo de código.
- Copie el **Id. de aplicación** asignado a la aplicación. Lo necesitará en breve.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## 3\. Crear sus directivas

En Azure AD B2C, cada experiencia del usuario se define mediante una [**directiva**](active-directory-b2c-reference-policies.md). Este ejemplo de código contiene tres experiencias de identidad: registro, inicio de sesión y editar perfil. Debe crear una directiva de cada tipo, como se describe en el [artículo de referencia de directiva](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Al crear sus tres directivas, asegúrese de:

- Elegir **Registro de id. de usuario** o **Registro de correo electrónico** en la hoja de proveedores de identidades.
- Seleccionar **Nombre para mostrar** y algunos otros atributos de registro en la directiva de registro.
- Elija la notificación de **Nombre para mostrar** como una notificación de aplicación en cada directiva. Puede elegir también otras notificaciones.
- Copie el **Nombre** de cada directiva después de crearla. Necesitará esos nombres de directivas en breve. 

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Cuando tenga tres directivas creadas correctamente, estará listo para crear su aplicación.

## 4\. Descargar el código y configurar la autenticación

El código de este ejemplo se conserva [en GitHub](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet). Para generar el ejemplo a medida que avance, puede [descargar un proyecto de esqueleto como .zip](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet/archive/skeleton.zip) o clonar el esqueleto:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet.git
```

El ejemplo completado también estará [disponible como .zip](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet/archive/complete.zip) o en la rama `complete` del mismo repositorio.

Cuando haya descargado el código de ejemplo, abra el archivo `.sln` de Visual Studio para empezar.

Su aplicación se comunica con Azure AD B2C mediante el envío de solicitudes de autenticación HTTP, especificando la directiva que desea ejecutar como parte de la solicitud. Para aplicaciones web. NET, puede usar la biblioteca OWIN de Microsoft para enviar solicitudes de autenticación de OpenID Connect, ejecutar directivas, administrar la sesión del usuario, etc.

Para comenzar, agregue los paquetes NuGet de middleware OWIN al proyecto mediante la Consola del Administrador de paquetes de Visual Studio.

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
PM> Install-Package Microsoft.Owin.Security.Cookies
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

Luego abra el archivo `web.config` en la raíz del proyecto y escriba los valores de configuración de su aplicación en la sección `<appSettings>`.

```
<configuration>
  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:Tenant" value="[Enter the name of your B2C directory, e.g. contoso.onmicrosoft.com]" /> 
    <add key="ida:ClientId" value="[Enter the Application Id assinged to your app by the Azure portal, e.g.580e250c-8f26-49d0-bee8-1c078add1609]" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}{1}{2}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="[Enter your sign up policy name, e.g. b2c_1_sign_up]" />
    <add key="ida:SignInPolicyId" value="[Enter your sign in policy name, e.g. b2c_1_sign_in]" />
    <add key="ida:UserProfilePolicyId" value="[Enter your edit profile policy name, e.g. b2c_1_profile_edit]" />
  </appSettings>
...
```

[AZURE.INCLUDE [active-directory-b2c-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Ahora, agregue una "Clase de inicio de OWIN" al proyecto llamado `Startup.cs`. Haga clic con el botón derecho en el proyecto --> **Agregar** --> **Nuevo elemento** --> Buscar "OWIN". Cambie la declaración de clase a `public partial class Startup` (ya hemos implementado parte de esta clase para usted en otro archivo). El middleware de OWIN invocará el método `Configuration(...)` cuando se inicie la aplicación; en este método, realice una llamada a ConfigureAuth(...), donde configuraremos la autenticación para la aplicación.

```C#
// Startup.cs

public partial class Startup
{
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

Abra el archivo `App_Start\Startup.Auth.cs` e implemente el método `ConfigureAuth(...)`. Los parámetros que proporciona en `OpenIdConnectAuthenticationOptions` servirán como coordenadas para que su aplicación se comunique con Azure AD. También tendrá que configurar la autenticación con cookies (el middleware OpenID Connect usa cookies para mantener la sesión del usuario, entre otras cuestiones).

```C#
// App_Start\Startup.Auth.cs

public partial class Startup
{
    // The ACR claim is used to indicate which policy was executed
    public const string AcrClaimType = "http://schemas.microsoft.com/claims/authnclassreference";
    public const string PolicyKey = "b2cpolicy";
    public const string OIDCMetadataSuffix = "/.well-known/openid-configuration";

    // App config settings
    private static string clientId = ConfigurationManager.AppSettings["ida:ClientId"];
    private static string aadInstance = ConfigurationManager.AppSettings["ida:AadInstance"];
    private static string tenant = ConfigurationManager.AppSettings["ida:Tenant"];
    private static string redirectUri = ConfigurationManager.AppSettings["ida:RedirectUri"];

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
                AuthenticationFailed = AuthenticationFailed,
                RedirectToIdentityProvider = OnRedirectToIdentityProvider,
            },
            Scope = "openid",
            ResponseType = "id_token",

            // The PolicyConfigurationManager takes care of getting the correct Azure AD authentication
            // endpoints from the OpenID Connect metadata endpoint.  It is included in the PolicyAuthHelpers folder.
            // The first parameter is the metadata URL of your B2C directory
            // The second parameter is an array of the policies that your app will use.
            ConfigurationManager = new PolicyConfigurationManager(
                String.Format(CultureInfo.InvariantCulture, aadInstance, tenant, "/v2.0", OIDCMetadataSuffix),
                new string[] { SignUpPolicyId, SignInPolicyId, ProfilePolicyId }),

            // This piece is optional - it is used for displaying the user's name in the navigation bar.
            TokenValidationParameters = new TokenValidationParameters
            {  
                NameClaimType = "name",
            },
        };

        app.UseOpenIdConnectAuthentication(options);
            
    }
```

## 5\. Enviar solicitudes de autenticación a Azure AD
Ahora la aplicación está correctamente configurada para comunicarse con Azure AD B2C mediante el protocolo de autenticación OpenID Connect. OWIN se ha ocupado de todos los detalles feos de la creación de mensajes de autenticación, validación de tokens de Azure AD y mantenimiento de la sesión de usuario. Todo lo que queda es iniciar cada flujo de usuario.

Cuando un usuario hace clic en los botones "Conectarse", "Iniciar sesión" o "Editar perfil" en la aplicación web, se invocará la acción asociada en `Controllers\AccountController.cs`. En cada caso, puede usar métodos integrados de OWIN para desencadenar la directiva correcta:

```C#
// Controllers\AccountController.cs

public void SignIn()
{
    if (!Request.IsAuthenticated)
    {
        // To execute a policy, you simply need to trigger an OWIN challenge.
        // You can indicate which policy to use by adding it to the AuthenticationProperties using the PolicyKey provided.
    
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties (
                new Dictionary<string, string> 
                { 
                    {Startup.PolicyKey, Startup.SignInPolicyId}
                })
            { 
                RedirectUri = "/", 
            }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}

public void SignUp()
{
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties(
                new Dictionary<string, string> 
                { 
                    {Startup.PolicyKey, Startup.SignUpPolicyId}
                })
            {
                RedirectUri = "/",
            }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}


public void Profile()
{
    if (Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties(
                new Dictionary<string, string> 
                { 
                    {Startup.PolicyKey, Startup.ProfilePolicyId}
                })
            {
                RedirectUri = "/",
            }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}
```

También puede usar una etiqueta `PolicyAuthorize` personalizada en los controladores para exigir que se ejecute una directiva determinada si el usuario no ha iniciado sesión todavía. Abra `Controllers\HomeController.cs` y agregue la etiqueta `[PolicyAuthorize]` al controlador de notificaciones. Asegúrese de reemplazar la directiva de ejemplo incluida con su propia directiva de inicio de sesión.

```C#
// Controllers\HomeController.cs

// You can use the PolicyAuthorize decorator to execute a certain policy if the user is not already signed into the app.
[PolicyAuthorize(Policy = "b2c_1_sign_in")]
public ActionResult Claims()
{
  ...
```

También puede usar OWIN para cerrar la sesión la sesión del usuario de la aplicación . De nuevo en `Controllers\AccountController.cs`:

```C#
// Controllers\AccountController.cs

public void SignOut()
{
    // To sign out the user, you should issue an OpenIDConnect sign out request using the last policy that the user executed.
    // This is as easy as looking up the current value of the ACR claim, adding it to the AuthenticationProperties, and making an OWIN SignOut call.

    HttpContext.GetOwinContext().Authentication.SignOut(
        new AuthenticationProperties(
            new Dictionary<string, string> 
            { 
                {Startup.PolicyKey, ClaimsPrincipal.Current.FindFirst(Startup.AcrClaimType).Value}
            }), OpenIdConnectAuthenticationDefaults.AuthenticationType, CookieAuthenticationDefaults.AuthenticationType);
}
```

De forma predeterminada, OWIN no enviará las directivas especificadas en `AuthenticationProperties` para Azure AD. Sin embargo, puede editar las solicitudes que OWIN genera en la notificación `RedirectToIdentityProvider`. Use esta notificación en `App_Start\Startup.Auth.cs` para capturar el extremo correcto para cada directiva desde los metadatos de la directiva. Esto garantizará que se envía la solicitud correcta a Azure AD para cada directiva que su aplicación quiere ejecutar.

```C#
// App_Start\Startup.Auth.cs

private async Task OnRedirectToIdentityProvider(RedirectToIdentityProviderNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
{
    PolicyConfigurationManager mgr = notification.Options.ConfigurationManager as PolicyConfigurationManager;
    if (notification.ProtocolMessage.RequestType == OpenIdConnectRequestType.LogoutRequest)
    {
        OpenIdConnectConfiguration config = await mgr.GetConfigurationByPolicyAsync(CancellationToken.None, notification.OwinContext.Authentication.AuthenticationResponseRevoke.Properties.Dictionary[Startup.PolicyKey]);
        notification.ProtocolMessage.IssuerAddress = config.EndSessionEndpoint;
    }
    else
    {
        OpenIdConnectConfiguration config = await mgr.GetConfigurationByPolicyAsync(CancellationToken.None, notification.OwinContext.Authentication.AuthenticationResponseChallenge.Properties.Dictionary[Startup.PolicyKey]);
        notification.ProtocolMessage.IssuerAddress = config.AuthorizationEndpoint;
    }
}
``` 

## 6\. Mostrar información de usuario
Al autenticar usuarios con OpenID Connect, Azure AD devuelve un id\_token a la aplicación que contiene **notificaciones** o aserciones sobre el usuario. Puede usar estas notificaciones para personalizar su aplicación.

Abra el archivo `Controllers\HomeController.cs`. Puede tener acceso a las solicitudes del usuario en sus controladores a través del objeto principal de seguridad `ClaimsPrincipal.Current`.

```C#
// Controllers\HomeController.cs

[PolicyAuthorize(Policy = "b2c_1_sign_in")]
public ActionResult Claims()
{
	Claim displayName = ClaimsPrincipal.Current.FindFirst(ClaimsPrincipal.Current.Identities.First().NameClaimType);
	ViewBag.DisplayName = displayName != null ? displayName.Value : string.Empty;
    return View();
}
```

Puede tener acceso a cualquier notificación que recibe su aplicación de la misma manera. Se ha imprimido una lista de todas las notificaciones que la aplicación recibe en la página de notificaciones para su inspección.

## 7\. Ejecutar la aplicación de ejemplo

Por último, compile y ejecute su aplicación. Regístrese para la aplicación con una dirección de correo electrónico o nombre de usuario. Cierre la sesión y vuelva a iniciarla como otro usuario. Edite el perfil de ese usuario. Cierre la sesión y regístrese con otro usuario. Observe cómo la información que se muestra en la pestaña **Notificaciones** se corresponde con la información configurada en las directivas.

## 8\. Agregar IDP sociales

Actualmente, la aplicación solo admite el registro y el inicio de sesión de usuario con lo que se denominan **cuentas locales** (cuentas almacenadas en el directorio B2C con un nombre de usuario y una contraseña). Con Azure AD B2C, puede agregar compatibilidad para otros **proveedores de identidades**, o IDP, sin cambiar el código.

Para agregar IDP sociales a su aplicación, comience siguiendo las instrucciones detalladas en uno o dos de estos artículos. Para cada IDP que quiere admitir, necesitará registrar una aplicación en su sistema y obtener un Id. de cliente.

- [Configurar Facebook como una IDP](active-directory-b2c-setup-fb-app.md)
- [Configurar Google como una IDP](active-directory-b2c-setup-goog-app.md)
- [Configurar Amazon como una IDP](active-directory-b2c-setup-amzn-app.md)
- [Configurar LinkedIn como una IDP](active-directory-b2c-setup-li-app.md) 

Cuando haya agregado los proveedores de identidades a su directorio B2C, necesitará regresar y editar cada una de las tres directivas para incluir los nuevos IDP, como se describe en el [artículo de referencia de directiva](active-directory-b2c-reference-policies.md). Cuando haya guardado las directivas, solo tiene que volver a ejecutar la aplicación. Debería ver los IDP nuevos agregados como una opción de inicio de sesión y registro en cada una de sus experiencias de identidad.

Puede experimentar con su directivas con libertad y observar el efecto en su aplicación de ejemplo: agregar o quitar IDP, manipular las notificaciones de la aplicación, cambiar los atributos de registro. Pruebe hasta que comience a comprender de qué manera se vinculan entre sí las directivas, las solicitudes de autenticación y OWIN.

Como referencia, el ejemplo finalizado (sin sus valores de configuración) [se proporciona en forma de archivo .zip aquí](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet/archive/complete.zip), aunque también puede clonarlo desde GitHub:

```
git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet.git
```

<!--

## Next Steps

You can now move onto more advanced B2C topics.  You may want to try:

[Calling a Web API from a Web App >>]()

[Customizing the your B2C App's UX >>]()

-->

<!---HONumber=Oct15_HO3-->