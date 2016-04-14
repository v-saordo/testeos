<properties 
	pageTitle="Integración del SDK de cobertura de Windows Phone Silverlight" 
	description="Cómo integrar Cobertura de Azure Mobile Engagement con aplicaciones Windows Phone Silverlight" 					
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="na" 
	ms.topic="article"
	ms.date="07/07/2015" 
	ms.author="piyushjo" />

#Integración del SDK de cobertura de Windows Phone Silverlight

Debe seguir el procedimiento de integración descrito en el documento [Integración del SDK de Windows Phone Silverlight Engagement](mobile-engagement-windows-phone-integrate-engagement.md) antes de seguir con esta guía.

##Integrar el SDK de Engagement Reach en el proyecto de Windows Phone Silverlight

No hace falta agregar nada. Las referencias `EngagementReach` y los recursos de ya están en el proyecto.

> [AZURE.TIP]Puede personalizar las imágenes que se encuentran en la carpeta `Resources` del proyecto, especialmente el icono de marca (el valor predeterminado para el icono Engagement).

##Agregar las capacidades

El SDK de Engagement Reach necesita algunas capacidades adicionales.

Abra el archivo `WMAppManifest.xml` y asegúrese de que en el panel se han declarado las siguientes capacidades:

-   `ID_CAP_PUSH_NOTIFICATION`
-   `ID_CAP_WEBBROWSERCOMPONENT`

El primero es usado por el servicio de MPNS para permitir la visualización de la notificación del sistema. La segunda se usa para insertar una tarea de explorador en el SDK.

Edite el archivo `WMAppManifest.xml` y agréguelo dentro de la etiqueta `<Capabilities />`:

	<Capability Name="ID_CAP_PUSH_NOTIFICATION" />
	<Capability Name="ID_CAP_WEBBROWSERCOMPONENT" />

##Habilitar el Servicio de notificaciones push de Microsoft

Para poder usar el **Servicio de notificaciones de inserción de Microsoft** (conocido como MPNS), el archivo `WMAppManifest.xml` debe tener una etiqueta `<App />` con un atributo `Publisher` establecido en el nombre del proyecto.

##Inicializar el SDK de Engagement Reach

### Configuración de Engagement

La configuración de Engagement se centraliza en el archivo `Resources\EngagementConfiguration.xml` del proyecto.

Edite este archivo para especificar la configuración de Reach:

-   *Opcional*, indique si se debe activar la inserción nativa (MPNS) o no entre las etiquetas `<enableNativePush>`y `</enableNativePush>`, (`true` de forma predeterminada).
-   *Opcional*, indique el nombre del canal de inserción entre las etiquetas `<channelName>` y `</channelName>` y proporcione el mismo nombre que usa la aplicación o deje el valor en blanco.

Si, en cambio, quiere especificarlo en tiempo de ejecución, puede llamar al método siguiente antes de la inicialización del agente de Engagement:

	/* Engagement configuration. */
	EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
	
	engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
	
	engagementConfiguration.Reach.EnableNativePush = true;                  
	/* [Optional] whether the native push (MPNS) is activated or not. */
	
	engagementConfiguration.Reach.ChannelName = "YOUR_PUSH_CHANNEL_NAME";   
	/* [Optional] Provide the same channel name that your application may currently use. */
	
	/* Initialize Engagement agent with above configuration. */
	EngagementAgent.Instance.Init(engagementConfiguration);

> [AZURE.TIP]Puede especificar el nombre del canal de inserción de MPNS de la aplicación. De forma predeterminada, Engagement crea un nombre basado en el appId. No hace falta que especifique el nombre usted mismo, excepto si tiene previsto usar el canal de inserción fuera de Engagement.

### Inicialización de Engagement

Modifique `App.xaml.cs`:

-   Agregue las instrucciones `using`:

		using Microsoft.Azure.Engagement;

-   Inserte `EngagementReach.Instance.Init` justo después de `EngagementAgent.Instance.Init` en `Application_Launching`:

		private void Application_Launching(object sender, LaunchingEventArgs e)
		{
		   EngagementAgent.Instance.Init();
		   EngagementReach.Instance.Init();
		}

-   Inserte `EngagementReach.Instance.OnActivated` en el método `Application_Activated`:

		private void Application_Activated(object sender, ActivatedEventArgs e)
		{
		   EngagementAgent.Instance.OnActivated(e);
		   EngagementReach.Instance.OnActivated(e);
		}

> [AZURE.IMPORTANT]`EngagementReach.Instance.Init` se ejecuta en un subproceso dedicado. No es necesario que lo haga usted.

##Consideraciones de envío de la tienda de aplicaciones

Microsoft impone algunas reglas al usar las notificaciones de inserción:

De la documentación sobre las [directivas de aplicación] de Microsoft, sección 2.9:

1) Debe pedir al usuario que acepte recibir notificaciones de inserción. A continuación, en la configuración, agregue una manera de deshabilitar las notificaciones de inserción.

En el objeto EngagementReach se proporcionan dos métodos para administrar la participación o no participación, `EnableNativePush()` y `DisableNativePush()`. Por ejemplo, podría crear una opción en la configuración con un control que permite deshabilitar o habilitar MPNS.

También puede decidir desactivar MPNS a través de la configuración de Engagement<windows-phone-sdk-reach-configuration>.

> 2\.9.1) La aplicación primero debe describir las notificaciones que se proporcionarán y **obtener el permiso explícito del usuario (participar)**. Además, **debe proporcionar un mecanismo a través del cual el usuario puede optar por dejar de recibir las notificaciones de inserción**. Todas las notificaciones que se proporcionan mediante el Servicio de notificaciones de inserción de Microsoft deben ser coherentes con la descripción que se proporciona al usuario y deben cumplir con todas las [directivas de aplicación][Content Policies] y [requisitos adicionales para tipos específicos de aplicación vigentes].

2) No debería usar demasiadas notificaciones de inserción. Engagement controlará las notificaciones por usted.

> 2\.9.2) La aplicación y el uso que hace del Servicio de notificaciones de inserción de Microsoft no deben usar excesivamente la capacidad de la red o el ancho de banda del Servicio de notificaciones de inserción de Microsoft. Tampoco deben sobrecargar innecesariamente un dispositivo Windows Phone u otro dispositivo o servicio de Microsoft con notificaciones de inserción excesivas, según determine Microsoft bajo su razonable criterio, ni dañar las redes o los servidores de Microsoft, los servidores de terceros o las redes conectadas al Servicio de notificaciones de inserción de Microsoft, ni interferir con ellos.

3) No dependa de MPNS para enviar información crítica. Engagement usa MPNS, por lo que esta regla también se aplica a las campañas que se crean en el front-end de Engagement.

> 2\.9.3) El Servicio de notificaciones de inserción de Microsoft no se puede usar para enviar notificaciones de misión crítica o que de otro modo podrían afectar a asuntos de vida o muerte, incluidas, sin limitación, las notificaciones críticas relacionadas con un dispositivo o condición médicos. MICROSOFT RENUNCIA A TODA GARANTÍA QUE EL USO DEL SERVICIO DE NOTIFICACIONES PUSH DE MICROSOFT O LA ENTREGA DE NOTIFICACIONES DEL SERVICIO DE NOTIFICACIONES PUSH DE MICROSOFT SE HARÁN SIN INTERRUPCIONES, SEAN LIBRE DE ERRORES O DE OTRO MODO SE GARANTICE QUE SE PRODUCIRÁN EN TIEMPO REAL.

**No podemos garantizar que la aplicación aprobará el proceso de validación si no se respetan estas recomendaciones.**

##Controlar la inserción de datos (opcional)

Si desea que la aplicación pueda recibir inserciones de datos Reach, debe implementar dos eventos de la clase EngagementReach:

	EngagementReach.Instance.DataPushStringReceived += (body) =>
	{
	   Debug.WriteLine("String data push message received: " + body);
	   return true;
	};
	
	EngagementReach.Instance.DataPushBase64Received += (decodedBody, encodedBody) =>
	{
	   Debug.WriteLine("Base64 data push message received: " + encodedBody);
	   // Do something useful with decodedBody like updating an image view
	   return true;
	};

Puede ver que la devolución de llamada de cada método devuelve un valor booleano. Engagement envía un comentario a su back-end después de enviar la inserción de datos. Si la devolución de llamada devuelve un valor falso, se enviará el comentario `exit`. De lo contrario, será `action`. Si no se establece ninguna devolución de llamada para los eventos, el comentario `drop` se devolverá a Engagement.

> [AZURE.WARNING]Engagement no puede recibir varios comentarios para la inserción de datos. Si tiene previsto establecer varios controladores en un evento, tenga en cuenta que los comentarios corresponderán al último controlador enviado. En este caso, es recomendable devolver siempre el mismo valor para evitar tener comentarios confusos en el front-end.

##Personalizar la interfaz de usuario (opcional)

### Primer paso

Puede personalizar la interfaz de usuario de Reach.

Para ello, debe crear una subclase de la clase `EngagementReachHandler`.

**Código de ejemplo:**

	using Microsoft.Azure.Engagement;
	
	namespace Example
	{
	   internal class ExampleReachHandler : EngagementReachHandler
	   {
	      // Override EngagementReachHandler methods depending on your needs
	   }
	}

A continuación, establezca el contenido del campo `EngagementReach.Instance.Handler` con el objeto personalizado en la clase `App.xaml.cs` del método `Application_Launching`.

**Código de ejemplo:**

	private void Application_Launching(object sender, LaunchingEventArgs e)
	{
	   // your app initialization 
	   EngagementReach.Instance.Handler = new ExampleReachHandler();
	   // Engagement Agent and Reach initialization
	}

> [AZURE.NOTE]De forma predeterminada, Engagement usa su propia implementación de `EngagementReachHandler`. No hace falta crear elementos propios. Si lo hace, tiene que invalidar todos los métodos. El comportamiento predeterminado consiste en seleccionar el objeto base de Engagement.

### Diseños

De forma predeterminada, Reach usará los recursos integrados del archivo DLL para mostrar las notificaciones y páginas.

Sin embargo, puede decidir usar sus propios recursos para reflejar su marca en estos componentes.

Puede invalidar los métodos `EngagementReachHandler` en la subclase para indicar a Engagement que debe usar sus diseños:

**Código de ejemplo:**

	// In your subclass of EngagementReachHandler
	
	public override string GetTextViewAnnouncementUri()
	{
	   // return the path of your own xaml
	}
	
	public override string GetWebViewAnnouncementUri()
	{
	   // return the path of your own xaml
	}
	
	public override string GetPollUri()
	{
	   // return the path of your own xaml
	}
	
	public override EngagementNotificationView CreateNotification(EngagementNotificationViewModel viewModel)
	{
	   // return a new instance of your own notification
	}

> [AZURE.TIP]El método `CreateNotification` puede devolver un valor nulo. La notificación no se mostrará y se quitará la campaña de Reach.

Para simplificar la implementación del diseño, también proporcionamos nuestro propio xaml que puede servir como base para el código. Estos códigos se encuentran en el archivo de SDK de Engagement (/src/reach/).

> [AZURE.WARNING]Los orígenes que proporcionamos son los mismos que usamos. Por lo tanto, si desea modificarlos directamente, no olvide cambiar el espacio de nombres y el nombre.

### Posición de notificación

De forma predeterminada, una notificación de la aplicación se muestra en la parte inferior izquierda de la aplicación. Puede cambiar este comportamiento al invalidar el método `GetNotificationPosition` del objeto `EngagementReachHandler`.

	// In your subclass of EngagementReachHandler
	
	public override EngagementReachHandler.NotificationPosition GetNotificationPosition(EngagementNotificationViewModel viewModel)
	{
	   // return a value of the EngagementReachHandler.NotificationPosition enum (TOP or BOTTOM)
	}

Actualmente, puede elegir entre las posiciones `BOTTOM` (valor predeterminado) y `TOP`.

### Iniciar el mensaje

Cuando un usuario hace clic en una notificación del sistema, Engagement inicia la aplicación, carga el contenido de los mensajes de inserción y muestra la página de la campaña correspondiente.

Hay un retraso entre el inicio de la aplicación y la presentación de la página (según la velocidad de la red).

Para indicar al usuario que algo se está cargando, debería proporcionar algún tipo de información visual, tal como una barra de progreso o un indicador de progreso. Engagement no puede controlar esto por sí solo, pero le ofrece algunos controladores para ello.

Para implementar la devolución de llamada, haga lo siguiente:

	/* The application has launched and the content is loading.
	 * You should display an indicator here.
	 */
	EngagementReach.Instance.RetrieveLaunchMessageStarted += () => { [...] };
	
	/* The application has finished loading the content and the page
	 * is about to be displayed.
	 * You should hide the indicator here.
	 */
	EngagementReach.Instance.RetrieveLaunchMessageCompleted += () => { [...] };
	
	/* The content has been loaded, but an error has occurred.
	 * You can provide an information to the user.
	 * You should hide the indicator here.
	 */
	EngagementReach.Instance.RetrieveLaunchMessageFailed += () => { [...] };

Puede establecer la devolución de llamada en el método `Application_Launching` del archivo `App.xaml.cs`, preferentemente antes de la llamada `EngagementReach.Instance.Init()`.

> [AZURE.TIP]A cada controlador lo llama el subproceso de interfaz de usuario. No tiene que preocuparse cuando se usa un objeto MessageBox u otro elemento relacionado con la interfaz de usuario.

[directivas de aplicación]: http://msdn.microsoft.com/library/windows/apps/hh184841(v=vs.105).aspx
[Content Policies]: http://msdn.microsoft.com/library/windows/apps/hh184842(v=vs.105).aspx
[requisitos adicionales para tipos específicos de aplicación vigentes]: http://msdn.microsoft.com/library/windows/apps/hh184838(v=vs.105).aspx
 

<!---HONumber=Oct15_HO3-->