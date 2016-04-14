<properties
	pageTitle="Agregar notificaciones push a la aplicación de Apache Cordova con las aplicaciones móviles de Azure | Servicio de aplicaciones de Azure"
	description="Obtenga información acerca de cómo usar las aplicaciones móviles de Azure para enviar notificaciones push a su aplicación de Apache Cordova."
	services="app-service\mobile"
	documentationCenter="javascript"
	manager="ggailey777"
	editor=""
	authors="adrianhall"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-html"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="02/11/2016"
	ms.author="adrianha"/>

# Agregar notificaciones push a la aplicación de Apache Cordova

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## Información general

En este tutorial, agregará notificaciones push al proyecto de [inicio rápido de Apache Cordova] para que cada vez que se inserte un registro, se envíe una notificación push. Este tutorial está basado en el tutorial de [inicio rápido de Apache Cordova], que debe completar primero. Si tiene un back-end de ASP.NET y no usa el proyecto de servidor de inicio rápido descargado, debe agregar el paquete de extensión de notificaciones push al proyecto. Para obtener más información acerca de los paquetes de extensión de servidor, consulte [Trabajar con el SDK del servidor back-end de .NET para Aplicaciones móviles de Azure].

##<a name="prerequisites"></a>Requisitos previos

Este tutorial describe el desarrollo de una aplicación de Apache Cordova con Visual Studio 2015 y su ejecución en el emulador de Google Android. Además, puede completar este tutorial en otros emuladores o dispositivos y en plataformas de desarrollo diferentes.

Para completar este tutorial, necesitará lo siguiente:

* Una [cuenta de Google] con una dirección de correo electrónico verificada.
* Un equipo con [Visual Studio Community 2015] o versiones posteriores.
* [Herramientas de Visual Studio para Apache Cordova].
* Una [cuenta de Azure activa](https://azure.microsoft.com/pricing/free-trial/).
* Un proyecto de [inicio rápido de Apache Cordova] completado. Se pueden completar otros tutoriales (como el de [autenticación]) en primer lugar, pero no es necesario.
* Un dispositivo Android.

Aunque se admiten las notificaciones push en los emuladores de Android, hemos descubierto que son inestables y no le recomendamos que pruebe las notificaciones push en los emuladores.

##<a name="create-hub"></a>Creación de un centro de notificaciones

[AZURE.INCLUDE [app-service-mobile-create-notification-hub](../../includes/app-service-mobile-create-notification-hub.md)]

##<a name="enable-gcm"></a>Habilitación del servicio de mensajería en la nube de Google

Como nos estamos centrando en la plataforma de Google Android, debe habilitar el Servicio de mensajería en la nube de Google. Si nuestro objetivo fueran dispositivos iOS de Apple, habilitaría la compatibilidad para APNS. De forma similar, si nuestro objetivo fueran dispositivos de Microsoft Windows, habilitaría la compatibilidad para WNS o MPNS.

[AZURE.INCLUDE [mobile-services-enable-google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

##<a name="configure-backend"></a>Configurar el back-end de una aplicación móvil para enviar solicitudes push

[AZURE.INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

##<a name="update-service"></a>Actualización del proyecto de servidor para enviar notificaciones push

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-configure-push-google](../../includes/app-service-mobile-dotnet-backend-configure-push-google.md)]

##<a name="configure-device"></a>Configurar el dispositivo Android para la depuración USB

Antes de implementar su aplicación en su dispositivo Android, debe habilitar la depuración USB. Realice los pasos siguientes en su teléfono Android:

1. Vaya a **Configuración** > **Acerca del teléfono**
2. Pulse en **Número de compilación** hasta que se habilite el modo de desarrollador
3. Vuelva a **Configuración**
4. Seleccione **Opciones de desarrollador**
5. Active **Depuración USB**
6. Conecte el teléfono Android al equipo de desarrollo con un cable USB.

En las pruebas de este tutorial, usamos un Google Nexus 5X que ejecuta la versión Android 6.0 (Marshmallow). Sin embargo, las técnicas son comunes a cualquier versión moderna de Android.

##<a name="add-push-to-app"></a>Incorporación de notificaciones de inserción a la aplicación

Debe asegurarse de que su proyecto de aplicación de Apache Cordova está listo para controlar las notificaciones push.

### Instalar el complemento de inserción de Apache Cordova

Las aplicaciones de Apache Cordova no controlan el dispositivo ni las funcionalidades de red de forma nativa. Estas funcionalidades se proporcionan mediante los complementos que se publican en [npm](https://www.npmjs.com/) o en GitHub. El complemento de `phonegap-plugin-push` se usa para controlar las notificaciones push de la red.

Puede instalar el complemento de inserción push de una de estas formas:

**Desde el símbolo del sistema:**

    cordova plugin add phonegap-plugin-push

**Desde Visual Studio**:

1.  Abra el archivo `config.xml` desde el Explorador de soluciones.
2.  Haga clic en **Complementos** > **Personalizado**, seleccione **Git** como origen de la instalación y, a continuación, escriba `https://github.com/phonegap/phonegap-plugin-push` como el origen.
	
	![](./media/app-service-mobile-cordova-get-started-push/add-push-plugin.png)
	
4.  Haga clic en la flecha situada junto al origen de la instalación y, a continuación, haga clic en **Agregar**.

Ahora ya está instalado el complemento de inserción.

### Instalar Google Play Services de Android

El complemento de inserción de PhoneGap se basa en Google Play Services para las notificaciones push. Para instalarlo:

1.  Abra **Visual Studio**.
2.  Haga clic en **Herramientas** > **Android** > **Android SDK Manager**.
3.  En la carpeta Extras, active la casilla situada junto a cada SDK requerido que no esté instalado. Se requieren los siguientes paquetes:
    * Android Support Library versión 23 o posteriores
    * Android Support Repository versión 20 o posteriores
    * Google Play Services versión 27 o posteriores
    * Google Repository versión 22 o posteriores
4.  Haga clic en **Instalar paquetes**.
5.  Espere hasta que la instalación se complete.

Las bibliotecas necesarias actuales se muestran en la [documentación de instalación de phonegap-plugin-push].

### Registrar el dispositivo para inserción en el inicio

1. Agregue una llamada a **registerForPushNotifications** durante la devolución de llamada para el proceso de inicio de sesión, o bien en la parte inferior del método **onDeviceReady**:

 
		// Login to the service.
		client.login('google')
		    .then(function () {
		        // Create a table reference
		        todoItemTable = client.getTable('todoitem');
		
		        // Refresh the todoItems
		        refreshDisplay();
		
		        // Wire up the UI Event Handler for the Add Item
		        $('#add-item').submit(addItemHandler);
		        $('#refresh').on('click', refreshDisplay);
		
				// Added to register for push notifications.
		        registerForPushNotifications();
		
		    }, handleError);

	En este ejemplo se muestra la llamada **registerForPushNotifications** una vez realizada la autenticación; este procedimiento se recomienda al utilizar en la aplicación tanto las notificaciones de inserción como la autenticación.

2. Agregue el nuevo método `registerForPushNotifications()` de la siguiente forma:

	    // Register for Push Notifications.
		// Requires that phonegap-plugin-push be installed.
	    var pushRegistration = null;
	    function registerForPushNotifications() {
	        pushRegistration = PushNotification.init({
	            android: {
	                senderID: 'Your_Project_ID'
	            },
	            ios: {
	                alert: 'true',
	                badge: 'true',
	                sound: 'true'
	            },
	            wns: {
	
	            }
	        });
	
	        pushRegistration.on('registration', function (data) {
	            client.push.register('gcm', data.registrationId);
	        });
	
	        pushRegistration.on('notification', function (data, d2) {
	            alert('Push Received: ' + data.message);
	        });
	
	        pushRegistration.on('error', handleError);
	    }

3. En el código anterior, reemplace `Your_Project_ID` por el identificador de proyecto numérico de la aplicación en [Google Developer Console].

## Prueba de la aplicación con el servicio móvil publicado

Puede probar la aplicación conectando directamente un teléfono Android con un cable USB. En lugar de **Google Android Emulator**, seleccione **Dispositivo**. Visual Studio descargará la aplicación en el dispositivo y ejecutará la aplicación. Después interactuará con la aplicación en el dispositivo.

Mejore su experiencia de desarrollo. Las aplicaciones de uso compartido de la pantalla, como [Mobizen], pueden ayudarle a desarrollar una aplicación de Android mediante la proyección de su pantalla Android en un explorador web de su PC.

También puede probar la aplicación Android en el emulador de Android. No olvide agregar una cuenta de Google en el emulador.

##<a name="next-steps"></a>Pasos siguientes

* Lea acerca de los [Centros de notificaciones] para obtener información sobre las notificaciones push.
* Si aún no lo ha hecho, siga el tutorial y [agregue autenticación] a su aplicación de Apache Cordova.

<!-- URLs -->
[agregue autenticación]: app-service-mobile-cordova-get-started-users.md
[inicio rápido de Apache Cordova]: app-service-mobile-cordova-get-started.md
[autenticación]: app-service-mobile-cordova-get-started-users.md
[Trabajar con el SDK del servidor back-end de .NET para Aplicaciones móviles de Azure]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[cuenta de Google]: http://go.microsoft.com/fwlink/p/?LinkId=268302
[Google Developer Console]: https://console.developers.google.com/home/dashboard
[documentación de instalación de phonegap-plugin-push]: https://github.com/phonegap/phonegap-plugin-push/blob/master/docs/INSTALLATION.md
[Mobizen]: https://www.mobizen.com/
[Visual Studio Community 2015]: http://www.visualstudio.com/
[Herramientas de Visual Studio para Apache Cordova]: https://www.visualstudio.com/es-ES/features/cordova-vs.aspx
[Centros de notificaciones]: ../notification-hubs/notification-hubs-overview.md

<!---HONumber=AcomDC_0302_2016-->