<properties 
    pageTitle="Tutorial: integración de Azure Active Directory con Clarizen | Microsoft Azure" 
    description="Aprenda a usar Clarizen con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automático, etc." 
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

#Tutorial: integración de Azure Active Directory con Clarizen

El objetivo de este tutorial es mostrar la integración de Azure y Clarizen. En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción de Azure válida
-   Una suscripción habilitada para el inicio de sesión único en Clarizen

Después de completar este tutorial, los usuarios de Azure AD que haya asignado a Clarizen podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de Clarizen (inicio de sesión iniciado por el proveedor de servicios) o desde [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1.  Habilitación de la integración de aplicaciones para Clarizen
2.  Configuración del inicio de sesión único
3.  Configuración del aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario](./media/active-directory-saas-clarizen-tutorial/IC784679.png "Escenario")
##Habilitación de la integración de aplicaciones para Clarizen

El objetivo de esta sección es describir cómo se habilita la integración de aplicaciones para Clarizen.

###Siga estos pasos para habilitar la integración de aplicaciones para Clarizen:

1.  En el panel de navegación izquierdo del Portal de administración de Azure, haga clic en **Active Directory**.

    ![Active Directory](./media/active-directory-saas-clarizen-tutorial/IC700993.png "Active Directory")

2.  En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3.  Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

    ![Aplicaciones](./media/active-directory-saas-clarizen-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación](./media/active-directory-saas-clarizen-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Agregar una aplicación de la galería](./media/active-directory-saas-clarizen-tutorial/IC749322.png "Agregar una aplicación de la galería")

6.  En el **cuadro de búsqueda**, escriba **Clarizen**.

    ![Galería de aplicaciones](./media/active-directory-saas-clarizen-tutorial/IC784680.png "Galería de aplicaciones")

7.  En el panel de resultados, seleccione **Clarizen** y luego haga clic en **Completar** para agregar la aplicación.

    ![Clarizen](./media/active-directory-saas-clarizen-tutorial/IC784681.png "Clarizen")
##Configuración del inicio de sesión único

El objetivo de esta sección es describir cómo permitir a los usuarios autenticarse en Clarizen con su cuenta de Azure AD mediante federación basada en el protocolo SAML.

###Siga estos pasos para configurar el inicio de sesión único:

1.  En el Portal de Azure AD, en la página de integración de aplicaciones de **Clarizen**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-clarizen-tutorial/IC784682.png "Configurar inicio de sesión único")

2.  En la página **¿Cómo desea que los usuarios inicien sesión en Clarizen?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y luego haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-clarizen-tutorial/IC784683.png "Configurar inicio de sesión único")

3.  En la página **Configuración de inicio de sesión único en Clarizen**, para descargar el certificado, haga clic en **Descargar certificado** y luego guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-clarizen-tutorial/IC784684.png "Configurar inicio de sesión único")

4.  En otra ventana del explorador web, inicie sesión en el sitio de la compañía de **Clarizen** como administrador (p.ej.: **https://app2.clarizen.com/Clarizen/Pages/Service/Login.aspx*).

5.  Haga clic en el nombre de usuario y luego haga clic en **Configuración**.

    ![Settings](./media/active-directory-saas-clarizen-tutorial/IC784685.png "Settings")

6.  Haga clic en la pestaña **Configuración global** y, luego, junto a **Autenticación federada** haga clic en**editar**.

    ![Configuración global](./media/active-directory-saas-clarizen-tutorial/IC786906.png "Configuración global")

7.  En el cuadro de diálogo **Federated Authentication** (Autenticación federada), realice los pasos siguientes:

    ![Autenticación federada](./media/active-directory-saas-clarizen-tutorial/IC785892.png "Autenticación federada")

    1.  Para cargar el certificado descargado, haga clic en **Cargar**.
    2.  En el Portal de Azure, en la página de diálogo **Configurar inicio de sesión único en Clarizen**, copie el valor de **Dirección URL del servicio de inicio de sesión único** y péguelo en el cuadro de texto **URL de inicio de sesión**.
    3.  En el Portal de Azure, en la página de diálogo **Configurar cierre de sesión único en Clarizen**, copie el valor de **Dirección URL del servicio de cierre de sesión único** y péguelo en el cuadro de texto **Sign-out URL** (URL de cierre de sesión).
    4.  Seleccione**Usar POST**.
    5.  Haga clic en **Guardar**.

8.  En el Portal de Azure AD, seleccione la confirmación de configuración de inicio de sesión único y, a continuación, haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-clarizen-tutorial/IC784688.png "Configurar inicio de sesión único")
##Configuración del aprovisionamiento de usuario

Para permitir que los usuarios de Azure AD inicien sesión en Clarizen, deben aprovisionarse en Clarizen. En el caso de Clarizen, el aprovisionamiento es una tarea manual.

###Para aprovisionar cuentas de usuario, realice estos pasos:

1.  Inicie sesión en el sitio de la compañía de **Clarizen** como administrador.

2.  Haga clic en**Contactos**.

    ![Contactos](./media/active-directory-saas-clarizen-tutorial/IC784689.png "Contactos")

3.  Haga clic en **Invitar a usuario**.

    ![Invitar a usuarios](./media/active-directory-saas-clarizen-tutorial/IC784690.png "Invitar a usuarios")

4.  En la página de diálogo Invite People (Invitar a contactos), realice los siguientes pasos:

    ![Invitar a contactos](./media/active-directory-saas-clarizen-tutorial/IC784691.png "Invitar a contactos")

    1.  En el cuadro de texto **Correo electrónico**, escriba la dirección de correo electrónico de la cuenta válida de Azure Active Directory que quiera aprovisionar.
    2.  Haga clic en **Invite** (Invitar).

    >[AZURE.NOTE]El titular de la cuenta de Azure Active Directory recibirá un mensaje de correo y seguirá un vínculo para confirmar su cuenta antes de que se active.

##Asignación de usuarios

Para probar la configuración, tiene que conceder acceso, mediante su asignación, a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

###Para asignar usuarios a Clarizen, siga estos pasos:

1.  En el Portal de Azure AD, cree una cuenta de prueba.

2.  En la página de integración de aplicaciones de **Clarizen **, haga clic en **Asignar usuarios**.

    ![Asignar usuarios](./media/active-directory-saas-clarizen-tutorial/IC784692.png "Asignar usuarios")

3.  Seleccione su usuario de prueba, haga clic en **Asignar** y luego en **Sí** para confirmar la asignación.

    ![Sí](./media/active-directory-saas-clarizen-tutorial/IC767830.png "Sí")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0121_2016-->