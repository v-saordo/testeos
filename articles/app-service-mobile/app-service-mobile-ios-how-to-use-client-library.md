<properties
	pageTitle="Uso del SDK de iOS para Aplicaciones móviles de Azure"
	description="Uso del SDK de iOS para Aplicaciones móviles de Azure"
	services="app-service\mobile"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="12/30/2015"
	ms.author="krisragh"/>

# Uso de la biblioteca de cliente de iOS para Aplicaciones móviles de Azure

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]&nbsp;
 
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

Esta guía muestra cómo realizar algunas tareas comunes a través del [SDK de iOS de Aplicaciones móviles de Azure](https://go.microsoft.com/fwLink/?LinkID=266533&clcid=0x409). Si está familiarizado con Aplicaciones móviles de Azure, complete primero [Inicio rápido de Aplicaciones móviles de Azure] para crear un back-end, crear una tabla y descargar un proyecto de Xcode para iOS pregenerada. En esta guía, nos centramos en el SDK de iOS de cliente. Para obtener más información sobre el SDK del lado servidor de .NET para el back-end, consulte [Trabajo con el back-end de .NET](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)

## Documentación de referencia

La documentación de referencia para el SDK de cliente de iOS se encuentra aquí: [Referencia de cliente de iOS para aplicaciones móviles de Azure](http://azure.github.io/azure-mobile-services/iOS/v3/).

##<a name="Setup"></a>Configuración y requisitos previos

En esta guía se asume que ha creado un back-end con una tabla. En esta guía se asume que la tabla tiene el mismo esquema que las tablas de dichos tutoriales. En esta guía también se supone que en el código se hace referencia a `MicrosoftAzureMobile.framework` e importa `MicrosoftAzureMobile/MicrosoftAzureMobile.h`.

##<a name="create-client"></a>Creación del cliente

Para obtener acceso al back-end de Aplicaciones móviles de Azure en el proyecto, cree un `MSClient`. Reemplace `AppUrl` por la dirección URL de la aplicación. Puede dejar `gatewayURLString` y `applicationKey` vacías. Si configura una puerta de enlace para la autenticación, rellene `gatewayURLString` con la dirección URL de la puerta de enlace.

**Objective-C**:

```
MSClient *client = [MSClient clientWithApplicationURLString:@"AppUrl"];
```

**Swift**:

```
let client = MSClient(applicationURLString: "AppUrl")
```


##<a name="table-reference"></a>Creación de una referencia de tabla

Para acceder a los datos o actualizarlos, cree una referencia a la tabla de back-end. Reemplace `TodoItem` por el nombre de la tabla.

**Objective-C**:

```
MSTable *table = [client tableWithName:@"TodoItem"];
```

**Swift**:

```
let table = client.tableWithName("TodoItem")
```


##<a name="querying"></a>Consulta de datos

Para crear una consulta de base de datos, consulte el objeto `MSTable`. La consulta siguiente obtiene todos los elementos de `TodoItem` y registra el texto de cada elemento.

**Objective-C**:

```
[table readWithCompletion:^(MSQueryResult *result, NSError *error) {
		if(error) { // error is nil if no error occured
				NSLog(@"ERROR %@", error);
		} else {
				for(NSDictionary *item in result.items) { // items is NSArray of records that match query
						NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
				}
		}
}];
```

**Swift**:

```
table.readWithCompletion({(result, error) -> Void in
    if error != nil { // error is nil if no error occured
        NSLog("ERROR %@", error!)
    } else {
        for item in (result?.items)! {
            NSLog("Todo Item: %@", item["text"] as! String)
        }
    }
})
```

##<a name="filtering"></a>Filtro de datos devueltos

Para filtrar los resultados, hay muchas opciones disponibles.

Para filtrar mediante un predicado, use un `NSPredicate` y `readWithPredicate`. Los siguientes filtros devolvieron datos para buscar solo los elementos Todo incompletos.

**Objective-C**:

```
// Create a predicate that finds items where complete is false
NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];
// Query the TodoItem table 
[table readWithPredicate:predicate completion:^(MSQueryResult *result, NSError *error) {
		if(error) {
				NSLog(@"ERROR %@", error);
		} else {
				for(NSDictionary *item in result.items) {
						NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
				}
		}
}];
```

**Swift**:

```
// Create a predicate that finds items where complete is false
let predicate =  NSPredicate(format:"complete == NO")
// Query the TodoItem table 
table.readWithPredicate(predicate, completion: { (result, error) -> Void in
    if error != nil {
        NSLog("ERROR %@", error!)
    } else {
        for item in (result?.items)! {
            NSLog("Todo Item: %@", item["text"] as! String)
        }
    }
})
```

##<a name="query-object"></a>Uso de MSQuery

Para realizar una consulta compleja (como de ordenación y paginación), cree un objeto `MSQuery` directamente o mediante un predicado:

**Objective-C**:

```
MSQuery *query = [table query];
MSQuery *query = [table queryWithPredicate: [NSPredicate predicateWithFormat:@"complete == NO"]];
```

**Swift**:

```
let query = table.query()
let query = table.queryWithPredicate(NSPredicate(format:"complete == NO"))
```

`MSQuery` le permite controlar varios comportamientos de consulta, incluidos los siguientes. Ejecutar una consulta `MSQuery` llamando a `readWithCompletion` en ella, como se muestra en el ejemplo siguiente. * Especificar el orden de los resultados * Limitar los campos que desea devolver * Limitar el número de registros a devolver * Especificar el recuento total de la respuesta * Especificar parámetros de cadena de consulta personalizados en la solicitud * Aplicar funciones adicionales


## <a name="sorting"></a>Ordenación de datos con MSQuery

Para ordenar los resultados, echemos un vistazo a un ejemplo. Para ordenar en primer lugar en orden ascendente por campo `text` y, a continuación, por orden descendente por campo `completion`, invoque `MSQuery`:

**Objective-C**:

```
[query orderByAscending:@"text"];
[query orderByDescending:@"complete"];
[query readWithCompletion:^(MSQueryResult *result, NSError *error) {
		if(error) {
				NSLog(@"ERROR %@", error);
		} else {
				for(NSDictionary *item in result.items) {
						NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
				}
		}
}];
```

**Swift**:

```        
query.orderByAscending("text")
query.orderByDescending("complete")
query.readWithCompletion { (result, error) -> Void in
    if error != nil {
        NSLog("ERROR %@", error!)
    } else {
        for item in (result?.items)! {
            NSLog("Todo Item: %@", item["text"] as! String)
        }
    }
}
```


## <a name="selecting"></a><a name="parameters"></a>Limitación de campos y expansión de los parámetros de cadena de consulta con MSQuery

Para limitar los campos que se devolverán en una consulta, especifique los nombres de los campos en la propiedad **selectFields**. Esto solamente devuelven los campos de texto y aquellos que se hayan rellenado:

**Objective-C**:

```
query.selectFields = @[@"text", @"complete"];
```

**Swift**:

```
query.selectFields = ["text", "complete"]
```

Para incluir parámetros de cadena de consulta adicionales en la solicitud al servidor (por ejemplo, porque los utiliza un script de servidor personalizado), introduzca `query.parameters`:

**Objective-C**:

```
query.parameters = @{
	@"myKey1" : @"value1",
	@"myKey2" : @"value2",
};
```

**Swift**:

```
query.parameters = ["myKey1": "value1", "myKey2": "value2"]
```

##<a name="inserting"></a>Insertar datos

Para insertar una nueva fila en la tabla, cree un nuevo `NSDictionary` e invoque `table insert`. Servicios móviles genera automáticamente columnas nuevas basadas en `NSDictionary` si [Esquema dinámico] no está deshabilitado.

Si no se proporciona `id`, el backend genera automáticamente un nuevo identificador único. Proporcione su propio `id` para utilizar direcciones de correo electrónico, nombres de usuario o sus propios valores personalizados como Id. Proporcionar su propio ID puede facilitar las combinaciones y la lógica de la base de datos de tipo empresarial.

`result` contiene el nuevo elemento que se insertó; dependiendo de la lógica del servidor, puede tener datos adicionales o modificados en comparación con lo que se pasó al servidor.

**Objective-C**:

```
NSDictionary *newItem = @{@"id": @"custom-id", @"text": @"my new item", @"complete" : @NO};
[table insert:newItem completion:^(NSDictionary *result, NSError *error) {
	if(error) {
		NSLog(@"ERROR %@", error);
	} else {
		NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
	}
}];
```

**Swift**:

```
let newItem = ["id": "custom-id", "text": "my new item", "complete": false]
table.insert(newItem) { (result, error) -> Void in
    if error != nil {
        NSLog("ERROR %@", error!)
    } else {
        NSLog("Todo Item: %@", result!["text"] as! String)
    }
}
```

##<a name="modifying"></a>Modificación de datos

Para actualizar una fila existente, modifique un elemento y llame a `update`:

**Objective-C**:

```
NSMutableDictionary *newItem = [oldItem mutableCopy]; // oldItem is NSDictionary
[newItem setValue:@"Updated text" forKey:@"text"];
[table update:newItem completion:^(NSDictionary *result, NSError *error) {
	if(error) {
		NSLog(@"ERROR %@", error);
	} else {
		NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
	}
}];
```

**Swift**:

```
let newItem = oldItem.mutableCopy() as! NSMutableDictionary // oldItem is NSDictionary
newerItem["text"] = "Updated text"
table.update(newerItem  as [NSObject : AnyObject]) { (result, error) -> Void in
    if error != nil {
        NSLog("ERROR %@", error!)
    } else {
        NSLog("Todo Item: %@", result!["text"] as! String)
    }
}
```

Como alternativa, proporcione el identificador de fila y el campo actualizado:

**Objective-C**:

```
[table update:@{@"id":@"custom-id", @"text":"my EDITED item"} completion:^(NSDictionary *result, NSError *error) {
	if(error) {
		NSLog(@"ERROR %@", error);
	} else {
		NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
	}
}];
```

**Swift**:

```
table.update(["id": "custom-id", "text": "my EDITED item"]) { (result, error) -> Void in
    if error != nil {
        NSLog("ERROR %@", error!)
    } else {
        NSLog("Todo Item: %@", result!["text"] as! String)
    }
    
}
```

Como mínimo, debe establecerse el atributo `id` al realizar actualizaciones.

##<a name="deleting"></a>Eliminación de datos

Para eliminar un elemento, invoque `delete` con el elemento:

**Objective-C**:

```
[table delete:item completion:^(id itemId, NSError *error) {
	if(error) {
		NSLog(@"ERROR %@", error);
	} else {
		NSLog(@"Todo Item ID: %@", itemId);
	}
}];
```

**Swift**:

```
table.delete(item as [NSObject : AnyObject]) { (itemId, error) -> Void in
	if error != nil {
		NSLog("ERROR %@", error!)
	} else {
		NSLog("Todo Item ID: %@", itemId! as! String)
	}
}
```

O bien elimínelo proporcionando un identificador de fila:

**Objective-C**:

```
[table deleteWithId:@"37BBF396-11F0-4B39-85C8-B319C729AF6D" completion:^(id itemId, NSError *error) {
	if(error) {
		NSLog(@"ERROR %@", error);
	} else {
		NSLog(@"Todo Item ID: %@", itemId);
	}
}];   
```

**Swift**:

```
table.deleteWithId("37BBF396-11F0-4B39-85C8-B319C729AF6D") { (itemId, error) -> Void in
        if error != nil {
        	NSLog("ERROR %@", error!)
        } else {
        	NSLog("Todo Item ID: %@", itemId! as! String)
        }
}
```

Como mínimo, el atributo `id` debe establecerse a la hora de efectuar eliminaciones.

##<a name="templates"></a>Procedimiento: Registro de plantillas push para enviar notificaciones entre plataformas

Para registrar plantillas, basta con pasar las plantillas con el método **client.push registerDeviceToken** en la aplicación cliente.

**Objective-C**:

```
[client.push registerDeviceToken:deviceToken template:iOSTemplate completion:^(NSError *error) {
	if(error) {
		NSLog(@"ERROR %@", error);
	}
}];
```

**Swift**:

```
client.push!.registerDeviceToken(deviceToken, template: iOSTemplate, completion: { (error) -> Void in
            if error != nil {
                NSLog("ERROR %@", error!)
            }
        })
```

Las plantillas serán de tipo NSDictionary y pueden contener varias plantillas con el formato siguiente:

**Objective-C**:

```
NSDictionary *iOSTemplate = @{ @"templateName": @{ @"body": @{ @"aps": @{ @"alert": @"$(message)" } } } };
```

**Swift**:

```
let iOSTemplate: [NSObject : AnyObject] = ["templateName": ["body": ["aps": ["alert": "$(message)"]]]]
```

Tenga en cuenta que se eliminará todas las etiquetas por seguridad. Para agregar etiquetas a las instalaciones o plantillas dentro de las instalaciones, vea [Trabajar con el SDK del servidor back-end de .NET para Aplicaciones móviles de Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#tags).

Para enviar notificaciones mediante estas plantillas registradas, trabaje con [API de Centros de notificaciones](https://msdn.microsoft.com/library/azure/dn495101.aspx).

##<a name="errors"></a>Gestión de errores

Al realizar una llamada a un servicio móvil, el bloque de finalización contiene un parámetro `NSError`. En caso de producirse un error, este parámetro no será nulo. En su código, debe marcar este parámetro y administrar el error según sea necesario, como se muestra en los fragmentos de código anteriores.

El archivo [`<WindowsAzureMobileServices/MSError.h>`](https://github.com/Azure/azure-mobile-services/blob/master/sdk/iOS/src/MSError.h) define las constantes `MSErrorResponseKey`, `MSErrorRequestKey` y `MSErrorServerItemKey` para obtener más datos relacionados con el error, que se pueden obtener de la siguiente manera:

**Objective-C**:

```
NSDictionary *serverItem = [error.userInfo objectForKey:MSErrorServerItemKey];
```

**Swift**:

```
let serverItem = error?.userInfo[MSErrorServerItemKey]
```

Además, el archivo define constantes para cada código de error, que se pueden usar como se indica a continuación:

**Objective-C**:

```
if (error.code == MSErrorPreconditionFailed) {
```

**Swift**:

```
if (error?.code == MSErrorPreconditionFailed) {
```

## <a name="adal"></a>Autenticación de usuarios con la biblioteca de autenticación de Active Directory

Puede utilizar la biblioteca de autenticación de Active Directory (ADAL) para iniciar la sesión de los usuarios en su aplicación con Azure Active Directory. Esta opción es con frecuencia preferible al uso de los métodos `loginAsync()`, ya que proporciona una experiencia UX más nativa y permite personalizaciones adicionales.

1. Configure su back-end de aplicación móvil para el inicio de sesión en AAD siguiendo el tutorial [Configuración de la aplicación del Servicio de aplicaciones para usar el inicio de sesión de Azure Active Directory](app-service-mobile-how-to-configure-active-directory-authentication.md). Asegúrese de completar el paso opcional de registrar una aplicación cliente nativa. Para iOS, se recomienda (aunque no es obligatorio) que el URI de redirección tenga el formato `<app-scheme>://<bundle-id>`. Para más detalles, consulte el [inicio rápido de iOS para ADAL](active-directory-devquickstarts-ios.md#em1-determine-what-your-redirect-uri-will-be-for-iosem).

2. Instale ADAL mediante Cocoapods. Edite el perfil para incluir lo siguiente, sustituyendo **YOUR-PROJECT** por el nombre de su proyecto de Xcode:

	source 'https://github.com/CocoaPods/Specs.git' link\_with ['YOUR-PROJECT'] xcodeproj 'YOUR-PROJECT'
	
	pod 'ADALiOS'

3. Mediante el Terminal, ejecute `pod install` desde el directorio que contiene el proyecto y, a continuación, abra el área de trabajo del Xcode generado (no el proyecto).

4. Agregue el siguiente código a la aplicación, según el lenguaje que esté utilizando. En cada caso, realice las sustituciones siguientes:

* Sustituya **INSERT-AUTHORITY-HERE** por el nombre del inquilino en el que ha aprovisionado la aplicación. El formato debe ser https://login.windows.net/contoso.onmicrosoft.com. Este valor se puede copiar de la pestaña Dominio de Azure Active Directory en el [Portal de Azure clásico].

* Sustituya **INSERT-RESOURCE-ID-HERE** por el id. de cliente del back-end de la aplicación móvil. Puede obtenerlo en la pestaña **Avanzadas** en **Configuración de Azure Active Directory** en el portal.

* Sustituya **INSERT-CLIENT-ID-HERE** por el id. de cliente que copió de la aplicación cliente nativa.

* Sustituya **INSERT-REDIRECT-URI-HERE** por el punto de conexión _/.auth/login/done_ del sitio, mediante el esquema HTTPS. Este valor debería ser similar a \__https://contoso.azurewebsites.net/.auth/login/done_.

**Objective-C**:

	#import <ADALiOS/ADAuthenticationContext.h>
	#import <ADALiOS/ADAuthenticationSettings.h>
	// ...
	- (void) authenticate:(UIViewController*) parent
	           completion:(void (^) (MSUser*, NSError*))completionBlock;
	{
	    NSString *authority = @"INSERT-AUTHORITY-HERE";
	    NSString *resourceId = @"INSERT-RESOURCE-ID-HERE";
	    NSString *clientId = @"INSERT-CLIENT-ID-HERE";
	    NSURL *redirectUri = [[NSURL alloc]initWithString:@"INSERT-REDIRECT-URI-HERE"];
	    ADAuthenticationError *error;
	    ADAuthenticationContext authContext = [ADAuthenticationContext authenticationContextWithAuthority:authority error:&error];
	    authContext.parentController = parent;
	    [ADAuthenticationSettings sharedInstance].enableFullScreen = YES;
	    [authContext acquireTokenWithResource:resourceId
	                                 clientId:clientId
	                              redirectUri:redirectUri
	                          completionBlock:^(ADAuthenticationResult *result) {
	                              if (result.status != AD_SUCCEEDED)
	                              {
	                                  completionBlock(nil, result.error);;
	                              }
	                              else
	                              {
	                                  NSDictionary *payload = @{
	                                                            @"access_token" : result.tokenCacheStoreItem.accessToken
	                                                            };
	                                  [client loginWithProvider:@"aad" token:payload completion:completionBlock];
	                              }
	                          }];
	}


**Swift**:

	// add the following imports to your bridging header:
	//     #import <ADALiOS/ADAuthenticationContext.h>
	//     #import <ADALiOS/ADAuthenticationSettings.h>
	
	func authenticate(parent:UIViewController, completion: (MSUser?, NSError?) -> Void) {
		let authority = "INSERT-AUTHORITY-HERE"
		let resourceId = "INSERT-RESOURCE-ID-HERE"
		let clientId = "INSERT-CLIENT-ID-HERE"
		let redirectUri = NSURL(string: "INSERT-REDIRECT-URI-HERE")
		var error: AutoreleasingUnsafeMutablePointer<ADAuthenticationError?> = nil
		let authContext = ADAuthenticationContext(authority: authority, error: error)
		authContext.parentController = parent
		ADAuthenticationSettings.sharedInstance().enableFullScreen = true;
		authContext.acquireTokenWithResource(resourceId, clientId: clientId, redirectUri: redirectUri, completionBlock: { (result) -> Void in
			if result.status != AD_SUCCEEDED {
				completion(nil, result.error)
			}
			else {
				let payload:[String:String] = ["access_token":result.tokenCacheStoreItem.accessToken]
				client.loginWithProvider("aad", token: payload, completion: completion)
			}
		})
	}


<!-- Anchors. -->

[What is Mobile Services]: #what-is
[Concepts]: #concepts
[Setup and Prerequisites]: #Setup
[How to: Create the Mobile Services client]: #create-client
[How to: Create a table reference]: #table-reference
[How to: Query data from a mobile service]: #querying
[Filter returned data]: #filtering
[Sort returned data]: #sorting
[Return data in pages]: #paging
[Select specific columns]: #selecting
[How to: Bind data to the user interface]: #binding
[How to: Insert data into a mobile service]: #inserting
[How to: Modify data in a mobile service]: #modifying
[How to: Authenticate users]: #authentication
[Cache authentication tokens]: #caching-tokens
[How to: Upload images and large files]: #blobs
[How to: Handle errors]: #errors
[How to: Design unit tests]: #unit-testing
[How to: Customize the client]: #customizing
[Customize request headers]: #custom-headers
[Customize data type serialization]: #custom-serialization
[Next Steps]: #next-steps
[How to: Use MSQuery]: #query-object

<!-- Images. -->

<!-- URLs. -->
[Inicio rápido de Aplicaciones móviles de Azure]: app-service-mobile-ios-get-started.md

[Add Mobile Services to Existing App]: /develop/mobile/tutorials/get-started-data
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Validate and modify data in Mobile Services by using server scripts]: /develop/mobile/tutorials/validate-modify-and-augment-data-ios
[Mobile Services SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[iOS SDK]: https://developer.apple.com/xcode

[Handling Expired Tokens]: http://go.microsoft.com/fwlink/p/?LinkId=301955
[Live Connect SDK]: http://go.microsoft.com/fwlink/p/?LinkId=301960
[Permissions]: http://msdn.microsoft.com/library/windowsazure/jj193161.aspx
[Service-side Authorization]: mobile-services-javascript-backend-service-side-authorization.md
[Use scripts to authorize users]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[Esquema dinámico]: http://go.microsoft.com/fwlink/p/?LinkId=296271
[How to: access custom parameters]: /develop/mobile/how-to-guides/work-with-server-scripts#access-headers
[Create a table]: http://msdn.microsoft.com/library/windowsazure/jj193162.aspx
[NSDictionary object]: http://go.microsoft.com/fwlink/p/?LinkId=301965
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[CLI to manage Mobile Services tables]: ../virtual-machines-command-line-tools.md#Mobile_Tables
[Conflict-Handler]: mobile-services-ios-handling-conflicts-offline-data.md#add-conflict-handling

<!---HONumber=AcomDC_0128_2016-->