<properties
	pageTitle="Utilización de nombres de dominio personalizados para simplificar la experiencia de inicio de sesión para los usuarios | Microsoft Azure"
	description="Se explica cómo agregar su propio nombre de dominio a Azure Active Directory y otra información relacionada."
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
	ms.topic="get-started-article"
	ms.date="02/08/2016"
	ms.author="curtand;jeffsta"/>

# Utilización de nombres de dominio personalizados para simplificar la experiencia de inicio de sesión de los usuarios

Puede utilizar sus propios nombres de dominio personalizados para mejorar y simplificar la experiencia del usuario sobre el inicio de sesión y otras experiencias en Azure Active Directory. Por ejemplo, si la organización posee el nombre de dominio ‘contoso.com’, los usuarios pueden iniciar sesión con nombres de usuario con los que están familiarizados, como 'joe@contoso.com'.

Todos los directorios de Azure Active Directory vienen con un nombre de dominio integrado en el formulario 'contoso.onmicrosoft.com', lo que le permite empezar a utilizar Azure u otros servicios de Microsoft. [Más información acerca de los dominios integrados](#built-in-domain-names-for-azure-active-directory).

## Utilización del nombre de dominio personalizado con Azure AD

Si la organización posee un nombre de dominio personalizado que resulta familiar a sus usuarios, es aconsejable utilizar ese nombre de dominio personalizado con Azure Active Directory. Para utilizar un nombre de dominio personalizado en Azure Active Directory debe:

-   [Agregar y comprobar su nombre de dominio personalizado](active-directory-add-domain-add-verify-general.md)

-   [Asignar usuarios al dominio personalizado](active-directory-add-domain-add-users.md)

## Utilización del nombre de dominio personalizado con Office 365 y otros servicios

Al igual que otros recursos en Azure AD, los nombres de dominio personalizado que haya agregado y comprobado pueden utilizarse con Office 365, Intune y otras aplicaciones que utilizan Azure AD. Por ejemplo, utilizar un nombre de dominio personalizado con Exchange Online permite a los usuarios enviar y recibir correo electrónico a direcciones de correo electrónico conocidas como joe@contoso.com. Para habilitar otras aplicaciones para que utilicen dominios personalizados, debe agregar entradas DNS adicionales en el registrador de DNS, como se indica en la aplicación.

-   [Utilización de dominios personalizados con Office 365](https://support.office.com/article/Add-your-users-and-domain-to-Office-365-6383f56d-3d09-4dcb-9b41-b5f5a5efd611?ui=es-ES&rs=es-ES&ad=US)

-   [Utilización de dominios personalizados con Intune](https://technet.microsoft.com/library/dn646966.aspx#BKMK_DomainNames)

## Administración de nombres de dominio en Azure AD

Los nombres de dominio no requieren, por lo general, administración continua en Azure Active Directory.

-   [Consulte la lista de nombres de dominio en Azure Active Directory](active-directory-add-domain-add-users.md)

-   [Qué hacer si se cambia el registrador DNS para el nombre de dominio personalizado](active-directory-add-domain-change-registrar.md)

## Eliminación de un nombre de dominio personalizado

Puede eliminar un nombre de dominio personalizado de Azure AD si su organización ya no utiliza ese nombre de dominio o si necesita utilizar ese nombre de dominio con otro Azure AD.

-   [Eliminación de un nombre de dominio personalizado](#_Deleting_a_custom)

## Nombres de dominio integrados para Azure Active Directory

Indique la diferencia entre los dominios integrados en Azure Active Directory (Azure AD) y los dominios personalizados que agrega.

-   Integrado: el dominio que viene con su directorio de Azure AD como, por ejemplo, contoso.onmicrosoft.com.

-   Personalizado: un nombre de dominio personalizado que su organización ya posee, como contoso.com

## Utilización de PowerShell o API Graph para administrar nombres de dominio

También se pueden completar la mayoría de las tareas de administración para los nombres de dominio de Azure Active Directory mediante Microsoft PowerShell o mediante programación utilizando la API Graph.

-   [Utilización de PowerShell para administrar los nombres de dominio en Azure AD](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)

-   [Utilización de API Graph para administrar los nombres de dominio en Azure AD (vista previa)](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations)


## Pasos siguientes

Si necesita recursos adicionales para comprender la utilización del nombre de dominio en Azure Active Directory, intente:

- [Incorporación de la marca de empresa a sus páginas de inicio de sesión y panel de acceso](active-directory-add-company-branding.md)
- [Incorporar y comprobar un nombre de dominio personalizado en Azure Active Directory](active-directory-add-domain-add-verify-general.md)
- [Asignar usuarios a un dominio personalizado](active-directory-add-domain-add-users.md)
- [Cambiar el registrador DNS para el nombre de dominio personalizado](active-directory-add-domain-change-registrar.md)
- [Eliminar un dominio personalizado en Azure Active Directory](active-directory-add-domain-delete-domain.md)

<!---HONumber=AcomDC_0211_2016-->