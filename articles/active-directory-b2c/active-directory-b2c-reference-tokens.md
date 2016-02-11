<properties
	pageTitle="Vista previa de Azure AD B2C | Microsoft Azure"
	description="Los tipos de tokens emitidos en la vista previa de Azure AD B2C."
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

# Vista previa de Azure AD B2C: referencia del token

Azure AD B2C emite varios tipos de tokens de seguridad en el procesamiento de cada [flujo de autenticación](active-directory-b2c-apps.md). Este documento describe el formato, las características de seguridad y el contenido de cada tipo de token.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Tipos de tokens

Azure AD B2C admite el [Protocolo de autorización de OAuth 2.0](active-directory-b2c-reference-protocols.md), que usa access\_tokens y refresh\_tokens. También admite la autenticación e inicio de sesión mediante [OpenID Connect](active-directory-b2c-reference-protocols.md), que presenta un tercer tipo de token, el id\_token. Todos estos tokens se representan como un "token de portador".

Un token portador es un token de seguridad ligero que concede al "portador" acceso a un recurso protegido. En este sentido, el "portador" es cualquier parte que pueda presentar el token. Aunque una parte debe autenticarse primero con Azure AD para recibir el token portador, si no se realizan los pasos necesarios para asegurar el token en la transmisión y el almacenamiento, este puede interceptarse y ser utilizado por un usuario no deseado. Mientras que algunos tokens de seguridad disponen de un mecanismo integrado para evitar ser usados por partes no autorizadas, los tokens portadores no tienen este mecanismo y deben transportarse en un canal seguro como, por ejemplo, la seguridad de la capa de transporte (HTTPS). Si un token portador se transmite sin cifrar, un usuario malintencionado puede utilizar un ataque de tipo "Man in the middle" para adquirir el token y usarlo para obtener acceso sin autorización a un recurso protegido. Los mismos principios de seguridad se aplican al almacenamiento o almacenamiento en caché de tokens portadores para su uso posterior. Asegúrate siempre de que la aplicación transmite y almacena los tokens de portador de manera segura. Para otras consideraciones sobre la seguridad de los tokens portadores, consulte la [Sección 5 de RFC 6750](http://tools.ietf.org/html/rfc6750).

Muchos de los tokens emitidos por Azure AD B2C se implementan como tokens web JSON o JWT. Un JWT es un medio compacto y seguro de la dirección URL para transferir información entre dos partes. La información contenida en los JWT se conoce como "notificaciones", o aserciones de información sobre el portador y el asunto del token. Las notificaciones de JWT son los objetos JSON codificados y serializados para su transmisión. Como los JWT emitidos por Azure AD B2C están firmados, pero no cifrados, puedes inspeccionar fácilmente el contenido de un JWT con fines de depuración. Hay varias herramientas disponibles para hacerlo, como [calebb.net](https://calebb.net). Para obtener más información sobre los JWT, consulte la [especificación de JWT](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html).

## Id\_Tokens

Los Id\_tokens son una forma de token de seguridad que la aplicación recibe de los extremos `authorize` y `token` de Azure AD B2C. Se representan como [JWT](#types-of-tokens) y contienen notificaciones que puede usar para identificar el usuario en la aplicación. Cuando se adquiere desde el extremo `authorize`, los id\_tokens suelen usarse para que el usuario inicie sesión en una aplicación web. Cuando se adquiere desde el extremo `token`, los id\_tokens se pueden enviar en solicitudes HTTP al comunicarse entre dos componentes de la misma aplicación o servicio. Puedes utilizar las notificaciones de un id\_token como te convenga. Normalmente se usan para mostrar información de la cuenta o tomar decisiones de control de acceso en una aplicación.

En ese momento los id\_tokens están firmados, pero no cifrados. Cuando la aplicación o API recibe un id\_token, tiene que [validar la firma](#validating-tokens) para probar la autenticidad del token y validar algunas notificaciones en el token para demostrar su validez. Las notificaciones validadas por una aplicación varían dependiendo de los requisitos de escenario, pero hay algunas [validaciones de notificación comunes](#validating-tokens) que la aplicación debe realizar en todos los escenarios.

A continuación se muestra información detallada sobre las notificaciones de los id\_tokens, así como un ejemplo de un id\_token. Ten en cuenta que las notificaciones de los id\_tokens no se devuelven en ningún orden concreto. Además, se pueden introducir nuevas notificaciones en los id\_tokens en cualquier momento y no se debe interrumpir la aplicación cuando se introducen nuevas notificaciones. La siguiente lista incluye las notificaciones que la aplicación puede interpretar de forma confiable en el momento de redactar este artículo. Para la práctica, intenta inspeccionar las notificaciones del id\_token de ejemplo pegándolo en [calebb.net](https://calebb.net). Si es necesario, se pueden encontrar más detalles en la [especificación de OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html).

#### Ejemplo de un Id\_Token
```
// Line breaks for display purposes only

eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IklkVG9rZW5TaWduaW5nS2V5Q29udGFpbmVyIn0.
eyJleHAiOjE0NDIzNjAwMzQsIm5iZiI6MTQ0MjM1NjQzNCwidmVyIjoiMS4wIiwiaXNzIjoiaHR0cHM6Ly9s
b2dpbi5taWNyb3NvZnRvbmxpbmUuY29tLzc3NTUyN2ZmLTlhMzctNDMwNy04YjNkLWNjMzExZjU4ZDkyNS92
Mi4wLyIsImFjciI6ImIyY18xX3NpZ25faW5fc3RvY2siLCJzdWIiOiJOb3Qgc3VwcG9ydGVkIGN1cnJlbnRs
eS4gVXNlIG9pZCBjbGFpbS4iLCJhdWQiOiI5MGMwZmU2My1iY2YyLTQ0ZDUtOGZiNy1iOGJiYzBiMjlkYzYi
LCJpYXQiOjE0NDIzNTY0MzQsImF1dGhfdGltZSI6MTQ0MjM1NjQzNCwiaWRwIjoiZmFjZWJvb2suY29tIn0.
h-uiKcrT882pSKUtWCpj-_3b3vPs3bOWsESAhPMrL-iIIacKc6_uZrWxaWvIYkLra5czBcGKWrYwrAC8ZvQe
DJWZ50WXQrZYODEW1OUwzaD_I1f1HE0c2uvaWdGXBpDEVdsD3ExKaFlKGjFR2V7F-fPThkVDdKmkUDQX3bVc
yyj2V2nlCQ9jd7aGnokTPfLfpOjuIrTsAdPcGpe5hfSEuwYDmqOJjGs9Jp1f-eSNEiCDQOaTBSvr479L5ptP
XWeQZyX2SypN05Rjr05bjZh3j70ZUimiocfJzjibeoDCaQTz907yAg91WYuFOrQxb-5BaUoR7K-O7vxr2M-_
CQhoFA

```

#### Notificaciones de los Id\_Tokens

Con Azure AD B2C, tendrá un control preciso sobre el contenido de los tokens. Puede configurar [directivas](active-directory-b2c-reference-policies.md) para enviar un determinado conjunto de datos de usuario en notificaciones que la aplicación requiere para sus operaciones. Estas notificaciones pueden incluir propiedades estándar como las propiedades `displayName` y `emailAddress` del usuario, así como [atributos de usuario personalizados](active-directory-b2c-reference-custom-attr) que se pueden definir en el directorio B2C. Sin embargo, cada id\_token que recibe contendrá un determinado conjunto de notificaciones relacionadas con la seguridad que las aplicaciones pueden usar para autenticar usuarios y solicitudes de manera segura. A continuación se muestran las notificaciones que se espera que existan en cada id\_token emitido por Azure AD B2C; cualquier otra notificación estará determinada por la directiva.

| Nombre | Notificación | Valor de ejemplo | Descripción |
| ----------------------- | ------------------------------- | ------------ | --------------------------------- |
| Público | `aud` | `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` | Identifica al destinatario previsto del token. En los id\_tokens, la audiencia es el Id. de aplicación de la aplicación, como se asigna a tu aplicación en el portal de registro de la aplicación. La aplicación tiene que validar este valor y rechazar el token si no coincide. |
| Emisor | `iss` | `https://login.microsoftonline.com/775527ff-9a37-4307-8b3d-cc311f58d925/v2.0/` | Identifica el servicio de token de seguridad (STS) que construye y devuelve el token, así como el directorio de Azure AD en el que se autenticó al usuario. La aplicación tiene que validar la notificación del emisor para asegurarse de que el token proviene del extremo de la versión 2.0. |
| Emitido a las | `iat` | `1438535543` | La hora en que se emitió el token, que se representa en tiempo de época. |
| Fecha de expiración | `exp` | `1438539443` | La hora en que el token deja de ser válido, que se representa en tiempo de época. La aplicación tiene que usar esta notificación para comprobar la validez de la duración del token. |
| No antes de | `nbf` | `1438535543` | Hora a la que el token pasa a ser válido, representada en tiempo de época. Normalmente es la misma que la hora de emisión. La aplicación tiene que usar esta notificación para comprobar la validez de la duración del token. |
| Versión | `ver` | `1.0` | La versión del id\_token, tal como se define por Azure AD. |
| Código Hash | `c_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | El código hash se incluye en los id\_tokens solo cuando se emite el id\_token junto con un código de autorización de OAuth 2.0. Se puede usar para validar la autenticidad de un código de autorización. Consulta la [especificación OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) para más información sobre cómo realizar esta validación. |
| Hash de Token de acceso | `at_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | El hash de token de acceso se incluye en los id\_tokens solo cuando se emite el id\_token junto con un código de autorización de OAuth 2.0. Se puede usar para validar la autenticidad de un token de acceso. Consulta la [especificación OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) para más información sobre cómo realizar esta validación. |
| Valor de seguridad | `nonce` | `12345` | El valor de seguridad es una estrategia para mitigar los ataques de reproducción de los tokens. La aplicación puede especificar un valor de seguridad en una solicitud de autorización mediante el parámetro de consulta `nonce`. El valor que se proporciona en la solicitud se emitirá en la notificación `nonce` del id\_token, sin modificaciones. Esto permite a la aplicación comprobar el valor con respecto al valor que especifica en la solicitud, que asocia la sesión de la aplicación con un id\_token determinado. La aplicación tiene que realizar esta validación durante el proceso de validación del id\_token. |
| Asunto | `sub` | `Not supported currently. Use oid claim.` | La entidad de seguridad sobre la que el token declara información como, por ejemplo, el usuario de una aplicación. Este valor es inmutable y no se puede reasignar o volver a usar, por lo que se puede usar para realizar comprobaciones de autorización de forma segura, por ejemplo, cuando se usa el token para tener acceso a un recurso. Sin embargo, la notificación de asunto no ha implementado todavía en la vista previa de Azure AD B2C. En lugar de usar la notificación de asunto para la autorización, debe configurar las directivas para incluir la notificación `oid` del identificador de objeto y usar su valor para identificar a los usuarios. |
| Referencia de clase de contexto de autenticación | `acr` | `b2c_1_sign_in` | El nombre de directiva que se usó para adquirir el id\_token. |
| Hora de autenticación | `auth_time | `1438535543` | Hora a la que el usuario especificó sus credenciales por última vez en tiempo de época. |



## Tokens de acceso

En la actualidad, Azure AD B2C no emite tokens de acceso. No se admite la autenticación entre dos entidades diferentes en esta primera fase de la vista previa: sin embargo, puede usar id\_tokens para comunicarse entre los componentes de la misma aplicación. Para obtener información acerca de las aplicaciones y escenarios de autenticación admitidos por la vista previa pública de Azure AD B2C, consulte el [artículo de aplicaciones B2C](active-directory-b2c-apps.md).

## Tokens de actualización

Los tokens de actualización son tokens de seguridad que la aplicación puede usar para adquirir nuevos id\_tokens y access\_tokens en un flujo de OAuth 2.0. Permite a la aplicación obtener acceso a largo plazo a los recursos en nombre de un usuario sin necesidad de que intervenga el usuario.

Para recibir una actualización en una respuesta de token, la aplicación tiene que solicitar el ámbito `offline_acesss`. Para obtener más información sobre el ámbito `offline_access`, consulte la [referencia de protocolo de Azure AD B2C](active-directory-b2c-reference-protocols.md).

Los tokens de actualización son, y siempre serán, totalmente opacos para tu aplicación. Los emite Azure AD y solo los puede inspeccionar e interpretar Azure AD. También son de larga duración, pero no se debe componer la aplicación esperando que un token de actualización dure por cualquier período de tiempo. Los tokens de actualización pueden ser invalidados en cualquier momento por varios motivos. La única forma para que tu aplicación sepa si un token de actualización es válido es intentar canjearlo mediante una solicitud de token a Azure AD.

Cuando se canjea un token de actualización por un nuevo token (y si se concedió el ámbito `offline_access` a la aplicación), se recibe un nuevo token de actualización en la respuesta del token. Es necesario guardar el token de actualización recién emitido y reemplazar el usado en la solicitud. Esto garantizará que los tokens de actualización sigan siendo válidos mientras sea posible.

## Validación de los tokens

En este momento, la única validación de tokens que deberían realizar tus aplicaciones es la validación de id\_tokens. Para validar un id\_token, la aplicación tiene que validar la firma del id\_token y las notificaciones del id\_token.

<!-- TODO: Link -->
Hay muchas bibliotecas de código abierto disponibles para validar los JWT según el lenguaje de preferencia. Se recomienda explorar esas opciones en lugar de implementar su propia lógica de validación. Esta información será útil para averiguar cómo usar adecuadamente dichas bibliotecas.

#### Validación de la firma
Un JWT contiene tres segmentos, que están separados por el carácter `.`. El primer segmento se conoce como el **encabezado**, el segundo como el **cuerpo** y el tercero como la **firma**. El segmento de firma se puede utilizar para validar la autenticidad del id\_token para que la aplicación pueda confiar en él.

Los id\_tokens se firman con algoritmos de cifrado asimétrico estándar del sector, como RSA 256. El encabezado del id\_token contiene información sobre el método de cifrado y la clave utilizados para firmar el token:

```
{
		"typ": "JWT",
		"alg": "RS256",
		"kid": "GvnPApfWMdLRi8PDmisFn7bprKg"
}
```

La notificación `alg` indica el algoritmo que se usó para firmar el token, mientras las notificaciones `kid` y `x5t` indican la clave pública concreta que se usó para firmar el token.

En cualquier momento, Azure AD puede firmar un id\_token mediante cualquiera de un determinado conjunto de pares de claves pública y privada. Azure AD rota los posibles conjuntos de claves de forma periódica, por lo que su aplicación debería estar escrita para manejar esos cambios de clave automáticamente. Una frecuencia razonable para comprobar las actualizaciones de las claves públicas usadas por Azure AD es aproximadamente 24 horas.

Azure AD B2C tiene un extremo de metadatos OpenID Connect, que permite a una aplicación obtener información sobre Azure AD B2C en tiempo de ejecución. En esta información se incluyen los extremos, los contenidos del token y las claves de firma de los token. Hay un documento de metadatos JSON para cada directiva en su directorio B2C. Por ejemplo, el documento de metadatos para la directiva `b2c_1_sign_in` en `fabrikamb2c.onmicrosoft.com` se encuentra en:

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

Puedes adquirir los datos de las claves de firmas necesarios para validar la firma utilizando el documento de metadatos de OpenID Connect, ubicado en:

```
https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in
```

donde `fabrikamb2c.onmicrosoft.com` es el directorio b2c usado para autenticar al usuario y `b2c_1_sign_in` es la directiva que se emplea para adquirir el id\_token. Para determinar qué directiva se usó en la firma de un id\_token (y de dónde obtener los metadatos), tiene dos opciones. En primer lugar, se incluye el nombre de la directiva en la notificación `acr` en id\_token. Se pueden analizar las notificaciones fuera del cuerpo del JWT mediante la descodificación del cuerpo en base 64 y la deserialización de la cadena JSON resultante. La notificación `acr` será el nombre de la directiva que se usó para emitir el id\_token. La otra opción consiste en codificar la directiva en el valor del parámetro `state` al emitir la solicitud y descodificarla para determinar qué directiva se ha usado. Cualquiera de los métodos es perfectamente válido.

El documento de metadatos es un objeto JSON que contiene varias piezas útiles de información, como la ubicación de los diferentes extremos necesarios para realizar la autenticación de OpenID Connect. También incluye un `jwks_uri`, que proporciona la ubicación del conjunto de claves públicas que se usan para los tokens de firma. A continuación se muestra esa ubicación, pero es mejor capturar esa ubicación dinámicamente utilizando el documento de metadatos y analizando el `jwks_uri`:

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`

El documento JSON que se encuentra en esta dirección url contiene toda la información de clave pública en uso en ese momento en particular. La aplicación puede usar las notificaciones `kid` o `x5t` en el encabezado de JWT para seleccionar la clave pública que se ha usado en este documento para firmar un determinado token. Después, puede realizar la validación de la firma mediante la clave pública correcta y el algoritmo indicado.

La realización de la validación de la firma queda fuera del ámbito de este documento, pero hay muchas bibliotecas de código abierto disponibles para ayudarte a hacerlo si es necesario.

#### Validación de las notificaciones
Cuando la aplicación o API recibe un id\_token, también debe realizar algunas comprobaciones de las notificaciones del id\_token. Entre ellos se incluyen los siguientes:

- Notificación **Audiencia**: comprueba que el id\_token se diseñó para proporcionarse a la aplicación.
- Notificaciones **No antes de** y **Hora de expiración**: comprueban que el id\_token no ha expirado.
- Notificación **Emisor**: comprueba que el token fue emitido realmente a la aplicación por Azure AD.
- **Valor de seguridad**: para mitigar ataques de reproducción de tokens.

En la [sección id\_token](#id_tokens) situada arriba se incluyen los detalles de los valores esperados para estas notificaciones.

## Vigencia de los tokens

Las siguientes vigencias de tokens solo se ofrecen para tu conocimiento, ya que pueden ayudar a desarrollar y depurar aplicaciones. Las aplicaciones no se deben redactar permitiendo que cualquiera de estas duraciones permanezca constante ya que pueden cambiar y lo harán en algún momento.

| Se necesita el cifrado de tokens | Vigencia | Descripción |
| ----------------------- | ------------------------------- | ------------ |
| Id\_Tokens | 1 hora | Los id\_tokens normalmente son válidos durante una hora. Tu aplicación web puede utilizar este mismo período de tiempo en el mantenimiento de su propia sesión con el usuario (recomendado), o elegir una duración para la sesión completamente diferente. Si la aplicación necesita obtener un nuevo id\_token, solo tiene que realizar una nueva solicitud de inicio de sesión en Azure AD. Si el usuario tiene una sesión de explorador válida con Azure AD, es posible que no tenga que volver a escribir sus credenciales. |
| Tokens de actualización | Hasta 14 días | Un token de actualización solo es válido durante un máximo de 14 días. Sin embargo, el token de actualización puede dejar de ser válido en cualquier momento por varias razones, por lo que tu aplicación tiene que continuar probando y usando un token de actualización hasta que se produzca un error o hasta que la aplicación lo sustituya por un nuevo token de actualización. Un token de actualización también dejará de ser válido si han pasado 90 días desde que el usuario introdujo sus credenciales. |
| Códigos de autorización | 5 minutos | Los códigos de autorización son de corta duración a propósito, y se deben canjear inmediatamente por access\_tokens, id\_tokens y refresh\_tokens cuando se reciben. |

<!---HONumber=AcomDC_0128_2016-->