<properties 
	pageTitle="Incorporación de notificaciones push a la aplicación de Windows 8.1 universal | Microsoft Azure" 
	description="Obtenga información acerca de cómo enviar notificaciones push a la aplicación Universal Windows 8.1 desde el servicio móvil de back-end de JavaScript a través de Centros de notificaciones de Azure" 
	services="mobile-services,notification-hubs" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/10/2015" 
	ms.author="glenga"/>


# Incorporación de notificaciones de inserción a la aplicación de Servicios móviles

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../../includes/mobile-services-selector-get-started-push.md)]

En este tema se muestra cómo usar Servicios móviles de Azure con un back-end de JavaScript para enviar notificaciones de inserción a una aplicación universal de Windows. Aprenderá a habilitar las notificaciones de inserción con los Centros de notificaciones de Azure en un proyecto de aplicación universal para Windows. Cuando termine, cada vez que se inserte un registro en la tabla TodoList, el servicio móvil enviará una notificación de inserción desde el back-end de JavaScript a todas las aplicaciones registradas en la Tienda Windows y la Tienda de Windows Phone. El centro de notificaciones que cree es gratuito con el servicio móvil, puede administrarse independientemente del servicio móvil y pueden utilizarlo otras aplicaciones y servicios.

>[AZURE.NOTE]En este tema se muestra cómo usar las herramientas de Visual Studio 2013 Update 3 para agregar compatibilidad con las notificaciones de inserción de Servicios móviles a una aplicación universal para Windows. Se pueden seguir estos mismos pasos para agregar notificaciones de inserción de Servicios móviles a una aplicación de la Tienda Windows o de la Tienda de Windows Phone 8.1. Para agregar notificaciones de inserción a una aplicación de Windows Phone 8 o Windows Phone Silverlight 8.1, consulte esta versión de [Introducción a las notificaciones de inserción en Servicios móviles](mobile-services-javascript-backend-windows-phone-get-started-push.md).

Este tutorial le guiará a través de estos pasos básicos para habilitar las notificaciones push:

1. [Registro de la aplicación para notificaciones de inserción](#register)
2. [Actualización del servicio para enviar notificaciones push](#update-service)
3. [Prueba de las notificaciones push en su aplicación](#test)

Para completar este tutorial, necesitará lo siguiente:

* Una [cuenta Microsoft Store](http://go.microsoft.com/fwlink/p/?LinkId=280045) activa.
* [Visual Studio 2013 Express para Windows](http://go.microsoft.com/fwlink/?LinkId=257546) con Update 3 o una versión posterior.

##<a id="register"></a>Registro de la aplicación para notificaciones de inserción

[AZURE.INCLUDE [mobile-services-create-new-push-vs2013](../../includes/mobile-services-create-new-push-vs2013.md)]

&nbsp;&nbsp;6. Vaya a la carpeta de proyecto `\Services\MobileServices\your_service_name`, abra el archivo de código push.register.cs generado e inspeccione el método **UploadChannel** que registra la URL de canal del dispositivo con el Centro de notificaciones.

&nbsp;&nbsp;7. Abra el archivo de código App.xaml.cs compartido y observe que se ha agregado una llamada al nuevo método **UploadChannel** en el controlador de eventos **OnLaunched**. Así se garantiza que se intentará registrar el dispositivo siempre que se inicie la aplicación.

&nbsp;&nbsp;8. Repita los pasos anteriores para agregar las notificaciones push al proyecto de aplicación de la Tienda de Windows Phone y, en el archivo App.xaml.cs compartido, quite la llamada extra al **Cliente de servicio móvil**, **UploadChannel** y el contenedor condicional `#if...#endif` restante. Ahora los dos proyectos pueden compartir una misma llamada a **UploadChannel**.

&nbsp;&nbsp;Tenga en cuenta que también puede simplificar el código generado unificando las definiciones [MobileServiceClient] del contenedor `#if...#endif` en una única definición sin contenedor, que deben usar las dos versiones de la aplicación.

Tras habilitar las notificaciones de inserción en la aplicación, actualice el servicio móvil para enviarlas.

##<a id="update-service"></a>Actualización del servicio para enviar notificaciones push

Los pasos siguientes sirven para actualizar el script de inserción registrado para la tabla TodoItem. Puede implementar código similar en cualquier script del servidor o ubicación de los servicios back-end.

[AZURE.INCLUDE [mobile-services-javascript-update-script-notification-hubs](../../includes/mobile-services-javascript-update-script-notification-hubs.md)]


##<a id="test"></a> Prueba de las notificaciones push en su aplicación

[AZURE.INCLUDE [mobile-services-javascript-backend-windows-universal-test-push](../../includes/mobile-services-javascript-backend-windows-universal-test-push.md)]

## <a name="next-steps"> </a>Pasos siguientes

En este tutorial hemos presentado las nociones para habilitar una aplicación de la Tienda Windows para que envíe notificaciones de inserción con Servicios móviles y Centros de notificaciones. A continuación, considere completar uno de los siguientes tutoriales:

+ [Enviar notificaciones de inserción a los usuarios autenticados](mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users.md) <br/>Aprenda a usar las etiquetas para enviar notificaciones de inserción desde su servicio móvil solo a un usuario autenticado.

+ [Enviar notificaciones de difusión a los suscriptores](../notification-hubs-windows-store-dotnet-send-breaking-news.md) <br/>Sepa cómo los usuarios se pueden registrar y recibir notificaciones de inserción de categorías que les interesan.

+ [Envío de notificaciones independientes de la plataforma a los suscriptores](../notification-hubs-aspnet-cross-platform-notify-users.md) <br/>Obtenga información sobre cómo usar las plantillas para enviar notificaciones de inserción desde su servicio móvil sin tener que diseñar cargas específicas de plataforma en el backend.

Puede obtener más información acerca de los Servicios móviles y los Centros de notificaciones en los siguientes temas:

* [Centros de notificaciones de Azure - Directrices de diagnóstico](../notification-hubs-diagnosing.md) <br/>Obtenga información acerca de cómo solucionar los problemas de notificaciones de inserción.

* [Introducción a la autenticación] <br/>Aprenda a autenticar a los usuarios de su aplicación con distintos tipos de cuentas con los servicios móviles.

* [¿Qué son los Centros de notificaciones?] <br/>Aprenda cómo funcionan los Centros de notificaciones para entregar notificaciones a sus aplicaciones en todas las principales plataformas cliente.

* [Uso de un cliente .NET para Servicios móviles de Azure] <br/>Obtenga más información sobre cómo usar Servicios móviles desde aplicaciones de C# Windows.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md
[Introducción a la autenticación]: mobile-services-javascript-backend-windows-universal-dotnet-get-started-users.md

[Send push notifications to authenticated users]: mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users.md

[¿Qué son los Centros de notificaciones?]: ../notification-hubs-overview.md

[Uso de un cliente .NET para Servicios móviles de Azure]: mobile-services-windows-dotnet-how-to-use-client-library.md
[MobileServiceClient]: http://go.microsoft.com/fwlink/p/?LinkId=302030
 

<!---HONumber=Nov15_HO4-->