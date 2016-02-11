<properties
   pageTitle="Guía del desarrollador de Azure Active Directory | Microsoft Azure"
   description="Este artículo ofrece una guía completa sobre recursos orientados al desarrollador para Azure Active Directory."
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="msmbaldwin"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="01/06/2016"
   ms.author="mbaldwin"/>


# Guía del desarrollador de Azure Active Directory

## Información general
Al ser una plataforma de administración de identidades como servicio (IDMaaS), Azure Active Directory proporciona a los desarrolladores una manera eficaz de integrar la administración de identidades en sus aplicaciones. Los siguientes artículos ofrecen información general sobre la implementación y las características clave de Azure Active Directory. Se recomienda leerlos por orden o, si desea profundizar en el tema, ir directamente a [Introducción](#getting-started).


1. [Beneficios de la integración con Azure Active Directory](active-directory-how-to-integrate.md): descubra por qué la integración con Azure Active Directory ofrece la mejor solución para lograr una autorización y un inicio de sesión seguros.

1. [Escenarios de autenticación de Active Directory](active-directory-authentication-scenarios.md): aproveche la autenticación simplificada de Azure Active Directory para proporcionar inicio de sesión en la aplicación.

1. [Integración de las aplicaciones con Azure Active Directory](active-directory-integrating-applications.md): obtenga información sobre cómo agregar, actualizar y quitar aplicaciones de Azure Active Directory, y acerca de las directrices de personalización de marca para aplicaciones integradas.

1. [API Graph de Azure Active Directory](active-directory-graph-api.md): use la API Graph de Azure Active Directory para acceder mediante programación a Azure Active Directory a través de extremos de la API de REST. Tenga en cuenta que la API Graph de Azure AD también es accesible a través de [Microsoft Graph](https://graph.microsoft.io/), una API unificada que permite el acceso a varias API del servicio de Microsoft Cloud a través de un punto de conexión de API de REST único y con un token de acceso único.

1. [Bibliotecas de autenticación de Azure Active Directory](active-directory-authentication-libraries.md): autentique a los usuarios fácilmente para obtener tokens de acceso mediante las bibliotecas de autenticación de Azure para .NET, JavaScript, Objective-C, Android, etc.


## Introducción

Estos tutoriales están adaptados para varias plataformas y pueden ayudarle a empezar a desarrollar rápidamente con Azure Active Directory. Como requisito previo, debe [obtener un inquilino de Azure Active Directory](active-directory-howto-tenant.md).

### Guías de inicio rápido de aplicaciones para móvil y PC

|[![iOS](./media/active-directory-developers-guide/ios.png)](active-directory-devquickstarts-ios.md)|[![Android](./media/active-directory-developers-guide/android.png)](active-directory-devquickstarts-android.md)|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-dotnet.md)| [![Windows Phone](./media/active-directory-developers-guide/windows.png)](active-directory-devquickstarts-windowsphone.md)|[![Tienda Windows](./media/active-directory-developers-guide/windows.png)](active-directory-devquickstarts-windowsstore.md)|[![Xamarin](./media/active-directory-developers-guide/xamarin.png)](active-directory-devquickstarts-xamarin.md)|[![Cordova](./media/active-directory-developers-guide/cordova.png)](active-directory-devquickstarts-cordova.md)
|:--:|:--:|:--:|:--:|:--:|:--:|:--:
|[iOS](active-directory-devquickstarts-ios.md)|[Android](active-directory-devquickstarts-android.md)|[.NET](active-directory-devquickstarts-dotnet.md)|[Windows Phone](active-directory-devquickstarts-windowsphone.md)|[Tienda Windows](active-directory-devquickstarts-windowsstore.md)|[Xamarin](active-directory-devquickstarts-xamarin.md)|[Cordova](active-directory-devquickstarts-cordova.md)

### Guías de inicio rápido de aplicaciones web

|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-webapp-dotnet.md)|[![Java](./media/active-directory-developers-guide/java.png)](active-directory-devquickstarts-webapp-java.md)|[![Javascript](./media/active-directory-developers-guide/javascript.png)](active-directory-devquickstarts-angular.md)|[![Node.js](./media/active-directory-developers-guide/nodejs.png)](active-directory-devquickstarts-openidconnect-nodejs.md)
|:--:|:--:|:--:|:--:|
|[.NET](active-directory-devquickstarts-webapp-dotnet.md)|[Java](active-directory-devquickstarts-webapp-java.md)|[Javascript](active-directory-devquickstarts-angular.md)|[Node.js](active-directory-devquickstarts-openidconnect-nodejs.md)

### Guías de inicio rápido de API web

|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-webapi-dotnet.md)|[![Node.js](./media/active-directory-developers-guide/nodejs.png)](active-directory-devquickstarts-webapi-nodejs.md)
|:--:|:--:|
|[.NET](active-directory-devquickstarts-webapi-dotnet.md)|[Node.js](active-directory-devquickstarts-webapi-nodejs.md)

### Consultas a la guía de inicio rápido de directorio

| [![.NET](./media/active-directory-developers-guide/graph.png)](active-directory-graph-api-quickstart.md)|
|:--:|
|[API Graph](active-directory-graph-api-quickstart.md)|

## Procedimientos

En estos artículos se describe cómo realizar tareas específicas con Azure Active Directory:

- [Obtención de un inquilino de Azure Active Directory](active-directory-howto-tenant.md)
- [Visualización de la aplicación en la galería de aplicaciones de Azure Active Directory](active-directory-app-gallery-listing.md)
- [Descripción del manifiesto de aplicación de Azure Active Directory](active-directory-application-manifest.md)
- [Creación de una aplicación con las API de Office 365](https://msdn.microsoft.com/office/office365/howto/getting-started-Office-365-APIs)
- [Envío de aplicaciones web para Office 365 al panel de vendedores](https://msdn.microsoft.com/office/office365/howto/submit-web-apps-seller-dashboard)
- [Vista previa: cómo compilar aplicaciones que inician sesión para los usuarios tanto con cuentas personales como profesionales o educativas](active-directory-appmodel-v2-overview.md)
- [Vista previa: Creación de aplicaciones que registren e inicien la sesión de los consumidores](active-directory-b2c-overview.md)


## Referencia

Estos artículos proporcionan información básica sobre las API de REST y de la biblioteca de autenticación, los protocolos, los errores, los ejemplos de código y los extremos.

###  Soporte técnico
- [Preguntas con etiquetas](http://stackoverflow.com/questions/tagged/azure-active-directory): encuentre soluciones de Azure Active Directory en el sitio de Stack Overflow buscando las etiquetas [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) y [adal](http://stackoverflow.com/questions/tagged/adal).

### Código

- [Bibliotecas de código abierto de Azure Active Directory](http://github.com/AzureAD): la manera más fácil de encontrar el código fuente de una biblioteca es usar la [lista de bibliotecas](active-directory-authentication-libraries.md).

- [Ejemplos de Azure Active Directory](https://github.com/azure-samples?query=active-directory): la forma más sencilla de navegar por la lista de ejemplos es usar el [índice de ejemplos de código](active-directory-code-samples.md).


### API Graph

- [Referencia de API Graph](https://msdn.microsoft.com/library/azure/hh974476.aspx): referencia de REST para la API Graph de Azure Active Directory. [Vea la experiencia con la referencia interactiva de API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

- [Ámbitos de los permisos de API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes): ámbitos de los permisos de OAuth 2.0 que sirven para controlar el acceso que una aplicación tiene a los datos de directorio en un inquilino.

### Bibliotecas de autenticación

- [.NET](https://msdn.microsoft.com/library/azure/mt417579.aspx): documentación de la biblioteca de autenticación. NET.

### Protocolos de autenticación

- [Referencia del protocolo SAML 2.0](https://msdn.microsoft.com/library/azure/dn195591.aspx): el protocolo SAML 2.0 permite que las aplicaciones ofrezcan una experiencia de inicio de sesión único a sus usuarios.


- [Referencia del protocolo OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx): puede usar el protocolo OAuth 2.0 para autorizar el acceso a las aplicaciones web y las API web en su inquilino de Azure Active Directory.


- [Referencia del protocolo OpenID Connect 1.0](https://msdn.microsoft.com/library/azure/dn645541.aspx): el protocolo OpenID Connect 1.0 amplía OAuth 2.0 para su uso como protocolo de autenticación.


- [Referencia del protocolo WS-Federation 1.2](https://msdn.microsoft.com/library/azure/dn903702.aspx): el protocolo WS-Federation 1.2 se describe en la especificación de Web Services Federation, versión 1.2.

- [Tipos de token y de notificación admitidos](active-directory-token-and-claims.md): puede usar esta guía para entender y evaluar las notificaciones en los tokens web JSON (JWT) y SAML 2.0.

## Vídeos

### Build 2015

Los ponentes de estas presentaciones de información general sobre el desarrollo de aplicaciones con características de Azure Active Directory trabajan directamente en el equipo de ingeniería. Las presentaciones tratan temas fundamentales, entre los que se incluyen IDMaaS, autenticación, federación de identidades e inicio de sesión único.

- [Azure Active Directory: Administración de identidades como servicio para aplicaciones modernas](https://azure.microsoft.com/documentation/videos/build-2015-azure-active-directory-identity-management-as-a-service-for-modern-applications/)
- [Desarrollo de aplicaciones web modernas con Azure Active Directory](https://azure.microsoft.com/documentation/videos/build-2015-develop-modern-web-applications-with-azure-active-directory/)
- [Desarrollo de aplicaciones nativas modernas con Azure Active Directory](https://azure.microsoft.com/documentation/videos/build-2015-develop-modern-native-applications-with-azure-active-directory/)

### Azure Friday
[Azure Friday](https://azure.microsoft.com/documentation/videos/azure-friday/) es una serie periódica de vídeos de 1:1 que se publica los viernes y ofrece entrevistas breves (de 10 a 15 minutos) a expertos en diversos temas de Azure. Utilice la característica de filtro de servicios de la página para ver todos los vídeos de Azure Active Directory.

- [Identidad de Azure 101](https://azure.microsoft.com/documentation/videos/azure-identity-basics/)
- [Identidad de Azure 102](https://azure.microsoft.com/documentation/videos/azure-identity-creating-active-directory/)
- [Identidad de Azure 103](https://azure.microsoft.com/documentation/videos/azure-identity-application-to-authenticate/)

## Redes sociales

- [Blog del equipo de Active Directory](http://blogs.technet.com/b/ad/): manténgase al tanto de los últimos avances en el mundo de Azure Active Directory.

- [Blog del equipo de Azure Active Directory Graph](http://blogs.msdn.com/b/aadgraphteam): información de Azure Active Directory específica de la API Graph.

- [Identidad en la nube](http://www.cloudidentity.net): reflexiones de un PM de Azure Active Directory sobre la administración de identidades como servicio.

- [Azure Active Directory en Twitter](https://twitter.com/azuread): anuncios de Azure Active Directory en 140 caracteres o menos.

<!---HONumber=AcomDC_0128_2016-->