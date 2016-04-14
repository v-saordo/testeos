<properties 
	pageTitle="Ejemplo de NoSQL de .NET para DocumentDB | Microsoft Azure" 
	description="Encontrará ejemplos de NoSQL de C# .NET en github sobre tareas comunes en DocumentDB, por ejemplo, operaciones CRUD para documentos JSON en bases de datos NoSQL." 
	keywords="Ejemplo de NoSQL"
	services="documentdb" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter=".net"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/23/2016" 
	ms.author="mimig"/>


# Ejemplos de .NET de DocumentDB

> [AZURE.SELECTOR]
- [.NET Examples](documentdb-dotnet-samples.md)
- [Node.js Examples](documentdb-nodejs-samples.md)
- [Azure Code Sample Gallery](https://azure.microsoft.com/documentation/samples/?service=documentdb)

En el repositorio de GitHub [azure-documentdb-net](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples) se incluyen soluciones de ejemplo que realizan operaciones CRUD y otras operaciones comunes en recursos de DocumentDB. Este artículo ofrece:

- Vínculos a las tareas de cada uno de los archivos de proyecto de ejemplo de C#. 
- Vínculos al contenido de referencia de la API relacionada.

**Requisitos previos**

1. Para usar estos ejemplos de NoSQL, necesita una cuenta de Azure:
    - Puede [abrir una cuenta de Azure de manera gratuita](https://azure.microsoft.com/pricing/free-trial/) - Obtiene crédito que puede usar para probar los servicios de Azure de pago, e incluso una vez agotado este, podrá mantener la cuenta y usar servicios gratuitos de Azure, como Sitios web. Nunca se la hará ningún cargo en la tarjeta de crédito, a menos que cambie explícitamente la configuración y pida que se le realice algún cargo.
   - Puede [activar las ventajas de suscriptor de Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/): su suscripción a Visual Studio le proporciona créditos todos los meses que puede usar con servicios de Azure de pago.
2. También necesita el [paquete de NuGet Microsoft.Azure.DocumentDB](http://www.nuget.org/packages/Microsoft.Azure.DocumentDB/). 

> [AZURE.NOTE]
Cada ejemplo es independiente, es decir, se configura a sí mismo y posteriormente se limpia solo. Por tanto, los ejemplos emiten varias llamadas a CreateDocumentCollectionAsync(). Cada vez que esto ocurre, se cobra a la suscripción una hora de uso de acuerdo con el nivel de rendimiento de la colección que se va a crear.

## Ejemplos de base de datos

El método [RunDatabaseDemo](https://github.com/Azure/azure-documentdb-dotnet/blob/530c8d9cf7c99df7300246da05206c57ce654233/samples/code-samples/DatabaseManagement/Program.cs#L72-L121) del ejemplo del proyecto DatabaseManagement muestra cómo realizar las tareas siguientes.

Tarea | Referencia de API
--- | ---
[Creación de una base de datos](https://github.com/Azure/azure-documentdb-dotnet/blob/530c8d9cf7c99df7300246da05206c57ce654233/samples/code-samples/DatabaseManagement/Program.cs#L90) | [DocumentClient.CreateDatabaseAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdatabaseasync.aspx)
[Consulta de una base de datos de una cuenta](https://github.com/Azure/azure-documentdb-dotnet/blob/530c8d9cf7c99df7300246da05206c57ce654233/samples/code-samples/DatabaseManagement/Program.cs#L81) | [DocumentQueryable.CreateDatabaseQuery](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createdatabasequery.aspx)
[Lectura de una base de datos por identificador](https://github.com/Azure/azure-documentdb-dotnet/blob/530c8d9cf7c99df7300246da05206c57ce654233/samples/code-samples/DatabaseManagement/Program.cs#L102) | [DocumentClient.ReadDatabaseAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readdatabaseasync.aspx)
[Enumeración de las bases de datos de una cuenta](https://github.com/Azure/azure-documentdb-dotnet/blob/530c8d9cf7c99df7300246da05206c57ce654233/samples/code-samples/DatabaseManagement/Program.cs#L108-L113) | [DocumentClient.ReadDatabaseFeedAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readdatabasefeedasync.aspx)
[Eliminación de una base de datos](https://github.com/Azure/azure-documentdb-dotnet/blob/530c8d9cf7c99df7300246da05206c57ce654233/samples/code-samples/DatabaseManagement/Program.cs#L118) | [DocumentClient.DeleteDatabaseAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.deletedatabaseasync.aspx)

## Ejemplos de colección 

El método [RunCollectionDemo](https://github.com/Azure/azure-documentdb-dotnet/blob/530c8d9cf7c99df7300246da05206c57ce654233/samples/code-samples/CollectionManagement/Program.cs#L96-L185) del proyecto CollectionManagement de ejemplo muestra cómo realizar las tareas siguientes.

Tarea | Referencia de API
--- | ---
[Creación de una colección](https://github.com/Azure/azure-documentdb-dotnet/blob/89670bc8aefd9bdd932db7f9b6d2fcb9b6acf35e/samples/code-samples/CollectionManagement/Program.cs#L101) | [DocumentClient.CreateDocumentCollectionAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync.aspx)
[Obtención del nivel de rendimiento de una colección](https://github.com/Azure/azure-documentdb-dotnet/blob/89670bc8aefd9bdd932db7f9b6d2fcb9b6acf35e/samples/code-samples/CollectionManagement/Program.cs#L130) | [DocumentQueryable.CreateOfferQuery](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createofferquery.aspx)
[Cambio del nivel de rendimiento de una colección](https://github.com/Azure/azure-documentdb-dotnet/blob/89670bc8aefd9bdd932db7f9b6d2fcb9b6acf35e/samples/code-samples/CollectionManagement/Program.cs#L142-L143) | [DocumentClient.ReplaceOfferAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.replaceofferasync.aspx)
[Obtención de una colección por identificador](https://github.com/Azure/azure-documentdb-dotnet/blob/89670bc8aefd9bdd932db7f9b6d2fcb9b6acf35e/samples/code-samples/CollectionManagement/Program.cs#L153) | [DocumentClient.ReadDocumentCollectionAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readdocumentcollectionasync.aspx)
[Lectura de una lista de todas las colecciones de una base de datos](https://github.com/Azure/azure-documentdb-dotnet/blob/89670bc8aefd9bdd932db7f9b6d2fcb9b6acf35e/samples/code-samples/CollectionManagement/Program.cs#L162) | [DocumentClient.ReadDocumentCollectionFeedAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readdocumentcollectionfeedasync.aspx)
[Eliminación de un colección](https://github.com/Azure/azure-documentdb-dotnet/blob/89670bc8aefd9bdd932db7f9b6d2fcb9b6acf35e/samples/code-samples/CollectionManagement/Program.cs#L175) | [DocumentClient.DeleteDocumentCollectionAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.deletedocumentcollectionasync.aspx)

## Ejemplos de documento

El método [RunDocumentsDemo](https://github.com/Azure/azure-documentdb-dotnet/blob/f374cc601f4cf08d11c88f0c3fa7dcefaf7ecfe8/samples/code-samples/DocumentManagement/Program.cs#L97-L102) del proyecto DocumentManagement de ejemplo muestra cómo realizar las tareas siguientes.

Tarea | Referencia de API
--- | ---
[Creación de un documento](https://github.com/Azure/azure-documentdb-dotnet/blob/f374cc601f4cf08d11c88f0c3fa7dcefaf7ecfe8/samples/code-samples/DocumentManagement/Program.cs#L198) | [DocumentClient.CreateDocumentAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdocumentasync.aspx)
[Lectura de un documento por identificador](https://github.com/Azure/azure-documentdb-dotnet/blob/f374cc601f4cf08d11c88f0c3fa7dcefaf7ecfe8/samples/code-samples/DocumentManagement/Program.cs#L211) | [DocumentClient.ReadDocumentAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readdocumentasync.aspx)
[Lectura de todos los documentos de una colección](https://github.com/Azure/azure-documentdb-dotnet/blob/f374cc601f4cf08d11c88f0c3fa7dcefaf7ecfe8/samples/code-samples/DocumentManagement/Program.cs#L227) | [DocumentClient.ReadDocumentFeedAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readdocumentfeedasync.aspx)
[Consulta de documentos](https://github.com/Azure/azure-documentdb-dotnet/blob/f374cc601f4cf08d11c88f0c3fa7dcefaf7ecfe8/samples/code-samples/DocumentManagement/Program.cs#L248-L251) | [DocumentClient.CreateDocumentQuery](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createdocumentquery.aspx)
[Sustitución de un documento](https://github.com/Azure/azure-documentdb-dotnet/blob/f374cc601f4cf08d11c88f0c3fa7dcefaf7ecfe8/samples/code-samples/DocumentManagement/Program.cs#L263) | [DocumentClient.ReplaceDocumentAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.replacedocumentasync.aspx)
[Aplicación de upsert a un documento](https://github.com/Azure/azure-documentdb-dotnet/blob/f374cc601f4cf08d11c88f0c3fa7dcefaf7ecfe8/samples/code-samples/DocumentManagement/Program.cs#L300) | [DocumentClient.UpsertDocumentAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.upsertdocumentasync.aspx)
[Eliminación de un documento](https://github.com/Azure/azure-documentdb-dotnet/blob/f374cc601f4cf08d11c88f0c3fa7dcefaf7ecfe8/samples/code-samples/DocumentManagement/Program.cs#L322) | [DocumentClient.DeleteDocumentAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.deletedocumentasync.aspx)
[Uso de objetos dinámicos de .NET](https://github.com/Azure/azure-documentdb-dotnet/blob/f374cc601f4cf08d11c88f0c3fa7dcefaf7ecfe8/samples/code-samples/DocumentManagement/Program.cs#L331-L380) | [DocumentClient.CreateDocumentAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdocumentasync.aspx)<br>[DocumentClient.ReadDocumentAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readdocumentasync.aspx)<br>[DocumentClient.ReplaceDocumentAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.replacedocumentasync.aspx)
[Reemplazo de documento con la comprobación de ETag condicional](https://github.com/Azure/azure-documentdb-dotnet/blob/f2b11dec45a195ddeed333560ebba63055f5ed09/samples/code-samples/DocumentManagement/Program.cs#L398-L440) | [DocumentClient.AccessCondition](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.accesscondition.aspx)<br>[Documents.Client.AccessConditionType](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.accessconditiontype.aspx)
[Lectura de documento únicamente si ha cambiado](https://github.com/Azure/azure-documentdb-dotnet/blob/f2b11dec45a195ddeed333560ebba63055f5ed09/samples/code-samples/DocumentManagement/Program.cs#L442-L470) | [DocumentClient.AccessCondition](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.accesscondition.aspx)<br>[Documents.Client.AccessConditionType](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.accessconditiontype.aspx)

## Ejemplos de indización

El método [RunIndexDemo](https://github.com/Azure/azure-documentdb-dotnet/blob/ea8c977b9c2f37ddc2894911ec239907ab60e40a/samples/code-samples/IndexManagement/Program.cs#L89-L117) del proyecto IndexManagement de ejemplo muestra cómo realizar las tareas siguientes.

Tarea | Referencia de API
--- | ---
[Exclusión de un documento del índice](https://github.com/Azure/azure-documentdb-dotnet/blob/2e9a48b6a446b47dd6182606c8608d439b88b683/samples/code-samples/IndexManagement/Program.cs#L125-L163) | [IndexingDirective.Exclude](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.indexingdirective.aspx)
[Uso de la indexación manual (en lugar de la automática)](https://github.com/Azure/azure-documentdb-dotnet/blob/2e9a48b6a446b47dd6182606c8608d439b88b683/samples/code-samples/IndexManagement/Program.cs#L171-L209) | [IndexingPolicy.Automatic](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.indexingpolicy.automatic.aspx)
[Uso de la indexación diferida (en lugar de coherente)](https://github.com/Azure/azure-documentdb-dotnet/blob/2e9a48b6a446b47dd6182606c8608d439b88b683/samples/code-samples/IndexManagement/Program.cs#L221-L238) | [IndexingMode.Lazy](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.indexingpolicy.indexingmode.aspx#P:Microsoft.Azure.Documents.IndexingPolicy.IndexingMode)
[Exclusión de las rutas de acceso de documentos especificados del índice](https://github.com/Azure/azure-documentdb-dotnet/blob/2e9a48b6a446b47dd6182606c8608d439b88b683/samples/code-samples/IndexManagement/Program.cs#L248-L297) | [IndexingPolicy.ExcludedPaths](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.indexingpolicy.excludedpaths.aspx) 
[Forzar una operación de examen de intervalo en una ruta de acceso indexada de hash](https://github.com/Azure/azure-documentdb-dotnet/blob/2e9a48b6a446b47dd6182606c8608d439b88b683/samples/code-samples/IndexManagement/Program.cs#L305-L340) | [FeedOptions.EnableScanInQuery](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.feedoptions.enablescaninquery.aspx)
[Uso de índices de intervalo en cadenas](https://github.com/Azure/azure-documentdb-dotnet/blob/2e9a48b6a446b47dd6182606c8608d439b88b683/samples/code-samples/IndexManagement/Program.cs#L342-L405)| [IndexingPolicy.IncludedPaths](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.indexingpolicy.includedpaths.aspx)<br>[RangeIndex](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.rangeindex.aspx)
[Realización de una transformación de índices](https://github.com/Azure/azure-documentdb-dotnet/blob/2e9a48b6a446b47dd6182606c8608d439b88b683/samples/code-samples/IndexManagement/Program.cs#L407-L464)| [ReplaceDocumentCollectionAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.replacedocumentcollectionasync.aspx)

Para más información sobre la indexación, vea [Directivas de indexación de DocumentDB](documentdb-indexing-policies.md).
 
## Ejemplos de creación de particiones

El archivo de ejemplo de creación de particiones, [azure-documentdb-net/samples/code-samples/Partitioning/Program.cs](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/Partitioning/Program.cs), muestra cómo realizar las tareas siguientes. En algunos casos, se utilizan archivos auxiliares adicionales para completar la tarea.

Tarea | Referencia de API
--- | ---
[Uso de HashPartitionResolver](https://github.com/Azure/azure-documentdb-dotnet/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/Partitioning/Program.cs#L144-L160) | [HashPartitionResolver](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.hashpartitionresolver.aspx)
[Uso de RangePartitionResolver](https://github.com/Azure/azure-documentdb-dotnet/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/Partitioning/Program.cs#L162-L186) | [Range](https://msdn.microsoft.com/library/azure/mt126048.aspx) con<br>[RangePartitionResolver](https://msdn.microsoft.com/library/azure/mt126047.aspx)
[Implementación de resoluciones de partición personalizadas](https://github.com/Azure/azure-documentdb-net/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/Partitioning/Program.cs#L285) | [IPartitionResolver](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.ipartitionresolver.aspx)
[Implementación de una tabla de búsqueda simple](https://github.com/Azure/azure-documentdb-dotnet/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/Partitioning/Program.cs#L115-L119) con<br>[LookupPartitionResolver.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Partitioning/Partitioners/LookupPartitionResolver.cs) | [RangePartitionResolver](https://msdn.microsoft.com/library/azure/mt126047.aspx)
[Implementación de una resolución de partición que crea o clona colecciones](https://github.com/Azure/azure-documentdb-dotnet/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/Partitioning/Program.cs#L121-L126) con<br> [ManagedHashPartitionResolver.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Partitioning/Partitioners/ManagedHashPartitionResolver.cs) | [IPartitionResolver](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.ipartitionresolver.aspx)
[Implementación de un esquema de excedentes](https://github.com/Azure/azure-documentdb-dotnet/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/Partitioning/Program.cs#L128-L134) con <br>[SpilloverPartitionResolver.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Partitioning/Partitioners/SpilloverPartitionResolver.cs) | [IPartitionResolver](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.ipartitionresolver.aspx)
[Guardado y carga de configuraciones de resolución](https://github.com/Azure/azure-documentdb-dotnet/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/Partitioning/Program.cs#L136-L137) con <br>[RunSerializeDeserializeSample](https://github.com/Azure/azure-documentdb-dotnet/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/Partitioning/Program.cs#L295-L311) | [IPartitionResolver](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.ipartitionresolver.aspx)
[Agregación, eliminación y reequilibrado de datos entre particiones](https://github.com/Azure/azure-documentdb-dotnet/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/Partitioning/Program.cs#L139-L141) con <br>[RepartitionDataSample](https://github.com/Azure/azure-documentdb-dotnet/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/Partitioning/Program.cs#L313-L345) y<br>[DocumentClientHashPartitioningManager.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Partitioning/Util/DocumentClientHashPartitioningManager.cs) | [HashPartitionResolver](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.hashpartitionresolver.aspx)
[Implementación de a una resolución de partición para el enrutamiento durante el reparticionamiento](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Partitioning/Partitioners/TransitionHashPartitionResolver.cs) | [IPartitionResolver](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.ipartitionresolver.aspx) 

Para más información sobre la creación de particiones, vea [Partición y escalado de datos en DocumentDB](documentdb-partition-data.md).

## Ejemplos de geoespacialidad  

El archivo de ejemplo de geoespacialidad, [azure-documentdb-net/samples/code-samples/Geospatial/Program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Geospatial/Program.cs), muestra cómo realizar las tareas siguientes.
 
Tarea | Referencia de API  
---- | ---  
[Habilitación de la indexación geoespacial en una colección nueva](https://github.com/Azure/azure-documentdb-dotnet/blob/7b09c085817e850d683bc59bd864c2f6b552d275/samples/code-samples/Geospatial/Program.cs#L45-L63) | [IndexingPolicy](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.indexingpolicy.aspx)<br>[IndexKind.Spatial](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.indexkind.aspx)<br>[DataType.Point](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.datatype.aspx)  
[Inserción de documentos con puntos GeoJSON](https://github.com/Azure/azure-documentdb-dotnet/blob/7b09c085817e850d683bc59bd864c2f6b552d275/samples/code-samples/Geospatial/Program.cs#L116-L126) | [DocumentClient.CreateDocumentAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdocumentasync.aspx)<br>[DataType.Point](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.datatype.aspx)   
[Búsqueda de puntos dentro de una distancia especificada](https://github.com/Azure/azure-documentdb-dotnet/blob/7b09c085817e850d683bc59bd864c2f6b552d275/samples/code-samples/Geospatial/Program.cs#L152-L194) | [ST\_DISTANCE](documentdb-sql-query.md#built-in-functions) o<br>[GeometryOperationExtensions.Distance](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.spatial.geometryoperationextensions.distance.aspx#M:Microsoft.Azure.Documents.Spatial.GeometryOperationExtensions.Distance(Microsoft.Azure.Documents.Spatial.Geometry,Microsoft.Azure.Documents.Spatial.Geometry)  
[Búsqueda de puntos dentro de un polígono](https://github.com/Azure/azure-documentdb-dotnet/blob/7b09c085817e850d683bc59bd864c2f6b552d275/samples/code-samples/Geospatial/Program.cs#L196-L221) | [ST\_WITHIN](documentdb-sql-query.md#built-in-functions) o<br>[GeometryOperationExtensions.Within](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.spatial.geometryoperationextensions.within.aspx#M:Microsoft.Azure.Documents.Spatial.GeometryOperationExtensions.Within(Microsoft.Azure.Documents.Spatial.Geometry,Microsoft.Azure.Documents.Spatial.Geometry) y<br>[Polygon](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.spatial.polygon.aspx)  
[Habilitación de la indexación geoespacial en una colección existente](https://github.com/Azure/azure-documentdb-dotnet/blob/7b09c085817e850d683bc59bd864c2f6b552d275/samples/code-samples/Geospatial/Program.cs#L312-L336) | [DocumentClient.ReplaceDocumentCollectionAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.replacedocumentcollectionasync.aspx)<br>[DocumentCollection.IndexingPolicy](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.documentcollection.indexingpolicy.aspx#P:Microsoft.Azure.Documents.DocumentCollection.IndexingPolicy)  
[Validación de datos de puntos y polígonos](https://github.com/Azure/azure-documentdb-dotnet/blob/7b09c085817e850d683bc59bd864c2f6b552d275/samples/code-samples/Geospatial/Program.cs#L223-L265) | [ST\_ISVALID](documentdb-sql-query.md#built-in-functions)<br>[ST\_ISVALIDDETAILED](documentdb-sql-query.md#built-in-functions)<br>[GeometryOperationExtensions.IsValid](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.spatial.geometryoperationextensions.isvalid.aspx)<br>[GeometryOperationExtensions.IsValidDetailed](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.spatial.geometryoperationextensions.isvaliddetailed.aspx)  
 
Para más información sobre cómo trabajar con datos geoespaciales, vea [Trabajo con datos geoespaciales en Azure DocumentDB](documentdb-geospatial.md).
 
## Ejemplos de consultas

El archivo de documentos de consultas [azure-documentdb-net/samples/code-samples/Queries/Program.cs](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/Queries/Program.cs), muestra cómo realizar las siguientes tareas con la gramática de consultas SQL, el proveedor LINQ con consulta y con Lambda.

Tarea | Referencia de API
--- | ---
[Consulta de todos los documentos](https://github.com/Azure/azure-documentdb-dotnet/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/Queries/Program.cs#L122-L138) | [DocumentQueryable.CreateDocumentQuery](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createdocumentquery.aspx)
[Consulta de igualdad mediante ==](https://github.com/Azure/azure-documentdb-dotnet/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/Queries/Program.cs#L251-L268) | [DocumentQueryable.CreateDocumentQuery](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createdocumentquery.aspx)
[Consulta de desigualdad mediante != y NOT](https://github.com/Azure/azure-documentdb-dotnet/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/Queries/Program.cs#L270-L276) | [DocumentQueryable.CreateDocumentQuery](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createdocumentquery.aspx)
[Consulta mediante operadores de intervalo como >, <>, =, < =](https://github.com/Azure/azure-documentdb-dotnet/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/Queries/Program.cs#L305-L325) | [DocumentQueryable.CreateDocumentQuery](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createdocumentquery.aspx)
[Consulta con operadores de intervalo en cadenas](https://github.com/Azure/azure-documentdb-dotnet/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/Queries/Program.cs#L337-L346) | [DocumentQueryable.CreateDocumentQuery](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createdocumentquery.aspx)
[Consulta con Order by](https://github.com/Azure/azure-documentdb-dotnet/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/Queries/Program.cs#L370-L392) | [DocumentQueryable.CreateDocumentQuery](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createdocumentquery.aspx)
[Uso de subdocumentos](https://github.com/Azure/azure-documentdb-dotnet/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/Queries/Program.cs#L394-L419) | [DocumentQueryable.CreateDocumentQuery](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createdocumentquery.aspx)
[Consulta con Joins dentro de documentos](https://github.com/Azure/azure-documentdb-dotnet/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/Queries/Program.cs#L421-L435) | [DocumentQueryable.CreateDocumentQuery](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createdocumentquery.aspx)
[Consulta con operadores de cadena, matemáticos y de matriz](https://github.com/Azure/azure-documentdb-dotnet/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/Queries/Program.cs#L527-L552) | [DocumentQueryable.CreateDocumentQuery](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createdocumentquery.aspx)
[Consulta con SQL con parámetros mediante SqlQuerySpec](https://github.com/Azure/azure-documentdb-dotnet/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/Queries/Program.cs#L140-L174) | [DocumentQueryable.CreateDocumentQuery](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createdocumentquery.aspx)<br>[SqlQuerySpec](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.sqlqueryspec.aspx)
[Consulta con paginación explícita](https://github.com/Azure/azure-documentdb-dotnet/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/Queries/Program.cs#L554-L576) | [DocumentQueryable.CreateDocumentQuery](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createdocumentquery.aspx)

Para más información sobre cómo escribir consultas, vea [Consulta SQL en DocumentDB](documentdb-sql-query.md).


## Ejemplos de programación en el servidor

El archivo de programación en el servidor, [azure-documentdb-net/samples/code-samples/Geospatial/Program.cs](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/ServerSideScripts/Program.cs), muestra cómo realizar las tareas siguientes.

Tarea | Referencia de API
--- | ---
[Creación de un procedimiento almacenado](https://github.com/Azure/azure-documentdb-net/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/ServerSideScripts/Program.cs#L112) | [DocumentClient.CreateStoredProcedureAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createstoredprocedureasync.aspx)
[Ejecución de un procedimiento almacenado](https://github.com/Azure/azure-documentdb-net/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/ServerSideScripts/Program.cs#L127) | [DocumentClient.ExecuteStoredProcedureAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.executestoredprocedureasync.aspx)
[Lectura de una fuente de documentos para un procedimiento almacenado](https://github.com/Azure/azure-documentdb-net/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/ServerSideScripts/Program.cs#L194) | [DocumentClient.ReadDocumentFeedAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readdocumentfeedasync.aspx)
[Creación de un procedimiento almacenado con Order by](https://github.com/Azure/azure-documentdb-net/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/ServerSideScripts/Program.cs#L219) | [DocumentClient.CreateStoredProcedureAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createstoredprocedureasync.aspx) 
[Creación de un desencadenador previo](https://github.com/Azure/azure-documentdb-net/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/ServerSideScripts/Program.cs#L264) | [DocumentClient.CreateTriggerAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createtriggerasync.aspx)
[Creación de un desencadenador posterior](https://github.com/Azure/azure-documentdb-net/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/ServerSideScripts/Program.cs#L329) | [DocumentClient.CreateTriggerAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createtriggerasync.aspx)
[Creación de una función definida por el usuario (UDF)](https://github.com/Azure/azure-documentdb-net/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/ServerSideScripts/Program.cs#L389) | [DocumentClient.CreateUserDefinedFunctionAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createuserdefinedfunctionasync.aspx) 

Para más información sobre la programación en el servidor, vea [Programación en el servidor de DocumentDB: procedimientos almacenados, desencadenadores de base de datos y UDF](documentdb-programming.md).

## Ejemplos de administración de usuarios

El archivo de administración de usuarios, [azure-documentdb-net/samples/code-samples/UserManagement/Program.cs](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/UserManagement/Program.cs), muestra cómo realizar las tareas siguientes.

Tarea | Referencia de API
--- | ---
[Creación de un usuario](https://github.com/Azure/azure-documentdb-net/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/UserManagement/Program.cs#L81) | [DocumentClient.CreateUserAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createuserasync.aspx)
[Establecimiento de permisos en una colección o en un documento](https://github.com/Azure/azure-documentdb-net/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/UserManagement/Program.cs#L85) | [DocumentClient.CreatePermissionAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createpermissionasync.aspx)
[Obtención de una lista de permisos de un usuario](https://github.com/Azure/azure-documentdb-net/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/UserManagement/Program.cs#L218) | [DocumentClient.ReadUserAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readuserasync.aspx)<br>[DocumentClient.ReadPermissionFeedAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readpermissionfeedasync.aspx)

<!---HONumber=AcomDC_0224_2016-->