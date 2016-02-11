<properties 
	pageTitle="Diagnóstico de problemas con dependencias en Application Insights" 
	description="Búsqueda de errores y rendimientos reducidos provocados por las dependencias" 
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
	ms.date="09/17/2015" 
	ms.author="awills"/>
 
# Diagnóstico de problemas con dependencias en Application Insights


Una *dependencia* es un componente externo al que llama la aplicación. Suele ser un servicio al que se llama mediante HTTP, una base de datos o un sistema de archivos. En Application Insights para Visual Studio, puede ver fácilmente cuánto tiempo espera su aplicación a las dependencias y la frecuencia con que se produce un error en una llamada de dependencia.

## Dónde puede usarla

La supervisión de dependencia lista para su uso sin configuraciones adicionales está disponible para:

* Servicios y aplicaciones web ASP.NET que se ejecutan en un servidor IIS o en Azure.
* [Aplicaciones web de Java](app-insights-java-agent.md)

Para otros tipos, como aplicaciones de dispositivo, puede escribir su propio monitor mediante la [API de TrackDependency](app-insights-api-custom-events-metrics.md#track-dependency).

El monitor de dependencia listo para su uso sin configuraciones adicionales actualmente llama a estos tipos de dependencias:

* ASP.NET
 * Bases de datos SQL
 * Servicios WFC y web de ASP.NET que usan enlaces basados en HTTP
 * Llamadas HTTP locales o remotas
 * DocumentDb, tabla, almacenamiento de blobs y cola de Azure
* Java
 * Llamadas a una base de datos a través de un controlador [JDBC](http://docs.oracle.com/javase/7/docs/technotes/guides/jdbc/), por ejemplo, MySQL, SQL Server, PostgreSQL o SQLite.
* Páginas web
 * Llamadas AJAX

De nuevo, puede escribir sus propias llamadas de SDK para supervisar otras dependencias.

## Para configurar la supervisión de dependencia

Instale al agente adecuado para el servidor host.

Plataforma | Instalación
---|---
Servidor IIS | [Monitor de estado](app-insights-monitor-performance-live-website-now.md)
Aplicación web de Azure | [Extensión de Application Insights](../azure-portal/insights-perf-analytics.md)
Servidor web de Java | [Aplicaciones web de Java](app-insights-java-agent.md)

El Monitor de estado para los servidores de IIS no precisa que vuelva a generar el proyecto de origen con el SDK de Application Insights.

## <a name="diagnosis"></a> Diagnóstico de problemas de rendimiento de dependencia

Para evaluar el rendimiento de las solicitudes en el servidor:

![En la página de información general de la aplicación en Application Insights, haga clic en el icono de rendimiento.](./media/app-insights-dependencies/01-performance.png)

Desplácese hacia abajo para buscar en la cuadrícula de solicitudes:

![Lista de solicitudes con promedios y recuentos](./media/app-insights-dependencies/02-reqs.png)

La primera tarda mucho tiempo. Veamos si podemos averiguar dónde se está invirtiendo el tiempo.

Haga clic en esa fila para ver los eventos de solicitud individuales:


![Lista de casos de solicitudes](./media/app-insights-dependencies/03-instances.png)

Haga clic en cualquier instancia de ejecución prolongada para inspeccionarla con mayor profundidad.

> [AZURE.NOTE]Desplácese hacia abajo un poco para elegir una instancia. La latencia en la canalización puede significar que los datos de las instancias superiores están incompletos.

Desplácese hacia abajo hasta las llamadas de dependencia remotas relacionadas con esta solicitud:

![Búsqueda de llamadas a dependencias remotas, identificación de duración inusual.](./media/app-insights-dependencies/04-dependencies.png)

Parece que la mayoría del tiempo que se ha invertido en atender a esta solicitud se ha empleado en una llamada a un servicio local.

Seleccione esa fila para obtener más información:


![Haga clic en esa dependencia remota para identificar la causa.](./media/app-insights-dependencies/05-detail.png)

El detalle incluye suficiente información para diagnosticar el problema.



## Errores

Si hay solicitudes con error, haga clic en el gráfico.

![Haga clic en el gráfico de las solicitudes con error.](./media/app-insights-dependencies/06-fail.png)

Haga clic en un tipo de solicitud y una instancia de la solicitud para buscar una llamada con errores a una dependencia remota.


![Haga clic en un tipo de solicitud, haga clic en la instancia para obtener acceso a una vista diferente de la misma instancia, haga clic en ella para obtener detalles de la excepción.](./media/app-insights-dependencies/07-faildetail.png)


## Seguimiento de dependencias personalizadas

El módulo de seguimiento de dependencias estándar detecta automáticamente las dependencias externas, como bases de datos y API de REST. Pero tal vez quieras que se traten algunos componentes adicionales de la misma manera.

Puede escribir código que envíe la información de dependencia usando la misma [API de TrackDependency](app-insights-api-custom-events-metrics.md#track-dependency) que usan los módulos estándar.

Por ejemplo, si compila el código con un ensamblado que no escribió usted mismo, podría cronometrar todas las llamadas al ensamblado para averiguar cómo contribuye a los tiempos de respuesta. Para que estos datos se muestren en los gráficos de dependencia en Application Insights, envíelos mediante `TrackDependency`.

```C#

            var success = false;
            var startTime = DateTime.UtcNow;
            var timer = System.Diagnostics.Stopwatch.StartNew();
            try
            {
                success = dependency.Call();
            }
            finally
            {
                timer.Stop();
                telemetry.TrackDependency("myDependency", "myCall", startTime, timer.Elapsed, success);
            }
```

Si desea desactivar el módulo de seguimiento de dependencia estándar, quite la referencia a DependencyTrackingTelemetryModule en [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md).

<!--Link references-->

<!---HONumber=AcomDC_0121_2016-->