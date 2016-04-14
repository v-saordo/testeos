<properties 
	pageTitle="Iniciar sesión por primera vez con Azure Multi-Factor Authentication" 
	description="Esta página describe cuál será la experiencia del usuario la primera vez que se inicia sesión." 
	services="multi-factor-authentication"
	keywords="cómo usar azure directory, active directory en la nube, tutorial de active directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenp" 
	editor="curtland"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/16/2016" 
	ms.author="billmath"/>
# La experiencia de configuración para Azure Multi-Factor Authentication

 Cuando un administrador ha configurado su cuenta para solicitar que proporcione tanto la contraseña como una respuesta desde el teléfono para comprobar su identidad, se utilizan ajustes de comprobación de seguridad adicionales. Si un administrador ha configurado su cuenta para requerir una comprobación de seguridad adicional, **no podrá iniciar sesión hasta que haya completado el proceso de inscripción automática**.

## Decisión sobre cómo va a utilizar la autenticación multifactor

 La primera vez que inicie sesión después de que se haya configurado su cuenta, se le pedirá que inicie el proceso de inscripción automática. Puede iniciar este proceso haciendo clic en **Configurar ahora.**

![Configuración](./media/multi-factor-authentication-end-user-first-time/first.png)

Mediante el proceso de inscripción, podrá especificar el método preferido de comprobación. Este puede ser uno de los siguientes en la tabla a continuación. Para obtener información adicional, incluido un ejemplo paso a paso no tiene más que hacer clic en uno de los métodos.

Método|Descripción
:------------- | :------------- | 
[Llamada de teléfono móvil](multi-factor-authentication-end-user-first-time-mobile-phone.md)| Hace una llamada de voz automática al teléfono de autenticación. El usuario responde a la llamada y pulsa la # del teclado del teléfono para autenticarse. Este número de teléfono no se sincronizará con Active Directory local.
[Mensaje de texto de teléfono móvil](multi-factor-authentication-end-user-first-time-mobile-phone.md)|Envía un mensaje de texto que contiene un código de verificación para el usuario. Se le pide al usuario que o bien conteste al mensaje de texto con el código de comprobación o que escriba el código de verificación en la interfaz de inicio de sesión.
[Llamada de teléfono de la oficina](multi-factor-authentication-end-user-first-time-office-phone.md)|Hace una llamada de voz automática al usuario. El usuario responde a la llamada y pulsa la # del teclado del teléfono para autenticarse.
[Aplicación móvil](multi-factor-authentication-end-user-first-time-mobile-app.md)|Inserta una notificación en la aplicación móvil Multi-factor en el smartphone o la tableta del usuario. El usuario toca "Comprobar" en la aplicación para autenticarse. Como alternativa, la aplicación también sirve como un token OTP para la autenticación sin conexión. El usuario escribe el token en la pantalla de inicio de sesión para autenticarse.<br><p> La aplicación Multi-Factor Authentication puede funcionar en dos modos diferentes para proporcionar la seguridad adicional que puede proporcionar un servicio de la autenticación multifactor. Estos son los siguientes:<li>** Notificación **: en este modo, la aplicación Multi-Factor Authentication impide el acceso no autorizado a cuentas y detiene las transacciones fraudulentas. Esto se realiza mediante una notificación push en su teléfono o dispositivo registrado. Simplemente vea la notificación y si es legítima seleccione Autenticar. En caso contrario, puede elegir Denegar o denegar e informar sobre la notificación fraudulenta. Para obtener información sobre cómo informar de notificaciones fraudulentas consulte Cómo usar la función Denegar e Informar de fraudes para Multi-Factor Authentication.</li><p><li>** Contraseña de un solo uso **: en este modo, la aplicación Azure Multi-Factor Authentication se puede usar como token de software para generar un código de acceso OATH. A continuación, este código de comprobación se puede escribir junto con el nombre de usuario y la contraseña a fin de proporcionar una segunda forma de autenticación.</li><br><p> La aplicación Azure Authenticator está disponible para [Windows Phone](http://www.windowsphone.com/es-ES/store/app/azure-authenticator/03a5b2bf-6066-418f-b569-e8aecbc06e50), [Android](https://play.google.com/store/apps/details?id=com.azure.authenticator) e [IOS](https://itunes.apple.com/us/app/azure-authenticator/id983156458).

 

<!---HONumber=AcomDC_0218_2016-->