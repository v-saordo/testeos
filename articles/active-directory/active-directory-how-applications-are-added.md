<properties
   pageTitle="Cómo se agregan las aplicaciones a Azure Active Directory."
   description="Este artículo describe cómo se agregan las aplicaciones a una instancia de Azure Active Directory."
   services="active-directory"
   documentationCenter=""
   authors="shoatman"
   manager="kbrint"
   editor=""/>

   <tags
      ms.service="active-directory"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="identity"
      ms.date="02/09/2016"
      ms.author="shoatman"/>

# Cómo y por qué se agregan aplicaciones a Azure AD

Una de las cosas desconcierta inicialmente al ver una lista de aplicaciones en la instancia de Azure Active Directory es comprender la procedencia de las aplicaciones y por qué existen. Este artículo ofrecerá una introducción de alto nivel de cómo se representan las aplicaciones en el directorio y le proporcionan un contexto que le ayudará a comprender cómo una aplicación llega a estar en el directorio.

## ¿Qué servicios proporciona Azure AD para las aplicaciones?

Las aplicaciones se agregan a Azure AD para aprovechar uno o varios de los servicios que proporciona. Los servicios incluyen:

* Autenticación y autorización de aplicaciones
* Autenticación y autorización de usuarios
* Inicio de sesión único (SSO) mediante federación o contraseña
* Aprovisionamiento y sincronización de usuarios
* Control de acceso basado en roles. Utilice el directorio para definir los roles de aplicación para realizar comprobaciones de autorización basadas en roles en una aplicación.
* Servicios de autorización oAuth (que usa Office 365 y otras aplicaciones Microsoft para autorizar el acceso a los recursos/las API).
* Publicación de aplicaciones y proxy. Publique una aplicación desde una red privada en Internet.

## ¿Cómo se representan las aplicaciones en el directorio?

Las aplicaciones se representan en Azure AD con dos objetos: un objeto de aplicación y un objeto de entidad de seguridad de servicio. Hay un objeto de aplicación registrado en un directorio de hogar/propietario o publicación y uno o más objetos de entidad de seguridad de servicio que representa la aplicación en todos los directorios en los que actúa.

El objeto de aplicación describe la aplicación en Azure AD (el servicio multiinquilino) y puede incluir cualquiera de las acciones siguientes: (*Nota*: esta no es una lista exhaustiva).

* Nombre, logotipo y publicador
* Secretos (claves simétricas o asimétricas utilizadas para autenticar la aplicación)
* Dependencias de API (oAuth)
* API/recursos/ámbitos publicados (oAuth)
* Roles de aplicación (RBAC)
* Configuración y metadatos SSO (SSO)
* Configuración y metadatos de aprovisionamiento de usuarios
* Configuración y metadatos proxy

La entidad de seguridad de servicio es un registro de la aplicación en todos los directorios, donde la aplicación actúa incluido su directorio particular. La entidad de seguridad de servicio:

* Hace referencia a un objeto de aplicación a través de la propiedad de Id. de aplicación
* Registra las asignaciones de rol de aplicación de grupo y de usuario local
* Registra los permisos de administrador y usuarios locales concedidos a la aplicación
    * Por ejemplo: permiso de la aplicación para tener acceso a un correo electrónico de usuarios determinado
* Registra directivas locales, incluida la directiva de acceso condicional
* Registra la configuración local alternativa para una aplicación
    * Reclama las reglas de transformación
    * Asignaciones de atributos (aprovisionamiento de usuarios)
    * Roles de aplicación específicos del inquilino (si la aplicación admite roles personalizados)
    * Nombre/logotipo

### Un diagrama de objetos de aplicación y entidades de seguridad de servicio en directorios

![Un diagrama muestra cómo los objetos de aplicación y entidad de seguridad de servicio existente con las instancias de Azure AD.][apps_service_principals_directory]

Como puede ver en el diagrama anterior. Microsoft mantiene dos directorios internamente (a la izquierda) que usa para publicar aplicaciones.

* Uno para Microsoft Apps (directorio de servicios de Microsoft)
* Uno para aplicaciones preintegradas de terceros (directorio de Galería de aplicaciones)

Se necesitan publicadores/proveedores de aplicaciones que se integren con Azure AD para disponer de un directorio de publicación. (Algunos directorios SAAS).

Las aplicaciones que agrega usted mismo incluyen:

* Aplicaciones que ha desarrollado (integradas con AAD)
* Aplicaciones conectadas para el inicio de sesión único
* Aplicaciones que se publicaron mediante el proxy de aplicación de Azure AD.

### Un par de notas y excepciones

* No todas las entidades de seguridad de servicio señalan a objetos de la aplicación. ¿Verdad? Cuando se generaron originalmente los servicios de Azure AD que se proporcionan a las aplicaciones eran mucho más limitados y la entidad de seguridad de servicio era suficiente para establecer una identidad de aplicación. La entidad de seguridad de servicio original era más cercana en cuanto a la forma a la cuenta de servicio de Windows Server Active Directory. Por este motivo, es posible crear a entidades de seguridad de servicio con Azure AD PowerShell sin crear primero un objeto de aplicación. Graph API requiere un objeto de la aplicación antes de crear un servicio de seguridad de servicio.
* Actualmente, no toda la información que se ha descrito anteriormente se expone mediante programación. Lo siguiente solo está disponible en la interfaz de usuario:
    * Reglas de transformación de notificaciones
    * Asignaciones de atributos (aprovisionamiento de usuarios)
* Para obtener más información sobre los objetos de aplicación y de la entidad de seguridad de servicio, consulte la documentación de referencia de API de REST de Azure AD Graph. *Sugerencia*: la documentación de Graph API de Azure AD es lo más parecido a una referencia de esquema de Azure AD disponible actualmente.  
    * [Aplicación](https://msdn.microsoft.com/library/azure/dn151677.aspx)
    * [Entidad de seguridad de servicio](https://msdn.microsoft.com/library/azure/dn194452.aspx)


## ¿Cómo se agregan aplicaciones a mi instancia de Azure AD?
Hay muchas maneras de que una aplicación puede agregarse a Azure AD:

* Adición de una aplicación desde la [Galería de aplicaciones de Azure Active Directory](https://azure.microsoft.com/updates/azure-active-directory-over-1000-apps/)
* Registro/inicio de sesión en una aplicación de terceros integrada con Azure Active Directory (por ejemplo: [Smartsheet](https://app.smartsheet.com/b/home) o [DocuSign](https://www.docusign.net/member/MemberLogin.aspx))
    * Durante el registro o inicio de sesión, se les solicitará a los usuarios que proporcionen permiso a la aplicación para obtener acceso a su perfil y otros permisos. La primera persona que dé su consentimiento hará que una entidad de seguridad de servicio que represente la aplicación se agregue al directorio.
* Registro/inicio de sesión en servicios en línea de Microsoft, como [Office 365](http://products.office.com/)
    * Al suscribirse a Office 365 o probar una versión de prueba, se crean una o más entidades de seguridad de servicio en el directorio que representa los distintos servicios que se usan para ofrecer toda la funcionalidad asociada a Office 365.
    * Algunos servicios de Office 365 como SharePoint crean entidades de seguridad de servicio de forma continua para permitir una comunicación segura entre los componentes que incluyen los flujos de trabajo.
* Para agregar una aplicación que esté desarrollando en el Portal de administración de Azure, consulte: https://msdn.microsoft.com/library/azure/dn132599.aspx
* Para agregar una aplicación que se va a desarrollar con Visual Studio, consulte:
    * [Métodos de autenticación de ASP.Net](http://www.asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio#orgauthoptions)
    * [Servicios conectados](http://blogs.msdn.com/b/visualstudio/archive/2014/11/19/connecting-to-cloud-services.aspx)
* Adición de una aplicación para usar el [proxy de aplicación de Azure AD](https://msdn.microsoft.com/library/azure/dn768219.aspx)
* Conexión de una aplicación para inicio de sesión único utilizando SAML o SSO de contraseña
* Muchas más, incluidas varias experiencias de desarrollador en Azure y experiencias del explorador de API en centros de desarrollador.

## ¿Quién tiene permiso para agregar aplicaciones a la instancia de Azure AD?

Solo los administradores globales pueden:

* Agregar aplicaciones desde la Galería de la aplicación de Azure AD (aplicaciones de terceros integradas previamente)
* Publicar una aplicación con el proxy de aplicación de Azure AD.

Todos los usuarios en el directorio tienen derechos de agregar las aplicaciones que desarrollan y pueden decidir qué aplicaciones comparten/proporcionan acceso a sus datos de la organización. *Recuerde que el registro/inicio de sesión del usuario en una aplicación y la garantía de permisos puede provocar que se cree una entidad de seguridad de servicio.*

Esto puede parecer al principio complicado, pero tenga en cuenta lo siguiente:

* Las aplicaciones han podido aprovechar Windows Server Active Directory para la autenticación de usuario durante muchos años sin necesidad de que la aplicación ese registre en el directorio. Ahora la organización contará con una visibilidad mejorada para conocer exactamente cuántas aplicaciones está usando el directorio y para qué.
* No es necesario para el proceso de registro/publicación de la aplicación controlada por el administrador. Con Servicios de federación de Active Directory (ADFS) es probable que un administrador tenga que agregar una aplicación como usuario de confianza en nombre de los desarrolladores. Ahora los desarrolladores pueden hacer uso del autoservicio.
* El registro/inicio de sesión de usuarios con cuentas de organización para fines empresariales es una buena opción. Si posteriormente dejan la organización, perderán el acceso a su cuenta en la aplicación que utilizaban.
* Disponer de un registro sobre qué datos se compartieron con qué aplicación es algo positivo. Los datos son más transportables que nunca y tener un registro claro de quién compartió que datos con qué aplicaciones resulta útil.
* Las aplicaciones que usan Azure AD para oAuth deciden exactamente qué permisos que pueden conceder los usuarios a las aplicaciones y qué permisos requieren la aceptación de un administrador. Cabe destacar que solo los administradores pueden dar su consentimiento a ámbitos más amplios y permisos más significativos.
* Los usuarios que agregan aplicaciones y permiten el acceso a sus datos son eventos auditados, por lo que puede ver los informes de auditoría en el Portal de administración de Azure para determinar cómo se agregó una aplicación al directorio.

**Nota:** *Microsoft ha estado funcionando con la configuración predeterminada durante muchos meses.*

Dicho esto, es posible evitar que los usuarios del directorio agreguen aplicaciones y puedan optar por qué información compartir con las aplicaciones modificando la configuración de Directory en el Portal de administración de Azure. Es posible obtener acceso a la siguiente configuración en el Portal de administración de Azure en la pestaña "Configurar" de Directory.

![Una captura de pantalla de la interfaz de usuario para configurar los ajustes de la aplicación integrada][app_settings]


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Pasos siguientes

Obtenga más información sobre cómo agregar aplicaciones a Azure AD y cómo configurar servicios para aplicaciones.

* Desarrolladores: [Integración de una aplicación con AAD](https://msdn.microsoft.com/library/azure/dn151122.aspx)
* Desarrolladores: [Revisión de código de ejemplo para aplicaciones integradas con Azure Active Directory en Github](https://github.com/AzureADSamples)
* Desarrolladores y profesionales de TI: [Revisión de la documentación de API de REST API para Azure Active Directory Graph API](https://msdn.microsoft.com/library/azure/hh974478.aspx)
* Profesionales de TI: [Uso de aplicaciones integradas previamente de Azure Active Directory desde la Galería de aplicaciones](https://msdn.microsoft.com/library/azure/dn308590.aspx)
* Profesionales de TI: [Búsqueda de tutoriales para la configuración de aplicaciones específicas integradas previamente](https://msdn.microsoft.com/library/azure/dn893637.aspx)
* Profesionales de TI: [Publicación de una aplicación con el proxy de aplicación de Azure Active Directory](https://msdn.microsoft.com/library/azure/dn768219.aspx)

## Consulte también

- [Índice de artículos sobre la administración de aplicaciones en Azure Active Directory](active-directory-apps-index.md)

<!--Image references-->
[apps_service_principals_directory]: media/active-directory-how-applications-are-added/HowAppsAreAddedToAAD.jpg
[app_settings]: media/active-directory-how-applications-are-added/IntegratedAppSettings.jpg

<!---HONumber=AcomDC_0211_2016-->