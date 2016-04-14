<properties
	pageTitle="Uso de datos sin conexión en la aplicación universal de Windows | Microsoft Azure"
	description="Obtenga información acerca de cómo usar Servicios móviles de Azure para almacenar en caché y sincronizar datos sin conexión en su aplicación universal de Windows"
	documentationCenter="mobile-services"
	authors="lindydonna"
	manager="dwrede"
	editor=""
	services="mobile-services"/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="02/11/2016"
	ms.author="donnam"/>

# Uso de la sincronización de datos sin conexión en servicios móviles

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-offline](../../includes/mobile-services-selector-offline.md)]

Este tutorial muestra cómo agregar compatibilidad sin conexión a una aplicación de tienda universal de Windows mediante los servicios móviles de Azure. La compatibilidad sin conexión permitirá interactuar con una base de datos local cuando la aplicación no disponga de conexión. Cuando la aplicación esté conectada a la base de datos de back-end, deberá sincronizar los cambios locales mediante las características sin conexión.

Si prefiere ver un vídeo, el clip que aparece a la derecha muestra los mismos pasos que este tutorial.

> [AZURE.VIDEO build-offline-apps-with-mobile-services]

En este tutorial, se actualiza el proyecto de aplicación universal del tutorial [Introducción a Servicios móviles] para ofrecer compatibilidad con las características sin conexión de los Servicios móviles de Azure. A continuación, agregará datos en un escenario sin conexión desconectado, sincronizará dichos elementos con la base de datos en línea e iniciará sesión en el [Portal de Azure clásico] para ver los cambios efectuados en los datos al ejecutar la aplicación.

>[AZURE.NOTE] Este tutorial está destinado a profundizar en el uso de Azure con los servicios móviles para almacenar y recuperar datos en una aplicación de la Tienda Windows. Si es su primera experiencia con servicios móviles, primero deberá completar el tutorial [Introducción a los servicios móviles].

##Requisitos previos

Este tutorial requiere lo siguiente:

* Visual Studio 2013 en Windows 8.1.
* Finalización del tutorial [Introducción a los servicios móviles].
* [SDK de servicios móviles de Azure versión 1.3.0 (o posterior)][Mobile Services SDK Nuget]
* [Almacén de SQLite de servicios móviles de Azure versión 1.0.0 (o posterior)][SQLite store nuget]
* [SQLite para Windows 8.1](http://www.sqlite.org/download.html)
* Una cuenta de Azure. Si no dispone de ninguna cuenta, puede registrarse para obtener una versión de evaluación de Azure y conseguir hasta 10 servicios móviles gratuitos que podrá seguir usando incluso después de que finalice la evaluación. Para obtener más información, consulte [Evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28).

## <a name="enable-offline-app"></a>Actualización de la aplicación para que admita características sin conexión

Las características sin conexión de Servicios móviles de Azure permiten interactuar con una base de datos local cuando el usuario se encuentra en un escenario sin conexión con su servicio móvil. Para usar estas características en la aplicación, inicialice `MobileServiceClient.SyncContext` en un almacén local. A continuación, obtenga una referencia a la tabla mediante la interfaz `IMobileServiceSyncTable`. En este tutorial se utiliza SQLite para el almacén local.

>[AZURE.NOTE] Puede omitir esta sección y solo obtener el proyecto de ejemplo que ya tiene compatibilidad sin conexión desde el repositorio de muestras de GitHub para Servicios móviles. El proyecto de ejemplo con compatibilidad sin conexión habilitado se encuentra aquí: [Ejemplo sin conexión de lista de tareas].

1. Instale el tiempo de ejecución de SQLite para Windows 8.1 y Windows Phone 8.1.

    * **Tiempo de ejecución de Windows 8.1:** Instalar [SQLite para Windows 8.1].
    * **Windows Phone 8.1:** Instalar [SQLite para Windows Phone 8.1].

    >[AZURE.NOTE] Si utiliza Internet Explorer, es posible que al hacer clic en el vínculo para instalar SQLite se le solicite descargar .vsix como archivo .zip. Guarde el archivo en una ubicación del disco duro con la extensión .vsix en lugar de .zip. A continuación, haga doble clic en el archivo .vsix en el Explorador de Windows para ejecutar la instalación.

2. En Visual Studio, abra el proyecto que completó en el tutorial [Introducción a los Servicios móviles]. Instale paquete NuGet **WindowsAzure.MobileServices.SQLiteStore** para el tiempo de ejecución de Windows 8.1 y los proyectos de Windows Phone 8.1.

    * **Windows 8.1:** En el Explorador de soluciones, haga clic con el botón secundario del mouse en el proyecto de Windows 8.1 y haga clic en **Administrar paquetes de Nuget** para ejecutar el Administrador de paquetes de NuGet. Busque **SQLiteStore** para instalar el paquete `WindowsAzure.MobileServices.SQLiteStore`.
    * **Windows Phone 8.1:** Haga clic con el botón secundario del mouse en el proyecto de Windows Phone 8.1 y haga clic en **Administrar paquetes de Nuget** para ejecutar el Administrador de paquetes de NuGet. Busque **SQLiteStore** para instalar el paquete `WindowsAzure.MobileServices.SQLiteStore`.

    >[AZURE.NOTE] Si la instalación crea una referencia a una versión anterior de SQLite, solo podrá eliminar dicha referencia duplicada.

    ![][2]

2. En el Explorador de soluciones, haga clic con el botón derecho en **Referencias** para el tiempo de ejecución de Windows 8.1 y los proyectos de la plataforma Windows Phone 8.1 y asegúrese de que hay una referencia a SQLite, que se encuentra en la sección **Extensiones**.

    ![][1] </br>

    **Tiempo de ejecución de Windows 8.1**

    ![][11] </br>

    **Windows Phone 8.1**

3. El tiempo de ejecución de SQLite requiere cambiar la arquitectura del proyecto que se va a crear a **x86**, **x64** o **ARM**. No se admite **cualquier CPU**. En el Explorador de soluciones, haga clic en Solución que aparece en la parte superior, luego cambie el cuadro desplegable de la arquitectura del procesador a uno de la configuración compatible que desea probar.

    ![][13]

5. En el Explorador de soluciones, en el proyecto compartido, abra el archivo MainPage.cs. Quite la marca de comentario de las siguientes instrucciones using en la parte superior del archivo:

        using Microsoft.WindowsAzure.MobileServices.SQLiteStore;  // offline sync
        using Microsoft.WindowsAzure.MobileServices.Sync;         // offline sync

6. En MainPage.cs, comente la definición de `todoTable` y quite la marca de comentario de la que aparece en la línea siguiente que llama a `MobileServicesClient.GetSyncTable()`:

        //private IMobileServiceTable<TodoItem> todoTable = App.MobileService.GetTable<TodoItem>();
        private IMobileServiceSyncTable<TodoItem> todoTable = App.MobileService.GetSyncTable<TodoItem>(); // offline sync


7. En MainPage.cs, en el área marcada como `Offline sync`, quite los comentarios de los métodos `InitLocalStoreAsync` y `SyncAsync`. El método `InitLocalStoreAsync` inicializa el contexto de sincronización de cliente con un almacén SQLite.

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

10. Agregue controladores de excepciones en el método `SyncAsync`:

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
                errorString = "Push failed because of sync errors: " +
                  ex.PushResult.Errors.Count + " errors, message: " + ex.Message;
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

    En este ejemplo, recuperamos todos los registros en el `todoTable` remoto, pero también es posible filtrar registros pasando una consulta. El primer parámetro para `PullAsync` es un identificador de consulta que se utiliza en la sincronización incremental, que utiliza la marca de tiempo `UpdatedAt` para obtener únicamente los registros modificados desde la última sincronización. El identificador de la consulta debe ser una cadena descriptiva que sea única para cada consulta lógica en la aplicación. Para la desactivación de la sincronización incremental, pase `null` como identificador de la consulta. Así se recuperarán todos los registros de cada operación de extracción, lo cual es potencialmente ineficaz.

    >[AZURE.NOTE] * Para quitar registros del almacén local del dispositivo cuando se han eliminado de la base de datos del servicio móvil, debe habilitar la [eliminación temporal]. De lo contrario, la aplicación podría llamar periódicamente a `IMobileServiceSyncTable.PurgeAsync()` para purgar el almacén local.

    Tenga en cuenta que `MobileServicePushFailedException` puede producirse por una operación de inserción y una de extracción. Puede producirse una extracción porque la operación de extracción ejecuta internamente una inserción para asegurarse de que todas las tablas junto con las relaciones son coherentes. El siguiente tutorial, [Control de conflictos con compatibilidad sin conexión para servicios móviles], muestra cómo manejar estas excepciones relacionadas con la sincronización.

11. En Visual Studio, presione la tecla **F5** para volver a realizar la compilación y ejecute la aplicación. La aplicación se comportará igual que antes de que cambiara la sincronización sin conexión, porque realiza una operación de sincronización en las operaciones de inserción y actualización.

## <a name="update-sync"></a>Actualización del comportamiento de sincronización de la aplicación

En esta sección, modificará la aplicación para que no se sincronice en las operaciones de inserción y actualización, sino solo cuando esté presionado el botón **Actualizar**. De este modo, interrumpirá la conexión de la aplicación con el servicio móvil para simular un escenario sin conexión. Al agregar elementos de datos, estos se mantendrán en el almacén local, pero no se sincronizarán con el servicio móvil.

1. En el proyecto compartido, abra MainPage.cs. Modifique los métodos `InsertTodoItem` y `UpdateCheckedTodoItem` para convertir en comentario las llamadas a `SyncAsync`.

2. Edite App.xaml.cs en el proyecto compartido. Convierta en comentario la inicialización de **MobileServiceClient** y agregue las siguientes líneas, que utilizan una dirección URL de un servicio móvil no válido:

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://your-mobile-service.azure-mobile.xxx/",
            "AppKey"
        );

3. En `InitLocalStoreAsync()`, convierta en comentario la llamada a `SyncAsync()`, de modo que la aplicación no realice una sincronización en el inicio.

4. Presione **F5** para compilar y ejecutar la aplicación. Escriba algunos nuevos elementos de lista de tareas y haga clic en **Guardar** para cada uno. Los nuevos elementos todo solo están en el almacén local hasta que se puedan insertar en el servicio móvil. La aplicación cliente se comporta como si estuviera conectada al servicio móvil y admite todas las operaciones de creación, lectura, actualización y eliminación (CRUD).

5. Cierre la aplicación y reiníciela para comprobar que los nuevos elementos que creó se mantienen en el almacén local.

## <a name="update-online-app"></a>Actualización de la aplicación para volver a conectar el servicio móvil

En esta sección se vuelve a conectar la aplicación al servicio móvil. De este modo se simula que la aplicación pasa de un estado sin conexión a un estado en línea con el servicio móvil. Cuando se presione el botón de actualización, los datos se sincronizarán con el servicio móvil.

1. En el proyecto compartido, abra App.xaml.cs. Quite la marca de comentario de la inicialización anterior de `MobileServiceClient` para volver a agregar la dirección URL y la clave de aplicación del servicio móvil correcto.

2. Presione la tecla **F5** para volver a compilar y ejecutar el proyecto. Observe que los datos tienen el mismo aspecto que en el escenario sin conexión aunque la aplicación ahora está conectada con el servicio móvil. Esto se debe a que la aplicación siempre funciona con el elemento `IMobileServiceSyncTable` que apunta al almacén local.

3. Inicie sesión en el [Portal de Azure clásico] y consulte la base de datos del servicio móvil. Si el servicio usa el back-end de JavaScript para servicios móviles, puede examinar los datos en la pestaña **Data** del servicio móvil.

    Si usa el back-end de .NET para el servicio móvil, en Visual Studio, vaya a **Explorador de servidores** -> **Azure** -> **Bases de datos SQL**. Haga clic con el botón derecho en la base de datos y seleccione **Abrir en el Explorador de objetos de SQL Server**.

    Observe que los datos no se han sincronizado entre la base de datos y el almacén local.

    ![][6]

4. En la aplicación, presione el botón **Actualizar**. Esto hace que la aplicación llame a `PushAsync` y `PullAsync`. Esta operación de inserción envía los elementos de almacén local al servicio móvil, y a continuación, recupera los nuevos datos del servicio móvil.

    Una operación de inserción se ejecuta de `MobileServiceClient.SyncContext` y no de `IMobileServicesSyncTable`, e inserta cambios en todas las tablas asociadas a dicho contexto de sincronización. Esto tiene por objeto incluir escenarios en los que existen relaciones entre tablas.

    ![][7]

5. En la aplicación, haga clic en la casilla situada junto a algunos elementos para completarlos en el almacén local.

    ![][8]

6. Inserte de nuevo el botón **Actualizar**, lo que hace que se llame a `SyncAsync`. `SyncAsync` llama tanto a inserción como a extracción; sin embargo, en este caso, podríamos haber eliminado la llamada a `PushAsync`. Esto se debe a que toda **extracción siempre realiza primero una inserción**. De este modo, se asegurará de que todas las tablas del almacén local sean coherentes con las relaciones.

    ![][10]


##Resumen

[AZURE.INCLUDE [mobile-services-offline-summary-csharp](../../includes/mobile-services-offline-summary-csharp.md)]

## Pasos siguientes

* [Control de conflictos con compatibilidad sin conexión para Servicios móviles]

* [Uso de la eliminación temporal en Servicios móviles][Soft Delete]

<!-- Anchors. -->
[Update the app to support offline features]: #enable-offline-app
[Update the sync behavior of the app]: #update-sync
[Update the app to reconnect your mobile service]: #update-online-app
[Next Steps]: #next-steps

<!-- Images -->
[1]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-add-reference-sqlite-dialog.png
[2]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-sqlitestore-nuget.png
[6]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-data-browse.png
[7]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-data-browse2.png
[8]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-online-app-run2.png
[10]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-data-browse3.png
[11]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-add-wp81-reference-sqlite-dialog.png
[12]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/new-synchandler-class.png
[13]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/cpu-architecture.png


<!-- URLs. -->
[Control de conflictos con compatibilidad sin conexión para servicios móviles]: mobile-services-windows-store-dotnet-handling-conflicts-offline-data.md
[Ejemplo sin conexión de lista de tareas]: http://go.microsoft.com/fwlink/?LinkId=394777
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Getting Started]: ../mobile-services-dotnet-backend-windows-phone-get-started.md
[Introducción a Servicios móviles]: ../mobile-services-windows-store-get-started.md
[Introducción a los servicios móviles]: ../mobile-services-windows-store-get-started.md
[SQLite para Windows 8.1]: http://go.microsoft.com/fwlink/?LinkId=394776
[SQLite para Windows Phone 8.1]: http://go.microsoft.com/fwlink/?LinkId=397953
[Soft Delete]: mobile-services-using-soft-delete.md
[eliminación temporal]: mobile-services-using-soft-delete.md


[Mobile Services SDK Nuget]: http://www.nuget.org/packages/WindowsAzure.MobileServices/1.3.0
[SQLite store nuget]: http://www.nuget.org/packages/WindowsAzure.MobileServices.SQLiteStore/1.0.0
[Portal de Azure clásico]: https://manage.windowsazure.com

<!---HONumber=AcomDC_0218_2016-->