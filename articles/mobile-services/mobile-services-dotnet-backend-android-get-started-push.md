<properties
	pageTitle="Introducción a la inserción (Android) | Microsoft Azure"
	description="Obtenga información acerca de cómo usar Servicios móviles de Azure para enviar notificaciones de inserción a su aplicación .Net Android."
	services="mobile-services, notification-hubs"
	documentationCenter="android"
	authors="RickSaling"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="02/07/2016"
	ms.author="ricksal"/>

# Incorporación de notificaciones de inserción a la aplicación de Servicios móviles

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-get-started-push](../../includes/mobile-services-selector-get-started-push.md)]

En este tema se muestra cómo usar los Servicios móviles de Azure para enviar notificaciones de inserción a la aplicación Android. En este tutorial aprenderá a agregar notificaciones de inserción al proyecto de inicio rápido con el servicio de mensajería en la nube de Google (GCM). Cuando haya finalizado, el servicio móvil le enviará una notificación de inserción cada vez que se inserte un registro.

Este tutorial está basado en el inicio rápido de Servicios móviles. Antes de iniciar este tutorial, primero debe completar [Introducción a Servicios móviles] para conectar su proyecto al servicio móvil. Por este motivo, este tutorial requiere también Visual Studio 2013.

## Código de ejemplo
Para ver el código fuente completo, vaya [aquí](https://github.com/RickSaling/mobile-services-samples/tree/push/GettingStartedWithPush).

## Habilitación del servicio de mensajería en la nube de Google

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

## Configuración del servicio móvil para enviar solicitudes de inserción

[AZURE.INCLUDE [mobile-services-android-configure-push](../../includes/mobile-services-android-configure-push.md)]

## Actualización del servicio móvil para enviar notificaciones push

[AZURE.INCLUDE [mobile-services-dotnet-backend-android-push-update-service](../../includes/mobile-services-dotnet-backend-android-push-update-service.md)]

## Incorporación de notificaciones de inserción a la aplicación

###Comprobación de la versión del SDK de Android

[AZURE.INCLUDE [mobile-services-verify-android-sdk-version](../../includes/mobile-services-verify-android-sdk-version.md)]


El siguiente paso es instalar los servicios de Google Play. El servicio de mensajería en la nube de Google tiene algunos requisitos mínimos en el nivel de API para desarrollo y prueba, que debe cumplir la propiedad **minSdkVersion** del manifiesto.

Si va a realizar pruebas con un dispositivo antiguo, consulte [Configuración del SDK de Google Play Services] para determinar el valor mínimo que puede configurar, y cómo configurarlo de forma adecuada.

###Incorporación de Google Play Services al proyecto

[AZURE.INCLUDE [Incorporación de Play Services](../../includes/mobile-services-add-google-play-services.md)]

###Incorporación de código

[AZURE.INCLUDE [mobile-services-android-getting-started-with-push](../../includes/mobile-services-android-getting-started-with-push.md)]

## Prueba de la aplicación con el servicio móvil publicado

Puede probar la aplicación conectando directamente un teléfono Android con un cable USB o utilizando un dispositivo virtual en el emulador.

### Habilitación de notificaciones de inserción para pruebas locales

[AZURE.INCLUDE [mobile-services-dotnet-backend-configure-local-push](../../includes/mobile-services-dotnet-backend-configure-local-push.md)]

[AZURE.INCLUDE [mobile-services-android-push-notifications-test](../../includes/mobile-services-android-push-notifications-test.md)]

Ha completado correctamente este tutorial.

## Pasos siguientes

En este tutorial se muestran los conceptos básicos de la habilitación de una aplicación de Android para que use Servicios móviles y Centros de notificaciones para enviar notificaciones de inserción. Le recomendamos que después realice el siguiente tutorial, [Envío de notificaciones push a usuarios autenticados], que muestra cómo usar etiquetas para enviar estas notificaciones con etiquetas desde un servicio móvil a solo un usuario autenticado.

+ [Enviar notificaciones de difusión a los suscriptores] <br/>Sepa cómo los usuarios se pueden registrar y recibir notificaciones de inserción de categorías que les interesan.

+ [Envío de notificaciones basadas en plantillas a los suscriptores] <br/>Obtenga información sobre cómo usar las plantillas para enviar notificaciones de inserción desde un servicio móvil sin tener que diseñar cargas específicas de plataforma en el back-end.

Puede obtener más información acerca de los Servicios móviles y los Centros de notificaciones en los siguientes temas:

* [¿Qué son los Centros de notificaciones?] <br/>Obtenga más información sobre el funcionamiento de los Centros de notificaciones para entregar notificaciones a sus aplicaciones en todas las principales plataformas de cliente.

* [Depuración de aplicaciones de centros de notificaciones](http://go.microsoft.com/fwlink/p/?linkid=386630) </br>Obtenga instrucciones de resolución de problemas y depuración de soluciones de centros de notificaciones.

* [Uso de la biblioteca de cliente Android para Servicios móviles] <br/>Obtenga más información sobre cómo usar Servicios móviles con Android.

<!-- Anchors. -->

[Create a new mobile service]: #create-service
[Download the service locally]: #download-the-service-locally
[Test the mobile service]: #test-the-service
[Download the GetStartedWithData project]: #download-app
[Update the app to use the mobile service for data access]: #update-app
[Test the Android App against the service hosted locally]: #test-locally-hosted
[Publish the mobile service to Azure]: #publish-mobile-service
[Test the Android App against the service hosted in Azure]: #test-azure-hosted
[Test the app against the published mobile service]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->

<!-- URLs. -->
[Get started with push notifications (Eclipse)]: mobile-services-dotnet-backend-android-get-started-push-ec.md
[Introducción a Servicios móviles]: mobile-services-dotnet-backend-android-get-started.md
[Mobile Services SDK]: http://go.microsoft.com/fwlink/p/?LinkId=257545

[Uso de la biblioteca de cliente Android para Servicios móviles]: mobile-services-android-how-to-use-client-library.md

[¿Qué son los Centros de notificaciones?]: ../notification-hubs-overview.md
[Enviar notificaciones de difusión a los suscriptores]: ../notification-hubs-windows-store-dotnet-send-breaking-news.md
[Envío de notificaciones basadas en plantillas a los suscriptores]: ../notification-hubs-windows-store-dotnet-send-localized-breaking-news.md

<!---HONumber=AcomDC_0211_2016-->