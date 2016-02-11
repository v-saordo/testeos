<properties 
    pageTitle="Tutorial: integración de Azure Active Directory con Projectplace | Microsoft Azure" 
    description="Aprenda cómo usar Projectplace con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc." 
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

#Tutorial: Integración de Azure Active Directory con Projectplace
  
El objetivo de este tutorial es mostrar la integración de Azure y Projectplace. En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción de Azure válida
-   Una suscripción habilitada para el inicio de sesión único en Projectplace
  
Después de completar este tutorial, los usuarios de Azure AD asignados a Projectplace podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía Projectplace (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
La situación descrita en este tutorial consta de los siguientes bloques de creación:

1.  Habilitación de la integración de aplicaciones para Projectplace
2.  Configuración del inicio de sesión único
3.  Configuración del aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario](./media/active-directory-saas-projectplace-tutorial/IC790217.png "Escenario")
##Habilitación de la integración de aplicaciones para Projectplace
  
El objetivo de esta sección es describir cómo habilitar la integración de las aplicaciones para Projectplace.

###Siga estos pasos para habilitar la integración de aplicaciones en Projectplace:

1.  En el panel de navegación izquierdo del Portal de administración de Azure, haga clic en **Active Directory**.

    ![Active Directory](./media/active-directory-saas-projectplace-tutorial/IC700993.png "Active Directory")

2.  En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3.  Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

    ![Aplicaciones](./media/active-directory-saas-projectplace-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación](./media/active-directory-saas-projectplace-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Agregar una aplicación de la galería](./media/active-directory-saas-projectplace-tutorial/IC749322.png "Agregar una aplicación de la galería")

6.  En el **cuadro de búsqueda**, escriba **Projectplace**.

    ![Galería de aplicaciones](./media/active-directory-saas-projectplace-tutorial/IC790218.png "Galería de aplicaciones")

7.  En el panel de resultados, seleccione **Projectplace** y luego haga clic en **Completar** para agregar la aplicación.

    ![ProjectPlace](./media/active-directory-saas-projectplace-tutorial/IC790219.png "ProjectPlace")
##Configuración del inicio de sesión único
  
El objetivo de esta sección es describir cómo se habilita la autenticación de los usuarios en Projectplace con su cuenta de Azure AD usando el protocolo SAML basado en la federación.

###Siga estos pasos para configurar el inicio de sesión único:

1.  En el Portal de Azure AD, en la página de integración de aplicaciones de **Projectplace**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-projectplace-tutorial/IC790220.png "Configurar inicio de sesión único")

2.  En la página **¿Cómo desea que los usuarios inicien sesión en Projectplace?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y luego haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-projectplace-tutorial/IC790221.png "Configurar inicio de sesión único")

3.  En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto de **URL de inicio de sesión de Projectplace**, escriba la dirección URL del inquilino de Projectplace (por ejemplo, "**http://company.projectplace.com*")) y luego haga clic en **Siguiente**.

    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-projectplace-tutorial/IC790222.png "Configurar dirección URL de la aplicación")

4.  En la página **Configuración de inicio de sesión único en Projectplace**, haga clic en **Descargar metadatos** y, luego, guarde el archivo de metadatos en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-projectplace-tutorial/IC790223.png "Configurar inicio de sesión único")

5.  Envíe el archivo de metadatos al equipo de soporte técnico de Projectplace.

    >[AZURE.NOTE]La configuración del inicio de sesión único la debe realizar el equipo de soporte técnico de Projectplace. Tan pronto como se complete la configuración, recibirá una notificación.

6.  En el portal de Azure AD, seleccione la confirmación de configuración de inicio de sesión único y luego haga clic en **Completa** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-projectplace-tutorial/IC790227.png "Configurar inicio de sesión único")
##Configuración del aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD inicien sesión en Projectplace, deben aprovisionarse en Projectplace. En el caso de Projectplace, el aprovisionamiento es una tarea manual.

###Para aprovisionar cuentas de usuario, realice los siguientes pasos:

1.  Inicie sesión en el sitio de la compañía **Projectplace** como administrador.

2.  Vaya a **personas** y luego haga clic en **Miembros**.

    ![Personas](./media/active-directory-saas-projectplace-tutorial/IC790228.png "Personas")

3.  Haga clic en **Agregar miembro**.

    ![Agregar miembros](./media/active-directory-saas-projectplace-tutorial/IC790232.png "Agregar miembros")

4.  En la sección **Agregar miembro**, lleve a cabo estos pasos:

    ![Nuevos miembros](./media/active-directory-saas-projectplace-tutorial/IC790233.png "Nuevos miembros")

    1.  En el cuadro de texto **Nuevos miembros**, escriba la dirección de correo electrónico de la cuenta de AAD válida que quiera suministrar en los cuadros de texto relacionados.
    2.  Haga clic en **Enviar**.

	    >[AZURE.NOTE]Se enviará un mensaje de correo electrónico con un vínculo para confirmar la cuenta antes de que se active al titular de la cuenta de Azure Active Directory.
    
>[AZURE.NOTE]Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Projectplace que proporcione Projectplace para aprovisionar cuentas de usuario de AAD.

##Asignación de usuarios
  
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

###Para asignar usuarios a Projectplace, lleve a cabo los siguientes pasos:

1.  En el portal de Azure AD, cree una cuenta de prueba.

2.  En la página de integración de la aplicación **Projectplace **, haga clic en **Asignar usuarios**.

    ![Asignar usuarios](./media/active-directory-saas-projectplace-tutorial/IC790234.png "Asignar usuarios")

3.  Seleccione su usuario de prueba, haga clic en **Asignar** y luego en **Sí** para confirmar la asignación.

    ![Sí](./media/active-directory-saas-projectplace-tutorial/IC767830.png "Sí")
  
Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0114_2016-->