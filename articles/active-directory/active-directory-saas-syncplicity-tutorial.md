<properties 
    pageTitle="Tutorial: integración de Azure Active Directory con Syncplicity | Microsoft Azure" 
    description="Aprenda cómo usar Syncplicity con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc." 
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

#Tutorial: integración de Azure Active Directory con Syncplicity
  
El objetivo de este tutorial es mostrar cómo configurar el inicio de sesión único entre Azure Active Directory (AAD) y Syncplicity.
  
En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción de Azure válida
-   Un inquilino de Syncplicity
  
Después de completar este tutorial, los usuarios de AAD a los que ha asignado acceso a Syncplicity podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de Syncplicity (inicio de sesión iniciado por el proveedor del servicio) o con el Panel de acceso de AAD.

1.  Habilitación de la integración de aplicaciones para Syncplicity
2.  Configuración del inicio de sesión único
3.  Configuración del aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario](./media/active-directory-saas-syncplicity-tutorial/IC769524.png "Escenario")

##Habilitación de la integración de aplicaciones para Syncplicity
  
El objetivo de esta sección es describir cómo habilitar la integración de las aplicaciones para Syncplicity.

###Siga estos pasos para habilitar la integración de aplicaciones para Syncplicity:

1.  En el panel de navegación izquierdo del Portal de administración de Azure, haga clic en **Active Directory**.

    ![Active Directory](./media/active-directory-saas-syncplicity-tutorial/IC700993.png "Active Directory")

2.  En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3.  Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

    ![Aplicaciones](./media/active-directory-saas-syncplicity-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación](./media/active-directory-saas-syncplicity-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Agregar una aplicación de la galería](./media/active-directory-saas-syncplicity-tutorial/IC749322.png "Agregar una aplicación de la galería")

6.  En el **cuadro de búsqueda**, escriba **Syncplicity**.

    ![Galería de aplicaciones de Syncplicity](./media/active-directory-saas-syncplicity-tutorial/IC769532.png "Galería de aplicaciones de Syncplicity")

7.  En el panel de resultados, seleccione **Syncplicity** y luego haga clic en **Completar** para agregar la aplicación.

    ![Syncplicity](./media/active-directory-saas-syncplicity-tutorial/IC769533.png "Syncplicity")

##Configuración del inicio de sesión único
  
El objetivo de esta sección es describir cómo habilitar usuarios para que se autentiquen en Syncplicity con su cuenta de Azure AD a través de la federación basada en el protocolo SAML.

###Siga estos pasos para configurar el inicio de sesión único:

1.  En el Portal de Azure AD, en la página de integración de aplicaciones de **Syncplicity**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-syncplicity-tutorial/IC769534.png "Configurar inicio de sesión único")

2.  En la página **¿Cómo desea que los usuarios inicien sesión en Syncplicity?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y luego haga clic en **Siguiente**.

    ![Inicio de sesión único de Microsoft Azure AD](./media/active-directory-saas-syncplicity-tutorial/IC769535.png "Inicio de sesión único de Microsoft Azure AD")

3.  En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto **URL de inicio de sesión de Syncplicity**, escriba la dirección URL que utilizan los usuarios para iniciar sesión en su aplicación Syncplicity y haga clic en **Siguiente**.

    La dirección URL de la aplicación es su URL de inquilino de Syncplicity, por ejemplo, **http://company.Syncplicity.com*):

    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-syncplicity-tutorial/IC769536.png "Configurar dirección URL de la aplicación")

4.  En la página **Configurar inicio de sesión único en Syncplicity**, para descargar el certificado, haga clic en **Descargar certificado** y, a continuación, guarde el archivo de certificado localmente en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-syncplicity-tutorial/IC769543.png "Configurar inicio de sesión único")

5.  Inicie sesión en su inquilino de **Syncplicity**.

6.  En el menú en la parte superior, haga clic en **admin** (Administración), seleccione **settings** (Configuración) y luego haga clic en **Custom domain and single sign-on** (Dominio personalizado e inicio de sesión único).

    ![Syncplicity](./media/active-directory-saas-syncplicity-tutorial/IC769545.png "Syncplicity")

7.  En la página del cuadro de diálogo **Single Sign-On (SSO)** (Configuración de inicio de sesión único [SSO]), siga estos pasos:

    ![Inicio de sesión único (SSO)](./media/active-directory-saas-syncplicity-tutorial/IC769550.png "Inicio de sesión único (SSO)")

    1.  En el cuadro de texto **Custom Domain** (Dominio personalizado), escriba el nombre de su dominio.
    2.  Seleccione **Enabled** (Habilitado) como **Single Sign-On Status** (Estado de inicio de sesión único).
    3.  En el Portal de Microsoft Azure, en la página **Configurar inicio de sesión único en Syncplicity**, copie el valor de **Id. de entidad ** y péguelo en el cuadro de texto **Id. de entidad**.
    4.  En el Portal de Microsoft Azure, en la página de diálogo **Configurar inicio de sesión único en Syncplicity**, copie el valor de **Dirección URL del servicio de inicio de sesión único** y péguelo en el cuadro de texto **Dirección URL de la página de inicio de sesión**.
    5.  En el Portal de Microsoft Azure, en la página de diálogo **Configurar inicio de sesión único en Syncplicity**, copie el valor de **Dirección URL de cierre de sesión remoto** y péguelo en el cuadro de texto **Dirección URL de la página de cierre de sesión**.
    6.  En **Certificado del proveedor de identidades**, haga clic en**Elegir archivo** y luego cargue el certificado que ha descargado del Portal de Microsoft Azure.
    7.  Haga clic en **Guardar cambios**.

8.  En el portal de Azure AD, seleccione la confirmación de configuración de inicio de sesión único y luego haga clic en **Completa** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Confirmación](./media/active-directory-saas-syncplicity-tutorial/IC769554.png "Confirmación")

##Configuración del aprovisionamiento de usuario
  
Para que los usuarios de AAD puedan iniciar sesión, deben aprovisionarse a Syncplicity. En esta sección se describe cómo crear cuentas de usuario de AAD en Syncplicity.

###Para aprovisionar cuentas de usuario a Syncplicity, realice los siguientes pasos:

1.  Inicie sesión en su inquilino de **Syncplicity** (por ejemplo,: **https://company.Syncplicity.com*)).

2.  Haga clic en **Admin** (Administración) y seleccione **user accounts** (cuentas de usuario).

3.  Haga clic en **Add a User** (Agregar un usuario).

    ![Administrar usuarios](./media/active-directory-saas-syncplicity-tutorial/IC769764.png "Administrar usuarios")

4.  Escriba la **Email Address** (Dirección de correo electrónico) de una cuenta de AAD que quiera aprovisionar, seleccione **User** (Usuario) como **Role** (Rol) y luego haga clic en **Next** (Siguiente).

    ![Información de cuenta](./media/active-directory-saas-syncplicity-tutorial/IC769765.png "Información de cuenta")

    >[AZURE.NOTE]El titular de la cuenta de AAD recibirá un mensaje de correo electrónico junto con un vínculo para confirmar y activar la cuenta.

5.  Seleccione un grupo de la compañía de la que debe convertirse en miembro su nuevo usuario y luego haga clic en **Next** (Siguiente).

    ![Pertenencia a grupos](./media/active-directory-saas-syncplicity-tutorial/IC769772.png "Pertenencia a grupos")

    >[AZURE.NOTE]Si no se muestra ningún grupo, simplemente haga clic en **Next** (Siguiente).

6.  Seleccione las carpetas que desea colocar bajo el control de Syncplicity en el equipo del usuario y luego haga clic en **Next** (Siguiente).

    ![Carpetas de Syncplicity](./media/active-directory-saas-syncplicity-tutorial/IC769773.png "Carpetas de Syncplicity")

>[AZURE.NOTE]Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Syncplicity ofrecida por Syncplicity para aprovisionar cuentas de usuario de AAD.

##Asignación de usuarios
  
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

###Para asignar usuarios a Syncplicity, lleve a cabo los siguientes pasos:

1.  En el portal de Azure AD, cree una cuenta de prueba.

2.  En la página de integración de la aplicación **Syncplicity**, haga clic en **Asignar usuarios**.

    ![Asignar usuarios](./media/active-directory-saas-syncplicity-tutorial/IC769557.png "Asignar usuarios")

3.  Seleccione su usuario de prueba, haga clic en **Asignar** y luego en **Sí** para confirmar la asignación.

    ![Sí](./media/active-directory-saas-syncplicity-tutorial/IC767830.png "Sí")
  
Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0114_2016-->