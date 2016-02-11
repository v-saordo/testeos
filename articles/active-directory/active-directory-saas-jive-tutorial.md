<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con Jive | Microsoft Azure" 
    description="Aprenda a usar Jive con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc." 
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

#Tutorial: Integración de Azure Active Directory con Jive

  
El objetivo de este tutorial es mostrar la integración de Azure y Jive. En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción de Azure válida
-   Un inquilino en Jive
  
La situación descrita en este tutorial consta de los siguientes bloques de creación:

1.  Habilitación de la integración de aplicaciones para Jive
2.  Configuración del aprovisionamiento de usuario

##Habilitación de la integración de aplicaciones para Jive
  
El objetivo de esta sección es describir cómo se habilita la integración de aplicaciones para Jive.

###Siga estos pasos para habilitar la integración de aplicaciones para Jive:

1.  En el panel de navegación izquierdo del Portal de administración de Azure, haga clic en **Active Directory**.

    ![Active Directory](./media/active-directory-saas-jive-tutorial/IC700993.png "Active Directory")

2.  En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3.  Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

    ![Aplicaciones](./media/active-directory-saas-jive-tutorial/IC700994.png "Aplicaciones")

4.  Para abrir la **Galería de aplicaciones**, haga clic en **Agregar una aplicación** y luego en **Agregar una aplicación que mi organización use**.

    ![¿Qué desea hacer?](./media/active-directory-saas-jive-tutorial/IC700995.png "¿Qué desea hacer?")

5.  En el **cuadro de búsqueda**, escriba **Jive**.

    ![Jive](./media/active-directory-saas-jive-tutorial/IC701001.png "Jive")

6.  En el panel de resultados, seleccione **Jive** y luego haga clic en **Completar** para agregar la aplicación.

    ![Jive](./media/active-directory-saas-jive-tutorial/IC701005.png "Jive")
##Configuración del aprovisionamiento de usuario
  
El objetivo de esta sección es describir cómo habilitar el aprovisionamiento de cuentas de usuario de Active Directory para Jive. Como parte de este procedimiento, es necesario proporcionar un token de seguridad de usuario que deberá solicitar a Jive.com.
  
La captura de pantalla siguiente muestra un ejemplo del cuadro de diálogo relacionado en Azure AD:

![Configuración del aprovisionamiento de usuarios](./media/active-directory-saas-jive-tutorial/IC698794.png "Configuración del aprovisionamiento de usuarios")

###Siga estos pasos para configurar el aprovisionamiento de usuario:

1.  En el Portal de administración de Azure, en la página de integración de aplicaciones de **Jive**, haga clic en **Configurar aprovisionamiento de usuarios** para abrir el cuadro de diálogo de **Configurar aprovisionamiento de usuarios**.

2.  En la página **Especifique sus credenciales de Jive para habilitar el aprovisionamiento automático de usuarios**, proporcione los valores de configuración siguientes:

    1.  En el cuadro de texto **Nombre de usuario del administrador de Jive**, escriba un nombre de cuenta de Jive que tenga asignado el perfil **Administrador del sistema** en Jive.com.

    2.  En el cuadro de texto **Contraseña de administrador de Jive**, escriba la contraseña para esta cuenta.

    3.  En el cuadro de texto **Dirección URL de inquilino de Jive**, escriba la dirección URL del inquilino de Jive.

        >[AZURE.NOTE]La dirección URL del inquilino de Jive es la que se usa en su organización para iniciar sesión en Jive. Normalmente, la dirección URL tiene el formato siguiente: **www.<organización>.jive.com**.

    4.  Haga clic en **Validar** para comprobar la configuración.

    5.  Haga clic en el botón **Siguiente** para abrir la página **Confirmación**.

3.  En la página **Confirmación**, haga clic en la marca de verificación para guardar la configuración.
  
Ahora ya puede crear una cuenta de prueba, espere 10 minutos y compruebe si la cuenta se ha sincronizado en Jive.com.

<!---HONumber=AcomDC_0121_2016-->