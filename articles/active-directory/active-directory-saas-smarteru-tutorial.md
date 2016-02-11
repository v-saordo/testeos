<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con SmarterU | Microsoft Azure" 
    description="Aprenda cómo usar SmarterU con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc." 
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

#Tutorial: integración de Azure Active Directory con SmarterU
  
El objetivo de este tutorial es mostrar la integración de Azure y SmarterU. En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción de Azure válida
-   Un inquilino de SmarterU
  
Después de completar este tutorial, los usuarios de Azure AD que asignó a SmarterU podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de SmarterU (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
La situación descrita en este tutorial consta de los siguientes bloques de creación:

1.  Habilitación de la integración de aplicaciones para SmarterU
2.  Configuración del inicio de sesión único
3.  Configuración del aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario](./media/active-directory-saas-smarteru-tutorial/IC777320.png "Escenario")

##Habilitación de la integración de aplicaciones para SmarterU
  
El objetivo de esta sección es describir cómo se habilita la integración de aplicaciones en SmarterU.

###Siga estos pasos para habilitar la integración de aplicaciones para SmarterU:

1.  En el panel de navegación izquierdo del Portal de administración de Azure, haga clic en **Active Directory**.

    ![Active Directory](./media/active-directory-saas-smarteru-tutorial/IC700993.png "Active Directory")

2.  En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3.  Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

    ![Aplicaciones](./media/active-directory-saas-smarteru-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación](./media/active-directory-saas-smarteru-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Agregar una aplicación de la galería](./media/active-directory-saas-smarteru-tutorial/IC749322.png "Agregar una aplicación de la galería")

6.  En el **cuadro de búsqueda**, escriba **SmarterU**.

    ![Galería de aplicaciones](./media/active-directory-saas-smarteru-tutorial/IC777321.png "Galería de aplicaciones")

7.  En el panel de resultados, seleccione **SmarterU** y luego haga clic en **Completa** para agregar la aplicación.

    ![SmarterU](./media/active-directory-saas-smarteru-tutorial/IC777322.png "SmarterU")

##Configuración del inicio de sesión único
  
El objetivo de esta sección es describir cómo se habilita la autenticación de los usuarios en SmarterU con su cuenta de Azure AD usando el protocolo SAML basado en la federación.

###Siga estos pasos para configurar el inicio de sesión único:

1.  En el Portal de Azure AD, en la página de integración de aplicaciones de **SmarterU**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-smarteru-tutorial/IC777323.png "Configurar inicio de sesión único")

2.  En la página **¿Cómo desea que los usuarios inicien sesión en SmarterU?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y, luego , haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-smarteru-tutorial/IC777324.png "Configurar inicio de sesión único")

3.  En la página **Configurar inicio de sesión único en SmarterU**, para descargar los metadatos, haga clic en **Descargar metadatos** y, luego, el archivo de datos localmente como **c:\\SmarterUMetaData.cer**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-smarteru-tutorial/IC777325.png "Configurar inicio de sesión único")

4.  En otra ventana del explorador web, inicie sesión en su sitio de la compañía de SmarterU como administrador.

5.  En la barra de herramientas de la parte superior, haga clic en **Configuración de cuenta**.

    ![Configuración de cuenta](./media/active-directory-saas-smarteru-tutorial/IC777326.png "Configuración de cuenta")

6.  En la página de configuración de la cuenta, realice los siguientes pasos:

    ![Autorización externa](./media/active-directory-saas-smarteru-tutorial/IC777327.png "Autorización externa")

    1.  Seleccione **Habilitar autorización externa**.
    2.  En la sección **Control de inicio de sesión maestro**, seleccione la pestaña **SmarterU**.
    3.  En la sección **Inicio de sesión predeterminado de usuario**, seleccione la pestaña **SmarterU**.
    4.  Seleccione **Habilitar Okta**.
    5.  Copie el contenido del archivo de metadatos descargado y luego péguelo en el cuadro de texto **Metadatos de Okta**.
    6.  Haga clic en **Guardar**.

7.  En el Portal de Azure AD, seleccione la confirmación de configuración de inicio de sesión único y, a continuación, haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-smarteru-tutorial/IC777328.png "Configurar inicio de sesión único")

##Configuración del aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD inicien sesión en SmarterU, deben aprovisionarse en SmarterU. En el caso de SmarterU, el aprovisionamiento es una tarea manual.

###Para aprovisionar cuentas de usuario, realice los siguientes pasos:

1.  Inicie sesión en su inquilino de **SmarterU**.

2.  Vaya a **Usuarios**.

3.  En la sección del usuario, lleve a cabo estos pasos:

    ![Nuevo usuario](./media/active-directory-saas-smarteru-tutorial/IC777329.png "Nuevo usuario")

    1.  Haga clic en **+Usuario**.
    2.  Escriba los valores de atributo relacionados de la cuenta de usuario de Azure AD en los siguientes cuadros de texto: **Correo electrónico principal**, **Id. de empleado**, **Contraseña**, **Comprobar contraseña**, **Nombre de pila**, **Apellido**.
    3.  Haga clic en **Activo**.
    4.  Haga clic en **Guardar**.

>[AZURE.NOTE]Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de SmarterU ofrecida por SmarterU para aprovisionar cuentas de usuario de AAD.

##Asignación de usuarios
  
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

###Para asignar usuarios a SmarterU, lleve a cabo los siguientes pasos:

1.  En el portal de Azure AD, cree una cuenta de prueba.

2.  En la página de integración de la aplicación **SmarterU**, haga clic en **Asignar usuarios**.

    ![Asignar usuarios](./media/active-directory-saas-smarteru-tutorial/IC777330.png "Asignar usuarios")

3.  Seleccione su usuario de prueba, haga clic en **Asignar** y luego en **Sí** para confirmar la asignación.

    ![Sí](./media/active-directory-saas-smarteru-tutorial/IC767830.png "Sí")
  
Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0114_2016-->