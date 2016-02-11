<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con Boomi | Microsoft Azure" 
    description="Aprenda a usar Boomi con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc." 
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

#Tutorial: Integración de Azure Active Directory con Boomi

El objetivo de este tutorial es mostrar la integración de Azure y Boomi. En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción de Azure válida
-   Una suscripción habilitada para inicio de sesión único en Boomi

Después de completar este tutorial, los usuarios de Azure AD que ha asignado a Boomi podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de Boomi (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1.  Habilitación de la integración de aplicaciones para Boomi
2.  Configuración del inicio de sesión único
3.  Configuración del aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario](./media/active-directory-saas-boomi-tutorial/IC791134.png "Escenario")
##Habilitación de la integración de aplicaciones para Boomi

El objetivo de esta sección es describir cómo se habilita la integración de las aplicaciones para Boomi.

###Siga estos pasos para habilitar la integración de aplicaciones para Boomi:

1.  En el panel de navegación izquierdo del Portal de administración de Azure, haga clic en **Active Directory**.

    ![Active Directory](./media/active-directory-saas-boomi-tutorial/IC700993.png "Active Directory")

2.  En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3.  Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

    ![Aplicaciones](./media/active-directory-saas-boomi-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación](./media/active-directory-saas-boomi-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Agregar una aplicación de la galería](./media/active-directory-saas-boomi-tutorial/IC749322.png "Agregar una aplicación de la galería")

6.  En el **cuadro de búsqueda**, escriba **Boomi**.

    ![Galería de aplicaciones](./media/active-directory-saas-boomi-tutorial/IC790822.png "Galería de aplicaciones")

7.  En el panel de resultados, seleccione **Boomi** y luego haga clic en **Completar** para agregar la aplicación.

    ![Boomi](./media/active-directory-saas-boomi-tutorial/IC790823.png "Boomi")
##Configuración del inicio de sesión único

El objetivo de esta sección es describir cómo se habilita la autenticación de usuarios en Boomi con su cuenta de Azure AD mediante la federación basada en el protocolo SAML.

###Siga estos pasos para configurar el inicio de sesión único:

1.  En el Portal de Azure AD, en la página de integración de aplicaciones de **Boomi**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-boomi-tutorial/IC790824.png "Configurar inicio de sesión único")

2.  En la página **¿Cómo desea que los usuarios inicien sesión en Boomi?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y luego haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-boomi-tutorial/IC790825.png "Configurar inicio de sesión único")

3.  En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto de **URL de respuesta de Boomi**, escriba la **Dirección URL de inicio de sesión de Boomi AtomSphere** (por ej.: "*https://platform.boomi.com/sso/AccountName/saml*”)) y haga clic en **Siguiente**.

    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-boomi-tutorial/IC790826.png "Configurar dirección URL de la aplicación")

4.  En la página **Configurar inicio de sesión único en Boomi**, para descargar el certificado, haga clic en **Descargar certificado** y luego guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-boomi-tutorial/IC790827.png "Configurar inicio de sesión único")

5.  En otra ventana del explorador web, inicie sesión como administrador en el sitio de la compañía de Boomi.

6.  En la barra de herramientas de la parte superior, haga clic en el nombre de su empresa y en **Configuración**.

    ![Configuración](./media/active-directory-saas-boomi-tutorial/IC790828.png "Configuración")

7.  Haga clic en **Opciones de SSO**.

    ![Opciones SSO](./media/active-directory-saas-boomi-tutorial/IC790829.png "Opciones SSO")

8.  En la sección **Opciones de inicio de sesión único**, siga estos pasos:

    ![Opciones de inicio de sesión único](./media/active-directory-saas-boomi-tutorial/IC790830.png "Opciones de inicio de sesión único")

    1.  Seleccione **Habilitar inicio de sesión único de SAML**.
    2.  Haga clic en **Importar** para cargar el certificado descargado.
    3.  En el Portal de Azure, en la página de diálogo **Configurar inicio de sesión único en Boomi**, copie el valor de **Dirección URL de inicio de sesión remoto** y péguelo en el cuadro de texto **URL de inicio de sesión del proveedor de identidades**.
    4.  Como **Ubicación del id. de federación**, seleccione **El id. de federación está en el elemento NameID de Subject**.
    5.  Haga clic en **Guardar**.

9.  En el Portal de Azure AD, seleccione la confirmación de configuración de inicio de sesión único y, a continuación, haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-boomi-tutorial/IC775560.png "Configurar inicio de sesión único")
##Configuración del aprovisionamiento de usuario

Para permitir que los usuarios de Azure AD inicien sesión en Boomi, tienen que aprovisionarse en Boomi. En el caso de Boomi, el aprovisionamiento es una tarea manual.

###Siga estos pasos para configurar el aprovisionamiento de usuario:

1.  Inicie sesión como administrador en el sitio de la compañía de **Boomi**.

2.  Vaya a **Administración de usuarios > Usuarios**.

    ![Usuarios](./media/active-directory-saas-boomi-tutorial/IC790831.png "Usuarios")

3.  Haga clic en **Agregar usuario**.

    ![Agregar usuario](./media/active-directory-saas-boomi-tutorial/IC790832.png "Agregar usuario")

4.  En la página del cuadro de diálogo **Agregar roles de usuario**, realice los siguientes pasos:

    ![Agregar usuario](./media/active-directory-saas-boomi-tutorial/IC790833.png "Agregar usuario")

    1.  Escriba los datos de una cuenta de AAD válida que desee aprovisionar en los cuadros de texto correspondientes: **Nombre**, **Apellido** y **Correo electrónico**.
    2.  Haga clic en Aceptar.

>[AZURE.NOTE]Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Boomi que proporcione Boomi para aprovisionar cuentas de usuario de AAD.

##Asignación de usuarios

Para probar la configuración, debe asignar los usuarios de Azure AD que quiera que usen su aplicación para concederles acceso a ella.

###Para asignar usuarios a Boomi, lleve a cabo los siguientes pasos:

1.  En el Portal de Azure AD, cree una cuenta de prueba.

2.  En la página de integración de aplicaciones de **Boomi **, haga clic en **Asignar usuarios**.

    ![Asignar usuarios](./media/active-directory-saas-boomi-tutorial/IC790834.png "Asignar usuarios")

3.  Seleccione su usuario de prueba, haga clic en **Asignar** y luego en **Sí** para confirmar la asignación.

    ![Sí](./media/active-directory-saas-boomi-tutorial/IC767830.png "Sí")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0121_2016-->