<properties
    pageTitle="Envío de registros de Diagnósticos de Azure a Application Insights"
    description="Configure los detalles de los registros de diagnóstico de Servicios en la nube de Azure que se envían al portal de Application Insights."
    services="application-insights"
    documentationCenter=".net"
    authors="sbtron"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
	ms.tgt_pltfrm="ibiza" 
    ms.devlang="na"
    ms.topic="article"
	ms.date="11/17/2015"
    ms.author="awills"/>

# Configuración del registro de diagnóstico de Azure en Application Insights

Cuando se configura un proyecto de Servicios en la nube o una máquina Virtual en Microsoft Azure, [Azure puede generar un registro de diagnóstico](vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md). Puede hacer que dicho registro se envíe a Application Insights para poder analizarlo junto con los datos de telemetría de diagnóstico y uso que el SDK de Application Insights envía desde la aplicación. El registro de Azure incluye eventos de la administración de la aplicación, como inicio, detención, bloqueos, así como contadores de rendimiento. El registro también incluye llamadas de la aplicación a System.Diagnostics.Trace.

En este artículo se describe la configuración de la captura de diagnóstico en detalle.

Necesitará tener instalado el SDK 2.8 de Azure en Visual Studio.

## Obtención de un recurso de Application Insights

Para lograr la mejor experiencia, [agregue el SDK de Application Insights a cada rol de la aplicación de Servicios en la nube](app-insights-cloudservices.md), o [a cualquier aplicación que vaya a ejecutar en la máquina virtual](app-insights-get-started.md). Luego, puede enviar los datos de diagnóstico para analizarlos y mostrarlos en el mismo recurso de Application Insights.

Como alternativa, si no desea usar el SDK, por ejemplo, porque la aplicación ya esté funcionando, puede [crear un nuevo recurso de Application Insights](app-insights-create-new-resource.md) en el Portal de Azure. Elija **Diagnósticos de Azure** como el tipo de aplicación.


## Envío de diagnósticos de Azure a Application Insights

Si puede actualizar el proyecto de aplicación, en Visual Studio, seleccione cada rol, elija sus propiedades y, en la pestaña Configuración, seleccione **Enviar diagnósticos a Application Insights**.

Si la aplicación ya está funcionando, use el Explorador de servidores o el explorador de Servicios en la nube de Visual Studio para abrir las propiedades de la aplicación. Seleccione **Enviar diagnósticos a Application Insights**.

En cada caso se le preguntará por los detalles del recurso de Application Insights que ha creado.

[Más información sobre la configuración de Application Insights para una aplicación de Servicios en la nube](app-insights-cloudservices.md).

## Configuración del adaptador de Diagnósticos de Azure

Siga leyendo solo si quiere seleccionar las partes del registro que envía a Application Insights. De forma predeterminada, se envía todo: eventos de Microsoft Azure, contadores de rendimiento y llamadas de seguimiento de la aplicación a System.Diagnostics.Trace.

Los Diagnósticos de Azure almacenan los datos en tablas de Almacenamiento de Azure. Sin embargo, también puede canalizar todos los datos, o un subconjunto de ellos, a Application Insights mediante la configuración de "receptores" y "canales" cuando se usa la extensión de Diagnósticos de Azure 1.5 o posterior.

### Configuración de Application Insights como receptor

Cuando usa las propiedades de rol para establecer "Enviar datos a Application Insights", el SDK de Azure (2.8 o posterior) agrega un elemento `<SinksConfig>` al [archivo púbico de configuración de Diagnósticos de Azure](https://msdn.microsoft.com/library/azure/dn782207.aspx) del rol.

`<SinksConfig>` define el receptor adicional donde se pueden enviar los datos de diagnóstico de Azure. Un ejemplo de `SinksConfig` sería parecido a este:

```xml

    <SinksConfig>
     <Sink name="ApplicationInsights">
      <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>
      <Channels>
        <Channel logLevel="Error" name="MyTopDiagData"  />
        <Channel logLevel="Verbose" name="MyLogData"  />
      </Channels>
     </Sink>
    </SinksConfig> 

```

El elemento `ApplicationInsights` especifica la clave de instrumentación que identifica el recurso de Application Insights al que se enviarán los datos de diagnóstico de Azure. Al seleccionar el recurso, se rellena automáticamente según la configuración de servicio de `APPINSIGHTS_INSTRUMENTATIONKEY`. (Si desea establecerlo manualmente, obtenga la clave de la lista desplegable Essentials del recurso).

`Channels` define los datos que se enviarán al receptor. El canal actúa como filtro. El atributo `loglevel` le permite especificar el nivel de registro que enviará el canal. Los valores disponibles son: `{Verbose, Information, Warning, Error, Critical}`.

### Envío de datos al receptor

Para enviar datos al receptor de Application Insights, agregue los atributos de receptor en el nodo DiagnosticMonitorConfiguration. Al agregar el elemento sinks a cada nodo, se especifica que desea que los datos recopilados de ese nodo y de cualquier nodo situado debajo de éste se envíen al receptor especificado.

Por ejemplo, el valor predeterminado creado por el SDK de Azure es enviar todos los datos de diagnóstico de Azure:

```xml

    <DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights">
```

Pero si desea enviar solo los registros de error, califique el nombre del receptor con un nombre de canal:

```xml

    <DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights.MyTopDiagdata">
```

Observe que estamos usando el nombre del receptor que hemos definido, junto con el nombre de un canal que definimos anteriormente.

Si solo quisiera enviar registros de aplicación detallados a Application Insights, entonces agregaría el atributo sinks al nodo `Logs`.

```xml

    <Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Verbose" sinks="ApplicationInsights.MyLogData"/>
```

También puede incluir varios receptores en la configuración en distintos niveles de la jerarquía. En ese caso, el receptor especificado en el nivel superior de la jerarquía actúa como una configuración global y el especificado en los elementos individuales como una invalidación de esa configuración global.

Este es un ejemplo completo del archivo de configuración pública que envía todos los errores a Application Insights (especificados en el nodo `DiagnosticMonitorConfiguration`) y además los registros de nivel detallado de los registros de aplicación (especificados en el nodo `Logs`).

```xml

    <WadCfg>
     <DiagnosticMonitorConfiguration overallQuotaInMB="4096"
       sinks="ApplicationInsights.MyTopDiagData"> <!-- All info below sent to this channel -->
      <DiagnosticInfrastructureLogs />
      <PerformanceCounters>
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" sinks="ApplicationInsights.MyLogData/>
        <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
        <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
      </PerformanceCounters>
      <WindowsEventLog scheduledTransferPeriod="PT1M">
        <DataSource name="Application!*" />
      </WindowsEventLog>
      <Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Verbose"
            sinks="ApplicationInsights.MyLogData"/> 
       <!-- This specific info sent to this channel -->
     </DiagnosticMonitorConfiguration>

     <SinksConfig>
      <Sink name="ApplicationInsights">
        <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>
        <Channels>
          <Channel logLevel="Error" name="MyTopDiagData"  />
          <Channel logLevel="Verbose" name="MyLogData"  />
        </Channels>
      </Sink>
     </SinksConfig>
    </WadCfg>
```

![](./media/app-insights-azure-diagnostics/diagnostics-publicconfig.png)

Existen algunas limitaciones que se deben tener en cuenta con esta funcionalidad:

* Los canales solo están diseñados para funcionar con tipos de registros y no con contadores de rendimiento. Si especifica un canal con un elemento de contador de rendimiento se pasará por alto. 
* El nivel de registro de un canal no puede superar el nivel de registro de lo que va a recopilar con Diagnósticos de Azure. Por ejemplo, no puede recopilar errores de registro de aplicación del elemento Logs e intentar enviar registros detallados al receptor de Application Insights. El atributo scheduledTransferLogLevelFilter debe recopilar siempre un número de registros igual o superior a los registros que intenta enviar a un receptor. 
* No puede enviar datos de blob recopilados mediante la extensión de Diagnósticos de Azure a Application Insights. Por ejemplo, nada de lo especificado en el nodo Directories. En el caso de volcados de memoria, el volcado real se sigue enviando al almacenamiento de blobs y solo se enviará a Application Insights una notificación de que el volcado de memoria se ha generado. 

## Temas relacionados

* [Supervisión de Servicios en la nube de Azure con Application Insights](app-insights-cloudservices.md)
* [Uso de PowerShell para enviar diagnósticos de Azure a Application Insights](app-insights-powershell-azure-diagnostics.md)
* [Archivo de configuración de Diagnósticos de Azure](https://msdn.microsoft.com/library/azure/dn782207.aspx)

<!---HONumber=AcomDC_1125_2015-->