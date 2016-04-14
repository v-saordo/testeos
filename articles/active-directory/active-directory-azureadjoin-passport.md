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
	ms.date="02/26/2016"
	ms.author="femila"/>

# Autenticación de identidades sin contraseñas a través de Microsoft Passport

Los métodos actuales de autenticación con contraseña, por sí solos, no son suficientes para proteger a los usuarios. Los usuarios reutilizan y olvidan las contraseñas. Las contraseñas se pueden violar, son susceptibles a la suplantación de identidad (phishing), son propensas al descifrado y se pueden adivinar. También son difíciles de recordar y tienden a ataques del tipo "[pass the hash](https://technet.microsoft.com/dn785092.aspx)".

## Microsoft Passport
Microsoft Passport es un método de autenticación basado en certificado o clave pública/privada dirigido a organizaciones y consumidores que va más allá de las contraseñas. Esta forma de autenticación se basa en credenciales de par de claves que pueden reemplazar a las contraseñas y que resisten las infracciones, los robos y las suplantaciones de identidad.

 Microsoft Passport permite a los usuarios autenticarse en una cuenta Microsoft, una cuenta de Windows Server Active Directory, una cuenta de Microsoft Azure Active Directory (Azure AD) o un servicio que no sea de Microsoft compatible con la autenticación Fast IDentity Online (FIDO). Una vez realizada una comprobación inicial de dos pasos durante la inscripción en Microsoft Passport, el servicio Microsoft Passport está configurado en el dispositivo del usuario y este define un gesto, que puede ser Windows Hello o un PIN. El usuario proporciona el gesto para comprobar su identidad. Windows usa entonces Microsoft Passport para autenticar al usuario y ayudar a obtener acceso a los servicios y recursos protegidos.

La clave privada queda disponible únicamente a través de un "gesto de usuario" como un PIN, biometría o un dispositivo remoto como una tarjeta inteligente que el usuario utiliza para iniciar sesión en el dispositivo. Esta información se vincula a un certificado o un par de claves asimétrico. Esta clave privada es certificada por el hardware si el dispositivo cuenta con un chip de Módulo de plataforma segura (TPM). La clave privada nunca abandona el dispositivo.

La clave pública se registra en Azure Active Directory y Windows Server Active Directory (para el entorno local). Los proveedores de identidades (IDP) validan al usuario mediante la asignación de la clave pública del usuario a la clave privada y proporcionan información de inicio de sesión mediante una contraseña de un solo uso (OTP), Phonefactor o un mecanismo de notificación diferente.

## ¿Por qué es recomendable que las empresas adopten Microsoft Passport?

Con Microsoft Passport, las empresas pueden proteger sus recursos incluso más gracias a:

* Configuración de Microsoft Passport con una opción preferida de hardware. Esto significa que las claves se generarán en TPM 1.2 o TPM 2.0 cuando sea posible. Cuando TPM no esté disponible, el software generará la clave.

* La definición de la complejidad y la longitud del PIN, y la posibilidad de habilitar el uso de Hello en su organización.

* La configuración de Microsoft Passport para admitir escenarios de tipo tarjeta inteligente con confianza basada en certificados.

## Funcionamiento de Microsoft Passport
1. Las claves se generan en el hardware por TPM o el software. Muchos dispositivos tienen un chip TPM integrado que protege el hardware mediante la integración de claves de cifrado en los dispositivos. TPM 1.2 o TPM 2.0 genera claves o certificados que se crean a partir de las claves generadas.

2. TPM certifica estas claves enlazadas al hardware.

3. Un único gesto de desbloqueo desbloquea el dispositivo. Este gesto permite el acceso a varios recursos si el dispositivo está unido a un dominio o a Azure AD.

## Funcionamiento del ciclo de vida de Microsoft Passport

![Ciclo de vida de Microsoft Passport](./media/active-directory-azureadjoin/active-directory-azureadjoin-microsoft-passport.png)

El diagrama anterior muestra el par de claves pública y privada y la validación por el proveedor de identidades. Cada uno de estos pasos se explica con detalle a continuación:

1. El usuario prueba su identidad a través de varios métodos de verificación integrados (gestos, tarjetas inteligentes físicas, autenticación multifactor) y envía esta información al proveedor de identidades, como Azure Active Directory o el entorno Active Directory local.

2. A continuación, el dispositivo crea la clave, la certifica, toma la parte pública de esta clave, la asocia con las instrucciones de la estación, inicia sesión y envía la clave al proveedor de identidades para registrarla.

4. En cuanto el proveedor de identidades registra la parte pública de la clave, este desafía al dispositivo a que firme con la parte privada de la clave.

5. El proveedor de identidades valida y emite entonces el token de autenticación que permite al usuario y al dispositivo tener acceso a los recursos protegidos. El proveedor de identidades puede escribir aplicaciones multiplataforma o usar la compatibilidad con el explorador (a través de las API de JavaScript/Webcrypto) para crear y usar las credenciales de Microsoft Passport para sus usuarios.

## Requisitos de implementación de Microsoft Passport
### En el nivel de empresa

* La empresa tiene una suscripción de Azure.

### En el nivel de usuario

* El equipo del usuario ejecuta Windows 10 Professional o Enterprise.

Para obtener instrucciones de implementación detalladas, consulte [Habilitar Microsoft Passport para el trabajo en la organización](active-directory-azureadjoin-passport-deployment.md).


## Información adicional

* [Windows 10 para la empresa: formas de usar dispositivos para trabajar](active-directory-azureadjoin-windows10-devices-overview.md)
* [Ampliación de las capacidades de nube a dispositivos de Windows 10 a través de Azure Active Directory Join](active-directory-azureadjoin-user-upgrade.md)
* [Conozca los escenarios de uso de Azure AD Join](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Experiencias de conexión de dispositivos unidos a un dominio a Azure AD para Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configuración de Azure AD Join](active-directory-azureadjoin-setup.md)

<!---HONumber=AcomDC_0302_2016-->