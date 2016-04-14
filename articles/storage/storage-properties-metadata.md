
<properties 
  pageTitle="Establecimiento y recuperación de propiedades y metadatos para los objetos contenidos en Almacenamiento de Azure | Microsoft Azure" 
  description="Almacenamiento de metadatos personalizados en los objetos de Almacenamiento de Azure y establecimiento y recuperación de propiedades del sistema." 
  services="storage" 
  documentationCenter="" 
  authors="tamram" 
  manager="carmonm" 
  editor="tysonn"/>

<tags 
  ms.service="storage" 
  ms.workload="storage" 
  ms.tgt_pltfrm="na" 
  ms.devlang="na" 
  ms.topic="article" 
  ms.date="02/20/2016" 
  ms.author="tamram"/>


# Establecer y recuperar propiedades y metadatos #

## Información general

Objetos en las propiedades de compatibilidad de sistema de Almacenamiento de Azure y metadatos definidos por el usuario, además de los datos que contienen:

*   **Propiedades del sistema.** En cada recurso de almacenamiento existen propiedades del sistema. Algunas se pueden leer o establecer, mientras que otras son de solo lectura. En segundo plano, algunas propiedades del sistema corresponden a ciertos encabezados HTTP estándar. La biblioteca de cliente de almacenamiento de Azure las mantiene automáticamente.  

*   **Metadatos definidos por el usuario.** Los metadatos definidos por el usuario son los metadatos que se especifican en un recurso determinado, en forma de par nombre-valor. Puede usar metadatos para almacenar valores adicionales con un recurso de almacenamiento; estos valores son únicamente para sus propios fines y no afectan a la manera en que se comporta el recurso.

El proceso de recuperación de los valores de propiedad y metadatos de un recurso de almacenamiento consta de dos pasos. Para poder leer estos valores, tiene que capturarlos explícitamente llamando el método **FetchAttributes**.

> [AZURE.IMPORTANT] Los valores de propiedad y los metadatos para un recurso de almacenamiento no se rellenan, a menos que se llame a uno de los métodos **FetchAttributes**.

## Establecer y recuperar propiedades

Para recuperar valores de propiedad, llame al método **FetchAttributes** en el blob o contenedor para rellenar las propiedades y, a continuación, lea los valores.

Para establecer propiedades en un objeto, especifique el valor de propiedad y, a continuación, llame al método **SetProperties**.

En el ejemplo de código siguiente se crea un contenedor y se escriben algunos de sus valores de propiedad en una ventana de la consola:

    //Parse the connection string for the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));
	
	//Create the service client object for credentialed access to the Blob service.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve a reference to a container. 
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Create the container if it does not already exist.
    container.CreateIfNotExists();

    // Fetch container properties and write out their values.
    container.FetchAttributes();
    Console.WriteLine("Properties for container {0}", container.StorageUri.PrimaryUri.ToString());
    Console.WriteLine("LastModifiedUTC: {0}", container.Properties.LastModified.ToString());
    Console.WriteLine("ETag: {0}", container.Properties.ETag);
    Console.WriteLine();

## Establecer y recuperar metadatos

Puede especificar metadatos como uno o más pares nombre-valor en un recurso de blob o contenedor. Para establecer los metadatos, agregue pares nombre-valor a la colección **Metadata** del recurso y, a continuación, llame al método **SetMetadata** para guardar los valores en el servicio.

> [AZURE.NOTE]\: El nombre de los metadatos debe cumplir las convenciones de nomenclatura para los identificadores de C#.
 
El ejemplo de código siguiente establece los metadatos en un contenedor. Un valor se establece mediante el método **Agregar** de la colección. El otro valor se establece mediante la sintaxis implícita de clave/valor. Ambos son válidos.

    public static void AddContainerMetadata(CloudBlobContainer container)
    {
        //Add some metadata to the container.
        container.Metadata.Add("docType", "textDocuments");
        container.Metadata["category"] = "guidance";

        //Set the container's metadata.
        container.SetMetadata();
    }

Para recuperar metadatos, llame al método **FetchAttributes** en el blob o contenedor para rellenar la colección **Metadata** y, a continuación, lea los valores como se muestra en el ejemplo siguiente.

    public static void ListContainerMetadata(CloudBlobContainer container)
    {
        //Fetch container attributes in order to populate the container's properties and metadata.
        container.FetchAttributes();

        //Enumerate the container's metadata.
        Console.WriteLine("Container metadata:");
        foreach (var metadataItem in container.Metadata)
        {
            Console.WriteLine("\tKey: {0}", metadataItem.Key);
            Console.WriteLine("\tValue: {0}", metadataItem.Value);
        }
    }

## Otras referencias  

- [Documentación de referencia de la biblioteca cliente de Almacenamiento de Azure para .NET](http://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx)
- [Paquete de la biblioteca cliente de Almacenamiento de Azure para .NET](https://www.nuget.org/packages/WindowsAzure.Storage/) 

<!---HONumber=AcomDC_0224_2016-->