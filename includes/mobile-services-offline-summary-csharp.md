Para admitir las características sin conexión de servicios móviles, usamos la interfaz `IMobileServiceSyncTable` e inicializamos `MobileServiceClient.SyncContext` con un almacén local. En este caso, el almacén local era una base de datos SQLite.

Las operaciones CRUD normales para servicios móviles funcionan como si la aplicación siguiera conectada, pero todas las operaciones se producen en el almacén local.

Cuando quisimos sincronizar el almacén local con el servidor, usamos los métodos `IMobileServiceSyncTable.PullAsync` y `MobileServiceClient.SyncContext.PushAsync`.

*  Para insertar cambios en el servidor, llamamos a `IMobileServiceSyncContext.PushAsync()`. Este método es miembro de `IMobileServicesSyncContext` y no de la tabla de sincronización porque insertará cambios en todas las tablas.

    Solo los registros que se han modificado localmente de alguna forma (mediante operaciones CUD) se enviarán al servidor.
   
* Para extraer datos de una tabla del servidor en la aplicación, llamamos a `IMobileServiceSyncTable.PullAsync`.

    Una extracción siempre realiza primero una inserción. De este modo, se asegurará de que todas las tablas del almacén local sean coherentes con las relaciones.

    También hay sobrecargas de `PullAsync()` que permiten que una consulta se especifique para filtrar los datos que se almacenan en el cliente. Si no se pasa ninguna consulta, `PullAsync()` extraerá todas las filas de la tabla (o consulta) correspondiente. Puede pasar la consulta para filtrar solo los cambios con los que su aplicación debe sincronizarse.

* Para habilitar la sincronización incremental, pase un identificador de consulta a `PullAsync()`. El identificador de consulta se utiliza para almacenar la última marca de tiempo actualizada de los resultados de la última operación de extracción. El identificador de la consulta debe ser una cadena descriptiva que sea única para cada consulta lógica en la aplicación. Si la consulta tiene un parámetro, el mismo valor de parámetro debe formar parte del identificador de consulta.

    Por ejemplo, si está filtrando en userid, debe formar parte del identificador de consulta:

        await PullAsync("todoItems" + userid, syncTable.Where(u => u.UserId = userid));

    Si desea cancelar la sincronización incremental, pase `null` como identificador de consulta. En este caso, se recuperarán todos los registros en cada llamada a `PullAsync`, que es potencialmente ineficaz.

* Para quitar registros del almacén local del dispositivo cuando se han eliminado de la base de datos del servicio móvil, debe habilitar la [eliminación temporal]. De lo contrario, la aplicación podría llamar periódicamente a `IMobileServiceSyncTable.PurgeAsync()` para purgar el almacén local.

<!---HONumber=Oct15_HO3-->