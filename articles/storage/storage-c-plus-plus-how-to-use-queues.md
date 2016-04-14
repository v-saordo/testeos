<properties
    pageTitle="Cómo usar el almacenamiento de colas (C++) | Microsoft Azure"
    description="Aprenda a usar el servicio de almacenamiento de colas en Azure. Los ejemplos están escritos en C++."
    services="storage"
    documentationCenter=".net"
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/17/2016"
    ms.author="dineshm"/>

# Uso del almacenamiento de colas de C++  

[AZURE.INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)]

## Información general
Esta guía muestra cómo realizar algunas tareas comunes a través del servicio de almacenamiento en cola de Azure. Los ejemplos están escritos en C++ y usan la [biblioteca de cliente de almacenamiento de Azure para C++](http://github.com/Azure/azure-storage-cpp/blob/master/README.md). Entre los escenarios descritos se incluyen **insertar**, **ojear**, **obtener** y **eliminar** mensajes de la cola, así como **crear y eliminar colas**.

>[AZURE.NOTE] Esta guía se destina a la biblioteca de cliente de almacenamiento de Azure para C++, versión 1.0.0 y posteriores. La versión recomendada es la biblioteca de cliente de almacenamiento 2.2.0, que se encuentra disponible a través de [NuGet](http://www.nuget.org/packages/wastorage) o [GitHub](http://github.com/Azure/azure-storage-cpp/).

[AZURE.INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]
[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Creación de una aplicación de C++  
En esta guía, usará las características de almacenamiento que se pueden ejecutar en una aplicación C++.

Para ello, deberá instalar la biblioteca de cliente de almacenamiento de Azure para C++ y crear una cuenta de almacenamiento de Azure en su suscripción de Azure.

Para instalar la biblioteca de cliente de almacenamiento de Azure para C++, puede usar los métodos siguientes:

-	**Linux:** siga las instrucciones indicadas en la página [Léame de la biblioteca de cliente de almacenamiento de Azure para C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md).  
-	**Windows:** en Visual Studio, haga clic en **Herramientas > Administrador de paquetes de NuGet > Consola del Administrador de paquetes**. Escriba el siguiente comando en la [Consola del Administrador de paquetes de NuGet](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) y presione **ENTRAR**.  

		Install-Package wastorage

## Configuración de la aplicación para obtener acceso al almacenamiento en cola
Agregue las siguientes instrucciones include en la parte superior del archivo C++ en el que desea usar las API de almacenamiento de Azure para obtener acceso a las colas:

	#include "was/storage_account.h"
	#include "was/queue.h"

## Configuración de una cadena de conexión de almacenamiento de Azure

Un cliente de almacenamiento de Azure utiliza una cadena de conexión de almacenamiento para almacenar extremos y credenciales con el fin de obtener acceso a los servicios de administración de datos. Al ejecutarse en una aplicación cliente, debe proporcionar la cadena de conexión de almacenamiento en el siguiente formato, usando el nombre de su cuenta de almacenamiento y la clave de acceso de almacenamiento de la cuenta de almacenamiento que se muestra en el [Portal de Azure](https://portal.azure.com) para los valores *AccountName* y *AccountKey*. Para obtener información sobre las cuentas de almacenamiento y las claves de acceso, consulte [Acerca de las cuentas de almacenamiento de Azure](storage-create-storage-account.md). En este ejemplo se muestra cómo puede declarar un campo estático para mantener la cadena de conexión:

	// Define the connection-string with your values.
	const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));

Para probar la aplicación en el equipo local de Windows, puede usar el [emulador de almacenamiento](storage-use-emulator.md) de Microsoft Azure que se instala con el [SDK de Azure](https://azure.microsoft.com/downloads/). El emulador de almacenamiento es una utilidad que simula los servicios Blob, Cola y Tabla disponibles en Azure en el equipo de desarrollo local. En el ejemplo siguiente se muestra cómo puede declarar un campo estático para mantener la cadena de conexión en el emulador de almacenamiento local:

	// Define the connection-string with Azure Storage Emulator.
	const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  

Para iniciar el emulador de almacenamiento de Azure, seleccione el botón **Inicio** o presione la tecla **Windows**. Comience a escribir **Emulador de almacenamiento de Azure** y seleccione **Emulador de almacenamiento de Microsoft Azure** en la lista de aplicaciones.

En los ejemplos siguientes se supone que usó uno de estos dos métodos para obtener la cadena de conexión de almacenamiento.

## Recuperación de la cadena de conexión
Puede usar la clase **cloud\_storage\_account** para representar la información de la cuenta de almacenamiento. Para recuperar la información de la cuenta de almacenamiento a partir de la cadena de conexión de almacenamiento, puede usar el método **parse**.

	// Retrieve storage account from connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

## Creación de una cola
Los objetos **cloud\_queue\_client** le permiten obtener objetos de referencia para las colas. El siguiente código crea un objeto **cloud\_queue\_client**.

	// Retrieve storage account from connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create a queue client.
	azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

Use el objeto **cloud\_queue\_client** para obtener una referencia a la cola que desea usar. En caso de que la cola no exista todavía, es posible crearla.

	// Retrieve a reference to a queue.
	azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

	// Create the queue if it doesn't already exist.
 	queue.create_if_not_exists();  

## Cómo insertar un mensaje en una cola
Para insertar un mensaje en una cola existente, cree en primer lugar un nuevo **cloud\_queue\_message**. A continuación, llame al método **add\_message**. Se puede crear un **cloud\_queue\_message** a partir de una cadena o de una matriz de **bytes**. A continuación se muestra el código con el que se crea una cola (si no existe) y se inserta el mensaje "Hola, mundo":

	// Retrieve storage account from connection-string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the queue client.
	azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

	// Retrieve a reference to a queue.
	azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

	// Create the queue if it doesn't already exist.
	queue.create_if_not_exists();

	// Create a message and add it to the queue.
	azure::storage::cloud_queue_message message1(U("Hello, World"));
	queue.add_message(message1);  

## Cómo ojear el siguiente mensaje
Puede ojear el mensaje situado en la parte delantera de una cola, sin quitarlo de la cola, llamando al método **peek\_message**.

	// Retrieve storage account from connection-string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the queue client.
	azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

	// Retrieve a reference to a queue.
	azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

	// Peek at the next message.
	azure::storage::cloud_queue_message peeked_message = queue.peek_message();

	// Output the message content.
	std::wcout << U("Peeked message content: ") << peeked_message.content_as_string() << std::endl;

## Cómo cambiar el contenido de un mensaje en cola
Puede cambiar el contenido de un mensaje local en la cola. Si el mensaje representa una tarea de trabajo, puede usar esta característica para actualizar el estado de la tarea de trabajo. El siguiente código actualiza el mensaje de la cola con contenido nuevo y amplía el tiempo de espera de la visibilidad en 60 segundos más. De este modo, se guarda el estado de trabajo asociado al mensaje y se le proporciona al cliente un minuto más para que siga elaborando el mensaje. Esta técnica se puede usar para realizar un seguimiento de los flujos de trabajo de varios pasos en los mensajes en cola, sin que sea necesario volver a empezar desde el principio si se produce un error en un paso del proceso a causa de un error de hardware o software. Normalmente, también mantendría un número de reintentos y, si el mensaje se intentara más de n veces, lo eliminaría. Esto proporciona protección frente a un mensaje que produce un error en la aplicación cada vez que se procesa.

	// Retrieve storage account from connection-string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_conection_string);

	// Create the queue client.
	azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

	// Retrieve a reference to a queue.
	azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

	// Get the message from the queue and update the message contents.
	// The visibility timeout "0" means make it visible immediately.
	// The visibility timeout "60" means the client can get another minute to continue
	// working on the message.
	azure::storage::cloud_queue_message changed_message = queue.get_message();

	changed_message.set_content(U("Changed message"));
	queue.update_message(changed_message, std::chrono::seconds(60), true);

	// Output the message content.
	std::wcout << U("Changed message content: ") << changed_message.content_as_string() << std::endl;  

## Cómo extraer el siguiente mensaje de la cola
El código quita un mensaje de una cola en dos pasos. Si se llama a **get\_message**, obtiene el siguiente mensaje de una cola. Un mensaje devuelto por **get\_message** se hace invisible a cualquier otro código que lea mensajes de esta cola. Para terminar quitando el mensaje de la cola, también debe llamar a **delete\_message**. Este proceso de extracción de un mensaje que consta de dos pasos garantiza que si su código no puede procesar un mensaje a causa de un error de hardware o software, otra instancia de su código puede obtener el mismo mensaje e intentarlo de nuevo. El código llama a **delete\_message** justo después de que se haya procesado el mensaje.

	// Retrieve storage account from connection-string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the queue client.
	azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

	// Retrieve a reference to a queue.
	azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

	// Get the next message.
	azure::storage::cloud_queue_message dequeued_message = queue.get_message();
	std::wcout << U("Dequeued message: ") << dequeued_message.content_as_string() << std::endl;

	// Delete the message.
	queue.delete_message(dequeued_message);

## Cómo usar las opciones adicionales para quitar mensajes de la cola
Hay dos formas de personalizar la recuperación de mensajes de una cola. En primer lugar, puede obtener un lote de mensajes (hasta 32). En segundo lugar, puede establecer un tiempo de espera de la invisibilidad más largo o más corto para que el código disponga de más o menos tiempo para procesar cada mensaje. El siguiente ejemplo de código usa el método **get\_messages** para obtener 20 mensajes en una llamada. A continuación, procesa cada mensaje con un bucle **for**. También establece el tiempo de espera de la invisibilidad en cinco minutos para cada mensaje. Tenga en cuenta que los 5 minutos empiezan a contar para todos los mensajes al mismo tiempo, por lo que después de que pasen los 5 minutos desde la llamada a **get\_messages**, todos los mensajes que no se han eliminado volverán a estar visibles.

	// Retrieve storage account from connection-string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the queue client.
	azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

	// Retrieve a reference to a queue.
	azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

	// Dequeue some queue messages (maximum 32 at a time) and set their visibility timeout to
	// 5 minutes (300 seconds).
	azure::storage::queue_request_options options;
	azure::storage::operation_context context;

	// Retrieve 20 messages from the queue with a visibility timeout of 300 seconds.
	std::vector<azure::storage::cloud_queue_message> messages = queue.get_messages(20, std::chrono::seconds(300), options, context);

	for (auto it = messages.cbegin(); it != messages.cend(); ++it)
	{
		// Display the contents of the message.
		std::wcout << U("Get: ") << it->content_as_string() << std::endl;
	}

## Cómo obtener la longitud de la cola
Puede obtener una estimación del número de mensajes existentes en una cola. El método **download\_attributes** solicita al servicio Cola la recuperación de los atributos de la cola, incluido el número de mensajes. El método **approximate\_message\_count** obtiene el número aproximado de mensajes en la cola.

	// Retrieve storage account from connection-string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the queue client.
	azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

	// Retrieve a reference to a queue.
	azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

	// Fetch the queue attributes.
	queue.download_attributes();

	// Retrieve the cached approximate message count.
	int cachedMessageCount = queue.approximate_message_count();

	// Display number of messages.
	std::wcout << U("Number of messages in queue: ") << cachedMessageCount << std::endl;  

## Cómo eliminar una cola
Para eliminar una cola y todos los mensajes contenidos en ella, llame al método **delete\_queue\_if\_exists** en el objeto de cola.

	// Retrieve storage account from connection-string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the queue client.
	azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

	// Retrieve a reference to a queue.
	azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

	// If the queue exists and delete it.
	queue.delete_queue_if_exists();  

## Pasos siguientes
Ahora que está familiarizado con los aspectos básicos del almacenamiento de colas, siga estos vínculos para obtener más información sobre Almacenamiento de Azure.

-	[Cómo usar el almacenamiento de blobs de C++](storage-c-plus-plus-how-to-use-blobs.md)
-	[Cómo usar el almacenamiento de tablas de C++](storage-c-plus-plus-how-to-use-tables.md)
-	[Enumeración de los recursos de almacenamiento de Azure en C++](storage-c-plus-plus-enumeration.md)
-	[Referencia de la biblioteca de clientes de almacenamiento para C++](http://azure.github.io/azure-storage-cpp)
-	[Documentación de Almacenamiento de Azure](https://azure.microsoft.com/documentation/services/storage/)

<!----HONumber=AcomDC_0224_2016-->