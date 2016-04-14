<properties
	pageTitle="Cómo usar la biblioteca de cliente de iOS para Servicios móviles de Azure"
	description="Uso de la biblioteca del cliente iOS para Servicios móviles"
	services="mobile-services"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="01/12/2016"
	ms.author="krisragh"/>

# Cómo usar la biblioteca de cliente de iOS para Servicios móviles de Azure

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-client-library](../../includes/mobile-services-selector-client-library.md)]

Esta guía muestra cómo realizar algunas tareas comunes a través del [SDK de iOS] de los Servicios móviles de Azure. Si no está familiarizado con los Servicios móviles, complete primero [Introducción a Servicios móviles] para configurar su cuenta, crear una tabla y crear un servicio móvil.

> [AZURE.NOTE]Esta guía usa el último [SDK de Servicios móviles de iOS](https://go.microsoft.com/fwLink/?LinkID=266533&clcid=0x409). Si el proyecto utiliza una versión anterior del SDK, actualice primero el marco de trabajo en Xcode.

[AZURE.INCLUDE [mobile-services-concepts](../../includes/mobile-services-concepts.md)]

##<a name="Setup"></a>Configuración y requisitos previos

En esta guía se asume que ha creado un servicio móvil con una tabla. Para obtener más información, vea [Crear una tabla] o reutilice la tabla `TodoItem` creada en [Introducción a Servicios móviles]. En esta guía se asume que la tabla tiene el mismo esquema que las tablas de dichos tutoriales. En esta guía también se da por supuesto que el Xcode hace referencia a `WindowsAzureMobileServices.framework` e importa `WindowsAzureMobileServices/WindowsAzureMobileServices.h`.

##<a name="create-client"></a>Creación del cliente de Servicios móviles

Para obtener acceso a un Servicio móvil de Azure en el proyecto, cree un objeto de cliente `MSClient`. Reemplace `AppUrl` y `AppKey` por la dirección URL del Servicio móvil y los valores del panel de la clave de aplicación respectivamente.

```
MSClient *client = [MSClient clientWithApplicationURLString:@"AppUrl" applicationKey:@"AppKey"];
```

##<a name="table-reference"></a>Creación de una referencia de tabla

Para acceder o actualizar datos para el Servicio móvil de Azure, cree una referencia a la tabla. Reemplace `TodoItem` por el nombre de la tabla.

```
	MSTable *table = [client tableWithName:@"TodoItem"];
```

##<a name="querying"></a>Consulta de datos

Para crear una consulta de base de datos, consulte el objeto `MSTable`. La consulta siguiente obtiene todos los elementos de `TodoItem` y registra el texto de cada elemento.

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

##<a name="filtering"></a>Filtro de datos devueltos

Para filtrar los resultados, hay muchas opciones disponibles.

Para filtrar mediante un predicado, use un `NSPredicate` y `readWithPredicate`. Los siguientes filtros devolvieron datos para buscar solo los elementos Todo incompletos.

```
// Create a predicate that finds items where complete is false
NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];
// Query the TodoItem table and update the items property with the results from the service
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

##<a name="query-object"></a>Uso de MSQuery

Para realizar una consulta compleja (como de ordenación y paginación), cree un objeto `MSQuery` directamente o mediante un predicado:

```
    MSQuery *query = [table query];
    MSQuery *query = [table queryWithPredicate: [NSPredicate predicateWithFormat:@"complete == NO"]];
```

`MSQuery` le permite controlar varios comportamientos de consulta, incluidos los siguientes. Ejecutar una consulta `MSQuery` llamando a `readWithCompletion` en ella, como se muestra en el ejemplo siguiente. * Especificar el orden de los resultados * Limitar los campos que desea devolver * Limitar el número de registros a devolver * Especificar el recuento total de la respuesta * Especificar parámetros de cadena de consulta personalizados en la solicitud * Aplicar funciones adicionales


## <a name="sorting"></a>Ordenación de datos con MSQuery

Para ordenar los resultados, echemos un vistazo a un ejemplo. Para ordenar en primer lugar en orden ascendente por campo `text` y, a continuación, por orden descendente por campo `completion`, invoque `MSQuery`:

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

## <a name="paging"></a>Devolución de datos de páginas mediante MSQuery

Servicios móviles limita la cantidad de registros que se devuelven en una sola respuesta. Para controlar el número de registros que se muestra a los usuarios, debe implementar un sistema de paginación. La paginación se obtiene mediante el uso de las tres propiedades siguientes del objeto **MSQuery**:

```
+	`BOOL includeTotalCount`
+	`NSInteger fetchLimit`
+	`NSInteger fetchOffset`
```

En el ejemplo siguiente, una sola función solicita 5 registros del servidor y, a continuación, los anexa a la colección local de registros previamente cargados:

```
// Create and initialize these properties
@property (nonatomic, strong)   NSMutableArray *loadedItems; // Init via [[NSMutableArray alloc] init]
@property (nonatomic)   				BOOL moreResults;
```

```
-(void)loadResults
{
    MSQuery *query = [self.table query];

    query.includeTotalCount = YES;
    query.fetchLimit = 5;
    query.fetchOffset = self.loadedItems.count;


    [query readWithCompletion:^(MSQueryResult *result, NSError *error) {
        if(!error) {
            // Add the items to our local copy
            [self.loadedItems addObjectsFromArray:result.items];

            // Set a flag to keep track if there are any additional records we need to load
            self.moreResults = (self.loadedItems.count <= result.totalCount);
        }
    }];
}

```

## <a name="selecting"></a><a name="parameters"></a>Limitación de campos y expansión de los parámetros de cadena de consulta con MSQuery

Para limitar los campos que se devolverán en una consulta, especifique los nombres de los campos en la propiedad **selectFields**. Esto solamente devuelven los campos de texto y aquellos que se hayan rellenado:

```
	query.selectFields = @[@"text", @"completed"];
```

Para incluir parámetros de cadena de consulta adicionales en la solicitud al servidor (por ejemplo, porque los utiliza un script de servidor personalizado), introduzca `query.parameters`:

```
	query.parameters = @{
		@"myKey1" : @"value1",
		@"myKey2" : @"value2",
	};
```

##<a name="inserting"></a>Insertar datos

Para insertar una nueva fila en la tabla, cree un nuevo `NSDictionary` e invoque `table insert`. Servicios móviles genera automáticamente columnas nuevas basadas en `NSDictionary` si [Esquema dinámico] no está deshabilitado.

Si no se proporciona `id`, el backend genera automáticamente un nuevo identificador único. Proporcione su propio `id` para utilizar direcciones de correo electrónico, nombres de usuario o sus propios valores personalizados como Id. Proporcionar su propio ID puede facilitar las combinaciones y la lógica de la base de datos de tipo empresarial.

```
	NSDictionary *newItem = @{@"id": @"custom-id", @"text": @"my new item", @"complete" : @NO};
	[self.table insert:newItem completion:^(NSDictionary *result, NSError *error) {
		// The result contains the new item that was inserted,
		// depending on your server scripts it may have additional or modified
		// data compared to what was passed to the server.
		if(error) {
				NSLog(@"ERROR %@", error);
		} else {
						NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
		}
	}];
```

##<a name="modifying"></a>Modificación de datos

Para actualizar una fila existente, modifique un elemento y llame a `update`:

```
	NSMutableDictionary *newItem = [oldItem mutableCopy]; // oldItem is NSDictionary
	[newItem setValue:@"Updated text" forKey:@"text"];
	[self.table update:newItem completion:^(NSDictionary *item, NSError *error) {
		// Handle error or perform additional logic as needed
	}];
```

Como alternativa, proporcione el identificador de fila y el campo actualizado:

```
	[self.table update:@{@"id":@"37BBF396-11F0-4B39-85C8-B319C729AF6D", @"Complete":@YES} completion:^(NSDictionary *item, NSError *error) {
		// Handle error or perform additional logic as needed
	}];
```

Como mínimo, debe establecerse el atributo `id` al realizar actualizaciones.

##<a name="deleting"></a>Eliminación de datos

Para eliminar un elemento, invoque `delete` con el elemento:

```
	[self.table delete:item completion:^(id itemId, NSError *error) {
		// Handle error or perform additional logic as needed
	}];
```

O bien elimínelo proporcionando un identificador de fila:

```
	[self.table deleteWithId:@"37BBF396-11F0-4B39-85C8-B319C729AF6D" completion:^(id itemId, NSError *error) {
		// Handle error or perform additional logic as needed
	}];
```

Como mínimo, el atributo `id` debe establecerse a la hora de efectuar eliminaciones.

##<a name="#custom-api"></a>Llamada a una API personalizada

Una API personalizada le permite definir extremos personalizados que exponen la funcionalidad del servidor que no se asigna a una operación de inserción, actualización, eliminación o lectura. Al usar una API personalizada, puede tener más control sobre la mensajería, incluida la lectura y el establecimiento de encabezados de mensajes HTTP y la definición del formato del cuerpo de un mensaje diferente de JSON. Para obtener un ejemplo de cómo crear una API personalizada en el servicio móvil, vea [Definición de un punto de conexión de API personalizada](mobile-services-dotnet-backend-define-custom-api.md).

[AZURE.INCLUDE [mobile-services-ios-call-custom-api](../../includes/mobile-services-ios-call-custom-api.md)]


##<a name="authentication"></a>Autenticación de usuarios

Servicios móviles de Azure es compatible con varios proveedores de identidad. Para obtener un tutorial básico, consulte [Autenticación].

Servicios móviles de Azure admite dos flujos de trabajo de autenticación:

- **Inicio de sesión administrado por el servidor**: Servicios móviles de Azure administra el proceso de inicio de sesión en nombre de su aplicación. Muestra una página de inicio de sesión específica del proveedor y se autentica con el proveedor elegido.

- **Inicio de sesión administrado por el cliente**: la _aplicación_ solicita un token al proveedor de identidades y presenta dicho token a Servicios móviles de Azure para su autenticación.

Cuando la autenticación se realice correctamente, obtendrá un objeto de usuario con un valor de identificador de usuario y el token de autenticación. Para utilizar este ID de usuario para autorizar a los usuarios, consulte [Autorización del servicio]. Para restringir el acceso solamente a los usuarios autenticados, consulte [Permisos].

### Inicio de sesión administrado por el servidor

Así es como puede agregar el inicio de sesión administrado por el servidor al proyecto [Inicio rápido de Servicios móviles]; puede utilizar un código similar para los demás proyectos. Para obtener más información y para ver un ejemplo de extremo a extremo en acción, consulte [Autenticación].

[AZURE.INCLUDE [mobile-services-ios-authenticate-app](../../includes/mobile-services-ios-authenticate-app.md)]

### Inicio de sesión administrado por el cliente (inicio de sesión único)

Puede realizar el proceso de inicio de sesión fuera del cliente de Servicios móviles, o bien para activar el inicio de sesión único o si la aplicación se pone en contacto con el proveedor de identidad directamente. En estos casos, para iniciar sesión en Servicios móviles, proporcione un token que haya obtenido con independencia del proveedor de identidades admitido.

En el ejemplo siguiente se usa el [SDK de Live Connect] a fin de habilitar el inicio de sesión único para las aplicaciones iOS. Aquí se asume que tiene una instancia **LiveConnectClient** denominada `liveClient` en el controlador y que el usuario ha iniciado sesión.

```
	[client loginWithProvider:@"microsoftaccount"
		token:@{@"authenticationToken" : self.liveClient.session.authenticationToken}
		completion:^(MSUser *user, NSError *error) {
				// Handle success and errors
	}];
```

##<a name="caching-tokens"></a>Almacenamiento de tokens de autenticación en la memoria caché

Veamos cómo se pueden almacenar en la memoria caché los tokens del proyecto [Inicio rápido de servicios móviles]; puede aplicar pasos similares a cualquier proyecto.[AZURE.INCLUDE [mobile-services-ios-authenticate-app-with-token](../../includes/mobile-services-ios-authenticate-app-with-token.md)]

##<a name="errors"></a>Gestión de errores

Al realizar una llamada a un servicio móvil, el bloque de finalización contiene un parámetro `NSError *error`. En caso de producirse un error, este parámetro no será nulo. En su código, debe marcar este parámetro y administrar el error según sea necesario.

El archivo [`<WindowsAzureMobileServices/MSError.h>`](https://github.com/Azure/azure-mobile-services/blob/master/sdk/iOS/src/MSError.h) define las constantes `MSErrorResponseKey`, `MSErrorRequestKey` y `MSErrorServerItemKey` para obtener más datos relacionados con el error. Además, el archivo define constantes para cada código de error. Para obtener un ejemplo sobre cómo utilizar estas constantes, consulte [Controlador de conflictos] para su uso de `MSErrorServerItemKey` y `MSErrorPreconditionFailed`.

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
[Inicio rápido de Servicios móviles]: mobile-services-ios-get-started.md
[Introducción a Servicios móviles]: mobile-services-ios-get-started.md
[Get started with Mobile Services]: mobile-services-ios-get-started.md
[Mobile Services SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Autenticación]: /develop/mobile/tutorials/get-started-with-users-ios
[SDK de iOS]: https://developer.apple.com/xcode

[Handling Expired Tokens]: http://go.microsoft.com/fwlink/p/?LinkId=301955
[SDK de Live Connect]: http://go.microsoft.com/fwlink/p/?LinkId=301960
[Permisos]: http://msdn.microsoft.com/library/windowsazure/jj193161.aspx
[Autorización del servicio]: mobile-services-javascript-backend-service-side-authorization.md
[Esquema dinámico]: http://go.microsoft.com/fwlink/p/?LinkId=296271
[Crear una tabla]: http://msdn.microsoft.com/library/windowsazure/jj193162.aspx
[NSDictionary object]: http://go.microsoft.com/fwlink/p/?LinkId=301965
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[CLI to manage Mobile Services tables]: ../virtual-machines-command-line-tools.md#Mobile_Tables
[Controlador de conflictos]: mobile-services-ios-handling-conflicts-offline-data.md#add-conflict-handling

<!---HONumber=AcomDC_0121_2016-->