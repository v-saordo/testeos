
<properties 
	pageTitle="Administración de activos y entidades relacionadas con el SDK de Servicios multimedia para .NET" 
	description="Obtenga información acerca de cómo administrar los recursos y las entidades relacionadas con el SDK de Servicios multimedia para .NET." 
	authors="juliako" 
	manager="dwrede" 
	editor="" 
	services="media-services" 
	documentationCenter=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
 	ms.date="02/11/2016"  
	ms.author="juliako"/>


#Administración de activos y entidades relacionadas con el SDK de Servicios multimedia para .NET


> [AZURE.SELECTOR]
- [.NET](media-services-dotnet-manage-entities.md)
- [REST](media-services-rest-manage-entities.md)


Este tema muestra cómo llevar a cabo las siguientes tareas de administración de Servicios multimedia:

- Obtención de una referencia de recurso 
- Obtención de una referencia de trabajo 
- Lista de todos los recursos 
- Lista de trabajos y recursos 
- Lista de todas las directivas de acceso 
- Enumerar todos los localizadores
- Enumeración de grandes colecciones de entidades
- Eliminación de un recurso 
- Eliminación de un trabajo 
- Eliminación de una directiva de acceso 

##Requisitos previos 

Consulte [Configuración del entorno](media-services-set-up-computer.md)

##Obtención de una referencia de recurso

Una tarea frecuente es obtener una referencia a un recurso existente en Servicios multimedia. En el ejemplo de código siguiente se muestra cómo puede obtener una referencia de recurso de la colección de recursos en el objeto de contexto del servidor mediante un Id. de recurso. En el ejemplo de código siguiente se usa una consulta Linq para obtener una referencia a un objeto IAsset existente.

	static IAsset GetAsset(string assetId)
	{
	    // Use a LINQ Select query to get an asset.
	    var assetInstance =
	        from a in _context.Assets
	        where a.Id == assetId
	        select a;
	    // Reference the asset as an IAsset.
	    IAsset asset = assetInstance.FirstOrDefault();
	
	    return asset;
	}

##Obtención de una referencia de trabajo

Cuando se trabaja con tareas de procesamiento en el código de Servicios multimedia, a menudo necesitará obtener una referencia a un trabajo existente basado en un identificador. En el ejemplo de código siguiente se muestra cómo obtener una referencia a un objeto IJob de la colección de trabajos. Advertencia Puede ser necesario obtener una referencia de trabajo cuando se inicia un trabajo de codificación que tarda mucho en ejecutarse y debe comprobar el estado del trabajo en un subproceso. En casos como éste, cuando el método devuelve un subproceso, deberá recuperar una referencia actualizada a un trabajo.

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

##Lista de todos los recursos

A medida que crece el número de recursos de almacenamiento, resulta útil mostrar una lista de los recursos. En el ejemplo de código siguiente se muestra cómo iterar a través de la colección de recursos en el objeto de contexto del servidor. Con cada recurso, el ejemplo de código también escribe algunos de sus valores de propiedad en la consola. Por ejemplo, cada recurso puede contener muchos archivos multimedia. El ejemplo de código escribe todos los archivos asociados con cada recurso.



	static void ListAssets()
	{
	    string waitMessage = "Building the list. This may take a few "
	        + "seconds to a few minutes depending on how many assets "
	        + "you have."
	        + Environment.NewLine + Environment.NewLine
	        + "Please wait..."
	        + Environment.NewLine;
	    Console.Write(waitMessage);
	
	    // Create a Stringbuilder to store the list that we build. 
	    StringBuilder builder = new StringBuilder();
	
	    foreach (IAsset asset in _context.Assets)
	    {
	        // Display the collection of assets.
	        builder.AppendLine("");
	        builder.AppendLine("******ASSET******");
	        builder.AppendLine("Asset ID: " + asset.Id);
	        builder.AppendLine("Name: " + asset.Name);
	        builder.AppendLine("==============");
	        builder.AppendLine("******ASSET FILES******");
	
	        // Display the files associated with each asset. 
	        foreach (IAssetFile fileItem in asset.AssetFiles)
	        {
	            builder.AppendLine("Name: " + fileItem.Name);
	            builder.AppendLine("Size: " + fileItem.ContentFileSize);
	            builder.AppendLine("==============");
	        }
	    }
	
	    // Display output in console.
	    Console.Write(builder.ToString());
	}

##Lista de trabajos y recursos

Una tarea relacionada importante es enumerar los recursos con sus respectivos trabajos asociados en Servicios multimedia. En el ejemplo de código siguiente se muestra cómo enumerar cada objeto IJob y, a continuación, para cada trabajo, muestra propiedades acerca del trabajo, todas las tareas relacionadas, todos los recursos de entrada y todos los recursos de salida. El código de este ejemplo puede ser útil para muchas otras tareas. Por ejemplo, si desea mostrar una lista de los recursos de salida de uno o más trabajos de codificación que ejecutó anteriormente, este código muestra cómo obtener acceso a los recursos de salida. Cuando tenga una referencia a un recurso de salida, puede entregar el contenido a otros usuarios o aplicaciones descargándolo o proporcionando direcciones URL.

Para obtener más información sobre las opciones de entrega de recursos, consulte [Entrega de recursos con el SDK de Servicios multimedia para .NET](media-services-deliver-streaming-content.md).

	// List all jobs on the server, and for each job, also list 
	// all tasks, all input assets, all output assets.

	static void ListJobsAndAssets()
	{
	    string waitMessage = "Building the list. This may take a few "
	        + "seconds to a few minutes depending on how many assets "
	        + "you have."
	        + Environment.NewLine + Environment.NewLine
	        + "Please wait..."
	        + Environment.NewLine;
	    Console.Write(waitMessage);
	
	    // Create a Stringbuilder to store the list that we build. 
	    StringBuilder builder = new StringBuilder();
	
	    foreach (IJob job in _context.Jobs)
	    {
	        // Display the collection of jobs on the server.
	        builder.AppendLine("");
	        builder.AppendLine("******JOB*******");
	        builder.AppendLine("Job ID: " + job.Id);
	        builder.AppendLine("Name: " + job.Name);
	        builder.AppendLine("State: " + job.State);
	        builder.AppendLine("Order: " + job.Priority);
	        builder.AppendLine("==============");
	
	
	        // For each job, display the associated tasks (a job  
	        // has one or more tasks). 
	        builder.AppendLine("******TASKS*******");
	        foreach (ITask task in job.Tasks)
	        {
	            builder.AppendLine("Task Id: " + task.Id);
	            builder.AppendLine("Name: " + task.Name);
	            builder.AppendLine("Progress: " + task.Progress);
	            builder.AppendLine("Configuration: " + task.Configuration);
	            if (task.ErrorDetails != null)
	            {
	                builder.AppendLine("Error: " + task.ErrorDetails);
	            }
	            builder.AppendLine("==============");
	        }
	
	        // For each job, display the list of input media assets.
	        builder.AppendLine("******JOB INPUT MEDIA ASSETS*******");
	        foreach (IAsset inputAsset in job.InputMediaAssets)
	        {
	
	            if (inputAsset != null)
	            {
	                builder.AppendLine("Input Asset Id: " + inputAsset.Id);
	                builder.AppendLine("Name: " + inputAsset.Name);
	                builder.AppendLine("==============");
	            }
	        }
	
	        // For each job, display the list of output media assets.
	        builder.AppendLine("******JOB OUTPUT MEDIA ASSETS*******");
	        foreach (IAsset theAsset in job.OutputMediaAssets)
	        {
	            if (theAsset != null)
	            {
	                builder.AppendLine("Output Asset Id: " + theAsset.Id);
	                builder.AppendLine("Name: " + theAsset.Name);
	                builder.AppendLine("==============");
	            }
	        }
	
	    }
	
	    // Display output in console.
	    Console.Write(builder.ToString());
	}

##Lista de todas las directivas de acceso

En Servicios multimedia, puede definir una directiva de acceso en un recurso o sus archivos. Una directiva de acceso define los permisos de un archivo o un recurso (tipo de acceso y la duración). En el código de Servicios multimedia, normalmente se define una directiva de acceso mediante la creación de un objeto IAccessPolicy y, a continuación, su asociación a un recurso existente. A continuación, cree un objeto ILocator, que permite proporcionar acceso directo a los recursos de Servicios multimedia. El proyecto de Visual Studio que acompaña a esta serie de documentación contiene varios ejemplos de código que muestran cómo crear y asignar directivas de acceso y localizadores a los activos.

En el ejemplo de código siguiente se muestra cómo enumerar todas las directivas de acceso del servidor y se muestra el tipo de permisos asociado a cada uno. Otra manera útil para ver las directivas de acceso es enumerar todos los objetos de ILocator en el servidor y, a continuación, para cada localizador, puede enumerar su directiva de acceso asociada mediante su propiedad AccessPolicy.

	static void ListAllPolicies()
	{
	    foreach (IAccessPolicy policy in _context.AccessPolicies)
	    {
	        Console.WriteLine("");
	        Console.WriteLine("Name:  " + policy.Name);
	        Console.WriteLine("ID:  " + policy.Id);
	        Console.WriteLine("Permissions: " + policy.Permissions);
	        Console.WriteLine("==============");
	
	    }
	}

##Enumerar todos los localizadores

Un localizador es una dirección URL que proporciona una ruta de acceso directo para tener acceso a un recurso, junto con los permisos para el recurso definidos por la directiva de acceso asociada del localizador. Cada activo puede tener una colección de objetos ILocator asociados a él en su propiedad Locators. El contexto de servidor también tiene una colección de localizadores que contiene todos los localizadores.

En el ejemplo de código siguiente se enumeran todos los localizadores del servidor. Para cada localizador, muestra el identificador para la directiva de acceso y recursos relacionado. También muestra el tipo de permisos, la fecha de caducidad y la ruta de acceso completa al recurso.

Tenga en cuenta que una ruta de acceso del localizador a un recurso solo es una dirección URL base para el recurso. Para crear una ruta directa a archivos individuales a los que podría desplazarse un usuario o una aplicación, el código debe agregar la ruta de acceso del archivo específico a la ruta del localizador. Para obtener más información sobre cómo hacerlo, consulte el tema [Entrega de recursos con el SDK de Servicios multimedia para .NET](media-services-deliver-streaming-content.md).

	static void ListAllLocators()
	{
	    foreach (ILocator locator in _context.Locators)
	    {
	        Console.WriteLine("***********");
	        Console.WriteLine("Locator Id: " + locator.Id);
	        Console.WriteLine("Locator asset Id: " + locator.AssetId);
	        Console.WriteLine("Locator access policy Id: " + locator.AccessPolicyId);
	        Console.WriteLine("Access policy permissions: " + locator.AccessPolicy.Permissions);
	        Console.WriteLine("Locator expiration: " + locator.ExpirationDateTime);
	        // The locator path is the base or parent path (with included permissions) to access  
	        // the media content of an asset. To create a full URL to a specific media file, take 
	        // the locator path and then append a file name and info as needed.  
	        Console.WriteLine("Locator base path: " + locator.Path);
	        Console.WriteLine("");
	    }
	}

## Enumeración de grandes colecciones de entidades

Al consultar entidades, hay un límite de 1000 entidades devueltas a la vez, porque la REST v2 pública limita los resultados de consulta a 1000. Debe usar Skip y Take al enumerar grandes colecciones de entidades.
	
La siguiente función recorre todos los trabajos en la cuenta de Servicios multimedia proporcionada. Servicios multimedia devuelve 1000 trabajos en la colección de trabajos. La función usa Skip y Take para asegurarse de que se enumeran todos los trabajos (en el caso de que tenga más de 1000 trabajos en su cuenta).
	
	static void ProcessJobs()
	{
	    try
	    {
	
	        int skipSize = 0;
	        int batchSize = 1000;
	        int currentBatch = 0;
	
	        while (true)
	        {
	            // Loop through all Jobs (1000 at a time) in the Media Services account
	            IQueryable _jobsCollectionQuery = _context.Jobs.Skip(skipSize).Take(batchSize);
	            foreach (IJob job in _jobsCollectionQuery)
	            {
	                currentBatch++;
	                Console.WriteLine("Processing Job Id:" + job.Id);
	            }
	
	            if (currentBatch == batchSize)
	            {
	                skipSize += batchSize;
	                currentBatch = 0;
	            }
	            else
	            {
	                break;
	            }
	        }
	    }
	    catch (Exception ex)
	    {
	        Console.WriteLine(ex.Message);
	    }
	}

##Eliminación de un recurso

En el ejemplo siguiente se elimina un recurso.

	static void DeleteAsset( IAsset asset)
	{
	    // delete the asset
	    asset.Delete();
	
	    // Verify asset deletion
	    if (GetAsset(asset.Id) == null)
	        Console.WriteLine("Deleted the Asset");
	
	}

##Eliminación de un trabajo

Para eliminar un trabajo, debe comprobar el estado del trabajo, como se indica en la propiedad State. Los trabajos finalizados o cancelados pueden eliminarse, mientras que los trabajos que se encuentran en otros estados, por ejemplo, en cola, programado o en proceso, se deben cancelar primero y, a continuación, se pueden eliminar. En el ejemplo de código siguiente se muestra un método para eliminar un trabajo; para ello, comprueba el estado de los trabajos y, a continuación, los elimina cuando el estado es finalizado o cancelado. Este código depende de la sección anterior de este tema para obtener una referencia a un trabajo: Obtención de una referencia de trabajo.

	static void DeleteJob(string jobId)
	{
	    bool jobDeleted = false;
	
	    while (!jobDeleted)
	    {
	        // Get an updated job reference.  
	        IJob job = GetJob(jobId);
	
	        // Check and handle various possible job states. You can 
	        // only delete a job whose state is Finished, Error, or Canceled.   
	        // You can cancel jobs that are Queued, Scheduled, or Processing,  
	        // and then delete after they are canceled.
	        switch (job.State)
	        {
	            case JobState.Finished:
	            case JobState.Canceled:
	            case JobState.Error:
	                // Job errors should already be logged by polling or event 
	                // handling methods such as CheckJobProgress or StateChanged.
	                // You can also call job.DeleteAsync to do async deletes.
	                job.Delete();
	                Console.WriteLine("Job has been deleted.");
	                jobDeleted = true;
	                break;
	            case JobState.Canceling:
	                Console.WriteLine("Job is cancelling and will be deleted "
	                    + "when finished.");
	                Console.WriteLine("Wait while job finishes canceling...");
	                Thread.Sleep(5000);
	                break;
	            case JobState.Queued:
	            case JobState.Scheduled:
	            case JobState.Processing:
	                job.Cancel();
	                Console.WriteLine("Job is scheduled or processing and will "
	                    + "be deleted.");
	                break;
	            default:
	                break;
	        }
	
	    }
	}


##Eliminación de una directiva de acceso

En el ejemplo de código siguiente se muestra cómo obtener una referencia a una directiva de acceso basada en un Id. de directiva y, a continuación, eliminar la directiva.

	static void DeleteAccessPolicy(string existingPolicyId)
	{
	    // To delete a specific access policy, get a reference to the policy.  
	    // based on the policy Id passed to the method.
	    var policyInstance =
	            from p in _context.AccessPolicies
	            where p.Id == existingPolicyId
	            select p;
	    IAccessPolicy policy = policyInstance.FirstOrDefault();
	
	    policy.Delete();
	
	}
	


##Rutas de aprendizaje de Servicios multimedia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Envío de comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0218_2016-->