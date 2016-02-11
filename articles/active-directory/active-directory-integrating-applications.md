<properties
   pageTitle="Integración de aplicaciones con Azure Active Directory | Microsoft Azure"
   description="Información detallada sobre cómo agregar, actualizar o quitar una aplicación en Azure Active Directory (Azure AD)."
   services="active-directory"
   documentationCenter=""
   authors="msmbaldwin"
   manager="mbaldwin"
   editor="mbaldwin" />
<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="01/08/2016"
   ms.author="mbaldwin;bryanla" />

# Integración de aplicaciones con Azure Active Directory

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Los desarrolladores de la empresa y los proveedores de software como servicio (SaaS) pueden desarrollar aplicaciones de línea de negocio o servicios comerciales en la nube que se pueden integrar con Azure Active Directory (Azure AD) para ofrecer inicio de sesión seguro y autorización en sus servicios. Para integrar una aplicación o un servicio con Azure AD, un desarrollador debe registrar primero los detalles de la aplicación con Azure AD mediante el Portal de Azure clásico.

En este artículo se muestra cómo agregar, actualizar o quitar una aplicación en Azure AD. Aprenderá sobre los diferentes tipos de aplicaciones que se pueden integrar con Azure AD, cómo configurar las aplicaciones para que tengan acceso a otros recursos como las API web y mucho más.

Para obtener más información acerca de los dos objetos de Azure AD que representan una aplicación registrada y la relación entre ellos, consulte [Objetos Application y objetos ServicePrincipal](active-directory-application-objects.md); para obtener más información acerca de las directrices de personalización de marca debe usar al desarrollar aplicaciones con Azure Active Directory, vea [Directrices de personalización de marca para aplicaciones](active-directory-branding-guidelines.md).

## Adición de una aplicación

Cualquier aplicación que quiera usar las funciones de Azure AD debe registrarse primero en un inquilino de Azure AD. Este proceso de registro implica proporcionar los detalles de Azure AD sobre la aplicación, como la dirección URL donde se encuentra, la dirección URL para enviar respuestas cuando un usuario está autenticado, el URI que identifica la aplicación y así sucesivamente.

Si está creando una aplicación web que solo necesita admitir el inicio de sesión para usuarios en Azure AD, basta con que siga las instrucciones siguientes. Si la aplicación necesita acceso a una API web o desea habilitar la aplicación como multiinquilino para permitir que los usuarios de otros inquilinos de Azure AD tengan acceso a ella, deberá seguir leyendo la sección [Actualización de una aplicación](#updating-an-application) para continuar con la configuración de la aplicación.

### Para registrar una aplicación nueva en el Portal de Azure clásico

1. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com).

1. Haga clic en el icono de Active Directory en el menú de la izquierda y luego en el directorio que quiera.

1. En el menú superior, haga clic en Aplicaciones. Si no se han agregado aplicaciones al directorio, esta página solo muestra el vínculo Agregar una aplicación. Haga clic en el vínculo, o como alternativa, puede hacer clic en el botón Agregar de la barra de comandos.

1. En la página ¿Qué desea hacer?, haga clic en el vínculo para Agregar una aplicación que mi organización está desarrollando.

1. En la página Proporcione información sobre su aplicación, debe especificar un nombre para la aplicación e indicar el tipo de aplicación que va a registrar con Azure AD. Puede elegir entre una aplicación web o una API web (valor predeterminado, conocida como cliente confidencial en la jerga de OAuth2), o una aplicación cliente nativa que represente una aplicación que se instala en un dispositivo como, por ejemplo, un teléfono o un equipo (conocida como cliente público en la jerga de OAuth2). Una vez finalizado, haga clic en el icono de flecha situado en la esquina inferior derecha de la página.

1. En la página Propiedades de la aplicación, indique la URL de inicio de sesión y el URI de id. de aplicación de la aplicación web (o simplemente el URI de redirección de una aplicación cliente nativa) y haga clic en la casilla situada en la esquina inferior derecha de la página.

1. La aplicación se agrega y le llevará a la página Inicio rápido de la aplicación. En función de que se trate de una aplicación web o nativa, verá opciones diferentes para agregar otras opciones a la aplicación. Una vez agregada la aplicación, puede empezar a actualizarla para permitir que los usuarios inicien sesión, tengan acceso a las API web de otras aplicaciones o configuren una aplicación multiinquilino (lo cual permite que otras organizaciones tengan acceso a la aplicación).

>[AZURE.NOTE]De forma predeterminada, el registro de aplicaciones recién creadas está configurado para permitir que los usuarios del directorio inicien sesión en la aplicación.

## Actualización de una aplicación

Una vez registrada la aplicación con Azure AD, es posible que tenga que actualizarse para proporcionar acceso a las API web, ponerla a disposición de otras organizaciones y mucho más. En esta sección se describe cómo configurar aún más la aplicación. Para obtener más información sobre la forma en que la autenticación funciona en Azure AD, vea [Escenarios de autenticación para Azure AD](active-directory-authentication-scenarios.md).

### Información general sobre el marco de consentimiento

El marco de consentimiento de Azure AD facilita el desarrollo de aplicaciones cliente web y nativas multiinquilino que requieren acceso a API web protegidas por un inquilino de Azure AD diferente de aquel donde se registra la aplicación cliente. Estas API web incluyen la API Graph, Office 365 y otros servicios de Microsoft, además de sus propias API web. El marco se basa en que un usuario o un administrador da consentimiento a una aplicación que solicita el registro en su directorio, lo cual puede implicar el acceso a los datos de directorio.

Por ejemplo, si una aplicación cliente web necesita llamar a la API web de Office 365 o la aplicación de recursos para leer información de calendario sobre el usuario, ese usuario tendrá que dar su consentimiento a la aplicación cliente. Después de dar su consentimiento, la aplicación cliente podrá llamar a la API web de Office 365 en nombre del usuario y usar la información de calendario según sea necesario.

El marco de consentimiento se basa en OAuth 2.0 y sus distintos flujos, como la concesión de credenciales de cliente y la concesión de código de autorización, mediante clientes públicos o confidenciales. Mediante el uso de OAuth 2.0, Azure AD permite crear muchos tipos diferentes de aplicaciones cliente, como en un teléfono, tableta, servidor o una aplicación web, y obtener acceso a los recursos necesarios.

Para obtener más información sobre el marco de consentimiento, consulte [OAuth 2.0 en Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx), [Escenarios de autenticación para Azure AD](active-directory-authentication-scenarios.md) y el tema de Office 365 [Understanding authentication with Office 365 APIs (Descripción de la autenticación con la API de Office 365)](https://msdn.microsoft.com/office/office365/howto/common-app-authentication-tasks).

#### Ejemplo de la experiencia de consentimiento

Los siguientes pasos le mostrarán cómo funciona la experiencia de consentimiento para el desarrollador de la aplicación y el usuario.

1. En la página de configuración de la aplicación del cliente web del Portal de Azure clásico, establezca los permisos que requiere la aplicación mediante los menús desplegables del control Permisos para otras aplicaciones.

    ![Permisos para otras aplicaciones](./media/active-directory-integrating-applications/permissions.png)

1. Plantéese la posibilidad de que los permisos de la aplicación estén actualizados, la aplicación se ejecute y un usuario esté a punto de usarla por primera vez. Si la aplicación todavía no adquirió un token de acceso o de actualización, tiene que ir al extremo de autorización de Azure AD para obtener un código de autorización que sirve para adquirir un nuevo token de acceso y de actualización.

1. Si el usuario no está ya autenticado, se le pedirá que inicie sesión en Azure AD.

    ![Inicio de sesión de usuario o administrador en Azure AD](./media/active-directory-integrating-applications/useradminsignin.png)

1. Cuando el usuario inicie sesión, Azure AD determinará si se debe mostrar una página de consentimiento al usuario. Esta determinación se basa en que el usuario (o el administrador de la organización) ya concediese el consentimiento de la aplicación. Si todavía no se concedió el consentimiento, Azure AD se lo pedirá al usuario y mostrará los permisos necesarios para que funcione. El conjunto de permisos que aparecen en el cuadro de diálogo de consentimiento son los mismos que se seleccionaron en el control Permisos para otras aplicaciones del Portal de Azure clásico.

    ![Experiencia de consentimiento de usuario](./media/active-directory-integrating-applications/userconsent.png)

1. Después de que el usuario concede el consentimiento, se devuelve un código de autorización para la aplicación, que se puede canjear para adquirir un token de acceso y un token de actualización. Para obtener más información sobre este flujo, vea la sección [Aplicación web a API web](active-directory-authentication-scenarios.md#web-application-to-web-api) en [Escenarios de autenticación para Azure AD](active-directory-authentication-scenarios.md).

### Configuración de una aplicación cliente para tener acceso a las API web

Cuando una aplicación cliente está configurada para tener acceso a una API web expuesta por una aplicación de recursos (es decir: API de Azure AD Graph), el marco de consentimiento descrito anteriormente garantizará de que el cliente obtiene la concesión de los permisos necesarios en función en los permisos solicitados. De forma predeterminada, todas las aplicaciones pueden elegir permisos de Azure Active Directory (API Graph) y de API de administración de servicios de Azure, con el permiso "Habilitar inicio de sesión y leer perfiles de usuario" de Azure AD ya seleccionado de forma predeterminada. Si la aplicación cliente se registra en un inquilino de Azure AD de Office 365, las API web y los permisos de SharePoint y Exchange Online también estarán disponibles para su selección. Puede seleccionar entre dos tipos de permisos en los menús desplegables situados junto a la API web que desee:

- Permisos de la aplicación: la aplicación cliente necesita acceso a la API web directamente por sí misma (sin contexto de usuario). Este tipo de permiso requiere el consentimiento del administrador y no está disponible para aplicaciones cliente nativas.

- Permisos delegados: la aplicación cliente necesita acceso a la API web como el usuario que inició sesión, pero con acceso limitado por el permiso seleccionado. Este tipo de permiso lo puede conceder un usuario a menos que el permiso esté configurado como que requiere el consentimiento del administrador.

#### Para agregar acceso a las API Web

1. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com).

1. Haga clic en el icono de Active Directory en el menú de la izquierda y luego en el directorio que quiera.

1. En el menú superior, haga clic en Aplicaciones y luego en la aplicación que quiera configurar. Aparecerá la página Inicio rápido con el inicio de sesión único y otra información de configuración.

1. Expanda la sección Acceder a las API web en otras aplicaciones de la página Inicio rápido y haga clic en el vínculo Configurarla ahora de la sección Habilitar acceso. Aparecerá la página de propiedades de la aplicación.

1. Desplácese hacia abajo hasta la sección Permisos para otras aplicaciones. La primera columna permite seleccionar entre las aplicaciones de recursos disponibles en el directorio que exponen una API web. Una vez seleccionada, puede elegir los permisos de la aplicación y delegados que expone la API web.

1. Una vez seleccionados, haga clic en el botón Guardar de la barra de comandos.

>[AZURE.NOTE]Al hacer clic en el botón Guardar también se establecen automáticamente los permisos para la aplicación en el directorio en función de los permisos para otras aplicaciones que configuró. Puede ver estos permisos de la aplicación examinando la pestaña Propiedades de la aplicación.

### Configuración de una aplicación de recursos para exponer las API Web

Puede desarrollar una API web y ponerla a disposición de las aplicaciones cliente exponiendo los ámbitos de permiso. Una API web configurada correctamente se pone a disposición de otras aplicaciones del mismo modo que otras API web de Microsoft, incluidas la API Graph y las API de Office 365. Los ámbitos de permiso se exponen a través del manifiesto de aplicación, que es un archivo JSON que representa la configuración de la identidad de la aplicación. Puede exponer los ámbitos de permiso navegando a la aplicación en el Portal de Azure clásico y haciendo clic en el botón Manifiesto de aplicación de la barra de comandos.

#### Adición de ámbitos de permiso a la aplicación de recursos

1. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com).

1. Haga clic en el icono de Active Directory en el menú de la izquierda y luego en el directorio que quiera.

1. En el menú superior, haga clic en Aplicaciones y luego en la aplicación de recursos que quiera configurar. Aparecerá la página Inicio rápido con el inicio de sesión único y otra información de configuración.

1. Haga clic en el botón Administrar manifiesto de la barra de comandos y seleccione Descargar manifiesto.

1. Abra el archivo de manifiesto de aplicación JSON y reemplace el nodo "oauth2Permissions" por el siguiente fragmento de código JSON. Este fragmento de código es un ejemplo de cómo exponer un ámbito de permiso conocido como suplantación de usuario. Asegúrese de cambiar el texto y los valores para su propia aplicación:

		"oauth2Permissions": [
		{
			"adminConsentDescription": "Allow the application full access to the Todo List service on behalf of the signed-in 	user",
			"adminConsentDisplayName": "Have full access to the Todo List service",
			"id": "b69ee3c9-c40d-4f2a-ac80-961cd1534e40",
			"isEnabled": true,
			"type": "User",
			"userConsentDescription": "Allow the application full access to the todo service on your behalf",
			"userConsentDisplayName": "Have full access to the todo service",
			"value": "user_impersonation"
			}
		],

    El valor del identificador debe ser un nuevo GUID generado que cree mediante una herramienta de generación de GUID o mediante programación. Representa un identificador único para el permiso que se expone mediante la API web. Una vez que el cliente está configurado correctamente para solicitar el acceso a la API web y llama a la API web, este presentará un token JWT de OAuth 2.0 que tiene la notificación de ámbito (scp) establecida en el valor anterior que, en este caso, es user\_impersonation.

	>[AZURE.NOTE]Puede exponer ámbitos de permiso adicionales posteriormente según sea necesario. Tenga en cuenta que la API web podría exponer varios permisos asociados a diversas funciones. Ahora puede controlar el acceso a la API web mediante la notificación de ámbito (scp) del token JWT de OAuth 2.0 recibido.

1. Guarde el archivo JSON actualizado y cárguelo. Para ello, haga clic en el botón Administrar manifiesto de la barra de comandos, seleccione Cargar manifiesto, vaya al archivo de manifiesto actualizado y selecciónelo. Una vez cargado, la API web ya está configurada para que la usen otras aplicaciones del directorio.

#### Para comprobar que la API web se expone a otras aplicaciones en el directorio

1. En el menú superior, haga clic en Aplicaciones, seleccione la aplicación cliente cuyo acceso a la API web quiera configurar y haga clic en Configurar.

1. Desplácese hacia abajo hasta la sección Permisos para otras aplicaciones. Haga clic en el menú desplegable Seleccionar aplicación y podrá seleccionar la API web para la que acaba de exponer un permiso. En el menú desplegable Permisos delegados, seleccione el nuevo permiso.

![Se muestran los permisos de la lista de tareas](./media/active-directory-integrating-applications/listpermissions.png)

#### Más sobre el manifiesto de aplicación
El manifiesto de aplicación realmente actúa como un mecanismo para actualizar la entidad de aplicación, que define todos los atributos de configuración de identidad de una aplicación de Azure AD, incluidos los ámbitos de permiso de API que analizamos. Para obtener más información sobre la entidad de aplicación, consulte la [documentación sobre la entidad de aplicación de API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#EntityreferenceApplicationEntity). Ahí encontrará completa información de referencia sobre los miembros de la entidad de aplicación utilizados para especificar los permisos para la API:

- el miembro appRoles, que es una colección de entidades de [AppRole](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#AppRoleType) que puede utilizarse para definir los **permisos de aplicación** para una API web  
- el miembro oauth2Permissions, que es una colección de entidades de [OAuth2Permission](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#OAuth2PermissionType) que puede utilizarse para definir los **permisos delegados** para una API web

Para obtener más información sobre los conceptos del manifiesto de aplicación en general, consulte [Descripción del manifiesto de aplicación de Azure Active Directory](active-directory-application-manifest.md).

### Acceso a las API de Office 365 y Azure AD Graph

Como se mencionó anteriormente, además de exponer y tener acceso a API en sus propias aplicaciones de recursos, también puede actualizar la aplicación cliente para tener acceso a las API expuestas por los recursos de Microsoft. La API de Azure AD Graph, que se denomina "Azure Active Directory" en la lista de permisos para otras aplicaciones, está disponible de forma predeterminada para todas las aplicaciones que se registran con Azure AD. Si va a registrar la aplicación cliente en un inquilino de Azure AD que se ha aprovisionado por Office 365, puede tener acceso todos los permisos expuestos por las API a varios recursos de Office 365.

Para obtener más información sobre los ámbitos de permiso expuestos por:

- API de Azure AD Graph, consulte el artículo [Permission scopes | Graph API concepts (Ámbitos de permiso | Conceptos de API Graph)](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes).
- API de Office 365, consulte el artículo [Authentication and Authorization using Common Consent Framework (Autenticación y autorización mediante el marco de consentimiento común)](https://msdn.microsoft.com/office/office365/howto/application-manifest). Consulte [Set up your Office 365 development environment (Configurar el entorno de desarrollo de Office 365)](https://msdn.microsoft.com/office/office365/HowTo/setup-development-environment) para ver una descripción completa sobre cómo crear una aplicación cliente que se integre con la API de Office 365.

>[AZURE.NOTE]Debido a una limitación actual, las aplicaciones cliente nativas solo pueden llamar a la API de Azure AD Graph si usan el permiso "Acceso al directorio de la organización". Esta restricción no se aplica a las aplicaciones web.

### Configuración de aplicaciones multiempresa

Al agregar una aplicación a Azure AD, tal vez quiera que solo tengan acceso a la aplicación los usuarios de la organización. Como alternativa, tal vez quiera que los usuarios de organizaciones externas tengan acceso a la aplicación. Estos dos tipos de aplicaciones se denominan aplicaciones multiempresa y aplicaciones de un solo inquilino. Puede modificar la configuración de una aplicación de un solo inquilino para convertirla en una aplicación multiempresa, lo que se trata a continuación en esta sección.

Es importante tener en cuenta las diferencias entre una aplicación de un solo inquilino y otra multiinquilino:

- La finalidad de una aplicación de un solo inquilino es su uso en una sola organización. Suele tratarse de aplicaciones de línea de negocio (LOB) escritas por un desarrollador de la empresa. A una aplicación de un solo inquilino solo obtienen acceso usuarios de un solo directorio y, por tanto, solo es necesario aprovisionarla en un directorio. 
- La finalidad de una aplicación multiempresa es su uso en muchas organizaciones. Se trata de aplicaciones web de software como servicio (SaaS) que suelen estar escritas por un proveedor de software independiente (ISV). Las aplicaciones multiinquilino se deben aprovisionar en cada uno de los directorios en los que se usarán, lo que requiere el consentimiento del usuario o del administrador para registrarlas, que se transmite a través del marco del consentimiento de Azure AD. Tenga en cuenta que todas las aplicaciones cliente nativas son multiinquilino de forma predeterminada porque están instaladas en el dispositivo del propietario del recurso. Consulte la sección Información general sobre el marco de consentimiento presentada anteriormente para obtener más detalles en el marco de consentimiento.

#### Habilitación de los usuarios externos para otorgar acceso

Si está escribiendo una aplicación que quiere poner a disposición de sus clientes o asociados externos a la organización, tendrá que actualizar la definición de la aplicación en el Portal de Azure clásico.

>[AZURE.NOTE]Al habilitar el tipo multiinquilino, debe asegurarse de que el URI del identificador de la aplicación pertenece a un dominio comprobado. Además, la Dirección URL de retorno debe comenzar por https://. Para obtener más información, vea [Objetos de aplicación y objetos de entidad de servicio](active-directory-application-objects.md).

##### Para habilitar el acceso a la aplicación para los usuarios externos

1. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com).

1. Haga clic en el icono de Active Directory en el menú de la izquierda y luego en el directorio que quiera.

1. En el menú superior, haga clic en Aplicaciones y luego en la aplicación que quiera configurar. Aparecerá la página Inicio rápido con opciones de configuración.

1. Expanda la sección Configurar aplicación multiinquilino de la página Inicio rápido y haga clic en el vínculo Configurarla ahora de la sección Habilitar acceso. Aparecerá la página de propiedades de la aplicación.

1. Haga clic en el botón Sí situado junto a La aplicación es multiempresa y luego en el botón Guardar de la barra de comandos.

Una vez realizado el cambio anterior, los usuarios y administradores de otras organizaciones podrán conceder acceso de la aplicación a sus directorios y otros datos.

### Desencadenamiento del marco de consentimiento de Azure AD en tiempo de ejecución 

Para utilizar el marco de consentimiento, las aplicaciones cliente multiinquilino deben solicitar autorización mediante OAuth 2.0. Hay [ejemplos de código](https://azure.microsoft.com/documentation/samples/?service=active-directory&term=multi-tenant) disponibles que muestran cómo una aplicación web, una aplicación nativa o una aplicación de demonio o de servidor solicita códigos de autorización y tokens de acceso para llamar a las API web.

Es posible que la aplicación web ofrezca también una experiencia de suscripción para los usuarios. Si realmente ofrece una experiencia de suscripción, se espera que el usuario haga clic en un botón de suscripción que redirigirá el explorador al punto de conexión de autorización de OAuth2.0 para Azure AD o a un punto de conexión de información del usuario de OpenID Connect. Estos extremos permiten que la aplicación obtenga información sobre el nuevo usuario inspeccionando el id\_token. Después de la fase de inicio de sesión, se presentará al usuario una petición de consentimiento similar a la que se mostró anteriormente en la sección Información general sobre el marco de consentimiento.

Como alternativa, también es posible que la aplicación web ofrezca una experiencia que permite a los administradores "suscribir mi compañía". Esta experiencia también redirigiría al usuario al extremo de autorización de OAuth 2.0 para Azure AD. Sin embargo, en este caso se pasa un parámetro prompt=admin\_consent al punto de conexión de la autorización para forzar la experiencia de consentimiento del administrador, donde el administrador concederá consentimiento en nombre de su organización. Solo un usuario que se autentique con una cuenta que pertenezca al rol Administrador global puede proporcionar el consentimiento; otros usuarios recibirán un error. Tras el consentimiento correcto, la respuesta contendrá admin\_consent=true. Al canjear un token de acceso, también recibirá un id\_token que proporcionará información sobre la organización y el administrador que se suscribió en la aplicación.

#### Habilitación de la concesión implícita de OAuth 2.0 para aplicaciones de una sola página

Las aplicaciones de una sola página (SPA) normalmente tienen una estructura con un front-end que hace gran uso de JavaScript y que se ejecuta en el explorador, el cual llama al back-end de la API web de la aplicación para llevar a cabo su lógica empresarial. Para las SPA hospedadas en Azure AD, se usa la concesión implícita de OAuth 2.0 para autenticar al usuario en Azure AD y obtener un token que puede usar para proteger las llamadas desde el cliente JavaScript de la aplicación hasta su API web de back-end. Después de que el usuario haya dado su consentimiento, este mismo protocolo de autenticación se puede usar para obtener tokens para proteger las llamadas entre el cliente y otros recursos de API web configurados para la aplicación. De forma predeterminada, la concesión implícita de OAuth 2.0 está deshabilitada para las aplicaciones. Puede habilitarla para su aplicación si configura el valor `oauth2AllowImplicitFlow`" en su [manifiesto de aplicación](active-directory-application-manifest.md), que es un archivo JSON que representa la configuración de identidad de la aplicación.

##### Para habilitar la concesión implícita de OAuth 2.0

1. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com).
1. Haga clic en el icono de **Active Directory** en el menú de la izquierda y, luego, en el directorio que quiera.
1. En el menú superior, haga clic en **Aplicaciones** y luego en la aplicación que quiera configurar. Aparecerá la página Inicio rápido con el inicio de sesión único y otra información de configuración.
1. Haga clic en el botón **Administrar manifiesto** de la barra de comandos y seleccione **Descargar manifiesto**. Abra el archivo de manifiesto de la aplicación JSON y establezca el valor "oauth2AllowImplicitFlow" en "true". De forma predeterminada, es "false".

       "oauth2AllowImplicitFlow": true,

1. Guarde el archivo JSON actualizado y cárguelo. Para ello, haga clic en el botón **Administrar manifiesto** en la barra de comandos, seleccione **Cargar manifiesto**, vaya al archivo de manifiesto actualizado y selecciónelo. Una vez cargado, la API web está ahora configurada para usar la concesión implícita de OAuth 2.0 para autenticar a los usuarios.


### Experiencias heredadas para conceder acceso

En esta sección se describe la experiencia de consentimiento heredada antes del 12 de marzo de 2014. Esta experiencia se sigue admitiendo y se describe a continuación. Antes de la nueva funcionalidad, solo se podían conceder los siguientes permisos:

- Iniciar sesión de usuarios en su organización

- Iniciar sesión de usuarios y leer los datos del directorio de su organización (solo como la aplicación)

- Iniciar sesión de usuarios y leer y escribir los datos del directorio de su organización (solo como la aplicación)

Puede seguir los pasos indicados en [Desarrollo de aplicaciones web multiempresa con Azure AD](https://msdn.microsoft.com/library/azure/dn151789.aspx) para conceder acceso a las nuevas aplicaciones registradas en Azure AD. Es importante tener en cuenta que el nuevo marco de consentimiento permite que las aplicaciones sean mucho más eficaces y, además, habilita a los usuarios y no solo a los administradores para que den su consentimiento a estas aplicaciones.

#### Creación del vínculo que concede acceso a usuarios externos (heredado)

Para que los usuarios externos se suscriban en su aplicación con sus cuentas profesionales, tendrá actualizar la aplicación para que muestre un botón que se vincula a la página de Azure AD que les habilita para conceder acceso. Las instrucciones de personalización de marca para este botón de suscripción se describen en el tema [Directrices de personalización de marca para aplicaciones integradas](active-directory-branding-guidelines.md). Después de que el usuario conceda o deniegue el acceso, la página Conceder acceso de Azure AD redirigirá el explorador a su aplicación con una respuesta. Para obtener más información sobre las propiedades de la aplicación, vea [Objetos de aplicación y objetos de entidad de servicio](active-directory-application-objects.md).

La página de concesión de acceso la crea Azure AD, y se puede encontrar un vínculo a ella en la página Configuración de la aplicación del Portal de Azure clásico. Para ir a la página Configuración, haga clic en el vínculo Aplicaciones del menú superior de su inquilino de Azure AD, haga clic en la aplicación que quiere configurar y luego en Configurar en el menú superior de la página Inicio rápido.

El vínculo de la aplicación tendrá este aspecto: `http://account.activedirectory.windowsazure.com/Consent.aspx?ClientID=058eb9b2-4f49-4850-9b78-469e3176e247&RequestedPermissions=DirectoryReaders&ConsentReturnURL=https%3A%2F%2Fadatum.com%2FExpenseReport.aspx%3FContextId%3D123456`. En la siguiente tabla se describen los elementos del vínculo:

|Parámetro|Descripción|
|---|---|
|ClientId|Obligatorio. Identificador del cliente que obtuvo como parte de la adición de la aplicación.|
|RequestedPermissions|Opcional. Nivel de acceso que solicita la aplicación, que se mostrará al usuario que concede el acceso de la aplicación. Si no se especifica, el nivel de acceso solicitado será de forma predeterminada solo inicio de sesión único. Las otras opciones son DirectoryReaders y DirectoryWriters. Para obtener más información sobre estos niveles de acceso, vea Niveles de acceso de la aplicación.|
|ConsentReturnUrl|Opcional. Dirección URL a la que quiere que se devuelva la respuesta de concesión de acceso. Este valor debe estar en codificación URL y encontrarse en el mismo dominio que la dirección URL de respuesta que se configuró en la definición de la aplicación. Si no se proporciona, la respuesta de concesión de acceso se redirigirá a la URL de respuesta configurada|

La especificación de un parámetro ConsentReturnUrl independiente de la URL de respuesta permitirá que la aplicación implemente una lógica independiente que pueda procesar la respuesta en una dirección URL distinta de la dirección URL de respuesta (que suele procesar tokens SAML de inicio de sesión). También puede especificar parámetros adicionales en la dirección URL codificada de ConsentReturnURL; estos se pasarán como parámetros de cadena de consulta a la aplicación tras la redirección. Este mecanismo sirve para mantener información adicional o para asociar la solicitud de la aplicación de una concesión de acceso a la respuesta de Azure AD.

#### Concesión de acceso a la experiencia del usuario y respuesta (heredado)

Cuando una aplicación se redirige al vínculo de acceso de concesión, las siguientes imágenes muestran lo que experimentará el usuario.

Si el usuario no inició sesión todavía, se le pedirá que lo haga:

![Inicio de sesión en AAD](./media/active-directory-integrating-applications/signintoaad.png)

Una vez autenticado el usuario, Azure AD redirigirá al usuario a la página Conceder acceso:

![Conceder acceso](./media/active-directory-integrating-applications/grantaccess.png)

>[AZURE.NOTE]Solo el administrador de la compañía de la organización externa puede conceder acceso a la aplicación. Si el usuario no es un administrador de la compañía, se le ofrecerá la posibilidad de enviar correo a su administrador de la compañía para solicitar que se conceda acceso a esta aplicación.

Después de que el cliente conceda acceso a una aplicación haciendo clic en Conceder acceso, o lo deniegue haciendo clic en Cancelar, Azure AD enviará una respuesta a la ConsentReturnUrl o a la dirección URL de respuesta configurada. Esta respuesta contiene los parámetros siguientes:

|Parámetro|Descripción|
|---|---|
|TenantId|Identificador único de la organización en Azure AD que concedió acceso a la aplicación. Este parámetro solo se especificará si el cliente concedió acceso.|
|Consentimiento|Este valor se establecerá en Concedido si se concedió acceso a la aplicación o en Denegado si se rechazó la solicitud.|

Se devolverán parámetros adicionales a la aplicación si se especificaron como parte de la dirección URL codificada de ConsentReturnUrl. El siguiente es un ejemplo de respuesta a una solicitud de concesión de acceso que indica que la aplicación se autorizó e incluye un ContextID que se suministró en la solicitud de concesión de acceso: `https://adatum.com/ExpenseReport.aspx?ContextID=123456&Consent=Granted&TenantId=f03dcba3-d693-47ad-9983-011650e64134`.

>[AZURE.NOTE]La respuesta de concesión de acceso no contendrá un token de seguridad para el usuario; la aplicación debe iniciar sesión del usuario por separado.

El siguiente es un ejemplo de respuesta a una solicitud de concesión de acceso que se denegó: `https://adatum.com/ExpenseReport.aspx?ContextID=123456&Consent=Denied`

#### Implementación de claves de aplicación para el acceso sin interrupciones a la API Graph (heredado)

A lo largo de la duración de la aplicación, puede que necesite cambiar las claves que usa cuando llama a Azure AD para adquirir un token de acceso para llamar a la API Graph. El cambio de claves suele dividirse en dos categorías: sustitución de emergencia cuando la clave se encuentra en peligro o sustitución cuando la clave actual está a punto de expirar. Debe seguirse el siguiente procedimiento para proporcionar acceso ininterrumpido a la aplicación mientras se actualizan las claves (sobre todo en el segundo caso).

1. En el Portal de Azure clásico, haga clic en el inquilino de su directorio, haga clic en Aplicaciones en el menú superior y luego en la aplicación que quiera configurar. Aparecerá la página Inicio rápido con el inicio de sesión único y otra información de configuración.

1. Haga clic en Configurar en el menú superior para ver una lista de propiedades de la aplicación y verá una lista de las claves.

1. En Claves, haga clic en el menú desplegable Seleccionar duración y seleccione 1 o 2 años, y haga clic en Guardar de la barra de comandos. De este modo, genera una nueva clave de contraseña para la aplicación. Copie esta nueva clave de contraseña. En este momento la aplicación puede usar tanto la clave nueva como la existente para obtener un token de acceso de Azure AD.

1. Vuelva a la aplicación y actualice la configuración para que empiece a usar la nueva clave de contraseña. Vea [Uso de la API Graph para consultar Azure AD](https://msdn.microsoft.com/library/azure/dn151791.aspx) si desea obtener un ejemplo de dónde debe suceder esta actualización.

1. Ahora debe implementar este cambio transversalmente en el entorno de producción (compruébelo primero en un nodo del servicio antes de implementarlo en el resto).

1. Cuando la actualización finalice en toda la implementación de producción, puede volver al Portal de Azure clásico y eliminar la clave anterior.

#### Cambio de las propiedades de la aplicación después de habilitar el acceso (heredado)

Después de habilitar el acceso de los usuarios externos a la aplicación, puede seguir efectuando cambios en las propiedades de la aplicación en el Portal de Azure clásico. Sin embargo, los clientes a lo que ya se concedió acceso a la aplicación antes de realizar cambios en ella no verán los cambios reflejados al consultar los detalles sobre la aplicación en el Portal de Azure clásico. Cuando la aplicación esté a disposición de los clientes, tendrá que tener mucho cuidado al realizar determinados cambios. Por ejemplo, si actualiza el URI de id. de aplicación, los clientes existentes a los que se concedió acceso antes de este cambio no podrán iniciar sesión en la aplicación con su cuenta educativa o de la compañía.

Si realiza un cambio al parámetro RequestedPermissions para solicitar un mayor nivel de acceso, los clientes existentes que usen funcionalidad de la aplicación que ahora aprovecha las nuevas llamadas a API con este aumento nivel de acceso pueden obtener una respuesta de acceso denegado de la API Graph. La aplicación debe controlar estos casos y pedir al cliente al cliente que conceda acceso a la aplicación con la solicitud de un nivel de acceso mayor.

## Eliminación de una aplicación

En esta sección se describe cómo quitar una aplicación del inquilino de Azure AD.

### Eliminación de una aplicación creada por su organización
Estas son las aplicaciones que se muestran con el filtro "Aplicaciones que tiene mi compañía" en la página principal de "Aplicaciones" para el inquilino de Azure AD. En términos técnicos, se trata de aplicaciones que se han registrado manualmente mediante el Portal de Azure clásico o mediante programación a través de PowerShell o la API Graph. Más específicamente, se representan mediante un objeto Application y ServicePrincipal en el inquilino. Para obtener más información, vea [Objetos Application y objetos ServicePrincipal](active-directory-application-objects.md).

#### Para quitar una aplicación de un solo inquilino del directorio

1. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com).

1. Haga clic en el icono de Active Directory en el menú de la izquierda y luego en el directorio que quiera.

1. En el menú superior, haga clic en Aplicaciones y luego en la aplicación que quiera configurar. Aparecerá la página Inicio rápido con el inicio de sesión único y otra información de configuración.

1. Haga clic en el botón Eliminar de la barra de comandos.

1. Haga clic en Sí en el mensaje de confirmación.

#### Para quitar una aplicación multiempresa de su directorio

1. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com).

1. Haga clic en el icono de Active Directory en el menú de la izquierda y luego en el directorio que quiera.

1. En el menú superior, haga clic en Aplicaciones y luego en la aplicación que quiera configurar. Aparecerá la página Inicio rápido con el inicio de sesión único y otra información de configuración.

1. En la sección La aplicación es multiempresa, haga clic en No. Esto hace que la aplicación sea de un solo inquilino, pero la aplicación seguirá estando en la organización que ya le dio su consentimiento.

1. Haga clic en el botón Eliminar de la barra de comandos.

1. Haga clic en Sí en el mensaje de confirmación.

### Eliminación de una aplicación multiinquilino autorizada por otra organización
Este es un subconjunto de las aplicaciones que se muestran con el filtro "Aplicaciones que usa mi compañía" en la página principal de "Aplicaciones" para el inquilino de Azure AD, en concreto los que no aparecen en la lista "Aplicaciones que tiene mi compañía". En términos técnicos, son aplicaciones multiinquilino registradas durante el proceso de consentimiento. Más específicamente, se representan solo mediante un objeto ServicePrincipal en su inquilino. Para obtener más información, vea [Objetos Application y objetos ServicePrincipal](active-directory-application-objects.md).

A fin de quitar el acceso de una aplicación multiinquilino a su directorio (después de concederle consentimiento), el administrador de la compañía debe tener una suscripción de Azure para quitar el acceso a través del Portal de Azure clásico. Simplemente vaya a la página de configuración de la aplicación y haga clic en el botón "Administrar el acceso" en la parte inferior. El administrador de la empresa también puede usar los [Cmdlets de Azure AD PowerShell](http://go.microsoft.com/fwlink/?LinkId=294151) para quitar el acceso.

## Pasos siguientes

- Vea el tema [Directrices de personalización de marca para aplicaciones integradas](active-directory-branding-guidelines.md)

- Más información sobre [Objetos de aplicación y objetos de entidad de servicio](active-directory-application-objects.md)

- Descripción del [manifiesto de aplicación de Azure Active Directory](active-directory-application-manifest.md)

- Visite la [Guía del desarrollador de Azure Active Directory](active-directory-developers-guide.md).

<!---HONumber=AcomDC_0114_2016-->