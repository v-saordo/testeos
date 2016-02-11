<properties
	pageTitle="Ediciones de Azure Active Directory | Microsoft Azure"
	description="Un tema que explica las opciones para las ediciones gratuitas y de pago de Azure Active Directory. Azure Active Directory Basic es la edición gratuita y Azure Active Directory Premium es la edición de pago."
	services="active-directory"
	documentationCenter=""
	authors="MarkusVi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/25/2016"
	ms.author="markvi"/>

# Ediciones de Azure Active Directory

Todos los servicios de negocios de Microsoft Online dependen de Azure Active Directory para el inicio de sesión y otras necesidades de identidad. Si se suscribe a alguno de los servicios de negocios de Microsoft Online (por ejemplo, Office 365, Microsoft Azure, etc.), recibirá Azure Active Directory (Azure AD) con acceso a todas las características gratuitas que se describen a continuación.

Azure Active Directory es un servicio que proporciona funcionalidades completas de identidad y acceso en la nube para empleados, asociados y clientes. En él se combinan servicios de directorio, regulación avanzada de identidades, una completa plataforma basada en estándares para los desarrolladores y la administración del acceso tanto para sus propias aplicaciones como para otras miles de aplicaciones previamente integradas. Con la edición gratuita de Azure Active Directory puede administrar usuarios y grupos, sincronizar directorios locales, obtener inicio de sesión único entre Azure, Office 365 y miles de aplicaciones SaaS conocidas como Salesforce, Workday, Concur, DocuSign, Google Apps, Box, ServiceNow, Dropbox, etc. Para obtener más información sobre Azure Active Directory, lea [¿Qué es AD?](active-directory-whatis.md).


Para mejorar su Azure Active Directory, puede agregar funcionalidades de pago con las ediciones básica y premium. Las ediciones de pago de Azure Active Directory se crean encima del directorio gratuito existente, y proporcionan funcionalidades de tipo empresarial que abarcan autoservicio, supervisión mejorada, informes de seguridad, Multi-Factor Authentication (MFA) y un acceso seguro para sus trabajadores móviles.

Las suscripciones a Office 365 incluyen características adicionales de Azure Active Directory que se describen en la siguiente tabla de comparación.


> [AZURE.NOTE] Para ver las opciones de precios de estas ediciones, consulte [Precios de Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/). <br>Las ediciones premium y básica de Azure Active Directory no se admiten actualmente en China. Póngase en contacto con nosotros en el Foro de Azure Active Directory para obtener más información.


- **Edición básica de Azure Active Directory**: esta edición, concebida para los trabajadores de tareas con primeras necesidades en la nube, proporciona soluciones de administración de identidades de autoservicio y de acceso a las aplicaciones basado en la nube. Con la edición básica de Azure Active Directory, obtiene características de mejora de la productividad y reducción de costos, como administración de acceso basado en grupo, autoservicio de restablecimiento de contraseña para aplicaciones en la nube y proxy de aplicaciones de Azure Active Directory (para publicar aplicaciones web locales con Azure Active Directory), y todas ellas respaldadas por un contrato de nivel de servicio empresarial con un tiempo de actividad del 99,9 por ciento.
 
- **Edición premium de Azure Active Directory**: esta edición, dirigida a las organizaciones con necesidades más acuciantes de administración de identidades y acceso, agrega completas funcionalidades de administración de identidades de tipo empresarial y permite a los usuarios híbridos acceder sin problemas a funcionalidades locales y en la nube. Incluye todo lo que necesitan los trabajadores de información y los administradores de identidades en los entornos híbridos para el acceso entre aplicaciones, la administración de identidades y acceso de autoservicio (IAM), la protección de identidades y la seguridad en la nube. Admite recursos avanzados de administración y delegación, como grupos dinámicos y administración de grupos de autoservicio. Incluye Microsoft Identity Manager (un conjunto de aplicaciones de administración de identidades y acceso locales) y proporciona funcionalidades de escritura diferida en la nube que permiten soluciones como el autoservicio de restablecimiento de contraseña para los usuarios locales.

Para suscribirse y empezar a usar Active Directory Premium hoy, vea [Introducción a Azure Active Directory Premium](active-directory-get-started-premium.md).


> [AZURE.NOTE] 
Hay varias funcionalidades de Azure Active Directory disponibles mediante las ediciones de "pago por uso":
>
>- Active Directory B2C es la solución de administración de identidades y acceso para las aplicaciones orientados al consumidor. Para obtener más información, consulte [Azure Active Directory B2C](https://azure.microsoft.com/documentation/services/active-directory-b2c/)
 
>-	Azure Multi-Factor Authentication se puede usar a través de proveedores por usuario o por autenticación. Para obtener más detalles, consulte [Qué es Azure Multi-Factor Authentication](multi-factor-authentication.md).


<br>

| Tipo de característica| Características| Edición gratuita| Edición básica| Edición premium| Solo aplicaciones de Office 365 |
| --- | --- | --- | --- | --- | --- |
| **Características frecuentes**| Objetos de directorio [1]| Hasta 500.000 objetos| Sin límite de objetos| Sin límite de objetos| Sin límite de objetos para las cuentas de usuario de Office 365|
| | [Administración de usuarios y grupos (agregar/actualizar/eliminar), aprovisionamiento basado en el usuario](active-directory-administer.md), [registro de dispositivos](active-directory-conditional-access-device-registration-overview.md)| ![Comprobar][12]| ![Comprobar][12]| ![Comprobar][12]| ![Comprobar][12]|
| | [SSO a aplicaciones SaaS, aplicaciones personalizadas, aplicaciones de proxy de aplicación](active-directory-enable-sso-scenario.md)| 10 aplicaciones por usuario [2]| 10 aplicaciones por usuario [2]| sin límite| 10 aplicaciones por usuario [2]|
| | [Cambio de contraseña de autoservicio para usuarios en la nube](active-directory-passwords-update-your-own-password.md)| ![Comprobar][12]| ![Comprobar][12]| ![Comprobar][12]| ![Comprobar][12]|
| | [Connect: para la sincronización entre directorios locales y Azure Active Directory](active-directory-aadconnect.md)| ![Comprobar][12]| ![Comprobar][12]| ![Comprobar][12]| ![Comprobar][12]|
| | **Versión preliminar**:[ colaboración B2B](active-directory-b2b-collaboration-overview.md)| ![Comprobar][12]| ![Comprobar][12]| ![Comprobar][12]| ![Comprobar][12]|
| | [Seguridad/informes de uso](active-directory-view-access-usage-reports.md)| Informes básicos| Informes básicos| Informes avanzados| Informes básicos|
| **Características premium y básicas**| [Aprovisionamiento y administración de acceso a aplicaciones basadas en grupos](active-directory-accessmanagement-group-saasapps.md)| | ![Comprobar][12]| ![Comprobar][12]| |
| | [Restablecimiento de contraseña de autoservicio para usuarios en la nube](active-directory-passwords.md)| | ![Comprobar][12]| ![Comprobar][12]| ![Comprobar][12]|
| | [Personalización de marca de la compañía (personalización de las páginas de inicio de sesión y del panel de acceso)](active-directory-add-company-branding.md)| | ![Comprobar][12]| ![Comprobar][12]| ![Comprobar][12]|
| | [Proxy de aplicación](active-directory-application-proxy-get-started.md)| | ![Comprobar][12]| ![Comprobar][12]| |
| | [Tiempo de actividad de SLA de alta disponibilidad (99,9 %)](https://azure.microsoft.com/support/legal/sla/)| | ![Comprobar][12]| ![Comprobar][12]| ![Comprobar][12]|
| **Características solo de Premium**| Administración de grupos de autoservicio/incorporación de aplicaciones de autoservicio/pertenencia a grupos dinámicos| | | ![Comprobar][12]| |
| | [Restablecimiento de contraseñas de autoservicio, cambio, desbloqueo con escritura diferida local](active-directory-passwords-getting-started.md/#enable-users-to-reset-or-change-their-ad-passwords)| | | ![Comprobar][12]| |
| | [Multi-Factor Authentication (en la nube y local)](multi-factor-authentication.md)| | | ![Comprobar][12]| Se limita a la nube solo para aplicaciones de Office 365|
| | [Licencias de usuario de Microsoft Identity Manager (MIM) y servidor MIM [3]](http://www.microsoft.com/server-cloud/products/microsoft-identity-manager/default.aspx)| | | ![Comprobar][12]| |
| | [Detección de aplicaciones de nube](active-directory-cloudappdiscovery-whatis.md)| | | ![Comprobar][12]| |
| | [Azure Active Directory Connect Health](active-directory-aadconnect-health.md)| | | ![Comprobar][12]| |
| | Sustitución automática de la contraseña para cuentas de grupo| | | ![Comprobar][12]| |
| | **Versión preliminar**: acceso condicional| | | ![Comprobar][12]| |
| | **Versión preliminar**: Privileged Identity Management| | | ![Comprobar][12]| |
| **Características relacionadas con Windows 10 y Azure AD Join**| Conectar un dispositivo de Windows 10 a Azure AD, SSO de escritorio, Microsoft Passport para Azure AD, recuperación de Bitlocker de administrador| ![Comprobar][12]| ![Comprobar][12]| ![Comprobar][12]| ![Comprobar][12]|
| | Inscripción automática de MDM, recuperación de Bitlocker de autoservicio, administradores locales adicionales para dispositivos con Windows 10 a través de Azure AD Join| | | ![Comprobar][12]| |





[1] La cuota de uso predeterminado es de 150.000 objetos. Un objeto es una entrada del servicio de directorio que está representada por un nombre completo único. Un ejemplo de objeto sería una entrada de usuario empleada para la autenticación. Si necesita sobrepasar la cuota predeterminada, póngase en contacto con el servicio de soporte técnico. El límite de 500 000 objetos no se aplica a Office 365, Microsoft Intune o cualquier otro servicio en línea de pago de Microsoft que se base en Azure Active Directory para servicios de directorio.

[2] Con las ediciones Gratis y Básica de Azure AD, los usuarios finales a los que se haya asignado acceso a aplicaciones de SaaS pueden ver hasta 10 aplicaciones en su Panel de acceso y obtener inicio de sesión único en ellas. Los usuarios pueden configurar el inicio de sesión único y asignar acceso de usuario a tantas aplicaciones de SaaS como deseen con los niveles Gratis y Básico; sin embargo, los usuarios finales verán solo 10 aplicaciones en su Panel de acceso a la vez.

[3] Con las licencias de Windows Server (cualquier edición), se conceden derechos de software de servidor de Microsoft Identity Manager. Puesto que Microsoft Identity Manager se ejecuta en el sistema operativo Windows Server, siempre que un servidor ejecute una copia válida y con licencia de Windows Server, Microsoft Identity Manager podrá instalarse y usarse en ese servidor. No se necesita ninguna otra licencia de servidor de Microsoft Identity Manager.



## Pasos siguientes

- [Introducción a Azure Active Directory Premium](active-directory-get-started-premium.md)
- [Incorporación de la marca de empresa a sus páginas de inicio de sesión y panel de acceso](active-directory-add-company-branding.md)
- [Visualización de los informes de acceso y uso](active-directory-view-access-usage-reports.md)


<!--Image references-->
[12]: ./media/active-directory-editions/ic195031.png

<!---HONumber=AcomDC_0128_2016-->