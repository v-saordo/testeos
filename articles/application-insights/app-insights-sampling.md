<properties 
	pageTitle="Muestreo de telemetría en Application Insights." 
	description="Cómo mantener el volumen de telemetría bajo control." 
	services="application-insights" 
    documentationCenter="windows"
	authors="vgorbenko" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/23/2015" 
	ms.author="awills"/>

#  Muestreo en Application Insights.

*Application Insights se encuentra en su versión de vista previa.*


El muestreo es una característica de Application Insights que permite recopilar y almacenar un conjunto reducido de telemetría al mismo tiempo que se mantiene un análisis estadísticamente correcto de los datos de la aplicación. Reduce el tráfico y ayuda a evitar la [limitación](app-insights-pricing.md#data-rate). Los datos se filtran de manera que los elementos relacionados se permitan, para que sea posible desplazarse entre elementos cuando se realicen investigaciones de diagnóstico. Cuando los recuentos de métrica se presentan al usuario en el portal, se renormalizan para tener en cuenta el muestreo y minimizar cualquier efecto en las estadísticas.

El muestreo adaptable está habilitado de forma predeterminada en el SDK de Application Insights para ASP.NET, versión 2.0.0-beta3 o posterior. El muestreo está actualmente en versión beta y puede cambiar en el futuro.

Hay dos módulos de muestreo alternativos:

* El muestreo adaptable ajusta automáticamente el porcentaje de muestreo para lograr un volumen específico de solicitudes. Actualmente solo está disponible para la telemetría del lado servidor ASP.NET.  
* También está disponible el muestreo de tasa fija, donde Con él, se especifica un porcentaje de muestreo. Está disponible para el código de aplicación web ASP.NET y páginas web de JavaScript. El cliente y el servidor sincronizarán su muestreo para que, en Búsqueda, pueda desplazarse entre las vistas de página y las solicitudes relacionadas.

## Habilitación del muestreo adaptable

**Actualice los paquetes de NuGet del proyecto** a la última versión *preliminar* de Application Insights: haga clic con el botón derecho en el proyecto en el Explorador de soluciones, elija Administrar paquetes de NuGet, active **Incluir versión preliminar** y busque Microsoft.ApplicationInsights.Web.

En [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md), puede ajustar diversos parámetros en el nodo `AdaptiveSamplingTelemetryProcessor`. Las cifras que se muestran son los valores predeterminados:

* `<MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>`

    Velocidad objetivo que el algoritmo de adaptación intenta lograr **en un solo host de servidor**. Si la aplicación web se ejecuta en varios hosts, se recomienda reducir este valor para mantenerse dentro de la velocidad objetivo de tráfico en el portal de Application Insights.

* `<EvaluationInterval>00:00:15</EvaluationInterval>`

    Intervalo en el que se vuelve a evaluar la velocidad actual de telemetría. La evaluación se realiza como una media móvil. Se recomienda acortar este intervalo si la telemetría experimenta ráfagas repentinas.

* `<SamplingPercentageDecreaseTimeout>00:02:00</SamplingPercentageDecreaseTimeout>`

    Cuando se cambia el valor de porcentaje de muestreo, tiempo mínimo que se tarda en permitir de nuevo que se reduzca el porcentaje de muestreo para capturar menos datos.

* `<SamplingPercentageIncreaseTimeout>00:15:00</SamplingPercentageDecreaseTimeout>`

    Cuando se cambia el valor de porcentaje de muestreo, tiempo mínimo que se tarda en permitir de nuevo que se aumente el porcentaje de muestreo para capturar más datos.

* `<MinSamplingPercentage>0.1</MinSamplingPercentage>`

    Como el porcentaje de muestreo varía, valor mínimo que se permite establecer.

* `<MaxSamplingPercentage>100.0</MaxSamplingPercentage>`

    Como el porcentaje de muestreo varía, valor máximo que se permite establecer.

* `<MovingAverageRatio>0.25</MovingAverageRatio>`

    En el cálculo de la media móvil, peso asignado al valor más reciente. Use un valor igual o menor que 1. Los valores menores hacen que el algoritmo reaccione con menor agilidad a los cambios repentinos.

* `<InitialSamplingPercentage>100</InitialSamplingPercentage>`

    Valor asignado cuando se acaba de iniciar la aplicación. No lo reduzca durante la depuración.

### Alternativa: configure el muestreo adaptivo en el código

En lugar de ajustar el muestreo en el archivo .config, puede usar código. Esto le permite especificar una función de devolución de llamada que se invoca cuando se vuelve a evaluar la frecuencia de muestreo. Puede utilizarlo, por ejemplo, para averiguar la velocidad de muestreo que se está usando.

Quite el nodo `AdaptiveSamplingTelemetryProcessor` del archivo .config.



*C#*

```C#

    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.Channel.Implementation;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    ...

    var adaptiveSamplingSettings = new SamplingPercentageEstimatorSettings();

    // Optional: here you can adjust the settings from their defaults.

    var builder = TelemetryConfiguration.Active.GetTelemetryProcessorChainBuilder();
    
    builder.UseAdaptiveSampling(
         adaptiveSamplingSettings,

        // Callback on rate re-evaluation:
        (double afterSamplingTelemetryItemRatePerSecond,
         double currentSamplingPercentage,
         double newSamplingPercentage,
         bool isSamplingPercentageChanged,
         SamplingPercentageEstimatorSettings s
        ) =>
        {
          if (isSamplingPercentageChanged)
          {
             // Report the sampling rate.
             telemetryClient.TrackMetric("samplingPercentage", newSamplingPercentage);
          }
      });

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

([Más información sobre los procesadores de telemetría](app-insights-api-filtering-sampling.md#filtering)).


<a name="other-web-pages"></a>
## Muestreo para páginas web con JavaScript

Puede configurar páginas web para el muestreo de frecuencia fija desde cualquier servidor.

Cuando [configure las páginas web para Application Insights](app-insights-javascript.md), modifique el fragmento que obtiene del portal de Application Insights. (En las aplicaciones ASP.NET, el fragmento normalmente está en \_Layout.cshtml). Inserte una línea como `samplingPercentage: 10,` antes de la clave de instrumentación:

    <script>
	var appInsights= ... 
	}({ 


    // Value must be 100/N where N is an integer.
    // Valid examples: 50, 25, 20, 10, 5, 1, 0.1, ...
	samplingPercentage: 10, 

	instrumentationKey:...
	}); 
	
	window.appInsights=appInsights; 
	appInsights.trackPageView(); 
	</script> 

Para el porcentaje de muestreo, elija un porcentaje que esté cerca de 100/N, donde N es un número entero. Actualmente el muestreo no es compatible con otros valores.

Si también habilita el muestreo de frecuencia fija en el servidor, los clientes y el servidor se sincronizarán para que, en Búsqueda, pueda desplazarse entre las solicitudes y las vistas de página relacionadas.


## Habilitación del muestreo de frecuencia fija en el servidor

1. **Actualice los paquetes NuGet del proyecto** a la *versión preliminar* más reciente de Application Insights. Haga clic con el botón derecho en el Explorador de soluciones, elija Administrar paquetes NuGet, active la opción **Incluir versión preliminar** y busque Microsoft.ApplicationInsights.Web. 

2. **Deshabilite el muestreo adaptable**: en [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md), quite o convierta en comentario el nodo `AdaptiveSamplingTelemetryProcessor`.

    ```xml

    <TelemetryProcessors>
    <!-- Disabled adaptive sampling:
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    -->
    

    ```

2. **Habilite el módulo de muestreo de frecuencia fija.** Agregue este fragmento a [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md):

    ```XML

    <TelemetryProcessors>
     <Add  Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">

      <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
      <SamplingPercentage>10</SamplingPercentage>
      </Add>
    </TelemetryProcessors>

    ```

> [AZURE.NOTE]Para el porcentaje de muestreo, elija un porcentaje que esté cerca de 100/N, donde N es un número entero. Actualmente el muestreo no es compatible con otros valores.



### Alternativa: habilite el muestreo de tasa fija en el código de servidor


En lugar de establecer el parámetro de muestreo en el archivo .config, puede usar código.

*C#*

```C#

    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    ...

    var builder = TelemetryConfiguration.Active.GetTelemetryProcessorChainBuilder();
    builder.UseSampling(10.0); // percentage

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

([Más información sobre los procesadores de telemetría](app-insights-api-filtering-sampling.md#filtering)).

## ¿Cuándo usar un muestreo?

El muestreo adaptable está habilitado automáticamente si usa el SDK de ASP.NET versión 2.0.0-beta3 o posterior.

Para la mayoría de las aplicaciones pequeñas y medianas no es necesario realizar muestreos. La información de diagnóstico más útil y las estadísticas más precisas se obtienen mediante la recopilación de datos en todas las actividades del usuario.

 
Las principales ventajas del muestreo son:

* El servicio de Application Insights descarta ("limita") puntos de datos cuando la aplicación envía un número muy elevado de telemetría en un intervalo de tiempo corto. 
* Para mantenerse dentro de la [cuota](app-insights-pricing.md) de puntos de datos de su plan de tarifa. 
* Para reducir el tráfico de red de la recopilación de telemetría. 

### ¿Muestreo adaptable o fijo?

Use el muestreo de frecuencia fija si:

* Quiere un muestreo sincronizado entre cliente y servidor para que, cuando investigue eventos en [Búsqueda](app-insights-diagnostic-search.md), pueda desplazarse entre los eventos relacionados en el cliente y el servidor, como vistas de página y solicitudes HTTP.
* Está seguro del porcentaje de muestreo adecuado para la aplicación. Debe ser lo bastante alto como para obtener métricas precisas, pero inferior a la frecuencia que supera la cuota de precios y los valores de limitación. 
* No está depurando la aplicación. Cuando presione F5 e intente ver algunas páginas de la aplicación, probablemente desee ver toda la telemetría.

De lo contrario, se recomienda el muestreo adaptable.

## ¿Cómo funciona el muestreo?

Desde el punto de vista de aplicación, el muestreo es una característica de SDK de Application Insights. Se especifica qué porcentaje de todos los puntos de datos debe enviarse al servicio Application Insights. Desde la versión 2.0.0 de SDK de Application Insights puede controlar el porcentaje de muestreo desde el código. (Las versiones futuras de SDK además permitirán configurar el porcentaje de muestreo desde el archivo ApplicationInsights.config.)

El SDK decide qué elementos de telemetría se descartarán y cuáles se van conservar. La decisión del muestreo se basa en varias reglas que tienen como objetivo conservar intactos todos los puntos de datos interrelacionados, manteniendo una experiencia de diagnóstico en Application Insights que sea práctica y confiable, incluso con un conjunto reducido de datos. Por ejemplo, si para una solicitud errónea su aplicación envía elementos de telemetría adicionales (como la excepción y los seguimientos registrados para dicha solicitud), el muestreo no dividirá esta solicitud y otra telemetría. Escogerá mantenerlos o descartarlos todos juntos Como resultado, al examinar los detalles de solicitud en Application Insights, siempre puede ver la solicitud junto con sus elementos de telemetría asociados.

Para las aplicaciones que definen "usuario" (es decir, las aplicaciones web más típicas), la decisión de muestreo se basa en el hash del identificador de usuario, lo que significa que toda la telemetría para cualquier usuario específico o se conservan o se descarta. Para los tipos de aplicaciones que no definen a los usuarios (como los servicios web) la decisión de muestreo se basa en el identificador de operación de la solicitud. Por último, para los elementos de telemetría que no tienen establecido ni identificador de usuario ni identificador de operación (por ejemplo elementos de telemetría notificados desde subprocesos asincrónicos sin contexto de http), el muestreo simplemente captura un porcentaje de elementos de telemetría de cada tipo.

Al presentarle la telemetría, el servicio de Application Insights ajusta las métricas con el mismo porcentaje de muestreo que se usó en el momento de la recopilación, para compensar por los puntos de datos que faltan. Por lo tanto, al examinar la telemetría en Application Insights, los usuarios ven aproximaciones estadísticamente correctas que están muy próximas a los números reales.

La precisión de la aproximación depende en gran medida del porcentaje de muestreo configurado. También, la precisión aumenta en las aplicaciones que administran un gran volumen de solicitudes básicamente similares de muchos usuarios. Por otro lado, para las aplicaciones que no funcionan con una carga significativa, el muestreo no es necesario ya que estas aplicaciones normalmente pueden enviar toda su telemetría normalmente manteniéndose dentro de la cuota, sin causar pérdida de datos por causa de la limitación.

Tenga en cuenta que Application Insights no muestrea tipos de telemetría de Métricas y Sesiones, ya que para estos tipos la reducción en la precisión puede ser no deseable en absoluto.

### Muestreo adaptable

El muestreo adaptable agrega un componente que supervisa la velocidad actual de transmisión desde el SDK y ajusta el porcentaje de muestreo para mantenerse dentro de la velocidad objetivo máxima. El ajuste se actualiza a intervalos regulares y se basa en una media móvil de la velocidad de transmisión de salida.

## Muestreo y el SDK de JavaScript

El SDK del lado del cliente (JavaScript) participa en el muestreo junto con el SDK del lado del servidor. Las páginas instrumentadas solo enviarán telemetría de cliente desde los mismos usuarios para los que el lado del servidor decidió tomar el muestreo. Esta lógica está diseñada para mantener la integridad de la sesión de usuario a través de los lados cliente y servidor. Como resultado, a partir de cualquier elemento específico de telemetría en Application Insights, puede encontrar todos los demás elementos de telemetría para ese usuario o esa sesión.

*Las telemetrías del lado de cliente y de servidor no muestran ejemplos coordinados tal y como se describe anteriormente.*

* Compruebe que habilitó el muestreo tanto en el cliente como en el servidor.
* Asegúrese de que la versión del SDK es 2.0 o superior.
* Compruebe que establece el mismo porcentaje de muestreo en el cliente y el servidor.


## Preguntas más frecuentes 

*¿Por qué realizar un muestreo no consiste simplemente en "recopilar X por ciento de cada tipo de telemetría"?*

 *  Aunque este método de muestreo proporcionaría un nivel de precisión muy alto en las aproximaciones métricas, destruiría la capacidad para poner en correlación datos de diagnóstico por usuario, sesión y solicitud, lo cual es crítico para el diagnóstico. Por lo tanto, el muestreo funciona mejor con una lógica "recopilar todos los elementos de telemetría para X por ciento de los usuarios de la aplicación", o "recopilar toda la telemetría para X por ciento de las solicitudes de aplicación". Para los elementos de telemetría no asociados con las solicitudes (por ejemplo, el procesamiento asincrónico en segundo plano), el recurso es "recopilar X por ciento de todos los elementos para cada tipo de telemetría". 

*¿Se puede cambiar el porcentaje de muestreo con el tiempo?*

 * Sí, el muestreo adaptable cambia gradualmente el porcentaje de muestreo, en función del volumen de datos de telemetría observado en ese momento.

*¿Se puede averiguar la frecuencia de muestreo que usa el muestreo adaptable?*

 * Sí, utilice el método de código de la configuración de muestreo adaptativo y podrá proporcionar una devolución de llamada que obtenga la velocidad de muestreo.

*Si uso el muestreo de frecuencia fija, ¿cómo sé cuál es el porcentaje de muestreo que funcionará mejor para mi aplicación?*

* Una manera de comenzar es con muestreo adaptable, averigüe qué velocidad elige (consulte la pregunta anterior) y luego cambie a muestreo de tasa fija con esa velocidad. 

    Si no, lo tendrá que adivinar. Analice su uso actual de telemetría en AI, observe las limitaciones que se produzcan y estime el volumen de telemetría recopilado. Estas tres entradas, junto con el plan de tarifa seleccionado, le sugerirán cuánto debería reducir el volumen de telemetría recopilado. Sin embargo, un aumento del número de usuarios o algún otro incremento en el volumen de datos de telemetría podrían provocar que la estimación no fuese válida.

*¿Qué sucede si configuro un porcentaje de muestreo demasiado bajo?*

* Un porcentaje de muestreo excesivamente bajo (muestreo demasiado drástico) reducirá la precisión de las aproximaciones cuando Application Insights intente compensar la visualización de los datos por la reducción del volumen de datos. Además, la experiencia de diagnóstico puede verse afectada negativamente, ya que algunas de las solicitudes con poca frecuencia errores o lentas pueden quedar fuera del muestreo.

*¿Qué sucede si configuro un porcentaje de muestreo demasiado alto?*

* Configurar un porcentaje de muestreo demasiado alto (no suficientemente drástico) dará como resultado una reducción insuficiente del volumen de telemetría recopilada. Se pueden seguir produciendo pérdidas de datos de telemetría relacionadas con la limitación, y el costo de usar Application Insights puede ser mayor de lo planeado, debido a los cargos debidos al uso por encima del límite.

*¿En qué plataformas puedo usar muestreo?*

* Actualmente, el muestreo adaptable está disponible para los lados del servidor de aplicaciones web ASP.NET (hospedadas en Azure o en su servidor propio). El muestreo de frecuencia fija está disponible para cualquier página web y para las aplicaciones web .NET, tanto del lado del cliente como del lado del servidor.

*Hay ciertos eventos excepcionales que siempre quiero ver. ¿Cómo se consigue que el módulo de muestreo los reconozca?*

 * Inicialice una instancia independiente de TelemetryClient con un nuevo valor de TelemetryConfiguration (no el activo de forma predeterminada). Úsela para enviar sus eventos excepcionales.

<!---HONumber=AcomDC_1217_2015-->