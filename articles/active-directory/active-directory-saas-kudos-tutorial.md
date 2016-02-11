<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con Kudos | Microsoft Azure" 
    description="Aprenda a usar Kudos con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc." 
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

#Tutorial: Integración de Azure Active Directory con Kudos
  
El objetivo de este tutorial es mostrar la integración de Azure y Kudos.  
En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción de Azure válida
-   Un inquilino de Kudos
  
Después de completar este tutorial, los usuarios de Azure AD asignados a Kudos podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de Kudos (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
La situación descrita en este tutorial consta de los siguientes bloques de creación:

1.  Habilitación de la integración de aplicaciones para Kudos
2.  Configuración del inicio de sesión único
3.  Configuración del aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario](./media/active-directory-saas-kudos-tutorial/IC787799.png "Escenario")
##Habilitación de la integración de aplicaciones para Kudos
  
El objetivo de esta sección es describir cómo habilitar la integración de aplicaciones para Kudos.

###Siga estos pasos para habilitar la integración de aplicaciones para Kudos:

1.  En el panel de navegación izquierdo del Portal de administración de Azure, haga clic en **Active Directory**.

    ![Active Directory](./media/active-directory-saas-kudos-tutorial/IC700993.png "Active Directory")

2.  En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3.  Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

    ![Aplicaciones](./media/active-directory-saas-kudos-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación](./media/active-directory-saas-kudos-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Agregar una aplicación de la galería](./media/active-directory-saas-kudos-tutorial/IC749322.png "Agregar una aplicación de la galería")

6.  En el **cuadro de búsqueda**, escriba **Kudos**.

    ![Galería de aplicaciones](./media/active-directory-saas-kudos-tutorial/IC787800.png "Galería de aplicaciones")

7.  En el panel de resultados, seleccione **Kudos** y luego haga clic en **Completar** para agregar la aplicación.

    ![Kudos](./media/active-directory-saas-kudos-tutorial/IC787801.png "Kudos")
##Configuración del inicio de sesión único
  
El objetivo de esta sección es describir cómo habilitar la autenticación de usuarios en Kudos con su cuenta de Azure AD mediante federación basada en el protocolo SAML.  
Como parte de este procedimiento, es necesario crear un archivo de certificado codificado en base 64.  
Si no está familiarizado con este procedimiento, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

###Siga estos pasos para configurar el inicio de sesión único:

1.  En el Portal de Azure AD, en la página de integración de aplicaciones de **Kudos**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-kudos-tutorial/IC787802.png "Configurar inicio de sesión único")

2.  En la página **¿Cómo desea que los usuarios inicien sesión en Kudos?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y luego haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-kudos-tutorial/IC787803.png "Configurar inicio de sesión único")

3.  En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto **URL de inicio de sesión de Kudos**, escriba su dirección URL con el siguiente patrón "*https://company.kudosnow.com*" y luego haga clic en **Siguiente**.

    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-kudos-tutorial/IC787804.png "Configurar dirección URL de la aplicación")

4.  En la página **Configuración de inicio de sesión único en Kudos**, haga clic en **Descargar certificado** y luego guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-kudos-tutorial/IC787805.png "Configurar inicio de sesión único")

5.  En otra ventana del explorador web, inicie sesión como administrador en el sitio de la compañía de Kudos.

6.  En el menú de la parte superior, haga clic en **Configuración**.

    ![Settings](./media/active-directory-saas-kudos-tutorial/IC787806.png "Settings")

7.  Haga clic en **Integraciones > SSO**.

8.  En la sección **SSO**, siga estos pasos:

    ![SSO](./media/active-directory-saas-kudos-tutorial/IC787807.png "SSO")

    1.  En el Portal de Azure, en la página de diálogo **Configurar inicio de sesión único en Kudos**, copie el valor de **Dirección URL del servicio de inicio de sesión único** y péguelo en el cuadro de texto **Dirección URL de inicio de sesión**.
	2.  Cree un archivo **codificado en base 64** a partir del certificado descargado.  

        >[AZURE.TIP]
        Para obtener más información, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

    3.  Abra el certificado codificado en base 64 en el Bloc de notas, copie el contenido del mismo en el Portapapeles y luego péguelo en el cuadro de texto **Certificado X.509**.
    4.  En el Portal de Azure, en la página de diálogo **Configurar inicio de sesión único en Kudos**, copie el valor de **Dirección URL del servicio de cierre de sesión único** y péguelo en el cuadro de texto **Dirección URL de cierre de sesión**.
	5.  En el cuadro de texto **Su dirección URL de Kudos**, escriba el nombre de su compañía.
    6.  Haga clic en **Guardar**.

9.  En el Portal de Azure AD, seleccione la confirmación de configuración de inicio de sesión único y, a continuación, haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-kudos-tutorial/IC787808.png "Configurar inicio de sesión único")
##Configuración del aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD inicien sesión en Kudos, deben aprovisionarse en Kudos. En el caso de Kudos, el aprovisionamiento es una tarea manual.

###Para aprovisionar cuentas de usuario, realice estos pasos:

1.  Inicie sesión como administrador en el sitio de la compañía de **Kudos**.

2.  En el menú de la parte superior, haga clic en **Configuración**.

    ![Settings](./media/active-directory-saas-kudos-tutorial/IC787806.png "Settings")

3.  Haga clic en **Administrador de usuarios**.

4.  Haga clic en la pestaña **Usuarios** y, a continuación, haga clic en **Agregar un usuario**.

    ![Administrador de usuarios](./media/active-directory-saas-kudos-tutorial/IC787809.png "Administrador de usuarios")

5.  En la sección **Agregar usuario**, lleve a cabo estos pasos:

    ![Agregar un usuario](./media/active-directory-saas-kudos-tutorial/IC787810.png "Agregar un usuario")

    1.  En los cuadros de texto correspondientes, escriba los valores de **Nombre**, **Apellido**, **Correo electrónico** y otros detalles de la cuenta de Azure Active Directory válida que quiera aprovisionar.
    2.  Haga clic en **Crear usuario**.

>[AZURE.NOTE]Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Kudos ofrecida por Kudos para aprovisionar cuentas de usuario de AAD.

##Asignación de usuarios
  
Para probar la configuración, debe asignar los usuarios de Azure AD que quiera que usen su aplicación para concederles acceso a ella.

###Para asignar usuarios a Kudos, siga estos pasos:

1.  En el Portal de Azure AD, cree una cuenta de prueba.

2.  En la página de integración de aplicaciones de **Kudos**, haga clic en **Asignar usuarios**.

    ![Asignar usuarios](./media/active-directory-saas-kudos-tutorial/IC787811.png "Asignar usuarios")

3.  Seleccione su usuario de prueba, haga clic en **Asignar** y luego en **Sí** para confirmar la asignación.

    ![Sí](./media/active-directory-saas-kudos-tutorial/IC767830.png "Sí")
  
Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

<!----HONumber=AcomDC_0121_2016-->



