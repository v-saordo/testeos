<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con xMatters OnDemand | Microsoft Azure" description="Aprenda cómo usar xMatters OnDemand con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc." 
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

#Tutorial: Integración de Azure Active Directory con xMatters OnDemand
  
El objetivo de este tutorial es mostrar la integración de Azure y xMatters OnDemand. En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción de Azure válida
-   Un inquilino de xMatters OnDemand
  
Después de completar este tutorial, los usuarios de Azure AD que ha asignado a xMatters OnDemand podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de xMatters OnDemand (inicio de sesión iniciado por el proveedor del servicio) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md)
  
La situación descrita en este tutorial consta de los siguientes bloques de creación:

1.  Habilitación de la integración de aplicaciones para xMatters OnDemand
2.  Configuración del inicio de sesión único
3.  Configuración del aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776788.png "Escenario")

##Habilitación de la integración de aplicaciones para xMatters OnDemand
  
El objetivo de esta sección es describir cómo habilitar la integración de las aplicaciones para xMatters OnDemand.

###Siga estos pasos para habilitar la integración de aplicaciones para xMatters OnDemand:

1.  En el panel de navegación izquierdo del Portal de administración de Azure, haga clic en **Active Directory**.

    ![Active Directory](./media/active-directory-saas-xmatters-ondemand-tutorial/IC700993.png "Active Directory")

2.  En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3.  Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

    ![Aplicaciones](./media/active-directory-saas-xmatters-ondemand-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación](./media/active-directory-saas-xmatters-ondemand-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Agregar una aplicación de la galería](./media/active-directory-saas-xmatters-ondemand-tutorial/IC749322.png "Agregar una aplicación de la galería")

6.  En el **cuadro de búsqueda**, escriba **xMatters OnDemand**.

    ![Galería de aplicaciones](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776789.png "Galería de aplicaciones")

7.  En el panel de resultados, seleccione **xMatters OnDemand** y, luego, haga clic en **Completa** para agregar la aplicación.

    ![xMatters OnDemand](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776790.png "xMatters OnDemand")

##Configuración del inicio de sesión único
  
El objetivo de esta sección es describir cómo se habilita la autenticación de los usuarios en xMatters OnDemand con su cuenta de Azure AD usando el protocolo SAML basado en la federación.

###Siga estos pasos para configurar el inicio de sesión único:

1.  En el portal de Azure AD, en la página de integración de aplicaciones de **XMatters OnDemand**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776791.png "Configurar inicio de sesión único")

2.  En la página **¿Cómo desea que los usuarios inicien sesión en xMatters OnDemand?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y, luego , haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776792.png "Configurar inicio de sesión único")

3.  En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto de **dirección URL de inicio de sesión de XMatters OnDemand**, escriba su dirección URL con el siguiente patrón "*https://\<nombre-inquilino>.XMattersOnDemandapp.com*" y luego haga clic en **Siguiente**.

    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776793.png "Configurar dirección URL de la aplicación")

4.  En la página **Configuración de inicio de sesión único en XMatters OnDemand**, para descargar el certificado, haga clic en **Descargar certificado** y luego guarde el archivo de certificado localmente como **c:\\XMatters OnDemand.cer**.

    >[AZURE.IMPORTANT]Debe reenviar el certificado al equipo de soporte de xMatters. El equipo de soporte técnico de xMatters debe cargar el certificado para que se pueda finalizar la configuración del inicio de sesión único.

    ![Configurar inicio de sesión único](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776794.png "Configurar inicio de sesión único")

5.  En otra ventana del explorador web, inicie sesión en su sitio de la compañía de xMatters OnDemand como administrador.

6.  En la barra de herramientas de la parte superior, haga clic en**Administrador**y luego en **Detalles de la compañía** de la barra de navegación del lado izquierdo.

    ![Administrador](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776795.png "Administrador")

7.  En la página **Configuración de SAML**, realice los siguientes pasos:

    ![Configuración de SAML](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776796.png "Configuración de SAML")

    1.  Seleccione **Habilitar SAML**.
    2.  En el portal de Azure, en la página del cuadro de diálogo **Configurar inicio de sesión único en XMatters OnDemand**, copie el valor del **Id. de proveedor de identidades** y luego péguelo en el cuadro de texto **Id. de proveedor de identidades**.
    3.  En el portal de Azure, en la página del cuadro de diálogo **Configurar inicio de sesión único en XMatters OnDemand**, copie el valor de la **Dirección URL del servicio de inicio de sesión único** y luego péguelo en el cuadro de texto **Dirección URL de inicio de sesión único**.
    4.  En el portal de Azure, en la página del cuadro de diálogo **Configurar inicio de sesión único en XMatters OnDemand**, copie el valor de la **Dirección URL del servicio de cierre de sesión único** y luego péguelo en el cuadro de texto **Dirección URL de cierre de sesión único**.
    5.  En la página de detalles de la compañía, en la parte superior, haga clic en**Guardar cambios**. ![Detalles de la compañía](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776797.png "Detalles de la compañía")

8.  En el portal de Azure AD, seleccione la confirmación de configuración de inicio de sesión único y luego haga clic en **Completa** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776798.png "Configurar inicio de sesión único")

##Configuración del aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD inicien sesión en XMatters OnDemand, deben aprovisionarse a XMatters OnDemand. En el caso de XMatters OnDemand, el aprovisionamiento es una tarea manual.

###Para aprovisionar cuentas de usuario, realice los siguientes pasos:

1.  Inicie sesión en su inquilino de **xMatters OnDemand**.

2.  Haga clic en la pestaña **Usuarios**.

3.  Haga clic en **Agregar usuario**.

    ![Usuarios](./media/active-directory-saas-xmatters-ondemand-tutorial/IC781048.png "Usuarios")

4.  Seleccione **Activo**.

5.  En la sección **Agregar un usuario**, realice estos pasos:

    ![Agregar un usuario](./media/active-directory-saas-xmatters-ondemand-tutorial/IC781049.png "Agregar un usuario")

    1.  Escriba el **UserID**, **Nombre**, **Apellido**, **Sitio** de una cuenta de AAD válida que quiera aprovisionar.
    2.  Haga clic en **Guardar**.

>[AZURE.NOTE]Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de XMatters OnDemand ofrecida por XMatters OnDemand para aprovisionar cuentas de usuario de AAD.

##Asignación de usuarios
  
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

###Para asignar usuarios a XMatters OnDemand, lleve a cabo los siguientes pasos:

1.  En el portal de Azure AD, cree una cuenta de prueba.

2.  En la página de integración de la aplicación **XMatters OnDemand **, haga clic en **Asignar usuarios**.

    ![Asignar usuarios](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776799.png "Asignar usuarios")

3.  Seleccione su usuario de prueba, haga clic en **Asignar** y luego en **Sí** para confirmar la asignación.

    ![Sí](./media/active-directory-saas-xmatters-ondemand-tutorial/IC767830.png "Sí")
  
Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0114_2016-->