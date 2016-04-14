<properties 
	pageTitle="Application Insights para ASP.NET 5" 
	description="Supervise la disponibilidad, el rendimiento y el uso de las aplicaciones web." 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/10/2016" 
	ms.author="awills"/>

# Application Insights para ASP.NET 5

Visual Studio Application Insights le permite supervisar la disponibilidad, el rendimiento y el uso de su aplicación web. Con los comentarios que obtendrá sobre el rendimiento y la eficacia de la aplicación en su entorno natural, pueda tomar decisiones meditadas sobre la dirección del diseño en cada ciclo de vida de desarrollo.

![Ejemplo](./media/app-insights-asp-net-five/sample.png)

Necesitará una suscripción a [Microsoft Azure](http://azure.com). Inicie sesión con una cuenta Microsoft, que podría tener para Windows, XBox Live u otros servicios en la nube de Microsoft. Si su equipo tiene una suscripción organizativa a Azure, el propietario puede agregarle a esta con la cuenta de Microsoft.

[Demostración del ejemplo](https://github.com/aspnet/Docs/tree/master/aspnet/fundamentals/application-insights/sample)

## Introducción

Si ha creado el proyecto en Visual Studio de 2015, ya debe disponer de Application Insights. De lo contrario, siga la [Guía de introducción](https://github.com/Microsoft/ApplicationInsights-aspnet5/wiki/Getting-Started).

## Uso de Application Insights

Inicie sesión en el [Portal de Microsoft Azure](https://portal.azure.com) y vaya al recurso que ha creado para supervisar la aplicación.

En una ventana del explorador independiente, use la aplicación durante un tiempo. Verá los datos que aparecen en los gráficos de Application Insights. (Es posible que tenga que hacer clic en Actualizar). Solo habrá una pequeña cantidad de datos mientras está desarrollando, pero estos gráficos realmente cobrarán vida cuando publique su aplicación y disponga de varios usuarios.

La página de información general muestra los gráficos de rendimiento que es probable que más le interesen: tiempo de respuesta del servidor, tiempo de carga de la página y recuentos de solicitudes con error. Haga clic en cualquier gráfico para ver más gráficos y datos.

Las vistas del portal se dividen en dos categorías principales:

* [El explorador de métricas](app-insights-metrics-explorer.md) muestra gráficos y tablas de métricas y recuentos, como tiempos de respuesta, tasas de errores o las métricas que crea el usuario con la [API](app-insights-api-custom-events-metrics.md). Filtre y segmente los datos por valores de propiedad para obtener una mejor comprensión de la aplicación y sus usuarios.
* [El explorador de búsqueda](app-insights-diagnostic-search.md) enumera los eventos individuales, como solicitudes específicas, excepciones, seguimientos de registros o eventos creados por el usuario con la [API](app-insights-api-custom-events-metrics.md). Filtre y busque en los eventos y desplácese entre los eventos relacionados para investigar los problemas.

## Alertas

* Configure [pruebas de disponibilidad](app-insights-monitor-web-app-availability.md) para probar su sitio web continuamente desde ubicaciones de todo el mundo y obtener correos electrónicos en cuanto alguna prueba falle.
* Configure [alertas de métricas](app-insights-monitor-web-app-availability.md) para saber si métricas como los tiempos de respuesta o las tasas de excepciones sobrepasan los límites aceptables.


## Obtener más telemetría

* [Supervise dependencias](app-insights-dependencies.md) para ver si REST, SQL u otros recursos externos se están ralentizando.
* [Use la API](app-insights-api-custom-events-metrics.md) para enviar sus propios eventos y métricas para obtener una vista más detallada del rendimiento y el uso de la aplicación.
* [Las pruebas de disponibilidad](app-insights-monitor-web-app-availability.md) comprueban su aplicación constantemente desde cualquier parte del mundo. 


## Código abierto

[Lectura y contribución al código](https://github.com/Microsoft/ApplicationInsights-aspnet5)


<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apikey]: app-insights-api-custom-events-metrics.md#ikey
[availability]: app-insights-monitor-web-app-availability.md
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[detect]: app-insights-detect-triage-diagnose.md
[diagnostic]: app-insights-diagnostic-search.md
[knowUsers]: app-insights-overview-usage.md
[metrics]: app-insights-metrics-explorer.md
[netlogs]: app-insights-asp-net-trace-logs.md
[perf]: app-insights-web-monitor-performance.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[roles]: app-insights-resources-roles-access-control.md
[start]: app-insights-overview.md
[usage]: app-insights-web-track-usage.md

<!---HONumber=AcomDC_0211_2016-->