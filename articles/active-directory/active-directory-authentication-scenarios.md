
<properties
   pageTitle="Escenarios de autenticación para Azure AD | Microsoft Azure"
   description="Información general de los cinco escenarios de autenticación más comunes para Azure Active Directory (AAD)."
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="msmbaldwin"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="02/09/2016"
   ms.author="mbaldwin"/>

# Escenarios de autenticación para Azure AD

Azure Active Directory (Azure AD) simplifica la autenticación para los desarrolladores, ya que ofrece la identificación como un servicio, con compatibilidad con protocolos genéricos del sector como OAuth 2.0 y OpenID Connect, además de bibliotecas de código abierto para distintas plataformas, para poder empezar a codificar rápidamente. Este documento le ayudará a comprender los diversos escenarios admitidos por Azure AD y le mostrará las tareas iniciales. Se divide en las secciones siguientes:

- [Conceptos básicos sobre autenticación en Azure AD](#basics-of-authentication-in-azure-ad)

- [Notificaciones de tokens de seguridad de Azure AD](#claims-in-azure-ad-security-tokens)

- [Conceptos básicos sobre el registro de una aplicación en Azure AD](#basics-of-registering-an-application-in-azure-ad)

- [Tipos de aplicaciones y escenarios](#application-types-and-scenarios)

  - [Explorador web a aplicación web](#web-browser-to-web-application)

  - [Aplicación de una sola página (SPA)](#single-page-application-spa)

  - [Aplicación nativa a API web](#native-application-to-web-api)

  - [Aplicación web a API web](#web-application-to-web-api)

  - [Aplicación de servidor o de demonio en API web](#daemon-or-server-application-to-web-api)



## Conceptos básicos sobre autenticación en Azure AD

Si no está familiarizado con los conceptos básicos de la autenticación en Azure AD, lea esta sección. De lo contrario, puede pasar a [Tipos de aplicaciones y escenarios](#application-types-and-scenarios).

Veamos el escenario más básico en el que es necesario identificarse: un usuario de un explorador web debe autenticarse en una aplicación web. Este escenario se describe más detalladamente en la sección [Explorador web a aplicación web](#web-browser-to-web-application), pero es un punto de partida útil para ilustrar las capacidades de Azure AD y conceptualizar el funcionamiento del escenario. Tenga en cuenta el diagrama siguiente para este escenario:

![Información general de inicio de sesión en la aplicación web](./media/active-directory-authentication-scenarios/basics_of_auth_in_aad.png)

Teniendo en cuenta el diagrama anterior, a continuación se indica qué debe saber sobre los distintos componentes:

- Azure AD es el proveedor de identidades, responsable de comprobar la identidad de los usuarios y las aplicaciones que existen en el directorio de una organización y, en última instancia, de emitir tokens de seguridad tras la autenticación correcta de dichos usuarios y aplicaciones.


- Una aplicación que desee externalizar la autenticación a Azure AD debe estar registrada en Azure AD, que registra e identifica de modo único la aplicación en el directorio.


- Los desarrolladores pueden usar las bibliotecas de autenticación de código abierto de Azure AD para facilitar la autenticación, ya que administran los detalles de los protocolos para el usuario. Consulte [Bibliotecas de autenticación de Azure Active Directory](active-directory-authentication-libraries.md) para obtener más información.


• Una vez autenticado el usuario, la aplicación debe validar el token de seguridad de este para garantizar que la autenticación se realizó correctamente para las partes implicadas. Los desarrolladores pueden usar las bibliotecas de autenticación proporcionadas para administrar la validación de cualquier token de Azure AD, incluidos los tokens web JSON (JWT) o de SAML 2.0. Si desea realizar la validación manualmente, vea la documentación del [Controlador de tokens JWT](https://msdn.microsoft.com/library/dn205065.aspx).


> [AZURE.IMPORTANT] Azure AD usa criptografía de clave pública para firmar los tokens y verificar que son válidos. Consulte [Información importante acerca de la cadencia de sustitución de clave en Azure AD](https://msdn.microsoft.com/library/azure/dn641920.aspx) para obtener más información sobre la lógica necesaria que debe tener en la aplicación para garantizar que siempre está actualizada con las claves más recientes.


• El flujo de solicitudes y respuestas del proceso de autenticación lo determina el protocolo de autenticación que se use, como OAuth 2.0, OpenID Connect, WS-Federation o SAML 2.0. Estos protocolos se analizan con más detalle en el tema [Protocolos de autenticación de Azure Active Directory](active-directory-authentication-protocols.md) y en las secciones siguientes.

> [AZURE.NOTE] Azure AD admite los estándares OAuth 2.0 y OpenID Connect, que hacen un uso generalizado de tokens portadores, incluidos los representados como JWT. Un token portador es un token de seguridad ligero que concede al "portador" acceso a un recurso protegido. En este sentido, el "portador" es cualquier parte que pueda presentar el token. Aunque una parte debe autenticarse primero con Azure AD para recibir el token portador, si no se realizan los pasos necesarios para asegurar el token en la transmisión y el almacenamiento, este puede interceptarse y ser utilizado por un usuario no deseado. Mientras que algunos tokens de seguridad disponen de un mecanismo integrado para evitar ser usados por partes no autorizadas, los tokens portadores no tienen este mecanismo y deben transportarse en un canal seguro como, por ejemplo, la seguridad de la capa de transporte (HTTPS). Si un token portador se transmite sin cifrar, un usuario malintencionado puede utilizar un ataque de tipo "Man in the middle" para adquirir el token y usarlo para obtener acceso sin autorización a un recurso protegido. Los mismos principios de seguridad se aplican al almacenamiento o almacenamiento en caché de tokens portadores para su uso posterior. Asegúrese siempre de que la aplicación transmite y almacena los tokens portadores de manera segura. Para otras consideraciones sobre la seguridad de los tokens portadores, consulte la [Sección 5 de RFC 6750](http://tools.ietf.org/html/rfc6750).


Ahora que ya tiene información general sobre los conceptos básicos, lea las secciones siguientes para comprender cómo funciona el aprovisionamiento en Azure AD, así como los escenarios comunes admitidos por Azure AD.


## Notificaciones de tokens de seguridad de Azure AD

Los tokens de seguridad que emite Azure AD contienen notificaciones o aserciones de información sobre el usuario que se ha autenticado. La aplicación puede usar estas notificaciones para varias tareas. Por ejemplo, pueden usarse para validar el token, identificar el inquilino de directorio del usuario, mostrar información del usuario o determinar la autorización del usuario, entre otros. Las notificaciones presentes en cualquier token de seguridad dependen del tipo de token, el tipo de credencial que se usa para autenticar al usuario y la configuración de la aplicación. En la tabla siguiente se proporciona una breve descripción de cada tipo de notificación que Azure AD emite. Para obtener más información, consulte [Tipos de token y notificación compatibles](active-directory-token-and-claims.md).


| Notificación | Descripción |
|-------|-------------|
| Identificador de aplicación | Identifica la aplicación que está usando el token.
| Público | Identifica el recurso de destinatario al que está destinado el token. |
| Referencia de clase de contexto de autenticación de aplicación | Indica cómo se autenticó el cliente (cliente público frente a cliente confidencial). |
| Instante de autenticación | Registra la fecha y la hora de la autenticación. |
| Método de autenticación | Indica cómo se autenticó el firmante del token (contraseña, certificado, etc.). |
| Nombre de usuario | Proporciona el nombre de usuario, tal como se estableció en Azure AD. |
| Grupos | Contiene identificadores de objeto de los grupos de Azure AD de los que es miembro el usuario. |
| Proveedor de identidades | Registra el proveedor de identidades que autenticó al firmante del token. |
| Emitido a las | Registra la hora a la que se emitió el token; suele utilizarse para la actualización del token. |
| Emisor | Identifica al STS que emitió el token, así como el inquilino de Azure AD. |
| Apellidos | Proporciona los apellidos del usuario, tal como se establecieron en Azure AD. |
| Nombre | Proporciona un valor en lenguaje natural que identifica al firmante del token. |
| Identificador de objeto | Contiene un identificador único e inmutable del firmante en Azure AD. |
| Roles | Contiene los nombres descriptivos de los roles de aplicación de Azure AD que se han concedido al usuario. |
| Scope | Indica los permisos concedidos a la aplicación cliente. |
| Asunto | Indica la entidad de seguridad sobre la que el token valida información. |
| Identificador de inquilino | Contiene un identificador único e inmutable del inquilino de directorio que emitió el token. |
| Duración del token | Define el intervalo de tiempo dentro del cual es válido un token. |
| Nombre principal de usuario | Contiene el nombre principal de usuario del firmante. |
| Versión | Contiene el número de versión del token. |


## Conceptos básicos sobre el registro de una aplicación en Azure AD

Cualquier aplicación que externalice la autenticación a Azure AD debe registrarse en un directorio. Este paso implica informar a Azure AD de la aplicación, incluidos, entre otros, la dirección URL donde se encuentra, la dirección URL a la que se envían las respuestas tras la autenticación y el URI que identifica la aplicación. Esta información es necesaria por algunos motivos importantes:

- Azure AD necesita coordenadas para comunicarse con la aplicación cuando administra el inicio de sesión o intercambia tokens. Entre ellas se incluyen las siguientes:

  - URI del identificador de la aplicación: identificador de una aplicación. Este valor se envía a Azure AD durante la autenticación para indicar para qué aplicación el llamador quiere un token. Además, este valor se incluye en el token de manera que la aplicación sepa que se trata del objetivo.


  - URL de respuesta y URI de redirección: en el caso de una API web o aplicación web, la dirección URL de respuesta es la ubicación a la que Azure AD enviará la respuesta de autenticación, incluido un token si la autenticación fue correcta. En el caso de una aplicación nativa, el URI de redirección es un identificador único al que Azure AD redirigirá el agente de usuario de una solicitud de OAuth 2.0.


  - Identificador de cliente: identificador de una aplicación que Azure AD genera cuando se registra la aplicación. Al solicitar un token o código de autorización, se envían la clave y el identificador del cliente a Azure AD durante la autenticación.


  - Clave: clave que se envía junto con un identificador de cliente al autenticarse en Azure AD para llamar a una API web.


- Azure AD debe asegurarse de que la aplicación tiene los permisos necesarios para obtener acceso a los datos del directorio, a otras aplicaciones de la organización, etc.

El aprovisionamiento se entiende mejor cuando se comprende que existen dos categorías de aplicaciones que se pueden desarrollar e integrarse con Azure AD:

- Aplicación de un solo inquilino: el uso de una aplicación de un solo inquilino se destina a una sola organización. Suele tratarse de aplicaciones de línea de negocio (LOB) escritas por un desarrollador de la empresa. A una aplicación de un solo inquilino solo obtienen acceso usuarios de un solo directorio y, por tanto, solo es necesario aprovisionarla en un directorio. Suele tratarse de aplicaciones registradas por un desarrollador de la organización.


- Aplicación multiempresa (de varios inquilinos): el uso de una aplicación multiempresa se destina a varias organizaciones, no solo a una. Suele tratarse de aplicaciones de software como servicio (SaaS) escritas por un proveedor de software independiente (ISV). Las aplicaciones multiempresa se deben aprovisionar en cada uno de los directorios en los que se usarán, lo que requiere el consentimiento del usuario o del administrador para registrarlas. El proceso de consentimiento se inicia cuando una aplicación se ha registrado en el directorio y se le proporciona acceso a la API de gráficos o quizás a otra API web. Cuando un usuario o un administrador de otra organización inicia sesión para usar la aplicación, se muestra un cuadro de diálogo que indica los permisos requeridos por la aplicación. A continuación, el usuario o el administrador pueden dar su consentimiento a la aplicación, lo que proporciona a la aplicación acceso a los datos establecidos y, por último, registra la aplicación en el directorio. Para obtener más información, consulte [Información general del marco de consentimiento](active-directory-integrating-applications.md#overview-of-the-consent-framework).

Cuando se desarrolla una aplicación multiempresa en lugar de una aplicación de un solo inquilino hay que tener en cuenta otros aspectos. Por ejemplo, si pone la aplicación a disposición de usuarios de varios directorios, necesitará un mecanismo para determinar en qué inquilino se encuentran. Una aplicación de un solo inquilino únicamente necesita buscar un usuario en su propio directorio, mientras que una aplicación multiempresa debe identificar un usuario específico en todos los directorios de Azure AD. Para realizar esta tarea, Azure AD proporciona un extremo de autenticación común donde cualquier aplicación multiempresa puede dirigir solicitudes de inicio de sesión, en lugar de un extremo específico del inquilino. Este extremo es https://login.microsoftonline.com/common para todos los directorios de Azure AD, mientras que un extremo específico de un inquilino podría ser https://login.microsoftonline.com/contoso.onmicrosoft.com. Es especialmente importante tener en cuenta el extremo común al desarrollar la aplicación porque necesitará toda la lógica necesaria para administrar varios inquilinos durante el inicio y el cierre de sesión y la validación de tokens.

Si actualmente desarrolla una aplicación de un solo inquilino pero quiere ponerla a disposición de varias organizaciones, puede realizar cambios fácilmente en la aplicación y en su configuración en Azure AD para que pueda aceptar varios inquilinos. Además, Azure AD usa la misma clave de firma para todos los tokens en todos los directorios, tanto si se proporciona autenticación para una aplicación de un solo inquilino como para una aplicación multiempresa de varios inquilinos.

Cada uno de los escenarios incluidos en este documento incluye una subsección en donde se describen sus requisitos de aprovisionamiento. Si desea obtener información más detallada sobre el aprovisionamiento de aplicaciones en Azure AD y las diferencias entre aplicaciones de un solo inquilino y multiempresa, vea [Integración de aplicaciones con Azure Active Directory](active-directory-integrating-applications.md). Siga leyendo para comprender los escenarios de aplicación comunes de Azure AD.

## Tipos de aplicaciones y escenarios

Cada uno de los escenarios que se describen en este documento puede desarrollarse con varios lenguajes y plataformas. Todos están respaldados por ejemplos de código completo que están disponibles en nuestra [guía de ejemplos de código](active-directory-code-samples.md) o directamente en los [repositorios de ejemplos de GitHub](https://github.com/Azure-Samples?utf8=%E2%9C%93&query=active-directory) correspondientes. Además, si la aplicación necesita un determinado fragmento o segmento de un escenario de un extremo a otro, dicha funcionalidad se podrá agregar de manera independiente en la mayoría de los casos. Por ejemplo, si tiene una aplicación nativa que llama a una API web, puede agregar fácilmente una aplicación web que también llame a la API web. En el diagrama siguiente se ilustran estos escenarios y tipos de aplicación, además de cómo pueden agregarse distintos componentes:

![Tipos de aplicaciones y escenarios](./media/active-directory-authentication-scenarios/application_types_and_scenarios.png)

Estos son los cinco escenarios principales de aplicación que admite Azure AD:

- [Explorador web a aplicación web](#web-browser-to-web-application): un usuario tiene que iniciar sesión en una aplicación web protegida por Azure AD.

- [Aplicación de una sola página (SPA)](#single-page-application-spa): un usuario tiene que iniciar sesión en una aplicación de una sola página protegida por Azure AD.

- [Aplicación nativa a API web](#native-application-to-web-api): una aplicación nativa que se ejecuta en teléfonos, tabletas o equipos tiene que autenticar un usuario para obtener recursos de una API web protegida por Azure AD.

- [Aplicación web a API web](#web-application-to-web-api): una aplicación web tiene que obtener recursos de una API web protegida por Azure AD.

- [Aplicación de servidor o de demonio en API web](#daemon-or-server-application-to-web-api): una aplicación de demonio o de servidor sin interfaz de usuario web tiene que obtener recursos de una API web protegida por Azure AD.

### Explorador web a aplicación web

En esta sección se describe una aplicación que autentica a un usuario de un explorador web en una aplicación web. En este escenario, la aplicación web dirige el explorador del usuario para que inicie sesión en Azure AD. Azure AD devuelve una respuesta de inicio de sesión a través el explorador del usuario, que contiene notificaciones sobre el usuario en un token de seguridad. Este escenario admite el inicio de sesión mediante los protocolos WS-Federation, SAML 2.0 y OpenID Connect.


#### Diagrama
![Flujo de autenticación de explorador a aplicación web](./media/active-directory-authentication-scenarios/web_browser_to_web_api.png)


#### Descripción del flujo de protocolo


1. Cuando un usuario visita la aplicación y debe iniciar sesión, se le redirige mediante una solicitud de inicio de sesión al extremo de autenticación de Azure AD.


2. El usuario inicia sesión en la página de inicio de sesión.


3. Si la autenticación es correcta, Azure AD crea un token de autenticación y devuelve una respuesta de inicio de sesión a la dirección URL de respuesta de la aplicación que se configuró en el Portal de administración de Azure. En el caso de una aplicación de producción, esta URL de respuesta debe ser HTTPS. El token devuelto incluye notificaciones sobre el usuario y Azure AD, necesarias para que la aplicación valide el token.


4. La aplicación valida el token mediante una clave de firma pública y la información del emisor disponible en el documento de metadatos de federación para Azure AD. Cuando la aplicación haya validado el token, Azure AD inicia una nueva sesión con el usuario. Hasta que expire, esta sesión permitirá al usuario tener acceso a la aplicación.


#### Ejemplos de código


Vea los ejemplos de código para escenarios de explorador web a aplicación web. Agregamos nuevos ejemplos continuamente, así que no olvide consultarlos con frecuencia. [Explorador web a aplicación web](active-directory-code-samples.md#web-browser-to-web-application).


#### Registro


- Un solo inquilino: si compila una aplicación únicamente para la organización, esta debe registrarse en el directorio de la compañía mediante el Portal de administración de Azure.


- Multiempresa (varios inquilinos): si compila una aplicación que puede ser utilizada por usuarios ajenos a la organización, esta debe registrarse en el directorio de la compañía, pero también en el directorio de cada organización que vaya a usar la aplicación. Para que la aplicación esté disponible en su directorio, puede incluir un proceso de inicio de sesión para que los clientes puedan dar su consentimiento a la aplicación. Cuando inicien sesión en la aplicación, se les presentará un cuadro de diálogo que muestra los permisos que la aplicación requiere y la opción de consentimiento. Según los permisos necesarios, es posible que se requiera que un administrador de la otra organización dé su consentimiento. Cuando el usuario o el administrador dan su consentimiento, la aplicación queda registrada en el directorio. Para obtener más información, vea [Integración de aplicaciones con Azure Active Directory](active-directory-integrating-applications.md).


#### Expiración del token

La sesión del usuario expira cuando expira la duración del token emitido por Azure AD. Si lo desea, la aplicación puede reducir este período de tiempo; por ejemplo, cerrar sesiones de usuario en función de un período de inactividad. Cuando la sesión expire, se le pedirá al usuario que vuelva a iniciar sesión.





### Aplicación de una sola página (SPA)


Esta sección describe la autenticación para una aplicación de una sola página que usa Azure AD para proteger su back-end de la API web. Las aplicaciones de una sola página normalmente están estructuradas como una capa de presentación (front-end) de JavaScript que se ejecuta en el explorador y un back-end de la API web que se ejecuta en un servidor e implementa la lógica de negocios de la aplicación. En este escenario, cuando el usuario inicia sesión, el front-end de JavaScript usa la [biblioteca de autenticación de Active Directory para JavaScript (ADAL.JS)](https://github.com/AzureAD/azure-activedirectory-library-for-js/tree/dev) y el protocolo de concesión implícita OAuth 2.0 para obtener un token de identificador (id\_token) de Azure AD. El token se almacena en caché y el cliente lo adjunta a la solicitud como token portador al realizar llamadas al back-end de la API web, que se protege con el middleware de OWIN.


#### Diagrama

![Diagrama de aplicación de una sola página](./media/active-directory-authentication-scenarios/single_page_app.png)

#### Descripción del flujo de protocolo

1. El usuario navega a la aplicación web.


2. La aplicación devuelve el front-end de JavaScript (capa de presentación) en el explorador.


3. El usuario inicia sesión, por ejemplo, haciendo clic en un vínculo de inicio de sesión. El explorador envía un comando GET al extremo de autorización de Azure AD para solicitar un identificador de token. Esta solicitud incluye el identificador de cliente y la URL de respuesta en los parámetros de consulta.


4. Azure AD valida la URL de respuesta contrastándola con la URL de respuesta registrada que se configuró en el Portal de administración de Azure.


5. El usuario inicia sesión en la página de inicio de sesión.


6. Si la autenticación es correcta, Azure AD crea un token de identificador y lo devuelve como un fragmento de URL (#) a la URL de respuesta de la aplicación. En el caso de una aplicación de producción, esta URL de respuesta debe ser HTTPS. El token devuelto incluye notificaciones sobre el usuario y Azure AD, necesarias para que la aplicación valide el token.


7. El código de cliente de JavaScript que se ejecuta en el explorador extrae el token de la respuesta para usarlo para proteger las llamadas al back-end de la API web de la aplicación.


8. El explorador llama al back-end de la API web de la aplicación con el token de acceso en el encabezado de autorización.

#### Ejemplos de código


Consulte los ejemplos de código para escenarios de aplicación de una sola página (SPA). Asegúrese de consultarlos con frecuencia; agregamos nuevos ejemplos continuamente. [Aplicación de una sola página (SPA)](active-directory-code-samples.md#single-page-application-spa).


#### Registro


- Un solo inquilino: si compila una aplicación únicamente para la organización, esta debe registrarse en el directorio de la compañía mediante el Portal de administración de Azure.


- Multiempresa (varios inquilinos): si compila una aplicación que puede ser utilizada por usuarios ajenos a la organización, esta debe registrarse en el directorio de la compañía, pero también en el directorio de cada organización que vaya a usar la aplicación. Para que la aplicación esté disponible en su directorio, puede incluir un proceso de inicio de sesión para que los clientes puedan dar su consentimiento a la aplicación. Cuando inicien sesión en la aplicación, se les presentará un cuadro de diálogo que muestra los permisos que la aplicación requiere y la opción de consentimiento. Según los permisos necesarios, es posible que se requiera que un administrador de la otra organización dé su consentimiento. Cuando el usuario o el administrador dan su consentimiento, la aplicación queda registrada en el directorio. Para obtener más información, vea [Integración de aplicaciones con Azure Active Directory](active-directory-integrating-applications.md).

Después de registrar la aplicación, esta debe configurarse para usar el protocolo de concesión implícita OAuth 2.0. De forma predeterminada, este protocolo está deshabilitado para las aplicaciones. Para habilitar el protocolo de concesión implícita OAuth2 para su aplicación, descargue el manifiesto de aplicación del Portal de administración de Azure, establezca el valor de "oauth2AllowImplicitFlow" en true y, a continuación, vuelva a cargar el manifiesto en el portal. Para obtener instrucciones detalladas, consulte [Habilitar la concesión implícita de OAuth 2.0 para aplicaciones de una sola página](active-directory-integrating-applications.md).


#### Expiración del token

Al usar ADAL.js para administrar la autenticación con Azure AD, se beneficia de varias características que facilitan la actualización de un token caducado, así como la obtención de tokens para recursos de la API web adicionales a los que puede llamar la aplicación. Cuando el usuario se autentica correctamente con Azure AD, se establece una sesión protegida por una cookie entre el explorador y Azure AD. Es importante tener en cuenta que la sesión tiene lugar entre el usuario y Azure AD, no entre el usuario y la aplicación web que se ejecuta en el servidor. Cuando un token expira, ADAL.js usa esta sesión para obtener otro token automáticamente. Esto se hace mediante el uso de un iFrame oculto para enviar y recibir la solicitud con el protocolo de concesión implícita OAuth. ADAL.js también puede usar el mismo mecanismo para obtener acceso automático a tokens de Azure AD para otros recursos de la API web a los que la aplicación llame, siempre que dichos recursos admitan el uso compartido de recursos entre orígenes (CORS), estén registrados en el directorio del usuario y este haya dado el consentimiento requerido durante el inicio de sesión.


### Aplicación nativa a Web API


En esta sección se describe una aplicación nativa que llama a una API web en nombre de un usuario. Este escenario se basa en el tipo de concesión de código de autorización OAuth 2.0 con un cliente público, tal como se describe en la sección 4.1 de la [especificación de OAuth 2.0](http://tools.ietf.org/html/rfc6749). La aplicación nativa obtiene un token de acceso para el usuario mediante el protocolo OAuth 2.0. Este token de acceso se envía después en la respuesta a la API web, que autoriza al usuario y devuelve el recurso deseado.

#### Diagrama

![Diagrama de aplicación nativa a API web](./media/active-directory-authentication-scenarios/native_app_to_web_api.png)

#### Flujo de autenticación para aplicación nativa a API

#### Descripción del flujo de protocolo


Si usa las bibliotecas de autenticación de AD, la mayoría de los detalles del protocolo que se describen a continuación se administran automáticamente, por ejemplo, la ventana emergente del explorador, el almacenamiento en caché de los tokens y la administración de los tokens de actualización.

1. Mediante una ventana emergente del explorador, la aplicación nativa realiza una solicitud al extremo de autorización de Azure AD. Esta solicitud incluye el identificador del cliente y el URI de redirección de la aplicación nativa, tal como se muestra en el Portal de administración, así como el URI del identificador de la aplicación para la API web. Si el usuario todavía no ha iniciado sesión, se le pide que lo haga.


2. Azure AD autentica al usuario. Si se trata de una aplicación de varios inquilinos y se requiere consentimiento para usarla, se pedirá el consentimiento del usuario si todavía no lo ha dado. Después de conceder consentimiento y tras la correcta autenticación, Azure AD emite una respuesta de código de autorización al URI de redirección de la aplicación cliente.


3. Cuando Azure AD devuelve una respuesta de código de autorización al URI de redirección, la aplicación cliente detiene la interacción con el explorador y extrae el código de autorización de la respuesta. Con este código de autorización, la aplicación cliente envía una solicitud al extremo de token de Azure AD que incluye el código de autorización, detalles sobre la aplicación cliente (URI de redirección e identificador del cliente), además del recurso deseado (URI del identificador de aplicación para la API web).


4. Azure AD valida el código de autorización e información sobre la aplicación cliente y la API web. Tras la correcta validación, Azure AD devuelve dos tokens: un token de acceso de JWT y un token de actualización de JWT. Además, Azure AD devuelve información básica sobre el usuario como, por ejemplo, el nombre para mostrar y el identificador de inquilino.


5. A través de HTTPS, la aplicación cliente usa el token de acceso de JWT devuelto para agregar la cadena JWT con una designación “Bearer” en el encabezado Authorization de la solicitud a la API web. A continuación, la API web valida el token de JWT y, si la validación es correcta, devuelve el recurso deseado.


6. Cuando el token de acceso expire, la aplicación cliente recibirá un mensaje de error que indica que el usuario debe volver a autenticarse. Si la aplicación tiene un token de actualización válido, puede usarse para adquirir un nuevo token de acceso sin pedir al usuario que vuelva a iniciar sesión. Si el token de actualización expira, será necesario que la aplicación vuelva a autenticar interactivamente al usuario.


> [AZURE.NOTE] El token de actualización que emite Azure AD se puede usar para obtener acceso a varios recursos. Por ejemplo, si tiene una aplicación cliente con permiso para llamar a dos API web, el token de actualización puede usarse para obtener también un token de acceso a la otra API web.


#### Ejemplos de código


Vea los ejemplos de código en escenarios de aplicación nativa a API web. Agregamos nuevos ejemplos continuamente, así que no olvide consultarlos con frecuencia. [Aplicación nativa a API web](active-directory-code-samples.md#native-application-to-web-api).


#### Registro


- Un solo inquilino: tanto la aplicación nativa como la API web deben estar registradas en el mismo directorio de Azure AD. La API web se puede configurar para que exponga un conjunto de permisos, que se usan para limitar el acceso de la aplicación nativa a sus recursos. A continuación, la aplicación cliente selecciona los permisos deseados en el menú desplegable “Permisos para otras aplicaciones” del Portal de administración de Azure.


- Multiempresa (varios inquilinos): en primer lugar, la aplicación nativa siempre se registra en el directorio del desarrollador o del editor. En segundo lugar, la aplicación nativa está configurada para indicar los permisos que requiere para ser funcional. La lista de permisos necesarios se muestra en un cuadro de diálogo cuando un usuario o administrador del directorio de destino da su consentimiento a la aplicación, lo que la pone a disposición de la organización. Algunas aplicaciones solo requieren permisos de nivel de usuario, para los que cualquier usuario de la organización puede dar el consentimiento. Otras aplicaciones requieren permisos de nivel de administrador, para los que un usuario de la organización no puede dar su consentimiento. Solo un administrador de directorio puede dar su consentimiento a las aplicaciones que requieran este nivel de permisos. Cuando el usuario o el administrador dan su consentimiento, solo la API web queda registrada en el directorio. Para obtener más información, vea [Integración de aplicaciones con Azure Active Directory](active-directory-integrating-applications.md).


#### Expiración del token


Cuando la aplicación nativa usa su código de autorización para obtener un token de acceso de JWT, también recibe un token de actualización de JWT. Cuando el token de acceso expira, se puede usar el token de actualización para volver a autenticar al usuario sin solicitarle que inicie sesión de nuevo. Este token de actualización se usa a continuación para autenticar al usuario, lo que se traduce en un token de acceso y un token de actualización nuevos.





### Aplicación web a Web API


En esta sección se describe una aplicación web que necesita obtener recursos de una API web. En este escenario, existen dos tipos de identidad que la aplicación web puede usar para autenticar y llamar a la API web: una identidad de aplicación o una identidad de usuario delegado. Para el tipo de identidad de aplicación, en este escenario se usan credenciales de cliente de OAuth 2.0 concedidas para autenticarse como aplicación y obtener acceso a la API web. Cuando se usa una identidad de aplicación, la API web solo puede detectar que la aplicación web la llama, ya que la API web no recibe ninguna información sobre el usuario. Si la aplicación recibe información sobre el usuario, se enviará mediante el protocolo de aplicación, sin la firma de Azure AD. La API web confía en que la aplicación web autenticó al usuario. Por ello, este patrón se conoce como subsistema de confianza.

Para el tipo de identidad de usuario delegado, este escenario se puede conseguir de dos maneras: concesión de código de autorización OpenID Connect y OAuth 2.0 con un cliente confidencial. La aplicación web obtiene un token de acceso para el usuario, que demuestra a la API web que el usuario se autenticó correctamente ante la aplicación web y que la aplicación web pudo obtener una identidad de usuario delegado para llamar a la API web. Este token de acceso se envía en la respuesta a la API web, que autoriza al usuario y devuelve el recurso deseado.

#### Diagrama

![Diagrama de aplicación web a API web](./media/active-directory-authentication-scenarios/web_app_to_web_api.png)



#### Descripción del flujo de protocolo

Los tipos de identidad de aplicación y de identidad de usuario delegado se tratan en el flujo siguiente. La diferencia clave entre ambos es que la identidad de usuario delegado debe adquirir primero un código de autorización para que el usuario pueda iniciar sesión y obtener acceso a la API web.

##### Identidad de aplicación con concesión de credenciales de cliente OAuth 2.0

1. Un usuario inicia sesión en una aplicación web mediante Azure AD (vea la sección anterior [Explorador web a aplicación web](#web-browser-to-web-application)).


2. La aplicación web necesita adquirir un token de acceso para poder autenticarse ante la API web y recuperar el recurso deseado. Realiza una solicitud al extremo de token de Azure AD y proporciona las credenciales, el identificador del cliente y el URI del identificador de aplicación de la API web.


3. Azure AD autentica la aplicación y devuelve un token de acceso de JWT que se usa para llamar a la API web.


4. A través de HTTPS, la aplicación web usa el token de acceso de JWT devuelto para agregar la cadena JWT con una designación “Bearer” en el encabezado Authorization de la solicitud a la API web. A continuación, la API web valida el token de JWT y, si la validación es correcta, devuelve el recurso deseado.

##### Identidad de usuario delegado con OpenID Connect

1. Un usuario inicia sesión en una aplicación web mediante Azure AD (vea la sección anterior [Explorador web a aplicación web](#web-browser-to-web-application)). Si el usuario de la aplicación web todavía no ha dado su consentimiento para permitir que la aplicación web llame a la API web en su nombre, el usuario tendrá que dar su consentimiento. La aplicación mostrará los permisos que requiere y, si alguno de estos permisos es de nivel de administrador, un usuario normal del directorio no podrá dar su consentimiento. Este proceso de consentimiento solo se aplica a las aplicaciones multiempresa (de varios inquilinos), no a las aplicaciones de un solo inquilino, puesto que la aplicación ya tendrá los permisos necesarios. Cuando el usuario inicia sesión, la aplicación web recibe un token de identificador con información sobre el usuario, así como un código de autorización.


2. Con el código de autorización emitido por Azure AD, la aplicación web envía una solicitud al extremo de token de Azure AD que incluye el código de autorización, detalles sobre la aplicación cliente (URI de redirección e identificador del cliente), además del recurso deseado (URI del identificador de aplicación para la API web).


3. Azure AD valida el código de autorización e información sobre la aplicación web y la API web. Tras la correcta validación, Azure AD devuelve dos tokens: un token de acceso de JWT y un token de actualización de JWT.


4. A través de HTTPS, la aplicación web usa el token de acceso de JWT devuelto para agregar la cadena JWT con una designación “Bearer” en el encabezado Authorization de la solicitud a la API web. A continuación, la API web valida el token de JWT y, si la validación es correcta, devuelve el recurso deseado.

##### Identidad de usuario delegado con concesión de código de autorización OAuth 2.0

1. Un usuario ya ha iniciado sesión en una aplicación web, cuyo mecanismo de autenticación es independiente de Azure AD.


2. La aplicación web requiere un código de autorización para adquirir un token de acceso, por lo que emite una solicitud a través del explorador al extremo de autorización de Azure AD y proporciona el identificador del cliente y el URI de redirección de la aplicación web después de la correcta autenticación. El usuario inicia sesión en Azure AD.


3. Si el usuario de la aplicación web todavía no ha dado su consentimiento para permitir que la aplicación web llame a la API web en su nombre, el usuario tendrá que dar su consentimiento. La aplicación mostrará los permisos que requiere y, si alguno de estos permisos es de nivel de administrador, un usuario normal del directorio no podrá dar su consentimiento. Este proceso de consentimiento solo se aplica a las aplicaciones multiempresa (de varios inquilinos), no a las aplicaciones de un solo inquilino, puesto que la aplicación ya tendrá los permisos necesarios.


4. Una vez que el usuario haya dado su consentimiento, la aplicación web recibe el código de autorización que necesita para adquirir un token de acceso.


5. Con el código de autorización emitido por Azure AD, la aplicación web envía una solicitud al extremo de token de Azure AD que incluye el código de autorización, detalles sobre la aplicación cliente (URI de redirección e identificador del cliente), además del recurso deseado (URI del identificador de aplicación para la API web).


6. Azure AD valida el código de autorización e información sobre la aplicación web y la API web. Tras la correcta validación, Azure AD devuelve dos tokens: un token de acceso de JWT y un token de actualización de JWT.


7. A través de HTTPS, la aplicación web usa el token de acceso de JWT devuelto para agregar la cadena JWT con una designación “Bearer” en el encabezado Authorization de la solicitud a la API web. A continuación, la API web valida el token de JWT y, si la validación es correcta, devuelve el recurso deseado.

#### Ejemplos de código

Vea los ejemplos de código para escenarios de aplicación web a API web. Agregamos nuevos ejemplos continuamente, así que no olvide consultarlos con frecuencia. [Aplicación web a API web](active-directory-code-samples.md#web-application-to-web-api).


#### Registro

- Un solo inquilino: tanto en el caso de la identidad de aplicación como en el de la identidad de usuario delegado, la aplicación web y la API web deben estar registradas en el mismo directorio de Azure AD. La API web puede configurarse para exponer un conjunto de permisos, que se usan para limitar el acceso de la aplicación web a sus recursos. Si se usa una identidad de usuario delegado, es necesario que la aplicación web seleccione los permisos deseados en el menú desplegable “Permisos para otras aplicaciones” del Portal de administración de Azure. Este paso no es necesario si se usa el tipo de identidad de aplicación.


- Multiempresa (varios inquilinos): en primer lugar, la aplicación web se configura para indicar los permisos que requiere para ser funcional. La lista de permisos necesarios se muestra en un cuadro de diálogo cuando un usuario o administrador del directorio de destino da su consentimiento a la aplicación, lo que la pone a disposición de la organización. Algunas aplicaciones solo requieren permisos de nivel de usuario, para los que cualquier usuario de la organización puede dar el consentimiento. Otras aplicaciones requieren permisos de nivel de administrador, para los que un usuario de la organización no puede dar su consentimiento. Solo un administrador de directorio puede dar su consentimiento a las aplicaciones que requieran este nivel de permisos. Cuando el usuario o el administrador dan su consentimiento, la aplicación web y la API web quedan registradas en el directorio.

#### Expiración del token

Cuando la aplicación web usa su código de autorización para obtener un token de acceso de JWT, también recibe un token de actualización de JWT. Cuando el token de acceso expira, se puede usar el token de actualización para volver a autenticar al usuario sin solicitarle que inicie sesión de nuevo. Este token de actualización se usa a continuación para autenticar al usuario, lo que se traduce en un token de acceso y un token de actualización nuevos.


### Aplicación de servidor o de demonio en API web


En esta sección se describe una aplicación de demonio o de servidor que necesita obtener recursos de una API web. Hay dos subescenarios aplicables a esta sección: un demonio que necesita llamar a una API web, basada en el tipo de concesión de credenciales de cliente OAuth 2.0, y una aplicación de servidor (como, por ejemplo, una API web) que necesita llamar a una API web, basada en la especificación provisional "On-Behalf-Of" de OAuth 2.0.

Es importante entender algunos aspectos para el escenario en el que una aplicación de demonio necesita llamar a una API web. En primer lugar, la interacción con el usuario no es posible con una aplicación de demonio, que requiere que la aplicación tenga su propia identidad. Un ejemplo de aplicación de demonio es un trabajo por lotes o un servicio de sistema operativo que se ejecuta en segundo plano. Este tipo de aplicación solicita un token de acceso con su identidad de aplicación y presenta el identificador del cliente, las credenciales (contraseña o certificado) y el URI del identificador de aplicación a Azure AD. Tras la correcta autenticación, el demonio recibe un token de acceso de Azure AD, que se usa posteriormente para llamar a la API web.

Para el escenario en el que una aplicación de servidor necesita llamar a una API web resulta útil usar un ejemplo. Imagine que un usuario se ha autenticado en una aplicación nativa y que la aplicación nativa necesita llamar a una API web. Azure AD emite un token de acceso de JWT para llamar a la API web. Si la API web necesita llamar a otra API web de nivel inferior, puede usar el flujo "On-Behalf-Of" para delegar la identidad del usuario y autenticarlo en la API web de segundo nivel.

#### Diagrama

![Diagrama de aplicación de servidor o de demonio en API web](./media/active-directory-authentication-scenarios/daemon_server_app_to_web_api.png)

#### Descripción del flujo de protocolo

##### Identidad de aplicación con concesión de credenciales de cliente OAuth 2.0

1. En primer lugar, la aplicación de servidor necesita autenticarse con Azure AD por sí misma, sin ninguna interacción como, por ejemplo, un cuadro de diálogo de inicio de sesión interactivo. Esta realiza una solicitud al extremo de token de Azure AD y proporciona las credenciales, el identificador del cliente y el URI del identificador de aplicación.


2. Azure AD autentica la aplicación y devuelve un token de acceso de JWT que se usa para llamar a la API web.


3. A través de HTTPS, la aplicación web usa el token de acceso de JWT devuelto para agregar la cadena JWT con una designación “Bearer” en el encabezado Authorization de la solicitud a la API web. A continuación, la API web valida el token de JWT y, si la validación es correcta, devuelve el recurso deseado.


##### Identidad de usuario delegado con la especificación provisional "On-Behalf-Of" de OAuth 2.0

En el flujo descrito a continuación se asume que un usuario se ha autenticado en otra aplicación (por ejemplo, una aplicación nativa) y se ha usado su identidad de usuario para adquirir un token de acceso para la API web de primer nivel.

1. La aplicación nativa envía el token de acceso a la API web de primer nivel.


2. La API web de primer nivel envía una solicitud al extremo de token de Azure AD y proporciona el identificador del cliente y las credenciales, así como el token de acceso del usuario. Además, la solicitud se envía con un parámetro on\_behalf\_of que indica que la API web solicita nuevos tokens para llamar a una API web de nivel inferior en nombre del usuario original.


3. Azure AD comprueba que la API web de primer nivel tiene permisos de acceso a la API web de segundo nivel, valida la solicitud y devuelve un token de acceso de JWT y un token de actualización de JWT a la API web de primer nivel.


4. La API web de primer nivel llama después a la API web de segundo nivel a través de HTTPS. Para ello, anexa la cadena de token en el encabezado Authorization de la solicitud. La API web de primer nivel puede seguir llamando a la API web de segundo nivel siempre que el token de acceso y los tokens de actualización sean válidos.

#### Ejemplos de código

Vea los ejemplos de código para escenarios de aplicación de servidor o de demonio en API web. Agregamos nuevos ejemplos continuamente, así que no olvide consultarlos con frecuencia. [Aplicación de servidor o de demonio en API web](active-directory-code-samples.md#server-or-daemon-application-to-web-api)

#### Registro

- Un solo inquilino: tanto en el caso de la identidad de aplicación como en el de la identidad de usuario delegado, la aplicación de demonio o de servidor debe estar registrada en el mismo directorio de Azure AD. La API web puede configurarse para exponer un conjunto de permisos, que se usan para limitar el acceso de la aplicación de demonio o de servidor a sus recursos. Si se usa un tipo de identidad de usuario delegado, es necesario que la aplicación de servidor seleccione los permisos deseados en el menú desplegable “Permisos para otras aplicaciones” del Portal de administración de Azure. Este paso no es necesario si se usa el tipo de identidad de aplicación.


- Multiempresa (varios inquilinos): en primer lugar, la aplicación de demonio o de servidor se configura para indicar los permisos que requiere para ser funcional. La lista de permisos necesarios se muestra en un cuadro de diálogo cuando un usuario o administrador del directorio de destino da su consentimiento a la aplicación, lo que la pone a disposición de la organización. Algunas aplicaciones solo requieren permisos de nivel de usuario, para los que cualquier usuario de la organización puede dar el consentimiento. Otras aplicaciones requieren permisos de nivel de administrador, para los que un usuario de la organización no puede dar su consentimiento. Solo un administrador de directorio puede dar su consentimiento a las aplicaciones que requieran este nivel de permisos. Cuando el usuario o el administrador dan su consentimiento, las dos API web quedan registradas en el directorio.

#### Expiración del token

Cuando la primera aplicación usa su código de autorización para obtener un token de acceso de JWT, también recibe un token de actualización de JWT. Cuando el token de acceso expira, el token de actualización se puede usar para autenticar al usuario sin pedir credenciales. Este token de actualización se usa a continuación para autenticar al usuario, lo que se traduce en un token de acceso y un token de actualización nuevos.

## Otras referencias

[Guía del desarrollador de Azure Active Directory](active-directory-developers-guide.md)

[Ejemplos de código de Azure Active Directory](active-directory-code-samples.md)

[Información importante acerca de la cadencia de sustitución de clave en Azure AD](https://msdn.microsoft.com/library/azure/dn641920.aspx)

[OAuth 2.0 en Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx)

<!----HONumber=AcomDC_0211_2016-->