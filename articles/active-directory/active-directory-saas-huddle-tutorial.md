<properties 
    pageTitle="Tutorial: integración de Azure Active Directory con Huddle | Microsoft Azure" 
    description="Aprenda a usar Huddle con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automático, etc." 
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
    ms.date="01/14/2016" 
    ms.author="jeedes" />

#Tutorial: integración de Azure Active Directory con Huddle
  
El objetivo de este tutorial es mostrar la integración de Azure y Huddle. En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción de Azure válida
-   Una suscripción habilitada para el inicio de sesión único en Huddle
  
Después de completar este tutorial, los usuarios de Azure AD que haya asignado a Huddle podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de Huddle (inicio de sesión iniciado por el proveedor de servicios) o desde [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
La situación descrita en este tutorial consta de los siguientes bloques de creación:

1.  Habilitación de la integración de aplicaciones para Huddle
2.  Configuración del inicio de sesión único
3.  Configuración del aprovisionamiento de usuario
4.  Asignación de usuarios

![Configurar inicio de sesión único](./media/active-directory-saas-huddle-tutorial/IC787830.png "Configurar inicio de sesión único")
##Habilitación de la integración de aplicaciones para Huddle
  
El objetivo de esta sección es describir cómo se habilita la integración de aplicaciones para Huddle.

###Siga estos pasos para habilitar la integración de aplicaciones para Huddle:

1.  En el panel de navegación izquierdo del Portal de administración de Azure, haga clic en **Active Directory**.

    ![Active Directory](./media/active-directory-saas-huddle-tutorial/IC700993.png "Active Directory")

2.  En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3.  Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

    ![Aplicaciones](./media/active-directory-saas-huddle-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación](./media/active-directory-saas-huddle-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Agregar una aplicación de la galería](./media/active-directory-saas-huddle-tutorial/IC749322.png "Agregar una aplicación de la galería")

6.  En el **cuadro de búsqueda**, escriba **Huddle**.

    ![Galería de aplicaciones](./media/active-directory-saas-huddle-tutorial/IC787831.png "Galería de aplicaciones")

7.  En el panel de resultados, seleccione **Huddle** y luego haga clic en **Completar** para agregar la aplicación.

    ![Huddle](./media/active-directory-saas-huddle-tutorial/IC787832.png "Huddle")
##Configuración del inicio de sesión único
  
El objetivo de esta sección es describir cómo permitir a los usuarios autenticarse en Huddle con su cuenta de Azure AD mediante federación basada en el protocolo SAML.

###Siga estos pasos para configurar el inicio de sesión único:

1.  En el Portal de Azure AD, en la página de integración de aplicaciones de **Huddle**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-huddle-tutorial/IC787833.png "Configurar inicio de sesión único")

2.  En la página **¿Cómo desea que los usuarios inicien sesión en Huddle?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y luego haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-huddle-tutorial/IC787834.png "Configurar inicio de sesión único")

3.  En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto **URL de inicio de sesión de Huddle**, escriba la dirección URL de su inquilino de Huddle con el siguiente patrón "**http://company.huddle.com*" y luego haga clic en **Siguiente**.

    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-huddle-tutorial/IC787835.png "Configurar dirección URL de la aplicación")

4.  En la página **Configurar inicio de sesión único en Huddle**, siga estos pasos:

    ![Configurar inicio de sesión único](./media/active-directory-saas-huddle-tutorial/IC787836.png "Configurar inicio de sesión único")

    1.  Haga clic en **Descargar certificado** y luego guarde el archivo de certificado en el equipo.
    2.  Copie el valor de **URL del emisor**, el valor de **Dirección URL de SSO de SAML** y el certificado descargado, y luego envíelos al equipo de soporte técnico de Huddle.

    >[AZURE.NOTE]El inicio de sesión único debe habilitarlo el equipo de soporte técnico de Huddle. Cuando se haya completado la configuración, recibirá una notificación.

5.  En el portal de Azure AD, seleccione la confirmación de configuración de inicio de sesión único y luego haga clic en **Completa** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-huddle-tutorial/IC787837.png "Configurar inicio de sesión único")
##Configuración del aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD inicien sesión en Huddle, deben aprovisionarse en Huddle. En el caso de Huddle, el aprovisionamiento es una tarea manual.

###Siga estos pasos para configurar el aprovisionamiento de usuario:

1.  Inicie sesión en el sitio de compañía de **Huddle** como administrador.

2.  Haga clic en **Área de trabajo**.

3.  Haga clic en **Contactos > Invitar a contactos**.

    ![Contactos](./media/active-directory-saas-huddle-tutorial/IC787838.png "Contactos")

4.  En la sección **Crear nueva invitación**, lleve a cabo estos pasos:

    ![Nueva invitación](./media/active-directory-saas-huddle-tutorial/IC787839.png "Nueva invitación")

    1.  En la lista **Elegir un equipo al que invitar a unirse a los contactos**, seleccione **equipo**.
    2.  En el cuadro de texto relacionado, escriba la **Dirección de correo electrónico** de una cuenta de AAD válida que quiera aprovisionar.
    3.  Haga clic en **Invitar**.

    >[AZURE.NOTE]El titular de la cuenta de Azure AD recibirá un mensaje de correo electrónico con un vínculo para confirmar la cuenta antes de que se active.

>[AZURE.NOTE]Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Huddle que proporcione Huddle para aprovisionar cuentas de usuario de AAD.

##Asignación de usuarios
  
Para probar la configuración, tiene que conceder acceso, mediante su asignación, a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

###Para asignar usuarios a Huddle, lleve a cabo los siguientes pasos:

1.  En el Portal de Azure AD, cree una cuenta de prueba.

2.  En la página de integración de aplicaciones de **Huddle **, haga clic en **Asignar usuarios**.

    ![Asignar usuarios](./media/active-directory-saas-huddle-tutorial/IC787840.png "Asignar usuarios")

3.  Seleccione su usuario de prueba, haga clic en **Asignar** y luego en **Sí** para confirmar la asignación.

    ![Sí](./media/active-directory-saas-huddle-tutorial/IC767830.png "Sí")
  
Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0121_2016-->