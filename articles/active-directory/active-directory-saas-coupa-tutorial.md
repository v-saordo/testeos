<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con Coupa | Microsoft Azure" 
    description="Aprenda a usar Coupa con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc." 
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

#Tutorial: Integración de Azure Active Directory con Coupa

El objetivo de este tutorial es mostrar la integración de Azure y Coupa. En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción de Azure válida
-   Una suscripción habilitada para el inicio de sesión único en Coupa

Después de completar este tutorial, los usuarios de Azure AD que ha asignado a Coupa podrá realizar un inicio de sesión único en la aplicación con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1.  Habilitación de la integración de aplicaciones para Coupa
2.  Configuración del inicio de sesión único
3.  Configuración del aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario](./media/active-directory-saas-coupa-tutorial/IC791897.png "Escenario")
##Habilitación de la integración de aplicaciones para Coupa

El objetivo de esta sección es describir cómo se habilita la integración de las aplicaciones para Coupa.

###Siga estos pasos para habilitar la integración de aplicaciones para Coupa:

1.  En el panel de navegación izquierdo del Portal de administración de Azure, haga clic en **Active Directory**.

    ![Active Directory](./media/active-directory-saas-coupa-tutorial/IC700993.png "Active Directory")

2.  En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3.  Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

    ![Aplicaciones](./media/active-directory-saas-coupa-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación](./media/active-directory-saas-coupa-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Agregar una aplicación de la galería](./media/active-directory-saas-coupa-tutorial/IC749322.png "Agregar una aplicación de la galería")

6.  En el **cuadro de búsqueda**, escriba **Coupa**.

    ![Galería de aplicaciones](./media/active-directory-saas-coupa-tutorial/IC791898.png "Galería de aplicaciones")

7.  En el panel de resultados, seleccione **Coupa** y luego haga clic en **Completar** para agregar la aplicación.

    ![Coupa](./media/active-directory-saas-coupa-tutorial/IC791899.png "Coupa")
##Configuración del inicio de sesión único

El objetivo de esta sección es describir cómo se habilita la autenticación de usuarios en Coupa con su cuenta de Azure AD mediante la federación basada en el protocolo SAML. La configuración de un inicio de sesión único para Coupa requiere la recuperación de un valor de huella digital de un certificado. Si no está familiarizado con este procedimiento, consulte [Recuperación del valor de huella digital de un certificado](http://youtu.be/YKQF266SAxI).

###Siga estos pasos para configurar el inicio de sesión único:

1.  Inicie sesión en su sitio de la compañía de Coupa como administrador.

2.  Vaya a **Configuración > Control de seguridad**.

    ![Controles de seguridad](./media/active-directory-saas-coupa-tutorial/IC791900.png "Controles de seguridad")

3.  Para descargar el archivo de metadatos de Coupa en el equipo, haga clic en **Descargar e importar metadatos de SP**.

    ![Metadatos de SP Coupa](./media/active-directory-saas-coupa-tutorial/IC791901.png "Metadatos de SP Coupa")

4.  En otra ventana de explorador, inicie sesión en el Portal de Azure Active Directory.

5.  En la página de integración de aplicaciones de **Coupa**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-coupa-tutorial/IC791902.png "Configurar inicio de sesión único")

6.  En la página **¿Cómo desea que los usuarios inicien sesión en Coupa?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y luego haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-coupa-tutorial/IC791903.png "Configurar inicio de sesión único")

7.  En la página **Configurar dirección URL de la aplicación**, realice los pasos siguientes:

    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-coupa-tutorial/IC791904.png "Configurar dirección URL de la aplicación")

    1.  En el cuadro de texto **Dirección URL de inicio de sesión**, escriba la dirección URL que los usuarios usan para iniciar sesión en la aplicación Coupa (p. ej.: “*http://company.Coupa.com*”)
    2.  Abra el archivo de metadatos de Coupa descargado y después copie la **dirección URL/índice AssertionConsumerService**.
    3.  En el cuadro de texto **Dirección URL de respuesta de Coupa**, pegue el valor de **la dirección URL/índice AssertionConsumerService**.
    4.  Haga clic en **Siguiente**.

8.  En la página **Configuración de inicio de sesión único en Coupa**, para descargar su archivo de metadatos, haga clic en **Descargar metadatos** y luego guarde el archivo localmente en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-coupa-tutorial/IC791905.png "Configurar inicio de sesión único")

9.  En el sitio de la compañía de Coupa, vaya a **Configuración > Control de seguridad**.

    ![Controles de seguridad](./media/active-directory-saas-coupa-tutorial/IC791900.png "Controles de seguridad")

10. En la sección **Iniciar sesión con credenciales de Coupa**, realice los pasos siguientes:

    ![Iniciar sesión con credenciales de Coupa](./media/active-directory-saas-coupa-tutorial/IC791906.png "Iniciar sesión con credenciales de Coupa")

    1.  Seleccione **Iniciar sesión con SAML**.
    2.  Haga clic en **Examinar** para cargar el archivo de metadatos de Azure Active descargado.
    3.  Haga clic en **Guardar**.

11. En el Portal de Azure AD, seleccione la confirmación de configuración de inicio de sesión único y, a continuación, haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-coupa-tutorial/IC791907.png "Configurar inicio de sesión único")
##Configuración del aprovisionamiento de usuario

Para permitir que los usuarios de Azure AD inicien sesión en Coupa, tienen que aprovisionarse en Coupa. En el caso de Coupa, el aprovisionamiento es una tarea manual.

###Siga estos pasos para configurar el aprovisionamiento de usuario:

1.  Inicie sesión como administrador en el sitio de la compañía de **Coupa**.

2.  En el menú en la parte superior, haga clic en **Configurar** y, luego, en **Usuarios**.

    ![Usuarios](./media/active-directory-saas-coupa-tutorial/IC791908.png "Usuarios")

3.  Haga clic en **Crear**.

    ![Crear usuarios](./media/active-directory-saas-coupa-tutorial/IC791909.png "Crear usuarios")

4.  En la sección **Creación de usuario**, lleve a cabo estos pasos:

    ![Detalles del usuario](./media/active-directory-saas-coupa-tutorial/IC791910.png "Detalles del usuario")

    1.  En los cuadros de texto relacionados, escriba los atributos **Nombre de usuario**, **Nombre**, **Apellido**, **Id. de inicio de sesión único**, **Correo electrónico** de una cuenta válida de Azure Active Directory que quiera aprovisionar.
    2.  Haga clic en **Crear**.

    >[AZURE.NOTE]El titular de la cuenta de Azure Active Directory recibirá un mensaje de correo electrónico con un vínculo para confirmar la cuenta antes de que se active.

>[AZURE.NOTE]Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Canvas ofrecida por Coupa para aprovisionar cuentas de usuario de AAD.

##Asignación de usuarios

Para probar la configuración, tiene que conceder acceso, mediante su asignación, a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

###Para asignar usuarios a Coupa, lleve a cabo los siguientes pasos:

1.  En el Portal de Azure AD, cree una cuenta de prueba.

2.  En la página de integración de aplicaciones de **Coupa**, haga clic en **Asignar usuarios**.

    ![Asignar usuarios](./media/active-directory-saas-coupa-tutorial/IC791911.png "Asignar usuarios")

3.  Seleccione su usuario de prueba, haga clic en **Asignar** y luego en **Sí** para confirmar la asignación.

    ![Sí](./media/active-directory-saas-coupa-tutorial/IC767830.png "Sí")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0121_2016-->