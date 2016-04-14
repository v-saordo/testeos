<properties
	pageTitle="Versión preliminar de Azure Active Directory B2C: configuración de la cuenta Microsoft | Microsoft Azure"
	description="Proporcione funciones de registro e inicio de sesión a los consumidores con cuentas Microsoft en las aplicaciones protegidas por Azure Active Directory B2C."
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

# Versión preliminar de Azure Active Directory B2C: registro e inicio de sesión para los consumidores con cuentas Microsoft

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Creación de una aplicación de cuenta Microsoft

Para usar una cuenta Microsoft como proveedor de identidades en Azure Active Directory (Azure AD) B2C, debe crear una aplicación de cuenta Microsoft y suministrarle los parámetros correctos. Necesita una cuenta de Microsoft para hacerlo. Si no tiene una, puede obtenerla en [https://www.live.com/](https://www.live.com/).

1. Vaya al [Centro para desarrolladores de cuentas Microsoft](https://account.live.com/developers/applications) e inicie sesión con las credenciales de su cuenta Microsoft.
2. Haga clic en **Crear aplicación**.

    ![Cuenta Microsoft: adición de una aplicación nueva](./media/active-directory-b2c-setup-msa-app/msa-add-new-app.png)

3. Proporcione un **nombre de la aplicación** y haga clic en **Acepto**. Es necesario aceptar los términos de uso de los servicios de Microsoft.

    ![Cuenta Microsoft: nombre de la aplicación](./media/active-directory-b2c-setup-msa-app/msa-app-name.png)

4. En la barra de navegación de la izquierda, haga clic en **Configuración de la API**. Escriba una **dirección de correo electrónico de contacto** válida.

    ![Cuenta Microsoft: configuración de la API](./media/active-directory-b2c-setup-msa-app/msa-api-settings.png)

5. Escriba `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` en el campo **URL de redireccionamiento**. Reemplace **{tenant}** por el nombre de su inquilino (por ejemplo, contosob2c.onmicrosoft.com). Haga clic en **Guardar** en la parte inferior de la página.

    ![Cuenta Microsoft: dirección URL de redireccionamiento](./media/active-directory-b2c-setup-msa-app/msa-redirect-url.png)

6. En la barra de navegación de la izquierda, haga clic en **Configuración de la aplicación**. Copie los valores **Id. de cliente** y **Secreto del cliente**. Necesitará ambos para configurar una cuenta Microsoft como proveedor de identidades de su inquilino. El **secreto de cliente** es una credencial de seguridad importante.

    ![Cuenta Microsoft: secreto de cliente](./media/active-directory-b2c-setup-msa-app/msa-client-secret.png)

## Configuración de una cuenta Microsoft como proveedor de identidades de su inquilino

1. Siga estos pasos para [ir a la hoja de características de B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) en el Portal de Azure.
2. En la hoja de características B2C, haga clic en **Proveedores de identidades**.
3. Haga clic en **+Agregar** en la parte superior de la hoja.
4. Proporcione un **Nombre** descriptivo para la configuración del proveedor de identidades. Por ejemplo, escriba "MSA".
5. Haga clic en **Tipo de proveedor de identidades**, seleccione **Cuenta Microsoft** y haga clic en **Aceptar**.
6. Haga clic en **Configurar este proveedor de identidades** y proporcione los valores de identificador de cliente y secreto de cliente de la aplicación de cuenta Microsoft que creó anteriormente.
7. Haga clic en **Aceptar** y en **Crear** para guardar la configuración de la cuenta Microsoft.

<!---HONumber=AcomDC_0302_2016-->