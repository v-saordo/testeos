<properties
	pageTitle="Sincronización de datos sin conexión en Aplicaciones móviles de Azure | Microsoft Azure"
	description="Referencia e información general conceptual de la característica de sincronización de datos sin conexión para Aplicaciones móviles de Azure"
	documentationCenter="windows"
	authors="wesmc7777"
	manager="dwrede"
	editor=""
	services="app-service\mobile"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="02/04/2016"
	ms.author="wesmc"/>

# Sincronización de datos sin conexión en Aplicaciones móviles de Azure

## ¿Qué es la sincronización de datos sin conexión?

La sincronización de datos sin conexión es una característica de SDK de cliente y de servidor de Aplicaciones móviles de Azure que facilita a los desarrolladores la creación de aplicaciones que funcionen sin una conexión de red.

Cuando la aplicación está en modo sin conexión, los usuarios todavía pueden crear y modificar datos, que se guardan en un almacén local. Cuando la aplicación se vuelve a conectar, esta puede sincronizar los cambios locales con el back-end de la aplicación de Azure. La característica también admite la detección de conflictos cuando se cambia el mismo registro en el cliente y el back-end. Luego, los conflictos se pueden manejar en el servidor o el cliente.

La sincronización sin conexión tiene una serie de ventajas:

* Mejore la capacidad de respuesta de las aplicaciones almacenando en caché datos de servidor de forma local en el dispositivo.
* Creación de aplicaciones sólidas que sigan siendo útiles cuando hay problemas de red
* Permitir a los usuarios finales crear y modificar datos incluso cuando no hay acceso de red, proporcionando escenarios con muy poca o ninguna conectividad.
* Sincronizar datos entre diferentes dispositivos y detectar conflictos cuando dos dispositivos modifican el mismo registro.
* Limitar el uso de las redes medidas o de alta latencia

Los siguientes tutoriales muestran cómo incorporar la sincronización sin conexión a los clientes móviles con Aplicaciones móviles de Azure:

* [Android: habilitar la sincronización sin conexión]
* [iOS: habilitar la sincronización sin conexión]
* [Xamarin iOS: habilitar la sincronización sin conexión]
* [Xamarin Android: habilitar la sincronización sin conexión]
* [Windows 8.1: habilitar la sincronización sin conexión]

## ¿Qué es una tabla de sincronización?

Para tener acceso al extremo de "/tables", los SDK de cliente móvil de Azure proporcionan interfaces como `IMobileServiceTable` (SDK de cliente de .NET) o `MSTable` (cliente de iOS). Estas API se conectan directamente con el back-end de la aplicación móvil de Azure y generan errores si el dispositivo cliente no tiene una conexión de red.

Para admitir el uso sin conexión, en su lugar, la aplicación debe usar las API de la *tabla de sincronización*, como `IMobileServiceSyncTable` (SDK de cliente de .NET) o `MSSyncTable` (cliente de iOS). Las mismas operaciones CRUD (creación, lectura, actualización, eliminación) funcionan con las API de la tabla de sincronización, salvo que ahora leerán desde un *almacén local* o escribirán en él. Antes de poder realizar cualquier operación de la tabla de sincronización, se debe inicializar el almacén local.

## ¿Qué es un almacén local?

Un almacén local es la capa de persistencia de datos del dispositivo cliente. Los SDK de cliente de Aplicaciones móviles de Azure proporcionan una implementación de almacén local predeterminada. En Windows, Xamarin y Android, se basa en SQLite; en iOS, se basa en Core Data.

Para usar la implementación basada en SQLite en Windows Phone o Windows Store 8.1, debe instalar una extensión de SQLite. Para obtener información, consulte [Windows 8.1: habilitar la sincronización sin conexión]. Android y iOS se distribuyen con una versión de SQLite en el sistema operativo del dispositivo, por lo que no es necesario hacer referencia a su propia versión de SQLite.

Los desarrolladores también pueden implementar su propio almacén local. Por ejemplo, si desea almacenar los datos en un formato cifrado en el cliente móvil, puede definir un almacén local que use SQLCipher para el cifrado.

## ¿Qué es un contexto de sincronización?

Un *contexto de sincronización* está asociado con un objeto de cliente móvil (como `IMobileServiceClient` o `MSClient`) y realiza el seguimiento de los cambios realizados con las tablas de sincronización. El contexto de sincronización mantiene una *cola de operación* que tiene una lista ordenada de operaciones CUD (creación, actualización, eliminación) que posteriormente se envía al servidor.

Un almacén local se asocia con el contexto de sincronización mediante un método de inicialización como `IMobileServicesSyncContext.InitializeAsync(localstore)` en el SDK de cliente de .NET.

<!-- TODO: link to client references -->


<!--
Client code will interact with the table using the `IMobileServiceSyncTable` interface to support offline buffering. This interface supports all the methods of `IMobileServiceTable` along with additional support for pulling data from a Mobile App backend table and merging it into a local store table. How the local table is synchronized with the backend database is mainly controlled by your logic in the client app.

The sync table uses the [System Properties](https://msdn.microsoft.com/library/azure/dn518225.aspx) on the table to implement change tracking for offline synchronization.



* The data objects on the client should have some system properties, most are not required.
	* Managed
		* Write out the attributes
	* iOS
		*table for the entity
* Note: because the iOS local store is based on Core Data, the developer must define the following tables:
	* System tables  -->


## Funcionamiento de la sincronización sin conexión

Al usar tablas de sincronización, el código de cliente determina el momento en que se sincronizan los cambios locales con un back-end de la aplicación móvil de Azure. No se envía nada al back-end hasta que hay una llamada a los cambios locales de *inserción*. De forma similar, el almacén local se rellena con datos nuevos solo cuando hay una llamada a los datos de *extracción*.

* **Inserción**: la inserción es una operación en el contexto de sincronización que envía todos los cambios CUD realizados desde la última inserción. Tenga en cuenta que no es posible enviar solo los cambios de una tabla individual porque las operaciones se podrían enviar desordenadas. La inserción ejecuta una serie de llamadas REST al back-end de la aplicación móvil de Azure, que a su vez, modificará la base de datos del servidor.

* **Extracción**: la extracción se realiza tabla por tabla y se puede personalizar con una consulta para recuperar solo un subconjunto de los datos del servidor. Luego, los SDK de cliente móvil de Azur insertan los datos que se obtienen en el almacén local.

* **Inserciones implícitas**: si se ejecuta una extracción en una tabla que tiene actualizaciones locales pendientes, la extracción ejecutará primero una inserción en el contexto de sincronización. Esto ayuda a minimizar los conflictos entre los cambios que ya están en cola y los datos nuevos del servidor.

* **Sincronización incremental**: el primer parámetro de la operación de extracción es un *nombre de consulta* que solo se usa en el cliente. Si usa un nombre de consulta no nulo, Azure Mobile SDK llevará a cabo una *sincronización incremental*. Cada vez que una operación de extracción devuelve un conjunto de resultados, la última marca de tiempo `__updatedAt` de dicho conjunto se almacena en las tablas del sistema local del SDK. Las operaciones de extracción posteriores solo recuperarán registros después de esa marca de tiempo.

  Para usar la sincronización incremental, el servidor debe devolver valores `__updatedAt` significativos y también admitir la ordenación mediante este campo. Sin embargo, puesto que el SDK agrega su propio orden en el campo updatedAt, no puede usar una consulta de extracción que tenga su propia cláusula `$orderBy$`.

  El nombre de consulta puede ser cualquier cadena que elija, pero debe ser único para cada consulta lógica de la aplicación. De lo contrario, diferentes operaciones de extracción podrían sobrescribir la misma marca de tiempo de sincronización incremental y las consultas podrían devolver resultados incorrectos.

  Si la consulta tiene un parámetro, una forma de crear un nombre de consulta único es incorporar el valor del parámetro. Por ejemplo, si está filtrando por userid, el nombre de consulta podría ser el siguiente:

		await todoTable.PullAsync("todoItems" + userid, syncTable.Where(u => u.UserId = userid));

  Si desea cancelar la sincronización incremental, pase `null` como identificador de consulta. En este caso, se recuperarán todos los registros en cada llamada a `PullAsync`, que es potencialmente ineficaz.



<!--   mymobileservice-code.azurewebsites.net/tables/TodoItem?$filter=(__updatedAt ge datetimeoffset'1970-01-01T00:00:00.0000000%2B00:00')&$orderby=__updatedAt&$skip=0&$top=50&__includeDeleted=true&__systemproperties=__updatedAt%2C__deleted
 -->

* **Purgado**: el contenido del almacén local se puede borrar con `IMobileServiceSyncTable.PurgeAsync`. Esto puede ser necesario si tiene datos obsoletos en la base de datos cliente o si desea descartar todos los cambios pendientes.

  Una purga borra una tabla del almacén local. Si hay operaciones con sincronización pendiente con la base de datos del servidor, la purga genera una excepción, a menos que esté establecido el parámetro *force purge*.

  Como un ejemplo de datos obsoletos en el cliente, supongamos que en el ejemplo "lista de tareas pendientes", Dispositivo1 extrae solo los elementos que no se han completado. Luego, otro dispositivo marca una tarea pendiente "Comprar leche" como completa en el servidor. Sin embargo, Dispositivo1 seguirá teniendo la tarea pendiente "Comprar leche" en el almacén local porque solo está extrayendo los elementos que no están marcados como completos. Una purga borra este elemento obsoleto.

## Pasos siguientes

* [iOS: habilitar la sincronización sin conexión]
* [Xamarin iOS: habilitar la sincronización sin conexión]
* [Xamarin Android: habilitar la sincronización sin conexión]
* [Windows 8.1: habilitar la sincronización sin conexión]

<!-- Links -->

[Android: habilitar la sincronización sin conexión]: ../app-service-mobile-android-get-started-offline-data.md
[iOS: habilitar la sincronización sin conexión]: ../app-service-mobile-ios-get-started-offline-data.md
[Xamarin iOS: habilitar la sincronización sin conexión]: ../app-service-mobile-xamarin-ios-get-started-offline-data.md
[Xamarin Android: habilitar la sincronización sin conexión]: ../app-service-mobile-xamarin-ios-get-started-offline-data.md
[Windows 8.1: habilitar la sincronización sin conexión]: ../app-service-mobile-windows-store-dotnet-get-started-offline-data.md

<!---HONumber=AcomDC_0211_2016-->