<properties 
    pageTitle="Directivas de indexación de DocumentDB | Microsoft Azure" 
    description="Obtenga información acerca de cómo funciona la indexación en DocumentDB y sobre cómo configurar y cambiar la directiva de indexación. Configurar la directiva de indexación dentro de DocumentDB para una indexación automática y un mayor rendimiento." 
	keywords="how indexing works, automatic indexing, indexing database, documentdb, azure, Microsoft azure"
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


# Directivas de indexación de DocumentDB

Aunque muchos clientes prefieren dejar que DocumentDB controle automáticamente [todos los aspectos de la indexación ](documentdb-indexing.md), DocumentDB también permite especificar una **directiva de indexación** personalizada para las colecciones durante la creación. Las directivas de indexación en DocumentDB son más flexibles y potentes que los índices secundarios que se ofrecen en otras plataformas de base de datos, ya que le permiten diseñar y personalizar la forma del índice sin sacrificar la flexibilidad del esquema. Para entender cómo funciona la indexación en DocumentDB, debe comprender que, mediante la administración de una directiva de indexación, puede lograr un equilibrio específico entre la sobrecarga de almacenamiento, el rendimiento de escritura y de consulta, y la coherencia de consultas del índice.

En este artículo, echamos un vistazo más detenido a las directivas de indexación de DocumentDB, la personalización de la directiva de indexación y las ventajas y desventajas asociadas.

Después de leer este artículo, podrá responder a las preguntas siguientes:

- ¿Cómo puedo reemplazar las propiedades para incluirlas o excluirlas de la indexación?
- ¿Cómo puedo configurar el índice para las actualizaciones finales?
- ¿Cómo puedo configurar la indexación para realizar consultas Order By o intervalo?
- ¿Cómo se pueden realizar cambios en la directiva de indexación de una colección?
- ¿Cómo se puede comparar el almacenamiento y el rendimiento de diferentes directivas de indexación?

##<a id="CustomizingIndexingPolicy"></a> Personalización de la directiva de indexación de una colección

Los desarrolladores pueden personalizar los equilibrios entre almacenamiento, rendimiento de escritura y consulta y coherencia de las consultas, reemplazando la directiva de indexación predeterminada en una colección de DocumentDB y configurando los aspectos siguientes.

- **Inclusión y exclusión de documentos y rutas de acceso del índice y al índice**. Los desarrolladores pueden elegir que se incluyan o excluyan determinados documentos en el índice en el momento de insertarlos o reemplazarlos en la colección. También pueden elegir la inclusión o exclusión de determinadas propiedades JSON, también denominadas rutas de acceso (incluidos los patrones de caracteres comodín), para que se indexen transversalmente en documentos que están incluidos en un índice.
- **Configuración de distintos tipos de índice**. Para cada una de las rutas de acceso incluidas, los desarrolladores también pueden especificar el tipo de índice necesario para una colección en función de sus datos y la carga de trabajo de consultas esperada, así como de la "precisión" numérica y de cadena para cada ruta de acceso.
- **Configuración de modos de actualización del índice**. DocumentDB admite tres modos de indexación que se pueden configurar mediante la directiva de indexación en una colección de DocumentDB: Coherente, Diferida y Ninguna. 

En el siguiente fragmento de código de .NET se muestra cómo establecer una directiva de indexación personalizada durante la creación de una colección. A continuación establecemos la directiva con el índice de intervalo para las cadenas y números en la precisión máxima. Esta directiva nos permite ejecutar consultas Order By en cadenas.

    var collection = new DocumentCollection { Id = "myCollection" };
    
    collection.IndexingPolicy.IndexingMode = IndexingMode.Consistent;
    
    collection.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/*", 
            Indexes = new Collection<Index> { 
                new RangeIndex(DataType.String) { Precision = -1 }, 
                new RangeIndex(DataType.Number) { Precision = -1 }
            }
        });

    await client.CreateDocumentCollectionAsync(database.SelfLink, collection);   


>[AZURE.NOTE] El esquema JSON de la directiva de indexación ha cambiado con el lanzamiento de la API de REST versión 2015-06-03 para admitir índices de intervalo en cadenas. El SDK de .NET 1.2.0 y los SDK de Java, Python y Node.js 1.1.0 admiten el nuevo esquema de directiva. Los SDK anteriores usan la API de REST versión 2015-04-08 y admiten el esquema anterior de la directiva de indexación.
>
>De forma predeterminada, DocumentDB indexa todas las propiedades de cadena en los documentos de forma coherente con un índice Hash y las propiedades numéricas con un índice de intervalo.

### Modos de indexación de bases de datos

DocumentDB admite tres modos de indexación que se pueden configurar mediante la directiva de indexación en una colección de DocumentDB: Coherente, Diferida y Ninguna.

**Coherente**: si la directiva de la colección de DocumentDB se designa como "coherente", las consultas realizadas en una colección DocumentDB determinada siguen el mismo nivel de coherencia que se especifique para las lecturas de punto (es decir, alta, de uso vinculado, sesión y eventual). El índice se actualiza de forma sincrónica como parte de la actualización del documento (es decir, inserción, reemplazo, actualización y eliminación de un documento en una colección de DocumentDB). La indexación coherente admite consultas coherentes a costa de una posible reducción en el rendimiento de escritura. Esta reducción depende de las rutas de acceso únicas que se deben indexar y del "nivel de coherencia". El modo de indexación coherente está diseñado para cargas de trabajo de tipo "escribir rápidamente, consultar inmediatamente".

**Diferida**: para permitir el rendimiento máximo de ingesta de documentos, se puede configurar una colección DocumentDB con coherencia diferida; lo que significa que las consultas terminan siendo coherentes. El índice se actualiza de forma asincrónica cuando una colección DocumentDB está inactiva, es decir, cuando la capacidad de rendimiento de la colección no se usa por completo para atender las solicitudes de usuario. Para cargas de trabajo de tipo "introducir ahora, consultar más adelante" que requieran ingesta de documentos sin obstáculos, es posible que el modo de indexación "diferido" sea el adecuado.

**Ninguna**: una colección marcada con el modo de indexación de "Ninguna" no tiene ningún índice asociado. Esto se suele usar si DocumentDB se emplea como almacenamiento de clave-valor y solo se puede acceder a los documentos mediante su propiedad de identificador.

>[AZURE.NOTE] La configuración de la directiva de indexación con "Ninguna" tiene el efecto secundario de quitar cualquier índice existente. Úsela si los patrones de acceso solo requieren "id" o "vinculación automática".

El siguiente programa de ejemplo muestra cómo crear una colección de DocumentDB mediante el SDK de .NET con la indización automática coherente en todas las inserciones de documentos.

La siguiente tabla muestra la coherencia de las consultas basadas en el modo de indexación (Coherente y Diferida) que se configure para la colección y el nivel de coherencia especificado para la solicitud de consulta. Esto se aplica a las consultas realizadas con cualquier interfaz: API de REST, SDK o desde procedimientos almacenados y desencadenadores.

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top">
                <p>
                </p>
            </td>
            <td valign="top">
                <p>
                    <strong>Coherente</strong>
                </p>
            </td>
            <td valign="top">
                <p>
                    <strong>Diferida</strong>
                </p>
            </td>            
        </tr>
        <tr>
            <td valign="top">
                <p>
                    <strong>Alta</strong>
                </p>
            </td>
            <td valign="top">
                <p>
                    Alta
                </p>
            </td>
            <td valign="top">
                <p>
                    Ocasional
                </p>
            </td>            
        </tr>       
        <tr>
            <td valign="top">
                <p>
                    <strong>De uso vinculado</strong>
                </p>
            </td>
            <td valign="top">
                <p>
                    De uso vinculado
                </p>
            </td>
            <td valign="top">
                <p>
                    Ocasional
                </p>
            </td>            
        </tr>          
        <tr>
            <td valign="top">
                <p>
                    <strong>Sesión</strong>
                </p>
            </td>
            <td valign="top">
                <p>
                    Sesión
                </p>
            </td>
            <td valign="top">
                <p>
                    Ocasional
                </p>
            </td>            
        </tr>      
        <tr>
            <td valign="top">
                <p>
                    <strong>Ocasional</strong>
                </p>
            </td>
            <td valign="top">
                <p>
                    Ocasional
                </p>
            </td>
            <td valign="top">
                <p>
                    Ocasional
                </p>
            </td>            
        </tr>         
    </tbody>
</table>

DocumentDB devuelve un error para las consultas realizadas en colecciones con el modo de indexación Ninguno. Será posible seguir ejecutando consultas como exámenes a través del encabezado `x-ms-documentdb-enable-scans` explícito en la API de REST o la opción `EnableScanInQuery` mediante el SDK de .NET. Algunas funciones de consulta, como ORDER BY, no se admiten como exámenes con `EnableScanInQuery`.

La siguiente tabla muestra la coherencia de las consultas basadas en el modo de indexación (Coherente, Diferida y Ninguna) cuando se especifica EnableScanInQuery.

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top">
                <p>
                </p>
            </td>
            <td valign="top">
                <p>
                    <strong>Coherente</strong>
                </p>
            </td>
            <td valign="top">
                <p>
                    <strong>Diferida</strong>
                </p>
            </td>       
            <td valign="top">
                <p>
                    <strong>None</strong>
                </p>
            </td>             
        </tr>
        <tr>
            <td valign="top">
                <p>
                    <strong>Alta</strong>
                </p>
            </td>
            <td valign="top">
                <p>
                    Alta
                </p>
            </td>
            <td valign="top">
                <p>
                    Ocasional
                </p>
            </td>    
            <td valign="top">
                <p>
                    Alta
                </p>
            </td>                
        </tr>       
        <tr>
            <td valign="top">
                <p>
                    <strong>De uso vinculado</strong>
                </p>
            </td>
            <td valign="top">
                <p>
                    De uso vinculado
                </p>
            </td>
            <td valign="top">
                <p>
                    Ocasional
                </p>
            </td>      
            <td valign="top">
                <p>
                    De uso vinculado
                </p>
            </td> 
        </tr>          
        <tr>
            <td valign="top">
                <p>
                    <strong>Sesión</strong>
                </p>
            </td>
            <td valign="top">
                <p>
                    Sesión
                </p>
            </td>
            <td valign="top">
                <p>
                    Ocasional
                </p>
            </td>   
            <td valign="top">
                <p>
                    Sesión
                </p>
            </td>             
        </tr>      
        <tr>
            <td valign="top">
                <p>
                    <strong>Ocasional</strong>
                </p>
            </td>
            <td valign="top">
                <p>
                    Ocasional
                </p>
            </td>
            <td valign="top">
                <p>
                    Ocasional
                </p>
            </td>      
            <td valign="top">
                <p>
                    Ocasional
                </p>
            </td>              
        </tr>         
    </tbody>
</table>

El siguiente ejemplo de código muestra cómo crear una colección de DocumentDB mediante .NET SDK con indexación coherente en todas las inserciones de documentos.

     // Default collection creates a hash index for all string and numeric    
     // fields. Hash indexes are compact and offer efficient
     // performance for equality queries.
     
     var collection = new DocumentCollection { Id ="defaultCollection" };
     
     collection.IndexingPolicy.IndexingMode = IndexingMode.Consistent;
     
     collection = await client.CreateDocumentCollectionAsync(database.SelfLink, collection);


### Rutas de acceso del índice

DocumentDB modela los documentos JSON y el índice en forma de árbol, y permite ajustarse a las directivas para las rutas de acceso dentro del árbol. Puede encontrar más detalles en esta [introducción a la indexación de DocumentDB](documentdb-indexing.md). En los documentos, puede elegir qué rutas de acceso se deben incluir o excluir del índice. Esto puede mejorar el rendimiento de escritura y reducir el almacenamiento necesario para índice en escenarios en los que se conocen de antemano los patrones de consulta.

Las rutas de acceso del índice comenzar con la raíz (/) y normalmente finalizan con el operador comodín ?, que indica que hay varios posibles valores para el prefijo. Por ejemplo, para atender a la consulta SELECT * FROM Families F WHERE F.familyName = "Andersen", debe incluir una ruta de acceso del índice para /familyName/? en la directiva de índice de la recopilación.

Las rutas de acceso del índice también pueden usar el operador comodín * para especificar el comportamiento de las rutas de acceso de forma recursiva en el prefijo. Por ejemplo, /payload/* puede usarse para excluir de la indexación de todo el contenido de la propiedad payload.

Estos son los patrones comunes para especificar las rutas de acceso del índice:

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top">
                <p>
                    <strong>Ruta de acceso</strong>
                </p>
            </td>
            <td valign="top">
                <p>
                    <strong>Descripción/caso de uso</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    /*
                </p>
            </td>
            <td valign="top">
                <p>
                    Ruta de acceso predeterminada de la recopilación. Recursiva. Se aplica a la totalidad del árbol de documentos.
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    /prop/?
                </p>
            </td>
            <td valign="top">
                <p>
                    Ruta de acceso de índice necesaria para atender las consultas como la siguiente (con tipos hash o de intervalo respectivamente):
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop = "value"
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop > 5
                </p>
                <p>
                    SELECT * FROM collection c ORDER BY c.prop
                </p>                
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    /"prop"/*
                </p>
            </td>
            <td valign="top">
                <p>
                    Ruta de acceso del índice para todas las rutas de acceso situadas en la etiqueta especificada. Funciona con las siguientes consultas
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop = "value"
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop.subprop > 5
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop.subprop.nextprop = "value"
                </p>
                <p>
                    SELECT * FROM collection c ORDER BY c.prop
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    /props/[]/?
                </p>
            </td>
            <td valign="top">
                <p>
                    Ruta de acceso de índice necesaria para servir a la iteración y a las consultas JOIN frente a las matrices de valores escalares como ["a", "b", "c"]:
                </p>
                <p>
                    SELECT tag FROM tag IN collection.props WHERE tag = "value"
                </p>
                <p>
                    SELECT tag FROM collection c JOIN tag IN c.props WHERE tag > 5
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    /props/[]/subprop/?
                </p>
            </td>
            <td valign="top">
                <p>
                    Ruta de acceso de índice necesaria para servir a la iteración y a las consultas JOIN frente a las matrices de objetos como [{subprop: "a"}, {subprop: "b"}]:
                </p>
                <p>
                    SELECT tag FROM tag IN collection.props WHERE tag.subprop = "value"
                </p>
                <p>
                    SELECT tag FROM collection c JOIN tag IN c.props WHERE tag.subprop = "value"
                </p>
            </td>
        </tr>        
        <tr>
            <td valign="top">
                <p>
                    /prop/subprop/?
                </p>
            </td>
            <td valign="top">
                <p>
                    Ruta de acceso de índice necesaria para atender consultas (con las de tipo hash o de intervalo respectivamente):
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop.subprop = "value"
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop.subprop > 5
                </p>
                <p>
                    SELECT * FROM collection c ORDER BY c.prop.subprop
                </p>                
            </td>
        </tr>
    </tbody>
</table>

>[AZURE.NOTE] Al configurar las rutas de acceso de índice personalizado, es necesario especificar la regla de indexación predeterminada para el árbol de todo el documento indicada mediante la ruta de acceso especial "/".

En el ejemplo siguiente se configura una ruta de acceso específica con indexación de intervalo y un valor de precisión personalizado de 20 bytes:

    var collection = new DocumentCollection { Id = "rangeSinglePathCollection" };    
    
    collection.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/Title/?", 
            Indexes = new Collection<Index> { 
                new RangeIndex(DataType.String) { Precision = 20 } } 
            });

    // Default for everything else
    collection.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/*" ,
            Indexes = new Collection<Index> {
                new HashIndex(DataType.String) { Precision = 3 }, 
                new RangeIndex(DataType.Number) { Precision = -1 } 
            }
        });
        
    collection = await client.CreateDocumentCollectionAsync(database.SelfLink, pathRange);

### Tipos de datos de índice, variantes y precisiones

Ahora que hemos echado un vistazo a cómo especificar las rutas de acceso, echemos un vistazo a las opciones que podemos usar para configurar la directiva de indexación en una ruta de acceso. Puede especificar una o más definiciones de indexación para cada ruta de acceso:

- Tipo de datos: **Cadena**, **Número** o **Punto** (solo puede contener una entrada por tipo de datos y ruta de acceso)
- Variante de índice: **Hash** (consultas de igualdad), **Intervalo** (consultas de igualdad, de intervalo o por Order By), o **Espacial** (consultas espaciales) 
- Precisión: 1-8 o -1 (precisión máxima) para números, 1-100 (precisión máxima) para cadenas

#### Tipo de índice

DocumentDB admite los tipos de índice Hash e Intervalo para cada ruta de acceso (que puede configurar para las cadenas, números o ambos).

- **Hash** admite consultas de igualdad y JOIN eficientes. Para la mayoría de los casos de uso, los índices hash no requieren una precisión mayor que el valor predeterminado de 3 bytes.
- **Intervalo** admite consultas de igualdad, consultas de intervalo (con >, <, >=, <=, !=) y consultas por Order By eficientes. De forma predeterminada, las consultas Order By también requieren una precisión índice máximo (-1).

DocumentDB también admite la clase de índice Espacial para cada ruta de acceso, que se puede especificar para el tipo de datos Punto. El valor en la ruta especificada debe ser un punto de GeoJSON válido como `{"type": "Point", "coordinates": [0.0, 10.0]}`.

- **Espacial** admite consultas espaciales eficaces (internas y a distancia).

Estos son las variantes de índice admitidas, con ejemplos de consultas que se pueden usar para atenderlas:

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top">
                <p>
                    <strong>Tipo de índice</strong>
                </p>
            </td>
            <td valign="top">
                <p>
                    <strong>Descripción/caso de uso</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    Hash
                </p>
            </td>
            <td valign="top">
                <p>
                    Se puede usar hash en /prop/? (o /*) para atender las siguientes consultas con eficacia: SELECT * FROM collection c WHERE c.prop = "value" Se puede usar hash sobre /props/[]/? (o /* o /props/*) para atender las siguientes consultas con eficacia: SELECT tag FROM collection c JOIN tag IN c.props WHERE tag = 5
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    Intervalo
                </p>
            </td>
            <td valign="top">
                <p>
                    Se puede usar intervalo en /prop /? (o /*) para atender las siguientes consultas con eficacia: SELECT * FROM collection c WHERE c.prop = "value" SELECT * FROM collection c WHERE c.prop > 5 SELECT * FROM collection c ORDER BY c.prop
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    Spatial
                </p>
            </td>
            <td valign="top">
                <p>
                    Se puede usar un intervalo /prop/? (or /*) para atender las siguientes consultas con eficacia: SELECT * FROM collection c WHERE ST_DISTANCE(c.prop, {"type": "Point", "coordinates": [0.0, 10.0]}) &lt; 40 SELECT * FROM collection c WHERE ST_WITHIN(c.prop, {"type": "Polygon", ... })
                </p>
            </td>
        </tr>        
    </tbody>
</table>

De forma predeterminada, se devuelve un error para las consultas con operadores de intervalo como > = si no hay ningún índice de intervalo (de ninguna precisión) para indicar que podría ser necesario realizar un examen para atender la consulta. Las consultas de intervalo se pueden realizar sin un índice de intervalo mediante el uso del encabezado x-ms-documentdb-allow-scans en la API de REST o con la opción de solicitud EnableScanInQuery mediante el SDK de .NET. Si hay otros filtros en la consulta en los que DocumentDB pueda usar el índice para realizar el filtrado, no se devolverá ningún error.

Se aplican las mismas reglas para las consultas espaciales. De forma predeterminada, se devuelve un error para las consultas espaciales si no hay ningún índice espacial y no hay ningún otro filtro que pueda obtenerse del índice. Se pueden realizar como un análisis mediante x-ms-documentdb-enable-examen/EnableScanInQuery.

#### Índice de precisión

La precisión de índice permite lograr un equilibrio entre la sobrecarga de almacenamiento de índices y el rendimiento de las consultas. Para los números, se recomienda usar la configuración de precisión predeterminada de -1 (“máxima”). Puesto que los números son 8 bytes en JSON, esto es equivalente a una configuración de 8 bytes. La selección de un valor inferior para la precisión, como 1-7, significa que los valores de algunos intervalos se asignan a la misma entrada de índice. Por lo tanto, se reducirá el espacio de almacenamiento del índice, pero la ejecución de la consulta podría tener que procesar más documentos y, por tanto, consumir más rendimiento, es decir, solicitar unidades.

La configuración de la precisión del índice tiene una aplicación más práctica con intervalos de cadena. Dado que las cadenas pueden ser de cualquier longitud arbitraria, la elección de la precisión del índice puede afectar al rendimiento de las consultas de intervalo de cadena, así como a la cantidad de espacio de almacenamiento del índice requerido. Los índices de intervalo de cadena pueden configurarse con 1-100 o -1 (“máxima”). Si desea realizar consultas por Order By en las propiedades de cadena, debe especificar una precisión de -1 para las rutas de acceso correspondientes.

Los índices espaciales siempre usan la precisión de índice predeterminada para los puntos y no puede invalidarse.

En el ejemplo siguiente se muestra cómo aumentar la precisión de los índices de intervalo en una recopilación mediante el SDK de .NET. Tenga en cuenta que esto usa la ruta predeterminada "/*".

**Creación de una colección con una precisión de índice personalizada**

    var rangeDefault = new DocumentCollection { Id = "rangeCollection" };
    
    rangeDefault.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/*", 
            Indexes = new Collection<Index> { 
                new RangeIndex(DataType.String) { Precision = -1 }, 
                new RangeIndex(DataType.Number) { Precision = -1 }
            }
        });

    await client.CreateDocumentCollectionAsync(database.SelfLink, rangeDefault);   


> [AZURE.NOTE] DocumentDB devuelve un error cuando una consulta usa Order By, pero no tiene un índice de intervalo en la ruta de acceso consultada con la precisión máxima.

De forma similar las rutas de acceso se pueden excluir completamente de la indexación. En el ejemplo siguiente se muestra cómo excluir una sección completa de los documentos (también conocida como subárbol) de la indexación usando el comodín "*".

    var collection = new DocumentCollection { Id = "excludedPathCollection" };
    collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/" });
    collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
    
    collection = await client.CreateDocumentCollectionAsync(database.SelfLink, excluded);


## Opción de suscribirse o no a la indexación

Puede elegir si desea que la recopilación de todos los documentos se indexe automáticamente. De forma predeterminada, todos los documentos se indexan automáticamente, pero puede desactivarla. Cuando se desactiva la indexación, solo se puede tener acceso a documentos a través de sus propios vínculos o mediante consultas con Id.

Cuando se desactiva la indexación automática, podrá agregar al índice de manera selectiva solo algunos documentos específicos. Por el contrario, puede dejar activada la indexación automática y excluir de forma selectiva solo algunos documentos específicos. Las configuraciones de indexación activada/desactivada son útiles cuando solo tiene un subconjunto de los documentos que necesita consultar.

Por ejemplo, en el ejemplo siguiente se muestra cómo incluir un documento explícitamente mediante [.NET SDK de DocumentDB](https://github.com/Azure/azure-documentdb-java) y la propiedad [RequestOptions.IndexingDirective](http://msdn.microsoft.com/library/microsoft.azure.documents.client.requestoptions.indexingdirective.aspx).

    // If you want to override the default collection behavior to either
    // exclude (or include) a Document from indexing,
    // use the RequestOptions.IndexingDirective property.
    client.CreateDocumentAsync(defaultCollection.SelfLink,
        new { id = "AndersenFamily", isRegistered = true },
        new RequestOptions { IndexingDirective = IndexingDirective.Include });

## Modificación de la directiva de indexación de una colección

DocumentDB le permite realizar cambios sobre la marcha en la directiva de indexación de una colección. Un cambio en la directiva de indexación en una colección de DocumentDB puede dar lugar a un cambio en la forma del índice, que incluye las rutas de acceso que se pueden indexar, su precisión, así como el modelo de coherencia del propio índice. Por lo tanto, un cambio en la directiva de indexación, requiere efectivamente una transformación del índice original en uno nuevo.

**Transformaciones de índice en línea**

![Cómo funciona la indexación: transformaciones de índice en línea de DocumentDB](media/documentdb-indexing-policies/index-transformations.png)

Las transformaciones de índice se realizan en línea, lo que significa que los documentos indexados por la directiva antigua se transforman eficazmente según la nueva directiva **sin afectar a la disponibilidad de escritura ni al rendimiento aprovisionado** de la colección. La coherencia de las operaciones de lectura y escritura realizadas con la interfaz API de REST, SDK o desde procedimientos almacenados y desencadenadores no se ve afectada durante la transformación de índice. Esto significa que no hay ninguna degradación del rendimiento ni tiempo de inactividad en las aplicaciones al realizar un cambio de directiva de indexación.

Sin embargo, durante el tiempo en que la transformación de índice está en curso, las consultas son coherentes finalmente, con independencia de la configuración del modo de indexación (Coherente o Diferida). Esto se aplica a las consultas realizadas con todas las interfaces: API de REST, SDK o desde procedimientos almacenados y desencadenadores. Al igual que con la indexación Diferida, la transformación de índice se realiza asincrónicamente en segundo plano en las réplicas mediante los recursos de reserva disponibles para una réplica determinada.

Las transformaciones de índice también se llevan a cabo **in-situ** (en el sitio), es decir, DocumentDB no mantiene dos copias del índice e intercambia el índice antiguo con el nuevo. Esto significa que no se requiere ni se usa espacio adicional en disco en las colecciones mientras se realizan transformaciones de índice.

Cuando se cambia la directiva de indexación, la forma en que se aplican los cambios para moverse del índice antiguo al nuevo dependen principalmente de las configuraciones de modo de indexación más que de los demás valores, por ejemplo, rutas de acceso incluidas/excluidas, variantes de índice y precisiones. Si la directiva antigua y la nueva usan indexación coherente, DocumentDB realiza una transformación de índice en línea. No se puede aplicar otro cambio de directiva de indexación con el modo de indexación coherente mientras la transformación está en curso.

Sin embargo, puede moverse al modo de indexación Diferida o Ninguna mientras una transformación está en curso.

- Cuando se mueve a Diferida, el cambio de la directiva de indexación tiene efecto inmediato y DocumentDB inicia asincrónicamente la recreación el índice. 
- Cuando se mueve a Ninguna, el índice se quita con efecto inmediato. El movimiento a Ninguna resulta útil cuando se quiere cancelar una transformación en curso y empezar de nuevo con una directiva de indexación distinta. 

Si usa .NET SDK, puede iniciar un cambio de directiva de indexación con el nuevo método **ReplaceDocumentCollectionAsync** y realizar el seguimiento del progreso porcentual de transformación del índice con la propiedad de respuesta **IndexTransformationProgress** desde una llamada a **ReadDocumentCollectionAsync**. Otros SDK y la API de REST admiten métodos y propiedades equivalentes para realizar cambios de la directiva de indización.

El siguiente fragmento de código muestra cómo modificar la directiva de indexación de una colección pasando del modo Coherente al modo Diferida.

**Modificación de directiva de indexación de Coherente a Diferida**

    // Switch to lazy indexing.
    Console.WriteLine("Changing from Default to Lazy IndexingMode.");

    collection.IndexingPolicy.IndexingMode = IndexingMode.Lazy;

    await client.ReplaceDocumentCollectionAsync(collection);


Puede comprobar el progreso de una transformación de índice mediante una llamada a ReadDocumentCollectionAsync, por ejemplo, como se muestra a continuación.

**Seguimiento del progreso de transformación de índice**

    long smallWaitTimeMilliseconds = 1000;
    long progress = 0;

    while (progress < 100)
    {
        ResourceResponse<DocumentCollection> collectionReadResponse = await     client.ReadDocumentCollectionAsync(collection.SelfLink);
        progress = collectionReadResponse.IndexTransformationProgress;

        await Task.Delay(TimeSpan.FromMilliseconds(smallWaitTimeMilliseconds));
    }

Puede quitar el índice de una colección moviendo al modo de indexación Ninguna. Esto puede ser una herramienta operativa útil si quiere cancelar una transformación en curso y comenzar inmediatamente una nueva.

**Eliminación del índice de una colección**

    // Switch to lazy indexing.
    Console.WriteLine("Dropping index by changing to to the None IndexingMode.");

    collection.IndexingPolicy.IndexingMode = IndexingMode.None;

    await client.ReplaceDocumentCollectionAsync(collection);

¿Cuando realizaría cambios de directiva de indexación en las colecciones de DocumentDB? Los siguientes son los casos de uso más comunes:

- Servicio de resultados coherentes durante el funcionamiento normal, pero reversión a la indexación diferida durante importaciones de conjuntos masivos de datos
- Inicio con nuevas características de indexación en las colecciones de DocumentDB actuales, por ejemplo, consultas geoespaciales que requieren el tipo de índice espacial, o Order By y por rango de cadenas que requieren la variante de índice de intervalo de cadena recién presentada
- Selección manual de las propiedades que se van a indexar y cambiarlas con el tiempo
- Optimización de la precisión de indexación para mejorar el rendimiento de las consultas o reducir el almacenamiento usado

>[AZURE.NOTE] Para modificar la directiva de indexación con ReplaceDocumentCollectionAsync, necesita la versión >= 1.3.0 de .NET SDK
>
> Para que la transformación del índice se complete correctamente, debe asegurarse de que haya suficiente espacio libre de almacenamiento disponible en la colección. Si la colección alcanza su cuota de almacenamiento, se pausará la transformación del índice. La transformación del índice se reanudará automáticamente una vez que haya espacio de almacenamiento disponible, por ejemplo, si elimina algunos documentos.

## Optimización del rendimiento

Las API de DocumentDB proporcionan información acerca de las métricas de rendimiento, como el almacenamiento de índice usado y el costo del rendimiento (unidades de solicitud) para cada operación. Esta información puede usarse para comparar varias directivas de indexación y para optimizar el rendimiento.

Para comprobar la cuota de almacenamiento y el uso de una colección, ejecute una solicitud HEAD o GET en el recurso de colección e inspeccione la cuota x-ms-request y los encabezados x-ms-request-usage. En el SDK de .NET, las propiedades [DocumentSizeQuota](http://msdn.microsoft.com/library/dn850325.aspx) y [DocumentSizeUsage](http://msdn.microsoft.com/library/azure/dn850324.aspx) de [ResourceResponse<T>](http://msdn.microsoft.com/library/dn799209.aspx) contienen los valores correspondientes.

     // Measure the document size usage (which includes the index size) against   
     // different policies.        
     ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(collectionSelfLink);  
     Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);


Para medir la sobrecarga de la indexación en cada operación de escritura (crear, actualizar o eliminar), inspeccione el encabezado x-ms-request-charge (o la propiedad [RequestCharge](http://msdn.microsoft.com/library/dn799099.aspx) equivalente en [ResourceResponse<T>](http://msdn.microsoft.com/library/dn799209.aspx) de .NET SDK) para medir el número de unidades de solicitudes usadas por estas operaciones.

     // Measure the performance (request units) of writes.     
     ResourceResponse<Document> response = await client.CreateDocumentAsync(collectionSelfLink, myDocument);              
     Console.WriteLine("Insert of document consumed {0} request units", response.RequestCharge);
     
     // Measure the performance (request units) of queries.    
     IDocumentQuery<dynamic> queryable =  client.CreateDocumentQuery(collectionSelfLink, queryString).AsDocumentQuery();                                  
     double totalRequestCharge = 0;
     while (queryable.HasMoreResults)
     {
        FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>(); 
        Console.WriteLine("Query batch consumed {0} request units",queryResponse.RequestCharge);
        totalRequestCharge += queryResponse.RequestCharge;
     }
     
     Console.WriteLine("Query consumed {0} request units in total", totalRequestCharge);

## Cambios en la especificación de la directiva de indexación
Se incorporó un cambio en el esquema de la directiva de indexación el 7 de julio de 2015 con la API de REST versión 2015-06-03. Las clases correspondientes de las versiones del SDK tienen implementaciones nuevas para que coincidan con el esquema.

Se han implementado los siguientes cambios en la especificación de JSON:

- La directiva de indexación admite índices de intervalo para las cadenas
- Cada ruta de acceso puede tener varias definiciones de índice, una para cada tipo de datos
- La precisión de la indexación admite 1-8 para números, 1-100 para las cadenas y -1 (precisión máxima)
- Los segmentos de rutas de acceso no requieren comillas dobles para escapar de cada ruta de acceso. Por ejemplo, ¿puede agregar una ruta de acceso para /title/? en lugar de /"title"/?
- La ruta de acceso raíz que representa "todas las rutas de acceso" se puede representar como /* (además de /)

Si tiene código que aprovisiona colecciones con una directiva de indexación personalizada escrita con la versión 1.1.0 del SDK de .NET o anteriores, será necesario cambiar el código de la aplicación para controlar estos cambios para pasar al SDK versión 1.2.0. Si no tiene código que configure la directiva de indexación o va a continuar usando una versión anterior del SDK, no se requerirá ningún cambio.

Para ver una comparación práctica, presentamos una directiva de indexación personalizada de ejemplo escrita con la API de REST versión 2015-06-03, así como la versión anterior, 08-04-2015.

**JSON de directiva de indexación anterior**

    {
       "automatic":true,
       "indexingMode":"Consistent",
       "IncludedPaths":[
          {
             "IndexType":"Hash",
             "Path":"/",
             "NumericPrecision":7,
             "StringPrecision":3
          }
       ],
       "ExcludedPaths":[
          "/"nonIndexedContent"/*"
       ]
    }

**JSON de directiva de indexación actual**

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
                   "kind":"Hash",
                   "dataType":"Number",
                   "precision":7
                }
             ]
          }
       ],
       "ExcludedPaths":[
          {
             "path":"/nonIndexedContent/*"
          }
       ]
    }

## Pasos siguientes

Siga los vínculos que aparecen a continuación para obtener ejemplos de administración de directivas de índice y para obtener más información acerca del lenguaje de consulta de DocumentDB.

1.	[Ejemplos de código de administración de índices de .NET DocumentDB](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/IndexManagement/Program.cs)
2.	[Operaciones de recopilación de la API de REST de DocumentDB](https://msdn.microsoft.com/library/azure/dn782195.aspx)
3.	[Consulta con SQL de Base de datos de documentos](documentdb-sql-query.md)

 

<!---HONumber=AcomDC_0204_2016-->