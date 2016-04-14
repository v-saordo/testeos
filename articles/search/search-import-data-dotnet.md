<properties
    pageTitle="Importación de datos en Búsqueda de Azure con el SDK para .NET | Microsoft Azure | Servicio de búsqueda hospedado en la nube"
    description="Carga de datos en un índice de Búsqueda de Azure mediante el SDK para .NET."
    services="search"
    documentationCenter=""
    authors="brjohnstmsft"
    manager=""
    editor=""
    tags=""/>

<tags
    ms.service="search"
    ms.devlang="dotnet"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="03/09/2016"
    ms.author="brjohnst"/>

# Importación de datos en Búsqueda de Azure mediante el SDK para .NET
> [AZURE.SELECTOR]
- [Información general](search-what-is-data-import.md)
- [Portal](search-import-data-portal.md)
- [.NET](search-import-data-dotnet.md)
- [REST](search-import-data-rest-api.md)
- [Indexadores](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)

En este artículo se mostrará cómo usar el [SDK de .NET para Búsqueda de Azure](https://msdn.microsoft.com/library/azure/dn951165.aspx) para importar datos en un índice de Búsqueda de Azure. Antes de comenzar este tutorial, debe haber [creado un índice de Búsqueda de Azure](search-create-index-dotnet.md). En este artículo también se asume que ha creado un objeto `SearchServiceClient`, como se muestra en [Creación de un índice de Búsqueda de Azure mediante el SDK para .NET](search-create-index-dotnet.md#CreateSearchServiceClient).

Tenga en cuenta que todo el código de ejemplo de este artículo está escrito en C#. El código fuente completo se puede encontrar [en GitHub](http://aka.ms/search-dotnet-howto).

Para insertar documentos en un índice mediante el SDK. para NET, necesitará:

  1. Crear un objeto `SearchIndexClient` para conectarse al índice de búsqueda.
  2. Crear un `IndexBatch` que contenga los documentos que se van a agregar, modificar o eliminar.
  3. Llamar al método `Documents.Index` del `SearchIndexClient` para enviar el `IndexBatch` al índice de búsqueda.

## I. Creación de una instancia de la clase SearchIndexClient
Para importar datos en el SDK de .NET para Búsqueda de Azure, será preciso crear una instancia de la clase `SearchIndexClient`. Dicha instancia se puede construir, pero es más fácil si ya tiene una instancia de `SearchServiceClient` para llamar a su método `Indexes.GetClient`. Por ejemplo, aquí se muestra cómo obtener un `SearchIndexClient` para el índice denominado "hoteles" de un `SearchServiceClient` denominado `serviceClient`:

```csharp
SearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

> [AZURE.NOTE] En una aplicación de búsqueda típica, el llenado y la administración de índices se controlan mediante un componente independiente de las consultas de búsqueda. `Indexes.GetClient` resulta cómodo para rellenar un índice porque evita la molestia de proporcionar otro `SearchCredentials`. Con este fin, pasa la clave de administrador que se usó para crear el `SearchServiceClient` al nuevo `SearchIndexClient`. Sin embargo, en la parte de la aplicación que ejecuta consultas, es mejor crear directamente el `SearchIndexClient` para poder pasar una clave de consulta en lugar de una clave de administración. Esta situación es coherente con el [principio de privilegios mínimos](https://en.wikipedia.org/wiki/Principle_of_least_privilege) y le ayudará a aumentar la seguridad de su aplicación. Puede encontrar más información acerca de las claves de administración y de consulta en el [referencia de la API de REST de Búsqueda de Azure en MSDN](https://msdn.microsoft.com/library/azure/dn798935.aspx).

`SearchIndexClient` tiene una propiedad `Documents`. Esta propiedad proporciona todos los métodos necesarios para agregar, modificar, eliminar o consultar los documentos del índice.

## II. Elección de la acción de indexación que va a usar
Para importar datos mediante el SDK. de NET, es preciso empaquetar los datos en un objeto `IndexBatch`. Un `IndexBatch` encapsula una colección de objetos `IndexAction`, cada uno de los cuales contiene un documento y una propiedad que indica a Búsqueda de Azure qué acción debe realizar en el documento (cargar, combinar, eliminar, etc.). Dependiendo de cuál de las acciones siguientes elija, se deberán incluir solo ciertos campos para cada documento:

Acción | Descripción | Campos necesarios para cada documento | Notas
--- | --- | --- | ---
`Upload` | Una acción `Upload` es similar a un "upsert" en el que el documento se insertará, si es nuevo, y se actualizará o reemplazará, si ya existe. | la clave, además de cualquier otro campo que desee definir | Al actualizar o reemplazar un documento existente, todos los campos que no estén especificados se establecerán en `null`. Esto ocurre incluso cuando el campo se ha establecido previamente en un valor que no sea nulo.
`Merge` | Permite actualizar un documento existente con los campos especificados. Si el documento no existe en el índice, se producirá un error en la combinación. | la clave, además de cualquier otro campo que desee definir | Cualquier campo que se especifica en una combinación reemplazará al campo existente en el documento. Aquí se incluyen los campos de tipo `DataType.Collection(DataType.String)`. Por ejemplo, si el documento contiene el campo `tags`, cuyo valor es `["budget"]`, y ejecuta una combinación con el valor `["economy", "pool"]` de `tags`, el valor final del campo `tags` será `["economy", "pool"]`. No será `["budget", "economy", "pool"]`.
`MergeOrUpload` | Esta acción se comporta como `Merge` si en el índice ya existe un documento con la clave especificada. Si el documento no existe, se comporta como `Upload` con un documento nuevo. | la clave, además de cualquier otro campo que desee definir |- `Delete` | Quita el documento especificado del índice. | solo la clave | Se ignorarán los campos que especifique, salvo el campo clave. Si desea quitar un campo individual de un documento, use `Merge` en su lugar y establezca el campo explícitamente con el valor NULL.

Puede especificar la acción que desea utilizar con los distintos métodos estáticos de las clases `IndexBatch` y `IndexAction`, como se muestra en la siguiente sección.

## III. Construcción de IndexBatch
Ahora que conoce las acciones que se van a realizar en los documentos, está listo para construir `IndexBatch`. El ejemplo siguiente muestra cómo crear un lote con otras acciones. Tenga en cuenta que en nuestro ejemplo se usa una clase personalizada denominada `Hotel` que se asigna a un documento del índice "hoteles".

```csharp
var actions =
    new IndexAction<Hotel>[]
    {
        IndexAction.Upload(
            new Hotel()
            { 
                HotelId = "1", 
                BaseRate = 199.0, 
                Description = "Best hotel in town",
                DescriptionFr = "Meilleur hôtel en ville",
                HotelName = "Fancy Stay",
                Category = "Luxury", 
                Tags = new[] { "pool", "view", "wifi", "concierge" },
                ParkingIncluded = false, 
                SmokingAllowed = false,
                LastRenovationDate = new DateTimeOffset(2010, 6, 27, 0, 0, 0, TimeSpan.Zero), 
                Rating = 5, 
                Location = GeographyPoint.Create(47.678581, -122.131577)
            }),
        IndexAction.Upload(
            new Hotel()
            { 
                HotelId = "2", 
                BaseRate = 79.99,
                Description = "Cheapest hotel in town",
                DescriptionFr = "Hôtel le moins cher en ville",
                HotelName = "Roach Motel",
                Category = "Budget",
                Tags = new[] { "motel", "budget" },
                ParkingIncluded = true,
                SmokingAllowed = true,
                LastRenovationDate = new DateTimeOffset(1982, 4, 28, 0, 0, 0, TimeSpan.Zero),
                Rating = 1,
                Location = GeographyPoint.Create(49.678581, -122.131577)
            }),
        IndexAction.MergeOrUpload(
            new Hotel() 
            { 
                HotelId = "3", 
                BaseRate = 129.99,
                Description = "Close to town hall and the river"
            }),
        IndexAction.Delete(new Hotel() { HotelId = "6" })
    };

var batch = IndexBatch.New(actions);
```

En este caso, se van a usar `Upload`, `MergeOrUpload` y `Delete` como acciones de búsqueda, tal como especifican los métodos a los que se llama en la clase `IndexAction`.

Se supone que este índice "hoteles" de ejemplo ya está relleno con varios documentos. Observe que no tuvimos que especificar todos los campos posibles del documento al utilizar `MergeOrUpload` y que solo especificamos la clave del documento (`HotelId`) al usar `Delete`.

Además, tenga en cuenta que en las solicitudes de indexación individuales solo se puede incluir un máximo de 1000 documentos.

> [AZURE.NOTE] En este ejemplo, aplicamos acciones diferentes a documentos diferentes. Si deseara realizar las mismas acciones en todos los documentos del lote, en lugar de llamar a `IndexBatch.New`, podría utilizar los restantes métodos estáticos de `IndexBatch`. Por ejemplo, podría crear lotes mediante una llamada a `IndexBatch.Merge`, `IndexBatch.MergeOrUpload` o `IndexBatch.Delete`. Estos métodos toman una colección de documentos (objetos del tipo `Hotel` en este ejemplo), en lugar de objetos `IndexAction`.

## IV. Importación de datos en el índice
Ahora que tiene un objeto `IndexBatch` inicializado, puede enviarlo al índice, para lo que debe realizar una llamada a `Documents.Index` en el objeto `SearchIndexClient`. En el ejemplo siguiente se muestra cómo llamar a `Index`, así como algunos pasos adicionales que es preciso llevar a cabo:

```csharp
try
{
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

Console.WriteLine("Waiting for documents to be indexed...\n");
Thread.Sleep(2000);
```

Tenga en cuenta el `try`/`catch` que rodea la llamada al método `Index`. El bloque catch controla un caso de error importante de indexación. Si su servicio Búsqueda de Azure no logra indizar algunos de los documentos del lote, aparece una `IndexBatchException` producida por `Documents.Index`. Esto puede suceder si indiza documentos mientras el servicio está sobrecargado. **Recomendamos encarecidamente controlar este caso de forma explícita en el código.** Puede retrasar la indización de los documentos que dieron error y volver a intentarlo; puede crear un registro y continuar, como hace el ejemplo, o puede adoptar otro enfoque según los requisitos de coherencia de datos de la aplicación.

Por último, el código del ejemplo anterior se retrasa dos segundos. La indización ocurre de manera asincrónica en el servicio Búsqueda de Azure, por lo que la aplicación de ejemplo debe esperar unos momentos para asegurarse de que los documentos estén disponibles para la búsqueda. Retrasos así solo suelen ser necesarios en las pruebas, demostraciones y aplicaciones de ejemplo.

<a name="HotelClass"></a>
### Gestión de documentos del SDK de .NET

Quizás se pregunte cómo consigue el SDK de Azure para .NET cargar en el índice las instancias de una clase definida por el usuario como `Hotel`. Para ayudar a responder esa pregunta, examinemos la clase `Hotel`, que se asigna al esquema de índice definido en [Creación de un índice de Búsqueda de Azure mediante el SDK para .NET](search-create-index-dotnet.md#DefineIndex):

```csharp
[SerializePropertyNamesAsCamelCase]
public partial class Hotel
{
    public string HotelId { get; set; }

    public double? BaseRate { get; set; }

    public string Description { get; set; }

    [JsonProperty("description_fr")]
    public string DescriptionFr { get; set; }

    public string HotelName { get; set; }

    public string Category { get; set; }

    public string[] Tags { get; set; }

    public bool? ParkingIncluded { get; set; }

    public bool? SmokingAllowed { get; set; }

    public DateTimeOffset? LastRenovationDate { get; set; }

    public int? Rating { get; set; }

    public GeographyPoint Location { get; set; }

    // ToString() method omitted for brevity...
}
```

Lo primero que debe tener en cuenta es que cada propiedad pública de `Hotel` corresponde a un campo de la definición del índice, pero con una diferencia fundamental: el nombre de cada campo comienza con una letra minúscula ("mayúsculas y minúsculas Camel"), mientras que el nombre de cada propiedad pública de `Hotel` comienza con una letra mayúscula ("mayúsculas y minúsculas Pascal"). Se trata de un escenario común en las aplicaciones .NET que realizan enlaces de datos cuando el esquema de destino está fuera del control del desarrollador de la aplicación. En lugar de tener que infringir las directrices de nomenclatura de .NET utilizando mayúsculas y minúsculas Camel para los nombres de las propiedades, puede usar el atributo `[SerializePropertyNamesAsCamelCase]` para indicar al SDK que asigne los nombres de las propiedades automáticamente a mayúsculas y minúsculas Camel.

> [AZURE.NOTE] El SDK de .NET para Búsqueda de Azure usa la biblioteca [NewtonSoft JSON.NET](http://www.newtonsoft.com/json/help/html/Introduction.htm) para serializar y deserializar los objetos de modelo personalizados en JSON y de este. Puede personalizar esta serialización si es necesario. Puede encontrar más detalles en [Actualización a la versión 1.1 del SDK de .NET para Búsqueda de Azure](search-dotnet-sdk-migration.md#WhatsNew). Un ejemplo de ello es el uso del atributo `[JsonProperty]` en la propiedad `DescriptionFr` del código de ejemplo anterior.

La segunda cosa importante acerca de la clase `Hotel` son los tipos de datos de las propiedades públicas. Los tipos .NET de esas propiedades se asignan a los tipos de campo equivalentes de la definición del índice. Por ejemplo, la propiedad de cadena `Category` se asigna al campo `category`, que es de tipo `DataType.String`. Se dan asignaciones de tipos semejantes entre `bool?` y `DataType.Boolean`, `DateTimeOffset?` y `DataType.DateTimeOffset`, etc. Las reglas específicas para la asignación de tipos se documentan con el método `Documents.Get` en [MSDN](https://msdn.microsoft.com/library/azure/dn931291.aspx).

La capacidad de usar sus propias clases como documentos funciona en ambas direcciones; también puede recuperar los resultados de la búsqueda y hacer que el SDK los deserialice automáticamente en el tipo que prefiera, como se muestra en el [siguiente artículo](search-query-dotnet.md).

> [AZURE.NOTE] El SDK de Búsqueda de Azure para .NET también admite documentos de tipo dinámico mediante la clase `Document`, que es una asignación clave/valor de nombres de campo a valores de campo. Esto es útil en escenarios en los que no se conoce el esquema del índice en el momento del diseño o en los que resulte inconveniente enlazar a clases de modelo específicas. Todos los métodos del SDK que se ocupan de los documentos tienen sobrecargas que funcionan con la clase `Document`, así como sobrecargas de asignación rigurosa que aceptan un parámetro de tipo genérico. En el código de ejemplo de este artículo solo se utilizan las últimas. Puede encontrar más información acerca de la clase `Document` [en MSDN](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.document.aspx).

**Nota importante acerca de los tipos de datos**

Al diseñar sus propias clases de modelo para asignarlas a un índice de Búsqueda de Azure, se recomienda declarar las propiedades de los tipos de valor, como `bool` y `int`, que aceptan valores NULL (por ejemplo, `bool?` en lugar de `bool`). Si usa una propiedad que no acepte valores NULL, tendrá que **garantizar** que ningún documento del índice contiene un valor NULL para el campo correspondiente. Ni el SDK ni el servicio Búsqueda de Azure le permitirá aplicar esto.

Este no es solo un problema hipotética: imagine un escenario en el que agrega un campo nuevo a un índice existente que es del tipo `DataType.Int32`. Después de actualizar la definición del índice, todos los documentos tendrán un valor null para ese campo nuevo (ya que todos los tipos aceptan valores NULL en Búsqueda de Azure). Si después utiliza una clase de modelo con una propiedad `int` que no acepta valores NULL para ese campo, obtendrá una excepción `JsonSerializationException` similar a esta al intentar recuperar documentos:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Por este motivo, recomendamos utilizar tipos que aceptan valores NULL en las clases de modelo como procedimiento recomendado.

## Pasos siguientes
Después de rellenar el índice de Búsqueda de Azure, estará listo para iniciar la emisión de consultas para buscar documentos. Para más información, consulte [Realización de una consulta en el índice de Búsqueda de Azure mediante el SDK para .NET](search-query-dotnet.md).

<!---HONumber=AcomDC_0309_2016-->