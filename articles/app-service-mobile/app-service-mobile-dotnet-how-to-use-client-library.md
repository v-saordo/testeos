<properties
	pageTitle="Biblioteca de cliente administrada de Aplicaciones móviles del Servicio de aplicaciones (Windows | Xamarin) | Microsoft Azure"
	description="Aprenda a usar un cliente .NET para Aplicaciones móviles del Servicio de aplicaciones de Azure con aplicaciones de Windows y Xamarin."
	services="app-service\mobile"
	documentationCenter=""
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="03/02/2016"
	ms.author="glenga"/>

# Uso del cliente administrado para Aplicaciones móviles de Azure

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

##Información general

En esta guía se muestra cómo realizar escenarios comunes con la biblioteca de cliente administrada para Aplicaciones móviles del Servicio de aplicaciones de Azure para Windows y Xamarin. Si no tiene experiencia en el uso de Aplicaciones móviles, considere primero la opción de completar el tutorial [Guía de inicio rápido de Aplicaciones móviles](app-service-mobile-windows-store-dotnet-get-started.md). En esta guía, nos centramos en el SDK administrado de cliente. Para más información sobre los SDK del lado servidor para aplicaciones móviles, vea [Trabajar con el SDK del back-end de .NET](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) o [Cómo usar el SDK del back-end de Node.js](app-service-mobile-node-backend-how-to-use-server-sdk.md).

## Documentación de referencia

La documentación de referencia para el SDK de cliente se encuentra aquí: [Referencia de cliente de .NET para aplicaciones móviles de Azure](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.aspx).

##<a name="setup"></a>Configuración y requisitos previos

Se supone que ya ha creado y publicado el proyecto de back-end de la aplicación móvil, que se incluye en una tabla. En el código usado en este tema, el nombre de la tabla es `TodoItem` y dispondrá de las siguientes columnas: `Id`, `Text` y `Complete`. Se trata de la misma tabla que se crea cuando se completa el [tutorial rápido](app-service-mobile-windows-store-dotnet-get-started.md).

El tipo del cliente con tipo correspondiente en C# es el siguiente:


	public class TodoItem
	{
		public string Id { get; set; }

		[JsonProperty(PropertyName = "text")]
		public string Text { get; set; }

		[JsonProperty(PropertyName = "complete")]
		public bool Complete { get; set; }
	}

Tenga en cuenta que [JsonPropertyAttribute](http://www.newtonsoft.com/json/help/html/Properties_T_Newtonsoft_Json_JsonPropertyAttribute.htm) se usa para definir la asignación *PropertyName* entre el tipo de cliente y la tabla.

Para información sobre cómo crear tablas nuevas en el back-end de aplicaciones móviles, vea [Cómo definir un controlador de tabla](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#how-to-define-a-table-controller) (back-end de. NET) o [Definir tablas con un esquema dinámico](app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations) (back-end de Node.js). Para un back-end de Node.js, también puede usar la configuración **Tablas fáciles** en el [Portal de Azure](https://portal.azure.com/).

##<a name="create-client"></a>Creación del cliente de aplicación móvil

El código siguiente crea el objeto `MobileServiceClient` que se usa para tener acceso al back-end de la aplicación móvil.

	MobileServiceClient client = new MobileServiceClient("MOBILE_APP_URL");

En el código anterior, reemplace `MOBILE_APP_URL` por la dirección URL del back-end de la aplicación móvil, que se encuentra en la hoja de la aplicación móvil en el [Portal de Azure](https://portal.azure.com/).

##<a name="instantiating"></a>Creación de una referencia de tabla

Todo el código que obtiene acceso a datos o los modifica en una tabla de back-end llama a las funciones del objeto `MobileServiceTable`. Obtenga una referencia a la tabla llamando al método [GetTable](https://msdn.microsoft.com/library/azure/jj554275.aspx) en una instancia de `MobileServiceClient` del modo indicado a continuación:

    IMobileServiceTable<TodoItem> todoTable =
		client.GetTable<TodoItem>();

Este es el modelo de serialización con tipo. También se admite un modelo de serialización sin tipo. El siguiente código crea una referencia a una tabla sin tipo:

	// Get an untyped table reference
	IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");

En las consultas sin tipo, debe especificar la cadena de consulta de OData subyacente.

##<a name="querying"></a>Consulta de datos desde la aplicación móvil

En esta sección se describe cómo generar consultas al back-end de la aplicación móvil, lo cual incluye la siguiente funcionalidad:

- [Filtro de datos devueltos]
- [Ordenar datos devueltos]
- [Devolver datos en páginas]
- [Seleccionar columnas específicas]
- [Búsqueda de datos por identificador]

>[AZURE.NOTE] Se aplica el tamaño de página del servidor para evitar que se devuelvan todas las filas. De esta forma, se evita que las consultas predeterminadas de los conjuntos de datos de gran tamaño incidan negativamente en el servicio. Para devolver más de 50 filas, use el método `Take` como se describe en [Devolución de datos en páginas].

### <a name="filtering"></a>Filtro de datos devueltos

El siguiente código muestra cómo filtrar los datos incluyendo una cláusula `Where` en una consulta. Devuelve todos los elementos de `todoTable` cuya propiedad `Complete` es igual a `false`. La función `Where` aplica un predicado de filtrado de filas a la consulta en la tabla.

	// This query filters out completed TodoItems and
	// items without a timestamp.
	List<TodoItem> items = await todoTable
	   .Where(todoItem => todoItem.Complete == false)
	   .ToListAsync();

Puede ver el identificador URI de la solicitud que se ha enviado al back-end mediante el software de inspección de mensajes, como las herramientas para desarrolladores del explorador o [Fiddler]. Si consulta el URI de solicitud siguiente, tenga en cuenta que se está modificando la propia cadena de consulta:

	GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1

Normalmente, esta solicitud se traduciría de forma aproximada en la siguiente consulta SQL en Azure:

	SELECT *
	FROM TodoItem
	WHERE ISNULL(complete, 0) = 0

La función que se pasa al método `Where` puede disponer de un número arbitrario de condiciones. Por ejemplo, la siguiente línea:

	// This query filters out completed TodoItems where Text isn't null
	List<TodoItem> items = await todoTable
	   .Where(todoItem => todoItem.Complete == false
		   && todoItem.Text != null)
	   .ToListAsync();

Se traduciría aproximadamente (para la misma solicitud mostrada anteriormente) de la siguiente forma:

	SELECT *
	FROM TodoItem
	WHERE ISNULL(complete, 0) = 0
	      AND ISNULL(text, 0) = 0

La instrucción `where` anterior buscará los elementos con el estado `Complete` establecido en false con no nulo `Text`.

También podría haberse escrito en varias líneas:

	List<TodoItem> items = await todoTable
	   .Where(todoItem => todoItem.Complete == false)
	   .Where(todoItem => todoItem.Text != null)
	   .ToListAsync();

Los dos métodos son equivalentes y pueden usarse indistintamente. La opción anterior (de concatenación de varios predicados en una consulta) es más compacta y es la que se recomienda.

La cláusula `where` admite las operaciones que pueden traducirse en el subconjunto OData. Incluye operadores relacionales (==, !=, <, <=, >, >=), operadores aritméticos (+, -, /, *, %), precisión numérica (Math.Floor, Math.Ceiling), funciones de cadena (Length, Substring, Replace, IndexOf, StartsWith, EndsWith), propiedades de fecha (Year, Month, Day, Hour, Minute, Second), propiedades de acceso de un objeto y expresiones que combinan todas las opciones anteriores.

### <a name="sorting"></a>Clasificación de datos devueltos

El siguiente código muestra cómo ordenar datos incluyendo una función `OrderBy` u `OrderByDescending` en la consulta. Devuelve los elementos de ordenados `todoTable` de manera ascendente por el campo `Text`.

	// Sort items in ascending order by Text field
	MobileServiceTableQuery<TodoItem> query = todoTable
					.OrderBy(todoItem => todoItem.Text)
 	List<TodoItem> items = await query.ToListAsync();

	// Sort items in descending order by Text field
	MobileServiceTableQuery<TodoItem> query = todoTable
					.OrderByDescending(todoItem => todoItem.Text)
 	List<TodoItem> items = await query.ToListAsync();

### <a name="paging"></a>Devolución de datos en páginas

De manera predeterminada, el back-end devuelve solo las primeras 50 filas. Aumente el número de filas devueltas llamando al método [Take]. Use `Take` junto con el método [Skip] para solicitar una "página" específica del conjunto de datos total devuelto por la consulta. Cuando se ejecuta la siguiente consulta, se devuelven los tres primeros elementos de la tabla.

	// Define a filtered query that returns the top 3 items.
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Take(3);
	List<TodoItem> items = await query.ToListAsync();

La siguiente consulta revisada omite los tres primeros resultados y devuelve los tres siguientes. Esa es realmente la segunda página de datos en la que el tamaño de página cuenta con tres elementos.

	// Define a filtered query that skips the top 3 items and returns the next 3 items.
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Skip(3)
					.Take(3);
	List<TodoItem> items = await query.ToListAsync();

También puede usar el método [IncludeTotalCount] para asegurarse de que la consulta obtendrá el recuento total de <i>todos</i> los registros que deberían devolverse, ignorando cualquier cláusula de limitación/paginación especificada:

	query = query.IncludeTotalCount();

Este es un escenario simplificado para pasar valores de paginación codificados de forma rígida a los métodos `Take` y `Skip`. En una aplicación en tiempo real, puede usar consultas similares a las anteriores con un control de paginación o interfaz de usuario comparable para permitir a los usuarios desplazarse a las páginas anteriores y posteriores.

>[AZURE.NOTE]Para reemplazar el límite de 50 filas de un back-end de la aplicación móvil, también debe aplicar [EnableQueryAttribute](https://msdn.microsoft.com/library/system.web.http.odata.enablequeryattribute.aspx) al método público GET y especificar el comportamiento de paginación. Cuando se aplica al método, lo siguiente establece el máximo de filas devueltas a 1000:

    [EnableQuery(MaxTop=1000)]


### <a name="selecting"></a>Selección de columnas específicas

Puede especificar qué conjunto de propiedades se incluirá en los resultados agregando una cláusula `Select` a su consulta. Por ejemplo, el siguiente código muestra cómo seleccionar solo un campo y también cómo seleccionar varios campos y darles formato:

	// Select one field -- just the Text
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Select(todoItem => todoItem.Text);
	List<string> items = await query.ToListAsync();

	// Select multiple fields -- both Complete and Text info
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Select(todoItem => string.Format("{0} -- {1}",
						todoItem.Text.PadRight(30), todoItem.Complete ?
						"Now complete!" : "Incomplete!"));
	List<string> items = await query.ToListAsync();

Todas las funciones descritas en adelante son adicionales, por lo que es posible seguir llamándolas y cada vez irá afectando más a la consulta. A continuación se muestra un ejemplo más:

	MobileServiceTableQuery<TodoItem> query = todoTable
					.Where(todoItem => todoItem.Complete == false)
					.Select(todoItem => todoItem.Text)
					.Skip(3).
					.Take(3);
	List<string> items = await query.ToListAsync();

### <a name="lookingup"></a>Búsqueda de datos por identificador

La función `LookupAsync` puede usarse para buscar objetos desde la base de datos con un identificador determinado.

	// This query filters out the item with the ID of 37BBF396-11F0-4B39-85C8-B319C729AF6D
	TodoItem item = await todoTable.LookupAsync("37BBF396-11F0-4B39-85C8-B319C729AF6D");

### <a name="lookingup"></a>Ejecución de consultas sin tipo

Al ejecutar una consulta mediante un objeto de tabla sin tipo, debe especificar explícitamente la cadena de consulta de OData, como en el ejemplo siguiente:

	// Lookup untyped data using OData
	JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");

Vuelva a obtener valores JSON que puede usar como un contenedor de propiedades. Para obtener más información sobre JToken y [Json.NET](http://json.codeplex.com/), consulte Json.NET.

##<a name="inserting"></a>Inserción de datos en el back-end de una aplicación móvil

Todos los tipos de cliente deben incluir un miembro llamado **Id**, que es una cadena de forma predeterminada. Este elemento **Id** es necesario para realizar operaciones CRUD y para trabajar sin conexión. El siguiente código muestra cómo insertar filas nuevas en una tabla. El parámetro contiene los datos que se van a insertar como un objeto .NET.

	await todoTable.InsertAsync(todoItem);

Si no se incluye un valor de identificador personalizado exclusivo en `todoItem` pasado a la llamada `todoTable.InsertAsync`, el servidor establecido en el objeto `todoItem` devuelto al cliente generará un valor para el identificador.

Para insertar datos sin tipo, puede aprovechar Json.NET como se muestra a continuación.

	JObject jo = new JObject();
	jo.Add("Text", "Hello World");
	jo.Add("Complete", false);
	var inserted = await table.InsertAsync(jo);

A continuación, se muestra un ejemplo en el que se usa una dirección de correo electrónico como un identificador de cadena exclusivo.

	JObject jo = new JObject();
	jo.Add("id", "myemail@emaildomain.com");
	jo.Add("Text", "Hello World");
	jo.Add("Complete", false);
	var inserted = await table.InsertAsync(jo);


###Trabajar con valores de Id.

El servicio Aplicaciones móviles admite valores de cadena personalizados únicos para la columna **id** de la tabla. Esto permite a las aplicaciones usar valores personalizados como direcciones de correo electrónico o nombres de usuario para el Id.

Los identificadores de cadena proporcionan las siguientes ventajas:

+ Se generan identificadores sin realizar una vuelta a la base de datos.
+ Los registros son más fáciles de fusionar desde diferentes tablas o bases de datos.
+ Los valores de los identificadores pueden integrarse mejor con una lógica de aplicación.

Cuando no se establece un valor de identificador de cadena en un registro insertado, el back-end de la aplicación móvil genera un valor único para el identificador. Puede usar el método `Guid.NewGuid()` para generar sus propios valores de ID, ya sea en el cliente o en el back-end.

##<a name="modifying"></a>Modificación de datos en el back-end de una aplicación móvil

El siguiente código muestra cómo actualizar una instancia existente con el mismo identificador con nueva información. El parámetro contiene los datos que se van a actualizar como un objeto .NET.

	await todoTable.UpdateAsync(todoItem);

Para insertar datos sin tipo, puede aprovechar Json.NET como sigue:
	JObject jo = new JObject();
	jo.Add("Id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
	jo.Add("Text", "Hello World");
	jo.Add("Complete", false);
	var inserted = await table.UpdateAsync(jo);

Tenga en cuenta que al realizar una actualización, debe especificarse un identificador. Así es cómo el back-end identifica qué instancia actualizar. El identificador puede obtenerse a partir del resultado de la llamada `InsertAsync`. Cuando intenta actualizar un elemento sin proporcionar el valor "Id", se genera una excepción `ArgumentException`.


##<a name="deleting"></a>Eliminación de datos del back-end de una aplicación móvil

El siguiente código muestra cómo eliminar una instancia existente. La instancia se identifica mediante el campo "Id" establecido en `todoItem`.

	await todoTable.DeleteAsync(todoItem);

Para eliminar datos sin tipo, puede aprovechar Json.NET de la siguiente manera:

	JObject jo = new JObject();
	jo.Add("Id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
	await table.DeleteAsync(jo);

Tenga en cuenta que al realizar una solicitud de eliminación, debe especificarse un identificador. Otras propiedades no se pasan al servicio o se omiten en este. El resultado de una llamada `DeleteAsync` normalmente es `null`. El identificador puede obtenerse a partir del resultado de la llamada `InsertAsync`. Al intentar eliminar un elemento sin el campo "Id" ya establecido, se devuelve `MobileServiceInvalidOperationException` desde el back-end.

##<a name="#custom-api"></a>Llamada a una API personalizada

Una API personalizada le permite definir extremos personalizados que exponen la funcionalidad del servidor que no se asigna a una operación de inserción, actualización, eliminación o lectura. Al usar una API personalizada, puede tener más control sobre la mensajería, incluida la lectura y el establecimiento de encabezados de mensajes HTTP y la definición del formato del cuerpo de un mensaje diferente de JSON.

Puede llamar a una API personalizada al llamar a una de las sobrecargas del método [InvokeApiAsync] en el cliente. Por ejemplo, la siguiente línea de código envía una solicitud POST a la API **completeAll** en el back-end:

    var result = await App.MobileService
        .InvokeApiAsync<MarkAllResult>("completeAll",
        System.Net.Http.HttpMethod.Post, null);

Tenga en cuenta que se trata de una llamada de método con tipo, lo que requiere la definición del tipo de valor devuelto **MarkAllResult**. Se admiten métodos con y sin tipos. Este ejemplo es casi insignificante, ya que tiene tipo, no envía ninguna carga, no tiene parámetros de consulta y no cambia los encabezados de solicitud. Para obtener ejemplos más realistas y un análisis más completo de [InvokeApiAsync], consulte [API personalizada en los SDK del cliente de Servicios móviles de Azure].

##Registrar notificaciones de inserción

El cliente de Aplicaciones móviles permite registrar las notificaciones push con Centros de notificaciones de Azure. Al registrar, se obtiene un identificador del servicio de notificaciones push (PNS) específico de la plataforma. A continuación, proporcione este valor junto con las etiquetas cuando se cree el registro. El código siguiente registra la aplicación de Windows para las notificaciones push en el Servicio de notificaciones de Windows.(WNS):

		private async void InitNotificationsAsync()
		{
		    // Request a push notification channel.
		    var channel =
		        await PushNotificationChannelManager
		            .CreatePushNotificationChannelForApplicationAsync();

		    // Register for notifications using the new channel and a tag collection.
			var tags = new List<string>{ "mytag1", "mytag2"};
		    await MobileService.GetPush().RegisterNativeAsync(channel.Uri, tags);
		}

Tenga en cuenta que en este ejemplo se incluyen dos etiquetas en el registro. Para más información sobre las aplicaciones de Windows, incluyendo cómo registrarse para los registros de plantillas, vea [Agregar notificaciones de inserción a la aplicación](app-service-mobile-windows-store-dotnet-get-started-push.md).

Las aplicaciones de Xamarin requieren código adicional para poder registrar una aplicación que se ejecute en la aplicación iOS o Android con el Servicio de notificaciones push de Apple (APNS) y el Servicio de mensajería en la nube de Google (GCM), respectivamente. Para más información, vea **Agregar notificaciones de inserción a la aplicación** ([Xamarin.iOS](partner-xamarin-mobile-services-ios-get-started-push.md#add-push) | [Xamarin.Android](partner-xamarin-mobile-services-android-get-started-push.md#add-push)).

## Registro de plantillas de inserción para enviar notificaciones entre plataformas

Para registrar plantillas, basta con pasar las plantillas con el método **MobileService.GetPush(). RegisterAsync()** en la aplicación cliente.

        MobileService.GetPush().RegisterAsync(channel.Uri, newTemplates());

Las plantillas serán de tipo JObject y pueden contener varias plantillas con el formato JSON siguiente:

        public JObject newTemplates()
        {
            // single template for Windows Notification Service toast
            var template = "<toast><visual><binding template="ToastText01"><text id="1">$(message)</text></binding></visual></toast>";

            var templates = new JObject
            {
                ["generic-message"] = new JObject
                {
                    ["body"] = template,
                    ["headers"] = new JObject
                    {
                        ["X-WNS-Type"] = "wns/toast"
                    },
                    ["tags"] = new JArray()
                },
                ["more-templates"] = new JObject {...}
            };
            return templates;
        }

El método **RegisterAsync()** también acepta iconos secundarios:

        MobileService.GetPush().RegisterAsync(string channelUri, JObject templates, JObject secondaryTiles);

Tenga en cuenta que se eliminará todas las etiquetas por seguridad. Para agregar etiquetas a las instalaciones o las plantillas dentro de las instalaciones, vea [Trabajar con el SDK del servidor back-end de .NET para Aplicaciones móviles de Azure].

Para enviar notificaciones mediante estas plantillas registradas, trabaje con [API de Centros de notificaciones](https://msdn.microsoft.com/library/azure/dn495101.aspx).

##<a name="optimisticconcurrency"></a>Uso de la simultaneidad optimista

En algunos casos, dos o más clientes pueden escribir cambios en el mismo elemento y al mismo tiempo. Si no se produjera la detección de conflictos, la última escritura sobrescribiría cualquier actualización anterior incluso si no fuese el resultado deseado. El *control de simultaneidad optimista* asume que cada transacción puede confirmarse y, por lo tanto, no usa ningún bloqueo de recursos. Antes de confirmar una transacción, el control de simultaneidad optimista comprueba que ninguna otra transacción haya modificado los datos. Si los datos se han modificado, la transacción de confirmación se desecha.

El servicio Aplicaciones móviles es compatible con el control de simultaneidad optimista gracias al seguimiento de cambios en cada elemento mediante la columna de propiedades del sistema `__version` que se definió en cada tabla en el back-end de la aplicación móvil. Cada vez que se actualiza un registro, el servicio Aplicaciones móviles establece la propiedad `__version` de ese registro en un nuevo valor. Durante cada solicitud de actualización, la propiedad `__version` del registro incluido con la solicitud se compara con la misma propiedad del registro en el servidor. Si la versión que pasa con la solicitud no coincide con el back-end, la biblioteca de cliente genera `MobileServicePreconditionFailedException<T>`. El tipo incluido con la excepción es el registro del back-end que contiene la versión del registro del servidor. A continuación, la aplicación puede usar esta información para decidir si ejecutar la solicitud de actualización de nuevo con el valor `__version` correcto del back-end para confirmar los cambios.

Para habilitar la simultaneidad optimista, la aplicación define una columna en la clase de tabla para la propiedad del sistema `__version`. La siguiente definición proporciona un ejemplo.

    public class TodoItem
    {
        public string Id { get; set; }

        [JsonProperty(PropertyName = "text")]
        public string Text { get; set; }

        [JsonProperty(PropertyName = "complete")]
        public bool Complete { get; set; }

		// *** Enable Optimistic Concurrency *** //
        [JsonProperty(PropertyName = "__version")]
        public byte[] Version { set; get; }
    }


Las aplicaciones con tablas sin tipo permiten la simultaneidad optimista mediante el establecimiento de la marca `Version` en `SystemProperties` de la tabla de la siguiente forma.

	//Enable optimistic concurrency by retrieving __version
	todoTable.SystemProperties |= MobileServiceSystemProperties.Version;


El siguiente código muestra cómo resolver un conflicto de escritura detectado. El valor `__version` correcto debe incluirse en la llamada `UpdateAsync()` para confirmar una resolución.

	private async void UpdateToDoItem(TodoItem item)
	{
    	MobileServicePreconditionFailedException<TodoItem> exception = null;

	    try
    	{
	        //update at the remote table
    	    await todoTable.UpdateAsync(item);
    	}
    	catch (MobileServicePreconditionFailedException<TodoItem> writeException)
	    {
        	exception = writeException;
	    }

    	if (exception != null)
    	{
			// Conflict detected, the item has changed since the last query
        	// Resolve the conflict between the local and server item
	        await ResolveConflict(item, exception.Item);
    	}
	}


	private async Task ResolveConflict(TodoItem localItem, TodoItem serverItem)
	{
    	//Ask user to choose the resoltion between versions
	    MessageDialog msgDialog = new MessageDialog(String.Format("Server Text: "{0}" \nLocal Text: "{1}"\n",
        	                                        serverItem.Text, localItem.Text),
                                                	"CONFLICT DETECTED - Select a resolution:");

	    UICommand localBtn = new UICommand("Commit Local Text");
    	UICommand ServerBtn = new UICommand("Leave Server Text");
    	msgDialog.Commands.Add(localBtn);
	    msgDialog.Commands.Add(ServerBtn);

    	localBtn.Invoked = async (IUICommand command) =>
	    {
        	// To resolve the conflict, update the version of the
	        // item being committed. Otherwise, you will keep
        	// catching a MobileServicePreConditionFailedException.
	        localItem.Version = serverItem.Version;

    	    // Updating recursively here just in case another
        	// change happened while the user was making a decision
	        UpdateToDoItem(localItem);
    	};

	    ServerBtn.Invoked = async (IUICommand command) =>
    	{
	        RefreshTodoItems();
    	};

	    await msgDialog.ShowAsync();
	}

Para más información, vea [Sincronización de datos sin conexión en Aplicaciones móviles de Azure](app-service-mobile-offline-data-sync.md).


##<a name="binding"></a>Enlace de datos de Aplicaciones móviles a una interfaz de usuario de Windows

En esta sección se describe cómo mostrar objetos de datos devueltos mediante elementos de la interfaz de usuario en una aplicación Windows. Para consultar elementos incompletos en `todoTable` y visualizarlos en una lista muy sencilla, puede ejecutar el siguiente código de ejemplo para enlazar el origen de la lista con una consulta. El uso de `MobileServiceCollection` crea una colección de enlaces para Aplicaciones móviles.

	// This query filters out completed TodoItems.
	MobileServiceCollection<TodoItem, TodoItem> items = await todoTable
		.Where(todoItem => todoItem.Complete == false)
		.ToCollectionAsync();

	// itemsControl is an IEnumerable that could be bound to a UI list control
	IEnumerable itemsControl  = items;

	// Bind this to a ListBox
	ListBox lb = new ListBox();
	lb.ItemsSource = items;

Algunos controles en tiempo de ejecución administrado admiten una interfaz denominada [ISupportIncrementalLoading](http://msdn.microsoft.com/library/windows/apps/Hh701916). Esta interfaz permite a los controles solicitar datos adicionales cuando el usuario se desplaza. Existe compatibilidad integrada de esta interfaz para las aplicaciones universales de Windows 8.1 a través de `MobileServiceIncrementalLoadingCollection`, que administra automáticamente las llamadas desde los controles. Para usar `MobileServiceIncrementalLoadingCollection` en las aplicaciones de Windows, realice las siguientes acciones:

			MobileServiceIncrementalLoadingCollection<TodoItem,TodoItem> items;
		items =  todoTable.Where(todoItem => todoItem.Complete == false)
					.ToIncrementalLoadingCollection();

		ListBox lb = new ListBox();
		lb.ItemsSource = items;


Para usar la nueva colección en aplicaciones de Windows Phone 8 y "Silverlight", use los métodos de extensión `ToCollection` en `IMobileServiceTableQuery<T>` y `IMobileServiceTable<T>`. Para cargar datos realmente, llame a `LoadMoreItemsAsync()`.

	MobileServiceCollection<TodoItem, TodoItem> items = todoTable.Where(todoItem => todoItem.Complete==false).ToCollection();
	await items.LoadMoreItemsAsync();

Cuando use la colección creada mediante la llamada a `ToCollectionAsync` o `ToCollection`, obtendrá una colección que puede enlazarse a los controles de la interfaz de usuario. Esta colección es para la paginación, es decir, un control puede solicitar a la colección cargar más elementos y la colección lo hará para el control. En este momento, no hay ningún código de usuario implicado y el control iniciará el flujo. Sin embargo, puesto que la colección está cargando datos desde la red, cabe esperar que a veces se produzca un error en la carga. Para gestionar esos errores, puede reemplazar el método `OnException` de `MobileServiceIncrementalLoadingCollection` para gestionar excepciones resultantes de las llamadas a `LoadMoreItemsAsync` que han realizado los controles.

Para finalizar, imagine que la tabla contiene muchos campos, pero solo desea que se muestren algunos en el control. Puede usar la guía de la sección "[Seleccionar columnas específicas](#selecting)" anterior para seleccionar las columnas concretas que se mostrarán en la interfaz de usuario.

## <a name="adal"></a>Autenticación de usuarios con la biblioteca de autenticación de Active Directory

Puede utilizar la biblioteca de autenticación de Active Directory (ADAL) para iniciar la sesión de los usuarios en su aplicación con Azure Active Directory. Con frecuencia, esta opción es preferible al uso de los métodos `loginAsync()`, ya que proporciona una experiencia UX más nativa y permite personalizaciones adicionales.

1. Configure su back-end de aplicación móvil para el inicio de sesión en AAD siguiendo el tutorial [Configuración de la aplicación del Servicio de aplicaciones para usar el inicio de sesión de Azure Active Directory](app-service-mobile-how-to-configure-active-directory-authentication.md). Asegúrese de completar el paso opcional de registrar una aplicación cliente nativa.

2. En Visual Studio o Xamarin Studio, abra el proyecto y agregue una referencia al paquete NuGet `Microsoft.IdentityModel.CLients.ActiveDirectory`. Al buscar, incluya las versiones preliminares.

3. Agregue el siguiente código a la aplicación, según la plataforma que utilice. En cada caso, realice las sustituciones siguientes:

* Reemplace **INSERT-AUTHORITY-HERE** por el nombre del inquilino en el que aprovisionó la aplicación. El formato debe ser https://login.windows.net/contoso.onmicrosoft.com. Este valor se puede copiar de la pestaña Dominio de Azure Active Directory en el [Portal de Azure clásico].

* Reemplace **INSERT-RESOURCE-ID-HERE** por el id. de cliente del back-end de la aplicación móvil. Puede obtenerlo en la pestaña **Avanzadas** en **Configuración de Azure Active Directory** en el portal.

* Reemplace **INSERT-CLIENT-ID-HERE** por el id. de cliente que copió de la aplicación cliente nativa.

* Reemplace **INSERT-REDIRECT-URI-HERE** por el punto de conexión _/.auth/login/done_ del sitio, mediante el esquema HTTPS. Este valor debería ser similar a \__https://contoso.azurewebsites.net/.auth/login/done_.

El código necesario para cada plataforma es el siguiente:

**Windows:**

        private MobileServiceUser user;
        private async Task AuthenticateAsync()
        {
            string authority = "INSERT-AUTHORITY-HERE";
            string resourceId = "INSERT-RESOURCE-ID-HERE";
            string clientId = "INSERT-CLIENT-ID-HERE";
            string redirectUri = "INSERT-REDIRECT-URI-HERE";
            while (user == null)
            {
                string message;
                try
                {
                  AuthenticationContext ac = new AuthenticationContext(authority);
                  AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId, new Uri(redirectUri), new PlatformParameters(PromptBehavior.Auto, false) );
                  JObject payload = new JObject();
                  payload["access_token"] = ar.AccessToken;
                  user = await App.MobileService.LoginAsync(MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
                  message = string.Format("You are now logged in - {0}", user.UserId);
                }
                catch (InvalidOperationException)
                {
                  message = "You must log in. Login Required";
                }
                var dialog = new MessageDialog(message);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }

**Xamarin.iOS**

        private MobileServiceUser user;
        private async Task AuthenticateAsync(UIViewController view)
        {
            string authority = "INSERT-AUTHORITY-HERE";
            string resourceId = "INSERT-RESOURCE-ID-HERE";
            string clientId = "INSERT-CLIENT-ID-HERE";
            string redirectUri = "INSERT-REDIRECT-URI-HERE";
            try
			{
				AuthenticationContext ac = new AuthenticationContext(authority);
				AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId, new Uri(redirectUri), new PlatformParameters(view));
				JObject payload = new JObject();
				payload["access_token"] = ar.AccessToken;
				user = await client.LoginAsync(MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
			}
			catch (Exception ex)
			{
				Console.Error.WriteLine(@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
			}
        }

**Xamarin.Android**

        private MobileServiceUser user;
        private async Task AuthenticateAsync()
        {
            string authority = "INSERT-AUTHORITY-HERE";
            string resourceId = "INSERT-RESOURCE-ID-HERE";
            string clientId = "INSERT-CLIENT-ID-HERE";
            string redirectUri = "INSERT-REDIRECT-URI-HERE";
            try
			{
				AuthenticationContext ac = new AuthenticationContext(authority);
				AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId, new Uri(redirectUri), new PlatformParameters(this));
				JObject payload = new JObject();
				payload["access_token"] = ar.AccessToken;
				user = await client.LoginAsync(MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
			}
			catch (Exception ex)
			{
				AlertDialog.Builder builder = new AlertDialog.Builder(this);
				builder.SetMessage(ex.Message);
				builder.SetTitle("You must log in. Login Required");
				builder.Create().Show();
			}
        }
		protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
		{
			base.OnActivityResult(requestCode, resultCode, data);
			AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);
		}

## <a name="package-sid"></a>Obtención del SID de un paquete de la Tienda Windows.

En aplicaciones de Windows, se necesita un SID de paquete para habilitar las notificaciones push. Para obtener este valor:

1. En el Explorador de soluciones de Visual Studio, haga clic con el botón secundario en el proyecto de la aplicación de la Tienda Windows y luego haga clic en **Tienda** > **Asociar aplicación con la Tienda...**.
2. En el asistente, haga clic en **Siguiente**, inicie sesión con su cuenta Microsoft, escriba un nombre para la aplicación en **Reserve un nuevo nombre de aplicación** y luego haga clic en **Reservar**.
3. Después de crear correctamente el registro de la aplicación, seleccione el nuevo nombre de la aplicación, haga clic en **Siguiente** y después en **Asociar**. Se agrega la información de registro necesaria de la Tienda Windows al manifiesto de aplicación.
4. Inicie sesión en el [Centro de desarrollo de Windows](https://dev.windows.com/es-ES/overview) con su cuenta Microsoft. En **Mis aplicaciones**, haga clic en el registro de la aplicación que acaba de crear.
5. Haga clic en **Administración de aplicaciones** > **Identidad de aplicación**, y después desplácese hacia abajo hasta encontrar el **SID del paquete**.

Muchos usos del SID del paquete lo tratan como un URI, en cuyo caso debe usar _ms-app://_ como el esquema. Tome nota de la versión del SID del paquete que se forma concatenando este valor como prefijo.

<!--- We want to just point to the authentication topic when it's done
##<a name="authentication"></a>How to: Authenticate users

Mobile Apps supports authenticating and authorizing app users using a variety of external identity providers: Facebook, Google, Microsoft Account, Twitter, and Azure Active Directory. You can set permissions on tables to restrict access for specific operations to only authenticated users. You can also use the identity of authenticated users to implement authorization rules in server scripts. For more information, see the tutorial [Add authentication to your app].

Two authentication flows are supported: a _server flow_ and a _client flow_. The server flow provides the simplest authentication experience, as it relies on the provider's web authentication interface. The client flow allows for deeper integration with device-specific capabilities as it relies on provider-specific device-specific SDKs.

###Server flow
To have App Service manage the authentication process in your Windows apps,
you must register your app with your identity provider. Then in your mobile App backed, you need to configure the application ID and secret provided by your provider. For more information, see the tutorial [Add authentication to your app].

Once you have registered your identity provider, simply call the [LoginAsync method] with the [MobileServiceAuthenticationProvider] value of your provider. For example, the following code initiates a server flow sign-in by using Facebook.

	private MobileServiceUser user;
	private async System.Threading.Tasks.Task Authenticate()
	{
		while (user == null)
		{
			string message;
			try
			{
				user = await client
					.LoginAsync(MobileServiceAuthenticationProvider.Facebook);
				message =
					string.Format("You are now logged in - {0}", user.UserId);
			}
			catch (InvalidOperationException)
			{
				message = "You must log in. Login Required";
			}

			var dialog = new MessageDialog(message);
			dialog.Commands.Add(new UICommand("OK"));
			await dialog.ShowAsync();
		}
	}

If you are using an identity provider other than Facebook, change the value of [MobileServiceAuthenticationProvider] above to the value for your provider.

In this case, App Service manages the OAuth 2.0 authentication flow by displaying the sign-in page of the selected provider and generating an App Service authentication token after successful sign-on with the identity provider. The [LoginAsync method] returns a [MobileServiceUser], which provides both the [userId] of the authenticated user and the [MobileServiceAuthenticationToken], as a JSON web token (JWT). This token can be cached and re-used until it expires. For more information, see [Caching the authentication token].

###Client flow

Your app can also independently contact the identity provider and then provide the returned token to App Service for authentication. This client flow enables you to provide a single sign-in experience for users or to retrieve additional user data from the identity provider.

####Single sign-in using a token from Facebook or Google

In the most simplified form, you can use the client flow as shown in this snippet for Facebook or Google.

	var token = new JObject();
	// Replace access_token_value with actual value of your access token obtained
	// using the Facebook or Google SDK.
	token.Add("access_token", "access_token_value");

	private MobileServiceUser user;
	private async System.Threading.Tasks.Task Authenticate()
	{
		while (user == null)
		{
			string message;
			try
			{
				// Change MobileServiceAuthenticationProvider.Facebook
				// to MobileServiceAuthenticationProvider.Google if using Google auth.
				user = await client
					.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
				message =
					string.Format("You are now logged in - {0}", user.UserId);
			}
			catch (InvalidOperationException)
			{
				message = "You must log in. Login Required";
			}

			var dialog = new MessageDialog(message);
			dialog.Commands.Add(new UICommand("OK"));
			await dialog.ShowAsync();
		}
	}


####Single sign-in using Microsoft Account with the Live SDK

To be able to authenticate users, you must register your app at the Microsoft account Developer Center. You must then connect this registration with your Mobile App backend. Complete the steps in [Register your app to use a Microsoft account login](app-service-mobile-how-to-configure-microsoft-authentication.md) to create a Microsoft account registration and connect it to your Mobile App backend. If you have both Windows Store and Windows Phone 8/Silverlight versions of your app, register the Windows Store version first.

The following code authenticates using Live SDK and uses the returned token to sign-in to your Mobile App backend.

	private LiveConnectSession session;
 	//private static string clientId = "<microsoft-account-client-id>";
    private async System.Threading.Tasks.Task AuthenticateAsync()
    {

        // Get the URL the Mobile App backend.
        var serviceUrl = App.MobileService.ApplicationUri.AbsoluteUri;

        // Create the authentication client for Windows Store using the service URL.
        LiveAuthClient liveIdClient = new LiveAuthClient(serviceUrl);
        //// Create the authentication client for Windows Phone using the client ID of the registration.
        //LiveAuthClient liveIdClient = new LiveAuthClient(clientId);

        while (session == null)
        {
            // Request the authentication token from the Live authentication service.
			// The wl.basic scope is requested.
            LiveLoginResult result = await liveIdClient.LoginAsync(new string[] { "wl.basic" });
            if (result.Status == LiveConnectSessionStatus.Connected)
            {
                session = result.Session;

                // Get information about the logged-in user.
                LiveConnectClient client = new LiveConnectClient(session);
                LiveOperationResult meResult = await client.GetAsync("me");

                // Use the Microsoft account auth token to sign in to App Service.
                MobileServiceUser loginResult = await App.MobileService
                    .LoginWithMicrosoftAccountAsync(result.Session.AuthenticationToken);

                // Display a personalized sign-in greeting.
                string title = string.Format("Welcome {0}!", meResult.Result["first_name"]);
                var message = string.Format("You are now logged in - {0}", loginResult.UserId);
                var dialog = new MessageDialog(message, title);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
            else
            {
                session = null;
                var dialog = new MessageDialog("You must log in.", "Login Required");
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }
    }


###<a name="caching"></a>Caching the authentication token
In some cases, the call to the login method can be avoided after the first time the user authenticates. You can use [PasswordVault] for Windows Store apps to cache the current user identity the first time they log in and every subsequent time you check whether you already have the user identity in our cache. When the cache is empty, you still need to send the user through the login process.

	// After logging in
	PasswordVault vault = new PasswordVault();
	vault.Add(new PasswordCredential("Facebook", user.UserId, user.MobileServiceAuthenticationToken));

	// Log in
	var creds = vault.FindAllByResource("Facebook").FirstOrDefault();
	if (creds != null)
	{
		user = new MobileServiceUser(creds.UserName);
		user.MobileServiceAuthenticationToken = vault.Retrieve("Facebook", creds.UserName).Password;
	}
	else
	{
		// Regular login flow
		user = new MobileServiceuser( await client
			.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
		var token = new JObject();
		// Replace access_token_value with actual value of your access token
		token.Add("access_token", "access_token_value");
	}

	 // Log out
	client.Logout();
	vault.Remove(vault.Retrieve("Facebook", user.UserId));


For Windows Phone apps, you may encrypt and cache data using the [ProtectedData] class and store sensitive information in isolated storage.

-->

##<a name="errors"></a>Gestión de errores

En el ejemplo siguiente se muestra cómo controlar una excepción devuelta por el back-end:

	private async void InsertTodoItem(TodoItem todoItem)
	{
		// This code inserts a new TodoItem into the database. When the operation completes
		// and App Service has assigned an Id, the item is added to the CollectionView
		try
		{
			await todoTable.InsertAsync(todoItem);
			items.Add(todoItem);
		}
		catch (MobileServiceInvalidOperationException e)
		{
			// Handle error
		}
	}


##<a name="unit-testing"></a>Diseño de pruebas unitarias

El valor devuelto por `MobileServiceClient.GetTable` y las consultas son interfaces. Esto hace que puedan crearse bocetos fácilmente para propósitos de prueba, por lo que puede crear `MyMockTable : IMobileServiceTable<TodoItem>` que implementa la lógica de prueba.

##<a name="customizing"></a>Personalización del cliente

Esta sección muestra formas en que puede personalizar los encabezados de solicitud y personalizar la serialización de objetos JSON en la respuesta.

### <a name="headers"></a>Personalización de encabezados de solicitud

Para admitir su escenario de aplicación específico, deberá personalizar la comunicación con el back-end de la aplicación móvil. Por ejemplo, es posible que desee agregar un encabezado personalizado a cada solicitud saliente o cambiar los códigos de estado de las respuestas. Para ello, proporcione un valor [DelegatingHandler] personalizado, como en el ejemplo siguiente:

    public async Task CallClientWithHandler()
    {
        MobileServiceClient client = new MobileServiceClient(
            "AppUrl", "", "",
            new MyHandler()
            );
        IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();
        var newItem = new TodoItem { Text = "Hello world", Complete = false };
        await todoTable.InsertAsync(newItem);
    }

    public class MyHandler : DelegatingHandler
    {
        protected override async Task<HttpResponseMessage>
            SendAsync(HttpRequestMessage request, CancellationToken cancellationToken)
        {
            // Add a custom header to the request.
            request.Headers.Add("x-my-header", "my value");
            var response = await base.SendAsync(request, cancellationToken);
            // Set a differnt response status code.
            response.StatusCode = HttpStatusCode.ServiceUnavailable;
            return response;
        }
    }

Este código agrega un nuevo encabezado **x-my-header** en la solicitud y establece arbitrariamente el código de respuesta como no disponible. En un escenario real, debería establecer el código de estado de respuesta basado en alguna lógica personalizada requerida por la aplicación.

### <a name="serialization"></a>Personalización de la serialización

La biblioteca de cliente de Aplicaciones móviles usa Json.NET para convertir una respuesta JSON en objetos de .NET en el cliente. Puede configurar el comportamiento de esta serialización entre tipos .NET y JSON en los mensajes. La clase [MobileServiceClient] expone una propiedad `SerializerSettings` de tipo [JsonSerializerSettings](http://james.newtonking.com/projects/json/help/?topic=html/T_Newtonsoft_Json_JsonSerializerSettings.htm)

Con esta propiedad, puede establecer una de las muchas propiedades Json.NET, como las siguientes:

	var settings = new JsonSerializerSettings();
	settings.ContractResolver = new CamelCasePropertyNamesContractResolver();
	client.SerializerSettings = settings;

Esta propiedad convierte todas las propiedades en minúsculas durante la serialización.

<!-- Anchors. -->
[Filtro de datos devueltos]: #filtering
[Ordenar datos devueltos]: #sorting
[Devolución de datos en páginas]: #paging
[Devolver datos en páginas]: #paging
[Seleccionar columnas específicas]: #selecting
[Búsqueda de datos por identificador]: #lookingup

<!-- Images. -->



<!-- URLs. -->
[Add authentication to your app]: app-service-mobile-windows-store-dotnet-get-started-users.md
[Trabajar con el SDK del servidor back-end de .NET para Aplicaciones móviles de Azure]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[PasswordVault]: http://msdn.microsoft.com/library/windows/apps/windows.security.credentials.passwordvault.aspx
[ProtectedData]: http://msdn.microsoft.com/library/system.security.cryptography.protecteddata%28VS.95%29.aspx
[LoginAsync method]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclientextensions.loginasync.aspx
[MobileServiceAuthenticationProvider]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider.aspx
[MobileServiceUser]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.aspx
[UserID]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.userid.aspx
[MobileServiceAuthenticationToken]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.mobileserviceauthenticationtoken.aspx
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[MobileServiceClient]: http://msdn.microsoft.com/library/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx
[IncludeTotalCount]: http://msdn.microsoft.com/library/windowsazure/dn250560.aspx
[Skip]: http://msdn.microsoft.com/library/windowsazure/dn250573.aspx
[Take]: http://msdn.microsoft.com/library/windowsazure/dn250574.aspx
[Fiddler]: http://www.telerik.com/fiddler
[API personalizada en los SDK del cliente de Servicios móviles de Azure]: http://blogs.msdn.com/b/carlosfigueira/archive/2013/06/19/custom-api-in-azure-mobile-services-client-sdks.aspx
[InvokeApiAsync]: http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.invokeapiasync.aspx
[DelegatingHandler]: https://msdn.microsoft.com/library/system.net.http.delegatinghandler(v=vs.110).aspx

<!---HONumber=AcomDC_0302_2016-->