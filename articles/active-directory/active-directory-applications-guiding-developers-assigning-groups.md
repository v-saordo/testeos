<properties
	pageTitle="Azure AD y aplicaciones: asignación de grupos a una aplicación | Microsoft Azure"
	description="Cómo implementar la asignación de grupos para aplicaciones de Azure."
	services="active-directory"
	documentationCenter=""
	authors="IHenkel"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/03/2015"
	ms.author="inhenk"/>

# Azure AD y aplicaciones: asignación de grupos a una aplicación
Para poder asignar usuarios y grupos a una aplicación, debe requerir la asignación de usuarios. Para obtener información sobre cómo requerir asignación de usuarios, consulte el artículo [Necesidad de asignación de usuario](active-directory-applications-guiding-developers-requiring-user-assignment.md).

En este artículo se supone que ya ha creado grupos en el Active Directory que está usando para esta aplicación.

## Asignación de grupos a una aplicación
1. Inicie sesión en el portal de Azure con una cuenta de administrador.
2. Haga clic en el elemento **Todos los elementos** del menú principal.
3. Elija el directorio que está usando para la aplicación.
4. Haga clic en la pestaña **APLICACIONES**.
5. Seleccione la aplicación en la lista de aplicaciones asociada a este directorio.
6. Haga clic en la pestaña **USUARIOS Y GRUPOS**.
7. Filtre la lista de grupos en Active Directory mediante la lista desplegable **Grupos**.
8. Seleccione el grupo.
9. Haga clic en **ASIGNAR**.
10. Haga clic en **Sí** cuando se le solicite.

## Pasos siguientes
[AZURE.INCLUDE [active-directory-applications-guiding-developers-for-lob-applications-toc.md](../../includes/active-directory-applications-guiding-developers-for-lob-applications-toc.md)]

<!---HONumber=AcomDC_1210_2015-->