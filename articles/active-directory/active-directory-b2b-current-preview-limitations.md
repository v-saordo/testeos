<properties
   pageTitle="Limitaciones actuales de vista previa para la colaboración B2B de Azure Active Directory | Microsoft Azure"
   description="Azure Active Directory B2B posibilita las relaciones entre empresas al permitir que los partners empresariales accedan de forma selectiva a las aplicaciones corporativas."
   services="active-directory"
   documentationCenter=""
   authors="viv-liu"
   manager="cliffdi"
   editor=""
   tags=""/>

<tags
   ms.service="active-directory"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="identity"
   ms.date="02/09/2016"
   ms.author="viviali"/>

# Vista previa de la colaboración B2B de Azure AD: limitaciones de la vista previa actual

- Autenticación multifactor (MFA) no admitida en los usuarios externos. Por ejemplo, si Contoso tiene MFA, pero la organización del partner no la tiene, no se les podrá conceder a los usuarios de la organización del partner MFA mediante la colaboración de B2B.
- Las invitaciones solo son posibles a través de CSV; no se admiten invitaciones individuales y acceso a la API.
- Solo los administradores globales de Azure AD pueden cargar archivos .csv.
- Actualmente no se admiten invitaciones a las direcciones de correo electrónico de consumidor (como hotmail.com, Gmail.com o comcast.net).
- No se ha probado el acceso externo de usuarios a aplicaciones locales.
- Los usuarios externos no se limpian automáticamente cuando el usuario real se elimina de su directorio.
- No se admiten las invitaciones a DL.
- Se pueden cargar un máximo de 2000 registros mediante CSV.

## Artículos relacionados
Examine nuestros otros artículos sobre colaboración de Azure B2B:

- [¿Qué es la colaboración de Azure AD B2B?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Cómo funciona](active-directory-b2b-how-it-works.md)
- [Tutorial detallado](active-directory-b2b-detailed-walkthrough.md)
- [Referencia de formato de archivo CSV](active-directory-b2b-references-csv-file-format.md)
- [Formato de token de usuario externo](active-directory-b2b-references-external-user-token-format.md)
- [Cambios de atributo de objeto de usuario externo](active-directory-b2b-references-external-user-object-attribute-changes.md)
- [Índice de artículos sobre la administración de aplicaciones en Azure Active Directory](active-directory-apps-index.md)

<!---HONumber=AcomDC_0211_2016-->