<properties
    pageTitle="Administración de las aplicaciones con Azure Active Directory | Microsoft Azure"
    description="En este artículo se describen las ventajas de la integración de Azure Active Directory con las aplicaciones locales, SaaS y de nube."
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="stevenpo"
    editor=""/>

   <tags
      ms.service="active-directory"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="identity"
      ms.date="02/16/2016"
      ms.author="markvi"/>

# Administración de aplicaciones con Azure Active Directory

Además del flujo de trabajo o contenido real, para las empresas las aplicaciones deben cumplir dos requisitos básicos:

1. Para aumentar la productividad, debe ser fácil detectar y tener acceso a las aplicaciones.

2. Para permitir la seguridad y la regulación, las organizaciones deben controlar y supervisar quién puede tener acceso y quién accede realmente a cada aplicación.

En palabras de las aplicaciones en la nube, esto se consigue mejor usando la identidad para controlar "*A QUIÉN se le permite hacer QUÉ*".

En la terminología informática:

- *Quién* se conoce como *identidad*: la administración de usuarios y grupos.

- *Qué* se conoce como *administración de acceso*: la administración del acceso a los recursos protegidos.

Ambos componentes juntos se conocen como *Administración de identidad y acceso (IAM)*, que, según define el grupo [Gartner](http://www.gartner.com/it-glossary/identity-and-access-management-iam), es "*la disciplina de seguridad que permite a las personas adecuadas tener acceso a los recursos adecuados en los momentos adecuados y por los motivos adecuados*".

Entonces, ¿cuál es el problema? Si IAM *no se administra* en un solo lugar con una solución integrada:

- Los administradores de identidades tienen que crear y actualizar individualmente las cuentas de usuario de todas las aplicaciones por separado, una actividad repetitiva y laboriosa.

- Los usuarios tienen que memorizar varias credenciales para tener acceso a las aplicaciones con las que necesitan trabajar. Como resultado, los usuarios tienden a anotar las contraseñas o usar otras soluciones de administración de contraseñas que presentan otros riesgos para la seguridad de los datos.

- Las actividades repetitivas y laboriosas reducen la cantidad de tiempo que los usuarios y los administradores trabajan en actividades que aumentan el balance final del negocio.

De modo que, ¿qué impide normalmente a las organizaciones adoptar soluciones IAM integradas?

- La mayoría de las soluciones técnicas se basan en plataformas de software que cada organización debe implementar y adaptar para sus propias aplicaciones.

- Las aplicaciones de nube a menudo se adoptan a un ritmo más rápido del que las organizaciones de TI pueden integrarse con las soluciones IAM existentes.

- Las herramientas de seguridad y supervisión requieren personalización e integración adicionales para conseguir escenarios E2E integrales.

## Azure Active Directory integrado con las aplicaciones

Azure Active Directory es la solución completa de identidad como servicio (IDaaS) de Microsoft que:

- Habilita la IAM como un servicio en la nube. 

- Proporciona administración de acceso central, inicio de sesión único (SSO) y creación de informes.

- Admite la administración de acceso integrado para [miles de aplicaciones](https://azure.microsoft.com/marketplace/active-directory/) de la Galería de aplicaciones, incluidas Salesforce, Google Apps, Box, Concur, etc.


Con todas las aplicaciones de Azure Active Directory que publica para sus asociados y clientes (empresa o consumidor) tendrá las mismas funcionalidades de administración de identidad y acceso.<br> Esto permite reducir significativamente los costos operativos.

¿Y si necesita implementar una aplicación que aún no aparece en la galería de aplicaciones? Aunque es un proceso un poco más lento que configurar el inicio de sesión único para las aplicaciones desde la galería de aplicaciones, Azure AD proporciona un asistente que le ayudará con la configuración.

El valor de Azure AD no solo se limita a las aplicaciones en la nube. También puede utilizarlo con aplicaciones locales al proporcionar acceso remoto seguro. Con el acceso remoto seguro, puede eliminar la necesidad de VPN o de otras implementaciones tradicionales de administración de acceso remoto.

Al proporcionar administración de acceso centralizada e inicio de sesión único (SSO) para todas las aplicaciones, Azure AD es la solución a los principales problemas de seguridad y productividad de los datos.

- Los usuarios pueden tener acceso a varias aplicaciones con un solo inicio de sesión, lo que se traduce en más tiempo para generar ingresos o para realizar actividades de operaciones empresariales.

- Los administradores de identidades pueden administrar el acceso a las aplicaciones en un solo lugar.

La ventaja para el usuario y la empresa es obvia. Examinemos más de cerca las ventajas para un administrador de identidades y para la organización.

## Ventajas de aplicaciones integradas

El proceso SSO consta de dos pasos:

- Autenticación, el proceso de validar la identidad del usuario.

- Autorización, la decisión de habilitar o bloquear el acceso a un recurso con una directiva de acceso.

Al usar Azure AD para administrar las aplicaciones y habilitar el SSO:

- La autenticación se realiza en la cuenta local del usuario (por ejemplo, AD) o en la cuenta de Azure AD.

- La autorización se ejecuta en la directiva de asignación y protección de Azure AD, lo que garantiza una experiencia coherente para el usuario final y permite agregar asignaciones, ubicaciones y condiciones MFA en cualquier aplicación, independientemente de sus capacidades internas.

Es importante comprender que la forma en que la autorización se aplica en la aplicación de destino varía en función de cómo se integra la aplicación con Azure AD.

- **Aplicaciones integradas previamente por el proveedor de servicios**, como Office 365 y Azure. Se trata de aplicaciones integradas directamente en Azure AD y que dependen de él para sus funcionalidades completas de administración de identidades y acceso. El acceso a estas aplicaciones se habilita a través de la información de directorio y la emisión de tokens.

- **Aplicaciones integradas previamente por Microsoft y aplicaciones personalizadas**. Son aplicaciones de nube independientes que dependen de un directorio de aplicaciones interno y pueden funcionar con independencia de Azure AD. El acceso a estas aplicaciones se habilita mediante la emisión de una credencial específica de la aplicación asignada a una cuenta de aplicación. Dependiendo de las funcionalidades de la aplicación, la credencial puede ser un token de federación o un nombre de usuario y una contraseña para una cuenta que se aprovisionó anteriormente en la aplicación.

- **Aplicaciones locales**. Son aplicaciones publicadas principalmente a través del proxy de la aplicación, que permiten el acceso a aplicaciones locales. Estas aplicaciones se basan en un directorio local central, como Windows Server Active Directory. El acceso a estas aplicaciones se habilita mediante el desencadenamiento del proxy para entregar el contenido de la aplicación al usuario final, al tiempo que se respeta el requisito de inicio de sesión local.

Por ejemplo, si un usuario se une a su organización, deberá crear una cuenta para él en Azure AD para las operaciones de inicio de sesión principales. Si este usuario requiere acceso a una aplicación administrada, como Salesforce, también deberá crearle una cuenta en Salesforce y vincularla a la cuenta de Azure para que SSO funcione. Cuando el usuario deja la organización, es aconsejable eliminar la cuenta de Azure AD y todas las cuentas homólogas de los almacenes de IAM de las aplicaciones a las que el usuario tuvo acceso.

## Detección de acceso

En las empresas modernas, los departamentos de TI no son a menudo conscientes de todas las aplicaciones en la nube que se usan. Junto con Cloud App Discovery, Azure AD proporciona una solución para detectar estas aplicaciones.

## Administración de cuentas

Tradicionalmente, la administración de cuentas de las distintas aplicaciones es un proceso manual que realiza TI o el personal de soporte de la organización. Azure AD automatiza completamente la administración de cuentas en todas las aplicaciones integradas del proveedor de servicios y en esas aplicaciones previamente integradas por Microsoft que admiten el aprovisionamiento automático de usuarios o SAML JIT.

## Aprovisionamiento automático de usuarios

Algunas aplicaciones proporcionan interfaces de automatización para la creación y eliminación (o desactivación) de cuentas. Si un proveedor ofrece esta interfaz, Azure Active Directory hace uso de ella. De esta manera, se reducen los costos operativos porque las tareas administrativas se producen automáticamente y se mejora la seguridad del entorno dado que disminuye la probabilidad de acceso no autorizado.

## Administración de acceso

Con Azure AD puede administrar el acceso a las aplicaciones mediante asignaciones basadas en reglas o individuales. También puede delegar la administración del acceso en las personas adecuadas de la organización, lo que garantiza una mejor supervisión y reduce la carga sobre el departamento de soporte técnico.

## Aplicaciones locales

El proxy de la aplicación integrado permite publicar las aplicaciones locales en los usuarios, lo que da lugar a una experiencia de usuario coherente con las aplicaciones modernas de nube, junto con las ventajas de las funcionalidades de supervisión, creación de informes y seguridad de Azure AD.

## Creación de informes y supervisión

Azure AD proporciona funcionalidades previamente integradas de supervisión y creación de informes que le permiten saber quién tiene acceso a las aplicaciones y cuándo se usan realmente.

## Funcionalidades relacionadas

Con Azure AD puede proteger las aplicaciones con directivas de acceso granular y MFA previamente integrada. Para obtener más información sobre Azure MFA, consulte [Azure MFA](https://azure.microsoft.com/services/multi-factor-authentication/).

## Introducción

Para comenzar a integrar las aplicaciones con Azure AD, eche un vistazo a la [Guía de introducción a la integración de Azure Active Directory con las aplicaciones](active-directory-integrating-applications-getting-started.md).

## Consulte también

[Índice de artículos sobre la administración de aplicaciones en Azure Active Directory](active-directory-apps-index.md)

<!---HONumber=AcomDC_0218_2016-->