<properties
pageTitle="Agregar la API de Excel a PowerApps Enterprise | Microsoft Azure"
description="Información general de la API de Excel con parámetros de la API de REST"
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

# Introducción a la API de Excel

Conéctese a Excel para insertar o eliminar una fila y mucho más. La API de Excel se puede usar desde:

- PowerApps

Con Excel, puede:

- Agregar la API de Excel a PowerApps Enterprise. A continuación, los usuarios pueden usar esta API en sus aplicaciones. 

Para obtener información sobre cómo agregar una API en PowerApps Enterprise, vaya a [Registro de una API administrada por Microsoft o una API administrada por TI](../power-apps/powerapps-register-from-available-apis.md).

## Desencadenadores y acciones
Excel incluye la siguiente acción. No hay ningún desencadenador.

|Desencadenador|Acciones|
|--- | ---|
|None | <ul><li>Obtener filas</li><li>Insertar fila</li><li>Eliminar fila</li><li>Obtener fila</li><li>Obtener tablas</li><li>Actualizar fila</li></ul>

Todas las API admiten datos en formato JSON y XML.

## Referencia de la API de REST de Swagger
Se aplica a la versión: 1.0.

### Inserta una fila nueva en una tabla de Excel
```POST: /datasets/{dataset}/tables/{table}/items```



| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|dataset|cadena|yes|path|Ninguna|Nombre del archivo de Excel|
|table|cadena|yes|path|Ninguna|Nombre de la tabla de Excel|
|item| |yes|body|Ninguna|Fila que se va a insertar en la tabla especificada en Excel|


### Respuesta

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|




### Recupera una sola fila de una tabla de Excel
```GET: /datasets/{dataset}/tables/{table}/items/{id}```



| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|dataset|cadena|yes|path|Ninguna|Nombre del archivo de Excel|
|table|cadena|yes|path|Ninguna|Nombre de la tabla de Excel|
|id|cadena|yes|path|Ninguna|Identificador único de la fila que se va a recuperar|


### Respuesta

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|




### Elimina una fila de una tabla de Excel
```DELETE: /datasets/{dataset}/tables/{table}/items/{id}```



| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|dataset|cadena|yes|path|Ninguna|Nombre del archivo de Excel|
|table|cadena|yes|path|Ninguna|Nombre de la tabla de Excel|
|id|cadena|yes|path|Ninguna|Identificador único de la fila que se va a eliminar|


### Respuesta

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|




### Actualiza una fila existente en una tabla de Excel
```PATCH: /datasets/{dataset}/tables/{table}/items/{id}```



| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|dataset|cadena|yes|path|Ninguna|Nombre del archivo de Excel|
|table|cadena|yes|path|Ninguna|Nombre de la tabla de Excel|
|id|cadena|yes|path|Ninguna|Identificador único de la fila que se va a actualizar|
|item| |yes|body|Ninguna|Fila con valores actualizados|


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

| Nombre | Tipo de datos |Obligatorio|
|---|---|---|
|de origen|cadena|no|
|DisplayName|cadena|no|
|urlEncoding|cadena|no|
|tableDisplayName|cadena|no|
|tablePluralName|cadena|no|

#### BlobDataSetsMetadata

| Nombre | Tipo de datos |Obligatorio|
|---|---|---|
|de origen|cadena|no|
|DisplayName|cadena|no|
|urlEncoding|cadena|no|

#### TableMetadata

| Nombre | Tipo de datos |Obligatorio|
|---|---|---|
|name|cadena|no|
|título|cadena|no|
|x-ms-permission|cadena|no|
|schema|not defined|no|

#### DataSetsList

| Nombre | Tipo de datos |Obligatorio|
|---|---|---|
|value|array|no|

#### DataSet

| Nombre | Tipo de datos |Obligatorio|
|---|---|---|
|Nombre|cadena|no|
|DisplayName|cadena|no|

#### Tabla

| Nombre | Tipo de datos |Obligatorio|
|---|---|---|
|Nombre|cadena|no|
|DisplayName|cadena|no|

#### Elemento

| Nombre | Tipo de datos |Obligatorio|
|---|---|---|
|ItemInternalId|cadena|no|

#### TablesList

| Nombre | Tipo de datos |Obligatorio|
|---|---|---|
|value|array|no|

#### ItemsList

| Nombre | Tipo de datos |Obligatorio|
|---|---|---|
|value|array|no|


## Pasos siguientes
[Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS](../app-service-logic/app-service-logic-create-a-logic-app.md) [¿Qué es Microsoft PowerApps Enterprise?](../power-apps/powerapps-get-started-azure-portal.md)

<!---HONumber=AcomDC_0302_2016-->