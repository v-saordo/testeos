<properties
pageTitle="Incorporación de la API de Salesforce a PowerApps Enterprise y a las aplicaciones lógicas| Microsoft Azure"
description="Información general de la API de Salesforce con parámetros de la API de REST"
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
ms.author="deonhe"/>

# Introducción a la API de Salesforce
Conéctese a Salesforce y cree objetos, obtenga objetos, etc. La API de Salesforce puede usarse desde:

- PowerApps 
- Aplicaciones lógicas 

>[AZURE.NOTE] Esta versión del artículo se aplica a la versión de esquema 2015-08-01-preview de las aplicaciones lógicas. Para la versión de esquema 2014-12-01-Versión preliminar, haga clic en [API de Salesforce](../app-service-logic/app-service-logic-connector-salesforce.md).

Con Salesforce, puede:

- Compilar el flujo de negocio en función de los datos que obtiene de SalesForce. 
- Usar desencadenadores para cuando se crea o actualiza un objeto.
- Usar acciones para crear un objeto, eliminar un objeto, etc. Estas acciones obtienen una respuesta y luego dejan el resultado a disposición de otras acciones. Por ejemplo, cuando se crea un nuevo objeto en Salesforce, puede enviar un correo electrónico mediante Office 365.
- Agregue la API de Salesforce a PowerApps Enterprise. Así los usuarios pueden usar esta API en sus aplicaciones. 

Si desea información sobre cómo agregar una API en PowerApps Enterprise, acuda a [Registro de una API administrada por Microsoft o una API administrada por TI](../power-apps/powerapps-register-from-available-apis.md).

Para agregar una operación en aplicaciones lógicas, consulte [Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Desencadenadores y acciones
La API de Salesforce incluye los siguientes desencadenadores y acciones.

| Desencadenadores | Acciones|
| --- | --- |
|<ul><li>Cuando se crea un objeto</li><li>Cuando se modifica un objeto</li></ul> | <ul><li>Crear objeto</li><li>Obtener objetos</li><li>Cuando se crea un objeto</li><li>Cuando se modifica un objeto</li><li>Eliminar objeto</li><li>Obtener objeto</li><li>Obtener tipos de objeto (SObjects)</li><li>Actualizar objeto</li></ul>

Todas las API admiten datos en formato JSON y XML.

## Creación de una conexión a Salesforce 

### Incorporación de una configuración adicional en PowerApps
Cuando agregue Salesforce a PowerApps Enterprise, escriba los valores **Clave de aplicación** y **Secreto de la aplicación** de la aplicación de Salesforce. El valor **URL de redireccionamiento** también se usa en la aplicación de Salesforce. Si no tiene una aplicación de Salesforce, puede usar los siguientes pasos para crearla:

1. [Inicie sesión en la página principal del desarrollador de Salesforce][5], seleccione su perfil y seleccione **Setup (Configuración)**: ![Página principal de Salesforce][6]

3. Seleccione **Crear** y, a continuación, **Aplicaciones**. En la página **Apps** (Aplicaciones), en **Connected Apps** (Aplicaciones conectadas), seleccione **New** (Nueva): ![Aplicación de creación de Salesforce][7]

4. En **Nueva aplicación conectada**:

	1. Escriba el valor para **Connected App Name** (Nombre de la aplicación conectada).  
	2. Escriba el valor para **API Name** (Nombre de la API).  
	3. Escriba el valor para **Contact Email** (Correo electrónico de contacto).  
	4. En _API (Enable OAuth Settings)_ (API: habilitar configuración de OAuth), seleccione **Enable OAuth Settings** (Habilitar configuración de OAuth) y establezca **Callback URL** (Dirección URL de devolución de llamada) en el valor mostrado al agregar la nueva API de Salesforce en el Portal de Azure.  

5. En _Ámbitos de OAuth seleccionados_, agregue los siguientes ámbitos a los **Ámbitos de OAuth seleccionados**:

	- Acceder y administrar los datos de Chatter (chatter\_api)
	- Acceder y administrar los datos (api)
	- Permitir el acceso a su identificador único (openid)
	- Realizar solicitudes en su nombre en cualquier momento (refresh\_token, offline\_access)

6. **Guarde** los cambios: ![Aplicación nueva de Salesforce][8]

Ahora, copie y pegue los valores **Clave de aplicación** y **Secreto de aplicación** en la configuración de Salesforce en el Portal de Azure.

### Incorporación de una configuración adicional en aplicaciones lógicas
Cuando agregue esta API a las aplicaciones lógicas, debe autorizar a estas para que se conecten a Salesforce.

1. Inicie sesión en su cuenta de Salesforce.
2. Permita que las aplicaciones lógicas se conecten a su cuenta de Salesforce y la usen. 

Después de crear la conexión, especifique las propiedades de Salesforce, como el nombre de tabla. En la **referencia de la API de REST** de este tema se describen estas propiedades.

>[AZURE.TIP] Puede usar esta misma conexión en otras aplicaciones lógicas.

## Referencia de API de REST de Swagger
Se aplica a la versión: 1.0.


### Crear objeto
Crea un objeto de Salesforce. ```POST: /datasets/default/tables/{table}/items```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|table|cadena|yes|path|Ninguna|Tipo SObject de Salesforce (ejemplo: 'Lead')|
|item| |yes|body|Ninguna|Objeto de Salesforce para crear|

### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|



### Obtener objeto
Recupera un objeto de Salesforce. ```GET: /datasets/default/tables/{table}/items/{id}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|table|cadena|yes|path|Ninguna|Tipo SObject de Salesforce (ejemplo: 'Lead')|
|id|cadena|yes|path|Ninguna|Identificador único del objeto de Salesforce para recuperar|

### Respuesta

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|



### Eliminar objeto
Elimina un objeto de Salesforce. ```DELETE: /datasets/default/tables/{table}/items/{id}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|table|cadena|yes|path|Ninguna|Tipo SObject de Salesforce (ejemplo: 'Lead')|
|id|cadena|yes|path|Ninguna|Identificador único del objeto de Salesforce para eliminar|

### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|



### Actualizar objeto
Actualiza un objeto de Salesforce. ```PATCH: /datasets/default/tables/{table}/items/{id}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|table|cadena|yes|path|Ninguna|Tipo SObject de Salesforce (ejemplo: 'Lead')|
|id|cadena|yes|path|Ninguna|Identificador único del objeto de Salesforce para actualizar|
|item| |yes|body|Ninguna|Objeto de Salesforce con propiedades cambiadas|

### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|



### Cuando se crea un objeto
Desencadena un flujo cuando se crea un objeto en Salesforce. ```GET: /datasets/default/tables/{table}/onnewitems```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|table|cadena|yes|path|Ninguna|Tipo SObject de Salesforce (ejemplo: 'Lead')|
|$skip|integer|no|query|Ninguna|Número de entradas para omitir (valor predeterminado = 0)|
|$top|integer|no|query|Ninguna|Número máximo de entradas para recuperar (valor predeterminado = 256)|
|$filter|cadena|no|query|Ninguna|Consulta de filtro de ODATA para restringir el número de entradas|
|$orderby|cadena|no|query|Ninguna|Consulta orderBy de ODATA para especificar el orden de las entradas|

### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|



### Cuando se modifica un objeto 
Desencadena un flujo cuando se modifica un objeto en Salesforce. ```GET: /datasets/default/tables/{table}/onupdateditems```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|table|cadena|yes|path|Ninguna|Tipo SObject de Salesforce (ejemplo: 'Lead')|
|$skip|integer|no|query|Ninguna|Número de entradas para omitir (valor predeterminado = 0)|
|$top|integer|no|query|Ninguna|Número máximo de entradas para recuperar (valor predeterminado = 256)|
|$filter|cadena|no|query|Ninguna|Consulta de filtro de ODATA para restringir el número de entradas|
|$orderby|cadena|no|query|Ninguna|Consulta orderBy de ODATA para especificar el orden de las entradas|

### Respuesta
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


#### TableMetadata

| Nombre | Tipo de datos | Obligatorio|
|---|---|---|
|name|cadena|no|
|título|cadena|no|
|x-ms-permission|cadena|no|
|schema|not defined|no|


#### DataSetsList

| Nombre | Tipo de datos | Obligatorio|
|---|---|---|
|value|array|no|


#### DataSet

| Nombre | Tipo de datos | Obligatorio|
|---|---|---|
|Nombre|cadena|
|DisplayName|cadena|no|


#### Tabla

| Nombre | Tipo de datos | Obligatorio|
|---|---|---|
|Nombre|cadena|no|
|DisplayName|cadena|no|


#### Elemento

| Nombre | Tipo de datos | Obligatorio|
|---|---|---|
|ItemInternalId|cadena|no|


#### ItemsList

| Nombre | Tipo de datos | Obligatorio|
|---|---|---|
|value|array|no|


#### TablesList

| Nombre | Tipo de datos | Obligatorio|
|---|---|---|
|value|array|no|


## Pasos siguientes
Después de agregar la API de Salesforce a PowerApps Enterprise, [conceda permisos a los usuarios](../power-apps/powerapps-manage-api-connection-user-access.md) para usar dicha API en sus aplicaciones.

[Creación de una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).


[5]: https://developer.salesforce.com
[6]: ./media/create-api-salesforce/salesforce-developer-homepage.png
[7]: ./media/create-api-salesforce/salesforce-create-app.png
[8]: ./media/create-api-salesforce/salesforce-new-app.png

<!---HONumber=AcomDC_0302_2016-->