<properties 
	pageTitle="Introducción a Azure Multi-Factor Authentication y a los Servicios de federación de Active Directory" 
	description="En esta página de Azure Multi-Factor Authentication se describe cómo empezar a trabajar con Azure MFA y AD FS" 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtland"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" ms.topic="get-started-article" 
	ms.date="02/25/2016" 
	ms.author="billmath"/>

# Introducción a Azure Multi-Factor Authentication y a los Servicios de federación de Active Directory



<center>![Cloud](./media/multi-factor-authentication-get-started-adfs/adfs.png)</center>

Si su organización ha federado su Active Directory local con Azure Active Directory mediante AD FS, tiene a su disposición las dos opciones siguientes para usar Multi-Factor Authentication.

- Recursos de nube seguros a través del uso de Azure Multi-Factor Authentication o los Servicios de federación de Active Directory 
- Protección de recursos en la nube y locales mediante Servidor Azure Multi-Factor Authentication 

En la tabla siguiente se resume la experiencia de autenticación entre la protección de recursos con Azure Multi-Factor Authentication y AD FS

|Experiencia de autenticación: aplicaciones basadas en explorador|Experiencia de autenticación: aplicaciones no basadas en explorador
:------------- | :------------- | :------------- |
Protección de recursos de Azure AD con Azure Multi-Factor Authentication |<li>El primer factor de autenticación se realiza localmente utilizando AD FS.</li> <li>El segundo factor es un método basado en teléfono que se lleva a cabo utilizando la autenticación en la nube.</li>|Los usuarios finales pueden usar contraseñas de aplicación para iniciar sesión.
Protección de los recursos de Azure AD mediante los servicios de federación de Active Directory |<li>El primer factor de autenticación se realiza localmente utilizando AD FS.</li><li>El segundo factor se realiza localmente respondiendo a la notificación.</li>|Los usuarios finales pueden usar contraseñas de aplicación para iniciar sesión.

Advertencias sobre las contraseñas de aplicación para usuarios federados:

- La contraseña de aplicación se comprueba mediante autenticación en la nube y, por tanto, omite las federaciones. La federación solo se usa activamente al configurar la contraseña de aplicación.
- La configuración del control de acceso de cliente local no admite la contraseña de aplicación
- Se pierde la capacidad de registro de autenticación local para la contraseña de aplicación.
- La deshabilitación o eliminación de la cuenta puede tardar hasta 3 horas en la sincronización de directorios, retrasando la deshabilitación o eliminación de la contraseña de aplicación en la identidad de nube.

Para obtener información sobre cómo configurar Azure Multi-Factor Authentication o Servidor Azure Multi-Factor Authentication con AD FS, consulte lo siguiente:

- [Protección de recursos en la nube con Azure Multi-Factor Authentication y AD FS](multi-factor-authentication-get-started-adfs-cloud.md)
- [Protección de recursos en la nube y locales mediante Servidor Azure Multi-Factor Authentication con Windows Server 2012 R2 AD FS](multi-factor-authentication-get-started-adfs-w2k12.md)
- [Protección de recursos en la nube y locales mediante Servidor Azure Multi-Factor Authentication con AD FS 2.0](multi-factor-authentication-get-started-adfs-adfs2.md)







 

<!---HONumber=AcomDC_0302_2016-->