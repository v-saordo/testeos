<properties 
    pageTitle="Tutorial: integración de Azure Active Directory con Samanage | Microsoft Azure" 
    description="Aprenda cómo usar Samanage con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc." 
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

#Tutorial: Integración de Azure Active Directory con Samanage
  
El objetivo de este tutorial es mostrar la integración de Azure y Samanage. En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción de Azure válida
-   Un inquilino de Samanage
  
Después de completar este tutorial, los usuarios de Azure AD que ha asignado a Samanage podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de Samanage (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
La situación descrita en este tutorial consta de los siguientes bloques de creación:

1.  Habilitación de la integración de aplicaciones en Samanage
2.  Configuración del inicio de sesión único
3.  Configuración del aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario](./media/active-directory-saas-samanage-tutorial/IC771705.png "Escenario")
##Habilitación de la integración de aplicaciones en Samanage
  
El objetivo de esta sección es describir cómo se habilita la integración de aplicaciones en Samanage.

###Siga estos pasos para habilitar la integración de aplicaciones en Samanage:

1.  En el panel de navegación izquierdo del Portal de administración de Azure, haga clic en **Active Directory**.

    ![Active Directory](./media/active-directory-saas-samanage-tutorial/IC700993.png "Active Directory")

2.  En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3.  Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

    ![Aplicaciones](./media/active-directory-saas-samanage-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación](./media/active-directory-saas-samanage-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Agregar una aplicación de la galería](./media/active-directory-saas-samanage-tutorial/IC749322.png "Agregar una aplicación de la galería")

6.  En el **cuadro de búsqueda**, escriba **Samanage**.

    ![Galería de aplicaciones](./media/active-directory-saas-samanage-tutorial/IC771707.png "Galería de aplicaciones")

7.  En el panel de resultados, seleccione **Samanage** y luego haga clic en **Completar** para agregar la aplicación.

    ![Samanage](./media/active-directory-saas-samanage-tutorial/IC771708.png "Samanage")
##Configuración del inicio de sesión único
  
El objetivo de esta sección es describir cómo habilitar usuarios para que se autentiquen en Samanage con su cuenta de Azure AD a través de la federación basada en el protocolo SAML.  
Como parte de este procedimiento, es necesario crear un archivo de certificado codificado en base 64.  
Si no está familiarizado con este procedimiento, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

###Siga estos pasos para configurar el inicio de sesión único:

1.  En el Portal de Azure AD, en la página de integración de aplicaciones de **Samanage**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-samanage-tutorial/IC771709.png "Configurar inicio de sesión único")

2.  En la página **¿Cómo desea que los usuarios inicien sesión en Samanage?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y luego haga clic en **Siguiente**.

    ![Inicio de sesión único de Microsoft Azure AD](./media/active-directory-saas-samanage-tutorial/IC771710.png "Inicio de sesión único de Microsoft Azure AD")

3.  En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto de **URL de inicio de sesión de Samanage**, escriba su dirección URL con el siguiente patrón "*https://\<nombre-inquilino>.samanage.com*" y luego haga clic en **Siguiente**.

    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-samanage-tutorial/IC771711.png "Configurar dirección URL de la aplicación")

4.  En la página **Configuración de inicio de sesión único en Samanage**, haga clic en **Descargar certificado** y luego guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-samanage-tutorial/IC777613.png "Configurar inicio de sesión único")

5.  En otra ventana del explorador web, inicie sesión en su sitio de la compañía de Samanage como administrador.

6.  Haga clic en **Panel** y seleccione **Configuración** en el panel de navegación izquierdo.

    ![Panel](./media/active-directory-saas-samanage-tutorial/IC771712.png "Panel")

7.  Haga clic en **Inicio de sesión único**.

    ![Inicio de sesión único](./media/active-directory-saas-samanage-tutorial/IC771713.png "Inicio de sesión único")

8.  En la página del cuadro de diálogo **Inicio de sesión mediante SAML**, realice los pasos siguientes y luego haga clic en **Guardar cambios**:

    1.  Haga clic en **Habilitar el inicio de sesión único con SAML**. 
        ![Inicio de sesión mediante SAML](./media/active-directory-saas-samanage-tutorial/IC771719.png "Inicio de sesión mediante SAML")
    2.  En el portal de Azure, en la página del cuadro de diálogo **Configurar inicio de sesión único en Samanage**, copie el valor del **Id. de proveedor de identidades** y luego péguelo en el cuadro de texto **URL del proveedor de identidades. 
        ![Configurar inicio de sesión único](./media/active-directory-saas-samanage-tutorial/IC771720.png "Configurar inicio de sesión único")
    3.  En el portal de Azure, en la página de diálogo **Configurar inicio de sesión único en Samanage**, copie el valor de **Dirección URL del inicio de sesión remoto** y péguelo en el cuadro de texto **Dirección URL de inicio de sesión**.
    4.  En el portal de Azure, en la página de diálogo **Configurar inicio de sesión único en Samanage**, copie el valor de **Dirección URL del cierre de sesión remoto** y péguelo en el cuadro de texto **Dirección URL de cierre de sesión**.
    5.  Cree un archivo **codificado en base 64** a partir del certificado descargado.  

        >[AZURE.TIP]Para obtener más información, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

    6.  Abra el certificado codificado en base 64 en el Bloc de notas, copie el contenido del mismo en el Portapapeles y, a continuación, péguelo en el cuadro de texto **Certificado X.509**.
    7.  Haga clic en **Crear usuarios si no existen en Samanage**. 
        ![Actualizar](./media/active-directory-saas-samanage-tutorial/IC771722.png "Actualizar")
    8.  Haga clic en **Actualizar**.

9.  En el portal de Azure AD, seleccione la confirmación de configuración de inicio de sesión único y luego haga clic en **Completa** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-samanage-tutorial/IC771723.png "Configurar inicio de sesión único")
##Configuración del aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD inicien sesión en Samanage, deben aprovisionarse en Samanage. En el caso de Samanage, el aprovisionamiento es una tarea manual.

###Para aprovisionar cuentas de usuario, realice los siguientes pasos:

1.  Inicie sesión en su inquilino de **Samanage**.

2.  Vaya a **Panel > Configuración**.

    ![Configuración](./media/active-directory-saas-samanage-tutorial/IC771724.png "Configuración")

3.  Haga clic en la pestaña **Usuarios**.

    ![Usuarios](./media/active-directory-saas-samanage-tutorial/IC771725.png "Usuarios")

4.  Haga clic en **Nuevo usuario**.

    ![Nuevo usuario](./media/active-directory-saas-samanage-tutorial/IC771726.png "Nuevo usuario")

5.  Escriba la **Dirección de correo electrónico** y el **Nombre** de una cuenta de Azure AD existente que quiera aprovisionar y luego haga clic en **Crear usuario**.

    >[AZURE.NOTE]El titular de la cuenta de AAD recibirá un mensaje de correo y seguirá un vínculo para confirmar su cuenta para que se active.

    ![Crear usuario](./media/active-directory-saas-samanage-tutorial/IC771727.png "Crear usuario")

>[AZURE.NOTE]Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Samanage que proporcione Samanage para aprovisionar cuentas de usuario de AAD.

##Asignación de usuarios
  
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

###Para asignar usuarios a Samanage, lleve a cabo los siguientes pasos:

1.  En el portal de Azure AD, cree una cuenta de prueba.

2.  En la página de integración de aplicaciones **Samanage **, haga clic en **Asignar usuarios**.

    ![Asignar usuarios](./media/active-directory-saas-samanage-tutorial/IC771728.png "Asignar usuarios")

3.  Seleccione su usuario de prueba, haga clic en **Asignar** y luego en **Sí** para confirmar la asignación.

    ![Sí](./media/active-directory-saas-samanage-tutorial/IC767830.png "Sí")
  
Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0114_2016-->