<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con Thoughtworks Mingle | Microsoft Azure" 
    description="Aprenda cómo usar Thoughtworks Mingle con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc." 
    services="active-directory" 
    authors="markusvi"  
    documentationCenter="na" manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="01/12/2016" 
    ms.author="markvi" />

#Tutorial: Integración de Azure Active Directory con Thoughtworks Mingle
  
El objetivo de este tutorial es mostrar la integración de Azure y Thoughtworks Mingle. En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción de Azure válida
-   Un inquilino de Thoughtworks Mingle
  
La situación descrita en este tutorial consta de los siguientes bloques de creación:

1.  Habilitación de la integración de aplicaciones para Thoughtworks Mingle
2.  Configuración del inicio de sesión único
3.  Configuración del aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785150.png "Escenario")

##Habilitación de la integración de aplicaciones para Thoughtworks Mingle
  
El objetivo de esta sección es describir cómo habilitar la integración de las aplicaciones para Thoughtworks Mingle.

###Siga estos pasos para habilitar la integración de aplicaciones para Thoughtworks Mingle:

1.  En el panel de navegación izquierdo del Portal de administración de Azure, haga clic en **Active Directory**.

    ![Active Directory](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC700993.png "Active Directory")

2.  En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3.  Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

    ![Aplicaciones](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Agregar una aplicación de la galería](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC749322.png "Agregar una aplicación de la galería")

6.  En el **cuadro de búsqueda**, escriba **thoughtworks mingle**.

    ![Galería de aplicaciones](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785151.png "Galería de aplicaciones")

7.  En el panel de resultados, seleccione **Thoughtworks Mingle** y, luego, haga clic en **Completa** para agregar la aplicación.

    ![Thoughtworks Mingle](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785152.png "Thoughtworks Mingle")

##Configuración del inicio de sesión único
  
El objetivo de esta sección es describir cómo se habilita la autenticación de los usuarios en Thoughtworks Mingle con su cuenta de Azure AD usando el protocolo SAML basado en la federación. Como parte de este procedimiento, es necesario cargar un certificado en Thoughtworks Mingle.

###Siga estos pasos para configurar el inicio de sesión único:

1.  En el portal de Azure AD, en la página de integración de aplicaciones de **Thoughtworks Mingle **, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785153.png "Configurar inicio de sesión único")

2.  En la página **¿Cómo desea que los usuarios inicien sesión en Thoughtworks Mingle?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y luego haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785154.png "Configurar inicio de sesión único")

3.  En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto de **URL de inquilino de Thoughtworks Mingle**, escriba su dirección URL con el siguiente patrón "**http://company.mingle.thoughtworks.com*" y luego haga clic en **Siguiente**.

    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785155.png "Configurar dirección URL de la aplicación")

4.  En la página **Configuración de inicio de sesión único en Thoughtworks Mingle**, haga clic en Descargar metadatos y luego guarde el archivo de metadatos en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785156.png "Configurar inicio de sesión único")

5.  Inicie sesión en su sitio de compañía de **Thoughtworks Mingle** como administrador.

6.  Haga clic en la pestaña **Administrador** y luego en **Configuración de SSO**.

    ![Configuración de SSO](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785157.png "Configuración de SSO")

7.  En la sección **Configuración de SSO**, lleve a cabo estos pasos:

    ![Configuración de SSO](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785158.png "Configuración de SSO")

    1.  Para cargar el archivo de metadatos, haga clic en **Elegir archivo**.
    2.  Haga clic en **Guardar cambios**.

8.  En el portal de Azure AD, seleccione la confirmación de configuración de inicio de sesión único y luego haga clic en **Completa** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785159.png "Configurar inicio de sesión único")

##Configuración del aprovisionamiento de usuario
  
Para que los usuarios de AAD puedan inician sesión, deben aprovisionarse para la aplicación Thoughtworks Mingle con sus nombres de usuario de Azure Active Directory. En el caso de Thoughtworks Mingle, el aprovisionamiento es una tarea manual.

###Siga estos pasos para configurar el aprovisionamiento de usuario:

1.  Inicie sesión en su sitio de compañía de Thoughtworks Mingle como administrador.

2.  Haga clic en **Perfil**.

    ![Su primer proyecto](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785160.png "Su primer proyecto")

3.  Haga clic en la pestaña **Administrador** y luego en **Usuarios**.

    ![Usuarios](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785161.png "Usuarios")

4.  Haga clic en **Nuevo usuario**.

    ![Nuevo usuario](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785162.png "Nuevo usuario")

5.  En la página del cuadro de diálogo **Nuevo usuario**, realice los pasos siguientes:

    ![Nuevo usuario](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785163.png "Nuevo usuario")

    1.  Escriba las opciones **Nombre de inicio de sesión**, **Nombre para mostrar**, **Elegir contraseña**, **Confirmar contraseña** de una cuenta de AAD válida que quiere aprovisionar en los cuadros de texto relacionados.
    2.  Como **Tipo de usuario**, seleccione **Usuario completo**.
    3.  Haga clic en **Crear este perfil**.

>[AZURE.NOTE]Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Thoughtworks Mingle ofrecida por Thoughtworks Mingle para aprovisionar cuentas de usuario de AAD.

##Asignación de usuarios
  
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

###Para asignar usuarios a Thoughtworks Mingle, lleve a cabo los siguientes pasos:

1.  En el portal de Azure AD, cree una cuenta de prueba.

2.  En la página de integración de la aplicación **Thoughtworks Mingle**, haga clic en **Asignar usuarios**.

    ![Asignar usuarios](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785164.png "Asignar usuarios")

3.  Seleccione su usuario de prueba, haga clic en **Asignar** y luego en **Sí** para confirmar la asignación.

    ![Sí](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC767830.png "Sí")
  
Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0114_2016-->