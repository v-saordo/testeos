<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con Zendesk | Microsoft Azure" 
    description="Aprenda cómo usar Zendesk con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc." 
    services="active-directory" 
    authors="markusvi"  
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

#Tutorial: Integración de Azure Active Directory con Zendesk
  
El objetivo de este tutorial es mostrar la integración de Azure y Zendesk. En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción de Azure válida
-   Un inquilino de Zendesk
  
Después de completar este tutorial, los usuarios de Azure AD que ha asignado a Zendesk podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de Zendesk (inicio de sesión iniciado por el proveedor del servicio) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
La situación descrita en este tutorial consta de los siguientes bloques de creación:

1.  Habilitación de la integración de aplicaciones para Zendesk
2.  Configuración del inicio de sesión único
3.  Configuración del aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario](./media/active-directory-saas-zendesk-tutorial/IC773083.png "Escenario")

##Habilitación de la integración de aplicaciones para Zendesk
  
El objetivo de esta sección es describir cómo habilitar la integración de las aplicaciones para Zendesk.

###Siga estos pasos para habilitar la integración de aplicaciones para Zendesk:

1.  En el panel de navegación izquierdo del Portal de administración de Azure, haga clic en **Active Directory**.

    ![Active Directory](./media/active-directory-saas-zendesk-tutorial/IC700993.png "Active Directory")

2.  En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3.  Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

    ![Aplicaciones](./media/active-directory-saas-zendesk-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación](./media/active-directory-saas-zendesk-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Agregar una aplicación de la galería](./media/active-directory-saas-zendesk-tutorial/IC749322.png "Agregar una aplicación de la galería")

6.  En el **cuadro de búsqueda**, escriba **Zendesk**.

    ![Galería de aplicaciones](./media/active-directory-saas-zendesk-tutorial/IC773084.png "Galería de aplicaciones")

7.  En el panel de resultados, seleccione **Zendesk** y, luego, haga clic en **Completa** para agregar la aplicación.

    ![Zendesk](./media/active-directory-saas-zendesk-tutorial/IC773085.png "Zendesk")

##Configuración del inicio de sesión único
  
El objetivo de esta sección es describir cómo se habilita la autenticación de los usuarios en Zendesk con su cuenta de Azure AD usando el protocolo SAML basado en la federación. La configuración del inicio de sesión único para Zendesk requiere que recupere un valor de huella digital de un certificado. Si no está familiarizado con este procedimiento, consulte [Recuperación del valor de huella digital de un certificado](http://youtu.be/YKQF266SAxI).

###Siga estos pasos para configurar el inicio de sesión único:

1.  En el portal de Azure AD, en la página de integración de aplicaciones de **Zendesk**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Inicio de sesión único](./media/active-directory-saas-zendesk-tutorial/IC773086.png "Inicio de sesión único")

2.  En la página **¿Cómo desea que los usuarios inicien sesión en Zendesk?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y, luego , haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-zendesk-tutorial/IC773087.png "Configurar inicio de sesión único")

3.  En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto de **dirección URL de inicio de sesión de Zendesk**, escriba su dirección URL con el siguiente patrón "*https://\<nombre-inquilino>.zendesk.com*"y luego haga clic en **Siguiente**.

    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-zendesk-tutorial/IC773088.png "Configurar dirección URL de la aplicación")

4.  En la página **Configuración de inicio de sesión único en ZScaler**, para descargar el certificado, haga clic en **Descargar certificado** y luego guarde el archivo de certificado localmente como **c:\\zendesk.cer**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-zendesk-tutorial/IC777534.png "Configurar inicio de sesión único")

5.  En otra ventana del explorador web, inicie sesión en su sitio de la compañía de Zendesk como administrador.

6.  Haga clic en **Administrador**.

7.  En el panel de navegación izquierdo, haga clic en **Configuración** y luego en **Seguridad**.

    ![Seguridad](./media/active-directory-saas-zendesk-tutorial/IC773089.png "Seguridad")

8.  En la página **Seguridad**, seleccione la pestaña **Administrador y agentes**.

9.  Seleccione **Inicio de sesión único (SSO) y SAML** y luego seleccione **SAML**.

10. En el portal de Azure AD, en la página **Configurar inicio de sesión único en Zendesk**, copie el valor de **Dirección URL de inicio de sesión único de SAML** y péguelo en el cuadro de texto **Dirección URL de inicio de sesión único de SAML**.

11. En el portal de Azure AD, en la página **Configurar inicio de sesión único en Zendesk**, copie el valor de **Dirección URL de cierre de sesión remoto** y péguelo en el cuadro de texto **Dirección URL de cierre de sesión remoto**.

    ![Inicio de sesión único](./media/active-directory-saas-zendesk-tutorial/IC773090.png "Inicio de sesión único")

12. Copie el valor de **Huella digital** del certificado exportado y péguelo en el cuadro de texto **Huella digital del certificado**.

	>[AZURE.TIP]Para obtener más información, consulte [Recuperación del valor de huella digital de un certificado](http://youtu.be/YKQF266SAxI).

13. Haga clic en **Guardar**.

14. En el Portal de Azure AD, seleccione la confirmación de configuración de inicio de sesión único y, a continuación, haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-zendesk-tutorial/IC773093.png "Configurar inicio de sesión único")

##Configuración del aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD inicien sesión en **Zendesk**, deben aprovisionarse a **Zendesk**. En el caso de **Zendesk**, el aprovisionamiento es una tarea manual.

###Para aprovisionar cuentas de usuario a Zendesk, realice los siguientes pasos:

1.  Inicie sesión en su inquilino de **Zendesk**.

2.  Seleccione la pestaña **Lista de clientes**.

3.  Seleccione la pestaña **Usuario**ficha y luego haga clic en**Agregar**.

    ![Agregar usuario](./media/active-directory-saas-zendesk-tutorial/IC773632.png "Agregar usuario")

4.  Escriba la dirección de correo electrónico de una cuenta de Azure AD existente que quiera aprovisionar y luego haga clic en **Guardar**.

    ![Nuevo usuario](./media/active-directory-saas-zendesk-tutorial/IC773633.png "Nuevo usuario")

>[AZURE.NOTE]Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Zendesk ofrecida por Zendesk para aprovisionar cuentas de usuario de AAD.

##Asignación de usuarios
  
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

###Para asignar usuarios a Zendesk, lleve a cabo los siguientes pasos:

1.  En el portal de Azure AD, cree una cuenta de prueba.

2.  En la página de integración de la aplicación **Zendesk **, haga clic en **Asignar usuarios**.

    ![Asignar usuarios](./media/active-directory-saas-zendesk-tutorial/IC773094.png "Asignar usuarios")

3.  Seleccione su usuario de prueba, haga clic en **Asignar** y luego en **Sí** para confirmar la asignación.

    ![Sí](./media/active-directory-saas-zendesk-tutorial/IC767830.png "Sí")
  
Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0114_2016-->