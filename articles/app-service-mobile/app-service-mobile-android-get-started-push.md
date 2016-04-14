<properties
	pageTitle="Incorporación de notificaciones push a la aplicación de Android con las Aplicaciones móviles de Azure"
	description="Obtenga información acerca de cómo usar las Aplicaciones móviles de Azure para enviar notificaciones push a su aplicación de Android."
	services="app-service\mobile"
	documentationCenter="android"
	manager="dwrede"
	editor=""
	authors="ysxu"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="02/04/2016"
	ms.author="yuaxu"/>

# Incorporación de notificaciones push a la aplicación de Android

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## Información general
En este tutorial, agregará notificaciones push al proyecto de [inicio rápido de Android] para que cada vez que se inserte un registro, se envíe una notificación push. Este tutorial está basado en el tutorial de [inicio rápido de Android], que debe completar primero. Si no usa el proyecto de servidor de inicio rápido descargado, debe agregar el paquete de extensión de notificaciones push al proyecto. Para obtener más información acerca de los paquetes de extensión de servidor, consulte [Trabajar con el SDK del servidor back-end de .NET para Aplicaciones móviles de Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

##Requisitos previos

Para completar este tutorial, necesitará lo siguiente:

* Una [cuenta de Google](http://go.microsoft.com/fwlink/p/?LinkId=268302) con una dirección de correo electrónico verificada
* [Visual Studio Community 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934); no es necesario para un proyecto de back-end de Node.js.
* Complete el [tutorial de inicio rápido](app-service-mobile-android-get-started.md).

##<a name="create-hub"></a>Creación de un centro de notificaciones

[AZURE.INCLUDE [app-service-mobile-create-notification-hub](../../includes/app-service-mobile-create-notification-hub.md)]

## Habilitación del servicio de mensajería en la nube de Google

[AZURE.INCLUDE [mobile-services-enable-google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

##Configuración del back-end de aplicaciones de móvil para enviar solicitudes push

[AZURE.INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

##<a id="update-service"></a>Actualización del proyecto de servidor para enviar notificaciones push

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-configure-push-google](../../includes/app-service-mobile-dotnet-backend-configure-push-google.md)]

## Incorporación de notificaciones de inserción a la aplicación

Debe asegurarse de que su proyecto de aplicación Android está listo para controlar las notificaciones de inserción.

###Comprobación de la versión del SDK de Android

[AZURE.INCLUDE [app-service-mobile-verify-android-sdk-version](../../includes/app-service-mobile-verify-android-sdk-version.md)]

El siguiente paso es instalar los servicios de Google Play. El servicio de mensajería en la nube de Google tiene algunos requisitos mínimos en el nivel de API para desarrollo y prueba, que debe cumplir la propiedad **minSdkVersion** del manifiesto.

Si va a realizar pruebas con un dispositivo antiguo, consulte [Configuración del SDK de Google Play Services] para determinar el valor mínimo que puede configurar, y cómo configurarlo de forma adecuada.

###Incorporación de Google Play Services al proyecto

[AZURE.INCLUDE [Incorporación de Play Services](../../includes/app-service-mobile-add-google-play-services.md)]

###Incorporación de código

[AZURE.INCLUDE [app-service-mobile-android-getting-started-with-push](../../includes/app-service-mobile-android-getting-started-with-push.md)]

## Prueba de la aplicación con el servicio móvil publicado

Puede probar la aplicación conectando directamente un teléfono Android con un cable USB o utilizando un dispositivo virtual en el emulador.

##<a id="more"></a>Más

* Las etiquetas permiten dirigirse a clientes segmentados con inserciones. [Trabajar con el SDK del servidor back-end de .NET para Aplicaciones móviles de Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) muestra cómo agregar etiquetas a la instalación de un dispositivo.

<!-- URLs -->
[inicio rápido de Android]: app-service-mobile-android-get-started.md

[Configuración del SDK de Google Play Services]: https://developers.google.com/android/guides/setup

<!---HONumber=AcomDC_0211_2016-->