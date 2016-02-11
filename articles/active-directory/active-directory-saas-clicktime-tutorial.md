<properties 
    pageTitle="Tutorial: integración de Azure Active Directory con ClickTime | Microsoft Azure" 
    description="Aprenda a usar ClickTime con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automático, etc." 
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

#Tutorial: integración de Azure Active Directory con ClickTime

El objetivo de este tutorial es mostrar la integración de Azure y ClickTime. En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción de Azure válida
-   Un inquilino de ClickTime

Después de completar este tutorial, los usuarios de Azure AD que haya asignado a ClickTime podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de ClickTime (inicio de sesión iniciado por el proveedor de servicios) o desde [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1.  Habilitación de la integración de aplicaciones para ClickTime
2.  Configuración del inicio de sesión único
3.  Configuración del aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario](./media/active-directory-saas-clicktime-tutorial/IC777274.png "Escenario")
##Habilitación de la integración de aplicaciones para ClickTime

El objetivo de esta sección es describir cómo se habilita la integración de aplicaciones para ClickTime.

###Siga estos pasos para habilitar la integración de aplicaciones para ClickTime:

1.  En el panel de navegación izquierdo del Portal de administración de Azure, haga clic en **Active Directory**.

    ![Active Directory](./media/active-directory-saas-clicktime-tutorial/IC700993.png "Active Directory")

2.  En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3.  Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

    ![Aplicaciones](./media/active-directory-saas-clicktime-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación](./media/active-directory-saas-clicktime-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Agregar una aplicación de la galería](./media/active-directory-saas-clicktime-tutorial/IC749322.png "Agregar una aplicación de la galería")

6.  En el **cuadro de búsqueda**, escriba **ClickTime**.

    ![Galería de aplicaciones](./media/active-directory-saas-clicktime-tutorial/IC777275.png "Galería de aplicaciones")

7.  En el panel de resultados, seleccione **ClickTime** y luego haga clic en **Completar** para agregar la aplicación.

    ![ClickTime](./media/active-directory-saas-clicktime-tutorial/IC777276.png "ClickTime")
##Configuración del inicio de sesión único

El objetivo de esta sección es describir cómo permitir a los usuarios autenticarse en ClickTime con su cuenta de Azure AD mediante federación basada en el protocolo SAML. Como parte de este procedimiento, se requiere cargar un certificado codificado en base 64 en el inquilino de ClickTime. Si no está familiarizado con este procedimiento, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

>[AZURE.IMPORTANT]Para poder configurar el inicio de sesión único en el inquilino de ClickTime, deberá ponerse en contacto primero con el soporte técnico de ClickTime para habilitar esta característica.

###Siga estos pasos para configurar el inicio de sesión único:

1.  En el Portal de Azure AD, en la página de integración de aplicaciones de **ClickTime**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único **.

    ![Habilitar inicio de sesión único](./media/active-directory-saas-clicktime-tutorial/IC777277.png "Habilitar inicio de sesión único")

2.  En la página **¿Cómo desea que los usuarios inicien sesión en ClickTime?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y luego haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-clicktime-tutorial/IC777278.png "Configurar inicio de sesión único")

3.  En la página **Configuración de inicio de sesión único en ClickTime**, para descargar el certificado, haga clic en **Descargar certificado** y luego guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-clicktime-tutorial/IC777279.png "Configurar inicio de sesión único")

4.  En otra ventana del explorador web, inicie sesión en el sitio de la compañía de ClickTime como administrador.

5.  En la barra de herramientas de la parte superior, haga clic en**Preferencias** y luego haga clic en **Configuración de seguridad**.

6.  En la sección de configuración **Preferencias de inicio de sesión único**, siga estos pasos:

    ![Configuración de seguridad](./media/active-directory-saas-clicktime-tutorial/IC777280.png "Configuración de seguridad")

    1.  Seleccione **Permitir** el inicio de sesión mediante inicio de sesión único (SSO) con **OneLogin**.
    2.  En el Portal de Azure, en la página de diálogo **Configurar inicio de sesión único en ClickTime**, copie el valor de **Dirección URL del servicio de inicio de sesión único** y péguelo en el cuadro de texto **Punto de conexión de proveedor de identidades**.
    3.  Cree un archivo **codificado en base 64** a partir del certificado descargado.  

        >[AZURE.TIP]Para obtener más información, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

    4.  Abra el certificado codificado en base 64 en el **Bloc de notas**, copie el contenido y, a continuación, péguelo en el cuadro de texto **X.509 Certificate** (Certificado X.509).
    5.  Haga clic en **Guardar**.

7.  En el Portal de Azure AD, seleccione la confirmación de configuración de inicio de sesión único y, a continuación, haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-clicktime-tutorial/IC777281.png "Configurar inicio de sesión único")
##Configuración del aprovisionamiento de usuario

Para permitir que los usuarios de Azure AD inicien sesión en ClickTime, deben aprovisionarse en ClickTime. En el caso de ClickTime, el aprovisionamiento es una tarea manual.

###Para aprovisionar cuentas de usuario, realice estos pasos:

1.  Inicie sesión en su inquilino de **ClickTime**.

2.  En la barra de herramientas de la parte superior, haga clic en **Compañía** y, luego, en **Contactos**.

    ![Contactos](./media/active-directory-saas-clicktime-tutorial/IC777282.png "Contactos")

3.  Haga clic en **Agregar persona**.

    ![Agregar persona](./media/active-directory-saas-clicktime-tutorial/IC777283.png "Agregar persona")

4.  En la sección New Person (Nueva persona), lleve a cabo estos pasos:

    ![Contactos](./media/active-directory-saas-clicktime-tutorial/IC777284.png "Contactos")

    1.  En el cuadro de texto **dirección de correo electrónico**, escriba la dirección de correo electrónico de la cuenta de Azure AD.
    2.  En el cuadro de texto **nombre completo**, escriba el nombre de la cuenta de Azure AD.  

        >[AZURE.NOTE]Si lo desea, puede establecer propiedades adicionales del nuevo objeto persona.

    3.  Haga clic en **Guardar**.

>[AZURE.NOTE]Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de ClickTime ofrecida por ClickTime para aprovisionar cuentas de usuario de AAD.

##Asignación de usuarios

Para probar la configuración, tiene que conceder acceso, mediante su asignación, a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

###Para asignar usuarios a ClickTime, lleve a cabo los siguientes pasos:

1.  En el Portal de Azure AD, cree una cuenta de prueba.

2.  En la página de integración de aplicaciones de **ClickTime **, haga clic en **Asignar usuarios**.

    ![Asignar usuarios](./media/active-directory-saas-clicktime-tutorial/IC777285.png "Asignar usuarios")

3.  Seleccione su usuario de prueba, haga clic en **Asignar** y luego en **Sí** para confirmar la asignación.

    ![Sí](./media/active-directory-saas-clicktime-tutorial/IC767830.png "Sí")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0121_2016-->