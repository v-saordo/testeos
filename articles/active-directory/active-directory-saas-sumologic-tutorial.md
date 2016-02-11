<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con SumoLogic | Microsoft Azure" 
    description="Aprenda cómo usar SumoLogic con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc." 
    services="active-directory" 
    authors="MarkusVi"  
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

#Tutorial: integración de Azure Active Directory con SumoLogic
  
El objetivo de este tutorial es mostrar la integración de Azure y SumoLogic. En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción de Azure válida
-   Un inquilino de SumoLogic
  
Después de completar este tutorial, los usuarios de Azure AD que asignó a SumoLogic podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de SumoLogic (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
La situación descrita en este tutorial consta de los siguientes bloques de creación:

1.  Habilitación de la integración de aplicaciones para SumoLogic
2.  Configuración del inicio de sesión único
3.  Configuración del aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario](./media/active-directory-saas-sumologic-tutorial/IC778549.png "Escenario")

##Habilitación de la integración de aplicaciones para SumoLogic
  
El objetivo de esta sección es describir cómo habilitar la integración de las aplicaciones para SumoLogic.

###Siga estos pasos para habilitar la integración de aplicaciones en SumoLogic:

1.  En el panel de navegación izquierdo del Portal de administración de Azure, haga clic en **Active Directory**.

    ![Active Directory](./media/active-directory-saas-sumologic-tutorial/IC700993.png "Active Directory")

2.  En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3.  Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

    ![Aplicaciones](./media/active-directory-saas-sumologic-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación](./media/active-directory-saas-sumologic-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Agregar una aplicación de la galería](./media/active-directory-saas-sumologic-tutorial/IC749322.png "Agregar una aplicación de la galería")

6.  En el **cuadro de búsqueda**, escriba **sumologic**.

    ![Galería de aplicaciones](./media/active-directory-saas-sumologic-tutorial/IC778550.png "Galería de aplicaciones")

7.  En el panel de resultados, seleccione **SumoLogic** y luego haga clic en **Completar** para agregar la aplicación.

    ![SumoLogic](./media/active-directory-saas-sumologic-tutorial/IC778551.png "SumoLogic")

##Configuración del inicio de sesión único
  
El objetivo de esta sección es describir cómo se habilita la autenticación de los usuarios en SumoLogic con su cuenta de Azure AD usando el protocolo SAML basado en la federación. Como parte de este procedimiento, es necesario cargar un certificado codificado en base 64 en su inquilino de SumoLogic. Si no está familiarizado con este procedimiento, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

###Siga estos pasos para configurar el inicio de sesión único:

1.  En el Portal de Azure AD, en la página de integración de aplicaciones de **SumoLogic**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-sumologic-tutorial/IC778552.png "Configurar inicio de sesión único")

2.  En la página **¿Cómo desea que los usuarios inicien sesión en SumoLogic?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y luego haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-sumologic-tutorial/IC778553.png "Configurar inicio de sesión único")

3.  En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto de **URL de inicio de sesión de SumoLogic**, escriba su dirección URL con el siguiente patrón "*https://\<nombre-inquilino>.SumoLogic.com*" y luego haga clic en **Siguiente**.

    ![Configurar URL aoo](./media/active-directory-saas-sumologic-tutorial/IC778554.png "Configurar URL aoo")

4.  En la página **Configuración de inicio de sesión único en SumoLogic**, para descargar el certificado, haga clic en **Descargar certificado** y luego guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-sumologic-tutorial/IC778555.png "Configurar inicio de sesión único")

5.  En otra ventana del explorador web, inicie sesión en su sitio de la compañía de SumoLogic como administrador.

6.  Vaya a **Administrar > Seguridad**.

    ![Manage](./media/active-directory-saas-sumologic-tutorial/IC778556.png "Manage")

7.  Haga clic en **SAML**.

    ![Configuración de seguridad global](./media/active-directory-saas-sumologic-tutorial/IC778557.png "Configuración de seguridad global")

8.  Desde la lista **Seleccionar una configuración o crear una nueva**, seleccione**Azure AD** y luego haga clic en **Configurar**.

    ![Configurar SAML 2.0](./media/active-directory-saas-sumologic-tutorial/IC778558.png "Configurar SAML 2.0")

9.  En el cuadro de diálogo **Configurar SAML 2.0**, realice los pasos siguientes:

    ![Configurar SAML 2.0](./media/active-directory-saas-sumologic-tutorial/IC778559.png "Configurar SAML 2.0")

    1.  En el cuadro de texto **Nombre de configuración**, escriba **Azure AD**.
    2.  Seleccione **Modo de depuración**.
    3.  En el portal de Azure, en la página de diálogo **Configurar inicio de sesión único en SumoLogic**, copie el valor de **URL del emisor** y péguelo en el cuadro de texto **Emisor**.
    4.  En el portal de Azure, en la página de diálogo **Configurar inicio de sesión único en SumoLogic**, copie el valor de **Dirección URL de solicitud de autenticación** y péguelo en el cuadro de texto **Dirección URL de solicitud de autenticación**.
    5.  Cree un archivo **codificado en base 64** a partir del certificado descargado.  

        >[AZURE.TIP]Para obtener más información, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o)

    6.  Abra el certificado codificado en base 64 en el Bloc de notas, copie su contenido en el Portapapeles y luego pegue todo el certificado en el cuadro de texto **Certificado X.509**.
    7.  Como **Atributo de correo electrónico**, seleccione **Usar SAML Subject**.
    8.  Seleccione **Configuración de inicio de sesión iniciada por el SP**.
    9.  En el cuadro de texto **Ruta de acceso de inicio de sesión**, escriba **Azure**.
    10. Haga clic en **Guardar**.

10. En el portal de Azure, en la página de diálogo **Configurar inicio de sesión único en SumoLogic**, seleccione la confirmación de configuración de inicio de sesión único y luego haga clic en **Completa**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-sumologic-tutorial/IC778560.png "Configurar inicio de sesión único")

##Configuración del aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD inicien sesión en SumoLogic, deben aprovisionarse a SumoLogic. En el caso de SumoLogic, el aprovisionamiento es una tarea manual.

###Para aprovisionar cuentas de usuario, realice los siguientes pasos:

1.  Inicie sesión en su inquilino de **SumoLogic**.

2.  Vaya a **Administrar > Usuarios**.

    ![Usuarios](./media/active-directory-saas-sumologic-tutorial/IC778561.png "Usuarios")

3.  Haga clic en **Agregar**.

    ![Usuarios](./media/active-directory-saas-sumologic-tutorial/IC778562.png "Usuarios")

4.  En el cuadro de diálogo **Nuevo usuario**, realice los pasos siguientes:

    ![Nuevo usuario](./media/active-directory-saas-sumologic-tutorial/IC778563.png "Nuevo usuario")

    1.  Escriba la información relacionada de la cuenta de Azure AD que quiere aprovisionar en los cuadros de texto **Nombre**, **Apellido** y **Correo electrónico**.
    2.  Seleccione un rol.
    3.  Como **Estado**, seleccione **Activo**.
    4.  Haga clic en **Guardar**.

>[AZURE.NOTE]Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de SumoLogic ofrecida por SumoLogic para aprovisionar cuentas de usuario de AAD.

##Asignación de usuarios
  
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

###Para asignar usuarios a SumoLogic, lleve a cabo los siguientes pasos:

1.  En el portal de Azure AD, cree una cuenta de prueba.

2.  En la página de integración de la aplicación **SumoLogic**, haga clic en **Asignar usuarios**.

    ![Asignar usuarios](./media/active-directory-saas-sumologic-tutorial/IC778564.png "Asignar usuarios")

3.  Seleccione su usuario de prueba, haga clic en **Asignar** y luego en **Sí** para confirmar la asignación.

    ![Sí](./media/active-directory-saas-sumologic-tutorial/IC767830.png "Sí")
  
Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0114_2016-->