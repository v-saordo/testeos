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

#Integración de cobertura para Engagement en Android

> [AZURE.IMPORTANT] Debe seguir el procedimiento de integración descrito en el documento Integración de Engagement en Android antes de seguir con esta guía.

##Integración estándar

El SDK de cobertura requiere la **biblioteca de soporte de Android (v4)**.

La forma más rápida de agregar la biblioteca al proyecto en **Eclipse** es `Right click on your project -> Android Tools -> Add Support Library...`.

Si no utiliza Eclipse, puede leer las instrucciones [aquí].

Copie los archivos de recursos de cobertura desde el SDK en el proyecto:

-   Copie los archivos desde la carpeta `res/layout` proporcionada con el SDK a la carpeta `res/layout` de su aplicación.
-   Copie los archivos desde la carpeta `res/drawable` proporcionada con el SDK a la carpeta `res/drawable` de su aplicación.

Edite su archivo `AndroidManifest.xml`:

-   Agregue la siguiente sección (entre las etiquetas `<application>` y `</application>`):

			<activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity" android:theme="@android:style/Theme.Light">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
			    <category android:name="android.intent.category.DEFAULT" />
			    <data android:mimeType="text/plain" />
			  </intent-filter>
			</activity>
			<activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity" android:theme="@android:style/Theme.Light">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
			    <category android:name="android.intent.category.DEFAULT" />
			    <data android:mimeType="text/html" />
			  </intent-filter>
			</activity>
			<activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementPollActivity" android:theme="@android:style/Theme.Light">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
			    <category android:name="android.intent.category.DEFAULT" />
			  </intent-filter>
			</activity>
			<activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementLoadingActivity" android:theme="@android:style/Theme.Dialog">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.reach.intent.action.LOADING"/>
			    <category android:name="android.intent.category.DEFAULT"/>
			  </intent-filter>
			</activity>
			<receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver" android:exported="false">
			  <intent-filter>
			    <action android:name="android.intent.action.BOOT_COMPLETED"/>
			    <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
			    <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
			    <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
			    <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
			    <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
			  </intent-filter>
			</receiver>
			<receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachDownloadReceiver">
			  <intent-filter>
			    <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
			  </intent-filter>
			</receiver>

-   Necesita este permiso para reproducir notificaciones del sistema en las que no se hizo clic durante el arranque (de lo contrario, se mantendrán en el disco, pero no volverán a mostrarse. Realmente debe incluir esto).

			<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />

-   Para especificar un icono utilizado para las notificaciones (tanto en las notificaciones de aplicación y del sistema), copie y edite la siguiente sección (entre las etiquetas `<application>` y `</application>`):

			<meta-data android:name="engagement:reach:notification:icon" android:value="<name_of_icon_WITHOUT_file_extension_and_WITHOUT_'@drawable/'>" />

> [AZURE.IMPORTANT] Esta sección es **obligatoria** si planifica utilizar notificaciones del sistema al crear campañas de cobertura. Android impide que se muestren las notificaciones del sistema sin iconos. Por tanto, si omite esta sección, los usuarios finales no podrán recibirlas.

-   Si crea campañas con notificaciones del sistema con imagen global, deberá agregar los siguientes permisos (después de la etiqueta `</application>`) si no se encuentran presentes:

			<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
			<uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>

  -   En Android M y si la aplicación está destinada al nivel de API Android 23 o superior, el permiso ``WRITE_EXTERNAL_STORAGE`` requiere la aprobación del usuario. Lea [esta sección](mobile-engagement-android-integrate-engagement.md#android-m-permissions).

-   En el caso de las notificaciones del sistema, también puede especificar en la campaña de cobertura si el dispositivo debe sonar y/o vibrar. Para que funcione, debe asegurarse de haber declarado el siguiente permiso (después de la etiqueta `</application>`):

			<uses-permission android:name="android.permission.VIBRATE" />

	Sin este permiso, Android impide que se muestren notificaciones del sistema si marca la opción de sonar o vibrar en el administrador de la campaña de cobertura.

-   Si crea su aplicación con **ProGuard** y tiene errores relacionados con la biblioteca de soporte Android o el archivo jar de Engagement, agregue las siguientes líneas a su archivo `proguard.cfg`:

			-dontwarn android.**
			-keep class android.support.v4.** { *; }

## Inserción nativa:

Ahora que ha configurado el módulo Reach, deberá configurar la inserción nativa para poder recibir las campañas en el dispositivo.

Se admiten dos servicios en Android:

  - Dispositivos de Google Play: Use [Servicio de mensajería en la nube de Google] siguiendo la guía [Integración de GCM con Engagement](mobile-engagement-android-gcm-integrate.md).
  - Dispositivos Amazon: Use [Amazon Device Messaging] siguiendo la guía [Integración de ADM con Engagement](mobile-engagement-android-adm-integrate.md).

Si desea orientarse a dispositivos de Amazon y de Google Play, es posible que todo esté dentro de un AndroidManifest.xml/APK para desarrollo. Pero al enviar a Amazon, es posible que se rechace la aplicación si se encuentra código de GCM.

En ese caso, debe usar varios APK.

**Ahora su aplicación está lista para recibir y mostrar campañas de cobertura.**

##Control de inserción de datos

### Integración

Si desea que su aplicación reciba inserciones de datos de cobertura, debe crear una subclase de `com.microsoft.azure.engagement.reach.EngagementReachDataPushReceiver` y hacer referencia a ella en el archivo `AndroidManifest.xml` (entre las etiquetas `<application>` y/o `</application>`):

			<receiver android:name="<your_sub_class_of_com.microsoft.azure.engagement.reach.EngagementReachDataPushReceiver>"
			  android:exported="false">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.reach.intent.action.DATA_PUSH" />
			  </intent-filter>
			</receiver>

Luego puede invalidar las devoluciones de llamada `onDataPushStringReceived` y `onDataPushBase64Received`. Aquí tiene un ejemplo:

			public class MyDataPushReceiver extends EngagementReachDataPushReceiver
			{
			  @Override
			  protected Boolean onDataPushStringReceived(Context context, String category, String body)
			  {
			    Log.d("tmp", "String data push message received: " + body);
			    return true;
			  }
			
			  @Override
			  protected Boolean onDataPushBase64Received(Context context, String category, byte[] decodedBody, String encodedBody)
			  {
			    Log.d("tmp", "Base64 data push message received: " + encodedBody);
			    // Do something useful with decodedBody like updating an image view
			    return true;
			  }
			}

### Categoría

El parámetro de categoría es opcional cuando se crea una campaña de inserción de datos y permite filtrar los datos que inserta. Esto es útil si tiene varios receptores de difusión que controlan distintos tipos de inserciones de datos, o bien, si desea insertar distintos tipos de datos `Base64` y desea identificar su tipo antes de analizarlos.

### Parámetro de devolución de devoluciones de llamada

Estas son algunas directrices para manejar correctamente el parámetro de devolución de `onDataPushStringReceived` y `onDataPushBase64Received`:

-   Un receptor de difusión debiera devolver `null` en la devolución de llamada si no sabe cómo controlar una inserción de datos. Debe usar la categoría para determina si el receptor de difusión debe controlar o no la inserción de datos.
-   Uno de los receptores de difusión debe devolver `true` en la devolución de llamada si acepta la inserción de datos.
-   Uno de los receptores de difusión debe devolver `false` en la devolución de llamada si reconoce la inserción de datos, pero la descarta por cualquier motivo. Por ejemplo, devuelve `false` cuando los datos recibidos no son válidos.
-   Si un receptor de difusión devuelve `true` mientras que otro devuelve `false` para la misma inserción de datos, el comportamiento es indefinido; nunca debe hacerlo.

El tipo de devolución se usa solo para las estadísticas de cobertura:

-   `Replied` aumenta si uno de los receptores de difusión devolvió `true` o `false`.
-   `Actioned` aumenta solo si uno de los receptores de difusión devolvió `true`.

##Personalización de las campañas

Para personalizar campañas, puede modificar los diseños proporcionados en el SDK de cobertura.

Debe conservar todos los identificadores usados en los diseños y los tipos de las vistas que usa un identificador, especialmente para vistas de texto y vistas de imagen. Algunas vistas solo se utilizan para ocultar o mostrar áreas, por tanto, es posible que se cambie su tipo. Compruebe el código fuente si intenta cambiar el tipo de una vista de los diseños proporcionados.

### Notificaciones

Existen dos tipos de notificaciones: notificaciones del sistema y notificaciones en aplicación, las que usan distintos archivos de diseño.

#### Notificaciones del sistema

Para personalizar las notificaciones del sistema, debe usar las **categorías**. Puede ir a [Categorías](#categories).

#### Notificación en aplicación

De manera predeterminada, una notificación en aplicación es una vista que se agrega de manera dinámica a la interfaz de usuario de actividad actual gracias al método Android `addContentView()`. Esto se denomina superposición de notificaciones. Las superposiciones de notificación son ideales para una integración rápida, debido a que no requieren que modifique ningún diseño en la aplicación.

Para modificar el aspecto de las superposiciones de notificación, puede simplemente modificar el archivo `engagement_notification_area.xml` según sus necesidades.

> [AZURE.NOTE] El archivo `engagement_notification_overlay.xml` es el que se usa para crear una superposición de notificación; incluye el archivo `engagement_notification_area.xml`. También puede personalizarla para ajustarse a sus necesidades (como para posicionar el área de notificación dentro de la superposición).

##### Incluya el diseño de la notificación como parte de un diseño de actividad

Las superposiciones son ideales para lograr una integración rápida, pero puede ser poco conveniente o tener efectos secundarios en casos especiales. El sistema de superposición se puede personalizar en el nivel de una actividad, para que sea fácil impedir los efectos secundarios para actividades especiales.

Puede decidir incluir nuestro diseño de notificación en su diseño existente gracias a la instrucción **include** de Android. El siguiente es un ejemplo de un diseño `ListActivity` modificado que contiene solo una `ListView`.

**Antes de la integración de Engagement :**

			<?xml version="1.0" encoding="utf-8"?>
			<ListView
			  xmlns:android="http://schemas.android.com/apk/res/android"
			  android:id="@android:id/list"
			  android:layout_width="fill_parent"
			  android:layout_height="fill_parent" />

**Después de la integración de Engagement :**

			<?xml version="1.0" encoding="utf-8"?>
			<LinearLayout
			  xmlns:android="http://schemas.android.com/apk/res/android"
			  android:orientation="vertical"
			  android:layout_width="fill_parent"
			  android:layout_height="fill_parent">
			
			  <ListView
			    android:id="@android:id/list"
			    android:layout_width="fill_parent"
			    android:layout_height="fill_parent"
			    android:layout_weight="1" />
			
			  <include layout="@layout/engagement_notification_area" />
			
			</LinearLayout>

En este ejemplo agregamos un contenedor principal, debido a que el diseño original usó una vista de lista como el elemento de nivel superior. También agregamos `android:layout_weight="1"` para poder agregar una vista debajo de una vista de lista configurada con `android:layout_height="fill_parent"`.

El SDK de cobertura para Engagement detecta automáticamente que el diseño de notificación está incluido en esta actividad y no agregará una superposición para esta actividad.

> [AZURE.TIP] Si usa una ListActivity en su aplicación, una superposición de cobertura visible impedirá que vuelva a reaccionar ante los elementos en los que se ha hecho clic en la vista de lista. Este es un problema conocido. Para solucionar este problema, le recomendamos que incruste el diseño de notificación en su propio diseño de actividad de lista, como en el ejemplo anterior.

##### Deshabilitación de notificación de aplicación por actividad

Si no desea agregar la superposición a su actividad y no desea incluir el diseño de notificación en su propio diseño, puede deshabilitar la superposición para esta actividad en el `AndroidManifest.xml` al agregar una sección `meta-data`, como en el siguiente ejemplo:

			<activity android:name="SplashScreenActivity">
			  <meta-data android:name="engagement:notification:overlay" android:value="false"/>
			</activity>

#### <a name="categories"></a> Categorías

Cuando modifica los diseños proporcionados, modifica el aspecto de todas las notificaciones. Las categorías permiten definir varios destinos objetivo (posiblemente comportamientos) para las notificaciones. Las categorías pueden especificarse cuando se crea una campaña de cobertura. Tenga en cuenta que las categorías también permiten personalizar anuncios y sondeos, aspectos que se describen más adelante en este documento.

Para registrar un controlador de categorías para las notificaciones, debe agregar una llamada cuando se inicializa la aplicación.

> [AZURE.IMPORTANT] Lea la advertencia acerca del atributo android:process <android-sdk-engagement-process> en el tema Integración de Engagement en Android antes de continuar.

El siguiente ejemplo supone que reconoció la advertencia anterior y que usa una subclase de `EngagementApplication`:

			public class MyApplication extends EngagementApplication
			{
			  @Override
			  protected void onApplicationProcessCreate()
			  {
			    // [...] other init
			    EngagementReachAgent reachAgent = EngagementReachAgent.getInstance(this);
			    reachAgent.registerNotifier(new MyNotifier(this), "myCategory");
			  }
			}

El objeto `MyNotifier` es la implementación del controlador de categorías de notificación. Es una implementación de la interfaz `EngagementNotifier` o una subclase de la implementación predeterminada: `EngagementDefaultNotifier`.

Observe que el mismo notificador puede controlar varias categorías. Puede registrarlas de la siguiente manera:

			reachAgent.registerNotifier(new MyNotifier(this), "myCategory", "myAnotherCategory");

Para reemplazar la implementación de categoría predeterminada, puede registrar su implementación como en el siguiente ejemplo:

			public class MyApplication extends EngagementApplication
			{
			  @Override
			  protected void onApplicationProcessCreate()
			  {
			    // [...] other init
			    EngagementReachAgent reachAgent = EngagementReachAgent.getInstance(this);
			    reachAgent.registerNotifier(new MyNotifier(this), Intent.CATEGORY_DEFAULT); // "android.intent.category.DEFAULT"
			  }
			}

La categoría actual usada en un controlador se transmite como un parámetro en la mayoría de los métodos que puede invalidar en `EngagementDefaultNotifier`.

Se transmite como un parámetro `String` o de manera indirecta en un objeto `EngagementReachContent` que tiene un método `getCategory()`.

Puede cambiar la mayor parte del proceso de creación de notificaciones si redefine los métodos en `EngagementDefaultNotifier`; si desea obtener una apariencia de personalización más avanzada, revise la documentación técnica y el código fuente.

##### Notificación en aplicación

Si solo desea usar diseños alternativos para una categoría específica, puede implementar esto como en el siguiente ejemplo:
			
			public class MyNotifier extends EngagementDefaultNotifier
			{
			  public MyNotifier(Context context)
			  {
			    super(context);
			  }
			
			  @Override
			  protected int getOverlayLayoutId(String category)
			  {
			    return R.layout.my_notification_overlay;
			  }
			
			
			  @Override
			  public Integer getOverlayViewId(String category)
			  {
			    return R.id.my_notification_overlay;
			  }
			
			  @Override
			  public Integer getInAppAreaId(String category)
			  {
			    return R.id.my_notification_area;
			  }
			}

**Ejemplo de `my_notification_overlay.xml`:**

			<?xml version="1.0" encoding="utf-8"?>
			<RelativeLayout
			  xmlns:android="http://schemas.android.com/apk/res/android"
			  android:id="@+id/my_notification_overlay"
			  android:layout_width="fill_parent"
			  android:layout_height="fill_parent">
			
			  <include layout="@layout/my_notification_area" />
			
			</RelativeLayout>

Como puede ver, el identificador de vista de superposición es distinto al estándar. Es importante que cada diseño utilice un identificador único para las superposiciones.

**Ejemplo de `my_notification_area.xml`:**

			<?xml version="1.0" encoding="utf-8"?>
			<merge
			  xmlns:android="http://schemas.android.com/apk/res/android"
			  android:layout_width="fill_parent"
			  android:layout_height="fill_parent">
			
			  <RelativeLayout
			    android:id="@+id/my_notification_area"
			    android:layout_width="fill_parent"
			    android:layout_height="64dp"
			    android:layout_alignParentTop="true"
			    android:background="#B000">
			
			    <LinearLayout
			      android:orientation="horizontal"
			      android:layout_width="fill_parent"
			      android:layout_height="fill_parent"
			      android:gravity="center_vertical">
			
			      <ImageView
			        android:id="@+id/engagement_notification_icon"
			        android:layout_width="48dp"
			        android:layout_height="48dp" />
			
			      <LinearLayout
			        android:id="@+id/engagement_notification_text"
			        android:orientation="vertical"
			        android:layout_width="fill_parent"
			        android:layout_height="fill_parent"
			        android:layout_weight="1"
			        android:gravity="center_vertical">
			
			        <TextView
			          android:id="@+id/engagement_notification_title"
			          android:layout_width="fill_parent"
			          android:layout_height="wrap_content"
			          android:singleLine="true"
			          android:ellipsize="end"
			          android:textAppearance="@android:style/TextAppearance.Medium" />
			
			        <TextView
			          android:id="@+id/engagement_notification_message"
			          android:layout_width="fill_parent"
			          android:layout_height="wrap_content"
			          android:maxLines="2"
			          android:ellipsize="end"
			          android:textAppearance="@android:style/TextAppearance.Small" />
			
			      </LinearLayout>
			
			      <ImageView
			        android:id="@+id/engagement_notification_image"
			        android:layout_width="wrap_content"
			        android:layout_height="fill_parent"
			        android:adjustViewBounds="true" />
			
			      <ImageButton
			        android:id="@+id/engagement_notification_close_area"
			        android:visibility="invisible"
			        android:layout_width="wrap_content"
			        android:layout_height="fill_parent"
			        android:src="@android:drawable/btn_dialog"
			        android:background="#0F00" />
			
			    </LinearLayout>
			
			    <ImageButton
			      android:id="@+id/engagement_notification_close"
			      android:layout_width="wrap_content"
			      android:layout_height="fill_parent"
			      android:layout_alignParentRight="true"
			      android:src="@android:drawable/btn_dialog"
			      android:background="#0F00" />
			
			  </RelativeLayout>
			
			</merge>

Como puede ver, el identificador de vista del área de notificación es distinto del estándar. Es importante que cada diseño utilice un identificador único para las áreas de notificación.

Este ejemplo simple de categoría crea notificaciones de aplicación (o en aplicación) que se muestran en la parte superior de la pantalla. No cambiamos los identificadores estándar que se usan en el área de notificación misma.

Si desea cambiar eso, debe redefinir el método `EngagementDefaultNotifier.prepareInAppArea`. Se recomienda consultar la documentación técnica y el código fuente de `EngagementNotifier` y `EngagementDefaultNotifier` si desea este nivel de personalización avanzada.

##### Notificaciones del sistema

Al extender `EngagementDefaultNotifier`, puede invalidar `onNotificationPrepared` para modificar la notificación que se preparó mediante la implementación predeterminada.

Por ejemplo:

			@Override
			protected boolean onNotificationPrepared(Notification notification, EngagementReachInteractiveContent content)
			  throws RuntimeException
			{
			  if ("ongoing".equals(content.getCategory()))
			    notification.flags |= Notification.FLAG_ONGOING_EVENT;
			  return true;
			}

Este ejemplo crea una notificación del sistema para un contenido que se muestra como un evento en curso cuando se utiliza la categoría "en curso".

Si desea crear el objeto `Notification` desde cero, puede devolver `false` al método y llamar a `notify` usted mismo en `NotificationManager`. En ese caso, es importante que conserve un `contentIntent`, un `deleteIntent` y el identificador de notificación que utiliza `EngagementReachReceiver`.

El siguiente es un ejemplo de una implementación de ese tipo correcta:

			@Override
			protected boolean onNotificationPrepared(Notification notification, EngagementReachInteractiveContent content) throws RuntimeException
			{
			  /* Required fields */
			  NotificationCompat.Builder builder = new NotificationCompat.Builder(mContext)
			    .setSmallIcon(notification.icon)              // icon is mandatory
			    .setContentIntent(notification.contentIntent) // keep content intent
			    .setDeleteIntent(notification.deleteIntent);  // keep delete intent
			
			  /* Your customization */
			  // builder.set...
			
			  /* Dismiss option can be managed only after build */
			  Notification myNotification = builder.build();
			  if (!content.isNotificationCloseable())
			    myNotification.flags |= Notification.FLAG_NO_CLEAR;
			
			  /* Notify here instead of super class */
			  NotificationManager manager = (NotificationManager) mContext.getSystemService(Context.NOTIFICATION_SERVICE);
			  manager.notify(getNotificationId(content), myNotification); // notice the call to get the right identifier
			
			  /* Return false, we notify ourselves */
			  return false;
			}

##### Anuncios solo de notificación

La administración del clic en un anuncio de solo notificación se puede personalizar al anular `EngagementDefaultNotifier.onNotifAnnouncementIntentPrepared` para modificar el `Intent` preparado. Usar este método le permite ajusta fácilmente las marcas.

Por ejemplo, para agregar la marca `SINGLE_TOP`:
			
			@Override
			protected Intent onNotifAnnouncementIntentPrepared(EngagementNotifAnnouncement notifAnnouncement,
			  Intent intent)
			{
			  intent.addFlags(Intent.FLAG_ACTIVITY_SINGLE_TOP);
			  return intent;
			}

En el caso de los usuarios de Engagement heredado, observe que las notificaciones del sistema sin dirección URL de acción ahora inicia la aplicación si estaba en segundo plano, por tanto, es posible llamar a este método con un anuncio sin una dirección URL de acción. Debe considerar eso al personalizar el intento.

También puede implementar `EngagementNotifier.executeNotifAnnouncementAction` desde cero.

##### Ciclo de vida de notificación

Al utilizar la categoría predeterminada, se llama a algunos métodos de ciclo de vida en el objeto `EngagementReachInteractiveContent` para mostrar estadísticas y actualizar el estado de la campaña:

-   Cuando la notificación se muestra en la aplicación o se pone en la barra de estado, se llama al método `displayNotification` (que informa las estadísticas) mediante `EngagementReachAgent` si `handleNotification` devuelve `true`.
-   Si se descarta la notificación, se llama al método `exitNotification`, se notifica la estadística y ahora se pueden procesar las campañas siguientes.
-   Si se hace clic en la notificación, se llama a `actionNotification`, se informa la estadística y se inicie el intento asociado.

Si su implementación de pasa por alto `EngagementNotifier` el comportamiento predeterminado, tiene que llamar a estos métodos de ciclo de vida por sí mismo. Los ejemplos siguientes muestran algunos casos donde se omite el comportamiento predeterminado:

-   No se extienden `EngagementDefaultNotifier`, por ejemplo, se implementa el control de categoría desde cero.
-   En el caso de las notificaciones del sistema, anuló la `onNotificationPrepared` y modificó el `contentIntent` o el `deleteIntent` en el objeto `Notification`.
-   En el caso de las notificaciones en aplicación, anuló `prepareInAppArea`; asegúrese de asignar, al menos `actionNotification`, a uno de sus controles de la interfaz de usuario.

> [AZURE.NOTE] Si `handleNotification` arroja una excepción, se elimina el contenido y se llama a `dropContent`. Esto se informa en las estadísticas y ahora es posible procesar las siguientes campañas.

### Anuncios y sondeos

#### Diseños

Puede modificar los archivos `engagement_text_announcement.xml`, `engagement_web_announcement.xml` y `engagement_poll.xml` para personalizar anuncios de texto, anuncios web y sondeos.

Estos archivos comparten dos diseños comunes para el área de título y el área de botones. El diseño del título es `engagement_content_title.xml` y usa el archivo dibujable epónimo para el segundo plano. El diseño de los botones de acción y salida es `engagement_button_bar.xml` y usa el archivo dibujable epónimo para el segundo plano.

En un sondeo, el diseño de la pregunta y sus opciones se inflan de manera dinámica usando varias veces el archivo de diseño `engagement_question.xml` para las preguntas y el archivo `engagement_choice.xml` para las opciones.

#### Categorías

##### Diseños alternativos

Como con las notificaciones, la categoría de la campaña puede utilizarse para tener diseños alternativos para los anuncios y sondeos.

Por ejemplo, para crear una categoría para un anuncio de texto, puede extender `EngagementTextAnnouncementActivity` y hacer referencia a él en el archivo `AndroidManifest.xml`:

			<activity android:name="com.your_company.MyCustomTextAnnouncementActivity">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.intent.action.ANNOUNCEMENT"/>
			    <category android:name="my_category" />
			    <data android:mimeType="text/plain" />
			  </intent-filter>
			</activity>

Observe que la categoría del filtro de intento se usa para marcar la diferencia con la actividad de anuncio predeterminada.

El SDK de cobertura usa el sistema de intento para resolver la actividad adecuada para una categoría específica y vuelve a la categoría predeterminada si la resolución no se realiza correctamente.

Luego debe implementar `MyCustomTextAnnouncementActivity`; si solo desea cambiar el diseño (pero mantener los mismos identificadores de vista), solo debe definir la clase como en el siguiente ejemplo:

			public class MyCustomTextAnnouncementActivity extends EngagementTextAnnouncementActivity
			{
			  @Override
			  protected String getLayoutName()
			  {
			    return "my_text_announcement";  // tell super class to use R.layout.my_text_announcement
			  }
			}

Para reemplazar la categoría predeterminada de anuncios de texto, solo reemplace `android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity"` por su implementación.

Los anuncios web y los sondeos se pueden personalizar de manera similar.

En el caso de los anuncios web, puede extender `EngagementWebAnnouncementActivity` y declarar su actividad en el `AndroidManifest.xml`, como en el ejemplo siguiente:

			<activity android:name="com.your_company.MyCustomWebAnnouncementActivity">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.intent.action.ANNOUNCEMENT"/>
			    <category android:name="my_category" />
			    <data android:mimeType="text/html" />    <!-- only difference with text announcements in the intent is the data mime type -->
			  </intent-filter>
			</activity>

En el caso de los sondeos, puede extender `EngagementPollActivity` y declarar su actividad en el `AndroidManifest.xml`, como en el ejemplo siguiente:

			<activity android:name="com.your_company.MyCustomPollActivity">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.intent.action.POLL"/>
			    <category android:name="my_category" />
			  </intent-filter>
			</activity>
			
##### Implementación desde cero

Puede implementar categorías para sus actividades de anuncio (y sondeo) sin extender una de las clases `Engagement*Activity` proporcionadas por el SDK de cobertura. Esto resulta útil, por ejemplo, si desea definir un diseño que no utiliza las mismas vistas que los diseños estándar.

Al igual que para la personalización de notificación avanzada, se recomienda mirar el código fuente de la implementación estándar.

A continuación se indican algunas cosas que es necesario tener en cuenta: Cobertura iniciará la actividad con un intento específico (correspondiente al filtro de intento), más un parámetro adicional, que es el identificador de contenido.

Para recuperar el objeto de contenido que contiene los campos que especificó al crear la campaña en el sitio web, puede hacer lo siguiente:

			public class MyCustomTextAnnouncement extends EngagementActivity
			{
			  private EngagementAnnouncement mContent;
			
			  @Override
			  protected void onCreate(Bundle savedInstanceState)
			  {
			    super.onCreate(savedInstanceState);
			
			    /* Get content */
			    mContent = EngagementReachAgent.getInstance(this).getContent(getIntent());
			    if (mContent == null)
			    {
			      /* If problem with content, exit */
			      finish();
			      return;
			    }
			
			    setContentView(R.layout.my_text_announcement);
			
			    /* Configure views by querying fields on mContent */
			    // ...
			  }
			}

En el caso de las estadísticas, debe informar que el contenido se muestra en el evento `onResume`:
			
			@Override
			protected void onResume()
			{
			 /* Mark the content displayed */
			 mContent.displayContent(this);
			 super.onResume();
			}

Luego, no olvide de llamar a `actionContent(this)` o a `exitContent(this)` en el objeto de contenido antes de que la actividad pase a segundo plano.

Si no llama a `actionContent` o a `exitContent`, no se enviarán las estadísticas (es decir, no habrá análisis en la campaña) y, lo que es más importante, las siguientes campañas no se notificarán hasta que se reinicie el proceso de la aplicación.

Los cambios de orientación u otros cambios de configuración pueden hacer que el código sea complicado como para determinar si la actividad pasa o no al segundo plano; la implementación estándar se asegura de que se informe el contenido al salir si el usuario deja la actividad (ya sea presionando `HOME` o `BACK`), pero no si cambia la orientación.

Esta es la parte interesante de la implementación:

			@Override
			protected void onUserLeaveHint()
			{
			  finish();
			}
			
			@Override
			protected void onPause()
			{
			  if (isFinishing() && mContent != null)
			  {
			    /*
			     * Exit content on exit, this is has no effect if another process method has already been
			     * called so we don't have to check anything here.
			     */
			    mContent.exitContent(this);
			  }
			  super.onPause();
			}

Como puede ver, si llamó a `actionContent(this)` y luego finalizó la actividad, se puede llamar a `exitContent(this)` de manera segura sin que tenga ningún efecto.

[aquí]: http://developer.android.com/tools/extras/support-library.html#Downloading
[Servicio de mensajería en la nube de Google]: http://developer.android.com/guide/google/gcm/index.html
[Amazon Device Messaging]: https://developer.amazon.com/sdk/adm.html
 

<!---HONumber=AcomDC_0302_2016-->