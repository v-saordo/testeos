<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con Tinfoil Security | Microsoft Azure"
    description="Aprenda cómo usar Tinfoil Security con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc." 
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

#Tutorial: Integración de Azure Active Directory con Tinfoil Security
  
El objetivo de este tutorial es mostrar la integración de Azure y Tinfoil Security. En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción de Azure válida
-   Una suscripción habilitada para el inicio de sesión único en Tinfoil Security
  
Después de completar este tutorial, los usuarios de Azure AD que ha asignado a Tinfoil Security podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de Tinfoil Security (inicio de sesión iniciado por el proveedor de identidades) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
La situación descrita en este tutorial consta de los siguientes bloques de creación:

1.  Habilitación de la integración de aplicaciones para Tinfoil Security
2.  Configuración del inicio de sesión único
3.  Configuración del aprovisionamiento de usuario
4.  Asignación de usuarios

![Configurar inicio de sesión único](./media/active-directory-saas-tinfoil-security-tutorial/IC798965.png "Configurar inicio de sesión único")

##Habilitación de la integración de aplicaciones para Tinfoil Security
  
El objetivo de esta sección es describir cómo se habilita la integración de aplicaciones para Tinfoil Security.

###Siga estos pasos para habilitar la integración de aplicaciones para Tinfoil Security:

1.  En el panel de navegación izquierdo del Portal de administración de Azure, haga clic en **Active Directory**.

    ![Active Directory](./media/active-directory-saas-tinfoil-security-tutorial/IC700993.png "Active Directory")

2.  En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3.  Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

    ![Aplicaciones](./media/active-directory-saas-tinfoil-security-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación](./media/active-directory-saas-tinfoil-security-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Agregar una aplicación de la galería](./media/active-directory-saas-tinfoil-security-tutorial/IC749322.png "Agregar una aplicación de la galería")

6.  En el **cuadro de búsqueda**, escriba **Tinfoil Security**.

    ![Galería de aplicaciones](./media/active-directory-saas-tinfoil-security-tutorial/IC798966.png "Galería de aplicaciones")

7.  En el panel de resultados, seleccione **Tinfoil Security** y luego haga clic en **Completar** para agregar la aplicación.

    ![Tinfoil Security](./media/active-directory-saas-tinfoil-security-tutorial/IC802771.png "Tinfoil Security")

##Configuración del inicio de sesión único
  
El objetivo de esta sección es describir cómo habilitar usuarios para que se autentiquen en Tinfoil Security con su cuenta de Azure AD mediante la federación basada en el protocolo SAML. La configuración de un inicio de sesión único para Tinfoil Security requiere la recuperación de un valor de huella digital de un certificado. Si no está familiarizado con este procedimiento, consulte [Recuperación del valor de huella digital de un certificado](http://youtu.be/YKQF266SAxI).

###Siga estos pasos para configurar el inicio de sesión único:

1.  En el Portal de Azure AD, en la página de integración de aplicaciones de **Tinfoil Security**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-tinfoil-security-tutorial/IC798967.png "Configurar inicio de sesión único")

2.  En la página **¿Cómo desea que los usuarios inicien sesión en Tinfoil Security?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y luego haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-tinfoil-security-tutorial/IC798968.png "Configurar inicio de sesión único")

3.  En la página **Configurar URL de aplicación**, en el cuadro de texto **Dirección URL de respuesta de Tinfoil Security**, escriba la dirección URL del servicio del consumidor de aserción (ACS) de Tinfoil Security (por ejemplo, "**https://www.tinfoilsecurity.com/saml/consume*" y luego haga clic en **Siguiente**.

    >[AZURE.NOTE]Debería poder obtener la dirección URL de ACS de los metadatos de Tinfoil Security (https://www.tinfoilsecurity.com/saml/metadata).

    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-tinfoil-security-tutorial/IC798969.png "Configurar dirección URL de la aplicación")

4.  En la página **Configurar inicio de sesión único en Tinfoil Security**, para descargar el certificado, haga clic en **Descargar certificado** y luego guarde el archivo de certificado en el equipo como **c:\\Tinfoil Security.cer**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-tinfoil-security-tutorial/IC798970.png "Configurar inicio de sesión único")

5.  En otra ventana del explorador web, inicie sesión como administrador en el sitio de la compañía de Tinfoil Security.

6.  En la barra de herramientas de la parte superior, haga clic en el icono de **Mi cuenta**.

    ![Panel](./media/active-directory-saas-tinfoil-security-tutorial/IC798971.png "Panel")

7.  Haga clic en **Seguridad**.

    ![Seguridad](./media/active-directory-saas-tinfoil-security-tutorial/IC798972.png "Seguridad")

8.  Siga estos pasos en la página de configuración **Inicio de sesión único**:

    ![Inicio de sesión único](./media/active-directory-saas-tinfoil-security-tutorial/IC798973.png "Inicio de sesión único")

    1.  Seleccione **Habilitar SAML**.
    2.  Haga clic en **Configuración manual**.
    3.  En el Portal de Azure, en la página de diálogo **Configurar inicio de sesión único en Tinfoil Security**, copie el valor de **URL de SSO de SAML** y péguelo en el cuadro de texto **URL del mensaje de SAML**.
    4.  Copie el valor de **Huella digital** del certificado exportado y péguelo en el cuadro de texto **Huella digital del certificado de SAML**.  

        >[AZURE.TIP]Para obtener más información, consulte [Recuperación del valor de huella digital de un certificado](http://youtu.be/YKQF266SAxI).

    5.  Copie **Su id. de cuenta**.
    6.  Haga clic en **Guardar**.

9.  En el Portal de Azure AD, seleccione la confirmación de configuración de inicio de sesión único y, a continuación, haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-tinfoil-security-tutorial/IC798974.png "Configurar inicio de sesión único")

10. En el menú de la parte superior, haga clic en **Atributos** para abrir el cuadro de diálogo **Atributos de token de SAML**.

    ![Atributos](./media/active-directory-saas-tinfoil-security-tutorial/IC795920.png "Atributos")

11. Para agregar las asignaciones de los atributos necesarios, realice los pasos siguientes:

    ![Atributos](./media/active-directory-saas-tinfoil-security-tutorial/IC798975.png "Atributos")

    1.  Haga clic en **agregar atributo de usuario**.
    2.  En el cuadro de texto **Nombre de atributo**, escriba **accountid**.
    3.  En el cuadro de texto **Valor del atributo**, pegue el valor del id. de la cuenta que ha copiado en la sección anterior.
    4.  Haga clic en **Completo**.

12. Haga clic en **Aplicar cambios**.

##Configuración del aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD inicien sesión en Tinfoil Security, deben aprovisionarse en Tinfoil Security. En el caso de Tinfoil Security, el aprovisionamiento es una tarea manual.

###Siga estos pasos para que se aprovisione un usuario:

1.  Si el usuario forma parte de una cuenta de empresa, deberá ponerse en contacto con el equipo de soporte técnico de Tinfoil Security para que se cree la cuenta del usuario.

2.  Si el usuario es usuario habitual de SaaS de Tinfoil Security, puede agregar un colaborador a cualquiera de sus sitios. Esto desencadena un proceso para enviar una invitación al correo electrónico especificado para crear una nueva cuenta de usuario de Tinfoil Security.

>[AZURE.NOTE]Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Tinfoil Security ofrecida por Tinfoil Security para aprovisionar cuentas de usuario de AAD.

##Asignación de usuarios
  
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

###Para asignar usuarios a Tinfoil Security, lleve a cabo los siguientes pasos:

1.  En el portal de Azure AD, cree una cuenta de prueba.

2.  En la página de integración de aplicaciones de **Tinfoil Security**, haga clic en **Asignar usuarios**.

    ![Asignar usuarios](./media/active-directory-saas-tinfoil-security-tutorial/IC798976.png "Asignar usuarios")

3.  Seleccione su usuario de prueba, haga clic en **Asignar** y luego en **Sí** para confirmar la asignación.

    ![Sí](./media/active-directory-saas-tinfoil-security-tutorial/IC767830.png "Sí")
  
Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0114_2016-->