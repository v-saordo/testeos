<properties 
	pageTitle="Administración de recursos de Servicios multimedia entre varias cuentas de almacenamiento" 
	description="En este artículo se dan instrucciones sobre cómo administrar recursos de servicios de multimedia en varias cuentas de almacenamiento." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/28/2016" 
	ms.author="juliako"/>


#Administración de recursos de Servicios multimedia entre varias cuentas de almacenamiento

A partir de Servicios multimedia de Microsoft Azure 2.2, puede asociar varias cuentas de almacenamiento a una sola cuenta de Servicios multimedia. La capacidad de asociar varias cuentas de almacenamiento a una cuenta de Servicios multimedia ofrece las siguientes ventajas:

- Equilibrio de carga de sus activos entre varias cuentas de almacenamiento.
- Escalado de Servicios multimedia para grandes cantidades de procesamiento de contenido (ya que actualmente una única cuenta de almacenamiento tiene un límite máximo de 500 TB). 

En este tema se muestra cómo asociar varias cuentas de almacenamiento a una cuenta de Servicios multimedia mediante la API de REST de administración de servicios de Azure. También muestra cómo especificar diferentes cuentas de almacenamiento al crear activos mediante el SDK de Servicios multimedia.

##Consideraciones

Al asociar varias cuentas de almacenamiento a su cuenta de Servicios multimedia, se aplican las siguientes consideraciones:

- Todas las cuentas de almacenamiento asociadas a una cuenta de Servicios multimedia deben estar en el mismo centro de datos que la cuenta de Servicios multimedia.
- Actualmente, cuando se asocia una cuenta de almacenamiento a la cuenta especificada de Servicios multimedia, no se puede desasociar.
- La cuenta de almacenamiento principal es la indicada durante el tiempo de creación de cuenta de Servicios multimedia. Actualmente, no puede cambiar la cuenta de almacenamiento predeterminada. 

Otras consideraciones:

Los Servicios multimedia usan el valor de la propiedad **IAssetFile.Name** al generar direcciones URL para el contenido de streaming (por ejemplo, http://{WAMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.). Por este motivo, no se permite la codificación porcentual. El valor de la propiedad Name no puede tener ninguno de los siguientes [caracteres reservados para la codificación porcentual](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters): !*'();:@&=+$,/?%#". Además, solo puede haber un '.' para la extensión del nombre de archivo.

##Para asociar una cuenta de almacenamiento con la API de REST de administración de servicios de Azure

Actualmente, la única manera de asociar varias cuentas de almacenamiento es usando la [API de REST de administración de servicios de Azure](http://msdn.microsoft.com/library/azure/dn167014.aspx). El ejemplo de código en el tema [Cómo usar la API de REST de administración de Servicios multimedia](https://msdn.microsoft.com/library/azure/dn167656.aspx) define el método **AttachStorageAccountToMediaServiceAccount** que asocia una cuenta de almacenamiento a la cuenta de Servicios multimedia especificada. El código del mismo tema define el método **ListStorageAccountDetails** que muestra todas las cuentas de almacenamiento asociadas a la cuenta de Servicios multimedia especificada.


##Para administrar los recursos de Servicios multimedia entre varias cuentas de almacenamiento

El código siguiente usa el último SDK de Servicios multimedia para realizar las siguientes tareas:

1. Muestre todas las cuentas de almacenamiento asociadas a la cuenta de Servicios multimedia especificada.
1. Recupere el nombre de la cuenta de almacenamiento predeterminada.
1. Cree un nuevo recurso en la cuenta de almacenamiento predeterminada.
1. Cree un recurso de salida del trabajo de codificación en la cuenta de almacenamiento especificada.
	
		using Microsoft.WindowsAzure.MediaServices.Client;
		using System;
		using System.Collections.Generic;
		using System.Configuration;
		using System.IO;
		using System.Linq;
		using System.Text;
		using System.Threading;
		using System.Threading.Tasks;
		
		namespace MultipleStorageAccounts
		{
		    class Program
		    {
		        // Location of the media file that you want to encode. 
		        private static readonly string _singleInputFilePath =
		            Path.GetFullPath(@"../..\supportFiles\multifile\interview2.wmv");
		
		        private static readonly string MediaServicesAccountName = 
		            ConfigurationManager.AppSettings["MediaServicesAccountName"];
		        private static readonly string MediaServicesAccountKey = 
		            ConfigurationManager.AppSettings["MediaServicesAccountKey"];
		
		        private static CloudMediaContext _context;
		        private static MediaServicesCredentials _cachedCredentials = null;
	
		        static void Main(string[] args)
		        {
	
		            // Create and cache the Media Services credentials in a static class variable.
		            _cachedCredentials = new MediaServicesCredentials(
		                            MediaServicesAccountName,
		                            MediaServicesAccountKey);
		            // Used the cached credentials to create CloudMediaContext.
		            _context = new CloudMediaContext(_cachedCredentials);
	
		
		            // Display the storage accounts associated with 
		            // the specified Media Services account:
		            foreach (var sa in _context.StorageAccounts)
		                Console.WriteLine(sa.Name);
		
		            // Retrieve the name of the default storage account.
		            var defaultStorageName = _context.StorageAccounts.Where(s => s.IsDefault == true).FirstOrDefault();
		            Console.WriteLine("Name: {0}", defaultStorageName.Name);
		            Console.WriteLine("IsDefault: {0}", defaultStorageName.IsDefault);
		
		            // Retrieve the name of a storage account that is not the default one.
		            var notDefaultStroageName = _context.StorageAccounts.Where(s => s.IsDefault == false).FirstOrDefault();
		            Console.WriteLine("Name: {0}", notDefaultStroageName.Name);
		            Console.WriteLine("IsDefault: {0}", notDefaultStroageName.IsDefault);
		            
		            // Create the original asset in the default storage account.
		            IAsset asset = CreateAssetAndUploadSingleFile(AssetCreationOptions.None, 
		                defaultStorageName.Name, _singleInputFilePath);
		            Console.WriteLine("Created the asset in the {0} storage account", asset.StorageAccountName);
		            
		            // Create an output asset of the encoding job in the other storage account.
		            IAsset outputAsset = CreateEncodingJob(asset, notDefaultStroageName.Name, _singleInputFilePath);
		            if(outputAsset!=null)
		                Console.WriteLine("Created the output asset in the {0} storage account", outputAsset.StorageAccountName);
		
		        }
		
		        static public IAsset CreateAssetAndUploadSingleFile(AssetCreationOptions assetCreationOptions, string storageName, string singleFilePath)
		        {
		            var assetName = "UploadSingleFile_" + DateTime.UtcNow.ToString();
		            
		            // If you are creating an asset in the default storage account, you can omit the StorageName parameter.
		            var asset = _context.Assets.Create(assetName, storageName, assetCreationOptions);
		
		            var fileName = Path.GetFileName(singleFilePath);
		
		            var assetFile = asset.AssetFiles.Create(fileName);
		
		            Console.WriteLine("Created assetFile {0}", assetFile.Name);
		
		            assetFile.Upload(singleFilePath);
		            
		            Console.WriteLine("Done uploading {0}", assetFile.Name);
		
		            return asset;
		        }
		
		        static IAsset CreateEncodingJob(IAsset asset, string storageName, string inputMediaFilePath)
		        {
		            // Declare a new job.
		            IJob job = _context.Jobs.Create("My encoding job");
		            // Get a media processor reference, and pass to it the name of the 
		            // processor to use for the specific task.
		            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");
		
		            // Create a task with the encoding details, using a string preset.
		            ITask task = job.Tasks.AddNew("My encoding task",
		                processor,
		                "H264 Multiple Bitrate 720p",
		                Microsoft.WindowsAzure.MediaServices.Client.TaskOptions.ProtectedConfiguration);
		
		            // Specify the input asset to be encoded.
		            task.InputAssets.Add(asset);
		            // Add an output asset to contain the results of the job. 
		            // This output is specified as AssetCreationOptions.None, which 
		            // means the output asset is not encrypted. 
		            task.OutputAssets.AddNew("Output asset", storageName,
		                AssetCreationOptions.None);
		
		            // Use the following event handler to check job progress.  
		            job.StateChanged += new
		                    EventHandler<JobStateChangedEventArgs>(StateChanged);
		
		            // Launch the job.
		            job.Submit();
		
		            // Check job execution and wait for job to finish. 
		            Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
		            progressJobTask.Wait();
		
		            // Get an updated job reference.
		            job = GetJob(job.Id);
		
		            // If job state is Error the event handling 
		            // method for job progress should log errors.  Here we check 
		            // for error state and exit if needed.
		            if (job.State == JobState.Error)
		            {
		                Console.WriteLine("\nExiting method due to job error.");
		                return null;
		            }
		
		            // Get a reference to the output asset from the job.
		            IAsset outputAsset = job.OutputMediaAssets[0];
		
		            return outputAsset;
		        }
		
		
		        private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
		        {
		            var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
		                ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();
		
		            if (processor == null)
		                throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));
		
		            return processor;
		        }
		
		        private static void StateChanged(object sender, JobStateChangedEventArgs e)
		        {
		            Console.WriteLine("Job state changed event:");
		            Console.WriteLine("  Previous state: " + e.PreviousState);
		            Console.WriteLine("  Current state: " + e.CurrentState);
		
		            switch (e.CurrentState)
		            {
		                case JobState.Finished:
		                    Console.WriteLine();
		                    Console.WriteLine("********************");
		                    Console.WriteLine("Job is finished.");
		                    Console.WriteLine("Please wait while local tasks or downloads complete...");
		                    Console.WriteLine("********************");
		                    Console.WriteLine();
		                    Console.WriteLine();
		                    break;
		                case JobState.Canceling:
		                case JobState.Queued:
		                case JobState.Scheduled:
		                case JobState.Processing:
		                    Console.WriteLine("Please wait...\n");
		                    break;
		                case JobState.Canceled:
		                case JobState.Error:
		                    // Cast sender as a job.
		                    IJob job = (IJob)sender;
		                    // Display or log error details as needed.
		                    Console.WriteLine("An error occurred in {0}", job.Id);
		                    break;
		                default:
		                    break;
		            }
		        }
		
		        static IJob GetJob(string jobId)
		        {
		            // Use a Linq select query to get an updated 
		            // reference by Id. 
		            var jobInstance =
		                from j in _context.Jobs
		                where j.Id == jobId
		                select j;
		            // Return the job reference as an Ijob. 
		            IJob job = jobInstance.FirstOrDefault();
		
		            return job;
		        }
		    }
		}
 

##Rutas de aprendizaje de Servicios multimedia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Envío de comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0204_2016-->