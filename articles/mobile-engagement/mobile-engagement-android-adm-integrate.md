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


#Integración de ADM con Engagement

> [AZURE.IMPORTANT] Debe seguir el procedimiento de integración descrito en el documento Integración de Engagement en Android antes de seguir con esta guía.
>
> Este documento solo es útil si integró el módulo Reach para admitir cualquier campaña. Para integrar campañas de Reach en la aplicación, lea primero Integración de Engagement Reach en Android.

##Introducción

La integración de ADM permite la inserción de la aplicación cuando tenga como destino dispositivos Android de Amazon.

Las cargas ADM insertadas en el SDK siempre contienen la clave `azme` en el objeto de datos. Por lo tanto, si usa ADM para otra finalidad en la aplicación, puede filtrar las inserciones basándose en esa clave.

> [AZURE.IMPORTANT] Solo los dispositivos Kindle de Amazon que ejecutan Android 4.0.3 o versiones posteriores son compatibles con la mensajería de dispositivos de Amazon; Sin embargo, puede integrar este código de forma segura en otros dispositivos.

##Suscribirse a ADM

Si aún no lo ha hecho, debe habilitar ADM en su cuenta de Amazon.

El procedimiento se detalla en: [<https://developer.amazon.com/sdk/adm/credentials.html>].

Una vez finalizado el procedimiento, obtiene:

-   Credenciales de OAuth (un identificador de cliente y un secreto de cliente) para que Engagement puede realizar inserciones en sus dispositivos.
-   Una clave de API que se debe integrar en la aplicación.

##Integración de SDK

### Administración de registros de dispositivos

Cada dispositivo debe enviar un comando de registro a los servidores de ADM; en caso contrario, no se puede establecer contacto con ellos.

Si ya usa la [biblioteca de cliente ADM] y ya ha [integrado ADM], puede ir directamente a android-sdk-adm-receive.

Si aún no ha integrado ADM, Engagement tiene una forma más sencilla de habilitarlo en la aplicación:

Edite su archivo `AndroidManifest.xml`:

-   Agregue el espacio para nombres de Amazon; el archivo debería empezar así:

		<?xml version="1.0" encoding="utf-8"?>
		<manifest xmlns:android="http://schemas.android.com/apk/res/android"
		          xmlns:amazon="http://schemas.amazon.com/apk/res/android"

-   Dentro de la etiqueta `<application/>`, agregue esta sección:

		<amazon:enable-feature
		   android:name="com.amazon.device.messaging"
		   android:required="false"/>

		<meta-data android:name="engagement:adm:register" android:value="true" />

-   Después de agregar la etiqueta de Amazon, puede tener un error de compilación si su destino de compilación del proyecto es inferior a Android 2.1. Debe usar un destino de compilación **Android 2.1+** (no se preocupe, aún puede disponer de `minSdkVersion` establecido en 4).
-   Integre la clave de API de ADM como un recurso siguiendo [este procedimiento].

A continuación, siga las instrucciones de las secciones siguientes.

### Comunicar el identificador de registro al servicio de inserción de Engagement y recibir notificaciones

Para comunicar el identificador de registro del dispositivo al servicio de inserción de Engagement y recibir sus notificaciones, agregue lo siguiente al archivo de `AndroidManifest.xml`, dentro de la etiqueta `<application/>` (incluso si usa ADM sin Engagement):

		<receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMEnabler"
		  android:exported="false">
		  <intent-filter>
		    <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT"/>
		  </intent-filter>
		</receiver>

		 <receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMReceiver"
		   android:permission="com.amazon.device.messaging.permission.SEND">
		  <intent-filter>
		    <action android:name="com.amazon.device.messaging.intent.REGISTRATION"/>
		    <action android:name="com.amazon.device.messaging.intent.RECEIVE"/>
		    <category android:name="<your_package_name>"/>
		  </intent-filter>
		</receiver>   

Asegúrese de tener los permisos siguientes en el `AndroidManifest.xml` (antes de la etiqueta `</application>`).

		<uses-permission android:name="android.permission.WAKE_LOCK"/>
		<uses-permission android:name="com.amazon.device.messaging.permission.RECEIVE"/>
		<uses-permission android:name="<your_package_name>.permission.RECEIVE_ADM_MESSAGE"/>
		<permission android:name="<your_package_name>.permission.RECEIVE_ADM_MESSAGE" android:protectionLevel="signature"/>

##Conceder credenciales de OAuth de Engagement

Envíe sus credenciales de OAuth (Id. de cliente y secreto de cliente) en $/\\#application/YOUR\\_APPID/native-push.

Ahora puede seleccionar "En cualquier momento" al crear sondeos y anuncios de Reach.


[<https://developer.amazon.com/sdk/adm/credentials.html>]: https://developer.amazon.com/sdk/adm/credentials.html
[biblioteca de cliente ADM]: https://developer.amazon.com/sdk/adm/setup.html
[integrado ADM]: https://developer.amazon.com/sdk/adm/integrating-app.html
[este procedimiento]: https://developer.amazon.com/sdk/adm/integrating-app.html#Asset

<!---HONumber=AcomDC_0302_2016-->