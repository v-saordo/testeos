<properties
	pageTitle="Introducción al almacenamiento de blobs y los servicios conectados de Visual Studio (ASP.NET 5) | Microsoft Azure"
	description="Cómo empezar a usar el almacenamiento de blobs de Azure en un proyecto de Visual Studio ASP.NET 5 después de crear una cuenta de almacenamiento mediante los servicios conectados de Visual Studio"
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

# Introducción al almacenamiento de blobs de Azure y los servicios conectados de Visual Studio (ASP.NET 5)

##Información general

En este artículo se describe cómo empezar a usar el almacenamiento de blobs de Azure en Visual Studio después de haber creado o hecho referencia a una cuenta de almacenamiento de Azure en un proyecto de ASP.NET 5 mediante el cuadro de diálogo Agregar servicios conectados de Visual Studio.

El almacenamiento de blobs de Azure es un servicio para almacenar grandes cantidades de datos no estructurados a los que puede obtenerse acceso desde cualquier lugar del mundo a través de HTTP o HTTPS. Un solo blob puede tener cualquier tamaño. Los blobs pueden tener forma de imágenes, archivos de audio y vídeo, archivos sin procesar y archivos de documentos. En este artículo se describe cómo empezar a usar el almacenamiento de blobs de Azure después de crear una cuenta de almacenamiento de Azure con el cuadro de diálogo **Agregar servicios conectados** de Visual Studio en un proyecto de ASP.NET 5.

Al igual que los archivos residen en carpetas, los blobs de almacenamiento residen en contenedores. Después de haber creado un almacenamiento, puede crear uno o varios contenedores en el almacenamiento. Por ejemplo, en un almacenamiento llamado "Scrapbook" puede crear contenedores en el almacenamiento denominados "images" para almacenar imágenes y "audio" para almacenar archivos de audio. Una vez creados los contenedores, puede cargar archivos de blob individuales a ellos. Vea [Introducción al Almacenamiento de blobs de Azure mediante .NET](storage-dotnet-how-to-use-blobs.md) para más información sobre la manipulación de blobs mediante programación.

##Contenedores de blobs de acceso en el código

Para obtener acceso mediante programación a los blobs en los proyectos ASP.NET 5, deberá agregar los elementos siguientes, si no están presentes aún.

1. Agregue las siguientes declaraciones de espacio de nombres de código en la parte superior de todo archivo C# en el que desee obtener acceso al almacenamiento de Azure mediante programación.

		using Microsoft.Framework.Configuration;
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Blob;
		using System.Threading.Tasks;
		using LogLevel = Microsoft.Framework.Logging.LogLevel;

2. Obtenga un objeto **CloudStorageAccount** que represente la información de su cuenta de almacenamiento. Use el código siguiente para obtener la cadena de conexión de almacenamiento y la información de la cuenta de almacenamiento de la configuración del servicio de Azure.

		 CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		   CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

    **NOTA:** use todo el código anterior delante del código que aparece en las secciones siguientes.


3. Use un objeto **CloudBlobClient** para obtener una referencia **CloudBlobContainer** a un contenedor existente en su cuenta de almacenamiento.

		// Create a blob client.
		CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        // Get a reference to a container named “mycontainer.”
        CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");



##Crear un contenedor en código

También puede usar el **CloudBlobClient** para crear un contenedor en su cuenta de almacenamiento. Tan solo tiene que agregar una llamada a **CreateIfNotExistsAsync** como en el código siguiente:

	// Create a blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get a reference to a container named “my-new-container.”
    CloudBlobContainer container = blobClient.GetContainerReference("my-new-container");

    // If “mycontainer” doesn’t exist, create it.
    await container.CreateIfNotExistsAsync();


**NOTA:** las API que realizan llamadas al almacenamiento de Azure en ASP.NET 5 son asincrónicas. Vea [Programación asincrónica con Async y Await](http://msdn.microsoft.com/library/hh191443.aspx) para más información. El código siguiente supone que se están utilizando métodos de programación asincrónica.

Para poner los archivos del contenedor a disposición de todo el mundo, puede convertir el contenedor en público usando el código siguiente:

	await container.SetPermissionsAsync(new BlobContainerPermissions
    {
        PublicAccess = BlobContainerPublicAccessType.Blob
    });

##Cargar un blob en un contenedor

Para cargar un archivo de blob en un contenedor, obtenga una referencia de contenedor y utilícela para obtener una referencia de blob. Una vez que disponga de la referencia de blob, puede cargar cualquier secuencia de datos en ella llamando al método **UploadFromStreamAsync**. Esta operación crea el blob si no existe o lo sobrescribe si ya existe. En el siguiente ejemplo se muestra cómo cargar un blob en un contenedor creado anteriormente.

	// Get a reference to a blob named "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

    // Create or overwrite the "myblob" blob with the contents of a local file
    // named “myfile”.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        await blockBlob.UploadFromStreamAsync(fileStream);
    }

##Enumerar los blobs de un contenedor
Para enumerar los blobs de un contenedor, primero obtenga una referencia de contenedor. A continuación, llame al método **ListBlobsSegmentedAsync** del contenedor para recuperar los blobs o los directorios que contiene. Para obtener acceso a las numerosas propiedades y métodos de una lista **IListBlobItem** recuperada, debe convertir esta última en un objeto **CloudBlockBlob**, **CloudPageBlob**, o **CloudBlobDirectory**. Si no conoce el tipo de blob, puede utilizar una comprobación de tipo para determinar el formato al que debe convertirlo. El código siguiente demuestra cómo recuperar y consultar el URI de cada elemento del contenedor.

	BlobContinuationToken token = null;
    do
    {
        BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(token);
        token = resultSegment.ContinuationToken;

        foreach (IListBlobItem item in resultSegment.Results)
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
    } while (token != null);

Hay otras maneras de enumerar el contenido de un contenedor de blobs. Vea [Introducción al Almacenamiento de blobs de Azure mediante .NET](storage-dotnet-how-to-use-blobs.md#list-the-blobs-in-a-container) para obtener más información.

##Descarga de un blob
Para descargar un blob, primero obtenga una referencia al blob y luego llame al método **DownloadToStreamAsync**. En el siguiente ejemplo se usa el método **DownloadToStreamAsync** para transferir el contenido del blob a un objeto de secuencia que luego puede guardar como archivo local.

	// Get a reference to a blob named "photo1.jpg".
	CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

	// Save the blob contents to a file named “myfile”.
	using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
	{
    	await blockBlob.DownloadToStreamAsync(fileStream);
	}

Hay otras maneras de guardar blobs como archivos. Vea [Introducción al Almacenamiento de blobs de Azure mediante .NET](storage-dotnet-how-to-use-blobs.md#download-blobs) para obtener más información.

##Eliminar un blob
Para eliminar un blob, obtenga primero una referencia al blob y, a continuación, llame al método **DeleteAsync**.

	// Get a reference to a blob named "myblob.txt".
	CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

	// Delete the blob.
	await blockBlob.DeleteAsync();

## Pasos siguientes

[AZURE.INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]

<!---HONumber=AcomDC_0224_2016-->