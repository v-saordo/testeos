<properties
	pageTitle="Incorporación de la API de Google Drive a PowerApps Enterprise o aplicaciones lógicas | Microsoft Azure"
	description="Información general de la API de Google Drive con parámetros de la API de REST"
	services=""
    suite=""
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

# Introducción a la API de Google Drive
Conéctese a Google Drive para crear archivos, obtener filas, etc. La API de Google Drive puede usarse desde:

- PowerApps 
- Aplicaciones lógicas 

Con Google Drive, puede:

- Compilar el flujo de negocio en función de los datos obtenidos de la búsqueda. 
- Usar acciones para buscar imágenes, noticias, etc. Estas acciones obtienen una respuesta y luego dejan el resultado a disposición de otras acciones. Por ejemplo, puede buscar un vídeo y luego usar Twitter para publicar ese vídeo en una fuente de Twitter.
- Agregar la API de Google Drive a PowerApps Enterprise. Así los usuarios pueden usar esta API en sus aplicaciones. 

Para obtener información acerca de cómo agregar una API en PowerApps Enterprise, vaya a [Registro de una API administrada por Microsoft o una API administrada por TI](../power-apps/powerapps-register-from-available-apis.md).

Para agregar una operación a las aplicaciones lógicas, consulte [Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS](../app-service-logic/app-service-logic-create-a-logic-app.md).


## Desencadenadores y acciones
Google Drive incluye las siguientes acciones. No hay desencadenadores.

Desencadenadores | Acciones
--- | ---
None | <ul><li>Crear archivo</li><li>Insertar fila</li><li>Copiar archivo</li><li>Eliminar archivo</li><li>Eliminar fila</li><li>Extraer archivo en carpeta</li><li>Obtener contenido de archivo mediante identificador</li><li>Obtener contenido de archivo mediante ruta de acceso</li><li>Obtener metadatos de archivo mediante identificador</li><li>Obtener metadatos de archivo mediante ruta de acceso</li><li>Obtener fila</li><li>Actualizar archivo</li><li>Actualizar fila</li></ul>

Todas las API admiten datos en formato JSON y XML.


## Creación de la conexión a Google Drive

### Incorporación de una configuración adicional en PowerApps
Al agregar Google Drive a PowerApps Enterprise, especifique los valores de **Clave de la aplicación** y **Secreto de la aplicación** de la aplicación de Google Drive. El valor de **URL de redireccionamiento** también se usa en la aplicación de Google. Si no tiene una aplicación de Google Drive, puede usar los siguientes pasos para crearla:

1. Inicie sesión en [Google Developers Console][5] y seleccione **Create an empty project** (Crear un proyecto vacío): ![Consola de desarrolladores de Google][6]

2. Escriba las propiedades de la aplicación y seleccione **Create** (Crear).
3. Seleccione **Use Google APIs** (Usar APIs de Google): ![Usar api de google][8]  
4. En Overview (Visión general), seleccione **Drive API** (API de Drive): ![Información general de la API de Google Drive][9]  
5. Seleccione **Habilitar API**: ![Habilitar la API de Google Drive][10]  
6. Al habilitar la API de Drive, seleccione **Credenciales** y, a continuación, **Id. de cliente de OAuth 2.0**: ![Agregar credenciales][12]  
7. Seleccione **Configurar pantalla de consentimiento**.
8. En **OAuth consent screen** (Pantalla de autorización de OAuth), especifique un **nombre de producto** y seleccione **Save** (Guardar): ![Configurar pantalla de consentimiento][13]  
9. En la página de creación del Id. de cliente:  

	1. En **Tipo de aplicación**, seleccione **Aplicación web**.
	2. Escriba un nombre para el cliente.
	3. Escriba el valor de la dirección URL de redirección que se muestra cuando se agregó a la API de Google Drive en el Portal de Azure.
	4. Seleccione **Crear**.  

	![Crear Id. de cliente][14]

11. Se mostrarán el identificador y el secreto de cliente de la aplicación registrada.

Ahora, copie y pegue los valores de **App Key** (Clave de aplicación) y **App Secret** (Secreto de aplicación) en la configuración de la API de Google Drive del Portal de Azure.


### Incorporación de una configuración adicional en aplicaciones lógicas
Cuando agregue esta API a las aplicaciones lógicas, debe autorizar a estas para que se conecten a Google Drive.

1. Inicie sesión en su cuenta de Google Drive.
2. Permita que las aplicaciones lógicas se conecten a Google Drive y lo usen. 

Después de crear la conexión, especifique las propiedades de Google Drive, como la ruta de acceso a la carpeta o el nombre de archivo. La **referencia de la API de REST** de este tema describe estas propiedades.

>[AZURE.TIP] Puede usar esta misma conexión de Google Drive en otras aplicaciones lógicas.


## Referencia de la API de REST de Swagger
Se aplica a la versión: 1.0.

### Crear archivo    
Carga un archivo en Google Drive. ```POST: /datasets/default/files```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|folderPath|cadena|yes|query|Ninguna |Ruta de acceso de carpeta para cargar el archivo en Google Drive|
|name|cadena|yes|query|Ninguna |Nombre del archivo que se va a crear en Google Drive|
|body|string(binary) |yes|body| Ninguna|Contenido del archivo que se va a cargar en Google Drive|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Insertar fila    
Inserta una fila en una hoja de Google. ```POST: /datasets/{dataset}/tables/{table}/items```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|dataset|cadena|yes|path| Ninguna|Identificador único del archivo de hoja de Google|
|table|cadena|yes|path|Ninguna |Identificador único de la hoja de cálculo|
|item|ItemInternalId: string |yes|body|Ninguna |Fila que se va a insertar en la hoja especificada|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Copiar archivo    
Copia un archivo en Google Drive. ```POST: /datasets/default/copyFile```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|de origen|cadena|yes|query| Ninguna|Dirección URL al archivo de origen|
|de destino|cadena|yes|query|Ninguna |Ruta de acceso al archivo de destino en Google Drive, incluido el nombre de archivo de destino|
|overwrite|boolean|no|query|Ninguna |Sobrescribe el archivo de destino si está establecido en 'true'|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Eliminar archivo    
Elimina un archivo de Google Drive. ```DELETE: /datasets/default/files/{id}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|id|cadena|yes|path|Ninguna |Identificador único del archivo que se va a eliminar de Google Drive|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Eliminar fila    
Elimina una fila de una hoja de Google. ```DELETE: /datasets/{dataset}/tables/{table}/items/{id}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|dataset|cadena|yes|path|Ninguna |Identificador único del archivo de hoja de Google|
|table|cadena|yes|path|Ninguna |Identificador único de la hoja de cálculo|
|id|cadena|yes|path|Ninguna |Identificador único de la fila que se va a eliminar|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Extraer archivo en la carpeta    
Extrae un archivo de almacenamiento en una carpeta de Google Drive (por ejemplo: .zip). ```POST: /datasets/default/extractFolderV2```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|de origen|cadena|yes|query|Ninguna |Ruta de acceso al archivo de almacenamiento|
|de destino|cadena|yes|query|Ninguna |Ruta de acceso de Google Drive para extraer el contenido del archivo|
|overwrite|boolean|no|query|Ninguna |Sobrescribe los archivos de destino si está establecido en 'true'|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Obtener contenido de archivo mediante el identificador    
Recupera el contenido del archivo de Google Drive mediante el identificador.```GET: /datasets/default/files/{id}/content```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|id|cadena|yes|path|Ninguna |Identificador único del archivo que se va a recuperar en Google Drive|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Obtener contenido de archivo mediante la ruta de acceso    
Recupera el contenido del archivo de Google Drive mediante la ruta de acceso. ```GET: /datasets/default/GetFileContentByPath```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|path|cadena|yes|query|Ninguna |Ruta de acceso del archivo de Google Drive|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Obtener metadatos de archivo mediante el identificador    
Recupera los metadatos del archivo de Google Drive mediante el identificador. ```GET: /datasets/default/files/{id}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|id|cadena|yes|path|Ninguna |Identificador único del archivo en Google Drive|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Obtener metadatos de archivo mediante la ruta de acceso    
Recupera los metadatos del archivo de Google Drive mediante la ruta de acceso. ```GET: /datasets/default/GetFileByPath```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|path|cadena|yes|query|Ninguna |Ruta de acceso del archivo de Google Drive|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Obtener fila    
Recupera una sola fila de una hoja de Google. ```GET: /datasets/{dataset}/tables/{table}/items/{id}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|dataset|cadena|yes|path|Ninguna |Identificador único del archivo de hoja de Google|
|table|cadena|yes|path|Ninguna |Identificador único de la hoja de cálculo|
|id|cadena|yes|path| Ninguna|Identificador único de la fila que se va a recuperar|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Actualizar archivo    
Actualiza un archivo en Google Drive. ```PUT: /datasets/default/files/{id}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|id|cadena|yes|path|Ninguna |Identificador único del archivo que se va a actualizar en Google Drive|
|body|string(binary) |yes|body| Ninguna|Contenido del archivo que se va a cargar en Google Drive|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Actualizar fila    
Actualiza una fila en una hoja de Google. ```PATCH: /datasets/{dataset}/tables/{table}/items/{id}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|dataset|cadena|yes|path|Ninguna |Identificador único del archivo de hoja de Google|
|table|cadena|yes|path| Ninguna|Identificador único de la hoja de cálculo|
|id|cadena|yes|path|Ninguna |Identificador único de la fila que se va a actualizar|
|item|ItemInternalId: string |yes|body|Ninguna |Fila con valores actualizados|

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

#### TableMetadata

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|name|cadena|no|
|título|cadena|no|
|x-ms-permission|cadena|no|
|schema|not defined|no|

#### TablesList

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|value|array|no|

#### Tabla

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|Nombre|cadena|no|
|DisplayName|cadena|no|

#### Elemento

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|ItemInternalId|cadena|no|

#### ItemsList

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|value|array|no|


## Pasos siguientes
Después de agregar Google Drive a PowerApps Enterprise, [conceda a los usuarios los permisos necesarios](../power-apps/powerapps-manage-api-connection-user-access.md) para usar la API en sus aplicaciones.

[Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS](../app-service-logic/app-service-logic-create-a-logic-app.md).


<!--References-->
[5]: https://console.developers.google.com/
[6]: ./media/create-api-googledrive/google-developers-console.png
[8]: ./media/create-api-googledrive/use-google-apis.png
[9]: ./media/create-api-googledrive/googledrive-api-overview.png
[10]: ./media/create-api-googledrive/enable-googledrive-api.png
[12]: ./media/create-api-googledrive/googledrive-api-credentials-add.png
[13]: ./media/create-api-googledrive/configure-consent-screen.png
[14]: ./media/create-api-googledrive/create-client-id.png

<!---HONumber=AcomDC_0302_2016-->