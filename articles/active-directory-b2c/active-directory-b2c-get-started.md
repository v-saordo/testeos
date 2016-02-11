<properties
	pageTitle="Vista previa de Azure Active Directory B2C: Creación de un inquilino de Azure Active Directory B2C | Microsoft Azure"
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
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="swkrish"/>

# Vista previa de Azure Active Directory B2C: Creación de un directorio de Azure AD B2C

Para empezar a usar Azure Active Directory (AD) B2C, siga los 3 pasos que se describen a continuación.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Paso 1: Registrarse para obtener una suscripción de Azure

Si ya tiene una suscripción de Azure, vaya al paso siguiente. Si no es así, regístrese para [una suscripción de Azure](sign-up-organization.md) y obtenga acceso a Azure AD B2C.

> [AZURE.NOTE]
La vista previa de Azure AD B2C es actualmente de uso gratuito pero limitado (hasta 50 000 usuarios por directorio). Se necesita una suscripción de Azure para tener acceso al [Portal de Azure clásico](http://manage.windowsazure.com/).

## Paso 2: Crear un inquilino de Azure AD B2C

Use los pasos siguientes para crear un nuevo inquilino de Azure AD B2C. Actualmente, las características B2C no pueden activarse en los directorios existentes, si tiene alguno.

1. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com/) como administrador de la suscripción. Esta cuenta es la misma cuenta profesional o educativa o la misma cuenta Microsoft que usó para suscribirse a Azure.
2. Haga clic en **Nuevo** > **Servicios de aplicaciones** > **Active Directory** > **Directorio** > **Creación personalizada**.

    ![Crear inquilino](./media/active-directory-b2c-get-started/new-directory.png)

3. Elija el **nombre**, el **nombre de dominio** y el **país o la región** del inquilino.
4. Active la opción que dice "**Este es un directorio B2C**".
5. Haga clic en la marca de verificación para completar la acción.

    ![Crear inquilino B2C](./media/active-directory-b2c-get-started/create-b2c-directory.png)

6. El inquilino se crea y aparecerá en la extensión de Active Directory. También se crea un administrador global del inquilino. Puede agregar otros administradores globales según sea necesario.

    > [AZURE.IMPORTANT]
    La creación del inquilino puede tardar en completarse hasta dos minutos. Si se encuentra con problemas durante la creación del inquilino, vea este [artículo](active-directory-b2c-support-create-directory.md) para obtener instrucciones.

## Paso 3: Ir a la hoja Características B2C en el Portal de Azure

1. Vaya a la extensión de Active Directory en la barra de navegación del lado izquierdo.
2. Busque su inquilino en la pestaña **Directorio** y haga clic en él.
3. Haga clic en la pestaña **Configurar**.
4. Haga clic en el vínculo **Administrar la configuración B2C** en la sección **Administración B2C**.

    ![Crear inquilino B2C](./media/active-directory-b2c-get-started/b2c-directory-configure-tab.png)

4. El Portal de Azure con la hoja de características B2C se abrirá en una nueva pestaña o ventana del explorador.

    > [AZURE.IMPORTANT]
    Hay un problema conocido por el que esta página no se carga correctamente (para un número reducido de inquilinos). Al actualizar el explorador se debería corregir. En caso contrario, póngase en contacto con el equipo de soporte técnico.

5. Ancle esta hoja (vea la esquina superior derecha) al panel de inicio para que pueda acceder a ella fácilmente.

    ![Hoja de características B2C](./media/active-directory-b2c-get-started/b2c-features-blade.png)

    > [AZURE.NOTE]
    Puede administrar usuarios y grupos, la configuración del autoservicio de restablecimiento de contraseña y las características de personalización de marca corporativa de su inquilino en el [Portal de Azure clásico](https://manage.windowsazure.com/).

## Pasos siguientes

Para continuar, [registre una aplicación con Azure AD B2C y cree una aplicación de inicio rápido](active-directory-b2c-app-registration.md).

<!---HONumber=AcomDC_0128_2016-->