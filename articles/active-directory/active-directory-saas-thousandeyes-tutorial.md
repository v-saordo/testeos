<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con ThousandEyes | Microsoft Azure" 
    description="Aprenda cómo usar ThousandEyes con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc." 
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

#Tutorial: integración de Azure Active Directory con ThousandEyes
  
El objetivo de este tutorial es mostrar cómo configurar el inicio de sesión único entre Azure Active Directory (Azure AD) y ThousandEyes.
  
En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción de Azure válida
-   Una suscripción habilitada para inicio de sesión único en ThousandEyes
  
Después de completar este tutorial, los usuarios de AAD a los que ha asignado acceso a ThousandEyes podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de ThousandEyes (inicio de sesión iniciado por el proveedor del servicio) o con el Panel de acceso de AAD.

1.  Habilitación de la integración de aplicaciones para ThousandEyes
2.  Configuración del inicio de sesión único
3.  Configuración del aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario](./media/active-directory-saas-thousandeyes-tutorial/IC790059.png "Escenario")

##Habilitación de la integración de aplicaciones para ThousandEyes
  
El objetivo de esta sección es describir cómo se habilita la integración de aplicaciones para ThousandEyes.

###Siga estos pasos para habilitar la integración de aplicaciones para ThousandEyes:

1.  En el panel de navegación izquierdo del Portal de administración de Azure, haga clic en **Active Directory**.

    ![Active Directory](./media/active-directory-saas-thousandeyes-tutorial/IC700993.png "Active Directory")

2.  En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3.  Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

    ![Aplicaciones](./media/active-directory-saas-thousandeyes-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación](./media/active-directory-saas-thousandeyes-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Agregar una aplicación de la galería](./media/active-directory-saas-thousandeyes-tutorial/IC749322.png "Agregar una aplicación de la galería")

6.  En el **cuadro de búsqueda**, escriba **ThousandEyes**.

    ![Galería de aplicaciones](./media/active-directory-saas-thousandeyes-tutorial/IC790060.png "Galería de aplicaciones")

7.  En el panel de resultados, seleccione **ThousandEyes** y luego, haga clic en **Completar** para agregar la aplicación.

    ![ThousandEyes](./media/active-directory-saas-thousandeyes-tutorial/IC790061.png "ThousandEyes")

##Configuración del inicio de sesión único
  
En esta sección se describe cómo se habilita la autenticación de los usuarios en ThousandEyes con su cuenta de Azure Active Directory usando el protocolo SAML basado en la federación.

###Siga estos pasos para configurar el inicio de sesión único:

1.  En el Portal de Azure AD, en la página de integración de aplicaciones de **ThousandEyes**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-thousandeyes-tutorial/IC790062.png "Configurar inicio de sesión único")

2.  En la página **¿Cómo desea que los usuarios inicien sesión en ThousandEyes?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y luego haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-thousandeyes-tutorial/IC790063.png "Configurar inicio de sesión único")

3.  En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto **URL de inicio de sesión de ThousandEyes**, escriba la dirección URL que utilizan los usuarios para iniciar sesión en su aplicación ThousandEyes (por ejemplo: "**https://app.thousandeyes.com/login/sso*")) y luego haga clic en **Siguiente**.

    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-thousandeyes-tutorial/IC790064.png "Configurar dirección URL de la aplicación")

4.  En la página **Configurar inicio de sesión único en ThousandEyes**, para descargar el certificado, haga clic en **Descargar certificado** y luego guarde el archivo de certificado localmente en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-thousandeyes-tutorial/IC790065.png "Configurar inicio de sesión único")

5.  En otra ventana del explorador web, inicie sesión en su sitio de la compañía de **ThousandEyes** como administrador.

6.  En el menú de la parte superior, haga clic en **Configuración**.

    ![Settings](./media/active-directory-saas-thousandeyes-tutorial/IC790066.png "Settings")

7.  Haga clic en **Cuenta**.

    ![Cuenta](./media/active-directory-saas-thousandeyes-tutorial/IC790067.png "Cuenta")

8.  Haga clic en la pestaña **Seguridad y autenticación**.

    ![Seguridad y autenticación](./media/active-directory-saas-thousandeyes-tutorial/IC790068.png "Seguridad y autenticación")

9.  En la sección **Configurar inicio de sesión único** siga los pasos siguientes:

    ![Configurar el inicio de sesión único](./media/active-directory-saas-thousandeyes-tutorial/IC790069.png "Configurar el inicio de sesión único")

    1.  Seleccione **Habilitar inicio de sesión único**.
    2.  En el portal de Microsoft Azure, en la página de diálogo **Configurar inicio de sesión único en ThousandEyes**, copie el valor de **Dirección URL del inicio de sesión remoto** y péguelo en el cuadro de texto **Dirección URL de página de inicio de sesión**.
    3.  En el portal de Microsoft Azure, en la página de diálogo **Configurar inicio de sesión único en ThousandEyes**, copie el valor de **Dirección URL de cierre de sesión remoto** y péguelo en el cuadro de texto **Dirección URL de página de cierre de sesión**.
    4.  En el portal de Microsoft Azure, en la página de diálogo **Configurar inicio de sesión único en ThousandEyes**, copie el valor de **Dirección URL de emisor** y péguelo en el cuadro de texto **Emisor de proveedor de identidades**.
    5.  En el **certificado del proveedor de identidades**haga clic en**Elegir archivo** y luego cargue el certificado que ha descargado del portal de Microsoft Azure.
    6.  Haga clic en **Guardar**.

10. En el Portal de Azure AD, seleccione la confirmación de configuración de inicio de sesión único y, a continuación, haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-thousandeyes-tutorial/IC790070.png "Configurar inicio de sesión único")

##Configuración del aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD inicien sesión en ThousandEyes, deben aprovisionarse en ThousandEyes. En el caso de ThousandEyes, el aprovisionamiento es una tarea manual.

###Para aprovisionar cuentas de usuario a ThousandEyes, realice los siguientes pasos:

1.  Inicie sesión en su sitio de la compañía de ThousandEyes como administrador.

2.  Haga clic en **Configuración**.

    ![Settings](./media/active-directory-saas-thousandeyes-tutorial/IC790066.png "Settings")

3.  Haga clic en **Cuenta**.

    ![Cuenta](./media/active-directory-saas-thousandeyes-tutorial/IC790067.png "Cuenta")

4.  Haga clic en la pestaña **Cuentas y usuarios**.

    ![Cuentas y usuarios](./media/active-directory-saas-thousandeyes-tutorial/IC790073.png "Cuentas y usuarios")

5.  En la sección **Agregar usuarios y cuentas**, lleve a cabo estos pasos:

    ![Agregar cuentas de usuario](./media/active-directory-saas-thousandeyes-tutorial/IC790074.png "Agregar cuentas de usuario")

    1.  Escriba el **Nombre**, el **Correo electrónico** y otros detalles de la cuenta de Azure Active Directory válida que quiere aprovisionar en los cuadros de texto relacionados.
    2.  Haga clic en **Agregar nuevo usuario a la cuenta**.

        >[AZURE.NOTE]El titular de la cuenta de AAD recibirá un mensaje de correo electrónico junto con un vínculo para confirmar y activar la cuenta.

>[AZURE.NOTE]Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de ThousandEyes ofrecida por ThousandEyes para aprovisionar cuentas de usuario de AAD.

##Asignación de usuarios
  
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

###Para asignar usuarios a ThousandEyes, lleve a cabo los siguientes pasos:

1.  En el portal de Azure AD, cree una cuenta de prueba.

2.  En la página de integración de la aplicación **ThousandEyes**, haga clic en **Asignar usuarios**.

    ![Asignar usuarios](./media/active-directory-saas-thousandeyes-tutorial/IC790075.png "Asignar usuarios")

3.  Seleccione su usuario de prueba, haga clic en **Asignar** y luego en **Sí** para confirmar la asignación.

    ![Sí](./media/active-directory-saas-thousandeyes-tutorial/IC767830.png "Sí")
  
Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0114_2016-->