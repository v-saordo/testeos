<properties
	pageTitle="Restricciones y limitaciones del modelo de aplicación v2.0 | Microsoft Azure"
	description="Una lista de limitaciones y restricciones con el modelo de aplicaciones v2.0 de Azure AD."
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

# Vista previa del modelo de aplicaciones v2.0: limitaciones y restricciones

Hay varias características y funcionalidades del modelo de aplicaciones v2.0 que no están admitidas aún en el período de vista previa pública. Se quitará cada una de estas limitaciones antes de que el modelo de aplicaciones v2.0 alcance la disponibilidad con carácter general, pero se recomienda que se tengan en cuenta si está compilando aplicaciones durante la vista previa pública.

> [AZURE.NOTE]Esta información se aplica a la vista previa pública del modelo de aplicaciones v2.0. Para obtener instrucciones sobre cómo integrar con el servicio de Azure AD disponible con carácter general, consulte la [Guía para desarrolladores de Azure Active Directory](active-directory-developers-guide.md).

## Soporte técnico para las aplicaciones de producción
Las aplicaciones que se integran con el modelo de aplicaciones v2.0 no se deberían publicar como aplicaciones de nivel de producción. El modelo de aplicaciones v2.0 se encuentra en vista previa pública en este momento: se pueden introducir cambios en cualquier momento y no hay ningún SLA garantizado por el servicio. No se ofrecerá soporte técnico para los incidentes que puedan producirse. Si está dispuesto a aceptar el riesgo de tomar una dependencia en un servicio que aún está en desarrollo, se le aconseja que se ponga en contacto con nosotros a través de @AzureAD para tratar el ámbito de su aplicación o servicio.

## Restricciones en las aplicaciones
Los siguientes tipos de aplicaciones no se admiten actualmente en la vista previa pública del modelo de aplicaciones v2.0. Para obtener una descripción de los tipos de aplicaciones admitidos, consulte [este artículo](active-directory-v2-flows.md).

##### Aplicaciones de una página (Javascript)
Muchas aplicaciones modernas tienen una aplicación de una página escrita en front-end, principalmente en javascript, que usa a menudo marcos SPA como AngularJS, Ember.js, Durandal, etc. El servicio Azure AD, disponible con carácter general, admite estas aplicaciones mediante el [flujo implícito de OAuth 2.0](active-directory-v2-protocols.md#oauth2-implicit-flow); sin embargo, este flujo no está disponible aún en el modelo de aplicaciones v2.0. Lo estará en el corto plazo.

Si está impaciente por tener un SPA que funcione con el modelo de aplicaciones v2.0, puede implementar la autenticación mediante el [flujo de la aplicación de servidor web](active-directory-v2-flows.md#web-apps) descrito anteriormente. Sin embargo, este no es el enfoque recomendado y la documentación para este escenario será limitada. Si desea hacerse una idea del escenario SPA, puede consultar el [ejemplo de código SPA de Azure AD, disponible con carácter general](active-directory-devquickstarts-angular.md).

##### Demonios/aplicaciones del lado del servidor
Las aplicaciones que contienen procesos de larga duración o que funcionan sin la presencia de un usuario también necesitan un modo de acceder a los recursos protegidos, como las API web. Estas aplicaciones pueden autenticar y obtener tokens con la identidad de la aplicación (en lugar de una identidad delegada del usuario) mediante el [flujo de credenciales de cliente de OAuth 2.0](active-directory-v2-protocols.md#oauth2-client-credentials-grant-flow).

Este flujo no es compatible actualmente con el modelo de aplicaciones v2.0, lo que quiere decir que las aplicaciones solo pueden obtener tokens una vez se haya producido un flujo de inicio de sesión de un usuario interactivo. El flujo de credenciales de cliente se agregará en un futuro próximo. Si desea ver el flujo de credenciales de cliente en el servicio Azure AD, disponible con carácter general, consulte el [ejemplo de demonio en GitHub](https://github.com/AzureADSamples/Daemon-DotNet).

##### API web encadenadas (en nombre de)
Muchas arquitecturas incluyen una API web que necesita llamar a otra API web de nivel inferior, ambas protegidas mediante el modelo de aplicaciones v2.0. Este escenario es común en los clientes nativos que tienen una API web back-end, que, a su vez, llama a un servicio de Microsoft Online, como Office 365 o Graph API.

Este escenario de API web encadenada puede admitirse mediante la concesión de credenciales de portador Jwt de OAuth 2.0, también conocido como el [flujo "en nombre de"](active-directory-v2-protocols.md#oauth2-on-behalf-of-flow). Sin embargo, el flujo "en nombre de" no está implementado actualmente en la vista previa del modelo de aplicaciones v2.0. Para ver cómo funciona este flujo en el servicio Azure AD, disponible con carácter general, consulte el [ejemplo de código "en nombre de" en GitHub](https://github.com/AzureADSamples/WebAPI-OnBehalfOf-DotNet).

##### API web independiente
En la vista previa del modelo de aplicaciones v2.0, puede [crear una API web que se protege mediante tokens de OAuth](active-directory-v2-flows.md#web-apis) desde el extremo v2.0. Sin embargo, esa API web sólo podrá recibir tokens de un cliente que comparta el mismo Id. de aplicación. No se admite la creación de un servicio web de terceros al que se acceda desde varios clientes diferentes.

Para ver cómo se crea una API web que acepte tokens de un cliente conocido con el mismo Id. de aplicación, vea los ejemplos de API web del modelo de aplicaciones v2.0 en nuestra sección [Introducción](active-directory-appmodel-v2-overview.md#getting-started).

## Restricciones para los usuarios
Actualmente todas las aplicaciones creadas con el modelo de aplicaciones v2.0 se expondrán públicamente para todos los usuarios con una cuenta Microsoft o una cuenta de Azure AD. Cualquier usuario con cualquier tipo de cuenta podrá instalar o navegar a su aplicación, escribir sus credenciales en el modelo de aplicaciones v2.0 y dar su consentimiento a los permisos de su aplicación correctamente. Puede escribir el código de su aplicación para rechazar inicios de sesión de ciertos conjuntos de usuarios, pero esto no les impedirá dar su consentimiento a la aplicación.

De hecho, sus aplicaciones no pueden restringir los tipos de usuario que pueden iniciar sesión en la aplicación. No podrá crear aplicaciones de línea de negocio (restringidas a los usuarios que pertenezcan una organización), aplicaciones disponibles solo para usuarios de empresas (con una cuenta de Azure AD) o aplicaciones disponibles solo para consumidores (con una cuenta Microsoft).

## Restricciones en los registros de aplicaciones
En este momento, todas las aplicaciones que deseen integrarse en el modelo de aplicaciones v2.0 deben crear un nuevo registro de aplicación en [apps.dev.microsoft.com](https://apps.dev.microsoft.com). Ninguna aplicación de Azure AD o de cuenta Microsoft existente será compatible con el modelo de aplicaciones v2.0, ni tampoco las aplicaciones registradas en cualquier portal que no sea el nuevo portal de registro de aplicaciones. No hay ninguna ruta de acceso de migración para una aplicación del servicio Azure AD, disponible con carácter general, para el modelo de aplicaciones v2.0.

De igual forma, las aplicaciones registradas en el nuevo portal de registro de aplicaciones funcionan exclusivamente con el modelo de aplicaciones v2.0. No puede usar el portal de registro de aplicaciones para crear aplicaciones que se integren correctamente con los servicios de Azure AD o de cuenta Microsoft.

Las aplicaciones que se registran en el nuevo portal de registro de aplicaciones están restringidas actualmente a un conjunto limitado de valores de parámetro redirect\_uri. El parámetro redirect\_uri para aplicaciones y servicios web deben comenzar con el esquema o `https`, mientras que el redirect\_uri para todas las demás plataformas debe utilizar el valor codificado de forma rígida de `urn:ietf:oauth:2.0:oob`.

Para obtener información sobre cómo registrar una aplicación en el nuevo portal de registro de aplicaciones, consulte [este artículo](active-directory-v2-app-registration.md).

## Restricciones en los servicios y API
El modelo de aplicaciones v2.0 es compatible actualmente con el inicio de sesión en cualquier aplicación registrada en el nuevo portal de registro de aplicaciones, siempre que esta se encuentre en la lista de [flujos de autenticación compatibles](active-directory-v2-flows.md). Sin embargo, estas aplicaciones sólo podrán adquirir tokens de acceso de OAuth 2.0 para un conjunto de recursos muy limitado. El extremo v2.0 solo emitirá access\_tokens para:

- La aplicación que solicita el token. Una aplicación puede adquirir un access\_token por sí misma si la aplicación lógica se compone de varios componentes o niveles diferentes. Para ver este escenario en acción, consulte nuestros tutoriales de [Introducción](active-directory-appmodel-v2-overview.md#getting-started).
- El correo electrónico de Outlook, el calendario y los contactos de las API de REST se encuentran todos ellos en https://outlook.office.com. Para obtener información sobre cómo compilar una aplicación que tenga acceso a estas API, consulte estos tutoriales de [Introducción a Office](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2).

Se agregarán más servicios de Microsoft Online en un futuro cercano, así como la compatibilidad con sus propios servicios y API web.

## Restricciones en las bibliotecas y SDK
No todos los lenguajes y plataformas tienen bibliotecas que admitan la vista previa del modelo de aplicaciones v2.0. El conjunto de bibliotecas de autenticación se limita actualmente a. NET, iOS, Android, NodeJS y Javascript. Los ejemplos de código y tutoriales correspondientes para cada uno de ellos están disponibles en nuestra sección [Introducción](active-directory-appmodel-v2-overview.md#getting-started).

Si desea integrar una aplicación en el modelo de aplicaciones v2.0 con otro lenguaje o plataforma, consulte la [referencia de protocolo de OAuth 2.0 y OpenID Connect](active-directory-v2-protocols.md), que le enseñarán cómo construir los mensajes HTTP necesarios para comunicarse con el extremo v2.0.

## Restricciones en los protocolos
El modelo de aplicaciones v2.0 es compatible con Open ID Connect y OAuth 2.0. Sin embargo, no se han incorporado todas las características y capacidades de cada protocolo en el modelo de aplicaciones v2.0. Estos son algunos ejemplos:

- Compatibilidad completa con el parámetro `prompt` de OpenID Connect
- Parámetro `login_hint` de OpenID Connect
- Parámetro `domain_hint` de OpenID Connect
- OpenID Connect `end_sesssion_endpoint`

Para comprender mejor el alcance de la funcionalidad del protocolo compatible con el modelo de aplicaciones v2.0, lea nuestra [referencia de protocolo OpenID Connect y OAuth 2.0](active-directory-v2-protocols.md).

<!---HONumber=AcomDC_1217_2015-->