<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con Veracode | Microsoft Azure" 
    description="Aprenda cómo usar Veracode con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc." 
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

#Tutorial: integración de Azure Active Directory con Veracode
  
El objetivo de este tutorial es mostrar la integración de Azure y Veracode. En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción de Azure válida
-   Una suscripción habilitada para el inicio de sesión único en Veracode
  
Después de completar este tutorial, los usuarios de Azure AD que ha asignado a Veracode podrá realizar un inicio de sesión único en la aplicación con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
La situación descrita en este tutorial consta de los siguientes bloques de creación:

1.  Habilitación de la integración de aplicaciones para Veracode
2.  Configuración del inicio de sesión único
3.  Configuración del aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario](./media/active-directory-saas-veracode-tutorial/IC802903.png "Escenario")

##Habilitación de la integración de aplicaciones para Veracode
  
El objetivo de esta sección es describir cómo se habilita la integración de aplicaciones en Veracode.

###Siga estos pasos para habilitar la integración de aplicaciones para Veracode:

1.  En el panel de navegación izquierdo del Portal de administración de Azure, haga clic en **Active Directory**.

    ![Active Directory](./media/active-directory-saas-veracode-tutorial/IC700993.png "Active Directory")

2.  En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3.  Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

    ![Aplicaciones](./media/active-directory-saas-veracode-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación](./media/active-directory-saas-veracode-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Agregar una aplicación de la galería](./media/active-directory-saas-veracode-tutorial/IC749322.png "Agregar una aplicación de la galería")

6.  En el **cuadro de búsqueda**, escriba **Veracode**.

    ![Galería de aplicaciones](./media/active-directory-saas-veracode-tutorial/IC802904.png "Galería de aplicaciones")

7.  En el panel de resultados, seleccione **Veracode** y luego haga clic en **Completar** para agregar la aplicación.

    ![Veracode](./media/active-directory-saas-veracode-tutorial/IC802905.png "Veracode")

##Configuración del inicio de sesión único
  
El objetivo de esta sección es describir cómo habilitar usuarios para que se autentiquen en Veracode con su cuenta de Azure AD a través de la federación basada en el protocolo SAML. La aplicación Veracode espera las aserciones de SAML en un formato específico, que requiere que se agreguen asignaciones de atributos personalizados a la configuración de los **atributos del token de SAML**. La siguiente captura de pantalla le muestra un ejemplo de esto.

![Atributos](./media/active-directory-saas-veracode-tutorial/IC802906.png "Atributos")

###Siga estos pasos para configurar el inicio de sesión único:

1.  En el Portal de Azure AD, en la página de integración de aplicaciones de **Veracode**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-veracode-tutorial/IC802907.png "Configurar inicio de sesión único")

2.  En la página **¿Cómo desea que los usuarios inicien sesión en Veracode?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y luego haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-veracode-tutorial/IC802908.png "Configurar inicio de sesión único")

3.  En la página **Configurar las opciones de la aplicación**, haga clic en **Siguiente**.

    ![Configurar las opciones de la aplicación](./media/active-directory-saas-veracode-tutorial/IC802909.png "Configurar las opciones de la aplicación")

4.  En la página **Configurar inicio de sesión único en Veracode**, para descargar el certificado, haga clic en **Descargar certificado** y luego guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-veracode-tutorial/IC802910.png "Configurar inicio de sesión único")

5.  En otra ventana del explorador web, inicie sesión en su sitio de la compañía de Veracode como administrador.

6.  En el menú de la parte superior, haga clic en **Configuración** y luego en **Administración**.

    ![Administración](./media/active-directory-saas-veracode-tutorial/IC802911.png "Administración")

7.  Haga clic en la pestaña **SAML**.

8.  En la sección **Configuración de SAML de organización** siga estos pasos:

    ![Administración](./media/active-directory-saas-veracode-tutorial/IC802912.png "Administración")

    1.  En el portal de Azure, en la página de diálogo **Configurar inicio de sesión único en Veracode**, copie el valor de **URL del emisor** y péguelo en el cuadro de texto **Emisor**.
    2.  Para cargar el certificado descargado, haga clic en **Elegir archivo**.
    3.  Seleccione **Habilitar registro automático**.

9.  En la sección **Configuración de registro automático**, realice los pasos siguientes y luego haga clic en **Guardar**:

    ![Administración](./media/active-directory-saas-veracode-tutorial/IC802913.png "Administración")

    1.  Como **Activación de nuevo usuario**, seleccione **No se requiere ninguna activación**.
    2.  Como **Actualizaciones de datos de usuario**, seleccione **Datos de usuario de Veracode de preferencia**.
    3.  Para **Detalles de atributo de SAML**, seleccione lo siguiente:
        -   **Roles de usuario**
        -   **Administrador de directivas**
        -   **Revisor**
        -   **Principal de seguridad**
        -   **Ejecutivo**
        -   **Remitente**
        -   **Creador**
        -   **Todos los tipos de detección**
        -   **Pertenencias a equipos**
        -   **Equipo predeterminado**

10. En el Portal de Azure AD, seleccione la confirmación de configuración de inicio de sesión único y, luego, haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-veracode-tutorial/IC802914.png "Configurar inicio de sesión único")

11. En el menú de la parte superior, haga clic en **Atributos** para abrir el cuadro de diálogo **Atributos de token de SAML**.

    ![Atributos](./media/active-directory-saas-veracode-tutorial/IC795920.png "Atributos")

12. Para agregar las asignaciones de los atributos necesarios, realice los pasos siguientes:

    ![Atributos](./media/active-directory-saas-veracode-tutorial/IC802906.png "Atributos")

	| Nombre del atributo | Valor de atributo |
	|:---------------|:----------------|
	| firstname | User.givenname |
	| lastname | User.surname |
	| email | User.mail |

    1.  En cada fila de datos de la tabla anterior, haga clic en **agregar atributo de usuario**.
    
	2.  En el cuadro de texto **Nombre de atributo**, escriba el nombre de atributo que se muestra para la fila.

    3.  En el cuadro de texto **Valor de atributo**, seleccione el valor de atributo que se muestra para la fila.

    4.  Haga clic en **Completo**.

13. Haga clic en **Aplicar cambios**.

##Configuración del aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD inicien sesión en Veracode, deben aprovisionarse en Veracode. En el caso de Veracode, el aprovisionamiento es una tarea automatizada. No hay ningún elemento de acción para usted.
  
Los usuarios se crean automáticamente si es necesario durante el primer intento de inicio de sesión único.

>[AZURE.NOTE]Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Veracode ofrecida por Veracode para aprovisionar cuentas de usuario de AAD.

##Asignación de usuarios
  
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

###Para asignar usuarios a Veracode, lleve a cabo los siguientes pasos:

1.  En el portal de Azure AD, cree una cuenta de prueba.

2.  En la página de integración de la aplicación **Veracode**, haga clic en **Asignar usuarios**.

    ![Asignar usuarios](./media/active-directory-saas-veracode-tutorial/IC802915.png "Asignar usuarios")

3.  Seleccione su usuario de prueba, haga clic en **Asignar** y luego en **Sí** para confirmar la asignación.

    ![Sí](./media/active-directory-saas-veracode-tutorial/IC767830.png "Sí")
  
Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0114_2016-->