<properties 
    pageTitle="Tutorial: integración de Azure Active Directory con Adaptive Suite | Microsoft Azure"
    description="Aprenda a usar Adaptive Suite con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc." 
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

#Tutorial: Integración de Azure Active Directory con Adaptive Suite

El objetivo de este tutorial es mostrar la integración de Azure y Adaptive Suite. En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción de Azure válida
-   Un inquilino de Adaptive Suite

Después de completar este tutorial, los usuarios de Azure AD que ha asignado a Adaptive Suite podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de Adaptive Suite (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1.  Habilitación de la integración de aplicaciones para Adaptive Suite
2.  Configuración del inicio de sesión único
3.  Configuración del aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario](./media/active-directory-saas-adaptive-suite-tutorial/IC805637.png "Escenario")
##Habilitación de la integración de aplicaciones para Adaptive Suite

El objetivo de esta sección es describir cómo habilitar la integración de aplicaciones para Adaptive Suite.

###Siga estos pasos para habilitar la integración de aplicaciones para Adaptive Suite:

1.  En el panel de navegación izquierdo del Portal de administración de Azure, haga clic en **Active Directory**.

    ![Active Directory](./media/active-directory-saas-adaptive-suite-tutorial/IC700993.png "Active Directory")

2.  En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3.  Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

    ![Aplicaciones](./media/active-directory-saas-adaptive-suite-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación](./media/active-directory-saas-adaptive-suite-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Agregar una aplicación de la galería](./media/active-directory-saas-adaptive-suite-tutorial/IC749322.png "Agregar una aplicación de la galería")

6.  En el cuadro **Buscar**, escriba **Adaptive Suite**.

    ![Galería de aplicaciones](./media/active-directory-saas-adaptive-suite-tutorial/IC805638.png "Galería de aplicaciones")

7.  En el panel de resultados, seleccione **Adaptive Suite** y, a continuación, haga clic en **Completar** para agregar la aplicación.

    ![Adaptive Suite](./media/active-directory-saas-adaptive-suite-tutorial/IC805639.png "Adaptive Suite")
##Configuración del inicio de sesión único

El objetivo de esta sección es describir cómo habilitar usuarios para que se autentiquen en Adaptive Suite con su cuenta de Azure AD mediante federación basada en el protocolo SAML. La configuración de un inicio de sesión único para Adaptive Suite requiere la recuperación de un valor de huella digital de un certificado. Si no está familiarizado con este procedimiento, consulte [Recuperación del valor de huella digital de un certificado](http://youtu.be/YKQF266SAxI).

###Siga estos pasos para configurar el inicio de sesión único:

1.  En el Portal de Azure AD, en la página de integración de aplicaciones de **Adaptive Suite**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-adaptive-suite-tutorial/IC805640.png "Configurar inicio de sesión único")

2.  En la página **¿Cómo desea que los usuarios inicien sesión en Adaptive Suite?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y, a continuación, haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-adaptive-suite-tutorial/IC805641.png "Configurar inicio de sesión único")

3.  En la página **Configurar las opciones de la aplicación**, en el cuadro de texto **URL de respuesta **, escriba su dirección URL con el siguiente patrón "**https://login.adaptiveinsights.com:443/samlsso/RlJFRVRSSUFMMTI3MTE=*" y, a continuación, haga clic en **Siguiente**.

    >[AZURE.NOTE]Puede obtener este valor de la página **SAML SSO Settings** (Configuración SSO de SAML) de Adaptive Suite.

    ![Configurar las opciones de la aplicación](./media/active-directory-saas-adaptive-suite-tutorial/IC805642.png "Configurar las opciones de la aplicación")

4.  En la página **Configurar inicio de sesión único en Adaptive Suite**, para descargar el certificado, haga clic en **Descargar certificado** y, a continuación, guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-adaptive-suite-tutorial/IC805643.png "Configurar inicio de sesión único")

5.  En otra ventana del explorador web, inicie sesión en el sitio de la compañía de Adaptive Suite como administrador.

6.  Vaya a **Admin** (Administración).

    ![Administrador](./media/active-directory-saas-adaptive-suite-tutorial/IC805644.png "Administrador")

7.  En la sección **Users and Roles** (Usuarios y roles), haga clic en **Manage SAML SSO Settings** (Administrar configuración de SSO de SAML).

    ![Manage SAML SSO Settings (Administrar configuración de SSO de SAML)](./media/active-directory-saas-adaptive-suite-tutorial/IC805645.png "Manage SAML SSO Settings (Administrar configuración de SSO de SAML)")

8.  En la página **SAML SSO Settings** (Configuración de SSO de SAML), realice los pasos siguientes:

    ![SAML SSO Settings (Configuración de SSO de) SAML](./media/active-directory-saas-adaptive-suite-tutorial/IC805646.png "SAML SSO Settings (Configuración de SSO de) SAML")

    1.  En el cuadro de texto **Identity provider name** (Nombre del proveedor de identidades), escriba el nombre de la configuración.
    2.  En el Portal de Azure, en la página de diálogo **Configurar inicio de sesión único en Adaptive Suite**, copie el valor de **Id. de entidad ** y péguelo en el cuadro de texto **Id. de entidad de proveedor de identidades**.
    3.  En el Portal de Azure, en la página de diálogo **Configurar inicio de sesión único en Adaptive Suite**, copie el valor de **Dirección URL de inicio de sesión único de SAML** y péguelo en el cuadro de texto **Dirección URL de inicio de sesión único de proveedor de identidades**.
    4.  En el Portal de Azure, en la página de diálogo **Configurar inicio de sesión único en Adaptive Suite**, copie el valor de **Dirección URL de inicio de sesión único de SAML** y péguelo en el cuadro de texto **Dirección URL de cierre de sesión personalizada**.
    5.  Para cargar el certificado descargado, haga clic en **Choose file** (Elegir archivo).
    6.  En **SAML user id** (Id. de usuario de SAML), seleccione **User’s Adaptive Insights user name** (Nombre de usuario de Adaptive Insights del usuario).
    7.  En **SAML user id location** (Ubicación del id. de usuario de SAML), seleccione **User id in NameID of Subject** (Id. de usuario en NameID del tema).
    8.  En **SAML NameID format** (Formato de NameID de SAML), seleccione **Email address** (Dirección de correo electrónico).
    9.  En **Enable SAML** (Habilitar SAML), seleccione **Allow SAML SSO and direct Adaptive Insights login** (Permitir inicio de sesión único de SAML e inicio de sesión directo de Adaptive Insights).
    10. Haga clic en **Guardar**.

9.  En el Portal de Azure AD, seleccione la confirmación de configuración de inicio de sesión único y, a continuación, haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-adaptive-suite-tutorial/IC805647.png "Configurar inicio de sesión único")
##Configuración del aprovisionamiento de usuario

Para permitir que los usuarios de Azure AD inicien sesión en Adaptive Suite, deben aprovisionarse en Adaptive Suite. En el caso de Adaptive Suite, el aprovisionamiento es una tarea manual.

###Siga estos pasos para configurar el aprovisionamiento de usuario:

1.  Inicie sesión en el sitio de la compañía de **Adaptive Suite** como administrador.

2.  Vaya a **Admin** (Administración).

    ![Administrador](./media/active-directory-saas-adaptive-suite-tutorial/IC805644.png "Administrador")

3.  En la sección **Users and Roles** (Usuarios y roles), haga clic en **Add User** (Agregar usuario).

    ![Agregar usuario](./media/active-directory-saas-adaptive-suite-tutorial/IC805648.png "Agregar usuario")

4.  En la sección **Nuevo usuario**, lleve a cabo estos pasos:

    ![Enviar](./media/active-directory-saas-adaptive-suite-tutorial/IC805649.png "Enviar")

    1.  Escriba **Name** (Nombre), **Login** (Inicio de sesión), **Email** (Correo electrónico), **Password** (Contraseña) de un usuario válido de Azure Active Directory que desee aprovisionar en los cuadros de texto relacionados.
    2.  Seleccione un **Role** (rol).
    3.  Haga clic en **Enviar**.

>[AZURE.NOTE]Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Adaptive Suite ofrecida por Adaptive Suite para aprovisionar cuentas de usuario de AAD.

##Asignación de usuarios

Para probar la configuración, tiene que conceder acceso, mediante su asignación, a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

###Para asignar usuarios a Adaptive Suite, lleve a cabo los siguientes pasos:

1.  En el Portal de Azure AD, cree una cuenta de prueba.

2.  En la página de integración de aplicaciones de **Adaptive Suite**, haga clic en **Asignar usuarios**.

    ![Asignar usuarios](./media/active-directory-saas-adaptive-suite-tutorial/IC805650.png "Asignar usuarios")

3.  Seleccione su usuario de prueba, haga clic en **Asignar** y luego en **Sí** para confirmar la asignación.

    ![Sí](./media/active-directory-saas-adaptive-suite-tutorial/IC767830.png "Sí")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0121_2016-->