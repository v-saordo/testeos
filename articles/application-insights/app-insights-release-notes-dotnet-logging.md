<properties 
	pageTitle="Notas de la versión de los adaptadores de registro de Application Insights" 
	description="Las actualizaciones más recientes." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>
<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/21/2015" 
	ms.author="abaranch"/>
 
# Notas de la versión de los adaptadores de registro para .NET

Si ya utiliza un marco de registro como log4net, NLog o System.Diagnostics.Trace, puede capturar esos registros y enviarlos a [Application Insights](https://azure.microsoft.com/services/application-insights/), donde puede correlacionar los seguimientos de registros con las acciones del usuario, las excepciones y otros eventos.


#### Primeros pasos

Consulte [Registros, excepciones y diagnósticos personalizados para ASP.NET en Application Insights](app-insights-search-diagnostic-logs.md).

## Versión 1.2.6

- Corrección de errores
- log4Net: recopila propiedades log4net como propiedades personalizadas. UserName ya no será más una propiedad personalizada (se recopila como telemetry.Context.User.Id). Timestamp ya no será más una propiedad personalizada.
- NLog: recopila propiedades NLog como propiedades personalizadas. SequenceID ya no será más una propiedad personalizada (se recopila como telemetry.Sequence). Timestamp ya no será más una propiedad personalizada. 

## Versión 1.2.5
- Abrir primero la versión de origen: referencias de API de Application Insights versión 1.2.3 o superior.

<!---HONumber=AcomDC_0128_2016-->