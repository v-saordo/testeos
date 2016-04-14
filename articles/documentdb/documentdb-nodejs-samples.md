<properties 
	pageTitle="Ejemplos de Node.js NoSQL para DocumentDB | Microsoft Azure" 
	description="Encontrará ejemplos de Node.js en github sobre tareas comunes en DocumentDB, por ejemplo, operaciones CRUD para documentos JSON en bases de datos NoSQL." 
	keywords="ejemplos de Node.js"
	services="documentdb" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter="nodejs"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/25/2016" 
	ms.author="mimig"/>


# Ejemplos de Node.js de DocumentDB

> [AZURE.SELECTOR]
- [Ejemplos de .NET](documentdb-dotnet-samples.md)
- [Ejemplos de Node.js](documentdb-nodejs-samples.md)
- [Galería de ejemplos de código de Azure](https://azure.microsoft.com/documentation/samples/?service=documentdb)

En el repositorio de GitHub [azure-documentdb-nodejs](https://github.com/Azure/azure-documentdb-node/tree/master/samples) se incluyen soluciones de ejemplo que realizan operaciones CRUD y otras operaciones comunes en recursos de DocumentDB. Este artículo ofrece:

- Vínculos a las tareas de cada uno de los archivos de proyecto de ejemplo de Node.js. 
- Vínculos al contenido de referencia de la API relacionada.

**Requisitos previos**

1. Para usar estos ejemplos de Node.js, necesita una cuenta de Azure:
    - Puede [abrir una cuenta de Azure de manera gratuita](https://azure.microsoft.com/pricing/free-trial/) - Obtiene crédito que puede usar para probar los servicios de Azure de pago, e incluso una vez agotado este, podrá mantener la cuenta y usar servicios gratuitos de Azure, como Sitios web. Nunca se la hará ningún cargo en la tarjeta de crédito, a menos que cambie explícitamente la configuración y pida que se le realice algún cargo.
   - Puede [activar las ventajas de suscriptor de Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/): su suscripción a Visual Studio le proporciona créditos todos los meses que puede usar con servicios de Azure de pago.
2. También necesita el [SDK de Node.js](documentdb-sdk-node.md). 

    > [AZURE.NOTE] Cada ejemplo es independiente, se configura por sí mismo y se limpia solo a continuación. Por lo tanto, los ejemplos emiten varias llamadas a [DocumentClient.createCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html). Cada vez que esto ocurre, se cobra a la suscripción una hora de uso de acuerdo con el nivel de rendimiento de la colección que se va a crear.

## Ejemplos de base de datos

El archivo [app.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/DatabaseManagement/app.js) del proyecto [DatabaseManagement](https://github.com/Azure/azure-documentdb-node/tree/master/samples/DatabaseManagement) muestra cómo realizar las tareas siguientes.

Tarea | Referencia de API
--- | ---
[Creación de una base de datos](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L121-L131) | [DocumentClient.createDatabase](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createDatabase)
[Consultar una base de datos de una cuenta](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L146-L171) | [DocumentClient.queryDatabases](http://azure.github.io/azure-documentdb-node/DocumentClient.html#queryDatabase)
[Leer una base de datos por identificador](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L89-L99) | [DocumentClient.readDatabase](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readDatabase)
[Enumerar bases de datos de una cuenta](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L111-L119) | [DocumentClient.readDatabases](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readDatabase)
[Eliminar una base de datos](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L133-L144) | [DocumentClient.deleteDatabase](http://azure.github.io/azure-documentdb-node/DocumentClient.html#deleteDatabase)

## Ejemplos de colección 

El archivo [app.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/CollectionManagement/app.js) del proyecto [CollectionManagement](https://github.com/Azure/azure-documentdb-node/tree/master/samples/CollectionManagement) muestra cómo realizar las tareas siguientes.

Tarea | Referencia de API
--- | ---
[Creación de una colección](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L97-L118) | [DocumentClient.createCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createCollection)
[Leer una lista de todas las colecciones en una base de datos](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L120-L130) | [DocumentClient.listCollections](http://azure.github.io/azure-documentdb-node/DocumentClient.html#listCollections)
[Obtener una colección por \_self](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L132-L141) | [DocumentClient.readCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readCollection)
[Obtener una colección por identificador](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L143-L156) | [DocumentClient.readCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readCollection)
[Obtener el nivel de rendimiento de una colección](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L158-L186) | [DocumentQueryable.queryOffers](http://azure.github.io/azure-documentdb-node/DocumentClient.html#queryOffers)
[Cambiar el nivel de rendimiento de una colección](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L188-L202) | [DocumentClient.replaceOffer](http://azure.github.io/azure-documentdb-node/DocumentClient.html#replaceOffer)
[Eliminar una colección](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L204-L215) | [DocumentClient.deleteCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#deleteCollection)

## Ejemplos de documento

El archivo [app.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/DocumentManagement/app.js) del proyecto [DocumentManagement](https://github.com/Azure/azure-documentdb-node/tree/master/samples/DocumentManagement) muestra cómo realizar las tareas siguientes.

Tarea | Referencia de API
--- | ---
[Creación de documentos](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L153-L177) | [DocumentClient.createDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createDocument)
[Leer la fuente de documentos de una colección](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L179-L189) | [DocumentClient.readDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readDocument)
[Leer un documento por identificador](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L191-L201) | [DocumentClient.readDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readDocument)
[Lectura de documento únicamente si ha cambiado](https://github.com/Azure/azure-documentdb-node/blob/0778eadea7abb2af41e8c22a239dc872c584f421/samples/DocumentManagement/app.js#L79-L107) | [DocumentClient.readDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readDocument)<br/>[RequestOptions.accessCondition](http://azure.github.io/azure-documentdb-node/global.html#RequestOptions)
[Consulta de documentos](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L82-L110) | [DocumentClient.queryDocuments](http://azure.github.io/azure-documentdb-node/DocumentClient.html#queryDocuments) 
[Sustitución de un documento](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L112-L119) | [DocumentClient.replaceDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#replaceDocument)
[Reemplazo de documento con la comprobación de ETag condicional](https://github.com/Azure/azure-documentdb-node/blob/0778eadea7abb2af41e8c22a239dc872c584f421/samples/DocumentManagement/app.js#L147-L164) | [DocumentClient.replaceDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#replaceDocument)<br/>[RequestOptions.accessCondition](http://azure.github.io/azure-documentdb-node/global.html#RequestOptions)
[Eliminar un documento](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L122-L133) | [DocumentClient.deleteDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#deleteDocument)

## Ejemplos de indización

El archivo [app.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/IndexManagement/app.js) del proyecto [IndexManagement](https://github.com/Azure/azure-documentdb-node/tree/master/samples/IndexManagement) muestra cómo realizar las tareas siguientes.

Tarea | Referencia de API
--- | ---
Crear una colección con indexación predeterminada | [DocumentClient.createDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html)
[Indexar manualmente un documento determinado](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L185-L238) | [indexingDirective: 'include'](http://azure.github.io/azure-documentdb-node/global.html#indexingDirective)
[Excluir manualmente un documento determinado del índice](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L120-L183) | [RequestOptions.indexingDirective](http://azure.github.io/azure-documentdb-node/global.html#RequestOptions)
[Usar la indexación diferida para la importación masiva o la lectura intensa de colecciones](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L240-L269) | [IndexingMode.Lazy](http://azure.github.io/azure-documentdb-node/global.html#indexingmode)
[Incluir rutas de acceso específicas de un documento en la indexación](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L433-L444) | [IndexingPolicy.IncludedPaths](http://azure.github.io/azure-documentdb-node/global.html#indexingpolicy) 
[Excluir determinadas rutas de acceso de la indexación](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L427-L450) | [ExcludedPath](http://azure.github.io/azure-documentdb-node/global.html#indexingpolicy)
[Permitir un análisis en una ruta de acceso de cadena durante una operación de intervalo](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L271-L347)| [ExcludedPath.EnableScanInQuery](http://azure.github.io/azure-documentdb-node/global.html#FeedOptions)
[Crear un índice de intervalo en una ruta de acceso de cadena](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L349-L425) | [DocumentClient.queryDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#queryDocument)
[Crear una colección con indexPolicy de forma predeterminada y luego actualizarla en línea](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L519-L614) | [DocumentClient.createCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createCollection)<br> [DocumentClient.replaceCollection#replaceCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html)

Para más información sobre la indexación, consulte [Directivas de indexación de DocumentDB](documentdb-indexing-policies.md).

## Ejemplos de programación en el servidor

El archivo [app.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/ServerSideScripts/app.js) del proyecto [ServerSideScripts](https://github.com/Azure/azure-documentdb-node/tree/master/samples/ServerSideScripts) muestra cómo realizar las tareas siguientes.

Tarea | Referencia de API
--- | ---
[Crear un procedimiento almacenado](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.ServerSideScripts/app.js#L44-L71) | [DocumentClient.createStoredProcedure](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createStoredProcedure)
[Ejecutar un procedimiento almacenado](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.ServerSideScripts/app.js#L73-L90) | [DocumentClient.executeStoredProcedure](http://azure.github.io/azure-documentdb-node/DocumentClient.html#executeStoredProcedure)

Para más información sobre la programación en el servidor, consulte [Programación en el servidor de DocumentDB: procedimientos almacenados, desencadenadores de base de datos y UDF](documentdb-programming.md).

## Ejemplos de creación de particiones

El archivo [app.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/Partitioning/app.js) del proyecto [Partitioning](https://github.com/Azure/azure-documentdb-node/tree/master/samples/Partitioning) muestra cómo realizar las tareas siguientes.

Tarea | Referencia de API
--- | ---
[Uso de HashPartitionResolver](https://github.com/Azure/azure-documentdb-node/blob/ce0fc3c4e70b0279091a1e03620a668d93a14fc2/samples/Partitioning/app.js#L53-L103) | [HashPartitionResolver](http://documentdb.com)

Para más información sobre la creación de particiones de datos en DocumentDB, consulte [Partición y escalado de datos en DocumentDB](documentdb-partition-data.md).

<!---HONumber=AcomDC_0302_2016-->