<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con Jobscience | Microsoft Azure" 
    description="Aprenda a usar Jobscience con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc." 
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

#Tutorial: Integración de Azure Active Directory con Jobscience
  
El objetivo de este tutorial es mostrar la integración de Azure y Jobscience.  
En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción de Azure válida
-   Una suscripción habilitada para inicio de sesión único en Jobscience
  
Después de completar este tutorial, los usuarios de Azure AD asignados a Jobscience podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de Jobscience (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
La situación descrita en este tutorial consta de los siguientes bloques de creación:

1.  Habilitación de la integración de aplicaciones para Jobscience
2.  Configuración del inicio de sesión único
3.  Configuración del aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario](./media/active-directory-saas-jobscience-tutorial/IC784341.png "Escenario")
##Habilitación de la integración de aplicaciones para Jobscience
  
El objetivo de esta sección es describir cómo habilitar la integración de aplicaciones para Jobscience.

###Siga estos pasos para habilitar la integración de aplicaciones para Jobscience:

1.  En el panel de navegación izquierdo del Portal de administración de Azure, haga clic en **Active Directory**.

    ![Active Directory](./media/active-directory-saas-jobscience-tutorial/IC700993.png "Active Directory")

2.  En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3.  Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

    ![Aplicaciones](./media/active-directory-saas-jobscience-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación](./media/active-directory-saas-jobscience-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Agregar una aplicación de la galería](./media/active-directory-saas-jobscience-tutorial/IC749322.png "Agregar una aplicación de la galería")

6.  En el **cuadro de búsqueda**, escriba **jobscience**.

    ![Galería de aplicaciones](./media/active-directory-saas-jobscience-tutorial/IC784342.png "Galería de aplicaciones")

7.  En el panel de resultados, seleccione **Jobscience** y, a continuación, haga clic en **Completar** para agregar la aplicación.

    ![Jobscience](./media/active-directory-saas-jobscience-tutorial/IC784357.png "Jobscience")
##Configuración del inicio de sesión único
  
El objetivo de esta sección es describir cómo habilitar la autenticación de usuarios en Jobscience con su cuenta de Azure AD mediante federación basada en el protocolo SAML.  
La configuración de un inicio de sesión único para Jobscience requiere la recuperación de un valor de huella digital de un certificado.  
Si no está familiarizado con este procedimiento, consulte [Recuperación del valor de huella digital de un certificado](http://youtu.be/YKQF266SAxI).

###Siga estos pasos para configurar el inicio de sesión único:

1.  Inicie sesión como administrador en el sitio de la compañía de Jobscience.

2.  Acceda a **Configuración**.

    ![Configuración](./media/active-directory-saas-jobscience-tutorial/IC784358.png "Configuración")

3.  En el panel de navegación izquierdo, en la sección **Administrar**, haga clic en **Administración de dominios** para expandir la sección relacionada y, luego, haga clic en la página **Mi dominio** para abrir la página **Mi dominio**.

    ![Mi dominio](./media/active-directory-saas-jobscience-tutorial/IC767825.png "Mi dominio")

4.  Para comprobar que el dominio se ha configurado correctamente, asegúrese de que está en el "**Paso 4 Implementación para usuarios**" y revise "**Mi configuración de dominio**".

    ![Dominio implementado al usuario](./media/active-directory-saas-jobscience-tutorial/IC784377.png "Dominio implementado al usuario")

5.  En una ventana de explorador web diferente, inicie sesión en el Portal de Azure.

6.  En la página de integración de aplicaciones de **Jobscience**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-jobscience-tutorial/IC784360.png "Configurar inicio de sesión único")

7.  En la página **¿Cómo desea que los usuarios inicien sesión en Jobscience?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y, a continuación, haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-jobscience-tutorial/IC784361.png "Configurar inicio de sesión único")

8.  En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto **URL de inicio de sesión de Jobscience**, escriba su dirección URL con el siguiente patrón "*http://company.my.salesforce.com*" y, a continuación, haga clic en **Siguiente**.

    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-jobscience-tutorial/IC784362.png "Configurar dirección URL de la aplicación")

9.  En la página **Configurar inicio de sesión único en Jobscience**, para descargar el certificado, haga clic en **Descargar certificado** y, a continuación, guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-jobscience-tutorial/IC784363.png "Configurar inicio de sesión único")

10. En el sitio de la compañía de Jobscience, haga clic en **Controles de seguridad** y, a continuación, haga clic en **Configuración de inicio de sesión único**.

    ![Controles de seguridad](./media/active-directory-saas-jobscience-tutorial/IC784364.png "Controles de seguridad")

11. En la sección **Configuración del inicio de sesión único**, siga estos pasos:

    ![Configuración de inicio de sesión único](./media/active-directory-saas-jobscience-tutorial/IC781026.png "Configuración de inicio de sesión único")

    1.  Seleccione **SAML habilitado**.
    2.  Haga clic en **Nuevo**.

12. En el cuadro de diálogo **Edición de la configuración de inicio de sesión único de SAML**, realice los pasos siguientes:

    ![Configuración de inicio de sesión único SAML](./media/active-directory-saas-jobscience-tutorial/IC784365.png "Configuración de inicio de sesión único SAML")

    1.  En el cuadro de texto **Nombre**, escriba el nombre de la configuración.
    2.  En el Portal de Azure, en la página de diálogo **Configurar inicio de sesión único en Jobscience**, copie el valor de **URL del emisor** y péguelo en el cuadro de texto **Emisor**.
    3.  En el cuadro de texto **Id. de entidad**, escriba **https://salesforce-jobscience.com**.
    4.  Haga clic en **Examinar** para cargar el certificado de Azure AD.
    5.  Como **Tipo de identidad SAML**, seleccione **La aserción contiene el identificador de la federación del objeto de usuario**.
    6.  Como **Ubicación de identidad SAML**, seleccione **La identidad está en el elemento NameIdentifier de la instrucción Subject**.
    7.  En el Portal de Azure, en la página de diálogo **Configurar inicio de sesión único en Jobscience**, copie el valor de **Dirección URL de inicio de sesión remoto** y péguelo en el cuadro de texto **Dirección URL de inicio de sesión del proveedor de identidades**.
    8.  En el Portal de Azure, en la página de diálogo **Configurar inicio de sesión único en Jobscience**, copie el valor de **Dirección URL de cierre de sesión remoto** y péguelo en el cuadro de texto **Dirección URL de cierre de sesión del proveedor de identidades**.
    9.  Haga clic en **Guardar**.

13. En el panel de navegación izquierdo, en la sección **Administrar** , haga clic en **Administración de dominios** para expandir la sección relacionada y, luego, haga clic en **Mi dominio** para abrir la página **Mi dominio**.

    ![Mi dominio](./media/active-directory-saas-jobscience-tutorial/IC767825.png "Mi dominio")

14. En la página **Mi dominio**, en la sección **Personalización de marca de la página de inicio de sesión**, haga clic en **Editar**.

    ![Personalización de marca de la página de inicio de sesión](./media/active-directory-saas-jobscience-tutorial/IC767826.png "Personalización de marca de la página de inicio de sesión")

15. En la página **Personalización de marca de la página de inicio de sesión**, en la sección **Servicio de autenticación**, se muestra el nombre de su **Configuración de SSO de SAML**. Selecciónelo y luego haga clic en **Guardar**.

    ![Personalización de marca de la página de inicio de sesión](./media/active-directory-saas-jobscience-tutorial/IC784366.png "Personalización de marca de la página de inicio de sesión")

16. En el portal de Azure AD, seleccione la confirmación de configuración de inicio de sesión único y luego haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-jobscience-tutorial/IC784367.png "Configurar inicio de sesión único")
  
Para obtener la dirección URL de inicio de sesión único iniciado por el proveedor de servicios, haga clic en **Configuración de inicio de sesión único** en la sección del menú **Controles de seguridad**.

![Controles de seguridad](./media/active-directory-saas-jobscience-tutorial/IC784368.png "Controles de seguridad")
  
Haga clic en el perfil SSO creado en el paso anterior.  
Esta página muestra la dirección URL de inicio de sesión único de su empresa (por ejemplo, *https://companyname.my.salesforce.com?so=companyid*).
##Configuración del aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD inicien sesión en Jobscience, deben aprovisionarse en Jobscience.  
En el caso de Jobscience, el aprovisionamiento es una tarea manual.

###Siga estos pasos para configurar el aprovisionamiento de usuario:

1.  Inicie sesión como administrador en el sitio de la compañía de **Jobscience**.

2.  Ir a Configuración

    ![Configuración](./media/active-directory-saas-jobscience-tutorial/IC784358.png "Configuración")

3.  Vaya a **Administrar usuarios > Usuarios**.

    ![Usuarios](./media/active-directory-saas-jobscience-tutorial/IC784369.png "Usuarios")

4.  Haga clic en **Nuevo usuario**.

    ![Todos los usuarios](./media/active-directory-saas-jobscience-tutorial/IC784370.png "Todos los usuarios")

5.  En el cuadro de diálogo **Editar usuario**, realice los siguientes pasos:

    ![Edición de usuarios](./media/active-directory-saas-jobscience-tutorial/IC784371.png "Edición de usuarios")

    1.  En los cuadros de texto relacionados, escriba el nombre, los apellidos, el alias, el correo electrónico, las propiedades de nombre y el alias del usuario de Azure AD que desee aprovisionar.
    2.  Haga clic en **Guardar**.

    >[AZURE.NOTE]El titular de la cuenta de Azure AD recibirá un mensaje de correo electrónico que incluye un vínculo para confirmar la cuenta antes de que se active.

>[AZURE.NOTE]Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Jobscience ofrecida por Jobscience para aprovisionar cuentas de usuario de AAD.

##Asignación de usuarios
  
Para probar la configuración, debe asignar los usuarios de Azure AD que quiera que usen su aplicación para concederles acceso a ella.

###Para asignar usuarios a Jobscience, siga estos pasos:

1.  En el Portal de Azure AD, cree una cuenta de prueba.

2.  En la página de integración de aplicaciones de **Jobscience**, haga clic en **Asignar usuarios**.

    ![Asignar usuarios](./media/active-directory-saas-jobscience-tutorial/IC784372.png "Asignar usuarios")

3.  Seleccione su usuario de prueba, haga clic en **Asignar** y luego en **Sí** para confirmar la asignación.

    ![Sí](./media/active-directory-saas-jobscience-tutorial/IC767830.png "Sí")
  
Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0121_2016-->

