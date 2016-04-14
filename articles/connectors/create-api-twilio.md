<properties
pageTitle="Incorporación de la API de Twilio en las Aplicaciones lógicas | Microsoft Azure"
description="Información general de la API de Twilio con parámetros de la API de REST"
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
ms.date="02/23/2016"
ms.author="mandia"/>

# Introducción a la API de Twilio

Conectarse a Twilio para enviar y recibir mensajes SMS, MMS y IP globales.

>[AZURE.NOTE] Esta versión del artículo se aplica a la versión de esquema 2015-08-01-preview de aplicaciones lógicas. Para la versión de esquema de vista previa de 01/12/2014, haga clic en [Twilio](../app-service-logic/app-service-logic-connector-twilio.md).

Con Twilio, puede:

- Compilar el flujo de negocio en función de los datos que obtiene de Twilio. 
- Usar acciones que obtienen un mensaje, enumeran mensajes y mucho más. Estas acciones obtienen una respuesta y luego dejan el resultado a disposición de otras acciones. Por ejemplo, cuando reciba un nuevo mensaje de Twilio, puede tomar este mensaje y usarlo como flujo de trabajo de Bus de servicio. 

Para agregar una operación en aplicaciones lógicas, consulte [Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Desencadenadores y acciones
La API de Twilio incluye las siguientes acciones. No hay desencadenadores.

| Desencadenadores | Acciones|
| --- | --- |
|None| <ul><li>Obtener mensaje</li><li>Enumerar mensajes</li><li>Enviar mensaje</li></ul>|

Todas las API admiten datos en formato JSON y XML.

## Creación de una conexión a Twilio
Cuando agregue esta API a las aplicaciones lógicas, escriba los valores Twilio siguientes:

|Propiedad| Obligatorio|Descripción|
| ---|---|---|
|Id. de cuenta|Sí|Escriba el identificador de cuenta de Twilio|
|Token de acceso|Sí|Escriba el token de acceso de Twilio|

Consulte [Twilio](https://www.twilio.com/docs/api/ip-messaging/guides/identity) para crear un token de acceso.

Después de crear la conexión, escriba las propiedades de Twilio. En la **referencia de la API de REST** de este tema se describen estas propiedades.

>[AZURE.TIP] Puede usar esta misma conexión de Twilio en otras aplicaciones lógicas.

## Referencia de API de REST de Swagger
#### Esta documentación corresponde a la versión: 1.0

### Obtener mensaje
Devuelve un único mensaje especificado por el identificador de mensaje proporcionado. ```GET: /Messages/{MessageId}.json```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|MessageId|cadena|yes|path|Ninguna|Id. de mensaje|

### Respuesta
|Nombre|Descripción|
|---|---|
|200|Operación correcta|
|400|Bad Request|
|404|Mensaje no encontrado|
|500|Error interno del servidor. Error desconocido|
|default|Error en la operación.|


### Enumerar mensajes
Devuelve una lista de mensajes asociados a su cuenta. ```GET: /Messages.json```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|Para|cadena|no|query|Ninguna|Al número de teléfono|
|De|cadena|no|query|Ninguna|Desde número de teléfono|
|DateSent|cadena|no|query|Ninguna|Mostrar solo los mensajes enviados en esa fecha (en formato GMT), como AAAA-MM-DD. Ejemplo: DateSent=2009-07-06. También puede especificar la desigualdad, como DateSent <=AAAA-MM-DD para los mensajes enviados en o antes de medianoche en una fecha y DateSent>=AAAA-MM-DD para los mensajes enviados en o después de medianoche en una fecha.|
|PageSize|integer|no|query|50|La cantidad de recursos para devolver en cada página de lista. Valor predeterminado: 50|
|Page|integer|no|query|0|Número de página. El valor predeterminado es 0.|

### Respuesta
|Nombre|Descripción|
|---|---|
|200|Operación correcta|
|400|Bad Request|
|500|Error interno del servidor. Error desconocido|
|default|Error en la operación.|



### Enviar mensaje
Envíe un nuevo mensaje a un número de teléfono móvil. ```POST: /Messages.json```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|sendMessageRequest| |yes|body|Ninguna|Mensaje para enviar|

### Respuesta
|Nombre|Descripción|
|---|---|
|200|Operación correcta|
|400|Bad Request|
|500|Error interno del servidor. Error desconocido|
|default|Error en la operación.|


## Definiciones de objeto

#### SendMessageRequest: modelo de solicitud para la operación de envío de mensajes

|Nombre de propiedad | Tipo de datos | Obligatorio|
|---|---|---|
|from|cadena|yes|
|to|cadena|yes|
|body|cadena|yes|
|media\_url|array|no|
|status\_callback|cadena|no|
|messaging\_service\_sid|cadena|no|
|application\_sid|cadena|no|
|max\_price|cadena|no|


#### Message: modelo de mensaje

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|body|cadena|no|
|from|cadena|no|
|to|cadena|no|
|status|cadena|no|
|sid|cadena|no|
|account\_sid|cadena|no|
|api\_version|cadena|no|
|num\_segments|cadena|no|
|num\_media|cadena|no|
|date\_created|cadena|no|
|date\_sent|cadena|no|
|date\_updated|cadena|no|
|dirección|cadena|no|
|error\_code|cadena|no|
|error\_message|cadena|no|
|price|cadena|no|
|price\_unit|cadena|no|
|uri|cadena|no|
|subresource\_uris|array|no|
|messaging\_service\_sid|cadena|no|

#### MessageList: modelo de respuesta para la operación de enumeración de mensajes

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|messages|array|no|
|page|integer|no|
|page\_size|integer|no|
|num\_pages|integer|no|
|uri|cadena|no|
|first\_page\_uri|cadena|no|
|next\_page\_uri|cadena|no|
|total|integer|no|
|previous\_page\_uri|cadena|no|

#### IncomingPhoneNumberList: modelo de respuesta para la operación de enumeración de mensajes

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|incoming\_phone\_numbers|array|no|
|page|integer|no|
|page\_size|integer|no|
|num\_pages|integer|no|
|uri|cadena|no|
|first\_page\_uri|cadena|no|
|next\_page\_uri|cadena|no|


#### AddIncomingPhoneNumberRequest: modelo de solicitud para la operación de agregar un número entrante

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|PhoneNumber|cadena|yes|
|AreaCode|cadena|no|
|FriendlyName|cadena|no|


#### IncomingPhoneNumber: número de teléfono entrante

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|phone\_number|cadena|no|
|friendly\_name|cadena|no|
|sid|cadena|no|
|account\_sid|cadena|no|
|date\_created|cadena|no|
|date\_updated|cadena|no|
|capabilities|not defined|no|
|status\_callback|cadena|no|
|status\_callback\_method|cadena|no|
|api\_version|cadena|no|


#### Capabilities: funcionalidades de número de teléfono

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|mms|boolean|no|
|sms|boolean|no|
|voice|boolean|no|

#### AvailablePhoneNumbers: números de teléfono disponibles

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|phone\_number|cadena|no|
|friendly\_name|cadena|no|
|lata|cadena|no|
|latitude|cadena|no|
|longitude|cadena|no|
|postal\_code|cadena|no|
|rate\_center|cadena|no|
|region|cadena|no|
|MMS|boolean|no|
|SMS|boolean|no|
|voice|boolean|no|


#### UsageRecords: clase de registros de uso

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|categoría|cadena|no|
|usage|cadena|no|
|usage\_unit|cadena|no|
|description|cadena|no|
|price|número|no|
|price\_unit|cadena|no|
|count|cadena|no|
|count\_unit|cadena|no|
|start\_date|cadena|no|
|end\_date|cadena|no|


## Pasos siguientes
[Creación de una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0224_2016-->