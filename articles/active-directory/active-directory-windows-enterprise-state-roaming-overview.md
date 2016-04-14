<properties
	pageTitle="Información general de Enterprise State Roaming | Microsoft Azure"
	description="Proporciona información sobre la configuración de Enterprise State Roaming en dispositivos de Windows. Enterprise State Roaming proporciona a los usuarios una experiencia unificada a través de sus dispositivos de Windows y reduce el tiempo necesario para configurar un nuevo dispositivo."
	services="active-directory"
    keywords="qué es Enterprise State Roaming, sincronización empresarial, nube de windows"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory"  
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/04/2016"
	ms.author="femila"/>

# Información general de Enterprise State Roaming

Con Windows 10, los usuarios de [Azure Active Directory (Azure AD)](active-directory-whatis.md) obtienen la capacidad de sincronizar de forma segura su configuración de usuario y los datos de configuración de la aplicación en la nube. Enterprise State Roaming proporciona a los usuarios una experiencia unificada a través de sus dispositivos de Windows y reduce el tiempo necesario para configurar un nuevo dispositivo. Enterprise State Roaming funciona de forma similar a la [sincronización de configuración de consumidor](http://windows.microsoft.com/es-ES/windows-8/sync-settings-pcs) estándar que se presentó en Windows 8. Además, Enterprise State Roaming ofrece:

- **Separación de datos del consumidor y de empresa**: las organizaciones controlan sus datos y no hay ninguna combinación de datos corporativos en una cuenta de nube de consumidor o datos de consumidores en una cuenta de nube de la empresa. 
- **Seguridad mejorada**: los datos se cifran automáticamente antes de salir del dispositivo Windows 10 del usuario mediante Azure Rights Management (Azure RMS) y los datos permanecen cifrados almacenados en la nube. Todo el contenido permanece cifrado en la nube, excepto los espacios de nombres, como nombres de configuración y nombres de aplicación de Windows.  
- **Servicios de administración y supervisión**: más control y visibilidad sobre quién sincroniza la configuración en su organización y en qué dispositivos. 
- **Ubicación geográfica de los datos en la nube**: los datos se almacenarán en una región de Azure según el país del dominio de Azure AD. 



| Artículo | Descripción |
|--------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Habilitación de Enterprise State Roaming en Azure Active Directory](active-directory-windows-enterprise-state-roaming-enable.md) | Enterprise State Roaming está disponible para cualquier organización con una suscripción Premium de Azure Active Directory (Azure AD). Para más información acerca de cómo obtener una suscripción de Azure AD, consulte la página de productos de Azure AD. |
| [Preguntas más frecuentes sobre itinerancia de datos y configuración](active-directory-windows-enterprise-state-roaming-faqs.md) | Este tema responde a algunas preguntas que los administradores de TI podrían tener sobre la sincronización de datos de aplicación y la configuración. |
| [Configuración de MDM y directivas de grupo](active-directory-windows-enterprise-state-roaming-group-policy-settings.md) | Windows 10 proporciona configuración de la directiva de directiva de grupo y de administración de dispositivos móviles (MDM) para limitar la sincronización de configuración. |
| [Referencia de la configuración de movilidad de Windows 10](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md) | Lo siguiente es una lista completa de todas las opciones que se movilizan o se copian en Windows 10. |

<!---HONumber=AcomDC_0204_2016-->