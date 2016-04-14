
* En **QSAppDelegate.m**, importe el SDK de iOS y **QSTodoService.h**:

```
        #import <WindowsAzureMobileServices/WindowsAzureMobileServices.h>
        #import "QSTodoService.h"
```

* En `didFinishLaunchingWithOptions`, en **QSAppDelegate.m**, inserte las líneas siguientes justo antes de `return YES;`:

```
        UIUserNotificationSettings* notificationSettings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeAlert | UIUserNotificationTypeBadge | UIUserNotificationTypeSound categories:nil];
        [[UIApplication sharedApplication] registerUserNotificationSettings:notificationSettings];
        [[UIApplication sharedApplication] registerForRemoteNotifications];
```

* En **QSAppDelegate.m**, agregue los métodos de controlador siguientes. Ahora su aplicación está actualizada para que sea compatible con las notificaciones push.

```
        // Registration with APNs is successful
        - (void)application:(UIApplication *)application
        didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken {

            QSTodoService *todoService = [QSTodoService defaultService];
            MSClient *client = todoService.client;

            [client.push registerDeviceToken:deviceToken completion:^(NSError *error) {
                if (error != nil) {
                    NSLog(@"Error registering for notifications: %@", error);
                }
            }];
        }

        // Handle any failure to register
        - (void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:
        (NSError *)error {
            NSLog(@"Failed to register for remote notifications: %@", error);
        }

        // Use userInfo in the payload to display a UIAlertView.
        - (void)application:(UIApplication *)application
              didReceiveRemoteNotification:(NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);

            NSDictionary *apsPayload = userInfo[@"aps"];
            NSString *alertString = apsPayload[@"alert"];

            UIAlertView *alert = [[UIAlertView alloc]
              initWithTitle:@"Notification"
              message:alertString
              delegate:nil
              cancelButtonTitle:@"OK"
              otherButtonTitles:nil];
            [alert show];
        }
```

<!---HONumber=Oct15_HO3-->