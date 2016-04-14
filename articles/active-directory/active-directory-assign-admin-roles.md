<properties
	pageTitle="Asignación de roles de administrador en Azure Active Directory | Microsoft Azure"
	description="Explica qué roles de administrador están disponibles con Azure Active Directory y cómo asignarlos."
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
	ms.date="02/18/2016"
	ms.author="curtand"/>

# Asignación de roles de administrador en Azure Active Directory (Azure AD)

En función del tamaño de su empresa, puede que quiera designar varios administradores que cumplan funciones diferentes. Esos administradores tendrán acceso a varias características del portal de Azure o el portal de Azure clásico y, según el rol que tengan, podrán crear o editar usuarios, asignar roles administrativos a otros, restablecer contraseñas de usuario o administrar licencias de usuario y dominios, entre otras funciones.

Es importante entender que un usuario al que se le haya asignado un rol de administrador tendrá los mismos permisos en todos los servicios en la nube a los que se haya suscrito la organización, independientemente de si se ha asignado el rol en el portal de Office 365, en el portal de Azure clásico o por medio del módulo de Azure AD para Windows PowerShell.

Los roles de administrador disponibles son los siguientes:

- **Administrador de facturación**: hace compras, administra suscripciones, administra incidencias de soporte técnico y supervisa el estado del servicio.

- **Administrador global**: tiene acceso a todas las características administrativas. La persona que se suscribe a la cuenta de Azure se convierte en un administrador global. Los administradores globales son los únicos que pueden asignar otros roles de administrador. Puede haber más de un administrador global en su empresa.

- **Administrador de contraseñas**: restablece las contraseñas, administra las solicitudes de servicio y supervisa el estado del servicio. Los administradores de contraseñas pueden restablecer contraseñas solo para los usuarios y otros administradores de contraseñas.

- **Administrador de servicios**: administra las solicitudes de servicio y supervisa el estado del servicio.
> [AZURE.NOTE]
> Para asignar el rol de administrador de servicios a un usuario, el administrador global debe asignar primero permisos administrativos al usuario en el servicio, como Exchange Online, y después asignar el rol de administrador de servicios al usuario en el portal de Azure clásico.

- **Administrador de usuarios**: restablece las contraseñas, supervisa el estado del servicio y administra cuentas de usuario, grupos de usuarios y solicitudes de servicio. Existen algunas limitaciones en los permisos de un administrador de usuarios. Por ejemplo, este no puede eliminar a un administrador global ni puede crear otros administradores. Tampoco puede restablecer las contraseñas de los administradores de facturación, globales y de servicio.

## Permisos de administrador

### Administrador de facturación

Puede hacer | No puede hacer
------------- | -------------
<p>Ver información de empresas y usuarios</p><p>Administrar incidencias de soporte técnico de Office</p><p>Hacer operaciones de facturación y compra para los productos de Office</p> | <p>Restablecer contraseñas de usuario</p><p>Crear y administrar vistas de usuarios</p><p>Crear, editar y eliminar usuarios y grupos, y administrar licencias de usuarios</p><p>Administrar dominios</p><p>Administrar información de la empresa</p><p>Delegar roles administrativos a otros</p><p>Utilizar la sincronización de directorios</p>

### Administrador global

Puede hacer | No puede hacer
------------- | -------------
<p>Ver información de empresas y usuarios</p><p>Administrar incidencias de soporte técnico de Office</p><p>Realizar operaciones de facturación y compra para los productos de Office</p> <p>Restablecer contraseñas de usuario</p><p>Crear y administrar vistas de usuarios</p><p>Crear, editar y eliminar usuarios y grupos, y administrar licencias de usuarios</p><p>Administrar dominios</p><p>Administrar información de la empresa</p><p>Delegar roles administrativos a otros</p><p>Usar la sincronización de directorios</p><p>Habilitar o deshabilitar la autenticación multifactor</p> | N/D

### Administrador de contraseñas

Puede hacer | No puede hacer
------------- | -------------
<p>Ver información de empresas y usuarios</p><p>Administrar incidencias de soporte técnico de Office</p><p>Restablecer contraseñas de usuario</p> | <p>Hacer operaciones de facturación y compra para los productos de Office</p><p>Crear y administrar vistas de usuarios</p><p>Crear, editar y eliminar usuarios y grupos, y administrar licencias de usuarios</p><p>Administrar dominios</p><p>Administrar información de la empresa</p><p>Delegar roles administrativos a otros</p><p>Utilizar la sincronización de directorios</p>

### Administrador de servicios

Puede hacer | No puede hacer
------------- | -------------
<p>Ver información de empresas y usuarios</p><p>Administrar incidencias de soporte técnico de Office</p> | <p>Restablecer contraseñas de usuario</p><p>Hacer operaciones de facturación y compra para los productos de Office</p><p>Crear y administrar vistas de usuarios</p><p>Crear, editar y eliminar usuarios y grupos, y administrar licencias de usuarios</p><p>Administrar dominios</p><p>Administrar información de la empresa</p><p>Delegar roles administrativos a otros</p><p>Utilizar la sincronización de directorios</p>

### Administrador de usuarios

Puede hacer | No puede hacer
------------- | -------------
<p>Ver información de empresas y usuarios</p><p>Administrar incidencias de soporte técnico de Office</p><p>Restablecer contraseñas de usuarios, con limitaciones. No puede restablecer las contraseñas para los administradores de facturación, globales y de servicios</p><p>Crear y administrar vistas de usuarios</p><p>Crear, editar y eliminar usuarios y grupos, y administrar licencias de usuarios, con limitaciones. No puede eliminar un administrador global ni crear otros administradores.</p> | <p>Realizar operaciones de facturación y compra para los productos de Office</p><p>Administrar dominios</p><p>Administrar información de la empresa</p><p>Delegar roles administrativos a otros</p><p>Usar la sincronización de directorios</p><p>Habilitar o deshabilitar la autenticación multifactor</p>

## Detalles acerca del rol de administrador global

El administrador global tiene acceso a todos los roles administrativos. De forma predeterminada, a la persona que se suscribe a una suscripción de Azure se le asigna el rol de administrador global para el directorio. Los administradores globales son los únicos que pueden asignar otros roles de administrador.

## Asignación o eliminación de roles de administrador

1. En el Portal de Azure clásico, haga clic en **Active Directory** y luego en el nombre del directorio de su organización.

2. En la página **Usuarios**, haga clic en el nombre para mostrar del usuario que desee editar.

3. En la lista **Rol organizativo**, seleccione el rol de administrador que quiera asignar a este usuario o **Usuario** si quiere quitar un rol de administrador existente.

4. En el cuadro **Dirección de correo electrónico alternativa**, escriba una dirección de correo electrónico. Esta dirección de correo electrónico se usa para notificaciones importantes, incluido el restablecimiento automático de contraseña, por lo que el usuario debe poder tener acceso a la cuenta de correo electrónico independientemente de si tiene acceso a Azure.

5. Seleccione **Permitir** o **Bloquear** para especificar si se permite al usuario iniciar sesión y tener acceso a servicios.

6. Especifique una ubicación en la lista desplegable **Ubicación de uso**.

7. Cuando haya terminado, haga clic en **Guardar**.

## Pasos siguientes

- Para más información acerca de cómo cambiar los administradores de una suscripción de Azure, consulte [Incorporación o cambio de roles de administrador de Azure](../billing-add-change-azure-subscription-administrator.md).

- Para más información sobre cómo se controla el acceso a los recursos en Microsoft Azure, consulte [Descripción de acceso a los recursos de Azure](active-directory-understanding-resource-access.md).

- Para obtener más información sobre cómo se relaciona Azure Active Directory con la suscripción de Azure, consulte [Asociación de las suscripciones de Azure con Azure Active Directory](active-directory-how-subscriptions-associated-directory.md).

- [Administrar usuarios](active-directory-create-users.md)

- [Administrar contraseñas](active-directory-manage-passwords.md)

- [Administrar grupos](active-directory-manage-groups.md)

<!---HONumber=AcomDC_0224_2016-->