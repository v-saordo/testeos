<properties
	pageTitle="Incorporación de la API de Box a las aplicaciones lógicas | Microsoft Azure"
	description="Información general de la API de Box con parámetros de la API de REST"
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

# Introducción a la API de Box
Conéctese a Box y cree y elimine archivos, entre muchas otras cosas. La API de Box puede utilizarse desde:

- Aplicaciones lógicas 

>[AZURE.NOTE] Esta versión del artículo se aplica a la versión de esquema 2015-08-01-preview de las aplicaciones lógicas. Para la versión de esquema 2014-12-01-preview, haga clic en [Conector de Box](../app-service-logic/app-service-logic-connector-box.md).

Con Box, puede hacer lo siguiente:

- Compilar el flujo de negocio en función de los datos que obtiene de Box. 
- Usar desencadenadores para cuando se crea o actualiza un archivo.
- Usar acciones que copian o eliminan archivos, entre otras muchas cosas. Estas acciones obtienen una respuesta y luego dejan el resultado a disposición de otras acciones. Por ejemplo, cuando se modifique un archivo en Box, puede tomar ese archivo y enviarlo por correo electrónico mediante Office 365.

Para agregar una operación en aplicaciones lógicas, consulte [Creación de una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Desencadenadores y acciones
Box incluye los siguientes desencadenadores y acciones.

| Desencadenadores | Acciones|
| --- | --- |
|<ul><li>Cuando se crea un archivo</li><li>Cuando se modifica un archivo</li></ul> | <ul><li>Crear archivo</li><li>Cuando se crea un archivo</li><li>Copiar archivo</li><li>Eliminar archivo</li><li>Extraer archivo en la carpeta</li><li>Obtener contenido de archivo mediante el identificador</li><li>Obtener contenido de archivo mediante la ruta de acceso</li><li>Obtener metadatos de archivo mediante el identificador</li><li>Obtener metadatos de archivo mediante la ruta de acceso</li><li>Actualizar archivo</li><li>Cuando se modifica un archivo</li></ul>

Todas las API admiten datos en formato JSON y XML.

## Creación de una conexión a Box
Cuando agregue esta API a las aplicaciones lógicas, debe autorizar a estas para que se conecten a su instancia de Box.

1. Inicie sesión en su cuenta de Box.
2. Seleccione **Authorize** (Autorizar) y permita que sus aplicaciones lógicas se conecten a Box y utilicen esta aplicación. 

Después de crear la conexión, escriba las propiedades de Box. En la **referencia de la API de REST** de este tema se describen estas propiedades.

>[AZURE.TIP] Puede usar esta misma conexión de Box en otras aplicaciones lógicas.

## Referencia de la API de REST de Swagger
Se aplica a la versión: 1.0.

### Crear archivo
Carga un archivo en Box. ```POST: /datasets/default/files```

| Nombre|Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|folderPath|cadena|Sí|query|None |Ruta de acceso de la carpeta para cargar el archivo en Box|
|name|cadena|Sí|query|None |Nombre del archivo que se va a crear en Box|
|body|string(binary) |Sí|body|None |Contenido del archivo que se va a cargar en Box|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Cuando se crea un archivo
Desencadena un flujo cuando se crea un nuevo archivo en una carpeta de Box. ```GET: /datasets/default/triggers/onnewfile```

| Nombre|Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|folderId|cadena|Sí|query|None |Identificador único de la carpeta en Box|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Copiar archivo
Copia un archivo en Box. ```POST: /datasets/default/copyFile```

| Nombre|Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|de origen|cadena|Sí|query|None |Dirección URL al archivo de origen|
|de destino|cadena|Sí|query| None|Ruta de acceso al archivo de destino en Box, incluido el nombre del archivo de destino|
|overwrite|boolean|No|query| None|Sobrescribe el archivo de destino si está establecido en 'true'|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Eliminar archivo
Elimina un archivo de Box. ```DELETE: /datasets/default/files/{id}```


| Nombre|Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|id|cadena|Sí|path|None |Identificador único del archivo que se va a eliminar de Box|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Extraer archivo en la carpeta
Extrae un archivo de almacenamiento en una carpeta de Box (ejemplo: .zip). ```POST: /datasets/default/extractFolderV2```

| Nombre|Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|de origen|cadena|Sí|query| |Ruta de acceso al archivo de almacenamiento|
|de destino|cadena|Sí|query| |Ruta de acceso de Box para extraer el contenido del archivo|
|overwrite|boolean|No|query| |Sobrescribe los archivos de destino si está establecido en 'true'|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Obtener contenido de archivo mediante el identificador
Recupera el contenido del archivo de Box mediante el identificador. ```GET: /datasets/default/files/{id}/content```

| Nombre|Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|id|cadena|Sí|path|None |Identificador único del archivo en Box|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Obtener contenido de archivo mediante la ruta de acceso
Recupera el contenido del archivo de Box mediante la ruta de acceso. ```GET: /datasets/default/GetFileContentByPath```

| Nombre|Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|path|cadena|Sí|query|None |Ruta de acceso única al archivo en Box|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Obtener metadatos de archivo mediante el identificador
Recupera los metadatos del archivo de Box mediante el identificador de archivo. ```GET: /datasets/default/files/{id}```

| Nombre|Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|id|cadena|Sí|path| None|Identificador único del archivo en Box|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Obtener metadatos de archivo mediante la ruta de acceso
Recupera los metadatos del archivo de Box mediante la ruta de acceso. ```GET: /datasets/default/GetFileByPath```

| Nombre|Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|path|cadena|Sí|query|None |Ruta de acceso única al archivo en Box|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Actualizar archivo
Actualiza un archivo en Box. ```PUT: /datasets/default/files/{id}```

| Nombre|Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|id|cadena|Sí|path| None|Identificador único del archivo que se va a actualizar en Box|
|body|string(binary) |Sí|body|None |Contenido del archivo que se va a actualizar en Box|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Cuando se modifica un archivo
Desencadena un flujo cuando se modifica un archivo en una carpeta de Box. ```GET: /datasets/default/triggers/onupdatedfile```

| Nombre|Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|folderId|cadena|Sí|query|None |Identificador único de la carpeta en Box|

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