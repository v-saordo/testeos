* Abra **QSTodoListViewController.m** y agregue el siguiente método. Cambie _facebook_ a _microsoftaccount_, _twitter_, _google_ o _windowsazureactivedirectory_ si no usa Facebook como su proveedor de identidades.

```
        - (void) loginAndGetData
        {
            MSClient *client = self.todoService.client;
            if (client.currentUser != nil) {
                return;
            }

            [client loginWithProvider:@"facebook" controller:self animated:YES completion:^(MSUser *user, NSError *error) {
                [self refresh];
            }];
        }
```

* Reemplace `[self refresh]` en `viewDidLoad` por lo siguiente:

```
        [self loginAndGetData];
```

* Presione **Ejecutar** para iniciar la aplicación y, a continuación, inicie sesión. Una vez que haya iniciado sesión, debería poder ver la lista de tareas pendientes y realizar actualizaciones en ella.

<!---HONumber=Oct15_HO3-->