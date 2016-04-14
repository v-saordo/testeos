
A continuación, debe cambiar la manera en que se registran las notificaciones de inserción para asegurarse de que el usuario se autentique antes de que se intente el registro.

1. En **QSAppDelegate.m**, quite por completo la implementación de **didFinishLaunchingWithOptions**:

2. Abra **QSTodoListViewController.m** y agregue el siguiente código al final del método **viewDidLoad**:

```
// Register for remote notifications
[[UIApplication sharedApplication] registerForRemoteNotificationTypes:
UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
```

<!---HONumber=Oct15_HO3-->