<properties
   pageTitle="Tutorial: Integración de Salesforce con Azure Active Directory | Microsoft Azure"
   description="Aprenda cómo usar Salesforce con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc."
   services="active-directory"
   documentationCenter=""
   authors="liviodlc"
   manager="TerryLanfear"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/20/2015"
   ms.author="liviodlc"/>

#Tutorial: Integración de Salesforce con Azure Active Directory

Este tutorial le mostrará cómo conectar el entorno de Salesforece a Azure Active Directory. Obtendrá información sobre cómo configurar un inicio de sesión único en Salesforce, cómo habilitar el aprovisionamiento automático de usuarios y cómo asignar usuarios para que dispongan de acceso a Salesforce.

##Requisitos previos

1. Para obtener acceso a Azure Active Directory a través del [Portal de administración de Azure](https://manage.windowsazure.com), primero debe tener una suscripción de Azure válida.

2. Debe tener un inquilino válido en [Salesforce.com](https://www.salesforce.com/).

> [AZURE.IMPORTANT]Si utiliza una cuenta de**prueba** de Salesforce.com, no podrá configurar el aprovisionamiento automático de usuarios. Las cuentas de prueba no disponen del acceso necesario a la API habilitado hasta que se adquieren.
> 
> Puede evitar limitación mediante una [cuenta libre de desarrollador](https://developer.salesforce.com/signup) para completar este tutorial.

Si está utilizando un entorno de espacio aislado de Salesforce, consulte el [Tutorial de integración de espacio aislado de Salesforce](https://go.microsoft.com/fwLink/?LinkID=521879).

##Tutoriales de vídeo

Puede seguir este tutorial con los vídeos a continuación.

**Tutorial de vídeo parte 1: Habilitación de inicio de sesión único**

> [AZURE.VIDEO integrating-salesforce-with-azure-ad-how-to-enable-single-sign-on]

**Tutorial de vídeo parte dos: Automatización del aprovisionamiento de usuarios**

> [AZURE.VIDEO integrating-salesforce-with-azure-ad-how-to-automate-user-provisioning]

##Paso 1: Adición de Salesforce al directorio

1. En el panel de navegación izquierdo del [Portal de administración de Azure](https://manage.windowsazure.com), haga clic en **Active Directory**.

	![Seleccione Active Directory en el panel de navegación izquierdo.][0]

2. En la lista **Directorio**, seleccione el directorio que le gustaría agregar a Salesforce.

3. Haga clic en **Aplicaciones** en el menú superior.

	![Haga clic en Aplicaciones.][1]

4. Haga clic en **Agregar** en la parte inferior de la página.

	![Haga clic en Agregar para agregar una nueva aplicación.][2]

5. En el cuadro de diálogo **Qué desea hacer**, haga clic en **Agregar una aplicación de la galería**.

	![Haga clic en Agregar una aplicación de la galería.][3]

6. En el **cuadro de búsqueda**, escriba **Salesforce**. A continuación, seleccione **Salesforce** en los resultados y haga clic en **Completar** para agregar la aplicación..

	![Agregue Salesforce.][4]

7. Ahora debería ver la página de inicio rápido para Salesforce:

	![Página de inicio rápido de Salesforce en Azure AD][5]

##Paso 2: Habilitación del inicio de sesión único

1. Para poder configurar el inicio de sesión único, debe configurar e implementar un dominio personalizado para su entorno de Salesforce. Para obtener instrucciones sobre cómo hacerlo, consulte [Configuración de un nombre de dominio](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_setup.htm&language=en_US).

2. En la página de inicio rápido de Salesforce en Azure AD, haga clic e el botón **Configurar inicio de sesión único**.

	![Botón de inicio de sesión único de configuración][6]

3. Se abrirá un cuadro de diálogo y verá una pantalla que le pregunta "¿Cómo desea que los usuarios inicien sesión Salesforce?". Seleccione **Inicio de sesión único de Azure AD** y luego haga clic en **Siguiente**.

	![Selección del inicio de sesión único e Azure AD][7]

	> [AZURE.NOTE]Para conocer más acerca de los diferentes opciones de inicio de sesión único, [haga clic aquí](../active-directory-appssoaccess-whatis/#how-does-single-sign-on-with-azure-active-directory-work).

4. En la página **Configurar opciones de aplicación**, complete la **dirección URL de inicio de sesión** escribiendo la dirección URL de dominio de Salesforce con el formato siguiente:
 - Cuenta de empresa: `https://<domain>.my.salesforce.com`
 - Cuenta de desarrollador: `https://<domain>-dev-ed.my.salesforce.com` 

	![Inserción de la dirección URL de inicio de sesión][8]

5. En la página **Configuración de inicio de sesión único en Salesforce**, haga clic en **Descargar certificado** y, a continuación, guarde el archivo de certificado localmente en el equipo.

	![Descargar certificado][9]

6. Abra una nueva pestaña en el explorador e inicie sesión en su cuenta de administrador de Salesforce.

7. En el panel de navegación del **administrador**, haga clic en **Controles de seguridad** para expandir la sección relacionada. A continuación, haga clic en **Configuración de inicio de sesión único**.

	![Haga clic en Configuración de inicio de sesión único en Controles de seguridad][10]

8. En la página **Configuración de inicio de sesión único**, haga clic en e botón **Editar**.

	![Haga clic en el botón Editar][11]

	> [AZURE.NOTE]Si no puede habilitar la configuración de inicio de sesión único para su cuenta de Salesforce, puede que necesite ponerse en contacto con el soporte técnico de Salesforce para habilitar la característica.

9. Seleccione **SAML habilitado** y, a continuación, haga clic en **Guardar**.

	![Seleccione SAML habilitado][12]

10. Para establecer la configuración de inicio de sesión único de SAML, haga clic en **Nuevo**.

	![Seleccione SAML habilitado][13]

11. En la página **Edición de la configuración de inicio de sesión único de SAML**, realice las siguientes configuraciones:

	![Captura de pantalla de las configuraciones que debe realizar][14]

 - En el campo **Nombre**, escriba un nombre descriptivo para esta configuración. Si se proporciona un valor para **Nombre**, se completa automáticamente la casilla de texto **Nombre de API**.

 - En Azure AD, copie el valor **URL del emisor** y, a continuación, péguelo en el campo **Emisor** en Salesforce.

 - En el **Cuadro de texto de identificador de entidades**, escriba el nombre de dominio de Salesforce con el siguiente patrón:
     - Cuenta de empresa: `https://<domain>.my.salesforce.com`
     - Cuenta de desarrollador: `https://<domain>-dev-ed.my.salesforce.com`

 - Haga clic en **Examinar** o **Elegir archivo** para abrir el cuadro de diálogo **Elegir archivos para cargar**, seleccione el certificado de Salesforce y, a continuación, haga clic en **Abrir** para cargar el certificado.

 - Para **Tipo de identidad de SAML**, seleccione**La aserción contiene el nombre de usuario de salesforce.com del usuario**.

 - Para **Ubicación de identidad de SAML**, seleccione **Identidad está en el elemento NameIdentifier de la instrucción de sujeto**.

 - En Azure AD, copie el valor **Dirección URL de inicio de sesión remoto** y, a continuación, péguelo en el campo **Dirección URL de inicio de sesión de proveedor de identidades** en Salesforce.

 - Para **Vinculación de solicitud iniciada del proveedor de servicios**, seleccione **Redirección HTTP**.

 - Por último, haga clic en **Guardar** para aplicar la configuración de inicio de sesión único de SAML.

12. En el panel de navegación izquierdo de Salesforce, haga clic en **Administración de dominios** para expandir la sección relacionada y, a continuación, haga clic en **Mi dominio**.

	![Haga clic en Mi dominio][15]

13. Desplácese hacia abajo hasta la sección **Configuración de autenticación** y, a continuación, haga clic en el botón **Editar**.

	![Haga clic en el botón Editar][16]

14. En la sección **Servicio de autenticación**, seleccione el nombre descriptivo de la configuración de SSO de SAML y, a continuación, haga clic en **Guardar**.

	![Seleccione la configuración de SSO][17]

	> [AZURE.NOTE]Si se selecciona más de un servicio de autenticación, cuando los usuarios intentan realizar un inicio de sesión único para el entorno Salesforce, se le solicitará que seleccione el servicio de autenticación con el que les gustaría iniciar sesión. Si no desea que esto ocurra, **deje sin activar todos los servicios de autenticación**.

15. En Azure AD, seleccione la casilla de confirmación de configuración de inicio de sesión único para habilitar el certificado que cargó en Salesforce. A continuación, haga clic en **Siguiente**.

	![Activación de la casilla de confirmación][18]

16. En la última página del cuadro de diálogo, escriba una dirección de correo electrónico si desea recibir notificaciones de correo electrónico de errores y advertencias relacionadas con el mantenimiento de esta configuración de inicio de sesión único.

	![Escriba la dirección de correo electrónico.][19]

17. Haga clic en **Completar** para cerrar el cuadro de diálogo. Para probar la configuración, vea la sección siguiente titulada [Asignación de usuarios a Salesforce](#step-4-assign-users-to-salesforce).

##Paso 3: Habilitación del aprovisionamiento automático de usuarios

1. En la página de inicio rápido de Azure AD para Salesforce, haga clic en el botón **Configurar aprovisionamiento de usuario**.

	![Haga clic en el botón Configurar aprovisionamiento de usuario][20]

2. En el cuadro de diálogo **Configurar aprovisionamiento de usuario**, escriba el nombre de usuario de administrador de Salesforce y la contraseña.

	![Escriba el nombre de usuario de administrador o contraseña][21]

	> [AZURE.NOTE]Si va a configurar un entorno de producción, la práctica recomendada es crear una nueva cuenta de administrador en Salesforce específicamente para este paso. Esta cuenta debe tener el perfil **Administrador del sistema** asignado en Salesforce.

3. Para obtener el token de seguridad de Salesforce, abra una nueva pestaña e inicie sesión en la misma cuenta de administrador de Salesforce. En la esquina superior derecha de la página, haga clic en su nombre y, a continuación, haga clic en **Mi configuración**.

	![Haga clic en el nombre y, a continuación, haga clic en Mi configuración][22]

4. En el panel de navegación izquierdo, haga clic en **Personal** para expandir la sección relacionada y, a continuación, haga clic en **Restablecer mi token de seguridad**.

	![Haga clic en el nombre y, a continuación, haga clic en Mi configuración][23]

5. En la página **Restablecimiento de mi token de seguridad**, haga clic en el botón **Restablecer token de seguridad**.

	![Lea las advertencias.][24]

6. Compruebe la bandeja de entrada de correo electrónico asociada a esta cuenta de administrador. Busque un correo electrónico de Salesforce.com que contenga el nuevo token de seguridad.

7. Copie el token, vaya a la ventana de Azure AD y péguelo en el campo **Token de seguridad de usuario**. A continuación, haga clic en **Siguiente**.

	![Pegue en el token de seguridad][25]

8. En la página confirmación, puede elegir recibir notificaciones por correo electrónico cuando se producen errores de aprovisionamiento. Haga clic en **Completar** para cerrar el cuadro de diálogo.

	![Escriba la dirección de correo electrónico para recibir notificaciones][26]

##Paso 4: Asignación de usuarios a Salesforce

1. Para probar la configuración, empiece a crear una nueva cuenta de prueba en el directorio.

2. En la página de inicio rápido de Salesforce, haga clic en el botón **Asignar usuarios**.

	![Haga clic en Asignar usuarios][27]

3. Seleccione el usuario de prueba y haga clic en el botón **Asignar** situado en la parte inferior de la pantalla:

 - Si no lo ha habilitado el aprovisionamiento automático de usuarios, verá el mensaje siguiente de confirmación:

		![Confirm the assignment.][28]

 - Si ha habilitado el aprovisionamiento automático de usuarios, verá un símbolo del sistema para definir qué tipo de perfil de Salesforce debe tener el usuario. Los usuarios recién aprovisionados deberían aparecer en su entorno Salesforce después de unos minutos.

		![Confirm the assignment.][29]

		> [AZURE.IMPORTANT] Si está aprovisionando un entorno de **desarrolladores** de Salesforce, dispondrá de un número muy limitado de licencias disponibles para cada perfil. Por tanto, es mejor aprovisionar usuarios al perfil **Usuario Chatter Free**, que tiene 4.999 licencias disponibles.

4. Para probar la configuración de inicio de sesión única, abra el Panel de acceso en[https://myapps.microsoft.com](https://myapps.microsoft.com/) y, a continuación, inicie sesión en la cuenta de prueba y haga clic en **Salesforce**.

[AZURE.INCLUDE [saas-toc](../../includes/active-directory-saas-toc.md)]

[0]: ./media/active-directory-saas-salesforce-tutorial/azure-active-directory.png
[1]: ./media/active-directory-saas-salesforce-tutorial/applications-tab.png
[2]: ./media/active-directory-saas-salesforce-tutorial/add-app.png
[3]: ./media/active-directory-saas-salesforce-tutorial/add-app-gallery.png
[4]: ./media/active-directory-saas-salesforce-tutorial/add-salesforce.png
[5]: ./media/active-directory-saas-salesforce-tutorial/salesforce-added.png
[6]: ./media/active-directory-saas-salesforce-tutorial/config-sso.png
[7]: ./media/active-directory-saas-salesforce-tutorial/select-azure-ad-sso.png
[8]: ./media/active-directory-saas-salesforce-tutorial/config-app-settings.png
[9]: ./media/active-directory-saas-salesforce-tutorial/download-certificate.png
[10]: ./media/active-directory-saas-salesforce-tutorial/sf-admin-sso.png
[11]: ./media/active-directory-saas-salesforce-tutorial/sf-admin-sso-edit.png
[12]: ./media/active-directory-saas-salesforce-tutorial/sf-enable-saml.png
[13]: ./media/active-directory-saas-salesforce-tutorial/sf-admin-sso-new.png
[14]: ./media/active-directory-saas-salesforce-tutorial/sf-saml-config.png
[15]: ./media/active-directory-saas-salesforce-tutorial/sf-my-domain.png
[16]: ./media/active-directory-saas-salesforce-tutorial/sf-edit-auth-config.png
[17]: ./media/active-directory-saas-salesforce-tutorial/sf-auth-config.png
[18]: ./media/active-directory-saas-salesforce-tutorial/sso-confirm.png
[19]: ./media/active-directory-saas-salesforce-tutorial/sso-notification.png
[20]: ./media/active-directory-saas-salesforce-tutorial/config-prov.png
[21]: ./media/active-directory-saas-salesforce-tutorial/config-prov-dialog.png
[22]: ./media/active-directory-saas-salesforce-tutorial/sf-my-settings.png
[23]: ./media/active-directory-saas-salesforce-tutorial/sf-personal-reset.png
[24]: ./media/active-directory-saas-salesforce-tutorial/sf-reset-token.png
[25]: ./media/active-directory-saas-salesforce-tutorial/got-the-token.png
[26]: ./media/active-directory-saas-salesforce-tutorial/prov-confirm.png
[27]: ./media/active-directory-saas-salesforce-tutorial/assign-users.png
[28]: ./media/active-directory-saas-salesforce-tutorial/assign-confirm.png
[29]: ./media/active-directory-saas-salesforce-tutorial/assign-sf-profile.png

<!---HONumber=Oct15_HO4-->