<properties 
    pageTitle="Tutorial: integración de Azure Active Directory con FreshService | Microsoft Azure" 
    description="Aprenda a usar FreshService con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automático, etc." 
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

#Tutorial: integración de Azure Active Directory con FreshService
  
El objetivo de este tutorial es mostrar la integración de Azure y FreshService. En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción de Azure válida
-   Una suscripción habilitada para el inicio de sesión único en FreshService
  
Después de completar este tutorial, los usuarios de Azure AD que haya asignado a FreshService podrán realizar un inicio de sesión único en la aplicación desde [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
La situación descrita en este tutorial consta de los siguientes bloques de creación:

1.  Habilitación de la integración de aplicaciones para FreshService
2.  Configuración del inicio de sesión único
3.  Configuración del aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario](./media/active-directory-saas-freshservice-tutorial/IC790807.png "Escenario")
##Habilitación de la integración de aplicaciones para FreshService
  
El objetivo de esta sección es describir cómo se habilita la integración de aplicaciones para FreshService.

###Siga estos pasos para habilitar la integración de aplicaciones para FreshService:

1.  En el panel de navegación izquierdo del Portal de administración de Azure, haga clic en **Active Directory**.

    ![Active Directory](./media/active-directory-saas-freshservice-tutorial/IC700993.png "Active Directory")

2.  En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3.  Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

    ![Aplicaciones](./media/active-directory-saas-freshservice-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación](./media/active-directory-saas-freshservice-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Agregar una aplicación de la galería](./media/active-directory-saas-freshservice-tutorial/IC749322.png "Agregar una aplicación de la galería")

6.  En el **cuadro de búsqueda**, escriba **FreshService**.

    ![Galería de aplicaciones](./media/active-directory-saas-freshservice-tutorial/IC790808.png "Galería de aplicaciones")

7.  En el panel de resultados, seleccione **FreshService** y luego haga clic en **Completar** para agregar la aplicación.

    ![Freshservice](./media/active-directory-saas-freshservice-tutorial/IC790809.png "Freshservice")
##Configuración del inicio de sesión único
  
El objetivo de esta sección es describir cómo permitir a los usuarios autenticarse en FreshService con su cuenta de Azure AD mediante federación basada en el protocolo SAML. La configuración de un inicio de sesión único para FreshService requiere la recuperación de un valor de huella digital de un certificado. Si no está familiarizado con este procedimiento, consulte [Recuperación del valor de huella digital de un certificado](http://youtu.be/YKQF266SAxI).

###Siga estos pasos para configurar el inicio de sesión único:

1.  En el Portal de Azure AD, en la página de integración de aplicaciones de **FreshService**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-freshservice-tutorial/IC790810.png "Configurar inicio de sesión único")

2.  En la página **¿Cómo desea que los usuarios inicien sesión en FreshService?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y luego haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-freshservice-tutorial/IC790811.png "Configurar inicio de sesión único")

3.  En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto **URL de inicio de sesión de FreshService**, escriba la dirección URL que usan los usuarios para iniciar sesión en su aplicación Freshdesk (por ejemplo: "**http://democompany.freshservice.com/*")) y luego haga clic en **Siguiente**.

    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-freshservice-tutorial/IC790812.png "Configurar dirección URL de la aplicación")

4.  En la página **Configurar inicio de sesión único en FreshService**, para descargar el certificado, haga clic en **Descargar certificado** y luego guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-freshservice-tutorial/IC790813.png "Configurar inicio de sesión único")

5.  En otra ventana del explorador web, inicie sesión en el sitio de la compañía de FreshService como administrador.

6.  En el menú de la parte superior, haga clic en **Administrador**.

    ![Administrador](./media/active-directory-saas-freshservice-tutorial/IC790814.png "Administrador")

7.  En el **Portal del cliente**, haga clic en **Seguridad**.

    ![Seguridad](./media/active-directory-saas-freshservice-tutorial/IC790815.png "Seguridad")

8.  En la sección **Seguridad**, realice estos pasos:

    ![Inicio de sesión único](./media/active-directory-saas-freshservice-tutorial/IC790816.png "Inicio de sesión único")

    1.  Conmute **Inicio de sesión único** a Activado.
    2.  Seleccione **Inicio de sesión único de SAML**.
    3.  En el Portal de Azure, en la página de diálogo **Configurar inicio de sesión único en FreshService**, copie el valor de **Dirección URL de inicio de sesión remoto** y péguelo en el cuadro de texto **Dirección URL de inicio de sesión de SAML**.
    4.  En el Portal de Azure, en la página de diálogo **Configurar inicio de sesión único en FreshService**, copie el valor de **Dirección URL de cierre de sesión remoto** y péguelo en el cuadro de texto **Dirección URL de cierre de sesión**.
    5.  Copie el valor de **Huella digital** del certificado exportado y luego péguelo en el cuadro de texto **Huella digital de certificado de seguridad**.
    
        >[AZURE.TIP]Para obtener más información, consulte [Recuperación del valor de huella digital de un certificado](http://youtu.be/YKQF266SAxI).

9.  En el portal de Azure AD, seleccione la confirmación de configuración de inicio de sesión único y luego haga clic en **Completa** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-freshservice-tutorial/IC790817.png "Configurar inicio de sesión único")
##Configuración del aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD inicien sesión en FreshService, deben aprovisionarse en FreshService. En el caso de FreshService, el aprovisionamiento es una tarea manual.

###Para aprovisionar cuentas de usuario, realice estos pasos:

1.  Inicie sesión en el sitio de la compañía de **FreshService** como administrador.

2.  En el menú de la parte superior, haga clic en **Administrador**.

    ![Administrador](./media/active-directory-saas-freshservice-tutorial/IC790814.png "Administrador")

3.  En la sección **Administración de usuarios**, haga clic en **Solicitantes**.

    ![Solicitantes](./media/active-directory-saas-freshservice-tutorial/IC790818.png "Solicitantes")

4.  Haga clic en **Nuevo solicitante**.

    ![Nuevos solicitantes](./media/active-directory-saas-freshservice-tutorial/IC790819.png "Nuevos solicitantes")

5.  En la sección **New Requester** (Nuevo solicitante), lleve a cabo estos pasos:

    ![Nuevo solicitante](./media/active-directory-saas-freshservice-tutorial/IC790820.png "Nuevo solicitante")

    1.  Escriba los atributos **Nombre** y **Correo electrónico** de la cuenta de Azure Active Directory válida que quiera aprovisionar en los cuadros de texto relacionados.
    2.  Haga clic en **Guardar**.

    >[AZURE.NOTE]El titular de la cuenta de Azure Active Directory recibirá un mensaje de correo electrónico con un vínculo para confirmar la cuenta antes de que se active.

>[AZURE.NOTE]Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de FreshService que proporcione FreshService para aprovisionar cuentas de usuario de AAD.

##Asignación de usuarios
  
Para probar la configuración, tiene que conceder acceso, mediante su asignación, a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

###Para asignar usuarios a FreshService, lleve a cabo los siguientes pasos:

1.  En el Portal de Azure AD, cree una cuenta de prueba.

2.  En la página de integración de aplicaciones de **FreshService **, haga clic en **Asignar usuarios**.

    ![Asignar usuarios](./media/active-directory-saas-freshservice-tutorial/IC790821.png "Asignar usuarios")

3.  Seleccione su usuario de prueba, haga clic en **Asignar** y luego en **Sí** para confirmar la asignación.

    ![Sí](./media/active-directory-saas-freshservice-tutorial/IC767830.png "Sí")
  
Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0121_2016-->