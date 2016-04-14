<properties
    pageTitle="Cómo usar el almacenamiento de blobs (C++) | Microsoft Azure"
    description="Obtenga información sobre cómo usar el servicio de almacenamiento de blobs de Azure. Los ejemplos están escritos en C++."
    services="storage"
    documentationCenter=".net"
    authors="tamram"
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

# Cómo usar el almacenamiento de blobs de C++  

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## Información general
Esta guía demuestra cómo realizar algunas tareas comunes a través del servicio de almacenamiento de blobs de Azure. Los ejemplos están escritos en C++ y usan la [biblioteca de cliente de almacenamiento de Azure para C++](http://github.com/Azure/azure-storage-cpp/blob/master/README.md). Entre los escenarios descritos se incluyen **cargar**, **enumerar**, **descargar** y **eliminar** blobs.

>[AZURE.NOTE] Esta guía se destina a la biblioteca de cliente de almacenamiento de Azure para C++, versión 1.0.0 y posteriores. La versión recomendada es la biblioteca de cliente de almacenamiento 2.2.0, que se encuentra disponible a través de [NuGet](http://www.nuget.org/packages/wastorage) o [GitHub](https://github.com/Azure/azure-storage-cpp).

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]
[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Creación de una aplicación de C++
En esta guía, usará las características de almacenamiento que se pueden ejecutar en una aplicación C++.

Para ello, deberá instalar la biblioteca de cliente de almacenamiento de Azure para C++ y crear una cuenta de almacenamiento de Azure en su suscripción de Azure.

Para instalar la biblioteca de cliente de almacenamiento de Azure para C++, puede usar los métodos siguientes:

-	**Linux:** siga las instrucciones indicadas en la página [Léame de la biblioteca de cliente de almacenamiento de Azure para C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md).  
-	**Windows:** en Visual Studio, haga clic en **Herramientas > Administrador de paquetes de NuGet > Consola del Administrador de paquetes**. Escriba el siguiente comando en la [Consola del Administrador de paquetes de NuGet](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) y presione **ENTRAR**.  

		Install-Package wastorage

## Configuración de su aplicación para obtener acceso al almacenamiento de blobs  
Agregue las siguientes instrucciones include en la parte superior del archivo C++ en el que desea usar las API de almacenamiento de Azure para obtener acceso a los blobs:

	#include "was/storage_account.h"
	#include "was/blob.h"

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

A continuación, obtenga una referencia a una clase **cloud\_blob\_client**, ya que le permite recuperar objetos que representan contenedores y blobs almacenados en el servicio de almacenamiento de blobs. El código siguiente crea un objeto **cloud\_blob\_client** usando el objeto de cuenta de almacenamiento recuperado anteriormente:

	// Create the blob client.
	azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();  

## Cómo crear un contenedor

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

En este ejemplo se muestra cómo crear un contenedor si todavía no existe:

	try
	{
		// Retrieve storage account from connection string.
		azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

		// Create the blob client.
		azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

		// Retrieve a reference to a container.
		azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

		// Create the container if it doesn't already exist.
		container.create_if_not_exists();
	}
	catch (const std::exception& e)
	{
		std::wcout << U("Error: ") << e.what() << std::endl;
	}  

De manera predeterminada, el nuevo contenedor es privado y debe especificar su clave de acceso de almacenamiento para descargar blobs de él. Si desea poner los archivos (blobs) del contenedor a disposición de todo el mundo, puede convertir el contenedor en público usando el código siguiente:

	// Make the blob container publicly accessible.
	azure::storage::blob_container_permissions permissions;
	permissions.set_public_access(azure::storage::blob_container_public_access_type::blob);
	container.upload_permissions(permissions);  

Cualquier usuario de Internet puede ver los blobs de los contenedores públicos, pero solo es posible modificarlos o eliminarlos si se dispone de la clave de acceso apropiada.

## Cómo cargar un blob en un contenedor
El almacenamiento de blobs de Azure admite blobs en bloques y en páginas. En la mayoría de los casos, se recomienda usar blobs en bloques.

Para cargar un archivo en un blob en bloques, obtenga una referencia de contenedor y úsela para obtener una referencia de blob en bloques. Una vez que disponga de la referencia de blob, puede cargar cualquier flujo de datos en ella llamando al método **upload\_from\_stream**. De este modo, se creará el blob si no existía anteriormente, o bien se sobrescribirá si ya existía. En el siguiente ejemplo se muestra cómo cargar un blob en un contenedor creado anteriormente.

	// Retrieve storage account from connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the blob client.
	azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

	// Retrieve a reference to a previously created container.
	azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

	// Retrieve reference to a blob named "my-blob-1".
	azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

	// Create or overwrite the "my-blob-1" blob with contents from a local file.
	concurrency::streams::istream input_stream = concurrency::streams::file_stream<uint8_t>::open_istream(U("DataFile.txt")).get();
	blockBlob.upload_from_stream(input_stream);
	input_stream.close().wait();

	// Create or overwrite the "my-blob-2" and "my-blob-3" blobs with contents from text.
	// Retrieve a reference to a blob named "my-blob-2".
	azure::storage::cloud_block_blob blob2 = container.get_block_blob_reference(U("my-blob-2"));
	blob2.upload_text(U("more text"));

	// Retrieve a reference to a blob named "my-blob-3".
	azure::storage::cloud_block_blob blob3 = container.get_block_blob_reference(U("my-directory/my-sub-directory/my-blob-3"));
	blob3.upload_text(U("other text"));  

Como alternativa, puede usar el método **upload\_from\_file** para cargar un archivo en un blob en bloques.

## Cómo enumerar los blobs en un contenedor
Para enumerar los blobs de un contenedor, primero obtenga una referencia de contenedor. A continuación, puede usar el método **list\_blobs** del contenedor para recuperar los blobs y los directorios que contiene. Para obtener acceso al conjunto completo de propiedades y métodos de un elemento **list\_blob\_item** devuelto, debe realizar una llamada al método **list\_blob\_item.as\_blob** para obtener un objeto **cloud\_blob** o al método **list\_blob.as\_directory** para obtener un objeto cloud\_blob\_directory. El código siguiente muestra cómo recuperar y consultar el URI de cada elemento del contenedor**my-sample-container**:

	// Retrieve storage account from connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the blob client.
	azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

	// Retrieve a reference to a previously created container.
	azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

	// Output URI of each item.
	azure::storage::list_blob_item_iterator end_of_results;
	for (auto it = container.list_blobs(); it != end_of_results; ++it)
	{
		if (it->is_blob())
		{
			std::wcout << U("Blob: ") << it->as_blob().uri().primary_uri().to_string() << std::endl;
		}
		else
		{
			std::wcout << U("Directory: ") << it->as_directory().uri().primary_uri().to_string() << std::endl;
		}
	}

Para obtener más información sobre cómo enumerar las operaciones, consulte [Enumeración de recursos de almacenamiento de Azure en C++](storage-c-plus-plus-enumeration.md).

## Cómo descargar blobs
Para descargar blobs, primero recupere una referencia de blob y, a continuación, llame al método **download\_to\_stream**. En el siguiente ejemplo, se usa el método **download\_to\_stream** para transferir el contenido del blob a un objeto de flujo que, a continuación, puede guardar en un archivo local.

	// Retrieve storage account from connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the blob client.
	azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

	// Retrieve a reference to a previously created container.
	azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

	// Retrieve reference to a blob named "my-blob-1".
	azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

	// Save blob contents to a file.
	concurrency::streams::container_buffer<std::vector<uint8_t>> buffer;
	concurrency::streams::ostream output_stream(buffer);
	blockBlob.download_to_stream(output_stream);

	std::ofstream outfile("DownloadBlobFile.txt", std::ofstream::binary);
	std::vector<unsigned char>& data = buffer.collection();

	outfile.write((char *)&data[0], buffer.size());
	outfile.close();  

Como alternativa, puede usar el método **download\_to\_file** para descargar el contenido de un blob en un archivo. También puede usar el método **download\_text** para descargar el contenido de un blob en forma de cadena de texto.

	// Retrieve storage account from connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the blob client.
	azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

	// Retrieve a reference to a previously created container.
	azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

	// Retrieve reference to a blob named "my-blob-2".
	azure::storage::cloud_block_blob text_blob = container.get_block_blob_reference(U("my-blob-2"));

	// Download the contents of a blog as a text string.
	utility::string_t text = text_blob.download_text();

## Cómo eliminar blobs
Para eliminar un blob, primero obtenga una referencia de blob y, a continuación, llame al método **delete\_blob** en ella.

	// Retrieve storage account from connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the blob client.
	azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

	// Retrieve a reference to a previously created container.
	azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

	// Retrieve reference to a blob named "my-blob-1".
	azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

	// Delete the blob.
	blockBlob.delete_blob();

## Pasos siguientes
Ahora que está familiarizado con los aspectos básicos del almacenamiento de blobs, siga estos vínculos para obtener más información sobre Almacenamiento de Azure.

-	[Cómo usar el almacenamiento de colas de C++](storage-c-plus-plus-how-to-use-queues.md)
-	[Cómo usar el almacenamiento de tablas de C++](storage-c-plus-plus-how-to-use-tables.md)
-	[Enumeración de los recursos de almacenamiento de Azure en C++](storage-c-plus-plus-enumeration.md)
-	[Referencia de la biblioteca de clientes de almacenamiento para C++](http://azure.github.io/azure-storage-cpp)
-	[Documentación de Almacenamiento de Azure](https://azure.microsoft.com/documentation/services/storage/)
- [Introducción a la utilidad de línea de comandos AzCopy](storage-use-azcopy.md)

<!---HONumber=AcomDC_0224_2016-->