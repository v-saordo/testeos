<properties
	pageTitle="Envío de notificaciones entre plataformas a los usuarios con Centros de notificaciones (ASP.NET)"
	description="Obtenga información acerca de cómo utilizar las plantillas de los Centros de notificaciones para enviar, en una sola solicitud, una notificación independiente de plataforma que tenga como destino todas las plataformas."
	services="notification-hubs"
	documentationCenter=""
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="12/11/2015" 
	ms.author="wesmc"/>

# Envío de notificaciones entre plataformas a los usuarios con Centros de notificaciones


En el tutorial anterior, [Notificación a los usuarios con Centros de notificaciones], aprendió a insertar notificaciones en todos los dispositivos que tiene registrado un usuario autenticado específico. En ese tutorial, se necesitaban varias solicitudes para enviar una notificación a cada plataforma de cliente compatible. Los Centros de notificaciones son compatibles con plantillas, que le permiten especificar cómo un dispositivo específico desea recibir notificaciones. Con esto se simplifica el envío de notificaciones entre plataformas. Este tema muestra cómo aprovecha las plantillas para enviar, en una sola solicitud, una notificación independiente de la plataforma que se dirige a todas las plataformas. Si desea obtener información más detallada sobre las plantillas, consulte [Información general acerca de los Centros de notificaciones de Azure][Templates].

> [AZURE.NOTE] Los Centros de notificaciones permiten que un dispositivo registre varias plantillas con la misma etiqueta. En este caso, un mensaje entrante dirigido a esa etiqueta da lugar a que se entreguen varias notificaciones al dispositivo, una por cada plantilla. Esto le permite mostrar el mismo mensaje en varias notificaciones visuales, como distintivo y como notificación del sistema en una aplicación de la Tienda Windows.

Lleve a cabo los siguientes pasos para enviar notificaciones entre plataformas mediante plantillas:

1. En el Explorador de soluciones en Visual Studio, expanda la carpeta **Controladores** y, a continuación, abra el archivo RegisterController.cs.

2. Encuentre el bloque de código en el método **Post** que crea un nuevo registro y reemplace el contenido del `switch` por el código siguiente:

		switch (deviceUpdate.Platform)
        {
            case "mpns":
                var toastTemplate = "<?xml version="1.0" encoding="utf-8"?>" +
                    "<wp:Notification xmlns:wp="WPNotification">" +
                       "<wp:Toast>" +
                            "<wp:Text1>$(message)</wp:Text1>" +
                       "</wp:Toast> " +
                    "</wp:Notification>";
                registration = new MpnsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
                break;
            case "wns":
                toastTemplate = @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(message)</text></binding></visual></toast>";
                registration = new WindowsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
                break;
            case "apns":
                var alertTemplate = "{"aps":{"alert":"$(message)"}}";
                registration = new AppleTemplateRegistrationDescription(deviceUpdate.Handle, alertTemplate);
                break;
            case "gcm":
                var messageTemplate = "{"data":{"msg":"$(message)"}}";
                registration = new GcmTemplateRegistrationDescription(deviceUpdate.Handle, messageTemplate);
                break;
            default:
                throw new HttpResponseException(HttpStatusCode.BadRequest);
        }

	Este código llama al método específico de la plataforma para crear un registro de plantilla en lugar de un registro nativo. No se deben modificar los registros existentes, dado que los registros de plantilla derivan de los registros nativos.

3. En el controlador **Notifications**, reemplace el método **sendNotification** por el código siguiente:

        public async Task<HttpResponseMessage> Post()
        {
            var user = HttpContext.Current.User.Identity.Name;
            var userTag = "username:" + user;

            var notification = new Dictionary<string, string> { { "message", "Hello, " + user } };
            await Notifications.Instance.Hub.SendTemplateNotificationAsync(notification, userTag);

            return Request.CreateResponse(HttpStatusCode.OK);
        }

	Este código envía una notificación a todas las plataformas al mismo tiempo y sin que sea necesario especificar una carga nativa. Los Centros de notificaciones crean y entregan la carga correcta a cada dispositivo con el valor _tag_ proporcionado, tal como se especifica en las plantillas registradas.

4. Publique de nuevo su proyecto de back-end de WebApi.

5. Ejecute la aplicación cliente otra vez y compruebe que el registro se realice correctamente.

6. (Opcional) Implemente la aplicación cliente en un segundo dispositivo y, a continuación, ejecute la aplicación.

	Observe que aparecerá una notificación en cada dispositivo.

## Pasos siguientes

Ahora que completó este tutorial, obtenga más información acerca de los Centros de notificaciones y las plantillas en estos temas:

+ **[Uso de los Centros de notificaciones para enviar noticias de última hora]** <br/>(C# para Tienda Windows / iOS)Muestra otro escenario para el uso de las plantillas

+  **[Información general acerca de los centros de notificaciones de Azure][Templates]** <br/>Este tema de información general contiene información más detallada sobre las plantillas.


<!-- Anchors. -->

<!-- Images. -->




<!-- URLs. -->
[Push to users ASP.NET]: /manage/services/notification-hubs/notify-users-aspnet
[Push to users Mobile Services]: /manage/services/notification-hubs/notify-users/
[Visual Studio 2012 Express for Windows 8]: http://go.microsoft.com/fwlink/?LinkId=257546

[Uso de los Centros de notificaciones para enviar noticias de última hora]: notification-hubs-windows-store-dotnet-send-breaking-news.md
[Azure Notification Hubs]: http://go.microsoft.com/fwlink/p/?LinkId=314257
[Notificación a los usuarios con Centros de notificaciones]: notification-hubs-aspnet-backend-windows-dotnet-notify-users.md
[Templates]: http://go.microsoft.com/fwlink/p/?LinkId=317339
[Notification Hub How to for Windows Store]: http://msdn.microsoft.com/library/windowsazure/jj927172.aspx

<!---HONumber=AcomDC_0218_2016-->