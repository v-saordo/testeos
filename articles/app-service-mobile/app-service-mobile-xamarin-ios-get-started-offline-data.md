<properties
    pageTitle="Activación de la sincronización sin conexión para la aplicación móvil de Azure (Xamarin iOS)"
    description="Obtenga información acerca de cómo usar la aplicación móvil de Servicios de aplicaciones para almacenar en caché y sincronizar datos sin conexión en su aplicación Xamarin iOS."
    documentationCenter="xamarin"
    authors="wesmc7777"
    manager="dwrede"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="article"
	ms.date="12/02/2015"
    ms.author="wesmc"/>

# Activación de la sincronización sin conexión para la aplicación móvil Xamarin.iOS

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

## Información general

Este tutorial presenta la característica de sincronización sin conexión de Aplicaciones móviles de Azure para Xamarin.iOS. La sincronización sin conexión permite a los usuarios finales interactuar con una aplicación móvil (ver, agregar o modificar datos), incluso cuando no hay ninguna conexión de red. Los cambios se almacenan en una base de datos local; una vez que el dispositivo se vuelve a conectar, estos cambios se sincronizan con el servicio remoto.

En este tutorial, actualizará el proyecto de aplicación Xamarin.iOS del tutorial [Creación de una aplicación Xamarin iOS] para conseguir compatibilidad con las características sin conexión de Aplicaciones móviles de Azure. Si no usa el proyecto de servidor de inicio rápido descargado, debe agregar paquetes de extensión de acceso de datos al proyecto. Para obtener más información acerca de los paquetes de extensión de servidor, consulte [Trabajar con el SDK del servidor back-end de .NET para Aplicaciones móviles de Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Para obtener más información acerca de la característica de sincronización sin conexión, consulte el tema [Sincronización de datos sin conexión en Aplicaciones móviles de Azure].

## Requisitos

Este tutorial requiere lo siguiente:

* Visual Studio 2013
* Visual Studio con la [extensión Xamarin] **o** [ Xamarin Studio] en OS X.
* Finalización del tutorial [Creación de una aplicación Xamarin iOS]. Este tutorial usa la aplicación completa que se explicó en ese tutorial.

## Revisión del código de sincronización de cliente

El proyecto de cliente de Xamarin que descargó cuando completó el tutorial [Creación de una aplicación Xamarin iOS] ya contiene el código que admite la sincronización sin conexión con una base de datos SQLite local. Esta es una breve descripción de lo que ya está incluido en el código del tutorial. Para obtener información general conceptual de la característica, consulte [Sincronización de datos sin conexión en Aplicaciones móviles de Azure].

* Antes de poder realizar cualquier operación de tabla, se debe inicializar el almacén local. La base de datos del almacén local se inicializa cuando `QSTodoListViewController.ViewDidLoad()` ejecuta `QSTodoService.InitializeStoreAsync()`. Esto crea una nueva base de datos SQLite local mediante la clase `MobileServiceSQLiteStore` que proporciona el SDK del cliente de la aplicación móvil de Azure. 

	El método `DefineTable` crea una tabla en el almacén local que coincide con los campos del tipo proporcionado, `ToDoItem` en este caso. El tipo no tiene que incluir todas las columnas que se encuentran en la base de datos remota. Es posible almacenar solo un subconjunto de columnas.

		// QSTodoService.cs

        public async Task InitializeStoreAsync()
        {
            var store = new MobileServiceSQLiteStore(localDbPath);
            store.DefineTable<ToDoItem>();

            // Uses the default conflict handler, which fails on conflict
            await client.SyncContext.InitializeAsync(store);
        }


* El miembro `todoTable` de `QSTodoService` es del tipo `IMobileServiceSyncTable` en lugar de `IMobileServiceTable`. Esto dirige todas las operaciones de tabla de creación, lectura, actualización y eliminación (CRUD) a la base de datos del almacén local.
 
	Para decidir cuándo se deben integrar esos cambios en el back-end de la aplicación móvil de Azure, llame a `IMobileServiceSyncContext.PushAsync()` con el contexto de sincronización para la conexión de cliente. El contexto de sincronización ayuda a mantener las relaciones entre tablas mediante el seguimiento y la inserción de los cambios en todas las tablas modificadas por una aplicación cliente cuando se llama a `PushAsync`.

	El código proporcionado llama a `QSTodoService.SyncAsync()` para sincronizarse cada vez que se actualiza la lista todoitem o se agrega o completa un todoitem. De esta forma, se sincroniza con cada cambio que ejecuta una inserción en el contexto de sincronización y una extracción en la tabla de sincronización. Sin embargo, es importante tener en cuenta que si se ejecuta una extracción en una tabla que tiene actualizaciones locales pendientes seguidas por el contexto, la operación de extracción primero activará de forma automática una inserción de contexto. Por eso, en estos casos (actualización y finalización de elementos), puede omitir la llamada explícita a `PushAsync`. Es redundante.

    En el ejemplo proporcionado, se consultan todos los registros de la tabla `TodoItem` remota, pero también es posible filtrar registros pasando un identificador de consulta y una consulta a `PushAsync`. Para obtener más información, consulte la sección *Sincronización incremental* en [Sincronización de datos sin conexión en Aplicaciones móviles de Azure].

	<!-- Need updated conflict handling info : `InitializeAsync` uses the default conflict handler, which fails whenever there is a conflict. To provide a custom conflict handler, see the tutorial [Handling conflicts with offline support for Mobile Services].
-->	// QSTodoService.cs

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


## Ejecución de la aplicación cliente

Ejecute la aplicación cliente al menos una vez para rellenar la base de datos del almacén local. En la siguiente sección, simulará un escenario sin conexión y modificará los datos del almacén local mientras la aplicación está sin conexión.


## Actualización del comportamiento de sincronización de la aplicación cliente

En esta sección, modificará el proyecto de cliente para simular un escenario sin conexión usando una dirección URL de aplicación no válida para el back-end. Al agregar o cambiar elementos de datos, estos cambios se conservan en el almacén local, pero no se sincronizan con el almacén de datos de back-end hasta que se restablezca la conexión.

1. En la parte superior de `QSTodoService.cs`, cambie la inicialización de `applicationURL` para que apunte a una dirección URL no válida:

        const string applicationURL = @"https://your-service.azurewebsites.xxx/"; 


2. Agregue un `catch` adicional para la clase `Exception` en `QSTodoService.SyncAsync`, que escribirá el mensaje de excepción en la consola.

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
            catch (Exception ex)
            {
                Console.Error.WriteLine(@"Exception: {0}", ex.Message);
            }
        }

3. Compile y ejecute la aplicación cliente. Agregue algunas tareas pendientes y observe la excepción registrada en la consola para cada intento de sincronización con el back-end de la aplicación móvil. Estos nuevos elementos solo existirán en el almacén local hasta que se puedan insertar en el back-end móvil. La aplicación cliente se comporta como si estuviera conectada al back-end y admite todas las operaciones de creación, lectura, actualización y eliminación (CRUD).

4. Cierre la aplicación y reiníciela para comprobar que los nuevos elementos que creó se mantienen en el almacén local.

5. (Opcional) Use Visual Studio para ver la tabla de base de datos SQL de Azure y observar que los datos de la base de datos de back-end no han cambiado.

	En Visual Studio, abra el **Explorador de servidores**. Vaya a la base de datos en **Azure**->**Bases de datos SQL**. Haga clic con el botón derecho en la base de datos y seleccione **Abrir en el Explorador de objetos de SQL Server**. Ahora puede buscar la tabla de base de datos SQL y su contenido.

6. (Opcional) Use una herramienta REST como Fiddler o Postman para consultar el back-end móvil mediante una consulta GET con la forma `https://your-mobile-app-backend-name.azurewebsites.net/tables/TodoItem`.

## Actualización de la aplicación cliente para volver a conectar el back-end móvil

En esta sección se volverá a conectar la aplicación al back-end móvil, que simula que la aplicación vuelve a un estado en línea. Al realizar el gesto de la actualización, los datos se sincronizarán con el back-end móvil.

1. Abra `QSTodoService.cs`. Corrija `applicationURL` y `gatewayURL` para que apunten a las direcciones URL correctas.

2. Vuelva a compilar la aplicación cliente y ejecútela. La aplicación intenta sincronizarse con el back-end de la aplicación móvil de Azure después de iniciarse. Compruebe que no hay excepciones registradas en la consola de depuración.

3. (Opcional) Vea los datos actualizados mediante el Explorador de objetos de SQL Server o una herramienta REST como Fiddler. Observe que los datos se han sincronizado entre la base de datos del back-end de la aplicación móvil de Azure y el almacén local.

    Observe que los datos se han sincronizado entre la base de datos y el almacén local, y contienen los elementos que agregó mientras la aplicación estaba desconectada.

## Recursos adicionales

* [Sincronización de datos sin conexión en Aplicaciones móviles de Azure]

* [Descripción de la nube: Sincronización sin conexión en Servicios móviles de Azure] (nota: el vídeo trata sobre Servicios móviles, pero la sincronización sin conexión funciona de forma similar en Aplicaciones móviles de Azure)

<!-- ##Summary

[AZURE.INCLUDE [mobile-services-offline-summary-csharp](../../includes/mobile-services-offline-summary-csharp.md)]

## Next steps

* [Handling conflicts with offline support for Mobile Services]

* [How to use the Xamarin Component client for Azure Mobile Services]
 -->

<!-- Images -->

<!-- URLs. -->
[Creación de una aplicación Xamarin iOS]: ../app-service-mobile-xamarin-ios-get-started.md
[Sincronización de datos sin conexión en Aplicaciones móviles de Azure]: ../app-service-mobile-offline-data-sync.md

[How to use the Xamarin Component client for Azure Mobile Services]: ../partner-xamarin-mobile-services-how-to-use-client-library.md

[ Xamarin Studio]: http://xamarin.com/download
[extensión Xamarin]: http://xamarin.com/visual-studio
 
[Descripción de la nube: Sincronización sin conexión en Servicios móviles de Azure]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri

<!---HONumber=AcomDC_1203_2015--->