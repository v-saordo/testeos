<properties 
	pageTitle="Configuración de Azure AD Join para los usuarios | Microsoft Azure" 
	description="Explica cómo pueden configurar los administradores Azure AD Join para el directorio local y el registro de dispositivos."
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

# Configuración de Azure AD Join en su organización

Antes de configurar Azure AD Join, deberá sincronizar el directorio local de usuarios con la nube o crear manualmente cuentas administradas en Azure AD.

Se pueden encontrar instrucciones detalladas para sincronizar los usuarios locales con Azure AD en [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md).


Para crear y administrar usuarios en Azure AD manualmente, consulte [Administración de usuarios en Azure AD](https://msdn.microsoft.com/library/azure/hh967609.aspx).

## Configuración del registro de dispositivos 
1. Inicie sesión en el Portal de Azure como administrador.
2. En el panel izquierdo, seleccione Active Directory.
3. En la pestaña **Directorio**, seleccione su directorio.
4. Seleccione la pestaña **Configurar**.
5. Desplácese hasta la sección llamada **Dispositivos**.
6. En la pestaña **Dispositivos**, configure lo siguiente:  
   * **Número máximo de dispositivos por usuario**: seleccione el número máximo de dispositivos que puede tener un usuario en Azure AD. Si un usuario alcanza esta cuota, no podrá agregar dispositivos adicionales hasta que se quiten uno o varios de sus dispositivos existentes.
   * **Requerir Multi-factor Auth para conectar dispositivos**: habilite esta opción cuando los usuarios deban proporcionar un segundo factor de autenticación para unir su dispositivo a Azure AD. Para obtener más información sobre Multi-Factor Authentication, consulte [Introducción a Multi-Factor Authentication de Azure en la nube](multi-factor-authentication-get-started-cloud/)
   * **Los usuarios pueden unir dispositivos a Azure AD**: seleccione los usuarios y grupos que tienen permiso para unir dispositivos a Azure AD.
   * **Administradores adicionales en dispositivos unidos a Azure AD**: con Azure AD Premium o Enterprise Mobility Suite (EMS), puede elegir a qué usuarios se les conceden derechos de administrador local al dispositivo. De forma predeterminada, a los administradores globales y a los propietarios de dispositivos se les conceden derechos de administrador local.

<center>![](./media/active-directory-azureadjoin/active-directory-aadjoin-configure-devices.png) </center>
Después de configurar Azure AD Join para los usuarios, se pueden conectar a Azure AD mediante sus dispositivos personales o corporativos.

A continuación se describen tres escenarios de cómo puede permitir que los usuarios configuren Azure AD Join:

- Los usuarios unen un dispositivo propiedad de la empresa directamente a Azure AD.
- Los usuarios de un dominio unen un dispositivo propiedad de la empresa a Active Directory local y lo extienden a Azure AD.
- Los usuarios agregan cuentas profesionales a Windows en un dispositivo personal 

## Información adicional
* [Windows 10 para empresa: formas de usar dispositivos para trabajar](active-directory-azureadjoin-windows10-devices-overview.md)
* [Ampliación de las capacidades de nube a dispositivos de Windows 10 a través de Azure Active Directory Join](active-directory-azureadjoin-user-upgrade.md)
* [Conozca los escenarios de uso de Azure AD Join](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Experiencias de conexión de dispositivos unidos a un dominio a Azure AD para Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configuración de Azure AD Join](active-directory-azureadjoin-setup.md)

<!---HONumber=AcomDC_1125_2015-->