<properties
	pageTitle="Tipos de punto de conexión v2.0 | Microsoft Azure"
	description="Tipos de aplicaciones y escenarios admitidos por el punto de conexión v2.0 de Azure AD."
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/20/2016"
	ms.author="dastrock"/>

# Tipos de punto de conexión v2.0
El punto de conexión v2.0 admite la autenticación de una variedad de arquitecturas de aplicaciones modernas, todas basadas en los protocolos estándar [OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow) o [OpenID Connect](active-directory-v2-protocols.md#openid-connect-sign-in-flow) del sector. Este documento describe brevemente los tipos de aplicaciones que puede crear, independientemente del lenguaje o la plataforma que prefiera. Le ayudará a comprender los escenarios de alto nivel antes de [introducirse en el código](active-directory-appmodel-v2-overview.md#getting-started).

> [AZURE.NOTE]
	No todas las características y escenarios de Azure Active Directory son compatibles con el punto de conexión v2.0. Para determinar si debe utilizar el punto de conexión v2.0, lea acerca de las [limitaciones de v2.0](active-directory-v2-limitations.md).

## Conceptos básicos
Todas las aplicaciones que utilicen el punto de conexión v2.0 tendrán que registrarse en [apps.dev.microsoft.com](https://apps.dev.microsoft.com). El proceso de registro de la aplicación recopilará y asignará algunos valores a la aplicación:

- Un **Id. de aplicación** que identifica de forma única su aplicación
- Un **URI de redireccionamiento** que puede utilizarse para dirigir las respuestas de nuevo a la aplicación
- Algunos otros valores específicos de cada escenario. Para más detalles, aprenda cómo [registrar una aplicación](active-directory-v2-app-registration.md).

Una vez registrada, la aplicación se comunica con Azure AD mediante el envío de solicitudes al punto de conexión v2.0 de Azure Active Directory: Proporcionamos marcos y bibliotecas de código abierto que se encargan de los detalles de estas solicitudes; sin embargo, también puede implementar la lógica de autenticación personalmente creando solicitudes para estos puntos de conexión:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```
<!-- TODO: Need a page for libraries to link to -->

## Aplicaciones web
En el caso de las aplicaciones web (.NET, PHP, Java, Ruby, Python, Node, etc.) a las que se accede a través de un explorador, puede realizar el inicio de sesión de los usuarios mediante [OpenID Connect](active-directory-v2-protocols.md#openid-connect-sign-in-flow). En OpenID Connect, la aplicación web recibe un `id_token`, un token de seguridad que comprueba la identidad del usuario y proporciona información sobre el usuario en forma de notificaciones:

```
// Partial raw id_token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded id_token
{
	"name": "John Smith",
	"email": "john.smith@gmail.com",
	"oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
	...
}
```

Puede obtener información sobre todos los tipos de token y notificaciones disponibles para una aplicación en la [referencia de token v2.0](active-directory-v2-tokens.md).

En las aplicaciones de servidor web, el flujo de autenticación de inicio de sesión realiza estos pasos de alto nivel:

![Imagen de las calles de la aplicación web](../media/active-directory-v2-flows/convergence_scenarios_webapp.png)

La validación del id\_token con una clave de firma pública recibida desde el extremo v2.0 es suficiente para garantizar la identidad del usuario y establecer una cookie de sesión que pueda utilizarse para identificar al usuario en las solicitudes de página posteriores.

Para ver este escenario en acción, pruebe uno de los ejemplos de código de inicio de sesión de aplicación web en nuestra sección [Introducción](active-directory-appmodel-v2-overview.md#getting-started).

Además de iniciar sesión simple, es posible que una aplicación de servidor web también necesite acceder a algún otro servicio web, como una API de REST. En este caso, la aplicación de servidor web puede participar en un flujo combinado de OpenID Connect y OAuth 2.0 , mediante el [flujo de código de autorización de OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow). Este escenario se explica más adelante en el [tema de introducción a WebApp-WebAPI](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md).

## API web
Puede utilizar el punto de conexión v2.0 para proteger también los servicios web, como la API web RESTful de su aplicación. En lugar de los id\_tokens y las cookies de sesión, las API web usan los access\_tokens de OAuth 2.0 para proteger sus datos y autenticar las solicitudes entrantes. El llamador de una API web anexa un access\_token en el encabezado de autorización de una solicitud HTTP:

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

La API web puede usar entonces el access\_token para comprobar la identidad del llamador de API y extraer información sobre el llamador de notificaciones que se codifican en el access\_token. Puede obtener información sobre todos los tipos de token y notificaciones disponibles para una aplicación en la [referencia de token v2.0](active-directory-v2-tokens.md).

Una API web puede ofrecer a los usuarios la capacidad para administrar la participación/no participación de ciertas funcionalidades o datos mediante la exposición de permisos, conocidos también como [ámbitos](active-directory-v2-scopes.md). Para que una aplicación de llamada adquiera permiso para un ámbito, el usuario debe consentir el ámbito durante un flujo. El extremo v2.0 se encargará de pedir permiso al usuario y de registrar esos permisos en todos los access\_tokens que recibe la API web. Lo único de lo que se tiene que encargar la API web es de validar el access\_tokens que recibe en cada llamada y de realizar las comprobaciones de autorización adecuadas.

Una API web puede recibir access\_tokens de todos los tipos de aplicaciones, incluidas las aplicaciones de servidor web, aplicaciones móviles y de escritorio, aplicaciones de una página, demonios del lado del servidor e incluso otras API web. El flujo de alto nivel para la autenticación de la API web es el siguiente:

![Imagen de las calles de la API web](../media/active-directory-v2-flows/convergence_scenarios_webapi.png)

Para más información sobre authorization\_codes, refresh\_tokens y los pasos detallados para obtener access\_tokens, lea sobre el [protocolo OAuth 2.0](active-directory-v2-protocols-oauth-code.md).

Para obtener información sobre cómo proteger una API web con los access\_tokens de OAuth2, consulte los ejemplos de código de las API web en nuestra [sección de introducción](active-directory-appmodel-v2-overview.md#getting-started).


## Aplicaciones móviles y nativas
Las aplicaciones instaladas en un dispositivo, como las aplicaciones móviles y de escritorio, suelen necesitar el acceso a servicios back-end o a las API web que almacenan datos y realizan varias funciones en nombre del usuario. Estas aplicaciones pueden agregar el inicio de sesión y la autorización a los servicios back-end mediante el [flujo de código de autorización de OAuth 2.0](active-directory-v2-protocols-oauth-code.md).

En este flujo, una la aplicación recibe un authorization\_code del extremo v2.0 tras el inicio de sesión del usuario, que representa el permiso de la aplicación para llamar a los servicios back-end en nombre del usuario que tiene la sesión iniciada actualmente. La aplicación podrá intercambiar el authoriztion\_code en segundo plano para un access\_token de OAuth 2.0 y un refresh\_token. La aplicación puede utilizar el access\_token para autenticar a la API web en las solicitudes HTTP y puede usar el refresh\_token para obtener access\_tokens nuevos cuando expiren los antiguos.

![Imagen de las calles de la aplicación nativa](../media/active-directory-v2-flows/convergence_scenarios_native.png)

## Aplicaciones de una sola página (Javascript)
Muchas aplicaciones modernas tienen un front-end de aplicación de una sola página escrito principalmente en Javascript que utiliza a menudo marcos como AngularJS, Ember.js, Durandal, etc. El punto de conexión v2.0 de Azure AD admite estas aplicaciones mediante el [flujo implícito de OAuth 2.0](active-directory-v2-protocols-implicit.md).

En este flujo, la aplicación recibe tokens del punto e conexión de autorización de v2.0 directamente, sin necesidad de realizar ningún intercambio de servidor back-end a servidor. Esto permite la completa implementación de la lógica de autenticación y el control de sesiones en el cliente de javascript, sin necesidad de realizar ninguna redirección de páginas adicional.

![Imagen de las calles de flujo implícito](../media/active-directory-v2-flows/convergence_scenarios_implicit.png)

Para ver este escenario en acción, pruebe uno de los ejemplos de código de aplicación de páginas único en nuestra sección [Introducción](active-directory-appmodel-v2-overview.md#getting-started).

## Limitaciones actuales
Estos tipos de aplicaciones no son compatibles actualmente con el punto de conexión v2.0, pero es algo que está en nuestra hoja de ruta. Las limitaciones y restricciones adicionales para el punto de conexión v2.0 se describen en el [artículo de limitaciones de v2.0](active-directory-v2-limitations.md).

### Aplicaciones de servidor o de demonio
Las aplicaciones que contienen procesos de larga duración o que funcionan sin la presencia de un usuario también necesitan un modo de acceder a los recursos protegidos, como las API web. Estas aplicaciones pueden autenticar y obtener tokens con la identidad de la aplicación (en lugar de una identidad delegada del usuario) mediante el flujo de credenciales de cliente de OAuth 2.0.

El flujo de credenciales de cliente no se admite actualmente en el punto de conexión v2.0. Para ver cómo funciona este flujo en el servicio Azure AD, disponible con carácter general, consulte el [ejemplo de código de demonio en GitHub](https://github.com/AzureADSamples/Daemon-DotNet).

### API web encadenadas (en nombre de)
Muchas arquitecturas incluyen una API web que necesita llamar a otra API web de nivel inferior, ambas protegidas mediante el punto de conexión v2.0. Este escenario es común en los clientes nativos que tienen una API web back-end, que, a su vez, llama a un servicio de Microsoft Online, como Office 365 o Graph API.

Este escenario de API web encadenada puede admitirse mediante la concesión de credenciales de portador Jwt de OAuth 2.0, también conocido como el [flujo "en nombre de"](active-directory-v2-protocols.md#oauth2-on-behalf-of-flow). Sin embargo, el flujo "en nombre de" no está implementado actualmente en el punto de conexión v2.0. Para ver cómo funciona este flujo en el servicio Azure AD, disponible con carácter general, consulte el [ejemplo de código "en nombre de" en GitHub](https://github.com/AzureADSamples/WebAPI-OnBehalfOf-DotNet).

<!---HONumber=AcomDC_0224_2016-->