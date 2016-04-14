<properties 
	pageTitle="Administración de la simultaneidad en Almacenamiento de Microsoft Azure"
	description="Administración de la simultaneidad para los servicios BLOB, Cola, Tabla y Archivo"
	services="storage"
	documentationCenter=""
	authors="jasonnewyork"
	manager="tadb"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="02/20/2016"
	ms.author="jahogg"/>

# Administración de la simultaneidad en Almacenamiento de Microsoft Azure

## Información general

Las aplicaciones modernas basadas en Internet, normalmente tienen varios usuarios que ven y actualizan datos simultáneamente. Esto requiere que los desarrolladores de las aplicaciones piensen detenidamente cómo proporcionar una experiencia predecible a sus usuarios finales, especialmente para escenarios donde varios usuarios pueden actualizar los mismos datos. Hay tres estrategias de simultaneidad de datos principales que normalmente tendrán en cuenta los desarrolladores:


1.	Simultaneidad optimista: una aplicación que realiza una actualización comprobará, como parte de dicha actualización, si los datos han cambiado desde que la aplicación leyera por última vez esos datos. Por ejemplo, si dos usuarios que ven una página wiki realizan una actualización en la misma página, entonces la plataforma wiki debe asegurarse de que la segunda actualización no sobrescribe la primera y que ambos usuarios comprenden si sus actualizaciones se realizaron correctamente o no. Esta estrategia se usa con más frecuencia en aplicaciones web.
2.	Simultaneidad pesimista: una aplicación que pretende realizar una actualización realizará un bloqueo en un objeto evitando que otros usuarios actualicen los datos hasta que el bloqueo se libere. Por ejemplo, en un escenario de replicación de datos maestro/esclavo, donde solamente el maestro realiza actualizaciones, el maestro normalmente mantendrá un bloqueo exclusivo durante un período de tiempo extendido en los datos para garantizar que ninguna otra persona puede actualizarlos.
3.	El último en escribir gana: enfoque que permite que cualquier operación de actualización se lleve a cabo sin comprobar si ninguna otra aplicación ha actualizado los datos desde la primera vez que la aplicación leyó los datos. Esta estrategia (o ausencia de una estrategia formal) normalmente se usa donde los datos están particionados de tal forma que no existe probabilidad de que varios usuarios obtengan acceso a los mismos datos. También puede resultar útil donde se procesen transmisiones de datos de corta duración.  

Este artículo proporciona información general de cómo la plataforma Almacenamiento de Azure simplifica el desarrollo proporcionando soporte de primera clase para estas tres estrategias de simultaneidad.

## Almacenamiento de Azure: simplificación del desarrollo en la nube
El servicio Almacenamiento de Azure, admite las tres estrategias, aunque es diferente en su capacidad de proporcionar soporte completo para simultaneidad optimista y pesimista porque se diseñó para adoptar un modelo de coherencia fuerte que garantice que cuando el servicio Almacenamiento confirme una operación de inserción o actualización de datos, todos los demás accesos a esos datos vean la última actualización. Las plataformas de almacenamiento que usan un modelo de coherencia eventual tienen un retardo entre el momento en el que un usuario lleva a cabo una escritura y el momento en el que los datos actualizados pueden ser vistos por otros usuarios, lo que complica el desarrollo de aplicaciones cliente para evitar que las incoherencias afecten a los usuarios finales.

Además de seleccionar una estrategia de simultaneidad apropiada, los desarrolladores también deben saber cómo una plataforma de almacenamiento aísla los cambios, especialmente los cambios en el mismo objeto a través de transacciones. El servicio de almacenamiento de Azure usa aislamiento de instantáneas para permitir que las operaciones de lectura tengan lugar simultáneamente con operaciones de escritura dentro de una sola partición. A diferencia de otros niveles de aislamiento, el aislamiento de instantánea garantiza que todas las lecturas ven una instantánea coherente de datos incluso mientras tienen lugar las actualizaciones, básicamente devolviendo los últimos valores confirmados mientras una transacción de actualización se procesa.

## Administrar la simultaneidad en almacenamiento de blobs
Puede optar por usar modelos de simultaneidad optimista o pesimista para administrar el acceso a blobs y contenedores del servicio BLOB. Si no especifica explícitamente una estrategia, la estrategia El último que escribe gana será la predeterminada.

### Simultaneidad optimista para blobs y contenedores  

El servicio Almacenamiento asigna un identificador a cada objeto almacenado. Este identificador se actualiza cada vez que se realiza una operación de actualización en un objeto. El identificador se devuelve al cliente como parte de una respuesta HTTP GET usando el encabezado ETag (etiqueta de entidad) que se define dentro del protocolo HTTP. Un usuario que realiza una actualización en tal objeto puede enviar la etiqueta ETag original junto con un encabezado condicional para garantizar que una actualización solamente tendrá lugar si se ha cumplido una determinada condición; en este caso, la condición es un encabezado “If-Match”, que requiere el servicio Almacenamiento para garantizar que el valor de ETag especificado en la solicitud de actualización es el mismo que el almacenado en el servicio Almacenamiento.

El esquema de este proceso es el siguiente:

1.	Recuperar un blob del servicio Almacenamiento. La respuesta incluye un valor de encabezado ETag HTTP que identifica la versión actual del objeto en el servicio Almacenamiento.
2.	Cuando actualice el blob, incluya el valor ETag recibido en el paso 1 en el encabezado condicional **If-Match** de la solicitud que envía al servicio.
3.	El servicio compara el valor ETag de la solicitud con el valor ETag actual del blob.
4.	Si el valor ETag actual del blob es una versión diferente de ETag del encabezado condicional **If-Match** de la solicitud, el servicio devuelve un error 412 al cliente. Esto indica al cliente que otro proceso ha actualizado el blob desde que el cliente lo recuperó.
5.	Si el valor ETag actual del blob es la misma versión que el valor ETag del encabezado condicional **If-Match** de la solicitud, el servicio realiza la operación solicitada y actualiza el valor ETag actual del blob para mostrar que ha creado una nueva versión.  

El siguiente fragmento de código de C# (usando Biblioteca de almacenamiento de cliente 4.2.0) muestra un ejemplo sencillo de cómo construir una **If-Match AccessCondition** basándose en el valor ETag al que se tiene acceso desde las propiedades de un blob que se recuperó o insertó previamente. Luego usa el objeto **AccessCondition** cuando actualiza el blob: el objeto **AccessCondition** agrega el encabezado **If-Match** a la solicitud. Si otro proceso ha actualizado el blob, el servicio BLOB devuelve un mensaje de estado HTTP 412 (Error en la condición previa). Puede descargar aquí el ejemplo completo: [Administración de simultaneidad con Almacenamiento de Azure](http://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

	// Retrieve the ETag from the newly created blob
	// Etag is already populated as UploadText should cause a PUT Blob call
	// to storage blob service which returns the etag in response.
	string orignalETag = blockBlob.Properties.ETag;

	// This code simulates an update by a third party.
	string helloText = "Blob updated by a third party.";

	// No etag, provided so orignal blob is overwritten (thus generating a new etag)
	blockBlob.UploadText(helloText);
	Console.WriteLine("Blob updated. Updated ETag = {0}",
	blockBlob.Properties.ETag);

	// Now try to update the blob using the orignal ETag provided when the blob was created
	try
	{
	    Console.WriteLine("Trying to update blob using orignal etag to generate if-match access condition");
	    blockBlob.UploadText(helloText,accessCondition:
	    AccessCondition.GenerateIfMatchCondition(orignalETag));
	}
	catch (StorageException ex)
	{
	    if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
	    {
	        Console.WriteLine("Precondition failure as expected. Blob's orignal etag no longer matches");
	        // TODO: client can decide on how it wants to handle the 3rd party updated content.
	    }
	    else
	        throw;
	}  

El servicio Almacenamiento también incluye soporte para encabezados condicionales adicionales como **If-Modified-Since**, **If-Unmodified-Since** y **If-None-Match**, además de combinaciones de los mismos. Para obtener más información, consulte [Especificar encabezados condicionales para las operaciones del servicio BLOB](http://msdn.microsoft.com/library/azure/dd179371.aspx) en MSDN.

En la siguiente tabla se resumen las operaciones de contenedor que aceptan encabezados condicionales como **If-Match** en la solicitud y que devuelven un valor ETag en la respuesta.

Operación |Devuelve el valor ETag del contenedor|	Acepta encabezados condicionales|
------------|-----------------------|------------------------------------|
Create Container|	Sí|	No|
Get Container Properties|	Sí|	No|
Get Container Metadata|	Sí|	No|
Set Container Metadata|	Sí|	Sí|
Get Container ACL|	Sí|	No|
Set Container ACL|	Sí|	Sí (*)|
Delete Container| No| Sí|
Lease Container| Sí| Sí|
List Blobs| No| No 

(*) Los permisos definidos por SetContainerACL se almacenan en caché y las actualizaciones a estos permisos tardan 30 segundos en propagarse durante los cuales no se garantiza la coherencia de las actualizaciones.

En la siguiente tabla, se resumen las operaciones de blob que aceptan encabezados condicionales como **If-Match** en la solicitud y que devuelven un valor ETag en la respuesta.

Operación |Devuelve el valor ETag |Acepta encabezados condicionales|
-----------|-------------------|----------------------------|
Put Blob|	Sí|	Sí|
Get Blob|	Sí|	Sí|
Get Blob Properties|	Sí|	Sí|
Set Blob Properties|	Sí|	Sí|
Get Blob Metadata|	Sí|	Sí|
Set Blob Metadata|	Sí|	Sí|
Lease Blob (*)| Sí| Sí|
Snapshot Blob| Sí| Sí|
Copy Blob| Sí| Sí (para blob de origen y de destino)|
Abort Copy Blob| No| No|
Delete Blob| No| Sí|
Put Block| No| No|
Put Block List| Sí| Sí|
Get Block List| Sí| No|
Put Page| Sí| Sí|
Get Page Ranges| Sí| Sí

(*) Lease Blob no cambia ETag en un blob.

### Simultaneidad pesimista para blobs
Para bloquear un blob para uso exclusivo, puede adquirir una [concesión](http://msdn.microsoft.com/library/azure/ee691972.aspx) en él. Cuando adquiera una concesión, especifique durante cuánto tiempo necesita la concesión: puede ser entre 15 y 60 segundos o de manera infinita, lo que corresponde a un bloqueo exclusivo. Puede renovar una concesión finita para extenderla y puede liberar cualquier concesión cuando haya terminado con ella. El servicio de blob libera automáticamente concesiones finitas cuando expiran.

Las concesiones permiten diferentes estrategias de sincronización, como por ejemplo las siguientes: escritura exclusiva / lectura compartida, escritura exclusiva / lectura exclusiva y escritura compartida / lectura exclusiva. Donde existe una concesión, el servicio Almacenamiento impone escrituras exclusivas (operaciones poner, establecer y eliminar) garantizando, sin embargo, que la exclusividad para operaciones de lectura requiere que el desarrollador garantice que todas las aplicaciones cliente usan un identificador de concesión y que solamente un cliente tiene un identificador de concesión válido en cada momento. Las operaciones de lectura que no incluyen un identificador de concesión, dan lugar a lecturas compartidas.

En el siguiente fragmento de código de C# se muestra un ejemplo de adquisición de una concesión exclusiva durante 30 segundos en un blob, actualizando el contenido de dicho blob y, a continuación, liberando la concesión. Si ya hay una concesión válida en el blob cuando intenta adquirir una nueva concesión, el servicio BLOB devuelve un resultado de estado “HTTP (409) Conflicto”. El fragmento de código siguiente usa un objeto **AccessCondition** para encapsular la información de concesión cuando crea una solicitud para actualizar el blob en el servicio Almacenamiento. Puede descargar aquí el ejemplo completo: [Administración de simultaneidad con Almacenamiento de Azure](http://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

	// Acquire lease for 15 seconds
	string lease = blockBlob.AcquireLease(TimeSpan.FromSeconds(15), null);
	Console.WriteLine("Blob lease acquired. Lease = {0}", lease);

	// Update blob using lease. This operation will succeed
	const string helloText = "Blob updated";
	var accessCondition = AccessCondition.GenerateLeaseCondition(lease);
	blockBlob.UploadText(helloText, accessCondition: accessCondition);
	Console.WriteLine("Blob updated using an exclusive lease");

	//Simulate third party update to blob without lease
	try
	{
	    // Below operation will fail as no valid lease provided
	    Console.WriteLine("Trying to update blob without valid lease");
	    blockBlob.UploadText("Update without lease, will fail");
	}
	catch (StorageException ex)
	{
	    if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
	        Console.WriteLine("Precondition failure as expected. Blob's lease does not match");
	    else
	        throw;
	}  

Si intenta una operación de escritura en un blob concedido sin pasar el identificador de concesión, la solicitud no se puede realizar y muestra el error 412. Tenga en cuenta que si la concesión espira antes de llamar al método **UploadText**, pero sigue pasando el identificador de concesión, la solicitud tampoco se podrá realizar y mostrará el error **412**. Para obtener más información acerca de la administración de tiempos de expiración de la concesión y de identificadores de concesión, vea la documentación REST [Lease Blob](http://msdn.microsoft.com/library/azure/ee691972.aspx).

Las siguientes operaciones de blob pueden usar concesiones para administrar simultaneidad pesimista:


-   Put Blob
-	Get Blob
-	Get Blob Properties
-	Set Blob Properties
-	Get Blob Metadata
-	Set Blob Metadata
-	Delete Blob
-	Put Block
-	Put Block List
-	Get Block List
-	Put Page
-	Get Page Ranges
-	Snapshot Blob (identificador de concesión opcional si existe una concesión)
-	Copy Blob (identificador de concesión necesario si existe una concesión en el blob de destino)
-	Abort Copy Blob (identificador de concesión necesario si existe una concesión infinita en el blob de destino)
-	Lease Blob  

### Simultaneidad pesimista para contenedores
Las concesiones en contenedores permiten las mismas estrategias de sincronización que en blob (escritura exclusiva / lectura compartida, escritura exclusiva / lectura exclusiva y escritura compartida / lectura exclusiva). Sin embargo, a diferencia de los blobs, el servicio Almacenamiento solamente impone exclusividad en operaciones de eliminación. Para eliminar un contenedor con una concesión activa, un cliente debe incluir el identificador de concesión activo con la solicitud de eliminación. El resto de operaciones de contenedor se realizan correctamente en un contenedor concedido sin incluir el identificador de concesión, en cuyo caso son operaciones compartidas. Si la exclusividad de la actualización (poner o establecer) o las operaciones de lectura son obligatorias, entonces los desarrolladores deben garantizar que todos los clientes usan un identificador de concesión y que solamente un cliente tiene un identificador de concesión válido en cada momento.

Las siguientes operaciones de contenedor pueden usar concesiones para administrar simultaneidad pesimista:

-	Delete Container
-	Get Container Properties
-	Get Container Metadata
-	Set Container Metadata
-	Get Container ACL
-	Set Container ACL
-	Lease Container  

Para obtener más información, consulte:

- [Especificar encabezados condicionales para las operaciones del servicio BLOB](http://msdn.microsoft.com/library/azure/dd179371.aspx)
- [Lease Container](http://msdn.microsoft.com/library/azure/jj159103.aspx)
- [Concesiones de blob ](http://msdn.microsoft.com/library/azure/ee691972.aspx)

## Administración de simultaneidad en el servicio Tabla
El servicio tabla usa comprobaciones de simultaneidad optimista como el comportamiento predeterminado cuando trabaja con entidades, a diferencia del servicio BLOB donde debe elegir explícitamente la realización de comprobaciones de simultaneidad optimista. La otra diferencia entre los servicios Tabla y BLOB es que solamente puede administrar el comportamiento de simultaneidad de entidades mientras que con el servicio BLOB puede administrar la simultaneidad tanto de contenedores como de blobs.

Para usar simultaneidad optimista y comprobar si otro proceso modificó una entidad desde que la recuperó del servicio de almacenamiento Tabla, puede usar el valor ETag que recibe cuando el servicio Tabla devuelve una entidad. El esquema de este proceso es el siguiente:

1.	Recuperar una entidad del servicio de almacenamiento Tabla. La respuesta incluye un valor ETag que muestra el identificador actual asociado con esa entidad en el servicio de almacenamiento.
2.	Cuando actualice la entidad, incluya el valor ETag recibido en el paso 1 en el encabezado **If-Match** obligatorio de la solicitud que envía al servicio.
3.	El servicio compara el valor ETag de la solicitud con el valor ETag actual de la entidad.
4.	Si el valor ETag actual de la entidad es diferente al valor ETag del encabezado **If-Match** obligatorio de la solicitud, el servicio devuelve un error 412 al cliente. Esto indica al cliente que otro proceso ha actualizado la entidad desde que el cliente la recuperó.
5.	Si el valor ETag actual de la entidad es el mismo que el valor ETag del encabezado **If-Match** obligatorio de la solicitud o el encabezado **If-Match** contiene el carácter comodín (*), el servicio realiza la operación solicitada y actualiza el valor ETAg actual de la entidad para mostrar que se ha actualizado.

Tenga en cuenta que, a diferencia del servicio BLOB, el servicio Tabla requiere que el cliente incluya un encabezado **If-Match** en las solicitudes de actualización. Sin embargo, es posible imponer una actualización no condicional (estrategia de tipo "El último en escribir gana") y omitir comprobaciones de simultaneidad si el cliente establece el encabezado **If-Match** en el carácter comodín (*) en la solicitud.

El siguiente fragmento de código C# muestra una entidad customer que se creó o recuperó previamente teniendo su dirección de correo electrónico actualizada. La operación de inserción o recuperación inicial almacena el valor ETag en el objeto customer y, dado que el ejemplo usa la misma instancia de objeto cuando ejecuta la operación de reemplazo, automáticamente vuelve a enviar el valor ETag al servicio Tabla, permitiendo al servicio comprobar las infracciones de simultaneidad. Si otro proceso ha actualizado la entidad en el almacenamiento de tabla, el servicio devuelve un mensaje de estado HTTP 412 (Error en la condición previa). Puede descargar aquí el ejemplo completo: [Administración de simultaneidad con Almacenamiento de Azure](http://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

	try
	{
	    customer.Email = "updatedEmail@contoso.org";
	    TableOperation replaceCustomer = TableOperation.Replace(customer);
	    customerTable.Execute(replaceCustomer);
	    Console.WriteLine("Replace operation succeeded.");
	}
	catch (StorageException ex)
	{
	    if (ex.RequestInformation.HttpStatusCode == 412)
	        Console.WriteLine("Optimistic concurrency violation – entity has changed since it was retrieved.");
	    else
	        throw;
	}  

Para deshabilitar explícitamente la comprobación de simultaneidad, debe establecer la propiedad **ETag** del objeto **employee** en “*” antes de ejecutar la operación de reemplazo.

customer.ETag = "*";

En la siguiente tabla se resume cómo las operaciones de entidad de tabla usan valores ETag:

Operación |Devuelve el valor ETag |Requiere encabezado de solicitud If-Match|
------------|-------------------|--------------------------------|
Query Entities|	Sí|	No|
Insert Entity|	Sí|	No|
Update Entity|	Sí|	Sí|
Merge Entity|	Sí|	Sí|
Delete Entity|	No|	Sí|
Insert or Replace Entity|	Sí|	No|
Insert or Merge Entity|	Sí|	No

Tenga en cuenta que las operaciones **Insert or Replace Entity** e **Insert or Merge Entity** *no* realizan ninguna comprobación de simultaneidad porque no envían un valor ETag al servicio Tabla.

En general, los desarrolladores que usan tablas deben basarse en simultaneidad optimista cuando desarrollan aplicaciones escalables. Si se necesita el bloqueo pesimista, un enfoque que los desarrolladores pueden usar cuando obtienen acceso a tablas es asignar un blob designado para cada tabla e intentar llevar a cabo una concesión en el blob antes de realizar operaciones en la tabla. Este enfoque no requiere que la aplicación garantice que todas las rutas de acceso a los datos obtienen la concesión antes de realizar operaciones en la tabla. También debe tener en cuenta que el tiempo mínimo de concesión es 15 segundos, lo que requiere una consideración cuidadosa para escalabilidad.

Para obtener más información, consulte:

- [Operaciones en entidades](http://msdn.microsoft.com/library/azure/dd179375.aspx)  

## Administración de simultaneidad en el servicio Cola
Un escenario en el que la simultaneidad es una preocupación en el servicio de colas es donde varios clientes recuperan mensajes de una cola. Cuando un mensaje se recupera de la cola, la respuesta incluye el mensaje y un valor de recepción de confirmación, que es necesario para eliminar dicho mensaje. El mensaje no se elimina automáticamente de la cola, pero después de haberse recuperado, no se muestra a otros clientes durante el intervalo de tiempo especificado por el parámetro visibilitytimeout. Se espera que el cliente que recupera el mensaje lo elimine una vez procesado y antes de que se cumpla el tiempo especificado por el elemento TimeNextVisible de la respuesta, que se calcula en función del valor del parámetro visibilitytimeout. El valor de visibilitytimeout se agrega al tiempo en el que el mensaje se recupera para determinar el valor de TimeNextVisible.

El servicio Cola no admite simultaneidad optimista o pesimista y, por esta razón, los clientes que procesan mensajes recuperados de una cola deben garantizar que los mensajes se procesan de una manera idempotente. Una estrategia El último que escribe gana se usa para actualizar operaciones como SetQueueServiceProperties, SetQueueMetaData, SetQueueACL y UpdateMessage.

Para obtener más información, consulte:

- [API de REST del servicio Cola](http://msdn.microsoft.com/library/azure/dd179363.aspx)
- [Get Messages](http://msdn.microsoft.com/library/azure/dd179474.aspx)  

## Administración de simultaneidad en el servicio Archivo
Se puede acceder al servicio Archivo utilizando dos extremos de protocolo diferentes: SMB y REST. El servicio REST no admite bloqueo optimista o bloqueo pesimista y todas las actualizaciones seguirán una estrategia de tipo El último en escribir gana. Los clientes SMB que montan recursos compartidos de archivo pueden aprovechar mecanismos de bloqueo del sistema de archivos para administrar el acceso a archivos compartidos, incluida la capacidad de realizar bloqueo pesimista. Cuando un cliente SMB abre un archivo, especifica tanto el acceso al archivo como el modo de uso compartido. El establecimiento de una opción Acceso de archivo en "Escritura" o "Lectura/Escritura" junto con un modo Uso compartido de archivos de "Ninguno" provocará el bloqueo del archivo por parte de un cliente hasta que el archivo se cierre. Si se intenta realizar la operación REST en un archivo donde un cliente SMB tiene el archivo bloqueado, el servicio REST devolverá el código de estado 409 (Conflicto) con el código de error SharingViolation.

Cuando un cliente SMB abre un archivo para eliminarlo, lo marca como eliminación pendiente hasta que el resto de identificadores de apertura del cliente SMB en ese archivo están cerrados. Mientras un archivo se marca como eliminación pendiente, cualquier operación REST en ese archivo devolverá el código de estado 409 (Conflicto) con el código de error SMBDeletePending. El código de estado 404 (No encontrado) no se devuelve porque es posible que el cliente SMB elimine la marca de eliminación pendiente antes de cerrar el archivo. En otras palabras, el código de estado 404 (No encontrado) solamente se espera cuando el archivo se ha quitado. Tenga en cuenta que mientras un archivo esté en estado de eliminación pendiente SMB, no se incluirá en los resultados de Enumerar archivos. Tenga en cuenta, además, que las operaciones REST Delete File y REST Delete Directory se confirman automáticamente y no dan como resultado un estado de eliminación pendiente.

Para obtener más información, consulte:

- [Administración de bloqueos de archivo](http://msdn.microsoft.com/library/azure/dn194265.aspx)  

## Resumen y pasos siguientes
El servicio Almacenamiento de Microsoft Azure se ha diseñado para satisfacer las necesidades de las aplicaciones en línea más complejas sin necesidad de que los desarrolladores comprometan o se replanteen sus asunciones de diseño clave, como simultaneidad y coherencia de datos, que han llegado a dar por sentado.

Aquí podrá obtener la aplicación de ejemplo completa a la que se hace referencia en este blog:

- [Administración de simultaneidad con Almacenamiento de Azure: aplicación de ejemplo](http://code.msdn.microsoft.com/Managing-Concurrency-using-56018114)  

Para obtener más información acerca de Almacenamiento de Azure, consulte:

- [Página principal de Almacenamiento de Microsoft Azure](https://azure.microsoft.com/services/storage/)
- [Introducción a Almacenamiento de Azure](storage-introduction.md)
- Introducción al almacenamiento para [Blob](storage-dotnet-how-to-use-blobs.md), [Tabla](storage-dotnet-how-to-use-tables.md), [Colas](storage-dotnet-how-to-use-queues.md) y [Archivos](storage-dotnet-how-to-use-files.md)
- Arquitectura de almacenamiento – [Almacenamiento de Azure: un servicio de almacenamiento en la nube altamente disponible con coherencia fuerte](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)

<!---HONumber=AcomDC_0224_2016-->