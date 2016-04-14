<properties
   pageTitle="Protección de una API web de back-end en una aplicación multiinquilino | Microsoft Azure"
   description="Protección de una API web de back-end"
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

# Protección de una API web de back-end en una aplicación multiinquilino

Este artículo [forma parte de una serie]. También hay una [aplicación de ejemplo] completa que acompaña a esta serie.

La aplicación [Tailspin Surveys] utiliza una API web de back-end web para administrar las operaciones CRUD en las encuestas. Por ejemplo, cuando un usuario hace clic en "My Surveys" (Mis encuestas), la aplicación web envía una solicitud HTTP a la API web:

```
GET /users/{userId}/surveys
```

La API web devuelve un objeto JSON:

```
{
  "Published":[],
  "Own":[
    {"Id":1,"Title":"Survey 1"},
    {"Id":3,"Title":"Survey 3"},
    ],
  "Contribute": [{"Id":8,"Title":"My survey"}]
}
```

La API web no permite solicitudes anónimas, por lo que la aplicación web debe autenticarse utilizando tokens de portador de OAuth 2.

> [AZURE.NOTE] Este es un escenario de servidor a servidor. La aplicación no realiza llamadas de AJAX a la API desde el explorador del cliente.

Puede basarse fundamentalmente en dos métodos:

- Identidad de usuario delegado. La aplicación web se autentica con la identidad del usuario.
- Identidad de aplicación. La aplicación web se autentica con su identificador de cliente, mediante el flujo de credenciales del cliente de OAuth2.

La aplicación Tailspin implementa la identidad de usuario delegado. Estas son la diferencias principales:

**Identidad de usuario delegado**

- El token de portador enviado a la API web contiene la identidad del usuario.
- La API web toma decisiones de autorización basadas en la identidad del usuario.
- La aplicación web necesita administrar errores 403 (Forbidden) desde la API web en caso de que el usuario no esté autorizado para realizar una acción.
- Normalmente, la aplicación web sigue tomando algunas decisiones de autorización que afectan a la IU, como el hecho de mostrar u ocultar los elementos de la IU.
- Con este planteamiento, la API web puede usarse potencialmente por clientes que no son de confianza, como una aplicación de JavaScript o una aplicación cliente nativa.

**Identidad de la aplicación**

- La API web no obtiene información sobre el usuario.
- La API web no emite ninguna autorización basándose en la identidad del usuario. Todas las decisiones de autorización son adoptadas por la aplicación web.  
- La API web no puede utilizarse por un cliente que no sea de confianza (aplicación JavaScript o aplicación cliente nativa).
- Este planteamiento puede ser un poco más fácil de implementar, porque no hay ninguna lógica de autorización en la API web.

En cualquier planteamiento, la aplicación web debe obtener un token de acceso, que es la credencial necesaria para llamar a la API web.

- En el caso de la identidad de usuario delegado, el token tiene que provenir del proveedor de identidades, que puede emitir un token en nombre del usuario.

- En el caso de las credenciales de cliente, una aplicación puede obtener el token del proveedor de identidades u hospedar su propio servidor de tokens (pero no puede escribir un servidor de tokens desde cero; utilice un marco de funcionamiento probado como [IdentityServer3]). Si se autentica con Azure AD, se recomienda encarecidamente obtener el token de acceso desde Azure AD, incluso con el flujo de credenciales de cliente.

En el resto de este artículo se supone que la aplicación se autentica con Azure AD.

![Obtención del token de acceso](media/guidance-multitenant-identity/access-token.png)

## Registro de la API web en Azure AD

Para que Azure AD emita un token de portador para la API web, necesita hacer algunos ajustes en Azure AD.

1. [Registro de la API web en Azure AD].

2. Agregue el identificador de cliente de la aplicación web al manifiesto de la aplicación de API web, en la propiedad `knownClientApplications`. Consulte [Update the application manifests] (Actualización de los manifiestos de la aplicación).

3. [Concesión de permiso a la aplicación web para llamar a la API web].

  En el Portal de administración de Azure, puede establecer dos tipos de permisos: "Permisos de aplicación" para la identidad de aplicación (flujo de credencial de cliente) o "Permisos delegados" para la identidad de usuario delegado.

  ![Permisos delegados](media/guidance-multitenant-identity/delegated-permissions.png)

## Obtención de un token de acceso

Antes de llamar a la API web, la aplicación web obtiene un token de acceso de Azure AD. En una aplicación. NET, utilice la [biblioteca de autenticación de AD Azure (ADAL) para .NET][ADAL].

En el flujo de código de autorización de OAuth 2, la aplicación intercambia un código de autorización para un token de acceso. El código siguiente usa ADAL para obtener el token de acceso. A este código se le llama durante los eventos `AuthorizationCodeReceived`.

```csharp
// The OpenID Connect middleware sends this event when it gets the authorization code.   
public override async Task AuthorizationCodeReceived(AuthorizationCodeReceivedContext context)
{
    string authorizationCode = context.ProtocolMessage.Code;
    string authority = "https://login.microsoftonline.com/" + tenantID
    string resourceID = "https://tailspin.onmicrosoft.com/surveys.webapi" // App ID URI
    ClientCredential credential = new ClientCredential(clientId, clientSecret);

    AuthenticationContext authContext = new AuthenticationContext(authority, tokenCache);
    AuthenticationResult authResult = await authContext.AcquireTokenByAuthorizationCodeAsync(
        authorizationCode, new Uri(redirectUri), credential, resourceID);

    // If successful, the token is in authResult.AccessToken
}
```

Estos son los distintos parámetros que son necesarios:

- `authority`. Se deriva del identificador del inquilino del usuario que ha iniciado sesión (no del identificador del inquilino del proveedor de SaaS).  
- `authorizationCode`. El código de autenticación que obtuvo del proveedor de identidades.
- `clientId`. El identificador de cliente de la aplicación web.
- `clientSecret`. El secreto de cliente de la aplicación web.
- `redirectUri`. El URI de redirección establecido para OpenID Connect. Es aquí donde se llama de nuevo al proveedor de identidades con el token.
- `resourceID`. El URI del identificador de aplicación de la API web que creó cuando registró la API web en Azure AD.
- `tokenCache`. Un objeto que almacena en caché los tokens de acceso. Consulte [Token caching] (Almacenamiento en caché de tokens).

Si `AcquireTokenByAuthorizationCodeAsync` se completa correctamente, ADAL almacena en caché el token. Más adelante, puede obtener el token de la memoria caché mediante una llamada a AcquireTokenSilentAsync:

```csharp
AuthenticationContext authContext = new AuthenticationContext(authority, tokenCache);
var result = await authContext.AcquireTokenSilentAsync(resourceID, credential, new UserIdentifier(userId, UserIdentifierType.UniqueId));
```

donde `userId` es el identificador de objeto del usuario, que se encuentra en la notificación `http://schemas.microsoft.com/identity/claims/objectidentifier`.

## Uso del token de acceso para llamar a la API web

Una vez que tenga el token, envíelo en el encabezado de autorización de las solicitudes HTTP a la API web.

```
Authorization: Bearer xxxxxxxxxx
```

El siguiente método de extensión de la aplicación Surveys establece el encabezado de autorización en un HTTP de solicitud, utilizando para ello la clase **HttpClient**.

```csharp
public static async Task<HttpResponseMessage> SendRequestWithBearerTokenAsync(this HttpClient httpClient, HttpMethod method, string path, object requestBody, string accessToken, CancellationToken ct)
{
    var request = new HttpRequestMessage(method, path);
    if (requestBody != null)
    {
        var json = JsonConvert.SerializeObject(requestBody, Formatting.None);
        var content = new StringContent(json, Encoding.UTF8, "application/json");
        request.Content = content;
    }

    request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
    request.Headers.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));

    var response = await httpClient.SendAsync(request, ct);
    return response;
}
```

> [AZURE.NOTE] Consulte [HttpClientExtensions.cs].

## Autenticación en la API web

La API web tiene que autenticar el token de portador. En ASP.NET Core 1.0, puede usar el paquete [Microsoft.AspNet.Authentication.JwtBearer][JwtBearer]. Este paquete proporciona software intermedio que permite a la aplicación recibir los tokens de portador de OpenID Connect.

Registre el software intermedio en la clase `Startup` de la API web.

```csharp
app.UseJwtBearerAuthentication(options =>
{
    options.Audience = "[app ID URI]";
    options.Authority = "https://login.microsoftonline.com/common/";
    options.TokenValidationParameters = new TokenValidationParameters
    {
        //Instead of validating against a fixed set of known issuers, we perform custom multi-tenant validation logic
        ValidateIssuer = false,
    };
    options.Events = new SurveysJwtBearerEvents();
});
```

> [AZURE.NOTE] Consulte [Startup.cs]

- **Audience**. Establezca este valor en la dirección URL del identificador de la aplicación para la API web, que creó al registrar la API web en Azure AD.
- **Authority**. En el caso de una aplicación multiinquilino, establezca esta propiedad en https://login.microsoftonline.com/common/``.
- **TokenValidationParameters**. En el caso de una aplicación multiinquilino, establezca **ValidateIssuer** en false. Esto significa que la aplicación validará al emisor.
- **Events** es una clase que se deriva de **JwtBearerEvents**.

### Validación del emisor

Valide el emisor del token en el evento **JwtBearerEvents.ValidatedToken**. El emisor se envía en la notificación "iss".

En la aplicación Surveys, la API web no controla el [registro de inquilinos]. Por lo tanto, solo comprueba si el emisor ya está en la base de datos de la aplicación. Si no es así, se produce una excepción, lo que provoca un error de autenticación.

```csharp
public override async Task ValidatedToken(ValidatedTokenContext context)
{
    var principal = context.AuthenticationTicket.Principal;
    var tenantManager = context.HttpContext.RequestServices.GetService<TenantManager>();
    var userManager = context.HttpContext.RequestServices.GetService<UserManager>();
    var issuerValue = principal.GetIssuerValue();
    var tenant = await tenantManager.FindByIssuerValueAsync(issuerValue);

    if (tenant == null)
    {
        // the caller was not from a trusted issuer - throw to block the authentication flow
        throw new SecurityTokenValidationException();
    }
}
```

> [AZURE.NOTE] Consulte [SurveysJwtBearerEvents.cs].

También puede utilizar el evento **ValidatedToken** para hacer la [transformación de notificaciones]. Recuerde que las notificaciones proceden directamente de Azure AD, por lo que si la aplicación web no hizo ninguna transformación, estas no se reflejarán en el token de portador que recibe la API web.

## Autorización

Para obtener una descripción general de la autorización, consulte [Authorization] (Autorización). En el caso de una API web, la principal diferencia es que el software intermedio JwtBearer controla las respuestas de autorización.

Por ejemplo, para restringir una acción de controlador a usuarios autenticados:

```csharp
[Authorize(ActiveAuthenticationSchemes = JwtBearerDefaults.AuthenticationScheme)]
```


Esto devuelve un código de estado 401 si el usuario no está autenticado.

Para restringir una acción de controlador mediante una directiva de autorización:

```csharp
[Authorize(Policy = PolicyNames.RequireSurveyCreator)]
```

Esto devuelve un código de estado 401 si el usuario no está autenticado, y 403 si el usuario está autenticado pero no autorizado. Registre la directiva en el inicio:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthorization(options =>
    {
        options.AddPolicy(PolicyNames.RequireSurveyCreator,
            policy =>
            {
                policy.AddRequirements(new SurveyCreatorRequirement());
                policy.AddAuthenticationSchemes(JwtBearerDefaults.AuthenticationScheme);
            });
    });
}
```


## Recursos adicionales

- [Escenarios de autenticación para Azure AD][auth-scenarios]

<!-- links -->
[ADAL]: https://msdn.microsoft.com/library/azure/jj573266.aspx
[auth-scenarios]: ../active-directory/active-directory-authentication-scenarios.md/#web-application-to-web-api
[JwtBearer]: https://www.nuget.org/packages/Microsoft.AspNet.Authentication.JwtBearer
[forma parte de una serie]: guidance-multitenant-identity.md
[Tailspin Surveys]: guidance-multitenant-identity-tailspin.md
[IdentityServer3]: https://github.com/IdentityServer/IdentityServer3
[Registro de la API web en Azure AD]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md#register-the-surveys-web-api
[Update the application manifests]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md#update-the-application-manifests
[Concesión de permiso a la aplicación web para llamar a la API web]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md#give-the-web-app-permissions-to-call-the-web-api
[Token caching]: guidance-multitenant-identity-token-cache.md
[HttpClientExtensions.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Common/HttpClientExtensions.cs
[Startup.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.WebAPI/Startup.cs
[registro de inquilinos]: guidance-multitenant-identity-signup.md
[SurveysJwtBearerEvents.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.WebAPI/SurveyJwtBearerEvents.cs
[transformación de notificaciones]: guidance-multitenant-identity-claims.md#claims-transformations
[Authorization]: guidance-multitenant-identity-authorize.md
[aplicación de ejemplo]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->