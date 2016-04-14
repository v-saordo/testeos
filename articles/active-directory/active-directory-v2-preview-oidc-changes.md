<properties
	pageTitle="Cambios en el punto de conexión de Azure AD v2.0 | Microsoft Azure"
	description="Una descripción de los cambios que se están realizando en los protocolos de vista previa pública del modelo de aplicación v2.0."
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

# Actualizaciones importantes de los protocolos de autenticación de la versión 2.0
¡Atención, desarrolladores! Durante las próximas dos semanas realizaremos algunas actualizaciones en nuestros protocolos de autenticación v2.0 que pueden suponer importantes cambios para cualquier aplicación que se haya escrito durante el período de vista previa.

## ¿Qué se ve afectado?
Todas las aplicaciones que se hayan escrito para usar la versión 2.0 del punto de conexión de autenticación convergente.

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
```

Puede encontrar más información sobre el punto de conexión v2.0 [aquí](active-directory-appmodel-v2-overview.md).

Si ha creado una aplicación con el punto de conexión v2.0 codificando directamente al protocolo v2.0, mediante cualquiera de nuestros productos de software intermedio de web: OpenID Connect o OAuth, o con otra biblioteca de terceros para realizar la autenticación, debe prepararse para realizar pruebas en sus proyectos y para hacer cambios si es necesario.

## ¿Qué no se ve afectado?
Las aplicaciones que se han escrito con el punto de conexión de autenticación de Azure AD de producción.

```
https://login.microsoftonline.com/common/oauth2/authorize
```

Este protocolo es inamovible y no experimentará ningún cambio.

Además, si su aplicación usa **solo** nuestra biblioteca de autenticación de Azure AD (ADAL) para realizar la autenticación, no tendrá que cambiar nada. ADAL ha protegido su aplicación de los cambios.

## ¿Cuáles son los cambios?
### Eliminación del valor x5t de los encabezados JWT
El punto de conexión de la versión 2.0 usa ampliamente tokens JWT, los cuales contienen una sección de parámetros de encabezado con metadatos pertinentes sobre el token. Si descodifica el encabezado de uno de nuestros JWT actuales, encontrará algo como esto:

```
{ 
	"type": "JWT",
	"alg": "RS256",
	"x5t": "MnC_VZcATfM5pOYiJHMba9goEKY",
	"kid": "MnC_VZcATfM5pOYiJHMba9goEKY"
}
```

En donde las propiedades "x5t" y "kid" identifican la clave pública que se debe usar para validar la firma del token, una vez recuperada del punto de conexión de metadatos OpenID Connect.

El cambio que vamos a realizar consiste en eliminar la propiedad "x5t". Puede seguir utilizando los mismos mecanismos para validar los tokens, pero debe basarse solo en la propiedad "kid" para recuperar la clave pública correcta, como se especifica en el protocolo OpenID Connect.

> [AZURE.IMPORTANT] **Su tarea: Asegúrese de que la aplicación no depende de la existencia del valor x5t.**

### Eliminación de profile\_info
Anteriormente, el punto de conexión v2.0 ha estado devolviendo un objeto JSON con codificación base64 en las respuestas de token llamadas `profile_info`. Al solicitar un token de acceso al punto de conexión v2.0 enviando una solicitud a:

```
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

La respuesta se parecerá al siguiente objeto JSON: ```
{ 
	"token_type": "Bearer",
	"expires_in": 3599,
	"scope": "https://outlook.office.com/mail.read",
	"access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
	"refresh_token": "OAAABAAAAiL9Kn2Z27UubvWFPbm0gL...",
	"profile_info": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
}
```

El valor `profile_info` contenía información sobre el usuario que había iniciado sesión en la aplicación: su nombre para mostrar, nombre, apellido, dirección de correo electrónico, identificador y demás. Principalmente, `profile_info` se usaba para almacenar en caché los tokens y para la visualización.

Vamos a quitar el valor `profile_info`, pero no se preocupe, seguiremos proporcionando esta información a los desarrolladores en otro sitio. En lugar de `profile_info`, el punto de conexión v2.0 devolverá ahora un valor `id_token` en cada respuesta de token:

```
{ 
	"token_type": "Bearer",
	"expires_in": 3599,
	"scope": "https://outlook.office.com/mail.read",
	"access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
	"refresh_token": "OAAABAAAAiL9Kn2Z27UubvWFPbm0gL...",
	"id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
}
```

Puede descodificar y analizar el id\_token para recuperar la misma información que hasta ahora recibía de profile\_info. El id\_token es un token web JSON (JWT), con el contenido especificado por OpenID Connect. El código para hacerlo es muy similar: basta con extraer el segmento central (el cuerpo) del id\_token y descodificarlo en base64 para obtener acceso al objeto JSON que se encuentra dentro.

En las próximas dos semanas, debe codificar la aplicación para recuperar la información de usuario bien desde `id_token` o `profile_info`, lo que esté presente. De este modo, cuando se realice el cambio, la aplicación podrá controlar sin problemas la transición desde `profile_info` a `id_token` sin interrupción.

> [AZURE.IMPORTANT] **Su tarea: Asegúrese de que la aplicación no depende de la existencia del valor `profile_info`.**

### Eliminación de id\_token\_expires\_in
De forma similar a `profile_info`, también estamos quitando el parámetro `id_token_expires_in` de las respuestas. Anteriormente, el punto de conexión v2.0 devolvía un valor para `id_token_expires_in` junto con cada respuesta id\_token, por ejemplo, en una respuesta de autorización:

```
https://myapp.com?id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...&id_token_expires_in=3599...
```

O en una respuesta de token:

```
{ 
	"token_type": "Bearer",
	"id_token_expires_in": 3599,
	"scope": "openid",
	"id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
	"refresh_token": "OAAABAAAAiL9Kn2Z27UubvWFPbm0gL...",
	"profile_info": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
}
```

El valor `id_token_expires_in` indica el número de segundos durante los que el id\_token mantiene su validez. Ahora, vamos a quitar el valor `id_token_expires_in` completamente. En su lugar, puede usar las notificaciones estándar de OpenID Connect `nbf` y `exp` para examinar la validez del id\_token. Consulte la [referencia de los tokens de la versión 2.0](active-directory-v2-tokens.md) para obtener más información sobre estas notificaciones.

> [AZURE.IMPORTANT] **Su tarea: Asegúrese de que la aplicación no depende de la existencia del valor `id_token_expires_in`.**


### Modificación de las notificaciones devueltas por scope = openid
Este cambio será el más significativo, de hecho, afectará a casi todas las aplicaciones que utilizan el punto de conexión de la versión 2.0. Muchas aplicaciones envían solicitudes al punto de conexión de v2.0 mediante el ámbito `openid`, como:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=...
&redirect_uri=...
&response_mode=form_post
&response_type=id_token
&scope=openid offline_access https://outlook.office.com/mail.read
```

Hoy en día, cuando el usuario concede el consentimiento para el ámbito `openid`, la aplicación recibe una gran cantidad de información sobre el usuario en el id\_token resultante. Estas notificaciones pueden incluir su nombre, nombre de usuario preferido, dirección de correo electrónico, identificador de objeto y más.

En esta actualización se está cambiando la información a la que el ámbito `openid` permite el acceso a la aplicación, para cumplir mejor con la especificación de OpenID Connect. El ámbito `openid` solo permitirá que el usuario inicie sesión en la aplicación y que esta reciba un identificador específico de aplicación para el usuario en la notificación `sub` de id\_token. Las notificaciones en id\_token que tengan únicamente el ámbito `openid` concedido, estarán desprovistas de toda información personal identificable. Algunos ejemplos de notificaciones id\_token son:

```
{ 
	"aud": "580e250c-8f26-49d0-bee8-1c078add1609",
	"iss": "https://login.microsoftonline.com/b9410318-09af-49c2-b0c3-653adc1f376e/v2.0 ",
	"iat": 1449520283,
	"nbf": 1449520283,
	"exp": 1449524183,
	"nonce": "12345",
	"sub": "MF4f-ggWMEji12KynJUNQZphaUTvLcQug5jdF2nl01Q",
	"tid": "b9410318-09af-49c2-b0c3-653adc1f376e",
	"ver": "2.0",
}
```

Si desea obtener información personal identificable (PII) acerca del usuario en la aplicación, esta tendrá que solicitar permisos adicionales al usuario. Estamos introduciendo soporte para dos nuevos ámbitos de la especificación OpenID Connect: los ámbitos `email` y `profile`, que permiten hacerlo.

- El ámbito `email` es muy sencillo: permite que la aplicación acceda a la dirección de correo electrónico principal del usuario a través de la notificación `email` en id\_token. Tenga en cuenta que la notificación `email` no siempre estará presente en los id\_tokens, solo se incluye si está disponible en el perfil del usuario.
- El ámbito `profile` ofrece a la aplicación acceso a toda la demás información básica sobre el usuario: su nombre, el nombre de usuario preferido, identificador de objeto y demás.

Esto le permite codificar la aplicación en un modo de divulgación mínima, puede pedir al usuario solo el conjunto de información que la aplicación necesita para hacer su trabajo. Si desea continuar obteniendo el conjunto completo de información de usuario que la aplicación está recibiendo actualmente, tiene que incluir los tres ámbitos en las solicitudes de autorización:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=...
&redirect_uri=...
&response_mode=form_post
&response_type=id_token
&scope=openid profile email offline_access https://outlook.office.com/mail.read
```

La aplicación puede comenzar enviando los ámbitos `email` y `profile` de forma inmediata, el punto de conexión v2.0 aceptará estos dos ámbitos e iniciará la solicitud de permisos a los usuarios según sea necesario. Sin embargo, el cambio en la interpretación del ámbito `openid` no surtirá efecto durante unas semanas.

> [AZURE.IMPORTANT] **Su tarea: Agregar los ámbitos `profile` y `email` si su aplicación requiere información sobre el usuario.** Tenga en cuenta que la ADAL incluirá ambos permisos en las solicitudes de forma predeterminada.

### Eliminación de la barra diagonal final del emisor.
Anteriormente, el valor de emisor que aparece en los tokens del punto de conexión v2.0 tenía la forma

```
https://login.microsoftonline.com/{some-guid}/v2.0/
```

Donde el GUID era el valor tenantId del inquilino de Azure AD que emitió el token. Con estos cambios, el valor del emisor se convierte en

```
https://login.microsoftonline.com/{some-guid}/v2.0 
```

en ambos tokens y en el documento de detección de OpenID Connect.

> [AZURE.IMPORTANT] **Su tarea: Asegúrese de que la aplicación acepta el valor del emisor con y sin una barra diagonal final durante la validación del emisor.**

## ¿Por qué se han hecho los cambios?
El motivo principal para la introducción de estos cambios es mantener la compatibilidad con la especificación estándar de OpenID Connect. A través de la compatibilidad con OpenID Connect, esperamos minimizar las diferencias entre la integración con los servicios de identidad de Microsoft y con otros servicios de identidad en la industria. Queremos que los desarrolladores puedan utilizar sus bibliotecas de autenticación de código abierto preferidas sin tener que modificar las bibliotecas para adaptarse a las diferencias de Microsoft.

## ¿Qué puede hacer?
En este momento, puede comenzar a realizar todos los cambios que se han descrito anteriormente. Inmediatamente, debe:

1.	**Eliminar las dependencias del parámetro de cabecera `x5t`.**
2.	**Controlar correctamente la transición de `profile_info` a `id_token` en las respuestas de token.**
3.  **Eliminar las dependencias del parámetro de respuesta `id_token_expires_in`.**
3.	**Agregar los ámbitos `profile` y `email` a la aplicación si la aplicación necesita información básica del usuario.**
4.	**Aceptar los valores de emisor en los tokens con y sin una barra diagonal final.**

Nuestra documentación [Vista previa del modelo de aplicaciones v2.0: protocolos - OAuth 2.0 y OpenID Connect](active-directory-v2-protocols.md) ya se ha actualizado para reflejar estos cambios, por lo que puede utilizarla como referencia a la hora de actualizar el código.

Si tiene alguna pregunta en todo lo relacionado con los cambios, no dude en contacto con nosotros en Twitter en @AzureAD.

## ¿Con qué frecuencia se realizan cambios en los protocolos?
No se prevén más cambios sustanciales en los protocolos de autenticación. Hemos juntado todos estos cambios en una sola versión de forma intencionada, para que no tenga que volver a pasar pronto por este tipo de proceso de actualización. Por supuesto que seguiremos agregando características al servicio de autenticación convergente de v2.0, de las que esperamos que saque provecho, pero estos cambios deberían ser aditivos y por tanto no será necesario cambiar el código existente.

Por último, queremos darles las gracias por probar las funciones durante el período de vista previa. Las ideas y las experiencias de nuestros primeros usuarios han tenido un valor inestimables hasta ahora, y esperamos que sigan compartiendo sus opiniones e ideas con nosotros.

¡ Feliz codificación!

Microsoft Identity Division

<!---HONumber=AcomDC_0224_2016-->