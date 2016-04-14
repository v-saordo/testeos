<properties
   pageTitle="Autorización en aplicaciones multiinquilino | Microsoft Azure"
   description="Realización de la autorización en una aplicación multiinquilino"
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

# Autorización en aplicaciones multiinquilino

Este artículo [forma parte de una serie]. También hay una [aplicación de ejemplo] completa que acompaña a esta serie.

En este artículo trataremos dos enfoques generales para la autorización.

-	**Autorización basada en roles**. Autorizar una acción basándose en los roles asignados a un usuario. Por ejemplo, algunas acciones requieren un rol de administrador.
-	**Autorización basada en recursos**. Autorizar una acción basándose en un recurso determinado. Por ejemplo, cada recurso tiene un propietario. El propietario puede eliminar el recurso; otros usuarios no.

Una aplicación típica empleará una combinación de ambos. Por ejemplo, para eliminar un recurso, el usuario debe ser el propietario del mismo _o_ administrador.

## Autorización basada en roles

La aplicación [Tailspin Surveys][Tailspin] define los siguientes roles:

- Administrador. Puede realizar todas las operaciones CRUD en cualquier encuesta que pertenezca a ese inquilino.
- Creador. Puede crear nuevas encuestas.
- Lector. Puede leer cualquier encuesta que pertenezca a ese inquilino.

Los roles se aplican a los _usuarios_ de la aplicación. En la aplicación Surveys, un usuario es administrador, creador o lector.

Para ver una discusión de cómo definir y administrar roles, consulte [Application roles (Roles de aplicación)].

Independientemente de cómo administre los roles, el código de autorización tendrá un aspecto similar. ASP.NET Core 1.0 presenta una abstracción llamada _directivas de autorización_. Con esta característica, el usuario define directivas de autorización en el código y después las aplica a las acciones de controlador. La directiva se separa del controlador.

### Creación de directivas

Para definir una directiva, primero cree una clase que implemente `IAuthorizationRequirement`. Es lo más fácil para derivar de `AuthorizationHandler`. En el método `Handle`, examine las notificaciones pertinentes.

A continuación se muestra un ejemplo de la aplicación Tailspin Surveys:

```csharp
public class SurveyCreatorRequirement : AuthorizationHandler<SurveyCreatorRequirement>, IAuthorizationRequirement
{
    protected override void Handle(AuthorizationContext context, SurveyCreatorRequirement requirement)
    {
        if (context.User.HasClaim(ClaimTypes.Role, Roles.SurveyAdmin) ||
            context.User.HasClaim(ClaimTypes.Role, Roles.SurveyCreator))
        {
            context.Succeed(requirement);
        }
    }
}
```

> [AZURE.NOTE] Consulte [SurveyCreatorRequirement.cs].

Esta clase define el requisito para un usuario para crear una nueva encuesta. El usuario debe estar en el rol SurveyAdmin o SurveyCreator.

En la clase de inicio, defina una directiva con nombre que incluya uno o varios requisitos. Si hay varios requisitos, el usuario debe cumplir _todos_ ellos para que se le conceda autorización. El código siguiente define dos directivas:

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy(PolicyNames.RequireSurveyCreator,
        policy =>
        {
            policy.AddRequirements(new SurveyCreatorRequirement());
            policy.AddAuthenticationSchemes(CookieAuthenticationDefaults.AuthenticationScheme);
        });

    options.AddPolicy(PolicyNames.RequireSurveyAdmin,
        policy =>
        {
            policy.AddRequirements(new SurveyAdminRequirement());
            policy.AddAuthenticationSchemes(CookieAuthenticationDefaults.AuthenticationScheme);
        });
});
```

> [AZURE.NOTE] Consulte [Startup.cs].

Este código también establece el esquema de autenticación, que indica a ASP.NET qué middleware de autenticación se debe ejecutar si se produce un error en la autorización. En este caso, especificamos el middleware de autenticación de cookies porque dicho middleware puede redirigir al usuario a una página "Prohibido". La ubicación de la página Prohibido se establece en la opción AccessDeniedPath para el middleware de cookies; consulte [Configuring the authentication middleware (Configuración del middleware de autenticación)].

### Autorización de acciones de controlador

Por último, para autorizar una acción en un controlador MVC, establezca la directiva en el atributo `Authorize`:

```csharp
[Authorize(Policy = "SurveyCreatorRequirement")]
public IActionResult Create()
{
    // ...
}
```

En versiones anteriores de ASP.NET, establecería la propiedad **Roles** en el atributo:

```csharp
// old way
[Authorize(Roles = "SurveyCreator")]

```

Esto todavía se admite en ASP.NET Core 1.0, pero tiene algunos inconvenientes en comparación con las directivas de autorización:

-	Supone un tipo de notificación determinado. Las directivas pueden comprobar cualquier tipo de notificación. Los roles son simplemente un tipo de notificación.
-	El nombre de rol está codificado de forma rígida en el atributo. Con las directivas, la lógica de autorización está toda en un mismo lugar, lo que facilita la actualización o incluso la carga de la configuración.
-	Las directivas permiten decisiones de autorización más complejas (por ejemplo, edad > = 21) que no se pueden expresar mediante la pertenencia de rol simple.

## Autorización basada en recursos

La _autorización basada en recursos_ tiene lugar siempre que la autorización depende de un recurso específico que se verá afectado por una operación. En la aplicación Tailspin Surveys , cada encuesta tiene un propietario y de cero a muchos colaboradores.

-	El propietario puede leer, actualizar, eliminar, publicar y cancelar la publicación de la encuesta.
-	El propietario puede asignar colaboradores a la encuesta.
-	Los colaboradores pueden leer y actualizar la encuesta.

Tenga en cuenta que "propietario" y "colaborador" no son roles de aplicación; se almacenan por encuesta, en la base de datos de la aplicación. Por ejemplo, para comprobar si un usuario puede eliminar una encuesta, la aplicación comprueba si dicho usuario es el propietario de esa encuesta.

En ASP.NET Core 1.0, implemente la autorización basada en recursos derivando de **AuthorizationHandler** e invalidando el método **Handle**.

```csharp
public class SurveyAuthorizationHandler : AuthorizationHandler<OperationAuthorizationRequirement, Survey>
{
     protected override void Handle(AuthorizationContext context, OperationAuthorizationRequirement operation, Survey resource)
    {
    }
}
```

Observe que esta clase está fuertemente tipada para objetos Survey. Registre la clase para DI en el inicio:

```csharp
services.AddSingleton<IAuthorizationHandler>(factory =>
{
    return new SurveyAuthorizationHandler();
});
```

Para realizar comprobaciones de autorización, use la interfaz **IAuthorizationService**, que puede inyectar insertar en los controladores. El código siguiente comprueba si un usuario puede leer una encuesta:

```csharp
if (await _authorizationService.AuthorizeAsync(User, survey, Operations.Read) == false)
{
    return new HttpStatusCodeResult(403);
}
```

Como pasamos en un objeto `Survey`, esta llamada invocará a `SurveyAuthorizationHandler`.

En el código de autorización, un buen enfoque es agregar todos los permisos basados en roles y en recursos del usuario, y después comprobar el conjunto total respecto a la operación deseada. A continuación se muestra un ejemplo de la aplicación Surveys. La aplicación define varios tipos de permiso:

- Administrador
- Colaborador
- Creador
- Propietario
- Lector

La aplicación también define un conjunto de operaciones posibles en encuestas:

- Crear
- Lectura
- Actualizar
- Eliminar
- Publicar
- Cancelar publicación

El código siguiente crea una lista de permisos para un usuario y encuesta determinados. Observe que este código busca tanto en los roles de aplicación del usuario como en los campos de propietario y colaborador de la encuesta.

```csharp
protected override void Handle(AuthorizationContext context, OperationAuthorizationRequirement operation, Survey resource)
{
    var permissions = new List<UserPermissionType>();
    string userTenantId = context.User.GetTenantIdValue();
    int userId = ClaimsPrincipalExtensions.GetUserKey(context.User);
    string user = context.User.GetUserName();

    if (resource.TenantId == userTenantId)
    {
        // Admin can do anything, as long as the resource belongs to the admin's tenant.
        if (context.User.HasClaim(ClaimTypes.Role, Roles.SurveyAdmin))
        {
            context.Succeed(operation);
            return;
        }

        if (context.User.HasClaim(ClaimTypes.Role, Roles.SurveyCreator))
        {
            permissions.Add(UserPermissionType.Creator);
        }
        else
        {
            permissions.Add(UserPermissionType.Reader);
        }

        if (resource.OwnerId == userId)
        {
            permissions.Add(UserPermissionType.Owner);
        }
    }
    if (resource.Contributors != null && resource.Contributors.Any(x => x.UserId == userId))
    {
        permissions.Add(UserPermissionType.Contributor);
    }
    if (ValidateUserPermissions[operation](permissions))
    {
        context.Succeed(operation);
    }
}
```

> [AZURE.NOTE] Vea [SurveyAuthorizationHandler.cs].

En una aplicación de varios inquilinos, debe asegurarse de que los permisos no se "fugan" a los datos de otro inquilino. En la aplicación Surveys, se permite el permiso Colaborador a través de los inquilinos (puede asignar a alguien de otro inquilino como colaborador). Los demás tipos de permiso están restringidos a los recursos que pertenecen al inquilino de ese usuario, por lo que el código comprueba el identificador del inquilino antes de conceder esos tipos de permiso. (El campo `TenantId` asignado cuando se crea la encuesta.)

El paso siguiente es comprobar la operación (lectura, actualización, eliminación, etc.) en relación a los permisos. La aplicación Surveys implementa este paso mediante el uso de una tabla de búsqueda de funciones:

```csharp
static readonly Dictionary<OperationAuthorizationRequirement, Func<List<UserPermissionType>, bool>> ValidateUserPermissions
    = new Dictionary<OperationAuthorizationRequirement, Func<List<UserPermissionType>, bool>>

    {
        { Operations.Create, x => x.Contains(UserPermissionType.Creator) },

        { Operations.Read, x => x.Contains(UserPermissionType.Creator) ||
                                x.Contains(UserPermissionType.Reader) ||
                                x.Contains(UserPermissionType.Contributor) ||
                                x.Contains(UserPermissionType.Owner) },

        { Operations.Update, x => x.Contains(UserPermissionType.Contributor) ||
                                x.Contains(UserPermissionType.Owner) },

        { Operations.Delete, x => x.Contains(UserPermissionType.Owner) },

        { Operations.Publish, x => x.Contains(UserPermissionType.Owner) },

        { Operations.UnPublish, x => x.Contains(UserPermissionType.Owner) }
    };
```

## Recursos adicionales

- [Resource Based Authorization (Autorización basada en recursos)](https://docs.asp.net/en/latest/security/authorization/resourcebased.html)
- [Custom Policy-Based Authorization (Autorización personalizada basada en directivas)](https://docs.asp.net/en/latest/security/authorization/policies.html)

<!-- Links -->
[Tailspin]: guidance-multitenant-identity-tailspin.md
[forma parte de una serie]: guidance-multitenant-identity.md
[Application roles (Roles de aplicación)]: guidance-multitenant-identity-app-roles.md
[SurveyCreatorRequirement.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Security/Policy/SurveyCreatorRequirement.cs
[Startup.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Web/Startup.cs
[Configuring the authentication middleware (Configuración del middleware de autenticación)]: guidance-multitenant-identity-authenticate.md#configuring-the-authentication-middleware
[SurveyAuthorizationHandler.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Security/Policy/SurveyAuthorizationHandler.cs
[aplicación de ejemplo]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->