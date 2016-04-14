<properties 
	pageTitle="Uso de una aplicación móvil como método de contacto con Azure MFA" 
	description="Esta página mostrará a los usuarios cómo utilizar la aplicación móvil como método de contacto principal para Azure MFA." 
	services="multi-factor-authentication" 
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

# Uso de una aplicación móvil como método de contacto con Azure Multi-Factor Authentication

Este artículo puede serle útil si desea usar la aplicación móvil como método de contacto principal. La información que aquí se ofrece le guiará a través de la configuración de Multi-Factor Authentication para usar la aplicación móvil como método de contacto principal.

La aplicación Azure Authenticator está disponible para [Windows Phone](http://www.windowsphone.com/store/app/azure-authenticator/03a5b2bf-6066-418f-b569-e8aecbc06e50), [Android](https://play.google.com/store/apps/details?id=com.azure.authenticator) e [IOS](https://itunes.apple.com/us/app/azure-authenticator/id983156458).

## Para usar una aplicación móvil como método de contacto


- Seleccione Aplicación móvil en la lista desplegable.


![Configuración](./media/multi-factor-authentication-end-user-first-time-mobile-app/mobileapp.png)

- Seleccione Notificación o Contraseña de un solo uso y haga clic en Configurar.
- En el teléfono que tenga instalada la aplicación Azure Authenticator, inicie la aplicación y haga clic en Digitalizar código de barras. Para agregar una cuenta que ya tenga MFA de Azure o una cuenta de terceros, vea [Agregar una cuenta manualmente](#adding-an-account-manually).

![Configuración](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan.png)

- Digitalice la imagen de código de barras que aparece en la pantalla para configurar la aplicación móvil. Haga clic en Listo para cerrar la pantalla del códigos de barras. Si no puede digitalizar el código de barras puede escribir la información manualmente.

![Configuración](./media/multi-factor-authentication-end-user-first-time-mobile-app/barcode.png)

- Comenzará la activación en el teléfono, una vez que se haya completado, haga clic en la opción para contactar. Con esto se enviará una notificación o un código de verificación a su teléfono. Haga clic en Comprobar.

![Configuración](./media/multi-factor-authentication-end-user-first-time-mobile-app/verify.png)

- Haga clic en Cerrar. A estas alturas, la verificación debería haberse realizado correctamente.
- Ahora se recomienda que escriba su número de teléfono móvil para usar en el caso de que pierda el acceso a la aplicación móvil.
- Especifique el país en la lista desplegable y escriba su número de teléfono móvil en la casilla junto al país. Haga clic en Siguiente.
- Ahora ya ha configurado el método de contacto y es el momento de hacer lo mismo con las contraseñas de aplicación para las aplicaciones sin explorador como Outlook 2010 o anterior. Si no usa estas aplicaciones, haga clic en **Listo**. De lo contrario, continúe con el paso siguiente.

![Configuración](./media/multi-factor-authentication-end-user-first-time-mobile-app/step4.png)

- Si está usando estas aplicaciones copie la contraseña de aplicación proporcionada y péguela en la aplicación sin explorador. Para obtener información acerca de aplicaciones específicas como Outlook y Lync vea cómo cambiar la contraseña en su correo electrónico a la contraseña de aplicación y cómo cambiar la contraseña en su aplicación a la contraseña de aplicación.
- Haga clic en Done (Listo).


## Agregar una cuenta manualmente
Si desea agregar una cuenta manualmente, seleccione el botón introducir cuenta manualmente.

![Configuración](./media/multi-factor-authentication-end-user-first-time-mobile-app/addaccount.png)

Ahora si tiene una cuenta que ya tenga MFA de Azure, especifique el código y la URL que se ofrece en la misma página que le muestra el código de barras. Esto se incluye en los cuadros de código y dirección URL en la aplicación móvil. Esto comenzará la instalación.

![Configuración](./media/multi-factor-authentication-end-user-first-time-mobile-app/barcode2.png)

Cuando se haya completado esta haga clic en Contacto. Con esto se enviará una notificación o un código de verificación a su teléfono. Haga clic en Comprobar. Para terminar, siga los pasos anteriores empezando por el número 6.

Si está usando una cuenta de terceros con la aplicación móvil, escriba el nombre de la cuenta y la clave de seguridad en los cuadros que se ofrecen y, a continuación, active la cuenta. Cuando haya hecho esto y comprobado la cuenta, siga los pasos anteriores empezando por el número 6.


![Configuración](./media/multi-factor-authentication-end-user-first-time-mobile-app/add3rdparty.png)

>[AZURE.NOTE]Si ve "Agregar cuenta profesional", esto es para Unión al área de trabajo y no para la autenticación multifactor. Puede pasar esto por alto.
 

<!---HONumber=AcomDC_0218_2016-->