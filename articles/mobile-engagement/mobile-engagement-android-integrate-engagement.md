<properties
	pageTitle="Integración del SDK de Android para Azure Mobile Engagement"
	description="Procedimientos y actualizaciones más recientes para el SDK de Android para Azure Mobile Engagement"
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="dwrede"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="Java"
	ms.topic="article"
	ms.date="02/29/2016"
	ms.author="piyushjo" />

#Integración de Engagement en Android

> [AZURE.SELECTOR]
- [Windows Universal](mobile-engagement-windows-store-integrate-engagement.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
- [iOS](mobile-engagement-ios-integrate-engagement.md)
- [Android](mobile-engagement-android-integrate-engagement.md)

Este procedimiento describe la manera más fácil de activar las funciones de Análisis y Supervisión de Engagement en su aplicación de Android.

> [AZURE.IMPORTANT] El nivel mínimo de la API del SDK de Android debe ser 10 o superior (Android 2.3.3 o superior).

Los siguientes pasos son suficientes para activar el informe de registros necesarios para calcular todas las estadísticas relativas a Usuarios, Sesiones, Actividades, Bloqueos y Aspectos técnicos. El informe de los registros necesarios para calcular otras estadísticas, como eventos, errores y trabajos debe realizarse manualmente mediante la API de Engagement (vea [Cómo usar la API de etiquetado avanzado de Mobile Engagement en su dispositivo Android](mobile-engagement-android-use-engagement-api.md)) debido a que estas estadísticas dependen de la aplicación.

##Inserción del SDK y el servicio Engagement en el proyecto de Android

Descargue el SDK de Android [aquí](https://aka.ms/vq9mfn) Obtenga `mobile-engagement-VERSION.jar` y colóquelos en la carpeta `libs` del proyecto Android (cree la carpeta libs si aún no existe).

> [AZURE.IMPORTANT]
Si compila su paquete de aplicación con ProGuard, deberá mantener algunas clases. Puede usar el siguiente snippet de configuración:
>
>
			-keep public class * extends android.os.IInterface
			-keep class com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity$EngagementReachContentJS {
			<methods>;
		 	}

Especifique la cadena de conexión de Engagement mediante la llamada al siguiente método en la actividad del iniciador:

			EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
			engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
			EngagementAgent.getInstance(this).init(engagementConfiguration);

La cadena de conexión de la aplicación se muestra en el portal de Azure.

-   Si no está, agregue los siguientes permisos de Android (delante de la etiqueta `<application>`):

			<uses-permission android:name="android.permission.INTERNET"/>
			<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>

-   Agregue la siguiente sección (entre las etiquetas `<application>` y `</application>`):

			<service
			  android:name="com.microsoft.azure.engagement.service.EngagementService"
			  android:exported="false"
			  android:label="<Your application name>Service"
			  android:process=":Engagement"/>

-   Cambie `<Your application name>` por el nombre de la aplicación.

> [AZURE.TIP] El atributo `android:label` le permite elegir el nombre del servicio Engagement que aparecerá para los usuarios finales en la pantalla "Running services" (Servicios en ejecución) de su teléfono. Se recomienda establecer este atributo en `"<Your application name>Service"` (por ejemplo, `"AcmeFunGameService"`).

La especificación del atributo `android:process` garantiza que el servicio Engagement se ejecutará en su propio proceso (al ejecutarse Engagement en el mismo proceso que su aplicación, el subproceso principal y de interfaz de usuario podría tener menos capacidad de respuesta).

> [AZURE.NOTE] Cualquier código que coloque en `Application.onCreate()` y otras devoluciones de llamada de aplicaciones se ejecutará para todos los procesos de la aplicación, incluido el servicio Engagement. Esto puede tener efectos secundarios no deseados (como asignaciones innecesarias de memoria y subprocesos en el proceso de Engagement o receptores o servicios de difusión duplicados).

Si anula `Application.onCreate()`, se recomienda que agregue el siguiente snippet de código al comienzo de su función `Application.onCreate()`:

			 public void onCreate()
			 {
			   if (EngagementAgentUtils.isInDedicatedEngagementProcess(this))
			     return;

			   ... Your code...
			 }

Puede hacer lo mismo para `Application.onTerminate()`, `Application.onLowMemory()` y `Application.onConfigurationChanged(...)`.

También puede ampliar `EngagementApplication` en lugar de `Application`: la devolución de llamada `Application.onCreate()` solo realiza el proceso y llama a `Application.onApplicationProcessCreate()` si el proceso actual no es el que hospeda el servicio Engagement; las mismas reglas se aplican para las demás devoluciones de llamada.

##Informes básicos

### Método recomendado: sobrecargar las clases `Activity`

Para activar el informe de todos los registros que necesita Engagement para calcular las estadísticas de Usuarios, Sesiones, Actividades, Bloqueos y Aspectos técnicos, tiene que hacer que todas las subclases `*Activity` hereden de las clases `Engagement*Activity` correspondientes (por ejemplo, si su actividad heredada amplía `ListActivity`, haga que amplíe `EngagementListActivity`).

**Sin Engagement:**

			package com.company.myapp;

			import android.app.Activity;
			import android.os.Bundle;

			public class MyApp extends Activity
			{
			  @Override
			  public void onCreate(Bundle savedInstanceState)
			  {
			    super.onCreate(savedInstanceState);
			    setContentView(R.layout.main);
			  }
			}

**Con Engagement:**

			package com.company.myapp;

			import com.microsoft.azure.engagement.activity.EngagementActivity;
			import android.os.Bundle;

			public class MyApp extends EngagementActivity
			{
			  @Override
			  public void onCreate(Bundle savedInstanceState)
			  {
			    super.onCreate(savedInstanceState);
			    setContentView(R.layout.main);
			  }
			}

> [AZURE.IMPORTANT] Al usar `EngagementListActivity` o `EngagementExpandableListActivity`, asegúrese de que cualquier llamada a `requestWindowFeature(...);` se realice antes que la llamada a `super.onCreate(...);`, de lo contrario se producirá un bloqueo.

Proporcionamos subclases de `FragmentActivity` y `MapActivity`, sin embargo, para evitar problemas con aplicaciones que usan **ProGuard**, no las incluimos en `engagement.jar`.

Puede encontrar estas clases en la carpeta `src` y puede copiarlas en su proyecto. Las clases también están en **JavaDoc**.

### Método alternativo: llamar a `startActivity()` y `endActivity()` manualmente

Si no puede o no quiere sobrecargar sus clases `Activity`, puede iniciar y finalizar sus actividades llamando a los métodos `EngagementAgent` directamente.

> [AZURE.IMPORTANT] El SDK de Android nunca llama al método `endActivity()`, incluso cuando la aplicación está cerrada (en Android, las aplicaciones nunca se cierran realmente). Por tanto, es *MUY* recomendable llamar al método `startActivity()` en la devolución de llamada `onResume` de *TODAS* las actividades y al método `endActivity()` en la devolución de llamada `onPause()` de *TODAS* las actividades. Esta es la única manera de asegurarse de que las sesiones no se pierdan. Si una sesión se pierde, el servicio Engagement nunca se desconectará del back-end de Engagement (dado que el servicio permanece conectado mientras una sesión esté pendiente).

Aquí tiene un ejemplo:

			public class MyActivity extends Some3rdPartyActivity
			{
			  @Override
			  protected void onResume()
			  {
			    super.onResume();
			    String activityNameOnEngagement = EngagementAgentUtils.buildEngagementActivityName(getClass()); // Uses short class name and removes "Activity" at the end.
			    EngagementAgent.getInstance(this).startActivity(this, activityNameOnEngagement, null);
			  }

			  @Override
			  protected void onPause()
			  {
			    super.onPause();
			    EngagementAgent.getInstance(this).endActivity();
			  }
			}

Este ejemplo es muy parecido a la clase `EngagementActivity` y sus variantes, cuyo código fuente se proporciona en la carpeta `src`.

##Prueba

Ahora, para comprobar la integración, ejecute la aplicación móvil en un dispositivo o emulador y compruebe que registra una sesión en la pestaña Monitor.

Las siguientes secciones son opcionales.

##Informes de ubicación

Si desea que se notifiquen las ubicaciones, deberá agregar algunas líneas de configuración (entre las etiquetas `<application>` y `</application>`).

### Informes de ubicaciones de áreas diferidas

Los informes de ubicación de área diferida permiten notificar el país, la región y la localidad asociados con los dispositivos. Este tipo de informe de ubicación sólo emplea ubicaciones de red (basadas en el identificador del teléfono móvil o en WIFI). El área del dispositivo se notifica como máximo una vez por sesión. El GPS no se utiliza nunca y, por tanto, este tipo de informe de ubicación tiene muy poco impacto (por no decir ninguno) en la batería.

Las áreas notificadas se utilizan para elaborar estadísticas geográficas acerca de los usuarios, las sesiones, los eventos y los errores. También se pueden usar como criterios en campañas de cobertura.

Para habilitar los informes diferidos de ubicación, puede hacerlo mediante la configuración anteriormente mencionada en este procedimiento:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setLazyAreaLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Puede que también deba agregar el siguiente permiso si no existe:

			<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

O bien, puede seguir usando ``ACCESS_FINE_LOCATION`` si ya lo usa en la aplicación.

### Informes de ubicación en tiempo real

Los informes de ubicación en tiempo real permiten notificar la latitud y la longitud asociadas con los dispositivos. De forma predeterminada, este tipo de informes de ubicación solo emplea ubicaciones de red (basadas en el identificador del teléfono móvil o en WIFI), y los informes solo están activos cuando la aplicación se ejecuta en primer plano (por ejemplo, durante una sesión).

Las ubicaciones en tiempo real *NO* se usan para calcular las estadísticas. Su único objetivo es permitir el uso de criterios de geovallas en tiempo real <Reach-audiencia-geovallas> en las campañas de Reach.

Para habilitar los informes de ubicación en tiempo real, puede hacerlo mediante la configuración anteriormente mencionada en este procedimiento:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Puede que también deba agregar el siguiente permiso si no existe:

			<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

O bien, puede seguir usando ``ACCESS_FINE_LOCATION`` si ya lo usa en la aplicación.

#### Informes basados en GPS

De forma predeterminada, los informes de ubicación en tiempo real solo emplean ubicaciones de red. Para habilitar el uso de ubicaciones basadas en GPS (que son mucho más precisas), use el objeto de configuración:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setFineRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Puede que también deba agregar el siguiente permiso si no existe:

			<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>

#### Informes en segundo plano

De forma predeterminada, los informes de ubicación en tiempo real solo están activos cuando la aplicación se ejecuta en primer plano (es decir, durante una sesión). Para habilitar los informes también en segundo plano, use el objeto de configuración:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setBackgroundRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

> [AZURE.NOTE] Cuando la aplicación se ejecuta en segundo plano, solo se notifican las ubicaciones de red, incluso si ha habilitado el GPS.

El informe de ubicación en segundo plano se detendrá si el usuario reinicia su dispositivo; puede agregar esto para hacer que se reinicie automáticamente en el momento de inicio:

			<receiver android:name="com.microsoft.azure.engagement.EngagementLocationBootReceiver"
			   android:exported="false">
			   <intent-filter>
			      <action android:name="android.intent.action.BOOT_COMPLETED" />
			   </intent-filter>
			</receiver>

Puede que también deba agregar el siguiente permiso si no existe:

			<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />

### Permisos de Android M

A partir de Android M, algunos permisos se administran en tiempo de ejecución y se necesita la aprobación del usuario.

Los permisos en tiempo de ejecución se desactivan de manera predeterminada para las nuevas instalaciones de aplicaciones con destino la API de Android nivel 23. De lo contrario, se activan de forma predeterminada.

El usuario puede habilitar o deshabilitar esos permisos en el menú de configuración del dispositivo. Desactivar los permisos en el menú del sistema termina los procesos en segundo plano de la aplicación; se trata de un comportamiento del sistema y no afecta a la capacidad para recibir inserciones en segundo plano.

En el contexto de Mobile Engagement, los permisos que requieren aprobación en tiempo de ejecución son:

- `ACCESS_COARSE_LOCATION`
- `ACCESS_FINE_LOCATION`
- `WRITE_EXTERNAL_STORAGE` (solo cuando el destino es la API de Android nivel 23)

El almacenamiento externo se usa únicamente para la característica de imágenes grandes de alcance. Si observa que pedir este permiso a los usuarios resulta molesto, puede quitarlo si solo lo usa para Mobile Engagement pero a costa de deshabilitar la característica de grandes imágenes.

Para las características de ubicación, debe solicitar permisos al usuario mediante un cuadro de diálogo estándar del sistema. Si el usuario lo aprueba, debe indicar a ``EngagementAgent`` que tenga en cuenta ese cambio en tiempo real (de lo contrario,el cambio se procesará la próxima vez que el usuario inicie la aplicación).

Este es un ejemplo de código para usar en una actividad de la aplicación para solicitar permisos y reenviar el resultado si es positivo a ``EngagementAgent``:

    @Override
    public void onCreate(Bundle savedInstanceState)
    {
      /* Other code... */

      /* Request permissions */
      requestPermissions();
    }

    @TargetApi(Build.VERSION_CODES.M)
    private void requestPermissions()
    {
      /* Avoid crashing if not on Android M */
      if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M)
      {
        /*
         * Request location permission, but this won't explain why it is needed to the user.
         * The standard Android documentation explains with more details how to display a rationale activity to explain the user why the permission is needed in your application.
         * Putting COARSE vs FINE has no impact here, they are part of the same group for runtime permission management.
         */
        if (checkSelfPermission(android.Manifest.permission.ACCESS_FINE_LOCATION) != PackageManager.PERMISSION_GRANTED)
          requestPermissions(new String[] { android.Manifest.permission.ACCESS_FINE_LOCATION }, 0);

        /* Only if you want to keep features using external storage */
        if (checkSelfPermission(android.Manifest.permission.WRITE_EXTERNAL_STORAGE) != PackageManager.PERMISSION_GRANTED)
          requestPermissions(new String[] { android.Manifest.permission.WRITE_EXTERNAL_STORAGE }, 1);
      }
    }

    @Override
    public void onRequestPermissionsResult(int requestCode, String[] permissions, int[] grantResults)
    {
      /* Only a positive location permission update requires engagement agent refresh, hence the request code matching from above function */
      if (requestCode == 0 && grantResults[0] == PackageManager.PERMISSION_GRANTED)
        getEngagementAgent().refreshPermissions();
    }

##Informes avanzados

De manera opcional, si desea notificar eventos, errores y trabajos específicos de la aplicación, deberá usar la API de Engagement a través de los métodos de la clase `EngagementAgent`. Un objeto de esta clase se puede recuperar mediante la llamada al método estático `EngagementAgent.getInstance()`.

La API de Engagement permite usar todas las capacidades avanzadas de Engagement, que se detallan en Cómo usar la API de Engagement en Android (así como en la documentación técnica de la clase `EngagementAgent`).

##Configuración avanzada (en AndroidManifest.xml)

Si desea asegurarse de que las estadísticas se envían en tiempo real cuando se utilice Wifi o cuando la pantalla esté apagada, agregue el siguiente permiso opcional:

			<uses-permission android:name="android.permission.WAKE_LOCK"/>

Si desea deshabilitar los informes de bloqueo, agregue esto (entre las etiquetas `<application>` y `</application>`):

			<meta-data android:name="engagement:reportCrash" android:value="false"/>

De forma predeterminada, el servicio de Engagement informa los registros en tiempo real. Si su aplicación notifica registros con mucha frecuencia, es mejor almacenar en búfer los registros y notificarlos todos a la vez de manera periódica (esto se conoce como "modo de ráfaga"). Para ello, agregue esto (entre las etiquetas `<application>` y `</application>`):

			<meta-data android:name="engagement:burstThreshold" android:value="<interval between too bursts (in milliseconds)>"/>

El modo de ráfaga aumenta ligeramente la duración de la batería, pero afecta al monitor de Engagement: la duración de todas las sesiones y trabajos se redondeará al umbral de ráfaga (por lo tanto, es posible que las sesiones y los trabajos más cortos que el umbral de ráfaga no sean visibles). Se recomienda usar un umbral de ráfaga inferior a 30.000 (30 segundos).

De forma predeterminada, el servicio Engagement establece la conexión con nuestros servidores en cuanto la red está disponible. Si desea aplazar la conexión, agregue esto (entre las etiquetas `<application>` y `</application>`):

			<meta-data android:name="engagement:connection:delay" android:value="<delay (in milliseconds)>"/>

De forma predeterminada, una sesión finaliza a los 10 seg. del final de su última actividad (que normalmente se produce al presionar Inicio o la tecla de retroceso, poniendo el teléfono inactivo o yendo a otra aplicación). Esto se hace para evitar que una sesión se divida cada vez que el usuario sale de la aplicación y vuelve a ella muy rápidamente (lo que puede suceder cuando se selecciona una imagen, se comprueba una notificación, etc.). Puede que desee modificar este parámetro. Para ello, agregue esto (entre las etiquetas `<application>` y `</application>`):

			<meta-data android:name="engagement:sessionTimeout" android:value="<session timeout (in milliseconds)>"/>

##Deshabilitación de los informes de registro

### Mediante una llamada al método

Si desea que Engagement deje de enviar registros, puede efectuar una llamada:

			EngagementAgent.getInstance(context).setEnabled(false);

Esta llamada es persistente: usa un archivo de preferencias compartido.

Si Engagement está activo cuando llama a esta función, puede que el servicio tarde 1 minuto en detenerse. Sin embargo, no se iniciará el servicio la próxima vez que inicie la aplicación.

Puede habilitar de nuevo los informes de registro mediante la llamada a la misma función con `true`.

### Integración en su propia `PreferenceActivity`

En lugar de llamar a esta función, también puede integrar este valor directamente en su `PreferenceActivity` existente.

Puede configurar Engagement para usar su archivo de preferencias (con el modo deseado) en el archivo `AndroidManifest.xml` con `application meta-data`:

-   La clave `engagement:agent:settings:name` se usa para definir el nombre del archivo de preferencias compartido.
-   La clave `engagement:agent:settings:mode` se usa para definir el modo del archivo de preferencias compartido; debe usar el mismo modo que en su `PreferenceActivity`. El modo se debe pasar como un número: si usa una combinación de indicadores constantes en su código, compruebe el valor total.

Engagement siempre usa la clave booleana `engagement:key` dentro del archivo de preferencias para administrar este valor.

En el siguiente ejemplo de `AndroidManifest.xml` se muestran los valores predeterminados:

			<application>
			    [...]
			    <meta-data
			      android:name="engagement:agent:settings:name"
			      android:value="engagement.agent" />
			    <meta-data
			      android:name="engagement:agent:settings:mode"
			      android:value="0" />

Luego, puede agregar un `CheckBoxPreference` a su diseño de preferencias como el siguiente:

			<CheckBoxPreference
			  android:key="engagement:enabled"
			  android:defaultValue="true"
			  android:title="Use Engagement"
			  android:summaryOn="Engagement is enabled."
			  android:summaryOff="Engagement is disabled." />

<!-- URLs. -->
[Device API]: http://go.microsoft.com/?linkid=9876094

<!---HONumber=AcomDC_0302_2016-->