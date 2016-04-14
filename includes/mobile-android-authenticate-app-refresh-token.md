Nuestra caché del token debería funcionar en un caso sencillo pero, ¿qué ocurre cuando el token expira o se revoca? El token puede expirar si la aplicación no se está ejecutando. Lo que podría significar que la caché del token no es válida. El token también podría expirar mientras la aplicación se está ejecutando. El resultado es un código de estado HTTP 401 "No autorizado".

Tenemos que ser capaces de detectar un token caducado y actualizarlo. Para ello, usamos un filtro [ServiceFilter](http://dl.windowsazure.com/androiddocs/com/microsoft/windowsazure/mobileservices/ServiceFilter.html) de la [Biblioteca de cliente Android](http://dl.windowsazure.com/androiddocs/).

En esta sección definirá un ServiceFilter que detectará una respuesta 401 del código de estado HTTP y desencadenará una actualización del token y de la caché del token. Además, este ServiceFilter bloqueará otras solicitudes salientes durante la autenticación para que dichas solicitudes puedan usar el token actualizado.

1. Abra el archivo ToDoActivity.java y agregue las siguientes instrucciones de importación:
 
        import java.util.concurrent.atomic.AtomicBoolean;
		import java.util.concurrent.ExecutionException;

		import com.microsoft.windowsazure.mobileservices.MobileServiceException;
 
2. Agregue los siguientes métodos a la clase `ToDoActivity`.

    	public boolean bAuthenticating = false;
	    public final Object mAuthenticationLock = new Object();

    Se utilizarán para ayudar a sincronizar la autenticación del usuario. Solo queremos autenticarlo una vez. Todas las llamadas realizadas durante una autenticación deben esperar y utilizar el nuevo token desde la autenticación en curso.

3. En el archivo ToDoActivity.java, agregue el método siguiente a la clase ToDoActivity que se utilizará para bloquear llamadas salientes en otros subprocesos mientras la autenticación está en curso.

	    /**
    	 * Detects if authentication is in progress and waits for it to complete. 
         * Returns true if authentication was detected as in progress. False otherwise.
    	 */
    	public boolean detectAndWaitForAuthentication()
    	{
    		boolean detected = false;
    		synchronized(mAuthenticationLock)
    		{
    			do
    			{
    				if (bAuthenticating == true)
    					detected = true;
    				try
    				{
    					mAuthenticationLock.wait(1000);
    				}
    				catch(InterruptedException e)
    				{}
    			}
    			while(bAuthenticating == true);
    		}
    		if (bAuthenticating == true)
    			return true;
    		
    		return detected;
    	}
    	

4. En el archivo ToDoActivity.java, agregue el método siguiente a la clase ToDoActivity. Este método desencadenará la espera y después actualizará el token en las solicitudes salientes cuando la autenticación se ha completado.

    	
    	/**
    	 * Waits for authentication to complete then adds or updates the token 
    	 * in the X-ZUMO-AUTH request header.
    	 * 
    	 * @param request
    	 *            The request that receives the updated token.
    	 */
    	private void waitAndUpdateRequestToken(ServiceFilterRequest request)
    	{
    		MobileServiceUser user = null;
    		if (detectAndWaitForAuthentication())
    		{
    			user = mClient.getCurrentUser();
    			if (user != null)
    			{
    				request.removeHeader("X-ZUMO-AUTH");
    				request.addHeader("X-ZUMO-AUTH", user.getAuthenticationToken());
    			}
    		}
    	}


5. En el archivo ToDoActivity.java, actualice el método `authenticate` de la clase ToDoActivity para que acepte un parámetro booleano que permita forzar la actualización del token y de la caché del token. También necesitamos notificar a los subprocesos bloqueados que la autenticación se ha completado para que puedan recoger el nuevo token.

	    /**
    	 * Authenticates with the desired login provider. Also caches the token. 
    	 * 
    	 * If a local token cache is detected, the token cache is used instead of an actual 
	     * login unless bRefresh is set to true forcing a refresh.
    	 * 
	     * @param bRefreshCache
    	 *            Indicates whether to force a token refresh. 
	     */
    	private void authenticate(boolean bRefreshCache) {
	        
		    bAuthenticating = true;
		    
		    if (bRefreshCache || !loadUserTokenCache(mClient))
	        {
	            // New login using the provider and update the token cache.
	            mClient.login(MobileServiceAuthenticationProvider.MicrosoftAccount,
	                    new UserAuthenticationCallback() {
	                        @Override
	                        public void onCompleted(MobileServiceUser user,
	                                Exception exception, ServiceFilterResponse response) {
    
	                    		synchronized(mAuthenticationLock)
	                    		{
		                        	if (exception == null) {
		                                cacheUserToken(mClient.getCurrentUser());
                                        createTable();
		                            } else {
		                                createAndShowDialog(exception.getMessage(), "Login Error");
		                            }
		            				bAuthenticating = false;
		            				mAuthenticationLock.notifyAll();
	                    		}
	                        }
	                    });
	        }
		    else
		    {
		    	// Other threads may be blocked waiting to be notified when 
		    	// authentication is complete.
		    	synchronized(mAuthenticationLock)
		    	{
		    		bAuthenticating = false;
		    		mAuthenticationLock.notifyAll();
		    	}
                createTable();
		    }
	    }   



6. En el archivo ToDoActivity.java, agregue este código para una nueva clase `RefreshTokenCacheFilter` dentro de la clase ToDoActivity:

		/**
		* The RefreshTokenCacheFilter class filters responses for HTTP status code 401. 
		 * When 401 is encountered, the filter calls the authenticate method on the 
		 * UI thread. Out going requests and retries are blocked during authentication. 
		 * Once authentication is complete, the token cache is updated and 
		 * any blocked request will receive the X-ZUMO-AUTH header added or updated to 
		 * that request.   
		 */
		private class RefreshTokenCacheFilter implements ServiceFilter {
		 
	        AtomicBoolean mAtomicAuthenticatingFlag = new AtomicBoolean();                     
	        
	        @Override
	        public ListenableFuture<ServiceFilterResponse> handleRequest(
	        		final ServiceFilterRequest request, 
	        		final NextServiceFilterCallback nextServiceFilterCallback
	        		)
	        {
	            // In this example, if authentication is already in progress we block the request
	            // until authentication is complete to avoid unnecessary authentications as 
	            // a result of HTTP status code 401. 
	            // If authentication was detected, add the token to the request.
	            waitAndUpdateRequestToken(request);
	 
	            // Send the request down the filter chain
	            // retrying up to 5 times on 401 response codes.
	            ListenableFuture<ServiceFilterResponse> future = null;
	            ServiceFilterResponse response = null;
	            int responseCode = 401;
	            for (int i = 0; (i < 5 ) && (responseCode == 401); i++)
	            {
	                future = nextServiceFilterCallback.onNext(request);
	                try {
	                    response = future.get();
	                    responseCode = response.getStatus().getStatusCode();
	                } catch (InterruptedException e) {
	                   e.printStackTrace();
	                } catch (ExecutionException e) {
	                    if (e.getCause().getClass() == MobileServiceException.class)
	                    {
	                        MobileServiceException mEx = (MobileServiceException) e.getCause();
	                        responseCode = mEx.getResponse().getStatus().getStatusCode();
	                        if (responseCode == 401)
	                        {
	                            // Two simultaneous requests from independent threads could get HTTP status 401. 
	                            // Protecting against that right here so multiple authentication requests are
	                            // not setup to run on the UI thread.
	                            // We only want to authenticate once. Requests should just wait and retry 
	                            // with the new token.
	                            if (mAtomicAuthenticatingFlag.compareAndSet(false, true))                                                                                                      
	                            {
	                                // Authenticate on UI thread
	                                runOnUiThread(new Runnable() {
	                                    @Override
	                                    public void run() {
	                                        // Force a token refresh during authentication.
	                                        authenticate(true);
	                                    }
	                                });
	                            }
	
				                // Wait for authentication to complete then update the token in the request. 
				                waitAndUpdateRequestToken(request);
				                mAtomicAuthenticatingFlag.set(false);                                                  
	                        }
	                    }
	                }
	            }
	            return future;
	        }
		}


    Este filtro de servicio comprobará cada respuesta en busca del código de estado HTTP 401 "No autorizado". Si se encuentra un 401, se configurará una nueva solicitud de inicio de sesión para obtener un nuevo token en el subproceso de la interfaz de usuario. Otras llamadas se bloquearán hasta que se complete el inicio de sesión o hasta 5 intentos fallidos. Cuando el nuevo token se obtenga, la solicitud que ha desencadenado el 401 se reintentará con el nuevo token y todas las llamadas bloqueadas se reintentarán con el nuevo token.

7. En el archivo ToDoActivity.java, agregue este código para una nueva clase `ProgressFilter` dentro de la clase ToDoActivity:
		
		/**
		* The ProgressFilter class renders a progress bar on the screen during the time the App is waiting for the response of a previous request.
		* the filter shows the progress bar on the beginning of the request, and hides it when the response arrived.
		*/
		private class ProgressFilter implements ServiceFilter {
			@Override
			public ListenableFuture<ServiceFilterResponse> handleRequest(ServiceFilterRequest request, NextServiceFilterCallback nextServiceFilterCallback) {

				final SettableFuture<ServiceFilterResponse> resultFuture = SettableFuture.create();

				runOnUiThread(new Runnable() {

					@Override
					public void run() {
						if (mProgressBar != null) mProgressBar.setVisibility(ProgressBar.VISIBLE);
					}
				});

				ListenableFuture<ServiceFilterResponse> future = nextServiceFilterCallback.onNext(request);

				Futures.addCallback(future, new FutureCallback<ServiceFilterResponse>() {
					@Override
					public void onFailure(Throwable e) {
						resultFuture.setException(e);
					}

					@Override
					public void onSuccess(ServiceFilterResponse response) {
						runOnUiThread(new Runnable() {

							@Override
							public void run() {
								if (mProgressBar != null) mProgressBar.setVisibility(ProgressBar.GONE);
							}
						});

						resultFuture.set(response);
					}
				});

				return resultFuture;
			}
		}
		
	Este filtro mostrará la barra de progreso en el principio de la solicitud y la ocultará cuando llegue la respuesta.

8. En el archivo ToDoActivity.java, actualice el método `onCreate` como sigue:

		@Override
	    public void onCreate(Bundle savedInstanceState) {
		    super.onCreate(savedInstanceState);
		    
		    setContentView(R.layout.activity_to_do);
		    mProgressBar = (ProgressBar) findViewById(R.id.loadingProgressBar);
        
		    // Initialize the progress bar
		    mProgressBar.setVisibility(ProgressBar.GONE);
        
		    try {
		    	// Create the Mobile Service Client instance, using the provided
		    	// Mobile Service URL and key
		    	mClient = new MobileServiceClient(
		    			"https://<YOUR MOBILE SERVICE>.azure-mobile.net/",
		    			"<YOUR MOBILE SERVICE KEY>", this)
                           .withFilter(new ProgressFilter())
                           .withFilter(new RefreshTokenCacheFilter());
            
		    	// Authenticate passing false to load the current token cache if available.
		    	authenticate(false);
		        	
		    } catch (MalformedURLException e) {
			    createAndShowDialog(new Exception("Error creating the Mobile Service. " +
			        "Verify the URL"), "Error");
		    }
	    }


       En este código, se usa `RefreshTokenCacheFilter` junto con `ProgressFilter`. También, durante `onCreate` queremos cargar la caché del token. Por tanto, `false` se pasa al método `authenticate`.

<!---HONumber=AcomDC_1210_2015-->