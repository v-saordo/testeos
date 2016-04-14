<properties
	pageTitle="Vista previa de Azure AD B2C | Microsoft Azure"
	description="Los tipos de aplicaciones que puede crear en la versión preliminar de Azure Active Directory B2C."
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
	ms.topic="hero-article"
	ms.date="02/25/2016"
	ms.author="dastrock"/>

# Versión preliminar de Azure Active Directory B2C: tipos de aplicaciones

Azure Active Directory (Azure AD) B2C admite la autenticación para una diversas arquitecturas de aplicaciones modernas. Todas ellas se basan en los protocolos estándar del sector [OAuth 2.0](active-directory-b2c-reference-protocols.md) o [OpenID Connect](active-directory-b2c-reference-protocols.md). Este documento describe brevemente los tipos de aplicaciones que puede crear, independientemente del lenguaje o la plataforma que prefiera. También ayuda a entender los escenarios de alto nivel antes de [empezar a crear aplicaciones](active-directory-b2c-overview.md#getting-started).

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Conceptos básicos
Cada aplicación que usa Azure AD B2C deberá estar registrada en su [directorio B2C](active-directory-b2c-get-started.md) mediante el [Portal de Azure](https://portal.azure.com/). El proceso de registro de la aplicación recopila y asigna algunos valores a la aplicación:

- Un **Id. de la aplicación** que identifica de forma única su aplicación.
- Un **URI de redirección** que puede usarse para dirigir las respuestas de nuevo a la aplicación.
- Cualquier otro valor específico de cada escenario. Para más información, aprenda a [registrar una aplicación](active-directory-b2c-app-registration.md).

Una vez registrada, la aplicación se comunica con Azure AD mediante el envío de solicitudes al punto de conexión v2.0 de Azure AD:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

En cada solicitud que se envía a Azure AD B2C se especifica una **directiva**. Una directiva controla el comportamiento de Azure AD. También puede utilizar estos puntos de conexión para crear un conjunto de experiencias de usuario muy personalizable. Algunas directivas comunes son las de registro, las de inicio de sesión y las de edición de perfiles. Si no está familiarizado con las directivas, debe leer acerca del [marco de directivas extensible](active-directory-b2c-reference-policies.md) de Azure AD B2C antes de continuar.

La interacción de cada aplicación con el punto de conexión v2.0 sigue un patrón de nivel alto similar:

1. La aplicación dirige al usuario al punto de conexión v2.0 para ejecutar una [directiva](active-directory-b2c-reference-policies.md).
2. El usuario completa la directiva según la definición de la misma.
4. La aplicación recibe un token de seguridad de algún tipo desde el punto de conexión v2.0.
5. La aplicación utiliza el token de seguridad para acceder a información o recursos protegidos.
6. El servidor de recursos valida el token de seguridad para asegurarse de que se puede conceder acceso.
7. La aplicación actualiza periódicamente el token de seguridad.

<!-- TODO: Need a page for libraries to link to -->
Estos pasos pueden diferir ligeramente según el tipo de aplicación que se crea. Las bibliotecas de código abierto pueden solucionar los detalles.

## Aplicaciones web
Para las aplicaciones web (incluidas .NET, PHP, Java, Ruby, Python y Node.js) que se hospedan en un servidor y a las que se tiene acceso a través de un explorador, Azure AD B2C admite [OpenID Connect](active-directory-b2c-reference-protocols.md) para todas las experiencias de usuario. Esto incluye el inicio de sesión, el registro y la administración de perfiles. En la implementación de OpenID Connect de Azure AD B2C, su aplicación web inicia estas experiencias de usuario mediante la emisión de solicitudes de autenticación a Azure AD. El resultado de la solicitud es un elemento `id_token`. Este token de seguridad representa la identidad del usuario. También proporciona información sobre el usuario en forma de notificaciones:

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

Aprenda más sobre todos los tipos de tokens y notificaciones disponibles para una aplicación en la [referencia de token B2C](active-directory-b2c-reference-tokens.md).

En una aplicación web, cada ejecución de una [directiva](active-directory-b2c-reference-policies.md) realiza estos pasos de alto nivel:

![Imagen de las calles de la aplicación web](./media/active-directory-b2c-apps/webapp.png)

Validación de `id_token` mediante una clave de firma pública que se recibe de Azure AD es suficiente para comprobar la identidad del usuario. Esto también establece una cookie de sesión que puede usarse para identificar al usuario en las solicitudes de página posteriores.

Para ver este escenario en acción, pruebe uno de los ejemplos de código de inicio de sesión de aplicación web en nuestra sección [Introducción](active-directory-b2c-overview.md#getting-started).

Además de facilitar un inicio de sesión simple, es posible que una aplicación de servidor web también necesite acceder a algún servicio web back-end. En este caso, la aplicación web puede realizar un [flujo de OpenID Connect](active-directory-b2c-reference-oidc.md) ligeramente distinto y adquirir tokens mediante códigos de autorización y tokens de actualización. Este escenario se describe en la siguiente sección: [API web](#web-apis).

<!--, and in our [WebApp-WebAPI Getting started topic](active-directory-b2c-devquickstarts-web-api-dotnet.md).-->

## API web
También puede usar Azure AD B2C para proteger los servicios web, como la API web RESTful de su aplicación. Las API web pueden usar OAuth 2.0 para proteger sus datos. También pueden autenticar solicitudes HTTP entrantes mediante el uso de tokens. El llamador de una API web anexa un token en el encabezado de autorización de una solicitud HTTP:

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

A continuación, la API web puede usar el token para comprobar la identidad del llamador de la API y extraer información sobre el llamador de notificaciones que se codifican en el token. Aprenda más sobre todos los tipos de tokens y notificaciones disponibles para una aplicación en la [referencia de token de Azure AD B2C](active-directory-b2c-reference-tokens.md).

> [AZURE.NOTE]
	Actualmente, la versión preliminar de Azure AD B2C solo admite las API web a las que acceden sus propios clientes conocidos. Por ejemplo, la aplicación completa puede incluir una aplicación iOS, una aplicación Android y una API web back-end. Esta arquitectura es totalmente compatible. Actualmente no se permite que un cliente de un asociado, como otra aplicación de iOS, tenga acceso a la misma API web. Todos los componentes de la aplicación deben compartir un identificador de aplicación único.

Una API web puede recibir tokens de muchos tipos de clientes, incluidas aplicaciones web, aplicaciones móviles y de escritorio, aplicaciones de una página, demonios del lado del servidor e incluso otras API web. Por ejemplo, veamos el flujo completo de una aplicación web que llama a una API web:

![Imagen de las calles de la API web de la aplicación web](./media/active-directory-b2c-apps/webapi.png)

Para más información sobre los códigos de autorización, los tokens de actualización y los pasos para obtener tokens, lea sobre el [protocolo OAuth 2.0](active-directory-b2c-reference-oauth-code.md).

Para más información sobre cómo proteger una API web con Azure AD B2C, consulte los tutoriales de API web en nuestra sección [Introducción](active-directory-b2c-overview.md#getting-started).

## Aplicaciones móviles y nativas
Las aplicaciones instaladas en dispositivos, como aplicaciones móviles y de escritorio, suelen necesitar el acceso a servicios back-end o a las API web en nombre de los usuarios. Puede agregar experiencias de administración de identidades personalizadas a sus aplicaciones nativas y llamar de forma segura a servicios back-end con Azure AD B2C y el [flujo de código de autorización de OAuth 2.0](active-directory-b2c-reference-oauth-code.md).

En este flujo, la aplicación ejecuta [directivas](active-directory-b2c-reference-policies.md) y recibe un elemento `authorization_code` de Azure AD después de que el usuario complete la directiva. El elemento `authorization_code` representa el permiso de la aplicación para llamar a servicios de back-end en nombre del usuario que actualmente ha iniciado sesión. La aplicación podrá intercambiar el elemento `authorization_code` en segundo plano para `id_token` y `refresh_token`. La aplicación puede utilizar el elemento `id_token` para autenticar a una API web back-end en las solicitudes HTTP. También puede usar el elemento `refresh_token` para obtener un nuevo `id_token` cuando caduca uno más antiguo.

> [AZURE.NOTE]
	Actualmente, la versión preliminar de Azure AD B2C solo admite la obtención de tokens de identificador que se usan para acceder a un servicio web back-end de la aplicación. Por ejemplo, la aplicación completa puede incluir una aplicación iOS, una aplicación Android y una API web back-end. Esta arquitectura es totalmente compatible. Actualmente no se permite que la aplicación iOS tenga acceso a una API web de un asociado mediante tokens de acceso de OAuth 2.0. Todos los componentes de la aplicación deben compartir un identificador de aplicación único.

![Imagen de las calles de la aplicación nativa](./media/active-directory-b2c-apps/native.png)

## Limitaciones de la vista previa actual
Actualmente, la versión preliminar de Azure AD B2C no admite los siguientes tipos de aplicaciones, pero está previsto que sean compatibles a tiempo para su disponibilidad con carácter general. Las limitaciones y restricciones adicionales relacionadas con la versión preliminar de Azure AD B2C se describen en [Limitaciones y restricciones](active-directory-b2c-limitations.md).

### Aplicaciones de una página (JavaScript)
Muchas aplicaciones modernas tienen un front-end de aplicación de una página escrito principalmente en JavaScript. A menudo usan un marco como AngularJS, Ember.js o Durandal. Los servicios de Azure AD disponibles con carácter general admiten estas aplicaciones mediante el flujo implícito de OAuth 2.0. Este flujo no está disponible todavía en la versión preliminar de Azure AD B2C. Pronto lo estará.

### Demonios o aplicaciones del lado del servidor
Las aplicaciones que contienen procesos de larga duración o que funcionan sin la presencia de un usuario también necesitan un modo de acceder a recursos protegidos, como las API web. Estas aplicaciones pueden autenticar y obtener tokens con la identidad de la aplicación (en lugar de una identidad delegada del usuario) y mediante el flujo de credenciales de cliente de OAuth 2.0.

Actualmente, este flujo no es compatible con Azure AD B2C. Estas aplicaciones pueden obtener tokens solo después de que se haya producido un flujo de usuario interactivo. El flujo de credenciales de cliente se agregará en un futuro próximo.

### Cadenas de la API web (flujo en nombre de)
Muchas arquitecturas incluyen una API web que necesita llamar a otra API web de nivel inferior, ambas protegidas mediante Azure AD B2C. Este escenario es habitual en los clientes nativos que tienen una API web back-end. Esto llama a un servicio en línea de Microsoft, como la API Azure AD Graph.

Este escenario de API web encadenadas puede admitirse mediante la concesión de credenciales de portador JWT de OAuth 2.0, también conocido como flujo "en nombre de". Sin embargo, actualmente el flujo "en nombre de" no está implementado en la versión preliminar de Azure AD B2C.

<!---HONumber=AcomDC_0302_2016-->