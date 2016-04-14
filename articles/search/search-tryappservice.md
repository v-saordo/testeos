<properties 
   pageTitle="Pruebe Búsqueda de Azure de forma gratuita con Servicio de aplicaciones de Azure | Microsoft Azure"
   description="Pruebe Búsqueda de Azure gratis durante una hora, usando la plantilla de Servicio de aplicaciones de Azure."
   services="search" 
   documentationCenter="" 
   authors="HeidiSteen" 
   manager="mblythe" 
   editor=""/>

<tags
   ms.service="search"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="search" 
   ms.date="02/21/2016"
   ms.author="heidist"/>

# Pruebe Búsqueda de Azure de forma gratuita con Servicio de aplicaciones de Azure

[Pruebe el Servicio de aplicaciones de Azure](https://tryappservice.azure.com/) es una sesión basada en navegador gratuita de una hora de duración para probar los servicios de Azure, incluido **Búsqueda de Azure**, sin que sea necesaria ninguna suscripción. El sitio ofrece varias plantillas para elegir. Al seleccionar la plantilla ASP.NET que incluye Búsqueda de Azure, obtendrá acceso a un sitio web completamente funcional, respaldado por los servicios que seleccionó.

Como parte de la oferta de **Pruebe el Servicio de aplicaciones de Azure**, el servicio Búsqueda de Azure ya se ha creado y está listo para recibir consultas de búsqueda. No puede cargar o usar su propio índice o sus propios datos, pero puede ejecutar consultas y realizar un número de cambios de código que vuelvan a dar forma a la experiencia del usuario.

Los datos de búsqueda proceden de [United States Geological Survey (USGS)](http://geonames.usgs.gov/domestic/download_data.htm), que consta de 3 millones de filas con puntos de referencia, sitios históricos, edificios y otros puntos de referencia en Estados Unidos.

## Primeros pasos

Si aún no ha iniciado la sesión de 1 hora, siga estos pasos para comenzar.

1. Haga clic con el botón derecho en [https://tryappservice.azure.com](https://tryappservice.azure.com/) para abrir este sitio en una nueva ventana del explorador.
2. Desplácese hacia abajo para seleccionar **Aplicación web**. 
2. Haga clic en **Siguiente**.
3. Elija la plantilla **ASP.NET + Azure Search Site**.

    ![][1]

4. Haga clic en **Crear**.
5. Elija un método de inicio de sesión y proporcione el nombre de usuario y la contraseña.

    ![][2]

6. Espere mientras el sitio se aprovisiona. Cuando esté listo, verá una página similar a esta. Cada página muestra un cronómetro para que siempre sepa cuánto tiempo le queda.

    ![][3]

7. Elija **Editar con Visual Studio Team Services** para ver la solución y examinar el sitio.
9. En Visual Studio Team Services, expanda las opciones de sesión en la parte superior de la página y, después, haga clic en **Examinar sitio web**.

    ![][4]

10. Debería ver que la página de comienzo de su sitio web de Búsqueda de Azure. Haga clic en el botón **Comenzar** para abrir el sitio.

    ![][5]

11. Se abre un sitio web ASP.NET en el explorador con un cuadro de búsqueda. Escriba un término que le resulte familiar para buscarlo, como *Yellowstone* o una montaña conocida como *Mount Rainier*. Empezar con un punto de referencia conocido facilita la evaluación de los resultados.

    ![][6]


## Qué hacer primero

Como el índice de búsqueda es totalmente operativo, un primer paso adecuado es probar algunas consultas. Búsqueda de Azure admite todos los operadores de búsqueda estándar (+, -, |), comillas para coincidencias literales, carácter comodín (*) y operadores de precedencia. Puede revisar la referencia de la sintaxis de consulta para ver la lista completa de los operadores.

- Empiece con una búsqueda con caracteres comodín agregando un asterisco (`*`). Esto indica cuántos documentos se encuentran en el índice: 2.262.578.
- Después, escriba "Yellowstone" y agregue "+center", "+building" y "-ND" para restringir progresivamente los resultados de la búsqueda solo a los centros de visitantes de Yellowstone, excepto los de Dakota del Norte: `Yellowstone +center +building -ND`.  
- Pruebe una frase de búsqueda que combine operadores de precedencia y coincidencia de cadenas: `statue+(park+MT)`. Debería ver resultados similares a la captura de pantalla siguiente. Observe que las categorías de faceta aparecen en Clase de característica y permiten realizar un filtrado autodirigido mediante navegación por facetas, una característica que normalmente se encuentra en la mayoría de las aplicaciones de búsqueda.

    ![][7]

¿Listo para continuar? Vamos a cambiar unas cuantas líneas de código para ver el impacto en las operaciones de búsqueda de texto completo.

## Cambiar searchMode.All

Búsqueda de Azure tiene una propiedad **searchMode** configurable que se puede usar para controlar el comportamiento del operador de búsqueda. Los valores válidos de esta propiedad son `Any` (valor predeterminado) o `All`. Consulte [Sintaxis de consulta simple](https://msdn.microsoft.com/library/dn798920.aspx) para obtener instrucciones para configurar estas opciones.

- **searchMode.Any** estipula que cualquier coincidencia en un término de búsqueda es suficiente para incluir un elemento en los resultados de búsqueda. Si la frase de búsqueda es `Yellowstone visitor center`, cualquier documento que contenga uno de estos términos se incluirá en los resultados de búsqueda. Este modo está orientado hacia la *recuperación*.
- **searchModel.All**, que se usa en este ejemplo, requiere que todos los términos especificados estén presentes en el documento. Este modo es más estricto que **searchMode.Any**, pero si su prioridad es la *precisión* frente a la recuperación, probablemente es la elección correcta para su aplicación. 

> [AZURE.NOTE] **searchMode.Any** funciona mejor cuando la construcción de la consulta se compone principalmente de frases, con un uso mínimo de operadores. Por regla general, los usuarios que buscan en aplicaciones de consumo, como sitios de comercio electrónico, tienden a usar términos precisos, mientras que las personas que buscan en contenido o datos, es más probable que incluyan operadores en la frase de búsqueda. Si cree que las búsquedas incluirán probablemente operadores, especialmente el operador `NOT (-)`, comience con **searchModel.All**. Por el contrario, la otra opción **searchMode.Any** aplicará `OR` al operador `NOT` con otros términos de búsqueda, lo que puede ampliar drásticamente los resultados en lugar de reducirlos. El ejemplo siguiente puede ayudarlo a entender la diferencia.

En esta tarea, cambiará el valor de **searchMode** y comparará los resultados de búsqueda según el modo.

1. Abra la ventana del explorador que contiene la aplicación de ejemplo, elija **Conectar con Visual Studio Team Services**.

    ![][8]

2. Abra **Search.cshtml**, busque `searchMode.All` en la línea 39 y cámbielo por `searchMode.Any`.

    ![][9]

3. En la barra de acceso rápido de la derecha, haga clic en **Ejecutar**.

    ![][10]
 
En la ventana de la aplicación recompilada, escriba un término de búsqueda que haya usado antes, como `Yellowstone +center +building -ND`, y compare los resultados antes y después de los cambios en **searchMode**.

Hay una gran diferencia. En lugar de siete resultados de búsqueda, obtiene más de dos millones.

   ![][11]
 
El comportamiento que observa se debe a la inclusión del operador `NOT` (en este caso, "-ND"), al que se aplica *OR* en lugar de *AND* cuando **searchMode** está establecido en `Any`.

Con esta configuración, los resultados de la búsqueda muestran concordancias para los términos de búsqueda `Yellowstone`, `center` y `building`, pero también todos los documentos que sean `NOT North Dakota`. Puesto que solo hay 13.081 documentos que contienen la frase `North Dakota`, se devuelve casi todo el conjunto de datos.

Es cierto que esta es una situación improbable, pero ilustra los efectos de **searchMode** en las frases de búsqueda que incluyen el operador `NOT`, por lo que es útil comprender por qué se produce el comportamiento y cómo cambiarlo si esto no es lo que se desea obtener.

Para continuar con este tutorial, revierta **searchMode** a su valor original (establecido en `All` en la línea 39), ejecute el programa y use la aplicación recompilada para las tareas restantes.
 
## Incorporación de un filtro global para el estado de Washington

Normalmente, si desea buscar en un subconjunto de los datos disponibles, se establece el filtro en el origen de datos al importar los datos. Con fines de aprendizaje se trabaja con datos de solo lectura, por lo que estableceremos el filtro en nuestra aplicación para que devuelva solo los documentos que incluyan el estado de Washington.

1. Abra Search.cshtml, busque el bloque de código **SearchParameters** (empieza en la línea 36) y agregue una línea de comentario además del filtro.

        var sp = new SearchParameters
        {
            //Add a filter for _Washington State
            Filter = "STATE_ALPHA eq 'WA'",
            // Specify whether Any or All of the search terms 
            SearchMode = SearchMode.All,
            // Include a count of results in the query result
            IncludeTotalResultCount = true,
            // Return an aggregated count of feature classes based on the specified query
            Facets = new[] { "FEATURE_CLASS" },
            // Limit the results to 20 documents
            Top = 20
        };


Los filtros se especifican con la sintaxis de OData y normalmente se usan con navegación por facetas o se incluyen en la cadena de consulta para restringir la consulta. Consulte [Sintaxis de filtro de OData](https://msdn.microsoft.com/library/azure/dn798921.aspx) para obtener más información.

2. Haga clic en **Ejecutar**.

3. Abra la aplicación.

4. Escriba el carácter comodín (*) para devolver un recuento. Observe que los resultados se limitan a 42.411 elementos, que son todos los documentos de todas las características geográficas del estado de Washington.

   ![][12]

## Incorporación de resaltado de referencias

Ahora que ha realizado una serie de cambios en una sola línea de código, querrá probar modificaciones más profundas que requieren cambios de código en varios lugares. La siguiente versión de **Search.cshtml** se puede pegar directamente en el archivo Search.cshtml en la sesión actual.

El código siguiente agrega resaltado de referencias. Tenga en cuenta las nuevas propiedades declaradas en [SearchParameters](https://msdn.microsoft.com/library/microsoft.azure.search.models.searchparameters_properties.aspx). También hay una nueva función que recorre en iteración la colección de resultados para obtener los documentos que se deben resaltar, además de HTML que representa el resultado.

Al ejecutar este ejemplo de código, las entradas de términos de búsqueda que tienen una correspondencia en los campos especificados se resaltan en negrita.

   ![][14]

Quizás quiera guardar una copia del archivo **Search.cshtml** original para comparar ambas versiones.

> [AZURE.NOTE] Los comentarios se han truncado para reducir el tamaño del archivo.
 
    @using System.Collections.Specialized
    @using System.Configuration
    @using Microsoft.Azure.Search
    @using Microsoft.Azure.Search.Models
    
    @{
    Layout = "~/_SiteLayout.cshtml";
    }
    
    @{
    // This modified search.cshtml file adds hit-highlighting
    
    string searchText = Request.Unvalidated["q"];
    string filterExpression = Request.Unvalidated["filter"];
    
    DocumentSearchResponse response = null;
    if (!string.IsNullOrEmpty(searchText))
    {
    searchText = searchText.Trim();
    
    // Retrieve search service name and an api key from configuration
    string searchServiceName = ConfigurationManager.AppSettings["SearchServiceName"];
    string apiKey = ConfigurationManager.AppSettings["SearchServiceApiKey"];
    
    var searchClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));
    var indexClient = searchClient.Indexes.GetClient("geonames");
    
    // Set the Search parameters used when executing the search request
    var sp = new SearchParameters
    {
    // Specify whether Any or All of the search terms must be matched in order to count the document as a match.
    SearchMode = SearchMode.All,
    // Include a count of results in the query result
    IncludeTotalResultCount = true,
    // Return an aggregated count of feature classes based on the specified query
    Facets = new[] { "FEATURE_CLASS" },
    // Limit the results to 20 documents
    Top = 20,
    // Enable hit-highlighting
    HighlightFields = new[] { "FEATURE_NAME", "DESCRIPTION", "FEATURE_CLASS", "COUNTY_NAME", "STATE_ALPHA" },
    HighlightPreTag = "<b>",
    HighlightPostTag = "</b>",
    };
    
    if (!string.IsNullOrEmpty(filterExpression))
    {
    // Add a search filter that will limit the search terms based on a specified expression.
    // This is often used with facets so that when a user selects a facet, the query is re-executed,
    // but limited based on the chosen facet value to further refine results
    sp.Filter = filterExpression;
    }
    
    // Execute the search query based on the specified search text and search parameters
    response = indexClient.Documents.Search(searchText, sp);
    }
    }
    
    @functions
    {
    private string GetFacetQueryString(string facetKey, string facetValue)
    {
    NameValueCollection queryString = HttpUtility.ParseQueryString(Request.Url.Query);
    queryString["filter"] = string.Format("{0} eq '{1}'", HttpUtility.UrlEncode(facetKey), HttpUtility.UrlEncode(facetValue));
    return queryString.ToString();
    }
    
    private HtmlString RenderHitHighlightedString(SearchResult item, string fieldName)
    {
    if (item.Highlights != null && item.Highlights.ContainsKey(fieldName))
    {
    string highlightedResult = string.Join("...", item.Highlights[fieldName]);
    return new HtmlString(highlightedResult);
    }
    return new HtmlString(item.Document[fieldName].ToString());
    }
    }
    
    <!-- Form to allow user to enter search terms -->
    <form class="form-inline" role="search">
    <div class="form-group">
    <input type="search" name="q" id="q" autocomplete="off" size="80" placeholder="Type something to search, i.e. 'park'." class="form-control" value="@searchText" />
    <button type="submit" id="search" class="btn btn-primary">Search</button>
    </div>
    </form>
    @if (response != null)
    {
    if (response.Count == 0)
    {
    <p style="padding-top:20px">No results found for "@searchText"</p>
    <h3>Suggestions:</h3>
    <ul>
    <li>Ensure words are spelled correctly.</li>
    <li>Try rephrasing keywords or using synonyms.</li>
    <li>Try less specific keywords.</li>
    </ul>
    }
    else
    {
    <div class="col-xs-3 col-md-2">
    <h3>Feature Class</h3>
    <ul class="list-unstyled">
    @if (!string.IsNullOrEmpty(filterExpression))
    {
    <li><a href="?q=@HttpUtility.UrlEncode(searchText)">All</a></li>
    }
    <!-- Cycle through the facet results and show the values and counts -->
    @foreach (var facet in response.Facets["FEATURE_CLASS"])
    {
    <li><a href="?@GetFacetQueryString("FEATURE_CLASS", (string)facet.Value)">@facet.Value (@facet.Count)</a></li>
    }
    </ul>
    </div>
    <div class="col-xs-12 col-sm-6 col-md-10">
    <p style="padding-top:20px">1 - @response.Results.Count of @response.Count results for "@searchText"</p>
    
    <ul class="list-unstyled">
    <!-- Cycle through the search results -->
    @foreach (var item in response.Results)
    {
    <li>
    <h3>@RenderHitHighlightedString(item, "FEATURE_NAME")</h3>
    <p>@RenderHitHighlightedString(item, "DESCRIPTION")</p>
    <p>@RenderHitHighlightedString(item, "FEATURE_CLASS"), elevation: @item.Document["ELEV_IN_M"] meters</p>
    <p>@RenderHitHighlightedString(item, "COUNTY_NAME") County, @RenderHitHighlightedString(item, "STATE_ALPHA")</p>
    <br />
    </li>
    }
    </ul>
    </div>
    }
    }

## ¿Por qué elegir Búsqueda de Azure?

Aunque otros servicios de Azure, como Base de datos SQL, ofrecen búsqueda de texto completo, un servicio como Búsqueda de Azure ofrece control sobre los ajustes, paginación y recuentos, resaltado de referencias, sugerencias para autocompletar consultas, compatibilidad con lenguaje natural, navegación por facetas, filtrado y mucho más. Como muestran algunos de nuestros [ejemplos](https://github.com/Azure-Samples?utf8=%E2%9C%93&query=search), es posible desarrollar una aplicación basada en búsqueda completa usando solo Búsqueda de Azure y ASP.NET.

## Pasos siguientes

Usando el servicio de solo lectura proporcionado en el sitio de [prueba del Servicio de aplicaciones de Azure](https://tryappservice.azure.com/), pudo ver la sintaxis de consulta y la búsqueda de texto completo en acción, obtuvo información sobre searchMode y los filtros, y agregó resaltado de referencias a la aplicación de búsqueda. En el siguiente paso, considere la posibilidad de crear y actualizar índices. Esto agrega la capacidad de:

- [Definir perfiles de puntuación](https://msdn.microsoft.com/library/dn798928.aspx), que se usan para ajustar las puntuaciones de búsqueda y mostrar en primer lugar los elementos de mayor valor.
- [Definir sugerencias](https://msdn.microsoft.com/library/mt131377.aspx) que agregan sugerencias de consulta autocompletando o anticipando la escritura de la entrada del usuario.
- [Definir indexadores](https://msdn.microsoft.com/library/dn946891.aspx), que actualizan el índice automáticamente cuando el origen de datos es Base de datos SQL de Azure o DocumentDB de Azure.

Para llevar a cabo todas estas tareas, necesitará una suscripción de Azure para poder crear y rellenar los índices de un servicio. Para obtener más información acerca de cómo suscribirse para una prueba gratuita, visite [https://azure.microsoft.com/pricing/free-trial](https://azure.microsoft.com/pricing/free-trial/).

Para obtener más información acerca de Búsqueda de Azure, visite nuestra [página de documentación](https://azure.microsoft.com/documentation/services/search/) en [http://azure.microsoft.com](https://azure.microsoft.com/) o consulte los [ejemplos y vídeos](search-video-demo-tutorial-list.md) que exploran la funcionalidad completa de Búsqueda de Azure.

## Hechos sobre el código y Búsqueda de Azure

Búsqueda de Azure es una plataforma totalmente administrada como un servicio [PaaS](https://en.wikipedia.org/wiki/Platform_as_a_service) que facilita a los desarrolladores la integración de interesantes experiencias de búsqueda en aplicaciones web y móviles. Los datos usados en las operaciones de búsqueda se almacenan con el servicio de búsqueda en Azure, donde la proximidad de los datos a las operaciones garantiza una baja latencia y comportamientos de búsqueda coherentes. Veamos esto con un poco más de detalle:

- Los datos, en los que se pueden efectuar búsquedas, se almacenan en un índice administrado por Búsqueda de Azure.
- El esquema que define el índice está definido por el usuario y especificará los campos de búsqueda, los campos que no son de búsqueda que podrían ser útiles en una expresión de filtro y construcciones como perfiles de puntuación para la optimización de resultados.
- Un índice de búsqueda contiene uno o varios documentos que son similares a una fila de una tabla. Es posible recuperarlos y realizar búsquedas en ellos.
- La mayoría de los índices se cargan desde un único conjunto de datos, preparado de antemano por el usuario, que incluye solo los campos que son útiles en el contexto de las operaciones de búsqueda. 
- Un indizador puede cargar los datos automáticamente (solo se admite para Base de datos SQL de Azure o DocumentDB de Azure) o una de las API de búsqueda de Azure puede insertarlos en un índice de búsqueda. Cuando se usa la API, puede insertar los datos de cualquier origen de datos, siempre que tenga el formato JSON.

En la opción [Servicio de aplicaciones de Azure](https://tryappservice.azure.com/), la plantilla "ASP.NET + Azure Search Site" proporciona código fuente para la aplicación web, que se puede modificar en Visual Studio Team Services (disponible como parte de la sesión de una hora). No se necesitan herramientas de desarrollo independientes para ver o cambiar el código.

El código se escribe en C#, usando la [biblioteca de cliente .NET de Búsqueda de Azure](https://msdn.microsoft.com/library/dn951165.aspx) para ejecutar las consultas en el índice, proporcionar navegación por facetas y mostrar recuentos y resultados de búsqueda en una página web.

Para generar y cargar el índice de búsqueda de USG se usó otro código, que no se incluye en la plantilla. Como el servicio es de solo lectura, todas las operaciones que requieren acceso de escritura deben completarse de antemano. Al final de este artículo, verá una [copia del esquema](#schema) usado para generar el esquema.

<a name="Schema"></a>
## Acerca del esquema

La siguiente captura de pantalla muestra el esquema usado para crear el índice que se usa en esta plantilla.
 
   ![][13]

### Archivo Schema.JSON

    {
      "@odata.context": "https://tryappservice.search.windows.net/$metadata#indexes/$entity",
      "name": "geonames",
      "fields": [
    {
      "name": "FEATURE_ID",
      "type": "Edm.String",
      "searchable": false,
      "filterable": false,
      "retrievable": true,
      "sortable": false,
      "facetable": false,
      "key": true,
      "analyzer": null
    },
    {
      "name": "FEATURE_NAME",
      "type": "Edm.String",
      "searchable": true,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": false,
      "key": false,
      "analyzer": null
    },
    {
      "name": "FEATURE_CLASS",
      "type": "Edm.String",
      "searchable": true,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    },
    {
      "name": "STATE_ALPHA",
      "type": "Edm.String",
      "searchable": true,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    },
    {
      "name": "STATE_NUMERIC",
      "type": "Edm.Int32",
      "searchable": false,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    },
    {
      "name": "COUNTY_NAME",
      "type": "Edm.String",
      "searchable": true,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    },
    {
      "name": "COUNTY_NUMERIC",
      "type": "Edm.Int32",
      "searchable": false,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    },
    {
      "name": "ELEV_IN_M",
      "type": "Edm.Int32",
      "searchable": false,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    },
    {
      "name": "ELEV_IN_FT",
      "type": "Edm.Int32",
      "searchable": false,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    },
    {
      "name": "MAP_NAME",
      "type": "Edm.String",
      "searchable": true,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": false,
      "key": false,
      "analyzer": null
    },
    {
      "name": "DESCRIPTION",
      "type": "Edm.String",
      "searchable": true,
      "filterable": false,
      "retrievable": true,
      "sortable": false,
      "facetable": false,
      "key": false,
      "analyzer": null
    },
    {
      "name": "HISTORY",
      "type": "Edm.String",
      "searchable": true,
      "filterable": false,
      "retrievable": true,
      "sortable": false,
      "facetable": false,
      "key": false,
      "analyzer": null
    },
    {
      "name": "DATE_CREATED",
      "type": "Edm.DateTimeOffset",
      "searchable": false,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    },
    {
      "name": "DATE_EDITED",
      "type": "Edm.DateTimeOffset",
      "searchable": false,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    }
      ],
      "scoringProfiles": [
    
      ],
      "defaultScoringProfile": null,
      "corsOptions": {
    "allowedOrigins": [
      "*"
    ],
    "maxAgeInSeconds": 300
      },
      "suggesters": [
    {
      "name": "AUTO_COMPLETE",
      "searchMode": "analyzingInfixMatching",
      "sourceFields": [
    "FEATURE_NAME",
    "FEATURE_CLASS",
    "COUNTY_NAME",
    "DESCRIPTION"
      ]
    }
      ]
    }

<!--Image references-->
[1]: ./media/search-tryappservice/AzSearch-TryAppService-TemplateTile.png
[2]: ./media/search-tryappservice/AzSearch-TryAppService-LoginAccount.png
[3]: ./media/search-tryappservice/AzSearch-TryAppService-AppCreated.png
[4]: ./media/search-tryappservice/AzSearch-TryAppService-BrowseSite.png
[5]: ./media/search-tryappservice/AzSearch-TryAppService-GetStarted.png
[6]: ./media/search-tryappservice/AzSearch-TryAppService-QueryWA.png
[7]: ./media/search-tryappservice/AzSearch-TryAppService-QueryPrecedence.png
[8]: ./media/search-tryappservice/AzSearch-TryAppService-VSOConnect.png
[9]: ./media/search-tryappservice/AzSearch-TryAppService-cSharpSample.png
[10]: ./media/search-tryappservice/AzSearch-TryAppService-RunBtn.png
[11]: ./media/search-tryappservice/AzSearch-TryAppService-searchmodeany.png
[12]: ./media/search-tryappservice/AzSearch-TryAppService-searchmodeWAState.png
[13]: ./media/search-tryappservice/AzSearch-TryAppService-Schema.png
[14]: ./media/search-tryappservice/AzSearch-TryAppService-HitHighlight.png

<!---HONumber=AcomDC_0224_2016-->