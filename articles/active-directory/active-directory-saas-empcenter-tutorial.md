<properties 
    pageTitle="Tutorial: integración de Azure Active Directory con EmpCenter | Microsoft Azure" 
    description="Aprenda a usar EmpCenter con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automático, etc." 
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
    ms.date="02/29/2016" 
    ms.author="jeedes" />

#Tutorial: integración de Azure Active Directory con EmpCenter
  
El objetivo de este tutorial es mostrar la integración de Azure y EmpCenter. En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción de Azure válida
-   Una suscripción habilitada para el inicio de sesión único en EmpCenter
  
Después de completar este tutorial, los usuarios de Azure AD que haya asignado a EmpCenter podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de EmpCenter (inicio de sesión iniciado por el proveedor de servicios) o desde [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
La situación descrita en este tutorial consta de los siguientes bloques de creación:

1.  Habilitación de la integración de aplicaciones para EmpCenter
2.  Configuración del inicio de sesión único
3.  Configuración del aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario](./media/active-directory-saas-empcenter-tutorial/IC802916.png "Escenario")
##Habilitación de la integración de aplicaciones para EmpCenter
  
El objetivo de esta sección es describir cómo se habilita la integración de aplicaciones para EmpCenter.

###Siga estos pasos para habilitar la integración de aplicaciones para EmpCenter:

1.  En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.

    ![Active Directory](./media/active-directory-saas-empcenter-tutorial/IC700993.png "Active Directory")

2.  En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3.  Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

    ![Aplicaciones](./media/active-directory-saas-empcenter-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación](./media/active-directory-saas-empcenter-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Agregar una aplicación de la galería](./media/active-directory-saas-empcenter-tutorial/IC749322.png "Agregar una aplicación de la galería")

6.  En el **cuadro de búsqueda**, escriba **EmpCenter**.

    ![Galería de aplicaciones](./media/active-directory-saas-empcenter-tutorial/IC802917.png "Galería de aplicaciones")

7.  En el panel de resultados, seleccione **EmpCenter** y luego haga clic en **Completar** para agregar la aplicación.

    ![EmpCentral](./media/active-directory-saas-empcenter-tutorial/IC802918.png "EmpCentral")
##Configuración del inicio de sesión único
  
El objetivo de esta sección es describir cómo permitir a los usuarios autenticarse en EmpCenter con su cuenta de Azure AD mediante el protocolo SAML basado en la federación.

###Siga estos pasos para configurar el inicio de sesión único:

1.  En el Portal de Azure clásico, en la página de integración de la aplicación **EmpCenter**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único **.

    ![Configurar inicio de sesión único](./media/active-directory-saas-empcenter-tutorial/IC802919.png "Configurar inicio de sesión único")

2.  En la página **¿Cómo desea que los usuarios inicien sesión en EmpCenter?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y luego haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-empcenter-tutorial/IC802920.png "Configurar inicio de sesión único")

3.  En la página **Configurar las opciones de la aplicación**, realice los pasos siguientes:

    ![Configurar las opciones de la aplicación](./media/active-directory-saas-empcenter-tutorial/IC802921.png "Configurar las opciones de la aplicación")

    1.  En el cuadro de texto **Dirección URL de inicio de sesión**, escriba la dirección URL que los usuarios usan para iniciar sesión en la aplicación EmpCenter (p. ej.: *https://partner-authenticati.empcenter.com/workforce/SSO.do*)).
    2.  Haga clic en **Siguiente**.

4.  En la página **Configurar inicio de sesión único en EmpCenter**, para descargar los metadatos, haga clic en **Descargar metadatos** y luego guarde el archivo de metadatos en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-empcenter-tutorial/IC802922.png "Configurar inicio de sesión único")

5.  Envíe el archivo de metadatos descargado al equipo de soporte técnico de EmpCenter.

    >[AZURE.NOTE] El equipo de soporte técnico de EmpCenter es el que tiene que realizar la configuración real de SSO. Cuando SSO se haya habilitado en su suscripción recibirá una notificación.

6.  En el Portal de Azure clásico, seleccione la confirmación de configuración de inicio de sesión único y, luego, haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-empcenter-tutorial/IC802923.png "Configurar inicio de sesión único")
##Configuración del aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD inicien sesión en EmpCenter, deben aprovisionarse en EmpCenter. 
En el caso de EmpCenter, las cuentas de usuario debe crearlas el equipo de soporte técnico de EmpCenter.

>[AZURE.NOTE] Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de EmpCenter que proporcione EmpCenter para aprovisionar cuentas de usuario de Azure Active Directory.

##Asignación de usuarios
  
Para probar la configuración, tiene que conceder acceso, mediante su asignación, a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

###Para asignar usuarios a EmpCenter, lleve a cabo los siguientes pasos:

1.  En el Portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicaciones de **EmpCenter ** haga clic en **Asignar usuarios**.

    ![Asignar usuarios](./media/active-directory-saas-empcenter-tutorial/IC802924.png "Asignar usuarios")

3.  Seleccione su usuario de prueba, haga clic en **Asignar** y luego en **Sí** para confirmar la asignación.

    ![Sí](./media/active-directory-saas-empcenter-tutorial/IC767830.png "Sí")
  
Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0302_2016-->
