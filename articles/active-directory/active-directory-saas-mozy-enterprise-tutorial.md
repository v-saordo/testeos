<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con Mozy Enterprise | Microsoft Azure" 
    description="Aprenda a usar Mozy Enterprise con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc." 
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
    ms.date="01/26/2016" 
    ms.author="jeedes" />

#Tutorial: Integración de Azure Active Directory con Mozy Enterprise
  
El objetivo de este tutorial es mostrar la integración de Azure y Mozy Enterprise. En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción de Azure válida
-   Un inquilino de Mozy Enterprise
  
Después de completar este tutorial, los usuarios de Azure AD asignados a Mozy Enterprise podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de Mozy Enterprise (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
La situación descrita en este tutorial consta de los siguientes bloques de creación:

1.  Habilitación de la integración de aplicaciones para Mozy Enterprise
2.  Configuración del inicio de sesión único
3.  Configuración del aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario](./media/active-directory-saas-mozy-enterprise-tutorial/IC777308.png "Escenario")
##Habilitación de la integración de aplicaciones para Mozy Enterprise
  
El objetivo de esta sección es describir cómo habilitar la integración de aplicaciones para Mozy Enterprise.

###Siga estos pasos para habilitar la integración de aplicaciones para Mozy Enterprise:

1.  En el panel de navegación izquierdo del Portal de administración de Azure, haga clic en **Active Directory**.

    ![Active Directory](./media/active-directory-saas-mozy-enterprise-tutorial/IC700993.png "Active Directory")

2.  En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3.  Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

    ![Aplicaciones](./media/active-directory-saas-mozy-enterprise-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación](./media/active-directory-saas-mozy-enterprise-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Agregar una aplicación de la galería](./media/active-directory-saas-mozy-enterprise-tutorial/IC749322.png "Agregar una aplicación de la galería")

6.  En el **cuadro de búsqueda**, escriba **mozy enterprise**.

    ![Galería de aplicaciones](./media/active-directory-saas-mozy-enterprise-tutorial/IC777309.png "Galería de aplicaciones")

7.  En el panel de resultados, seleccione **Mozy Enterprise** y, a continuación, haga clic en **Completar** para agregar la aplicación.

    ![Mozy Enterprise](./media/active-directory-saas-mozy-enterprise-tutorial/IC777310.png "Mozy Enterprise")
##Configuración del inicio de sesión único
  
El objetivo de esta sección es describir cómo habilitar la autenticación de usuarios en Mozy Enterprise con su cuenta de Azure AD mediante federación basada en el protocolo SAML. Como parte de este procedimiento, es necesario cargar un certificado codificado en base 64 en su inquilino de Mozy Enterprise. Si no está familiarizado con este procedimiento, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

###Siga estos pasos para configurar el inicio de sesión único:

1.  En el Portal de Azure AD, en la página de integración de aplicaciones de **Mozy Enterprise**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-mozy-enterprise-tutorial/IC771709.png "Configurar inicio de sesión único")

2.  En la página **¿Cómo desea que los usuarios inicien sesión en Mozy Enterprise?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y, a continuación, haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-mozy-enterprise-tutorial/IC777311.png "Configurar inicio de sesión único")

3.  En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto **URL de inicio de sesión de Mozy Enterprise**, escriba su dirección URL con el siguiente patrón "*https://\<NombreDeInquilino>.Mozyenterprise.com*" y, luego, haga clic en **Siguiente**.

    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-mozy-enterprise-tutorial/IC777312.png "Configurar dirección URL de la aplicación")

4.  En la página **Configuración de inicio de sesión único en Mozy Enterprise**, para descargar el certificado, haga clic en **Descargar certificado** y, a continuación, guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-mozy-enterprise-tutorial/IC777313.png "Configurar inicio de sesión único")

5.  En otra ventana del explorador web, inicie sesión como administrador en el sitio de la compañía de Mozy Enterprise.

6.  En la sección **Configuration** (Configuración), haga clic en **Authentication Policy** (Directiva de autenticación).

    ![Directiva de autenticación](./media/active-directory-saas-mozy-enterprise-tutorial/IC777314.png "Directiva de autenticación")

7.  En la sección **Authentication Policy** (Directiva de autenticación), realice estos pasos:

    ![Directiva de autenticación](./media/active-directory-saas-mozy-enterprise-tutorial/IC777315.png "Directiva de autenticación")

    1.  Seleccione **Servicio de directorio** como **Proveedor**.
    2.  Seleccione **Use LDAP Push** (Usar inserción de LDAP).
    3.  Haga clic en la pestaña **SAML Authentication** (Autenticación SAML).
    4.  En el Portal de Azure, en la página de diálogo **Configurar inicio de sesión único en Mozy Enterprise**, copie el valor de **Dirección URL de solicitud de autenticación** y péguelo en el cuadro de texto **Dirección URL de autenticación**.
    5.  En el Portal de Azure, en la página de diálogo **Configurar inicio de sesión único en Mozy Enterprise**, copie el valor de **Identificador del proveedor de identidades** y péguelo en el cuadro de texto **Extremo de SAML**.
    6.  Cree un archivo **codificado en base 64** a partir del certificado descargado.  

        >[AZURE.TIP]Para obtener más información, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

    7.  Abra el certificado codificado en base 64 en el Bloc de notas, copie su contenido en el Portapapeles y, a continuación, pegue todo el certificado en el cuadro de texto **Certificado SAML**.
    8.  Seleccione **Habilitar SSO para que los administradores inicien sesión con sus credenciales de red**.
    9.  Haga clic en **Guardar cambios**.

8.  En el Portal de Azure, en la página de diálogo **Configurar inicio de sesión único en Mozy Enterprise**, seleccione la confirmación de configuración de inicio de sesión único y luego haga clic en **Completar**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-mozy-enterprise-tutorial/IC777316.png "Configurar inicio de sesión único")
##Configuración del aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD inicien sesión en Mozy Enterprise, deben aprovisionarse en Mozy Enterprise. En el caso de Mozy Enterprise, el aprovisionamiento es una tarea manual.

###Para aprovisionar cuentas de usuario, realice estos pasos:

1.  Inicie sesión en su inquilino de **Mozy Enterprise**.

2.  Haga clic en **Users** (Usuarios) y luego en **Add New User** (Agregar nuevo usuario).

    ![Usuarios](./media/active-directory-saas-mozy-enterprise-tutorial/IC777317.png "Usuarios")

    >[AZURE.NOTE]La opción **Add New User** (Agregar nuevo usuario) solo se muestra si **Mozy** está seleccionado como proveedor en **Authentication policy** (Directiva de autenticación). Si la autenticación SAML está configurada, los usuarios se agregan automáticamente al iniciar sesión por primera vez con inicio de sesión único.

3.  En el cuadro de diálogo Nuevo usuario, realice los pasos siguientes:

    ![Agregar usuarios](./media/active-directory-saas-mozy-enterprise-tutorial/IC777318.png "Agregar usuarios")

    1.  En la lista **Choose a Group** (Elija un grupo), seleccione un grupo.
    2.  En la lista **Tipo de usuario**, seleccione un tipo.
    3.  En el cuadro de texto **Username** (Nombre de usuario), escriba el nombre de usuario de Azure AD.
    4.  En el cuadro de texto **Email** (Correo electrónico), escriba la dirección de correo electrónico del usuario de Azure AD.
    5.  Seleccione **Send user instruction email** (Enviar correo electrónico al usuario con instrucciones).
    6.  Haga clic en **Add User(s)** (Agregar usuarios).

    >[AZURE.NOTE]Después de crear el usuario, se enviará un correo electrónico al usuario de Azure AD que incluye un vínculo para confirmar la cuenta antes de que se active.

>[AZURE.NOTE]Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Mozy Enterprise ofrecida por Mozy Enterprise para aprovisionar cuentas de usuario de AAD.

##Asignación de usuarios
 
Para probar la configuración, debe asignar los usuarios de Azure AD que quiera que usen su aplicación para concederles acceso a ella.

###Para asignar usuarios a Mozy Enterprise, siga estos pasos:

1.  En el Portal de Azure AD, cree una cuenta de prueba.

2.  En la página de integración de aplicaciones de **Mozy Enterprise**, haga clic en **Asignar usuarios**.

    ![Asignar usuarios](./media/active-directory-saas-mozy-enterprise-tutorial/IC777319.png "Asignar usuarios")

3.  Seleccione su usuario de prueba, haga clic en **Asignar** y luego en **Sí** para confirmar la asignación.

    ![Sí](./media/active-directory-saas-mozy-enterprise-tutorial/IC767830.png "Sí")
  
Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0128_2016-->