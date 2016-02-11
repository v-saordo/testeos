<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con ArcGIS | Microsoft Azure" 
    description="Aprenda a usar ArcGIS con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc." 
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

#Tutorial: Integración de Azure Active Directory con ArcGIS

El objetivo de este tutorial es mostrar la integración de Azure y ArcGIS. En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción de Azure válida
-   Una suscripción habilitada para el inicio de sesión único en ArcGIS

Después de completar este tutorial, los usuarios de Azure AD que ha asignado a ArcGIS podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de ArcGIS (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md)

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1.  Habilitación de la integración de aplicaciones para ArcGIS
2.  Configuración del inicio de sesión único
3.  Configuración del aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario](./media/active-directory-saas-arcgis-tutorial/IC784735.png "Escenario")
##Habilitación de la integración de aplicaciones para ArcGIS

El objetivo de esta sección es describir cómo se habilita la integración de aplicaciones para ArcGIS.

###Siga estos pasos para habilitar la integración de aplicaciones para ArcGIS:

1.  En el panel de navegación izquierdo del Portal de administración de Azure, haga clic en **Active Directory**.

    ![Active Directory](./media/active-directory-saas-arcgis-tutorial/IC700993.png "Active Directory")

2.  En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3.  Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

    ![Aplicaciones](./media/active-directory-saas-arcgis-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación](./media/active-directory-saas-arcgis-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Agregar una aplicación de la galería](./media/active-directory-saas-arcgis-tutorial/IC749322.png "Agregar una aplicación de la galería")

6.  En el **cuadro de búsqueda**, escriba **ArcGIS**.

    ![Galería de aplicaciones](./media/active-directory-saas-arcgis-tutorial/IC784736.png "Galería de aplicaciones")

7.  En el panel de resultados, seleccione **ArcGIS** y luego haga clic en **Completar** para agregar la aplicación.

    ![ArcGIS](./media/active-directory-saas-arcgis-tutorial/IC784737.png "ArcGIS")
##Configuración del inicio de sesión único

El objetivo de esta sección es describir cómo habilitar usuarios para que se autentiquen en ArcGIS con su cuenta de Azure AD mediante federación basada en el protocolo SAML.

###Siga estos pasos para configurar el inicio de sesión único:

1.  En el Portal de Azure AD, en la página de integración de aplicaciones de **ArcGIS**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-arcgis-tutorial/IC784738.png "Configurar inicio de sesión único")

2.  En la página **¿Cómo desea que los usuarios inicien sesión en ArcGIS?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y luego haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-arcgis-tutorial/IC784739.png "Configurar inicio de sesión único")

3.  En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto **URL de inicio de sesión de ArcGIS**, escriba la dirección URL que usan los usuarios para iniciar sesión con el patrón "*https://company.maps.arcgis.com*" y luego haga clic en **Siguiente**.

    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-arcgis-tutorial/IC784740.png "Configurar dirección URL de la aplicación")

4.  En la página **Configuración de inicio de sesión único en ArcGIS**, haga clic en **Descargar metadatos** y, luego, guarde el archivo de metadatos localmente en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-arcgis-tutorial/IC784741.png "Configurar inicio de sesión único")

5.  En otra ventana del explorador web, inicie sesión en como administrador en el sitio de la compañía de ArcGIS.

6.  Haga clic en **Editar configuración**.

    ![Editar configuración](./media/active-directory-saas-arcgis-tutorial/IC784742.png "Editar configuración")

7.  Haga clic en **Seguridad**.

    ![Seguridad](./media/active-directory-saas-arcgis-tutorial/IC784743.png "Seguridad")

8.  En **Inicios de sesión de la empresa**, haga clic en **Establecer proveedor de identidades**.

    ![Inicios de sesión de la empresa](./media/active-directory-saas-arcgis-tutorial/IC784744.png "Inicios de sesión de la empresa")

9.  En la sección **Configurar proveedor de identidades**, realice los pasos siguientes:

    ![Establecer proveedor de identidades](./media/active-directory-saas-arcgis-tutorial/IC784745.png "Establecer proveedor de identidades")

    1.  En el cuadro de texto **Nombre**, escriba el nombre de su organización.
    2.  En **Los metadatos para el proveedor de identidades de la empresa se proporcionarán con**, seleccione **Un archivo**.
    3.  Haga clic en **Elegir archivo** para cargar el archivo de metadatos descargado.
    4.  Haga clic en **Establecer proveedor de identidades**.

10. En el Portal de Azure AD, seleccione la confirmación de configuración de inicio de sesión único y, luego, haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-arcgis-tutorial/IC784746.png "Configurar inicio de sesión único")
##Configuración del aprovisionamiento de usuario

Para permitir que los usuarios de Azure AD inicien sesión en ArcGIS, tienen que aprovisionarse en ArcGIS.  
En el caso de ArcGIS, el aprovisionamiento es una tarea manual.

###Siga estos pasos para configurar el aprovisionamiento de usuario:

1.  Inicie sesión en su inquilino de **ArcGIS**.

2.  Haga clic en **Invitar a miembros**.

    ![Invitar a miembros](./media/active-directory-saas-arcgis-tutorial/IC784747.png "Invitar a miembros")

3.  Seleccione **Agregar miembros automáticamente sin enviar un correo electrónico** y luego haga clic en **Siguiente**.

    ![Agregar miembros automáticamente](./media/active-directory-saas-arcgis-tutorial/IC784748.png "Agregar miembros automáticamente")

4.  En la página de diálogo **Miembros**, realice los pasos siguientes:

    ![Agregar y revisar](./media/active-directory-saas-arcgis-tutorial/IC784749.png "Agregar y revisar")

    1.  Escriba**Nombre**, **Apellido** y **Correo electrónico** de una cuenta válida de AAD que quiera aprovisionar.
    2.  Haga clic en **Agregar y revisar**.

5.  Revise los datos que ha escrito y luego haga clic en**Agregar miembros**.

    ![Agregar miembro](./media/active-directory-saas-arcgis-tutorial/IC784750.png "Agregar miembro")

>[AZURE.NOTE] Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de ArcGIS ofrecida por ArcGIS para aprovisionar cuentas de usuario de AAD.

##Asignación de usuarios

Para probar la configuración, tiene que conceder acceso, mediante su asignación, a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

###Para asignar usuarios a ArcGIS, lleve a cabo los siguientes pasos:

1.  En el Portal de Azure AD, cree una cuenta de prueba.

2.  En la página de integración de aplicaciones de **ArcGIS**, haga clic en **Asignar usuarios**.

    ![Asignar usuarios](./media/active-directory-saas-arcgis-tutorial/IC784751.png "Asignar usuarios")

3.  Seleccione su usuario de prueba, haga clic en **Asignar** y luego en **Sí** para confirmar la asignación.

    ![Sí](./media/active-directory-saas-arcgis-tutorial/IC767830.png "Sí")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0121_2016-->