<properties 
	pageTitle="Historial de versiones de Azure AD Connect Health" 
	description="Este documento describe las versiones de Azure AD Connect Health y lo que se ha incluido en dichas versiones." 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtand"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/18/2016" 
	ms.author="billmath"/>

# Azure AD Connect Health: historial de versiones

El equipo de Azure Active Directory actualiza periódicamente Azure AD Connect Health con nuevas características y funciones.

Este artículo está diseñado para ayudarle a realizar un seguimiento de las versiones que se han publicado.


## Enero de 2016


**Actualización del agente:**

- Agente de Azure AD Connect Health para AD FS (versión 2.6.91.1512)


**Nuevas características:**

- [Herramienta de conexión de prueba para agentes de Azure AD Connect Health](active-directory-aadconnect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service)


## Noviembre de 2015


**Nuevas características:**

- Soporte para el [control de acceso basado en rol](active-directory-aadconnect-health-operations.md#manage-access-with-role-based-access-control)


**Nuevas características de la versión preliminar:**

- [Azure AD Connect Health para sincronización](active-directory-aadconnect-health-sync.md)

**Problemas corregidos:**

- Correcciones de errores detectados durante los registros del agente. 

## Septiembre de 2015

**Nuevas características:**

- Informe de contraseña de nombre de usuario incorrecto para AD FS 
- Soporte técnico para configurar el proxy de HTTP sin autenticar 
- Soporte técnico para configurar el agente en Server Core
- Mejoras en las alertas de AD FS 
- Mejoras en el agente de Azure AD Connect Health para AD FS en la conectividad y carga de datos. 


**Problemas corregidos:**

- Correcciones de errores en el uso de tipos de errores de AD FS. 


## Junio de 2015

**Versión inicial de Azure AD Connect Health para AD FS y el proxy de AD FS.**

**Nuevas características:**

- Alertas de supervisión de AD FS y servidores proxy de AD FS con notificaciones de correo electrónico. 
- Fácil acceso a la topología y patrones de AD FS en los contadores de rendimiento de AD FS. 
- Tendencia de las solicitudes correctas de token en servidores de AD FS agrupados por aplicaciones, métodos de autenticación, ubicación de la red de solicitud, etc. 
- Tendencias en las solicitudes con error en los servidores de AD FS agrupados por aplicaciones, tipos de error, etc.
- Implementación más sencilla del agente mediante credenciales de administrador global de Azure AD.  




## Pasos siguientes
Conozca más detalles acerca de la [supervisión de la infraestructura de identidad local y los servicios de sincronización en la nube](active-directory-aadconnect-health.md).

<!---HONumber=AcomDC_0224_2016-->