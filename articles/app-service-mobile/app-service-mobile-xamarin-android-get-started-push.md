<properties
	pageTitle="Agregar notificaciones push a la aplicación de Xamarin.Android con el Servicio de aplicaciones de Azure"
	description="Más información sobre cómo usar Servicios de aplicaciones de Azure y Centros de notificaciones de Azure para enviar notificaciones push a la aplicación de Xamarin Android"
	services="app-service\mobile"
	documentationCenter="xamarin"
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-android"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="02/04/2016"
	ms.author="glenga"/>

# Agregar notificaciones push a la aplicación de Xamarin.Android

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

##Información general

En este tutorial, agregará notificaciones push al proyecto de [inicio rápido de Xamarin.Android] para que cada vez que se inserte un registro, se envíe una notificación push. Este tutorial se basa en el tutorial de [inicio rápido de Xamarin.Android], que debe completar primero. Si no usa el proyecto de servidor de inicio rápido descargado, debe agregar el paquete de extensión de notificaciones push al proyecto. Para obtener más información acerca de los paquetes de extensión de servidor, consulte [Trabajar con el SDK del servidor back-end de .NET para Aplicaciones móviles de Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

##Requisitos previos

Este tutorial requiere lo siguiente:

+ Una cuenta de Google activa. Puede registrarse para obtener una cuenta de Google en [accounts.google.com](http://go.microsoft.com/fwlink/p/?LinkId=268302).

+ [Componente cliente Servicio de mensajería en la nube de Google](http://components.xamarin.com/view/GCMClient/) Agregará este componente durante este tutorial.

+ Tutorial de [inicio rápido de Xamarin.Android] completado.


##<a name="create-hub"></a>Crear un Centro de notificaciones

[AZURE.INCLUDE [app-service-mobile-create-notification-hub](../../includes/app-service-mobile-create-notification-hub.md)]

##<a id="register"></a>Habilitación del servicio de mensajería en la nube de Google

[AZURE.INCLUDE [Habilitación de GCM](../../includes/mobile-services-enable-google-cloud-messaging.md)]

##Configurar el back-end de una aplicación móvil para que envíe solicitudes push

[AZURE.INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

##<a id="update-server"></a>Actualizar el proyecto de servidor para que envíe notificaciones push

[AZURE.INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

##<a id="configure-app"></a>Configurar el proyecto cliente para las notificaciones push

[AZURE.INCLUDE [mobile-services-xamarin-android-push-configure-project](../../includes/mobile-services-xamarin-android-push-configure-project.md)]

##<a id="add-push"></a>Incorporación de código de notificaciones de inserción a la aplicación

[AZURE.INCLUDE [app-service-mobile-xamarin-android-push-add-to-app](../../includes/app-service-mobile-xamarin-android-push-add-to-app.md)]

## <a name="test"></a>Prueba de las notificaciones push en su aplicación

Puede probar la aplicación con un dispositivo virtual en el emulador. Hay pasos de configuración adicionales necesarios cuando se ejecuta en un emulador.

1. Asegúrese de que va a implementar o depurar en un dispositivo virtual que tenga las API de Google como destino, como se muestra a continuación en el administrador de dispositivo virtual Android (AVD).

	![](./media/app-service-mobile-xamarin-android-get-started-push/google-apis-avd-settings.png)

2. Agregue una cuenta de Google a los dispositivos Android haciendo clic en **Aplicaciones** > **Configuración** > **Agregar cuenta** y siga los avisos para usar Agregar una cuenta de Google existente al dispositivo o crear una nueva.

	![](./media/app-service-mobile-xamarin-android-get-started-push/add-google-account.png)

3. Ejecute la aplicación todolist como antes e inserte un nuevo elemento todo. Esta vez, se muestra un icono de notificación en el área de notificación. Puede abrir el cajón de notificaciones para ver el texto completo de la notificación.

	![](./media/app-service-mobile-xamarin-android-get-started-push/android-notifications.png)


<!-- URLs. -->
[inicio rápido de Xamarin.Android]: app-service-mobile-xamarin-android-get-started.md

[Google Cloud Messaging Client Component]: http://components.xamarin.com/view/GCMClient/
[Xamarin.Android]: http://xamarin.com/download/
[Azure Mobile Services Component]: http://components.xamarin.com/view/azure-mobile-services/

<!---HONumber=AcomDC_0211_2016-->