<properties
	pageTitle="Vista previa de Azure Active Directory B2C: Registro de la aplicación | Microsoft Azure"
	description="Registro de la aplicación con Azure Active Directory B2C"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="mbaldwin"
	editor="bryanla"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="02/25/2016"
	ms.author="swkrish"/>


# Versión preliminar de Azure Active Directory B2C: registro de la aplicación

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Requisito previo

Para crear una aplicación que acepte registros e inicios de sesión de consumidores, primero deberá registrarla en un inquilino de Azure Active Directory B2C. Obtenga su propio inquilino siguiendo los pasos descritos en [Crear un inquilino de Azure AD B2C](active-directory-b2c-get-started.md). Si ha seguido todos los pasos de este artículo, debe tener la hoja de características B2C anclada en el panel de inicio.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## Navegación a la hoja de características B2C

Puede desplazarse a la hoja de características B2C desde el Portal de Azure o el Portal de Azure clásico.

### 1\. Acceso en el Portal de Azure

Si dispone de la hoja de características B2C anclada al Panel de inicio, la verá en cuanto inicie sesión en el [Portal de Azure](https://portal.azure.com/) como administrador global del inquilino B2C.

Otra manera de acceder a la hoja es hacer clic en **Examinar** y luego en **Azure AD B2C** en el panel de navegación izquierdo del [Portal de Azure](https://portal.azure.com/).

También puede acceder a ella directamente en [https://portal.azure.com/{tenant}.onmicrosoft.com/?#blade/Microsoft\_AAD\_B2CAdmin/TenantManagementBlade/id/](https://portal.azure.com/{tenant}.onmicrosoft.com/?#blade/Microsoft_AAD_B2CAdmin/TenantManagementBlade/id/), donde **{tenant}** se sustituirá por el nombre usado en el momento de la creación del inquilino (por ejemplo, contosob2c). Puede marcar este vínculo para usarlo en el futuro.

    > [AZURE.IMPORTANT]
    You need to be a Global Administrator of the B2C tenant to be able to access the B2C features blade. A Global Administrator from any other tenant or a user from any tenant cannot access it.

### 2\. Acceso en el Portal de Azure clásico

Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com/) como administrador de la suscripción. (esto es, con la misma cuenta profesional o educativa o la misma cuenta Microsoft que usó para suscribirse a Azure). Vaya a la extensión de Active Directory de la izquierda y haga clic en el inquilino B2C. En la pestaña **Inicio rápido** (la primera pestaña que se abre), haga clic en **Administrar la configuración B2C** en **Administrar**. Se abrirá la hoja de características B2C en una nueva ventana de explorador o en una nueva pestaña.

También encontrará el vínculo **Administrar la configuración B2C** (de la sección **Administración B2C**) en la pestaña **Configurar**.

## Registro de una aplicación

1. En la hoja de características B2C del Portal de Azure, haga clic en **Aplicaciones**.
2. Haga clic en **+Agregar** en la parte superior de la hoja.
3. Escriba un **nombre** para la aplicación que la describa ante los consumidores. Por ejemplo, puede escribir "Aplicación Contoso B2C".
4. Si va a escribir una aplicación basada en web, mueva el conmutador **Incluir aplicación web/API web** a **Sí**. Las **Direcciones URL de respuesta** son puntos de conexión en los que Azure AD B2C devolverá los tokens que solicite su aplicación. Por ejemplo, escriba: `https://localhost:44321/`. Si la aplicación incluye un componente de servidor (API) que se debe proteger, es conveniente que cree (y copie) también un **Secreto de aplicación** haciendo clic en el botón **Generar clave**.

    > [AZURE.NOTE]
    El **secreto de aplicación** es una credencial de seguridad importante.

5. Si va a escribir una aplicación móvil, mueva el conmutador **Incluir cliente nativo** a **Sí**. Copie el **URI de redirección** predeterminado creado automáticamente.
6. Haga clic en **Crear** para registrar la aplicación.
7. Haga clic en la aplicación que acaba de crear y copie el **Id. de aplicación** único global que usará más adelante en el código.

## Creación de una aplicación de inicio rápido

Ahora que tiene una aplicación registrada en Azure AD B2C, puede completar uno de nuestros tutoriales de inicio rápido para ponerse en marcha rápidamente. Estas son algunas recomendaciones:

[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-b2c-quickstart-table.md)]

<!---HONumber=AcomDC_0302_2016-->