<properties
	pageTitle="Incorporación de notificaciones push a la aplicación de Windows 8.1 universal | Microsoft Azure"
	description="Obtenga información acerca de cómo enviar notificaciones push a la aplicación Universal Windows 8.1 desde el servicio móvil de back-end de .NET a través de Centros de notificaciones de Azure"
	services="mobile-services,notification-hubs"
	documentationCenter="windows"
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows-store"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="11/11/2015"
	ms.author="glenga"/>

# Incorporación de notificaciones de inserción a la aplicación de Servicios móviles

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-get-started-push](../../includes/mobile-services-selector-get-started-push.md)]

##Información general
Este tema muestra cómo usar Servicios móviles de Azure con un back-end de .NET para enviar notificaciones de inserción a una aplicación universal para Windows. Aprenderá a habilitar las notificaciones de inserción con los Centros de notificaciones de Azure en un proyecto de aplicación universal para Windows. Cuando termine, el servicio móvil enviará una notificación de inserción desde el back-end de .NET a todas las aplicaciones de la Tienda Windows y de la Tienda de Windows Phone, siempre que se inserte un registro en la tabla TodoList. El centro de notificaciones que cree es gratuito con el servicio móvil, puede administrarse independientemente del servicio móvil y pueden utilizarlo otras aplicaciones y servicios.

Para completar este tutorial, necesitará lo siguiente:

* Una [cuenta Microsoft Store activa](http://go.microsoft.com/fwlink/p/?LinkId=280045).
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=391934" target="_blank">Visual Studio Community 2013</a>.

##<a id="register"></a>Registro de la aplicación para notificaciones de inserción

[AZURE.INCLUDE [mobile-services-create-new-push-vs2013](../../includes/mobile-services-create-new-push-vs2013.md)]

&nbsp;&nbsp;6. Vaya a la carpeta de proyecto `\Services\MobileServices\your_service_name`, abra el archivo de código push.register.cs generado e inspeccione el método **UploadChannel** que registra la URL de canal del dispositivo con el Centro de notificaciones.

&nbsp;&nbsp;7. Abra el archivo de código App.xaml.cs compartido y observe que se ha agregado una llamada al nuevo método **UploadChannel** en el controlador de eventos **OnLaunched**. Así se garantiza que se intentará registrar el dispositivo siempre que se inicie la aplicación.

&nbsp;&nbsp;8. Repita los pasos anteriores para agregar las notificaciones de inserción al proyecto de aplicación de la Tienda de Windows Phone y, en el archivo App.xaml.cs compartido, quite la llamada extra a **UploadChannel** y el contenedor condicional `#if...#endif` restante. Ahora los dos proyectos pueden compartir una misma llamada a **UploadChannel**.

> [AZURE.NOTE]Si quiere simplificar el código generado, unifique las definiciones [MobileServiceClient](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx) del contenedor `#if...#endif` en una única definición sin contenedor, que deben usar las dos versiones de la aplicación.

Tras habilitar las notificaciones de inserción en la aplicación, actualice el servicio móvil para enviarlas.

##<a id="update-service"></a>Actualización del servicio para enviar notificaciones push

Los pasos siguientes actualizan la clase TodoItemController existente para enviar una notificación de inserción a todos los dispositivos registrados cuando se inserta un elemento nuevo. Puede implementar código similar en cualquier elemento [ApiController](https://msdn.microsoft.com/library/system.web.http.apicontroller.aspx) o [TableController](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.tables.tablecontroller.aspx) personalizado, o en cualquier otra ubicación de los servicios back-end.

[AZURE.INCLUDE [mobile-services-dotnet-backend-update-server-push](../../includes/mobile-services-dotnet-backend-update-server-push.md)]

##<a id="local-testing"></a> Habilitación de notificaciones de inserción para pruebas locales

[AZURE.INCLUDE [mobile-services-dotnet-backend-configure-local-push-vs2013](../../includes/mobile-services-dotnet-backend-configure-local-push-vs2013.md)]

Los pasos pendientes de esta sección son opcionales. Permiten probar la aplicación con un servicio móvil que se ejecuta en el equipo local. Si quiere probar las notificaciones de inserción con el servicio móvil que se ejecuta en Azure, vaya directamente a la última sección. Esto se debe a que el Asistente para agregar notificaciones de inserción ya ha configurado la aplicación para conectar con el servicio que se ejecuta en Azure.

>[AZURE.NOTE]Nunca use un servicio móvil de producción para probar y desarrollar el trabajo. Para probarlo, publique siempre el proyecto de servicio móvil en un servicio de almacenamiento provisional independiente.

&nbsp;&nbsp;5. Abra el archivo de proyecto App.xaml.cs compartido y busque las líneas de código que crean una instancia nueva de la clase [MobileServiceClient] para acceder al servicio móvil que se ejecuta en Azure.

&nbsp;&nbsp;6. Convierta este código en comentario y agregue el código necesario para crear una clase [MobileServiceClient] nueva con el mismo nombre, pero con la URL del host local en el constructor. Algo similar a lo siguiente:

	// This MobileServiceClient has been configured to communicate with your local
	// test project for debugging purposes.
	public static MobileServiceClient todolistClient = new MobileServiceClient(
		"http://localhost:4584"
	);

&nbsp;&nbsp;Con esta clase [MobileServiceClient], la aplicación conectará con el servicio local en lugar de con la versión que se hospeda en Azure. Para volver y ejecutar la aplicación con el servicio móvil que se hospeda en Azure, cambie las definiciones [MobileServiceClient] por las originales.

##<a id="test"></a> Prueba de las notificaciones push en su aplicación

[AZURE.INCLUDE [mobile-services-dotnet-backend-windows-universal-test-push](../../includes/mobile-services-dotnet-backend-windows-universal-test-push.md)]

## <a name="next-steps"> </a>Pasos siguientes

En este tutorial hemos presentado las nociones para habilitar una aplicación de la Tienda Windows para que envíe notificaciones de inserción con Servicios móviles y Centros de notificaciones. Le recomendamos que después realice el siguiente tutorial, [Envío de notificaciones de inserción a usuarios autenticados], que muestra cómo enviar estas notificaciones con etiquetas desde un servicio móvil a solo un usuario autenticado.

Puede obtener más información acerca de los Servicios móviles y los Centros de notificaciones en los siguientes temas:

* [Incorporación de Servicios móviles a una aplicación existente][Get started with data] <br/>Obtenga más información sobre cómo almacenar y consultar datos con servicios móviles.

* [Adición de autenticación a la aplicación][Get started with authentication] <br/>Obtenga información sobre cómo autenticar a los usuarios de su aplicación con distintos tipos de cuentas con los servicios móviles.

* [¿Qué son los Centros de notificaciones?] <br/>Obtenga más información sobre el funcionamiento de los Centros de notificaciones para entregar notificaciones a sus aplicaciones en todas las principales plataformas de cliente.

* [Depuración de aplicaciones de los Centros de notificaciones](http://go.microsoft.com/fwlink/p/?linkid=386630) </br>Obtenga orientación sobre la solución de problemas y la depuración de soluciones de los Centros de notificaciones.

* [Uso de un cliente .NET para Servicios móviles de Azure] <br/>Obtenga más información sobre cómo usar Servicios móviles desde aplicaciones de C# Windows.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md
[Get started with data]: mobile-services-dotnet-backend-windows-universal-dotnet-get-started-data.md
[Get started with authentication]: mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users.md

[Envío de notificaciones de inserción a usuarios autenticados]: mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users.md

[¿Qué son los Centros de notificaciones?]: ../notification-hubs-overview.md

[Uso de un cliente .NET para Servicios móviles de Azure]: mobile-services-windows-dotnet-how-to-use-client-library.md
[MobileServiceClient]: http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx

<!---HONumber=AcomDC_1203_2015-->