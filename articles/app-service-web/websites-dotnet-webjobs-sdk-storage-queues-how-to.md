<properties 
	pageTitle="Uso del almacenamiento de colas de Azure con el SDK de WebJobs" 
	description="Obtenga información sobre cómo usar el almacenamiento de colas de Azure con el SDK de WebJobs. Cree y elimine colas; inserte, inspeccione, obtenga y elimine mensajes de la cola y mucho más." 
	services="app-service\web, storage" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="12/14/2015" 
	ms.author="tdykstra"/>

# Uso del almacenamiento de colas de Azure con el SDK de WebJobs

## Información general

Esta guía proporciona ejemplos de código C# que muestran cómo usar la versión 1.x del SDK de WebJobs de Azure con el servicio de almacenamiento de colas de Azure.

En la guía se supone que sabe [cómo crear un proyecto de trabajos web en Visual Studio con cadenas de conexión que señalan a su cuenta de almacenamiento](websites-dotnet-webjobs-sdk-get-started.md#configure-storage) o a [varias cuentas de almacenamiento](https://github.com/Azure/azure-webjobs-sdk/blob/master/test/Microsoft.Azure.WebJobs.Host.EndToEndTests/MultipleStorageAccountsEndToEndTests.cs).

La mayoría de los fragmentos de código muestran solo las funciones, no el código que crea el objeto `JobHost` como en este ejemplo:

		static void Main(string[] args)
		{
		    JobHost host = new JobHost();
		    host.RunAndBlock();
		}
		
La guía incluye los siguientes temas:

-   [Cómo desencadenar una función cuando se recibe un mensaje en cola](#trigger)
	- Mensajes en cola de cadena
	- Mensaje en cola de POCO
	- Funciones asincrónicas
	- Tipos con los que funciona el atributo QueueTrigger
	- Algoritmo de sondeo
	- Varias instancias
	- Ejecución en paralelo
	- Obtener metadatos de cola o de mensaje en cola
	- Apagado correcto
-   [Cómo crear un mensaje en cola mientras se procesa un mensaje en cola](#createqueue)
	- Mensajes en cola de cadena
	- Mensaje en cola de POCO
	- Crear varios mensajes o en funciones asincrónicas
	- Tipos con los que funciona el atributo Queue
	- Utilizar atributos del SDK de WebJobs en el cuerpo de una función
-   [Cómo leer y escribir datos BLOB al procesar un mensaje en cola](#blobs)
	- Mensajes en cola de cadena
	- Mensaje en cola de POCO
	- Tipos con los que funciona el atributo Blob
-   [Cómo controlar los mensajes dudosos](#poison)
	- Control automático de mensajes dudosos
	- Control manual de mensajes dudosos
-   [Cómo establecer opciones de configuración](#config)
	- Establecer cadenas de conexión de SDK en el código
	- Configurar QueueTrigger
	- Establecer valores para los parámetros del constructor del SDK de WebJobs en el código
-   [Cómo desencadenar manualmente una función](#manual)
-   [Cómo escribir registros](#logs) 
-   [Control de errores y configuración de tiempos de espera](#errors)
-   [Pasos siguientes](#nextsteps)

## <a id="trigger"></a> Cómo desencadenar una función cuando se recibe un mensaje en cola

Para escribir una función que el SDK de WebJobs llama cuando se recibe un mensaje en cola, use el atributo `QueueTrigger`. El constructor de atributo toma un parámetro de cadena que especifica el nombre de la cola para sondear. También puede [establecer dinámicamente el nombre de cola](#config).

### Mensajes en cola de cadena

En el ejemplo siguiente, la cola contiene un mensaje de cadena, por lo que se aplica `QueueTrigger` a un parámetro de cadena denominado `logMessage`, que contiene el contenido del mensaje en cola. La función [ escribe un mensaje de registro en el panel](#logs).
 

		public static void ProcessQueueMessage([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
		{
		    logger.WriteLine(logMessage);
		}

Ademas de `string`, el parámetro puede ser una matriz de bytes, un objeto `CloudQueueMessage` o un objeto POCO que defina.

### Mensajes en cola POCO [ (objeto CRL estándar](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object))

En el ejemplo siguiente, el mensaje en cola contiene JSON para un objeto `BlobInformation` que incluye una propiedad `BlobName`. El SDK automáticamente deserializa el objeto.

		public static void WriteLogPOCO([QueueTrigger("logqueue")] BlobInformation blobInfo, TextWriter logger)
		{
		    logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
		}

El SDK usa el [paquete Newtonsoft.Json NuGet](http://www.nuget.org/packages/Newtonsoft.Json) para serializar y deserializar los mensajes. Si crea mensajes en cola en un programa que no usa el SDK de WebJobs, puede escribir código similar al siguiente ejemplo para crear un mensaje en cola POCO que pueda analizar el SDK.

		BlobInformation blobInfo = new BlobInformation() { BlobName = "log.txt" };
		var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
		logQueue.AddMessage(queueMessage);

### Funciones asincrónicas

La siguiente función async [escribe un registro en el panel](#logs).

		public async static Task ProcessQueueMessageAsync([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
		{
		    await logger.WriteLineAsync(logMessage);
		}

Las funciones asincrónicas pueden tomar un [token de cancelación](http://www.asp.net/mvc/overview/performance/using-asynchronous-methods-in-aspnet-mvc-4#CancelToken), como se muestra en el siguiente ejemplo, que copia un blob. (Para obtener una explicación del marcador de posición `queueTrigger` consulte la sección [Blobs](#blobs).)

		public async static Task ProcessQueueMessageAsyncCancellationToken(
		    [QueueTrigger("blobcopyqueue")] string blobName, 
		    [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput,
		    CancellationToken token)
		{
		    await blobInput.CopyToAsync(blobOutput, 4096, token);
		}

### <a id="qtattributetypes"></a> Tipos con los que funciona el atributo QueueTrigger

Puede usar `QueueTrigger` con los siguientes tipos:

* `string`
* Un tipo de POCO serializado como JSON
* `byte[]`
* `CloudQueueMessage`

### <a id="polling"></a> Algoritmo de sondeo

El SDK implementa un algoritmo de interrupción exponencial aleatorio para reducir el efecto del sondeo de cola inactiva en los costos de transacción de almacenamiento. Cuando se encuentra un mensaje, el SDK espera dos segundos y, a continuación, comprueba si hay otro mensaje; cuando no se encuentra ningún mensaje, espera unos cuatro segundos antes de intentarlo de nuevo. Después de varios intentos fallidos para obtener un mensaje de la cola, el tiempo de espera sigue aumentando hasta que alcanza el tiempo de espera máximo, predeterminado en un minuto. [El tiempo de espera máximo es configurable](#config).

### <a id="instances"></a> Varias instancias

Si la aplicación web se ejecuta en varias instancias, un trabajo web continuo se ejecuta en cada máquina y esta última esperará a los desencadenadores e intentará ejecutar funciones. El desencadenador de la cola del SDK de WebJobs automáticamente impide que una función procese un mensaje de cola varias veces; las funciones no tienen que escribirse para que sean idempotentes. Sin embargo, si desea asegurarse de que solo una instancia de una función se ejecuta incluso cuando hay varias instancias de la aplicación web de host, puede utilizar el atributo `Singleton`.

### <a id="parallel"></a> Ejecución en paralelo

Si tiene varias funciones escuchando en diferentes colas, el SDK las llamará en paralelo cuando se reciban mensajes simultáneamente.

Esto también se da cuando se reciben varios mensajes de una sola cola. De forma predeterminada, el SDK obtiene un lote de 16 mensajes de la cola a la vez y ejecuta la función que se procesa en paralelo. [El tamaño del lote es configurable](#config). Cuando el número que se está procesando llegue a la mitad del tamaño del lote, el SDK obtiene otro lote y empieza a procesar los mensajes. Por lo tanto, el número máximo de mensajes simultáneos que se procesan por función es uno y medio por el tamaño del lote. Este límite se aplica por separado a cada función que tiene un atributo `QueueTrigger`.

Si no desea ejecutar en paralelo los mensajes en una cola, establezca el tamaño del lote en 1. Consulte también **más control sobre el procesamiento de colas** en [RTM de SDK de WebJobs de Azure 1.1.0](/blog/azure-webjobs-sdk-1-1-0-rtm/).

### <a id="queuemetadata"></a>Obtener metadatos de cola o de mensaje en cola

Puede obtener las siguientes propiedades de mensaje agregando parámetros a la firma del método:

* `DateTimeOffset` expirationTime
* `DateTimeOffset` insertionTime
* `DateTimeOffset` nextVisibleTime
* `string` queueTrigger (contiene el texto del mensaje)
* `string` id
* `string` popReceipt
* `int` dequeueCount

Si desea trabajar directamente con la API de almacenamiento de Azure, también puede agregar un parámetro `CloudStorageAccount`.

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

### <a id="graceful"></a>Cierre estable

Una función que se ejecuta en un trabajo web continuo puede aceptar un parámetro `CancellationToken` que permite al sistema operativo notificar a la función cuándo el trabajo web esté a punto de terminar. Puede utilizar esta notificación para asegurarse de que la función no se termina inesperadamente en una forma que deje los datos en un estado incoherente.

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

## <a id="createqueue"></a> Cómo crear un mensaje en cola mientras se procesa un mensaje en cola

Para escribir una función que crea un mensaje en cola nuevo, use el atributo `Queue`. Al igual que `QueueTrigger`, se pasa el nombre de la cola como una cadena, o bien puede [establecer dinámicamente el nombre de cola](#config).

### Mensajes en cola de cadena

El siguiente ejemplo de código no asincrónico crea un mensaje en cola nuevo en la cola llamada "outputqueue", con el mismo contenido que el mensaje en cola recibido en la cola llamada "inputqueue". (En el caso de las funciones asincrónicas, use `IAsyncCollector<T>` como se muestra más adelante en esta sección).


		public static void CreateQueueMessage(
		    [QueueTrigger("inputqueue")] string queueMessage,
		    [Queue("outputqueue")] out string outputQueueMessage )
		{
		    outputQueueMessage = queueMessage;
		}
  
### Mensajes en cola POCO [(objeto CRL estándar](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object))

Para crear un mensaje en cola que contiene un objeto POCO en lugar de una cadena, pase el tipo POCO como un parámetro de salida al `Queue` constructor de atributo.
 
		public static void CreateQueueMessage(
		    [QueueTrigger("inputqueue")] BlobInformation blobInfoInput,
		    [Queue("outputqueue")] out BlobInformation blobInfoOutput )
		{
		    blobInfoOutput = blobInfoInput;
		}

El SDK serializa automáticamente el objeto a JSON. Siempre se crea un mensaje en cola, incluso si el objeto es null.

### Crear varios mensajes o en funciones asincrónicas

Para crear varios mensajes, cree el tipo de parámetro para la cola de salida `ICollector<T>` o `IAsyncCollector<T>`, como se muestra en el ejemplo siguiente.

		public static void CreateQueueMessages(
		    [QueueTrigger("inputqueue")] string queueMessage,
		    [Queue("outputqueue")] ICollector<string> outputQueueMessage,
		    TextWriter logger)
		{
		    logger.WriteLine("Creating 2 messages in outputqueue");
		    outputQueueMessage.Add(queueMessage + "1");
		    outputQueueMessage.Add(queueMessage + "2");
		}

Cada mensaje en cola se crea inmediatamente cuando se llama al método `Add`.

### Tipos con los que funciona el atributo Queue

Puede usar el atributo `Queue` en los siguientes tipos de parámetro:

* `out string` (crea un mensaje en cola si el valor de parámetro es no nulo cuando termina la función)
* `out byte[]` (funciona como `string`) 
* `out CloudQueueMessage` (funciona como `string`) 
* `out POCO` (un tipo serializable, crea un mensaje con un objeto nulo si el parámetro es nulo cuando termina la función)
* `ICollector`
* `IAsyncCollector`
* `CloudQueue` (para crear manualmente mensajes usando directamente la API de almacenamiento de Azure)

### <a id="ibinder"></a>Utilizar atributos del SDK de WebJobs en el cuerpo de una función

Si necesita realizar algún trabajo en la función antes de usar un atributo del SDK de WebJobs como `Queue`, `Blob`, o `Table`, puede usar la interfaz `IBinder`.

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

La interfaz `IBinder` también se puede usar con los atributos `Table` y `Blob`.

## <a id="blobs"></a> Cómo leer y escribir blobs y tablas al procesar un mensaje en cola

Los atributos `Blob` y `Table` permiten leer y escribir blobs y tablas. Los ejemplos en esta sección se aplican a los blobs. Para los ejemplos de código que muestran cómo desencadenar procesos cuando se crean o actualizan los blobs, consulte [Uso del almacenamiento de blobs de Azure con el SDK de WebJobs](websites-dotnet-webjobs-sdk-storage-blobs-how-to.md), y para obtener ejemplos de código que leen y escriben las tablas, vea [Cómo usar el almacenamiento de tablas Azure con el SDK de WebJobs](websites-dotnet-webjobs-sdk-storage-tables-how-to.md).

### Mensajes en cola de cadena que desencadenan operaciones de blob

En el caso de un mensaje en cola que contiene una cadena, `queueTrigger` es un marcador de posición que puede usar en el parámetro `blobPath` del atributo `Blob` que contiene el contenido del mensaje.

El ejemplo siguiente usa objetos `Stream` para leer y escribir blobs. El mensaje en cola es el nombre de un blob ubicado en el contenedor textblobs. Se creará una copia del blob con "-new" anexado al nombre en el mismo contenedor.

		public static void ProcessQueueMessage(
		    [QueueTrigger("blobcopyqueue")] string blobName, 
		    [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput)
		{
		    blobInput.CopyTo(blobOutput, 4096);
		}

El constructor de atributo `Blob` toma un parámetro `blobPath` que especifica el nombre del contenedor y el nombre del blob. Para obtener más información acerca de este marcador de posición, consulte [Uso del almacenamiento de blobs de Azure con el SDK de WebJobs](websites-dotnet-webjobs-sdk-storage-blobs-how-to.md),

Cuando el atributo representa a un objeto `Stream`, otro parámetro de constructor especifica el modo `FileAccess` como lectura, escritura o lectura/escritura.

El siguiente ejemplo utiliz un objeto `CloudBlockBlob` para eliminar un blob. El mensaje en cola es el nombre del blob.

		public static void DeleteBlob(
		    [QueueTrigger("deleteblobqueue")] string blobName,
		    [Blob("textblobs/{queueTrigger}")] CloudBlockBlob blobToDelete)
		{
		    blobToDelete.Delete();
		}

### <a id="pocoblobs"></a> Mensajes en cola POCO [(objeto CRL estándar](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object))

Para un objeto POCO almacenado como JSON en el mensaje en cola, puede usar marcadores de posición que nombren las propiedades del objeto en el parámetro `blobPath` del atributo `Queue`. También puede utilizar [nombres de propiedad de metadatos de cola](#queuemetadata) como marcadores de posición.

El ejemplo siguiente copia un blob a un blob nuevo con una extensión distinta. El mensaje en cola es un objeto `BlobInformation` que incluye las propiedades `BlobName` y `BlobNameWithoutExtension`. Los nombres de propiedad se usan como marcadores de posición en la ruta de acceso del blob para los atributos `Blob`.
 
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

Si necesita realizar algún trabajo en la función antes de enlazar un blob a un objeto, puede usar el atributo en el cuerpo de la función, [como se ha mostrado anteriormente para el atributo de cola](#ibinder).

### <a id="blobattributetypes"></a> Tipos con los que puede usar el atributo Blob
 
El atributo `Blob` se puede usar con los siguientes tipos:

* `Stream` (lectura o escritura, especificado según el uso del parámetro del constructor FileAccess)
* `TextReader`
* `TextWriter`
* `string` (lectura)
* `out string` (escritura; crea un blob solo si el parámetro de cadena es no nulo cuando se devuelve la función)
* POCO (lectura)
* out POCO (escritura; siempre crea un blob, crea como objeto nulo si el parámetro POCO es nulo cuando se devuelve la función)
* `CloudBlobStream` (escritura)
* `ICloudBlob` (lectura o escritura)
* `CloudBlockBlob` (lectura o escritura) 
* `CloudPageBlob` (lectura o escritura) 

## <a id="poison"></a> Cómo controlar los mensajes dudosos

Los mensajes cuyo contenido produce un error de una función se denominan *mensajes dudosos*. Cuando se produce un error en la función, el mensaje de la cola no se elimina y se recoge de nuevo, provocando que el ciclo se repita. El SDK puede interrumpir automáticamente el ciclo después de un número limitado de iteraciones, o puede hacerlo usted manualmente.

### Control automático de mensajes dudosos

El SDK llamará a una función hasta 5 veces para procesar un mensaje de la cola. Si se produce un error en el quinta intento, el mensaje se mueve a una cola de mensajes dudosos. [Es posible configurar el número máximo de reintentos](#config).

La cola de mensajes dudosos se denomina *{originalqueuename}*-poison. Puede escribir una función para procesar los mensajes desde la cola de mensajes dudosos registrándolos o enviando una notificación indicando que se necesita atención manual.

En el siguiente ejemplo, la función `CopyBlob` presentará un error cuando un mensaje en cola contenga el nombre de un blob que no existe. Cuando esto ocurre, el mensaje se mueve desde la cola de copyblobqueue a la cola copyblobqueue-poison. A continuación, `ProcessPoisonMessage` registra el mensaje dudoso.

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

![Salida de consola para el control de mensajes dudosos](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/poison.png)

### Control manual de mensajes dudosos

Puede obtener el número de veces que se ha seleccionado un mensaje para el procesamiento agregando un parámetro `int` denominado `dequeueCount` a su función. Puede consultar el recuento de eliminación de cola en el código de función y realizar su propio control de mensajes dudosos cuando el número supere un umbral, tal y como se muestra en el ejemplo siguiente.

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

## <a id="config"></a> Cómo establecer las opciones de configuración

Puede usar el tipo `JobHostConfiguration` para establecer las siguientes opciones de configuración:

* Establecer las cadenas de conexión de SDK en el código.
* Configurar `QueueTrigger` como el recuento de eliminación de cola máximo.
* Obtener nombres de cola a partir de la configuración.

### <a id="setconnstr"></a>Establecer cadenas de conexión de SDK en el código

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

### <a id="configqueue"></a>Configurar QueueTrigger

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

### <a id="setnamesincode"></a>Establecer valores para los parámetros del constructor del SDK de WebJobs en el código

En ocasiones, deseará especificar un nombre de cola, un contenedor o nombre de blob o un nombre de cola en el código en lugar de codificarlo. Por ejemplo, es posible que desee especificar el nombre de la cola para `QueueTrigger` en un archivo de configuración o una variable de entorno.

Para hacerlo, puede pasar un objeto `NameResolver` al tipo `JobHostConfiguration`. Incluya marcadores de posición especiales rodeados de signos de porcentaje (%) en los parámetros del constructor de atributos del SDK de WebJobs y el código `NameResolver` especifica los valores reales que se utilizarán en lugar de esos marcadores de posición.

Por ejemplo, suponga que desea usar una cola denominada logqueuetest en el entorno de prueba y un logqueueprod denominado en producción. En lugar de codificar de forma rígida un nombre de cola, es preferible especificar el nombre de una entrada en la colección `appSettings` que tendría el nombre de la cola real. Si la clave `appSettings` es logqueue, la función podría parecerse al siguiente ejemplo.

		public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
		{
		    Console.WriteLine(logMessage);
		}

La clase `NameResolver` podría obtener el nombre de la cola de `appSettings` como se muestra en el ejemplo siguiente:

		public class QueueNameResolver : INameResolver
		{
		    public string Resolve(string name)
		    {
		        return ConfigurationManager.AppSettings[name].ToString();
		    }
		}

Pase la clase `NameResolver` al objeto `JobHost`, como se muestra en el ejemplo siguiente.

		static void Main(string[] args)
		{
		    JobHostConfiguration config = new JobHostConfiguration();
		    config.NameResolver = new QueueNameResolver();
		    JobHost host = new JobHost(config);
		    host.RunAndBlock();
		}
 
**Nota**: los nombres de cola, tabla y blob se resuelven cada vez que se llama a una función, pero los nombres de contenedores de blobs solo se resuelven cuando se inicia la aplicación. No puede cambiar el nombre del contenedor de blobs mientras el trabajo está en ejecución.

## <a id="manual"></a>Cómo desencadenar manualmente una función

Para desencadenar manualmente una función, use el método `Call` o `CallAsync` en el objeto `JobHost` y el atributo `NoAutomaticTrigger` en la función, como aparece en el siguiente ejemplo.

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

## <a id="logs"></a>Cómo escribir registros

El Panel muestra registros en dos lugares: la página del trabajo web y la página de una invocación de trabajo web en especial.

![Registros en la página del trabajo web](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardapplogs.png)

![Registros en la página de invocación de la función](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardlogs.png)

El resultado de los métodos de consola que llama en una función o en el método `Main()` aparece en la página Panel del trabajo web y no en la página de una invocación de método en especial. El resultado del objeto TextWriter que obtiene a partir de un parámetro en la firma del método aparece en la página Panel de una invocación de un método.

El resultado de la consola no se puede vincular a una invocación de método en especial, porque la consola tiene un solo subproceso, mientras que muchas funciones de trabajo se pueden ejecutar al mismo tiempo. Esta es la razón por la que el SDK proporciona a cada invocación de función su objeto escritor de registros único.

Para escribir [registros de seguimiento de aplicación](web-sites-dotnet-troubleshoot-visual-studio.md#logsoverview), use `Console.Out` (crea registros marcados como INFO) y `Console.Error` (crea registros marcados como ERROR). Una alternativa es usar [Trace o TraceSource](http://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx), que proporciona niveles de Modo detallado, Advertencia y Críticos, además de Info y Error. Los registros de seguimiento de aplicaciones aparecen en los archivos de registro de la aplicación web, tablas de Azure o blobs de Azure, dependiendo de cómo se configuró la aplicación web de Azure. Como ocurre en todos los resultados de la consola, los 100 registros de aplicación más recientes también aparecen en la página Panel para el trabajo web, no en la página para una innovación de función.

El resultado de la consola aparece en el Panel solo si el programa se ejecuta en un Azure WebJob, no si el programa se ejecuta localmente o en algún otro entorno.

Deshabilite el registro del panel para escenarios de alto rendimiento. De forma predeterminada, el SDK escribe registros en el almacenamiento y esta actividad puede reducir el rendimiento cuando se procesan muchos mensajes. Para deshabilitar el registro, establezca la cadena de conexión del panel en un valor nulo como se muestra en el ejemplo siguiente.

		JobHostConfiguration config = new JobHostConfiguration();       
		config.DashboardConnectionString = “”;        
		JobHost host = new JobHost(config);
		host.RunAndBlock();

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

En el Panel del SDK de WebJobs, el resultado del objeto `TextWriter` aparece cuando va a la página de una invocación de función especial y hace clic en **Alternar resultados**:

![Clic en el vínculo de invocación de la función](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardinvocations.png)

![Registros en la página de invocación de la función](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardlogs.png)

En el Panel del SDK de WebJobs, las 100 líneas más recientes de los resultados de la consola aparecen cuando va a la página de WebJob (no de la invocación de función) y hace clic en **Alternar resultados**.
 
![Clic en Alternar resultados](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardapplogs.png)

En un WebJob continuo, los registros de aplicación aparecen en /data/jobs/continuous/*{webjobname}*/job\_log.txt en el sistema de archivos de la aplicación web.

		[09/26/2014 21:01:13 > 491e54: INFO] Console.Write - Hello world!
		[09/26/2014 21:01:13 > 491e54: ERR ] Console.Error - Hello world!
		[09/26/2014 21:01:13 > 491e54: INFO] Console.Out - Hello world!

En un blob de Azure el aspecto de los registros de aplicación es similar al siguiente: 2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738373502,0,17404,17,Console.Write - Hello world!, 2014-09-26T21:01:13,Error,contosoadsnew,491e54,635473620738373502,0,17404,19,Console.Error - Hello world!, 2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738529920,0,17404,17,Console.Out - Hello world!,

Y en una tabla de Azure, los registros de `Console.Out` y `Console.Error` tienen este aspecto:

![Registro de información en la tabla](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/tableinfo.png)

![Registro de errores en la tabla](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/tableerror.png)

Si desea conectar su propio registrador, consulte [este ejemplo](http://github.com/Azure/azure-webjobs-sdk-samples/blob/master/BasicSamples/MiscOperations/Program.cs).

## <a id="errors"></a>Control de errores y configuración de tiempos de espera

El SDK de WebJobs también incluye un atributo de [tiempo de espera](http://github.com/Azure/azure-webjobs-sdk-samples/blob/master/BasicSamples/MiscOperations/Functions.cs) que puede usar para hacer que una función se cancele si no se completa dentro del período de tiempo especificado. Y si desea generar una alerta cuando se producen demasiados errores dentro de un período de tiempo especificado, puede utilizar el atributo `ErrorTrigger`. Este es un [ejemplo de ErrorTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Error-Monitoring).

```
public static void ErrorMonitor(
[ErrorTrigger("00:01:00", 1)] TraceFilter filter, TextWriter log,
[SendGrid(
    To = "admin@emailaddress.com",
    Subject = "Error!")]
 SendGridMessage message)
{
    // log last 5 detailed errors to the Dashboard
   log.WriteLine(filter.GetDetailedMessage(5));
   message.Text = filter.GetDetailedMessage(1);
}
```

También puede deshabilitar y habilitar de manera dinámica las funciones para controlar si pueden desencadenarse, mediante un modificador de configuración que podría ser una configuración de la aplicación o el nombre de variable de entorno. Para el código de ejemplo, consulte el atributo `Disable` del [repositorio de ejemplos del SDK de WebJobs](https://github.com/Azure/azure-webjobs-sdk-samples/blob/master/BasicSamples/MiscOperations/Functions.cs).

## <a id="nextsteps"></a> Pasos siguientes

En esta guía se han proporcionado ejemplos de código que muestran cómo controlar los escenarios comunes para trabajar con colas de Azure. Para obtener más información acerca de cómo usar el SDK de WebJobs y WebJobs de Azure, consulte [Recursos de WebJobs de Azure recomendados](http://go.microsoft.com/fwlink/?linkid=390226).
 

<!---HONumber=AcomDC_1217_2015-->