

<properties 
	pageTitle="Unión de un dispositivo personal a su organización | Microsoft Azure" 
	description="Explica cómo los usuarios pueden registrar sus equipos personales con Windows 10 en su red corporativa, y se ofrecen pasos de implementación para un escenario BYOD." 
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

# Unión de un dispositivo personal a su organización

Para unir un dispositivo de Windows 10 a su organización
--------------------------------------------------------------------------------------------
1.	En el menú **Inicio**, seleccione **Configuración**. 
2.	Seleccione **Cuentas** y luego haga clic en **Su cuenta**.
3.	Haga clic en **Agregar cuenta profesional o educativa** y luego escriba su cuenta de organización.
4.	Se le redirigirá a la página de inicio de sesión para su organización. Escriba su nombre de usuario y contraseña y haga clic en **Aceptar**.
5.	A continuación, encontrará un desafío de autenticación multifactor. La TI configura esta función.
6.	Luego, Azure AD comprobará si este usuario/dispositivo requiere la inscripción a la administración de dispositivos móviles (MDM). 
7.	A continuación, Windows registrará el dispositivo en el directorio de la organización en Azure AD y lo inscribirá en MDM.
8.	Cuando termine este proceso, si se trata de un usuario administrado, Windows concluirá el proceso de instalación y llevará al usuario al escritorio a través del inicio de sesión automático.
9.	Si se trata de un usuario federado, verá la pantalla de inicio de sesión de Windows y deberá escribir sus credenciales.

## Información adicional
* [Windows 10 para empresa: formas de usar dispositivos para trabajar](active-directory-azureadjoin-windows10-devices-overview.md)
* [Ampliación de las capacidades de nube a dispositivos de Windows 10 a través de Azure Active Directory Join](active-directory-azureadjoin-user-upgrade.md)
* [Autenticación de identidades sin contraseñas a través de Microsoft Passport](active-directory-azureadjoin-passport.md)
* [Conozca los escenarios de uso de Azure AD Join](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Experiencias de conexión de dispositivos unidos a un dominio a Azure AD para Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configuración de Azure AD Join](active-directory-azureadjoin-setup.md)

<!---HONumber=AcomDC_1125_2015-->