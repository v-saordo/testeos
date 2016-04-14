<properties 
	pageTitle="Integración del SDK de Cobertura de Windows Universal Apps" 
	description="Cómo integrar Cobertura de Azure Mobile Engagement con aplicaciones Windows Universal"
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/29/2016" 
	ms.author="piyushjo" />

# Integración del SDK de Cobertura de Windows Universal Apps

Debe seguir el procedimiento de integración descrito en el documento [Integración del SDK de Windows Universal Engagement](mobile-engagement-windows-store-integrate-engagement.md) antes de seguir con esta guía.

## Integre el SDK de Cobertura de Engagement en el proyecto de Windows Universal

No hace falta agregar nada. Las referencias y los recursos de `EngagementReach` ya están en el proyecto.

> [AZURE.TIP] Puede personalizar las imágenes que se encuentran en la carpeta `Resources` del proyecto, especialmente el icono de marca (el valor predeterminado para el icono Engagement). En las aplicaciones universales también puede mover la carpeta `Resources` del proyecto compartido para compartir su contenido entre aplicaciones, pero tendrá que mantener el archivo `Resources\EngagementConfiguration.xml` en su ubicación predeterminada debido a que depende de la plataforma.

## Habilitar el Servicio de notificaciones de Windows

### Solo Windows 8.x y Windows Phone 8.1

Para poder usar el **Servicio de notificaciones de Windows** (conocido como WNS) en el archivo `Package.appxmanifest` en `Application UI`, haga clic en `All Image Assets` en el cuadro inferior izquierdo. A la derecha del cuadro de `Notifications`, cambie `toast capable` de `(not set)` a `(Yes)`.

### Todas las plataformas

Deberá sincronizar la aplicación con su cuenta de Microsoft y la plataforma de Engagement. Para esto deberá crear una cuenta o iniciar sesión en el [Centro de desarrollo de Windows](https://dev.windows.com). Después, cree una nueva aplicación y busque el SID y la clave secreta. En el servidor front-end de Engagement, vaya a la opción de configuración de la aplicación en `native push` y pegue sus credenciales. Luego, haga clic con el botón secundario del mouse en el proyecto, seleccione `store` y `Associate App with the Store...`. Solo tiene que seleccionar la aplicación que creó antes para sincronizarla.

## Inicializar el SDK de Engagement Reach

Modifique `App.xaml.cs`:

-   Inserte `EngagementReach.Instance.Init` justo después de `EngagementAgent.Instance.Init` en su método `InitEngagement`:

		private void InitEngagement(IActivatedEventArgs e)
		{
		  EngagementAgent.Instance.Init(e);
		  EngagementReach.Instance.Init(e);
		}

	`EngagementReach.Instance.Init` se ejecuta en un subproceso dedicado. No es necesario que lo haga usted.

> [AZURE.NOTE] Si está usando las notificaciones de inserción en otra parte de la aplicación, tendrá que [compartir su canal de inserción](#push-channel-sharing) con Engagement Reach.

## Integración

Engagement proporciona dos maneras de implementar la notificación y el anuncio de Cobertura: la integración de superposición y la integración de la vista web.

La integración de superposición no requiere que se escriba mucho código en la aplicación. Simplemente tendrá que etiquetar las páginas y los archivos xaml y cs con EngagementPageOverlay. Además, si personaliza la vista predeterminada de Engagement, la personalización se compartirá con todas las páginas etiquetadas y se definirá una sola vez. Sin embargo, si las páginas tienen que heredar de un objeto que no sea EngagementPageOverlay, estará obligado a usar la integración de la vista web.

Integración de WebView es más complicada de implementar. Sin embargo, si las páginas de la aplicación deben heredar de un objeto distinto de "Page", tendrá que integrar la vista web y su comportamiento.

> [AZURE.TIP] Deberá considerar la posibilidad de agregar un elemento `<Grid></Grid>` en el nivel raíz para que incluya todo el contenido de la página. Para la integración de la vista web, simplemente agregue Webview como elemento secundario de esta cuadrícula. Si necesita establecer el componente de Engagement en otra parte, recuerde que debe administrar el tamaño de presentación usted mismo.

### Integración de superposición

Engagement proporciona una superposición para la visualización de notificaciones y anuncios.

Si desea usarlo, no use la integración de webview.

En el archivo .xaml, cambie la referencia EngagementPage a EngagementPageOverlay.

-   Agregue a las declaraciones de espacios de nombres:

		xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay"

-   Reemplace `engagement:EngagementPage` por `engagement:EngagementPageOverlay`:

**Con EngagementPage:**

		<engagement:EngagementPage 
		    xmlns:engagement="using:Microsoft.Azure.Engagement">
		
		    <!-- layout -->
		</engagement:EngagementPage>

**Con EngagementPageOverlay:**

		<engagement:EngagementPageOverlay 
		    xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay">
		
		    <!-- layout -->
		</engagement:EngagementPageOverlay>

> **Con EngagementPageOverlay para 8.1+:**

		<engagement:EngagementPageOverlay 
		    xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay">
		    <Grid>
		      <!-- layout -->
		    </Grid>
		</engagement:EngagementPageOverlay>

Luego, en el archivo .cs, etiquete la página en "EngagementPageOverlay" en lugar de "EngagementPage" e importe "Microsoft.Azure.Engagement.Overlay".

			using Microsoft.Azure.Engagement.Overlay;

-   Reemplace `EngagementPage` por `EngagementPageOverlay`:

**Con EngagementPage:**

			using Microsoft.Azure.Engagement;
			
			namespace Example
			{
			  public sealed partial class ExamplePage : EngagementPage
			  {
			    [...]
			  }
			}

**Con EngagementPageOverlay:**

			using Microsoft.Azure.Engagement.Overlay;
			
			namespace Example
			{
			  public sealed partial class ExamplePage : EngagementPageOverlay 
			  {
			    [...]
			  }
			}

Ahora esta página usa el mecanismo de superposición de Engagement. No hace falta insertar una vista web.

La superposición de Engagement usa el primer elemento "Grid" que encuentra en el archivo xaml para agregar dos vistas web en la página. Si desea localizar la ubicación en la que se establecerán las vistas web, puede definir una cuadrícula con el nombre "EngagementGrid" similar al siguiente:

			<Grid x:Name="EngagementGrid"></Grid>

Puede personalizar la notificación y el anuncio de superposición directamente en sus archivos xaml y cs:

-   `EngagementAnnouncement.html` : el diseño html de vista web `Announcement`.
-   `EngagementOverlayAnnouncement.xaml` : el diseño xaml `Announcement`.
-   `EngagementOverlayAnnouncement.xaml.cs` : el código vinculado `EngagementOverlayAnnouncement.xaml`.
-   `EngagementNotification.html` : el diseño html de vista web `Notification`.
-   `EngagementOverlayNotification.xaml` : el diseño xaml `Notification`.
-   `EngagementOverlayNotification.xaml.cs`: el código vinculado `EngagementOverlayNotification.xaml`.
-   `EngagementPageOverlay.cs`: el código de visualización de anuncios y notificaciones de `Overlay`.

### Integración de vista web

Si desea usarlo, no use la integración de superposición.

Para mostrar el contenido de Engagement, deberá integrar las dos vistas web xaml en cada página y debe mostrar la notificación y el anuncio. Por lo tanto, agregue este código al archivo xaml:

			<WebView x:Name="engagement_notification_content" Visibility="Collapsed" Height="80" HorizontalAlignment="Right" VerticalAlignment="Top"/>
			<WebView x:Name="engagement_announcement_content" Visibility="Collapsed" HorizontalAlignment="Right" VerticalAlignment="Top"/> 

> **Para la integración 8.1+:**

			<engagement:EngagementPage
			    xmlns:engagement="using:Microsoft.Azure.Engagement">
			    <Grid>
			      <!-- Your layout -->
			      <WebView x:Name="engagement_notification_content" Visibility="Collapsed" Height="80" HorizontalAlignment="Right" VerticalAlignment="Top"/>
			      <WebView x:Name="engagement_announcement_content" Visibility="Collapsed"  HorizontalAlignment="Right" VerticalAlignment="Top"/> 
			    </Grid>
			</engagement:EngagementPage>

El archivo .cs asociado deberá tener el siguiente aspecto:

    using Microsoft.Azure.Engagement;
    using System;
    using Windows.ApplicationModel.Core;
    using Windows.UI.ViewManagement;
    using Windows.UI.Xaml;
    using Windows.UI.Xaml.Navigation;

    namespace My.Namespace.Example
    {
			/// <summary>
			/// An empty page that can be used on its own or navigated to within a Frame.
			/// </summary>
			public sealed partial class ExampleEngagementReachPage : EngagementPage
			{
			  public ExampleEngagementReachPage()
			  {
			    this.InitializeComponent();
			
			    /* Set your webview elements to the correct size. */
			    SetWebView(width, height);
			  }
			
			  #region to implement
              /* Attach events when page is navigated. */
              protected override void OnNavigatedTo(NavigationEventArgs e)
              {
                /* Update the webview when the app window is resized. */
                Window.Current.SizeChanged += DisplayProperties_OrientationChanged;

                /* Update the webview when the app/status bar is resized. */
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
                ApplicationView.GetForCurrentView().VisibleBoundsChanged += DisplayProperties_VisibleBoundsChanged; 
    #endif
                base.OnNavigatedTo(e);
              }

			  /* When page is left ensure to detach SizeChanged handler. */
			  protected override void OnNavigatedFrom(NavigationEventArgs e)
			  {
			    Window.Current.SizeChanged -= DisplayProperties_OrientationChanged;
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
                ApplicationView.GetForCurrentView().VisibleBoundsChanged -= DisplayProperties_VisibleBoundsChanged;
    #endif
			    base.OnNavigatedFrom(e);
			  }
			  
			  /* "width" and "height" are the current size of your application display. */
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
			  double width = ApplicationView.GetForCurrentView().VisibleBounds.Width;
			  double height = ApplicationView.GetForCurrentView().VisibleBounds.Height;
    #else
			  double width =  Window.Current.Bounds.Width;
			  double height =  Window.Current.Bounds.Height;
    #endif
			
			  /// <summary>
			  /// Set your webview elements to the correct size.
			  /// </summary>
			  /// <param name="width">The width of your current display.</param>
			  /// <param name="height">The height of your current display.</param>
			  private void SetWebView(double width, double height)
			  {
			    #pragma warning disable 4014
			    CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal,
			            () =>
			            {
			              this.engagement_notification_content.Width = width;
			              this.engagement_announcement_content.Width = width;
			              this.engagement_announcement_content.Height = height;
			            });
			  }
			
			  /// <summary>
			  /// Handler that takes the Windows.Current.SizeChanged and indicates that webviews have to be resized.
			  /// </summary>
			  /// <param name="sender">Original event trigger.</param>
			  /// <param name="e">Window Size Changed Event arguments.</param>
			  private void DisplayProperties_OrientationChanged(object sender, Windows.UI.Core.WindowSizeChangedEventArgs e)
			  {
			    double width = e.Size.Width;
			    double height = e.Size.Height;
			
			    /* Set your webview elements to the correct size. */
			    SetWebView(width, height);
			  }

    #if WINDOWS_PHONE_APP || WINDOWS_UWP			  
			  /// <summary>
			  /// Handler that takes the ApplicationView.VisibleBoundsChanged and indicates that webviews have to be resized
			  /// </summary>
			  /// <param name="sender">The related application view.</param>
			  /// <param name="e">Related event arguments.</param>
			  private void DisplayProperties_VisibleBoundsChanged(ApplicationView sender, Object e)
			  {
			    double width = sender.VisibleBounds.Width;
			    double height = sender.VisibleBounds.Height;
			
			    /* Set your webview elements to the correct size. */
			    SetWebView(width, height);
			  }
    #endif
			  #endregion
			}
    }

> Esta implementación integró el cambio de tamaño de vista web al activarse la pantalla del dispositivo.

## Controlar la inserción de datos (opcional)

Si desea que la aplicación pueda recibir inserciones de datos Reach, debe implementar dos eventos de la clase EngagementReach:

En App.xaml.cs, en "Public App(){}", agregue lo siguiente:

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

> [AZURE.WARNING] Engagement no puede recibir varios comentarios para la inserción de datos. Si tiene previsto establecer varios controladores en un evento, tenga en cuenta que los comentarios corresponderán al último controlador enviado. En este caso, es recomendable devolver siempre el mismo valor para evitar tener comentarios confusos en el front-end.

## Personalizar la interfaz de usuario (opcional)

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

A continuación, establezca el contenido del campo `EngagementReach.Instance.Handler` con el objeto personalizado en la clase `App.xaml.cs` del método `App()`.

**Código de ejemplo:**

			protected override void OnLaunched(LaunchActivatedEventArgs args)
			{
			  // your app initialization 
			  EngagementReach.Instance.Handler = new ExampleReachHandler();
			  // Engagement Agent and Reach initialization
			}

> [AZURE.NOTE] De forma predeterminada, Engagement usa su propia implementación de `EngagementReachHandler`. No hace falta crear elementos propios. Si lo hace, tiene que invalidar todos los métodos. El comportamiento predeterminado consiste en seleccionar el objeto base de Engagement.

### Vista web

De forma predeterminada, Reach usará los recursos integrados del archivo DLL para mostrar las notificaciones y páginas.

Para proporcionar posibilidades de personalización completa, solo se usa la vista web. Si desea personalizar diseños, invalide directamente los archivos de recursos `EngagementAnnouncement.html` y `EngagementNotification.html`. Engagement necesita todo el código de `<body></body>` para que se ejecute correctamente. No obstante, puede agregar una etiqueta exterior `engagement_webview_area`.

Sin embargo, puede decidir usar sus propios recursos.

Puede invalidar los métodos `EngagementReachHandler` en la subclase para indicar a Engagement que debe usar sus diseños (asegúrese de integrar el mecanismo de Engagement):

**Código de ejemplo:**
			
			// In your subclass of EngagementReachHandler
			
			public override string GetAnnouncementHTML()
			{
			  return base.GetAnnouncementHTML();
			}
			public override string GetAnnouncementName()
			{
			  return base.GetAnnouncementName();
			}
			public override string GetNotfificationHTML()
			{
			  return base.GetNotfificationHTML();
			}
			public override string GetNotfificationName()
			{
			  return base.GetNotfificationName();
			}


De forma predeterminada, AnnouncementHTML es `ms-appx-web:///Resources/EngagementAnnouncement.html`. Representa el archivo html que diseña el contenido de un mensaje de inserción (anuncio de texto, anuncio web y anuncio de sondeo). AnnouncementName es `engagement_announcement_content`. Es el nombre del diseño de vista web de la página xaml.

NotfificationHTML es `ms-appx-web:///Resources/EngagementNotification.html`. Representa el archivo html que diseña la notificación de un mensaje de inserción. NotfificationName es `engagement_notification_content`. Es el nombre del diseño de vista web de la página xaml.

### Personalización

Puede personalizar la vista web de notificación y anuncio como desee si quiere conservar el objeto de Engagement. Asegúrese de que el objeto de vista web se describa tres veces: la primera, en el archivo xaml, la segunda en el archivo .cs del método "setwebview()" y la tercera en el archivo html.

-   En el código xaml, describa el componente webview de diseño gráfico actual.
-   En el archivo .cs, puede definir "setwebview()" en el que establecer la dimensión de las dos vistas web de dos (notificación, anuncio). Es muy eficaz al cambiar el tamaño de la aplicación.
-   En el archivo html de Engagement, se describen el contenido de la vista web, el diseño y las posiciones de los elementos entre sí.

### Iniciar el mensaje

Cuando un usuario hace clic en una notificación del sistema, Engagement inicia la aplicación, carga el contenido de los mensajes de inserción y muestra la página de la campaña correspondiente.

Hay un retraso entre el inicio de la aplicación y la presentación de la página (según la velocidad de la red).

Para indicar al usuario que algo se está cargando, debería proporcionar algún tipo de información visual, tal como una barra de progreso o un indicador de progreso. Engagement no puede controlar esto por sí solo, pero le ofrece algunos controladores para ello.

Para implementar la devolución de llamada, en App.xaml.cs, en "Public App(){}", agregue lo siguiente:

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

Puede establecer la devolución de llamada en el método "Public App(){}" del archivo `App.xaml.cs`, preferentemente antes de la llamada `EngagementReach.Instance.Init()`.

> [AZURE.TIP] A cada controlador lo llama el subproceso de interfaz de usuario. No tiene que preocuparse cuando se usa un objeto MessageBox u otro elemento relacionado con la interfaz de usuario.

##<a id="push-channel-sharing"></a> Uso compartido de canal de inserción

Si está usando las notificaciones de inserción para otra finalidad en la aplicación, tendrá que usar la característica de uso compartido del canal de inserción del SDK de Engagement. Esto es para evitar la inserción perdida.

- Puede proporcionar su propio canal de inserción a la inicialización de Engagement Reach. El SDK lo usará en lugar de solicitar uno nuevo.

Actualice la inicialización de Engagement Reach con el canal de inserción en el método de `InitEngagement` desde el archivo `App.xaml.cs`:
    
    /* Your own push channel logic... */
    var pushChannel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
    
    /*...Engagement initialization */
    EngagementAgent.Instance.Init(e);
	EngagementReach.Instance.Init(e,pushChannel);

- También, si desea usar el canal de inserción después de la inicialización de Reach, puede establecer una devolución de llamada en Engagement Reach para obtener el canal de inserción una vez creado por el SDK.

Establezca la devolución de llamada en cualquier lugar **después** de la inicialización de Reach:

    /* Set action on the SDK push channel. */
    EngagementReach.Instance.SetActionOnPushChannel((PushNotificationChannel channel) => 
    {
      /* The forwarded channel can be null if its creation fails for any reason. */
      if (channel != null)
      {
		/* Your own push channel logic... */
      });
	}

## Sugerencia de esquema personalizado

Se incluye el uso de esquemas personalizados. Puede enviar otro tipo de URI desde el front-end de Engagement para usarse en la aplicación de Engagement. Los esquemas predeterminados, como `http, ftp, ...`, los administra Windows. Una ventana pedirá información si no hay ninguna aplicación predeterminada instalada en el dispositivo. También puede crear su propio esquema personalizado para su aplicación.

Es una manera sencilla de establecer un esquema personalizado en la aplicación es abrir `Package.appxmanifest` en el panel `Declarations`. Seleccione `Protocol` del cuadro desplegable Declaraciones disponibles y agréguelo. Edite el campo `Name` con el nombre deseado del nuevo protocolo.

Ahora, para usar este protocolo, edite `App.xaml.cs` mediante el método `OnActivated` y no se olvide de inicializar Engagement aquí también:

			/// <summary>
			/// Enter point when app his called by another way than user click
			/// </summary>
			/// <param name="args">Activation args</param>
			protected override void OnActivated(IActivatedEventArgs args)
			{
			  /* Init engagement like it was launch by a custom uri scheme */
			  EngagementAgent.Instance.Init(args);
			  EngagementReach.Instance.Init(args);
			
			  //TODO design action to do when app is launch
			
			  #region Custom scheme use
			  if (args.Kind == ActivationKind.Protocol)
			  {
			    ProtocolActivatedEventArgs myProtocol = (ProtocolActivatedEventArgs)args;
			
			    if (myProtocol.Uri.Scheme.Equals("protocolName"))
			    {
			      string path = myProtocol.Uri.AbsolutePath;
			    }
			  }
			  #endregion
 

<!---HONumber=AcomDC_0302_2016-->