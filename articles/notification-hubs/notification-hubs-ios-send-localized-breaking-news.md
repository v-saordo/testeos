<properties
	pageTitle="Tutorial de últimas noticias localizadas sobre los Centros de notificaciones para iOS"
	description="Obtenga información acerca de los Centros de notificaciones del Bus de servicio de Azure para enviar notificaciones de noticias de última hora localizadas (iOS)."
	services="notification-hubs"
	documentationCenter="ios"
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="12/16/2015"
	ms.author="wesmc"/>

# Uso de los Centros de notificaciones para enviar noticias de última hora localizadas a dispositivos iOS

> [AZURE.SELECTOR]
- [Windows Store C#](notification-hubs-windows-store-dotnet-send-localized-breaking-news)
- [iOS](notification-hubs-ios-send-localized-breaking-news)


##Información general

En este tema se muestra cómo usar la característica de [plantillas](notification-hubs-templates.md) de los Centros de notificaciones de Azure para difundir notificaciones de noticias de última hora localizadas por lenguaje y dispositivo. En este tutorial comenzará con la aplicación de iOS que se creó en [Uso de los Centros de notificaciones para enviar noticias de última hora]. Una vez que lo haya completado, podrá registrarse en las categorías que le interesan, especificar un idioma para recibir las notificaciones y recibir solo notificaciones de inserción para las categorías seleccionadas en dicho idioma.


Este escenario tiene dos partes:

- La aplicación iOS permite que los dispositivos cliente especifiquen un idioma y que se suscriban a distintas categorías de noticias de última hora;

- el back-end difunde las notificaciones, mediante las características de **etiqueta** y **plantilla** de los Centros de notificaciones de Azure.



##Requisitos previos

Debe haber completado el tutorial [Uso de Centros de notificaciones para enviar noticias de última hora] y debe tener disponible el código, porque este tutorial se basa directamente en ese código.

Visual Studio 2012 o posterior es opcional.



##Conceptos de plantilla

En el tutorial [Uso de Centros de notificaciones para enviar noticias de última hora] creó una aplicación que utilizó **etiquetas** para suscribirse a notificaciones para distintas categorías de noticias. Sin embargo, muchas aplicaciones están dirigidas a varios mercados y requieren localización. Esto significa que el contenido de las notificaciones mismas se debe localizar y entregar al conjunto de dispositivos correcto. En este tema podremos mostrar cómo usar la característica de **plantilla** de los Centros de notificaciones para entregar fácilmente notificaciones de noticias de última hora localizadas.

Nota: una forma de enviar notificaciones localizadas es crear varias versiones de cada etiqueta. Por ejemplo, para admitir inglés, francés y chino mandarín, necesitaríamos tres etiquetas distintas para noticias mundiales: "world\_en", "world\_fr" y "world\_ch". Luego tendríamos que enviar una versión localizada de las noticias mundiales a cada una de estas etiquetas. En este tema usamos plantillas para evitar la proliferación de etiquetas y el requisito de enviar varios mensajes.

A un alto nivel, las plantillas son una forma de especificar la manera en que un dispositivo específico debe recibir una notificación. La plantilla especifica el formato de carga exacto haciendo referencia a las propiedades que forman parte del mensaje enviado por el back-end de la aplicación. En nuestro caso, enviaremos un mensaje independiente de la configuración regional que contengan todos los idiomas compatibles:

	{
		"News_English": "...",
		"News_French": "...",
		"News_Mandarin": "..."
	}

Esto garantizará que los dispositivos se registren con una plantilla que hace referencia a la propiedad correcta. Por ejemplo, una aplicación iOS que desea registrarse para noticias en francés se registrará de la siguiente manera:

	{
		aps:{
			alert: "$(News_French)"
		}
	}

Las plantillas son una característica muy eficaz de la que puede obtener más información en nuestro artículo [Plantillas](notification-hubs-templates.md).

##Interfaz de usuario de la aplicación

Ahora modificaremos la aplicación de noticias de última hora que creó en el tema [Uso de los Centros de notificaciones para enviar noticias de última hora] para enviar noticias de última hora localizadas con plantillas.


En MainStoryboard\_iPhone.storyboard, agregue un control segmentado con los tres idiomas compatibles: inglés, francés y chino mandarín.

![][13]

A continuación, asegúrese de agregar un IBOutlet en ViewController.h tal como se muestra a continuación:

![][14]

##Creación de la aplicación iOS


1. En Notification.h, agregue el método *retrieveLocale*, modifique el almacén y suscríbase a métodos tal como se muestra a continuación:

		- (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet*) categories completion: (void (^)(NSError* error))completion;

		- (void) subscribeWithLocale:(int) locale categories:(NSSet*) categories completion:(void (^)(NSError *))completion;

		- (NSSet*) retrieveCategories;

		- (int) retrieveLocale;

	En Notification.m, modifique el método *storeCategoriesAndSubscribe* agregando el parámetro de configuración regional y almacenándolo en los valores predeterminados del usuario:

		- (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion {
		    NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
		    [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];
		    [defaults setInteger:locale forKey:@"BreakingNewsLocale"];
		    [defaults synchronize];

		    [self subscribeWithLocale: locale categories:categories completion:completion];
		}

	Luego modifique el método *subscribe* para incluir la configuración regional:

		- (void) subscribeWithLocale: (int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion{
		    SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:@"<connection string>" notificationHubPath:@"<hub name>"];

		    NSString* localeString;
		    switch (locale) {
		        case 0:
		            localeString = @"English";
		            break;
		        case 1:
		            localeString = @"French";
		            break;
		        case 2:
		            localeString = @"Mandarin";
		            break;
		    }

		    NSString* template = [NSString stringWithFormat:@"{"aps":{"alert":"$(News_%@)"},"inAppMessage":"$(News_%@)"}", localeString, localeString];

		    [hub registerTemplateWithDeviceToken:self.deviceToken name:@"localizednewsTemplate" jsonBodyTemplate:template expiryTemplate:@"0" tags:categories completion:completion];
		}

	Observe cómo usamos ahora el método *registerTemplateWithDeviceToken*, en lugar de *registerNativeWithDeviceToken*. Cuando nos registramos para obtener una plantilla, tenemos que proporcionar la plantilla json y también ponerle un nombre (dado que nuestra aplicación puede querer registrar distintas plantillas). Asegúrese de registrar sus categorías como etiquetas, debido a que queremos asegurarnos de recibir las notificaciones para dichas noticias.

	Agregue un método para recuperar la configuración regional a partir de los ajustes predeterminados del usuario:

		- (int) retrieveLocale {
		    NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];

		    int locale = [defaults integerForKey:@"BreakingNewsLocale"];

		    return locale < 0?0:locale;
		}

2. Ahora que hemos modificado nuestra clase de notificaciones, debemos asegurarnos de que nuestro ViewController utilice el nuevo UISegmentControl. Agregue la siguiente línea en el método *viewDidLoad* para asegurarse de mostrar la configuración regional actualmente seleccionada:

		self.Locale.selectedSegmentIndex = [notifications retrieveLocale];

	A continuación, en el método *subscribe*, cambie la llamada a *storeCategoriesAndSubscribe* a lo siguiente:

		[notifications storeCategoriesAndSubscribeWithLocale: self.Locale.selectedSegmentIndex categories:[NSSet setWithArray:categories] completion: ^(NSError* error) {
	        if (!error) {
	            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
	                                  @"Subscribed!" delegate:nil cancelButtonTitle:
	                                  @"OK" otherButtonTitles:nil, nil];
	            [alert show];
	        } else {
	            NSLog(@"Error subscribing: %@", error);
	        }
	    }];

3. Finalmente, debe actualizar el método *didRegisterForRemoteNotificationsWithDeviceToken* en AppDelegate.m, para que pueda actualizar correctamente el registro cuando se inicie la aplicación. Cambie la llamada al método *subscribe* de notificaciones con lo siguiente:

		NSSet* categories = [self.notifications retrieveCategories];
	    int locale = [self.notifications retrieveLocale];
	    [self.notifications subscribeWithLocale: locale categories:categories completion:^(NSError* error) {
	        if (error != nil) {
	            NSLog(@"Error registering for notifications: %@", error);
	        }
	    }];

##(Opcional) Envíe notificaciones de plantillas localizadas desde la aplicación de consola .NET.

[AZURE.INCLUDE [notification-hubs-localized-back-end](../../includes/notification-hubs-localized-back-end.md)]



##(Opcional) Enviar notificaciones de plantillas localizadas desde el dispositivo

Si no tiene acceso a Visual Studio, o simplemente quiere probar el envío de las notificaciones de plantilla localizadas directamente desde la aplicación del dispositivo. Simplemente puede agregar los parámetros de plantilla localizada al método `SendNotificationRESTAPI` que ha definido en el tutorial anterior.

		- (void)SendNotificationRESTAPI:(NSString*)categoryTag
		{
		    NSURLSession* session = [NSURLSession sessionWithConfiguration:[NSURLSessionConfiguration
									 defaultSessionConfiguration] delegate:nil delegateQueue:nil];

		    NSString *json;

		    // Construct the messages REST endpoint
		    NSURL* url = [NSURL URLWithString:[NSString stringWithFormat:@"%@%@/messages/%@", HubEndpoint,
		                                       HUBNAME, API_VERSION]];

		    // Generated the token to be used in the authorization header.
		    NSString* authorizationToken = [self generateSasToken:[url absoluteString]];

		    //Create the request to add the template notification message to the hub
		    NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];
		    [request setHTTPMethod:@"POST"];

		    // Add the category as a tag
		    [request setValue:categoryTag forHTTPHeaderField:@"ServiceBusNotification-Tags"];

			// Template notification
	        json = [NSString stringWithFormat:@"{"messageParam":"Breaking %@ News : %@","
					"News_English":"Breaking %@ News in English : %@","
					"News_French":"Breaking %@ News in French : %@","
					"News_Mandarin":"Breaking %@ News in Mandarin : %@","
	                categoryTag, self.notificationMessage.text,
	                categoryTag, self.notificationMessage.text,  // insert English localized news here
	                categoryTag, self.notificationMessage.text,  // insert French localized news here
	                categoryTag, self.notificationMessage.text]; // insert Mandarin localized news here

	        // Signify template notification format
	        [request setValue:@"template" forHTTPHeaderField:@"ServiceBusNotification-Format"];

			// JSON Content-Type
			[request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];

		    //Authenticate the notification message POST request with the SaS token
		    [request setValue:authorizationToken forHTTPHeaderField:@"Authorization"];

		    //Add the notification message body
		    [request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];

		    // Send the REST request
		    NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request
		               completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
	           {
	           NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
	               if (error || httpResponse.statusCode != 200)
	               {
	                   NSLog(@"\nError status: %d\nError: %@", httpResponse.statusCode, error);
	               }
	               if (data != NULL)
	               {
	                   //xmlParser = [[NSXMLParser alloc] initWithData:data];
	                   //[xmlParser setDelegate:self];
	                   //[xmlParser parse];
	               }
	           }];

		    [dataTask resume];
		}




## Pasos siguientes

Para obtener más información sobre el uso de plantillas, consulte:

- [Notificación a los usuarios con los Centros de notificaciones de Azure: ASP.NET]
- [Notificación a los usuarios con los Centros de notificaciones de Azure: Servicios móviles]






<!-- Images. -->

[13]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized1.png
[14]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized2.png






<!-- URLs. -->
[How To: Service Bus Notification Hubs (iOS Apps)]: http://msdn.microsoft.com/library/jj927168.aspx
[Uso de Centros de notificaciones para enviar noticias de última hora]: /manage/services/notification-hubs/breaking-news-ios
[Uso de los Centros de notificaciones para enviar noticias de última hora]: /manage/services/notification-hubs/breaking-news-ios
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notificación a los usuarios con los Centros de notificaciones de Azure: ASP.NET]: /manage/services/notification-hubs/notify-users-aspnet
[Notificación a los usuarios con los Centros de notificaciones de Azure: Servicios móviles]: /manage/services/notification-hubs/notify-users
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-ios
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-ios
[Push notifications to app users]: /develop/mobile/tutorials/push-notifications-to-users-ios
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[JavaScript and HTML]: ../get-started-with-push-js.md

[Windows Developer Preview registration steps for Mobile Services]: ../mobile-services-windows-developer-preview-registration.md
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for iOS]: http://msdn.microsoft.com/library/jj927168.aspx

<!---HONumber=AcomDC_1217_2015-->