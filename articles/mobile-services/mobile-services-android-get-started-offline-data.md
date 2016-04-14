<properties
	pageTitle="Agregar sincronización de datos sin conexión a la aplicación de Servicios móviles de Android |Microsoft Azure"
	description="Obtenga información sobre cómo usar Servicios móviles de Azure para almacenar datos en caché y sincronizarlos sin conexión en su aplicación Android."
	documentationCenter="android"
	authors="RickSaling"
	manager="dwrede"
	editor=""
	services="mobile-services"/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="02/07/2016"
	ms.author="ricksal"/>

# Incorporación de sincronización de datos sin conexión a la aplicación de Servicios móviles de Android

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;

[AZURE.INCLUDE [mobile-services-selector-offline](../../includes/mobile-services-selector-offline.md)]

## Resumen

Las aplicaciones móviles pueden perder la conectividad de red cuando se mueven a un área sin servicio, o debido a problemas de red. Por ejemplo, puede que una aplicación de la industria de la construcción que se usa en una construcción remota deba especificar datos de programación que se sincronizarán posteriormente con Azure. Con la sincronización sin conexión de Servicios móviles de Azure, puede seguir trabajando cuando la conectividad de red se pierde, lo que es esencial para muchas aplicaciones móviles. Con la sincronización sin conexión, se trabaja con una copia local de la tabla de Azure SQL Server, y periódicamente se vuelven a sincronizar las dos.

En este tutorial, actualizará la aplicación siguiendo el [tutorial del Inicio rápido de Servicios móviles] para habilitar la sincronización sin conexión y, después, probará la aplicación agregando datos sin conexión, sincronizará esos elementos con la base de datos en línea y comprobará los cambios en el Portal de Azure clásico.

Tanto si está conectado como no, pueden surgir conflictos en cualquier momento en que se realizan varios cambios a los datos. Un futuro tutorial explorará cómo administrar los conflictos de sincronización, donde podrá elegir qué versión de los cambios acepta. En este tutorial, daremos por hecho que no hay conflictos de sincronización y los cambios que realice a los datos existentes se aplicarán directamente al servidor de SQL Azure.


## Qué necesita para empezar

[AZURE.INCLUDE [mobile-services-android-prerequisites](../../includes/mobile-services-android-prerequisites.md)]

## Actualización de la aplicación para que admita la sincronización sin conexión

Con la sincronización sin conexión, se lee y se escribe desde una *tabla de sincronización* (usando la interfaz *IMobileServiceSyncTable*), que forma parte de una base de datos **SQLite** en el dispositivo.

Para insertar y extraer los cambios entre el dispositivo y Servicios móviles de Azure, se usa un *contexto de sincronización* (*MobileServiceClient.SyncContext*), que se inicializa con la base de datos local usada para almacenar los datos localmente.

1. Agregue permisos para comprobar la conectividad de red agregando este código al archivo *AndroidManifest.xml*:

	    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />


2. Agregue las siguientes instrucciones **import** *ToDoActivity.java*:

		import java.util.Map;

		import android.widget.Toast;

		import com.microsoft.windowsazure.mobileservices.table.query.Query;
		import com.microsoft.windowsazure.mobileservices.table.sync.MobileServiceSyncContext;
		import com.microsoft.windowsazure.mobileservices.table.sync.MobileServiceSyncTable;
		import com.microsoft.windowsazure.mobileservices.table.sync.localstore.ColumnDataType;
		import com.microsoft.windowsazure.mobileservices.table.sync.localstore.SQLiteLocalStore;

3. Cerca de la parte superior de la clase `ToDoActivity`, cambie la declaración de la variable `mToDoTable` de una clase `MobileServiceTable<ToDoItem>` a una clase `MobileServiceSyncTable<ToDoItem>`.

		 private MobileServiceSyncTable<ToDoItem> mToDoTable;

	Aquí es donde se define la tabla de sincronización.

4. Para controlar la inicialización del almacén local, en el método `onCreate`, agregue las siguientes líneas después de crear la instancia `MobileServiceClient`:

			// Saves the query which will be used for pulling data
			mPullQuery = mClient.getTable(ToDoItem.class).where().field("complete").eq(false);

			SQLiteLocalStore localStore = new SQLiteLocalStore(mClient.getContext(), "ToDoItem", null, 1);
			SimpleSyncHandler handler = new SimpleSyncHandler();
			MobileServiceSyncContext syncContext = mClient.getSyncContext();

			Map<String, ColumnDataType> tableDefinition = new HashMap<String, ColumnDataType>();
			tableDefinition.put("id", ColumnDataType.String);
			tableDefinition.put("text", ColumnDataType.String);
			tableDefinition.put("complete", ColumnDataType.Boolean);
			tableDefinition.put("__version", ColumnDataType.String);

			localStore.defineTable("ToDoItem", tableDefinition);
			syncContext.initialize(localStore, handler).get();

			// Get the Mobile Service Table instance to use
			mToDoTable = mClient.getSyncTable(ToDoItem.class);

5. Siguiendo el código anterior, que está dentro de un bloque `try`, agregue otro bloque `catch` después de `MalformedURLException`:

		} catch (Exception e) {
			Throwable t = e;
			while (t.getCause() != null) {
					t = t.getCause();
				}
			createAndShowDialog(new Exception("Unknown error: " + t.getMessage()), "Error");
		}

6. Agregue este método para comprobar la conectividad de red:

		private boolean isNetworkAvailable() {
			ConnectivityManager connectivityManager
					= (ConnectivityManager) getSystemService(Context.CONNECTIVITY_SERVICE);
			NetworkInfo activeNetworkInfo = connectivityManager.getActiveNetworkInfo();
			return activeNetworkInfo != null && activeNetworkInfo.isConnected();
		}


7. Agregue este método para sincronizar entre el almacén local *SQL Light* y Azure SQL Server:

		public void syncAsync(){
			if (isNetworkAvailable()) {
				new AsyncTask<Void, Void, Void>() {

					@Override
					protected Void doInBackground(Void... params) {
						try {
							mClient.getSyncContext().push().get();
							mToDoTable.pull(mPullQuery).get();

						} catch (Exception exception) {
							createAndShowDialog(exception, "Error");
						}
						return null;
					}
				}.execute();
			} else {
				Toast.makeText(this, "You are not online, re-sync later!" +
						"", Toast.LENGTH_LONG).show();
			}
		}


8. En el método `onCreate`, agregue este código después de la última línea, directamente antes de la llamada a `refreshItemsFromTable`:

			syncAsync();

	Esto hace que, al iniciarse, el dispositivo se sincronice con la tabla de Azure. En caso contrario, se mostrará el último contenido sin conexión del almacén local.



9. Actualice el código en el método `refreshItemsFromTable` para que use esta consulta (primera línea de código dentro del bloque `try`):

		final MobileServiceList<ToDoItem> result = mToDoTable.read(mPullQuery).get();

10. En el método `onOptionsItemSelected`, reemplace el contenido del bloque `if` por este código:

			syncAsync();
			refreshItemsFromTable();

	Este código se ejecuta cuando se presiona el botón **Actualizar** en la esquina superior derecha. Este es el modo principal de sincronizar el almacén local con Azure, además de la sincronización en el inicio. Esto fomenta la sincronización de cambios por lotes y resulta eficaz porque la extracción de Azure es una operación relativamente costosa. También puede diseñar la aplicación para que se sincronice en cada cambio, agregando una llamada a `syncAsync` a los métodos `addItem` y `checkItem`, si la aplicación así lo requiere.


## Prueba de la aplicación

En esta sección, probará el comportamiento con la red inalámbrica activada y, después, desactivará la red inalámbrica para crear un escenario sin conexión.

Al agregar elementos de datos, se guardan en el almacén local de SQL Light pero no se sincronizarán con el servicio móvil hasta que presione el botón **Actualizar**. Otras aplicaciones pueden tener requisitos diferentes con respecto a cuándo deben sincronizarse los datos, pero para los fines de demostración de este tutorial, haga que el usuario lo solicite expresamente.

Al presionar este botón, se inicia una nueva tarea en segundo plano que primero inserta todos los cambios realizados en el almacén local, mediante el contexto de sincronización, y después extrae todos los datos cambiados de Azure a la tabla local.


### Pruebas en línea

Permite probar los escenarios siguientes.

1. Agregue algunos elementos nuevos a su dispositivo.
2. Compruebe que los elementos no se muestran en el portal.
3. Después, presione **Actualizar** y compruebe que se muestran.
4. Cambie o agregue un elemento en el portal, después presione **Actualizar** y compruebe que los cambios aparecen en el dispositivo.

### Pruebas sin conexión

<!-- Now if you run the app and tap the refresh button, you should see all the items from the server. At that point you should be able to turn off the networking from the device by placing it in *Airplane Mode*, and continue making changes – the app will work just fine. When it’s time to sync the changes to the server, turn the network back on, and tap the **Refresh** button again.

One thing which is important to point out: if there are pending changes in the local store, a pull operation will first push those changes to the server (so that if there are changes in the same row, the push operation will fail and the application has an opportunity to handle the conflicts appropriately). That means that the push call in the code above isn’t necessarily required, but I think it’s always a good practice to be explicit about what the code is doing.
-->

1. Coloque el dispositivo o el simulador en *Modo avión*. Esto crea un escenario sin conexión.

2. Agregue algunos elementos *ToDo* o marque algunos elementos como completados. Salga del dispositivo o del simulador (o fuerce el cierre de la aplicación) y reinicie. Compruebe que los cambios se conservan en el dispositivo porque se guardan en el almacén local de SQL Light.

3. Consulte el contenido de la tabla *TodoItem* de Azure. Compruebe que los nuevos elementos _no_ se han sincronizado con el servidor:

   - Para el back-end de JavaScript, vaya al Portal de Azure clásico y haga clic en la pestaña Datos para ver el contenido de la tabla `TodoItem`.
   - Para el back-end de .NET, vea el contenido de tabla con una herramienta SQL, como *SQL Server Management Studio*, o con un cliente REST como *Fiddler* o *Postman*.

4. Active la red inalámbrica en el dispositivo o el simulador. Después, presione el botón **Actualizar**.

5. Vea los datos de TodoItem de nuevo en el portal de Azure clásico. Ahora deberían aparecer los elementos de TodoItems nuevos y modificados.


## Pasos siguientes

* [Uso de la eliminación temporal en Servicios móviles][Soft Delete]

## Recursos adicionales

* [Descripción de la nube: Sincronización sin conexión en Servicios móviles de Azure]

* [Azure Friday: Aplicaciones habilitadas sin conexión en Servicios móviles de Azure] \(Nota: las demostraciones son para Windows, pero las características tratadas son aplicables a todas las plataformas)


<!-- URLs. -->

[Get the sample app]: #get-app
[Review the Core Data model]: #review-core-data
[Review the Mobile Services sync code]: #review-sync
[Change the sync behavior of the app]: #setup-sync
[Test the app]: #test-app


[Mobile Services sample repository on GitHub]: https://github.com/Azure/mobile-services-samples


[Get started with Mobile Services]: mobile-services-android-get-started.md
[Handling Conflicts with Offline Support for Mobile Services]: mobile-services-android-handling-conflicts-offline-data.md
[Soft Delete]: mobile-services-using-soft-delete.md

[Descripción de la nube: Sincronización sin conexión en Servicios móviles de Azure]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Aplicaciones habilitadas sin conexión en Servicios móviles de Azure]: http://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/

[tutorial del Inicio rápido de Servicios móviles]: mobile-services-android-get-started.md

<!---HONumber=AcomDC_0211_2016-->