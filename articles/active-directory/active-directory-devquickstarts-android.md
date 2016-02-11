<properties
	pageTitle="Introducción a Azure AD Android | Microsoft Azure"
	description="Cómo crear una aplicación Android que se integra con Azure AD para el inicio de sesión y llama a las API protegidas de Azure AD mediante OAuth."
	services="active-directory"
	documentationCenter="android"
	authors="brandwe"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="brandwe"/>

# Integración de Azure AD en una aplicación Android

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Si está desarrollando una aplicación de escritorio, Azure AD le facilita la autenticación de sus usuarios mediante sus cuentas de Active Directory. También permite a la aplicación consumir con seguridad cualquier API web protegida por Azure AD, como las API de Office 365 o la API de Azure.

Para los clientes de Android que necesitan tener acceso a recursos protegidos, Azure AD proporciona la Biblioteca de autenticación de Active Directory (ADAL). El único propósito de ADAL es facilitar a su aplicación la obtención de tokens de acceso. Para demostrar lo fácil que es, crearemos aquí una aplicación de lista de tareas pendientes de Android que permita realizar las siguientes acciones:

-	Obtener tokens de acceso para llamar a la API de lista de tareas pendientes utilizando el [protocolo de autenticación OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx)
-	Obtener listas de tareas pendientes de un usuario
-	Cerrar la sesión de los usuarios

Para comenzar, necesitará a un inquilino de Azure AD en el que pueda crear usuarios y registrar una aplicación. Si aún no tiene un inquilino, [descubra cómo conseguir uno](active-directory-howto-tenant.md).

## Paso 1: descargue y ejecute el servidor de ejemplos TODO de la API de REST de Node.js

Este ejemplo está escrito específicamente para que funcione con nuestro ejemplo existente para crear una API de REST To-Do de un solo inquilino para Microsoft Azure Active Directory. Se trata de un requisito previo para el Inicio rápido.

Para obtener información acerca de cómo configurar esta opción, visite nuestros ejemplos existentes aquí:

* [Servicio de API de REST de ejemplo de Microsoft Azure Active Directory para Node.js](active-directory-devquickstarts-webapi-nodejs.md)

## Paso 2: registre la API web con el inquilino de Microsoft Azure AD

**¿Qué estoy haciendo?**

*Microsoft Active Directory permite agregar dos tipos de aplicaciones. Las API web que ofrecen servicios a usuarios y aplicaciones (ya sea en la Web o una aplicación que se ejecuta en un dispositivo) que tienen acceso a esas API web. En este paso, va a registrar la API web que se ejecuta localmente para probar este ejemplo. Normalmente, esta API web sería un servicio de REST que ofrece la funcionalidad a la que desea que acceda una aplicación. Microsoft Azure Active Directory puede proteger cualquier extremo.*

*Aquí asumimos que va a registrar la API de REST TODO mencionada anteriormente, pero este procedimiento sirve para cualquier API web que desee que Azure Active Directory proteja.*

Pasos para registrar una API web con Microsoft Azure AD

1. Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com).
2. Haga clic en Active Directory en el panel de navegación izquierdo.
3. Haga clic en el inquilino de AD donde desea registrar la aplicación de ejemplo.
4. Haga clic en la pestaña Aplicaciones.
5. En el cajón, haga clic en Agregar.
6. Haga clic en "Agregar una aplicación que mi organización está desarrollando".
7. Escriba un nombre descriptivo para la aplicación, por ejemplo "ServicioListaTodo", seleccione "Aplicación web y/o API web" y haga clic en Siguiente.
8. Para la URL de inicio de sesión, escriba la dirección URL base para el ejemplo, que es de forma predeterminada `https://localhost:8080`.
9. Para el URI de identificación de la aplicación, escriba `https://<your_tenant_name>/TodoListService` y sustituya `<your_tenant_name>` por el nombre de su inquilino de Azure AD. Haga clic en Aceptar para completar el registro.
10. Mientras sigue en el portal de Azure, haga clic en la pestaña Configurar de la aplicación.
11. **Busque el valor de Id. de cliente y cópielo aparte**, ya que lo necesitará más tarde para configurar la aplicación.

## Paso 3: registre la aplicación de cliente nativo de Android de ejemplo

El registro de su aplicación web es el primer paso. A continuación, también deberá proporcionar información a Azure Active Directory sobre su aplicación. Esto permite a su aplicación comunicarse con la recién registrada API web.

**¿Qué estoy haciendo?**

*Como se señaló anteriormente, Microsoft Active Directory permite agregar dos tipos de aplicaciones. Las API web que ofrecen servicios a usuarios y aplicaciones (ya sea en la Web o una aplicación que se ejecuta en un dispositivo) que tienen acceso a esas API web. En este paso, va a registrar la aplicación en este ejemplo. Debe hacerlo para que esta aplicación pueda solicitar acceso a la API web que acaba de registrar. Azure Active Directory rechazará incluso que la aplicación pueda solicitar el inicio de sesión, a menos que esté registrada. Eso forma parte de la seguridad del modelo.*

*Aquí asumimos que va a registrar esta aplicación de ejemplo mencionada anteriormente, pero este procedimiento sirve para cualquier aplicación que desarrolle.*

**¿Por qué pongo una aplicación y una API web en un inquilino?**

*Como posiblemente haya intuido, podría crear una aplicación con acceso a una API externa registrada en Azure Active Directory desde otro inquilino. Si lo hace, se pedirá a los clientes que acepten el uso de la API en la aplicación. Lo bueno es que la Biblioteca de autenticación de Active Directory para iOS se encarga de este consentimiento por usted. A medida que nos encontremos con características más avanzadas, verá que se trata de una parte importante del trabajo necesario para tener acceso al conjunto de API de Microsoft desde Azure y Office, así como cualquier otro proveedor de servicios. Por ahora, como ha registrado su API web y la aplicación en el mismo inquilino, no verá las peticiones de consentimiento. Este suele ser el caso si va a desarrollar una aplicación solo para que la use su propia empresa.*

1. Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com).
2. Haga clic en Active Directory en el panel de navegación izquierdo.
3. Haga clic en el inquilino de AD donde desea registrar la aplicación de ejemplo.
4. Haga clic en la pestaña Aplicaciones.
5. En el cajón, haga clic en Agregar.
6. Haga clic en "Agregar una aplicación que mi organización está desarrollando".
7. Escriba un nombre descriptivo para la aplicación, por ejemplo "ServicioListaTodo-Android", seleccione "Aplicación de cliente nativo" y haga clic en Siguiente.
8. Para el URI de redirección, escriba `http://TodoListClient`. Haga clic en Finalizar.
9. Haga clic en la pestaña Configurar de la aplicación.
10. Busque el valor de identificador de cliente y cópielo aparte, lo necesitará más tarde al configurar la aplicación.
11. En "Permisos para otras aplicaciones", haga clic en "Agregar aplicación". Seleccione "Otras" en la lista desplegable "Mostrar" y haga clic en la marca de comprobación superior. Busque ServicioListaTodo y haga clic en él. Haga clic también en la marca de comprobación inferior para agregar la aplicación. Seleccione "Acceso a ServicioListaTodo" en la lista desplegable "Permisos delegados" y guarde la configuración.



Para compilar con Maven, puede utilizar pom.xml en el nivel superior.

  * Clone este repositorio en el directorio que desee:

  `$ git clone git@github.com:AzureADSamples/NativeClient-Android.git`

  * Siga los pasos descritos en la sección de [requisitos previos](https://github.com/MSOpenTech/azure-activedirectory-library-for-android/wiki/Setting-up-maven-environment-for-Android) para configurar Maven para Android.
  * Configure el emulador con SDK 19.
  * Vaya a la carpeta raíz donde ha clonado el repositorio.
  * Ejecute el comando: mvn clean install.
  * Acceda al directorio de la muestra de inicio rápido: cd samples\hello.
  * Ejecute el comando: mvn android:deploy android:run.
  * La aplicación debería iniciarse.
  * Escriba las credenciales del usuario de prueba para probarlas.

Los paquetes Jar se enviarán también junto con el paquete aar.

### Paso 4: descargue ADAL de Android y agréguelo al área de trabajo de Eclipse

Ahora tiene varias opciones para usar esta biblioteca en el proyecto Android:

* Puede usar el código fuente para importar esta biblioteca en Eclipse y vincularla a la aplicación.
* Si utiliza Studio Android, puede usar el formato de paquete *aar* y hacer referencia a los archivos binarios.

####Opción 1: origen a través de ZIP

Para descargar una copia del código fuente, haga clic en "Descargar ZIP" en el lado derecho de la página o haga clic [aquí](https://github.com/AzureAD/azure-activedirectory-library-for-android/archive/v1.0.9.tar.gz).

####Opción 2: origen a través de Git

Para obtener el código fuente del SDK a través de Git, simplemente escriba:

    git clone git@github.com:AzureAD/azure-activedirectory-library-for-android.git
    cd ./azure-activedirectory-library-for-android/src

####Opción 3: binarios a través de Gradle

Puede obtener los archivos binarios desde el repositorio central de Maven. El paquete AAR puede incluirse de la siguiente forma en el proyecto en AndroidStudio:

```gradle
repositories {
    mavenCentral()
    flatDir {
        dirs 'libs'
    }
    maven {
        url "YourLocalMavenRepoPath\.m2\repository"
    }
}
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile('com.microsoft.aad:adal:1.1.1') {
        exclude group: 'com.android.support'
    } // Recent version is 1.1.1
}
```

####Opción 4: aar a través de Maven

Si utiliza el complemento m2e en Eclipse, puede especificar la dependencia en el archivo pom.xml:

```xml
<dependency>
    <groupId>com.microsoft.aad</groupId>
    <artifactId>adal</artifactId>
    <version>1.1.1</version>
    <type>aar</type>
</dependency>
```


####Opción 5: paquete jar dentro de la carpeta libs
Puede obtener el archivo jar desde el repositorio de Maven y colocarlo en la carpeta *libs* del proyecto. También deberá copiar los recursos necesarios en el proyecto, ya que los paquetes jar no los incluyen.


### Paso 5: agregue referencias a ADAL de Android en el proyecto


2. Agregue una referencia al proyecto y especifíquela como una biblioteca de Android. Si no sabe cómo hacerlo, [haga clic aquí para obtener más información](http://developer.android.com/tools/projects/projects-eclipse.html).

3. Agregue la dependencia de proyecto para la depuración en la configuración del proyecto.

4. Actualice el archivo del proyecto AndroidManifest.xml para incluir:

    ```Java
      <uses-permission android:name="android.permission.INTERNET" />
      <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
      <application
            android:allowBackup="true"
            android:debuggable="true"
            android:icon="@drawable/ic_launcher"
            android:label="@string/app_name"
            android:theme="@style/AppTheme" >

            <activity
                android:name="com.microsoft.aad.adal.AuthenticationActivity"
                android:label="@string/title_login_hello_app" >
            </activity>
      ....
      <application/>
    ```

7. Cree una instancia de AuthenticationContext en la actividad principal. Los detalles de esta llamada quedan fuera del ámbito de este archivo Léame, pero puede empezar a trabajar examinando el [ejemplo del cliente nativo de Android](https://github.com/AzureADSamples/NativeClient-Android). Aquí tiene un ejemplo:

    ```Java
    // Authority is in the form of https://login.windows.net/yourtenant.onmicrosoft.com
    mContext = new AuthenticationContext(MainActivity.this, authority, true); // This will use SharedPreferences as            default cache
    ```
  * NOTA: mContext es un campo de su actividad.

8. Copie este bloque de código para controlar el final de AuthenticationActivity después de que el usuario escriba las credenciales y reciba el código de autorización:

    ```Java
     @Override
     protected void onActivityResult(int requestCode, int resultCode, Intent data) {
         super.onActivityResult(requestCode, resultCode, data);
         if (mContext != null) {
             mContext.onActivityResult(requestCode, resultCode, data);
         }
     }
    ```

9. Para solicitar un token, hay que definir una devolución de llamada.

    ```Java
    private AuthenticationCallback<AuthenticationResult> callback = new AuthenticationCallback<AuthenticationResult>() {

            @Override
            public void onError(Exception exc) {
                if (exc instanceof AuthenticationException) {
                    textViewStatus.setText("Cancelled");
                    Log.d(TAG, "Cancelled");
                } else {
                    textViewStatus.setText("Authentication error:" + exc.getMessage());
                    Log.d(TAG, "Authentication error:" + exc.getMessage());
                }
            }

            @Override
            public void onSuccess(AuthenticationResult result) {
                mResult = result;

                if (result == null || result.getAccessToken() == null
                        || result.getAccessToken().isEmpty()) {
                    textViewStatus.setText("Token is empty");
                    Log.d(TAG, "Token is empty");
                } else {
                    // request is successful
                    Log.d(TAG, "Status:" + result.getStatus() + " Expired:"
                            + result.getExpiresOn().toString());
                    textViewStatus.setText(PASSED);
                }
            }
        };
    ```
10. Por último, solicite un token mediante esa devolución de llamada:

    ```Java
     mContext.acquireToken(MainActivity.this, resource, clientId, redirect, user_loginhint, PromptBehavior.Auto, "",
                    callback);
    ```

Explicación de los parámetros:

  * Hay que especificar el recurso, que es al que está intentando acceder.
  * El identificador del cliente es necesario. Su valor procede del portal de AzureAD.
  * Puede configurar redirectUri como el nombre del paquete. No es necesario que se proporcione la llamada a acquireToken.
  * PromptBehavior ayuda a pedir las credenciales para omitir la memoria caché y la cookie.
  * La devolución de llamada se realizará una vez que se intercambie el código de autorización para un token.

  La devolución de llamada tendrá un objeto de AuthenticationResult que tiene accesstoken, fecha de caducidad e información sobre el token del identificador.

Opcional: **acquireTokenSilent**

Puede llamar a **acquireTokenSilent** para controlar el almacenamiento en caché y la actualización del token. Proporciona también la versión de sincronización. Acepta userid como parámetro.

    ```java
     mContext.acquireTokenSilent(resource, clientid, userId, callback );
    ```

11. **Agente**:
 la aplicación de portal de empresa de Microsoft Intune proporcionará el componente del agente. ADAL usará la cuenta del agente (si hay una cuenta de usuario que se ha creado en este autenticador y el desarrollador decide no omitirla). El desarrollador puede omitir el usuario del agente con:

    ```java
     AuthenticationSettings.Instance.setSkipBroker(true);
    ```

 El desarrollador necesita registrar un redirectUri especial para el uso del agente. RedirectUri tiene el formato msauth://packagename/Base64UrlencodedSignature. Puede obtener un redirecturi para su aplicación mediante el script "brokerRedirectPrint.ps1". También puede usar la llamada de API mContext.getBrokerRedirectUri. La firma está relacionada con los certificados de firma.

 El modelo de agente actual es para un usuario. AuthenticationContext proporciona un método de API para obtener el usuario del agente.

 ```java
 String brokerAccount =  mContext.getBrokerUser();
 ```
 Si la cuenta es válida, se devolverá el usuario del agente.

 El manifiesto de aplicación debe tener permisos para usar cuentas del administrador de cuentas: http://developer.android.com/reference/android/accounts/AccountManager.html

 * GET_ACCOUNTS
 * USE_CREDENTIALS
 * MANAGE_ACCOUNTS


Con procedimiento, debe contar con todo lo necesario para lograr una integración correcta con Azure Active Directory. Para obtener más ejemplos de este trabajo, visite el repositorio AzureADSamples/ en GitHub.

## Información importante

### Personalización

Los recursos del proyecto de la biblioteca pueden sobrescribirse con los recursos de la aplicación. Esto sucede cuando la aplicación se está generando. Por este motivo, puede personalizar el diseño de la actividad de autenticación del modo que desee. Deberá asegurarse de mantener el identificador de los controles que use ADAL (WebView).

### Agente

El componente del agente se entregará con la aplicación del portal de empresa de Microsoft Intune. La cuenta se creará en el administrador de cuentas. El tipo de cuenta es "com.microsoft.workaccount". Solo se permiten cuentas SSO. Después de completar el desafío del dispositivo para una de las aplicaciones, se creará la cookie de SSO para este usuario.

### ADFS y URL de la autoridad

ADFS no se reconoce como STS de producción, por lo que debe desactivar el descubrimiento de la instancia y pasar el valor "false" al constructor de AuthenticationContext.

La dirección URL de la autoridad necesita la instancia de STS y el nombre del inquilino: https://login.windows.net/yourtenant.onmicrosoft.com

### Consulta de los elementos en caché

ADAL proporciona memoria caché predeterminada en SharedPrefrecens con algunas características sencillas para hacer consultas sobre los elementos en caché. Puede obtener la memoria caché actual de AuthenticationContext con: 
```Java
 ITokenCacheStore cache = mContext.getCache();
```
También puede proporcionar la implementación de la memoria caché, si desea personalizarla. 
```Java
mContext = new AuthenticationContext(MainActivity.this, authority, true, yourCache);
```

### PromptBehavior

ADAL permite especificar el comportamiento de la petición. PromptBehavior.Auto hará que se muestre la interfaz de usuario si el token de actualización no es válido y se necesitan credenciales de usuario. PromptBehavior.Always omitirá el uso de la memoria caché y mostrará siempre la interfaz de usuario.

### Solicitud de token silenciosa desde la memoria caché y actualización

Este método no utiliza mensajes emergentes en la interfaz de usuario y no requiere ninguna actividad. Se devolverá un token desde la memoria caché si está disponible. Si el token ha caducado, intentará actualizarlo. Si el token de actualización está caducado o falla por cualquier motivo, devolverá AuthenticationException.

    ```Java
    Future<AuthenticationResult> result = mContext.acquireTokenSilent(resource, clientid, userId, callback );
    ```

También puede hacer una llamada de sincronización con este método. Puede establecer null para la devolución de llamada o usar acquireTokenSilentSync.

### Diagnóstico

Estas son las principales fuentes de información para diagnosticar problemas:

+ Excepciones
+ Registros
+ Seguimientos de red

Además, tenga en cuenta que los identificadores de correlación son fundamentales para el diagnóstico en la biblioteca. Puede establecer los identificadores de correlación sujetos a solicitud si desea establecer una correlación de ADAL con otras operaciones en el código. Si no establece un identificador de correlaciones, ADAL generará uno aleatorio y todos los mensajes de registro y llamadas de red se marcarán con el identificador de correlación. El identificador generado automáticamente se modifica con cada solicitud.

#### Excepciones

Esto es, obviamente, el primer diagnóstico. Intentamos ofrecerle mensajes de error útiles. Si encuentra alguno que no sea útil, genere un caso y háganoslo saber. Proporcione también información sobre el dispositivo, por ejemplo, el modelo y el número de SDK.

#### Registros

Puede configurar la biblioteca para que genere mensajes de registro que podrá utilizar para diagnosticar problemas. Configure el registro mediante la siguiente llamada para configurar una devolución de llamada que ADAL usará para entregar los mensajes de registro a medida que se generen.


 ```Java
 Logger.getInstance().setExternalLogger(new ILogger() {
     @Override
     public void Log(String tag, String message, String additionalMessage, LogLevel level, ADALError errorCode) {
      ...
      // You can write this to logfile depending on level or errorcode.
      writeToLogFile(getApplicationContext(), tag +":" + message + "-" + additionalMessage);
     }
 }
 ```
Los mensajes pueden escribirse en un archivo de registro personalizado, tal como se muestra a continuación. Por desgracia, no hay ninguna manera estándar de obtener los registros de un dispositivo. Hay algunos servicios que pueden ayudar con esta tarea. También puede "inventar" sus propios métodos, como enviar el archivo a un servidor.

```Java
private syncronized void writeToLogFile(Context ctx, String msg) {
       File directory = ctx.getDir(ctx.getPackageName(), Context.MODE_PRIVATE);
       File logFile = new File(directory, "logfile");
       FileOutputStream outputStream = new FileOutputStream(logFile, true);
       OutputStreamWriter osw = new OutputStreamWriter(outputStream);
       osw.write(msg);
       osw.flush();
       osw.close();
}
```

##### Niveles de registro

+ Error (excepciones)
+ Warn (advertencia)
+ Info (información)
+ Verbose (más detalles)

El nivel de registro se define de la siguiente forma: 
```Java
Logger.getInstance().setLogLevel(Logger.LogLevel.Verbose);
 ```

 Todos los mensajes de registro se envían a logcat, además de las devoluciones de llamada de registro personalizadas. 
 Un registro de logcat se puede obtener en formato de archivo de la siguiente forma:

 ```
  adb logcat > "C:\logmsg\logfile.txt"
 ```
Más ejemplos sobre comandos adb: https://developer.android.com/tools/debugging/debugging-log.html#startingLogcat

#### Seguimiento de red

Puede usar varias herramientas para capturar el tráfico HTTP que genera ADAL. Esto es muy útil si está familiarizado con el protocolo OAuth o si necesita proporcionar información de diagnóstico a Microsoft o a otros canales de soporte técnico.

Fiddler es la herramienta de seguimiento de HTTP más sencilla. Utilice los vínculos siguientes para configurar el modo correcto de registrar el tráfico de red de ADAL. Para que surta efecto, es necesario configurar Fiddler o cualquier otra herramienta, como Charles, para registrar el tráfico SSL sin cifrar. Nota: los seguimientos generados de esta manera pueden contener información altamente privilegiada, como tokens de acceso, nombres de usuario y contraseñas. Si utiliza cuentas de producción, no comparta esta información con terceras partes. Si necesita transmitir el seguimiento a alguien para obtener soporte técnico, reproduzca el problema con una cuenta temporal con nombres de usuario y contraseñas que no le importe compartir.

+ [Configuración de Fiddler para Android](http://docs.telerik.com/fiddler/configure-fiddler/tasks/ConfigureForAndroid)
+ [Configuración de reglas de Fiddler para ADAL](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki/How-to-listen-to-httpUrlConnection-in-Android-app-from-Fiddler)


### Modo de diálogo
El método acquireToken sin actividad es compatible con el modo de cuadro de diálogo.

### Cifrado

ADAL cifra los tokens y los almacena en SharedPreferences de forma predeterminada. Puede consultar la clase StorageHelper para ver los detalles. Android introdujo el almacenamiento seguro de claves privadas AndroidKeyStore 4.3 (API18). ADAL lo utiliza para API18 y las versiones posteriores. Si desea usar ADAL para las versiones inferiores de SDK, deberá proporcionar la clave secreta en AuthenticationSettings.INSTANCE.setSecretKey.

### Desafío de portador de Oauth2

La clase AuthenticationParameters proporciona funcionalidad para obtener el authorization_uri a partir del desafío de portador de Oauth2.

### Cookies de sesión en WebView

Android WebView no elimina las cookies de sesión después de cerrar la aplicación. Se puede controlar con el código de ejemplo siguiente: 
```java
CookieSyncManager.createInstance(getApplicationContext());
CookieManager cookieManager = CookieManager.getInstance();
cookieManager.removeSessionCookie();
CookieSyncManager.getInstance().sync();
```
 Más información sobre las cookies: http://developer.android.com/reference/android/webkit/CookieSyncManager.html

### Reemplazos de recursos

La biblioteca de ADAL incluye cadenas en inglés para los dos mensajes siguientes de ProgressDialog.

La aplicación debe sobrescribirlas si desea ver las cadenas traducidas.

```Java
<string name="app_loading">Loading...</string>
<string name="broker_processing">Broker is processing</string>
<string name="http_auth_dialog_username">Username</string>
<string name="http_auth_dialog_password">Password</string>
<string name="http_auth_dialog_title">Sign In</string>
<string name="http_auth_dialog_login">Login</string>
<string name="http_auth_dialog_cancel">Cancel</string>
```

=======

### Cuadro de diálogo NTLM
La versión 1.1.0 de ADAL admite el cuadro de diálogo NTLM que se procesa a través del evento onReceivedHttpAuthRequest desde WebViewClient. El diseño del cuadro de diálogo y las cadenas se pueden personalizar. ### Paso 5: descargue el código de ejemplo de cliente nativo de iOS

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]

<!---HONumber=AcomDC_0128_2016-->