<properties
	pageTitle="Introducción al Almacenamiento de blobs de Azure mediante .NET | Microsoft Azure"
	description="Almacene datos de archivo en la nube con Almacenamiento de blobs (objetos) de Azure. Introducción a operaciones simples de Almacenamiento de blobs, incluida la creación de un contenedor y la carga, descarga, enumeración y eliminación del contenido de blob."
	services="storage"
	documentationCenter=".net"
	authors="tamram"
	manager="carmonm"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="02/25/2016"
	ms.author="tamram"/>


# Introducción al Almacenamiento de blobs de Azure mediante .NET

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## Información general

Almacenamiento de blobs de Azure es un servicio que almacena datos de archivo en la nube. El Almacenamiento de blobs puede almacenar cualquier tipo de datos binarios o texto, como un documento, un archivo multimedia o un instalador de aplicación. El Almacenamiento de blobs a veces se conoce como "almacenamiento de objetos".

### Acerca de este tutorial

Este tutorial muestra cómo escribir código .NET para algunos escenarios comunes con el Almacenamiento de blobs de Azure. Entre los escenarios descritos se incluyen cargar, enumerar, descargar y eliminar blobs.

**Tiempo estimado para completar:** 45 minutos

**Requisitos previos:**

- [Microsoft Visual Studio](https://www.visualstudio.com/es-ES/visual-studio-homepage-vs.aspx)
- [Biblioteca de cliente de Almacenamiento de Azure para .NET](https://www.nuget.org/packages/WindowsAzure.Storage/)
- [Administrador de configuración Azure para .NET](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/)
- Una [cuenta de almacenamiento de Azure](storage-create-storage-account.md#create-a-storage-account).


[AZURE.INCLUDE [storage-dotnet-client-library-version-include](../../includes/storage-dotnet-client-library-version-include.md)]

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[AZURE.INCLUDE [storage-configure-connection-string-include](../../includes/storage-configure-connection-string-include.md)]

## Acceso al almacenamiento de blobs mediante programación

[AZURE.INCLUDE [storage-dotnet-obtain-assembly](../../includes/storage-dotnet-obtain-assembly.md)]

### Declaraciones de espacio de nombres

Agregue las siguientes declaraciones de espacio de nombres en la parte superior de todo archivo C# en el que desee obtener acceso al almacenamiento de Azure mediante programación:

    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Blob;

Asegúrese de hacer referencia al ensamblado `Microsoft.WindowsAzure.Storage.dll`.

[AZURE.INCLUDE [storage-dotnet-retrieve-conn-string](../../includes/storage-dotnet-retrieve-conn-string.md)]

El tipo **CloudBlobClient** permite recuperar objetos que representan contenedores y blobs almacenados en el servicio de almacenamiento de blobs. El código siguiente crea un objeto **CloudBlobClient** utilizando el objeto de cuenta de almacenamiento recuperado anteriormente:

    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

## Crear un contenedor

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

En este ejemplo se muestra cómo crear un contenedor si todavía no existe:

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve a reference to a container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Create the container if it doesn't already exist.
    container.CreateIfNotExists();

De manera predeterminada, el nuevo contenedor es privado y debe especificar su clave de acceso de almacenamiento para descargar blobs de él. Si desea poner los archivos del contenedor a disposición de todo el mundo, puede convertir el contenedor en público utilizando el código siguiente:

    container.SetPermissions(
        new BlobContainerPermissions { PublicAccess =
 	    BlobContainerPublicAccessType.Blob });

Cualquier usuario de Internet puede ver los blobs de los contenedores públicos, pero solo es posible modificarlos o eliminarlos si se dispone de la clave de acceso apropiada.

## Cargar un blob en un contenedor

El almacenamiento de blobs de Azure admite blobs en bloques y en páginas. En la mayoría de los casos, se recomienda usar blobs en bloques.

Para cargar un archivo en un blob en bloques, obtenga una referencia de contenedor y utilícela para obtener una referencia de blob en bloques. Una vez que disponga de la referencia de blob, puede cargar cualquier secuencia de datos en ella llamando al método **UploadFromStream**. De este modo, se creará el blob si no existía anteriormente, o bien se sobrescribirá si ya existía.

En el siguiente ejemplo se muestra cómo cargar un blob en un contenedor creado anteriormente.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

    // Create or overwrite the "myblob" blob with contents from a local file.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        blockBlob.UploadFromStream(fileStream);
    }

## Enumerar los blobs de un contenedor

Para enumerar los blobs de un contenedor, primero obtenga una referencia de contenedor. A continuación, puede utilizar el método **ListBlobs** del contenedor para recuperar los blobs y los directorios que contiene. Para obtener acceso a las numerosas propiedades y métodos de una lista **IListBlobItem** recuperada, debe convertir esta última en un objeto **CloudBlockBlob**, **CloudPageBlob**, o **CloudBlobDirectory**. Si se desconoce el tipo, puede realizar una comprobación de tipo para determinar el formato al que se debe convertir. El código siguiente muestra cómo recuperar y consultar el URI de cada elemento del contenedor `photos`:

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
	CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

	// Retrieve reference to a previously created container.
	CloudBlobContainer container = blobClient.GetContainerReference("photos");

	// Loop over items within the container and output the length and URI.
	foreach (IListBlobItem item in container.ListBlobs(null, false))
	{
		if (item.GetType() == typeof(CloudBlockBlob))
		{
			CloudBlockBlob blob = (CloudBlockBlob)item;

			Console.WriteLine("Block blob of length {0}: {1}", blob.Properties.Length, blob.Uri);

		}
		else if (item.GetType() == typeof(CloudPageBlob))
		{
			CloudPageBlob pageBlob = (CloudPageBlob)item;

			Console.WriteLine("Page blob of length {0}: {1}", pageBlob.Properties.Length, pageBlob.Uri);

		}
		else if (item.GetType() == typeof(CloudBlobDirectory))
		{
			CloudBlobDirectory directory = (CloudBlobDirectory)item;

			Console.WriteLine("Directory: {0}", directory.Uri);
		}
	}

Como se mostró anteriormente, puede asignar nombre a los blobs incluyendo información de ruta de acceso. De este modo crea una estructura de directorios virtuales que puede organizar y recorrer tal como lo haría en un sistema de archivos tradicional. Tenga en cuenta que la estructura de directorios es solo virtual: los únicos recursos disponibles en el almacenamiento de blobs son contenedores y blobs. Sin embargo, la biblioteca de clientes de almacenamiento ofrece un objeto **CloudBlobDirectory** para hacer referencia a un directorio virtual y simplificar el proceso de trabajo con blobs organizados de este modo.

Por ejemplo, observe el siguiente conjunto de blobs en bloques incluidos en un contenedor denominado `photos`:

	photo1.jpg
	2010/architecture/description.txt
	2010/architecture/photo3.jpg
	2010/architecture/photo4.jpg
	2011/architecture/photo5.jpg
	2011/architecture/photo6.jpg
	2011/architecture/description.txt
	2011/photo7.jpg

Al llamar a **ListBlobs** en el contenedor "photos" (como en el ejemplo anterior), se devuelve una lista jerárquica. Contiene objetos **CloudBlobDirectory** y **CloudBlockBlob** que representan, respectivamente, los directorios y los blobs del contenedor. El resultado tiene el siguiente aspecto:

	Directory: https://<accountname>.blob.core.windows.net/photos/2010/
	Directory: https://<accountname>.blob.core.windows.net/photos/2011/
	Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


También existe la opción de establecer el parámetro **UseFlatBlobListing** del método **ListBlobs** en **true**. En este caso, los blobs del contenedor se devuelven como objetos **CloudBlockBlob**. La llamada a **ListBlobs** para devolver una lista plana tiene el siguiente aspecto:

    // Loop over items within the container and output the length and URI.
	foreach (IListBlobItem item in container.ListBlobs(null, true))
	{
	   ...
	}

y el resultado tiene este aspecto:

	Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
	Block blob of length 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
	Block blob of length 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
	Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
	Block blob of length 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
	Block blob of length 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
	Block blob of length 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
	Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


## Descargar blobs

Para descargar blobs, primero recupere una referencia de blob y, a continuación, llame al método **DownloadToStream**. En el siguiente ejemplo, se usa el método **DownloadToStream** para transferir el contenido del blob a un objeto de secuencia que, a continuación, puede guardar en un archivo local.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "photo1.jpg".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

    // Save blob contents to a file.
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        blockBlob.DownloadToStream(fileStream);
    }

También puede usar el método **DownloadToStream** para descargar el contenido de un blob en forma de cadena de texto.

	// Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

	// Retrieve reference to a blob named "myblob.txt"
	CloudBlockBlob blockBlob2 = container.GetBlockBlobReference("myblob.txt");

	string text;
	using (var memoryStream = new MemoryStream())
	{
		blockBlob2.DownloadToStream(memoryStream);
		text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
	}

## Eliminar blobs

Para eliminar un blob, primero obtenga una referencia de blob y, a continuación, llame al método **Delete** en ella.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "myblob.txt".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

    // Delete the blob.
    blockBlob.Delete();


## Enumerar blobs en páginas de forma asincrónica

Si enumera un gran número de blobs o desea controlar el número de resultados que devuelve en una operación de listado, puede enumerar blobs en páginas de resultados. En este ejemplo se muestra cómo devolver resultados en páginas asincrónicamente de forma que la ejecución no se bloquee mientras se espera a devolver un conjunto grande de resultados.

En este ejemplo se muestra un listado de blobs plano, pero también puede realizar un listado jerárquico estableciendo el parámetro `useFlatBlobListing` del método **ListBlobsSegmentedAsync** en `false`.

Dado que el método de ejemplo llama a un método asincrónico, debe ir precedido por la palabra clave `async` y debe devolver un objeto **Task**. La palabra clave await especificada para el método **ListBlobsSegmentedAsync** suspende la ejecución del método de ejemplo hasta que la tarea de enumeración se completa.

    async public static Task ListBlobsSegmentedInFlatListing(CloudBlobContainer container)
    {
        //List blobs to the console window, with paging.
        Console.WriteLine("List blobs in pages:");

        int i = 0;
        BlobContinuationToken continuationToken = null;
        BlobResultSegment resultSegment = null;

        //Call ListBlobsSegmentedAsync and enumerate the result segment returned, while the continuation token is non-null.
        //When the continuation token is null, the last page has been returned and execution can exit the loop.
        do
        {
            //This overload allows control of the page size. You can return all remaining results by passing null for the maxResults parameter,
            //or by calling a different overload.
            resultSegment = await container.ListBlobsSegmentedAsync("", true, BlobListingDetails.All, 10, continuationToken, null, null);
            if (resultSegment.Results.Count<IListBlobItem>() > 0) { Console.WriteLine("Page {0}:", ++i); }
            foreach (var blobItem in resultSegment.Results)
            {
                Console.WriteLine("\t{0}", blobItem.StorageUri.PrimaryUri);
            }
            Console.WriteLine();

            //Get the continuation token.
            continuationToken = resultSegment.ContinuationToken;
        }
        while (continuationToken != null);
    }

## Escritura en un blob en anexos

Un blob en anexos es un nuevo tipo de blob, que se introdujo con la versión 5.x de la biblioteca de cliente de almacenamiento de Azure para. NET. Un blob en anexos se optimiza para las operaciones de anexado, como el registro. Como un blob en bloques, un blob en anexos se compone también de bloques, pero en el caso del blob en anexos cuando se agrega un nuevo bloque, siempre se anexa al final del blob. No se puede actualizar o eliminar un bloque existente en un blob en anexos. Los identificadores de bloque para un blob en anexos no está expuestos como lo están en el caso de los blobs en bloques.

Cada bloque en un blob en anexos puede tener un tamaño diferente, hasta un máximo de 4 MB y el blob puede incluir un máximo de 50.000 bloques. El tamaño máximo de un blob en anexos es, por tanto, ligeramente superior a 195 GB (4 MB X 50.000 bloques).

El ejemplo siguiente crea un nuevo blob en anexos y le anexa algunos datos, para simular una operación de registro simple.

    //Parse the connection string for the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

    //Create service client for credentialed access to the Blob service.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    //Get a reference to a container.
    CloudBlobContainer container = blobClient.GetContainerReference("my-append-blobs");

    //Create the container if it does not already exist.
    container.CreateIfNotExists();

    //Get a reference to an append blob.
    CloudAppendBlob appendBlob = container.GetAppendBlobReference("append-blob.log");

    //Create the append blob. Note that if the blob already exists, the CreateOrReplace() method will overwrite it.
    //You can check whether the blob exists to avoid overwriting it by using CloudAppendBlob.Exists().
    appendBlob.CreateOrReplace();

    int numBlocks = 10;

    //Generate an array of random bytes.
    Random rnd = new Random();
    byte[] bytes = new byte[numBlocks];
    rnd.NextBytes(bytes);

    //Simulate a logging operation by writing text data and byte data to the end of the append blob.
    for (int i = 0; i < numBlocks; i++)
    {
        appendBlob.AppendText(String.Format("Timestamp: {0:u} \tLog Entry: {1}{2}",
            DateTime.UtcNow, bytes[i], Environment.NewLine));
    }

    //Read the append blob to the console window.
    Console.WriteLine(appendBlob.DownloadText());

Consulte [Descripción Blobs en bloques, en anexos y en páginas](https://msdn.microsoft.com/library/azure/ee691964.aspx) para obtener más información acerca de las diferencias entre los tres tipos de blobs.

## Pasos siguientes

Ahora que está familiarizado con los aspectos básicos del Almacenamiento de blobs, siga estos vínculos para obtener más información.

### Documentación de referencia del Almacenamiento de blobs

- [Referencia de la biblioteca de clientes de almacenamiento para .NET](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)
- [Referencia de API de REST](http://msdn.microsoft.com/library/azure/dd179355)

### Guías de características adicionales

- [Introducción al Almacenamiento de tablas para .NET](storage-dotnet-how-to-use-tables.md)
- [Introducción al Almacenamiento en cola para .NET](storage-dotnet-how-to-use-queues.md)
- [Introducción al Almacenamiento de archivos para .NET](storage-dotnet-how-to-use-files.md)
- [Introducción a la utilidad de línea de comandos AzCopy](storage-use-azcopy)
- [Uso del almacenamiento de blobs de Azure con el SDK de WebJobs](../app-service-web/websites-dotnet-webjobs-sdk-storage-blobs-how-to.md)
- [Uso de bases de datos SQL de .NET (C#)](../sql-database/sql-database-dotnet-how-to-use.md)

  [Blob5]: ./media/storage-dotnet-how-to-use-blobs/blob5.png
  [Blob6]: ./media/storage-dotnet-how-to-use-blobs/blob6.png
  [Blob7]: ./media/storage-dotnet-how-to-use-blobs/blob7.png
  [Blob8]: ./media/storage-dotnet-how-to-use-blobs/blob8.png
  [Blob9]: ./media/storage-dotnet-how-to-use-blobs/blob9.png

  [Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/
  [Configuring Connection Strings]: http://msdn.microsoft.com/library/azure/ee758697.aspx
  [.NET client library reference]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [REST API reference]: http://msdn.microsoft.com/library/azure/dd179355

<!---HONumber=AcomDC_0309_2016-->