<properties
	pageTitle="Incorporación de la API de FTP en las aplicaciones lógicas | Microsoft Azure"
	description="Información general de la API de FTP con parámetros de la API de REST"
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

# Introducción a la API de FTP
Conéctese a un servidor FTP administrar los archivos, lo que incluye tareas como cargar archivos, eliminar archivos, etc. La API de FTP se puede usar desde:

- Aplicaciones lógicas

>[AZURE.NOTE] Esta versión del artículo se aplica a la versión de esquema 2015-08-01-preview de las aplicaciones lógicas. Para la versión de esquema 2014-12-01-preview, haga clic en [Conector de FTP](../app-service-logic/app-service-logic-connector-ftp.md).

Con FTP, puede:

- Compilar el flujo de negocio en función de los datos que obtiene de FTP. 
- Usar un desencadenador cuando se actualiza un archivo.
- Usar acciones que crean archivos, obtienen contenido de archivos, etc. Estas acciones obtienen una respuesta y luego dejan el resultado a disposición de otras acciones. Por ejemplo, puede obtener el contenido de un archivo y después actualizar una base de datos SQL. 

Para agregar una operación a las aplicaciones lógicas, consulte [Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS](../app-service-logic/app-service-logic-create-a-logic-app.md).


## Desencadenadores y acciones
FTP tiene los siguientes desencadenadores y acciones disponibles.

Desencadenadores | Acciones
--- | ---
<ul><li>Obtiene un archivo actualizado</li></ul> | <ul><li>Crear archivo</li><li>Copiar archivo</li><li>Eliminar archivo</li><li>Extraer carpeta</li><li>Obtener contenido de archivo</li><li>Obtener contenido de archivo mediante ruta de acceso</li><li>Obtener metadatos de archivo</li><li>Obtener metadatos de archivo mediante ruta de acceso</li><li>Obtener un archivo actualizado</li><li>Actualizar archivo</li></ul>

Todas las API admiten datos en formato JSON y XML.

## Creación de una conexión a FTP
Cuando agregue esta API a las aplicaciones lógicas, escriba los valores siguientes:

|Propiedad| Obligatorio|Descripción|
| ---|---|---|
|Dirección del servidor| Sí | Escriba el dominio completo (FQDN) o la dirección IP del servidor FTP.|
|Nombre de usuario| Sí | Escriba el nombre de usuario para conectarse al servidor FTP.|
|Password | Sí | Escriba la contraseña del nombre de usuario.|

Después de crear la conexión, especifique las propiedades de FTP, como el archivo de origen o la carpeta de destino. La **referencia de la API de REST** de este tema describe estas propiedades.

>[AZURE.TIP] Puede usar esta misma conexión de FTP en otras aplicaciones lógicas.

## Referencia de la API de REST de Swagger
Se aplica a la versión: 1.0.

### Crear archivo
Carga un archivo en el servidor FTP. ```POST: /datasets/default/files```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|folderPath|cadena|yes|query|Ninguna |Ruta de acceso de carpeta para cargar el archivo en el servidor FTP|
|name|cadena|yes|query| Ninguna|Nombre del archivo que se va a crear en el servidor FTP|
|body| |yes|body|Ninguna |Contenido del archivo que se va a cargar en el servidor FTP|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|

### Copiar archivo
Copia un archivo en el servidor FTP. ```POST: /datasets/default/copyFile```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|de origen|cadena|yes|query|Ninguna |Dirección URL al archivo de origen|
|de destino|cadena|yes|query|Ninguna |Ruta de acceso al archivo de destino en el servidor FTP, incluido el nombre de archivo de destino|
|overwrite|boolean|no|query|Ninguna |Sobrescribe el archivo de destino si está establecido en 'true'|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|

### Eliminar archivo 
Elimina un archivo del servidor FTP. ```DELETE: /datasets/default/files/{id}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|id|cadena|yes|path|Ninguna |Identificador único del archivo que se va a eliminar del servidor FTP|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|

### Extraer carpeta
Extrae un archivo de almacenamiento en una carpeta del servidor FTP (por ejemplo: .zip). ```POST: /datasets/default/extractFolderV2```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|de origen|cadena|yes|query| Ninguna|Ruta de acceso al archivo de almacenamiento|
|de destino|cadena|yes|query| Ninguna|Ruta de acceso a la carpeta de destino|
|overwrite|boolean|no|query|Ninguna|Sobrescribe los archivos de destino si está establecido en 'true'|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|

### Obtener contenido de archivo
Recupera el contenido del archivo del servidor FTP mediante el identificador. ```GET: /datasets/default/files/{id}/content```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|id|cadena|yes|path|Ninguna |Identificador único del archivo|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Obtener contenido de archivo mediante la ruta de acceso
Recupera el contenido del archivo del servidor FTP mediante la ruta de acceso. ```GET: /datasets/default/GetFileContentByPath```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|path|cadena|yes|query|Ninguna |Ruta de acceso única al archivo en el servidor FTP|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Obtener metadatos de archivo 
Recupera los metadatos del archivo del servidor FTP mediante el identificador de archivo. ```GET: /datasets/default/files/{id}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|id|cadena|yes|path|Ninguna|Identificador único del archivo|

#### Respuesta
| Nombre | Descripción |
| --- | --- |
| 200 | OK | 
| default | Error en la operación.


### Obtener metadatos de archivo mediante la ruta de acceso
Recupera los metadatos del archivo del servidor FTP mediante la ruta de acceso. ```GET: /datasets/default/GetFileByPath```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|path|cadena|yes|query| Ninguna|Ruta de acceso única al archivo en el servidor FTP|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Obtener un archivo actualizado
Obtiene un archivo actualizado. ```GET: /datasets/default/triggers/onupdatedfile```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|folderId|cadena|yes|query|Ninguna |Identificador de la carpeta en la que se va a buscar un archivo actualizado|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Actualizar archivo 
Actualiza un archivo en el servidor FTP. ```PUT: /datasets/default/files/{id}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|id|cadena|yes|path| Ninguna|Identificador único del archivo que se va a actualizar en el servidor FTP|
|body| |yes|body|Ninguna |Contenido del archivo que se va a actualizar en el servidor FTP|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


## Definiciones de objeto

#### DataSetsMetadata

| Nombre | Tipo de datos | Obligatorio |
|---|---|---|
|tabular|not defined|no|
|blob|not defined|no|

#### TabularDataSetsMetadata

| Nombre | Tipo de datos | Obligatorio |
|---|---|---|
|de origen|cadena|no|
|DisplayName|cadena|no|
|urlEncoding|cadena|no|
|tableDisplayName|cadena|no|
|tablePluralName|cadena|no|

#### BlobDataSetsMetadata

| Nombre | Tipo de datos | Obligatorio |
|---|---|---|
|de origen|cadena|no|
|DisplayName|cadena|no|
|urlEncoding|cadena|no|

#### BlobMetadata

| Nombre | Tipo de datos | Obligatorio |
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

[Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0302_2016-->