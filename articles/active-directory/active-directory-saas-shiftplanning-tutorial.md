<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con ShiftPlanning | Microsoft Azure" 
    description="Aprenda cómo usar ShiftPlanning con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc." 
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

#Tutorial: Integración de Azure Active Directory con ShiftPlanning
  
El objetivo de este tutorial es mostrar la integración de Azure y ShiftPlanning. En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción de Azure válida
-   Una suscripción habilitada para el inicio de sesión único en ShiftPlanning
  
Después de completar este tutorial, los usuarios de Azure AD que ha asignado a ShiftPlanning podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de ShiftPlanning (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
La situación descrita en este tutorial consta de los siguientes bloques de creación:

1.  Habilitación de la integración de aplicaciones para ShiftPlanning
2.  Configuración del inicio de sesión único
3.  Configuración del aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario](./media/active-directory-saas-shiftplanning-tutorial/IC786612.png "Escenario")
##Habilitación de la integración de aplicaciones para ShiftPlanning
  
El objetivo de esta sección es describir cómo habilitar la integración de las aplicaciones para ShiftPlanning.

###Siga estos pasos para habilitar la integración de aplicaciones para ShiftPlanning:

1.  En el panel de navegación izquierdo del Portal de administración de Azure, haga clic en **Active Directory**.

    ![Active Directory](./media/active-directory-saas-shiftplanning-tutorial/IC700993.png "Active Directory")

2.  En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3.  Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

    ![Aplicaciones](./media/active-directory-saas-shiftplanning-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación](./media/active-directory-saas-shiftplanning-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Agregar una aplicación de la galería](./media/active-directory-saas-shiftplanning-tutorial/IC749322.png "Agregar una aplicación de la galería")

6.  En el **cuadro de búsqueda**, escriba **ShiftPlanning**.

    ![Galería de aplicaciones](./media/active-directory-saas-shiftplanning-tutorial/IC786613.png "Galería de aplicaciones")

7.  En el panel de resultados, seleccione **ShiftPlanning** y luego haga clic en **Completa** para agregar la aplicación.

    ![ShiftPlanning](./media/active-directory-saas-shiftplanning-tutorial/IC786614.png "ShiftPlanning")
##Configuración del inicio de sesión único
  
El objetivo de esta sección es describir cómo se habilita la autenticación de los usuarios en ShiftPlanning con su cuenta de Azure AD usando el protocolo SAML basado en la federación. Como parte de este procedimiento, es necesario crear un archivo de certificado codificado en base 64. Si no está familiarizado con este procedimiento, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o)

###Siga estos pasos para configurar el inicio de sesión único:

1.  En el Portal de Azure AD, en la página de integración de aplicaciones de **ShiftPlanning**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-shiftplanning-tutorial/IC786615.png "Configurar inicio de sesión único")

2.  En la página **¿Cómo desea que los usuarios inicien sesión en ShiftPlanning?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y, luego , haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-shiftplanning-tutorial/IC786616.png "Configurar inicio de sesión único")

3.  En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto de **URL de inicio de sesión de ShiftPlanning**, escriba su dirección URL con el siguiente patrón "**https://company.shiftplanning.com/includes/saml/*" y luego haga clic en **Siguiente**.

    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-shiftplanning-tutorial/IC786617.png "Configurar dirección URL de la aplicación")

4.  En la página **Configuración de inicio de sesión único en ShiftPlanning**, para descargar el certificado, haga clic en **Descargar certificado** y luego guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-shiftplanning-tutorial/IC786618.png "Configurar inicio de sesión único")

5.  En otra ventana del explorador web, inicie sesión en su sitio de la compañía de **ShiftPlanning** como administrador.

6.  En el menú de la parte superior, haga clic en **Administrador**.

    ![Administrador](./media/active-directory-saas-shiftplanning-tutorial/IC786619.png "Administrador")

7.  En **Integración**, haga clic en **Inicio de sesión único**.

    ![Inicio de sesión único](./media/active-directory-saas-shiftplanning-tutorial/IC786620.png "Inicio de sesión único")

8.  En la sección **Inicio de sesión único**, siga estos pasos:

    ![Inicio de sesión único](./media/active-directory-saas-shiftplanning-tutorial/IC786905.png "Inicio de sesión único")

    1.  Seleccione **SAML habilitado**.
    2.  Seleccione **Permitir inicio de sesión de contraseña**.
    3.  En el Portal de Azure, en la página de diálogo **Configurar inicio de sesión único en ShiftPlanning**, copie el valor de **Dirección URL de inicio de sesión remoto** y péguelo en el cuadro de texto **Dirección URL de emisor de SAML**.
    4.  En el Portal de Azure, en la página de diálogo **Configurar inicio de sesión único en ShiftPlanning**, copie el valor de **Dirección URL de cierre de sesión remoto** y péguelo en el cuadro de texto **Dirección URL de cierre de sesión remoto**.
    5.  Cree un archivo **codificado en base 64** a partir del certificado descargado.  

        >[AZURE.TIP]Para obtener más información, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

    6.  Abra el certificado codificado en base 64 en el Bloc de notas, copie el contenido del mismo en el Portapapeles y luego péguelo en el cuadro de texto **Certificado X.509**.
    7.  Haga clic en **Guardar configuración**.

9.  En el portal de Azure AD, seleccione la confirmación de configuración de inicio de sesión único y luego haga clic en **Completa** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-shiftplanning-tutorial/IC786621.png "Configurar inicio de sesión único")
##Configuración del aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD inicien sesión en ShiftPlanning, deben aprovisionarse en ShiftPlanning. En el caso de ShiftPlanning, el aprovisionamiento es una tarea manual.

###Para aprovisionar cuentas de usuario, realice los siguientes pasos:

1.  Inicie sesión en su sitio de la compañía de **ShiftPlanning** como administrador.

2.  Haga clic en **Administrador**.

    ![Administrador](./media/active-directory-saas-shiftplanning-tutorial/IC786619.png "Administrador")

3.  Haga clic en **Personal**.

    ![Personal](./media/active-directory-saas-shiftplanning-tutorial/IC786623.png "Personal")

4.  En **Acciones**, haga clic en **Agregar empleado**.

    ![Agregar empleados](./media/active-directory-saas-shiftplanning-tutorial/IC786624.png "Agregar empleados")

5.  En la sección **Agregar empleados**, lleve a cabo estos pasos:

    ![Guardar empleados](./media/active-directory-saas-shiftplanning-tutorial/IC786625.png "Guardar empleados")

    1.  Escriba los datos de una cuenta de AAD válida que desee aprovisionar en los cuadros de texto correspondientes: **Nombre**, **Apellido** y **Correo electrónico**.
    2.  Haga clic en **Guardar empleados**.

>[AZURE.NOTE]Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de ShiftPlanning ofrecida por ShiftPlanning para aprovisionar cuentas de usuario de AAD.

##Asignación de usuarios
  
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

###Para asignar usuarios a ShiftPlanning, lleve a cabo los siguientes pasos:

1.  En el portal de Azure AD, cree una cuenta de prueba.

2.  En la página de integración de la aplicación **ShiftPlanning**, haga clic en **Asignar usuarios**.

    ![Asignar usuarios](./media/active-directory-saas-shiftplanning-tutorial/IC786626.png "Asignar usuarios")

3.  Seleccione su usuario de prueba, haga clic en **Asignar** y luego en **Sí** para confirmar la asignación.

    ![Sí](./media/active-directory-saas-shiftplanning-tutorial/IC767830.png "Sí")
  
Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0114_2016-->