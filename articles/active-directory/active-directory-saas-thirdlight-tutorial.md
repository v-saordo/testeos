<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con Thirdlight | Microsoft Azure" 
    description="Aprenda cómo usar Thirdlight con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc." 
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

#Tutorial: integración de Azure Active Directory con Thirdlight
  
El objetivo de este tutorial es mostrar la integración de Azure y Thirdlight. En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción de Azure válida
-   Una suscripción habilitada para el inicio de sesión único en Thirdlight
  
Después de completar este tutorial, los usuarios de Azure AD que ha asignado a Thirdlight podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de Thirdlight (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
La situación descrita en este tutorial consta de los siguientes bloques de creación:

1.  Habilitación de la integración de aplicaciones para Thirdlight
2.  Configuración del inicio de sesión único
3.  Configuración del aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario](./media/active-directory-saas-thirdlight-tutorial/IC805836.png "Escenario")

##Habilitación de la integración de aplicaciones para Thirdlight
  
El objetivo de esta sección es describir cómo habilitar la integración de las aplicaciones para Thirdlight.

###Siga estos pasos para habilitar la integración de aplicaciones para Thirdlight:

1.  En el panel de navegación izquierdo del Portal de administración de Azure, haga clic en **Active Directory**.

    ![Active Directory](./media/active-directory-saas-thirdlight-tutorial/IC700993.png "Active Directory")

2.  En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3.  Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

    ![Aplicaciones](./media/active-directory-saas-thirdlight-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación](./media/active-directory-saas-thirdlight-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Agregar una aplicación de la galería](./media/active-directory-saas-thirdlight-tutorial/IC749322.png "Agregar una aplicación de la galería")

6.  En el **cuadro de búsqueda**, escriba **Thirdlight**.

    ![Galería de aplicaciones](./media/active-directory-saas-thirdlight-tutorial/IC805837.png "Galería de aplicaciones")

7.  En el panel de resultados, seleccione **Thirdlight** y luego haga clic en **Completar** para agregar la aplicación.

    ![Thirdlight](./media/active-directory-saas-thirdlight-tutorial/IC805838.png "Thirdlight")

##Configuración del inicio de sesión único
  
El objetivo de esta sección es describir cómo se habilita la autenticación de los usuarios en Thirdlight con su cuenta de Azure AD usando el protocolo SAML basado en la federación. La configuración de un inicio de sesión único para Thirdlight requiere la recuperación de un valor de huella digital de un certificado. Si no está familiarizado con este procedimiento, consulte [Recuperación del valor de huella digital de un certificado](http://youtu.be/YKQF266SAxI).

###Siga estos pasos para configurar el inicio de sesión único:

1.  En el portal de Azure AD, en la página de integración de aplicaciones de **Thirdlight**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único **.

    ![Configurar inicio de sesión único](./media/active-directory-saas-thirdlight-tutorial/IC805839.png "Configurar inicio de sesión único")

2.  En la página **¿Cómo desea que los usuarios inicien sesión en Thirdlight?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y luego haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-thirdlight-tutorial/IC805840.png "Configurar inicio de sesión único")

3.  En la página **Configurar URL de aplicación**, en el cuadro de texto **URL de inicio de sesión de Thirdlight**, escriba la dirección URL usada por los usuarios para iniciar sesión en su aplicación Thirdlight (por ejemplo: "**http://azuresso2.thirdlight.com/*")), y luego haga clic en **Siguiente**.

    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-thirdlight-tutorial/IC805841.png "Configurar dirección URL de la aplicación")

4.  En la página **Configuración de inicio de sesión único en Thirdlight**, para descargar sus metadatos, haga clic en **Descargar metadatos** y, luego, guarde el archivo de metadatos localmente en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-thirdlight-tutorial/IC805842.png "Configurar inicio de sesión único")

5.  En otra ventana del explorador web, inicie sesión en su sitio de la compañía de Thirdlight como administrador.

6.  Vaya a **Configuración > Administración del sistema** y luego haga clic en **SAML2**.

    ![Administración del sistema](./media/active-directory-saas-thirdlight-tutorial/IC805843.png "Administración del sistema")

7.  En la sección de configuración de SAML2, realice los pasos siguientes:

    ![Inicio de sesión único SAML](./media/active-directory-saas-thirdlight-tutorial/IC805844.png "Inicio de sesión único SAML")

    1.  Seleccione **Habilitar inicio de sesión único de SAML2**.
    2.  Como **Origen para los metadatos de IdP**, seleccione **Cargar metadatos de IdP desde XML**.
    3.  Abra el archivo de metadatos descargado, copie el contenido y luego péguelo en el cuadro de texto **XML de metadatos de IdP**.
    4.  Haga clic en **Guardar configuración de SAML2**.

8.  En el Portal de Azure AD, seleccione la confirmación de configuración de inicio de sesión único y, a continuación, haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-thirdlight-tutorial/IC805845.png "Configurar inicio de sesión único")

##Configuración del aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD inicien sesión en Thirdlight, deben aprovisionarse en Thirdlight. En el caso de Thirdlight, el aprovisionamiento es una tarea manual.

###Siga estos pasos para configurar el aprovisionamiento de usuario:

1.  Inicie sesión en su sitio de la compañía de **Thirdlight** como administrador.

2.  Vaya a la pestaña **Usuarios**.

3.  Seleccione **Usuarios y grupos**.

4.  Haga clic en el botón **Agregar nuevo usuario**.

5.  Escriba **el nombre de usuario, el nombre o la descripción, el correo electrónico, elegir un valor preestablecido o grupo de nuevos miembros**de una cuenta de AAD válida que quiera suministrar.

6.  Haga clic en **Crear**.

>[AZURE.NOTE]Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Thirdlight ofrecida por Thirdlight para aprovisionar cuentas de usuario de AAD.

##Asignación de usuarios
  
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

###Para asignar usuarios a Thirdlight, lleve a cabo los siguientes pasos:

1.  En el portal de Azure AD, cree una cuenta de prueba.

2.  En la página de integración de la aplicación de **Thirdlight ** haga clic en **Asignar usuarios**.

    ![Asignar usuarios](./media/active-directory-saas-thirdlight-tutorial/IC805846.png "Asignar usuarios")

3.  Seleccione su usuario de prueba, haga clic en **Asignar** y luego en **Sí** para confirmar la asignación.

    ![Sí](./media/active-directory-saas-thirdlight-tutorial/IC767830.png "Sí")
  
Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0114_2016-->