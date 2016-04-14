<properties
	pageTitle="Incorporación de la API de Almacenamiento de blobs de Azure en las Aplicaciones lógicas | Microsoft Azure"
	description="Información general sobre la API de Almacenamiento de blobs de Azure con los parámetros de la API de REST"
	services=""
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
   ms.date="02/25/2016"
   ms.author="mandia"/>

# Introducción a la API de Almacenamiento de blobs de Azure
Conéctese a un blob de Azure para administrar los archivos de un contenedor de blobs, por ejemplo, para crear o eliminar archivos, entre otras muchas cosas. La API de Almacenamiento de blobs de Azure puede utilizarse desde:

- Aplicaciones lógicas 

>[AZURE.NOTE] Esta versión del artículo se aplica a la versión de esquema 2015-08-01-preview de las aplicaciones lógicas. Para la versión de esquema 2014-12-01-preview, haga clic en [Conector de blobs de Almacenamiento de Azure](../app-service-logic/app-service-logic-connector-azurestorageblob.md).

Con Almacenamiento de blobs de Azure, puede:

- Compilar el flujo de negocio en función de los datos que se obtienen del blob. 
- Usar acciones que le permiten crear un archivo, obtener contenido del archivo y mucho más. Estas acciones obtienen una respuesta y luego dejan el resultado a disposición de otras acciones. Por ejemplo, puede buscar "urgente" en un archivo en el blob y, a continuación, enviar todos los archivos con "urgente" en un correo electrónico mediante Office 365. 

Para agregar una operación en aplicaciones lógicas, consulte [Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Desencadenadores y acciones
Blob de Azure incluye las siguientes acciones. No hay desencadenadores.

| Desencadenadores | Acciones|
| --- | --- |
| Ninguno. | <ul><li>Crear archivo</li><li>Copiar archivo</li><li>Eliminar archivo</li><li>Extraer archivo en la carpeta</li><li>Obtener contenido del archivo</li><li>Obtener contenido del archivo mediante la ruta de acceso</li><li>Obtener metadatos del archivo</li><li>Obtener metadatos del archivo mediante</li><li>Actualizar archivo</li></ul> |

Todas las API admiten datos en formato JSON y XML.

## Creación de una conexión a un blob de Azure
Cuando agregue esta API a las aplicaciones lógicas, escriba los siguientes valores de la cuenta de almacenamiento:

|Propiedad| Obligatorio|Descripción|
| ---|---|---|
|Nombre de la cuenta de almacenamiento de Azure | yes | El nombre de la cuenta de Almacenamiento de blobs|
|La clave de acceso de la cuenta de Almacenamiento de Azure | yes | La clave de acceso a su cuenta de Almacenamiento de blobs|

Después de crear la conexión, escriba las propiedades del blob, como el nombre de archivo o la ruta de acceso de carpeta. En la **referencia de la API de REST** de este tema se describen estas propiedades.

>[AZURE.TIP] Puede usar esta misma conexión de blob en otras aplicaciones lógicas.
 

## Referencia de API de REST de Swagger
Se aplica a la versión: 1.0.

### Crear archivo
Carga un archivo a Almacenamiento de blobs de Azure. ```POST: /datasets/default/files```

| Nombre|Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|folderPath|cadena|yes|query| Ninguna |Ruta de acceso de la carpeta para cargar el archivo en el Almacenamiento de blobs de Azure|
|name|cadena|yes|query|Ninguna |Nombre del archivo que se va a crear en Almacenamiento de blobs de Azure|
|body|string(binary) |yes|body|Ninguna|Contenido del archivo que se va a cargar en el Almacenamiento de blobs de Azure|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|

### Copiar archivo
Copia un archivo en Almacenamiento de blobs de Azure. ```POST: /datasets/default/copyFile```

| Nombre|Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|de origen|cadena|yes|query|Ninguna |Dirección URL al archivo de origen|
|de destino|cadena|yes|query| Ninguna|Ruta de acceso del archivo de destino en Almacenamiento de blobs de Azure, incluido el nombre de archivo de destino|
|overwrite|boolean|no|query|Ninguna |Sobrescribe el archivo de destino si está establecido en 'true'|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Eliminar archivo
Elimina un archivo de Almacenamiento de blobs de Azure. ```DELETE: /datasets/default/files/{id}```

| Nombre|Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|id|cadena|yes|path|Ninguna |Identificador único del archivo que se va a eliminar del Almacenamiento de blobs de Azure|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Extraer archivo en la carpeta
Extrae un archivo de almacenamiento en una carpeta de Almacenamiento de blobs de Azure (ejemplo: .zip). ```POST: /datasets/default/ExtractFolderV2```

| Nombre|Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|de origen|cadena|yes|query| Ninguna|Ruta de acceso al archivo de almacenamiento|
|de destino|cadena|yes|query|Ninguna |Ruta de acceso en Almacenamiento de blobs de Azure para extraer el contenido del archivo|
|overwrite|boolean|no|query|Ninguna |Sobrescribe los archivos de destino si está establecido en 'true'|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Obtener contenido de archivo
Recupera contenido de archivo de Almacenamiento de blobs de Azure mediante el identificador. ```GET: /datasets/default/files/{id}/content```

| Nombre|Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|id|cadena|yes|path|Ninguna|Identificador único del archivo|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Obtener contenido de archivo mediante la ruta de acceso
Recupera el contenido de archivo de Almacenamiento de blobs de Azure mediante la ruta de acceso. ```GET: /datasets/default/GetFileContentByPath```

| Nombre|Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|path|cadena|yes|query|Ninguna |Ruta de acceso única al archivo en Almacenamiento de blobs de Azure|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Obtener metadatos de archivo
Recupera los metadatos de archivo de Almacenamiento de blobs de Azure mediante el identificador de archivo. ```GET: /datasets/default/files/{id}```

| Nombre|Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|id|cadena|yes|path|Ninguna |Identificador único del archivo|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Obtener metadatos de archivo mediante la ruta de acceso
Recupera los metadatos de archivo de Almacenamiento de blobs de Azure mediante la ruta de acceso. ```GET: /datasets/default/GetFileByPath```

| Nombre|Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|path|cadena|yes|query|Ninguna|Ruta de acceso única al archivo en Almacenamiento de blobs de Azure|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Actualizar archivo
Actualiza un archivo en Almacenamiento de blobs de Azure. ```PUT: /datasets/default/files/{id}```

| Nombre|Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|id|cadena|yes|path|Ninguna |Identificador único del archivo que se va a actualizar en Almacenamiento de blobs de Azure|
|body|string(binary) |yes|body|Ninguna |Contenido del archivo que se va a actualizar en Almacenamiento de blobs de Azure|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|

## Definiciones de objeto

#### DataSetsMetadata

|Nombre de propiedad | Tipo de datos | Obligatorio|
|---|---|---|
|tabular|not defined|no|
|blob|not defined|no|

#### TabularDataSetsMetadata

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|de origen|cadena|no|
|DisplayName|cadena|no|
|urlEncoding|cadena|no|
|tableDisplayName|cadena|no|
|tablePluralName|cadena|no|

#### BlobDataSetsMetadata

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|de origen|cadena|no|
|DisplayName|cadena|no|
|urlEncoding|cadena|no|


#### BlobMetadata

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|Id|cadena|no|
|Nombre|cadena|no|
|DisplayName|cadena|no|
|Ruta de acceso|cadena|no|
|LastModified|cadena|no|
|Tamaño|integer|no|
|MediaType|cadena|no|
|IsFolder|boolean|no|
|ETag|cadena|no|
|FileLocator|cadena|no|

## Pasos siguientes

[Creación de una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

<!---HONumber=AcomDC_0302_2016-->