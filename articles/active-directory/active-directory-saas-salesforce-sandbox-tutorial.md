<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con Salesforce Sandbox | Microsoft Azure" description="Aprenda cómo usar Salesforce Sandbox con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc." 
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
    ms.date="12/18/2015" 
    ms.author="jeedes" />


#Tutorial: Integración de Azure Active Directory con Salesforce Sandbox
>[AZURE.TIP]Para enviar comentarios, haga clic [aquí](http://go.microsoft.com/fwlink/?LinkId=521878).
  
El objetivo de este tutorial es mostrar la integración de Azure y Salesforce Sandbox.  
Los espacios aislados ofrecen la capacidad de crear varias copias de su organización en entornos independientes para una variedad de propósitos, como desarrollo, pruebas y aprendizaje, sin poner en peligro los datos y las aplicaciones de la organización de producción de Salesforce.  
Para obtener más información, vea [Información general del espacio aislado](https://help.salesforce.com/HTViewHelpDoc?id=create_test_instance.htm&language=en_US).
  
En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción de Azure válida
-   Un espacio aislado en Salesforce.com
  
Si aún no tiene un espacio aislado válido en Salesforce.com, deberá ponerse en contacto con Salesforce.
  
La situación descrita en este tutorial consta de los siguientes bloques de creación:

1.  Habilitación de la integración de aplicaciones para Salesforce Sandbox
2.  Configuración del inicio de sesión único
3.  Habilitación de su dominio
4.  Configuración del aprovisionamiento de usuario
5.  Asignación de usuarios

![Escenario](./media/active-directory-saas-salesforce-sandbox-tutorial/IC769571.png "Escenario")
##Habilitación de la integración de aplicaciones para Salesforce Sandbox
  
El objetivo de esta sección es describir cómo habilitar la integración de las aplicaciones para el espacio aislado de Salesforce.

###Siga estos pasos para habilitar la integración de aplicaciones para el espacio aislado de Salesforce:

1.  En el panel de navegación izquierdo del Portal de administración de Azure, haga clic en **Active Directory**.

    ![Active Directory](./media/active-directory-saas-salesforce-sandbox-tutorial/IC700993.png "Active Directory")

2.  En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3.  Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

    ![Aplicaciones](./media/active-directory-saas-salesforce-sandbox-tutorial/IC700994.png "Aplicaciones")

4.  Para abrir la **Galería de aplicaciones**, haga clic en **Agregar una aplicación** y luego en **Agregar una aplicación que mi organización use**.

    ![¿Qué desea hacer?](./media/active-directory-saas-salesforce-sandbox-tutorial/IC700995.png "¿Qué desea hacer?")

5.  En el **cuadro de búsqueda**, escriba **Salesforce Sandbox**.

    ![Galería de aplicaciones](./media/active-directory-saas-salesforce-sandbox-tutorial/IC710978.png "Galería de aplicaciones")

6.  En el panel de resultados, seleccione **Salesforce Sandbox** y, luego, haga clic en **Completa** para agregar la aplicación.

    ![Salesforce Sandbox](./media/active-directory-saas-salesforce-sandbox-tutorial/IC746474.png "Salesforce Sandbox")
##Configuración del inicio de sesión único
  
El objetivo de esta sección es describir cómo habilitar usuarios para que se autentiquen en Salesforce con su cuenta de Azure AD a través de la federación basada en el protocolo SAML.

###Siga estos pasos para configurar el inicio de sesión único:

1.  En el portal de Azure AD, en la página de integración de aplicaciones de **Salesforce Sandbox**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-salesforce-sandbox-tutorial/IC749323.png "Configurar inicio de sesión único")

2.  En la página **¿Cómo desea que los usuarios inicien sesión en Salesforce Sandbox?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y luego haga clic en **Siguiente**.

    ![Salesforce Sandbox](./media/active-directory-saas-salesforce-sandbox-tutorial/IC746479.png "Salesforce Sandbox")

3.  En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto **URL de inicio de sesión**, escriba su dirección URL con el siguiente patrón `http://company.my.salesforce.com` y, a continuación, haga clic en **Siguiente**.

    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781022.png "Configurar dirección URL de la aplicación")

4. Si ya ha configurado un inicio de sesión único para otra instancia de Salesforce Sandbox en su directorio, también debe configurar la **Identificador** para que tenga el mismo valor que la **URL de inicio de sesión**. El campo **Identificador** puede encontrarse al activar la casilla **Mostrar la configuración avanzada** en la página **Configurar dirección URL de la aplicación** del cuadro de diálogo.

4.  En la página **Configurar inicio de sesión único en Salesforce Sandbox**, haga clic en **Descargar certificado** y después guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781023.png "Configurar inicio de sesión único")

5.  En otra ventana del explorador web, inicie sesión en su sitio de la compañía del espacio aislado de Salesforce como administrador.

6.  En el menú de la parte superior, haga clic en **Configuración**.

    ![Configuración](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781024.png "Configuración")

7.  En el panel de navegación de la izquierda, haga clic en **Controles de seguridad**y luego haga clic en **Configuración de inicio de sesión único (Single Sign on)**.

    ![Configuración de inicio de sesión único](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781025.png "Configuración de inicio de sesión único")

8.  En la sección Configuración del inicio de sesión único, siga estos pasos:

    ![Configuración de inicio de sesión único](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781026.png "Configuración de inicio de sesión único")

    1.  Seleccione **SAML habilitado**.
    2.  Haga clic en **Nuevo**.

9.  En la sección Configuración del inicio de sesión único de SAML siga estos pasos:

    ![Configuración de inicio de sesión único SAML](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781027.png "Configuración de inicio de sesión único SAML")

    1.  En el cuadro de texto Nombre, escriba el nombre de la configuración (por ejemplo, *SPSSOWAAD\_Test*).
    2.  En el portal de Azure, en la página de diálogo **Configurar inicio de sesión único en Salesforce Sandbox**, copie el valor de **URL del emisor** y péguelo en el cuadro de texto **Emisor**.
    3.  En el cuadro de texto **Id. de entidad**, escriba **https://test.salesforce.com** si se trata de la primera instancia de Salesforce Sandbox que va a agregar a su directorio. Si ya ha agregado una instancia de Salesforce Sandbox, para el **Id. de entidad** escriba la **URL de inicio de sesión**, que debe tener este formato: `http://company.my.salesforce.com`
    4.  Haga clic en **Examinar** para cargar el certificado descargado.
    5.  Como **Tipo de identidad SAML**, seleccione **La aserción contiene el identificador de la federación del objeto de usuario**.
    6.  Como **Ubicación de identidad de SAML**, seleccione **Identidad está en el elemento NameIdentifier de la instrucción de sujeto**.
    7.  En el Portal de Azure, en la página de diálogo **Configurar inicio de sesión único en Salesforce Sandbox**, copie el valor de **Dirección URL de inicio de sesión remoto** y péguelo en el cuadro de texto **Dirección URL de inicio de sesión de proveedor de identidades**.
    8.  En el Portal de Azure, en la página de diálogo **Configurar inicio de sesión único en Salesforce Sandbox**, copie el valor de **Dirección URL de cierre de sesión remoto** y péguelo en el cuadro de texto **Dirección URL de cierre de sesión de proveedor de identidades**.
    9.  Como **Vinculación de solicitud iniciada del proveedor de servicios**, seleccione **HTTP Post**.
    10. Haga clic en **Guardar**.

10. En el Portal de Azure AD, seleccione la confirmación de configuración de inicio de sesión único y, a continuación, haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781028.png "Configurar inicio de sesión único")

##Habilitación de su dominio
  
En esta sección se supone que ya ha creado un dominio.  
Para obtener más información, consulte [Definición de su nombre de dominio](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US).

###Realice los pasos siguientes para habilitar su dominio:

1.  En el panel de navegación izquierdo, haga clic en **Administración de dominios**y luego en **Mi dominio**.

    ![Mi dominio](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781029.png "Mi dominio")

    >[AZURE.NOTE]Asegúrese de que su dominio se ha configurado correctamente.

2.  En la sección **Configuración de la página de inicio de sesión**, haga clic en**Editar**, luego, como **Servicio de autenticación**, seleccione el nombre de la configuración de inicio de sesión único de SAML en la sección anterior y finalmente haga clic en **Guardar**.

    ![Mi dominio](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781030.png "Mi dominio")
  
Tan pronto como tenga un dominio configurado, los usuarios deberán usar la dirección URL de dominio para iniciar sesión en el espacio aislado de Salesforce. Para obtener el valor de la dirección URL, haga clic en el perfil de SSO que haya creado en la sección anterior.
##Configuración del aprovisionamiento de usuario
  
El objetivo de esta sección es describir cómo habilitar el aprovisionamiento de cuentas de usuario de Active Directory para Salesforce Sandbox.

###Siga estos pasos para configurar el aprovisionamiento de usuario:

1.  En el portal de Salesforce, en la barra de navegación superior, seleccione el nombre para expandir su menú de usuario:

    ![Mi configuración](./media/active-directory-saas-salesforce-sandbox-tutorial/IC698773.png "Mi configuración")

2.  En el menú del usuario, seleccione **Mi configuración** para abrir su página **Mi configuración**.

3.  En el panel izquierdo, haga clic en **Personal** para expandir la sección relacionada y luego haga clic en **Restablecer mi token de seguridad**:

    ![Mi configuración](./media/active-directory-saas-salesforce-sandbox-tutorial/IC698774.png "Mi configuración")

4.  En la página **Restablecer mi token de seguridad**, haga clic en **Restablecer token de seguridad** para solicitar un mensaje de correo electrónico que contenga el token de seguridad de Salesforce.com.

    ![Nuevo token](./media/active-directory-saas-salesforce-sandbox-tutorial/IC698776.png "Nuevo token")

5.  Compruebe en su bandeja de entrada de correo electrónico si le ha llegado un correo electrónico de Salesforce.com con el asunto "**salesforce.com.com security confirmation**" (confirmación de seguridad de salesforce.com.com).

6.  Revise este mensaje de correo electrónico y copie el valor del token de seguridad.

7.  En el Portal de administración de Azure, en la página de integración de aplicaciones de **Salesforce Sandbox**, haga clic en **Configurar aprovisionamiento de usuarios** para abrir el cuadro de diálogo de **Configurar aprovisionamiento de usuarios**.

    ![Configuración de aprovisionamiento de usuario](./media/active-directory-saas-salesforce-sandbox-tutorial/IC769573.png "Configuración de aprovisionamiento de usuario")

8.  En la página **Especifique sus credenciales de Salesforce Sandbox para habilitar el aprovisionamiento automático de usuarios**, proporcione los valores de configuración siguientes:

    ![Salesforce Sandbox](./media/active-directory-saas-salesforce-sandbox-tutorial/IC746476.png "Salesforce Sandbox")

    1.  En el **Nombre de usuario de administrador de Salesforce Sandbox**, escriba un nombre de cuenta de espacio aislado de Salesforce que tenga el perfil **Administrador del sistema** asignado en Salesforce.com.

    2.  En el cuadro de texto **Contraseña de administrador de Salesforce Sandbox**, escriba la contraseña para esta cuenta.

    3.  En el cuadro de texto **Token de seguridad del usuario**, pegue el valor del token de seguridad.

    4.  Haga clic en **Validar** para comprobar la configuración.

    5.  Haga clic en el botón **Siguiente** para abrir la página **Confirmación**.

9.  En la página **Confirmación**, haga clic en **Completar** para guardar la configuración.
##Asignación de usuarios
  
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

###Para asignar usuarios a Salesforce Sandbox, lleve a cabo los siguientes pasos:

1.  En el portal de Azure AD, cree una cuenta de prueba.

2.  En la página de integración de aplicaciones **Salesforce Sandbox **, haga clic en **Asignar usuarios**.

    ![Asignar usuarios](./media/active-directory-saas-salesforce-sandbox-tutorial/IC769574.png "Asignar usuarios")

3.  Seleccione su usuario de prueba, haga clic en **Asignar** y, a continuación, en **Sí** para confirmar la asignación.

    ![Sí](./media/active-directory-saas-salesforce-sandbox-tutorial/IC767830.png "Sí")
  
Ahora debería esperar 10 minutos y comprobar si la cuenta se ha sincronizado en Salesforce Sandbox.
  
Si quiere probar su configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=AcomDC_1223_2015-->