En esta sección vamos a modificar el modelo de nuestra base de datos mediante la incorporación de un nuevo campo de marca de tiempo llamado **CompleteDate**. Este campo registrará la última vez que se completó el elemento todo. Entity Framework actualizará la base de datos basándose en el cambio de modelo utilizando una clase inicializadora de base de datos predeterminada derivada de [DropCreateDatabaseIfModelChanges](http://go.microsoft.com/fwlink/?LinkId=394621).

1. En el Explorador de soluciones de Visual Studio, expanda la carpeta **App\_Start** en el proyecto de servicio todolist. Abra el archivo WebApiConfig.cs.

2. En el archivo WebApiConfig.cs, observe que la clase del inicializador de base de datos predeterminado procede de la clase `DropCreateDatabaseIfModelChanges`. Esto significa que cualquier cambio en el modelo tendrá como resultado la anulación y la recreación de la tabla para adaptarse al modelo nuevo. De esta manera, los datos de la tabla se pierden y la tabla se reinicializa. Modifique el método Seed del inicializador de base de datos para que los datos de inicialización figuren del modo que se indica a continuación cuando se guarde el archivo WebApiConfig.cs.

    >[AZURE.NOTE]Al usar el inicializador de base de datos predeterminado, Entity Framework eliminará la base de datos y la volverá a crear siempre que detecte un cambio del modelo de datos en la definición del modelo de Code First. Para realizar este cambio en el modelo de datos y mantener los datos existentes en la base de datos, debe usar Migraciones de Code First. Para obtener más información, vea [Uso de Migraciones de Code First para actualizar el modelo de datos](../articles/mobile-services-dotnet-backend-how-to-use-code-first-migrations.md).

        List<TodoItem> todoItems = new List<TodoItem>
        {
          new TodoItem { Id = "1", Text = "First seed item", Complete = false },
          new TodoItem { Id = "2", Text = "Second seed item", Complete = false },
        };
     

3. En el Explorador de soluciones de Visual Studio, expanda la carpeta **DataObjects** en el proyecto de servicio todolist. Abra el archivo TodoItem.cs y actualice la clase TodoItem para incluir el campo CompleteDate como a continuación. Luego, guarde el archivo TodoItem.cs.

        public class TodoItem : EntityData
        {
          public string Text { get; set; }
          public bool Complete { get; set; }
          public System.DateTime? CompleteDate { get; set; }
        }

4. En el Explorador de soluciones de Visual Studio, expanda la carpeta **Controllers** en el proyecto de servicio todolist. Abra el archivo TodoItemController.cs y actualice el método `PatchTodoItem` de modo que establezca **CompleteDate** cuando la propiedad **Complete** cambia de false a true. A continuación, guarde el archivo TodoItemController.cs.

        public Task<TodoItem> PatchTodoItem(string id, Delta<TodoItem> patch)
        {
            // If complete is being set to true and was false, set CompleteDate
            if ((patch.GetEntity().Complete == true) &&
                (GetTodoItem(id).Queryable.Single().Complete == false))
            {
                patch.TrySetPropertyValue("CompleteDate", System.DateTime.Now);
            }
            return UpdateAsync(id, patch);
        }


5. Vuelva a compilar el proyecto de servicio todolist de back-end .NET y compruebe que no haya errores de compilación.

A continuación, actualice la aplicación cliente para mostrar los datos nuevos de **CompleteDate**.

<!---HONumber=Oct15_HO3-->