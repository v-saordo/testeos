<properties
	pageTitle="Modelo de aplicaciones v2.0 | Microsoft Azure"
	description="Una comparación entre Azure AD de disponibilidad general vista previa pública del modelo de aplicaciones v2.0."
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

# Diferencias de la vista previa del modelo de aplicaciones v2.0

Si está familiarizado con el servicio de Azure AD de disposición general o ha integrado aplicaciones con Azure AD en el pasado, puede que haya algunas diferencias en el modelo de aplicaciones v2.0 que no esperaría. Este documento describe esas diferencias para su comprensión.

> [AZURE.NOTE]
	Esta información se aplica a la vista previa pública del modelo de aplicaciones v2.0. Para obtener instrucciones sobre cómo integrar con el servicio de Azure AD disponible con carácter general, consulte la [Guía para desarrolladores de Azure Active Directory](active-directory-developers-guide.md).


## Cuentas de Microsoft y cuentas de Azure AD
El modelo de aplicaciones v2.0 permite a los desarrolladores crear aplicaciones que aceptan el inicio de sesión tanto desde cuentas de Microsoft como desde cuentas de Azure AD, mediante un extremo único. Esto le ofrece la posibilidad de crear su aplicación completamente independiente de la cuenta; puede no conocer el tipo de cuenta con el que el usuario inicia sesión. Por supuesto, *puede* hacer que la aplicación reconozca el tipo de cuenta que se usa en una sesión determinada, aunque no es necesario.

Por ejemplo, si su aplicación llama a las [API de REST de Office 365](https://msdn.microsoft.com/office/office365/howto/authenticate-Office-365-APIs-using-v2), los usuarios de empresa tendrán a su disposición algunos datos y funciones adicionales, como los sitios de SharePoint o datos de directorio. Pero para muchas acciones, como [leer el correo de un usuario](https://msdn.microsoft.com/office/office365/howto/authenticate-Office-365-APIs-using-v2), el código se puede crear exactamente de la misma manera tanto para las cuentas de Microsoft como para las cuentas de Azure AD.

La integración de su aplicación con las cuentas de Azure AD y las cuentas de Microsoft ahora es un proceso sencillo. Puede usar un conjunto único de extremos, una sola biblioteca y un registro de aplicaciones único para obtener acceso tanto al mundo empresarial como al de los consumidores. Para obtener más información sobre la vista previa modelo de aplicaciones v2.0, consulte la [información general](active-directory-appmodel-v2-overview.md).


## Nuevo portal de registro de aplicaciones
El modelo de aplicaciones v2.0 requiere que registre sus aplicaciones de Microsoft en una nueva ubicación: [apps.dev.microsoft.com](https://apps.dev.microsoft.com). En este portal podrá obtener un id. de aplicación y personalizar la apariencia de la página de inicio de sesión de su aplicación, entre otras cuestiones. Seguiremos agregando cada vez más funciones a este Portal de registro de aplicaciones, basándonos en gran medida en los comentarios que recibimos durante la vista previa. La intención es que este portal sea la nueva ubicación donde puede ir dirigirse para administrar todo lo relacionado con las aplicaciones de Microsoft.

Y lo mejor es que no necesita una suscripción a Azure u Office para usarlo. Todo lo que necesita es una cuenta de Microsoft personal o una cuenta profesional o educativa.

Tenga en cuenta que en la actualidad, las aplicaciones registradas en el nuevo Portal de registro de aplicaciones solo funcionarán con el modelo de aplicaciones v2.0 y *solo* funcionarán con el modelo de aplicaciones v2.0 las aplicaciones registradas en el nuevo Portal de registro de aplicaciones. Si intenta usar una de estas aplicaciones con los servicios de Azure AD o la cuenta de Microsoft de disponibilidad general, o intenta usar una aplicación existente con el modelo de aplicaciones v2.0, pueda encontrar incompatibilidades.


## Un id. de aplicación para todas las plataformas
En el servicio de Azure AD de disponibilidad general, puede haber registrado varias aplicaciones diferentes para un proyecto único. Se vio obligado a usar registros de aplicaciones independientes para sus clientes nativos y las aplicaciones web:

![Interfaz de usuario de registro de aplicación antigua](../media/active-directory-v2-flows/old_app_registration.PNG)

Por ejemplo, si ha creado un sitio web y una aplicación de iOS, debía registrarlas por separado, con dos id. de aplicación diferentes. Si tenía un sitio web y una API web de back-end, puede que haya registrado cada una de ellas como una aplicación independiente en Azure AD. Si tenía una aplicación de iOS y una aplicación Android, también puede que haya registrado dos aplicaciones diferentes.

<!-- You may have even registered different apps for each of your build environments - one for dev, one for test, and one for production. -->

Ahora todo lo que necesita es un registro de aplicación único y un id. de aplicación único para cada uno de los proyectos. Puede agregar varias "plataformas" a cada proyecto y ofrece los datos adecuados para cada plataforma que agregue. Por supuesto, puede crear tantas aplicaciones como desee, en función de sus requisitos, pero para la mayoría de los casos solo será necesario un id. de aplicación.

<!-- You can also label a particular platform as "production-ready" when it is ready to be published to the outside world, and use that same Application Id safely in your development environments. -->

Nuestro objetivo es que esto dará lugar a una experiencia de desarrollo y administración de aplicaciones más simplificada y que creará una vista más consolidada de un proyecto único en el que podría trabajar.


## Ámbitos, no recursos
En el servicio de disponibilidad general de Azure AD, una aplicación puede comportarse como **recurso**o un destinatario de tokens. Un recurso puede definir varios **ámbitos** o **oAuth2Permissions** que comprende, lo que permite a las aplicaciones cliente solicitar tokens para ese recurso para un conjunto determinado de ámbitos. Piense en la API de Azure AD Graph como ejemplo de un recurso:

- Identificador de recursos o `AppID URI`: `https://graph.windows.net/`
- Ámbitos o `OAuth2Permissions`: `Directory.Read`, `Directory.Write`, etc.  

Todo esto se cumple para el modelo de aplicaciones v2.0. Una aplicación todavía se puede comportar como recurso, definir ámbitos y ser identificada por un URI. Las aplicaciones cliente todavía pueden solicitar acceso a esos ámbitos. Sin embargo, ha cambiado la manera en que un cliente solicita esos permisos. En el pasado, una solicitud de autorización de OAuth 2.0 para Azure AD podría haber tenido un aspecto similar al siguiente:

```
GET https://login.microsoftonline.com/common/oauth2/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&resource=https%3A%2F%2Fgraph.windows.net%2F
...
```

donde el parámetro **resource** indicaba para qué recurso la aplicación cliente solicitaba autorización. Azure AD calculaba los permisos requeridos por la aplicación en función de la configuración estática en el Portal de Azure y emitía tokens en consecuencia. Ahora, la misma solicitud de autorización de OAuth 2.0 tiene un aspecto similar al siguiente:

```
GET https://login.microsoftonline.com/common/v2.0/oauth2/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20https%3A%2F%2Fgraph.windows.net%2Fdirectory.write
...
```

donde el parámetro **scope** indica para qué recursos y permisos está solicitando autorización la aplicación. El recurso deseado sigue estando muy presente en la solicitud; simplemente está incluido en cada uno de los valores del parámetro scope. El uso del parámetro scope de esta manera permite que el modelo de aplicaciones v2.0 sea más compatible con la especificación de OAuth 2.0 y se alinea más estrechamente con prácticas comunes del sector. También permite que las aplicaciones realicen [consentimiento incremental](#incremental-and-dynamic-consent), que se describe en la siguiente sección.

## Consentimiento incremental y dinámico
Las aplicaciones registradas en el servicio de Azure AD de disponibilidad general tenían que especificar sus permisos de OAuth 2.0 necesarios en el Portal de Azure, en el momento de la creación de las aplicaciones:

![Interfaz de usuario de registro de permisos](../media/active-directory-v2-flows/app_reg_permissions.PNG)

Los permisos que una aplicación requerían se configuraban **estáticamente**. Aunque esto permitía que la configuración de la aplicación existiera en el Portal de Azure y mantenía bien el aspecto del código, presentaba algunos problemas para los desarrolladores:

- Una aplicación debía conocer todos los permisos que consumiría alguna vez durante la creación de la aplicación. Agregar permisos con el tiempo era un proceso difícil.
- Una aplicación debía conocer todos los recursos a los que tendría acceso alguna vez durante por adelantado. Era difícil crear aplicaciones que podrían tener acceso a un número arbitrario de recursos.
- Una aplicación tenía que solicitar todos los permisos que podría necesitar alguna vez tras el primer inicio de sesión del usuario. En algunos casos, esto llevaría a una lista muy larga de permisos, lo que desanimaría a los usuarios finales de aprobar el acceso de la aplicación en el inicio de sesión inicial.

En el modelo de aplicaciones 2.0, puede especificar los permisos que necesita su aplicación **dinámicamente** en tiempo de ejecución durante el uso normal de su aplicación. Para ello, puede especificar los ámbitos que necesita su aplicación en un momento determinado en el tiempo incluyéndolos en el parámetro `scope` de una solicitud de autorización:

```
GET https://login.microsoftonline.com/common/v2.0/oauth2/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20https%3A%2F%2Fgraph.windows.net%2Fdirectory.write
...
```

Lo anterior solicita permiso para que la aplicación lea los datos de directorio de un usuario de Azure AD, así como escribir datos en su directorio. Si el usuario ha dado su consentimiento para esos permisos en el pasado para esta aplicación concreta, simplemente introducirán sus credenciales y habrán iniciado sesión en la aplicación. Si el usuario no ha dado su consentimiento a cualquiera de estos permisos, el extremo de v2.0 pedirá al usuario su consentimiento para esos permisos. Para obtener más información, puede leer sobre [permisos, consentimiento y ámbitos](active-directory-v2-scopes.md).

Permitir que una aplicación solicite permisos dinámicamente mediante el parámetro `scope` le da un control total sobre la experiencia del usuario. Si lo desea, puede elegir adelantar su experiencia de consentimiento y pedir todos los permisos en una solicitud de autorización inicial. O bien, si su aplicación requiere un gran número de permisos, puede elegir recopilarlos del usuario de forma incremental, a medida que intentan usar determinadas características de la aplicación con el tiempo.

## Ámbitos conocidos

#### Acceso sin conexión
El modelo de aplicaciones v2.0 presenta un nuevo permiso conocido para las aplicaciones: el ámbito `offline_access`. Todas las aplicaciones deberán solicitar este permiso si necesitan tener acceso a los recursos en nombre de un usuario durante un período de tiempo prolongado, incluso cuando es posible que el usuario no haya estado usando la aplicación activamente. El ámbito `offline_access` le aparecerá al usuario en cuadros de diálogo de consentimiento como "Obtener acceso a los datos sin conexión", que el usuario debe aceptar. Solicitar el permiso `offline_access` permitirá a su aplicación web recibir refresh\_tokens de OAuth 2.0 desde el extremo de v2.0. Los refresh\_tokens son de larga duración y se pueden intercambiar por nuevos access\_tokens de OAuth 2.0 durante largos períodos de acceso.

Si su aplicación no solicita el ámbito de `offline_access`, no recibirá refresh\_tokens. Esto significa que cuando canjees un código de autorización (authorization\_code) del [flujo del código de autorización de OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow), solo recibirás un token de acceso (access\_token) desde el extremo de `/token`. Ese token de acceso seguirá siendo válido durante un breve período de tiempo (normalmente una hora), pero finalmente caducará. En ese momento, la aplicación tendrá que redirigir al usuario de nuevo al extremo de `/authorize` para recuperar un nuevo authorization\_code. Durante esta redirección, es posible o no que el usuario necesite escribir sus credenciales de nuevo o volver a dar el consentimiento a permisos, según el tipo de aplicación.

Para obtener más información acerca de OAuth 2.0, refresh\_tokens y access\_tokens, consulte la [referencia del protocolo del modelo de aplicaciones v2.0](active-directory-v2-protocols.md).

#### OpenID, perfil y correo electrónico

En el servicio de Azure Active Directory original, el flujo de inicio de sesión de OpenID Connect más básico proporciona una gran cantidad de información sobre el usuario en el id\_token resultante. Las notificaciones de un id\_token pueden incluir el nombre de usuario, el nombre de usuario preferido, la dirección de correo electrónico, el id. de objeto, etc.

Ahora restringimos la información que el ámbito `openid` a la que permite acceder su aplicación. El ámbito `openid` solo permitirá que el usuario inicie sesión en la aplicación y que esta reciba un identificador específico de aplicación para el usuario. Si desea obtener información personal identificable (PII) acerca del usuario en la aplicación, esta tendrá que solicitar permisos adicionales al usuario. Introducimos dos nuevos ámbitos (`email` y `profile`) que permiten hacerlo.

El ámbito `email` es muy sencillo: permite que la aplicación acceda a la dirección de correo electrónico principal del usuario a través de la notificación `email` en id\_token. El ámbito `profile` ofrece a la aplicación acceso a toda la demás información básica sobre el usuario: su nombre, el nombre de usuario preferido, identificador de objeto y demás.

Esto le permite codificar la aplicación en un modo de divulgación mínima, puede pedir al usuario solo el conjunto de información que la aplicación necesita para hacer su trabajo. Para más información sobre estos ámbitos, consulte [la referencia de los ámbitos de la versión 2.0](active-directory-v2-scopes.md).


## Notificaciones de token
Las notificaciones en tokens emitidas por el extremo de v2.0 no serán idénticas a los tokens emitidos por los extremos de Azure AD de disponibilidad general; las aplicaciones que migran al nuevo servicio no deben suponer que existirá una notificación concreta en id\_tokens o access\_tokens. Los tokens emitidos por el extremo dev2.0 son compatibles con las especificaciones de OAuth 2.0 y OpenID Connect, pero pueden seguir una semántica diferente a la del servicio de Azure AD de disponibilidad general.

Para obtener información sobre las notificaciones específicas emitidas en tokens del modelo de aplicaciones v2.0, consulte la [referencia del token v2.0](active-directory-v2-tokens.md).


## Limitaciones de vista previa
Hay una serie de restricciones que se deben tener en cuenta al crear una aplicación con el modelo de aplicaciones v2.0 durante la vista previa pública. Consulte el [documento de limitaciones del modelo de aplicaciones v2.0](active-directory-v2-limitations.md) para ver si alguna de estas restricciones se aplica a su escenario concreto.

<!---HONumber=AcomDC_0128_2016-->