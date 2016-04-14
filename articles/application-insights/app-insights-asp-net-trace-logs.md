<properties 
	pageTitle="Exploración de registros de seguimiento de .NET en Application Insights" 
	description="Busque registros generados con Seguimiento, NLog o Log4Net." 
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
	ms.date="02/22/2016" 
	ms.author="awills"/>
 
# Exploración de registros de seguimiento de .NET en Application Insights  

Si usa NLog, log4Net o System.Diagnostics.Trace para realizar el seguimiento de diagnósticos en la aplicación ASP.NET, sus registros se pueden enviar a [Visual Studio Application Insights][start], donde puede explorarlos y buscarlos. Los registros se combinarán con los otros informes de telemetría procedente de su aplicación, y así podrá identificar los seguimientos asociados con el mantenimiento de cada solicitud de usuario y correlacionarlos con otros eventos e informes de excepciones.

> [AZURE.NOTE] ¿Necesita el módulo de captura de registros? Es un adaptador útil para registradores de terceros, pero si aún no está usando NLog, log4Net o System.Diagnostics.Trace, considere la posibilidad de llamar directamente a [Application Insights TrackTrace()](app-insights-api-custom-events-metrics.md#track-trace).


## Instalación del registro en la aplicación

Instale el marco de registro elegido en su proyecto. Esto debería producir una entrada en app.config o web.config.

> Debe agregar una entrada a web.config si usa System.Diagnostics.Trace.

## Configuración de Application Insights para recopilar registros

Si todavía no lo ha hecho, **[agregue Application Insights a su proyecto](app-insights-asp-net.md)**. Verá una opción para incluir el compilador de registros.

O **configure Application Insights** haciendo clic con el botón derecho en el proyecto en el Explorador de soluciones. Seleccione las opciones para incluir el compilador de registros.

*¿No aparece ningún menú de Application Insights ni una opción de compilador de registros?* Pruebe la [solución de problemas](#troubleshooting).


## Instalación manual

Utilice este método si el tipo de proyecto no es compatible con el programa de instalación de Application Insights (por ejemplo, un proyecto de escritorio de Windows).

1. Si planea usar log4Net o NLog, instálelo en su proyecto. 
2. En el Explorador de soluciones, haga clic con el botón derecho en el proyecto y seleccione **Administrar paquetes de NuGet**.
3. Busque "Application Insights"

    ![Get the prerelease version of the appropriate adapter](./media/app-insights-asp-net-trace-logs/appinsights-36nuget.png)

4. Seleccione el paquete adecuado entre los siguientes:
  + Microsoft.ApplicationInsights.TraceListener (para capturar las llamadas de System.Diagnostics.Trace)
  + Microsoft.ApplicationInsights.NLogTarget
  + Microsoft.ApplicationInsights.Log4NetAppender

El paquete de NuGet instala los ensamblados necesarios y también modifica el archivo web.config o app.config.

## Insertar llamadas de registro de diagnóstico

Si usa System.Diagnostics.Trace, una llamada típica sería:

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

Si prefiere log4net o NLog:

    logger.Warn("Slow response - database01");


## Uso de la API de seguimiento directamente

Puede llamar directamente a la API de seguimiento de Application Insights. Los adaptadores de registro usan esta API.

Por ejemplo:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");

Una ventaja de TrackTrace es que puede colocar datos relativamente largos en el mensaje. Por ejemplo, aquí podría codificar datos POST.


## Explorar los registros

Ejecute la aplicación, ya sea en modo de depuración o mediante su implementación para que esté activa.

En la hoja de información general de su aplicación del [portal de Application Insights][portal], elija [Buscar][diagnostic].

![En Application Insights, elija Buscar.](./media/app-insights-asp-net-trace-logs/020-diagnostic-search.png)

![Búsqueda de diagnóstico](./media/app-insights-asp-net-trace-logs/10-diagnostics.png)

Por ejemplo, puede:

* Filtrar por seguimientos de registro, o por elementos con determinadas propiedades
* Inspeccionar un elemento específico en detalle
* Encontrar otros informes de telemetría relacionados con la misma solicitud de usuario (es decir, con el mismo OperationId) 
* Guardar la configuración de esta página como favorita

> [AZURE.NOTE] **Muestreo.** Si la aplicación envía una gran cantidad de datos y usa el SDK de Application Insights para ASP.NET versión 2.0.0-beta3 o posterior, la característica de muestreo adaptativo puede operar y enviar solamente un porcentaje de los datos de telemetría. [Obtenga más información sobre el muestreo.](app-insights-sampling.md)

## Pasos siguientes

[Diagnóstico de errores y excepciones en ASP.NET][exceptions]

[Más información sobre Búsqueda de diagnóstico][diagnostic].



## Solución de problemas

### ¿Cómo se puede hacer para Java?

Utilice los [adaptadores de registro de Java](app-insights-java-trace-logs.md).

### No aparece la opción de Application Insights en el menú contextual del proyecto

* Compruebe si las herramientas de Application Insights están instaladas en este equipo de desarrollo. En el menú Herramientas de Visual Studio, en Extensiones y actualizaciones, busque Herramientas de Application Insights. Si no se encuentran en la pestaña Instalado, abra la pestaña En línea e instálelas.
* Podría tratarse de un tipo de proyecto no admitido por las herramientas de Application Insights. Use la [instalación manual](#manual-installation).

### No aparece la opción de adaptador en la herramienta de configuración

* Debe instalar primero el marco de registro.
* Si usa System.Diagnostics.Trace, asegúrese [de que lo ha configurado en `web.config`](https://msdn.microsoft.com/library/system.diagnostics.eventlogtracelistener.aspx).
* ¿Tiene la versión más reciente de las herramientas de Application Insights? En el menú **Herramientas** de Visual Studio, elija **Extensiones y actualizaciones** y abra la pestaña **Actualizaciones**. Si las herramientas de Application Insights se encuentran ahí, haga clic para actualizarlas.


### <a name="emptykey"></a>Aparece el mensaje de error "La clave de instrumentación no puede estar vacía".

Parece que ha instalado el paquete de NuGet del adaptador de registro sin tener que instalar Application Insights.

En el Explorador de soluciones, haga clic con el botón derecho en `ApplicationInsights.config` y elija **Actualizar Application Insights**. Aparecerá un cuadro de diálogo que le invita a iniciar sesión en Azure y a crear un recurso de Application Insights, o a volver a utilizar uno existente. Esto debería solucionarlo.

### Puedo ver seguimientos en Búsqueda de diagnóstico, pero no los otros eventos.

A veces, el paso de todos los eventos y solicitudes por la canalización puede llevar un rato.

### <a name="limits"></a>¿Qué cantidad de datos se conserva?

Hasta 500 eventos por segundo de cada aplicación. Los eventos se conservan durante siete días.

### No veo algunas de las entradas del registro que esperaba

Si la aplicación envía una gran cantidad de datos y usa el SDK de Application Insights para ASP.NET versión 2.0.0-beta3 o posterior, la característica de muestreo adaptativo puede operar y enviar solamente un porcentaje de los datos de telemetría. [Obtenga más información sobre el muestreo.](app-insights-sampling.md)

## <a name="add"></a>Pasos siguientes

* [Configuración de pruebas de disponibilidad y de capacidad de respuesta][availability]
* [Solución de problemas][qna]





<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-web-failures-exceptions.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[start]: app-insights-overview.md

 

<!---HONumber=AcomDC_0224_2016-->