<properties 
    pageTitle="Tutorial: integración de Azure Active Directory con Slack | Microsoft Azure" 
    description="Aprenda a usar Slack con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automático, etc." 
    services="active-directory" 
    authors="markusvi"  
    documentationCenter="na" 
    manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="01/12/2016" 
    ms.author="markvi" />

#Tutorial: Integración de Azure Active Directory con Slack
  
El objetivo de este tutorial es mostrar la integración de Azure y Slack. En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción de Azure válida
-   Una suscripción habilitada para el inicio de sesión único en Slack
  
Después de completar este tutorial, los usuarios de Azure AD que ha asignado a Slack podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de Slack (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
La situación descrita en este tutorial consta de los siguientes bloques de creación:

1.  Habilitación de la integración de aplicaciones en Slack
2.  Configuración del inicio de sesión único
3.  Configuración del aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario](./media/active-directory-saas-slack-tutorial/IC794980.png "Escenario")

##Habilitación de la integración de aplicaciones en Slack
  
El objetivo de esta sección es describir cómo se habilita la integración de aplicaciones en Slack.

###Siga estos pasos para habilitar la integración de aplicaciones en Slack:

1.  En el panel de navegación izquierdo del Portal de administración de Azure, haga clic en **Active Directory**.

    ![Active Directory](./media/active-directory-saas-slack-tutorial/IC700993.png "Active Directory")

2.  En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3.  Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

    ![Aplicaciones](./media/active-directory-saas-slack-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación](./media/active-directory-saas-slack-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Agregar una aplicación de la galería](./media/active-directory-saas-slack-tutorial/IC749322.png "Agregar una aplicación de la galería")

6.  En el **cuadro de búsqueda**, escriba **Slack**.

    ![Galería de aplicaciones](./media/active-directory-saas-slack-tutorial/IC794981.png "Galería de aplicaciones")

7.  En el panel de resultados, seleccione **Slack** y luego haga clic en **Completar** para agregar la aplicación.

    ![Escenario](./media/active-directory-saas-slack-tutorial/IC796925.png "Escenario")

##Configuración del inicio de sesión único
  
El objetivo de esta sección es describir cómo se habilita la autenticación de los usuarios en Slack con su cuenta de Azure AD a través de la federación basada en el protocolo SAML. Como parte de este procedimiento, es necesario crear un archivo de certificado codificado en base 64. Si no está familiarizado con este procedimiento, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o)

###Siga estos pasos para configurar el inicio de sesión único:

1.  En el Portal de Azure AD, en la página de integración de aplicaciones de **Slack**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-slack-tutorial/IC794982.png "Configurar inicio de sesión único")

2.  En la página **¿Cómo desea que los usuarios inicien sesión en Slack?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y luego haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-slack-tutorial/IC794983.png "Configurar inicio de sesión único")

3.  En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto de **URL de inicio de sesión de Slack**, escriba la dirección URL del inquilino de Slack (por ejemplo, "**https://azuread.slack.com*")) y haga clic en **Siguiente**.

    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-slack-tutorial/IC794984.png "Configurar dirección URL de la aplicación")

4.  En la página **Configurar inicio de sesión único en Slack**, para descargar el certificado, haga clic en **Descargar certificado** y luego guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-slack-tutorial/IC794985.png "Configurar inicio de sesión único")

5.  En otra ventana del explorador web, inicie sesión en su sitio de la compañía de Slack como administrador.

6.  Vaya a **Microsoft Azure AD > Configuración del equipo**.

    ![Configuración del equipo](./media/active-directory-saas-slack-tutorial/IC794986.png "Configuración del equipo")

7.  En la sección **Configuración del equipo**, haga clic en la pestaña **Autenticación** y luego haga clic en **Cambiar configuración**.

    ![Configuración del equipo](./media/active-directory-saas-slack-tutorial/IC794987.png "Configuración del equipo")

8.  En el cuadro de diálogo **Configuración de la autenticación SAML**, realice los pasos siguientes:

    ![Configuración de SAML](./media/active-directory-saas-slack-tutorial/IC794988.png "Configuración de SAML")

    1.  En el portal de Azure, en la página de diálogo **Configurar inicio de sesión único en Slack**, copie el valor de **Dirección URL de SAML SSO** y péguelo en el cuadro de texto **Punto de conexión de SAML 2.0 (HTTP)**.
    2.  En el portal de Azure, en la página de diálogo **Configurar inicio de sesión único en Slack**, copie el valor de **URL del emisor** y péguelo en el cuadro de texto **Emisor del proveedor de identidades**.
    3.  Cree un archivo **codificado en base 64** a partir del certificado descargado.
    
        >[AZURE.TIP]Para obtener más información, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

    4.  Abra el certificado codificado en base 64 en el Bloc de notas, copie su contenido en el portapapeles y luego péguelo en el cuadro de texto **Certificado público**.
    5.  Anule la selección de **Permitir a los usuarios cambiar su dirección de correo electrónico**.
    6.  Seleccione **Permitir a los usuarios elegir su propio nombre de usuario**.
    7.  Como **La autenticación para su equipo debe usarse por**, seleccione **Es opcional**.
    8.  Haga clic en **Guardar configuración**.

9.  En el Portal de Azure AD, seleccione la confirmación de configuración de inicio de sesión único y, a continuación, haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-slack-tutorial/IC794989.png "Configurar inicio de sesión único")

##Configuración del aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD inicien sesión en Slack, deben aprovisionarse en Slack.
  
No hay elemento de acción para que configure el aprovisionamiento de usuarios en Slack. Cuando un usuario asignado intente iniciar sesión en Slack, se creará automáticamente una cuenta de Slack si es necesario.

##Asignación de usuarios
  
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

###Para asignar usuarios a Slack, lleve a cabo los siguientes pasos:

1.  En el portal de Azure AD, cree una cuenta de prueba.

2.  En la página de integración de la aplicación **Slack**, haga clic en **Asignar usuarios**.

    ![Asignar usuarios](./media/active-directory-saas-slack-tutorial/IC794990.png "Asignar usuarios")

3.  Seleccione su usuario de prueba, haga clic en **Asignar** y luego en **Sí** para confirmar la asignación.

    ![Sí](./media/active-directory-saas-slack-tutorial/IC767830.png "Sí")
  
Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0114_2016-->