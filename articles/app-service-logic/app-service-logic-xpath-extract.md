<properties
   pageTitle="Uso del Extractor XPath de BizTalk en aplicaciones lógicas del Servicio de aplicaciones de Azure | Microsoft Azure"
   description="Extractor XPath de BizTalk"
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
   ms.date="12/07/2015"
   ms.author="rajram"/>

#Extractor XPath de BizTalk

El conector Extractor XPath de BizTalk ayuda a su aplicación a buscar y extraer datos desde contenido XML según un XPath determinado.

## Uso del Extractor XPath de BizTalk
1. Para utilizar el Extractor XPath de BizTalk, primero debe crear una instancia de la aplicación de API del Extractor XPath de BizTalk. Esto puede hacerse ya sea en línea, mediante la creación de una aplicación lógica o mediante la selección de la aplicación de API del Extractor XPath de BizTalk desde Azure Marketplace.

	>[AZURE.NOTE]No hay opciones de configuración asociadas a BizTalk Xpath Extractor.
2. [Creación de una nueva aplicación lógica] Abra "Desencadenadores y acciones" en la aplicación lógica para abrir el Diseñador de aplicaciones lógicas y configure el flujo.
3. En el diseñador, el panel derecho enumera las aplicaciones de API con las que se puede compilar el flujo. Busque el «Extractor XPath de BizTalk» Al seleccionarlo se agrega el Extractor XPath al flujo y se aprovisiona una instancia del mismo.
4. Una vez aprovisionada, el diseñador muestra la acción asociada con la aplicación de API Extractor XPath de BizTalk: ![Elegir acción de Extractor XPath de BizTalk][1]

5. Elija "Extraer mediante XPath". "Extraer mediante XPath" evalúa la expresión xpath de entrada en un XML de entrada determinado: ![Entrada de Extractor XPath de BizTalk][2]

	Parámetro|Tipo|Descripción del parámetro
---|---|---
XPath|cadena|Ruta de la consulta dentro de XML.
Input Xml|cadena|Contenido XML de entrada.

La acción devuelve la salida como una cadena - resultado. El resultado contiene el valor de la ruta de la consulta dentro de XML.

<!-- References -->
[1]: ./media/app-service-logic-xpath-extract/ChooseAction.PNG
[2]: ./media/app-service-logic-xpath-extract/ConfigureInput.PNG

<!-- Links -->
[Creación de una nueva aplicación lógica]: app-service-logic-create-a-logic-app.md

<!---HONumber=AcomDC_1210_2015-->