<properties
	pageTitle="Creación de una regla sencilla para configurar pertenencias dinámicas para un grupo | Microsoft Azure"
	description="Explica cómo crear una regla sencilla para configurar pertenencias dinámicas para un grupo."
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
	ms.date="02/09/2016"
	ms.author="curtand"/>


# Creación de una regla sencilla para configurar pertenencias dinámicas para un grupo

Para habilitar la pertenencia dinámica para un grupo determinado, siga estos pasos:

1. En el Portal de Azure, en la pestaña **Grupos**, seleccione el grupo que quiere editar y luego, en la pestaña **Configurar** de este grupo, establezca el conmutador **Habilitar pertenencia dinámica** en **Sí**.

2. Ahora puede configurar una sola regla sencilla para el grupo que controlará cómo funciona la pertenencia dinámica para este grupo. Asegúrese de que la opción **Agregar usuarios donde** está seleccionada y, a continuación, seleccione una propiedad de usuario en la lista (por ejemplo, department, jobTitle, etc.).

3. A continuación, seleccione una condición (Not Equals, Equals, Not Starts With, Starts With, Not Contains, Contains, Not Match, Match), y por último, especifique un valor para la propiedad del usuario seleccionado. Por ejemplo, si un grupo se asigna a una aplicación SaaS y habilita las pertenencias dinámicas para este grupo estableciendo una regla mediante la cual **Agregar usuarios donde** se establece para un puesto de trabajo (jobTitle) con la condición Equals(-eq)Sales Rep, todos los usuarios de su directorio de Azure AD cuyos puestos de trabajo se hayan configurado en Sales Rep tendrán acceso a la aplicación SaaS.

4. Tenga en cuenta que puede configurar una regla de pertenencia dinámica en grupos de seguridad o en los grupos de Office. Las pertenencias dinámicas a grupos requieren que se asigne una licencia de Azure AD Premium al administrador que administra la regla de un grupo y a todos los usuarios seleccionados por la regla para que sean miembros del grupo.

Aquí puede obtener más información sobre reglas complejas para la pertenencia dinámica a grupos:

* [Uso de atributos para crear reglas avanzadas](active-directory-accessmanagement-groups-with-advanced-rules.md)

Estos artículos proporcionan información adicional sobre Azure Active Directory.

* [Administración del acceso a los recursos con grupos de Azure Active Directory](active-directory-manage-groups.md)
* [Índice de artículos sobre la administración de aplicaciones en Azure Active Directory](active-directory-apps-index.md)
* [¿Qué es Azure Active Directory?](active-directory-whatis.md)
* [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0224_2016-->