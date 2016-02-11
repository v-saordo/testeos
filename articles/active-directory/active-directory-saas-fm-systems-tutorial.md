<properties 
    pageTitle="Tutorial: integración de Azure Active Directory con FM: Systems | Microsoft Azure" 
    description="Aprenda a usar FM: Systems con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automático, etc." 
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

#Tutorial: integración de Azure Active Directory con FM: Systems
  
El objetivo de este tutorial es mostrar la integración de Azure y FM:Systems. En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción de Azure válida
-   Una suscripción habilitada para el inicio de sesión único en FM:Systems
  
Después de completar este tutorial, los usuarios de Azure AD que haya asignado a FM:Systems podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de FM:Systems (inicio de sesión iniciado por el proveedor de servicios) o desde [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
La situación descrita en este tutorial consta de los siguientes bloques de creación:

1.  Habilitación de la integración de aplicaciones para FM:Systems
2.  Configuración del inicio de sesión único
3.  Configuración del aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario](./media/active-directory-saas-fm-systems-tutorial/IC795899.png "Escenario")
##Habilitación de la integración de aplicaciones para FM:Systems
  
El objetivo de esta sección es describir cómo habilitar la integración de las aplicaciones para FM:Systems.

###Siga estos pasos para habilitar la integración de aplicaciones para FM:Systems:

1.  En el panel de navegación izquierdo del Portal de administración de Azure, haga clic en **Active Directory**.

    ![Active Directory](./media/active-directory-saas-fm-systems-tutorial/IC700993.png "Active Directory")

2.  En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3.  Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

    ![Aplicaciones](./media/active-directory-saas-fm-systems-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación](./media/active-directory-saas-fm-systems-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Agregar una aplicación de la galería](./media/active-directory-saas-fm-systems-tutorial/IC749322.png "Agregar una aplicación de la galería")

6.  En el **cuadro de búsqueda**, escriba **FM:Systems**.

    ![Galería de aplicaciones](./media/active-directory-saas-fm-systems-tutorial/IC795900.png "Galería de aplicaciones")

7.  En el panel de resultados, seleccione **FM:Systems** y, a continuación, haga clic en **Completar** para agregar la aplicación.

    ![FM: Systems](./media/active-directory-saas-fm-systems-tutorial/IC800213.png "FM: Systems")
##Configuración del inicio de sesión único
  
El objetivo de esta sección es describir cómo permitir a los usuarios autenticarse en FM:Systems con su cuenta de Azure AD mediante federación basada en el protocolo SAML.

###Siga estos pasos para configurar el inicio de sesión único:

1.  En el Portal de Azure AD, en la página de integración de aplicaciones de **FM:Systems**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-fm-systems-tutorial/IC790810.png "Configurar inicio de sesión único")

2.  En la página **¿Cómo desea que los usuarios inicien sesión en FM:Systems?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y luego haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-fm-systems-tutorial/IC795901.png "Configurar inicio de sesión único")

3.  En la página **Configurar dirección URL de la aplicación**, realice los pasos siguientes:

    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-fm-systems-tutorial/IC795902.png "Configurar dirección URL de la aplicación")

    1.  En el cuadro de texto **Dirección URL de inicio de sesión de FM:Systems**, escriba la **dirección URL de respuesta** de FM:Systems (p. ej.: **https://dpr.fmshosted.com/fminteract/ConsumerService2.aspx*).

        >[AZURE.WARNING]Este valor lo puede proporcionar el equipo de soporte técnico de FM:Systems.

    2.  Haga clic en **Siguiente**.

4.  En la página **Configurar inicio de sesión único en FM:Systems**, para descargar los metadatos, haga clic en **Descargar metadatos** y luego guarde los metadatos en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-fm-systems-tutorial/IC795903.png "Configurar inicio de sesión único")

5.  Envíe el archivo de metadatos descargado al equipo de soporte técnico de FM: Systems.

    >[AZURE.NOTE]El equipo de soporte técnico de FM: Systems es el que tiene que realizar la configuración real de SSO. Cuando SSO se haya habilitado en su suscripción recibirá una notificación.

6.  En el portal de Azure AD, seleccione la confirmación de configuración de inicio de sesión único y luego haga clic en **Completa** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-fm-systems-tutorial/IC795904.png "Configurar inicio de sesión único")
##Configuración del aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD inicien sesión en FM:Systems, deben aprovisionarse en FM:Systems. En el caso de FM:Systems, el aprovisionamiento es una tarea manual.

###Siga estos pasos para configurar el aprovisionamiento de usuario:

1.  En una ventana del explorador web, inicie sesión en el sitio de la compañía de FM:Systems como administrador.

2.  Vaya a **Administración del sistema > Administrar seguridad > Usuarios > Lista de usuarios**.

    ![Administración del sistema](./media/active-directory-saas-fm-systems-tutorial/IC795905.png "Administración del sistema")

3.  Haga clic en **Crear nuevo usuario**.

    ![Crear nuevo usuario](./media/active-directory-saas-fm-systems-tutorial/IC795906.png "Crear nuevo usuario")

4.  En la sección **Crear usuario**, lleve a cabo estos pasos:

    ![Crear usuario](./media/active-directory-saas-fm-systems-tutorial/IC795907.png "Crear usuario")

    1.  Escriba el nombre de usuario, la contraseña y su confirmación, la dirección de correo electrónico y el Id. de empleado de la cuenta de Azure Active Directory válida que quiera aprovisionar en los cuadros de texto relacionados.
    2.  Haga clic en **Siguiente**.

>[AZURE.NOTE]Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de FM:Systems que proporcione FM:Systems para aprovisionar cuentas de usuario de AAD.

##Asignación de usuarios
  
Para probar la configuración, tiene que conceder acceso, mediante su asignación, a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

###Para asignar usuarios a FM:Systems, lleve a cabo los siguientes pasos:

1.  En el Portal de Azure AD, cree una cuenta de prueba.

2.  En la página de integración de aplicaciones de **FM:Systems **, haga clic en **Asignar usuarios**.

    ![Asignar usuarios](./media/active-directory-saas-fm-systems-tutorial/IC795908.png "Asignar usuarios")

3.  Seleccione su usuario de prueba, haga clic en **Asignar** y luego en **Sí** para confirmar la asignación.

    ![Sí](./media/active-directory-saas-fm-systems-tutorial/IC767830.png "Sí")
  
Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0121_2016-->