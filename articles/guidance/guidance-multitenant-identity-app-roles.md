<properties
   pageTitle="Roles de aplicación | Microsoft Azure"
   description="Realización de la autorización mediante roles de aplicación"
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

#  Roles de aplicación en aplicaciones multiinquilino

Este artículo [forma parte de una serie]. También hay una [aplicación de ejemplo] completa que acompaña a esta serie.

Los roles de aplicación se usan para asignar permisos a usuarios. Por ejemplo, la aplicación [Tailspin Surveys][Tailspin] define los siguientes roles:

- Administrador. Puede realizar todas las operaciones CRUD en cualquier encuesta que pertenezca a ese inquilino.
- Creador. Puede crear nuevas encuestas.
- Lector. Puede leer cualquier encuesta que pertenezca a ese inquilino.

Puede ver que los roles finalmente se traducen en permisos durante la [autorización]. Pero la primera pregunta es cómo asignar y administrar roles. Identificamos tres opciones principales:

-	[Roles de aplicación de Azure AD](#roles-using-azure-ad-app-roles)
-	[Grupos de seguridad de Azure AD](#roles-using-azure-ad-security-groups)
-	[Administrador de roles de aplicación](#roles-using-an-application-role-manager).

## Roles que usan roles de aplicación de Azure AD

Este es el enfoque que utilizamos en la aplicación Tailspin Surveys.

En este enfoque, el proveedor SaaS define los roles de aplicación agregándolos al manifiesto de aplicación. Después de que un cliente se suscriba, un administrador del directorio de AD del cliente asigna usuarios a los roles. Cuando un usuario inicia sesión, los roles asignados del usuario se envían como notificaciones.

> [AZURE.NOTE] Si el cliente tiene Azure AD Premium, el administrador puede asignar un grupo de seguridad a un rol y los miembros del grupo heredarán el rol de aplicación. Esto es una forma cómoda de administrar roles, porque el propietario del grupo no necesita ser un administrador de AD.

Ventajas de este enfoque:

-	Modelo de programación simple.
-	Los roles son específicos de la aplicación. Las notificaciones de rol para una aplicación no se envían a otra aplicación.
-	Si el cliente quita la aplicación de su inquilino de AD, los roles desaparecen.
-	La aplicación no necesita ningún permiso adicional de Active Directory que no sea el de lectura del perfil del usuario.

Inconvenientes:

- Los clientes que no tienen Azure AD Premium no pueden asignar grupos de seguridad a roles. Para estos clientes, todas las asignaciones de usuario las debe llevar a cabo un administrador de AD.
- Si tiene una API web de back-end, que es independiente de la aplic. web, las asignaciones de rol para la aplic. web no se aplican a la API web. Para obtener más información acerca de este punto, vea [Securing a backend web API (Protección de una API web de back-end)].

### Implementación

**Definir los roles.** El proveedor de SaaS declara los roles de aplicación en el manifiesto de aplicación. Por ejemplo, a continuación se muestra la entrada de manifiesto de la aplicación Surveys:

```
"appRoles": [
  {
    "allowedMemberTypes": [
      "User"
    ],
    "description": "Creators can create Surveys",
    "displayName": "SurveyCreator",
    "id": "1b4f816e-5eaf-48b9-8613-7923830595ad",
    "isEnabled": true,
    "value": "SurveyCreator"
  },
  {
    "allowedMemberTypes": [
      "User"
    ],
    "description": "Administrators can manage the Surveys in their tenant",
    "displayName": "SurveyAdmin",
    "id": "c20e145e-5459-4a6c-a074-b942bbd4cfe1",
    "isEnabled": true,
    "value": "SurveyAdmin"
  }
],
```

La propiedad `value` aparece en la notificación de rol. La propiedad `id` es el identificador único para el rol definido. Siempre genera un nuevo valor GUID para `id`.

**Asignar usuarios**. Cuando se suscribe un nuevo cliente, la aplicación se registra en el inquilino de AD del cliente. En este punto, un administrador de AD para ese inquilino puede asignar usuarios a roles.

> [AZURE.NOTE] Como se señaló anteriormente, los clientes con Azure AD Premium también pueden asignar grupos de seguridad a los roles.

La siguiente captura de pantalla del Portal de Azure muestra tres usuarios. Alice se asignó directamente a un rol. Bob heredó un rol como miembro de un grupo de seguridad llamado "Surveys Admin", que se asigna a un rol. Charles no está asignado a ningún rol.

![Usuarios asignados](media/guidance-multitenant-identity/role-assignments.png)

> [AZURE.NOTE] Como alternativa, la aplicación puede asignar roles mediante programación, usando la API Graph de Azure AD. Sin embargo, esto requiere que la aplicación obtenga permisos de escritura para el directorio de AD del cliente. Una aplicación con esos permisos podría provocar bastantes daños; el cliente confía en que la aplicación no va a destrozar su directorio. Muchos clientes pueden no estar dispuestos a conceder este nivel de acceso.

**Obtener notificaciones de rol**. Cuando un usuario inicia sesión, la aplicación recibe los roles asignados del usuario en una notificación con el tipo `http://schemas.microsoft.com/ws/2008/06/identity/claims/role`.

Un usuario puede tener varios roles o ningún rol. En el código de autorización, no suponga que el usuario tiene exactamente un rol de notificación. En su lugar, escriba el código que comprueba si hay un valor de notificación determinado:

```csharp
if (context.User.HasClaim(ClaimTypes.Role, "Admin")) { ... }
```

## Roles que usan grupos de seguridad de Azure AD

En este enfoque, los roles se representan como grupos de seguridad de AD. La aplicación asigna permisos a los usuarios según la pertenencia de estos a grupos de seguridad.

Ventajas:

-	Para los clientes que no tienen Azure AD Premium, este enfoque les permite utilizar grupos de seguridad para administrar las asignaciones de roles.

Desventajas:

- Complejidad. Dado que cada inquilino envía notificaciones de grupo diferentes, la aplicación debe realizar un seguimiento de qué grupos de seguridad corresponden a qué roles de aplicación para cada inquilino.
- Si el cliente quita la aplicación de su inquilino de AD, los grupos de seguridad se mantienen en su directorio de AD.

### Implementación

En el manifiesto de aplicación, establezca la propiedad `groupMembershipClaims` en "SecurityGroup". Esto es necesario para obtener notificaciones de pertenencia a grupo de AAD.

```
{
   // ...
   "groupMembershipClaims": "SecurityGroup",
}
```

Cuando un nuevo cliente se suscribe, la aplicación indica al cliente que cree grupos de seguridad para los roles que necesita la aplicación. A continuación, el cliente necesita escribir los identificadores de objeto de grupo en la aplicación. La aplicación almacena esto en una tabla que asigna identificadores de grupo a roles de la aplicación por inquilino.

> [AZURE.NOTE] Como alternativa, la aplicación podría crear los grupos mediante programación usando la API Graph de Azure AD. Esto sería menos propenso a errores. Sin embargo, ello requiere que la aplicación obtenga permisos de "lectura y escritura en todos los grupos" para el directorio de AD del cliente. Muchos clientes pueden no estar dispuestos a conceder este nivel de acceso.

Cuando un usuario inicia sesión:

1.	La aplicación recibe los grupos del usuario como notificaciones. El valor de cada notificación es el identificador de objeto de un grupo.
2.	Azure AD limita el número de grupos enviados en el token. Si el número de grupos supera este límite, Azure AD envía una notificación especial que indica que se está por encima del límite. Si esa notificación está presente, la aplicación debe consultar la API Graph de Azure AD para obtener todos los grupos a los que pertenece ese usuario. Para obtener más información, vea [Authorization in Cloud Applications using AD Groups (Autorización de aplicaciones en la nube mediante grupos de AD)], en la sección titulada "Groups claim overage" (Exceso de notificaciones de grupos).
3.	La aplicación busca los identificadores de objeto en su propia base de datos, para encontrar los roles de aplicación correspondientes para asignar al usuario.
4.	La aplicación agrega un valor de notificación personalizado a la entidad de seguridad del usuario que expresa el rol de aplicación. Por ejemplo: `survey_role` = "SurveyAdmin".

Las directivas de autorización deben usar la notificación de rol personalizada, no la notificación de grupo.

## Roles que usan un administrador de roles de aplicación

Con este enfoque, los roles de aplicación no se almacenan en Azure AD en absoluto. En su lugar, la aplicación almacena las asignaciones de roles para cada usuario en su propia base de datos (por ejemplo, mediante la clase **RoleManager** en ASP.NET Identity).

Ventajas:

-	La aplicación tiene control total sobre los roles y asignaciones de usuario.

Inconvenientes:

- Más complejo, es más difícil de mantener.
- No se pueden usar grupos de seguridad de AD para administrar asignaciones de roles.
- Almacena la información del usuario en la base de datos de la aplicación, donde puede perder la sincronización con el directorio de AD del inquilino a medida que se agregan o quitan usuarios.   

Existen muchos ejemplos para este enfoque. Por ejemplo, vea [Crear una aplicación ASP.NET MVC con la autenticación y Base de datos SQL e implementar al Servicio de aplicaciones de Azure].


## Recursos adicionales

-	[Authorization in a web app using Azure AD application roles & role claims (Autorización en una aplic. web con roles de aplicación de Azure AD y notificaciones de rol)]
-	[Autorización en aplicaciones de nube mediante grupos de AD (Autorización de aplicaciones en la nube mediante grupos de AD)]
-	[Roles based access control in cloud applications using Azure AD (Control de acceso basado en roles en aplicaciones en la nube mediante Azure AD)]
-	[Tipos de notificaciones y tokens admitidos]. Describe las notificaciones de roles y grupos en Azure AD.
-	[Descripción del manifiesto de aplicación de Azure Active Directory]

<!-- Links -->
[Tailspin]: guidance-multitenant-identity-tailspin.md
[forma parte de una serie]: guidance-multitenant-identity.md
[autorización]: guidance-multitenant-identity-authorize.md
[Securing a backend web API (Protección de una API web de back-end)]: guidance-multitenant-identity-web-api.md
[Authorization in Cloud Applications using AD Groups (Autorización de aplicaciones en la nube mediante grupos de AD)]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[Autorización en aplicaciones de nube mediante grupos de AD (Autorización de aplicaciones en la nube mediante grupos de AD)]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[Crear una aplicación ASP.NET MVC con la autenticación y Base de datos SQL e implementar al Servicio de aplicaciones de Azure]: ../app-service-web/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md
[Descripción del manifiesto de aplicación de Azure Active Directory]: ../active-directory/active-directory-application-manifest.md
[Tipos de notificaciones y tokens admitidos]: ../active-directory/active-directory-token-and-claims.md
[Roles based access control in cloud applications using Azure AD (Control de acceso basado en roles en aplicaciones en la nube mediante Azure AD)]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
[Authorization in a web app using Azure AD application roles & role claims (Autorización en una aplic. web con roles de aplicación de Azure AD y notificaciones de rol)]: https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims
[aplicación de ejemplo]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->