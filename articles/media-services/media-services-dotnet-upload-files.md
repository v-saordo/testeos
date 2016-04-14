<properties 
	pageTitle="Cargar archivos en una cuenta de Servicios multimedia mediante .NET" 
	description="Aprenda a obtener contenido multimedia en Servicios multimedia mediante la creación y carga de recursos." 
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



#Cargar archivos en una cuenta de Servicios multimedia mediante .NET

[AZURE.INCLUDE [media-services-selector-upload-files](../../includes/media-services-selector-upload-files.md)]

En Servicios multimedia, cargará (o introducirá) los archivos digitales en un recurso. La entidad **Recurso** puede contener archivos de vídeo, audio, imágenes, colecciones de miniaturas, pistas de texto y subtítulos (y los metadatos acerca de estos archivos). Una vez cargados los archivos, el contenido se almacena de forma segura en la nube para un posterior procesamiento y streaming.

Los archivos del recurso se denominan **archivos de recursos**. La instancia de **AssetFile** y el archivo multimedia real son dos objetos distintos. La instancia de AssetFile contiene metadatos sobre el archivo multimedia, mientras que el archivo multimedia contiene el contenido multimedia real.

Al crear recursos, puede especificar las siguientes opciones de cifrado.

- **Ninguno**: no se utiliza cifrado. Este es el valor predeterminado. Tenga en cuenta que al utilizar esta opción el contenido no está protegido en tránsito o en reposo en el almacenamiento. Si tiene previsto entregar un MP4 mediante una descarga progresiva, utilice esta opción. 
- **CommonEncryption**: utilice esta opción si va a cargar contenido que ya se ha cifrado y protegido con cifrado común o DRM de PlayReady (por ejemplo, Smooth Streaming protegido con DRM de PlayReady).
- **EnvelopeEncrypted**: utilice esta opción si va a cargar HLS cifrado con AES. Tenga en cuenta que los archivos deben haberse codificado y cifrado con Transform Manager.
- **StorageEncrypted**: cifra el contenido no cifrado localmente mediante el cifrado AES de 256 bits y, a continuación, lo carga en el almacenamiento de Azure donde se almacena cifrado en reposo. Los recursos protegidos con el cifrado de almacenamiento se descifran automáticamente y se colocan en un sistema de archivos cifrados antes de la codificación y, opcionalmente, se vuelven a cifrar antes de volver a cargarlos como un nuevo recurso de salida. El caso de uso principal para el cifrado de almacenamiento es cuando desea proteger los archivos multimedia de entrada de alta calidad con un sólido cifrado en reposo en disco.

	Los Servicios multimedia proporcionan cifrado de almacenamiento en disco para sus recursos, no por cable como el administrador de derechos digitales (DRM).

	Si el recurso tiene el almacenamiento cifrado, asegúrese de configurar la directiva de entrega de recursos. Para más información, consulte [Configuración de la directiva de entrega de recursos](media-services-dotnet-configure-asset-delivery-policy.md).

Si se especifica que el recurso se cifre con una opción **CommonEncrypted** o una opción **EnvelopeEncypted**, deberá asociar el recurso con una **ContentKey**. Para obtener más información, consulte [Creación de una ContentKey](media-services-dotnet-create-contentkey.md).

Si especifica que el recurso se cifre con una opción **StorageEncrypted**, el SDK de Servicios multimedia para .NET creará una **StorateEncrypted** **ContentKey** para el recurso.

>[AZURE.NOTE]Los Servicios multimedia usan el valor de la propiedad IAssetFile.Name al generar direcciones URL para el contenido de streaming (por ejemplo, http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.). Por este motivo, no se permite la codificación porcentual. El valor de la propiedad **Name** no puede tener ninguno de los siguientes [caracteres reservados para la codificación porcentual](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters): !*'();:@&=+$,/?%#". Además, solo puede haber un '.' para la extensión del nombre de archivo.

En este tema se muestra cómo usar el SDK de Servicios multimedia para .NET, así como las extensiones del SDK de Servicios multimedia para .NET para cargar archivos en un recurso de Servicios multimedia.

 
## Carga de un solo archivo con el SDK .NET de Servicios multimedia 

El código de ejemplo siguiente usa el SDK de .NET para realizar las tareas siguientes:

- Crea un activo vacío.
- Crea una instancia de AssetFile que necesitamos asociar con el recurso.
- Crea una instancia de AccessPolicy que define los permisos y la duración del acceso al recurso.
- Crea una instancia de Locator que proporciona acceso al recurso.
- Carga un solo archivo multimedia en los Servicios multimedia. 

		
		static public IAsset CreateAssetAndUploadSingleFile(AssetCreationOptions assetCreationOptions, string singleFilePath)
		{
            if (!File.Exists(singleFilePath))
            {
                Console.WriteLine("File does not exist.");
                return null;
            }

            var assetName = Path.GetFileNameWithoutExtension(singleFilePath);
            IAsset inputAsset = _context.Assets.Create(assetName, assetCreationOptions); 

            var assetFile = inputAsset.AssetFiles.Create(Path.GetFileName(singleFilePath));

            Console.WriteLine("Created assetFile {0}", assetFile.Name);

            var policy = _context.AccessPolicies.Create(
                                    assetName,
                                    TimeSpan.FromDays(30),
                                    AccessPermissions.Write | AccessPermissions.List);

            var locator = _context.Locators.CreateLocator(LocatorType.Sas, inputAsset, policy);

            Console.WriteLine("Upload {0}", assetFile.Name);

            assetFile.Upload(singleFilePath);
            Console.WriteLine("Done uploading {0}", assetFile.Name);

            locator.Delete();
            policy.Delete();

            return inputAsset;
		}

##Carga de varios archivos con el SDK .NET de Servicios multimedia 

El código siguiente muestra cómo crear un activo y cargar varios archivos.

El código hace lo siguiente:
	
- 	Crea un recurso vacío mediante el método CreateEmptyAsset definido en el paso anterior.
 	
- 	Crea una instancia de **AccessPolicy** que define los permisos y la duración del acceso al recurso.
 	
- 	Crea una instancia de **Locator** que proporciona acceso al recurso.
 	
- 	Crea una instancia de **BlobTransferClient**. Este tipo representa a un cliente que funciona en los blobs de Azure. En este ejemplo, usamos al cliente para supervisar el progreso de carga.
 	
- 	Enumera los archivos en el directorio especificado y crea una instancia de **AssetFile** para cada archivo.
 	
- 	Carga los archivos en los Servicios multimedia con el método **UploadAsync**.
 	
>[AZURE.NOTE] Use el método UploadAsync para asegurarse de que las llamadas no provocan un bloqueo y los archivos se cargan en paralelo.
 	
 	
        static public IAsset CreateAssetAndUploadMultipleFiles(AssetCreationOptions assetCreationOptions, string folderPath)
        {
            var assetName = "UploadMultipleFiles_" + DateTime.UtcNow.ToString();

            IAsset asset = _context.Assets.Create(assetName, assetCreationOptions);

            var accessPolicy = _context.AccessPolicies.Create(assetName, TimeSpan.FromDays(30),
                                                                AccessPermissions.Write | AccessPermissions.List);

            var locator = _context.Locators.CreateLocator(LocatorType.Sas, asset, accessPolicy);

            var blobTransferClient = new BlobTransferClient();
            blobTransferClient.NumberOfConcurrentTransfers = 20;
            blobTransferClient.ParallelTransferThreadCount = 20;

            blobTransferClient.TransferProgressChanged += blobTransferClient_TransferProgressChanged;

            var filePaths = Directory.EnumerateFiles(folderPath);

            Console.WriteLine("There are {0} files in {1}", filePaths.Count(), folderPath);

            if (!filePaths.Any())
            {
                throw new FileNotFoundException(String.Format("No files in directory, check folderPath: {0}", folderPath));
            }

            var uploadTasks = new List<Task>();
            foreach (var filePath in filePaths)
            {
                var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
                Console.WriteLine("Created assetFile {0}", assetFile.Name);

                // It is recommended to validate AccestFiles before upload. 
                Console.WriteLine("Start uploading of {0}", assetFile.Name);
                uploadTasks.Add(assetFile.UploadAsync(filePath, blobTransferClient, locator, CancellationToken.None));
            }

            Task.WaitAll(uploadTasks.ToArray());
            Console.WriteLine("Done uploading the files");

            blobTransferClient.TransferProgressChanged -= blobTransferClient_TransferProgressChanged;

            locator.Delete();
            accessPolicy.Delete();

            return asset;
        }
	
	static void  blobTransferClient_TransferProgressChanged(object sender, BlobTransferProgressChangedEventArgs e)
	{
	    if (e.ProgressPercentage > 4) // Avoid startup jitter, as the upload tasks are added.
	    {
	        Console.WriteLine("{0}% upload competed for {1}.", e.ProgressPercentage, e.LocalFile);
	    }
	}



Al cargar un número elevado de recursos, tenga en cuenta lo siguiente.

- Cree un nuevo objeto **CloudMediaContext** por subproceso. La clase **CloudMediaContext** no es segura para subprocesos.
 
- Aumente NumberOfConcurrentTransfers desde el valor predeterminado de 2 a un valor superior como 5. La configuración de esta propiedad afecta a todas las instancias de **CloudMediaContext**.
 
- Mantenga ParallelTransferThreadCount en el valor predeterminado de 10.
 
##<a id="ingest_in_bulk"></a>Ingesta de activos en bloque con SDK .NET de Servicios multimedia 

La carga de archivos de recursos de gran tamaño puede ser un obstáculo durante la creación de recursos. La ingesta de recursos en masa o "Ingesta en masa" implica la separación de la creación de recursos del proceso de carga. Para adoptar un enfoque de ingesta en masa, cree un manifiesto (IngestManifest) que describa el recurso y sus archivos asociados. A continuación, use el método de carga que prefiera para cargar los archivos asociados al contenedor de blobs del manifiesto. Los Servicios multimedia de Microsoft Azure ven el contenedor de blobs asociado al manifesto. Una vez que se carga un archivo en el contenedor de blobs, los Servicios multimedia de Microsoft Azure completan la creación de recursos según la configuración del recurso en el manifiesto (IngestManifestAsset).


Para crear un nuevo IngestManifest, llame al método Create expuesto por la colección de IngestManifests en CloudMediaContext. Este método creará un nuevo IngestManifest con el nombre de manifesto proporcionado.

	IIngestManifest manifest = context.IngestManifests.Create(name);

Cree los recursos que se asociarán con el IngestManifest en masa. Configure las opciones de cifrado deseadas en el recurso para la ingesta en masa.

	// Create the assets that will be associated with this bulk ingest manifest
	IAsset destAsset1 = _context.Assets.Create(name + "_asset_1", AssetCreationOptions.None);
	IAsset destAsset2 = _context.Assets.Create(name + "_asset_2", AssetCreationOptions.None);

Un IngestManifestAsset permite asociar un recurso con un IngestManifest en masa para la ingesta en masa. También asocia los AssetFiles que conformarán cada recurso. Para crear un IngestManifestAsset, use el método Create en el contexto del servidor.

En el ejemplo siguiente se agregan dos nuevos IngestManifestAssets que asocian los dos recursos creados anteriormente al manifesto de ingesta en masa. Además, cada IngestManifestAsset asociad un conjunto de archivos que se cargarán para cada recurso durante la ingesta en masa.

	string filename1 = _singleInputMp4Path;
	string filename2 = _primaryFilePath;
	string filename3 = _singleInputFilePath;
	
	IIngestManifestAsset bulkAsset1 =  manifest.IngestManifestAssets.Create(destAsset1, new[] { filename1 });
	IIngestManifestAsset bulkAsset2 =  manifest.IngestManifestAssets.Create(destAsset2, new[] { filename2, filename3 });
	
Puede usar cualquier aplicación cliente de alta velocidad capaz de cargar los archivos de recursos en el URI del contenedor de almacenamiento de blobs proporcionado por la propiedad **IIngestManifest.BlobStorageUriForUpload** de IngestManifest. Un servicio de carga de alta velocidad destacado es [Aspera On Demand para la aplicación de Azure](https://datamarket.azure.com/application/2cdbc511-cb12-4715-9871-c7e7fbbb82a6). También puede escribir código para cargar los archivos de recursos como se muestra en el ejemplo de código siguiente.
	
	static void UploadBlobFile(string destBlobURI, string filename)
	{
	    Task copytask = new Task(() =>
	    {
	        var storageaccount = new CloudStorageAccount(new StorageCredentials(_storageAccountName, _storageAccountKey), true);
	        CloudBlobClient blobClient = storageaccount.CreateCloudBlobClient();
	        CloudBlobContainer blobContainer = blobClient.GetContainerReference(destBlobURI);
	
	        string[] splitfilename = filename.Split('\\');
	        var blob = blobContainer.GetBlockBlobReference(splitfilename[splitfilename.Length - 1]);
	
	        using (var stream = System.IO.File.OpenRead(filename))
	            blob.UploadFromStream(stream);
	
	        lock (consoleWriteLock)
	        {
	            Console.WriteLine("Upload for {0} completed.", filename);
	        }
	    });
	
	    copytask.Start();
	}

El código para cargar los archivos de recursos para el ejemplo usado en este tema se muestra en el ejemplo de código siguiente.
	
	UploadBlobFile(manifest.BlobStorageUriForUpload, filename1);
	UploadBlobFile(manifest.BlobStorageUriForUpload, filename2);
	UploadBlobFile(manifest.BlobStorageUriForUpload, filename3);
	

Puede determinar el progreso del proceso de ingesta en masa de todos los recursos asociados con un **IngestManifest** mediante el sondeo de la propiedad Statistics de **IngestManifest**. Para actualizar la información de progreso, debe usar un **CloudMediaContext** nuevo cada vez que sondee la propiedad Statistics.

En el ejemplo siguiente se muestra cómo sondear un **IngestManifest** por su Id.
	
	static void MonitorBulkManifest(string manifestID)
	{
	   bool bContinue = true;
	   while (bContinue)
	   {
	      CloudMediaContext context = GetContext();
	      IIngestManifest manifest = context.IngestManifests.Where(m => m.Id == manifestID).FirstOrDefault();
	
	      if (manifest != null)
	      {
	         lock(consoleWriteLock)
	         {
	            Console.WriteLine("\nWaiting on all file uploads.");
	            Console.WriteLine("PendingFilesCount  : {0}", manifest.Statistics.PendingFilesCount);
	            Console.WriteLine("FinishedFilesCount : {0}", manifest.Statistics.FinishedFilesCount);
	            Console.WriteLine("{0}% complete.\n", (float)manifest.Statistics.FinishedFilesCount / (float)(manifest.Statistics.FinishedFilesCount + manifest.Statistics.PendingFilesCount) * 100);
	
	            if (manifest.Statistics.PendingFilesCount == 0)
	            {
	               Console.WriteLine("Completed\n");
	               bContinue = false;
	            }
	         }
	
	         if (manifest.Statistics.FinishedFilesCount < manifest.Statistics.PendingFilesCount)
	            Thread.Sleep(60000);
	      }
	      else // Manifest is null
	         bContinue = false;
	   }
	}
	


##Cargar archivos con extensiones del SDK de .NET 

En el ejemplo siguiente se muestra cómo cargar un solo archivo con extensiones del SDK de .NET. En este caso, se usa el método **CreateFromFile**, pero también está disponible la versión asincrónica (**CreateFromFileAsync**). El método **CreateFromFile** le permite especificar el nombre de archivo, la opción de cifrado y una devolución de llamada para notificar el progreso de carga del archivo.


	static public IAsset UploadFile(string fileName, AssetCreationOptions options)
	{
	    IAsset inputAsset = _context.Assets.CreateFromFile(
	        fileName,
	        options,
	        (af, p) =>
	        {
	            Console.WriteLine("Uploading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
	        });
	
	    Console.WriteLine("Asset {0} created.", inputAsset.Id);
	
	    return inputAsset;
	}

En el ejemplo siguiente se llama a la función UploadFile y se especifica el cifrado de almacenamiento como la opción de creación de recursos.


	var asset = UploadFile(@"C:\VideoFiles\BigBuckBunny.mp4", AssetCreationOptions.StorageEncrypted);


##Rutas de aprendizaje de Servicios multimedia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Envío de comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]



##Pasos siguientes
Ahora que ha cargado un recurso en los Servicios multimedia, vaya al tema [Obtención de un procesador multimedia][].

[Obtención de un procesador multimedia]: media-services-get-media-processor.md
 

<!---HONumber=AcomDC_0211_2016-->