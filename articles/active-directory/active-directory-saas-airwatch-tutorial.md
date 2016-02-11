<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con AirWatch | Microsoft Azure" 
    description="Aprenda a usar AirWatch con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc." 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="01/14/2016" 
    ms.author="jeedes" />

#Tutorial: Integración de Azure Active Directory con AirWatch

El objetivo de este tutorial es mostrar la integración de Azure y AirWatch. En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción de Azure válida
-   Una suscripción habilitada para el inicio de sesión único en AirWatch

Después de completar este tutorial, los usuarios de Azure AD que ha asignado a AirWatch podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de AirWatch (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1.  Habilitación de la integración de aplicaciones para AirWatch
2.  Configuración del inicio de sesión único
3.  Configuración del aprovisionamiento de usuario
4.  Asignación de usuarios

![AirWatch](./media/active-directory-saas-airwatch-tutorial/IC791913.png "AirWatch")
##Habilitación de la integración de aplicaciones para AirWatch

El objetivo de esta sección es describir cómo se habilita la integración de aplicaciones para AirWatch.

###Siga estos pasos para habilitar la integración de aplicaciones para AirWatch:

1.  En el panel de navegación izquierdo del Portal de administración de Azure, haga clic en **Active Directory**.

    ![Active Directory](./media/active-directory-saas-airwatch-tutorial/IC700993.png "Active Directory")

2.  En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3.  Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

    ![Aplicaciones](./media/active-directory-saas-airwatch-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación](./media/active-directory-saas-airwatch-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Agregar una aplicación de la galería](./media/active-directory-saas-airwatch-tutorial/IC749322.png "Agregar una aplicación de la galería")

6.  En el **cuadro de búsqueda**, escriba **AirWatch**.

    ![Galería de aplicaciones](./media/active-directory-saas-airwatch-tutorial/IC791914.png "Galería de aplicaciones")

7.  En el panel de resultados, seleccione **AirWatch** y, a continuación, haga clic en **Completar** para agregar la aplicación.

    ![AirWatch](./media/active-directory-saas-airwatch-tutorial/IC791915.png "AirWatch")
##Configuración del inicio de sesión único

El objetivo de esta sección es describir cómo habilitar usuarios para que se autentiquen en AirWatch con su cuenta de Azure AD mediante federación basada en el protocolo SAML. Como parte de este procedimiento, se requiere crear un archivo de certificado codificado en base 64. Si no está familiarizado con este procedimiento, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

###Siga estos pasos para configurar el inicio de sesión único:

1.  En el Portal de Azure AD, en la página de integración de aplicaciones de **AirWatch**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-airwatch-tutorial/IC791916.png "Configurar inicio de sesión único")

2.  En la página **¿Cómo desea que los usuarios inicien sesión en AirWatch?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y, a continuación, haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-airwatch-tutorial/IC791917.png "Configurar inicio de sesión único")

3.  En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto **URL de inicio de sesión de AirWatch**, escriba la dirección URL que usan sus usuarios para iniciar sesión en la aplicación AirWatch (p. ej.: "*https:// companycode.awmdm.com/AirWatch/Login?gid=companycode*") y, a continuación, haga clic en **Siguiente**.

    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-airwatch-tutorial/IC791918.png "Configurar dirección URL de la aplicación")

4.  En la página **Configurar inicio de sesión único en AirWatch**, haga clic en **Descargar certificado** y, a continuación, guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-airwatch-tutorial/IC791919.png "Configurar inicio de sesión único")

5.  En otra ventana del explorador web, inicie sesión en el sitio de la compañía de AirWatch como administrador.

6.  En el panel de navegación izquierdo, haga clic en **Accounts** (Cuentas) y luego en **Administrators** (Administradores).

    ![Administradores](./media/active-directory-saas-airwatch-tutorial/IC791920.png "Administradores")

7.  Expanda el menú **Settings** (Configuración) y, a continuación, haga clic en **Directory Services** (Servicios de directorio).

    ![Settings](./media/active-directory-saas-airwatch-tutorial/IC791921.png "Settings")

8.  Haga clic en la pestaña **User** (Usuario), en el campo de texto **Base DN** (DN base), escriba el nombre de dominio y, a continuación, haga clic en **Save** (Guardar).

    ![Usuario](./media/active-directory-saas-airwatch-tutorial/IC791922.png "Usuario")

9.  Haga clic en la pestaña **Server** (Servidor).

    ![Server](./media/active-directory-saas-airwatch-tutorial/IC791923.png "Server")

10. Lleve a cabo los siguiente pasos:

    ![Cargar](./media/active-directory-saas-airwatch-tutorial/IC791924.png "Cargar")

    1.  En **Directory Type** (Tipo de directorio), seleccione **None** (Ninguno).
    2.  Seleccione **Use SAML For Authentication** (Usar SAML para autenticación).
    3.  Para cargar el certificado descargado, haga clic en **Upload** (Cargar).

11. En la sección **Request** (Solicitud), siga estos pasos:

    ![Solicitud](./media/active-directory-saas-airwatch-tutorial/IC791925.png "Solicitud")

    1.  En **Request Binding Type** (Tipo de enlace de solicitud), seleccione **POST**.
    2.  En el Portal de Azure, en la página de diálogo **Configurar inicio de sesión único en Airwatch**, copie el valor de **Dirección URL del servicio de inicio de sesión único** y péguelo en el cuadro de texto **Dirección URL de inicio de sesión de proveedor de identidades**.
    3.  En la lista **NameID Format** (Formato de NameID), seleccione **Email address** (Dirección de correo electrónico).
    4.  Haga clic en **Guardar**.

12. Haga clic de nuevo en la pestaña **User** (Usuario).

    ![Usuario](./media/active-directory-saas-airwatch-tutorial/IC791926.png "Usuario")

13. En la sección **Attribute** (Atributo), realice estos pasos:

    ![Atributo](./media/active-directory-saas-airwatch-tutorial/IC791927.png "Atributo")

    1.  En el cuadro de texto **Object Identifier** (Identificador de objeto), escriba **http://schemas.microsoft.com/identity/claims/objectidentifier**.
    2.  En el cuadro de texto **Username** (Nombre de usuario), escriba **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
    3.  En el cuadro de texto **Display Name** (Nombre para mostrar), escriba **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.
    4.  En el cuadro de texto **First Name** (Nombre), escriba **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.
    5.  En el cuadro de texto **Last Name** (Apellido), escriba **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**.
    6.  En el cuadro de texto **Correo electrónico**, escriba **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
    7.  Haga clic en **Guardar**.

14. En el Portal de Azure AD, seleccione la confirmación de configuración de inicio de sesión único y, a continuación, haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-airwatch-tutorial/IC791928.png "Configurar inicio de sesión único")
##Configuración del aprovisionamiento de usuario

Para permitir que los usuarios de Azure AD inicien sesión en AirWatch, tienen que aprovisionarse en AirWatch. En el caso de AirWatch, el aprovisionamiento es una tarea manual.

###Para aprovisionar cuentas de usuario, realice estos pasos:

1.  Inicie sesión en el sitio de la compañía de **AirWatch** como administrador.

2.  En el panel de navegación izquierdo, haga clic en **Accounts** (Cuentas) y luego en **Users** (Usuarios).

    ![Usuarios](./media/active-directory-saas-airwatch-tutorial/IC791929.png "Usuarios")

3.  En el menú **Users** (Usuarios), haga clic en **List View** (Vista de lista) y, a continuación, haga clic en **Add > Add User**. (Agregar > Agregar usuario).

    ![Agregar usuario](./media/active-directory-saas-airwatch-tutorial/IC791930.png "Agregar usuario")

4.  En el cuadro de diálogo **Add / Edit User** (Agregar/Editar usuario), realice los siguientes pasos:

    ![Agregar usuario](./media/active-directory-saas-airwatch-tutorial/IC791931.png "Agregar usuario")

    1.  Especifique **Username** (Nombre de usuario), **Password** (Contraseña), **Confirm Password** (Confirmar contraseña), **First Name** (Nombre), **Last Name** (Apellido), **Email Address** (Correo electrónico) de una cuenta de Azure Active Directory válida que quiera aprovisionar en los cuadros de texto relacionados.
    2.  Haga clic en **Guardar**.

>[AZURE.NOTE]Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de AirWatch ofrecida por AirWatch para aprovisionar cuentas de usuario de AAD.

##Asignación de usuarios

Para probar la configuración, tiene que conceder acceso, mediante su asignación, a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

###Para asignar usuarios a AirWatch, siga estos pasos:

1.  En el Portal de Azure AD, cree una cuenta de prueba.

2.  En la página de integración de aplicaciones de **AirWatch**, haga clic en **Asignar usuarios**.

    ![Asignar usuarios](./media/active-directory-saas-airwatch-tutorial/IC791932.png "Asignar usuarios")

3.  Seleccione su usuario de prueba, haga clic en **Asignar** y luego en **Sí** para confirmar la asignación.

    ![Sí](./media/active-directory-saas-airwatch-tutorial/IC767830.png "Sí")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0121_2016-->