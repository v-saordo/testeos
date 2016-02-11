<properties 
    pageTitle="Tutorial: integración de Azure Active Directory con ScreenSteps | Microsoft Azure" 
    description="Aprenda cómo usar ScreenSteps con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc." 
    services="active-directory" 
    authors="markusvi"  
    documentationCenter="na" manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="01/12/2016" 
    ms.author="markvi" />

#Tutorial: Integración de Azure Active Directory con ScreenSteps
  
El objetivo de este tutorial es mostrar la integración de Azure y ScreenSteps. En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción de Azure válida
-   Un inquilino de ScreenSteps
  
Después de completar este tutorial, los usuarios de Azure AD que ha asignado a ScreenSteps podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de ScreenSteps (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
La situación descrita en este tutorial consta de los siguientes bloques de creación:

1.  Habilitación de la integración de aplicaciones para ScreenSteps
2.  Configuración del inicio de sesión único
3.  Configuración del aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario](./media/active-directory-saas-screensteps-tutorial/IC778516.png "Escenario")
##Habilitación de la integración de aplicaciones para ScreenSteps
  
El objetivo de esta sección es describir cómo habilitar la integración de las aplicaciones para ScreenSteps.

###Siga estos pasos para habilitar la integración de aplicaciones para ScreenSteps:

1.  En el panel de navegación izquierdo del Portal de administración de Azure, haga clic en **Active Directory**.

    ![Active Directory](./media/active-directory-saas-screensteps-tutorial/IC700993.png "Active Directory")

2.  En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3.  Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

    ![Aplicaciones](./media/active-directory-saas-screensteps-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación](./media/active-directory-saas-screensteps-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Agregar una aplicación de la galería](./media/active-directory-saas-screensteps-tutorial/IC749322.png "Agregar una aplicación de la galería")

6.  En el **cuadro de búsqueda**, escriba **ScreenSteps**.

    ![Galería de aplicaciones](./media/active-directory-saas-screensteps-tutorial/IC778517.png "Galería de aplicaciones")

7.  En el panel de resultados, seleccione **ScreenSteps** y, luego, haga clic en **Completa** para agregar la aplicación.

    ![ScreenSteps](./media/active-directory-saas-screensteps-tutorial/IC778518.png "ScreenSteps")
##Configuración del inicio de sesión único
  
El objetivo de esta sección es describir cómo se habilita la autenticación de los usuarios en ScreenSteps con su cuenta de Azure AD usando el protocolo SAML basado en la federación.

###Siga estos pasos para configurar el inicio de sesión único:

1.  En el portal de Azure AD, en la página de integración de aplicaciones de **ScreenSteps**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-screensteps-tutorial/IC778519.png "Configurar inicio de sesión único")

2.  En la página **¿Cómo desea que los usuarios inicien sesión en ScreenSteps?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y, luego , haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-screensteps-tutorial/IC778520.png "Configurar inicio de sesión único")

3.  En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto de **URL de inicio de sesión de ScreenSteps**, escriba su dirección URL con el siguiente patrón "*https://\<nombre-inquilino>.ScreenSteps.com*" y luego haga clic en **Siguiente**.

    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-screensteps-tutorial/IC778521.png "Configurar dirección URL de la aplicación")

4.  En la página **Configuración de inicio de sesión único en ScreenSteps**, para descargar el certificado, haga clic en **Descargar certificado** y luego guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-screensteps-tutorial/IC778522.png "Configurar inicio de sesión único")

5.  En otra ventana del explorador web, inicie sesión en su sitio de la compañía de ScreenSteps como administrador.

6.  Haga clic en **Administración de cuentas**.

    ![Administración de cuentas](./media/active-directory-saas-screensteps-tutorial/IC778523.png "Administración de cuentas")

7.  Haga clic en **Autenticación remota**.

    ![Autenticación remota](./media/active-directory-saas-screensteps-tutorial/IC778524.png "Autenticación remota")

8.  Haga clic en **Crear extremo de autenticación**.

    ![Autenticación remota](./media/active-directory-saas-screensteps-tutorial/IC778525.png "Autenticación remota")

9.  En la sección **Crear un punto de conexión de autenticación**, siga estos pasos:

    ![Cree un extremo de autenticación](./media/active-directory-saas-screensteps-tutorial/IC778526.png "Cree un extremo de autenticación")

    1.  En el cuadro de texto **Título**, escriba un título.
    2.  En la lista **Modo**, seleccione **SAML**.
    3.  Haga clic en **Crear**.

10. En la sección **Punto de conexión de autenticación remota**, siga estos pasos:

    ![Extremo de autenticación remota](./media/active-directory-saas-screensteps-tutorial/IC778527.png "Extremo de autenticación remota")

    1.  En el Portal de Azure AD, en la página de diálogo **Configurar inicio de sesión único en ScreenSteps**, copie el valor de la **Dirección URL de inicio de sesión remoto** y péguelo en el cuadro de texto **Remote Login URL (dirección URL de inicio de sesión remoto)**.
    2.  En el Portal de Azure AD, en la página de diálogo **Configurar inicio de sesión único en ScreenSteps**, copie el valor de la **Dirección URL de cierre de sesión remoto** y péguelo en el cuadro de texto **Dirección URL de inicio de sesión remoto**.
    3.  Haga clic en **Elegir un archivo** y luego cargue el certificado descargado.
    4.  Haga clic en **Actualizar**.

11. En el portal de Azure AD, seleccione la confirmación de configuración de inicio de sesión único y luego haga clic en **Completa** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-screensteps-tutorial/IC778542.png "Configurar inicio de sesión único")
##Configuración del aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD inicien sesión en **ScreenSteps**, deben aprovisionarse en **ScreenSteps**. En el caso de **ScreenSteps**, el aprovisionamiento es una tarea manual.

###Para aprovisionar cuentas de usuario a ScreenSteps, realice los siguientes pasos:

1.  Inicie sesión en su inquilino de **ScreenSteps**.

2.  Haga clic en **Administración de cuentas**.

    ![Administración de cuentas](./media/active-directory-saas-screensteps-tutorial/IC778523.png "Administración de cuentas")

3.  Haga clic en **Usuarios**.

    ![Usuarios](./media/active-directory-saas-screensteps-tutorial/IC778544.png "Usuarios")

4.  Haga clic en **Crear un usuario**.

    ![Todos los usuarios](./media/active-directory-saas-screensteps-tutorial/IC778545.png "Todos los usuarios")

5.  Desde la lista **Rol de usuario**, seleccione un rol para el usuario.

6.  En la sección de Rol de usuario, escriba el "**Nombre**, **Apellido**, **Correo electrónico**, **Inicio de sesión**, **Contraseña** y **Confirmación de contraseña**" de una cuenta válida de AAD que quiera aprovisionar en los cuadros de texto relacionados.

    ![Nuevo usuario](./media/active-directory-saas-screensteps-tutorial/IC778546.png "Nuevo usuario")

7.  En la sección Grupos, seleccione**Grupo de autenticación (SAML)**y luego haga clic en **Crear usuario**.

    ![Grupos](./media/active-directory-saas-screensteps-tutorial/IC778547.png "Grupos")

>[AZURE.NOTE]Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de ScreenSteps ofrecida por ScreenSteps para aprovisionar cuentas de usuario de AAD.

##Asignación de usuarios
  
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

###Para asignar usuarios a ScreenSteps, lleve a cabo los siguientes pasos:

1.  En el portal de Azure AD, cree una cuenta de prueba.

2.  En la página de integración de la aplicación **ScreenSteps**, haga clic en **Asignar usuarios**.

    ![Asignar usuarios](./media/active-directory-saas-screensteps-tutorial/IC773094.png "Asignar usuarios")

3.  Seleccione su usuario de prueba, haga clic en **Asignar** y luego en **Sí** para confirmar la asignación.

    ![Asignar usuarios](./media/active-directory-saas-screensteps-tutorial/IC778548.png "Asignar usuarios")
  
Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0114_2016-->