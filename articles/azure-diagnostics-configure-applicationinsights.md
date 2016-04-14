<properties
   pageTitle="Configuración de Diagnósticos de Azure para enviar datos a Application Insights | Microsoft Azure"
   description="Actualice la configuración pública de Diagnósticos de Azure para enviar datos a Application Insights."
   services="multiple"
   documentationCenter=".net"
   authors="sbtron"
   manager=""
   editor="" />
<tags
   ms.service="application-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/15/2015"
   ms.author="saurabh" />

# Configuración de Diagnósticos de Azure para enviar datos a Application Insights

Los Diagnósticos de Azure almacenan los datos en tablas de Almacenamiento de Azure. Sin embargo, también puede canalizar todos los datos, o un subconjunto de ellos, a Application Insights mediante la configuración de "receptores" y "canales" cuando se usa la extensión de Diagnósticos de Azure 1.5 o posterior.

En este artículo se describe cómo crear la configuración pública de la extensión de Diagnósticos de Azure para enviar datos a Application Insights.

## Configuración de Application Insights como receptor

La extensión de Diagnósticos de Azure 1.5 presenta el elemento **<SinksConfig>** en la configuración pública, que define el *receptor* adicional donde se pueden enviar los datos de Diagnósticos de Azure. Como parte de esto, puede especificar los detalles del recurso de Application Insights donde desea enviar los datos de Diagnósticos de Azure **<SinksConfig>**. Por ejemplo, **SinksConfig** tendría un aspecto similar al siguiente:

	<SinksConfig>
        <Sink name="ApplicationInsights">
          <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>
          <Channels>
            <Channel logLevel="Error" name="MyTopDiagData"  />
            <Channel logLevel="Verbose" name="MyLogData"  />
          </Channels>
        </Sink>
      </SinksConfig> 

Para el elemento **Sink**, el atributo *name* especifica un valor de cadena que se usará para hacer referencia de forma única al receptor. El elemento **ApplicationInsights** especifica la clave de instrumentación del recurso de Application Insights donde se enviarán los datos de Diagnósticos de Azure. Si no dispone de un recurso existente de Application Insights, consulte [Creación de un nuevo recurso de Application Insights](app-insights-create-new-resource.md) para obtener más información sobre cómo crear un recurso y obtener la clave de instrumentación.

Si va a desarrollar un proyecto de servicio en la nube con Azure SDK 2.8, esta clave de instrumentación se rellena automáticamente en la configuración pública según el valor de configuración de servicio **APPINSIGHTS\_INSTRUMENTATIONKEY** al empaquetar el proyecto de servicio en la nube. Consulte [Uso de Application Insights con Diagnósticos de Azure para solucionar problemas de servicios en la nube](cloud-services-dotnet-diagnostics-applicationinsights.md).

El elemento **Channels** le permite definir uno o varios elementos **Channel** para los datos que se enviarán al receptor. El canal actúa como filtro y le permite seleccionar los niveles de registro específicos que querría enviar al receptor. Por ejemplo, puede recopilar registros detallados y enviarlos al almacenamiento, pero puede elegir definir un canal con un nivel de registro de Error y cuando se envíen registros a través de ese canal solo los registros de errores se enviarán a ese receptor. En un elemento **Channel** el atributo *name* se usa para hacer referencia de forma exclusiva a ese canal. El atributo *loglevel* le permite especificar el nivel de registro que le permitirá el canal. Los niveles de registro disponibles en orden de mayor a menor información son: detallado, información, advertencia, error, crítico.

## Envío de datos al receptor de Application Insights
Una vez que se ha definido el receptor de Application Insights, puede enviar datos a ese receptor si agrega el atributo *sink* a los elementos del nodo **DiagnosticMonitorConfiguration**. Al agregar el elemento *sinks* a cada nodo especifica que desea que los datos recopilados de ese nodo y de cualquier nodo situado debajo de éste se envíen al receptor especificado.

Por ejemplo, si desea enviar todos los datos que se van a recopilar con Diagnósticos de Azure puede agregar el atributo *sink* directamente al nodo **DiagnosticMonitorConfiguration**. Establezca el valor de *sinks* en el nombre del receptor que se especificó en **SinkConfig**.

	<DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights">

Si quisiera enviar solo registros de errores al receptor de Application Insights podría establecer el valor *sinks* para que sea el nombre del receptor seguido del nombre del canal separado por un punto ("."). Por ejemplo, para enviar solo los registros de errores al receptor de Application Insights, use el canal MyTopDiagdata que se definió anteriormente en SinksConfig.
 
	<DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights.MyTopDiagdata">

Si solo quisiera enviar registros de aplicación detallados a Application Insights, entonces agregaría el atributo *sinks* al nodo **Logs**.
	
	<Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Verbose" sinks="ApplicationInsights.MyLogData"/>

También puede incluir varios receptores en la configuración en distintos niveles de la jerarquía. En ese caso, el receptor especificado en el nivel superior de la jerarquía actúa como una configuración global y el especificado en los elementos individuales como una invalidación de esa configuración global.

Este es un ejemplo completo del archivo de configuración pública que envía todos los errores a Application Insights (especificados en el nodo **DiagnosticMonitorConfiguration**) y realiza un registro detallado de los registros de aplicación (especificados en el nodo **Logs**).

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
                sinks="ApplicationInsights.MyLogData"/> <!-- This specific info sent to this channel -->
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

![Configuración pública de diagnósticos](./media/azure-diagnostics-configure-applicationinsights/diagnostics-publicconfig.png)

Existen algunas limitaciones que se deben tener en cuenta con esta funcionalidad.

- Los canales solo están diseñados para funcionar con tipos de registros y no con contadores de rendimiento. Si especifica un canal con un elemento de contador de rendimiento se pasará por alto. 
- El nivel de registro de un canal no puede superar el nivel de registro de lo que va a recopilar con Diagnósticos de Azure. Por ejemplo, no puede recopilar errores de registro de aplicación del elemento Logs e intentar enviar registros detallados al receptor de Application Insights. El atributo *scheduledTransferLogLevelFilter* debe recopilar siempre un número de registros igual o superior a los registros que intenta enviar a un receptor. 
- No puede enviar datos de blob recopilados mediante la extensión de Diagnósticos de Azure a Application Insights. Por ejemplo, nada de lo especificado en el nodo *Directories*. En el caso de volcados de memoria, el volcado real se sigue enviando al almacenamiento de blobs y solo se enviará a Application Insights una notificación de que el volcado de memoria se ha generado. 


## Pasos siguientes

- Use [PowerShell](cloud-services-diagnostics-powershell.md) para habilitar la extensión de Diagnósticos de Azure en su aplicación. 
- Use [Visual Studio](vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md) para habilitar la extensión de Diagnósticos de Azure en su aplicación 

<!---HONumber=AcomDC_1217_2015-->