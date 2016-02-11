<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con Cornerstone OnDemand | Microsoft Azure" 
    description="Aprenda cómo usar Cornerstone OnDemand con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc." 
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

#Tutorial: Integración de Azure Active Directory con Cornerstone OnDemand

El objetivo de este tutorial es mostrar la integración de Azure y Cornerstone OnDemand. En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción de Azure válida
-   Un inquilino de Cornerstone OnDemand

Después de completar este tutorial, los usuarios de Azure AD que ha asignado a Cornerstone OnDemand podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de Cornerstone OnDemand (inicio de sesión iniciado por el proveedor del servicio) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1.  Habilitación de la integración de aplicaciones para Cornerstone OnDemand
2.  Configuración del inicio de sesión único
3.  Configuración del aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781593.png "Escenario")
##Habilitación de la integración de aplicaciones para Cornerstone OnDemand

El objetivo de esta sección es describir cómo habilitar la integración de las aplicaciones para Cornerstone OnDemand.

###Siga estos pasos para habilitar la integración de aplicaciones para Cornerstone OnDemand:

1.  En el panel de navegación izquierdo del Portal de administración de Azure, haga clic en **Active Directory**.

    ![Active Directory](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC700993.png "Active Directory")

2.  En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3.  Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

    ![Aplicaciones](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Agregar una aplicación de la galería](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC749322.png "Agregar una aplicación de la galería")

6.  En el **cuadro de búsqueda**, escriba **cornerstone ondemand**.

    ![Galería de aplicaciones](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781594.png "Galería de aplicaciones")

7.  En el panel de resultados, seleccione **Cornerstone OnDemand** y, luego, haga clic en **Completa** para agregar la aplicación.

    ![Cornerstone OnDemand](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781595.png "Cornerstone OnDemand")
##Configuración del inicio de sesión único

El objetivo de esta sección es describir cómo se habilita la autenticación de los usuarios en Cornerstone OnDemand con su cuenta de Azure AD usando el protocolo SAML basado en la federación.

###Siga estos pasos para configurar el inicio de sesión único:

1.  En el portal de Azure AD, en la página de integración de aplicaciones de **Cornerstone OnDemand**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Habilitar el inicio de sesión único](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781596.png "Habilitar el inicio de sesión único")

2.  En la página **¿Cómo desea que los usuarios inicien sesión en Cornerstone OnDemand?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y, luego , haga clic en **Siguiente**.

    ![Inicio de sesión único de Microsoft Azure AD](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781597.png "Inicio de sesión único de Microsoft Azure AD")

3.  En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto **URL de inicio de sesión de Cornerstone OnDemand**, escriba su dirección URL con el siguiente patrón *http://company.csod.com*" y luego haga clic en **Siguiente**.

    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781598.png "Configurar dirección URL de la aplicación")

4.  En la página **Configurar inicio de sesión único en Cornerstone OnDemand**, para descargar el certificado, haga clic en **Descargar certificado** y luego guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781599.png "Configurar inicio de sesión único")

5.  Envíe los elementos siguientes al equipo de soporte técnico de Cornerstone OnDemand:

    1.  El certificado descargado
    2.  El valor **Dirección URL de inicio de sesión remoto**.
    3.  El valor **Dirección URL de cierre de sesión remoto**.

    >[AZURE.NOTE]El inicio de sesión único debe configurarlo el equipo de soporte técnico Cornerstone OnDemand. Cuando se haya completado la configuración, recibirá una notificación del equipo de soporte técnico.

6.  Seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781600.png "Configurar inicio de sesión único")
##Configuración del aprovisionamiento de usuario

Para permitir que los usuarios de Azure AD inicien sesión en Cornerstone OnDemand, deben aprovisionarse a Cornerstone OnDemand. En el caso de Cornerstone OnDemand, el aprovisionamiento es una tarea manual.

###Siga estos pasos para configurar el aprovisionamiento de usuario:

1.  Envíe la información (p. ej.: nombre, correo electrónico) sobre el usuario de Azure AD que desee aprovisionar al equipo de soporte técnico de Cornerstone OnDemand.

>[AZURE.NOTE]Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Cornerstone OnDemand ofrecida por Cornerstone OnDemand para aprovisionar cuentas de usuario de AAD.

##Asignación de usuarios

Para probar la configuración, tiene que conceder acceso, mediante su asignación, a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

###Para asignar usuarios a Cornerstone OnDemand, lleve a cabo los siguientes pasos:

1.  En el Portal de Azure AD, cree una cuenta de prueba.

2.  En la página de integración de la aplicación **Cornerstone OnDemand **, haga clic en **Asignar usuarios**.

    ![Asignar usuarios](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC775564.png "Asignar usuarios")

3.  Seleccione su usuario de prueba, haga clic en **Asignar** y luego en **Sí** para confirmar la asignación.

    ![Asignar usuarios](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781601.png "Asignar usuarios")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0121_2016-->