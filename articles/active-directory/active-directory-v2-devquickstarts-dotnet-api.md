<properties
	pageTitle="Modelo de aplicación v2.0 &gt;API web .Net| Microsoft Azure"
	description="Cómo crear una API web MVC de .NET que acepta los token de las cuentas de Microsoft y de las cuentas profesionales o educativas."
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
	ms.date="12/09/2015"
	ms.author="dastrock"/>

# Vista previa del modelo de aplicaciones v2.0: proteger una API web MVC

Con el modelo de aplicaciones v2.0, puede proteger una API web mediante el token de acceso [OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow), lo que permite a los usuarios que tengan cuenta personal de Microsoft y cuentas profesionales o educativas el acceso seguro a su API web.

> [AZURE.NOTE]Esta información se aplica a la vista previa pública del modelo de aplicaciones v2.0. Para obtener instrucciones sobre cómo integrarse en el servicio de Azure AD, disponible con carácter general, consulte la [Guía para desarrolladores de Azure Active Directory](active-directory-developers-guide.md).

En las API web ASP.NET puede realizar esto con el OWIN middleware de Microsoft incluido en .NET Framework 4.5. Aquí usaremos OWIN para crear una API web MVC de "Lista de tareas pendientes" que: - permita a los clientes crear y leer tareas desde la lista de tareas pendientes de un usuario. - designe las API protegidas. -valide que las llamadas de API web contienen un token de acceso válido.

Para llevar a cabo esta tarea, deberá hacer lo siguiente:

1. Registrar una aplicación con Azure AD
2. Configurar la aplicación para usar la canalización de autenticación OWIN.
3. Configurar una aplicación de cliente para llamar a la API web Lista de tareas pendientes

El código de este tutorial se conserva [en GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet). Para poder continuar, puede [descargar el esqueleto de la aplicación como .zip](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/skeleton.zip) o clonar el esqueleto:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet.git```

La aplicación completa se ofrece también al final de este tutorial.


## 1. Registrar una aplicación
Cree una nueva aplicación en [apps.dev.microsoft.com](https://apps.dev.microsoft.com) o siga estos [pasos detallados](active-directory-v2-app-registration.md). Asegúrese de que:

- Anotar el **Id. de aplicación** asignado a su aplicación; lo necesitará pronto.

La solución de Visual Studio también contiene "TodoListClient", que es una sencilla aplicación WPF.  TodoListClient se utiliza para demostrar cómo el usuario inicia sesión y cómo el cliente puede enviar solicitudes a la API de web.  En este caso, tanto TodoListClient como TodoListService se representan por la misma aplicación.  Para configurar TodoListClient, también debe:

- Agregar la plataforma **Móvil** a la aplicación.
- Copiar el **URI de redirección** desde el portal. Debe usar el valor predeterminado de `urn:ietf:wg:oauth:2.0:oob`.


## 2. Configurar la aplicación para que use la canalización de autenticación OWIN

Ahora que ha registrado una aplicación, debe configurarla para comunicarse con el extremo v2.0 con objeto de validar las solicitudes y tokens entrantes.

-	Para comenzar, abra la solución y agregue los paquetes NuGet de middleware OWIN al proyecto de ServicioListaTodo mediante la Consola del Administrador de paquetes.

```
PM> Install-Package Microsoft.Owin.Security.OAuth -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Security.Jwt -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService
```

-	Agregue una Clase de inicio OWIN al proyecto de ServicioListaTodo llamado `Startup.cs`. Haga clic con el botón derecho en el proyecto --> **Agregar** --> **Nuevo elemento** --> Busque "OWIN". El middleware OWIN invocará el método `Configuration(…)` al iniciarse la aplicación.
-	Cambie la declaración de clase a `public partial class Startup` (ya hemos implementado parte de esta clase para usted en otro archivo). En el método `Configuration(…)`, realice una llamada a ConfigureAuth(...) para configurar la autenticación para la aplicación web.

```C#
public partial class Startup
{
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

-	Abra el archivo `App_Start\Startup.Auth.cs` e implemente el método `ConfigureAuth(…)`, que configurará la API web para aceptar tokens desde el punto de conexión v2.0.

```C#
public void ConfigureAuth(IAppBuilder app)
{
		var tvps = new TokenValidationParameters
		{
				// In this app, the TodoListClient and TodoListService
				// are represented using the same Application Id - we use
				// the Application Id to represent the audience, or the
				// intended recipient of tokens.

				ValidAudience = clientId,

				// In a real applicaiton, you might use issuer validation to
				// verify that the user's organization (if applicable) has
				// signed up for the app.  Here, we'll just turn it off.

				ValidateIssuer = false,
		};

		// Set up the OWIN pipeline to use OAuth 2.0 Bearer authentication.
		// The options provided here tell the middleware about the type of tokens
		// that will be recieved, which are JWTs for the v2.0 endpoint.

		// NOTE: The usual WindowsAzureActiveDirectoryBearerAuthenticaitonMiddleware uses a
		// metadata endpoint which is not supported by the v2.0 endpoint.  Instead, this
		// OpenIdConenctCachingSecurityTokenProvider can be used to fetch & use the OpenIdConnect
		// metadata document.

		app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
		{
				AccessTokenFormat = new Microsoft.Owin.Security.Jwt.JwtFormat(tvps, new OpenIdConnectCachingSecurityTokenProvider("https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration")),
		});
}
```

-	Ahora puede utilizar atributos de `[Authorize]` para proteger sus controladores y acciones con la autenticación portadora de OAuth 2.0. Decore la clase `Controllers\TodoListController.cs` con una etiqueta Autorizar. Esto obligará al usuario a iniciar sesión antes de tener acceso a esa página.

```C#
[Authorize]
public class TodoListController : ApiController
{
```

- Cuando un autor de llamada autorizado invoca correctamente una de las API `TodoListController`, es posible que la acción deba tener acceso a información sobre este. OWIN proporciona acceso a las notificaciones dentro del token portador a través del objeto `ClaimsPrincpal`.  

```C#
public IEnumerable<TodoItem> Get()
{
    // You can use the ClaimsPrincipal to access information about the
    // user making the call.  In this case, we use the 'sub' or
    // NameIdentifier claim to serve as a key for the tasks in the data store.

    Claim subject = ClaimsPrincipal.Current.FindFirst(ClaimTypes.NameIdentifier);

    return from todo in todoBag
           where todo.Owner == subject.Value
           select todo;
}
```

-	Por último, abra el archivo `web.config` en la raíz del proyecto de ServicioListaTodo y escriba sus valores de configuración en la sección `<appSettings>`.
  -	Su `ida:Audience` es el **Id. de aplicación** de la aplicación que escribió en el portal.

## 3\. Configurar una aplicación cliente y ejecutar el servicio
Para poder ver el servicio de lista de tareas pendientes en acción, deberá configurar el cliente de lista de tareas pendientes para que pueda obtener token del extremo v2.0 y realizar llamadas al servicio.

- El proyecto TodoListClient, abra `App.config` y escriba sus valores de configuración en la sección `<appSettings>`.
  -	Su Id. de aplicación `ida:ClientId` que copió desde el portal.
	- `ida:RedirectUri` es el **uri de redireccionamiento** desde el portal.

Por último, limpie, compile y ejecute cada proyecto. Ahora dispone de una API web MVC de .NET que acepta los token de las cuentas de Microsoft y de las cuentas profesionales o educativas. Inicie sesión en TodoListClient y llame a la api web para agregar tareas a la lista de tareas del usuario.

Como referencia, el ejemplo finalizado (sin sus valores de configuración) [se proporciona en forma de archivo .zip aquí](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/complete.zip), aunque también puede clonarlo desde GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet.git```

## Pasos siguientes
Ahora puede pasar a otros temas adicionales. También puede probar lo siguiente:

[Llamar a una API web desde una aplicación web con el modelo de aplicaciones v2.0 >>](active-directory-devquickstarts-webapp-webapi-dotnet.md)

Para obtener recursos adicionales, consulte: - [la vista previa del modelo de aplicaciones v2.0 >>](active-directory-appmodel-v2-overview.md) - [la etiqueta "azure-active-directory" StackOverflow >>](http://stackoverflow.com/questions/tagged/azure-active-directory).

<!---HONumber=AcomDC_1217_2015-->