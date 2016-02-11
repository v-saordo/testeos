<properties 
	pageTitle="Registros, excepciones y diagnósticos personalizados para ASP.NET en Application Insights" 
	description="Diagnostique los problemas de las aplicaciones web de ASP.NET mediante la búsqueda de solicitudes, excepciones y registros generados con Trace, NLog o Log4Net." 
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
	ms.date="11/25/2015" 
	ms.author="awills"/>
 
# Registros, excepciones y diagnósticos personalizados para ASP.NET en Application Insights

[Application Insights][start] incluye la eficaz herramienta [Búsqueda de diagnóstico][diagnostic], que permite explorar y obtener detalles de los datos de telemetría enviados por el SDK de Application Insights desde su aplicación. El SDK envía numerosos eventos, como las vistas de página de usuario, de forma automática.

También puede escribir código para enviar seguimientos, informes de excepciones y eventos personalizados. Además, si ya usa un marco de registro como log4J, log4net, NLog o System.Diagnostics.Trace, puede capturar esos registros e incluirlos en la búsqueda. Esto permite poner los seguimientos del registro en correlación con las acciones del usuario, las excepciones y otros eventos de forma más fácil.

## <a name="send"></a>Antes de escribir telemetría personalizada

Si aún no ha [configurado Application Insights para su proyecto][start], hágalo ahora.

Al ejecutar la aplicación, esta enviará algunos datos de telemetría que se mostrarán en Búsqueda de diagnóstico, incluidas las solicitudes recibidas por el servidor, las vistas de página registradas en el cliente, las vistas de página y las excepciones no detectadas.

Abra Búsqueda de diagnóstico para ver los datos de telemetría que el SDK envía automáticamente.

![](./media/app-insights-search-diagnostic-logs/appinsights-45diagnostic.png)

![](./media/app-insights-search-diagnostic-logs/appinsights-31search.png)

Los detalles varían de un tipo de aplicación a otro. Puede hacer clic en cualquier parte de un evento individual para obtener más detalles.

## Muestreo 

Si la aplicación envía una gran cantidad de datos y usa el SDK de Application Insights para ASP.NET versión 2.0.0-beta3 o posterior, la característica de muestreo adaptativo puede operar y enviar solamente un porcentaje de los datos de telemetría. [Obtenga más información sobre el muestreo.](app-insights-sampling.md)


##<a name="events"></a>Eventos personalizados

Los eventos personalizados se muestran tanto en la [Búsqueda de diagnóstico][diagnostic] como en el [Explorador de métricas][metrics]. Puede enviarlos desde dispositivos, páginas web y aplicaciones de servidor. Se pueden usar con fines de diagnóstico y para [entender los patrones de uso][track].

Un evento personalizado tiene un nombre y también puede incluir propiedades por las que se puede filtrar, junto con medidas numéricas.

JavaScript en el cliente

    appInsights.trackEvent("WinGame",
         // String properties:
         {Game: currentGame.name, Difficulty: currentGame.difficulty},
         // Numeric measurements:
         {Score: currentGame.score, Opponents: currentGame.opponentCount}
         );

C# en el servidor

    // Set up some properties:
    var properties = new Dictionary <string, string> 
       {{"game", currentGame.Name}, {"difficulty", currentGame.Difficulty}};
    var measurements = new Dictionary <string, double>
       {{"Score", currentGame.Score}, {"Opponents", currentGame.OpponentCount}};

    // Send the event:
    telemetry.TrackEvent("WinGame", properties, measurements);


VB en el servidor

    ' Set up some properties:
    Dim properties = New Dictionary (Of String, String)
    properties.Add("game", currentGame.Name)
    properties.Add("difficulty", currentGame.Difficulty)

    Dim measurements = New Dictionary (Of String, Double)
    measurements.Add("Score", currentGame.Score)
    measurements.Add("Opponents", currentGame.OpponentCount)

    ' Send the event:
    telemetry.TrackEvent("WinGame", properties, measurements)

### Ejecución de la aplicación y visualización de los resultados

Abra Búsqueda de diagnóstico.

Seleccione Evento personalizado y elija un nombre de evento concreto.

![](./media/app-insights-search-diagnostic-logs/appinsights-332filterCustom.png)


Especifique un término de búsqueda en un valor de propiedad para filtrar los datos aún más.

![](./media/app-insights-search-diagnostic-logs/appinsights-23-customevents-5.png)

Profundice en un evento individual para ver sus propiedades detalladas.

![](./media/app-insights-search-diagnostic-logs/appinsights-23-customevents-4.png)

##<a name="pages"></a> Vistas de página

La telemetría de vista de página se envía mediante la llamada trackPageView() en [el fragmento de código de JavaScript que el usuario inserta en las páginas web][usage]. Su objetivo principal es contribuir a los recuentos de vistas de página que aparecen en la página de información general.

Normalmente se le llama una vez en cada página HTML, pero puede insertar más llamadas: por ejemplo, si tiene una aplicación de una sola página y desea registrar una página nueva cada vez que el usuario obtiene más datos.

    appInsights.trackPageView(pageSegmentName, "http://fabrikam.com/page.htm"); 

A veces resulta útil asociar propiedades que pueda usar como filtros en la búsqueda de diagnóstico:

    appInsights.trackPageView(pageSegmentName, "http://fabrikam.com/page.htm",
     {Game: currentGame.name, Difficulty: currentGame.difficulty});


##<a name="trace"></a> Telemetría de seguimiento

La telemetría de seguimiento es código que el usuario inserta de forma específica para crear registros de diagnóstico.

Por ejemplo, puede insertar llamadas como esta:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");


####  Instalación de un adaptador para el marco de registro

También puede buscar los registros generados con un marco de registro: log4Net, NLog o System.Diagnostics.Trace.

1. Si planea usar log4Net o NLog, instálelo en su proyecto. 
2. En el Explorador de soluciones, haga clic con el botón derecho en el proyecto y seleccione **Administrar paquetes de NuGet**.
3. Seleccione En línea > Todo, seleccione **Incluir versión preliminar** y busque "Microsoft.ApplicationInsights"

    ![Get the prerelease version of the appropriate adapter](./media/app-insights-search-diagnostic-logs/appinsights-36nuget.png)

4. Seleccione el paquete adecuado entre los siguientes:
  + Microsoft.ApplicationInsights.TraceListener (para capturar las llamadas de System.Diagnostics.Trace)
  + Microsoft.ApplicationInsights.NLogTarget
  + Microsoft.ApplicationInsights.Log4NetAppender

El paquete de NuGet instala los ensamblados necesarios y también modifica el archivo web.config o app.config.

#### <a name="pepper"></a>Inserción de llamadas de registro de diagnóstico

Si usa System.Diagnostics.Trace, una llamada típica sería:

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

Si prefiere log4net o NLog:

    logger.Warn("Slow response - database01");

Ejecute la aplicación en modo de depuración o impleméntela.

Los mensajes aparecerán en Búsqueda de diagnóstico cuando se selecciona el filtro de seguimiento.

### <a name="exceptions"></a>Excepciones

La obtención de informes de excepciones en Application Insights supone una experiencia de gran eficacia, sobre todo porque permite navegar entre las solicitudes con error y las excepciones y leer la pila de excepciones.

En algunos casos, será necesario [insertar algunas líneas de código][exceptions] para asegurarse de que las excepciones se detecten automáticamente.

También puede escribir código explícito para enviar datos de telemetría de las excepciones:

JavaScript

    try 
    { ...
    }
    catch (ex)
    {
      appInsights.TrackException(ex, "handler loc",
        {Game: currentGame.Name, 
         State: currentGame.State.ToString()});
    }

C#

    var telemetry = new TelemetryClient();
    ...
    try 
    { ...
    }
    catch (Exception ex)
    {
       // Set up some properties:
       var properties = new Dictionary <string, string> 
         {{"Game", currentGame.Name}};

       var measurements = new Dictionary <string, double>
         {{"Users", currentGame.Users.Count}};

       // Send the exception telemetry:
       telemetry.TrackException(ex, properties, measurements);
    }

VB

    Dim telemetry = New TelemetryClient
    ...
    Try
      ...
    Catch ex as Exception
      ' Set up some properties:
      Dim properties = New Dictionary (Of String, String)
      properties.Add("Game", currentGame.Name)

      Dim measurements = New Dictionary (Of String, Double)
      measurements.Add("Users", currentGame.Users.Count)
  
      ' Send the exception telemetry:
      telemetry.TrackException(ex, properties, measurements)
    End Try

Los parámetros de las propiedades y las medidas son opcionales, pero son útiles para filtrar y agregar información adicional. Por ejemplo, si tiene una aplicación que se puede ejecutar varios juegos, podría buscar todos los informes de excepción relacionados con un juego en particular. Puede agregar tantos elementos como desee para cada diccionario.

#### Visualización de excepciones

En la hoja de información general se muestra un resumen de las excepciones y puede hacer clic en cualquier parte de este para ver más detalles. Por ejemplo:


![](./media/app-insights-search-diagnostic-logs/appinsights-039-1exceptions.png)

Haga clic en cualquier tipo de excepción para ver instancias específicas:

![](./media/app-insights-search-diagnostic-logs/appinsights-333facets.png)

También puede abrir la Búsqueda de diagnóstico directamente, filtrar por las excepciones y elegir el tipo de excepción que desea ver.

### Notificación de excepciones no controladas

Application Insights notifica las excepciones no controladas siempre que sea posible, ya sea de los dispositivos, los [exploradores web][usage] o los servidores web e independientemente de que estén instrumentadas por el [Monitor de estado][redfield] o el [SDK de Application Insights][greenbrown].

Sin embargo, no siempre puede realizar esta acción, ya que .NET Framework captura las excepciones. Por lo tanto, para asegurarse de ver todas las excepciones, tendrá que escribir un pequeño controlador de excepciones. El procedimiento más adecuado en cada caso varía en función de la tecnología. Consulte [Telemetría de excepción para ASP.NET][exceptions] para obtener más información.

### Correlación con una compilación

Cuando se leen registros de diagnóstico, es probable que el código fuente haya cambiado desde que se implementó el código activo.

Por lo tanto, resulta útil incluir información de la compilación (como la dirección URL de la versión actual) en una propiedad junto con cada excepción o seguimiento.

En lugar de agregar la propiedad por separado a cada llamada de excepción, puede establecer la información en el contexto predeterminado.

    // Telemetry initializer class
    public class MyTelemetryInitializer : IContextInitializer
    {
        public void Initialize (TelemetryContext context)
        {
            context.Properties["AppVersion"] = "v2.1";
        }
    }

En el inicializador de la aplicación como Global.asax.cs:

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.ContextInitializers
        .Add(new MyTelemetryInitializer());
    }

###<a name="requests"></a> Solicitudes de servidor web

La telemetría de las solicitudes se envía automáticamente al [instalar el monitor de estado en el servidor web][redfield] o al [agregar Application Insights al proyecto web][greenbrown]. También se inserta automáticamente en los gráficos de tiempo de solicitud y respuesta del explorador de métricas y en la página de información general.

Si desea enviar eventos adicionales, puede usar la API de TrackRequest().

## <a name="questions"></a>Preguntas y respuestas

### <a name="emptykey"></a>Aparece el mensaje de error "La clave de instrumentación no puede estar vacía".

Parece que ha instalado el paquete de NuGet del adaptador de registro sin tener que instalar Application Insights.

En el Explorador de soluciones, haga clic con el botón derecho en `ApplicationInsights.config` y elija **Actualizar Application Insights**. Aparecerá un cuadro de diálogo que le invita a iniciar sesión en Azure y a crear un recurso de Application Insights, o a volver a utilizar uno existente. Esto debería solucionarlo.

### <a name="limits"></a>¿Qué cantidad de datos se conserva?

Hasta 500 eventos por segundo de cada aplicación. Los eventos se conservan durante siete días.

### No aparecen algunos de mis eventos o seguimientos

Si la aplicación envía una gran cantidad de datos y usa el SDK de Application Insights para ASP.NET versión 2.0.0-beta3 o posterior, la característica de muestreo adaptativo puede operar y enviar solamente un porcentaje de los datos de telemetría. [Obtenga más información sobre el muestreo.](app-insights-sampling.md)


## <a name="add"></a>Pasos siguientes

* [Configuración de pruebas de disponibilidad y de capacidad de respuesta][availability]
* [Solución de problemas][qna]





<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-asp-net-exceptions.md
[greenbrown]: app-insights-asp-net.md
[metrics]: app-insights-metrics-explorer.md
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md
[track]: app-insights-api-custom-events-metrics.md
[usage]: app-insights-web-track-usage.md

 

<!---HONumber=AcomDC_0128_2016-->