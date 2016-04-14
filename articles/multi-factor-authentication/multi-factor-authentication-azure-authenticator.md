<properties 
	pageTitle="Aplicación Azure Authenticator para teléfonos móviles" 
	description="Obtenga información acerca de cómo actualizar a la versión más reciente de Azure Authenticatior." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtland"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/16/2016" 
	ms.author="billmath"/>



# Cambio a la nueva aplicación Azure Authenticator

Con el lanzamiento de la aplicación Azure Authenticator, disponible para [Windows Phone](http://www.windowsphone.com/es-ES/store/app/azure-authenticator/03a5b2bf-6066-418f-b569-e8aecbc06e50), [Android](https://play.google.com/store/apps/details?id=com.azure.authenticator),e [IOS](https://itunes.apple.com/us/app/azure-authenticator/id983156458), se reemplaza a la antigua aplicación de autenticación multifactor. La aplicación Multi-Factor Authentication continuará funcionando, pero si decide cambiarse a la nueva aplicación Azure Authenticator, este artículo puede ayudarle.


## Cómo cambiar a la nueva aplicación Azure Authenticator 

**Paso 1:** instalar Azure Authenticator.

![Nube](./media/multi-factor-authentication-azure-authenticator/home.png)

**Paso 2:** activar sus cuentas con la nueva aplicación

Asegúrese en primer lugar, de que tiene a mano el código QR o el código y la dirección URL para la cuenta que desea agregar a la aplicación.

> [AZURE.NOTE] ¿No está seguro de cómo obtener el código QR? Para obtener ayuda, póngase en contacto con el Servicio de asistencia.
> 
> ¿No puede activar su cuenta con la nueva aplicación? Póngase en contacto con el Servicio de asistencia.
>


Una vez que tenga el código QR delante, inicie la aplicación. Haga clic en +.


![Add account](./media/multi-factor-authentication-azure-authenticator/addaccount.png)

Con esto se iniciará la cámara para examinar el código QR. Si no puede examinar el código QR, siempre tiene la opción de entrada manual.

Para confirmar que la cuenta se ha activado correctamente, compruebe que la nueva cuenta aparece en las páginas de las cuentas.


Siga este paso para todas las cuentas que desee migrar a la nueva aplicación.



**Paso 3:** desinstalar la aplicación anterior Multi-Factor Authentication en el teléfono.

Cuando haya agregado todas las cuentas a la nueva aplicación, desinstale la aplicación antigua en el teléfono.

¿Quiere saber cómo quitar cuentas individuales de la aplicación antigua? Puntee en la cuenta. Obtendrá una opción para "Borrar".

![Quitar cuenta](./media/multi-factor-authentication-azure-authenticator/remove.png)

## Cómo agregar una cuenta mediante el escáner de códigos de barras



- En primer lugar, vaya a la página de configuración de comprobación de seguridad. Para obtener información sobre cómo ir a esta página, vea [Cambiar la configuración de seguridad](multi-factor-authentication-end-user-manage-settings.md).

- Haga clic en el botón Configurar.
 
![Add account](./media/multi-factor-authentication-azure-authenticator/azureauthe.png)

- Aparecerá una pantalla con un código de barras.
  
![Digitalizar código de barras](./media/multi-factor-authentication-azure-authenticator/barcode2.png)

- Abra ahora la aplicación de Azure Authenticator; debería ir a la página de cuentas. Aquí verá una lista de las cuentas que ha configurado. Si quiere agregar una cuenta nueva, haga clic en el signo +. Se abrirá el escáner.

![Add account](./media/multi-factor-authentication-azure-authenticator/addaccount3.png)

- Digitalice el código de barras. 

![Add account](./media/multi-factor-authentication-azure-authenticator/scan.png)

- Espere mientras se activa la cuenta.

- Eso es todo. Debería ver ahora la nueva cuenta en la página de cuentas.

![Add account](./media/multi-factor-authentication-azure-authenticator/addaccount2.png)


## Cómo agregar una cuenta de Azure manualmente

Si quiere agregar una cuenta manualmente, puede hacerlo de la siguiente manera:

- En primer lugar, vaya a la página de configuración de comprobación de seguridad. Para obtener información sobre cómo ir a esta página, vea [Cambiar la configuración de seguridad](multi-factor-authentication-end-user-manage-settings.md).

- Haga clic en el botón Configurar.
 
![Add account](./media/multi-factor-authentication-azure-authenticator/azureauthe.png)

- Aparecerá una pantalla con un código de barras. Tenga en cuenta el código y la URL que se encuentra debajo del código de barras.
  
![Digitalizar código de barras](./media/multi-factor-authentication-azure-authenticator/barcode2.png)

- Abra ahora la aplicación de Azure Authenticator; debería ir a la página de cuentas. Aquí verá una lista de las cuentas que ha configurado. Si quiere agregar una cuenta nueva, haga clic en el signo +. Se abrirá el escáner.

![Add account](./media/multi-factor-authentication-azure-authenticator/addaccount3.png)

- Haga clic en el botón para especificar manualmente de la parte inferior.

![Add account](./media/multi-factor-authentication-azure-authenticator/scan.png)

- Especifique el código y la URL que se ofrece en la misma página que le muestra el código de barras. Esto se incluye en los cuadros de código y dirección URL en la aplicación móvil. Esto comenzará la instalación.

![Add account](./media/multi-factor-authentication-azure-authenticator/manual.png)

- Espere mientras se activa la cuenta.

- Eso es todo. Debería ver ahora la nueva cuenta en la página de cuentas.

![Add account](./media/multi-factor-authentication-azure-authenticator/addaccount2.png)

## Cómo agregar una cuenta que no sea de Azure manualmente

Si quiere agregar una cuenta que no sea de Azure manualmente, por ejemplo, su cuenta de Microsoft, puede hacerlo de la siguiente manera:


- Agregar manualmente una cuenta que no sea de Azure puede realizarse mediante el análisis del código QR o escribiendo la clave secreta.
- Si va a especificar manualmente la clave secreta, obtenga la clave secreta en el sitio con el que está asociada la cuenta. Por ejemplo, en Outlook.com, vaya a la configuración de la cuenta, la configuración de seguridad y seleccione la opción para configurar una aplicación de autenticación. Tiene que seleccionar que indica que no puede escanear el código de barras para obtener la clave secreta.
- 

![Add account](./media/multi-factor-authentication-azure-authenticator/secretkey.png)

- Abra la aplicación Azure Authenticator; debería ir a la página de cuentas. Aquí verá una lista de las cuentas que ha configurado. Si quiere agregar una cuenta nueva, haga clic en el signo +. Se abrirá el escáner.

![Add account](./media/multi-factor-authentication-azure-authenticator/addaccount3.png)

- Examine el código QR o haga clic en el botón para especificar manualmente de la parte inferior. Si examina el código QR, omita el paso siguiente, debido a que la activación comenzará inmediatamente.

![Add account](./media/multi-factor-authentication-azure-authenticator/scan.png)

- Si va a introducir la clave secreta manualmente, escriba el nombre de la cuenta y la clave secreta que se ofrece en la misma página que le muestra el código de barras. Esto se incluye en los cuadros de código y dirección URL en la aplicación móvil. Esto comenzará la instalación.

![Add account](./media/multi-factor-authentication-azure-authenticator/manual.png)

- Espere mientras se activa la cuenta. Ahora debería ver la nueva cuenta agregada.

![Add account](./media/multi-factor-authentication-azure-authenticator/msaccount.png)

- Para completar el proceso, especifique el código que se encuentra debajo de su cuenta (en este caso es 875 756) y escríbalo en el cuadro de la página de la que recibió la clave secreta y haga clic en Siguiente.  

![Add account](./media/multi-factor-authentication-azure-authenticator/verify.png)

## Cómo agregar una cuenta con TouchID
La aplicación móvil de Azure Authenticator en iOS admite Touch ID. Azure Multi-Factor Authentication permite a las organizaciones requerir un PIN además de disponer de su dispositivo registrado. Con esta nueva característica, los usuarios de iOS con dispositivos habilitados con Touch ID ya no necesitan volver a escribir el PIN. Cuando se configure, los usuarios solo digitalizarán su huella digital en lugar de escribir el PIN y pulsar Aprobar.

La configuración de Touch ID con Azure Authenticator es realmente sencilla. Solo tiene que completar un desafío de comprobación normal con PIN, y si su dispositivo admite Touch ID, lo configuraremos automáticamente para usted.

![Touch ID](./media/multi-factor-authentication-azure-authenticator/touchid1.png)

A partir de ese momento, cuando se requiera que compruebe el inicio de sesión, pulsará en la notificación de inserción recibida y digitalizará su huella dactilar en lugar de escribir su PIN.

![Touch ID](./media/multi-factor-authentication-azure-authenticator/touchid2.png)

<!---HONumber=AcomDC_0218_2016-->