<properties
	pageTitle="Uso de datos sin conexión en Servicios móviles (Xamarin iOS) | Microsoft Azure"
	description="Obtenga información acerca de cómo usar Servicios móviles de Azure para almacenar en caché y sincronizar datos sin conexión en su aplicación Xamarin iOS"
	documentationCenter="xamarin"
	authors="lindydonna"
	editor="wesmc"
	manager="dwrede"
	services="mobile-services"/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="donnam"/>

# Uso de la sincronización de datos sin conexión en servicios móviles

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-offline](../../includes/mobile-services-selector-offline.md)]

Este tema le guía a través de las funcionalidades de sincronización sin conexión de Servicios móviles de Azure en la aplicación de inicio rápido de la lista de tareas. La sincronización sin conexión le permite crear fácilmente aplicaciones que se pueden utilizar incluso si el usuario final no tiene acceso a la red.

La sincronización sin conexión tiene varios usos posibles:

* Mejore la capacidad de respuesta de las aplicaciones almacenando en caché datos de servidor de forma local en el dispositivo.
* Hacer que las aplicaciones sean resistentes ante una conectividad de red intermitente.
* Permitir a los usuarios finales crear y modificar datos incluso cuando no hay acceso de red, proporcionando escenarios con muy poca o ninguna conectividad.
* Sincronizar datos entre diferentes dispositivos y detectar conflictos cuando dos dispositivos modifican el mismo registro.

>[AZURE.NOTE] Necesita una cuenta de Azure para completar este tutorial. Si no dispone de ninguna cuenta, puede registrarse para obtener una versión de evaluación de Azure y conseguir hasta 10 servicios móviles gratuitos que podrá seguir usando incluso después de que finalice la evaluación. Para obtener más información, consulte <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Evaluación gratuita de Azure</a>.
>
> Si es su primera experiencia con Servicios móviles, primero deberá completar el tutorial [Introducción a los Servicios móviles].

Este tutorial le guiará a través de estos pasos básicos:

1. [Revisión del código de sincronización de Servicios móviles]
2. [Actualización del comportamiento de sincronización de la aplicación]
3. [Actualización de la aplicación para volver a conectar el servicio móvil]

Este tutorial requiere lo siguiente:

* Visual Studio con la [extensión Xamarin] **o** [Xamarin Studio] en OS X.
* XCode 4.5 y iOS 6.0 (o versiones posteriores).
* Finalización del tutorial [Introducción a los Servicios móviles].

## <a name="review-offline"></a>Revisión del código de sincronización de Servicios móviles

La sincronización sin conexión de Servicios móviles de Azure permite a los usuarios finales interactuar con una base de datos local cuando la red no está accesible. Para usar estas características en la aplicación, inicialice `MobileServiceClient.SyncContext` en un almacén local. A continuación, obtenga una referencia a la tabla mediante la interfaz `IMobileServiceSyncTable`. Esta sección le guía a través del código relacionado con la sincronización sin conexión en `QSTodoService.cs`.

1. En Visual Studio, abra el proyecto que completó en el tutorial [Introducción a los Servicios móviles]. Abra el archivo `QSTodoService.cs`.

2. Tenga en cuenta que el tipo del miembro `todoTable` es `IMobileServiceSyncTable`. La sincronización sin conexión usa esta interfaz de tabla de sincronización en lugar de `IMobileServiceTable`. Cuando se utiliza una tabla de sincronización, todas las operaciones van al almacén local y solo se sincronizan con el servicio remoto con operaciones de inserción y extracción explícitas.

    Para obtener una referencia a una tabla de sincronización, se utiliza el método `GetSyncTable()`. Para quitar la funcionalidad de sincronización sin conexión, se utilizaría `GetTable()` en su lugar.

3. Antes de poder realizar cualquier operación de tabla, se debe inicializar el almacén local. Esto se hace en el método `InitializeStoreAsync`:

        public async Task InitializeStoreAsync()
        {
            var store = new MobileServiceSQLiteStore(localDbPath);
            store.DefineTable<ToDoItem>();

            // Uses the default conflict handler, which fails on conflict
            await client.SyncContext.InitializeAsync(store);
        }

    Esto crea un almacén local mediante la clase `MobileServiceSQLiteStore`, que se proporciona en el SDK de Servicios móviles. También puede proporcionar una implementación de otro almacén local mediante la implementación de `IMobileServiceLocalStore`.

    El método `DefineTable` crea una tabla en el almacén local que coincide con los campos del tipo proporcionado, `ToDoItem` en este caso. El tipo no tiene que incluir todas las columnas que se encuentran en la base de datos remota, es posible almacenar solo un subconjunto de columnas.

    Esta sobrecarga de `InitializeAsync` utiliza el controlador de conflictos predeterminado, que produce un error cuando hay un conflicto. Para proporcionar un controlador de conflictos personalizado, consulte el tutorial [Control de conflictos con compatibilidad sin conexión para Servicios móviles].

4. El método `SyncAsync` desencadena la operación de sincronización real:

        public async Task SyncAsync()
        {
            try
            {
                await client.SyncContext.PushAsync();
                await todoTable.PullAsync("allTodoItems", todoTable.CreateQuery()); // query ID is used for incremental sync
            }

            catch (MobileServiceInvalidOperationException e)
            {
                Console.Error.WriteLine(@"Sync Failed: {0}", e.Message);
            }
        }

    En primer lugar, hay una llamada a `IMobileServiceSyncContext.PushAsync()`. Este método es miembro de `IMobileServicesSyncContext` y no de la tabla de sincronización porque insertará cambios en todas las tablas. Solo los registros que se han modificado localmente de alguna forma (mediante operaciones CUD) se enviarán al servidor.

    A continuación, el método llama a `IMobileServiceSyncTable.PullAsync()` para extraer datos de una tabla en el servidor a la aplicación. Tenga en cuenta que si hay cambios pendientes en el contexto de sincronización, una extracción siempre realiza primero una inserción. De este modo, se asegurará de que permanezcan sincronizadas todas las tablas del almacén local con las relaciones. En este caso, hemos llamado a la inserción explícitamente.

    En este ejemplo, recuperamos todos los registros de la tabla `TodoItem` remota, pero también es posible filtrar registros pasando una consulta. El primer parámetro para `PullAsync()` es un identificador de consulta que se utiliza en la sincronización incremental, que utiliza la marca de tiempo `UpdatedAt` para obtener solo aquellos registros modificados desde la última sincronización. El identificador de la consulta debe ser una cadena descriptiva que sea única para cada consulta lógica en la aplicación. Para la desactivación de la sincronización incremental, pase `null` como identificador de la consulta. Así se recuperarán todos los registros de cada operación de extracción, lo cual es potencialmente ineficaz.

    >[AZURE.NOTE] Para quitar registros del almacén local del dispositivo cuando se han eliminado de la base de datos del servicio móvil, debe habilitar la [eliminación temporal]. De lo contrario, la aplicación podría llamar periódicamente a `IMobileServiceSyncTable.PurgeAsync()` para purgar el almacén local.

    Tenga en cuenta que `MobileServicePushFailedException` puede producirse por una operación de inserción y una de extracción. El siguiente tutorial, [Control de conflictos con compatibilidad sin conexión para servicios móviles], muestra cómo manejar estas excepciones relacionadas con la sincronización.

5. En la clase `QSTodoService`, se llama al método `SyncAsync()` después de las operaciones que modifican datos, `InsertTodoItemAsync()` y `CompleteItemAsync`. También se le llama desde `RefreshDataAsync()`, de modo que el usuario obtiene los datos más recientes cada vez que realizan los gestos de actualización. La aplicación también lleva a cabo una sincronización en el inicio, ya que `QSTodoListViewController.ViewDidLoad()` llama a `RefreshDataAsync()`.

    Dado que `SyncAsync()` se llama cada vez que se modifican datos, esta aplicación supone que el usuario está en línea siempre que se editan datos. En la siguiente sección, se actualizará la aplicación para que los usuarios puedan editar incluso cuando estén sin conexión.

## <a name="update-sync"></a>Actualización del comportamiento de sincronización de la aplicación

En esta sección, modificará la aplicación para que no se sincronice en el inicio de la aplicación o en las operaciones de inserción o actualización, sino solo cuando se presione el botón Actualizar. De este modo, interrumpirá la conexión de la aplicación con el servicio móvil para simular un escenario sin conexión. Al agregar elementos de datos, estos se mantendrán en el almacén local, pero no se sincronizarán de inmediato con el servicio móvil.

1. Abra `QSTodoService.cs`. Comente las llamadas a `SyncAsync()` en los siguientes métodos:

    - `InsertTodoItemAsync`
    - `CompleteItemAsync`
    - `RefreshDataAsync`

    Ahora, `RefreshAsync()` solo cargará los datos del almacén local, pero no podrá conectar con el back-end de aplicación.

2. En `QSTodoService.cs`, convierta en comentario las definiciones de los miembros `applicationURL` y `applicationKey`. Agregue las líneas siguientes, que hacen referencia a una dirección URL de servicio móvil no válido:

        const string applicationURL = @"https://your-mobile-service.azure-mobile.xxx/";
        const string applicationKey = @"AppKey";

3. Para asegurarse de que los datos se sincronizan cuando se realiza el movimiento de actualización, modifique el método `QSTodoListViewController.RefreshAsync()`. Agregue una llamada a `SyncAsync()` antes de llamar a `RefreshDataAsync()`:

        private async Task RefreshAsync ()
        {
            RefreshControl.BeginRefreshing ();

            await todoService.SyncAsync();
            await todoService.RefreshDataAsync (); // add this line

            RefreshControl.EndRefreshing ();

            TableView.ReloadData ();
        }

4. Compile y ejecute la aplicación. Agregue nuevos elementos a la lista de tareas. Estos nuevos elementos solo existirán en el almacén local hasta que se puedan insertar en el servicio móvil. La aplicación cliente se comporta como si estuviera conectada al servicio móvil y admite todas las operaciones de creación, lectura, actualización y eliminación (CRUD).

5. Cierre la aplicación y reiníciela para comprobar que los nuevos elementos que creó se mantienen en el almacén local.

## <a name="update-online-app"></a>Actualización de la aplicación para volver a conectar el servicio móvil

En esta sección se vuelve a conectar la aplicación al servicio móvil. De este modo se simula que la aplicación pasa de un estado sin conexión a un estado en línea con el servicio móvil. Al realizar el gesto de la actualización, los datos se sincronizarán con el servicio móvil.

1. Abra `QSTodoService.cs`. Quite la dirección URL de servicio móvil no válida y vuelva a agregar la clave de dirección URL y aplicación correcta.

2. Recompile y ejecute la aplicación. Observe que los datos tienen el mismo aspecto que en el escenario sin conexión aunque la aplicación ahora está conectada con el servicio móvil. Esto se debe a que la aplicación siempre funciona con el elemento `IMobileServiceSyncTable` que apunta al almacén local.

3. Inicie sesión en el [Portal de Azure clásico] y consulte la base de datos del servicio móvil. Si el servicio usa el back-end de JavaScript, puede examinar los datos en la pestaña **Datos** del servicio móvil.

    Si usa el back-end de .NET para el servicio móvil, en Visual Studio, vaya a **Explorador de servidores** -> **Azure** -> **Bases de datos SQL**. Haga clic con el botón derecho en la base de datos y seleccione **Abrir en el Explorador de objetos de SQL Server**.

    Observe que los datos *no* se han sincronizado entre la base de datos y el almacén local.

4. En la aplicación, realice el gesto de actualización desplegando la lista de elementos. Esto hace que la aplicación llame a `RefreshDataAsync()`, que a su vez llama a `SyncAsync()`. Esto llevará a cabo las operaciones de inserción y extracción, primero enviando los elementos del almacén local al servicio móvil y después recuperando nuevos datos desde el servicio.

5. Compruebe la base de datos para el servicio móvil para confirmar que los cambios se han sincronizado.

##Resumen

[AZURE.INCLUDE [mobile-services-offline-summary-csharp](../../includes/mobile-services-offline-summary-csharp.md)]

## Pasos siguientes

* [Uso del cliente del componente Xamarin para servicios móviles de Azure]

<!-- Anchors. -->
[Revisión del código de sincronización de Servicios móviles]: #review-offline
[Actualización del comportamiento de sincronización de la aplicación]: #update-sync
[Actualización de la aplicación para volver a conectar el servicio móvil]: #update-online-app

<!-- Images -->

<!-- URLs. -->
[Control de conflictos con compatibilidad sin conexión para Servicios móviles]: mobile-services-xamarin-ios-handling-conflicts-offline-data.md
[Introducción a los Servicios móviles]: mobile-services-ios-get-started.md
[Uso del cliente del componente Xamarin para servicios móviles de Azure]: partner-xamarin-mobile-services-how-to-use-client-library.md
[eliminación temporal]: mobile-services-using-soft-delete.md

[Xamarin Studio]: http://xamarin.com/download
[extensión Xamarin]: http://xamarin.com/visual-studio
[Portal de Azure clásico]: https://manage.windowsazure.com

<!---HONumber=AcomDC_0128_2016-->