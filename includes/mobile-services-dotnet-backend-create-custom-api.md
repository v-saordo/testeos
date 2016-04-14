

1. En Visual Studio, haga clic con el botón secundario en la carpeta Controllers, expanda **Agregar** y, a continuación, haga clic en **Nuevo elemento de scaffolding**. Se mostrará el cuadro de diálogo Add Scaffold.

2. Expanda **Servicios móviles de Azure**, haga clic en **Controlador personalizado de Servicios móviles de Azure**, en **Agregar**, proporcione un **Nombre del controlador** de `CompleteAllController` y haga clic en **Agregar** de nuevo.

	![Cuadro de diálogo de Agregar Scaffold de la API web](./media/mobile-services-dotnet-backend-create-custom-api/add-custom-api-controller.png)

	De esta forma, se crea una nueva clase de controlador vacía llamada **CompleteAllController**.

	>[AZURE.NOTE]Si el cuadro de diálogo no dispone de scaffolding específico de Servicios móviles, cree una nueva clase para **Controlador de Web API - Vacío**. En esta nueva clase de controlador, agregue una propiedad **Services** pública, que devuelve el tipo **ApiServices**. Esta propiedad se usa para obtener acceso a la configuración específica del servidor desde dentro del controlador.

3. En **CompleteAllController.cs**, agregue las siguientes instrucciones **using**. Reemplace `todolistService` por el espacio de nombres del proyecto de servicio móvil, que debe agregarse al nombre de servicio móvil con `Service`.

		using System.Threading.Tasks;
		using todolistService.Models;

4. En **CompleteAllController.cs**, agregue la siguiente clase para encapsular la respuesta enviada al cliente.

        // We use this class to keep parity with other Mobile Services
        // that use the JavaScript backend. This way the same client
        // code can call either type of Mobile Service backend.
        public class MarkAllResult
        {
            public int count;
        }

5. Agregue el siguiente código al nuevo controlador. Reemplace `todolistContext` por el nombre de DbContext del modelo de datos, que debe ser el nombre del servicio móvil agregado con `Context`. De manera similar, reemplace el nombre de esquema de la instrucción UPDATE con el nombre del servicio móvil. Este código usa la [clase Database](http://msdn.microsoft.com/library/system.data.entity.database.aspx) para obtener acceso a la tabla **TodoItems** directamente a fin de establecer la marca de completado en todos los elementos. Este método es compatible con una solicitud POST y el número de filas cambiadas se devuelve al cliente como un valor entero.


	    // POST api/completeall
        public async Task<MarkAllResult> Post()
        {
            using (todolistContext context = new todolistContext())
            {
                // Get the database from the context.
                var database = context.Database;

                // Create a SQL statement that sets all uncompleted items
                // to complete and execute the statement asynchronously.
                var sql = @"UPDATE todolist.TodoItems SET Complete = 1 " +
                            @"WHERE Complete = 0; SELECT @@ROWCOUNT as count";

                var result = new MarkAllResult();
                result.count = await database.ExecuteSqlCommandAsync(sql);

                // Log the result.
                Services.Log.Info(string.Format("{0} items set to 'complete'.",
                    result.count.ToString()));

                return result;
            }
        }

	> [AZURE.TIP]Con los permisos predeterminados, cualquier usuario con la clave de la aplicación puede llamar a la API personalizada. Sin embargo, la clave de aplicación no se considera una credencial segura porque no se puede distribuir o almacenar de manera segura. Considere la posibilidad de restringir el acceso únicamente a usuarios autenticados para mayor seguridad.

<!---HONumber=Oct15_HO3-->