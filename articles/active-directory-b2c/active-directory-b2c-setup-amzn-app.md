<properties
	pageTitle="Vista previa de Azure Active Directory B2C: configuración de Amazon | Microsoft Azure"
	description="Proporcionar registro e inicio de sesión a los consumidores con cuentas de Amazon en las aplicaciones protegidas por Azure Active Directory B2C"
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

# Vista previa de Azure Active Directory B2C: proporcionar a los consumidores registro e inicio de sesión con cuentas de Amazon

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Creación de una aplicación de Amazon

Para usar Amazon como proveedor de identidades en Azure Active Directory (Azure AD) B2C, debe crear una aplicación de Amazon y suministrarle los parámetros correctos. Necesita una cuenta de Amazon para ello. Si no tiene una, puede obtenerla en [http://www.amazon.com/](http://www.amazon.com/).

1. Vaya al [Centro para desarrolladores de Amazon](https://login.amazon.com/) e inicie sesión con las credenciales de su cuenta de Amazon.
2. Si no lo ha hecho ya, haga clic en **Registrarse**, siga los pasos de registro de desarrolladores y acepte la política.
3. Haga clic en **Register new application** (Registrar nueva aplicación).

    ![Registrar una nueva aplicación en el sitio web de Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-new-app.png)

4. Proporcione información de la aplicación (**nombre**, **descripción** y **dirección URL de aviso de privacidad**) y haga clic en **Guardar**.

    ![Proporcionar información de la aplicación para registrar una nueva aplicación en Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-register-app.png)

5. En la sección **Configuración web**, copie los valores **Id. de cliente** y **Secreto del cliente**. (Debe hacer clic en el botón **Mostrar secreto** para verlo). Necesitará ambos para configurar Amazon como proveedor de identidades de su inquilino. Haga clic en **Edit** (Editar) en la parte inferior de la sección. El **secreto de cliente** es una credencial de seguridad importante.

	![Proporcionar el identificador de cliente y el secreto de cliente para la nueva aplicación en Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-client-secret.png)

6. Escriba `https://login.microsoftonline.com` en el campo **Orígenes de JavaScript permitidos** y `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` en el campo **Direcciones URL de retorno permitidas**. Reemplace **{tenant}** por el nombre de su inquilino (por ejemplo, contoso.onmicrosoft.com). Haga clic en **Save**. El valor de **{tenant}** distingue mayúsculas de minúsculas.

    ![Proporcionar orígenes de JavaScript y direcciones URL de retorno para la nueva aplicación en Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-urls.png)

## Configuración de Amazon como proveedor de identidades del inquilino

1. Siga estos pasos para [desplazarse hasta la hoja de características B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) en el Portal de Azure.
2. En la hoja de características B2C, haga clic en **Proveedores de identidades**.
3. Haga clic en **+Agregar** en la parte superior de la hoja.
4. Proporcione un **Nombre** descriptivo para la configuración del proveedor de identidades. Por ejemplo, "Amzn".
5. Haga clic en **Tipo de proveedor de identidades**, seleccione **Amazon** y haga clic en **Aceptar**.
6. Haga clic en **Configurar este proveedor de identidades** y escriba el identificador de cliente y el secreto de cliente de la aplicación de Amazon que creó anteriormente.
7. Haga clic en **Aceptar** y, luego, en **Crear** para guardar la configuración de Amazon.

<!---HONumber=AcomDC_0224_2016-->