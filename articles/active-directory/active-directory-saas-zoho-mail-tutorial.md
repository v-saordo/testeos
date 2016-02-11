<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con Zoho Mail | Microsoft Azure" 
    description="Aprenda cómo usar Zoho Mail con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc." 
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

#Tutorial: Integración de Azure Active Directory con Zoho Mail
  
El objetivo de este tutorial es mostrar la integración de Azure y Zoho Mail. En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción de Azure válida
-   Un inquilino de correo Zoho Mail
  
Después de completar este tutorial, los usuarios de Azure AD que ha asignado a Zoho Mail podrá realizar un inicio de sesión único en la aplicación en el sitio de la compañía de Zoho Mail (inicio de sesión iniciado por el proveedor del servicio) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md)
  
La situación descrita en este tutorial consta de los siguientes bloques de creación:

1.  Habilitación de la integración de aplicaciones para Zoho Mail
2.  Configuración del inicio de sesión único
3.  Configuración del aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario](./media/active-directory-saas-zoho-mail-tutorial/IC789600.png "Escenario")

##Habilitación de la integración de aplicaciones para Zoho Mail
  
El objetivo de esta sección es describir cómo habilitar la integración de las aplicaciones para Zoho Mail.

###Siga estos pasos para habilitar la integración de aplicaciones para Zoho Mail:

1.  En el panel de navegación izquierdo del Portal de administración de Azure, haga clic en **Active Directory**.

    ![Active Directory](./media/active-directory-saas-zoho-mail-tutorial/IC700993.png "Active Directory")

2.  En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3.  Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

    ![Aplicaciones](./media/active-directory-saas-zoho-mail-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación](./media/active-directory-saas-zoho-mail-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Agregar una aplicación de la galería](./media/active-directory-saas-zoho-mail-tutorial/IC749322.png "Agregar una aplicación de la galería")

6.  En el **cuadro de búsqueda**, escriba **Zoho Mail**.

    ![Galería de aplicaciones](./media/active-directory-saas-zoho-mail-tutorial/IC789601.png "Galería de aplicaciones")

7.  En el panel de resultados, seleccione **Zoho Mail** y, luego, haga clic en **Completa** para agregar la aplicación.

    ![Zoho Mail](./media/active-directory-saas-zoho-mail-tutorial/IC789602.png "Zoho Mail")

##Configuración del inicio de sesión único
  
El objetivo de esta sección es describir cómo se habilita la autenticación de los usuarios en Zoho Mail con su cuenta de Azure AD usando el protocolo SAML basado en la federación. Como parte de este procedimiento, es necesario crear un archivo de certificado codificado en base 64. Si no está familiarizado con este procedimiento, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

###Siga estos pasos para configurar el inicio de sesión único:

1.  En el portal de Azure AD, en la página de integración de aplicaciones de **Zoho Mail**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-zoho-mail-tutorial/IC789603.png "Configurar inicio de sesión único")

2.  En la página **¿Cómo desea que los usuarios inicien sesión en Zoho Mail?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y, luego , haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-zoho-mail-tutorial/IC789604.png "Configurar inicio de sesión único")

3.  En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto de **dirección URL de inicio de sesión de Zoho Mail**, escriba su dirección URL con el siguiente patrón "**http://company.ZohoMail.com*" y luego haga clic en **Siguiente**.

    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-zoho-mail-tutorial/IC789605.png "Configurar dirección URL de la aplicación")

4.  En la página **Configuración de inicio de sesión único en Zoho Mail**, haga clic en **Descargar certificado** y luego guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-zoho-mail-tutorial/IC789606.png "Configurar inicio de sesión único")

5.  En otra ventana del explorador web, inicie sesión en su sitio de la compañía de Zoho Mail como administrador.

6.  Vaya al **Panel de control**.

    ![Panel de control](./media/active-directory-saas-zoho-mail-tutorial/IC789607.png "Panel de control")

7.  Haga clic en la pestaña **Autenticación SAML**.

    ![Autenticación SAML](./media/active-directory-saas-zoho-mail-tutorial/IC789608.png "Autenticación SAML")

8.  En la sección **Detalles de la autenticación SAML**, realice los pasos siguientes:

    ![Detalles de autenticación SAML](./media/active-directory-saas-zoho-mail-tutorial/IC789609.png "Detalles de autenticación SAML")

    1.  En el portal de Azure, en la página de diálogo **Configurar inicio de sesión único en Zoho Mail**, copie el valor de **Dirección URL del inicio de sesión remoto** y péguelo en el cuadro de texto **Dirección URL de inicio de sesión**.
    2.  En el portal de Azure, en la página de diálogo **Configurar inicio de sesión único en Zoho Mail**, copie el valor de **Dirección URL de cierre de sesión remoto** y péguelo en el cuadro de texto **Dirección URL de cierre de sesión**.
    3.  En el portal de Azure, en la página de diálogo **Configurar inicio de sesión único en Zoho Mail**, copie el valor de **Cambiar dirección URL de contraseña** y péguelo en el cuadro de texto **Cambiar dirección URL de contraseña**.
    4.  Cree un archivo **codificado en base 64** a partir del certificado descargado.  

        >[AZURE.TIP]Para obtener más información, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

    5.  Abra el certificado codificado en base 64 en el Bloc de notas, copie el contenido del mismo en el Portapapeles y, a continuación, péguelo en el cuadro de texto **PublicKey**
    6.  Como **Algoritmo** seleccione **RSA**.
    7.  Haga clic en **Aceptar**.

9.  En el portal de Azure AD, seleccione la confirmación de configuración de inicio de sesión único y luego haga clic en **Completa** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-zoho-mail-tutorial/IC789610.png "Configurar inicio de sesión único")

##Configuración del aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD inicien sesión en Zoho Mail, deben aprovisionarse a Zoho Mail. En el caso de Zoho Mail, el aprovisionamiento es una tarea manual.

###Para aprovisionar cuentas de usuario, realice los siguientes pasos:

1.  Inicie sesión en su sitio de la compañía de **Zoho Mail** como administrador.

2.  Vaya a **Panel de Control > Correo y documentos**.

3.  Vaya a **Detalles del usuario > Agregar usuario**.

    ![Agregar usuario](./media/active-directory-saas-zoho-mail-tutorial/IC789611.png "Agregar usuario")

4.  En el cuadro de diálogo **Agregar usuarios**, realice los pasos siguientes:

    ![Agregar usuario](./media/active-directory-saas-zoho-mail-tutorial/IC789612.png "Agregar usuario")

    1.  Escriba el **Nombre**, **Apellido**, **Id. de correo electrónico**, **Contraseña** de una cuenta de Azure Active Directory válida que quiera aprovisionar en los cuadros de texto relacionados.
    2.  Haga clic en **Aceptar**.  

        >[AZURE.NOTE]El titular de la cuenta de Azure Active Directory recibirá un mensaje de correo electrónico con un vínculo para confirmar la cuenta antes de que se active.

>[AZURE.NOTE]Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Zoho Mail ofrecida por Zoho Mail para aprovisionar cuentas de usuario de AAD.

##Asignación de usuarios
  
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

###Para asignar usuarios a Zoho Mail, lleve a cabo los siguientes pasos:

1.  En el portal de Azure AD, cree una cuenta de prueba.

2.  En la página de integración de la aplicación **Zoho Mail **, haga clic en **Asignar usuarios**.

    ![Asignar usuarios](./media/active-directory-saas-zoho-mail-tutorial/IC789613.png "Asignar usuarios")

3.  Seleccione su usuario de prueba, haga clic en **Asignar** y luego en **Sí** para confirmar la asignación.

    ![Sí](./media/active-directory-saas-zoho-mail-tutorial/IC767830.png "Sí")
  
Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0114_2016-->