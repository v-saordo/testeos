<properties 
	pageTitle="Tutorial de aplicación de la Tienda Windows de Smooth Streaming" 
	description="Aprenda a usar los Servicios multimedia de Azure para crear una aplicación de la Tienda Windows de C# con un control MediaElement de XML para reproducir contenido de Smooth Streaming." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/03/2016" 
	ms.author="juliako"/>



#Generación de una aplicación de la Tienda Windows de Smooth Streaming

El SDK de cliente Smooth Streaming para Windows 8 permite a los desarrolladores crear aplicaciones de la Tienda Windows que reproduzcan contenido de Smooth Streaming en directo y bajo demanda. Además de la reproducción básica de contenido de Smooth Streaming, el SDK también ofrece funciones enriquecidas como la protección de Microsoft PlayReady, la restricción del nivel de calidad, Live DVR, la conmutación de la secuencia de audio, la escucha de las actualizaciones de estado (como los cambios del nivel de calidad) y los eventos de error, entre otras. Para obtener más información acerca de las funciones compatibles, consulte las [notas de la versión](http://www.iis.net/learn/media/smooth-streaming/smooth-streaming-client-sdk-for-windows-8-release-notes). Para más información, vea [Player Framework para Windows 8](http://playerframework.codeplex.com/).

Este tutorial contiene cuatro lecciones:

1. Creación de una aplicación básica de Tienda de Smooth Streaming
2. Incorporación de una barra deslizante para controlar el progreso multimedia
3. Selección de secuencias de Smooth Streaming
4. Selección de pistas de Smooth Streaming

##Requisitos previos

- Windows 8 de 32 o 64 bits. Puede conseguir la [Evaluación de Windows 8 Enterprise](http://msdn.microsoft.com/evalcenter/jj554510.aspx) en MSDN.
- Visual Studio 2012 o Visual Studio Express 2012 (o una versión posterior). Puede obtener la versión de prueba [aquí](http://www.microsoft.com/visualstudio/11/downloads).
- [SDK de cliente Smooth Streaming de Microsoft para Windows 8](http://visualstudiogallery.msdn.microsoft.com/04423d13-3b3e-4741-a01c-1ae29e84fea6?SRC=Homehttp://visualstudiogallery.msdn.microsoft.com/04423d13-3b3e-4741-a01c-1ae29e84fea6?SRC=Home).


La solución completa de cada lección se puede descargar en las muestras de código de desarrollador de MSDN (Galería de código) (información en inglés):

- [Lección 1](http://code.msdn.microsoft.com/Smooth-Streaming-Client-0bb1471f): Un sencillo reproductor multimedia de Smooth Streaming para Windows 8 
- [Lección 2](http://code.msdn.microsoft.com/A-simple-Windows-8-Smooth-ee98f63a): Un sencillo reproductor multimedia de Smooth Streaming para Windows 8 con un control de barra deslizante 
- [Lección 3](http://code.msdn.microsoft.com/A-Windows-8-Smooth-883c3b44): Un reproductor multimedia de Smooth Streaming para Windows 8 con selección de secuencias  
- [Lección 4](http://code.msdn.microsoft.com/A-Windows-8-Smooth-aa9e4907): Un reproductor multimedia de Smooth Streaming para Windows 8 con selección de pistas

##Lección 1: Creación de una aplicación básica de Tienda de Smooth Streaming

En esta lección, creará una aplicación de Tienda Windows con un control MediaElement para reproducir contenido de Smooth Streaming. La aplicación en ejecución tiene este aspecto:

![Ejemplo de aplicación de Tienda Windows de Smooth Streaming][PlayerApplication]
 
Para obtener más información acerca del desarrollo de la aplicación de la Tienda Windows, consulte [Desarrollo de magníficas aplicaciones para Windows 8](http://msdn.microsoft.com/windows/apps/br229512.aspx). Esta lección contiene los procedimientos siguientes:

1.	Creación de un proyecto de Tienda Windows
2.	Diseño de la interfaz de usuario (XAML)
3.	Modificación del archivo de código subyacente
4.	Compilación y prueba de la aplicación

**Para crear un proyecto de Tienda Windows**

1.	Ejecute Visual Studio 2012 o posterior.
2.	En el menú **Archivo**, haga clic en **Nuevo** y, a continuación, en **Proyecto**.
3.	En el diálogo Nuevo proyecto, escriba o seleccione los valores siguientes:

Nombre|Valor
---|---
Grupo de plantillas|Instalado/Plantillas/Visual C#/Tienda Windows
Plantilla|Aplicación vacía (XAML)
Nombre|SSPlayer
Ubicación|C:\\SSTutorials
Nombre de la solución|SSPlayer
Crear directorio para la solución|(seleccionado)

4.	Haga clic en **Aceptar**.

**Para agregar una referencia al SDK de cliente Smooth Streaming**

1.	En el Explorador de soluciones, haga clic con el botón derecho en **SSPlayer** y, a continuación, haga clic en **Agregar referencia**.
2.	Escriba o seleccione los valores siguientes:

Nombre|Valor
---|---
Grupo de referencia|Windows/Extensiones
Referencia|Seleccione el SDK de cliente Smooth Streaming de Microsoft para Windows 8 y el paquete en tiempo de ejecución de Microsoft Visual C++
	
3.	Haga clic en **Aceptar**. 

Después de agregar las referencias, debe seleccionar la plataforma de destino (x64 o x86), ya que en ninguna configuración de la plataforma de la CPU funciona la incorporación de las referencias. En el Explorador de soluciones, verá una marca de advertencia amarilla en estas referencias agregadas.

**Para diseñar la interfaz de usuario del reproductor**

1.	En el Explorador de soluciones, haga doble clic en **MainPage.xaml** para abrirlo en la vista de diseño.
2.	Busque las etiquetas **&lt;Grid&gt;** y **&lt;/Grid&gt;** del archivo XAML y pegue el código siguiente entre ellas:

		<Grid.RowDefinitions>
		    <RowDefinition Height="20"/>    <!-- spacer -->
		    <RowDefinition Height="50"/>    <!-- media controls -->
		    <RowDefinition Height="100*"/>  <!-- media element -->
		    <RowDefinition Height="80*"/>   <!-- media stream and track selection -->
		    <RowDefinition Height="50"/>    <!-- status bar -->
		</Grid.RowDefinitions>
		
		<StackPanel Name="spMediaControl" Grid.Row="1" Orientation="Horizontal">
		    <TextBlock x:Name="tbSource" Text="Source :  " FontSize="16" FontWeight="Bold" VerticalAlignment="Center" />
		    <TextBox x:Name="txtMediaSource" Text="http://ecn.channel9.msdn.com/o9/content/smf/smoothcontent/elephantsdream/Elephants_Dream_1024-h264-st-aac.ism/manifest" FontSize="10" Width="700" Margin="0,4,0,10" />
		    <Button x:Name="btnSetSource" Content="Set Source" Width="111" Height="43" Click="btnSetSource_Click"/>
		    <Button x:Name="btnPlay" Content="Play" Width="111" Height="43" Click="btnPlay_Click"/>
		    <Button x:Name="btnPause" Content="Pause"  Width="111" Height="43" Click="btnPause_Click"/>
		    <Button x:Name="btnStop" Content="Stop"  Width="111" Height="43" Click="btnStop_Click"/>
		    <CheckBox x:Name="chkAutoPlay" Content="Auto Play" Height="55" Width="Auto" IsChecked="{Binding AutoPlay, ElementName=mediaElement, Mode=TwoWay}"/>
		    <CheckBox x:Name="chkMute" Content="Mute" Height="55" Width="67" IsChecked="{Binding IsMuted, ElementName=mediaElement, Mode=TwoWay}"/>
		</StackPanel>

		<StackPanel Name="spMediaElement" Grid.Row="2" Height="435" Width="1072"
		            HorizontalAlignment="Center" VerticalAlignment="Center">
		    <MediaElement x:Name="mediaElement" Height="356" Width="924" MinHeight="225"
		                  HorizontalAlignment="Center" VerticalAlignment="Center" 
		                  AudioCategory="BackgroundCapableMedia" />
		    <StackPanel Orientation="Horizontal">
		        <Slider x:Name="sliderProgress" Width="924" Height="44"
		                HorizontalAlignment="Center" VerticalAlignment="Center"
		                PointerPressed="sliderProgress_PointerPressed"/>
		        <Slider x:Name="sliderVolume" 
		                HorizontalAlignment="Right" VerticalAlignment="Center" Orientation="Vertical" 
		                Height="79" Width="148" Minimum="0" Maximum="1" StepFrequency="0.1" 
		                Value="{Binding Volume, ElementName=mediaElement, Mode=TwoWay}" 
		                ToolTipService.ToolTip="{Binding Value, RelativeSource={RelativeSource Mode=Self}}"/>
		    </StackPanel>
		</StackPanel>

		<StackPanel Name="spStatus" Grid.Row="4" Orientation="Horizontal">
		    <TextBlock x:Name="tbStatus" Text="Status :  " 
		       FontSize="16" FontWeight="Bold" VerticalAlignment="Center" HorizontalAlignment="Center" />
		    <TextBox x:Name="txtStatus" FontSize="10" Width="700" VerticalAlignment="Center"/>
		</StackPanel>

	Se usa el control MediaElement para reproducir contenido multimedia. En la lección siguiente se usará el control deslizante llamado sliderProgress para controlar el progreso multimedia.

3.	Presione **CTRL+S** para guardar el archivo.

El control MediaElement no admite contenido Smooth Streaming directamente. Para habilitar la compatibilidad con Smooth Streaming, debe registrar el controlador de esquema de bytes de Smooth Streaming por la extensión del nombre del archivo y el tipo MIME. Para el registro, debe usar el método MediaExtensionManager.RegisterByteStremHandler del espacio de nombres Windows.Media.

En este archivo XAML, algunos controladores de eventos están asociados a los controles. Debe definir dichos controladores de eventos.

**Para modificar el archivo de código subyacente**

1.	En el Explorador de soluciones, haga clic con el botón derecho en **MainPage.xaml** y, a continuación, en **Ver código**.
2.	En la parte superior del archivo, agregue la siguiente instrucción using:

		using Windows.Media;

3.	Al principio de la clase **MainPage**, agregue el miembro de datos siguiente:

		private MediaExtensionManager extensions = new MediaExtensionManager();

4.	Al principio del constructor **MainPage**, agregue las dos líneas siguientes:

		extensions.RegisterByteStreamHandler("Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", ".ism", "text/xml");
		extensions.RegisterByteStreamHandler("Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", ".ism", "application/vnd.ms-sstr+xml");
		
5.	Al final de la clase **MainPage**, pegue el código siguiente:

		#region UI Button Click Events
		private void btnPlay_Click(object sender, RoutedEventArgs e)
		{
		    mediaElement.Play();
		    txtStatus.Text = "MediaElement is playing ...";
		}
		
		private void btnPause_Click(object sender, RoutedEventArgs e)
		{
		    mediaElement.Pause();
		    txtStatus.Text = "MediaElement is paused";
		}
		
		private void btnSetSource_Click(object sender, RoutedEventArgs e)
		{
		    sliderProgress.Value = 0;
		    mediaElement.Source = new Uri(txtMediaSource.Text);
		
		    if (chkAutoPlay.IsChecked == true)
		    {
		        txtStatus.Text = "MediaElement is playing ...";
		    }
		    else
		    {
		        txtStatus.Text = "Click the Play button to play the media source.";
		    }
		}
		
		private void btnStop_Click(object sender, RoutedEventArgs e)
		{
		    mediaElement.Stop();
		    txtStatus.Text = "MediaElement is stopped";
		}
		
		private void sliderProgress_PointerPressed(object sender, PointerRoutedEventArgs e)
		{
		    txtStatus.Text = "Seek to position " + sliderProgress.Value;
		    mediaElement.Position = new TimeSpan(0, 0, (int)(sliderProgress.Value));
		}
		#endregion

	Aquí se define el controlador de eventos sliderProgress\_PointerPressed. Hay algo más que hacer para que empiece a funcionar, pero ello se tratará en la lección siguiente del tutorial.
6.	Presione **CTRL+S** para guardar el archivo.

El archivo de código subyacente finalizado tendrá un aspecto similar al siguiente:

![Vista del código en Visual Studio de la aplicación de Tienda Windows de Smooth Streaming][CodeViewPic]

**Para compilar y probar la aplicación**

1.	En el menú **COMPILAR**, haga clic en **Administrador de configuración**.
2.	Cambie **Plataforma de soluciones activa** para que coincida con la plataforma de desarrollo.
3.	Presione **F6** para compilar el proyecto. 
4.	Presione **F5** para ejecutar la aplicación.
5.	En la parte superior de la aplicación, puede usar la URL de Smooth Streaming predeterminada o escribir una diferente. 
6.	Haga clic en **Establecer origen**. Como la opción **Reproducción automática** está habilitada de forma predeterminada, el contenido multimedia se reproducirá automáticamente. Puede controlar el contenido multimedia usando los botones **Reproducir**, **Pausa** y **Detener**. Puede controlar el volumen multimedia usando el control deslizante vertical. No obstante, el control deslizante horizontal que controla el progreso multimedia no está completamente implementado todavía. 

Ha terminado la lección 1. En esta lección se usa el control MediaElement para reproducir contenido de Smooth Streaming. En la lección siguiente, agregará un control deslizante para el progreso del contenido de Smooth Streaming.


##Lección 2: Incorporación de una barra deslizante para controlar el progreso multimedia
En la lección 1 ha creado una aplicación de Tienda Windows con un control XAML de MediaElement para reproducir contenido multimedia de Smooth Streaming. Esta incorpora algunas funciones multimedia básicas como iniciar, detener y poner en pausa. En esta lección, agregará un control de barra deslizante a la aplicación.

En este tutorial usaremos un temporizador para actualizar la posición del control deslizante según la posición actual del control MediaElement. Hay que actualizar las horas de inicio y fin del control deslizante en caso de que se use contenido en directo. Esto se puede controlar mejor en el evento de actualización del origen adaptivo.

Los orígenes multimedia son objetos que generan datos multimedia. La resolución del origen usa una URL o una secuencia de bytes y crea el origen multimedia adecuado para el contenido en cuestión. La resolución del origen es la forma estándar que tienen las aplicaciones para crear los orígenes multimedia.

Esta lección contiene los procedimientos siguientes:

1.	Registro del controlador de Smooth Streaming 
2.	Incorporación de los controladores de eventos de nivel de administrador de origen adaptativo
3.	Incorporación de los controladores de eventos de nivel de origen adaptativo
4.	Incorporación de controladores de eventos MediaElement
5.	Incorporación de código relacionado con la barra deslizante
6.	Compilación y prueba de la aplicación

**Para registrar el controlador de esquema de bytes de Smooth Streaming y pasar el conjunto de propiedades**

1.	En el Explorador de soluciones, haga clic con el botón derecho en **MainPage.xaml** y, a continuación, en **Ver código**.
2.	Al principio del archivo, agregue las siguientes instrucciones using:

		using Microsoft.Media.AdaptiveStreaming;

3.	Al principio de la clase MainPage, agregue los miembros de datos siguientes:

		private Windows.Foundation.Collections.PropertySet propertySet = new Windows.Foundation.Collections.PropertySet();             
		private IAdaptiveSourceManager adaptiveSourceManager;
	
4.	Dentro del constructor **MainPage**, agregue el código siguiente después de la línea **this.Initialize Components();** y las líneas del código de registro escritas en la lección anterior:
	
		// Gets the default instance of AdaptiveSourceManager which manages Smooth 
		//Streaming media sources.
		adaptiveSourceManager = AdaptiveSourceManager.GetDefault();
		// Sets property key value to AdaptiveSourceManager default instance.
		// {A5CE1DE8-1D00-427B-ACEF-FB9A3C93DE2D}" must be hardcoded.
		propertySet["{A5CE1DE8-1D00-427B-ACEF-FB9A3C93DE2D}"] = adaptiveSourceManager;
	
5.	Dentro del constructor **MainPage**, modifique los dos métodos RegisterByteStreamHandler para agregar los parámetros forth:

		// Registers Smooth Streaming byte-stream handler for ".ism" extension and, 
		// "text/xml" and "application/vnd.ms-ss" mime-types and pass the propertyset. 
		// http://*.ism/manifest URI resources will be resolved by Byte-stream handler.
		extensions.RegisterByteStreamHandler(
		    "Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", 
		    ".ism", 
		    "text/xml", 
		    propertySet );
		extensions.RegisterByteStreamHandler(
		    "Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", 
		    ".ism", 
		    "application/vnd.ms-sstr+xml", 
		propertySet);

6.	Presione **CTRL+S** para guardar el archivo.

**Para agregar el controlador de eventos de nivel de administrador de orígenes adaptativo**

1.	En el Explorador de soluciones, haga clic con el botón derecho en **MainPage.xaml** y, a continuación, en **Ver código**.
2.	Dentro de la clase **MainPage**, agregue el siguiente miembro de datos:

		private AdaptiveSource adaptiveSource = null;

3.	Al final de la clase **MainPage**, agregue el siguiente controlador de eventos:
	
		#region Adaptive Source Manager Level Events
		private void mediaElement_AdaptiveSourceOpened(AdaptiveSource sender, AdaptiveSourceOpenedEventArgs args)
		{
		    adaptiveSource = args.AdaptiveSource;
		}
		#endregion Adaptive Source Manager Level Events

4.	Al final del constructor **MainPage**, agregue la línea siguiente para suscribirse al evento abierto de origen adaptativo:
	
	adaptiveSourceManager.AdaptiveSourceOpenedEvent += new AdaptiveSourceOpenedEventHandler(mediaElement\_AdaptiveSourceOpened);

5.	Presione **CTRL+S** para guardar el archivo.

**Para incorporar los controladores de eventos de nivel de origen adaptativo**

1.	En el Explorador de soluciones, haga clic con el botón derecho en **MainPage.xaml** y, a continuación, en **Ver código**.
2.	Dentro de la clase **MainPage**, agregue el siguiente miembro de datos:
	
		private AdaptiveSourceStatusUpdatedEventArgs adaptiveSourceStatusUpdate; 
		private Manifest manifestObject;
	
3.	Al final de la clase **MainPage**, agregue los siguientes controladores de eventos:

		#region Adaptive Source Level Events
		private void mediaElement_ManifestReady(AdaptiveSource sender, ManifestReadyEventArgs args)
		{
		    adaptiveSource = args.AdaptiveSource;
		    manifestObject = args.AdaptiveSource.Manifest;
		}
		
		private void mediaElement_AdaptiveSourceStatusUpdated(AdaptiveSource sender, AdaptiveSourceStatusUpdatedEventArgs args)
		{
		    adaptiveSourceStatusUpdate = args;
		}
		
		private void mediaElement_AdaptiveSourceFailed(AdaptiveSource sender, AdaptiveSourceFailedEventArgs args)
		{
		    txtStatus.Text = "Error: " + args.HttpResponse;
		}
		#endregion Adaptive Source Level Events

4.	Al final del método **mediaElement AdaptiveSourceOpened**, agregue el código siguiente para suscribirse a los eventos:
	
		adaptiveSource.ManifestReadyEvent +=
	                mediaElement_ManifestReady;
	    adaptiveSource.AdaptiveSourceStatusUpdatedEvent += 
		    mediaElement_AdaptiveSourceStatusUpdated;
		adaptiveSource.AdaptiveSourceFailedEvent += 
		    mediaElement_AdaptiveSourceFailed;
	
5.	Presione **CTRL+S** para guardar el archivo.

También están disponibles los mismos eventos en el nivel de administrador de origen adaptativo, que se pueden usar para administrar la funcionalidad común a todos los elementos multimedia de la aplicación. Cada AdaptiveSource incluye sus propios eventos y todos los eventos AdaptiveSource se aplicarán en cascada debajo de AdaptiveSourceManager.

**Para agregar controladores de eventos de elementos multimedia**

1.	En el Explorador de soluciones, haga clic con el botón derecho en **MainPage.xaml** y, a continuación, en **Ver código**.
2.	Al final de la clase **MainPage**, agregue los siguientes controladores de eventos:
	
		#region Media Element Event Handlers 
		private void MediaOpened(object sender, RoutedEventArgs e)
		{
		    txtStatus.Text = "MediaElement opened";
		}
		
		private void MediaFailed(object sender, ExceptionRoutedEventArgs e)
		{
		    txtStatus.Text= "MediaElement failed: " + e.ErrorMessage;
		}
		
		private void MediaEnded(object sender, RoutedEventArgs e)
		{
		    txtStatus.Text ="MediaElement ended.";
		}
		#endregion Media Element Event Handlers

3.	Al final del constructor **MainPage**, agregue el código siguiente para suscribirse a los eventos:
	
		mediaElement.MediaOpened += MediaOpened;
		mediaElement.MediaEnded += MediaEnded;
		mediaElement.MediaFailed += MediaFailed;

4.	Presione **CTRL+S** para guardar el archivo.

**Para agregar código relacionado con la barra deslizante**

1.	En el Explorador de soluciones, haga clic con el botón derecho en **MainPage.xaml** y, a continuación, en **Ver código**.
2.	Al principio del archivo, agregue las siguientes instrucciones using:
	
		using Windows.UI.Core;

3.	Dentro de la clase **MainPage**, agregue los siguientes miembros de datos:
	
		public static CoreDispatcher _dispatcher;
		private DispatcherTimer sliderPositionUpdateDispatcher;
	
4.	Al final del constructor **MainPage**, agregue el código siguiente:

		_dispatcher = Window.Current.Dispatcher;
		PointerEventHandler pointerpressedhandler = new PointerEventHandler(sliderProgress_PointerPressed);
		sliderProgress.AddHandler(Control.PointerPressedEvent, pointerpressedhandler, true);    
	
5.	Al final de la clase **MainPage**, agregue el código siguiente:
	
		#region sliderMediaPlayer
		private double SliderFrequency(TimeSpan timevalue)
		{
		    long absvalue = 0;
		    double stepfrequency = -1;
		
		    if (manifestObject != null)
		    {
		        absvalue = manifestObject.Duration - (long)manifestObject.StartTime;
		    }
		    else
		    {
		        absvalue = mediaElement.NaturalDuration.TimeSpan.Ticks;
		    }
		
		    TimeSpan totalDVRDuration = new TimeSpan(absvalue);
		
		    if (totalDVRDuration.TotalMinutes >= 10 && totalDVRDuration.TotalMinutes < 30)
		    {
		       stepfrequency = 10;
		    }
		    else if (totalDVRDuration.TotalMinutes >= 30 
		             && totalDVRDuration.TotalMinutes < 60)
		    {
		        stepfrequency = 30;
		    }
		    else if (totalDVRDuration.TotalHours >= 1)
		    {
		        stepfrequency = 60;
		    }
		
		    return stepfrequency;
		}
		
		void updateSliderPositionoNTicks(object sender, object e)
		{
		    sliderProgress.Value = mediaElement.Position.TotalSeconds;
		}
		
		public void setupTimer()
		{
		    sliderPositionUpdateDispatcher = new DispatcherTimer();
		    sliderPositionUpdateDispatcher.Interval = new TimeSpan(0, 0, 0, 0, 300);
		    startTimer();
		}

		public void startTimer()
		{
		    sliderPositionUpdateDispatcher.Tick += updateSliderPositionoNTicks;
		    sliderPositionUpdateDispatcher.Start();
		}
		
		// Slider start and end time must be updated in case of live content
		public async void setSliderStartTime(long startTime)
		{
		    await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
		    {
		        TimeSpan timespan = new TimeSpan(adaptiveSourceStatusUpdate.StartTime);
		        double absvalue = (int)Math.Round(timespan.TotalSeconds, MidpointRounding.AwayFromZero);
		        sliderProgress.Minimum = absvalue;
		    });
		}
		
		// Slider start and end time must be updated in case of live content
		public async void setSliderEndTime(long startTime)
		{
		    await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
		    {
		        TimeSpan timespan = new TimeSpan(adaptiveSourceStatusUpdate.EndTime);
		        double absvalue = (int)Math.Round(timespan.TotalSeconds, MidpointRounding.AwayFromZero);
		        sliderProgress.Maximum = absvalue;
		    });
		}
		#endregion sliderMediaPlayer

	**Nota**: se usa CoreDispatcher para realizar cambios en el subproceso de interfaz de usuario desde un subproceso no perteneciente a la interfaz de usuario. En caso de cuello de botella en el subproceso de distribuidor, el desarrollador puede optar por usar el distribuidor proporcionado por el elemento de interfaz de usuario que pretende actualizar. Por ejemplo:
	
		await sliderProgress.Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () => { TimeSpan 
		  timespan = new TimeSpan(adaptiveSourceStatusUpdate.EndTime); 
		double absvalue  = (int)Math.Round(timespan.TotalSeconds, MidpointRounding.AwayFromZero); 
		  sliderProgress.Maximum = absvalue; }); 
		

6.	Al final del método **mediaElement\_AdaptiveSourceStatusUpdated**, agregue el código siguiente:
	
		setSliderStartTime(args.StartTime);
		setSliderEndTime(args.EndTime);

7.	Al final del método **MediaOpened**, agregue el código siguiente:
	
	sliderProgress.StepFrequency = SliderFrequency(mediaElement.NaturalDuration.TimeSpan); sliderProgress.Width = mediaElement.Width; setupTimer();

8.	Presione **CTRL+S** para guardar el archivo.

**Para compilar y probar la aplicación**

1. Presione **F6** para compilar el proyecto. 
2.	Presione **F5** para ejecutar la aplicación.
3.	En la parte superior de la aplicación, puede usar la URL de Smooth Streaming predeterminada o escribir una diferente. 
4.	Haga clic en **Establecer origen**. 
5.	Pruebe la barra deslizante.

Ha completado la lección 2. En esta lección ha agregado un control deslizante a la aplicación.

##Lección 3: Selección de secuencias de Smooth Streaming
Smooth Streaming es capaz de transmitir contenido con pistas de audio de varios lenguajes que los usuarios pueden seleccionar. En esta lección, podrá habilitar a los usuarios para que seleccionen las transmisiones. Esta lección contiene los procedimientos siguientes:

1. Modificación del archivo XAML
2. Modificación del archivo de código subyacente
3. Compilación y prueba de la aplicación


**Para modificar el archivo XAML**

1. En el Explorador de soluciones, haga clic con el botón derecho en **MainPage.xaml** y, a continuación, en **Diseñador de vistas**.
2. Busque &lt;Grid.RowDefinitions&gt; y modifique RowDefinitions para que tenga el aspecto siguiente:

		<Grid.RowDefinitions>            
			<RowDefinition Height="20"/>
		    <RowDefinition Height="50"/>
		    <RowDefinition Height="100"/>
		    <RowDefinition Height="80"/>
		    <RowDefinition Height="50"/>
		</Grid.RowDefinitions>

3. Dentro de las etiquetas &lt;Grid&gt;&lt;/Grid&gt;, agregue el código siguiente para definir un control de cuadro de lista, para que los usuarios puedan ver la lista de secuencias disponibles y, a continuación, seleccione las secuencias:

		<Grid Name="gridStreamAndBitrateSelection" Grid.Row="3">
			<Grid.RowDefinitions>
				<RowDefinition Height="300"/>
			</Grid.RowDefinitions>
			<Grid.ColumnDefinitions>
				<ColumnDefinition Width="250*"/>
				<ColumnDefinition Width="250*"/>
			</Grid.ColumnDefinitions>
			<StackPanel Name="spStreamSelection" Grid.Row="1" Grid.Column="0">
				<StackPanel Orientation="Horizontal">
					<TextBlock Name="tbAvailableStreams" Text="Available Streams:" FontSize="16" VerticalAlignment="Center"></TextBlock>
					<Button Name="btnChangeStreams" Content="Submit" Click="btnChangeStream_Click"/>
				</StackPanel>
			    <ListBox x:Name="lbAvailableStreams" Height="200" Width="200" Background="Transparent" HorizontalAlignment="Left" 
			    	ScrollViewer.VerticalScrollMode="Enabled" ScrollViewer.VerticalScrollBarVisibility="Visible">
					<ListBox.ItemTemplate>
						<DataTemplate>
				        	<CheckBox Content="{Binding Name}" IsChecked="{Binding isChecked, Mode=TwoWay}" />
						</DataTemplate>
					</ListBox.ItemTemplate>
				</ListBox>
			</StackPanel>
		</Grid>

4. Presione **CTRL+S** para guardar los cambios.


**Para modificar el archivo de código subyacente**

1. En el Explorador de soluciones, haga clic con el botón derecho en **MainPage.xaml** y, a continuación, en **Ver código**.
2. Dentro del espacio de nombres SSPlayer, agregue la nueva clase: #region class Stream
	
	    public class Stream
	    {
	        private IManifestStream stream;
	        public bool isCheckedValue;
	        public string name;
	
	        public string Name
	        {
	            get { return name; }
	            set { name = value; }
	        }
	
	        public IManifestStream ManifestStream
	        {
	            get { return stream; }
	            set { stream = value; }
	        }
	
	        public bool isChecked
	        {
	            get { return isCheckedValue; }
	            set
	            {
	                // mMke the video stream always checked.
	                if (stream.Type == MediaStreamType.Video)
	                {
	                    isCheckedValue = true;
	                }
	                else
	                {
	                    isCheckedValue = value;
	                }
	            }
	        }
	
	        public Stream(IManifestStream streamIn)
	        {
	            stream = streamIn;
	            name = stream.Name;
	        }
	    }
	    #endregion class Stream

3. Al principio de la clase MainPage, agregue las siguientes definiciones de variable:

		private List<Stream> availableStreams;
		private List<Stream> availableAudioStreams;
		private List<Stream> availableTextStreams;
		private List<Stream> availableVideoStreams;

4. Dentro de la clase MainPage, agregue la región siguiente:

		#region stream selection
		///<summary>
		///Functionality to select streams from IManifestStream available streams
		/// </summary>
		
		// This function is called from the mediaElement_ManifestReady event handler 
		// to retrieve the streams and populate them to the local data members.
		public void getStreams(Manifest manifestObject)
		{
		    availableStreams = new List<Stream>();
		    availableVideoStreams = new List<Stream>();
		    availableAudioStreams = new List<Stream>();
		    availableTextStreams = new List<Stream>();
		
		    try
		    {
		        for (int i = 0; i<manifestObject.AvailableStreams.Count; i++)
		        {
		            Stream newStream = new Stream(manifestObject.AvailableStreams[i]);
		            newStream.isChecked = false;
		
		            //populate the stream lists based on the types
		            availableStreams.Add(newStream);
		
		            switch (newStream.ManifestStream.Type)
		            {
		                case MediaStreamType.Video:
		                    availableVideoStreams.Add(newStream);
		                    break;
		                case MediaStreamType.Audio:
		                    availableAudioStreams.Add(newStream);
		                    break;
		                case MediaStreamType.Text:
		                    availableTextStreams.Add(newStream);
		                    break;
		            }
		
		            // Select the default selected streams from the manifest.
		            for (int j = 0; j<manifestObject.SelectedStreams.Count; j++)
		            {
		                string selectedStreamName = manifestObject.SelectedStreams[j].Name;
		                if (selectedStreamName.Equals(newStream.Name))
		                {
		                    newStream.isChecked = true;
		                    break;
		                }
		            }
		        }
		    }
		    catch (Exception e)
		    {
		        txtStatus.Text = "Error: " + e.Message;
		    }
		}
		
		// This function set the list box ItemSource
		private async void refreshAvailableStreamsListBoxItemSource()
		{
		    try
		    {
		        //update the stream check box list on the UI
		        await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, ()
		            => { lbAvailableStreams.ItemsSource = availableStreams; });
		    }
		    catch (Exception e)
		    {
		        txtStatus.Text = "Error: " + e.Message;
		    }
		}
		
		// This function creates a selected streams list
		private void createSelectedStreamsList(List<IManifestStream> selectedStreams)
		{
		    bool isOneVideoSelected = false;
		    bool isOneAudioSelected = false;
		
		    // Only one video stream can be selected
		    for (int j = 0; j<availableVideoStreams.Count; j++)
		    {
		        if (availableVideoStreams[j].isChecked && (!isOneVideoSelected))
		        {
		            selectedStreams.Add(availableVideoStreams[j].ManifestStream);
		            isOneVideoSelected = true;
		        }
		    }
		
		    // Select the frist video stream from the list if no video stream is selected
		    if (!isOneVideoSelected)
		    {
		        availableVideoStreams[0].isChecked = true;
		        selectedStreams.Add(availableVideoStreams[0].ManifestStream);
		    }
		
		    // Only one audio stream can be selected
		    for (int j = 0; j<availableAudioStreams.Count; j++)
		    {
		        if (availableAudioStreams[j].isChecked && (!isOneAudioSelected))
		        {
		            selectedStreams.Add(availableAudioStreams[j].ManifestStream);
		            isOneAudioSelected = true;
		            txtStatus.Text = "The audio stream is changed to " + availableAudioStreams[j].ManifestStream.Name;
		        }
		    }
		
		    // Select the frist audio stream from the list if no audio steam is selected.
		    if (!isOneAudioSelected)
		    {
		        availableAudioStreams[0].isChecked = true;
		        selectedStreams.Add(availableAudioStreams[0].ManifestStream);
		    }
		
		    // Multiple text streams are supported.
		    for (int j = 0; j < availableTextStreams.Count; j++)
		    {
		        if (availableTextStreams[j].isChecked)
		        {
		            selectedStreams.Add(availableTextStreams[j].ManifestStream);
		        }
		    }
		}
		
		// Change streams on a smooth streaming presentation with multiple video streams.
		private async void changeStreams(List<IManifestStream> selectStreams)
		{
		    try
		    {
		        IReadOnlyList<IStreamChangedResult> returnArgs =
		            await manifestObject.SelectStreamsAsync(selectStreams);
		    }
		    catch (Exception e)
		    {
		        txtStatus.Text = "Error: " + e.Message;
		    }
		}
		#endregion stream selection

5. Busque el método mediaElement\_ManifestReady y anexe el código siguiente al final de la función:
	
		getStreams(manifestObject);
        refreshAvailableStreamsListBoxItemSource();

	Así, cuando el manifiesto MediaElement está listo, el código obtiene una lista de secuencias disponibles y rellena el cuadro de lista de la interfaz de usuario con la lista.

6. Dentro de la clase MainPage, busque la región de eventos de clic de los botones de interfaz de usuario y, a continuación, agregue la siguiente definición de función:

		private void btnChangeStream_Click(object sender, RoutedEventArgs e)
        {
            List<IManifestStream> selectedStreams = new List<IManifestStream>();

            // Create a list of the selected streams
            createSelectedStreamsList(selectedStreams);

            // Change streams on the presentation
            changeStreams(selectedStreams);
        }

**Para compilar y probar la aplicación**

1. Presione **F6** para compilar el proyecto. 
2.	Presione **F5** para ejecutar la aplicación.
3.	En la parte superior de la aplicación, puede usar la URL de Smooth Streaming predeterminada o escribir una diferente. 
4.	Haga clic en **Establecer origen**. 
5.	El idioma predeterminado es audio\_eng. Intente cambiar entre audio\_eng y audio\_es. Cada vez que seleccione una nueva secuencia, debe hacer clic en el botón Enviar.

Ha completado la lección 3. En esta lección ha agregado la funcionalidad para elegir secuencias.

##Lección 4: Selección de pistas de Smooth Streaming
Una presentación de Smooth Streaming puede contener varios archivos de vídeo codificados con diferentes niveles de calidad (velocidad de bits) y resoluciones. En esta lección habilitará a los usuarios para que puedan seleccionar las pistas. Esta lección contiene los procedimientos siguientes:

1. Modificación del archivo XAML
2. Modificación del archivo de código subyacente
3. Compilación y prueba de la aplicación

**Para modificar el archivo XAML**

1. En el Explorador de soluciones, haga clic con el botón derecho en **MainPage.xaml** y, a continuación, en **Diseñador de vistas**.
2. Busque la etiqueta &lt;Grid&gt; cuyo nombre es **gridStreamAndBitrateSelection** y anexe el código siguiente al final de la etiqueta:

		<StackPanel Name="spBitRateSelection" Grid.Row="1" Grid.Column="1">
		 <StackPanel Orientation="Horizontal">
		     <TextBlock Name="tbBitRate" Text="Available Bitrates:" FontSize="16" VerticalAlignment="Center"/>
		     <Button Name="btnChangeTracks" Content="Submit" Click="btnChangeTrack_Click" />
		 </StackPanel>
		 <ListBox x:Name="lbAvailableVideoTracks" Height="200" Width="200" Background="Transparent" HorizontalAlignment="Left" 
		          ScrollViewer.VerticalScrollMode="Enabled" ScrollViewer.VerticalScrollBarVisibility="Visible">
		     <ListBox.ItemTemplate>
		         <DataTemplate>
		             <CheckBox Content="{Binding Bitrate}" IsChecked="{Binding isChecked, Mode=TwoWay}"/>
		         </DataTemplate>
		     </ListBox.ItemTemplate>
		 </ListBox>
		</StackPanel>

3. Presione **CTRL+S** para guardar los cambios.


**Para modificar el archivo de código subyacente**

1. En el Explorador de soluciones, haga clic con el botón derecho en **MainPage.xaml** y, a continuación, en **Ver código**.
2. Dentro del espacio de nombres SSPlayer, agregue la nueva clase:
	
		#region class Track
	    public class Track
	    {
	        private IManifestTrack trackInfo;
	        public string _bitrate;
	        public bool isCheckedValue;
	
	        public IManifestTrack TrackInfo
	        {
	            get { return trackInfo; }
	            set { trackInfo = value; }
	        }
	
	        public string Bitrate
	        {
	            get { return _bitrate; }
	            set { _bitrate = value; }
	        }
	
	        public bool isChecked
	        {
	            get { return isCheckedValue; }
	            set
	            {
	                isCheckedValue = value;
	            }
	        }
	
	        public Track(IManifestTrack trackInfoIn)
	        {
	            trackInfo = trackInfoIn;
	            _bitrate = trackInfoIn.Bitrate.ToString();
	        }
	        //public Track() { }
	    }
	    #endregion class Track

3. Al principio de la clase MainPage, agregue las siguientes definiciones de variable:
	
		private List<Track> availableTracks;

4. Dentro de la clase MainPage, agregue la región siguiente:
	
		#region track selection
        /// <summary>
        /// Functionality to select video streams
        /// </summary>

        /// This Function gets the tracks for the selected video stream
        public void getTracks(Manifest manifestObject)
        {
            availableTracks = new List<Track>();

            IManifestStream videoStream = getVideoStream();
            IReadOnlyList<IManifestTrack> availableTracksLocal = videoStream.AvailableTracks;
            IReadOnlyList<IManifestTrack> selectedTracksLocal = videoStream.SelectedTracks;

            try
            {
                for (int i = 0; i < availableTracksLocal.Count; i++)
                {
                    Track thisTrack = new Track(availableTracksLocal[i]);
                    thisTrack.isChecked = true;

                    for (int j = 0; j < selectedTracksLocal.Count; j++)
                    {
                        string selectedTrackName = selectedTracksLocal[j].Bitrate.ToString();
                        if (selectedTrackName.Equals(thisTrack.Bitrate))
                        {
                            thisTrack.isChecked = true;
                            break;
                        }
                    }
                    availableTracks.Add(thisTrack);
                }
            }
            catch (Exception e)
            {
                txtStatus.Text = e.Message;
            }
        }

		// This function gets the video stream that is playing
        private IManifestStream getVideoStream()
        {
            IManifestStream videoStream = null;
            for (int i = 0; i < manifestObject.SelectedStreams.Count; i++)
            {
                if (manifestObject.SelectedStreams[i].Type == MediaStreamType.Video)
                {
                    videoStream = manifestObject.SelectedStreams[i];
                    break;
                }
            }
            return videoStream;
        }

		// This function set the UI list box control ItemSource
        private async void refreshAvailableTracksListBoxItemSource()
        {
            try
            {
                // Update the track check box list on the UI 
                await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, ()
                    => { lbAvailableVideoTracks.ItemsSource = availableTracks; });
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }        
        }

		// This function creates a list of the selected tracks.
        private void createSelectedTracksList(List<IManifestTrack> selectedTracks)
        {
            // Create a list of selected tracks
            for (int j = 0; j < availableTracks.Count; j++)
            {
                if (availableTracks[j].isCheckedValue == true)
                {
                    selectedTracks.Add(availableTracks[j].TrackInfo);
                }
            }
        }

		// This function selects the tracks based on user selection 
        private void changeTracks(List<IManifestTrack> selectedTracks)
        {
            IManifestStream videoStream = getVideoStream();
            try
            {
                videoStream.SelectTracks(selectedTracks);
            }
            catch (Exception ex)
            {
                txtStatus.Text = ex.Message;
            }
        }
        #endregion track selection

5. Busque el método mediaElement\_ManifestReady y anexe el código siguiente al final de la función:

		getTracks(manifestObject);
		refreshAvailableTracksListBoxItemSource();

6. Dentro de la clase MainPage, busque la región de eventos de clic de los botones de interfaz de usuario y, a continuación, agregue la siguiente definición de función:

		private void btnChangeStream_Click(object sender, RoutedEventArgs e)
        {
            List<IManifestStream> selectedStreams = new List<IManifestStream>();

            // Create a list of the selected streams
            createSelectedStreamsList(selectedStreams);

            // Change streams on the presentation
            changeStreams(selectedStreams);
        }

**Para compilar y probar la aplicación**

1. Presione **F6** para compilar el proyecto. 
2.	Presione **F5** para ejecutar la aplicación.
3.	En la parte superior de la aplicación, puede usar la URL de Smooth Streaming predeterminada o escribir una diferente. 
4.	Haga clic en **Establecer origen**. 
5.	De forma predeterminada, todas las pistas de la secuencia de vídeo están seleccionadas. Para ver el cambio de velocidad de bits, puede seleccionar la menor velocidad de bits disponible y, a continuación, la mayor. Debe hacer clic en Submit después de realizar cada uno de los cambios. Puede ver los cambios de calidad del vídeo.

Ha completado la lección 4. En esta lección ha agregado la funcionalidad para elegir pistas.


##Rutas de aprendizaje de Servicios multimedia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Envío de comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##Otros recursos:
- [Creación de una aplicación JavaScript de Smooth Streaming para Windows 8 con características avanzadas](http://blogs.iis.net/cenkd/archive/2012/08/10/how-to-build-a-smooth-streaming-windows-8-javascript-application-with-advanced-features.aspx)
- [Información técnica sobre Smooth Streaming](http://www.iis.net/learn/media/on-demand-smooth-streaming/smooth-streaming-technical-overview)

[PlayerApplication]: ./media/media-services-build-smooth-streaming-apps/SSClientWin8-1.png
[CodeViewPic]: ./media/media-services-build-smooth-streaming-apps/SSClientWin8-2.png
 

<!---HONumber=AcomDC_0211_2016-->