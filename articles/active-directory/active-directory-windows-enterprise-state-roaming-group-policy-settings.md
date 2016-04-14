<properties
	pageTitle="Configuración de MDM y directivas de grupo | Microsoft Azure"
	description="Proporciona información sobre la configuración de directiva de grupo y la administración de dispositivos móviles (MDM) que debe usarse en dispositivos de empresa. Estas directivas se aplican al dispositivo completo del usuario."
	services="active-directory"
    keywords="¿cuál es la configuración de directiva de grupo y MDM para Enterprise State Roaming, Enterprise State Roaming, nube de windows"
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

# Configuración de MDM y directivas de grupo

Use esta configuración de directiva de grupo y de dispositivos móviles (MDM) de solo en dispositivos de empresa, dado que estas directivas se aplican en todo el dispositivo del usuario. Aplicar una directiva MDM para deshabilitar la sincronización de configuración para un dispositivo de usuario personal ejercerá un impacto negativo en el uso de ese dispositivo. Además, otras cuentas de usuario en el dispositivo también se verán afectadas por la directiva.

Las empresas que desean administrar la movilidad para sus dispositivos personales (no administrados) pueden usar el Portal de Azure para habilitar o deshabilitar la movilidad, en lugar de usar la directiva de grupo o MDM. Las tablas siguientes describen la configuración de la directiva disponible.

## Configuración de MDM
La configuración de directiva MDM se aplica a Windows 10 y Windows 10 Mobile.

| Nombre | Descripción |
|------------------------------------|----------------------------------------------------------------------|
| Permitir la conexión con una cuenta de Microsoft | Permite a los usuarios autenticarse con una cuenta de Microsoft en el dispositivo. |
| Permitir la sincronización de mi configuración | Permite a los usuarios movilizar los datos de la aplicación y la configuración de Windows. |
 
## Configuración de directiva de grupo
La configuración de directiva de grupo se aplica a dispositivos de Windows 10 que están unidos a un dominio de Active Directory. La tabla incluye la configuración heredada que aparecerá para administrar la configuración de sincronización, pero que no funcionan para Enterprise State Roaming para Windows 10.

| Nombre | Descripción |
|-------------------------------------|-------------|
| Cuentas: bloquear cuentas Microsoft |Esta configuración de directiva impide a los usuarios agregar nuevas cuentas de Microsoft en este equipo.|
| No sincronizar |Permite a los usuarios movilizar los datos de la aplicación y la configuración de Windows.|
| No sincronizar personalización |Deshabilita la sincronización del grupo Temas.|
| No sincronizar la configuración del explorador |Deshabilita la sincronización del grupo Internet Explorer.|
| No sincronizar contraseñas |Deshabilita la sincronización del grupo Contraseñas.|
| No sincronizar otra configuración de Windows |Deshabilita la sincronización del grupo Otra configuración de Windows.|
| No sincronizar la personalización del escritorio |No se usa; no tiene efecto.|
| No sincronizar con conexiones de uso medido |Deshabilita la movilidad en conexiones limitadas, como 3G móvil.|
| No sincronizar aplicaciones |No se usa; no tiene efecto.|
|No sincronizar la configuración de aplicaciones |Deshabilita la movilidad de datos de la aplicación.|
|No sincronizar la configuración de inicio |No se usa; no tiene efecto.|


## Temas relacionados
- [Información general de Enterprise State Roaming](active-directory-windows-enterprise-state-roaming-overview.md)
- [Habilitación de Enterprise State Roaming en Azure Active Directory](active-directory-windows-enterprise-state-roaming-enable.md)
- [Preguntas más frecuentes sobre itinerancia de datos y configuración](active-directory-windows-enterprise-state-roaming-faqs.md)
- [Referencia de la configuración de movilidad de Windows 10](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)

<!---HONumber=AcomDC_0204_2016-->