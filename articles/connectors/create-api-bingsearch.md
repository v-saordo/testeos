<properties
	pageTitle="Incorporación de la API Búsqueda de Bing en PowerApps Enterprise o aplicaciones lógicas | Microsoft Azure"
	description="Información general de la API Búsqueda de Bing con parámetros de la API de REST"
	services=""
    suite=""
	documentationCenter="" 
	authors="MandiOhlinger"
	manager="erikre"
	editor=""
	tags="connectors"/>

<tags
   ms.service="multiple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="03/02/2016"
   ms.author="mandia"/>

# Introducción a la API Búsqueda de Bing 
Conéctese a Búsqueda de Bing para buscar noticias, vídeos y mucho más. La API de Búsqueda de Bing se puede utilizar desde:

- Aplicaciones lógicas 
- PowerApps

> [AZURE.SELECTOR]
- [Aplicaciones lógicas](../articles/connectors/create-api-bingsearch.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-bingsearch.md)


Con Búsqueda de Bing, puede hacer lo siguiente:

- Compilar el flujo de negocio en función de los datos obtenidos de la búsqueda. 
- Usar acciones para buscar imágenes, noticias, etc. Estas acciones obtienen una respuesta y luego dejan el resultado a disposición de otras acciones. Por ejemplo, puede buscar un vídeo y luego usar Twitter para publicar ese vídeo en una fuente de Twitter.
- Incorporación de la API Búsqueda de Bing a PowerApps Enterprise. Así los usuarios pueden usar esta API en sus aplicaciones. 

Para obtener información sobre cómo agregar una API en PowerApps Enterprise, vaya a [Registro de una API en PowerApps](../power-apps/powerapps-register-from-available-apis.md).

Para agregar una operación en aplicaciones lógicas, consulte [Creación de una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Desencadenadores y acciones
Búsqueda de Bing incluye las siguientes acciones. No hay desencadenadores.

Desencadenadores | Acciones
--- | ---
None | <ul><li>Buscar en web</li><li>Buscar vídeos</li><li>Buscar imágenes</li><li>Buscar noticias</li><li>Buscar relacionados</li><li>Buscar ortografías</li><li>Buscar todo</li></ul>

Todas las API admiten datos en formato JSON y XML.

## Agregar configuración adicional
Al agregar Búsqueda de Bing a PowerApps Enterprise, se solicitará una clave de cuenta. Si no tiene una clave de Búsqueda de Bing, utilice la [oferta gratuita de Búsqueda de Bing](https://datamarket.azure.com/dataset/bing/search) para obtener una.


## Referencia de API de REST de Swagger
Se aplica a la versión: 1.0.

### Buscar en web 
Recupera los sitios web de una búsqueda de Bing. ```GET: /Web```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|query|cadena|yes|query|Ninguna |Texto que se va a buscar (ejemplo, 'xbox')|
|maxResult|integer|no|query|Ninguna |El número máximo de resultados que se devolverán|
|startOffset|integer|no|query| Ninguna|Número de resultados que se van a omitir|
|adultContent|cadena|no|query|Ninguna |Filtro de contenido para adultos. Valores válidos: <ul><li>Off (Desactivado)</li><li>Moderate (Moderado)</li><li>Strict (Estricto)</li></ul>|
|market|cadena|no|query|Ninguna |Mercado o región para restringir la búsqueda (ejemplo, es-ES)|
|longitude|número|no|query| Ninguna|Longitud (coordenada este y oeste) para restringir la búsqueda (ejemplo, 47.603450)|
|latitude|número|no|query| Ninguna|Latitud (coordenada norte/sur) para restringir la búsqueda (ejemplo, -122.329696)|
|webFileType|cadena|no|query|Ninguna |Tipo de archivo para restringir la búsqueda (ejemplo, 'DOC')|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Buscar vídeos 
Recupera vídeos de una búsqueda de Bing. ```GET: /Video```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|query|cadena|yes|query|Ninguna |Texto que se va a buscar (ejemplo, 'xbox')|
|maxResult|integer|no|query| Ninguna|El número máximo de resultados que se devolverán|
|startOffset|integer|no|query|Ninguna |Número de resultados que se van a omitir|
|adultContent|cadena|no|query|Ninguna |Filtro de contenido para adultos. Valores válidos: <ul><li>Off (Desactivado)</li><li>Moderate (Moderado)</li><li>Strict (Estricto)</li></ul>|
|market|cadena|no|query|Ninguna |Mercado o región para restringir la búsqueda (ejemplo, es-ES)|
|longitude|número|no|query|Ninguna |Longitud (coordenada este y oeste) para restringir la búsqueda (ejemplo, 47.603450)|
|latitude|número|no|query|Ninguna |Latitud (coordenada norte/sur) para restringir la búsqueda (ejemplo, -122.329696)|
|videoFilters|cadena|no|query|Ninguna |Filtro de búsqueda en función del tamaño, el aspecto, el color, el estilo, la cara o cualquier combinación de estos. Valores válidos: <ul><li>Duration:Short</li><li>Duration:Medium</li><li>Duration:Long</li><li>Aspect:Standard</li><li>Aspect:Widescreen</li><li>Resolution:Low</li><li>Resolution:Medium</li><li>Resolution:High</li></ul> <br/><br/>Por ejemplo: "Duration:Short+Resolution:High"|
|videoSortBy|cadena|no|query|Ninguna |Criterio de ordenación de los resultados. Valores válidos: <ul><li>Date (Fecha)</li><li>Relevance (Relevancia)</li></ul> <p>El orden de clasificación por fecha implica un orden descendente.</p>|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Buscar imágenes    
Recupera las imágenes de una búsqueda de Bing. ```GET: /Image```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|query|cadena|yes|query|Ninguna |Texto que se va a buscar (ejemplo, 'xbox')|
|maxResult|integer|no|query|Ninguna |El número máximo de resultados que se devolverán|
|startOffset|integer|no|query|Ninguna |Número de resultados que se van a omitir|
|adultContent|cadena|no|query|Ninguna |Filtro de contenido para adultos. Valores válidos: <ul><li>Off (Desactivado)</li><li>Moderate (Moderado)</li><li>Strict (Estricto)</li></ul>|
|market|cadena|no|query|Ninguna |Mercado o región para restringir la búsqueda (ejemplo, es-ES)|
|longitude|número|no|query| Ninguna|Longitud (coordenada este y oeste) para restringir la búsqueda (ejemplo, 47.603450)|
|latitude|número|no|query|Ninguna |Latitud (coordenada norte/sur) para restringir la búsqueda (ejemplo, -122.329696)|
|imageFilters|cadena|no|query|Ninguna |Filtro de búsqueda en función del tamaño, el aspecto, el color, el estilo, la cara o cualquier combinación de estos. Valores válidos: <ul><li>Size:Small</li><li>Size:Medium</li><li>Size:Large</li><li>Size:Width:[ancho]</li><li>Size:Height:[alto]</li><li>Aspect:Square</li><li>Aspect:Wide</li><li>Aspect:Tall</li><li>Color:Color</li><li>Color:Monochrome</li><li>Style:Photo</li><li>Style:Graphics</li><li>Face:Face</li><li>Face:Portrait</li><li>Face:Other</li></ul><br/><br/>Por ejemplo: "Size:Small+Aspect:Square"|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Buscar noticias    
Recupera los resultados de noticias de una búsqueda de Bing. ```GET: /News```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|query|cadena|yes|query|Ninguna |Texto que se va a buscar (ejemplo, 'xbox')|
|maxResult|integer|no|query|Ninguna |El número máximo de resultados que se devolverán|
|startOffset|integer|no|query| Ninguna|Número de resultados que se van a omitir|
|adultContent|cadena|no|query|Ninguna |Filtro de contenido para adultos. Valores válidos: <ul><li>Off (Desactivado)</li><li>Moderate (Moderado)</li><li>Strict (Estricto)</li></ul>|
|market|cadena|no|query|Ninguna |Mercado o región para restringir la búsqueda (ejemplo, es-ES)|
|longitude|número|no|query|Ninguna |Longitud (coordenada este y oeste) para restringir la búsqueda (ejemplo, 47.603450)|
|latitude|número|no|query|Ninguna |Latitud (coordenada norte/sur) para restringir la búsqueda (ejemplo, -122.329696)|
|newsSortBy|cadena|no|query| Ninguna|Criterio de ordenación de los resultados. Valores válidos: <ul><li>Date (Fecha)</li><li>Relevance (Relevancia)</li></ul> <p>El orden de clasificación por fecha implica un orden descendente.</p>|
|newsCategory|cadena|no|query| |Categoría de noticias para restringir la búsqueda (ejemplo: 'rt\_Business')|
|newsLocationOverride|cadena|no|query|Ninguna |Invalida la detección de ubicación de Bing. Este parámetro solo es aplicable en el mercado de es-ES. El formato de entrada es US./<state /> (ejemplo: "US.WA")|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Buscar ortografías    
Recupera sugerencias de ortografía. ```GET: /SpellingSuggestions```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|query|cadena|yes|query| Ninguna|Texto que se va a buscar (ejemplo, 'xbox')|
|maxResult|integer|no|query|Ninguna |El número máximo de resultados que se devolverán|
|startOffset|integer|no|query| Ninguna|Número de resultados que se van a omitir|
|adultContent|cadena|no|query|Ninguna |Filtro de contenido para adultos. Valores válidos: <ul><li>Off (Desactivado)</li><li>Moderate (Moderado)</li><li>Strict (Estricto)</li></ul>|
|market|cadena|no|query| Ninguna|Mercado o región para restringir la búsqueda (ejemplo, es-ES)|
|longitude|número|no|query|Ninguna |Longitud (coordenada este y oeste) para restringir la búsqueda (ejemplo, 47.603450)|
|latitude|número|no|query|Ninguna |Latitud (coordenada norte/sur) para restringir la búsqueda (ejemplo, -122.329696)|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Buscar relacionados    
Recupera los resultados de la búsqueda relacionados de una búsqueda de Bing. ```GET: /RelatedSearch```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|query|cadena|yes|query|Ninguna |Texto que se va a buscar (ejemplo, 'xbox')|
|maxResult|integer|no|query|Ninguna |El número máximo de resultados que se devolverán|
|startOffset|integer|no|query| Ninguna|Número de resultados que se van a omitir|
|adultContent|cadena|no|query|Ninguna |Filtro de contenido para adultos. Valores válidos: <ul><li>Off (Desactivado)</li><li>Moderate (Moderado)</li><li>Strict (Estricto)</li></ul>|
|market|cadena|no|query|Ninguna |Mercado o región para restringir la búsqueda (ejemplo, es-ES)|
|longitude|número|no|query|Ninguna |Longitud (coordenada este y oeste) para restringir la búsqueda (ejemplo, 47.603450)|
|latitude|número|no|query| Ninguna|Latitud (coordenada norte/sur) para restringir la búsqueda (ejemplo, -122.329696)|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Buscar todo    
Recupera todos los sitios web, vídeos, imágenes, etc. de una búsqueda de Bing. ```GET: /CompositeSearch```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|query|cadena|yes|query|Ninguna |Texto que se va a buscar (ejemplo, 'xbox')|
|maxResult|integer|no|query|Ninguna |El número máximo de resultados que se devolverán|
|startOffset|integer|no|query|Ninguna |Número de resultados que se van a omitir|
|adultContent|cadena|no|query|Ninguna |Filtro de contenido para adultos. Valores válidos: <ul><li>Off (Desactivado)</li><li>Moderate (Moderado)</li><li>Strict (Estricto)</li></ul>|
|market|cadena|no|query|Ninguna |Mercado o región para restringir la búsqueda (ejemplo, es-ES)|
|longitude|número|no|query|Ninguna |Longitud (coordenada este y oeste) para restringir la búsqueda (ejemplo, 47.603450)|
|latitude|número|no|query|Ninguna |Latitud (coordenada norte/sur) para restringir la búsqueda (ejemplo, -122.329696)|
|webFileType|cadena|no|query|Ninguna |Tipo de archivo para restringir la búsqueda (ejemplo, 'DOC')|
|videoFilters|cadena|no|query|Ninguna |Filtro de búsqueda en función del tamaño, el aspecto, el color, el estilo, la cara o cualquier combinación de estos. Valores válidos: <ul><li>Duration:Short</li><li>Duration:Medium</li><li>Duration:Long</li><li>Aspect:Standard</li><li>Aspect:Widescreen</li><li>Resolution:Low</li><li>Resolution:Medium</li><li>Resolution:High</li></ul> <br/><br/>Por ejemplo: "Duration:Short+Resolution:High"|
|videoSortBy|cadena|no|query|Ninguna |Criterio de ordenación de los resultados. Valores válidos: <ul><li>Date (Fecha)</li><li>Relevance (Relevancia)</li></ul> <p>El orden de clasificación por fecha implica un orden descendente.</p>|
|imageFilters|cadena|no|query|Ninguna |Filtro de búsqueda en función del tamaño, el aspecto, el color, el estilo, la cara o cualquier combinación de estos. Valores válidos: <ul><li>Size:Small</li><li>Size:Medium</li><li>Size:Large</li><li>Size:Width:[ancho]</li><li>Size:Height:[alto]</li><li>Aspect:Square</li><li>Aspect:Wide</li><li>Aspect:Tall</li><li>Color:Color</li><li>Color:Monochrome</li><li>Style:Photo</li><li>Style:Graphics</li><li>Face:Face</li><li>Face:Portrait</li><li>Face:Other</li></ul><br/><br/>Por ejemplo: "Size:Small+Aspect:Square"|
|newsSortBy|cadena|no|query|Ninguna |Criterio de ordenación de los resultados. Valores válidos: <ul><li>Date (Fecha)</li><li>Relevance (Relevancia)</li></ul> <p>El orden de clasificación por fecha implica un orden descendente.</p>|
|newsCategory|cadena|no|query|Ninguna |Categoría de noticias para restringir la búsqueda (ejemplo: 'rt\_Business')|
|newsLocationOverride|cadena|no|query|Ninguna |Invalida la detección de ubicación de Bing. Este parámetro solo es aplicable en el mercado de es-ES. El formato de entrada es US./<state /> (ejemplo: "US.WA")|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


## Definiciones de objeto

#### WebResultModel: resultados de búsqueda en web de Bing

|Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|Título|cadena|no|
|Descripción|cadena|no|
|DisplayUrl|cadena|no|
|Id|cadena|no|
|FullUrl|cadena|no|

#### VideoResultModel: resultados de la búsqueda de vídeo de Bing

|Nombre de propiedad | Tipo de datos |Obligatorio |
|---|---|---|
|Título|cadena|no|
|DisplayUrl|cadena|no|
|Id|cadena|no|
|MediaUrl|cadena|no|
|Tiempo de ejecución|integer|no|
|Miniatura|not defined|no|

#### ThumbnailModel: propiedades de vista en miniatura del elemento multimedia

|Nombre de propiedad | Tipo de datos |Obligatorio |
|---|---|---|
|MediaUrl|cadena|no|
|ContentType|cadena|no|
|Ancho|integer|no|
|Alto|integer|no|
|FileSize|integer|no|

#### ImageResultModel: resultados de búsqueda de imagen de Bing

|Nombre de propiedad | Tipo de datos |Obligatorio |
|---|---|---|
|Título|cadena|no|
|DisplayUrl|cadena|no|
|Id|cadena|no|
|MediaUrl|cadena|no|
|SourceUrl|cadena|no|
|Miniatura|not defined|no|

#### NewsResultModel: resultados de búsqueda de noticias de Bing

|Nombre de propiedad | Tipo de datos |Obligatorio |
|---|---|---|
|Título|cadena|no|
|Descripción|cadena|no|
|DisplayUrl|cadena|no|
|Id|cadena|no|
|Origen|cadena|no|
|Date|cadena|no|

#### SpellResultModel: resultados de sugerencias de ortografía de Bing

|Nombre de propiedad | Tipo de datos |Obligatorio |
|---|---|---|
|Id|cadena|no|
|Valor|cadena|no|

#### RelatedSearchResultModel: resultados de búsqueda relacionados de Bing

|Nombre de propiedad | Tipo de datos |Obligatorio |
|---|---|---|
|Título|cadena|no|
|Id|cadena|no|
|BingUrl|cadena|no|

#### CompositeSearchResultModel: resultados de búsqueda compuesta de Bing

|Nombre de propiedad | Tipo de datos |Obligatorio |
|---|---|---|
|WebResultsTotal|integer|no|
|ImageResultsTotal|integer|no|
|VideoResultsTotal|integer|no|
|NewsResultsTotal|integer|no|
|SpellSuggestionsTotal|integer|no|
|WebResults|array|no|
|ImageResults|array|no|
|VideoResults|array|no|
|NewsResults|array|no|
|SpellSuggestionResults|array|no|
|RelatedSearchResults|array|no|

## Pasos siguientes

Después de agregar la API de Búsqueda de Bing a PowerApps Enterprise, [conceda permisos a los usuarios](../power-apps/powerapps-manage-api-connection-user-access.md) para usarla en sus aplicaciones.

[Creación de una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

<!---HONumber=AcomDC_0302_2016-->