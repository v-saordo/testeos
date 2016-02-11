<properties 
	pageTitle="Configuración del SDK de Application Insights con ApplicationInsights.config o .xml" 
	description="Habilitación o deshabilitación de los módulos de recopilación de datos e incorporación de contadores de rendimiento y otros parámetros" 
	services="application-insights"
    documentationCenter="" 
	authors="OlegAnaniev-MSFT"
    editor="alancameronwills" 
	manager="douge"/>
 
<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/05/2015" 
	ms.author="awills"/>

# Configuración del SDK de Application Insights con ApplicationInsights.config o .xml

El SDK de Application Insights para .NET consta de varios paquetes de NuGet. El [paquete principal](http://www.nuget.org/packages/Microsoft.ApplicationInsights)proporciona la API para enviar telemetría a Application Insights. Los [paquetes adicionales](http://www.nuget.org/packages?q=Microsoft.ApplicationInsights) proporcionan _módulos_ e _inicializadores_ de telemetría para hacer un seguimiento automático de la aplicación y su contexto. Si ajusta el archivo de configuración, puede habilitar o deshabilitar los módulos e inicializadores de telemetría, y establecer los parámetros para algunos de ellos.

El archivo de configuración se denomina `ApplicationInsights.config` o `ApplicationInsights.xml`, dependiendo del tipo de la aplicación. Se agrega automáticamente al proyecto cuando se [instalan la mayoría de las versiones del SDK][start]. También se agrega a una aplicación web con el [Monitor de estado en un servidor IIS][redfield] o al seleccionar la [extensión de Application Insights para un sitio web o una máquina virtual de Azure][azure].

No hay un archivo equivalente para controlar el [SDK en una página web][client].

En este documento se describen las secciones que verá en el archivo de configuración, cómo controlan los componentes del SDK y qué paquetes NuGet cargan esos componentes.

## Módulos de telemetría (ASP.NET)

Cada módulo de telemetría recopila un tipo específico de datos y usa la API principal para enviar dichos datos. Los módulos los instalan diferentes paquetes NuGet, que también agregan las líneas necesarias al archivo .config.

Hay un nodo en el archivo de configuración para cada módulo. Para deshabilitar un módulo, elimine el nodo o conviértalo en comentario.



### Seguimiento de dependencia

El [seguimiento de dependencia](app-insights-dependencies.md) recopila la telemetría sobre las llamadas que realiza la aplicación a bases de datos y a servicios y bases de datos externos. Para permitir que este módulo funcione en un servidor IIS, deberá [instalar el Monitor de estado][redfield]. Para utilizarlo en aplicaciones web o máquinas virtuales de Azure, [seleccione la extensión Application Insights][azure].

También puede escribir su propio código de seguimiento de dependencias con la [API de TrackDependency](app-insights-api-custom-events-metrics.md#track-dependency).


* `Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule`
* Paquete NuGet [Microsoft.ApplicationInsights.DependencyCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector).

### Recopilador de rendimiento

[Recopila contadores de rendimiento del sistema](app-insights-web-monitor-performance.md#system-performance-counters) como CPU, memoria y carga de la red desde instalaciones de IIS. Puede especificar qué contadores recopilar, incluidos los contadores de rendimiento que ha configurado usted mismo.

* `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule`
* Paquete NuGet [Microsoft.ApplicationInsights.PerfCounterCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector).


### Telemetría de diagnósticos de Application Insights

`DiagnosticsTelemetryModule` informa de errores en el propio código de instrumentación de Application Insights. Por ejemplo, si el código no puede tener acceso a los contadores de rendimiento o si un `ITelemetryInitializer` inicia una excepción. La telemetría de seguimiento que sigue este módulo aparece en la [Búsqueda de diagnóstico][diagnostic].
 
* `Microsoft.ApplicationInsights.Extensibility.Implementation.Tracing.DiagnosticsTelemetryModule`
* Paquete NuGet [Microsoft.ApplicationInsights](http://www.nuget.org/packages/Microsoft.ApplicationInsights). Si solamente se instala este paquete, el archivo ApplicationInsights.config no se crea automáticamente. 

### Modo de programador

`DeveloperModeWithDebuggerAttachedTelemetryModule` fuerza al `TelemetryChannel` de Application Insights a enviar los datos inmediatamente, elemento de telemetría a elemento, cuando se adjunta un depurador al proceso de aplicación. Esto reduce la cantidad de tiempo entre el momento en que la aplicación realiza un seguimiento de telemetría y el momento en que esta aparece en el portal de Application Insights. Esto provoca una sobrecarga considerable en la CPU y el ancho de banda.

* `Microsoft.ApplicationInsights.WindowsServer.DeveloperModeWithDebuggerAttachedTelemetryModule`
* Paquete NuGet [Application Insights Windows Server](http://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/)

### Seguimiento de solicitud web

Informa del [tiempo de respuesta y del código del resultado](app-insights-asp-net.md) de solicitudes HTTP.

* `Microsoft.ApplicationInsights.Web.RequestTrackingTelemetryModule`
* Paquete de NuGet [Microsoft.ApplicationInsights.Web](http://www.nuget.org/packages/Microsoft.ApplicationInsights.Web)

### Seguimiento de excepciones

`ExceptionTrackingTelemetryModule` realiza excepciones no controladas en la aplicación web. Consulte [Errores y excepciones][exceptions].

* `Microsoft.ApplicationInsights.Web.ExceptionTrackingTelemetryModule`
* Paquete de NuGet [Microsoft.ApplicationInsights.Web](http://www.nuget.org/packages/Microsoft.ApplicationInsights.Web)


* `Microsoft.ApplicationInsights.WindowsServer.UnobservedExceptionTelemetryModule` - Realiza un seguimiento de [excepciones de tareas inadvertidas](http://blogs.msdn.com/b/pfxteam/archive/2011/09/28/task-exception-handling-in-net-4-5.aspx).
* `Microsoft.ApplicationInsights.WindowsServer.UnhandledExceptionTelemetryModule` - Realiza un seguimiento de excepciones no controladas para roles de trabajo, servicios de Windows y aplicaciones de consola.
* Paquete NuGet [Application Insights Windows Server](http://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/).

### API principal

El paquete principal proporciona la [API principal](https://msdn.microsoft.com/library/mt420197.aspx) del SDK. Los otros módulos de telemetría usan esto y también puede [usarlo usted mismo para definir su propia telemetría](app-insights-api-custom-events-metrics.md).

* No hay entrada en ApplicationInsights.config.
* Paquete NuGet [Microsoft.ApplicationInsights](http://www.nuget.org/packages/Microsoft.ApplicationInsights). Si solamente instala este NuGet, no se genera ningún archivo .config.

## Canal de telemetría

El canal de telemetría administra el almacenamiento en búfer y la transmisión de telemetría al servicio Application Insights.

* `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel` es el canal predeterminado para servicios. Almacena en búfer los datos de la memoria.
* `Microsoft.ApplicationInsights.PersistenceChannel` es una alternativa para aplicaciones de consola. Pueden guardar los datos no vaciados en almacenamiento persistente cuando la aplicación se cierre y se enviarán cuando la aplicación se inicie de nuevo.


## Inicializadores de telemetría (ASP.NET)

Los inicializadores de telemetría establecen propiedades de contexto que se envían junto con todos los elementos de telemetría.

También puede [escribir sus propios inicializadores](app-insights-api-filtering-sampling.md#add-properties) para establecer propiedades de contexto.

Los inicializadores estándar están todos establecidos por los paquetes NuGet web o WindowsServer:


* `AccountIdTelemetryInitializer` establece la propiedad AccountId.
* `AuthenticatedUserIdTelemetryInitializer` establece la propiedad AuthenticatedUserId establecida por el SDK de JavaScript.
* `AzureRoleEnvironmentTelemetryInitializer` actualiza las propiedades `RoleName` y `RoleInstance` del contexto `Device` para todos los elementos de telemetría con información extraída del entorno de tiempo de ejecución de Azure.
* `BuildInfoConfigComponentVersionTelemetryInitializer` actualiza la propiedad `Version` del contexto `Component` para todos los elementos de telemetría con el valor extraído del archivo `BuildInfo.config` que produce MS Build.
* `ClientIpHeaderTelemetryInitializer` actualiza la propiedad `Ip` del contexto `Location` de todos los elementos de telemetría según el encabezado HTTP `X-Forwarded-For` de la solicitud.
* `DeviceTelemetryInitializer` actualiza las propiedades siguientes del contexto `Device` para todos los elementos de telemetría.
 - `Type` se establece en "PC".
 - `Id` se establece en el nombre de dominio del equipo donde se ejecuta la aplicación web.
 - `OemName` se establece en el valor extraído del campo `Win32_ComputerSystem.Manufacturer` con WMI.
 - `Model` se establece en el valor extraído del campo `Win32_ComputerSystem.Model` con WMI.
 - `NetworkType` se establece en el valor extraído de `NetworkInterface`.
 - `Language` se establece en el nombre de `CurrentCulture`.
* `DomainNameRoleInstanceTelemetryInitializer` actualiza la propiedad `RoleInstance` del contexto `Device` para todos los elementos de telemetría con el nombre de dominio del equipo donde se ejecuta la aplicación web.
* `OperationNameTelemetryInitializer` actualiza la propiedad `Name` de la propiedad `RequestTelemetry` y `Name` propiedad del contexto `Operation` de todos los elementos de telemetría según el método HTTP, así como los nombres del controlador MVC de ASP.NET y la acción que se invoca para procesar la solicitud.
* `OperationIdTelemetryInitializer` actualiza la propiedad de contexto `Operation.Id` de todos los elementos de telemetría de los que se realiza un seguimiento mientras se controla una solicitud con el `RequestTelemetry.Id` que se genera.
* `SessionTelemetryInitializer` actualiza la propiedad `Id` del contexto `Session` para todos los elementos de telemetría con valor extraído de la cookie `ai_session` que genera el código de instrumentación JavaScript de Application Insights que se ejecuta en el explorador del usuario. 
* `SyntheticTelemetryInitializer` actualiza las propiedades de los contextos `User`, `Session` y `Operation` de todos los elementos de telemetría de los que se realiza un seguimiento al tratar una solicitud de un origen sintético, como una prueba de disponibilidad o un bot de motor de búsqueda. De forma predeterminada, [Explorador de métricas](app-insights-metrics-explorer.md) no muestra telemetría sintética.
* `UserAgentTelemetryInitializer` actualiza la propiedad `UserAgent` del contexto `User` de todos los elementos de telemetría según el encabezado HTTP `User-Agent` de la solicitud.
* `UserTelemetryInitializer` actualiza las propiedades `Id` y `AcquisitionDate` del contexto `User` para todos los elementos de telemetría con valores extraídos de la cookie `ai_user` que genera el código de instrumentación JavaScript de Application Insights que se ejecuta en el explorador del usuario.


## Procesadores de telemetría (ASP.NET)

Los procesadores de telemetría pueden filtrar y modificar cada elemento de telemetría justo antes de que se envíe desde el SDK al portal.

También puede [escribir sus propios procesadores de telemetría](app-insights-api-filtering-sampling.md#filtering).


#### Procesador de telemetría de muestreo adaptivo (desde 2.0.0-beta3)

Esta opción está habilitada de manera predeterminada. Si la aplicación envía una gran cantidad de datos de telemetría, este procesador quita algunos de ellos.

```xml

    <TelemetryProcessors>
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    </TelemetryProcessors>

```

El parámetro proporciona el destino que el algoritmo intenta alcanzar. Cada instancia del SDK funciona de forma independiente, por lo que si el servidor es un clúster de varios equipos, se multiplicará el volumen real de telemetría en consonancia.

[Obtenga más información sobre el muestreo](app-insights-sampling.md).



#### Procesador de telemetría de muestreo de tasa fija (desde 2.0.0-beta1)

También hay un [procesador de telemetría de muestreo](app-insights-api-filtering-sampling.md#sampling) estándar (desde 2.0.1):

```XML

    <TelemetryProcessors>
     <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">

     <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
     <SamplingPercentage>10</SamplingPercentage>
     </Add>
   </TelemetryProcessors>

```



## Parámetros de canal (Java)

Estos parámetros afectan al modo en que el SDK de Java debe almacenar y vaciar los datos de telemetría que recopila.

#### MaxTelemetryBufferCapacity

El número de elementos de telemetría que pueden almacenarse en el almacenamiento del SDK en memoria. Cuando se alcanza este número, se vacía el búfer de telemetría; es decir, los elementos de telemetría se envían al servidor de Application Insights.

-	Mín: 1
-	Máx: 1000
-	Valor predeterminado: 500

```

  <ApplicationInsights>
      ...
      <Channel>
       <MaxTelemetryBufferCapacity>100</MaxTelemetryBufferCapacity>
      </Channel>
      ...
  </ApplicationInsights>
```

#### FlushIntervalInSeconds 

Determina la frecuencia con que los datos almacenados en el almacenamiento en memoria deben vaciarse (enviarse a Application Insights).

-	Mín: 1
-	Máx: 300
-	Valor predeterminado: 5

```

    <ApplicationInsights>
      ...
      <Channel>
        <FlushIntervalInSeconds>100</FlushIntervalInSeconds>
      </Channel>
      ...
    </ApplicationInsights>
```

#### MaxTransmissionStorageCapacityInMB

Determina el tamaño máximo en MB que se asigna al almacenamiento persistente en el disco local. Este almacenamiento se utiliza para conservar los elementos de telemetría que no pudieron transmitirse al extremo de Application Insights. Cuando se ha alcanzado el tamaño de almacenamiento, se descartarán los nuevos elementos de telemetría.

-	Mín: 1
-	Máx: 100
-	Valor predeterminado: 10

```

   <ApplicationInsights>
      ...
      <Channel>
        <MaxTransmissionStorageCapacityInMB>50</MaxTransmissionStorageCapacityInMB>
      </Channel>
      ...
   </ApplicationInsights>
```



## InstrumentationKey

Esto determina el recurso de Application Insights en el que aparecen los datos. Normalmente se crea un recurso independiente, con una clave independiente, para cada una de las aplicaciones.

Si desea establecer la clave de forma dinámica (por ejemplo, si desea enviar los resultados de su aplicación a distintos recursos) puede omitir la clave del archivo de configuración y establecerla en el código.

Para establecer la clave de todas las instancias de TelemetryClient, incluidos los módulos de telemetría estándar, establezca la clave en TelemetryConfiguration.Active. Hágalo en un método de inicialización, como global.aspx.cs, en un servicio de ASP.NET:

```C#

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey = 
          // - for example -
          WebConfigurationManager.Settings["ikey"];
      //...
```

Si solo desea enviar un conjunto específico de eventos a un recurso diferente, puede establecer la clave para un TelemetryClient específico:

```C#

    var tc = new TelemetryClient();
    tc.Context.InstrumentationKey = "----- my key ----";
    tc.TrackEvent("myEvent");
    // ...

```

[Obtenga más información acerca de la API][api].

Para obtener una nueva clave, [cree un nuevo recurso en el portal de Application Insights][new].

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-asp-net-exceptions.md
[netlogs]: app-insights-asp-net-trace-logs.md
[new]: app-insights-create-new-resource.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md

<!---HONumber=AcomDC_1203_2015-->