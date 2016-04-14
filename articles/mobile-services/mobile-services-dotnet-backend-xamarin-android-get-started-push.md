<properties
	pageTitle="Introducción a Servicios móviles para aplicaciones de Android de Xamarin | Microsoft Azure"
	description="Obtenga información acerca de cómo usar Servicios móviles de Azure y centros de notificaciones para enviar notificaciones de inserción a la aplicación Xamarin Android"
	services="mobile-services"
	documentationCenter="xamarin"
	authors="ggailey777"
	manager="dwrede"
	editor="mollybos"/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-android"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="12/11/2015"
	ms.author="glenga"/>

# Incorporación de notificaciones de inserción a la aplicación de Servicios móviles

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-get-started-push](../../includes/mobile-services-selector-get-started-push.md)]

##Información general

Este tema muestra cómo puede usar Servicios móviles de Azure para enviar notificaciones de inserción a una aplicación de Xamarin.Android. En este tutorial aprenderá a agregar notificaciones de inserción con el servicio de mensajería en la nube de Google (GCM) al proyecto [Introducción a Servicios móviles]. Cuando haya finalizado, el servicio móvil le enviará una notificación de inserción cada vez que se inserte un registro.

Este tutorial requiere lo siguiente:

+ Una cuenta de Google activa.
+ [Componente cliente Servicio de mensajería en la nube de Google] Agregará este componente durante este tutorial.

Ya debe tener instalados los componentes [Xamarin.Android] y [Servicios móviles de Azure][Azure Mobile Services Component] en el proyecto desde el momento en que completó [Introducción a los Servicios móviles].

##<a id="register"></a>Habilitación del servicio de mensajería en la nube de Google

[AZURE.INCLUDE [Habilitación de GCM](../../includes/mobile-services-enable-google-cloud-messaging.md)]

##<a id="configure"></a>Configuración del servicio móvil para enviar solicitudes de inserción

[AZURE.INCLUDE [mobile-services-android-configure-push](../../includes/mobile-services-android-configure-push.md)]

##<a id="update-server"></a>Actualización del servicio móvil para enviar notificaciones de inserción

[AZURE.INCLUDE [mobile-services-dotnet-backend-android-push-update-service](../../includes/mobile-services-dotnet-backend-android-push-update-service.md)]

##<a id="configure-app"></a>Configuración del proyecto existente para notificaciones de inserción

[AZURE.INCLUDE [mobile-services-xamarin-android-push-configure-project](../../includes/mobile-services-xamarin-android-push-configure-project.md)]

##<a id="add-push"></a>Incorporación de código de notificaciones de inserción a la aplicación

[AZURE.INCLUDE [mobile-services-xamarin-android-push-add-to-app](../../includes/mobile-services-xamarin-android-push-add-to-app.md)]

##<a name="test-app"></a>Prueba de la aplicación con el servicio móvil publicado

Puede probar la aplicación conectando directamente un teléfono Android con un cable USB o utilizando un dispositivo virtual en el emulador.

###<a id="local-testing"></a> Habilitación de notificaciones de inserción para pruebas locales

[AZURE.INCLUDE [mobile-services-dotnet-backend-configure-local-push](../../includes/mobile-services-dotnet-backend-configure-local-push.md)]

[AZURE.INCLUDE [mobile-services-android-push-notifications-test](../../includes/mobile-services-android-push-notifications-test.md)]

<!-- URLs. -->
[Introducción a Servicios móviles]: mobile-services-dotnet-backend-xamarin-android-get-started.md
[Introducción a los Servicios móviles]: mobile-services-dotnet-backend-xamarin-android-get-started.md


[Componente cliente Servicio de mensajería en la nube de Google]: http://components.xamarin.com/view/GCMClient/
[Xamarin.Android]: http://xamarin.com/download/
[Azure Mobile Services Component]: http://components.xamarin.com/view/azure-mobile-services/

<!---HONumber=AcomDC_1217_2015-->