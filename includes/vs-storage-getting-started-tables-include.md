#####Creación de una tabla
Los objetos **CloudTableClient** le permiten obtener objetos de referencia para tablas y entidades. El código siguiente crea un objeto **CloudTableClient** y lo usa para crear una nueva tabla. El código intenta hacer referencia a una tabla denominada “people”. Si no encuentra ninguna tabla con ese nombre, la crea.

**NOTA**: en todo el código de esta guía se asume que la aplicación que se está desarrollando es un proyecto del Servicio en la nube de Azure y se usa una cadena de conexión de almacenamiento guardada en la configuración del servicio de la aplicación de Azure.

	// Create the table client.
	CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
	
	// Create the table if it doesn't exist.
	CloudTable table = tableClient.GetTableReference("people");
	table.CreateIfNotExists();

#####Adición de una entidad a una tabla
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

Las operaciones de tablas que afectan a las entidades se realizan con el objeto **CloudTable** que se creó anteriormente en el apartado "Creación de una tabla". El objeto **TableOperation** representa la operación que se va a realizar. En el ejemplo de código siguiente se muestra cómo crear un objeto **CloudTable** y un objeto **CustomerEntity**. Para preparar la operación, se crea un objeto **TableOperation** para insertar la entidad de cliente en la tabla. Por último, se ejecuta la operación llamando a CloudTable.Execute.

	// Create the CloudTable object that represents the "people" table.
	CloudTable table = tableClient.GetTableReference("people");
	
	// Create a new customer entity.
	CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
	customer1.Email = "Walter@contoso.com";
	customer1.PhoneNumber = "425-555-0101";
	
	// Create the TableOperation that inserts the customer entity.
	TableOperation insertOperation = TableOperation.Insert(customer1);
	
	// Execute the insert operation.
	table.Execute(insertOperation);

#####Inserción de un lote de entidades
Puede insertar varias entidades en una tabla mediante una única operación de escritura. El siguiente ejemplo de código crea dos objetos de entidad ("Jeff Smith" y "Ben Smith"), agrega cada uno de ellos a un objeto **TableBatchOperation** con el método Insert y, a continuación, inicia la operación llamando a CloudTable.Execute.

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

#####Obtención de todas las entidades en una partición
Para consultar una tabla a fin de obtener todas las entidades de una partición, use un objeto **TableQuery**. El ejemplo de código siguiente especifica un filtro para las entidades en las que “Smith” es la clave de partición. En este ejemplo, los campos de cada entidad se imprimen en la consola, como parte de los resultados de la consulta.

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

#####Obtención de una sola entidad
Puede escribir una consulta para obtener una sola entidad concreta. El código siguiente utiliza un objeto **TableOperation** para especificar el cliente llamado "Ben Smith". Este método devuelve solo una entidad, en lugar de una colección, y el valor devuelto en TableResult.Result es un objeto **CustomerEntity**. La forma más rápida de recuperar una sola entidad del servicio **Tabla** es especificar claves tanto de partición como de fila en las consultas.

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

#####Eliminación de una entidad
Puede eliminar fácilmente una entidad después de haberla encontrado. El código siguiente busca una entidad de cliente denominada "Ben Smith" y, si la encuentra, la elimina.

	// Create the CloudTable that represents the "people" table.
	CloudTable table = tableClient.GetTableReference("people");
	
	// Create a retrieve operation that expects a customer entity.
	TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");
	
	// Execute the operation.
	TableResult retrievedResult = table.Execute(retrieveOperation);
	
	// Assign the result to a CustomerEntity object.
	CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;
	
	// Create the Delete TableOperation and then execute it.
	if (deleteEntity != null)
	{
	   TableOperation deleteOperation = TableOperation.Delete(deleteEntity);
	
	   // Execute the operation.
	   table.Execute(deleteOperation);
	
	   Console.WriteLine("Entity deleted.");
	}
	
	else
	   Console.WriteLine("Couldn't delete the entity.");

[Más información acerca de Almacenamiento de Azure](http://azure.microsoft.com/documentation/services/storage/) Consulte también [Explorar recursos de almacenamiento en el Explorador de servidores](http://msdn.microsoft.com/library/azure/ff683677.aspx).

<!---HONumber=62-->