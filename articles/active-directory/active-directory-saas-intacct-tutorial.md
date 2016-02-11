<properties 
    pageTitle="Tutorial: integración de Azure Active Directory con Intacct | Microsoft Azure" 
    description="Aprenda a usar Intacct con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc." 
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

#Tutorial: integración de Azure Active Directory con Intacct
  
El objetivo de este tutorial es mostrar la integración de Azure e Intacct. En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción de Azure válida
-   Un inquilino de Intacct
  
Después de completar este tutorial, los usuarios de Azure AD que ha asignado a Intacct podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de Intacct (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
La situación descrita en este tutorial consta de los siguientes bloques de creación:

1.  Habilitación de la integración de aplicaciones para Intacct
2.  Configuración del inicio de sesión único
3.  Configuración del aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario](./media/active-directory-saas-intacct-tutorial/IC790030.png "Escenario")
##Habilitación de la integración de aplicaciones para Intacct
  
El objetivo de esta sección es describir cómo se habilita la integración de aplicaciones para Intacct.

###Siga estos pasos para habilitar la integración de aplicaciones para Intacct:

1.  En el panel de navegación izquierdo del Portal de administración de Azure, haga clic en **Active Directory**.

    ![Active Directory](./media/active-directory-saas-intacct-tutorial/IC700993.png "Active Directory")

2.  En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3.  Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

    ![Aplicaciones](./media/active-directory-saas-intacct-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación](./media/active-directory-saas-intacct-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Agregar una aplicación de la galería](./media/active-directory-saas-intacct-tutorial/IC749322.png "Agregar una aplicación de la galería")

6.  En el **cuadro de búsqueda**, escriba **Intacct**.

    ![Galería de aplicaciones](./media/active-directory-saas-intacct-tutorial/IC790031.png "Galería de aplicaciones")

7.  En el panel de resultados, seleccione **Intacct** y luego haga clic en **Completar** para agregar la aplicación.

    ![Intacct](./media/active-directory-saas-intacct-tutorial/IC790032.png "Intacct")
##Configuración del inicio de sesión único
  
El objetivo de esta sección es describir cómo se habilita la autenticación de usuarios en Intacct con su cuenta de Azure AD mediante la federación basada en el protocolo SAML. Como parte de este procedimiento, se requiere crear un archivo de certificado codificado en base 64. Si no está familiarizado con este procedimiento, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

###Siga estos pasos para configurar el inicio de sesión único:

1.  En el Portal de Azure AD, en la página de integración de aplicaciones de **Intacct**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-intacct-tutorial/IC790033.png "Configurar inicio de sesión único")

2.  En la página **¿Cómo desea que los usuarios inicien sesión en Intacct?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y luego haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-intacct-tutorial/IC790034.png "Configurar inicio de sesión único")

3.  En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto **URL de inicio de sesión de Intacct**, escriba su dirección URL con el siguiente patrón "**https://Intacct.com/company*" y luego haga clic en **Siguiente**.

    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-intacct-tutorial/IC790035.png "Configurar dirección URL de la aplicación")

4.  En la página **Configuración de inicio de sesión único en Intacct**, haga clic en **Descargar certificado** y luego guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-intacct-tutorial/IC790036.png "Configurar inicio de sesión único")

5.  En otra ventana del explorador web, inicie sesión como administrador en el sitio de la compañía de Intacct.

6.  Haga clic en la pestaña **Compañía** y luego haga clic en**Información de la compañía**.

    ![Compañía](./media/active-directory-saas-intacct-tutorial/IC790037.png "Compañía")

7.  Haga clic en la pestaña **Seguridad** y luego haga clic en **Editar**.

    ![Seguridad](./media/active-directory-saas-intacct-tutorial/IC790038.png "Seguridad")

8.  En la sección **Inicio de sesión único (SSO)**, siga estos pasos:

    ![Inicio de sesión único](./media/active-directory-saas-intacct-tutorial/IC790039.png "Inicio de sesión único")

    1.  Seleccione **Habilitar inicio de sesión único**.
    2.  En **Tipo de proveedor de identidad**, seleccione **SAML 2.0**.
    3.  En el Portal de Azure, en la página de diálogo **Configurar inicio de sesión único en Intacct**, copie el valor de **URL del emisor** y péguelo en el cuadro de texto **URL del emisor**.
    4.  En el Portal de Azure, en la página de diálogo **Configurar inicio de sesión único en Intacct**, copie el valor de **Dirección URL del inicio de sesión remoto** y péguelo en el cuadro de texto **Dirección URL de inicio de sesión**.
    5.  Cree un archivo **codificado en base 64** a partir del certificado descargado.
        
		>[AZURE.TIP]Para obtener más información, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

    6.  Abra el certificado codificado en base 64 en el Bloc de notas, copie el contenido del mismo en el Portapapeles y luego péguelo en el cuadro de texto **Certificado**.
    7.  Haga clic en **Guardar**.

9.  En el Portal de Azure AD, seleccione la confirmación de configuración de inicio de sesión único y, a continuación, haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-intacct-tutorial/IC790040.png "Configurar inicio de sesión único")
##Configuración del aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD inicien sesión en Intacct, tienen que aprovisionarse en Intacct. En el caso de Intacct, el aprovisionamiento es una tarea manual.

###Para aprovisionar cuentas de usuario, realice estos pasos:

1.  Inicie sesión en su inquilino de **Intacct**.

2.  Haga clic en la pestaña **Compañía** y luego haga clic en**Usuarios**.

    ![Usuarios](./media/active-directory-saas-intacct-tutorial/IC790041.png "Usuarios")

3.  Haga clic en la pestaña **Agregar**.

    ![Agregar](./media/active-directory-saas-intacct-tutorial/IC790042.png "Agregar")

4.  En la sección **Información del usuario**, lleve a cabo estos pasos:

    ![Información de usuario](./media/active-directory-saas-intacct-tutorial/IC790043.png "Información de usuario")

    1.  Escriba el **Id. de usuario**, **Apellidos**, **Nombre**, **Dirección de correo electrónico**, **Puesto** y **Teléfono** de una cuenta de Azure AD que quiera aprovisionar en los cuadros de texto relacionados.
    2.  Seleccione **Privilegios administrativos** de una cuenta de Azure AD que quiera aprovisionar.
    3.  Haga clic en **Guardar**.
        
		>[AZURE.NOTE]El titular de la cuenta de AAD recibirá un mensaje de correo y seguirá un vínculo para confirmar su cuenta antes de que se active.

>[AZURE.NOTE]Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Intacct ofrecida por Intacct para aprovisionar cuentas de usuario de AAD.

##Asignación de usuarios
  
Para probar la configuración, tiene que conceder acceso, mediante su asignación, a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

###Para asignar usuarios a Intacct, lleve a cabo los siguientes pasos:

1.  En el Portal de Azure AD, cree una cuenta de prueba.

2.  En la página de integración de aplicaciones de **Intacct **, haga clic en **Asignar usuarios**.

    ![Asignar usuarios](./media/active-directory-saas-intacct-tutorial/IC790044.png "Asignar usuarios")

3.  Seleccione su usuario de prueba, haga clic en **Asignar** y luego en **Sí** para confirmar la asignación.

    ![Sí](./media/active-directory-saas-intacct-tutorial/IC767830.png "Sí")
  
Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0121_2016-->