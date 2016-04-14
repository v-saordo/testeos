
##<a name="add-select-images"></a>Actualización de la aplicación cliente de inicio rápido para capturar y cargar imágenes

En esta sección se actualizará el proyecto a partir del tutorial [Introducción a Servicios móviles] para tomar fotografías y cargarlas en el almacenamiento de blobs de Azure. Para capturar la imagen, este tutorial utiliza [CameraCaptureTask] del espacio de nombres `Microsoft.Phone.Tasks`. Esta clase inicia la interfaz de usuario de la cámara en el dispositivo Windows Phone para capturar la foto y guarda la imagen automáticamente en el álbum de cámara del dispositivo Windows Phone. Si no desea que las imágenes se guarden en el álbum de cámara, utilice en su lugar la clase [PhotoCamera] en el espacio de nombres `Microsoft.Devices`.

1. En el Explorador de soluciones de Visual Studio, en el proyecto, expanda **Propiedades**. A continuación, abra el archivo WMAppManifest.xml y, en la pestaña **Capacidades**, habilite la cámara haciendo clic en **ID\\_CAP\\_ISV\\_CAMERA**. Cierre el archivo para guardar el cambio.

   	![](./media/mobile-services-windows-phone-upload-to-blob-storage/mobile-upload-blob-app-WMAppmanifest-wp8.png)

   	Esto asegura que la aplicación puede utilizar una cámara conectada al equipo. A los usuarios se les solicitará el acceso a la cámara la primera vez que se ejecuta la aplicación.

2. Abra el archivo MainPage.xaml y reemplace el elemento **Grid** llamado **ContentPanel** por el siguiente código:

        <!--ContentPanel - place additional content here-->
        <Grid x:Name="ContentPanel" Grid.Row="1" Margin="12,0,12,0">
            <Grid.RowDefinitions>
                <RowDefinition Height="Auto" />
                <RowDefinition Height="Auto" />
                <RowDefinition Height="Auto" />
                <RowDefinition Height="Auto" />
                <RowDefinition Height="Auto" />
                <RowDefinition Height="*" />
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="2*" />
                <ColumnDefinition Width="2*" />
            </Grid.ColumnDefinitions>
            <TextBlock Grid.Row="0" Grid.ColumnSpan="2" Text="Enter some text below, click Capture Image to add a captured image. Then click Save to insert a new TodoItem item into your database" TextWrapping="Wrap" Margin="12"/>
            <TextBox Grid.Row="1" Grid.ColumnSpan="2" Name="TextInput" Text="" />
            <Button Name="ButtonCaptureImage" Grid.Row="2" Click="ButtonCaptureImage_Click">Capture Image</Button>
            <Button Grid.Row ="2" Grid.Column="1" Name="ButtonSave" Click="ButtonSave_Click">Save</Button>
            <TextBlock Grid.Row="3" Grid.ColumnSpan="2" Text="Click refresh below to load the unfinished TodoItems from your database. Use the checkbox to complete and update your TodoItems" TextWrapping="Wrap" Margin="12" />
            <Button Grid.Row="4" Grid.ColumnSpan="2" Name="ButtonRefresh" Click="ButtonRefresh_Click">Refresh</Button>
            <phone:LongListSelector Grid.Row="5" Grid.ColumnSpan="2" Name="ListItems">
                <phone:LongListSelector.ItemTemplate>
                    <DataTemplate>
                        <StackPanel Orientation="Vertical">
                            <CheckBox Name="CheckBoxComplete" IsChecked="{Binding Complete, Mode=TwoWay}" Checked="CheckBoxComplete_Checked" Content="{Binding Text}" Margin="10,5" VerticalAlignment="Center"/>
                            <Image Name="ImageUpload" Source="{Binding ImageUri, Mode=OneWay}" MaxHeight="150"/>
                        </StackPanel>
                    </DataTemplate>
                </phone:LongListSelector.ItemTemplate>
            </phone:LongListSelector>
        </Grid>


   	Esto agrega un botón nuevo para iniciar [CameraCaptureTask] y agrega una imagen a **ItemTemplate**, y establece su origen de enlace como el URI de la imagen cargada en el servicio de almacenamiento de blobs.

3. Abra el archivo de proyecto MainPage.xaml.cs y agregue las siguientes instrucciones **using**:
	
		using Microsoft.Phone.Tasks;
		using System.IO;
		using Microsoft.WindowsAzure.Storage.Auth;
		using Microsoft.WindowsAzure.Storage.Blob;
    
4. En el archivo de proyecto MainPage.xaml.cs, actualice la clase TodoItem agregando las siguientes propiedades:

        [JsonProperty(PropertyName = "containerName")]
        public string ContainerName { get; set; }
		
        [JsonProperty(PropertyName = "resourceName")]
        public string ResourceName { get; set; }
		
        [JsonProperty(PropertyName = "sasQueryString")]
        public string SasQueryString { get; set; }
		
        [JsonProperty(PropertyName = "imageUri")]
        public string ImageUri { get; set; } 

5. En el archivo de proyecto MainPage.xaml.cs, actualice la clase MainPage. Agregue el siguiente código para declarar [CameraCaptureTask] y un objeto de secuencia que hará referencia a la imagen capturada:

        // Using the CameraCaptureTask to allow the user to capture a todo item image //
        CameraCaptureTask cameraCaptureTask;
		
        // Using a stream reference to upload the image to blob storage.
        Stream imageStream = null;

6. En el archivo de proyecto MainPage.xaml.cs, actualice la clase MainPage. Agregue el siguiente código para actualizar el constructor y así crear CameraCaptureTask y agregar un controlador de eventos para el evento completado:

        // Constructor
        public MainPage()
        {
            InitializeComponent();
			
            cameraCaptureTask = new CameraCaptureTask();
            cameraCaptureTask.Completed += cameraCaptureTask_Completed;
        }
		
        void cameraCaptureTask_Completed(object sender, PhotoResult e)
        {
            imageStream = e.ChosenPhoto;
        }

7. En el archivo de proyecto MainPage.xaml.cs, actualice la clase MainPage. Agregue el siguiente código que muestra la interfaz de usuario de la cámara para permitir al usuario capturar una imagen cuando se hace clic en el botón **Capture Image** (Capturar imagen):

        private void ButtonCaptureImage_Click(object sender, RoutedEventArgs e)
        {
            cameraCaptureTask.Show();
        }


8. En el archivo de proyecto MainPage.xaml.cs, actualice la clase MainPage. Reemplace el método `InsertTodoItem` existente por el código siguiente:
 
        private async void InsertTodoItem(TodoItem todoItem)
        {
            string errorString = string.Empty;            
			
            if (imageStream != null)
            {
                // Set blob properties of TodoItem.
                todoItem.ContainerName = "todoitemimages";
                todoItem.ResourceName = Guid.NewGuid().ToString() + ".jpg";
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
				
                // Upload the new image as a BLOB from the stream.
                CloudBlockBlob blobFromSASCredential =
                    container.GetBlockBlobReference(todoItem.ResourceName);
                await blobFromSASCredential.UploadFromStreamAsync(imageStream);
				
				// When you request an SAS at the container-level instead of the blob-level,
				// you are able to upload multiple streams using the same container credentials.

                imageStream = null;
            }              
			
            // Add the new item to the collection.
            items.Add(todoItem);
            TextInput.Text = "";
        }


	Este código envía una solicitud al servicio móvil para insertar un elemento TodoItem nuevo, incluido el nombre del archivo de imagen. La respuesta contiene la SAS, que luego se utiliza para insertar la imagen en el almacén de blobs, y el URI de la imagen para el enlace de datos.

El paso final es probar la aplicación y validar que se carga correctamente.
		
##<a name="test"></a>Prueba de carga de imágenes en la aplicación

1. En Visual Studio, puede presionar la tecla F5 para probar la aplicación en el emulador o con un dispositivo real dirigido.

2. Escriba texto en el cuadro de texto y, a continuación, haga clic en **Capture Image** (Capturar imagen).

   	![](./media/mobile-services-windows-phone-upload-to-blob-storage/mobile-upload-blob-app-view-wp8.png)

  	Se muestra la interfaz de usuario de captura de la cámara.

3. Haga clic en la imagen o en el botón de instantánea del teléfono para tomar una fotografía.
  
   	![](./media/mobile-services-windows-phone-upload-to-blob-storage/mobile-upload-blob-app-view-camera-wp8.png)

4. Haga clic en **accept** para aceptar la imagen y salir de la interfaz de usuario de la cámara.

    ![](./media/mobile-services-windows-phone-upload-to-blob-storage/mobile-upload-blob-app-view-camera-accept-wp8.png)

5. Haga clic en **Guardar** para insertar el elemento nuevo y cargar la imagen.

	![](./media/mobile-services-windows-phone-upload-to-blob-storage/mobile-upload-blob-app-view-save-wp8.png)

6. El elemento nuevo, junto con la imagen cargada, se muestra en la vista de lista.

	![](./media/mobile-services-windows-phone-upload-to-blob-storage/mobile-upload-blob-app-view-final-wp8.png)

   >[AZURE.NOTE]La imagen se descarga automáticamente desde el servicio de almacenamiento de blobs cuando la propiedad <code>imageUri</code> del elemento nuevo está ligada al control <strong>Image</strong>.


[Introducción a Servicios móviles]: ../articles/mobile-services-windows-phone-get-started.md
[CameraCaptureTask]: http://msdn.microsoft.com/library/windowsphone/develop/microsoft.phone.tasks.cameracapturetask(v=vs.105).aspx
[PhotoCamera]: http://msdn.microsoft.com/library/windowsphone/develop/microsoft.devices.photocamera(v=vs.105).aspx

<!---HONumber=Oct15_HO3-->