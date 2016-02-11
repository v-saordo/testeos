<properties
	pageTitle="Vista previa de Azure Active Directory B2C: Configuración de Facebook | Microsoft Azure"
	description="Proporcionar registro e inicio de sesión a los consumidores con cuentas de Facebook en las aplicaciones protegidas por Azure Active Directory B2C"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="bryanla"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/12/2016"
	ms.author="swkrish"/>

# Vista previa de Azure Active Directory B2C: Proporcionar a los consumidores registro e inicio de sesión con cuentas de Facebook

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Creación de una aplicación de Facebook

Para usar Facebook como proveedor de identidades en Azure Active Directory (AD) B2C, primero debe crear una aplicación de Facebook y suministrarle los parámetros correctos. Para ello, necesita una cuenta de Facebook; si no tiene una, puede obtenerla en [https://www.facebook.com/](https://www.facebook.com/).

1. Vaya al [sitio web para desarrolladores de Facebook](https://developers.facebook.com/) e inicie sesión con las credenciales de su cuenta de Facebook.
2. Si aún no lo ha hecho, debe registrarse como desarrollador de Facebook. Para ello, haga clic en **Register** (Registrarse) (en la esquina superior derecha de la página), acepte las políticas de Facebook y complete los pasos de registro.
3. Haga clic en **My Apps** (Mis aplicaciones) y, a continuación, en **Add a new App** (Agregar una nueva aplicación) A continuación, elija **Website** (Sitio web) como plataforma y haga clic en **Skip and Create App ID** (Omitir y crear un id. de aplicación).

    ![FB - agregar una nueva aplicación](./media/active-directory-b2c-setup-fb-app/fb-add-new-app.png)

    ![FB - agregar una nueva aplicación - sitio web](./media/active-directory-b2c-setup-fb-app/fb-add-new-app-website.png)

    ![FB - crear el id. de aplicación](./media/active-directory-b2c-setup-fb-app/fb-new-app-skip.png)

4. En el formulario, proporcione un **nombre para mostrar**, elija la **categoría** adecuada y haga clic en **Create App ID** (Crear id. de aplicación). Nota: debe aceptar las políticas de la plataforma de Facebook y realizar una comprobación de seguridad en línea.

    ![FB - crear el id. de aplicación](./media/active-directory-b2c-setup-fb-app/fb-create-app-id.png)

5. En la barra de navegación de la izquierda, haga clic en **Settings** (Configuración). Escriba una **dirección de correo electrónico de contacto** válida.
6. Haga clic en **+Add Platform** (+ Agregar plataforma) y, luego, seleccione **Website** (Sitio web).

    ![FB - configuración](./media/active-directory-b2c-setup-fb-app/fb-settings.png)

    ![FB - configuración](./media/active-directory-b2c-setup-fb-app/fb-website.png)

7. Escriba [https://login.microsoftonline.com/](https://login.microsoftonline.com/) en el campo **Site URL** (URL del sitio) y, luego, haga clic en **Save Changes** (Guardar cambios).
8. Copie el valor de **App ID** (Id. de aplicación). Haga clic en **Show** (Mostrar) y copie el valor de **App Secret** (Secreto de la aplicación). Necesitará ambos para configurar Facebook como proveedor de identidades de su inquilino. Nota: el **secreto de aplicación** es una credencial de seguridad importante.

    ![FB - URL del sitio](./media/active-directory-b2c-setup-fb-app/fb-site-url.png)

9. Haga clic en la pestaña **Avanzadas** en la parte superior y luego escriba `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` en el campo **URI de redirección de OAuth válidos** (en la sección **Seguridad**), donde **{tenant}** se sustituirá por el nombre de su inquilino (por ejemplo, contosob2c.onmicrosoft.com). Haga clic en **Save Changes** (Guardar cambios) en la parte inferior de la página.

    ![FB - URI de redirección de OAuth](./media/active-directory-b2c-setup-fb-app/fb-oauth-redirect-uri.png)

10. Para que la aplicación Facebook se puede usar con Azure AD B2C, es necesario ponerla a disposición del público. Para ello, haga clic en **Estado y revisión** en el panel de navegación izquierdo y envíe la aplicación a revisión (haga clic en el botón **Iniciar un envío**). Una vez que Facebook apruebe la aplicación, puede hacerla pública si gira el conmutador de la parte superior de la página a **SÍ**. Y hace clic en **Confirmar**.

    ![FB - envío de la aplicación](./media/active-directory-b2c-setup-fb-app/fb-app-submission.png)

    ![FB - aplicación pública](./media/active-directory-b2c-setup-fb-app/fb-app-public.png)

## Configuración de Facebook como proveedor de identidades del inquilino

1. [Siga estos pasos para desplazarse hasta la hoja de características B2C en el Portal de Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. En la hoja de características B2C, haga clic en **Proveedores de identidades**.
3. Haga clic en **+Agregar** en la parte superior de la hoja.
4. Proporcione un **Nombre** descriptivo para la configuración del proveedor de identidades. Por ejemplo, "FB".
5. Haga clic en **Tipo de proveedor de identidades**, seleccione **Facebook** y haga clic en **Aceptar**.
6. Haga clic en **Configurar este proveedor de identidades** y escriba el **id. de aplicación** y el **secreto de aplicación** de la aplicación Facebook que creó anteriormente en los campos **Id. de cliente** y **Secreto de cliente**, respectivamente.
7. Haga clic en **Aceptar** y, luego, en **Crear** para guardar la configuración de Facebook.

<!---HONumber=AcomDC_0114_2016-->