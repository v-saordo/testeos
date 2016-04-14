<properties 
	pageTitle="Personalización del indexador de Búsqueda de Azure | Microsoft Azure | Servicio de búsqueda hospedado en la nube" 
	description="Aprenda a personalizar la configuración y las directivas de los indexadores de Búsqueda de Azure, un servicio de búsqueda hospedado en la nube en Microsoft Azure." 
	services="search" 
	documentationCenter="" 
	authors="chaosrealm" 
	manager="pablocas" 
	editor=""/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="02/04/2016" 
	ms.author="eugenesh"/>

#Personalización del indexador de Búsqueda de Azure

La configuración de un indexador en Búsqueda de Azure le permite cambiar el nombre de los campos entre un origen de datos y un índice de destino, transformar cadenas a partir de una tabla de base de datos en colecciones de cadenas, cambiar la directiva de detección de cambios en un origen de datos, codificar como dirección URL claves de documento que contienen caracteres no seguros para direcciones URL y tolerar errores para indexar algunos documentos.

Si no está familiarizado con los indexadores de Búsqueda de Azure, es posible que desee revisar primero los siguientes artículos:

- [Conexión de Base de datos SQL de Azure a Búsqueda de Azure con indexadores](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)
- [Conexión de DocumentDB con Búsqueda de Azure mediante indexadores](../documentdb/documentdb-search-indexer.md)
- [SDK de .NET con compatibilidad para indexadores](https://msdn.microsoft.com/library/dn951165.aspx) o 
- [Referencia de API de REST de indexadores](https://msdn.microsoft.com/library/azure/dn946891.aspx)

##Cambio del nombre de campos entre un origen de datos y un índice de destino##

**Asignaciones de campos** son propiedades que concilian las diferencias entre las definiciones de campo. Los ejemplos más comunes se encuentran en nombres de campos que infringen las reglas de nomenclatura de Búsqueda de Azure. Considere una tabla de origen donde uno o más nombres de campos comienzan con un carácter de subrayado (como `_id`). Búsqueda de Azure no permite un nombre de campo que comience con un carácter de subrayado, por lo que debe cambiarse el nombre del campo.

El ejemplo siguiente ilustra la actualización de un indexador para incluir una asignación de campo que "cambie el nombre" del campo `_id` del origen de datos al campo `id` del índice de destino:

	PUT https://[service name].search.windows.net/indexers/myindexer?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]
    {
        "dataSourceName" : "mydatasource",
        "targetIndexName" : "myindex",
        "fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ] 
    } 

**NOTA:** debe usar una versión de API de vista previa 2015-02-28 para usar asignaciones de campo.

Puede especificar varias asignaciones de campo.

	"fieldMappings" : [ 
		{ "sourceFieldName" : "_id", "targetFieldName" : "id" },
        { "sourceFieldName" : "_timestamp", "targetFieldName" : "timestamp" },
	 ]

Los nombres de campo de origen y destino no distinguen entre mayúsculas y minúsculas.

##Transformación de cadenas desde una tabla de base de datos en colecciones de cadenas##

Las asignaciones de campos también pueden usarse para transformar valores del campo de origen mediante *funciones de asignación*.

En dicha función, `jsonArrayToStringCollection`, analiza un campo que contiene una cadena con formato de matriz JSON en un campo Collection(Edm.String) en el índice de destino. Está pensado para su usarse con el indexador de SQL Azure en particular, puesto que SQL no tiene un tipo de datos de colección nativo. Se puede usar del modo indicado a continuación:

	"fieldMappings" : [ { "sourceFieldName" : "tags", "mappingFunction" : { "name" : "jsonArrayToStringCollection" } } ] 

Por ejemplo, si el campo de origen contiene la cadena `["red", "white", "blue"]`, a continuación, el campo de destino de tipo `Collection(Edm.String)` se rellenará con los tres valores `"red"`, `"white"` y `"blue"`.

Observe que la propiedad `targetFieldName` es opcional; si se omite, se usará el valor `sourceFieldName`.

##Cambio de la directiva de detección de cambios en un origen de datos##
  
En Búsqueda de Azure, la decisión de qué directiva de detección de cambios se usará se determina en gran medida por la directiva de detección de cambios admitida por el origen de datos y el esquema de datos. Con el tiempo, especialmente si actualiza o migra bases de datos, es posible que desee cambiar una directiva de detección de cambios a otro tipo. Por ejemplo, quizás haya actualizado recién la Base de datos SQL de Azure a una versión más reciente que admite Seguimiento integrado de cambios, por lo que desea cambiar desde la directiva de límite máximo a la directiva de seguimiento integrado de cambios. O quizás ha decidido usar una columna distinta como el límite máximo.

Si solo llama a la API de REST de origen de datos PUT para actualizar el origen de datos, es posible que reciba una respuesta 400 con un mensaje de error similar al siguiente:


	"Change detection policy cannot be changed for data source '…' because indexer '…' references this data source and has a non-empty change tracking state. You can use Reset API to reset the indexer's change tracking state, and retry this call."

 Probablemente se pregunte qué significa esto y cómo solucionarlo. Este error se produce debido a que Búsqueda de Azure mantiene el estado interno asociado a la directiva de detección de cambios. Cuando se cambia la directiva, se invalida el estado existente debido a que no se aplica a la directiva nueva. Esto significa que el indexador debe comenzar a indexar de cero el origen de datos con la directiva nueva, lo que tiene posibles consecuencias para usted (por ejemplo, una carga adicional en la base de datos o cargos adicionales por concepto de ancho de banda de la red). Es por esta razón que Búsqueda de Azure le pide llamar a la [API de restablecimiento del indexador](https://msdn.microsoft.com/library/azure/dn946897.aspx) para restablecer el estado asociado con la directiva de detección de cambios actual, después de lo cual es posible cambiar la directiva con una llamada de origen de datos PUT normal. Por supuesto, Búsqueda de Azure podría realizar el restablecimiento automáticamente, pero consideramos que era importante que entendiera explícitamente las consecuencias de llamar a la API de restablecimiento.

##Codificación como dirección URL de claves de documento que contienen caracteres no seguros para dirección URL##

Búsqueda de Azure restringe los caracteres dentro de un campo clave de documento a los caracteres seguros para dirección URL, debido a que los usuarios deben poder consultar documentos según sus claves. ¿Qué ocurre entonces cuando los documentos que necesita indexar contienen dichos caracteres en el campo de clave? Si indexa documentos con un SDK de cliente o una API de REST, puede codificar las claves como dirección URL. Con los indexadores, puede indicar a Búsqueda de Azure que codifique las claves como dirección URL definiendo el parámetro **base64EncodeKeys** en `true` al crear o actualizar el indexador:

    PUT https://[service name].search.windows.net/indexers/myindexer?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]
    {
        "dataSourceName" : "mydatasource",
        "targetIndexName" : "myindex",
        "parameters" : { "base64EncodeKeys": true }
    }

Para obtener detalles sobre la codificación, consulte este [artículo de MSDN](http://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx).

NOTA: si necesita buscar o filtrar valores de clave, deberá realizar una codificación similar de las claves usadas en las solicitudes, para que su solicitud coincida con el valor codificado almacenado en el índice de búsqueda.


##Tolerancia de errores para indexar algunos documentos##

De manera predeterminada, un indexador de Búsqueda de Azure deja de realizar la indexación en el momento mismo en que se produce un error al indexar un documento. Dependiendo del escenario, puede elegir tolerar algunos errores (por ejemplo, si vuelve a indexar de manera repetida todo el origen de datos). Búsqueda de Azure proporciona dos parámetros de indexador para ajustar este comportamiento:

- **maxFailedItems**: la cantidad de elementos que pueden presentar errores de indexación antes de que la ejecución de un indexador se considere un error. El valor predeterminado es 0.
- **maxFailedItemsPerBatch**: la cantidad de elementos que pueden presentar errores al indexarse en un solo lote antes de que la ejecución de un indexador se considere un error. El valor predeterminado es 0.

Puede cambiar estos valores en cualquier momento si especifica uno o ambos parámetros cuando crea o actualiza el indexador:

	PUT https://[service name].search.windows.net/indexers/myindexer?api-version=[api-version]
	Content-Type: application/json
	api-key: [admin key]
    {
        "dataSourceName" : "mydatasource",
        "targetIndexName" : "myindex",
        "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 5 }
    }

Incluso si intenta tolerar algunos errores, la [API de obtención del estado de indexador](https://msdn.microsoft.com/library/azure/dn946884.aspx) devolverá información acerca de cuáles son los documentos que presentaron errores.

Eso es todo por ahora. Si tiene ideas o sugerencias para futuras ideas de contenido, envíenos un mensaje de Twitter con el hashtag #AzureSearch o envíe sus ideas a nuestra [página de UserVoice](https://feedback.azure.com/forums/263029-azure-search/).
 

<!---HONumber=AcomDC_0224_2016-->