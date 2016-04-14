<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con Zscaler | Microsoft Azure" 
    description="Aprenda cómo usar Zscaler con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc." 
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
    ms.date="02/29/2016" 
    ms.author="jeedes" />

#Tutorial: Integración de Azure Active Directory con Zscaler
  
El objetivo de este tutorial es mostrar la integración de Azure y Zscaler. En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción de Azure válida
-   Un inquilino en Zscaler
  
La situación descrita en este tutorial consta de los siguientes bloques de creación:

1.  Habilitación de la integración de aplicaciones para Zscaler
2.  Configuración del inicio de sesión único
3.  Configuración de los valores de proxy
4.  Configuración del aprovisionamiento de usuario
5.  Asignación de usuarios

![Escenario](./media/active-directory-saas-zscaler-tutorial/IC769226.png "Escenario")

##Habilitación de la integración de aplicaciones para Zscaler
  
El objetivo de esta sección es describir cómo habilitar la integración de las aplicaciones para Zscaler.

###Siga estos pasos para habilitar la integración de aplicaciones para Zscaler:

1.  En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.

    ![Active Directory](./media/active-directory-saas-zscaler-tutorial/IC700993.png "Active Directory")

2.  En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3.  Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

    ![Aplicaciones](./media/active-directory-saas-zscaler-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación](./media/active-directory-saas-zscaler-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Agregar una aplicación de la galería](./media/active-directory-saas-zscaler-tutorial/IC749322.png "Agregar una aplicación de la galería")

6.  En el **cuadro de búsqueda**, escriba **Zscaler**.

    ![Galería de aplicaciones](./media/active-directory-saas-zscaler-tutorial/IC769227.png "Galería de aplicaciones")

7.  En el panel de resultados, seleccione **Zscaler** y, luego, haga clic en **Completa** para agregar la aplicación.

    ![Zscaler](./media/active-directory-saas-zscaler-tutorial/IC769228.png "Zscaler")

##Configuración del inicio de sesión único
  
El objetivo de esta sección es describir cómo se habilita la autenticación de los usuarios en Zscaler con su cuenta de Azure AD usando el protocolo SAML basado en la federación. Como parte de este procedimiento, es necesario cargar un certificado en Zscaler.

###Siga estos pasos para configurar el inicio de sesión único:

1.  En el Portal de Azure clásico, en la página de integración de la aplicación **Zscaler**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Habilitar el inicio de sesión único](./media/active-directory-saas-zscaler-tutorial/IC769229.png "Habilitar el inicio de sesión único")

2.  En la página **¿Cómo desea que los usuarios inicien sesión en Zscaler?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y, luego , haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-zscaler-tutorial/IC769230.png "Configurar inicio de sesión único")

3.  En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto de **dirección URL de inicio de sesión de Zscaler**, escriba su dirección URL de inicio de sesión que obtuvo de Zscaler y luego haga clic en **Siguiente**:

    >[AZURE.NOTE] Póngase en contacto con el equipo de soporte técnico de Zscaler si no sabe qué dirección URL de inicio de sesión es.

    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-zscaler-tutorial/IC769231.png "Configurar dirección URL de la aplicación")

4.  En la página **Configurar inicio de sesión único en Zscaler**, siga estos pasos:

    ![Configurar inicio de sesión único](./media/active-directory-saas-zscaler-tutorial/IC769232.png "Configurar inicio de sesión único")

    1.  Haga clic en **Descargar certificado** y luego guarde el certificado como **c:\\Zscaler.cer**.
    2.  Copia la **Dirección URL de solicitud de autenticación**en el portapapeles.

5.  Inicie sesión en su inquilino de Zscaler.

6.  En el menú de la parte superior, haga clic en **Administración**.

    ![Administración](./media/active-directory-saas-zscaler-tutorial/IC769486.png "Administración")

7.  En **Administrar administradores y roles** haga clic en **Administrar usuarios y autenticación**.

    ![Administrar administradores y roles](./media/active-directory-saas-zscaler-tutorial/IC769487.png "Administrar administradores y roles")

8.  En la sección **Elegir opción de autenticación para su organización**, lleve a cabo los pasos siguientes:

    ![Elegir opciones de autenticación](./media/active-directory-saas-zscaler-tutorial/IC769488.png "Elegir opciones de autenticación")

    1.  Seleccione **Autenticarse mediante el inicio de sesión único SAML**.
    2.  Haga clic en **Configurar parámetros de inicio de sesión único SAML**.

9.  En la página de diálogo **Configure SAML Single Sign-On Parameters** (Configurar parámetros de inicio de sesión único SAML), lleve a cabo estos pasos y luego haga clic en el botón **Done**.

    ![Carga del certificado](./media/active-directory-saas-zscaler-tutorial/IC769489.png "Carga del certificado")

    1.  En el cuadro de texto **Dirección URL del portal de SAML al que se envían los usuarios para autenticación**, pegue el valor del campo **Dirección URL de la solicitud de autenticación** desde el Portal de Azure clásico.
    2.  En el cuadro de texto **Atributo que contiene el nombre de inicio de sesión**, escriba **NameID**.
    3.  En el campo **Cargar certificado público de SSL**, cargue el certificado que ha descargado desde el Portal de Azure clásico.
    4.  Seleccione **Habilitar aprovisionamiento automático de SAML**.

10. En la página del cuadro de diálogo **Configurar autenticación de usuario**, realice los pasos siguientes:

    ![Configurar autenticación de usuario](./media/active-directory-saas-zscaler-tutorial/IC769490.png "Configurar autenticación de usuario")

    1.  Haga clic en **Guardar**.
    2.  Haga clic en **Activar ahora**.

11. En el Portal de Azure clásico, seleccione la confirmación de configuración de inicio de sesión único y, luego, haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-zscaler-tutorial/IC769491.png "Configurar inicio de sesión único")

##Configuración de los valores de proxy

###Para definir la configuración de proxy en Internet Explorer

1.  Inicie **Internet Explorer**.

2.  Seleccione **Opciones de Internet** en el menú **Herramientas** para abrir el cuadro de diálogo **Opciones de Internet**.

    ![Opciones de Internet](./media/active-directory-saas-zscaler-tutorial/IC769492.png "Opciones de Internet")

3.  Haga clic en la pestaña **Conexiones**.

    ![Conexiones](./media/active-directory-saas-zscaler-tutorial/IC769493.png "Conexiones")

4.  Haga clic en **Configuración de LAN** para abrir el cuadro de diálogo **Configuración de LAN**.

5.  En la sección del servidor proxy, lleve a cabo estos pasos:

    ![Servidor proxy](./media/active-directory-saas-zscaler-tutorial/IC769494.png "Servidor proxy")

    1.  Seleccione Usar un servidor proxy para la LAN.
    2.  En el cuadro de texto Dirección, escriba **gateway.zscalertwo.net**.
    3.  En el cuadro de texto Puerto, escriba **80**.
    4.  Seleccione **No usar servidor proxy para direcciones locales**.
    5.  Haga clic en **Aceptar** para cerrar el cuadro de diálogo **Configuración de red de área local (LAN)**.

6.  Haga clic en **Aceptar** para cerrar el cuadro de diálogo **Opciones de Internet**.

##Configuración del aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD inicien sesión en Zscaler, deben aprovisionarse a Zscaler. En el caso de Zscaler, el aprovisionamiento es una tarea manual.

###Siga estos pasos para configurar el aprovisionamiento de usuario:

1.  Inicie sesión en su inquilino de **Zscaler**.

2.  Haga clic en **Administración**.

    ![Administración](./media/active-directory-saas-zscaler-tutorial/IC781035.png "Administración")

3.  Haga clic en **User Management** (Administración de usuarios).

    ![Administración de usuarios](./media/active-directory-saas-zscaler-tutorial/IC781036.png "Administración de usuarios")

4.  En la pestaña **Users** (Usuarios) haga clic en **Add** (Agregar).

    ![Agregar](./media/active-directory-saas-zscaler-tutorial/IC781037.png "Agregar")

5.  En la sección Agregar usuario, lleve a cabo estos pasos:

    ![Agregar usuario](./media/active-directory-saas-zscaler-tutorial/IC781038.png "Agregar usuario")

    1.  Escriba el **Id. de usuario**, el **Nombre para mostrar del usuario**, la **Contraseña**, **Confirmar contraseña** y luego seleccione **Grupos** y el **Departamento** de una cuenta de AAD válida que quiera aprovisionar.
    2.  Haga clic en **Guardar**.

>[AZURE.NOTE] Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Zscaler ofrecida por Zscaler para aprovisionar cuentas de usuario de AAD.

##Asignación de usuarios
  
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

###Para asignar usuarios a Zscaler, lleve a cabo los siguientes pasos:

1.  En el Portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de la aplicación **Zscaler**, haga clic en **Asignar usuarios**.

    ![Asignar usuarios](./media/active-directory-saas-zscaler-tutorial/IC769495.png "Asignar usuarios")

3.  Seleccione su usuario de prueba, haga clic en **Asignar** y luego en **Sí** para confirmar la asignación.

    ![Sí](./media/active-directory-saas-zscaler-tutorial/IC767830.png "Sí")
  
Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0302_2016-->