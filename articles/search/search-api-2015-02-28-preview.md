<properties
   pageTitle="API de REST del Servicio Búsqueda de Azure versión 2015-02-28-Preview | Microsoft Azure | API de vista previa de Búsqueda de Azure"
   description="La API de REST del Servicio Búsqueda de Azure versión 2015-02-28-Preview incluye funciones experimentales como analizadores de lenguaje Natural y búsquedas moreLikeThis."
   services="search"
   documentationCenter="na"
   authors="HeidiSteen"
   manager="mblythe"
   editor=""/>

<tags
   ms.service="search"
   ms.devlang="rest-api"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="search"
   ms.date="02/16/2016"
   ms.author="heidist"/>

# API de REST del Servicio Búsqueda de Azure versión 2015-02-28-Preview

Este artículo es la documentación de referencia de `api-version=2015-02-28-Preview`. Esta vista previa amplía la versión disponible generalmente actual, [api-version=2015-02-28](https://msdn.microsoft.com/library/dn798935.aspx), proporcionando las siguientes funciones experimentales:

- Parámetro de consulta `moreLikeThis` en API de [Buscar documentos](#SearchDocs). Busca otros documentos que sean relevantes para otro documento específico.

Algunas partes adicionales de la API de REST `2015-02-28-Preview` se documentan por separado. Entre ellos se incluyen los siguientes:

- [Perfiles de puntuación](search-api-scoring-profiles-2015-02-28-preview.md)
- [Indexadores](search-api-indexers-2015-02-28-preview.md)

El servicio de Búsqueda de Azure está disponible en varias versiones. Consulte [Versiones del servicio de búsqueda](http://msdn.microsoft.com/library/azure/dn864560.aspx) para obtener más información.

## API incluidas en este documento

La API del servicio Búsqueda de Azure admite dos sintaxis de URL para operaciones de API: simple y OData (consulte [Compatibilidad con OData (API de Búsqueda de Azure)](http://msdn.microsoft.com/library/azure/dn798932.aspx) para obtener más información). La lista siguiente muestra la sintaxis simple.

[Crear índice](#CreateIndex)

    POST /indexes?api-version=2015-02-28-Preview

[Actualizar índice](#UpdateIndex)

    PUT /indexes/[index name]?api-version=2015-02-28-Preview

[Obtener índice](#GetIndex)

    GET /indexes/[index name]?api-version=2015-02-28-Preview

[Enumerar índices](#ListIndexes)

    GET /indexes?api-version=2015-02-28-Preview

[Obtención de estadísticas de índice](#GetIndexStats)

    GET /indexes/[index name]/stats?api-version=2015-02-28-Preview

[Eliminar un índice](#DeleteIndex)

    DELETE /indexes/[index name]?api-version=2015-02-28-Preview

[Agregar, eliminar y actualizar datos en un índice](#AddOrUpdateDocuments)

    POST /indexes/[index name]/docs/index?api-version=2015-02-28-Preview

[Buscar en documentos](#SearchDocs)

    GET /indexes/[index name]/docs?[query parameters]
    POST /indexes/[index name]/docs/search?api-version=2015-02-28-Preview

[Buscar documento](#LookupAPI)

     GET /indexes/[index name]/docs/[key]?[query parameters]

[Documentos de recuento](#CountDocs)

    GET /indexes/[index name]/docs/$count?api-version=2015-02-28-Preview

[Sugerencias](#Suggestions)

    GET /indexes/[index name]/docs/suggest?[query parameters]
    POST /indexes/[index name]/docs/suggest?api-version=2015-02-28-Preview

________________________________________
<a name="IndexOps"></a>
## Operaciones de índice

Puede crear y administrar índices en el servicio de Búsqueda de Azure a través de solicitudes HTTP sencillas (POST, GET, PUT, DELETE) en un recurso de índice determinado. Para crear un índice, primero debe PUBLICAR un documento JSON que describa el esquema de índice. El esquema define los campos de índice, sus tipos de datos y cómo pueden utilizarse (por ejemplo, en las búsquedas de texto completo, filtros, ordenación o faceting). También define los perfiles de puntuación, los proveedores de sugerencias y otros atributos para configurar el comportamiento del índice.

En el ejemplo siguiente se proporciona una ilustración de un esquema que se utiliza para buscar información sobre hoteles con el campo de descripción definido en dos idiomas. Observe de qué modo controlan los atributos cómo se utiliza el campo. Por ejemplo, el `hotelId` se utiliza como clave de documento (`"key": true`) y se excluye de búsquedas de texto completo (`"searchable": false`).

    {
    "name": "hotels",  
    "fields": [
      {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false},
      {"name": "baseRate", "type": "Edm.Double"},
      {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
	  {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
      {"name": "hotelName", "type": "Edm.String"},
      {"name": "category", "type": "Edm.String"},
      {"name": "tags", "type": "Collection(Edm.String)"},
      {"name": "parkingIncluded", "type": "Edm.Boolean"},
      {"name": "smokingAllowed", "type": "Edm.Boolean"},
      {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
      {"name": "rating", "type": "Edm.Int32"},
      {"name": "location", "type": "Edm.GeographyPoint"}
     ],
     "suggesters": [
      {
       "name": "sg",
       "searchMode": "analyzingInfixMatching",
       "sourceFields": ["hotelName"]
      }
     ]
    }

Una vez creado el índice, podrá cargar documentos que rellenen el índice. Consulte [Agregar o actualizar documentos](#AddOrUpdateDocuments) para este siguiente paso.

Para obtener una introducción de vídeo sobre la indexación en Búsqueda de Azure, consulte el [episodio de portada de nube del canal 9 en Búsqueda de Azure](http://go.microsoft.com/fwlink/p/?LinkId=511509).


<a name="CreateIndex"></a>
## Crear índice

Un índice es el medio principal para organizar y buscar documentos en la Búsqueda de Azure, de modo similar a cómo organiza los registros en una base de datos una tabla. Cada índice tiene una colección de documentos que cumplen el esquema de índice (nombres de campo, tipos de datos y propiedades), pero los índices también especifican construcciones adicionales (proveedores de sugerencias, opciones de CORS y perfiles de puntuación) que definen otros comportamientos de búsqueda.

Puede crear un índice nuevo dentro de un servicio de Búsqueda de Azure mediante una solicitud HTTP POST o PUT. El cuerpo de la solicitud es un esquema JSON que especifica la información de índice y configuración.

    POST https://[service name].search.windows.net/indexes?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

Como alternativa, puede usar PUT y especificar el nombre del índice en el URI. Si el índice no existe, se creará.

    PUT https://[search service url]/indexes/[index name]?api-version=[api-version]

Crear un índice determina la estructura de los documentos almacenados y usados en operaciones de búsqueda. Rellenar el índice es una operación independiente. En este paso, puede usar un [indexador](https://msdn.microsoft.com/library/azure/mt183328.aspx) (disponible para orígenes de datos admitidos) o una operación [Agregar, actualizar o eliminar documentos](https://msdn.microsoft.com/library/azure/dn798930.aspx). El índice invertido se genera cuando se publican los documentos.

**Nota**: El número máximo de índices permitido varía según el nivel de precios. El servicio gratuito permite hasta tres índices. El servicio estándar permite 50 índices por servicio de búsqueda. Consulte [Límites y restricciones](http://msdn.microsoft.com/library/azure/dn798934.aspx) para obtener detalles.

**Solicitud**

HTTPS es necesario para todas las solicitudes de servicio. La solicitud **Crear índice** se puede construir con un método POST o PUT. Al usar POST, debe proporcionar un nombre de índice en el cuerpo de la solicitud junto con la definición de esquema de índice. Con PUT, el nombre del índice es parte de la dirección URL. Si el índice no existe, se crea. Si ya existe, se actualiza a la nueva definición.

El nombre del índice debe estar en minúsculas, comenzar por una letra o un número, no tener ninguna barra o punto y tener menos de 128 caracteres. Después de iniciar el nombre del índice por una letra o un número, el resto del nombre puede incluir cualquier letra, número y guiones, siempre que los guiones no aparezcan de manera consecutiva.

`api-version` es obligatorio Consulte [Versiones del servicio de búsqueda](http://msdn.microsoft.com/library/azure/dn864560.aspx) para obtener una lista de las versiones disponibles.

**Encabezados de solicitud**

En la lista siguiente se describen los encabezados de solicitud obligatorios y opcionales.

- `Content-Type`: obligatorio. Establézcalo en `application/json`
- `api-key`: obligatorio. El `api-key` se usa para
- autenticar la solicitud al servicio de búsqueda. Es un valor de cadena único para el servicio. La solicitud **Crear índice** debe incluir un encabezado `api-key` establecido en su clave de administración (en lugar de una clave de consulta).

También necesitará el nombre del servicio para construir la dirección URL de la solicitud. Puede obtener el nombre de servicio y `api-key` desde el panel de servicio en el Portal de Azure. Consulte [Crear un servicio de Búsqueda de Azure en el portal](search-create-service-portal.md) para obtener ayuda sobre la navegación en páginas.

<a name="RequestData"></a> **Sintaxis del cuerpo de la solicitud**

El cuerpo de la solicitud contiene una definición de esquema, que incluye la lista de campos de datos de los documentos que se introducirán en este índice, tipos de datos, atributos, así como una lista opcional de perfiles de puntuación que se utilizan para puntuar documentos de coincidencias de puntuación en el momento de las consultas.

Tenga en cuenta que para una solicitud POST, debe especificar el nombre del índice en el cuerpo de la solicitud.

Solo puede haber un campo de clave en el índice. Debe ser un campo de cadena. Este campo representa el identificador único de cada documento almacenado en el índice.

Las partes principales de un índice son las siguientes:

- `name`
- `fields` que se introducirán en este índice, incluido el nombre, tipo de datos y propiedades que definen las acciones permitidas en ese campo.
- `suggesters` se usa para autocompletar o anticipar la escritura de las consultas.
- `scoringProfiles` se usa para la clasificación de la puntuación de la búsqueda personalizada. Consulte [Agregar perfiles de puntuación](https://msdn.microsoft.com/library/azure/dn798928.aspx) para obtener más información.
- `defaultScoringProfile` se usa para sobrescribir los comportamientos de puntuación predeterminados.
- `corsOptions` para permitir las consultas de origen cruzado en el índice.

La sintaxis para estructurar la carga de la solicitud es la siguiente. En este tema se proporciona una solicitud de ejemplo.

    {
      "name": (optional on PUT; required on POST) "name_of_index",
      "fields": [
        {
          "name": "name_of_field",
          "type": "Edm.String | Collection(Edm.String) | Edm.Int32 | Edm.Int64 | Edm.Double | Edm.Boolean | Edm.DateTimeOffset | Edm.GeographyPoint",
          "searchable": true (default where applicable) | false (only Edm.String and Collection(Edm.String) fields can be searchable),
          "filterable": true (default) | false,
          "sortable": true (default where applicable) | false (Collection(Edm.String) fields cannot be sortable),
          "facetable": true (default where applicable) | false (Edm.GeographyPoint fields cannot be facetable),
          "key": true | false (default, only Edm.String fields can be keys),
          "retrievable": true (default) | false,		      
          "analyzer": "name of the analyzer used for search and indexing", (only if 'searchAnalyzer' and 'indexAnalyzer' are not set)
          "searchAnalyzer": "name of the search analyzer", (only if 'indexAnalyzer' is set and 'analyzer' is not set)
          "indexAnalyzer": "name of the indexing analyzer" (only if 'searchAnalyzer' is set and 'analyzer' is not set)
        }
      ],
      "suggesters": [
        {
          "name": "name of suggester",
          "searchMode": "analyzingInfixMatching" (other modes may be added in the future),
          "sourceFields": ["field1", "field2", ...]
        }
      ],
      "scoringProfiles": [
        {
          "name": "name of scoring profile",
          "text": (optional, only applies to searchable fields) {
            "weights": {
              "searchable_field_name": relative_weight_value (positive numbers),
              ...
            }
          },
          "functions": (optional) [
            {
              "type": "magnitude | freshness | distance | tag",
              "boost": # (positive number used as multiplier for raw score != 1),
              "fieldName": "...",
              "interpolation": "constant | linear (default) | quadratic | logarithmic",
              "magnitude": {
                "boostingRangeStart": #,
                "boostingRangeEnd": #,
                "constantBoostBeyondRange": true | false (default)
              },
              "freshness": {
                "boostingDuration": "..." (value representing timespan leading to now over which boosting occurs)
              },
              "distance": {
                "referencePointParameter": "...", (parameter to be passed in queries to use as reference location, see "scoringParameter" for syntax details)
                "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)
              },
			  "tag": {
				"tagsParameter": "..." (parameter to be passed in queries to specify list of tags to compare against target field, see "scoringParameter" for syntax details)
              }
            }
          ],
          "functionAggregation": (optional, applies only when functions are specified)
            "sum (default) | average | minimum | maximum | firstMatching"
        }
      ],
      "defaultScoringProfile": (optional) "...",
      "corsOptions": (optional) {
        "allowedOrigins": ["*"] | ["origin_1", "origin_2", ...],
        "maxAgeInSeconds": (optional) max_age_in_seconds (non-negative integer)
      }
    }

**Atributos de índice**

Es posible establecer los siguientes atributos para crear un índice. Para obtener más información sobre la puntuación y los perfiles de puntuación, consulte [Agregar perfiles de puntuación](https://msdn.microsoft.com/library/azure/dn798928.aspx).

`name` : establece el nombre del campo.

`type` : establece el tipo de datos del campo. Consulte [Tipos de datos admitidos](#DataTypes) para obtener una lista de tipos admitidos.

`searchable`: marca el campo como de búsqueda de texto completo. Esto significa que se someterá a análisis como la separación de palabras durante la indexación. Si establece un campo `searchable` en un valor como "día soleado", internamente, se dividirá en los tokens individuales "soleado" y "día". Esto permite realizar búsquedas de texto completo de estos términos. Los campos de tipo `Edm.String` o `Collection(Edm.String)` son `searchable` de manera predeterminada. Los campos de otros tipos no pueden ser `searchable`.

  - **Nota**: Los campos `searchable` consumen espacio adicional en el índice, ya que Búsqueda de Azure almacenará una versión con tokens adicional del valor del campo para las búsquedas de texto completo. Si desea ahorrar espacio en el índice y no necesita incluir un campo en las búsquedas, establezca `searchable` en `false`.

`filterable`: permite hacer referencia al campo en consultas de `$filter`. `filterable` difiere de `searchable` en cómo se controlan las cadenas. Los campos de tipo `Edm.String` o `Collection(Edm.String)` que son `filterable` no sufren separación de palabras, por lo que las comparaciones son solo para las coincidencias exactas. Por ejemplo, si establece este campo `f` en "día soleado", `$filter=f eq 'sunny'` no encontrará ninguna coincidencia, pero `$filter=f eq 'sunny day'` sí. Todos los campos son `filterable` de forma predeterminada.

`sortable`: de forma predeterminada el sistema ordena los resultados por calificación, pero en muchas experiencias los usuarios desearán ordenar por los campos de los documentos. Los campos de tipo `Collection(Edm.String)` no pueden ser `sortable`. El resto de campos son `sortable` de forma predeterminada.

`facetable`: suele utilizarse en una presentación de resultados de búsqueda que incluya el número de resultados por categoría (por ejemplo, busque cámaras digitales y consulte los resultados divididos por marca, por megapíxeles, por precio, etc.). Esta opción no puede utilizarse con campos de tipo `Edm.GeographyPoint`. El resto de campos son `facetable` de forma predeterminada.

  - **Nota**: los campos de tipo `Edm.String` que son `filterable`, `sortable` o `facetable` solo pueden ocupar una longitud de 32 KB como máximo. Esto se debe a que esos campos se tratan como un término de búsqueda único y la longitud máxima de un término de Búsqueda de Azure es de 32 KB. Si necesita almacenar más texto que este en un campo de cadena único, deberá establecer explícitamente `filterable`, `sortable` y `facetable` en `false` en la definición del índice.

  - **Nota**: Si un campo no tiene ninguno de los atributos anteriores establecidos en `true` (`searchable`, `filterable`, `sortable` o `facetable`) el campo se excluirá eficazmente del índice invertido. Esta opción es útil para los campos que no se utilizan en las consultas, pero que son necesarios en los resultados de la búsqueda. La exclusión de esos campos del índice mejora el rendimiento.

`suggestions`: las versiones anteriores de la API incluían una propiedad `suggestions`. Esta propiedad booleana ahora está en desuso y ya no está disponible en `2015-02-28` o en `2015-02-28-Preview`. Use la [API de proveedores de sugerencias](#Suggesters) en su lugar. En la versión `2014-07-31`, se ha usado la propiedad `suggestions` para especificar si el campo podría usarse para autocompletar, para campos de tipo `Edm.String` o `Collection(Edm.String)`. `suggestions` fue `false` de forma predeterminada porque necesitaba espacio adicional en el índice, pero si lo habilitó, consulte [Transición de la vista previa al lanzamiento General en Búsqueda de Azure](search-transition-from-preview.md) para obtener instrucciones sobre cómo realizar una transición a la nueva API.

`key`: marca el campo como que contiene identificadores únicos para los documentos del índice. Es necesario elegir exactamente un campo como campo `key` y debe ser de tipo `Edm.String`. Los campos de clave pueden usarse para buscar documentos directamente a través de la [API de búsqueda](#LookupAPI).

`retrievable`: establece si el campo se puede devolver un resultado de búsqueda. Esto resulta útil cuando desea usar un campo (por ejemplo, margen) como filtro, ordenación o mecanismo de puntuación, pero no desea que el campo sea visible para el usuario final. Este atributo debe ser `true` para los campos `key`.

`analyzer`: establece el nombre del analizador que se usa para el campo en el momento de la búsqueda y la indexación. Para conocer el conjunto de valores permitido, consulte [Analizadores](https://msdn.microsoft.com/library/mt605304.aspx). Esta opción puede utilizarse solo con campos `searchable` y no se puede establecer junto con `searchAnalyzer` o `indexAnalyzer`. Una vez que se elige el analizador, no se podrá cambiar para el campo.

`searchAnalyzer`: establece el nombre del analizador que se usa en el momento de búsqueda para el campo. Para conocer el conjunto de valores permitido, consulte [Analizadores](https://msdn.microsoft.com/library/mt605304.aspx). Esta opción solo puede utilizarse con campos `searchable`. Se debe establecer junto con `indexAnalyzer` y no se puede establecer junto con la opción `analyzer`. Este analizador se puede actualizar en un campo existente.

`indexAnalyzer`: establece el nombre del analizador que se usa en el momento de la indexación para el campo. Para conocer el conjunto de valores permitido, consulte [Analizadores](https://msdn.microsoft.com/library/mt605304.aspx). Esta opción solo puede utilizarse con campos `searchable`. Se debe establecer junto con `searchAnalyzer` y no se puede establecer junto con la opción `analyzer`. Una vez que se elige el analizador, no se podrá cambiar para el campo.

`suggesters`: establece el modo de búsqueda y los campos que son el origen del contenido para obtener sugerencias. Consulte [Proveedores de sugerencias](#Suggesters) para obtener más información.

`scoringProfiles`: define comportamientos de puntuación personalizados que permiten influir en los elementos que aparecen más arriba en los resultados de la búsqueda. Los perfiles de puntuación se componen de ponderaciones de campos y de funciones. Consulte [Agregar perfiles de puntuación](https://msdn.microsoft.com/library/azure/dn798928.aspx) para obtener más información acerca de los atributos usados en un perfil de puntuación.

<!-- This is a standalone topic in MSDN -->
<a name="LanguageSupport"></a> **Compatibilidad con idioma**

Los campos localizables se someten a análisis que con frecuencia implican la separación de palabras, la normalización de texto y el filtrado de términos. De forma predeterminada, los campos localizables de la Búsqueda de Azure se analizan con el [Analizador Apache Lucene estándar](http://lucene.apache.org/core/4_9_0/analyzers-common/index.html) que divide el texto en elementos siguiendo las reglas de ["Segmentación de texto Unicode"](http://unicode.org/reports/tr29/). Además, el analizador estándar convierte todos los caracteres en minúsculas. Los documentos indexados y lo términos de búsqueda son sometidos a análisis durante la indexación y el procesamiento de consultas.

Búsqueda de Azure admite una variedad de lenguajes. Cada uno de esos idiomas requiere un analizador de texto no estándar que representa las características de un idioma determinado. Búsqueda de Azure ofrece dos tipos de analizadores:

- 35 analizadores respaldados por Lucene.
- 50 analizadores respaldados por la tecnología de procesamiento de lenguaje natural de Microsoft usada en Office y Bing.

Es posible que algunos desarrolladores prefieran la solución más familiar, simple y de código abierto de Lucene. Los analizadores de Lucene son más rápidos, pero los analizadores de Microsoft disponen de capacidades avanzadas, como la lematización, la descomposición de palabras (en idiomas como el alemán, danés, neerlandés, sueco, noruego, estonio, finés, húngaro, eslovaco) y el reconocimiento de entidades (direcciones URL, correos electrónicos, fechas y números). Si es posible, debe ejecutar las comparaciones de los analizadores de Microsoft y Lucene para decidir cuál es la que se ajusta mejor.

***Cómo se comparan***

El analizador de Lucene para inglés amplía el analizador estándar. Elimina los posesivos (los ’s finales) de las palabras, aplica la lematización conforme al [Algoritmo de lematización Porter](http://tartarus.org/~martin/PorterStemmer/) y elimina las [palabras no significativas](http://en.wikipedia.org/wiki/Stop_words) del inglés.

En comparación, el analizador de Microsoft realiza la lematización en lugar del stemming. Significa que puede controlar mucho mejor formas de palabras derivadas e irregulares, lo que da como resultado unos resultados de búsqueda más pertinentes (consulte el módulo 7 de [presentación MVA de Búsqueda de Azure](http://www.microsoftvirtualacademy.com/training-courses/adding-microsoft-azure-search-to-your-websites-and-apps) para obtener más detalles).

La indexación con analizadores de Microsoft es entre dos y tres veces más lenta de media que sus equivalentes de Lucene en función del idioma. El rendimiento de la búsqueda no debería verse afectado significativamente en las consultas de tamaño medio.

***Configuración***

Para cada campo de la definición del índice, puede establecer la propiedad `analyzer` en un nombre de analizador que especifica el idioma y el proveedor. Se aplicará el mismo analizador durante la búsqueda e indización de ese campo. Por ejemplo, puede tener campos separados para descripciones de hoteles en inglés, francés y español que existen en paralelo dentro del mismo índice. Use el [parámetro de consulta "searchFields"](#SearchQueryParameters) para especificar qué campo concreto del lenguaje buscar en las consultas. Puede revisar ejemplos de consultas que incluyan la propiedad `analyzer` en [Buscar documentos](#SearchDocs).

***Lista de analizadores***

A continuación se muestra la lista de idiomas admitidos y los nombres de analizadores Lucene y Microsoft.

<table style="font-size:12">
    <tr>
		<th>Lenguaje</th>
		<th>Nombre del analizador de Microsoft</th>
		<th>Nombre del analizador de Lucene</th>
	</tr>
    <tr>
		<td>Árabe</td>
		<td>ar.microsoft</td>
		<td>ar.lucene</td>		
	</tr>
    <tr>
    	<td>Armenio</td>
		<td></td>
    	<td>hy.lucene</td>
  	</tr>
    <tr>
		<td>Bangla</td>
		<td>bn.microsoft</td>
		<td></td>
	</tr>
  	<tr>
    	<td>Vasco</td>
		<td></td>
    	<td>eu.Lucene</td>
    </tr>
  	<tr>
 		<td>Búlgaro</td>
		<td>bg.microsoft</td>
    	<td>bg.lucene</td>
  	</tr>
  	<tr>
    	<td>Catalán</td>
    	<td>ca.microsoft</td>
		<td>ca.lucene</td>  		
  	</tr>
    <tr>
		<td>Chino simplificado</td>
		<td>zh-Hans.microsoft</td>
		<td>zh-Hans.lucene</td>		
	</tr>
    <tr>
		<td>Chino tradicional</td>
		<td>zh-Hant.microsoft</td>
		<td>zh-Hant.lucene</td>		
	<tr>
    <tr>
		<td>Croata</td>
		<td>hr.microsoft</td>
		<td/></td>
	</tr>
    <tr>
		<td>Checo</td>
		<td>cs.microsoft</td>
		<td>cs.lucene</td>		
	</tr>    
    <tr>
		<td>Danés</td>
		<td>da.microsoft</td>
		<td>da.lucene</td>		
	</tr>    
    <tr>
		<td>Neerlandés</td>
		<td>nl.microsoft</td>
		<td>nl.lucene</td>	
	</tr>    
    <tr>
		<td>English</td>		
		<td>en.microsoft</td>
		<td>en.lucene</td>		
	</tr>
    <tr>
		<td>Estonio</td>
		<td>et.microsoft</td>
		<td></td>
	</tr>
    <tr>
		<td>Finés</td>
		<td>fi.microsoft</td>
		<td>fi.lucene</td>		
	</tr>    
    <tr>
		<td>Francés</td>
		<td>fr.microsoft</td>
		<td>fr.lucene</td>		
	</tr>
    <tr>
    	<td>Gallego</td>
	    <td></td>
		<td>gl.lucene</td>    	
  	</tr>
    <tr>
		<td>Alemán</td>
		<td>de.microsoft</td>
		<td>de.lucene</td>		
	</tr>
    <tr>
		<td>Griego</td>
		<td>el.microsoft</td>
		<td>el.lucene</td>		
	</tr>
    <tr>
		<td>Gujarati</td>
		<td>gu.microsoft</td>
		<td></td>
	</tr>
    <tr>
		<td>Hebreo</td>
		<td>he.microsoft</td>
		<td></td>
	</tr>
    <tr>
		<td>Hindi</td>
		<td>hi.microsoft</td>
		<td>hi.lucene</td>		
	</tr>
    <tr>
		<td>Húngaro</td>		
		<td>hu.microsoft</td>
		<td>hu.lucene</td>
	</tr>
    <tr>
		<td>Islandés</td>
		<td>is.microsoft</td>
		<td></td>
	</tr>
    <tr>
		<td>Indonesio (Bahasa)</td>
		<td>id.microsoft</td>
		<td>id.lucene</td>		
	</tr>
    <tr>
    	<td>Irlandés</td>
		<td></td>
      	<td>ga.lucene</td>
    </tr>
    <tr>
		<td>Italiano</td>
		<td>it.microsoft</td>
		<td>it.lucene</td>		
	</tr>
    <tr>
		<td>Japonés</td>
		<td>ja.microsoft</td>
		<td>ja.lucene</td>
		
	</tr>
    <tr>
		<td>Kannada</td>
		<td>ka.microsoft</td>
		<td></td>
	</tr>
    <tr>
		<td>Coreano</td>
		<td>ko.Microsoft</td>
		<td>ko.lucene</td>
	</tr>
    <tr>
		<td>Letón</td>		
		<td>lv.microsoft</td>
		<td>lv.lucene</td>	
	</tr>
    <tr>
		<td>Lituano</td>
		<td>lt.microsoft</td>
		<td></td>
	</tr>
    <tr>
		<td>Malayalam</td>
		<td>ml.microsoft</td>
		<td></td>
	</tr>
    <tr>
		<td>Malayo (latino)</td>
		<td>ms.microsoft</td>
		<td></td>
	</tr>
    <tr>
		<td>Marathi</td>
		<td>mr.microsoft</td>
		<td></td>
	</tr>
    <tr>
		<td>Noruego</td>
		<td>nb.microsoft</td>
		<td>no.lucene</td>		
	</tr>
  	<tr>
    	<td>Persa</td>
		<td></td>
		<td>fa.lucene</td>    	
  	</tr>
    <tr>
		<td>Polaco</td>
		<td>pl.microsoft</td>
		<td>pl.lucene</td>		
	</tr>
    <tr>
		<td>Portugués (Brasil)</td>
		<td>pt-Br.microsoft</td>
		<td>pt-Br.lucene</td>		
	</tr>
    <tr>
		<td>Portugués (Portugal)</td>
		<td>pt-Pt.microsoft</td>		
		<td>pt-Pt.lucene</td>
	</tr>
    <tr>
		<td>Punjabi</td>
		<td>pa.microsoft</td>
		<td></td>
	</tr>
    <tr>
		<td>Rumano</td>
		<td>ro.microsoft</td>
		<td>ro.lucene</td>
	</tr>
    <tr>
		<td>Ruso</td>
		<td>ru.microsoft</td>
		<td>ru.lucene</td>	
	</tr>
    <tr>
		<td>Serbio (cirílico)</td>
		<td>sr-cyrillic.microsoft</td>
		<td></td>
	</tr>
    <tr>
		<td>Serbio (latino)</td>
		<td>sr-latin.microsoft</td>
		<td></td>
	</tr>
    <tr>
		<td>Eslovaco</td>
		<td>sk.microsoft</td>
		<td></td>
	</tr>
    <tr>
		<td>Esloveno</td>
		<td>sl.microsoft</td>
		<td></td>
	</tr>
    <tr>
		<td>Español</td>
		<td>es.Microsoft</td>
		<td>es.lucene</td>
	</tr>
    <tr>
		<td>Sueco</td>
		<td>sv.microsoft</td>
		<td>sv.lucene</td>
	</tr>

    <tr>
		<td>Tamil</td>
		<td>ta.microsoft</td>
		<td></td>
	</tr>
    <tr>
		<td>Telugu</td>
		<td>te.microsoft</td>
		<td></td>
	</tr>
    <tr>
		<td>Tailandés</td>
		<td>th.microsoft</td>
		<td>th.lucene</td>
	</tr>
    <tr>
		<td>Turco</td>
		<td>tr.microsoft</td>
		<td>tr.lucene</td>		
	</tr>
    <tr>
		<td>Ucraniano</td>
		<td>uk.microsoft</td>
		<td></td>
	</tr>
    <tr>
		<td>Urdu</td>
		<td>ur.microsoft</td>
		<td></td>
	</tr>
    <tr>
		<td>Vietnamita</td>
		<td>vi.microsoft</td>
		<td></td>
	</tr>
	<td colspan="3">Además, Búsqueda de Azure proporciona las configuraciones del analizador de lenguaje válido</td>
    <tr>
		<td>Plegamiento de ASCII estándar</td>
		<td>standardasciifolding.lucene</td>
		<td>
		<ul>
			<li>Segmentación de texto Unicode (Tokenizer estándar)</li>
			<li>Filtro de plegamiento de ASCII: convierte los caracteres Unicode que no pertenecen al conjunto de los primeros 127 caracteres ASCII en sus valores equivalentes ASCII. Esto es útil para quitar los signos diacríticos.</li>
		</ul>
		</td>
	</tr>
</table>

Todos los analizadores con nombres anotados con <i>lucene</i> disponen de tecnología de [analizadores de idioma de Apache Lucene](http://lucene.apache.org/core/4_9_0/analyzers-common/overview-summary.html). Puede encontrar más información sobre el filtro de plegamiento de ASCII [aquí](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/miscellaneous/ASCIIFoldingFilter.html).

**Proveedores de sugerencias**

Un `suggester` define qué campos de un índice se utilizan para admitir Autocompletar en las búsquedas. Normalmente las cadenas de búsqueda parcial se envían a las [API de sugerencias](#Suggestions) mientras el usuario está escribiendo una consulta de búsqueda y la API devuelve un conjunto de frases sugeridas. El proveedor de sugerencias que defina en el índice determina qué campos se utilizan para generar los términos de búsqueda de escritura anticipada. Consulte [Proveedores de sugerencias](#Suggesters) para obtener más detalles sobre la configuración.

**Perfiles de puntuación**

Un `scoringProfile` define comportamientos de puntuación personalizados que permiten influir en los elementos que aparecen más arriba en los resultados de la búsqueda. Los perfiles de puntuación se componen de ponderaciones de campos y de funciones. Para poder utilizarlos, especifique un perfil por nombre en la cadena de consulta.

El perfil de puntuación predeterminada funciona en segundo plano para calcular un resultado de búsqueda para todos los elementos de un conjunto de resultados. Puede usar un perfil de puntuación interno y sin nombre. Como alternativa, establezca `defaultScoringProfile` para usar un perfil personalizado como valor predeterminado, al que se invoca cuando no se especifica un perfil personalizado en la cadena de consulta.

Consulte [Adición de perfiles de puntuación a un índice de búsqueda (API de REST de servicio Búsqueda de Azure)](search-api-scoring-profiles-2015-02-28-preview.md) para obtener más información.

**Opciones de CORS**

Javascript del lado cliente no puede llamar a las API de forma predeterminada debido a que el explorador evitará todas las solicitudes entre orígenes. Habilite CORS (uso compartido recursos entre orígenes) estableciendo el atributo `corsOptions` para que permita consultas de origen cruzado en su índice. Tenga en cuenta que solamente las API de consulta admiten CORS por motivos de seguridad. Se pueden establecer las opciones siguientes para CORS:

- `allowedOrigins` (obligatorio): se trata de una lista de orígenes a los que se le concederá acceso a su índice. Esto significa que cualquier código Javascript que se suministre desde esos orígenes podrá consultar el índice (suponiendo que proporcione la clave de API correcta). Cada origen suele ser de formato `protocol://fully-qualified-domain-name:port`, aunque a menudo se omite el puerto. Consulte [este artículo](http://go.microsoft.com/fwlink/?LinkId=330822) para obtener más detalles.
 - Si desea permitir el acceso a todos los orígenes, incluya `*` como elemento único en la matriz `allowedOrigins`. Tenga en cuenta que **esta no es una práctica recomendada para los servicios de búsqueda de producción.** Sin embargo, puede ser útil para el desarrollo o con fines de depuración.
- `maxAgeInSeconds` (opcional): los exploradores usan este valor para determinar la duración (en segundos) para almacenar en la memoria caché las respuestas preparatorias de CORS. Esto debe ser un entero no negativo. Cuanto mayor sea este valor es, mejor será el rendimiento, pero más tiempo tardarán en surtir efecto los cambios en la directiva CORS. Si no se establece, se usará una duración predeterminada de 5 minutos.

<a name="CreateUpdateIndexExample"></a> **Ejemplo de cuerpo de solicitud**

    {
      "name": "hotels",  
      "fields": [
        {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false},
        {"name": "baseRate", "type": "Edm.Double"},
        {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
	    {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, analyzer="fr.lucene"},
        {"name": "hotelName", "type": "Edm.String"},
        {"name": "category", "type": "Edm.String"},
        {"name": "tags", "type": "Collection(Edm.String)"},
        {"name": "parkingIncluded", "type": "Edm.Boolean"},
        {"name": "smokingAllowed", "type": "Edm.Boolean"},
        {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
        {"name": "rating", "type": "Edm.Int32"},
        {"name": "location", "type": "Edm.GeographyPoint"}
      ],
      "suggesters": [
        {
          "name": "sg",
          "searchMode": "analyzingInfixMatching",
          "sourceFields": ["hotelName"]
        }
      ]
    }

**Respuesta**

Para obtener una solicitud correcta: "201 Creado".

De forma predeterminada, el cuerpo de la respuesta contendrá el JSON de la definición del índice que se creó. Si se establece el encabezado de la solicitud `Prefer` en `return=minimal`, el cuerpo de respuesta quedará vacío y el código de estado correcto será "204 Sin contenido" en lugar de "201 creado". Esto es cierto independientemente de si se utiliza PUT o POST para crear el índice.

**Comentarios**

Actualmente, hay compatibilidad limitada para las actualizaciones del esquema de índice. Actualmente no se admiten las actualizaciones del esquema que requieran volver a indexar, como el cambio de tipos de campo. Aunque los campos existentes no se pueden modificar ni eliminar, los campos nuevos pueden agregarse a un índice existente en cualquier momento. Al agregar un nuevo campo, todos los documentos existentes del índice tendrán un valor null para ese campo automáticamente. No se consumirá espacio de almacenamiento adicional hasta que se agreguen nuevos documentos al índice.

<a name="Suggesters"></a>
## Proveedores de sugerencias

La característica de sugerencias de Búsqueda de Azure es una capacidad de consulta de escritura anticipada o autocompletar, que proporciona una lista de posibles términos de búsqueda en respuesta a las entradas de cadenas parciales especificadas en un cuadro de búsqueda. Probablemente haya observado sugerencias de consulta al utilizar los motores de búsqueda web comerciales: si se escribe ".NET" en Bing, se genera una lista de términos para ".NET 4.5", ".NET Framework 3.5", y así sucesivamente. Cuando se utiliza la API de REST del servicio Búsqueda, la implementación de sugerencias en una aplicación de Búsqueda de Azure personalizada requiere lo siguiente:

- Habilite las sugerencias agregando una construcción de **proveedor de sugerencias** en el índice, lo que proporciona el nombre, el modo de búsqueda y una lista de campos para los que se requiere la escritura anticipada. Por ejemplo, si especifica "nombreCiudad" como un campo de origen, al escribir la cadena de búsqueda parcial de "Sea", dará como resultado "Seattle", "Seaside" y "Seatac" (las tres son nombres de ciudades reales) ofrecidos como sugerencias de consulta para el usuario.

- Invoque sugerencias llamando a la [API de sugerencias](#Suggestions) en el código de aplicación. Normalmente las cadenas de búsqueda parcial se envían al servicio mientras el usuario está escribiendo una consulta de búsqueda y la API devuelve un conjunto de frases sugeridas.

Este artículo explica cómo configurar un **proveedor de sugerencias**. También debe revisar la [API de sugerencias](#Suggestions) para obtener más información sobre cómo se utiliza un proveedor de sugerencias.

**Uso**

`Suggesters` se crean en el índice y funcionan mejor cuando se usan para sugerir documentos específicos en lugar de términos o frases flexibles. Los campos de mejores candidatos son títulos, nombres y demás frases relativamente cortas que pueden identificar un elemento. Los campos repetitivos son menos efectivos, por ejemplo, las categorías y etiquetas, o campos muy largos como campos de descripciones o comentarios.

Como parte de la definición del índice puede agregar un único proveedor de sugerencias a la colección `suggesters`. Las propiedades que definen a un proveedor de sugerencias se incluyen las siguientes:

- `name`: el nombre del proveedor de sugerencias. Use el nombre del proveedor de sugerencias para llamar a la API de `suggest`.
- `searchMode`: la estrategia que se usa para buscar las frases candidatas. El único modo que se admite actualmente es `analyzingInfixMatching`, que establece una correspondencia flexible de frases al principio o en medio de las oraciones.
- `sourceFields`: lista de uno o más campos que son el origen del contenido para obtener sugerencias. Solo los campos de tipo `Edm.String` y `Collection(Edm.String)` pueden ser orígenes para obtener sugerencias. Solo se pueden usar los campos que no tienen un analizador de lenguaje personalizado establecido.

**Ejemplo de proveedor de sugerencias**

Un proveedor de sugerencias forma parte del índice. Solo un proveedor de sugerencias puede existir en la colección `suggesters` en la versión actual, junto con la colección de campos y `scoringProfiles`.

		{
		  "name": "hotels",
		  "fields": [
		     . . .
		   ],
		  "suggesters": [
		    {
		    "name": "sg",
		    "searchMode": "analyzingInfixMatching",
		    "sourceFields: ["hotelName", "category"]
		    }
		  ],
		  "scoringProfiles": [
		     . . .
		  ]
		}

> [AZURE.NOTE]  Si ha usado la versión de vista previa pública de Búsqueda de Azure, `suggesters` sustituirá a una propiedad booleana anterior (`"suggestions": false`) que solo admitía sugerencias de prefijos para cadenas cortas (3-25 caracteres). Su reemplazo, `suggesters`, admite la detección de coincidencias de infijos que encuentra términos coincidentes al principio o en medio del contenido del campo, con una mejor tolerancia a errores en las cadenas de búsqueda. A partir de la versión disponible con carácter general, esta es ahora la única implementación de la API de sugerencias. La propiedad `suggestions` anterior que se introdujo en `api-version=2014-07-31-Preview` continúa funcionando en esa versión, pero no está operativa en la versión `2015-02-28` o posteriores de Búsqueda de Azure.

<a name="UpdateIndex"></a>
## Actualizar índice

Puede actualizar un índice existente en Búsqueda de Azure mediante una solicitud HTTP PUT. Las actualizaciones pueden incluir agregar nuevos campos al esquema existente, modificar las opciones de CORS y modificar perfiles de puntuación. Consulte [Agregar perfiles de puntuación](https://msdn.microsoft.com/library/azure/dn798928.aspx) para obtener más información. Especifique el nombre del índice que se va a actualizar en el URI de solicitud:

    PUT https://[search service url]/indexes/[index name]?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

**Importante:** la compatibilidad con las actualizaciones del esquema de índice se limita a las operaciones que no requieren volver a generar el índice de búsqueda. Actualmente no se admiten las actualizaciones del esquema que requieran volver a indexar, como el cambio de tipos de campo. Pueden agregarse nuevos campos en cualquier momento, aunque no se pueden modificar ni eliminar los campos existentes. Lo mismo se aplica a `suggesters`. Es posible agregar nuevos campos a un proveedor de sugerencias en el momento en que se agregan los campos, pero no se puede quitar campos de `suggesters` y no se pueden agregar campos existentes a `suggesters`.

Al agregar un nuevo campo a un índice, todos los documentos existentes en el índice tendrá un valor null para ese campo automáticamente. No se consumirá espacio de almacenamiento adicional hasta que se agreguen nuevos documentos al índice.

**Solicitud**

HTTPS es necesario para todas las solicitudes de servicio. La solicitud **Actualizar índice** se construye utilizando HTTP PUT. Con PUT, el nombre del índice es parte de la dirección URL. Si el índice no existe, se crea. Si ya existe el índice, se actualiza a la nueva definición.

El nombre del índice debe estar en minúsculas, comenzar por una letra o un número, no tener ninguna barra o punto y tener menos de 128 caracteres. Después de iniciar el nombre del índice por una letra o un número, el resto del nombre puede incluir cualquier letra, número y guiones, siempre que los guiones no aparezcan de manera consecutiva.

`api-version=[string]` (obligatorio). La versión de vista previa es `api-version=2015-02-28-Preview`. Consulte [Versiones del servicio de búsqueda](http://msdn.microsoft.com/library/azure/dn864560.aspx) para obtener más información y versiones alternativas.

**Encabezados de solicitud**

En la lista siguiente se describen los encabezados de solicitud obligatorios y opcionales.

- `Content-Type`: obligatorio. Establézcalo en `application/json`
- `api-key`: obligatorio. `api-key` se usa para autenticar la solicitud en su servicio de búsqueda. Es un valor de cadena único para el servicio. La solicitud **Actualizar índice** debe incluir un encabezado `api-key` establecido en su clave de administración (en lugar de una clave de consulta).

También necesitará el nombre del servicio para construir la dirección URL de la solicitud. Puede obtener el nombre de servicio y `api-key` desde el panel de servicio en el Portal de Azure. Consulte [Crear un servicio de Búsqueda de Azure en el portal](search-create-service-portal.md) para obtener ayuda sobre la navegación en páginas.

**Sintaxis del cuerpo de la solicitud**

Al actualizar un índice existente, el cuerpo debe incluir la definición de esquema original, además de los nuevos campos que se van a agregar, así como los perfiles de puntuación modificados, los proveedores de sugerencias y las opciones de CORS, si existen. Si no va a modificar los perfiles de puntuación y las opciones de CORS, deberá incluir los originales de cuando se creó el índice. En general, el mejor patrón que se puede utilizar para las actualizaciones consiste en recuperar la definición del índice con un comando GET, modificarlo y luego actualizarlo con PUT.

A continuación se reproduce la sintaxis del esquema usada para crear un índice por motivos de comodidad. Consulte [Crear índice](#CreateIndex) para obtener más detalles.

    {
      "name": (optional) "name_of_index",
      "fields": [
        {
          "name": "name_of_field",
          "type": "Edm.String | Collection(Edm.String) | Edm.Int32 | Edm.Int64 | Edm.Double | Edm.Boolean | Edm.DateTimeOffset | Edm.GeographyPoint",
          "searchable": true (default where applicable) | false (only Edm.String and Collection(Edm.String) fields can be searchable),
          "filterable": true (default) | false,
          "sortable": true (default where applicable) | false (Collection(Edm.String) fields cannot be sortable),
          "facetable": true (default where applicable) | false (Edm.GeographyPoint fields cannot be facetable),
          "key": true | false (default, only Edm.String fields can be keys),
          "retrievable": true (default) | false, 
		  "analyzer": "name of the analyzer used for search and indexing", (only if 'searchAnalyzer' and 'indexAnalyzer' are not set)
          "searchAnalyzer": "name of the search analyzer", (only if 'indexAnalyzer' is set and 'analyzer' is not set)
          "indexAnalyzer": "name of the indexing analyzer" (only if 'searchAnalyzer' is set and 'analyzer' is not set)
        }
      ],
      "suggesters": [
        {
          "name": "name of suggester",
          "searchMode": "analyzingInfixMatching" (other modes may be added in the future),
          "sourceFields": ["field1", "field2", ...]
        }
      ],
      "scoringProfiles": [
        {
          "name": "name of scoring profile",
          "text": (optional, only applies to searchable fields) {
            "weights": {
              "searchable_field_name": relative_weight_value (positive numbers),
              ...
            }
          },
          "functions": (optional) [
            {
              "type": "magnitude | freshness | distance | tag",
              "boost": # (positive number used as multiplier for raw score != 1),
              "fieldName": "...",
              "interpolation": "constant | linear (default) | quadratic | logarithmic",
              "magnitude": {
                "boostingRangeStart": #,
                "boostingRangeEnd": #,
                "constantBoostBeyondRange": true | false (default)
              },
              "freshness": {
                "boostingDuration": "..." (value representing timespan leading to now over which boosting occurs)
              },
              "distance": {
                "referencePointParameter": "...", (parameter to be passed in queries to use as reference location, see "scoringParameter" for syntax details)
                "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)
              },
			  "tag": {
				"tagsParameter": "..." (parameter to be passed in queries to specify list of tags to compare against target field, see "scoringParameter" for syntax details)
              }
            }
          ],
          "functionAggregation": (optional, applies only when functions are specified)
            "sum (default) | average | minimum | maximum | firstMatching"
        }
      ],
      "defaultScoringProfile": (optional) "...",
      "corsOptions": (optional) {
        "allowedOrigins": ["*"] | ["origin_1", "origin_2", ...],
        "maxAgeInSeconds": (optional) max_age_in_seconds (non-negative integer)
      }
    }


**Respuesta**

Para obtener una solicitud correcta: "204 Sin contenido".

De forma predeterminada, el cuerpo de respuesta estará vacío. No obstante, si el encabezado de la solicitud `Prefer` está establecido en `return=representation`, el cuerpo de la respuesta contendrá el JSON de la definición del índice que se actualizó. En este caso, el código de estado correcto será "200 Correcto".

<a name="ListIndexes"></a>
## Índices de la lista

La operación **Índices de la lista** devuelve una lista de los índices que se encuentran actualmente en el servicio de Búsqueda de Azure.

    GET https://[service name].search.windows.net/indexes?api-version=[api-version]
    api-key: [admin key]

**Solicitud**

HTTPS es necesario para todas las solicitudes de servicio. La solicitud **Índices de la lista** puede crearse mediante el método GET.

`api-version=[string]` (obligatorio). La versión de vista previa es `api-version=2015-02-28-Preview`. Consulte [Versiones del servicio de búsqueda](http://msdn.microsoft.com/library/azure/dn864560.aspx) para obtener más información y versiones alternativas.

**Encabezados de solicitud**

En la lista siguiente se describen los encabezados de solicitud obligatorios y opcionales.

- `api-key`: obligatorio. `api-key` se usa para autenticar la solicitud en su servicio de búsqueda. Es un valor de cadena único para el servicio. La solicitud **Índices de la lista** debe incluir un `api-key` establecido en una clave de administración (en lugar de una clave de consulta).

También necesitará el nombre del servicio para construir la dirección URL de la solicitud. Puede obtener el nombre de servicio y `api-key` desde el panel de servicio en el Portal de Azure. Consulte [Crear un servicio de Búsqueda de Azure en el portal](search-create-service-portal.md) para obtener ayuda sobre la navegación en páginas.

**Cuerpo de la solicitud**

Ninguno.

**Respuesta**

Código de estado: al obtener una respuesta correcta, se visualiza 200 Correcto.

A continuación se proporciona un cuerpo de respuesta de ejemplo:

    {
      "value": [
        {
          "name": "Books",
          "fields": [
            {"name": "ISBN", ...},
            ...
          ]
        },
        {
          "name": "Games",
          ...
        },
        ...
      ]
    }

Tenga en cuenta que puede filtrar la respuesta a solo las propiedades que le interesan. Por ejemplo, si solamente desea una lista de los nombres de índices, use la opción de consulta OData `$select`:

    GET /indexes?api-version=2015-02-28-Preview&$select=name

En este caso, la respuesta del ejemplo anterior podría aparecer como sigue:

    {
      "value": [
        {"name": "Books"},
        {"name": "Games"},
        ...
      ]
    }

Se trata de una técnica útil para ahorrar ancho de banda, si tiene una gran cantidad de índices en el servicio de búsqueda.

<a name="GetIndex"></a>
## Obtener índice

La operación **Obtener índice** obtiene la definición del índice de Búsqueda de Azure.

    GET https://[service name].search.windows.net/indexes/[index name]?api-version=[api-version]
    api-key: [admin key]

**Solicitud**

HTTPS es necesario para las solicitudes de servicio. La solicitud **Obtener índices** puede crearse mediante el método GET.

El [nombre de índice] del URI de la solicitud especifica qué índice se va a obtener de la colección de índices.

`api-version=[string]` (obligatorio). La versión de vista previa es `api-version=2015-02-28-Preview`. Consulte [Versiones del servicio de búsqueda](http://msdn.microsoft.com/library/azure/dn864560.aspx) para obtener más información y versiones alternativas.

**Encabezados de solicitud**

En la lista siguiente se describen los encabezados de solicitud obligatorios y opcionales.

- `api-key`: `api-key` se usa para autenticar la solicitud en su servicio de búsqueda. Es un valor de cadena único para el servicio. La solicitud **Obtener índice** debe incluir un `api-key` establecido en una clave de administración (en lugar de una clave de consulta).

También necesitará el nombre del servicio para construir la dirección URL de la solicitud. Puede obtener el nombre de servicio y `api-key` desde el panel de servicio en el Portal de Azure. Consulte [Crear un servicio de Búsqueda de Azure en el portal](search-create-service-portal.md) para obtener ayuda sobre la navegación en páginas.

**Cuerpo de la solicitud**

Ninguno.

**Respuesta**

Código de estado: al obtener una respuesta correcta, se visualiza 200 Correcto.

Consulte el JSON de ejemplo en [creación y actualización de un índice](#CreateUpdateIndexExample) para obtener un ejemplo de la carga de respuesta.

<a name="DeleteIndex"></a>
## Eliminar índice

La operación **Eliminar índice** quita un índice y los documentos asociados del servicio de Búsqueda de Azure. Puede obtener el nombre del índice del panel de servicio en el Portal de Azure o de la API. Consulte [Índices de la lista](#ListIndexes) para obtener más información.

    DELETE https://[service name].search.windows.net/indexes/[index name]?api-version=[api-version]
    api-key: [admin key]

**Solicitud**

HTTPS es necesario para las solicitudes de servicio. La solicitud **Eliminar índice** puede crearse mediante el método DELETE.

El [nombre de índice] del URI de la solicitud especifica qué índice se va a eliminar de la colección de índices.

`api-version=[string]` (obligatorio). La versión de vista previa es `api-version=2015-02-28-Preview`. Consulte [Versiones del servicio de búsqueda](http://msdn.microsoft.com/library/azure/dn864560.aspx) para obtener más información y versiones alternativas.

**Encabezados de solicitud**

En la lista siguiente se describen los encabezados de solicitud obligatorios y opcionales.

- `api-key`: obligatorio. `api-key` se usa para autenticar la solicitud en su servicio de búsqueda. Es un valor de cadena, único en su URL de servicio. La solicitud **Eliminar índice** debe incluir un encabezado `api-key` establecido en su clave de administración (en lugar de una clave de consulta).

También necesitará el nombre del servicio para construir la dirección URL de la solicitud. Puede obtener el nombre de servicio y `api-key` desde el panel de servicio en el Portal de Azure. Consulte [Crear un servicio de Búsqueda de Azure en el portal](search-create-service-portal.md) para obtener ayuda sobre la navegación en páginas.

**Cuerpo de la solicitud**

Ninguno.

**Respuesta**

Código de estado: al obtener una respuesta correcta, se visualiza 204 Sin contenido.

<a name="GetIndexStats"></a>
## Obtención de estadísticas de índice

La operación **Obtener estadísticas de índice** obtiene de Búsqueda de Azure un recuento de documentos para el índice actual, más el uso del almacenamiento.

	GET https://[service name].search.windows.net/indexes/[index name]/stats?api-version=[api-version]
    api-key: [admin key]

**Solicitud**

HTTPS es necesario para todas las solicitudes de servicio. La solicitud **Obtener estadísticas de índices** puede crearse mediante el método GET.

El [nombre de índice] del URI de la solicitud indica al servicio que devuelva estadísticas de índice para el índice especificado.

`api-version=[string]` (obligatorio). La versión de vista previa es `api-version=2015-02-28-Preview`. Consulte [Versiones del servicio de búsqueda](http://msdn.microsoft.com/library/azure/dn864560.aspx) para obtener más información y versiones alternativas.


**Encabezados de solicitud**

En la lista siguiente se describen los encabezados de solicitud obligatorios y opcionales.

- `api-key`: `api-key` se usa para autenticar la solicitud en su servicio de búsqueda. Es un valor de cadena único para el servicio. La solicitud **Obtener estadísticas de índice** debe incluir un `api-key` establecido en una clave de administración (en lugar de una clave de consulta).

También necesitará el nombre del servicio para construir la dirección URL de la solicitud. Puede obtener el nombre de servicio y `api-key` desde el panel de servicio en el Portal de Azure. Consulte [Crear un servicio de Búsqueda de Azure en el portal](search-create-service-portal.md) para obtener ayuda sobre la navegación en páginas.

**Cuerpo de la solicitud**

Ninguno.

**Respuesta**

Código de estado: al obtener una respuesta correcta, se visualiza 200 Correcto.

El cuerpo de la respuesta está en el formato siguiente:

    {
      "documentCount": number,
	  "storageSize": number (size of the index in bytes)
    }

________________________________________
<a name="DocOps"></a>
## Operaciones del documento

En Búsqueda de Azure, se almacena un índice en la nube y se rellena con documentos JSON que se cargan en el servicio. Todos los documentos que se cargan comprenden el corpus de los datos de búsqueda. Los documentos contienen campos, algunos de los cuales se acortan en términos de búsqueda cuando se cargan. El segmento de URL `/docs` de la API de Búsqueda de Azure representa la colección de documentos en un índice. Todas las operaciones realizadas en la colección, como cargar, combinar, eliminar o consultar documentos se producen en el contexto de un índice único, por lo que las direcciones URL de estas operaciones siempre se iniciarán mediante `/indexes/[index name]/docs` para un nombre de índice especificado.

El código de aplicación debe generar documentos JSON para cargarlos en la búsqueda de Azure o se puede usar un [indizador](https://msdn.microsoft.com/library/dn946891.aspx) para cargar documentos si el origen de datos es la Base de datos SQL de Azure o DocumentDB. Normalmente, los índices se rellenan desde un único conjunto de datos que suministre.

Debe planear disponer de un documento para cada elemento que desee buscar. Una aplicación de alquiler de películas puede disponer de un documento por película, una aplicación de escaparate podría tener un documento por SKU, una aplicación de software con fines pedagógicos en línea podría tener un documento por curso, una empresa de investigación podría tener un documento para cada documento académico de su repositorio, y así sucesivamente.

Los documentos constan de uno o varios campos. Los campos pueden contener texto acortado por Búsqueda de Azure a términos de búsqueda, así como los valores no acortados o que no sean de texto que pueden utilizarse en filtros o perfiles de puntuación. Los nombres, tipos de datos y funciones de búsqueda admitidos para cada campo están determinados por el esquema de índice. Uno de los campos de cada esquema de índice debe designarse como identificador, y cada documento debe tener un valor para el campo de identificador que identifica ese documento de manera única en el índice. Todos los demás campos de documento son opcionales y se establecerán de manera predeterminada en un valor null si no se especifican. Tenga en cuenta que los valores null no ocupan espacio en el índice de búsqueda.

Para poder cargar documentos, debe haber creado el índice en el servicio. Consulte [Crear índice](#CreateIndex) para obtener más información acerca de este primer paso.

<a name="AddOrUpdateDocuments"></a>
## Agregar, actualizar o eliminar documentos

Puede cargar, combinar, combinar o cargar o eliminar documentos en un índice especificado mediante HTTP POST. Para números elevados de actualizaciones, se recomienda efectuar el procesamiento por lotes de documentos (hasta 1.000 documentos por lote o aproximadamente 16 MB por lote).

    POST https://[service name].search.windows.net/indexes/[index name]/docs/index?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

**Solicitud**

HTTPS es necesario para todas las solicitudes de servicio. Puede cargar, combinar, combinar o cargar o eliminar documentos en un índice especificado mediante HTTP POST.

El URI de solicitud incluye eI [nombre de índice], y especifica en qué índice publicar los documentos. Solo se pueden publicar documentos en un índice de cada vez.

`api-version=[string]` (obligatorio). La versión de vista previa es `api-version=2015-02-28-Preview`. Consulte [Versiones del servicio de búsqueda](http://msdn.microsoft.com/library/azure/dn864560.aspx) para obtener más información y versiones alternativas.

**Encabezados de solicitud**

En la lista siguiente se describen los encabezados de solicitud obligatorios y opcionales.

- `Content-Type`: obligatorio. Establézcalo en `application/json`
- `api-key`: obligatorio. `api-key` se usa para autenticar la solicitud en su servicio de búsqueda. Es un valor de cadena único para el servicio. La solicitud **Agregar documentos** debe incluir un encabezado `api-key` establecido en su clave de administración (en lugar de una clave de consulta).

También necesitará el nombre del servicio para construir la dirección URL de la solicitud. Puede obtener el nombre de servicio y `api-key` desde el panel de servicio en el Portal de Azure. Consulte [Crear un servicio de Búsqueda de Azure en el portal](search-create-service-portal.md) para obtener ayuda sobre la navegación en páginas.

**Cuerpo de la solicitud**

El cuerpo de la solicitud contiene uno o más documentos para indexar. Los documentos se identifican mediante una clave única. Cada documento está asociado a una acción: carga, combinación, combinación o carga o eliminación. Las solicitudes de carga deben incluir los datos del documento como un conjunto de pares de clave/valor.

    {
      "value": [
        {
          "@search.action": "upload (default) | merge | mergeOrUpload | delete",
          "key_field_name": "unique_key_of_document", (key/value pair for key field from index schema)
          "field_name": field_value (key/value pairs matching index schema)
            ...
        },
        ...
      ]
    }

> [AZURE.NOTE] Las claves de documento solo pueden contener letras, números, guiones ("-"), caracteres de subrayado ("\_") y signos igual ("="). Para más información, consulte [Reglas de nomenclatura](https://msdn.microsoft.com/library/azure/dn857353.aspx).

**Acciones de documentos**

- `upload`: una acción de carga es similar a un "upsert" donde se insertará el documento si es nuevo y se actualizará/reemplazará si existe. Tenga en cuenta que se reemplazarán todos los campos en el caso de la actualización.
- `merge`: la combinación actualiza un documento existente con los campos especificados. Si el documento no existe, se producirá un error en la combinación. Cualquier campo que se especifica en una combinación reemplazará al campo existente en el documento. Aquí se incluyen los campos de tipo `Collection(Edm.String)`. Por ejemplo, si el documento contiene un campo "etiquetas" con el valor `["budget"]` y ejecuta una combinación con el valor `["economy", "pool"]` para "etiquetas", el valor final del campo "etiquetas" será `["economy", "pool"]`. **No** será `["budget", "economy", "pool"]`.
- `mergeOrUpload`: se comporta como `merge` si ya existe un documento con la clave especificada en el índice. Si el documento no existe, se comporta como `upload` con un nuevo documento.
- `delete`: la eliminación quita el documento especificado del índice. Tenga en cuenta que puede especificar solo el valor del campo de clave en una operación `delete`. Si se intenta especificar otros campos se producirá un error HTTP 400. Si desea quitar un campo individual de un documento, use `merge` en su lugar y simplemente establezca el campo explícitamente en `null`.

**Respuesta**

Código de estado: se obtendrá 200 Correcto con una respuesta correcta, lo que significa que todos los elementos se han indexado correctamente (como se indica en el campo "status" establecido en true para todos los elementos):

    {
      "value": [
        {
          "key": "unique_key_of_document",
          "status": true,
          "errorMessage": null
        }
      ]
    }  

Código de estado: se obtendrá 207 cuando no se haya indexado correctamente al menos un elemento (tal y como se indica en el campo "status" establecido en false para los elementos que no se hayan indexado):

    {
      "value": [
        {
          "key": "unique_key_of_document",
          "status": false,
          "errorMessage": "The search service is too busy to process this document. Please try again later."
        }
      ]
    }  

La propiedad `errorMessage` indicará el motivo del error de indexación si es posible.

**Nota**: Si el código de cliente encuentra con frecuencia una respuesta 207, una razón posible es que el sistema está bajo carga. Puede confirmar esto comprobando la propiedad `errorMessage`. En tal caso, es recomendable ***limitar solicitudes de indexación***. De lo contrario, si el tráfico de indexación de tráfico no se reduce, es posible que el sistema comience a rechazar todas las solicitudes mediante errores 503.

Código de estado: 429 indica que se ha superado la cuota del número de documentos por índice. Debe crear un nuevo índice o actualizar para obtener límites de capacidad superiores.

**Ejemplo:**

    {
      "value": [
        {
          "@search.action": "upload",
          "hotelId": "1",
          "baseRate": 199.0,
          "description": "Best hotel in town",
		  "description_fr": "Meilleur hôtel en ville",
          "hotelName": "Fancy Stay",
		  "category": "Luxury",
          "tags": ["pool", "view", "wifi", "concierge"],
          "parkingIncluded": false,
		  "smokingAllowed": false,
          "lastRenovationDate": "2010-06-27T00:00:00Z",
          "rating": 5,
          "location": { "type": "Point", "coordinates": [-122.131577, 47.678581] }
        },
        {
          "@search.action": "upload",
          "hotelId": "2",
          "baseRate": 79.99,
          "description": "Cheapest hotel in town",
	      "description_fr": "Hôtel le moins cher en ville",
          "hotelName": "Roach Motel",
		  "category": "Budget",
          "tags": ["motel", "budget"],
          "parkingIncluded": true,
		  "smokingAllowed": true,
          "lastRenovationDate": "1982-04-28T00:00:00Z",
          "rating": 1,
          "location": { "type": "Point", "coordinates": [-122.131577, 49.678581] }
        },
        {
          "@search.action": "merge",
          "hotelId": "3",
          "baseRate": 279.99,
          "description": "Surprisingly expensive",
          "lastRenovationDate": null
        },
        {
          "@search.action": "delete",
          "hotelId": "4"
        }
      ]
    }
________________________________________
<a name="SearchDocs"></a>
## Buscar en documentos

La operación de **búsqueda** se emite como una solicitud GET o POST y especifica parámetros que ofrecen los criterios necesarios para seleccionar los documentos coincidentes.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?[query parameters]
    api-key: [admin or query key]

    POST https://[service name].search.windows.net/indexes/[index name]/docs/search?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin or query key]

**Cuándo usar POST en lugar de GET**

Cuando use HTTP GET para llamar a la API de **búsqueda**, deberá tener en cuenta que la longitud de la URL de la solicitud no puede superar los 8 KB. Esto suele ser suficiente para la mayoría de las aplicaciones. Sin embargo, algunas aplicaciones generan consultas muy extensas o expresiones de filtro OData. Para estas aplicaciones, el uso de HTTP POST es una opción mejor porque permite filtros y consultas mayores que GET. Con POST, el número de términos o cláusulas en una consulta es el factor limitador, no el tamaño de la consulta básica, ya que el límite de tamaño de la solicitud POST es de 16 MB aproximadamente.

> [AZURE.NOTE] Aunque el límite de tamaño de la solicitud POST es muy grande, las consultas y las expresiones de filtro de búsqueda no pueden ser arbitrariamente complejas. Consulte [Sintaxis de consulta de Lucene](https://msdn.microsoft.com/library/mt589323.aspx) y [Sintaxis de expresiones de OData](https://msdn.microsoft.com/library/dn798921.aspx) para más información sobre las limitaciones de complejidad de consultas y filtros de búsqueda. **Solicitud**

HTTPS es necesario para las solicitudes de servicio. La solicitud **Búsqueda** puede crearse mediante los método GET o POST.

El URI de solicitud especifica qué índice se va a consultar para todos los documentos que coinciden con los parámetros de consulta. Los parámetros se especifican en la cadena de consulta en el caso de solicitudes GET y en el cuerpo de la solicitud en el caso de las solicitudes POST.

Como práctica recomendada al crear solicitudes GET, recuerde [codificar con URL](https://msdn.microsoft.com/library/system.uri.escapedatastring.aspx) los parámetros de consulta específicos al llamar a la API de REST directamente. Para las operaciones de **Búsqueda**, esto incluye:

- `$filter`
- `facet`
- `highlightPreTag`
- `highlightPostTag`
- `search`
- `moreLikeThis`

Solo se recomienda la codificación de direcciones URL en los parámetros de consulta anterior. Si codifica con URL involuntariamente la cadena de consulta completa (todo lo situado después de la?), las solicitudes se dividirán.

Además, la codificación con URL solo es necesaria cuando se llama directamente a la API de REST directamente con GET. No se requiere ninguna codificación de URL cuando se llama a la **Búsqueda** mediante POST o cuando se usa la [biblioteca de cliente .NET](https://msdn.microsoft.com/library/dn951165.aspx) que controla la codificación de direcciones URL en su lugar.

<a name="SearchQueryParameters"></a> **Parámetros de consulta**

La **búsqueda** acepta varios parámetros que ofrecen criterios de consulta y que también especifican el comportamiento de búsqueda. Ofrece estos parámetros en la cadena de consulta URL al llamar a la **búsqueda** mediante GET y como propiedades JSON en el cuerpo de solicitud al llamar a la **búsqueda** mediante POST. La sintaxis de algunos parámetros es algo diferente entre GET y POST. Estas diferencias se indican como aplicables a continuación:

`search=[string]` (opcional): el texto que se debe buscar. Se busca en los campos `searchable` de forma predeterminada a menos que se especifique `searchFields`. Al realizar búsquedas en campos `searchable`, se limita el propio texto de la búsqueda, por lo que los distintos términos pueden separarse mediante un espacio en blanco (por ejemplo: `search=hello world`). Para encontrar un término, use `*` (esto puede ser útil para las consultas de filtro booleano). Omitir este parámetro tiene el mismo efecto que establecerlo en `*`. Para obtener información específica sobre la sintaxis de búsqueda, consulte [Sintaxis de consulta simple](https://msdn.microsoft.com/library/dn798920.aspx).

  - **Nota**: Los resultados a veces pueden ser sorprendentes al consultar sobre campos `searchable`. El tokenizer incluye una lógica para controlar los casos comunes en texto en inglés como apóstrofos, comas en números, etc. Por ejemplo, `search=123,456` hallará el único término 123,456 en lugar de los términos individuales 123 y 456, ya que en los números grandes en inglés se usan comas como separadores de miles. Por este motivo, se recomienda usar espacios en blanco en lugar de signos de puntuación para separar los términos en el parámetro `search`.

`searchMode=any|all` (opcional, tiene como valor predeterminado `any`): si alguno o todos los términos de búsqueda deben coincidir con el fin de contar el documento como una coincidencia.

`searchFields=[string]` (opcional): la lista separada por comas de nombres de campo para buscar el texto especificado. Los campos de destino deben estar marcados como `searchable`.

`queryType=simple|full` (opcional, tiene como valor predeterminado `simple`): cuando se establece en "simple", el texto de búsqueda se interpreta mediante un lenguaje de consulta simple que permite símbolos como +, * y "". Las consultas se evalúan en todos los campos de búsqueda (o campos indicados en `searchFields`) en cada documento de manera predeterminada. Cuando se establece el tipo de consulta en `full`, el texto de búsqueda se interpreta mediante el lenguaje de consulta de Lucene que permite realizar búsquedas específicas de campos y ponderadas. Para obtener información específica sobre las sintaxis de búsqueda, consulte [Sintaxis de consulta simple](https://msdn.microsoft.com/library/dn798920.aspx) y [Sintaxis de consulta de Lucene](https://msdn.microsoft.com/library/mt589323.aspx).
 
> [AZURE.NOTE] No está admitido el intervalo de búsqueda en el lenguaje de consulta de Lucene, es preferible usar $filter que ofrece una funcionalidad similar.

`moreLikeThis=[key]` (opcional) **Importante:** esta función solo está disponible en `2015-02-28-Preview`. Esta opción no se puede usar en una consulta que contiene el parámetro de búsqueda de texto, `search=[string]`. El parámetro `moreLikeThis` busca documentos que son similares al documento especificado por la clave del documento. Cuando se realiza una solicitud de búsqueda con `moreLikeThis`, se genera una lista de términos de búsqueda en función de la frecuencia y la rareza de los términos en el documento de origen. Estos términos se usan a continuación para realizar la solicitud. De forma predeterminada, se considera el contenido de todos los campos `searchable` a menos que se use `searchFields` para restringir los campos que se buscan.

`$skip=#` (opcional): el número de resultados de búsqueda que se omiten; no puede ser superior a 100.000. Si necesita examinar documentos en secuencia pero no puede usar `$skip` debido a esta limitación, utilice `$orderby` en una clave totalmente ordenada y `$filter` con una consulta por rango en su lugar.

> [AZURE.NOTE] Al llamar a la **Búsqueda** mediante POST, este parámetro se denomina `skip` en lugar de `$skip`.

`$top=#` (opcional): número de resultados de búsqueda para recuperar. Se puede usar junto con `$skip` para implementar la paginación del lado del cliente de los resultados de la búsqueda.

> [AZURE.NOTE] Al llamar a la **búsqueda** mediante POST, este parámetro se denomina `top` en lugar de `$top`.

`$count=true|false` (opcional, tiene como valor predeterminado `false`): especifica si se va a obtener el número total de resultados. Este es el recuento de todos los documentos que coinciden con los parámetros `search` y `$filter`, omitiendo `$top` y `$skip`. Establecer este valor en `true` puede afectar al rendimiento. Tenga en cuenta que el número devuelto será una aproximación.

> [AZURE.NOTE] Al llamar a la **búsqueda** mediante POST, este parámetro se denomina `count` en lugar de `$count`.

`$orderby=[string]` (opcional): lista de expresiones separadas por comas por la que ordenar los resultados. Cada expresión puede ser un nombre de campo o una llamada a la función `geo.distance()`. Cada expresión puede ir seguida de `asc` para indicar el orden ascendente y de `desc` para indicar el orden descendente. El valor predeterminado es ascendente. Los empates se resolverán por la puntuación de coincidencia de los documentos. Si no se especifica ningún `$orderby`, el orden predeterminado será descendente por puntuación de coincidencia del documento. Hay un límite de 32 cláusulas para `$orderby`.

> [AZURE.NOTE] Al llamar a la **Búsqueda** mediante POST, este parámetro se denomina `orderby` en lugar de `$orderby`.

`$select=[string]` (opcional): lista de campos separados por comas para recuperar. Si no se especifica nada, se incluirán todos los campos marcados como recuperables en el esquema. También se pueden solicitar explícitamente todos los campos estableciendo este parámetro en `*`.

> [AZURE.NOTE] Al llamar a la **Búsqueda** mediante POST, este parámetro se denomina `select` en lugar de `$select`.

`facet=[string]` (cero o más): un campo por el que establecer facetas. Es posible que la cadena contenga parámetros para personalizar la faceta expresada como pares `name:value` separados por comas. Los parámetros válidos son:

- `count` (número máximo de términos de faceta; el valor predeterminado es 10). No hay ningún máximo, pero los valores más altos incurren en una penalización de rendimiento correspondiente, especialmente si el campo con facetas contiene un gran número de términos únicos.
  - Por ejemplo: `facet=category,count:5` obtiene las cinco categorías principales en los resultados de la faceta.  
  - **Nota**: Si el parámetro `count` es menor que el número de términos únicos, es posible que los resultados no sean precisos. Esto es debido a la manera en que se distribuyen las consultas de facetas entre las particiones. Aumentar `count` generalmente aumenta la precisión de los recuentos de términos, pero ello afecta al rendimiento.
- `sort` (uno de `count` para ordenar de manera *descendente* por número, `-count` para ordenar de manera *ascendente* por número, `value` para ordenar de manera *ascendente* por valor o `-value` para ordenar de manera *descendente* por valor)
  - Por ejemplo: `facet=category,count:3,sort:count` obtiene las tres categorías principales en los resultados de la faceta en orden descendente por el número de documentos con el nombre de cada ciudad. Por ejemplo, si las tres categorías principales son Presupuesto, Motel y Lujo, y Presupuesto tiene 5 resultados, Motel tiene 6 y Lujo tiene 4, a continuación, los depósitos se colocarán en el orden siguiente: Motel, Presupuesto, Lujo.
  - Por ejemplo: `facet=rating,sort:-value` genera depósitos para todas las clasificaciones posibles en orden descendente por valor. Por ejemplo, si las clasificaciones son de 1 a 5, los depósitos se ordenarán como 5, 4, 3, 2, 1 independientemente de cuántos documentos coincidan con cada clasificación.
- `values` (valores numéricos delimitados por canalización o `Edm.DateTimeOffset` que especifican un conjunto dinámico de valores de entrada de faceta)
  - Por ejemplo: `facet=baseRate,values:10|20` genera tres depósitos: uno para la tarifa base 0 hasta, pero sin incluir la tarifa 10, uno para 10 hasta pero sin incluir 20 y uno para 20 o superiores.
  - Por ejemplo: `facet=lastRenovationDate,values:2010-02-01T00:00:00Z` genera dos depósitos: uno para hoteles reformados antes de febrero de 2010 y otro para hoteles reformados desde el 1 de febrero de 2010 en adelante.
- `interval` (intervalo de número entero mayor que 0 para números, o `minute`, `hour`, `day`, `week`, `month`, `quarter`, `year` para los valores de fecha y hora)
  - Por ejemplo: `facet=baseRate,interval:100` genera depósitos basados en intervalos de tarifas base de tamaño de 100. Por ejemplo, si las tarifas base se encuentran entre 60 y 600 dólares, habrá depósitos para 0-100, 100-200, 300 200, 300-400, 400-500 y 500-600.
  - Por ejemplo: `facet=lastRenovationDate,interval:year` genera un depósito para cada año en que se han reformado los hoteles.
- `timeoffset` ([+-] hh: mm, [+-] hhmm, o [+-] hh) `timeoffset` es opcional. Solo se puede combinar con la opción `interval` y solo cuando se aplica a un campo de tipo `Edm.DateTimeOffset`. El valor especifica la diferencia horaria UTC para explicar la configuración de los límites de tiempo.
  - Por ejemplo: `facet=lastRenovationDate,interval:day,timeoffset:-01:00` usa el límite de día que comienza a la 01:00:00 UTC (medianoche en la zona horaria de destino)
- **Nota**: `count` y `sort` se pueden combinar en la misma especificación de faceta, pero no se pueden combinar con `interval` o `values`, y `interval` y `values` no se pueden combinar entre sí.
- **Nota**: Las facetas de intervalo de fecha y hora se calculan en función de la hora UTC si `timeoffset` no se ha especificado. Por ejemplo, para `facet=lastRenovationDate,interval:day`, el límite de día comienza a las 00:00:00 UTC. 

> [AZURE.NOTE] Al llamar a la **búsqueda** mediante POST, este parámetro se denomina `facets` en lugar de `facet`. Además, lo especifica como una matriz JSON de cadenas, donde cada cadena es una expresión de faceta independiente.

`$filter=[string]` (opcional): expresión de búsqueda estructurada en la sintaxis estándar de OData. Consulte [Sintaxis de expresiones de OData](#ODataExpressionSyntax) para obtener detalles sobre el subconjunto de la gramática de expresiones de OData que admite la Búsqueda de Azure.

> [AZURE.NOTE] Al llamar a la **búsqueda** mediante POST, este parámetro se denomina `filter` en lugar de `$filter`.

`highlight=[string]` (opcional): conjunto de nombres de campos delimitado por comas usado para los resaltados de referencias. Solo se pueden usar `searchable` campos para resaltar las referencias.

`highlightPreTag=[string]` (opcional, se establece de forma predeterminada en `<em>`): una etiqueta de cadena que se antepone al resaltado de referencias. Debe establecerse con `highlightPostTag`.

> [AZURE.NOTE] Al llamar a la **búsqueda** mediante GET, los caracteres reservados en la dirección URL deben estar codificados con porcentaje (por ejemplo, %23 en vez de #).

`highlightPostTag=[string]` (opcional, se establece de forma predeterminada en `</em>`): una etiqueta de cadena que se antepone al resaltado de referencias. Debe establecerse con `highlightPreTag`.

> [AZURE.NOTE] Al llamar a la **Búsqueda** mediante GET, los caracteres reservados en la dirección URL deben estar codificados con porcentaje (por ejemplo, %23 en vez de #).

`scoringProfile=[string]` (opcional): nombre de un perfil de puntuación para evaluar puntuaciones de coincidencias de documentos coincidentes con el fin de ordenar los resultados.

`scoringParameter=[string]` (cero o más): indica el valor para cada parámetro definido en una función de puntuación (por ejemplo, `referencePointParameter`) con el formato nombre: valor. Por ejemplo, si el perfil de puntuación define una función con un parámetro denominado "mylocation" la opción de cadena de consulta sería & scoringParameter = mylocation:-122.2,44.8

> [AZURE.NOTE] Al llamar a la **Búsqueda** mediante POST, este parámetro se denomina `scoringParameters` en lugar de `scoringParameter`. Además, lo especifica como una matriz JSON de cadenas, donde cada cadena es un par nombre:valor independiente.

`minimumCoverage` (opcional, el valor predeterminado es 100): un número entre 0 y 100 que indica el porcentaje del índice que debe estar cubierto por una consulta de búsqueda para que la consulta se realice correctamente. De forma predeterminada, todo el índice debe estar disponible o `Search` se devolverá el código de estado HTTP 503. Si establece `minimumCoverage` y `Search` se realiza correctamente, devolverá HTTP 200 e incluye un valor `@search.coverage` en la respuesta que indica el porcentaje del índice que se incluyó en la consulta.

> [AZURE.NOTE] Establecer este parámetro en un valor inferior a 100 puede ser útil para garantizar la disponibilidad de la búsqueda incluso para servicios con una única réplica. Sin embargo, no se garantiza que todos los documentos coincidentes existan en los resultados de búsqueda. Si la recuperación de búsqueda es más importante para la aplicación que la disponibilidad, es mejor dejar `minimumCoverage` en su valor predeterminado de 100.

`api-version=[string]` (obligatorio). La versión de vista previa es `api-version=2015-02-28-Preview`. Consulte [Versiones del servicio de búsqueda](http://msdn.microsoft.com/library/azure/dn864560.aspx) para obtener más información y versiones alternativas.

Nota: Para esta operación, `api-version` se especifica como un parámetro de consulta en la dirección URL sin tener en cuenta si se llama a la **Búsqueda** con GET o POST.

**Encabezados de solicitud**

En la lista siguiente se describen los encabezados de solicitud obligatorios y opcionales.

- `api-key`: `api-key` se usa para autenticar la solicitud en su servicio de búsqueda. Es un valor de cadena, único en su URL de servicio. La solicitud de **Búsqueda** puede especificar una clave de administración o una clave de consulta para `api-key`.

También necesitará el nombre del servicio para construir la dirección URL de la solicitud. Puede obtener el nombre de servicio y `api-key` desde el panel de servicio en el Portal de Azure. Consulte [Crear un servicio de Búsqueda de Azure en el portal](search-create-service-portal.md) para obtener ayuda sobre la navegación en páginas.

**Cuerpo de la solicitud**

Para GET: ninguno.

Para POST:

    {
      "count": true | false (default),
      "facets": [ "facet_expression_1", "facet_expression_2", ... ],
      "filter": "odata_filter_expression",
      "highlight": "highlight_field_1, highlight_field_2, ...",
      "highlightPreTag": "pre_tag",
      "highlightPostTag": "post_tag",
      "minimumCoverage": # (% of index that must be covered to declare query successful; default 100),
      "moreLikeThis": "document_key" (mutually exclusive with "search" parameter),
      "orderby": "orderby_expression",
      "scoringParameters": [ "scoring_parameter_1", "scoring_parameter_2", ... ],
      "scoringProfile": "scoring_profile_name",
      "search": "simple_query_expression",
      "searchFields": "field_name_1, field_name_2, ...",
      "searchMode": "any" (default) | "all",
      "select": "field_name_1, field_name_2, ...",
      "skip": # (default 0),
      "top": #
    }

**Continuación de las respuestas de búsqueda parciales**

A veces, Búsqueda de Azure no puede devolver todos los resultados solicitados en una sola respuesta de Búsqueda. Esto puede ocurrir por diferentes motivos; por ejemplo, cuando la consulta solicita demasiados documentos al no especificar `$top` o especificar un valor para `$top` que es demasiado grande. En tales casos, Búsqueda de Azure incluirá la anotación `@odata.nextLink` en el cuerpo de respuesta, y también `@search.nextPageParameters` si era una solicitud POST. Puede usar los valores de estas anotaciones para formular otra solicitud de Búsqueda a fin de obtener la siguiente parte de la respuesta de búsqueda. Esto se denomina ***continuación*** de la solicitud de Búsqueda original, y las anotaciones se suelen llamar ***tokens de continuación***. Consulte [el ejemplo siguiente](#SearchResponse) para obtener más información sobre la sintaxis de estas anotaciones y el lugar en que aparecen en el cuerpo de respuesta.

Los motivos por los que Búsqueda de Azure podría devolver tokens de continuación son específicos de la implementación y están sujetos a cambio. Los clientes sólidos deben estar siempre preparados para controlar los casos en que se devuelven menos documentos de lo esperado y se incluye un token de continuación para seguir recuperando documentos. Tenga en cuenta también que debe usar el mismo método HTTP como solicitud original para poder continuar. Por ejemplo, si envía una solicitud GET, las solicitudes de continuación que envíe también deben utilizar GET (y lo mismo para POST).

<a name="SearchResponse"></a> **Respuesta**

Código de estado: al obtener una respuesta correcta, se visualiza 200 Correcto.

    {
      "@odata.count": # (if $count=true was provided in the query),
      "@search.coverage": # (if minimumCoverage was provided in the query),
      "@search.facets": { (if faceting was specified in the query)
        "facet_field": [
          {
            "value": facet_entry_value (for non-range facets),
            "from": facet_entry_value (for range facets),
            "to": facet_entry_value (for range facets),
            "count": number_of_documents
          }
        ],
        ...
      },
      "@search.nextPageParameters": { (request body to fetch the next page of results if not all results could be returned in this response and Search was called with POST)
        "count": ... (value from request body if present),
        "facets": ... (value from request body if present),
        "filter": ... (value from request body if present),
        "highlight": ... (value from request body if present),
        "highlightPreTag": ... (value from request body if present),
        "highlightPostTag": ... (value from request body if present),
        "minimumCoverage": ... (value from request body if present),
        "moreLikeThis": ... (value from request body if present),
        "orderby": ... (value from request body if present),
        "scoringParameters": ... (value from request body if present),
        "scoringProfile": ... (value from request body if present),
        "search": ... (value from request body if present),
        "searchFields": ... (value from request body if present),
        "searchMode": ... (value from request body if present),
        "select": ... (value from request body if present),
        "skip": ... (page size plus value from request body if present),
        "top": ... (value from request body if present minus page size),
      },
      "value": [
        {
          "@search.score": document_score (if a text query was provided),
          "@search.highlights": {
            field_name: [ subset of text, ... ],
            ...
          },
          key_field_name: document_key,
          field_name: field_value (retrievable fields or specified projection),
          ...
        },
        ...
      ],
      "@odata.nextLink": (URL to fetch the next page of results if not all results could be returned in this response; Applies to both GET and POST)
    }

**Ejemplos:**

Puede encontrar ejemplos adicionales en la página [Sintaxis de expresiones de OData para la Búsqueda de Azure](https://msdn.microsoft.com/library/azure/dn798921.aspx).

1) Busque en el índice por fecha en orden descendente.


    GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview
    {
      "search": "*",
      "orderby": [ "lastRenovationDate desc" ]
    }

2) En una búsqueda con facetas, busque en el índice y recupere las facetas de categorías, clasificación, etiquetas, así como elementos con baseRate en intervalos específicos:


    GET /indexes/hotels/docs?search=test&facet=category&facet=rating&facet=tags&facet=baseRate,values:80|150|220&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview
    {
      "search": "test",
      "facets": [ "category", "rating", "tags", "baseRate,values:80|150|220" ]
    }

3) Utilizando un filtro, restrinja los resultados de la consulta con facetas anterior después de que el usuario haga clic en la tarifa 3 y en la categoría "Motel":


    GET /indexes/hotels/docs?search=test&facet=tags&facet=baseRate,values:80|150|220&$filter=rating eq 3 and category eq 'Motel'&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview
    {
      "search": "test",
      "facets": [ "tags", "baseRate,values:80|150|220" ],
      "filter": "rating eq 3 and category eq 'Motel'"
    }

4) En una búsqueda con facetas, establezca un límite superior en términos únicos devueltos en una consulta. El valor predeterminado es 10, pero se puede aumentar o disminuir este valor utilizando el parámetro `count` en el atributo `facet`:


    GET /indexes/hotels/docs?search=test&facet=city,count:5&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview
    {
      "search": "test",
      "facets": [ "city,count:5" ]
    }

5) Busque en el índice en campos específicos; por ejemplo, un campo específico del idioma:


    GET /indexes/hotels/docs?search=hôtel&searchFields=description_fr&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview
    {
      "search": "hôtel",
      "searchFields": [ "description_fr" ]
    }

6) Busque en el índice en varios campos. Por ejemplo, puede almacenar y consultar los campos de búsqueda en varios idiomas, todo ello en el mismo índice. Si las descripciones de inglés y francés coexisten en el mismo documento, puede devolver cualquiera en los resultados de la consulta:


	GET /indexes/hotels/docs?search=hotel&searchFields=description,description_fr&api-version=2015-02-28-Preview

	POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview
    {
      "search": "hotel",
      "searchFields": [ "description", "description_fr" ]
    }

Tenga en cuenta que solo puede consultar un índice de cada vez. No cree varios índices para cada idioma a menos que planee consultar una de cada vez.

7) Paginación: obtenga la primera página de los elementos (el tamaño de la página es 10):


    GET /indexes/hotels/docs?search=*&$skip=0&$top=10&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview
    {
      "search": "*",
      "skip": 0,
      "top": 10
    }

8) Paginación: obtenga la segunda página de los elementos (el tamaño de la página es 10):


    GET /indexes/hotels/docs?search=*&$skip=10&$top=10&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview
    {
      "search": "*",
      "skip": 10,
      "top": 10
    }

9) Recupere un conjunto específico de campos:


    GET /indexes/hotels/docs?search=*&$select=hotelName,description&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview
    {
      "search": "*",
      "select": [ "hotelName", "description" ]
    }

10) Recupere documentos que coincidan con una expresión de filtro específica


    GET /indexes/hotels/docs?$filter=(baseRate ge 60 and baseRate lt 300) or hotelName eq 'Fancy Stay'&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview
    {
      "filter": "(baseRate ge 60 and baseRate lt 300) or hotelName eq 'Fancy Stay'"
    }

11) Busque en el índice y obtenga fragmentos con resaltado de referencias


    GET /indexes/hotels/docs?search=something&highlight=description&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview
    {
      "search": "something",
      "highlight": "description"
    }

12) Busque en el índice y obtenga documentos ordenados de más próximos a más alejados de una ubicación de referencia


    GET /indexes/hotels/docs?search=something&$orderby=geo.distance(location, geography'POINT(-122.12315 47.88121)')&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview
    {
      "search": "something",
      "orderby": [ "geo.distance(location, geography'POINT(-122.12315 47.88121)')" ]
    }

13) Busque en el índice suponiendo que hay un perfil de puntuaciones denominado "geográfico" con dos funciones de puntuación de distancia, una para definir un parámetro llamado "currentLocation" y otra para definir un parámetro llamado "lastLocation"


    GET /indexes/hotels/docs?search=something&scoringProfile=geo&scoringParameter=currentLocation:-122.123,44.77233&scoringParameter=lastLocation:-121.499,44.2113&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview
    {
      "search": "something",
      "scoringProfile": "geo",
      "scoringParameters": [ "currentLocation:-122.123,44.77233", "lastLocation:-121.499,44.2113" ]
    }

14) Busque documentos en el índice utilizando la [sintaxis de consulta simple](https://msdn.microsoft.com/library/dn798920.aspx). Esta consulta devuelve los hoteles, en los que los campos de búsqueda contienen los términos "comodidad" y "ubicación", pero no "motel":


    GET /indexes/hotels/docs?search=comfort +location -motel&searchMode=all&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview
    {
      "search": "comfort +location -motel",
      "searchMode": "all"
    }

Tenga en cuenta el uso de `searchMode=all` anteriormente. Incluyendo este parámetro se invalida el valor predeterminado de `searchMode=any`, lo que asegura que `-motel` significa "Y NO" en lugar de "O NO". Sin `searchMode=all`, obtendrá "O NO", que expandirá en lugar de restringir los resultados de la búsqueda, lo cual puede resultar contradictorio para algunos usuarios.

15) Busque documentos en el índice usando la [sintaxis de consulta de Lucene](https://msdn.microsoft.com/library/mt589323.aspx). Esta consulta devuelve los hoteles en los que el campo de categoría contiene el término "budget" y todos los campos de búsqueda que incluyen la expresión "recently renovated". Los documentos que contienen la expresión "recently renovated" tienen una clasificación más alta como resultado del valor de aumento del término (3)

    GET /indexes/hotels/docs?search=category:budget AND "recently renovated"^3&searchMode=all&api-version=2015-02-28-Preview&querytype=full

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview
    {
      "search": "category:budget AND "recently renovated"^3",
      "queryType": "full",
      "searchMode": "all"
    }

<a name="LookupAPI"></a>
## Buscar documento

La operación **Buscar documento** permite recuperar un documento de Búsqueda de Azure. Esto resulta útil cuando un usuario hace clic en un resultado de búsqueda específico y desea buscar detalles específicos acerca de ese documento.

    GET https://[service name].search.windows.net/indexes/[index name]/docs/[key]?[query parameters]
    api-key: [admin or query key]

**Solicitud**

HTTPS es necesario para las solicitudes de servicio. La solicitud **Buscar documento** se puede generar del modo indicado a continuación.

    GET /indexes/[index name]/docs/key?[query parameters]

También puede usar la sintaxis de OData tradicional para la búsqueda de claves:

    GET /indexes('[index name]')/docs('[key]')?[query parameters]

El URI de solicitud incluye un [nombre de índice] y una [clave], que especifica qué documento para recuperar del índice correspondiente. Solamente se puede obtener un documento de cada vez. Utilice **Búsqueda** para obtener varios documentos en una única solicitud.

**Parámetros de consulta**

`$select=[string]` (opcional): lista de campos separados por comas para recuperar. Si no se especifica nada o se establece en `*`, se incluirán en la proyección todos los campos marcados como recuperables en el esquema.

`api-version=[string]` (obligatorio). La versión de vista previa es `api-version=2015-02-28-Preview`. Consulte [Versiones del servicio de búsqueda](http://msdn.microsoft.com/library/azure/dn864560.aspx) para obtener más información y versiones alternativas.

Nota: Para esta operación, `api-version` se especifica como parámetro de consulta.

**Encabezados de solicitud**

En la lista siguiente se describen los encabezados de solicitud obligatorios y opcionales.

- `api-key`: `api-key` se usa para autenticar la solicitud en su servicio de búsqueda. Es un valor de cadena, único en su URL de servicio. La solicitud **Buscar documento** puede especificar una clave de administración o una clave de consulta para `api-key`.

También necesitará el nombre del servicio para construir la dirección URL de la solicitud. Puede obtener el nombre de servicio y `api-key` desde el panel de servicio en el Portal de Azure. Consulte [Crear un servicio de Búsqueda de Azure en el portal](search-create-service-portal.md) para obtener ayuda sobre la navegación en páginas.

**Cuerpo de la solicitud**

Ninguno.

**Respuesta**

Código de estado: al obtener una respuesta correcta, se visualiza 200 Correcto.

    {
      field_name: field_value (fields matching the default or specified projection)
    }

**Ejemplo**

Busque el documento que tiene la clave "2"

    GET /indexes/hotels/docs/2?api-version=2015-02-28-Preview

Busque el documento que tiene la clave "3" con la sintaxis de OData:

    GET /indexes('hotels')/docs('3')?api-version=2015-02-28-Preview

<a name="CountDocs"></a>
## Documentos de recuento

La operación **Documentos de recuento** recupera un recuento del número de documentos en un índice de búsqueda. La sintaxis `$count` forma parte del protocolo OData.

    GET https://[service name].search.windows.net/indexes/[index name]/docs/$count?api-version=[api-version]
    Accept: text/plain
    api-key: [admin or query key]

**Solicitud**

HTTPS es necesario para las solicitudes de servicio. La solicitud **Documentos de recuento** puede crearse mediante el método GET.

El [nombre de índice] del URI de la solicitud indica al servicio que devuelva un recuento de todos los elementos de la colección de documentos del índice especificado.

`api-version=[string]` (obligatorio). La versión de vista previa es `api-version=2015-02-28-Preview`. Consulte [Versiones del servicio de búsqueda](http://msdn.microsoft.com/library/azure/dn864560.aspx) para obtener más información y versiones alternativas.

**Encabezados de solicitud**

En la lista siguiente se describen los encabezados de solicitud obligatorios y opcionales.

- `Accept`: este valor debe establecerse en `text/plain`.
- `api-key`: `api-key` se usa para autenticar la solicitud en su servicio de búsqueda. Es un valor de cadena, único en su URL de servicio. La solicitud **Documentos de recuento** puede especificar una clave de administración o una clave de consulta para `api-key`.

También necesitará el nombre del servicio para construir la dirección URL de la solicitud. Puede obtener el nombre de servicio y `api-key` desde el panel de servicio en el Portal de Azure. Consulte [Crear un servicio de Búsqueda de Azure en el portal](search-create-service-portal.md) para obtener ayuda sobre la navegación en páginas.

**Cuerpo de la solicitud**

Ninguno.

**Respuesta**

Código de estado: al obtener una respuesta correcta, se visualiza 200 Correcto.

El cuerpo de la respuesta contiene el valor de recuento como un entero con formato de texto sin formato.

<a name="Suggestions"></a>
## Sugerencias

La operación **Sugerencias** recupera sugerencias basadas en la entrada de búsqueda parcial. Se suele usar en los cuadros de búsqueda para proporcionar sugerencias anticipadas cuando los usuarios están introduciendo términos de búsqueda.

Las solicitudes de sugerencias están destinadas a sugerir documentos de destino, por lo que el texto sugerido puede repetirse si varios documentos candidatos coinciden con la misma entrada de búsqueda. Puede usar `$select` para recuperar otros campos del documento (incluida la clave del documento) para que pueda indicar qué documento es el origen para cada sugerencia.

Una operación **Sugerencias** se emite como una solicitud GET o POST.

    GET https://[service name].search.windows.net/indexes/[index name]/docs/suggest?[query parameters]
    api-key: [admin or query key]

    POST https://[service name].search.windows.net/indexes/[index name]/docs/suggest?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin or query key]

**Cuándo usar POST en lugar de GET**

Cuando use HTTP GET para llamar a la API de **Sugerencias**, deberá tener en cuenta que la longitud de la URL de la solicitud no puede superar los 8 KB. Esto suele ser suficiente para la mayoría de las aplicaciones. Sin embargo, algunas aplicaciones generan consultas muy extensas, en concreto, expresiones de filtro de OData. Para estas aplicaciones, el uso de HTTP POST es una opción mejor porque permite filtros mayores que GET. Con POST, el número de cláusulas en un filtro es el factor limitador, no el tamaño de la cadena del filtro, ya que el límite de tamaño de la solicitud POST es de 16 MB aproximadamente.

> [AZURE.NOTE] Aunque el límite de tamaño de la solicitud POST es muy grande, las expresiones de filtro no pueden ser arbitrariamente complejas. Consulte [Sintaxis de expresiones de OData](https://msdn.microsoft.com/library/dn798921.aspx) para más información sobre las limitaciones de complejidad de filtros.

**Solicitud**

HTTPS es necesario para las solicitudes de servicio. La solicitud de **Sugerencias** puede crearse mediante los métodos GET o POST.

El URI de la solicitud especifica el nombre del índice que se consulta. Los parámetros, como el término de búsqueda de entrada parcial, se especifican en la cadena de consulta en el caso de solicitudes GET y en el cuerpo de la solicitud en el caso de las solicitudes POST.

Como práctica recomendada al crear solicitudes GET, recuerde [codificar con URL](https://msdn.microsoft.com/library/system.uri.escapedatastring.aspx) los parámetros de consulta específicos al llamar a la API de REST directamente. Para las operaciones **Sugerencias**, se incluye lo siguiente:

- `$filter`
- `highlightPreTag`
- `highlightPostTag`
- `search`

Solo se recomienda la codificación de direcciones URL en los parámetros de consulta anterior. Si codifica con URL involuntariamente la cadena de consulta completa (todo lo situado después de la?), las solicitudes se dividirán.

Además, la codificación con URL solo es necesaria cuando se llama directamente a la API de REST directamente con GET. No se necesita ninguna codificación de URL para llamar a las **Sugerencias** mediante POST o cuando se usa la [biblioteca de cliente .NET](https://msdn.microsoft.com/library/dn951165.aspx), que controla la codificación de direcciones URL en su lugar.

**Parámetros de consulta**

Las **sugerencias** aceptan varios parámetros que ofrecen criterios de consulta y que también especifican el comportamiento de la búsqueda. Estos parámetros se especifican en la cadena de consulta URL al llamar a las **Sugerencias** mediante GET y como propiedades JSON en el cuerpo de solicitud al llamar a las **Sugerencias** mediante POST. La sintaxis de algunos parámetros es algo diferente entre GET y POST. Estas diferencias se indican como aplicables a continuación:

`search=[string]`: texto de búsqueda que se utiliza para sugerir las consultas. Debe tener 1 carácter como mínimo y no más de 100 caracteres.

`highlightPreTag=[string]` (opcional): una etiqueta de cadena que se antepone a resultados de búsquedas. Debe establecerse con `highlightPostTag`.

> [AZURE.NOTE] Al llamar a las **Sugerencias** mediante GET, los caracteres reservados en la dirección URL deben estar codificados con porcentaje (por ejemplo, %23 en vez de #).

`highlightPostTag=[string]` (opcional): una etiqueta de cadena que se adjunta a resultados de búsquedas. Debe establecerse con `highlightPreTag`.

> [AZURE.NOTE] Al llamar a las **Sugerencias** mediante GET, los caracteres reservados en la dirección URL deben estar codificados con porcentaje (por ejemplo, %23 en vez de #).

`suggesterName=[string]`: el nombre del proveedor de sugerencias tal y como se especifica en la colección `suggesters` que forma parte de la definición del índice. Un `suggester` determina qué campos se analizan para encontrar términos de consulta sugeridos. Consulte [Proveedores de sugerencias](#Suggesters) para obtener más información.

`fuzzy=[boolean]` (opcional, valor predeterminado = false): si se establece en true esta API encontrará sugerencias incluso si hay un carácter sustituido o no encontrado en el texto de búsqueda. Si bien esto proporciona una mejor experiencia en algunos escenarios, ello afecta al rendimiento, ya que las búsquedas de sugerencias aproximadas son más lentas y consumen más recursos.

`searchFields=[string]` (opcional): lista separada por comas de nombres de campo para buscar el texto de búsqueda especificado. Los campos de destino deben estar habilitados para obtener sugerencias.

`$top=#` (opcional, valor predeterminado = 5): número de sugerencias para recuperar. Debe ser un número entre 1 y 100.

> [AZURE.NOTE] Al llamar a las **sugerencias** mediante POST, este parámetro se denomina `top` en lugar de `$top`.

`$filter=[string]` (opcional): expresión que filtra los documentos que se consideran para obtener sugerencias.

> [AZURE.NOTE] Al llamar a las **sugerencias** mediante POST, este parámetro se denomina `filter` en lugar de `$filter`.

`$orderby=[string]` (opcional): lista de expresiones separadas por comas por la que ordenar los resultados. Cada expresión puede ser un nombre de campo o una llamada a la función `geo.distance()`. Cada expresión puede ir seguida de `asc` para indicar el orden ascendente y de `desc` para indicar el orden descendente. El valor predeterminado es ascendente. Hay un límite de 32 cláusulas para `$orderby`.

> [AZURE.NOTE] Al llamar a las **sugerencias** mediante POST, este parámetro se denomina `orderby` en lugar de `$orderby`.

`$select=[string]` (opcional): lista de campos separados por comas para recuperar. Si no se especifica, solo se devolverá la clave del documento y el texto de la sugerencia. Se pueden solicitar explícitamente todos los campos estableciendo este parámetro en `*`.

> [AZURE.NOTE] Al llamar a las **Sugerencias** mediante POST, este parámetro se denomina `select` en lugar de `$select`.

`minimumCoverage` (opcional, el valor predeterminado es 80): un número entre 0 y 100 que indica el porcentaje del índice que debe estar cubierto por una consulta de búsqueda para que la consulta se realice correctamente. De forma predeterminada, al menos el 80% del índice debe estar disponible o `Suggest` devolverá el código de estado HTTP 503. Si establece `minimumCoverage` y `Suggest` se realiza correctamente, devolverá HTTP 200 e incluye un valor `@search.coverage` en la respuesta que indica el porcentaje del índice que se incluyó en la consulta.

> [AZURE.NOTE] Establecer este parámetro en un valor inferior a 100 puede ser útil para garantizar la disponibilidad de la búsqueda incluso para servicios con una única réplica. Sin embargo, no se garantiza que todos los documentos coincidentes existan en los resultados. Si la recuperación es más importante para la aplicación que la disponibilidad, es mejor dejar `minimumCoverage` por debajo de su valor predeterminado de 80.

`api-version=[string]` (obligatorio). La versión de vista previa es `api-version=2015-02-28-Preview`. Consulte [Versiones del servicio de búsqueda](http://msdn.microsoft.com/library/azure/dn864560.aspx) para obtener más información y versiones alternativas.

Nota: Para esta operación, `api-version` se especifica como un parámetro de consulta en la dirección URL sin tener en cuenta si se llama a las **Sugerencias** con GET o POST.

**Encabezados de solicitud**

En la lista siguiente se describen los encabezados de solicitud obligatorios y opcionales.

- `api-key`: `api-key` se usa para autenticar la solicitud en su servicio de búsqueda. Es un valor de cadena, único en su URL de servicio. La solicitud **Sugerencias** puede especificar una clave de administración o una clave de consulta como `api-key`.

También necesitará el nombre del servicio para construir la dirección URL de la solicitud. Puede obtener el nombre de servicio y `api-key` desde el panel de servicio en el Portal de Azure. Consulte [Crear un servicio de Búsqueda de Azure en el portal](search-create-service-portal.md) para obtener ayuda sobre la navegación en páginas.

**Cuerpo de la solicitud**

Para GET: ninguno.

Para POST:

    {
      "filter": "odata_filter_expression",
	  "fuzzy": true | false (default),
      "highlightPreTag": "pre_tag",
      "highlightPostTag": "post_tag",
      "minimumCoverage": # (% of index that must be covered to declare query successful; default 80),
      "orderby": "orderby_expression",
      "search": "partial_search_input",
      "searchFields": "field_name_1, field_name_2, ...",
      "select": "field_name_1, field_name_2, ...",
	  "suggesterName": "suggester_name",
      "top": # (default 5)
    }

**Respuesta**

Código de estado: al obtener una respuesta correcta, se visualiza 200 Correcto.

    {
      "@search.coverage": # (if minimumCoverage was provided in the query),
      "value": [
        {
          "@search.text": "...",
          "<key field>": "..."
        },
        ...
      ]
    }

Si se utiliza la opción de proyección para recuperar campos que están incluidos en cada elemento de la matriz:

    {
      "@search.coverage": # (if minimumCoverage was provided in the query),
      "value": [
        {
          "@search.text": "...",
          "<key field>": "..."
          <other projected data fields>
        },
        ...
      ]
    }

**Ejemplo**

Recupere 5 sugerencias en las que la entrada de búsqueda parcial sea "lux"

    GET /indexes/hotels/docs/suggest?search=lux&$top=5&suggesterName=sg&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/suggest?api-version=2015-02-28-Preview
    {
      "search": "lux",
      "top": 5,
      "suggesterName": "sg"
    }

<!---HONumber=AcomDC_0224_2016-->