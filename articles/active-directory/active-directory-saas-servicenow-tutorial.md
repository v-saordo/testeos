<properties 
    pageTitle="Tutorial: integración de Azure Active Directory con ServiceNow | Microsoft Azure" 
    description="Aprenda a usar ServiceNow con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automático, etc." 
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

#Tutorial: Integración de Azure Active Directory con ServiceNow
  
El objetivo de este tutorial es mostrar la integración de Azure y ServiceNow.
En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción de Azure válida
-   Un inquilino en ServiceNow
  
Después de completar este tutorial, los usuarios de Azure AD asignados a ServiceNow podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de ServiceNow (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
La situación descrita en este tutorial consta de los siguientes bloques de creación:

1.  Habilitación de la integración de aplicaciones para ServiceNow
2.  Configuración del inicio de sesión único
3.  Configuración del aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario](./media/active-directory-saas-servicenow-tutorial/IC769496.png "Escenario")
##Habilitación de la integración de aplicaciones para ServiceNow
  
El objetivo de esta sección es describir cómo habilitar la integración de aplicaciones para ServiceNow.

###Siga estos pasos para habilitar la integración de aplicaciones para ServiceNow:

1.  En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.

    ![Active Directory](./media/active-directory-saas-servicenow-tutorial/IC700993.png "Active Directory")

2.  En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3.  Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

    ![Aplicaciones](./media/active-directory-saas-servicenow-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación](./media/active-directory-saas-servicenow-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Agregar una aplicación de la galería](./media/active-directory-saas-servicenow-tutorial/IC749322.png "Agregar una aplicación de la galería")

6.  En el **cuadro de búsqueda**, escriba **ServiceNow**.

    ![Galería de aplicaciones](./media/active-directory-saas-servicenow-tutorial/IC701016.png "Galería de aplicaciones")

7.  En el panel de resultados, seleccione **ServiceNow** y haga clic en **Completar** para agregar la aplicación.

    ![ServiceNow](./media/active-directory-saas-servicenow-tutorial/IC701017.png "ServiceNow")
##Configuración del inicio de sesión único
  
El objetivo de esta sección es describir cómo habilitar usuarios para que se autentiquen en ServiceNow con su cuenta de Azure AD mediante federación basada en el protocolo SAML.

Como parte de este procedimiento, es necesario cargar un certificado codificado en base 64 en su inquilino de Dropbox para Empresas. Si no está familiarizado con este procedimiento, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

###Siga estos pasos para configurar el inicio de sesión único:

1.  En el Portal de Azure AD clásico, en la página de integración de aplicaciones de **ServiceNow**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-servicenow-tutorial/IC749323.png "Configurar inicio de sesión único")

2.  En la página **¿Cómo desea que los usuarios inicien sesión en ServiceNow?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y luego haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-servicenow-tutorial/IC749324.png "Configurar inicio de sesión único")

3.  En la página **Configurar las opciones de la aplicación**, realice los pasos siguientes:

    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-servicenow-tutorial/IC769497.png "Configurar dirección URL de la aplicación")

    a. En el cuadro de texto **URL de inicio de sesión de ServiceNow**, escriba la dirección URL usada por los usuarios para iniciar sesión en la aplicación ServiceNow (por ejemplo: *https://\<nombreInstancia>.service-now.com*).

    b. En el cuadro de texto **URL del emisor**, escriba la dirección URL usada por los usuarios para iniciar sesión en la aplicación ServiceNow (por ejemplo: *https://\<nombreInstancia>.service-now.com*).

    c. Haga clic en **Siguiente**.

4.  En la página **Configurar el inicio de sesión único automáticamente**, haga clic en **Configurar manualmente esta aplicación para el inicio de sesión único** y luego en **Siguiente**.

    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-servicenow-tutorial/IC7694971.png "Configurar dirección URL de la aplicación")



4.  En la página **Configurar inicio de sesión único en ServiceNow**, haga clic en **Descargar certificado**, guarde el archivo de certificado localmente en el equipo y haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-servicenow-tutorial/IC749325.png "Configurar inicio de sesión único")

1. Inicie sesión en su aplicación ServiceNow como administrador.

2. En el panel de navegación de la izquierda, haga clic en **Properties** (Propiedades).

    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-servicenow-tutorial/IC7694980.png "Configurar dirección URL de la aplicación")


3. En el cuadro de diálogo **Multiple Provider SSO Properties** (Propiedades de SSO de varias proveedores), realice los pasos siguientes:

    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-servicenow-tutorial/IC7694981.png "Configurar dirección URL de la aplicación")

    a. En **Enable multiple provider SSO** (Habilitar SSO de varios proveedores), seleccione **Yes** (Sí).

    b. En **Enable debug logging got the multiple provider SSO integration** (Habilitar registro de depuración para integración de SSO de varios proveedores), seleccione **Yes** (Sí).

    c. En el cuadro de texto **The field on the user table that...** (El campo en la tabla de usuario que...), escriba **user\_name** (nombre\_usuario).

    d. Haga clic en **Guardar**.



1. En el panel de navegación de la izquierda, haga clic en **Certificados x509**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-servicenow-tutorial/IC7694973.png "Configurar inicio de sesión único")


1. En el cuadro de diálogo **Certificados X.509**, haga clic en **Nuevo**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-servicenow-tutorial/IC7694974.png "Configurar inicio de sesión único")


1. En el cuadro de diálogo **Certificados X.509**, realice los pasos siguientes:

    ![Configurar inicio de sesión único](./media/active-directory-saas-servicenow-tutorial/IC7694975.png "Configurar inicio de sesión único")

    a. Haga clic en **Nuevo**.

    b. En el cuadro de texto **Name** (Nombre), escriba el nombre de la configuración (por ejemplo, **TestSAML2.0**).

    c. Seleccione **Active** (Activo).

    d. Como **Format** (Formato), seleccione **PEM**.

    e. Como **Type** (Tipo), seleccione **Trust Store Cert** (Confiar en certificados de almacén).

    f. Cree un archivo codificado en base 64 a partir del certificado descargado.
    > [AZURE.NOTE] Para obtener más información, consulte [How to convert a binary certificate into a text file](http://youtu.be/PlgrzUZ-Y1o) (Conversión de un certificado binario en un archivo de texto).
    
    g. Abra el certificado codificado en base 64 en el Bloc de notas, copie el contenido del mismo en el Portapapeles y luego péguelo en el cuadro de texto **Certificado PEM**.

    h. Haga clic en **Actualizar**.


1. En el panel de navegación de la izquierda, haga clic en **Identity Providers** (Proveedores de identidades).

    ![Configurar inicio de sesión único](./media/active-directory-saas-servicenow-tutorial/IC7694976.png "Configurar inicio de sesión único")

1. En el cuadro de diálogo **Identity Providers** (Proveedores de identidad), haga clic en **New** (Nuevo):

    ![Configurar inicio de sesión único](./media/active-directory-saas-servicenow-tutorial/IC7694977.png "Configurar inicio de sesión único")


1. En el cuadro de diálogo **Identity Providers** (Proveedores de identidades), haga clic en **SAML2 Update1?**:

    ![Configurar inicio de sesión único](./media/active-directory-saas-servicenow-tutorial/IC7694978.png "Configurar inicio de sesión único")


1. En el cuadro de diálogo SAML2 Update1 Properties (Propiedades de SAML2 Update1), realice los pasos siguientes:

    ![Configurar inicio de sesión único](./media/active-directory-saas-servicenow-tutorial/IC7694982.png "Configurar inicio de sesión único")


    En el cuadro de texto **Name** (Nombre), escriba el nombre de la configuración (por ejemplo, **SAML 2.0**).

    b. En el cuadro de texto **User Field** (Campo de usuario), escriba **email** (dirección de correo electrónico).

    c. En el Portal de Azure AD clásico, copie el valor de **Id. de proveedor de identidad** y péguelo en el cuadro de texto **Identity Provider URL** (URL de proveedor de identidades).

    d. En el Portal de Azure AD clásico, copie el valor de **Dirección URL de solicitud de autenticación** y péguelo en el cuadro de texto **Identity Provider's AuthnRequest** (Solicitud de autenticación de proveedor de identidades).

    e. En el Portal de Azure AD clásico, copie el valor de **Dirección URL del servicio de cierre de sesión único** y péguelo en el cuadro de texto **Identity Provider's SingleLogoutRequest** (Solicitud de cierre de sesión única del proveedor de identidades).

    f. En el cuadro de texto **ServiceNow Homepage** (Página de inicio de ServiceNow), escriba la dirección URL de la página de inicio de instancia de ServiceNow.

    > [AZURE.NOTE] La página de inicio de la instancia de ServiceNow es una concatenación de su **URL de inquilino de ServiceNow** y **/navpage.do** (por ejemplo: *https://fabrikam.service-now.com/navpage.do*).
 

    g. En el cuadro de texto **Entity ID / Issuer** (Id. de entidad / emisor), escriba la dirección URL de su inquilino ServiceNow.

    h. En el cuadro de texto **Audience URL** (Dirección URL de audiencia), escriba la dirección URL de su inquilino ServiceNow.

    i. En el cuadro de texto **Protocol Binding for the IDP's SingleLogoutRequest** (Vinculación de protocolo para la solicitud de cierre de sesión único del proveedor de identidades), escriba **urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect**.

    j. En el cuadro de texto NameID Policy (Directiva de Id. de nombres), escriba **urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified**.

    k. Anule la selección de **Create an AuthnContextClass** (Crear AuthnContextClass).

    l. En **AuthnContextClassRef Method** (Método AuthnContextClassRef), escriba **http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password**.

    m. En el cuadro de diálogo **Clock Skew** (Desplazamiento del reloj), escriba **60**.

    n. Como **Single Sign On Script** (Script de inicio de sesión único), seleccione **MultiSSO\_SAML2\_Update1**.

    o. Como **Certificado X.509**, seleccione el certificado que ha creado en el paso anterior.

    p. Haga clic en **Submit** (Enviar).



6. En el Portal de Azure AD clásico, seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-servicenow-tutorial/IC7694990.png "Configurar inicio de sesión único")

7. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-servicenow-tutorial/IC7694991.png "Configurar inicio de sesión único")



##Configuración del aprovisionamiento de usuario
  
El objetivo de esta sección es describir cómo habilitar el aprovisionamiento de cuentas de usuario de Active Directory para ServiceNow.


### Siga estos pasos para configurar el aprovisionamiento de usuario:

1. En el Portal de administración de Azure clásico, en la página de integración de la aplicación **ServiceNow**, haga clic en **Configurar aprovisionamiento de usuarios**. <br><br> ![Aprovisionamiento de usuarios](./media/active-directory-saas-servicenow-tutorial/IC769498.png "Aprovisionamiento de usuarios")


2. En la página **Especifique sus credenciales de ServiceNow para habilitar el aprovisionamiento automático de usuarios**, proporcione los valores de configuración siguientes: Configurar aprovisionamiento de usuarios

     2.1. En el cuadro de texto **Nombre de la instancia de ServiceNow**, escriba el nombre de la instancia de ServiceNow.

     2.2. En el cuadro de texto **Nombre del usuario administrador de ServiceNow**, escriba el nombre de la cuenta de administrador de ServiceNow.

     2.3. En el cuadro de texto **Contraseña de administración de ServiceNow**, escriba la contraseña para esta cuenta.

     2.4. Haga clic en **Validar** para comprobar la configuración.

     2.5. Haga clic en el botón **Siguiente** para abrir la página **Pasos siguientes**.

     2.6. Si quiere aprovisionar todos los usuarios para esta aplicación, seleccione "**Aprovisionar automáticamente todas las cuentas del directorio en esta aplicación**". <br><br> ![Pasos siguientes](./media/active-directory-saas-servicenow-tutorial/IC698804.png "Pasos siguientes")

     2.7. En la página **Pasos siguientes**, haga clic en **Completar** para guardar la configuración.











##Asignación de usuarios
  
Para probar la configuración, tiene que conceder acceso, mediante su asignación, a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

###Siga estos pasos para asignar usuarios a ServiceNow:

1.  En el Portal de Azure AD, cree una cuenta de prueba.

2.  En la página de integración de aplicaciones de **ServiceNow**, haga clic en **Asignar usuarios**.

    ![Asignar usuarios](./media/active-directory-saas-servicenow-tutorial/IC769499.png "Asignar usuarios")

3.  Seleccione su usuario de prueba, haga clic en **Asignar** y luego en **Sí** para confirmar la asignación.

    ![Sí](./media/active-directory-saas-servicenow-tutorial/IC767830.png "Sí")
  
Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).


## Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!---HONumber=AcomDC_0128_2016-->