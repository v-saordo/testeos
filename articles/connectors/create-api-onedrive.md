<properties
pageTitle="Incorporación de la API de OneDrive a PowerApps Enterprise y a las aplicaciones lógicas| Microsoft Azure"
description="Información general de la API de OneDrive con parámetros de la API de REST"
services=""	
documentationCenter="" 	
authors="msftman"	
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

# Introducción a la API de OneDrive

Conéctese a OneDrive para administrar los archivos, incluyendo las tareas de carga, obtención y eliminación de archivos, y muchas más. La API de OneDrive puede usarse desde:

- PowerApps 
- Aplicaciones lógicas 

>[AZURE.NOTE] Esta versión del artículo se aplica a la versión de esquema 2015-08-01-preview de las aplicaciones lógicas. Para la versión de esquema 2014-12-01 Versión preliminar, haga clic en [API de OneDrive](../app-service-logic/app-service-logic-connector-onedrive.md).

Con OneDrive, puede:

- Compilar el flujo de negocio en función de los datos que obtiene de OneDrive. 
- Usar desencadenadores para cuando se crea o actualiza un archivo.
- Usar acciones para crear o eliminar un archivo, entre otras muchas cosas. Estas acciones obtienen una respuesta y luego dejan el resultado a disposición de otras acciones. Por ejemplo, cuando se crea un nuevo archivo en OneDrive, puede enviar ese archivo por correo electrónico mediante Office 365.
- Agregue la API de OneDrive a PowerApps Enterprise. Así los usuarios pueden usar esta API en sus aplicaciones. 

Si desea información sobre cómo agregar una API en PowerApps Enterprise, acuda a [Registro de una API administrada por Microsoft o una API administrada por TI](../power-apps/powerapps-register-from-available-apis.md).

Para agregar una operación en aplicaciones lógicas, consulte [Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Desencadenadores y acciones
La API de OneDrive incluye los siguientes desencadenadores y acciones.

| Desencadenadores | Acciones|
| --- | --- |
|<ul><li>Cuando se crea un archivo</li><li>Cuando se modifica un archivo</li></ul> | <ul><li>Crear archivo</li><li>Enumerar archivos de una carpeta</li><li>Cuando se crea un archivo</li><li>Copiar archivo</li><li>Eliminar archivo</li><li>Extraer carpeta</li><li>Obtener contenido de archivo mediante el identificador</li><li>Obtener contenido de archivo mediante la ruta de acceso</li><li>Obtener metadatos de archivo mediante el identificador</li><li>Obtener metadatos de archivo mediante la ruta de acceso</li><li>Enumerar carpeta raíz</li><li>Actualizar archivo</li><li>Cuando se modifica un archivo</li></ul>

Todas las API admiten datos en formato JSON y XML.

## Creación de una conexión a OneDrive

### Incorporación de una configuración adicional en PowerApps
Cuando agregue OneDrive a PowerApps Enterprise, escriba los valores **Clave de aplicación** y **Secreto de la aplicación** de la aplicación de OneDrive. El valor **URL de redireccionamiento** también se usa en la aplicación de OneDrive. Si no tiene una aplicación de OneDrive, puede usar los siguientes pasos para crearla:

1. Vaya a la [página de creación de la aplicación][5] en _Centro para desarrolladores de la Cuenta Microsoft_ e inicie sesión con su _Cuenta Microsoft_.

2. Escriba su **Nombre de la aplicación** acepte el contrato:

	![Nueva aplicación OneDrive][6]

3. En la configuración:

	1. Seleccione **Configuración de API**.  
	2. Establezca **URL de redireccionamiento** en el valor mostrado al agregar la nueva API de OneDrive en el Portal de Azure.  
	3. **Guarde** los cambios.  

	![Configuración de la API de la aplicación OneDrive][7]

Ahora, copie y pegue los valores **Clave de aplicación** y **Secreto de aplicación** en la configuración de OneDrive en el Portal de Azure.

### Incorporación de una configuración adicional en aplicaciones lógicas
Cuando agregue esta API a las aplicaciones lógicas, debe autorizar a estas para que se conecten a OneDrive.

1. Inicie sesión en su cuenta de OneDrive.
2. Permita que las aplicaciones lógicas se conecten y usen su OneDrive. 

Después de crear la conexión, especifique las propiedades de OneDrive, como la ruta de acceso a la carpeta o el nombre de archivo. En la **referencia de la API de REST** de este tema se describen estas propiedades.

>[AZURE.TIP] Puede usar esta misma conexión en otras aplicaciones lógicas.

## Referencia de la API de REST de Swagger
Se aplica a la versión: 1.0.


### Obtener metadatos de archivo mediante el identificador
Recupera los metadatos de un archivo de OneDrive mediante el identificador. ```GET: /datasets/default/files/{id}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|id|cadena|yes|path|Ninguna|Identificador único del archivo en OneDrive|

### Respuestas
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Actualizar archivo
Actualiza un archivo en OneDrive. ```PUT: /datasets/default/files/{id}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|id|cadena|yes|path|Ninguna|Identificador único del archivo que se va a actualizar en OneDrive|
|body| |yes|body|Ninguna|Contenido del archivo que se va a actualizar en OneDrive|


### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|

### Eliminar archivo
Elimina un archivo de OneDrive. ```DELETE: /datasets/default/files/{id}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|id|cadena|yes|path|Ninguna|Identificador único del archivo que se va a eliminar de OneDrive|


### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Obtener metadatos de archivo mediante la ruta de acceso
Recupera los metadatos de un archivo de OneDrive mediante la ruta de acceso. ```GET: /datasets/default/GetFileByPath```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|path|cadena|yes|query|Ninguna|Ruta de acceso única al archivo en OneDrive|


### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|




### Obtener contenido de archivo mediante la ruta de acceso
Recupera el contenido de un archivo en OneDrive mediante la ruta de acceso. ```GET: /datasets/default/GetFileContentByPath```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|path|cadena|yes|query|Ninguna|Ruta de acceso única al archivo en OneDrive|


### Respuesta

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|




### Obtener contenido de archivo mediante el identificador
Recupera el contenido de un archivo en OneDrive mediante el identificador. ```GET: /datasets/default/files/{id}/content```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|id|cadena|yes|path|Ninguna|Identificador único del archivo en OneDrive|


### Respuesta

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|




### Crear archivo
Carga un archivo en OneDrive. ```POST: /datasets/default/files```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|folderPath|cadena|yes|query|Ninguna|Ruta de acceso de carpeta para cargar el archivo en OneDrive|
|name|cadena|yes|query|Ninguna|Nombre del archivo que se va a crear en OneDrive|
|body| |yes|body|Ninguna|Contenido del archivo que se va a cargar en OneDrive|


### Respuesta

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|



### Copiar archivo
Copia un archivo en OneDrive. ```POST: /datasets/default/copyFile```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|de origen|cadena|yes|query|Ninguna|Dirección URL al archivo de origen|
|de destino|cadena|yes|query|Ninguna|Ruta de acceso al archivo de destino en OneDrive, incluido el nombre de archivo de destino|
|overwrite|boolean|no|query|false|Sobrescribe el archivo de destino si está establecido en 'true'|


### Respuesta

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|



### Cuando se crea un archivo
Desencadena un flujo cuando se crea un nuevo archivo en una carpeta de OneDrive. ```GET: /datasets/default/triggers/onnewfile```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|folderId|cadena|yes|query|Ninguna|Identificador único de la carpeta en OneDrive|


### Respuesta

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|



### Desencadena un flujo al modificar un archivo en una carpeta de OneDrive
Desencadena un flujo cuando se modifica un archivo en una carpeta de OneDrive. ```GET: /datasets/default/triggers/onupdatedfile```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|folderId|cadena|yes|query|Ninguna|Identificador único de la carpeta en OneDrive|


### Respuesta

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|



### Extraer carpeta
Extrae una carpeta a OneDrive. ```POST: /datasets/default/extractFolderV2```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|de origen|cadena|yes|query|Ninguna|Ruta de acceso al archivo de almacenamiento|
|de destino|cadena|yes|query|Ninguna|Ruta de acceso de OneDrive para extraer el contenido del archivo|
|overwrite|boolean|no|query|false|Sobrescribe los archivos de destino si está establecido en 'true'|


### Respuesta

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
Después de agregar la API de OneDrive a PowerApps Enterprise, [conceda permisos a los usuarios](../power-apps/powerapps-manage-api-connection-user-access.md) para usar dicha API en sus aplicaciones.

[Creación de una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).


[5]: https://account.live.com/developers/applications/create
[6]: ./media/create-api-onedrive/onedrive-new-app.png
[7]: ./media/create-api-onedrive/onedrive-app-api-settings.png

<!-----HONumber=AcomDC_0302_2016-->