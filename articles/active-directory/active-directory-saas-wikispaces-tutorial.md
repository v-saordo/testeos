<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con Wikispaces | Microsoft Azure" 
    description="Aprenda cómo usar Wikispaces con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc." 
    services="active-directory" 
    authors="MarkusVi"  
    documentationCenter="na" 
    manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="01/12/2016" 
    ms.author="markvi" />

#Tutorial: Integración de Azure Active Directory con Wikispaces
  
El objetivo de este tutorial es mostrar la integración de Azure y Wikispaces. En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción de Azure válida
-   Una suscripción habilitada para el inicio de sesión único en Wikispaces
  
Después de completar este tutorial, los usuarios de Azure AD que ha asignado a Wikispaces podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de Wikispaces (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
La situación descrita en este tutorial consta de los siguientes bloques de creación:

1.  Habilitación de la integración de aplicaciones para Wikispaces
2.  Configuración del inicio de sesión único
3.  Configuración del aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario](./media/active-directory-saas-wikispaces-tutorial/IC787182.png "Escenario")

##Habilitación de la integración de aplicaciones para Wikispaces
  
El objetivo de esta sección es describir cómo habilitar la integración de las aplicaciones para Wikispaces.

###Siga estos pasos para habilitar la integración de aplicaciones para Wikispaces:

1.  En el panel de navegación izquierdo del Portal de administración de Azure, haga clic en **Active Directory**.

    ![Active Directory](./media/active-directory-saas-wikispaces-tutorial/IC700993.png "Active Directory")

2.  En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3.  Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

    ![Aplicaciones](./media/active-directory-saas-wikispaces-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación](./media/active-directory-saas-wikispaces-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Agregar una aplicación de la galería](./media/active-directory-saas-wikispaces-tutorial/IC749322.png "Agregar una aplicación de la galería")

6.  En el **cuadro de búsqueda**, escriba **Wikispaces**.

    ![Galería de aplicaciones](./media/active-directory-saas-wikispaces-tutorial/IC787186.png "Galería de aplicaciones")

7.  En el panel de resultados, seleccione **Wikispaces** y luego, haga clic en **Completar** para agregar la aplicación.

    ![Wikispaces](./media/active-directory-saas-wikispaces-tutorial/IC787187.png "Wikispaces")

##Configuración del inicio de sesión único
  
El objetivo de esta sección es describir cómo se habilita la autenticación de los usuarios en Wikispaces con su cuenta de Azure AD usando el protocolo SAML basado en la federación.

###Siga estos pasos para configurar el inicio de sesión único:

1.  En el Portal de Azure AD, en la página de integración de aplicaciones de **Wikispaces**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-wikispaces-tutorial/IC787188.png "Configurar inicio de sesión único")

2.  En la página **¿Cómo desea que los usuarios inicien sesión en Wikispaces?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y, luego , haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-wikispaces-tutorial/IC787189.png "Configurar inicio de sesión único")

3.  En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto de **dirección URL de inicio de sesión de Wikispaces**, escriba su dirección URL con el siguiente patrón "**http://company.wikispaces.net*" y luego haga clic en **Siguiente**.

    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-wikispaces-tutorial/IC787190.png "Configurar dirección URL de la aplicación")

4.  En la página **Configuración de inicio de sesión único en Wikispaces**, haga clic en **Descargar metadatos** y, luego, guarde el archivo de metadatos en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-wikispaces-tutorial/IC787191.png "Configurar inicio de sesión único")

5.  Envíe el archivo de metadatos al equipo de soporte técnico de Wikispaces.

    >[AZURE.NOTE]La configuración del inicio de sesión único la debe realizar el equipo de soporte técnico de Wikispaces. Tan pronto como se complete la configuración, recibirá una notificación.

6.  En el portal de Azure AD, seleccione la confirmación de configuración de inicio de sesión único y luego haga clic en **Completa** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-wikispaces-tutorial/IC787192.png "Configurar inicio de sesión único")

##Configuración del aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD inicien sesión en Wikispaces, deben aprovisionarse en Wikispaces. En el caso de Wikispaces, el aprovisionamiento es una tarea manual.

###Para aprovisionar cuentas de usuario, realice los siguientes pasos:

1.  Inicie sesión en su sitio de la compañía de **Wikispaces** como administrador.

2.  Vaya a **Miembros**.

    ![Miembros](./media/active-directory-saas-wikispaces-tutorial/IC787193.png "Miembros")

3.  Haga clic en **Invitar asistentes**.

    ![Invitar asistentes](./media/active-directory-saas-wikispaces-tutorial/IC787194.png "Invitar asistentes")

4.  En la sección **Invitar asistentes**, siga estos pasos:

    ![Invitar asistentes](./media/active-directory-saas-wikispaces-tutorial/IC787208.png "Invitar a contactos")

    1.  Escriba los **nombres de usuario o dirección de correo electrónico** de una cuenta válida de AAD que quiera aprovisionar en los cuadros de texto relacionados.
    2.  Haga clic en **Send** (Enviar).  

        >[AZURE.NOTE]El titular de la cuenta de Azure Active Directory recibe un mensaje de correo electrónico con un vínculo para confirmar la cuenta antes de que se active.

>[AZURE.NOTE]Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Wikispaces ofrecida por Wikispaces para aprovisionar cuentas de usuario de AAD.

##Asignación de usuarios
  
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

###Para asignar usuarios a Wikispaces, lleve a cabo los siguientes pasos:

1.  En el portal de Azure AD, cree una cuenta de prueba.

2.  En la página de integración de la aplicación **Wikispaces **, haga clic en **Asignar usuarios**.

    ![Asignar usuarios](./media/active-directory-saas-wikispaces-tutorial/IC787195.png "Asignar usuarios")

3.  Seleccione su usuario de prueba, haga clic en **Asignar** y luego en **Sí** para confirmar la asignación.

    ![Sí](./media/active-directory-saas-wikispaces-tutorial/IC767830.png "Sí")
  
Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0114_2016-->