<properties
   pageTitle="Guía de introducción a la integración de Azure Active Directory con las aplicaciones | Microsoft Azure"
   description="Este artículo es una guía de introducción a la integración de Azure Active Directory (AD) con aplicaciones locales y de nube."
   services="active-directory"
   documentationCenter=""
   authors="ihenkel"
   manager="stevenpo"
   editor=""/>

   <tags
      ms.service="active-directory"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="identity"
      ms.date="10/16/2015"
      ms.author="inhenk"/>

# Guía de introducción a la integración de Azure Active Directory con las aplicaciones
## Información general
Este tema tiene como fin ofrecerle una guía básica para la integración de las aplicaciones con Azure Active Directory (AD). Cada una de las secciones siguientes contiene un breve resumen de un tema más detallado para que pueda identificar qué partes de esta guía de introducción son importantes para usted. Si desea profundizar más en algún tema, siga los vínculos.

## Antes de comenzar, realizar un inventario
Antes de pasar a la integración de aplicaciones con Azure AD, es importante saber dónde se encuentra y adónde quiere ir. Las siguientes preguntas están diseñadas para ayudarle a pensar en su proyecto de integración de aplicaciones de Azure AD.

### Inventario de aplicaciones
- ¿Dónde están todas las aplicaciones? ¿De quiénes son?
- ¿Qué tipo de autenticación necesitan las aplicaciones?
- ¿Quién necesita acceso a qué aplicaciones?
- ¿Quiere implementar una nueva aplicación?
  - ¿La integrará de forma interna y la implementará en una instancia de proceso de Azure?
  - ¿Usará una que esté disponible en la Galería de aplicaciones de Azure?

### Inventario de usuarios y grupos
- ¿Dónde residen las cuentas de usuario?
 - Active Directory local
 - Azure AD
 - Dentro de una base de datos de aplicaciones independiente de su propiedad
 - En aplicaciones no sancionadas
 - Todo lo anterior
- ¿Qué permisos y asignaciones de rol tienen actualmente los usuarios individuales? ¿Debe revisar su acceso o está seguro de que las asignaciones de acceso y rol de usuario son apropiadas ahora?
- ¿Ya están los grupos establecidos en su Active Directory local?
 - ¿Cómo están organizados los grupos?
 - ¿Quiénes son los miembros del grupo?
 - ¿Qué asignaciones de permisos y roles tienen actualmente los grupos?
- ¿Deberá limpiar las bases de datos de usuarios o grupos antes de la integración? (Esta es una pregunta bastante importante. Entrada y salida de elementos no utilizados).

### Inventario de administración de acceso
- ¿Cómo administra actualmente el acceso de los usuarios a las aplicaciones? ¿Es necesario cambiarlo? ¿Ha pensado en otras formas de administrar el acceso, como con [RBAC](role-based-access-control-configure.md) por ejemplo?
- ¿Quién necesita acceso a qué?

Quizás no tenga respuestas a todas estas preguntas por adelantado, pero no importa. Esta guía puede ayudarle a responder a algunas de esas preguntas y a tomar algunas decisiones informadas.

## Requisitos previos
- Una suscripción de Azure y un directorio de Azure Active Directory. Si todavía no dispone de una suscripción a Azure, puede probar Azure de forma gratuita durante 30 días. [pruébelo.](https://azure.microsoft.com/trial/get-started-active-directory/)

## Integración de aplicaciones con Azure AD
### Búsqueda de aplicaciones de nube no sancionadas con Cloud App Discovery
Como se mencionó anteriormente, puede haber aplicaciones que no han sido administradas por su organización hasta ahora. Como parte del proceso de inventario, es posible encontrar aplicaciones de nube no sancionadas. Consulte [Búsqueda de aplicaciones de nube no sancionadas con Cloud App Discovery](active-directory-cloudappdiscovery-whatis.md).

### Tipos de autenticación
Cada una de las aplicaciones puede tener requisitos de autenticación diferentes. Con Azure AD, pueden usar certificados de firma con aplicaciones que usan SAML 2.0, WS-Federation o protocolos de conexión OpenID, así como inicio de sesión único con contraseña. Para obtener más información sobre los tipos de autenticación de aplicaciones para su uso con Azure AD, consulte [Administración de certificados para federada el inicio de sesión único federado en Azure Active Directory](active-directory-sso-certs.md) e [Inicio de sesión único basado en contraseña](active-directory-appssoaccess-whatis.md).

### Habilitación de SSO con el proxy de la aplicación de Azure AD
Con el proxy de la aplicación de Microsoft Azure Active Directory, puede proporcionar acceso seguro a las aplicaciones que se encuentran dentro de la red privada, desde cualquier parte y en cualquier dispositivo. Después de instalar un conector de proxy de la aplicación en el entorno, se puede configurar fácilmente con Azure AD. Consulte [Habilitación de SSO con el proxy de la aplicación de Azure AD](active-directory-appssoaccess-enable-hybrid-access.md) y [Publicación de nuevas aplicaciones con el proxy de la aplicación de Azure AD](active-directory-application-proxy-configure.md).

### Integración de aplicaciones con Azure AD
Los siguientes artículos describen las distintas formas en que las aplicaciones se integran con Azure AD, y ofrecen alguna orientación.

- [Determinación de qué Active Directory usar](active-directory-administer.md)
- [Integración con aplicaciones existentes](active-directory-sso-integrate-existing-apps.md)
- [Publicación de nuevas aplicaciones con el proxy de la aplicación de Azure AD](active-directory-application-proxy-configure.md)
- [Uso de aplicaciones de la Galería de aplicaciones de Azure](active-directory-appssoaccess-whatis.md/#get-started-with-the-azure-ad-application-gallery.md)
- [Lista de tutoriales sobre integración de aplicaciones SaaS](active-directory-saas-tutorial-list.md)

## Administración del acceso a las aplicaciones
En los artículos siguientes se describen formas de administrar el acceso a las aplicaciones después de que se han integrado con Azure AD mediante conectores de Azure AD y Azure AD.

- [Administración del acceso a aplicaciones con Azure AD](active-directory-managing-access-to-apps.md)
- [Automatización con conectores de Azure AD](active-directory-saas-app-provisioning.md)
- [Asignación de usuarios a una aplicación](active-directory-applications-guiding-developers-assigning-users.md)
- [Asignación de grupos a una aplicación](active-directory-applications-guiding-developers-assigning-groups.md)
- [Uso compartido de cuentas](active-directory-sharing-accounts.md)

## Integración de aplicaciones personalizadas
Si va a crear una nueva aplicación y quiere ayudar a los desarrolladores a aprovechar la eficacia de Azure AD, consulte [Directrices para los desarrolladores](active-directory-applications-guiding-developers-for-lob-applications.md).

Si quiere agregar su aplicación personalizada a la Galería de aplicaciones de Azure, consulte ["Traiga su propia aplicación" con la configuración de SALM de autoservicio de Azure AD](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx).

<!---HONumber=AcomDC_0107_2016-->