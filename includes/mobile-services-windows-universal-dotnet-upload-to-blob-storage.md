##Instalación del cliente de almacenamiento para aplicaciones de la Tienda Windows

Para poder utilizar una SAS para cargar imágenes al almacenamiento de blobs, primero debe agregar el paquete NuGet que instala la biblioteca de clientes de almacenamiento para aplicaciones de la Tienda Windows.

1. En el **Explorador de soluciones** de Visual Studio, haga clic con el botón derecho en el nombre del proyecto y, a continuación, seleccione **Administrar paquetes de NuGet**.

2. En el panel izquierdo, seleccione la categoría **En línea**, busque `WindowsAzure.Storage`, haga clic en **Instalar** en el paquete de **Almacenamiento de Azure** y, a continuación, acepte los contratos de licencia.

  	![](./media/mobile-services-windows-universal-dotnet-upload-to-blob-storage/mobile-add-storage-nuget-package-dotnet.png)

  	Con esto se agrega la biblioteca de clientes para servicios de almacenamiento de Azure al proyecto.

A continuación, actualizará la aplicación de inicio rápido para capturar y cargar imágenes.

##Actualización de la aplicación cliente de inicio rápido para capturar y cargar imágenes

1. En Visual Studio, abra el archivo Package.appxmanifest para el proyecto de aplicación de Windows y, en la pestaña **Capacidades**, habilite las capacidades **Webcam** y **Microphone**.

   	![](./media/mobile-services-windows-universal-dotnet-upload-to-blob-storage/mobile-app-manifest-camera.png)
 
   	Esto asegura que la aplicación puede utilizar una cámara conectada al equipo. A los usuarios se les solicitará el acceso a la cámara la primera vez que se ejecuta la aplicación.

2. Repita el paso anterior para el proyecto de aplicación de Windows Phone.
 
3. En el proyecto de aplicación de Windows, abra el archivo MainPage.xaml y reemplace el elemento **StackPanel** justo después del primer elemento **QuickStartTask** con el código siguiente:

		<StackPanel Orientation="Horizontal" Margin="72,0,0,0">
            <TextBox Name="TextInput" Margin="5" MaxHeight="40" MinWidth="300"></TextBox>
            <AppBarButton Label="Photo" Icon="Camera" Name="ButtonCapture" Click="ButtonCapture_Click" />
            <AppBarButton Label="Upload" Icon="Upload" Name="ButtonSave" Click="ButtonSave_Click"/>
        </StackPanel>
        <Grid Name="captureGrid" Margin="62,0,0,0" Visibility="Collapsed" >
            <Grid.RowDefinitions>
                <RowDefinition Height="*" />
                <RowDefinition Height="Auto" />
            </Grid.RowDefinitions>
            <CaptureElement x:Name="previewElement" Grid.Row="0" Tapped="previewElement_Tapped" />
            <Image Name="imagePreview" Grid.Row="0"  />
            <StackPanel Name="captureButtons" Orientation="Horizontal" Grid.Row="1">
                <TextBlock Name="TextCapture" />
                <AppBarButton Label="Retake" Icon="Redo" Name="ButtonRetake" Click="ButtonCapture_Click" />
                <AppBarButton Label="Cancel" Icon="Cancel" Name="ButtonCancel" Click="ButtonCancel_Click" />
            </StackPanel>
        </Grid>

2. Reemplace el elemento **StackPanel** de **DataTemplate** por el siguiente código:

        <StackPanel Orientation="Vertical">
            <CheckBox Name="CheckBoxComplete" IsChecked="{Binding Complete, Mode=TwoWay}" 
                        Checked="CheckBoxComplete_Checked" Content="{Binding Text}" 
                        Margin="10,5" VerticalAlignment="Center"/>
            <Image Name="ImageUpload" Source="{Binding ImageUri, Mode=OneWay}"
                    MaxHeight="250"/>
        </StackPanel> 

   	Esto agrega una imagen a **ItemTemplate** y establece su origen de enlace como el URI de la imagen cargada en el servicio de almacenamiento de blobs.

3. En el proyecto de aplicación de Windows Phone, abra el archivo MainPage.xaml y reemplace el elemento **ButtonSave** con el código siguiente:

        <StackPanel Grid.Row ="1" Grid.Column="1"  Orientation="Horizontal">
            <AppBarButton Label="Photo" Icon="Camera" Name="ButtonCapture" 
                          Click="ButtonCapture_Click" Height="78" Width="62" />
            <AppBarButton Label="Upload" Icon="Upload" Name="ButtonSave" 
                          Click="ButtonSave_Click"/>
        </StackPanel>
        <Grid Grid.Row="2" Name="captureGrid" Grid.RowSpan="3" Grid.ColumnSpan="2" 
              Canvas.ZIndex="99" Background="{ThemeResource ApplicationPageBackgroundThemeBrush}" 
              Visibility="Collapsed">
            <Grid.RowDefinitions>
                <RowDefinition Height="*" />
                <RowDefinition Height="Auto" />
            </Grid.RowDefinitions>
            <CaptureElement Grid.Row="0" x:Name="previewElement" Tapped="previewElement_Tapped" />                    
            <Image Grid.Row="0" Name="imagePreview" Visibility="Collapsed" />
            <StackPanel Grid.Row="1" Name="captureButtons" 
                        Orientation="Horizontal" Visibility="Collapsed">
                <TextBlock Name="TextCapture" VerticalAlignment="Bottom" />
                <AppBarButton Label="Retake" Icon="Redo" Name="ButtonRetake" 
                              Click="ButtonRetake_Click" />
                <AppBarButton Label="Cancel" Icon="Cancel" Name="ButtonCancel" 
                              Click="ButtonCancel_Click" />
            </StackPanel>
        </Grid>

2. Reemplace el elemento **StackPanel** de **DataTemplate** por el siguiente código:

        <StackPanel Orientation="Vertical">
            <CheckBox Name="CheckBoxComplete" IsChecked="{Binding Complete, Mode=TwoWay}" 
                      Checked="CheckBoxComplete_Checked" Content="{Binding Text}" 
                      Margin="10,5" VerticalAlignment="Center"/>
            <Image Name="ImageUpload" Source="{Binding ImageUri, Mode=OneWay}" 
                   MaxHeight="150"/>
        </StackPanel>

4. En la carpeta DataModel compartida, abra el archivo de proyecto TodoItem.cs y agregue las siguientes propiedades a la clase TodoItem:

        [JsonProperty(PropertyName = "containerName")]
        public string ContainerName { get; set; }
		
        [JsonProperty(PropertyName = "resourceName")]
        public string ResourceName { get; set; }
		
        [JsonProperty(PropertyName = "sasQueryString")]
        public string SasQueryString { get; set; }
		
        [JsonProperty(PropertyName = "imageUri")]
        public string ImageUri { get; set; } 

3. Abra el archivo de proyecto compartido MainPage.cs y agregue las siguientes instrucciones **using**:
	
		using Windows.Media.Capture;
		using Windows.Media.MediaProperties;
		using Windows.Storage;
		using Windows.UI.Xaml.Input;
		using Microsoft.WindowsAzure.Storage.Auth;
		using Microsoft.WindowsAzure.Storage.Blob;
		using Windows.UI.Xaml.Media.Imaging;

5. Agregue el siguiente código a la clase MainPage:

        // Use a StorageFile to hold the captured image for upload.
        StorageFile media = null;
        MediaCapture cameraCapture;
        bool IsCaptureInProgress;

        private async Task CaptureImage()
        {
            // Capture a new photo or video from the device.
            cameraCapture = new MediaCapture();
            cameraCapture.Failed += cameraCapture_Failed;

            // Initialize the camera for capture.
            await cameraCapture.InitializeAsync();

            #if WINDOWS_PHONE_APP
            cameraCapture.SetPreviewRotation(VideoRotation.Clockwise90Degrees);
            cameraCapture.SetRecordRotation(VideoRotation.Clockwise90Degrees);
            #endif

            captureGrid.Visibility = Windows.UI.Xaml.Visibility.Visible;
            previewElement.Visibility = Windows.UI.Xaml.Visibility.Visible;
            previewElement.Source = cameraCapture;
            await cameraCapture.StartPreviewAsync();
        }

        private async void previewElement_Tapped(object sender, TappedRoutedEventArgs e)
        {
            // Block multiple taps.
            if (!IsCaptureInProgress)
            {                
                IsCaptureInProgress = true;

                // Create the temporary storage file.
                media = await ApplicationData.Current.LocalFolder
                    .CreateFileAsync("capture_file.jpg", CreationCollisionOption.ReplaceExisting);

                // Take the picture and store it locally as a JPEG.
                await cameraCapture.CapturePhotoToStorageFileAsync(
                    ImageEncodingProperties.CreateJpeg(), media);

                captureButtons.Visibility = Visibility.Visible;

				// Use the stored image as the preview source.
                BitmapImage tempBitmap = new BitmapImage(new Uri(media.Path));
                imagePreview.Source = tempBitmap;
                imagePreview.Visibility = Visibility.Visible;
                previewElement.Visibility = Visibility.Collapsed;
                IsCaptureInProgress = false;
            }
        }

        private async void cameraCapture_Failed(MediaCapture sender, MediaCaptureFailedEventArgs errorEventArgs)
        {
            // It's safest to return this back onto the UI thread to show the message dialog.
            MessageDialog dialog = new MessageDialog(errorEventArgs.Message);
            await this.Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, 
                async () => { await dialog.ShowAsync(); });            
        }

        private async void ButtonCapture_Click(object sender, RoutedEventArgs e)
        {
            // Prevent multiple initializations.
            ButtonCapture.IsEnabled = false;

            await CaptureImage();
        }

        private async void ButtonRetake_Click(object sender, Windows.UI.Xaml.RoutedEventArgs e)
        {
            // Reset the capture and then start again.
            await ResetCaptureAsync();
            await CaptureImage();
        }

        private async void ButtonCancel_Click(object sender, Windows.UI.Xaml.RoutedEventArgs e)
        {
            await ResetCaptureAsync();
        }

        private async Task ResetCaptureAsync()
        {
            captureGrid.Visibility = Windows.UI.Xaml.Visibility.Collapsed;
            imagePreview.Visibility = Visibility.Collapsed;
            captureButtons.Visibility = Visibility.Collapsed;
            previewElement.Source = null;
            ButtonCapture.IsEnabled = true;

            // Make sure we stop the preview and release resources.
            await cameraCapture.StopPreviewAsync();
            cameraCapture.Dispose();
            media = null;
        }

  	Este código muestra la interfaz de usuario usada para capturar una imagen y guarda la imagen en un archivo de almacenamiento.

6. Reemplace el método `InsertTodoItem` existente por el código siguiente:
 
        private async Task InsertTodoItem(TodoItem todoItem)
        {
            string errorString = string.Empty;

            if (media != null)
            {
                // Set blob properties of TodoItem.
                todoItem.ContainerName = "todoitemimages";

                // Use a unigue GUID to avoid collisions.
                todoItem.ResourceName = Guid.NewGuid().ToString();
            }

            // Send the item to be inserted. When blob properties are set this
            // generates an SAS in the response.
            await todoTable.InsertAsync(todoItem);

            // If we have a returned SAS, then upload the blob.
            if (!string.IsNullOrEmpty(todoItem.SasQueryString))
            {
                // Get the URI generated that contains the SAS 
                // and extract the storage credentials.
                StorageCredentials cred = new StorageCredentials(todoItem.SasQueryString);
                var imageUri = new Uri(todoItem.ImageUri);

                // Instantiate a Blob store container based on the info in the returned item.
                CloudBlobContainer container = new CloudBlobContainer(
                    new Uri(string.Format("https://{0}/{1}",
                        imageUri.Host, todoItem.ContainerName)), cred);

                // Get the new image as a stream.
                using (var inputStream = await media.OpenReadAsync())
                {
                    // Upload the new image as a BLOB from the stream.
                    CloudBlockBlob blobFromSASCredential =
                        container.GetBlockBlobReference(todoItem.ResourceName);
                    await blobFromSASCredential.UploadFromStreamAsync(inputStream);
                }

                // When you request an SAS at the container-level instead of the blob-level,
                // you are able to upload multiple streams using the same container credentials.

                await ResetCaptureAsync();
            }

            // Add the new item to the collection.
            items.Add(todoItem);
        }

	Este código envía una solicitud al servicio móvil para insertar un nuevo TodoItem. La respuesta contiene la SAS, que se usa para cargar la imagen del almacenamiento local al almacenamiento de blobs de Azure. Se usa la dirección URL de la imagen cargada en el enlace de datos.

El paso final es probar ambas versiones de la aplicación y validar que se cargue correctamente en ambos dispositivos.
		
##<a name="test"></a>Prueba de carga de imágenes en la aplicación

1. En Visual Studio, asegúrese de que el proyecto de Windows esté establecido como proyecto predeterminado y después presione la tecla F5 para ejecutar la aplicación.

2. Escriba texto en el cuadro de texto en **Insert a TodoItem** y, a continuación, haga clic en **Photo**.

3. Haga clic o puntee en la vista previa para tomar una imagen y luego haga clic en **Upload** (Cargar) para insertar el nuevo elemento y cargar la imagen.

	![](./media/mobile-services-windows-universal-dotnet-upload-to-blob-storage/mobile-quickstart-blob-appbar2.png)

4. El elemento nuevo, junto con la imagen cargada, se muestra en la vista de lista.

	![](./media/mobile-services-windows-universal-dotnet-upload-to-blob-storage/mobile-quickstart-blob-ie.png)

   	>[AZURE.NOTE]La imagen se descarga automáticamente desde el almacenamiento de blobs cuando la propiedad *imageUri* del nuevo elemento está enlazada al control **Image**.

5. Detenga la aplicación y reinicie la versión de la aplicación para el proyecto de Windows Phone.

	Se muestra la imagen cargada previamente.

6. Como antes, escriba texto en el cuadro de texto y haga clic en **Photo** (Foto).

   	![](./media/mobile-services-windows-universal-dotnet-upload-to-blob-storage/mobile-upload-blob-app-view-wp8.png)

3. Puntee en la vista previa para tomar una imagen y luego haga clic en **Upload** (Cargar) para insertar el nuevo elemento y cargar la imagen.

	![](./media/mobile-services-windows-universal-dotnet-upload-to-blob-storage/mobile-upload-blob-app-view-final-wp8.png)

Ha completado el tutorial de carga de imágenes.

<!---HONumber=Oct15_HO3-->