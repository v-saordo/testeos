<properties
	pageTitle="Aplicación web .NET de Azure AD v2.0 | Microsoft Azure"
	description="Cómo crear una aplicación web de MVC de .NET que llame a los servicios web mediante cuentas personales de Microsoft y cuentas profesionales o educativas para iniciar sesión."
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
	ms.date="02/20/2016"
	ms.author="dastrock"/>

# Llamada a una API web desde una aplicación web .NET

Con el punto de conexión v2.0 puede agregar rápidamente la autenticación a sus aplicaciones web y API web compatibles tanto con las cuentas personales de Microsoft como con las cuentas profesionales o educativas. Aquí compilaremos una aplicación web MVC que inicia la sesión de los usuarios mediante OpenID Connect, con un poco de ayuda del software intermedio OWIN de Microsoft. La aplicación web obtendrá tokens de acceso de OAuth 2.0 para una API web protegida por OAuth 2.0 que permite las tareas de creación, lectura y eliminación en la "lista de tareas pendientes" de un usuario determinado.

> [AZURE.NOTE]
	No todas las características y escenarios de Azure Active Directory son compatibles con el punto de conexión v2.0. Para determinar si debe usar el punto de conexión v2.0, lea acerca de las [limitaciones de v2.0](active-directory-v2-limitations.md).

Este tutorial se centra principalmente en el uso de ADAL para obtener y usar tokens de acceso en una aplicación web. Encontrará [aquí](active-directory-v2-flows.md#web-apps) la descripción completa. Como requisitos previos, es aconsejable que aprenda primero cómo [agregar un inicio de sesión básico a una aplicación web](active-directory-v2-devquickstarts-dotnet-web.md) o cómo [proteger correctamente una API web](active-directory-v2-devquickstarts-dotnet-api.md).

## Descarga de código de ejemplo

El código de este tutorial se conserva [en GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet). Para continuar, puede [descargar el esqueleto de la aplicación como un archivo .zip](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet/archive/skeleton.zip) o clonar el esqueleto:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet.git```

Como alternativa, puede [descargar la aplicación completada como .zip](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet/archive/complete.zip) o clonar la aplicación completada:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet.git```

## Registrar una aplicación
Crea una nueva aplicación en [apps.dev.microsoft.com](https://apps.dev.microsoft.com) o siga estos [pasos detallados](active-directory-v2-app-registration.md). Asegúrese de que:

- Anotar el **Id. de aplicación** asignado a su aplicación; lo necesitará pronto.
- Crear un **secreto de aplicación** del tipo **contraseña** y copia su valor para más tarde.
- Agregar la plataforma **web** para su aplicación.
- Escribir el **URI de redireccionamiento** correcto. El uri de redirección indica a Azure AD a dónde se deben dirigir las respuestas de autenticación: el valor predeterminado para este tutorial es `https://localhost:44326/`.


## Instalar OWIN
Agregue los paquetes NuGet del software intermedio OWIN al proyecto `TodoList-WebApp` mediante la Consola del Administrador de paquetes. Se usará el software intermedio OWIN para emitir solicitudes de inicio y cierre de sesión, administrar la sesión del usuario y obtener información sobre el usuario, entre otras cosas.

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect -ProjectName TodoList-WebApp
PM> Install-Package Microsoft.Owin.Security.Cookies -ProjectName TodoList-WebApp
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoList-WebApp
```

## Iniciar la sesión del usuario
Ahora configure el software intermedio OWIN para usar el [protocolo de autenticación OpenID Connect](active-directory-v2-protocols.md#openid-connect-sign-in-flow).

-	Abra el archivo `web.config` en la raíz del proyecto `TodoList-WebApp` y escriba los valores de configuración de su aplicación en la sección `<appSettings>`.
    -	El `ida:ClientId` es el **identificador de aplicación** asignado a su aplicación en el portal de registro.
	- El `ida:ClientSecret` es el **secreto de aplicación** que creó en el portal de registro.
    -	El `ida:RedirectUri` es el **identificador URI de redireccionamiento** que escribió en el portal.
- Abra el archivo `web.config` en la raíz del proyecto `TodoList-Service` y reemplace el `ida:Audience` con el mismo **Id. de aplicación** que en el caso anterior.


- Abra el archivo `App_Start\Startup.Auth.cs` y agregue instrucciones de `using` para las bibliotecas anteriores.
- Implemente el método `ConfigureAuth(...)` en el mismo archivo. Los parámetros que proporciona en `OpenIDConnectAuthenticationOptions` servirán como coordenadas para que su aplicación se comunique con Azure AD.

```C#
public void ConfigureAuth(IAppBuilder app)
{
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());

    app.UseOpenIdConnectAuthentication(
        new OpenIdConnectAuthenticationOptions
        {

					// The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0 
					// The `Scope` describes the permissions that your app will need.  See https://azure.microsoft.com/documentation/articles/active-directory-v2-scopes/
					// In a real application you could use issuer validation for additional checks, like making sure the user's organization has signed up for your app, for instance.

					ClientId = clientId,
					Authority = String.Format(CultureInfo.InvariantCulture, aadInstance, "common", "/v2.0 "),
					Scope = "openid email profile offline_access",
					RedirectUri = redirectUri,
					PostLogoutRedirectUri = redirectUri,
					TokenValidationParameters = new TokenValidationParameters
					{
						ValidateIssuer = false,
					},

					// The `AuthorizationCodeReceived` notification is used to capture and redeem the authorization_code that the v2.0 endpoint returns to your app.

					Notifications = new OpenIdConnectAuthenticationNotifications
					{
						AuthenticationFailed = OnAuthenticationFailed,
						AuthorizationCodeReceived = OnAuthorizationCodeReceived,
					}

    	});
}
...
```

## Uso de ADAL para obtener tokens de acceso
Queremos usar [OAuth 2.0 conjuntamente con OpenID Connect](active-directory-v2-protocols.md#openid-connect-with-oauth-code-flow) en la notificación de `AuthorizationCodeReceived` para canjear el authorization\_code en un token de acceso para el servicio de lista de tareas pendientes. ADAL puede facilitarle este proceso:

- En primer lugar, instale la versión preliminar de ADAL:

```PM> Install-Package Microsoft.Experimental.IdentityModel.Clients.ActiveDirectory -ProjectName TodoList-WebApp -IncludePrerelease``` - Y agregue otra instrucción `using` al archivo `App_Start\Startup.Auth.cs` para ADAL. - Ahora agregue un método nuevo, el controlador de eventos `OnAuthorizationCodeReceived`. Este controlador usará ADAL para obtener un token de acceso a la API de la lista de tareas pendientes y almacenará el token en la caché de tokens de ADAL para su uso posterior:

```C#
private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification notification)
{
		string userObjectId = notification.AuthenticationTicket.Identity.FindFirst("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier").Value;
		string tenantID = notification.AuthenticationTicket.Identity.FindFirst("http://schemas.microsoft.com/identity/claims/tenantid").Value;
		string authority = String.Format(CultureInfo.InvariantCulture, aadInstance, tenantID, string.Empty);
		ClientCredential cred = new ClientCredential(clientId, clientSecret);

		// Here you ask for a token using the web app's clientId as the scope, since the web app and service share the same clientId.
		var authContext = new Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext(authority, new NaiveSessionCache(userObjectId));
		var authResult = await authContext.AcquireTokenByAuthorizationCodeAsync(notification.Code, new Uri(redirectUri), cred, new string[] { clientId });
}
...
```

- En las aplicaciones web, ADAL tiene una caché de tokens extensible que puede utilizarse para almacenar los token. Este ejemplo implementa el `NaiveSessionCache` que usa almacenamiento de sesión http para almacenar los token en el caché.

<!-- TODO: Token Cache article -->


## 4\. Llamada a la API web
Ahora es el momento de usar el access\_token que adquirió en el paso 3. Abra el archivo `Controllers\TodoListController.cs` de la aplicación web para hacer todas las solicitudes CRUD a la API de la lista de tareas pendientes.

- Puede usar ADAL aquí de nuevo para capturar access\_tokens de la memoria caché de ADAL. En primer lugar, agregue una instrucción de `using` para ADAL en este archivo.

    `using Microsoft.Experimental.IdentityModel.Clients.ActiveDirectory;`

- En la acción `Index`, use el método `AcquireTokenSilentAsync` de ADAL para obtener un access\_token que pueda utilizarse para leer datos desde el servicio de lista de tareas pendientes:

```C#
...
string userObjectID = ClaimsPrincipal.Current.FindFirst("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier").Value;
string tenantID = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/tenantid").Value;
string authority = String.Format(CultureInfo.InvariantCulture, Startup.aadInstance, tenantID, string.Empty);
ClientCredential credential = new ClientCredential(Startup.clientId, Startup.clientSecret);

// Here you ask for a token using the web app's clientId as the scope, since the web app and service share the same clientId.
AuthenticationContext authContext = new AuthenticationContext(authority, new NaiveSessionCache(userObjectID));
result = await authContext.AcquireTokenSilentAsync(new string[] { Startup.clientId }, credential, UserIdentifier.AnyUser);
...
```

- Entonces, el ejemplo agrega el token resultante a la solicitud HTTP GET como el encabezado de `Authorization`, que usa el servicio de lista de tareas pendientes para autenticar la solicitud.
- Si el servicio de lista de tareas pendientes devuelve una respuesta de `401 Unauthorized`, el access\_tokens en ADAL dejó de ser válido por algún motivo. En este caso, debe anular cualquier access\_tokens de la memoria caché de ADAL y mostrar al usuario un mensaje de que necesita iniciar sesión de nuevo, lo que reiniciará el flujo de adquisición del token.

```C#
...
// If the call failed with access denied, then drop the current access token from the cache,
// and show the user an error indicating they might need to sign-in again.
if (response.StatusCode == System.Net.HttpStatusCode.Unauthorized)
{
		var todoTokens = authContext.TokenCache.ReadItems().Where(a => a.Scope.Contains(Startup.clientId));
		foreach (TokenCacheItem tci in todoTokens)
				authContext.TokenCache.DeleteItem(tci);

		return new RedirectResult("/Error?message=Error: " + response.ReasonPhrase + " You might need to sign in again.");
}
...
```

- Del mismo modo, si ADAL no puede devolver un access\_token por cualquier motivo, se recomienda que indique al usuario que vuelva a iniciar sesión. Esto es tan sencillo como detectar cualquier `AdalException`:

```C#
...
catch (AdalException ee)
{
		// If ADAL could not get a token silently, show the user an error indicating they might need to sign in again.
		return new RedirectResult("/Error?message=An Error Occurred Reading To Do List: " + ee.Message + " You might need to log out and log back in.");
}
...
```

- Se implementa exactamente la misma llamada de `AcquireTokenSilentAsync` en las acciones `Create` y `Delete`. En las aplicaciones web, puede utilizar este método ADAL para obtener access\_tokens siempre que los necesite en su aplicación. ADAL se encargará de adquirir, almacenar en caché y actualizar tokens por usted.

Por último, compile y ejecute su aplicación. Inicie sesión con una cuenta Microsoft o con una cuenta de Azure AD y observe cómo se refleja la identidad del usuario en la barra de navegación superior. Agregue y elimine algunos elementos de lista de tareas del usuario para ver las llamadas de API protegidas de OAuth 2.0 en acción. Ahora dispone de una aplicación web y de una API web, ambas protegidas mediante protocolos estándar del sector, que pueden autenticar a los usuarios tanto con sus cuentas personales como con sus cuentas profesionales/educativas.

Como referencia, [aquí puede ver](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet/archive/complete.zip) el ejemplo finalizado (sin sus valores de configuración).

## Pasos siguientes

Para obtener recursos adicionales, consulte: - [La guía para desarrolladores de v2.0 >>](active-directory-appmodel-v2-overview.md) - [La etiqueta "adal" de StackOverflow >>](http://stackoverflow.com/questions/tagged/adal)

<!---HONumber=AcomDC_0224_2016-->