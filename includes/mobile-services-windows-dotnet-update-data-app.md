
2. Reemplace la definición de clase TodoItem por el código siguiente: 

	    public class TodoItem
	    {
	        public string Id { get; set; }
	
	        [Newtonsoft.Json.JsonProperty(PropertyName = "text")]  
	        public string Text { get; set; }
	
	        [Newtonsoft.Json.JsonProperty(PropertyName = "complete")]  
	        public bool Complete { get; set; }
	    }
	
	El atributo **JsonPropertyAttribute** se usa para definir la asignación entre los nombres de propiedad en el tipo de cliente y los nombres de columna en la tabla de datos subyacente.

	>[AZURE.NOTE]En un proyecto de aplicación universal de Windows, la clase TodoItem se define en un archivo de código independiente en la carpeta compartida DataModel.

1. En el archivo MainPage.cs, agregue o quite la marca de comentario en las siguientes instrucciones using:

		using Microsoft.WindowsAzure.MobileServices;


4. Elimine o convierta en comentario la línea que define la colección de elementos existente y luego quite la marca de comentario o agregue las siguientes líneas y reemplace _&lt;suCliente&gt;_ por el campo `MobileServiceClient` que se agregó al archivo App.xaml.cs cuando conectó su proyecto al servicio móvil:

		private MobileServiceCollection<TodoItem, TodoItem> items;
		private IMobileServiceTable<TodoItem> todoTable = 
		    App.<yourClient>.GetTable<TodoItem>();
		  
	Este código crea una colección de enlaces (elementos) compatible con los servicios móviles y una clase proxy para la tabla de base de datos (todoTable).


4. En el método **InsertTodoItem**, quite la línea de código que define la propiedad **TodoItem.Id**, agregue el modificador **async** al método y quite la marca de comentario de la siguiente línea de código:

		await todoTable.InsertAsync(todoItem);


	Este código inserta un elemento nuevo en la tabla.

5. Reemplace el método **RefreshTodoItems** por el código siguiente:

		private async void RefreshTodoItems()
        {
            MobileServiceInvalidOperationException exception = null;
            try
            {
                // Query that returns all items.   
                items = await todoTable.ToCollectionAsync();             
            }
            catch (MobileServiceInvalidOperationException e)
            {
                exception = e;
            }
            if (exception != null)
            {
                await new MessageDialog(exception.Message, "Error loading items").ShowAsync();
            }
            else
            {
                ListItems.ItemsSource = items;
                this.ButtonSave.IsEnabled = true;
            }    
        }

	Esto define el enlace a la colección de elementos que hay en `todoTable`, que contiene todos los objetos **TodoItem** devueltos por el servicio móvil. Si hay algún problema al ejecutar la consulta, aparece un cuadro de mensaje para mostrar los errores.

6. En el método **UpdateCheckedTodoItem**, agregue el modificador **async** al método y quite la marca de comentario en la siguiente línea de código:

		await todoTable.UpdateAsync(item);

	Esto envía una actualización de elemento al servicio móvil.

Ahora que se ha actualizado la aplicación para utilizar Servicios móviles para almacenamiento back-end, es momento de probar la aplicación con Servicios móviles.

<!---HONumber=Nov15_HO4-->