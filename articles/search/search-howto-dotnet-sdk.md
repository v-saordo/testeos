<properties
   pageTitle="Uso de Búsqueda de Azure desde una aplicación .NET | Microsoft Azure | Servicio de búsqueda hospedado en la nube"
   description="Cómo usar Búsqueda de Azure desde una aplicación .NET"
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

# Cómo usar Búsqueda de Azure desde una aplicación .NET

Este artículo es un tutorial para empezar a trabajar con el [SDK de Búsqueda de Azure para .NET](https://msdn.microsoft.com/library/azure/dn951165.aspx). Puede utilizar el SDK para .NET para implementar una experiencia de búsqueda enriquecida en la aplicación mediante Búsqueda de Azure.

## Qué es el SDK de Búsqueda de Azure

El SDK contiene una biblioteca de cliente, `Microsoft.Azure.Search`. Permite administrar los índices, los orígenes de datos y los indizadores, así como cargar y administrar documentos y ejecutar consultas, todo ello sin tener que ocuparse de los detalles de HTTP y JSON.

La biblioteca de cliente define clases como `Index`, `Field` y `Document`, además de operaciones como `Indexes.Create` y `Documents.Search` en las clases `SearchServiceClient` y `SearchIndexClient`. Estas clases están organizadas en los espacios de nombres siguientes:

- [Microsoft.Azure.Search](https://msdn.microsoft.com/library/azure/microsoft.azure.search.aspx)
- [Microsoft.Azure.Search.Models](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.aspx)

La versión actual del SDK de .NET para Búsqueda de Azure ya está disponible con carácter general. Si desea enviarnos comentarios para que los tengamos en cuenta en próxima versión, visite nuestra [página de comentarios](https://feedback.azure.com/forums/263029-azure-search/).

El SDK para .NET es compatible con la versión `2015-02-28` de la API de REST de Búsqueda de Azure, documentada en [MSDN](https://msdn.microsoft.com/library/azure/dn798935.aspx). Esta versión ahora es compatible con la sintaxis de consulta Lucene y los analizadores de idioma de Microsoft. Las nuevas características que *no* forman parte de esta versión, como el parámetro de búsqueda `moreLikeThis`, se encuentran en [vista previa](search-api-2015-02-28-preview.md) y no están disponibles todavía en el SDK. Puede consultar [Versiones del servicio de búsqueda](https://msdn.microsoft.com/library/azure/dn864560.aspx) o [Actualizaciones más recientes de Búsqueda de Azure](search-latest-updates.md) para ver las actualizaciones de estado de cada característica.

Estas son otras características no admitidas en este SDK:

  - [Operaciones de administración](https://msdn.microsoft.com/library/azure/dn832684.aspx). Las operaciones de administración incluyen el aprovisionamiento de servicios Búsqueda de Azure y la administración de claves de API. Estas se admitirán en un SDK de administración de Búsqueda de Azure para .NET en el futuro.

## Actualización a la versión más reciente del SDK

Si ya utiliza una versión anterior del SDK de .NET para Búsqueda de Azure y desea actualizar a la nueva versión disponible en [este artículo](search-dotnet-sdk-migration.md) se explica cómo.

## Requisitos para el SDK

1. Visual Studio 2013 o Visual Studio 2015.

2. Su propio servicio Búsqueda de Azure. Para usar el SDK, será necesario el nombre del servicio y una o varias claves de API. [Crear un servicio en el portal](search-create-service-portal.md) le ayudará con estos pasos.

3. Descargue el [paquete NuGet](http://www.nuget.org/packages/Microsoft.Azure.Search) del SDK de Búsqueda de Azure para .NET mediante "Administrar paquetes de NuGet" en Visual Studio. Solo tiene que buscar el nombre del paquete `Microsoft.Azure.Search` en NuGet.org.

El SDK de .NET de Búsqueda de Azure es compatible con las aplicaciones destinadas a .NET Framework 4.5, además de aplicaciones de la Tienda Windows destinadas a Windows 8.1 y Windows Phone 8.1. No se admite Silverlight.

## Escenarios principales

Hay varias tareas que debe realizar en su aplicación de búsqueda. En este tutorial, hablaremos sobre estos escenarios básicos:

- Creación de un índice
- Llenado del índice con documentos
- Búsqueda de documentos mediante filtros y búsqueda de texto completo

El código de ejemplo siguiente expone cada una de ellas. No dude en usar los fragmentos de código en su propia aplicación.

### Información general

La aplicación de ejemplo que vamos a explorar crea un nuevo índice denominado "hotels", lo rellena con varios documentos y, a continuación, ejecuta varias consultas de búsqueda. Este es el programa principal, que muestra el flujo general:

    // This sample shows how to delete, create, upload documents and query an index
    static void Main(string[] args)
    {
        // Put your search service name here. This is the hostname portion of your service URL.
        // For example, if your service URL is https://myservice.search.windows.net, then your
        // service name is myservice.
        string searchServiceName = "myservice";

        string apiKey = "Put your API admin key here."

        SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));

        Console.WriteLine("{0}", "Deleting index...\n");
        DeleteHotelsIndexIfExists(serviceClient);

        Console.WriteLine("{0}", "Creating index...\n");
        CreateHotelsIndex(serviceClient);

        SearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");

        Console.WriteLine("{0}", "Uploading documents...\n");
        UploadDocuments(indexClient);

        Console.WriteLine("{0}", "Searching documents 'fancy wifi'...\n");
        SearchDocuments(indexClient, searchText: "fancy wifi");

        Console.WriteLine("\n{0}", "Filter documents with category 'Luxury'...\n");
        SearchDocuments(indexClient, searchText: "*", filter: "category eq 'Luxury'");

        Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
        Console.ReadKey();
    }

Lo recorreremos paso a paso. Primero, debemos crear un nuevo `SearchServiceClient`. Este objeto le permite administrar índices. Para crear uno, deberá proporcionar el nombre de su servicio Búsqueda de Azure y una clave de API de administración.

        // Put your search service name here. This is the hostname portion of your service URL.
        // For example, if your service URL is https://myservice.search.windows.net, then your
        // service name is myservice.
        string searchServiceName = "myservice";

        string apiKey = "Put your API admin key here."

        SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));

> [AZURE.NOTE] Si proporciona una clave incorrecta (por ejemplo, una clave de consulta cuando se requiere una clave de administración), el `SearchServiceClient` producirá un `CloudException` con el mensaje de error "Forbidden" (Prohibido) cuando llame a un método de operación con él, como `Indexes.Create`. Si esto sucede, compruebe la clave de API.

Las siguientes líneas llaman a métodos para crear un índice llamado "hotels", eliminándolo antes si ya existe. Describiremos estos métodos más adelante.

        Console.WriteLine("{0}", "Deleting index...\n");
        DeleteHotelsIndexIfExists(serviceClient);

        Console.WriteLine("{0}", "Creating index...\n");
        CreateHotelsIndex(serviceClient);

A continuación, el índice debe rellenarse. Para ello, necesitamos un `SearchIndexClient`. Hay dos maneras de obtener uno: se puede crear o se puede llamar a `Indexes.GetClient` en el `SearchServiceClient`. Usamos esta última por motivos de comodidad.

        SearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");

> [AZURE.NOTE] En una aplicación de búsqueda típica, el llenado y la administración de índices se controlan mediante un componente independiente de las consultas de búsqueda. `Indexes.GetClient` resulta cómodo para rellenar un índice porque evita la molestia de proporcionar otro `SearchCredentials`. Con este fin, pasa la clave de administrador que se usó para crear el `SearchServiceClient` al nuevo `SearchIndexClient`. Sin embargo, en la parte de la aplicación que ejecuta consultas, es mejor crear directamente el `SearchIndexClient` para poder pasar una clave de consulta en lugar de una clave de administración. Esto está en consonancia con el principio de privilegios mínimos y le ayudará a proteger su aplicación. Puede encontrar más información acerca de las claves de administración y consulta [aquí](https://msdn.microsoft.com/library/azure/dn798935.aspx).

Ahora que tenemos un `SearchIndexClient`, podemos rellenar el índice. Para ello utilizaremos otro método que tratamos más adelante.

        Console.WriteLine("{0}", "Uploading documents...\n");
        UploadDocuments(indexClient);

Finalmente, ejecutamos algunas consultas de búsqueda y mostramos los resultados, utilizando de nuevo el `SearchIndexClient`:

        Console.WriteLine("{0}", "Searching documents 'fancy wifi'...\n");
        SearchDocuments(indexClient, searchText: "fancy wifi");

        Console.WriteLine("\n{0}", "Filter documents with category 'Luxury'...\n");
        SearchDocuments(indexClient, searchText: "*", filter: "category eq 'Luxury'");

        Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
        Console.ReadKey();

Si ejecuta esta aplicación con un nombre de servicio y una clave de API válidos, el resultado debe ser similar al siguiente:

    Deleting index...

    Creating index...

    Uploading documents...

    Searching documents 'fancy wifi'...

    ID: 1058-441    Name: Fancy Stay        Category: Luxury        Tags: [pool, view, concierge]
    ID: 956-532     Name: Express Rooms     Category: Budget        Tags: [wifi, budget]

    Filter documents with category 'Luxury'...

    ID: 1058-441    Name: Fancy Stay        Category: Luxury        Tags: [pool, view, concierge]
    ID: 566-518     Name: Surprisingly Expensive Suites     Category: Luxury	Tags: []
    Complete.  Press any key to end application...

El código fuente completo de la aplicación se proporciona al final de este artículo.

A continuación, veremos más de cerca cada uno de los métodos llamados por `Main`.

### Creación de un índice

Después de crear un `SearchServiceClient`, lo siguiente que hace `Main` es eliminar el índice "hotels" si ya existe. Esto se lleva a cabo mediante el método siguiente:

    private static void DeleteHotelsIndexIfExists(SearchServiceClient serviceClient)
    {
        if (serviceClient.Indexes.Exists("hotels"))
        {
            serviceClient.Indexes.Delete("hotels");
        }
    }

Este método utiliza el `SearchServiceClient` proporcionado para comprobar si existe el índice y, en ese caso, eliminarlo.

> [AZURE.NOTE] El código de ejemplo de este artículo usa los métodos sincrónicos del SDK de Búsqueda de Azure para .NET por motivos de simplicidad. En sus propias aplicaciones, recomendamos que use métodos asincrónicos para mantener su escalabilidad y capacidad de respuesta. Por ejemplo, en el método anterior podría utilizar `ExistsAsync` y `DeleteAsync` en lugar de `Exists` y `Delete`.

A continuación, `Main` crea un nuevo índice "hotels" mediante una llamada a este método:

    private static void CreateHotelsIndex(SearchServiceClient serviceClient)
    {
        var definition = new Index()
        {
            Name = "hotels",
            Fields = new[]
            {
                new Field("hotelId", DataType.String)                       { IsKey = true },
                new Field("hotelName", DataType.String)                     { IsSearchable = true, IsFilterable = true },
                new Field("baseRate", DataType.Double)                      { IsFilterable = true, IsSortable = true },
                new Field("category", DataType.String)                      { IsSearchable = true, IsFilterable = true, IsSortable = true, IsFacetable = true },
                new Field("tags", DataType.Collection(DataType.String))     { IsSearchable = true, IsFilterable = true, IsFacetable = true },
                new Field("parkingIncluded", DataType.Boolean)              { IsFilterable = true, IsFacetable = true },
                new Field("lastRenovationDate", DataType.DateTimeOffset)    { IsFilterable = true, IsSortable = true, IsFacetable = true },
                new Field("rating", DataType.Int32)                         { IsFilterable = true, IsSortable = true, IsFacetable = true },
                new Field("location", DataType.GeographyPoint)              { IsFilterable = true, IsSortable = true }
            }
        };

        serviceClient.Indexes.Create(definition);
    }

Este método crea un nuevo objeto `Index` con una lista de objetos `Field` que define el esquema del nuevo índice. Cada campo tiene un nombre, un tipo de datos y varios atributos que definen su comportamiento de búsqueda. Además de campos, puede agregar al índice perfiles de puntuación, proveedores de sugerencias u opciones de CORS (se omiten en el ejemplo para mayor brevedad). Puede encontrar más información sobre el objeto Index y sus partes constituyentes en la referencia del SDK en [MSDN](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.index_members.aspx), así como en la [referencia de la API de REST de Búsqueda de Azure](https://msdn.microsoft.com/library/azure/dn798935.aspx).

### Llenado del índice

El siguiente paso en `Main` consiste en rellenar el índice recién creado. Esto se lleva a cabo mediante el método siguiente:

    private static void UploadDocuments(SearchIndexClient indexClient)
    {
        var documents =
            new Hotel[]
            {
                new Hotel()
                {
                    HotelId = "1058-441",
                    HotelName = "Fancy Stay",
                    BaseRate = 199.0,
                    Category = "Luxury",
                    Tags = new[] { "pool", "view", "concierge" },
                    ParkingIncluded = false,
                    LastRenovationDate = new DateTimeOffset(2010, 6, 27, 0, 0, 0, TimeSpan.Zero),
                    Rating = 5,
                    Location = GeographyPoint.Create(47.678581, -122.131577)
                },
                new Hotel()
                {
                    HotelId = "666-437",
                    HotelName = "Roach Motel",
                    BaseRate = 79.99,
                    Category = "Budget",
                    Tags = new[] { "motel", "budget" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(1982, 4, 28, 0, 0, 0, TimeSpan.Zero),
                    Rating = 1,
                    Location = GeographyPoint.Create(49.678581, -122.131577)
                },
                new Hotel()
                {
                    HotelId = "970-501",
                    HotelName = "Econo-Stay",
                    BaseRate = 129.99,
                    Category = "Budget",
                    Tags = new[] { "pool", "budget" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(1995, 7, 1, 0, 0, 0, TimeSpan.Zero),
                    Rating = 4,
                    Location = GeographyPoint.Create(46.678581, -122.131577)
                },
                new Hotel()
                {
                    HotelId = "956-532",
                    HotelName = "Express Rooms",
                    BaseRate = 129.99,
                    Category = "Budget",
                    Tags = new[] { "wifi", "budget" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(1995, 7, 1, 0, 0, 0, TimeSpan.Zero),
                    Rating = 4,
                    Location = GeographyPoint.Create(48.678581, -122.131577)
                },
                new Hotel()
                {
                    HotelId = "566-518",
                    HotelName = "Surprisingly Expensive Suites",
                    BaseRate = 279.99,
                    Category = "Luxury",
                    ParkingIncluded = false
                }
            };

        try
        {
            var batch = IndexBatch.Upload(documents);
            indexClient.Documents.Index(batch);
        }
        catch (IndexBatchException e)
        {
            // Sometimes when your Search service is under load, indexing will fail for some of the documents in
            // the batch. Depending on your application, you can take compensating actions like delaying and
            // retrying. For this simple demo, we just log the failed document keys and continue.
            Console.WriteLine(
                "Failed to index some of the documents: {0}",
                String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
        }

        // Wait a while for indexing to complete.
        Thread.Sleep(2000);
    }

Este método tiene cuatro partes. La primera crea una matriz de objetos `Hotel` que serán los datos de entrada que cargaremos en el índice. Estos datos están incluidos en el código por motivos de simplicidad. En su propia aplicación, los datos provendrán normalmente de un origen de datos externo, como una base de datos SQL.

En la segunda parte se crea un `IndexBatch` que contiene los documentos. Especifique la operación que desee aplicar al lote en el momento de su creación, en este caso llamando a `IndexBatch.Upload`. A continuación, el lote se carga en el índice de Búsqueda de Azure mediante el método `Documents.Index`.

> [AZURE.NOTE] En este ejemplo nos limitamos a cargar documentos. Si desea combinar los cambios en los documentos existentes o eliminar documentos, puede crear lotes llamando a `IndexBatch.Merge`, `IndexBatch.MergeOrUpload` o `IndexBatch.Delete`. También puede combinar diferentes operaciones en un único lote llamando a `IndexBatch.New`, que selecciona una colección de objetos de `IndexAction`, que indican a Búsqueda de Azure que realice una operación determinada en un documento. Puede crear cada `IndexAction` con su propia operación llamando al método correspondiente como `IndexAction.Merge`, `IndexAction.Upload`, etc.

La tercera parte de este método es un bloque catch que controla un caso de error importante para la indización. Si su servicio Búsqueda de Azure no logra indizar algunos de los documentos del lote, aparece una `IndexBatchException` producida por `Documents.Index`. Esto puede suceder si indiza documentos mientras el servicio está sobrecargado. **Recomendamos encarecidamente controlar este caso de forma explícita en el código.** Puede retrasar la indización de los documentos que dieron error y volver a intentarlo; puede crear un registro y continuar, como hace el ejemplo, o puede adoptar otro enfoque según los requisitos de coherencia de datos de la aplicación.

Por último, el método se retrasa durante dos segundos. La indización ocurre de manera asincrónica en el servicio Búsqueda de Azure, por lo que la aplicación de ejemplo debe esperar unos momentos para asegurarse de que los documentos estén disponibles para la búsqueda. Retrasos así solo suelen ser necesarios en las pruebas, demostraciones y aplicaciones de ejemplo.

#### Gestión de documentos del SDK de .NET

Quizás se pregunte cómo consigue el SDK de Azure para .NET cargar en el índice las instancias de una clase definida por el usuario como `Hotel`. Para responder mejor a esa pregunta, echemos un vistazo a la clase `Hotel`:

    [SerializePropertyNamesAsCamelCase]
    public class Hotel
    {
        public string HotelId { get; set; }

        public string HotelName { get; set; }

        public double? BaseRate { get; set; }

        public string Category { get; set; }

        public string[] Tags { get; set; }

        public bool? ParkingIncluded { get; set; }

        public DateTimeOffset? LastRenovationDate { get; set; }

        public int? Rating { get; set; }

        public GeographyPoint Location { get; set; }

        public override string ToString()
        {
            return String.Format(
                "ID: {0}\tName: {1}\tCategory: {2}\tTags: [{3}]",
                HotelId,
                HotelName,
                Category,
                (Tags != null) ? String.Join(", ", Tags) : String.Empty);
        }
    }

Lo primero que debe tener en cuenta es que cada propiedad pública de `Hotel` corresponde a un campo de la definición del índice, pero con una diferencia fundamental: el nombre de cada campo comienza con una letra minúscula ("mayúsculas y minúsculas Camel"), mientras que el nombre de cada propiedad pública de `Hotel` comienza con una letra mayúscula ("mayúsculas y minúsculas Pascal"). Se trata de un escenario común en las aplicaciones .NET que realizan enlaces de datos cuando el esquema de destino está fuera del control del desarrollador de la aplicación. En lugar de tener que infringir las directrices de nomenclatura de .NET utilizando mayúsculas y minúsculas Camel para los nombres de las propiedades, puede usar el atributo `[SerializePropertyNamesAsCamelCase]` para indicar al SDK que asigne los nombres de las propiedades automáticamente a mayúsculas y minúsculas Camel.

> [AZURE.NOTE] El SDK de .NET de Búsqueda de Azure usa la biblioteca [NewtonSoft JSON.NET](http://www.newtonsoft.com/json/help/html/Introduction.htm) para serializar y deserializar los objetos de modelo personalizados hacia JSON y desde JSON. Puede personalizar esta serialización si es necesario. Puede encontrar más información [aquí](search-dotnet-sdk-migration.md#WhatsNew).

La segunda cosa importante acerca de la clase `Hotel` son los tipos de datos de las propiedades públicas. Los tipos .NET de esas propiedades se asignan a los tipos de campo equivalentes de la definición del índice. Por ejemplo, la propiedad de cadena `Category` se asigna al campo `category`, que es de tipo `Edm.String`. Se dan asignaciones de tipos semejantes entre `bool?` y `Edm.Boolean`, `DateTimeOffset?` y `Edm.DateTimeOffset`, etc. Las reglas específicas para la asignación de tipos se documentan con el método `Documents.Get` en [MSDN](https://msdn.microsoft.com/library/azure/dn931291.aspx).

Esta posibilidad de usar sus propias clases como documentos funciona en ambas direcciones: también puede recuperar los resultados de la búsqueda y hacer que el SDK los deserialice automáticamente a un tipo de su elección, como veremos en la siguiente sección.

> [AZURE.NOTE] El SDK de Búsqueda de Azure para .NET también admite documentos de tipo dinámico mediante la clase `Document`, que es una asignación clave/valor de nombres de campo a valores de campo. Esto es útil en escenarios en los que no se conoce el esquema del índice en el momento del diseño o en los que resulte inconveniente enlazar a clases de modelo específicas. Todos los métodos del SDK que se ocupan de los documentos tienen sobrecargas que funcionan con la clase `Document`, así como sobrecargas de asignación rigurosa que aceptan un parámetro de tipo genérico. En el código de ejemplo de este tutorial solo se utilizan las últimas. Puede encontrar más información acerca de la clase `Document` [aquí](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.document.aspx).

**Nota importante acerca de los tipos de datos**

Al diseñar sus propias clases de modelo para asignar a un índice de Búsqueda de Azure, es recomendable declarar las propiedades de tipos de valor como `bool` y `int` que aceptan valores NULL (por ejemplo: `bool?` en lugar de `bool`). Si usa un tipo de modelo con una propiedad que no acepta valores NULL, tendrá que **garantizar** que ningún documento del índice contiene un valor NULL para el campo correspondiente. Ni el SDK ni el servicio Búsqueda de Azure le permitirá aplicar esto.

Esto no es solo una inquietud hipotética: imagine un escenario donde agregar un nuevo campo a un índice existente que es de tipo `Edm.Int32`. Después de actualizar la definición del índice, todos los documentos tendrán un valor null para ese campo nuevo (ya que todos los tipos aceptan valores NULL en Búsqueda de Azure). Si después utiliza una clase de modelo con una propiedad `int` que no acepta valores NULL para ese campo, obtendrá `JsonSerializationException` así al intentar recuperar documentos:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Por este motivo, recomendamos utilizar tipos que aceptan valores NULL en las clases de modelo como procedimiento recomendado.

### Búsqueda de documentos en el índice

El último paso de la aplicación de ejemplo es buscar algunos documentos en el índice. Esto es lo que hace el método siguiente:

    private static void SearchDocuments(SearchIndexClient indexClient, string searchText, string filter = null)
    {
        // Execute search based on search text and optional filter
        var sp = new SearchParameters();

        if (!String.IsNullOrEmpty(filter))
        {
            sp.Filter = filter;
        }

        DocumentSearchResult<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
        foreach (SearchResult<Hotel> result in response.Results)
        {
            Console.WriteLine(result.Document);
        }
    }

En primer lugar, el método crea un nuevo objeto `SearchParameters`. Esto se utiliza para especificar opciones adicionales para la consulta, como el orden, los filtros, la paginación y las facetas. En este ejemplo, solo vamos a definir la propiedad `Filter`.

El siguiente paso consiste en ejecutar la consulta de búsqueda. Esto se hace mediante el método `Documents.Search`: En este caso, pasamos como una cadena el texto de búsqueda que se usará, además de los parámetros de búsqueda creados anteriormente. También especificamos `Hotel` como el parámetro de tipo para `Documents.Search`, lo que indica al SDK que deserialice los documentos de los resultados de búsqueda en objetos de tipo `Hotel`.

Por último, este método recorre en iteración todas las coincidencias de los resultados de búsqueda e imprime cada documento en la consola.

Veamos más de cerca cómo se llama a este método:

    SearchDocuments(indexClient, searchText: "fancy wifi");

    SearchDocuments(indexClient, searchText: "*", filter: "category eq 'Luxury'");

En la primera llamada estamos buscando todos los documentos que contienen los términos de consulta "fancy" o "wifi". En la segunda llamada, el texto de búsqueda se define como "*", lo que significa "buscar todos los elementos". Puede encontrar más información acerca de la sintaxis de expresiones de consulta de búsqueda [aquí](https://msdn.microsoft.com/library/azure/dn798920.aspx).

La segunda llamada usa una expresión `$filter` de OData, `category eq 'Luxury'`. Esto restringe la búsqueda de forma que devuelva solo aquellos documentos en los que el campo `category` coincida exactamente con la cadena "Luxury". Puede encontrar más información acerca de la sintaxis de OData admitida por Búsqueda de Azure [aquí](https://msdn.microsoft.com/library/azure/dn798921.aspx).

Ahora que sabe lo que hacen estas dos llamadas, le será más fácil ver por qué su resultado tiene el siguiente aspecto:

    Searching documents 'fancy wifi'...

    ID: 1058-441    Name: Fancy Stay        Category: Luxury        Tags: [pool, view, concierge]
    ID: 956-532     Name: Express Rooms     Category: Budget        Tags: [wifi, budget]

    Filter documents with category 'Luxury'...

    ID: 1058-441    Name: Fancy Stay        Category: Luxury        Tags: [pool, view, concierge]
    ID: 566-518     Name: Surprisingly Expensive Suites     Category: Luxury	Tags: []

La primera búsqueda devuelve dos documentos. El primero tiene "Fancy" en el nombre, mientras que el segundo tiene "wifi" en el campo `tags`. La segunda búsqueda devuelve dos documentos, que resultan ser los únicos documentos del índice con el campo `category` definido como "Luxury".

Este paso finaliza el tutorial, pero no se detenga aquí. Los **pasos siguientes** proporcionan recursos adicionales para obtener más información acerca de Búsqueda de Azure.

## Pasos siguientes

- Examine las referencias del [SDK de .NET](https://msdn.microsoft.com/library/azure/dn951165.aspx) y la [API de REST](https://msdn.microsoft.com/library/azure/dn798935.aspx) en MSDN.
- Profundice en sus conocimientos por medio de [vídeos y otros ejemplos y tutoriales](search-video-demo-tutorial-list.md).
- Obtenga información acerca de las características y capacidades de esta versión del SDK de Búsqueda de Azure: [Introducción a Búsqueda de Azure](https://msdn.microsoft.com/library/azure/dn798933.aspx)
- Consulte las [convenciones de nomenclatura](https://msdn.microsoft.com/library/azure/dn857353.aspx) para conocer las reglas que deben seguir los nombres de diversos objetos.
- Consulte los [tipos de datos admitidos](https://msdn.microsoft.com/library/azure/dn798938.aspx) en Búsqueda de Azure.


## Código fuente de aplicación de ejemplo

Este es el código fuente completo de la aplicación de ejemplo que se utiliza en este tutorial. Tenga en cuenta que necesitará reemplazar con sus propios valores el nombre del servicio y los marcadores de posición de claves de API en Program.cs si desea compilar y ejecutar el ejemplo.

Program.cs:

    using System;
    using System.Configuration;
    using System.Linq;
    using System.Threading;
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    using Microsoft.Spatial;

    namespace AzureSearch.SDKHowTo
    {
        class Program
        {
            // This sample shows how to delete, create, upload documents and query an index
            static void Main(string[] args)
            {
                // Put your search service name here. This is the hostname portion of your service URL.
                // For example, if your service URL is https://myservice.search.windows.net, then your
                // service name is myservice.
                string searchServiceName = "myservice";

                string apiKey = "Put your API admin key here."

                SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));

                Console.WriteLine("{0}", "Deleting index...\n");
                DeleteHotelsIndexIfExists(serviceClient);

                Console.WriteLine("{0}", "Creating index...\n");
                CreateHotelsIndex(serviceClient);

                SearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");

                Console.WriteLine("{0}", "Uploading documents...\n");
                UploadDocuments(indexClient);

                Console.WriteLine("{0}", "Searching documents 'fancy wifi'...\n");
                SearchDocuments(indexClient, searchText: "fancy wifi");

                Console.WriteLine("\n{0}", "Filter documents with category 'Luxury'...\n");
                SearchDocuments(indexClient, searchText: "*", filter: "category eq 'Luxury'");

                Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
                Console.ReadKey();
            }

            private static void DeleteHotelsIndexIfExists(SearchServiceClient serviceClient)
            {
                if (serviceClient.Indexes.Exists("hotels"))
                {
                    serviceClient.Indexes.Delete("hotels");
                }
            }

            private static void CreateHotelsIndex(SearchServiceClient serviceClient)
            {
                var definition = new Index()
                {
                    Name = "hotels",
                    Fields = new[]
                    {
                        new Field("hotelId", DataType.String)                       { IsKey = true },
                        new Field("hotelName", DataType.String)                     { IsSearchable = true, IsFilterable = true },
                        new Field("baseRate", DataType.Double)                      { IsFilterable = true, IsSortable = true },
                        new Field("category", DataType.String)                      { IsSearchable = true, IsFilterable = true, IsSortable = true, IsFacetable = true },
                        new Field("tags", DataType.Collection(DataType.String))     { IsSearchable = true, IsFilterable = true, IsFacetable = true },
                        new Field("parkingIncluded", DataType.Boolean)              { IsFilterable = true, IsFacetable = true },
                        new Field("lastRenovationDate", DataType.DateTimeOffset)    { IsFilterable = true, IsSortable = true, IsFacetable = true },
                        new Field("rating", DataType.Int32)                         { IsFilterable = true, IsSortable = true, IsFacetable = true },
                        new Field("location", DataType.GeographyPoint)              { IsFilterable = true, IsSortable = true }
                    }
                };

                serviceClient.Indexes.Create(definition);
            }

            private static void UploadDocuments(SearchIndexClient indexClient)
            {
                var documents =
                    new Hotel[]
                    {
                        new Hotel()
                        {
                            HotelId = "1058-441",
                            HotelName = "Fancy Stay",
                            BaseRate = 199.0,
                            Category = "Luxury",
                            Tags = new[] { "pool", "view", "concierge" },
                            ParkingIncluded = false,
                            LastRenovationDate = new DateTimeOffset(2010, 6, 27, 0, 0, 0, TimeSpan.Zero),
                            Rating = 5,
                            Location = GeographyPoint.Create(47.678581, -122.131577)
                        },
                        new Hotel()
                        {
                            HotelId = "666-437",
                            HotelName = "Roach Motel",
                            BaseRate = 79.99,
                            Category = "Budget",
                            Tags = new[] { "motel", "budget" },
                            ParkingIncluded = true,
                            LastRenovationDate = new DateTimeOffset(1982, 4, 28, 0, 0, 0, TimeSpan.Zero),
                            Rating = 1,
                            Location = GeographyPoint.Create(49.678581, -122.131577)
                        },
                        new Hotel()
                        {
                            HotelId = "970-501",
                            HotelName = "Econo-Stay",
                            BaseRate = 129.99,
                            Category = "Budget",
                            Tags = new[] { "pool", "budget" },
                            ParkingIncluded = true,
                            LastRenovationDate = new DateTimeOffset(1995, 7, 1, 0, 0, 0, TimeSpan.Zero),
                            Rating = 4,
                            Location = GeographyPoint.Create(46.678581, -122.131577)
                        },
                        new Hotel()
                        {
                            HotelId = "956-532",
                            HotelName = "Express Rooms",
                            BaseRate = 129.99,
                            Category = "Budget",
                            Tags = new[] { "wifi", "budget" },
                            ParkingIncluded = true,
                            LastRenovationDate = new DateTimeOffset(1995, 7, 1, 0, 0, 0, TimeSpan.Zero),
                            Rating = 4,
                            Location = GeographyPoint.Create(48.678581, -122.131577)
                        },
                        new Hotel()
                        {
                            HotelId = "566-518",
                            HotelName = "Surprisingly Expensive Suites",
                            BaseRate = 279.99,
                            Category = "Luxury",
                            ParkingIncluded = false
                        }
                    };

                try
                {
                    var batch = IndexBatch.Upload(documents);
                    indexClient.Documents.Index(batch);
                }
                catch (IndexBatchException e)
                {
                    // Sometimes when your Search service is under load, indexing will fail for some of the documents in
                    // the batch. Depending on your application, you can take compensating actions like delaying and
                    // retrying. For this simple demo, we just log the failed document keys and continue.
                    Console.WriteLine(
                        "Failed to index some of the documents: {0}",
                        String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
                }

                // Wait a while for indexing to complete.
                Thread.Sleep(2000);
            }

            private static void SearchDocuments(SearchIndexClient indexClient, string searchText, string filter = null)
            {
                // Execute search based on search text and optional filter
                var sp = new SearchParameters();

                if (!String.IsNullOrEmpty(filter))
                {
                    sp.Filter = filter;
                }

                DocumentSearchResult<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
                foreach (SearchResult<Hotel> result in response.Results)
                {
                    Console.WriteLine(result.Document);
                }
            }
        }
    }

Hotel.cs:

    using System;
    using Microsoft.Azure.Search.Models;
    using Microsoft.Spatial;

    namespace AzureSearch.SDKHowTo
    {
        [SerializePropertyNamesAsCamelCase]
        public class Hotel
        {
            public string HotelId { get; set; }

            public string HotelName { get; set; }

            public double? BaseRate { get; set; }

            public string Category { get; set; }

            public string[] Tags { get; set; }

            public bool? ParkingIncluded { get; set; }

            public DateTimeOffset? LastRenovationDate { get; set; }

            public int? Rating { get; set; }

            public GeographyPoint Location { get; set; }

            public override string ToString()
            {
                return String.Format(
                    "ID: {0}\tName: {1}\tCategory: {2}\tTags: [{3}]",
                    HotelId,
                    HotelName,
                    Category,
                    (Tags != null) ? String.Join(", ", Tags) : String.Empty);
            }
        }
    }

También puede encontrar el código fuente de ejemplo completo [en GitHub](http://aka.ms/search-dotnet-howto).

<!---HONumber=AcomDC_0211_2016-->