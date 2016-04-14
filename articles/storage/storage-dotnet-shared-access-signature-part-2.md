<properties
	pageTitle="Crear y usar una SAS con Almacenamiento de blobs | Microsoft Azure"
	description="En este tutorial se muestra cómo crear firmas de acceso compartido para su uso con Almacenamiento de blobs y cómo usarlas desde las aplicaciones cliente."
	services="storage"
	documentationCenter=""
	authors="tamram"
	manager="carmonm"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="02/14/2016"
	ms.author="tamram"/>


# Firmas de acceso compartido, Parte 2: Creación y uso de una SAS con Almacenamiento de blobs

## Información general

En la [Parte 1](storage-dotnet-shared-access-signature-part-1.md) de este tutorial se analizaron las firmas de acceso compartido (SAS) y se explicaron los procedimientos recomendados para su uso. En la parte 2 se muestra cómo generar este tipo de firmas para luego usarlas con Almacenamiento de blobs. Los ejemplos están escritos en C# y usan la biblioteca del cliente de almacenamiento de Azure para .NET. Entre los escenarios descritos se incluyen los siguientes aspectos del uso de firmas de acceso compartido:

- Generación de una firma de acceso compartido en un contenedor
- Generación de una firma de acceso compartido en un blob
- Creación de una directiva de acceso almacenada para administrar las firmas en los recursos de un contenedor
- Comprobación de las firmas de acceso compartido de una aplicación cliente

## Acerca de este tutorial

En este tutorial, nos centraremos en la creación de firmas de acceso compartido para contenedores y blobs mediante la creación de dos aplicaciones de consola. La primera aplicación de consola genera firmas de acceso compartido en un contenedor y en un blob. Dicha aplicación conoce las claves de la cuenta de almacenamiento. La segunda aplicación de consola, que actuará como aplicación cliente, obtiene acceso a los recursos del contenedor y del blob mediante las firmas de acceso compartido creadas con la primera aplicación. Esta aplicación solo usa las firmas mencionadas para autenticar su acceso a los recursos del contenedor y del blob, pero no conoce las claves de la cuenta.

## Parte 1: Creación de una aplicación de consola para generar firmas de acceso compartido

En primer lugar, asegúrese de tener instalada la biblioteca del cliente de almacenamiento de Azure para .NET. Puede instalar el paquete de [NuGet](http://nuget.org/packages/WindowsAzure.Storage/ "Paquete de NuGet") que contiene los ensamblados más recientes para la biblioteca del cliente. Este es el método recomendado para asegurarse de que cuenta con las últimas revisiones. También puede descargar dicha biblioteca como parte de la última versión del [SDK de Azure para .NET](https://azure.microsoft.com/downloads/).

En Visual Studio, cree una aplicación de consola de Windows y denomínela **GenerateSharedAccessSignatures**. Agregue referencias a los archivos **Microsoft.WindowsAzure.Configuration.dll** y **Microsoft.WindowsAzure.Storage.dll** de una de las siguientes formas:

- 	Si desea instalar el paquete de NuGet, instale en primer lugar la extensión [NuGet Package Manager Extension for Visual Studio](http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) (Administrador de paquetes NuGet para Visual Studio). En Visual Studio, seleccione **Proyecto | Administrar paquetes de NuGet**, busque en línea **Almacenamiento de Azure** y siga las instrucciones de instalación.
- 	También puede buscar los ensamblados en la instalación del SDK de Azure y agregar referencias a estos.

En la parte superior del archivo Program.cs, agregue las siguientes instrucciones **using**:

    using System.IO;    
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;

Edite el archivo app.config para que contenga un valor de configuración con una cadena de conexión que apunte a la cuenta de almacenamiento. El archivo app.config deberá tener un aspecto similar a este:

    <configuration>
      <startup>
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
      </startup>
      <appSettings>
        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey"/>
      </appSettings>
    </configuration>

### Generación de un URI de firma de acceso compartido para un contenedor

Para comenzar, se agregará un método para generar una firma de acceso compartido en un nuevo contenedor. En este caso, la firma no está asociada a una directiva de acceso almacenada, por lo que incluye en el URI la información que indica su tiempo de expiración y los permisos que concede.

En primer lugar, agregue código al método **Main()** para autenticar el acceso a la cuenta de almacenamiento y crear un contenedor:

    static void Main(string[] args)
    {
	    //Parse the connection string and return a reference to the storage account.
	    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));

	    //Create the blob client object.
	    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

	    //Get a reference to a container to use for the sample code, and create it if it does not exist.
	    CloudBlobContainer container = blobClient.GetContainerReference("sascontainer");
	    container.CreateIfNotExists();

	    //Insert calls to the methods created below here...

	    //Require user input before closing the console window.
	    Console.ReadLine();
    }

A continuación, agregue un nuevo método que genere la firma de acceso compartido para el contenedor y que devuelva el URI de la firma:

    static string GetContainerSasUri(CloudBlobContainer container)
    {
	    //Set the expiry time and permissions for the container.
	    //In this case no start time is specified, so the shared access signature becomes valid immediately.
	    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
	    sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
	    sasConstraints.Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List;

	    //Generate the shared access signature on the container, setting the constraints directly on the signature.
	    string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);

	    //Return the URI string for the container, including the SAS token.
	    return container.Uri + sasContainerToken;
    }

Agregue las líneas siguientes en la parte inferior del método **Main()**, antes de la llamada a **Console.ReadLine()**, para llamar a **GetContainerSasUri()** y escribir el URI de la firma en la ventana de la consola:

    //Generate a SAS URI for the container, without a stored access policy.
    Console.WriteLine("Container SAS URI: " + GetContainerSasUri(container));
    Console.WriteLine();

Compile y ejecute para generar el URI de la firma de acceso compartido para el nuevo contenedor, que será similar al siguiente:

	https://storageaccount.blob.core.windows.net/sascontainer?sv=2012-02-12&se=2013-04-13T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3D

Una vez que se ejecute el código, la firma de acceso compartido creada en el contenedor será válida durante las veinticuatro horas siguientes. Dicha firma concede permiso al cliente para enumerar los blobs en el contenedor y escribir un nuevo blob en este.

### Generación de un URI de firma de acceso compartido para un blob

A continuación, vamos a escribir código similar al anterior para crear un nuevo blob en el contenedor y generar una firma de acceso compartido para este. Dicha firma no está asociada a una directiva de acceso almacenada, por lo que incluye la hora de inicio, el tiempo de expiración y la información de permisos en el URI.

Agregue un nuevo método para crear un blob y escriba texto en este. A continuación, se genera una firma de acceso compartido y se devuelve el URI de la firma:

    static string GetBlobSasUri(CloudBlobContainer container)
    {
	    //Get a reference to a blob within the container.
	    CloudBlockBlob blob = container.GetBlockBlobReference("sasblob.txt");

	    //Upload text to the blob. If the blob does not yet exist, it will be created.
	    //If the blob does exist, its existing content will be overwritten.
	    string blobContent = "This blob will be accessible to clients via a Shared Access Signature.";
	    MemoryStream ms = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
	    ms.Position = 0;
	    using (ms)
	    {
		    blob.UploadFromStream(ms);
	    }

	    //Set the expiry time and permissions for the blob.
	    //In this case the start time is specified as a few minutes in the past, to mitigate clock skew.
	    //The shared access signature will be valid immediately.
	    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
	    sasConstraints.SharedAccessStartTime = DateTime.UtcNow.AddMinutes(-5);
	    sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
	    sasConstraints.Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write;

	    //Generate the shared access signature on the blob, setting the constraints directly on the signature.
	    string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

	    //Return the URI string for the container, including the SAS token.
	    return blob.Uri + sasBlobToken;
    }

En la parte inferior del método **Main()**, agregue las líneas siguientes para llamar a **GetBlobSasUri()**, antes de la llamada a **Console.ReadLine()**, y escriba el URI de la firma de acceso compartido en la ventana de la consola:

    //Generate a SAS URI for a blob within the container, without a stored access policy.
    Console.WriteLine("Blob SAS URI: " + GetBlobSasUri(container));
    Console.WriteLine();


Compile y ejecute para generar el URI de la firma de acceso compartido para el nuevo blob, que será similar al siguiente:

	https://storageaccount.blob.core.windows.net/sascontainer/sasblob.txt?sv=2012-02-12&st=2013-04-12T23%3A37%3A08Z&se=2013-04-13T00%3A12%3A08Z&sr=b&sp=rw&sig=dF2064yHtc8RusQLvkQFPItYdeOz3zR8zHsDMBi4S30%3D

### Creación de una directiva de acceso almacenada en el contenedor

Ahora vamos a crear una directiva de acceso almacenada en el contenedor que definirá las restricciones de las firmas de acceso compartido asociadas.

En los ejemplos anteriores, hemos especificado la hora de inicio (de forma implícita o explícita), el tiempo de expiración y los permisos del URI de la firma de acceso compartido. En los siguientes, especificaremos estos datos en la directiva de acceso almacenada y no en la firma. Al hacerlo, podemos cambiar estas restricciones sin necesidad de volver a emitir la firma de acceso compartido.

Es posible tener una o varias restricciones en la firma de acceso compartido y, el resto, en la directiva de acceso almacenada. Sin embargo, la hora de inicio, el tiempo de expiración y los permisos solo pueden especificarse en una de ambas ubicaciones; por ejemplo, no se pueden especificar permisos en la firma de acceso compartido y también en la directiva de acceso almacenada.

Tenga en cuenta que al agregar una directiva de acceso a un contenedor, debe obtener los permisos existentes del contenedor, agregar la nueva directiva de acceso y, a continuación, establecer los permisos del contenedor.

Agregue un nuevo método que cree una directiva de acceso almacenada en un contenedor y devuelva el nombre de esta:

    static void CreateSharedAccessPolicy(CloudBlobClient blobClient, CloudBlobContainer container,
		string policyName)
    {
        //Create a new shared access policy and define its constraints.
        SharedAccessBlobPolicy sharedPolicy = new SharedAccessBlobPolicy()
        {
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List | SharedAccessBlobPermissions.Read
        };

        //Get the container's existing permissions.
        BlobContainerPermissions permissions = container.GetPermissions();

        //Add the new policy to the container's permissions, and set the container's permissions.
        permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
        container.SetPermissions(permissions);
    }

En la parte inferior del método **Main()**, antes de la llamada a **Console.ReadLine()**, agregue las siguientes líneas para borrar primero cualquier directiva de acceso existente y llame luego al método **CreateSharedAccessPolicy()**:

    //Clear any existing access policies on container.
    BlobContainerPermissions perms = container.GetPermissions();
    perms.SharedAccessPolicies.Clear();
    container.SetPermissions(perms);

    //Create a new access policy on the container, which may be optionally used to provide constraints for
    //shared access signatures on the container and the blob.
    string sharedAccessPolicyName = "tutorialpolicy";
    CreateSharedAccessPolicy(blobClient, container, sharedAccessPolicyName);

Tenga en cuenta que cuando borra las directivas de acceso en un contenedor, deberá primero obtener los permisos existentes del contenedor, borrar luego los permisos y establecerlos de nuevo.

### Generación de un URI de firma de acceso compartido en el contenedor que usa una directiva de acceso

A continuación, vamos a crear otra firma de acceso compartido en el contenedor generado anteriormente, si bien esta vez la asociaremos a la directiva de acceso creada en el ejemplo anterior.

Agregue un nuevo método para generar otra firma de acceso compartido en el contenedor:

    static string GetContainerSasUriWithPolicy(CloudBlobContainer container, string policyName)
    {
	    //Generate the shared access signature on the container. In this case, all of the constraints for the
	    //shared access signature are specified on the stored access policy.
	    string sasContainerToken = container.GetSharedAccessSignature(null, policyName);

	    //Return the URI string for the container, including the SAS token.
	    return container.Uri + sasContainerToken;
    }

En la parte inferior del método **Main()**, antes de la llamada a **Console.ReadLine()**, agregue las siguientes líneas para llamar al método **GetContainerSasUriWithPolicy**:

    //Generate a SAS URI for the container, using a stored access policy to set constraints on the SAS.
    Console.WriteLine("Container SAS URI using stored access policy: " + GetContainerSasUriWithPolicy(container, sharedAccessPolicyName));
    Console.WriteLine();

### Generación de un URI de firma de acceso compartido en el blob que usa una directiva de acceso

Por último, agregaremos un método similar para crear otro blob y generar una firma de acceso compartido que se asocia a una directiva de acceso.

Agregue un nuevo método para crear un blob y generar una firma de acceso compartido:

    static string GetBlobSasUriWithPolicy(CloudBlobContainer container, string policyName)
    {
	    //Get a reference to a blob within the container.
	    CloudBlockBlob blob = container.GetBlockBlobReference("sasblobpolicy.txt");

	    //Upload text to the blob. If the blob does not yet exist, it will be created.
	    //If the blob does exist, its existing content will be overwritten.
	    string blobContent = "This blob will be accessible to clients via a shared access signature. " +
	    "A stored access policy defines the constraints for the signature.";
	    MemoryStream ms = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
	    ms.Position = 0;
	    using (ms)
	    {
		    blob.UploadFromStream(ms);
	    }

	    //Generate the shared access signature on the blob.
	    string sasBlobToken = blob.GetSharedAccessSignature(null, policyName);

	    //Return the URI string for the container, including the SAS token.
	    return blob.Uri + sasBlobToken;
    }

En la parte inferior del método **Main()**, antes de la llamada a **Console.ReadLine()**, agregue las siguientes líneas para llamar al método **GetBlobSasUriWithPolicy**:

    //Generate a SAS URI for a blob within the container, using a stored access policy to set constraints on the SAS.
    Console.WriteLine("Blob SAS URI using stored access policy: " + GetBlobSasUriWithPolicy(container, sharedAccessPolicyName));
    Console.WriteLine();

Ahora, el método **Main()** debe tener este aspecto en su conjunto. Ejecútelo para escribir los URI de firma de acceso compartido en la ventana de la consola y, a continuación, cópielos y péguelos en un archivo de texto para usarlos en la segunda parte de este tutorial.

    static void Main(string[] args)
    {
	    //Parse the connection string and return a reference to the storage account.
	    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));

	    //Create the blob client object.
	    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

	    //Get a reference to a container to use for the sample code, and create it if it does not exist.
	    CloudBlobContainer container = blobClient.GetContainerReference("sascontainer");
	    container.CreateIfNotExists();

	    //Generate a SAS URI for the container, without a stored access policy.
	    Console.WriteLine("Container SAS URI: " + GetContainerSasUri(container));
	    Console.WriteLine();

	    //Generate a SAS URI for a blob within the container, without a stored access policy.
	    Console.WriteLine("Blob SAS URI: " + GetBlobSasUri(container));
	    Console.WriteLine();

        //Clear any existing access policies on container.
        BlobContainerPermissions perms = container.GetPermissions();
        perms.SharedAccessPolicies.Clear();
        container.SetPermissions(perms);

        //Create a new access policy on the container, which may be optionally used to provide constraints for
        //shared access signatures on the container and the blob.
	    string sharedAccessPolicyName = "tutorialpolicy";
	    CreateSharedAccessPolicy(blobClient, container, sharedAccessPolicyName);

	    //Generate a SAS URI for the container, using a stored access policy to set constraints on the SAS.
	    Console.WriteLine("Container SAS URI using stored access policy: " + GetContainerSasUriWithPolicy(container, sharedAccessPolicyName));
	    Console.WriteLine();

	    //Generate a SAS URI for a blob within the container, using a stored access policy to set constraints on the SAS.
	    Console.WriteLine("Blob SAS URI using stored access policy: " + GetBlobSasUriWithPolicy(container, sharedAccessPolicyName));
	    Console.WriteLine();

	    Console.ReadLine();
    }

Al ejecutar la aplicación de consola GenerateSharedAccessSignatures, el resultado que se mostrará en la ventana de la consola será similar al siguiente. Estas son las firmas de acceso compartido que se usarán en la segunda parte de este tutorial.

![sas-console-output-1][sas-console-output-1]

## Parte 2: Creación de una aplicación de consola para probar las firmas de acceso compartido

Para probar las firmas de acceso compartido creadas en los ejemplos anteriores, crearemos una segunda aplicación de consola que use dichas firmas para realizar operaciones en el contenedor y en un blob.

> [AZURE.NOTE] Si han transcurrido más de 24 horas desde que se completó la primera parte del tutorial, las firmas generadas ya no serán válidas. En este caso, deberá ejecutar el código de la primera aplicación de consola a fin de generar nuevas firmas de acceso compartido y usarlas en la segunda parte del tutorial.

En Visual Studio, cree una nueva aplicación de consola de Windows y denomínela **ConsumeSharedAccessSignatures**. Agregue referencias a **Microsoft.WindowsAzure.Configuration.dll** y **Microsoft.WindowsAzure.Storage.dll**, como ya ha hecho anteriormente.

En la parte superior del archivo Program.cs, agregue las siguientes instrucciones **using**:

    using System.IO;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;

Agregue las siguientes constantes al cuerpo del método **Main()** y actualice sus valores a las firmas de acceso compartido generadas en la parte 1 del tutorial.

    static void Main(string[] args)
    {
	    string containerSAS = "<your container SAS>";
	    string blobSAS = "<your blob SAS>";
	    string containerSASWithAccessPolicy = "<your container SAS with access policy>";
	    string blobSASWithAccessPolicy = "<your blob SAS with access policy>";
    }

### Incorporación de un método para probar las operaciones del contenedor con una firma de acceso compartido

A continuación, vamos a agregar un método para probar algunas operaciones representativas del contenedor con una firma de acceso compartido en este último. Tenga en cuenta que dicha firma se usa para devolver una referencia al contenedor y el acceso a este se autentica en función de la firma sola.

Agregue el método siguiente a Program.cs:

    static void UseContainerSAS(string sas)
    {
        //Try performing container operations with the SAS provided.

        //Return a reference to the container using the SAS URI.
        CloudBlobContainer container = new CloudBlobContainer(new Uri(sas));

        //Create a list to store blob URIs returned by a listing operation on the container.
        List<ICloudBlob> blobList = new List<ICloudBlob>();

        //Write operation: write a new blob to the container.
        try
        {
            CloudBlockBlob blob = container.GetBlockBlobReference("blobCreatedViaSAS.txt");
            string blobContent = "This blob was created with a shared access signature granting write permissions to the container. ";
            MemoryStream msWrite = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
            msWrite.Position = 0;
            using (msWrite)
            {
                blob.UploadFromStream(msWrite);
            }
            Console.WriteLine("Write operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Write operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }

        //List operation: List the blobs in the container.
        try
        {
            foreach (ICloudBlob blob in container.ListBlobs())
            {
                blobList.Add(blob);
            }
            Console.WriteLine("List operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("List operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }

        //Read operation: Get a reference to one of the blobs in the container and read it.
        try
        {
            CloudBlockBlob blob = container.GetBlockBlobReference(blobList[0].Name);
            MemoryStream msRead = new MemoryStream();
            msRead.Position = 0;
            using (msRead)
            {
                blob.DownloadToStream(msRead);
                Console.WriteLine(msRead.Length);
            }
            Console.WriteLine("Read operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Read operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }
        Console.WriteLine();

        //Delete operation: Delete a blob in the container.
        try
        {
            CloudBlockBlob blob = container.GetBlockBlobReference(blobList[0].Name);
            blob.Delete();
            Console.WriteLine("Delete operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Delete operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }        
    }


Actualice el método **Main()** para que llame a **UseContainerSAS()** con las dos firmas de acceso compartido creadas en el contenedor:

	static void Main(string[] args)
	{
	    string containerSAS = "<your container SAS>";
	    string blobSAS = "<your blob SAS>";
	    string containerSASWithAccessPolicy = "<your container SAS with access policy>";
	    string blobSASWithAccessPolicy = "<your blob SAS with access policy>";

	    //Call the test methods with the shared access signatures created on the container, with and without the access policy.
	    UseContainerSAS(containerSAS);
	    UseContainerSAS(containerSASWithAccessPolicy);

	    Console.ReadLine();
	}


### Incorporación de un método para probar operaciones de blob con una firma de acceso compartido

Por último, vamos a agregar un método para probar algunas operaciones representativas del blob usando una firma de acceso compartido en este último. En este caso se usa el constructor **CloudBlockBlob(String)** y se pasa la firma de acceso compartido para devolver una referencia al blob. No se requiere ningún otro tipo de autenticación, esta se basa exclusivamente en la firma.

Agregue el método siguiente a Program.cs:

    static void UseBlobSAS(string sas)
    {
        //Try performing blob operations using the SAS provided.

        //Return a reference to the blob using the SAS URI.
        CloudBlockBlob blob = new CloudBlockBlob(new Uri(sas));

        //Write operation: Write a new blob to the container.
        try
        {
            string blobContent = "This blob was created with a shared access signature granting write permissions to the blob. ";
            MemoryStream msWrite = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
            msWrite.Position = 0;
            using (msWrite)
            {
                blob.UploadFromStream(msWrite);
            }
            Console.WriteLine("Write operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Write operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }

        //Read operation: Read the contents of the blob.
        try
        {
            MemoryStream msRead = new MemoryStream();
            using (msRead)
            {
                blob.DownloadToStream(msRead);
                msRead.Position = 0;
                using (StreamReader reader = new StreamReader(msRead, true))
                {
                    string line;
                    while ((line = reader.ReadLine()) != null)
                    {
                        Console.WriteLine(line);
                    }
                }
            }
            Console.WriteLine("Read operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Read operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }

        //Delete operation: Delete the blob.
        try
        {
            blob.Delete();
            Console.WriteLine("Delete operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Delete operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }        
    }


Actualice el método **Main()** para que llame a **UseBlobSAS(**) con las dos firmas de acceso compartido creadas en el blob:

	static void Main(string[] args)
	{
	    string containerSAS = "<your container SAS>";
	    string blobSAS = "<your blob SAS>";
	    string containerSASWithAccessPolicy = "<your container SAS with access policy>";
	    string blobSASWithAccessPolicy = "<your blob SAS with access policy>";

	    //Call the test methods with the shared access signatures created on the container, with and without the access policy.
	    UseContainerSAS(containerSAS);
	    UseContainerSAS(containerSASWithAccessPolicy);

	    //Call the test methods with the shared access signatures created on the blob, with and without the access policy.
	    UseBlobSAS(blobSAS);
	    UseBlobSAS(blobSASWithAccessPolicy);

	    Console.ReadLine();
	}

Ejecute la aplicación de consola y observe el resultado para ver qué operaciones se permiten para cada firma. El resultado de la ventana de la consola será similar al siguiente:

![sas-console-output-2][sas-console-output-2]

## Pasos siguientes

[Firmas de acceso compartido, Parte 1: Descripción del modelo de firmas de acceso compartido](storage-dotnet-shared-access-signature-part-1.md)

[Administración del acceso de lectura anónimo a contenedores y blobs](storage-manage-access-to-resources.md)

[Delegación de acceso con una firma de acceso compartido (API de REST)](http://msdn.microsoft.com/library/azure/ee395415.aspx)

[Introducción a las firmas de acceso compartido de tabla y cola](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx)

[sas-console-output-1]: ./media/storage-dotnet-shared-access-signature-part-2/sas-console-output-1.PNG
[sas-console-output-2]: ./media/storage-dotnet-shared-access-signature-part-2/sas-console-output-2.PNG

<!---HONumber=AcomDC_0218_2016-->