<properties
	pageTitle="Vista previa de Azure AD B2C: Llamada a una API web desde una aplicación Android | Microsoft Azure"
	description="Este artículo le mostrará cómo crear una aplicación Android de "Lista de tareas pendientes" que llama a una API web de node.js mediante tokens de portador de OAuth 2.0. Tanto la aplicación Android como la API web usan Azure AD B2C para administrar identidades de usuario y autenticar usuarios."
	services="active-directory-b2c"
	documentationCenter="android"
	authors="brandwe"
	manager="msmbaldwin"
	editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="11/19/2015"
	ms.author="brandwe"/>

# Vista previa de Azure AD B2C: Llamada a una API web desde una aplicación Android

Con Azure AD B2C, puede agregar unas características de administración de identidades de autoservicio eficaces a sus aplicaciones Android y API web en unos cuantos pasos. Este artículo le mostrará cómo crear una aplicación Android de "Lista de tareas pendientes" que llama a una API web de node.js con tokens de portador de OAuth 2.0. Tanto la aplicación Android como la API web usan Azure AD B2C para administrar identidades de usuario y autenticar usuarios.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

 	
> [AZURE.NOTE]
	Este inicio rápido presenta un requisito previo de que hay que tener una API web protegida por Azure AD con B2C para poder funcionar completamente. Hemos creado uno para que lo pueda usar tanto para .Net como para node.js. Este tutorial supone que está configurado el ejemplo de API web de node.js. Consulte el [tutorial de la API de Azure AD B2C para node.js](active-directory-b2c-devquickstarts-api-node.md).

 
> [AZURE.NOTE]
	Este artículo no trata de la implementación de registros, inicios de sesión y administración de perfiles con Azure AD B2C. Se centra en la llamada a las API web después de que el usuario ya está autenticado. Si no lo ha hecho ya, debe comenzar con el [tutorial de introducción a las aplicaciones web .NET](active-directory-b2c-devquickstarts-web-dotnet.md) para obtener información sobre los conceptos básicos de Azure AD B2C.


Para los clientes de Android que necesitan tener acceso a recursos protegidos, Azure AD proporciona la Biblioteca de autenticación de Active Directory (ADAL). El único propósito de ADAL es facilitar a su aplicación la obtención de tokens de acceso. Para demostrar lo fácil que es, crearemos aquí una aplicación de lista de tareas pendientes de Android que permita realizar las siguientes acciones:

-	Obtener tokens de acceso para llamar a la API de lista de tareas pendientes utilizando el [protocolo de autenticación OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx)
-	Obtener listas de tareas pendientes de un usuario
-	Cierre la sesión de los usuarios.



### Paso 1: Obtener un directorio de Azure AD B2C

Para poder usar Azure AD B2C, debe crear un directorio o inquilino. Un directorio es un contenedor para todos los usuarios, aplicaciones, grupos, etc. Si no tiene uno todavía, vaya a [crear un directorio de B2C](active-directory-b2c-get-started.md) antes de continuar.

### Paso 2: Crear una aplicación

Ahora debe crear una aplicación en su directorio de B2C, que ofrece a Azure AD información que necesita para comunicarse de forma segura con su aplicación. Tanto la aplicación como la API web se representarán mediante un **Id. de aplicación** único en este caso, ya que conforman una aplicación lógica. Para crear una aplicación, siga [estas instrucciones](active-directory-b2c-app-registration.md). Asegúrese de

- Incluir una **aplicación web/API web** en la aplicación.
- Escribir `urn:ietf:wg:oauth:2.0:oob` como **dirección URL de respuesta**: es la dirección URL predeterminada para este ejemplo de código.
- Crear un **Secreto de aplicación ** para la aplicación y copiarlo. Lo necesitará en breve.
- Escribir el **Id. de aplicación** asignado a la aplicación. También lo necesitará en breve.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

### Paso 3: Crear sus directivas

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

En Azure AD B2C, cada experiencia del usuario se define mediante una [**directiva**](active-directory-b2c-reference-policies.md). Esta aplicación contiene tres experiencias de identidad: registro, inicio de sesión e inicio de sesión con Facebook. Debe crear una directiva de cada tipo, como se describe en el [artículo de referencia de directiva](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Al crear sus tres directivas, asegúrese de:

- Seleccionar **Nombre para mostrar** y algunos otros atributos de registro en la directiva de registro.
- Elegir las notificaciones de aplicación **Nombre para mostrar** e **Id. de objeto** en todas las directivas. Puede elegir también otras notificaciones.
- Copiar el **Nombre** de cada directiva después de crearla. Debe tener el prefijo `b2c_1_`. Necesitará esos nombres de directivas en breve. 

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Cuando tenga tres directivas creadas correctamente, estará listo para crear su aplicación.

Tenga en cuenta que este artículo no trata de cómo usar las directivas que acaba de crear. Si quiere aprender cómo funcionan las directivas en Azure AD B2C, debe comenzar con el [tutorial de introducción a las aplicaciones web .NET](active-directory-b2c-devquickstarts-web-dotnet.md).

### Paso 4: Descargar el código

El código de este tutorial se conserva [en GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android). Para generar el ejemplo a medida que avance, puede [descargar un proyecto de esqueleto como .zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/skeleton.zip) o clonar el esqueleto:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-Android.git
```

> [AZURE.NOTE]**Es necesaria la descarga del esqueleto para completar este tutorial.** Debido a la complejidad de la implementación de una aplicación totalmente funcional en Android, el **esqueleto** tiene el código de experiencia de usuario que se ejecutará una vez completado el tutorial siguiente. Se trata de una medida de ahorro de tiempo para los desarrolladores. El código de experiencia de usuario no es relevante para el tema de cómo agregar B2C a una aplicación Android.

La aplicación completada también estará [disponible como .zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/complete.zip) o en la rama `complete` del mismo repositorio.


Para compilar con Maven, puede utilizar pom.xml en el nivel superior.


  * Siga los pasos descritos en la [sección de requisitos previos para configurar Maven para Android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android/wiki/Setting-up-maven-environment-for-Android).
  * Configure el emulador con SDK 21.
  * Vaya a la carpeta raíz donde ha clonado el repositorio.
  * Ejecute el comando: mvn clean install.
  * Acceda al directorio de la muestra de inicio rápido: cd samples\\hello.
  * Ejecute el comando: mvn android:deploy android:run.
  * La aplicación debería iniciarse.
  * Escriba las credenciales del usuario de prueba para probarlas.

Los paquetes Jar se enviarán también junto con el paquete aar.

### Paso 5: Descargar ADAL de Android y agregarlo al área de trabajo de Android Studio

Ahora tiene varias opciones para usar esta biblioteca en el proyecto Android:

* Puede usar el código fuente para importar esta biblioteca en Eclipse y vincularla a la aplicación.
* Si utiliza Studio Android, puede usar el formato de paquete *aar* y hacer referencia a los archivos binarios.



####Opción 1: binarios a través de Gradle (recomendado)

Puede obtener los archivos binarios desde el repositorio central de Maven. El paquete AAR puede incluirse de la siguiente forma en el proyecto en AndroidStudio (ejemplo: en `build.gradle`):

```gradle
repositories {
    mavenCentral()
    flatDir {
        dirs 'libs'
    }
    maven {
        url "YourLocalMavenRepoPath\\.m2\\repository"
    }
}
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile('com.microsoft.aad:adal:2.0.1-alpha') {
        exclude group: 'com.android.support'
    } // Recent version is 2.0.1-alpha
}
```

####Opción 2: aar a través de Maven

Si usa el complemento m2e en Eclipse, puede especificar la dependencia en el archivo `pom.xml`:

```xml
<dependency>
    <groupId>com.microsoft.aad</groupId>
    <artifactId>adal</artifactId>
    <version>2.0.1-alpha</version>
    <type>aar</type>
</dependency>
```

####Opción 3: código fuente a través de Git (último recurso)

Para obtener el código fuente del SDK a través de Git, simplemente escriba:

    git clone git@github.com:AzureAD/azure-activedirectory-library-for-android.git
    cd ./azure-activedirectory-library-for-android/src
    
    use the branch "convergence"


### Paso 6: Establecer el archivo de configuración

Vamos a usar la configuración establecida en el portal de B2C anterior para configurar el proyecto de Android.

Abra `helpes/Constants.java` y rellene los valores para lo siguiente:

```

package com.microsoft.aad.taskapplication.helpers;

import com.microsoft.aad.adal.AuthenticationResult;

public class Constants {

    public static final String SDK_VERSION = "1.0";
    public static final String UTF8_ENCODING = "UTF-8";
    public static final String HEADER_AUTHORIZATION = "Authorization";
    public static final String HEADER_AUTHORIZATION_VALUE_PREFIX = "Bearer ";

    // -------------------------------AAD
    // PARAMETERS----------------------------------
    public static String AUTHORITY_URL = "https://login.microsoftonline.com/hypercubeb2c.onmicrosoft.com/";
    public static String CLIENT_ID = "<your application id>";
    public static String[] SCOPES = {"<your application id>"};
    public static String[] ADDITIONAL_SCOPES = {""};
    public static String REDIRECT_URL = "<redirect uri>";
    public static String CORRELATION_ID = "";
    public static String USER_HINT = "";
    public static String EXTRA_QP = "";
    public static String FB_POLICY = "B2C_1_<your policy>";
    public static String EMAIL_SIGNIN_POLICY = "B2C_1_<your policy>";
    public static String EMAIL_SIGNUP_POLICY = "B2C_1_<your policy>";
    public static boolean FULL_SCREEN = true;
    public static AuthenticationResult CURRENT_RESULT = null;
    // Endpoint we are targeting for the deployed WebAPI service
    public static String SERVICE_URL = "http://localhost:3000/tasks";

    // ------------------------------------------------------------------------------------------

    static final String TABLE_WORKITEM = "WorkItem";
    public static final String SHARED_PREFERENCE_NAME = "com.example.com.test.settings";


}


```
**SCOPES**: se trata de los ámbitos que pasamos al servidor que queremos solicitar desde el servidor para el inicio de sesión del usuario. Para la vista previa de B2C, pasamos client\_id. Sin embargo, esto cambiará la lectura de ámbitos en el futuro. Entonces se actualizará este documento.
**ADDITIONAL\_SCOPES**: son los ámbitos adicionales que podemos usar para la aplicación. Esto se usará en el futuro.
**CLIENT\_ID**: el id. de aplicación que obtuvo en el portal.
**REDIRECT\_URL**: la redirección donde se espera que se envíe de nuevo el token.
**EXTRA\_QP**: cualquier elemento adicional que desea pasar al servidor en formato codificado de dirección URL.
**FB\_POLICY**: la directiva que está invocando. La parte más importante de este tutorial.
**EMAIL\_SIGNIN\_POLICY**: la directiva que está invocando. La parte más importante de este tutorial.
**EMAIL\_SIGNUP\_POLICY**: la directiva que está invocando. La parte más importante de este tutorial.

### Paso 7: Agregar referencias a ADAL de Android en el proyecto


> [AZURE.NOTE]ADAL para Android usa un modelo basado en intentos para invocar la autenticación. Los intentos dependen de la aplicación para realizar el trabajo. Todo este ejemplo y, en realidad, todo el uso de ADAL para Android, consiste en administrar los intentos y pasar información entre ellos.


Lo primero que debemos hacer es indicarle a Android el diseño de la aplicación, incluidos los Intents() que deseamos usar. Voy a explicar estos intentos de forma detallada más adelante.

Actualice el archivo AndroidManifest.xml del proyecto para que incluya todos los intentos:

```
   <?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.microsoft.aad.taskapplication"
    android:versionCode="1"
    android:versionName="1.0" >

    <uses-sdk
        android:minSdkVersion="11"
        android:targetSdkVersion="19" />

    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />

    <application
        android:allowBackup="true"
        android:icon="@drawable/ic_launcher"
        android:label="@string/app_name"
        android:theme="@style/AppTheme" >
        <activity
            android:name="com.microsoft.aad.adal.AuthenticationActivity"
            android:configChanges="orientation|keyboardHidden|screenSize"
            android:exported="true"
            android:label="@string/title_login_hello_app" >
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.LoginActivity"
            android:configChanges="orientation|keyboardHidden|screenSize"
            android:label="@string/app_name" >
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.UsersListActivity"
            android:label="@string/title_activity_feed" >
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.SettingsActivity"
            android:label="@string/title_activity_settings" >
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.AddTaskActivity"
            android:label="@string/title_activity_settings" >
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.ToDoActivity"
            android:label="@string/app_name" >
        </activity>
    </application>

</manifest>    
```

Como puede ver, definimos las cinco actividades que vamos a usar.

**AuthenticationActivity**: procede de ADAL y es la que proporciona la vista web de inicio de sesión.

**LoginActivity**: es la que muestra las directivas de inicio de sesión y los botones para cada directiva.

**SettingsActivity**: permite cambiar la configuración de la aplicación en tiempo de ejecución.

**AddTaskActivity**: permite agregar tareas a la API de REST protegida por Azure AD.

**ToDoActivity**: la actividad principal que muestra las tareas.



### Paso 8: Crear la actividad Login

Vamos a crear una actividad principal y la llamaremos `LoginActivity`.

Cree un archivo que se llame `LoginActivity.java`.

Se necesita inicializar la actividad y agregar algunos botones que controlen la interfaz de usuario. De nuevo, esto es bastante sencillo y le resultará familiar si ha escrito código Android antes:

```
import android.app.Activity;
import android.app.ProgressDialog;
import android.content.Intent;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.Toast;

import com.microsoft.aad.adal.AuthenticationContext;
import com.microsoft.aad.taskapplication.helpers.Constants;
import com.microsoft.aad.taskapplication.helpers.WorkItemAdapter;

/**
 * Created by brwerner on 9/17/15.
 */
public class LoginActivity extends Activity {

    private final static String TAG = "ToDoActivity";

    private AuthenticationContext mAuthContext;

    /**
     * Adapter to sync the items list with the view
     */
    private WorkItemAdapter mAdapter = null;

    /**
     * Show this dialog when activity first launches to check if user has login
     * or not.
     */
    private ProgressDialog mLoginProgressDialog;

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_signin);
        Toast.makeText(getApplicationContext(), TAG + "LifeCycle: OnCreate", Toast.LENGTH_SHORT)
                .show();

        Button button = (Button) findViewById(R.id.signin_facebook);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(LoginActivity.this, ToDoActivity.class);
                intent.putExtra("thePolicy", Constants.FB_POLICY);
                startActivity(intent);

            }
        });

        button = (Button) findViewById(R.id.signin_email);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(LoginActivity.this, ToDoActivity.class);
                intent.putExtra("thePolicy", Constants.EMAIL_SIGNIN_POLICY);
                startActivity(intent);

            }
        });

        button = (Button) findViewById(R.id.signup_email);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(LoginActivity.this, ToDoActivity.class);
                intent.putExtra("thePolicy", Constants.EMAIL_SIGNUP_POLICY);
                startActivity(intent);

            }
        });

    }

}


```
Lo que hemos hecho es crear botones que llaman al intento ToDoActivity (que llamará a ADAL cuando necesitemos un token) con nuestra propia actividad como referencia y un parámetro adicional. El método `intent.putExtra()` pasa este parámetro adicional. Aquí puede ver que definimos "thePolicy" del modo en que lo especificó en `Constants.java`. Esto permite al intento saber qué directiva se va a invocar durante la autenticación.

### Paso 9: Crear la actividad Settings

Es solo una actividad que rellena la interfaz de usuario de configuración.

Cree un archivo que se llame `SettingsActivity.java`.

Este es un CRUD muy sencillo:

```
 package com.microsoft.aad.taskapplication;

import android.app.Activity;
import android.content.SharedPreferences;
import android.content.SharedPreferences.Editor;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.CheckBox;
import android.widget.Switch;
import android.widget.TextView;

import com.microsoft.aad.taskapplication.helpers.Constants;

/**
 * Settings page to try broker by options
 */
public class SettingsActivity extends Activity {

	//private CheckBox checkboxAskBroker, checkboxCheckBroker;
    private Switch fullScreenSwitch;

	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.activity_settings);

        loadSettings();
//		checkboxAskBroker = (CheckBox) findViewById(R.id.askInstall);
//		checkboxCheckBroker = (CheckBox) findViewById(R.id.useBroker);

        Button save = (Button) findViewById(R.id.settingsSave);

        save.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                TextView textView = (TextView)findViewById(R.id.authority);
                Constants.AUTHORITY_URL = textView.getText().toString();

                textView = (TextView)findViewById(R.id.resource);
            //    Constants.SCOPES = textView.getText().toString();

                textView = (TextView)findViewById(R.id.clientId);
                Constants.CLIENT_ID = textView.getText().toString();

                textView = (TextView)findViewById(R.id.extraQueryParameters);
                Constants.EXTRA_QP = textView.getText().toString();

                textView = (TextView)findViewById(R.id.redirectUri);
                Constants.REDIRECT_URL = textView.getText().toString();

                textView = (TextView)findViewById(R.id.serviceUrl);
                Constants.SERVICE_URL = textView.getText().toString();

                textView = (TextView)findViewById(R.id.serviceUrl);
                textView.setText(Constants.SERVICE_URL);

                textView = (TextView)findViewById(R.id.fb_signin);
                textView.setText(Constants.FB_POLICY);

                textView = (TextView)findViewById(R.id.email_signin);
                textView.setText(Constants.EMAIL_SIGNIN_POLICY);

                textView = (TextView)findViewById(R.id.email_signup);
                textView.setText(Constants.EMAIL_SIGNUP_POLICY);

                textView = (TextView)findViewById(R.id.correlationId);
                textView.setText(Constants.CORRELATION_ID);

                fullScreenSwitch = (Switch)findViewById(R.id.fullScreen);
                Constants.FULL_SCREEN = fullScreenSwitch.isChecked();

                finish();
            }
        });


	}

    private void loadSettings() {
        TextView textView = (TextView)findViewById(R.id.authority);
        textView.setText(Constants.AUTHORITY_URL);
        textView = (TextView)findViewById(R.id.resource);
        textView.setText(Constants.SCOPES[0]);
        textView = (TextView)findViewById(R.id.clientId);
        textView.setText(Constants.CLIENT_ID);
        textView = (TextView)findViewById(R.id.extraQueryParameters);
        textView.setText(Constants.EXTRA_QP);
        textView = (TextView)findViewById(R.id.redirectUri);
        textView.setText(Constants.REDIRECT_URL);
        textView = (TextView)findViewById(R.id.serviceUrl);
        textView.setText(Constants.SERVICE_URL);
        textView = (TextView)findViewById(R.id.fb_signin);
        textView.setText(Constants.FB_POLICY);
        textView = (TextView)findViewById(R.id.email_signin);
        textView.setText(Constants.EMAIL_SIGNIN_POLICY);
        textView = (TextView)findViewById(R.id.email_signup);
        textView.setText(Constants.EMAIL_SIGNUP_POLICY);
        textView = (TextView)findViewById(R.id.correlationId);
        textView.setText(Constants.CORRELATION_ID);

        fullScreenSwitch = (Switch)findViewById(R.id.fullScreen);
        fullScreenSwitch.setChecked(Constants.FULL_SCREEN);
    }

	private void saveSettings(String key, boolean value) {
		SharedPreferences prefs = SettingsActivity.this.getSharedPreferences(
				Constants.SHARED_PREFERENCE_NAME, Activity.MODE_PRIVATE);
		Editor prefsEditor = prefs.edit();
		prefsEditor.putBoolean(key, value);
		prefsEditor.commit();
	}
}
```

### Paso 10: Crear la actividad AddTask

Esto nos permitirá agregar una tarea al extremo de la API de REST. De nuevo, esto es bastante sencillo.

Cree un archivo que se llame `AddTaskActivity.java`.

Y escriba:

```
package com.microsoft.aad.taskapplication;

import android.app.Activity;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.EditText;

import com.microsoft.aad.taskapplication.helpers.Constants;
import com.microsoft.aad.taskapplication.helpers.TodoListHttpService;

public class AddTaskActivity extends Activity {

    EditText textField;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_add_task);
        textField = (EditText) findViewById(R.id.taskToAdd);
        Button button = (Button) findViewById(R.id.postTaskbutton);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (textField.getText().toString() != null
                        && !textField.getText().toString().trim().isEmpty()
                        && Constants.CURRENT_RESULT != null) {

                    TodoListHttpService service = new TodoListHttpService();
                    try {
                        service.addItem(textField.getText().toString(), Constants.CURRENT_RESULT.getAccessToken());
                    } catch (Exception e) {
                        SimpleAlertDialog.showAlertDialog(getApplicationContext(), "Exception caught", e.getMessage());
                    }
                    finish();
                }
            }
        });
    }

}

```

### Paso 11: Crear la actividad ToDoList

Ahora tenemos la actividad más importante, la que nos permite obtener un token de Azure AD para la directiva y usar, a continuación, ese token para llamar al servidor de la API de REST de tarea.

Cree un archivo que se llame `ToDoActivity.java`.

Escriba lo siguiente (las llamadas se explicarán posteriormente):

```

package com.microsoft.aad.taskapplication;

import android.app.Activity;
import android.app.AlertDialog;
import android.app.ProgressDialog;
import android.content.Intent;
import android.os.Build;
import android.os.Bundle;
import android.view.View;
import android.view.Window;
import android.webkit.CookieManager;
import android.webkit.CookieSyncManager;
import android.widget.ArrayAdapter;
import android.widget.Button;
import android.widget.ListView;
import android.widget.TextView;
import android.widget.Toast;

import com.microsoft.aad.adal.AuthenticationCallback;
import com.microsoft.aad.adal.AuthenticationContext;
import com.microsoft.aad.adal.AuthenticationResult;
import com.microsoft.aad.adal.AuthenticationSettings;
import com.microsoft.aad.adal.UserIdentifier;
import com.microsoft.aad.adal.PromptBehavior;
import com.microsoft.aad.taskapplication.helpers.Constants;
import com.microsoft.aad.taskapplication.helpers.InMemoryCacheStore;
import com.microsoft.aad.taskapplication.helpers.TodoListHttpService;
import com.microsoft.aad.taskapplication.helpers.Utils;
import com.microsoft.aad.taskapplication.helpers.WorkItemAdapter;


import java.io.UnsupportedEncodingException;
import java.net.MalformedURLException;
import java.net.URL;
import java.security.NoSuchAlgorithmException;
import java.security.spec.InvalidKeySpecException;
import java.util.ArrayList;
import java.util.List;
import java.util.UUID;

public class ToDoActivity extends Activity {



    private final static String TAG = "ToDoActivity";

    private AuthenticationContext mAuthContext;
    private static AuthenticationResult sResult;

    /**
     * Adapter to sync the items list with the view
     */
    private WorkItemAdapter mAdapter = null;

    /**
     * Show this dialog when activity first launches to check if user has login
     * or not.
     */
    private ProgressDialog mLoginProgressDialog;


    /**
     * Initializes the activity
     */
    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_list_todo_items);
        CookieSyncManager.createInstance(getApplicationContext());
        Toast.makeText(getApplicationContext(), TAG + "LifeCycle: OnCreate", Toast.LENGTH_SHORT)
                .show();

        // Clear previous sessions
        clearSessionCookie();
        try {
            // Provide key info for Encryption
            if (Build.VERSION.SDK_INT < 18) {
                Utils.setupKeyForSample();
            }

        Button button = (Button) findViewById(R.id.addTaskButton);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(ToDoActivity.this, AddTaskActivity.class);
                startActivity(intent);
            }
        });

        button = (Button) findViewById(R.id.appSettingsButton);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(ToDoActivity.this, SettingsActivity.class);
                startActivity(intent);

            }
        });

        button = (Button) findViewById(R.id.switchUserButton);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(ToDoActivity.this, LoginActivity.class);
                startActivity(intent);

            }
        });


        final TextView name = (TextView)findViewById(R.id.userLoggedIn);


        mLoginProgressDialog = new ProgressDialog(this);
        mLoginProgressDialog.requestWindowFeature(Window.FEATURE_NO_TITLE);
        mLoginProgressDialog.setMessage("Login in progress...");
        mLoginProgressDialog.show();
        // Ask for token and provide callback
        try {
            mAuthContext = new AuthenticationContext(ToDoActivity.this, Constants.AUTHORITY_URL,
                    false);
            String policy = getIntent().getStringExtra("thePolicy");

            if(Constants.CORRELATION_ID != null &&
                    Constants.CORRELATION_ID.trim().length() !=0){
                mAuthContext.setRequestCorrelationId(UUID.fromString(Constants.CORRELATION_ID));
            }

            AuthenticationSettings.INSTANCE.setSkipBroker(true);

            mAuthContext.acquireToken(ToDoActivity.this, Constants.SCOPES, Constants.ADDITIONAL_SCOPES, policy, Constants.CLIENT_ID,
                    Constants.REDIRECT_URL, getUserInfo(), PromptBehavior.Always,
                    "nux=1&" + Constants.EXTRA_QP,
                    new AuthenticationCallback<AuthenticationResult>() {

                        @Override
                        public void onError(Exception exc) {
                            if (mLoginProgressDialog.isShowing()) {
                                mLoginProgressDialog.dismiss();
                            }
                            SimpleAlertDialog.showAlertDialog(ToDoActivity.this,
                                    "Failed to get token", exc.getMessage());
                        }

                        @Override
                        public void onSuccess(AuthenticationResult result) {
                            if (mLoginProgressDialog.isShowing()) {
                                mLoginProgressDialog.dismiss();
                            }

                            if (result != null && !result.getToken().isEmpty()) {
                                setLocalToken(result);
                                updateLoggedInUser();
                                getTasks();
                                ToDoActivity.sResult = result;
                                Toast.makeText(getApplicationContext(), "Token is returned", Toast.LENGTH_SHORT)
                                        .show();

                                if (sResult.getUserInfo() != null) {
                                    name.setText(result.getUserInfo().getDisplayableId());
                                    Toast.makeText(getApplicationContext(),
                                            "User:" + sResult.getUserInfo().getDisplayableId(), Toast.LENGTH_SHORT)
                                            .show();
                                }
                            } else {
                                //TODO: popup error alert
                            }
                        }
                    });
        } catch (Exception e) {
            SimpleAlertDialog.showAlertDialog(ToDoActivity.this, "Exception caught", e.getMessage());
        }
        Toast.makeText(ToDoActivity.this, TAG + "done", Toast.LENGTH_SHORT).show();
    } catch (InvalidKeySpecException e) {
            e.printStackTrace();
        } catch (NoSuchAlgorithmException e) {
            e.printStackTrace();
        } catch (UnsupportedEncodingException e) {
            e.printStackTrace();
        }}
   
```

        
 Es posible que observe que esto está basado en métodos aún no escritos, como `updateLoggedInUser()`, `clearSessionCookie()` y `getTasks()`. Los escribiremos más adelante. Puede omitir los errores sin problemas por ahora en Android Studio.

Explicación de los parámetros:

  * ***SCOPES*** es obligatorio y son los ámbitos para los que se está intentando solicitar acceso. Para la vista previa de B2C, esto es lo mismo que Clientid pero va a cambiar en el futuro.
  * ***POLICY*** es la directiva para la que desea autenticar al usuario. 
  * ***CLIENT\_ID*** es obligatorio y procede del Portal de Azure AD.
  * Puede configurar redirectUri como el nombre del paquete. No es necesario que se proporcione la llamada a acquireToken.
  * ***getUserInfo()*** es la forma en que buscamos si el usuario ya está en la memoria caché y le preguntamos si no se encuentra o el token de acceso no es válido. Escribiremos este método más adelante.
  * ***PromptBehavior*** ayuda a solicitar las credenciales para omitir la memoria caché y la cookie.
  * Se llamará a ***Callback*** después de intercambiar el código de autorización para un token.

  La devolución de llamada tendrá un objeto de AuthenticationResult que tiene accesstoken, fecha de caducidad e información sobre el token del identificador.

> [AZURE.NOTE]La aplicación del portal de empresa de Microsoft Intune proporciona el componente de agente y puede instalarse en el dispositivo del usuario. Los desarrolladores deben estar preparados para permitir su uso ya que proporciona el SSO en todas las aplicaciones del dispositivo. ADAL para Android usará la cuenta del agente si hay una cuenta de usuario creada en el autenticador. Para usar el agente, el desarrollador necesita registrar un redirectUri especial para el uso del agente. RedirectUri tiene el formato msauth://packagename/Base64UrlencodedSignature. Puede obtener el redirecturi para la aplicación mediante el script `brokerRedirectPrint.ps1` o usar la llamada a la API `mContext.getBrokerRedirectUri()`. La firma está relacionada con los certificados de firma procedentes de Google Play Store.

 El desarrollador puede omitir el usuario del agente con:

    ```java
     AuthenticationSettings.Instance.setSkipBroker(true);
    ```
> [AZURE.NOTE]Con el fin de reducir la complejidad de esta guía de inicio rápido de B2C, hemos optado, en nuestro ejemplo, por omitir el agente.

A continuación, vamos a crear algunos métodos auxiliares que obtendrán el token solo durante las llamadas de autenticación a la API de tarea.

**En el mismo archivo** llamado `ToDoActivity.java`

```
    private void getToken(final AuthenticationCallback callback) {

        String policy = getIntent().getStringExtra("thePolicy");

        // one of the acquireToken overloads
        mAuthContext.acquireToken(ToDoActivity.this, Constants.SCOPES, Constants.ADDITIONAL_SCOPES,
                policy, Constants.CLIENT_ID, Constants.REDIRECT_URL, getUserInfo(),
                PromptBehavior.Always, "nux=1&" + Constants.EXTRA_QP, callback);
    }
```

Vamos a agregar también algunos métodos que van a ejecutar un "set" y "get" de nuestro AuthenticationResult (que tiene nuestro token) en las CONSTANTES globales. Esto es necesario porque, aunque `ToDoActivity.java` usa **sResult** en sus flujos, las demás actividades no tienen acceso al token para trabajar (como agregar una tarea a `AddTaskActivity.java`)

```

    private AuthenticationResult getLocalToken() {
        return Constants.CURRENT_RESULT;
    }

    private void setLocalToken(AuthenticationResult newToken) {


        Constants.CURRENT_RESULT = newToken;
    }

    
```
### Paso 12: Crear un método para devolver un UserIdentifier

ADAL para Android representa el usuario en forma de objeto **UserIdentifier**. Esto administra el usuario y permite saber si se está usando el mismo usuario en las llamadas, de modo que podemos confiar en la caché en vez de realizar una nueva llamada al servidor. Para facilitar esta tarea, creamos un `getUserInfo()` que devolverá un UserIdentifier que podemos usar para `acquireToken()`. También creamos un método getUniqueId() que rápidamente devolverá el Id. de UserIdentifier de la memoria caché.

```
  private String getUniqueId() {
        if (sResult != null && sResult.getUserInfo() != null
                && sResult.getUserInfo().getUniqueId() != null) {
            return sResult.getUserInfo().getUniqueId();
        }

        return null;
    }

    private UserIdentifier getUserInfo() {

        final TextView names = (TextView)findViewById(R.id.userLoggedIn);
        String name = names.getText().toString();
        return new UserIdentifier(name, UserIdentifier.UserIdentifierType.OptionalDisplayableId);
    }
    
```
 
### Paso 13: Escribir algunos métodos auxiliares

Necesitamos escribir algunos métodos auxiliares que nos ayuden a borrar las cookies y ofrecer un AuthenticationCallback. Se usan exclusivamente para el ejemplo a fin de asegurarse de que estamos en un estado limpio al llamar a la actividad ToDo.

**En el mismo archivo** llamado `ToDoActivity.java`

```

    private void clearSessionCookie() {

        CookieManager cookieManager = CookieManager.getInstance();
        cookieManager.removeSessionCookie();
        CookieSyncManager.getInstance().sync();
    }
``` 

```
    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        mAuthContext.onActivityResult(requestCode, resultCode, data);
    }
    
```   

### Paso 14: Llamar a la API de tarea

Ahora que tenemos la actividad cableada y lista para realizar el trabajo pesado de captar tokens, vamos a escribir la API para acceder al servidor de tarea.

Nuestro `getTasks` proporciona una matriz que representa las tareas en el servidor.

Vamos a escribir primero nuestro `getTask`:

**En el mismo archivo** llamado `ToDoActivity.java`

```
    private void getTasks() {
        if (sResult == null || sResult.getToken().isEmpty())
            return;

        List<String> items = new ArrayList<>();
        try {
            items = new TodoListHttpService().getAllItems(sResult.getToken());
        } catch (Exception e) {
            items = new ArrayList<>();
        }

        ListView listview = (ListView) findViewById(R.id.listViewToDo);
        ArrayAdapter<String> adapter = new ArrayAdapter<String>(this,
                android.R.layout.simple_list_item_1, android.R.id.text1, items);
        listview.setAdapter(adapter);
    }

```


 
 Vamos a escribir también un método que inicializará las tablas en la primera ejecución.
 
 **En el mismo archivo** llamado `ToDoActivity.java`
 
```
    private void initAppTables() {
        try {
            ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
            listViewToDo.setAdapter(mAdapter);

        } catch (Exception e) {
            createAndShowDialog(new Exception(
                    "There was an error creating the Mobile Service. Verify the URL"), "Error");
        }
    }

```
 
 Verá que este código requiere algunos métodos adicionales para que funcione. Vamos a escribirlos ahora.
 
### Creación del generador de direcciones URL de extremo
 
 Es necesario generar la dirección URL de extremo a la que se va a conectar. Vamos a hacerlo en el mismo archivo de clase.
 
 **En el mismo archivo** llamado `ToDoActivity.java`
 
  ```
    private URL getEndpointUrl() {
        URL endpoint = null;
        try {
            endpoint = new URL(Constants.SERVICE_URL);
        } catch (MalformedURLException e) {
            e.printStackTrace();
        }
        return endpoint;
    }

 ```


Tenga en cuenta que agregamos el token de acceso a la solicitud en el código siguiente:

### Paso 15: Escribir algunos métodos de la experiencia de usuario

Android requiere controlar algunas devoluciones de llamada para que funcione la aplicación. Son `createAndShowDialog` y `onResume()`. Esto es bastante sencillo y le resultará familiar si ha escrito código Android antes:

Vamos a escribirlas ahora.

**En el mismo archivo** llamado `ToDoActivity.java`

```
    @Override
    public void onResume() {
        super.onResume(); // Always call the superclass method first

        updateLoggedInUser();
        // User can click logout, it will come back here
        // It should refresh list again
        getTasks();
    }

    
```

Y ahora, vamos a administrar las devoluciones de llamada del cuadro de diálogo.

**En el mismo archivo** llamado `ToDoActivity.java`


```
    /**
     * Creates a dialog and shows it
     *
     * @param exception The exception to show in the dialog
     * @param title     The dialog title
     */
    private void createAndShowDialog(Exception exception, String title) {
        createAndShowDialog(exception.toString(), title);
    }

    /**
     * Creates a dialog and shows it
     *
     * @param message The dialog message
     * @param title   The dialog title
     */
    private void createAndShowDialog(String message, String title) {
        AlertDialog.Builder builder = new AlertDialog.Builder(this);

        builder.setMessage(message);
        builder.setTitle(title);
        builder.create().show();
    }
    
```

Eso es todo. Debe tener un archivo `ToDoActivity.java` que se compila. Todo el proyecto debe compilarse también en este momento.
    


### Paso 16: Ejecutar la aplicación de ejemplo

Por último, cree y ejecute la aplicación tanto en Android Studio como en Eclipse. Regístrese o inicie sesión en la aplicación y cree tareas para el usuario que ha iniciado sesión. Cierre sesión y vuelva a iniciarla como otro usuario, y cree tareas para ese usuario.

Observe cómo se almacenan las tareas por usuario en la API, puesto que la API extrae la identidad del usuario del token de acceso que recibe.

Como referencia, el ejemplo completado [se ofrece aquí en forma de archivo .zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/complete.zip), aunque también puede clonarlo desde GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-Android```


### Información importante


#### Cifrado

ADAL cifra los tokens y los almacena en SharedPreferences de forma predeterminada. Puede consultar la clase StorageHelper para ver los detalles. Android introdujo el almacenamiento seguro de claves privadas AndroidKeyStore 4.3 (API18). ADAL lo utiliza para API18 y las versiones posteriores. Si desea usar ADAL para las versiones inferiores de SDK, deberá proporcionar la clave secreta en AuthenticationSettings.INSTANCE.setSecretKey.

#### Cookies de sesión en WebView

Android WebView no elimina las cookies de sesión después de cerrar la aplicación. Se puede controlar con el código de ejemplo siguiente:
```
CookieSyncManager.createInstance(getApplicationContext());
CookieManager cookieManager = CookieManager.getInstance();
cookieManager.removeSessionCookie();
CookieSyncManager.getInstance().sync();
```
Más información sobre las cookies: http://developer.android.com/reference/android/webkit/CookieSyncManager.html
 

<!---HONumber=AcomDC_1125_2015-->


