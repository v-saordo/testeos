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

# Vista previa de Azure AD B2C: Flujo de código de autorización de OAuth 2.0

La concesión de un código de autorización de OAuth 2.0 se puede usar en aplicaciones que se instalan en un dispositivo para obtener acceso a recursos protegidos, como las API web. Con la implementación de OAuth 2.0 de Azure AD B2C puede agregar registro, inicio de sesión y otras tareas de administración de identidades a las aplicaciones móviles y de escritorio. Esta guía, que es independiente del lenguaje, describe cómo enviar y recibir mensajes HTTP sin usar ninguna de nuestras bibliotecas de código abierto.

<!-- TODO: Need link to libraries -->

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

El flujo de código de autorización de OAuth 2.0 se describe en la [sección 4.1 de la especificación OAuth 2.0](http://tools.ietf.org/html/rfc6749). Se puede usar para realizar la autenticación y la autorización en la mayoría de los tipos de aplicación, incluidas las [aplicaciones web](active-directory-b2c-apps.md#web-apps) y las [aplicaciones instaladas de forma nativa](active-directory-b2c-apps.md#mobile-and-native-apps). Permite que las aplicaciones adquieran de forma segura **access\_tokens** que se puedan usar para acceder a los recursos protegidos mediante un [servidor de autorización](active-directory-b2c-reference-protocols.md#the-basics). Esta guía se centra en un tipo determinado de flujo de código de autorización de OAuth 2.0: los **clientes públicos**. Un cliente público es cualquier aplicación cliente que no es de confianza para mantener la integridad de una contraseña secreta de forma segura. Esto incluye aplicaciones móviles, aplicaciones de escritorio y prácticamente cualquier aplicación que se ejecute en un dispositivo y necesite obtener access\_tokens. Si desea agregar la administración de identidades a una aplicación web mediante Azure AD B2C, debe usar [OpenID Connect](active-directory-b2c-reference-oidc.md) en lugar de OAuth 2.0.

Azure AD B2C extiende los flujos de OAuth 2.0 estándar para realizar algo más que una autorización y autenticación simples. Presenta el [**parámetro de directiva**](active-directory-b2c-reference-poliices.md), que le permite usar OAuth 2.0 para agregar experiencias de usuario a su aplicación como registro, inicio de sesión y administración de perfiles. Aquí le mostraremos cómo usar OAuth 2.0 y las directivas para implementar cada una de estas experiencias en sus aplicaciones nativas y obtener access\_tokens para tener acceso a las API web.

Las siguientes solicitudes HTTP de ejemplo usarán nuestro directorio de ejemplo de B2C, **fabrikamb2c.onmicrosoft.com**, así como nuestras directivas y la aplicación de ejemplo. Puede probar las solicitudes por usted mismo con estos valores, o bien puede reemplazarlos por los suyos propios. Obtenga más información acerca de cómo [obtener su propio directorio de B2C, aplicación y directivas](#use-your-own-b2c-directory).

## 1\. Obtener un código de autorización
El flujo de código de autorización comienza con el cliente que dirige al usuario al extremo `/authorize`. Esta es la parte interactiva del flujo, donde el usuario actuará realmente. En esta solicitud, el cliente indica los permisos que necesita adquirir del usuario en el parámetro `scope` y la directiva que se va a ejecutar en el parámetro `p`. A continuación se proporcionan tres ejemplos (con saltos de línea para facilitar la lectura), cada uno con una directiva diferente.

#### Uso de una directiva de inicio de sesión

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_sign_in
```

#### Uso de una directiva de registro

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_sign_up
```

#### Uso de una directiva de edición de perfil

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_edit_profile
```

| Parámetro | | Descripción |
| ----------------------- | ------------------------------- | ----------------------- |
| client\_id | requerido | El identificador de aplicación que el [Portal de Azure](https://portal.azure.com/) asignó a la aplicación. |
| response\_type | requerido | Debe incluir `code` para el flujo de código de autorización. |
| redirect\_uri | requerido | El redirect\_uri de su aplicación, a donde su aplicación puede enviar y recibir las respuestas de autenticación. Debe coincidir exactamente con uno de los redirect\_uris que registró en el portal, con la excepción de que debe estar codificado como URL. |
| ámbito | requerido | Una lista de ámbitos separada por espacios. Un valor de ámbito único indica a Azure AD los dos permisos que se solicitan. El ámbito `openid` indica un permiso para iniciar sesión para el usuario y obtener datos sobre el usuario en forma de **id\_tokens** (más información sobre esto más adelante). El ámbito `offline_access` indica que la aplicación necesitará un **refresh\_token** para un acceso de larga duración a los recursos. |
| response\_mode | recomendado | Especifica el método que debe usarse para enviar el authorization\_code resultante de nuevo a tu aplicación. Puede ser uno de 'query', 'form\_post' o 'fragment'.
| state | recomendado | Un valor incluido en la solicitud que se devolverá también en la respuesta del token. Puede ser una cadena de cualquier contenido que desee. Se utiliza normalmente un valor único generado de forma aleatoria para evitar los ataques de falsificación de solicitudes entre sitios. El estado también se usa para codificar información sobre el estado del usuario en la aplicación antes de que se haya producido la solicitud de autenticación, por ejemplo, la página en la que estaban o la directiva que se ejecutaba. |
| p | requerido | Indica la directiva que se va a ejecutar. Es el nombre de una directiva creada en su directorio de B2C, cuyo valor debe comenzar por "b2c\_1\_". Obtenga más información acerca de las directivas [aquí](active-directory-b2c-reference-policies.md). |
| símbolo del sistema | opcional | Indica el tipo de interacción necesaria con el usuario. El único valor válido en este momento es 'login', lo que obliga al usuario a escribir sus credenciales en esa solicitud. El inicio de sesión único no surtirá efecto. |

En este punto, se pedirá al usuario que complete el flujo de trabajo de la directiva. Esto puede implicar que el usuario tenga que escribir su nombre de usuario y contraseña, iniciar sesión con una identidad social, registrarse para el directorio o realizar cualquier otro número de pasos, en función de cómo esté definida la directiva. Una vez que el usuario complete la directiva, Azure AD devolverá una respuesta a su aplicación en el `redirect_uri` indicado usando el método especificado en el parámetro `response_mode`. La respuesta será exactamente la misma para cada uno de los casos anteriores, independientemente de la directiva que se ejecute.

Una respuesta correcta al usar `response_mode=query` tiene el siguiente aspecto:

```
GET urn:ietf:wg:oauth:2.0:oob?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...        // the authorization_code, truncated
&state=arbitrary_data_you_can_receive_in_the_response                // the value provided in the request
```

| Parámetro | Descripción |
| ----------------------- | ------------------------------- |
| código | El authorization\_code que solicitó la aplicación. La aplicación puede usar el código de autorización para solicitar un token de acceso para un recurso de destino. Los authorization\_codes son de muy corta duración, normalmente expiran después de unos 10 minutos. |
| state | Si se incluye un parámetro de estado en la solicitud, debería aparecer el mismo valor en la respuesta. La aplicación debería comprobar que los valores de estado de la solicitud y la respuesta son idénticos. |

Las respuestas de error también pueden enviarse al `redirect_uri` para que la aplicación pueda controlarlas adecuadamente:

```
GET urn:ietf:wg:oauth:2.0:oob?
error=access_denied
&error_description=The+user+has+cancelled+entering+self-asserted+information
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parámetro | Descripción |
| ----------------------- | ------------------------------- |
| error | Una cadena de código de error que puede utilizarse para clasificar los tipos de errores que se producen y para reaccionar ante ellos. |
| error\_description | Un mensaje de error específico que puede ayudar a un desarrollador a identificar la causa de un error de autenticación. |
| state | Si se incluye un parámetro de estado en la solicitud, debería aparecer el mismo valor en la respuesta. La aplicación debería comprobar que los valores de estado de la solicitud y la respuesta son idénticos. |


## 2\. Obtención de un token
Ahora que ha adquirido un authorization\_code, puede canjear el `code` por un token al recurso deseado mediante el envío de una solicitud `POST` al extremo `/token`: En la vista previa de Azure AD B2C, el único recurso para el que puede solicitar un token es la API web back-end de la aplicación. La convención usada para solicitar un token para sí mismo es usar el ámbito `openid`:

```
POST fabrikamb2c.onmicrosoft.com/v2.0/oauth2/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
	"grant_type": "authorization_code",
	"client_id": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6",
	"scope": "openid offline_access",
	"code": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...",
	"redirect_uri": "urn:ietf:wg:oauth:2.0:oob"
}
```

| Parámetro | | Descripción |
| ----------------------- | ------------------------------- | --------------------- |
| p | requerido | La directiva usada para adquirir el código de autorización. No se puede usar una directiva diferente en esta solicitud. **Tenga en cuenta que este parámetro se agrega a la cadena de consulta** pero no al cuerpo de POST. |
| client\_id | requerido | El identificador de aplicación que el [Portal de Azure](https://portal.azure.com/) asignó a la aplicación. |
| grant\_type | requerido | Debe ser `authorization_code` para el flujo de código de autorización. |
| ámbito | requerido | Una lista de ámbitos separada por espacios. Un valor de ámbito único indica a Azure AD los dos permisos que se solicitan. El ámbito `openid` indica un permiso para iniciar sesión para el usuario y obtener datos sobre el usuario en forma de **id\_tokens**. Se puede usar para obtener tokens para la propia API web back-end de la aplicación, representada por el mismo Id. de aplicación que el cliente. El ámbito `offline_access` indica que la aplicación necesitará un **refresh\_token** para un acceso de larga duración a los recursos. |
| código | requerido | El authorization\_code que adquirió en el primer segmento del flujo. |
| redirect\_uri | requerido | El redirect\_uri de la aplicación en la que recibió el authorization\_code. |

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

## 3\. Uso del token
Ahora que ha adquirido correctamente un `id_token`, puede usar el token en solicitudes a las API web de back-end mediante su inclusión en el encabezado `Authorization`:

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## 4\. Actualización del token
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
	"redirect_uri": "urn:ietf:wg:oauth:2.0:oob"
}
```

| Parámetro | | Descripción |
| ----------------------- | ------------------------------- | -------- |
| p | requerido | La directiva usada para adquirir el código de actualización original. No se puede usar una directiva diferente en esta solicitud. **Tenga en cuenta que este parámetro se agrega a la cadena de consulta** pero no al cuerpo de POST. |
| client\_id | requerido | El identificador de aplicación que el [Portal de Azure](https://portal.azure.com/) asignó a la aplicación. |
| grant\_type | requerido | Debe ser `refresh_token` para este segmento del flujo de código de autorización. |
| ámbito | requerido | Una lista de ámbitos separada por espacios. Un valor de ámbito único indica a Azure AD los dos permisos que se solicitan. El ámbito `openid` indica un permiso para iniciar sesión para el usuario y obtener datos sobre el usuario en forma de **id\_tokens**. Se puede usar para obtener tokens para la propia API web back-end de la aplicación, representada por el mismo Id. de aplicación que el cliente. El ámbito `offline_access` indica que la aplicación necesitará un **refresh\_token** para un acceso de larga duración a los recursos. |
| redirect\_uri | requerido | El redirect\_uri de la aplicación en la que recibió el authorization\_code. |
| refresh\_token | requerido | El refresh\_token original que adquirió en el segundo segmento del flujo. |

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

## Uso del directorio de B2C propio

Si desea probar estas solicitudes por sí mismo, primero debe realizar estos tres pasos y, a continuación, reemplazar los valores del ejemplo anterior por sus propios valores:

- [Crear un directorio de B2C](active-directory-b2c-get-started.md) y usar el nombre del directorio en las solicitudes.
- [Crear una aplicación](active-directory-b2c-app-registration.md) para obtener un Id. de aplicación y un redirect\_uri. Es posible que desee incluir un **cliente nativo** en la aplicación.
- [Crear directivas](active-directory-b2c-reference-policies.md) para obtener los nombres de las directivas.

<!---HONumber=AcomDC_0128_2016-->