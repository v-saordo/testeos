<properties
	pageTitle="Uso de servicios móviles para cargar imágenes en el almacenamiento de blobs (Android) | Servicios móviles"
	description="Obtenga información acerca de cómo usar Servicios móviles para cargar imágenes en Almacenamiento de Azure y acceder a las imágenes desde su aplicación de Android."
	services="mobile-services"
	documentationCenter="android"
	authors="RickSaling"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="ricksal"/>

# Carga de imágenes a Almacenamiento de Azure desde un dispositivo Android

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;

[AZURE.INCLUDE [mobile-services-selector-upload-data-blob-storage](../../includes/mobile-services-selector-upload-data-blob-storage.md)]

En este tema se muestra cómo habilitar la aplicación Servicios móviles de Azure para Android para cargar imágenes en Almacenamiento de Azure.

Servicios móviles utiliza una Base de datos SQL para almacenar datos. Sin embargo, es más eficaz almacenar los datos de objeto binario grande (BLOB) en Almacenamiento de Azure. En este tutorial va a habilitar la aplicación de inicio rápido de Servicios móviles para sacar fotografías con la cámara Android y cargar las imágenes en Almacenamiento de Azure.


## Qué necesita para empezar

Antes de comenzar este tutorial, tiene que completar primero el inicio rápido a los Servicios móviles: [Introducción a Servicios móviles].

Este tutorial requiere lo siguiente:

+ Una [cuenta de almacenamiento de Azure](../storage/storage-create-storage-account.md)
+ Un dispositivo Android con una cámara

## Cómo funciona la aplicación

Cargar la imagen de la fotografía es un proceso de varios pasos:

- En primer lugar saque una foto e inserta una fila de TodoItem en la base de datos SQL que contiene los nuevos campos de metadatos usados por Almacenamiento de Azure.
- Un nuevo script de **inserción** de servicio móvil SQL pide al Almacenamiento de Azure una firma de acceso compartido (SAS).
- Ese script devuelve la SAS y un URI para el blob al cliente.
- El cliente carga la foto, usando la SAS y el URI del blob.

¿Qué es una SAS?

No es seguro almacenar las credenciales necesarias para cargar datos en el servicio Almacenamiento de Azure dentro de la aplicación cliente. En lugar de eso, estas credenciales se almacenan en su servicio móvil y se usan para generar una firma de acceso compartido (SAS) que da permiso para cargar una imagen nueva. Servicios móviles devuelve de manera segura una SAS, una credencial de expiración en cinco minutos, a la aplicación cliente. Luego la aplicación utiliza esta credencial temporal para cargar la imagen. Para obtener más información, consulte [Firmas de acceso compartido, Parte 1: Entendimiento del modelo SAS](../storage/storage-dotnet-shared-access-signature-part-1.md)

## Código de ejemplo
[Aquí](https://github.com/Azure/mobile-services-samples/tree/master/UploadImages) está la parte completa del código fuente de cliente de esta aplicación. Para ejecutarlo, debe completar las partes de back-end de Servicios móviles de este tutorial.

## Actualización del script de inserción registrado en el Portal de Azure clásico

[AZURE.INCLUDE [mobile-services-configure-blob-storage](../../includes/mobile-services-configure-blob-storage.md)]


## Actualización de la aplicación de inicio rápido para capturar y cargar imágenes

### Referencia a la biblioteca de cliente Android de Almacenamiento de Azure

1. Para agregar la referencia a la biblioteca, en la **aplicación** > archivo **build.gradle** agregue esta línea a la sección `dependencies`:

		compile 'com.microsoft.azure.android:azure-storage-android:0.6.0@aar'


2. Cambie el valor `minSdkVersion` a 15 (lo requiere la API de la cámara).

3. Presione el icono **Sincronizar proyecto con archivos de Gradle**.

### Actualización del archivo de manifiesto para el almacenamiento y la cámara

1. Especificar que la aplicación requiere tener una cámara disponible con la adición de esta línea a **AndroidManifest.xml**:

	    <uses-feature android:name="android.hardware.camera"
	        android:required="true" />

2. Especificar que la aplicación necesita permiso para escribir en un almacenamiento externo con la adición de esta línea de **AndroidManifest.xml**:

	    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />

	Tenga en cuenta que el almacenamiento externo de Android no es necesariamente una tarjeta SD: para obtener más información, consulte [Guardar archivos](http://developer.android.com/training/basics/data-storage/files.html).

### Actualización de los archivos de recursos de la nueva interfaz de usuario

1. Agregar títulos para los nuevos botones con la adición del siguiente código al archivo **strings.xml** en el directorio *valores*:

	    <string name="preview_button_text">Take Photo</string>
	    <string name="upload_button_text">Upload</string>

2. En el archivo **activity\_to\_do.xml** en la carpeta **res = > layout**, agregue este código antes del código existente para el botón **Agregar**.

         <Button
             android:id="@+id/buttonPreview"
             android:layout_width="120dip"
             android:layout_height="wrap_content"
             android:onClick="takePicture"
             android:text="@string/preview_button_text" />

3. Reemplace el elemento de botón **Agregar** con el código siguiente:

         <Button
             android:id="@+id/buttonUpload"
             android:layout_width="100dip"
             android:layout_height="wrap_content"
             android:onClick="uploadPhoto"
             android:text="@string/upload_button_text" />


### Adición de un código para la captura de fotografías

1. En **ToDoActivity.java** agregue este código para crear un objeto **Archivo** con un nombre único.

		// Create a File object for storing the photo
	    private File createImageFile() throws IOException {
	        // Create an image file name
	        String timeStamp = new SimpleDateFormat("yyyyMMdd_HHmmss").format(new Date());
	        String imageFileName = "JPEG_" + timeStamp + "_";
	        File storageDir = Environment.getExternalStoragePublicDirectory(Environment.DIRECTORY_PICTURES);
	        File image = File.createTempFile(
	                imageFileName,  /* prefix */
	                ".jpg",         /* suffix */
	                storageDir      /* directory */
	        );
	        return image;
	    }

5. Agregue este código para iniciar la aplicación de cámara de Android. A continuación, puede sacar fotografías y cuando una le parezca correcta, pulse **Guardar**, con ello se almacenará en el archivo que acaba de crear.

		// Run an Intent to start up the Android camera
	    static final int REQUEST_TAKE_PHOTO = 1;
	    public Uri mPhotoFileUri = null;
	    public File mPhotoFile = null;

	    public void takePicture(View view) {
	        Intent takePictureIntent = new Intent(MediaStore.ACTION_IMAGE_CAPTURE);
	        // Ensure that there's a camera activity to handle the intent
	        if (takePictureIntent.resolveActivity(getPackageManager()) != null) {
	            // Create the File where the photo should go
	            try {
	                mPhotoFile = createImageFile();
	            } catch (IOException ex) {
	                // Error occurred while creating the File
	                //
	            }
	            // Continue only if the File was successfully created
	            if (mPhotoFile != null) {
	                mPhotoFileUri = Uri.fromFile(mPhotoFile);
	                takePictureIntent.putExtra(MediaStore.EXTRA_OUTPUT, mPhotoFileUri);
	                startActivityForResult(takePictureIntent, REQUEST_TAKE_PHOTO);
	            }
	        }
	    }

### Adición de un código para cargar el archivo de fotos en el almacenamiento de blobs


1. En primer lugar agregue algunos nuevos campos de metadatos al objeto `ToDoItem` agregando este código a **ToDoItem.java**.

		/**
	     *  imageUri - points to location in storage where photo will go
	     */
	    @com.google.gson.annotations.SerializedName("imageUri")
	    private String mImageUri;

	    /**
	     * Returns the item ImageUri
	     */
	    public String getImageUri() {
	        return mImageUri;
	    }

	    /**
	     * Sets the item ImageUri
	     *
	     * @param ImageUri
	     *            Uri to set
	     */
	    public final void setImageUri(String ImageUri) {
	        mImageUri = ImageUri;
	    }

	    /**
	     * ContainerName - like a directory, holds blobs
	     */
	    @com.google.gson.annotations.SerializedName("containerName")
	    private String mContainerName;

	    /**
	     * Returns the item ContainerName
	     */
	    public String getContainerName() {
	        return mContainerName;
	    }

	    /**
	     * Sets the item ContainerName
	     *
	     * @param ContainerName
	     *            Uri to set
	     */
	    public final void setContainerName(String ContainerName) {
	        mContainerName = ContainerName;
	    }

	    /**
	     *  ResourceName
	     */
	    @com.google.gson.annotations.SerializedName("resourceName")
	    private String mResourceName;

	    /**
	     * Returns the item ResourceName
	     */
	    public String getResourceName() {
	        return mResourceName;
	    }

	    /**
	     * Sets the item ResourceName
	     *
	     * @param ResourceName
	     *            Uri to set
	     */
	    public final void setResourceName(String ResourceName) {
	        mResourceName = ResourceName;
	    }

	    /**
	     *  SasQueryString - permission to write to storage
	     */
	    @com.google.gson.annotations.SerializedName("sasQueryString")
	    private String mSasQueryString;

	    /**
	     * Returns the item SasQueryString
	     */
	    public String getSasQueryString() {
	        return mSasQueryString;
	    }

	    /**
	     * Sets the item SasQueryString
	     *
	     * @param SasQueryString
	     *            Uri to set
	     */
	    public final void setSasQueryString(String SasQueryString) {
	        mSasQueryString = SasQueryString;
	    }

2. Reemplace el constructor de parámetro 0 con este código:

	    /**
	     * ToDoItem constructor
	     */
	    public ToDoItem() {
	        mContainerName = "";
	        mResourceName = "";
	        mImageUri = "";
	        mSasQueryString = "";
	    }

3. Reemplace el constructor de parámetros múltiples con este código:

	    /**
	     * Initializes a new ToDoItem
	     *
	     * @param text
	     *            The item text
	     * @param id
	     *            The item id
	     */
	    public ToDoItem(String text,
	                    String id,
	                    String containerName,
	                    String resourceName,
	                    String imageUri,
	                    String sasQueryString) {
	        this.setText(text);
	        this.setId(id);
	        this.setContainerName(containerName);
	        this.setResourceName(resourceName);
	        this.setImageUri(imageUri);
	        this.setSasQueryString(sasQueryString);
	    }


4. En el archivo **ToDoActivity.java**, reemplace el método **addItem** en **ToDoActivity.java** con el código siguiente que carga la imagen.

	    /**
	     * Add a new item
	     *
	     * @param view
	     *            The view that originated the call
	     */
	    public void uploadPhoto(View view) {
	        if (mClient == null) {
	            return;
	        }

	        // Create a new item
	        final ToDoItem item = new ToDoItem();

	        item.setText(mTextNewToDo.getText().toString());
	        item.setComplete(false);
	        item.setContainerName("todoitemimages");

	        // Use a unigue GUID to avoid collisions.
	        UUID uuid = UUID.randomUUID();
	        String uuidInString = uuid.toString();
	        item.setResourceName(uuidInString);

	        // Send the item to be inserted. When blob properties are set this
	        // generates an SAS in the response.
	        AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
	            @Override
	            protected Void doInBackground(Void... params) {
	                try {
		                    final ToDoItem entity = addItemInTable(item);

		                    // If we have a returned SAS, then upload the blob.
		                    if (entity.getSasQueryString() != null) {

	                       // Get the URI generated that contains the SAS
	                        // and extract the storage credentials.
	                        StorageCredentials cred =
								new StorageCredentialsSharedAccessSignature(entity.getSasQueryString());
	                        URI imageUri = new URI(entity.getImageUri());

	                        // Upload the new image as a BLOB from a stream.
	                        CloudBlockBlob blobFromSASCredential =
	                                new CloudBlockBlob(imageUri, cred);

	                        blobFromSASCredential.uploadFromFile(mPhotoFileUri.getPath());
  	                    }

	                    runOnUiThread(new Runnable() {
	                        @Override
	                        public void run() {
	                            if(!entity.isComplete()){
	                                mAdapter.add(entity);
	                            }
	                        }
	                    });
	                } catch (final Exception e) {
	                    createAndShowDialogFromTask(e, "Error");
	                }
	                return null;
	            }
	        };

	        runAsyncTask(task);

	        mTextNewToDo.setText("");
	    }


Este código envía una solicitud al servicio móvil para insertar un nuevo TodoItem. La respuesta contiene la SAS, que se usa para cargar la imagen del almacenamiento local a un blob de Almacenamiento de Azure.


## Prueba de carga de imágenes

1. En Android Studio pulse **Run** (ejecutar). En el cuadro de diálogo, elija el dispositivo que se va a usar.

2. Cuando aparezca la interfaz de usuario de la aplicación, escriba un texto en el cuadro de texto denominado **Add a ToDo item** (agregar un elemento ToDo).

3. Pulse **Sacar foto**. Cuando se inicia la aplicación de la cámara, saque una foto. Haga clic en la marca de verificación para aceptar la foto.

4. Pulse **Cargar**. Compruebe que ToDoItem se agregó a la lista, como de costumbre.

5. En el Portal de Azure clásico, vaya a la cuenta de almacenamiento y pulse la pestaña **Contenedores** y pulse el nombre del contenedor en la lista.

6. Aparecerá una lista de los archivos de blob cargados. Seleccione uno y pulse **Descargar**.

7. La imagen que cargó aparece ahora en una ventana del explorador.


## <a name="next-steps"> </a>Pasos siguientes

Ahora que ha podido cargar de manera segura imágenes al integrar su servicio móvil con el servicio BLOB, revise algunos de los otros temas relacionados con la integración y el servicio back-end:

+ [Envío de correo electrónico desde servicios móviles con SendGrid]

  Aprenda a agregar la funcionalidad de correo electrónico a su Servicio móvil con el servicio de correo electrónico SendGrid. Este tema demuestra cómo agregar scripts del lado servidor para enviar correo electrónico mediante SendGrid.

+ [Programar trabajos de back-end en Servicios móviles]

  Aprenda a utilizar la funcionalidad del programador de trabajos de Servicios móviles para definir el código de script de servidor que se ejecuta según una programación que define usted.

+ [Referencia del script del servidor de servicios móviles]

  Temas de referencia para utilizar scripts de servidor con la finalidad de ejecutar tareas del lado servidor e integración con otros componentes de Azure y recursos externos.

+ [Referencia conceptual de Servicios móviles con .NET]

  Obtenga más información sobre el uso de Servicios móviles con .NET.


<!-- Anchors. -->
[Install the Storage Client library]: #install-storage-client
[Update the client app to capture images]: #add-select-images
[Update the insert script to generate an SAS]: #update-scripts
[Upload images to test the app]: #test
[Next Steps]: #next-steps

<!-- Images. -->

[2]: ./media/mobile-services-windows-store-dotnet-upload-data-blob-storage/mobile-add-storage-nuget-package-dotnet.png


<!-- URLs. -->
[Envío de correo electrónico desde servicios móviles con SendGrid]: store-sendgrid-mobile-services-send-email-scripts.md
[Programar trabajos de back-end en Servicios móviles]: mobile-services-schedule-recurring-tasks.md
[Send push notifications to Windows Store apps using Service Bus from a .NET back-end]: http://go.microsoft.com/fwlink/?LinkId=277073&clcid=0x409
[Referencia del script del servidor de servicios móviles]: mobile-services-how-to-use-server-scripts.md
[Introducción a Servicios móviles]: mobile-services-javascript-backend-windows-store-dotnet-get-started.md

[Azure classic portal]: https://manage.windowsazure.com/
[How To Create a Storage Account]: ../storage-create-storage-account.md
[Azure Storage Client library for Store apps]: http://go.microsoft.com/fwlink/p/?LinkId=276866
[Referencia conceptual de Servicios móviles con .NET]: mobile-services-windows-dotnet-how-to-use-client-library.md
[App settings]: http://msdn.microsoft.com/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7

<!---HONumber=AcomDC_0128_2016-->