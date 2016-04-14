<properties
	pageTitle="Introducción a la sincronización de datos sin conexión en Servicios móviles (iOS) | Microsoft Azure"
	description="Obtenga información acerca de cómo usar Servicios móviles de Azure para almacenar en caché y sincronizar datos sin conexión en su aplicación iOS"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""
	services="mobile-services"/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="01/12/2016"
	ms.author="krisragh;donnam"/>

# Introducción a la sincronización de datos sin conexión en Servicios móviles

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-offline](../../includes/mobile-services-selector-offline.md)]

La sincronización sin conexión le permite ver, agregar o modificar datos en una aplicación móvil, incluso cuando no hay ninguna conexión de red. En este tutorial, aprenderá cómo puede la aplicación almacenar automáticamente los cambios de una base de datos sin conexión local y sincronizar esos cambios siempre que vuelva a conectarse.

La sincronización sin conexión tiene varias ventajas:

* Mejora la capacidad de respuesta de las aplicaciones almacenando en caché datos de servidor de forma local en el dispositivo
* Hace que las aplicaciones sean resistentes ante una conectividad de red intermitente.
* Permite crear y modificar los datos incluso con poca o ninguna conectividad
* Sincroniza datos en varios dispositivos
* Detecta los conflictos cuando dos dispositivos modifican el mismo registro

> [AZURE.NOTE] Para completar este tutorial, deberá tener una cuenta de Azure. Si no dispone de ninguna cuenta, puede registrarse para obtener una versión de evaluación de Azure y acceder a [servicios móviles gratuitos que puede seguir usando incluso después de que finalice dicha evaluación](https://azure.microsoft.com/pricing/details/mobile-services/). Para obtener más información, consulte [Evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28 target="\_blank").

Este tutorial está basado en el [Tutorial de introducción a Servicios móviles], que debe completar primero. En primer lugar, revisemos el código relacionado con la sincronización sin conexión en el inicio rápido.

## <a name="review-sync"></a>Revisión del código de sincronización de Servicios móviles

La sincronización sin conexión de Servicios móviles de Azure permite a los usuarios finales interactuar con una base de datos local cuando la red no está accesible. Para utilizar estas características en su aplicación, inicialice el contexto de sincronización de `MSClient` y haga referencia a un almacén local. A continuación, obtenga una referencia a la tabla mediante la interfaz `MSSyncTable`.

* En **QSTodoService.m**, observe que el tipo del miembro `syncTable` es `MSSyncTable`. La sincronización sin conexión usa esto en lugar de `MSTable`. Cuando se utiliza una tabla de sincronización, todas las operaciones van al almacén local y solo se sincronizan con el servicio remoto con operaciones de inserción y extracción explícitas.

```
		@property (nonatomic, strong)   MSSyncTable *syncTable;
```

Para obtener una referencia a una tabla de sincronización, utilice el método `syncTableWithName`. Para quitar la funcionalidad de sincronización sin conexión, use `tableWithName` en su lugar.

* En **QSTodoService.m**, antes de que se realicen operaciones en las tablas, la tienda local se inicializa en `QSTodoService.init`:

```
		MSCoreDataStore *store = [[MSCoreDataStore alloc] initWithManagedObjectContext:context];
		self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:nil dataSource:store callback:nil];
```

Esto crea una tienda local mediante la interfaz `MSCoreDataStore`. Puede proporcionar otra tienda local mediante la implementación del protocolo `MSSyncContextDataSource`.

El primer parámetro de `initWithDelegate` especifica un controlador de conflictos, pero ya que hemos pasado `nil`, obtenemos el controlador de conflictos predeterminado que produce un error en cualquier conflicto. Para obtener más información sobre cómo implementar un controlador de conflictos personalizado, consulte [Control de conflictos con compatibilidad sin conexión para Servicios móviles].

* En **QSTodoService.m**, `syncData` primero inserta nuevos cambios y, a continuación, llama a `pullData` para obtener datos desde el servicio remoto. En `syncData`, llamamos primero a `pushWithCompletion` en el contexto de sincronización. Este método es miembro de `MSSyncContext` y no de la propia tabla de sincronización, porque insertará cambios en todas las tablas. Solo se envían al servidor los registros que se han modificado localmente de alguna forma (mediante operaciones de creación, actualización o eliminación). Al final de `syncData`, llamamos a la aplicación auxiliar `pullData`.

En este ejemplo, la operación de inserción no es estrictamente necesaria. Si hay cambios pendientes en el contexto de sincronización para la tabla que está realizando una operación de inserción, la extracción siempre realiza primero una inserción. Sin embargo, si tiene más de una tabla de sincronización, llame a la inserción explícitamente para tener coherencia entre las tablas.

```
      -(void)syncData:(QSCompletionBlock)completion
      {
          // push all changes in the sync context, then pull new data
          [self.client.syncContext pushWithCompletion:^(NSError *error) {
              [self logErrorIfNotNil:error];
              [self pullData:completion];
          }];
      }

```

* A continuación, en **QSTodoService.m**, `pullData` obtiene datos nuevos que coinciden con una consulta. `pullData` llama a `MSSyncTable.pullWithQuery` para recuperar datos remotos y almacenarlos localmente. `pullWithQuery` también le permite especificar una consulta para filtrar los registros que desea recuperar. En este ejemplo, la consulta recupera simplemente todos los registros en la tabla `TodoItem` remota.

El segundo parámetro para `pullWithQuery` es un identificador de consulta que se utiliza para la _sincronización incremental_. La sincronización incremental recupera solo aquellos registros modificados desde la última sincronización, mediante la marca de tiempo `UpdatedAt` del registro (llamada `ms_updatedAt` en el almacén local). El identificador de la consulta es una cadena descriptiva única para cada consulta lógica en la aplicación. Para la desactivación de la sincronización incremental, pase `nil` como identificador de la consulta. Esto resulta ineficaz, ya que se recuperarán todos los registros de cada operación de extracción.

```
      -(void)pullData:(QSCompletionBlock)completion
      {
          MSQuery *query = [self.syncTable query];

          // Pulls data from the remote server into the local table.
          // We're pulling all items and filtering in the view
          // query ID is used for incremental sync
          [self.syncTable pullWithQuery:query queryId:@"allTodoItems" completion:^(NSError *error) {
              [self logErrorIfNotNil:error];

              // Let the caller know that we have finished
              if (completion != nil) {
                  dispatch_async(dispatch_get_main_queue(), completion);
              }
          }];
      }
```


>[AZURE.NOTE] Para quitar registros del almacén local del dispositivo cuando se han eliminado de la base de datos del servicio móvil, habilite la [eliminación temporal]. De lo contrario, la aplicación podría llamar periódicamente a `MSSyncTable.purgeWithQuery` para purgar el almacén local.


* En **QSTodoService.m**, los métodos `addItem` y `completeItem` invocan a `syncData` después de modificar datos. En **QSTodoListViewController.m**, el método `refresh` también invoca a `syncData` para que la interfaz de usuario muestre los datos más recientes en cada actualización y en el inicio (`init` llama a `refresh`).

Dado que la aplicación llama a `syncData` siempre que modifica datos, la aplicación supone que está en línea siempre que modifica los datos de la aplicación.

## <a name="review-core-data"></a>Revisión del modelo Core Data

Cuando se usa el almacén sin conexión Core Data, tendrá que definir tablas y campos determinados en el modelo de datos. La aplicación de ejemplo ya incluye un modelo de datos con el formato correcto. En esta sección se le guiará a través de estas tablas y el modo de utilizarlas.

- Abra **QSDataModel.xcdatamodeld**. Hay cuatro tablas definidas: tres que utiliza el SDK y una para los propios elementos de la lista de tareas:

      * MS\_TableOperations: para el seguimiento de elementos que se sincronizarán con el servidor
      * MS\_TableOperationErrors: para realizar el seguimiento de los errores que se producen durante la sincronización sin conexión
      * MS\_TableConfig: para realizar el seguimiento de la hora de la última actualización de la última operación de sincronización para todas las operaciones de extracción
      * TodoItem: para almacenar elementos de lista de tareas. Las columnas de sistema **ms\_createdAt**, **ms\_updatedAt** y **ms\_version** son propiedades del sistema opcionales.

>[AZURE.NOTE] El SDK de Servicios móviles reserva los nombres de columna que empiezan por "**`ms_`**". No use este prefijo en elementos distintos de las columnas del sistema. De lo contrario, se modificarán los nombres de columna cuando se use el servicio remoto.

- Al utilizar la característica de sincronización sin conexión, debe definir las tablas del sistema, tal como se muestra a continuación.

    ### Tablas del sistema

    #### MS\_TableOperations

    | Atributo | Escriba |
    |-------------- |   ------    |
    | Id. (obligatorio) | Integer 64 |
    | itemId | Cadena |
    | propiedades | Binary Data |
    | table | Cadena |
    | tableKind | Integer 16 |

    #### MS\_TableOperationErrors

    | Atributo | Escriba |
    |-------------- | ----------  |
    | Id. (obligatorio) | String |
    | operationId | Integer 64 |
    | propiedades | Binary Data |
    | tableKind | Integer 16 |

    #### MS\_TableConfig


    | Atributo | Escriba |
    |-------------- | ----------  |
    | Id. (obligatorio) | String |
    | key | Cadena |
    | keyType | Integer 64 |
    | table | Cadena |
    | value | Cadena |

    ### Tabla de datos

    #### TodoItem

    | Atributo | Tipo | Nota: |
    |-------------- |  ------ | -------------------------------------------------------|
    | Id. (obligatorio) | String | clave principal en almacén remoto (requerido) |
    | complete | Booleano | todo item field |
    | text | Cadena | todo item field |
    | ms\_createdAt | Date | (opcional) maps to \_\_createdAt system property | | ms\_updatedAt | Date | (opcional) maps to \_\_updatedAt system property | | ms\_version | String | (opcional) used to detect conflicts, maps to \_\_version |



## <a name="setup-sync"></a>Cambio del comportamiento de sincronización de la aplicación

En esta sección, modificará la aplicación para que no se sincronice en el inicio de la aplicación o al insertar y actualizar elementos, sino solo cuando se realice el gesto de actualización.

* En **QSTodoListViewController.m**, cambie `viewDidLoad` para quitar la llamada a `[self refresh]` al final del método. Ahora, los datos no se sincronizarán con el servidor al inicio de la aplicación, sino que almacenarán tan solo localmente.

* En **QSTodoService.m**, modifique `addItem` para que no se sincronice tras la inserción del elemento. Quite el bloque `self syncData` y sustitúyalo por lo siguiente:

```
        if (completion != nil) {
            dispatch_async(dispatch_get_main_queue(), completion);
        }
```

* De forma similar, en **QSTodoService.m**, en `completeItem`, quite el bloque de `self syncData` y sustitúyalo por lo siguiente:

```
        if (completion != nil) {
            dispatch_async(dispatch_get_main_queue(), completion);
        }
```

## <a name="test-app"></a>Aplicación de prueba

En esta sección, desactivará el Wi-Fi en el simulador para crear un escenario sin conexión. Al agregar elementos de datos, estos se guardarán en el almacén local Core Data, pero no se sincronizarán con el servicio móvil.

1. Desactive la conexión a Internet en el equipo Mac. Desactivar Wi-Fi solo en el simulador de iOS puede no tener ningún efecto, ya que el simulador puede seguir usando la conexión a Internet del Mac host; por tanto, desactive Internet para el propio equipo. Esto simula un escenario sin conexión.

2. Agregue algunos elementos de la lista de pendientes o complete algunos elementos. Salga del simulador (o fuerce el cierre de la aplicación) y reinicie. Compruebe que se han guardado los cambios. Tenga en cuenta que los elementos de datos continúan mostrándose debido a que se almacenan en el almacén local Core Data.

3. Vea el contenido de la tabla TodoItem remota. Compruebe que los nuevos elementos _no_ se han sincronizado con el servidor.

   - Para el back-end de JavaScript, vaya al [Portal de Azure clásico](http://manage.windowsazure.com) y haga clic en la pestaña Datos para ver el contenido de la tabla `TodoItem`.
   - Para el back-end de .NET, vea el contenido de tabla con una herramienta SQL, como SQL Server Management Studio, o con un cliente REST como Fiddler o Postman.

4. Active la Wi-Fi en el simulador de iOS. A continuación, realice el gesto de actualización desplegando la lista de elementos. Verá una rueda con el progreso y el texto "Sincronizando...".

5. Vea los datos de TodoItem de nuevo. Ahora deberían aparecer los elementos de TodoItems nuevos y modificados.

## Resumen

Para admitir las características sin conexión de servicios móviles, usamos la interfaz `MSSyncTable` e inicializamos `MSClient.syncContext` con un almacén local. En este caso, el almacén local era una base de datos Core Data.

Cuando se utiliza un almacén local Core Data, debe definir varias tablas con las [propiedades del sistema de corrección][Review the Core Data model]. Las operaciones normales para servicios móviles funcionan como si la aplicación siguiera conectada, pero todas las operaciones se producen en el almacén local.

Para sincronizar el almacén local con el servidor, ha usado `MSSyncTable.pullWithQuery` y `MSClient.syncContext.pushWithCompletion`:

		* To push changes to the server, you called `pushWithCompletion`. This method is in `MSSyncContext` instead of the sync table because it will push changes across all tables. Only records that are modified in some way locally (through CUD operations) are be sent to the server.

		* To pull data from a table on the server to the app, you called `MSSyncTable.pullWithQuery`. A pull always issues a push first. This is to ensure all tables in the local store along with relationships remain consistent. `pullWithQuery` can also be used to filter the data that is stored on the client, by customizing the `query` parameter.

## Pasos siguientes

* [Control de conflictos con compatibilidad sin conexión para Servicios móviles]

* [Uso de la eliminación temporal en Servicios móviles][Soft Delete]

## Recursos adicionales

* [Descripción de la nube: Sincronización sin conexión en Servicios móviles de Azure]

* [Azure Friday: Aplicaciones habilitadas sin conexión en Servicios móviles de Azure] (Nota: las demostraciones son para Windows, pero las características tratadas son aplicables a todas las plataformas)

<!-- URLs. -->

[Get the sample app]: #get-app
[Review the Core Data model]: #review-core-data
[Review the Mobile Services sync code]: #review-sync
[Change the sync behavior of the app]: #setup-sync
[Test the app]: #test-app

[core-data-1]: ./media/mobile-services-ios-get-started-offline-data/core-data-1.png
[core-data-2]: ./media/mobile-services-ios-get-started-offline-data/core-data-2.png
[core-data-3]: ./media/mobile-services-ios-get-started-offline-data/core-data-3.png
[defining-core-data-main-screen]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-main-screen.png
[defining-core-data-model-editor]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-model-editor.png
[defining-core-data-tableoperationerrors-entity]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-tableoperationerrors-entity.png
[defining-core-data-tableoperations-entity]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-tableoperations-entity.png
[defining-core-data-tableconfig-entity]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-tableconfig-entity.png
[defining-core-data-todoitem-entity]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-todoitem-entity.png
[update-framework-1]: ./media/mobile-services-ios-get-started-offline-data/update-framework-1.png
[update-framework-2]: ./media/mobile-services-ios-get-started-offline-data/update-framework-2.png

[Core Data Model Editor Help]: https://developer.apple.com/library/mac/recipes/xcode_help-core_data_modeling_tool/Articles/about_cd_modeling_tool.html
[Creating an Outlet Connection]: https://developer.apple.com/library/mac/recipes/xcode_help-interface_builder/articles-connections_bindings/CreatingOutlet.html
[Build a User Interface]: https://developer.apple.com/library/mac/documentation/ToolsLanguages/Conceptual/Xcode_Overview/Edit_User_Interfaces/edit_user_interface.html
[Adding a Segue Between Scenes in a Storyboard]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardSegue.html#//apple_ref/doc/uid/TP40014225-CH25-SW1
[Adding a Scene to a Storyboard]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardScene.html

[Core Data]: https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/CoreData/cdProgrammingGuide.html
[Download the preview SDK here]: http://aka.ms/Gc6fex
[How to use the Mobile Services client library for iOS]: mobile-services-ios-how-to-use-client-library.md
[Offline iOS Sample]: https://github.com/Azure/mobile-services-samples/tree/master/TodoOffline/iOS/blog20140611
[Mobile Services sample repository on GitHub]: https://github.com/Azure/mobile-services-samples


[Get started with Mobile Services]: mobile-services-ios-get-started.md
[Control de conflictos con compatibilidad sin conexión para Servicios móviles]: mobile-services-ios-handling-conflicts-offline-data.md
[Soft Delete]: mobile-services-using-soft-delete.md
[eliminación temporal]: mobile-services-using-soft-delete.md

[Descripción de la nube: Sincronización sin conexión en Servicios móviles de Azure]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Aplicaciones habilitadas sin conexión en Servicios móviles de Azure]: http://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/

[Tutorial de introducción a Servicios móviles]: mobile-services-ios-get-started.md

<!---HONumber=AcomDC_0128_2016-->