<properties
	pageTitle="Versión preliminar de Azure Active Directory B2C: creación de un inquilino de Azure Active Directory B2C | Microsoft Azure"
	description="Un tema sobre cómo crear un inquilino de Azure Active Directory B2C"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="curtand"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="02/25/2016"
	ms.author="swkrish"/>

# Versión preliminar de Azure Active Directory B2C: creación de un inquilino de Azure AD B2C

Para empezar a usar Microsoft Azure Active Directory (Azure AD) B2C, siga los tres pasos descritos en este artículo.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Paso 1: Registrarse para obtener una suscripción de Azure

Si ya tiene una suscripción de Azure, omita este paso. Si no es así, regístrese para conseguir [una suscripción de Azure](../active-directory/sign-up-organization.md) y obtener acceso a Azure AD B2C.

> [AZURE.NOTE]
Actualmente, puede usar la versión preliminar de Azure AD B2C de forma gratuita, pero su uso está limitado a 50.000 usuarios por inquilino. Se necesita una suscripción de Azure para tener acceso al [Portal de Azure clásico](http://manage.windowsazure.com/).

## Paso 2: Crear un inquilino de Azure AD B2C

Use los pasos siguientes para crear un nuevo inquilino de Azure AD B2C. Actualmente, las características B2C no pueden activarse en los directorios existentes, si tiene alguno.

1. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com/) como administrador de la suscripción. Esta cuenta es la misma cuenta profesional o educativa o la misma cuenta Microsoft que usó para registrarse en Azure.
2. Haga clic en **Nuevo** > **Servicios de aplicaciones** > **Active Directory** > **Directorio** > **Creación personalizada**.

    ![Captura de pantalla del proceso de inicio de creación de un inquilino](./media/active-directory-b2c-get-started/new-directory.png)

3. Elija el **Nombre**, el **Nombre de dominio** y el **País o región** del inquilino.
4. Active la opción que dice "**Este es un directorio B2C**".
5. Haga clic en la marca de verificación para completar la acción.

    ![Captura de pantalla de la marca de verificación para crear un directorio B2C](./media/active-directory-b2c-get-started/create-b2c-directory.png)

6. El inquilino se crea y aparecerá en la extensión de Active Directory. También se crea un administrador global del inquilino. Puede agregar otros administradores globales según sea necesario.

    > [AZURE.IMPORTANT]
    La creación del inquilino puede tardar en completarse hasta dos minutos. Si aparecen problemas durante la creación de inquilinos, consulte [Creación de un inquilino de Azure AD o de Azure AD B2C: problemas y soluciones](active-directory-b2c-support-create-directory.md) para obtener instrucciones.

## Paso 3: Ir a la hoja de características B2C en el Portal de Azure

1. Vaya a la extensión de Active Directory en la barra de navegación del lado izquierdo.
2. Busque su inquilino en la pestaña **Directorio** y haga clic en él.
3. Haga clic en la pestaña **Configurar**.
4. Haga clic en el vínculo **Administrar la configuración B2C** en la sección **Administración B2C**.

    ![Captura de pantalla de la configuración de directorios para B2C](./media/active-directory-b2c-get-started/b2c-directory-configure-tab.png)

5. El Portal de Azure con la hoja de características B2C se abrirá en una nueva pestaña o ventana del explorador.

    > [AZURE.IMPORTANT]
    Hay un problema conocido por el que esta página no se carga correctamente (para un número reducido de inquilinos). Al actualizar el explorador se debería corregir. De lo contrario, póngase en contacto con el soporte técnico de Azure.

6. Ancle esta hoja al panel de inicio para facilitar el acceso (la herramienta Anclar está marcada en rojo en la esquina superior derecha de la hoja de características).

    ![Captura de pantalla de la hoja de características B2C](./media/active-directory-b2c-get-started/b2c-features-blade.png)

    > [AZURE.NOTE]
    Puede administrar usuarios y grupos, la configuración del autoservicio de restablecimiento de contraseña y las características de personalización de marca corporativa de su inquilino en el [Portal de Azure clásico](https://manage.windowsazure.com/).

## Pasos siguientes

Obtenga información acerca de cómo registrar una aplicación con Azure AD B2C y cómo crear una aplicación de inicio rápido; para ello, consulte [Vista previa de Azure Active Directory B2C: Registro de la aplicación](active-directory-b2c-app-registration.md).

<!---HONumber=AcomDC_0302_2016-->