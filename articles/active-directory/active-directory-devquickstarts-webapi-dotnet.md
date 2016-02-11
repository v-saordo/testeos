<properties
	pageTitle="Introducción a .NET de Azure AD | Microsoft Azure"
	description="Cómo crear una API web de .NET MVC que se integra con Azure AD para su autenticación y autorización."
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


# Proteger una API web con tokens portadores de Azure AD

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Si crea una aplicación que proporciona acceso a recursos protegidos, deberá saber cómo proteger dichos recursos frente a accesos injustificados. Azure AD facilita la protección de una API web mediante tokens de acceso portadores de OAuth 2.0 con solo unas pocas líneas de código.

En las aplicaciones web Asp.NET, puede realizar esto con la implementación del middleware OWIN orientado a la comunidad incluido en .NET Framework 4.5. Aquí, usaremos OWIN para crear una API web "Lista de tareas pendientes" que: -Designe las API protegidas. -Valide que las llamadas de API web contienen un token de acceso válido.

Para llevar a cabo esta tarea, deberá hacer lo siguiente:

1. Registrar una aplicación con Azure AD
2. Configurar la aplicación para usar la canalización de autenticación OWIN.
3. Configurar una aplicación cliente para llamar a la API web Lista de tareas pendientes

Para empezar, [descargue el esquema de la aplicación](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/skeleton.zip) o [descargue el ejemplo finalizado](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip). Cualquiera de los dos son una solución de Visual Studio 2013. También necesitará a un inquilino de Azure AD en el que registrar la aplicación. Si aún no tiene uno, [descubra cómo conseguirlo](active-directory-howto-tenant.md).


## *1. Registrar una aplicación con Azure AD*
Para proteger su aplicación, primero deberá crear una aplicación en el inquilino y proporcionar a Azure AD algunos elementos de información clave.

-	Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com)
-	En el panel de navegación izquierdo, haga clic en **Active Directory**.
-	Seleccione el inquilino en el que va a registrar la aplicación.
-	Haga clic en la pestaña **Aplicaciones** y en **Agregar** en el cajón inferior.
-	Siga las indicaciones y cree una nueva **Aplicación web y/o API web**.
    -	El **nombre** de la aplicación describirá su aplicación a los usuarios finales. Escriba "Servicio de lista de tareas pendientes".
    -	El **Uri de redirección** es una combinación de esquema y cadena que utilizaría Azure AD para devolver los tokens solicitados por la aplicación. Escriba `https://localhost:44321/` para este valor.
-	Una vez completado el registro, diríjase a la pestaña **Configurar** y busque el campo **URI de id. de aplicación**. Escriba un identificador específico de un inquilino para este valor, p. ej. `https://contoso.onmicrosoft.com/TodoListService`
- Guarde la configuración. Deje abierto el portal (también deberá registrar su aplicación cliente en breve).

## *2. Configurar la aplicación para usar la canalización de autenticación OWIN*

Ahora que ha registrado una aplicación con Azure AD, deberá configurarla para comunicarse con Azure AD a fin de validar solicitudes y tokens entrantes.

-	Para comenzar, abra la solución y agregue los paquetes NuGet de middleware OWIN al proyecto de ServicioListaTodo mediante la Consola del Administrador de paquetes.

```
PM> Install-Package Microsoft.Owin.Security.ActiveDirectory -ProjectName TodoListService
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

-	Abra el archivo `App_Start\Startup.Auth.cs` e implemente el método `ConfigureAuth(…)`. Los parámetros que proporciona en `WindowsAzureActiveDirectoryBearerAuthenticationOptions` servirán como coordenadas para que su aplicación se comunique con Azure AD.

```C#
public void ConfigureAuth(IAppBuilder app)
{
    app.UseWindowsAzureActiveDirectoryBearerAuthentication(
        new WindowsAzureActiveDirectoryBearerAuthenticationOptions
        {
            Audience = ConfigurationManager.AppSettings["ida:Audience"],
            Tenant = ConfigurationManager.AppSettings["ida:Tenant"]
        });
}
```

-	Ahora puede utilizar atributos `[Authorize]` para proteger sus controladores y acciones con la autenticación portadora JWT. Decore la clase `Controllers\TodoListController.cs` con una etiqueta Autorizar. Esto obligará al usuario a iniciar sesión antes de tener acceso a esa página.

```C#
[Authorize]
public class TodoListController : ApiController
{
```

- Cuando un autor de llamada autorizado invoca correctamente una de las API `TodoListController`, es posible que la acción deba tener acceso a información sobre este. OWIN proporciona acceso a las notificaciones dentro del token portador a través del objeto `ClaimsPrincpal`.  
- Es un requisito común de las API web validar los "ámbitos" presentes en el token (esto garantiza la aceptación por parte del usuario final de los permisos necesarios para tener acceso al servicio de lista Todo):

```C#
public IEnumerable<TodoItem> Get()
{
    // user_impersonation is the default permission exposed by applications in AAD
    if (ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/scope").Value != "user_impersonation")
    {
        throw new HttpResponseException(new HttpResponseMessage {
          StatusCode = HttpStatusCode.Unauthorized,
          ReasonPhrase = "The Scope claim does not contain 'user_impersonation' or scope claim not found"
        });
    }
    ...
}
```

-	Por último, abra el archivo `web.config` en la raíz del proyecto de ServicioListaTodo y escriba sus valores de configuración en la sección `<appSettings>`.
  -	`ida:Tenant` es el nombre de su inquilino de Azure AD, p. ej., "contoso.onmicrosoft.com".
  -	Su `ida:Audience` es el URI de id. de aplicación de la aplicación que escribió en el Portal de Azure.

## *3. Configurar una aplicación cliente y ejecutar el servicio*
Para poder ver el servicio de lista Todo en acción, deberá configurar el cliente de lista Todo para que pueda obtener tokens de AAD y realizar llamadas al servicio.

- Volver al [Portal de administración de Azure](https://manage.windowsazure.com)
- Cree una nueva aplicación en su inquilino de Azure AD y seleccione **Aplicación de cliente nativo** en la indicación resultante.
    -	El **nombre** de la aplicación describirá su aplicación a los usuarios finales
    -	Escriba `http://TodoListClient/` para el valor **Uri de redirección**.
- Una vez que haya completado el registro, AAD asignará a su aplicación un **id. de aplicación** único. Necesitará este valor en los pasos siguientes, de modo que cópielo desde la pestaña Configurar.
-	Una vez completado el registro, diríjase a la pestaña **Configurar** y busque el campo **URI de id. de aplicación**. Escriba un identificador específico de un inquilino para este valor, p. ej. `https://contoso.onmicrosoft.com/TodoListService`
- También en la pestaña **Configurar**, busque la sección "Permisos para otras aplicaciones". Haga clic en "Agregar aplicación". Seleccione "Otras" en la lista desplegable "Mostrar" y haga clic en la marca de comprobación superior. Busque y haga clic en su servicio de lista de tareas pendientes y en la marca de comprobación inferior para agregar la aplicación. Seleccione "Acceso al servicio de lista de tareas pendientes" en la lista desplegable "Permisos delegados" y guarde la configuración.


- En Visual Studio, abra `App.config` en el proyecto de ClienteListaTodo y escriba sus valores de configuración en la sección `<appSettings>`.
  -	`ida:Tenant` es el nombre de su inquilino de Azure AD, p. ej., "contoso.onmicrosoft.com".
  -	Su id. de aplicación `ida:ClientId` que copió desde el Portal de Azure.
  -	Su `todo:TodoListResourceId` es el URI de id. de aplicación de la aplicación de servicio de lista de tareas pendientes que escribió en el Portal de Azure.

Por último, limpie, compile y ejecute cada proyecto. Si aún no lo ha hecho, ahora es el momento de crear un nuevo usuario en su inquilino con un dominio *.onmicrosoft.com. Inicie sesión en el cliente de lista de tareas pendientes con ese usuario y agregue algunas tareas a la lista de tareas pendientes del usuario.

Como referencia, [aquí](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip) puede ver el ejemplo finalizado (sin sus valores de configuración). Ahora puede pasar a más escenarios de identidad adicionales que es posible que desee probar:

[Crear un cliente nativo de .NET con Azure AD >>](../active-directory-devquickstarts-native-dotnet.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]

<!---HONumber=AcomDC_0128_2016-->