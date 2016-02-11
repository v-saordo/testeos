<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con Picturepark | Microsoft Azure" 
    description="Aprenda cómo usar Picturepark con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc." 
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

#Tutorial: Integración de Azure Active Directory con Picturepark
  
El objetivo de este tutorial es mostrar la integración de Azure y Picturepark. En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción de Azure válida
-   Un inquilino de Picturepark
  
Después de completar este tutorial, los usuarios de Azure AD que ha asignado a Picturepark podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de Picturepark (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
La situación descrita en este tutorial consta de los siguientes bloques de creación:

1.  Habilitación de la integración de aplicaciones para Picturepark
2.  Configuración del inicio de sesión único
3.  Configuración del aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario](./media/active-directory-saas-picturepark-tutorial/IC795055.png "Escenario")
##Habilitación de la integración de aplicaciones para Picturepark
  
El objetivo de esta sección es describir cómo habilitar la integración de las aplicaciones para Picturepark.

###Siga estos pasos para habilitar la integración de aplicaciones para Picturepark:

1.  En el panel de navegación izquierdo del Portal de administración de Azure, haga clic en **Active Directory**.

    ![Active Directory](./media/active-directory-saas-picturepark-tutorial/IC700993.png "Active Directory")

2.  En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3.  Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

    ![Aplicaciones](./media/active-directory-saas-picturepark-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación](./media/active-directory-saas-picturepark-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Agregar una aplicación de la galería](./media/active-directory-saas-picturepark-tutorial/IC749322.png "Agregar una aplicación de la galería")

6.  En el **cuadro de búsqueda**, escriba **Picturepark**.

    ![Galería de aplicaciones](./media/active-directory-saas-picturepark-tutorial/IC795056.png "Galería de aplicaciones")

7.  En el panel de resultados, seleccione **Picturepark** y luego haga clic en **Completar** para agregar la aplicación.

    ![Picturepark](./media/active-directory-saas-picturepark-tutorial/IC795057.png "Picturepark")
##Configuración del inicio de sesión único
  
El objetivo de esta sección es describir cómo se habilita la autenticación de los usuarios en Picturepark con su cuenta de Azure AD usando el protocolo SAML basado en la federación. La configuración de un inicio de sesión único para Picturepark requiere la recuperación de un valor de huella digital de un certificado. Si no está familiarizado con este procedimiento, consulte [Recuperación del valor de huella digital de un certificado](http://youtu.be/YKQF266SAxI).

###Siga estos pasos para configurar el inicio de sesión único:

1.  En el Portal de Azure AD, en la página de integración de aplicaciones de **Picturepark**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-picturepark-tutorial/IC795058.png "Configurar inicio de sesión único")

2.  En la página **¿Cómo desea que los usuarios inicien sesión en Picturepark?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y luego haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-picturepark-tutorial/IC795059.png "Configurar inicio de sesión único")

3.  En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto de **URL de inicio de sesión de Picturepark**, escriba su dirección URL con el siguiente patrón "**http://company.picturepark.com*" y luego haga clic en **Siguiente**.

    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-picturepark-tutorial/IC795060.png "Configurar dirección URL de la aplicación")

4.  En la página **Configurar inicio de sesión único en Picturepark**, para descargar el certificado, haga clic en **Descargar certificado** y luego guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-picturepark-tutorial/IC795061.png "Configurar inicio de sesión único")

5.  En otra ventana del explorador web, inicie sesión en el sitio de la compañía Picturepark como administrador.

6.  En la barra de herramientas de la parte superior, haga clic en **Herramientas administrativas** y luego en **Consola de administración**.

    ![Consola de administración](./media/active-directory-saas-picturepark-tutorial/IC795062.png "Consola de administración")

7.  Haga clic en **Autenticación** y luego en **Proveedores de identidades**.

    ![Autenticación](./media/active-directory-saas-picturepark-tutorial/IC795063.png "Autenticación")

8.  En la sección de **Configuración del proveedor de identidades**, realice los pasos siguientes:

    ![Configuración del proveedor de identidades](./media/active-directory-saas-picturepark-tutorial/IC795064.png "Configuración del proveedor de identidades")

    1.  Haga clic en **Agregar**.
    2.  Escriba un nombre para su configuración.
    3.  Seleccione **Establecer como predeterminado**.
    4.  En el portal de Azure, en la página de diálogo **Configurar inicio de sesión único en Picturepark**, copie el valor de **Dirección URL de SSO de SAML** y luego péguelo en el cuadro de texto **URI de emisor**.
    5.  Copie el valor de **Huella digital** del certificado exportado y péguelo en el cuadro de texto **Huella digital del emisor de confianza**.  

        >[AZURE.TIP]Para obtener más información, consulte [Recuperación del valor de huella digital de un certificado](http://youtu.be/YKQF266SAxI).

    6.  Haga clic en **JoinDefaultUsersGroup**.
    7.  Para establecer el atributo **Emailaddress** en el cuadro de texto **Notificación**, escriba ****http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**. ![Configuración](./media/active-directory-saas-picturepark-tutorial/IC795065.png "Configuración")
8.  Haga clic en **Guardar**.

9.  En el Portal de Azure AD, seleccione la confirmación de configuración de inicio de sesión único y, a continuación, haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-picturepark-tutorial/IC795066.png "Configurar inicio de sesión único")

##Configuración del aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD inicien sesión en Picturepark, deben aprovisionarse en Picturepark. En el caso de Picturepark, el aprovisionamiento es una tarea manual.

###Para aprovisionar cuentas de usuario, realice los siguientes pasos:

1.  Inicie sesión en su inquilino de **Picturepark**.

2.  En la barra de herramientas de la parte superior, haga clic en **Herramientas administrativas** y luego en **Usuarios**.

    ![Usuarios](./media/active-directory-saas-picturepark-tutorial/IC795067.png "Usuarios")

3.  En la pestaña de **información general de los usuarios**, haga clic **Nuevo**.

    ![Administración de usuarios](./media/active-directory-saas-picturepark-tutorial/IC795068.png "Administración de usuarios")

4.  En el cuadro de diálogo **Crear usuario**, realice los pasos siguientes:

    ![Crear usuario](./media/active-directory-saas-picturepark-tutorial/IC795069.png "Crear usuario")

    1.  Escriba :**Dirección de correo electrónico**, **Contraseña**, **Confirmar contraseña**, **Nombre**, **Apellido**, **Compañía**, **País**, **Código postal**, **Ciudad** de un usuario válido de Azure Active Directory que quiera aprovisionar en los cuadros de texto relacionados.
    2.  Seleccione un **Idioma**.
    3.  Haga clic en **Crear**.

>[AZURE.NOTE]Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Picturepark ofrecida por Picturepark para aprovisionar cuentas de usuario de AAD.

##Asignación de usuarios
  
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

###Para asignar usuarios a Picturepark, lleve a cabo los siguientes pasos:

1.  En el portal de Azure AD, cree una cuenta de prueba.

2.  En la página de integración de la aplicación **Picturepark**, haga clic en **Asignar usuarios**.

    ![Asignar usuarios](./media/active-directory-saas-picturepark-tutorial/IC795070.png "Asignar usuarios")

3.  Seleccione su usuario de prueba, haga clic en **Asignar** y luego en **Sí** para confirmar la asignación.

    ![Sí](./media/active-directory-saas-picturepark-tutorial/IC767830.png "Sí")
  
Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0114_2016-->