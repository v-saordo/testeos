<properties
	pageTitle="Agregar la API de SMTP en las aplicaciones lógicas | Microsoft Azure"
	description="Información general de la API de SMTP con parámetros de la API de REST"
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

# Introducción a la API de SMTP
Conéctese a un servidor SMTP para enviar correo electrónico. La API de SMTP se puede usar desde:

- Aplicaciones lógicas

>[AZURE.NOTE] Esta versión del artículo se aplica a la versión de esquema 2015-08-01-preview de las aplicaciones lógicas. Para la versión de esquema 2014-12-01-Versión preliminar, haga clic en [Conector de SMTP](../app-service-logic/app-service-logic-connector-smtp.md).

Con SMTP, puede:

- Compilar el flujo de negocio que incluye el envío de correo electrónico mediante SMTP. 
- Usar una acción para enviar correo electrónico. Esta acción obtiene una respuesta y luego deja el resultado a disposición de otras acciones. Por ejemplo, cuando hay un nuevo archivo en el servidor FTP, puede seleccionar ese archivo y enviarlo por correo electrónico como datos adjuntos mediante SMTP. 

Para agregar una operación en aplicaciones lógicas, consulte [Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Desencadenadores y acciones
La API de SMTP tiene la siguiente acción disponible. No hay ningún desencadenador.

|Desencadenadores | Acciones|
|--- | ---|
|None | Enviar correo electrónico|

Todas las API admiten datos en formato JSON y XML.

## Creación de una conexión a SMTP
Cuando agregue esta API a las aplicaciones lógicas, escriba los valores siguientes:

|Propiedad| Obligatorio|Descripción|
| ---|---|---|
| Nombre del servidor SMTP | Sí | Escriba el dominio completo (FQDN) o la dirección IP del servidor SMTP.|
| Nombre de usuario |Sí |Escriba el nombre de usuario para conectarse al servidor SMTP. |
| Password | Sí|Escriba la contraseña del nombre de usuario. |

Después de crear la conexión, especifique las propiedades de SMTP, como los valores Para o CC. En la **referencia de la API de REST** de este tema se describen estas propiedades.

>[AZURE.TIP] Puede usar esta misma conexión de SMTP en otras aplicaciones lógicas.

## Referencia de la API de REST de Swagger
Se aplica a la versión: 1.0.

### Enviar correo electrónico
Envía un correo electrónico a uno o más destinatarios. ```POST: /SendEmail```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|emailMessage| many|yes|body|Ninguna |Mensaje de correo electrónico|

## Definiciones de objeto

#### Correo electrónico: Correo electrónico de SMTP

| Nombre | Tipo de datos | Obligatorio|
|---|---|---|
|Para|cadena|no|
|CC|cadena|no|
|Asunto|cadena|no|
|Cuerpo|cadena|no|
|De|cadena|no|
|IsHtml|boolean|no|
|CCO|cadena|no|
|Importancia|cadena|no|
|Datos adjuntos|array|no|


#### Datos adjuntos: Datos adjuntos de correo electrónico

| Nombre | Tipo de datos |Obligatorio|
|---|---|---|
|FileName|cadena|no|
|ContentId|cadena|no|
|ContentData|cadena|yes|
|ContentType|cadena|yes|
|ContentTransferEncoding|cadena|yes|


## Pasos siguientes
[Crear una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0302_2016-->