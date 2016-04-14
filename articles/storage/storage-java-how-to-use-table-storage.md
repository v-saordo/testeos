<properties
	pageTitle="Uso del almacenamiento de tablas de Java | Microsoft Azure"
	description="Aprenda a usar el servicio de almacenamiento de tablas en Azure. Los ejemplos de código están escritos en código Java."
	services="storage"
	documentationCenter="java"
	authors="rmcmurray"
	manager="wpickett"
	editor="jimbe"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="Java"
	ms.topic="article"
	ms.date="02/29/2016"
	ms.author="robmcm"/>


# Uso del almacenamiento de tablas de Java

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]

## Información general

Esta guía muestra cómo realizar algunas tareas comunes a través del servicio de almacenamiento de tablas de Azure. Los ejemplos están escritos en Java y utilizan el [SDK de almacenamiento de Azure para Java][]. Entre los escenarios descritos se incluyen **crear**, **enumerar** y **eliminar**, así como **insertar**, **consultar**, **modificar** y **eliminar** entidades de una tabla. Para obtener más información acerca de las tablas, consulte la sección [Pasos siguientes](#Next-Steps).

Nota: hay un SDK disponible para los desarrolladores que usen el almacenamiento de Azure en dispositivos Android. Para obtener más información, vea el [SDK de almacenamiento de Azure para Android][].

[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Creación de una aplicación Java

En esta guía utilizará funciones del almacenamiento que puede ejecutar en una aplicación Java localmente o bien mediante código a través de un rol web o un rol de trabajo de Azure.

Para ello, deberá instalar el Kit de desarrollo de Java (JDK) y crear una cuenta de almacenamiento de Azure en su suscripción de Azure. Después, deberá verificar que su sistema de desarrollo satisface los requisitos mínimos y las dependencias que se indican en el repositorio del [SDK de almacenamiento de Azure para Java][] en GitHub. Si su sistema cumple esos requisitos, puede seguir las instrucciones para descargar e instalar las bibliotecas de almacenamiento de Azure para Java en su sistema desde ese repositorio. Cuando haya completado esas tareas, podrá crear una aplicación Java que use los ejemplos de este artículo.

## Configuración de la aplicación para acceder al almacenamiento de tablas

Agregue las siguientes instrucciones de importación en la parte superior del archivo Java en el que desea utilizar las API de almacenamiento de Microsoft Azure para obtener acceso a las tablas:

    // Include the following imports to use table APIs
    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.table.*;
    import com.microsoft.azure.storage.table.TableQuery.*;

## Configuración de una cadena de conexión de almacenamiento de Azure

Un cliente de almacenamiento de Azure utiliza una cadena de conexión de almacenamiento para almacenar extremos y credenciales con el fin de obtener acceso a los servicios de administración de datos. Al ejecutarse en una aplicación cliente, debe proporcionar la cadena de conexión de almacenamiento en el siguiente formato, usando el nombre de su cuenta de almacenamiento y la clave de acceso principal de la cuenta de almacenamiento que se muestra en el [Portal de Azure](https://portal.azure.com) para los valores *AccountName* y *AccountKey*. En este ejemplo se muestra cómo puede declarar un campo estático para mantener la cadena de conexión:

    // Define the connection-string with your values.
    public static final String storageConnectionString =
        "DefaultEndpointsProtocol=http;" +
        "AccountName=your_storage_account;" +
        "AccountKey=your_storage_account_key";

En una aplicación que se esté ejecutando en un rol de Microsoft Azure, esta cadena se puede almacenar en el archivo de configuración de servicio, *ServiceConfiguration.cscfg*, y se puede obtener acceso a él con una llamada al método **RoleEnvironment.getConfigurationSettings**. A continuación se muestra un ejemplo de cómo obtener la cadena de conexión desde un elemento de **configuración** denominado *StorageConnectionString* en el archivo de configuración del servicio:

    // Retrieve storage account from connection-string.
    String storageConnectionString =
        RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");

En los ejemplos siguientes se supone que ha usado uno de estos dos métodos para obtener la cadena de conexión de almacenamiento.

## Cómo crear una tabla

Los objetos **CloudTableClient** le permiten obtener objetos de referencia para tablas y entidades. El siguiente código crea un objeto **CloudTableClient** y lo usa para crear un nuevo objeto **CloudTable** que representa una tabla llamada "people". (Nota: Hay otras maneras de crear objetos **CloudStorageAccount**. Para obtener más información, consulte **CloudStorageAccount** en la [Referencia del SDK de cliente de almacenamiento de Azure]).

    try
    {
    	// Retrieve storage account from connection-string.
    	CloudStorageAccount storageAccount =
	       CloudStorageAccount.parse(storageConnectionString);

	   // Create the table client.
	   CloudTableClient tableClient = storageAccount.createCloudTableClient();

	   // Create the table if it doesn't exist.
	   String tableName = "people";
	   CloudTable cloudTable = new CloudTable(tableName,tableClient);
	   cloudTable.createIfNotExists();
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## Enumeración de las tablas

Para obtener una lista de las tablas, llame al método **CloudTableClient.listTables()** para recuperar una lista que se puede iterar de nombres de tablas.

    try
    {
    	// Retrieve storage account from connection-string.
    	CloudStorageAccount storageAccount =
	       CloudStorageAccount.parse(storageConnectionString);

    	// Create the table client.
    	CloudTableClient tableClient = storageAccount.createCloudTableClient();

    	// Loop through the collection of table names.
    	for (String table : tableClient.listTables())
    	{
		  // Output each table name.
		  System.out.println(table);
	   }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## Cómo agregar una entidad a una tabla

Las entidades se asignan a objetos de Java utilizando una clase personalizada que implementa **TableEntity**. Para mayor comodidad, la clase **TableServiceEntity** implementa **TableEntity** y usa la reflexión para asignar propiedades a los métodos de captador y establecedor indicados para las propiedades. Para agregar una entidad a una tabla, cree primero una clase que defina las propiedades de la entidad. El código siguiente define una clase de entidad que utiliza el nombre de pila del cliente como clave de fila y el apellido como clave de partición. En conjunto, la clave de partición y la clave de fila de una entidad la identifican inequívocamente en la tabla. Puede realizarse una consulta en las entidades con la misma clave de partición de manera más rápida que en aquellas que tienen claves de partición distintas.

    public class CustomerEntity extends TableServiceEntity {
        public CustomerEntity(String lastName, String firstName) {
            this.partitionKey = lastName;
            this.rowKey = firstName;
        }

        public CustomerEntity() { }

        String email;
        String phoneNumber;

        public String getEmail() {
            return this.email;
        }

        public void setEmail(String email) {
            this.email = email;
        }

        public String getPhoneNumber() {
            return this.phoneNumber;
        }

        public void setPhoneNumber(String phoneNumber) {
            this.phoneNumber = phoneNumber;
        }
    }

Las operaciones de tabla que afectan a las entidades requieren un objeto **TableOperation**. Este objeto define la operación que va a realizarse en una entidad, que puede ejecutarse con un objeto **CloudTable**. El código siguiente crea una instancia nueva de la clase **CustomerEntity** con algunos datos de clientes que se van a almacenar. El código siguiente llama a **TableOperation.insertOrReplace** para crear un objeto **TableOperation** a fin de insertar una entidad en una tabla y asocia el objeto **CustomerEntity** a ella. Por último, el código llama al método **execute** en **CloudTable**, especificando la tabla "people" y el nuevo objeto **TableOperation**, que posteriormente envía una solicitud al servicio de almacenamiento para insertar la nueva entidad de cliente en la tabla "people" o sustituir la entidad si ya existe.

    try
    {
    	// Retrieve storage account from connection-string.
    	CloudStorageAccount storageAccount =
	       CloudStorageAccount.parse(storageConnectionString);

    	// Create the table client.
    	CloudTableClient tableClient = storageAccount.createCloudTableClient();

    	// Create a cloud table object for the table.
    	CloudTable cloudTable = tableClient.getTableReference("people");

    	// Create a new customer entity.
    	CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    	customer1.setEmail("Walter@contoso.com");
    	customer1.setPhoneNumber("425-555-0101");

    	// Create an operation to add the new customer to the people table.
    	TableOperation insertCustomer1 = TableOperation.insertOrReplace(customer1);

    	// Submit the operation to the table service.
    	cloudTable.execute(insertCustomer1);
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## Cómo insertar un lote de entidades

Puede insertar un lote de entidades en el servicio Tabla mediante una operación de escritura. El siguiente código crea un objeto **TableBatchOperation** y, a continuación, le agrega tres operaciones de inserción. Cada operación de inserción se agrega mediante la creación de un nuevo objeto de entidad, se configuran sus valores y, a continuación, se llama al método **insert** en el objeto **TableBatchOperation** para asociar la entidad a una nueva operación de inserción. A continuación, el código llama a **execute** en el objeto **CloudTable**, especificando la tabla "people" y el objeto **TableBatchOperation**, que envía el lote de operaciones de tabla al servicio de almacenamiento en una única solicitud.

    try
    {
    	// Retrieve storage account from connection-string.
    	CloudStorageAccount storageAccount =
	       CloudStorageAccount.parse(storageConnectionString);

    	// Create the table client.
    	CloudTableClient tableClient = storageAccount.createCloudTableClient();

    	// Define a batch operation.
    	TableBatchOperation batchOperation = new TableBatchOperation();

    	// Create a cloud table object for the table.
    	CloudTable cloudTable = tableClient.getTableReference("people");

    	// Create a customer entity to add to the table.
    	CustomerEntity customer = new CustomerEntity("Smith", "Jeff");
    	customer.setEmail("Jeff@contoso.com");
    	customer.setPhoneNumber("425-555-0104");
    	batchOperation.insertOrReplace(customer);

	   // Create another customer entity to add to the table.
	   CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
	   customer2.setEmail("Ben@contoso.com");
	   customer2.setPhoneNumber("425-555-0102");
	   batchOperation.insertOrReplace(customer2);

	   // Create a third customer entity to add to the table.
	   CustomerEntity customer3 = new CustomerEntity("Smith", "Denise");
	   customer3.setEmail("Denise@contoso.com");
	   customer3.setPhoneNumber("425-555-0103");
	   batchOperation.insertOrReplace(customer3);

	   // Execute the batch of operations on the "people" table.
	   cloudTable.execute(batchOperation);
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

Algunos aspectos que cabe tener en cuenta acerca de las operaciones por lotes:

- Puede ejecutar cualquier combinación de 100 operaciones de inserción, eliminación, combinación, reemplazo, inserción o combinación e inserción o reemplazo en un único lote.
- Una operación por lotes puede tener una operación de recuperación, si es que es la única operación del lote.
- Todas las entidades de la misma operación por lotes deben compartir la misma clave de partición.
- Una operación por lotes se limita a una carga de datos de 4 MB.

## Cómo recuperar todas las entidades de una partición

Para consultar una tabla a fin de obtener las entidades de una partición, use un objeto **TableQuery**. Llame a **TableQuery.from** para crear una consulta en una tabla concreta que devuelva un tipo de resultado específico. El código siguiente especifica un filtro para las entidades en las que "Smith" es la clave de partición. **TableQuery.generateFilterCondition** es un método auxiliar para crear filtros para las consultas. Llame a **where** en la referencia devuelta por el método **TableQuery.from** para aplicar el filtro a la consulta. Si la consulta se ejecuta con una llamada a **execute** en el objeto **CloudTable**, devuelve un **iterador** con el tipo de resultado **CustomerEntity** especificado. A continuación, puede usar el **iterador** devuelto para cada bucle para consumir los resultados. En este código, los campos de cada entidad se imprimen en la consola, como parte de los resultados de la consulta.

    try
    {
    	// Define constants for filters.
    	final String PARTITION_KEY = "PartitionKey";
    	final String ROW_KEY = "RowKey";
    	final String TIMESTAMP = "Timestamp";

    	// Retrieve storage account from connection-string.
    	CloudStorageAccount storageAccount =
	       CloudStorageAccount.parse(storageConnectionString);

    	// Create the table client.
    	CloudTableClient tableClient = storageAccount.createCloudTableClient();

	   // Create a cloud table object for the table.
	   CloudTable cloudTable = tableClient.getTableReference("people");

    	// Create a filter condition where the partition key is "Smith".
    	String partitionFilter = TableQuery.generateFilterCondition(
	       PARTITION_KEY,
	       QueryComparisons.EQUAL,
	       "Smith");

	   // Specify a partition query, using "Smith" as the partition key filter.
	   TableQuery<CustomerEntity> partitionQuery =
	       TableQuery.from(CustomerEntity.class)
	       .where(partitionFilter);

        // Loop through the results, displaying information about the entity.
        for (CustomerEntity entity : cloudTable.execute(partitionQuery)) {
            System.out.println(entity.getPartitionKey() +
                " " + entity.getRowKey() +
                "\t" + entity.getEmail() +
                "\t" + entity.getPhoneNumber());
	   }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## Cómo recuperar un intervalo de entidades de una partición

Si no quiere consultar todas las entidades de una partición, puede especificar un rango mediante el uso de operadores de comparación en un filtro. El código siguiente combina dos filtros para obtener todas las entidades de la partición "Smith" en las que la clave de fila (nombre de pila) empieza por una letra hasta "E" en el alfabeto. A continuación, imprime los resultados de la consulta. Si usa las entidades agregadas a la tabla en la sección de inserción por lotes de esta guía, solo se devuelven dos entidades en este momento (Ben y Denise Smith); Jeff Smith no se incluye.

    try
    {
    	// Define constants for filters.
    	final String PARTITION_KEY = "PartitionKey";
    	final String ROW_KEY = "RowKey";
    	final String TIMESTAMP = "Timestamp";

    	// Retrieve storage account from connection-string.
    	CloudStorageAccount storageAccount =
	       CloudStorageAccount.parse(storageConnectionString);

	   // Create the table client.
	   CloudTableClient tableClient = storageAccount.createCloudTableClient();

	   // Create a cloud table object for the table.
	   CloudTable cloudTable = tableClient.getTableReference("people");

    	// Create a filter condition where the partition key is "Smith".
    	String partitionFilter = TableQuery.generateFilterCondition(
	       PARTITION_KEY,
	       QueryComparisons.EQUAL,
	       "Smith");

    	// Create a filter condition where the row key is less than the letter "E".
    	String rowFilter = TableQuery.generateFilterCondition(
	       ROW_KEY,
	       QueryComparisons.LESS_THAN,
	       "E");

    	// Combine the two conditions into a filter expression.
    	String combinedFilter = TableQuery.combineFilters(partitionFilter,
	        Operators.AND, rowFilter);

    	// Specify a range query, using "Smith" as the partition key,
    	// with the row key being up to the letter "E".
    	TableQuery<CustomerEntity> rangeQuery =
	       TableQuery.from(CustomerEntity.class)
	       .where(combinedFilter);

    	// Loop through the results, displaying information about the entity
        for (CustomerEntity entity : cloudTable.execute(rangeQuery)) {
            System.out.println(entity.getPartitionKey() +
                " " + entity.getRowKey() +
                "\t" + entity.getEmail() +
                "\t" + entity.getPhoneNumber());
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## Cómo recuperar una sola entidad

Puede enviar una consulta para recuperar una sola entidad concreta. El código siguiente llama a **TableOperation.retrieve** con los parámetros de clave de partición y clave de fila para especificar el cliente "Jeff Smith", en lugar de crear un elemento **TableQuery** y usar filtros para realizar la misma operación. Cuando se ejecuta, la operación de recuperación devuelve solo una entidad, en lugar de una colección de entidades. El método **getResultAsType** convierte el resultado en el tipo de objetivo de asignación, un objeto **CustomerEntity**. Si este tipo no es compatible con el tipo especificado para la consulta, se mostrará una excepción. Se devuelve un valor nulo si no coincide exactamente la clave de fila y de partición de ninguna entidad. La forma más rápida de recuperar una sola entidad del servicio Tabla es especificar claves tanto de partición como de fila en las consultas.

    try
    {
    	// Retrieve storage account from connection-string.
    	CloudStorageAccount storageAccount =
	       CloudStorageAccount.parse(storageConnectionString);

    	// Create the table client.
    	CloudTableClient tableClient = storageAccount.createCloudTableClient();

    	// Create a cloud table object for the table.
    	CloudTable cloudTable = tableClient.getTableReference("people");

    	// Retrieve the entity with partition key of "Smith" and row key of "Jeff"
    	TableOperation retrieveSmithJeff =
	       TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

	   // Submit the operation to the table service and get the specific entity.
	   CustomerEntity specificEntity =
    		cloudTable.execute(retrieveSmithJeff).getResultAsType();

    	// Output the entity.
    	if (specificEntity != null)
    	{
            System.out.println(specificEntity.getPartitionKey() +
                " " + specificEntity.getRowKey() +
                "\t" + specificEntity.getEmail() +
                "\t" + specificEntity.getPhoneNumber());
	   }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## Modificación de una entidad

Para modificar una entidad, recupérela del servicio Tabla, realice los cambios en el objeto de entidad y vuelva a guardar los cambios en dicho servicio con una operación de reemplazo o combinación. El código siguiente cambia el número de teléfono de un cliente. En lugar de llamar a **TableOperation.insert** como hicimos para la inserción, este código llama a **TableOperation.replace**. El método **CloudTable.execute** llama al servicio Tabla y la entidad se reemplaza, a no ser que otra aplicación la haya modificado desde que la aplicación la recuperó. Cuando se produce esa situación, se muestra una excepción y la entidad debe recuperarse, modificarse y guardarse de nuevo. Este patrón de reintento de simultaneidad optimista es común en un sistema de almacenamiento distribuido.

    try
    {
    	// Retrieve storage account from connection-string.
    	CloudStorageAccount storageAccount =
	       CloudStorageAccount.parse(storageConnectionString);

    	// Create the table client.
    	CloudTableClient tableClient = storageAccount.createCloudTableClient();

    	// Create a cloud table object for the table.
    	CloudTable cloudTable = tableClient.getTableReference("people");

    	// Retrieve the entity with partition key of "Smith" and row key of "Jeff".
    	TableOperation retrieveSmithJeff =
	       TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

    	// Submit the operation to the table service and get the specific entity.
    	CustomerEntity specificEntity =
		  cloudTable.execute(retrieveSmithJeff).getResultAsType();

    	// Specify a new phone number.
    	specificEntity.setPhoneNumber("425-555-0105");

    	// Create an operation to replace the entity.
    	TableOperation replaceEntity = TableOperation.replace(specificEntity);

    	// Submit the operation to the table service.
    	cloudTable.execute(replaceEntity);
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## Cómo consultar un subconjunto de las propiedades de entidad

Una consulta de tabla puede recuperar solo algunas propiedades de una entidad. Esta técnica, denominada proyección, reduce el ancho de banda y puede mejorar el rendimiento de las consultas, en especial en el caso de entidades de gran tamaño. La consulta del código siguiente usa el método **select** para devolver solo las direcciones de correo electrónico de las entidades de la tabla. Los resultados se proyectan en una colección de propiedades **String** con la ayuda de un objeto **EntityResolver**, que hace la conversión del tipo de entidades que el servidor devuelve. Puede obtener más información sobre la proyección en [Tablas de Azure: Introducción de Upsert y proyección de consultas][]. Tenga en cuenta que la proyección no es compatible con el emulador de almacenamiento local, por lo que este código solo se ejecuta cuando se utiliza una cuenta del servicio Tabla.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

    	// Create the table client.
    	CloudTableClient tableClient = storageAccount.createCloudTableClient();

    	// Create a cloud table object for the table.
    	CloudTable cloudTable = tableClient.getTableReference("people");

    	// Define a projection query that retrieves only the Email property
    	TableQuery<CustomerEntity> projectionQuery =
	       TableQuery.from(CustomerEntity.class)
	       .select(new String[] {"Email"});

    	// Define a Entity resolver to project the entity to the Email value.
    	EntityResolver<String> emailResolver = new EntityResolver<String>() {
            @Override
            public String resolve(String PartitionKey, String RowKey, Date timeStamp, HashMap<String, EntityProperty> properties, String etag) {
                return properties.get("Email").getValueAsString();
            }
        };

        // Loop through the results, displaying the Email values.
        for (String projectedString :
            cloudTable.execute(projectionQuery, emailResolver)) {
                System.out.println(projectedString);
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## Inserción o reemplazo de una entidad

En ocasiones, es posible que desee agregar una entidad a una tabla sin saber si ya existe en la tabla. Una operación de inserción o reemplazo le permite realizar una consulta única que insertará la entidad si no existe o que reemplazará la existente si la hubiera. Según los ejemplos anteriores, el siguiente código inserta o reemplaza la entidad de "Walter Harp". Después de crear una nueva entidad, este código llama al método **TableOperation.insertOrReplace**. A continuación, este código llama a **execute** en el objeto **CloudTable** con la tabla y las operaciones de inserción o reemplazo de tabla como parámetros. Para actualizar solo parte de una entidad, en su lugar, se puede usar el método **TableOperation.insertOrMerge**. Tenga en cuenta que la inserción o el reemplazo no son compatibles con el emulador de almacenamiento local, por lo que este código solo se ejecuta cuando se utiliza una cuenta del servicio Tabla. Puede obtener más información acerca de la inserción o reemplazo y la inserción o fusión en [Tablas de Azure: Introducción a Upsert y proyección de consultas][].

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the table client.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

        // Create a cloud table object for the table.
        CloudTable cloudTable = tableClient.getTableReference("people");

        // Create a new customer entity.
        CustomerEntity customer5 = new CustomerEntity("Harp", "Walter");
        customer5.setEmail("Walter@contoso.com");
        customer5.setPhoneNumber("425-555-0106");

        // Create an operation to add the new customer to the people table.
        TableOperation insertCustomer5 = TableOperation.insertOrReplace(customer5);

        // Submit the operation to the table service.
        cloudTable.execute(insertCustomer5);
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## Cómo eliminar una entidad

Puede eliminar fácilmente una entidad después de que la haya recuperado. Cuando se recupere la entidad, llame a **TableOperation.delete** con la entidad que se eliminará. A continuación, llame a **execute** en el objeto **CloudTable**. El código siguiente recupera y elimina una entidad de cliente.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the table client.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

        // Create a cloud table object for the table.
        CloudTable cloudTable = tableClient.getTableReference("people");

        // Create an operation to retrieve the entity with partition key of "Smith" and row key of "Jeff".
        TableOperation retrieveSmithJeff = TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

        // Retrieve the entity with partition key of "Smith" and row key of "Jeff".
        CustomerEntity entitySmithJeff =
            cloudTable.execute(retrieveSmithJeff).getResultAsType();

        // Create an operation to delete the entity.
        TableOperation deleteSmithJeff = TableOperation.delete(entitySmithJeff);

        // Submit the delete operation to the table service.
        cloudTable.execute(deleteSmithJeff);
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## Cómo eliminar una tabla

Finalmente, el código siguiente elimina una tabla de una cuenta de almacenamiento. Las tablas eliminadas no podrán volver a crearse durante un tiempo tras la eliminación, que normalmente suele ser de menos de 40 segundos.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the table client.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

        // Delete the table and all its data if it exists.
        CloudTable cloudTable = new CloudTable("people",tableClient);
        cloudTable.deleteIfExists();
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## Pasos siguientes

Ahora que está familiarizado con los aspectos básicos del almacenamiento de tablas, siga estos vínculos para obtener más información acerca de cómo realizar tareas de almacenamiento más complejas.

- [SDK de almacenamiento de Azure para Java][]
- [Referencia del SDK de cliente de almacenamiento de Azure][]
- [API de REST de almacenamiento de Azure][]
- [Blog del equipo de almacenamiento de Azure][]

Para obtener más información, consulte también el [Centro para desarrolladores de Java](/develop/java/).


[Azure SDK for Java]: http://go.microsoft.com/fwlink/?LinkID=525671
[SDK de almacenamiento de Azure para Java]: https://github.com/azure/azure-storage-java
[SDK de almacenamiento de Azure para Android]: https://github.com/azure/azure-storage-android
[Referencia del SDK de cliente de almacenamiento de Azure]: http://dl.windowsazure.com/storage/javadoc/
[API de REST de almacenamiento de Azure]: https://msdn.microsoft.com/library/azure/dd179355.aspx
[Blog del equipo de almacenamiento de Azure]: http://blogs.msdn.com/b/windowsazurestorage/
[Tablas de Azure: Introducción a Upsert y proyección de consultas]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx
[Tablas de Azure: Introducción de Upsert y proyección de consultas]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx

<!---HONumber=AcomDC_0302_2016-->