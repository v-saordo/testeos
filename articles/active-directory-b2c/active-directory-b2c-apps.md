<properties
	pageTitle="Vista previa de Azure AD B2C | Microsoft Azure"
	description="Los tipos de aplicaciones que puede crear en la vista previa de Azure AD B2C."
	services="active-directory-b2c"
	documentationCenter=""
	authors="dstrockis"
	manager="msmbaldwin"
	editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/22/2015"
	ms.author="dastrock"/>

# Vista previa de Azure AD B2C: Tipos de aplicaciones

Azure AD B2C admite la autenticación de una gran variedad de arquitecturas de aplicaciones modernas, todas basadas en protocolos estándar del sector [OAuth 2.0](active-directory-b2c-reference-protocols.md) y/o [OpenID Connect](active-directory-b2c-reference-protocols.md). Este documento describe brevemente los tipos de aplicaciones que puede crear, independientemente del lenguaje o la plataforma que prefiera. Le ayudará a comprender los escenarios de alto nivel antes de [incorporarse al código](active-directory-b2c-overview.md#getting-started).

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Conceptos básicos
Cada aplicación que usa Azure AD B2C deberá estar registrada en su [directorio B2C](active-directory-b2c-get-started.md) mediante el [Portal de Azure](https://portal.azure.com/). El proceso de registro de la aplicación recopilará y asignará algunos valores a la aplicación:

- Un **Id. de aplicación** que identifica de forma única su aplicación
- Un **URI de redireccionamiento** que puede utilizarse para dirigir las respuestas de nuevo a la aplicación
- Algunos otros valores específicos de cada escenario. Para obtener más detalles, aprenda cómo [registrar una aplicación](active-directory-b2c-app-registration.md).

Una vez registrada, la aplicación se comunica con Azure AD mediante el envío de solicitudes al extremo v2.0 de Azure AD:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

En cada solicitud que se envía a Azure AD B2C se especifica una **directiva**. Una directiva controla el comportamiento de Azure AD y permite usar estos extremos para crear un conjunto muy personalizable de experiencias de usuario. Algunas directivas comunes incluyen directivas de registro, directivas de inicio de sesión y directivas de edición de perfiles. Si no está familiarizado con las directivas, debe conocer el [marco extensible de directivas](active-directory-b2c-reference-policies.md) de Azure AD B2C antes de seguir con la lectura.

La interacción de cada aplicación con el extremo v2.0 sigue un patrón similar de nivel alto:

1. La aplicación dirige al usuario final al extremo v2.0 para ejecutar una [directiva](active-directory-b2c-reference-policies.md).
2. El usuario completa la directiva según la definición de la misma.
4. La aplicación recibe un token de seguridad de algún tipo a partir del extremo v2.0.
5. La aplicación utiliza el token de seguridad para acceder a información protegida o a un recurso.
6. El servidor de recursos valida el token de seguridad para asegurarse de que se puede conceder acceso.
7. La aplicación actualiza periódicamente el token de seguridad.

<!-- TODO: Need a page for libraries to link to -->
Cada uno de estos pasos variará ligeramente en función del tipo de aplicación que está creando; tenemos bibliotecas de código abierto que se ocupan de los detalles por usted.

## Aplicaciones web
Para las aplicaciones web (. NET, PHP, Java, Ruby, Python, Node, etc.) que se hospedan en un servidor y a las que se tiene acceso a través de un explorador, Azure AD B2C admite [OpenID Connect](active-directory-b2c-reference-protocols.md) para todas las experiencias de usuario, incluidos inicio de sesión, registro y administración de perfiles. En la implementación de OpenID Connect de Azure AD B2C, su aplicación web inicia estas experiencias de usuario mediante la emisión de solicitudes de autenticación a Azure AD. El resultado de la solicitud es un `id_token`, un token de seguridad que representa la identidad del usuario y proporciona información sobre el usuario en forma de notificaciones:

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

Puede obtener información sobre todos los tipos de tokens y notificaciones disponibles para una aplicación en la [referencia de token B2C](active-directory-b2c-reference-tokens.md).

En las aplicaciones web, cada ejecución de una [directiva](active-directory-b2c-reference-policies.md) realiza estos pasos de alto nivel:

![Imagen de las calles de la aplicación web](./media/active-directory-b2c-apps/webapp.png)

La validación del id\_token con una clave de firma pública recibida desde Azure AD es suficiente para garantizar la identidad del usuario y establecer una cookie de sesión que pueda usarse para identificar al usuario en las solicitudes de página posteriores.

Para ver este escenario en acción, pruebe uno de los ejemplos de código de inicio de sesión de aplicación web en nuestra sección [Introducción](active-directory-b2c-overview.md#getting-started).

Además de un simple inicio de sesión, es posible que una aplicación de servidor web también necesite acceder a algún servicio web back-end. En este caso, la aplicación web puede realizar un [flujo de OpenID Connect](active-directory-b2c-reference-oidc.md) ligeramente distinto y adquirir tokens mediante códigos de autorización y tokens de actualización. A continuación se describe este escenario en la sección [API web](#web-apis).

<!--, and in our [WebApp-WebAPI Getting Started topic](active-directory-b2c-devquickstarts-web-api-dotnet.md).-->

## API web
También puede usar Azure AD B2C para proteger los servicios web, como la API web RESTful de su aplicación. Las API web pueden usar OAuth 2.0 para proteger datos y autenticar solicitudes HTTP entrantes con tokens. El llamador de una API web anexa un token en el encabezado de autorización de una solicitud HTTP:

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

La API web puede usar entonces el token para comprobar la identidad del llamador de API y extraer información sobre el llamador de notificaciones que se codifican en el token. Puede obtener información sobre todos los tipos de tokens y notificaciones disponibles para una aplicación en la [referencia de token de Azure AD B2C](active-directory-b2c-reference-tokens.md).

> [AZURE.NOTE]
	La vista previa de Azure AD B2C solo admite actualmente las API web a las que acceden sus propios clientes conocidos. Por ejemplo, la aplicación completa puede incluir una aplicación iOS, una aplicación Android y una API web back-end. Esta arquitectura es totalmente compatible. Lo que no se admite actualmente es permitir que un cliente de terceros, como otra aplicación de iOS, tenga acceso también a la misma API web. De hecho, cada componente de la aplicación completa debe compartir un Id. de aplicación único.

Una API web puede recibir tokens de todos los tipos de clientes, incluidas aplicaciones web, aplicaciones móviles y de escritorio, aplicaciones de una página, demonios del lado del servidor e incluso otras API web. Por ejemplo, veamos el flujo completo de una aplicación web que llama a una API web.

![Imagen de las calles de la API web de la aplicación web](./media/active-directory-b2c-apps/webapi.png)

Para obtener más información sobre authorization\_codes, refresh\_tokens y los pasos detallados para obtener tokens, lea sobre el [protocolo OAuth 2.0](active-directory-b2c-reference-oauth-code.md).

Para obtener información sobre cómo proteger una API web con Azure AD B2C, consulte los tutoriales de API web en nuestra sección [Introducción](active-directory-b2c-overview.md#getting-started).
	
## Aplicaciones móviles y nativas
Las aplicaciones instaladas en un dispositivo, como aplicaciones móviles y de escritorio, suelen necesitar el acceso a servicios back-end o a las API web en nombre del usuario. Puede agregar experiencias de administración de identidades personalizadas en sus aplicaciones nativas y llamar de forma segura a servicios back-end con Azure AD B2C y el [flujo de código de autorización de OAuth 2.0](active-directory-b2c-reference-oauth-code.md).

En este flujo, la aplicación ejecuta [directivas](active-directory-b2c-reference-policies.md) y recibe un authorization\_code de Azure AD después de que el usuario complete la directiva. El authorization\_code representa el permiso de la aplicación para llamar a los servicios back-end en nombre del usuario que ha iniciado sesión actualmente. La aplicación podrá intercambiar el authoriztion\_code en segundo plano por un id\_token y un refresh\_token. La aplicación puede usar el id\_token para autenticar la API web back-end en solicitudes HTTP y el refresh\_token, para obtener id\_tokens nuevos cuando expiren los antiguos.

> [AZURE.NOTE]
	La vista previa de Azure AD B2C actualmente solo admite la obtención de id\_tokens que se usan para tener acceso a un servicio web back-end de la aplicación. Por ejemplo, la aplicación completa puede incluir una aplicación iOS, una aplicación Android y una API web back-end. Esta arquitectura es totalmente compatible. Lo que no se está admitido actualmente es permitir a la aplicación iOS tener acceso a una API web de terceros mediante access\_tokens de OAuth 2.0. De hecho, cada componente de la aplicación completa debe compartir un Id. de aplicación único.

![Imagen de las calles de la aplicación nativa](./media/active-directory-b2c-apps/native.png)

## Limitaciones de la vista previa actual
Estos tipos de aplicaciones no son compatibles actualmente con la vista previa de Azure AD B2C, pero está previsto que sean compatibles a tiempo para su disponibilidad con carácter general. Las limitaciones y restricciones adicionales para la vista previa de Azure AD B2C se describen en el [artículo de limitaciones](active-directory-b2c-limitations.md).

### Aplicaciones de una página (Javascript)
Muchas aplicaciones modernas tienen una aplicación de una página escrita en front-end, principalmente en javascript, que usa a menudo marcos SPA como AngularJS, Ember.js, Durandal, etc. El servicio Azure AD, disponible con carácter general, admite estas aplicaciones mediante el flujo implícito de OAuth 2.0; sin embargo, este flujo no está disponible aún en Azure AD B2C. Lo estará en el corto plazo.

### Demonios/aplicaciones del lado del servidor
Las aplicaciones que contienen procesos de larga duración o que funcionan sin la presencia de un usuario también necesitan un modo de acceder a los recursos protegidos, como las API web. Estas aplicaciones pueden autenticar y obtener tokens con la identidad de la aplicación (en lugar de una identidad delegada del usuario) mediante el flujo de credenciales de cliente de OAuth 2.0.

Este flujo no es compatible actualmente con Azure AD B2C, lo que quiere decir que las aplicaciones solo pueden obtener tokens una vez producido un flujo de usuario interactivo. El flujo de credenciales de cliente se agregará en un futuro próximo.

### Cadenas de la API web (en nombre de)
Muchas arquitecturas incluyen una API web que necesita llamar a otra API web de nivel inferior, ambas protegidas mediante Azure AD B2C. Este escenario es común en los clientes nativos que tienen una API web back-end, que, a su vez, llama a un servicio de Microsoft Online, como la API de Azure AD Graph.

Este escenario de API web encadenadas puede admitirse mediante la concesión de credenciales de portador Jwt de OAuth 2.0, también conocido como flujo "en nombre de". Sin embargo, el flujo "en nombre de" no está implementado actualmente en la vista previa de Azure AD B2C.

<!---HONumber=AcomDC_0128_2016-->