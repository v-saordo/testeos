<properties
	pageTitle="Vista previa de Azure AD B2C | Microsoft Azure"
	description="Creación de aplicaciones web mediante la implementación del protocolo de autenticación OpenID Connect de Azure AD."
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
	ms.date="01/21/2016"
	ms.author="dastrock"/>

# Vista previa de Active Directory B2C: inicie sesión web con OpenID Connect

OpenID Connect es un protocolo de autenticación basado en OAuth 2.0 que puede usarse para que los usuarios inicien sesión de forma segura en las aplicaciones web. Mediante la implementación de Active Directory B2C de OpenID Connect, puede externalizar el registro, el inicio de sesión y otras experiencias de administración de identidades en sus aplicaciones web para Azure AD. Esta guía le enseñará a hacerlo de manera independiente del lenguaje, y describe cómo enviar y recibir mensajes HTTP sin usar ninguna de nuestras bibliotecas de código abierto.

<!-- TODO: Need link to libraries -->

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

[OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) amplía el protocolo de *autorización* de OAuth 2.0 para usarlo como un protocolo de *autenticación*, lo que le permite realizar inicios de sesión únicos mediante OAuth. Presenta el concepto de un `id_token`, que es un token de seguridad que permite al cliente comprobar la identidad del usuario y obtener información del perfil básica sobre el usuario. Puesto que amplía OAuth 2.0, también permite que las aplicaciones adquieran de forma segura **access\_tokens** que se puedan usar para acceder a los recursos protegidos mediante un [servidor de autorización](active-directory-b2c-reference-protocols.md#the-basics). OpenID Connect es nuestra recomendación si está creando una aplicación web que está alojada en un servidor y a la que se accede mediante un explorador. Si desea agregar la administración de identidades a sus aplicaciones móviles o de escritorio con Azure AD B2C, debe usar [OAuth 2.0](active-directory-b2c-reference-oauth-code.md) en lugar de OpenID Connect.

Azure AD B2C extiende el protocolo OpenID Connect estándar para realizar algo más que una autorización y autenticación simples. Presenta el [**parámetro de directiva**](active-directory-b2c-reference-poliices.md), que le permite usar OpenID Connect para agregar experiencias de usuario a su aplicación como registro, inicio de sesión y administración de perfiles. Aquí le mostraremos cómo usar OpenID Connect y las directivas para implementar cada una de estas experiencias en sus aplicaciones web y obtener access\_tokens para tener acceso a las API web.

Las siguientes solicitudes HTTP de ejemplo usarán nuestro directorio de ejemplo B2C, **fabrikamb2c.onmicrosoft.com**, así como nuestras directivas y la aplicación de ejemplo ****https://aadb2cplayground.azurewebsites.net**. Puede probar las solicitudes por usted mismo con estos valores, o bien puede reemplazarlos por los suyos propios. Obtenga más información acerca de cómo [obtener su propio directorio B2C, aplicación y directivas](#use-your-own-b2c-directory).

## Envío de solicitudes de autenticación
Cuando su aplicación web necesita autenticar al usuario y ejecutar la directiva, puede dirigir al usuario al extremo `/authorize`. Esta es la parte interactiva del flujo, donde el usuario actuará realmente de acuerdo con la directiva. En esta solicitud, el cliente indica los permisos que necesita adquirir del usuario en el parámetro `scope` y la directiva que se va a ejecutar en el parámetro `p`. A continuación se proporcionan tres ejemplos (con saltos de línea para facilitar la lectura), cada uno con una directiva diferente. Para hacerse una idea de cómo funciona cada solicitud, intente pegar la solicitud en un explorador y volver a ejecutarlo.

#### Uso de una directiva de inicio de sesión

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_in
```

#### Uso de una directiva de registro

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_up
```

#### Uso de una directiva de edición de perfil

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_edit_profile
```

| Parámetro | | Descripción |
| ----------------------- | ------------------------------- | ----------------------- |
| client\_id | requerido | El identificador de aplicación que el [Portal de Azure](https://portal.azure.com) asignó a la aplicación. |
| response\_type | requerido | Debe incluir `id_token` para OpenID Connect. Si su aplicación web también necesitará tokens para llamar a una API web, puede usar `code+id_token`, como hemos hecho aquí. |
| redirect\_uri | requerido | El redirect\_uri de su aplicación, a donde su aplicación puede enviar y recibir las respuestas de autenticación. Debe coincidir exactamente con uno de los redirect\_uris que registró en el portal, con la excepción de que debe estar codificado como URL. |
| ámbito | requerido | Una lista de ámbitos separada por espacios. Un valor de ámbito único indica a Azure AD los dos permisos que se solicitan. El ámbito `openid` indica un permiso para iniciar sesión para el usuario y obtener datos sobre el usuario en forma de **id\_tokens** (más información sobre esto más adelante). El ámbito `offline_access` es opcional para las aplicaciones web. Indica que la aplicación necesitará un **refresh\_token** para un acceso de larga duración a los recursos. |
| response\_mode | recomendado | Especifica el método que debe usarse para enviar el authorization\_code resultante de nuevo a tu aplicación. Puede ser uno de 'query', 'form\_post' o 'fragment'. |
| state | recomendado | Un valor incluido en la solicitud que se devolverá también en la respuesta del token. Puede ser una cadena de cualquier contenido que desee. Se utiliza normalmente un valor único generado de forma aleatoria para evitar los ataques de falsificación de solicitudes entre sitios. El estado también se usa para codificar información sobre el estado del usuario en la aplicación antes de que se haya producido la solicitud de autenticación, por ejemplo, la página en la que estaban. |
| valor de seguridad | requerido | Un valor incluido en la solicitud, generada por la aplicación, que se incluirá en el id\_token resultante como una notificación. La aplicación puede comprobar este valor para mitigar los ataques de reproducción de token. Normalmente, el valor es una cadena única aleatoria que puede utilizarse para identificar el origen de la solicitud. |
| p | requerido | Indica la directiva que se va a ejecutar. Es el nombre de una directiva creada en su directorio de B2C, cuyo valor debe comenzar por "b2c\_1\_". Obtenga más información acerca de las directivas [aquí](active-directory-b2c-reference-policies.md). |
| símbolo del sistema | opcional | Indica el tipo de interacción necesaria con el usuario. El único valor válido en este momento es 'login', lo que obliga al usuario a escribir sus credenciales en esa solicitud. El inicio de sesión único no surtirá efecto. |

En este punto, se pedirá al usuario que complete el flujo de trabajo de la directiva. Esto puede implicar que el usuario tenga que escribir su nombre de usuario y contraseña, iniciar sesión con una identidad social, registrarse para el directorio o realizar cualquier otro número de pasos, en función de cómo esté definida la directiva. Una vez que el usuario complete la directiva, Azure AD devolverá una respuesta a su aplicación en el `redirect_uri` indicado usando el método especificado en el parámetro `response_mode`. La respuesta será exactamente la misma para cada uno de los casos anteriores, independientemente de la directiva que se ejecute.

Una respuesta correcta al usar `response_mode=query` tiene el siguiente aspecto:

```
GET https://aadb2cplayground.azurewebsites.net/#
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parámetro | Descripción |
| ----------------------- | ------------------------------- |
| ID\_token | El id\_token que solicitó la aplicación. Puede usar el id\_token para comprobar la identidad del usuario y comenzar una sesión con el usuario. Se incluyen más detalles sobre id\_tokens y su contenido en la [referencia del token de Azure AD B2C](active-directory-b2c-reference-tokens.md). |
| código | El authorization\_code que solicitó la aplicación, si usó `response_type=code+id_token`. La aplicación puede usar el código de autorización para solicitar un token de acceso para un recurso de destino. Los authorization\_codes son de muy corta duración, normalmente expiran después de unos 10 minutos. |
| state | Si se incluye un parámetro de estado en la solicitud, debería aparecer el mismo valor en la respuesta. La aplicación debería comprobar que los valores de estado de la solicitud y la respuesta son idénticos. |

Las respuestas de error también pueden enviarse al `redirect_uri` para que la aplicación pueda controlarlas adecuadamente:

```
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parámetro | Descripción |
| ----------------------- | ------------------------------- |
| error | Una cadena de código de error que puede utilizarse para clasificar los tipos de errores que se producen y para reaccionar ante ellos. |
| error\_description | Un mensaje de error específico que puede ayudar a un desarrollador a identificar la causa de un error de autenticación. |
| state | Si se incluye un parámetro de estado en la solicitud, debería aparecer el mismo valor en la respuesta. La aplicación debería comprobar que los valores de estado de la solicitud y la respuesta son idénticos. |


## Validar el id\_token
Recibir un solo id\_token no es suficiente para autenticar al usuario; debe validar la firma del id\_token y comprobar las notificaciones en el token según los requisitos de su aplicación. Azure AD B2C usa [tokens web JSON (JWT)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) y la criptografía de clave pública para firmar los tokens y comprobar que son válidos. Hay muchas bibliotecas de código abierto disponibles para validar los JWT según el lenguaje de preferencia. Se recomienda explorar esas opciones en lugar de implementar su propia lógica de validación. Esta información será útil para averiguar cómo usar adecuadamente dichas bibliotecas.

Azure AD B2C tiene un extremo de metadatos OpenID Connect, que permite a una aplicación obtener información sobre Azure AD B2C en tiempo de ejecución. En esta información se incluyen los extremos, los contenidos del token y las claves de firma de los token. Hay un documento de metadatos JSON para cada directiva en su directorio B2C. Por ejemplo, el documento de metadatos para la directiva `b2c_1_sign_in` en `fabrikamb2c.onmicrosoft.com` se encuentra en:

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

Una de las propiedades de este documento de configuración es `jwks_uri`, cuyo valor para la misma directiva sería:

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`.

Para determinar qué directiva se usó en la firma de un id\_token (y de dónde obtener los metadatos), tiene dos opciones. En primer lugar, se incluye el nombre de la directiva en la notificación `acr` en id\_token. Para obtener información sobre cómo analizar las notificaciones de un id\_token, consulte la [referencia del token de Azure AD B2C](active-directory-b2c-reference-tokens.md). La otra opción consiste en codificar la directiva en el valor del parámetro `state` al emitir la solicitud y descodificarla para determinar qué directiva se ha usado. Cualquiera de los métodos es perfectamente válido.

Una vez que haya adquirido el documento de metadatos del extremo de metadatos OpenID Connect, puede usar las claves públicas RSA256 ubicadas en este extremo para validar la firma del id\_token. Hay varias claves enumeradas en este extremo en cualquier momento, cada una identificada con un `kid`. El encabezado del id\_token también contiene una notificación `kid`, que indica cuál de estas claves se usó para firmar el id\_token. Consulte la [referencia del token de Azure AD B2C](active-directory-b2c-reference-tokens.md) para más información, por ejemplo, [Vista previa de Azure AD B2C: referencia del token](active-directory-b2c-reference-tokens.md#validating-tokens) e [Información importante acerca de la sustitución de la clave de firma en Azure AD](active-directory-b2c-reference-tokens.md#validating-tokens). 
<!--TODO: Improve the information on this-->

Una vez haya validado la firma del id\_token, hay algunas notificaciones que tendrá que comprobar:

- Debería validar la notificación `nonce` para evitar ataques de reproducción del token. Su valor debería ser el que especificó en la solicitud de inicio de sesión.
- Debería validar la notificación `aud` para asegurarse de que se ha emitido el id\_token para su aplicación. Su valor debería ser el identificador de la aplicación.
- Debería validar las notificaciones `iat` y `exp` para asegurarse de que el id\_token no ha expirado.

Se recomienda que valide notificaciones adicionales según su escenario. Algunas validaciones comunes incluyen:

- Asegurarse de que la organización/el usuario se ha registrado en la aplicación.
- Asegurarse de que el usuario tiene la autorización/los privilegios adecuados
- Asegurarse de que se haya producido un determinado nivel de autenticación, como la autenticación multifactor.

Para obtener más información sobre las notificaciones en un id\_token, consulte la [referencia del token de Azure AD B2C](active-directory-b2c-reference-tokens.md).

Una vez que haya validado completamente el id\_token, puede iniciar una sesión con el usuario y usar las notificaciones en el id\_token para obtener información sobre el usuario en su aplicación. Esta información puede usarse para su visualización, registros, autorizaciones, etc.

## Obtención de un token
Si todo lo que su aplicación web necesita hacer es ejecutar directivas, puede omitir las secciones siguientes. Estas secciones solo son aplicables a las aplicaciones web que necesitan realizar llamadas autenticadas a una API web que también están protegidas por Azure AD B2C.

Ahora que ha adquirido un authorization\_code con `response_type=code+id_token`, puede canjearlo por un token al recurso deseado mediante el envío de una solicitud `POST` al extremo `/token`. En la vista previa de Azure AD B2C, el único recurso para el que puede solicitar un token es la API web back-end de la aplicación. La convención usada para solicitar un token para sí mismo es usar el ámbito `openid`:

```
POST fabrikamb2c.onmicrosoft.com/v2.0/oauth2/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
	"grant_type": "authorization_code",
	"client_id": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6",
	"scope": "openid offline_access",
	"code": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...",
	"redirect_uri": "urn:ietf:wg:oauth:2.0:oob",
	"client_secret": "<your-application-secret>"
}
```

| Parámetro | | Descripción |
| ----------------------- | ------------------------------- | --------------------- |
| p | requerido | La directiva usada para adquirir el código de autorización. No se puede usar una directiva diferente en esta solicitud. **Tenga en cuenta que este parámetro se agrega a la cadena de consulta** pero no al cuerpo de POST. |
| client\_id | requerido | El identificador de aplicación que el [Portal de Azure](https://portal.azure.com) asignó a la aplicación. |
| grant\_type | requerido | Debe ser `authorization_code` para el flujo de código de autorización. |
| ámbito | requerido | Una lista de ámbitos separada por espacios. Un valor de ámbito único indica a Azure AD los dos permisos que se solicitan. El ámbito `openid` indica un permiso para iniciar sesión para el usuario y obtener datos sobre el usuario en forma de **id\_tokens**. Se puede usar para obtener tokens para la propia API web back-end de la aplicación, representada por el mismo Id. de aplicación que el cliente. El ámbito `offline_access` indica que la aplicación necesitará un **refresh\_token** para un acceso de larga duración a los recursos. |
| código | requerido | El authorization\_code que adquirió en el primer segmento del flujo. |
| redirect\_uri | requerido | El redirect\_uri de la aplicación en la que recibió el authorization\_code. |
| client\_secret | requerido | El secreto de la aplicación que generó en el [Portal de Azure](https://portal.azure.com). Este secreto de la aplicación es un artefacto de seguridad importante y debe almacenarse de forma segura en el servidor. También debe procurar rotar este secreto del cliente de forma periódica. |

Una respuesta de token correcta tendrá un aspecto similar al siguiente:

```
{
	"not_before": "1442340812",
	"token_type": "Bearer",
	"id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
	"scope": "openid offline_access",
	"id_token_expires_in": "3600",
	"profile_info": "eyJ2ZXIiOiIxLjAiLCJ0aWQiOiI3NzU1MjdmZi05YTM3LTQzMDctOGIzZC1jY...",
	"refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
	"refresh_token_expires_in": "1209600"
}
```
| Parámetro | Descripción |
| ----------------------- | ------------------------------- |
| not\_before | Hora a la que el token se considera válido, en tiempo de época. |
| token\_type | Indica el valor de tipo de token. El único tipo que admite Azure AD es portador. |
| ID\_token | El token JWT firmado solicitado. |
| ámbito | Los ámbitos para los que el token es válido, que se pueden usar para almacenar en caché los tokens para su uso posterior. |
| id\_token\_expires\_in | Durante cuánto tiempo es válido el id\_token (en segundos). |
| profile\_info | La cadena JSON codificada de Base 64 que puede contener información útil sobre el usuario para mostrarla en la aplicación nativa. Su contenido exacto dependerá de las notificaciones de la aplicación configurada en la directiva |
| refresh\_token | Un token de actualización de OAuth 2.0. La aplicación puede usar este token para adquirir tokens adicionales una vez que expire el token actual. Los refresh\_tokens son de larga duración y pueden usarse para conservar el acceso a los recursos durante largos períodos de tiempo. Para obtener más información, consulte la [referencia del token B2C](active-directory-b2c-reference-tokens.md). Tenga en cuenta que debe haber usado el ámbito `offline_access` en las solicitudes de token y autorización para recibir un token de actualización. |
| refresh\_token\_expires\_in | El tiempo máximo que un token de actualización puede ser válido (en segundos). Sin embargo, el token de actualización puede dejar de ser válido en cualquier momento. |

> [AZURE.NOTE]
	Si en ese momento está pensando: "¿dónde está el access\_token?", tenga en cuenta lo siguiente. Cuando solicite el ámbito `openid`, Azure AD emitirá un `id_token` JWT en la respuesta. Aunque este `id_token` no es técnicamente un access\_token de OAuth 2.0, se puede usar como tal al comunicarse con el propio servicio back-end de la aplicación, representado por el mismo client\_id que el cliente. El `id_token` sigue siendo un token de portador JWT firmado que se puede enviar a un recurso en un encabezado de autorización HTTP y usar para autenticar solicitudes. La diferencia es que un `id_token` no tiene un mecanismo para definir el ámbito de acceso que puede tener una aplicación cliente en particular. Sin embargo, cuando la aplicación cliente es el único cliente capaz de comunicarse con el servicio back-end (como ocurre con la vista previa de Azure AD B2C actual), no hay necesidad de usar este mecanismo de definición de ámbito. Cuando la vista previa de Azure AD B2C agrega la capacidad para que los clientes puedan comunicarse con recursos adicionales propios y de terceros, se incorporarán access\_tokens. No obstante, incluso en ese momento, el uso de `id_tokens` para comunicarse con el servicio back-end de la aplicación seguirá siendo el modelo de solución recomendado. Para obtener más información sobre los tipos de aplicaciones que puede crear con la vista previa de Azure AD B2C, consulte [este artículo](active-directory-b2c-apps.md).

Las respuestas de error tendrán un aspecto similar al siguiente:

```
{
	"error": "access_denied",
	"error_description": "The user revoked access to the app.",
}
```

| Parámetro | Descripción |
| ----------------------- | ------------------------------- |
| error | Una cadena de código de error que puede utilizarse para clasificar los tipos de errores que se producen y para reaccionar ante ellos. |
| error\_description | Un mensaje de error específico que puede ayudar a un desarrollador a identificar la causa de un error de autenticación. |

## Uso del token
Ahora que ha adquirido correctamente un `id_token`, puede usar el token en solicitudes a las API web de back-end mediante su inclusión en el encabezado `Authorization`:

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## Actualización del token
Los Id\_tokens tienen una duración breve y debe actualizarlos una vez expiren para seguir teniendo acceso a los recursos. Puede hacerlo mediante el envío de otra solicitud `POST` al extremo `/token`, esta vez proporcionando el `refresh_token` en lugar del `code`:

```
POST fabrikamb2c.onmicrosoft.com/v2.0/oauth2/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
	"grant_type": "refresh_token",
	"client_id": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6",
	"scope": "openid offline_access",
	"refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...",
	"redirect_uri": "urn:ietf:wg:oauth:2.0:oob",
	"client_secret": "<your-application-secret>"
}
```

| Parámetro | | Descripción |
| ----------------------- | ------------------------------- | -------- |
| p | requerido | La directiva usada para adquirir el código de actualización original. No se puede usar una directiva diferente en esta solicitud. **Tenga en cuenta que este parámetro se agrega a la cadena de consulta** pero no al cuerpo de POST. |
| client\_id | requerido | El identificador de aplicación que el [Portal de Azure](https://portal.azure.com) asignó a la aplicación. |
| grant\_type | requerido | Debe ser `refresh_token` para este segmento del flujo de código de autorización. |
| ámbito | requerido | Una lista de ámbitos separada por espacios. Un valor de ámbito único indica a Azure AD los dos permisos que se solicitan. El ámbito `openid` indica un permiso para iniciar sesión para el usuario y obtener datos sobre el usuario en forma de **id\_tokens**. Se puede usar para obtener tokens para la propia API web back-end de la aplicación, representada por el mismo Id. de aplicación que el cliente. El ámbito `offline_access` indica que la aplicación necesitará un **refresh\_token** para un acceso de larga duración a los recursos. |
| redirect\_uri | requerido | El redirect\_uri de la aplicación en la que recibió el authorization\_code. |
| refresh\_token | requerido | El refresh\_token original que adquirió en el segundo segmento del flujo. Tenga en cuenta que debe haber usado el ámbito `offline_access` en las solicitudes de token y autorización para recibir un token de actualización. |
| client\_secret | requerido | El secreto de la aplicación que generó en el [Portal de Azure](https://portal.azure.com). Este secreto de la aplicación es un artefacto de seguridad importante y debe almacenarse de forma segura en el servidor. También debe procurar rotar este secreto del cliente de forma periódica. |

Una respuesta de token correcta tendrá un aspecto similar al siguiente:

```
{
	"not_before": "1442340812",
	"token_type": "Bearer",
	"id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
	"scope": "openid offline_access",
	"id_token_expires_in": "3600",
	"profile_info": "eyJ2ZXIiOiIxLjAiLCJ0aWQiOiI3NzU1MjdmZi05YTM3LTQzMDctOGIzZC1jY...",
	"refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
	"refresh_token_expires_in": "1209600"
}
```
| Parámetro | Descripción |
| ----------------------- | ------------------------------- |
| not\_before | Hora a la que el token se considera válido, en tiempo de época. |
| token\_type | Indica el valor de tipo de token. El único tipo que admite Azure AD es portador. |
| ID\_token | El token JWT firmado solicitado. |
| ámbito | Los ámbitos para los que el token es válido, que se pueden usar para almacenar en caché los tokens para su uso posterior. |
| id\_token\_expires\_in | Durante cuánto tiempo es válido el id\_token (en segundos). |
| profile\_info | La cadena JSON codificada de Base 64 que puede contener información útil sobre el usuario para mostrarla en la aplicación nativa. Su contenido exacto dependerá de las notificaciones de la aplicación configurada en la directiva |
| refresh\_token | Un token de actualización de OAuth 2.0. La aplicación puede usar este token para adquirir tokens adicionales una vez que expire el token actual. Los refresh\_tokens son de larga duración y pueden usarse para conservar el acceso a los recursos durante largos períodos de tiempo. Para obtener más información, consulte la [referencia del token B2C](active-directory-b2c-reference-tokens.md). |
| refresh\_token\_expires\_in | El tiempo máximo que un token de actualización puede ser válido (en segundos). Sin embargo, el token de actualización puede dejar de ser válido en cualquier momento. |

Las respuestas de error tendrán un aspecto similar al siguiente:

```
{
	"error": "access_denied",
	"error_description": "The user revoked access to the app.",
}
```

| Parámetro | Descripción |
| ----------------------- | ------------------------------- |
| error | Una cadena de código de error que puede utilizarse para clasificar los tipos de errores que se producen y para reaccionar ante ellos. |
| error\_description | Un mensaje de error específico que puede ayudar a un desarrollador a identificar la causa de un error de autenticación. |


<!--

Here is the entire flow for a native  app; each request is detailed in the sections below:

![OAuth Auth Code Flow](./media/active-directory-b2c-reference-oauth-code/convergence_scenarios_native.png)

-->

## Envío de una solicitud de cierre de sesión

Si desea cerrar la sesión del usuario de la aplicación, no es suficiente borrar las cookies de su aplicación; de lo contrario, termine la sesión con el usuario. También debe redirigir al usuario a Azure AD para el cierre de sesión. Si no lo hace, el usuario puede volver a autenticar su aplicación sin escribir sus credenciales de nuevo, porque tienen un inicio de sesión único válido con Azure AD.

Simplemente puede redirigir al usuario al `end_session_endpoint` que aparece en el mismo documento de metadatos OpenID Connect descrito [anteriormente](#validate-the-id-token):

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/logout?
p=b2c_1_sign_in
&post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| Parámetro | | Descripción |
| ----------------------- | ------------------------------- | ------------ |
| p | requerido | La directiva que el usuario empleó más recientemente para iniciar sesión en su aplicación. |
| post\_logout\_redirect\_uri | recomendado | La dirección URL a la que se debe redireccionar al usuario después de un cierre de sesión correcto. Si no se incluye, Azure AD B2C mostrará un mensaje genérico al usuario. |

> [AZURE.NOTE]
	Si bien dirigir al usuario a `end_session_endpoint` borrará algunos de los estados de inicio de sesión único del usuario con Azure AD, en la actualidad no cerrará la sesión del usuario realmente. En su lugar, el usuario seleccionará el IDP con el que desea iniciar sesión y, a continuación, volverá a autenticarse sin escribir sus credenciales. En el caso de IDP sociales, este es el comportamiento esperado. Si un usuario desea cerrar sesión en su directorio B2C, no necesariamente significa que desea cerrar sesión de su cuenta de Facebook completamente. Sin embargo, en el caso de las cuentas locales, debería ser posible terminar la sesión del usuario correctamente. Es una conocida [limitación](active-directory-b2c-limitations.md) de la vista previa de Azure AD que el cierre de sesión de las cuentas locales no funcionan correctamente. Una solución alternativa para la terminación inmediata es enviar el parámetro `&prompt=login` en cada solicitud de autenticación, que tendrá la apariencia del comportamiento deseado, pero interrumpirá el inicio de sesión único entre aplicaciones en su directorio B2C.

## Uso del directorio de B2C propio

Si desea probar estas solicitudes por sí mismo, primero debe realizar estos tres pasos y, a continuación, reemplazar los valores del ejemplo anterior por sus propios valores:

- [Crear un directorio de B2C](active-directory-b2c-get-started.md) y usar el nombre del directorio en las solicitudes.
- [Crear una aplicación](active-directory-b2c-app-registration.md) para obtener un Id. de aplicación y un redirect\_uri. Deseará incluir un **web app/web api** en su aplicación y, opcionalmente, crear un **secreto de aplicación**.
- [Crear directivas](active-directory-b2c-reference-policies.md) para obtener los nombres de las directivas.

<!--

TODO

OpenID Connect for the v2.0 app model is the recommended way to implement sign-in for a [web  app](active-directory-v2-flows.md#web-apps).  The most basic sign-in flow contains the following steps:

image goes here

-->

<!---HONumber=AcomDC_0128_2016-->