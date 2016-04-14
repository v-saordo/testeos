<properties
   pageTitle="Administración de identidades para aplicaciones multiinquilino | Microsoft Azure"
   description="Introducción a la administración de identidades en aplicaciones multiinquilino"
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

# Administración de identidades en aplicaciones multiinquilino: introducción

Este artículo [forma parte de una serie]. También hay una [aplicación de ejemplo] completa que acompaña a esta serie.

Supongamos que está escribiendo una aplicación de SaaS empresarial que se hospedará en la nube. Por supuesto, la aplicación tendrá usuarios:

![Usuarios](media/guidance-multitenant-identity/users.png)

Sin embargo, los usuarios pertenecen a las organizaciones:

![Usuarios de organización](media/guidance-multitenant-identity/org-users.png)

Ejemplo: Tailspin oferta suscripciones a su aplicación de SaaS. Contoso y Fabrikam se registran en la aplicación. Cuando Alice (`alice@contoso`) inicia sesión, la aplicación debe saber que esta usuaria forma parte de Contoso.

- Alice _debería_ tener acceso a datos de Contoso.
- Alice _no debería_ tener acceso a datos de Fabrikam.

Esta guía le mostrará cómo administrar identidades de usuario en una aplicación multiinquilino, usando [Azure Active Directory][AzureAD] (Azure AD) para controlar el inicio de sesión y la autenticación.

## ¿Qué significa multiinquilino?

Un _inquilino_ es un grupo de usuarios. En una aplicación SaaS, el inquilino es un suscriptor o un usuario de la aplicación. La _arquitectura multiinquilino_ es una arquitectura en la que varios inquilinos comparten la misma instancia física de la aplicación. Aunque los inquilinos comparten los recursos físicos (como las máquinas virtuales o el almacenamiento), cada inquilino obtiene su propia instancia lógica de la aplicación.

Normalmente, los datos de la aplicación se comparten entre los usuarios de un mismo inquilino, pero no con otros inquilinos.

![Multiinquilino](media/guidance-multitenant-identity/multitenant.png)

Compare esta arquitectura con otra de un solo inquilino, donde cada inquilino tiene una instancia física dedicada. En una arquitectura de un solo inquilino, los nuevos inquilinos se agregan mediante la puesta en marcha de nuevas instancias de la aplicación.

![Un solo inquilino](media/guidance-multitenant-identity/single-tenant.png)

### Arquitectura multiinquilino y escalado horizontal

Para lograr la reducción horizontal en la nube, es habitual agregar más instancias físicas. Este proceso se conoce como _escalado horizontal_. Piense en una aplicación web. Para administrar más tráfico, puede agregar más máquinas virtuales de servidor y colocarlas detrás de un equilibrador de carga. Cada máquina virtual ejecuta una instancia física independiente de la aplicación web.

![Equilibrio de carga de un sitio web](media/guidance-multitenant-identity/load-balancing.png)

Cualquier solicitud puede enrutarse a cualquier instancia. En conjunto, el sistema funciona como una única instancia lógica. Puede anular una máquina virtual o poner en marcha una nueva sin que los usuarios resulten afectados. En esta arquitectura, cada instancia física es multiinquilino, y se escala mediante la incorporación de más instancias. Si una instancia deja de funcionar, ningún inquilino debería resultar afectado.

## Identidad en una aplicación multiinquilino

En una aplicación multiinquilino, debe tener en cuenta a los usuarios en el contexto de los inquilinos.

**Autenticación**

- Los usuarios inician sesión en la aplicación con sus credenciales de la organización. No es necesario que creen nuevos perfiles de usuario para la aplicación.
- Los usuarios de la misma organización forman parte del mismo inquilino.
- Cuando un usuario inicia sesión, la aplicación sabe a qué inquilino pertenece el usuario.

**Autorización**

- Al autorizar las acciones de un usuario (por ejemplo, ver un recurso), la aplicación debe tener en cuenta el inquilino del usuario.
- Es necesario asignar roles a los usuarios dentro de la aplicación, como "Administrador" o "Usuario estándar". Las asignaciones de roles deben administrarse por el cliente, no por el proveedor de SaaS.

**Ejemplo**. Alice, una empleada de Contoso, va hasta la aplicación en su explorador y hace clic en el botón "Iniciar sesión". De este modo, se le redirige a una pantalla de inicio de sesión en la que escribe sus credenciales corporativas (nombre de usuario y contraseña). En este punto, esta usuaria está registrada en la aplicación como `alice@contoso.com`. La aplicación también sabe que Alice es una usuaria con rol de administrador para esta aplicación. En consecuencia, puede ver una lista de todos los recursos que pertenecen a Contoso. Sin embargo, no puede ver los recursos de Fabrikam, porque tiene el rol de administrador únicamente dentro de su inquilino.

En esta guía, examinaremos específicamente el uso de Azure AD para la administración de identidades.

- Se supone que el cliente almacena sus perfiles de usuario en Azure AD (incluidos los inquilinos de Office 365 y Dynamics CRM).
- Los usuarios con una instancia de Active Directory (AD) local pueden usar [Azure AD Connect][ADConnect] para sincronizar su AD local con Azure AD.

Si un cliente con AD local no puede usar Azure AD Connect (debido a la directiva de TI corporativa u otras razones), el proveedor de SaaS puede federarse con el AD del cliente a través de los Servicios de federación de Active Directory (AD FS). Esta opción se describe en [Federating with a customer's AD FS] (Federación con AD FS de un cliente).

Esta guía no tiene en cuenta otros aspectos de la arquitectura multiinquilino como la partición de datos, la configuración de cada inquilino, etc.

<!-- Links -->
[ADConnect]: ../active-directory/active-directory-aadconnect.md
[AzureAD]: https://azure.microsoft.com/documentation/services/active-directory/
[forma parte de una serie]: guidance-multitenant-identity.md
[Federating with a customer's AD FS]: guidance-multitenant-identity-adfs.md
[aplicación de ejemplo]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->