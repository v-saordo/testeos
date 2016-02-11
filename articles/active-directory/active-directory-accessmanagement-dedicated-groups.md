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
	ms.date="11/17/2015"
	ms.author="curtand"/>

# Grupos dedicados en Azure Active Directory

En Azure Active Directory, los grupos dedicados se crean automáticamente y la pertenencia a grupos dedicados también es automática. No puede agregar miembros a grupos ni quitarlos mediante el Portal de Azure, los cmdlets de Windows PowerShell o de manera programática. Para habilitar grupos dedicados, en la pestaña Configurar del Portal de Azure, defina la opción **Habilitar grupos dedicados en Sí**.

Una vez que la opción Habilitar grupos dedicados se define en **Sí**, puede habilitar el directorio para que cree automáticamente el grupo dedicado Todos los usuarios mediante la definición de la opción **Habilitar el grupo "Todos los usuarios"** en **Sí**. También puede editar el nombre de este grupo dedicado; para ello, escríbalo en el campo **Nombre para mostrar del grupo “**Todos los usuarios**”**.

El grupo dedicado Todos los usuarios puede resultar útil si desea asignar los mismos permisos a todos los usuarios del directorio. Por ejemplo, puede otorgar a todos los usuarios del directorio acceso a una aplicación SaaS si asigna acceso a esta aplicación para el grupo dedicado Todos los usuarios.

Estos artículos proporcionan información adicional sobre Azure Active Directory.

* [Administración del acceso a los recursos con grupos de Azure Active Directory](active-directory-manage-groups.md)
* [¿Qué es Azure Active Directory?](active-directory-whatis.md)
* [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=Nov15_HO4-->