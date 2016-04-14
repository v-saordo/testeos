
1. En el **Explorador de proyectos** en Android Studio, abra el archivo ToDoActivity.java y agregue las siguientes instrucciones de importación.

		import java.util.concurrent.ExecutionException;
		import java.util.concurrent.atomic.AtomicBoolean;

		import android.content.Context;
		import android.content.SharedPreferences;
		import android.content.SharedPreferences.Editor;

		import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceAuthenticationProvider;
		import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceUser;

2. Agregue el método siguiente a la clase **TodoActivity**:
	
		private void authenticate() {
		    // Login using the Google provider.
		    
			ListenableFuture<MobileServiceUser> mLogin = mClient.login(MobileServiceAuthenticationProvider.Google);
	
	    	Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
	    		@Override
	    		public void onFailure(Throwable exc) {
	    			createAndShowDialog((Exception) exc, "Error");
	    		}   		
	    		@Override
	    		public void onSuccess(MobileServiceUser user) {
	    			createAndShowDialog(String.format(
	                        "You are now logged in - %1$2s",
	                        user.getUserId()), "Success");
	    			createTable();	
	    		}
	    	});   	
		}


	De este modo se crea un método para administrar el proceso de autenticación. El usuario se autentica mediante el inicio de sesión en Google. Aparecerá un diálogo que muestra el identificador del usuario autenticado. No puede continuar sin una autenticación positiva.

    > [AZURE.NOTE]Si usa un proveedor de identidades que no sea Google, cambie el valor pasado a **login** a uno de los siguientes: _MicrosoftAccount_, _Facebook_, _Twitter_ o _windowsazureactivedirectory_.

3. En el método **onCreate**, agregue la siguiente línea de código después del código que crea una instancia del objeto `MobileServiceClient`.

		authenticate();

	De este modo se llama al proceso de autenticación.

4. Mueva el código restante situado detrás de `authenticate();` del método **onCreate** a un nuevo método **createTable** cuyo aspecto es el siguiente:

		private void createTable() {
	
			// Get the table instance to use.
			mToDoTable = mClient.getTable(ToDoItem.class);
	
			mTextNewToDo = (EditText) findViewById(R.id.textNewToDo);
	
			// Create an adapter to bind the items with the view.
			mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
			ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
			listViewToDo.setAdapter(mAdapter);
	
			// Load the items from Azure.
			refreshItemsFromTable();
		}

9. En el menú **Ejecutar**, haga clic en **Ejecutar aplicación** para iniciar la aplicación e inicie sesión con el proveedor de identidades que haya elegido.

   	Si ha iniciado sesión correctamente, la aplicación debe ejecutarse sin errores, y debería poder consultar el servicio back-end y realizar actualizaciones en los datos.

<!---HONumber=AcomDC_1210_2015-->