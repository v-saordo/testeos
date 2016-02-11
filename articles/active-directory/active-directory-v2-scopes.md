<properties
	pageTitle="Ámbitos, permisos y consentimiento del modelo de aplicación v2.0 | Microsoft Azure"
	description="Descripción de la autorización del modelo de aplicaciones de Azure AD versión 2.0, que incluye los ámbitos, los permisos y el consentimiento."
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

# Vista previa de la versión 2.0 del modelo de aplicaciones: ámbitos, permisos y consentimiento

Las aplicaciones que se integran con Azure AD siguen un modelo de autorización especial que permite a los usuarios controlar cómo puede tener acceso a sus datos una aplicación. En la versión 2.0 del modelo de aplicaciones se ha actualizado la implementación de este modelo de autorización y se ha cambiado cómo tiene que interactuar una aplicación con Azure AD. En este tema se tratan los conceptos básicos de este modelo de autorización, incluidos los ámbitos, los permisos y el consentimiento.

> [AZURE.NOTE]
	Esta información se aplica a la vista previa pública de la versión 2.0 del modelo de aplicaciones. Para obtener instrucciones sobre cómo integrar con el servicio de Azure AD disponible con carácter general, consulta la [Guía para desarrolladores de Azure Active Directory](active-directory-developers-guide.md).

## Ámbitos y permisos

La versión 2.0 del modelo de aplicaciones implementa el protocolo de autorización [OAuth 2.0](active-directory-v2-protocols.md), que es un método para permitir a una aplicación de terceros tener acceso a los recursos hospedados en la web en nombre del usuario. Cualquier recurso hospedado en la web que se integre con Azure AD tendrá un identificador de recursos o **URI del Id. de aplicación**. Por ejemplo, algunos de los recursos de Microsoft hospedados en la web incluyen:

- La API de Correo unificado de Office 365:`https://outlook.office.com`
- La API del Administrador de recursos de Azure:`https://management.azure.com`
- La API Graph de Azure AD:`https://graph.windows.net`

Ocurre lo mismo con cualquier recurso de terceros integrado con Azure AD. Cualquiera de estos recursos también puede definir un conjunto de permisos que se puede utilizar para dividir la funcionalidad de ese recurso en fragmentos menores. Por ejemplo, la API de Office 365 de Correo unificado definió estos permisos básicos:

- Leer el buzón del usuario
- Escribir en el buzón del usuario
- Enviar correo como un usuario

Mediante la definición de estos permisos, el recurso puede tener un control específico sobre sus datos y sobre cómo se expone al mundo exterior. Una aplicación de terceros puede solicitar estos permisos de un usuario final y el usuario final tiene que aprobar los permisos para que la aplicación pueda actuar en su nombre. Al fragmentar la funcionalidad del recurso en conjuntos de permisos de menor tamaño, se pueden crear aplicaciones de terceros para solicitar solo los permisos concretos que necesitan para realizar sus tareas. También permite a los usuarios finales saber exactamente cómo una aplicación utiliza sus datos para que estén más seguros de que la aplicación no se comporta de forma malintencionada.

En Azure AD y OAuth, estos permisos se conocen como **ámbitos**. También puedes ver que se refieren a ellos como **oAuth2Permissions**. Un ámbito se representa en Azure AD como un valor de cadena. Al igual que en el ejemplo de la API de Correo unificado de Office 365, el valor de ámbito para cada permiso es:

- Leer el buzón del usuario: `Mail.Read`
- Escribir en el buzón del usuario: `Mail.ReadWrite`
- Enviar correo como un usuario: `Mail.Send`

Una aplicación puede solicitar estos permisos especificando los ámbitos en las solicitudes al extremo de la versión 2.0, tal como se describe a continuación.

## Consentimiento

En una solicitud de autorización de [OpenID Connect o OAuth 2.0](active-directory-v2-protocols.md), una aplicación puede solicitar los permisos que necesita mediante el parámetro de consulta `scope`. Por ejemplo, cuando un usuario inicia sesión en una aplicación, la aplicación podría enviar una solicitud similar a la siguiente (con saltos de línea para mejorar la legibilidad):

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=
https%3A%2F%2Foutlook.office.com%2Fmail.read%20
https%3A%2F%2Foutlook.office.com%2Fmail.send
&state=12345
```

El parámetro `scope` es una lista separada por espacios que incluye los ámbitos que solicita la aplicación. Cada ámbito individual se indica anexando el valor de ámbito al identificador de recursos (URI del Id. de aplicación). La solicitud anterior indica que la aplicación necesita permiso para leer el buzón del usuario y enviar correo electrónico como usuario.

Después de que el usuario escriba sus credenciales, el extremo de la versión 2.0 buscará un registro coincidente con el **consentimiento del usuario**. Si el usuario no ha consentido ninguno de los permisos solicitados en el pasado, el extremo de la versión 2.0 le solicitará al usuario que conceda los permisos solicitados.

![Captura de pantalla del consentimiento de la cuenta profesional.](../media/active-directory-v2-flows/work_account_consent.png)

Cuando el usuario apruebe el permiso, se grabará el consentimiento para que el usuario no tenga que volver a dar su consentimiento en inicios de sesión posteriores.

## Consentimiento incremental

La aplicación no tiene que solicitar todos los permisos que necesita del usuario inicial al inicio de sesión o al registrarse. Como puedes especificar los ámbitos en cada solicitud, la aplicación puede realizar un "consentimiento incremental" y elegir cuándo será el mejor momento para solicitar al usuario su consentimiento. Hay algunas razones comunes por las que una aplicación podría no solicitar todos los permisos a la vez:

- Aplicaciones que necesitan gran cantidad de permisos. Si tu aplicación tiene acceso a muchos recursos diferentes y realiza funciones enriquecidas en todos ellos, la lista de permisos que necesita la aplicación puede extenderse muy rápidamente. Históricamente, las listas largas de permisos desaniman a los usuarios a suscribirse a una aplicación y reducen la adopción de usuarios. En lugar de solicitar estos permisos a la vez, la aplicación puede solicitar un subconjunto tras el inicio de sesión inicial y después, de forma incremental, solicitar permisos adicionales cuando el usuario intente utilizar características avanzadas.
- Aplicaciones que crecen con el tiempo. Casi todas las aplicaciones comienzan con un pequeño conjunto de funcionalidades y crecen con el tiempo para incorporar nuevas características. Con el consentimiento incremental, puedes realizar cambios en la aplicación y solicitar nuevos permisos al usuario con gran facilidad. Solo tienes que actualizar el código para enviar ámbitos adicionales en las solicitudes de autorización.
- Permisos de solo administrador. Algunos recursos definirán los permisos que **solo** puede consentir o aprobar el administrador de una organización. Por ejemplo, la API Graph de Azure AD define el permiso `Directory.Write`, lo que permite a una aplicación crear, actualizar y eliminar usuarios y grupos, entre otras cosas. Puedes imaginar por qué ese permiso puede requerir la aprobación de un administrador con privilegios elevados. Mediante el uso del consentimiento incremental, la aplicación puede exponer un conjunto básico de funcionalidades para que cualquier usuario pueda registrarse sin la aprobación del administrador. Sin embargo, una vez que intentan realizar una acción de privilegios elevados, puedes solicitar el permiso de solo administrador y requerir que un administrador se registre antes de tener acceso a esa parte de la aplicación.

## Uso de permisos

Después de que el usuario da su consentimiento para los permisos de la aplicación, la aplicación puede obtener tokens de acceso que representan los permisos de la aplicación para acceder a un recurso de alguna manera. Un token de acceso determinado solo se puede utilizar para un único recurso, pero con codificación en su interior será cada permiso que la aplicación concediera para ese recurso. Para adquirir un token de acceso, la aplicación puede realizar una solicitud al extremo de token de la versión 2.0:

```
POST common/v2.0/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
	"grant_type": "authorization_code",
	"client_id": "2d4d11a2-f814-46a7-890a-274a72a7309e",
	"scope": "https://outlook.office.com/mail.read https://outlook.office.com/mail.send",
	"code": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq..."
	"client_secret": "zc53fwe80980293klaj9823"  // NOTE: Only required for web apps
}
```

El token de acceso resultante se puede usar en las solicitudes HTTP para el recurso e indicará al recurso de forma fiable que la aplicación tiene el permiso indicado para realizar una tarea determinada.

Para conocer más detalles sobre el protocolo OAuth 2.0 y cómo adquirir tokens de acceso, consulta la [referencia del protocolo de la versión 2.0 del modelo de aplicaciones](active-directory-v2-protocols.md).


## Ámbitos de OpenId Connect

La implementación de la versión 2.0 de OpenID Connect tiene unos cuantos ámbitos bien definidos que no se aplican a ningún recurso determinado: `openid`, `email`, `profile`, y `offline_access`.

#### OpenId

Si una aplicación realiza el inicio de sesión mediante [OpenID Connect](active-directory-v2-protocols.md#openid-connect-sign-in-flow) deberá solicitar el ámbito `openid`. El ámbito `openid` aparecerá en la pantalla de consentimiento de la cuenta profesional como el permiso "Iniciar sesión" y en la pantalla de consentimiento de la cuenta personal de Microsoft como el permiso "Ver el perfil y conectarse a las aplicaciones y servicios con la cuenta de Microsoft". Este permiso permite que una aplicación reciba un identificador único para el usuario en forma de notificación `sub`. También ofrece a la aplicación acceso al punto de conexión de la información de usuario. El ámbito `openid` también se puede utilizar en el extremo del token de la versión 2.0 para adquirir id\_tokens, que se pueden utilizar para proteger las llamadas HTTP entre distintos componentes de una aplicación.

#### Email

El ámbito `email` pueden incluirse junto con el ámbito `openid` y con cualquier otro. Proporciona a la aplicación el acceso a la dirección de correo electrónico principal del usuario en forma de notificación `email`. La notificación `email` solo se incluirá en los tokens si una dirección de correo electrónico está asociada a la cuenta de usuario, que no es siempre el caso. Si utiliza el ámbito `email`, la aplicación debe estar preparada para controlar los casos en los que la notificación `email` no exista en el token.

#### Perfil

El ámbito `profile` pueden incluirse junto con el ámbito `openid` y con cualquier otro. Proporciona acceso a la aplicación a una gran cantidad de información sobre el usuario. Esto incluye, pero no se limita a, nombre del usuario, apellido, nombre de usuario preferido, identificador de objeto y demás. Para obtener una lista completa de las notificaciones de perfil disponibles en id\_tokens para un usuario determinado, consulte la [referencia de los tokens de la versión 2.0](active-directory-v2-tokens.md).

#### Offline\_Access

El [ámbito `offline_access`](http://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess) permite que la aplicación tenga acceso a recursos en nombre del usuario durante un largo período de tiempo. En la pantalla de consentimiento de la cuenta profesional este ámbito aparecerá como el permiso "Acceso a los datos en cualquier momento". En la pantalla de consentimiento de la cuenta personal de Microsoft este ámbito aparecerá como el permiso "Acceso a tu información en cualquier momento". Cuando un usuario aprueba el ámbito `offline_access`, la aplicación se habilitará para recibir los tokens de actualización del extremo de token de la versión 2.0. Los tokens de actualización son de larga duración y permiten a la aplicación adquirir nuevos tokens de acceso a medida que los antiguos caducan.

Si tu aplicación no solicita el ámbito `offline_access`, no recibirá tokens de actualización (refresh\_tokens). Esto significa que cuando canjees un código de autorización (authorization\_code) del [flujo del código de autorización de OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow), solo recibirás un token de acceso (access\_token) desde el extremo de `/token`. Ese token de acceso seguirá siendo válido durante un breve período de tiempo (normalmente una hora), pero finalmente caducará. En ese momento, la aplicación tendrá que redirigir al usuario de nuevo al extremo de `/authorize` para recuperar un nuevo authorization\_code. Durante esta redirección, es posible o no que el usuario necesite escribir sus credenciales de nuevo o volver a dar el consentimiento a permisos, según el tipo de aplicación.

Para más información sobre cómo obtener y usar los tokens de actualización, consulte la [referencia de protocolos de la versión 2.0](active-directory-v2-protocols.md).

<!---HONumber=AcomDC_0128_2016-->