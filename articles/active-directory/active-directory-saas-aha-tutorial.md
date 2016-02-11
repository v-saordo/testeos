<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con Aha! | Microsoft Azure" 
    description="Aprenda a usar Aha! con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc." 
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

#Tutorial: Integración de Azure Active Directory con Aha!

El objetivo de este tutorial es mostrar la integración de Azure y Aha!. En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción de Azure válida
-   Una suscripción habilitada para el inicio de sesión único en Aha!

Después de completar este tutorial, los usuarios de Azure AD que ha asignado a Aha! podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de Aha! (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md)

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1.  Habilitación de la integración de aplicaciones para Aha!
2.  Configuración del inicio de sesión único
3.  Configuración del aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario](./media/active-directory-saas-aha-tutorial/IC798944.png "Escenario")
##Habilitación de la integración de aplicaciones para Aha!

El objetivo de esta sección es describir cómo se habilita la integración de aplicaciones para Aha!.

###Siga estos pasos para habilitar la integración de aplicaciones para Aha!:

1.  En el panel de navegación izquierdo del Portal de administración de Azure, haga clic en **Active Directory**.

    ![Active Directory](./media/active-directory-saas-aha-tutorial/IC700993.png "Active Directory")

2.  En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3.  Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

    ![Aplicaciones](./media/active-directory-saas-aha-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación](./media/active-directory-saas-aha-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Agregar una aplicación de la galería](./media/active-directory-saas-aha-tutorial/IC749322.png "Agregar una aplicación de la galería")

6.  En el **cuadro de búsqueda**, escriba **Aha!**.

    ![Galería de aplicaciones](./media/active-directory-saas-aha-tutorial/IC798945.png "Galería de aplicaciones")

7.  En el panel de resultados, seleccione **Aha!** y luego haga clic en **Completar** para agregar la aplicación.

    ![Aha!](./media/active-directory-saas-aha-tutorial/IC802746.png "Aha!")
##Configuración del inicio de sesión único

El objetivo de esta sección es describir cómo habilitar usuarios para que se autentiquen en Aha! con su cuenta de Azure AD mediante federación basada en el protocolo SAML.

###Siga estos pasos para configurar el inicio de sesión único:

1.  En el Portal de Azure AD, en la página de integración de aplicaciones de **Aha!**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-aha-tutorial/IC798946.png "Configurar inicio de sesión único")

2.  En la página **¿Cómo desea que los usuarios inicien sesión en Aha!?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y luego haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-aha-tutorial/IC798947.png "Configurar inicio de sesión único")

3.  En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto **URL de inicio de sesión de Aha!**, escriba la dirección URL que los usuarios usan para iniciar sesión en la aplicación Aha! (por ejemplo: "**https://company.aha.io/session/new*") y haga clic en **Siguiente**.

    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-aha-tutorial/IC798948.png "Configurar dirección URL de la aplicación")

4.  En la página **Configurar inicio de sesión único en Aha!**, para descargar el archivo de metadatos, haga clic en **Descargar metadatos** y luego guarde el archivo de metadatos localmente en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-aha-tutorial/IC798949.png "Configurar inicio de sesión único")

5.  En otra ventana del explorador web, inicie sesión en el sitio de la compañía de Aha! como administrador.

6.  En el menú de la parte superior, haga clic en **Configuración**.

    ![Settings](./media/active-directory-saas-aha-tutorial/IC798950.png "Settings")

7.  Haga clic en **Cuenta**.

    ![Perfil](./media/active-directory-saas-aha-tutorial/IC798951.png "Perfil")

8.  Haga clic en **Seguridad e inicio de sesión único**.

    ![Security and single sign-on (Seguridad e inicio de sesión único)](./media/active-directory-saas-aha-tutorial/IC798952.png "Security and single sign-on (Seguridad e inicio de sesión único)")

9.  En la sección **Inicio de sesión único**, en **Proveedor de identidad**, seleccione **SAML2.0**.

    ![Security and single sign-on (Seguridad e inicio de sesión único)](./media/active-directory-saas-aha-tutorial/IC798953.png "Security and single sign-on (Seguridad e inicio de sesión único)")

10. Siga estos pasos en la página de configuración **Inicio de sesión único**:

    ![Inicio de sesión único](./media/active-directory-saas-aha-tutorial/IC798954.png "Inicio de sesión único")

    1.  En el cuadro de texto **Name** (Nombre), escriba el nombre de la configuración.
    2.  Para **Configurar mediante**, seleccione **Archivo de metadatos**.
    3.  Para cargar el archivo de metadatos descargado, haga clic en **Examinar**.
    4.  Haga clic en **Actualizar**.

11. En el portal de Azure AD, seleccione la confirmación de configuración de inicio de sesión único y luego haga clic en **Completa** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-aha-tutorial/IC798955.png "Configurar inicio de sesión único")
##Configuración del aprovisionamiento de usuario

Para permitir que los usuarios de Azure AD inicien sesión en Aha!, tienen que aprovisionarse en Aha!. En el caso de Aha!, el aprovisionamiento es una tarea automatizada. No hay ningún elemento de acción para usted.
  
Los usuarios se crean automáticamente si es necesario durante el primer intento de inicio de sesión único.

>[AZURE.NOTE]Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Aha! que proporcione Aha! para aprovisionar cuentas de usuario de AAD.

##Asignación de usuarios

Para probar la configuración, tiene que conceder acceso, mediante su asignación, a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

###Para asignar usuarios a Aha!, lleve a cabo los siguientes pasos:

1.  En el Portal de Azure AD, cree una cuenta de prueba.

2.  En la página de integración de aplicaciones de **Aha!**, haga clic en **Asignar usuarios**.

    ![Asignar usuarios](./media/active-directory-saas-aha-tutorial/IC798956.png "Asignar usuarios")

3.  Seleccione su usuario de prueba, haga clic en **Asignar** y luego en **Sí** para confirmar la asignación.

    ![Sí](./media/active-directory-saas-aha-tutorial/IC767830.png "Sí")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0121_2016-->