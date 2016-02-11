<properties
    pageTitle="Tutorial: integración de Azure Active Directory con ABa Sainsburys Connect | Microsoft Azure" 
    description="Aprenda a usar ABa Sainsburys Connect con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc." 
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

#Tutorial: integración de Azure Active Directory con ABa Sainsburys Connect

El objetivo de este tutorial es mostrar la integración de Azure y ABa Sainsburys Connect. En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción de Azure válida
-   Una suscripción habilitada para el inicio de sesión único en ABa Sainsburys Connect

Después de completar este tutorial, los usuarios de Azure AD que ha asignado a ABa Sainsburys Connect podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de ABa Sainsburys Connect (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md)

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1.  Habilitación de la integración de aplicaciones para ABa Sainsburys Connect
2.  Configuración del inicio de sesión único
3.  Configuración del aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC807723.png "Escenario")
##Habilitación de la integración de aplicaciones para ABa Sainsburys Connect

El objetivo de esta sección es describir cómo habilitar la integración de aplicaciones para ABa Sainsburys Connect.

###Siga estos pasos para habilitar la integración de aplicaciones para ABa Sainsburys Connect:

1.  En el panel de navegación izquierdo del Portal de administración de Azure, haga clic en **Active Directory**.

    ![Active Directory](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC700993.png "Active Directory")

2.  En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3.  Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

    ![Aplicaciones](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Agregar una aplicación de la galería](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC749322.png "Agregar una aplicación de la galería")

6.  En el **cuadro de búsqueda**, escriba **ABa Sainsburys Connect**.

    ![ABa Sainsburys Connect](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC807724.png "ABa Sainsburys Connect")

7.  En el panel de resultados, seleccione **ABa Sainsburys Connect** y, luego, haga clic en **Completar** para agregar la aplicación.

    ![ABa Sainsburys Connect](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC807725.png "ABa Sainsburys Connect")
##Configuración del inicio de sesión único

El objetivo de esta sección es describir cómo habilitar usuarios para que se autentiquen en ABa Sainsburys Connect con su cuenta de Azure AD mediante federación basada en el protocolo SAML.

###Siga estos pasos para configurar el inicio de sesión único:

1.  En el Portal de Azure AD, en la página de integración de aplicaciones de **ABa Sainsburys Connect**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC807726.png "Configurar inicio de sesión único")

2.  En la página **¿Cómo desea que los usuarios inicien sesión en ABa Sainsburys Connect?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y, a continuación, haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC807727.png "Configurar inicio de sesión único")

3.  En la página **Configurar las opciones de la aplicación**, realice los pasos siguientes:

    ![Configurar las opciones de la aplicación](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC807728.png "Configurar las opciones de la aplicación")

    1.  En el cuadro de texto **URL de inicio de sesión**, escriba la dirección URL que los usuarios usan para iniciar sesión en la aplicación ABa Sainsburys Connect (p. ej.: **https://myaba.co.uk/client-access/sainsburys/saml.php*).
    2.  Haga clic en **Siguiente**.

4.  En la página **Configurar inicio de sesión único en Aba Sainsburys Connect**, para descargar los metadatos, haga clic en **Descargar metadatos** y luego guarde el archivo de metadatos en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC807729.png "Configurar inicio de sesión único")

5.  Envíe el archivo de metadatos descargado al equipo de soporte técnico de ABa Sainsburys Connect.

    >[AZURE.NOTE]El equipo de soporte técnico de ABa Sainsburys Connect es el que tiene que realizar la configuración real de SSO. Cuando SSO se haya habilitado en su suscripción recibirá una notificación.

6.  En el portal de Azure AD, seleccione la confirmación de configuración de inicio de sesión único y luego haga clic en **Completa** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC807730.png "Configurar inicio de sesión único")
##Configuración del aprovisionamiento de usuario

Para permitir que los usuarios de Azure AD inicien sesión en ABa Sainsburys Connect, deben aprovisionarse en ABa Sainsburys Connect. En el caso de ABa Sainsburys Connect, el equipo de soporte técnico de ABa Sainsburys Connect debe crear las cuentas de usuario.

>[AZURE.NOTE]Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de ABa Sainsburys Connect que proporcione ABa Sainsburys Connect para aprovisionar cuentas de usuario de Azure Active Directory.

##Asignación de usuarios

Para probar la configuración, tiene que conceder acceso, mediante su asignación, a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

###Para asignar usuarios a ABa Sainsburys Connect, lleve a cabo los siguientes pasos:

1.  En el Portal de Azure AD, cree una cuenta de prueba.

2.  En la página de integración de aplicaciones de **ABa Sainsburys Connect**, haga clic en **Asignar usuarios**.

    ![Asignar usuarios](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC807731.png "Asignar usuarios")

3.  Seleccione su usuario de prueba, haga clic en **Asignar** y luego en **Sí** para confirmar la asignación.

    ![Sí](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC767830.png "Sí")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0121_2016-->