<properties 
    pageTitle="Trabajo con datos geoespaciales en Azure DocumentDB | Microsoft Azure" 
    description="Entender cómo crear, indexar y consultar los objetos espaciales con DocumentDB de Azure." 
    services="documentdb" 
    documentationCenter="" 
    authors="arramac" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="documentdb" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="data-services" 
    ms.date="02/03/2016" 
    ms.author="arramac"/>
    
# Trabajo con datos geoespaciales en Azure DocumentDB

Este artículo es una introducción a la funcionalidad geoespacial en [Azure DocumentDB](https://azure.microsoft.com/services/documentdb/). Después de leer este artículo, podrá responder a las preguntas siguientes:

- ¿Cómo almaceno los datos espaciales en Azure DocumentDB?
- ¿Cómo puedo consultar los datos geoespaciales en Azure DocumentDB en SQL y LINQ?
- ¿Qué tengo que hacer para habilitar y deshabilitar la indexación en DocumentDB?

Consulte el [proyecto Github](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Geospatial/Program.cs) para obtener ejemplos de código.

## Introducción a los datos espaciales

Los datos espaciales describen la posición y la forma de los objetos en el espacio. En la mayoría de las aplicaciones, estos se refieren a objetos situados la tierra, y son por ello datos geoespaciales. Los datos espaciales pueden usarse para representar la ubicación de una persona, un lugar de interés o el límite de una ciudad o un lago. Casos de uso más comunes suelen implicar las consultas de búsqueda por proximidad, por ejemplo, "encontrar todas las cafeterías cerca de la ubicación actual".

### GeoJSON
DocumentDB admite la indexación y consulta de datos de puntos geoespaciales que se representan mediante la [especificación GeoJSON](http://geojson.org/geojson-spec.html). Las estructuras de datos de GeoJSON son siempre objetos JSON válidos, por lo que se pueden almacenar y consultar mediante DocumentDB sin tener que usar herramientas o bibliotecas especializadas El SDK de DocumentDB proporcionan clases y métodos auxiliares que facilitan el trabajo con datos espaciales.

### Elementos points, linestrings y polygons
Un **punto** denota una posición única en el espacio. En los datos geoespaciales, un punto representa la ubicación exacta, que puede ser una dirección de una tienda de ultramarinos, un quiosco, un automóvil o una ciudad. Un punto se representa en GeoJSON (y DocumentDB) mediante su par de coordenadas o su longitud y latitud. Este es un ejemplo JSON para un punto.

**Puntos en DocumentDB**

    {
       "type":"Point",
       "coordinates":[ 31.9, -4.8 ]
    }

>[AZURE.NOTE] La especificación de GeoJSON especifica primero la longitud y segundo la latitud. Al igual que en otras aplicaciones de mapeado, la longitud y la latitud son ángulos y se representan en grados. Los valores de longitud se miden a partir del meridiano cero y están comprendidos entre -180 y 180.0 grados, mientras que los valores de latitud se miden a partir del Ecuador y están comprendidos entre -90,0 y 90,0 grados.
>
> DocumentDB interpreta las coordenadas tal como están representadas por el sistema de referencia WGS-84. Consulte a continuación para obtener más detalles acerca de los sistemas de coordenadas de referencia.

Esto se puede insertar en un documento DocumentDB tal como se muestra en este ejemplo de un perfil de usuario que contiene datos de ubicación:

**Uso de perfil con una ubicación almacenada en DocumentDB**

    {
       "id":"documentdb-profile",
       "screen_name":"@DocumentDB",
       "city":"Redmond",
       "topics":[ "NoSQL", "Javascript" ],
       "location":{
          "type":"Point",
          "coordinates":[ 31.9, -4.8 ]
       }
    }

Además de los puntos, GeoJSON también admite LineStrings y polígonos. Los elementos **LineStrings** representan una serie de dos o más puntos en el espacio y los segmentos que los conectan. En los datos geoespaciales los elementos linestrings se usan normalmente para representar autopistas o ríos. Un **polígono** es un área delimitada por puntos conectados que forman un elemento LineString cerrado. Los polígonos se usan normalmente para representar formaciones naturales como lagos, o jurisdicciones políticas como estados y ciudades. Este es un ejemplo de un polígono en DocumentDB.

**Polígonos en DocumentDB**

    {
       "type":"Polygon",
       "coordinates":[
           [ 31.8, -5 ],
           [ 31.8, -4.7 ],
           [ 32, -4.7 ],
           [ 32, -5 ],
           [ 31.8, -5 ]
       ]
    }

>[AZURE.NOTE] La especificación de GeoJSON requiere que para que un polígono sea válido, el último par de coordenadas proporcionado tiene que ser el mismo que el primero, para así crear una forma cerrada.
>
>El orden de los puntos dentro de un polígono debe especificarse siguiendo el sentido contrario al de las agujas del reloj. Un polígono cuyos puntos se hayan especificado en el sentido de las agujas del reloj representa el inverso de la región dentro de él.

Además de puntos, LineStrings y polígonos, GeoJSON también especifica la representación de cómo agrupar varias ubicaciones geoespaciales, así como la forma de asociar propiedades arbitrarias a la ubicación geográfica como un **Característica**. Dado que estos objetos son válidos en JSON, se puede almacenar y procesar todos en DocumentDB. Sin embargo, DocumentDB solo admite la indización automática de puntos.

### Sistemas de coordenadas de referencia

Dado que la forma de la tierra es irregular, las coordenadas de los datos geoespaciales se representa en muchos sistemas de coordenadas de referencia (CRS), cada uno con sus propios marcos de referencia y unidades de medida. Por ejemplo, la "National Grid of Britain" es un sistema de referencia muy preciso para el Reino Unido, pero no fuera de él.

El sistema de coordenadas de referencia más popular en uso hoy en día es el Sistema Geodésico Mundial [WGS 84](http://earth-info.nga.mil/GandG/wgs84/). Los dispositivos GPS y muchos servicios de mapeado como Google Maps y API de Bing Maps, usan WGS 84. DocumentDB admite indexación y consulta de datos geoespaciales usando solo el sistema WGS 84.

## Creación de documentos con datos espaciales
Al crear documentos que contengan valores GeoJSON, se indizan automáticamente con un índice espacial de acuerdo con la directiva de indexación de la colección. Si está trabajando con un SDK DocumentDB en un lenguaje dinámico como Python o Node.js, debe crear especificaciones GeoJSON válidas.

**Creación de documentos con datos geoespaciales en Node.js**

    var userProfileDocument = {
       "name":"documentdb",
       "location":{
          "type":"Point",
          "coordinates":[ -122.12, 47.66 ]
       }
    };

    client.createDocument(collectionLink, userProfileDocument, function (err, created) {
        // additional code within the callback
    });

Si está trabajando con SDK de .NET (o Java), puede usar las nuevas clases de punto y polígonos en el espacio de nombres Microsoft.Azure.Documents.Spatial para insertar información de ubicación dentro de los objetos de la aplicación. Estas clases ayudan a simplificar la serialización y deserialización de datos espaciales en GeoJSON.

**Creación de documentos con datos geoespaciales en .NET**

    using Microsoft.Azure.Documents.Spatial;
    
    public class UserProfile
    {
        [JsonProperty("name")]
        public string Name { get; set; }

        [JsonProperty("location")]
        public Point Location { get; set; }
        
        // More properties
    }
    
    await client.CreateDocumentAsync(
        collection.SelfLink, 
        new UserProfile 
        { 
            Name = "documentdb", 
            Location = new Point (-122.12, 47.66) 
        });

Si no dispone de la información de latitud y longitud, pero tiene los nombres de ubicación como ciudad o país o direcciones físicas, puede buscar las coordenadas reales mediante un servicio de codificación geográfica como servicios de REST de Bing Maps. Obtener más información acerca de la codificación geográfica de Bing Maps [aquí](https://msdn.microsoft.com/library/ff701713.aspx).

## Consulta de tipos espaciales

Ahora que ya hemos visto cómo insertar datos geoespaciales, echemos un vistazo a cómo consultar estos datos mediante DocumentDB con SQL y LINQ.

### Funciones integradas SQL espaciales
DocumentDB admite las siguientes funciones integradas de Open Geospatial Consortium (OGC) para realizar consultas geoespaciales. Para obtener más detalles sobre el conjunto completo de funciones integradas en el lenguaje SQL, consulte [Base de datos de documentos de consulta](documentdb-sql-query.md).

<table>
<tr>
  <td><strong>Uso</strong></td>
  <td><strong>Descripción</strong></td>
</tr>
<tr>
  <td>ST_DISTANCE (point_expr, point_expr)</td>
  <td>Devuelve la distancia entre las dos expresiones de punto de GeoJSON.</td>
</tr>
<tr>
  <td>ST_WITHIN (point_expr, polygon_expr)</td>
  <td>Devuelve una expresión booleana que indica si el punto de GeoJSON especificado en el primer argumento está dentro del polígono GeoJSON en el segundo argumento.</td>
</tr>
<tr>
  <td>ST_ISVALID</td>
  <td>Devuelve un valor booleano que indica si la expresión de punto o polígono GeoJSON especificada es válida.</td>
</tr>
<tr>
  <td>ST_ISVALIDDETAILED</td>
  <td>Devuelve un valor JSON que contiene un valor booleano si la expresión de punto o polígono GeoJSON especificada es válida; si no es válida, devuelve también el motivo en forma de valor de cadena.</td>
</tr>
</table>

Las funciones espaciales pueden usarse para realizar consultas de proximidad con datos espaciales. Por ejemplo, aquí hay una consulta que devuelve todos los documentos de la familia que estén dentro de un radio de 30 km de la ubicación especificada mediante la función integrada ST\_DISTANCE.

**Consultar**

    SELECT f.id 
    FROM Families f 
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000

**Resultados**

    [{
      "id": "WakefieldFamily"
    }]

Si incluye la indexación espacial en la directiva de indexación, las "consultas de distancia" se atenderán eficazmente a través del índice. Para obtener más detalles sobre la indexación espacial, consulte la sección siguiente. Aunque no tenga un índice espacial para las rutas de acceso especificadas, aún podrá realizar consultas espaciales mediante la especificación del encabezado de solicitud `x-ms-documentdb-query-enable-scan` con el valor establecido en "true". En. NET, para hacerlo es preciso pasar el argumento **FeedOptions** opcional en consultas en las que [EnableScanInQuery](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.enablescaninquery.aspx#P:Microsoft.Azure.Documents.Client.FeedOptions.EnableScanInQuery) está establecido en true.

ST\_WITHIN puede usarse para comprobar si un punto se encuentra dentro de un polígono. Normalmente, los polígonos se usan para representar límites, como códigos postales, límites estatales o formaciones naturales. Una vez más, si incluye la indexación espacial en la directiva de indexación, las consultas "interiores" se atenderán eficazmente a través del índice.

Los argumentos de polígono de ST\_WITHIN solo pueden contener un anillo individual, es decir, los polígonos no deben contener orificios. En [Límites y cuotas de DocumentDB](documentdb-limits.md) encontrará el número máximo de puntos permitidos en un polígono para una consulta ST\_WITHIN.

**Consultar**

    SELECT * 
    FROM Families f 
    WHERE ST_WITHIN(f.location, {
    	'type':'Polygon', 
    	'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
    })

**Resultados**

    [{
      "id": "WakefieldFamily",
    }]
    
>[AZURE.NOTE] De forma parecida a cómo funcionan los tipos no coincidentes en una consulta de DocumentDB, si el valor de ubicación especificado en cualquier argumento está mal formado o no es válido, se evaluará como **sin definir** y el documento evaluado se omite de los resultados de la consulta. Si la consulta no devuelve resultados, ejecute ST\_ISVALIDDETAILED para depurarla y saber por qué el tipo espacial no es válido.

ST\_ISVALID y ST\_ISVALIDDETAILED pueden usarse para comprobar si un objeto espacial es válido. Por ejemplo, la consulta siguiente comprueba la validez de un punto con un valor de latitud fuera del intervalo (-132,8). ST\_ISVALID devuelve solo un valor booleano y ST\_ISVALIDDETAILED devuelve el valor booleano y una cadena que contiene el motivo por el que se considera no válida.

** Consultar **

    SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })

**Resultados**

    [{
      "$1": false
    }]

Estas funciones también se pueden usar para validar polígonos. Por ejemplo, aquí usamos ST\_ISVALIDDETAILED para validar un polígono que no está cerrado.

**Consultar**

    SELECT ST_ISVALIDDETAILED({ "type": "Polygon", "coordinates": [[ 
    	[ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] 
    	]]})

**Resultados**

    [{
       "$1": { 
      	  "valid": false, 
      	  "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a polygon must have the same start and end points." 
      	}
    }]
    
### Consultas LINQ en el SDK de .NET

El SDK de .NET de DocumentDB también proporciona métodos de código auxiliar `Distance()` y `Within()` para su uso dentro de las expresiones LINQ. El proveedor LINQ de DocumentDB traduce estas llamadas al método a las llamadas de función integrada de SQL equivalentes (ST\_DISTANCE y ST\_WITHIN respectivamente).

Este es un ejemplo de una consulta LINQ que busca todos los documentos de la colección de DocumentDB cuyo valor de "ubicación" se encuentra en un radio de 30km del punto especificado mediante LINQ.

**Consulta LINQ de distancia**

    foreach (UserProfile user in client.CreateDocumentQuery<UserProfile>(collection.SelfLink)
        .Where(u => u.ProfileType == "Public" && a.Location.Distance(new Point(32.33, -4.66)) < 30000))
    {
        Console.WriteLine("\t" + user);
    }

De forma similar, aquí vemos una consulta para buscar todos los documentos cuyo "ubicación" está dentro del cuadro o polígono especificado.

**Consulta LINQ interna**

    Polygon rectangularArea = new Polygon(
        new[]
        {
            new LinearRing(new [] {
                new Position(31.8, -5),
                new Position(32, -5),
                new Position(32, -4.7),
                new Position(31.8, -4.7),
                new Position(31.8, -5)
            })
        });

    foreach (UserProfile user in client.CreateDocumentQuery<UserProfile>(collection.SelfLink)
        .Where(a => a.Location.Within(rectangularArea)))
    {
        Console.WriteLine("\t" + user);
    }


Ahora que hemos visto cómo consultar documentos con LINQ y SQL, echemos un vistazo a cómo configurar DocumentDB para los índices espaciales.

## Indización

Como se describe en el artículo [Indexación independiente de esquemas con Azure DocumentDB](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf), diseñamos el motor de base de datos de DocumentDB para que sea realmente independiente de los esquemas y proporcioné compatibilidad de primera clase con JSON. Ahora el motor de base de datos de escritura optimizado de DocumentDB también entiende de forma nativa los datos espaciales representados en el estándar GeoJSON.

En pocas palabras, la geometría se proyecta a partir de coordenadas geodésicas en un plano 2D, y a continuación, se divide progresivamente en celdas con un **quadtree**. Estas celdas se asignan a 1D según la ubicación de la celda dentro de una **curva de Hilbert**, que conserva la localidad de los puntos. Además al indexar los datos de ubicación, estos pasan por un proceso conocido como **teselación**, es decir, todas las celdas que se cruzan en una ubicación se identifican y se almacenan como claves en el índice de DocumentDB. En el momento de la consulta, los argumentos como puntos y polígonos también se teselan para extraer los intervalos de Id. de celda pertinentes, y luego se usan para recuperar los datos del índice.

Si especifica una directiva de indexación que incluye el índice espacial para / * (todas las rutas de acceso), entonces todos los puntos dentro de la colección se indexan para que las consultas espaciales resulten eficaces (ST\_WITHIN y ST\_DISTANCE). Los índices espaciales no tienen un valor de precisión y usan siempre un valor de precisión predeterminado.

El siguiente fragmento JSON, muestra una directiva de indexación con la indexación espacial habilitada, es decir, indexar cualquier punto GeoJSON que se encuentre dentro de los documentos para la consulta espacial. Si va a modificar la directiva de indexación mediante el Portal de Azure, puede especificar el siguiente JSON para que la directiva de indexación habilite la indexación espacial en la colección.

**JSON de directiva de indexación de una colección con la característica espacial habilitada**

    {
       "automatic":true,
       "indexingMode":"Consistent",
       "includedPaths":[
          {
             "path":"/*",
             "indexes":[
                {
                   "kind":"Hash",
                   "dataType":"String",
                   "precision":3
                },
                {
                   "kind":"Range",
                   "dataType":"Number",
                   "precision":-1
                },
                {
                   "kind":"Spatial",
                   "dataType":"Point"
                }
             ]
          }
       ],
       "excludedPaths":[
       ]
    }

Aquí vemos un fragmento de código de .NET que muestra cómo crear una colección con índices espaciales activados para todas las rutas que contengan puntos.

**Creación de una colección con indexación espacial**

    IndexingPolicy spatialIndexingPolicy = new IndexingPolicy();
    spatialIndexingPolicy.IncludedPaths.Add(new IncludedPath
    {
        Path = "/*",
        Indexes = new System.Collections.ObjectModel.Collection<Index>()
            {
                new RangeIndex(DataType.Number) { Precision = -1 },
                new RangeIndex(DataType.String) { Precision = -1 },
                new SpatialIndex(DataType.Point)
            }
    });

    Console.WriteLine("Creating new collection...");
    collection = await client.CreateDocumentCollectionAsync(dbLink, collectionDefinition);

Y aquí vemos cómo puede modificar una colección existente para aprovechar las ventajas de la indexación espacial de los puntos que se almacenan en los documentos.

**Modificación de una colección ya existente con indexación espacial**

    Console.WriteLine("Updating collection with spatial indexing enabled in indexing policy...");
    collection.IndexingPolicy = spatialIndexingPolicy; 
    await client.ReplaceDocumentCollectionAsync(collection);

    Console.WriteLine("Waiting for indexing to complete...");
    long indexTransformationProgress = 0;
    while (indexTransformationProgress < 100)
    {
        ResourceResponse<DocumentCollection> response = await client.ReadDocumentCollectionAsync(collection.SelfLink);
        indexTransformationProgress = response.IndexTransformationProgress;

        await Task.Delay(TimeSpan.FromSeconds(1));
    }

> [AZURE.NOTE] Si el valor GeoJSON de la ubicación que se encuentra en el documento es incorrecto o no válido, no se indexará para realizar consultas espaciales. Puede validar los valores de ubicación mediante ST\_ISVALID y ST\_ISVALIDDETAILED.

## Pasos siguientes
Ahora que ya sabe cómo empezar a trabajar con la compatibilidad geoespacial en DocumentDB, puede:

- Comenzar a codificar con los [ejemplos de código geoespacial .NET en Github](https://github.com/Azure/azure-documentdb-dotnet/blob/e880a71bc03c9af249352cfa12997b51853f47e5/samples/code-samples/Geospatial/Program.cs)
- Empezar a realizar consultas geoespaciales en el [Área de consultas de DocumentDB](http://www.documentdb.com/sql/demo#geospatial)
- Obtener más información en [Base de datos de documentos de consulta](documentdb-sql-query.md).
- Obtener más información sobre [Directivas de indexación de DocumentDB](documentdb-indexing-policies.md)

<!---HONumber=AcomDC_0204_2016-->