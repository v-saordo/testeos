<properties
   pageTitle="Autenticación en aplicaciones multiinquilino | Microsoft Azure"
   description="Cómo una aplicación multiinquilino puede autenticar los usuarios desde Azure AD"
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="mwasson"/>

# Autenticación en aplicaciones multiinquilino

Este artículo [forma parte de una serie](guidance-multitenant-identity.md). También hay una [aplicación de ejemplo] completa que acompaña a esta serie.

En este artículo, se describe cómo una aplicación multiinquilino puede autenticar usuarios de Azure Active Directory (Azure AD) usando OpenID Connect (OIDC) para ese fin.

## Información general

Nuestra [implementación de referencia](guidance-multitenant-identity-tailspin.md) es una aplicación ASP.NET Core 1.0. La aplicación usa el software intermedio OpenID Connect integrado para realizar el flujo de autenticación OIDC. En el diagrama siguiente, se muestra lo que ocurre cuando el usuario inicia sesión, a un alto nivel.

![Flujo de autenticación](media/guidance-multitenant-identity/auth-flow.png)

1.	El usuario hace clic en el botón de inicio de sesión en la aplicación. Esta acción se controla mediante un controlador MVC.
2.	El controlador MVC devuelve una acción **ChallengeResult**.
3.	El software intermedio intercepta la acción **ChallengeResult** y crea una respuesta 302, que redirige al usuario a la página de inicio de sesión de Azure AD.
4.	El usuario se autentica con Azure AD.
5.	Azure AD envía un token de identificador a la aplicación.
6.	El software intermedio valida el token de identificador. En este punto, el usuario está ya autenticado dentro de la aplicación.
7.	El software intermedio redirige al usuario a la aplicación.

## Registro de la aplicación con Azure AD

Para habilitar OpenID Connect, el proveedor de SaaS registra la aplicación dentro de su propio inquilino de Azure AD.

Para registrar la aplicación, siga los pasos descritos en [Integración de aplicaciones con Azure Active Directory](../active-directory/active-directory-integrating-applications.md), en la sección "Para registrar una aplicación nueva en el Portal de Azure clásico".

En la página **Configurar**:

-	anote el identificador de cliente.
-	En **La aplicación es multiinquilino**, seleccione **Sí**.
-	Establezca **URL de respuesta** en una dirección URL a la que Azure AD enviará la respuesta de autenticación. Puede usar la dirección URL base de la aplicación.
  -	Nota: La ruta de acceso de la dirección URL puede ser cualquiera, siempre que el nombre de host coincida con la aplicación implementada.
  -	Puede establecer varias direcciones URL de respuesta. Durante el desarrollo, puede usar una dirección `localhost` para ejecutar la aplicación localmente.
-	Genere un secreto de cliente: en **Claves**, haga clic en la lista desplegable **Seleccionar duración** y elija 1 o 2 años. La clave será visible cuando se haga clic en **Guardar**. Asegúrese de copiar el valor porque no se muestra de nuevo cuando se vuelva a cargar la página de configuración.

## Configuración del software intermedio de autenticación

En esta sección, se describe cómo configurar el software intermedio de autenticación en ASP.NET Core 1.0 para la autenticación multiinquilino con OpenID Connect.

En la clase de inicio, agregue el software intermedio OpenID Connect:

```csharp
app.UseOpenIdConnectAuthentication(options =>
{
    options.AutomaticAuthenticate = true;
    options.AutomaticChallenge = true;
    options.ClientId = [client ID];
    options.Authority = "https://login.microsoftonline.com/common/";
    options.CallbackPath = [callback path];
    options.PostLogoutRedirectUri = [application URI];
    options.SignInScheme = CookieAuthenticationDefaults.AuthenticationScheme;
    options.TokenValidationParameters = new TokenValidationParameters
    {
        ValidateIssuer = false
    };
    options.Events = [event callbacks];
});
```

> [AZURE.NOTE] Consulte [Startup.cs](https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Web/Startup.cs).

Para más información acerca de la clase de inicio, consulte [Application Startup](https://docs.asp.net/en/latest/fundamentals/startup.html) (Inicio de la aplicación) en la documentación de ASP.NET Core 1.0.

Establezca las opciones de software intermedio siguientes:

- **ClientId**. El identificador de cliente de la aplicación, que obtuvo al registrar la aplicación en Azure AD.
- **Authority**. Para una aplicación multiinquilino, establezca esta propiedad en `https://login.microsoftonline.com/common/`. Se trata de la dirección URL para el punto de conexión común de Azure AD, que permite a los usuarios de cualquier inquilino de Azure AD iniciar sesión. Para más información sobre el punto de conexión común, consulte [esta entrada de blog](http://www.cloudidentity.com/blog/2014/08/26/the-common-endpoint-walks-like-a-tenant-talks-like-a-tenant-but-is-not-a-tenant/).
- En **TokenValidationParameters**, establezca **ValidateIssuer** en false. Esto significa que la aplicación se encargará de validar el valor del emisor en el token de identificador. (El software intermedio sigue validando el token). Para más información acerca de la validación del emisor, consulte [Validación del emisor](guidance-multitenant-identity-claims.md#issuer-validation).
- **CallbackPath**. Establézcalo en la ruta de acceso de la dirección URL de respuesta registrada en Azure AD. Por ejemplo, si la dirección URL de respuesta es `http://contoso.com/aadsignin`, **CallbackPath** debe ser `aadsignin`. Si no establece esta opción, el valor predeterminado es `signin-oidc`.
- **PostLogoutRedirectUri**. Especifique una dirección URL a la que redirigir a los usuarios tras el cierre de sesión. Debe tratarse de una página que permita solicitudes anónimas, normalmente la página principal.
- **SignInScheme**. Establezca esta opción en `CookieAuthenticationDefaults.AuthenticationScheme`. Esta configuración significa que, después de autenticarse el usuario, las notificaciones de usuario se almacenan localmente en una cookie. Esta cookie es la forma en que la sesión del usuario permanece iniciada durante la sesión del explorador.
- **Eventos.** Devoluciones de llamada de evento; consulte [Eventos de autenticación](#authentication-events).

Además, agregue el software intermedio de autenticación de cookies a la canalización. Este software intermedio se encarga de escribir las notificaciones del usuario en una cookie y después de leerla durante las cargas de página posteriores.

```csharp
app.UseCookieAuthentication(options =>
{
    options.AutomaticAuthenticate = true;
    options.AutomaticChallenge = true;
    options.AccessDeniedPath = "/Home/Forbidden";
});
```

## Inicio del flujo de autenticación

Para iniciar el flujo de autenticación en el MVC de ASP.NET, devuelva una acción **ChallengeResult** desde el controlador:

```csharp
[AllowAnonymous]
public IActionResult SignIn()
{
    return new ChallengeResult(
        OpenIdConnectDefaults.AuthenticationScheme,
        new AuthenticationProperties
        {
            IsPersistent = true,
            RedirectUri = Url.Action("SignInCallback", "Account")
        });
}
```

Esto hace que el software intermedio devuelva una respuesta 302 (encontrado) que redirige al punto de conexión de autenticación.

## Sesión de inicio de sesión de usuario

Como se ha mencionado, cuando el usuario inicia sesión por primera vez, el software intermedio de autenticación de cookies escribe las notificaciones del usuario en una cookie. Después, se autentican las solicitudes HTTP al leer la cookie.

De forma predeterminada, el software intermedio de cookies escribe una [cookie de sesión][session-cookie], que se elimina cuando el usuario cierra el explorador. La próxima vez que el usuario visita el sitio, tendrá que iniciar sesión de nuevo. Sin embargo, si establece **IsPersistent** en true en la acción **ChallengeResult**, el software intermedio escribe una cookie persistente, por lo que la sesión del usuario permanece iniciada aunque se cierre el explorador. Puede configurar la expiración de la cookie; consulte [Controlling cookie options][cookie-options] (Control de opciones de cookies). Las cookies persistentes son más cómodas para el usuario, pero pueden ser inadecuadas para algunas aplicaciones (por ejemplo, una aplicación de banca) en las que el usuario desea iniciar sesión cada vez.

## Acerca del software intermedio OpenID Connect

El software intermedio OpenID Connect en ASP.NET oculta la mayoría de los detalles del protocolo. Esta sección contiene algunas notas sobre la implementación que pueden ser útiles para comprender el flujo del protocolo.

En primer lugar, vamos a examinar el flujo de autenticación en términos de ASP.NET (omitiendo los detalles del flujo de protocolo OIDC entre la aplicación y Azure AD). En el siguiente diagrama se muestra el proceso.

![Flujo de inicio de sesión](media/guidance-multitenant-identity/sign-in-flow.png)

En este diagrama, hay dos controladores MVC. El controlador de cuentas controla las solicitudes de inicio de sesión y el controlador principal sirve la página principal.

Este es el proceso de autenticación:

1. El usuario hace clic en el botón de inicio de sesión y el explorador envía una solicitud GET. Por ejemplo: `GET /Account/SignIn/`.
2. El controlador de cuentas devuelve una acción `ChallengeResult`.
3. El software intermedio OIDC devuelve una respuesta HTTP 302 y redirige al usuario a Azure AD.
4. El explorador envía la solicitud de autenticación a Azure AD.
5. El usuario inicia sesión en Azure AD, desde donde se devuelve una respuesta de autenticación.
6. El software intermedio OIDC crea una entidad de seguridad de notificaciones y la pasa al software intermedio de autenticación de cookies.
7. El software intermedio de cookies serializa la entidad de seguridad de notificaciones y establece una cookie.
8. El software intermedio OIDC redirige a la dirección URL de devolución de llamada de la aplicación.
10. El explorador sigue a la redirección y envía la cookie en la solicitud.
11. El software intermedio de cookies deserializa la cookie en una entidad de seguridad de notificaciones y establece `HttpContext.User` en dicha entidad. La solicitud se enruta a un controlador MVC.

### Vale de autenticación

Si la autenticación se realiza correctamente, el software intermedio OIDC crea un vale de autenticación, que contiene una entidad de seguridad de notificaciones con las notificaciones del usuario. Puede acceder al vale dentro de los eventos **AuthenticationValidated** o **TicketReceived**.

> [AZURE.NOTE] Hasta que se complete todo el flujo de autenticación, `HttpContext.User` sigue conteniendo una entidad de seguridad anónima y _no_ el usuario autenticado. La entidad de seguridad anónima tiene una colección vacía de notificaciones. Una vez que la autenticación se completa y la aplicación realiza la redirección, el software intermedio de cookies deserializa la cookie de autenticación y establece `HttpContext.User` en una entidad de seguridad de notificaciones que representa al usuario autenticado.

### Eventos de autenticación

Durante el proceso de autenticación, el software intermedio OpenID Connect genera una serie de eventos:

- **RedirectToAuthenticationEndpoint**. Se llama justo antes de que el software intermedio redirija al punto de conexión de autenticación. Puede usar este evento para modificar la dirección URL de redireccionamiento; por ejemplo, para agregar parámetros de solicitud. Consulte [Incorporación de la petición de consentimiento del administrador](guidance-multitenant-identity-signup.md#adding-the-admin-consent-prompt) para ver un ejemplo.

- **AuthorizationResponseReceived**. Se llama después de que el software intermedio reciba la respuesta de autenticación del proveedor de identidades (IDP), pero antes de que valide la respuesta.

- **AuthorizationCodeReceived**. Se llama con el código de autorización.

- **TokenResponseReceived**. Se llama después de que el software intermedio obtiene un token de acceso desde el IDP. Se aplica solo al flujo de código de autorización.

- **AuthenticationValidated**. Se llama después de que el software intermedio valide el token de identificador. En este punto, la aplicación tiene un conjunto de notificaciones validadas sobre el usuario. Puede usar este evento para realizar una validación adicional de las notificaciones o para transformarlas. Consulte [Working with claims-based identities in multitenant applications](guidance-multitenant-identity-claims.md) (Trabajo con identidades basadas en notificaciones en aplicaciones multiinquilino)

- **UserInformationReceived**. Se llama si el software intermedio obtiene el perfil de usuario desde el punto de conexión de información de usuario. Solo se aplica al flujo de código de autorización y solamente con `GetClaimsFromUserInfoEndpoint = true` en las opciones del software intermedio.

- **TicketReceived**. Se llama cuando se completa la autenticación. Se trata del último evento, siempre que la autenticación se realice correctamente. Una vez que se controla este evento, el usuario inicia sesión en la aplicación.

- **AuthenticationFailed**. Se llama si se produce un error de autenticación. Use este evento para controlar los errores de autenticación; por ejemplo, redirigiendo a una página de error.

Para proporcionar devoluciones de llamada para estos eventos, establezca la opción **Events** en el software intermedio. Hay dos maneras diferentes de declarar los controladores de eventos: insertados con expresiones lambda o en una clase que deriva de **OpenIdConnectEvents**.

Insertados con expresiones lambda:

```csharp
app.UseOpenIdConnectAuthentication(options =>
{
    // Other options not shown.

    options.Events = new OpenIdConnectEvents
    {
        OnTicketReceived = (context) =>
        {
             // Handle event
             return Task.FromResult(0);
        },
        // other events
    }
});
```

Con derivación de **OpenIdConnectEvents**:

```csharp
public class SurveyAuthenticationEvents : OpenIdConnectEvents
{
    public override Task TicketReceived(TicketReceivedContext context)
    {
        // Handle event
        return base.TicketReceived(context);
    }
    // other events
}

// In Startup.cs:
app.UseOpenIdConnectAuthentication(options =>
{
    // Other options not shown.

    options.Events = new SurveyAuthenticationEvents();
});
```

Se recomienda el segundo enfoque si sus devoluciones de llamada de evento tienen lógica sustancial, para que no sobrecarguen la clase de inicio. Nuestra implementación de referencia usa este enfoque; consulte [SurveyAuthenticationEvents.cs](https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Web/Security/SurveyAuthenticationEvents.cs).

### Puntos de conexión de OpenID Connect

Azure AD admite la [detección de OpenID Connect](https://openid.net/specs/openid-connect-discovery-1_0.html), en la que el proveedor de identidades (IDP) devuelve un documento de metadatos JSON desde un [punto de conexión conocido](https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderConfig). El documento de metadatos contiene información como:

-	La dirección URL del punto de conexión de autorización. Aquí es a donde redirige la aplicación para autenticar al usuario.
-	La dirección URL del punto de conexión de fin de sesión, a donde la aplicación va para cerrar la sesión del usuario.
-	La dirección URL para obtener las claves de firma, que el cliente usa para validar los tokens de OIDC que obtiene del IDP.

De forma predeterminada, el software intermedio OIDC sabe cómo capturar estos metadatos. Establezca la opción **Authority** en el software intermedio, el cual construye la dirección URL para los metadatos. (La dirección URL de metadatos se puede invalidar estableciendo la opción **MetadataAddress**).

### Flujos de OpenID Connect

De forma predeterminada, el software intermedio OIDC usa un flujo híbrido con el modo de respuesta de envío de formulario.

-	_Flujo híbrido_ significa que el cliente puede obtener un token de identificador y un código de autorización en el mismo recorrido de ida y vuelta al servidor de autorización.
-	_Modo de respuesta de envío de formulario_ significa que el servidor de autorización usa una solicitud POST HTTP para enviar el token de identificador y el código autorización a la aplicación. Los valores tienen el formato form-urlencoded (content type = "application/x-www-form-urlencoded").

Cuando el software intermedio OIDC redirige al punto de conexión de autorización, la dirección URL de redireccionamiento incluye todos los parámetros de cadena de consulta necesarios para OIDC. Para el flujo híbrido:

-	client\_id. Este valor se establece en la opción **ClientId**.
-	scope = "openid profile", lo que significa que es una solicitud de OIDC y que se busca el perfil del usuario.
-	response\_type = "code id\_token". Esto especifica el flujo híbrido.
-	response\_mode = "form\_post". Esto especifica la respuesta de envío de formulario.

Para especificar un flujo diferente, establezca la propiedad **ResponseType** en las opciones. Por ejemplo:

```csharp
app.UseOpenIdConnectAuthentication(options =>
{
    options.ResponseType = "code"; // Authorization code flow

    // Other options
}
```

[cookie-options]: https://docs.asp.net/en/latest/security/authentication/cookie.html#controlling-cookie-options
[session-cookie]: https://en.wikipedia.org/wiki/HTTP_cookie#Session_cookie
[aplicación de ejemplo]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->