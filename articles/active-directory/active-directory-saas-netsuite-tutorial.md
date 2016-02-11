<properties
   pageTitle="Tutorial: Integración de NetSuite con Azure Active Directory | Microsoft Azure"
   description="Aprenda cómo usar NetSuite con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc."
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

#Tutorial: Integración de NetSuite con Azure Active Directory

Este tutorial le mostrará cómo conectar el entorno de NetSuite a Azure Active Directory (Azure AD). Obtendrá información sobre cómo configurar un inicio de sesión único en NetSuite, cómo habilitar el aprovisionamiento automático de usuarios y cómo asignar usuarios para que dispongan de acceso a NetSuite.

##Requisitos previos

1. Para obtener acceso a Azure Active Directory a través del [Portal de administración de Azure](https://manage.windowsazure.com), primero debe tener una suscripción de Azure válida.

2. Debe tener acceso de administrador a una suscripción de [NetSuite](http://www.netsuite.com/portal/home.shtml). Puede usar una cuenta de prueba gratuita de cualquiera de los servicios.

##Paso 1: Adición de NetSuite a su directorio

1. En el panel de navegación izquierdo del [Portal de administración de Azure](https://manage.windowsazure.com), haga clic en **Active Directory**.

	![Seleccione Active Directory en el panel de navegación izquierdo.][0]

2. En la lista **Directorio**, seleccione el directorio que desee agregar a NetSuite.

3. Haga clic en **Aplicaciones** en el menú superior.

	![Haga clic en Aplicaciones.][1]

4. Haga clic en **Agregar** en la parte inferior de la página.

	![Haga clic en Agregar para agregar una nueva aplicación.][2]

5. En el cuadro de diálogo **Qué desea hacer**, haga clic en **Agregar una aplicación de la galería**.

	![Haga clic en Agregar una aplicación de la galería.][3]

6. En el **cuadro de búsqueda**, escriba **NetSuite**. A continuación, seleccione **NetSuite** en los resultados y haga clic en **Completar** para agregar la aplicación.

	![Agregue NetSuite.][4]

7. Ahora debería ver la página de inicio rápido para NetSuite:

	![Página de inicio rápido de NetSuite en Azure AD][5]

##Paso 2: Habilitación del inicio de sesión único

1. En Azure AD, en la página de inicio rápido para NetSuite, haga clic en el botón **Configurar inicio de sesión único**.

	![Botón de inicio de sesión único de configuración][6]

2. Se abrirá un cuadro de diálogo y verá una pantalla que le pregunta "¿Cómo desea que los usuarios inicien sesión NetSuite?". Seleccione **Inicio de sesión único de Azure AD** y luego haga clic en **Siguiente**.

	![Selección del inicio de sesión único e Azure AD][7]

	> [AZURE.NOTE]Para conocer más acerca de los diferentes opciones de inicio de sesión único, [haga clic aquí](../active-directory-appssoaccess-whatis/#how-does-single-sign-on-with-azure-active-directory-work).

3. En la página **Configurar las opciones de la aplicación**, en el campo **URL de respuesta**, escriba la dirección URL del inquilino de NetSuite con uno de los formatos siguientes:
	- `https://<tenant-name>.netsuite.com/saml2/acs`
	- `https://<tenant-name>.na1.netsuite.com/saml2/acs`
	- `https://<tenant-name>.na2.netsuite.com/saml2/acs`
	- `https://<tenant-name>.sandbox.netsuite.com/saml2/acs`
	- `https://<tenant-name>.na1.sandbox.netsuite.com/saml2/acs`
	- `https://<tenant-name>.na2.sandbox.netsuite.com/saml2/acs`

	![Especificación de la dirección URL de inquilino][8]

4. En la página **Configuración de inicio de sesión único en NetSuite**, haga clic en **Descargar metadatos** y, a continuación, guarde el archivo de certificado localmente en el equipo.

	![Descargue los metadatos.][9]

5. Abra una nueva pestaña en el explorador e inicie sesión en el sitio de la empresa Netsuite como administrador.

6. En la barra de herramientas en la parte superior de la página, haga clic en **Configuración** y, a continuación, haga clic en**Administrador de instalación**.

	![Vaya al Administrador de instalación][10]

7. En la lista **Tareas de configuración**, seleccione **Integración**.

	![Vaya a la integración][11]

8. En la sección **Administrar autenticación**, haga clic en **Inicio de sesión único de SAML**.

	![Vaya al inicio de sesión único SAML][12]

9. En la página **Configuración de SAML**, realice los pasos siguientes:

	- En Azure Active Directory, copie el valor **Dirección URL de inicio de sesión remoto** y péguelo en el campo **Página de inicio de sesión del proveedor de identidad** en NetSuite.

		![La página Configuración de SAML.][13]

	- En NetSuite, seleccione **Método de autenticación principal**.

	- Para el campo con la etiqueta**Metadatos del proveedor de identidades de SAMLV2**, seleccione **Cargar archivo de metadatos de IDP**. A continuación, haga clic en **Examinar** para cargar el archivo de metadatos que descargó en el paso 4.

		![Carga de metadatos][16]

	- Haga clic en **Enviar**.

9. En Azure AD, seleccione la casilla de confirmación de configuración de inicio de sesión único para habilitar el certificado que cargó en NetSuite. A continuación, haga clic en **Siguiente**.

	![Activación de la casilla de confirmación][14]

10. En la última página del cuadro de diálogo, escriba una dirección de correo electrónico si desea recibir notificaciones de correo electrónico de errores y advertencias relacionadas con el mantenimiento de esta configuración de inicio de sesión único.

	![Escriba la dirección de correo electrónico.][15]

11. Haga clic en **Completar** para cerrar el cuadro de diálogo. A continuación, haga clic en **Atributos** en la parte superior de la página.

	![Haga clic en Atributos.][17]

12. Haga clic en **Agregar atributo de usuario**.

	![Haga clic en Agregar atributo de usuario.][18]

13. Para el campo **Nombre de atributo**, escriba `account`. Para el campo **Valor del atributo**, escriba el identificador de cuenta de NetSuite A continuación se incluyen instrucciones sobre cómo encontrar el identificador de la cuenta:

	![Agregue el identificador de cuenta NetSuite.][19]

	- En NetSuite, haga clic en **Instalar** en el menú de navegación superior.
	- A continuación, haga clic en la sección **Tareas de configuración** del menú de navegación izquierdo, seleccione la sección **Integración** y haga clic en **Preferencias de servicios web**.
	- Copie el identificador de la cuenta NetSuite y péguelo en el campo **Valor del atributo** en Azure AD.

		![Obtención del identificador de la cuenta][20]

14. En Azure AD, haga clic en **Completar** para terminar de agregar el atributo SAML. A continuación, haga clic en**Aplicar cambios** en el menú de la parte inferior.

	![Guarde los cambios.][21]

15. Antes de que los usuarios puedan realizar el inicio de sesión único en NetSuite, se les deben asignar primero los permisos adecuados en NetSuite. Siga las instrucciones siguientes para asignar estos permisos.

	- En el menú de navegación superior, haga clic en **Configuración** y, a continuación, haga clic en **Administrador de instalación**.

		![Vaya al Administrador de instalación][10]

	- En el menú de navegación izquierdo, seleccione **Usuarios/Roles** y luego haga clic en **Administrar roles**.

		![Vaya a Administrar roles][22]

	- Haga clic en **Nuevo rol**.

	- Escriba un **nombre** para el nuevo rol y luego active la casilla **Solo inicio de sesión único**.

		![Ponga un nombre al nuevo rol.][23]

	- Haga clic en **Guardar**.

	- En el menú de la parte superior, haga clic en **Permisos**. A continuación, haga clic en **Configuración**.

		![Vaya a Permisos][24]

	- Seleccione **Configurar inicio de sesión único de SAM** y luego haga clic en **Agregar**.

	- Haga clic en **Guardar**.

	- En el menú de navegación superior, haga clic en **Configuración** y luego haga clic en **Administrador de instalación**.

		![Vaya al Administrador de instalación][10]

	- En el menú de navegación izquierdo, seleccione **Usuarios/Roles** y luego haga clic en **Administrar usuarios**.

		![Vaya a Administrar usuarios][25]

	- Seleccione un usuario de prueba. A continuación, haga clic en **Editar**.

		![Vaya a Administrar usuarios][26]

	- En el cuadro de diálogo Roles, seleccione el rol que se ha creado y haga clic en **Agregar**.

		![Vaya a Administrar usuarios][27]

	- Haga clic en **Guardar**.

19. Para probar la configuración, consulte la sección siguiente titulada [Asignación de usuarios a NetSuite](#step-4-assign-users-to-netsuite).

##Paso 3: Habilitación del aprovisionamiento automático de usuarios

> [AZURE.NOTE]De forma predeterminada, los usuarios aprovisionados se agregarán a la subsidiaria raíz del entorno NetSuite.

1. En Azure Active Directory, en la página de inicio rápido de NetSuite, haga clic en **Configurar aprovisionamiento de usuarios**.

	![Configuración de aprovisionamiento de usuario][28]

2. En el cuadro de diálogo que se abre, escriba las credenciales de administrador para NetSuite y luego haga clic en **Siguiente**.

	![Escriba las credenciales de administrador de NetSuite.][29]

3. En la página de confirmación, escriba la dirección de correo electrónico para recibir notificaciones de errores de aprovisionamiento.

	![Confirme.][30]

4. Haga clic en **Completar** para cerrar el cuadro de diálogo.

##Paso 4: Asignación de usuarios a NetSuite

1. Para probar la configuración, empiece a crear una nueva cuenta de prueba en el directorio.

2. En la página de inicio rápido de NetSuite, haga clic en el botón **Asignar usuarios**.

	![Haga clic en Asignar usuarios][31]

3. Seleccione el usuario de prueba y haga clic en el botón **Asignar** situado en la parte inferior de la pantalla:

 - Si no lo ha habilitado el aprovisionamiento automático de usuarios, verá el mensaje siguiente de confirmación:

		![Confirm the assignment.][32]

 - Si ha habilitado el aprovisionamiento automático de usuarios, verá un símbolo del sistema para definir qué tipo de rol debe tener el usuario en NetSuite. Los usuarios recién aprovisionados deberían aparecer en su entorno NetSuite después de unos minutos.

4. Para probar la configuración de inicios de sesión únicos, abra el panel de acceso en [https://myapps.microsoft.com](https://myapps.microsoft.com/), inicie sesión en la cuenta de prueba y haga clic en **NetSuite**.

[AZURE.INCLUDE [saas-toc](../../includes/active-directory-saas-toc.md)]

[0]: ./media/active-directory-saas-netsuite-tutorial/azure-active-directory.png
[1]: ./media/active-directory-saas-netsuite-tutorial/applications-tab.png
[2]: ./media/active-directory-saas-netsuite-tutorial/add-app.png
[3]: ./media/active-directory-saas-netsuite-tutorial/add-app-gallery.png
[4]: ./media/active-directory-saas-netsuite-tutorial/add-netsuite.png
[5]: ./media/active-directory-saas-netsuite-tutorial/quick-start-netsuite.png
[6]: ./media/active-directory-saas-netsuite-tutorial/config-sso.png
[7]: ./media/active-directory-saas-netsuite-tutorial/sso-netsuite.png
[8]: ./media/active-directory-saas-netsuite-tutorial/sso-config-netsuite.png
[9]: ./media/active-directory-saas-netsuite-tutorial/config-sso-netsuite.png
[10]: ./media/active-directory-saas-netsuite-tutorial/ns-setup.png
[11]: ./media/active-directory-saas-netsuite-tutorial/ns-integration.png
[12]: ./media/active-directory-saas-netsuite-tutorial/ns-saml.png
[13]: ./media/active-directory-saas-netsuite-tutorial/ns-saml-setup.png
[14]: ./media/active-directory-saas-netsuite-tutorial/ns-sso-confirm.png
[15]: ./media/active-directory-saas-netsuite-tutorial/sso-email.png
[16]: ./media/active-directory-saas-netsuite-tutorial/ns-sso-setup.png
[17]: ./media/active-directory-saas-netsuite-tutorial/ns-attributes.png
[18]: ./media/active-directory-saas-netsuite-tutorial/ns-add-attribute.png
[19]: ./media/active-directory-saas-netsuite-tutorial/ns-add-account.png
[20]: ./media/active-directory-saas-netsuite-tutorial/ns-account-id.png
[21]: ./media/active-directory-saas-netsuite-tutorial/ns-save-saml.png
[22]: ./media/active-directory-saas-netsuite-tutorial/ns-manage-roles.png
[23]: ./media/active-directory-saas-netsuite-tutorial/ns-new-role.png
[24]: ./media/active-directory-saas-netsuite-tutorial/ns-sso.png
[25]: ./media/active-directory-saas-netsuite-tutorial/ns-manage-users.png
[26]: ./media/active-directory-saas-netsuite-tutorial/ns-edit-user.png
[27]: ./media/active-directory-saas-netsuite-tutorial/ns-add-role.png
[28]: ./media/active-directory-saas-netsuite-tutorial/netsuite-provisioning.png
[29]: ./media/active-directory-saas-netsuite-tutorial/ns-creds.png
[30]: ./media/active-directory-saas-netsuite-tutorial/ns-confirm.png
[31]: ./media/active-directory-saas-netsuite-tutorial/assign-users.png
[32]: ./media/active-directory-saas-netsuite-tutorial/assign-confirm.png

<!---HONumber=Oct15_HO4-->