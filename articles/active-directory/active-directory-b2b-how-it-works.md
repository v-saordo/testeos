<properties
   pageTitle="Vista previa de colaboración de Azure AD B2B: funcionamiento | Microsoft Azure"
   description="Describe la forma en que la colaboración de Azure Active Directory B2B posibilita las relaciones entre empresas al permitir que los asociados empresariales accedan de forma selectiva a las aplicaciones corporativas."
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
   ms.date="02/03/2016"
   ms.author="viviali"/>

# Vista previa de la colaboración B2B de Azure AD: funcionamiento
La colaboración de Azure AD B2B se basa en un modelo de invitación y canje. Debe especificar las direcciones de correo electrónico de las partes con las que desea trabajar junto con las aplicaciones que deben usar. Azure AD les envía una invitación por correo electrónico que incluye un vínculo. El usuario del asociado debe seguir el vínculo y, cuando se le indique, debe iniciar sesión con su cuenta de Azure AD o bien suscribirse con una cuenta de Azure AD nueva.

1. El administrador invita a los usuarios del asociado cargando un [archivo .csv estructurado](active-directory-b2b-references-csv-file-format.md) mediante el Portal de Azure.
2. El Portal envía correos electrónicos de invitación a los usuarios del asociado.
3. Los usuarios del asociado hacen clic en el vínculo incluido en el correo electrónico. A continuación, se les pide que inicien sesión con sus credenciales de trabajo (si ya pertenecen a Azure AD) o que se suscriban como usuarios de colaboración en Azure AD B2B.
4. Los usuarios del asociado se redirigen a la aplicación a la que se les invitó, a la que ahora tienen acceso.

## Operaciones del directorio
En Azure AD, los usuarios del asociado se consideran como usuarios externos. Esto significa que el administrador puede aprovisionar licencias, asignar pertenencias a grupos y proporcionar otros accesos a las aplicaciones corporativas mediante el Portal de Azure o usar Azure PowerShell al igual que con otros usuarios de su empresa.

Aunque no es obligatorio contar con una suscripción de pago de Azure AD (Básica o Premium) para usar Azure AD B2B, los inquilinos que dispongan de una suscripción de pago de Azure AD (Básica o Premium) disfrutarán de las siguientes ventajas adicionales:

 - Los administradores pueden asignar grupos a las aplicaciones lo que simplifica la administración del acceso de los usuarios invitados.
 - La personalización de la marca del inquilino de administración se usa para personalizar la marca en los correos electrónicos de invitación y la experiencia de canje, lo que proporciona más contexto a los usuarios invitados del asociado.

## Artículos relacionados
 Examine nuestros otros artículos sobre la colaboración de Azure B2B:

 - [¿Qué es la colaboración de Azure AD B2B?](active-directory-b2b-what-is-azure-ad-b2b.md)
 - [Tutorial detallado](active-directory-b2b-detailed-walkthrough.md)
 - [Referencia de formato de archivo CSV](active-directory-b2b-references-csv-file-format.md)
 - [Formato de token de usuario externo](active-directory-b2b-references-external-user-token-format.md)
 - [Cambios de atributo de objeto de usuario externo](active-directory-b2b-references-external-user-object-attribute-changes.md)
 - [Limitaciones de la vista previa actual](active-directory-b2b-current-preview-limitations.md)
 - [Índice de artículos sobre la administración de aplicaciones en Azure Active Directory](active-directory-apps-index.md)

<!---HONumber=AcomDC_0211_2016-->