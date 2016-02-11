<properties
   pageTitle="Protocolos de autenticación de Azure Active Directory | Microsoft Azure"
   description="Información general sobre los protocolos de autenticación admitidos por Azure Active Directory (AD)."
   documentationCenter="dev-center-name"
   authors="msmbaldwin"
   services="active-directory"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="01/08/2016"
   ms.author="mbaldwin"/>

# Protocolos de autenticación de Azure Active Directory

Azure Active Directory (Azure AD) admite varios de los protocolos de autenticación y autorización utilizados más comúnmente. En los temas de esta sección se describen los protocolos admitidos y su implementación en Azure AD. En los temas se incluye una revisión de los tipos de notificación compatibles, una introducción al uso de los metadatos de federación, documentación de referencia detallada sobre los protocolos OAuth 2.0. y SAML 2.0 y una sección de solución de problemas.

## Artículos y referencia de protocolos de autenticación

- [Información importante acerca de la cadencia de sustitución de clave en Azure AD](https://msdn.microsoft.com/library/azure/dn641920.aspx): obtenga información sobre la cadencia de sustitución de clave de firma de Azure AD, los cambios que puede realizar para actualizar la clave automáticamente y una explicación sobre cómo actualizar los escenarios de aplicación más comunes.


- [Tipos de token y notificación compatibles](active-directory-token-and-claims.md): obtenga información sobre las notificaciones en los tokens que emite Azure AD.


- [Metadatos de federación](https://msdn.microsoft.com/library/azure/dn195592.aspx): obtenga información sobre cómo buscar e interpretar los documentos de metadatos que Azure AD genera.


- [OAuth 2.0 en Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx): obtenga información sobre la implementación de OAuth 2.0 en Azure AD.


- [OpenID Connect 1.0](https://msdn.microsoft.com/library/azure/dn645541.aspx): obtenga información sobre cómo usar el protocolo de autorización OAuth 2.0 para la autenticación.


- [Referencia del protocolo SAML](https://msdn.microsoft.com/library/azure/dn195591.aspx): obtenga información sobre los perfiles SAML de inicio de sesión único y de cierre de sesión único de Azure AD.


- [WS-Federation 1.2](https://msdn.microsoft.com/library/azure/dn903702.aspx): obtenga información acerca de WS-Federation 1.2 en Azure AD.


- [Solución de problemas de protocolos de autenticación](https://msdn.microsoft.com/library/azure/dn195584.aspx): obtenga información para evitar problemas e interpretar y solucionar errores al usar Azure AD.



## Otras referencias

[Guía del desarrollador de Azure Active Directory](active-directory-developers-guide.md)

[Uso de Azure AD para la autenticación](../app-service-web/web-sites-authentication-authorization.md)

[Ejemplos de código de Azure Active Directory](active-directory-code-samples.md)

<!---HONumber=AcomDC_0114_2016-->