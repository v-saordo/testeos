<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con RunMyProcess | Microsoft Azure" 
    description="Aprenda cómo usar RunMyProcess con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc." 
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

#Tutorial: Integración de Azure Active Directory con RunMyProcess
  
El objetivo de este tutorial es mostrar la integración de Azure y RunMyProcess. En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción de Azure válida
-   Un inquilino de RunMyProcess
  
Después de completar este tutorial, los usuarios de Azure AD asignados a RunMyProcess podrán realizar un inicio de sesión único en la aplicación en el sitio de empresa de RunMyProcess (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
La situación descrita en este tutorial consta de los siguientes bloques de creación:

1.  Habilitación de la integración de aplicaciones para RunMyProcess
2.  Configuración del inicio de sesión único
3.  Configuración del aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario](./media/active-directory-saas-runmyprocess-tutorial/IC789614.png "Escenario")
##Habilitación de la integración de aplicaciones para RunMyProcess
  
El objetivo de esta sección es describir cómo habilitar la integración de las aplicaciones para RunMyProcess.

###Siga estos pasos para habilitar la integración de aplicaciones para RunMyProcess:

1.  En el panel de navegación izquierdo del Portal de administración de Azure, haga clic en **Active Directory**.

    ![Active Directory](./media/active-directory-saas-runmyprocess-tutorial/IC700993.png "Active Directory")

2.  En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3.  Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

    ![Aplicaciones](./media/active-directory-saas-runmyprocess-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación](./media/active-directory-saas-runmyprocess-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Agregar una aplicación de la galería](./media/active-directory-saas-runmyprocess-tutorial/IC749322.png "Agregar una aplicación de la galería")

6.  En el **cuadro de búsqueda**, escriba **RunMyProcess**.

    ![Galería de aplicaciones](./media/active-directory-saas-runmyprocess-tutorial/IC789615.png "Galería de aplicaciones")

7.  En el panel de resultados, seleccione **RunMyProcess** y luego haga clic en **Completar** para agregar la aplicación.

    ![RunMyProcess](./media/active-directory-saas-runmyprocess-tutorial/IC789616.png "RunMyProcess")
##Configuración del inicio de sesión único
  
El objetivo de esta sección es describir cómo se habilita la autenticación de los usuarios en RunMyProcess con su cuenta de Azure AD usando el protocolo SAML basado en la federación. Como parte de este procedimiento, es necesario crear un archivo de certificado codificado en base 64. Si no está familiarizado con este procedimiento, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

###Siga estos pasos para configurar el inicio de sesión único:

1.  En el Portal de Azure AD, en la página de integración de aplicaciones de **RunMyProcess**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-runmyprocess-tutorial/IC789617.png "Configurar inicio de sesión único")

2.  En la página **¿Cómo desea que los usuarios inicien sesión en RunMyProcess?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y luego haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-runmyprocess-tutorial/IC789622.png "Configurar inicio de sesión único")

3.  En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto de **URL de inicio de sesión de RunMyProcess**, escriba su dirección URL con el siguiente patrón "**http://company.runmyprocess.com*" y luego haga clic en **Siguiente**.

    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-runmyprocess-tutorial/IC789623.png "Configurar dirección URL de la aplicación")

4.  En la página **Configuración de inicio de sesión único en RunMyProcess**, haga clic en **Descargar certificado** y luego guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-runmyprocess-tutorial/IC789624.png "Configurar inicio de sesión único")

5.  En otra ventana del explorador web, inicie sesión en el sitio RunMyProcess de la compañía como administrador.

6.  Vaya a **Cuenta > Configuración**.

    ![Cuenta](./media/active-directory-saas-runmyprocess-tutorial/IC789625.png "Cuenta")

7.  Haga clic en la pestaña **Método de autenticación**.

8.  En la sección **Método de autenticación**, realice los pasos siguientes:

    ![SSO](./media/active-directory-saas-runmyprocess-tutorial/IC789626.png "SSO")

    1.  Como **Método**, seleccione**SSO con Samlv2**.
    2.  En el portal de Azure, en la página de diálogo **Configurar inicio de sesión único en RunMyProcess**, copie el valor de **Dirección URL de SAML SSO** y péguelo en el cuadro de texto **Redireccionamiento de SSO**.
    3.  En el portal de Azure, en la página de diálogo **Configurar inicio de sesión único en RunMyProcess**, copie el valor de **Dirección URL de cierre de sesión único** y péguelo en el cuadro de texto **Redireccionamiento de cierre de sesión**.
    4.  En el cuadro de texto **Nombre de id. de formato**, escriba **urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress**.
    5.  Cree un archivo **codificado en base 64** a partir del certificado descargado.  

        >[AZURE.TIP]Para obtener más información, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

    6.  Abra el certificado codificado en base 64 en el Bloc de notas, copie el contenido del mismo en el Portapapeles y luego péguelo en el cuadro de texto **Certificado**.
    7.  Haga clic en **Guardar**.

9.  En el Portal de Azure AD, seleccione la confirmación de configuración de inicio de sesión único y, a continuación, haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-runmyprocess-tutorial/IC789627.png "Configurar inicio de sesión único")
##Configuración del aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD inicien sesión en RunMyProcess, deben aprovisionarse en RunMyProcess. En el caso de RunMyProcess, el aprovisionamiento es una tarea manual.

###Para aprovisionar cuentas de usuario, realice los siguientes pasos:

1.  Inicie sesión en su sitio de la compañía de **RunMyProcess** como administrador.

2.  Vaya a **Cuenta > Usuarios** y luego haga clic en **Nuevo usuario**.

    ![Nuevo usuario](./media/active-directory-saas-runmyprocess-tutorial/IC789631.png "Nuevo usuario")

3.  En la sección **Configuración del usuario**, lleve a cabo estos pasos:

    ![Perfil](./media/active-directory-saas-runmyprocess-tutorial/IC789632.png "Perfil")

    1.  Escriba el **Nombre** y la **Dirección de correo electrónico** de una cuenta de AAD válida que quiera aprovisionar en los cuadros de texto correspondientes.
    2.  Seleccione un **Lenguaje IDE**, un **Lenguaje** y un **Perfil**.
    3.  Seleccione **Enviarme mensaje de correo electrónico de creación de cuenta**.
    4.  Haga clic en **Guardar**.

>[AZURE.NOTE]Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de RunMyProcess que proporcione RunMyProcess para aprovisionar cuentas de usuario de Azure Active Directory.

##Asignación de usuarios
  
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

###Para asignar usuarios a RunMyProcess, lleve a cabo los siguientes pasos:

1.  En el portal de Azure AD, cree una cuenta de prueba.

2.  En la página de integración de aplicaciones de **RunMyProcess **, haga clic en **Asignar usuarios**.

    ![Asignar usuarios](./media/active-directory-saas-runmyprocess-tutorial/IC789633.png "Asignar usuarios")

3.  Seleccione su usuario de prueba, haga clic en **Asignar** y luego en **Sí** para confirmar la asignación.

    ![Sí](./media/active-directory-saas-runmyprocess-tutorial/IC767830.png "Sí")
  
Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0114_2016-->