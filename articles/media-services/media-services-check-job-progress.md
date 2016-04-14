<properties 
	pageTitle="Comprobación del progreso del trabajo mediante .NET" 
	description="Aprenda a usar el código del controlador de eventos para realizar el seguimiento del progreso del trabajo y enviar actualizaciones de estado. Los ejemplos de código están escritos en C# y utilizan el SDK de Servicios multimedia para .NET." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/03/2016"    
	ms.author="juliako"/>

#Comprobación del progreso del trabajo

> [AZURE.SELECTOR]
- [Portal](media-services-portal-check-job-progress.md)
- [.NET](media-services-check-job-progress.md)
- [REST](media-services-rest-check-job-progress.md)

Al ejecutar trabajos, muchas veces se requiere una forma de hacer un seguimiento al progreso del trabajo. Puede comprobar el progreso [definiendo un controlador de eventos StateChanged](#statechange_event_handler) o [utilizando el almacenamiento de la cola de Azure para supervisar las notificaciones sobre trabajos de servicios multimedia](#check_progress_with_queues). Ambos métodos se describen en este tema.

##<a id="statechange_event_handler"></a>Definición de controlador de eventos de StateChanged para supervisar el progreso del trabajo

El siguiente ejemplo de código define el controlador de eventos StateChanged. Este controlador de eventos hace un seguimiento del progreso del trabajo y proporciona un estado actualizado, según el estado. El código define también el método LogJobStop. Este método auxiliar registra los detalles del error.

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
	            LogJobStop(job.Id);
	            break;
	        default:
	            break;
	    }
	}
	
	private static void LogJobStop(string jobId)
	{
	    StringBuilder builder = new StringBuilder();
	    IJob job = GetJob(jobId);
	
	    builder.AppendLine("\nThe job stopped due to cancellation or an error.");
	    builder.AppendLine("***************************");
	    builder.AppendLine("Job ID: " + job.Id);
	    builder.AppendLine("Job Name: " + job.Name);
	    builder.AppendLine("Job State: " + job.State.ToString());
	    builder.AppendLine("Job started (server UTC time): " + job.StartTime.ToString());
	    builder.AppendLine("Media Services account name: " + _accountName);
	    builder.AppendLine("Media Services account location: " + _accountLocation);
	    // Log job errors if they exist.  
	    if (job.State == JobState.Error)
	    {
	        builder.Append("Error Details: \n");
	        foreach (ITask task in job.Tasks)
	        {
	            foreach (ErrorDetail detail in task.ErrorDetails)
	            {
	                builder.AppendLine("  Task Id: " + task.Id);
	                builder.AppendLine("    Error Code: " + detail.Code);
	                builder.AppendLine("    Error Message: " + detail.Message + "\n");
	            }
	        }
	    }
	    builder.AppendLine("***************************\n");
	    // Write the output to a local file and to the console. The template 
	    // for an error output file is:  JobStop-{JobId}.txt
	    string outputFile = _outputFilesFolder + @"\JobStop-" + JobIdAsFileName(job.Id) + ".txt";
	    WriteToFile(outputFile, builder.ToString());
	    Console.Write(builder.ToString());
	}
	
	private static string JobIdAsFileName(string jobID)
	{
	    return jobID.Replace(":", "_");
	}



##<a id="check_progress_with_queues"></a>Uso del almacenamiento de la cola de Azure para supervisar las notificaciones sobre trabajos de Servicios multimedia

Servicios multimedia de Microsoft Azure tiene la capacidad de entregar mensajes de notificación al [almacenamiento en cola de Azure](../storage-dotnet-how-to-use-queues.md#what-is) cuando se procesar trabajos multimedia. Este tema muestra cómo obtener estos mensajes de notificación del almacenamiento en cola.

Se puede obtener acceso a los mensajes entregados al almacenamiento de cola desde cualquier lugar del mundo. La arquitectura de mensajería en cola de Azure es fiable y altamente escalable. El almacenamiento en cola de sondeo es preferible a otros métodos.

Un escenario común para escuchar notificaciones de Servicios multimedia es si va a desarrollar un sistema de administración de contenido que necesita realizar alguna tarea adicional después de que se complete un trabajo de codificación (por ejemplo, la activación del siguiente paso del flujo de trabajo o la publicación de contenido).

###Consideraciones

Tenga en cuenta lo siguiente al desarrollar aplicaciones de Servicios multimedia que usen la cola de almacenamiento de Azure.

- El servicio de colas no ofrece ninguna garantía de entrega ordenada de tipo primero en entrar, primero en salir (FIFO). Para obtener más información, consulte [Colas de Azure y Colas de Bus de servicio de Azure: comparación y diferencias](https://msdn.microsoft.com/library/azure/hh767287.aspx).
- Colas de almacenamiento de Azure no es un servicio push; tiene que sondear la cola. 
- Puede tener cualquier número de colas. Para obtener más información, consulte la [API de REST del servicio de cola](https://msdn.microsoft.com/library/azure/dd179363.aspx).
- Colas de almacenamiento de Azure tiene algunas limitaciones y particularidades que se describen en el siguiente artículo: [Colas de Azure y Colas de Bus de servicio de Azure: comparación y diferencias](https://msdn.microsoft.com/library/azure/hh767287.aspx).

###Ejemplo de código

El ejemplo de código de esta sección realiza lo siguiente:

1. Define la clase **EncodingJobMessage** que se asigna al formato de mensaje de notificación. El código deserializa los mensajes recibidos de la cola en objetos del tipo **EncodingJobMessage**.
1. Carga la información de cuenta de almacenamiento y Servicios multimedia del archivo app.config. Utiliza esta información para crear los objetos **CloudMediaContext** y **CloudQueue**.
1. Crea la cola que va a recibir los mensajes de notificación sobre el trabajo de codificación.
1. Crea el extremo de notificación que se asigna a la cola.
1. Adjunta el extremo de notificación al trabajo y envía el trabajo de codificación. Puede tener varios extremos de notificación adjuntos a un trabajo.
1. En este ejemplo, solo estamos interesados en los estados finales del procesamiento de trabajos, por lo que pasamos **NotificationJobState.FinalStatesOnly** al método **AddNew**. 
		
		job.JobNotificationSubscriptions.AddNew(NotificationJobState.FinalStatesOnly, _notificationEndPoint);
1. Si se pasa NotificationJobState.All es de esperar que se obtengan todas las notificaciones de cambio de estado: En cola -> Programado -> Procesando -> Finalizado. Sin embargo, como se indicó anteriormente, el servicio de Colas de almacenamiento de Azure no garantiza la entrega ordenada. Puede utilizar la propiedad de marca de tiempo (definida en el tipo EncodingJobMessage en el ejemplo siguiente) para ordenar los mensajes. Es posible que reciba mensajes de notificación duplicados. Utilice la propiedad ETag (definida en el tipo de EncodingJobMessage) para comprobar si existen duplicados. Tenga en cuenta que también es posible que se omitan algunas notificaciones de cambio de estado. 
1. Espera a que el trabajo llegue al estado Finalizado comprobando la cola cada 10 segundos. Elimina los mensajes una vez que se hayan procesado.
1. Elimina la cola y el extremo de notificación.

>[AZURE.NOTE]La manera recomendada de supervisar el estado de un trabajo es escuchar los mensajes de notificación, tal como se muestra en el ejemplo siguiente.
>
>Como alternativa, puede comprobar el estado de un trabajo mediante el uso de la propiedad **IJob.State**. Tenga en cuenta que es posible que llegue un mensaje de notificación sobre la finalización de un trabajo antes de que el estado en **IJob** se establezca en **Finalizado**. La propiedad **IJob.State** reflejará el estado exacto con un ligero retraso.

	
	using System;
	using System.Linq;
	using System.Configuration;
	using System.IO;
	using System.Text;
	using System.Threading;
	using System.Threading.Tasks;
	using System.Collections.Generic;
	using Microsoft.WindowsAzure.MediaServices.Client;
	using System.Web;
	using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage.Queue;
	using System.Runtime.Serialization.Json;
	
	namespace JobNotification
	{
	    public class EncodingJobMessage
	    {
	        // MessageVersion is used for version control. 
	        public String MessageVersion { get; set; }
	    
	        // Type of the event. Valid values are 
	        // JobStateChange and NotificationEndpointRegistration.
	        public String EventType { get; set; }
	    
	        // ETag is used to help the customer detect if 
	        // the message is a duplicate of another message previously sent.
	        public String ETag { get; set; }
	    
	        // Time of occurrence of the event.
	        public String TimeStamp { get; set; }
	
	        // Collection of values specific to the event.
	
	        // For the JobStateChange event the values are:
	        //     JobId - Id of the Job that triggered the notification.
	        //     NewState- The new state of the Job. Valid values are:
	        //          Scheduled, Processing, Canceling, Cancelled, Error, Finished
	        //     OldState- The old state of the Job. Valid values are:
	        //          Scheduled, Processing, Canceling, Cancelled, Error, Finished
	
	        // For the NotificationEndpointRegistration event the values are:
	        //     NotificationEndpointId- Id of the NotificationEndpoint 
	        //          that triggered the notification.
	        //     State- The state of the Endpoint. 
	        //          Valid values are: Registered and Unregistered.
	
	        public IDictionary<string, object> Properties { get; set; }
	    }
	
	    class Program
	    {
	        private static CloudMediaContext _context = null;
	        private static CloudQueue _queue = null;
	        private static INotificationEndPoint _notificationEndPoint = null;
	
	        private static readonly string _singleInputMp4Path =
	            Path.GetFullPath(@"C:\supportFiles\multifile\BigBuckBunny.mp4");
	
	        static void Main(string[] args)
	        {
	            // Get the values from app.config file.
	            string mediaServicesAccountName = ConfigurationManager.AppSettings["MediaServicesAccountName"];
	            string mediaServicesAccountKey = ConfigurationManager.AppSettings["MediaServicesAccountKey"];
	            string storageConnectionString = ConfigurationManager.AppSettings["StorageConnectionString"];
	
	
	            string endPointAddress = Guid.NewGuid().ToString();
	
	            // Create the context. 
	            _context = new CloudMediaContext(mediaServicesAccountName, mediaServicesAccountKey);
	
	            // Create the queue that will be receiving the notification messages.
	            _queue = CreateQueue(storageConnectionString, endPointAddress);
	
	            // Create the notification point that is mapped to the queue.
	            _notificationEndPoint = 
	                    _context.NotificationEndPoints.Create(
	                    Guid.NewGuid().ToString(), NotificationEndPointType.AzureQueue, endPointAddress);
	
	
	            if (_notificationEndPoint != null)
	            {
	                IJob job = SubmitEncodingJobWithNotificationEndPoint(_singleInputMp4Path);
	                WaitForJobToReachedFinishedState(job.Id);
	            }
	
	            // Clean up.
	            _queue.Delete();      
	            _notificationEndPoint.Delete();
	       }
	
	
	        static public CloudQueue CreateQueue(string storageAccountConnectionString, string endPointAddress)
	        {
	            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(storageAccountConnectionString);
	
	            // Create the queue client
	            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
	
	            // Retrieve a reference to a queue
	            CloudQueue queue = queueClient.GetQueueReference(endPointAddress);
	
	            // Create the queue if it doesn't already exist
	            queue.CreateIfNotExists();
	
	            return queue;
	        }
	 
	
	        public static IJob SubmitEncodingJobWithNotificationEndPoint(string inputMediaFilePath)
	        {
	            // Declare a new job.
	            IJob job = _context.Jobs.Create("My MP4 to Smooth Streaming encoding job");
	
	            //Create an encrypted asset and upload the mp4. 
	            IAsset asset = CreateAssetAndUploadSingleFile(AssetCreationOptions.StorageEncrypted, 
	                inputMediaFilePath);
	
	            // Get a media processor reference, and pass to it the name of the 
	            // processor to use for the specific task.
	            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");
	
	            // Create a task with the conversion details, using a configuration file. 
	            ITask task = job.Tasks.AddNew("My encoding Task",
	                processor,
	                "H264 Multiple Bitrate 720p",
	                Microsoft.WindowsAzure.MediaServices.Client.TaskOptions.None);
	
	            // Specify the input asset to be encoded.
	            task.InputAssets.Add(asset);
	
	            // Add an output asset to contain the results of the job.
	            task.OutputAssets.AddNew("Output asset",
	                AssetCreationOptions.None);
	
	            // Add a notification point to the job. You can add multiple notification points.  
	            job.JobNotificationSubscriptions.AddNew(NotificationJobState.FinalStatesOnly, 
	                _notificationEndPoint);
	
	            job.Submit();
	
	            return job;
	        }
	
	        public static void WaitForJobToReachedFinishedState(string jobId)
	        {
	            int expectedState = (int)JobState.Finished;
	            int timeOutInSeconds = 600;
	
	            bool jobReachedExpectedState = false;
	            DateTime startTime = DateTime.Now;
	            int jobState = -1;
	
	            while (!jobReachedExpectedState)
	            {
	                // Specify how often you want to get messages from the queue.
	                Thread.Sleep(TimeSpan.FromSeconds(10));
	
	                foreach (var message in _queue.GetMessages(10))
	                {
	                    using (Stream stream = new MemoryStream(message.AsBytes))
	                    {
	                        DataContractJsonSerializerSettings settings = new DataContractJsonSerializerSettings();
	                        settings.UseSimpleDictionaryFormat = true;
	                        DataContractJsonSerializer ser = new DataContractJsonSerializer(typeof(EncodingJobMessage), settings);
	                        EncodingJobMessage encodingJobMsg = (EncodingJobMessage)ser.ReadObject(stream);
	
	                        Console.WriteLine();
	
	                        // Display the message information.
	                        Console.WriteLine("EventType: {0}", encodingJobMsg.EventType);
	                        Console.WriteLine("MessageVersion: {0}", encodingJobMsg.MessageVersion);
	                        Console.WriteLine("ETag: {0}", encodingJobMsg.ETag);
	                        Console.WriteLine("TimeStamp: {0}", encodingJobMsg.TimeStamp);
	                        foreach (var property in encodingJobMsg.Properties)
	                        {
	                            Console.WriteLine("    {0}: {1}", property.Key, property.Value);
	                        }
	
	                        // We are only interested in messages 
	                        // where EventType is "JobStateChange".
	                        if (encodingJobMsg.EventType == "JobStateChange")
	                        {
	                            string JobId = (String)encodingJobMsg.Properties.Where(j => j.Key == "JobId").FirstOrDefault().Value;
	                            if (JobId == jobId)
	                            {
	                                string oldJobStateStr = (String)encodingJobMsg.Properties.
	                                                            Where(j => j.Key == "OldState").FirstOrDefault().Value;
	                                string newJobStateStr = (String)encodingJobMsg.Properties.
	                                                            Where(j => j.Key == "NewState").FirstOrDefault().Value;
	
	                                JobState oldJobState = (JobState)Enum.Parse(typeof(JobState), oldJobStateStr);
	                                JobState newJobState = (JobState)Enum.Parse(typeof(JobState), newJobStateStr);
	
	                                if (newJobState == (JobState)expectedState)
	                                {
	                                    Console.WriteLine("job with Id: {0} reached expected state: {1}", 
	                                        jobId, newJobState);
	                                    jobReachedExpectedState = true;
	                                    break;
	                                }
	                            }
	                        }
	                    }
	                    // Delete the message after we've read it.
	                    _queue.DeleteMessage(message);
	                }
	
	                // Wait until timeout
	                TimeSpan timeDiff = DateTime.Now - startTime;
	                bool timedOut = (timeDiff.TotalSeconds > timeOutInSeconds);
	                if (timedOut)
	                {
	                    Console.WriteLine(@"Timeout for checking job notification messages, 
	                                        latest found state ='{0}', wait time = {1} secs",
	                        jobState,
	                        timeDiff.TotalSeconds);
	
	                    throw new TimeoutException();
	                }
	            }
	        }
	   
	        static private IAsset CreateAssetAndUploadSingleFile(AssetCreationOptions assetCreationOptions, string singleFilePath)
	        {
	            var asset = _context.Assets.Create("UploadSingleFile_" + DateTime.UtcNow.ToString(), 
	                assetCreationOptions);
	
	            var fileName = Path.GetFileName(singleFilePath);
	
	            var assetFile = asset.AssetFiles.Create(fileName);
	
	            Console.WriteLine("Created assetFile {0}", assetFile.Name);
	            Console.WriteLine("Upload {0}", assetFile.Name);
	
	            assetFile.Upload(singleFilePath);
	            Console.WriteLine("Done uploading of {0}", assetFile.Name);
	
	            return asset;
	        }
	
	        static private IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
	        {
	            var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
	                ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();
	
	            if (processor == null)
	                throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));
	
	            return processor;
	        }
	    }
	}

El ejemplo anterior genera el siguiente resultado. Los valores variarán.
	
	Created assetFile BigBuckBunny.mp4
	Upload BigBuckBunny.mp4
	Done uploading of BigBuckBunny.mp4
	
	EventType: NotificationEndPointRegistration
	MessageVersion: 1.0
	ETag: e0238957a9b25bdf3351a88e57978d6a81a84527fad03bc23861dbe28ab293f6
	TimeStamp: 2013-05-14T20:22:37
	    NotificationEndPointId: nb:nepid:UUID:d6af9412-2488-45b2-ba1f-6e0ade6dbc27
	    State: Registered
	    Name: dde957b2-006e-41f2-9869-a978870ac620
	    Created: 2013-05-14T20:22:35
	
	EventType: JobStateChange
	MessageVersion: 1.0
	ETag: 4e381f37c2d844bde06ace650310284d6928b1e50101d82d1b56220cfcb6076c
	TimeStamp: 2013-05-14T20:24:40
	    JobId: nb:jid:UUID:526291de-f166-be47-b62a-11ffe6d4be54
	    JobName: My MP4 to Smooth Streaming encoding job
	    NewState: Finished
	    OldState: Processing
	    AccountName: westeuropewamsaccount
	job with Id: nb:jid:UUID:526291de-f166-be47-b62a-11ffe6d4be54 reached expected 
	State: Finished
	

##Rutas de aprendizaje de Servicios multimedia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Envío de comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0211_2016-->