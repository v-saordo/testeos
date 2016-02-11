<properties
   pageTitle="Cambios de atributos de objeto de usuario externo para la vista previa de colaboración de Azure Active Directory B2B | Microsoft Azure"
   description="Azure Active Directory B2B posibilita las relaciones entre empresas al permitir que los partners empresariales accedan de forma selectiva a las aplicaciones corporativas."
   services="active-directory"
   authors="viv-liu"
   manager="cliffdi"
   editor=""
   tags=""/>

<tags
   ms.service="active-directory"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="na"
   ms.date="10/27/2015"
   ms.author="viviali"/>

# Cambios de atributos de objeto de usuario externo para la vista previa de colaboración de Azure Active Directory (Azure AD) B2B
Cada usuario de un directorio de Azure AD se representa mediante un objeto de usuario. El objeto de usuario en Azure AD sufre cambios de atributo en varias fases del flujo de canje de invitaciones de la colaboración de B2B. El objeto de usuario que representa al usuario asociado en el directorio tiene atributos que cambian en el momento del canje, cuando el partner hace clic en el vínculo en el correo electrónico de invitación. Concretamente:

- Se rellenan los atributos **SignInName** y **AltSecId**
- El atributo **DisplayName** cambia del nombre principal del usuario (user\_fabrikam.com#EXT#@contoso.com) al nombre de inicio de sesión (user@fabrikam.com).

El seguimiento de estos atributos en Azure AD puede ayudarle a solucionar problemas haya canjeado un usuario asociado o no su invitación de colaboración de B2B.

## Artículos relacionados
Examine nuestros otros artículos sobre colaboración de Azure B2B:

- [¿Qué es la colaboración de Azure AD B2B?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Cómo funciona](active-directory-b2b-how-it-works.md)
- [Tutorial detallado](active-directory-b2b-detailed-walkthrough.md)
- [Referencia de formato de archivo CSV](active-directory-b2b-references-csv-file-format.md)
- [Formato de token de usuario externo](active-directory-b2b-references-external-user-token-format.md)
- [Limitaciones de la vista previa actual](active-directory-b2b-current-preview-limitations.md)

<!---HONumber=Nov15_HO1-->