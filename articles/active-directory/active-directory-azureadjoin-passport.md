<properties 
	pageTitle="Autenticación de identidades sin contraseñas a través de Microsoft Passport | Microsoft Azure" 
	description="Ofrece información general de Microsoft Passport e información adicional sobre la implementación de Microsoft Passport." 
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

# Autenticación de identidades sin contraseñas a través de Microsoft Passport

Los métodos actuales de autenticación con contraseña, por sí solos, no son suficientes para proteger a los usuarios. Los usuarios reutilizan y olvidan las contraseñas. Las contraseñas se pueden violar, son susceptibles a la suplantación de identidad (phishing), son propensas al descifrado y se pueden adivinar. También son difíciles de recordar y tienden a ataques del tipo "[pass the hash](https://technet.microsoft.com/dn785092.aspx)".

## Qué es Microsoft Passport
Microsoft Passport es un nuevo enfoque de autenticación basado en certificado o clave pública/privada dirigido a las organizaciones y los clientes que no se limitan a las contraseñas. Esta forma de autenticación se basa en estas credenciales de par de claves que pueden sustituir a las contraseñas y pueden ser resistente a infracciones, robos y suplantación de identidad (phishing). Microsoft Passport permite a los usuarios autenticarse en una cuenta Microsoft, de Active Directory y de Microsoft Azure Active Directory (AD), o en servicios que no son de Microsoft compatibles con la autenticación Fast ID Online (FIDO). Después de una comprobación inicial de dos pasos durante la inscripción en Microsoft Passport, ya hay una cuenta de Microsoft Passport configurada en el dispositivo del usuario y el usuario define un gesto, que puede ser Windows Hello o un PIN. El usuario proporciona el gesto para comprobar la identidad; Windows usa entonces Microsoft Passport para autenticar a los usuarios y ayudarles a obtener acceso a los recursos y servicios protegidos.

La clave privada estás disponible únicamente a través de un "gesto del usuario" como un PIN, biometría o un dispositivo remoto como una tarjeta inteligente que usa el usuario para iniciar sesión en el dispositivo. Esta información se vincula a un certificado o un par de claves asimétricas. Esta clave privada es certificada por el hardware si el dispositivo cuenta con un chip de Módulo de plataforma segura (TPM). La clave privada nunca abandona el dispositivo.

La clave pública se registra en Azure Active Directory y Windows Server Active Directory (para el entorno local). Los proveedores de identidades (IDP) validan al usuario mediante la asignación de la clave pública a la clave privada y proporcionan información de inicio de sesión mediante una contraseña de un solo uso (OTP), Phonefactor o un mecanismo de notificación diferente.

## ¿Por qué las empresas deben adoptar Microsoft Passport?

Con Microsoft Passport, las empresas pueden proteger sus recursos incluso más gracias a:

* La configuración de Microsoft Passport con una opción preferida de hardware, lo que significa que las claves se generarán en TPM 1.2 o TPM 2.0 cuando estén disponibles y mediante software cuando TPM no lo esté. 

* La definición de la complejidad y la longitud del PIN, y la posibilidad de habilitar el uso de Hello en su organización.

* La configuración de Microsoft Passport para admitir escenarios de tipo tarjeta inteligente con confianza basada en certificados.

## ¿Cómo funciona?
1. Las claves se generan en el hardware. Muchos equipos tienen un chip integrado de Módulo de plataforma segura (TPM) que protege el hardware mediante la integración de claves criptográficas en los dispositivos. El TPM 1.2 o TPM 2.0 se utiliza para generar claves o certificados que estarán codificados con las claves generadas.

2. Estas claves enlazadas al hardware se certifican mediante el TPM.

3. Con un solo gesto de desbloqueo se desbloquea el dispositivo, y con este gesto se permitirá el acceso a varios recursos si el dispositivo se ha unido a un dominio o a Azure AD.

## Ciclo de vida de Microsoft Passport

![](./media/active-directory-azureadjoin/active-directory-azureadjoin-microsoft-passport.png)

El diagrama anterior muestra el par de claves pública y privada y la validación por el proveedor de identidades. Cada uno de estos pasos se explica con detalle a continuación:

1. El usuario prueba su identidad a través de varios métodos de corrección integrados (gestos, tarjetas inteligentes físicas, Multi-factor Authentication) y envía esta información al proveedor de identidades (IDP), como Azure Active Directory o Active Directory.

2. A continuación, el dispositivo crea la clave, la certifica, toma la parte pública de esta clave, la asocia con las instrucciones de la estación, inicia sesión y envía la clave al IDP para registrarla.

3. En cuanto se registra la parte pública de la clave en el IDP, desafía al dispositivo a que firme con la parte privada de la clave. El IDP valida y emite entonces el token de autenticación que permite al usuario tener acceso a los recursos protegidos.

4. En cuanto la parte pública de la clave se registra en el IDP, desafía al dispositivo a firmar con la parte privada de la clave.

5. El IDP valida y emite entonces el token de autenticación que permite al usuario y al dispositivo tener acceso a los recursos protegidos. El IDP puede escribir aplicaciones multiplataforma o usar la compatibilidad con el explorador a través de las API JS/Webcrypto para crear y usar las credenciales de Microsoft Passport para sus usuarios.

## Requisitos de implementación
En el nivel de empresa
---------------------------
* Suscripción de Azure

En el nivel de usuario
-------------------------------------------------------------
* El equipo debe ejecutar el SKU de Windows 10 Professional o Enterprise

Para obtener instrucciones de implementación detalladas, consulte [Habilitar Microsoft Passport para el trabajo en la organización](active-directory-azureadjoin-passport-deployment.md).


## Información adicional

* [Windows 10 para la empresa: formas de usar dispositivos para trabajar](active-directory-azureadjoin-windows10-devices-overview.md)
* [Ampliación de las capacidades de nube a dispositivos de Windows 10 a través de Azure Active Directory Join](active-directory-azureadjoin-user-upgrade.md)
* [Conozca los escenarios de uso de Azure AD Join](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Experiencias de conexión de dispositivos unidos a un dominio a Azure AD para Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configuración de Azure AD Join](active-directory-azureadjoin-setup.md)

<!---HONumber=AcomDC_1125_2015-->