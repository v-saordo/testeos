<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con Adobe EchoSign | Microsoft Azure" 
    description="Aprenda a usar Adobe EchoSign con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc." 
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

#Tutorial: Integración de Azure Active Directory con Adobe EchoSign

El objetivo de este tutorial es mostrar la integración de Azure y Adobe EchoSign. En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción de Azure válida
-   Una suscripción habilitada para el inicio de sesión único en Adobe EchoSign

Después de completar este tutorial, los usuarios de Azure AD que ha asignado a Adobe EchoSign podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de Adobe EchoSign (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1.  Habilitación de la integración de aplicaciones para Adobe EchoSign
2.  Configuración del inicio de sesión único
3.  Configuración del aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario](./media/active-directory-saas-adobe-echosign-tutorial/IC789511.png "Escenario")
##Habilitación de la integración de aplicaciones para Adobe EchoSign

El objetivo de esta sección es describir cómo habilitar la integración de aplicaciones para Adobe EchoSign.

###Siga estos pasos para habilitar la integración de aplicaciones para Adobe EchoSign:

1.  En el panel de navegación izquierdo del Portal de administración de Azure, haga clic en **Active Directory**.

    ![Active Directory](./media/active-directory-saas-adobe-echosign-tutorial/IC700993.png "Active Directory")

2.  En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3.  Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

    ![Aplicaciones](./media/active-directory-saas-adobe-echosign-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación](./media/active-directory-saas-adobe-echosign-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Agregar una aplicación de la galería](./media/active-directory-saas-adobe-echosign-tutorial/IC749322.png "Agregar una aplicación de la galería")

6.  En el cuadro **Buscar**, escriba **Adobe EchoSign**.

    ![Galería de aplicaciones](./media/active-directory-saas-adobe-echosign-tutorial/IC789514.png "Galería de aplicaciones")

7.  En el panel de resultados, seleccione **Adobe EchoSign** y, luego, haga clic en **Completar** para agregar la aplicación.

    ![Adobe EchoSign](./media/active-directory-saas-adobe-echosign-tutorial/IC789515.png "Adobe EchoSign")
##Configuración del inicio de sesión único

El objetivo de esta sección es describir cómo habilitar usuarios para que se autentiquen en Adobe EchoSign con su cuenta de Azure AD mediante federación basada en el protocolo SAML. Como parte de este procedimiento, se requiere crear un archivo de certificado codificado en base 64. Si no está familiarizado con este procedimiento, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

###Siga estos pasos para configurar el inicio de sesión único:

1.  En el Portal de Azure AD, en la página de integración de aplicaciones de **Adobe EchoSign**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-adobe-echosign-tutorial/IC789516.png "Configurar inicio de sesión único")

2.  En la página **¿Cómo desea que los usuarios inicien sesión en Adobe EchoSign?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y, a continuación, haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-adobe-echosign-tutorial/IC789517.png "Configurar inicio de sesión único")

3.  En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto **URL de inicio de sesión de Adobe EchoSign**, escriba su dirección URL con el siguiente patrón "**https://company.echosign.com/*" y, a continuación, haga clic en **Siguiente**.

    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-adobe-echosign-tutorial/IC789518.png "Configurar dirección URL de la aplicación")

4.  En la página **Configuración de inicio de sesión único en Adobe EchoSign**, haga clic en **Descargar certificado** y, a continuación, guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-adobe-echosign-tutorial/IC789519.png "Configurar inicio de sesión único")

5.  En otra ventana del explorador web, inicie sesión en el sitio de la compañía de Adobe EchoSign como administrador.

6.  En el menú de la parte superior, haga clic en **Account** (Cuenta) y, a continuación, en el panel de navegación de la izquierda, haga clic en **SAML Settings** (Configuración de SAML) en **Account Settings** (Configuración de la cuenta).

    ![Cuenta](./media/active-directory-saas-adobe-echosign-tutorial/IC789520.png "Cuenta")

7.  En la sección de configuración de SAML, lleve a cabo estos pasos:

    ![Configuración de SAML](./media/active-directory-saas-adobe-echosign-tutorial/IC789521.png "Configuración de SAML")

    1.  En **SAML Mode** (Modo SAML), seleccione **SAML Mandatory** (SAML obligatorio).
    2.  Seleccione **Allow EchoSign Account Administrators to log in using their EchoSign Credentials** (Permitir a los administradores de cuentas de EchoSign iniciar sesión con sus credenciales de EchoSign).
    3.  En **User Creation** (Creación de usuario), seleccione **Automatically add users authenticated through SAML** (Agregar automáticamente usuarios autenticados a través de SAML).

8.  Continúe con los siguientes pasos:

    ![Configuración de SAML](./media/active-directory-saas-adobe-echosign-tutorial/IC789522.png "Configuración de SAML")

    1.  En el Portal de Azure, en la página de diálogo **Configurar inicio de sesión único en Adobe EchoSign**, copie el valor de **Id. de entidad ** y péguelo en el cuadro de texto **Id. de entidad de IdP**.
    2.  En el Portal de Azure, en la página de diálogo **Configurar inicio de sesión único en Adobe EchoSign**, copie el valor de **Dirección URL de inicio de sesión remoto** y péguelo en el cuadro de texto **Dirección URL de inicio de sesión de IdP**.
    3.  En el Portal de Azure, en la página de diálogo **Configurar inicio de sesión único en Adobe EchoSign**, copie el valor de **Dirección URL de cierre de sesión remoto** y péguelo en el cuadro de texto **Dirección URL de cierre de sesión de IdP**.
    4.  Cree un archivo **codificado en base 64** a partir del certificado descargado.  

		>[AZURE.TIP]Para obtener más información, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).
    5.  Abra el certificado codificado en base 64 en el Bloc de notas, copie el contenido del mismo en el Portapapeles y, a continuación, péguelo en el cuadro de texto **Certificado IdP**.
    6.  Haga clic en **Guardar cambios**.

9.  En el portal de Azure AD, seleccione la confirmación de configuración de inicio de sesión único y luego haga clic en **Completa** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-adobe-echosign-tutorial/IC789523.png "Configurar inicio de sesión único")
##Configuración del aprovisionamiento de usuario

Para permitir que los usuarios de Azure AD inicien sesión en Adobe EchoSign, deben aprovisionarse en Adobe EchoSign. En el caso de Adobe EchoSign, el aprovisionamiento es una tarea manual.

###Para aprovisionar cuentas de usuario, realice estos pasos:

1.  Inicie sesión en el sitio de la compañía de **Adobe EchoSign** como administrador.

2.  En el menú de la parte superior, haga clic en **Account** (Cuenta) y, a continuación, en el panel de navegación de la izquierda, haga clic en **Users & Groups** (Usuarios y grupos) y en **Create a new user** (Crear nuevo usuario).

    ![Cuenta](./media/active-directory-saas-adobe-echosign-tutorial/IC789524.png "Cuenta")

3.  En la sección **Create New User** (Crear nuevo usuario), lleve a cabo estos pasos:

    ![Crear usuario](./media/active-directory-saas-adobe-echosign-tutorial/IC789525.png "Crear usuario")

    1.  Escriba **Email Address** (Dirección de correo electrónico), **First Name** (Nombre) y **Last Name** (Apellido) de una cuenta de AAD válida que desee aprovisionar en los cuadros de texto correspondientes.
    2.  Haga clic en **Create User** (Crear usuario).

		>[AZURE.NOTE]El titular de la cuenta de Azure Active Directory recibirá un mensaje de correo electrónico con un vínculo para confirmar la cuenta antes de que se active.

>[AZURE.NOTE]Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Adobe EchoSign ofrecida por Adobe EchoSign para aprovisionar cuentas de usuario de AAD.

##Asignación de usuarios

Para probar la configuración, tiene que conceder acceso, mediante su asignación, a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

###Para asignar usuarios a Adobe EchoSign, lleve a cabo los siguientes pasos:

1.  En el Portal de Azure AD, cree una cuenta de prueba.

2.  En la página de integración de aplicaciones de **Adobe EchoSign**, haga clic en **Asignar usuarios**.

    ![Asignar usuarios](./media/active-directory-saas-adobe-echosign-tutorial/IC789526.png "Asignar usuarios")

3.  Seleccione su usuario de prueba, haga clic en **Asignar** y luego en **Sí** para confirmar la asignación.

    ![Sí](./media/active-directory-saas-adobe-echosign-tutorial/IC767830.png "Sí")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0121_2016-->