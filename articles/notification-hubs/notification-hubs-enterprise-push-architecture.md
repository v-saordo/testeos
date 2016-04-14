<properties
	pageTitle="Centros de notificaciones: arquitectura de inserción empresarial"
	description="Instrucciones de uso de los Centros de notificaciones de Azure en un entorno de empresa"
	services="notification-hubs"
	documentationCenter=""
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="02/29/2016" 
	ms.author="wesmc"/>

# Instrucciones sobre arquitectura de inserción empresarial

En la actualidad, las empresas están pasando cada vez más hacia la creación de aplicaciones móviles para sus usuarios finales (externos) o para los empleados (internos). Disponen de sistemas back-end como grandes sistemas (mainframes) o algunas aplicaciones de línea de negocio (LoB) que deben estar integradas en la arquitectura de aplicaciones móviles. En esta guía hablaremos acerca de la mejor manera de realizar esta integración y recomendaremos soluciones posibles a escenarios comunes.

Un requisito frecuente es el envío de notificaciones de inserción a los usuarios a través de su aplicación móvil cuando se produce un evento de interés en los sistemas back-end. Por ejemplo, el cliente de un banco que tiene la aplicación del banco en su iPhone desea recibir una notificación cuando se efectúe un cargo por encima de un importe determinado desde su cuenta, o un escenario de intranet en el que un empleado del departamento financiero que tiene una aplicación de aprobación de presupuestos en su Windows Phone desea recibir una notificación cuando obtenga una solicitud de aprobación.

El procesamiento de la cuenta bancaria o de la solicitud de aprobación se realiza probablemente en algún sistema back-end que debe iniciar una inserción al usuario. Podría haber muchos sistemas back-end de este tipo que deben crear todos el mismo tipo de lógica para implementar la inserción cuando un evento desencadena una notificación. Aquí la complejidad reside en la integración de varios back-ends juntos con un único sistema de inserción donde los usuarios finales podrían haberse suscrito a diferentes notificaciones e incluso podría haber varias aplicaciones móviles, por ejemplo, en el caso de aplicaciones móviles de intranet donde una aplicación móvil puede querer recibir notificaciones de varios de estos sistemas back-end. Los sistemas back-end no conocen o no necesitan conocer la semántica/tecnología de inserción, de modo que una solución común aquí ha sido tradicionalmente introducir un componente que sondea los sistemas back-end en busca de eventos de interés y que es responsable de enviar los mensajes de inserción al cliente. Aquí hablaremos de una solución mejor incluso usando el modelo de temas y suscripciones del Bus de servicio de Azure, que reducirá la complejidad y hará que la solución sea escalable.

Esta es la arquitectura general de la solución (generalizada con varias aplicaciones móviles pero igualmente aplicable cuando solo hay una aplicación móvil).

## Arquitectura

![][1]

La pieza clave en este diagrama arquitectónico es el Bus de servicio de Azure que proporciona un modelo de programación de temas y suscripciones (se puede obtener más información al respecto en [Programación Pub/Sub del Bus de servicio]). El receptor, en este caso el back-end móvil (normalmente [Servicio móvil de Azure], que iniciarán una inserción a las aplicaciones móviles) no recibe los mensajes directamente de los sistemas back-end si no que nosotros disponemos de una capa de abstracción intermedia que proporciona el [Bus de servicio de Azure] que permite que el back-end móvil reciba mensajes de uno o varios sistemas back-end. Se debe crear un tema de Bus de servicio para cada uno de los sistemas back-end, por ejemplo, Cuentas, RR.HH., Finanzas, que son básicamente "temas" de interés que iniciarán mensajes para enviarse como notificaciones de inserción. Los sistemas back-end enviarán mensajes a estos temas. Un back-end móvil puede suscribirse a uno o varios de estos temas mediante la creación de una suscripción al Bus de servicio. Esto permitirá al back-end móvil recibir una notificación del sistema back-end correspondiente. El back-end móvil sigue escuchando mensajes en sus suscripciones y, en cuanto llega uno, da la vuelta y lo envía como notificación a su centro de notificaciones. Los centros de notificaciones entregan finalmente el mensaje a la aplicación móvil. Así que, resumiendo, tenemos los siguientes componentes clave:

1. Sistema back-end (sistema LoB o heredado)
	- Crea el tema del Bus de servicio
	- Envía el mensaje
2. Back-end móvil
	- Crea la suscripción al servicio
	- Recibe el mensaje (del sistema back-end)
	- Envía una notificación a los clientes (a través del Centro de notificaciones de Azure)
3. Aplicación móvil
	- Recibe y muestra notificaciones

###Ventajas:

1. La separación entre el receptor (aplicación/servicio móvil a través del Centro de notificaciones) y el emisor (sistemas back-end) permite la integración de sistemas back-end adicionales con cambios mínimos.
2. Esto hace también que en el escenario de varias aplicaciones móviles, sea posible recibir eventos de uno o varios sistemas back-end.  

## Sample:

###Requisitos previos
Para familiarizarse con los conceptos, así como con los pasos comunes de creación y configuración, debe realizar los siguientes tutoriales:

1. [Programación Pub/Sub del Bus de servicio]\: en este tutorial se explican los detalles de trabajar con temas y suscripciones del Bus de servicio, cómo crear un espacio de nombres que contenga temas y suscripciones o cómo enviar y recibir mensajes de ellos.
2. [Tutorial sobre Centros de notificaciones: Windows Universal]\: en este tutorial se explica cómo configurar una aplicación de la Tienda Windows y usar los Centros de notificaciones para registrar y, después, recibir notificaciones.

###Código de ejemplo

El código de ejemplo completo está disponible en [Ejemplos de centro de notificaciones]. Se divide en tres componentes:

1. **EnterprisePushBackendSystem**

	a. Este proyecto usa el paquete de NuGet *WindowsAzure.ServiceBus* y se basa en la [programación Pub/Sub del Bus de servicio].

	b. Se trata de una aplicación de consola C# sencilla para simular un sistema LoB que inicia el mensaje que se entrega a la aplicación móvil.

		static void Main(string[] args)
        {
            string connectionString =
                CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

            // Create the topic where we will send notifications
            CreateTopic(connectionString);

            // Send message
            SendMessage(connectionString);
        }

	c. `CreateTopic` se usa para crear el tema del Bus de servicio al que se enviarán los mensajes.

        public static void CreateTopic(string connectionString)
        {
            // Create the topic if it does not exist already

            var namespaceManager =
                NamespaceManager.CreateFromConnectionString(connectionString);

            if (!namespaceManager.TopicExists(sampleTopic))
            {
                namespaceManager.CreateTopic(sampleTopic);
            }
        }

	d. `SendMessage` se usa para enviar los mensajes a este tema del Bus de servicio. En este ejemplo, simplemente enviaremos un conjunto de mensajes aleatorios al tema de manera periódica. Normalmente, habrá un sistema back-end que enviará los mensajes cuando se produzca un evento.

        public static void SendMessage(string connectionString)
        {
            TopicClient client =
                TopicClient.CreateFromConnectionString(connectionString, sampleTopic);

            // Sends random messages every 10 seconds to the topic
            string[] messages =
            {
                "Employee Id '{0}' has joined.",
                "Employee Id '{0}' has left.",
                "Employee Id '{0}' has switched to a different team."
            };

            while (true)
            {
                Random rnd = new Random();
                string employeeId = rnd.Next(10000, 99999).ToString();
                string notification = String.Format(messages[rnd.Next(0,messages.Length)], employeeId);

                // Send Notification
                BrokeredMessage message = new BrokeredMessage(notification);
                client.Send(message);

                Console.WriteLine("{0} Message sent - '{1}'", DateTime.Now, notification);

                System.Threading.Thread.Sleep(new TimeSpan(0, 0, 10));
            }
        }

2. **ReceiveAndSendNotification**

	a. Este proyecto usa los paquetes de NuGet *WindowsAzure.ServiceBus* y *Microsoft.Web.WebJobs.Publish* y se basa en la [programación Pub/Sub del Bus de servicio].

	b. Se trata de otra consola de aplicación C# que ejecutaremos como un [trabajo web de Azure] dado que se tiene que ejecutar continuamente para escuchar mensajes de los sistemas LoB/back-end. Formará parte de su back-end móvil.

	    static void Main(string[] args)
	    {
	        string connectionString =
	                 CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

	        // Create the subscription which will receive messages
	        CreateSubscription(connectionString);

	        // Receive message
	        ReceiveMessageAndSendNotification(connectionString);
	    }

	c. `CreateSubscription` se usa para crear una suscripción al Bus de servicio para el tema al que el sistema back-end enviará los mensajes. En función del escenario de negocio, este componente creará una o varias suscripciones a los temas correspondientes (por ejemplo, algunas podrían recibir mensajes del sistema de RR.HH., otras del sistema de Finanzas. etc.)

	    static void CreateSubscription(string connectionString)
        {
            // Create the subscription if it does not exist already
            var namespaceManager =
                NamespaceManager.CreateFromConnectionString(connectionString);

            if (!namespaceManager.SubscriptionExists(sampleTopic, sampleSubscription))
            {
                namespaceManager.CreateSubscription(sampleTopic, sampleSubscription);
            }
        }

	d. ReceiveMessageAndSendNotification se usa para leer el mensaje del tema mediante su suscripción y, si la lectura se realiza correctamente, entonces se crea una notificación (en el escenario de ejemplo, una notificación del sistema nativa de Windows) para su envío a la aplicación móvil mediante los Centros de notificaciones de Azure.

		static void ReceiveMessageAndSendNotification(string connectionString)
        {
            // Initialize the Notification Hub
            string hubConnectionString = CloudConfigurationManager.GetSetting
                    ("Microsoft.NotificationHub.ConnectionString");
            hub = NotificationHubClient.CreateClientFromConnectionString
                    (hubConnectionString, "enterprisepushservicehub");

            SubscriptionClient Client =
                SubscriptionClient.CreateFromConnectionString
                        (connectionString, sampleTopic, sampleSubscription);

            Client.Receive();

            // Continuously process messages received from the subscription
            while (true)
            {
                BrokeredMessage message = Client.Receive();
                var toastMessage = @"<toast><visual><binding template=""ToastText01""><text id=""1"">{messagepayload}</text></binding></visual></toast>";

                if (message != null)
                {
                    try
                    {
                        Console.WriteLine(message.MessageId);
                        Console.WriteLine(message.SequenceNumber);
                        string messageBody = message.GetBody<string>();
                        Console.WriteLine("Body: " + messageBody + "\n");

                        toastMessage = toastMessage.Replace("{messagepayload}", messageBody);
                        SendNotificationAsync(toastMessage);

                        // Remove message from subscription
                        message.Complete();
                    }
                    catch (Exception)
                    {
                        // Indicate a problem, unlock message in subscription
                        message.Abandon();
                    }
                }
            }
        }
        static async void SendNotificationAsync(string message)
        {
            await hub.SendWindowsNativeNotificationAsync(message);
        }

	e. Para publicar esto como un **trabajo web**, haga clic con el botón derecho en la solución en Visual Studio y seleccione **Publicar como trabajo web**.

	![][2]

	f. Seleccione su perfil de publicación y cree un nuevo sitio web de Azure, si aún no existe, que hospede este trabajo web. Cuando tenga el sitio web, haga clic en **Publicar**.

	![][3]

	g. Configure el trabajo como "Ejecutar continuamente", de modo que cuando inicie sesión en el [Portal de Azure clásico] vea algo parecido a esto:

	![][4]


3. **EnterprisePushMobileApp**

	a. Se trata de una aplicación de la Tienda Windows que recibirá notificaciones del sistema del trabajo web que se ejecuta como parte de su back-end móvil y las mostrará. Se basa en el [tutorial sobre Centros de notificaciones: Windows Universal].

	b. Asegúrese de que su aplicación está habilitada para recibir notificaciones del sistema.

	c. Asegúrese de que se llama al siguiente código de registro de los Centros de notificaciones al inicio de la aplicación (después de sustituir los valores de *HubName* y *DefaultListenSharedAccessSignature*:

        private async void InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

            var hub = new NotificationHub("[HubName]", "[DefaultListenSharedAccessSignature]");
            var result = await hub.RegisterNativeAsync(channel.Uri);

            // Displays the registration ID so you know it was successful
            if (result.RegistrationId != null)
            {
                var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }

### Ejecución del ejemplo:

1. Asegúrese de que su trabajo web se ejecuta correctamente y está programado como "Ejecutar continuamente".
2. Ejecute **EnterprisePushMobileApp**, que iniciará la aplicación de la Tienda Windows.
3. Ejecute la aplicación de consola **EnterprisePushBackendSystem**, que simulará el back-end LoB e iniciará el envío de mensajes, de modo que verá que aparecen notificaciones del sistema como las siguientes:

	![][5]

4. Los mensajes se enviaron originalmente a temas del Bus de servicio, los cuales son supervisadas por las suscripciones del Bus de servicio en el trabajo web. Cuando se recibe un mensaje, se crea una notificación y se envía a la aplicación móvil. Puede examinar los registros del trabajo web para confirmar el procesamiento en el vínculo Registros en el [Portal de Azure clásico] correspondiente a su trabajo web:

	![][6]

<!-- Images -->
[1]: ./media/notification-hubs-enterprise-push-architecture/architecture.png
[2]: ./media/notification-hubs-enterprise-push-architecture/WebJobsContextMenu.png
[3]: ./media/notification-hubs-enterprise-push-architecture/PublishAsWebJob.png
[4]: ./media/notification-hubs-enterprise-push-architecture/WebJob.png
[5]: ./media/notification-hubs-enterprise-push-architecture/Notifications.png
[6]: ./media/notification-hubs-enterprise-push-architecture/WebJobsLog.png

<!-- Links -->
[Ejemplos de centro de notificaciones]: https://github.com/Azure/azure-notificationhubs-samples
[Servicio móvil de Azure]: http://azure.microsoft.com/documentation/services/mobile-services/
[Bus de servicio de Azure]: http://azure.microsoft.com/documentation/articles/fundamentals-service-bus-hybrid-solutions/
[Programación Pub/Sub del Bus de servicio]: http://azure.microsoft.com/documentation/articles/service-bus-dotnet-how-to-use-topics-subscriptions/
[trabajo web de Azure]: http://azure.microsoft.com/documentation/articles/web-sites-create-web-jobs/
[Tutorial sobre Centros de notificaciones: Windows Universal]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[Portal de Azure clásico]: https://manage.windowsazure.com/

<!---HONumber=AcomDC_0302_2016-->