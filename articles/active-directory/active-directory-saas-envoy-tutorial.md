<properties 
    pageTitle="Tutorial: integración de Azure Active Directory con Envoy | Microsoft Azure" 
    description="Aprenda a usar Envoy con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automático, etc." 
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

#Tutorial: integración de Azure Active Directory con Envoy
  
El objetivo de este tutorial es mostrar la integración de Azure y Envoy. En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción de Azure válida
-   Un inquilino de Envoy
  
Después de completar este tutorial, los usuarios de Azure AD que haya asignado a Envoy podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de Envoy (inicio de sesión iniciado por el proveedor de servicios) o desde [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
La situación descrita en este tutorial consta de los siguientes bloques de creación:

1.  Habilitación de la integración de aplicaciones para Envoy
2.  Configuración del inicio de sesión único
3.  Configuración del aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario](./media/active-directory-saas-envoy-tutorial/IC776759.png "Escenario")
##Habilitación de la integración de aplicaciones para Envoy
  
El objetivo de esta sección es describir cómo se habilita la integración de aplicaciones para Envoy.

###Siga estos pasos para habilitar la integración de aplicaciones para Envoy:

1.  En el panel de navegación izquierdo del Portal de administración de Azure, haga clic en **Active Directory**.

    ![Active Directory](./media/active-directory-saas-envoy-tutorial/IC700993.png "Active Directory")

2.  En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3.  Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

    ![Aplicaciones](./media/active-directory-saas-envoy-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación](./media/active-directory-saas-envoy-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Agregar una aplicación de la galería](./media/active-directory-saas-envoy-tutorial/IC749322.png "Agregar una aplicación de la galería")

6.  En el **cuadro de búsqueda**, escriba **Envoy**.

    ![Galería de aplicaciones](./media/active-directory-saas-envoy-tutorial/IC776760.png "Galería de aplicaciones")

7.  En el panel de resultados, seleccione **Envoy** y luego haga clic en **Completar** para agregar la aplicación.

    ![Envoy](./media/active-directory-saas-envoy-tutorial/IC776777.png "Envoy")
##Configuración del inicio de sesión único
  
El objetivo de esta sección es describir cómo permitir a los usuarios autenticarse en Envoy con su cuenta de Azure AD mediante federación basada en el protocolo SAML. La configuración de un inicio de sesión único para Envoy requiere la recuperación de un valor de huella digital de un certificado. Si no está familiarizado con este procedimiento, consulte [Recuperación del valor de huella digital de un certificado](http://youtu.be/YKQF266SAxI).

###Siga estos pasos para configurar el inicio de sesión único:

1.  En el Portal de Azure AD, en la página de integración de aplicaciones de **Envoy**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Habilitar inicio de sesión único](./media/active-directory-saas-envoy-tutorial/IC776778.png "Habilitar inicio de sesión único")

2.  En la página **¿Cómo desea que los usuarios inicien sesión en Envoy?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y luego haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-envoy-tutorial/IC776779.png "Configurar inicio de sesión único")

3.  En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto **URL de inicio de sesión de Envoy**, escriba su dirección URL con el siguiente patrón "*https://\<nombreDeInquilino>.Envoy.com*" y luego haga clic en **Siguiente**.

    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-envoy-tutorial/IC776780.png "Configurar dirección URL de la aplicación")

4.  En la página **Configurar inicio de sesión único en Envoy**, para descargar el certificado, haga clic en **Descargar certificado** y, a continuación, guarde el archivo de certificado localmente como **c:\\Envoy.cer**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-envoy-tutorial/IC776781.png "Configurar inicio de sesión único")

5.  En otra ventana del explorador web, inicie sesión en el sitio de la compañía de Envoy como administrador.

6.  En la barra de herramientas de la parte superior, haga clic en el icono de **Configuración**.

    ![Envoy](./media/active-directory-saas-envoy-tutorial/IC776782.png "Envoy")

7.  Haga clic en **Compañía**.

    ![Compañía](./media/active-directory-saas-envoy-tutorial/IC776783.png "Compañía")

8.  Haga clic en **SAML**.

    ![SAML](./media/active-directory-saas-envoy-tutorial/IC776784.png "SAML")

9.  En la sección de configuración de **SAML Authentication** (Autenticación de SAML), realice los pasos siguientes:

    ![Autenticación SAML](./media/active-directory-saas-envoy-tutorial/IC776785.png "Autenticación SAML")

    >[AZURE.NOTE]La aplicación genera automáticamente el valor del identificador de ubicación de la sede central.

    1.  Copie el valor de **Huella digital** del certificado exportado y luego péguelo en el cuadro de texto **Huella digital**.  

        >[AZURE.TIP]Para obtener más información, consulte [Recuperación del valor de huella digital de un certificado](http://youtu.be/YKQF266SAxI).

    2.  En el Portal de Azure, en la página de diálogo **Configurar inicio de sesión único en Envoy**, copie el valor de **Dirección URL de SSO de SAML** y péguelo en el cuadro de texto **Dirección URL de SAML para HTTP del proveedor de identidades**.
    3.  Haga clic en **Guardar cambios**.

10. En el Portal de Azure AD, seleccione la confirmación de configuración de inicio de sesión único y, a continuación, haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-envoy-tutorial/IC776786.png "Configurar inicio de sesión único")
##Configuración del aprovisionamiento de usuario
  
No hay elemento de acción para que configure el aprovisionamiento de usuarios en Envoy. Cuando un usuario asignado intenta iniciar sesión en Envoy desde el panel de acceso, Envoy comprueba si el usuario existe. Si no hay cuentas de usuario disponibles, Envoy crea una automáticamente.
##Asignación de usuarios
  
Para probar la configuración, tiene que conceder acceso, mediante su asignación, a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

###Para asignar usuarios a Envoy, lleve a cabo los siguientes pasos:

1.  En el Portal de Azure AD, cree una cuenta de prueba.

2.  En la página de integración de aplicaciones de **Envoy **, haga clic en **Asignar usuarios**.

    ![Asignar usuarios](./media/active-directory-saas-envoy-tutorial/IC776787.png "Asignar usuarios")

3.  Seleccione su usuario de prueba, haga clic en **Asignar** y luego en **Sí** para confirmar la asignación.

    ![Sí](./media/active-directory-saas-envoy-tutorial/IC767830.png "Sí")
  
Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0121_2016-->