<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con Sprinklr | Microsoft Azure" 
    description="Aprenda cómo usar Sprinklr con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc." 
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

#Tutorial: integración de Azure Active Directory con Sprinklr
  
El objetivo de este tutorial es mostrar la integración de Azure y Sprinklr.  
En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción de Azure válida
-   Un inquilino de Sprinklr
  
Después de completar este tutorial, los usuarios de Azure AD que asignó a Sprinklr podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de Sprinklr (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
La situación descrita en este tutorial consta de los siguientes bloques de creación:

1.  Habilitación de la integración de aplicaciones para Sprinklr
2.  Configuración del inicio de sesión único
3.  Configuración del aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario](./media/active-directory-saas-sprinklr-tutorial/IC782900.png "Escenario")

##Habilitación de la integración de aplicaciones para Sprinklr
  
El objetivo de esta sección es describir cómo se habilita la integración de aplicaciones en Sprinklr.

###Siga estos pasos para habilitar la integración de aplicaciones para Sprinklr:

1.  En el panel de navegación izquierdo del Portal de administración de Azure, haga clic en **Active Directory**.

    ![Active Directory](./media/active-directory-saas-sprinklr-tutorial/IC700993.png "Active Directory")

2.  En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3.  Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

    ![Aplicaciones](./media/active-directory-saas-sprinklr-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación](./media/active-directory-saas-sprinklr-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Agregar una aplicación de la galería](./media/active-directory-saas-sprinklr-tutorial/IC749322.png "Agregar una aplicación de la galería")

6.  En el **cuadro de búsqueda**, escriba **Sprinklr**.

    ![Galería de aplicaciones](./media/active-directory-saas-sprinklr-tutorial/IC782901.png "Galería de aplicaciones")

7.  En el panel de resultados, seleccione **Sprinklr** y, luego, haga clic en **Completar** para agregar la aplicación.

    ![Sprinklr](./media/active-directory-saas-sprinklr-tutorial/IC782902.png "Sprinklr")

##Configuración del inicio de sesión único
  
El objetivo de esta sección es describir cómo se habilita la autenticación de los usuarios en Sprinklr con su cuenta de Azure AD usando el protocolo SAML basado en la federación.  
Como parte de este procedimiento, es necesario crear un archivo de certificado codificado en base 64.  
Si no está familiarizado con este procedimiento, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

###Siga estos pasos para configurar el inicio de sesión único:

1.  En el portal de Azure AD, en la página de integración de aplicaciones de **Sprinklr**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-sprinklr-tutorial/IC782903.png "Configurar inicio de sesión único")

2.  En la página **¿Cómo desea que los usuarios inicien sesión en Sprinklr?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y, luego , haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-sprinklr-tutorial/IC782904.png "Configurar inicio de sesión único")

3.  En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto de **URL de inicio de sesión de Sprinklr**, escriba su dirección URL con el siguiente patrón "*https://\<nombre-inquilino>.sprinklr.com*" y luego haga clic en **Siguiente**.

    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-sprinklr-tutorial/IC782905.png "Configurar dirección URL de la aplicación")

4.  En la página **Configuración de inicio de sesión único en Sprinklr**, para descargar el certificado, haga clic en **Descargar certificado** y luego guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-sprinklr-tutorial/IC782906.png "Configurar inicio de sesión único")

5.  En otra ventana del explorador web, inicie sesión en su sitio de la compañía de Sprinklr como administrador.

6.  Vaya a **Administration > Settings** (Administración > Configuración).

    ![Administración](./media/active-directory-saas-sprinklr-tutorial/IC782907.png "Administración")

7.  Vaya a **Manage Partner > Single Sign** (Administrar socios > Inicio de sesión único) en el panel izquierdo.

    ![Administrar socio](./media/active-directory-saas-sprinklr-tutorial/IC782908.png "Administrar socio")

8.  Haga clic en **+Add Single Sign On** (+ Agregar inicios de sesión únicos).

    ![Inicios de sesión únicos](./media/active-directory-saas-sprinklr-tutorial/IC782909.png "Inicios de sesión únicos")

9.  Siga estos pasos en la página **Inicio de sesión único**:

    ![Inicios de sesión únicos](./media/active-directory-saas-sprinklr-tutorial/IC782910.png "Inicios de sesión únicos")

    1.  En el cuadro de texto **Name** (Nombre), escriba el nombre de la configuración (por ejemplo, *WAADSSOTest*).
    2.  Seleccione **Habilitado**.
    3.  Seleccione **Use new SSO Certificate** (Usar el nuevo certificado de SSO).
    4.  Cree un archivo **codificado en base 64** a partir del certificado descargado.  

        >[AZURE.TIP]Para obtener más información, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

    5.  Abra el certificado codificado en base 64 en el Bloc de notas, copie su contenido en el Portapapeles y, a continuación, péguelo en el cuadro de texto **Identity provider certificate** (Certificado de proveedor de identidades).
    6.  En el Portal de Azure, en la página de diálogo **Configurar inicio de sesión único en Sprinklr**, copie el valor de **Id. de proveedor de identidades** y péguelo en el cuadro de texto **Id. de entidad**.
    7.  En el Portal de Azure, en la página de diálogo **Configurar inicio de sesión único en Sprinklr**, copie el valor de **Dirección URL de inicio de sesión remoto** y péguelo en el cuadro de texto **Dirección URL de inicio de sesión del proveedor de identidades**.
    8.  En el Portal de Azure, en la página de diálogo **Configurar inicio de sesión único en Sprinklr**, copie el valor de **Dirección URL de cierre de sesión remoto** y péguelo en el cuadro de texto **Dirección URL de cierre del proveedor de identidades**.
    9.  Como **Tipo de id. de usuario de SAML**, seleccione **La aserción contiene el nombre de usuario de sprinklr.com del usuario**.
    10. Para **Ubicación de id. de usuario de SAML**, seleccione **El id. de usuario está en el elemento NameIdentifier de la instrucción Subject**.
    11. Cierre **Guardar**.

        ![SAML](./media/active-directory-saas-sprinklr-tutorial/IC782911.png "SAML")

10. En el portal de Azure AD, seleccione la confirmación de configuración de inicio de sesión único y luego haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-sprinklr-tutorial/IC782912.png "Configurar inicio de sesión único")

##Configuración del aprovisionamiento de usuario
  
Para que los usuarios de AAD puedan iniciar sesión, deben aprovisionarse para acceso dentro de la aplicación Syncplicity.  
En esta sección se describe cómo crear cuentas de usuario de AAD en Sprinklr.

###Para aprovisionar cuentas de usuario a Sprinklr, realice los siguientes pasos:

1.  Inicie sesión en su sitio de la compañía de Sprinklr como administrador.

2.  Vaya a **Administration > Settings** (Administración > Configuración).

    ![Administración](./media/active-directory-saas-sprinklr-tutorial/IC782907.png "Administración")

3.  Vaya a **Manage Client > Users** (Administrar clientes > Usuarios) en el panel izquierdo.

    ![Settings](./media/active-directory-saas-sprinklr-tutorial/IC782914.png "Settings")

4.  Haga clic en **Agregar usuario**.

    ![Settings](./media/active-directory-saas-sprinklr-tutorial/IC782915.png "Settings")

5.  En el cuadro de diálogo **Edit user** (Editar usuario), realice los siguientes pasos:

    ![Editar usuario](./media/active-directory-saas-sprinklr-tutorial/IC782916.png "Editar usuario")

    1.  En los cuadros de texto **Email** (Correo electrónico), **Name** (Nombre) y **Last Name** (Apellidos), escriba la información de una cuenta de usuario de Azure AD que quiera aprovisionar.
    2.  Seleccione **Password Disabled** (Contraseña deshabilitada).
    3.  Seleccione un valor en **Language** (Idioma).
    4.  Seleccione un valor en **User Type** (Tipo de usuario).
    5.  Haga clic en **Update** (Actualizar).

    >[AZURE.IMPORTANT]La opción **Password Disabled** (Contraseña deshabilitada) debe estar seleccionada para que los usuarios puedan iniciar sesión a través de un proveedor de identidades.

6.  Vaya a **Role** (Rol) y luego lleve a cabo los siguientes pasos:

    ![Roles de socios](./media/active-directory-saas-sprinklr-tutorial/IC782917.png "Roles de socios")

    1.  En la lista **Global**, seleccione**ALL\_Permissions** (Todos los permisos).
    2.  Haga clic en **Update** (Actualizar).

>[AZURE.NOTE] Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Sprinklr ofrecida por Sprinklr para aprovisionar cuentas de usuario de Azure AD.

##Asignación de usuarios
  
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

###Para asignar usuarios a Sprinklr, lleve a cabo los siguientes pasos:

1.  En el portal de Azure AD, cree una cuenta de prueba.

2.  En la página de integración de la aplicación **Sprinklr**, haga clic en **Asignar usuarios**.

    ![Asignar usuarios](./media/active-directory-saas-sprinklr-tutorial/IC782918.png "Asignar usuarios")

3.  Seleccione su usuario de prueba, haga clic en **Asignar** y luego en **Sí** para confirmar la asignación.

    ![Sí](./media/active-directory-saas-sprinklr-tutorial/IC767830.png "Sí")
  
Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0114_2016-->