<properties 
	pageTitle="Uso de Powershell para configurar alertas en Application Insights" 
	description="Automatice la configuración de Application Insights para recibir correos electrónicos sobre los cambios en las métricas." 
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
	ms.date="01/22/2016" 
	ms.author="awills"/>
 
# Uso de PowerShell para configurar alertas en Application Insights

Puede automatizar la configuración de [alertas](app-insights-alerts.md) en [Visual Studio Application Insights](app-insights-overview.md).

## Instalación única

Si no ha usado PowerShell con su suscripción de Azure antes:

Instale el módulo de Azure Powershell en el equipo donde desea ejecutar los scripts.

 * Instale el [Instalador de plataforma web de Microsoft (v5 o superior)](http://www.microsoft.com/web/downloads/platform.aspx).
 * Úselo para instalar Microsoft Azure Powershell.


## Conexión a Azure

Inicie Azure PowerShell y [conéctese a su suscripción](powershell-install-configure.md):

```PowerShell

    Add-AzureAccount
    Switch-AzureMode AzureResourceManager
```


## Obtención de alertas

    Get-AlertRule -ResourceGroup "Fabrikam" [-Name "My rule"] [-DetailedOutput]

## Agregar alerta


    Add-AlertRule  -Name "{ALERT NAME}" -Description "{TEXT}" `
     -ResourceGroup "{GROUP NAME}" `
     -ResourceId "/subscriptions/{SUBSCRIPTION ID}/resourcegroups/{GROUP NAME}/providers/microsoft.insights/components/{APP RESOURCE NAME}" `
     -MetricName "{METRIC NAME}" `
     -Operator GreaterThan  `
     -Threshold {NUMBER}   `
     -WindowSize {HH:MM:SS}  `
     [-SendEmailToServiceOwners] `
     [-CustomEmails "EMAIL1@X.COM","EMAIL2@Y.COM" ] `
     -Location "East US"
     -RuleType Metric



## Ejemplo 1

Enviarme un correo electrónico si la respuesta del servidor a las solicitudes HTTP, calculada durante 5 minutos, tarda más de 1 segundo. Mi recurso de Application Insights se denomina IceCreamWebApp, y está en el grupo de recursos Fabrikam. Soy el propietario de la suscripción de Azure.

El GUID es el id. de la suscripción (no la clave de instrumentación de la aplicación).

    Add-AlertRule -Name "slow responses" `
     -Description "email me if the server responds slowly" `
     -ResourceGroup "Fabrikam" `
     -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Fabrikam/providers/microsoft.insights/components/IceCreamWebApp" `
     -MetricName "request.duration" `
     -Operator GreaterThan `
     -Threshold 1 `
     -WindowSize 00:05:00 `
     -SendEmailToServiceOwners `
     -Location "East US" -RuleType Metric

## Ejemplo 2

Tengo una aplicación en la que uso [TrackMetric()](app-insights-api-custom-events-metrics.md#track-metric) para informar de una métrica denominada "salesPerHour". Enviar un correo electrónico a mis colegas si "salesPerHour" es menor que 100, calculado durante 24 horas.

    Add-AlertRule -Name "poor sales" `
     -Description "slow sales alert" `
     -ResourceGroup "Fabrikam" `
     -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Fabrikam/providers/microsoft.insights/components/IceCreamWebApp" `
     -MetricName "salesPerHour" `
     -Operator LessThan `
     -Threshold 100 `
     -WindowSize 24:00:00 `
     -CustomEmails "satish@fabrikam.com","lei@fabrikam.com" `
     -Location "East US" -RuleType Metric

La misma regla puede usarse con la métrica notificada mediante el [parámetro de medida](app-insights-api-custom-events-metrics.md#properties) de otra llamada de seguimiento, como TrackEvent o trackPageView.

## Nombres de métrica

Nombre de métrica | Nombre de pantalla | Descripción
---|---|---
`basicExceptionBrowser.count`|Excepciones de explorador|Recuento de excepciones no detectadas en el explorador.
`basicExceptionServer.count`|Excepciones de servidor|Número de excepciones no controladas producidas por la aplicación
`clientPerformance.clientProcess.value`|Tiempo de procesamiento del cliente|Tiempo transcurrido entre la recepción del último byte de un documento hasta que se carga el DOM. Todavía se pueden procesar solicitudes asincrónicas.
`clientPerformance.networkConnection.value`|Tiempo de conexión de red de carga de página| Tiempo que tarda el explorador en conectarse a la red. Puede ser 0 si se almacena en caché.
`clientPerformance.receiveRequest.value`|Tiempo de recepción de respuesta| Tiempo que transcurre entre que el explorador envía la solicitud hasta que comienza a recibir la respuesta.
`clientPerformance.sendRequest.value`|Tiempo de solicitud de envío| Tiempo que tarda el explorador en enviar la solicitud.
`clientPerformance.total.value`|Tiempo de carga de página del explorador|Tiempo que transcurre entre la solicitud del usuario hasta que se cargan el DOM, las hojas de estilo, los scripts y las imágenes.
`performanceCounter.available_bytes.value`|Memoria disponible|Memoria física inmediatamente disponible para un proceso o para su uso por parte del sistema.
`performanceCounter.io_data_bytes_per_sec.value`|Velocidad de E/S del proceso|Número total de bytes por segundo leídos y escritos en los archivos, la red y los dispositivos.
`performanceCounter.number_of_exceps_thrown_per_sec`|velocidad de excepciones|Excepciones iniciadas por segundo.
`performanceCounter.percentage_processor_time.value`|CPU de procesos|El porcentaje de tiempo transcurrido de todos los subprocesos del proceso usados por el procesador en las instrucciones de ejecución del proceso de aplicaciones.
`performanceCounter.percentage_processor_total.value`|Tiempo de procesador|El porcentaje de tiempo que invierte el procesador en subprocesos no inactivos.
`performanceCounter.process_private_bytes.value`|Bytes privados del proceso|Memoria asignada exclusivamente a los procesos de la aplicación supervisada.
`performanceCounter.request_execution_time.value`|Tiempo de ejecución de solicitud ASP.NET|Tiempo de ejecución de la solicitud más reciente.
`performanceCounter.requests_in_application_queue.value`|Solicitudes ASP.NET en la cola de ejecución|Longitud de la cola de solicitudes de aplicación.
`performanceCounter.requests_per_sec`|Velocidad de solicitudes ASP.NET|Velocidad de todas las solicitudes a la aplicación por segundo desde ASP.NET.
`remoteDependencyFailed.durationMetric.count`|Errores de dependencia|Recuento de llamadas erróneas realizadas por la aplicación de servidor a recursos externos.
`request.duration`|Tiempo de respuesta del servidor|Tiempo entre que se recibe una solicitud HTTP y se termina de enviar la respuesta.
`request.rate`|Velocidad de solicitudes|Velocidad de todas las solicitudes a la aplicación por segundo.
`requestFailed.count`|Error en las solicitudes|Recuento de solicitudes HTTP que dieron lugar a un código de respuesta >= 400 
`view.count`|Vistas de página|Recuento de solicitudes de usuario de cliente de una página web. Se filtra el tráfico sintético.
{el nombre de métrica personalizado}|{El nombre de métrica}|El valor de métrica notificado por [TrackMetric](app-insights-api-custom-events-metrics.md#track-metric) o en el [parámetro de medidas de una llamada de seguimiento](app-insights-api-custom-events-metrics.md#properties).

Las métricas se envían por módulos de telemetría diferentes:

Grupo de métricas | Módulo de recopilador
---|---
basicExceptionBrowser,<br/>clientPerformance,<br/>view | [JavaScript de explorador](app-insights-javascript.md)
performanceCounter | [Rendimiento](app-insights-configuration-with-applicationinsights-config.md#nuget-package-3)
remoteDependencyFailed| [Dependencia](app-insights-configuration-with-applicationinsights-config.md#nuget-package-1)
request,<br/>requestFailed|[Solicitud de servidor](app-insights-configuration-with-applicationinsights-config.md#nuget-package-2)


## Consulte también


* [Script para configurar Application Insights](app-insights-powershell-script-create-resource.md)
* [Crear Application Insights y recursos de pruebas web a partir de plantillas](app-insights-powershell.md)
* [Uso de PowerShell para enviar Diagnósticos de Azure a Application Insights](app-insights-powershell-azure-diagnostics.md)


 

<!---HONumber=AcomDC_0128_2016-->