<properties
    pageTitle="Importación de datos en Búsqueda de Azure con la API de REST | Microsoft Azure | Servicio de búsqueda hospedado en la nube"
    description="Carga de datos en un índice de Búsqueda de Azure con la API de REST."
    services="search"
    documentationCenter=""
    authors="ashmaka"
    manager=""
    editor=""
    tags=""/>

<tags
    ms.service="search"
    ms.devlang="rest-api"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="03/09/2016"
    ms.author="ashmaka"/>

# Importación de datos en Búsqueda de Azure con la API de REST
> [AZURE.SELECTOR]
- [Información general](search-what-is-data-import.md)
- [Portal](search-import-data-portal.md)
- [.NET](search-import-data-dotnet.md)
- [REST](search-import-data-rest-api.md)
- [Indexadores](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)


En este artículo mostraré cómo usar la [API de REST de Búsqueda de Azure](https://msdn.microsoft.com/library/azure/dn798935.aspx) para importar datos en un índice de Búsqueda de Azure. Antes de comenzar este tutorial, debe haber [creado ya un índice de Búsqueda de Azure](search-create-index-rest-api.md).

Para poder insertar documentos en el índice mediante la API de REST, deberá emitir una solicitud HTTP POST al punto de conexión de la dirección URL del índice. El cuerpo de la solicitud HTTP es un objeto JSON que contiene los documentos que se van a agregar, modificar o eliminar.

## I. Identificación de la clave de API de administración del servicio de Búsqueda de Azure
Al emitir solicitudes HTTP en el servicio mediante la API de REST, *cada* solicitud de API debe incluir la clave de API que se generó para el servicio de Búsqueda que aprovisionó. Tener una clave válida genera la confianza, solicitud a solicitud, entre la aplicación que envía la solicitud y el servicio que se encarga de ella.

1. Para buscar las claves de API del servicio debe iniciar sesión en el [Portal de Azure](https://portal.azure.com/)
2. Vaya a la hoja de servicio de Búsqueda de Azure
3. Haga clic en el icono "Claves"

El servicio tendrá *claves de administración* y *claves de consulta*.

  - Sus *claves de administración* principal y secundaria permiten conceder derechos completos para todas las operaciones, incluida la capacidad para administrar el servicio, crear y eliminar índices, indexadores y orígenes de datos. Existen dos claves, de forma que puede usar la clave secundaria si decide volver a generar la clave principal y viceversa.
  - Las *claves de consulta* conceden acceso de solo lectura a índices y documentos y normalmente se distribuyen entre las aplicaciones cliente que emiten solicitudes de búsqueda.

Para importar datos en un índice, puede usar su clave de administración principal o la secundaria.

## II. Elección de la acción de indexación que va a usar
Al utilizar la API de REST, emitirá solicitudes HTTP POST con cuerpos de solicitud JSON a la dirección URL del punto de conexión del índice de Búsqueda de Azure. El objeto JSON en el cuerpo de la solicitud HTTP contiene una única matriz JSON denominada "value" que contiene los objetos JSON que representan los documentos que le gustaría agregar, actualizar o eliminar del índice.

Cada objeto JSON de la matriz de "value" representa un documento que se va a indexar. Cada uno de estos objetos contiene la clave del documento y especifica la acción de indexación deseada (cargar, combinar, eliminar, etc.). Dependiendo de cuál de las acciones siguientes elija, se deberán incluir solo ciertos campos para cada documento:

@search.action | Descripción | Campos necesarios para cada documento | Notas
--- | --- | --- | ---
`upload` | Una acción `upload` es similar a un "upsert" donde se insertará el documento si es nuevo y se actualizará/reemplazará si ya existe. | la clave, además de cualquier otro campo que desee definir | Al actualizar o reemplazar un documento existente, cualquier campo que no esté especificado en la solicitud tendrá su campo establecido en `null`. Esto ocurre incluso cuando el campo se ha establecido previamente en un valor que no sea nulo.
`merge` | Permite actualizar un documento existente con los campos especificados. Si el documento no existe en el índice, se producirá un error en la combinación. | la clave, además de cualquier otro campo que desee definir | Cualquier campo que se especifica en una combinación reemplazará al campo existente en el documento. Aquí se incluyen los campos de tipo `Collection(Edm.String)`. Por ejemplo, si el documento contiene un campo `tags` con el valor `["budget"]` y ejecuta una combinación con el valor `["economy", "pool"]` para `tags`, el valor final del campo `tags` será `["economy", "pool"]`. No será `["budget", "economy", "pool"]`.
`mergeOrUpload` | Esta acción se comporta como `merge` si ya existe un documento con la clave especificada en el índice. Si el documento no existe, se comporta como `upload` con un nuevo documento. | la clave, además de cualquier otro campo que desee definir |- 
`delete` | Permite quitar el documento especificado del índice. | solo la clave | Los campos que especifique se ignorarán excepto el campo clave. Si desea quitar un campo individual de un documento, use `merge` en su lugar y simplemente establezca el campo explícitamente con el valor null.

## III. Construcción de la solicitud HTTP y el cuerpo de la solicitud
Ahora que ha recopilado los valores de campo necesarios para las acciones de índice, está listo para construir la solicitud HTTP real y el cuerpo de solicitud JSON para importar sus datos.

#### Solicitudes y encabezados de solicitud
En la dirección URL, deberá proporcionar el nombre del servicio, el nombre del índice ("hoteles" en este caso), así como la versión adecuada de la API (la versión actual de la API es `2015-02-28` en el momento de publicar este documento). Deberá definir los encabezados de solicitud `Content-Type` y `api-key`. Para el último, use una de las claves de administración del servicio.

    POST https://[search service].search.windows.net/indexes/hotels/docs/index?api-version=2015-02-28
    Content-Type: application/json
    api-key: [admin key]

#### Cuerpo de la solicitud

```JSON
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
            "@search.action": "mergeOrUpload",
            "hotelId": "3",
            "baseRate": 129.99,
            "description": "Close to town hall and the river"
        },
        {
            "@search.action": "delete",
            "hotelId": "6"
        }
    ]
}
```

En este caso, estamos usando `upload`, `mergeOrUpload` y `delete` como acciones de búsqueda.

Se supone que este índice "hoteles" de ejemplo ya está relleno con varios documentos. Observe cómo no tuvimos que especificar todos los campos posibles del documento al utilizar `mergeOrUpload` y cómo especificamos solo la clave del documento (`hotelId`) al usar `delete`.

Además, tenga en cuenta que solo se puede incluir hasta 1000 documentos (o 16 MB) en una única solicitud de indexación.

## IV. Descripción del código de respuesta HTTP
#### 200
Después de enviar una solicitud de indexación correcta recibirá una respuesta HTTP con código de estado de `200 OK`. El cuerpo JSON de la respuesta HTTP será el siguiente:

```JSON
{
    "value": [
        {
            "key": "unique_key_of_document",
            "status": true,
            "errorMessage": null
        },
        ...
    ]
}
```

#### 207
Se devolverá un código de estado de `207` solo con que un elemento no se indexe correctamente. El cuerpo JSON de la respuesta HTTP contendrá información sobre los documentos que no se hayan indexado correctamente.

```JSON
{
    "value": [
        {
            "key": "unique_key_of_document",
            "status": false,
            "errorMessage": "The search service is too busy to process this document. Please try again later."
        },
        ...
    ]
}
```

> [AZURE.NOTE] Esto suele significar que la carga en el servicio de búsqueda está alcanzando un punto en el que las solicitudes de indexación empezarán a devolver respuestas `503`. En este caso, es muy recomendable que interrumpa el código de cliente y espere antes de volver a intentarlo. Esto le dará al sistema un tiempo para recuperarse, lo cual hará aumentar las posibilidades de que las solicitudes futuras se realicen correctamente. Si lo vuelve a intentar demasiado pronto solo logrará prolongar la situación.

#### 429
Se devolverá un código de estado de `429` si se ha superado la cuota de número de documentos por índice.

#### 503
Se devolverá un código de estado de `503` si ninguno de los elementos de la solicitud se han indexado correctamente. Este error significa que el sistema está sobrecargado y no se puede procesar la solicitud en este momento.

> [AZURE.NOTE] En este caso, es muy recomendable que interrumpa el código de cliente y espere antes de volver a intentarlo. Esto le dará al sistema un tiempo para recuperarse, lo cual hará aumentar las posibilidades de que las solicitudes futuras se realicen correctamente. Si lo vuelve a intentar demasiado pronto solo logrará prolongar la situación.

Para obtener más información sobre las acciones de documentos y las respuestas de éxito o error, consulte [Agregar, actualizar o eliminar documentos](https://msdn.microsoft.com/library/azure/dn798930.aspx). Para obtener más información sobre otros códigos de estado HTTP que se devuelven en caso de error, consulte [Códigos de estado HTTP (Búsqueda de Azure)](https://msdn.microsoft.com/library/azure/dn798925.aspx).

## Pasos siguientes
Después de rellenar el índice de Búsqueda de Azure, estará listo para iniciar la emisión de consultas para buscar documentos. Consulte [Realización de una consulta al índice de Búsqueda de Azure con la API de REST](search-query-rest-api.md) para obtener más información.

<!---HONumber=AcomDC_0309_2016-->