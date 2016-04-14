<properties
	pageTitle="Vista previa de Azure Active Directory B2C: Configuración de Google+ | Microsoft Azure"
	description="Proporcione funciones de registro e inicio de sesión a los consumidores con cuentas de Google+ en las aplicaciones protegidas por Azure Active Directory B2C."
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

# Versión preliminar de Azure Active Directory B2C: registro e inicio de sesión para los usuarios con cuentas de Google+

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Creación de una aplicación de Google+

Para usar Google+ como proveedor de identidades en Azure Active Directory (Azure AD) B2C, debe crear una aplicación de Google+ y suministrarle los parámetros correctos. Necesita una cuenta de Google+ para hacerlo. Si no tiene una, puede obtenerla en [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp).

1. Vaya a [Google Developers Console](https://console.developers.google.com/) e inicie sesión con las credenciales de su cuenta de Google+.
2. Haga clic en **Crear proyecto**, escriba un **nombre de proyecto** y luego haga clic en **Crear**.

    ![Google+: introducción](./media/active-directory-b2c-setup-goog-app/google-get-started.png)

    ![Google+: nuevo proyecto](./media/active-directory-b2c-setup-goog-app/google-new-project.png)

3. En el panel de navegación izquierdo, haga clic en **Administrador de API** y, a continuación, en **Credenciales**.
4. Haga clic en la **pantalla de consentimiento de OAuth** en la parte superior.

    ![Google+: credenciales](./media/active-directory-b2c-setup-goog-app/google-add-cred.png)

5. Seleccione o especifique una **dirección de correo electrónico** válida, proporcione un **nombre de producto** y haga clic en **Guardar**.

    ![Google+: pantalla de consentimiento de OAuth](./media/active-directory-b2c-setup-goog-app/google-consent-screen.png)

6. Haga clic en **Nuevas credenciales** y, a continuación, elija **Id. de cliente de OAuth**.

    ![Google+: pantalla de consentimiento de OAuth](./media/active-directory-b2c-setup-goog-app/google-add-oauth2-client-id.png)

7. En **Tipo de aplicación**, seleccione **Aplicación web**.

    ![Google+: pantalla de consentimiento de OAuth](./media/active-directory-b2c-setup-goog-app/google-web-app.png)

8. Proporcione un **Nombre** para la aplicación, escriba `https://login.microsoftonline.com` en el campo **Orígenes de JavaScript autorizados** y `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` en el campo **URI de redireccionamiento autorizado**. Reemplace **{tenant}** por el nombre de su inquilino (por ejemplo, contosob2c.onmicrosoft.com). El valor de **{tenant}** distingue mayúsculas de minúsculas. Haga clic en **Crear**.

    ![Google+: creación del identificador de cliente](./media/active-directory-b2c-setup-goog-app/google-create-client-id.png)

9. Copie los valores **Id. de cliente** y **Secreto del cliente**. Necesitará ambos para configurar Google+ como proveedor de identidades de su inquilino. El **secreto de cliente** es una credencial de seguridad importante.

    ![Google+: secreto de cliente](./media/active-directory-b2c-setup-goog-app/google-client-secret.png)

## Configuración de Google+ como proveedor de identidades del inquilino

1. Siga estos pasos para [ir a la hoja de características de B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) en el Portal de Azure.
2. En la hoja de características B2C, haga clic en **Proveedores de identidades**.
3. Haga clic en **+Agregar** en la parte superior de la hoja.
4. Proporcione un **Nombre** descriptivo para la configuración del proveedor de identidades. Por ejemplo, "G+".
5. Haga clic en **Tipo de proveedor de identidades**, seleccione **Google** y haga clic en **Aceptar**.
6. Haga clic en **Configurar este proveedor de identidades** y escriba el identificador de cliente y el secreto de cliente de la aplicación de Google+ que creó anteriormente.
7. Haga clic en **Aceptar** y, luego, en **Crear** para guardar la configuración de Google+.

<!---HONumber=AcomDC_0302_2016-->