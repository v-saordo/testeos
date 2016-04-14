<properties
	pageTitle="Uso de varios clientes con un solo back-end de servicio móvil | Servicios móviles de Azure"
	description="Obtenga información sobre cómo usar un solo back-end de servicio móvil en varias aplicaciones cliente destinadas a distintas plataformas móviles."
	services="mobile-services"
	documentationCenter=""
	authors="ggailey777"
	manager="dwrede"
	editor="mollybos"/>
<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="12/07/2015"
	ms.author="glenga"/>

# Compatibilidad de plataformas de varios dispositivos desde un único servicio móvil

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


Uno de los principales beneficios de usar Servicios móviles de Azure en el desarrollo de aplicaciones móviles es la capacidad de usar un servicio back-end único que admita su aplicación en varias plataformas de cliente. La solución Servicios móviles proporciona bibliotecas de cliente nativas para todas las plataformas de dispositivos importantes, lo que facilita el desarrollo de aplicaciones mediante un solo servicio back-end y el uso de herramientas de desarrollador de varias plataformas. En este tema se tratan las siguientes consideraciones para el funcionamiento de su aplicación en varias plataformas de cliente mientras se usa un único back-end de servicios móviles:

##<a id="push"></a>Notificaciones de inserción multiplataforma

La solución Servicios móviles usa Centros de notificaciones de Azure para enviar notificaciones de inserción a aplicaciones cliente en todas las plataformas de dispositivos importantes. Centros de notificaciones proporciona una infraestructura coherente y unificada para crear y administrar registros de dispositivos y para enviar notificaciones de inserción multiplataforma. Centros de notificaciones admite el envío de notificaciones de inserción al usar los siguientes servicios de notificación específicos de la plataforma:

+ Servicio de notificaciones de inserción de Apple (APNS) para aplicaciones iOS
+ Servicio Google Cloud Messaging (GCM) para aplicaciones Android
+ Windows Notification Service (WNS) para la Tienda Windows, la Tienda de Windows Phone 8.1 y las aplicaciones universales de Windows
+ Servicio de notificaciones de inserción de Microsoft (MPNS) para las aplicaciones Silverlight de Windows Phone

Para obtener más información, consulte [Centros de notificaciones de Azure].

Los registros de cliente se crean al usar la función de registro de la biblioteca de clientes de Servicios móviles específica de la plataforma o al usar las API de REST de Servicios móviles. Centros de notificaciones admite dos tipos de registros de dispositivo:

+ **Registro nativo**<br/>Los registros nativos se adaptan al servicio de inserción específico de la plataforma. Al enviar notificaciones a dispositivos registrados mediante registros nativos, deberá llamar a API específicas de la plataforma en el servicio móvil. Para enviar una notificación a dispositivos en varias plataformas, se requieren varias llamadas específicas de la plataforma.

+ **Registro de plantilla**<br/>Centros de notificaciones también admite registros de plantilla específicos de la plataforma. Al usar registros de plantillas puede emplear una llamada de la API para enviar una notificación a la aplicación que se ejecuta en cualquier plataforma registrada. Para obtener más información, vea [Envío de notificaciones entre plataformas a los usuarios].

Las tablas en las siguientes secciones se vinculan a los tutoriales de cliente específicos en los que se muestra cómo implementar notificaciones de inserción desde servicios móviles back-end de .NET y JavaScript.

###Back-end de .NET

En un servicio móvil back-end de .NET, las notificaciones se envían al llamar al método [SendAsync] en el objeto [PushClient](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.notifications.pushclient.aspx) que se obtiene de la propiedad [ApiServices.Push](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.apiservices.push.aspx). La notificación de inserción enviada (nativa o de plantilla) depende del objeto derivado de [IPushMessageque](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.notifications.ipushmessage.aspx) se pasa al método [SendAsync], tal como se muestra en la tabla siguiente:

|Plataforma |[APNS](mobile-services-dotnet-backend-ios-get-started-push.md)|[GCM](mobile-services-dotnet-backend-android-get-started-push.md) |[WNS](mobile-services-dotnet-backend-windows-store-dotnet-get-started-push.md) | MPNS
|-----|-----|----|----|-----|
|Nativa|[ApplePushMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.applepushmessage.aspx) |[GooglePushMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.googlepushmessage.aspx) |[WindowsPushMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.windowspushmessage.aspx) | [MpnsPushMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.mpnspushmessage.aspx) |

El siguiente código envía una notificación desde un servicio back-en de .NET a todos los registros de dispositivo iOS y de la Tienda Windows:

	// Define a push notification for APNS.
	ApplePushMessage apnsMessage = new ApplePushMessage(item.Text, TimeSpan.FromHours(1));

	// Define a push notification for WNS.
	WindowsPushMessage wnsMessage = new WindowsPushMessage();
    wnsMessage.XmlPayload = @"<?xml version=""1.0"" encoding=""utf-8""?>" +
                         @"<toast><visual><binding template=""ToastText01"">" +
                         @"<text id=""1"">" + item.Text + @"</text>" +
                         @"</binding></visual></toast>";

	// Send push notifications to all registered iOS and Windows Store devices.
    await Services.Push.SendAsync(apnsMessage);
	await Services.Push.SendAsync(wnsMessage);

Para obtener ejemplos de cómo enviar notificaciones de inserción a otras plataformas de cliente nativo, haga clic en los vínculos de plataforma del encabezado de la tabla anterior.

Cuando usa registros de cliente de plantilla en lugar de registros de cliente nativo, puede enviar la misma notificación con una única llamada a [SendAsync], proporcionando un objeto [TemplatePushMessage], tal como se indica a continuación:

	// Create a new template message and add the 'message' parameter.
	var templatePayload = new TemplatePushMessage();
    templatePayload.Add("message", item.Text);

	// Send a push notification to all template registrations.
    await Services.Push.SendAsync(templatePayload);

###Back-end de JavaScript

En un servicio móvil back-end de JavaScript, las notificaciones se envían mediante una llamada al método **send** en el objeto específico de la plataforma que se obtiene del [objeto de inserción] global, como se muestra en la siguiente tabla:

|Plataforma |[APNS](mobile-services-javascript-backend-ios-get-started-push.md)|[GCM](mobile-services-javascript-backend-android-get-started-push.md) |[WNS](mobile-services-javascript-backend-windows-store-dotnet-get-started-push.md) |[MPNS](mobile-services-javascript-backend-windows-phone-get-started-push.md)|
|-----|-----|----|----|-----|
|Nativa|[objeto apns](http://msdn.microsoft.com/library/azure/jj839711.aspx) |[objeto gcm](http://msdn.microsoft.com/library/azure/dn126137.aspx) |[objeto wns](http://msdn.microsoft.com/library/azure/jj860484.aspx) | [objeto mpns](http://msdn.microsoft.com/library/azure/jj871025.aspx) |

El código siguiente envía notificaciones de inserción a todos los registros de Android y Windows Phone:

	// Define a push notification for GCM.
	var gcmPayload =
    '{"data":{"message" : item.text }}';

	// Define the payload for a Windows Phone toast notification.
	var mpnsPayload = '<?xml version="1.0" encoding="utf-8"?>' +
    '<wp:Notification xmlns:wp="WPNotification"><wp:Toast>' +
    '<wp:Text1>New Item</wp:Text1><wp:Text2>' + item.text +
    '</wp:Text2></wp:Toast></wp:Notification>';

	// Send push notifications to all registered Android and Windows Phone 8.0 devices.
	push.mpns.send(null, mpnsPayload, 'toast', 22, {
            success: function(pushResponse) {
                // Push succeeds.
                },
                error: function (pushResponse) {
                    // Push fails.
                    }
                });
    push.gcm.send(null, gcmPayload, {
            success: function(pushResponse) {
                // Push succeeds.
                },
                error: function (pushResponse) {
                    // Push fails.
                    }
                });

Para obtener ejemplos de cómo enviar notificaciones de inserción a otras plataformas de cliente nativo, haga clic en los vínculos de plataforma del encabezado de la tabla anterior.

Cuando usa registros de cliente de plantilla en lugar de registros de cliente nativo, puede enviar la misma notificación con una única llamada a la función **send** del [objeto de inserción] global, proporcionando una carga de mensaje de plantilla, como se indica a continuación:

	// Create a new template message with the 'message' parameter.
	var templatePayload = { "message": item.text };

	// Send a push notification to all template registrations.
    push.send(null, templatePayload, {
            success: function(pushResponse) {
                // Push succeeds.
                },
                error: function (pushResponse) {
                    // Push fails.
                    }
                });

##<a id="xplat-app-dev"></a>Desarrollo de aplicaciones multiplataforma
El desarrollo de aplicaciones de dispositivo móvil nativo para todas las principales plataformas de dispositivos móviles requiere conocimientos de al menos los lenguajes de programación Objective-C, Java y C# o JavaScript. Dado el gasto que representa del desarrollo entre todas estas plataformas diversas, algunos desarrolladores optan por una experiencia completa basada en exploradores web para sus aplicaciones. Sin embargo, estas experiencias web no pueden acceder a la mayoría de los recursos nativos que proporcionan la experiencia enriquecida que esperan los usuarios en sus dispositivos móviles.

Hay herramientas multiplataforma disponibles que ofrecen una experiencia nativa más completa en un dispositivo móvil, a la vez que siguen compartiendo una única base de código, normalmente JavaScript. La solución Servicios móviles facilita la creación y administración de servicios back-end para las plataformas de desarrollo de aplicaciones multiplataforma al proporcionar tutoriales de inicio rápido para las siguientes plataformas de desarrollo:

+ [**Appcelerator**](http://go.microsoft.com/fwlink/p/?LinkId=509987)<br/>Appcelerator le permite usar JavaScript para desarrollar una única aplicación que se compila para ejecutarse como nativa en todas las plataformas de dispositivos móviles. Esto proporciona una experiencia de usuario enriquecida en la interfaz de usuario, acceso a todos los recursos de dispositivo nativo y rendimiento de aplicación nativa. Para obtener más información, vea el [tutorial de Appcelerator][Appcelerator].

+ [**PhoneGap**](https://go.microsoft.com/fwLink/p/?LinkID=390707)**/**[**Cordova**](http://cordova.apache.org/)<br/>PhoneGap (una distribución del proyecto Apache Cordova) es un marco gratuito de código abierto que le permite usar API web estandarizadas, HTML y JavaScript para desarrollar una única aplicación que se ejecute en dispositivos Android, iOS y Windows. PhoneGap ofrece una interfaz de usuario de vista web, con una experiencia de usuario mejorada mediante el acceso a los recursos nativos del dispositivo, tal como notificaciones de inserción, acelerómetro, cámara, almacenamiento, geoubicación y el explorador en aplicación. Para obtener más información, vea el [tutorial de inicio rápido de PhoneGap][PhoneGap].

	Ahora, Visual Studio también le permite crear aplicaciones Cordova multiplataforma mediante la extensión de aplicación híbrida multidispositivo para Visual Studio, un software disponible en versión preliminar. Para obtener más información, vea [Introducción a las aplicaciones híbridas multidispositivo mediante HTML y JavaScript](http://msdn.microsoft.com/library/dn771545.aspx).

+ [**Sencha Touch**](http://go.microsoft.com/fwlink/p/?LinkId=509988)<br/>Sencha Touch proporciona un conjunto de controles, optimizados para pantallas táctiles, que ofrecen una experiencia tipo nativa en una variedad de dispositivos móviles desde una única base de código HTML y JavaScript. Sencha Touch se puede usar con las bibliotecas de PhoneGap o Cordova para proporcionar a los usuarios acceso a los recursos de dispositivo nativos. Para obtener más información, vea el [tutorial de inicio rápido de Sencha Touch][Sencha].

+ [**Xamarin**](https://go.microsoft.com/fwLink/p/?LinkID=330242)<br/>Xamarin le permite crear aplicaciones completamente nativas para dispositivos iOS y Android, con una interfaz de usuario completamente nativa y acceso a todos los recursos de dispositivo. Las aplicaciones Xamarin se codifican en C# en lugar de Objective-C y Java. De este modo, los desarrolladores de .NET pueden publicar aplicaciones en iOS y Android, y compartir código desde los proyectos de Windows. Xamarin ofrece una experiencia de usuario completamente nativa en dispositivos iOS y Android a partir del código C#. Esto permite reutilizar parte del código de Servicios móviles desde las aplicaciones de Windows en dispositivos iOS y Android. Para obtener más información, consulte [Desarrollo de Xamarin](#xamarin) a continuación.


<!-- URLs -->
[Centros de notificaciones de Azure]: /develop/net/how-to-guides/service-bus-notification-hubs/
[SSO Windows Store]: /develop/mobile/tutorials/single-sign-on-windows-8-dotnet/
[SSO Windows Phone]: /develop/mobile/tutorials/single-sign-on-wp8/
[Tutorials and resources]: /develop/mobile/resources/
[Get started with Notification Hubs]: /manage/services/notification-hubs/getting-started-windows-dotnet/
[Envío de notificaciones entre plataformas a los usuarios]: /manage/services/notification-hubs/notify-users-xplat-mobile-services/
[Get started with push Windows dotnet]: /develop/mobile/tutorials/get-started-with-push-dotnet-vs2012/
[Get started with push Windows js]: /develop/mobile/tutorials/get-started-with-push-js-vs2012/
[Get started with push Windows Phone]: /develop/mobile/tutorials/get-started-with-push-wp8/
[Get started with push iOS]: /develop/mobile/tutorials/get-started-with-push-ios/
[Get started with push Android]: /develop/mobile/tutorials/get-started-with-push-android/
[Dynamic schema]: http://msdn.microsoft.com/library/windowsazure/jj193175.aspx
[How to use a .NET client with Mobile Services]: documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/
[objeto de inserción]: http://msdn.microsoft.com/library/windowsazure/jj554217.aspx
[TemplatePushMessage]: http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.templatepushmessage.aspx
[PhoneGap]: mobile-services-javascript-backend-phonegap-get-started.md
[Sencha]: partner-sencha-mobile-services-get-started.md
[Appcelerator]: ../partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started.md
[SendAsync]: http://msdn.microsoft.com/library/microsoft.windowsazure.mobile.service.notifications.pushclient.sendasync.aspx
[What's next for Windows Phone 8 developers]: http://msdn.microsoft.com/library/windows/apps/dn655121(v=vs.105).aspx
[Building universal Windows apps for all Windows devices]: http://go.microsoft.com/fwlink/p/?LinkId=509905
[Universal Windows app project for Azure Mobile Services using MVVM]: http://code.msdn.microsoft.com/Universal-Windows-app-for-db3564de

<!---HONumber=AcomDC_1210_2015-->