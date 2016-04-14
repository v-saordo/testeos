<properties
	pageTitle="Versión preliminar de Azure Active Directory B2C | Microsoft Azure"
	description="Creación de una aplicación web que tiene registro, inicio de sesión y administración de perfiles mediante Azure Active Directory B2C."
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
	ms.date="02/19/2016"
	ms.author="dastrock"/>

# Versión preliminar de Azure AD B2C: creación de una aplicación web de .NET

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Con Azure Active Directory (Azure AD) B2C, puede agregar eficaces características de administración de identidades de autoservicio a su aplicación web en unos cuantos pasos. En este artículo se muestra cómo crear una aplicación web de controlador de vista de modelos de .NET que incluya el registro, el inicio de sesión y la administración de perfiles de usuarios. La aplicación incluirá compatibilidad para el registro y el inicio de sesión mediante un nombre de usuario o correo electrónico, y mediante el uso de cuentas sociales como Facebook y Google.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Obtener un directorio de Azure AD B2C

Para poder usar Azure AD B2C, debe crear un directorio o inquilino. Un directorio es un contenedor para todos los usuarios, las aplicaciones, los grupos, etc. Si aún no tiene uno, [cree un directorio B2C](active-directory-b2c-get-started.md) antes de continuar con esta guía.

## Creación de una aplicación

A continuación, debe crear una aplicación en su directorio B2C. Esto proporciona a Azure AD la información que necesita para comunicarse de forma segura con la aplicación. Para crear una aplicación, siga [estas instrucciones](active-directory-b2c-app-registration.md). Asegúrese de:

- Incluir una **aplicación web/API web** en la aplicación.
- Escribir `https://localhost:44316/` como **Dirección URL de respuesta**. Es la dirección URL predeterminada para este ejemplo de código.
- Escribir el **Id. de aplicación** asignado a la aplicación. Lo necesitará más adelante.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## Crear sus directivas

En Azure AD B2C, cada experiencia del usuario se define mediante una [directiva](active-directory-b2c-reference-policies.md). Este ejemplo de código contiene tres experiencias de identidad: registro, inicio de sesión y edición de perfil. Debe crear una directiva de cada tipo, como se describe en el [artículo de referencia de las directivas](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Cuando cree las tres directivas, asegúrese de:

- Elegir **Registro de id. de usuario** o **Registro de correo electrónico** en la hoja de proveedores de identidades.
- Seleccionar **Nombre para mostrar** y algunos otros atributos de registro en la directiva de registro.
- Elegir la notificación de **Nombre para mostrar** como una notificación de aplicación en cada directiva. Puede elegir también otras notificaciones.
- Copiar el **Nombre** de cada directiva después de crearla. Necesitará esos nombres de directiva más adelante.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Después de crear las tres directivas, está listo para compilar la aplicación.

## Descargar el código y configurar la autenticación

El código de este ejemplo [se conserva en GitHub](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet). Para generar el ejemplo a medida que avance, puede [descargar un proyecto de esqueleto como un archivo .zip](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet/archive/skeleton.zip). También puede clonar el esqueleto:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet.git
```

El ejemplo completado también estará [disponible como archivo .zip](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet/archive/complete.zip) o en la rama `complete` del mismo repositorio.

Una vez descargado el código de ejemplo, abra el archivo .sln de Visual Studio para empezar.

Su aplicación se comunica con Azure AD B2C mediante el envío de solicitudes de autenticación HTTP que especifican la directiva que desea ejecutar como parte de la solicitud. En el caso de las aplicaciones web. NET, puede usar la biblioteca OWIN de Microsoft para enviar solicitudes de autenticación de OpenID Connect, ejecutar directivas, administrar sesiones de usuario, etc.

Para comenzar, agregue los paquetes de NuGet de middleware OWIN al proyecto mediante la Consola del Administrador de paquetes de Visual Studio.

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
PM> Install-Package Microsoft.Owin.Security.Cookies
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

Luego, abra el archivo `web.config` en la raíz del proyecto y escriba los valores de configuración de su aplicación en la sección `<appSettings>`.

```
<configuration>
  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:Tenant" value="[Enter the name of your B2C directory, e.g. contoso.onmicrosoft.com]" />
    <add key="ida:ClientId" value="[Enter the Application Id assigned to your app by the Azure portal, e.g.580e250c-8f26-49d0-bee8-1c078add1609]" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}{1}{2}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="[Enter your sign up policy name, e.g. b2c_1_sign_up]" />
    <add key="ida:SignInPolicyId" value="[Enter your sign in policy name, e.g. b2c_1_sign_in]" />
    <add key="ida:UserProfilePolicyId" value="[Enter your edit profile policy name, e.g. b2c_1_profile_edit]" />
  </appSettings>
...
```

[AZURE.INCLUDE [active-directory-b2c-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Ahora, agregue una clase de inicio de OWIN al proyecto llamado `Startup.cs`. Haga clic con el botón derecho en el proyecto, seleccione **Agregar** y **Nuevo elemento** y, a continuación, busque "OWIN". Cambie la declaración de clase a `public partial class Startup`. Hemos implementado parte de esta clase automáticamente en otro archivo. El middleware OWIN invocará el método `Configuration(...)` al iniciarse la aplicación. En este método, realice una llamada a `ConfigureAuth(...)`, donde se configura la autenticación para la aplicación.

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

Abra el archivo `App_Start\Startup.Auth.cs` e implemente el método `ConfigureAuth(...)`. Los parámetros que proporciona en `OpenIdConnectAuthenticationOptions` sirven como coordenadas para que su aplicación se comunique con Azure AD. También debe configurar la autenticación de cookies. El middleware OpenID Connect usa cookies para mantener las sesiones de usuario, entre otras cosas.

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
            // endpoints from the OpenID Connect metadata endpoint. It is included in the PolicyAuthHelpers folder.
            // The first parameter is the metadata URL of your B2C directory.
            // The second parameter is an array of the policies that your app will use.
            ConfigurationManager = new PolicyConfigurationManager(
                String.Format(CultureInfo.InvariantCulture, aadInstance, tenant, "/v2.0", OIDCMetadataSuffix),
                new string[] { SignUpPolicyId, SignInPolicyId, ProfilePolicyId }),

            // This piece is optional. It is used to display the user's name in the navigation bar.
            TokenValidationParameters = new TokenValidationParameters
            {  
                NameClaimType = "name",
            },
        };

        app.UseOpenIdConnectAuthentication(options);

    }
```

## Enviar solicitudes de autenticación a Azure AD
Ahora la aplicación está correctamente configurada para comunicarse con Azure AD B2C mediante el protocolo de autenticación OpenID Connect. OWIN se ha ocupado de todos los detalles de la creación de mensajes de autenticación, validación de tokens de Azure AD y mantenimiento de la sesión de usuario. Todo lo que queda es iniciar cada flujo de usuarios.

Cuando un usuario selecciona **Registrarse**, **Iniciar sesión** o **Editar perfil** en la aplicación web, se invoca la acción asociada en `Controllers\AccountController.cs`. En cada caso, puede usar métodos integrados de OWIN para desencadenar la directiva correcta:

```C#
// Controllers\AccountController.cs

public void SignIn()
{
    if (!Request.IsAuthenticated)
    {
        // To execute a policy, you simply need to trigger an OWIN challenge.
        // You can indicate which policy to use by adding it to the AuthenticationProperties by using the PolicyKey provided.

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

También puede utilizar una etiqueta `PolicyAuthorize` personalizada en los controladores que requiere la ejecución de una directiva determinada si el usuario no ha iniciado sesión. Abra `Controllers\HomeController.cs` y agregue la etiqueta `[PolicyAuthorize]` al controlador de notificaciones. Reemplace la directiva de ejemplo por su propia directiva de inicio de sesión.

```C#
// Controllers\HomeController.cs

// You can use the PolicyAuthorize decorator to execute a certain policy if the user is not already signed in the app.
[PolicyAuthorize(Policy = "b2c_1_sign_in")]
public ActionResult Claims()
{
  ...
```

También puede usar OWIN para cerrar la sesión del usuario de la aplicación. En `Controllers\AccountController.cs`:

```C#
// Controllers\AccountController.cs

public void SignOut()
{
    // To sign out the user, you should issue an OpenID Connect sign-out request by using the last policy that the user executed.
    // This is as easy as looking up the current value of the ACR claim, adding it to the AuthenticationProperties, and making an OWIN SignOut call.

    HttpContext.GetOwinContext().Authentication.SignOut(
        new AuthenticationProperties(
            new Dictionary<string, string>
            {
                {Startup.PolicyKey, ClaimsPrincipal.Current.FindFirst(Startup.AcrClaimType).Value}
            }), OpenIdConnectAuthenticationDefaults.AuthenticationType, CookieAuthenticationDefaults.AuthenticationType);
}
```

De forma predeterminada, OWIN no enviará las directivas especificadas en `AuthenticationProperties` a Azure AD. Sin embargo, puede editar las solicitudes que OWIN genera en la notificación `RedirectToIdentityProvider`. Use esta notificación en `App_Start\Startup.Auth.cs` para capturar el extremo correcto para cada directiva desde los metadatos de la directiva. Esto garantiza que se envíe la solicitud correcta a Azure AD para cada directiva que su aplicación quiera ejecutar.

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

## Mostrar información de usuario
Cuando se autentican los usuarios mediante OpenID Connect, Azure AD devuelve un token de identificador a la aplicación que contiene **notificaciones**. Se trata de aserciones sobre el usuario. Puede usar notificaciones para personalizar su aplicación.

Abra el archivo `Controllers\HomeController.cs`. Puede tener acceso a las notificaciones del usuario en sus controladores a través del objeto principal de seguridad `ClaimsPrincipal.Current`.

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

Puede tener acceso a cualquier notificación que recibe su aplicación de la misma manera. Tiene disponible una lista de todas las notificaciones que recibe la aplicación en la página **Notificaciones**.

## Ejecutar la aplicación de ejemplo

Por último, puede compilar y ejecutar la aplicación. Regístrese en la aplicación con una dirección de correo electrónico o un nombre de usuario. Cierre la sesión y vuelva a iniciarla como el mismo usuario. Edite el perfil de ese usuario. Cierre la sesión y regístrese como otro usuario. Observe que la información que se muestra en la pestaña **Notificaciones** se corresponde con la información configurada en las directivas.

## Agregar IDP sociales

Actualmente, la aplicación admite solo registros e inicios de sesión de usuarios mediante **cuentas locales**. Se trata de cuentas almacenadas en el directorio B2C que utilizan un nombre de usuario y una contraseña. Con Azure AD B2C, puede agregar compatibilidad para otros **proveedores de identidades** sin cambiar el código.

Para agregar proveedores de identidades sociales a su aplicación, comience siguiendo las instrucciones detalladas en estos artículos. Para cada proveedor de identidades que desee admitir, necesitará registrar una aplicación en ese sistema y obtener un identificador de cliente.

- [Configurar Facebook como una IDP](active-directory-b2c-setup-fb-app.md)
- [Configurar Google como una IDP](active-directory-b2c-setup-goog-app.md)
- [Configurar Amazon como una IDP](active-directory-b2c-setup-amzn-app.md)
- [Configurar LinkedIn como una IDP](active-directory-b2c-setup-li-app.md)

Después de agregar proveedores de identidades a su directorio B2C, tendrá que editar cada una de las tres directivas para incluir los nuevos proveedores de identidades, como se describe en el [artículo de referencia de las directivas](active-directory-b2c-reference-policies.md). Después de guardar las directivas, vuelva a ejecutar la aplicación. Debería ver los proveedores de identidades nuevos agregados como opciones de inicio de sesión y registro en cada una de sus experiencias de identidad.

Puede experimentar con las directivas y observar el efecto en la aplicación de ejemplo. Agregue o quite proveedores de identidades, manipule notificaciones de aplicación o cambie atributos de registro. Experimente para ver cómo se combinan las directivas, las solicitudes de autenticación y OWIN.

Como referencia, se proporciona el ejemplo finalizado (sin sus valores de configuración) [como un archivo .zip](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet/archive/complete.zip). También puede clonarlo desde GitHub:

```
git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet.git
```

<!--

## Next steps

You can now move on to more advanced B2C topics. You might try:

[Call a web API from a web app]()

[Customize the UX for a B2C app]()

-->

<!---HONumber=AcomDC_0302_2016-->