<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con SpringCM | Microsoft Azure" 
    description="Aprenda cómo usar SpringCM con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc." 
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

#Tutorial: integración de Azure Active Directory con SpringCM
  
El objetivo de este tutorial es mostrar cómo configurar el inicio de sesión único entre Azure Active Directory y SpringCM.
  
En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción de Azure válida
-   Una suscripción habilitada para el inicio de sesión único en Azure Active Directory
  
Después de completar este tutorial, los usuarios de Azure Active Directory que haya asignado a SpringCM podrán realizar inicios de sesión únicos mediante el panel de acceso de AAD.

1.  Habilitación de la integración de aplicaciones para SpringCM
2.  Configuración del inicio de sesión único
3.  Configuración del aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario](./media/active-directory-saas-spring-cm-tutorial/IC797044.png "Escenario")

##Habilitación de la integración de aplicaciones para SpringCM
  
El objetivo de esta sección es describir cómo se habilita la integración de aplicaciones en SpringCM.

###Siga estos pasos para habilitar la integración de aplicaciones para SpringCM:

1.  En el panel de navegación izquierdo del Portal de administración de Azure, haga clic en **Active Directory**.

    ![Active Directory](./media/active-directory-saas-spring-cm-tutorial/IC700993.png "Active Directory")

2.  En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3.  Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

    ![Aplicaciones](./media/active-directory-saas-spring-cm-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación](./media/active-directory-saas-spring-cm-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Agregar una aplicación de la galería](./media/active-directory-saas-spring-cm-tutorial/IC749322.png "Agregar una aplicación de la galería")

6.  En el **cuadro de búsqueda**, escriba **SpringCM**.

    ![Galería de aplicaciones](./media/active-directory-saas-spring-cm-tutorial/IC797045.png "Galería de aplicaciones")

7.  En el panel de resultados, seleccione **SpringCM** y luego haga clic en **Completa** para agregar la aplicación.

    ![SpringCM](./media/active-directory-saas-spring-cm-tutorial/IC797046.png "SpringCM")

##Configuración del inicio de sesión único
  
El objetivo de esta sección es describir cómo habilitar usuarios para que se autentiquen en SpringCM con su cuenta de Azure AD a través de la federación basada en el protocolo SAML.

###Siga estos pasos para configurar el inicio de sesión único:

1.  En el portal de Azure AD, en la página de integración de aplicaciones de **SpringCM**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-spring-cm-tutorial/IC797047.png "Configurar inicio de sesión único")

2.  En la página **¿Cómo desea que los usuarios inicien sesión en SpringCM?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y, luego , haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-spring-cm-tutorial/IC797048.png "Configurar inicio de sesión único")

3.  En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto **URL de inicio de sesión de SpringCM**, escriba la dirección URL que utilizan los usuarios para iniciar sesión en su aplicación SpringCM y luego haga clic en **Siguiente**.

    La dirección URL de la aplicación es su URL de inquilino de SpringCM (por ejemplo, **https://na11.springcm.com/atlas/SSO/SSOEndpoint.ashx?aid=16826*):

    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-spring-cm-tutorial/IC797049.png "Configurar dirección URL de la aplicación")

4.  En la página **Configurar inicio de sesión único en SpringCM**, para descargar el certificado, haga clic en **Descargar certificado** y luego guarde el archivo de certificado localmente en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-spring-cm-tutorial/IC797050.png "Configurar inicio de sesión único")

5.  En otra ventana del explorador web, inicie sesión en su sitio de la compañía de **SpringCM** como administrador.

6.  En el menú de la parte superior, haga clic en**IR A**, haga clic en **Preferencias** y luego en la sección **Preferencias de cuenta**, haga clic en**SSO de SAML**.

    ![SSO DE SAML](./media/active-directory-saas-spring-cm-tutorial/IC797051.png "SSO DE SAML")

7.  En la sección de configuración del proveedor de identidades, realice los pasos siguientes:

    ![Configuración del proveedor de identidades](./media/active-directory-saas-spring-cm-tutorial/IC797052.png "Configuración del proveedor de identidades")

    1.  Para cargar el certificado descargado de Azure Active Directory, haga clic en **Seleccionar certificado de emisor** o **Cambiar el certificado del emisor**.
    2.  En el portal de Microsoft Azure, en la página **Configurar inicio de sesión único en SpringCM**, copie el valor de **URL del emisor** y péguelo en el cuadro de texto **Emisor**.
    3.  En el portal de Microsoft Azure, en la página **Configurar inicio de sesión único en SpringCM**, copie el valor de **URL del servicio de inicio de sesión único** y péguelo en el cuadro de texto **Punto de conexión iniciado por el proveedor de servicios (SP)**.
    4.  Como **SAML habilitado**, seleccione **Habilitar**.
    5.  Haga clic en **Guardar**.

8.  En el Portal de Azure AD, seleccione la confirmación de configuración de inicio de sesión único y, a continuación, haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-spring-cm-tutorial/IC797053.png "Configurar inicio de sesión único")

##Configuración del aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure Active Directory inicien sesión en SpringCM, deben aprovisionarse en SpringCM. En el caso de SpringCM, el aprovisionamiento es una tarea manual.

>[AZURE.NOTE]Para obtener más información, vea [Crear y editar un usuario de SpringCM](http://knowledge.springcm.com/create-and-edit-a-springcm-user).

###Para aprovisionar cuentas de usuario a SpringCM, realice los siguientes pasos:

1.  Inicie sesión en su sitio de compañía de **SpringCM** como administrador.

2.  Haga clic en **GOTO** y luego en**Libreta de direcciones**.

    ![Crear usuario](./media/active-directory-saas-spring-cm-tutorial/IC797054.png "Crear usuario")

3.  Haga clic en **Crear usuario**.

4.  Seleccione un **Rol de usuario**.

5.  Seleccione **Enviar correo electrónico de activación**.

6.  Escriba el nombre, el apellido y la dirección de correo electrónico de una cuenta de Azure Active Directory válida que quiera aprovisionar en los cuadros de texto relacionados.

7.  Agregue el usuario a un **Grupo de seguridad**.

8.  Haga clic en **Guardar**.

>[AZURE.NOTE]Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de SpringCM ofrecida por SpringCM para aprovisionar cuentas de usuario de AAD.

##Asignación de usuarios
  
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

###Para asignar usuarios a SpringCM, lleve a cabo los siguientes pasos:

1.  En el portal de Azure AD, cree una cuenta de prueba.

2.  En la página de integración de la aplicación **SpringCM**, haga clic en **Asignar usuarios**.

    ![Asignar usuarios](./media/active-directory-saas-spring-cm-tutorial/IC797055.png "Asignar usuarios")

3.  Seleccione su usuario de prueba, haga clic en **Asignar** y luego en **Sí** para confirmar la asignación.

    ![Sí](./media/active-directory-saas-spring-cm-tutorial/IC767830.png "Sí")
  
Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0114_2016-->