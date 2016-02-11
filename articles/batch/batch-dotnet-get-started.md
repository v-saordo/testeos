<properties
	pageTitle="Tutorial: Introducción a la biblioteca de .NET de Lote de Azure| Microsoft Azure"
	description="Aprenda los conceptos básicos sobre Lote de Azure y cómo desarrollar para el servicio Lote con un escenario simple."
	services="batch"
	documentationCenter=".net"
	authors="mmacy"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.tgt_pltfrm="na"
	ms.workload="big-compute"
	ms.date="01/08/2016"
	ms.author="marsma"/>

# Introducción a la biblioteca de Lote de Azure para .NET  

En este artículo, aprenderá los datos básicos de [Lote de Azure ][azure_batch] y de la biblioteca de [.NET de Lote][net_api] a medida que examinamos paso a paso una aplicación de ejemplo en C#. Examinaremos la forma en que esta aplicación de ejemplo saca provecho del servicio Lote para procesar una carga de trabajo paralela en la nube y cómo interactúa con [Almacenamiento de Azure](./../storage/storage-introduction.md) para almacenar provisionalmente y recuperar archivos. Aprenderá técnicas comunes del flujo de trabajo de la aplicación Lote. También obtendrá conocimientos básicos de los componentes principales de Lote, como trabajos, tareas, grupos y nodos de proceso.

![Flujo de trabajo de soluciones de Lote (básico)][11]<br/>

## Requisitos previos

En este artículo se asume que tiene conocimientos prácticos de C# y Visual Studio. También se asume que puede cumplir los requisitos para la creación de cuentas que se especifican a continuación en Azure y los servicios Lote y Almacenamiento.

### Cuentas

- **Cuenta de Azure**: si aún no dispone de una suscripción a Azure, puede crear una cuenta de evaluación gratuita en cuestión de minutos en [Evaluación gratuita de Azure](http://azure.microsoft.com/pricing/free-trial/).
- **Cuenta de Lote**: una vez que tenga una suscripción a Azure, [cree y administre de una cuenta de Lote de Azure](batch-account-create-portal.md).
- **Cuenta de Almacenamiento**: consulte la sección "Crear una cuenta de almacenamiento" en [Acerca de las cuentas de almacenamiento de Azure](../storage-create-storage-account.md).

### Visual Studio

Para generar el proyecto de ejemplo, debe tener **Visual Studio 2013** o **Visual Studio 2015**. En [Información general de los productos de Visual Studio 2015][visual_studio], puede encontrar las versiones de evaluación gratuita de Visual Studio.

### Ejemplo de código de *DotNetTutorial*

El ejemplo [DotNetTutorial][github_dotnettutorial] es uno de los muchos ejemplos de código que se encuentra en el repositorio [azure-batch-samples][github_samples] en GitHub. Para descargar el ejemplo, haga clic en el botón **Download ZIP** (Descargar ZIP) de la página principal del repositorio o haga clic en el vínculo de descarga directa [azure-batch-samples-master.zip][github_samples_zip]. Una vez extraído el contenido del archivo ZIP, encontrará la solución en la carpeta siguiente:

`\azure-batch-samples\CSharp\ArticleProjects\DotNetTutorial`

### Explorador de Lote de Azure (opcional)

El [Explorador de Lote de Azure][github_batchexplorer] es una utilidad gratuita que se incluye en el repositorio [azure-batch-samples][github_samples] de GitHub. Aunque el Explorador de Lote no se requiere para completar este tutorial, se recomienda encarecidamente que lo use en la depuración y administración de las entidades de su cuenta de Lote. En la entrada del blog [Azure Batch Explorer Sample Walkthrough][batch_explorer_blog] (Tutorial de ejemplo del Explorador de Lote de Azure), encontrará información acerca de una versión anterior del Explorador de Lote.

## Información general del proyecto de ejemplo DotNetTutorial

El código de ejemplo *DotNetTutorial* es una solución de Visual Studio 2013 que consta de dos proyectos: **DotNetTutorial** y **TaskApplication**.

- **DotNetTutorial** es la aplicación cliente que interactúa con los servicios Lote y Almacenamiento para ejecutar una carga de trabajo paralela en los nodos de proceso (máquinas virtuales). DotNetTutorial se ejecuta en la estación de trabajo local.

- **TaskApplication** es el programa que se ejecuta en los nodos de proceso de Azure para realizar el trabajo real. En el ejemplo, `TaskApplication.exe` analiza el texto de un archivo descargado de Almacenamiento de Azure (el archivo de entrada). Luego genera un archivo de texto (el archivo de salida) que contiene una lista de las tres palabras que más veces aparecen en el archivo de entrada. Después de crear el archivo de salida, TaskApplication lo carga en Almacenamiento de Azure. Esto hace que esté disponible para la aplicación cliente para su descarga. El proyecto TaskApplication se ejecuta en paralelo en varios nodos de ejecución en el servicio Lote.

El siguiente diagrama ilustra las operaciones principales que realiza la aplicación cliente, *DotNetTutorial*, y la aplicación que ejecutan las tareas, *TaskApplication*. Este flujo de trabajo básico es típico de muchas soluciones de proceso que se crean con Lote. Aunque no muestra todas las características disponibles en el servicio Lote, casi todos los escenarios de Lote incluyen procesos similares.

![Flujo de trabajo de ejemplo de Lote][8]<br/>

**Paso 1.** Crear **contenedores** en Almacenamiento de blobs de Azure.<br/> **Paso 2:** Cargar una aplicación de tarea y archivos de entrada en los contenedores.<br/> **Paso 3:** Crear un **grupo** de Lote.<br/> &nbsp;&nbsp;&nbsp;&nbsp;**3a.** El grupo **StartTask** descarga los archivos binarios de la tarea (TaskApplication) en los nodos cuando se unen al grupo.<br/> **Paso 4** Crear un **trabajo** de Lote.<br/> **Paso 5.** Agregar **tareas** al trabajo<br/> &nbsp;&nbsp;&nbsp;&nbsp;**5a.** Las tareas se programan para ejecutarse en los nodos.<br/> &nbsp;&nbsp;&nbsp;&nbsp;**5b.** Cada tarea descarga sus datos de entrada de Almacenamiento de Azure y después comienza la ejecución.<br/> **Paso 6.** Supervisar tareas.<br/> &nbsp;&nbsp;&nbsp;&nbsp;**6a.** A medida que se completan las tareas, cargan sus datos de salida en Almacenamiento de Azure<br/> **Paso 7.** Descargar el resultado de la tarea de Almacenamiento

Como se ha indicado, no todas las soluciones de Lote realizarán estos mismos pasos y pueden incluir muchos más, pero la aplicación de ejemplo *DotNetTutorial* muestra los procesos comunes que se encuentran en una solución de Lote.

## Generación del proyecto de ejemplo *DotNetTutorial*

Antes de ejecutar correctamente el ejemplo, debe especificar las credenciales de las cuentas de Lote y de Almacenamiento en el archivo `Program.cs` del proyecto *DotNetTutorial*. Si aún no lo ha hecho, abra la solución en Visual Studio, para lo que debe hacer clic en el archivo de solución `DotNetTutorial.sln`. O bien ábralo desde Visual Studio, para lo que debe usar el menú **Archivo > Abrir > Proyecto o solución**.

Abra `Program.cs` en el proyecto *DotNetTutorial*. Luego, agregue las credenciales en la parte superior del archivo como se ha especificado:

```
// Update the Batch and Storage account credential strings below with the values unique to your accounts.
// These are used when constructing connection strings for the Batch and Storage client objects.

// Batch account credentials
private const string BatchAccountName = "";
private const string BatchAccountKey  = "";
private const string BatchAccountUrl  = "";

// Storage account credentials
private const string StorageAccountName = "";
private const string StorageAccountKey  = "";
```

Las credenciales de las cuenta de Lote y de Almacenamiento se pueden encontrar en la hoja de cuentas de cada servicio en el [Portal de Azure][azure_portal]:

![Credenciales de Lote en el portal][9] ![Credenciales de Almacenamiento en el portal][10]<br/>

Tras actualizar el proyecto con sus credenciales, haga clic con el botón derecho en la solución en el Explorador de soluciones y haga clic en **Generar solución**. Si se le solicita, confirme la restauración de los paquetes NuGet.

> [AZURE.TIP] Si los paquetes NuGet no se restauran automáticamente o ve errores relativos a un error al restaurarlos, asegúrese de que tiene instalado el [Administrador de paquetes NuGet][nuget_packagemgr] y habilite la descarga de los paquetes que falten. Consulte [Enabling Package Restore During Build][nuget_restore] (Habilitación de la restauración de paquetes durante la compilación) para habilitar la descarga de paquetes.

En las secciones siguientes, se desglosarán los pasos que lleva a cabo la aplicación de ejemplo para procesar una carga de trabajo en el servicio Lote y se explicarán dichos pasos con detalle. Es aconsejable consultar la solución abierta en Visual Studio a medida que se avanza en este artículo, ya que no se tratan todas las líneas de código del ejemplo.

Para comenzar con el paso 1, navegue a la parte superior del método `MainAsync` en el archivo `Program.cs` del proyecto *DotNetTutorial*. Cada paso descrito a continuación sigue aproximadamente la progresión de las llamadas al método en `MainAsync`.

## Paso 1: Crear contenedores de Almacenamiento

![Crear contenedores en Almacenamiento de Azure][1] <br/>

Lote incluye compatibilidad integrada con la interacción con Almacenamiento de Azure. Los contenedores de la cuenta de Almacenamiento proporcionarán las tareas que se ejecutan en la cuenta de Lote con los archivos que se necesitan para ejecutarlas. Los contenedores también proporcionan un lugar para almacenar los datos de salida que producen las tareas. Lo primero que hace la aplicación cliente *DotNetTutorial* es crear tres contenedores en [Almacenamiento de blobs de Azure](./../storage/storage-introduction.md):

- **application**: este contenedor albergará la aplicación que ejecutarán las tareas, así como todas sus dependencias, como archivos DLL.
- **input**: las tareas descargarán del contenedor *input* los archivos de datos que se van a procesar.
- **output**: cuando las tareas completan el procesamiento de los archivos de entrada, cargan los resultados en el contenedor *output*.

Para interactuar con una cuenta de Almacenamiento y crear contenedores, se usa la [Biblioteca de cliente de Almacenamiento de Azure para .NET][net_api_storage] para crear una referencia a la cuenta con [CloudStorageAccount][net_cloudstorageaccount] (y a partir de ahí, se crea un cliente de blobs, [CloudBlobClient][net_cloudblobclient]):

```
// Construct the Storage account connection string
string storageConnectionString = String.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}",
                                                StorageAccountName, StorageAccountKey);

// Retrieve the storage account
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(storageConnectionString);

// Create the blob client, for use in obtaining references to blob storage containers
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
```

Usamos la referencia `blobClient` en toda la aplicación y la pasamos como parámetro a varios métodos. Un ejemplo de esto es el bloqueo de código que sigue inmediatamente a lo anterior, donde llamamos a `CreateContainerIfNotExistAsync` para crear realmente los contenedores.

```
// Use the blob client to create the containers in Azure Storage if they don't yet exist
const string appContainerName    = "application";
const string inputContainerName  = "input";
const string outputContainerName = "output";
await CreateContainerIfNotExistAsync(blobClient, appContainerName);
await CreateContainerIfNotExistAsync(blobClient, inputContainerName);
await CreateContainerIfNotExistAsync(blobClient, outputContainerName);
```

```
private static async Task CreateContainerIfNotExistAsync(CloudBlobClient blobClient, string containerName)
{
		CloudBlobContainer container = blobClient.GetContainerReference(containerName);

		if (await container.CreateIfNotExistsAsync())
		{
				Console.WriteLine("Container [{0}] created.", containerName);
		}
		else
		{
				Console.WriteLine("Container [{0}] exists, skipping creation.", containerName);
		}
}
```

Una vez creados los contenedores, la aplicación ya puede cargar los archivos que utilizarán las tareas.

> [AZURE.TIP] [How to use Blob Storage from .NET](./.. / storage/storage-dotnet-how-to-use-blobs.md) ofrece una buena introducción de cómo trabajar con los blobs y contenedores de Almacenamiento de Azure. Al empezar a trabajar con Lote, debe encontrarse cerca de la parte superior de la lista de lectura.

## Paso 2: Cargar la aplicación de tarea y los archivos de datos

![Cargar una aplicación de tarea y archivos de entrada (datos) en los contenedores][2] <br/>

En la operación de carga de archivos, *DotNetTutorial* define primero las colecciones de las rutas de acceso a los archivos **application** e **input** tal como existen en la máquina local. Después cargará estos archivos en los contenedores que creó en el paso anterior.

```
// Paths to the executable and its dependencies that will be executed by the tasks
List<string> applicationFilePaths = new List<string>
{
    // The DotNetTutorial project includes a project reference to TaskApplication, allowing us to
    // determine the path of the task application binary dynamically
    typeof(TaskApplication.Program).Assembly.Location,
    "Microsoft.WindowsAzure.Storage.dll"
};

// The collection of data files that are to be processed by the tasks
List<string> inputFilePaths = new List<string>
{
    @"..\..\taskdata1.txt",
    @"..\..\taskdata2.txt",
    @"..\..\taskdata3.txt"
};

// Upload the application and its dependencies to Azure Storage. This is the application that will
// process the data files, and will be executed by each of the tasks on the compute nodes.
List<ResourceFile> applicationFiles = await UploadFilesToContainerAsync(blobClient, appContainerName, applicationFilePaths);

// Upload the data files. This is the data that will be processed by each of the tasks that are
// executed on the compute nodes within the pool.
List<ResourceFile> inputFiles = await UploadFilesToContainerAsync(blobClient, inputContainerName, inputFilePaths);
```

Hay dos métodos en `Program.cs` que intervienen en el proceso de carga:

- `UploadFilesToContainerAsync`: este método devuelve una colección de objetos [ResourceFile][net_resourcefile] (que se explican a continuación) y llama internamente a `UploadFileToContainerAsync` para cargar todos los archivos pasados en el parámetro *filePaths*.
- `UploadFileToContainerAsync`: es el método que realmente realiza la carga del archivo y crea los objetos [ResourceFile][net_resourcefile]. Después de cargar el archivo, obtiene una firma de acceso compartido (SAS) para el archivo y devuelve un objeto ResourceFile que lo representa. A continuación también se tratan las firmas de acceso compartido.

```
private static async Task<ResourceFile> UploadFileToContainerAsync(CloudBlobClient blobClient, string containerName, string filePath)
{
		Console.WriteLine("Uploading file {0} to container [{1}]...", filePath, containerName);

		string blobName = Path.GetFileName(filePath);

		CloudBlobContainer container = blobClient.GetContainerReference(containerName);
		CloudBlockBlob blobData = container.GetBlockBlobReference(blobName);
		await blobData.UploadFromFileAsync(filePath, FileMode.Open);

		// Set the expiry time and permissions for the blob shared access signature. In this case, no start time is specified,
		// so the shared access signature becomes valid immediately
		SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
		{
				SharedAccessExpiryTime = DateTime.UtcNow.AddHours(2),
				Permissions = SharedAccessBlobPermissions.Read
		};

		// Construct the SAS URL for blob
		string sasBlobToken = blobData.GetSharedAccessSignature(sasConstraints);
		string blobSasUri = String.Format("{0}{1}", blobData.Uri, sasBlobToken);

		return new ResourceFile(blobSasUri, blobName);
}
```

### ResourceFiles

Un objeto [ResourceFile][net_resourcefile] proporciona tareas de Lote con la dirección URL a un archivo de Almacenamiento de Azure que se descargarán en un nodo de ejecución antes de ejecutar esa tarea. La propiedad [ResourceFile.BlobSource][net_resourcefile_blobsource] especifica la dirección URL completa del archivo, tal como existe en Almacenamiento de Azure. La dirección URL también puede incluir una firma de acceso compartido (SAS) que proporcione acceso seguro al archivo. La mayoría de tipos de tareas dentro de .NET de Lote incluyen una propiedad *ResourceFiles*, que comprende las clases:

- [CloudTask][net_task]
- [StartTask][net_pool_starttask]
- [JobPreparationTask][net_jobpreptask]
- [JobReleaseTask][net_jobreltask]

La aplicación de ejemplo DotNetTutorial no usa los tipos de tarea JobPreparationTask o JobReleaseTask, pero puede leer más acerca de ellas en [Ejecución de tareas de preparación y finalización de trabajos en nodos de ejecución de Lote de Azure](batch-job-prep-release.md).

### Firma de acceso compartido (SAS)

Las firmas de acceso compartido son cadenas que, cuando se incluyen como parte de una dirección URL, proporcionan acceso seguro a los contenedores y blobs de Almacenamiento de Azure. La aplicación DotNetTutorial utiliza direcciones URL de firma de acceso compartido de blobs y contenedores, y muestra cómo obtener estas cadenas de firma de acceso compartido en el servicio Almacenamiento.

- **Firmas de acceso compartido de blobs**: la clase StartTask del grupo en DotNetTutorial usa firmas de acceso compartido de blobs al descargar los archivos binarios de la aplicación y los archivos de datos de entrada de Almacenamiento (consulte el paso 3 a continuación). El método `UploadFileToContainerAsync` de `Program.cs` de DotNetTutorial contiene el código que obtiene la firma de acceso compartido de cada blob. Para ello, llama a [CloudblobData.GetSharedAccessSignature][net_sas_blob].

- **Firmas de acceso compartido de contenedores**: cuando cada tarea finaliza su trabajo en el nodo de proceso, carga su archivo de salida en el contenedor *output* de Almacenamiento de Azure. Para ello, TaskApplication usa una firma de acceso compartido de contenedor que proporciona acceso de escritura al contenedor como parte de la ruta de acceso al cargar el archivo. La obtención de la firma de acceso compartido del contenedor se realiza de manera similar que cuando se obtiene la firma de acceso compartido de un blob. En DotNetTutorial, encontrará que el método auxiliar `GetContainerSasUrl` llama a [CloudBlobContainer.GetSharedAccessSignature][net_sas_container] para hacerlo. En "Paso 6: Supervisar tareas", obtendrá más información acerca de la forma en que TaskApplication usa la firma de acceso compartido de un contenedor.

> [AZURE.TIP] Para más información acerca de cómo proporcionar acceso seguro a los datos de una cuenta de Almacenamiento, consulte la serie de dos partes acerca de las firmas de acceso compartido, [Firmas de acceso compartido, Parte 1: Descripción del modelo SAS](./../storage/storage-dotnet-shared-access-signature-part-1.md) y [Firmas de acceso compartido, Parte 2: Creación y uso de una SAS con Almacenamiento de blobs](./../storage/storage-dotnet-shared-access-signature-part-2.md).

## Paso 3: Crear un grupo de Lote

![Crear un grupo de Lote][3] <br/>

Después de cargar los archivos de datos y de aplicación en la cuenta de Almacenamiento, *DotNetTutorial* inicia su interacción con el servicio Lote mediante la biblioteca .NET de Lote. Para ello, primero se crea una clase [BatchClient][net_batchclient]:

```
BatchSharedKeyCredentials cred = new BatchSharedKeyCredentials(BatchAccountUrl, BatchAccountName, BatchAccountKey);
using (BatchClient batchClient = BatchClient.Open(cred))
{
	...
```

Después, se crea un grupo de nodos de proceso en la cuenta de Lote con una llamada a `CreatePoolAsync`. `CreatePoolAsync` usa el método [BatchClient.PoolOperations.CreatePool][net_pool_create] para crear un grupo en el servicio Lote.

```
private static async Task CreatePoolAsync(BatchClient batchClient, string poolId, IList<ResourceFile> resourceFiles)
{
    Console.WriteLine("Creating pool [{0}]...", poolId);

    // Create the unbound pool. Until we call CloudPool.Commit() or CommitAsync(), no pool is actually created in the
    // Batch service. This CloudPool instance is therefore considered "unbound," and we can modify its properties.
    CloudPool pool = batchClient.PoolOperations.CreatePool(
				poolId: poolId,
				targetDedicated: 3,           // 3 compute nodes
				virtualMachineSize: "small",  // single-core, 1.75 GB memory, 225 GB disk
				osFamily: "4");               // Windows Server 2012 R2

    // Create and assign the StartTask that will be executed when compute nodes join the pool.
    // In this case, we copy the StartTask's resource files (that will be automatically downloaded
    // to the node by the StartTask) into the shared directory that all tasks will have access to.
    pool.StartTask = new StartTask
    {
        // Specify a command line for the StartTask that copies the task application files to the
        // node's shared directory. Every compute node in a Batch pool is configured with several
        // pre-defined environment variables that you can reference by using commands or applications
        // run by tasks.

        // Since a successful execution of robocopy can return a non-zero exit code (e.g. 1 when one or
        // more files were successfully copied) we need to manually exit with a 0 for Batch to recognize
        // StartTask execution success.
        CommandLine = "cmd /c (robocopy %AZ_BATCH_TASK_WORKING_DIR% %AZ_BATCH_NODE_SHARED_DIR%) ^& IF %ERRORLEVEL% LEQ 1 exit 0",
        ResourceFiles = resourceFiles,
        WaitForSuccess = true
    };

    await pool.CommitAsync();
}
```

Al crear un grupo con [CreatePool][net_pool_create], especifique varios parámetros, como el número de nodos de proceso, el [tamaño de los nodos](./../cloud-services/cloud-services-sizes-specs.md) y el [sistema operativo](./../cloud-services/cloud-services-guestos-update-matrix.md) de los nodos.

> [AZURE.IMPORTANT] Se cobrará por recursos de proceso en Lote. Para minimizar los costos, puede reducir `targetDedicated` a 1 antes de ejecutar el ejemplo.

Junto con estas propiedades de nodo físicas, también puede especificar una clase [StartTask][net_pool_starttask] para el grupo. La clase StartTask se ejecutará en cada nodo cuando el nodo se una al grupo, así como cada vez que se reinicie el nodo. Esta clase es especialmente útil para instalar aplicaciones en nodos de ejecución antes de la ejecución de tareas. Por ejemplo, si las tareas procesan los datos mediante scripts de Python, puede usar una clase StartTask para instalar Python en los nodos de proceso.

En esta aplicación de ejemplo, la clase StartTask copia los archivos que descarga de Almacenamiento (que se especifican mediante la propiedad *ResourceFiles* de StartTask) desde el directorio de trabajo de clase StartTask al directorio compartido al que pueden acceder *todas* las tareas que se ejecutan en el nodo.

En el fragmento de código anterior destaca también el uso de dos variables de entorno en la propiedad *CommandLine* de la clase StartTask: `%AZ_BATCH_TASK_WORKING_DIR%` y `%AZ_BATCH_NODE_SHARED_DIR%`. Cada nodo de proceso de un grupo de Lote se configura automáticamente con un número de variables de entorno específicas de Lote. Todos los procesos que ejecute una tarea tienen acceso a estas variables de entorno.

> [AZURE.TIP] Para más información acerca de las variables de entorno disponibles en los nodos de proceso en un grupo de Lote, así como información acerca de los directorios de trabajo de las tareas, consulte las secciones "Configuración del entorno para las tareas" y "Archivos y directorios" de [Información general de las características de Lote de Azure](batch-api-basics.md).

## Paso 4: Crear el trabajo de Lote

![Crear trabajo de Lote][4]<br/>

Un trabajo de Lote es básicamente una colección de tareas asociadas a un grupo de nodos de proceso. Se puede usar no solo para la organización y seguimiento de las tareas en las cargas de trabajo relacionadas, sino también para imponer ciertas restricciones, como el tiempo máximo de ejecución del trabajo (y, por extensión, sus tareas), así como la prioridad del trabajo, en relación con los restantes trabajos de la cuenta de Lote. Sin embargo, en este ejemplo, el trabajo está asociado solo con el grupo que se creó en el paso 3. No hay propiedades adicionales configuradas.

Todos los trabajos de Lote están asociados a un grupo específico. Esta asociación indica en qué nodos se ejecutarán las tareas del trabajo. Para especificarlo, use la propiedad [CloudJob.PoolInformation][net_job_poolinfo], como se muestra en el siguiente fragmento de código.

```
private static async Task CreateJobAsync(BatchClient batchClient, string jobId, string poolId)
{
    Console.WriteLine("Creating job [{0}]...", jobId);

    CloudJob job = batchClient.JobOperations.CreateJob();
    job.Id = jobId;
    job.PoolInformation = new PoolInformation { PoolId = poolId };

    await job.CommitAsync();
}
```

Ahora que se ha creado un trabajo, se agregan las tareas para realizar dicho trabajo.

## Paso 5: Agregar tareas al trabajo

![Agregar tareas al trabajo][5]<br/> *(1) Las tareas se agregan al trabajo, (2) las tareas se programan para ejecutarse en los nodos y (3) las tareas descargan los archivos de datos que se van a procesar*

Para realizar el trabajo, las tareas deben agregarse a un trabajo. Las clases [CloudTask][net_task] se configuran con una propiedad de línea de comandos y la colección [ResourceFiles][net_task_resourcefiles] (al igual que la clase StartTask del grupo) que la tarea descarga en el nodo antes de que su línea de comandos se ejecute automáticamente. En el proyecto de ejemplo *DotNetTutorial*, cada tarea procesa un solo archivo. Por lo tanto, su colección ResourceFiles contiene un único elemento.

```
private static async Task<List<CloudTask>> AddTasksAsync(BatchClient batchClient, string jobId, List<ResourceFile> inputFiles, string outputContainerSasUrl)
{
    Console.WriteLine("Adding {0} tasks to job [{1}]...", inputFiles.Count, jobId);

    // Create a collection to hold the tasks that we'll be adding to the job
    List<CloudTask> tasks = new List<CloudTask>();

    // Create each of the tasks. Because we copied the task application to the
    // node's shared directory with the pool's StartTask, we can access it via
    // the shared directory on the node that the task runs on.
    foreach (ResourceFile inputFile in inputFiles)
    {
        string taskId = "topNtask" + inputFiles.IndexOf(inputFile);
        string taskCommandLine = String.Format("cmd /c %AZ_BATCH_NODE_SHARED_DIR%\\TaskApplication.exe {0} 3 \"{1}\"", inputFile.FilePath, outputContainerSasUrl);

        CloudTask task = new CloudTask(taskId, taskCommandLine);
        task.ResourceFiles = new List<ResourceFile> { inputFile };
        tasks.Add(task);
    }

    // Add the tasks as a collection, as opposed to adding a separate AddTask call for each. Bulk task submission
    // helps to ensure efficient underlying API calls to the Batch service.
    await batchClient.JobOperations.AddTaskAsync(jobId, tasks);

    return tasks;
}
```

> [AZURE.IMPORTANT] Cuando acceden a variables de entorno como `%AZ_BATCH_NODE_SHARED_DIR%` o ejecutan una aplicación que no se encuentra en `PATH` del nodo, las líneas de comandos de la tarea deben ir precedidas de `cmd /c`. Así se ejecutará explícitamente el intérprete de comandos y se dará la orden de finalización después de ejecutar el comando. Este requisito no es necesario si las tareas ejecutan una aplicación en `PATH` del nodo (como *robocopy.exe* o *powershell.exe*) y no se utilizan variables de entorno.

En el bucle `foreach` del fragmento de código anterior, puede ver que la línea de comandos de la tarea se construye de forma que se pasan tres argumentos de línea de comandos a *TaskApplication.exe*:

1. El **primer argumento** es la ruta de acceso del archivo que se va a procesar. Se trata de la ruta de acceso local al archivo, tal como existe en el nodo. Cuando se creó el objeto ResourceFile de `UploadFileToContainerAsync`, se usó el nombre de archivo para esta propiedad (como un parámetro del constructor ResourceFile). Esto indica que el archivo puede encontrarse en el mismo directorio que *TaskApplication.exe*.

2. El **segundo argumento** especifica que las *N* palabras más usadas deben escribirse en el archivo de salida. En el ejemplo, esto se codifica de forma rígida, con el fin de que las tres palabras más usadas se escriban en el archivo de salida.

3. El **tercer argumento** es la firma de acceso compartido (SAS) que proporciona acceso de escritura al contenedor **output** de Almacenamiento de Azure. *TaskApplication.exe* utiliza esta URL de firma de acceso compartido al cargar el archivo de salida en Almacenamiento de Azure. El código se puede encontrar en el método `UploadFileToContainer` del archivo `Program.cs` del proyecto TaskApplication:

```
// NOTE: From project TaskApplication Program.cs

private static void UploadFileToContainer(string filePath, string containerSas)
{
		string blobName = Path.GetFileName(filePath);

		// Obtain a reference to the container using the SAS URI.
		CloudBlobContainer container = new CloudBlobContainer(new Uri(containerSas));

		// Upload the file (as a new blob) to the container
		try
		{
				CloudBlockBlob blob = container.GetBlockBlobReference(blobName);
				blob.UploadFromFile(filePath, FileMode.Open);

				Console.WriteLine("Write operation succeeded for SAS URL " + containerSas);
				Console.WriteLine();
		}
		catch (StorageException e)
		{

				Console.WriteLine("Write operation failed for SAS URL " + containerSas);
				Console.WriteLine("Additional error information: " + e.Message);
				Console.WriteLine();

				// Indicate that a failure has occurred so that when the Batch service sets the
				// CloudTask.ExecutionInformation.ExitCode for the task that executed this application,
				// it properly indicates that there was a problem with the task.
				Environment.ExitCode = -1;
		}
}
```

## Paso 6: Supervisar tareas

![Supervisar tareas][6]<br/> *La aplicación cliente (1) supervisa las tareas que comprueban la finalización y que el estado es correcto y (2) las tareas cargan los datos resultantes en Almacenamiento de Azure*

Cuando las tareas se agregan a un trabajo, automáticamente se ponen en cola y se programan para su ejecución en los nodos de ejecución del grupo asociado al trabajo. Según la configuración que especifique, Lote controla la administración de las colas, programación y reintentos de todas las tareas, así como otros cometidos de administración de tareas por usted. Existen varios enfoques para supervisar la ejecución de tareas. DotNetTutorial muestra un ejemplo sencillo que informa únicamente sobre la finalización y los estados de error y de realización correcta de la tarea.

En el método `MonitorTasks` de `Program.cs` de DotNetTutorial, hay tres conceptos de .NET de Lote que justifican una explicación. Dichos conceptos se enumeran a continuación en su orden de aparición:

1. **ODATADetailLevel**: la especificación de [ODATADetailLevel][net_odatadetaillevel] en las operaciones de lista (como obtener una lista de las tareas de un trabajo) es esencial para garantizar el rendimiento de la aplicación de Lote. Agregue [Consulta eficaz del servicio Lote de Azure](batch-efficient-list-queries.md) a su lista de lecturas si va a realizar cualquier tipo de supervisión de estado en las aplicaciones de Lote.

2. **TaskStateMonitor**: [TaskStateMonitor][net_taskstatemonitor] proporciona aplicaciones de .NET de Lote con utilidades auxiliares para la supervisión de los estados de la tarea. En `MonitorTasks`, *DotNetTutorial* espera hasta que todas las tareas alcanzan el estado [TaskState.Completed][net_taskstate] dentro de un límite de tiempo. Luego finaliza el trabajo.

3. **TerminateJobAsync**: la finalización de un trabajo con [JobOperations.TerminateJobAsync][net_joboperations_terminatejob] (o con JobOperations.TerminateJob de bloqueo) marcará el trabajo como completado. Esta operación es esencial para hacerlo la solución de Lote usa [JobReleaseTask][net_jobreltask]. Se trata de un tipo especial de tarea, que se describe en [Tareas de preparación y la finalización de trabajos](batch-job-prep-release).

A continuación se muestra el método `MonitorTasks` de `Program.cs` de *DotNetTutorial*:

```
private static async Task<bool> MonitorTasks(BatchClient batchClient, string jobId, TimeSpan timeout)
{
    bool allTasksSuccessful = true;
    const string successMessage = "All tasks reached state Completed.";
    const string failureMessage = "One or more tasks failed to reach the Completed state within the timeout period.";

    // Obtain the collection of tasks currently managed by the job. Note that we use a detail level to
    // specify that only the "id" property of each task should be populated. Using a detail level for
    // all list operations helps to lower response time from the Batch service.
    ODATADetailLevel detail = new ODATADetailLevel(selectClause: "id");
    List<CloudTask> tasks = await batchClient.JobOperations.ListTasks(JobId, detail).ToListAsync();

    Console.WriteLine("Awaiting task completion, timeout in {0}...", timeout.ToString());

    // We use a TaskStateMonitor to monitor the state of our tasks. In this case, we will wait for all tasks to
    // reach the Completed state.
    TaskStateMonitor taskStateMonitor = batchClient.Utilities.CreateTaskStateMonitor();
    bool timedOut = await taskStateMonitor.WaitAllAsync(tasks, TaskState.Completed, timeout);

    if (timedOut)
    {
        allTasksSuccessful = false;

        await batchClient.JobOperations.TerminateJobAsync(jobId, failureMessage);

        Console.WriteLine(failureMessage);
    }
    else
    {
        await batchClient.JobOperations.TerminateJobAsync(jobId, successMessage);

        // All tasks have reached the "Completed" state. However, this does not guarantee that all tasks were completed successfully.
        // Here we further check each task's ExecutionInfo property to ensure that it did not encounter a scheduling error
        // or return a non-zero exit code.

        // Update the detail level to populate only the task id and executionInfo properties.
        // We refresh the tasks below, and need only this information for each task.
        detail.SelectClause = "id, executionInfo";

        foreach (CloudTask task in tasks)
        {
            // Populate the task's properties with the latest info from the Batch service
            await task.RefreshAsync(detail);

            if (task.ExecutionInformation.SchedulingError != null)
            {
                // A scheduling error indicates a problem starting the task on the node. It is important to note that
                // the task's state can be "Completed," yet the task still might have encountered a scheduling error.

                allTasksSuccessful = false;

                Console.WriteLine("WARNING: Task [{0}] encountered a scheduling error: {1}", task.Id, task.ExecutionInformation.SchedulingError.Message);
            }
            else if (task.ExecutionInformation.ExitCode != 0)
            {
                // A non-zero exit code may indicate that the application executed by the task encountered an error
                // during execution. As not every application returns non-zero on failure by default (e.g. robocopy),
                // your implementation of error checking may differ from this example.

                allTasksSuccessful = false;

                Console.WriteLine("WARNING: Task [{0}] returned a non-zero exit code - this may indicate task execution or completion failure.", task.Id);
            }
        }
    }

    if (allTasksSuccessful)
    {
        Console.WriteLine("Success! All tasks completed successfully within the specified timeout period.");
    }

    return allTasksSuccessful;
}
```

## Paso 7: Descargar el resultado de la tarea

![Descargar el resultado de la tarea desde Almacenamiento][7]<br/>

Ahora que se ha completado el trabajo, el resultado de las tareas se puede descargar desde Almacenamiento de Azure. Para ello, es preciso realizar una llamada a `DownloadBlobsFromContainerAsync` en `Program.cs` de *DotNetTutorial*:

```
private static async Task DownloadBlobsFromContainerAsync(CloudBlobClient blobClient, string containerName, string directoryPath)
{
		Console.WriteLine("Downloading all files from container [{0}]...", containerName);

		// Retrieve a reference to a previously created container
		CloudBlobContainer container = blobClient.GetContainerReference(containerName);

		// Get a flat listing of all the block blobs in the specified container
		foreach (IListBlobItem item in container.ListBlobs(prefix: null, useFlatBlobListing: true))
		{
				// Retrieve reference to the current blob
				CloudBlob blob = (CloudBlob)item;

				// Save blob contents to a file in the specified folder
				string localOutputFile = Path.Combine(directoryPath, blob.Name);
				await blob.DownloadToFileAsync(localOutputFile, FileMode.Create);
		}

		Console.WriteLine("All files downloaded to {0}", directoryPath);
}
```

> [AZURE.NOTE] La llamada a `DownloadBlobsFromContainerAsync` en la aplicación *DotNetTutorial* especifica que los archivos se deben descargar en su carpeta `%TEMP%`. Puede modificar si lo desea esta ubicación de salida.

## Paso 8: Eliminar contenedores

Como se le cobrará por los datos que residen en Almacenamiento de Azure, siempre es una buena idea quitar los blobs que ya no sean necesario para los trabajos de Lote. En `Program.cs` de DotNetTutorial, esto se realiza con tres llamadas al método auxiliar `DeleteContainerAsync`:

```
// Clean up Storage resources
await DeleteContainerAsync(blobClient, appContainerName);
await DeleteContainerAsync(blobClient, inputContainerName);
await DeleteContainerAsync(blobClient, outputContainerName);
```

El propio método simplemente obtiene una referencia al contenedor y después llama a [CloudBlobContainer.DeleteIfExistsAsync][net_container_delete]\:

```
private static async Task DeleteContainerAsync(CloudBlobClient blobClient, string containerName)
{
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);

    if (await container.DeleteIfExistsAsync())
    {
        Console.WriteLine("Container [{0}] deleted.", containerName);
    }
    else
    {
        Console.WriteLine("Container [{0}] does not exist, skipping deletion.", containerName);
    }
}
```

## Paso 9: Eliminar el trabajo y el grupo

En el último paso, se pedirá al usuario que elimine el trabajo y el grupo que creó la aplicación DotNetTutorial. Aunque no se cobra por los trabajos y tareas, *sí* que se cobra por los nodos de proceso. Por consiguiente, se recomienda asignar solo los nodos necesarios. La eliminación de los grupos que no se usen puede formar parte del proceso de mantenimiento.

Las propiedades [JobOperations][net_joboperations] y [PoolOperations][net_pooloperations] de BatchClient tienen sus métodos de eliminación correspondientes, a los que se llama si el usuario confirma la eliminación:

```
// Clean up the resources we've created in the Batch account if the user so chooses
Console.WriteLine();
Console.WriteLine("Delete job? [yes] no");
string response = Console.ReadLine().ToLower();
if (response != "n" && response != "no")
{
    await batchClient.JobOperations.DeleteJobAsync(JobId);
}

Console.WriteLine("Delete pool? [yes] no");
response = Console.ReadLine();
if (response != "n" && response != "no")
{
    await batchClient.PoolOperations.DeletePoolAsync(PoolId);
}
```

> [AZURE.IMPORTANT] Tenga en cuenta que los recursos de proceso se cobran (la eliminación de grupos sin usar reducirá el costo). Tenga en cuenta que al eliminar un grupo se eliminan todos los nodos de proceso del grupo y que los datos de los nodos no se podrán recuperar cuando se elimine el grupo.

## Ejecución del ejemplo *DotNetTutorial*

Al ejecutar la aplicación de ejemplo, el resultado de la consola será similar al siguiente. Durante la ejecución, experimentará una pausa en `Awaiting task completion, timeout in 00:30:00...` mientras se inician los nodos de proceso del grupo. Use el [Explorador de Lote][github_batchexplorer] para supervisar el grupo, los nodos de proceso, el trabajo y las tareas durante la ejecución, y después de ella. Use el [Portal de Azure][azure_portal] o uno de los [exploradores de Almacenamiento de Azure disponibles][storage_explorers] para ver los recursos de Almacenamiento (contenedores y blobs) que crea la aplicación.

El tiempo de ejecución típico es **aproximadamente de 5 minutos** al ejecutar la aplicación en su configuración predeterminada.

```
Sample start: 1/8/2016 09:42:58 AM

Container [application] created.
Container [input] created.
Container [output] created.
Uploading file C:\repos\azure-batch-samples\CSharp\ArticleProjects\DotNetTutorial\bin\Debug\TaskApplication.exe to container [application]...
Uploading file Microsoft.WindowsAzure.Storage.dll to container [application]...
Uploading file ..\..\taskdata1.txt to container [input]...
Uploading file ..\..\taskdata2.txt to container [input]...
Uploading file ..\..\taskdata3.txt to container [input]...
Creating pool [DotNetTutorialPool]...
Creating job [DotNetTutorialJob]...
Adding 3 tasks to job [DotNetTutorialJob]...
Awaiting task completion, timeout in 00:30:00...
Success! All tasks completed successfully within the specified timeout period.
Downloading all files from container [output]...
All files downloaded to C:\Users\USERNAME\AppData\Local\Temp
Container [application] deleted.
Container [input] deleted.
Container [output] deleted.

Sample end: 1/8/2016 09:47:47 AM
Elapsed time: 00:04:48.5358142

Delete job? [yes] no: yes
Delete pool? [yes] no: yes

Sample complete, hit ENTER to exit...
```

## Pasos siguientes

Puede realizar todos los cambios que desee en *DotNetTutorial* y *TaskApplication* para experimentar con distintos escenarios de proceso. Por ejemplo, pruebe a agregar un retraso en la ejecución a *TaskApplication*, como con [Thread.Sleep][net_thread_sleep], para simular tareas de ejecución prolongada y supervisarlas con la característica *Mapa térmico* del Explorador de Lote. Pruebe a agregar más tareas o a ajustar el número de nodos de proceso. Agregue lógica para buscar un grupo existente y permitir su uso para reducir el tiempo de ejecución (*sugerencia*: modifique `ArticleHelpers.cs` en el proyecto [Microsoft.Azure.Batch.Samples.Common][github_samples_common] de [azure-batch-samples][github_samples]).

Ahora que está familiarizado con el flujo de trabajo básico de una solución de Lote, ha llegado el momento de adentrarse en las características adicionales del servicio Lote.

- Consulte el artículo [Información general de las características de Lote de Azure](batch-api-basics.md), que se recomienda si no de conoce el servicio.
- Comience por los restantes artículos de desarrollo de Lote de la sección **Desarrollo en profundidad** de [Lote][batch_learning_path].
- Consulte una implementación diferente del procesamiento de la carga de trabajo de "las N palabras más usadas" con Lote en el ejemplo [TopNWords][github_topnwords].

[azure_batch]: https://azure.microsoft.com/services/batch/
[azure_portal]: https://portal.azure.com
[batch_explorer_blog]: http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx
[batch_learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[github_batchexplorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[github_dotnettutorial]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/DotNetTutorial
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_common]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/Common
[github_samples_zip]: https://github.com/Azure/azure-batch-samples/archive/master.zip
[github_topnwords]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/TopNWords
[net_api]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[net_api_storage]: https://msdn.microsoft.com/library/azure/mt347887.aspx
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_job]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_job_poolinfo]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.protocol.models.cloudjob.poolinformation.aspx
[net_joboperations]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.joboperations
[net_joboperations_terminatejob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.terminatejobasync.aspx
[net_jobpreptask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobpreparationtask.aspx
[net_jobreltask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobreleasetask.aspx
[net_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.aspx
[net_odatadetaillevel]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.aspx
[net_pool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_pool_create]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[net_pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx
[net_pooloperations]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.pooloperations
[net_resourcefile]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.resourcefile.aspx
[net_resourcefile_blobsource]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.resourcefile.blobsource.aspx
[net_sas_blob]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblob.getsharedaccesssignature.aspx
[net_sas_container]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblobcontainer.getsharedaccesssignature.aspx
[net_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_task_resourcefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.resourcefiles.aspx
[net_taskstate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.taskstate.aspx
[net_taskstatemonitor]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskstatemonitor.aspx
[net_thread_sleep]: https://msdn.microsoft.com/library/274eh01d(v=vs.110).aspx
[net_cloudblobclient]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobclient.aspx
[net_cloudblobcontainer]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.aspx
[net_cloudstorageaccount]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.cloudstorageaccount.aspx
[net_container_delete]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.deleteifexistsasync.aspx
[nuget_packagemgr]: https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c
[nuget_restore]: https://docs.nuget.org/consume/package-restore/msbuild-integrated#enabling-package-restore-during-build
[storage_explorers]: http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx
[visual_studio]: https://www.visualstudio.com/products/vs-2015-product-editions

[1]: ./media/batch-dotnet-get-started/batch_workflow_01_sm.png "Crear contenedores en Almacenamiento de Azure"
[2]: ./media/batch-dotnet-get-started/batch_workflow_02_sm.png "Cargar una aplicación de tarea y archivos de entrada (datos) en los contenedores"
[3]: ./media/batch-dotnet-get-started/batch_workflow_03_sm.png "Crear grupo de Lote"
[4]: ./media/batch-dotnet-get-started/batch_workflow_04_sm.png "Crear trabajo de Lote"
[5]: ./media/batch-dotnet-get-started/batch_workflow_05_sm.png "Agregar tareas al trabajo"
[6]: ./media/batch-dotnet-get-started/batch_workflow_06_sm.png "Supervisar tareas"
[7]: ./media/batch-dotnet-get-started/batch_workflow_07_sm.png "Descargar el resultado de la tarea desde Almacenamiento"
[8]: ./media/batch-dotnet-get-started/batch_workflow_sm.png "Flujo de trabajo de solución de Lote (diagrama completo)"
[9]: ./media/batch-dotnet-get-started/credentials_batch_sm.png "Credenciales de Lote en el Portal"
[10]: ./media/batch-dotnet-get-started/credentials_storage_sm.png "Credenciales de Almacenamiento en el Portal"
[11]: ./media/batch-dotnet-get-started/batch_workflow_minimal_sm.png "Flujo de trabajo de solución de Lote (diagrama mínimo)"

<!---HONumber=AcomDC_0204_2016-->