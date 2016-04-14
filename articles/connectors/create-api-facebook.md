<properties
	pageTitle="Incorporación de la API de Facebook en las Aplicaciones lógicas | Microsoft Azure"
	description="Información general de la API de Facebook con parámetros de la API de REST"
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

# Introducción a la API de Facebook
Conéctese a Facebook y publique en una biografía, obtenga una fuente de página y mucho más. La API de Facebook se puede usar desde:

- Aplicaciones lógicas 

>[AZURE.NOTE] Esta versión del artículo se aplica a la versión de esquema 2015-08-01-preview de las aplicaciones lógicas. Para la versión de esquema 2014-12-01-preview, haga clic en [Introducción al conector de Facebook y su incorporación a una aplicación lógica](../app-service-logic/app-service-logic-connector-facebook.md).


Con Facebook, puede hacer lo siguiente:

- Compilar el flujo de negocio en función de los datos que obtiene de Facebook. 
- Utilizar un desencadenador cuando se reciba un nuevo mensaje.
- Usar acciones para publicar en la biografía, obtener una fuente de página y mucho más. Estas acciones obtienen una respuesta y luego dejan el resultado a disposición de otras acciones. Por ejemplo, cuando haya un nuevo mensaje en su biografía, puede tomar ese mensaje e insertarlo en su fuente de Twitter. 

Para agregar una operación a las aplicaciones lógicas, consulte [Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Desencadenadores y acciones
La API de Facebook incluye los siguientes desencadenadores y acciones.

| Desencadenadores | Acciones|
| --- | --- |
| <ul><li>Cuando haya un nuevo mensaje en mi escala de tiempo</li></ul> |<ul><li>Obtener fuente de mi escala de tiempo</li><li>Publicar en mi escala de tiempo</li><li>Cuando haya un nuevo mensaje en mi escala de tiempo</li><li>Obtener fuente de página</li><li>Obtener escala de tiempo de usuario</li><li>Publicar en página</li></ul>

Todas las API admiten datos en formato JSON y XML.

## Creación de una conexión a Facebook
Al agregar esta API a las aplicaciones lógicas, debe autorizar a estas para que se conecten a su instancia de Facebook.

1. Inicie sesión en su cuenta de Facebook.
2. Seleccione **Authorize** (Autorizar) y permita que sus aplicaciones lógicas se conecten y utilicen su aplicación de Facebook. 

Después de crear la conexión, especifique las propiedades de Facebook. La **referencia de la API de REST** de este tema describe estas propiedades.

>[AZURE.TIP] Puede usar esta misma conexión de Facebook en otras aplicaciones lógicas.

## Referencia de API de REST de Swagger
Se aplica a la versión: 1.0.

### Obtener fuente de mi biografía
Obtiene las fuentes de la escala de tiempo del usuario que ha iniciado sesión. ```GET: /me/feed```

| Nombre|Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|fields|cadena|no|query|Ninguna |Especificar los campos que desea que se devuelvan. Ejemplo (id,name,picture).|
|limit|integer|no|query| Ninguna|Número máximo de mensajes que se van a recuperar|
|por|cadena|no|query| Ninguna|Restringir la lista de mensajes a solo aquellos con ubicación adjuntada.|
|filter|cadena|no|query| Ninguna|Recuperar solo los mensajes que coincidan con un filtro de transmisión en particular.|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|400|Bad Request|
|500|Internal Server Error|
|default|Error en la operación.|


### Publicar en mi biografía
Publica un mensaje de estado en la escala de tiempo del usuario que ha iniciado la sesión. ```POST: /me/feed```

| Nombre|Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|post|cadena |yes|body|Ninguna |Nuevo mensaje que se va a publicar|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|400|Bad Request|
|500|Internal Server Error|
|default|Error en la operación.|


### Cuando haya un nuevo mensaje en mi biografía
Desencadena un nuevo flujo cuando hay un nuevo mensaje en la escala de tiempo del usuario que ha iniciado la sesión. ```GET: /trigger/me/feed```

No hay ningún parámetro.

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|400|Bad Request|
|500|Internal Server Error|
|default|Error en la operación.|


### Obtener fuente de página
Obtener mensajes de la fuente de una página especificada. ```GET: /{pageId}/feed```

| Nombre|Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|pageId|cadena|yes|path| Ninguna|Identificador de la página de la que se deben recuperar los mensajes.|
|limit|integer|no|query| Ninguna|Número máximo de mensajes que se van a recuperar|
|include\_hidden|boolean|no|query|Ninguna |Si se incluirán o no los mensajes que haya ocultado la página|
|fields|cadena|no|query|Ninguna |Especificar los campos que desea que se devuelvan. Ejemplo (id,name,picture).|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|400|Bad Request|
|500|Internal Server Error|
|default|Error en la operación.|


### Obtener biografía de usuario
Obtener mensajes de la escala de tiempo de un usuario. ```GET: /{userId}/feed```

| Nombre|Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|userId|cadena|yes|path|Ninguna |Id. del usuario cuya biografía se debe recuperar.|
|limit|integer|no|query|Ninguna |Número máximo de mensajes que se van a recuperar|
|por|cadena|no|query|Ninguna |Restringir la lista de mensajes a solo aquellos con ubicación adjuntada.|
|filter|cadena|no|query| Ninguna|Recuperar solo los mensajes que coincidan con un filtro de transmisión en particular.|
|fields|cadena|no|query| Ninguna|Especificar los campos que desea que se devuelvan. Ejemplo (id,name,picture).|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|400|Bad Request|
|500|Internal Server Error|
|default|Error en la operación.|


### Publicar en la página
Publicar un mensaje en una página de Facebook con el usuario que inició sesión. ```POST: /{pageId}/feed```

| Nombre|Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|pageId|cadena|yes|path|Ninguna |Id. de la página que se va a publicar|
|post|many |yes|body|Ninguna |Nuevo mensaje para publicar.|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|400|Bad Request|
|500|Internal Server Error|
|default|Error en la operación.|


## Definiciones de objeto

#### GetFeedResponse

|Nombre de propiedad | Tipo de datos | Obligatorio|
|---|---|---|
|data|array|no|

#### TriggerFeedResponse

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|data|array|no|

#### PostItem: una sola entrada en la fuente de un perfil
El perfil puede ser un usuario, una página, una aplicación o un grupo.

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|id|cadena|no|
|admin\_creator|array|no|
|caption|cadena|no|
|created\_time|cadena|no|
|description|cadena|no|
|feed\_targeting|not defined|no|
|from|not defined|no|
|icon|cadena|no|
|is\_hidden|boolean|no|
|is\_published|boolean|no|
|link|cadena|no|
|message|cadena|no|
|name|cadena|no|
|object\_id|cadena|no|
|picture|cadena|no|
|place|not defined|no|
|privacy|not defined|no|
|propiedades|array|no|
|de origen|cadena|no|
|status\_type|cadena|no|
|story|cadena|no|
|targeting|not defined|no|
|to|array|no|
|type|cadena|no|
|updated\_time|cadena|no|
|with\_tags|not defined|no|

#### TriggerItem: una sola entrada en la fuente de un perfil
El perfil puede ser un usuario, una página, una aplicación o un grupo.

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|id|cadena|no|
|created\_time|cadena|no|
|from|not defined|no|
|message|cadena|no|
|type|cadena|no|

#### AdminItem

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|id|cadena|no|
|link|cadena|no|

#### PropertyItem

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|name|cadena|no|
|text|cadena|no|
|href|cadena|no|

#### UserPostFeedRequest

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|message|cadena|yes|
|link|cadena|no|
|picture|cadena|no|
|name|cadena|no|
|caption|cadena|no|
|description|cadena|no|
|place|cadena|no|
|etiquetas|cadena|no|
|privacy|not defined|no|
|object\_attachment|cadena|no|

#### PagePostFeedRequest

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|message|cadena|yes|
|link|cadena|no|
|picture|cadena|no|
|name|cadena|no|
|caption|cadena|no|
|description|cadena|no|
|actions|array|no|
|place|cadena|no|
|etiquetas|cadena|no|
|object\_attachment|cadena|no|
|targeting|not defined|no|
|feed\_targeting|not defined|no|
|published|boolean|no|
|scheduled\_publish\_time|cadena|no|
|backdated\_time|cadena|no|
|backdated\_time\_granularity|cadena|no|
|child\_attachments|array|no|
|multi\_share\_end\_card|boolean|no|

#### PostFeedResponse

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|id|cadena|no|

#### ProfileCollection

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|data|array|no|

#### UserItem

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|id|cadena|no|
|first\_name|cadena|no|
|last\_name|cadena|no|
|name|cadena|no|
|gender|cadena|no|
|about|cadena|no|

#### ActionItem

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|name|cadena|no|
|link|cadena|no|

#### TargetItem

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|countries|array|no|
|locales|array|no|
|regions|array|no|
|cities|array|no|

#### FeedTargetItem: objeto que controla la dirección de la fuente de noticias de este mensaje
Es más probable que cualquier miembro de estos grupos vea este mensaje y existe una menor probabilidad en el caso de otras personas. Solo se aplica a las páginas.

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|countries|array|no|
|regions|array|no|
|cities|array|no|
|age\_min|integer|no|
|age\_max|integer|no|
|genders|array|no|
|relationship\_statuses|array|no|
|interested\_in|array|no|
|college\_years|array|no|
|interests|array|no|
|relevant\_until|integer|no|
|education\_statuses|array|no|
|locales|array|no|

#### PlaceItem

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|id|cadena|no|
|name|cadena|no|
|overall\_rating|número|no|
|location|not defined|no|

#### LocationItem

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|city|cadena|no|
|country|cadena|no|
|latitude|número|no|
|located\_in|cadena|no|
|longitude|número|no|
|name|cadena|no|
|region|cadena|no|
|state|cadena|no|
|street|cadena|no|
|zip|cadena|no|

#### PrivacyItem

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|description|cadena|no|
|value|cadena|yes|
|allow|cadena|no|
|deny|cadena|no|
|friends|cadena|no|

#### ChildAttachmentsItem

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|link|cadena|no|
|picture|cadena|no|
|image\_hash|cadena|no|
|name|cadena|no|
|description|cadena|no|

#### PostPhotoRequest

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|url|cadena|yes|
|caption|cadena|no|

#### PostPhotoResponse

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|id|cadena|yes|
|post\_id|cadena|yes|

#### PostVideoRequest

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|videoData|cadena|yes|
|description|cadena|yes|
|título|cadena|yes|
|uploadedVideoName|cadena|no|

#### GetPhotoResponse

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|data|not defined|yes|

#### GetPhotoResponseItem

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|url|cadena|yes|
|is\_silhouette|boolean|yes|
|height|cadena|no|
|width|cadena|no|

#### GetEventResponse

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|data|array|yes|

#### GetEventResponseItem

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|id|cadena|yes|
|name|cadena|yes|
|start\_time|cadena|no|
|end\_time|cadena|no|
|timezone|cadena|no|
|location|cadena|no|
|description|cadena|no|
|ticket\_uri|cadena|no|
|rsvp\_status|cadena|yes|


## Pasos siguientes

[Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0302_2016-->