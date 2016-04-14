<properties
	pageTitle="Ámbitos, permisos y consentimiento de Azure AD v2.0 | Microsoft Azure"
	description="Descripción de la autorización del punto de conexión v2.0 de Azure AD, que incluye los ámbitos, los permisos y el consentimiento."
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

# Ámbitos, permisos y consentimiento en el punto de conexión v2.0

Las aplicaciones que se integran con Azure AD siguen un modelo de autorización especial que permite a los usuarios controlar cómo puede tener acceso a sus datos una aplicación. Se ha actualizado la implementación v2.0 de este modelo de autorización y se ha cambiado cómo tiene que interactuar una aplicación con Azure AD. En este tema se tratan los conceptos básicos de este modelo de autorización, incluidos los ámbitos, los permisos y el consentimiento.

> [AZURE.NOTE]
	No todas las características y escenarios de Azure Active Directory son compatibles con el punto de conexión v2.0. Para determinar si debe usar el punto de conexión v2.0, lea acerca de las [limitaciones de v2.0](active-directory-v2-limitations.md).

## Ámbitos y permisos

Azure AD implementa el protocolo de autorización [OAuth 2.0](active-directory-v2-protocols.md), que es un método para permitir que una aplicación de terceros tenga acceso a los recursos hospedados en la web en nombre del usuario. Cualquier recurso hospedado en la web que se integre con Azure AD tendrá un identificador de recursos o **URI del Id. de aplicación**. Por ejemplo, algunos de los recursos de Microsoft hospedados en la web incluyen:

- La API de Correo unificado de Office 365:`https://outlook.office.com`
- La API Graph de Azure AD:`https://graph.windows.net`
- Microsoft Graph: `https://graph.microsoft.com`

Ocurre lo mismo con cualquier recurso de terceros integrado con Azure AD. Cualquiera de estos recursos también puede definir un conjunto de permisos que se puede utilizar para dividir la funcionalidad de ese recurso en fragmentos menores. Por ejemplo, Microsoft Graph tiene definidos algunos permisos:

- Leer el calendario de un usuario
- Escribir en el calendario de un usuario
- Enviar correo como un usuario
- [Y mucho más](https://graph.microsoft.io)

Mediante la definición de estos permisos, el recurso puede tener un control específico sobre sus datos y sobre cómo se expone al mundo exterior. Una aplicación de terceros puede solicitarle estos permisos a un usuario final, y dicho usuario tiene que aprobar los permisos para que la aplicación pueda actuar en su nombre. Al fragmentar la funcionalidad del recurso en conjuntos de permisos de menor tamaño, se pueden crear aplicaciones de terceros para solicitar solo los permisos concretos que necesitan para realizar sus tareas. También permite a los usuarios finales saber exactamente cómo una aplicación utiliza sus datos para que estén más seguros de que la aplicación no se comporta de forma malintencionada.

En Azure AD y OAuth, estos permisos se conocen como **ámbitos**. También puedes ver que se refieren a ellos como **oAuth2Permissions**. Un ámbito se representa en Azure AD como un valor de cadena. Al igual que en el ejemplo de Microsoft Graph, el valor de ámbito para cada permiso es:

- Leer el calendario de un usuario: `Calendar.Read`
- Escribir en el calendario de un usuario: `Mail.ReadWrite`
- Enviar correo como un usuario: `Mail.Send`

Una aplicación puede solicitar estos permisos especificando los ámbitos en las solicitudes al extremo de la versión 2.0, tal como se describe a continuación.


## Consentimiento

En una solicitud de autorización de [OpenID Connect o OAuth 2.0](active-directory-v2-protocols.md), una aplicación puede solicitar los permisos que necesita mediante el parámetro de consulta `scope`. Por ejemplo, cuando un usuario inicia sesión en una aplicación, la aplicación podría enviar una solicitud similar a la siguiente (con saltos de línea para mejorar la legibilidad):

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=
https%3A%2F%2Fgraph.microsoft.com%2Fcalendar.read%20
https%3A%2F%2Fgraph.microsoft.com%2Fmail.send
&state=12345
```

El parámetro `scope` es una lista separada por espacios que incluye los ámbitos que solicita la aplicación. Cada ámbito individual se indica anexando el valor de ámbito al identificador de recursos (URI del Id. de aplicación). La solicitud anterior indica que la aplicación necesita permiso para leer el calendario del usuario y enviar correo electrónico como usuario.

Después de que el usuario escriba sus credenciales, el extremo de la versión 2.0 buscará un registro coincidente con el **consentimiento del usuario**. Si el usuario no ha consentido ninguno de los permisos solicitados en el pasado, el extremo de la versión 2.0 le solicitará al usuario que conceda los permisos solicitados.

![Captura de pantalla del consentimiento de la cuenta profesional.](../media/active-directory-v2-flows/work_account_consent.png)

Cuando el usuario apruebe el permiso, se grabará el consentimiento para que el usuario no tenga que volver a dar su consentimiento en inicios de sesión posteriores.

## Uso de permisos

Después de que el usuario da su consentimiento para los permisos de la aplicación, la aplicación puede obtener tokens de acceso que representan los permisos de la aplicación para acceder a un recurso de alguna manera. Un token de acceso determinado solo se puede utilizar para un único recurso, pero con codificación en su interior será cada permiso que la aplicación concediera para ese recurso. Para adquirir un token de acceso, la aplicación puede realizar una solicitud al extremo de token de la versión 2.0:

```
POST common/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
	"grant_type": "authorization_code",
	"client_id": "6731de76-14a6-49ae-97bc-6eba6914391e",
	"scope": "https://outlook.office.com/mail.read https://outlook.office.com/mail.send",
	"code": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq..."
	"redirect_uri": "https://localhost/myapp",
	"client_secret": "zc53fwe80980293klaj9823"  // NOTE: Only required for web apps
}
```

El token de acceso resultante se puede usar en las solicitudes HTTP para el recurso e indicará al recurso de forma fiable que la aplicación tiene el permiso indicado para realizar una tarea determinada.

Para conocer más detalles sobre el protocolo OAuth 2.0 y cómo adquirir tokens de acceso, consulte la [referencia del protocolo del punto de conexión v2.0](active-directory-v2-protocols.md).

## Ámbitos de OpenId Connect

La implementación de la versión 2.0 de OpenID Connect tiene unos cuantos ámbitos bien definidos que no se aplican a ningún recurso determinado: `openid`, `email`, `profile` y `offline_access`.

#### OpenId

Si una aplicación realiza el inicio de sesión mediante [OpenID Connect](active-directory-v2-protocols.md#openid-connect-sign-in-flow) deberá solicitar el ámbito `openid`. El ámbito `openid` aparecerá en la pantalla de consentimiento de la cuenta profesional como el permiso "Iniciar sesión" y en la pantalla de consentimiento de la cuenta personal de Microsoft como el permiso "Ver el perfil y conectarse a las aplicaciones y servicios con la cuenta de Microsoft". Este permiso permite que una aplicación reciba un identificador único para el usuario en forma de notificación `sub`. También ofrece a la aplicación acceso al punto de conexión de la información de usuario. El ámbito `openid` también se puede utilizar en el extremo del token de la versión 2.0 para adquirir id\_tokens, que se pueden utilizar para proteger las llamadas HTTP entre distintos componentes de una aplicación.

#### Email

El ámbito `email` pueden incluirse junto con el ámbito `openid` y con cualquier otro. Proporciona a la aplicación el acceso a la dirección de correo electrónico principal del usuario en forma de notificación `email`. La notificación `email` solo se incluirá en los tokens si una dirección de correo electrónico está asociada a la cuenta de usuario, que no es siempre el caso. Si usa el ámbito `email`, la aplicación debe estar preparada para controlar los casos en los que la notificación `email` no exista en el token.

#### Perfil

El ámbito `profile` pueden incluirse junto con el ámbito `openid` y con cualquier otro. Proporciona acceso a la aplicación a una gran cantidad de información sobre el usuario. Esto incluye, pero no se limita a, nombre del usuario, apellido, nombre de usuario preferido, identificador de objeto y demás. Para obtener una lista completa de las notificaciones de perfil disponibles en id\_tokens para un usuario determinado, consulte la [referencia de los tokens de v2.0](active-directory-v2-tokens.md).

#### Offline\_Access

El [ámbito `offline_access`](http://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess) permite que la aplicación tenga acceso a recursos en nombre del usuario durante un largo período de tiempo. En la pantalla de consentimiento de la cuenta profesional este ámbito aparecerá como el permiso "Acceso a los datos en cualquier momento". En la pantalla de consentimiento de la cuenta personal de Microsoft este ámbito aparecerá como el permiso "Acceso a tu información en cualquier momento". Cuando un usuario aprueba el ámbito `offline_access`, la aplicación se habilitará para recibir los tokens de actualización del extremo de token de la versión 2.0. Los tokens de actualización son de larga duración y permiten a la aplicación adquirir nuevos tokens de acceso a medida que los antiguos caducan.

Si tu aplicación no solicita el ámbito `offline_access`, no recibirá tokens de actualización (refresh\_tokens). Esto significa que cuando canjees un código de autorización (authorization\_code) del [flujo del código de autorización de OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow), solo recibirás un token de acceso (access\_token) desde el extremo de `/token`. Ese token de acceso seguirá siendo válido durante un breve período de tiempo (normalmente una hora), pero finalmente caducará. En ese momento, la aplicación tendrá que redirigir al usuario de nuevo al extremo de `/authorize` para recuperar un nuevo authorization\_code. Durante esta redirección, es posible o no que el usuario necesite escribir sus credenciales de nuevo o volver a dar el consentimiento a permisos, según el tipo de aplicación.

Para obtener más información sobre cómo obtener y usar los tokens de actualización, consulte la [referencia de protocolos de v2.0](active-directory-v2-protocols.md).

<!---HONumber=AcomDC_0224_2016-->