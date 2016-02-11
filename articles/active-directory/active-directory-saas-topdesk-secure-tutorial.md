<properties 
    pageTitle="Tutorial: integración de Azure Active Directory con TOPdesk - Secure | Microsoft Azure" description="Aprenda cómo usar TOPdesk - Secure con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc." 
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

#Tutorial: integración de Azure Active Directory con TOPdesk - Secure
  
El objetivo de este tutorial es mostrar la integración de Azure y TOPdesk - Secure.  
En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción de Azure válida
-   Una suscripción habilitada para el inicio de sesión único en TOPdesk - Secure
  
Después de completar este tutorial, los usuarios de Azure AD que ha asignado a TOPdesk - Secure podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de TOPdesk - Secure (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
La situación descrita en este tutorial consta de los siguientes bloques de creación:

1.  Habilitación de la integración de aplicaciones para TOPdesk - Secure
2.  Configuración del inicio de sesión único
3.  Configuración del aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario](./media/active-directory-saas-topdesk-secure-tutorial/IC790596.png "Escenario")

##Habilitación de la integración de aplicaciones para TOPdesk - Secure
  
El objetivo de esta sección es describir cómo habilitar la integración de las aplicaciones para TOPdesk - Secure.

###Siga estos pasos para habilitar la integración de aplicaciones para TOPdesk - Secure:

1.  En el panel de navegación izquierdo del Portal de administración de Azure, haga clic en **Active Directory**.

    ![Active Directory](./media/active-directory-saas-topdesk-secure-tutorial/IC700993.png "Active Directory")

2.  En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3.  Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

    ![Aplicaciones](./media/active-directory-saas-topdesk-secure-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación](./media/active-directory-saas-topdesk-secure-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Agregar una aplicación de la galería](./media/active-directory-saas-topdesk-secure-tutorial/IC749322.png "Agregar una aplicación de la galería")

6.  En el **cuadro de búsqueda**, escriba **TOPdesk - Secure**.

    ![Galería de aplicaciones](./media/active-directory-saas-topdesk-secure-tutorial/IC790597.png "Galería de aplicaciones")

7.  En el panel de resultados, seleccione **TOPdesk - Secure** y, luego, haga clic en **Completa** para agregar la aplicación.

    ![TOPdesk - Secure](./media/active-directory-saas-topdesk-secure-tutorial/IC791933.png "TOPdesk - Secure")

##Configuración del inicio de sesión único
  
El objetivo de esta sección es describir cómo se habilita la autenticación de los usuarios en TOPdesk - Secure con su cuenta de Azure AD usando el protocolo SAML basado en la federación.  
La configuración del inicio de sesión único para TOPdesk - Secure requiere cargar un archivo de icono de logotipo. Para obtener el archivo de icono, póngase en contacto con el equipo de soporte técnico de TOPdesk.

###Siga estos pasos para configurar el inicio de sesión único:

1.  Inicie sesión en su sitio de la compañía de **TOPdesk - Secure** como administrador.

2.  En el menú **TOPdesk**, haga clic en **Configuración**.

    ![Settings](./media/active-directory-saas-topdesk-secure-tutorial/IC790598.png "Settings")

3.  Haga clic en **Login Settings** (Configuración de inicio de sesión).

    ![Configuración de inicio de sesión](./media/active-directory-saas-topdesk-secure-tutorial/IC790599.png "Configuración de inicio de sesión")

4.  Expanda el menú **Login Settings** (Configuración de inicio de sesión) y luego haga clic en **General**.

    ![General](./media/active-directory-saas-topdesk-secure-tutorial/IC790600.png "General")

5.  En la sección **Seguro** de la sección de configuración **Inicio de sesión SAML**, realice los pasos siguientes:

    ![Configuración técnica](./media/active-directory-saas-topdesk-secure-tutorial/IC790855.png "Configuración técnica")

    1.  Haga clic en **Download** (Descargar) para descargar el archivo de metadatos público y luego guárdelo localmente en el equipo.
    2.  Abra el archivo de metadatos y luego busque el nodo **AssertionConsumerService**.
        ![Servicio del consumidor de aserción](./media/active-directory-saas-topdesk-secure-tutorial/IC790856.png "Servicio del consumidor de aserción")
    3.  Copie el valor **AssertionConsumerService**.  

        >[AZURE.NOTE]Necesitará el valor de la sección **Configurar dirección URL de la aplicación** más adelante en este tutorial.

6.  En otra ventana del explorador web, inicie sesión en su portal de **Azure Active Directory** como administrador.

7.  En la página de integración de aplicaciones de **TOPdesk - Secure**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-topdesk-secure-tutorial/IC790602.png "Configurar inicio de sesión único")

8.  En la página **¿Cómo desea que los usuarios inicien sesión en TOPdesk - Secure?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y luego haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-topdesk-secure-tutorial/IC790603.png "Configurar inicio de sesión único")

9.  En la página **Configurar dirección URL de la aplicación**, realice los pasos siguientes:

    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-topdesk-secure-tutorial/IC790604.png "Configurar dirección URL de la aplicación")

    1.  En el cuadro de texto **URL de inicio de sesión de TOPdesk - Secure**, escriba la dirección URL usada por los usuarios para iniciar sesión en su aplicación TOPdesk - Secure (por ejemplo, "*https://qssolutions.topdesk.net*").
   2.  En el cuadro de texto**URL de respuesta de TOPdesk – Public**, pegue la **URL de AssertionConsumerService de TOPdesk - Secure**(por ejemplo, "*https://qssolutions.topdesk.net/tas/public/login/saml*")
   3.  Haga clic en **Siguiente**.

10. En la página **Configuración de inicio de sesión único en TOPdesk - Secure**, para descargar su archivo de metadatos, haga clic en **Descargar metadatos** y luego guarde el archivo localmente en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-topdesk-secure-tutorial/IC790605.png "Configurar inicio de sesión único")

11. Lleve a cabo los siguientes pasos para crear un archivo de certificado:

    ![Certificate](./media/active-directory-saas-topdesk-secure-tutorial/IC790606.png "Certificate")

    1.  Abra el archivo de metadatos descargado.
    2.  Expanda el nodo **RoleDescriptor** que tiene un **xsi:type** de **fed:ApplicationServiceType**.
    3.  Copie el valor del nodo **X509Certificate**.
    4.  Guarde el valor de **X509Certificate** copiado localmente en el equipo en un archivo.

12. En el sitio de compañía de TOPdesk - Secure, en el menú **TOPdesk**, haga clic en **Configuración**.

    ![Settings](./media/active-directory-saas-topdesk-secure-tutorial/IC790598.png "Settings")

13. Haga clic en **Login Settings** (Configuración de inicio de sesión).

    ![Configuración de inicio de sesión](./media/active-directory-saas-topdesk-secure-tutorial/IC790599.png "Configuración de inicio de sesión")

14. Expanda el menú **Login Settings** (Configuración de inicio de sesión) y luego haga clic en **General**.

    ![General](./media/active-directory-saas-topdesk-secure-tutorial/IC790600.png "General")

15. En la sección **Public** (Público), haga clic en **Add** (Agregar).

    ![Agregar](./media/active-directory-saas-topdesk-secure-tutorial/IC790607.png "Agregar")

16. En la página de diálogo del **SAML configuration assistant** (Asistente de configuración de SAML), realice los siguientes pasos:

    ![Asistente de configuración de SAML](./media/active-directory-saas-topdesk-secure-tutorial/IC790608.png "Asistente de configuración de SAML")

    1.  Para cargar el archivo de metadatos descargado en **Federation Metadata** (Metadatos de federación), haga clic en **Browse** (Examinar).
    2.  Para cargar el archivo del certificado, en **Certificate (RSA)** (Certificado [RSA]), haga clic en **Browse** (Examinar).
    3.  Para cargar el archivo de logotipo que obtuvo del equipo de soporte técnico de TOPdesk, en el **icono del logotipo**, haga clic en **Browse** (Examinar).
    4.  En el cuadro de texto **User name attribute** (Atributo de nombre de usuario), escriba **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
    5.  En el cuadro de texto **Display name** (Nombre para mostrar), escriba un nombre para su configuración.
    6.  Haga clic en **Guardar**.

17. En el Portal de Azure AD, seleccione la confirmación de configuración de inicio de sesión único y, a continuación, haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-topdesk-secure-tutorial/IC790609.png "Configurar inicio de sesión único")

##Configuración del aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD inicien sesión en TOPdesk - Secure, deben aprovisionarse en TOPdesk - Secure.  
En el caso de TOPdesk - Secure, el aprovisionamiento es una tarea manual.

###Siga estos pasos para configurar el aprovisionamiento de usuario:

1.  Inicie sesión en su sitio de la compañía de **TOPdesk - Secure** como administrador.

2.  En el menú de la parte superior, haga clic en**TOPdesk > Nuevo > Archivos de soporte > Operador**.

    ![Operador](./media/active-directory-saas-topdesk-secure-tutorial/IC790610.png "Operador")

3.  En el cuadro de diálogo **Nuevo operador**, realice los pasos siguientes:

    ![Nuevo operador](./media/active-directory-saas-topdesk-secure-tutorial/IC790611.png "Nuevo operador")

    1.  Haga clic en la pestaña General.
    2.  En el cuadro de texto **Apellido** de la sección **General**, escriba el apellido de una cuenta válida de Azure Active Directory que quiera aprovisionar.
    3.  Seleccione un **Sitio** para la cuenta en la sección **Ubicación**.
    4.  En el cuadro de texto **Nombre de inicio de sesión** de la sección **Inicio de sesión de TOPdesk**, escriba un nombre de inicio de sesión para el usuario.
    5.  Haga clic en **Guardar**.

>[AZURE.NOTE]Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de TOPdesk - Secure ofrecida por TOPdesk - Secure para aprovisionar cuentas de usuario de AAD.

##Asignación de usuarios
  
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

###Para asignar usuarios a TOPdesk - Secure, lleve a cabo los siguientes pasos:

1.  En el portal de Azure AD, cree una cuenta de prueba.

2.  En la página de integración de la aplicación **TOPdesk - Secure**, haga clic en **Asignar usuarios**.

    ![Asignar usuarios](./media/active-directory-saas-topdesk-secure-tutorial/IC790612.png "Asignar usuarios")

3.  Seleccione su usuario de prueba, haga clic en **Asignar** y luego en **Sí** para confirmar la asignación.

    ![Sí](./media/active-directory-saas-topdesk-secure-tutorial/IC767830.png "Sí")
  
Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0114_2016-->