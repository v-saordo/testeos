
<properties 
    pageTitle="Uso de Azure RemoteApp con cuentas de usuario de Office 365 | Microsoft Azure"
	description="Uso de Azure RemoteApp con las cuentas de usuario de Office 365"
	services="remoteapp"
	documentationCenter="" 
	authors="piotrci" 
	manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="02/08/2016" 
    ms.author="elizapo" />



# Uso de Azure RemoteApp con las cuentas de usuario de Office 365
Si tiene una suscripción a Office 365, dispone de un Azure Active Directory que almacena los nombres de usuario y contraseñas que se usan para tener acceso a servicios de Office 365. Por ejemplo, cuando los usuarios activan Office 365 ProPlus, autentican en Azure AD para buscar licencias. A la mayoría de los clientes le gustaría usar el mismo directorio con Azure RemoteApp.

Si está implementando Azure RemoteApp, lo más probable es que esté usando una suscripción de Azure asociada a un Azure AD diferente. Para poder usar el directorio de Office 365, debe mover la suscripción de Azure a ese directorio.

Para obtener información sobre cómo implementar aplicaciones cliente de Office 365, consulte [Uso de la suscripción a Office 365 con Azure RemoteApp](remoteapp-officesubscription.md).
 
## Fase 1: Registro de su suscripción gratuita de Office 365 Azure Active Directory
Si utiliza el Portal de Azure clásico, siga los pasos de [Registre su suscripción gratuita de Azure Active Directory](https://technet.microsoft.com/library/dn832618.aspx) para obtener acceso administrativo a Azure AD a través del Portal de administración de Azure. Como resultado de este proceso, debe ser capaz de iniciar sesión en el Portal de Azure y ver su directorio ahí. En este momento no verá mucho más, puesto que la suscripción de Azure completa que usa con Azure RemoteApp está en un directorio diferente.

Recuerde el nombre y la contraseña de la cuenta de administrador creada en este paso. Los necesitará en la fase 2.

Si utiliza el portal de Azure, consulte [How to register and activate a free Azure Active Directory using Office 365 portal (Cómo registrar y activar Azure Active Directory de manera gratuita con el portal de Office 365)](http://azureblogger.com/2016/01/how-to-register-and-activate-a-free-azure-active-directory-using-office-365-portal/).

## Fase 2: Cambio del Azure AD asociado a la suscripción de Azure
Vamos a cambiar su suscripción de Azure desde el directorio actual en el directorio de Office 365 con el que trabajamos en la fase 1.

Siga las instrucciones descritas en [Cambio del inquilino de Azure Active Directory en Azure RemoteApp](remoteapp-changetenant.md). Preste especial atención a los siguientes aspectos:

- Paso n.º 1: si ha implementado Azure RemoteApp (ARA) en esta suscripción, asegúrese de quitar todas las cuentas de usuario de Azure AD de cualquier colección ARA en primer lugar antes de intentar nada más. También puede eliminar las colecciones existentes.
- Paso n.º 2: este es un paso crítico. Debe usar una cuenta de Microsoft (por ejemplo, @outlook.com) como administrador de servicios en la suscripción; Esto es porque no podemos tener cuentas de usuario del Azure AD existente vinculadas a la suscripción: si se hace, no podremos moverla a un Azure AD diferente.
- Paso n.º 4: al agregar un directorio existente, el sistema le pedirá que inicie sesión con la cuenta de administrador para ese directorio. Asegúrese de usar la cuenta de administrador de la fase 1.
- Paso n.º 5: cambio del directorio principal de la suscripción a su directorio de Office 365. El resultado final debe ser que aparezca la suscripción en el directorio de Office 365 en Configuración -> Suscripciones. ![Cambio del directorio principal de la suscripción](./media/remoteapp-o365user/settings.png)
 

En este momento, su suscripción de Azure RemoteApp está asociada al Office 365 Azure AD. Puede usar las cuentas de usuario existentes de Office 365 con Azure Remote App.

<!---HONumber=AcomDC_0211_2016-->