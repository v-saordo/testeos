
En el ejemplo anterior se mostró un inicio de sesión estándar, que requiere que el cliente se ponga en contacto con el proveedor de identidades y con el servicio móvil cada vez que se inicia la aplicación. Este método no solo es ineficaz, sino que también puede enfrentarse a problemas relacionados con el uso si varios clientes intentan iniciar la aplicación al mismo tiempo. Un método mejor es almacenar en caché el token de autorización devuelto por los servicios móviles e intentar usarlo primero antes de utilizar un inicio de sesión basado en proveedores.

>[AZURE.NOTE]Puede almacenar en caché el token emitido por los servicios móviles con independencia de si es una autenticación administrada por el cliente o por el servicio. Este tutorial utiliza la autenticación administrada por el servicio.


1. En Eclipse, abra el archivo ToDoActivity.java y agregue las siguientes instrucciones de importación:

        import android.content.Context;
        import android.content.SharedPreferences;
        import android.content.SharedPreferences.Editor;

2. Agregue los siguientes métodos a la clase `ToDoActivity`.

    	public static final String SHAREDPREFFILE = "temp";	
	    public static final String USERIDPREF = "uid";	
    	public static final String TOKENPREF = "tkn";	


3. En el archivo ToDoActivity.java, agregue la siguiente definición para el método `cacheUserToken`.
 
    	private void cacheUserToken(MobileServiceUser user)
	    {
    		SharedPreferences prefs = getSharedPreferences(SHAREDPREFFILE, Context.MODE_PRIVATE);
    	    Editor editor = prefs.edit();
	        editor.putString(USERIDPREF, user.getUserId());
    	    editor.putString(TOKENPREF, user.getAuthenticationToken());
	        editor.commit();
    	}	
  
    Este método almacena el identificador de usuario y el token en un archivo de preferencias que está marcado como privado. Esto debería proteger el acceso a la memoria caché para que otras aplicaciones del dispositivo no tengan acceso al token porque la preferencia está aislada para la aplicación. Sin embargo, si alguien obtiene acceso al dispositivo, es posible que pueda tener acceso a la caché del token mediante otros medios.

    >[AZURE.NOTE]Puede proteger adicionalmente el token con cifrado si el acceso del token a los datos se considera sumamente sensible y alguien puede obtener acceso al dispositivo. Sin embargo, una solución completamente segura está fuera del alcance de este tutorial y depende de los requisitos de seguridad.


4. En el archivo ToDoActivity.java, agregue la siguiente definición para el método `loadUserTokenCache`.

    	private boolean loadUserTokenCache(MobileServiceClient client)
	    {
	        SharedPreferences prefs = getSharedPreferences(SHAREDPREFFILE, Context.MODE_PRIVATE);
    	    String userId = prefs.getString(USERIDPREF, "undefined"); 
	        if (userId == "undefined")
	            return false;
    	    String token = prefs.getString(TOKENPREF, "undefined"); 
    	    if (token == "undefined")
    	        return false;
        	    
    	    MobileServiceUser user = new MobileServiceUser(userId);
    	    user.setAuthenticationToken(token);
    	    client.setCurrentUser(user);
        	    
    	    return true;
	    }



5. En el archivo *ToDoActivity.java*, reemplace el método `authenticate` por el método siguiente que usa una caché del token. Cambie el proveedor de inicio de sesión si desea usar otra cuenta que no sea la de Microsoft.

		private void authenticate() {
			// We first try to load a token cache if one exists.
		    if (loadUserTokenCache(mClient))
		    {
		        createTable();
		    }
		    // If we failed to load a token cache, login and create a token cache
		    else
		    {
			    // Login using the Google provider.    
				ListenableFuture<MobileServiceUser> mLogin = mClient.login(MobileServiceAuthenticationProvider.Google);
		
		    	Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
		    		@Override
		    		public void onFailure(Throwable exc) {
		    			createAndShowDialog("You must log in. Login Required", "Error");
		    		}   		
		    		@Override
		    		public void onSuccess(MobileServiceUser user) {
		    			createAndShowDialog(String.format(
		                        "You are now logged in - %1$2s",
		                        user.getUserId()), "Success");
		    			cacheUserToken(mClient.getCurrentUser());
		    			createTable();	
		    		}
		    	});
		    }
		}

6. Cree la aplicación y pruebe la autenticación mediante una cuenta válida. Ejecútela al menos dos veces. Durante la primera ejecución, debe recibir un mensaje para iniciar sesión y crear la memoria caché del token. Después, cada ejecución intentará cargar la caché del token para su autenticación y no debería requerirse el inicio de sesión.

<!---HONumber=August15_HO6-->