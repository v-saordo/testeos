<properties
	pageTitle="Uso de Fiddler para evaluar y probar las API de REST de Búsqueda de Azure | Microsoft Azure | Servicio de búsqueda hospedado en la nube"
	description="Usar Fiddler para comprobar la disponibilidad de Búsqueda de Azure y probar las API de REST sin código."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="02/18/2016"
	ms.author="heidist"/>

# Usar Fiddler para evaluar y probar las API de REST de Búsqueda de Azure
> [AZURE.SELECTOR]
- [Overview](search-query-overview.md)
- [Search Explorer](search-explorer.md)
- [Fiddler](search-fiddler.md)
- [.NET](search-query-dotnet.md)
- [REST](search-query-rest-api.md)

En este procedimiento se explica cómo usar Fiddler, disponible como [descarga gratuita de Telerik](http://www.telerik.com/fiddler), para emitir solicitudes HTTP y ver las respuestas usando la API de REST de Búsqueda de Azure sin tener que escribir código. Búsqueda de Azure es un servicio de búsqueda hospedado en la nube en Microsoft Azure, fácilmente programable a través de API de .NET y REST. Las API de REST del servicio Búsqueda de Azure están documentadas en [MSDN](https://msdn.microsoft.com/library/azure/dn798935.aspx).

En los siguientes pasos, podrá crear un índice, cargar documentos, consultar el índice y, a continuación, consultar el sistema en busca de información de servicio.

Para completar estos pasos, necesitará un servicio Búsqueda de Azure y `api-key`. Consulte [Crear un servicio Búsqueda de Azure en el portal](search-create-service-portal.md) para obtener instrucciones sobre cómo empezar.

## Creación de un índice

1. Inicie Fiddler. En el menú **Archivo**, desactive **Capturar tráfico** para ocultar actividad HTTP irrelevante que no está relacionada con la tarea actual.

3. En la pestaña **Compositor**, formulará una solicitud similar a la siguiente captura de pantalla:

  	![][1]

2. Seleccione **PUT**.

3. Escriba una dirección URL que especifica la dirección URL del servicio, los atributos de la solicitud y la versión de la API. Algunos indicadores que tener en cuenta:
   + Use HTTPS como el prefijo.
   + El atributo de solicitud es "/índices/hoteles". Esto le indica a la búsqueda que cree un índice llamado "hoteles".
   + La versión de la API se escribe en minúsculas y se especifica como "?api-version=2015-02-28". Las versiones de API son importantes porque Búsqueda de Azure implementa actualizaciones regularmente. En raras ocasiones, una actualización de servicio puede presentar un cambio innovador en la API. Por este motivo, Búsqueda de Azure requiere una versión de la API en cada solicitud para que tenga el control total sobre cuál se usa.

    La URL completa debe ser similar al siguiente ejemplo.

         https://my-app.search.windows.net/indexes/hotels?api-version=2015-02-28

4.	Especifique el encabezado de la solicitud al reemplazar el host y la clave de API por valores que son válidos para su servicio.

        User-Agent: Fiddler
        host: my-app.search.windows.net
        content-type: application/json
        api-key: 1111222233334444

5.	En el cuerpo de la solicitud, pegue los campos que componen la definición del índice.

         {
        "name": "hotels",  
        "fields": [
          {"name": "hotelId", "type": "Edm.String", "key":true, "searchable": false},
          {"name": "baseRate", "type": "Edm.Double"},
          {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
          {"name": "hotelName", "type": "Edm.String"},
          {"name": "category", "type": "Edm.String"},
          {"name": "tags", "type": "Collection(Edm.String)"},
          {"name": "parkingIncluded", "type": "Edm.Boolean"},
          {"name": "smokingAllowed", "type": "Edm.Boolean"},
          {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
          {"name": "rating", "type": "Edm.Int32"},
          {"name": "location", "type": "Edm.GeographyPoint"}
         ]
        }

6.	Haga clic en **Ejecutar**.

En unos segundos verá una respuesta HTTP 201 en la lista de sesiones, que indica que el índice se creó correctamente.

Si obtiene HTTP 504, compruebe que la URL especifique HTTPS. Si se muestra el error HTTP 400 o 404, compruebe el cuerpo de la solicitud para verificar que no haya errores al copiar/pegar. Un HTTP 403 indica normalmente que hay un problema con la clave de API (es una clave no válida o un problema de sintaxis sobre cómo se específica la clave de API).

## Carga de documentos

En la pestaña **Compositor**, se verá su solicitud para enviar documentos como a continuación. El cuerpo de la solicitud contiene los datos de búsqueda de cuatro hoteles.

   ![][2]

1. Seleccione **POST**.

2.	Escriba una URL que comience con HTTPS, seguida de la URL del servicio y, después, "/indexes/<'indexname'>/docs/index?api-version=2015-02-28". La URL completa debe ser similar al siguiente ejemplo.

        https://my-app.search.windows.net/indexes/hotels/docs/index?api-version=2015-02-28

3.	El encabezado de la solicitud debe ser igual que antes. Recuerde que reemplazó el host y la clave de API con valores que son válidos para el servicio.

        User-Agent: Fiddler
        host: my-app.search.windows.net
        content-type: application/json
        api-key: 1111222233334444

4.	El cuerpo de la solicitud contiene cuatro documentos que se van agregar al índice de hoteles.

        {
        "value": [
        {
        	"@search.action": "upload",
        	"hotelId": "1",
        	"baseRate": 199.0,
        	"description": "Best hotel in town",
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
        	"@search.action": "upload",
        	"hotelId": "3",
        	"baseRate": 279.99,
        	"description": "Surprisingly expensive",
        	"hotelName": "Dew Drop Inn",
        	"category": "Bed and Breakfast",
        	"tags": ["charming", "quaint"],
        	"parkingIncluded": true,
        	"smokingAllowed": false,
        	"lastRenovationDate": null,
        	"rating": 4,
        	"location": { "type": "Point", "coordinates": [-122.33207, 47.60621] }
          },
          {
        	"@search.action": "upload",
        	"hotelId": "4",
        	"baseRate": 220.00,
        	"description": "This could be the one",
        	"hotelName": "A Hotel for Everyone",
        	"category": "Basic hotel",
        	"tags": ["pool", "wifi"],
        	"parkingIncluded": true,
        	"smokingAllowed": false,
        	"lastRenovationDate": null,
        	"rating": 4,
        	"location": { "type": "Point", "coordinates": [-122.12151, 47.67399] }
          }
         ]
        }

8.	Haga clic en **Ejecutar**.

En unos pocos segundos debe ver una respuesta HTTP 200 en la lista de sesiones. Esto indica que los documentos se crearon correctamente. Si obtiene un 207, al menos un documento no pudo cargarse. Si obtiene un error 404, se ha producido un error de sintaxis en el encabezado o en el cuerpo de la solicitud.

## Consultas al índice

Ahora que se han cargado el índice y los documentos, puede emitir consultas con ellos. En la pestaña **Compositor**, el comando **GET** que consulta su servicio será similar a la siguiente captura de pantalla.

   ![][3]

1.	Seleccione **GET**.

2.	Escriba una URL que comience por HTTPS, seguida de la URL del servicio, después "/indexes/<'indexname'>/docs?" y, después, los parámetros de la consulta. A modo de ejemplo, use la siguiente URL, que reemplaza el nombre del host de ejemplo por uno que es válido para su servicio.

        https://my-app.search.windows.net/indexes/hotels/docs?search=motel&facet=category&facet=rating,values:1|2|3|4|5&api-version=2015-02-28

    Esta consulta busca el término “motel” y recupera las categorías de faceta para las calificaciones.

3.	El encabezado de la solicitud debe ser igual que antes. Recuerde que reemplazó el host y la clave de API con valores que son válidos para el servicio.

        User-Agent: Fiddler
        host: my-app.search.windows.net
        content-type: application/json
        api-key: 1111222233334444

El código de respuesta debe ser 200 y el resultado de la respuesta debe ser similar a la siguiente captura de pantalla.

   ![][4]

La siguiente consulta de ejemplo proviene del tema [Operación de índice de búsqueda (API de Búsqueda de Azure)](http://msdn.microsoft.com/library/dn798927.aspx) en MSDN. Muchas de las consultas de ejemplo en este tema incluyen espacios, que no están permitidos en Fiddler. Reemplace cada espacio por un carácter + antes de pegar la cadena de la consulta e intentar la consulta en Fiddler:

**Los espacios anteriores se reemplazan:**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2015-02-28

**Los espacios posteriores se reemplazan por +:**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate+desc&api-version=2015-02-28

## Consultas al sistema

También puede consultar al sistema para obtener recuentos de documentos y consumo de almacenamiento. En la pestaña **Compositor**, su solicitud será similar a la siguiente y la respuesta devolverá un recuento de la cantidad de documentos y el espacio usado.

 ![][5]

1.	Seleccione **GET**.

2.	Escriba una URL que incluya la URL de su servicio, seguida de "/indexes/hotels/stats?api-version=2015-02-28":

        https://my-app.search.windows.net/indexes/hotels/stats?api-version=2015-02-28

3.	Especifique el encabezado de la solicitud al reemplazar el host y la clave de API por valores que son válidos para su servicio.

        User-Agent: Fiddler
        host: my-app.search.windows.net
        content-type: application/json
        api-key: 1111222233334444

4.	Deje vacío el cuerpo de la solicitud.

5.	Haga clic en **Ejecutar**. Debe ver un código de estado HTTP 200 en la lista de sesiones. Seleccione la entrada enviada para su comando.

6.	Haga clic en la pestaña **Inspectores**, en **Encabezados** y seleccione el formato JSON. Debe ver el recuento de documentos y el tamaño del almacenamiento (en KB).

## Pasos siguientes

Consulte [Administración del servicio de búsqueda en Microsoft Azure](search-manage.md) para un enfoque sin código para administrar y usar Búsqueda de Azure.


<!--Image References-->
[1]: ./media/search-fiddler/AzureSearch_Fiddler1_PutIndex.png
[2]: ./media/search-fiddler/AzureSearch_Fiddler2_PostDocs.png
[3]: ./media/search-fiddler/AzureSearch_Fiddler3_Query.png
[4]: ./media/search-fiddler/AzureSearch_Fiddler4_QueryResults.png
[5]: ./media/search-fiddler/AzureSearch_Fiddler5_QueryStats.png

<!---HONumber=AcomDC_0224_2016-->