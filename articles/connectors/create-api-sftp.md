<properties
	pageTitle="Incorporación de la API de SFTP en las aplicaciones lógicas | Microsoft Azure"
	description="Información general de la API de SFTP con parámetros de la API de REST"
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

# Introducción a la API de SFTP
Conéctese a un servidor SFTP para administrar sus archivos. Puede realizar diferentes tareas en el servidor SFTP, como cargar archivos, eliminar archivos, etc. La API de SFTP se puede usar desde:

- Aplicaciones lógicas

>[AZURE.NOTE] Esta versión del artículo se aplica a la versión de esquema 2015-08-01-preview de las aplicaciones lógicas. Para la versión de esquema 2014-12-01-Versión preliminar, haga clic en [Conector de SFTP](../app-service-logic/app-service-logic-connector-sftp.md).

Con SFTP, puede:

- Compilar el flujo de negocio en función de los datos que obtiene de SFTP. 
- Usar un desencadenador cuando se actualiza un archivo.
- Usar acciones que crean archivos, eliminan archivos, etc. Estas acciones obtienen una respuesta y luego dejan el resultado a disposición de otras acciones. Por ejemplo, puede obtener el contenido de un archivo y después actualizar una base de datos SQL. 

Para agregar una operación en aplicaciones lógicas, consulte [Creación de una nueva aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).


## Desencadenadores y acciones
La API de SFTP tiene los siguientes desencadenadores y acciones disponibles.

Desencadenadores | Acciones
--- | ---
<ul><li>Cuando se crea o modifica un archivo</li></ul> | <ul><li>Crear archivo</li><li>Copiar archivo</li><li>Eliminar archivo</li><li>Extraer carpeta</li><li>Obtener contenido de archivo</li><li>Obtener contenido de archivo mediante la ruta de acceso</li><li>Obtener metadatos de archivo</li><li>Obtener metadatos de archivo mediante la ruta de acceso</li><li>Actualizar archivo</li><li>Cuando se crea o modifica un archivo</li></ul>

Todas las API admiten datos en formato JSON y XML.


## Creación de una conexión a SFTP
Cuando agregue esta API a las aplicaciones lógicas, escriba los valores siguientes:

|Propiedad| Obligatorio|Descripción|
| ---|---|---|
|Dirección del servidor host| Sí | Escriba el dominio completo (FQDN) o la dirección IP del servidor SFTP.|
|Nombre de usuario| Sí | Escriba el nombre de usuario para conectarse al servidor SFTP.|
|Password | Sí | Escriba la contraseña del nombre de usuario.|
|Huella digital de la tecla del host del servidor SSH | Sí | Especifique la huella digital de la tecla del host pública para el servidor SSH. <br/><br/>Normalmente, el administrador del servidor puede proporcionarle esta clave. También puede utilizar las herramientas ```WinSCP``` o ```ssh-keygen-g3 -F``` para obtener la huella digital de la clave. | 

Después de crear la conexión, especifique las propiedades de SFTP, como la ruta de acceso a la carpeta o el archivo. En la **referencia de la API de REST** de este tema se describen estas propiedades.

>[AZURE.TIP] Puede usar esta misma conexión de SFTP en otras aplicaciones lógicas.


## Referencia de la API de REST de Swagger
Se aplica a la versión: 1.0.

### Crear archivo
Carga un archivo en SFTP. ```POST: /datasets/default/files```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|folderPath|cadena|yes|query|Ninguna |Ruta de acceso única de la carpeta en SFTP|
|name|cadena|yes|query| Ninguna|Nombre del archivo|
|body|string(binary) |yes|body|Ninguna |Contenido del archivo que se va a crear en SFTP|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|

### Copiar archivo
Copia un archivo en SFTP. ```POST: /datasets/default/copyFile```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|de origen|cadena|yes|query| Ninguna|Ruta de acceso al archivo de origen|
|de destino|cadena|yes|query|Ninguna |Ruta de acceso al archivo de destino, incluido el nombre de archivo|
|overwrite|boolean|no|query|Ninguna|Sobrescribe el archivo de destino si está establecido en 'true'|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|

### Eliminar archivo 
Elimina un archivo en SFTP. ```DELETE: /datasets/default/files/{id}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|id|cadena|yes|path|Ninguna |Identificador único del archivo en SFTP|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|

### Extraer carpeta
Extrae un archivo de almacenamiento en una carpeta mediante SFTP (ejemplo: .zip) ```POST: /datasets/default/extractFolderV2```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|de origen|cadena|yes|query|Ninguna |Ruta de acceso al archivo de almacenamiento|
|de destino|cadena|yes|query|Ninguna |Ruta de acceso a la carpeta de destino|
|overwrite|boolean|no|query|Ninguna|Sobrescribe los archivos de destino si está establecido en 'true'|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|

### Obtener contenido de archivo
Recupera el contenido del archivo desde SFTP mediante el identificador. ```GET: /datasets/default/files/{id}/content```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|id|cadena|yes|path|Ninguna |Identificador único del archivo en SFTP|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Obtener contenido de archivo mediante la ruta de acceso
Recupera el contenido del archivo desde SFTP mediante la ruta de acceso. ```GET: /datasets/default/GetFileContentByPath```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|path|cadena|yes|query| Ninguna|Ruta de acceso única del archivo en SFTP|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Obtener metadatos de archivo 
Recupera los metadatos de archivo de SFTP mediante el identificador de archivo. ```GET: /datasets/default/files/{id}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|id|cadena|yes|path| Ninguna|Identificador único del archivo en SFTP|

#### Respuesta
| Nombre | Descripción |
| --- | --- |
| 200 | OK | 
| default | Error en la operación.


### Obtener metadatos de archivo mediante la ruta de acceso
Recupera los metadatos del archivo de SFTP mediante la ruta de acceso. ```GET: /datasets/default/GetFileByPath```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|path|cadena|yes|query|Ninguna |Ruta de acceso única del archivo en SFTP|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Actualizar archivo
Actualiza el contenido del archivo mediante SFTP. ```PUT: /datasets/default/files/{id}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|id|cadena|yes|path|Ninguna |Identificador único del archivo en SFTP|
|body|string(binary) |yes|body| Ninguna|Contenido del archivo que se va a actualizar en SFTP|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Cuando se crea o modifica un archivo 
Desencadena un flujo al modificar un archivo en SFTP. ```GET: /datasets/default/triggers/onupdatedfile```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|folderId|cadena|yes|query|Ninguna |Identificador único de la carpeta|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


## Definiciones de objeto

#### DataSetsMetadata

| Nombre | Tipo de datos | Obligatorio|
|---|---|---|
|tabular|not defined|no|
|blob|not defined|no|

#### TabularDataSetsMetadata

| Nombre | Tipo de datos | Obligatorio|
|---|---|---|
|de origen|cadena|no|
|DisplayName|cadena|no|
|urlEncoding|cadena|no|
|tableDisplayName|cadena|no|
|tablePluralName|cadena|no|

#### BlobDataSetsMetadata

| Nombre | Tipo de datos | Obligatorio|
|---|---|---|
|de origen|cadena|no|
|DisplayName|cadena|no|
|urlEncoding|cadena|no|

#### BlobMetadata

| Nombre | Tipo de datos | Obligatorio|
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