<properties 
	pageTitle="Procedimiento para implementar la navegación por facetas en Búsqueda de Azure | Microsoft Azure | Categorías de navegación de búsquedas" 
	description="Agregue navegación con facetas a aplicaciones que se integran con Búsqueda de Azure, un servicio de búsqueda hospedado en la nube en Microsoft Azure." 
	services="search" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="mblythe" 
	editor=""/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="02/18/2016" 
	ms.author="heidist"/>

#Procedimiento para implementar la navegación por facetas en Búsqueda de Azure

La navegación por facetas es un mecanismo de filtrado que proporciona una navegación en profundidad autodirigida en aplicaciones de búsqueda. Aunque es posible que el término “navegación por facetas” no le resulte familiar, es casi seguro que ya ha usado esta característica. Como se muestra en el siguiente ejemplo, la navegación por facetas no es más que las categorías usadas para filtrar los resultados.

## Su aspecto

 ![][1]
  
Las facetas pueden ayudarle a encontrar lo que busca y le garantizan que obtendrá al menos un resultado. Como desarrollador, las facetas permiten exponer los criterios de búsqueda más útiles para navegar por el corpus de búsqueda. En las aplicaciones de tienda en línea, la navegación por facetas suele basarse en marcas, departamentos (zapatos para niños), tamaño, precio, popularidad y clasificación.

La implementación de la navegación por facetas difiere en las distintas tecnologías de búsqueda y puede resultar muy compleja. En Búsqueda de Azure, la navegación por facetas se compila en tiempo de consulta usando los campos con atributos especificados anteriormente en el esquema. En las consultas que la aplicación crea, una consulta debe enviar *parámetros de consulta de faceta* para recibir los valores de filtro de faceta disponibles para ese conjunto de resultados de documento. Para recortar realmente el conjunto de resultados de documento, la aplicación debe aplicar una expresión `$filter`.

En términos de desarrollo de aplicaciones, la escritura del código que construye las consultas supone la mayor parte del trabajo. El servicio proporciona muchos de los comportamientos de aplicación que podrían interesarle de la navegación por facetas, incluida la compatibilidad integrada para establecer intervalos y obtener recuentos de los resultados de la faceta. El servicio también incluye valores predeterminados razonables que le ayudarán a evitar estructuras de navegación difíciles de manejar.

Este artículo contiene las siguientes secciones:

- [Cómo crearla](#howtobuildit)
- [Crear la capa de presentación](#presentationlayer)
- [Crear el índice](#buildindex)
- [Comprobar la calidad de los datos](#checkdata)
- [Crear la consulta](#buildquery)
- [Sugerencias sobre cómo controlar la navegación por facetas](#tips)
- [Navegación por facetas basada en los valores de un intervalo](#rangefacets)
- [Navegación por facetas basada en GeoPoints](#geofacets)
- [Prueba](#tryitout)

##Por qué usarla
Las aplicaciones de búsqueda más eficaces tienen varios modelos de interacción además de un cuadro de búsqueda. La navegación por facetas es un punto de entrada alternativo para búsqueda que supone una alternativa práctica a la escritura manual de expresiones de búsqueda complejas.

##Conceptos básicos

Si no está familiarizado con el desarrollo de búsquedas, la mejor manera de pensar en la navegación por facetas es que muestra las posibilidades de una búsqueda autodirigida. Es un tipo de experiencia de búsqueda en profundidad basada en filtros predefinidos, que se usa para restringir rápidamente los resultados de búsqueda mediante acciones de apuntar y hacer clic.

**Modelo de interacción**

La experiencia de búsqueda de la navegación por facetas es iterativa, por lo que comenzaremos por describirla como una secuencia de consultas que se despliega en respuesta a las acciones del usuario.

El punto de partida es una página de aplicación que proporciona navegación por facetas, normalmente situada en la periferia. La navegación por facetas suele tener una estructura de árbol con casillas para cada valor o texto en el que se puede hacer clic.

1.	Una consulta enviada a Búsqueda de Azure especifica la estructura de la navegación por facetas mediante uno o más parámetros de consulta de faceta. Por ejemplo, la consulta podría incluir `facet=Rating`, quizá con una opción `:values` o `:sort` para refinar aún más la presentación.
2.	La capa de presentación representa una página de búsqueda que proporciona navegación por facetas, usando las facetas especificadas en la solicitud.
3.	En una estructura de navegación por facetas dada que incluye el campo de valoración Rating, el usuario hace clic en "4" para indicar que solo se deben mostrar los productos con una valoración de 4 o superior. 
4.	En respuesta, la aplicación envía una consulta que incluye `$filter=Rating ge 4` 
5.	La capa de presentación actualiza la página para mostrar un conjunto de resultados reducido únicamente con los elementos que cumplen el nuevo criterio (en este caso, los productos con una valoración de 4 y superior).

Una faceta es un parámetro de consulta, pero no lo confunda con una entrada de consulta. Nunca se usa como criterio de selección en una consulta. En su lugar, piense en los parámetros de consulta de faceta como entradas a la estructura de navegación que se devuelven en la respuesta. Para cada parámetro de consulta de faceta que proporcione, Búsqueda de Azure evaluará cuántos documentos hay en los resultados parciales de cada valor de faceta.

Observe la expresión `$filter` en el paso 4. Se trata de un aspecto importante de la navegación por facetas. Aunque las facetas y los filtros son independientes de la API, necesitará ambos para proporcionar la experiencia que desea.

**Patrones de diseño**

En el código de aplicación, el patrón es usar parámetros de consulta de faceta para devolver la estructura de navegación por facetas junto con los resultados de las facetas, además de una expresión $filter que controla el evento de clic. Piense en la expresión `$filter` como en el código subyacente al recorte real de los resultados de búsqueda que se devuelven a la capa de presentación. En una faceta Colors dada, la posibilidad de hacer clic en el color Red se implementa mediante una expresión `$filter` que selecciona solo los elementos que tienen un color rojo.

**Conceptos básicos de consultas en Búsqueda de Azure**

En Búsqueda de Azure, una solicitud se especifica mediante uno o más parámetros de consulta (consulte [Buscar documentos](http://msdn.microsoft.com/library/azure/dn798927.aspx) para obtener una descripción de cada uno). Ninguno de los parámetros de consulta son obligatorios, pero debe tener al menos uno para que una consulta sea válida.

La precisión, entendida normalmente como la capacidad de filtrar los resultados irrelevantes, se consigue mediante una o ambas de estas expresiones:

- **search=**<br/> El valor de este parámetro constituye la expresión de búsqueda. Podría ser un fragmento de texto o una expresión de búsqueda compleja que incluye varios términos y operadores. En el servidor, las expresiones de búsqueda se usan para realizar búsquedas de texto completo que consultan en los campos de búsqueda del índice si hay términos coincidentes y devuelven los resultados en orden. Si establece `search` en NULL, la ejecución de la consulta se realiza en todo el índice (es decir, `search=*`). En este caso, los demás elementos de la consulta, como un `$filter` o el perfil de puntuación, serán los principales factores que afectan a qué documentos se devuelven (`($filter`) y en qué orden (`scoringProfile` o `$orderb`y).

- **$filter=**<br/> Un filtro es un mecanismo eficaz para limitar el tamaño de los resultados de búsqueda según los valores de atributos de documento específicos. Primero se evalúa una expresión `$filter` y después la lógica de uso de facetas que genera los valores disponibles y los correspondientes recuentos para cada valor.

Las expresiones de búsqueda complejas reducirán el rendimiento de la consulta. Siempre que sea posible, use expresiones de filtro bien construidas para aumentar la precisión y mejorar el rendimiento de las consultas.

Para entender mejor cómo los filtros agregan más precisión, compare una expresión de búsqueda compleja con una que incluya una expresión de filtro:

- `GET /indexes/hotel/docs?search=lodging budget +Seattle –motel +parking`

- `GET /indexes/hotel/docs?search=lodging&$filter=City eq ‘Seattle’ and Parking and Type ne ‘motel’`

Aunque ambas consultas son válidas, la segunda es mejor si está buscando hoteles que no sean moteles con estacionamiento en Seattle. La primera consulta se basa en las palabras específicas que se mencionan o no en campos de cadena tales como nombre, descripción u otros que contengan datos que se pueden buscar. La segunda consulta busca coincidencias precisas en datos estructurados y es probable que sea mucho más exacta.

En las aplicaciones que incluyen navegación por facetas, querrá asegurarse de que cada acción del usuario en una estructura de navegación por facetas vaya acompañada de una restricción de los resultados de búsqueda, que se logra mediante una expresión filter.

<a name="howtobuildit"></a>
##Cómo crearla

La navegación por facetas en Búsqueda de Azure se implementa en el código de aplicación que crea la solicitud, pero se basa en los elementos predefinidos del esquema.

En el índice de búsqueda está predefinido el atributo de índice `Facetable [true|false]`, que se establece en los campos seleccionados para habilitar o deshabilitar su uso en una estructura de navegación por facetas. Sin `"Facetable" = true`, un campo no se puede usar en la navegación por facetas.

En tiempo de consulta, el código de aplicación crea una solicitud que incluye `facet=[string]`, un parámetro de solicitud que proporciona el campo por el que se realizará la consulta por facetas. Una consulta puede tener varias facetas, como `&facet=color&facet=category&facet=rating`, separadas por un carácter de y comercial (&).

El código de aplicación también debe construir una expresión `$filter` para controlar los eventos de clic en la navegación por facetas. Una expresión `$filter` reduce los resultados de búsqueda, usando el valor de la faceta como criterio de filtro.

Búsqueda de Azure devuelve los resultados de búsqueda, según los términos especificados por el usuario, junto con las actualizaciones de la estructura de navegación por facetas. En Búsqueda de Azure, la navegación por facetas es una construcción de un solo nivel, con los valores de las facetas y los recuentos de cuántos resultados se encuentran para cada una.

La capa de presentación del código proporciona la experiencia del usuario. Debe enumerar las partes que constituyen la navegación por facetas, como la etiqueta, los valores, las casillas y el recuento. La API de REST de Búsqueda de Azure es independiente de la plataforma, así que puede usar cualquier lenguaje y plataforma que desee. Lo importante es incluir elementos de interfaz de usuario que admitan la actualización incremental, actualizando el estado de la interfaz de usuario cuando se seleccione cada faceta adicional.

En las secciones siguientes veremos con más detalle cómo crear cada parte a partir de la capa de presentación.

<a name="presentationlayer"></a>
##Crear la capa de presentación

Empezar a trabajar desde la capa de presentación puede ayudarle a descubrir requisitos que de otra forma podrían perderse, y comprender las características que son esenciales para la experiencia de búsqueda.

En términos de la navegación por facetas, la página o aplicación web muestra la estructura de la navegación por facetas, detecta la entrada del usuario en la página e inserta los elementos modificados.

En el caso de las aplicaciones web, normalmente se usa AJAX en la capa de presentación porque permite actualizar los cambios incrementales. También puede usar ASP.NET MVC o cualquier otra plataforma de visualización que puede conectarse a un servicio Búsqueda de Azure a través de HTTP. La aplicación de ejemplo a la que se hace referencia en este artículo (**AdventureWorks Catalog**) es una aplicación ASP.NET MVC.

El ejemplo siguiente, tomado del archivo **index.cshtml** de la aplicación de ejemplo, crea una estructura HTML dinámica para mostrar la navegación por facetas en la página de resultados de búsqueda. En el ejemplo, la navegación por facetas se integra en la página de resultados de búsqueda y aparece después de que el usuario envíe un término de búsqueda.

Observe que cada faceta tiene una etiqueta (Colors, Categories, Prices), un enlace a un campo con facetas (color, categoryName, listPrice) y un parámetro `.count`, que se usa para devolver el número de elementos encontrado en el resultado de esa faceta.

  ![][2]
 

> [AZURE.TIP] Al diseñar la página de resultados de búsqueda, no olvide agregar un mecanismo para borrar las facetas. Si usa casillas, los usuarios intuirán fácilmente cómo borrar los filtros. En otros diseños, puede que necesite un patrón de ruta de navegación u otro enfoque creativo. Por ejemplo, en la aplicación de ejemplo AdventureWorks Catalog, puede hacer clic en el título, AdventureWorks Catalog, para restablecer la página de búsqueda.

<a name="buildindex"></a>
##Crear el índice

El uso de facetas se habilita para cada campo en el índice mediante este atributo de índice: `"Facetable": true`. Todos los tipos de campo que podrían usarse en la navegación por facetas son `Facetable` de forma predeterminada. Entre estos tipos de campo se incluyen `Edm.String`, `Edm.DateTimeOffset`, y todos los tipos de campo numéricos (básicamente, todos los tipos de campo se pueden usar con facetas excepto `Edm.GeographyPoint`, que no se puede usar en la navegación por facetas).

Al crear un índice, el procedimiento recomendado para la navegación por facetas desactivar explícitamente el uso de facetas para los campos que nunca deben usarse como facetas. En concreto, los campos de cadena de valores singleton, tales como un identificador o el nombre del producto, deben establecerse en `"Facetable": false` para impedir su uso accidental (e ineficaz) en una exploración por facetas.

El siguiente es el esquema de la aplicación de ejemplo AdventureWorks Catalog (al que se han recortado algunos atributos para reducir el tamaño total):

 ![][3]
 
Observe que `Facetable` se ha desactivado para los campos de cadena que no deben usarse como facetas, como el nombre o el identificador. Desactivar el uso de facetas donde no es necesario ayuda a mantener un tamaño de índice pequeño y, por lo general, mejora el rendimiento.

> [AZURE.TIP] Como procedimiento recomendado, incluya el conjunto completo de atributos de índice para cada campo. Aunque `Facetable` está activado para casi todos los campos de forma predeterminada, establecer deliberadamente cada atributo puede ayudar a considerar detenidamente las implicaciones de cada decisión del esquema.

<a name="checkdata"></a>
##Comprobar la calidad de los datos 

Al desarrollar cualquier aplicación centrada en datos, la preparación de los datos suele ser una de las principales tareas. Las aplicaciones de búsqueda no son ninguna excepción. La calidad de los datos influye directamente en si la estructura de navegación por facetas se materializa tal y como se espera, así como en su eficacia para ayudarle a crear filtros que reduzcan el conjunto de resultados.

En Búsqueda de Azure, el corpus de búsqueda está formado por los documentos que rellenan un índice. Los documentos proporcionan los valores que se usan para calcular las facetas. Si desea usar la marca o el precio como facetas, cada documento debe contener valores de *BrandName* y *ProductPrice* que sean válidos, coherentes y productivos como opción de filtro.

Este es un pequeño recordatorio de lo que se debe pulir:

- Para cada campo que desee usar como faceta, pregúntese si contiene valores que son adecuados como filtros en búsquedas autodirigidas. Los valores deben ser breves, descriptivos y suficientemente distintivos para ofrecer una opción clara entre las opciones de la competencia.
- Errores ortográficos o valores casi coincidentes. Si usa Color como faceta y los valores de campo incluyen Naranja y Nraanja (una palabra incorrecta), una faceta basada en el campo Color seleccionará ambas.
- La mezcla de mayúsculas y minúsculas en el texto también puede causar estragos en la navegación por facetas, porque naranja y Naranja aparecen como dos valores diferentes. 
- Las versiones en singular y plural del mismo valor pueden producir una faceta diferente para cada una.

Como puede imaginar, la diligencia en la preparación de los datos es un aspecto esencial de una navegación por facetas eficiente.

<a name="buildquery"></a>
##Crear la consulta

El código que se escribe para crear consultas debe especificar todas las partes de una consulta válida, incluidas las expresiones de búsqueda, las facetas o los filtros de puntuación (todo lo que se usa para formular una solicitud). En esta sección, exploraremos cómo encajan las facetas en una consulta y cómo se usan los filtros con facetas para entregar un conjunto de resultados reducido.

Un ejemplo suele ser una buena manera de comenzar. En el ejemplo siguiente, tomado del archivo **CatalogSearch.cs**, se crea una solicitud que crea una navegación por facetas basada en Color, Category y Price.

Observe que las facetas son una parte integral de esta aplicación de ejemplo. La experiencia de búsqueda en AdventureWorks Catalog está diseñada en torno a los filtros y la navegación por facetas. Esto es evidente por el lugar destacado que ocupa la navegación por facetas en la página. La aplicación de ejemplo incluye los parámetros URI de las facetas (color, categoría, precios) como propiedades en el método Search (tal y como se construye en la aplicación de ejemplo).

  ![][4]
 
Un parámetro de consulta de faceta se establece en un campo y, según el tipo de datos, se puede parametrizar aún más con una lista delimitada por comas que incluya `count:<integer>`, `sort:<>`, `intervals:<integer>` y `values:<list>`. Se admiten listas de valores para datos numéricos cuando se establecen intervalos. Consulte [Buscar documentos (API de Búsqueda de Azure)](http://msdn.microsoft.com/library/azure/dn798927.aspx) para obtener más información sobre su uso.

Además de las facetas, la solicitud que la aplicación formula también debe generar filtros para reducir el conjunto de documentos candidatos en función de una selección de valores de faceta. Para una tienda de bicicletas, la navegación por facetas proporciona pistas para preguntas como "¿Qué colores, fabricantes y tipos de bicicletas hay disponibles" y filtra por respuestas a preguntas como "qué bicicletas exactas son rojas, son bicicletas de montaña o están en este intervalo de precios".

Cuando un usuario hace clic en "Red" para indicar que solo se deben mostrar productos de color rojo, la consulta siguiente que la aplicación envía incluiría `$filter=Color eq ‘Red’`.

## Procedimientos recomendados para la navegación por facetas

La siguiente lista resume algunos procedimientos recomendados.

- **Precisión**<br/> Use filtros. Si confía solamente en expresiones de búsqueda, la lematización podría provocar que se devuelva un documento que no tiene un valor de faceta preciso en ninguno de sus campos. 

- **Campos de destino**<br/> En la profundización por facetas, normalmente solo querrá incluir los documentos que tienen el valor de faceta en un campo específico (con faceta), no en cualquiera de todos los campos en los que se puede buscar. Agregar un filtro refuerza el campo de destino y dirige el servicio para que busque un valor coincidente solo en el campo con faceta.

- **Eficiencia del índice**<br/> Si la aplicación usa navegación por facetas exclusivamente (es decir, ningún cuadro de búsqueda), puede marcar el campo como `searchable=false`, `facetable=true` para generar un índice más compacto. Además, la indización se produce solo en los valores de faceta completos, sin saltos de palabras ni indización de los componentes de un valor de varias palabras.

- **Rendimiento**<br/> Los filtros restringen el conjunto de documentos candidatos para la búsqueda y los excluyen de la clasificación. Si tiene un conjunto grande de documentos, usar una profundización por facetas muy selectiva suele proporcionar un rendimiento mucho mayor.


<a name="tips"></a>
##Sugerencias sobre cómo controlar la navegación por facetas

La siguiente es una lista de sugerencias con información sobre problemas específicos.

**Agregar etiquetas para cada campo en la navegación por facetas**

Las etiquetas suelen definirse en el código o formulario HTML (**index.cshtml*** en la aplicación de ejemplo). No hay ninguna API en Búsqueda de Azure para las etiquetas de navegación por facetas u otros tipos de metadatos.

**Definir qué campos se pueden usar como facetas**

Recuerde que el esquema del índice determina qué campos están disponibles para usar como facetas. Suponiendo que un campo se pueda usar para la búsqueda por facetas, la consulta especifica qué campos se van a usar para la búsqueda por facetas. El campo que se va a usar para la búsqueda por facetas proporciona los valores que aparecerán debajo de la etiqueta.

Los valores que aparecen debajo de cada etiqueta se recuperan del índice. Por ejemplo, si el campo de faceta es *Color*, los valores disponibles para un filtrado adicional serán los valores de ese campo (Red, Black, etc.).

Solo para los valores Numeric y DateTime, puede establecer explícitamente valores en el campo de faceta (por ejemplo, `facet=Rating,values:1|2|3|4|5`). Se permite una lista de valores para estos tipos de campo para simplificar la separación de los resultados de faceta en intervalos contiguos (intervalos basados en valores numéricos o en períodos de tiempo).

**Recortar los resultados de faceta**

Los resultados de faceta son documentos que se encuentran en los resultados de búsqueda y que coinciden con un término de faceta. En el ejemplo siguiente, en los resultados de búsqueda de *cloud computing*, 254 elementos también tienen *internal specification* como tipo de contenido. Los elementos no son necesariamente excluyentes mutuamente. Si un elemento cumple los criterios de ambos filtros, se contabiliza en cada uno de ellos. Esto es posible cuando se usan facetas en campos `Collection(Edm.String)`, que suelen usarse para implementar el etiquetado de documentos.

		Search term: "cloud computing"
		Content type
		   Internal specification (254)
		   Video (10) 

En general, si encuentra que los resultados de faceta son demasiado grandes de forma persistente, se recomienda agregar más filtros, tal y como se explica en las secciones anteriores, para ofrecer a los usuarios de la aplicación más opciones para delimitar la búsqueda.

**Limitar los elementos en la navegación por facetas**

Para cada campo de faceta del árbol de navegación, hay un límite predeterminado de 10 valores. Este valor predeterminado tiene sentido en las estructuras de navegación porque mantiene la lista de valores en un tamaño fácil de administrar. Puede invalidar el valor predeterminado y asignar un valor a count.

- `&facet=city,count:5` especifica que solo se devuelven como resultado de la faceta las 5 primeras ciudades encontradas en los principales resultados. Dado un término de búsqueda de "aeropuerto" y 32 coincidencias, si la consulta especifica `&facet=city,count:5`, solo se incluyen en los resultados de la faceta las primeras cinco ciudades con más documentos en los resultados de búsqueda.

Observe la diferencia entre resultados de búsqueda y resultados de faceta. Los resultados de búsqueda son todos los documentos que coinciden con la consulta. Los resultados de faceta son las coincidencias para cada valor de la faceta. En el ejemplo, los resultados de búsqueda incluirán los nombres de ciudades que no están en la lista de clasificación de faceta (5 en nuestro ejemplo). Los resultados que se filtran mediante la navegación por facetas son visibles para el usuario cuando se borran las facetas o eligen otras facetas además de City.

> [AZURE.NOTE] Analizar `count` cuando hay más de un tipo puede ser confuso. En la tabla siguiente se ofrece un breve resumen de cómo se usa el término en la API de Búsqueda de Azure así como código de ejemplo y documentación.

- `@colorFacet.count`<br/> En el código de presentación, verá un parámetro count en la faceta, que se usa para mostrar el número de resultados de faceta. En los resultados de faceta, count indica el número de documentos que coinciden con el término de faceta o con el intervalo de facetas.

- `&facet=City,count:12`<br/> En una consulta de faceta, puede establecer count en un valor. El valor predeterminado es 10, pero puede establecer uno inferior o superior. Al establecer `count:12` se obtienen las 12 principales coincidencias en los resultados de faceta por recuento de documentos.

- "`@odata.count`"<br/> En la respuesta de la consulta, este valor indica el número de elementos coincidentes en los resultados de búsqueda. Por término medio, es mayor que la suma de todos los resultados de faceta combinados, debido a la presencia de elementos que coinciden con el término de búsqueda pero con ninguno de los valores de faceta.


**Niveles de navegación por facetas**

Como ya se mencionó, no hay compatibilidad directa para facetas anidadas en una jerarquía. De forma predeterminada, la navegación por facetas solo admite un nivel de filtros. Sin embargo, existen soluciones alternativas. Puede codificar una estructura jerárquica de facetas en una `Collection(Edm.String)` con un punto de entrada por jerarquía. La implementación de esta solución queda fuera del ámbito de este artículo, pero puede leer acerca de las colecciones en [OData con ejemplos](http://msdn.microsoft.com/library/ff478141.aspx).

**Validar los campos**

Si crea la lista de facetas de manera dinámica basándose en la entrada de usuarios que no son de confianza, debe validar que los nombres de los campos con facetas son válidos o escribir los nombres entre caracteres de escape al generar las direcciones URL usando `Uri.EscapeDataString()` en. NET, o su equivalente en la plataforma que elija.

**Recuentos en los resultados de faceta**

Al agregar un filtro a una consulta por facetas, quizás quiera conservar la instrucción facet (por ejemplo, `facet=Rating&$filter=Rating ge 4`). Técnicamente, facet=Rating no es necesaria, pero conservarla devuelve los recuentos de los valores de faceta para las valoraciones de 4 y superiores. Por ejemplo, si un usuario hace clic en "4" y la consulta incluye un filtro mayor o igual que "4", se devuelven los recuentos de valoraciones de 4 y superiores.

**Cómo afecta el particionamiento a los recuentos de faceta**

En determinadas circunstancias, puede que vea que los recuentos de faceta no coinciden con los conjuntos de resultados (consulte [Navegación por facetas en Búsqueda de Azure (publicación del foro)](https://social.msdn.microsoft.com/Forums/azure/06461173-ea26-4e6a-9545-fbbd7ee61c8f/faceting-on-azure-search?forum=azuresearch)).

Los recuentos de faceta pueden ser incorrectos debido a la arquitectura de particionamiento. Cada índice de búsqueda tiene varias particiones, y cada una notifica las N primeras facetas por recuento de documentos, que después se combina en un único resultado. Si algunas particiones tienen muchos valores coincidentes, mientras que otros tienen menos, verá que algunos valores de faceta faltan o se contabilizan con un número inferior en los resultados.

Aunque este comportamiento podría cambiar en cualquier momento, si se produce hoy puede solucionarlo estableciendo artificialmente count:<number> en un número muy grande para forzar que se notifique el valor completo de cada partición. Si el valor de count: es mayor o igual que el número de valores únicos en el campo, se garantizan resultados precisos. Sin embargo, si el recuento de documentos es muy alto afecta al rendimiento, por lo que debe usar esta opción con prudencia.

<a name="rangefacets"></a>
##Navegación por facetas basada en un intervalo de valores

El uso de facetas en intervalos es un requisito habitual de las aplicaciones de búsqueda. Se admiten intervalos para datos numéricos y valores de fecha y hora. Puede leer más acerca de cada enfoque en [Buscar documentos (API de Búsqueda de Azure)](http://msdn.microsoft.com/library/azure/dn798927.aspx).

Búsqueda de Azure simplifica la construcción de intervalos mediante dos enfoques de cálculo de intervalos. En ambos enfoques, Búsqueda de Azure crea los intervalos adecuados con las entradas proporcionadas. Por ejemplo, si especifica valores de intervalo de 10|20|30, creará automáticamente los intervalos 0 -10, 10-20, y 20-30. La aplicación de ejemplo quita los intervalos que están vacíos.

**Enfoque 1: usar el parámetro interval**<br/> Para establecer las facetas de precio en incrementos de 10 dólares, especificaría: `&facet=price,interval:10`


**Enfoque 2: usar una lista de valores**<br/> Para datos numéricos, puede usar una lista de valores. Tenga en cuenta el intervalo de facetas de listPrice, que se representa como sigue:

  ![][5]

El intervalo se especifica en el archivo **CatalogSearch.cs** usando una lista de valores:

    facet=listPrice,values:10|25|100|500|1000|2500

Cada intervalo se genera usando 0 como punto de partida, un valor de la lista como extremo y, después, se recorta el intervalo anterior para crear diferentes intervalos. Búsqueda de Azure hará esto como parte de la navegación por facetas. No es necesario escribir código para estructurar cada intervalo.

### Crear un filtro para intervalos de facetas ###

Para filtrar los documentos según un intervalo seleccionado por el usuario, puede usar los operadores de filtro `"ge"` y `"lt"` en una expresión de dos partes que define los extremos del intervalo. Por ejemplo, si el usuario elige el intervalo de 10 a 25, el filtro sería `$filter=listPrice ge 10 and listPrice lt 25`.

En la aplicación de ejemplo, la expresión de filtro usa los parámetros **priceFrom** y **priceTo** para establecer los extremos. El método **BuildFilter** en **CatalogSearch.cs** contiene la expresión de filtro que proporciona los documentos dentro de un intervalo.

  ![][6]

<a name="geofacets"></a>
##Navegación por facetas basada en GeoPoints

Es habitual ver filtros que ayudan a elegir una tienda, un restaurante o un destino en función de su proximidad a la ubicación actual. Aunque este tipo de filtro podría ser similar a la navegación por facetas, en realidad es solo un filtro. Lo mencionamos aquí para aquellos que buscan específicamente consejos de implementación para ese problema de diseño concreto.

Hay dos funciones geoespaciales en Búsqueda de Azure, **geo.distance** y **geo.intersects**.

- La función **geo.distance** devuelve la distancia en kilómetros entre dos puntos, siendo uno un campo y el otro una constante que se pasa como parte del filtro. 

- La función **geo.intersects** devuelve true si un punto determinado se encuentra dentro de un polígono determinado, donde el punto es un campo y el polígono se especifica como una lista constante de coordenadas que se pasa como parte del filtro.

Puede encontrar ejemplos de filtros en [Sintaxis de expresiones de OData (Búsqueda de Azure)](http://msdn.microsoft.com/library/azure/dn798921.aspx). Para obtener más información acerca de la búsqueda geoespacial, consulte [Crear una aplicación de búsqueda geoespacial en Búsqueda de Azure](search-create-geospatial.md).

<a name="tryitout"></a>
##Prueba

La demostración de Búsqueda de Azure con Adventure Works en Codeplex contiene los ejemplos a los que se hace referencia en este artículo. Cuando trabaje con los resultados de búsqueda, vigile los posibles cambios en las direcciones URL en la construcción de la consulta. Esta aplicación anexa facetas al URI a medida que las selecciona.

1.	Configure la aplicación de ejemplo para usar la clave de API y la dirección URL del servicio. 

	Observe el esquema que se define en el archivo Program.cs del proyecto CatalogIndexer. Especifica los campos que se pueden usar como faceta para color, listPrice, size, weight, categoryName y modelName. Solo algunos de ellos (color, listPrice, categoryName) se implementan en realidad en la navegación por facetas.

3.	Ejecute la aplicación.

	Al principio, solo es visible el cuadro de búsqueda. Puede hacer clic en el botón Search para obtener todos los resultados o puede escribir un término de búsqueda.

	![][7]
 
4.	Escriba un término de búsqueda, por ejemplo, bike, y haga clic en **Search**. La consulta se ejecuta rápidamente.
 
	Con los resultados de búsqueda se devuelve también una estructura de navegación por facetas. En la dirección URL, las facetas para Colors, Categories y Prices son NULL. En la página de resultados de búsqueda, la estructura de navegación por facetas incluye los recuentos de cada resultado de faceta.

	 ![][8]
 
5.	Haga clic en un color, categoría e intervalo de precios. Las facetas son NULL en una búsqueda inicial, pero a medida que toman valores, en los resultados de búsqueda se recortan los elementos que ya no coinciden. Observe que el URI recoge los cambios de la consulta.

	![][9]
 
6.	Para borrar la consulta por facetas para poder probar comportamientos de consulta diferente, haga clic en **AdventureWorks Catalog** en la parte superior de la página.

	![][10]
 
<a name="nextstep"></a>
##Paso siguiente

Para probar sus conocimientos, puede agregar un campo de faceta para *modelName*. El índice ya está configurado para esta faceta, por lo que no se necesita ningún cambio en el índice. Sin embargo, necesitará modificar el código HTML para incluir una nueva faceta para Models y agregar el campo de faceta al constructor de la consulta.

Para obtener más información sobre los principios de diseño de la navegación por facetas, recomendamos los siguientes vínculos:

- [Diseñar para la búsqueda por facetas](http://www.uie.com/articles/faceted_search/)
- [Patrones de diseño: Navegación por facetas](http://alistapart.com/article/design-patterns-faceted-navigation)

También puede ver el vídeo de [profundización en Búsqueda de Azure](http://channel9.msdn.com/Events/TechEd/Europe/2014/DBI-B410). En el minuto 45:25, hay una demostración sobre cómo implementar las facetas.

<!--Anchors-->
[How to build it]: #howtobuildit
[Build the presentation layer]: #presentationlayer
[Build the index]: #buildindex
[Check for data quality]: #checkdata
[Build the query]: #buildquery
[Tips on how to control faceted navigation]: #tips
[Faceted navigation based on range values]: #rangefacets
[Faceted navigation based on GeoPoints]: #geofacets
[Try it out]: #tryitout

<!--Image references-->
[1]: ./media/search-faceted-navigation/Facet-1-slide.PNG
[2]: ./media/search-faceted-navigation/Facet-2-CSHTML.PNG
[3]: ./media/search-faceted-navigation/Facet-3-schema.PNG
[4]: ./media/search-faceted-navigation/Facet-4-SearchMethod.PNG
[5]: ./media/search-faceted-navigation/Facet-5-Prices.PNG
[6]: ./media/search-faceted-navigation/Facet-6-buildfilter.PNG
[7]: ./media/search-faceted-navigation/Facet-7-appstart.png
[8]: ./media/search-faceted-navigation/Facet-8-appbike.png
[9]: ./media/search-faceted-navigation/Facet-9-appbikefaceted.png
[10]: ./media/search-faceted-navigation/Facet-10-appTitle.png

<!--Link references-->
[Designing for Faceted Search]: http://www.uie.com/articles/faceted_search/
[Design Patterns: Faceted Navigation]: http://alistapart.com/article/design-patterns-faceted-navigation
[Create your first application]: search-create-first-solution.md
[OData expression syntax (Azure Search)]: http://msdn.microsoft.com/library/azure/dn798921.aspx
[Create a geospatial search application in Azure Search]: search-create-geospatial.md
[Azure Search Adventure Works Demo]: https://azuresearchadventureworksdemo.codeplex.com/
[http://www.odata.org/documentation/odata-version-2-0/overview/]: http://www.odata.org/documentation/odata-version-2-0/overview/
[Faceting on Azure Search forum post]: ../faceting-on-azure-search.md?forum=azuresearch
[Search Documents (Azure Search API)]: http://msdn.microsoft.com/library/azure/dn798927.aspx

 

<!---HONumber=AcomDC_0224_2016-->