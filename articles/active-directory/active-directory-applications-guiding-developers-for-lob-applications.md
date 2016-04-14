<properties
	pageTitle="Azure AD y aplicaciones: guiar a los desarrolladores | Microsoft Azure"
	description="Escrito para los profesionales de TI, este artículo ofrece instrucciones para integrar las aplicaciones de Azure con Active Directory."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/09/2016"
	ms.author="kgremban"/>

# Azure AD y aplicaciones: guía para los desarrolladores

## Información general

Esta guía proporciona información general sobre el desarrollo de aplicaciones de línea de negocio (LoB) para Azure Active Directory (AD) y ha sido escrita específicamente para administradores globales de Active Directory y Office 365.

La creación de aplicaciones integradas con Azure AD proporciona a los usuarios de la organización inicio de sesión único con Office 365. Disponer de la aplicación en Azure AD proporciona control sobre la directiva de autenticación establecida para la aplicación. Para obtener más información sobre el acceso condicional y cómo proteger las aplicaciones con autenticación multifactor (MFA), consulte el siguiente documento: [Configuración de las reglas de acceso](active-directory-conditional-access-azuread-connected-apps.md).

La aplicación necesita registrarse para poder utilizar Azure Active Directory. El registro de la aplicación permite a los desarrolladores de la organización autenticar a los miembros de dicha organización mediante Azure AD y solicitar acceso a sus recursos de usuario, como su correo electrónico, calendario, documentos, etc.

Cualquier miembro del directorio (no invitados) puede registrar una aplicación, conocido también como *crear un objeto de aplicación*.

El registro de una aplicación permite a cualquier usuario hacer lo siguiente:

- Obtener una identidad para su aplicación que Azure AD reconoce
- Obtener uno o más secretos o claves que la aplicación puede usar para autenticarse a sí misma en AD
- Identificar a la aplicación con un nombre, logotipo, etc. en el portal de Azure
- Aprovechar las características de autorización de Azure AD para las aplicaciones
  - Control de acceso basado en rol de la aplicación (RBAC)
  - Azure Active Directory como servidor de autorización de OAuth (proteger una API expuesta por la aplicación)

- Declarar permisos requeridos necesarios para que la aplicación funcionando según lo previsto. Entre ellos se incluyen los siguientes:
	  - Permisos de la aplicación (solo administradores globales). Por ejemplo:
	    - Pertenencia de rol en otra aplicación de Azure AD o pertenencia de rol respecto a un recurso de Azure, grupo de recursos o suscripción
	  - Permisos delegados (cualquier usuario) Por ejemplo:
	    - (Azure AD) Perfil de lectura e inicio de sesión
	    - (Exchange) Leer correo y enviar correo
	    - (SharePoint) Leer

> [AZURE.NOTE]De forma predeterminada, cualquier miembro puede registrar una aplicación. Para obtener información sobre cómo restringir permisos para el registro de aplicaciones a miembros específicos, consulte el documento [Cómo se agregan aplicaciones a Azure AD](active-directory-how-applications-are-added.md#who-has-permission-to-add-applications-to-my-azure-ad-instance).

A continuación se indica lo que usted, el administrador global, necesitará hacer para ayudar a los desarrolladores a que su aplicación esté lista para la producción:

- Configurar reglas de acceso (directiva de acceso/MFA)
- Configurar la aplicación para requerir asignación de usuarios y asignar usuarios
- Suprimir la experiencia de consentimiento del usuario predeterminada

## Configuración de las reglas de acceso

Como mencionamos anteriormente, consulte el siguiente artículo para obtener más información sobre la configuración de reglas de acceso para cualquier aplicación.

[Configuración de las reglas de acceso](active-directory-conditional-access-azuread-connected-apps.md)

## Configurar la aplicación para requerir asignación de usuarios y asignar usuarios

De forma predeterminada, no se requiere la asignación de usuarios para que estos puedan acceder a una aplicación. Sin embargo, si la aplicación expone roles o si desea que la aplicación aparezca en el panel de acceso de un usuario, debe requerir la asignación de usuarios y asignar usuarios y/o grupos.

[Necesidad de asignación de usuarios](active-directory-applications-guiding-developers-requiring-user-assignment.md)

Si es un suscriptor de Azure AD Premium o Enterprise Mobility Suite (EMS), se recomienda aprovechar los grupos. La asignación de grupos a la aplicación permite delegar la administración del acceso continuo al propietario del grupo. Puede crear el grupo o pedir a la parte responsable de la organización que lo haga usando sus recursos de administración de grupos.

[Asignación de usuarios a una aplicación](active-directory-applications-guiding-developers-assigning-users.md) [Asignación de grupos a una aplicación](active-directory-applications-guiding-developers-assigning-groups.md)

## Supresión del consentimiento del usuario

De forma predeterminada, el usuario deberá consentir el permiso necesario para iniciar sesión. La experiencia de consentimiento, que se solicita para conceder permisos a una aplicación, puede ser desconcertante para los usuarios que no estén familiarizados con la necesidad de tomar una decisión de este tipo.

Para las aplicaciones de confianza, puede dar su consentimiento a la aplicación en nombre de todos los usuarios de la organización.

Para obtener más información sobre el consentimiento del usuario y la experiencia de consentimiento en Azure, consulte [Integración de aplicaciones con Azure Active Directory](active-directory-integrating-applications.md).

##Artículos relacionados

- [Índice de artículos sobre la administración de aplicaciones en Azure Active Directory](active-directory-apps-index.md)

<!---HONumber=AcomDC_0211_2016-->