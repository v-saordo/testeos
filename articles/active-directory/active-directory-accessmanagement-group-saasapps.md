
<properties
	pageTitle="Uso de un grupo para administrar el acceso a aplicaciones SaaS| Microsoft Azure"
	description="Cómo usar grupos en Azure Active Directory Premium o Basic para asignar el acceso a aplicaciones SaaS integradas con Azure Active Directory."
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
	ms.date="11/17/2015"
	ms.author="curtand"/>


# Uso de un grupo para administrar el acceso a las aplicaciones SaaS

Con Azure Active Directory (Azure AD) puede usar grupos para asignar el acceso a una aplicación SaaS integrada con Azure AD. Por ejemplo, si desea asignar acceso al departamento de marketing para utilizar cinco aplicaciones SaaS diferentes, puede crear un grupo que contenga a los usuarios del departamento de marketing y, a continuación, asignar ese grupo a las cinco aplicaciones SaaS requeridas por el departamento de marketing. De este modo, ahorrará tiempo administrando la pertenencia a grupos del departamento de marketing en un solo lugar. A continuación, los usuarios se asignan a la aplicación cuando se agregan como miembros del grupo de marketing, y se eliminan sus asignaciones de la aplicación cuando se eliminan del grupo de marketing.

Esta función se puede utilizar con cientos de aplicaciones que puede agregar desde la Galería de aplicaciones de Azure AD.

**Para asignar acceso para un grupo a una aplicación SaaS**

1. Abra el explorador de su preferencia y vaya al Portal de Azure. En el Portal de Azure, encuentre la extensión de Active Directory en la barra de navegación izquierda. En la pestaña **Directorio**, haga clic en el directorio en el que desea asignar el acceso para un grupo a una aplicación Saas.


2. Haga clic en la pestaña **Aplicaciones** del directorio. Seleccione una aplicación que haya agregado desde la Galería de aplicaciones y, a continuación, haga clic en la pestaña **Usuarios y grupos**.

3. En la pestaña **Usuarios y grupos**, en el campo **Comenzando con**, escriba el nombre del grupo al que desea asignar acceso y haga clic en la marca de verificación en la esquina superior derecha. Solo necesita escribir la primera parte del nombre del grupo. Haga clic en el grupo para destacarlo y, a continuación, haga clic en el botón **Asignar acceso** y haga clic en **Sí** cuando aparezca el mensaje de confirmación.


4. También puede ver qué usuarios están asignados a la aplicación, directamente o mediante la pertenencia a un grupo. Para ello, cambie la opción **Mostrar lista desplegable de "Grupos"** a **"Todos los usuarios"**. La lista muestra los usuarios en el directorio y si cada uno de ellos está asignado o no a la aplicación. La lista también muestra si los usuarios asignados están asignados a la aplicación directamente (el tipo de asignación aparece como "Directo"), o en virtud de la pertenencia a un grupo (el tipo de asignación aparece como "Heredado").


> [AZURE.NOTE]Solo después de habilitar Azure AD Premium o Azure AD Basic, verá la pestaña Usuarios y grupos.

Estos artículos proporcionan información adicional sobre Azure Active Directory.

* [Administración del acceso a los recursos con grupos de Azure Active Directory](active-directory-manage-groups.md)

* [¿Qué es Azure Active Directory?](active-directory-whatis.md)

* [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=Nov15_HO4-->