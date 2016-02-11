<properties
   pageTitle="Descripción del manifiesto de aplicación de Azure Active Directory | Microsoft Azure"
   description="Cobertura detallada de cómo usar el manifiesto de aplicación de Azure Active Directory, que representa la configuración de identidad de una aplicación en un inquilino de Azure AD, y se usa para facilitar la autorización de OAuth, la experiencia de consentimiento y mucho más."
   services="active-directory"
   documentationCenter=""
   authors="bryanla"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="12/18/2015"
   ms.author="dkershaw;bryanla"/>

# Descripción del manifiesto de aplicación de Azure Active Directory

Las aplicaciones que se integran con Azure Active Directory (AD) deben estar registradas con un inquilino de Azure AD y proporcionar una configuración persistente de identidad para la aplicación. Esta configuración se consulta en tiempo de ejecución, lo que habilita los escenarios que permiten una aplicación para externalizar y negociar la autenticación/autorización mediante Azure AD. Para obtener más información sobre el modelo de aplicación de Azure AD, vea el artículo [Adición, actualización y eliminación de una aplicación][ADD-UPD-RMV-APP].

## Actualización de la configuración de identidad de una aplicación

Hay en realidad varias opciones disponibles para actualizar las propiedades de la configuración de identidad de una aplicación, que varían en función de las capacidades y los niveles de dificultad, incluidas las siguientes:

- La **interfaz de usuario web del [Portal de Azure clásico][AZURE-CLASSIC-PORTAL]** le permite actualizar las propiedades más comunes de una aplicación. Esta es la forma más rápida y menos propensa a errores de actualizar las propiedades de la aplicación pero no le otorga acceso completo a todas las propiedades, como sí lo hacen los dos métodos siguientes.
- Para escenarios más avanzados donde necesita actualizar las propiedades que no se exponen en el Portal de Azure clásico, puede modificar el **manifiesto de aplicación**. Este es el punto importante de este artículo y se explica con más detalle a partir de la siguiente sección.
- También es posible **escribir una aplicación que use la [API Graph][GRAPH-API]** para actualizarla, lo que requiere un mayor esfuerzo. Esto puede ser una opción atractiva, no obstante, si está escribiendo software de administración o si necesita actualizar las propiedades de la aplicación periódicamente de forma automática.

## Uso del manifiesto de aplicación para actualizar la configuración de identidad de una aplicación
A través del [Portal de Azure clásico][AZURE-CLASSIC-PORTAL], puede administrar la configuración de identidad de la aplicación, mediante la descarga y carga de la representación de un archivo JSON, que se denomina un manifiesto de aplicación. No se almacena ningún archivo real en el directorio: el manifiesto de aplicación es simplemente una operación GET de HTTP en la entidad de aplicación de API Graph de Azure AD y la carga es una operación PATCH de HTTP en la entidad de aplicación.

Por lo tanto, para comprender el formato y las propiedades del manifiesto de aplicación, deberá hacer referencia a la documentación de la [entidad de aplicación][APPLICATION-ENTITY] de API Graph. Ejemplos de actualizaciones que se pueden realizar mediante la carga del manifiesto de aplicación:

- Declarar ámbitos de permisos (oauth2Permissions) expuestos por la API web. Consulte el tema "Exposición de las API web a otras aplicaciones" en [Integración de aplicaciones con Azure Active Directory][INTEGRATING-APPLICATIONS-AAD] para obtener información sobre cómo implementar la suplantación de usuario mediante el ámbito de permisos delegados de oauth2Permissions. Tal como se mencionó anteriormente, todas las propiedades de la entidad de aplicación están documentadas en el artículo de referencia [Entity and Complex Type reference][APPLICATION-ENTITY] de API Graph, incluido el miembro oauth2Permissions que es una colección de tipo [OAuth2Permission][APPLICATION-ENTITY-OAUTH2-PERMISSION].
- Declarar roles de aplicación (appRoles) expuestos por la aplicación. La propiedad appRole de la entidad de aplicación es una colección de tipo [AppRole][APPLICATION-ENTITY-APP-ROLE]. Vea el artículo [Control de acceso basado en roles en aplicaciones en la nube con Azure AD][RBAC-CLOUD-APPS-AZUREAD] para un ejemplo de implementación.
- Declarar las aplicaciones cliente conocidas (knownClientApplications), que permiten enlazar de forma lógica el consentimiento de las aplicaciones cliente especificadas a la API de recursos o web.
- Solicitar a Azure AD que emita una notificación de pertenencia a grupos para el usuario que inició sesión (groupMembershipClaims). NOTA: esto puede configurarse para emitir además notificaciones sobre pertenencias a roles de directorio del usuario. Vea el artículo [Autorización en aplicaciones en la nube con grupos de AD][AAD-GROUPS-FOR-AUTHORIZATION] para obtener un ejemplo de implementación.
- Permitir que la aplicación admita flujos de concesión implícita de OAuth 2.0 (oauth2AllowImplicitFlow). Este tipo de flujo de concesión se utiliza con páginas web JavaScript integradas o aplicaciones de página única (SPA).
- Habilitar el uso de certificados X 509 como la clave secreta (keyCredentials). Vea los artículos [Compilar aplicaciones de servicio y demonio en Office 365][O365-SERVICE-DAEMON-APPS] y [Guía del desarrollador para la autenticación con la API del Administrador de recursos de Azure ][DEV-GUIDE-TO-AUTH-WITH-ARM] para ejemplos de implementación.

El manifiesto de aplicación también proporciona una forma adecuada de realizar un seguimiento del estado del registro de la aplicación. Como está disponible en formato JSON, la representación del archivo se puede proteger en el control de código fuente, junto con el código fuente de la aplicación.

## Ejemplo paso a paso
Ahora vamos a recorrer los pasos necesarios para actualizar la configuración de identidad de la aplicación mediante el manifiesto de aplicación: Nos centraremos en uno de los ejemplos dados anteriormente, que muestra cómo declarar un nuevo ámbito de permiso en una aplicación de recursos:

1. Navegue hasta el [Portal de Azure clásico][AZURE-CLASSIC-PORTAL] e inicie sesión con una cuenta que tenga privilegios de administrador o coadministrador de servicios.

2. Una vez autenticado, desplácese hacia abajo y seleccione la extensión "Active Directory" de Azure en el panel de navegación de la izquierda (1) y haga clic en el inquilino de Azure AD en el que está registrada la aplicación (2).

    ![Selección del inquilino de Azure AD][SELECT-AZURE-AD-TENANT]

3. Cuando se abre la página de directorio, haga clic en "Aplicaciones" (1) en la parte superior de la página para ver una lista de aplicaciones registradas en el inquilino. A continuación, busque la aplicación que desea actualizar en la lista y haga clic en ella (2).

    ![Selección del inquilino de Azure AD][SELECT-AZURE-AD-APP]

4. Ahora que ha seleccionado la página principal de la aplicación, tenga en cuenta la característica "Administrar manifiesto" en la parte inferior de la página (1). Si hace clic en este vínculo, se le pedirá que descargue o cargue el archivo de manifiesto de JSON. Haga clic en "Descargar manifiesto" (2), tras lo que aparecerá el cuadro de diálogo de confirmación de descarga que le solicitará confirmación. Para ello, haga clic en "Descargar manifiesto" (3) y abra o guarde el archivo localmente (4).

    ![Administración del manifiesto, opción de descarga][MANAGE-MANIFEST-DOWNLOAD]

    ![Descarga del manifiesto][DOWNLOAD-MANIFEST]

5. En este ejemplo, se guarda el archivo localmente, lo que permite abrirlo en un editor, realizar cambios en JSON y volver a guardarlo. Este es el aspecto de la estructura de JSON dentro del editor de JSON de Visual Studio. Tenga en cuenta que sigue el esquema de la [entidad de aplicación][APPLICATION-ENTITY] tal como se mencionó anteriormente:

    ![Actualización del manifiesto de JSON][UPDATE-MANIFEST]

    Por ejemplo, suponiendo que desea implementar o exponer un permiso nuevo llamado "Employees.Read.All" en nuestra aplicación (API) de recursos, simplemente agregaría un elemento nuevo o adicional a la colección oauth2Permissions, es decir:

        "oauth2Permissions": [
        {
        "adminConsentDescription": "Allow the application to access MyWebApplication on behalf of the signed-in user.",
        "adminConsentDisplayName": "Access MyWebApplication",
        "id": "aade5b35-ea3e-481c-b38d-cba4c78682a0",
        "isEnabled": true,
        "type": "User",
        "userConsentDescription": "Allow the application to access MyWebApplication on your behalf.",
        "userConsentDisplayName": "Access MyWebApplication",
        "value": "user_impersonation"
        },
        {
        "adminConsentDescription": "Allow the application to have read-only access to all Employee data.",
        "adminConsentDisplayName": "Read-only access to Employee records",
        "id": "2b351394-d7a7-4a84-841e-08a6a17e4cb8",
        "isEnabled": true,
        "type": "User",
        "userConsentDescription": "Allow the application to have read-only access to your Employee data.",
        "userConsentDisplayName": "Read-only access to your Employee records",
        "value": "Employees.Read.All"
        }
        ],

    La entrada debe ser única y, por tanto, debe generar un nuevo identificador único global (GUID) para la propiedad `"id"`. En este caso, como especificamos `"type": "User"`, este permiso puede ser consentido por cualquier cuenta autenticada por el inquilino de Azure AD en el que se registra la aplicación de recursos o de API, de forma que se concede a la aplicación cliente permiso para tener acceso a él en nombre de la cuenta. Las cadenas de descripción y nombre para mostrar se usan durante el consentimiento y para su presentación en el Portal de Azure clásico.

6. Cuando termine de actualizar el manifiesto, vuelva a la página de aplicación de Azure AD en el Portal de Azure clásico, haga clic en la característica "Administrar manifiesto" de nuevo (1) pero esta vez seleccione la opción "Cargar manifiesto" (2). De forma parecida a la descarga, aparecerá de nuevo un segundo cuadro de diálogo que le solicitará la ubicación del archivo de JSON. Haga clic en "Buscar archivo..." (3), a continuación, use el cuadro de diálogo "Elegir archivos para cargar" a fin de seleccionar el archivo de JSON (4) y haga clic en "Abrir". Una vez que el cuadro de diálogo desaparece, seleccione la marca de verificación de "Correcto" (5) y se cargará el manifiesto.

    ![Administración del manifiesto, opción de carga][MANAGE-MANIFEST-UPLOAD]

    ![Carga del manifiesto de JSON][UPLOAD-MANIFEST]

    ![Carga del manifiesto de JSON - confirmación][UPLOAD-MANIFEST-CONFIRM]

Ahora que el manifiesto está guardado, puede otorgar a una aplicación cliente registrada acceso al nuevo permiso que agregamos antes, pero esta vez puede usar la interfaz de usuario web del Portal de Azure clásico en lugar de editar el manifiesto de la aplicación cliente:

1. Primero, vaya a la página "Configurar" de la aplicación cliente a la que desea agregar acceso a la nueva API y haga clic en el botón "Agregar aplicación".
2. A continuación, aparecerá la lista de aplicaciones (API) de recursos registrado en el inquilino. Haga clic en el signo de la suma (+) junto al nombre de la aplicación de recursos para seleccionarla.  
3. A continuación, haga clic en la marca de verificación de la esquina inferior derecha. 
4. Cuando vuelva a la sección "Agregar aplicación" de la página de configuración de su cliente, verá la nueva aplicación de recursos en la lista. Si mantiene el mouse sobre la sección "Permisos delegados" a la derecha de esa fila, verá aparecer una lista desplegable. Haga clic en la lista y seleccione el nuevo permiso para poder agregarlo a la lista de permisos solicitados del cliente. Nota: Este permiso nuevo se almacenará en la configuración de identidad de la aplicación cliente, en la propiedad de colección "requiredResourceAccess".

![Permisos para otras aplicaciones][PERMS-TO-OTHER-APPS]

![Permisos para otras aplicaciones][PERMS-SELECT-APP]

![Permisos para otras aplicaciones][PERMS-SELECT-PERMS]

Eso es todo. Ahora las aplicaciones se ejecutarán con su nueva configuración de identidad.

## Pasos siguientes
Use la siguiente sección de comentarios DISQUS para proporcionar comentarios y ayudarnos a refinar y remodelar nuestro contenido.

<!--Image references-->
[DOWNLOAD-MANIFEST]: ./media/active-directory-application-manifest/download-manifest.png
[MANAGE-MANIFEST-DOWNLOAD]: ./media/active-directory-application-manifest/manage-manifest-download.png
[MANAGE-MANIFEST-UPLOAD]: ./media/active-directory-application-manifest/manage-manifest-upload.png
[PERMS-SELECT-APP]: ./media/active-directory-application-manifest/portal-perms-select-app.png
[PERMS-SELECT-PERMS]: ./media/active-directory-application-manifest/portal-perms-select-perms.png
[PERMS-TO-OTHER-APPS]: ./media/active-directory-application-manifest/portal-perms-to-other-apps.png
[SELECT-AZURE-AD-APP]: ./media/active-directory-application-manifest/select-azure-ad-application.png
[SELECT-AZURE-AD-TENANT]: ./media/active-directory-application-manifest/select-azure-ad-tenant.png
[UPDATE-MANIFEST]: ./media/active-directory-application-manifest/update-manifest.png
[UPLOAD-MANIFEST]: ./media/active-directory-application-manifest/upload-manifest.png
[UPLOAD-MANIFEST-CONFIRM]: ./media/active-directory-application-manifest/upload-manifest-confirm.png

<!--article references -->
[AAD-GROUPS-FOR-AUTHORIZATION]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[ADD-UPD-RMV-APP]: active-directory-integrating-applications.md
[APPLICATION-ENTITY]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#ApplicationEntity
[APPLICATION-ENTITY-APP-ROLE]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#AppRoleType
[APPLICATION-ENTITY-OAUTH2-PERMISSION]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#OAuth2PermissionType
[AZURE-CLASSIC-PORTAL]: https://manage.windowsazure.com
[DEV-GUIDE-TO-AUTH-WITH-ARM]: http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/
[GRAPH-API]: active-directory-graph-api.md
[INTEGRATING-APPLICATIONS-AAD]: https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
[O365-PERM-DETAILS]: https://msdn.microsoft.com/office/office365/HowTo/application-manifest
[O365-SERVICE-DAEMON-APPS]: https://msdn.microsoft.com/office/office365/howto/building-service-apps-in-office-365
[RBAC-CLOUD-APPS-AZUREAD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/

<!---HONumber=AcomDC_1223_2015-->