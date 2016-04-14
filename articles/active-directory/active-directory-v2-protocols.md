<properties
	pageTitle="Protocolos de Azure AD v2.0 | Microsoft Azure"
	description="Guía de los protocolos que admite el punto de conexión de Azure AD v2.0."
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

# Protocolos de v2.0: OAuth 2.0 y OpenID Connect

El punto de conexión v2.0 puede usar Azure AD para identidad como servicio con protocolos estándar del sector, OpenID Connect y OAuth 2.0. Aunque el servicio sea compatible con el estándar, puede haber diferencias sutiles entre cualquiera de las dos implementaciones de estos protocolos. La información que aquí se describe será útil si decide escribir su código mediante el envío y la administración directos de solicitudes HTTP o mediante el uso de una biblioteca de código abierto de terceros, en lugar de usar una de nuestras bibliotecas de código abierto. <!-- TODO: Need link to libraries above -->

> [AZURE.NOTE]
	No todas las características y escenarios de Azure Active Directory son compatibles con el punto de conexión v2.0. Para determinar si debe usar el punto de conexión v2.0, lea acerca de las [limitaciones de v2.0](active-directory-v2-limitations.md).

## Conceptos básicos
En casi todos los flujos de OAuth y OpenID Connect hay cuatro partes implicadas en el intercambio:

![Funciones de OAuth 2.0](../media/active-directory-v2-flows/protocols_roles.png)

- El **servidor de autorización** es el punto de conexión v2.0. Es responsable de garantizar la identidad del usuario, conceder y revocar el acceso a los recursos y emitir tokens. También se le conoce como proveedor de identidad: controla de forma segura todo lo que tenga que ver con la información del usuario, su acceso y las relaciones de confianza entre las partes de un flujo.
- El **propietario del recurso** suele ser el usuario final. Es la parte que posee los datos y tiene la capacidad de permitir que terceros tengan acceso a esos datos o recursos.
- El **cliente de OAuth** es su aplicación, identificada por su id. de aplicación. Suele ser la parte con la que interactúa el usuario final y solicita tokens del servidor de autorización. El cliente debe contar con el permiso del propietario del recurso para acceder a este.
- El **servidor de recursos** es donde residen el recurso o los datos. Confía en el servidor de autorización para autenticar y autorizar al cliente de OAuth de forma segura y usa access\_tokens de portador para garantizar que se puede conceder el acceso a un recurso.


## Registro de aplicaciones
Todas las aplicaciones que usen el punto de conexión v2.0 tendrán que registrarse en [apps.dev.microsoft.com](https://apps.dev.microsoft.com) para que puedan interactuar con el uso de OAuth o de OpenID Connect. El proceso de registro de la aplicación recopilará y asignará algunos valores a la aplicación:

- Un **Id. de aplicación** que identifica de forma única su aplicación
- Un **URI de redireccionamiento** o **identificador de paquete** que puede utilizarse para dirigir las respuestas de nuevo a la aplicación
- Algunos otros valores específicos de cada escenario.

Para más detalles, aprenda cómo [registrar una aplicación](active-directory-v2-app-registration.md).

## Extremos
Una vez registrada, la aplicación se comunica a Azure AD mediante el envío de solicitudes al extremo v2.0:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
```

Donde `{tenant}` puede adoptar uno de cuatro valores:

| Valor | Descripción |
| ----------------------- | ------------------------------- |
| `common` | Permite que los usuarios con cuentas personales de Microsoft y con cuentas profesionales o educativas de Azure Active Directory inicien sesión en la aplicación. |
| `organizations` | Permite que solo los usuarios con cuentas profesionales o educativas de Azure Active Directory inicien sesión en la aplicación. |
| `consumers` | Permite que solo los usuarios con cuentas personales de Microsoft (MSA) inicien sesión en la aplicación. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` o `contoso.onmicrosoft.com` | Permite que solo los usuarios con cuentas profesionales o educativas de un inquilino específico de Azure Active Directory inicien sesión en la aplicación. Puede usarse el nombre de dominio descriptivo del inquilino de Azure AD o bien el identificador de GUID del inquilino. |

Para obtener más información sobre cómo interactuar con estos puntos de conexión, elija un tipo de aplicación en particular a continuación.

## Tokens
La implementación v2.0 de OAuth 2.0 y OpenID Connect hace un uso generalizado de tokens de portador, incluidos los representados como JWT. Un token portador es un token de seguridad ligero que concede al "portador" acceso a un recurso protegido. En este sentido, el "portador" es cualquier parte que pueda presentar el token. Aunque una parte debe autenticarse primero con Azure AD para recibir el token portador, si no se realizan los pasos necesarios para asegurar el token en la transmisión y el almacenamiento, este puede interceptarse y ser utilizado por un usuario no deseado. Mientras que algunos tokens de seguridad disponen de un mecanismo integrado para evitar ser usados por partes no autorizadas, los tokens portadores no tienen este mecanismo y deben transportarse en un canal seguro como, por ejemplo, la seguridad de la capa de transporte (HTTPS). Si un token portador se transmite sin cifrar, un usuario malintencionado puede utilizar un ataque de tipo "Man in the middle" para adquirir el token y usarlo para obtener acceso sin autorización a un recurso protegido. Los mismos principios de seguridad se aplican al almacenamiento o almacenamiento en caché de tokens portadores para su uso posterior. Asegúrate siempre de que la aplicación transmite y almacena los tokens de portador de manera segura. Para otras consideraciones sobre la seguridad de los tokens portadores, consulte la [Sección 5 de RFC 6750](http://tools.ietf.org/html/rfc6750).

Encontrará más detalles sobre los diferentes tipos de token que se usan en el punto de conexión v2.0 en [la referencia de token del punto de conexión v2.0](active-directory-v2-tokens.md).

## Protocolos

Si está listo para ver algunas solicitudes de ejemplo, comience con uno de los siguiente tutoriales. Cada uno de ellos corresponde a un escenario de autenticación determinado. Si necesita ayuda para determinar cuál es el flujo correcto para usted, vea [los tipos de aplicaciones que puede compilar con v2.0](active-directory-v2-flows.md).

- [Creación de aplicaciones móviles y nativas con OAuth 2.0](active-directory-v2-protocols-oauth-code.md)
- [Creación de aplicaciones web con OpenID Connect](active-directory-v2-protocols-oidc.md)
- [Creación de aplicaciones de una sola página con el flujo implícito de OAuth 2.0](active-directory-v2-protocols-implicit.md)
- Creación de demonios o procesos del lado servidor con el flujo de credenciales de cliente de OAuth 2.0 (próximamente)
- Obtención de tokens en una API web con el flujo "en nombre de" de OAuth 2.0 (próximamente)

<!-- - Get tokens using a username & password with the OAuth 2.0 Resource Owner Password Credentials Flow (coming soon) -->
<!-- [Call the Azure AD Graph API using the OAuth 2.0 Client Credentials Flow](active-directory-reference-graph.md) -->

<!---HONumber=AcomDC_0224_2016-->