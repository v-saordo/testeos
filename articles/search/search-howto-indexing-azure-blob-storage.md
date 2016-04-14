<properties
pageTitle="Indexación de Almacenamiento de blobs de Azure con Búsqueda de Azure"
description="Aprenda a indexar el Almacenamiento de blobs de Azure y a extraer el texto de los documentos con Búsqueda de Azure"
services="search"
documentationCenter=""
authors="chaosrealm"
manager="pablocas"
editor="" />

<tags
ms.service="search"
ms.devlang="rest-api"
ms.workload="search" ms.topic="article"  
ms.tgt_pltfrm="na"
ms.date="02/08/2016"
ms.author="eugenesh" />

# Indexación de documentos en Almacenamiento de blobs de Azure con Búsqueda de Azure

En este artículo se explica cómo usar Búsqueda de Azure para indexar documentos (como archivos PDF o de Office) almacenados en el Almacenamiento de blobs de Azure. El nuevo indexador de blob de Búsqueda de Azure agiliza y facilita este proceso.

> [AZURE.IMPORTANT] Actualmente, la versión de esta funcionalidad es una versión preliminar. Está disponible solo en la API de REST con la versión **2015-02-28-Preview**. Por favor, recuerde que las versiones preliminares de las API están pensadas para realizar pruebas y evaluar, y no deben usarse en entornos de producción.

## Configuración de la indexación de blob

Para instalar y configurar un indexador de Almacenamiento de blobs de Azure, puede usar la API de REST de Búsqueda de Azure para crear y administrar **indexadores** y **orígenes de datos**, como se describe en [este artículo](https://msdn.microsoft.com/library/azure/dn946891.aspx). En el futuro, se agregará la indexación de blobs para el SDK de .NET de Búsqueda de Azure y el Portal de Azure.

Un origen de datos especifica los datos que se deben indexar, las credenciales necesarias para obtener acceso a los mismos y las directivas que posibilitan que Búsqueda de Azure identifique cambios en los datos de forma eficiente (filas nuevas, modificadas o eliminadas). Un origen de datos se define como un recurso independiente para que puedan usarlo múltiples indexadores.

Un indexador es un recurso que conecta los orígenes de datos con los índices de búsqueda de destino.

Para configurar un indexador de blob, haga lo siguiente:

1. Cree un origen de datos de tipo `azureblob` que hace referencia a un contenedor (y de forma opcional, a una carpeta en ese contenedor) en una cuenta de almacenamiento de Azure
	- Transfiera la cadena de conexión de la cuenta de almacenamiento como parámetro `credentials.connectionString`
	- Especifique un nombre de contenedor. Opcionalmente, también puede incluir una carpeta mediante el parámetro `query`
2. Cree el indexador mediante la conexión de origen de datos con un índice de destino existente (cree el índice si aún no tiene uno)

En el ejemplo siguiente se ilustra este método.

	POST https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	    "name" : "blob-datasource",
	    "type" : "azureblob",
	    "credentials" : { "connectionString" : "<my storage connection string>" },
	    "container" : { "name" : "my-container", "query" : "my-folder" }
	}   

A continuación, cree un indexador que haga referencia al origen de datos y un índice de destino. Por ejemplo:

	POST https://[service name].search.windows.net/indexers?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	  "name" : "blob-indexer",
	  "dataSourceName" : "blob-datasource",
	  "targetIndexName" : "my-target-index",
	  "schedule" : { "interval" : "PT2H" }
	}


## Formatos de documento admitidos

El indexador de blob puede extraer texto de los siguientes formatos de documento:

- PDF
- Formatos de Microsoft Office: DOC/DOCX XLSX, XLS y PPTX/PPT, MSG (correos electrónicos de Outlook)  
- HTML
- XML
- ZIP
- EML
- Texto sin formato  

## Proceso de extracción de documentos

Búsqueda de Azure indexa cada documento (blob) como sigue:

- Se extrae el contenido del documento de texto completo en un campo de cadena denominado `content`. Tenga en cuenta que actualmente no se proporciona soporte para extraer varios documentos de un solo blob:
	- Por ejemplo, un archivo CSV está indexado como un solo documento.
	- Un documento compuesto o insertado (por ejemplo, un archivo ZIP o un documento de Word con insertados de correo electrónico de Outlook con un archivo PDF adjunto) también se indexa como un solo documento.

- Las propiedades de metadatos especificadas por el usuario en el blob, si las hubiera, se extraen textualmente. También pueden usarse para controlar ciertos aspectos del proceso de extracción de documento: consulte la sección [Uso de los metadatos personalizados para el control de la extracción de documentos](#CustomMetadataControl) para obtener más detalles.

- Las propiedades de metadatos de blob estándar se extraen en los campos siguientes:

	- **metadata\_storage\_name** (Edm.String): el nombre de archivo del blob. Por ejemplo, si tiene un blob /my-container/my-folder/subfolder/resume.pdf, el valor de este campo es `resume.pdf`.

	- **metadata\_storage\_path** (Edm.String): el URI completo del blob, incluida la cuenta de almacenamiento. Por ejemplo, `https://myaccount.blob.core.windows.net/my-container/my-folder/subfolder/resume.pdf`

	- **metadata\_storage\_content\_type** (Edm.String): tipo de contenido tal como especifica el código que usó para cargar el blob. Por ejemplo: `application/octet-stream`.

	- **metadata\_storage\_last\_modified** (Edm.DateTimeOffset): la última marca de tiempo modificada para el blob. Búsqueda de Azure usa esta marca de tiempo para identificar los blobs modificados, y de esta forma evitar volver a indexar todo después de la indexación inicial.

	- **metadata\_storage\_size** (Edm.Int64): tamaño del blob en bytes.

	- **metadata\_storage\_content\_md5** (Edm.String): hash MD5 del contenido del blob, si está disponible.

- Se extraen las propiedades de metadatos específicos de cada formato de documento en los campos enumerados [aquí](#ContentSpecificMetadata).

No es necesario definir campos para todas las propiedades anteriores en el índice de búsqueda, capture solo las propiedades que necesita para la aplicación.

> [AZURE.NOTE] A menudo, los nombres de campo en el índice existente serán diferentes de los nombres de campo generados durante la extracción del documento. Puede usar **asignaciones de campos** para asignar los nombres de propiedad proporcionados por Búsqueda de Azure a los nombres de campo en el índice de búsqueda. A continuación, verá un ejemplo del uso de las asignaciones de campos.

## Selección del campo de clave de documento y uso de diferentes nombres de campo

En Búsqueda de Azure, la clave del documento identifica de forma exclusiva a un documento. Cada índice de búsqueda tiene que tener exactamente un campo de clave del tipo Edm.String. El campo de clave es necesario para cada documento que se va a agregar al índice (de hecho, es el único campo obligatorio).
   
Debe considerar detenidamente qué campo extraído se debe asignar al campo de clave para el índice. Los candidatos son:

- **metadata\_storage\_name**: Este podría ser un candidato conveniente, pero tenga en cuenta lo siguiente (1) los nombres podrían no ser exclusivos, ya que puede que tenga blobs con el mismo nombre en carpetas diferentes y (2) el nombre puede contener caracteres que no son válidos para las claves de documento, como los guiones. Puede resolver el problema de los caracteres no válidos habilitando la opción `base64EncodeKeys` en las propiedades del indexador; si lo hace, no olvide codificar las claves de documento al transferirlas en llamadas API, tal como las búsquedas. (Por ejemplo, en .NET puede usar el [método UrlTokenEncode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) para ese fin).

- **metadata\_storage\_path**: El uso de la ruta de acceso completa garantiza la exclusividad, pero la ruta de acceso contiene siempre caracteres `/` que [no son válidos en una clave de documento](https://msdn.microsoft.com/library/azure/dn857353.aspx). Como anteriormente, tiene la opción de codificar las claves mediante la opción `base64EncodeKeys`.

- Si ninguna de las opciones anteriores le sirve, tiene la opción que le garantiza máxima flexibilidad que es la de agregar una propiedad de metadatos personalizada a los blobs. De todas formas, esta opción requiere que el proceso de carga de blob agregue dicha propiedad de metadatos a todos los blobs. Dado que la clave es una propiedad obligatoria, todos los blobs que no tengan esa propiedad no se indexarán.

> [AZURE.IMPORTANT] Si no hay ninguna asignación explícita para el campo de clave en el índice, Búsqueda de Azure usará automáticamente `metadata_storage_path` (la segunda opción anterior) como clave y habilitará la codificación de base 64 para las claves.

En este ejemplo, vamos a seleccionar el campo `metadata_storage_name` como clave de documento. Supongamos también que el índice tiene un campo de clave denominado `key` y un campo `fileSize` para almacenar el tamaño del documento. Para conectar las cosas como desee, especifique las siguientes asignaciones de campo al crear o actualizar el indexador:

	"fieldMappings" : [
	  { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key" },
	  { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
	]

Para conectar todo esto, aquí está la forma de agregar asignaciones de campo y habilitar la codificación de base 64 para las claves para un indexador ya existente:

	PUT https://[service name].search.windows.net/indexers/blob-indexer?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	  "dataSourceName" : " blob-datasource ",
	  "targetIndexName" : "my-target-index",
	  "schedule" : { "interval" : "PT2H" },
	  "fieldMappings" : [
	    { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key" },
	    { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
	  ],
	  "parameters" : { "base64EncodeKeys": true }
	}

> [AZURE.NOTE] Para más información sobre las asignaciones de campos, vea [este artículo](search-indexers-customization.md).

## Indexación incremental y detección de eliminación

Al configurar un indexador de blob para ejecutarlo en una programación, se vuelven a indexar solamente los blobs modificados, que vienen determinados por marca de tiempo `LastModified` del blob.

> [AZURE.NOTE] No tiene que especificar una directiva de detección de cambios, la indexación incremental ya está habilitada automáticamente.

Para indicar que determinados documentos tienen que quitarse del índice, debe usar una estrategia de eliminación temporal: en lugar de eliminar los blobs correspondientes, agregue una propiedad de metadatos personalizada para indicar que están eliminados y configure una directiva de detección de eliminación temporal en el origen de datos.

> [AZURE.WARNING] Si en lugar de usar una directiva de detección de eliminación, simplemente elimina los blobs, los documentos correspondientes no se eliminarán del índice de búsqueda.

Por ejemplo, la directiva que se muestra a continuación considerará que un blob se elimina si tiene una propiedad de metadatos `IsDeleted` con el valor `true`:

	PUT https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	    "name" : "blob-datasource",
	    "type" : "azureblob",
	    "credentials" : { "connectionString" : "<your storage connection string>" },
		"container" : { "name" : "my-container", "query" : "my-folder" },
		"dataDeletionDetectionPolicy" : {
			"@odata.type" :"#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy", 	
			"softDeleteColumnName" : "IsDeleted",
			"softDeleteMarkerValue" : "true"
		}
	}   

<a name="ContentSpecificMetadata"></a>
## Propiedades de metadatos específicas del tipo de contenido

La tabla siguiente resume el procesamiento que se realiza para cada formato de documento y describe las propiedades de metadatos extraídas por Búsqueda de Azure.

Formato de documento/Tipo de contenido | Propiedades de metadatos específicas del tipo de contenido | Detalles de procesamiento
-------------------------------|-------------------------------------------|-------------------
HTML (`text/html`) | `metadata_content_encoding`<br/>`metadata_content_type`<br/>`metadata_language`<br/>`metadata_description`<br/>`metadata_keywords`<br/>`metadata_title` | Seccionar el marcado HTML y extraer texto
PDF (`application/pdf`) | `metadata_content_type`<br/>`metadata_language`<br/>`metadata_author`<br/>`metadata_title`| Extraer texto, incluyendo los documentos insertados (excepto las imágenes)
DOCX (application/vnd.openxmlformats-officedocument.wordprocessingml.document) | `metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` | Extraer texto, incluyendo los documentos insertados
DOC (application/msword) | `metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` | Extraer texto, incluyendo los documentos insertados
XLSX (application/vnd.openxmlformats-officedocument.spreadsheetml.sheet) | `metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` | Extraer texto, incluyendo los documentos insertados
XLS (application/vnd.ms-excel) | `metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` | Extraer texto, incluyendo los documentos insertados
PPTX (application/vnd.openxmlformats-officedocument.presentationml.presentation) | `metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` | Extraer texto, incluyendo los documentos insertados
PPT (application/vnd.ms-powerpoint) | `metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` | Extraer texto, incluyendo los documentos insertados
MSG (application/vnd.ms-outlook) | `metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_message_bcc`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_subject` | Extraer texto, incluidos los datos adjuntos
ZIP (application/zip) | `metadata_content_type` | Extraer el texto de todos los documentos en el archivo
XML (application/xml) | `metadata_content_type`</br>`metadata_content_encoding`</br> | Seccionar el marcado XML y extraer texto
JSON (application/json) | `metadata_content_type`</br>`metadata_content_encoding` | Extraer texto<br/>NOTA: si necesita extraer varios campos de documentos de un blob JSON, vote [esta sugerencia de UserVoice](https://feedback.azure.com/forums/263029-azure-search/suggestions/11113539-extract-document-structure-from-json-blobs/)
EML (message/rfc822) | `metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_creation_date`<br/>`metadata_subject` | Extraer texto, incluidos los datos adjuntos
Plain text (text/plain) | `metadata_content_type`</br>`metadata_content_encoding`</br> | 

<a name="CustomMetadataControl"></a>
## Uso de los metadatos personalizados para el control de la extracción de documentos

Puede agregar propiedades de metadatos a un blob para controlar algunos aspectos de la indexación del blob y del proceso de extracción de documentos. En la actualidad se admiten las siguientes propiedades:

Nombre de propiedad | Valor de la propiedad | Explicación
--------------|----------------|------------
AzureSearch\_Skip | "true" | Da instrucciones al indexador del blob de omitir completamente el blob; no se intentará la extracción ni de metadatos ni de contenido. Esto es útil cuando desea omitir determinados tipos de contenido, o cuando un blob en particular falla varias veces e interrumpe el proceso de indexación.
AzureSearch\_SkipContent | "true" | Indica al indexador de blobs que solo indexe los metadatos y omita la extracción de contenido del blob. Esto es útil si el contenido del blob no es interesante, pero desea indexar los metadatos adjuntos al blob.

## Ayúdenos a mejorar Búsqueda de Azure

Si tiene solicitudes o ideas para mejorar las características, póngase en contacto con nosotros en nuestro [sitio UserVoice](https://feedback.azure.com/forums/263029-azure-search/).

<!---HONumber=AcomDC_0302_2016-->