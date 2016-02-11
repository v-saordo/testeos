<properties
	pageTitle="Tipos de aplicaciones del modelo de aplicación v2.0 | Microsoft Azure"
	description="Los tipos de aplicaciones y escenarios admitidos por la vista previa pública del modelo de aplicaciones v2.0 de Azure AD."
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
	ms.date="12/09/2015"
	ms.author="dastrock"/>

# Vista previa del modelo de aplicaciones v2.0: tipos de aplicaciones
El modelo de aplicaciones v2.0 admite la autenticación de una variedad de arquitecturas de aplicaciones modernas, todas basadas en protocolos estándar del sector [OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow) y/o [OpenID Connect](active-directory-v2-protocols.md#openid-connect-sign-in-flow). Este documento describe brevemente los tipos de aplicaciones que puede crear, independientemente del lenguaje o la plataforma que prefiera. Le ayudará a comprender los escenarios de alto nivel antes de [introducirse en el código](active-directory-appmodel-v2-overview.md#getting-started).

> [AZURE.NOTE]Esta información se aplica a la vista previa pública del modelo de aplicaciones v2.0. Para obtener instrucciones sobre cómo integrarse en el servicio de Azure AD, disponible con carácter general, consulte la [Guía para desarrolladores de Azure Active Directory](active-directory-developers-guide.md).

## Conceptos básicos
Todas las aplicaciones que utilicen el modelo de aplicaciones v2.0 tendrán que registrarse en [apps.dev.microsoft.com](https://apps.dev.microsoft.com). El proceso de registro de la aplicación recopilará y asignará algunos valores a la aplicación:

- Un **Id. de aplicación** que identifica de forma única su aplicación
- Un **URI de redireccionamiento** que puede utilizarse para dirigir las respuestas de nuevo a la aplicación
- Algunos otros valores específicos de cada escenario. Para obtener más detalles, aprenda cómo [registrar una aplicación](active-directory-v2-app-registration.md).

Una vez registrada, la aplicación le comunica a Azure AD mis solicitudes de envío al extremo v2.0:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

La interacción de cada aplicación con el extremo v2.0 sigue un patrón similar de nivel alto:

1. La aplicación dirige al usuario final al extremo v2.0 para iniciar sesión.
2. El usuario se autentica escribiendo su nombre de usuario y contraseña o de otro modo.
3. El usuario autoriza la aplicación para que actúe en su nombre, concediendo los permisos que solicita la aplicación.
4. La aplicación recibe un token de seguridad de algún tipo a partir del extremo v2.0.
5. La aplicación utiliza el token de seguridad para acceder a información protegida o a un recurso.
6. El servidor de recursos valida el token de seguridad para asegurarse de que se puede conceder acceso.
7. La aplicación actualiza periódicamente el token de seguridad.

<!-- TODO: Need a page for libraries to link to -->
Cada uno de estos siete pasos variarán ligeramente en función del tipo de aplicación que está creando; tenemos bibliotecas de código abierto que se ocupan de los detalles por usted. Puede obtener más información sobre [permisos, consentimiento y ámbitos](active-directory-v2-scopes.md) o leer para explorar algunos ejemplos concretos.

## Aplicaciones web
Para las aplicaciones web (.NET, PHP, Java, Ruby, Python, Node, etc.) a las que se accede a través de un explorador, el modelo de aplicaciones v2.0 admite el inicio de sesión de usuarios mediante[OpenID Connect](active-directory-v2-protocols.md#openid-connect-sign-in-flow). En OpenID Connect, la aplicación web recibe un `id_token`, un token de seguridad que comprueba la identidad del usuario y proporciona información sobre el usuario en forma de notificaciones:

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

Además de iniciar sesión simple, es posible que una aplicación de servidor web también necesite acceder a algún otro servicio web, como una API de REST. En este caso, la aplicación de servidor web puede participar en un flujo combinado de OpenID Connect y OAuth 2.0 , mediante el [flujo de código de autorización de OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow). Este escenario se explica más adelante en la [sección API web](#web-apis) y en nuestro [tema de introducción a WebApp-WebAPI](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md).

## API web
También puede utilizar el modelo de aplicaciones v2.0 para proteger los servicios web, como la API web RESTful de su aplicación. En lugar de los id\_tokens y las cookies de sesión, las API web usan los access\_tokens de OAuth 2.0 para proteger sus datos y autenticar las solicitudes entrantes. El llamador de una API web anexa un access\_token en el encabezado de autorización de una solicitud HTTP:

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

La API web puede usar entonces el access\_token para comprobar la identidad del llamador de API y extraer información sobre el llamador de notificaciones que se codifican en el access\_token. Puede obtener información sobre todos los tipos de token y notificaciones disponibles para una aplicación en la [referencia de token v2.0](active-directory-v2-tokens.md).

Una API web puede ofrecer a los usuarios la capacidad para administrar la participación/no participación de ciertas funcionalidades o datos mediante la exposición de permisos, conocidos también como [ámbitos](active-directory-v2-scopes.md). Para que una aplicación de llamada adquiera permiso para un ámbito, el usuario debe consentir el ámbito durante un flujo. El extremo v2.0 se encargará de pedir permiso al usuario y de registrar esos permisos en todos los access\_tokens que recibe la API web. Lo único de lo que se tiene que encargar la API web es de validar el access\_tokens que recibe en cada llamada y de realizar las comprobaciones de autorización adecuadas.

Una API web puede recibir access\_tokens de todos los tipos de aplicaciones, incluidas las aplicaciones de servidor web, aplicaciones móviles y de escritorio, aplicaciones de una página, demonios del lado del servidor e incluso otras API web. Por ejemplo, veamos el flujo completo de una aplicación de servidor web que llama a una API web.

![Imagen de las calles de la API web de la aplicación web](../media/active-directory-v2-flows/convergence_scenarios_webapp_webapi.png)

Para más información sobre authorization\_codes, refresh\_tokens y los pasos detallados para obtener access\_tokens, lea sobre el [protocolo OAuth 2.0](active-directory-v2-protocols-oauth-code.md).

Para obtener información sobre cómo proteger una API web con el modelo de aplicaciones v2.0 y los access\_tokens de OAuth 2.0, consulte los ejemplos de código de las API web en nuestra [sección Introducción](active-directory-appmodel-v2-overview.md#getting-started).


## Aplicaciones móviles y nativas
Las aplicaciones instaladas en un dispositivo, como las aplicaciones móviles y de escritorio, suelen necesitar el acceso a servicios back-end o a las API web que almacenan datos y realizan varias funciones en nombre del usuario. Estas aplicaciones pueden agregar el inicio de sesión y la autorización a los servicios back-end mediante el modelo v2.0 y el [flujo de código de autorización de OAuth 2.0](active-directory-v2-protocols-oauth-code.md).

En este flujo, una la aplicación recibe un authorization\_code del extremo v2.0 tras el inicio de sesión del usuario, que representa el permiso de la aplicación para llamar a los servicios back-end en nombre del usuario que tiene la sesión iniciada actualmente. La aplicación podrá intercambiar el authoriztion\_code en segundo plano para un access\_token de OAuth 2.0 y un refresh\_token. La aplicación puede utilizar el access\_token para autenticar a la API web en las solicitudes HTTP y puede usar el refresh\_token para obtener access\_tokens nuevos cuando expiren los antiguos.

![Imagen de las calles de la aplicación nativa](../media/active-directory-v2-flows/convergence_scenarios_native.png)

## Aplicaciones de una página (Javascript)
Muchas aplicaciones modernas tienen una aplicación de una página escrita en front-end, principalmente en javascript, que usa a menudo marcos SPA como AngularJS, Ember.js, Durandal, etc. El modelo de aplicación de Azure AD v2.0 admite estas aplicaciones mediante el [flujo implícito de OAuth 2.0](active-directory-v2-protocols-implicit.md).

En este flujo, la aplicación recibe tokens del punto e conexión de autorización de v2.0 directamente, sin necesidad de realizar ningún intercambio de servidor back-end a servidor. Esto permite la completa implementación de la lógica de autenticación y el control de sesiones en el cliente de javascript, sin necesidad de realizar ninguna redirección de páginas adicional.

Para ver este escenario en acción, pruebe uno de los ejemplos de código de aplicación de páginas único en nuestra sección [Introducción](active-directory-appmodel-v2-overview.md#getting-started).

## Limitaciones de la vista previa actual
Estos tipos de aplicaciones no son compatibles actualmente con la vista previa del modelo de aplicaciones v2.0, pero está previsto que sean compatibles a tiempo para su disponibilidad con carácter general. Las limitaciones y restricciones adicionales para la vista preliminar pública del modelo de aplicaciones v2.0 se describen en el [artículo de limitaciones de la vista preliminar v2.0](active-directory-v2-limitations.md).

### Demonios/aplicaciones del lado del servidor
Las aplicaciones que contienen procesos de larga duración o que funcionan sin la presencia de un usuario también necesitan un modo de acceder a los recursos protegidos, como las API web. Estas aplicaciones pueden autenticar y obtener tokens con la identidad de la aplicación (en lugar de una identidad delegada del usuario) mediante el [flujo de credenciales de cliente de OAuth 2.0](active-directory-v2-protocols.md#oauth2-client-credentials-grant-flow).

Este flujo no es compatible actualmente con el modelo de aplicaciones v2.0, lo que quiere decir que las aplicaciones solo pueden obtener tokens una vez se haya producido un flujo de inicio de sesión de un usuario interactivo. El flujo de credenciales de cliente se agregará en un futuro próximo. Si quiere ver el flujo de credenciales de cliente en el servicio Azure AD, disponible con carácter general, vea el [ejemplo de demonio en GitHub](https://github.com/AzureADSamples/Daemon-DotNet).

### API web encadenadas (en nombre de)
Muchas arquitecturas incluyen una API web que necesita llamar a otra API web de nivel inferior, ambas protegidas mediante el modelo de aplicaciones v2.0. Este escenario es común en los clientes nativos que tienen una API web back-end, que, a su vez, llama a un servicio de Microsoft Online, como Office 365 o Graph API.

Este escenario de API web encadenada puede admitirse mediante la concesión de credenciales de portador Jwt de OAuth 2.0, también conocido como el [flujo "en nombre de"](active-directory-v2-protocols.md#oauth2-on-behalf-of-flow). Sin embargo, el flujo "en nombre de" no está implementado actualmente en la vista previa del modelo de aplicaciones v2.0. Para ver cómo funciona este flujo en el servicio Azure AD, disponible con carácter general, vea el [ejemplo de código "en nombre de" en GitHub](https://github.com/AzureADSamples/WebAPI-OnBehalfOf-DotNet).

<!---HONumber=AcomDC_1217_2015-->