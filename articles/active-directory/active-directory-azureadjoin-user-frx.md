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
	ms.date="02/26/2016"
	ms.author="femila"/>

# Configuración de un dispositivo nuevo con Azure AD durante la configuración

En Windows 10, los usuarios pueden unir sus dispositivos a Azure Active Directory (Azure AD) en la Primera vista de Windows (FRX). Esto permite que las organizaciones distribuyan dispositivos empaquetados a sus empleados o estudiantes, o bien permitirles elegir sus propios dispositivos (CYOD). Si un dispositivo tiene instaladas las ediciones Windows 10 Professional o Windows 10 Enterprise, la experiencia predeterminada es el proceso de configuración de los dispositivos de empresa.

## Para unir un dispositivo a Azure AD


1. Al activar el nuevo dispositivo e iniciar el proceso de configuración, debería ver el mensaje **Preparación**. Siga las indicaciones para configurar el dispositivo.
2. Para comenzar, personalice su región e idioma. A continuación, acepte los términos de licencia del software de Microsoft. ![Personalizar para la región](./media/active-directory-azureadjoin/active-directory-azureadjoin-customize-region.png)
3. Seleccione la red que desea usar para conectarse a Internet.
4. Seleccione si usa un dispositivo personal o un dispositivo propiedad de la empresa. Si es propiedad de la empresa, haga clic en **Este dispositivo pertenece a mi organización**. Con esto comienza la experiencia de Azure AD Join. La siguiente pantalla aparece si usa Windows 10 Professional.
<center>
![Pantalla ¿A quién pertenece el equipo?](./media/active-directory-azureadjoin/active-directory-azureadjoin-who-owns-pc.png)

5.	Escriba las credenciales que le ha proporcionado la organización.
<center>
![Pantalla de inicio de sesión](./media/active-directory-azureadjoin/active-directory-azureadjoin-sign-in.png)
6.	Después de que escriba su nombre de usuario, se coloca un inquilino coincidente en Azure AD. Si se encuentra en un dominio federado, se le redirigirá al servidor del servicio de token seguro (STS) local; por ejemplo, Servicios de federación de Active Directory (AD FS).
7. Si es un usuario en un dominio no federado, deberá escribir las credenciales directamente en la página hospedada en Azure AD. Si se ha configurado la personalización de la marca, también verá el logotipo de la organización y texto complementario.
8.	A continuación, encontrará un desafío de Multi-factor Authentication. Este desafío puede configurarlo un administrador de TI.
9.	Azure AD comprobará si este usuario/dispositivo requiere la inscripción en la administración de dispositivos móviles.
10.	Windows registra el dispositivo en el directorio de la organización en Azure AD y lo inscribe en la administración de dispositivos móviles, si procede.
11.	Si es un usuario administrado, Windows le llevará al escritorio mediante el proceso de inicio de sesión automático.
12.	Si es un usuario federado, verá la pantalla de inicio de sesión de Windows y deberá escribir sus credenciales.

> [AZURE.NOTE] La configuración predeterminada de Windows no permite la unión a un dominio de Windows Server Active Directory local. Por lo tanto, si planea unir un equipo a un dominio, debe seleccionar en su lugar el vínculo **Configurar Windows con una cuenta local**. Luego, puede unirse al dominio desde la configuración del equipo como ya lo hizo anteriormente.

## Información adicional
* [Windows 10 para la empresa: formas de usar dispositivos para trabajar](active-directory-azureadjoin-windows10-devices-overview.md)
* [Ampliación de las capacidades de nube a dispositivos de Windows 10 a través de Azure Active Directory Join](active-directory-azureadjoin-user-upgrade.md)
* [Autenticación de identidades sin contraseñas a través de Microsoft Passport](active-directory-azureadjoin-passport.md)
* [Conozca los escenarios de uso de Azure AD Join](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Experiencias de conexión de dispositivos unidos a un dominio a Azure AD para Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configuración de Azure AD Join](active-directory-azureadjoin-setup.md)

<!---HONumber=AcomDC_0302_2016-->