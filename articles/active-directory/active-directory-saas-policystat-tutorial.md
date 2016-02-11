<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con PolicyStat | Microsoft Azure" 
    description="Aprenda cómo usar PolicyStat con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc." 
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

#Tutorial: Integración de Azure Active Directory con PolicyStat
  
El objetivo de este tutorial es mostrar la integración de Azure y PolicyStat. En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción de Azure válida
-   Un inquilino de PolicyStat
  
Después de completar este tutorial, los usuarios de Azure AD asignados a PolicyStat podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de PolicyStat (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
La situación descrita en este tutorial consta de los siguientes bloques de creación:

1.  Habilitación de la integración de aplicaciones para PolicyStat
2.  Configuración del inicio de sesión único
3.  Configuración del aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario](./media/active-directory-saas-policystat-tutorial/IC808662.png "Escenario")
##Habilitación de la integración de aplicaciones para PolicyStat
  
El objetivo de esta sección es describir cómo habilitar la integración de las aplicaciones para PolicyStat.

###Siga estos pasos para habilitar la integración de aplicaciones para PolicyStat:

1.  En el panel de navegación izquierdo del Portal de administración de Azure, haga clic en **Active Directory**.

    ![Active Directory](./media/active-directory-saas-policystat-tutorial/IC700993.png "Active Directory")

2.  En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3.  Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

    ![Aplicaciones](./media/active-directory-saas-policystat-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación](./media/active-directory-saas-policystat-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Agregar una aplicación de la galería](./media/active-directory-saas-policystat-tutorial/IC749322.png "Agregar una aplicación de la galería")

6.  En el **cuadro de búsqueda**, escriba **PolicyStat**.

    ![Galería de aplicaciones](./media/active-directory-saas-policystat-tutorial/IC808627.png "Galería de aplicaciones")

7.  En el panel de resultados, seleccione **PolicyStat** y luego haga clic en **Completar** para agregar la aplicación.

    ![PolicyStat](./media/active-directory-saas-policystat-tutorial/IC810430.png "PolicyStat")
##Configuración del inicio de sesión único
  
El objetivo de esta sección es describir cómo se habilita la autenticación de los usuarios en PolicyStat con su cuenta de Azure AD usando el protocolo SAML basado en la federación. La aplicación PolicyStat espera las aserciones de SAML en un formato específico, que requiere que se agreguen asignaciones de atributos personalizados a la configuración de los **atributos del token de SAML**. La siguiente captura de pantalla le muestra un ejemplo de esto.

![Atributos](./media/active-directory-saas-policystat-tutorial/IC808628.png "Atributos")

###Siga estos pasos para configurar el inicio de sesión único:

1.  En el portal de Azure AD, en la página de integración de aplicaciones de **PolicyStat**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único **.

    ![Configurar inicio de sesión único](./media/active-directory-saas-policystat-tutorial/IC808629.png "Configurar inicio de sesión único")

2.  En la página **¿Cómo desea que los usuarios inicien sesión en PolicyStat?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y luego haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-policystat-tutorial/IC808630.png "Configurar inicio de sesión único")

3.  En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto **URL de inicio de sesión de Work.com**, escriba la dirección URL que utilizan los usuarios para iniciar sesión en su aplicación PolicyStat de URL (por ejemplo, *“https://demo-azure.policystat.com”*) y luego haga clic en **Siguiente**.

    ![Configurar las opciones de la aplicación](./media/active-directory-saas-policystat-tutorial/IC808631.png "Configurar las opciones de la aplicación")

4.  En la página **Configuración de inicio de sesión único en PolicyStat**, haga clic en **Descargar metadatos** y, luego, guarde el archivo de metadatos en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-policystat-tutorial/IC808632.png "Configurar inicio de sesión único")

5.  En otra ventana del explorador web, inicie sesión en el sitio de la compañía PolicyStat como administrador.

6.  Haga clic en la pestaña **Administración **y luego en **Configuración de inicio de sesión único** en el panel de navegación izquierdo.

    ![Menú Administrador](./media/active-directory-saas-policystat-tutorial/IC808633.png "Menú Administrador")

7.  En la sección **Configuración**, seleccione **Habilitar la integración de inicio de sesión único**.

    ![Configuración de inicio de sesión único](./media/active-directory-saas-policystat-tutorial/IC808634.png "Configuración de inicio de sesión único")

8.  Haga clic en **Configurar atributos**y, a continuación, en la sección **Configurar atributos**, realice los pasos siguientes:

    ![Configuración de inicio de sesión único](./media/active-directory-saas-policystat-tutorial/IC808635.png "Configuración de inicio de sesión único")

    1.  En el cuadro de texto **Atributo de nombre de usuario**, escriba **uid**.
    2.  En el cuadro de texto **Atributo de nombre**, escriba **firstname**.
    3.  En el cuadro de texto **Atributo de apellido**, escriba **lastname**.
    4.  En el cuadro de texto **Atributo de correo electrónico**, escriba **emailaddress**.
    5.  Haga clic en **Guardar cambios**.

9.  Haga clic en **Sus metadatos de IDP**y, luego, en la sección **Sus metadatos de IDP**, realice los pasos siguientes:

    ![Configuración de inicio de sesión único](./media/active-directory-saas-policystat-tutorial/IC808635.png "Configuración de inicio de sesión único")

    1.  Abra el archivo de metadatos descargado, copie el contenido y luego péguelo en el cuadro de texto **Metadatos del proveedor de identidades**.
    2.  Haga clic en **Guardar cambios**.

10. En el portal de Azure AD, seleccione la confirmación de configuración de inicio de sesión único y luego haga clic en **Completa** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-policystat-tutorial/IC771723.png "Configurar inicio de sesión único")

11. 12. En el menú de la parte superior, haga clic en **Atributos** para abrir el cuadro de diálogo **Atributos de token de SAML**.

    ![Atributos](./media/active-directory-saas-policystat-tutorial/IC795920.png "Atributos")

13. Para agregar las asignaciones de los atributos necesarios, realice los pasos siguientes:

    ![Atributos](./media/active-directory-saas-policystat-tutorial/IC804823.png "Atributos")

    1.  Haga clic en **agregar atributo de usuario**.
    2.  En el cuadro de texto **Nombre de atributo**, escriba **uid**.
    3.  En el cuadro de texto **Valor del atributo**, seleccione **ExtractMailPrefix()**.
    4.  En la lista **Correo**, seleccione **User.mail**.
    5.  Haga clic en **Completo**.
##Configuración del aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD inicien sesión en PolicyStat, deben aprovisionarse en PolicyStat. PolicyStat admite aprovisionamiento de usuarios justo a tiempo. Esto significa que no es necesario agregar usuarios manualmente a PolicyStat. Los usuarios se agregarán automáticamente en su primer inicio de sesión a través del inicio de sesión único.

>[AZURE.NOTE]Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de PolicyStat ofrecida por PolicyStat para aprovisionar cuentas de usuario de AAD.

##Asignación de usuarios
  
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

###Para asignar usuarios a PolicyStat, lleve a cabo los siguientes pasos:

1.  En el portal de Azure AD, cree una cuenta de prueba.

2.  En la página de integración de aplicaciones de **PolicyStat**, haga clic en **Asignar usuarios**.

    ![Asignar usuarios](./media/active-directory-saas-policystat-tutorial/IC808636.png "Asignar usuarios")

3.  Seleccione su usuario de prueba, haga clic en **Asignar** y luego en **Sí** para confirmar la asignación.

    ![Sí](./media/active-directory-saas-policystat-tutorial/IC767830.png "Sí")
  
Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0114_2016-->