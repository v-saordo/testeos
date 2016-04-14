<properties
	pageTitle="Introducción al Almacenamiento de tablas de Azure mediante .NET | Microsoft Azure"
	description="Almacene datos no estructurados en la nube con el Almacenamiento de tablas de Azure, el almacén de datos NoSQL de Microsoft. Introducción a las operaciones de Almacenamiento de tablas simples, incluidas la creación y eliminación de tablas y la inserción, actualización, eliminación y consulta de datos."
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
	ms.date="02/14/2016"
	ms.author="tamram"/>


# Introducción al Almacenamiento de tablas de Azure mediante .NET

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]

## Información general

Almacenamiento de tablas de Azure es un servicio que almacena datos de NoSQL no estructurados en la nube. Almacenamiento de tablas es un almacén de claves/atributos con un diseño sin esquema. Como Almacenamiento de tablas carece de esquema, es fácil adaptar los datos a medida que evolucionan las necesidades de la aplicación. El acceso a los datos es rápido y rentable para todos los tipos de aplicaciones y, además, su coste es muy inferior al del SQL tradicional para volúmenes de datos similares.

El almacenamiento de tablas se puede usar para almacenar conjuntos de datos flexibles, como datos de usuarios para aplicaciones web, libretas de direcciones, información de dispositivos y cualquier otro tipo de metadatos requerido por el servicio. Una tabla puede almacenar un número cualquiera de entidades y una cuenta de almacenamiento puede incluir un número cualquiera de tablas, hasta alcanzar el límite de capacidad de este tipo de cuenta.

Este tutorial muestra cómo escribir código .NET para algunos escenarios comunes mediante Almacenamiento de tablas de Azure, incluidos la creación y eliminación de una tabla y la inserción, actualización, eliminación y consulta de los datos de tablas.

[AZURE.INCLUDE [storage-dotnet-client-library-version-include](../../includes/storage-dotnet-client-library-version-include.md)]

[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[AZURE.INCLUDE [storage-configure-connection-string-include](../../includes/storage-configure-connection-string-include.md)]

## Acceso al almacenamiento de tablas mediante programación

[AZURE.INCLUDE [storage-dotnet-obtain-assembly](../../includes/storage-dotnet-obtain-assembly.md)]

### Declaraciones de espacio de nombres
Agregue las siguientes declaraciones de espacio de nombres de código en la parte superior de todo archivo C# en el que desee obtener acceso al Almacenamiento de Azure mediante programación.

    using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Table;

Asegúrese de hacer referencia al ensamblado `Microsoft.WindowsAzure.Storage.dll`.

[AZURE.INCLUDE [storage-dotnet-retrieve-conn-string](../../includes/storage-dotnet-retrieve-conn-string.md)]

## Creación de una tabla

Los objetos **CloudTableClient** le permiten obtener objetos de referencia para tablas y entidades. El código siguiente crea un objeto **CloudTableClient** y lo usa para crear una nueva tabla. En todo el código de este artículo se asume que la aplicación que se está desarrollando es un proyecto del Servicio en la nube de Azure y se usa una cadena de conexión de almacenamiento guardada en la configuración del servicio de la aplicación de Azure.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the table if it doesn't exist.
    CloudTable table = tableClient.GetTableReference("people");
    table.CreateIfNotExists();

## Adición de una entidad a una tabla

Las entidades se asignan a objetos C# utilizando una clase personalizada derivada de **TableEntity**. Para agregar una entidad a una tabla, cree una clase que defina las propiedades de la entidad. El código siguiente define una clase de entidad que utiliza el nombre de pila del cliente como clave de fila y el apellido como clave de partición. En conjunto, la clave de partición y la clave de fila de una entidad la identifican inequívocamente en la tabla. Las entidades con la misma clave de partición pueden consultarse más rápidamente que aquellas con diferentes claves de partición, pero el uso de claves de partición diversas permite una mayor escalabilidad de las operaciones paralelas. Toda propiedad que deba almacenarse en el servicio Tabla debe ser una propiedad pública de un tipo compatible que exponga tanto `get` como `set`. Además, el tipo de entidad *must* expone un constructor sin parámetros.

    public class CustomerEntity : TableEntity
    {
        public CustomerEntity(string lastName, string firstName)
        {
            this.PartitionKey = lastName;
            this.RowKey = firstName;
        }

        public CustomerEntity() { }

        public string Email { get; set; }

        public string PhoneNumber { get; set; }
    }

Las operaciones de tablas que afectan a las entidades se realizan mediante el objeto **CloudTable** que se creó en la sección "Creación de una tabla". La operación que va a realizarse se representa con un objeto **TableOperation**. El ejemplo de código siguiente muestra la creación del objeto **CloudTable** y, a continuación, un objeto **CustomerEntity**. Para preparar la operación, se crea un objeto **TableOperation** para insertar la entidad de cliente en la tabla. Por último, se ejecuta la operación llamando a **CloudTable.Execute**.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

	// Create the CloudTable object that represents the "people" table.
	CloudTable table = tableClient.GetTableReference("people");

    // Create a new customer entity.
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    customer1.PhoneNumber = "425-555-0101";

    // Create the TableOperation object that inserts the customer entity.
    TableOperation insertOperation = TableOperation.Insert(customer1);

    // Execute the insert operation.
    table.Execute(insertOperation);

## Inserción de un lote de entidades

Puede insertar un lote de entidades en una tabla mediante una operación de escritura. Otras notas acerca de las operaciones por lotes:

-  Puede realizar actualizaciones, eliminaciones e inserciones en la misma operación por lotes.
-  Una única operación por lotes puede incluir hasta 100 entidades.
-  Todas las entidades de la misma operación por lotes deben compartir la misma clave de partición.
-  Aunque es posible realizar una consulta a modo de operación por lotes, debe tratarse de la única operación del lote.

<!-- -->
El siguiente ejemplo de código crea dos objetos de entidad y agrega cada uno a **TableBatchOperation** con el método **Insert**. A continuación, se llama a **CloudTable.Execute** para ejecutar la operación.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

	// Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create the batch operation.
    TableBatchOperation batchOperation = new TableBatchOperation();

    // Create a customer entity and add it to the table.
	CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
	customer1.Email = "Jeff@contoso.com";
	customer1.PhoneNumber = "425-555-0104";

	// Create another customer entity and add it to the table.
	CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
	customer2.Email = "Ben@contoso.com";
	customer2.PhoneNumber = "425-555-0102";

	// Add both customer entities to the batch insert operation.
	batchOperation.Insert(customer1);
	batchOperation.Insert(customer2);

	// Execute the batch operation.
	table.ExecuteBatch(batchOperation);

## todas las entidades de una partición

Para consultar una tabla a fin de obtener todas las entidades de una partición, utilice un objeto **TableQuery**. El ejemplo de código siguiente especifica un filtro para las entidades en las que “Smith” es la clave de partición. En este ejemplo, los campos de cada entidad se imprimen en la consola, como parte de los resultados de la consulta.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Construct the query operation for all customer entities where PartitionKey="Smith".
    TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

    // Print the fields for each customer.
    foreach (CustomerEntity entity in table.ExecuteQuery(query))
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
            entity.Email, entity.PhoneNumber);
    }

## Recuperación de un rango de entidades de una partición

Si no desea consultar todas las entidades de una partición, puede especificar un rango combinando el filtro de clave de partición con un filtro de clave de fila. El ejemplo de código siguiente utiliza dos filtros para obtener todas las entidades de la partición “Smith” en las que la clave de fila (nombre de pila) empieza por una letra anterior a “E” en el alfabeto y, a continuación, imprime los resultados de la consulta.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

	// Create the table query.
    TableQuery<CustomerEntity> rangeQuery = new TableQuery<CustomerEntity>().Where(
        TableQuery.CombineFilters(
            TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"),
            TableOperators.And,
            TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "E")));

    // Loop through the results, displaying information about the entity.
    foreach (CustomerEntity entity in table.ExecuteQuery(rangeQuery))
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
            entity.Email, entity.PhoneNumber);
    }

## Recuperación de una sola entidad

Puede enviar una consulta para recuperar una sola entidad concreta. El código siguiente utiliza**TableOperation** para especificar el cliente "Ben Smith". Este método devuelve solo una entidad, en lugar de una colección, y el valor devuelto en **TableResult.Result** es un objeto **CustomerEntity**. La forma más rápida de recuperar una sola entidad del servicio Tabla es especificar claves tanto de partición como de fila en las consultas.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a retrieve operation that takes a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the retrieve operation.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Print the phone number of the result.
	if (retrievedResult.Result != null)
	   Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
	else
	   Console.WriteLine("The phone number could not be retrieved.");

## una entidad

Para actualizar una entidad, recupérela del servicio Tabla, modifique su objeto y, a continuación, guarde los cambios de nuevo en el servicio Tabla. El código siguiente cambia el número de teléfono de un cliente. En lugar de llamar a **Insert**, este código utiliza **Replace**. Esto hace que la entidad se reemplace por completo en el servidor, a menos que la entidad del servidor cambiara desde que se recuperó, en cuyo caso la operación dará error. Este error evita que la aplicación sobrescriba accidentalmente un cambio realizado entre la recuperación y la actualización por otro componente de la misma. La manera correcta de actuar ante este error es recuperar de nuevo la entidad, hacer los cambios oportunos (si siguen siendo válidos) y, a continuación, realizar otra operación **Replace**. En la siguiente sección se muestra cómo invalidar este comportamiento.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a retrieve operation that takes a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the operation.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Assign the result to a CustomerEntity object.
    CustomerEntity updateEntity = (CustomerEntity)retrievedResult.Result;

    if (updateEntity != null)
	{
	   // Change the phone number.
	   updateEntity.PhoneNumber = "425-555-0105";

	   // Create the InsertOrReplace TableOperation.
	   TableOperation updateOperation = TableOperation.Replace(updateEntity);

	   // Execute the operation.
	   table.Execute(updateOperation);

	   Console.WriteLine("Entity updated.");
	}

	else
	   Console.WriteLine("Entity could not be retrieved.");

## Inserción o reemplazo de una entidad

Las operaciones **Replace** darán error si la entidad se cambió desde su recuperación del servidor. Asimismo, primero debe recuperar la entidad del servidor para que la operación **Replace** se realice correctamente. Sin embargo, en ocasiones no sabe si la entidad existe en el servidor y los valores actuales almacenados en él son irrelevantes. La actualización debe sobrescribir todos. Para realizarlo, debe utilizar una operación **InsertOrReplace**. Esta operación inserta la entidad si no existe o la reemplaza si ya existe, con independencia del momento en que se realizó la última actualización. En el ejemplo de código siguiente, también se recupera la entidad de cliente Ben Smith pero, a continuación, se guarda de nuevo en el servidor mediante **InsertOrReplace**. Las actualizaciones realizadas en la entidad entre las operaciones de recuperación y actualización se sobrescribirán.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a retrieve operation that takes a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the operation.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Assign the result to a CustomerEntity object.
    CustomerEntity updateEntity = (CustomerEntity)retrievedResult.Result;

    if (updateEntity != null)
	{
	   // Change the phone number.
	   updateEntity.PhoneNumber = "425-555-1234";

	   // Create the InsertOrReplace TableOperation.
	   TableOperation insertOrReplaceOperation = TableOperation.InsertOrReplace(updateEntity);

	   // Execute the operation.
	   table.Execute(insertOrReplaceOperation);

	   Console.WriteLine("Entity was updated.");
	}

	else
	   Console.WriteLine("Entity could not be retrieved.");

## Consulta de un subconjunto de propiedades de las entidades

Una consulta de tabla puede recuperar tan solo unas cuantas propiedades de una entidad en lugar de todas ellas. Esta técnica, denominada proyección, reduce el ancho de banda y puede mejorar el rendimiento de las consultas, en especial en el caso de entidades de gran tamaño. La consulta del código siguiente devuelve solo las direcciones de correo electrónico de las entidades de la tabla. Esto se consigue utilizando una consulta de **DynamicTableEntity** y también **EntityResolver**. Puede obtener más información sobre la proyección en la [entrada de blog Introducción de la proyección de upsert y consulta][]. Tenga en cuenta que no se admite la proyección en el emulador de almacenamiento local, por lo que este código solo se ejecuta cuando está usando una cuenta en el servicio Tabla.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Define the query, and select only the Email property.
    TableQuery<DynamicTableEntity> projectionQuery = new TableQuery<DynamicTableEntity>().Select(new string[] { "Email" });

    // Define an entity resolver to work with the entity after retrieval.
    EntityResolver<string> resolver = (pk, rk, ts, props, etag) => props.ContainsKey("Email") ? props["Email"].StringValue : null;

    foreach (string projectedEmail in table.ExecuteQuery(projectionQuery, resolver, null, null))
    {
        Console.WriteLine(projectedEmail);
    }

## Eliminación de una entidad

Puede eliminar fácilmente una entidad una vez recuperada utilizando el mismo patrón mostrado para actualizar una entidad. El código siguiente recupera y elimina una entidad de cliente.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a retrieve operation that expects a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the operation.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Assign the result to a CustomerEntity.
    CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;

    // Create the Delete TableOperation.
	if (deleteEntity != null)
	{
	   TableOperation deleteOperation = TableOperation.Delete(deleteEntity);

	   // Execute the operation.
	   table.Execute(deleteOperation);

	   Console.WriteLine("Entity deleted.");
	}

	else
	   Console.WriteLine("Could not retrieve the entity.");

## Eliminación de una tabla

Finalmente, el ejemplo de código siguiente elimina una tabla de una cuenta de almacenamiento. Una tabla que se ha eliminada no podrá volver a crearse durante un tiempo tras la eliminación.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Delete the table it if exists.
    table.DeleteIfExists();

## Recuperación de entidades en páginas de forma asincrónica

Si está leyendo un gran número de entidades y desea procesar/mostrar entidades según se van recuperando en lugar de esperar a que se devuelvan todas, puede recuperarlas mediante una consulta segmentada. En este ejemplo se muestra cómo devolver resultados en páginas mediante el patrón Async-Await de forma que la ejecución no se bloquee mientras se espera que se devuelva un conjunto grande de resultados. Para obtener más información sobre el uso del patrón Async-Await en. NET, vea [Programación asincrónica con Async y Await (C# y Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx).

    // Initialize a default TableQuery to retrieve all the entities in the table.
    TableQuery<CustomerEntity> tableQuery = new TableQuery<CustomerEntity>();

    // Initialize the continuation token to null to start from the beginning of the table.
    TableContinuationToken continuationToken = null;

    do
    {
        // Retrieve a segment (up to 1,000 entities).
        TableQuerySegment<CustomerEntity> tableQueryResult =
            await table.ExecuteQuerySegmentedAsync(tableQuery, continuationToken);

        // Assign the new continuation token to tell the service where to
        // continue on the next iteration (or null if it has reached the end).
        continuationToken = tableQueryResult.ContinuationToken;

        // Print the number of rows retrieved.
        Console.WriteLine("Rows retrieved {0}", tableQueryResult.Results.Count);

    // Loop until a null continuation token is received, indicating the end of the table.
    } while(continuationToken != null);

## Pasos siguientes

Ahora que está familiarizado con los aspectos básicos del almacenamiento de Tabla, siga estos vínculos para obtener más información sobre tareas de almacenamiento más complejas.

- Consulte la documentación de referencia del servicio de tabla para obtener información detallada acerca de las API disponibles:
    - [Referencia de la biblioteca de clientes de almacenamiento para .NET](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)
    - [Referencia de API de REST](http://msdn.microsoft.com/library/azure/dd179355)
- Aprenda a simplificar el código que escriba para trabajar con Almacenamiento de Azure mediante [SDK de WebJobs de Azure](../app-service-web/websites-dotnet-webjobs-sdk-get-started.md).
- Consulte más guías de características para obtener información acerca de otras opciones del almacenamiento de datos en Azure.
    - [Introducción al Almacenamiento de blobs de Azure mediante .NET](storage-dotnet-how-to-use-blobs.md) para almacenar datos estructurados.
    - [Uso de Base de datos SQL de Azure en aplicaciones .NET](sql-database-dotnet-how-to-use.md) para almacenar datos relacionales.

  [Download and install the Azure SDK for .NET]: /develop/net/
  [Creating an Azure Project in Visual Studio]: http://msdn.microsoft.com/library/azure/ee405487.aspx

  [Blob5]: ./media/storage-dotnet-how-to-use-table-storage/blob5.png
  [Blob6]: ./media/storage-dotnet-how-to-use-table-storage/blob6.png
  [Blob7]: ./media/storage-dotnet-how-to-use-table-storage/blob7.png
  [Blob8]: ./media/storage-dotnet-how-to-use-table-storage/blob8.png
  [Blob9]: ./media/storage-dotnet-how-to-use-table-storage/blob9.png

  [entrada de blog Introducción de la proyección de upsert y consulta]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx
  [.NET Client Library reference]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [Azure Storage Team blog]: http://blogs.msdn.com/b/windowsazurestorage/
  [Configure Azure Storage connection strings]: http://msdn.microsoft.com/library/azure/ee758697.aspx
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2
  [How to: Programmatically access Table storage]: #tablestorage

<!----HONumber=AcomDC_0218_2016-->