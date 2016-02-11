<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con LogicMonitor | Microsoft Azure" 
    description="Aprenda a usar LogicMonitor con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc." 
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

#Tutorial: Integración de Azure Active Directory con LogicMonitor
  
El objetivo de este tutorial es mostrar la integración de Azure y LogicMonitor. En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción de Azure válida
-   Un inquilino de LogicMonitor
  
La situación descrita en este tutorial consta de los siguientes bloques de creación:

1.  Habilitación de la integración de aplicaciones para LogicMonitor
2.  Configuración del inicio de sesión único
3.  Configuración del aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario](./media/active-directory-saas-logicmonitor-tutorial/IC790045.png "Escenario")
##Habilitación de la integración de aplicaciones para LogicMonitor
  
El objetivo de esta sección es describir cómo habilitar la integración de aplicaciones para LogicMonitor.

###Siga estos pasos para habilitar la integración de aplicaciones para LogicMonitor:

1.  En el panel de navegación izquierdo del Portal de administración de Azure, haga clic en **Active Directory**.

    ![Active Directory](./media/active-directory-saas-logicmonitor-tutorial/IC700993.png "Active Directory")

2.  En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3.  Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

    ![Aplicaciones](./media/active-directory-saas-logicmonitor-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación](./media/active-directory-saas-logicmonitor-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Agregar una aplicación de la galería](./media/active-directory-saas-logicmonitor-tutorial/IC749322.png "Agregar una aplicación de la galería")

6.  En el **cuadro de búsqueda**, escriba **logicmonitor**.

    ![Galería de aplicaciones](./media/active-directory-saas-logicmonitor-tutorial/IC790046.png "Galería de aplicaciones")

7.  En el panel de resultados, seleccione **LogicMonitor** y luego haga clic en **Completar** para agregar la aplicación.

    ![LogicMonitor](./media/active-directory-saas-logicmonitor-tutorial/IC790047.png "LogicMonitor")
##Configuración del inicio de sesión único
  
El objetivo de esta sección es describir cómo habilitar la autenticación de usuarios en LogicMonitor con su cuenta de Azure AD mediante federación basada en el protocolo SAML.

###Siga estos pasos para configurar el inicio de sesión único:

1.  En el portal de Azure AD, en la página de integración de aplicaciones de **LogicMonitor **, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-logicmonitor-tutorial/IC790048.png "Configurar inicio de sesión único")

2.  En la página **¿Cómo desea que los usuarios inicien sesión en LogicMonitor?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y luego haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-logicmonitor-tutorial/IC790049.png "Configurar inicio de sesión único")

3.  En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto **URL de inicio de sesión**, escriba la dirección URL que los usuarios utilizan para iniciar sesión en LogicMonitor (por ejemplo: "**http://company.logicmonitor.com*")) y haga clic en **Siguiente**.

    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-logicmonitor-tutorial/IC790050.png "Configurar dirección URL de la aplicación")

4.  En la página **Configuración de inicio de sesión único en LogicMonitor**, haga clic en **Descargar metadatos** y luego guarde el archivo en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-logicmonitor-tutorial/IC790051.png "Configurar inicio de sesión único")

5.  Inicie sesión como administrador en el sitio de la compañía de **LogicMonitor**.

6.  En el menú de la parte superior, haga clic en **Configuración**.

    ![Settings](./media/active-directory-saas-logicmonitor-tutorial/IC790052.png "Settings")

7.  En la barra de navegación del lado izquierdo, haga clic en **Inicio de sesión único**.

    ![Inicio de sesión único](./media/active-directory-saas-logicmonitor-tutorial/IC790053.png "Inicio de sesión único")

8.  En la sección **Configuración del inicio de sesión único**, siga estos pasos:

    ![Configuración de inicio de sesión único](./media/active-directory-saas-logicmonitor-tutorial/IC790054.png "Configuración de inicio de sesión único")

    1.  Seleccione **Habilitar inicio de sesión único**.
    2.  Como **Asignación de rol predeterminado**, seleccione **readonly**.
    3.  Abra el archivo de metadatos descargado en el Bloc de notas y luego pegue el contenido del archivo en el cuadro de texto **Metadatos del proveedor de identidades**.
    4.  Haga clic en **Guardar cambios**.

9.  En el portal de Azure AD, seleccione la confirmación de configuración de inicio de sesión único y luego haga clic en **Completa** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-logicmonitor-tutorial/IC790055.png "Configurar inicio de sesión único")
##Configuración del aprovisionamiento de usuario
  
Para que los usuarios de AAD puedan inician sesión, deben aprovisionarse para la aplicación LogicMonitor con sus nombres de usuario de Azure Active Directory.

###Siga estos pasos para configurar el aprovisionamiento de usuario:

1.  Inicie sesión como administrador en el sitio de la compañía de LogicMonitor.

2.  En el menú de la parte superior, haga clic en **Configuración** y, luego, en **Roles y usuarios**.

    ![Roles y usuarios](./media/active-directory-saas-logicmonitor-tutorial/IC790056.png "Roles y usuarios")

3.  Haga clic en **Agregar**.

4.  En la sección **Agregar una cuenta**, realice estos pasos:

    ![Agregar una cuenta](./media/active-directory-saas-logicmonitor-tutorial/IC790057.png "Agregar una cuenta")

    1.  En los cuadros de texto correspondientes, escriba los valores de **Nombre de usuario**, **Correo electrónico**, **Contraseña** y **Vuelva a escribir contraseña** del usuario de Azure Active Directory que quiera aprovisionar.
    2.  Seleccione **Roles**, **Ver permisos** y **Estado**.
    3.  Haga clic en **Enviar**.

>[AZURE.NOTE]Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de LogicMonitor que proporcione LogicMonitor para aprovisionar cuentas de usuario de Azure Active Directory.

##Asignación de usuarios
  
Para probar la configuración, debe asignar los usuarios de Azure AD que quiera que usen su aplicación para concederles acceso a ella.

###Para asignar usuarios a LogicMonitor, siga estos pasos:

1.  En el Portal de Azure AD, cree una cuenta de prueba.

2.  En la página de integración de aplicaciones de **LogicMonitor**, haga clic en **Asignar usuarios**.

    ![Asignar usuarios](./media/active-directory-saas-logicmonitor-tutorial/IC790058.png "Asignar usuarios")

3.  Seleccione su usuario de prueba, haga clic en **Asignar** y luego en **Sí** para confirmar la asignación.

    ![Sí](./media/active-directory-saas-logicmonitor-tutorial/IC767830.png "Sí")
  
Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0121_2016-->