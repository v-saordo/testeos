<properties
   pageTitle="Uso del Validador XML de BizTalk en aplicaciones lógicas en el Servicio de aplicaciones de Azure | Microsoft Azure"
   description="Valide esquemas utilizando el Validador XML de BizTalk en la aplicación lógica."
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="rajram"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="02/18/2016"
   ms.author="rajram"/>

# Validador XML de BizTalk

>[AZURE.NOTE] Esta versión del artículo se aplica a la versión de esquema 2014-12-01-preview de las aplicaciones lógicas.

Use el conector Validador XML de BizTalk de la aplicación para validar los datos XML con respecto a los esquemas XML predefinidos. Los usuarios pueden usar los esquemas existentes o generar esquemas basándose en una instancia de archivo sin formato, una instancia de JSON o conectores existentes.

## Uso del Validador XML de BizTalk
Para usar el Validador XML de BizTalk, primero cree una instancia de la aplicación de API del Validador XML de BizTalk. Esto puede hacerse ya sea en línea, mediante la creación de una aplicación lógica o mediante la selección de la aplicación de API del Validador XML de BizTalk desde Azure Marketplace.

### Configuración del Validador XML de BizTalk
El Validador XML de BizTalk adopta esquemas como parte de su configuración. Para iniciar la hoja de configuración de la aplicación de API, los usuarios pueden iniciar la aplicación de API directamente desde el Portal de Azure, o bien pueden hacer doble clic en dicha aplicación en la superficie del diseñador:

![Configuración del Validador XML de BizTalk][1]

En la hoja de la aplicación de API, los usuarios pueden configurar esquemas seleccionando *Esquemas*.

![Elemento de Esquemas del Validador XML de BizTalk][2]

Los usuarios pueden cargar esquemas desde un disco o generar uno a partir de una instancia de archivo sin formato o una instancia JSON:

![Esquemas del Validador XML de BizTalk][3]


### Uso del Codificador de archivos sin formato de BizTalk en la superficie de diseño
Una vez configurado, los usuarios pueden seleccionar *->* y elegir una acción de una lista de acciones:

![Lista de acciones del Validador XML de BizTalk][4]

#### Validar XML

La acción Validar XML valida un entrada XML dada con respecto a esquemas preconfigurados:

![Acción Validar XML del Validador XML de BizTalk][5]

Parámetro|Tipo|Descripción del parámetro
---|---|---
XML de entrada|cadena|XML de entrada para validar

La acción devuelve la salida como un objeto. El resultado contiene el modelo que representa la respuesta del Validador XML. Consta de resultado, nombre de esquema, nodo raíz y descripción del error.


<!-- References -->
[1]: ./media/app-service-logic-xml-validator/XmlValidator.ClickToConfigure.PNG
[2]: ./media/app-service-logic-xml-validator/XmlValidator.SchemasPart.PNG
[3]: ./media/app-service-logic-xml-validator/XmlValidator.SchemaUpload.PNG
[4]: ./media/app-service-logic-xml-validator/XmlValidator.ListOfActions.PNG
[5]: ./media/app-service-logic-xml-validator/XmlValidator.ValidateXml.PNG

<!---HONumber=AcomDC_0224_2016-->