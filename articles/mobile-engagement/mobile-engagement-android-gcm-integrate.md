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

#Integración de GCM con Mobile Engagement

> [AZURE.IMPORTANT] Debe seguir el procedimiento de integración descrito en el documento Integración de Engagement en Android antes de seguir con esta guía.
>
> Este documento solo es útil si integró el módulo de Reach para admitir campañas en cualquier momento. Para integrar campañas de Reach en la aplicación, lea primero Integración de Engagement Reach en Android.

##Introducción

La integración de GCM permite realizar inserciones en su aplicación.

Las cargas GCM insertadas en el SDK siempre contienen la clave `azme` en el objeto de datos. Por lo tanto, si utiliza GCM para otra finalidad en la aplicación, puede filtrar las inserciones basándose en esa clave.

> [AZURE.IMPORTANT] Solo los dispositivos que ejecuten Android 2.2 o versiones posteriores, con Google Play instalado y con una conexión de fondo de Google habilitada, se pueden insertar mediante GCM; sin embargo, puede integrar este código de forma segura en los dispositivos no compatibles (simplemente usa representaciones).

##Suscripción a GCM y habilitación del servicio de GCM

Si aún no lo ha hecho, debe habilitar el servicio de GCM en su cuenta de Google.

En la fecha de redacción de este documento (5 de febrero de 2014), puede seguir el procedimiento en: [<http://developer.android.com/guide/google/gcm/gs.html>].

Siga este procedimiento para habilitar GCM en su cuenta. Cuando llegue a la sección **Obtención de una clave de API**, no la lea y vuelva a esta página en lugar de seguir con el procedimiento de Google.

El procedimiento explica que el **número de proyecto** se usa como el **Id. de remitente de GCM**; lo necesitará más adelante en este procedimiento.

> [AZURE.IMPORTANT] El **número de proyecto** no debe confundirse con el **Id. de proyecto**. El Id. de proyecto ahora puede ser diferente (es un nombre en los proyectos nuevos). Lo que necesita integrar en el SDK de Engagement es el **número de proyecto**, que se muestra en el menú **Información general** de la [Consola de desarrolladores de Google].

##Integración de SDK

### Administración de registros de dispositivos

Cada dispositivo debe enviar un comando de registro a los servidores de Google; en caso contrario, no se puede establecer la conexión.

También puede anular el registro de un dispositivo de las notificaciones de GCM (el dispositivo se elimina automáticamente si se desinstala la aplicación).

Si usa la [biblioteca de cliente GCM], puede leer directamente android-sdk-gcm-receive.

Si aún no ha enviado el intento de registro, puede hacer que Engagement registre el dispositivo automáticamente.

Para habilitar esta opción, agregue lo siguiente al archivo de `AndroidManifest.xml`, dentro de la etiqueta `<application/>`:

			<!-- If only 1 sender, don't forget the \n, otherwise it will be parsed as a negative number... -->
			<meta-data android:name="engagement:gcm:sender" android:value="<Your Google Project Number>\n" />

### Comunicar el identificador de registro al servicio de inserción de Engagement y recibir notificaciones

Para comunicar el identificador de registro del dispositivo al servicio de inserción de Engagement y recibir notificaciones, agregue lo siguiente al archivo de `AndroidManifest.xml`, dentro de la etiqueta `<application/>` (aunque administre registros de dispositivos):

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
			    <category android:name="<your_package_name>" />
			  </intent-filter>
			</receiver>

Asegúrese de tener los permisos siguientes en `AndroidManifest.xml` (después de la etiqueta `</application>`).

			<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
			<uses-permission android:name="<your_package_name>.permission.C2D_MESSAGE" />
			<permission android:name="<your_package_name>.permission.C2D_MESSAGE" android:protectionLevel="signature" />

##Conceder acceso de Engagement a una clave de API de servidor

Si aún no lo ha hecho, cree una **clave de API de servidor** en la [Consola de desarrolladores de Google].

La clave del servidor **no debe tener restricción de IP**.

En la fecha de redacción de este documento (5 de febrero de 2014), el procedimiento es el siguiente:

-   Para ello, abra la [Consola de desarrolladores de Google].
-   Seleccione el mismo proyecto que antes en el procedimiento (el que tiene el **número de proyecto** que integró en `AndroidManifest.xml`).
-   Vaya a APIs & auth-\\ > Credenciales, haga clic en "CREAR NUEVA CLAVE" en la sección "Acceso a API pública".
-   Seleccione "Clave de servidor".
-   En la siguiente pantalla, déjela en blanco **(sin restricciones de IP)** y haga clic en crear.
-   Copie la **clave de API** generada.
-   Vaya a $/#application/YOUR\_ENGAGEMENT\_APPID/native-push.
-   En la sección GCM edite la clave de API con la que acaba de generar y copiar.

Ahora es posible seleccionar "En cualquier momento" al crear sondeos y anuncios de Reach.

> [AZURE.IMPORTANT] En realidad, Engagement necesita una **clave de servidor**, no se puede usar una clave de Android por servidores de Engagement.

##Prueba

Ahora, para comprobar su integración, lea Prueba de integración de Engagement en Android.


[<http://developer.android.com/guide/google/gcm/gs.html>]: http://developer.android.com/guide/google/gcm/gs.html
[Google Developers Console]: https://cloud.google.com/console
[biblioteca de cliente GCM]: http://developer.android.com/guide/google/gcm/gs.html#libs
[Consola de desarrolladores de Google]: https://cloud.google.com/console

<!---HONumber=AcomDC_0302_2016-->