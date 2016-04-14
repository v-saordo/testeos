<properties
	pageTitle="Introducción a Azure Mobile Engagement para aplicaciones Windows Phone Silverlight"
	description="Aprenda a usar Azure Mobile Engagement con análisis y notificaciones de inserción para aplicaciones Windows Phone Silverlight."
	services="mobile-engagement"
	documentationCenter="windows"
	authors="piyushjo"
	manager="dwrede"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows-phone"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="02/29/2016"
	ms.author="piyushjo" />

# Introducción a Azure Mobile Engagement para aplicaciones Windows Phone Silverlight

> [AZURE.SELECTOR]
- [Windows Universal](mobile-engagement-windows-store-dotnet-get-started.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-get-started.md)
- [iOS | Obj C](mobile-engagement-ios-get-started.md)
- [iOS | Swift](mobile-engagement-ios-swift-get-started.md)
- [Android](mobile-engagement-android-get-started.md)
- [Cordova](mobile-engagement-cordova-get-started.md)

En este tema se muestra cómo usar Azure Mobile Engagement para comprender el uso que hace de las aplicaciones y enviar notificaciones de inserción a los usuarios segmentados de una aplicación de Windows Phone Silverlight. En este tutorial se demuestra el escenario de difusión sencillo con Mobile Engagement. En él, puede crear una aplicación de Windows Phone Silverlight vacía que recopila datos básicos y recibe notificaciones de inserción mediante el Servicio de notificaciones de inserción de Microsoft (MPNS).

> [AZURE.NOTE] Si va a desarrollar para Windows Phone 8.1 (no Silverlight), consulte el [tutorial Windows Universal](mobile-engagement-windows-store-dotnet-get-started.md).

Este tutorial requiere lo siguiente:

+ Visual Studio 2013
+ Paquete de Nuget [MicrosoftAzure.MobileEngagement]

> [AZURE.IMPORTANT] La finalización de este tutorial es un requisito previo de todos los demás tutoriales de Mobile Engagement para aplicaciones de Windows Phone Silverlight y, para completarlo, debe disponer de una cuenta activa de Azure. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fes-ES%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Evaluación gratuita de Azure</a>.

##<a id="setup-azme"></a>Configurar Mobile Engagement para su aplicación de Windows Phone

[AZURE.INCLUDE [Crear aplicación de Mobile Engagement en el portal](../../includes/mobile-engagement-create-app-in-portal.md)]

##<a id="connecting-app"></a>Conectar la aplicación al backend de Mobile Engagement

En este tutorial se presenta una "integración básica", que es el conjunto mínimo necesario para recopilar los datos y enviar una notificación de inserción. Toda la documentación de integración completa se encuentra en la [Integración del SDK de Windows Phone para Mobile Engagement](../mobile-engagement-windows-phone-sdk-overview/).

Crearemos una aplicación básica con Visual Studio para demostrar la integración.

###Crear un nuevo proyecto de Windows Phone Silverlight

En los siguientes pasos se supone el uso de Visual Studio de 2015 aunque los pasos son similares en versiones anteriores de Visual Studio.

1. Inicie Visual Studio y, en la pantalla **Principal**, seleccione **Nuevo proyecto**.

2. En el menú emergente, seleccione **Windows 8** -> **Windows Phone** -> **Aplicación vacía (Windows Phone Silverlight)**. Rellene el **Nombre** de la aplicación y el **Nombre de la solución** y luego haga clic en **Aceptar**.

    ![][1]

3. Puede elegir como destino la versión **Windows Phone 8.0** o **Windows Phone 8.1**.

Ahora ha creado una nueva aplicación Windows Phone Silverlight en la que se integrará el SDK de Azure Mobile Engagement.

###Conectar la aplicación al backend de Mobile Engagement

1. Instale el paquete de NuGet [MicrosoftAzure.MobileEngagement] en su proyecto.

2. Abra `WMAppManifest.xml` (en la carpeta Propiedades) y asegúrese de que se declara lo siguiente (agréguelo en caso de que no sea así) en la etiqueta `<Capabilities />`:

		<Capability Name="ID_CAP_NETWORKING" />
		<Capability Name="ID_CAP_IDENTITY_DEVICE" />

    ![][2]

3. Ahora, pegue la cadena de conexión que copió anteriormente para la aplicación Mobile Engagement y péguela en el archivo `Resources\EngagementConfiguration.xml`, entre las etiquetas `<connectionString>` y `</connectionString>`:

    ![][3]

4. En el archivo `App.xaml.cs`:

	a. Agregue la instrucción `using`:

			using Microsoft.Azure.Engagement;

	b. Inicialice el SDK en el método `Application_Launching`:

			private void Application_Launching(object sender, LaunchingEventArgs e)
			{
			  EngagementAgent.Instance.Init();
			}

	c. Inserte lo siguiente en `Application_Activated`:

			private void Application_Activated(object sender, ActivatedEventArgs e)
			{
			   EngagementAgent.Instance.OnActivated(e);
			}

##<a id="monitor"></a>Habilitación de la supervisión en tiempo real

Para comenzar a enviar datos y asegurarse de que los usuarios estén activos, debe enviar al menos una pantalla (Actividad) al back-end de Mobile Engagement.

1. En MainPage.xaml.cs, agregue la instrucción `using`:

    	using Microsoft.Azure.Engagement;

2. Para ello, reemplace la clase base de **MainPage**, que es anterior a **PhoneApplicationPage**, con **EngagementPage**.

    	class MainPage : EngagementPage 
	
3. En el archivo `MainPage.xml`:

	a. Agregue a las declaraciones de espacios de nombres:

	   	 xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"

	b. Reemplace `phone:PhoneApplicationPage` en el nombre de etiqueta XML por `engagement:EngagementPage`.

##<a id="monitor"></a>Conectar la aplicación con la supervisión en tiempo real

[AZURE.INCLUDE [Conectar la aplicación con la supervisión en tiempo real](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Habilitación de notificaciones push y mensajería en la aplicación

Mobile Engagement permite interactuar y llegar a los usuarios mediante notificaciones de inserción y mensajería en la aplicación en el contexto de las campañas. Este módulo se denomina REACH en el portal de Mobile Engagement. En las secciones siguientes se instala la aplicación para recibirlos.

###Habilitar la aplicación para recibir notificaciones de inserción de MPNS

Agregue nuevas capacidades a su archivo `WMAppManifest.xml`:

		ID_CAP_PUSH_NOTIFICATION
		ID_CAP_WEBBROWSERCOMPONENT

   ![][5]

###Inicializar el SDK de REACH

1. En `App.xaml.cs`, llame a `EngagementReach.Instance.Init();` en la función **Application\_Launching** justo después de la inicialización del agente:

		private void Application_Launching(object sender, LaunchingEventArgs e)
		{
		   EngagementAgent.Instance.Init();
		   EngagementReach.Instance.Init();
		}

2. En `App.xaml.cs`, llame a `EngagementReach.Instance.OnActivated(e);` en la función **Application\_Activated** justo después de la inicialización del agente:

		private void Application_Activated(object sender, ActivatedEventArgs e)
		{
		   EngagementAgent.Instance.OnActivated(e);
		   EngagementReach.Instance.OnActivated(e);
		}

Está listo. Ahora comprobaremos que ha llevado a cabo correctamente esta integración básica.

##<a id="send"></a>Enviar una notificación a su aplicación

[AZURE.INCLUDE [Crear campaña de inserción de Windows](../../includes/mobile-engagement-windows-push-campaign.md)]

Ahora debería ver una notificación en su dispositivo que se mostrará como una notificación de aplicación si la aplicación está abierta, en caso contrario, como una notificación del sistema similar a la siguiente:

![][6]

<!-- URLs. -->
[MicrosoftAzure.MobileEngagement]: http://go.microsoft.com/?linkid=9874664
[Mobile Engagement Windows Phone SDK documentation]: ../mobile-engagement-windows-phone-integrate-engagement/

<!-- Images. -->
[1]: ./media/mobile-engagement-windows-phone-get-started/project-properties.png
[2]: ./media/mobile-engagement-windows-phone-get-started/wmappmanifest-capabilities.png
[3]: ./media/mobile-engagement-windows-phone-get-started/add-connection-string.png
[5]: ./media/mobile-engagement-windows-phone-get-started/reach-capabilities.png
[6]: ./media/mobile-engagement-windows-phone-get-started/push-screenshot.png

<!---HONumber=AcomDC_0302_2016-->