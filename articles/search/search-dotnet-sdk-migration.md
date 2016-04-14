<properties
   pageTitle="Actualización a la versión 1.1 del SDK de .NET para Búsqueda de Azure | Microsoft Azure | Servicio de búsqueda hospedado en la nube"
   description="Actualización a la versión 1.1 del SDK de .NET para Búsqueda de Azure"
   services="search"
   documentationCenter=""
   authors="brjohnstmsft"
   manager="pablocas"
   editor=""/>

<tags
   ms.service="search"
   ms.devlang="dotnet"
   ms.workload="search"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.date="02/09/2016"
   ms.author="brjohnst"/>

# Actualización a la versión 1.1 del SDK de .NET para Búsqueda de Azure

Si usa la versión preliminar 1.0.2 o una anterior del [SDK de .NET para Búsqueda de Azure](https://msdn.microsoft.com/library/azure/dn951165.aspx), este artículo le ayudará a actualizar la aplicación a la primera versión disponible con carácter general, la versión 1.1.

Para obtener un tutorial más general del SDK que incluya ejemplos, vea [Cómo usar Búsqueda de Azure desde una aplicación .NET](search-howto-dotnet-sdk.md).

La versión 1.1 del SDK de .NET para Búsqueda de Azure contiene varios cambios respecto a las versiones anteriores a la versión preliminar 1.0.0 (incluidas la versión preliminar 0.13.0 y anteriores). La mayoría son de menor importancia, por lo que cambiar el código solo precisará del mínimo esfuerzo. Vea [Pasos para actualizar](#UpgradeSteps), a fin de obtener instrucciones sobre cómo cambiar el código para usar la nueva versión del SDK.

<a name="WhatsNew"></a>
## Novedades de la versión 1.1

La versión 1.1 remite a la misma versión de la API de REST que las versiones anteriores del SDK de .NET para Búsqueda de Azure (28-02-2015), por lo que esta versión no presenta ninguna característica nueva del servicio. Sin embargo, hay nuevas características de la serialización del cliente.

El SDK usa JSON.NET para serializar y deserializar documentos. La nueva versión del SDK es compatible con la serialización personalizada a través de `JsonConverter` y `IContractResolver` (vea la [documentación de JSON.NET](http://www.newtonsoft.com/json/help/html/Introduction.htm) para obtener más detalles). Esto puede ser útil cuando desea adaptar una clase de modelo existente de la aplicación para usarla con Búsqueda de Azure y otros escenarios más avanzados. Por ejemplo, con la serialización personalizada, puede:

 - Incluir o excluir determinadas propiedades de la clase de modelo para que se almacenen como campos del documento.
 - Asignar entre los nombres de propiedad del código y los nombres de campo del índice.
 - Crear atributos personalizados que se pueden utilizar tanto para asignar propiedades a campos de documentos como para crear la definición de índice correspondiente.

Puede encontrar ejemplos de implementación de serialización personalizada en las pruebas unitarias del SDK de .NET para Búsqueda de Azure en GitHub. Un buen punto de partida es [esta carpeta](https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/Search/Search.Tests/Tests/Models). Contiene clases que las pruebas de serialización personalizada utilizan.

Además de la serialización personalizada, el nuevo SDK también admite la serialización de objetos `SearchContinuationToken`. Esto puede resultar útil si llama a Búsqueda de Azure desde una aplicación web y debe intercambiar tokens de continuación con un explorador o cliente móvil mientras se recorren las páginas de los resultados de la búsqueda.

<a name="UpgradeSteps"></a>
## Pasos para actualizar

En primer lugar, actualice la referencia de NuGet para `Microsoft.Azure.Search` mediante la Consola del Administrador de paquetes NuGet, o bien haga clic con el botón derecho en las referencias del proyecto y seleccione "Administrar paquetes NuGet..." en Visual Studio.

Una vez que NuGet ha descargado los nuevos paquetes y sus dependencias, recompile el proyecto.

Si antes usaba las versiones preliminares 1.0.0, 1.0.1 o 1.0.2, la compilación debería ejecutarse correctamente y estará lista para comenzar a usarse.

De lo contrario, si usaba la versión preliminar 0.13.0 u otra anterior, verá errores de compilación como los siguientes:

    Program.cs(137,56,137,62): error CS0117: 'Microsoft.Azure.Search.Models.IndexBatch' does not contain a definition for 'Create'
    Program.cs(137,99,137,105): error CS0117: 'Microsoft.Azure.Search.Models.IndexAction' does not contain a definition for 'Create'
    Program.cs(146,41,146,54): error CS1061: 'Microsoft.Azure.Search.IndexBatchException' does not contain a definition for 'IndexResponse' and no extension method 'IndexResponse' accepting a first argument of type 'Microsoft.Azure.Search.IndexBatchException' could be found (are you missing a using directive or an assembly reference?)
    Program.cs(163,13,163,42): error CS0246: The type or namespace name 'DocumentSearchResponse' could not be found (are you missing a using directive or an assembly reference?)

El paso siguiente consiste en corregir los errores de compilación uno a uno. La mayoría requiere cambiar algunos nombres de clase y método cuyo nombre ha cambiado en el SDK. La [lista de cambios importantes de la versión 1.1](#ListOfChanges) contiene una lista de estos cambios de nombre.

Si usa clases personalizadas para modelar los documentos, y esas clases tienen propiedades de tipos primitivos que no aceptan valores nulos (por ejemplo, `int` o `bool` en C#), hay una corrección de errores en la versión 1.1 del SDK que debe tener en cuenta. Para obtener más información, consulte la sección [Correcciones de errores en la versión 1.1](#BugFixes).

Por último, una vez que haya solucionado los errores de compilación, puede realizar cambios a la aplicación para aprovechar las ventajas de la nueva funcionalidad, si lo desea. La característica de serialización personalizada del nuevo SDK se detalla en [Novedades de la versión 1.1](#WhatsNew).

<a name="ListOfChanges"></a>
## Lista de cambios importantes de la versión 1.1

La siguiente lista se ordena por la probabilidad de que el cambio afecte al código de aplicación.

### Cambios de IndexBatch e IndexAction

El nombre `IndexBatch.Create` se ha cambiado por `IndexBatch.New` y ya no tiene un argumento `params`. Puede usar `IndexBatch.New` para los lotes que mezclan distintos tipos de acciones (combinaciones, eliminaciones, etc.). Además, existen nuevos métodos estáticos para crear lotes donde todas las acciones son las mismas: `Delete`, `Merge`, `MergeOrUpload` y `Upload`.

`IndexAction` ya no tiene constructores públicos y sus propiedades ahora son inmutables. Debe usar los nuevos métodos estáticos para crear acciones para propósitos diferentes: `Delete`, `Merge`, `MergeOrUpload` y `Upload`. `IndexAction.Create` se ha quitado. Si utiliza la sobrecarga que solo usa un documento, asegúrese de usar `Upload` en su lugar.

#### Ejemplo

Si el código tiene el siguiente aspecto:

    var batch = IndexBatch.Create(documents.Select(doc => IndexAction.Create(doc)));
    indexClient.Documents.Index(batch);

Puede cambiarlo por este para corregir cualquier error de compilación:

    var batch = IndexBatch.New(documents.Select(doc => IndexAction.Upload(doc)));
    indexClient.Documents.Index(batch);

Si lo desea, puede simplificarlo aún más y optar por este:

    var batch = IndexBatch.Upload(documents);
    indexClient.Documents.Index(batch);

### Cambios de IndexBatchException

La propiedad `IndexBatchException.IndexResponse` ha cambiado a `IndexingResults`, y su tipo ahora es `IList<IndexingResult>`.

#### Ejemplo

Si el código tiene el siguiente aspecto:

    catch (IndexBatchException e)
    {
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexResponse.Results.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

Puede cambiarlo por este para corregir cualquier error de compilación:

    catch (IndexBatchException e)
    {
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

<a name="OperationMethodChanges"></a>
### Cambios del método de operación

Cada operación del SDK de .NET para Búsqueda de Azure se expone como un conjunto de sobrecargas de método para los autores de llamadas sincrónicas y asincrónicas. Las firmas y la factorización de estas sobrecargas de método han cambiado en la versión 1.1.

Por ejemplo, la operación de "Obtener estadísticas de índice" en versiones anteriores del SDK expone estas firmas:

En `IIndexOperations`:

    // Asynchronous operation with all parameters
    Task<IndexGetStatisticsResponse> GetStatisticsAsync(
        string indexName,
        CancellationToken cancellationToken);

En `IndexOperationsExtensions`:

    // Asynchronous operation with only required parameters
    public static Task<IndexGetStatisticsResponse> GetStatisticsAsync(
        this IIndexOperations operations,
        string indexName);

    // Synchronous operation with only required parameters
    public static IndexGetStatisticsResponse GetStatistics(
        this IIndexOperations operations,
        string indexName);

Las firmas de método para la misma operación en la versión 1.1 tienen este aspecto:

En `IIndexesOperations`:

    // Asynchronous operation with lower-level HTTP features exposed
    Task<AzureOperationResponse<IndexGetStatisticsResult>> GetStatisticsWithHttpMessagesAsync(
        string indexName,
        SearchRequestOptions searchRequestOptions = default(SearchRequestOptions),
        Dictionary<string, List<string>> customHeaders = null,
        CancellationToken cancellationToken = default(CancellationToken));

En `IndexesOperationsExtensions`:

    // Simplified asynchronous operation
    public static Task<IndexGetStatisticsResult> GetStatisticsAsync(
        this IIndexesOperations operations,
        string indexName,
        SearchRequestOptions searchRequestOptions = default(SearchRequestOptions),
        CancellationToken cancellationToken = default(CancellationToken));

    // Simplified synchronous operation
    public static IndexGetStatisticsResult GetStatistics(
        this IIndexesOperations operations,
        string indexName,
        SearchRequestOptions searchRequestOptions = default(SearchRequestOptions));

A partir de la versión 1.1, el SDK de .NET para Búsqueda de Azure organiza los métodos de operación de forma diferente:

 - Los parámetros opcionales ahora se modelan como predeterminados en lugar de como sobrecargas de método adicionales. Esto reduce el número de sobrecargas de método, en ocasiones drásticamente.
 - Los métodos de extensión ahora ocultan muchos de los detalles superfluos de HTTP del autor de llamada. Por ejemplo, las versiones anteriores del SDK devuelven un objeto de respuesta con un código de estado HTTP, que a menudo no necesitará comprobar, ya que los métodos de operación lanzan `CloudException` para cualquier código de estado que indica un error. Los nuevos métodos de extensión solo devuelven objetos de modelo, lo que le ahorra la molestia de tener que desencapsularlos en el código.
 - Por el contrario, las interfaces principales ahora exponen métodos que permiten ejercer un mayor control a nivel de HTTP en caso de que sea necesario. Ahora puede transferir encabezados HTTP personalizados para incluirlos en solicitudes, y el nuevo tipo de devolución `AzureOperationResponse<T>` le ofrece acceso directo a `HttpRequestMessage` y `HttpResponseMessage` para la operación. `AzureOperationResponse` se define en el espacio de nombres `Microsoft.Rest.Azure` y reemplaza a `Hyak.Common.OperationResponse`.

### Cambios de la clase de modelo

Debido a los cambios de firma descritos en [Cambios del método de operación](#OperationMethodChanges), muchas clases del espacio de nombres `Microsoft.Azure.Search.Models` se han cambiado de nombre o se han eliminado. Por ejemplo:

 - `IndexDefinitionResponse` se ha reemplazado por `AzureOperationResponse<Index>`
 - El nombre `DocumentSearchResponse` ha cambiado a `DocumentSearchResult`
 - El nombre `IndexResult` ha cambiado a `IndexingResult`
 - `Documents.Count()` ahora devuelve `long` con el número de documentos en lugar de `DocumentCountResponse`
 - El nombre `IndexGetStatisticsResponse` ha cambiado a `IndexGetStatisticsResult`
 - El nombre `IndexListResponse` ha cambiado a `IndexListResult`

Para resumir, las clases derivadas de `OperationResponse` que existían solo para encapsular un objeto de modelo se han quitado. El sufijo de las demás clases ha cambiado de `Response` a `Result`.

#### Ejemplo

Si el código tiene el siguiente aspecto:

    IndexerGetStatusResponse statusResponse = null;

    try
    {
        statusResponse = _searchClient.Indexers.GetStatus(indexer.Name);
    }
    catch (Exception ex)
    {
        Console.WriteLine("Error polling for indexer status: {0}", ex.Message);
        return;
    }

    IndexerExecutionResult lastResult = statusResponse.ExecutionInfo.LastResult;

Puede cambiarlo por este para corregir cualquier error de compilación:

    IndexerExecutionInfo status = null;

    try
    {
        status = _searchClient.Indexers.GetStatus(indexer.Name);
    }
    catch (Exception ex)
    {
        Console.WriteLine("Error polling for indexer status: {0}", ex.Message);
        return;
    }

    IndexerExecutionResult lastResult = status.LastResult;

#### Clases de respuesta e IEnumerable

Un cambio adicional que puede afectar a su código es que las clases de respuesta que contienen colecciones ya no implementan `IEnumerable<T>`. En su lugar, puede acceder a la propiedad de colección directamente. Por ejemplo, si el código tiene el siguiente aspecto:

    DocumentSearchResponse<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
    foreach (SearchResult<Hotel> result in response)
    {
        Console.WriteLine(result.Document);
    }

Puede cambiarlo por este para corregir cualquier error de compilación:

    DocumentSearchResult<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
    foreach (SearchResult<Hotel> result in response.Results)
    {
        Console.WriteLine(result.Document);
    }

#### Nota importante para las aplicaciones web

Si tiene una aplicación web que serializa `DocumentSearchResponse` directamente para enviar los resultados de la búsqueda al explorador, debe cambiar el código, o los resultados no se serializarán correctamente. Por ejemplo, si el código tiene el siguiente aspecto:

    public ActionResult Search(string q = "")
    {
        // If blank search, assume they want to search everything
        if (string.IsNullOrWhiteSpace(q))
            q = "*";

        return new JsonResult
        {
            JsonRequestBehavior = JsonRequestBehavior.AllowGet,
            Data = _featuresSearch.Search(q)
        };
    }

Puede cambiar mediante la obtención de la propiedad `.Results` de la respuesta de búsqueda para corregir la representación de los resultados de la búsqueda:

    public ActionResult Search(string q = "")
    {
        // If blank search, assume they want to search everything
        if (string.IsNullOrWhiteSpace(q))
            q = "*";

        return new JsonResult
        {
            JsonRequestBehavior = JsonRequestBehavior.AllowGet,
            Data = _featuresSearch.Search(q).Results
        };
    }

Tendrá que buscar esos casos en el código usted mismo; **el compilador no advierte** porque `JsonResult.Data` es de tipo `object`.

### Cambios de CloudException

La clase `CloudException` se ha desplazado desde el espacio de nombres `Hyak.Common` al espacio de nombres `Microsoft.Rest.Azure`. Además, el nombre de su propiedad `Error` ha cambiado a `Body`.

### Cambios de SearchServiceClient y SearchIndexClient

El tipo de la propiedad `Credentials` ha cambiado de `SearchCredentials` a su clase base, `ServiceClientCredentials`. Si necesita acceder a `SearchCredentials` de `SearchIndexClient` o `SearchServiceClient`, use la nueva propiedad `SearchCredentials`.

En versiones anteriores del SDK, `SearchServiceClient` y `SearchIndexClient` tenían constructores con un parámetro `HttpClient`. Estos se han reemplazado con constructores que obtienen `HttpClientHandler` y una matriz de objetos `DelegatingHandler`. Esto facilita instalar controladores personalizados para procesar previamente las solicitudes HTTP si es necesario.

Por último, los constructores que obtuvieron `Uri` y `SearchCredentials` han cambiado. Por ejemplo, si tiene código con este aspecto:

    var client =
        new SearchServiceClient(
            new SearchCredentials("abc123"),
            new Uri("http://myservice.search.windows.net"));

Puede cambiarlo por este para corregir cualquier error de compilación:

    var client =
        new SearchServiceClient(
            new Uri("http://myservice.search.windows.net"),
            new SearchCredentials("abc123"));

Observe también que el tipo del parámetro de credenciales ha cambiado a `ServiceClientCredentials`. Es poco probable que esto afecte al código, ya que `SearchCredentials` deriva de `ServiceClientCredentials`.

### Transferir un identificador de solicitud

En versiones anteriores del SDK, puede establecer un identificador de solicitud en `SearchServiceClient` o `SearchIndexClient` y se incluiría en cada solicitud a la API de REST. Esto es útil para solucionar problemas con el servicio de búsqueda si necesita ponerse en contacto con soporte técnico. Sin embargo, es más útil establecer un identificador único de solicitud para cada operación, en lugar de utilizar el mismo identificador para todas las operaciones. Por este motivo, los métodos `SetClientRequestId` de `SearchServiceClient` y `SearchIndexClient` se han quitado. En su lugar, puede pasar un identificador de solicitud a cada método de operación mediante el parámetro opcional `SearchRequestOptions`.

> [AZURE.NOTE] En una futura versión del SDK, agregaremos un nuevo mecanismo para establecer un identificador de solicitud globalmente en los objetos cliente que sea coherente con el enfoque usado por otros SDK de Azure.

#### Ejemplo

Si tiene código con este aspecto:

    client.SetClientRequestId(Guid.NewGuid());
    ...
    long count = client.Documents.Count();

Puede cambiarlo por este para corregir cualquier error de compilación:

    long count = client.Documents.Count(new SearchRequestOptions(requestId: Guid.NewGuid()));

### Cambios de nombre de interfaz

Los nombres de interfaz del grupo de operaciones han cambiado por coherencia con sus nombres de propiedad correspondientes:

 - El nombre del tipo de `ISearchServiceClient.Indexes` ha cambiado de `IIndexOperations` a `IIndexesOperations`.
 - El nombre del tipo de `ISearchServiceClient.Indexers` ha cambiado de `IIndexerOperations` a `IIndexersOperations`.
 - El nombre del tipo de `ISearchServiceClient.DataSources` ha cambiado de `IDataSourceOperations` a `IDataSourcesOperations`.
 - El nombre del tipo de `ISearchIndexClient.Documents` ha cambiado de `IDocumentOperations` a `IDocumentsOperations`.

Es poco probable que este cambio afecte al código, a menos que cree simulacros de estas interfaces a efectos de pruebas.

<a name="BugFixes"></a>
## Corrección de errores en la versión 1.1

Se produjo un error en las versiones anteriores del SDK de .NET para Búsqueda de Azure en relación con la serialización de clases de modelos personalizados. El error puede producirse si ha creado una clase de modelo personalizado con una propiedad de un tipo de valor que no acepta valores NULL.

### Pasos para reproducir

Cree una clase de modelo personalizado con una propiedad de tipo de valor que no acepta valores NULL. Por ejemplo, agregue una propiedad `UnitCount` pública de tipo `int` en lugar de `int?`.

Si indexa un documento con el valor predeterminado de ese tipo (por ejemplo, 0 para `int`), el campo será null en Búsqueda de Azure. Si desea buscar posteriormente ese documento, la llamada `Search` producirá `JsonSerializationException` indicando que no se puede convertir `null` a `int`.

Además, los filtros pueden no funcionar según lo esperado, ya que se escribió null en el índice, en lugar del valor previsto.

### Detalles de corrección

Corregimos este problema en la versión 1.1 del SDK. Ahora, si tiene una clase de modelo similar a la siguiente:

    public class Model
    {
        public string Key { get; set; }

        public int IntValue { get; set; }
    }

y establece `IntValue` en 0, ese valor ahora se serializa correctamente como 0 en la conexión y se almacena como 0 en el índice. El recorrido de ida y vuelta también funciona según lo previsto.

Hay un problema potencial que hay que tener en cuenta con este enfoque: si utiliza un tipo de modelo con una propiedad que no acepta valores NULL, tendrá que **garantizar** que ningún documento del índice contiene un valor NULL para el campo correspondiente. Ni el SDK ni la API de REST para Búsqueda de Azure le permitirá aplicar esto.

Esto no es solo una inquietud hipotética: imagine un escenario donde agregar un nuevo campo a un índice existente que es de tipo `Edm.Int32`. Después de actualizar la definición del índice, todos los documentos tendrán un valor null para ese campo nuevo (ya que todos los tipos aceptan valores NULL en Búsqueda de Azure). Si después utiliza una clase de modelo con una propiedad `int` que no acepta valores NULL para ese campo, obtendrá `JsonSerializationException` así al intentar recuperar documentos:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Por este motivo, recomendamos utilizar tipos que aceptan valores null en las clases de modelo como procedimiento recomendado.

Para obtener más detalles sobre este error y la corrección, vea [este problema en GitHub](https://github.com/Azure/azure-sdk-for-net/issues/1063).

## Conclusión
Si necesita más detalles sobre el uso del SDK de .NET para Búsqueda de Azure, vea nuestras [instrucciones](search-howto-dotnet-sdk.md) actualizadas recientemente y los artículos de [introducción](search-get-started-dotnet.md).

Agradecemos sus comentarios sobre el SDK. Si tiene problemas, no dude en pedirnos ayuda en el [foro de MSDN sobre Búsqueda de Azure](https://social.msdn.microsoft.com/Forums/azure/es-ES/home?forum=azuresearch). Si encuentra un error, puede presentar un problema en el [repositorio de GitHub del SDK de .NET para Azure](https://github.com/Azure/azure-sdk-for-net/issues). Asegúrese de que prefija el título del problema con "SDK de búsqueda: ".

Gracias por usar Búsqueda de Azure.

<!---HONumber=AcomDC_0211_2016-->