<properties
   pageTitle="Evento 'Actor desconocido' de informes de Azure Active Directory | Microsoft Azure"
   description="Descripción del evento 'Actor desconocido' en los informes de Azure Active Directory"
   services="active-directory"
   documentationCenter=""
   authors="kenhoff"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="12/07/2015"
   ms.author="kenhoff"/>

# Evento 'Actor desconocido' de informes de Azure Active Directory

*Esta documentación forma parte de la [guía de informes de Azure Active Directory](active-directory-reporting-guide.md).*

En raras ocasiones, puede encontrar valores inusuales en los campos "Actor" o "Usuario" de los informes de Azure AD. Este comportamiento es normal y se debe a dos eventos:

## Una entidad de servicio está actuando en el directorio, sin un contexto de usuario

En este caso, una entidad de servicio (aplicación) está realizando actualizaciones de directorio sin iniciar sesión realmente como usuario. Esto hace que el id. de la entidad de servicio se muestre como Actor, en lugar del nombre principal de usuario. Este es un ejemplo:

![](./media/active-directory-reporting-unknown-actor/spd-actor.png)

Se trata de un error conocido y estamos trabajando diligentemente para resolverlo.

## Se eliminó un usuario del directorio antes de que se procesara el evento

En este caso, un usuario se eliminó del directorio antes de que se procesara el evento y se le asociara un nombre de usuario. Este es un ejemplo:

![](./media/active-directory-reporting-unknown-actor/unknown-actor.png)

Se trata de un error conocido y estamos trabajando diligentemente para resolverlo.

<!-- ![](./media/active-directory-reporting-unknown-actor/uid-actor.png) -->

<!---HONumber=AcomDC_1210_2015-->