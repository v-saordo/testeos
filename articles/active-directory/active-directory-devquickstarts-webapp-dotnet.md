<properties
	pageTitle="Introducción a .NET de Azure AD | Microsoft Azure"
	description="Cómo crear una aplicación web de .NET MVC que se integra con Azure AD para el inicio de sesión."
	services="active-directory"
	documentationCenter=".net"
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="dastrock"/>

# Inicio y cierre de sesión de la aplicación web con Azure AD

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Azure AD facilita la externalización de la administración de identidad de su aplicación web, proporcionando un inicio y cierre de sesión únicos con solo unas pocas líneas de código. En las aplicaciones web Asp.NET, puede realizar esto con la implementación de Microsoft del middleware OWIN orientado a la comunidad incluido en .NET Framework 4.5. Aquí usaremos OWIN para: - Iniciar sesión para el usuario en la aplicación con Azure AD como proveedor de identidad. -Mostrar alguna información sobre el usuario. - Cerrar sesión para el usuario de la aplicación.

Para ello, deberá hacer lo siguiente:

1. Registrar una aplicación con Azure AD
2. Configurar la aplicación para usar la canalización de autenticación OWIN.
3. Use OWIN para emitir solicitudes de inicio y cierre de sesión a Azure AD.
4. Imprima datos sobre el usuario.

Para empezar, [descargue el esquema de la aplicación](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/skeleton.zip) o [descargue el ejemplo finalizado](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/complete.zip). También necesitará a un inquilino de Azure AD en el que registrar la aplicación. Si aún no tiene uno, [descubra cómo conseguirlo](active-directory-howto-tenant.md).

## *1. Registrar una aplicación con Azure AD*
Para habilitar su aplicación a fin de autenticar a los usuarios, primero deberá registrar una nueva aplicación en su inquilino.

- Inicie sesión en el Portal de administración de Azure.
- En el panel de navegación izquierdo, haga clic en **Active Directory**.
- Seleccione el inquilino donde desea registrar la aplicación.
- Haga clic en la pestaña **Aplicaciones** y en Agregar en el cajón inferior.
- Siga las indicaciones y cree una nueva **Aplicación web y/o API web**.
    - El **nombre** de la aplicación describirá su aplicación a los usuarios finales
    -	La **dirección URL de inicio de sesión** es la dirección URL base de su aplicación. El valor predeterminado del esquema es `https://localhost:44320/`.
    - El **URI de id. de aplicación** es un identificador único de su aplicación. La convención consiste en usar `https://<tenant-domain>/<app-name>`, p. ej. `https://contoso.onmicrosoft.com/my-first-aad-app`
- Una vez que haya completado el registro, AAD asignará a su aplicación un identificador de cliente único. Necesitará este valor en las secciones siguientes, de modo que cópielo desde la pestaña Configurar.

## *2. Configurar la aplicación para usar la canalización de autenticación OWIN*
Aquí configuraremos el middleware OWIN para usar el protocolo de autenticación OpenID Connect. OWIN se usará para emitir solicitudes de inicio y cierre de sesión, administrar la sesión del usuario y obtener información sobre el usuario, entre otras cosas.

-	Para comenzar, agregue los paquetes NuGet de middleware OWIN al proyecto mediante la Consola del Administrador de paquetes.

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
PM> Install-Package Microsoft.Owin.Security.Cookies
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

-	Agregue una Clase de inicio OWIN al proyecto denominado `Startup.cs`. Haga clic con el botón derecho en el proyecto **Agregar** --> **Nuevo elemento** --> Busque "OWIN". El middleware OWIN invocará el método `Configuration(...)` al iniciarse la aplicación.
-	Cambie la declaración de clase a `public partial class Startup` (ya hemos implementado parte de esta clase para usted en otro archivo). En el método `Configuration(...)`, realice una llamada a ConfigureAuth(...) para configurar la autenticación para la aplicación web.  

```C#
public partial class Startup
{
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

-	Abra el archivo `App_Start\Startup.Auth.cs` e implemente el método `ConfigureAuth(...)`. Los parámetros que proporciona en `OpenIDConnectAuthenticationOptions` servirán como coordenadas para que su aplicación se comunique con Azure AD. También tendrá que configurar la autenticación con cookies (el middleware OpenID Connect usa cookies debajo de las portadas).

```C#
public void ConfigureAuth(IAppBuilder app)
{
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());

    app.UseOpenIdConnectAuthentication(
        new OpenIdConnectAuthenticationOptions
        {
            ClientId = clientId,
            Authority = authority,
            PostLogoutRedirectUri = postLogoutRedirectUri,
        });
}
```

-	Por último, abra el archivo `web.config` en la raíz del proyecto y escriba sus valores de configuración en la sección `<appSettings>`.
    -	`ida:ClientId` de su aplicación es el GUID que copió desde el Portal de Azure en Paso 1.
    -	`ida:Tenant` es el nombre de su inquilino de Azure AD, p. ej., "contoso.onmicrosoft.com".
    -	Su `ida:PostLogoutRedirectUri` indica a Azure AD dónde se debe redirigir a un usuario después de completarse correctamente una solicitud de cierre de sesión.

## *3. Use OWIN para emitir solicitudes de inicio y cierre de sesión a Azure AD*
Ahora la aplicación está correctamente configurada para comunicarse con Azure AD mediante el protocolo de autenticación OpenID Connect. OWIN se ha ocupado de todos los detalles feos de la creación de mensajes de autenticación, validación de tokens de Azure AD y mantenimiento de la sesión de usuario. Lo único que queda consiste en ofrecer a sus usuarios una forma de iniciar y cerrar sesión.

- Puede usar etiquetas Autorizar en sus controladores para solicitar que el usuario inicie sesión antes de tener acceso a una página determinada. Abra `Controllers\HomeController.cs` y agregue la etiqueta `[Authorize]` al controlador Acerca de.

```C#
[Authorize]
public ActionResult About()
{
  ...
```

-	También puede usar OWIN para emitir directamente solicitudes de autenticación desde dentro de su código. Abra `Controllers\AccountController.cs`. En las acciones SignIn() y SignOut(), emita un concurso de OpenID Connect y solicitudes de cierre de sesión, respectivamente.

```C#
public void SignIn()
{
    // Send an OpenID Connect sign-in request.
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(new AuthenticationProperties { RedirectUri = "/" }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}
public void SignOut()
{
    // Send an OpenID Connect sign-out request.
    HttpContext.GetOwinContext().Authentication.SignOut(
        OpenIdConnectAuthenticationDefaults.AuthenticationType, CookieAuthenticationDefaults.AuthenticationType);
}
```

-	Ahora, abra `Views\Shared\_LoginPartial.cshtml`. Aquí mostrará al usuario los vínculos de inicio y cierre de sesión de su aplicación e imprimirá el nombre del usuario en una vista.

```HTML
@if (Request.IsAuthenticated)
{
    <text>
        <ul class="nav navbar-nav navbar-right">
            <li class="navbar-text">
                Hello, @User.Identity.Name!
            </li>
            <li>
                @Html.ActionLink("Sign out", "SignOut", "Account")
            </li>
        </ul>
    </text>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li>@Html.ActionLink("Sign in", "SignIn", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
    </ul>
}
```

## *4. Mostrar información de usuario*
Al autenticar usuarios con OpenID Connect, Azure AD devuelve id\_token a la aplicación que contiene "notificaciones" o aserciones sobre el usuario. Puede usar estas notificaciones para personalizar su aplicación:

- Abra el archivo `Controllers\HomeController.cs`. Puede tener acceso a las solicitudes del usuario en sus controladores a través del objeto principal de seguridad `ClaimsPrincipal.Current`.

```C#
public ActionResult About()
{
    ViewBag.Name = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Name).Value;
    ViewBag.ObjectId = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
    ViewBag.GivenName = ClaimsPrincipal.Current.FindFirst(ClaimTypes.GivenName).Value;
    ViewBag.Surname = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Surname).Value;
    ViewBag.UPN = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Upn).Value;

    return View();
}
```

Por último, compile y ejecute su aplicación. Si aún no lo ha hecho, ahora es el momento de crear un nuevo usuario en su inquilino con un dominio *.onmicrosoft.com. Inicie sesión con ese usuario y observe cómo se refleja la identidad del usuario en la barra de navegación superior. Cierre la sesión y vuelva a iniciarla como otro usuario en su inquilino. Si se siente especialmente ambicioso, registre y ejecute otra instancia de esta aplicación (con su propio clientId) y vea Inicio de sesión único en acción.

Como referencia, [aquí puede ver](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/complete.zip) el ejemplo finalizado (sin sus valores de configuración).

Ahora puede pasar a temas más avanzados. También puede probar lo siguiente:

[Protección de una API web con Azure AD >>](active-directory-devquickstarts-webapi-dotnet.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]

<!---HONumber=AcomDC_0128_2016-->