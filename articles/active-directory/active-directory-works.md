<properties
	pageTitle="¿Cómo funciona Azure Active Directory? | Microsoft Azure"
	description="Azure Active Directory crea un panorama de identidad en la nube para usted. Se puede conectar a su sistema de identidad local o usar de forma independiente."
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



# ¿Cómo funciona Azure Active Directory?

###Otros artículos sobre este tema
[¿Qué es Azure AD?](active-directory-whatis.md)<br> [¿Cómo funciona?](active-directory-works.md)<br> [Introducción](active-directory-get-started.md)<br> [Pasos siguientes](active-directory-next-steps.md)<br> [Más información](active-directory-learn-map.md)


Azure Active Directory (Azure AD) crea un panorama de identidad en la nube para usted. Se puede conectar a su sistema de identidad local o usar de forma independiente.

Imagínese una cuenta de Azure AD como si fuera un permiso de conducir para la nube: es el id. único para tener acceso a los servicios en línea. En ese sentido, Azure AD funciona como su propio registrador privado en la nube para los permisos de conducir. Habilita identidades que se usarán en cualquier parte de la nube y mejora la movilidad de los usuarios que tienen acceso a recursos locales.

> [AZURE.NOTE] Para usar Azure Active Directory, necesita una cuenta de Azure. Si aún no tiene ninguna, puede [registrarse para obtener una cuenta de Azure gratuita](https://azure.microsoft.com/pricing/free-trial/).

## ¿Cómo Azure AD admite Office 365, Microsoft Intune y otros servicios de Azure?
El portal de Azure, el Centro de administración de Office 365, el portal de cuentas Microsoft Intune y los cmdlets del módulo de PowerShell de Azure AD leen desde y escriben en una sola instancia compartida de Azure AD que está asociada a su directorio. Los portales (o cmdlets) actúan como una interfaz front-end que extrae o cambia la información del directorio. [Más información sobre la compatibilidad con otros servicios](active-directory-administer.md#what-is-an-azure-ad-tenant)

## ¿Cómo Azure AD admite aplicaciones de terceros?
Azure AD simplifica la autenticación para los desarrolladores proporcionando identidad como un servicio, junto con bibliotecas de código abierto para distintas plataformas con el fin de ayudarle a empezar a codificar rápidamente. [Más información sobre escenarios de autenticación para Azure AD](active-directory-authentication-scenarios.md).


## ¿Cómo Azure AD amplía mi directorio local?
Azure Active admite varios de los protocolos de autenticación y autorización más usados. [Más información sobre los protocolos de autenticación de Azure Active Directory](active-directory-authentication-scenarios.md).

## ¿Cómo Azure me ayuda a administrar identidades?
¿Desea obtener más información sobre cómo administrar Azure AD? ¿Cómo obtener un directorio? ¿Cómo eliminar un directorio? ¿Cómo administrar datos de directorios? Más información sobre la administración del directorio de Azure AD. [Más información sobre cómo administrar Azure AD](active-directory-administer.md).

## Recursos adicionales

* [Registro en Azure como una organización](sign-up-organization.md)
* [Identidad de Azure](fundamentals-identity.md)

<!---HONumber=AcomDC_0128_2016-->