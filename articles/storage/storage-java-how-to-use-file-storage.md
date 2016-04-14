<properties
	pageTitle="Uso del almacenamiento de archivos de Java | Microsoft Azure"
	description="Aprenda a utilizar el servicio de archivos de Azure para cargar, descargar, incluir en un listado y eliminar archivos. Ejemplos escritos en Java."
	services="storage"
	documentationCenter="java"
	authors="rmcmurray"
	manager="wpickett"
	editor="jimbe" />

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="Java"
	ms.topic="article"
	ms.date="02/29/2016"
	ms.author="robmcm"/>

# Uso del almacenamiento de archivos de Java

[AZURE.INCLUDE [storage-selector-file-include](../../includes/storage-selector-file-include.md)]

## Información general

En esta guía aprenderá cómo realizar operaciones básicas en el servicio de almacenamiento de archivos de Microsoft Azure. Mediante ejemplos escritos en Java aprenderá a crear recursos compartidos y directorios, cargar, enumerar y eliminar archivos. Si no está familiarizado con el servicio de almacenamiento de archivos de Microsoft Azure, le resultará muy útil repasar los conceptos de las secciones siguientes comprender los ejemplos.

[AZURE.INCLUDE [storage-file-concepts-include](../../includes/storage-file-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Creación de una aplicación Java

Para compilar las muestras, se necesitará el Kit de desarrollo de Java (JDK) y el [SDK de Almacenamiento de Azure para Java][]. También deberá haber creado una cuenta de almacenamiento de Azure.

## Configure la aplicación para usar el almacenamiento de archivos

Para utilizar las API de almacenamiento de Azure, agregue la siguiente instrucción al principio del archivo Java desde el que desea acceder al servicio de almacenamiento.

	// Include the following imports to use blob APIs.
    import com.microsoft.azure.storage.*;
	import com.microsoft.azure.storage.file.*;

## Configuración de una cadena de conexión de almacenamiento de Azure

Para usar el almacenamiento de archivos, necesita conectarse con la cuenta de almacenamiento de Azure. El primer paso sería configurar una cadena de conexión que se utilizará para establecer la conexión con su cuenta de almacenamiento. Definamos una variable estática para hacerlo.

	// Configure the connection-string with your values
	public static final String storageConnectionString =
	    "DefaultEndpointsProtocol=http;" +
	    "AccountName=your_storage_account_name;" +
	    "AccountKey=your_storage_account_key";

> [AZURE.NOTE] Reemplace your\_storage\_account\_name y your\_storage\_account\_key por los valores reales de la cuenta de almacenamiento.

## Conexión a una cuenta de almacenamiento de Azure

Para conectarse a su cuenta de almacenamiento, deberá usar el objeto **CloudStorageAccount**, pasando una cadena de conexión a su método de **análisis**.

	// Use the CloudStorageAccount object to connect to your storage account
	try {
		CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);
	} catch (InvalidKeyException invalidKey) {
		// Handle the exception
	}

**CloudStorageAccount.parse** produce una InvalidKeyException, por lo que deberá colocarla dentro de un bloque try/catch.

## Creación de una cola

Todos los archivos y directorios del almacenamiento de archivos residen en un contenedor denominado **Share**. La cuenta de almacenamiento puede tener tantos recursos compartidos como los que permita la capacidad de su cuenta. Para obtener acceso a un recurso compartido y su contenido, deberá usar a un cliente de almacenamiento de archivo.

	// Create the file storage client.
	CloudFileClient fileClient = storageAccount.createCloudFileClient();

Con el cliente de almacenamiento de archivos, puede obtener una referencia a un recurso compartido.

	// Get a reference to the file share
	CloudFileShare share = fileClient.getShareReference("sampleshare");

Para crear el recurso compartido, utilice el método **createIfNotExists** del objeto CloudFileShare.

	if (share.createIfNotExists()) {
		System.out.println("New share created");
	}

En este punto, **share** contiene una referencia a un recurso compartido denominado **sampleshare**.

## Carga de un archivo

Los recursos compartidos de almacenamiento de archivos de Azure contienen como mínimo un directorio raíz donde pueden residir los archivos. En esta sección, aprenderá cómo cargar un archivo del almacenamiento local en el directorio raíz de un recurso compartido.

El primer paso para cargar un archivo es obtener una referencia al directorio donde debe residir. Para ello, llame al método **getRootDirectoryReference** del objeto del recurso compartido.

	//Get a reference to the root directory for the share.
	CloudFileDirectory rootDir = share.getRootDirectoryReference();

Ahora que tiene una referencia al directorio raíz del recurso compartido, puede cargar un archivo en él utilizando el código siguiente.

	// Define the path to a local file.
	final String filePath = "C:\\temp\\Readme.txt";

	CloudFile cloudFile = rootDir.getFileReference("Readme.txt");
	cloudFile.uploadFromFile(filePath);

## Creación de directorios

También puede organizar el almacenamiento colocando archivos dentro de los subdirectorios en lugar de mantenerlos a todos en el directorio raíz. El servicio de almacenamiento de archivos de Azure permite crear tantos directorios como lo permita su cuenta. El código siguiente creará un subdirectorio denominado **sampledir** bajo el directorio raíz.

	//Get a reference to the root directory for the share.
	CloudFileDirectory rootDir = share.getRootDirectoryReference();

	//Get a reference to the sampledir directory
	CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");

	if (sampleDir.createIfNotExists()) {
		System.out.println("sampledir created");
	} else {
		System.out.println("sampledir already exists");
	}

## Enumeración de archivos y directorios en un recurso compartido

Obtener una lista de archivos y directorios dentro de un recurso compartido se realiza fácilmente mediante una llamada a **listFilesAndDirectories** en una referencia de CloudFileDirectory. El método devuelve una lista de objetos ListFileItem en los que puede efectuar la iteración. Por ejemplo, el siguiente código enumerará archivos y directorios dentro del directorio raíz.

	//Get a reference to the root directory for the share.
	CloudFileDirectory rootDir = share.getRootDirectoryReference();

	for ( ListFileItem fileItem : rootDir.listFilesAndDirectories() ) {
		System.out.println(fileItem.getUri());
	}


## Descarga de un archivo

Una de las operaciones más frecuentes que se llevará a cabo en el almacenamiento de archivos es descargar archivos. En el ejemplo siguiente, el código descarga el archivo SampleFile.txt y muestra su contenido.

	//Get a reference to the root directory for the share.
	CloudFileDirectory rootDir = share.getRootDirectoryReference();

	//Get a reference to the directory that contains the file
	CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");

	//Get a reference to the file you want to download
	CloudFile file = sampleDir.getFileReference("SampleFile.txt");

	//Write the contents of the file to the console.
	System.out.println(file.downloadText());

## Eliminación de un archivo

Otra operación de almacenamiento de archivo común es la eliminación de archivos. El código siguiente elimina un archivo denominado SampleFile.txt que se almacena en un directorio denominado **sampledir**.


	// Get a reference to the root directory for the share.
	CloudFileDirectory rootDir = share.getRootDirectoryReference();

	// Get a reference to the directory where the file to be deleted is in
	CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");

	String filename = "SampleFile.txt"
	CloudFile file;

	file = containerDir.getFileReference(filename)
	if ( file.deleteIfExists() ) {
		System.out.println(filename + " was deleted");
	}


## Eliminación de un directorio

Eliminar un directorio es una tarea bastante sencilla, aunque se debe tener en cuenta que no se puede eliminar un directorio que contenga archivos u otros directorios.

	// Get a reference to the root directory for the share.
	CloudFileDirectory rootDir = share.getRootDirectoryReference();

	// Get a reference to the directory you want to delete
	CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");

	// Delete the directory
	if ( containerDir.deleteIfExists() ) {
		System.out.println("Directory deleted");
	}


## Eliminación de un recurso compartido

Para eliminar un recurso compartido es necesario efectuar una llamada al método **deleteIfExists** en un objeto CloudFileShare. A continuación se facilita código de ejemplo que lo hace.

	try
	{
	    // Retrieve storage account from connection-string.
	    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

	    // Create the file client.
	   CloudFileClient fileClient = storageAccount.createCloudFileClient();

	   // Get a reference to the file share
	   CloudFileShare share = fileClient.getShareReference("sampleshare");

	   if (share.deleteIfExists()) {
		   System.out.println("sampleshare deleted");
	   }
	} catch (Exception e) {
		e.printStackTrace();
	}

## Pasos siguientes

Si desea obtener más información acerca de otras API de almacenamiento de Azure, siga estos vínculos.

- [Centro de desarrolladores de Java](http://azure.microsoft.com/develop/java/)
- [SDK de almacenamiento de Azure para Java](https://github.com/azure/azure-storage-java)
- [SDK de almacenamiento de Azure para Android](https://github.com/azure/azure-storage-android)
- [Referencia del SDK de cliente de almacenamiento de Azure](http://dl.windowsazure.com/storage/javadoc/)
- [API de REST de servicios de almacenamiento de Azure](https://msdn.microsoft.com/library/azure/dd179355.aspx)
- [Blog del equipo de almacenamiento de Azure](http://blogs.msdn.com/b/windowsazurestorage/)
- [Transferencia de datos con la utilidad en línea de comandos AzCopy](storage-use-azcopy.md)

<!---HONumber=AcomDC_0302_2016-->