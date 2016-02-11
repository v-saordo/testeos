<properties 
	pageTitle="Configuración de un dispositivo nuevo con Azure AD durante la configuración | Microsoft Azure" 
	description="Un tema que explica cómo los usuarios pueden configurar Azure AD Join durante la Primera vista de Windows." 
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

# Configuración de un dispositivo nuevo con Azure AD durante la configuración

En Windows 10, los usuarios finales pueden unir su dispositivo a Azure AD en la Primera vista de Windows (FRX). Esto permitirá que las organizaciones distribuyan dispositivos empaquetados a sus empleados o estudiantes, o bien permitirles elegir su propio dispositivo (CYOD). Si instala el SKU de Windows 10 Professional o Enterprise, la experiencia se establece de manera predeterminada en la configuración de dispositivos de propiedad de la empresa.

Para unir un dispositivo a Azure AD
-----------------------------------------------------------------------

1. Después de la fase de **Introducción**, verá el asistente para la instalación.
2. Para comenzar, personalice la región y el idioma, acepte el CLUF y conéctese a Internet.
<center> ![](./media/active-directory-azureadjoin/active-directory-azureadjoin-customize-region.png) </center>
3. Seleccione la red para conectarse a Internet.
4. Seleccione si se trata de un dispositivo personal o de un dispositivo de propiedad de la empresa:
5. Haga clic en **Este dispositivo pertenece a mi organización**. Con esto comienza la experiencia de Azure AD Join. La siguiente pantalla aparece en el SKU de Windows 10 Professional. 
<center> ![](./media/active-directory-azureadjoin/active-directory-azureadjoin-who-owns-pc.png) </center>

6.	Escriba las credenciales que proporcionadas por la organización.
<center> ![](./media/active-directory-azureadjoin/active-directory-azureadjoin-sign-in.png) </center>
7.	Una vez que escriba su nombre de usuario, se coloca un inquilino coincidente en Azure AD. Si se encuentra en un dominio federado, se le redirigirá al servidor del servicio de token seguro (STS) local (por ejemplo, AD FS). Si se trata de un usuario en dominios no federados, deberá escribir las credenciales directamente en la página hospedada en Azure AD. También verá el logotipo de la organización y texto complementario si se configuró la Personalización de la marca.
8.	A continuación, encontrará un desafío de autenticación multifactor. La TI configura esta función.
9.	Luego, Azure AD comprobará si este usuario/dispositivo requiere la inscripción a la administración de dispositivos móviles (MDM). 
10.	A continuación, Windows registrará el dispositivo en el directorio de la organización en Azure AD y lo inscribirá en MDM.
11.	Cuando termine este proceso, si se trata de un usuario administrado, Windows concluirá el proceso de instalación y llevará al usuario al escritorio a través del inicio de sesión automático.
12.	Si se trata de un usuario federado, verá la pantalla de inicio de sesión de Windows y deberá escribir sus credenciales para iniciar sesión.

> [AZURE.NOTE]La configuración rápida de Windows no admite la unión a un dominio de Active Directory local. Por lo tanto, si planea unir un equipo a un dominio, debe seleccionar el vínculo "Configurar Windows con una cuenta local en su lugar". Luego, puede unirse al dominio desde la configuración de PC como ya lo hizo anteriormente.

## Información adicional
* [Windows 10 para empresa: formas de usar dispositivos para trabajar](active-directory-azureadjoin-windows10-devices-overview.md)
* [Ampliación de las capacidades de nube a dispositivos de Windows 10 a través de Azure Active Directory Join](active-directory-azureadjoin-user-upgrade.md)
* [Autenticación de identidades sin contraseñas a través de Microsoft Passport](active-directory-azureadjoin-passport.md)
* [Conozca los escenarios de uso de Azure AD Join](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Experiencias de conexión de dispositivos unidos a un dominio a Azure AD para Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configuración de Azure AD Join](active-directory-azureadjoin-setup.md)

<!---HONumber=AcomDC_1125_2015-->