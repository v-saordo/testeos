<properties
	pageTitle="Activación de la sincronización sin conexión para la aplicación móvil de Azure (Windows 8.1) | Microsoft Azure"
	description="Obtenga información acerca de cómo usar Aplicaciones móviles de Azure para almacenar en caché y sincronizar datos sin conexión en su aplicación de la Tienda Windows"
	documentationCenter="windows"
	authors="wesmc7777"
	manager="dwrede"
	editor=""
	services="app-service\mobile"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="11/22/2015"
	ms.author="wesmc"/>

# Activación de la sincronización sin conexión para la aplicación de Windows

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

## Información general

Este tutorial muestra cómo agregar compatibilidad sin conexión a una aplicación de la tienda de Windows 8.1 o Windows Phone mediante un back-end de aplicación móvil de Azure. La sincronización sin conexión permite a los usuarios finales interactuar con una aplicación móvil (ver, agregar o modificar datos), incluso cuando no hay ninguna conexión de red. Los cambios se almacenan en una base de datos local; una vez que el dispositivo se vuelve a conectar, estos cambios se sincronizan con el back-end remoto.

En este tutorial, actualizará el proyecto de aplicación de Windows 8.1 del tutorial [Creación de una aplicación de Windows] para conseguir compatibilidad con las características sin conexión de Aplicaciones móviles de Azure. Si no usa el proyecto de servidor de inicio rápido descargado, debe agregar paquetes de extensión de acceso de datos al proyecto. Para obtener más información acerca de los paquetes de extensión de servidor, consulte [Trabajar con el SDK del servidor back-end de .NET para Aplicaciones móviles de Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Para obtener más información acerca de la característica de sincronización sin conexión, consulte el tema [Sincronización de datos sin conexión en Aplicaciones móviles de Azure].

## Requisitos

Este tutorial requiere lo siguiente:

* Visual Studio 2013 en Windows 8.1.
* Finalización del tutorial [Creación de una aplicación para Windows][create a windows app].
* [Almacén de SQLite de servicios móviles de Azure][sqlite store nuget]
* [SQLite para Windows 8.1](http://www.sqlite.org/downloads)

## Actualización de la aplicación cliente para que admita características sin conexión

Las características sin conexión de Aplicaciones móviles de Azure permiten interactuar con una base de datos local cuando el usuario se encuentra en un escenario sin conexión. Para usar estas características en la aplicación, inicialice `MobileServiceClient.SyncContext` en un almacén local. A continuación, obtenga una referencia a la tabla mediante la interfaz `IMobileServiceSyncTable`. En este tutorial se utiliza SQLite para el almacén local.

1. Instale el tiempo de ejecución de SQLite para Windows 8.1 y Windows Phone 8.1.

    * **Tiempo de ejecución de Windows 8.1:** Instalar [SQLite para Windows 8.1].
    * **Windows Phone 8.1:** Instalar [SQLite para Windows Phone 8.1].

    >[AZURE.NOTE]Estas instrucciones también funcionarán para los proyectos de Windows 10 UAP, pero en su lugar debería instalar [SQLite para Windows 10].

2. En Visual Studio, abra el proyecto que completó en el tutorial [Creación de una aplicación para Windows]. Instale paquete NuGet **Microsoft.Azure.Mobile.Client.SQLiteStore** para el tiempo de ejecución de Windows 8.1 y los proyectos de Windows Phone 8.1. Después, agregue la referencia de NuGet a los proyectos de Tienda Windows 8.1 y Windows Phone 8.1.

    >[AZURE.NOTE]Si la instalación crea una referencia adicional a una versión diferente de SQLite a la que tiene instalada, se genera un error de compilación. Debería resolver este error quitando el duplicado del nodo de **referencias** de sus proyectos.

3. En el Explorador de soluciones, haga clic con el botón derecho en **Referencias** para el tiempo de ejecución de Windows 8.1 y los proyectos de la plataforma Windows Phone 8.1 y asegúrese de que hay una referencia a SQLite, que se encuentra en la sección **Extensiones**.

    ![][1] </br>

    **Tiempo de ejecución de Windows 8.1**

    ![][11] </br>

    **Windows Phone 8.1**

4. SQLite es una biblioteca nativa y requiere que elija una arquitectura específica de la plataforma como **x86**, **x64** o **ARM**. No se admite **cualquier CPU**. En el Explorador de soluciones, haga clic en Solución que aparece en la parte superior, luego cambie el cuadro desplegable de la arquitectura del procesador a uno de la configuración compatible que desea probar.

    ![][13]

5. En el Explorador de soluciones, en el proyecto compartido, abra el archivo MainPage.cs. Quite la marca de comentario de las siguientes instrucciones using en la parte superior del archivo:

        using Microsoft.WindowsAzure.MobileServices.SQLiteStore;  // offline sync
        using Microsoft.WindowsAzure.MobileServices.Sync;         // offline sync

6. En el archivo MainPage.cs, comente la línea de código que inicializa `todoTable` como `IMobileServiceTable`. Quite el comentario de la línea de código que inicializa `todoTable` como `IMobileServiceSyncTable`:

        //private IMobileServiceTable<TodoItem> todoTable = App.MobileService.GetTable<TodoItem>();
        private IMobileServiceSyncTable<TodoItem> todoTable = App.MobileService.GetSyncTable<TodoItem>(); // offline sync

7. En MainPage.cs, en el área marcada como `Offline sync`, quite los comentarios de los métodos `InitLocalStoreAsync` y `SyncAsync`. El método `InitLocalStoreAsync` inicializa el contexto de sincronización de cliente con un almacén SQLite. En Visual Studio, puede seleccionar todas las líneas comentadas y usar el método abreviado de teclado **Ctrl**+**K**+**U** para quitar los comentarios.

	Observe que en `SyncAsync`, una operación push se ejecuta a partir de `MobileServiceClient.SyncContext` en lugar de `IMobileServicesSyncTable`. Esto ocurre porque el contexto realiza un seguimiento de los cambios que realizó el cliente para todas las tablas. Esto tiene por objeto incluir escenarios en los que existen relaciones entre tablas. Para más información sobre este comportamiento, consulte [Sincronización de datos sin conexión en Aplicaciones móviles de Azure].

        private async Task InitLocalStoreAsync()
        {
            if (!App.MobileService.SyncContext.IsInitialized)
            {
                var store = new MobileServiceSQLiteStore("localstore.db");
                store.DefineTable<TodoItem>();
                await App.MobileService.SyncContext.InitializeAsync(store);
            }

            await SyncAsync();
        }

        private async Task SyncAsync()
        {
            await App.MobileService.SyncContext.PushAsync();
            await todoTable.PullAsync("todoItems", todoTable.CreateQuery());
        }

8. En el controlador de eventos `OnNavigatedTo`, quite el comentario de la llamada a `InitLocalStoreAsync`:

        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            await InitLocalStoreAsync(); // offline sync
            await RefreshTodoItems();
        }

9. Quite la marca de comentario de las 3 llamadas a `SyncAsync` en los métodos `InsertTodoItem`, `UpdateCheckedTodoItem` y `ButtonRefresh_Click`:

        private async Task InsertTodoItem(TodoItem todoItem)
        {
            await todoTable.InsertAsync(todoItem);
            items.Add(todoItem);

            await SyncAsync(); // offline sync
        }

        private async Task UpdateCheckedTodoItem(TodoItem item)
        {
            await todoTable.UpdateAsync(item);
            items.Remove(item);
            ListItems.Focus(Windows.UI.Xaml.FocusState.Unfocused);

            await SyncAsync(); // offline sync
        }

        private async void ButtonRefresh_Click(object sender, RoutedEventArgs e)
        {
            ButtonRefresh.IsEnabled = false;

            await SyncAsync(); // offline sync
            await RefreshTodoItems();

            ButtonRefresh.IsEnabled = true;
        }

10. Agregue controladores de excepciones en el método `SyncAsync`. En una situación sin conexión, se genera `MobileServicePushFailedException` con `PushResult.Status == CancelledByNetworkError`.

        private async Task SyncAsync()
        {
            String errorString = null;

            try
            {
                await App.MobileService.SyncContext.PushAsync();
                await todoTable.PullAsync("todoItems", todoTable.CreateQuery()); // first param is query ID, used for incremental sync
            }

            catch (MobileServicePushFailedException ex)
            {
                errorString = "Push failed because of sync errors. You may be offine.\nMessage: " +
                  ex.Message + "\nPushResult.Status: " + ex.PushResult.Status.ToString();
            }
            catch (Exception ex)
            {
                errorString = "Pull failed: " + ex.Message +
                  "\n\nIf you are still in an offline scenario, " +
                  "you can try your Pull again when connected with your Mobile Serice.";
            }

            if (errorString != null)
            {
                MessageDialog d = new MessageDialog(errorString);
                await d.ShowAsync();
            }
        }

    En este ejemplo de `PullAsync`, se recuperan todos los registros del elemento `todoTable` remoto, pero también es posible filtrar registros si se pasa una consulta. El primer parámetro para `PullAsync` es un identificador de consulta que se utiliza en la sincronización incremental, que utiliza la marca de tiempo `UpdatedAt` para obtener únicamente los registros modificados desde la última sincronización. El identificador de consulta debe ser una cadena descriptiva que sea única para cada consulta lógica de la aplicación cliente. Para la desactivación de la sincronización incremental, pase `null` como identificador de la consulta. Así se recuperarán todos los registros de cada operación de extracción, lo cual es potencialmente ineficaz.

    Tenga en cuenta que `MobileServicePushFailedException` puede producirse por una operación de inserción y una de extracción. Puede producirse una extracción porque la operación de extracción ejecuta internamente una inserción para asegurarse de que todas las tablas junto con las relaciones son coherentes.

11. En Visual Studio, presione la tecla **F5** para volver a compilar y ejecutar la aplicación cliente. La aplicación se comportará igual que antes de que cambiara la sincronización sin conexión, porque realiza una operación de sincronización en las operaciones de inserción y actualización. Sin embargo, rellenará una base de datos local que se puede usar en un escenario sin conexión. Ahora que la base de datos local está llena, plantearemos un escenario sin conexión en la siguiente sección.

## <a name="update-sync"></a>Actualizar el comportamiento de sincronización de la aplicación cliente

En esta sección, modificará la aplicación cliente para simular un escenario sin conexión mediante la interrupción de la conexión al back-end de la aplicación móvil de Azure. Al agregar elementos de datos, el controlador de excepciones le informará de que la aplicación está funcionando en modo sin conexión con `PushResult.Status == CancelledByNetworkError`. Los elementos agregados se mantienen en el almacén local, pero no se sincronizan con el back-end de la aplicación móvil hasta que el usuario vuelve a estar en línea y ejecuta una inserción correcta en el back-end de la aplicación móvil de Azure.

1. Edite App.xaml.cs en el proyecto compartido. Convierta en comentario la inicialización de **MobileServiceClient** y agregue las siguientes líneas, que utilizan una dirección URL de una aplicación móvil no válida:

         public static MobileServiceClient MobileService = 
				new MobileServiceClient("https://your-service.azurewebsites.fail");

	Tenga en cuenta que, cuando la aplicación también usa autenticación, el inicio de sesión dará error. También puede mostrar el comportamiento sin conexión deshabilitando las redes Wi-Fi y móvil en el dispositivo o usar el modo avión.

2. Presione **F5** para compilar y ejecutar la aplicación. Observe que la sincronización no se pudo actualizar cuando se inició la aplicación.
3. Escriba algunos nuevos elementos de lista de tareas y haga clic en **Guardar** para cada uno. Se produce un error de inserción en cada uno con `PushResult.Status=CancelledByNetworkError`. Los nuevos elementos todo solo están en el almacén local hasta que se puedan insertar en el back-end de aplicación móvil. 
 
	Puede suprimir el cuadro de diálogo de excepción de `PushResult.Status=CancelledByNetworkError`. De este modo, la aplicación cliente se comportará como si estuviera conectada al back-end de la aplicación móvil y admitiera todas las operaciones de creación, lectura, actualización y eliminación (CRUD) sin problemas.

4. Cierre la aplicación y reiníciela para comprobar que los nuevos elementos que creó se mantienen en el almacén local.

5. (Opcional) En Visual Studio, abra el **Explorador de servidores**. Vaya a la base de datos en **Azure**->**Bases de datos SQL**. Haga clic con el botón derecho en la base de datos y seleccione **Abrir en el Explorador de objetos de SQL Server**. Ahora puede buscar la tabla de base de datos SQL y su contenido. Compruebe que no han cambiado los datos de la base de datos back-end.

6. (Opcional) Use una herramienta REST como Fiddler o Postman para consultar el back-end móvil mediante una consulta GET con la forma `https://your-mobile-app-backend-name.azurewebsites.net/tables/TodoItem`.

## <a name="update-online-app"></a>Actualizar la aplicación para volver a conectar el back-end de la aplicación móvil

En esta sección se vuelve a conectar al back-end de aplicación móvil. De este modo se simula que la aplicación pasa de un estado sin conexión a un estado en línea con el back-ende de aplicación móvil. Cuando ejecute la aplicación por primera vez, el controlador de eventos `OnNavigatedTo` llamará a `InitLocalStoreAsync`. Este llamará a su vez a `SyncAsync` para sincronizar el almacén local con la base de datos de back-end. Por lo tanto, la aplicación intentará sincronizarse en el inicio.

1. En el proyecto compartido, abra App.xaml.cs. Quite los comentarios de la inicialización anterior de `MobileServiceClient` para usar las direcciones URL correctas de la aplicación móvil y la puerta de enlace.

2. Presione la tecla **F5** para volver a compilar y ejecutar el proyecto. La aplicación sincroniza los cambios locales con el back-end de la aplicación móvil de Azure mediante las operaciones de inserción y extracción una vez que se ejecuta el controlador de eventos `OnNavigatedTo`.

3. (Opcional) Vea los datos actualizados mediante el Explorador de objetos de SQL Server o una herramienta REST como Fiddler. Observe que los datos se han sincronizado entre la base de datos del back-end de la aplicación móvil de Azure y el almacén local.

4. En la aplicación, haga clic en la casilla situada junto a algunos elementos para completarlos en el almacén local.

  `UpdateCheckedTodoItem` llama a `SyncAsync` para sincronizar cada elemento completo con el back-end de la aplicación móvil. `SyncAsync` llama a las operaciones de inserción y extracción. Sin embargo, tenga en cuenta que **cuando se ejecute una extracción en una tabla en la que el cliente haya realizado cambios, primero se ejecuta una inserción de forma automática en el contexto de sincronización de cliente**. De este modo, se asegurará de que todas las tablas del almacén local, junto con las relaciones, sean coherentes. Así que, en este caso, se podría haber eliminado la llamada a `PushAsync` porque esta se realiza automáticamente cuando se ejecuta una extracción. Este comportamiento puede producir una inserción inesperada si no se está al tanto de la operación. Para más información sobre este comportamiento, consulte [Sincronización de datos sin conexión en Aplicaciones móviles de Azure].


##Resumen

Para admitir las características sin conexión de servicios móviles, usamos la interfaz `IMobileServiceSyncTable` e inicializamos `MobileServiceClient.SyncContext` con un almacén local. En este caso, el almacén local era una base de datos SQLite.

Las operaciones CRUD normales para servicios móviles funcionan como si la aplicación siguiera conectada, pero todas las operaciones se producen en el almacén local.

Cuando se quiere sincronizar el almacén local con el servidor, se usan los métodos `IMobileServiceSyncTable.PullAsync` y `MobileServiceClient.SyncContext.PushAsync`.

*  Para insertar cambios en el servidor, llamamos a `IMobileServiceSyncContext.PushAsync()`. Este método es miembro de `IMobileServicesSyncContext` y no de la tabla de sincronización porque insertará cambios en todas las tablas.

    Solo los registros que se han modificado localmente de alguna forma (mediante operaciones CUD) se enviarán al servidor.

* Para extraer datos de una tabla del servidor en la aplicación, llamamos a `IMobileServiceSyncTable.PullAsync`.

    Una extracción siempre realiza primero una inserción de contexto de sincronización si el contexto de sincronización ha realizado un seguimiento de los cambios de esa tabla. De este modo, se asegurará de que todas las tablas del almacén local sean coherentes con las relaciones.

    También hay sobrecargas de `PullAsync()` que permiten que una consulta se especifique para filtrar los datos que se almacenan en el cliente. Si no se pasa ninguna consulta, `PullAsync()` extraerá todas las filas de la tabla (o consulta) correspondiente. Puede pasar la consulta para filtrar solo los cambios con los que su aplicación debe sincronizarse.

* Para habilitar la sincronización incremental, pase un identificador de consulta a `PullAsync()`. El identificador de consulta se utiliza para almacenar la última marca de tiempo actualizada de los resultados de la última operación de extracción. El identificador de la consulta debe ser una cadena descriptiva que sea única para cada consulta lógica en la aplicación. Si la consulta tiene un parámetro, el mismo valor de parámetro podría formar parte del identificador de consulta.

    Por ejemplo, si está filtrando por userid, el identificador de consulta único podría ser:

        await PullAsync("todoItems" + userid, syncTable.Where(u => u.UserId = userid));

    Si desea cancelar la sincronización incremental, pase `null` como identificador de consulta. En este caso, se recuperarán todos los registros en cada llamada a `PullAsync`, que es potencialmente ineficaz.

* De lo contrario, la aplicación podría llamar periódicamente a `IMobileServiceSyncTable.PurgeAsync()` para purgar el almacén local.


## Recursos adicionales

* [Sincronización de datos sin conexión en Aplicaciones móviles de Azure]

* [Cloud Cover: sincronización sin conexión en Servicios móviles de Azure] (nota: el vídeo trata sobre Servicios móviles, pero la sincronización sin conexión funciona de forma similar en Aplicaciones móviles de Azure)

* [Azure Friday: Aplicaciones habilitadas sin conexión en Servicios móviles de Azure]

<!-- Anchors. -->
[Update the app to support offline features]: #enable-offline-app
[Update the sync behavior of the app]: #update-sync
[Update the app to reconnect your Mobile Apps backend]: #update-online-app
[Next Steps]: #next-steps

<!-- Images -->
[1]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/app-service-mobile-add-reference-sqlite-dialog.png
[11]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/app-service-mobile-add-wp81-reference-sqlite-dialog.png
[13]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/cpu-architecture.png


<!-- URLs. -->
[Sincronización de datos sin conexión en Aplicaciones móviles de Azure]: ../app-service-mobile-offline-data-sync.md
[create a windows app]: ../app-service-mobile-windows-store-dotnet-get-started.md
[Creación de una aplicación de Windows]: ../app-service-mobile-windows-store-dotnet-get-started.md
[Creación de una aplicación para Windows]: ../app-service-mobile-windows-store-dotnet-get-started.md
[SQLite para Windows 8.1]: http://go.microsoft.com/fwlink/?LinkID=716919
[SQLite para Windows Phone 8.1]: http://go.microsoft.com/fwlink/?LinkID=716920
[SQLite para Windows 10]: http://go.microsoft.com/fwlink/?LinkID=716921

[sqlite store nuget]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.SQLiteStore/
 
[Cloud Cover: sincronización sin conexión en Servicios móviles de Azure]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Aplicaciones habilitadas sin conexión en Servicios móviles de Azure]: http://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/

<!---HONumber=AcomDC_1203_2015--->