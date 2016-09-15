<properties 
	pageTitle="Copiar un blob existente en un activo de Servicios multimedia" 
	description="En este tema se muestra cómo copiar un blob existente en un activo de Servicios multimedia." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="ne" 
	ms.topic="article" 
	ms.date="02/03/2016" 
	ms.author="juliako"/>

#Copiar un blob existente en un activo de Servicios multimedia

En este tema se muestra cómo copiar blobs de una cuenta de almacenamiento a un nuevo recurso de Servicios multimedia de Microsoft Azure.

Los blobs podrían existir en una cuenta de almacenamiento asociada a la cuenta de Servicios multimedia o una cuenta de almacenamiento que no esté asociada a la cuenta de Servicios multimedia. En este tema se muestra cómo copiar blobs de una cuenta de almacenamiento a un activo de Servicios multimedia. Tenga en cuenta que también puede copiar entre centros de datos. Sin embargo, se podrían cobrar gastos por hacerlo. Para obtener más información sobre los precios, vea [Transferencias de datos](https://azure.microsoft.com/pricing/#header-11).

>[AZURE.NOTE] No debe intentar cambiar el contenido de los contenedores de blobs que generó Servicios multimedia sin usar las API de Servicios multimedia.

##Descarga de un ejemplo

Obtenga y ejecute un ejemplo desde [aquí](https://azure.microsoft.com/documentation/samples/media-services-dotnet-copy-blob-into-asset/).

##Requisitos previos

- Dos cuentas de Servicios multimedia en una suscripción de Azure nueva o existente. Vea el tema [Cómo crear una cuenta de Servicios multimedia](media-services-create-account.md).
- Sistemas operativos: Windows 10, Windows 7, Windows 2008 R2 o Windows 8.
- .NET Framework 4.5.
- Visual Studio 2010 SP1 (Professional, Premium, Ultimate o Express) o posterior.

##Configurar su proyecto

En esta sección creará y configurará un proyecto de aplicación de consola de C#.

1. Use Visual Studio 2012 o Visual Studio 2010 SP1 para crear una nueva solución que contiene el proyecto de aplicación de consola de C#. 
2. Escriba CopyExistingBlobsIntoAsset para el nombre y luego haga clic en Aceptar.
1. Use Nuget para agregar referencias a archivos DLL relacionados con los Servicios multimedia. En el menú principal de Visual Studio, seleccione HERRAMIENTAS -> Administrador de paquetes de la biblioteca -> Consola del administrador de paquetes. En la ventana de la consola escriba Install-Package windowsazure.mediaservices y presione Entrar.
1. Agregue otras referencias que son necesarias para este proyecto: System.Configuration.
1. Reemplace instrucciones using que se agregaron al archivo Programs.cs de forma predeterminada por las siguientes:
		
		using System;
		using System.Linq;
		using System.Configuration;
		using System.IO;
		using System.Text;
		using System.Threading;
		using System.Threading.Tasks;
		using System.Collections.Generic;
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.MediaServices.Client;
		using Microsoft.WindowsAzure;
		using System.Web;
		using Microsoft.WindowsAzure.Storage.Blob;
		using Microsoft.WindowsAzure.Storage.Auth;

1. Agregue la sección appSettings al archivo .config y actualice los valores en función de los valores de nombres y la clave de almacenamiento y Servicios multimedia.

		<appSettings>
		  <add key="MediaServicesAccountName" value="Media-Services-Account-Name"/>
		  <add key="MediaServicesAccountKey" value="Media-Services-Account-Key"/>
		  <add key="MediaServicesStorageAccountName" value="Media-Services-Storage-Account-Name"/>
		  <add key="MediaServicesStorageAccountKey" value="Media-Services-Storage-Account-Key"/>
		  <add key="ExternalStorageAccountName" value="External-Storage-Account-Name"/>
		  <add key="ExternalStorageAccountKey" value="External-Storage-Account-Key"/>
		</appSettings>


##Copiar blobs de una cuenta de almacenamiento a un recurso de Servicios multimedia.

El ejemplo de código siguiente realiza las siguientes tareas:

1. Crea la instancia de CloudMediaContext. 
1. Crea instancias de CloudStorageAccount: \_sourceStorageAccount y \_destinationStorageAccount.
1. Carga archivos de Smooth Streaming desde un directorio local a un contenedor de blobs que se encuentra en \_sourceStorageAccount.
1. Crea un nuevo activo. El contenedor de blobs que se crea para este activo se encuentra en \_destinationStorageAccount.
1. Usa el SDK de almacenamiento de Azure para copiar los blobs especificados en el contenedor asociado al activo.

>[AZURE.NOTE]La operación de copia no genera una excepción si el localizador ha caducado.

1. Establece el archivo .ism para que sea el archivo principal.
1. Crea la URL de Smooth Streaming para el localizador OnDemandOrigin asociado al activo. 
		
		class Program
		{
		    // Read values from the App.config file. 
		    static string _accountName = ConfigurationManager.AppSettings["MediaServicesAccountName"];
		    static string _accountKey = ConfigurationManager.AppSettings["MediaServicesAccountKey"];
		    static string _storageAccountName = ConfigurationManager.AppSettings["MediaServicesStorageAccountName"];
		    static string _storageAccountKey = ConfigurationManager.AppSettings["MediaServicesStorageAccountKey"];
		    static string _externalStorageAccountName = ConfigurationManager.AppSettings["ExternalStorageAccountName"];
		    static string _externalStorageAccountKey = ConfigurationManager.AppSettings["ExternalStorageAccountKey"];
		
		    private static MediaServicesCredentials _cachedCredentials = null;
		    private static CloudMediaContext _context = null;
		
		    private static CloudStorageAccount _sourceStorageAccount = null;
		    private static CloudStorageAccount _destinationStorageAccount = null;
		
		    static void Main(string[] args)
		    {
		        _cachedCredentials = new MediaServicesCredentials(
		                        _accountName,
		                        _accountKey);
		        // Use the cached credentials to create CloudMediaContext.
		        _context = new CloudMediaContext(_cachedCredentials);
		
		        // In this example the storage account from which we copy blobs is not 
		        // associated with the Media Services account into which we copy blobs.
		        // But the same code will work for coping blobs from a storage account that is 
		        // associated with the Media Services account.
		        //
		        // Get a reference to a storage account that is not associated with a Media Services account
		        // (an external account).  
		        StorageCredentials externalStorageCredentials =
		            new StorageCredentials(_externalStorageAccountName, _externalStorageAccountKey);
		        _sourceStorageAccount = new CloudStorageAccount(externalStorageCredentials, true);
		
		        //Get a reference to the storage account that is associated with a Media Services account. 
		        StorageCredentials mediaServicesStorageCredentials =
		            new StorageCredentials(_storageAccountName, _storageAccountKey);
		        _destinationStorageAccount = new CloudStorageAccount(mediaServicesStorageCredentials, false);
		
		        // Upload Smooth Streaming files into a storage account.
		        string localMediaDir = @"C:\supportFiles\streamingfiles";
		        CloudBlobContainer blobContainer =
		            UploadContentToStorageAccount(localMediaDir);
		
		        // Create a new asset and copy the smooth streaming files into 
		        // the container that is associated with the asset.
		        IAsset asset = CreateAssetFromExistingBlobs(blobContainer);
		
		        // Get the streaming URL for the smooth streaming files 
		        // that were copied into the asset.   
		        string urlForClientStreaming = CreateStreamingLocator(asset);
		        Console.WriteLine("Smooth Streaming URL: " + urlForClientStreaming);
		
		        Console.ReadLine();
		    }
		
		    /// <summary>
		    /// Uploads content from a local directory into the specified storage account.
		    /// In this example the storage account is not associated with the Media Services account.
		    /// </summary>
		    /// <param name="localPath">The path from which to upload the files.</param>
		    /// <returns>The container that contains the uploaded files.</returns>
		    static public CloudBlobContainer UploadContentToStorageAccount(string localPath)
		    {
		        CloudBlobClient externalCloudBlobClient = _sourceStorageAccount.CreateCloudBlobClient();
		        CloudBlobContainer externalMediaBlobContainer = externalCloudBlobClient.GetContainerReference("streamingfiles");
		
		        externalMediaBlobContainer.CreateIfNotExists();
		
		        // Upload files to the blob container.  
		        DirectoryInfo uploadDirectory = new DirectoryInfo(localPath);
		        foreach (var file in uploadDirectory.EnumerateFiles())
		        {
		            CloudBlockBlob blob = externalMediaBlobContainer.GetBlockBlobReference(file.Name);
		
		            blob.UploadFromFile(file.FullName, FileMode.Open);
		        }
		
		        return externalMediaBlobContainer;
		    }
		
		    /// <summary>
		    /// Creates a new asset and copies blobs from the specifed storage account.
		    /// </summary>
		    /// <param name="mediaBlobContainer">The specified blob container.</param>
		    /// <returns>The new asset.</returns>
		    static public IAsset CreateAssetFromExistingBlobs(CloudBlobContainer mediaBlobContainer)
		    {
		        // Create a new asset. 
		        IAsset asset = _context.Assets.Create("CopyBlob_" + Guid.NewGuid(), AssetCreationOptions.None);
		
		        IAccessPolicy writePolicy = _context.AccessPolicies.Create("writePolicy", TimeSpan.FromHours(24), AccessPermissions.Write);
		        ILocator destinationLocator = _context.Locators.CreateLocator(LocatorType.Sas, asset, writePolicy);
		
		        CloudBlobClient destBlobStorage = _destinationStorageAccount.CreateCloudBlobClient();
		
		        // Get the asset container URI and Blob copy from mediaContainer to assetContainer. 
		        string destinationContainerName = (new Uri(destinationLocator.Path)).Segments[1];
		
		        CloudBlobContainer assetContainer = destBlobStorage.GetContainerReference(destinationContainerName);
		
		        if (assetContainer.CreateIfNotExists())
		        {
		            assetContainer.SetPermissions(new BlobContainerPermissions
		            {
		                PublicAccess = BlobContainerPublicAccessType.Blob
		            });
		        }
		
		        var blobList = mediaBlobContainer.ListBlobs();
		        foreach (var sourceBlob in blobList)
		        {
		            var assetFile = asset.AssetFiles.Create((sourceBlob as ICloudBlob).Name);
		            CopyBlob(sourceBlob as ICloudBlob, assetContainer);
		            assetFile.ContentFileSize = (sourceBlob as ICloudBlob).Properties.Length;
		            assetFile.Update();
		        }
		
		        destinationLocator.Delete();
		        writePolicy.Delete();
		
		        // Since we copied a set of Smooth Streaming files, 
		        // set the .ism file to be the primary file. 
		        SetISMFileAsPrimary(asset);
		
		        return asset;
		    }
		
		    /// <summary>
		    /// Creates the OnDemandOrigin locator in order to get the streaming URL.
		    /// </summary>
		    /// <param name="asset">The asset that contains the smooth streaming files.</param>
		    /// <returns>The streaming URL.</returns>
		    static public string CreateStreamingLocator(IAsset asset)
		    {
		        var ismAssetFile = asset.AssetFiles.ToList().
		            Where(f => f.Name.EndsWith(".ism", StringComparison.OrdinalIgnoreCase)).First();
		
		        // Create a 30-day readonly access policy. 
            // You cannot create a streaming locator using an AccessPolicy that includes write or delete permissions.            
		        IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
		            TimeSpan.FromDays(30),
		            AccessPermissions.Read);
		
		        // Create a locator to the streaming content on an origin. 
		        ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
		            policy,
		            DateTime.UtcNow.AddMinutes(-5));
		
		        return originLocator.Path + ismAssetFile.Name + "/manifest";
		    }
		
		    /// <summary>
		    /// Copies the specified blob into the specified container.
		    /// </summary>
		    /// <param name="sourceBlob">The source container.</param>
		    /// <param name="destinationContainer">The destination container.</param>
		    static private void CopyBlob(ICloudBlob sourceBlob, CloudBlobContainer destinationContainer)
            {
                var signature = sourceBlob.GetSharedAccessSignature(new SharedAccessBlobPolicy
                {
                    Permissions = SharedAccessBlobPermissions.Read,
                    SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24)
                });

                ICloudBlob destinationBlob = destinationContainer.GetBlockBlobReference(sourceBlob.Name);

                if (destinationBlob.Exists())
                {
                    Console.WriteLine(string.Format("Destination blob '{0}' already exists. Skipping.", destinationBlob.Uri));
                }
                else
                {
                    try
                    {
                        Console.WriteLine(string.Format("Copy blob '{0}' to '{1}'", sourceBlob.Uri, destinationBlob.Uri));
                        destinationBlob.StartCopyFromBlob(new Uri(sourceBlob.Uri.AbsoluteUri + signature));


                        while (true)
                        {
                            // The StartCopyFromBlob is an async operation, 
                            // so we want to check if the copy operation is completed before proceeding. 
                            // To do that, we call FetchAttributes on the blob and check the CopyStatus. 
                            destinationBlob.FetchAttributes();
                            if (destinationBlob.CopyState.Status != CopyStatus.Pending)
                            {
                                break;
                            }
                            //It's still not completed. So wait for some time.
                            System.Threading.Thread.Sleep(1000);
                        }

                        Console.WriteLine("Final blob copy status = " + destinationBlob.CopyState.Status);
                    }
                    catch (Exception ex)
                    {
                        Console.WriteLine(string.Format("Error copying blob '{0}': {1}", sourceBlob.Name, ex.Message));
                    }
                }
            }
		
		    /// <summary>
		    /// Sets a file with the .ism extension as a primary file.
		    /// </summary>
		    /// <param name="asset">The asset that contains the smooth streaming files.</param>
		    static private void SetISMFileAsPrimary(IAsset asset)
		    {
		        var ismAssetFiles = asset.AssetFiles.ToList().
		            Where(f => f.Name.EndsWith(".ism", StringComparison.OrdinalIgnoreCase)).ToArray();
		
		        if (ismAssetFiles.Count() != 1)
		            throw new ArgumentException("The asset should have only one, .ism file");
		
		        ismAssetFiles.First().IsPrimary = true;
		        ismAssetFiles.First().Update();
		    }
		}
 

##Rutas de aprendizaje de Servicios multimedia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Envío de comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0211_2016-->