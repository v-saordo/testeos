<properties
	pageTitle="Los Centros de notificaciones de Azure notifican a los usuarios para Android con back-end de .NET"
	description="Aprenda a enviar notificaciones push a los usuarios en Azure. Ejemplos de código escritos en Java para Android"
	documentationCenter="android"
	services="notification-hubs"
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="12/16/2015"
	ms.author="wesmc"/>

#Los Centros de notificaciones de Azure notifican a los usuarios para Android con back-end de .NET


[AZURE.INCLUDE [notification-hubs-selector-aspnet-backend-notify-users](../../includes/notification-hubs-selector-aspnet-backend-notify-users.md)]

##Información general

La compatibilidad con las notificaciones de inserción en Azure le permite tener acceso a una infraestructura multiplataforma y de escalamiento horizontal fácil de usar, que simplifica considerablemente la implementación de notificaciones de inserción tanto en aplicaciones de consumidor, como en aplicaciones empresariales para plataformas móviles. Este tutorial muestra cómo puede utilizar los Centros de notificaciones de Azure para enviar notificaciones de inserción a un usuario de aplicaciones determinado en un dispositivo concreto. Un back-end de ASP.NET WebAPI se usa para autenticar clientes y generar notificaciones, tal como se muestra en el tema de referencia [Registro desde el back-end de la aplicación](notification-hubs-registration-management.md#registration-management-from-a-backend). Este tutorial se basa en el Centro de notificaciones que creó en el tutorial [Introducción a los Centros de notificaciones (Android)](notification-hubs-android-get-started.md).

> [AZURE.NOTE]En este tutorial se supone que se ha creado y configurado el Centro de notificaciones tal como se describe en [Introducción a los Centros de notificaciones (Android)](notification-hubs-android-get-started.md).

[AZURE.INCLUDE [notification-hubs-aspnet-backend-notifyusers](../../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## Creación del proyecto Android

El paso siguiente es crear la aplicación Android.

1. Siga el tutorial [Introducción a los Centros de notificaciones (Android)](notification-hubs-android-get-started.md) para crear y configurar la aplicación para recibir notificaciones push desde GCM.

2. Abra el archivo **res/layout/activity\_main.xml** y reemplace el contenido por las siguientes definiciones de contenido.

    Esto agrega nuevos controles de EditText para iniciar sesión como usuario. Además se agrega un campo para una etiqueta de nombre de usuario que será parte de las notificaciones que envíe:

		<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
            xmlns:tools="http://schemas.android.com/tools" android:layout_width="match_parent"
            android:layout_height="match_parent" android:paddingLeft="@dimen/activity_horizontal_margin"
            android:paddingRight="@dimen/activity_horizontal_margin"
            android:paddingTop="@dimen/activity_vertical_margin"
            android:paddingBottom="@dimen/activity_vertical_margin" tools:context=".MainActivity">

        <EditText
            android:id="@+id/usernameText"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:ems="10"
            android:hint="@string/usernameHint"
            android:layout_above="@+id/passwordText"
            android:layout_alignParentEnd="true" />
        <EditText
            android:id="@+id/passwordText"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:ems="10"
            android:hint="@string/passwordHint"
            android:inputType="textPassword"
            android:layout_above="@+id/buttonLogin"
            android:layout_alignParentEnd="true" />
        <Button
            android:id="@+id/buttonLogin"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="@string/loginButton"
            android:onClick="login"
            android:layout_above="@+id/toggleButtonGCM"
            android:layout_centerHorizontal="true"
            android:layout_marginBottom="24dp" />
        <ToggleButton
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:textOn="WNS on"
            android:textOff="WNS off"
            android:id="@+id/toggleButtonWNS"
            android:layout_toLeftOf="@id/toggleButtonGCM"
            android:layout_centerVertical="true" />
        <ToggleButton
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:textOn="GCM on"
            android:textOff="GCM off"
            android:id="@+id/toggleButtonGCM"
            android:checked="true"
            android:layout_centerHorizontal="true"
            android:layout_centerVertical="true" />
        <ToggleButton
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:textOn="APNS on"
            android:textOff="APNS off"
            android:id="@+id/toggleButtonAPNS"
            android:layout_toRightOf="@id/toggleButtonGCM"
            android:layout_centerVertical="true" />
        <EditText
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:id="@+id/editTextNotificationMessageTag"
            android:layout_below="@id/toggleButtonGCM"
            android:layout_centerHorizontal="true"
            android:hint="@string/notification_message_tag_hint" />
        <EditText
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:id="@+id/editTextNotificationMessage"
            android:layout_below="@+id/editTextNotificationMessageTag"
            android:layout_centerHorizontal="true"
            android:hint="@string/notification_message_hint" />
        <Button
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="@string/send_button"
            android:id="@+id/sendbutton"
            android:onClick="sendNotificationButtonOnClick"
            android:layout_below="@+id/editTextNotificationMessage"
            android:layout_centerHorizontal="true" />
        </RelativeLayout>



3. Abra el archivo **res/values/strings.xml** y reemplace la definición `send_button` por las siguientes líneas que redefinen la cadena para el `send_button` y agregan cadenas para los demás controles:

        <string name="usernameHint">Username</string>
        <string name="passwordHint">Password</string>
        <string name="loginButton">1. Log in</string>
        <string name="send_button">2. Send Notification</string>
        <string name="notification_message_tag_hint">
			Recipient username tag
		</string>

	El diseño gráfico main\_activity.xml ahora debe tener un aspecto como este:

	![][A1]

4. Cree una clase nueva llamada **RegisterClient** en el mismo paquete que su clase `MainActivity`. Use el código siguiente para el nuevo archivo de clase.

		import java.io.IOException;
        import java.io.UnsupportedEncodingException;
        import java.util.Set;

        import org.apache.http.HttpResponse;
        import org.apache.http.HttpStatus;
        import org.apache.http.client.ClientProtocolException;
        import org.apache.http.client.HttpClient;
        import org.apache.http.client.methods.HttpPost;
        import org.apache.http.client.methods.HttpPut;
        import org.apache.http.client.methods.HttpUriRequest;
        import org.apache.http.entity.StringEntity;
        import org.apache.http.impl.client.DefaultHttpClient;
        import org.apache.http.util.EntityUtils;
        import org.json.JSONArray;
        import org.json.JSONException;
        import org.json.JSONObject;

        import android.content.Context;
        import android.content.SharedPreferences;
        import android.util.Log;

        public class RegisterClient {
            private static final String PREFS_NAME = "ANHSettings";
            private static final String REGID_SETTING_NAME = "ANHRegistrationId";
            private String Backend_Endpoint;
            SharedPreferences settings;
            protected HttpClient httpClient;
            private String authorizationHeader;

            public RegisterClient(Context context, String backendEnpoint) {
                super();
                this.settings = context.getSharedPreferences(PREFS_NAME, 0);
                httpClient =  new DefaultHttpClient();
                Backend_Endpoint = backendEnpoint + "/api/register";
            }

            public String getAuthorizationHeader() {
                return authorizationHeader;
            }

            public void setAuthorizationHeader(String authorizationHeader) {
                this.authorizationHeader = authorizationHeader;
            }

            public void register(String handle, Set<String> tags) throws ClientProtocolException, IOException, JSONException {
                String registrationId = retrieveRegistrationIdOrRequestNewOne(handle);

                JSONObject deviceInfo = new JSONObject();
                deviceInfo.put("Platform", "gcm");
                deviceInfo.put("Handle", handle);
                deviceInfo.put("Tags", new JSONArray(tags));

                int statusCode = upsertRegistration(registrationId, deviceInfo);

                if (statusCode == HttpStatus.SC_OK) {
                    return;
                } else if (statusCode == HttpStatus.SC_GONE){
                    settings.edit().remove(REGID_SETTING_NAME).commit();
                    registrationId = retrieveRegistrationIdOrRequestNewOne(handle);
                    statusCode = upsertRegistration(registrationId, deviceInfo);
                    if (statusCode != HttpStatus.SC_OK) {
                        Log.e("RegisterClient", "Error upserting registration: " + statusCode);
                        throw new RuntimeException("Error upserting registration");
                    }
                } else {
                    Log.e("RegisterClient", "Error upserting registration: " + statusCode);
                    throw new RuntimeException("Error upserting registration");
                }
            }

            private int upsertRegistration(String registrationId, JSONObject deviceInfo)
                    throws UnsupportedEncodingException, IOException,
                    ClientProtocolException {
                HttpPut request = new HttpPut(Backend_Endpoint+"/"+registrationId);
                request.setEntity(new StringEntity(deviceInfo.toString()));
                request.addHeader("Authorization", "Basic "+authorizationHeader);
                request.addHeader("Content-Type", "application/json");
                HttpResponse response = httpClient.execute(request);
                int statusCode = response.getStatusLine().getStatusCode();
                return statusCode;
            }

            private String retrieveRegistrationIdOrRequestNewOne(String handle) throws ClientProtocolException, IOException {
                if (settings.contains(REGID_SETTING_NAME))
                    return settings.getString(REGID_SETTING_NAME, null);

                HttpUriRequest request = new HttpPost(Backend_Endpoint+"?handle="+handle);
                request.addHeader("Authorization", "Basic "+authorizationHeader);
                HttpResponse response = httpClient.execute(request);
                if (response.getStatusLine().getStatusCode() != HttpStatus.SC_OK) {
                    Log.e("RegisterClient", "Error creating registrationId: " + response.getStatusLine().getStatusCode());
                    throw new RuntimeException("Error creating Notification Hubs registrationId");
                }
                String registrationId = EntityUtils.toString(response.getEntity());
                registrationId = registrationId.substring(1, registrationId.length()-1);

                settings.edit().putString(REGID_SETTING_NAME, registrationId).commit();

                return registrationId;
            }
        }

	Este componente implementa las llamadas REST requeridas para ponerse en contacto con el back-end de la aplicación con la finalidad de registrar notificaciones de inserción. También almacena localmente los *registrationIds* creados por el Centro de notificaciones tal como se detalla en [Registro desde el back-end de la aplicación](notification-hubs-registration-management.md#registration-management-from-a-backend). Tenga en cuenta que usa un token de autorización almacenado localmente cuando hace clic en el botón **Iniciar sesión**.

5. En su clase `MainActivity`, elimine o convierta en comentario el campo privado para `NotificationHub` y agregue un campo para la clase `RegisterClient` y una cadena para el extremo del back-end de ASP.NET. Asegúrese de reemplazar `<Enter Your Backend Endpoint>` por el extremo de back-end obtenido anteriormente. Por ejemplo: `http://mybackend.azurewebsites.net`.


		//private NotificationHub hub;
		private RegisterClient registerClient;
	    private static final String BACKEND_ENDPOINT = "<Enter Your Backend Endpoint>";


6. En su clase `MainActivity`, en el método `onCreate`, quite o convierta en comentario la inicialización del campo `hub` y la llamada al método `registerWithNotificationHubs`. A continuación, agregue código para inicializar una instancia de la clase `RegisterClient`. El método debe contener las siguientes líneas:

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);

            MyHandler.mainActivity = this;
            NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);
            gcm = GoogleCloudMessaging.getInstance(this);

            //hub = new NotificationHub(HubName, HubListenConnectionString, this);
            //registerWithNotificationHubs();

	        registerClient = new RegisterClient(this, BACKEND_ENDPOINT);

            setContentView(R.layout.activity_main);
        }

7. En su clase `MainActivity`, elimine o convierta en comentario el método `registerWithNotificationHubs` completo. No se usará en este tutorial.

8. Agregue las siguientes instrucciones `import` al archivo **MainActivity.java**.

		import android.widget.Button;
		import java.io.UnsupportedEncodingException;
		import android.content.Context;
		import java.util.HashSet;
		import android.widget.Toast;
		import org.apache.http.client.ClientProtocolException;
		import java.io.IOException;
		import org.apache.http.HttpStatus;


9. A continuación, agregue los siguientes métodos para controlar el evento de clic del botón **Iniciar sesión** y el envío de notificaciones push.

	    @Override
	    protected void onStart() {
	    	super.onStart();
        	Button sendPush = (Button) findViewById(R.id.sendbutton);
	        sendPush.setEnabled(false);
	    }

		public void login(View view) throws UnsupportedEncodingException {
	    	this.registerClient.setAuthorizationHeader(getAuthorizationHeader());

	    	final Context context = this;
	    	new AsyncTask<Object, Object, Object>() {
				@Override
				protected Object doInBackground(Object... params) {
					try {
						String regid = gcm.register(SENDER_ID);
				        registerClient.register(regid, new HashSet<String>());
					} catch (Exception e) {
	                    DialogNotify("MainActivity - Failed to register", e.getMessage());
						return e;
					}
					return null;
				}

				protected void onPostExecute(Object result) {
                	Button sendPush = (Button) findViewById(R.id.sendbutton);
			        sendPush.setEnabled(true);
					Toast.makeText(context, "Logged in and registered.",
							Toast.LENGTH_LONG).show();
				}
			}.execute(null, null, null);
	    }

		private String getAuthorizationHeader() throws UnsupportedEncodingException {
			EditText username = (EditText) findViewById(R.id.usernameText);
	    	EditText password = (EditText) findViewById(R.id.passwordText);
	    	String basicAuthHeader = username.getText().toString()+":"+password.getText().toString();
	    	basicAuthHeader = Base64.encodeToString(basicAuthHeader.getBytes("UTF-8"), Base64.NO_WRAP);
	    	return basicAuthHeader;
		}

        /**
         * This method calls the ASP.NET WebAPI backend to send the notification message
         * to the platform notification service based on the pns parameter.
         *
         * @param pns     The platform notification service to send the notification message to. Must
         *                be one of the following ("wns", "gcm", "apns").
         * @param userTag The tag for the user who will receive the notification message. This string
         *                must not contain spaces or special characters.
         * @param message The notification message string. This string must include the double quotes
         *                to be used as JSON content.
         */
        public void sendPush(final String pns, final String userTag, final String message)
                throws ClientProtocolException, IOException {
            new AsyncTask<Object, Object, Object>() {
                @Override
                protected Object doInBackground(Object... params) {
                    try {

                        String uri = BACKEND_ENDPOINT + "/api/notifications";
                        uri += "?pns=" + pns;
                        uri += "&to_tag=" + userTag;

                        HttpPost request = new HttpPost(uri);
                        request.addHeader("Authorization", "Basic "+ getAuthorizationHeader());
                        request.setEntity(new StringEntity(message));
                        request.addHeader("Content-Type", "application/json");

                        HttpResponse response = new DefaultHttpClient().execute(request);

                        if (response.getStatusLine().getStatusCode() != HttpStatus.SC_OK) {
                            DialogNotify("MainActivity - Error sending " + pns + " notification",
								response.getStatusLine().toString());
                            throw new RuntimeException("Error sending notification");
                        }
                    } catch (Exception e) {
                        DialogNotify("MainActivity - Failed to send " + pns + " notification ", e.getMessage());
                        return e;
                    }

                    return null;
                }
            }.execute(null, null, null);
        }


	El controlador `login` para el botón **Iniciar sesión** genera un token de autenticación básico que usa el nombre de usuario y la contraseña de entrada (observe que esto representa cualquier token que el esquema de autenticación usa) y, a continuación, usa `RegisterClient` para llamar al back-end para registro.

	El método `sendPush` llama al back-end para desencadenar una notificación segura al usuario según la etiqueta del usuario. El servicio de notificación de plataforma al que `sendPush` apunta depende de la cadena `pns` transferida.

10. En su clase `MainActivity`, actualice el método `sendNotificationButtonOnClick` para llamar al método `sendPush` con los servicios de notificación de plataforma seleccionados del usuario tal como sigue.

        /**
         * Send Notification button click handler. This method sends the push notification
         * message to each platform selected.
         *
         * @param v The view
         */
        public void sendNotificationButtonOnClick(View v)
                throws ClientProtocolException, IOException {

            String nhMessageTag = ((EditText) findViewById(R.id.editTextNotificationMessageTag))
                    .getText().toString();
            String nhMessage = ((EditText) findViewById(R.id.editTextNotificationMessage))
                    .getText().toString();

            // JSON String
            nhMessage = """ + nhMessage + """;

            if (((ToggleButton)findViewById(R.id.toggleButtonWNS)).isChecked())
            {
                sendPush("wns", nhMessageTag, nhMessage);
            }
            if (((ToggleButton)findViewById(R.id.toggleButtonGCM)).isChecked())
            {
                sendPush("gcm", nhMessageTag, nhMessage);
            }
            if (((ToggleButton)findViewById(R.id.toggleButtonAPNS)).isChecked())
            {
                sendPush("apns", nhMessageTag, nhMessage);
            }
        }



## Ejecución de la aplicación


1. Ejecute la aplicación en un dispositivo o un emulador con Android Studio.

2. En la aplicación de Android, escriba un nombre de usuario y una contraseña. Ambos deben tener el mismo valor de cadena y no pueden contener espacios ni caracteres especiales.

3. En la aplicación de Android, haga clic en **Iniciar sesión**. Espere que aparezca un mensaje de aviso que indique **Sesión iniciada y registrado**. Esto habilitará el botón **Enviar notificación**.

	![][A2]

4. Haga clic en los botones de alternancia para habilitar todas las plataformas donde ejecutó la aplicación y registró un usuario.
5. Escriba el nombre del usuario que recibirá el mensaje de notificación. Ese usuario debe estar registrado para recibir notificaciones en los dispositivos de destino.

6. Escriba un mensaje para que el usuario lo reciba como un mensaje de notificación push.
7. Haga clic en **Enviar notificación**. Cada dispositivo que tiene un registro con la etiqueta de nombre de usuario coincidente recibirá la notificación push.


[A1]: ./media/notification-hubs-aspnet-backend-android-notify-users/android-notify-users.png
[A2]: ./media/notification-hubs-aspnet-backend-android-notify-users/android-notify-users-enter-password.png

<!---HONumber=AcomDC_1217_2015-->