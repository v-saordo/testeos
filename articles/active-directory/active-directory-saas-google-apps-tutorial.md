<properties
   pageTitle="Tutorial: Integración de Google Apps con Azure Active Directory | Microsoft Azure"
   description="Aprenda cómo usar Google Apps con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc."
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
   ms.date="11/01/2015"
   ms.author="liviodlc"/>

#Tutorial: Integración de Google Apps con Azure Active Directory

Este tutorial le mostrará cómo conectar el entorno de Google Apps a Azure Active Directory (Azure AD). Obtendrá información sobre cómo configurar un inicio de sesión único en Google Apps, cómo habilitar el aprovisionamiento automático de usuarios y cómo asignar usuarios para que dispongan de acceso a Google Apps.

##Requisitos previos

1. Para obtener acceso a Azure Active Directory a través del [Portal de administración de Azure](https://manage.windowsazure.com), primero debe tener una suscripción de Azure válida.

2. Debe tener un inquilino válido para [Google Apps para trabajo](https://www.google.com/work/apps/) o [Google Apps para educación](https://www.google.com/edu/products/productivity-tools/). Puede usar una cuenta de prueba gratuita de cualquiera de los servicios.

##Tutorial en vídeo

Habilitación del inicio de sesión único en Google Apps en 2 minutos:

> [AZURE.VIDEO enable-single-sign-on-to-google-apps-in-2-minutes-with-azure-ad]

##Paso 1: Adición de Google Apps a su directorio

1. En el panel de navegación izquierdo del [Portal de administración de Azure](https://manage.windowsazure.com), haga clic en **Active Directory**.

	![Seleccione Active Directory en el panel de navegación izquierdo.][0]

2. En la lista **Directorio**, seleccione el directorio al que desea agregar Google Apps.

3. Haga clic en **Aplicaciones** en el menú superior.

	![Haga clic en Aplicaciones.][1]

4. Haga clic en **Agregar** en la parte inferior de la página.

	![Haga clic en Agregar para agregar una nueva aplicación.][2]

5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

	![Haga clic en Agregar una aplicación de la galería.][3]

6. En el **cuadro de búsqueda**, escriba **Google Apps**. A continuación, seleccione **Google Apps** en los resultados y haga clic en **Completar** para agregar la aplicación.

	![Agregue Google Apps.][4]

7. Ahora debería ver la página de inicio rápido para Google Apps:

	![Página de inicio rápido de Google Apps en Azure AD][5]

##Paso 2: Habilitación del inicio de sesión único

1. En Azure AD, en la página de inicio rápido de Google Apps, haga clic en el botón **Configurar inicio de sesión único**.

	![Botón de inicio de sesión único de configuración][6]

2. Se abrirá un cuadro de diálogo y verá una pantalla que le pregunta "¿Cómo desea que los usuarios inicien sesión Google Apps?". Seleccione **Inicio de sesión único de Azure AD** y, a continuación, haga clic en **Siguiente**.

	![Selección del inicio de sesión único e Azure AD][7]

	> [AZURE.NOTE]Para obtener más información sobre las diferentes opciones de inicio de sesión único, [haga clic aquí](../active-directory-appssoaccess-whatis/#how-does-single-sign-on-with-azure-active-directory-work).

3. En la página **Configuración de las opciones de la aplicación**, en el campo **URL de inicio de sesión**, escriba la dirección URL del inquilino de Google Apps con el formato siguiente:`https://mail.google.com/a/<yourdomain>`

	![Especificación de la dirección URL de inquilino][8]

4. En la página **Configurar el inicio de sesión único automáticamente**, escriba el dominio del inquilino de Google Apps. A continuación, presione el botón **Configurar**.

	![Escriba el nombre de dominio y presione Configurar.](./media/active-directory-saas-google-apps-tutorial/ga-auto-config.png)

	> [AZURE.NOTE]Si prefiere configurar el inicio de sesión único manualmente, consulte [Paso opcional: configuración manual del inicio de sesión único](#optional-step-manually-configure-single-sign-on)

5. Inicie sesión en su cuenta de administrador de Google Apps. A continuación, haga clic en **Permitir** para permitir que Azure Active Directory realice cambios en la configuración de su suscripción de Google Apps.

	![Escriba el nombre de dominio y presione Configurar.](./media/active-directory-saas-google-apps-tutorial/ga-consent.PNG)

6. Espere unos segundos mientras Azure Active Directory configura al inquilino de Google Apps. Cuando haya terminado, haga clic en **Siguiente**.

10. En la última página del cuadro de diálogo, escriba una dirección de correo electrónico si desea recibir notificaciones de correo electrónico de errores y advertencias relacionadas con el mantenimiento de esta configuración de inicio de sesión único.

	![Escriba la dirección de correo electrónico.][14]

11. Haga clic en **Completar** para cerrar el cuadro de diálogo. Para probar la configuración, consulte la sección [Asignación de usuarios a Google Apps](#step-4-assign-users-to-google-apps), que encontrará a continuación.

##Paso opcional: configuración manual del inicio de sesión único

Si prefiere configurar el inicio de sesión único manualmente, siga estos pasos:

1. En Azure AD, en la página de inicio rápido de Google Apps, haga clic en el botón **Configurar inicio de sesión único**.

	![Botón de inicio de sesión único de configuración][6]

2. Se abrirá un cuadro de diálogo y verá una pantalla que le pregunta "¿Cómo desea que los usuarios inicien sesión Google Apps?". Seleccione **Inicio de sesión único de Azure AD** y, a continuación, haga clic en **Siguiente**.

	![Selección del inicio de sesión único e Azure AD][7]

	> [AZURE.NOTE]Para obtener más información sobre las diferentes opciones de inicio de sesión único, [haga clic aquí](../active-directory-appssoaccess-whatis/#how-does-single-sign-on-with-azure-active-directory-work).

3. En la página **Configuración de las opciones de la aplicación**, en el campo **URL de inicio de sesión**, escriba la dirección URL del inquilino de Google Apps con el formato siguiente:`https://mail.google.com/a/<yourdomain>`

	![Especificación de la dirección URL de inquilino][8]

4. En la página **Configurar el inicio de sesión único automáticamente**, active la casilla **Configurar manualmente esta aplicación para el inicio de sesión único**. A continuación, haga clic en **Siguiente**.

	![Elija la configuración manual.](./media/active-directory-saas-google-apps-tutorial/ga-auto-skip.PNG)

4. En la página **Configuración de inicio de sesión único en Google Apps**, haga clic en **Descargar certificado** y después guarde el archivo de certificado localmente en el equipo.

	![Descargue el certificado.][9]

5. Abra una nueva pestaña en el explorador e inicie sesión en la [Consola de administración de Google Apps](http://admin.google.com/) con su cuenta de administrador.

6. Haga clic en **Seguridad**. Si no ve el vínculo, puede estar oculto debajo del menú **Más controles** en la parte inferior de la pantalla.

	![Haga clic en Seguridad.][10]

7. En la página **Seguridad**, haga clic en **Configurar inicio de sesión único (SSO)**.

	![Haga clic en SSO.][11]

8. Realice los cambios de configuración siguientes:

	![Configuración de SSO][12]

	- Seleccione **Configurar SSO con un proveedor de identidades de terceros**.

	- En Azure AD, copie la **dirección URL del servicio de inicio de sesión único** y péguela en el campo **URL de página de inicio de sesión** de Google Apps.

	- En Azure AD, copie la **dirección URL del servicio de cierre de sesión único** y péguela en el campo **URL de página de cierre de sesión** de Google Apps.

	- En Azure AD, copie el valor de **Cambiar dirección URL de contraseña** y péguelo en el campo **Cambiar URL de contraseña** de Google Apps.

	- En Google Apps, para el **certificado de verificación**, cargue el certificado que descargó en el paso 4.

	- Haga clic en **Guardar cambios**.

9. En Azure AD, seleccione la casilla de confirmación de configuración de inicio de sesión único para habilitar el certificado que cargó en Google Apps. A continuación, haga clic en **Siguiente**.

	![Activación de la casilla de confirmación][13]

10. En la última página del cuadro de diálogo, escriba una dirección de correo electrónico si desea recibir notificaciones de correo electrónico de errores y advertencias relacionadas con el mantenimiento de esta configuración de inicio de sesión único.

	![Escriba la dirección de correo electrónico.][14]

11. Haga clic en **Completar** para cerrar el cuadro de diálogo. Para probar la configuración, consulte la sección [Asignación de usuarios a Google Apps](#step-4-assign-users-to-google-apps), que encontrará a continuación.

##Paso 3: Habilitación del aprovisionamiento automático de usuarios

> [AZURE.NOTE]Otra opción viable para la automatización del aprovisionamiento de usuarios en Google Apps es usar [Google Apps Directory Sync (GADS)](https://support.google.com/a/answer/106368?hl=en), que aprovisiona las identidades de Active Directory locales en Google Apps. En cambio, la solución en este tutorial realiza el aprovisionamiento de los usuarios de Azure Active Directory (nube) y a los grupos habilitados para correo en Google Apps.

1. Inicie sesión en la [Consola de administración de Google Apps](http://admin.google.com/) con su cuenta de administrador y haga clic en **Seguridad**. Si no ve el vínculo, puede estar oculto debajo del menú **Más controles** en la parte inferior de la pantalla.

	![Haga clic en Seguridad.][10]

2. En la página **Seguridad**, haga clic en **Referencia de API**.

	![Haga clic en la referencia de la API.][15]

3. Seleccione **Enable API access**.

	![Haga clic en la referencia de la API.][16]

	> [AZURE.IMPORTANT]El nombre en Active Directory de todos los usuarios desee aprovisionar en Google Apps *debe* estar enlazado con un dominio personalizado. Por ejemplo, Google Apps no aceptará los nombres de usuario parecidos a bob@contoso.onmicrosoft.com, mientras que bob@contoso.com se aceptará. Puede cambiar el dominio de un usuario existente editando sus propiedades en Azure AD. A continuación, se incluyen instrucciones sobre cómo establecer un dominio personalizado para Azure Active Directory y Google Apps.

4. Si no ha agregado un nombre de dominio personalizado para Azure Active Directory todavía, siga estos pasos:

	- En el panel de navegación izquierdo del [Portal de administración](https://manage.windowsazure.com) de Azure, haga clic en **Active Directory**. En la lista de directorios, seleccione el directorio. 

	- Haga clic en **Dominios** en el menú de nivel superior y, a continuación, haga clic en **Agregar un dominio personalizado**.

		![Incorporación de un dominio personalizado][17]

	- Escriba el nombre del dominio en el campo **Nombre de dominio**. Debe ser el mismo nombre de dominio que pensaba utilizar para Google Apps. Cuando esté listo, haga clic en el botón **Agregar**.

		![Escriba el nombre de dominio.][18]

	- Haga clic en **Siguiente** para ir a la página de comprobación. Para comprobar que es propietario de este dominio, debe editar los registros DNS del dominio según los valores proporcionados en esta página. Puede optar por realizar la comprobación con **registros MX** o **registros TXT** según lo que seleccione en la opción **Tipo de registro**. Para obtener instrucciones detalladas sobre cómo comprobar nombres de dominio con Azure AD, consulte [Incorporación de su propio nombre de dominio a Azure AD](https://go.microsoft.com/fwLink/?LinkID=278919&clcid=0x409).

		![Compruebe el nombre de dominio.][19]

	- Repita los pasos anteriores para todos los dominios que desee agregar a su directorio.

5. Ahora que ha comprobado todos los dominios con Azure AD, debe comprobarlos de nuevo ahora con Google Apps. Para cada dominio que no esté registrado aún con Google Apps, realice los pasos siguientes:

	- En la [Consola de administración de Google Apps](http://admin.google.com/), haga clic en **Dominios**.

		![Haga clic en Dominios][20]

	- Haga clic en **Añadir un dominio o un alias de dominio**.

		![Adición de un nuevo dominio][21]

	- Seleccione **Añadir otro dominio** y escriba el nombre del dominio que desee agregar.

		![Especificación de un nombre de dominio][22]

	- Haga clic en **Continuar y comprobar la propiedad del dominio**. A continuación, siga los pasos para comprobar que posee el nombre de dominio. Para obtener instrucciones completas sobre cómo comprobar un dominio con Google Apps, consulte [Cómo elegir un método de verificación](https://support.google.com/webmasters/answer/35179).

	- Repita los pasos anteriores para todos los dominios adicionales que se va a agregar a Google Apps.

	> [AZURE.WARNING]Si cambia el dominio principal del inquilino de Google Apps y ya ha configuran el inicio de sesión único con Azure AD, tendrá que repetir el paso 3 en [Paso dos: habilitación del inicio de sesión único](#step-two-enable-single-sign-on).

6. En la [Consola de administración de Google Apps](http://admin.google.com/), haga clic en **Funciones de administrador**.

	![Haga clic en Google Apps][26]

7. Determine qué cuenta de administrador le gustaría usar para administrar el aprovisionamiento de usuarios. Para el **rol de administrador** de esa cuenta, edite los **privilegios** de dicho rol. Asegúrese de que tiene todos los **privilegios de la API de administración** habilitados para que esta cuenta pueda usarse para el aprovisionamiento.

	![Haga clic en Google Apps][27]

	> [AZURE.NOTE]Si va a configurar un entorno de producción, la práctica recomendada es crear una nueva cuenta de administrador en Google Apps específicamente para este paso. Esta cuenta debe tener asociada a ella un rol de administrador que tenga los privilegios necesarios de la API.

8. En Azure Active Directory, haga clic en **Aplicaciones** en el menú de nivel superior y, a continuación, haga clic en **Google Apps**.

	![Haga clic en Google Apps][23]

9. En la página de inicio rápido de Google Apps, haga clic en **Configurar aprovisionamiento de usuarios**.

	![Configuración de aprovisionamiento de usuario][24]

10. En el cuadro de diálogo que se abre, haga clic en **Habilitar aprovisionamiento de usuarios** para autenticarse en la cuenta de administrador de Google Apps que desea utilizar para administrar el aprovisionamiento.

	![Habilitación de aprovisionamiento][25]

11. Confirme que desea conceder permiso de Azure Active Directory para realizar cambios en el inquilino de Google Apps.

	![Confirme los permisos.][28]

12. Haga clic en **Completar** para cerrar el cuadro de diálogo.

##Paso 4: Asignación de usuarios a Google Apps

1. Para probar la configuración, empiece a crear una nueva cuenta de prueba en el directorio.

2. En la página de inicio rápido de Google Apps, haga clic en el botón **Asignar usuarios**.

	![Haga clic en Asignar usuarios][29]

3. Seleccione el usuario de prueba y haga clic en el botón **Asignar** de la parte inferior de la pantalla:

 - Si no lo ha habilitado el aprovisionamiento automático de usuarios, verá el mensaje siguiente de confirmación:

		![Confirm the assignment.][30]

 - Si ha habilitado el aprovisionamiento automático de usuarios, verá un símbolo del sistema para definir qué tipo de rol debe tener el usuario en Google Apps. Los usuarios recién aprovisionados deberían aparecer en su entorno de Google Apps después de unos minutos.

4. Para probar la configuración del inicio de sesión único, abra el panel de acceso en [https://myapps.microsoft.com](https://myapps.microsoft.com/) y, a continuación, inicie sesión en la cuenta de prueba y haga clic en **Google Apps**.

[AZURE.INCLUDE [saas-toc](../../includes/active-directory-saas-toc.md)]

[0]: ./media/active-directory-saas-google-apps-tutorial/azure-active-directory.png
[1]: ./media/active-directory-saas-google-apps-tutorial/applications-tab.png
[2]: ./media/active-directory-saas-google-apps-tutorial/add-app.png
[3]: ./media/active-directory-saas-google-apps-tutorial/add-app-gallery.png
[4]: ./media/active-directory-saas-google-apps-tutorial/add-gapps.png
[5]: ./media/active-directory-saas-google-apps-tutorial/gapps-added.png
[6]: ./media/active-directory-saas-google-apps-tutorial/config-sso.png
[7]: ./media/active-directory-saas-google-apps-tutorial/sso-gapps.png
[8]: ./media/active-directory-saas-google-apps-tutorial/sso-url.png
[9]: ./media/active-directory-saas-google-apps-tutorial/download-cert.png
[10]: ./media/active-directory-saas-google-apps-tutorial/gapps-security.png
[11]: ./media/active-directory-saas-google-apps-tutorial/security-gapps.png
[12]: ./media/active-directory-saas-google-apps-tutorial/gapps-sso-config.png
[13]: ./media/active-directory-saas-google-apps-tutorial/gapps-sso-confirm.png
[14]: ./media/active-directory-saas-google-apps-tutorial/gapps-sso-email.png
[15]: ./media/active-directory-saas-google-apps-tutorial/gapps-api.png
[16]: ./media/active-directory-saas-google-apps-tutorial/gapps-api-enabled.png
[17]: ./media/active-directory-saas-google-apps-tutorial/add-custom-domain.png
[18]: ./media/active-directory-saas-google-apps-tutorial/specify-domain.png
[19]: ./media/active-directory-saas-google-apps-tutorial/verify-domain.png
[20]: ./media/active-directory-saas-google-apps-tutorial/gapps-domains.png
[21]: ./media/active-directory-saas-google-apps-tutorial/gapps-add-domain.png
[22]: ./media/active-directory-saas-google-apps-tutorial/gapps-add-another.png
[23]: ./media/active-directory-saas-google-apps-tutorial/apps-gapps.png
[24]: ./media/active-directory-saas-google-apps-tutorial/gapps-provisioning.png
[25]: ./media/active-directory-saas-google-apps-tutorial/gapps-provisioning-auth.png
[26]: ./media/active-directory-saas-google-apps-tutorial/gapps-admin.png
[27]: ./media/active-directory-saas-google-apps-tutorial/gapps-admin-privileges.png
[28]: ./media/active-directory-saas-google-apps-tutorial/gapps-auth.png
[29]: ./media/active-directory-saas-google-apps-tutorial/assign-users.png
[30]: ./media/active-directory-saas-google-apps-tutorial/assign-confirm.png

<!---HONumber=Nov15_HO2-->