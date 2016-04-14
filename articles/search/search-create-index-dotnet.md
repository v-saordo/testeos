<properties
    pageTitle="Creación de un índice de Búsqueda de Azure mediante el SDK para .NET | Microsoft Azure | Servicio de búsqueda hospedado en la nube"
    description="Creación de un índice en código mediante SDK de .NET para Búsqueda de Azure"
    services="search"
    documentationCenter=""
    authors="brjohnstmsft"
    manager=""
    editor=""
    tags="azure-portal"/>

<tags
    ms.service="search"
    ms.devlang="dotnet"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="03/09/2016"
    ms.author="brjohnst"/>

# Creación de un índice de Búsqueda de Azure mediante el SDK para .NET
> [AZURE.SELECTOR]
- [Información general](search-what-is-an-index.md)
- [Portal](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [REST](search-create-index-rest-api.md)


Este artículo le guiará a través del proceso de creación de un [índice](https://msdn.microsoft.com/library/azure/dn798941.aspx) de Búsqueda de Azure usando el [SDK de .NET para Búsqueda de Azure](https://msdn.microsoft.com/library/azure/dn951165.aspx).

Antes de seguir con esta guía y crear un índice, debe haber [creado ya un servicio de Búsqueda de Azure](search-create-service-portal.md).

Tenga en cuenta que todo el código de ejemplo de este artículo está escrito en C#. El código fuente completo se puede encontrar [en GitHub](http://aka.ms/search-dotnet-howto).

## I. Identificación de la clave de API de administración del servicio de Búsqueda de Azure
Ahora que ha aprovisionado un servicio de Búsqueda de Azure, está casi preparado para emitir solicitudes en el punto de conexión de servicio mediante el SDK para .NET. En primer lugar, tiene que obtener una de las claves de API de administrador que se generaron para aprovisionar el servicio de búsqueda. El SDK para .NET enviará esta clave de API en cada solicitud al servicio. Tener una clave válida genera la confianza, solicitud a solicitud, entre la aplicación que envía la solicitud y el servicio que se encarga de ella.

1. Para buscar las claves de API del servicio debe iniciar sesión en el [Portal de Azure](https://portal.azure.com/)
2. Vaya a la hoja de servicio de Búsqueda de Azure
3. Haga clic en el icono "Claves"

El servicio tendrá *claves de administración* y *claves de consulta*.

  - Sus *claves de administración* principal y secundaria permiten conceder derechos completos para todas las operaciones, incluida la capacidad para administrar el servicio, crear y eliminar índices, indexadores y orígenes de datos. Existen dos claves, de forma que puede usar la clave secundaria si decide volver a generar la clave principal y viceversa.
  - Las *claves de consulta* conceden acceso de solo lectura a índices y documentos y normalmente se distribuyen entre las aplicaciones cliente que emiten solicitudes de búsqueda.

Para crear un índice, puede usar su clave de administración principal o la secundaria.

<a name="CreateSearchServiceClient"></a>
## II. Creación de una instancia de la clase SearchServiceClient
Para empezar a usar el SDK de .NET para Búsqueda de Azure, tendrá que crear una instancia de la clase `SearchServiceClient`. Esta clase tiene varios constructores. El que desea tiene el nombre del servicio de búsqueda y un objeto `SearchCredentials` como parámetros. `SearchCredentials` contiene su clave de API.

El código que aparece a continuación crea un nuevo `SearchServiceClient` con valores para el nombre de servicio de búsqueda y la clave de API que se almacenan en el archivo de configuración de la aplicación (`app.config` o `web.config`):

```csharp
string searchServiceName = ConfigurationManager.AppSettings["SearchServiceName"];
string adminApiKey = ConfigurationManager.AppSettings["SearchServiceAdminApiKey"];

SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
```

`SearchServiceClient` tiene una propiedad `Indexes`. Esta propiedad proporciona todos los métodos que necesita para crear, enumerar, actualizar o eliminar los índices de Búsqueda de Azure.

> [AZURE.NOTE] La clase `SearchServiceClient` administra las conexiones con el servicio de búsqueda. Para evitar la apertura de un número excesivo de conexiones, debe intentar, si es posible, compartir una única instancia de `SearchServiceClient` en la aplicación. Sus métodos son seguros para subprocesos lo que permite habilitar este tipo de uso compartido.

<a name="DefineIndex"></a>
## III. Definición del índice de Búsqueda de Azure con la clase `Index`
Una única llamada al método `Indexes.Create` creará el índice. Este método toma como parámetro un objeto `Index` que define el índice de Búsqueda de Azure. Tiene que crear un objeto `Index` e inicializarlo de la forma siguiente:

1. Establezca la propiedad `Name` del objeto `Index` en el nombre del índice.
2. Establezca la propiedad `Fields` del objeto `Index` en una matriz de objetos `Field`. Cada uno de los objetos `Field` define el comportamiento de un campo en el índice. Puede proporcionar el nombre del campo en el constructor, junto con el tipo de datos (o el analizador de campos de cadena). También puede establecer otras propiedades, como `IsSearchable`, `IsFilterable`, etc.

Es importante que tenga en cuenta sus necesidades de negocio y experiencia de usuario al diseñar el índice, ya que a cada `Field` se le tienen que asignar las [propiedades adecuadas](https://msdn.microsoft.com/library/azure/dn798941.aspx). Estas propiedades controlan qué características de búsqueda (filtrado, facetas, ordenación, búsqueda de texto completo, etc.) se aplican a qué campos. Para cualquier propiedad que no establezca de forma explícita, el valor predeterminado de la clase `Field` es deshabilitar la característica de búsqueda correspondiente, excepto si la habilita específicamente.

En nuestro ejemplo, hemos llamado a nuestro índice "hoteles" y definido los campos como sigue:

```csharp
var definition = new Index()
{
    Name = "hotels",
    Fields = new[] 
    { 
        new Field("hotelId", DataType.String)                       { IsKey = true, IsFilterable = true },
        new Field("baseRate", DataType.Double)                      { IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("description", DataType.String)                   { IsSearchable = true },
        new Field("description_fr", AnalyzerName.FrLucene),
        new Field("hotelName", DataType.String)                     { IsSearchable = true, IsFilterable = true, IsSortable = true },
        new Field("category", DataType.String)                      { IsSearchable = true, IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("tags", DataType.Collection(DataType.String))     { IsSearchable = true, IsFilterable = true, IsFacetable = true },
        new Field("parkingIncluded", DataType.Boolean)              { IsFilterable = true, IsFacetable = true },
        new Field("smokingAllowed", DataType.Boolean)               { IsFilterable = true, IsFacetable = true },
        new Field("lastRenovationDate", DataType.DateTimeOffset)    { IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("rating", DataType.Int32)                         { IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("location", DataType.GeographyPoint)              { IsFilterable = true, IsSortable = true }
    }
};
```

Hemos elegido cuidadosamente los valores de propiedad para cada `Field`, basándonos en la idea que tenemos sobre cómo se van a utilizar en una aplicación. Por ejemplo, es probable que las personas que estén buscando hoteles estarán interesadas en coincidencias de palabras clave en el campo `description`, por eso hemos habilitado la búsqueda de texto completo para ese campo estableciendo `IsSearchable` en `true`.

Tenga en cuenta que solo puede designar un campo en el tipo de índice `DataType.String` como campo _clave_, estableciendo `IsKey` en `true` (vea `hotelId` en el ejemplo anterior).

La definición del índice anterior utiliza un analizador de lenguaje personalizado para el campo `description_fr` porque está diseñado para almacenar texto en francés. Consulte [Compatibilidad de idioma (API de REST de servicio de búsqueda de Azure)](https://msdn.microsoft.com/library/azure/dn879793.aspx), así como la correspondiente [entrada de blog](https://azure.microsoft.com/blog/language-support-in-azure-search/) para más información acerca de los analizadores de idiomas.

> [AZURE.NOTE]  Tenga en cuenta que al pasar `AnalyzerName.FrLucene` en el constructor, el `Field` automáticamente será del tipo `DataType.String` y tendrá `IsSearchable` establecido en `true`.

## IV. creación del índice
Ahora que tiene un objeto `Index` inicializado, puede crear el índice simplemente realizando una llamada a `Indexes.Create` en el objeto `SearchServiceClient`:

```csharp
serviceClient.Indexes.Create(definition);
```

Para una solicitud correcta, el método realizará la devolución normalmente. Si hay un problema con la solicitud, como un parámetro no válido, el método producirá una `CloudException`.

Cuando haya terminado con un índice y desee eliminarlo, simplemente llame al método `Indexes.Delete` en su `SearchServiceClient`. Por ejemplo, así es cómo se eliminaría el índice "hoteles":

```csharp
serviceClient.Indexes.Delete("hotels");
```

> [AZURE.NOTE] El código de ejemplo de este artículo usa los métodos sincrónicos del SDK de Búsqueda de Azure para .NET por motivos de simplicidad. En sus propias aplicaciones, recomendamos que use métodos asincrónicos para mantener su escalabilidad y capacidad de respuesta. Por ejemplo, en los ejemplos anteriores podría utilizar `CreateAsync` y `DeleteAsync` en lugar de `Create` y `Delete`.

## Pasos siguientes
Después de crear un índice de Búsqueda de Azure, ya podrá cargar el contenido en el índice y empezar la búsqueda de los datos. Para más información, consulte [Importación de datos a Búsqueda de Azure mediante el SDK para .NET](search-import-data-dotnet.md).

<!---HONumber=AcomDC_0309_2016-->