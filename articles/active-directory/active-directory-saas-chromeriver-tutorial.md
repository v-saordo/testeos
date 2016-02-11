<properties 
    pageTitle="Tutorial: integración de Azure Active Directory con Chromeriver | Microsoft Azure" 
    description="Aprenda cómo usar Chromeriver con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc." 
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


#Tutorial: integración de Azure Active Directory con Chromeriver

El objetivo de este tutorial es mostrar la integración de Azure y Chromeriver. En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción de Azure válida
-   Una suscripción habilitada para inicio de sesión único en Chromeriver

Después de completar este tutorial, los usuarios de Azure AD que haya asignado a Chromeriver podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de Chromeriver (inicio de sesión iniciado por el proveedor de servicios) o desde [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1.  Habilitación de la integración de aplicaciones para Chromeriver
2.  Configuración del inicio de sesión único
3.  Configuración del aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario](./media/active-directory-saas-chromeriver-tutorial/IC802755.png "Escenario")
##Habilitación de la integración de aplicaciones para Chromeriver

El objetivo de esta sección es describir cómo se habilita la integración de aplicaciones para Chromeriver.

###Siga estos pasos para habilitar la integración de aplicaciones para Chromeriver:

1.  En el panel de navegación izquierdo del Portal de administración de Azure, haga clic en **Active Directory**.

    ![Active Directory](./media/active-directory-saas-chromeriver-tutorial/IC700993.png "Active Directory")

2.  En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3.  Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

    ![Aplicaciones](./media/active-directory-saas-chromeriver-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación](./media/active-directory-saas-chromeriver-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Agregar una aplicación de la galería](./media/active-directory-saas-chromeriver-tutorial/IC749322.png "Agregar una aplicación de la galería")

6.  En el **cuadro de búsqueda**, escriba **Chromeriver**.

    ![Galería de aplicaciones](./media/active-directory-saas-chromeriver-tutorial/IC802756.png "Galería de aplicaciones")

7.  En el panel de resultados, seleccione **Chromeriver** y luego haga clic en **Completar** para agregar la aplicación.
##Configuración del inicio de sesión único

El objetivo de esta sección es describir cómo habilitar usuarios para que se autentiquen en Chromeriver con su cuenta de Azure AD mediante federación basada en el protocolo SAML.

###Siga estos pasos para configurar el inicio de sesión único:

1.  En el Portal de Azure AD, en la página de integración de aplicaciones de **Chromeriver**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-chromeriver-tutorial/IC802757.png "Configurar inicio de sesión único")

2.  En la página **¿Cómo desea que los usuarios inicien sesión en Chromeriver?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y luego haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-chromeriver-tutorial/IC802758.png "Configurar inicio de sesión único")

3.  En la página **Configurar las opciones de la aplicación**, realice los pasos siguientes:

    ![Configurar las opciones de la aplicación](./media/active-directory-saas-chromeriver-tutorial/IC802759.png "Configurar las opciones de la aplicación")

    1.  En el cuadro de texto **URL de respuesta**, escriba su dirección URL de **AssertionConsumerService** de Chromeriver (p. ej.: **https://qa-app.chromeriver.com/login/sso/saml/consume?customerId=911*)).

        >[AZURE.NOTE]Este valor lo puede proporcionar el equipo de soporte técnico de Chromeriver.

    2.  Haga clic en **Siguiente**.

4.  En la página **Configurar inicio de sesión único en Chromeriver**, para descargar los metadatos, haga clic en **Descargar metadatos** y luego guarde el archivo de metadatos en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-chromeriver-tutorial/IC802760.png "Configurar inicio de sesión único")

5.  Envíe el archivo de metadatos descargado al equipo de soporte técnico de Chromeriver.

    >[AZURE.NOTE]El equipo de soporte técnico de Chromeriver es el que tiene que realizar la configuración real de SSO. Cuando SSO se haya habilitado en su suscripción recibirá una notificación.

6.  En el portal de Azure AD, seleccione la confirmación de configuración de inicio de sesión único y luego haga clic en **Completa** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-chromeriver-tutorial/IC802761.png "Configurar inicio de sesión único")
##Configuración del aprovisionamiento de usuario

Para permitir que los usuarios de Azure AD inicien sesión en Chromeriver, deben aprovisionarse en Chromeriver. En el caso de Chromeriver, las cuentas de usuario debe crearlas el equipo de soporte técnico de Chromeriver.

>[AZURE.NOTE]Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Chromeriver que proporcione Chromeriver para aprovisionar cuentas de usuario de Azure Active Directory.

##Asignación de usuarios

Para probar la configuración, tiene que conceder acceso, mediante su asignación, a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

###Para asignar usuarios a Chromeriver, lleve a cabo los siguientes pasos:

1.  En el Portal de Azure AD, cree una cuenta de prueba.

2.  En la página de integración de aplicaciones de **Chromeriver ** haga clic en **Asignar usuarios**.

    ![Asignar usuarios](./media/active-directory-saas-chromeriver-tutorial/IC802762.png "Asignar usuarios")

3.  Seleccione su usuario de prueba, haga clic en **Asignar** y luego en **Sí** para confirmar la asignación.

    ![Sí](./media/active-directory-saas-chromeriver-tutorial/IC767830.png "Sí")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0121_2016-->