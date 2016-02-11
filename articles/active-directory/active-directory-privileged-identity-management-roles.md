<properties
   pageTitle="Privileged Identity Management de Azure: Roles"
   description="Obtenga información sobre los roles que se usan para identidades con privilegios con la extensión de Privileged Identity Management de Azure."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="na"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="01/21/2016"
   ms.author="kgremban"/>

# Privileged Identity Management de Azure: Roles

<!-- **PLACEHOLDER: Need description of how this works. Azure PIM uses roles from MSODS objects.**-->

## Roles de Azure Active Directory, Office 365 y otros orígenes

Privileged Identity Management (PIM) de Azure utiliza los siguientes roles como roles de administrador predeterminados:

- Administrador global
- Administrador de facturación
- Administrador de servicios
- Administrador de usuarios
- Administrador de contraseñas

Para obtener más información sobre los roles de Office 365, Exchange Online, SharePoint Online y Skype Empresarial, vaya a: [Asignación de roles de administrador en Office 365](https://support.office.com/es-ES/article/Assigning-admin-roles-in-Office-365-eac4d046-1afd-4f1a-85fc-8219c79e1504?ui=es-ES&rs=es-ES&ad=US).

<!--**PLACEHOLDER: The above article may not be the one we want since PIM gets roles from places other that Office 365**-->


<!-- ## The PIM Security Administrator Role **PLACEHOLDER: Need description of the Security Administrator role.**-->

## Roles de usuario e inicio de sesión
> [AZURE.NOTE]Para que un usuario pueda iniciar sesión en PIM de Azure, debe tener una licencia para Azure.

## Asignación de una licencia a un usuario en Azure AD

> [AZURE.NOTE] La opción de licencias sólo aparecerá si realmente existen licencias para esta suscripción.

1. Con una cuenta de administrador global o una cuenta de coadministrador, inicie sesión en [http://manage.windowsazure.com](http://manage.windowsazure.com).
2. Haga clic en **Todos los elementos** en el menú principal.
3. Seleccione el directorio con el que quiere trabajar y que tiene licencias asociadas.
4. Haga clic en **Licencias**. Aparecerá la lista de licencias disponibles.
5. Haga clic en el plan de licencias que contiene las licencias que quiere distribuir.
6. Haga clic en **Asignar usuarios**.
7. Seleccione el usuario al que quiere asignar una licencia.
8. Haga clic en el botón **Asignar**. El usuario ahora puede iniciar sesión en Azure.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Pasos siguientes
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0128_2016-->