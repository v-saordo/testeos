<properties 
    pageTitle="Tutorial: integración de Azure Active Directory con AppDynamics | Microsoft Azure" 
    description="Aprenda a usar AppDynamics con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc." 
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

#Tutorial: Integración de Azure Active Directory con AppDynamics

El objetivo de este tutorial es mostrar la integración de Azure y AppDynamics. En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción de Azure válida
-   Una suscripción habilitada para el inicio de sesión único en AppDynamics

Después de completar este tutorial, los usuarios de Azure AD que ha asignado a AppDynamics podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de AppDynamics (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1.  Habilitación de la integración de aplicaciones para AppDynamics
2.  Configuración del inicio de sesión único
3.  Configuración del aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario](./media/active-directory-saas-appdynamics-tutorial/IC790209.png "Escenario")
##Habilitación de la integración de aplicaciones para AppDynamics

El objetivo de esta sección es describir cómo habilitar la integración de aplicaciones para AppDynamics.

###Siga estos pasos para habilitar la integración de aplicaciones para AppDynamics:

1.  En el panel de navegación izquierdo del Portal de administración de Azure, haga clic en **Active Directory**.

    ![Active Directory](./media/active-directory-saas-appdynamics-tutorial/IC700993.png "Active Directory")

2.  En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3.  Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

    ![Aplicaciones](./media/active-directory-saas-appdynamics-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación](./media/active-directory-saas-appdynamics-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Agregar una aplicación de la galería](./media/active-directory-saas-appdynamics-tutorial/IC749322.png "Agregar una aplicación de la galería")

6.  En el **cuadro de búsqueda**, escriba **AppDynamics**.

    ![Galería de aplicaciones](./media/active-directory-saas-appdynamics-tutorial/IC790210.png "Galería de aplicaciones")

7.  En el panel de resultados, seleccione **AppDynamics** y luego haga clic en **Completar** para agregar la aplicación.

    ![AppDynamics](./media/active-directory-saas-appdynamics-tutorial/IC790211.png "AppDynamics")
##Configuración del inicio de sesión único

El objetivo de esta sección es describir cómo habilitar usuarios para que se autentiquen en AppDynamics con su cuenta de Azure AD mediante federación basada en el protocolo SAML. Como parte de este procedimiento, se requiere crear un archivo de certificado codificado en base 64. Si no está familiarizado con este procedimiento, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

###Siga estos pasos para configurar el inicio de sesión único:

1.  En el Portal de Azure AD, en la página de integración de aplicaciones de **AppDynamics**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-appdynamics-tutorial/IC790212.png "Configurar inicio de sesión único")

2.  En la página **¿Cómo desea que los usuarios inicien sesión en AppDynamics?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y luego haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-appdynamics-tutorial/IC790213.png "Configurar inicio de sesión único")

3.  En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto **URL de inicio de sesión de AppDynamics**, escriba la dirección URL que los usuarios usan para iniciar sesión en AppDynamics ("**https://companyname.saas.appdynamics.com*")) y haga clic en **Siguiente**.

    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-appdynamics-tutorial/IC790214.png "Configurar dirección URL de la aplicación")

4.  En la página **Configuración de inicio de sesión único en AppDynamics**, para descargar el certificado, haga clic en **Descargar certificado** y luego guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-appdynamics-tutorial/IC790215.png "Configurar inicio de sesión único")

5.  En otra ventana del explorador web, inicie sesión en el sitio de la compañía de AppDynamics como administrador.

6.  En la barra de herramientas de la parte superior, haga clic en **Configuración** y luego en **Administración**.

    ![Administración](./media/active-directory-saas-appdynamics-tutorial/IC790216.png "Administración")

7.  Haga clic en la pestaña **Authentication Provider** (Proveedor de autenticación).

    ![Proveedor de autenticación](./media/active-directory-saas-appdynamics-tutorial/IC790224.png "Proveedor de autenticación")

8.  En la sección **Proveedor de autenticación**, realice estos pasos:

    ![Configuración de SAML](./media/active-directory-saas-appdynamics-tutorial/IC790225.png "Configuración de SAML")

    1.  En **Authentication Provider** (Proveedor de autenticación), seleccione **SAML**.
    2.  En el Portal de Azure, en la página de diálogo **Configurar inicio de sesión único en AppDynamics**, copie el valor de **Dirección URL de inicio de sesión remoto** y péguelo en el cuadro de texto **Dirección URL de inicio de sesión**.
    3.  En el Portal de Azure, en la página de diálogo **Configurar inicio de sesión único en AppDynamics**, copie el valor de **Dirección URL de cierre de sesión remoto** y péguelo en el cuadro de texto **Dirección URL de cierre de sesión**.
    4.  Cree un archivo **codificado en base 64** a partir del certificado descargado.  

        >[AZURE.TIP]Para obtener más información, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

    5.  Abra el certificado codificado en base 64 en el Bloc de notas, copie el contenido del mismo en el Portapapeles y luego péguelo en el cuadro de texto **Certificado**.
    6.  Haga clic en **Guardar**. ![Save](./media/active-directory-saas-appdynamics-tutorial/IC777673.png "Save")

9.  En el Portal de Azure AD, seleccione la confirmación de configuración de inicio de sesión único y, a continuación, haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-appdynamics-tutorial/IC790226.png "Configurar inicio de sesión único")
##Configuración del aprovisionamiento de usuario

Para permitir que los usuarios de Azure AD inicien sesión en AppDynamics, tienen que aprovisionarse en AppDynamics. En el caso de AppDynamics, el aprovisionamiento es una tarea manual.

###Siga estos pasos para configurar el aprovisionamiento de usuario:

1.  Inicie sesión en su sitio de la compañía de AppDynamics como administrador.

2.  Vaya a **Users** (Usuarios) y, a continuación, haga clic en **+** para abrir el cuadro de diálogo **Create User** (Crear usuario).

    ![Usuarios](./media/active-directory-saas-appdynamics-tutorial/IC790229.png "Usuarios")

3.  En la sección **Crear usuario**, lleve a cabo estos pasos:

    ![Crear usuario](./media/active-directory-saas-appdynamics-tutorial/IC790230.png "Crear usuario")

    1.  Escriba **Nombre de usuario**, **Nombre**, **Correo electrónico**, **Nueva contraseña**, **Repetir nueva contraseña** de una cuenta válida de AAD que desee aprovisionar en los cuadros de texto relacionados.
    2.  Haga clic en **Guardar**.

>[AZURE.NOTE]Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de AppDynamics suministrada por AppDynamics para aprovisionar cuentas de usuario de Azure AD.

##Asignación de usuarios

Para probar la configuración, tiene que conceder acceso, mediante su asignación, a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

###Para asignar usuarios a AppDynamics, lleve a cabo los siguientes pasos:

1.  En el Portal de Azure AD, cree una cuenta de prueba.

2.  En la página de integración de aplicaciones de **AppDynamics**, haga clic en **Asignar usuarios**.

    ![Asignar usuarios](./media/active-directory-saas-appdynamics-tutorial/IC790231.png "Asignar usuarios")

3.  Seleccione su usuario de prueba, haga clic en **Asignar** y luego en **Sí** para confirmar la asignación.

    ![Sí](./media/active-directory-saas-appdynamics-tutorial/IC767830.png "Sí")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0121_2016-->