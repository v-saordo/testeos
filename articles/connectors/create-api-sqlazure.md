<properties
	pageTitle="Incorporación de la API de SQL Azure en las aplicaciones lógicas | Microsoft Azure"
	description="Información general de la API de SQL Azure con parámetros de la API de REST"
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


# Introducción a la API de SQL Azure
Conectarse a SQL Azure para administrar las tablas y filas, como insertar filas, obtener tablas y mucho más.

La API de SQL Azure puede usarse desde:

- Aplicaciones lógicas 

>[AZURE.NOTE] Esta versión del artículo se aplica a la versión de esquema 2015-08-01-preview de las aplicaciones lógicas. Para la versión de esquema 01/12/2014-Versión preliminar, haga clic en [Conector de SQL](../app-service-logic/app-service-logic-connector-sql.md).

Con SQL Azure puede:

- Compilar el flujo de negocio en función de los datos que obtiene de SQL Azure. 
- Usar acciones para obtener una fila, insertar una fila y mucho más. Estas acciones obtienen una respuesta y luego dejan el resultado a disposición de otras acciones. Por ejemplo, puede obtener una fila de datos de SQL Azure y luego agregar datos a Excel. 

Para agregar una operación en aplicaciones lógicas, consulte [Creación de una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).


## Desencadenadores y acciones
SQL incluye las siguientes acciones. No hay desencadenadores.

Desencadenadores | Acciones
--- | ---
None | <ul><li>Obtener fila</li><li>Obtener filas</li><li>Insertar fila</li><li>Eliminar fila</li><li>Obtener tablas</li><li>Actualizar fila</li></ul>

Todas las API admiten datos en formato JSON y XML.

## Creación de la conexión a SQL
Cuando agregue esta API a las aplicaciones lógicas, escriba los valores siguientes:

|Propiedad| Obligatorio|Descripción|
| ---|---|---|
|Cadena de conexión SQL|Sí|Escriba la cadena de conexión de SQL Azure|

Después de crear la conexión, especifique las propiedades de SQL, como el nombre de tabla. En la **referencia de la API de REST** de este tema se describen estas propiedades.

>[AZURE.TIP] Puede usar esta conexión en otras aplicaciones lógicas.

## Referencia de la API de REST de Swagger
Se aplica a la versión: 1.0.

### Obtener fila 
Recupera una sola fila de una tabla SQL. ```GET: /datasets/default/tables/{table}/items/{id}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|table|cadena|yes|path|Ninguna|Nombre de la tabla SQL|
|id|cadena|yes|path|Ninguna|Identificador único de la fila que se va a recuperar|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Obtener filas 
Recupera filas de una tabla SQL. ```GET: /datasets/default/tables/{table}/items```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|table|cadena|yes|path|Ninguna|Nombre de la tabla SQL|
|$skip|integer|no|query|Ninguna|Número de entradas para omitir (valor predeterminado = 0)|
|$top|integer|no|query|Ninguna|Número máximo de entradas para recuperar (valor predeterminado = 256)|
|$filter|cadena|no|query|Ninguna|Consulta de filtro de ODATA para restringir el número de entradas|
|$orderby|cadena|no|query|Ninguna|Consulta orderBy de ODATA para especificar el orden de las entradas|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Insertar fila 
Inserta una nueva fila en una tabla SQL. ```POST: /datasets/default/tables/{table}/items```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|table|cadena|yes|path|Ninguna|Nombre de la tabla SQL|
|item|ItemInternalId: string|yes|body|Ninguna|Fila que se va a insertar en la tabla especificada en SQL|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Eliminar fila 
Elimina una fila de una tabla SQL. ```DELETE: /datasets/default/tables/{table}/items/{id}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|table|cadena|yes|path|Ninguna|Nombre de la tabla SQL|
|id|cadena|yes|path|Ninguna|Identificador único de la fila que se va a eliminar|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Obtener tablas 
Recupera tablas de una base de datos SQL. ```GET: /datasets/default/tables```

No hay parámetros para esta llamada.

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Actualizar fila 
Actualiza una fila existente en una tabla SQL. ```PATCH: /datasets/default/tables/{table}/items/{id}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|table|cadena|yes|path|Ninguna|Nombre de la tabla SQL|
|id|cadena|yes|path|Ninguna|Identificador único de la fila que se va a actualizar|
|item|ItemInternalId: string|yes|body|Ninguna|Fila con valores actualizados|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|

## Definiciones de objeto

#### DataSetsMetadata

|Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|tabular|not defined|no|
|blob|not defined|no|

#### TabularDataSetsMetadata

|Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|de origen|cadena|no|
|DisplayName|cadena|no|
|urlEncoding|cadena|no|
|tableDisplayName|cadena|no|
|tablePluralName|cadena|no|

#### BlobDataSetsMetadata

|Nombre de propiedad | Tipo de datos |Obligatorio |
|---|---|---|
|de origen|cadena|no|
|DisplayName|cadena|no|
|urlEncoding|cadena|no|

#### TableMetadata

|Nombre de propiedad | Tipo de datos |Obligatorio |
|---|---|---|
|name|cadena|no|
|título|cadena|no|
|x-ms-permission|cadena|no|
|schema|not defined|no|

#### DataSetsList

|Nombre de propiedad | Tipo de datos |Obligatorio |
|---|---|---|
|value|array|no|

#### DataSet

|Nombre de propiedad | Tipo de datos |Obligatorio |
|---|---|---|
|Nombre|cadena|no|
|DisplayName|cadena|no|

#### Tabla

|Nombre de propiedad | Tipo de datos |Obligatorio |
|---|---|---|
|Nombre|cadena|no|
|DisplayName|cadena|no|

#### Elemento

|Nombre de propiedad | Tipo de datos |Obligatorio |
|---|---|---|
|ItemInternalId|cadena|no|

#### ItemsList

|Nombre de propiedad | Tipo de datos |Obligatorio |
|---|---|---|
|value|array|no|

#### TablesList

|Nombre de propiedad | Tipo de datos |Obligatorio |
|---|---|---|
|value|array|no|


## Pasos siguientes

[Crear una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

<!---HONumber=AcomDC_0302_2016-->