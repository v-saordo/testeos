<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con Replicon | Microsoft Azure" 
    description="Aprenda cómo usar Replicon con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc." 
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

#Tutorial: Integración de Azure Active Directory con Replicon
  
El objetivo de este tutorial es mostrar la integración de Azure y Replicon. En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción de Azure válida
-   Un inquilino de Replicon
  
Después de completar este tutorial, los usuarios de Azure AD que ha asignado a Replicon podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía Replicon (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
La situación descrita en este tutorial consta de los siguientes bloques de creación:

1.  Habilitación de la integración de aplicaciones para Replicon
2.  Configuración del inicio de sesión único
3.  Configuración del aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario](./media/active-directory-saas-replicon-tutorial/IC777798.png "Escenario")
##Habilitación de la integración de aplicaciones para Replicon
  
El objetivo de esta sección es describir cómo habilitar la integración de aplicaciones para Replicon.

###Siga estos pasos para habilitar la integración de aplicaciones para Replicon:

1.  En el panel de navegación izquierdo del Portal de administración de Azure, haga clic en **Active Directory**.

    ![Active Directory](./media/active-directory-saas-replicon-tutorial/IC700993.png "Active Directory")

2.  En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3.  Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

    ![Aplicaciones](./media/active-directory-saas-replicon-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación](./media/active-directory-saas-replicon-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Agregar una aplicación de la galería](./media/active-directory-saas-replicon-tutorial/IC749322.png "Agregar una aplicación de la galería")

6.  En el **cuadro de búsqueda**, escriba **Replicon**.

    ![Galería de aplicaciones](./media/active-directory-saas-replicon-tutorial/IC777799.png "Galería de aplicaciones")

7.  En el panel de resultados, seleccione **Replicon** y luego haga clic en **Completar** para agregar la aplicación.

    ![Replicon](./media/active-directory-saas-replicon-tutorial/IC777800.png "Replicon")
##Configuración del inicio de sesión único
  
El objetivo de esta sección es describir cómo habilitar usuarios para que se autentiquen en Replicon con su cuenta de Azure AD a través de la federación basada en el protocolo SAML.

###Siga estos pasos para configurar el inicio de sesión único:

1.  En el Portal de Azure AD, en la página de integración de aplicaciones de **Replicon**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-replicon-tutorial/IC777801.png "Configurar inicio de sesión único")

2.  En la página **¿Cómo desea que los usuarios inicien sesión en Replicon?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y luego haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-replicon-tutorial/IC777802.png "Configurar inicio de sesión único")

3.  En la página **Configurar dirección URL de la aplicación**, realice los pasos siguientes:

    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-replicon-tutorial/IC777803.png "Configurar dirección URL de la aplicación")

    1.  En el cuadro de texto **URL de inicio de sesión de Replicon**, escriba la dirección URL de inquilino de Replicon (por ejemplo, *https://na2.replicon.com/company/saml2/sp-sso/post*).
    2.  En el cuadro de texto **URL de respuesta de Replicon**, escriba su dirección URL de **AssertionConsumerService** de Replicon (por ejemplo, *https://global.replicon.com/!/saml2/company/sso/post*).

        >[AZURE.NOTE]Puede obtener la dirección URL de los metadatos de Replicon en: **https://global.replicon.com/!/saml2/\<SuClaveCompañía>**.

    3.  Haga clic en **Siguiente**.

4.  En la página **Configurar inicio de sesión único en Replicon**, para descargar los metadatos, haga clic en **Descargar metadatos** y, a continuación, guarde los metadatos en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-replicon-tutorial/IC777804.png "Configurar inicio de sesión único")

5.  En otra ventana del explorador web, inicie sesión en como administrador en el sitio de Replicon de la compañía.

6.  Lleve a cabo los siguientes pasos para configurar SAML 2.0:

    ![Habilitar autenticación SAML](./media/active-directory-saas-replicon-tutorial/IC777805.png "Habilitar autenticación SAML")

    1.  Para mostrar el cuadro de diálogo **Habilitar autenticación de SAML2**, agregue lo siguiente a la URL después de su clave de compañía:**/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2** Lo siguiente muestra el esquema de la dirección URL completa:**https://na2.replicon.com/\< SuClaveCompañía> /services/SecurityService1.svc/help/test/EnableSAMLAuthentication2**.
    2.  Haga clic en **+** para expandir la sección **v20Configuration**.
    3.  Haga clic en **+** para expandir la sección **metaDataConfiguration**.
    4.  Haga clic en **Elegir archivo** para seleccionar el archivo XML de metadatos del proveedor de identidades y haga clic en **Enviar**.

7.  En el portal de Azure AD, seleccione la confirmación de configuración de inicio de sesión único y luego haga clic en **Completa** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-replicon-tutorial/IC778418.png "Configurar inicio de sesión único")
##Configuración del aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD inicien sesión en Replicon, deben aprovisionarse en Replicon. En el caso de Replicon, el aprovisionamiento es una tarea manual.

###Siga estos pasos para configurar el aprovisionamiento de usuario:

1.  En una ventana del explorador web, inicie sesión en como administrador en el sitio de Replicon de la compañía.

2.  Vaya a **Administración > Usuarios**.

    ![Usuarios](./media/active-directory-saas-replicon-tutorial/IC777806.png "Usuarios")

3.  Haga clic en **+Agregar usuario**.

    ![Agregar usuario](./media/active-directory-saas-replicon-tutorial/IC777807.png "Agregar usuario")

4.  En la sección **Perfil de usuario**, lleve a cabo estos pasos:

    ![Perfil de usuario](./media/active-directory-saas-replicon-tutorial/IC777808.png "Perfil de usuario")

    1.  En el cuadro de texto **Nombre de inicio de sesión**, escriba la dirección de correo electrónico del usuario de Azure AD que quiera aprovisionar.
    2.  Como **Tipo de autenticación**, seleccione **SSO**.
    3.  En el cuadro de texto **Departamento**, escriba el departamento del usuario.
    4.  Como **Tipo de empleado**, seleccione **Administrador**.
    5.  Haga clic en **Guardar perfil de usuario**.

>[AZURE.NOTE]Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Replicon que proporcione Replicon para aprovisionar cuentas de usuario de AAD.

##Asignación de usuarios
  
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

###Para asignar usuarios a Replicon, lleve a cabo los siguientes pasos:

1.  En el portal de Azure AD, cree una cuenta de prueba.

2.  En la página de integración de aplicaciones **Replicon **, haga clic en **Asignar usuarios**.

    ![Asignar usuarios](./media/active-directory-saas-replicon-tutorial/IC777809.png "Asignar usuarios")

3.  Seleccione su usuario de prueba, haga clic en **Asignar** y luego en **Sí** para confirmar la asignación.

    ![Sí](./media/active-directory-saas-replicon-tutorial/IC767830.png "Sí")
  
Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

<!----HONumber=AcomDC_0114_2016-->
