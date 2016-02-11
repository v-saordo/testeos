<properties
	pageTitle="Administración de contraseñas en Azure Active Directory | Microsoft Azure"
	description="Cómo administrar contraseñas en Azure Active Directory."
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
	ms.date="01/05/2016"
	ms.author="curtand"/>

# Administración de contraseñas en Azure Active Directory

Si es administrador, puede restablecer la contraseña de un usuario en Azure Active Directory (Azure AD) en el Portal de Azure clásico. Haga clic en el nombre del directorio y en la página Usuarios, haga clic en el nombre del usuario y en la parte inferior del portal, haga clic en **Restablecer contraseña**.

El resto de este tema abarca el conjunto completo de funcionalidades de administración de contraseñas que Azure AD admite, y que incluye:

- El cambio de la **contraseña de autoservicio** permite a los usuarios finales o administradores cambiar sus contraseñas expiradas o no expiradas sin llamar a un administrador o departamento de soporte técnico para obtener soporte técnico.
- El restablecimiento de la **contraseña de autoservicio** permite a los usuarios finales o administradores restablecer sus contraseñas automáticas sin llamar a un administrador o departamento de soporte técnico para obtener soporte técnico. El restablecimiento de la contraseña de autoservicio requiere Azure AD Premium o Básico. Para obtener más información, consulte [Ediciones de Azure Active Directory](active-directory-editions.md).
- El **restablecimiento de contraseña iniciado por el administrador** permite a un administrador restablecer la contraseña de un usuario final o de otro administrador desde el Portal de Azure clásico.
- Los **informes de actividad de administración de contraseñas** proporcionan a los administradores perspectivas sobre una actividad de registro y restablecimiento de contraseña en su organización.
- La **escritura diferida de la contraseña** permite la administración de contraseñas locales desde la nube, por lo que todos los escenarios anteriores pueden realizarlos los usuarios sincronizados con contraseña y federados, o en nombre de ellos. La escritura diferida de la contraseña requiere Azure AD Premium. Para obtener más información, consulte [Introducción a Azure Active Directory Premium](active-directory-get-started-premium.md).

> [AZURE.NOTE]
Azure AD Premium está disponible para los clientes chinos con la instancia de todo el mundo de Azure AD. Azure AD Premium no es compatible actualmente en el servicio de Microsoft Azure operado por 21Vianet en China. Para obtener más información, póngase en contacto con nosotros en el [foro de Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/).

Utilice los siguientes vínculos para ir a la documentación en la que esté más interesado:

- [Información general: administración de contraseñas en Azure AD](active-directory-passwords-how-it-works.md)
- [Restablecimiento de la contraseña de autoservicio: habilitación, configuración y prueba de restablecimiento de la contraseña de autoservicio](active-directory-passwords-getting-started.md#enable-users-to-reset-their-azure-ad-passwords)
- [Restablecimiento de la contraseña de autoservicio: personalización del restablecimiento de la contraseña para satisfacer sus necesidades](active-directory-passwords-customize.md)
- [Restablecimiento de la contraseña de autoservicio: prácticas recomendadas de implementación y administración](active-directory-passwords-best-practices.md)
- [Informes de administración de contraseñas de Azure AD: visualización de la actividad de administración de contraseñas en el inquilino](active-directory-passwords-get-insights.md)
- [Escritura diferida de contraseñas: configuración de Azure AD para administrar las contraseñas locales](active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords)
- [Solución de problemas de la administración de contraseñas de Azure AD](active-directory-passwords-troubleshoot.md)
- [Preguntas más frecuentes para la administración de contraseñas de Azure AD](active-directory-passwords-faq.md)


## Pasos siguientes

- [Administración de Azure AD](active-directory-administer.md)
- [Creación o edición de usuarios en Azure AD](active-directory-create-users.md)
- [Administración de grupos en Azure AD](active-directory-manage-groups.md)

<!---HONumber=AcomDC_0128_2016-->