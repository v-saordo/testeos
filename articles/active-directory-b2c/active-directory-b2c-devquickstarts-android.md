<properties
	pageTitle="Versión preliminar de Azure Active Directory B2C: llamada a una API web desde una aplicación Android | Microsoft Azure"
	description="Este artículo le mostrará cómo crear una aplicación de "lista de tareas pendientes" para Android que llame a una API web de Node.js mediante tokens de portador de OAuth 2.0. Tanto la aplicación Android como la API web usan Azure Active Directory B2C para administrar identidades de usuario y autenticar usuarios."
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
	ms.date="02/17/2016"
	ms.author="brandwe"/>

# Versión preliminar de Azure AD B2C: llamada a una API web desde una aplicación Android

Con Azure Active Directory (Azure AD) B2C es posible agregar eficaces características de administración de identidades de autoservicio tanto a aplicaciones Android como a API web en pocos pasos. Este artículo le mostrará cómo crear una aplicación Android de "lista de tareas pendientes" que llama a una API web de Node.jscon tokens de portador de OAuth 2.0. Tanto la aplicación Android como la API web usan Azure AD B2C para administrar identidades de usuario y autenticar usuarios.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

Esta guía de inicio rápido requiere tener una API web protegida mediante Azure AD B2C para funcionar totalmente. Hemos creado una para que la pueda usar con .Net y con Node.js. En este tutorial se supone que se ha configurado el ejemplo de API web de Node.js. Para más información, consulte el tutorial [Versión preliminar de B2C: protección de una API web mediante Node.js](active-directory-b2c-devquickstarts-api-node.md).

Para los clientes de Android que necesitan tener acceso a recursos protegidos, Azure AD proporciona la Biblioteca de autenticación de Active Directory (ADAL). El único propósito de ADAL es facilitar a su aplicación la obtención de tokens de acceso. Para demostrar lo fácil que es, en esta guía crearemos una aplicación Android de lista de tareas pendientes que permita realizar lo siguiente:

- Obtener tokens de acceso para llamar a la API de lista de tareas pendientes utilizando el [protocolo de autenticación OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx).
- Obtener listas de tareas pendientes de los usuarios.
- Desconectar a los usuarios.

> [AZURE.NOTE] Este artículo no trata sobre cómo implementar el inicio de sesión, el registro y la administración de perfiles con Azure AD B2C. Se centra en cómo realizar llamadas a las API web una vez autenticado el usuario. Si aún no lo ha hecho, debe comenzar por el [tutorial de introducción a las aplicaciones web .NET](active-directory-b2c-devquickstarts-web-dotnet.md) para aprender los conceptos básicos de Azure AD B2C.

## Obtener un directorio de Azure AD B2C

Para poder usar Azure AD B2C, debe crear un directorio o inquilino. Un directorio es un contenedor para todos los usuarios, las aplicaciones, los grupos, etc. Si no tiene ninguno, [cree un directorio B2C](active-directory-b2c-get-started.md) antes de continuar con esta guía.

## Creación de una aplicación

A continuación, debe crear una aplicación en su directorio B2C. Esto proporciona a Azure AD la información que necesita para comunicarse de forma segura con la aplicación. Tanto la aplicación como la API web se representan mediante un único **identificador de aplicación** en este caso, ya que conforman una aplicación lógica. Para crear una aplicación, siga [estas instrucciones](active-directory-b2c-app-registration.md). Asegúrese de:

- Incluir una **aplicación web**o una **API web** en la aplicación.
- Escribir `urn:ietf:wg:oauth:2.0:oob` como **URL de respuesta**. Es la dirección URL predeterminada para este ejemplo de código.
- Crear un **secreto de aplicación** para la aplicación y copiarlo. Lo necesitará más adelante. Tenga en cuenta que para poder usar este valor, es preciso [incluirlo entre secuencias de escape XML](https://www.w3.org/TR/2006/REC-xml11-20060816/#dt-escape).
- Copiar el **identificador de aplicación** asignado a la aplicación. También lo necesitará más adelante.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## Crear sus directivas

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

En Azure AD B2C, cada experiencia de usuario se define mediante una [directiva](active-directory-b2c-reference-policies.md). Esta aplicación contiene tres experiencias de identidad: registro, inicio de sesión e inicio de sesión mediante Facebook. Es necesario crear una directiva de cada tipo, como se describe en el [artículo de referencia de las directivas](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Cuando cree las tres directivas, asegúrese de:

- Elegir el **Nombre para mostrar** y los restantes atributos de registro en la directiva de registro.
- Elegir las notificaciones de aplicación **Nombre para mostrar** e **Id. de objeto** en todas las directivas. Puede elegir también otras notificaciones.
- Copiar el **Nombre** de cada directiva después de crearla. Debe tener el prefijo `b2c_1_`. Necesitará estos nombres de directiva más adelante.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Después de crear las tres directivas, está listo para compilar la aplicación.

Tenga en cuenta que este artículo no trata de cómo usar las directivas que acaba de crear. Para aprender acerca del funcionamiento de las directivas en Azure AD B2C, comience con el [tutorial de introducción a las aplicaciones web .NET](active-directory-b2c-devquickstarts-web-dotnet.md).

## Descargar el código

El código de este tutorial [se mantiene en GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android). Para compilar el ejemplo a medida que avance, puede [descargar un proyecto de esqueleto como un archivo .zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/skeleton.zip). También puede clonar el esqueleto:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-Android.git
```

> [AZURE.NOTE] **Para completar este tutorial, es preciso descargar el esqueleto.** Dada la complejidad de la implementación de una aplicación Android totalmente funcional, el esqueleto tiene el código de UX que se ejecutará una vez completado este tutorial. Se trata de una medida de ahorro de tiempo para los desarrolladores. El código de UX no es relevante para el tema de cómo agregar B2C a una aplicación Android.

La aplicación completada también está [disponible como archivo .zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/complete.zip) o en la rama `complete` del mismo repositorio.

Para compilar con Maven, puede utilizar `pom.xml` en el nivel superior.

  1. Siga los pasos descritos en la [sección de requisitos previos para configurar Maven para Android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android/wiki/Setting-up-maven-environment-for-Android).
  2. Configure el emulador con SDK 21.
  3. Vaya a la carpeta raíz donde ha clonado el repositorio.
  4. Ejecute el comando `mvn clean install`.
  5. Cambie el directorio al ejemplo de inicio rápido `cd samples\hello`.
  6. Ejecute el comando `mvn android:deploy android:run`.

La aplicación debería iniciarse. Escriba las credenciales del usuario de prueba para probarlas.

Los paquetes de archivos de almacenamiento Java (JAR) también se enviarán junto con el paquete de archivos de almacenamiento Android (AAR).

## Descarga de ADAL de Android y su incorporación al área de trabajo de Android Studio

Tiene opciones sobre cómo usar esta biblioteca en el proyecto de Android:

* Puede usar el código fuente para importar esta biblioteca a Eclipse y vincularla a la aplicación.
* Si utiliza Android Studio, puede usar el formato de paquete AAR y hacer referencia a los archivos binarios.

### Opción 1: binarios a través de Gradle (recomendado)

Puede obtener los archivos binarios desde el repositorio central de Maven. El paquete AAR puede incluirse en el proyecto en Android Studio (por ejemplo, en `build.gradle`) de esta forma:

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

### Opción 2: AAR mediante Maven

Si usa el complemento `m2e` en Eclipse, puede especificar la dependencia en el archivo `pom.xml`:

```xml
<dependency>
    <groupId>com.microsoft.aad</groupId>
    <artifactId>adal</artifactId>
    <version>2.0.1-alpha</version>
    <type>aar</type>
</dependency>
```

### Opción 3: código fuente a través de Git (último recurso)

Para obtener el código fuente del SDK mediante Git, escriba:

    git clone git@github.com:AzureAD/azure-activedirectory-library-for-android.git
    cd ./azure-activedirectory-library-for-android/src

Utilice la **convergencia** de ramas.

## Configuración del archivo de configuración

Use la configuración que ha establecido antes en el portal de B2C para configurar el proyecto de Android.

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
- `SCOPES`: los ámbitos que pasa al servidor y que desea solicitar del servidor cuando un usuario inicie sesión. En el caso de la versión preliminar de B2C, pasa `client_id`. Sin embargo, se espera que esto cambie a `read scopes` en el futuro. Este documento se actualizará cuando esto ocurra.
- `ADDITIONAL_SCOPES`: los ámbitos adicionales que puede usar para la aplicación. Se espera que se usen en el futuro.
- `CLIENT_ID`: el identificador de la aplicación que obtuvo del portal.
- `REDIRECT_URL`: la redirección donde se espera que se envíe de nuevo el token.
- `EXTRA_QP`: cualquier elemento adicional que desea pasar al servidor en formato codificado de dirección URL.
- `FB_POLICY`: la directiva que está invocando. Esta es la parte más importante del tutorial.
- `EMAIL_SIGNIN_POLICY`: la directiva que está invocando. Esta es la parte más importante del tutorial.
- `EMAIL_SIGNUP_POLICY`: la directiva que está invocando. Esta es la parte más importante del tutorial.

## Incorporación de referencias a ADAL de Android en el proyecto

> [AZURE.NOTE]	ADAL para Android usa un modelo basado en intentos para invocar la autenticación. Los intentos dependen de la aplicación para realizar el trabajo. Este ejemplo completo, y todos los ADAL para Android, se centran en cómo administrar los intentos y pasar información entre ellos.

En primer lugar, indique a Android el diseño de la aplicación, incluidos los intentos que desea utilizar. Estos intentos se explican detalladamente más adelante en este tutorial.

Actualice el archivo `AndroidManifest.xml` del proyecto para que incluya todos los intentos:

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

Como puede ver, se definen cinco actividades. Las utilizará todas.

- `AuthenticationActivity`: procede de ADAL y proporciona la vista web de inicio de sesión.
- `LoginActivity`: muestra las directivas de inicio de sesión y los botones para cada directiva.
- `SettingsActivity`: se usa para cambiar la configuración de la aplicación en tiempo de ejecución.
- `AddTaskActivity`: se usa para agregar tareas a la API de REST que están protegidas mediante Azure AD.
- `ToDoActivity`: la actividad principal que muestra las tareas.

## Creación de la actividad de inicio de sesión

Cree una actividad principal y llámela `LoginActivity`.

Cree un archivo llamado `LoginActivity.java`.

Necesita inicializar la actividad y agregar algunos botones que controlen la interfaz de usuario. Esto ya le resultará familiar si ha escrito código de Android antes.

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
Ahora ha creado los botones que llaman a su intento `ToDoActivity` (que llamará a ADAL cuando necesite un token). Para ello, usan la actividad como una referencia y un parámetro adicional. El método `intent.putExtra()` pasa este parámetro adicional. Defina `"thePolicy"` con lo que especificó en `Constants.java`. Esto indica al intento qué directiva debe invocar durante la autenticación.

## Creación de la actividad de configuración

Se trata de una actividad que rellena la interfaz de usuario de configuración.

Cree un archivo llamado `SettingsActivity.java` para operaciones crear, leer, actualizar y eliminar (CRUD) sencillas.

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

## Creación de la actividad para agregar tareas

Puede usar esta actividad para agregar una tarea en el punto de conexión de la API de REST.

Cree un archivo llamado `AddTaskActivity.java` y escriba lo siguiente.

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

## Creación de la actividad de lista de tareas pendientes

Se trata de la actividad más importante. Puede utilizarla para obtener un token de Azure AD para una directiva y, a continuación, usar ese token para llamar al servidor de la API de REST de tarea.

Cree un archivo llamado `ToDoActivity.java` y escriba lo siguiente. (Las llamadas se explicarán más adelante).

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


 Puede que haya observado que esto se basa en métodos que aún no se han escrito. Incluyen `updateLoggedInUser()`, `clearSessionCookie()` y `getTasks()`. Los escribirá más adelante en esta guía. Puede omitir los errores sin problemas por ahora en Android Studio.

Explicación de los parámetros:

  - `SCOPES`: obligatorio; los ámbitos para los que se está intentando solicitar acceso. Para la versión preliminar de B2C, es lo mismo que `client_id`, pero está previsto que cambie en el futuro.
  - `POLICY`: la directiva para cuando desee autenticar al usuario.
  - `CLIENT_ID`: obligatorio; procede del portal de Azure AD.
  - `redirectUri`: se puede configurar como el nombre del paquete. No es necesario proporcionarlo para la llamada a `acquireToken`.
  - `getUserInfo()`: la forma de buscar si un usuario ya está en la memoria caché. El parámetro también describe cómo solicitar un usuario si no se encuentra o si su token de acceso no es válido. Este método se escribirá más adelante en esta guía.
  - `PromptBehavior.always`: ayuda a pedir las credenciales para omitir la memoria caché y la cookie.
  - `Callback`: se le llama después de intercambiar un código de autorización para un token. Tendrá un objeto `AuthenticationResult`, que contiene el token de acceso, la fecha de caducidad y la información del token de identificador.

> [AZURE.NOTE]	La aplicación del portal de empresa de Microsoft Intune proporciona el componente de agente y puede instalarse en el dispositivo del usuario. La aplicación proporciona acceso de inicio de sesión único (SSO) en todas las aplicaciones del dispositivo. Los desarrolladores deben estar preparados para permitir Intune. ADAL para Android usará la cuenta del agente si hay una cuenta de usuario creada en el autenticador. Para usar el agente, el desarrollador necesita registrar un elemento `redirectUri` especial para que lo use el agente. `redirectUri` tiene el formato msauth://packagename/Base64UrlencodedSignature. Puede obtener `redirectUri` para la aplicación mediante el script `brokerRedirectPrint.ps1` o mediante la llamada a la API `mContext.getBrokerRedirectUri()`. La firma está relacionada con los certificados de firma procedentes de Google Play Store.

 Puede omitir el usuario del agente mediante:

    ```java
     AuthenticationSettings.Instance.setSkipBroker(true);
    ```
> [AZURE.NOTE] Con el fin de reducir la complejidad de esta guía de inicio rápido de B2C, hemos optado por omitir el agente en nuestro ejemplo.

A continuación, cree algunos métodos auxiliares que obtendrán el token solo durante las llamadas de autenticación a la API de tarea.

En el mismo archivo `ToDoActivity.java`, escriba lo siguiente.

```
    private void getToken(final AuthenticationCallback callback) {

        String policy = getIntent().getStringExtra("thePolicy");

        // one of the acquireToken overloads
        mAuthContext.acquireToken(ToDoActivity.this, Constants.SCOPES, Constants.ADDITIONAL_SCOPES,
                policy, Constants.CLIENT_ID, Constants.REDIRECT_URL, getUserInfo(),
                PromptBehavior.Always, "nux=1&" + Constants.EXTRA_QP, callback);
    }
```

Agregue también los métodos para establecer ("set") y obtener ("get") `AuthenticationResult` (que tiene el token) al elemento `Constants` global. Aunque `ToDoActivity.java` utiliza `sResult` en sus flujos, debe agregar estos métodos. Si no lo hace, sus otras actividades no tendrán acceso al token para hacer el trabajo (por ejemplo, agregar una tarea en `AddTaskActivity.java`).

```

    private AuthenticationResult getLocalToken() {
        return Constants.CURRENT_RESULT;
    }

    private void setLocalToken(AuthenticationResult newToken) {


        Constants.CURRENT_RESULT = newToken;
    }


```
## Creación de un método para devolver un identificador de usuario

ADAL para Android representa el usuario en forma de un objeto `UserIdentifier`. Esto administra el usuario. Puede utilizar el objeto para identificar si el mismo usuario se usa en las llamadas. Con esta información, puede confiar en la memoria caché, en lugar de realizar una nueva llamada al servidor. Para facilitar esta tarea, creamos el método `getUserInfo()`, que devuelve `UserIdentifier`. Puede utilizarlo con `acquireToken()`. También creamos un método `getUniqueId()`, que devuelve el identificador de `UserIdentifier` en la memoria caché.

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

### Métodos auxiliares del escritor

A continuación, escriba algunos métodos auxiliares para ayudar a borrar cookies y proporcionar `AuthenticationCallback`. Estos métodos se usan únicamente para el ejemplo, para asegurarse de que se encuentra en un estado limpio al llamar a la actividad `ToDo`.

En el mismo archivo, llamado `ToDoActivity.java`, escriba lo siguiente.

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

## Llamada a la API de tarea

Una vez que su actividad esté lista para tomar los tokens, puede escribir su API para acceder al servidor de tareas.

`getTasks` proporciona una matriz que representa las tareas en el servidor.

Comience con `getTasks`.

En el mismo archivo, llamado `ToDoActivity.java`, escriba lo siguiente.

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

Escriba también un método que inicializará las tablas en la primera ejecución.

En el mismo archivo, llamado `ToDoActivity.java`, escriba lo siguiente.

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

Verá que este código requiere algunos métodos adicionales para que funcione. Escríbalos a continuación.

### Creación de un generador de direcciones URL del punto de conexión

Es necesario generar la dirección URL del punto de conexión a la que se va a conectar. Hágalo en el mismo archivo de clase.

**En el mismo archivo**, llamado `ToDoActivity.java`, escriba lo siguiente.

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


Tenga en cuenta que el token de acceso se agrega a la solicitud en el código tratado en la sección siguiente.

## Escritura de algunos métodos de la experiencia de usuario

Android requiere controlar algunas devoluciones de llamada para que funcione la aplicación. Son `createAndShowDialog` y `onResume()`. Esto ya le resultará familiar si ha escrito código de Android antes.

En el mismo archivo, llamado `ToDoActivity.java`, escriba lo siguiente.

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

A continuación, administre las devoluciones de llamada del cuadro de diálogo.

En el mismo archivo, llamado `ToDoActivity.java`, escriba lo siguiente.

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

Ahora debe tener un archivo `ToDoActivity.java` que se compila. Todo el proyecto también debe compilarse en este punto.

## Ejecutar la aplicación de ejemplo

Por último, cree y ejecute la aplicación en Android Studio o en Eclipse. Regístrese o inicie sesión en la aplicación. Cree tareas para el usuario que inició sesión. Cierre la sesión y vuelva a iniciarla como otro usuario y, a continuación, cree las tareas de dicho usuario.

Observe que las tareas se almacenan por usuario en la API, ya que la API extrae la identidad del usuario del token de acceso que recibe.

Como referencia, el ejemplo completo [se proporciona como un archivo .zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/complete.zip). También puede clonarlo desde GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-Android```


## Información importante


### Cifrado

ADAL cifra los tokens y los almacena en `SharedPreferences` de forma predeterminada. Puede consultar la clase `StorageHelper` para ver los detalles. Android introdujo el almacenamiento seguro de claves privadas **AndroidKeyStore para 4.3(API18)**. ADAL lo utiliza para API18 y las versiones posteriores. Si desea usar ADAL para las versiones inferiores del SDK, deberá proporcionar la clave secreta en `AuthenticationSettings.INSTANCE.setSecretKey`.

### Cookies de sesión en la vista web

La vista web de Android no elimina las cookies de sesión después de cerrar la aplicación. Puede controlarlo con el siguiente código de ejemplo.
```
CookieSyncManager.createInstance(getApplicationContext());
CookieManager cookieManager = CookieManager.getInstance();
cookieManager.removeSessionCookie();
CookieSyncManager.getInstance().sync();
```
[Aprenda más acerca de las cookies](http://developer.android.com/reference/android/webkit/CookieSyncManager.html).

<!---HONumber=AcomDC_0302_2016-->