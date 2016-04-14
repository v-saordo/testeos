<properties 
	pageTitle="Problemas con Azure Multi-Factor Authentication | Microsoft Azure" 
	description="Este documento ofrecerá a los usuarios información sobre qué hacer si se encuentran con un problema con Azure Multi-Factor Authentication" 
	services="multi-factor-authentication"
	keywords = "cliente de multi-factor authentication, problema de autenticación, identificador de correlación"
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
	ms.date="02/25/2016" 
	ms.author="billmath"/>

# Problemas con Azure Multi-Factor Authentication
>[AZURE.IMPORTANT]
Ayúdenos a mejorar esta página. Si no encuentra una respuesta a su problema en esta página, ofrezca comentarios detallados para que agregarla.

La siguiente información le ayudará a resolver algunos de los problemas comunes que podría encontrar.


- [Errores de Id. de correlación](#correlation-id-errors)
- [Perdí el teléfono o me lo robaron](#i-have-lost-my-phone-or-it-was-stolen)
- [Deseo cambiar mi número de teléfono](#i-want-to-change-my-phone-number)
- [Tengo un teléfono nuevo y necesito cambiar mi número de teléfono.](#i-have-a-new-phone-and-need-to-change-my-phone-number)
- [No recibo ningún código en el teléfono](#i-am-not-receiving-a-code-or-a-call-on-my-phone)
- [Las contraseñas de la aplicación no funcionan](#app-passwords-are-not-working)
- [¿Cómo puedo quitar Azure Authenticator del dispositivo anterior y moverlo a uno nuevo?](#how-do-i-clean-up-azure-authenticator-from-my-old-device-and-move-to-a-new-one)
- [No encuentro una respuesta a mi problema.](#i-didnt-find-an-answer-to-my-problem)

##Errores de Id. de correlación
Si ha probado los pasos de solución de problemas siguientes y continúa teniendo problemas, puede publicar una pregunta en los [foros de Azure AD](https://social.msdn.microsoft.com/forums/azure/home?forum=WindowsAzureAD), [Buscar en Microsoft Knowledge Base (KB)](https://www.microsoft.com/es-ES/Search/result.aspx?q=azure%20active%20directory%20connect&form=mssupport) o [ponerse en contacto con el servicio de soporte técnico](https://support.microsoft.com/es-ES) y estudiaremos el problema en cuanto nos sea posible.

Cuando se comunique con el soporte técnico, se recomienda que incluya la información siguiente:

 - **Descripción general del error**: qué mensaje de error exacto ha obtenido el usuario. Si no hay ningún mensaje de error, describe detalladamente el comportamiento inesperado observado.
 - **Página**: en qué página estaba cuando se generó el error (incluir la dirección URL).
 - **Código de error**: el código de error específico que ha recibido.
 - **Id. de sesión**: el id. de sesión específico que ha recibido.
 - **Id. de correlación**: el código de id. de correlación generado cuando el usuario vio el error.
 - **Marca de tiempo**: en qué fecha y a qué hora exactamente se ha generado el error (incluir la zona horaria).
 
![Id. de correlación](./media/multi-factor-authentication-end-user-manage/correlation.png)

 - **Id. de usuario**: cuál fue el identificador del usuario que ha obtenido el error (por ejemplo, user@contoso.com)?
 - **Información sobre el usuario**: si se trataba de un usuario federado, con sincronización de hash de contraseña o solo de la nube. ¿El usuario tiene asignada una licencia de Azure AD Premium, Enterprise Mobility o Azure AD Básico? ¿El usuario utiliza Office 365? etc.

Incluir esta información nos ayudará a solucionar el problema lo antes posible.

## Perdí el teléfono o me lo robaron
Si perdió el teléfono o se lo robaron, le recomendamos que solicite al administrador que restablezca las [contraseñas de la aplicación](multi-factor-authentication-manage-users-and-devices.md#delete-users-existing-app-passwords) y que borre todos los [dispositivos recordados](multi-factor-authentication-manage-users-and-devices.md#restore-mfa-on-all-suspended-devices-for-a-user).

Si desea volver a obtener acceso a la cuenta, tiene dos opciones. En primer lugar, si configuró un número de teléfono de autenticación alternativo, puede usarlo para volver a obtener acceso a la cuenta y cambiar la configuración de seguridad.

Si especificó un número de teléfono de autenticación secundarios, puede usarlo para iniciar sesión. ![Configuración](./media/multi-factor-authentication-end-user-manage/altphone.png) Observe que, en la captura de pantalla anterior, se configuraron dos números de teléfono. Uno de los números termina en 67 y el segundo, en 30.
  
Para iniciar sesión con el número de teléfono alternativo, inicie sesión de la manera habitual y, luego, simplemente elija **Usar una opción de comprobación distinta**. ![Comprobación distinta](./media/multi-factor-authentication-end-user-manage/differentverification.png)

Luego, seleccione el otro número de teléfono. En este caso, seleccionaría **Llamarme al +X XXXXXXXX30**.

![Teléfono alternativo](./media/multi-factor-authentication-end-user-manage/altphone2.png)

>[AZURE.IMPORTANT]
Es importante configurar un número de teléfono de autenticación secundario. Debido a que el número de teléfono principal y la aplicación móvil probablemente se encuentran en el mismo teléfono, el número de teléfono secundario es la única forma que tiene para volver a tener acceso a su cuenta si se le pierde el teléfono o si se lo robaron.

Si no configuró un número de teléfono de autenticación secundario, deberá ponerse en contacto con el administrador para pedirle que borre su configuración, para que, la próxima vez que inicie sesión, se le solicite volver a [configurar la autenticación multifactor](multi-factor-authentication-manage-users-and-devices.md#require-selected-users-to-provide-contact-methods-again).

## Deseo cambiar mi número de teléfono
Según cómo use la autenticación multifactor, existen algunos lugares donde puede cambiar ajustes como su número de teléfono. Use la tabla que aparece a continuación para elegir la opción más conveniente.

Cómo usa la autenticación multifactor|Descripción
:------------- | :------------- | 
[La uso con Office 365](#changing-your-settings-with-office-365)| Esto significa que tendrá que cambiar la configuración a través del portal de Office 365.
[No lo sé](#changing-your-settings-with-the-myapps-portal)|Esto significa que deberá iniciar sesión en [http://myapps.microsoft.com](http://myapps.microsoft.com) y cambiar aquí su configuración.
[La uso con Microsoft Azure](#changing-your-settings-with-microsoft-azure)| Esto significa que tendrá que cambiar la configuración a través del Portal de Azure.


 
### Cambio de la configuración con Office 365


Si utiliza la autenticación multifactor con Office 365 deberá administrar la configuración de comprobación de seguridad adicional a través del portal de Office 365.

#### Cambio de la configuración en el Portal de Office 365

1. Inicie sesión en el [Portal de Office 365](https://login.microsoftonline.com/).
2. En la esquina superior derecha, seleccione el widget y elija la configuración de Office 365.
3. Haga clic en Comprobación de seguridad adicional.
4. A la derecha, haga clic en el vínculo que dice **Actualizar los números de teléfono usados para la seguridad de cuenta.** ![O365](./media/multi-factor-authentication-end-user-manage/o365a.png)
5. Esto le llevará a la página que le permitirá cambiar la configuración. ![O365](./media/multi-factor-authentication-end-user-manage/o365b.png)


### Cambio de la configuración con el Portal de Myapps

Si no está seguro de cómo usar la autenticación multifactor, siempre puede cambiar la configuración mediante el Portal de Myapps.

#### Cambio de la configuración en el Portal de Myapps

1. Inicie sesión en [https://myapps.microsoft.com](https://myapps.microsoft.com).	
2. En la parte superior, seleccione el perfil.
3. Seleccione Comprobación de seguridad adicional. ![MyApps](./media/multi-factor-authentication-end-user-manage/myapps1.png)
4. Esto le llevará a la página que le permitirá cambiar la configuración.

![Página de proofup](./media/multi-factor-authentication-end-user-manage-myapps/proofup.png)

### Cambio de la configuración con Microsoft Azure

Si usa la autenticación multifactor con Azure, le interesará cambiar la configuración mediante el Portal de Azure.

#### Para obtener acceso a la configuración de la comprobación de seguridad adicional en el Portal de Azure


1. Inicie sesión en el Portal de Azure.
2. En la parte superior del Portal de Azure, haga clic en su nombre de usuario. Aparecerá un cuadro de lista desplegable.
3. En el cuadro desplegable, seleccione Comprobación de seguridad adicional. ![Las tablas de Azure](./media/multi-factor-authentication-end-user-manage/azure1.png)
4. Esto le llevará a la página que le permitirá cambiar la configuración. ![Página de proofup](./media/multi-factor-authentication-end-user-manage-azure/proofup.png)

##Tengo un teléfono nuevo y necesito cambiar mi número de teléfono.

Si tiene un nuevo teléfono y necesita cambiar el número de contacto principal que usa mfa, puede hacerlo de una de las dos maneras posibles.

>[AZURE.IMPORTANT]
Es importante configurar un número de teléfono de autenticación secundario. Debido a que el número de teléfono principal y la aplicación móvil probablemente se encuentran en el mismo teléfono, el número de teléfono secundario es la única forma que tiene para volver a tener acceso a su cuenta si se le pierde el teléfono o si se lo robaron.

La primera es mediante un método de autenticación secundario. Si especificó un número de teléfono de autenticación secundarios, puede usarlo para iniciar sesión. ![Configuración](./media/multi-factor-authentication-end-user-manage/altphone.png) Observe que, en la captura de pantalla anterior, se configuraron dos números de teléfono. Uno de los números termina en 67 y el segundo, en 30.
  
Para iniciar sesión con el número de teléfono alternativo, inicie sesión de la manera habitual y, luego, simplemente elija **Usar una opción de comprobación distinta**. ![Comprobación distinta](./media/multi-factor-authentication-end-user-manage/differentverification.png)

Luego, seleccione el otro número de teléfono. En este caso, seleccionaría **Llamarme al +X XXXXXXXX30**.

![Teléfono alternativo](./media/multi-factor-authentication-end-user-manage/altphone2.png)

La segunda es ponerse en contacto con el administrador o con la persona que configura mfa por usted. Solo debe hacerlo si no ha configurado un número de teléfono de autenticación secundario. De lo contrario, tendrá que ponerse en contacto con el administrador o la persona que configuró mfa y pedirle que borre su configuración, para que, la próxima vez que inicie sesión, se le solicite volver a [configurar la autenticación multifactor](multi-factor-authentication-manage-users-and-devices.md#require-selected-users-to-provide-contact-methods-again).

##No recibo ningún código o llamada en el teléfono

En primer lugar, debe asegurarse de lo siguiente:



- Si ha seleccionado recibir una llamada de teléfono en su teléfono móvil, asegúrese de que tiene una señal de móvil adecuada. La disponibilidad y la velocidad de la entrega puede variar según la ubicación y el proveedor de servicios.
- Si eligió recibir códigos de comprobación por mensaje de texto en su teléfono móvil, asegúrese de que su dispositivo y su plan de servicio admitan la entrega de mensajes de texto. La disponibilidad y la velocidad de la entrega puede variar según la ubicación y el proveedor de servicios. Además, asegúrese de contar con una señal de celular adecuada cuando intenta recibir estos códigos.
- Si eligió recibir una comprobación a través de la aplicación móvil, asegúrese de tener una señal de celular considerable. Además, recuerde que la disponibilidad y la velocidad de la entrega puede variar según la ubicación y el proveedor de servicios. 

Si tiene un smartphone, le recomendamos usar la [aplicación Azure Authenticator](multi-factor-authentication-azure-authenticator.md).

Si desea cambiar entre la opción de recibir los códigos de comprobación por mensajes de texto o a través de la aplicación móvil, elija **Usar una opción de comprobación distinta**.

![Comprobación distinta](./media/multi-factor-authentication-end-user-manage/differentverification.png)

En algunas ocasiones, la entrega de uno de estos servicios resulta más confiable que la otra.

Tenga en cuenta que, si recibió varios códigos de comprobación, solo funcionará el más reciente.

Si anteriormente configuró un teléfono de reserva, le recomendamos que vuelva a intentarlo. Para ello, seleccione ese teléfono cuando se le solicite en la página de inicio de sesión. Si no tiene configurado otro método, póngase en contacto con el administrador y pídale que borre su configuración, para que, la próxima vez que inicie sesión, se le solicite volver a [configurar la autenticación multifactor](multi-factor-authentication-manage-users-and-devices.md#require-selected-users-to-provide-contact-methods-again).

##Las contraseñas de la aplicación no funcionan
En primer lugar, asegúrese de haber escrito correctamente la contraseña de la aplicación. Si sigue sin funcionar, vuelva a intentar el inicio de sesión y [cree una contraseña de aplicación nueva](multi-factor-authentication-end-user-app-passwords.md). Si esto no funciona, póngase en contacto con el administrador y pídale que [elimine sus contraseñas de aplicación existentes](multi-factor-authentication-manage-users-and-devices.md#delete-users-existing-app-passwords) y luego cree una nueva y úsela.

##¿Cómo puedo quitar Azure Authenticator del dispositivo anterior y moverlo a uno nuevo?
Cuando desinstala la aplicación del dispositivo o vuelve a programar el dispositivo, no se quita la activación en el back-end. Debe usar los pasos que se detallan en [Migrar a un dispositivo nuevo](multi-factor-authentication-azure-authenticator.md#how-to-move-to-the-new-azure-authenticator-app).

##No encuentro una respuesta a mi problema.
Si no encuentra una respuesta a su problema en esta página, puede publicar una pregunta en los [Foros de Azure AD](https://social.msdn.microsoft.com/forums/azure/home?forum=WindowsAzureAD), [buscar en Microsoft Knowledge Base (KB)](https://www.microsoft.com/es-ES/Search/result.aspx?q=azure%20active%20directory%20connect&form=mssupport) o [ponerse en contacto con el soporte técnico](https://support.microsoft.com/es-ES).

Además, puede ponerse en contacto con el administrador o con la persona que configuró la autenticación multifactor para usted y ver si puede ayudarle.

Por último, asegúrese de dejar algunos comentarios detallados en esta página para que podemos actualizarla y continuar mejorándola proporcionando más información.

<!---HONumber=AcomDC_0302_2016-->