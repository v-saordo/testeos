<properties
	pageTitle="Restricciones y limitaciones del punto de conexión v2.0 | Microsoft Azure"
	description="Una lista de limitaciones y restricciones con el punto de conexión v2.0 de Azure AD."
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

# ¿Debo usar el punto de conexión v2.0? 

Al crear aplicaciones que se integran con Azure Active Directory, necesitará decidir si los protocolos de autenticación y el punto de conexión v2.0 cubrirán sus necesidades. El modelo de aplicaciones original de Azure AD sigue siendo totalmente compatible y, en algunos aspectos, tiene más características que v2.0. Sin embargo, el punto de conexión v2.0 [introduce importantes ventajas](active-directory-v2-compare.md) para los desarrolladores que pueden convencerle de usar el nuevo modelo de programación. Con el tiempo, v2.0 crecerá para abarcar todas las características de Azure AD, para que solo necesite usar el punto de conexión v2.0.

En este momento, hay dos características principales que puede lograr con el punto de conexión v2.0:

- Inicio de sesión de los usuarios con cuentas profesionales y personales.
- Llamada a la [API de Outlook convergente](https://dev.outlook.com).

Ambas características pueden implementarse en aplicaciones para sitios web, móviles y PC por igual. Si estas características relativamente limitadas tienen sentido para su aplicación, se recomienda usar el punto de conexión v2.0. Si la aplicación requiere alguna funcionalidad adicional de los servicios de Microsoft, se recomienda que seguir usando los puntos de conexión comprobados y reales de Azure AD y la cuenta de Microsoft. Con el tiempo, los puntos de conexión v2.0 incluirán la cuenta de Microsoft y Azure AD y ayudaremos a todos los desarrolladores a pasar al punto de conexión v2.0.

Mientras tanto, este artículo está pensado para ayudarle a determinar si el punto de conexión v2.0 es para usted. Seguiremos actualizando este artículo a lo largo del tiempo para reflejar el estado actual del punto de conexión v2.0, así que consúltelo de nuevo para volver a evaluar sus requisitos en relación con las funcionalidades de v2.0.

Si tiene una aplicación existente con Azure AD que no utiliza el punto de conexión v2.0, no es necesario empezar desde cero. En el futuro proporcionaremos una forma de habilitar las aplicaciones de Azure AD existentes para usarlas con el punto de conexión v2.0.

## Restricciones en las aplicaciones
Los siguientes tipos de aplicaciones no son compatibles actualmente con el punto de conexión v2.0. Para obtener una descripción de los tipos de aplicaciones admitidos, consulte [este artículo](active-directory-v2-flows.md).

##### API web independiente
Con el punto de conexión v2.0, tiene la capacidad de [crear una API web protegida mediante OAuth 2.0](active-directory-v2-flows.md#web-apis). Sin embargo, esa API web solo podrá recibir tokens de una aplicación que comparta el mismo identificador de aplicación. No se admite la creación de una API web a la que se acceda con otro identificador de aplicación. Ese cliente no podrá solicitar u obtener permisos para su API web.

Para ver cómo se crea una API web que acepte tokens de un cliente con el mismo identificador de aplicación, consulte los ejemplos de API web del punto de conexión v2.0 en nuestra sección [Introducción](active-directory-appmodel-v2-overview.md#getting-started).

##### Demonios/aplicaciones del lado del servidor
Las aplicaciones que contienen procesos de larga duración o que funcionan sin la presencia de un usuario también necesitan un modo de acceder a los recursos protegidos, como las API web. Estas aplicaciones pueden autenticar y obtener tokens con la identidad de la aplicación (en lugar de una identidad delegada del usuario) mediante el flujo de credenciales de cliente de OAuth 2.0.

Este flujo no es compatible actualmente con el punto de conexión v2.0, lo que quiere decir que las aplicaciones solo pueden obtener tokens una vez que se haya producido un flujo de inicio de sesión de un usuario interactivo. El flujo de credenciales de cliente se agregará en un futuro próximo. Si quiere ver el flujo de credenciales del cliente en el punto de conexión de Azure AD original, consulte el [ejemplo de demonio en GitHub](https://github.com/AzureADSamples/Daemon-DotNet).

##### Flujo en nombre de la API web
Muchas arquitecturas incluyen una API web que necesita llamar a otra API web de nivel inferior, ambas protegidas mediante el punto de conexión v2.0. Este escenario es común en los clientes nativos que tienen una API web back-end, que, a su vez, llama a un servicio de Microsoft Online u otra API web creada a medida que admite Azure AD.

Este escenario puede admitirse mediante la concesión de credenciales de portador Jwt de OAuth 2.0, también conocido como flujo "en nombre de". Sin embargo, el flujo "en nombre de" no se admite actualmente para el punto de conexión v2.0. Para ver cómo funciona este flujo en el servicio Azure AD, disponible con carácter general, consulte el [ejemplo de código "en nombre de" en GitHub](https://github.com/AzureADSamples/WebAPI-OnBehalfOf-DotNet).

## Restricciones en los registros de aplicaciones
En este momento, todas las aplicaciones que deseen integrarse en el punto de conexión v2.0 deben crear un nuevo registro de aplicaciones en [apps.dev.microsoft.com](https://apps.dev.microsoft.com). Ninguna aplicación de Azure AD o de cuenta de Microsoft existente será compatible con el punto de conexión v2.0, ni tampoco las aplicaciones registradas en cualquier portal que no sea el nuevo portal de registro de aplicaciones. Tenemos previsto proporcionar una manera de habilitar aplicaciones existentes para su uso como una aplicación de v2.0, pero en este momento, no hay ninguna ruta de migración para una aplicación al punto de conexión v2.0.

De forma similar, las aplicaciones registradas en el nuevo portal de registro de aplicaciones no funcionarán en el punto de conexión de autenticación de Azure AD original. Sin embargo, puede usar las aplicaciones creadas en el portal de registro de aplicaciones para una integración correcta con el punto de conexión de autenticación de cuentas de Microsoft, `https://login.live.com`.

Las aplicaciones que se registran en el nuevo portal de registro de aplicaciones están restringidas actualmente a un conjunto limitado de valores de parámetro redirect\_uri. El parámetro redirect\_uri para aplicaciones y servicios web deben comenzar con el esquema o `https`, mientras que el redirect\_uri para todas las demás plataformas debe utilizar el valor codificado de forma rígida de `urn:ietf:oauth:2.0:oob`.

Para obtener información sobre cómo registrar una aplicación en el nuevo portal de registro de aplicaciones, consulte [este artículo](active-directory-v2-app-registration.md).

## Restricciones en los servicios y API
Actualmente, el punto de conexión v2.0 es compatible con el inicio de sesión en cualquier aplicación registrada en el nuevo portal de registro de aplicaciones, siempre que se encuentre en la lista de [flujos de autenticación compatibles](active-directory-v2-flows.md). Sin embargo, estas aplicaciones sólo podrán adquirir tokens de acceso de OAuth 2.0 para un conjunto de recursos muy limitado. El extremo v2.0 solo emitirá access\_tokens para:

- La aplicación que solicita el token. Una aplicación puede adquirir un access\_token por sí misma si la aplicación lógica se compone de varios componentes o niveles diferentes. Para ver este escenario en acción, consulte nuestros tutoriales de [Introducción](active-directory-appmodel-v2-overview.md#getting-started).
- El correo electrónico de Outlook, el calendario y los contactos de las API de REST se encuentran todos ellos en https://outlook.office.com. Para obtener información sobre cómo compilar una aplicación que tenga acceso a estas API, consulte estos tutoriales de [Introducción a Office](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2).
- Las API de Microsoft Graph. Para más información acerca de Microsoft Graph y todos los datos que está disponibles, consulte [https://graph.microsoft.io](https://graph.microsoft.io).

No hay otros servicios compatibles en este momento. Se agregarán más servicios de Microsoft Online en el futuro, así como la compatibilidad con sus propios servicios y API web.

## Restricciones en las bibliotecas y SDK
Para ayudarle a realizar pruebas, hemos proporcionado una versión experimental de la biblioteca de autenticación de Active Directory que es compatible con el punto de conexión v2.0. Sin embargo, esta versión de ADAL es una versión preliminar: no es compatible y cambiará drásticamente durante los próximos meses. Hay ejemplos de código que usan ADAL para. NET, iOS, Android y Javascript disponibles en nuestra sección [Introducción](active-directory-appmodel-v2-overview.md#getting-started) si desea obtener rápidamente una aplicación que se ejecute con el punto de conexión v2.0.

Si desea utilizar el punto de conexión v2.0 en una aplicación de producción, tiene las siguientes opciones:

- Si está creando una aplicación web, puede utilizar sin ningún riesgo nuestro software intermedio del lado del servidor disponible con carácter general, para realizar el inicio de sesión y la validación de tokens. Incluye el software intermedio OWIN Open ID Connect para ASP.NET y nuestro complemento NodeJS Passport. También hay ejemplos de código con este software intermedio disponibles en nuestra sección [Introducción](active-directory-appmodel-v2-overview.md#getting-started).
- Para otras plataformas y aplicaciones nativas y móviles, también puede integrarse con el punto de conexión v2.0 directamente enviando y recibiendo mensajes de protocolo en el código de su aplicación. Los protocolos v2.0 OpenID Connect y OAuth [se han documentado explícitamente](active-directory-v2-protocols.md) para ayudarle a realizar dicha integración.
- Por último, puede usar las bibliotecas de código abierto de Open ID Connect y OAuth para integrarse con el punto de conexión v2.0. El protocolo v2.0 debe ser compatible con muchas bibliotecas de código abierto de los protocolos sin cambios importantes. La disponibilidad de estas bibliotecas varía según la plataforma y el lenguaje, y en los sitios web de [OpenID Connect](http://openid.net/connect/) y [OAuth 2.0](http://oauth.net/2/) se mantiene una lista de las implementaciones populares. A continuación se muestran las bibliotecas de código abierto del cliente y ejemplos que se han probado con el punto de conexión v2.0. Tenga en cuenta que características como el [Registro de clientes dinámico de OpenID Connect](https://openid.net/specs/openid-connect-registration-1_0.html) y los puntos de conexión de validación de tokens no son compatibles todavía y puede ser necesario que estén deshabilitadas en la biblioteca para que funcionen con el punto de conexión v2: 

  - [Servidor de identidades de Java WSO2](https://docs.wso2.com/display/IS500/Introducing+the+Identity+Server)
  - [Federación Java Gluu](https://github.com/GluuFederation/oxAuth)
  - [Node.Js passport-openidconnect](https://www.npmjs.com/package/passport-openidconnect)
  - [Cliente básico de PHP OpenID Connect](https://github.com/jumbojett/OpenID-Connect-PHP)
  - [Ejemplo de OpenID Connect para Android](https://github.com/learning-layers/android-openid-connect)

## Restricciones en los protocolos
El punto de conexión v2.0 solo es compatible con OpenID Connect y OAuth 2.0. Sin embargo, no se han incorporado todas las características y capacidades de cada protocolo en el punto de conexión v2.0. Estos son algunos ejemplos:

- OpenID Connect `end_sesssion_endpoint`
- Concesión de las credenciales del cliente OAuth 2.0

Para comprender mejor el alcance de la funcionalidad del protocolo compatible con el punto de conexión v2.0, lea nuestra [referencia de los protocolos OpenID Connect y OAuth 2.0](active-directory-v2-protocols.md).

## Características avanzadas de Azure AD para desarrolladores
Hay un conjunto de características para desarrolladores en el servicio de Azure Active Directory que todavía no son compatibles para el punto de conexión v2.0, incluidos:

- Notificaciones de grupo para usuarios de Azure AD
- Roles de aplicación y notificaciones de rol

<!---HONumber=AcomDC_0224_2016-->