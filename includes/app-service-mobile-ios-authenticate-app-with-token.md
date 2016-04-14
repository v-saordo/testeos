
En el ejemplo anterior se pone en contacto con el proveedor de identidades y el servicio móvil cada vez que inicie la aplicación. En su lugar, puede almacenar en caché el token de autorización y usarla en primer lugar.

* Para cifrar y almacenar tokens de autenticación en un cliente iOS, se recomienda usar iOS Keychain. Vamos a usar [SSKeychain](https://github.com/soffes/sskeychain) (un contenedor simple de iOS Keychain). Siga las instrucciones de la página de SSKeychain para agregarlo al proyecto. Compruebe que el valor **Habilitar módulos** está habilitado en la opción **Configuración de compilación** (sección **Apple LLVM - Idiomas - Módulos**) del proyecto.

* Abra **QSTodoListViewController.m** y agregue el siguiente código:

```
		- (void) saveAuthInfo {
				[SSKeychain setPassword:self.todoService.client.currentUser.mobileServiceAuthenticationToken forService:@"AzureMobileServiceTutorial" account:self.todoService.client.currentUser.userId]
		}


		- (void)loadAuthInfo {
				NSString *userid = [[SSKeychain accountsForService:@"AzureMobileServiceTutorial"][0] valueForKey:@"acct"];
		    if (userid) {
		        NSLog(@"userid: %@", userid);
		        self.todoService.client.currentUser = [[MSUser alloc] initWithUserId:userid];
		         self.todoService.client.currentUser.mobileServiceAuthenticationToken = [SSKeychain passwordForService:@"AzureMobileServiceTutorial" account:userid];

		    }
		}
```

* En `loginAndGetData`, modifique el bloque de finalización de `loginWithProvider:controller:animated:completion:`. Agregue la siguiente línea justo antes de `[self refresh]` para almacenar el Id. de usuario y las propiedades del token:

```
				[self saveAuthInfo];
```

* Se cargan el Id. de usuario y las propiedades del token cuando se inicia la aplicación. En el `viewDidLoad` en **QSTodoListViewController.m**, agréguelo justo después de que se inicialice `self.todoService`.

```
				[self loadAuthInfo];
```

<!---HONumber=Oct15_HO3-->