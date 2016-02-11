<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con Kintone | Microsoft Azure" 
    description="Aprenda a usar Kintone con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc." 
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
    ms.date="01/05/2016" 
    ms.author="jeedes" />

#Tutorial: Integración de Azure Active Directory con Kintone
  
El objetivo de este tutorial es mostrar la integración de Azure y Kintone. En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción de Azure válida
-   Una suscripción habilitada para inicio de sesión único en Kintone
  
Después de completar este tutorial, los usuarios de Azure AD que ha asignado a Kintone podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de Kintone (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
La situación descrita en este tutorial consta de los siguientes bloques de creación:

1.  Habilitación de la integración de aplicaciones para Kintone
2.  Configuración del inicio de sesión único
3.  Configuración del aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario](./media/active-directory-saas-kintone-tutorial/IC785859.png "Escenario")
##Habilitación de la integración de aplicaciones para Kintone
  
El objetivo de esta sección es describir cómo habilitar la integración de aplicaciones para Kintone.

###Siga estos pasos para habilitar la integración de aplicaciones para Kintone:

1.  En el panel de navegación izquierdo del Portal de administración de Azure, haga clic en **Active Directory**.

    ![Active Directory](./media/active-directory-saas-kintone-tutorial/IC700993.png "Active Directory")

2.  En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3.  Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

    ![Aplicaciones](./media/active-directory-saas-kintone-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación](./media/active-directory-saas-kintone-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Agregar una aplicación de la galería](./media/active-directory-saas-kintone-tutorial/IC749322.png "Agregar una aplicación de la galería")

6.  En el **cuadro de búsqueda**, escriba **Kintone**.

    ![Galería de aplicaciones](./media/active-directory-saas-kintone-tutorial/IC785867.png "Galería de aplicaciones")

7.  En el panel de resultados, seleccione **Kintone** y luego haga clic en **Completar** para agregar la aplicación.

    ![Kintone](./media/active-directory-saas-kintone-tutorial/IC785871.png "Kintone")
##Configuración del inicio de sesión único
  
El objetivo de esta sección es describir cómo se habilita la autenticación de usuarios en Kintone con su cuenta de Azure AD mediante federación basada en el protocolo SAML.

###Siga estos pasos para configurar el inicio de sesión único:

1.  En el Portal de Azure AD, en la página de integración de aplicaciones de **Kintone**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-kintone-tutorial/IC785872.png "Configurar inicio de sesión único")

2.  En la página **¿Cómo desea que los usuarios inicien sesión en Kintone?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y, a continuación, haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-kintone-tutorial/IC785873.png "Configurar inicio de sesión único")

3.  En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto **URL de inicio de sesión de Kintone**, escriba su dirección URL con el siguiente patrón "**https://company.kintone.com*" y luego haga clic en **Siguiente**.

    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-kintone-tutorial/IC785875.png "Configurar dirección URL de la aplicación")

4.  En la página **Configuración de inicio de sesión único en Kintone**, para descargar el certificado, haga clic en **Descargar certificado** y luego guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-kintone-tutorial/IC785878.png "Configurar inicio de sesión único")

5.  En otra ventana del explorador web, inicie sesión como administrador en el sitio de la compañía de **Kintone**.

6.  Haga clic en **Configuración**.

    ![Settings](./media/active-directory-saas-kintone-tutorial/IC785879.png "Settings")

7.  Haga clic en **Users & System Administration** (Administración del sistema y usuarios).

    ![Administración del sistema y usuarios](./media/active-directory-saas-kintone-tutorial/IC785880.png "Administración del sistema y usuarios")

8.  En **Administración del sistema > Seguridad**, haga clic en **Inicio de sesión**.

    ![Inicio de sesión](./media/active-directory-saas-kintone-tutorial/IC785881.png "Inicio de sesión")

9.  Haga clic en **Habilitar autenticación SAML**.

    ![Autenticación SAML](./media/active-directory-saas-kintone-tutorial/IC785882.png "Autenticación SAML")

10. En la sección SAML Authentication (Autenticación SAML), realice los pasos siguientes:

    ![Autenticación SAML](./media/active-directory-saas-kintone-tutorial/IC785883.png "Autenticación SAML")

    1.  En el Portal de Azure, en la página de diálogo **Configurar inicio de sesión único en Kintone**, copie el valor de **Dirección URL del inicio de sesión remoto** y péguelo en el cuadro de texto **Dirección URL de inicio de sesión**.
    2.  En el Portal de Azure, en la página de diálogo **Configurar inicio de sesión único en Kintone**, copie el valor de **Dirección URL de cierre de sesión remoto** y péguelo en el cuadro de texto **Dirección URL de cierre de sesión**.
    3.  Haga clic en **Examinar** para cargar el certificado descargado.
    4.  Haga clic en **Guardar**.

11. En el Portal de Azure AD, seleccione la confirmación de configuración de inicio de sesión único y, a continuación, haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-kintone-tutorial/IC785884.png "Configurar inicio de sesión único")
##Configuración del aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD inicien sesión en Kintone, deben aprovisionarse en Kintone. En el caso de Kintone, el aprovisionamiento es una tarea manual.

###Para aprovisionar cuentas de usuario, realice estos pasos:

1.  Inicie sesión en el sitio de la compañía de **Kintone** como administrador.

2.  Haga clic en **Configuración**.

    ![Settings](./media/active-directory-saas-kintone-tutorial/IC785879.png "Settings")

3.  Haga clic en **Users & System Administration** (Administración del sistema y usuarios).

    ![Administración del sistema y usuario](./media/active-directory-saas-kintone-tutorial/IC785880.png "Administración del sistema y usuario")

4.  En **Administración de usuarios** haga clic en **Departamentos y usuarios**.

    ![Departamento y usuarios](./media/active-directory-saas-kintone-tutorial/IC785888.png "Departamento y usuarios")

5.  Haga clic en **Nuevo usuario**.

    ![Nuevos usuarios](./media/active-directory-saas-kintone-tutorial/IC785889.png "Nuevos usuarios")

6.  En la sección **Nuevo usuario**, lleve a cabo estos pasos:

    ![Nuevos usuarios](./media/active-directory-saas-kintone-tutorial/IC785890.png "Nuevos usuarios")

    1.  Escriba un **Nombre para mostrar**, **Nombre de inicio de sesión**, **Nueva contraseña**, **Confirmar contraseña**, **Dirección de correo electrónico** y otros detalles de una cuenta válida de AAD que quiera aprovisionar en los cuadros de texto relacionados.
    2.  Haga clic en **Guardar**.

>[AZURE.NOTE]Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Kintone ofrecida por Kintone para aprovisionar cuentas de usuario de AAD.

##Asignación de usuarios
  
Para probar la configuración, tiene que conceder acceso, mediante su asignación, a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

###Para asignar usuarios a Kintone, siga estos pasos:

1.  En el Portal de Azure AD, cree una cuenta de prueba.

2.  En la página de integración de aplicaciones de **Kintone **, haga clic en **Asignar usuarios**.

    ![Asignar usuarios](./media/active-directory-saas-kintone-tutorial/IC785891.png "Asignar usuarios")

3.  Seleccione su usuario de prueba, haga clic en **Asignar** y luego en **Sí** para confirmar la asignación.

    ![Sí](./media/active-directory-saas-kintone-tutorial/IC767830.png "Sí")
  
Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0107_2016-->