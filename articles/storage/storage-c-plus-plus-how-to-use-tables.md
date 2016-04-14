<properties
    pageTitle="Uso del almacenamiento de tablas (C++) | Microsoft Azure"
    description="Aprenda a usar el servicio de almacenamiento de tablas en Azure. Los ejemplos están escritos en C++."
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

# Uso de almacenamiento de tablas desde C++

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]

## Información general  
Esta guía muestra cómo realizar algunas tareas comunes a través del servicio de almacenamiento de tablas de Azure. Los ejemplos están escritos en C++ y usan la [biblioteca de cliente de almacenamiento de Azure para C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md). Entre los escenarios descritos se incluyen **crear y eliminar una tabla**, así como **trabajar con entidades de tabla**.

>[AZURE.NOTE] Esta guía se destina a la biblioteca de cliente de almacenamiento de Azure para C++, versión 1.0.0 y posteriores. La versión recomendada es la biblioteca de cliente de almacenamiento 2.2.0, que se encuentra disponible a través de [NuGet](http://www.nuget.org/packages/wastorage) o [GitHub](https://github.com/Azure/azure-storage-cpp/).

[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]
[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]


## Creación de una aplicación de C++  
En esta guía, usará las características de almacenamiento que se pueden ejecutar en una aplicación C++. Para ello, deberá instalar la biblioteca de cliente de almacenamiento de Azure para C++ y crear una cuenta de almacenamiento de Azure en su suscripción de Azure.

Para instalar la biblioteca de cliente de almacenamiento de Azure para C++, puede usar los métodos siguientes:

-	**Linux:** siga las instrucciones indicadas en la página [Léame de la biblioteca de cliente de almacenamiento de Azure para C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md).  
-	**Windows:** en Visual Studio, haga clic en **Herramientas > Administrador de paquetes de NuGet > Consola del Administrador de paquetes**. Escriba el siguiente comando en la [Consola del Administrador de paquetes de NuGet](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) y presione Entrar.  

		Install-Package wastorage

## Configuración de la aplicación para acceder al almacenamiento de tablas  
Agregue las siguientes instrucciones include en la parte superior del archivo C++ en el que desea usar las API de almacenamiento de Azure para obtener acceso a las tablas:

	#include "was/storage_account.h"
	#include "was/table.h"

## Configuración de una cadena de conexión de Almacenamiento de Azure  
Un cliente de almacenamiento de Azure utiliza una cadena de conexión de almacenamiento para almacenar extremos y credenciales con el fin de obtener acceso a los servicios de administración de datos. Cuando se ejecuta una aplicación cliente, debe proporcionar la cadena de conexión de almacenamiento en el formato siguiente. Use el nombre de la cuenta de almacenamiento y la clave de acceso de almacenamiento para la cuenta de almacenamiento que aparece en el [Portal de Azure](https://portal.azure.com) para los valores *AccountName* y *AccountKey*. Para obtener información sobre las cuentas de almacenamiento y las claves de acceso, consulte [Acerca de las cuentas de almacenamiento de Azure](storage-create-storage-account.md). En este ejemplo se muestra cómo puede declarar un campo estático para mantener la cadena de conexión:

	// Define the connection string with your values.
	const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));

Para probar la aplicación en el equipo local de Windows, puede usar el [emulador de almacenamiento](storage-use-emulator.md) de Azure que se instala con el [SDK de Azure](https://azure.microsoft.com/downloads/). El emulador de almacenamiento es una utilidad que simula los servicios Blob, Cola y Tabla de Azure en el equipo de desarrollo local. En el ejemplo siguiente se muestra cómo puede declarar un campo estático para mantener la cadena de conexión en el emulador de almacenamiento local:

	// Define the connection string with Azure storage emulator.
	const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  

Para iniciar el emulador de almacenamiento de Azure, haga clic en el botón **Inicio** o pulse la tecla Windows. Comience a escribir **Emulador de almacenamiento de Azure** y seleccione **Emulador de almacenamiento de Microsoft Azure** en la lista de aplicaciones.

En los ejemplos siguientes se supone que usó uno de estos dos métodos para obtener la cadena de conexión de almacenamiento.

## Recuperación de la cadena de conexión  
Puede usar la clase **cloud\_storage\_account** para representar la información de la cuenta de almacenamiento. Para recuperar la información de la cuenta de almacenamiento a partir de la cadena de conexión de almacenamiento, puede usar el método parse.

	// Retrieve the storage account from the connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

A continuación, obtenga una referencia a una clase **cloud\_table\_client**, ya que permite obtener objetos de referencia para las tablas y las entidades almacenadas en el servicio de almacenamiento de tabla. El código siguiente crea un objeto **cloud\_table\_client** usando el objeto de cuenta de almacenamiento recuperado anteriormente:

	// Create the table client.
	azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

## Creación de una tabla
Los objetos **cloud\_table\_client** le permiten obtener objetos de referencia para tablas y entidades. El código siguiente crea un objeto **cloud\_table\_client** y lo usa para crear una nueva tabla.

	// Retrieve the storage account from the connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);  

	// Create the table client.
	azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

	// Retrieve a reference to a table.
	azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

	// Create the table if it doesn't exist.
	table.create_if_not_exists();  

## Adición de una entidad a una tabla
Para agregar una entidad a una tabla, cree un nuevo objeto **table\_entity** y páselo a **table\_operation::insert\_entity**. El código siguiente usa el nombre de pila del cliente como clave de fila y el apellido como clave de partición. En conjunto, la clave de partición y la clave de fila de una entidad la identifican inequívocamente en la tabla. Las entidades con la misma clave de partición pueden consultarse más rápidamente que aquellas con diferentes claves de partición, pero el uso de claves de partición diversas permite una mayor escalabilidad de las operaciones paralelas. Para obtener más información, consulte [Lista de comprobación de rendimiento y escalabilidad de Almacenamiento de Microsoft Azure](storage-performance-checklist.md).

El código siguiente crea una instancia nueva de **table\_entity** con algunos datos de clientes que se van a almacenar. El código siguiente llama a **table\_operation::insert\_entity** para crear un objeto **table\_operation** a fin de insertar una entidad en una tabla y le asocia la nueva entidad de tabla. Por último, el código llama al método execute en el objeto **cloud\_table**. La nueva **table\_operation** envía una solicitud al servicio Tabla para insertar la nueva entidad de cliente en la tabla "people".

	// Retrieve the storage account from the connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the table client.
	azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

	// Retrieve a reference to a table.
	azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

	// Create the table if it doesn't exist.
	table.create_if_not_exists();

	// Create a new customer entity.
	azure::storage::table_entity customer1(U("Harp"), U("Walter"));

	azure::storage::table_entity::properties_type& properties = customer1.properties();
	properties.reserve(2);
	properties[U("Email")] = azure::storage::entity_property(U("Walter@contoso.com"));

	properties[U("Phone")] = azure::storage::entity_property(U("425-555-0101"));

	// Create the table operation that inserts the customer entity.
	azure::storage::table_operation insert_operation = azure::storage::table_operation::insert_entity(customer1);

	// Execute the insert operation.
	azure::storage::table_result insert_result = table.execute(insert_operation);

## Inserción de un lote de entidades
Puede insertar un lote de entidades en el servicio Tabla mediante una operación de escritura. El código siguiente crea un objeto **table\_batch\_operation** y, luego, le agrega tres operaciones de inserción. Cada operación de inserción se agrega al crear un nuevo objeto de entidad, configurar sus valores y, a continuación, llamar al método insert en el objeto **table\_batch\_operation** para asociar la entidad a una nueva operación de inserción. A continuación, se llama a **cloud\_table.execute** para ejecutar la operación.

	// Retrieve the storage account from the connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the table client.
	azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

	// Create a cloud table object for the table.
	azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

	// Define a batch operation.
	azure::storage::table_batch_operation batch_operation;

	// Create a customer entity and add it to the table.
	azure::storage::table_entity customer1(U("Smith"), U("Jeff"));

	azure::storage::table_entity::properties_type& properties1 = customer1.properties();
	properties1.reserve(2);
	properties1[U("Email")] = azure::storage::entity_property(U("Jeff@contoso.com"));
	properties1[U("Phone")] = azure::storage::entity_property(U("425-555-0104"));

	// Create another customer entity and add it to the table.
	azure::storage::table_entity customer2(U("Smith"), U("Ben"));

	azure::storage::table_entity::properties_type& properties2 = customer2.properties();
	properties2.reserve(2);
	properties2[U("Email")] = azure::storage::entity_property(U("Ben@contoso.com"));
	properties2[U("Phone")] = azure::storage::entity_property(U("425-555-0102"));

	// Create a third customer entity to add to the table.
	azure::storage::table_entity customer3(U("Smith"), U("Denise"));

	azure::storage::table_entity::properties_type& properties3 = customer3.properties();
	properties3.reserve(2);
	properties3[U("Email")] = azure::storage::entity_property(U("Denise@contoso.com"));
	properties3[U("Phone")] = azure::storage::entity_property(U("425-555-0103"));

	// Add customer entities to the batch insert operation.
	batch_operation.insert_or_replace_entity(customer1);
	batch_operation.insert_or_replace_entity(customer2);
	batch_operation.insert_or_replace_entity(customer3);

	// Execute the batch operation.
	std::vector<azure::storage::table_result> results = table.execute_batch(batch_operation);

Algunos aspectos que cabe tener en cuenta acerca de las operaciones por lotes:

-	Puede ejecutar cualquier combinación de 100 operaciones de inserción, eliminación, combinación, reemplazo, inserción o combinación e inserción o reemplazo en un único lote.  
-	Una operación por lotes puede tener una operación de recuperación, si es que es la única operación del lote.  
-	Todas las entidades de la misma operación por lotes deben compartir la misma clave de partición.  
-	Una operación por lotes se limita a una carga de datos de 4 MB.  

## todas las entidades de una partición
Para consultar una tabla a fin de obtener todas las entidades de una partición, use un objeto **table\_query**. El ejemplo de código siguiente especifica un filtro para las entidades en las que “Smith” es la clave de partición. En este ejemplo, los campos de cada entidad se imprimen en la consola, como parte de los resultados de la consulta.

	// Retrieve the storage account from the connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the table client.
	azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

	// Create a cloud table object for the table.
	azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

	// Construct the query operation for all customer entities where PartitionKey="Smith".
	azure::storage::table_query query;

	query.set_filter_string(azure::storage::table_query::generate_filter_condition(U("PartitionKey"), azure::storage::query_comparison_operator::equal, U("Smith")));

	// Execute the query.
	azure::storage::table_query_iterator it = table.execute_query(query);

	// Print the fields for each customer.
	azure::storage::table_query_iterator end_of_results;
	for (; it != end_of_results; ++it)
	{
		const azure::storage::table_entity::properties_type& properties = it->properties();

		std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key()
			<< U(", Property1: ") << properties.at(U("Email")).string_value()
			<< U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;
	}  

La consulta de este ejemplo recupera todas las entidades que coinciden con los criterios de filtro. Si tiene tablas grandes y necesita descargar las entidades de tabla con frecuencia, se recomienda almacenar los datos en blobs de Almacenamiento de Azure en su lugar.

## Recuperación de un rango de entidades de una partición
Si no desea consultar todas las entidades de una partición, puede especificar un rango combinando el filtro de clave de partición con un filtro de clave de fila. El ejemplo de código siguiente utiliza dos filtros para obtener todas las entidades de la partición “Smith” en las que la clave de fila (nombre de pila) empieza por una letra anterior a “E” en el alfabeto y, a continuación, imprime los resultados de la consulta.

	// Retrieve the storage account from the connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the table client.
	azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

	// Create a cloud table object for the table.
	azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

	// Create the table query.
	azure::storage::table_query query;

	query.set_filter_string(azure::storage::table_query::combine_filter_conditions(
		azure::storage::table_query::generate_filter_condition(U("PartitionKey"),
		azure::storage::query_comparison_operator::equal, U("Smith")),
		azure::storage::query_logical_operator::op_and,
		azure::storage::table_query::generate_filter_condition(U("RowKey"), azure::storage::query_comparison_operator::less_than, U("E"))));

	// Execute the query.
	azure::storage::table_query_iterator it = table.execute_query(query);

	// Loop through the results, displaying information about the entity.
	azure::storage::table_query_iterator end_of_results;
	for (; it != end_of_results; ++it)
	{
		const azure::storage::table_entity::properties_type& properties = it->properties();

		std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key()
			<< U(", Property1: ") << properties.at(U("Email")).string_value()
			<< U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;
	}  

## Recuperación de una sola entidad
Puede enviar una consulta para recuperar una sola entidad concreta. El código siguiente usa un objeto **table\_operation::retrive\_entity** para especificar el cliente "Jeff Smith". Este método devuelve una sola entidad, en lugar de una colección, y el valor devuelto está en **table\_result**. La forma más rápida de recuperar una sola entidad del servicio Tabla es especificar claves tanto de partición como de fila en las consultas.

	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the table client.
	azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

	// Create a cloud table object for the table.
	azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

	// Retrieve the entity with partition key of "Smith" and row key of "Jeff".
	azure::storage::table_operation retrieve_operation = azure::storage::table_operation::retrieve_entity(U("Smith"), U("Jeff"));
	azure::storage::table_result retrieve_result = table.execute(retrieve_operation);

	// Output the entity.
	azure::storage::table_entity entity = retrieve_result.entity();
	const azure::storage::table_entity::properties_type& properties = entity.properties();

	std::wcout << U("PartitionKey: ") << entity.partition_key() << U(", RowKey: ") << entity.row_key()
		<< U(", Property1: ") << properties.at(U("Email")).string_value()
		<< U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;

## una entidad
Para reemplazar una entidad, recupérela del servicio Tabla, modifique su objeto y, luego, guarde los cambios de nuevo en el servicio Tabla. El código siguiente cambia el número de teléfono y la dirección de correo electrónico de un cliente. En lugar de llamar a **table\_operation::insert\_entity**, este código usa **table\_operation::replace\_entity**. Esto hace que la entidad se reemplace por completo en el servidor, a menos que la entidad del servidor cambiara desde que se recuperó, en cuyo caso la operación dará error. Este error evita que la aplicación sobrescriba accidentalmente un cambio realizado entre la recuperación y la actualización por otro componente de la misma. La manera correcta de actuar ante este error es recuperar de nuevo la entidad, hacer los cambios oportunos (si siguen siendo válidos) y, a continuación, realizar otra operación **table\_operation::replace\_entity**. En la siguiente sección se muestra cómo invalidar este comportamiento.

	// Retrieve the storage account from the connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the table client.
	azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

	// Create a cloud table object for the table.
	azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

	// Replace an entity.
	azure::storage::table_entity entity_to_replace(U("Smith"), U("Jeff"));
	azure::storage::table_entity::properties_type& properties_to_replace = entity_to_replace.properties();
	properties_to_replace.reserve(2);

	// Specify a new phone number.
	properties_to_replace[U("Phone")] = azure::storage::entity_property(U("425-555-0106"));

	// Specify a new email address.
	properties_to_replace[U("Email")] = azure::storage::entity_property(U("JeffS@contoso.com"));

	// Create an operation to replace the entity.
	azure::storage::table_operation replace_operation = azure::storage::table_operation::replace_entity(entity_to_replace);

	// Submit the operation to the Table service.
	azure::storage::table_result replace_result = table.execute(replace_operation);

## Inserción o reemplazo de una entidad
Las operaciones **table\_operation::replace\_entity** darán error si la entidad se cambió desde su recuperación del servidor. Asimismo, primero debe recuperar la entidad del servidor para que la operación **table\_operation::replace\_entity** se realice correctamente. Sin embargo, en ocasiones no sabe si la entidad existe en el servidor y los valores actuales almacenados en él son irrelevantes, ya que la actualización debe sobrescribirlos todos. Para realizarlo, debe usar una operación **table\_operation::insert\_or\_replace\_entity**. Esta operación inserta la entidad si no existe o la reemplaza si ya existe, con independencia del momento en que se realizó la última actualización. En el ejemplo de código siguiente, también se recupera la entidad de cliente Jeff Smith pero, luego, se guarda de nuevo en el servidor mediante **table\_operation::insert\_or\_replace\_entity**. Las actualizaciones realizadas en la entidad entre la operación de recuperación y actualización se sobrescribirán.

	// Retrieve the storage account from the connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the table client.
	azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

	// Create a cloud table object for the table.
	azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

	// Insert-or-replace an entity.
	azure::storage::table_entity entity_to_insert_or_replace(U("Smith"), U("Jeff"));
	azure::storage::table_entity::properties_type& properties_to_insert_or_replace = entity_to_insert_or_replace.properties();

	properties_to_insert_or_replace.reserve(2);

	// Specify a phone number.
	properties_to_insert_or_replace[U("Phone")] = azure::storage::entity_property(U("425-555-0107"));

	// Specify an email address.
	properties_to_insert_or_replace[U("Email")] = azure::storage::entity_property(U("Jeffsm@contoso.com"));

	// Create an operation to insert-or-replace the entity.
	azure::storage::table_operation insert_or_replace_operation = azure::storage::table_operation::insert_or_replace_entity(entity_to_insert_or_replace);

	// Submit the operation to the Table service.
	azure::storage::table_result insert_or_replace_result = table.execute(insert_or_replace_operation);

## Consulta de un subconjunto de propiedades de las entidades  
Una consulta de tabla puede recuperar solo algunas propiedades de una entidad. La consulta del código siguiente usa el método **table\_query::set\_select\_columns** para devolver solo las direcciones de correo electrónico de las entidades de la tabla.

	// Retrieve the storage account from the connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the table client.
	azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

	// Create a cloud table object for the table.
	azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

	// Define the query, and select only the Email property.
	azure::storage::table_query query;
	std::vector<utility::string_t> columns;

	columns.push_back(U("Email"));
	query.set_select_columns(columns);

	// Execute the query.
	azure::storage::table_query_iterator it = table.execute_query(query);

	// Display the results.
	azure::storage::table_query_iterator end_of_results;
	for (; it != end_of_results; ++it)
	{
		std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key();

		const azure::storage::table_entity::properties_type& properties = it->properties();
		for (auto prop_it = properties.begin(); prop_it != properties.end(); ++prop_it)
		{
			std::wcout << ", " << prop_it->first << ": " << prop_it->second.str();
		}

		std::wcout << std::endl;
	}

>[AZURE.NOTE] Consultar una serie de propiedades de una entidad es una operación más eficaz que recuperar todas las propiedades.

## Eliminación de una entidad
Puede eliminar fácilmente una entidad después de que la haya recuperado. Cuando se recupere la entidad, llame a **table\_operation::delete\_entity** con la entidad que se eliminará. A continuación, llame al método **cloud\_table.execute**. El código siguiente recupera y elimina una entidad con la clave de partición "Smith" y la clave de fila "Jeff".

	// Retrieve the storage account from the connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the table client.
	azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

	// Create a cloud table object for the table.
	azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

	// Create an operation to retrieve the entity with partition key of "Smith" and row key of "Jeff".
	azure::storage::table_operation retrieve_operation = azure::storage::table_operation::retrieve_entity(U("Smith"), U("Jeff"));
	azure::storage::table_result retrieve_result = table.execute(retrieve_operation);

	// Create an operation to delete the entity.
	azure::storage::table_operation delete_operation = azure::storage::table_operation::delete_entity(retrieve_result.entity());

	// Submit the delete operation to the Table service.
	azure::storage::table_result delete_result = table.execute(delete_operation);  

## Eliminación de una tabla
Finalmente, el ejemplo de código siguiente elimina una tabla de una cuenta de almacenamiento. Una tabla que se ha eliminada no podrá volver a crearse durante un tiempo tras la eliminación.

	// Retrieve the storage account from the connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the table client.
	azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

	// Create a cloud table object for the table.
	azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

	// Create an operation to retrieve the entity with partition key of "Smith" and row key of "Jeff".
	azure::storage::table_operation retrieve_operation = azure::storage::table_operation::retrieve_entity(U("Smith"), U("Jeff"));
	azure::storage::table_result retrieve_result = table.execute(retrieve_operation);

	// Create an operation to delete the entity.
	azure::storage::table_operation delete_operation = azure::storage::table_operation::delete_entity(retrieve_result.entity());

	// Submit the delete operation to the Table service.
	azure::storage::table_result delete_result = table.execute(delete_operation);

## Pasos siguientes
Ahora que está familiarizado con los aspectos básicos del almacenamiento de tablas, siga estos vínculos para obtener más información sobre Almacenamiento de Azure:

-	[Uso del almacenamiento de blobs en C++](storage-c-plus-plus-how-to-use-blobs.md)
-	[Uso del almacenamiento de colas en C++](storage-c-plus-plus-how-to-use-queues.md)
-	[Enumeración de los recursos de Almacenamiento de Azure en C++](storage-c-plus-plus-enumeration.md)
-	[Referencia de la biblioteca de clientes de almacenamiento para C++](http://azure.github.io/azure-storage-cpp)
-	[Documentación de Almacenamiento de Azure](https://azure.microsoft.com/documentation/services/storage/)

<!----HONumber=AcomDC_0224_2016-->