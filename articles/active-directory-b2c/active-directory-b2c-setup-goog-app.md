<properties
	pageTitle="Vista previa de Azure Active Directory B2C: Configuración de Google+ | Microsoft Azure"
	description="Proporcionar registro e inicio de sesión a los consumidores con cuentas de Google+ en las aplicaciones protegidas por Azure Active Directory B2C"
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

# Vista previa de Azure Active Directory B2C: Proporcionar a los consumidores registro e inicio de sesión con cuentas de Google+

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Creación de una aplicación de Google+

Para usar Google+ como proveedor de identidades en Azure Active Directory (AD) B2C, primero debe crear una aplicación de Google+ y suministrarle los parámetros correctos. Para ello, necesita una cuenta de Google+; si no tiene una, puede obtenerla en [https://www.facebook.com/](https://accounts.google.com/SignUp).

1. Vaya a [Google Developers Console](https://console.developers.google.com/) e inicie sesión con las credenciales de su cuenta de Google+.
2. Haga clic en **Create Project** (Crear proyecto), en **Project name** (Nombre de proyecto) escriba un nombre para el proyecto y luego haga clic en **Create** (Crear).

    ![G+ - primeros pasos](./media/active-directory-b2c-setup-goog-app/google-get-started.png)

    ![G+ - nuevo proyecto](./media/active-directory-b2c-setup-goog-app/google-new-project.png)

3. En el panel de navegación izquierdo, haga clic en **API Manager** (Administrador de API) y, luego, en **Credentials** (Credenciales).
4. Haga clic en la **pantalla de consentimiento de OAuth** en la parte superior.

    ![G+ - credenciales](./media/active-directory-b2c-setup-goog-app/google-add-cred.png)

5. Seleccione o especifique una **dirección de correo electrónico** válida, proporcione un **nombre de producto** y haga clic en **Save** (Guardar).

    ![G+ - pantalla de consentimiento de OAuth](./media/active-directory-b2c-setup-goog-app/google-consent-screen.png)

6. Haga clic en **Agregar credenciales** y luego elija **Id. de cliente OAuth 2.0**.

    ![G+ - pantalla de consentimiento de OAuth](./media/active-directory-b2c-setup-goog-app/google-add-oauth2-client-id.png)

7. En **Tipo de aplicación**, seleccione **Aplicación web**.

    ![G+ - pantalla de consentimiento de OAuth](./media/active-directory-b2c-setup-goog-app/google-web-app.png)

8. En **Name**(Nombre), proporcione un nombre para la aplicación, escriba [https://login.microsoftonline.com](https://login.microsoftonline.com) en el campo **Authorized JavaScript origins** (Orígenes de JavaScript autorizados) y `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` en el campo **Authorized redirect URIs** (URI de redirección autorizados), donde **{tenant}** se sustituirá por el nombre del inquilino (por ejemplo, contosob2c.onmicrosoft.com). Haga clic en **Crear**. Nota: el valor de **{tenant}** distingue mayúsculas de minúsculas.

    ![G+ - crear id. de cliente](./media/active-directory-b2c-setup-goog-app/google-create-client-id.png)

9. Copie los valores **Id. de cliente** y **Secreto del cliente**. Necesitará ambos para configurar Google+ como proveedor de identidades de su inquilino. Nota: el **secreto de cliente** es una credencial de seguridad importante.

    ![G+ - secreto de cliente](./media/active-directory-b2c-setup-goog-app/google-client-secret.png)

## Configuración de Google+ como proveedor de identidades del inquilino

1. [Siga estos pasos para desplazarse hasta la hoja de características B2C en el Portal de Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. En la hoja de características B2C, haga clic en **Proveedores de identidades**.
3. Haga clic en **+Agregar** en la parte superior de la hoja.
4. Proporcione un **Nombre** descriptivo para la configuración del proveedor de identidades. Por ejemplo, "G+".
5. Haga clic en **Tipo de proveedor de identidades**, seleccione **Google** y haga clic en **Aceptar**.
6. Haga clic en **Configurar este proveedor de identidades** y escriba el **id. de cliente** y el **secreto de cliente** de la aplicación de Google+ que creó anteriormente.
7. Haga clic en **Aceptar** y, luego, en **Crear** para guardar la configuración de Google+.

<!---HONumber=AcomDC_0114_2016-->