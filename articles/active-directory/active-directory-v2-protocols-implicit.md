<properties
	pageTitle="Flujo implícito de Azure AD v2.0 | Microsoft Azure"
	description="Creación de aplicaciones web mediante la implementación del flujo implícito de la versión 2.0 de Azure AD para aplicaciones de una sola página."
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
	ms.topic="article"
	ms.date="01/11/2016"
	ms.author="dastrock"/>

# Protocolos de la versión 2.0: uso del flujo implícito para las SPA
Con las aplicaciones v2.0, puede iniciar la sesión de los usuarios en sus aplicaciones de una página tanto con cuentas de Microsoft personales como profesionales o educativas. Las aplicaciones de una sola página y otras aplicaciones JavaScript que se ejecutan principalmente en un explorador, se enfrentan con algunos retos interesantes por lo que se refiere a la autenticación:

- Las características de seguridad de estas aplicaciones son significativamente diferentes de las de las aplicaciones web tradicionales basadas en el servidor.
- Muchos proveedores de identidades y servidores de autorización no admiten solicitudes CORS por razones de seguridad bien documentadas.
- Los redireccionamientos del explorador a una página completa fuera de la aplicación se vuelven especialmente invasivos para la experiencia del usuario.

Para estas aplicaciones (por ejemplo, AngularJS, Ember.js, React.js, etc.), Azure AD admite el flujo de concesión implícita de OAuth 2.0. El flujo implícito se describe en la [especificación de OAuth 2.0](http://tools.ietf.org/html/rfc6749#section-4.2). Su principal ventaja es que hace posible que la aplicación obtenga tokens de Azure AD sin necesidad de realizar un intercambio de credenciales con el servidor back-end. De esta forma, la aplicación puede iniciar la sesión del usuario, mantenerla y recibir tokens para otras API web, y todo dentro del código de cliente de JavaScript. Existen algunas consideraciones de seguridad importantes que se deben tener en cuenta al usar el flujo implícito, especialmente en lo referente a la [suplantación de identidad del cliente](http://tools.ietf.org/html/rfc6749#section-10.3) y el [usuario](http://tools.ietf.org/html/rfc6749#section-10.3).

Si quiere usar el flujo implícito y Azure AD para agregar autenticación a la aplicación JavaScript, se recomienda el uso de nuestra biblioteca JavaScript de código abierto, [adal.js](https://github.com/AzureAD/azure-activedirectory-library-for-js). Puede encontrar [aquí](active-directory-appmodel-v2-overview.md#getting-started) algunos tutoriales de AngularJS que le ayudarán a empezar.

Sin embargo, si prefiere no usar una biblioteca en su aplicación de una página y enviar mensajes de protocolo usted mismo, siga estos pasos generales.

> [AZURE.NOTE]
    Esta información se aplica a la vista previa pública del modelo de aplicaciones v2.0. Para obtener instrucciones sobre cómo integrar con el servicio de Azure AD disponible con carácter general, consulte la [Guía para desarrolladores de Azure Active Directory](active-directory-developers-guide.md).

## Envío de la solicitud de inicio de sesión

Al principio, para iniciar la sesión del usuario en la aplicación, puede enviar una solicitud de autorización [OpenID Connect](active-directory-v2-protocols-oidc.md) y obtener un `id_token` del punto de conexión v2.0:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token+token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&response_mode=fragment&state=12345&nonce=678910
```
> [AZURE.TIP] Pruebe a pegar esta solicitud en un explorador.


| Parámetro | | Descripción |
| ----------------------- | ------------------------------- | --------------- |
| client\_id | requerido | El id. de aplicación que el portal de registro ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) asignó a su aplicación. |
| response\_type | requerido | Debe incluir `id_token` para el inicio de sesión en OpenID Connect. También puede incluir el response\_type `token`. El uso de `token` aquí permitirá a la aplicación recibir un token de acceso inmediatamente desde el punto de conexión autorizado sin tener que realizar una segunda solicitud para autorizar el punto de conexión. Si utiliza el response\_type `token`, el parámetro `scope` debe contener un ámbito que indica para qué recurso se va a emitir el token. |
| redirect\_uri | recomendado | El redirect\_uri de su aplicación, a donde su aplicación puede enviar y recibir las respuestas de autenticación. Debe coincidir exactamente con uno de los redirect\_uris que registró en el portal, con la excepción de que debe estar codificado como URL. |
| ámbito | requerido | Una lista de ámbitos separada por espacios. Asegúrese de que incluye el ámbito `openid` para OpenID Connect, lo que se traduce en el permiso de "inicio de sesión" en la interfaz de usuario de consentimiento. Opcionalmente, también puede incluir los [ámbitos](active-directory-v2-scopes.md) `email` o `profile` para obtener acceso a datos de usuario adicionales. También puede incluir otros ámbitos en esta solicitud para solicitar consentimiento para diversos recursos. |
| response\_mode | recomendado | Especifica el método que debe usarse para enviar el token resultante de nuevo a la aplicación. Debe ser `fragment` para el flujo implícito. |
| state | recomendado | Un valor incluido en la solicitud que se devolverá también en la respuesta del token. Puede ser una cadena de cualquier contenido que desee. Se utiliza normalmente un valor único generado de forma aleatoria para [evitar los ataques de falsificación de solicitudes entre sitios](http://tools.ietf.org/html/rfc6749#section-10.12). El estado también se usa para codificar información sobre el estado del usuario en la aplicación antes de que se haya producido la solicitud de autenticación, por ejemplo, la página o vista en la que estaban. |
| valor de seguridad | requerido | Un valor incluido en la solicitud, generada por la aplicación, que se incluirá en el id\_token resultante como una notificación. La aplicación puede comprobar este valor para mitigar los ataques de reproducción de token. Normalmente, el valor es una cadena única aleatoria que puede utilizarse para identificar el origen de la solicitud. |
| símbolo del sistema | opcional | Indica el tipo de interacción necesaria con el usuario. Los únicos valores válidos en este momento son 'login', 'none' y 'consent'. `prompt=login` obligará al usuario a escribir sus credenciales en esa solicitud, negando el inicio de sesión único. En el lado opuesto, `prompt=none` se asegurará de que al usuario no se le presenta ninguna solicitud interactiva del tipo que sea. Si no se puede completar la solicitud sin notificaciones mediante el inicio de sesión único, el punto de conexión v2.0 devolverá un error. `prompt=consent` desencadenará el cuadro de diálogo de consentimiento de OAuth después de que el usuario inicia sesión, y solicitará a este que conceda permisos a la aplicación. |
| login\_hint | opcional | Puede usarse para rellenar previamente el campo de nombre de usuario y dirección de correo electrónico de la página de inicio de sesión del usuario, si sabe su nombre de usuario con antelación. A menudo las aplicaciones usarán este parámetro durante la reautenticación, habiendo extraído ya el nombre de usuario de un inicio de sesión anterior utilizando la notificación `preferred_username`. |
| domain\_hint | opcional | Puede ser `consumers` o `organizations`. Si se incluye, omitirá el proceso de detección basado en correo electrónico por el que pasa el usuario en la página de inicio de sesión de v2.0, con lo que la experiencia de usuario será ligeramente más sencilla. A menudo las aplicaciones usarán este parámetro durante la reautenticación, extrayendo la notificación `tid` de id\_token. Si el valor de la notificación `tid` es `9188040d-6c67-4c5b-b112-36a304b66dad`, debe usar `domain_hint=consumers`. De lo contrario, use `domain_hint=organizations`. |

En este punto, se le pedirá al usuario que escriba sus credenciales y que complete la autenticación. El punto de conexión v2.0 también garantiza que el usuario ha dado su consentimiento a los permisos indicados en el parámetro de la consulta `scope`. Si el usuario no ha dado su consentimiento a alguno de esos permisos, se le solicitará al usuario su consentimiento para los permisos necesarios. Aquí puede encontrar información detallada sobre los [permisos, el consentimiento y las aplicaciones multiempresa](active-directory-v2-scopes.md).

Una vez que el usuario se autentica y otorga su consentimiento, el punto de conexión v2.0 devuelve una respuesta a su aplicación en el `redirect_uri` indicado mediante el método especificado en el parámetro `response_mode`.

#### Respuesta correcta
Una respuesta correcta que usa `response_mode=fragment` y `response_type=id_token+token` es como la siguiente, con saltos de línea para mejorar la legibilidad:

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&token_type=Bearer
&expires_in=3599
&scope=https%3a%2f%2fgraph.microsoft.com%2fmail.read 
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
```

| Parámetro | Descripción |
| ----------------------- | ------------------------------- |
| access\_token | Incluido si `response_type` incluye `token`. El token de acceso que la aplicación ha solicitado, en este caso para Microsoft Graph. El token de acceso no se debe descodificar o inspeccionar en forma alguna, se puede tratar como una cadena opaca. |
| token\_type | Incluido si `response_type` incluye `token`. Siempre será `Bearer`. |
| expires\_in | Incluido si `response_type` incluye `token`. Indica el número de segundos que el token es válido para el almacenamiento en caché. |
| ámbito | Incluido si `response_type` incluye `token`. Indica los ámbitos para los que será válido access\_token. |
| ID\_token | El id\_token que solicitó la aplicación. Puede usar el id\_token para comprobar la identidad del usuario y comenzar una sesión con el usuario. Se incluyen más detalles sobre id\_tokens y su contenido en la [referencia a los tokens del punto de conexión v2.0](active-directory-v2-tokens.md). |
| state | Si se incluye un parámetro de estado en la solicitud, debería aparecer el mismo valor en la respuesta. La aplicación debería comprobar que los valores de estado de la solicitud y la respuesta son idénticos. |
| id\_token\_expires\_in | Durante cuánto tiempo es válido el token de id. (en segundos). |


#### Respuesta de error
Las respuestas de error también pueden enviarse al `redirect_uri` para que la aplicación pueda controlarlas adecuadamente:

```
GET https://localhost/myapp/#
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parámetro | Descripción |
| ----------------------- | ------------------------------- |
| error | Una cadena de código de error que puede utilizarse para clasificar los tipos de errores que se producen y para reaccionar ante ellos. |
| error\_description | Un mensaje de error específico que puede ayudar a un desarrollador a identificar la causa de un error de autenticación. |

## Validar el id\_token
Recibir un solo id\_token no es suficiente para autenticar al usuario; debe validar la firma del id\_token y comprobar las notificaciones en el token según los requisitos de su aplicación. El punto de conexión v2.0 usa [tokens web JSON (JWT)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) y criptografía de clave pública para firmar los tokens y comprobar que son válidos.

Puede elegir validar el `id_token` en el código de cliente, pero una práctica común es enviar el `id_token` a un servidor back-end y realizar allí las validaciones. Una vez haya validado la firma del id\_token, se le solicitará que compruebe algunas notificaciones: Consulte la [referencia de los tokens de la versión 2.0](active-directory-v2-tokens.md) para más información, incluyendo las secciones [Validación de los tokens](active-directory-v2-tokens.md#validating-tokens) y [Validación de la firma](active-directory-v2-tokens.md#validating-tokens). Se recomienda hacer uso de una biblioteca para analizar y validar los tokens, hay al menos una disponible para la mayoría de los lenguajes y plataformas. <!--TODO: Improve the information on this-->

Se recomienda que valide notificaciones adicionales según su escenario. Algunas validaciones comunes incluyen:

- Asegurarse de que la organización/el usuario se ha registrado en la aplicación.
- Asegurarse de que el usuario tiene la autorización/los privilegios adecuados
- Asegurarse de que se haya producido un determinado nivel de autenticación, como la autenticación multifactor.

Para más información sobre las notificaciones en un id\_token, consulte la [referencia a los tokens del modelo de aplicaciones v2.0](active-directory-v2-tokens.md).

Una vez que haya validado completamente el id\_token, puede iniciar una sesión con el usuario y usar las notificaciones en el id\_token para obtener información sobre el usuario en su aplicación. Esta información puede utilizarse para su visualización, registros, autorizaciones, etc.

## Obtención de tokens de acceso

Ahora que ha iniciado la sesión del usuario en su aplicación de una página, puede obtener tokens de acceso para llamar a las API web protegidas por Azure AD, como las de [Microsoft Graph](https://graph.microsoft.io). Incluso si ya recibió un token mediante response\_type `token`, puede utilizar este método para adquirir tokens para recursos adicionales sin tener que redirigir al usuario para volver a iniciar sesión.

En el flujo normal de OpenID Connect y OAuth, haría esto mediante una solicitud al punto de conexión v2.0 `/token`. Sin embargo, el punto de conexión v2.0 no admite solicitudes CORS, así que las llamadas a AJAX para obtener y actualizar los tokens están fuera de nuestro alcance. En su lugar, puede usar el flujo implícito en un iframe oculto para obtener nuevos tokens para otras API web:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&response_mode=fragment&state=12345&nonce=678910&prompt=none&domain_hint=organizations&login_hint=myuser@mycompany.com
```

> [AZURE.TIP] Pruebe a pegar esta solicitud en un explorador (pero si desea que se realice correctamente, modifique primero los valores de domain\_hint & login\_hint)

| Parámetro | | Descripción |
| ----------------------- | ------------------------------- | --------------- |
| client\_id | requerido | El id. de aplicación que el portal de registro ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) asignó a su aplicación. |
| response\_type | requerido | Debe incluir `id_token` para el inicio de sesión en OpenID Connect. También puede incluir otros elementos response\_type como `code`. |
| redirect\_uri | recomendado | El redirect\_uri de su aplicación, a donde su aplicación puede enviar y recibir las respuestas de autenticación. Debe coincidir exactamente con uno de los redirect\_uris que registró en el portal, con la excepción de que debe estar codificado como URL. |
| ámbito | requerido | Una lista de ámbitos separada por espacios. Para obtener los tokens, incluya todos los [ámbitos](active-directory-v2-scopes.md) que necesita para el recurso de interés. |
| response\_mode | recomendado | Especifica el método que debe usarse para enviar el token resultante de nuevo a la aplicación. Puede ser `query`, `form_post`, o `fragment`. |
| state | recomendado | Un valor incluido en la solicitud que se devolverá también en la respuesta del token. Puede ser una cadena de cualquier contenido que desee. Se utiliza normalmente un valor único generado de forma aleatoria para evitar los ataques de falsificación de solicitudes entre sitios. El estado también se usa para codificar información sobre el estado del usuario en la aplicación antes de que se haya producido la solicitud de autenticación, por ejemplo, la página o vista en la que estaban. |
| valor de seguridad | requerido | Un valor incluido en la solicitud, generada por la aplicación, que se incluirá en el id\_token resultante como una notificación. La aplicación puede comprobar este valor para mitigar los ataques de reproducción de token. Normalmente, el valor es una cadena única aleatoria que puede utilizarse para identificar el origen de la solicitud. |
| símbolo del sistema | requerido | Para actualizar y obtener tokens en un iframe oculto, debe usar `prompt=none` para asegurarse de que el iframe no se bloquea en la página de inicio de sesión de v2.0 y vuelve inmediatamente. |
| login\_hint | requerido | Para actualizar y obtener tokens en un iframe oculto, tiene que incluir el nombre de usuario en esta sugerencia para distinguir entre varias sesiones que el usuario pueda tener en un momento dado. Puede extraer el nombre de usuario de un inicio de sesión anterior utilizando la notificación `preferred_username`. |
| domain\_hint | requerido | Puede ser `consumers` o `organizations`. Para actualizar y obtener tokens en un iframe oculto, tiene que incluir el domain\_hint en la solicitud. Debe extraer la notificación `tid` de id\_token de un inicio de sesión anterior para determinar qué valor va a usar. Si el valor de la notificación `tid` es `9188040d-6c67-4c5b-b112-36a304b66dad`, debe usar `domain_hint=consumers`. De lo contrario, use `domain_hint=organizations`. |

Gracias al parámetro `prompt=none`, esta solicitud tendrá éxito o dará error inmediatamente y volverá a la aplicación. Se enviará una respuesta correcta a la aplicación en el `redirect_uri` indicado, mediante el método especificado en el parámetro `response_mode`.

#### Respuesta correcta
Una respuesta correcta al usar `response_mode=fragment` tiene el siguiente aspecto:

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
&token_type=Bearer
&expires_in=3600
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read
```

| Parámetro | Descripción |
| ----------------------- | ------------------------------- |
| access\_token | El token que solicitó la aplicación. |
| token\_type | Siempre será `Bearer`. |
| state | Si se incluye un parámetro de estado en la solicitud, debería aparecer el mismo valor en la respuesta. La aplicación debería comprobar que los valores de estado de la solicitud y la respuesta son idénticos. |
| expires\_in | Durante cuánto tiempo es válido el token de acceso (en segundos). |
| ámbito | Los ámbitos para los que el token de acceso es válido. |

#### Respuesta de error
Las respuestas de error también pueden enviarse al `redirect_uri` para que la aplicación pueda controlarlas adecuadamente. En el caso de `prompt=none`, un error esperado será:

```
GET https://localhost/myapp/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Parámetro | Descripción |
| ----------------------- | ------------------------------- |
| error | Una cadena de código de error que puede utilizarse para clasificar los tipos de errores que se producen y para reaccionar ante ellos. |
| error\_description | Un mensaje de error específico que puede ayudar a un desarrollador a identificar la causa de un error de autenticación. |

Si recibe este error en la solicitud de iframe, el usuario debe iniciar sesión de nuevo de manera interactiva para recuperar un nuevo token. Puede elegir tratar este caso de la manera que más sentido tenga para su aplicación.

## Actualización de tokens

Tanto los elementos `id_token` como `access_token` caducan tras un corto período de tiempo, así que su aplicación debe estar preparada para actualizar estos tokens de manera periódica. Para actualizar cualquier tipo de token, puede realizar la misma solicitud de iframe oculto que antes mediante el parámetro `prompt=none` para controlar el comportamiento de Azure AD. Si quiere recibir un nuevo `id_token`, asegúrese de usar `response_type=id_token` y `scope=openid`, así como un parámetro `nonce`.


## Envío de una solicitud de cierre de sesión

Actualmente, la versión preliminar del modelo de aplicaciones v2.0 no admite el `end_session_endpoint` de OpenIdConnect. Esto significa que la aplicación no puede enviar una solicitud al extremo v2.0 para finalizar una sesión de usuario y borrar las cookies establecidas por el extremo v2.0. Para cerrar la sesión de un usuario, la aplicación puede simplemente finalizar su propia sesión con el usuario y dejar la sesión del usuario con el extremo v2.0 intacta. La próxima vez que el usuario intente iniciar sesión, verá una página de "elegir cuenta", en la que se enumeran sus cuentas con inicio de sesión activo. En esa página, el usuario puede elegir cerrar la sesión de cualquier cuenta, finalizando así la sesión con el extremo v2.0.

<!--

When you wish to sign the user out of the  app, it is not sufficient to clear your app's cookies or otherwise end the session with the user.  You must also redirect the user to the v2.0 endpoint for sign out.  If you fail to do so, the user will be able to re-authenticate to your app without entering their credentials again, because they will have a valid single sign-on session with the v2.0 endpoint.

You can simply redirect the user to the `end_session_endpoint` listed in the OpenID Connect metadata document:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
```

| Parameter | | Description |
| ----------------------- | ------------------------------- | ------------ |
| post_logout_redirect_uri | recommended | The URL which the user should be redirected to after successful logout.  If not included, the user will be shown a generic message by the v2.0 endpoint.  |

-->

<!---HONumber=AcomDC_0128_2016-->