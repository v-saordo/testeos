<properties
	pageTitle="Los Centros de notificaciones de Azure notifican a los usuarios para iOS con back-end de .NET"
	description="Aprenda a enviar notificaciones push a los usuarios en Azure. Ejemplos de código escritos en Objective-C con la API de .NET."
	documentationCenter="ios"
	authors="wesmc7777"
	manager="dwrede"
	editor=""
	services="notification-hubs"/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="12/16/2015"
	ms.author="wesmc"/>

#Los Centros de notificaciones de Azure notifican a los usuarios para iOS con back-end de .NET

[AZURE.INCLUDE [notification-hubs-selector-aspnet-backend-notify-users](../../includes/notification-hubs-selector-aspnet-backend-notify-users.md)]

##Información general

La compatibilidad con las notificaciones de inserción en Azure le permite tener acceso a una infraestructura multiplataforma y de escalamiento horizontal fácil de usar, que simplifica considerablemente la implementación de notificaciones de inserción tanto en aplicaciones de consumidor, como en aplicaciones empresariales para plataformas móviles. Este tutorial muestra cómo puede utilizar los Centros de notificaciones de Azure para enviar notificaciones de inserción a un usuario de aplicaciones determinado en un dispositivo concreto. Un back-end de ASP.NET WebAPI se usa para autenticar clientes y generar notificaciones, tal como se muestra en el tema de referencia [Registro desde el back-end de la aplicación](notification-hubs-registration-management.md#registration-management-from-a-backend).

> [AZURE.NOTE]Este tutorial asume que ha creado y configurado el centro de notificaciones tal como se describe en [Introducción a los Centros de notificaciones (iOS)](notification-hubs-ios-get-started.md). Este tutorial también es el requisito previo para el tutorial [Inserción segura (iOS)](notification-hubs-aspnet-backend-ios-secure-push.md). Si usa Servicios móviles como su servicio back-end, consulte la [versión de Servicios móviles](../mobile-services-javascript-backend-ios-push-notifications-app-users.md) de este tutorial.



[AZURE.INCLUDE [notification-hubs-aspnet-backend-notifyusers](../../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## Modificación de la aplicación iOS

1. Abra la aplicación de vista de una página que creó en el tutorial [Introducción a los Centros de notificaciones (iOS)](notification-hubs-ios-get-started.md).

	> [AZURE.NOTE]En esta sección asumimos que configuró el proyecto con un nombre de organización vacío. Si no es así, debe anteponer el nombre de la organización a todos los nombres de clase.

2. En Main.storyboard, agregue los componentes mostrados en la captura de pantalla siguiente desde la biblioteca de objetos.

    ![][1]

	+ **Nombre de usuario**: Campo de texto de la interfaz de usuario con texto de marcador de posición, *Escriba el nombre de usuario*, inmediatamente debajo de la etiqueta de resultados de envío y limitado a los márgenes izquierdo y derecho y por debajo de la etiqueta de envío de resultados.
	+ **Contraseña**: Campo de texto de la interfaz de usuario con texto de marcador de posición, *Escriba la contraseña*, inmediatamente debajo del campo de texto de no nombre de usuario y limitado a los márgenes izquierdo y derecho y por debajo del campo de texto del nombre de usuario. Active la opción **Entrada de texto seguro** en el Inspector de atributos, en *Devolver clave*.
	+ **Iniciar sesión**: Botón de la interfaz de usuario inmediatamente debajo del campo de texto de contraseña y la opción **Habilitado** del Inspector de atributos, debajo de *Control-Content*
	+ **WNS**: etiqueta y modificador para habilitar el envío de la notificación del Servicio de notificaciones de Windows si se ha instalado en el concentrador. Consulte el tutorial [introductorio de Windows](notification-hubs-windows-store-dotnet-get-started.md).
	+ **GCM**: etiqueta y modificador para habilitar el envío de la notificación a Google Cloud Messaging si se ha instalado en el concentrador. Consulte el tutorial [introductorio de Android](notification-hubs-android-get-started.md).
	+ **APNS**: etiqueta y modificador para habilitar el envío de la notificación al Servicio de notificaciones de la plataforma Apple.
	+ **Nombre de usuario destinatario**: campo de texto de la interfaz de usuario con texto de marcador de posición, *Etiqueta de nombre de usuario destinatario*, inmediatamente debajo de la etiqueta GCM y limitado a los márgenes izquierdo y derecho y por debajo de la etiqueta GCM.


	Algunos componentes se agregaron al tutorial [Introducción a Centros de notificaciones (iOS)](notification-hubs-ios-get-started.md).

3. Presione **Ctrl** y arrastre desde los componentes de la vista a ViewController.h y agregue esos nuevos medios.

	    @property (weak, nonatomic) IBOutlet UITextField *UsernameField;
		@property (weak, nonatomic) IBOutlet UITextField *PasswordField;
		@property (weak, nonatomic) IBOutlet UITextField *RecipientField;
		@property (weak, nonatomic) IBOutlet UITextField *NotificationField;

		// Used to enable the buttons on the UI
		@property (weak, nonatomic) IBOutlet UIButton *LogInButton;
		@property (weak, nonatomic) IBOutlet UIButton *SendNotificationButton;

		// Used to enabled sending notifications across platforms
		@property (weak, nonatomic) IBOutlet UISwitch *WNSSwitch;
		@property (weak, nonatomic) IBOutlet UISwitch *GCMSwitch;
		@property (weak, nonatomic) IBOutlet UISwitch *APNSSwitch;

		- (IBAction)LogInAction:(id)sender;

4. En ViewController.h, agregue el `#define` siguiente justo debajo de las instrucciones de importación. Sustituya el marcador de posición *<Escriba su extremo de back-end>* por la URL de destino utilizada para implementar el back-end de la aplicación en la sección anterior. Por ejemplo, **http://you_backend.azurewebsites.net*.

		#define BACKEND_ENDPOINT @"<Enter Your Backend Endpoint>"

4. En el proyecto, cree una nueva **Cocoa Touch Class** denominada **RegisterClient** para interactuar con el back-end de ASP.NET que ha creado. Cree la clase que hereda de `NSObject`. Después agregue el siguiente código en RegisterClient.h.

		@interface RegisterClient : NSObject

		@property (strong, nonatomic) NSString* authenticationHeader;

		-(void) registerWithDeviceToken:(NSData*)token tags:(NSSet*)tags
			andCompletion:(void(^)(NSError*))completion;

		-(instancetype) initWithEndpoint:(NSString*)Endpoint;

		@end

5. En la actualización RegisterClient.m la sección `@interface`:

		@interface RegisterClient ()

		@property (strong, nonatomic) NSURLSession* session;
		@property (strong, nonatomic) NSURLSession* endpoint;

		-(void) tryToRegisterWithDeviceToken:(NSData*)token tags:(NSSet*)tags retry:(BOOL)retry
					andCompletion:(void(^)(NSError*))completion;
		-(void) retrieveOrRequestRegistrationIdWithDeviceToken:(NSString*)token
					completion:(void(^)(NSString*, NSError*))completion;
		-(void) upsertRegistrationWithRegistrationId:(NSString*)registrationId deviceToken:(NSString*)token
					tags:(NSSet*)tags andCompletion:(void(^)(NSURLResponse*, NSError*))completion;

		@end

6. Reemplace la sección `@implementation` en RegisterClient.m con el código siguiente.


		@implementation RegisterClient

		// Globals used by RegisterClient
		NSString *const RegistrationIdLocalStorageKey = @"RegistrationId";

		-(instancetype) initWithEndpoint:(NSString*)Endpoint
		{
		    self = [super init];
		    if (self) {
		        NSURLSessionConfiguration* config = [NSURLSessionConfiguration defaultSessionConfiguration];
		        _session = [NSURLSession sessionWithConfiguration:config delegate:nil delegateQueue:nil];
				_endpoint = Endpoint;
		    }
		    return self;
		}

		-(void) registerWithDeviceToken:(NSData*)token tags:(NSSet*)tags
					andCompletion:(void(^)(NSError*))completion
		{
		    [self tryToRegisterWithDeviceToken:token tags:tags retry:YES andCompletion:completion];
		}

		-(void) tryToRegisterWithDeviceToken:(NSData*)token tags:(NSSet*)tags retry:(BOOL)retry
					andCompletion:(void(^)(NSError*))completion
		{
		    NSSet* tagsSet = tags?tags:[[NSSet alloc] init];

		    NSString *deviceTokenString = [[token description]
				stringByTrimmingCharactersInSet:[NSCharacterSet characterSetWithCharactersInString:@"<>"]];
		    deviceTokenString = [[deviceTokenString stringByReplacingOccurrencesOfString:@" " withString:@""]
									uppercaseString];

		    [self retrieveOrRequestRegistrationIdWithDeviceToken: deviceTokenString
				completion:^(NSString* registrationId, NSError *error) {
		        NSLog(@"regId: %@", registrationId);
		        if (error) {
		            completion(error);
		            return;
		        }

		        [self upsertRegistrationWithRegistrationId:registrationId deviceToken:deviceTokenString
					tags:tagsSet andCompletion:^(NSURLResponse * response, NSError *error) {
		            if (error) {
		                completion(error);
		                return;
		            }

		            NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*)response;
		            if (httpResponse.statusCode == 200) {
		                completion(nil);
		            } else if (httpResponse.statusCode == 410 && retry) {
		                [self tryToRegisterWithDeviceToken:token tags:tags retry:NO andCompletion:completion];
		            } else {
		                NSLog(@"Registration error with response status: %ld", (long)httpResponse.statusCode);

		                completion([NSError errorWithDomain:@"Registration" code:httpResponse.statusCode
									userInfo:nil]);
		            }

		        }];
		    }];
		}

		-(void) upsertRegistrationWithRegistrationId:(NSString*)registrationId deviceToken:(NSData*)token
					tags:(NSSet*)tags andCompletion:(void(^)(NSURLResponse*, NSError*))completion
		{
		    NSDictionary* deviceRegistration = @{@"Platform" : @"apns", @"Handle": token,
													@"Tags": [tags allObjects]};
		    NSData* jsonData = [NSJSONSerialization dataWithJSONObject:deviceRegistration
								options:NSJSONWritingPrettyPrinted error:nil];

		    NSLog(@"JSON registration: %@", [[NSString alloc] initWithData:jsonData
												encoding:NSUTF8StringEncoding]);

		    NSString* endpoint = [NSString stringWithFormat:@"%@/api/register/%@", _endpoint,
									registrationId];
		    NSURL* requestURL = [NSURL URLWithString:endpoint];
		    NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
		    [request setHTTPMethod:@"PUT"];
		    [request setHTTPBody:jsonData];
		    NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@",
													self.authenticationHeader];
		    [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];
		    [request setValue:@"application/json" forHTTPHeaderField:@"Content-Type"];

		    NSURLSessionDataTask* dataTask = [self.session dataTaskWithRequest:request
				completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
			{
		        if (!error)
		        {
		            completion(response, error);
		        }
		        else
		        {
		            NSLog(@"Error request: %@", error);
		            completion(nil, error);
		        }
		    }];
		    [dataTask resume];
		}

		-(void) retrieveOrRequestRegistrationIdWithDeviceToken:(NSString*)token
					completion:(void(^)(NSString*, NSError*))completion
		{
		    NSString* registrationId = [[NSUserDefaults standardUserDefaults]
										objectForKey:RegistrationIdLocalStorageKey];

		    if (registrationId)
		    {
		        completion(registrationId, nil);
		        return;
		    }

		    // request new one & save
		    NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@/api/register?handle=%@",
									_endpoint, token]];
		    NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
		    [request setHTTPMethod:@"POST"];
		    NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@",
													self.authenticationHeader];
		    [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

		    NSURLSessionDataTask* dataTask = [self.session dataTaskWithRequest:request
				completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
			{
		        NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
		        if (!error && httpResponse.statusCode == 200)
		        {
		            NSString* registrationId = [[NSString alloc] initWithData:data
						encoding:NSUTF8StringEncoding];

		            // remove quotes
		            registrationId = [registrationId substringWithRange:NSMakeRange(1,
										[registrationId length]-2)];

		            [[NSUserDefaults standardUserDefaults] setObject:registrationId
						forKey:RegistrationIdLocalStorageKey];
		            [[NSUserDefaults standardUserDefaults] synchronize];

		            completion(registrationId, nil);
		        }
		        else
		        {
		            NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
		            if (error)
		                completion(nil, error);
		            else {
		                completion(nil, [NSError errorWithDomain:@"Registration" code:httpResponse.statusCode
									userInfo:nil]);
		            }
		        }
		    }];
		    [dataTask resume];
		}

		@end

	El código anterior implementa la lógica explicada en el artículo de referencia [Registro desde el back-end de la aplicación](notification-hubs-registration-management.md#registration-management-from-a-backend) usando NSURLSession para realizar llamadas REST al back-end de la aplicación y NSUserDefaults para almacenar localmente del identificador de registro devuelto por el Centro de notificaciones.

	Tenga en cuenta que esta clase requiere que su propiedad **authorizationHeader** esté establecida para que funcione correctamente. Esta propiedad la establece la clase **ViewController** después del inicio de sesión.

7. En ViewController.h, agregue una instrucción `#import` a RegisterClient.h. A continuación, agregue una declaración para el token del dispositivo y haga referencia a una instancia `RegisterClient` en la sección `@interface`:

		#import "RegisterClient.h"

		@property (strong, nonatomic) NSData* deviceToken;
		@property (strong, nonatomic) RegisterClient* registerClient;

8. En ViewController.m, agregue una declaración del método privado en la sección `@interface`:

		@interface ViewController () <UITextFieldDelegate, NSURLConnectionDataDelegate, NSXMLParserDelegate>

		// create the Authorization header to perform Basic authentication with your app back-end
		-(void) createAndSetAuthenticationHeaderWithUsername:(NSString*)username
						AndPassword:(NSString*)password;

		@end

> [AZURE.NOTE]El siguiente fragmento de código no es un esquema de autenticación seguro; debe sustituir la implementación de **createAndSetAuthenticationHeaderWithUsername:AndPassword:** por el mecanismo de autenticación específico que genera un token de autenticación para ser consumido por la clase de cliente del registro, por ejemplo, OAuth, Active Directory.

9. A continuación, en la sección `@implementation` de ViewController.m agregue el siguiente código que agrega la implementación para establecer el encabezado de autenticación y el token del dispositivo.

		-(void) setDeviceToken: (NSData*) deviceToken
		{
		    _deviceToken = deviceToken;
		    self.LogInButton.enabled = YES;
		}

		-(void) createAndSetAuthenticationHeaderWithUsername:(NSString*)username
						AndPassword:(NSString*)password;
		{
		    NSString* headerValue = [NSString stringWithFormat:@"%@:%@", username, password];

		    NSData* encodedData = [[headerValue dataUsingEncoding:NSUTF8StringEncoding] base64EncodedDataWithOptions:NSDataBase64EncodingEndLineWithCarriageReturn];

		    self.registerClient.authenticationHeader = [[NSString alloc] initWithData:encodedData
														encoding:NSUTF8StringEncoding];
		}

		-(BOOL)textFieldShouldReturn:(UITextField *)textField
		{
		    [textField resignFirstResponder];
		    return YES;
		}

	Observe cómo al establecer el token del dispositivo se habilita el botón de inicio de sesión. Esto se debe a que, como una parte de la acción de inicio de sesión, el controlador de vista registrará notificaciones de inserción con el back-end de la aplicación. Por lo tanto, no se desea que la acción Iniciar sesión sea accesible hasta que el token del dispositivo se haya configurado correctamente. Es posible que desacople el inicio de sesión del registro de inserción, siempre que lo primero ocurra antes que lo último.

10. En ViewController.m, utilice los siguientes fragmentos de código para implementar el método de acción para el botón **Iniciar sesión** y un método para enviar el mensaje de notificación con el back-end ASP.NET.

		- (IBAction)LogInAction:(id)sender {
		    // create authentication header and set it in register client
		    NSString* username = self.UsernameField.text;
		    NSString* password = self.PasswordField.text;

		    [self createAndSetAuthenticationHeaderWithUsername:username AndPassword:password];

		    __weak ViewController* selfie = self;
		    [self.registerClient registerWithDeviceToken:self.deviceToken tags:nil
				andCompletion:^(NSError* error) {
		        if (!error) {
		            dispatch_async(dispatch_get_main_queue(),
					^{
		                selfie.SendNotificationButton.enabled = YES;
		                [self MessageBox:@"Success" message:@"Registered successfully!"];
		            });
		        }
		    }];
		}


		- (void)SendNotificationASPNETBackend:(NSString*)pns UsernameTag:(NSString*)usernameTag
					Message:(NSString*)message
		{
		    NSURLSession* session = [NSURLSession
		    	sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration] delegate:nil
		        delegateQueue:nil];

			// Pass the pns and username tag as parameters with the REST URL to the ASP.NET backend
		    NSURL* requestURL = [NSURL URLWithString:[NSString
				stringWithFormat:@"%@/api/notifications?pns=%@&to_tag=%@", BACKEND_ENDPOINT, pns,
				usernameTag]];

		    NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
		    [request setHTTPMethod:@"POST"];

			// Get the mock authenticationheader from the register client
		    NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@",
				self.registerClient.authenticationHeader];
		    [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

		    //Add the notification message body
		    [request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];
		    [request setHTTPBody:[message dataUsingEncoding:NSUTF8StringEncoding]];

			// Execute the send notification REST API on the ASP.NET Backend
		    NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request
				completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
			{
		        NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
		        if (error || httpResponse.statusCode != 200)
		        {
		            NSString* status = [NSString stringWithFormat:@"Error Status for %@: %d\nError: %@\n",
										pns, httpResponse.statusCode, error];
		            dispatch_async(dispatch_get_main_queue(),
		            ^{
						// Append text because all 3 PNS calls may also have information to view
		                [self.sendResults setText:[self.sendResults.text stringByAppendingString:status]];
		            });
		            NSLog(status);
		        }

		        if (data != NULL)
		        {
		            xmlParser = [[NSXMLParser alloc] initWithData:data];
		            [xmlParser setDelegate:self];
		            [xmlParser parse];
		        }
			}];
		    [dataTask resume];
		}


11. Actualice la acción para que el botón **Enviar notificación** utilice el back-end ASP.NET y envíela a cualquier PNS habilitada por un modificador.


		- (IBAction)SendNotificationMessage:(id)sender
		{
		    //[self SendNotificationRESTAPI];
		    [self SendToEnabledPlatforms];
		}


		-(void)SendToEnabledPlatforms
		{
		    NSString* json = [NSString stringWithFormat:@""%@"",self.notificationMessage.text];

			[self.sendResults setText:@""];

		    if ([self.WNSSwitch isOn])
		        [self SendNotificationASPNETBackend:@"wns" UsernameTag:self.RecipientField.text Message:json];

		    if ([self.GCMSwitch isOn])
		        [self SendNotificationASPNETBackend:@"gcm" UsernameTag:self.RecipientField.text Message:json];

		    if ([self.APNSSwitch isOn])
		        [self SendNotificationASPNETBackend:@"apns" UsernameTag:self.RecipientField.text Message:json];
		}



11. En la función **ViewDidLoad**, agregue el siguiente código para crear una instancia de RegisterClient y establezca el delegado para sus campos de texto.

		self.UsernameField.delegate = self;
		self.PasswordField.delegate = self;
		self.RecipientField.delegate = self;
		self.registerClient = [[RegisterClient alloc] initWithEndpoint:BACKEND_ENDPOINT];

12. Ahora, en **AppDelegate.m**, quite todo el contenido del método **application:didRegisterForPushNotificationWithDeviceToken:** y reemplácelo por lo siguiente para asegurarse de que el controlador de vista contiene el token de dispositivo más reciente recuperado desde APN:

		// Add import to the top of the file
		#import "ViewController.h"

	    - (void)application:(UIApplication *)application
	    			didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
	    {
		    ViewController* rvc = (ViewController*) self.window.rootViewController;
		    rvc.deviceToken = deviceToken;
		}

13. Finalmente, en **AppDelegate.m**, asegúrese de que tiene el siguiente método:

		- (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
		    NSLog(@"%@", userInfo);
		    [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
		}

## Prueba de la aplicación

1. En XCode, ejecute la aplicación en un dispositivo iOS físico (las notificaciones de inserción no funcionarán en el simulador).

2. En la interfaz de usuario de la aplicación iOS, escriba un nombre de usuario y contraseña. Esta información puede ser cualquier cadena, pero deben tener ambos el mismo valor de cadena. A continuación, haga clic en **Iniciar sesión**.

	![][2]


3. Debería ver una ventana emergente que le informa del éxito de registro. Haga clic en **Aceptar**.

	![][3]

4. En el campo de texto **Etiqueta de nombre de usuario destinatario**, escriba la etiqueta de nombre de usuario usada con el registro desde otro dispositivo.
5. Escriba un mensaje de notificación y haga clic en **Enviar notificación**. Solo los dispositivos que tienen un registro con la etiqueta de nombre de usuario del destinatario reciben el mensaje de notificación. Solo se envía a los usuarios.

	![][4]


[1]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-interface.png
[2]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-enter-user-pwd.png
[3]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-registered.png
[4]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-enter-msg.png

<!---HONumber=AcomDC_1217_2015-->