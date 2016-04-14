<properties
	pageTitle="Introducción a Azure Mobile Engagement"
	description="Aprenda a usar Azure Mobile Engagement con los análisis y las notificaciones de inserción para aplicaciones Android."
	services="mobile-engagement"
	documentationCenter="android"
	authors="piyushjo"
	manager="dwrede"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="Java"
	ms.topic="hero-article"
	ms.date="12/02/2015"
	ms.author="piyushjo" />

# Introducción a Azure Mobile Engagement para aplicaciones Android

> [AZURE.SELECTOR]
- [Windows Universal](mobile-engagement-windows-store-dotnet-get-started.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-get-started.md)
- [iOS | Obj C](mobile-engagement-ios-get-started.md)
- [iOS | Swift](mobile-engagement-ios-swift-get-started.md)
- [Android](mobile-engagement-android-get-started.md)
- [Cordova](mobile-engagement-cordova-get-started.md)

En este tema se muestra cómo usar Azure Mobile Engagement para comprender el uso de su aplicación y cómo enviar notificaciones de inserción a usuarios segmentados de una aplicación de Android. En este tutorial se demuestra el escenario de difusión sencillo con Mobile Engagement. En él, creará una aplicación en blanco de Android que recopila datos básicos y recibe notificaciones de inserción con el Servicio de mensajería en la nube de Google (GCM).

Este tutorial requiere lo siguiente:

+ el SDK de Android (se supone que va usará Studio Android), que puede descargar [aquí](http://go.microsoft.com/fwlink/?LinkId=389797)
+ el [SDK de Android para Mobile Engagement Android]

> [AZURE.IMPORTANT]Completar este tutorial es un requisito previo para los demás tutoriales de Mobile Engagement para aplicaciones de Android. Para completarlo, debe tener una cuenta activa de Azure. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fes-ES%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Evaluación gratuita de Azure</a>.

##<a id="setup-azme"></a>Configuración de Mobile Engagement para una aplicación Android

[AZURE.INCLUDE [Creación de la aplicación de Mobile Engagement en el portal](../../includes/mobile-engagement-create-app-in-portal.md)]

##<a id="connecting-app"></a>Conectar la aplicación al backend de Mobile Engagement

En este tutorial se presenta una "integración básica", que es el conjunto mínimo necesario para recopilar los datos y enviar una notificación de inserción. La documentación de integración completa se puede encontrar en la [integración del SDK de Android para Mobile Engagement](../mobile-engagement-android-sdk-overview/).

Crearemos una aplicación básica con Android Studio para demostrar la integración.

###Creación de un nuevo proyecto de Android

1. Inicie **Android Studio** y, en el menú emergente, seleccione **Iniciar un nuevo proyecto de Android Studio**.

    ![][1]

2. Especifique el nombre de la aplicación y el dominio de la compañía. Tome nota de los datos que especifique pues los necesitará más adelante. Haga clic en **Siguiente**.

    ![][2]

3. Seleccione el factor de forma de destino y el nivel de API. A continuación, haga clic en **Siguiente**.

	>[AZURE.NOTE]Compromiso de Mobile requiere el nivel de API mínimo de 10 (2.3.3 Android).

    ![][3]

4. Seleccione **Actividad en blanco** aquí, ya que será la única pantalla para esta aplicación, y haga clic en **Siguiente**.

    ![][4]

5. Por último, deje el valor predeterminado como está y haga clic en **Finalizar**.

    ![][5]

Ahora Android Studio crea la aplicación de demostración en la que integraremos Mobile Engagement.

###Incluir la biblioteca de SDK en el proyecto

Descargar e integrar la biblioteca de SDK

1. Descargue el [SDK de Android para Mobile Engagement].
2. Extraiga el archivo a una carpeta en el equipo.
3. Identifique la biblioteca .jar para la versión actual de este SDK y cópiela en el Portapapeles.

	  ![][6]

4. Vaya a la sección **Proyecto** (1) y pegue el .jar en la carpeta libs (2).

	  ![][7]

5. Sincronice el proyecto para cargar la biblioteca.

	  ![][8]

###Conectar la aplicación al back-end de Mobile Engagement mediante la cadena de conexión

1. Copie las siguientes líneas de código en la creación de la actividad (debe realizarse solo en un lugar de la aplicación, normalmente la actividad principal). Para esta aplicación de ejemplo, abra MainActivity en la carpeta src -> main -> java y agregue lo siguiente:

		EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
		engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
		EngagementAgent.getInstance(this).init(engagementConfiguration);

2. Resuelva las referencias pulsando Alt + Intro o agregando las siguientes instrucciones de importación:

		import com.microsoft.azure.engagement.EngagementAgent;
		import com.microsoft.azure.engagement.EngagementConfiguration;

3. Vuelva al Portal de Azure clásico en la página **Información de conexión** de la aplicación y copie la **cadena de conexión**.

	  ![][9]

4. Péguela en el parámetro `setConnectionString` para reemplazar el ejemplo proporcionado, tal como se muestra a continuación:

		engagementConfiguration.setConnectionString("Endpoint=my-company-name.device.mobileengagement.windows.net;SdkKey=********************;AppId=*********");

###Agregar permisos y una declaración de servicio

1. Agregue estos permisos al archivo Manifest.xml del proyecto inmediatamente anterior a la etiqueta `<application>`:

		<uses-permission android:name="android.permission.INTERNET"/>
		<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
		<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
		<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
		<uses-permission android:name="android.permission.VIBRATE" />
		<uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>

2. Agregue lo siguiente entre las etiquetas `<application>` y `</application>` para declarar el servicio del agente:

		<service
 			android:name="com.microsoft.azure.engagement.service.EngagementService"
 			android:exported="false"
 			android:label="<Your application name>"
 			android:process=":Engagement"/>

3. En el código que acaba de pegar, reemplace `"<Your application name>"` en la etiqueta. Esto es lo que se muestra en el menú **Configuración** donde los usuarios pueden ver los servicios que se están ejecutando en el dispositivo. Puede agregar la palabra "Servicio" a la etiqueta, por ejemplo.

###Enviar una pantalla a Mobile Engagement

Para comenzar a enviar datos y asegurarse de que los usuarios estén activos, debe enviar al menos una pantalla (Actividad) al back-end de Mobile Engagement.

Vaya a **MainActivity.java** y agregue lo siguiente para reemplazar la clase base de **MainActivity** a **EngagementActivity**:

	public class MainActivity extends EngagementActivity {

Debería convertir en comentario (excluir) la línea siguiente para este escenario de ejemplo simple:

    // setSupportActionBar(toolbar);

Si desea tenerlo a mano, debe consultar el escenario "Informes básicos" en nuestro [Integración de Engagement en Android]

##<a id="monitor"></a>Conexión de la aplicación con la supervisión en tiempo real

[AZURE.INCLUDE [Conectar la aplicación con la supervisión en tiempo real](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Habilitar las notificaciones push y la mensajería en la aplicación

Mobile Engagement permite interactuar y llegar por REACH a los usuarios mediante notificaciones de inserción y mensajería en la aplicación en el contexto de las campañas. Este módulo se denomina REACH en el portal de Mobile Engagement. En las secciones siguientes se instala la aplicación para recibirlos.

### Habilitar la mensajería en la aplicación

1. Copie los siguientes recursos de mensajería en aplicación en Manifest.xml, entre las etiquetas `<application>` y `</application>`.

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

2. Copie los recursos en el proyecto siguiendo estos pasos:
	1. Vuelva al contenido de descarga del SDK y abra la carpeta 'res'.

		 ![][13]

	2. Vuelva a Android Studio, seleccione el directorio 'main' de los archivos de proyecto y luego péguelo para agregar los recursos al proyecto.

		 ![][14]

###Especificación de un icono para las notificaciones

Pegue el siguiente fragmento XML en su Manifest.xml entre las etiquetas `<application>` y `</application>`.

		<meta-data android:name="engagement:reach:notification:icon" android:value="engagement_close"/>

Esto define el icono que se muestra tanto en el sistema como en las notificaciones en aplicación. Es opcional para las notificaciones en aplicación y obligatorio para las notificaciones del sistema. Android rechazará las notificaciones del sistema con iconos no válidos.

Asegúrese de usar un icono que se encuentre en una de las carpetas de recursos **dibujables** (como ``engagement_close.png``). **mipmap** no se admite.

>[AZURE.NOTE]No debería usar el icono del **iniciador**. Tiene una resolución diferente y normalmente está en las carpetas mipmap, que no se admiten.

Para las aplicaciones reales, puede usar un icono adecuado para notificaciones según las [directrices de diseño de Android](http://developer.android.com/design/patterns/notifications.html).

>[AZURE.TIP]Para asegurarse de usar las resoluciones de icono correctas, consulte [estos ejemplos](https://www.google.com/design/icons). Desplácese hasta la sección **Notificación**, haga clic en un icono y luego haga clic en `PNGS` para descargar el conjunto de iconos dibujables. Puede ver qué carpetas de recursos dibujables usar con qué resolución para cada versión del icono.

##Creación de un proyecto del Servicio de mensajería en la nube de Google con clave de API

[AZURE.INCLUDE [mobile-engagement-enable-Google-cloud-messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

###Habilitar la aplicación para recibir notificaciones de inserción de GCM

1. Pegue lo siguiente entre las etiquetas `<application>` y `</application>` del archivo Manifest.xml después de reemplazar el `project number` obtenido en la consola de Google Play. \\n se introduce de manera intencionada para asegurarse de finalizar el número de proyecto con él.

		<meta-data android:name="engagement:gcm:sender" android:value="************\n" />

2. Pegue el código siguiente en su Manifest.xml entre las etiquetas `<application>` y `</application>`. Reemplace el nombre del paquete <Your package name>.

		<receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMEnabler"
		android:exported="false">
			<intent-filter>
				<action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT" />
			</intent-filter>
		</receiver>

		<receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMReceiver" android:permission="com.google.android.c2dm.permission.SEND">
			<intent-filter>
				<action android:name="com.google.android.c2dm.intent.REGISTRATION" />
				<action android:name="com.google.android.c2dm.intent.RECEIVE" />
				<category android:name="<Your package name>" />
			</intent-filter>
		</receiver>

3. Agregue el último conjunto de permisos resaltados antes de la etiqueta `<application>`. Reemplace `<Your package name>` por el nombre real del paquete de la aplicación.

		<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
		<uses-permission android:name="<Your package name>.permission.C2D_MESSAGE" />
		<permission android:name="<Your package name>.permission.C2D_MESSAGE" android:protectionLevel="signature" />

###Conceder acceso de Mobile Engagement a la clave de API de GCM

Para permitir que Mobile Engagement envíe notificaciones de inserción en su nombre, deberá conceder acceso a la clave de API. Esto se hace configurando y escribiendo la clave en el portal de Mobile Engagement.

1. Acceda al portal de Mobile Engagement.

	En el Portal de Azure clásico, asegúrese de que se encuentra en la aplicación que estamos usando para este proyecto y luego haga clic en el botón **Interactuar** en la parte inferior:

	![][15]

2. A continuación, haga clic en **Configuración** -> **Inserción nativa** para acceder a la clave GCM:

	![][16]

3. Haga clic en el icono **Editar** delante de **Clave de API**, en la sección **Configuración de GCM**, tal y como se muestra a continuación:

	![][17]

4. En el menú emergente, pegue la clave del servidor de GCM obtenida antes y, a continuación, haga clic en **Aceptar**.

	![][18]

##<a id="send"></a>Enviar una notificación a su aplicación

Ahora crearemos una campaña de notificación de inserción simple que enviará una notificación de inserción a nuestra aplicación.

1. Vaya a la pestaña **COBERTURA** en el portal de Mobile Engagement.

2. Haga clic en **Nuevo anuncio** para crear la campaña de notificaciones push.

	![][20]

3. Configure el primer campo de la campaña mediante los pasos siguientes:

	![][21]

	a. Asigne un nombre a la campaña.

	b. Seleccione el **Tipo de entrega** como *Notificación del sistema -> Simple*: este es el tipo de notificación push simple de Android que incluye un título y una pequeña línea de texto.

	c. En **Tiempo de entrega**, seleccione *Cualquier momento* para permitir que la aplicación reciba una notificación, independientemente de si se ha iniciado o no.

	d. En el texto de la notificación, escriba el **título** que aparecerá en negrita en la inserción.

	e. Luego, escriba el **Mensaje**.

4. Desplácese hacia abajo y, en la sección **Contenido**, elija **Solo notificación**.

	![][22]

5. Ya ha terminado de configurar la campaña más básica posible. Ahora, desplácese de nuevo hacia abajo y haga clic en el botón **Crear** para guardar la campaña.

6. Último paso: haga clic en **Activar** para activar su campaña y enviar notificaciones push.

	![][24]

<!-- URLs. -->
[SDK de Android para Mobile Engagement]: https://aka.ms/vq9mfn
[SDK de Android para Mobile Engagement Android]: https://aka.ms/vq9mfn
[Mobile Engagement Android SDK documentation]: https://aka.ms/tujlkm
[Integración de Engagement en Android]: https://azure.microsoft.com/es-ES/documentation/articles/mobile-engagement-android-integrate-engagement/#basic-reporting

<!-- Images. -->
[1]: ./media/mobile-engagement-android-get-started/android-studio-new-project.png
[2]: ./media/mobile-engagement-android-get-started/android-studio-project-props.png
[3]: ./media/mobile-engagement-android-get-started/android-studio-project-props2.png
[4]: ./media/mobile-engagement-android-get-started/android-studio-add-activity.png
[5]: ./media/mobile-engagement-android-get-started/android-studio-activity-name.png
[6]: ./media/mobile-engagement-android-get-started/sdk-content.png
[7]: ./media/mobile-engagement-android-get-started/paste-jar.png
[8]: ./media/mobile-engagement-android-get-started/sync-project.png
[9]: ./media/mobile-engagement-android-get-started/app-connection-info-page.png
[13]: ./media/mobile-engagement-android-get-started/copy-resources.png
[14]: ./media/mobile-engagement-android-get-started/paste-resources.png
[15]: ./media/mobile-engagement-android-get-started/engage-button.png
[16]: ./media/mobile-engagement-android-get-started/engagement-portal.png
[17]: ./media/mobile-engagement-android-get-started/native-push-settings.png
[18]: ./media/mobile-engagement-android-get-started/api-key.png
[20]: ./media/mobile-engagement-android-get-started/new-announcement.png
[21]: ./media/mobile-engagement-android-get-started/campaign-first-params.png
[22]: ./media/mobile-engagement-android-get-started/campaign-content.png
[24]: ./media/mobile-engagement-android-get-started/campaign-activate.png

<!---HONumber=AcomDC_0114_2016-->