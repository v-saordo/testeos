<properties 
	pageTitle="Escenarios de uso y consideraciones de implementación de Azure AD Join | Microsoft Azure" 
	description="Se explica cómo los administradores pueden configurar Azure AD Join para sus usuarios finales (empleados, estudiantes, otros usuarios). También describe los distintos escenarios reales de uso de Azure AD Join." 
	services="active-directory" 
	documentationCenter="" 
	authors="femila" 
	manager="stevenpo" 
	editor=""
	tags="azure-classic-portal"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/19/2015" 
	ms.author="femila"/>

# Escenarios de uso y consideraciones de implementación de Azure AD Join 

## Escenarios de uso de Azure AD Join
Escenario 1: Empresas principalmente en la nube
--------------------------------------------------------
Azure AD Join puede resultarle ventajoso si actualmente opera y administra identidades para su negocio en la nube o tiene pensado trasladarse a la nube en un futuro cercano. Puede usar una cuenta que haya creado en Azure AD para iniciar sesión en Windows 10. Mediante [el proceso de configuración rápida](active-directory-azureadjoin-user-frx.md) o la unión a Azure AD mediante [la experiencia de configuración básica](active-directory-azureadjoin-user-upgrade.md), sus usuarios pueden unir sus equipos a Azure AD. Ahora, los usuarios disfrutan de acceso SSO a sus recursos de nube, como Office 365 en el explorador o en las aplicaciones de Office.

Escenario 2: Instituciones educativas
----------------------------------------------------------------------------------
Las instituciones educativas tienen habitualmente dos tipos de usuarios: profesores y estudiantes. A los profesores se les considera miembros más a largo plazo de la organización, y lo deseable es crear cuentas locales para ellos. Pero los estudiantes son miembros más a corto plazo de la organización y, por lo tanto, se pueden administrar en Azure AD de modo que la escala de directorio se puede mover a la nube en lugar de al entorno local. Los estudiantes ahora pueden iniciar sesión en Windows con su cuenta de Azure AD y obtener acceso a los recursos de Office 365 en las aplicaciones de Office.

Escenario 3: Los negocios minoristas
---------------------------------------------------------------------------------------
El sector minorista tiene trabajadores de temporada y empleados a largo plazo. Los empleados a jornada completa, más a largo plazo, se pueden crear como cuentas locales y usarían normalmente máquinas unidas a un dominio. Sin embargo, los trabajadores de temporada son miembros más a corto plazo de la organización y, por lo tanto, es necesario administrarlos con licencias de usuario que se puedan mover fácilmente. La creación de estos usuarios en la nube con licencias de Office 365 permite a estos usuarios obtener los beneficios de iniciar sesión en aplicaciones de Windows y Office con una cuenta de Azure AD, y mantener al mismo tiempo la movilidad de sus licencias una vez que abandonan su puesto de trabajo.

Escenario 4: Otros escenarios
------------------------------------------------------------------------------------------
Aparte de los escenarios que se han descrito anteriormente, puede beneficiarse de que los usuarios unan sus dispositivos a Azure AD gracias a la experiencia de unión simplificada, la administración de dispositivos en Azure AD, la inscripción automática de MDM y el inicio de sesión único en recursos de Azure AD y locales.


##Consideraciones de implementación de Azure AD Join

Permitir que los usuarios unan un dispositivo propiedad de la empresa directamente a Azure AD.
-----------------------------------------------------------------------------------------

Las empresas pueden proporcionar cuentas exclusivamente en la nube a organizaciones y empresas asociadas. Luego, a estos socios se les proporciona acceso fácil e inicio de sesión único a las aplicaciones y los recursos de su empresa. Este escenario es aplicable a los usuarios que utilizan sus dispositivos para acceder principalmente a los recursos en la nube, como las aplicaciones de Office 365 o de SaaS que basan su autenticación en Azure AD.

### Requisitos previos
**En el nivel de empresa (administrador)**

*	Suscripción de Azure con Azure Active Directory  

**En el nivel de usuario**

*	Windows 10 (SKU de Professional y Enterprise)

### Tareas de administrador
* [Configuración del registro de dispositivos](active-directory-azureadjoin-setup.md)

### Tareas de usuario
* [Configuración de un nuevo dispositivo de Windows 10 con Azure AD durante la instalación](active-directory-azureadjoin-user-frx.md)
* [Configuración de un dispositivo de Windows 10 con Azure AD desde Configuración](active-directory-azureadjoin-user-upgrade.md)
* [Unión de un dispositivo de Windows 10 personal a su organización](active-directory-azureadjoin-personal-device.md)
  


## Habilitación de BYOD en su organización para Windows 10
Puede configurar a los usuarios y empleados para que usen sus dispositivos personales de Windows para acceder a las aplicaciones y los recursos de la empresa. Los usuarios pueden agregar sus cuentas de Azure AD (cuentas profesionales) a un dispositivo de Windows personal para tener acceso a los recursos de trabajo de forma segura y conforme a lo establecido.

### Requisitos previos
**En el nivel de empresa (administrador)**

*	Suscripción de Azure AD

**En el nivel de usuario**

*	Windows 10 (SKU de Professional y Enterprise)


### Tareas de administrador

* [Configuración del registro de dispositivos](active-directory-azureadjoin-setup.md)

### Tareas de usuario
* [Unión de un dispositivo de Windows 10 personal a su organización](active-directory-azureadjoin-personal-device.md)


## Información adicional
* [Windows 10 para la empresa: formas de usar dispositivos para trabajar](active-directory-azureadjoin-windows10-devices-overview.md)
* [Ampliación de las capacidades de nube a dispositivos de Windows 10 a través de Azure Active Directory Join](active-directory-azureadjoin-user-upgrade.md)
* [Autenticación de identidades sin contraseñas a través de Microsoft Passport](active-directory-azureadjoin-passport.md)
* [Conozca los escenarios de uso de Azure AD Join](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Experiencias de conexión de dispositivos unidos a un dominio a Azure AD para Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configuración de Azure AD Join](active-directory-azureadjoin-setup.md)

<!---HONumber=AcomDC_1125_2015-->