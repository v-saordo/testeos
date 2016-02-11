<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con Bonus.ly | Microsoft Azure" 
    description="Aprenda cómo usar Bonus.ly con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc." 
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

#Tutorial: Integración de Azure Active Directory con Bonus.ly

El objetivo de este tutorial es mostrar la integración de Azure y Bonus.ly. En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción de Azure válida
-   Un inquilino de prueba en Bonus.ly

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1.  Habilitación de la integración de aplicaciones para Bonus.ly
2.  Configuración del inicio de sesión único
3.  Configuración del aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario](./media/active-directory-saas-bonus-tutorial/IC773679.png "Escenario")
##Habilitación de la integración de aplicaciones para Bonus.ly

El objetivo de esta sección es describir cómo se habilita la integración de las aplicaciones para Bonus.ly.

###Siga estos pasos para habilitar la integración de aplicaciones para Bonus.ly:

1.  En el panel de navegación izquierdo del Portal de administración de Azure, haga clic en **Active Directory**.

    ![Habilitar el inicio de sesión único](./media/active-directory-saas-bonus-tutorial/IC773680.png "Habilitar el inicio de sesión único")

2.  En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3.  Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

    ![Aplicaciones](./media/active-directory-saas-bonus-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación](./media/active-directory-saas-bonus-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Agregar una aplicación de la galería](./media/active-directory-saas-bonus-tutorial/IC749322.png "Agregar una aplicación de la galería")

6.  En el **cuadro de búsqueda**, escriba **Bonus.ly**.

    ![Galería de aplicaciones](./media/active-directory-saas-bonus-tutorial/IC773681.png "Galería de aplicaciones")

7.  En el panel de resultados, seleccione **Bonus.ly** y luego haga clic en **Completar** para agregar la aplicación.

    ![Bonusly](./media/active-directory-saas-bonus-tutorial/IC773682.png "Bonusly")
##Configuración del inicio de sesión único

El objetivo de esta sección es describir cómo habilitar usuarios para que se autentiquen en Bonus.ly con su cuenta de Azure AD mediante la federación basada en el protocolo SAML. La configuración de un inicio de sesión único para Bonus.ly requiere la recuperación de un valor de huella digital de un certificado. Si no está familiarizado con este procedimiento, consulte [Recuperación del valor de huella digital de un certificado](http://youtu.be/YKQF266SAxI).

###Siga estos pasos para configurar el inicio de sesión único:

1.  En el Portal de Azure AD, en la página de integración de aplicaciones de **Bonus.ly**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-bonus-tutorial/IC749323.png "Configurar inicio de sesión único")

2.  En la página **¿Cómo desea que los usuarios inicien sesión en Bonus.ly?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y luego haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-bonus-tutorial/IC773683.png "Configurar inicio de sesión único")

3.  En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto de **Dirección URL de inquilino de Bonus.ly**, escriba su dirección URL con el siguiente patrón "*https://\<nombreDeInquilino>.Bonus.ly*" y luego haga clic en **Siguiente**.

    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-bonus-tutorial/IC773684.png "Configurar dirección URL de la aplicación")

4.  En la página **Configuración de inicio de sesión único en Bonus.ly**, haga clic en **Descargar certificado** y luego guarde el archivo de certificado en el equipo como **c:\\Bonusly.cer**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-bonus-tutorial/IC773685.png "Configurar inicio de sesión único")

5.  En una ventana de explorador web diferente, inicie sesión en su inquilino de **Bonus.ly**.

6.  En la barra de herramientas de la parte superior, haga clic en **Configuración** y seleccione **Integraciones y aplicaciones**.

    ![Bonusly](./media/active-directory-saas-bonus-tutorial/IC773686.png "Bonusly")

7.  En **Inicio de sesión único** seleccione **SAML**.

8.  En la página de diálogo **SAML**, realice los pasos siguientes:

    ![Bonusly](./media/active-directory-saas-bonus-tutorial/IC773687.png "Bonusly")

    1.  En el Portal de Azure, en la página de diálogo **Configurar inicio de sesión único en Bonus.ly**, copie el valor de **Dirección URL de inicio de sesión remoto** y péguelo en el cuadro de texto **URL de destino de SSO de IdP**.
    2.  En el Portal de Azure, en la página de diálogo **Configurar inicio de sesión único en Bonus.ly**, copie el valor de **Id. del emisor** y péguelo en el cuadro de texto **Emisor de IdP**.
    3.  En el Portal de Azure, en la página de diálogo **Configurar inicio de sesión único en Bonus.ly**, copie el valor de **URL de inicio de sesión remoto** y péguelo en el cuadro de texto **URL de inicio de sesión de IdP**.
    4.  Copie el valor de **Huella digital** del certificado exportado y luego péguelo en el cuadro de texto **Huella digital del certificado**.

        >[AZURE.TIP]Para obtener más información, consulte [Recuperación del valor de huella digital de un certificado](http://youtu.be/YKQF266SAxI).

9.  Haga clic en **guardar**.

10. En el Portal de Microsoft Azure AD, seleccione la confirmación de configuración y, luego, haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-bonus-tutorial/IC773689.png "Configurar inicio de sesión único")
##Configuración del aprovisionamiento de usuario

Para permitir que los usuarios de Azure AD inicien sesión en Bonus.ly, tienen que aprovisionarse en Bonus.ly. En el caso de Bonus.ly, el aprovisionamiento es una tarea manual.

###Siga estos pasos para configurar el aprovisionamiento de usuario:

1.  En una ventana de explorador web diferente, inicie sesión en su inquilino de Bonus.ly.

2.  Hacer clic en **Configuración**.

    ![Settings](./media/active-directory-saas-bonus-tutorial/IC781041.png "Settings")

3.  Haga clic en la pestaña **Usuarios y bonificaciones**.

    ![Usuarios y bonificaciones](./media/active-directory-saas-bonus-tutorial/IC781042.png "Usuarios y bonificaciones")

4.  Haga clic en **Administrar usuarios**.

    ![Administrar usuarios](./media/active-directory-saas-bonus-tutorial/IC781043.png "Administrar usuarios")

5.  Haga clic en **Agregar usuario**.

    ![Agregar usuario](./media/active-directory-saas-bonus-tutorial/IC781044.png "Agregar usuario")

6.  En el cuadro de diálogo **Agregar usuario**, realice los pasos siguientes:

    ![Agregar usuario](./media/active-directory-saas-bonus-tutorial/IC781045.png "Agregar usuario")

    1.  Escriba los datos de una cuenta de AAD válida que desee aprovisionar en los cuadros de texto correspondientes: “**Correo electrónico**, **Nombre**, **Apellido**”.
    2.  Haga clic en **Guardar**.

    >[AZURE.NOTE]El titular de la cuenta de AAD recibirá un mensaje de correo electrónico con un vínculo para confirmar la cuenta antes de que se active.

>[AZURE.NOTE]Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Bonus.ly que proporcione Bonus.ly para aprovisionar cuentas de usuario de AAD.

##Asignación de usuarios

Para probar la configuración, debe asignar los usuarios de Azure AD que quiera que usen su aplicación para concederles acceso a ella.

###Para asignar usuarios a Bonus.ly, lleve a cabo los siguientes pasos:

1.  En el Portal de Azure AD, cree una cuenta de prueba.

2.  En la página de integración de aplicaciones de Bonus.ly, haga clic en **Asignar usuarios**.

    ![Asignar usuarios](./media/active-directory-saas-bonus-tutorial/IC773690.png "Asignar usuarios")

3.  Seleccione su usuario de prueba, haga clic en **Asignar** y luego en **Sí** para confirmar la asignación.

    ![Sí](./media/active-directory-saas-bonus-tutorial/IC767830.png "Sí")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0121_2016-->