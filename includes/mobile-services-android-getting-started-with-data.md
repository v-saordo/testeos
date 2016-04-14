Ahora que el servicio móvil está listo, puede actualizar la aplicación a fin de almacenar elementos en Servicios móviles en lugar de en la colección local.

1. Compruebe que dispone de las siguientes líneas en la etiqueta **dependencies** del archivo *build.gradle (Module app)* y, de no ser así, agréguelas. Esto agrega referencias al SDK de cliente de Android de Servicios móviles.

		compile 'com.android.support:support-v4:21.0.3'
    	compile 'com.google.code.gson:gson:2.2.2'
	    compile 'com.google.guava:guava:18.0'
	    compile 'com.microsoft.azure:azure-mobile-services-android-sdk:2.0.2+'


2. Ahora vuelva a generar el proyecto haciendo clic en **Sincronizar proyecto con archivos de Gradle**.

3. Abra el archivo AndroidManifest.xml y agregue la línea siguiente, que permite a la aplicación obtener acceso a los Servicios móviles de Azure.

		<uses-permission android:name="android.permission.INTERNET" />


4. En el Explorador de proyectos, abra el archivo TodoActivity.java, que se encuentra en la carpeta **GetStartedWithData => app => src => java**, y elimine los comentarios de las siguientes líneas de código:



		import java.net.MalformedURLException;
		import android.os.AsyncTask;
		import com.google.common.util.concurrent.FutureCallback;
		import com.google.common.util.concurrent.Futures;
		import com.google.common.util.concurrent.ListenableFuture;
		import com.microsoft.windowsazure.mobileservices.MobileServiceClient;
		import com.microsoft.windowsazure.mobileservices.MobileServiceList;
		import com.microsoft.windowsazure.mobileservices.http.NextServiceFilterCallback;
		import com.microsoft.windowsazure.mobileservices.http.ServiceFilter;
		import com.microsoft.windowsazure.mobileservices.http.ServiceFilterRequest;
		import com.microsoft.windowsazure.mobileservices.http.ServiceFilterResponse;
		import com.microsoft.windowsazure.mobileservices.table.MobileServiceTable;

 
5. Comente las líneas siguientes:

		import java.util.ArrayList;
		import java.util.List;

6. Eliminaremos la lista de la memoria que utiliza actualmente la aplicación, para que podamos reemplazarla por un servicio móvil. En la clase **ToDoActivity**, comente la línea de código siguiente que define la lista **toDoItemList** existente.

		public List<ToDoItem> toDoItemList = new ArrayList<ToDoItem>();

7. Guarde el archivo y el proyecto indicará los errores de la compilación. Busque las tres ubicaciones restantes en las que se usa la variable `toDoItemList` y convierta en comentarios las secciones indicadas. De este modo se elimina por completo la lista de la memoria.

8. Ahora agregamos nuestros servicios móviles. Quite la marca de comentario de las líneas de código siguientes:

		private MobileServiceClient mClient;
		private private MobileServiceTable<ToDoItem> mToDoTable;

9. Busque la clase *ProgressFilter* en la parte inferior del archivo y quite la marca de comentario. Esta clase muestra un indicador 'loading' mientras *MobileServiceClient* está ejecutando operaciones de red.


10. En el Portal de Azure clásico, haga clic en **Servicios móviles** y haga clic en el servicio móvil que acaba de crear.

11. Haga clic en la pestaña **Panel** y anote la **dirección URL del sitio**; a continuación, haga clic en **Administrar claves** y anote la **clave de la aplicación**.

   	![](./media/download-android-sample-code/mobile-dashboard-tab.png)

  	Necesitará estos valores para obtener acceso al servicio móvil desde su código de aplicación.

12. En el método **onCreate**, quite la marca de comentario de las líneas de código siguientes que definen la variable **MobileServiceClient**:

		try {
		// Create the Mobile Service Client instance, using the provided
		// Mobile Service URL and key
			mClient = new MobileServiceClient(
					"MobileServiceUrl",
					"AppKey", 
					this).withFilter(new ProgressFilter());

			// Get the Mobile Service Table instance to use
			mToDoTable = mClient.getTable(ToDoItem.class);
		} catch (MalformedURLException e) {
			createAndShowDialog(new Exception("There was an error creating the Mobile Service. Verify the URL"), "Error");
		}

  	De esta forma, se crea una nueva instancia de *MobileServiceClient* que se usa para obtener acceso al servicio móvil. Además, se crea la instancia de *MobileServiceTable* que se usa para el almacenamiento de datos de proxy en el servicio móvil.

13. En el código de arriba, reemplace `MobileServiceUrl` y `AppKey` por la URL y la clave de la aplicación de su servicio móvil, por ese orden.



14. Quite la marca de comentario de estas líneas del método **checkItem**:

	    new AsyncTask<Void, Void, Void>() {
	        @Override
	        protected Void doInBackground(Void... params) {
	            try {
	                mToDoTable.update(item).get();
	                runOnUiThread(new Runnable() {
	                    public void run() {
	                        if (item.isComplete()) {
	                            mAdapter.remove(item);
	                        }
	                        refreshItemsFromTable();
	                    }
	                });
	            } catch (Exception exception) {
	                createAndShowDialog(exception, "Error");
	            }
	            return null;
	        }
	    }.execute();

   	De este modo se envía una actualización del elemento al servicio móvil y se eliminan los elementos marcados del adaptador.
    
15. Quite la marca de comentario de estas líneas del método **addItem**:
	
		// Insert the new item
		new AsyncTask<Void, Void, Void>() {
	        @Override
	        protected Void doInBackground(Void... params) {
	            try {
	                mToDoTable.insert(item).get();
	                if (!item.isComplete()) {
	                    runOnUiThread(new Runnable() {
	                        public void run() {
	                            mAdapter.add(item);
	                        }
	                    });
	                }
	            } catch (Exception exception) {
	                createAndShowDialog(exception, "Error");
	            }
	            return null;
	        }
	    }.execute();
		

  	Este código crea un elemento y lo inserta en la tabla del servicio móvil remoto.

16. Quite la marca de comentario de estas líneas del método **refreshItemsFromTable**:

		// Get the items that weren't marked as completed and add them in the adapter
	    new AsyncTask<Void, Void, Void>() {
	        @Override
	        protected Void doInBackground(Void... params) {
	            try {
	                final MobileServiceList<ToDoItem> result = mToDoTable.where().field("complete").eq(false).execute().get();
	                runOnUiThread(new Runnable() {
	                    @Override
	                    public void run() {
	                        mAdapter.clear();

	                        for (ToDoItem item : result) {
	                            mAdapter.add(item);
	                        }
	                    }
	                });
	            } catch (Exception exception) {
	                createAndShowDialog(exception, "Error");
	            }
	            return null;
	        }
	    }.execute();

	De este modo se consulta el servicio móvil y se devuelven todos los elementos que no se hayan marcado como completos. Los elementos se agregan al adaptador para enlace.
		

<!-- URLs. -->
[Mobile Services Android SDK]: http://aka.ms/Iajk6q

<!---HONumber=AcomDC_1203_2015-->