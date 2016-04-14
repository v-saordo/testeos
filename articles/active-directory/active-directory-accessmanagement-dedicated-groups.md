<properties
	pageTitle="Grupos dedicados en Azure Active Directory | Microsoft Azure"
	description="Información general sobre cómo funcionan los grupos específicos en Azure Active Directory y cómo se crean."
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="stevenpo"
	editor=""
	/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/09/2016"
	ms.author="curtand"/>

# Grupos dedicados en Azure Active Directory

En Azure Active Directory, los grupos dedicados se crean automáticamente y la pertenencia a grupos dedicados también es automática. No puede agregar miembros a grupos ni quitarlos mediante el Portal de Azure, los cmdlets de Windows PowerShell o de manera programática. Para habilitar grupos dedicados, en la pestaña Configurar del Portal de Azure, defina la opción **Habilitar grupos dedicados en Sí**.

Una vez que la opción Habilitar grupos dedicados se define en **Sí**, puede habilitar el directorio para que cree automáticamente el grupo dedicado Todos los usuarios mediante la definición de la opción **Habilitar el grupo "Todos los usuarios"** en **Sí**. También puede editar el nombre de este grupo dedicado; para ello, escríbalo en el campo **Nombre para mostrar del grupo “**Todos los usuarios**”**.

El grupo dedicado Todos los usuarios puede resultar útil si desea asignar los mismos permisos a todos los usuarios del directorio. Por ejemplo, puede otorgar a todos los usuarios del directorio acceso a una aplicación SaaS si asigna acceso a esta aplicación para el grupo dedicado Todos los usuarios.

Tenga en cuenta que el grupo dedicado "Todos los usuarios" incluye a todos los usuarios del directorio, incluidos los invitados y los usuarios externos. Si necesita un grupo que excluya a los usuarios externos, puede crear un grupo con una regla dinámica como

(user.userPrincipalName -notContains "#EXT#@")

Para crear un grupo que excluya a todos los invitados, use una regla como

(user.userType -ne "Guest")

En este artículo se explica con mayor detalle cómo crear una regla para administrar a los miembros de un grupo de Azure Active Directory:

* [Crear una regla sencilla para configurar pertenencias dinámicas a un grupo](active-directory-accessmanagement-simplerulegroup.md)


Estos artículos proporcionan información adicional sobre Azure Active Directory.

* [Administración del acceso a los recursos con grupos de Azure Active Directory](active-directory-manage-groups.md)
* [Índice de artículos sobre la administración de aplicaciones en Azure Active Directory](active-directory-apps-index.md)
* [¿Qué es Azure Active Directory?](active-directory-whatis.md)
* [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0224_2016-->