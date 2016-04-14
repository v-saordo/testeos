<properties
	pageTitle="Asignación de usuarios a un dominio personalizado en Azure Active Directory | Microsoft Azure"
	description="Cómo rellenar un dominio personalizado en Azure Active Directory con cuentas de usuario."
	services="active-directory"
	documentationCenter=""
	authors="jeffsta"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/03/2016"
	ms.author="curtand;jeffsta"/>

# Asignación de usuarios a un dominio personalizado

Una vez que haya agregado el dominio personalizado a Azure Active Directory, debe agregar las cuentas de usuario de este dominio para que pueda comenzar a autenticarlas.

## Usuarios sincronizados desde un directorio de la red corporativa

Si ya estableció una conexión entre su Active Directory local y Azure Active Directory, puede rellenar las cuentas mediante la sincronización. Para más información sobre cómo sincronizar Azure Active Directory con su Active Directory local, consulte [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md).

## Usuarios que se agregan y administran en la nube

Para cambiar el dominio de una cuenta de usuario existente:

1.  Abra el Portal de Azure clásico con una cuenta que sea un administrador global o un administrador del usuario.

2.  Abra el directorio.

3.  Seleccione la pestaña **Usuarios**.

4.  Seleccione el usuario de la lista.

5.  Cambie el dominio del usuario y seleccione **Guardar**.

Esto también puede hacerse con [Microsoft PowerShell](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains) o [API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations).

## Selección de un dominio personalizado al crear un nuevo usuario

1.  Abra el Portal de Azure clásico con una cuenta que sea un administrador global o un administrador del usuario.

2.  Abra el directorio.

3.  Seleccione la pestaña **Usuarios**.

4.  En la barra de comandos, haga clic en **Agregar**.

5.  Cuando agregue el nombre de usuario, elija el dominio personalizado de la lista de dominios.

6.  Seleccione **Guardar**.

## Pasos siguientes

- [Incorporación de su propio nombre de dominio a Azure Active Directory](active-directory-add-domain.md)
- [Incorporación de la marca de empresa a sus páginas de inicio de sesión y panel de acceso](active-directory-add-company-branding.md)
- [Add and verify a custom domain name in Azure Active Directory (Incorporación y comprobación de un nombre de dominio personalizado en Azure Active Directory)](active-directory-add-domain-add-verify-general.md)
- [Qué hacer si se cambia el registrador DNS del nombre de dominio personalizado](active-directory-add-domain-change-registrar.md)
- [Deleting a custom domain name (Eliminación de un nombre de dominio personalizado)](active-directory-add-domain-delete-domain.md)

<!---HONumber=AcomDC_0211_2016-->