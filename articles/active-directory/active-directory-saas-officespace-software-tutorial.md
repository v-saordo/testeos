<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con OfficeSpace Software | Microsoft Azure" 
    description="Aprenda cómo usar OfficeSpace Software con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc." 
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

#Tutorial: Integración de Azure Active Directory con OfficeSpace Software
  
El objetivo de este tutorial es mostrar la integración de Azure e OfficeSpace Software. En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción de Azure válida
-   Un suscripción habilitada para el inicio de sesión único en OfficeSpace Software
  
Después de completar este tutorial, los usuarios de Azure AD que ha asignado a OfficeSpace Software podrán realizar un inicio de sesión único en la aplicación en el sitio de OfficeSpace Software de la compañía (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
La situación descrita en este tutorial consta de los siguientes bloques de creación:

1.  Habilitación de la integración de aplicaciones para OfficeSpace Software
2.  Configuración del inicio de sesión único
3.  Configuración del aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario](./media/active-directory-saas-officespace-software-tutorial/IC777764.png "Escenario")
##Habilitación de la integración de aplicaciones para OfficeSpace Software
  
El objetivo de esta sección es describir cómo habilitar la integración de las aplicaciones para OfficeSpace Software.

###Siga estos pasos para habilitar la integración de aplicaciones para OfficeSpace Software:

1.  En el panel de navegación izquierdo del Portal de administración de Azure, haga clic en **Active Directory**.

    ![Active Directory](./media/active-directory-saas-officespace-software-tutorial/IC700993.png "Active Directory")

2.  En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3.  Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

    ![Aplicaciones](./media/active-directory-saas-officespace-software-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación](./media/active-directory-saas-officespace-software-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Agregar una aplicación de la galería](./media/active-directory-saas-officespace-software-tutorial/IC749322.png "Agregar una aplicación de la galería")

6.  En el **cuadro de búsqueda**, escriba **OfficeSpace Software**.

    ![Galería de aplicaciones](./media/active-directory-saas-officespace-software-tutorial/IC777765.png "Galería de aplicaciones")

7.  En el panel de resultados, seleccione **OfficeSpace Software** y, luego, haga clic en **Completa** para agregar la aplicación.

    ![OfficeSpace Software](./media/active-directory-saas-officespace-software-tutorial/IC781007.png "OfficeSpace Software")
##Configuración del inicio de sesión único
  
El objetivo de esta sección es describir cómo se habilita la autenticación de los usuarios en OfficeSpace Software con su cuenta de Azure AD usando el protocolo SAML basado en la federación. La configuración de un inicio de sesión único para OfficeSpace Software requiere la recuperación de un valor de huella digital de un certificado. Si no está familiarizado con este procedimiento, consulte [Recuperación del valor de huella digital de un certificado](http://youtu.be/YKQF266SAxI).

###Siga estos pasos para configurar el inicio de sesión único:

1.  En el portal de Azure AD, en la página de integración de aplicaciones de **OfficeSpace Software**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-officespace-software-tutorial/IC777766.png "Configurar inicio de sesión único")

2.  En la página **¿Cómo desea que los usuarios inicien sesión en OfficeSpace Software?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y luego haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-officespace-software-tutorial/IC777767.png "Configurar inicio de sesión único")

3.  En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto **URL de inicio de sesión de OfficeSpace Software**, escriba la dirección URL que utilizan los usuarios para iniciar sesión en su aplicación OfficeSpace Software (por ejemplo: "**https://company.officespacesoftware.com*")) y luego haga clic en **Siguiente**.

    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-officespace-software-tutorial/IC775556.png "Configurar dirección URL de la aplicación")

4.  En la página **Configurar inicio de sesión único en OfficeSpace Software**, para descargar el certificado, haga clic en **Descargar certificado** y luego guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-officespace-software-tutorial/IC793769.png "Configurar inicio de sesión único")

5.  En otra ventana del explorador web, inicie sesión en como administrador en el sitio de OfficeSpace Software de la compañía.

6.  Vaya a **Administración > Conectores**.

    ![Administrador](./media/active-directory-saas-officespace-software-tutorial/IC777769.png "Administrador")

7.  Haga clic en **Autorización de SAML**.

    ![Conectores](./media/active-directory-saas-officespace-software-tutorial/IC777770.png "Conectores")

8.  En la sección **Autorización SAML**, realice los pasos siguientes:

    ![Configuración de SAML](./media/active-directory-saas-officespace-software-tutorial/IC777771.png "Configuración de SAML")

    1.  En el portal de Azure, en la página de diálogo **Configurar inicio de sesión único en OfficeSpace Software**, copie el valor de **Dirección URL de inicio de sesión remoto** y péguelo en el cuadro de texto **Dirección URL del proveedor de cierre de sesión**.
    2.  En el portal de Azure, en la página de diálogo **Configurar inicio de sesión único en OfficeSpace Software**, copie el valor de **Dirección URL de cierre de sesión remoto** y péguelo en el cuadro de texto **Dirección URL de destino de IdP de cliente**.
    3.  Copie el valor de **Huella digital** del certificado exportado y luego péguelo en el cuadro de texto **Huella digital del certificado de IdP de cliente**.  

        >[AZURE.TIP]Para obtener más información, consulte [Recuperación del valor de huella digital de un certificado](http://youtu.be/YKQF266SAxI).

    4.  Haga clic en **Guardar configuración**.

9.  En el portal de Azure AD, seleccione la confirmación de configuración de inicio de sesión único y luego haga clic en **Completa** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-officespace-software-tutorial/IC777772.png "Configurar inicio de sesión único")
##Configuración del aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD inicien sesión en OfficeSpace Software, tienen que aprovisionarse en OfficeSpace Software. En el caso de OfficeSpace Software, el aprovisionamiento es una tarea automatizada. No hay ningún elemento de acción para usted. Los usuarios se crean automáticamente si es necesario durante el primer intento de inicio de sesión único.

>[AZURE.NOTE]Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de OfficeSpace Software ofrecida por OfficeSpace Software para aprovisionar cuentas de usuario de AAD.

##Asignación de usuarios
  
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

###Para asignar usuarios a OfficeSpace Software, lleve a cabo los siguientes pasos:

1.  En el portal de Azure AD, cree una cuenta de prueba.

2.  En la página de integración de aplicaciones de **OfficeSpace Software**, haga clic en **Asignar usuarios**.

    ![Asignar usuarios](./media/active-directory-saas-officespace-software-tutorial/IC777773.png "Asignar usuarios")

3.  Seleccione su usuario de prueba, haga clic en **Asignar** y luego en **Sí** para confirmar la asignación.

    ![Sí](./media/active-directory-saas-officespace-software-tutorial/IC767830.png "Sí")
  
Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0121_2016-->