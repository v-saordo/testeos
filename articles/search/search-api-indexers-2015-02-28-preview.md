<properties 
pageTitle="Operaciones de indizador (API de REST del servicio de Búsqueda de Azure: 2015-02-28-Preview) |API de vista previa de Búsqueda de Azure" 
description="Operaciones de indexador (API de REST del servicio de Búsqueda de Azure: 2015-02-28-Preview)" 
services="search" 
documentationCenter="" 
authors="chaosrealm" 
manager="pablocas"
editor="" />

<tags 
ms.service="search" 
ms.devlang="rest-api" 
ms.workload="search" 
ms.topic="article"  
ms.tgt_pltfrm="na" 
ms.date="02/18/2016" 
ms.author="eugenesh" />

#Operaciones de indexador (API de REST del servicio de Búsqueda de Azure: 2015-02-28-Preview)#

> [AZURE.NOTE] En este artículo se describen los indexadores de [2015-02-28-Preview](./search-api-2015-02-28-preview). Esta versión de la API agrega un indizador de Almacenamiento de blobs de Azure con la extracción del documento, además de otras mejoras.

## Información general ##

Búsqueda de Azure puede integrarse directamente con algunos orígenes de datos comunes, eliminando la necesidad de escribir código para indexar los datos. Para configurar esto, puede llamar a la API de Búsqueda de Azure para crear y administrar **indexadores** y **orígenes de datos**.

Un **indexador** es un recurso que conecta los orígenes de datos con los índices de búsqueda de destino. Un indexador se usa de las maneras siguientes:

- Realizar una copia única de los datos para rellenar un índice.
- Sincronizar un índice con los cambios del origen de datos en una programación. La programación forma parte de la definición del indizador.
- Invocar a petición para actualizar un índice según sea necesario. 

Un **indexador** es útil cuando desea actualizaciones periódicas de un índice. Puede configurar una programación en línea como parte de una definición de indexador o ejecutarla a petición mediante [Ejecutar indexador](#RunIndexer).

Un **origen de datos** especifica los datos que es necesario indexar, las credenciales para obtener acceso a estos y las directivas que posibilitan que Búsqueda de Azure identifique cambios en los datos de forma eficaz (por ejemplo, las filas modificadas o eliminadas en una tabla de una base de datos). Se define como un recurso independiente para que puedan usarlo múltiples indexadores.

Actualmente se admiten los siguientes orígenes de datos:

- **Base de datos SQL de Azure** y **SQL Server en máquinas virtuales de Azure**. Para obtener un tutorial de destino, consulte [este artículo](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28/). 
- **DocumentDB de Azure**. Para obtener un tutorial de destino, consulte [este artículo](../documentdb/documentdb-search-indexer). 
- **Almacenamiento de blobs de Azure**, incluidos los siguientes formatos de documento: PDF, Microsoft Office (DOCX/DOC, XLS/XSLX, PPTX/PPT, MSG), HTML, XML, ZIP y archivos de texto sin formato (incluido JSON). Para obtener un tutorial de destino, consulte [este artículo](search-howto-indexing-azure-blob-storage.md).
	 
Estamos considerando agregar compatibilidad con orígenes de datos adicionales en el futuro. Para ayudarnos a priorizar estas decisiones, proporcione sus comentarios en el [foro de comentarios de Búsqueda de Azure](http://feedback.azure.com/forums/263029-azure-search).

Consulte [Límites de servicio](search-limits-quotas-capacity.md) para obtener información sobre los límites máximos relacionados con recursos de origen de datos y de indexador.

## Flujo típico de uso ##

Puede crear y administrar indexadores y orígenes de datos mediante solicitudes HTTP sencillas (POST, GET, PUT, DELETE) en un recurso `data source` o `indexer` determinado.

La configuración de la indexación automática suele ser un proceso de cuatro pasos:

1. Identifique el origen de datos que contiene los datos que es necesario indexar. Tenga en cuenta que es posible que Búsqueda de Azure no admita todos los tipos de datos presentes en el origen de datos Consulte [Tipos de datos compatibles](https://msdn.microsoft.com/library/azure/dn798938.aspx) para obtener la lista.

2. Cree un índice de Búsqueda de Azure cuyo esquema sea compatible con su origen de datos.
  
3. Cree un origen de datos de Búsqueda de Azure, tal como se describe en [Crear origen de datos](#CreateDataSource).
  
4. Cree un indexador de Búsqueda de Azure tal y como se describe en [Crear indexador](#CreateIndexer).

Debe planear la creación de un indizador para cada combinación de origen de datos e índice de destino. Puede hacer que varios indexadores escriban en el mismo índice, y puede reutilizar el mismo origen de datos para varios indexadores. Sin embargo, un indexador solo puede usar un origen de datos de cada vez y solo puede escribir en un índice único.

Después de crear un indexador, puede recuperar su estado de ejecución mediante la operación [Obtener el estado del indexador](#GetIndexerStatus). También puede ejecutar un indexador en cualquier momento (en lugar de o además de ejecutarlo de forma periódica según una programación) mediante la operación [Ejecutar indexador](#RunIndexer).

<!-- MSDN has 2 art files plus a API topic link list -->


## Crear origen de datos ##

En Búsqueda de Azure, se utiliza un origen de datos con indexadores, proporcionando la información de conexión para la actualización de datos ad hoc o programada de un índice de destino. Puede crear un origen de datos nuevo dentro de un servicio de Búsqueda de Azure mediante una solicitud HTTP POST.
	
    POST https://[service name].search.windows.net/datasources?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

Como alternativa, puede usar PUT y especificar el nombre del origen de datos en el URI. Si el origen de datos no existe, se creará.

    PUT https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]

**Nota**: El número máximo de orígenes de datos permitido varía según el nivel de precios. El servicio gratuito permite hasta tres orígenes de datos. El servicio estándar permite 50 orígenes de datos. Consulte [Límites de servicio](search-limits-quotas-capacity.md) para obtener más información.

**Solicitud**

HTTPS es necesario para todas las solicitudes de servicio. La solicitud **Crear origen de datos** se puede construir con un método POST o PUT. Al usar POST, debe proporcionar un nombre de origen de datos en el cuerpo de la solicitud junto con la definición del origen de datos. Con PUT, el nombre es parte de la dirección URL. Si el origen de datos no existe, se crea. Si ya existe, se actualiza a la nueva definición.

El nombre del origen de datos debe estar en minúsculas, comenzar por una letra o un número, no tener ninguna barra o punto y tener menos de 128 caracteres. Después de iniciar el nombre del origen de datos por una letra o un número, el resto del nombre puede incluir cualquier letra, número y guiones, siempre que los guiones no aparezcan de manera consecutiva. Consulte [Reglas de nomenclatura](https://msdn.microsoft.com/library/azure/dn857353.aspx) para obtener más información.

`api-version` es obligatorio. La versión actual es `2015-02-28`. [Versiones de Búsqueda de Azure](https://msdn.microsoft.com/library/azure/dn864560.aspx) contiene detalles y más información sobre versiones alternativas.

**Encabezados de solicitud**

En la lista siguiente se describen los encabezados de solicitud obligatorios y opcionales.

- `Content-Type`: obligatorio. Establézcalo en `application/json`
- `api-key`: obligatorio. `api-key` se usa para autenticar la solicitud en su servicio de búsqueda. Es un valor de cadena único para el servicio. La solicitud **Crear origen de datos** debe incluir un encabezado `api-key` establecido en su clave de administración (en lugar de una clave de consulta). 
 
También necesitará el nombre del servicio para construir la dirección URL de la solicitud. Puede obtener el nombre de servicio y `api-key` desde el panel de servicio en el [Portal de administración de Azure](https://portal.azure.com/). Consulte [Crear un servicio de búsqueda en el portal](search-create-service-portal.md) para obtener ayuda sobre la navegación en páginas.

<a name="CreateDataSourceRequestSyntax"></a> **Sintaxis del cuerpo de la solicitud**

El cuerpo de la solicitud contiene una definición de origen de datos, que incluye el tipo de origen de datos, las credenciales para leer los datos, así como políticas de detección de cambios en los datos opcionales y de detección de eliminación de datos que se usan para identificar de forma eficaz datos cambiados o eliminados en el origen de datos cuando se usan con un indexador programado periódicamente.


La sintaxis para estructurar la carga de la solicitud es la siguiente. En este tema se proporciona una solicitud de ejemplo.

    { 
		"name" : "Required for POST, optional for PUT. The name of the data source",
    	"description" : "Optional. Anything you want, or nothing at all",
    	"type" : "Required. Must be one of 'azuresql', 'documentdb', or 'azureblob'",
    	"credentials" : { "connectionString" : "Required. Connection string for your data source" },
    	"container" : { "name" : "Required. The name of the table, collection, or blob container you wish to index" },
    	"dataChangeDetectionPolicy" : { Optional. See below for details }, 
    	"dataDeletionDetectionPolicy" : { Optional. See below for details }
	}

La solicitud contiene las siguientes propiedades:

- `name`: obligatorio. el nombre del origen de datos. Un nombre de origen de datos solo debe contener letras minúsculas, números o guiones, no puede comenzar ni terminar con guiones y está limitado a 128 caracteres.
- `description`: descripción opcional. 
- `type`: obligatorio. Debe ser uno de los tipos de orígenes de datos compatibles:
	- `azuresql`: base de datos SQL de Azure y SQL Server en máquinas virtuales de Azure
	- `documentdb`: DocumentDB de Azure
	- `azureblob`: Almacenamiento de blobs de Azure
- `credentials`:
	- La propiedad `connectionString` obligatoria especifica la cadena de conexión del origen de datos. El formato de la cadena de conexión depende del tipo de origen de datos: 
		- Para SQL Azure, esta es la cadena de conexión de SQL Server habitual. Si está usando el Portal de Azure para recuperar la cadena de conexión, use la opción `ADO.NET connection string`.
		- Para DocumentDB, la cadena de conexión debe tener el formato siguiente: `"AccountEndpoint=https://[your account name].documents.azure.com;AccountKey=[your account key];Database=[your database id]"`. Todos los valores son obligatorios. Puede encontrarlos en el [Portal de Azure](https://portal.azure.com/).  
		- Para Almacenamiento de blobs de Azure, es la cadena de conexión de la cuenta de almacenamiento. El formato se describe [aquí](https://azure.microsoft.com/documentation/articles/storage-configure-connection-string/). Se requiere un protocolo para el punto de conexión HTTPS.  
		
- `container`, obligatorio: especifica los datos que se van a indexar mediante las propiedades `name` y `query`:
	- `name`, obligatorio:
		- SQL Azure: especifica la tabla o vista. Puede usar nombres calificados con el esquema, como `[dbo].[mytable]`.
		- DocumentDB: especifica la colección. 
		- Almacenamiento de blobs de Azure: especifica el contenedor de almacenamiento. 
	- `query`, opcional:
		- DocumentDB: permite especificar una consulta que convierte un diseño del documento JSON arbitrario en un esquema sin formato que Búsqueda de Azure puede indexar.  
		- Almacenamiento de blobs de Azure: permite especificar una carpeta virtual dentro del contenedor de blob. Por ejemplo, para la ruta de acceso de blob `mycontainer/documents/blob.pdf`, se puede usar `documents` como la carpeta virtual.
		- SQL Azure: no se admite la consulta. Si necesita esta funcionalidad, vote por [esta sugerencia](https://feedback.azure.com/forums/263029-azure-search/suggestions/9893490-support-user-provided-query-in-sql-indexer)
   
- Las propiedades `dataChangeDetectionPolicy` y `dataDeletionDetectionPolicy` opcionales se describen a continuación.

<a name="DataChangeDetectionPolicies"></a> **Directivas de detección de cambios de datos**

El fin de una directiva de detección de cambios de datos es identificar de forma eficaz los elementos de datos que han cambiado. Las directivas compatibles varían según el tipo de origen de datos. En las siguientes secciones se describe cada directiva.

***Directiva de detección de cambios de límite superior***

Use esta directiva cuando el origen de datos contenga una columna o propiedad que cumpla los criterios siguientes:
 
- Todas las inserciones especifican un valor para la columna. 
- Todas las actualizaciones de un elemento también cambian el valor de la columna. 
- El valor de esta columna aumenta con cada cambio.
- Las consultas que usan una cláusula de filtro similar a la siguiente `WHERE [High Water Mark Column] > [Current High Water Mark Value]` pueden ejecutarse de manera eficiente.

Por ejemplo, al usar orígenes de datos de SQL Azure, una columna `rowversion` indexada será el candidato ideal para usar con la directiva del límite superior.

Esta directiva se puede especificar del modo siguiente:

	{ 
		"@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
		"highWaterMarkColumnName" : "[a row version or last_updated column name]" 
	} 

> [AZURE.NOTE] Al usar orígenes de datos de DocumentDB, debe usar la propiedad `_ts` proporcionada por DocumentDB.

> [AZURE.NOTE] Cuando se usan automáticamente los orígenes de datos de Blob de Azure, Búsqueda de Azure usa una directiva de detección de cambio de límite máximo basada en una marca de tiempo modificada por última vez de un blob; no tiene que especificar esta directiva usted mismo.

***Directiva de detección de cambios integrados de SQL***

Si la base de datos SQL admite el [seguimiento de cambios](https://msdn.microsoft.com/library/bb933875.aspx), se recomienda usar la directiva de seguimiento de cambios integrada de SQL. Esta directiva habilita el seguimiento de cambios más eficaz y también permite que la Búsqueda de Azure identifique las filas eliminadas sin tener que disponer de una columna de "eliminación temporal" explícita en su esquema.

El seguimiento de cambios integrado se admite a partir de las siguientes versiones de la base de datos de SQL Server: - SQL Server 2008 R2, si está usando SQL Server en máquinas virtuales de Azure. - Base de datos SQL de Azure V12, si está usando Base de datos SQL Azure.

Al usar la directiva de seguimiento de cambios integrada de SQL, no especifique una directiva de detección de eliminación de datos independiente, ya que esta directiva tiene compatibilidad integrada para identificar las filas eliminadas.

Esta directiva solo se puede usar con tablas; no se puede usar con vistas. Deberá habilitar el seguimiento de cambios para la tabla que está usando para poder emplear esta directiva. Consulte [Habilitar y deshabilitar el seguimiento de cambios](https://msdn.microsoft.com/library/bb964713.aspx) para obtener instrucciones.
 
Al estructurar la solicitud de **Crear origen de datos**, la directiva de seguimiento de cambios integrada de SQL puede especificarse del modo indicado a continuación:

	{ 
		"@odata.type" : "#Microsoft.Azure.Search.SqlIntegratedChangeTrackingPolicy" 
	}

<a name="DataDeletionDetectionPolicies"></a> **Directivas de detección de eliminación de datos**

El fin de una directiva de detección de eliminación de datos es identificar eficazmente los elementos de datos eliminados. Actualmente, la única directiva compatible es la directiva `Soft Delete`, que permite identificar elementos eliminados según el valor de una columna `soft delete` o propiedad en el origen de datos. Esta directiva se puede especificar del modo siguiente:

	{ 
		"@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
		"softDeleteColumnName" : "the column that specifies whether a row was deleted", 
		"softDeleteMarkerValue" : "the value that identifies a row as deleted" 
	}

**NOTA:** Solo se admiten las columnas con cadenas, números enteros o valores booleanos. El valor usado como `softDeleteMarkerValue` debe ser una cadena, aunque la columna correspondiente contenga números enteros o booleanos. Por ejemplo, si el valor que aparece en el origen de datos es 1, use `"1"` como `softDeleteMarkerValue`.

<a name="CreateDataSourceRequestExamples"></a> **Ejemplos de cuerpo de solicitud**

Si piensa usar el origen de datos con un indexador que se ejecuta en una programación, este ejemplo muestra cómo especificar directivas de detección de cambios y eliminaciones:

    { 
		"name" : "asqldatasource",
		"description" : "a description",
    	"type" : "azuresql",
    	"credentials" : { "connectionString" : "Server=tcp:....database.windows.net,1433;Database=...;User ID=...;Password=...;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
    	"container" : { "name" : "sometable" },
    	"dataChangeDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy", "highWaterMarkColumnName" : "RowVersion" }, 
    	"dataDeletionDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy", "softDeleteColumnName" : "IsDeleted", "softDeleteMarkerValue" : "true" }
	}

Si solo desea usar el origen de datos para una copia única de los datos, pueden omitir las directivas:

    { 
		"name" : "asqldatasource",
    	"description" : "anything you want, or nothing at all",
    	"type" : "azuresql",
    	"credentials" : { "connectionString" : "Server=tcp:....database.windows.net,1433;Database=...;User ID=...;Password=...;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
    	"container" : { "name" : "sometable" }
	} 

**Respuesta**

Para obtener una solicitud correcta: "201 Creado".

<a name="UpdateDataSource"></a>
## Actualizar el origen de datos ##

Puede actualizar un origen de datos existente mediante una solicitud HTTP PUT. Especifique el nombre del origen de datos a actualizar en el URI de la solicitud:

    PUT https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

`api-version` es obligatorio. La versión actual es `2015-02-28`. [Versiones de Búsqueda de Azure](https://msdn.microsoft.com/library/azure/dn864560.aspx) contiene detalles y más información sobre versiones alternativas.

El `api-key` debe ser una clave de administración (en lugar de una clave de consulta). Consulte la sección de autenticación en [API de REST de servicio de búsqueda](https://msdn.microsoft.com/library/azure/dn798935.aspx) para obtener más información acerca de las claves. [Crear un servicio de búsqueda en el portal](search-create-service-portal.md) explica cómo obtener la dirección URL del servicio y las propiedades de clave usadas en la solicitud.

**Solicitud**

La sintaxis del cuerpo de la solicitud es la misma que para [Crear solicitudes de origen de datos](#CreateDataSourceRequestSyntax).

> [AZURE.NOTE]
Algunas propiedades no se pueden actualizar en un origen de datos existente. Por ejemplo, no puede cambiar el tipo de un origen de datos existente.

> [AZURE.NOTE]
Si no desea cambiar la cadena de conexión para un origen de datos existente, puede especificar el literal `<unchanged>` para la cadena de conexión. Esto es útil en situaciones donde es necesario actualizar el origen de datos, pero no tiene un acceso cómodo a la cadena de conexión, ya que son datos confidenciales.

**Respuesta**

Para efectuar una solicitud correcta: 201 Creado si se ha creado un origen de datos nuevo, y 204 Sin contenido si se ha actualizado un origen de datos existente.

<a name="ListDataSource"></a>
## Enumerar orígenes de datos ##

La operación **Enumerar orígenes de datos** devuelve una lista de los orígenes de datos en el servicio de Búsqueda de Azure.

    GET https://[service name].search.windows.net/datasources?api-version=[api-version]
    api-key: [admin key]

`api-version` es obligatorio. La versión actual es `2015-02-28`. [Versiones de Búsqueda de Azure](https://msdn.microsoft.com/library/azure/dn864560.aspx) contiene detalles y más información sobre versiones alternativas.

El `api-key` debe ser una clave de administración (en lugar de una clave de consulta). Consulte la sección de autenticación en [API de REST de servicio de búsqueda](https://msdn.microsoft.com/library/azure/dn798935.aspx) para obtener más información acerca de las claves. [Crear un servicio de búsqueda en el portal](search-create-service-portal.md) explica cómo obtener la dirección URL del servicio y las propiedades de clave usadas en la solicitud.

**Respuesta**

Para obtener una solicitud correcta: "200 Correcto".

A continuación se proporciona un cuerpo de respuesta de ejemplo:

    {
      "value" : [
        {
          "name": "datasource1",
          "type": "azuresql",
		  ... other data source properties
        }]
    }

Tenga en cuenta que puede filtrar la respuesta a solo las propiedades que le interesan. Por ejemplo, si solamente desea una lista de los nombres de orígenes de datos, use la opción de consulta OData `$select`:

    GET /datasources?api-version=205-02-28&$select=name

En este caso, la respuesta del ejemplo anterior podría aparecer como sigue:

    {
      "value" : [ { "name": "datasource1" }, ... ]
    }

Se trata de una técnica útil para ahorrar ancho de banda, si tiene una gran cantidad de orígenes de datos en el servicio de búsqueda.

<a name="GetDataSource"></a>
## Obtener origen de datos ##

La operación **Obtener origen de datos** permite obtener la definición del origen de datos de Búsqueda de Azure.

    GET https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]
    api-key: [admin key]

`api-version` es obligatorio. La versión actual es `2015-02-28`. [Versiones de Búsqueda de Azure](https://msdn.microsoft.com/library/azure/dn864560.aspx) contiene detalles y más información sobre versiones alternativas.

El `api-key` debe ser una clave de administración (en lugar de una clave de consulta). Consulte la sección de autenticación en [API de REST de servicio de búsqueda](https://msdn.microsoft.com/library/azure/dn798935.aspx) para obtener más información acerca de las claves. [Crear un servicio de búsqueda en el portal](search-create-service-portal.md) explica cómo obtener la dirección URL del servicio y las propiedades de clave usadas en la solicitud.

**Respuesta**

Código de estado: al obtener una respuesta correcta, se visualiza 200 Correcto.

La respuesta es similar a los ejemplos de [Solicitudes de ejemplo de crear origen de datos](#CreateDataSourceRequestExamples):

	{ 
		"name" : "asqldatasource",
		"description" : "a description",
    	"type" : "azuresql",
    	"credentials" : { "connectionString" : "Server=tcp:....database.windows.net,1433;Database=...;User ID=...;Password=...;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
    	"container" : { "name" : "sometable" },
    	"dataChangeDetectionPolicy" : { 
            "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
			"highWaterMarkColumnName" : "RowVersion" }, 
    	"dataDeletionDetectionPolicy" : { 
            "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
			"softDeleteColumnName" : "IsDeleted", 
			"softDeleteMarkerValue" : "true" }
	}

> [AZURE.NOTE] No establezca el encabezado de la solicitud `Accept` en `application/json;odata.metadata=none` al llamar a esta API, ya que hacerlo provocará la omisión del atributo `@odata.type` de la respuesta y no será capaz de diferenciar entre los cambios de datos y directivas de detección de eliminación de datos de distintos tipos.

<a name="DeleteDataSource"></a>
## Eliminar origen de datos ##

La operación **Eliminar origen de datos** elimina un origen de datos del servicio de Búsqueda de Azure.

    DELETE https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]
    api-key: [admin key]

> [AZURE.NOTE] Si algún indizador hace referencia al origen de datos que está eliminando, la operación de eliminación continuará. Sin embargo, los indexadores pasarán a un estado de error en su siguiente ejecución.

`api-version` es obligatorio. La versión actual es `2015-02-28`. [Versiones de Búsqueda de Azure](https://msdn.microsoft.com/library/azure/dn864560.aspx) contiene detalles y más información sobre versiones alternativas.

El `api-key` debe ser una clave de administración (en lugar de una clave de consulta). Consulte la sección de autenticación en [API de REST de servicio de búsqueda](https://msdn.microsoft.com/library/azure/dn798935.aspx) para obtener más información acerca de las claves. [Crear un servicio de búsqueda en el portal](search-create-service-portal.md) explica cómo obtener la dirección URL del servicio y las propiedades de clave usadas en la solicitud.

**Respuesta**

Código de estado: al obtener una respuesta correcta, se visualiza 204 Sin contenido.

<a name="CreateIndexer"></a>
## Crear indexador ##

Puede crear un indexador nuevo dentro de un servicio de Búsqueda de Azure mediante una solicitud HTTP POST:
	
    POST https://[service name].search.windows.net/indexers?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

Como alternativa, puede usar PUT y especificar el nombre del origen de datos en el URI. Si el origen de datos no existe, se creará.

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]

> [AZURE.NOTE] El número máximo de indizadores permitido varía según el plan de tarifa. El servicio gratuito permite hasta tres indexadores. El servicio estándar permite 50 indexadores. Consulte [Límites de servicio](search-limits-quotas-capacity.md) para obtener más información.

`api-version` es obligatorio. La versión actual es `2015-02-28`. [Versiones de Búsqueda de Azure](https://msdn.microsoft.com/library/azure/dn864560.aspx) contiene detalles y más información sobre versiones alternativas.

El `api-key` debe ser una clave de administración (en lugar de una clave de consulta). Consulte la sección de autenticación en [API de REST de servicio de búsqueda](https://msdn.microsoft.com/library/azure/dn798935.aspx) para obtener más información acerca de las claves. [Crear un servicio de búsqueda en el portal](search-create-service-portal.md) explica cómo obtener la dirección URL del servicio y las propiedades de clave usadas en la solicitud.


<a name="CreateIndexerRequestSyntax"></a> **Sintaxis del cuerpo de la solicitud**

El cuerpo de la solicitud contiene una definición de indexador, que especifica el origen de datos y el índice de destino para la indexación, así como la programación de indexación opcional y los parámetros.


La sintaxis para estructurar la carga de la solicitud es la siguiente. En este tema se proporciona una solicitud de ejemplo.

    { 
		"name" : "Required for POST, optional for PUT. The name of the indexer",
    	"description" : "Optional. Anything you want, or null",
    	"dataSourceName" : "Required. The name of an existing data source",
        "targetIndexName" : "Required. The name of an existing index",
        "schedule" : { Optional. See Indexing Schedule below. },
        "parameters" : { Optional. See Indexing Parameters below. },
        "fieldMappings" : { Optional. See Field Mappings below. },
        "disabled" : Optional boolean value indicating whether the indexer is disabled. False by default.  
	}

**Programación de indexador**

Un indizador puede especificar opcionalmente una programación. Si existe una programación, el indizador se ejecutará de forma periódica de acuerdo con la misma. Una programación tiene los siguientes atributos:

- `interval`: obligatorio. Valor de duración que especifica un intervalo o período durante el que se ejecuta el indizador. El intervalo mínimo permitido es de 5 minutos y el máximo de un día. Debe tener el formato de un valor "dayTimeDuration" XSD (subconjunto restringido de un valor de [duración ISO 8601](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration)). El patrón de este es: `"P[nD][T[nH][nM]]"`. Ejemplos: `PT15M` para cada 15 minutos, `PT2H` para cada 2 horas. 

- `startTime`: obligatorio. Valor de fecha y hora UTC que especifica cuándo debería empezar a ejecutarse el indexador.

**Parámetros de un indexador**

Un indexador puede especificar varios parámetros que afectan a su comportamiento. Todos los parámetros son opcionales.

- `maxFailedItems` : el número de elementos que es posible que no se indexen antes de que la ejecución de un indexador se considere un fallo. El valor predeterminado es 0. La operación [Obtener el estado del indexador](#GetIndexerStatus) devuelve información acerca de los elementos no indexados. 

- `maxFailedItemsPerBatch` : el número de elementos que es posible que no se indexen en cada lote antes de que la ejecución de un indexador se considere un fallo. El valor predeterminado es 0.

- `base64EncodeKeys`: especifica si las claves de documento estarán codificadas en base 64. Búsqueda de Azure impone restricciones en los caracteres que pueden estar presentes en una clave del documento. Sin embargo, los valores de los datos de origen pueden contener caracteres que no son válidos. Si es necesario indexar estos valores como claves del documento, este indicador puede establecerse en true. El valor predeterminado es `false`.

- `batchSize`: especifica el número de elementos que se leen desde el origen de datos y se indizan como un único lote para mejorar el rendimiento. El valor predeterminado depende del tipo de origen de datos: es 1000 para SQL de Azure y DocumentDB, y 10 para Almacenamiento de blobs de Azure.


**Asignaciones de campos**

Puede usar asignaciones de campos para asignar un nombre de campo del origen de datos a otro nombre de campo en el índice de destino. Por ejemplo, considere una tabla de origen con un campo `_id`. Búsqueda de Azure no permite un nombre de campo que comience por un carácter de subrayado, por lo que debe cambiarse el nombre del campo. Esto puede hacerse mediante la propiedad `fieldMappings` del indexador del modo indicado a continuación:
	
	"fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ] 

Puede especificar varias asignaciones de campo.

	"fieldMappings" : [ 
		{ "sourceFieldName" : "_id", "targetFieldName" : "id" },
        { "sourceFieldName" : "_timestamp", "targetFieldName" : "timestamp" },
	 ]

Los nombres de campo de origen y destino no distinguen entre mayúsculas y minúsculas.

<a name="FieldMappingFunctions"></a> ***Funciones de asignación de campos***

Las asignaciones de campos también pueden usarse para transformar valores del campo de origen mediante *funciones de asignación*.

Actualmente solo se admite una función de este tipo: `jsonArrayToStringCollection`. Analiza un campo que contiene una cadena con formato como el de una matriz JSON en un campo de Collection(Edm.String) en el índice de destino. Está pensado para su usarse con el indexador de SQL Azure en particular, puesto que SQL no tiene un tipo de datos de colección nativo. Se puede usar del modo indicado a continuación:

	"fieldMappings" : [ { "sourceFieldName" : "tags", "mappingFunction" : { "name" : "jsonArrayToStringCollection" } } ] 

Por ejemplo, si el campo de origen contiene la cadena `["red", "white", "blue"]`, a continuación, el campo de destino de tipo `Collection(Edm.String)` se rellenará con los tres valores `"red"`, `"white"` y `"blue"`.

Observe que la propiedad `targetFieldName` es opcional; si se omite, se usará el valor `sourceFieldName`.

<a name="CreateIndexerRequestExamples"></a> **Ejemplo de cuerpo de solicitud**

En el ejemplo siguiente se crea un indexador que copia datos de la tabla a la que hace referencia el origen de datos `ordersds` en el índice `orders` de acuerdo con una programación que empieza el 1 de enero de 2015 UTC y se ejecuta cada hora. Cada invocación de indexador se realizará correctamente si no se logra indexar más de cinco elementos en cada lote y no más de 10 elementos en total.

	{
        "name" : "myindexer",
        "description" : "a cool indexer",
        "dataSourceName" : "ordersds",
        "targetIndexName" : "orders",
        "schedule" : { "interval" : "PT1H", "startTime" : "2015-01-01T00:00:00Z" },
        "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 5, "base64EncodeKeys": false }
	}

**Respuesta**

Para obtener una solicitud correcta: "201 Creado".


<a name="UpdateIndexer"></a>
## Actualizar indexador ##

Puede actualizar un indexador existente mediante una solicitud HTTP PUT. Especifique el nombre del indexador que se va a actualizar en el URI de solicitud:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

`api-version` es obligatorio. La versión actual es `2015-02-28`. [Versiones de Búsqueda de Azure](https://msdn.microsoft.com/library/azure/dn864560.aspx) contiene detalles y más información sobre versiones alternativas.

El `api-key` debe ser una clave de administración (en lugar de una clave de consulta). Consulte la sección de autenticación en [API de REST de servicio de búsqueda](https://msdn.microsoft.com/library/azure/dn798935.aspx) para obtener más información acerca de las claves. [Crear un servicio de búsqueda en el portal](search-create-service-portal.md) explica cómo obtener la dirección URL del servicio y las propiedades de clave usadas en la solicitud.

**Solicitud**

La sintaxis del cuerpo de la solicitud es la misma que para [Crear solicitudes de indexador](#CreateIndexerRequestSyntax).

**Respuesta**

Para efectuar una solicitud correcta: 201 Creado si se ha creado un indexador nuevo, y 204 Sin contenido si se ha actualizado un indexador existente.


<a name="ListIndexers"></a>
## Enumerar indexadores ##

La operación **Enumerar indexadores** devuelve una lista de los índices que se encuentran en el servicio de Búsqueda de Azure.

    GET https://[service name].search.windows.net/indexers?api-version=[api-version]
    api-key: [admin key]


`api-version` es obligatorio. La versión de vista previa es `2015-02-28-Preview`. [Versiones de Búsqueda de azure](https://msdn.microsoft.com/library/azure/dn864560.aspx) contiene detalles y más información sobre versiones alternativas.

El `api-key` debe ser una clave de administración (en lugar de una clave de consulta). Consulte la sección de autenticación en [API de REST de servicio de búsqueda](https://msdn.microsoft.com/library/azure/dn798935.aspx) para obtener más información acerca de las claves. [Crear un servicio de búsqueda en el portal](search-create-service-portal.md) explica cómo obtener la dirección URL del servicio y las propiedades de clave usadas en la solicitud.

**Respuesta**

Para obtener una solicitud correcta: "200 Correcto".

A continuación se proporciona un cuerpo de respuesta de ejemplo:

    {
      "value" : [
      {
        "name" : "myindexer",
        "description" : "a cool indexer",
        "dataSourceName" : "ordersds",
        "targetIndexName" : "orders",
        ... other indexer properties
	  }]
    }

Tenga en cuenta que puede filtrar la respuesta a solo las propiedades que le interesan. Por ejemplo, si solamente desea una lista de los nombres de los indexadores, use la opción de consulta OData `$select`:

    GET /indexers?api-version=2014-10-20-Preview&$select=name

En este caso, la respuesta del ejemplo anterior podría aparecer como sigue:

    {
      "value" : [ { "name": "myindexer" } ]
    }

Se trata de una técnica útil para ahorrar ancho de banda, si tiene una gran cantidad de indexadores en el servicio de búsqueda.


<a name="GetIndexer"></a>
## Obtener indexador ##

La operación **Obtener indexador** obtiene la definición del indexador de Búsqueda de Azure.

    GET https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]
    api-key: [admin key]

`api-version` es obligatorio. La versión de vista previa es `2015-02-28-Preview`. [Versiones de Búsqueda de azure](https://msdn.microsoft.com/library/azure/dn864560.aspx) contiene detalles y más información sobre versiones alternativas.

El `api-key` debe ser una clave de administración (en lugar de una clave de consulta). Consulte la sección de autenticación en [API de REST de servicio de búsqueda](https://msdn.microsoft.com/library/azure/dn798935.aspx) para obtener más información acerca de las claves. [Crear un servicio de búsqueda en el portal](search-create-service-portal.md) explica cómo obtener la dirección URL del servicio y las propiedades de clave usadas en la solicitud.

**Respuesta**

Código de estado: al obtener una respuesta correcta, se visualiza 200 Correcto.

La respuesta es similar a los ejemplos de [Crear solicitudes de ejemplo de indexador](#CreateIndexerRequestExamples):

	{
        "name" : "myindexer",
        "description" : "a cool indexer",
        "dataSourceName" : "ordersds",
        "targetIndexName" : "orders",
        "schedule" : { "interval" : "PT1H", "startTime" : "2015-01-01T00:00:00Z" },
        "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 5, "base64EncodeKeys": false }
	}


<a name="DeleteIndexer"></a>
## Eliminar indexador ##

La operación **Eliminar indexador** quita un indexador del servicio de Búsqueda de Azure.

    DELETE https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]
    api-key: [admin key]

Cuando se elimina un indexador, las ejecuciones de indexador en curso en ese momento se ejecutarán hasta completarse, pero no se programarán más ejecuciones. Los intentos de usar un indexador inexistente provocarán que se muestre el código de estado HTTP 404 No encontrado.
 
`api-version` es obligatorio. La versión de vista previa es `2015-02-28-Preview`. [Versiones de Búsqueda de azure](https://msdn.microsoft.com/library/azure/dn864560.aspx) contiene detalles y más información sobre versiones alternativas.

El `api-key` debe ser una clave de administración (en lugar de una clave de consulta). Consulte la sección de autenticación en [API de REST de servicio de búsqueda](https://msdn.microsoft.com/library/azure/dn798935.aspx) para obtener más información acerca de las claves. [Crear un servicio de búsqueda en el portal](search-create-service-portal.md) explica cómo obtener la dirección URL del servicio y las propiedades de clave usadas en la solicitud.

**Respuesta**

Código de estado: al obtener una respuesta correcta, se visualiza 204 Sin contenido.

<a name="RunIndexer"></a>
## Ejecutar indexador ##

Además de ejecutarse periódicamente según una programación, un indexador también puede invocarse a petición mediante la operación **Ejecutar indexador**:

	POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=[api-version]
    api-key: [admin key]

`api-version` es obligatorio. La versión de vista previa es `2015-02-28-Preview`. [Versiones de Búsqueda de azure](https://msdn.microsoft.com/library/azure/dn864560.aspx) contiene detalles y más información sobre versiones alternativas.

El `api-key` debe ser una clave de administración (en lugar de una clave de consulta). Consulte la sección de autenticación en [API de REST de servicio de búsqueda](https://msdn.microsoft.com/library/azure/dn798935.aspx) para obtener más información acerca de las claves. [Crear un servicio de búsqueda en el portal](search-create-service-portal.md) explica cómo obtener la dirección URL del servicio y las propiedades de clave usadas en la solicitud.

**Respuesta**

Código de estado: 202 Aceptado se obtiene tras una respuesta correcta.

<a name="GetIndexerStatus"></a>
## Obtener estado del indexador ##

La operación **Obtener estado del indexador** recupera el estado actual y el historial de ejecución de un indexador:

	GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=[api-version]
    api-key: [admin key]


`api-version` es obligatorio. La versión de vista previa es `2015-02-28-Preview`. [Versiones de Búsqueda de azure](https://msdn.microsoft.com/library/azure/dn864560.aspx) contiene detalles y más información sobre versiones alternativas.

El `api-key` debe ser una clave de administración (en lugar de una clave de consulta). Consulte la sección de autenticación en [API de REST de servicio de búsqueda](https://msdn.microsoft.com/library/azure/dn798935.aspx) para obtener más información acerca de las claves. [Crear un servicio de búsqueda en el portal](search-create-service-portal.md) explica cómo obtener la dirección URL del servicio y las propiedades de clave usadas en la solicitud.

**Respuesta**

Código de estado: 200 Correcto al obtener una respuesta correcta.

El cuerpo de la respuesta contiene información sobre el estado general del indexador, la última invocación de este, así como el historial de invocaciones recientes del mismo (si existe).

Un cuerpo de respuesta de muestra tiene el siguiente aspecto:

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

**Estado del indexador**

El estado del indexador puede ser uno de los siguientes valores:

- `running` indica que el indexador se ejecuta con normalidad. Tenga en cuenta que es posible que algunas de las ejecuciones del indexador continúen provocando un error, por lo que es una buena idea comprobar la propiedad `lastResult` también. 

- `error` indica que el indexador experimentó un error que no se pueden corregir sin intervención humana. Por ejemplo, es posible que las credenciales del origen de datos hayan caducado, que el esquema del origen de datos haya cambiado o que el índice del destino haya cambiado separándose.

**Resultado de la ejecución del indexador**

Un resultado de la ejecución de indexador contiene información sobre la ejecución de un indexador único. El resultado más reciente aparece como la propiedad `lastResult` del estado del indexador. Otros resultados recientes, si están presentes, se devuelven como la propiedad `executionHistory` del estado del indexador.

El resultado de la ejecución del indexador contiene las siguientes propiedades:

- `status`: el estado de una ejecución. Consulte [Estado de la ejecución del indexador](#IndexerExecutionStatus) a continuación para obtener más información. 

- `errorMessage`: mensaje de error de una ejecución errónea.

- `startTime`: la hora en UTC en la que se inició esta ejecución.

- `endTime`: la hora en UTC en la que se finalizó esta ejecución. Este valor no se establece si la ejecución todavía está en curso.

- `errors`: una lista de errores de nivel de elemento, si los hay. Cada entrada contiene una clave de documento (propiedad `key`) y un mensaje de error (propiedad `errorMessage`).

- `itemsProcessed`: el número de elementos del origen de datos (por ejemplo, filas de tablas) que el indexador intentó indexar durante esta ejecución.

- `itemsFailed`: el número de elementos que produjeron un error durante esta ejecución.
 
- `initialTrackingState`: siempre `null` para la primera ejecución del indexador, o si la política de seguimiento de cambio de datos no está habilitada en el origen de datos usado. Si esta directiva está habilitada, en ejecuciones posteriores, este valor indica el primer valor de seguimiento de cambio (el más bajo) procesado por esta ejecución.

- `finalTrackingState`: siempre `null` si la política de seguimiento de cambio de datos no está habilitada en el origen de datos usado. En caso contrario, indica el último valor de seguimiento de cambios (superior) procesado correctamente por esta ejecución.

<a name="IndexerExecutionStatus"></a> **Estado de la ejecución del indexador**

El estado de la ejecución del indexador captura el estado de una ejecución de un indexador único. Puede presentar los siguientes valores:

- `success` indica que se ha completado correctamente la ejecución del indexador.

- `inProgress` indica que la ejecución de indexador está en curso.

- `transientFailure` indica que la ejecución de indexador ha fallado. Consulte la propiedad `errorMessage` para obtener detalles. El error puede o no requerir intervención humana para su solución; por ejemplo, corregir una incompatibilidad de esquema entre el origen de datos y el índice de destino requiere acción por parte del usuario, mientras que un tiempo de inactividad temporal de un origen de datos no la requiere. Las invocaciones del indexador continuarán según la programación, si hay alguna establecida.

- `persistentFailure` indica que se ha producido un error en el indexador de manera que requiere la intervención humana. Detención de ejecuciones programadas del indexador. Tras solucionar el problema, use Restablecer la API de indexador para reiniciar las ejecuciones programadas.

- `reset` indica que se ha restablecido el indexador mediante una llamada a la API de restablecimiento del indexador (consultar a continuación).

<a name="ResetIndexer"></a>
## Restablecer el indexador ##

La operación **Restablecer el indexador** restablece el estado de seguimiento de cambios asociado con el indexador. Esto permite desencadenar la reindexación desde cero (por ejemplo, si ha cambiado el esquema del origen de datos) o para cambiar la directiva de detección de cambios de datos de un origen de datos asociado con el indexador.

	POST https://[service name].search.windows.net/indexers/[indexer name]/reset?api-version=[api-version]
    api-key: [admin key]

`api-version` es obligatorio. La versión de vista previa es `2015-02-28-Preview`. [Versiones de Búsqueda de azure](https://msdn.microsoft.com/library/azure/dn864560.aspx) contiene detalles y más información sobre versiones alternativas.

El `api-key` debe ser una clave de administración (en lugar de una clave de consulta). Consulte la sección de autenticación en [API de REST de servicio de búsqueda](https://msdn.microsoft.com/library/azure/dn798935.aspx) para obtener más información acerca de las claves. [Crear un servicio de búsqueda en el portal](search-create-service-portal.md) explica cómo obtener la dirección URL del servicio y las propiedades de clave usadas en la solicitud.

**Respuesta**

Código de estado: 204 Sin contenido para obtener una respuesta correcta.

## Asignación entre tipos de datos de SQL y tipos de datos de Búsqueda de Azure ##

<table style="font-size:12">
<tr>
<td>Tipo de datos de SQL</td>	
<td>Tipos de campos de índice de destino permitidos</td>
<td>Notas</td>
</tr>
<tr>
<td>bit</td>
<td>Edm.Boolean, Edm.String</td>
<td></td>
</tr>
<tr>
<td>int, smallint, tinyint</td>
<td>Edm.Int32, Edm.Int64, Edm.String</td>
<td></td>
</tr>
<tr>
<td>bigint</td>
<td>Edm.Int64, Edm.String</td>
<td></td>
</tr>
<tr>
<td>real, float</td>
<td>Edm.Double, Edm.String</td>
<td></td>
</tr>
<tr>
<td>smallmoney, money<br>decimal<br>numeric
</td>
<td>Edm.String</td>
<td>Búsqueda de Azure no admite la conversión de tipos decimales en Edm.Double porque esto podría provocar la pérdida de precisión
</td>
</tr>
<tr>
<td>char, nchar, varchar, nvarchar</td>
<td>Edm.String<br/>Collection(Edm.String)</td>
<td>Consulte [Funciones de asignación de campos](#FieldMappingFunctions) en este documento para obtener más información acerca de cómo transformar una columna de cadenas en una Collection(Edm.String)</td>
</tr>
<tr>
<td>smalldatetime, datetime, datetime2, date, datetimeoffset</td>
<td>Edm.DateTimeOffset, Edm.String</td>
<td></td>
</tr>
<tr>
<td>uniqueidentifer</td>
<td>Edm.String</td>
<td></td>
</tr>
<tr>
<td>geography</td>
<td>Edm.GeographyPoint</td>
<td>Solo se admiten instancias de geography de tipo POINT con SRID 4326 (que es el valor predeterminado)</td>
</tr>
<tr>
<td>rowversion</td>
<td>N/D</td>
<td>Las columnas de versión de la fila no se pueden almacenar en el índice de búsqueda, pero pueden usarse para el seguimiento de cambios</td>
</tr>
<tr>
<td>time, timespan<br>binary, varbinary, image,<br>xml, geometry, CLR types</td>
<td>N/D</td>
<td>No compatible</td>
</tr>
</table>

## asignación entre tipos de datos de JSON y de Búsqueda de Azure ##

<table style="font-size:12">
<tr>
<td>Tipo de datos de JSON</td>	
<td>Tipos de campos de índice de destino permitidos</td>
<td>Notas</td>
</tr>
<tr>
<td>booleano</td>
<td>Edm.Boolean, Edm.String</td>
<td></td>
</tr>
<tr>
<td>Números enteros</td>
<td>Edm.Int32, Edm.Int64, Edm.String</td>
<td></td>
</tr>
<tr>
<td>Números de punto flotante</td>
<td>Edm.Double, Edm.String</td>
<td></td>
</tr>
<tr>
<td>cadena</td>
<td>Edm.String</td>
<td></td>
</tr>
<tr>
<td>Matrices de tipos primitivos, por ejemplo "a", "b", "c"</td>
<td>Collection(Edm.String)</td>
<td></td>
</tr>
<tr>
<td>Cadenas que parecen fechas</td>
<td>Edm.DateTimeOffset, Edm.String</td>
<td></td>
</tr>
<tr>
<td>Objetos de punto de GeoJSON</td>
<td>Edm.GeographyPoint</td>
<td>Los puntos de GeoJSON son objetos JSON del siguiente formato: { "tipo" : "Punto", "coordenadas" : [long, lat] } </td>
</tr>
<tr>
<td>Otros objetos JSON</td>
<td>N/D</td>
<td>No se admite; actualmente Búsqueda de Azure solo admite tipos primitivos y colecciones de cadenas</td>
</tr>
</table>

<!---HONumber=AcomDC_0224_2016-->