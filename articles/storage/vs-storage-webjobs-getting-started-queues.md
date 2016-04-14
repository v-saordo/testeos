<properties
	pageTitle="Introducción al almacenamiento de colas y servicios conectados de Visual Studio (proyectos de WebJobs) | Microsoft Azure"
	description="Cómo empezar a usar el almacenamiento de colas de Azure en un proyecto de WebJob después de conectarse a una cuenta de almacenamiento con los servicios conectados de Visual Studio."
	services="storage"
	documentationCenter=""
	authors="TomArcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="storage"
	ms.workload="web"
	ms.tgt_pltfrm="vs-getting-started"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/21/2016"
	ms.author="tarcher"/>

# Introducción al Almacenamiento de colas de Azure y servicios conectados de Visual Studio (proyectos de WebJobs)

## Información general

En este artículo se describe cómo empezar a usar el Almacenamiento en cola de Azure en un proyecto de WebJobs de Azure de Visual Studio después de crear una cuenta de almacenamiento de Azure o hacer referencia a ella con el cuadro de diálogo <1>Agregar servicios conectados</1> de Visual Studio. Al agregar una cuenta de almacenamiento a un proyecto de WebJobs con el cuadro de diálogo **Agregar servicios conectados** de Visual Studio, se instalan los paquetes de NuGet de Almacenamiento de Azure adecuados, se agregan las referencias de .NET adecuadas al proyecto y se actualizan las cadenas de conexión para la cuenta de almacenamiento en el archivo App.config.

En este artículo se ofrecen ejemplos de código C# que muestran cómo usar la versión 1.x del SDK de WebJobs de Azure con el servicio Almacenamiento de colas de Azure.

El almacenamiento en cola de Azure es un servicio para almacenar grandes cantidades de mensajes a los que puede obtenerse acceso desde cualquier lugar del mundo a través de llamadas autenticadas con HTTP o HTTPS. Un único mensaje en cola puede tener un tamaño de hasta 64 KB y una cola puede contener millones de mensajes, hasta el límite de capacidad total de una cuenta de almacenamiento. Vea [Introducción al Almacenamiento en cola de Azure mediante .NET](storage-dotnet-how-to-use-queues.md) para obtener más información. Para obtener más información acerca de ASP.NET, consulte [ASP.NET](http://www.asp.net).



## Desencadenar una función cuando se recibe un mensaje de cola

Para escribir una función que el SDK de WebJobs llama cuando se recibe un mensaje en cola, use el atributo **QueueTrigge**. El constructor de atributo toma un parámetro de cadena que especifica el nombre de la cola para sondear. Para ver cómo establecer dinámicamente el nombre de la cola, consulte [Establecimiento de opciones de configuración](#how-to-set-configuration-options).

### Mensajes en cola de cadena

En el ejemplo siguiente, la cola contiene un mensaje de cadena, por lo que **QueueTrigger** se aplica a un parámetro de cadena llamado **logMessage** que tiene el contenido del mensaje de la cola. La función [ escribe un mensaje de registro en el panel](#how-to-write-logs).


		public static void ProcessQueueMessage([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
		{
		    logger.WriteLine(logMessage);
		}

Además de **string**, el parámetro puede ser una matriz de bytes, un objeto **CloudQueueMessage** o un objeto POCO que defina.

### Mensajes en cola POCO [ (objeto CRL estándar](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object))

En el ejemplo siguiente, el mensaje de cola contiene JSON para un objeto **BlobInformation** que incluye una propiedad **BlobName**. El SDK automáticamente deserializa el objeto.

		public static void WriteLogPOCO([QueueTrigger("logqueue")] BlobInformation blobInfo, TextWriter logger)
		{
		    logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
		}

El SDK usa el [paquete Newtonsoft.Json NuGet](http://www.nuget.org/packages/Newtonsoft.Json) para serializar y deserializar los mensajes. Si crea mensajes en cola en un programa que no usa el SDK de WebJobs, puede escribir código similar al siguiente ejemplo para crear un mensaje en cola POCO que pueda analizar el SDK.

		BlobInformation blobInfo = new BlobInformation() { BlobName = "log.txt" };
		var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
		logQueue.AddMessage(queueMessage);

### Funciones asincrónicas

La siguiente función async [escribe un registro en el panel](#how-to-write-logs).

		public async static Task ProcessQueueMessageAsync([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
		{
		    await logger.WriteLineAsync(logMessage);
		}

Las funciones asincrónicas pueden tomar un [token de cancelación](http://www.asp.net/mvc/overview/performance/using-asynchronous-methods-in-aspnet-mvc-4#CancelToken), como se muestra en el siguiente ejemplo, que copia un blob. (Para obtener una explicación del marcador de posición **queueTrigger**, consulte la sección [Blobs](#how-to-read-and-write-blobs-and-tables-while-processing-a-queue-message)).

		public async static Task ProcessQueueMessageAsyncCancellationToken(
		    [QueueTrigger("blobcopyqueue")] string blobName,
		    [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput,
		    CancellationToken token)
		{
		    await blobInput.CopyToAsync(blobOutput, 4096, token);
		}

## Tipos con los que funciona el atributo QueueTrigger

Puede usar **QueueTrigger** con los tipos siguientes:

* **cadena**
* Un tipo de POCO serializado como JSON
* **byte**
* **CloudQueueMessage**

## Algoritmo de sondeo

El SDK implementa un algoritmo de interrupción exponencial aleatorio para reducir el efecto del sondeo de cola inactiva en los costos de transacción de almacenamiento. Cuando se encuentra un mensaje, el SDK espera dos segundos y, a continuación, comprueba si hay otro mensaje; cuando no se encuentra ningún mensaje, espera unos cuatro segundos antes de intentarlo de nuevo. Después de varios intentos fallidos para obtener un mensaje de la cola, el tiempo de espera sigue aumentando hasta que alcanza el tiempo de espera máximo, predeterminado en un minuto. [El tiempo de espera máximo es configurable](#how-to-set-configuration-options).

## Varias instancias

Si la aplicación web se ejecuta en varias instancias, un WebJobs continuo se ejecuta en cada máquina y estas, a su vez, esperarán a los desencadenadores e intentarán ejecutar funciones. En algunos escenarios puede ocurrir que algunas funciones procesen los mismos datos dos veces, por lo que las funciones deben ser idempotentes (escritas de tal forma que al llamarlas repetidamente con los mismos datos de entrada no se generen resultados duplicados).

## Ejecución en paralelo

Si tiene varias funciones escuchando en diferentes colas, el SDK las llamará en paralelo cuando se reciban mensajes simultáneamente.

Esto también se da cuando se reciben varios mensajes de una sola cola. De forma predeterminada, el SDK obtiene un lote de 16 mensajes de la cola a la vez y ejecuta la función que se procesa en paralelo. [El tamaño del lote es configurable](#how-to-set-configuration-options). Cuando el número que se está procesando llegue a la mitad del tamaño del lote, el SDK obtiene otro lote y empieza a procesar los mensajes. Por lo tanto, el número máximo de mensajes simultáneos que se procesan por función es uno y medio por el tamaño del lote. Este límite se aplica por separado para cada función que tenga un atributo **QueueTrigger**. Si no desea la ejecución en paralelo de los mensajes en una cola, establezca el tamaño del lote en 1.

## Obtener metadatos de cola o de mensaje en cola

Puede obtener las siguientes propiedades de mensaje agregando parámetros a la firma del método:

* **DateTimeOffset** expirationTime
* **DateTimeOffset** insertionTime
* **DateTimeOffset** nextVisibleTime
* **string** queueTrigger (contiene texto de mensaje)
* **string** id
* **string** popReceipt
* **int** dequeueCount

Si desea trabajar directamente con la API de almacenamiento de Azure, también puede agregar un parámetro **CloudStorageAccount**.

En el siguiente ejemplo escribirá todos estos metadatos en un registro de aplicación de información. En el ejemplo, logMessage y queueTrigger contienen el contenido del mensaje en cola.

		public static void WriteLog([QueueTrigger("logqueue")] string logMessage,
		    DateTimeOffset expirationTime,
		    DateTimeOffset insertionTime,
		    DateTimeOffset nextVisibleTime,
		    string id,
		    string popReceipt,
		    int dequeueCount,
		    string queueTrigger,
		    CloudStorageAccount cloudStorageAccount,
		    TextWriter logger)
		{
		    logger.WriteLine(
		        "logMessage={0}\n" +
			"expirationTime={1}\ninsertionTime={2}\n" +
		        "nextVisibleTime={3}\n" +
		        "id={4}\npopReceipt={5}\ndequeueCount={6}\n" +
		        "queue endpoint={7} queueTrigger={8}",
		        logMessage, expirationTime,
		        insertionTime,
		        nextVisibleTime, id,
		        popReceipt, dequeueCount,
		        cloudStorageAccount.QueueEndpoint,
		        queueTrigger);
		}

Este es un registro de ejemplo escrito por el código de ejemplo:

		logMessage=Hello world!
		expirationTime=10/14/2014 10:31:04 PM +00:00
		insertionTime=10/7/2014 10:31:04 PM +00:00
		nextVisibleTime=10/7/2014 10:41:23 PM +00:00
		id=262e49cd-26d3-4303-ae88-33baf8796d91
		popReceipt=AgAAAAMAAAAAAAAAfc9H0n/izwE=
		dequeueCount=1
		queue endpoint=https://contosoads.queue.core.windows.net/
		queueTrigger=Hello world!

## Apagado correcto

Una función que se ejecuta en un WebJob continuo puede aceptar un parámetro **CancellationToken** que permite al sistema operativo notificar a la función cuando el WebJob está a punto de terminar. Puede utilizar esta notificación para asegurarse de que la función no se termina inesperadamente en una forma que deje los datos en un estado incoherente.

En el ejemplo siguiente se muestra cómo comprobar la finalización de WebJob inminente en una función.

	public static void GracefulShutdownDemo(
	            [QueueTrigger("inputqueue")] string inputText,
	            TextWriter logger,
	            CancellationToken token)
	{
	    for (int i = 0; i < 100; i++)
	    {
	        if (token.IsCancellationRequested)
	        {
	            logger.WriteLine("Function was cancelled at iteration {0}", i);
	            break;
	        }
	        Thread.Sleep(1000);
	        logger.WriteLine("Normal processing for queue message={0}", inputText);
	    }
	}

**Nota:** el Panel puede no mostrar correctamente el estado y la salida de funciones que se hayan cerrado.

Para obtener más información, consulte [Cierre estable de WebJobs](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.VCt1GXl0wpR).

## Creación de un mensaje de cola al procesar un mensaje de cola

Para escribir una función que crea un mensaje en cola nuevo, use el atributo **Queue**. Al igual que **QueueTrigger**, pasa el nombre de cola como una cadena o puede [establecer el nombre de la cola dinámicamente](#how-to-set-configuration-options).

### Mensajes en cola de cadena

El siguiente ejemplo de código no asincrónico crea un mensaje en cola nuevo en la cola llamada "outputqueue", con el mismo contenido que el mensaje en cola recibido en la cola llamada "inputqueue". (En el caso de las funciones asincrónicas, use **IAsyncCollector<T>** como se muestra más adelante en esta sección).


		public static void CreateQueueMessage(
		    [QueueTrigger("inputqueue")] string queueMessage,
		    [Queue("outputqueue")] out string outputQueueMessage )
		{
		    outputQueueMessage = queueMessage;
		}

### Mensajes en cola POCO [ (objeto CRL estándar](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object))

Para crear un mensaje en cola que contiene un objeto POCO en lugar de una cadena, pase el tipo POCO como un parámetro de salida al constructor de atributo **Queue**.

		public static void CreateQueueMessage(
		    [QueueTrigger("inputqueue")] BlobInformation blobInfoInput,
		    [Queue("outputqueue")] out BlobInformation blobInfoOutput )
		{
		    blobInfoOutput = blobInfoInput;
		}

El SDK serializa automáticamente el objeto a JSON. Siempre se crea un mensaje en cola, incluso si el objeto es null.

### Crear varios mensajes o en funciones asincrónicas

Para crear varios mensajes, convierta el tipo de parámetro para la cola de salida **ICollector<T>** o **IAsyncCollector<T>**, como se muestra en el siguiente ejemplo.

		public static void CreateQueueMessages(
		    [QueueTrigger("inputqueue")] string queueMessage,
		    [Queue("outputqueue")] ICollector<string> outputQueueMessage,
		    TextWriter logger)
		{
		    logger.WriteLine("Creating 2 messages in outputqueue");
		    outputQueueMessage.Add(queueMessage + "1");
		    outputQueueMessage.Add(queueMessage + "2");
		}

Cada mensaje de cola se crea inmediatamente cuando se llama al método **Add**.

### Tipos con los que funciona el atributo Queue

Puede usar el atributo **Queue** en los siguientes tipos de parámetro:

* **out string** (crea un mensaje en cola si el valor de parámetro es no nulo cuando termina la función)
* **out byte** (funciona como **cadena**)
* **out CloudQueueMessage** (funciona como **cadena**)
* **out POCO** (un tipo serializable, crea un mensaje con un objeto nulo si el parámetro es nulo cuando termina la función)
* **ICollector**
* **IAsyncCollector**
* **CloudQueue** (para crear manualmente mensajes usando directamente la API de Almacenamiento de Azure)

### Utilizar atributos del SDK de WebJobs en el cuerpo de una función

Si necesita realizar algún trabajo en la función antes de usar un atributo del SDK de WebJobs como **Queue**, **Blob** o **Table**, puede usar la interfaz **IBinder**.

En el siguiente ejemplo se toma un mensaje de la cola de entrada y se crea un mensaje nuevo con el mismo contenido en una cola de salida. El nombre de la cola de salida se establece por el código en el cuerpo de la función.

		public static void CreateQueueMessage(
		    [QueueTrigger("inputqueue")] string queueMessage,
		    IBinder binder)
		{
		    string outputQueueName = "outputqueue" + DateTime.Now.Month.ToString();
		    QueueAttribute queueAttribute = new QueueAttribute(outputQueueName);
		    CloudQueue outputQueue = binder.Bind<CloudQueue>(queueAttribute);
		    outputQueue.AddMessage(new CloudQueueMessage(queueMessage));
		}

La interfaz **IBinder** también puede usarse con los atributos **Table** y **Blob**.

## Lectura y escritura de blobs y tablas al procesar un mensaje de cola

Los atributos **Blob** y **Table** permiten leer y escribir los blobs y tablas. Los ejemplos en esta sección se aplican a los blobs. Para los ejemplos de código que muestran cómo desencadenar procesos cuando se crean o actualizan los blobs, consulte [Uso del almacenamiento de blobs de Azure con el SDK de WebJobs](../app-service-web/websites-dotnet-webjobs-sdk-storage-blobs-how-to.md), y para obtener ejemplos de código que leen y escriben las tablas, vea [Cómo usar el almacenamiento de tablas Azure con el SDK de WebJobs](../app-service-web/websites-dotnet-webjobs-sdk-storage-tables-how-to.md).

### Mensajes en cola de cadena que desencadenan operaciones de blob

Para un mensaje de cola que contiene una cadena, **queueTrigger** es un marcador de posición que se puede usar en el parámetro **blobPath** del atributo **Blob** que tiene el contenido del mensaje.

El ejemplo siguiente usa objetos **Stream** para leer y escribir blobs. El mensaje en cola es el nombre de un blob ubicado en el contenedor textblobs. Se creará una copia del blob con "-new" anexado al nombre en el mismo contenedor.

		public static void ProcessQueueMessage(
		    [QueueTrigger("blobcopyqueue")] string blobName,
		    [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput)
		{
		    blobInput.CopyTo(blobOutput, 4096);
		}

El constructor de atributo **Blob** toma un parámetro **blobPath** que especifica el nombre del contenedor y el nombre del blob. Para obtener más información acerca de este marcador de posición, consulte [Uso del almacenamiento de blobs de Azure con el SDK de WebJobs](../app-service-web/websites-dotnet-webjobs-sdk-storage-blobs-how-to.md).

Cuando el atributo representa a un objeto **Stream**, otro parámetro de constructor especifica el modo **FileAccess** como lectura, escritura o lectura/escritura.

En el ejemplo siguiente se usa un objeto **CloudBlockBlob** para eliminar un blob. El mensaje en cola es el nombre del blob.

		public static void DeleteBlob(
		    [QueueTrigger("deleteblobqueue")] string blobName,
		    [Blob("textblobs/{queueTrigger}")] CloudBlockBlob blobToDelete)
		{
		    blobToDelete.Delete();
		}

### Mensajes en cola POCO [ (objeto CRL estándar](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object))

Para un objeto POCO almacenado como JSON en el mensaje de la cola, puede usar marcadores de posición que asignen nombre a propiedades del objeto en el parámetro **blobPath** del atributo **Queue**. También puede utilizar nombres de propiedad de metadatos de cola como marcadores de posición. Consulte [Obtener metadatos de cola o de mensaje en cola](#get-queue-or-queue-message-metadata).

El ejemplo siguiente copia un blob a un blob nuevo con una extensión distinta. El mensaje de la cola es un objeto **BlobInformation** que incluye las propiedades **BlobName** y **BlobNameWithoutExtension**. Los nombres de propiedad se usan como marcadores de posición en la ruta de acceso del blob para los atributos **Blob**.

		public static void CopyBlobPOCO(
		    [QueueTrigger("copyblobqueue")] BlobInformation blobInfo,
		    [Blob("textblobs/{BlobName}", FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{BlobNameWithoutExtension}.txt", FileAccess.Write)] Stream blobOutput)
		{
		    blobInput.CopyTo(blobOutput, 4096);
		}

El SDK usa el [paquete Newtonsoft.Json NuGet](http://www.nuget.org/packages/Newtonsoft.Json) para serializar y deserializar los mensajes. Si crea mensajes en cola en un programa que no usa el SDK de WebJobs, puede escribir código similar al siguiente ejemplo para crear un mensaje en cola POCO que pueda analizar el SDK.

		BlobInformation blobInfo = new BlobInformation() { BlobName = "boot.log", BlobNameWithoutExtension = "boot" };
		var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
		logQueue.AddMessage(queueMessage);

Si necesita realizar algún trabajo en la función antes de enlazar un blob a un objeto, puede usar el atributo en el cuerpo de la función, como se ha mostrado en [Utilizar atributos del SDK de WebJobs en el cuerpo de una función](#use-webjobs-sdk-attributes-in-the-body-of-a-function).

###Tipos con los que puede usar el atributo Blob

El atributo **Blob** se puede usar con los siguientes tipos:

* **Stream** (lectura o escritura, especificado según el uso del parámetro del constructor FileAccess)
* **TextReader**
* **TextWriter**
* **string** (lectura)
* **out string** (escritura; crea un blob solo si el parámetro de cadena es no nulo cuando se devuelve la función)
* POCO (lectura)
* out POCO (escritura; siempre crea un blob, crea como objeto nulo si el parámetro POCO es nulo cuando se devuelve la función)
* **CloudBlobStream** (escritura)
* **ICloudBlob** (lectura o escritura)
* **CloudBlockBlob** (lectura o escritura)
* **CloudPageBlob** (lectura o escritura)

##Control de mensajes dudosos

Los mensajes cuyo contenido produce un error de una función se denominan *mensajes dudosos*. Cuando se produce un error en la función, el mensaje de la cola no se elimina y se recoge de nuevo, provocando que el ciclo se repita. El SDK puede interrumpir automáticamente el ciclo después de un número limitado de iteraciones, o puede hacerlo usted manualmente.

### Control automático de mensajes dudosos

El SDK llamará a una función hasta 5 veces para procesar un mensaje de la cola. Si se produce un error en el quinta intento, el mensaje se mueve a una cola de mensajes dudosos. Puede ver cómo configurar el número máximo de reintentos en [Establecimiento de opciones de configuración](#how-to-set-configuration-options).

La cola de mensajes dudosos se denomina *{originalqueuename}*-poison. Puede escribir una función para procesar los mensajes desde la cola de mensajes dudosos registrándolos o enviando una notificación indicando que se necesita atención manual.

En el siguiente ejemplo la función **CopyBlob** generará un error cuando un mensaje de cola contenga el nombre de un blob que no existe. Cuando esto ocurre, el mensaje se mueve desde la cola de copyblobqueue a la cola copyblobqueue-poison. Después, **ProcessPoisonMessage** registra el mensaje dudoso.

		public static void CopyBlob(
		    [QueueTrigger("copyblobqueue")] string blobName,
		    [Blob("textblobs/{queueTrigger}", FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{queueTrigger}-new", FileAccess.Write)] Stream blobOutput)
		{
		    blobInput.CopyTo(blobOutput, 4096);
		}

		public static void ProcessPoisonMessage(
		    [QueueTrigger("copyblobqueue-poison")] string blobName, TextWriter logger)
		{
		    logger.WriteLine("Failed to copy blob, name=" + blobName);
		}

La ilustración siguiente muestra la salida de consola de estas funciones cuando se procesa un mensaje dudoso.

![Salida de consola para el control de mensajes dudosos](./media/vs-storage-webjobs-getting-started-queues/poison.png)

### Control manual de mensajes dudosos

Puede obtener el número de veces que se ha recogido un mensaje para el procesamiento agregando un parámetro **int** llamado **dequeueCount** a la función. Puede consultar el recuento de eliminación de cola en el código de función y realizar su propio control de mensajes dudosos cuando el número supere un umbral, tal y como se muestra en el ejemplo siguiente.

		public static void CopyBlob(
		    [QueueTrigger("copyblobqueue")] string blobName, int dequeueCount,
		    [Blob("textblobs/{queueTrigger}", FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{queueTrigger}-new", FileAccess.Write)] Stream blobOutput,
		    TextWriter logger)
		{
		    if (dequeueCount > 3)
		    {
		        logger.WriteLine("Failed to copy blob, name=" + blobName);
		    }
		    else
		    {
		    blobInput.CopyTo(blobOutput, 4096);
		    }
		}

## Establecimiento de opciones de configuración

Puede usar el tipo **JobHostConfiguration** para establecer las opciones de configuración siguientes:

* Establecer las cadenas de conexión de SDK en el código.
* Definir la configuración **QueueTrigger** como el número máximo de eliminaciones de la cola.
* Obtener nombres de cola a partir de la configuración.

###Establecimiento de cadenas de conexión del SDK en código

Configurar las cadenas de conexión de SDK en el código permite utilizar sus propios nombres de cadena de conexión en archivos de configuración o las variables de entorno, como se muestra en el ejemplo siguiente.

		static void Main(string[] args)
		{
		    var _storageConn = ConfigurationManager
		        .ConnectionStrings["MyStorageConnection"].ConnectionString;

		    var _dashboardConn = ConfigurationManager
		        .ConnectionStrings["MyDashboardConnection"].ConnectionString;

		    var _serviceBusConn = ConfigurationManager
		        .ConnectionStrings["MyServiceBusConnection"].ConnectionString;

		    JobHostConfiguration config = new JobHostConfiguration();
		    config.StorageConnectionString = _storageConn;
		    config.DashboardConnectionString = _dashboardConn;
		    config.ServiceBusConnectionString = _serviceBusConn;
		    JobHost host = new JobHost(config);
		    host.RunAndBlock();
		}

### Configurar QueueTrigger

Puede configurar los siguientes valores, que se aplican para el procesamiento de mensajes de la cola:

- El número máximo de mensajes en cola que se recogen simultáneamente para ejecutarse en paralelo (el valor predeterminado es 16).
- El número máximo de reintentos antes de enviar un mensaje de la cola a una cola de mensajes dudosos (el valor predeterminado es 5).
- El tiempo de espera máximo antes de volver a sondear cuando una cola está vacía (el valor predeterminado es 1 minuto).

En el ejemplo siguiente se muestra cómo configurar estas opciones:

		static void Main(string[] args)
		{
		    JobHostConfiguration config = new JobHostConfiguration();
		    config.Queues.BatchSize = 8;
		    config.Queues.MaxDequeueCount = 4;
		    config.Queues.MaxPollingInterval = TimeSpan.FromSeconds(15);
		    JobHost host = new JobHost(config);
		    host.RunAndBlock();
		}

### Establecer valores para los parámetros del constructor del SDK de WebJobs en el código

En ocasiones, deseará especificar un nombre de cola, un contenedor o nombre de blob o un nombre de cola en el código en lugar de codificarlo. Por ejemplo, es posible que desee especificar el nombre de la cola para **QueueTrigger** en un archivo de configuración o una variable de entorno.

Puede hacerlo pasando un objeto **NameResolver** para el tipo **JobHostConfiguration**. Incluya marcadores de posición especiales rodeados de signos de porcentaje (%) en los parámetros del constructor de atributos del SDK de WebJobs y el código **NameResolver** especifica los valores reales que se usarán en lugar de esos marcadores de posición.

Por ejemplo, suponga que desea usar una cola denominada logqueuetest en el entorno de prueba y un logqueueprod denominado en producción. En lugar de un nombre de cola codificado de forma rígida, desea especificar el nombre de una entrada en la colección **appSettings** que tendría el nombre de cola real. Si la clave **appSettings** es logqueue, la función podría ser similar al ejemplo siguiente.

		public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
		{
		    Console.WriteLine(logMessage);
		}

La clase **NameResolver** podría entonces obtener el nombre de la cola de **appSettings** como se muestra en el ejemplo siguiente:

		public class QueueNameResolver : INameResolver
		{
		    public string Resolve(string name)
		    {
		        return ConfigurationManager.AppSettings[name].ToString();
		    }
		}

Pasa la clase **NameResolver** en el objeto **JobHost** tal y como se muestra en el ejemplo siguiente.

		static void Main(string[] args)
		{
		    JobHostConfiguration config = new JobHostConfiguration();
		    config.NameResolver = new QueueNameResolver();
		    JobHost host = new JobHost(config);
		    host.RunAndBlock();
		}

**Nota**: los nombres de cola, tabla y blob se resuelven cada vez que se llama a una función, pero los nombres de contenedores de blobs solo se resuelven cuando se inicia la aplicación. No puede cambiar el nombre del contenedor de blobs mientras el trabajo está en ejecución.

## Desencadenar una función manualmente

Para desencadenar una función manualmente, use el método **Call** o **CallAsync** en el objeto **JobHost** y el atributo **NoAutomaticTrigger** en la función, tal y como se muestra en el ejemplo siguiente.

		public class Program
		{
		    static void Main(string[] args)
		    {
		        JobHost host = new JobHost();
		        host.Call(typeof(Program).GetMethod("CreateQueueMessage"), new { value = "Hello world!" });
		    }

		    [NoAutomaticTrigger]
		    public static void CreateQueueMessage(
		        TextWriter logger,
		        string value,
		        [Queue("outputqueue")] out string message)
		    {
		        message = value;
		        logger.WriteLine("Creating queue message: ", message);
		    }
		}

## Escritura de registros

El Panel muestra registros en dos lugares: la página del trabajo web y la página de una invocación de trabajo web en especial.

![Registros en la página del trabajo web](./media/vs-storage-webjobs-getting-started-queues/dashboardapplogs.png)

![Registros en la página de invocación de la función](./media/vs-storage-webjobs-getting-started-queues/dashboardlogs.png)

El resultado de los métodos de consola que llama en una función o en el método **Main()** aparece en la página Panel del WebJob y no en la página de una invocación de método en especial. El resultado del objeto TextWriter que obtiene a partir de un parámetro en la firma del método aparece en la página Panel de una invocación de un método.

El resultado de la consola no se puede vincular a una invocación de método en especial, porque la consola tiene un solo subproceso, mientras que muchas funciones de trabajo se pueden ejecutar al mismo tiempo. Esta es la razón por la que el SDK proporciona a cada invocación de función su objeto escritor de registros único.

Para escribir [registros de seguimiento de aplicación](web-sites-dotnet-troubleshoot-visual-studio.md#logsoverview), use **Console.Out** (crea registros marcados como INFO) y **Console.Error** (crea registros marcados como ERROR). Una alternativa es usar [Trace o TraceSource](http://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx), que proporciona niveles de Modo detallado, Advertencia y Críticos, además de Info y Error. Los registros de seguimiento de aplicaciones aparecen en los archivos de registro de la aplicación web, tablas de Azure o blobs de Azure, dependiendo de cómo se configuró la aplicación web de Azure. Como ocurre en todos los resultados de la consola, los 100 registros de aplicación más recientes también aparecen en la página Panel para el trabajo web, no en la página para una innovación de función.

El resultado de la consola aparece en el Panel solo si el programa se ejecuta en un Azure WebJob, no si el programa se ejecuta localmente o en algún otro entorno.

Para deshabilitar el registro estableciendo la cadena de conexión del panel en null. Para obtener más información, consulte [Establecimiento de opciones de configuración](#how-to-set-configuration-options).

En el ejemplo siguiente se muestran varias maneras de escribir registros:

		public static void WriteLog(
		    [QueueTrigger("logqueue")] string logMessage,
		    TextWriter logger)
		{
		    Console.WriteLine("Console.Write - " + logMessage);
		    Console.Out.WriteLine("Console.Out - " + logMessage);
		    Console.Error.WriteLine("Console.Error - " + logMessage);
		    logger.WriteLine("TextWriter - " + logMessage);
		}

En el panel del SDK de WebJobs, la salida del objeto **TextWriter** aparece cuando va a la página para una invocación de función determinada y hace clic en **Alternar salida**:

![Clic en el vínculo de invocación de la función](./media/vs-storage-webjobs-getting-started-queues/dashboardinvocations.png)

![Registros en la página de invocación de la función](./media/vs-storage-webjobs-getting-started-queues/dashboardlogs.png)

En el Panel del SDK de WebJobs, las 100 líneas más recientes de los resultados de la consola aparecen cuando va a la página de WebJob (no de la invocación de función) y hace clic en **Alternar resultados**.

![Clic en Alternar resultados](./media/vs-storage-webjobs-getting-started-queues/dashboardapplogs.png)

En un WebJob continuo, los registros de aplicación aparecen en /data/jobs/continuous/*{webjobname}*/job\_log.txt en el sistema de archivos de la aplicación web.

		[09/26/2014 21:01:13 > 491e54: INFO] Console.Write - Hello world!
		[09/26/2014 21:01:13 > 491e54: ERR ] Console.Error - Hello world!
		[09/26/2014 21:01:13 > 491e54: INFO] Console.Out - Hello world!

En un blob de Azure el aspecto de los registros de aplicación es similar al siguiente: 2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738373502,0,17404,17,Console.Write - Hello world!, 2014-09-26T21:01:13,Error,contosoadsnew,491e54,635473620738373502,0,17404,19,Console.Error - Hello world!, 2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738529920,0,17404,17,Console.Out - Hello world!,

Y en una tabla de Azure los registros **Console.Out** y **Console.Error** tienen el siguiente aspecto:

![Registro de información en la tabla](./media/vs-storage-webjobs-getting-started-queues/tableinfo.png)

![Registro de errores en la tabla](./media/vs-storage-webjobs-getting-started-queues/tableerror.png)

##Pasos siguientes

En este artículo se han proporcionado ejemplos de código que muestran cómo tratar escenarios comunes para trabajar con colas de Azure. Para obtener más información acerca de cómo usar el SDK de WebJobs y WebJobs de Azure, consulte [Recursos de documentación de WebJobs de Azure](http://go.microsoft.com/fwlink/?linkid=390226).

<!---HONumber=AcomDC_0224_2016-->