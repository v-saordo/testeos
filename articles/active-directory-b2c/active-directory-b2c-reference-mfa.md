<properties
	pageTitle="Vista previa de Azure Active Directory B2C: Multi-Factor Authentication | Microsoft Azure"
	description="Habilitación de Multi-Factor Authentication en las aplicaciones orientadas al consumidor protegidas por Azure Active Directory B2C"
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
	ms.date="01/04/2016"
	ms.author="swkrish"/>

# Versión preliminar de Azure Active Directory B2C: Habilitación de Multi-Factor Authentication en las aplicaciones orientadas al consumidor

Azure Active Directory (Azure AD) B2C se integra directamente con [Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md) para que pueda agregar una segunda capa de seguridad a las experiencias de registro e inicio de sesión en las aplicaciones orientadas al consumidor. Y todo esto sin necesidad de escribir una sola línea de código. Actualmente se admite la comprobación mediante llamadas de teléfono y mensajes de texto. Aunque ya haya creado directivas de registro e inicio de sesión, puede habilitar Multi-Factor Authentication.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

> [AZURE.NOTE]
También se puede habilitar Multi-Factor Authentication al crear directivas de registro e inicio de sesión, no solo mediante la edición de las directivas existentes.

Esta característica ayuda a las aplicaciones a tratar escenarios como los siguientes:

- No se requiere Multi-Factor Authentication para acceder a una aplicación, pero sí para acceder a otra. Por ejemplo, el consumidor puede iniciar sesión en una aplicación de seguros de automóvil con una cuenta local o social, pero debe comprobar el número de teléfono antes de acceder a la aplicación principal de seguros registrada en el mismo directorio.
- No se requiere Multi-Factor Authentication para acceder a una aplicación en general, pero sí para acceder a las partes confidenciales que contiene. Por ejemplo, el consumidor puede iniciar sesión en una aplicación de banca con una cuenta social o local y comprobar el saldo de las cuentas, pero debe confirmar el número de teléfono antes de realizar una transferencia bancaria.

## Modificación de la directiva de registro para habilitar Multi-Factor Authentication

1. [Siga estos pasos para ir a la hoja de características B2C en el Portal de Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Haga clic en **Directivas de registro**.
3. Haga clic en la directiva de registro (por ejemplo, "B2C\_1\_SiUp") para abrirla.
4. Haga clic en **Autenticación multifactor** y configure el **Estado** como **Activado**. Haga clic en **OK**.
5. Haga clic en **Guardar** en la parte superior de la hoja.

Puede usar la característica "Ejecutar ahora" en la directiva para comprobar la experiencia del consumidor. Confirme lo siguiente:

Se crea una cuenta de consumidor en su directorio antes de que se produzca el paso de Multi-Factor Authentication. Durante el paso, se pide al consumidor que proporcione su número de teléfono y que lo compruebe. Si la comprobación es satisfactoria, el número de teléfono se adjunta a la cuenta de consumidor para su uso posterior. Incluso si el consumidor cancela o abandona, se le puede pedir que compruebe de nuevo un número de teléfono durante el siguiente inicio de sesión (con Multi-Factor Authentication habilitada).

## Modificación de la directiva de inicio de sesión para habilitar Multi-Factor Authentication

1. [Siga estos pasos para ir a la hoja de características B2C en el Portal de Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Haga clic en **Directivas de inicio de sesión**.
3. Haga clic en la directiva de inicio de sesión (por ejemplo, "B2C\_1\_SiIn") para abrirla. Haga clic en **Editar** en la parte superior de la hoja.
4. Haga clic en **Autenticación multifactor** y configure el **Estado** como **Activado**. Haga clic en **OK**.
5. Haga clic en **Guardar** en la parte superior de la hoja.

Puede usar la característica "Ejecutar ahora" en la directiva para comprobar la experiencia del consumidor. Confirme lo siguiente:

Cuando el consumidor inicia la sesión (mediante una cuenta local o social), si un número de teléfono comprobado está asociado a su cuenta, se le pide que lo compruebe. Si no hay ningún número de teléfono asociado, se le pide al consumidor que proporcione uno y que lo compruebe. Si la comprobación es correcta, el número de teléfono se asocia a la cuenta de consumidor para su uso posterior.

<!---HONumber=AcomDC_0224_2016-->