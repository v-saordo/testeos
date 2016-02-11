<properties
	pageTitle="Vista previa de Azure Active Directory B2C: Configuración de la cuenta Microsoft | Microsoft Azure"
	description="Proporcione registro e inicio de sesión a los consumidores con cuentas Microsoft en las aplicaciones protegidas por Azure Active Directory B2C"
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

# Vista previa de Azure Active Directory B2C: Proporcionar a registro e inicio de sesión a los consumidores con cuentas Microsoft

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Creación de una aplicación de cuenta Microsoft

Para usar una cuenta Microsoft como proveedor de identidades en Azure Active Directory (AD) B2C, primero debe crear una aplicación de cuenta Microsoft y suministrarle los parámetros correctos. Para ello, necesitará una cuenta Microsoft; si no tiene una, puede obtenerla en [https://www.live.com/](https://www.live.com/).

1. Vaya al [Centro para desarrolladores de cuentas Microsoft](https://account.live.com/developers/applications) e inicie sesión con las credenciales de su cuenta Microsoft.
2. Haga clic en **Crear aplicación**.

    ![MSA - agregar una nueva aplicación](./media/active-directory-b2c-setup-msa-app/msa-add-new-app.png)

3. En **Nombre de la aplicación** proporcione un nombre para la aplicación y haga clic en **Acepto**. Nota: Es necesario aceptar los que términos de uso de los servicios de Microsoft.

    ![MSA - nombre de aplicación](./media/active-directory-b2c-setup-msa-app/msa-app-name.png)

4. En el panel de navegación izquierdo, haga clic en **Configuración de API**. Escriba una **dirección de correo electrónico de contacto** válida.

    ![Configuración de API](./media/active-directory-b2c-setup-msa-app/msa-api-settings.png)

5. Escriba `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` en el campo **URL de redireccionamiento**, donde **{tenant}** de sustituirá por el nombre de su inquilino (por ejemplo, contosob2c.onmicrosoft.com). Haga clic en **Guardar** en la parte inferior de la página.

    ![MSA - URL de redireccionamiento](./media/active-directory-b2c-setup-msa-app/msa-redirect-url.png)

6. En el panel de navegación izquierdo, haga clic en **Configuración de API**. Copie los valores **Id. de cliente** y **Secreto del cliente**. Necesitará ambos para configurar una cuenta Microsoft como proveedor de identidades de su inquilino. Nota: el **secreto de cliente** es una credencial de seguridad importante.

    ![MSA - secreto de cliente](./media/active-directory-b2c-setup-msa-app/msa-client-secret.png)

## Configuración de una cuenta Microsoft como proveedor de identidades de su inquilino

1. [Siga estos pasos para desplazarse hasta la hoja de características B2C en el Portal de Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. En la hoja de características B2C, haga clic en **Proveedores de identidades**.
3. Haga clic en **+Agregar** en la parte superior de la hoja.
4. Proporcione un **Nombre** descriptivo para la configuración del proveedor de identidades. Por ejemplo, escriba "MSA".
5. Haga clic en **Tipo de proveedor de identidades**, seleccione **Cuenta Microsoft** y haga clic en **Aceptar**.
6. Haga clic en **Configurar este proveedor de identidades** y proporcione los valores de **Id. de cliente** y **Secreto de cliente** de la aplicación de cuenta Microsoft que creó anteriormente.
7. Haga clic en **Aceptar** y en **Crear** para guardar la configuración de la cuenta Microsoft.

<!---HONumber=AcomDC_0114_2016-->