<properties 
    pageTitle="Conexión de DocumentDB con Búsqueda de Azure mediante indizadores | Microsoft Azure" 
    description="En este artículo se muestra cómo usar el indizador de Búsqueda de Azure con DocumentDB como origen de datos."
    services="documentdb" 
    documentationCenter="" 
    authors="AndrewHoh" 
    manager="jhubbard" 
    editor="mimig"/>

<tags 
    ms.service="documentdb" 
    ms.devlang="rest-api" 
    ms.topic="article" 
    ms.tgt_pltfrm="NA" 
    ms.workload="data-services" 
    ms.date="02/01/2016" 
    ms.author="anhoh"/>

#Conexión de DocumentDB con Búsqueda de Azure mediante indizadores

Si desea mejorar las experiencias de búsqueda en los datos de DocumentDB, use el indizador de Búsqueda de Azure para dicha base de datos. En este artículo mostraremos cómo integrar Azure DocumentDB con Búsqueda de Azure sin necesidad de escribir código alguno para mantener la infraestructura de indización.

Para establecer esta opción, debe [configurar una cuenta de Búsqueda de Azure](../search/search-get-started.md#start-with-the-free-service) (no es necesario actualizar a la búsqueda estándar) y, a continuación, llamar a la [API de REST de Búsqueda de Azure](https://msdn.microsoft.com/library/azure/dn798935.aspx) para crear un **origen de datos** de DocumentDB y un **indizador** para dicho origen de datos.

##<a id="Concepts"></a>Conceptos del indizador de Búsqueda de Azure

Búsqueda de Azure admite la creación y administración de orígenes de datos (incluida DocumentDB), así como de indizadores que se usan en dichos orígenes.

Un **origen de datos** especifica los datos que es necesario indizar, las credenciales para obtener acceso a estos y las directivas que posibilitan que Búsqueda de Azure identifique cambios en los datos de forma eficaz (por ejemplo, los documentos modificados o eliminados dentro de una colección). El origen de datos se define como un recurso independiente para que puedan usarlo múltiples indizadores.

Un **indizador** describe cómo fluyen los datos desde el origen de datos a un índice de búsqueda de destino. Debe planear la creación de un indizador para cada combinación de origen de datos e índice de destino. Si bien varios indizadores pueden escribir en el mismo índice, cada indizador únicamente puede escribir en un solo índice. Un indizador se usa para:

- Realizar una copia única de los datos para rellenar un índice.
- Sincronizar un índice con los cambios del origen de datos en una programación. La programación forma parte de la definición del indizador.
- Invocar actualizaciones a petición para un índice según sea necesario. 

##<a id="CreateDataSource"></a>Paso 1: Creación de un origen de datos

Emita una solicitud HTTP POST para crear un nuevo origen de datos en el servicio Búsqueda de Azure que incluya los siguientes encabezados de solicitud.
    
    POST https://[Search service name].search.windows.net/datasources?api-version=[api-version]
    Content-Type: application/json
    api-key: [Search service admin key]

`api-version` es obligatorio Entre los valores válidos se incluye `2015-02-28` o una versión posterior.

El cuerpo de la solicitud contiene la definición del origen de datos, que debe incluir los siguientes campos:

- **nombre**: el nombre del origen de datos.

- **tipo**: use `documentdb`.

- **credenciales**:

    - **connectionString**: obligatorio. Especifique la información de conexión a Azure DocumentDB con el formato siguiente: `AccountEndpoint=<DocumentDB endpoint url>;AccountKey=<DocumentDB auth key>;Database=<DocumentDB database id>`

- **contenedor**:

    - **nombre**: obligatorio. Especifique la colección de DocumentDB que se va a indizar. 

    - **consulta**: opcional. Puede especificar una consulta para acoplar un documento JSON arbitrario en un esquema plano que Búsqueda de Azure pueda indizar.

- **dataChangeDetectionPolicy**: opcional. Consulte la [directiva de detección de cambios de datos](#DataChangeDetectionPolicy) más adelante.

- **dataDeletionDetectionPolicy**: opcional. Consulte la [directiva de detección de eliminación de datos](#DataDeletionDetectionPolicy) más adelante.

###<a id="DataChangeDetectionPolicy"></a>Captura de documentos modificados

El fin de una directiva de detección de cambios de datos es identificar de forma eficaz los elementos de datos que han cambiado. Actualmente, solo es compatible la directiva `High Water Mark` que usa la propiedad de marca de tiempo de última modificación `_ts` proporcionada por DocumentDB, especificada de la siguiente forma:

    { 
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "_ts" 
    } 

También tendrá que agregar `_ts` a la proyección y la cláusula `WHERE` para la consulta. Por ejemplo:

    SELECT s.id, s.Title, s.Abstract, s._ts FROM Sessions s WHERE s._ts > @HighWaterMark


###<a id="DataDeletionDetectionPolicy"></a>Captura de documentos eliminados

Cuando se eliminan filas de la tabla de origen, también debe eliminar dichas filas del índice de búsqueda. El fin de una directiva de detección de eliminación de datos es identificar eficazmente los elementos de datos eliminados. Actualmente, solo es compatible la directiva `Soft Delete` (la eliminación se indica con algún tipo de marca), especificada de la siguiente forma:

    { 
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the property that specifies whether a document was deleted", 
        "softDeleteMarkerValue" : "the value that identifies a document as deleted" 
    }

> [AZURE.NOTE] Si usa una proyección personalizada, deberá incluir la propiedad en la cláusula SELECT.

###<a id="CreateDataSourceExample"></a>Ejemplo de cuerpo de solicitud

En el ejemplo siguiente se crea un origen de datos con una consulta personalizada y sugerencias de directiva:

    {
        "name": "mydocdbdatasource",
        "type": "documentdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myDocDbEndpoint.documents.azure.com;AccountKey=myDocDbAuthKey;Database=myDocDbDatabaseId"
        },
        "container": {
            "name": "myDocDbCollectionId",
            "query": "SELECT s.id, s.Title, s.Abstract, s._ts FROM Sessions s WHERE s._ts > @HighWaterMark" 
        },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        },
        "dataDeletionDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName": "isDeleted",
            "softDeleteMarkerValue": "true"
        }
    }

###Response

Si el origen de datos se crea correctamente, recibirá una respuesta HTTP 201 que indica que se ha creado.

##<a id="CreateIndex"></a>Paso 2: Creación de un índice

Si aún no tiene un índice de Búsqueda de Azure de destino, créelo. Puede crearlo desde la [interfaz de usuario del Portal de Azure](../search/search-get-started.md#test-service-operations) o mediante la [API de creación de índices](https://msdn.microsoft.com/library/azure/dn798941.aspx).

	POST https://[Search service name].search.windows.net/indexes?api-version=[api-version]
	Content-Type: application/json
	api-key: [Search service admin key]


Asegúrese de que el esquema del índice de destino es compatible con el de los documentos JSON de origen o el resultado de la proyección de consultas personalizada.

###Figura A: asignación entre tipos de datos de JSON y de Búsqueda de Azure

| TIPO DE DATOS DE JSON|	TIPOS DE CAMPOS DE ÍNDICE DE DESTINO COMPATIBLES|
|---|---|
|Booleano|Edm.Boolean, Edm.String|
|Números que parecen enteros|Edm.Int32, Edm.Int64, Edm.String|
|Números que parecen puntos flotantes|Edm.Double, Edm.String|
|Cadena|Edm.String|
|Matrices de tipos primitivos, por ejemplo, "a", "b", "c" |Collection(Edm.String)|
|Cadenas que parecen fechas| Edm.DateTimeOffset, Edm.String|
|Objetos GeoJSON, por ejemplo, {"tipo": "Punto", "coordenadas": [long, lat]} | Edm.GeographyPoint |
|Otros objetos JSON|N/D|

###<a id="CreateIndexExample"></a>Ejemplo de cuerpo de solicitud

En el ejemplo siguiente se crea un índice con un campo de identificador y de descripción:

    {
       "name": "mysearchindex",
       "fields": [{
         "name": "id",
         "type": "Edm.String",
         "key": true,
         "searchable": false
       }, {
         "name": "description",
         "type": "Edm.String",
         "filterable": false,
         "sortable": false,
         "facetable": false,
         "suggestions": true
       }]
     }

###Response

Si el índice se crea correctamente, recibirá una respuesta HTTP 201 que indica que se ha creado.

##<a id="CreateIndexer"></a>Paso 3: Creación de un indizador

Para crear un indizador dentro de un servicio de Búsqueda de Azure, use una solicitud HTTP POST con los siguientes encabezados:
    
    POST https://[Search service name].search.windows.net/indexers?api-version=[api-version]
    Content-Type: application/json
    api-key: [Search service admin key]

El cuerpo de la solicitud contiene la definición del indizador, que debe incluir los siguientes campos:

- **nombre**: obligatorio. El nombre del indizador.

- **dataSourceName**: obligatorio. El nombre de un origen de datos existente.

- **targetIndexName**: obligatorio. El nombre de un índice existente.

- **programación**: opcional. Consulte la [programación de indización](#IndexingSchedule) a continuación.

###<a id="IndexingSchedule"></a>Ejecución de indizadores en una programación

Un indizador puede especificar opcionalmente una programación. Si existe una programación, el indizador se ejecutará de forma periódica de acuerdo con la misma. Una programación tiene los siguientes atributos:

- **intervalo**: obligatorio. Valor de duración que especifica un intervalo o período durante el que se ejecuta el indizador. El intervalo mínimo permitido es de 5 minutos y el máximo de un día. Debe tener el formato de un valor "dayTimeDuration" XSD (subconjunto restringido de un valor de [duración ISO 8601](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration)). El patrón de este es: `P(nD)(T(nH)(nM))`. Ejemplos: `PT15M` para cada 15 minutos, `PT2H` para cada 2 horas. 

- **startTime**: obligatorio. Valor de fecha y hora UTC que especifica cuándo debería empezar a ejecutarse el indizador.

###<a id="CreateIndexerExample"></a>Ejemplo de cuerpo de solicitud

En el ejemplo siguiente se crea un indizador que copia datos de la colección a la que hace referencia el origen de datos `myDocDbDataSource` en el índice `mySearchIndex` de acuerdo con una programación que empieza el 1 de enero de 2015 UTC y se ejecuta cada hora.

    {
        "name" : "mysearchindexer",
        "dataSourceName" : "mydocdbdatasource",
        "targetIndexName" : "mysearchindex",
        "schedule" : { "interval" : "PT1H", "startTime" : "2015-01-01T00:00:00Z" }
    }

###Response

Si el indizador se crea correctamente, recibirá una respuesta HTTP 201 que indica que se ha creado.

##<a id="RunIndexer"></a>Paso 4: Ejecución de un indizador

Además de ejecutarse periódicamente según una programación, un indizador también puede invocarse a petición mediante la emisión de la siguiente solicitud HTTP POST:

    POST https://[Search service name].search.windows.net/indexers/[indexer name]/run?api-version=[api-version]
    api-key: [Search service admin key]

###Response

Si el indizador se invoca correctamente, recibirá una respuesta HTTP 202 que indica que se ha aceptado.

##<a name="GetIndexerStatus"></a>Paso 5: Obtención del estado del indizador

Puede emitir una solicitud HTTP GET para recuperar el estado actual y el historial de ejecución de un indizador:

    GET https://[Search service name].search.windows.net/indexers/[indexer name]/status?api-version=[api-version]
    api-key: [Search service admin key]

###Response

Se devolverá una respuesta HTTP 200 de aceptado junto con un cuerpo de respuesta que contiene información sobre el estado general del indizador, la última invocación de este, así como el historial de invocaciones recientes del mismo (si existe).

La respuesta debe ser similar a la siguiente:

    {
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2014-11-26T03:37:18.853Z",
            "endTime":"2014-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
         },
        "executionHistory":[ {
            "status":"success",
             "errorMessage":null,
            "startTime":"2014-11-26T03:37:18.853Z",
            "endTime":"2014-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        }]
    }

El historial de ejecución contiene como máximo las 50 ejecuciones completadas más recientemente en orden cronológico inverso (la ejecución más reciente aparece en primer lugar).

##<a name="NextSteps"></a>Pasos siguientes

¡Enhorabuena! En este artículo ha aprendido a integrar Azure DocumentDB con Búsqueda de Azure usando el indizador para dicha base de datos.

 - Para más información sobre Azure DocumentDB, consulte la [página del servicio DocumentDB](https://azure.microsoft.com/services/documentdb/).

 - Para obtener más información sobre Búsqueda de Azure, consulte la [página del servicio Búsqueda](https://azure.microsoft.com/services/search/).
 

<!---HONumber=AcomDC_0204_2016-->