##<a name="add-select-images"></a>Actualización de la aplicación cliente de inicio rápido para capturar y cargar imágenes

1. En Visual Studio, abra el archivo Package.appxmanifest y, en la pestaña **Capacidades**, habilite las capacidades **Webcam** y **Microphone**.

   	![](./media/mobile-services-windows-store-dotnet-upload-to-blob-storage/mobile-app-manifest-camera.png)
 
   	Esto asegura que la aplicación puede utilizar una cámara conectada al equipo. A los usuarios se les solicitará el acceso a la cámara la primera vez que se ejecuta la aplicación.

1. Abra el archivo MainPage.xaml y reemplace el elemento **StackPanel** directamente después del primer elemento **Task** por el siguiente código:

        <StackPanel Orientation="Horizontal" Margin="72,0,0,0">
            <TextBox Name="TextInput" Margin="5" MaxHeight="40" MinWidth="300"></TextBox>
            <Button Name="btnTakePhoto" Style="{StaticResource PhotoAppBarButtonStyle}"
                    Click="OnTakePhotoClick" />
            <Button Name="ButtonSave" Style="{StaticResource UploadAppBarButtonStyle}" 
                    Click="ButtonSave_Click"/>
        </StackPanel>

2. Reemplace el elemento **StackPanel** de **DataTemplate** por el siguiente código:

        <StackPanel Orientation="Vertical">
            <CheckBox Name="CheckBoxComplete" IsChecked="{Binding Complete, Mode=TwoWay}" 
                        Checked="CheckBoxComplete_Checked" Content="{Binding Text}" 
                        Margin="10,5" VerticalAlignment="Center"/>
            <Image Name="ImageUpload" Source="{Binding ImageUri, Mode=OneWay}"
                    MaxHeight="250"/>
        </StackPanel> 

   	Esto agrega una imagen a **ItemTemplate** y establece su origen de enlace como el URI de la imagen cargada en el servicio de almacenamiento de blobs.

3. Abra el archivo de proyecto MainPage.xaml.cs y agregue las siguientes instrucciones **using**:
	
		using Windows.Media.Capture;
		using Windows.Storage;
		using Windows.UI.Popups;
		using Microsoft.WindowsAzure.Storage.Auth;
		using Microsoft.WindowsAzure.Storage.Blob;
    
4. En la clase TodoItem, agregue las siguientes propiedades:

        [JsonProperty(PropertyName = "containerName")]
        public string ContainerName { get; set; }
		
        [JsonProperty(PropertyName = "resourceName")]
        public string ResourceName { get; set; }
		
        [JsonProperty(PropertyName = "sasQueryString")]
        public string SasQueryString { get; set; }
		
        [JsonProperty(PropertyName = "imageUri")]
        public string ImageUri { get; set; } 

   	>[AZURE.NOTE]Para agregar nuevas propiedades al objeto TodoItem en un servicio móvil de back-end de JavaScript, debe tener el esquema dinámico habilitado en el servicio móvil. Cuando el esquema dinámico está habilitado, automáticamente se agregan columnas nuevas a la tabla TodoItem que se asignan a estas nuevas propiedades. Para un servicio móvil de back-end de .NET, consulte el tema [Modificación del modelo de datos de un servicio móvil back-end de .NET](../articles/mobile-services/mobile-services-dotnet-backend-how-to-use-code-first-migrations.md).

5. En la clase MainPage, agregue el siguiente código:

        // Use a StorageFile to hold the captured image for upload.
        StorageFile media = null;
		
		private async void OnTakePhotoClick(object sender, RoutedEventArgs e)
		{
			// Capture a new photo or video from the device.
			CameraCaptureUI cameraCapture = new CameraCaptureUI();
			media = await cameraCapture
				.CaptureFileAsync(CameraCaptureUIMode.PhotoOrVideo);
        }

  	Este código muestra la interfaz de usuario de la cámara para capturar una imagen y guarda la imagen en un archivo de almacenamiento.

6. Reemplace el método `InsertTodoItem` existente por el código siguiente:
 
        private async void InsertTodoItem(TodoItem todoItem)
        {
            string errorString = string.Empty;
			
            if (media != null)
            {
                // Set blob properties of TodoItem.
                todoItem.ContainerName = "todoitemimages";
                todoItem.ResourceName = media.Name;
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
                using (var fileStream = await media.OpenStreamForReadAsync())
                {                   					
                    // Upload the new image as a BLOB from the stream.
                    CloudBlockBlob blobFromSASCredential =
                        container.GetBlockBlobReference(todoItem.ResourceName);
                    await blobFromSASCredential.UploadFromStreamAsync(fileStream.AsInputStream());
                }
				
				// When you request an SAS at the container-level instead of the blob-level,
				// you are able to upload multiple streams using the same container credentials.
            }
			
            // Add the new item to the collection.
            items.Add(todoItem);
        }

	Este código envía una solicitud al servicio móvil para insertar un elemento TodoItem nuevo, incluido el nombre del archivo de imagen. La respuesta contiene la SAS, que luego se utiliza para insertar la imagen en el almacén de blobs, y el URI de la imagen para el enlace de datos.

El paso final es probar la aplicación y validar que se carga correctamente.
		
##<a name="test"></a>Prueba de carga de imágenes en la aplicación

1. En Visual Studio, presione la tecla F5 para ejecutar la aplicación.

2. Escriba texto en el cuadro de texto en **Insert a TodoItem** y, a continuación, haga clic en **Photo**.

   	![](./media/mobile-services-windows-store-dotnet-upload-to-blob-storage/mobile-quickstart-blob-appbar.png)

  	Se muestra la interfaz de usuario de captura de la cámara.

3. Haga clic en la imagen para tomar una foto y, a continuación, haga clic en **OK**.
  
   	![](./media/mobile-services-windows-store-dotnet-upload-to-blob-storage/mobile-quickstart-blob-camera.png)

4. Haga clic en **Upload** para insertar el elemento nuevo y cargar la imagen.

	![](./media/mobile-services-windows-store-dotnet-upload-to-blob-storage/mobile-quickstart-blob-appbar2.png)

5. El elemento nuevo, junto con la imagen cargada, se muestra en la vista de lista.

	![](./media/mobile-services-windows-store-dotnet-upload-to-blob-storage/mobile-quickstart-blob-ie.png)

   	>[AZURE.NOTE]La imagen se descarga automáticamente desde el servicio de almacenamiento de blobs cuando la propiedad <code>imageUri</code> del elemento nuevo está ligada al control <strong>Image</strong>.

<!---HONumber=Oct15_HO3-->