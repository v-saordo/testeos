1. En el proyecto de su aplicación, abra el archivo `AndroidManifest.xml`. En el código que aparece en los próximos dos pasos, reemplace _`**my_app_package**`_ por el nombre del paquete de la aplicación del proyecto, que es el valor del atributo `package` de la etiqueta `manifest`. 

2. Agregue los siguientes permisos nuevos después del elemento `uses-permission` existente:

        <permission android:name="**my_app_package**.permission.C2D_MESSAGE" 
            android:protectionLevel="signature" />
        <uses-permission android:name="**my_app_package**.permission.C2D_MESSAGE" /> 
        <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
        <uses-permission android:name="android.permission.GET_ACCOUNTS" />
        <uses-permission android:name="android.permission.WAKE_LOCK" />

3. Agregue el siguiente código después de la etiqueta de apertura `application`:

        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
            						 	android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="**my_app_package**" />
            </intent-filter>
        </receiver>


4. Descargue y descomprima el [SDK de Android para Servicio móviles], abra la carpeta **notificaciones**, copie el archivo **notifications-1.0.1.jar** en la carpeta *libs* del proyecto Eclipse y actualice la carpeta *libs*.

    > [AZURE.NOTE]Los números que aparecen al final del nombre del archivo pueden cambiar en versiones de SDK posteriores.

5.  Abra el archivo *ToDoItemActivity.java* y agregue la siguiente instrucción de importación:

		import com.microsoft.windowsazure.notifications.NotificationsManager;


6. Agregue la siguiente variable privada a la clase: reemplace _`<PROJECT_NUMBER>`_ por el número de proyecto que Google ha asignado a la aplicación en el procedimiento anterior:

		public static final String SENDER_ID = "<PROJECT_NUMBER>";

7. Cambie la definición de *MobileServiceClient* de **privado** a **público estático**, por lo que ahora presenta el siguiente aspecto:

		public static MobileServiceClient mClient;



9. A continuación, necesitamos agregar una nueva clase para controlar las notificaciones. En el Explorador de paquetes, haga clic con el botón secundario en el paquete (bajo el nodo `src`), haga clic en **Nuevo** y después en **Clase**.

10. En **Nombre**, escriba `MyHandler`, en **Superclase** escriba `com.microsoft.windowsazure.notifications.NotificationsHandler` y, a continuación, haga clic en **Finalizar**

	![](./media/mobile-services-android-get-started-push/mobile-services-android-create-class.png)

	Con esto se crea la clase MyHandler.

11. Agregue las siguientes instrucciones para la clase `MyHandler`:

		import android.app.NotificationManager;
		import android.app.PendingIntent;
		import android.content.Context;
		import android.content.Intent;
		import android.os.AsyncTask;
		import android.os.Bundle;
		import android.support.v4.app.NotificationCompat;

	
12. A continuación, agregue los miembros siguientes para la clase `MyHandler`:

		public static final int NOTIFICATION_ID = 1;
		private NotificationManager mNotificationManager;
		NotificationCompat.Builder builder;
		Context ctx;


13. En la clase `MyHandler`, agregue el siguiente código para invalidar el método **onRegistered**, que registra el dispositivo con el centro de notificaciones del servicio móvil.

		@Override
		public void onRegistered(Context context,  final String gcmRegistrationId) {
		    super.onRegistered(context, gcmRegistrationId);
	
		    new AsyncTask<Void, Void, Void>() {
		    		    	
		    	protected Void doInBackground(Void... params) {
		    		try {
		    		    ToDoActivity.mClient.getPush().register(gcmRegistrationId, null);
		    		    return null;
	    		    }
	    		    catch(Exception e) { 
			    		// handle error    		    
	    		    }
					return null;  		    
	    		}
		    }.execute();
		}



14. En la clase `MyHandler`, agregue el siguiente código para invalidar el método **onReceive**, que ocasiona que se visualice la notificación al recibirla.

		@Override
		public void onReceive(Context context, Bundle bundle) {
		    ctx = context;
		    String nhMessage = bundle.getString("message");
	
		    sendNotification(nhMessage);
		}
	
		private void sendNotification(String msg) {
			mNotificationManager = (NotificationManager)
		              ctx.getSystemService(Context.NOTIFICATION_SERVICE);
	
		    PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
		          new Intent(ctx, ToDoActivity.class), 0);
	
		    NotificationCompat.Builder mBuilder =
		          new NotificationCompat.Builder(ctx)
		          .setSmallIcon(R.drawable.ic_launcher)
		          .setContentTitle("Notification Hub Demo")
		          .setStyle(new NotificationCompat.BigTextStyle()
		                     .bigText(msg))
		          .setContentText(msg);
	
		     mBuilder.setContentIntent(contentIntent);
		     mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
		}


15. En el archivo TodoActivity.java, actualice el método **onCreate** de la clase *ToDoActivity* para registrar la clase de controlador de notificación. Asegúrese de agregar este código después de crear una instancia de *MobileServiceClient*.


		NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);

    Ahora su aplicación está actualizada para que sea compatible con las notificaciones push.

<!-- URLs. -->
[SDK de Android para Servicio móviles]: http://aka.ms/Iajk6q

<!---HONumber=August15_HO6-->