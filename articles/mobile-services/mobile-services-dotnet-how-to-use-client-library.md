<properties
	pageTitle="Trabajo con la biblioteca de cliente administrada de Servicios móviles (Windows | Xamarin) | Microsoft Azure"
	description="Aprenda a usar a un cliente .NET para Servicios móviles de Azure con aplicaciones de Windows y Xamarin."
	services="mobile-services"
	documentationCenter=""
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="01/28/2016"
	ms.author="glenga"/>

# Uso de la biblioteca de cliente administrada para Servicios móviles de Azure.

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-client-library](../../includes/mobile-services-selector-client-library.md)]

##Información general

Esta guía muestra cómo realizar tareas comunes con la biblioteca de cliente administrada para Servicios móviles de Azure en aplicaciones de Windows y Xamarin. Entre las tareas incluidas se encuentran la consulta, inserción, actualización y eliminación de datos, la autenticación de usuarios y la administración de errores. Si no tiene experiencia en el uso de Servicios móviles, considere primero la opción de completar el tutorial [Introducción a Servicios móviles](mobile-services-dotnet-backend-xamarin-ios-get-started.md).

[AZURE.INCLUDE [mobile-services-concepts](../../includes/mobile-services-concepts.md)]

##<a name="setup"></a>Configuración y requisitos previos

Se asume que ha creado un servicio móvil y una tabla. Para obtener más información, consulte [Crear una tabla](http://go.microsoft.com/fwlink/?LinkId=298592). En el código usado en este tema, el nombre de la tabla es `TodoItem` y dispondrá de las siguientes columnas: `Id`, `Text` y `Complete`.

El tipo .NET del cliente con tipo correspondiente es el siguiente:


	public class TodoItem
	{
		public string Id { get; set; }

		[JsonProperty(PropertyName = "text")]
		public string Text { get; set; }

		[JsonProperty(PropertyName = "complete")]
		public bool Complete { get; set; }
	}

Tenga en cuenta que [JsonPropertyAttribute](http://www.newtonsoft.com/json/help/html/Properties_T_Newtonsoft_Json_JsonPropertyAttribute.htm) se usa para definir la asignación entre la asignación de PropertyName entre el tipo de cliente y la tabla.

Cuando está habilitado el esquema dinámico en un servicio móvil de back-end de JavaScript, Servicios móviles de Azure genera automáticamente columnas nuevas basadas en el objeto en las solicitudes de inserción o actualización. Para obtener más información, consulte [Esquema dinámico](http://go.microsoft.com/fwlink/?LinkId=296271). En un servicio móvil de back-end .NET, la tabla se define en el modelo de datos del proyecto.

##<a name="create-client"></a>Creación del cliente de Servicios móviles

El código siguiente crea el objeto `MobileServiceClient` que se usa para obtener acceso al servicio móvil.


	MobileServiceClient client = new MobileServiceClient(
		"AppUrl",
		"AppKey"
	);

En el código anterior, reemplace `AppUrl` y `AppKey` por la URL y la clave de aplicación del servicio móvil, en ese orden. Estos datos están disponibles en el Portal de Azure clásico si selecciona el servicio móvil y hace clic en "Panel".

>[AZURE.IMPORTANT]La clave de aplicación está diseñada para filtrar solicitudes aleatorias contra el servicio móvil y se distribuye con la aplicación. Dado que esta clave no está cifrada, no puede considerarse segura. Para proteger verdaderamente los datos de su servicio móvil, los usuarios se deben autenticar antes de permitir el acceso. Para obtener más información, consulte [Autenticación de usuarios](#authentication)

##<a name="instantiating"></a>Creación de una referencia de tabla

Todo el código que obtiene acceso o modifica los datos de la tabla de Servicios móviles llama a las funciones del objeto `MobileServiceTable`. Obtenga una referencia a la tabla llamando al método [GetTable](https://msdn.microsoft.com/library/azure/jj554275.aspx) en una instancia de `MobileServiceClient` del modo indicado a continuación:

    IMobileServiceTable<TodoItem> todoTable =
		client.GetTable<TodoItem>();

Este es el modelo de serialización con tipo; consulte la discusión del [modelo de serialización sin tipo](#untyped) a continuación.

##<a name="querying"></a>Consulta de datos desde un servicio móvil

En esta sección se describe cómo generar consultas al servicio móvil, lo cual incluye la siguiente funcionalidad:

- [Filtro de datos devueltos]
- [Ordenar datos devueltos]
- [Devolver datos en páginas]
- [Seleccionar columnas específicas]
- [Búsqueda de datos por identificador]

>[AZURE.NOTE] Se aplica el tamaño de página del servidor para evitar que se devuelvan todas las filas. De esta forma, se evita que las consultas predeterminadas de los conjuntos de datos de gran tamaño incidan negativamente en el servicio. Para devolver más de 50 filas, use el método `Take` como se describe en [Devolución de datos en páginas].

### <a name="filtering"></a>Filtro de datos devueltos

El siguiente código muestra cómo filtrar los datos incluyendo una cláusula `Where` en una consulta. Devuelve todos los elementos de `todoTable` cuya propiedad `Complete` es igual a `false`. La función `Where` aplica un predicado de filtrado de fila a la consulta en la tabla.

	// This query filters out completed TodoItems and
	// items without a timestamp.
	List<TodoItem> items = await todoTable
	   .Where(todoItem => todoItem.Complete == false)
	   .ToListAsync();

Puede ver el URI de la solicitud que se ha enviado al servicio móvil mediante el software de inspección de mensajes, como las herramientas para desarrolladores del explorador o [Fiddler]. Si consulta el URI de solicitud siguiente, tenga en cuenta que se está modificando la propia cadena de consulta:

	GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1
Normalmente, esta solicitud se traduciría aproximadamente en la siguiente consulta SQL en el servidor:

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

Los dos métodos son equivalentes y pueden usarse indistintamente. La opción anterior, de concatenación de varios predicados en una consulta, es más compacta y es la que se recomienda.

La cláusula `where` es compatible con las operaciones que pueden traducirse en el subconjunto OData de Servicios móviles. Incluye operadores relacionales (==, !=, <, <=, >, >=), operadores aritméticos (+, -, /, *, %), precisión numérica (Math.Floor, Math.Ceiling), funciones de cadena (Length, Substring, Replace, IndexOf, StartsWith, EndsWith), propiedades de fecha (Year, Month, Day, Hour, Minute, Second), propiedades de acceso de un objeto y expresiones que combinan todas las opciones anteriores.

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

De manera predeterminada, el servidor devuelve solo las primeras 50 filas. Aumente el número de filas devueltas llamando al método [Take]. Use `Take` junto con el método [Skip] para solicitar una "página" específica del conjunto de datos total devuelto por la consulta. Cuando se ejecuta la siguiente consulta, se devuelven los tres primeros elementos de la tabla.

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

####Consideraciones de paginación para un servicio móvil de backend .NET

Para invalidar el límite de 50 filas en un servicio móvil de backend .NET, también debe aplicar [EnableQueryAttribute](https://msdn.microsoft.com/library/system.web.http.odata.enablequeryattribute.aspx) al método público GET y especificar el comportamiento de paginación. Cuando se aplica al método, lo siguiente establece el máximo de filas devueltas a 1000:

    [EnableQuery(MaxTop=1000)]


### <a name="selecting"></a>Selección de columnas específicas

Puede especificar qué conjunto de propiedades se incluirá en los resultados agregando una cláusula `Select` a su consulta. Por ejemplo, el siguiente código muestra cómo seleccionar solo un campo y también cómo seleccionar varios campos y darles formato:

	// Select one field -- just the Text
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Select(todoItem => todoItem.Text);
	List<string> items = await query.ToListAsync();

	// Select multiple fields -- both Complete and Text info
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Select(todoItem => string.Format("{0} -- {1}", todoItem.Text.PadRight(30), todoItem.Complete ? "Now complete!" : "Incomplete!"));
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

##<a name="inserting"></a>Inserción de datos en un servicio móvil

> [AZURE.NOTE] Si desea realizar operaciones de inserción, búsqueda, eliminación o actualización en un tipo, tiene que crear un miembro denominado **Id**. Este es el motivo por el que la clase de ejemplo **TodoItem** cuenta con un miembro de **Id** de nombre. Debe haber un valor válido siempre presente en las operaciones de actualización y eliminación.

El siguiente código muestra cómo insertar filas nuevas en una tabla. El parámetro contiene los datos que se van a insertar como un objeto .NET.

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

Servicios móviles admite valores de cadena personalizados únicos para columna **id** de la tabla. Esto permite a las aplicaciones usar valores personalizados como direcciones de correo electrónico o nombres de usuario para el Id.

Los identificadores de cadena proporcionan las siguientes ventajas:

+ Se generan identificadores sin realizar una vuelta a la base de datos.
+ Los registros son más fáciles de fusionar desde diferentes tablas o bases de datos.
+ Los valores de los identificadores pueden integrarse mejor con una lógica de aplicación.

Cuando no se establece un valor de identificador de cadena en un registro insertado, Servicios móviles genera un valor único para el Id. Puede utilizar el método `Guid.NewGuid()` para generar sus propios valores de Id., en el cliente o en un servicio móvil backend. NET. Para obtener más información acerca de la generación de GUID en un servicio móvil de backend de JavaScript, consulte [Generación de valores de identificador únicos](mobile-services-how-to-use-server-scripts.md#generate-guids).

También puede utilizar identificadores enteros para las tablas. Para usar un identificador de números enteros, debe crear la tabla con el comando `mobile table create` usando la opción `--integerId`. Este comando se usa con la interfaz de la línea de comandos (CLI) de Azure. Para obtener más información sobre el uso de la CLI, consulte [CLI para administrar tablas de Servicios móviles](../virtual-machines-command-line-tools.md#Mobile_Tables).

##<a name="modifying"></a>Modificación de datos en un servicio móvil

El siguiente código muestra cómo actualizar una instancia existente con el mismo identificador con nueva información. El parámetro contiene los datos que se van a actualizar como un objeto .NET.

	await todoTable.UpdateAsync(todoItem);


Para insertar datos sin tipo, puede aprovechar Json.NET. Tenga en cuenta que cuando realice una actualización, debe especificarse un identificador, ya que de esa forma el servicio móvil identifica qué instancia actualizar. El identificador puede obtenerse a partir del resultado de la llamada `InsertAsync`.

	JObject jo = new JObject();
	jo.Add("Id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
	jo.Add("Text", "Hello World");
	jo.Add("Complete", false);
	var inserted = await table.UpdateAsync(jo);

Si intenta actualizar un elemento sin proporcionar un valor "Id", no hay forma de que el servicio indique qué instancia actualizar, por lo que el SDK de Servicios móviles iniciará `ArgumentException`.


##<a name="deleting"></a>Eliminación de datos en un servicio móvil

El siguiente código muestra cómo eliminar una instancia existente. La instancia se identifica mediante el campo "Id" establecido en `todoItem`.

	await todoTable.DeleteAsync(todoItem);

Para eliminar datos sin tipo, puede aprovechar Json.NET. Tenga en cuenta que cuando realice una solicitud de eliminación, debe especificarse un identificador, ya que de esa forma el servicio móvil identifica qué instancia eliminar. Una solicitud de eliminación precisa solo el identificador; las demás propiedades no se pasan al servicio, y si se pasa alguna, se ignoran en el servicio. El resultado de una llamada `DeleteAsync` es normalmente `null` también. El identificador puede obtenerse a partir del resultado de la llamada `InsertAsync`.

	JObject jo = new JObject();
	jo.Add("Id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
	await table.DeleteAsync(jo);

Si intenta eliminar un elemento sin el campo "Id" ya establecido, no hay forma de que el servicio indique qué instancia eliminar, por lo que volverá a obtener `MobileServiceInvalidOperationException` del servicio. De forma parecida, si intenta eliminar un elemento sin tipo sin el campo "Id" ya establecido, volverá a obtener `MobileServiceInvalidOperationException` del servicio.

##<a name="#custom-api"></a>Llamada a una API personalizada

Una API personalizada le permite definir extremos personalizados que exponen la funcionalidad del servidor que no se asigna a una operación de inserción, actualización, eliminación o lectura. Al usar una API personalizada, puede tener más control sobre la mensajería, incluida la lectura y el establecimiento de encabezados de mensajes HTTP y la definición del formato del cuerpo de un mensaje diferente de JSON. Para obtener un ejemplo de cómo crear una API personalizada en el servicio móvil, vea [Definición de un punto de conexión de API personalizada](mobile-services-dotnet-backend-define-custom-api.md).

Puede llamar a una API personalizada al llamar a una de las sobrecargas del método [InvokeApiAsync] en el cliente. Por ejemplo, la siguiente línea de código envía una solicitud POST a la API **completeAll** del servicio móvil:

    var result = await App.MobileService
        .InvokeApiAsync<MarkAllResult>("completeAll",
        System.Net.Http.HttpMethod.Post, null);

Tenga en cuenta que se trata de una llamada de método con tipo, lo que requiere la definición del tipo de valor devuelto **MarkAllResult**. Se admiten métodos con y sin tipos. Este ejemplo es casi insignificante, ya que tiene tipo, no envía ninguna carga, no tiene parámetros de consulta y no cambia los encabezados de solicitud. Para obtener ejemplos más realistas y un análisis más completo de [InvokeApiAsync], consulte [API personalizada en los SDK del cliente de Servicios móviles de Azure].

##Registrar notificaciones de inserción

El cliente de Servicios móviles permite registrar las notificaciones de inserción con Centros de notificaciones de Azure. Al registrar, se obtiene un identificador del servicio de notificaciones push (PNS) específico de la plataforma. A continuación, proporcione este valor junto con las etiquetas cuando se cree el registro. El código siguiente registra la aplicación de Windows para las notificaciones push en el Servicio de notificaciones de Windows.(WNS):

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

Tenga en cuenta que en este ejemplo se incluyen dos etiquetas en el registro. Para obtener más información sobre las aplicaciones de Windows, consulte [Incorporación de notificaciones push a la aplicación](mobile-services-dotnet-backend-windows-universal-dotnet-get-started-push.md).

Las aplicaciones de Xamarin requieren código adicional para poder registrar una aplicación de Xamarin que se ejecute en la aplicación iOS o Android con el Servicio de notificaciones push de Apple (APNS) y el Servicio de mensajería en la nube de Google (GCM), respectivamente. Para obtener más información, consulte **Incorporación de notificaciones push a la aplicación** ([Xamarin.iOS](partner-xamarin-mobile-services-ios-get-started-push.md#add-push) | [Xamarin.Android](partner-xamarin-mobile-services-android-get-started-push.md#add-push)).

>[AZURE.NOTE]Cuando necesite enviar notificaciones a los usuarios registrados específicos, es importante requerir autenticación antes del registro y, a continuación, comprobar que el usuario está autorizado para registrarse con una etiqueta específica. Por ejemplo, se debe comprobar que un usuario no se registra con una etiqueta de Id. de usuario de otra persona. Para obtener más información, consulte [Envío de notificaciones de inserción a usuarios autenticados](mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users.md).

##<a name="pull-notifications"></a>Cómo usar notificaciones periódicas en una aplicación de Windows

Windows admite notificaciones periódicas (notificaciones push) para actualizar los iconos dinámicos. Con las notificaciones periódicas habilitadas, Windows tendrá acceso periódico a un punto de conexión de la API personalizada para actualizar el icono dinámico en el menú Inicio. Para usar notificaciones periódicas, debe [definir una API personalizada](mobile-services-javascript-backend-define-custom-api.md) que devuelva datos XML en un formato específico de icono. Para obtener más información, consulte [Notificaciones periódicas](https://msdn.microsoft.com/library/windows/apps/hh761461.aspx).

El siguiente ejemplo activa las notificaciones periódicas para solicitar datos de la plantilla de icono desde un punto de conexión personalizado de *iconos*:

    TileUpdateManager.CreateTileUpdaterForApplication().StartPeriodicUpdate(
        new System.Uri(MobileService.ApplicationUri, "/api/tiles"),
        PeriodicUpdateRecurrence.Hour
    );

Seleccione un valor [PeriodicUpdateRecurrance](https://msdn.microsoft.com/library/windows/apps/windows.ui.notifications.periodicupdaterecurrence.aspx) que se adapte mejor a la frecuencia de actualización de sus datos.

##<a name="optimisticconcurrency"></a>Uso de la simultaneidad optimista

En algunos casos, dos o más clientes pueden escribir cambios en el mismo elemento y al mismo tiempo. Si no se produjera la detección de conflictos, la última escritura sobrescribiría cualquier actualización anterior incluso si no fuese el resultado deseado. El control de simultaneidad optimista asume que cada transacción puede confirmarse y, por lo tanto, no usa ningún bloqueo de recursos. Antes de confirmar una transacción, el control de simultaneidad optimista comprueba que ninguna otra transacción haya modificado los datos. Si los datos se han modificado, la transacción de confirmación se desecha.

Servicios móviles es compatible con el control de simultaneidad optimista mediante el seguimiento de cambios en cada elemento mediante la columna de propiedades del sistema `__version` que se definió en cada tabla creada por Servicios móviles. Cada vez que se actualiza un registro, Servicios móviles establece la propiedad `__version` para ese registro en un nuevo valor. Durante cada solicitud de actualización, la propiedad `__version` del registro incluido con la solicitud se compara con la misma propiedad del registro en el servidor. Si la versión que pasa con la solicitud no coincide con el servidor, la biblioteca cliente de .NET de Servicios móviles inicia `MobileServicePreconditionFailedException<T>`. El tipo incluido con la excepción es el registro del servidor que contiene la versión del registro del servidor. A continuación, la aplicación puede usar esta información para decidir si ejecutar la solicitud de actualización de nuevo con el valor `__version` correcto del servidor para confirmar los cambios.

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


Para obtener un ejemplo más completo sobre el uso de la simultaneidad optimista para Servicios móviles, consulte [Tutorial de simultaneidad optimista].


##<a name="binding"></a>Enlace de datos de servicio móvil a una interfaz de usuario de Windows

En esta sección se describe cómo mostrar objetos de datos devueltos mediante elementos de la interfaz de usuario en una aplicación Windows. Para consultar elementos incompletos en `todoTable` y visualizarlos en una lista muy sencilla, puede ejecutar el siguiente código de ejemplo para enlazar el origen de la lista con una consulta. El uso de `MobileServiceCollection` crea una colección de enlaces para los servicios móviles.

	// This query filters out completed TodoItems.
	MobileServiceCollection<TodoItem, TodoItem> items = await todoTable
		.Where(todoItem => todoItem.Complete == false)
		.ToCollectionAsync();

	// itemsControl is an IEnumerable that could be bound to a UI list control
	IEnumerable itemsControl  = items;

	// Bind this to a ListBox
	ListBox lb = new ListBox();
	lb.ItemsSource = items;

Algunos controles en tiempo de ejecución administrado son compatibles con una interfaz denominada [ISupportIncrementalLoading](http://msdn.microsoft.com/library/windows/apps/Hh701916). Esta interfaz permite a los controles solicitar datos adicionales cuando el usuario se desplaza. Existe compatibilidad integrada de esta interfaz para las aplicaciones universales de Windows 8.1 a través de `MobileServiceIncrementalLoadingCollection`, que administra automáticamente las llamadas desde los controles. Para usar `MobileServiceIncrementalLoadingCollection` en las aplicaciones de Windows, realice las siguientes acciones:

			MobileServiceIncrementalLoadingCollection<TodoItem,TodoItem> items;
		items =  todoTable.Where(todoItem => todoItem.Complete == false)
					.ToIncrementalLoadingCollection();

		ListBox lb = new ListBox();
		lb.ItemsSource = items;


Para usar la nueva colección en aplicaciones de Windows Phone 8 y "Silverlight", use los métodos de extensión `ToCollection` en `IMobileServiceTableQuery<T>` y `IMobileServiceTable<T>`. Para cargar datos realmente, llame a `LoadMoreItemsAsync()`.

	MobileServiceCollection<TodoItem, TodoItem> items = todoTable.Where(todoItem => todoItem.Complete==false).ToCollection();
	await items.LoadMoreItemsAsync();

Cuando use la colección creada mediante la llamada a `ToCollectionAsync` o `ToCollection`, obtendrá una colección que puede enlazarse a los controles de la interfaz de usuario. Esta colección es para la paginación, es decir, un control puede solicitar a la colección cargar más elementos y la colección lo hará para el control. En este momento, no hay ningún código de usuario implicado y el control iniciará el flujo. Sin embargo, puesto que la colección está cargando datos desde la red, cabe esperar que a veces se produzca un error en la carga. Para gestionar esos errores, puede reemplazar el método `OnException` de `MobileServiceIncrementalLoadingCollection` para gestionar excepciones resultantes de las llamadas a `LoadMoreItemsAsync` que han realizado los controles.

Para finalizar, imagine que la tabla contiene muchos campos, pero solo desea que se muestren algunos en el control. Puede usar la guía de la sección anterior ["Selección de columnas específicas"](#selecting) para seleccionar las columnas específicas para mostrar en la interfaz de usuario.

##<a name="authentication"></a>Autenticación de usuarios

Servicios móviles es compatible con la autenticación y autorización de los usuarios de aplicaciones mediante diversos proveedores de identidades externas: Facebook, Google, Microsoft Account, Twitter y Azure Active Directory. Puede establecer permisos en tablas para restringir el acceso a operaciones específicas solo a usuarios autenticados. También puede usar la identidad de usuarios autenticados para implementar reglas de autorización en scripts del servidor. Para obtener más información, consulte el tutorial [Incorporación de la autenticación a su aplicación].

Se admiten dos flujos de autenticación: un _server flow_ y un _client flow_. El flujo de servidor ofrece la experiencia de autenticación más simple, ya que se basa en la interfaz de autenticación web del proveedor. El flujo de cliente permite una mayor integración con capacidades específicas del dispositivo, ya que se basa en SDK específicos del dispositivo y específicos del proveedor.

###Flujo de servidor
Para que Servicios móviles administre el proceso de autenticación en las aplicaciones de Windows, debe registrar la aplicación con el proveedor de identidades. A continuación, en el servicio móvil, tendrá que configurar el identificador y el secreto de la aplicación proporcionados por el proveedor. Para obtener más información, consulte el tutorial [Incorporación de la autenticación a su aplicación].

Una vez que haya registrado el proveedor de identidades, simplemente llame al método [LoginAsync] con el valor [MobileServiceAuthenticationProvider] del proveedor. Por ejemplo, el siguiente código activa un inicio de sesión de flujo de servidor mediante Facebook.

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

Si está usando un proveedor de identidades diferente al de Facebook, cambie el valor de [MobileServiceAuthenticationProvider] anterior por el valor de su proveedor.

En este caso, Servicios móviles administra el flujo de autenticación de OAuth 2.0 mostrando la página de inicio de sesión del proveedor seleccionado y generando un token de autenticación de Servicios móviles después de que se realice un inicio de sesión correcto con el proveedor de identidades. El [método LoginAsync] devuelve [MobileServiceUser], que proporciona [userId] del usuario autenticado y [MobileServiceAuthenticationToken] como un token de web JSON (JWT). El token puede almacenarse en caché y volver a usarse hasta que expire. Para obtener más información, consulte [Almacenamiento en caché del token de autenticación].

###Flujo de cliente

La aplicación también puede ponerse en contacto de manera independiente con el proveedor de identidades y, a continuación, proporcionar el token devuelto a Servicios móviles para la autenticación. Este flujo de cliente le permite proporcionar una experiencia de inicio de sesión único para los usuarios o recuperar datos de usuario adicionales del proveedor de identidades.

####Inicio de sesión único mediante un token de Facebook o Google

En la forma más simplificada, puede usar el flujo de cliente como se muestra en este fragmento para Facebook o Google.

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


####Inicio de sesión único mediante Cuenta Microsoft con el SDK de Live

Para autenticar usuarios, debe registrar la aplicación en el Centro para desarrolladores de la cuenta de Microsoft. A continuación, debe conectarse este registro con el servicio móvil. Complete los pasos de [Registro de la aplicación para usar un inicio de sesión de cuenta Microsoft](mobile-services-how-to-register-microsoft-authentication.md) a fin de crear un registro de cuenta Microsoft y conectarlo al servicio móvil. Si dispone de ambas versiones de la aplicación, Tienda Windows y Windows Phone 8/Silverlight, registre primero la versión de Tienda Windows.

El siguiente código se autentica mediante el SDK de Live y usa el token devuelto para iniciar sesión en el servicio móvil.

	private LiveConnectSession session;
 	//private static string clientId = "<microsoft-account-client-id>";
    private async System.Threading.Tasks.Task AuthenticateAsync()
    {

        // Get the URL the mobile service.
        var serviceUrl = App.MobileService.ApplicationUri.AbsoluteUri;

        // Create the authentication client for Windows Store using the mobile service URL.
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

                // Use the Microsoft account auth token to sign in to Mobile Services.
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


###<a name="caching"></a>Almacenamiento en caché del token de autenticación
En algunos casos, la llamada al método de inicio de sesión puede evitarse después de la primera vez que el usuario se autentique. Puede usar [PasswordVault] en las aplicaciones de la Tienda Windows para almacenar en caché la identidad del usuario actual la primera vez que se inicie sesión en ellas y las veces posteriores que compruebe si ya dispone de la identidad de usuario en la memoria caché. Si la memoria caché está vacía, tendrá que enviar el usuario a través del proceso de inicio de sesión.

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


En las aplicaciones de Windows Phone, puede almacenar en caché los datos y cifrarlos mediante la clase [ProtectedData], y almacenar información confidencial en un almacén aislado.

##<a name="errors"></a>Gestión de errores

Existen varias formas de detectar, validar y solucionar errores en Servicios móviles.

Por ejemplo, los scripts de servidor se registran en un servicio móvil y pueden usarse para realizar una amplia variedad de operaciones en los datos que se han insertado y actualizado, incluidas la modificación y validación de los datos. Imagine que define y registra un script de servidor que valida y modifica datos:

	function insert(item, user, request)
	{
	   if (item.text.length > 10) {
		  request.respond(statusCodes.BAD_REQUEST, { error: "Text cannot exceed 10 characters" });
	   } else {
		  request.execute();
	   }
	}

Este script del servidor valida la longitud de los datos de cadena enviados al servicio móvil y rechaza las cadenas que son muy largas, en este caso superiores a 10 caracteres.

Ahora que el servicio móvil está validando datos y enviando respuestas de error en el servidor, puede actualizar la aplicación .NET para poder gestionar las respuestas de error a partir de la validación.

	private async void InsertTodoItem(TodoItem todoItem)
	{
		// This code inserts a new TodoItem into the database. When the operation completes
		// and Mobile Services has assigned an Id, the item is added to the CollectionView
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

##<a name="untyped"></a>Trabajo con datos sin tipo

El cliente .NET se ha creado para escenarios fuertemente tipados. Sin embargo, es recomendable una experiencia menos tipada; por ejemplo, cuando se trata con objetos con un esquema abierto. El escenario se habilita de la forma siguiente. En las consultas, prescinda de LINQ y use el formato.

	// Get an untyped table reference
	IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");

	// Lookup untyped data using OData
	JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");

Vuelva a obtener valores JSON que puede usar como un contenedor de propiedades. Para obtener más información sobre JToken y [Json.NET](http://json.codeplex.com/), consulte Json.NET.

##<a name="unit-testing"></a>Diseño de pruebas unitarias

El valor devuelto por `MobileServiceClient.GetTable` y las consultas son interfaces. Esto hace que puedan crearse bocetos fácilmente para propósitos de prueba, por lo que puede crear `MyMockTable : IMobileServiceTable<TodoItem>` que implementa la lógica de prueba.

##<a name="customizing"></a>Personalización del cliente

Esta sección muestra formas en que puede personalizar los encabezados de solicitud y personalizar la serialización de objetos JSON en la respuesta.

### <a name="headers"></a>Personalización de encabezados de solicitud

Para admitir el escenario de aplicación específico, deberá personalizar la comunicación con el servicio móvil. Por ejemplo, es posible que desee agregar un encabezado personalizado a cada solicitud saliente o cambiar los códigos de estado de las respuestas. Puede hacer esto proporcionando un DelegatingHandler personalizado, como en el ejemplo siguiente:

	public async Task CallClientWithHandler()
	{
		MobileServiceClient client = new MobileServiceClient(
			"AppUrl",
			"AppKey" ,
			new MyHandler()
			);
		IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();
		var newItem = new TodoItem { Text = "Hello world", Complete = false };
		await table.InsertAsync(newItem);
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

La biblioteca de cliente de Servicios móviles usa Json.NET para convertir una respuesta JSON en objetos de .NET en el cliente. Puede configurar el comportamiento de esta serialización entre tipos .NET y JSON en los mensajes. La clase [MobileServiceClient](http://msdn.microsoft.com/library/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx) expone una propiedad `SerializerSettings` de tipo [JsonSerializerSettings](http://james.newtonking.com/projects/json/help/?topic=html/T_Newtonsoft_Json_JsonSerializerSettings.htm)

Con esta propiedad, puede establecer una de las muchas propiedades Json.NET, como las siguientes:

	var settings = new JsonSerializerSettings();
	settings.ContractResolver = new CamelCasePropertyNamesContractResolver();
	client.SerializerSettings = settings;

Esta propiedad convierte todas las propiedades en minúsculas durante la serialización.

<!-- Anchors. -->
[What is Mobile Services]: #what-is
[Concepts]: #concepts
[How to: Create the Mobile Services client]: #create-client
[How to: Create a table reference]: #instantiating
[How to: Query data from a mobile service]: #querying
[Filtro de datos devueltos]: #filtering
[Ordenar datos devueltos]: #sorting
[Devolución de datos en páginas]: #paging
[Devolver datos en páginas]: #paging
[Seleccionar columnas específicas]: #selecting
[Búsqueda de datos por identificador]: #lookingup
[How to: Bind data to user interface in a mobile service]: #binding
[How to: Insert data into a mobile service]: #inserting
[How to: Modify data in a mobile service]: #modifying
[How to: Delete data in a mobile service]: #deleting
[How to: Use Optimistic Concurrency]: #optimisticconcurrency
[How to: Authenticate users]: #authentication
[How to: Handle errors]: #errors
[How to: Design unit tests]: #unit-testing
[How to: Query data from a mobile service]: #querying
[How to: Customize the client]: #customizing
[How to: Work with untyped data]: #untyped
[Customize request headers]: #headers
[Customize serialization]: #serialization
[Next steps]: #nextsteps
[Almacenamiento en caché del token de autenticación]: #caching
[How to: Call a custom API]: #custom-api

<!-- Images. -->



<!-- URLs. -->
[Incorporación de la autenticación a su aplicación]: mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users.md
[PasswordVault]: http://msdn.microsoft.com/library/windows/apps/windows.security.credentials.passwordvault.aspx
[ProtectedData]: http://msdn.microsoft.com/library/system.security.cryptography.protecteddata%28VS.95%29.aspx
[LoginAsync]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclientextensions.loginasync.aspx
[método LoginAsync]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclientextensions.loginasync.aspx
[MobileServiceAuthenticationProvider]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider.aspx
[MobileServiceUser]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.aspx
[UserID]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.userid.aspx
[MobileServiceAuthenticationToken]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.mobileserviceauthenticationtoken.aspx
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[CLI to manage Mobile Services tables]: ../virtual-machines-command-line-tools.md/#Commands_to_manage_mobile_services
[Tutorial de simultaneidad optimista]: mobile-services-windows-store-dotnet-handle-database-conflicts.md
[MobileServiceClient]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx

[IncludeTotalCount]: http://msdn.microsoft.com/library/windowsazure/dn250560.aspx
[Skip]: http://msdn.microsoft.com/library/windowsazure/dn250573.aspx
[Take]: http://msdn.microsoft.com/library/windowsazure/dn250574.aspx
[Fiddler]: http://www.telerik.com/fiddler
[API personalizada en los SDK del cliente de Servicios móviles de Azure]: http://blogs.msdn.com/b/carlosfigueira/archive/2013/06/19/custom-api-in-azure-mobile-services-client-sdks.aspx
[InvokeApiAsync]: http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.invokeapiasync.aspx

<!---HONumber=AcomDC_0211_2016-->