<properties
	pageTitle="Creación o edición de usuarios en Azure Active Directory | Microsoft Azure"
	description="Explica cómo crear o editar cuentas de usuario en Azure Active Directory."
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/05/2016"
	ms.author="curtand"/>

# Creación o edición de usuarios en Azure Active Directory

Hay que crear una cuenta en Azure Active Directory (Azure AD) para cada usuario que vaya a tener acceso a un servicio en la nube de Microsoft. También puede cambiar las cuentas de usuario o eliminarlas cuando ya no sean necesarias. De forma predeterminada, los usuarios no tienen permisos de administrador, pero puede asignárselos si lo desea.

## Creación de un usuario

1. Haga clic en **Active Directory** y después haga clic en el nombre del directorio de su organización.
2. En la página **Usuarios**, haga clic en **Agregar usuario**.
3. En la página **Proporcione información sobre este usuario**, elija una de estas opciones en **Tipo de usuario**:
	1. **Nuevo usuario de la organización**: indica que desea que se cree una nueva cuenta de usuario y se administre en el directorio.
	2. **Usuario con una cuenta Microsoft existente**: indica que desea agregar una cuenta de Microsoft existente a su directorio para colaborar en los recursos de Azure con un coadministrador que acceda a Azure con una cuenta de Microsoft.
	3. **Usuario en otro directorio de Azure AD**: indica que desea agregar una cuenta de usuario a su directorio, cuyo origen es otro directorio de Azure AD. Deberá ser miembro del otro directorio para poder seleccionar un usuario en él.
4. Según la opción seleccionada, escriba un nombre de usuario o el nombre de la cuenta de Microsoft con la que el usuario iniciará sesión.
5. En la página **Perfiles** del usuario, especifique el nombre, los apellidos y un nombre descriptivo. Elija también un rol en el menú desplegable Roles. Para obtener más información acerca de los roles del usuario y el administrador, consulte [Asignación de roles de administrador en Azure AD](active-directory-assign-admin-roles.md). Especifique si se debe **habilitar la autenticación multifactor**.
6. En la página **Obtener contraseña temporal**, haga clic en **Crear**.

Si su organización usa más de un dominio, debe tener en cuenta los siguientes problemas al crear una cuenta de usuario:

- Puede crear cuentas de usuario con el mismo nombre principal de usuario (UPN) en los dominios si crea en primer lugar, por ejemplo, geoffgrisso@contoso.onmicrosoft.com seguido de geoffgrisso@contoso.com.
- No se puede crear geoffgrisso@contoso.com seguido de geoffgrisso@contoso.onmicrosoft.com.

## Edición de un usuario

Si el usuario que está intentando editar está sincronizado con el servicio de Active Directory local, aparece un mensaje de error y no será posible editar el usuario mediante este procedimiento. Para editar el usuario, utilice las herramientas de administración locales de Active Directory.

Para editar un usuario en el Portal de Azure clásico:

1. Haga clic en **Active Directory** y, a continuación, haga clic en el nombre del directorio de su organización.
2. En la página **Usuarios**, haga clic en el nombre para mostrar del usuario que desee editar.
3. Termine los cambios y, a continuación, haga clic en **Guardar**.

## Restablecimiento de la contraseña del usuario

1. Haga clic en **Active Directory** y después haga clic en el nombre del directorio de su organización.
2. En la página **Usuarios**, haga clic en el nombre para mostrar del usuario que desee editar.
3. En la parte inferior del portal, haga clic en **Restablecer contraseña**.
4. En el cuadro de diálogo Restablecer contraseña, haga clic en **Restablecer**.
5. Haga clic en la marca de verificación para confirmar que se ha restablecido la contraseña.

## Creación de un usuario externo

En Azure AD, también puede agregar usuarios a un directorio de Azure AD desde otro directorio de Azure AD o un usuario con una cuenta de Microsoft. Un usuario puede ser miembro de hasta 20 directorios distintos.

Los usuarios que se agregan desde otro directorio se consideran "usuarios externos". Los usuarios externos pueden colaborar con los usuarios que ya existen en un directorio (por ejemplo, en un entorno de prueba) sin necesidad de iniciar sesión con las credenciales y cuentas nuevas. Los usuarios externos se autentican mediante su directorio particular cuando inician sesión. Esta autenticación funciona para todos los directorios de los que sean miembros.

Para crear un usuario externo, cree un usuario en el Portal de Azure clásico y, en **Tipo de usuario**, seleccione **Usuario en otro directorio de Azure AD**.

## Limitaciones y administración de usuarios externos

Cuando se agrega un usuario desde un directorio a un nuevo directorio, ese usuario se considera como un usuario externo en el nuevo directorio. Inicialmente, se copian el nombre para mostrar y el nombre de usuario desde el directorio particular del usuario y se pegan en el usuario externo en el otro directorio. A partir de ese momento, esas y otras propiedades del objeto de usuario externo son totalmente independientes; es decir, si se realiza un cambio en el usuario en el directorio particular (por ejemplo, cambiar el nombre de usuario, agregar un puesto de trabajo, etc.), esos cambios no se propagan a la cuenta del usuario externo en el otro directorio.

La única vinculación entre los dos objetos es que el usuario siempre se autentica en el directorio particular o con su cuenta de Microsoft. Por eso, no se muestra una opción para restablecer la contraseña ni habilitar la autenticación multifactor para una cuenta de usuario externo: actualmente, la directiva de autenticación del directorio particular o la cuenta de Microsoft es la única que se evalúa cuando el usuario inicia sesión.

> [AZURE.NOTE]Si lo desea, puede deshabilitar el usuario externo en el directorio y esta acción bloqueará el acceso al directorio.

Si se elimina un usuario en su directorio particular o se cancela su cuenta de Microsoft, el usuario externo sigue existiendo en el directorio. Sin embargo, el usuario no puede tener acceso a los recursos del directorio porque ya no puede autenticarse en su directorio particular ni en la cuenta de Microsoft.

Un usuario que sea administrador de varios directorios puede administrar cada uno de esos directorios en el Portal de Azure clásico. Sin embargo, otras aplicaciones, como Office 365, no proporcionan actualmente experiencias para asignar servicios como un usuario externo en otro directorio ni permiten acceder a ellos. A partir de ahora, daremos instrucciones a los desarrolladores sobre cómo pueden funcionar sus aplicaciones con usuarios que sean miembros de varios directorios.

Actualmente, existen ciertas limitaciones porque un administrador solo puede conceder consentimiento a una aplicación multiempresa en su directorio particular, y el aprovisionamiento solo se puede realizar para aplicaciones SaaS y SSO a través del Panel de acceso del directorio particular. Los usuarios de cuentas de Microsoft tienen las mismas limitaciones, ya que actualmente no pueden conceder consentimiento para una aplicación multiempresa ni usar el Panel de acceso.

## Invitados

Un **invitado** es un usuario del directorio cuyo Tipo de usuario está establecido en "Invitado". Los usuarios normales tienen un tipo de usuario de "Miembro" para indicar que son miembros del directorio. Los invitados se crean cuando se comparte un recurso con alguien externo al directorio, por ejemplo, al agregar una cuenta de Microsoft a su suscripción de Azure o al compartir un documento en SharePoint con un usuario externo.

Los invitados tienen un conjunto limitado de derechos en el directorio. Estos derechos limitan la posibilidad de que los invitados descubran información sobre otros usuarios del directorio, aunque pueden interactuar con los usuarios y grupos asociados a los recursos en los que están trabajando. Por ejemplo, un invitado asignado a una suscripción de Azure podrá ver a otros usuarios y grupos asociados a la suscripción de Azure. También es posible buscar a otros usuarios del directorio a los que haya que concederles acceso a la suscripción, siempre que se conozca la dirección de correo electrónico completa del usuario. Un invitado solo puede ver un conjunto limitado de propiedades de otros usuarios. Estas propiedades se limitan al nombre para mostrar, la dirección de correo electrónico, el nombre principal del usuario (UPN) y la foto en miniatura.

## Configuración de las directivas de acceso del usuario

La pestaña **Configurar** de un directorio incluye opciones para controlar el acceso de los usuarios externos. Estas opciones solo se pueden cambiar en la interfaz de usuario (no hay ningún método de Windows PowerShell o API) en el Portal de Azure clásico completo y solo puede hacerlo un administrador global de directorio. Para abrir la pestaña **Configurar** del Portal de Azure clásico, haga clic en **Active Directory** y luego haga clic en el nombre del directorio.

![][1]

A continuación, puede editar las opciones para controlar el acceso de usuarios externos.

![][2]

De forma predeterminada, los invitados no pueden mostrar el contenido del directorio y, por tanto, no ven a ningún usuario ni grupo en la **Lista de miembros**. Pueden buscar un usuario si escriben la dirección de correo electrónico completa del usuario y luego conceden acceso. El conjunto de restricciones predeterminadas para los usuarios son:

- No pueden enumerar usuarios ni grupos en el directorio.
- Pueden ver información limitada de un usuario si conocen la dirección de correo electrónico del usuario.
- Pueden ver información limitada de un grupo si conocen el nombre del grupo.

La capacidad de los invitados para ver información limitada de un usuario o grupo les permite invitar a otras personas y ver algunos detalles de la gente con la que colaboran.

## Pasos siguientes

- [Administración de Azure AD](active-directory-administer.md)
- [Administración de contraseñas en Azure AD](active-directory-manage-passwords.md)
- [Administración de grupos en Azure AD](active-directory-manage-groups.md)

<!--Image references-->
[1]: ./media/active-directory-create-users/RBACDirConfigTab.png
[2]: ./media/active-directory-create-users/RBACGuestAccessControls.png

<!---HONumber=AcomDC_0107_2016-->