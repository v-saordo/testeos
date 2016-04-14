<properties 
   pageTitle="Uso del conector de Wait en Aplicaciones lógicas | Servicio de aplicaciones de Microsoft Azure" 
   description="Creación y configuración del conector de Wait o la aplicación de API y su uso en una aplicación lógica en Servicio de aplicaciones de Azure" 
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

# Introducción al conector de Wait y su incorporación a su aplicación lógica
>[AZURE.NOTE] Esta versión del artículo se aplica a la versión de esquema 2014-12-01-preview de las aplicaciones lógicas.

El conector de espera permite que una aplicación retrase su ejecución durante un período de tiempo especificado o hasta la aparición de un tiempo especificado. Puede agregar el conector de Wait a sus datos de flujo de trabajo empresarial y datos de proceso como parte de este flujo de trabajo en una aplicación lógica. Cuando se emplea en una aplicación lógica, se puede usar para retrasar la ejecución.

## Uso del conector de espera
Para usar el conector espera, deberá crear primero una instancia de la aplicación de API del conector de espera. Esta tarea puede realizarse en línea mediante la creación de una aplicación lógica, o bien seleccionando la aplicación de API del conector de espera en Azure Marketplace.

## Uso del conector de espera en la superficie del diseñador de aplicaciones lógicas
El conector de espera se puede usar como una acción. No tiene ningún desencadenador.

### Acción
- Haga clic en el conector de espera en el panel derecho: ![Lista de acciones][1]
- El conector de espera admite dos acciones: 
	- Retraso
	- Retrasar hasta
	 
- Seleccione *Retraso*: ![Entrada de retraso][2]
- Proporcione las entradas para la acción y configúrela: ![Acción configurada][3]

Parámetro|Tipo|Descripción del parámetro
---|---|---
Duración en minutos|integer|Duración del retraso en minutos


## Aplicaciones adicionales del conector
Una vez creado el conector, puede agregarlo a un flujo empresarial mediante una aplicación lógica. Consulte [¿Qué son las aplicaciones lógicas?](app-service-logic-what-are-logic-apps.md)

>[AZURE.NOTE] Si desea empezar a trabajar con las aplicaciones lógicas de Azure antes de registrarse para obtener una cuenta de Azure, vaya a [Prueba de aplicaciones lógicas](https://tryappservice.azure.com/?appservice=logic), donde podrá crear inmediatamente una aplicación lógica de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

Consulte la referencia de API de REST de Swagger en [Referencia de conectores y aplicaciones de API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

También puede consultar las estadísticas de rendimiento y la seguridad de control para el conector. Consulte [Administración y supervisión de conectores y aplicaciones de API](../app-service-api/app-service-api-manage-in-portal.md).

<!--References -->
[1]: ./media/app-service-logic-wait/ListOfActions.PNG
[2]: ./media/app-service-logic-wait/DelayInput.PNG
[3]: ./media/app-service-logic-wait/ActionConfigured.PNG

<!---HONumber=AcomDC_0224_2016-->