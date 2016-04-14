<properties
   pageTitle="Formato de token de usuario externo para la vista previa de colaboración de Azure Active Directory B2B | Microsoft Azure"
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
   ms.workload="na"
   ms.date="02/09/2016"
   ms.author="viviali"/>

# Vista previa de la colaboración B2B de Azure AD: formato de token de usuario externo

Las notificaciones para un anuncio de Azure estándar token se describen en la [admite tokens y los tipos de notificación](active-directory-token-and-claims.md) artículo en azure.microsoft.com.

Las notificaciones que son diferentes para un usuario autenticado externo de colaboración de B2B son las siguientes:<br/> - **OID:** el id. de objeto del inquilino de recursos<br/> - **TID**: Id. de inquilino del inquilino de recursos<br/> - **Emisor**: trata el inquilino de recursos<br/> - **IDP**: trata el inquilino principal del usuario<br/> - **AltSecId**: es el id. de seguridad alternativo, que es opaco para usted<br/>.

## Artículos relacionados
Examine nuestros otros artículos sobre colaboración de Azure B2B:

- [¿Qué es la colaboración de Azure AD B2B?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Cómo funciona](active-directory-b2b-how-it-works.md)
- [Tutorial detallado](active-directory-b2b-detailed-walkthrough.md)
- [Referencia de formato de archivo CSV](active-directory-b2b-references-csv-file-format.md)
- [Cambios de atributo de objeto de usuario externo](active-directory-b2b-references-external-user-object-attribute-changes.md)
- [Limitaciones de la vista previa actual](active-directory-b2b-current-preview-limitations.md)
- [Índice de artículos sobre la administración de aplicaciones en Azure Active Directory](active-directory-apps-index.md)

<!---HONumber=AcomDC_0211_2016-->