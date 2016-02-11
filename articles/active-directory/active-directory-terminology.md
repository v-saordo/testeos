<properties
	pageTitle="Terminología de Azure AD | Microsoft Azure"
	description="Términos y definiciones relacionados con Azure AD."
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

# Terminología de Azure AD

Microsoft Azure Active Directory (Azure AD) tiene un conjunto único de terminología que está relacionada con escenarios en la nube, híbridos y locales. En la tabla siguiente se definen estos términos para proporcionarle información básica sobre su uso.

 Término | Definición
------------- | -------------
Comprobación de seguridad adicional | Opción de configuración de seguridad que un administrador global puede establecer en una cuenta de usuario de un directorio de la organización para requerir que se use una contraseña de usuario y una respuesta desde su teléfono para comprobar la identidad en el sistema de autenticación de Azure Active Directory.
Azure Active Directory | Servicio de identidad de Azure que proporciona capacidades de control de acceso y administración de identidades a través de una API basada en REST.
Control de acceso de Azure Active Directory | Servicio de Azure que proporciona autenticación federada y autorización basada en notificaciones y controlada por reglas para servicios web de REST.
Sistema de autenticación de Azure Active Directory | Servicio de identidad de Microsoft en la nube que se usa para autenticar y autorizar cuentas profesionales o educativas.
Azure Active Directory Graph | Capacidad de Azure Active Directory que tiene acceso a objetos de usuario, grupo y rol dentro de un gráfico de empresa social para emerger fácilmente información del usuario y relaciones.
Módulo Azure Active Directory para Windows PowerShell | Grupo de cmdlets que se usan para administrar Azure Active Directory. Puede usar estos cmdlets para administrar usuarios, grupos, dominios, suscripciones de servicios en la nube, licencias, la sincronización de directorios, el inicio de sesión único, etc.
Azure Active Directory Connect | El asistente de Azure Active Directory Connect es la única herramienta y experiencia guiada para la conexión de sus directorios locales con Azure Active Directory. El asistente implementa y configura todos los componentes necesarios para preparar la integración de directorios, incluidos los servicios de sincronización, la sincronización de contraseñas o los Servicios de federación de Active Directory (ADFS) y los requisitos previos, como el módulo de Azure AD PowerShell.
Herramienta de sincronización de Azure Active Directory | Aplicación que proporciona sincronización unidireccional de objetos de directorio desde un servicio de Active Directory local de una empresa a Azure Active Directory.
Integración de directorios | Característica de Azure Active Directory que se puede configurar para mejorar la experiencia administrativa asociada con el mantenimiento de las identidades en el directorio local y en el directorio en la nube. Los escenarios de integración de directorio incluyen la sincronización de directorios y la sincronización de directorios con inicio de sesión único.
Sincronización de directorios | Se usa para sincronizar objetos de directorio local (usuarios, grupos, contactos) en la nube para reducir la sobrecarga administrativa. La sincronización de directorios se encuentra en el Portal de Azure AD y en el Portal de Azure clásico. Una vez que se ha configurado la sincronización de directorios, los administradores pueden aprovisionar objetos de directorio de una implementación local de Active Directory local a una instancia de Azure AD.
Microsoft Online Services - Ayudante para el inicio de sesión | El Ayudante para el inicio de sesión es una aplicación instalada en un equipo cliente que permite a un usuario iniciar sesión una vez que está en el equipo y, a continuación, obtener acceso a servicios un número de veces ilimitado durante el inicio de sesión único. Sin el Ayudante para el inicio de sesión, los usuarios finales deben proporcionar un nombre y una contraseña cada vez que intentan obtener acceso a un servicio. El Ayudante para el inicio de sesión no se debe confundir con el inicio de sesión único, que es una característica de integración de directorios de Azure Active Directory que se puede implementar para aprovechar las credenciales corporativas locales existentes de usuario para tener acceso sin ningún problema a los servicios en la nube de Microsoft.
Multi-Factor Authentication (también conocida como autenticación en dos fases o 2FA) | Multi-Factor Authentication agrega un segundo nivel de seguridad a los inicios de sesión y las transacciones del usuario. Cuando habilita Multi-Factor Authentication para una cuenta de usuario en Azure AD, el usuario debe usar el teléfono, además de sus credenciales de contraseña estándar, como su método de comprobación de seguridad adicional cada vez que necesite iniciar sesión y usar cualquiera de los servicios en la nube de Microsoft a los que está suscrita su organización.
Inicio de sesión único | Se usa para proporcionar a los usuarios una experiencia de autenticación perfecta cuando tienen acceso a los servicios en la nube de Microsoft mientras están conectados a la red corporativa. Para configurar el inicio de sesión único, las organizaciones deben implementar un servicio de token de seguridad local. Una vez configurado el inicio de sesión único, los usuarios pueden usar sus credenciales corporativas de Active Directory (nombre de usuario y contraseña) para tener acceso a los servicios en la nube y sus recursos locales existentes.
Id. de usuario | Un id. de usuario es un identificador único que un usuario proporciona en la página de inicio de sesión para tener acceso a los servicios en la nube de Microsoft a los que está suscrita su organización.
Cuenta profesional o educativa | Una cuenta de usuario asignada por una organización (profesional, educativa, sin ánimo de lucro) a uno de sus integrantes (un empleado, estudiante, cliente) que proporciona acceso de inicio de sesión a una o más suscripciones de servicios en la nube de Microsoft de la organización, como Office 365 o Azure. Estas cuentas se almacenan en el directorio de Azure AD de una organización y normalmente se eliminan cuando el usuario abandona la organización. Las cuentas profesionales o educativas se diferencian de las cuentas de Microsoft en que es el administrador, y no el usuario, quien las crea y administra en la organización.

## Pasos siguientes
- [Inicio de sesión en Azure como una organización](sign-up-organization.md)
- [Cómo se asocian las suscripciones a Azure con Azure AD](active-directory-how-subscriptions-associated-directory.md)
- [Restricciones y límites del servicio Azure AD](active-directory-service-limits-restrictions.md)

<!---HONumber=AcomDC_0107_2016-->