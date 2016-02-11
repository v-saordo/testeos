<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con Abintegro | Microsoft Azure" 
    description="Aprenda a usar Abintegro con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc." 
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

#Tutorial: Integración de Azure Active Directory con Abintegro

El objetivo de este tutorial es mostrar la integración de Azure y Abintegro. En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción de Azure válida
-   Una suscripción habilitada para el inicio de sesión único en Abintegro

Después de completar este tutorial, los usuarios de Azure AD que ha asignado a Abintegro podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de Abintegro (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md)

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1.  Habilitación de la integración de aplicaciones para Abintegro
2.  Configuración del inicio de sesión único
3.  Configuración del aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario](./media/active-directory-saas-abintegro-tutorial/IC790076.png "Escenario")
##Habilitación de la integración de aplicaciones para Abintegro

El objetivo de esta sección es describir cómo habilitar la integración de aplicaciones para Abintegro.

###Siga estos pasos para habilitar la integración de aplicaciones para Abintegro:

1.  En el panel de navegación izquierdo del Portal de administración de Azure, haga clic en **Active Directory**.

    ![Active Directory](./media/active-directory-saas-abintegro-tutorial/IC700993.png "Active Directory")

2.  En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3.  Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

    ![Aplicaciones](./media/active-directory-saas-abintegro-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación](./media/active-directory-saas-abintegro-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Agregar una aplicación de la galería](./media/active-directory-saas-abintegro-tutorial/IC749322.png "Agregar una aplicación de la galería")

6.  En el **cuadro de búsqueda**, escriba **abintegro**.

    ![Galería de aplicaciones](./media/active-directory-saas-abintegro-tutorial/IC790077.png "Galería de aplicaciones")

7.  En el panel de resultados, seleccione **Abintegro** y luego haga clic en **Completar** para agregar la aplicación.

    ![Abintegro](./media/active-directory-saas-abintegro-tutorial/IC790078.png "Abintegro")
##Configuración del inicio de sesión único

El objetivo de esta sección es describir cómo habilitar usuarios para que se autentiquen en Abintegro con su cuenta de Azure AD mediante federación basada en el protocolo SAML.

###Siga estos pasos para configurar el inicio de sesión único:

1.  En el Portal de Azure AD, en la página de integración de aplicaciones de **Abintegro**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-abintegro-tutorial/IC790079.png "Configurar inicio de sesión único")

2.  En la página **¿Cómo desea que los usuarios inicien sesión en Abintegro?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y luego haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-abintegro-tutorial/IC790080.png "Configurar inicio de sesión único")

3.  En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto **URL de inicio de sesión de Abintegro**, escriba la dirección URL que los usuarios usan para iniciar sesión en Abintegro (por ejemplo: `https://dev.abintegro.com/Shibboleth.sso/Login?entityID=<Issuer>&target=https://dev.abintegro.com/secure/`) y luego haga clic en **Siguiente**.

    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-abintegro-tutorial/IC790081.png "Configurar dirección URL de la aplicación")

4.  En la página **Configuración de inicio de sesión único en Abintegro**, haga clic en **Descargar metadatos** y luego guarde el archivo de metadatos en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-abintegro-tutorial/IC790082.png "Configurar inicio de sesión único")

5.  Envíe el archivo de metadatos al equipo de soporte técnico de Abintegro.

    >[AZURE.NOTE]La configuración del inicio de sesión único la debe realizar el equipo de soporte técnico de Abintegro. Tan pronto como se complete la configuración, recibirá una notificación.

6.  En el portal de Azure AD, seleccione la confirmación de configuración de inicio de sesión único y luego haga clic en **Completa** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-abintegro-tutorial/IC790083.png "Configurar inicio de sesión único")
##Configuración del aprovisionamiento de usuario

No hay elemento de acción para que configure el aprovisionamiento de usuarios en Abintegro. Cuando un usuario asignado intenta iniciar sesión en Abintegro desde el panel de acceso, Abintegro comprueba si el usuario existe. Si no hay cuentas de usuario disponibles, Abintegro crea una automáticamente.
##Asignación de usuarios

Para probar la configuración, tiene que conceder acceso, mediante su asignación, a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

###Para asignar usuarios a Abintegro, lleve a cabo los siguientes pasos:

1.  En el Portal de Azure AD, cree una cuenta de prueba.

2.  En la página de integración de aplicaciones de **Abintegro**, haga clic en **Asignar usuarios**.

    ![Asignar usuarios](./media/active-directory-saas-abintegro-tutorial/IC790084.png "Asignar usuarios")

3.  Seleccione su usuario de prueba, haga clic en **Asignar** y luego en **Sí** para confirmar la asignación.

    ![Sí](./media/active-directory-saas-abintegro-tutorial/IC767830.png "Sí")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0121_2016-->