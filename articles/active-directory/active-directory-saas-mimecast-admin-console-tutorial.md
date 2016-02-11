<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con Mimecast Admin Console | Microsoft Azure" 
    description="Aprenda a usar Mimecast Admin Console con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc." 
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

#Tutorial: Integración de Azure Active Directory con Mimecast Admin Console
  
El objetivo de este tutorial es mostrar la integración de Azure y Mimecast Admin Console. En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción de Azure válida
-   Un suscripción habilitada para inicio de sesión único en Mimecast Admin Console
  
Después de completar este tutorial, los usuarios de Azure AD asignados a Mimecast Admin Console podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de Mimecast Admin Console (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
La situación descrita en este tutorial consta de los siguientes bloques de creación:

1.  Habilitación de la integración de aplicaciones para Mimecast Admin Console
2.  Configuración del inicio de sesión único
3.  Configuración del aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795008.png "Escenario")
##Habilitación de la integración de aplicaciones para Mimecast Admin Console
  
El objetivo de esta sección es describir cómo habilitar la integración de aplicaciones para Mimecast Admin Console.

###Siga estos pasos para habilitar la integración de aplicaciones para Mimecast Admin Console:

1.  En el panel de navegación izquierdo del Portal de administración de Azure, haga clic en **Active Directory**.

    ![Active Directory](./media/active-directory-saas-mimecast-admin-console-tutorial/IC700993.png "Active Directory")

2.  En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3.  Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

    ![Aplicaciones](./media/active-directory-saas-mimecast-admin-console-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación](./media/active-directory-saas-mimecast-admin-console-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Agregar una aplicación de la galería](./media/active-directory-saas-mimecast-admin-console-tutorial/IC749322.png "Agregar una aplicación de la galería")

6.  En el **cuadro de búsqueda**, escriba **Mimecast Admin Console**.

    ![Galería de aplicaciones](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795009.png "Galería de aplicaciones")

7.  En el panel de resultados, seleccione **Mimecast Admin Console** y, a continuación, haga clic en **Completar** para agregar la aplicación.

    ![Consola de administración de Mimecast](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795010.png "Consola de administración de Mimecast")
##Configuración del inicio de sesión único
  
El objetivo de esta sección es describir cómo habilitar la autenticación de usuarios en Mimecast Admin Console con su cuenta de Azure AD mediante federación basada en el protocolo SAML. Como parte de este procedimiento, es necesario crear un archivo de certificado codificado en base 64. Si no está familiarizado con este procedimiento, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

###Siga estos pasos para configurar el inicio de sesión único:

1.  En el Portal de Azure AD, en la página de integración de aplicaciones de **Mimecast Admin Console**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795011.png "Configurar inicio de sesión único")

2.  En la página **¿Cómo desea que los usuarios inicien sesión en Mimecast Admin Console?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y, a continuación, haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795012.png "Configurar inicio de sesión único")

3.  En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto **URL de inicio de sesión de Mimecast Admin Console**, escriba la dirección URL que los usuarios utilizan para iniciar sesión en la aplicación Mimecast Admin Console (por ejemplo, “https://webmail-uk.mimecast.com” o “https://webmail-us.mimecast.com”) y haga clic en **Siguiente**.

    >[AZURE.NOTE]La dirección URL de inicio de sesión es específica de la región.

    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795013.png "Configurar dirección URL de la aplicación")

4.  En la página **Configurar inicio de sesión único en Mimecast Admin Console**, para descargar el certificado, haga clic en **Descargar certificado** y, a continuación, guarde el archivo de certificado localmente en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795014.png "Configurar inicio de sesión único")

5.  En otra ventana del explorador web, inicie sesión como administrador en el sitio de la compañía de Mimecast Admin Console.

6.  Vaya a **Servicios > Aplicación**.

    ![Servicios](./media/active-directory-saas-mimecast-admin-console-tutorial/IC794998.png "Servicios")

7.  Haga clic en **Authentication Profiles** (Perfiles de autenticación).

    ![Perfiles de autenticación](./media/active-directory-saas-mimecast-admin-console-tutorial/IC794999.png "Perfiles de autenticación")

8.  Haga clic en **New Authentication Profile** (Nuevo perfil de autenticación).

    ![Nuevos perfiles de autenticación](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795000.png "Nuevos perfiles de autenticación")

9.  En la sección **Authentication Profile** (Perfil de autenticación), realice estos pasos:

    ![Perfil de autenticación](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795015.png "Perfil de autenticación")

    1.  En el cuadro de texto **Description** (Descripción), escriba un nombre para la configuración.
    2.  Seleccione **Enforce SAML Authentication for Mimecast Admin Console** (Aplicar la autenticación SAML a Mimecast Admin Console).
    3.  Como **Provider** (Proveedor), seleccione **Azure Active Directory**.
    4.  En el Portal de Azure, en la página de diálogo **Configurar inicio de sesión único en Mimecast Admin Console**, copie el valor de **URL del emisor** y péguelo en el cuadro de texto **URL del emisor**.
    5.  En el Portal de Azure, en la página de diálogo **Configurar inicio de sesión único en Mimecast Admin Console**, copie el valor de **Dirección URL de inicio de sesión remoto** y péguelo en el cuadro de texto **Dirección URL de inicio de sesión**.
    6.  En el Portal de Azure, en la página de diálogo **Configurar inicio de sesión único en Mimecast Admin Console**, copie el valor de **Dirección URL de inicio de sesión remoto** y péguelo en el cuadro de texto **Dirección URL de cierre de sesión**.  

        >[AZURE.NOTE]El valor de Dirección URL de inicio de sesión y el valor de Dirección URL de cierre de sesión para Mimecast Admin Console son los mismos.

    7.  Cree un archivo **codificado en base 64** a partir del certificado descargado.

        >[AZURE.TIP]Para obtener más información, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

    8.  Abra el certificado codificado en base 64 en el Bloc de notas, quite la primera línea (“*--*”) y la última línea (“*--*”), copie el resto del contenido en el Portapapeles y, a continuación, péguelo en el cuadro de texto **Certificado de proveedor de identidades (metadatos)**.
    9.  Seleccione **Permitir inicio de sesión único**.
    10. Haga clic en **Guardar**.

10. En el Portal de Azure AD, seleccione la confirmación de configuración de inicio de sesión único y, a continuación, haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795016.png "Configurar inicio de sesión único")
##Configuración del aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD inicien sesión en Mimecast Admin Console, deben aprovisionarse en Mimecast Admin Console. En el caso de Mimecast Admin Console, el aprovisionamiento es una tarea manual.
  
Deberá registrar un dominio para poder crear los usuarios.

###Siga estos pasos para configurar el aprovisionamiento de usuario:

1.  Inicie sesión en **Mimecast Admin Console** como administrador.

2.  Vaya a **Directories > Internal** (Directorios > Interno).

    ![Directorios](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795003.png "Directorios")

3.  Haga clic en **Register New Domain** (Registrar nuevo dominio).

    ![Registrar nuevo dominio](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795004.png "Registrar nuevo dominio")

4.  Una vez creado el nuevo dominio, haga clic en **New Address** (Nueva dirección).

    ![Nueva dirección](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795005.png "Nueva dirección")

5.  En el cuadro de diálogo Nueva dirección, realice estos pasos:

    ![Save](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795006.png "Save")

    1.  Escriba los datos de una cuenta de AAD válida que desee aprovisionar en los cuadros de texto correspondientes: **Email Address** (Dirección de correo electrónico), **Global Name** (Nombre global), **Password** (Contraseña) y **Confirm Password** (Confirmar contraseña).
    2.  Haga clic en **Guardar**.

>[AZURE.NOTE]Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Mimecast Admin Console ofrecida por Mimecast Admin Console para aprovisionar cuentas de usuario de AAD.

##Asignación de usuarios
  
Para probar la configuración, debe asignar los usuarios de Azure AD que quiera que usen su aplicación para concederles acceso a ella.

###Para asignar usuarios a Mimecast Admin Console, siga estos pasos:

1.  En el Portal de Azure AD, cree una cuenta de prueba.

2.  En la página de integración de aplicaciones de **Mimecast Admin Console**, haga clic en **Asignar usuarios**.

    ![Asignar usuarios](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795017.png "Asignar usuarios")

3.  Seleccione su usuario de prueba, haga clic en **Asignar** y luego en **Sí** para confirmar la asignación.

    ![Sí](./media/active-directory-saas-mimecast-admin-console-tutorial/IC767830.png "Sí")
  
Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0121_2016-->