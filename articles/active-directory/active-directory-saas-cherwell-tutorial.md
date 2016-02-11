<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con Cherwell | Microsoft Azure" 
    description="Aprenda a usar Cherwell con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc." 
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
    ms.date="01/05/2016" 
    ms.author="jeedes" />

#Tutorial: Integración de Azure Active Directory con Cherwell

El objetivo de este tutorial es mostrar la integración de Azure y Cherwell. En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción de Azure válida
-   Una suscripción habilitada para inicio de sesión único en Cherwell

Después de completar este tutorial, los usuarios de Azure AD que ha asignado a Cherwell podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de Cherwell (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1.  Habilitación de la integración de aplicaciones para Cherwell
2.  Configuración del inicio de sesión único
3.  Configuración del aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario](./media/active-directory-saas-cherwell-tutorial/IC798988.png "Escenario")
##Habilitación de la integración de aplicaciones para Cherwell

El objetivo de esta sección es describir cómo se habilita la integración de aplicaciones para Cherwell.

###Siga estos pasos para habilitar la integración de aplicaciones para Cherwell:

1.  En el panel de navegación izquierdo del Portal de administración de Azure, haga clic en **Active Directory**.

    ![Active Directory](./media/active-directory-saas-cherwell-tutorial/IC700993.png "Active Directory")

2.  En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3.  Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

    ![Aplicaciones](./media/active-directory-saas-cherwell-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación](./media/active-directory-saas-cherwell-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Agregar una aplicación de la galería](./media/active-directory-saas-cherwell-tutorial/IC749322.png "Agregar una aplicación de la galería")

6.  En el **cuadro de búsqueda**, escriba **Cherwell**.

    ![Cherwell](./media/active-directory-saas-cherwell-tutorial/IC798989.png "Cherwell")

7.  En el panel de resultados, seleccione **Cherwell** y luego haga clic en **Completar** para agregar la aplicación.
##Configuración del inicio de sesión único

El objetivo de esta sección es describir cómo habilitar usuarios para que se autentiquen en Cherwell con su cuenta de Azure AD mediante federación basada en el protocolo SAML.

###Siga estos pasos para configurar el inicio de sesión único:

1.  En el Portal de Azure AD, en la página de integración de aplicaciones de **Cherwell**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-cherwell-tutorial/IC798990.png "Configurar inicio de sesión único")

2.  En la página **¿Cómo desea que los usuarios inicien sesión en Cherwell?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y luego haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-cherwell-tutorial/IC798991.png "Configurar inicio de sesión único")

3.  En la página **Configurar dirección URL de la aplicación**, realice los pasos siguientes:

    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-cherwell-tutorial/IC798992.png "Configurar dirección URL de la aplicación")

    1.  En el cuadro de texto **Dirección URL de inicio de sesión**, escriba la dirección URL que los usuarios usan para iniciar sesión en la aplicación **Cherwell** (p. ej.: **https://pictdev.cherwellondemand.com/cherwellclient*)
    2.  Haga clic en **Siguiente**.

4.  En la página **Configurar inicio de sesión único en Cherwell**, siga estos pasos:

    ![Configurar inicio de sesión único](./media/active-directory-saas-cherwell-tutorial/IC798993.png "Configurar inicio de sesión único")

    1.  Haga clic en **Descargar certificado** y luego guarde el certificado en el equipo.
    2.  Copie la **Dirección URL del proveedor de identidades**.
    3.  Copie la **Dirección URL del servicio de inicio de sesión único**.
    4.  Haga clic en **Siguiente**.

5.  Envíe el certificado descargado, la **Dirección URL del proveedor de identidades** y la **Dirección URL del servicio de inicio de sesión único** al equipo de soporte de Cherwell.

    >[AZURE.NOTE]El equipo de soporte técnico de Cherwell es el que tiene que realizar la configuración real de SSO. Cuando SSO se haya habilitado en su suscripción recibirá una notificación.

6.  En el portal de Azure AD, seleccione la confirmación de configuración de inicio de sesión único y luego haga clic en **Completa** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-cherwell-tutorial/IC798994.png "Configurar inicio de sesión único")
##Configuración del aprovisionamiento de usuario

Para permitir que los usuarios de Azure AD inicien sesión en Cherwell, tienen que aprovisionarse en Cherwell. En el caso de Cherwell, las cuentas de usuario debe crearlas el equipo de soporte técnico de Cherwell.

>[AZURE.NOTE]Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Cherwell que proporcione Cherwell para aprovisionar cuentas de usuario de Azure Active Directory.

##Asignación de usuarios

Para probar la configuración, tiene que conceder acceso, mediante su asignación, a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

###Para asignar usuarios a Cherwell, lleve a cabo los siguientes pasos:

1.  En el Portal de Azure AD, cree una cuenta de prueba.

2.  En la página de integración de aplicaciones de **Cherwell**, haga clic en **Asignar usuarios**.

    ![Asignar usuarios](./media/active-directory-saas-cherwell-tutorial/IC798995.png "Asignar usuarios")

3.  Seleccione su usuario de prueba, haga clic en **Asignar** y luego en **Sí** para confirmar la asignación.

    ![Sí](./media/active-directory-saas-cherwell-tutorial/IC767830.png "Sí")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0107_2016-->