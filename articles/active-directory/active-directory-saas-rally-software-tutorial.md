<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con Rally Software | Microsoft Azure" 
    description="Aprenda cómo usar Rally Software con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc." 
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

#Tutorial: Integración de Azure Active Directory con Rally Software
  
El objetivo de este tutorial es mostrar la integración de Azure y Rally Software. En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción de Azure válida
-   Un inquilino de Rally Software
  
La situación descrita en este tutorial consta de los siguientes bloques de creación:

1.  Habilitación de la integración de aplicaciones para Rally Software
2.  Configuración del inicio de sesión único
3.  Configuración del aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario](./media/active-directory-saas-rally-software-tutorial/IC769525.png "Escenario")
##Habilitación de la integración de aplicaciones para Rally Software
  
El objetivo de esta sección es describir cómo habilitar la integración de las aplicaciones para Rally Software.

###Siga estos pasos para habilitar la integración de aplicaciones para Rally Software:

1.  En el panel de navegación izquierdo del Portal de administración de Azure, haga clic en **Active Directory**.

    ![Active Directory](./media/active-directory-saas-rally-software-tutorial/IC700993.png "Active Directory")

2.  En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3.  Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

    ![Aplicaciones](./media/active-directory-saas-rally-software-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación](./media/active-directory-saas-rally-software-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Agregar una aplicación de la galería](./media/active-directory-saas-rally-software-tutorial/IC749322.png "Agregar una aplicación de la galería")

6.  En el **cuadro de búsqueda**, escriba **rally**.

    ![Galería de aplicaciones](./media/active-directory-saas-rally-software-tutorial/IC769526.png "Galería de aplicaciones")

7.  En el panel de resultados, seleccione **Rally Software** y, luego, haga clic en **Completa** para agregar la aplicación.

    ![Software de Rally](./media/active-directory-saas-rally-software-tutorial/IC769527.png "Software de Rally")
##Configuración del inicio de sesión único
  
El objetivo de esta sección es describir cómo se habilita la autenticación de los usuarios en Rally Software con su cuenta de Azure AD usando el protocolo SAML basado en la federación. Como parte de este procedimiento, es necesario cargar un certificado en Rally Software.

###Siga estos pasos para configurar el inicio de sesión único:

1.  En el portal de Azure AD, en la página de integración de aplicaciones de **Rally Software **, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-rally-software-tutorial/IC749323.png "Configurar inicio de sesión único")

2.  En la página **¿Cómo desea que los usuarios inicien sesión en Rally?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y luego haga clic en **Siguiente**.

    ![Inicio de sesión único de Microsoft Azure AD](./media/active-directory-saas-rally-software-tutorial/IC769528.png "Inicio de sesión único de Microsoft Azure AD")

3.  En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto de **URL de inicio de sesión de Rally Software**, escriba su dirección URL con el siguiente patrón "*https://\<nombre-inquilino>.rally.com*" y luego haga clic en **Siguiente**.

    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-rally-software-tutorial/IC769529.png "Configurar dirección URL de la aplicación")

4.  En la página **Configuración de inicio de sesión único en Rally**, haga clic en Descargar metadatos y luego guarde el archivo de metadatos en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-rally-software-tutorial/IC769530.png "Configurar inicio de sesión único")

5.  Inicie sesión en su inquilino de **Rally Software**.

6.  En la barra de herramientas de la parte superior, haga clic en**Configuración**y luego seleccione **Suscripción**.

    ![La suscripción](./media/active-directory-saas-rally-software-tutorial/IC769531.png "La suscripción")

7.  Haga clic en el botón **Acción** de la barra de herramientas de la parte superior del lado derecho y luego seleccione**Editar suscripción**.

8.  En la página del cuadro de diálogo **Suscripción**, realice los pasos siguientes y luego haga clic en **Guardar y cerrar**:

    ![Autenticación](./media/active-directory-saas-rally-software-tutorial/IC769542.png "Autenticación")

    1.  Seleccione **Rally o autenticación de SSO** en la lista desplegable de autenticación.
    2.  En el portal de Azure, en la página del cuadro de diálogo **Configurar inicio de sesión único en Rally Software**, copie el valor del **Id. de proveedor de identidades** y luego péguelo en el cuadro de texto **URL del proveedor de identidades**.
    3.  En el portal de Azure, en la página de diálogo **Configurar inicio de sesión único en Rally Software**, copie el valor de **Dirección URL de cierre de sesión remoto**.

9.  En el Portal de Azure AD, seleccione la confirmación de configuración de inicio de sesión único y, a continuación, haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-rally-software-tutorial/IC769547.png "Configurar inicio de sesión único")
##Configuración del aprovisionamiento de usuario
  
Para que los usuarios de AAD puedan inician sesión, deben aprovisionarse para la aplicación Rally Software con sus nombres de usuario de Azure Active Directory.

###Siga estos pasos para configurar el aprovisionamiento de usuario:

1.  Inicie sesión en su inquilino de Rally Software.

2.  Vaya a **Configuración > USUARIOS** y luego haga clic en **+Agregar nuevo**.

    ![Usuarios](./media/active-directory-saas-rally-software-tutorial/IC781039.png "Usuarios")

3.  Escriba el nombre en el cuadro de texto Nuevo usuario y luego haga clic en **Agregar con detalles**.

4.  En la sección **Crear usuario**, lleve a cabo estos pasos:

    ![Crear usuario](./media/active-directory-saas-rally-software-tutorial/IC781040.png "Crear usuario")

    1.  En el cuadro de texto **Nombre de usuario**, escriba el nombre del usuario de Azure AD que quiera aprovisionar.
    2.  En el cuadro de texto **Dirección de correo electrónico**, escriba la dirección de correo electrónico del usuario de Azure AD que quiera aprovisionar.
    3.  Haga clic en **Guardar y cerrar**.

>[AZURE.NOTE]Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Rally Software ofrecida por Rally Software para aprovisionar cuentas de usuario de AAD.

##Asignación de usuarios
  
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

###Para asignar usuarios a Rally Software, lleve a cabo los siguientes pasos:

1.  En el portal de Azure AD, cree una cuenta de prueba.

2.  En la página de integración de aplicaciones de **Rally Software**, haga clic en **Asignar usuarios**.

    ![Asignar usuarios](./media/active-directory-saas-rally-software-tutorial/IC769548.png "Asignar usuarios")

3.  Seleccione su usuario de prueba, haga clic en **Asignar** y luego en **Sí** para confirmar la asignación.

    ![Sí](./media/active-directory-saas-rally-software-tutorial/IC767830.png "Sí")
  
Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0114_2016-->