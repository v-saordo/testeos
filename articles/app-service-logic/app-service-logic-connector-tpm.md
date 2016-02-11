<properties 
   pageTitle="Uso del conector de Administración de socios comerciales de BizTalk en Aplicaciones lógicas | Servicio de aplicaciones de Microsoft Azure" 
   description="Creación y configuración del conector de Administración de socios comerciales de BizTalk o la aplicación de API y su uso en una aplicación lógica en Servicio de aplicaciones de Azure" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="rajeshramabathiran" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="12/17/2015"
   ms.author="rajram"/>

# Introducción a Administración de socios comerciales de BizTalk y su incorporación a su aplicación lógica
El servicio Administración de socios comerciales (TPM) de BizTalk le permite definir y mantener relaciones de negocio a negocio, como socios y contratos, junto con artefactos asociados como esquemas y certificados. Estas relaciones se pueden aplicar mediante servicios de API relacionados como X 12, EDIFACT y AS2.

La aplicación de API TPM es el requisito básico del conector AS2, de la aplicación de API X12 o de la aplicación de API EDIFACT. Puede agregar Administración de socios comerciales de BizTalk a los datos de flujos de trabajo y de procesos empresariales como parte de un flujo de trabajo de negocio a negocio dentro de una aplicación lógica.

## Requisitos previos
- Base de datos de SQL Azure en blanco: debe crear primero una base de datos de SQL Azure en blanco antes de crear una nueva aplicación de API de TPM.

## Descripción de socios, contratos y perfiles
Obtenga más información sobre cómo [crear un contrato de socio comercial][1].

## Aplicaciones adicionales del conector
Una vez creado el conector, puede agregarlo a un flujo de trabajo empresarial mediante una aplicación lógica. Consulte [¿Qué son las aplicaciones lógicas?](app-service-logic-what-are-logic-apps.md)

>[AZURE.NOTE]Si desea empezar a trabajar con las aplicaciones lógicas de Azure antes de registrarse para obtener una cuenta de Azure, vaya a [Prueba de aplicaciones lógicas](https://tryappservice.azure.com/?appservice=logic), donde podrá crear inmediatamente una aplicación lógica de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

Consulte la referencia de API de REST de Swagger en [Referencia de conectores y aplicaciones de API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

También puede consultar las estadísticas de rendimiento y la seguridad de control para el conector. Consulte [Administración y supervisión de las aplicaciones de API y los conectores integrados](app-service-logic-monitor-your-connectors.md).

<!--References-->
[1]: app-service-logic-create-a-trading-partner-agreement.md

<!---HONumber=AcomDC_1223_2015-->