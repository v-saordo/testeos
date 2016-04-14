<properties
	pageTitle="Analizadores personalizados (API de REST de Búsqueda de Azure versión 2015-02-28-Versión preliminar) | Microsoft Azure"
	description="Analizadores personalizados (API de REST de Búsqueda de Azure versión 2015-02-28-Versión preliminar)"
	services="search"
	documentationCenter=""
	authors="Yahnoosh"
	manager="pablocas"
	editor=""/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.author="jlembicz"
	ms.date="02/18/2016" />

# Analizadores (API de REST de Búsqueda de Azure versión 2015-02-28-Versión preliminar)

> [AZURE.NOTE] La compatibilidad con analizadores personalizados está actualmente en versión preliminar. Para poder aprovechar esta característica tiene que utilizar la versión 2015-02-28-Versión preliminar de la API de REST del servicio de Búsqueda de Azure. Tenga en cuenta que las características de la versión preliminar actualmente no están agregadas al SDK para .NET, por lo que usar la API de REST en versión preliminar es la única opción de programación que tiene en este momento.

## Información general

El rol de un motor de búsqueda de texto completo, en términos simples, es procesar y almacenar documentos de forma que sea posible realizar de forma eficiente consultas y recuperación. A nivel general, se trata en definitiva de extraer palabras importantes de los documentos, colocarlas en un índice y luego utilizar el índice para buscar documentos que coincidan con las palabras de una consulta determinada. El proceso de extraer palabras de los documentos y buscar consultas se denomina análisis léxico. Los componentes que realizan los análisis léxicos se denominan analizadores. En Búsqueda de Azure puede elegir entre un conjunto de [analizadores predefinidos independientes del lenguaje](#Analyzers) y [analizadores específicos de un lenguaje](https://msdn.microsoft.com/library/azure/dn879793.aspx). También tiene una opción para definir sus propios, analizadores personalizados. Un analizador personalizado le permiten hacerse con el control del proceso de conversión de texto en tokens que se pueden indexar o buscar. Se trata de una configuración definida por el usuario que consta de un único tokenizer predefinido y uno o más filtros de token. El tokenizer es el responsable de dividir texto en tokens y los filtros de token son los que modifican los tokens emitidos por el tokenizer.

Escenarios populares habilitados por analizadores personalizados incluyen:

- Búsqueda fonética: agregue un filtro fonético para habilitar la búsqueda basada en cómo suena una palabra en lugar de en cómo es escribe.
- Deshabilitar el análisis léxico: use el analizador de palabra clave para crear campos de búsqueda que o se analizan
- Búsqueda rápida de prefijo/sufijo: agregue el filtro de token Edge N-gram a prefijos de índice de palabras para habilitar la coincidencia de prefijo rápida. Combínelo con el filtro de token de tipo Reverse para habilitar la coincidencia de sufijo.
- Tokenización personalizada: por ejemplo, use el tokenizer Whitespace para dividir las oraciones en tokens usando el espacio en blanco como un delimitador
- Plegamiento de ASCII: agregue el filtro de plegamiento de ASCII para normalizar los signos diacríticos como ö o ê en términos de búsqueda.

Puede definir varios analizadores personalizados para variar la combinación de filtros, pero cada campo solo puede usar un analizador para el análisis de indexación y otro para el análisis de la búsqueda.
 
Esta página proporciona una lista de analizadores, tokenizer y filtros de token admitidos. También encontrará una descripción de los cambios en la definición del índice con un ejemplo de uso. Introducción y exploración de escenarios, consulte [Analizadores personalizados en Búsqueda de Azure](link). Para más información sobre la tecnología subyacente aprovechada en la implementación de Búsqueda de Azure, consulte [el resumen del paquete de análisis Lucene](http://lucene.apache.org/core/4_10_3/core/org/apache/lucene/analysis/package-summary.html).


## Analizador predeterminado

El analizador predeterminado es [Apache Lucene Standard](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html).

## Reglas de validación

Los nombres de los analizadores personalizados, tokenizer y filtros de token tienen que ser únicos y no pueden los mismos que los de cualquiera de los analizadores, tokenizer o filtros de token predefinidos.
 

## Definición de índices con los analizadores personalizados 

Los analizadores personalizados se definen en el momento de la creación de índices. A continuación, se muestra la sintaxis de los componentes de análisis personalizado en la definición del índice. Se proporciona un ejemplo completo [aquí](#Example)

	{
	  	. . .
	 	(standard create index request body)
	 	. . .  
		"analyzers": (optional) [ 
		    {  
		       "name": "name of analyzer",  
		       "@odata.type": "#Microsoft.Azure.Search.CustomAnalyzer", 
		       "tokenizer": "tokenizer_name", (tokenizer_name is the name of a tokenizer from the [Tokenizers](#Tokenizers) table)
		       "tokenFilters": [
				  "token_filter_name", (token_filter_name is the name of a token filter from the [Token Filters](#TokenFilters) table)
				  "token_filter_name"
				] (token filters will be applied from left to right)
		    },
			{  
		       "name": "name of analyzer",  
		       "@odata.type": "#analyzer_type", 
		       "option1": value1,
		       "option2": value2,
		       ...  
		    }
		],  
		"tokenizers": (optional) [ 
		    {  
		       "name": "tokenizer_name",  
		       "@odata.type": "#tokenizer_type", 
		       "option1": value1,  
		       "option2": value2, 
		       ...  
		    }
		],
		"tokenFilters": (optional) [ 
		    {  
		       "name": "token_filter_name",  
		       "@odata.type": "#token_filter_type", 
		       "option1": value1,  
		       "option2": value2, 
		       ...  
		    }
		]	
	}

> [AZURE.NOTE] Los analizadores personalizados que cree, no se exponen en el portal de Azure. La única manera de agregar un analizador personalizado es a través del código que realiza llamadas a la API de REST al definir un índice.

##Atributos de índice para analizadores personalizados, tokenizer y filtros de token 

Esta sección especifica las propiedades de configuración para los analizadores, tokenizer y filtros de token de una definición de índice.

###Analizadores

<table>
  <tbody>
    <tr>
      <td>
        <b>Name</b>
      </td>
      <td>
		Solo debe contener letras, dígitos, espacios, guiones o caracteres de subrayado, solo puede iniciarse y finalizar con caracteres alfanuméricos y tiene un límite de 128 caracteres.
      </td>
    </tr>
    <tr>
      <td>
        <b>Tipo</b>
      </td>
      <td>
		Los valores válidos son "#Microsoft.Azure.Search.CustomAnalyzer" o un nombre de analizador de la lista de analizadores admitidos. Consulte la columna <b>analyzer_type</b> en la tabla [Analizadores](#Analyzers) a continuación.
      </td>
	</tr>
    <tr>
      <td>
        <b>Tokenizer</b> (solo válido para #Microsoft.Azure.Search.CustomAnalyzer)
      </td>
      <td>
		Obligatorio. Tiene que ser uno de los tokenizer predefinidos que aparecen en la tabla [Tokenizer](#Tokenizers) a continuación o cualquiera de los tokenizer personalizados definidos en la definición del índice.
      </td>
	</tr>
    <tr>
      <td>
        <b>Filtros tokenizer</b> (solo válido para #Microsoft.Azure.Search.CustomAnalyzer)
      </td>
      <td>
		Todos los filtros de token son o bien uno de los filtros predefinidos de token enumerados en la tabla [Filtros de token](#TokenFilters) o cualquiera de los filtros de token personalizados definidos en la definición del índice.
      </td>
	</tr>
    <tr>
      <td>
        <b>Opciones</b>
      </td>
      <td>
		Tienen que ser [opciones válidas](#Analyzers) de un analizador predefinido (no personalizado).
      </td>
	</tr>
  </tbody>
</table>

###Tokenizer

Un tokenizer divide un texto continuo en una secuencia de tokens, como una frase se divide en palabras. Puede especificar exactamente un tokenizer por analizador personalizado. Si necesita más de un tokenizer, puede crear varios analizadores personalizados y asignarlos campo por campo en el esquema de índice. Un analizador personalizado puede usar un tokenizer predefinido con opciones predeterminadas o personalizadas.

<table>
  <tbody>
    <tr>
      <td>
        <b>Name</b>
      </td>
      <td>
		Solo debe contener letras, dígitos, espacios, guiones o caracteres de subrayado, solo puede iniciarse y finalizar con caracteres alfanuméricos y tiene un límite de 128 caracteres.
      </td>
    </tr>
    <tr>
      <td>
        <b>Tipo</b>
      </td>
      <td>
		Nombre de tokenizer de la lista de tokenizer admitidos. Consulte la columna <b>tokenizer_type</b> en la tabla [Tokenizer](#Tokenizers) a continuación.
      </td>
	</tr>
     <tr>
      <td>
        <b>Opciones</b>
      </td>
      <td>
		Tiene que ser [opciones válidas](#Tokenizers) de un tipo determinado de tokenizer.
      </td>
	</tr>
  </tbody>
</table>

###Filtros de token

Un filtro de token se utiliza para filtrar o modificar los tokens generados por un tokenizer. Por ejemplo, puede especificar un filtro en minúsculas que convierte todos los caracteres a minúsculas. Puede tener varios filtros de token en un analizador personalizado. Los filtros de token se ejecutan en el orden en que aparecen.

<table>
  <tbody>
    <tr>
      <td>
        <b>Name</b>
      </td>
      <td>
		Solo debe contener letras, dígitos, espacios, guiones o caracteres de subrayado, solo puede iniciarse y finalizar con caracteres alfanuméricos y tiene un límite de 128 caracteres.
      </td>
    </tr>
    <tr>
      <td>
        <b>Tipo</b>
      </td>
      <td>
		Nombre de filtro de token de la lista de filtros de token admitidos. Consulte la columna <b>token_filter_type</b> en la tabla [Filtros de token](#TokenFilters) a continuación.
      </td>
	</tr>
     <tr>
      <td>
        <b>Opciones</b>
      </td>
      <td>
		Tiene que ser [opciones válidas](#TokenFilters) de un tipo determinado de filtro de token.
      </td>
	</tr>
  </tbody>
</table>

<a name="Example"></a>
##Ejemplo de definición de índice

Este ejemplo de definición de índice incluye un campo mediante un analizador personalizado "my\_analyzer", que a su vez utiliza un tokenzier estándar personalizado "my\_standard\_tokenizer" y dos filtros de token: el filtro de conversión a minúsculas "lowercase" y el filtro personalizado de plegamiento de ASCII "my\_asciifolding".

	{
	   "name":"myindex",
	   "fields":[
	      {
	         "name":"id",
	         "type":"Edm.String",
	         "key":true,
	         "searchable":false
	      },
	      {
	         "name":"text",
	         "type":"Edm.String",
	         "searchable":true,
	         "analyzer":"my_analyzer"
	      }
	   ],
	   "analyzers":[
	      {
	         "name":"my_analyzer",
	         "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
	         "tokenizer":"my_standard_tokenizer",
	         "tokenFilters":[
	            "my_asciifolding",
	            "lowercase"
	         ]
	      }
	   ],
	   "tokenizers":[
	      {
	         "name":"my_standard_tokenizer",
	         "@odata.type":"#Microsoft.Azure.Search.StandardTokenizer",
	         "maxTokenLength":20
	      }
	   ],
	   "tokenFilters":[
	      {
	         "name":"my_asciifolding",
	         "@odata.type":"#Microsoft.Azure.Search.AsciiFoldingTokenFilter",
	         "preserveOriginal":true
	      }
	   ]
	}

<a name="Analyzers"></a>
##Analizadores

<table>
  <tbody>
	<thead>
	<tr>
		<td>analyzer_name</td>
		<td>analyzer_type</td>
		<td>Descripción</td>
		<td>Opciones</td>
	</tr>
	</thead>
    <tr>
      <td>[keyword](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html)</td>
      <td>#Microsoft.Azure.Search.KeywordAnalyzer</td>
	  <td>Todo el contenido de un campo se trata como un solo token. Esto es útil para los datos como códigos postales, identificadores y algunos nombres de producto</td>
	  <td></td>
    </tr>
    <tr>
      <td>[pattern](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/PatternAnalyzer.html)</td>
      <td>#Microsoft.Azure.Search.PatternAnalyzer</td>
	  <td>Separa el texto de manera flexible en términos a través de un patrón de expresión regular</td>
	  <td>
		- lowercase - tipo: booleano - deben los términos ir en minúscula, valor predeterminado true - [pattern] (http://docs.oracle.com/javase/7/docs/api/java/util/regex/Pattern.html?is-external=true) - tipo: cadena - un patrón de expresión regular para que coincida con los separadores de token, valor predeterminado: \w+ - [flags] (http://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html#field_summary) - tipo: cadena - marcas de expresión regular, valor predeterminado: un tipo de cadena vacía - stopwords - tipo: matriz de cadenas- una lista de palabras irrelevantes, valor predeterminado: una lista vacía
	  </td>
    </tr>
    <tr>
      <td>[simple](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/SimpleAnalyzer.html)</td>
      <td>#Microsoft.Azure.Search.SimpleAnalyzer</td>	 
	  <td>Divide el texto en los lugares en donde no hay letras y los convierte a minúsculas</td>
	  <td></td>
    </tr>
    <tr>
      <td>[snowball](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/snowball/SnowballAnalyzer.html)</td>
      <td>#Microsoft.Azure.Search.SnowballAnalyzer</td>
	  <td>Un analizador estándar con el [filtro de lematización snowball] (http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/snowball/SnowballFilter.html)</td>
	  <td>
		- language - tipo: cadena - valores permitidos: danés, neerlandés, inglés, finés, francés, alemán, húngaro, italiano, noruego, portugués, ruso, español, sueco -palabras no significativas- tipo: matriz de cadenas - una lista de palabras no significativas, valor predeterminado: una lista vacía
	  </td>
    </tr>
    <tr>
      <td>[standard](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html)</td>
      <td>#Microsoft.Azure.Search.StandardAnalyzer</td>
	  <td>Analizador de Lucene estándar: se compone del tokenizer estándar, el filtro de minúsculas (LowercaseFilter) y el filtro de palabras no significativas (StopFilter)</td>
	  <td>
		- maxTokenLength - tipo: int - la longitud máxima del token, valor predeterminado: 255. Los tokens que sobrepasen la longitud máxima se dividen. - stopwords - tipo: matriz de cadenas - una lista de palabras no significativas, valor predeterminado: una lista vacía
	  </td>
    </tr>
    <tr>
      <td>standardasciifolding.lucene</td>
      <td>#Microsoft.Azure.Search.StandardAsciiFoldingAnalyzer</td>	  
	  <td>Analizador estándar con el filtro de plegamiento de Ascii</td>
	  <td></td>
    </tr>
    <tr>
      <td>[stop] (http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/StopAnalyzer.html)</td>
      <td>#Microsoft.Azure.Search.StopAnalyzer</td>
	  <td>Divide el texto en los lugares en donde no hay letras, aplica los filtros de token de minúsculas y de palabra no significativa</td>
	  <td>
		- stopwords - tipo: matriz de cadenas - una lista de palabras no significativas, valor predeterminado: una lista vacía
	  </td>
    </tr>
    <tr>
      <td>[whitespace](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html)</td>
      <td>#Microsoft.Azure.Search.WhitespaceAnalyzer</td>
	  <td>Un analizador que usa el tokenizer de espacio en blanco.</td>
	  <td></td>
    </tr>
  </tbody>
</table>

<a name="Tokenizers"></a>
##Tokenizer

<table>
  <tbody>
	<thead>
	<tr>
		<td>tokenizer_name</td>
		<td>tokenizer_type</td>
		<td>Descripción</td>
		<td>Opciones</td>
	</tr>
	</thead>
    <tr>
      <td>[classic](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/ClassicTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.ClassicTokenizer</td>
	  <td>Tokenizer que se basa en la gramática, es adecuado para procesar la mayoría de los documentos en idiomas europeos</td>
	  <td>
		- maxTokenLength - tipo: int - la longitud máxima del token, valor predeterminado: 255. Los tokens que superan la longitud máxima se dividen. 
	  </td>	  
    </tr>
    <tr>
      <td>[edgeNGram](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.EdgeNGramTokenizer</td>
	  <td>Divide en tokens a partir de una entrada perimetral creando N-gramas de un tamaño o tamaños determinados</td>
	  <td>
		- minGram - tipo: int - predeterminado: 1 - maxGram - tipo: int - predeterminado: 2 - tokenChars - tipo: matriz de cadenas - clases de caracteres para mantener en los tokens, valores permitidos: letras, dígitos, espacio en blanco, puntuación, símbolos
	  </td>	  
    </tr>
    <tr>
      <td>[keyword](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.KeywordTokenizer</td>
	  <td>Emite la entrada completa como un token único</td>
	  <td>
		-bufferSize - tipo: int - tamaño del búfer de lectura, valor predeterminado: 256
	  </td>
    </tr>
    <tr>
      <td>[letter](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/LetterTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.LetterTokenizer</td>
	  <td>Divide el texto en los lugares en donde no hay letras</td>
	  <td></td>
    </tr>
    <tr>
      <td>[lowercase](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/LowerCaseTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.LowercaseTokenizer</td>
	  <td>Divide el texto en los lugares en donde no hay letras y los convierte a minúsculas</td>
	  <td></td>
    </tr>
    <tr>
      <td>[nGram](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.NGramTokenizer</td>
	  <td>Divide en tokens a partir de una entrada creando N-gramas del tamaño o tamaños determinados</td>
	  <td>
		- minGram - tipo: int - predeterminado: 1 - maxGram - tipo: int - predeterminado: 2 - tokenChars - tipo: matriz de cadenas - clases de caracteres para mantener en los tokens, valores permitidos: letras, dígitos, espacio en blanco, puntuación, símbolos
	  </td>
    </tr>
    <tr>
      <td>[path_hierarchy](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/path/PathHierarchyTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.PathHierarchyTokenizer</td>
	  <td>Tokenizer para jerarquías de tipo de ruta de acceso</td>
	  <td>
		- delimiter - tipo: cadena - valor predeterminado: '/' - replacement - tipo: cadena - si se establece, reemplaza el carácter delimitador, valor predeterminada: delimitador - bufferSize - tipo: int - valor predeterminado: 1024 - reverse - tipo: booleano - si es el valor es true, genera el token en orden inverso, valor predeterminado: true - skip - tipo: booleano - tokens iniciales que se omiten, valor predeterminado: 0
	  </td>
    </tr>
    <tr>
      <td>[pattern](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/pattern/PatternTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.PatternTokenizer</td>
	  <td>Este tokenizer usa la correspondencia de patrón Regex para construir tokens distintos</td>
	  <td>
		 - pattern - tipo: cadena - patrón de expresión regular, valor predeterminado: \w+ - [flags] (http://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html#field_summary) - tipo: cadena - marcas de expresión regular, valor predeterminado: una cadena vacía - group - tipo: int - qué grupo se extraerá en tokens, valor predeterminado: -1 (división)
	  </td>
    </tr>
    <tr>
      <td>[standard](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/StandardTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.StandardTokenizer</td>
	  <td>Divide el texto siguiendo las [reglas de segmentación de texto Unicode] (http://unicode.org/reports/tr29/)</td>
	  <td>
		 - maxTokenLength - tipo: int - la longitud máxima del token, valor predeterminado: 255. Los tokens que superan la longitud máxima se dividen. 
	  </td>
    </tr>
    <tr>
      <td>[uax_url_email](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/UAX29URLEmailTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.UaxEmailUrlTokenizer</td>
	  <td>Convierte direcciones URL y mensajes de correo electrónico en un solo token</td>
	  <td>
		- maxTokenLength - tipo: int - la longitud máxima del token, valor predeterminado: 255. Los tokens que superan la longitud máxima se dividen.
	  </td>
    </tr>
    <tr>
      <td>[whitespace](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/WhitespaceTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.WhitespaceTokenizer</td>
	  <td>Divide el texto en los espacio en blanco</td>
	  <td></td>
    </tr>
  </tbody>
</table>

<a name="TokenFilters"></a>
##Filtros de token

<table>
  <tbody>
	<thead>
	<tr>
		<td>token_filter_name</td>
		<td>token_filter_type</td>
		<td>Descripción</td>
		<td>Opciones</td>
	</tr>
	</thead>
    <tr>
      <td>[arabic_normalization] (http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ar/ArabicNormalizationFilter.html)</td>
      <td>#Microsoft.Azure.Search.ArabicNormalizationTokenFilter</td>
	  <td>Elimina todos los caracteres situados después de un apóstrofo (incluido el propio apóstrofo)</td>
    </tr>
    <tr>
      <td>[apostrophe](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/tr/ApostropheFilter.html)</td>
      <td>#Microsoft.Azure.Search.ApostropheTokenFilter</td>
	  <td>Elimina todos los caracteres después de un apóstrofo</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[asciifolding](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/ASCIIFoldingFilter.html)</td>
      <td>#Microsoft.Azure.Search.AsciiFoldingTokenFilter</td>
	  <td>Convierte los caracteres Unicode alfabéticos, numéricos y simbólicos que no están en los primeros 127 caracteres ASCII (el bloque Unicode "Latín básico") en sus valores ASCII equivalentes, si existe alguno</td>
	  <td>
		- preserveOriginal - tipo: booleano - si es el valor es true, el token original se mantendrán, valor predeterminado: false
	  </td>	  
    </tr>
    <tr>
      <td>[cjk_bigram](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/cjk/CJKBigramFilter.html)</td>
      <td>#Microsoft.Azure.Search.CjkBigramTokenFilter</td>
	  <td>Forma bigramas de términos CJK que se generan a partir del StandardTokenizer</td>
	  <td>
		- ignoreScripts - tipo: booleano - scripts que se ignorarán, los valores permitidos: han hiragana, katakana, hangul. Valor predeterminado: una lista vacía - outputUnigram - tipo: booleano - se establece en el valor true si desea dar siempre salida tanto a unigramas como a bigramas, valor predeterminado: false
	  </td>	  
    </tr>
    <tr>
      <td>[cjk_width](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/cjk/CJKWidthFilter.html)</td>
      <td>#Microsoft.Azure.Search.CjkWidthTokenFilter</td>
	  <td>Normaliza las diferencias de ancho de los caracteres CJK. Pliega las variantes de ASCII de ancho completo en el equivalente de Latín básico y las variantes de Katakana de mitad de ancho en el equivalente kana</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[classic](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/ClassicFilter.html)</td>
      <td>#Microsoft.Azure.Search.ClassicTokenFilter</td>
	  <td>Quita los posesivos del inglés y los puntos de los acrónimos</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[common_grams](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/commongrams/CommonGramsFilter.html)</td>
      <td>#Microsoft.Azure.Search.CommonGramTokenFilter</td>
	  <td>Construye bigramas para los términos que se producen con frecuencia durante la indexación. Los términos únicos también se indexan, con bigramas superpuestos.</td>
	  <td>
		- commonWords - tipo: matriz de cadena - el conjunto de palabras comunes, valor predeterminado: una lista vacía - ignoreCase - tipo: booleano - si el valor es true, la coincidencia de palabras comunes se realizará sin diferenciar entre mayúsculas y minúsculas, valor predeterminado: false - queryMode - tipo: booleano - genera bigramas, a continuación, quita palabras comunes y términos únicos seguidos de una palabra común, valor predeterminado: false
	  </td>	  
    </tr>
    <tr>
      <td>[delimited_payload_filter](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/payloads/DelimitedPayloadTokenFilter.html)</td>
      <td>#Microsoft.Azure.Search.DelimitedPayloadTokenFilter</td>
	  <td>Los caracteres antes del delimitador son "token", los que están después son la carga. Por ejemplo, si el delimitador es ' |', en la cadena "hello|world", "hello" es el token y "world" es la carga. También puede incluir un codificador para convertir la carga de forma adecuada (de caracteres a bytes)</td>
	  <td>
		- delimiter - tipo: carácter, valor predeterminado: ' |' - encoding - tipo: cadena - valores permitidos: int, float, identity. Valor predeterminado: float
	  </td>	  
    </tr>
    <tr>
      <td>[dictionary_decompounder](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/compound/DictionaryCompoundWordTokenFilter.html)</td>
      <td>#Microsoft.Azure.Search.DictionaryDecompounderTokenFilter</td>
	  <td>Descompone palabras compuestas que se encuentra en muchas lenguas germánicas</td>
	  <td>
		- wordList - tipo: matriz de cadena - la lista de palabras para coincidencias, valor predeterminado: una lista vacía - minWordSize - tipo: int - solo se procesan palabras más largas que el valor indicado, valor predeterminado: 5 - minSubwordSize - tipo: int - solo se procesan subpalabras más largas que el valor indicado, valor predeterminado: 2 - maxSubwordSize - tipo: int - solo se procesan subpalabras más cortas que el valor indicado, valor predeterminado: 15 - onlyLongestMatch - tipo: booleano - se agrega solo la subpalabra coincidente más larga a la salida , valor predeterminado: false 
	  </td>	  
    </tr>
    <tr>
      <td>[edgeNGram](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenFilter.html)</td>
      <td>#Microsoft.Azure.Search.EdgeNGramTokenFilter</td>
	  <td>Genera N-gramas del tamaño o tamaños determinados empezando desde delante o detrás de un token de entrada</td>
	  <td>
		-minGram - tipo: int - valor predeterminado: 1 - maxGram - tipo: int - valor predeterminado: 2 -side - tipo: cadena - especifica a partir de qué lado de la entrada se debe generar el N-grama. Valores permitidos: front, back
	  </td>	  
    </tr>   
    <tr>
      <td>[elision](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/util/ElisionFilter.html)</td>
      <td>#Microsoft.Azure.Search.ElisionTokenFilter</td>
	  <td>Elimina las elisiones. Por ejemplo, "l'avion" (el avión) se convertirá en "avion" (avión).</td>
	  <td>
		- articles - tipo: matriz de cadena - un conjunto de artículos para quitar, valor predeterminado: una lista vacía
	  </td>	  
    </tr>
    <tr>
      <td>[german_normalization](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/de/GermanNormalizationFilter.html)</td>
      <td>#Microsoft.Azure.Search.GermanNormalizationTokenFilter</td>
	  <td>Normaliza los caracteres alemanes siguiendo la heurística del [algoritmo de snowball German2] (http://snowball.tartarus.org/algorithms/german2/stemmer.html)</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[hindi_normalization] (http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/hi/HindiNormalizationFilter.html)</td>
      <td>#Microsoft.Azure.Search.HindiNormalizationTokenFilter</td>
	  <td>Normaliza los textos en hindi para quitar algunas diferencias en las variaciones ortográficas</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[indic_normalization] (http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/in/IndicNormalizationFilter.html)</td>
      <td>#Microsoft.Azure.Search.IndicNormalizationTokenFilter</td>
	  <td>Normaliza la representación Unicode de texto en los idiomas de la India.</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[keep](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeepWordFilter.html)</td>
      <td>#Microsoft.Azure.Search.KeepTokenFilter</td>
	  <td>Un filtro de token que solo mantiene tokens con texto que estén contenido en una lista de palabras especificadas</td>
	  <td>
		- keepWords - tipo: matriz de cadenas - una lista de palabras para mantener, valor predeterminado: una lista vacía - keepWordsCase - tipo: booleano - si el valor es true, poner en minúsculas todas las palabras en primer lugar, valor predeterminado: false
	  </td>	  
    </tr>
    <tr>
      <td>[keep_types](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/TypeTokenFilter.html)</td>
      <td>#Microsoft.Azure.Search.KeepTypesTokenFilter</td>
	  <td>Conserva los tokens cuyos tipos aparecen en la lista determinada de tipos permitidos</td>
	  <td>
		- types - tipo: matriz de cadenas - una lista de los tipos a mantener
	  </td>	  
    </tr>
    <tr>
      <td>[keyword_marker](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeywordMarkerFilter.html)</td>
      <td>#Microsoft.Azure.Search.KeywordMarkerTokenFilter</td>
	  <td>Marca términos como palabras clave</td>
	  <td>
		- keywords - tipo: matriz de cadenas - una lista de palabras para marcar como palabras clave, valor predeterminado: un tipo de lista vacía - ignoreCase - tipo: booleano - si el valor es true, poner en minúsculas todas las palabras en primer lugar, valor predeterminado: false
	  </td>	  
    </tr>
    <tr>
      <td>[keyword_repeat](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeywordRepeatFilter.html)</td>
      <td>#Microsoft.Azure.Search.KeywordRepeatTokenFilter</td>
	  <td>Emite dos veces cada token entrante una vez como palabra clave y otra como palabra no clave</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[kstem](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/en/KStemFilter.html)</td>
      <td>#Microsoft.Azure.Search.KStemTokenFilter</td>
	  <td>Un filtro kstem de alto rendimiento para inglés</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[length](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/LengthFilter.html)</td>
      <td>#Microsoft.Azure.Search.LengthTokenFilter</td>
	  <td>Quita las palabras que son demasiado largas o demasiado cortas</td>
	  <td>
		- min - tipo: int - el número mínimo, valor predeterminado: 0 - max - tipo: int - el número máximo, valor predeterminado: valor entero máximo
	  </td>	  
    </tr>
    <tr>
      <td>[limit](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/LimitTokenCountFilter.html)</td>
      <td>#Microsoft.Azure.Search.LimitTokenFilter</td>
	  <td>Limita el número de tokens durante la indexación</td>
	  <td>
		- maxTokenCount - tipo: int - el número máximo de tokens para generar, valor predeterminado: 1 - consumeAllTokens - tipo: booleano - si todos los tokens de la entrada tiene que utilizarse incluso si se alcanza el recuento máximo de tokens, valor predeterminado: false
	  </td>	  
    </tr>
    <tr>
      <td>[lowercase](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html)</td>
      <td>#Microsoft.Azure.Search.LowercaseTokenFilter</td>
	  <td>Normaliza el texto del token en minúsculas.</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[nGram](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenFilter.html)</td>
      <td>#Microsoft.Azure.Search.NGramTokenFilter</td>
	  <td>Genera N-gramas del tamaño o tamaños determinados</td>
	  <td>
		-minGram - tipo: int, valor predeterminado: 1 - maxGram - tipo: int, valor predeterminado: 2
	  </td>	  
    </tr>
    <tr>
      <td>[pattern_capture](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/pattern/PatternCaptureGroupTokenFilter.html)</td>
      <td>#Microsoft.Azure.Search.PatternCaptureTokenFilter</td>
	  <td>Utiliza regexes de Java para emitir varios tokens: uno para cada grupo de captura en uno o varios patrones</td>
	  <td>
		- patterns - tipo: matriz de cadenas - una lista de patrones para que coincida con cada token - preserveOriginal - tipo: booleano - se establece en el valor true para devolver el token original incluso si coincide con uno de los patrones
	  </td>	  
    </tr>
    <tr>
      <td>[persian_normalization](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/fa/PersianNormalizationFilter.html)</td>
      <td>#Microsoft.Azure.Search.PersianNormalizationTokenFilter</td>
	  <td>Aplica la normalización para el idioma persa</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[phonetic]https://lucene.apache.org/core/4_10_3/analyzers-phonetic/org/apache/lucene/analysis/phonetic/package-tree.html)</td>
      <td>#Microsoft.Azure.Search.PhoneticTokenFilter</td>
	  <td>Crea tokens para coincidencias fonética.</td>
	  <td>
		- encoder - tipo: cadena - el codificador fonético que se use, valores permitidos: metaphone, doublemetaphone, soundex, refinedsoundex, caverphone1, caverphone2, Colonia, nysiis, koelnerphonetik, haasephonetik, beidermorse. Valor predeterminado: metaphone - replace - tipo: booleano - el valor es true si los tokens codificados deben reemplazar a los tokens originales y false si se deben agregar como sinónimos, valor predeterminado: true
	  </td>	  
    </tr>
    <tr>
      <td>[porter_stem](http://lucene.apache.org/core/4_10_3/analyzers-common/org/tartarus/snowball/ext/PorterStemmer.html)</td>
      <td>#Microsoft.Azure.Search.PorterStemTokenFilter</td>
	  <td>Transforma la secuencia de tokens según el [algoritmo de lematización de Porter] (http://tartarus.org/~martin/PorterStemmer/)</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[reverse](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/reverse/ReverseStringFilter.html)</td>
      <td>#Microsoft.Azure.Search.ReverseTokenFilter</td>
	  <td>Invierte la cadena de token</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[scandinavian_normalization](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianNormalizationFilter.html)</td>
      <td>#Microsoft.Azure.Search.ScandinavianNormalizationTokenFilter</td>
	  <td>Normaliza el uso de los caracteres escandinavos intercambiables</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[scandinavian_folding](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianFoldingFilter.html)</td>
      <td>#Microsoft.Azure.Search.ScandinavianFoldingNormalizationTokenFilter</td>
	  <td>Pliega los subconjuntos de caracteres escandinavos åÅäæÄÆ->a and öÖøØ->o. También discrimina el uso de las vocales dobles aa, ae, ao, oe y oo, dejando solo la primera de ellas.</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[shingle](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/shingle/ShingleFilter.html)</td>
      <td>#Microsoft.Azure.Search.ShingleTokenFilter</td>
	  <td>Crea combinaciones de tokens como un token único</td>
	  <td>
		-maxShingleSize - tipo: int, valor predeterminado: 2 - minShingleSize - tipo: int, valor predeterminado: 2 - outputUnigrams - tipo: booleano - si el valor es true, el flujo de salida contendrá los tokens de entrada (unigramas), y shingles, valor predeterminado: true - outputUnigramsIfNoShingles - tipo: booleano - si el valor es true, reemplaza el comportamiento de outputUnigrams, false para las ocasiones cuando no hay shingles disponibles, valor predeterminado: falso - SeparadorToken - tipo: cadena - la cadena que se utiliza al combinar tokens adyacentes para formar un shingle, valor predeterminado: "" - filterToken - tipo: cadena - la cadena de inserción para cada posición en la que no hay ningún token, valor predeterminado: "_"
	  </td>	  
    </tr>
    <tr>
      <td>[snowball](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/snowball/SnowballFilter.html)</td>
      <td>#Microsoft.Azure.Search.SnowballTokenFilter</td>
	  <td>Filtro de token de lematización Snowaball</td>
	  <td>
		- language - tipo: cadena - valores permitidos: armenio, vasco, catalán, danés, neerlandés, inglés, finés, francés, alemán, alemán2, húngaro, italiano, Kp, Lovins, noruego, Porter, portugués, rumano, ruso, español, sueco, turco
	  </td>	  
    </tr>
	<tr>
      <td>[sorani_normalization](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ckb/SoraniNormalizationFilter.html)</td>
      <td>#Microsoft.Azure.Search.SoraniNormalizationTokenFilter</td>
	  <td>Normaliza la representación Unicode de texto en Sorani.</td>
	  <td></td>	  
    </tr>
	<tr>
      <td>stemmer</td>
      <td>#Microsoft.Azure.Search.StemmerTokenFilter</td>
	  <td>Filtro de lematización específico de idioma</td>
	  <td>
		- language - tipo: cadena - valores permitidos: - [árabe] (http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/ar/ArabicStemmer.html) - [armenio] (http://snowball.tartarus.org/algorithms/armenian/stemmer.html) - [vasco] (http://snowball.tartarus.org/algorithms/basque/stemmer.html) - [brasileño] (http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/br/BrazilianStemmer.html) - [búlgaro] (http://members.unine.ch/jacques.savoy/Papers/BUIR.pdf) - [Catalán](http://snowball.tartarus.org/algorithms/catalan/stemmer.html) - [checo] (http://portal.acm.org/citation.cfm?id=1598600) - [danés] (http://snowball.tartarus.org/algorithms/danish/stemmer.html)- [neerlandés] (http://snowball.tartarus.org/algorithms/dutch/stemmer.html) - [inglés] (http://snowball.tartarus.org/algorithms/porter/stemmer.html) - [english_ligero](http://ciir.cs.umass.edu/pubfiles/ir-35.pdf) - [inglés _mínimo](http://www.researchgate.net/publication/220433848_How_effective_is_suffixing) - [inglés_posesivos] (http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/en/EnglishPossessiveFilter.html) -[porter2] (http://snowball.tartarus.org/algorithms/english/stemmer.html) - [lovins] (http://snowball.tartarus.org/algorithms/lovins/stemmer.html) - [finés] (http://snowball.tartarus.org/algorithms/finnish/stemmer.html) - [finés_ligero] (http://clef.isti.cnr.it/2003/WN_web/22.pdf) - [francés] (http://snowball.tartarus.org/algorithms/french/stemmer.html) - [francés_ligero] (http://dl.acm.org/citation.cfm?id=1141523) - [francés] [ francés_mínimo] (http://dl.acm.org/citation.cfm?id=318984) - [gallego] (http://bvg.udc.es/recursos_lingua/stemming.jsp)- [gallego_mínimo] (http://bvg.udc.es/recursos_lingua/stemming.jsp)- [alemán] (http://snowball.tartarus.org/algorithms/german/stemmer.html) - [alemán 2] (http://snowball.tartarus.org/algorithms/german2/stemmer.html) - [alemán_ligero] (http://dl.acm.org/citation.cfm?id=1141523) - [alemán_mínimo] (http://members.unine.ch/jacques.savoy/clef/morpho.pdf) - [griego] (http://sais.se/mthprize/2007/ntais2007.pdf) - [hindi] (http://computing.open.ac.uk/Sites/EACLSouthAsia/Papers/p6-Ramanathan.pdf) - [húngaro] (http://snowball.tartarus.org/algorithms/hungarian/stemmer.html) - [húngaro_ligero] (http://dl.acm.org/citation.cfm?id=1141523 &amp; dl = ACM &amp; coll = DL &amp; CFID = 179095584 &amp; CFTOKEN = 80067181) - [Indonesio] (http://www.illc.uva.nl/Publications/ResearchReports/MoL-2003-02.text.pdf) - [irlandés] (http://snowball.tartarus.org/otherapps/oregan/intro.html) - [italiano] (http://snowball.tartarus.org/algorithms/italian/stemmer.html) - [italiano_ligero] (http://www.ercim.eu/publication/ws-proceedings/CLEF2/savoy.pdf) - [sorani] (http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/ckb/SoraniStemmer.html) - [letón] (http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/lv/LatvianStemmer.html) - [noruego] (http://snowball.tartarus.org/algorithms/norwegian/stemmer.html) - [noruego_ligero] (http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/no/NorwegianLightStemmer.html) - [noruego_mínimo] (http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/no/NorwegianMinimalStemmer.html) - [nynorsk_ligero] (http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/no/NorwegianLightStemmer.html) - [nynorsk_mínimo](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/no/NorwegianMinimalStemmer.html) - [portugués](http://snowball.tartarus.org/algorithms/portuguese/stemmer.html) - [portugués_ligero](http://dl.acm.org/citation.cfm?id=1141523&amp;dl=ACM&amp;coll=DL&amp;CFID=179095584&amp;CFTOKEN=80067181) - [portugués_mínimo](http://www.inf.ufrgs.br/~buriol/papers/Orengo_CLEF07.pdf) - [portugués_rslp](http://www.inf.ufrgs.br//~viviane/rslp/index.htm) - [rumano](http://snowball.tartarus.org/algorithms/romanian/stemmer.html) - [ruso](http://snowball.tartarus.org/algorithms/russian/stemmer.html) - [ruso_ligero](http://doc.rero.ch/lm.php?url=1000%2C43%2C4%2C20091209094227-CA%2FDolamic_Ljiljana_-_Indexing_and_Searching_Strategies_for_the_Russian_20091209.pdf) - [español](http://snowball.tartarus.org/algorithms/spanish/stemmer.html) - [español_ligero](http://www.ercim.eu/publication/ws-proceedings/CLEF2/savoy.pdf) - [sueco](http://snowball.tartarus.org/algorithms/swedish/stemmer.html) - [sueco_ligero](http://clef.isti.cnr.it/2003/WN_web/22.pdf) - [turco](http://snowball.tartarus.org/algorithms/turkish/stemmer.html)
	  </td>	  
    </tr>
	<tr>
      <td>[stemmer_override](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/StemmerOverrideFilter.html)</td>
      <td>#Microsoft.Azure.Search.StemmerOverrideTokenFilter</td>
	  <td>Cualquier término lematizado por diccionario se marcará como palabra clave para que no se repita la operación más adelante con otros lematizadores. Tiene que colocarse antes de cualquier filtro de lematización.</td>
	  <td>
		-rules - tipo: matriz de cadenas - reglas de lematización en el siguiente formato "palabra = > lema" por ejemplo, "dijo = > decir", valor predeterminado: una lista vacía
	  </td>	  
    </tr>
	<tr>
      <td>[stop](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/StopFilter.html)</td>
      <td>#Microsoft.Azure.Search.StopTokenFilter</td>
	  <td>Quita las palabras no significativas de una secuencia de tokens</td>
	  <td>
		- stopwords - tipo: matriz de cadena - la lista de palabras no significativas, valor predeterminado: una lista vacía. -stopwords_list - tipo: cadena - lista predefinida de palabras no significativas, los valores permitidos: _arabic_, _armenian_, _basque_, _brazilian_, _bulgarian_, _catalan_, _czech_, _danish_, _dutch_, _english_, _finnish_, _french_, _galician_, _german_, _greek_, _hindi_, _hungarian_, _indonesian_, _irish_, _italian_, _latvian_, _norwegian_, _persian_, _portuguese_, _romanian_, _russian_, _sorani_, _spanish_, _swedish_, _thai_, _turkish_, valor predeterminado: _english_ - ignoreCase - tipo: booleano - si el valor es true, todas las palabras se ponen en minúscula para empezar, valor predeterminado: false - removeTrailing -: booleano - si el valor es true, ignorar el último término de búsqueda si es una palabra no significativa, valor predeterminado: true
	  </td>	  
    </tr>
	<tr>
      <td>[synonym](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/synonym/SynonymFilter.html)</td>
      <td>#Microsoft.Azure.Search.SynonymTokenFilter</td>
	  <td>Realiza coincidencias entre los sinónimos únicos o múltiples de una palabra en una secuencia de tokens</td>
	  <td>
		-synonyms - tipo: matriz de cadena - lista de sinónimos en uno de los dos formatos siguientes: - fabuloso, mítico, legendario = > fantástico, todos los términos en el lado izquierdo del símbolo = > se reemplazarán con todos los términos del lado derecho - fabuloso, mítico, legendario, fantástico - lista separada por comas de palabras equivalentes. Establezca la opción de expansión para cambiar cómo se interpreta la lista - ignoreCase - tipo: booleano - convierte en mayúsculas o minúsculas la entrada para la coincidencia, valor predeterminado: false - expand - tipo: booleano - si el valor es true, todas las palabras en la lista de sinónimos (si no se utiliza la notación = >) se asignan a otra. La siguiente lista: fabuloso, mítico, legendario, fantástico es equivalente a: fabuloso, mítico, legendario, fantástico => fabuloso, mítico, legendario, fantástico - si es falso, la siguiente lista: fabuloso, mítico, legendario, fantástico será equivalente a: fabuloso, mítico, legendario, fantástico => fabuloso   
	  </td>	  
    </tr>
	<tr>
      <td>[trim](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/TrimFilter.html)</td>
      <td>#Microsoft.Azure.Search.TrimTokenFilter</td>
	  <td>Recorta los espacios en blanco del principio y el final en los tokens</td>
	  <td></td>	  
    </tr>
	<tr>
      <td>[truncate](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/TruncateTokenFilter.html)</td>
      <td>#Microsoft.Azure.Search.TruncateTokenFilter</td>
	  <td>Divide los términos para que tengan una longitud concreta</td>
	  <td>
		- length - tipo: int - opción necesaria
	  </td>	  
    </tr>
	<tr>
      <td>[unique](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/RemoveDuplicatesTokenFilter.html)</td>
      <td>#Microsoft.Azure.Search.UniqueTokenFilter</td>
	  <td>Filtra los tokens con el mismo texto que el token anterior</td>
	  <td>
		-onlyOnSamePosition - tipo: booleano - si se establece, quita duplicados solo en la misma posición, valor predeterminado: true
	  </td>	  
    </tr>
    <tr>
      <td>[uppercase](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/UpperCaseFilter.html)</td>
      <td>#Microsoft.Azure.Search.UppercaseTokenFilter</td>
	  <td>Normaliza el texto del token a mayúsculas</td>
	  <td></td>	  
    </tr>
	<tr>
      <td>[word_delimiter](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/WordDelimiterFilter.html)</td>
      <td>#Microsoft.Azure.Search.WordDelimiterTokenFilter</td>
	  <td>Divide palabras en subpalabras y realiza transformaciones opcionales en grupos de subpalabras</td>
	  <td>
		-generateWordParts - tipo: booleano - hace que se generen partes de palabras p. ej. "AzureSearch" ->"Azure" "Search", valor predeterminado: true - generateNumberParts - tipo: booleano - hace se generen subwords de número, valor predeterminado: true - catenateWords - tipo: booleano - hace que se concatenen el máximo de ejecuciones de partes de palabras p. ej. "Azure-Search" -> "AzureSearch", valor predeterminado: false - catenateNumbers - tipo: booleano - hace que se concatenen el máximo de ejecuciones de partes de números p. ej. "1-2" -> "12", valor predeterminado: falso - catenateAll - tipo: booleano - hace que se concatenen todas las parte de las subpalabras, por ejemplo, "Azure-Search-1" -> "AzureSearch1", valor predeterminado: false - splitOnCaseChange - tipo: booleano - si es true, divide palabras en caseChange p. ej. "AzureSearch" -> "Azure" "Search", valor predeterminado: true - preserveOriginal - hace que se conserven las palabras originales y se agreguen a la lista de subpalabras, valor predeterminado: falso - splitOnNumerics - tipo: booleano - si es true, divide los números p. ej., "Azure1Search" -> "Azure" "8" "Search", valor predeterminado: true - stemEnglishPossessive - tipo: booleano- hace que se quite la "s" del final para cada subpalabra, valor predeterminado: true - protectedWords - tipo: matriz de cadenas - tokens para impedir que se delimiten, valor predeterminado: una lista vacía
	  </td>	  
    </tr>
  </tbody>
</table>

**Consulte también** [Servicio de búsqueda de Azure REST](http://msdn.microsoft.com/library/azure/dn798935.aspx) en MSDN <br/> [Crear índice (API de REST de servicios de búsqueda de Azure)](http://msdn.microsoft.com/library/azure/dn798941.aspx) en MSDN<br/>

<!---HONumber=AcomDC_0224_2016-->