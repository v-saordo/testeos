<properties 
	pageTitle="Muestreo, filtrado y preprocesamiento en el SDK de Application Insights" 
	description="Escriba complementos para que el SDK filtre los datos antes de enviar la telemetría al portal de Application Insights, además de efectuar muestreos en dichos datos o agregarles propiedades." 
	services="application-insights"
    documentationCenter="" 
	authors="alancameronwills" 
	manager="douge"/>
 
<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="02/25/2016" 
	ms.author="awills"/>

# Muestreo, filtrado y preprocesamiento de la telemetría en el SDK de Application Insights

*Application Insights se encuentra en su versión de vista previa.*

Puede escribir y configurar complementos para el SDK de Application Insights con el fin de personalizar cómo se captura y se procesa la telemetría antes de enviarla al servicio de Application Insights.

Actualmente, estas características están disponibles para el SDK de ASP.NET.

* El [muestreo](#sampling) reduce el volumen de la telemetría sin que ello influya en las estadísticas. Mantiene juntos los puntos de datos relacionados para que pueda navegar entre ellos a la hora de diagnosticar un problema. En el portal, se multiplican los recuentos totales para compensar el muestreo.
* El [filtrado](#filtering) permite seleccionar o modificar la telemetría en el SDK antes de enviarla al servidor. Por ejemplo, para reducir el volumen de la telemetría puede excluir las solicitudes de robots. Se trata de un enfoque más básico que el muestreo para reducir el tráfico. Permite ejercer más control sobre lo que se transmite, pero debe tener en cuenta que se verán afectadas las estadísticas: por ejemplo, si filtra todas las solicitudes correctas.
* [Agregue propiedades](#add-properties) a cualquier telemetría enviada desde la aplicación, incluida la telemetría de los módulos estándar. Por ejemplo, puede agregar valores calculados o números de versión para filtrar los datos en el portal.
* La [API del SDK](app-insights-api-custom-events-metrics.md) se usa para enviar métricas y eventos personalizados.

Antes de comenzar:

* Instale el [SDK de Application Insights](app-insights-asp-net.md) en su aplicación. Instale manualmente los paquetes NuGet y seleccione la *versión preliminar* más reciente.
* Pruebe la [API de Application Insights](app-insights-api-custom-events-metrics.md). 


## Muestreo

*Esta característica está en versión beta.*

El [muestreo](app-insights-sampling.md) es la forma recomendada de reducir el tráfico conservando estadísticas precisas. El filtro selecciona los elementos relacionados, para que pueda desplazarse entre los elementos de diagnóstico. Los recuentos de evento se ajustan en el explorador de métrica para compensar por los elementos filtrados.

* Se recomienda el muestreo adaptable, ya que ajusta automáticamente el porcentaje de muestreo para lograr un volumen específico de solicitudes. Actualmente solo está disponible para la telemetría de servidor ASP.NET. 
* También está disponible el [muestreo de tasa fija](app-insights-sampling.md). Con él, se especifica un porcentaje de muestreo. Está disponible para el código de aplicación web ASP.NET y páginas web de JavaScript. El cliente y el servidor sincronizarán su muestreo por lo que, en Búsqueda, puede desplazarse entre las solicitudes y las vistas de página relacionadas.
* El muestreo de ingesta funciona cuando se recibe la telemetría en el portal de Application Insights, por lo que se puede usar independientemente del SDK que se use. No reduce el tráfico de telemetría en la red, pero reduce el volumen que se procesa y se almacena en Application Insights. Solo cuenta en la cuota mensual la telemetría retenida. 

### Para habilitar el muestreo de ingesta

En la barra de configuración, abra la hoja Cuotas y precios. Haga clic en Muestreo y seleccione una relación de muestreo.

### Para habilitar el muestreo adaptable

**Actualice los paquetes de NuGet del proyecto** a la última versión *preliminar* de Application Insights: haga clic con el botón derecho en el proyecto en el Explorador de soluciones, elija Administrar paquetes de NuGet, active **Incluir versión preliminar** y busque Microsoft.ApplicationInsights.Web.

En [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md), puede ajustar la velocidad máxima de telemetría que el algoritmo de adaptación tiene como objetivo:

    <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>

### Muestreo de lado cliente

Para obtener un muestreo de tipo fijo sobre los datos de páginas web, coloque una línea adicional en el [fragmento de código de Application Insights](app-insights-javascript.md) insertado (normalmente en una página maestra como \_Layout.cshtml):

*JavaScript*

```JavaScript

	}({ 

	samplingPercentage: 10.0, 

	instrumentationKey:...
	}); 
```

* Establezca un porcentaje (10 en este ejemplo) que es igual a 100/N donde N es un número entero: por ejemplo 50 (= 100/2), 33,33 (= 100/3), 25 (= 100/4) o 10 (= 100/10). 
* Si también habilita el [muestreo de tipo fijo](app-insights-sampling.md) en el servidor, el cliente y el servidor sincronizarán su muestreo por lo que, en Búsqueda, puede desplazarse entre las solicitudes y las vistas de página relacionadas.

[Obtenga más información sobre el muestreo](app-insights-sampling.md).

## Filtros

Esta técnica le ofrece un control más directo sobre lo que se incluirá en la transmisión de telemetría o lo que se excluirá de ella. Se puede utilizar junto con el muestreo o por separado.

Para filtrar la telemetría, escriba un procesador de telemetría y regístrelo con el SDK. Toda la telemetría pasa por el procesador. Puede optar por no incluirlo en la transmisión o por agregar propiedades. Esto incluye la telemetría de los módulos estándar como el recopilador de solicitudes HTTP y el recopilador de dependencia, así como la telemetría que haya escrito. Por ejemplo, puede filtrar para dejar fuera la telemetría acerca de las solicitudes de robots o las llamadas de dependencia correctas.

> [AZURE.WARNING] El filtrado de la telemetría enviada desde el SDK usando procesadores puede sesgar las estadísticas que se ven en el portal, y dificultar el seguimiento de elementos relacionados.
> 
> En su lugar, puede efectuar un [muestreo](#sampling).

### Crear un procesador de telemetría

1. Actualice el SDK de Application Insights a la versión más reciente (2.0.0-beta2 o posterior). Haga clic con el botón derecho en el proyecto en el Explorador de soluciones de Visual Studio y elija Administrar paquetes de NuGet. En el Administrador de paquetes de NuGet, seleccione **Incluir versión preliminar** y busque Microsoft.ApplicationInsights.Web.

1. Para crear un filtro, implemente ITelemetryProcessor. Se trata de otro punto de extensibilidad como el módulo de telemetría, el inicializador de telemetría y el canal de telemetría.

    Observe que los procesadores de telemetría forman una cadena de procesamiento. Al crear instancias de un procesador de telemetría, se pasa un vínculo al procesador siguiente en la cadena. Cuando un punto de datos de telemetría se pasa al método de proceso, realiza su trabajo y, a continuación, llama al procesador de telemetría siguiente de la cadena.

    ``` C#

    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.Extensibility;

    public class SuccessfulDependencyFilter : ITelemetryProcessor
      {
        private ITelemetryProcessor Next { get; set; }

        // You can pass values from .config
        public string MyParamFromConfigFile { get; set; }

        // Link processors to each other in a chain.
        public SuccessfulDependencyFilter(ITelemetryProcessor next)
        {
            this.Next = next;
        }
        public void Process(ITelemetry item)
        {
            // To filter out an item, just return 
            if (!OKtoSend(item)) { return; }
            // Modify the item if required 
            ModifyItem(item);

            this.Next.Process(item);
        }

        // Example: replace with your own criteria.
        private bool OKtoSend (ITelemetry item)
        {
            var dependency = item as DependencyTelemetry;
            if (dependency == null) return true;

            return dependency.Success != true;
        }

        // Example: replace with your own modifiers.
        private void ModifyItem (ITelemetry item)
        {
            item.Context.Properties.Add("app-version", "1." + MyParamFromConfigFile);
        }
    }
    

    ```
2. Inserte esto en ApplicationInsights.config: 

```XML

    <TelemetryProcessors>
      <Add Type="WebApplication9.SuccessfulDependencyFilter, WebApplication9">
         <!-- Set public property -->
         <MyParamFromConfigFile>2-beta</MyParamFromConfigFile>
      </Add>
    </TelemetryProcessors>

```

(Observe que se trata de la misma sección donde inicializa un filtro de muestreo).

Puede pasar valores de cadena desde el archivo .config proporcionando propiedades con nombre públicas en la clase.

> [AZURE.WARNING] Tenga cuidado para que el nombre de tipo y los nombres de propiedad del archivo .config coincidan con los nombres de clase y propiedad del código. Si el archivo .config hace referencia a un tipo o propiedad que no existe, el SDK puede producir un error de forma silenciosa al enviar cualquier telemetría.

 
**Alternativamente,** se puede inicializar el filtro en el código. En una clase de inicialización adecuada (por ejemplo AppStart de Global.asax.cs) inserte el procesador en la cadena:

```C#

    var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;
    builder.Use((next) => new SuccessfulDependencyFilter(next));

    // If you have more processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

Los TelemetryClients creados a partir de este punto usarán sus procesadores.

### Filtros de ejemplo

#### Solicitudes sintéticas

Filtrar las pruebas web y robots. Aunque el Explorador de métricas proporciona la opción para filtrar y dejar fuera los orígenes sintéticos, esta opción reduce el tráfico filtrándolos en el SDK.

``` C#

    public void Process(ITelemetry item)
    {
      if (!string.IsNullOrEmpty(item.Context.Operation.SyntheticSource)) {return;}

      // Send everything else: 
      this.Next.Process(item);
    }

```

#### Error de autenticación

Filtrar solicitudes con una respuesta "401".

```C#

public void Process(ITelemetry item)
{
    var request = item as RequestTelemetry;

    if (request != null &&
    request.ResponseCode.Equals("401", StringComparison.OrdinalIgnoreCase))
    {
        // To filter out an item, just terminate the chain: 
        return;
    }
    // Send everything else: 
    this.Next.Process(item);
}

```

#### Filtrar las llamadas de dependencia rápida y remota

Si solo desea diagnosticar las llamadas que son lentas, descarte las rápidas.

> [AZURE.NOTE] Esto sesgará las estadísticas que se ve en el portal. El gráfico de dependencias será como si las llamadas de dependencia fuesen todos errores.

``` C#

public void Process(ITelemetry item)
{
    var request = item as DependencyTelemetry;
            
    if (request != null && request.Duration.Milliseconds < 100)
    {
        return;
    }
    this.Next.Process(item);
}

```


## Agregar propiedades

Use inicializadores de telemetría para definir las propiedades globales que se envían con toda la telemetría; y para invalidar el comportamiento seleccionado de los módulos de telemetría estándar.

Por ejemplo, para el paquete Application Insights para web recopila telemetría acerca de las solicitudes HTTP. De forma predeterminada, marca como errónea cualquier solicitud con un código de respuesta >= 400. Pero si desea tratar 400 como correcto, puede proporcionar un inicializador de telemetría que establezca la propiedad Éxito.

Si se proporciona un inicializador de telemetría, se llama cada vez que se llama a cualquiera de los métodos Track*(). Esto incluye los métodos llamados por los módulos de telemetría estándar. Por convención, estos módulos no establecen ninguna propiedad que ya haya sido establecida por un inicializador.

**Defina su inicializador**

*C#*

```C#

    using System;
    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.DataContracts;
    using Microsoft.ApplicationInsights.Extensibility;

    namespace MvcWebRole.Telemetry
    {
      /*
       * Custom TelemetryInitializer that overrides the default SDK 
       * behavior of treating response codes >= 400 as failed requests
       * 
       */
      public class MyTelemetryInitializer : ITelemetryInitializer
      {
        public void Initialize(ITelemetry telemetry)
        {
            var requestTelemetry = telemetry as RequestTelemetry;
            // Is this a TrackRequest() ?
            if (requestTelemetry == null) return;
            int code;
            bool parsed = Int32.TryParse(requestTelemetry.ResponseCode, out code);
            if (!parsed) return;
            if (code >= 400 && code < 500)
            {
                // If we set the Success property, the SDK won't change it:
                requestTelemetry.Success = true;
                // Allow us to filter these requests in the portal:
                requestTelemetry.Context.Properties["Overridden400s"] = "true";
            }
            // else leave the SDK to set the Success property      
        }
      }
    }
```

**Cargue su inicializador**

En ApplicationInsights.config:

    <ApplicationInsights>
      <TelemetryInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="MvcWebRole.Telemetry.MyTelemetryInitializer, MvcWebRole"/> 
        ...
      </TelemetryInitializers>
    </ApplicationInsights>

*Alternativamente,* se pueden crear instancias del inicializador en el código, por ejemplo en Global.aspx.cs:


```C#
    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
        .Add(new MyTelemetryInitializer());
    }
```


[Obtenga más información de este ejemplo.](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole)

<a name="js-initializer"></a>
### Inicializadores de telemetría de JavaScript

*JavaScript*

Inserte un inicializador de telemetría inmediatamente después del código de inicialización que obtuvo del portal:

```JS

    <script type="text/javascript">
        // ... initialization code
        ...({
            instrumentationKey: "your instrumentation key"
        });
        window.appInsights = appInsights;


        // Adding telemetry initializer.
        // This is called whenever a new telemetry item
        // is created.

        appInsights.queue.push(function () {
            appInsights.context.addTelemetryInitializer(function (envelope) {
                var telemetryItem = envelope.data.baseData;

                // To check the telemetry item’s type - for example PageView:
                if (envelope.name == Microsoft.ApplicationInsights.Telemetry.PageView.envelopeType) {
                    // this statement removes url from all page view documents
                    telemetryItem.url = "URL CENSORED";
                }

                // To set custom properties:
                telemetryItem.properties = telemetryItem.properties || {};
                telemetryItem.properties["globalProperty"] = "boo";

                // To set custom metrics:
                telemetryItem.measurements = telemetryItem.measurements || {};
                telemetryItem.measurements["globalMetric"] = 100;
            });
        });

        // End of inserted code.

        appInsights.trackPageView();
    </script>
```

Para obtener un resumen de las propiedades no personalizadas disponibles en telemetryItem, consulte el [modelo de datos](app-insights-export-data-model.md#lttelemetrytypegt).

Puede agregar tantos inicializadores como desee.


## Documentos de referencia

* [Información general acerca de la API](app-insights-api-custom-events-metrics.md)

* [Referencia de ASP.NET](https://msdn.microsoft.com/library/dn817570.aspx)


## Código del SDK

* [SDK básico de ASP.NET](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [ASP.NET 5](https://github.com/Microsoft/ApplicationInsights-aspnet5)
* [SDK de JavaScript](https://github.com/Microsoft/ApplicationInsights-JS)


## <a name="next"></a>Pasos siguientes


[Búsqueda de eventos y registros][diagnostic]

[Ejemplos y tutoriales](app-insights-code-samples.md)

[Solución de problemas][qna]


<!--Link references-->

[client]: app-insights-javascript.md
[config]: app-insights-configuration-with-applicationinsights-config.md
[create]: app-insights-create-new-resource.md
[data]: app-insights-data-retention-privacy.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-asp-net-exceptions.md
[greenbrown]: app-insights-asp-net.md
[java]: app-insights-java-get-started.md
[metrics]: app-insights-metrics-explorer.md
[qna]: app-insights-troubleshoot-faq.md
[trace]: app-insights-search-diagnostic-logs.md
[windows]: app-insights-windows-get-started.md

 

<!---HONumber=AcomDC_0302_2016-->