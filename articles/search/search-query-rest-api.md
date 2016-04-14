<properties
    pageTitle="Realización de una consulta en un índice de Búsqueda de Azure con la API de REST | Microsoft Azure | Servicio de búsqueda hospedado en la nube"
    description="Cree una consulta de búsqueda en Búsqueda de Azure y use los parámetros de búsqueda para filtrar y ordenar los resultados de búsqueda."
    services="search"
    documentationCenter=""
	authors="ashmaka"
/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="03/09/2016"
    ms.author="ashmaka"/>

# Realización de una consulta al índice de Búsqueda de Azure con la API de REST
> [AZURE.SELECTOR]
- [Información general](search-query-overview.md)
- [Explorador de búsqueda](search-explorer.md)
- [Fiddler](search-fiddler.md)
- [.NET](search-query-dotnet.md)
- [REST](search-query-rest-api.md)

En este artículo se muestra cómo realizar una consulta a un índice con la [API de REST de Búsqueda de Azure](https://msdn.microsoft.com/library/azure/dn798935.aspx). Antes de comenzar este tutorial, debe haber [creado ya un índice de Búsqueda de Azure](search-create-index-rest-api.md) y [haberlo rellenado con datos](search-import-data-rest-api.md).

## I. Identificación de la clave de API de consulta del servicio de Búsqueda de Azure
Un componente clave de cada operación de búsqueda en la API de REST de Búsqueda de Azure es la *clave de API* que se generó para el servicio que ha aprovisionado. Tener una clave válida genera la confianza, solicitud a solicitud, entre la aplicación que envía la solicitud y el servicio que se encarga de ella.

1. Para buscar las claves de API del servicio debe iniciar sesión en el [Portal de Azure](https://portal.azure.com/)
2. Vaya a la hoja de servicio de Búsqueda de Azure
3. Haga clic en el icono "Claves"

El servicio tendrá *claves de administración* y *claves de consulta*.

 - Sus *claves de administración* principal y secundaria permiten conceder derechos completos para todas las operaciones, incluida la capacidad para administrar el servicio, crear y eliminar índices, indexadores y orígenes de datos. Existen dos claves, de forma que puede usar la clave secundaria si decide volver a generar la clave principal y viceversa.
 - Las *claves de consulta* conceden acceso de solo lectura a índices y documentos y normalmente se distribuyen entre las aplicaciones cliente que emiten solicitudes de búsqueda.

Para consultar un índice, puede utilizar una de las claves de consulta. Las claves de administración también se pueden utilizar para las consultas, pero debe utilizar una clave de consulta en el código de aplicación ya que esto se adapta mejor al [principio de privilegios mínimos](https://en.wikipedia.org/wiki/Principle_of_least_privilege).

## II. Formulación de la consulta
Hay dos maneras de [realizar búsquedas en el índice mediante la API de REST](https://msdn.microsoft.com/library/azure/dn798927.aspx). Una manera es emitir una solicitud HTTP POST donde se definirán los parámetros de consulta en un objeto JSON en el cuerpo de solicitud. La otra manera es emitir una solicitud HTTP GET en la que se definirán los parámetros de consulta en la URL de la solicitud. Tenga en cuenta que la solicitud POST tiene unos [límites más flexibles](https://msdn.microsoft.com/library/azure/dn798927.aspx) en relación con el tamaño de los parámetros de consulta en comparación con los de la solicitud GET. Por este motivo, se recomienda usar la solicitud POST a menos que haya circunstancias especiales en las que utilizar la solicitud GET sea más adecuado.

Tanto para la solicitud POST como para la GET,deberá proporcionar el *nombre del servicio*, el *nombre del índice*, así como la *versión adecuada de la API* (la versión actual de la API es `2015-02-28` en el momento de publicar este documento) en la URL de la solicitud. Para la solicitud GET, la *cadena de consulta* al final de la dirección URL debe proporcionar los parámetros de consulta. Consulte a continuación el formato de dirección URL:

    https://[service name].search.windows.net/indexes/[index name]/docs?[query string]&api-version=2015-02-28

El formato de la solicitud POST es el mismo, pero solo con la versión de API en los parámetros de la cadena de consulta.

#### Tipos de consultas

Búsqueda de Azure ofrece muchas opciones para crear consultas extremadamente eficaces. Los dos tipos principales de consultas que usará son `search` y `filter`. Una consulta `search` busca uno o más términos en todos los campos _habilitados para búsqueda_ del índice, y funciona tal y como cabría esperar de un motor de búsqueda como Google o Bing. Una consulta `filter` evalúa una expresión booleana en todos los campos _filtrables_ de un índice. A diferencia de las consultas `search`, las consultas `filter` buscan el contenido exacto de un campo, lo que significa que distinguen mayúsculas de minúsculas en el caso de los campos de cadena.

Puede usar los filtros y las búsquedas conjuntamente o por separado. Si los usa conjuntamente, el filtro se aplica primero a todo el índice y, después, se realiza la búsqueda en los resultados del filtro. Por lo tanto, los filtros pueden ser una técnica útil para mejorar el rendimiento porque reducen el conjunto de documentos en los que se debe procesar la consulta de búsqueda.

La sintaxis de las expresiones de filtro es un subconjunto del [lenguaje de filtro de OData](https://msdn.microsoft.com/library/azure/dn798921.aspx). Para las consultas de búsqueda, puede usar la [sintaxis simplificada](https://msdn.microsoft.com/library/azure/dn798920.aspx) o la [sintaxis de consulta de Lucene](https://msdn.microsoft.com/library/azure/mt589323.aspx).

Para obtener más información acerca de los distintos parámetros de una consulta, visite [Buscar documentos](https://msdn.microsoft.com/library/azure/dn798927.aspx). También hay algunas consultas de ejemplo a continuación.

#### Consultas de ejemplo

Presentamos algunas consultas de ejemplo en un índice llamado "hoteles". Estas consultas se muestran en el formato de las solicitudes GET y POST.

Busque en todo el índice el término "presupuesto" y devuelva solo el campo `hotelName`:

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=budget&$select=hotelName&api-version=2015-02-28

POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2015-02-28
{
    "search": "budget",
    "select": "hotelName"
}
```

Aplique un filtro al índice para buscar hoteles con una tarifa inferior a 150 dólares por noche, y devolver `hotelId` y `description`:

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&$filter=baseRate lt 150&$select=hotelId,description&api-version=2015-02-28

POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2015-02-28
{
    "search": "*",
    "filter": "baseRate lt 150",
    "select": "hotelId,description"
}
```

Busque en todo el índice, ordene por un campo específico (`lastRenovationDate`) en orden descendente, tome los dos primeros resultados y muestre solo `hotelName` y `lastRenovationDate`:

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&$top=2&$orderby=lastRenovationDate desc&$select=hotelName,lastRenovationDate&api-version=2015-02-28

POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2015-02-28
{
    "search": "*",
    "orderby": "lastRenovationDate desc",
    "select": "hotelName,lastRenovationDate",
    "top": 2
}
```

## III. Envío de la solicitud HTTP
Ahora que ha formulado la consulta como parte de la dirección URL de la solicitud HTTP (para GET) o del cuerpo de la solicitud (para POST), puede definir los encabezados de solicitud y enviar la consulta.

#### Solicitudes y encabezados de solicitud
Debe definir dos encabezados de solicitud para GET o tres para POST:
1. El encabezado `api-key` debe establecerse en la clave de consulta que encontró anteriormente en el paso I. Tenga en cuenta que también puede usar una clave de administración como el encabezado `api-key`, pero se recomienda usar una clave de consulta, ya que esta concede exclusivamente acceso de solo lectura a los índices y documentos.
2. El encabezado `Accept` debe establecerse en `application/json`.
3. Solo para POST, el encabezado `Content-Type` debe establecerse también en `application/json`.

Consulte a continuación una solicitud HTTP GET creada para buscar en el índice "hoteles" mediante la API de REST de Búsqueda de Azure con una consulta sencilla que busque el término "motel":

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=motel&api-version=2015-02-28
Accept: application/json
api-key: [query key]
```

Aquí aparece la misma consulta de ejemplo, pero esta vez utilizando HTTP POST:

```
POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2015-02-28
Content-Type: application/json
Accept: application/json
api-key: [query key]

{
    "search": "motel"
}
```

Una solicitud de consulta correcta dará como resultado un código de estado de `200 OK` y los resultados de la búsqueda se devuelven como JSON en el cuerpo de la respuesta. Aquí se muestra el aspecto de los resultados de la consulta anterior, suponiendo que el índice "hoteles" se ha rellenado con los datos de ejemplo de [Importación de datos en Búsqueda de Azure con la API de REST](search-import-data-rest-api.md) (tenga en cuenta que se ha aplicado formato a JSON para mayor claridad).

```JSON
{
    "value": [
        {
            "@search.score": 0.59600675,
            "hotelId": "2",
            "baseRate": 79.99,
            "description": "Cheapest hotel in town",
            "description_fr": "Hôtel le moins cher en ville",
            "hotelName": "Roach Motel",
            "category": "Budget",
            "tags":["motel", "budget"],
            "parkingIncluded": true,
            "smokingAllowed": true,
            "lastRenovationDate": "1982-04-28T00:00:00Z",
            "rating": 1,
            "location": {
                "type": "Point",
                "coordinates": [-122.131577, 49.678581],
                "crs": {
                    "type":"name",
                    "properties": {
                        "name": "EPSG:4326"
                    }
                }
            }
        }
    ]
}
```

Para obtener más información, visite la sección "Respuesta" de [Buscar documentos](https://msdn.microsoft.com/library/azure/dn798927.aspx). Para obtener más información sobre otros códigos de estado HTTP que se devuelven en caso de error, consulte [Códigos de estado HTTP (Búsqueda de Azure)](https://msdn.microsoft.com/library/azure/dn798925.aspx).

<!---HONumber=AcomDC_0309_2016-->