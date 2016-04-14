<properties
   pageTitle="Creación de aplicación de AD y entidad de servicio en el portal | Microsoft Azure"
   description="Describe cómo crear una nueva aplicación de Active Directory y una entidad de servicio que puede utilizarse con el control de acceso basado en roles en el Administrador de recursos de Azure para administrar el acceso a los recursos."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/27/2016"
   ms.author="tomfitz"/>

# Creación de aplicación de Active Directory y entidad de servicio mediante el portal

## Información general
Si tiene una aplicación o un proceso automatizado que necesita tener acceso a unos recursos o modificarlos, puede usar el portal clásico para crear una aplicación de Active Directory. Si crea una aplicación de Active Directory a través del portal clásico, en realidad se crea la aplicación y una entidad de servicio. Puede ejecutar la aplicación con la identidad de esta o con la identidad del usuario que ha iniciado sesión en la aplicación. Estos dos métodos de autenticación de las aplicaciones se conocen como interactivo (el usuario inicia sesión) y no interactivo (la aplicación proporciona sus propias credenciales). En el modo no interactivo, debe asignar la entidad de servicio a un rol con el permiso correcto.

En este tema se muestra cómo crear una nueva aplicación y entidad de servicio mediante el portal clásico. Actualmente, debe usar el portal clásico para crear una nueva aplicación de Active Directory. Esta capacidad se agregará al portal de Azure en una versión posterior. Puede utilizar el portal para asignar la aplicación a un rol. También puede llevar a cabo estos pasos a través de Azure PowerShell o CLI de Azure. Para obtener más información sobre cómo usar PowerShell o CLI con la entidad de servicio, consulte [Autenticación de una entidad de servicio con el Administrador de recursos de Azure](resource-group-authenticate-service-principal.md).

## Conceptos
1. Azure Active Directory (AAD): un servicio de administración de identidades y acceso creado para la nube. Para obtener más información, consulte [¿Qué es Azure Active Directory?](active-directory/active-directory-whatis.md)
2. Entidad de servicio: una instancia de una aplicación en un directorio.
3. Aplicación de AD: un registro de directorio en AAD que identifica una aplicación en AAD. 

Para obtener una explicación más detallada de las aplicaciones y entidades de servicio, consulte [Objetos de aplicación y de entidad de servicio](active-directory/active-directory-application-objects.md). Para obtener más información acerca de la autenticación de Active Directory, vea [Escenarios de autenticación en Azure AD](active-directory/active-directory-authentication-scenarios.md).


## Creación de aplicación

Para aplicaciones interactivas y no interactivas, debe crear y configurar la aplicación de Active Directory.

1. Inicie sesión en su cuenta de Azure a través del [portal clásico](https://manage.windowsazure.com/).

2. Seleccione **Active Directory** en el panel izquierdo.

     ![seleccionar Active Directory][1]

3. Seleccione el directorio que desea utilizar para crear la nueva aplicación.

     ![elegir directorio][2]

3. Para ver las aplicaciones en su directorio, haga clic en **Aplicaciones**.

     ![ver aplicaciones][11]

4. Si no ha creado una aplicación en ese directorio previamente, debería ver algo similar a la siguiente imagen. Haga clic en **AGREGAR UNA APLICACIÓN**.

     ![agregar aplicación][6]

     O bien, haga clic en **Agregar** en el panel inferior.

     ![agregar][12]

5. Seleccione el tipo de aplicación que desea crear. Para este tutorial, no usaremos una aplicación desde la galería.

     ![nueva aplicación][10]

6. Rellene el nombre de la aplicación y seleccione el tipo de aplicación que desea utilizar. Seleccione el tipo de aplicación que va a crear. Para este tutorial, vamos a elegir crear una **APLICACIÓN WEB Y/O API WEB** y hacer clic en el botón siguiente.

     ![aplicación de nombre][9]

7. Rellene las propiedades de la aplicación. Para **DIRECCIÓN URL DE INICIO DE SESIÓN**, proporcione el URI para un sitio web que describe la aplicación. No se valida la existencia del sitio web. Para **URI DE ID. DE APLICACIÓN**, proporcione el URI que identifica la aplicación. No se valida la singularidad o la existencia del extremo. Si había seleccionado **Aplicación de cliente nativo** para el tipo de aplicación, proporcionará un valor de **URI de redirección**. Haga clic en **Completo** para crear la aplicación de AAD.

     ![propiedades de la aplicación][4]

Ha creado la aplicación.

## Obtención de id. de cliente e id. de inquilino

Al tener acceso a la aplicación mediante programación, necesitará el id. de la aplicación. Seleccione la pestaña **Configurar** y copie el **ID. DE CLIENTE**.
  
   ![id. de cliente][5]

En algunos casos, debe pasar el id. del inquilino con su solicitud de autenticación. Para recuperar el id. de inquilino, seleccione **Ver puntos de conexión** en la parte inferior de la pantalla y recupere el id. como se muestra a continuación.

   ![id. de inquilino](./media/resource-group-create-service-principal-portal/save-tenant.png)

## Creación de una clave de autenticación

Si la aplicación se va a ejecutar con sus propias credenciales, debe crear una clave para la aplicación.

1. Haga clic en la pestaña **Configurar** para configurar la contraseña de su aplicación.

     ![configurar aplicación][3]

2. Desplácese hacia abajo hasta la sección **Claves** y seleccione cuánto tiempo desea que la contraseña sea válida.

     ![claves][7]

3. Seleccione **Guardar** para crear la clave.

     ![guardar][13]

     Se muestra la clave guardada, y tiene la posibilidad de copiarla. No podrá recuperar la clave más tarde, por lo que querrá recuperarla ahora.

     ![clave guardada][8]

La aplicación está ahora lista y la entidad de servicio creada en el inquilino. Al iniciar sesión como una entidad de servicio asegúrese de usar lo siguiente:

* **ID. DE CLIENTE**: como nombre de usuario.
* **CLAVE**: como contraseña.

## Establecimiento de permisos delegados

Si la aplicación tiene acceso a recursos en nombre del usuario que ha iniciado sesión, debe conceder a la aplicación el permiso delegado para tener acceso a otras aplicaciones. Esto se puede hacer en la sección de **permisos para otras aplicaciones** de la pestaña **Configurar**. De forma predeterminada, ya hay habilitado un permiso delegado para Azure Active Directory. Deje este permiso delegado sin cambios.

1. Seleccione **Agregar aplicación**.

2. En la lista, seleccione **API de administración de servicios de Azure**.

      ![seleccionar aplicación](./media/resource-group-create-service-principal-portal/select-app.png)

3. Agregue el permiso delegado **Acceso a la Administración de servicios de Azure (vista previa)** a la API de administración de servicios.

       ![seleccionar permiso](./media/resource-group-create-service-principal-portal/select-permissions.png)

4. Guarde el cambio.

## Configuración de aplicación multiinquilino

Si los usuarios de otros directorios de Azure Active Directory pueden dar su consentimiento a la aplicación e iniciar sesión en ella, debe habilitar los servicios multiinquilino. En la pestaña **Configurar**, establezca **La aplicación es multiempresa** en **Sí**.

![multiinquilino](./media/resource-group-create-service-principal-portal/multi-tenant.png)

## Asignación de aplicación a un rol

Si la aplicación no se está ejecutando con la identidad de un usuario que ha iniciado sesión, debe asignar la aplicación a un rol a fin de concederle permisos para realizar acciones. Para asignar la aplicación a un rol, cambie desde el portal clásico al [Portal de Azure](https://portal.azure.com). Debe decidir qué rol agregar a la aplicación y en qué ámbito. Para obtener más información acerca de los roles disponibles, vea [RBAC: Roles integrados](./active-directory/role-based-access-built-in-roles.md). Puede establecer el ámbito en el nivel de suscripción, grupo de recursos o recurso. Los permisos se heredan en los niveles inferiores de ámbito (por ejemplo, el hecho de agregar una aplicación al rol Lector para un grupo de recursos significa que esta puede leer el grupo de recursos y los recursos que contenga).

1. En el portal, desplácese hasta el nivel de ámbito al que desea asignar la aplicación. En este tema, puede navegar a un grupo de recursos y, en la hoja del grupo de recursos, seleccione el icono **Acceso**.

     ![seleccionar usuarios](./media/resource-group-create-service-principal-portal/select-users.png)

2. Seleccione **Agregar**.

     ![seleccionar agregar](./media/resource-group-create-service-principal-portal/select-add.png)

3. Seleccione el rol **lector** (o el rol que desea asignar a la aplicación).

     ![seleccionar rol](./media/resource-group-create-service-principal-portal/select-role.png)

4. En primer lugar, verá la lista de usuarios que puede agregar al rol, no verá las aplicaciones. Solo verá el grupo y los usuarios.

     ![mostrar usuarios](./media/resource-group-create-service-principal-portal/show-users.png)

5. Para que la aplicación aparezca, debe buscarla. Comience a escribir el nombre de la aplicación y cambiará la lista de opciones disponibles. Seleccione la aplicación cuando la vea en la lista.

     ![asignar a rol](./media/resource-group-create-service-principal-portal/assign-to-role.png)

6. Seleccione **Aceptar** para finalizar la asignación de un rol. Ahora debería ver la aplicación en la lista de usos asignados a un rol para el grupo de recursos.

     ![mostrar](./media/resource-group-create-service-principal-portal/show-app.png)

Para obtener más información acerca de cómo asignar usuarios y aplicaciones a los roles a través del portal, consulte [Administración del acceso con el Portal de administración de Azure](../role-based-access-control-configure/#manage-access-using-the-azure-management-portal).

## Obtención de token de acceso en el código

Si está utilizando. NET, puede recuperar el token de acceso para su aplicación con el código siguiente.

En primer lugar, debe instalar la biblioteca de autenticación de Active Directory en el proyecto de Visual Studio. La manera más fácil de hacerlo es utilizar el paquete de NuGet. Abra la ventana Consola del Administrador de paquetes y escriba los siguientes comandos.

    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.19.208020213
    PM> Update-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Safe

Para iniciar sesión con el id. y el secreto de cliente, utilice el método siguiente para recuperar el token.

    public static string GetAccessToken()
    {
        var authenticationContext = new AuthenticationContext("https://login.windows.net/{tenantId or tenant name}");  
        var credential = new ClientCredential(clientId: "{client id}", clientSecret: "{application password}");
        var result = authenticationContext.AcquireToken(resource: "https://management.core.windows.net/", clientCredential:credential);

        if (result == null) {
            throw new InvalidOperationException("Failed to obtain the JWT token");
        }

        string token = result.AccessToken;

        return token;
    }

Para iniciar sesión en nombre del usuario, use el método siguiente para recuperar el token.

    public static string GetAcessToken()
    {
        var authenticationContext = new AuthenticationContext("https://login.windows.net/{tenant id}");
        var result = authenticationContext.AcquireToken(resource: "https://management.core.windows.net/", {client id}, new Uri({redirect uri});

        if (result == null) {
            throw new InvalidOperationException("Failed to obtain the JWT token");
        }

        string token = result.AccessToken;

        return token;
    }

Puede pasar el token en el encabezado de la solicitud con el código siguiente:

    string token = GetAcessToken();
    request.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);


## Pasos siguientes

- Para obtener información sobre cómo especificar directivas de seguridad, consulte [Control de acceso basado en roles de Azure](./active-directory/role-based-access-control-configure.md).  
- Para ver una demostración en vídeo de estos pasos, consulte [Enabling Programmatic Management of an Azure Resource with Azure Active Directory](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Enabling-Programmatic-Management-of-an-Azure-Resource-with-Azure-Active-Directory).
- Para obtener información acerca del uso de Azure PowerShell o la CLI de Azure para trabajar con aplicaciones de Active Directory y entidades de servicio, incluida la forma de utilizar un certificado para la autenticación, consulte [Autenticación de una entidad de servicio con el Administrador de recursos de Azure](./resource-group-authenticate-service-principal.md).
- Para obtener instrucciones sobre cómo implementar la seguridad con el Administrador de recursos de Azure, consulte [Consideraciones de seguridad para el Administrador de recursos de Azure](best-practices-resource-manager-security.md).


<!-- Images. -->
[1]: ./media/resource-group-create-service-principal-portal/active-directory.png
[2]: ./media/resource-group-create-service-principal-portal/active-directory-details.png
[3]: ./media/resource-group-create-service-principal-portal/application-configure.png
[4]: ./media/resource-group-create-service-principal-portal/app-properties.png
[5]: ./media/resource-group-create-service-principal-portal/client-id.png
[6]: ./media/resource-group-create-service-principal-portal/create-application.png
[7]: ./media/resource-group-create-service-principal-portal/create-key.png
[8]: ./media/resource-group-create-service-principal-portal/save-key.png
[9]: ./media/resource-group-create-service-principal-portal/tell-us-about-your-application.png
[10]: ./media/resource-group-create-service-principal-portal/what-do-you-want-to-do.png
[11]: ./media/resource-group-create-service-principal-portal/view-applications.png
[12]: ./media/resource-group-create-service-principal-portal/add-icon.png
[13]: ./media/resource-group-create-service-principal-portal/save-icon.png

<!---HONumber=AcomDC_0224_2016-->