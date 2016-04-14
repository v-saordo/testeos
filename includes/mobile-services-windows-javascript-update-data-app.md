

1. Luego, quite la marca de comentario o agregue la siguiente línea de código y reemplace `<yourClient>` por la variable agregada al archivo service.js cuando conectó su proyecto al servicio móvil.

		var todoTable = <yourClient>.getTable('TodoItem');

   	Este código crea un objeto de proxy (**todoTable**) para la nueva tabla de base de datos usando el filtro de almacenamiento en caché.

2. Reemplace la función **InsertTodoItem** por el siguiente código:

		var insertTodoItem = function (todoItem) {
		    // Inserts a new row into the database. When the operation completes
		    // and Mobile Services has assigned an id, the item is added to the binding list.
		    todoTable.insert(todoItem).done(function (item) {
		        todoItems.push(item);
		    });
		};

	Este código inserta un elemento nuevo en la tabla.

3. Reemplace la función **RefreshTodoItems** por el siguiente código:

        var refreshTodoItems = function () {
            // This code refreshes the entries in the list by querying the table.
            // Results are filtered to remove completed items.
            todoTable.where({ complete: false })
                .read().done(function (results) {
                todoItems = new WinJS.Binding.List(results);
                listItems.winControl.itemDataSource = todoItems.dataSource;
            });
        };

   	Esto define el enlace a la colección de elementos que hay en todoTable, que contiene todos los objetos **TodoItem** devueltos por el servicio móvil.

4. Reemplace la función **UpdateCheckedTodoItem** por el siguiente código:
        
        var updateCheckedTodoItem = function (todoItem) {
            // This code takes a freshly completed TodoItem and updates the database. 
            todoTable.update(todoItem);
            // Remove the completed item from the filtered list.
            todoItems.splice(todoItems.indexOf(todoItem), 1);
        };

   	Esto envía una actualización de elemento al servicio móvil.

Ahora que se ha actualizado la aplicación para utilizar Servicios móviles para almacenamiento back-end, es momento de probar la aplicación con Servicios móviles.

<!---HONumber=Oct15_HO3-->