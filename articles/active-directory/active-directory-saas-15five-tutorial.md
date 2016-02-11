<properties 
    pageTitle="Tutorial: integración de Azure Active Directory con 15Five | Microsoft Azure" 
    description="Aprenda a usar 15Five con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc." 
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

#Tutorial: integración de Azure Active Directory con 15Five

El objetivo de este tutorial es mostrar la integración de Azure y 15Five. En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción de Azure válida
-   Una suscripción habilitada para el inicio de sesión único en 15Five

Después de completar este tutorial, los usuarios de Azure AD que ha asignado a 15Five podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de 15Five (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md)

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1.  Habilitación de la integración de aplicaciones para 15Five
2.  Configuración del inicio de sesión único
3.  Configuración del aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario](./media/active-directory-saas-15five-tutorial/IC784667.png "Escenario")
##Habilitación de la integración de aplicaciones para 15Five

El objetivo de esta sección es describir cómo se habilita la integración de aplicaciones en 15Five.

###Siga estos pasos para habilitar la integración de aplicaciones para 15Five:

1.  En el panel de navegación izquierdo del Portal de administración de Azure, haga clic en **Active Directory**.

    ![Active Directory](./media/active-directory-saas-15five-tutorial/IC700993.png "Active Directory")

2.  En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3.  Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

    ![Aplicaciones](./media/active-directory-saas-15five-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación](./media/active-directory-saas-15five-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Agregar una aplicación de la galería](./media/active-directory-saas-15five-tutorial/IC749322.png "Agregar una aplicación de la galería")

6.  En el **cuadro de búsqueda**, escriba **15Five**.

    ![Galería de aplicaciones](./media/active-directory-saas-15five-tutorial/IC784668.png "Galería de aplicaciones")

7.  En el panel de resultados, seleccione **15Five** y luego haga clic en **Completar** para agregar la aplicación.

    ![15Five](./media/active-directory-saas-15five-tutorial/IC784669.png "15Five")
##Configuración del inicio de sesión único

El objetivo de esta sección es describir cómo habilitar usuarios para que se autentiquen en 15Five con su cuenta de Azure AD mediante federación basada en el protocolo SAML.

###Siga estos pasos para configurar el inicio de sesión único:

1.  En el Portal de Azure AD, en la página de integración de aplicaciones de **15Five**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-15five-tutorial/IC784670.png "Configurar inicio de sesión único")

2.  En la página **¿Cómo desea que los usuarios inicien sesión en 15Five?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y luego haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-15five-tutorial/IC784671.png "Configurar inicio de sesión único")

3.  En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto de **URL de inicio de sesión de 15Five**, escriba su dirección URL con el siguiente patrón "**https://company.15Five.com*" y luego haga clic en **Siguiente**.

    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-15five-tutorial/IC784672.png "Configurar dirección URL de la aplicación")

4.  En la página **Configuración de inicio de sesión único en 15Five**, haga clic en **Descargar metadatos** y, luego, reenvíe el archivo de metadatos al equipo de soporte técnico de 15Five.

    ![Configurar inicio de sesión único](./media/active-directory-saas-15five-tutorial/IC784673.png "Configurar inicio de sesión único")

    >[AZURE.NOTE]El inicio de sesión único debe habilitarlo el equipo de soporte técnico de 15Five.

5.  En el portal de Azure AD, seleccione la confirmación de configuración de inicio de sesión único y luego haga clic en **Completa** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-15five-tutorial/IC784674.png "Configurar inicio de sesión único")
##Configuración del aprovisionamiento de usuario

Para permitir que los usuarios de Azure AD inicien sesión en 15Five, deben aprovisionarse en 15Five. En el caso de 15Five, el aprovisionamiento es una tarea manual.

###Siga estos pasos para configurar el aprovisionamiento de usuario:

1.  Inicie sesión en el sitio de la compañía de **15Five** como administrador.

2.  Vaya a **Administrar compañía**.

    ![Manage Company (Administrar compañía)](./media/active-directory-saas-15five-tutorial/IC784675.png "Manage Company (Administrar compañía)")

3.  Vaya a **Contactos > Agregar contactos**.

    ![Contactos](./media/active-directory-saas-15five-tutorial/IC784676.png "Contactos")

4.  En la sección Add New Person (Agregar nueva persona), lleve a cabo estos pasos:

    ![Add New Person (Agregar nueva persona)](./media/active-directory-saas-15five-tutorial/IC784677.png "Add New Person (Agregar nueva persona)")

    1.  Especifique **First Name** (Nombre), **Last Name** (Apellido), **Title** (Título), **Email address** (Id. de correo electrónico) de una cuenta de Azure Active Directory válida que quiera aprovisionar en los cuadros de texto relacionados.
    2.  Haga clic en **Done**.

    >[AZURE.NOTE]El titular de la cuenta de Azure AD recibirá un mensaje de correo electrónico con un vínculo para confirmar la cuenta antes de que se active.

>[AZURE.NOTE]Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de 15Five que proporcione 15Five para aprovisionar cuentas de usuario de AAD.

##Asignación de usuarios

Para probar la configuración, tiene que conceder acceso, mediante su asignación, a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

###Para asignar usuarios a 15Five, lleve a cabo los siguientes pasos:

1.  En el Portal de Azure AD, cree una cuenta de prueba.

2.  En la página de integración de aplicaciones de **15Five**, haga clic en **Asignar usuarios**.

    ![Asignar usuarios](./media/active-directory-saas-15five-tutorial/IC784678.png "Asignar usuarios")

3.  Seleccione su usuario de prueba, haga clic en **Asignar** y luego en **Sí** para confirmar la asignación.

    ![Sí](./media/active-directory-saas-15five-tutorial/IC767830.png "Sí")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0121_2016-->