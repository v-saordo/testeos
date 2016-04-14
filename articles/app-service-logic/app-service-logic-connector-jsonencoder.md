<properties
   pageTitle="Uso del conector del codificador JSON de BizTalk en Aplicaciones lógicas | Servicio de aplicaciones de Microsoft Azure"
   description="Creación y configuración del conector del codificador JSON de BizTalk o la aplicación de API y su uso en una aplicación lógica en Servicio de aplicaciones de Azure"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="rajeshramabathiran"
   manager="erikre"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="02/10/2016"
   ms.author="rajram"/>

# Introducción al codificador JSON de BizTalk y su incorporación a su aplicación lógica 
>[AZURE.NOTE] Esta versión del artículo se aplica a la versión de esquema 2014-12-01-preview de las aplicaciones lógicas.

El conector de codificación y descodificación JSON de BizTalk ayuda a la interoperabilidad de las aplicaciones entre datos JSON y XML. Puede convertir una instancia JSON dada a XML y viceversa.

Puede agregar el conector del Codificador JSON de BizTalk a sus datos de flujo de trabajo empresarial y datos de proceso como parte de este flujo de trabajo en una aplicación lógica.

## Uso del codificador JSON de BizTalk
Para usar el codificador JSON de BizTalk, primero debe crear una instancia de la aplicación de API del codificador JSON de BizTalk. Esta operación puede realizarse en línea, mediante la creación de una aplicación lógica, o bien seleccionando la aplicación de API del codificador JSON de BizTalk en Azure Marketplace.

## Uso del codificador JSON de BizTalk en la superficie del diseñador de aplicaciones lógicas
Siga los pasos que se indican en [Creación de una aplicación lógica]. El codificador JSON de BizTalk puede usarse como una acción. No tiene ningún desencadenador.

### Acción
- Haga clic en el codificador JSON de BizTalk en el panel derecho

	![Configuración de la acción][3]
- Haga clic en ->

	![Lista de acciones][4]
- El codificador JSON de BizTalk admite dos acciones. Seleccione *Xml to JSON* (XML a JSON)

	![Entrada de XML a JSON][5]
- Proporcione las entradas para la acción y configúrela.

	![Codificar y enviar configurados][6]

Parámetro|Tipo|Descripción del parámetro
---|---|---
XML de entrada|objeto|Contenido de XML de entrada
Quitar sobre exterior|cadena|Marca establecida para quitar el nodo raíz del contenido XML

La acción devuelve una representación json del contenido de entrada.

## Aplicaciones adicionales del conector
Una vez creado el conector, puede agregarlo a un flujo empresarial mediante una aplicación lógica. Consulte [¿Qué son las aplicaciones lógicas?](app-service-logic-what-are-logic-apps.md)

>[AZURE.NOTE] Si desea empezar a trabajar con las aplicaciones lógicas de Azure antes de registrarse para obtener una cuenta de Azure, vaya a [Prueba de aplicaciones lógicas](https://tryappservice.azure.com/?appservice=logic), donde podrá crear inmediatamente una aplicación lógica de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

Consulte la referencia de API de REST de Swagger en [Referencia de conectores y aplicaciones de API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

También puede consultar las estadísticas de rendimiento y la seguridad de control para el conector. Consulte [Administración y supervisión de conectores y aplicaciones de API](../app-service-api/app-service-api-manage-in-portal.md).

<!--References -->
[1]: app-service-logic-connector-tpm
[2]: app-service-logic-create-a-trading-partner-agreement
[3]: ./media/app-service-logic-json-encoder/ActionSettings.PNG
[4]: ./media/app-service-logic-json-encoder/ListOfActions.PNG
[5]: ./media/app-service-logic-json-encoder/EncodeInput.PNG
[6]: ./media/app-service-logic-json-encoder/EncodeConfigured.PNG

<!--Links -->
[Creación de una aplicación lógica]: app-service-logic-create-a-logic-app.md

<!---HONumber=AcomDC_0224_2016-->