<properties
    pageTitle="Integración del inicio de sesión único de Azure Active Directory con aplicaciones SaaS | Microsoft Azure"
    description="Habilite la autenticación de inicio de sesión único y la administración del acceso centralizado para el aprovisionamiento de usuarios de las aplicaciones SaaS en Azure Active Directory. Información general sobre cómo integrar Azure Active Directory en las aplicaciones SaaS."
    services="active-directory"
	  keywords="integrar Azure AD con aplicaciones SaaS"
    documentationCenter=""
    authors="curtand"
    manager="stevenpo"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="02/09/2016"
    ms.author="curtand"/>

# Integración del inicio de sesión único de Azure Active Directory con aplicaciones SaaS  

[AZURE.INCLUDE [active-directory-sso-use-case-intro](../../includes/active-directory-sso-use-case-intro.md)]

Para empezar a configurar el inicio de sesión único para una aplicación que traiga a su organización, usará un directorio existente en Azure Active Directory (Azure AD). Puede usar un directorio de Azure AD que obtenga a través de Microsoft Azure, Office 365 o Windows Intune. Si dispone de dos o más de estas aplicaciones, vea [Administración del directorio de Azure AD](active-directory-administer.md) para determinar cuál de ellas usar.

## Autenticación

Para las aplicaciones que admiten SAML 2.0, WS-Federation, o protocolos OpenID Connect, usa Azure Active Directory usa la firma de certificados para establecer relaciones de confianza. Para obtener más información, consulte [Administración de certificados para inicio de sesión único federado](active-directory-sso-certs.md).

Para las aplicaciones que admiten únicamente el inicio de sesión basado en formularios HTML, Azure Active Directory utiliza el 'almacén de contraseñas' para establecer relaciones de confianza. Permite a los usuarios de su organización iniciar la sesión automáticamente en una aplicación SaaS de terceros en Azure AD, usando la información de la cuenta del usuario de la aplicación SaaS. Azure AD recopila y almacena de forma segura la información de la cuenta del usuario y la contraseña correspondiente. Para obtener más información, consulte [Inicio de sesión único con contraseña](active-directory-appssoaccess-whatis.md#password-based-single-sign-on).

## Autorización

Una cuenta aprovisionada permite al usuario tener autorización para usar una aplicación, después de haberse autenticado a través de un inicio de sesión único. El aprovisionamiento de usuarios puede realizarse manualmente o, en algunos casos, puede agregar y quitar la información de usuario de la aplicación de SaaS basándose en los cambios realizados en Azure Active Directory. Para obtener más información sobre el uso de conectores de Azure AD existentes para el aprovisionamiento automatizado, consulte [Automatización del aprovisionamiento y desaprovisionamiento de usuarios para aplicaciones SaaS con Azure Active Directory](active-directory-saas-app-provisioning.md)

De lo contrario, puede agregar manualmente la información de usuario a una aplicación o usar otras soluciones de aprovisionamiento que están disponibles en Marketplace.

## Access

Azure AD proporciona varias maneras personalizables para implementar aplicaciones para los usuarios finales de su organización. No se bloquea en cualquier implementación concreta o solución de acceso. Puede usar [la solución que mejor se adapte a sus necesidades](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users).

## Consideraciones adicionales para las aplicaciones ya en uso

La configuración del inicio de sesión único para una aplicación que ya se usa en su organización es un proceso diferente al de crear nuevas cuentas para una nueva aplicación. Hay un par de pasos preliminares: asignar las identidades de usuario de la aplicación a las identidades de Azure AD y comprender cómo los usuarios experimentarán el inicio de sesión en una aplicación una vez que esté integrada.

> [AZURE.NOTE] Para configurar el inicio de sesión único para una aplicación existente, debe tener derechos de administrador global tanto en Azure AD como en la aplicación de SaaS.

### Asignación de cuentas de usuario

Normalmente, la identidad de un usuario tiene un identificador único que podría ser una dirección de correo electrónico o un nombre universal personal (UPN). Tendrá que vincular (asignar) la identidad de la aplicación de cada usuario a sus respectivas identidades de Azure AD. Hay dos maneras de lograrlo en función del requisito de autenticación de la aplicación.

Para obtener más información sobre la vinculación de identidades de la aplicación con identidades de Azure AD, consulte [Personalización de notificaciones emitidas en el token SAML](http://social.technet.microsoft.com/wiki/contents/articles/31257.azure-active-directory-customizing-claims-issued-in-the-saml-token-for-pre-integrated-apps.aspx) y [Personalización de asignaciones de atributos para aprovisionamiento](active-directory-saas-customizing-attribute-mappings.md).

### Descripción de la experiencia de inicio de sesión del usuario

Al integrar el inicio de sesión único para una aplicación que ya está en uso, es importante tener en cuenta que la experiencia del usuario se verá afectada. Para todas las aplicaciones, los usuarios comenzarán a usar sus credenciales de Azure AD para iniciar sesión. Además, pueden tener que usar otro portal para acceder a las aplicaciones.

El inicio de sesión único para algunas aplicaciones puede realizarse en la interfaz de inicio de sesión de la aplicación, pero para otras aplicaciones el usuario tendrá que obtener acceso a un portal de la aplicación central ([Mis aplicaciones](http://myapps.microsoft.com) u [Office365](http://portal.office.com/myapps)) para iniciar sesión. Obtenga más información acerca de los diferentes tipos de inicio de sesión único y sus experiencias de usuario en [Qué es el acceso a las aplicaciones y el inicio de sesión único con Azure Active Directory](active-directory-appssoaccess-whatis.md).

Vea también *Supresión del consentimiento del usuario* en el artículo [Guiar a los desarrolladores](active-directory-applications-guiding-developers-for-lob-applications.md).

## Pasos siguientes


Para las aplicaciones SaaS incluidas en la galería de aplicaciones, Azure AD ofrece una serie de [tutoriales sobre cómo integrarlas](active-directory-saas-tutorial-list.md).

Si la aplicación no está en la galería de aplicaciones, puede [agregarla a la galería de aplicaciones de Azure AD como una aplicación personalizada](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx).

Encontrará más información sobre todos estos problemas en la biblioteca de Azure.com, por ejemplo, [Qué es el acceso a las aplicaciones y el inicio de sesión único con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## Consulte también

- [Índice de artículos sobre la administración de aplicaciones en Azure Active Directory](active-directory-apps-index.md)

<!---HONumber=AcomDC_0211_2016-->