
##<a name="update-app"></a>Actualización de la aplicación para llamar a la API personalizada

1. En Visual Studio, abra el archivo default.html en el proyecto de inicio rápido, busque el elemento **button** llamado `buttonRefresh` y agregue el siguiente elemento nuevo inmediatamente después: 

		<button id="buttonCompleteAll" style="margin-left: 5px">Complete All</button>

	Esta acción agrega un nuevo botón a la página.

2. Abra el archivo de código default.js de la carpeta de proyecto `js`, busque la función **refreshTodoItems** y asegúrese de que esta función contenga el siguiente código:

	    todoTable.where({ complete: false })
	       .read()
	       .done(function (results) {
	           todoItems = new WinJS.Binding.List(results);
	           listItems.winControl.itemDataSource = todoItems.dataSource;
	       });            

	Esto filtra los elementos para que la consulta no devuelva los elementos completados.

3. Después de la función **refreshTodoItems**, agregue el siguiente código:

		var completeAllTodoItems = function () {
		    var okCommand = new Windows.UI.Popups.UICommand("OK");
		
		    // Asynchronously call the custom API using the POST method. 
		    mobileService.invokeApi("completeall", {
		        body: null,
		        method: "post"
		    }).done(function (results) {
		        var message = results.result.count + " item(s) marked as complete.";
		        var dialog = new Windows.UI.Popups.MessageDialog(message);
		        dialog.commands.append(okCommand);
		        dialog.showAsync().done(function () {
		            refreshTodoItems();
		        });
		    }, function (error) {
		        var dialog = new Windows.UI.Popups
		            .MessageDialog(error.message);
		        dialog.commands.append(okCommand);
		        dialog.showAsync().done();
		    });
		};

        buttonCompleteAll.addEventListener("click", function () {
            completeAllTodoItems();
        });

	Este método controla el evento **Click** del botón nuevo. Se llama al método **InvokeApiAsync** en el cliente, el cual envía una solicitud POST a la nueva API personalizada. El resultado devuelto por la API personalizada se muestra en un cuadro de diálogo de mensaje, al igual que todos los errores.

## <a name="test-app"></a>Prueba de la aplicación

1. En Visual Studio, presione la tecla **F5** para recopilar el proyecto e iniciar la aplicación.

2. En la aplicación, escriba algo de texto en **Insertar TodoItem** y, a continuación, haga clic en **Guardar**.

3. Repita el paso anterior hasta que haya agregado varios elementos todo a la lista.

4. Haga clic en el botón **Completar todo**.

  	![](./media/mobile-services-windows-store-javascript-call-custom-api/mobile-custom-api-windows-store-completed.png)

	Aparece un cuadro de diálogo de mensaje que indica el número de elementos marcados como completos y la consulta filtrada se vuelve a ejecutar, con lo que se borran todos los elementos de la lista.

<!---HONumber=Oct15_HO3-->