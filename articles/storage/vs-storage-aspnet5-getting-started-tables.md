<properties
	pageTitle="Introducción al almacenamiento de tablas y los servicios conectados de Visual Studio (ASP.NET 5) | Microsoft Azure"
	description="Introducción a Almacenamiento de tablas de Azure en un proyecto ASP.NET 5 en Visual Studio después de conectarse a una cuenta de almacenamiento mediante los servicios conectados de Visual Studio"
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

# Introducción al almacenamiento Tabla de Azure y los servicios conectados de Visual Studio

## Información general

En este artículo se describe cómo empezar a usar el almacenamiento Tabla de Azure en Visual Studio después de crear una cuenta de almacenamiento de Azure en un proyecto de ASP.NET 5 mediante el cuadro de diálogo **Agregar servicios conectados** de Visual Studio, o después de hacer referencia a una.

El servicio de almacenamiento de tabla de Azure permite almacenar una gran cantidad de datos estructurados. El servicio es un almacén de datos NoSQL que acepta llamadas autenticadas desde dentro y fuera de la nube de Azure. Las tablas de Azure son ideales para el almacenamiento de datos estructurados no relacionales.

La operación **Agregar servicios conectados** instala los paquetes de NuGet adecuados para tener acceso a almacenamiento de Azure en su proyecto y agrega la cadena de conexión para la cuenta de almacenamiento para los archivos de configuración del proyecto.

Para más información general sobre el uso del almacenamiento en tablas de Azure, vea [Introducción al Almacenamiento de tablas de Azure mediante .NET](storage-dotnet-how-to-use-tables.md).

Para comenzar, necesita crear una tabla en su cuenta de almacenamiento. Le mostraremos cómo crear una tabla de Azure en el código. También le mostraremos cómo realizar operaciones básicas de tabla y de entidad, como agregar, modificar, leer y leer entidades de tabla. Los ejemplos están escritos en código C# y utilizan la biblioteca del cliente de almacenamiento de Azure para .NET.

**NOTA:** algunas de las API que realizan llamadas al almacenamiento de Azure en ASP.NET 5 son asincrónicas. Consulte [Programación asincrónica con Async y Await](http://msdn.microsoft.com/library/hh191443.aspx) para obtener más información. El código siguiente supone que se están utilizando métodos de programación asincrónica.

## Acceso a tablas en código

Para obtener acceso a las tablas en los proyectos ASP.NET 5, deberá incluir los elementos siguientes en los archivos de origen C# que acceden al almacenamiento de tablas de Azure.

1. Asegúrese de que las declaraciones del espacio de nombres de la parte superior del archivo de C# incluyen estas instrucciones **using**.

	    using Microsoft.Framework.Configuration;
	    using Microsoft.WindowsAzure.Storage;
	    using Microsoft.WindowsAzure.Storage.Table;
	    using System.Threading.Tasks;
	    using LogLevel = Microsoft.Framework.Logging.LogLevel;

2. Obtenga un objeto **CloudStorageAccount** que represente la información de su cuenta de almacenamiento. Use el código siguiente para obtener la cadena de conexión de almacenamiento y la información de la cuenta de almacenamiento de la configuración del servicio de Azure.

	    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
	        CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

    **NOTA**: use todo el código anterior delante del código que aparece en los ejemplos siguientes.

3. Obtenga un objeto **CloudTableClient** para hacer referencia a los objetos de tabla de la cuenta de almacenamiento.

	    // Create the table client.
    	CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

4. Obtenga un objeto de referencia **CloudTable** para hacer referencia a una tabla y a entidades específicas.

    	// Get a reference to a table named "peopleTable"
	    CloudTable table = tableClient.GetTableReference("peopleTable");

## Crear una tabla en el código

Para crear la tabla de Azure, solo tiene que agregar una llamada a **CreateIfNotExistsAsync()**.

	// Create the CloudTable if it does not exist
	await table.CreateIfNotExistsAsync();

## Adición de una entidad a una tabla

Para agregar una entidad a una tabla, cree una clase que defina las propiedades de la entidad. El código siguiente define una clase de entidad llamada **CustomerEntity** que usa el nombre del cliente como clave de fila y el apellido como clave de partición.

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

Las operaciones de tablas que afectan a las entidades se realizan con el objeto **CloudTable** que se creó anteriormente en el apartado "Acceso a tablas en código". El objeto **TableOperation** representa la operación que se va a realizar. En el ejemplo de código siguiente se muestra cómo crear un objeto **CloudTable** y un objeto **CustomerEntity**. Para preparar la operación, se crea un objeto **TableOperation** para insertar la entidad de cliente en la tabla. Por último, la operación se ejecuta llamando a CloudTable.ExecuteAsync.

	// Create a new customer entity.
	CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
	customer1.Email = "Walter@contoso.com";
	customer1.PhoneNumber = "425-555-0101";

	// Create the TableOperation that inserts the customer entity.
	TableOperation insertOperation = TableOperation.Insert(customer1);

	// Execute the insert operation.
	await peopleTable.ExecuteAsync(insertOperation);

## Inserción de un lote de entidades

Puede insertar varias entidades en una tabla mediante una única operación de escritura. En el ejemplo de código siguiente se crean dos objetos de entidad ("Jeff Smith" y "Ben Smith") que se agregan a un objeto **TableBatchOperation** mediante el método **Insert** y luego se inicia la operación llamando a CloudTable.ExecuteBatchAsync.

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
	await peopleTable.ExecuteBatchAsync(batchOperation);

## Obtención de todas las entidades en una partición
Para consultar una tabla a fin de obtener todas las entidades de una partición, use un objeto **TableQuery**. El ejemplo de código siguiente especifica un filtro para las entidades en las que “Smith” es la clave de partición. En este ejemplo, los campos de cada entidad se imprimen en la consola, como parte de los resultados de la consulta.

	// Construct the query operation for all customer entities where PartitionKey="Smith".
    TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

    // Print the fields for each customer.
    TableContinuationToken token = null;
    do
    {
    	TableQuerySegment<CustomerEntity> resultSegment = await peopleTable.ExecuteQuerySegmentedAsync(query, token);
		token = resultSegment.ContinuationToken;

		foreach (CustomerEntity entity in resultSegment.Results)
    	{
    		Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
    		entity.Email, entity.PhoneNumber);
        }
    } while (token != null);

## Obtención de una sola entidad
Puede escribir una consulta para obtener una sola entidad concreta. El código siguiente utiliza un objeto **TableOperation** para especificar el cliente llamado "Ben Smith". Este método devuelve solo una entidad, en lugar de una colección, y el valor devuelto en **TableResult.Result** es un objeto **CustomerEntity**. La forma más rápida de recuperar una sola entidad del servicio **Tabla** es especificar claves tanto de partición como de fila en las consultas.

	// Create a retrieve operation that takes a customer entity.
	TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

	// Execute the retrieve operation.
	TableResult retrievedResult = await peopleTable.ExecuteAsync(retrieveOperation);

	// Print the phone number of the result.
	if (retrievedResult.Result != null)
	   Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
	else
	   Console.WriteLine("The phone number could not be retrieved.");

## Eliminación de una entidad
Puede eliminar fácilmente una entidad después de haberla encontrado. El código siguiente busca una entidad de cliente denominada "Ben Smith" y, si la encuentra, la elimina.

	// Create a retrieve operation that expects a customer entity.
	TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

	// Execute the operation.
	TableResult retrievedResult = peopleTable.Execute(retrieveOperation);

	// Assign the result to a CustomerEntity object.
	CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;

	// Create the Delete TableOperation and then execute it.
	if (deleteEntity != null)
	{
	   TableOperation deleteOperation = TableOperation.Delete(deleteEntity);

	   // Execute the operation.
	   await peopleTable.ExecuteAsync(deleteOperation);

	   Console.WriteLine("Entity deleted.");
	}

	else
	   Console.WriteLine("Couldn't delete the entity.");

## Pasos siguientes

[AZURE.INCLUDE [vs-storage-dotnet-tables-next-steps](../../includes/vs-storage-dotnet-tables-next-steps.md)]

<!---HONumber=AcomDC_0224_2016-->