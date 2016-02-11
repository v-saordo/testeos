<properties
	pageTitle="# Configure el registro automático de dispositivos para dispositivos Windows 7 unidos a un dominio| Microsoft Azure"
	description="Pasos para configurar sus dispositivos Windows 7 unidos a un dominio para registrarse automáticamente en Azure AD y pasos para implementar el paquete de software de registro de dispositivos en sus dispositivos Windows 7 unidos a un dominio mediante un sistema de distribución de software como System Center Configuration Manager."
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/24/2015"
	ms.author="femila"/>

# Configure el registro automático de dispositivos para dispositivos Windows 7 unidos a un dominio

Como administrador de TI, puede configurar sus dispositivos Windows 7 unidos a un dominio para registrarse automáticamente en Azure AD. Para ello, debe implementar el paquete de software de registro de dispositivos en sus dispositivos Windows 7 unidos a un dominio mediante un sistema de distribución de software como System Center Configuration Manager. Asegúrese de leer y completar los requisitos previos descritos en el registro automático de dispositivos en Azure Active Directory para dispositivos Windows unidos a un dominio.

##Instalación del paquete de software de registro de dispositivos en dispositivos Windows 7 unidos a un dominio

El registro de dispositivos para Windows 7 está disponible como [paquete MSI descargable](https://connect.microsoft.com/site1164). El paquete debe instalarse en equipos con Windows 7 que se unan a un dominio de Active Directory. Debe implementar el paquete mediante un sistema de distribución de software como System Center Configuration Manager. El paquete MSI es compatible con las opciones de instalación silenciosa estándar mediante el parámetro /quiet. El paquete de software está disponible para su descarga en el [sitio web de Microsoft Connect](https://connect.microsoft.com/site1164). Aquí puede seleccionar y, a continuación, descargar Unión al área de trabajo para Windows 7.

![](./media/active-directory-conditional-access/device-registration-process-windows7.gif)

## Unión al área de trabajo con Azure Active Directory
El registro de dispositivos para dispositivos Windows 7 unidos a un dominio no requiere ni incluye una interfaz de usuario. Una vez instalado en el equipo, cualquier usuario de dominio que inicie sesión en el equipo se registrará de forma automática y silenciosa en un objeto de dispositivo en Azure AD. Habrá un objeto de dispositivo en Azure AD para todos los usuarios registrados del dispositivo físico.

El instalador crea una tarea programada en el sistema que se ejecuta en el contexto del usuario y se desencadena en el inicio de sesión de usuario. La tarea registra silenciosamente el usuario y dispositivo en Azure AD después de completarse el inicio de sesión de usuario. La tarea programada se puede encontrar en la Biblioteca del Programador de tareas en **Microsoft** > **Unión al área de trabajo**. La tarea ejecutará y registrará todos los usuarios de Active Directory que inicien sesión en el equipo. En la siguiente ilustración se describe el proceso paso a paso para el registro automático de dispositivos.

![](./media/active-directory-conditional-access/automatic-device-registration-windows7.png)

1. Un usuario (trabajador de la información) inicia sesión en un equipo cliente con Windows 7 con credenciales de dominio de Active Directory.
1. Se ejecuta la tarea programada de Unión al área de trabajo.
1. El usuario se autentica silenciosamente con AD FS mediante Autenticación integrada de Windows.
1. El equipo con Windows 7 se registra para el usuario en Azure AD.
1. Se crea un certificado y objeto de dispositivo en Azure AD. El objeto representa user@device.
1. El certificado de Unión al área de trabajo se almacena en el equipo.

## Anulación del registro de sus dispositivos Windows 7 unidos a un dominio

Puede elegir anular el registro de sus dispositivos Windows 7 unidos a un dominio haciendo lo siguiente: desinstale el paquete de software de Unión al área de trabajo de sus dispositivos Windows 7 unidos a un dominio con un sistema de distribución de software como System Center Configuration Manager.

A continuación, abra un símbolo del sistema en el equipo con Windows 7 y ejecute el siguiente comando para anular el registro del dispositivo:
    
    %ProgramFiles%\Microsoft Workplace Join\AutoWorkplace.exe /leave

>[AZURE.NOTE]Este comando debe ejecutarse en el contexto de cada usuario del dominio que ha iniciado sesión en el equipo. Visor de eventos y errores para dispositivos Windows 7 unidos a un dominio.

El registro de eventos de Windows en el equipo con Windows 7 mostrará mensajes relacionados con Unión al área de trabajo. Puede encontrar mensajes para eventos de Unión al área de trabajo con éxito y sin éxito. El registro de eventos puede encontrarse en el Visor de eventos en Registros de aplicaciones y servicios> Microsoft-Unión al área de trabajo.

## Otros temas

- [Información general sobre el Registro de dispositivos de Azure Active Directory](active-directory-conditional-access-device-registration-overview.md)
- [Registro automático de dispositivos en Azure Active Directory para dispositivos Windows unidos a un dominio](active-directory-conditional-access-automatic-device-registration.md)
- [Configuración del registro automático de dispositivos para dispositivos Windows 8.1 unidos a un dominio](active-directory-conditional-access-automatic-device-registration-windows8_1.md)

 

<!---HONumber=AcomDC_1203_2015-->