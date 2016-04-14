<properties
	pageTitle="Uso de un cliente HTM con Servicios móviles de Azure | Microsoft Azure"
	description="Obtenga información acerca de cómo usar un cliente HTML para Servicios móviles de Azure."
	services="mobile-services"
	documentationCenter=""
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-html"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="01/26/2016"
	ms.author="glenga"/>

# Uso de un cliente HTML/JavaScript para Servicios móviles de Azure

[AZURE.INCLUDE [mobile-services-selector-client-library](../../includes/mobile-services-selector-client-library.md)]

##Información general

En esta guía se muestra cómo ejecutar escenarios comunes con la utilización de un cliente HTML/JavaScript para Servicios móviles de Azure, que incluye aplicaciones de Windows Store JavaScript y PhoneGap/Cordova. Entre las tareas incluidas se encuentran la consulta, inserción, actualización y eliminación de datos, la autenticación de usuarios y la administración de errores. Si no tiene experiencia en el uso de Servicios móviles, considere primero la opción de completar la [guía de inicio rápido de Servicios móviles](mobile-services-html-get-started.md) (en inglés). El tutorial de inicio rápido le ayuda a configurar la cuenta y crear el primer servicio móvil.

[AZURE.INCLUDE [mobile-services-concepts](../../includes/mobile-services-concepts.md)]

##<a name="create-client"></a>Creación del cliente de Servicios móviles

La manera en que se agrega una referencia para el cliente de Servicios móviles depende de la plataforma de aplicaciones, que incluye lo siguiente:

- En una aplicación basada en web, abra el archivo HTML y agregue lo siguiente a las referencias de script de la página:

        <script src="http://ajax.aspnetcdn.com/ajax/mobileservices/MobileServices.Web-1.2.7.min.js"></script>

- Para una aplicación de la Tienda Windows escrita en JavaScript/HTML, agregue el paquete NuGet **WindowsAzure.MobileServices.WinJS** a su proyecto.

- Para una aplicación PhoneGap o Cordova, agregue el [complemento de servicios móviles](https://github.com/Azure/azure-mobile-services-cordova) al proyecto. Este complemento admite [notificaciones de inserción](#push-notifications).

En el editor, abra o cree un archivo JavaScript, agregue el siguiente código que define la variable `MobileServiceClient` y, a continuación, proporcione la URL y la clave de la aplicación desde el servicio móvil en el constructor `MobileServiceClient`, en dicho orden.

	var MobileServiceClient = WindowsAzure.MobileServiceClient;
    var client = new MobileServiceClient('AppUrl', 'AppKey');

Debe sustituir el marcador de posición `AppUrl` por la dirección URL de la aplicación del servicio móvil y `AppKey` por la clave de la aplicación que obtuvo en el [Portal de Azure clásico](http://manage.windowsazure.com/).

>[AZURE.IMPORTANT]La clave de aplicación está diseñada para filtrar solicitudes aleatorias contra el servicio móvil y se distribuye con la aplicación. Dado que esta clave no está cifrada, no puede considerarse segura. Para proteger verdaderamente los datos de su servicio móvil, los usuarios se deben autenticar antes de permitir el acceso. Para obtener más información, consulte [Autenticación de usuarios](#authentication)

##<a name="querying"></a>Consulta de datos desde un servicio móvil

Todo el código que obtiene acceso a los datos de la tabla de Base de datos SQL, o los modifica, llama a las funciones del objeto `MobileServiceTable`. Se obtiene una referencia a la tabla llamando a la función `getTable()` en una instancia de `MobileServiceClient`.

    var todoItemTable = client.getTable('todoitem');


### <a name="filtering"></a>Filtro de datos devueltos

El siguiente código muestra cómo filtrar los datos incluyendo una cláusula `where` en una consulta. Devuelve todos los elementos de `todoItemTable` cuyo campo Complete es `false`. `todoItemTable` es la referencia a la tabla del servicio móvil que creamos anteriormente. La función Where aplica un predicado de filtrado de fila a la consulta en la tabla. Acepta como argumento una función o un objeto JSON que define el filtrado de filas y devuelve una consulta que se puede componer aún más.

	var query = todoItemTable.where({
	    complete: false
	}).read().done(function (results) {
	    alert(JSON.stringify(results));
	}, function (err) {
	    alert("Error: " + err);
	});

Al llamar a `where` en el objeto Query y pasar un objeto como un parámetro, ordenamos a Servicios móviles que devuelva solo las filas cuya columna `complete` contenga el valor `false`. Además, consulte el URI de solicitud siguiente y observe que se está modificando la propia cadena de consulta:

	GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1

Puede ver el URI de la solicitud que se ha enviado al servicio móvil mediante el software de inspección de mensajes, como las herramientas para desarrolladores del explorador o Fiddler.

Normalmente, esta solicitud se traduciría aproximadamente en la siguiente consulta SQL en el servidor:

	SELECT *
	FROM TodoItem
	WHERE ISNULL(complete, 0) = 0

El objeto pasado al método `where` puede tener un número arbitrario de parámetros, que se interpretarán como cláusulas AND para la consulta. Por ejemplo, la siguiente línea:

	query.where({
	   complete: false,
	   assignee: "david",
	   difficulty: "medium"
	}).read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});

Se traduciría aproximadamente (para la misma solicitud mostrada anteriormente) de la siguiente forma:

	SELECT *
	FROM TodoItem
	WHERE ISNULL(complete, 0) = 0
	      AND assignee = 'david'
	      AND difficulty = 'medium'

La instrucción `where` y la consulta SQL anteriores encuentran elementos incompletos asignados a "david" de dificultad "medium".

No obstante, hay otra forma de escribir la misma consulta. Una llamada `.where` en el objeto Query agregará una expresión `AND` a la cláusula `WHERE` por lo que, en su lugar, podríamos haber escrito eso en tres líneas:

	query.where({
	   complete: false
	});
	query.where({
	   assignee: "david"
	});
	query.where({
	   difficulty: "medium"
	});

O bien usar la API fluida:

	query.where({
	   complete: false
	})
	   .where({
	   assignee: "david"
	})
	   .where({
	   difficulty: "medium"
	});

Los dos métodos son equivalentes y pueden usarse indistintamente. Todas las llamadas `where` hasta el momento obtienen un objeto con algunos parámetros y se comparan en términos de igualdad con respecto a los datos de la base de datos. No obstante, hay otra sobrecarga del método de consulta, que usa una función en lugar del objeto. En esta función, podemos escribir expresiones más complejas, con la utilización de operadores como inequality y otras operaciones relacionales. En estas funciones, la palabra clave `this` enlaza al objeto de servidor.

El cuerpo de la función se traduce en una expresión booleana Open Data Protocol (OData) que se pasa a un parámetro de la cadena de consulta. Es posible transferir una función que no usa ningún parámetro, como:

    query.where(function () {
       return this.assignee == "david" && (this.difficulty == "medium" || this.difficulty == "low");
    }).read().done(function (results) {
       alert(JSON.stringify(results));
    }, function (err) {
       alert("Error: " + err);
    });


Si se transfiere una función con parámetros, cualquier argumento después de la cláusula `where` se enlaza a los parámetros de función en orden. Cualquier objeto procedente de fuera del ámbito de la función DEBE transferirse como parámetro; la función no puede capturar ninguna variable externa. En los dos ejemplos siguientes, el argumento "david" se enlaza al parámetro `name` y, en el primer ejemplo, el argumento "medium" también se enlaza al parámetro `level`. Además, la función debe consistir en una única instrucción `return` con una expresión admitida, como:

	 query.where(function (name, level) {
	    return this.assignee == name && this.difficulty == level;
	 }, "david", "medium").read().done(function (results) {
	    alert(JSON.stringify(results));
	 }, function (err) {
	    alert("Error: " + err);
	 });

Por tanto, siempre que sigamos las reglas, podemos agregar filtros más complejos a las consultas de la base de datos, como:

    query.where(function (name) {
       return this.assignee == name &&
          (this.difficulty == "medium" || this.difficulty == "low");
    }, "david").read().done(function (results) {
       alert(JSON.stringify(results));
    }, function (err) {
       alert("Error: " + err);
    });

Es posible combinar `where` con `orderBy`, `take` y `skip`. Consulte la siguiente sección para obtener información detallada.

### <a name="sorting"></a>Clasificación de datos devueltos

El siguiente código muestra cómo ordenar datos incluyendo una función `orderBy` u `orderByDescending` en la consulta. Devuelve los elementos de `todoItemTable` ordenados de manera ascendente por el campo `text`. De forma predeterminada, el servidor devuelve solo los primeros 50 elementos.

> [AZURE.NOTE] Se utiliza el tamaño de página del servidor de forma predeterminada para evitar que se devuelvan todos los elementos. De esta forma, se evita que las consultas predeterminadas de los conjuntos de datos de gran tamaño incidan negativamente en el servicio. Si llama a `take`, tal y como se describe en la siguiente sección, puede aumentar el número de elementos que se devolverán. `todoItemTable` es la referencia a la tabla de Servicios móviles que hemos creado anteriormente.

	var ascendingSortedTable = todoItemTable.orderBy("text").read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});

	var descendingSortedTable = todoItemTable.orderByDescending("text").read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});

	var descendingSortedTable = todoItemTable.orderBy("text").orderByDescending("text").read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});

### <a name="paging"></a>Devolución de datos en páginas

De forma predeterminada, los Servicios móviles solo devuelven 50 filas en una solicitud determinada, a menos que el cliente solicite explícitamente más datos en la respuesta. El siguiente código muestra cómo implementar la paginación en los datos devueltos a través de las cláusulas `take` y `skip` en la consulta. Cuando se ejecuta la siguiente consulta, se devuelven los tres primeros elementos de la tabla.

	var query = todoItemTable.take(3).read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});

Observe que el método `take(3)` se ha traducido en la opción de consulta `$top=3` en el URI de la consulta.

La siguiente consulta revisada omite los tres primeros resultados y devuelve los tres siguientes. Esa es realmente la segunda página de datos en la que el tamaño de página cuenta con tres elementos.

	var query = todoItemTable.skip(3).take(3).read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});

También puede ver el URI de la solicitud enviada al servicio móvil. Observe que el método `skip(3)` se ha traducido en la opción de consulta `$skip=3` en el URI de la consulta.

Este es un escenario simplificado para pasar valores de paginación codificados de forma rígida a las funciones `take` y `skip`. En una aplicación en tiempo real, puede usar consultas similares a las anteriores con un control de paginación o interfaz de usuario comparable para permitir a los usuarios desplazarse a las páginas anteriores y posteriores.

### <a name="selecting"></a>Selección de columnas específicas

Puede especificar qué conjunto de propiedades se incluirá en los resultados agregando una cláusula `select` a su consulta. Por ejemplo, el siguiente código devuelve las propiedades `id`, `complete` y `text` de cada fila en `todoItemTable`:

	var query = todoItemTable.select("id", "complete", "text").read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	})

Aquí los parámetros para la función de selección son los nombres de las columnas de la tabla que desea devolver.


Todas las funciones descritas en adelante son adicionales, por lo que es posible seguir llamándolas y cada vez irá afectando más a la consulta. A continuación se muestra un ejemplo más:

    query.where({
       complete: false
    })
       .select('id', 'assignee')
       .orderBy('assignee')
       .take(10)
       .read().done(function (results) {
       alert(JSON.stringify(results));
    }, function (err) {
       alert("Error: " + err);

### <a name="lookingup"></a>Búsqueda de datos por identificador

La función `lookup` usa solo el valor `id` y devuelve el objeto de la base de datos con dicho identificador. Las tablas de la base de datos se crean con una columna `id` de cadena o de entero. La opción predeterminada es una columna `id` de cadena.

	todoItemTable.lookup("37BBF396-11F0-4B39-85C8-B319C729AF6D").done(function (result) {
	   alert(JSON.stringify(result));
	}, function (err) {
	   alert("Error: " + err);
	})

##<a name="odata-query"></a>Ejecución de una operación de consulta de OData

Servicios móviles usa las convenciones de URI de consulta OData para la composición y ejecución de las consultas REST. No todas las consultas OData se pueden crear mediante las funciones de consulta integradas, especialmente las operaciones de filtro complejas como, por ejemplo, la búsqueda de una subcadena en una propiedad. Para estos tipos de consultas complejas, puede pasar cualquier opción de consulta OData válida a la función `read`, tal como se indica a continuación:

	function refreshTodoItems() {
	    todoItemTable.read("$filter=substringof('search_text',text)").then(function(items) {
	        var itemElements = $.map(items, createUiForTodoItem);
	        $("#todo-items").empty().append(itemElements);
	        $("#no-items").toggle(items.length === 0);
	    }, handleError);
	}

>[AZURE.NOTE]Cuando proporciona una cadena de opción de consulta OData sin formato en la función `read`, no es posible usar también los métodos de compilador de consultas en la misma consulta. En este caso, debe componer la consulta completa como una cadena de consulta OData. Para obtener más información sobre las opciones de consulta del sistema OData, consulte la [referencia de opciones de consulta del sistema OData].

##<a name="inserting"></a>Inserción de datos en un servicio móvil

El siguiente código muestra cómo insertar filas nuevas en una tabla. El cliente solicita la inserción de una fila de datos mediante el envío de una solicitud POST al servicio móvil. El cuerpo de la solicitud contiene los datos que se insertarán, como un objeto JSON.

	todoItemTable.insert({
	   text: "New Item",
	   complete: false
	})

De esta forma se insertan los datos en la tabla a partir del objeto JSON proporcionado. También puede especificar una función de devolución de llamada que se invocará cuando se haya completado la inserción:

	todoItemTable.insert({
	   text: "New Item",
	   complete: false
	}).done(function (result) {
	   alert(JSON.stringify(result));
	}, function (err) {
	   alert("Error: " + err);
	});

###Trabajar con valores de Id.

Servicios móviles admite valores de cadena personalizados únicos para columna **id** de la tabla. Esto permite a las aplicaciones usar valores personalizados como direcciones de correo electrónico o nombres de usuario para el Id. Por ejemplo, el código siguiente inserta un nuevo elemento como un objeto JSON, donde el identificador único es una dirección de correo electrónico:

	todoItemTable.insert({
	   id: "myemail@domain.com",
	   text: "New Item",
	   complete: false
	});

Los identificadores de cadena proporcionan las siguientes ventajas:

+ Se generan identificadores sin realizar una vuelta a la base de datos.
+ Los registros son más fáciles de fusionar desde diferentes tablas o bases de datos.
+ Los valores de los identificadores pueden integrarse mejor con una lógica de aplicación.

Si no se ha establecido un valor de identificador de cadena en un registro insertado, Servicios móviles genera un valor único para el Id. Para obtener más información sobre cómo generar sus propios valores de Id., en el cliente o en un back-end .NET, consulte [Generación de valores de identificador únicos](mobile-services-how-to-use-server-scripts.md#generate-guids).

También puede utilizar identificadores enteros para las tablas. Para usar un identificador de números enteros, debe crear la tabla con el comando `mobile table create` usando la opción `--integerId`. Este comando se usa con la interfaz de la línea de comandos (CLI) de Azure. Para obtener más información sobre el uso de la CLI, consulte [CLI para administrar tablas de Servicios móviles](../virtual-machines-command-line-tools.md#Mobile_Tables).

##<a name="modifying"></a>Modificación de datos en un servicio móvil

El siguiente código muestra cómo actualizar datos en una tabla. El cliente solicita la actualización de una fila de datos mediante el envío de una solicitud PATCH al servicio móvil. El cuerpo de la solicitud contiene los campos específicos que se actualizarán, como un objeto JSON. Actualiza un elemento existente en la tabla .`todoItemTable`

	todoItemTable.update({
	   id: idToUpdate,
	   text: newText
	})

El primer parámetro especifica la instancia que se actualizará en la tabla, según especifique su identificador.

También puede especificar una función de devolución de llamada que se invocará cuando se haya completado la actualización:

	todoItemTable.update({
	   id: idToUpdate,
	   text: newText
	}).done(function (result) {
	   alert(JSON.stringify(result));
	}, function (err) {
	   alert("Error: " + err);
	});

##<a name="deleting"></a>Eliminación de datos en un servicio móvil

El siguiente código muestra cómo eliminar datos de una tabla. El cliente solicita la eliminación de una fila de datos mediante el envío de una solicitud DELETE al servicio móvil. Elimina un elemento existente en la tabla todoItemTable.

	todoItemTable.del({
	   id: idToDelete
	})

El primer parámetro especifica la instancia que se eliminará en la tabla, según especifique su identificador.

También puede especificar una función de devolución de llamada que se invocará cuando se haya completado la eliminación:

	todoItemTable.del({
	   id: idToDelete
	}).done(function () {
	   /* Do something */
	}, function (err) {
	   alert("Error: " + err);
	});

##<a name="binding"></a>Visualización de datos en la interfaz de usuario

En esta sección se describe cómo mostrar objetos de datos devueltos mediante elementos de la interfaz de usuario. Para consultar elementos en `todoItemTable` y visualizarlos en una lista muy sencilla, puede ejecutar el siguiente código de ejemplo. No se realiza selección, filtrado ni ordenación de ningún tipo.

	var query = todoItemTable;

	query.read().then(function (todoItems) {
	   // The space specified by 'placeToInsert' is an unordered list element <ul> ... </ul>
	   var listOfItems = document.getElementById('placeToInsert');
	   for (var i = 0; i < todoItems.length; i++) {
	      var li = document.createElement('li');
	      var div = document.createElement('div');
	      div.innerText = todoItems[i].text;
	      li.appendChild(div);
	      listOfItems.appendChild(li);
	   }
	}).read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});

En una aplicación de la Tienda Windows, los resultados de una consulta se pueden usar para crear un objeto WinJS.Binding.List, que puede enlazarse como el origen de datos de un objeto [ListView]. Para obtener más información, consulte [Enlace de datos (aplicaciones de la Tienda Windows con JavaScript y HTML)].

##<a name="custom-api"></a>Llamada a una API personalizada

Una API personalizada le permite definir extremos personalizados que exponen la funcionalidad del servidor que no se asigna a una operación de inserción, actualización, eliminación o lectura. Al usar una API personalizada, puede tener más control sobre la mensajería, incluida la lectura y el establecimiento de encabezados de mensajes HTTP y la definición del formato del cuerpo de un mensaje diferente de JSON. Para obtener un ejemplo de cómo crear una API personalizada en el servicio móvil, vea [Definición de un extremo de API personalizada](mobile-services-dotnet-backend-define-custom-api.md).

Llame a una API personalizada desde el cliente mediante una llamada al método [invokeApi](https://github.com/Azure/azure-mobile-services/blob/master/sdk/Javascript/src/MobileServiceClient.js#L337) en **MobileServiceClient**. Por ejemplo, la siguiente línea de código envía una solicitud POST a la API **completeAll** del servicio móvil:

    client.invokeApi("completeall", {
        body: null,
        method: "post"
    }).done(function (results) {
        var message = results.result.count + " item(s) marked as complete.";
        alert(message);
        refreshTodoItems();
    }, function(error) {
        alert(error.message);
    });


Para obtener ejemplos más realistas y un análisis más completo de **invokeApi**, consulte [API personalizada en los SDK del cliente de Servicios móviles de Azure](http://blogs.msdn.com/b/carlosfigueira/archive/2013/06/19/custom-api-in-azure-mobile-services-client-sdks.aspx).

##<a name="authentication"></a>Autenticación de usuarios

Servicios móviles es compatible con la autenticación y autorización de los usuarios de aplicaciones mediante diversos proveedores de identidades externas: Facebook, Google, Microsoft Account y Twitter. Puede establecer permisos en tablas para restringir el acceso a operaciones específicas solo a usuarios autenticados. También puede usar la identidad de usuarios autenticados para implementar reglas de autorización en scripts del servidor. Para obtener más información, consulte el tutorial [Introducción a la autenticación].

>[AZURE.NOTE] Si se usa la autenticación en una aplicación de PhoneGap o Cordova, también debe agregar los siguientes complementos al proyecto:
>
>+ https://git-wip-us.apache.org/repos/asf/cordova-plugin-device.git
>+ https://git-wip-us.apache.org/repos/asf/cordova-plugin-inappbrowser.git


Se admiten dos flujos de autenticación: un _server flow_ y un _client flow_. El flujo de servidor ofrece la experiencia de autenticación más simple, ya que se basa en la interfaz de autenticación web del proveedor. El flujo de cliente permite una mayor integración con capacidades específicas del dispositivo, como el inicio de sesión único, ya que se basa en SDK específicos del dispositivo y específicos del proveedor.

###Flujo de servidor
Para que Servicios móviles administre el proceso de autenticación en la aplicación de la Tienda Windows o HTML5, debe registrar la aplicación con el proveedor de identidades. A continuación, en el servicio móvil, tendrá que configurar el identificador y el secreto de la aplicación proporcionados por el proveedor. Para obtener más información, consulte el tutorial [Incorporación de la autenticación a su aplicación](mobile-services-html-get-started-users.md).

Una vez que haya registrado el proveedor de identidades, simplemente llame al [método LoginAsync] con el valor [MobileServiceAuthenticationProvider] del proveedor. Por ejemplo, para iniciar sesión con Facebook, use el siguiente código:

	client.login("facebook").done(function (results) {
	     alert("You are now logged in as: " + results.userId);
	}, function (err) {
	     alert("Error: " + err);
	});

Si utiliza un proveedor de identidades que no sea Facebook, cambie el valor pasado al método `login` anterior a uno de los siguientes: `microsoftaccount`, `facebook`, `twitter`, `google` o `windowsazureactivedirectory`.

En este caso, Servicios móviles administra el flujo de autenticación de OAuth 2.0 mostrando la página de inicio de sesión del proveedor seleccionado y generando un token de autenticación de Servicios móviles después de que se realice un inicio de sesión correcto con el proveedor de identidades. La función [login], cuando se completa, devuelve un objeto JSON (**user**) que expone el identificador de usuario y el token de autenticación de Servicios móviles en los campos **userId** y **authenticationToken**, respectivamente. El token puede almacenarse en caché y volver a usarse hasta que expire. Para obtener más información, consulte [Almacenamiento en caché del token de autenticación].

###Flujo de cliente
La aplicación también puede ponerse en contacto de manera independiente con el proveedor de identidades y, a continuación, proporcionar el token devuelto a Servicios móviles para la autenticación. Este flujo de cliente le permite proporcionar una experiencia de inicio de sesión único para los usuarios o recuperar datos de usuario adicionales del proveedor de identidades.

####Ejemplo básico de SDK de Google o Facebook

Este ejemplo usa el SDK de cliente de Facebook para la autenticación:

	client.login(
	     "facebook",
	     {"access_token": token})
	.done(function (results) {
	     alert("You are now logged in as: " + results.userId);
	}, function (err) {
	     alert("Error: " + err);
	});

En este ejemplo se asume que el token proporcionado por el SDK del proveedor correspondiente se almacena en la variable `token`. En este momento Twitter no se puede usar para la autenticación de cliente.

####Ejemplo básico de cuenta Microsoft
El siguiente ejemplo usa el SDK de Live, que admite el inicio de sesión único para aplicaciones de la Tienda Windows mediante la cuenta de Microsoft:

	WL.login({ scope: "wl.basic"}).then(function (result) {
	      client.login(
	            "microsoftaccount",
	            {"authenticationToken": result.session.authentication_token})
	      .done(function(results){
	            alert("You are now logged in as: " + results.userId);
	      },
	      function(error){
	            alert("Error: " + err);
	      });
	});

Este ejemplo simplificado obtiene un token de Live Connect, que se proporciona a los Servicios móviles realizando una llamada a la función [login].


####Ejemplo completo de cuenta Microsoft

En el ejemplo siguiente se muestra cómo usar el SDK de Live con las API de WinJS para proporcionar una experiencia mejorada de inicio de sesión único:

	// Set the mobileClient variable to client variable generated by the tooling.
	var mobileClient = <yourClient>;

	var session = null;
	var login = function () {
		return new WinJS.Promise(function (complete) {
			WL.login({ scope: "wl.basic" }).then(function (result) {
				session = result.session;

				WinJS.Promise.join([
					WL.api({ path: "me", method: "GET" }),
					mobileClient.login(result.session.authentication_token)
				]).done(function (results) {
					// Build the welcome message from the Microsoft account info.
					var profile = results[0];
					var title = "Welcome " + profile.first_name + "!";
					var message = "You are now logged in as: "
						+ mobileClient.currentUser.userId;
					var dialog = new Windows.UI.Popups.MessageDialog(message, title);
					dialog.showAsync().then(function () {
						// Reload items from the mobile service.
						refreshTodoItems();
					}).done(complete);

				}, function (error) {

				});
			}, function (error) {
				session = null;
				var dialog = new Windows.UI.Popups.MessageDialog("You must log in.", "Login Required");
				dialog.showAsync().done(complete);
			});
		});
	}

	var authenticate = function () {
		// Block until sign-in is successful.
		login().then(function () {
			if (session === null) {
				// Authentication failed, try again.
				authenticate();
			}
		});
	}

	// Initialize the Live client.
	WL.init({
		redirect_uri: mobileClient.applicationUrl
	});

	// Start the sign-in process.
	authenticate();

De este modo se inicializa el cliente Live Connect, se envía una nueva solicitud de inicio de sesión a la cuenta de Microsoft, se envía el token de autenticación devuelto a Servicios móviles y, a continuación, se muestra la información del usuario que ha iniciado sesión. La aplicación no se inicia hasta que la autenticación se realiza correctamente. 
<!--- //this guidance may be bad from an XSS vulnerability standpoint. We need to find better guidance for this
###Caching the authentication token
In some cases, the call to the login method can be avoided after the first time the user authenticates. We can use [sessionStorage] or [localStorage] to cache the current user identity the first time they log in and every subsequent time we check whether we already have the user identity in our cache. If the cache is empty or calls fail (meaning the current login session has expired), we still need to go through the login process.

    // After logging in
    sessionStorage.loggedInUser = JSON.stringify(client.currentUser);

    // Log in
    if (sessionStorage.loggedInUser) {
       client.currentUser = JSON.parse(sessionStorage.loggedInUser);
    } else {
       // Regular login flow
   }

     // Log out
    client.logout();
    sessionStorage.loggedInUser = null;
-->

##<a name="push-notifications"></a>Registrar notificaciones de inserción

Cuando la aplicación es una aplicación de PhoneGap o Apache Cordova HTML/JavaScript, la plataforma móvil nativa le permite recibir notificaciones de inserción en el dispositivo. El [complemento Apache Cordova para Servicios móviles de Azure](https://github.com/Azure/azure-mobile-services-cordova) permite registrar las notificaciones de inserción con Centros de notificaciones de Azure. El servicio de notificación específico usado depende de la plataforma de dispositivos nativos en la que se ejecuta el código. Para obtener un ejemplo de cómo hacerlo, consulte la muestra [Usar Microsoft Azure para notificaciones de inserción en aplicaciones Cordova](https://github.com/Azure/mobile-services-samples/tree/master/CordovaNotificationsArticle).

>[AZURE.NOTE]Actualmente este complemento solo es compatible con dispositivos iOS y Android. Para obtener una solución que incluya también dispositivos Windows, consulte el artículo [Notificaciones de inserción en aplicaciones de PhoneGap mediante la integración de los Centros de notificaciones](http://blogs.msdn.com/b/azuremobile/archive/2014/06/17/push-notifications-to-phonegap-apps-using-notification-hubs-integration.aspx).

##<a name="errors"></a>Gestión de errores

Existen varias formas de detectar, validar y solucionar errores en Servicios móviles.

Por ejemplo, los scripts de servidor se registran en un servicio móvil y pueden usarse para realizar una amplia variedad de operaciones en los datos que se han insertado y actualizado, incluidas la modificación y validación de los datos. Imagine que define y registra un script de servidor que valida y modifica datos:

	function insert(item, user, request) {
	   if (item.text.length > 10) {
		  request.respond(statusCodes.BAD_REQUEST, { error: "Text cannot exceed 10 characters" });
	   } else {
	      request.execute();
	   }
	}

Este script del servidor valida la longitud de los datos de cadena enviados al servicio móvil y rechaza las cadenas que son muy largas, en este caso superiores a 10 caracteres.

Ahora que el servicio móvil está validando datos y enviando respuestas de error en el servidor, puede actualizar la aplicación HTML para poder gestionar las respuestas de error a partir de la validación.

	todoItemTable.insert({
	   text: itemText,
	   complete: false
	})
	   .then(function (results) {
	   alert(JSON.stringify(results));
	}, function (error) {
	   alert(JSON.parse(error.request.responseText).error);
	});


Para ofrecer más detalles, transfiere el controlador del error como el segundo argumento cada vez que obtiene acceso a los datos:

	function handleError(message) {
	   if (window.console && window.console.error) {
	      window.console.error(message);
	   }
	}

	client.getTable("tablename").read()
		.then(function (data) { /* do something */ }, handleError);

##<a name="promises"></a>Uso de promesas

Las promesas ofrecen un mecanismo para programar el trabajo que hay que hacer en un valor que aún no se ha procesado. Se trata de una abstracción para administrar interacciones con API asincrónicas.

La promesa `done` se ejecuta en cuanto la función proporcionada se ha completado satisfactoriamente o ha producido un error. A diferencia de la promesa `then`, se garantiza el lanzamiento de cualquier error no identificado en la función y, después de que los controladores han terminado de ejecutarse, esta función lanza cualquier error que debería haberse devuelto después como una promesa en el estado de error. Para obtener más información, consulte [done].

	promise.done(onComplete, onError);

Como:

	var query = todoItemTable;
	query.read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});

La promesa `then` es igual que `done`, pero a diferencia de `then`, con `done` se garantiza el lanzamiento de cualquier error no identificado en la función. Si no proporciona un controlador de error a `then` y la operación experimenta un error, no se lanza una excepción, sino que, en su lugar, se devuelve una promesa en el estado de error. Para obtener más información, consulte [then].

	promise.then(onComplete, onError).done( /* Your success and error handlers */ );

Como:

	var query = todoItemTable;
	query.read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});

Puede utilizar promesas de diferentes formas. Puede encadenar operaciones de promesas con llamadas a `then` o `done` en la promesa devuelta por la función `then` anterior. Use `then` para una fase intermedia de la operación (por ejemplo `.then().then()`) y `done` para la fase final de la operación (por ejemplo `.then().then().done()`). Puede encadenar varias funciones `then`, porque `then` devuelve una promesa. No puede encadenar más de un método `done`, porque devuelve undefined. [Más información acerca de las diferencias entre then y done].

	todoItemTable.insert({
	   text: "foo"
	}).then(function (inserted) {
	   inserted.newField = 123;
	   return todoItemTable.update(inserted);
	}).done(function (insertedAndUpdated) {
	   alert(JSON.stringify(insertedAndUpdated));
	})

##<a name="customizing"></a>Personalización de encabezados de solicitud de cliente

Puede enviar encabezados de solicitud personalizados con la función `withFilter`, con la lectura y escritura de propiedades arbitrarias de la solicitud que se va a enviar dentro del filtro. Puede agregar dicho encabezado HTTP personalizado si un script del servidor lo necesita o si puede mejorarse con él.

	var client = new WindowsAzure.MobileServiceClient('https://your-app-url', 'your-key')
	   .withFilter(function (request, next, callback) {
	   request.headers.MyCustomHttpHeader = "Some value";
	   next(request, callback);
	});

Los filtros se usan para mucho más que para personalizar encabezados de solicitud. Pueden usarse para examinar o cambiar solicitudes, examinar o cambiar respuestas, derivar llamadas de redes, enviar varias llamadas, etc.

##<a name="hostnames"></a>Uso de recursos de origen cruzado

Para controlar a qué sitios web se les permite interactuar con solicitudes y enviarlas al servicio móvil, asegúrese de que agrega el nombre de host al sitio web que usa para hospedarlo en la lista blanca de uso compartido de recursos de origen cruzado (CORS). Para un servicio móvil de back-end de JavaScript, puede configurar la lista blanca en la pestaña Configurar del [Portal de Azure clásico](https://manage.windowsazure.com). Puede usar caracteres comodín en caso de que sea necesario. De forma predeterminada, los nuevos Servicios móviles ordenan a los exploradores que permitan el acceso solo desde `localhost` y el uso compartido de recursos entre orígenes permite que el código JavaScript se ejecute en un explorador en un nombre de host externo para interactuar con el servicio móvil. Esta configuración no es necesaria para aplicaciones WinJS.

<!-- Anchors. -->
[What is Mobile Services]: #what-is
[Concepts]: #concepts
[How to: Create the Mobile Services client]: #create-client
[How to: Query data from a mobile service]: #querying
[Filter returned data]: #filtering
[Sort returned data]: #sorting
[Return data in pages]: #paging
[Select specific columns]: #selecting
[Look up data by ID]: #lookingup
[How to: Display data in the user interface]: #binding
[How to: Insert data into a mobile service]: #inserting
[How to: Modify data in a mobile service]: #modifying
[How to: Delete data in a mobile service]: #deleting
[How to: Authenticate users]: #authentication
[How to: Handle errors]: #errors
[How to: Use promises]: #promises
[How to: Customize request headers]: #customizing
[How to: Use cross-origin resource sharing]: #hostnames
[Next steps]: #nextsteps
[Execute an OData query operation]: #odata-query



<!-- URLs. -->
[then]: http://msdn.microsoft.com/library/windows/apps/br229728.aspx
[done]: http://msdn.microsoft.com/library/windows/apps/hh701079.aspx
[Más información acerca de las diferencias entre then y done]: http://msdn.microsoft.com/library/windows/apps/hh700334.aspx
[how to handle errors in promises]: http://msdn.microsoft.com/library/windows/apps/hh700337.aspx

[sessionStorage]: http://msdn.microsoft.com/library/cc197062(v=vs.85).aspx
[localStorage]: http://msdn.microsoft.com/library/cc197062(v=vs.85).aspx

[ListView]: http://msdn.microsoft.com/library/windows/apps/br211837.aspx
[Enlace de datos (aplicaciones de la Tienda Windows con JavaScript y HTML)]: http://msdn.microsoft.com/library/windows/apps/hh758311.aspx
[login]: https://github.com/Azure/azure-mobile-services/blob/master/sdk/Javascript/src/MobileServiceClient.js#L301
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[referencia de opciones de consulta del sistema OData]: http://go.microsoft.com/fwlink/p/?LinkId=444502

<!---HONumber=AcomDC_0211_2016-->