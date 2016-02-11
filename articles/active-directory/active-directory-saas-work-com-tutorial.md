<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con Work.com | Microsoft Azure" 
    description="Aprenda cómo usar Work.com con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc." 
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

#Tutorial: Integración de Azure Active Directory con Work.com
  
El objetivo de este tutorial es mostrar la integración de Azure y Work.com.  
En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción de Azure válida
-   Una suscripción habilitada para el inicio de sesión único en Work.com
  
Después de completar este tutorial, los usuarios de AAD a los que ha asignado acceso a Work.com podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de Work.com (inicio de sesión iniciado por el proveedor del servicio) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
La situación descrita en este tutorial consta de los siguientes bloques de creación:

1.  Habilitación de la integración de aplicaciones en Work.com
2.  Configuración del inicio de sesión único
3.  Configuración del aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario](./media/active-directory-saas-work-com-tutorial/IC794105.png "Escenario")

##Habilitación de la integración de aplicaciones en Work.com
  
El objetivo de esta sección es describir cómo se habilita la integración de aplicaciones para Work.com.

###Siga estos pasos para habilitar la integración de aplicaciones para Work.com:

1.  En el panel de navegación izquierdo del Portal de administración de Azure, haga clic en **Active Directory**.

    ![Active Directory](./media/active-directory-saas-work-com-tutorial/IC700993.png "Active Directory")

2.  En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3.  Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

    ![Aplicaciones](./media/active-directory-saas-work-com-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación](./media/active-directory-saas-work-com-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Agregar una aplicación de la galería](./media/active-directory-saas-work-com-tutorial/IC749322.png "Agregar una aplicación de la galería")

6.  En el **cuadro de búsqueda**, escriba **Work.com**.

    ![Galería de aplicaciones](./media/active-directory-saas-work-com-tutorial/IC794106.png "Galería de aplicaciones")

7.  En el panel de resultados, seleccione **Work.com** y luego haga clic en **Completar** para agregar la aplicación.

    ![Work.com](./media/active-directory-saas-work-com-tutorial/IC794107.png "Work.com")

##Configuración del inicio de sesión único
  
El objetivo de esta sección es describir cómo se habilita la autenticación de los usuarios en Work.com con su cuenta de Azure AD usando el protocolo SAML basado en la federación.  
Como parte de este procedimiento, es necesario cargar un certificado en Work.com.com.

>[AZURE.NOTE]Para configurar el inicio de sesión único, deberá configurar todavía un nombre de dominio personalizado de Work.com. Deberá definir al menos un nombre de dominio, probar su nombre de dominio e implementarlo en toda la organización.

###Siga estos pasos para configurar el inicio de sesión único:

1.  Inicie sesión en su inquilino de Work.com como administrador.

2.  Acceda a **Setup** (Configuración).

    ![Configuración](./media/active-directory-saas-work-com-tutorial/IC794108.png "Configuración")

3.  En el panel de navegación izquierdo, en la sección **Administrar**, haga clic en **Administración de dominios** para expandir la sección relacionada y, luego, haga clic en la página **Mi dominio** para abrir la página **Mi dominio**.

    ![Mi dominio](./media/active-directory-saas-work-com-tutorial/IC767825.png "Mi dominio")

4.  Para comprobar que el dominio se ha configurado correctamente, asegúrese de que está en el "**Paso 4 Implementación para usuarios**" y revise "**Mi configuración de dominio**".

    ![Dominio implementado al usuario](./media/active-directory-saas-work-com-tutorial/IC784377.png "Dominio implementado al usuario")

5.  En una ventana de explorador web diferente, inicie sesión en el portal de Azure.

6.  En la página de integración de aplicaciones de **Work.com**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-work-com-tutorial/IC794109.png "Configurar inicio de sesión único")

7.  En la página **¿Cómo desea que los usuarios inicien sesión en Work.com?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y, luego , haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-work-com-tutorial/IC794110.png "Configurar inicio de sesión único")

8.  En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto **URL de inicio de sesión de Work.com**, escriba la dirección URL que utilizan los usuarios para iniciar sesión en su aplicación Work.com (por ejemplo: "*http://company.my.salesforce.com*") y luego haga clic en **Siguiente**.

    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-work-com-tutorial/IC794111.png "Configurar dirección URL de la aplicación")

9.  En la página **Configurar inicio de sesión único en Work.com**, para descargar el certificado, haga clic en **Descargar certificado** y, a continuación, guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-work-com-tutorial/IC794112.png "Configurar inicio de sesión único")

10. Inicie sesión en su inquilino de Work.com.

11. Acceda a **Setup** (Configuración).

    ![Configuración](./media/active-directory-saas-work-com-tutorial/IC794108.png "Configuración")

12. Expanda el menú **Security Controls** (Controles de seguridad) y luego haga clic en **Single Sign-On Settings** (Configuración de inicio de sesión único).

    ![Configuración de inicio de sesión único](./media/active-directory-saas-work-com-tutorial/IC794113.png "Configuración de inicio de sesión único")

13. En la página del cuadro de diálogo **Single Sign-On Settings** (Configuración de inicio de sesión único), siga estos pasos:

    ![SAML habilitado](./media/active-directory-saas-work-com-tutorial/IC781026.png "SAML habilitado")

    1.  Seleccione **SAML habilitado**.
    2.  Haga clic en **Nuevo**.

14. En la sección **SAML Single Sign-On Settings** (Configuración del inicio de sesión único de SAML), siga estos pasos:

    ![Configuración de inicio de sesión único SAML](./media/active-directory-saas-work-com-tutorial/IC794114.png "Configuración de inicio de sesión único SAML")

    1.  En el cuadro de texto **Name** (Nombre), escriba el nombre de la configuración.  

        >[AZURE.NOTE]Si se proporciona un valor para **Name** (Nombre), el cuadro de texto **API Name** (Nombre de API) se completa automáticamente.

    2.  En el portal de Azure, en la página de diálogo **Configurar inicio de sesión único en Work.com**, copie el valor de **URL del emisor** y péguelo en el cuadro de texto **Emisor**.
    3.  Para cargar el certificado descargado, haga clic en **Examinar** .
    4.  En el cuadro de texto **Id. de entidad**, escriba **https://salesforce-work.com**.
    5.  Como **Tipo de identidad SAML**, seleccione **La aserción contiene el identificador de la federación del objeto de usuario**.
    6.  Como **Ubicación de identidad de SAML**, seleccione **La identidad está en el elemento NameIdentifier de la instrucción de sujeto**.
    7.  En el Portal de Azure, en la página de diálogo **Configurar inicio de sesión único en Work.com**, copie el valor de **Dirección URL de inicio de sesión remoto** y péguelo en el cuadro de texto **Dirección URL de inicio de sesión de proveedor de identidades**.
    8.  En el Portal de Azure, en la página de diálogo **Configurar inicio de sesión único en Work.com**, copie el valor de **Dirección URL de cierre de sesión remoto** y péguelo en el cuadro de texto **Dirección URL de cierre de sesión de proveedor de identidades**.
    9.  Como **Vinculación de solicitud iniciada del proveedor de servicios**, seleccione **HTTP Post**.
    10. Haga clic en **Guardar**.

15. En su portal de Work.com, en el panel de navegación izquierdo, haga clic en **Domain Management** (Administración de dominios) para expandir la sección relacionada y luego haga clic en la página **My Domain** (Mi dominio) para abrir la página **My Domain** (Mi dominio).

    ![Mi dominio](./media/active-directory-saas-work-com-tutorial/IC794115.png "Mi dominio")

16. En la página **Mi dominio**, en la sección **Personalización de marca de la página de inicio de sesión**, haga clic en **Editar**.

    ![Personalización de marca de la página de inicio de sesión](./media/active-directory-saas-work-com-tutorial/IC767826.png "Personalización de marca de la página de inicio de sesión")

17. En la página **Login Page Branding** (Personalización de marca de la página de inicio de sesión), en la sección **Authentication Service** (Servicio de autenticación), se muestra el nombre de su **SAML SSO Settings** (Configuración de SSO de SAML). Selecciónelo y luego haga clic en **Save** (Guardar).

    ![Personalización de marca de la página de inicio de sesión](./media/active-directory-saas-work-com-tutorial/IC784366.png "Personalización de marca de la página de inicio de sesión")

18. En el portal de Azure AD, seleccione la confirmación de configuración de inicio de sesión único y luego haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-work-com-tutorial/IC794116.png "Configurar inicio de sesión único")

##Configuración del aprovisionamiento de usuario
  
Para que los usuarios de Azure Active Directory puedan iniciar sesión, deben aprovisionarse a Work.com.  
En el caso de Work.com, el aprovisionamiento es una tarea manual.

###Siga estos pasos para configurar el aprovisionamiento de usuario:

1.  Inicie sesión en su sitio de la compañía de Work.com como administrador.

2.  Acceda a **Setup** (Configuración).

    ![Configuración](./media/active-directory-saas-work-com-tutorial/IC794108.png "Configuración")

3.  Vaya a **Administrar usuarios > Usuarios**.

    ![Administrar usuarios](./media/active-directory-saas-work-com-tutorial/IC784369.png "Administrar usuarios")

4.  Haga clic en **Nuevo usuario**.

    ![Todos los usuarios](./media/active-directory-saas-work-com-tutorial/IC794117.png "Todos los usuarios")

5.  En la sección Edición de usuario, lleve a cabo estos pasos:

    ![Edición de usuarios](./media/active-directory-saas-work-com-tutorial/IC794118.png "Edición de usuarios")

    1.  Escriba los atributos **Apellido**, **Alias**, **Correo electrónico**, **Nombre de usuario** y **Sobrenombre** de una cuenta de Azure Active Directory válida que quiera aprovisionar en los cuadros de texto relacionados.
    2.  Seleccione **Role** (Rol), **User License** (Licencia de usuario) y **Profile** (Perfil).
    3.  Haga clic en **Guardar**.  

        >[AZURE.NOTE]El titular de la cuenta de Azure Active Directory recibirá un mensaje de correo electrónico con un vínculo para confirmar la cuenta antes de que se active.

>[AZURE.NOTE]Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Work.com ofrecida por Work.com para aprovisionar cuentas de usuario de AAD.

##Asignación de usuarios
  
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

###Para asignar usuarios a Work.com, lleve a cabo los siguientes pasos:

1.  En el portal de Azure AD, cree una cuenta de prueba.

2.  En la página de integración de la aplicación Work.com, haga clic en **Asignar usuarios**.

    ![Asignar usuarios](./media/active-directory-saas-work-com-tutorial/IC794119.png "Asignar usuarios")

3.  Seleccione su usuario de prueba, haga clic en **Asignar** y luego en **Sí** para confirmar la asignación.

    ![Sí](./media/active-directory-saas-work-com-tutorial/IC767830.png "Sí")
  
Ahora debería esperar 10 minutos y comprobar si la cuenta se ha sincronizado en Work.com.com.
  
Si quiere probar su configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0114_2016-->