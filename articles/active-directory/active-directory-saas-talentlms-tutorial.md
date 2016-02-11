<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con TalentLMS | Microsoft Azure" 
    description="Aprenda cómo usar TalentLMS con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc." 
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

#Tutorial: integración de Azure Active Directory con TalentLMS
  
El objetivo de este tutorial es mostrar la integración de Azure y TalentLMS. En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción de Azure válida
-   Un inquilino de TalentLMS
  
Después de completar este tutorial, los usuarios de Azure AD que ha asignado a TalentLMS podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de TalentLMS (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
La situación descrita en este tutorial consta de los siguientes bloques de creación:

1.  Habilitación de la integración de aplicaciones para TalentLMS
2.  Configuración del inicio de sesión único
3.  Configuración del aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario](./media/active-directory-saas-talentlms-tutorial/IC777289.png "Escenario")

##Habilitación de la integración de aplicaciones para TalentLMS
  
El objetivo de esta sección es describir cómo se habilita la integración de aplicaciones en TalentLMS.

###Siga estos pasos para habilitar la integración de aplicaciones en TalentLMS:

1.  En el panel de navegación izquierdo del Portal de administración de Azure, haga clic en **Active Directory**.

    ![Active Directory](./media/active-directory-saas-talentlms-tutorial/IC700993.png "Active Directory")

2.  En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3.  Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

    ![Aplicaciones](./media/active-directory-saas-talentlms-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación](./media/active-directory-saas-talentlms-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Agregar una aplicación de la galería](./media/active-directory-saas-talentlms-tutorial/IC749322.png "Agregar una aplicación de la galería")

6.  En el **cuadro de búsqueda**, escriba **TalentLMS**.

    ![Galería de aplicaciones](./media/active-directory-saas-talentlms-tutorial/IC777290.png "Galería de aplicaciones")

7.  En el panel de resultados, seleccione **TalentLMS** y luego, haga clic en **Completar** para agregar la aplicación.

    ![TalentLMS](./media/active-directory-saas-talentlms-tutorial/IC777291.png "TalentLMS")

##Configuración del inicio de sesión único
  
El objetivo de esta sección es describir cómo se habilita la autenticación de los usuarios en TalentLMS con su cuenta de Azure AD usando el protocolo SAML basado en la federación. La configuración de un inicio de sesión único para TalentLMS requiere la recuperación de un valor de huella digital de un certificado. Si no está familiarizado con este procedimiento, consulte [Recuperación del valor de huella digital de un certificado](http://youtu.be/YKQF266SAxI).

###Siga estos pasos para configurar el inicio de sesión único:

1.  En el Portal de Azure AD, en la página de integración de aplicaciones de **TalentLMS**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-talentlms-tutorial/IC777292.png "Configurar inicio de sesión único")

2.  En la página **¿Cómo desea que los usuarios inicien sesión en TalentLMS?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y luego haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-talentlms-tutorial/IC777293.png "Configurar inicio de sesión único")

3.  En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto de **URL de inicio de sesión de TalentLMS**, escriba su dirección URL con el siguiente patrón "*https://\<nombre-inquilino>.TalentLMSapp.com*" y luego haga clic en **Siguiente**.

    ![Dirección URL de inicio de sesión](./media/active-directory-saas-talentlms-tutorial/IC777294.png "Dirección URL de inicio de sesión")

4.  En la página **Configurar inicio de sesión único en TalentLMS**, para descargar el certificado, haga clic en **Descargar certificado** y luego guarde el archivo de certificado localmente como **c:TalentLMS.cer**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-talentlms-tutorial/IC777295.png "Configurar inicio de sesión único")

5.  En otra ventana del explorador web, inicie sesión en su sitio de la compañía de TalentLMS como administrador.

6.  En la sección **Cuenta y configuración**, haga clic en la pestaña **Usuarios**.

    ![Cuenta y configuración](./media/active-directory-saas-talentlms-tutorial/IC777296.png "Cuenta y configuración")

7.  Haga clic en **Inicio de sesión único (SSO)**.

8.  En la sección Inicio de sesión único, siga estos pasos:

    ![Inicio de sesión único](./media/active-directory-saas-talentlms-tutorial/IC777297.png "Inicio de sesión único")

    1.  En la lista **Tipo de integración de SSO**, seleccione **SAML 2.0**.
    2.  En el portal de Azure, en la página del cuadro de diálogo **Configurar inicio de sesión único en TalentLMS**, copie el valor del **Id. de proveedor de identidades** y luego péguelo en el cuadro de texto **Id. de proveedor de identidades (IdP)**.
    3.  Copie el valor de **Huella digital** del certificado exportado y péguelo en el cuadro de texto **Huella digital del certificado**.

        >[AZURE.TIP]Para obtener más información, consulte [Recuperación del valor de huella digital de un certificado](http://youtu.be/YKQF266SAxI).

    4.  En el Portal de Azure, en la página de diálogo **Configurar inicio de sesión único en TalentLMS**, copie el valor de **Dirección URL de inicio de sesión remoto** y péguelo en el cuadro de texto **Dirección URL de inicio de sesión remota**.
    5.  En el Portal de Azure, en la página de diálogo **Configurar inicio de sesión único en TalentLMS**, copie el valor de **Dirección URL de cierre de sesión remoto** y péguelo en el cuadro de texto **Dirección URL de cierre de sesión remota**.
    6.  En el cuadro de texto **TargetedID**, escriba ****http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name**.
7.  En el cuadro de texto **Nombre**, escriba ****http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.
8.  En el cuadro de texto **Last Name** (Apellido), escriba ****http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**.
9.  En el cuadro de texto **Correo electrónico**, escriba ****http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
10. Haga clic en **Guardar**.

9.  En el Portal de Azure AD, seleccione la confirmación de configuración de inicio de sesión único y, a continuación, haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-talentlms-tutorial/IC777298.png "Configurar inicio de sesión único")

##Configuración del aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD inicien sesión en TalentLMS, deben aprovisionarse en TalentLMS. En el caso de TalentLMS, el aprovisionamiento es una tarea manual.

###Para aprovisionar cuentas de usuario, realice los siguientes pasos:

1.  Inicie sesión en su inquilino de **TalentLMS**.

2.  Haga clic en **Usuarios** y luego en **Agregar usuario**.

3.  En la página del cuadro de diálogo **Agregar usuario**, realice los siguientes pasos:

    ![Agregar usuario](./media/active-directory-saas-talentlms-tutorial/IC777299.png "Agregar usuario")

    1.  Escriba los valores de atributo relacionados de la cuenta de usuario de Azure AD en los siguientes cuadros de texto: **Nombre**, **Apellido**, **Dirección de correo electrónico**.
    2.  Haga clic en **Agregar usuario**.

>[AZURE.NOTE]Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de TalentLMS ofrecida por TalentLMS para aprovisionar cuentas de usuario de AAD.

##Asignación de usuarios
  
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

###Para asignar usuarios a TalentLMS, lleve a cabo los siguientes pasos:

1.  En el portal de Azure AD, cree una cuenta de prueba.

2.  En la página de integración de la aplicación **TalentLMS**, haga clic en **Asignar usuarios**.

    ![Asignar usuarios](./media/active-directory-saas-talentlms-tutorial/IC777300.png "Asignar usuarios")

3.  Seleccione su usuario de prueba, haga clic en **Asignar** y luego en **Sí** para confirmar la asignación.

    ![Sí](./media/active-directory-saas-talentlms-tutorial/IC767830.png "Sí")
  
Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0114_2016-->