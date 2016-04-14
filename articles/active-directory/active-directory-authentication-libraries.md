<properties
   pageTitle="Bibliotecas de autenticación de Azure Active Directory | Microsoft Azure"
   description="La biblioteca de autenticación de Azure AD (ADAL) permite a los desarrolladores de aplicaciones cliente autenticar fácilmente a los usuarios en Active Directory (AD) local o en la nube y luego obtener tokens de acceso para proteger las llamadas de API."
   services="active-directory"
   documentationCenter=""
   authors="msmbaldwin"
   manager="mbaldwin"
   editor="mbaldwin" />
<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="02/25/2016"
   ms.author="mbaldwin" />

# Bibliotecas de autenticación de Azure Active Directory

La biblioteca de autenticación de Azure AD (ADAL) permite a los desarrolladores de aplicaciones cliente autenticar fácilmente a los usuarios en Active Directory (AD) local o en la nube y luego obtener tokens de acceso para proteger las llamadas de API. ADAL incluye muchas características que hacen que la autenticación resulte más fácil para los desarrolladores, como la compatibilidad asincrónica, una memoria caché de tokens configurable que almacena tokens de acceso y tokens de actualización, actualización automática de tokens cuando caduca un token de acceso y hay un token de actualización disponible, etc. Al controlar la mayor parte de la complejidad, ADAL puede ayudar a los desarrolladores a centrarse en la lógica de negocios de su aplicación y proteger recursos fácilmente sin ser un experto en seguridad.

ADAL está disponible en diversas plataformas.

|Plataforma|Nombre del paquete|Cliente/servidor|Descargar|Código fuente|Documentación y ejemplos|
|---|---|---|---|---|---|
|Cliente .NET Client, Tienda Windows, Windows Phone (8.1)|Biblioteca de autenticación de Active Directory (ADAL) para .NET|Cliente|[Microsoft.IdentityModel.Clients.ActiveDirectory (NuGet)](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory)|[ADAL para .NET (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet)|[Documentación](https://msdn.microsoft.com/library/azure/mt417579.aspx)|
|JavaScript|Biblioteca de autenticación de Active Directory (ADAL) para JavaScript|Cliente|[ADAL para JavaScript (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-js)|[ADAL para JavaScript (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-js)|Ejemplo: [SinglePageApp-DotNet (Github)](https://github.com/AzureADSamples/SinglePageApp-DotNet)|
|OS X, iOS|Biblioteca de autenticación de Active Directory (ADAL) para Objective-C|Cliente|[ADAL para Objective-C (CocoaPods)](https://cocoapods.org/?q=adal%20io)|[ADAL para Objective-C (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-objc)|Ejemplo: [NativeClient-iOS (Github)](https://github.com/AzureADSamples/NativeClient-iOS)|
|Android|Biblioteca de autenticación de Active Directory (ADAL) para Android|Cliente|[ADAL para Android (repositorio central)](http://search.maven.org/remotecontent?filepath=com/microsoft/aad/adal/)|[ADAL para Android (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-android)|Ejemplo: [NativeClient-Android (Github)](https://github.com/AzureADSamples/NativeClient-Android)|
|Node.js|Biblioteca de autenticación de Active Directory (ADAL) para Node.js|Cliente|[ADAL para Node.js (npm)](https://www.npmjs.com/package/adal-node)|[ADAL para Node.js (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-nodejs)|Ejemplo: [WebAPI-Nodejs (Github)](https://github.com/AzureADSamples/WebAPI-Nodejs)|
|Node.js|Middleware de autenticación Passport de Microsoft Azure Active Directory para Node|Cliente|[Azure Active Directory Passport para Node.js (npm)](https://www.npmjs.com/package/passport-azure-ad)|[Azure Active Directory para Node.js (Github)](https://github.com/AzureAD/passport-azure-ad)||
|Java|Biblioteca de autenticación de Active Directory (ADAL) para Java|Cliente|[ADAL para Java (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-java)|[ADAL para Java (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-java)||
|.NET|Extensiones de protocolo de identidad de Microsoft .NET Framework 4.5|Server|[Microsoft.IdentityModel.Protocol.Extensions (NuGet)](https://www.nuget.org/packages/Microsoft.IdentityModel.Protocol.Extensions)|[Extensiones de modelo de identidad de Azure AD para .NET (Github)](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet)||
|.NET|Controlador de token web JSON para Microsoft .Net Framework 4.5|Server|[System.IdentityModel.Tokens.Jwt (NuGet)](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt)|[Extensiones de modelo de identidad de Azure AD para .NET (Github)](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet)||
|.NET|Middleware OWIN que habilita una aplicación para que use la tecnología de Microsoft para la autenticación.|Server|[Microsoft.Owin.Security.ActiveDirectory (NuGet)](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/)|[OWIN (CodePlex)](http://katanaproject.codeplex.com)||
|.NET|Middleware OWIN que habilita una aplicación para que use OpenIDConnect para la autenticación.|Server|[Microsoft.Owin.Security.OpenIdConnect (NuGet)](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect)|[OWIN (CodePlex)](http://katanaproject.codeplex.com)|Ejemplo: [WebApp-OpenIDConnecty-DotNet (Github)](https://github.com/AzureADSamples/WebApp-OpenIDConnect-DotNet)|
|.NET|Middleware OWIN que habilita una aplicación para que use WS-Federation para la autenticación.|Server|[Microsoft.Owin.Security.WsFederation (NuGet)](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation)|[OWIN (CodePlex)](http://katanaproject.codeplex.com)|Ejemplo: [WebApp-WSFederation-DotNet (Github)](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet)|

## Escenarios

A continuación presentamos tres escenarios comunes en los que se puede usar ADAL para la autenticación.

### Autenticación de usuarios de una aplicación cliente en un recurso remoto

En este escenario, un desarrollador tiene un cliente, como una aplicación WPF, que necesita acceso a un recurso remoto protegido con Azure AD, como una API web. Tiene una suscripción de Azure, sabe cómo invocar la API web de bajada y conoce el inquilino de Azure AD que la API web usa. Por consiguiente, puede usar ADAL para facilitar la autenticación con Azure AD, ya sea delegando por completo la experiencia de autenticación a ADAL o controlando explícitamente las credenciales del usuario. ADAL hace que resulte fácil autenticar el usuario, obtener un token de acceso y un token de actualización de Azure AD y luego usar el token de acceso para realizar solicitudes a la API web.

Para un código de ejemplo que muestra este escenario con autenticación en Azure AD, vea [Aplicación WPF cliente nativa para API web](https://github.com/azureadsamples/nativeclient-dotnet).

### Autenticación de una aplicación de servidor en un recurso remoto

En este escenario, un desarrollador tiene una aplicación en ejecución en un servidor que necesita acceso a un recurso remoto protegido con Azure AD, como una API web. Tiene una suscripción de Azure, sabe cómo invocar el servicio de bajada y conoce el inquilino de Azure AD que la API web usa. Por consiguiente, puede usar ADAL para facilitar la autenticación con Azure AD controlando explícitamente las credenciales de la aplicación. ADAL hace que resulte fácil recuperar un token de Azure AD con las credenciales del cliente de la aplicación y luego usar ese token para realizar solicitudes a la API web. ADAL también controla la administración de la duración del token de acceso almacenándolo en la caché y renovándolo cuando sea necesario. Para un código de ejemplo que muestra este escenario, vea [Aplicación de consola para API web](https://github.com/AzureADSamples/Daemon-DotNet).

### Autenticación de una aplicación de servidor en nombre del usuario para acceso a un recurso remoto

En este escenario, un desarrollador tiene una aplicación en ejecución en un servidor que necesita acceso a un recurso remoto protegido con Azure AD, como una API web. La solicitud también debe realizarse en nombre del usuario en Azure AD. Tiene una suscripción de Azure, sabe cómo invocar la API web de bajada y conoce el inquilino de Azure AD que el servicio usa. Cuando el usuario se autentica en la aplicación web, la aplicación puede obtener un código de autorización para el usuario de Azure AD. La aplicación web puede luego usar ADAL para obtener un token de acceso y un token de actualización en nombre del usuario con las credenciales de cliente y el código de autorización asociados a la aplicación de Azure AD. Cuando la aplicación web está en posesión del token de acceso, puede llamar a la API web hasta que el token expire. Cuando el token expira, la aplicación web puede usar ADAL para obtener un nuevo token de acceso con el token de actualización que recibió anteriormente.


## Otras referencias

[Guía del desarrollador de Azure Active Directory](active-directory-developers-guide.md)

[Escenarios de autenticación para Azure Active Directory](active-directory-authentication-scenarios.md)

[Ejemplos de código de Azure Active Directory](active-directory-code-samples.md)

<!---HONumber=AcomDC_0302_2016-->