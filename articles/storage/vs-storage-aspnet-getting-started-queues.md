<properties
	pageTitle="Introducción al almacenamiento de colas y los servicios conectados de Visual Studio (ASP.NET) | Microsoft Azure"
	description="Cómo empezar a usar el almacenamiento de colas de Azure en un proyecto ASP.NET en Visual Studio después de conectarse a una cuenta de almacenamiento mediante los servicios conectados de Visual Studio"
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

# Introducción al almacenamiento Cola de Azure y los servicios relacionados de Visual Studio

## Información general

En este artículo se describe cómo empezar a usar el almacenamiento Cola de Azure en Visual Studio después de haber creado o hecho referencia a una cuenta de almacenamiento de Azure en un proyecto de ASP.NET con el cuadro de diálogo **Agregar servicios conectados** de Visual Studio.

Le mostraremos cómo crear una cola de Azure en la cuenta de almacenamiento y cómo acceder a ella. También le mostraremos cómo realizar operaciones básicas de cola, como agregar, modificar, leer y quitar mensajes de cola. Los ejemplos están escritos en código C# y usan la [biblioteca del cliente de almacenamiento de Microsoft Azure para .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx). Para obtener más información acerca de ASP.NET, consulte [ASP.NET](http://www.asp.net).

El almacenamiento en cola de Azure es un servicio para almacenar grandes cantidades de mensajes a los que puede obtenerse acceso desde cualquier lugar del mundo a través de llamadas autenticadas con HTTP o HTTPS. Un único mensaje en cola puede tener un tamaño de hasta 64 KB y una cola puede contener millones de mensajes, hasta el límite de capacidad total de una cuenta de almacenamiento.

## Acceso a colas en el código

Para obtener acceso a las colas en los proyectos de ASP.NET, debe incluir los elementos siguientes en los archivos de origen C# que tengan acceso al almacenamiento de colas de Azure.

1. Asegúrese de que las declaraciones del espacio de nombres de la parte superior del archivo de C# incluyen estas instrucciones **using**.

		using Microsoft.Framework.Configuration;
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Queue;

2. Obtenga un objeto **CloudStorageAccount** que represente la información de su cuenta de almacenamiento. Use el código siguiente para obtener la cadena de conexión de almacenamiento y la información de la cuenta de almacenamiento de la configuración del servicio de Azure.

		 CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		   CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. Obtenga un objeto **CloudTableClient** para hacer referencia a los objetos de cola en la cuenta de almacenamiento.

	    // Create the CloudQueueClient object for this storage account.
    	CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

4. Obtenga un objeto **CloudQueue** para hacer referencia a una cola específica.

    	// Get a reference to a queue named "messageQueue"
	    CloudQueue messageQueue = queueClient.GetQueueReference("messageQueue");


**NOTA:** use todo el código anterior delante del código que aparece en los ejemplos siguientes.

## Creación de una cola en código

Para crear una cola de Azure en el código, simplemente agregue una llamada a **CreateIfNotExists** en el código anterior.

	// Create the messageQueue if it does not exist
	messageQueue.CreateIfNotExists();

## un mensaje a una cola

Para insertar un mensaje en una cola existente, cree un nuevo objeto **CloudQueueMessage** y luego llame al método **AddMessage**.

Se puede crear un objeto **CloudQueueMessage** a partir de una cadena (en formato UTF-8) o de una matriz de bytes.

Este es un ejemplo que inserta el mensaje "Hello, World".

	// Create a message and add it to the queue.
	CloudQueueMessage message = new CloudQueueMessage("Hello, World");
	messageQueue.AddMessage(message);

## Leer un mensaje de una cola

Puede inspeccionar el mensaje situado en la parte delantera de una cola, sin quitarlo de la cola, mediante una llamada al método PeekMessage().

	// Peek at the next message
    CloudQueueMessage peekedMessage = messageQueue.PeekMessage();

## Leer y eliminar un mensaje de una cola

Su código puede quitar un mensaje de una cola en dos pasos. 1. Llame a GetMessage() para obtener el siguiente mensaje en una cola. Un mensaje devuelto por GetMessage() se hace invisible a cualquier otro código de lectura de mensajes de esta cola. De forma predeterminada, este mensaje permanece invisible durante 30 segundos. 2. Para terminar de quitar el mensaje de la cola, llame a **DeleteMessage**.

Este proceso extracción de un mensaje que consta de dos pasos garantiza que si su código no puede procesar un mensaje a causa de un error de hardware o software, otra instancia de su código puede obtener el mismo mensaje e intentarlo de nuevo. El código siguiente llama a **DeleteMessage** justo después de haber procesado el mensaje.

	// Get the next message in the queue.
	CloudQueueMessage retrievedMessage = messageQueue.GetMessage();

	// Process the message in less than 30 seconds

	// Then delete the message.
	await messageQueue.DeleteMessage(retrievedMessage);


## Usar opciones adicionales para quitar mensajes de la cola

Hay dos formas de personalizar la recuperación de mensajes de una cola. En primer lugar, puede obtener un lote de mensajes (hasta 32). En segundo lugar, puede establecer un tiempo de espera de la invisibilidad más largo o más corto para que el código disponga de más o menos tiempo para procesar cada mensaje. El siguiente ejemplo de código utiliza el método **GetMessages** para obtener 20 mensajes en una llamada. A continuación, procesa cada mensaje con un bucle **foreach**. También establece el tiempo de espera de la invisibilidad en cinco minutos para cada mensaje. Tenga en cuenta que los 5 minutos empiezan a contar para todos los mensajes al mismo tiempo, por lo que después de pasar los 5 minutos desde la llamada a **GetMessages**, todos los mensajes que no se han eliminado volverán a estar visibles.

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    foreach (CloudQueueMessage message in queue.GetMessages(20, TimeSpan.FromMinutes(5)))
    {
        // Process all messages in less than 5 minutes, deleting each message after processing.
        queue.DeleteMessage(message);
    }

## la longitud de la cola

Puede obtener una estimación del número de mensajes existentes en una cola. El método **FetchAttributes** solicita al servicio de cola que recupere los atributos de la cola, incluido el número de mensajes. La propiedad **ApproximateMethodCount** devuelve el último valor recuperado por el método **FetchAttributes**, sin llamar al servicio de cola.

	// Fetch the queue attributes.
	messageQueue.FetchAttributes();

    // Retrieve the cached approximate message count.
    int? cachedMessageCount = messageQueue.ApproximateMessageCount;

	// Display number of messages.
	Console.WriteLine("Number of messages in queue: " + cachedMessageCount);

## Uso del patrón Async-Await con API de cola comunes

En este ejemplo se muestra cómo usar el patrón Async-Await con API de cola comunes. El ejemplo llama a la versión asincrónica de cada uno de los métodos determinados, esto se puede ver en la corrección de Async posterior de cada método. Cuando se utiliza un método asincrónico, el patrón Async-Await suspende la ejecución local hasta que se complete la llamada. Este comportamiento permite que el subproceso actual realice otro trabajo que ayuda a evitar cuellos de botella en el rendimiento y mejora la capacidad de respuesta general de la aplicación. Para obtener más información sobre el uso del patrón Async-Await en. NET, consulte [Async y Await (C# y Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx)

    // Create a message to put in the queue
    CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

    // Async enqueue the message
    await messageQueue.AddMessageAsync(cloudQueueMessage);
    Console.WriteLine("Message added");

    // Async dequeue the message
    CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();
    Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

    // Async delete the message
    await messageQueue.DeleteMessageAsync(retrievedMessage);
    Console.WriteLine("Deleted message");

## Eliminación de una cola

Para eliminar una cola y todos los mensajes contenidos en ella, llame al método **Delete** en el objeto de cola.

    // Delete the queue.
    messageQueue.Delete();

## Pasos siguientes

[AZURE.INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]

<!---HONumber=AcomDC_0224_2016-->