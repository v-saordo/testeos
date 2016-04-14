
<properties
	pageTitle="Flujo de código de autorización de Azure AD v2.0 OAuth | Microsoft Azure"
	description="Creación de aplicaciones web mediante la implementación del protocolo de autenticación OAuth 2.0 de Azure AD."
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="msmbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="02/20/2016"
	ms.author="dastrock"/>

# Protocolos de la versión 2.0: Flujo de código de autorización de OAuth 2.0

La concesión de un código de autorización de OAuth 2.0 se puede usar en aplicaciones que se instalan en un dispositivo para obtener acceso a recursos protegidos, como las API web. Mediante la implementación de OAuth 2.0 del modelo de aplicaciones v2.0, puede agregar inicio de sesión y acceso a API a las aplicaciones móviles y de escritorio. Esta guía, que es independiente del lenguaje, describe cómo enviar y recibir mensajes HTTP sin usar ninguna de nuestras bibliotecas de código abierto.

<!-- TODO: Need link to libraries -->	

> [AZURE.NOTE]
	No todas las características y escenarios de Azure Active Directory son compatibles con el punto de conexión v2.0. Para determinar si debe usar el punto de conexión v2.0, lea acerca de las [limitaciones de v2.0](active-directory-v2-limitations.md).

El flujo de código de autorización de OAuth 2.0 se describe en la [sección 4.1 de la especificación OAuth 2.0](http://tools.ietf.org/html/rfc6749). Se usa para realizar la autenticación y la autorización en la mayoría de los tipos de aplicación, incluidas las [aplicaciones web](active-directory-v2-flows.md#web-apps) y las [aplicaciones instaladas de forma nativa](active-directory-v2-flows.md#mobile-and-native-apps). Permite que las aplicaciones adquieran de forma segura access\_tokens que se puedan usar para acceder a los recursos protegidos mediante el punto de conexión v2.0.

## Diagrama de protocolo
En un nivel alto, el flujo de autenticación completo para una aplicación nativa o móvil es algo parecido a esto:

![Flujo de código de autenticación de OAuth](../media/active-directory-v2-flows/convergence_scenarios_native.png)

## Solicitud de un código de autorización
El flujo del código de autorización comienza con el cliente dirigiendo al usuario al punto de conexión `/authorize`. En esta solicitud, el cliente indica los permisos que necesita adquirir del usuario:

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&state=12345
```

> [AZURE.TIP] Pruebe a pegar la siguiente solicitud en un explorador.

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=query&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&state=12345
```

| Parámetro | | Descripción |
| ----------------------- | ------------------------------- | --------------- |
| tenant | requerido | El valor `{tenant}` de la ruta de acceso de la solicitud se puede usar para controlar quién puede iniciar sesión en la aplicación. Los valores permitidos son `common`, `organizations`, `consumers` y los identificadores de inquilinos. Para obtener más información, consulte los [conceptos básicos sobre el protocolo](active-directory-v2-protocols.md#endpoints). |
| client\_id | requerido | El id. de aplicación que el portal de registro ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) asignó a su aplicación. |
| response\_type | requerido | Debe incluir `code` para el flujo de código de autorización. |
| redirect\_uri | recomendado | El redirect\_uri de su aplicación, a donde su aplicación puede enviar y recibir las respuestas de autenticación. Debe coincidir exactamente con uno de los redirect\_uris que registró en el portal, con la excepción de que debe estar codificado como URL. Para las aplicaciones nativas y móviles, debe usar el valor predeterminado de `urn:ietf:wg:oauth:2.0:oob`. |
| ámbito | requerido | Lista separada por espacios de los [ámbitos](active-directory-v2-scopes.md) para los que desea que el usuario dé su consentimiento. |
| response\_mode | recomendado | Especifica el método que debe usarse para enviar el token resultante de nuevo a la aplicación. Puede ser `query` o `form_post`. |
| state | recomendado | Un valor incluido en la solicitud que se devolverá también en la respuesta del token. Puede ser una cadena de cualquier contenido que desee. Normalmente se usa un valor único generado de forma aleatoria para [evitar los ataques de falsificación de solicitudes entre sitios](http://tools.ietf.org/html/rfc6749#section-10.12). El estado también se usa para codificar información sobre el estado del usuario en la aplicación antes de que se haya producido la solicitud de autenticación, por ejemplo, la página o vista en la que estaban. |
| símbolo del sistema | opcional | Indica el tipo de interacción necesaria con el usuario. Los únicos valores válidos en este momento son 'login', 'none' y 'consent'. `prompt=login` obligará al usuario a escribir sus credenciales en esa solicitud, negando el inicio de sesión único. En el lado opuesto, `prompt=none` se asegurará de que al usuario no se le presenta ninguna solicitud interactiva del tipo que sea. Si no se puede completar la solicitud sin notificaciones mediante el inicio de sesión único, el punto de conexión v2.0 devolverá un error. `prompt=consent` desencadenará el cuadro de diálogo de consentimiento de OAuth después de que el usuario inicia sesión, y solicitará a este que conceda permisos a la aplicación. |
| login\_hint | opcional | Puede usarse para rellenar previamente el campo de nombre de usuario y dirección de correo electrónico de la página de inicio de sesión del usuario, si sabe su nombre de usuario con antelación. A menudo las aplicaciones usarán este parámetro durante la reautenticación, dado que ya han extraído el nombre de usuario de un inicio de sesión anterior mediante la notificación `preferred_username`. |
| domain\_hint | opcional | Puede ser `consumers` o `organizations`. Si se incluye, omitirá el proceso de detección basado en correo electrónico por el que pasa el usuario en la página de inicio de sesión de v2.0, con lo que la experiencia de usuario será ligeramente más sencilla. A menudo las aplicaciones usarán este parámetro durante la reautenticación, para lo que extraerán la notificación `tid` de un inicio de sesión anterior. Si el valor de la notificación `tid` es `9188040d-6c67-4c5b-b112-36a304b66dad`, debe usar `domain_hint=consumers`. De lo contrario, use `domain_hint=organizations`. |

En este punto, se le pedirá al usuario que escriba sus credenciales y que complete la autenticación. El punto de conexión v2.0 también garantiza que el usuario ha dado su consentimiento a los permisos indicados en el parámetro de la consulta `scope`. Si el usuario no ha dado su consentimiento a alguno de esos permisos, se le solicitará al usuario su consentimiento para los permisos necesarios. Aquí puede encontrar información detallada sobre los [permisos, el consentimiento y las aplicaciones multiempresa](active-directory-v2-scopes.md).

Una vez que el usuario se autentica y otorga su consentimiento, el punto de conexión v2.0 devuelve una respuesta a su aplicación en el `redirect_uri` indicado mediante el método especificado en el parámetro `response_mode`.

#### Respuesta correcta
Una respuesta correcta al usar `response_mode=query` tiene el siguiente aspecto:

```
GET urn:ietf:wg:oauth:2.0:oob?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=12345
```

| Parámetro | Descripción |
| ----------------------- | ------------------------------- |
| código | El authorization\_code que solicitó la aplicación. La aplicación puede utilizar el código de autorización para solicitar un token de acceso para el recurso de destino. Los authorization\_codes son de muy corta duración, normalmente expiran después de unos 10 minutos. |
| state | Si se incluye un parámetro de estado en la solicitud, debería aparecer el mismo valor en la respuesta. La aplicación debería comprobar que los valores de estado de la solicitud y la respuesta son idénticos. |

#### Respuesta de error
Las respuestas de error también pueden enviarse al `redirect_uri` para que la aplicación pueda controlarlas adecuadamente:

```
GET urn:ietf:wg:oauth:2.0:oob?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parámetro | Descripción |
| ----------------------- | ------------------------------- |
| error | Una cadena de código de error que puede utilizarse para clasificar los tipos de errores que se producen y para reaccionar ante ellos. |
| error\_description | Un mensaje de error específico que puede ayudar a un desarrollador a identificar la causa de un error de autenticación. |

## Solicitud de un token de acceso
Ahora que ha adquirido un código de autorización y el usuario le ha concedido permiso, puede canjear el `code` por un `access_token` al recurso deseado mediante el envío de una solicitud de `POST` al punto de conexión `/token`:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=authorization_code
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps
```

> [AZURE.TIP] Intente importar el siguiente comando Curl en Postman (para que se realice correctamente tendrá que reemplazar el `code` con el suyo propio).

```
curl -X POST -H "Cache-Control: no-cache" -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=6731de76-14a6-49ae-97bc-6eba6914391e&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXrFgnryzZvcDbKTvyz36ono600tLhxSdnoOe50zSgxiIQhD36sIPLln7lNOMrUi1ralV_hOfZItjuwqeTOTFgXRG_rhkIzBfKmudQHD1KUodPD84a308LAfJ5ciLak9nlNVyVOL7gViWADpdZv_KrBXgaJXkxKZ4qxeYT_wf6yajHP2Gt4LPijuhqJIsqId7Xo8FkNIsmlvZkdArZDLgpZdunDmnis_623fu4vMeuWyVhrAoesilIqbwP_bKWNhGO_fcQ1Spsa-TDgfqUyrXnk3UYc-B3m6Npvkx3bYv3NrUSNxqdMONxR-3HowU3Uke-jM3Z8GR25HE4YAdfTqVxHtd6DEP9aamMIRH0LwuM4uxUrgeALqpbPenabekOZkkZ5-KKY4AyJKMOWxvMmqJRz9gYHnGUxqKcl2-F7250rHNGZTbJPurie_3WzNrRKFOQAF84mbsGoeYvSXlbI5uiH3Bw9kpOw302r26K4j-IKoMpw2BXU0mNxoGEL_wC0oTkVqRNg_sTTcsAPU1giW0hj-LONWc0ZgcKNI00fXaC5l6V8i2ERWyBy4Ys8gKIc7mynZnCpf2tgrxMBH5sloZ1Lf6P63CiAA&client_secret=JqQX2PNo9bpM0uEihUPzyrh&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&grant_type=authorization_code' 'https://login.microsoftonline.com/common/oauth2/v2.0/token'
```


| Parámetro | | Descripción |
| ----------------------- | ------------------------------- | --------------------- |
| tenant | requerido | El valor `{tenant}` de la ruta de acceso de la solicitud se puede usar para controlar quién puede iniciar sesión en la aplicación. Los valores permitidos son `common`, `organizations`, `consumers` y los identificadores de inquilinos. Para obtener más información, consulte los [conceptos básicos sobre el protocolo](active-directory-v2-protocols.md#endpoints). |
| client\_id | requerido | El id. de aplicación que el portal de registro ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) asignó a su aplicación. |
| grant\_type | requerido | Debe ser `authorization_code` para el flujo de código de autorización. |
| ámbito | requerido | Una lista de ámbitos separada por espacios. Los ámbitos solicitados en esta fase deben ser un subconjunto de los ámbitos solicitados en el primer segmento o un equivalente de este. Si los ámbitos especificados en esta solicitud abarcan varios servidores de recursos, el extremo v2.0 devolverá un token para el recurso especificado en el primer ámbito. Para obtener una explicación más detallada de los ámbitos, consulte [permisos, consentimiento y ámbitos](active-directory-v2-scopes.md). |
| código | requerido | El authorization\_code que adquirió en el primer segmento del flujo. |
| redirect\_uri | requerido | El mismo valor redirect\_uri usado para adquirir el código de autorización. |
| client\_secret | necesario para las aplicaciones web | El secreto de la aplicación que creó en el portal de registro de aplicaciones para su aplicación. No debería utilizarse en una aplicación nativa, porque los client\_secrets no se pueden almacenar de forma confiable en los dispositivos. Es necesario para aplicaciones web y las API web, que tienen la capacidad de almacenar el client\_secret de forma segura en el lado del servidor. |

#### Respuesta correcta
Una respuesta de token correcta tendrá un aspecto similar al siguiente:

```
{
	"access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
	"token_type": "Bearer",
	"expires_in": 3599,
	"scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
	"refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
	"id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```
| Parámetro | Descripción |
| ----------------------- | ------------------------------- |
| access\_token | El token de acceso solicitado. La aplicación puede utilizar este token para autenticar a los recursos protegidos, como una API web. |
| token\_type | Indica el valor de tipo de token. El único tipo que admite Azure AD es el portador |
| expires\_in | Durante cuánto tiempo es válido el token de acceso (en segundos). |
| ámbito | Los ámbitos para los que el access\_token es válido. |
| refresh\_token | Un token de actualización de OAuth 2.0. La aplicación puede utilizar este token para adquirir tokens de acceso adicionales una vez que expire el token de acceso actual. Los refresh\_tokens son de larga duración y pueden usarse para conservar el acceso a los recursos durante largos períodos de tiempo. Para más información, consulte la [referencia a los tokens v2.0](active-directory-v2-tokens.md). |
| ID\_token | Un token web JSON (JWT) sin firmar. La aplicación puede descodificar base64Url en los segmentos de este token para solicitar información acerca del usuario que ha iniciado sesión. La aplicación puede almacenar en caché los valores y mostrarlos, pero no debe confiar en ellos para cualquier autorización o límite de seguridad. Para obtener más información sobre los id\_tokens, consulte la [referencia de token del punto de conexión v2.0](active-directory-v2-tokens.md). |

#### Respuesta de error
Las respuestas de error tendrán un aspecto similar al siguiente:

```
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| Parámetro | Descripción |
| ----------------------- | ------------------------------- |
| error | Una cadena de código de error que puede utilizarse para clasificar los tipos de errores que se producen y para reaccionar ante ellos. |
| error\_description | Un mensaje de error específico que puede ayudar a un desarrollador a identificar la causa de un error de autenticación. |
| error\_codes | Una lista de los códigos de error específicos de STS que pueden ayudar en los diagnósticos. |
| timestamp | La hora a la que se produjo el error. |
| trace\_id | Un identificador exclusivo para la solicitud que puede ayudar en los diagnósticos. |
| correlation\_id | Un identificador exclusivo para la solicitud que puede ayudar en los diagnósticos entre componentes. |

## Uso del token de acceso
Ahora que ha adquirido correctamente un `access_token`, puede usar el token en solicitudes a las API web mediante su inclusión en el encabezado `Authorization`:

```
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

> [AZURE.TIP] Pruebe el siguiente comando (reemplace el token con el suyo propio)

```
curl -X GET -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q" 'https://graph.microsoft.com/v1.0/me/messages'
```

## Actualización del token de acceso
Los Access\_tokens tienen una duración breve y debe actualizarlos una vez expiren para seguir teniendo acceso a los recursos. Puede hacerlo mediante el envío de otra solicitud `POST` al punto de conexión `/token`, esta vez proporcionando el `refresh_token` en lugar del `code`:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=refresh_token
&client_secret=JqQX2PNo9bpM0uEihUPzyrh	  // NOTE: Only required for web apps
```

> [AZURE.TIP] Intente importar el siguiente comando Curl en postman (para que se realice correctamente tendrá que reemplazar el refresh\_token con el suyo propio)

```
curl -X POST -H "Cache-Control: no-cache" -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=6731de76-14a6-49ae-97bc-6eba6914391e&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXrFgnryzZvcDbKTvyz36ono600tLhxSdnoOe50zSgxiIQhD36sIPLln7lNOMrUi1ralV_hOfZItjuwqeTOTFgXRG_rhkIzBfKmudQHD1KUodPD84a308LAfJ5ciLak9nlNVyVOL7gViWADpdZv_KrBXgaJXkxKZ4qxeYT_wf6yajHP2Gt4LPijuhqJIsqId7Xo8FkNIsmlvZkdArZDLgpZdunDmnis_623fu4vMeuWyVhrAoesilIqbwP_bKWNhGO_fcQ1Spsa-TDgfqUyrXnk3UYc-B3m6Npvkx3bYv3NrUSNxqdMONxR-3HowU3Uke-jM3Z8GR25HE4YAdfTqVxHtd6DEP9aamMIRH0LwuM4uxUrgeALqpbPenabekOZkkZ5-KKY4AyJKMOWxvMmqJRz9gYHnGUxqKcl2-F7250rHNGZTbJPurie_3WzNrRKFOQAF84mbsGoeYvSXlbI5uiH3Bw9kpOw302r26K4j-IKoMpw2BXU0mNxoGEL_wC0oTkVqRNg_sTTcsAPU1giW0hj-LONWc0ZgcKNI00fXaC5l6V8i2ERWyBy4Ys8gKIc7mynZnCpf2tgrxMBH5sloZ1Lf6P63CiAA&client_secret=JqQX2PNo9bpM0uEihUPzyrh&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&grant_type=refresh_token' 'https://login.microsoftonline.com/common/oauth2/v2.0/token'
```

| Parámetro | | Descripción |
| ----------------------- | ------------------------------- | -------- |
| tenant | requerido | El valor `{tenant}` de la ruta de acceso de la solicitud se puede usar para controlar quién puede iniciar sesión en la aplicación. Los valores permitidos son `common`, `organizations`, `consumers` y los identificadores de inquilinos. Para obtener más información, consulte los [conceptos básicos sobre el protocolo](active-directory-v2-protocols.md#endpoints). |
| client\_id | requerido | El id. de aplicación que el portal de registro ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) asignó a su aplicación. |
| grant\_type | requerido | Debe ser `refresh_token` para este segmento del flujo de código de autorización. |
| ámbito | requerido | Una lista de ámbitos separada por espacios. Los ámbitos solicitados en este segmento deben ser un subconjunto de los ámbitos solicitados en el segmento de la solicitud del authorization\_code original o un equivalente de este. Si los ámbitos especificados en esta solicitud abarcan varios servidores de recursos, el extremo v2.0 devolverá un token para el recurso especificado en el primer ámbito. Para obtener una explicación más detallada de los ámbitos, consulte [permisos, consentimiento y ámbitos](active-directory-v2-scopes.md). |
| refresh\_token | requerido | El refresh\_token que adquirió en el segundo segmento del flujo. |
| redirect\_uri | requerido | El mismo valor redirect\_uri usado para adquirir el código de autorización. |
| client\_secret | necesario para las aplicaciones web | El secreto de la aplicación que creó en el portal de registro de aplicaciones para su aplicación. No debería utilizarse en una aplicación nativa, porque los client\_secrets no se pueden almacenar de forma confiable en los dispositivos. Es necesario para aplicaciones web y las API web, que tienen la capacidad de almacenar el client\_secret de forma segura en el lado del servidor. |

#### Respuesta correcta
Una respuesta de token correcta tendrá un aspecto similar al siguiente:

```
{
	"access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
	"token_type": "Bearer",
	"expires_in": 3599,
	"scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
	"refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
	"id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```
| Parámetro | Descripción |
| ----------------------- | ------------------------------- |
| access\_token | El token de acceso solicitado. La aplicación puede utilizar este token para autenticar a los recursos protegidos, como una API web. |
| token\_type | Indica el valor de tipo de token. El único tipo que admite Azure AD es el portador |
| expires\_in | Durante cuánto tiempo es válido el token de acceso (en segundos). |
| ámbito | Los ámbitos para los que el access\_token es válido. |
| refresh\_token | Un nuevo token de actualización de OAuth 2.0. Debe reemplazar el token de actualización antiguo con este token de actualización recientemente adquirido para asegurar que los tokens de actualización siguen siendo válidos durante tanto tiempo como sea posible. |
| ID\_token | Un token web JSON (JWT) sin firmar. La aplicación puede descodificar base64Url en los segmentos de este token para solicitar información acerca del usuario que ha iniciado sesión. La aplicación puede almacenar en caché los valores y mostrarlos, pero no debe confiar en ellos para cualquier autorización o límite de seguridad. Para obtener más información sobre los id\_tokens, consulte la [referencia de token del punto de conexión v2.0](active-directory-v2-tokens.md). |

#### Respuesta de error
```
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| Parámetro | Descripción |
| ----------------------- | ------------------------------- |
| error | Una cadena de código de error que puede utilizarse para clasificar los tipos de errores que se producen y para reaccionar ante ellos. |
| error\_description | Un mensaje de error específico que puede ayudar a un desarrollador a identificar la causa de un error de autenticación. |
| error\_codes | Una lista de los códigos de error específicos de STS que pueden ayudar en los diagnósticos. |
| timestamp | La hora a la que se produjo el error. |
| trace\_id | Un identificador exclusivo para la solicitud que puede ayudar en los diagnósticos. |
| correlation\_id | Un identificador exclusivo para la solicitud que puede ayudar en los diagnósticos entre componentes. |

<!---HONumber=AcomDC_0302_2016-->