<properties 
	pageTitle="Procedimientos de actualización del SDK de aplicaciones Windows Universal" 
	description="Procedimientos de actualización del SDK de Windows Universal Apps para Azure Mobile Engagement" 					
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

#Procedimientos de actualización del SDK de aplicaciones Windows Universal

Si ya integró una versión anterior de Engagement en la aplicación, debería tener en cuenta los siguientes puntos a la hora de actualizar el SDK.

Es posible que tenga que seguir varios procedimientos si se perdió varias versiones del SDK. Por ejemplo, si migra desde 0.10.1 a 0.11.0, primero debe seguir el procedimiento "de 0.9.0 a 0.10.1" y luego el procedimiento "de 0.10.1 a 0.11.0".

##De 3.2.0 a 3.3.0

### Recursos
Este paso se refiere solo a los recursos personalizados. Si ha personalizado los recursos proporcionados por el SDK (html, imágenes, superposición), tendrá que hacer una copia de seguridad de estos antes de actualizar y volver a aplicar la personalización en los recursos actualizados.

##De 3.1.0 a 3.2.0

### Recursos
Este paso se refiere solo a los recursos personalizados. Si ha personalizado los recursos proporcionados por el SDK (html, imágenes, superposición), tendrá que hacer una copia de seguridad de estos antes de actualizar y volver a aplicar la personalización en los recursos actualizados.

### integración de vista web.
En esta versión se introdujeron algunas mejoras para disponer de compatibilidad con distintos factores de formulario de dispositivos. Asegúrese de que la integración de las vistas web coincida con lo siguiente:

En la página XAML ():

			<WebView x:Name="engagement_notification_content" Visibility="Collapsed" Height="80" HorizontalAlignment="Right" VerticalAlignment="Top"/>
			<WebView x:Name="engagement_announcement_content" Visibility="Collapsed" HorizontalAlignment="Right" VerticalAlignment="Top"/> 

Y en el archivo .cs asociado:

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

##De 2.0.0 a 3.0.0

### Recursos
Este paso se refiere solo a los recursos personalizados. Si ha personalizado los recursos proporcionados por el SDK (html, imágenes, superposición), tendrá que hacer una copia de seguridad de estos antes de actualizar y volver a aplicar la personalización en los recursos actualizados.

##De 1.1.1 a 2.0.0

A continuación se describe cómo migrar una integración del SDK desde el servicio Capptain que ofrece Capptain SAS en una aplicación con la tecnología de Azure Mobile Engagement.

> [Azure.IMPORTANT] Capptain y Mobile Engagement no son los mismos servicios, y el procedimiento que se indica a continuación destaca únicamente cómo migrar la aplicación cliente. La migración del SDK en la aplicación NO migrará los datos desde los servidores Capptain a los servidores Mobile Engagement.

Si va a migrar desde una versión anterior, consulte el sitio web de Capptain para migrar a 1.1.1 en primer lugar luego aplique el siguiente procedimiento.

### Paquete de NuGet

Reemplace **Capptain.WindowsPhone** por el paquete Nuget **MicrosoftAzure.MobileEngagement**.

### Aplicar Mobile Engagement

El SDK usa el término `Engagement`. Debe actualizar el proyecto para que coincida con este cambio.

Debe desinstalar el paquete NuGet de Capptain actual. Tenga en cuenta que se van a quitar todos los cambios de la carpeta recursos de Capptain. Si desea conservar los archivos, haga una copia de ellos.

Después, instale el nuevo paquete NuGet de Microsoft Azure Mobile Engagement en el proyecto. Lo encontrará directamente en el [sitio web de NuGet] o aquí, en el índice. Esta acción reemplaza todos los archivos de recursos que Engagement usa y agrega el nuevo archivo DLL de Engagement a las referencias del proyecto.

Tendrá que limpiar las referencias del proyecto al eliminar de referencias del archivo DLL de Capptain. Si no lo hace, la versión de Capptain entrará en conflicto y se producirán errores.

Si tiene recursos Capptain personalizados, copie el contenido de los archivos antiguos y péguelos en los nuevos archivos de Engagement. Tenga en cuenta que se deben actualizar tanto los archivos xaml como los archivos cs.

Una vez completados estos pasos, solo tendrá que reemplazar las referencias de Capptain antiguas por las nuevas referencias de Engagement.

1. Se deben actualizar todos los espacios de nombres de Capptain.

	Antes de la migración:
	
		using Capptain.Agent;
		using Capptain.Reach;
	
	Después de la migración:
	
		using Microsoft.Azure.Engagement;

2. Todas las clases de Capptain que contengan "Capptain" deberían contener "Engagement".

	Antes de la migración:
	
		public sealed partial class MainPage : CapptainPage
		{
		  protected override string GetCapptainPageName()
		  {
		    return "Capptain Demo";
		  }
		  ...
		}
	
	Después de la migración:
	
		public sealed partial class MainPage : EngagementPage
		{
		  protected override string GetEngagementPageName()
		  {
		    return "Engagement Demo";
		  }
		  ...
		}

3. En el caso de los archivos xaml, también cambian el espacio de nombres y los atributos de Capptain.

	Antes de la migración:
	
		<capptain:CapptainPage
		...
		xmlns:capptain="clr-namespace:Capptain.Agent;assembly=Capptain.Agent.WP"
		...
		</capptain:CapptainPage>
	
	Después de la migración:
	
		<engagement:EngagementPage
		...
		xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"
		...
		</engagement:EngagementPage>

4. Cambios de la página de superposición
	> [AZURE.IMPORTANT] También cambia la superposición Su espacio de nombres nuevo es `Microsoft.Azure.Engagement.Overlay`. Se debe usar tanto en los archivos xaml como los archivos cs. Además `CapptainGrid` se debe denominar `EngagementGrid`, `capptain_notification_content` y `capptain_announcement_content` se denominan `engagement_notification_content` y `engagement_announcement_content`.
	
	Para la superposición:
	
		<capptain:CapptainPageOverlay
		  xmlns:capptain="using:Capptain.Overlay"
		  ...
		</capptain:CapptainPageOverlay>
	
	Se convierte en:
	
		<EngagementPageOverlay
		  engagement="using:Microsoft.Azure.Engagement.Overlay"
		  ...
		</engagement:EngagementPageOverlay>

5. En el caso de otros recursos, como imágenes Capptain y archivos HTML, tenga en cuenta que su nombre también habrá cambiado para usar "Engagement".

### Declaración del proyecto

En Package.appxmanifest `File Type Associations` se ha actualizado de:

 -   capptain\_reach\_content a engagement\_reach\_content
 -   capptain\_log\_file a engagement\_log\_file

### Identificador de aplicación o clave SDK

Engagement usa una cadena de conexión. No es necesario especificar un identificador de aplicación y una clave SDK con Mobile Engagement, solo tiene que especificar una cadena de conexión. Esta se puede configurar en el archivo EngagementConfiguration.

La configuración de Engagement se puede establecer en el archivo `Resources\EngagementConfiguration.xml` del proyecto.

Edite este archivo para especificar lo siguiente:

-   La cadena de conexión de la aplicación entre las etiquetas `<connectionString>` y `<\connectionString>`.

Si desea especificarla en tiempo de ejecución, puede llamar al método siguiente antes de la inicialización del agente de Engagement:

	/* Engagement configuration. */
	EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
	engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
	
	/* Initialize Engagement agent with above configuration. */
	EngagementAgent.Instance.Init(args, engagementConfiguration);

La cadena de conexión de la aplicación se muestra en el Portal de Azure clásico.

### Cambio de nombre de elementos

Todos los elementos con el nombre *capptain* han cambiado a *engagement*. De forma similar, *Capptain* cambió a *Engagement*.

Ejemplos de elementos Capptain que se usan habitualmente:

-   CapptainConfiguration ahora se llama EngagementConfiguration
-   CapptainAgent ahora se llama EngagementAgent
-   CapptainReach ahora se llama EngagementReach
-   CapptainHttpConfig ahora se llama EngagementHttpConfig
-   GetCapptainPageName ahora se llama GetEngagementPageName

Tenga en cuenta que el cambio de nombre también afecta a los métodos invalidados.

 

<!---HONumber=AcomDC_0302_2016-->