<properties
	pageTitle="Eliminación de un dominio personalizado en Azure Active Directory | Microsoft Azure"
	description="Cómo eliminar un dominio personalizado en Azure Active Directory."
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
	ms.date="02/05/2016"
	ms.author="curtand;jeffsta"/>


# Eliminación de un nombre de dominio personalizado

Puede eliminar un nombre de dominio personalizado que ya no tenga que utilizar con su directorio de Azure AD si, por ejemplo, ha cambiado de nombre corporativo o si está utilizando un directorio de Azure AD diferente, por nombrar un par de situaciones. También puede eliminar un dominio sin comprobar si, por ejemplo, después de agregarlo se da cuenta de que ha escrito el nombre incorrectamente o no estableció el valor correctamente en cuanto a si el dominio se federará.

## Antes de empezar

Antes de quitar un nombre de dominio, se recomienda que lea la siguiente información:

- No se puede quitar el nombre de dominio contoso.onmicrosoft.com original que se proporcionó para el directorio al suscribirse.
- Un dominio de nivel superior que tenga subdominios asociados no se podrá quitar hasta que se hayan quitado dichos subdominios. Por ejemplo, no puede quitar adatum.com si tiene corp.adatum.com u otro subdominio que use el nombre de dominio de nivel superior. Para obtener más información, consulte el artículo de soporte [Error "El dominio tiene subdominios asociados" o "No se puede quitar un dominio que tenga subdominios" al intentar quitar un dominio de Office 365](https://support.microsoft.com/kb/2787792/).
- ¿Ha activado la sincronización de directorios? Si es así, se habrá agregado automáticamente a su cuenta un dominio con un aspecto similar a este: contoso.mail.onmicrosoft.com. No se puede quitar este nombre de dominio.
- Para poder quitar un nombre de dominio, primero debe quitar el nombre de dominio de todas las cuentas de usuario o correo electrónico asociadas con el dominio. Puede quitar todas las cuentas o bien editarlas en masa para cambiar las direcciones de correo electrónico y la información de nombre de dominio. Para obtener más información, consulte [Creación o edición de usuarios en Azure AD](active-directory-create-users.md). No olvide quitar:

	-   Cualquier usuario que tenga el dominio en su dirección de correo electrónico o nombre de usuario

	-   Cualquier grupo habilitado para correo electrónico que tenga el dominio en su dirección de correo electrónico

	-   Cualquier aplicación que tenga el dominio como parte de su URL de respuesta

- Si hospeda un sitio de SharePoint Online en un nombre de dominio que se utiliza para una colección de sitios de SharePoint Online, debe eliminar la colección de sitios para poder quitar el nombre de dominio.

## Para eliminar un dominio

1.  Inicie sesión en el Portal de Azure clásico usando una cuenta con privilegios de administrador global para ese directorio.

2.  Abra el directorio y seleccione **Dominios**.

3.  Seleccione el dominio y haga clic en Eliminar.

## Pasos siguientes

- [Using custom domain names to simplify the sign-in experience for your users (Uso de nombres de dominio personalizado para simplificar la experiencia de inicio de sesión para los usuarios)](active-directory-add-domain.md)
- [Incorporación de la marca de empresa a sus páginas de inicio de sesión y panel de acceso](active-directory-add-company-branding.md)
- [Add and verify a custom domain name in Azure Active Directory (Incorporación y comprobación de un nombre de dominio personalizado en Azure Active Directory)](active-directory-add-domain-add-verify-general.md)
- [Assign users to a custom domain (Asignación de usuarios a un dominio personalizado)](active-directory-add-domain-add-users.md)
- [Change the DNS registrar for your custom domain name (Cambio del registrador DNS para el nombre de dominio personalizado)](active-directory-add-domain-change-registrar.md)

<!----HONumber=AcomDC_0211_2016-->