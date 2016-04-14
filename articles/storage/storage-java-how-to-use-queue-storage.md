<properties
	pageTitle="Uso del almacenamiento de colas de Java | Microsoft Azure"
	description="Aprenda a utilizar el servicio Cola de Azure para crear y eliminar colas e insertar, obtener y eliminar mensajes. Ejemplos escritos en Java."
	services="storage"
	documentationCenter="java"
	authors="rmcmurray"
	manager="wpickett"
	editor="jimbe"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="Java"
	ms.topic="article"
	ms.date="02/29/2016"
	ms.author="robmcm"/>

# Uso del almacenamiento de colas de Java

[AZURE.INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)]

## Información general

Esta guía muestra cómo realizar algunas tareas comunes a través del servicio de almacenamiento en cola de Azure. Los ejemplos están escritos en Java y utilizan el [SDK de almacenamiento de Azure para Java][]. Entre los escenarios descritos se incluyen **insertar**, **ojear**, **obtener** y **eliminar** mensajes de la cola, así como **crear** y **eliminar** colas. Para obtener más información sobre las colas, consulte la sección [Pasos siguientes](#Next-Steps).

Nota: hay un SDK disponible para los desarrolladores que usen el almacenamiento de Azure en dispositivos Android. Para obtener más información, vea el [SDK de almacenamiento de Azure para Android][].

[AZURE.INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Creación de una aplicación Java

En esta guía utilizará funciones del almacenamiento que puede ejecutar en una aplicación Java localmente o bien mediante código a través de un rol web o un rol de trabajo de Azure.

Para ello, deberá instalar el Kit de desarrollo de Java (JDK) y crear una cuenta de almacenamiento de Azure en su suscripción de Azure. Después, deberá verificar que su sistema de desarrollo satisface los requisitos mínimos y las dependencias que se indican en el repositorio del [SDK de almacenamiento de Azure para Java][] en GitHub. Si su sistema cumple esos requisitos, puede seguir las instrucciones para descargar e instalar las bibliotecas de almacenamiento de Azure para Java en su sistema desde ese repositorio. Cuando haya completado esas tareas, podrá crear una aplicación Java que use los ejemplos de este artículo.

## Configuración de la aplicación para obtener acceso al almacenamiento en cola

Agregue las siguientes instrucciones de importación en la parte superior del archivo Java en el que desea utilizar las API de almacenamiento de Azure para obtener acceso a las colas:

    // Include the following imports to use queue APIs.
    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.queue.*;

## Configuración de una cadena de conexión de almacenamiento de Azure

Un cliente de almacenamiento de Azure utiliza una cadena de conexión de almacenamiento para almacenar extremos y credenciales con el fin de obtener acceso a los servicios de administración de datos. Al ejecutarse en una aplicación cliente, debe proporcionar la cadena de conexión de almacenamiento en el siguiente formato, usando el nombre de su cuenta de almacenamiento y la clave de acceso principal de la cuenta de almacenamiento que se muestra en el [Portal de Azure](https://portal.azure.com) para los valores *AccountName* y *AccountKey*. En este ejemplo se muestra cómo puede declarar un campo estático para mantener la cadena de conexión:

    // Define the connection-string with your values.
    public static final String storageConnectionString =
        "DefaultEndpointsProtocol=http;" +
        "AccountName=your_storage_account;" +
        "AccountKey=your_storage_account_key";

En una aplicación que se esté ejecutando en un rol de Microsoft Azure, esta cadena se puede almacenar en el archivo de configuración de servicio, *ServiceConfiguration.cscfg*, y se puede obtener acceso a él con una llamada al método **RoleEnvironment.getConfigurationSettings**. A continuación se muestra un ejemplo de cómo obtener la cadena de conexión desde un elemento de **configuración** denominado *StorageConnectionString* en el archivo de configuración del servicio:

    // Retrieve storage account from connection-string.
    String storageConnectionString =
        RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");

En los ejemplos siguientes se supone que ha usado uno de estos dos métodos para obtener la cadena de conexión de almacenamiento.

## Creación de una cola

Los objetos **CloudQueueClient** le permiten obtener objetos de referencia para las colas. El siguiente código crea un objeto **CloudQueueClient**. (Nota: Hay otras maneras de crear objetos **CloudStorageAccount**. Para obtener más información, consulte **CloudStorageAccount** en la Referencia del [SDK de cliente de almacenamiento de Azure]).

Use el objeto **CloudQueueClient** para obtener una referencia a la cola que desea utilizar. En caso de que la cola no exista todavía, es posible crearla.

    try
    {
    	// Retrieve storage account from connection-string.
    	CloudStorageAccount storageAccount =
	       CloudStorageAccount.parse(storageConnectionString);

	   // Create the queue client.
	   CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

	   // Retrieve a reference to a queue.
	   CloudQueue queue = queueClient.getQueueReference("myqueue");

	   // Create the queue if it doesn't already exist.
	   queue.createIfNotExists();
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## Incorporación de un mensaje a una cola

Para insertar un mensaje en una cola existente, cree en primer lugar un nuevo **CloudQueueMessage**. A continuación, llame al método **addMessage**. Se puede crear un **CloudQueueMessage** a partir de una cadena (en formato UTF-8) o de una matriz de bytes. A continuación se muestra el código con el que se crea una cola (si no existe) y se inserta el mensaje "Hola, mundo".

    try
    {
    	// Retrieve storage account from connection-string.
    	CloudStorageAccount storageAccount =
	       CloudStorageAccount.parse(storageConnectionString);

    	// Create the queue client.
    	CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    	// Retrieve a reference to a queue.
    	CloudQueue queue = queueClient.getQueueReference("myqueue");

    	// Create the queue if it doesn't already exist.
    	queue.createIfNotExists();

    	// Create a message and add it to the queue.
    	CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    	queue.addMessage(message);
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## Cómo ojear el siguiente mensaje

Puede inspeccionar el mensaje situado en la parte delantera de una cola, sin quitarlo de la cola, mediante una llamada al método **peekMessage**.

    try
    {
    	// Retrieve storage account from connection-string.
    	CloudStorageAccount storageAccount =
	       CloudStorageAccount.parse(storageConnectionString);

    	// Create the queue client.
    	CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    	// Retrieve a reference to a queue.
    	CloudQueue queue = queueClient.getQueueReference("myqueue");

    	// Peek at the next message.
    	CloudQueueMessage peekedMessage = queue.peekMessage();

    	// Output the message value.
    	if (peekedMessage != null)
    	{
		  System.out.println(peekedMessage.getMessageContentAsString());
	   }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## Cómo cambiar el contenido de un mensaje en cola

Puede cambiar el contenido de un mensaje local en la cola. Si el mensaje representa una tarea de trabajo, puede usar esta característica para actualizar el estado de la tarea de trabajo. El siguiente código actualiza el mensaje de la cola con contenido nuevo y amplía el tiempo de espera de la visibilidad en 60 segundos más. De este modo, se guarda el estado de trabajo asociado al mensaje y se le proporciona al cliente un minuto más para que siga elaborando el mensaje. Esta técnica se puede utilizar para realizar un seguimiento de los flujos de trabajo de varios pasos en los mensajes en cola, sin que sea necesario volver a empezar desde el principio si se produce un error en un paso del proceso a causa de un error de hardware o software. Normalmente, también mantendría un número de reintentos y, si el mensaje se intentara más de *n* veces, lo eliminaría. Esto proporciona protección frente a un mensaje que produce un error en la aplicación cada vez que se procesa.

El siguiente código de ejemplo busca en la cola de mensajes, encuentra el primer mensaje cuyo contenido coincide con "Hola, mundo" y luego modifica el contenido del mensaje "Hola mundo" y se cierra.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

    	// Create the queue client.
    	CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    	// Retrieve a reference to a queue.
    	CloudQueue queue = queueClient.getQueueReference("myqueue");

        // The maximum number of messages that can be retrieved is 32.
        final int MAX_NUMBER_OF_MESSAGES_TO_PEEK = 32;

        // Loop through the messages in the queue.
        for (CloudQueueMessage message : queue.retrieveMessages(MAX_NUMBER_OF_MESSAGES_TO_PEEK,1,null,null))
        {
            // Check for a specific string.
            if (message.getMessageContentAsString().equals("Hello, World"))
            {
                // Modify the content of the first matching message.
                message.setMessageContent("Updated contents.");
                // Set it to be visible in 30 seconds.
                EnumSet<MessageUpdateFields> updateFields =
                    EnumSet.of(MessageUpdateFields.CONTENT,
                    MessageUpdateFields.VISIBILITY);
                // Update the message.
                queue.updateMessage(message, 30, updateFields, null, null);
                break;
            }
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

Como alternativa, el siguiente código de ejemplo actualiza únicamente el primer mensaje visible de la cola.

    try
    {
    	// Retrieve storage account from connection-string.
    	CloudStorageAccount storageAccount =
	       CloudStorageAccount.parse(storageConnectionString);

    	// Create the queue client.
    	CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    	// Retrieve a reference to a queue.
    	CloudQueue queue = queueClient.getQueueReference("myqueue");

    	// Retrieve the first visible message in the queue.
    	CloudQueueMessage message = queue.retrieveMessage();

    	if (message != null)
    	{
            // Modify the message content.
            message.setMessageContent("Updated contents.");
            // Set it to be visible in 60 seconds.
            EnumSet<MessageUpdateFields> updateFields =
                EnumSet.of(MessageUpdateFields.CONTENT,
                MessageUpdateFields.VISIBILITY);
            // Update the message.
            queue.updateMessage(message, 60, updateFields, null, null);
    	}
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## Cómo obtener la longitud de la cola

Puede obtener una estimación del número de mensajes existentes en una cola. El método **downloadAttributes** pide al servicio de cola varios valores actuales, incluido un conteo de cuántos mensajes están en la cola. El recuento solo es aproximado, ya que se pueden agregar o borrar mensajes después de que el servicio de cola haya respondido su solicitud. El método **getApproximateMethodCount** devuelve el último valor recuperado por la llamada a **downloadAttributes**, sin llamar al servicio de cola.

    try
    {
    	// Retrieve storage account from connection-string.
    	CloudStorageAccount storageAccount =
	       CloudStorageAccount.parse(storageConnectionString);

    	// Create the queue client.
    	CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    	// Retrieve a reference to a queue.
    	CloudQueue queue = queueClient.getQueueReference("myqueue");

	   // Download the approximate message count from the server.
    	queue.downloadAttributes();

    	// Retrieve the newly cached approximate message count.
    	long cachedMessageCount = queue.getApproximateMessageCount();

    	// Display the queue length.
    	System.out.println(String.format("Queue length: %d", cachedMessageCount));
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## Extracción del siguiente mensaje de la cola

El código extrae un mensaje de una cola en dos pasos. Al llamar a **retrieveMessage**, obtiene el siguiente mensaje de una cola. Un mensaje devuelto por **retrieveMessage** se hace invisible a cualquier otro código de lectura de mensajes de esta cola. De forma predeterminada, este mensaje permanece invisible durante 30 segundos. Para terminar de quitar el mensaje de la cola, también debe llamar a **deleteMessage**. Este proceso extracción de un mensaje que consta de dos pasos garantiza que si su código no puede procesar un mensaje a causa de un error de hardware o software, otra instancia de su código puede obtener el mismo mensaje e intentarlo de nuevo. Su código llama a **deleteMessage** justo después de que se haya procesado el mensaje.

    try
    {
    	// Retrieve storage account from connection-string.
    	CloudStorageAccount storageAccount =
    	    CloudStorageAccount.parse(storageConnectionString);

    	// Create the queue client.
    	CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    	// Retrieve a reference to a queue.
    	CloudQueue queue = queueClient.getQueueReference("myqueue");

    	// Retrieve the first visible message in the queue.
    	CloudQueueMessage retrievedMessage = queue.retrieveMessage();

    	if (retrievedMessage != null)
    	{
    		// Process the message in less than 30 seconds, and then delete the message.
    		queue.deleteMessage(retrievedMessage);
    	}
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }


## Opciones adicionales para quitar mensajes de la cola

Hay dos formas de personalizar la recuperación de mensajes de una cola. En primer lugar, puede obtener un lote de mensajes (hasta 32). En segundo lugar, puede establecer un tiempo de espera de la invisibilidad más largo o más corto para que el código disponga de más o menos tiempo para procesar cada mensaje.

El siguiente ejemplo de código utiliza el método **retrieveMessage** para obtener 20 mensajes en una llamada. A continuación, procesa cada mensaje con un bucle **for**. También establece el tiempo de espera de la invisibilidad en cinco minutos (300 segundos) para cada mensaje. Tenga en cuenta que los cinco minutos empiezan a contar para todos los mensajes al mismo tiempo, por lo que después de pasar los cinco minutos desde la llamada a **retrieveMessage**, todos los mensajes que no se han eliminado volverán a estar visibles.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

        // Retrieve a reference to a queue.
        CloudQueue queue = queueClient.getQueueReference("myqueue");

        // Retrieve 20 messages from the queue with a visibility timeout of 300 seconds.
        for (CloudQueueMessage message : queue.retrieveMessages(20, 300, null, null)) {
            // Do processing for all messages in less than 5 minutes,
            // deleting each message after processing.
            queue.deleteMessage(message);
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## Enumeración de las colas

Para obtener una lista de las colas actuales, llame al método **CloudQueueClient.listQueues()**, el cual devolverá una colección de objetos **CloudQueue**.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient =
            storageAccount.createCloudQueueClient();

        // Loop through the collection of queues.
        for (CloudQueue queue : queueClient.listQueues())
        {
            // Output each queue name.
            System.out.println(queue.getName());
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## Cómo eliminar una cola

Para eliminar una cola y todos los mensajes contenidos en ella, llame al método **deleteIfExists** en el objeto de cola **CloudQueue**.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

        // Retrieve a reference to a queue.
        CloudQueue queue = queueClient.getQueueReference("myqueue");

        // Delete the queue if it exists.
        queue.deleteIfExists();
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## Pasos siguientes

Ahora que está familiarizado con los aspectos básicos del almacenamiento de colas, utilice estos vínculos para obtener más información acerca de tareas de almacenamiento más complejas.

- [SDK de almacenamiento de Azure para Java][]
- [Referencia del SDK de cliente de almacenamiento de Azure][]
- [API de REST de servicios de almacenamiento de Azure][]
- [Blog del equipo de almacenamiento de Azure][]

[Azure SDK for Java]: http://go.microsoft.com/fwlink/?LinkID=525671
[SDK de almacenamiento de Azure para Java]: https://github.com/azure/azure-storage-java
[SDK de almacenamiento de Azure para Android]: https://github.com/azure/azure-storage-android
[Referencia del SDK de cliente de almacenamiento de Azure]: http://dl.windowsazure.com/storage/javadoc/
[SDK de cliente de almacenamiento de Azure]: http://dl.windowsazure.com/storage/javadoc/
[API de REST de servicios de almacenamiento de Azure]: https://msdn.microsoft.com/library/azure/dd179355.aspx
[Blog del equipo de almacenamiento de Azure]: http://blogs.msdn.com/b/windowsazurestorage/

<!---HONumber=AcomDC_0302_2016-->