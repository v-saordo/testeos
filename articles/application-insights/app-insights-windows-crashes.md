<properties 
	pageTitle="La detección y el diagnóstico se bloquean en las aplicaciones de la Tienda Windows y de Windows Phone con Application Insights" 
	description="Analice los problemas de rendimiento de la aplicación de su dispositivo Windows con Application Insights." 
	services="application-insights" 
    documentationCenter="windows"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/21/2015" 
	ms.author="awills"/>

# La detección y el diagnóstico se bloquean en las aplicaciones de la Tienda Windows y de Windows Phone con Application Insights

*Application Insights se encuentra en su versión de vista previa.*

Si los usuarios experimentan bloqueos en la aplicación, le gustaría saberlo rápidamente y conocer detalles sobre qué ha ocurrido. Con Application Insights, puede supervisar con qué frecuencia se producen los bloqueos, recibir avisos cuando se produzcan e investigar los informes de incidentes individuales.

"Bloqueo" significa que la aplicación finaliza debido a una excepción no detectada. Si la aplicación detecta una excepción, puede notificarla con la [API TrackException][apiexceptions], pero continuará ejecutándose. En ese caso, no se registrará como un bloqueo.


## Supervisión de la frecuencia de bloqueo

Si aún no lo ha hecho, agregue [Application Insights al proyecto de la aplicación][windows] y vuelva a publicarlo.

Los bloqueos se muestran en la hoja de información general de la aplicación en [el portal de Application Insights][portal].

![](./media/app-insights-windows-crashes/appinsights-d018-oview.png)

Puede modificar el intervalo de tiempo que se muestra en el gráfico.


## Establecimiento de una alerta para detectar bloqueos

![En el gráfico de bloqueos, haga clic en Reglas de alerta y, a continuación, en Agregar alerta](./media/app-insights-windows-crashes/appinsights-d023-alert.png)

## Diagnóstico de bloqueos

Para averiguar si algunas versiones de la aplicación se bloquean más que otras, haga clic en el gráfico de bloqueos y, a continuación, segméntelo por la versión de la aplicación:

![](./media/app-insights-windows-crashes/appinsights-d26crashSegment.png)


Para detectar las excepciones que causan bloqueos, abra Búsqueda de diagnóstico. Es posible que desee quitar otros tipos de telemetría para centrarse en las excepciones:

![](./media/app-insights-windows-crashes/appinsights-d26crashExceptions.png)

[Más información acerca del filtrado de Búsqueda de diagnostico][diagnostic].
 

Haga clic en cualquier excepción para ver sus detalles, incluidas las propiedades asociadas y el seguimiento de la pila.

![](./media/app-insights-windows-crashes/appinsights-d26crash.png)

Vea las otras excepciones y eventos que se produjeron cerca de esa excepción:


![](./media/app-insights-windows-crashes/appinsights-d26crashRelated.png)

## Inserción de eventos y registros de seguimiento

Para ayudar a diagnosticar los problemas, puede [insertar llamadas de seguimiento y buscar los registros en Application Insights][diagnostic].

## <a name="debug"></a>Modo de depuración frente a modo de lanzamiento

#### Depurar

Si compila en modo de depuración, los eventos se envían en cuanto se generan. Si pierde la conectividad a Internet y después sale de la aplicación antes de recuperar la conectividad, se descarta la telemetría sin conexión.

#### Lanzamiento

Si compila en la configuración de lanzamiento, los eventos se almacenan en el dispositivo y se envían cuando se reanuda la aplicación. También se envían datos durante el primer uso de la aplicación. Si no hay ninguna conexión a Internet durante el inicio, la telemetría anterior y la telemetría del ciclo de vida actual se almacenan y se envían en la siguiente reanudación.

## <a name="next"></a>Pasos siguientes

[Detección, evaluación de errores y problemas de diagnóstico con Application Insights][detect]

[API de Application Insights][api]

[Captura de registros de diagnóstico][trace]

[Solución de problemas](app-insights-windows-troubleshoot.md)




<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiexceptions]: app-insights-api-custom-events-metrics.md#track-exception
[detect]: app-insights-detect-triage-diagnose.md
[diagnostic]: app-insights-diagnostic-search.md
[platforms]: app-insights-platforms.md
[portal]: http://portal.azure.com/
[trace]: app-insights-search-diagnostic-logs.md
[windows]: app-insights-windows-get-started.md

 

<!---HONumber=AcomDC_1203_2015-->