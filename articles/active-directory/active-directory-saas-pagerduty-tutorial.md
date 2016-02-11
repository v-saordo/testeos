<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con Pagerduty | Microsoft Azure" 
    description="Aprenda cómo usar Pagerduty con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc." 
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

#Tutorial: Integración de Azure Active Directory con Pagerduty
  
El objetivo de este tutorial es mostrar la integración de Azure y Pagerduty. En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción de Azure válida
-   Un inquilino de Pagerduty
  
Después de completar este tutorial, los usuarios de Azure AD asignados a Pagerduty podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía Pagerduty (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
La situación descrita en este tutorial consta de los siguientes bloques de creación:

1.  Habilitación de la integración de aplicaciones para Pagerduty
2.  Configuración del inicio de sesión único
3.  Configuración del aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario](./media/active-directory-saas-pagerduty-tutorial/IC778528.png "Escenario")
##Habilitación de la integración de aplicaciones para Pagerduty
  
El objetivo de esta sección es describir cómo habilitar la integración de las aplicaciones para Pagerduty.

###Siga estos pasos para habilitar la integración de aplicaciones en Pagerduty:

1.  En el panel de navegación izquierdo del Portal de administración de Azure, haga clic en **Active Directory**.

    ![Active Directory](./media/active-directory-saas-pagerduty-tutorial/IC700993.png "Active Directory")

2.  En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3.  Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

    ![Aplicaciones](./media/active-directory-saas-pagerduty-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación](./media/active-directory-saas-pagerduty-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Agregar una aplicación de la galería](./media/active-directory-saas-pagerduty-tutorial/IC749322.png "Agregar una aplicación de la galería")

6.  En el **cuadro de búsqueda**, escriba **Pagerduty**.

    ![Galería de aplicaciones](./media/active-directory-saas-pagerduty-tutorial/IC778529.png "Galería de aplicaciones")

7.  En el panel de resultados, seleccione **Pagerduty** y luego haga clic en **Completar** para agregar la aplicación.

    ![PagerDuty](./media/active-directory-saas-pagerduty-tutorial/IC778530.png "PagerDuty")
##Configuración del inicio de sesión único
  
El objetivo de esta sección es describir cómo se habilita la autenticación de los usuarios en Pagerduty con su cuenta de Azure AD usando el protocolo SAML basado en la federación. Como parte de este procedimiento, es necesario crear un archivo de certificado codificado en base 64. Si no está familiarizado con este procedimiento, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

###Siga estos pasos para configurar el inicio de sesión único:

1.  En el Portal de Azure AD, en la página de integración de aplicaciones de **Pagerduty**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-pagerduty-tutorial/IC778531.png "Configurar inicio de sesión único")

2.  En la página **¿Cómo desea que los usuarios inicien sesión en Pagerduty?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y luego haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-pagerduty-tutorial/IC778532.png "Configurar inicio de sesión único")

3.  En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto de **URL de inicio de sesión de Pagerduty**, escriba su dirección URL con el siguiente patrón "*https://\<nombreDeInquilino>.Pagerduty.com*" y luego haga clic en **Siguiente**.

    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-pagerduty-tutorial/IC778533.png "Configurar dirección URL de la aplicación")

4.  En la página **Configuración de inicio de sesión único en Pagerduty**, haga clic en **Descargar certificado** y luego guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-pagerduty-tutorial/IC778534.png "Configurar inicio de sesión único")

5.  En otra ventana del explorador web, inicie sesión en el sitio de la compañía Pagerduty como administrador.

6.  En el menú de la parte superior, haga clic en **Configuración de cuenta**.

    ![Configuración de cuenta](./media/active-directory-saas-pagerduty-tutorial/IC778535.png "Configuración de cuenta")

7.  Haga clic en **Inicio de sesión único**.

    ![Inicio de sesión único](./media/active-directory-saas-pagerduty-tutorial/IC778536.png "Inicio de sesión único")

8.  En la página Habilitar inicio de sesión único (SSO), siga estos pasos:

    ![Habilitar el inicio de sesión único](./media/active-directory-saas-pagerduty-tutorial/IC778537.png "Habilitar inicio de sesión único")

    1.  Cree un archivo **codificado en base 64** a partir del certificado descargado.  

        >[AZURE.TIP]Para obtener más información, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

    2.  Abra el certificado codificado en base 64 en el Bloc de notas, copie el contenido del mismo en el Portapapeles y luego péguelo en el cuadro de texto **Certificado X.509**.
    3.  En el Portal de Azure, en la página de diálogo **Configurar inicio de sesión único en Pagerduty**, copie el valor de **Dirección URL del inicio de sesión remoto** y péguelo en el cuadro de texto **Login URL (dirección URL de inicio de sesión)**.
    4.  En el Portal de Azure, en la página de diálogo **Configurar inicio de sesión único en Pagerduty**, copie el valor de **Dirección URL del cierre de sesión remoto** y péguelo en el cuadro de texto **Dirección URL de cierre de sesión**.
    5.  Seleccione **Activar inicio de sesión único**.
    6.  Haga clic en **Guardar cambios**.

9.  En el portal de Azure AD, seleccione la confirmación de configuración de inicio de sesión único y luego haga clic en **Completa** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-pagerduty-tutorial/IC778538.png "Configurar inicio de sesión único")
##Configuración del aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD inicien sesión en Pagerduty, deben aprovisionarse en Pagerduty. En el caso de Pagerduty, el aprovisionamiento es una tarea manual.

###Para aprovisionar cuentas de usuario, realice los siguientes pasos:

1.  Inicie sesión en su inquilino de **Pagerduty**.

2.  En el menú de la parte superior, haga clic en **Usuarios**.

3.  Haga clic en **Agregar usuarios**.

    ![Agregar usuarios](./media/active-directory-saas-pagerduty-tutorial/IC778539.png "Agregar usuarios")

4.  En el cuadro de diálogo **Invitar a su equipo**, escriba el **Nombre y apellido** y la dirección de **Correo electrónico** del usuario de Azure AD que quiera aprovisionar, haga clic en**Agregar**y luego haga clic en **Enviar invitaciones**.

    ![Invitar a su equipo](./media/active-directory-saas-pagerduty-tutorial/IC778540.png "Invitar a su equipo")

    >[AZURE.NOTE]Todos los usuarios agregados recibirán una invitación para crear una cuenta de PagerDuty.

>[AZURE.NOTE]Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Pagerduty ofrecida por Pagerduty para aprovisionar cuentas de usuario de AAD.

##Asignación de usuarios
  
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

###Para asignar usuarios a Pagerduty, lleve a cabo los siguientes pasos:

1.  En el portal de Azure AD, cree una cuenta de prueba.

2.  En la página de integración de la aplicación **Pagerduty **, haga clic en **Asignar usuarios**.

    ![Asignar usuarios](./media/active-directory-saas-pagerduty-tutorial/IC778541.png "Asignar usuarios")

3.  Seleccione su usuario de prueba, haga clic en **Asignar** y luego en **Sí** para confirmar la asignación.

    ![Sí](./media/active-directory-saas-pagerduty-tutorial/IC767830.png "Sí")
  
Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0121_2016-->