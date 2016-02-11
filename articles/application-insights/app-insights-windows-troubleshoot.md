<properties 
	pageTitle="Solución de problemas de Application Insights en dispositivos Windows" 
	description="Guía de solución de problemas y preguntas y respuestas para Application Insights en dispositivos Windows." 
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
	ms.date="11/24/2015" 
	ms.author="awills"/>
 
# Solución de problemas y preguntas frecuentes para Application Insights en dispositivos Windows

¿Tiene dudas o problemas con [Visual Studio Application Insights en Windows][windows]? a continuación se incluyen algunas sugerencias.



## No aparecen datos 

*He agregado Application Insights correctamente y he ejecutado mi aplicación, pero no aparecen datos en el portal.*

* Espere un minuto y haga clic en Actualizar,
* Compruebe que tenga una clave de instrumentación definida en el archivo ApplicationInsights.config y que sea la misma que la clave del portal de Application Insights. Para ver la clave, haga clic en Essentials en la hoja de información general.
* Asegúrese de que la aplicación [solicita acceso de red saliente](https://msdn.microsoft.com/library/windows/apps/hh452752.aspx).
* ¿Hay un firewall entre el dispositivo de prueba o el emulador y el portal de Application Insights? Puede que tenga que abrir los puertos TCP 80 y 443 para el tráfico saliente a dc.services.visualstudio.com y f5.services.visualstudio.com.
* En el panel de inicio de Microsoft Azure, observe el mapa de estado del servicio. Si hay algunas indicaciones de alerta, espere hasta que hayan vuelto a su estado correcto y después cierre y vuelva a abrir el cuadro de la aplicación de Application Insights.


#### Solía ver datos, pero ya no sucede esto.

* Compruebe el [blog de estado](http://blogs.msdn.com/b/applicationinsights-status/).
* ¿Ha alcanzado su cuota mensual de puntos de datos? Abra Configuración/Cuotas y Precios para averiguarlo. Si es así, puede actualizar el plan o pagar para obtener capacidad adicional. Consulte el [esquema de precios](https://azure.microsoft.com/pricing/details/application-insights/).


## ¿Cómo puedo agregar Application Insights a una aplicación universal?

Si va a crear una nueva solución en Visual Studio 2015, simplemente seleccione la opción de agregar Application Insights en el cuadro de diálogo de nuevo proyecto. Así se enviarán datos de telemetría de todos los tipos de aplicación de destino al mismo recurso de Application Insights.

Si ya creó la solución de aplicación universal, haga clic en cada proyecto principal y seleccione **Agregar Application Insights**.



## Deshabilitación de la telemetría

*¿Cómo puedo deshabilitar la recopilación de telemetría?*

En el código:

    TelemetryConfiguration config = TelemetryConfiguration.Active;
    config.TrackingIsDisabled = true;

## Cambio del recurso de destino

*¿Cómo puedo cambiar el recurso de Application Insights al que mi proyecto envía datos?*

En la nueva hoja de información general de Application Insights, abra Essentials y copie la clave de instrumentación.

Pegue la clave en el archivo ApplicationInsights.config en el nodo `<InstrumentationKey>`.

O bien, si desea cambiar los destinos en tiempo de ejecución, use:

     var telemetry = new TelemetryClient();
     telemetry.Context.InstrumentationKey = newKey;
    
## ¿Cómo superviso una aplicación cliente/servidor?

Existen dos formas de hacerlo:

* Cree dos recursos de Application Insights (con claves de instrumentación diferentes) para el cliente y el servidor. Pero créelas en el mismo grupo de recursos de Azure. Así podrá cambiar fácilmente entre uno y otro.
* Use un recurso de Application Insights y coloque su clave de instrumentación en los proyectos de cliente y servidor. Luego, puede correlacionar las métricas y los eventos de los dos orígenes.

Para ayudar a correlacionar eventos en el cliente y el servidor, genere un id. de operación para cada solicitud. Transmítalo entre el cliente y el servidor y agréguelo a la telemetría en ambos extremos:

    telemetry.Context.OperationId = opid;


## Pantalla de inicio de Azure

*Estoy mirando el [Portal de Azure](https://portal.azure.com). ¿El mapa indica algo sobre mi aplicación?*

No, este muestra el estado de los servidores de Azure en todo el mundo.

*¿Cómo puedo encontrar datos sobre mi aplicación desde el panel de inicio de Azure (pantalla principal)?*

Suponiendo que [ya ha configurado su aplicación para Application Insights][windows], haga clic en Examinar, seleccione Application Insights y elija el recurso que ha creado para su aplicación. Para acceder más rápido en el futuro, puede anclar el recurso al panel de inicio.

## Retención de datos 

*¿Cuánto tiempo se retienen los datos en el portal? ¿Es seguro?*

Consulte [Privacidad y retención de los datos][data].

## Pasos siguientes

*He configurado Application Insights para mi aplicación de servidor Java. ¿Qué más puedo hacer?*

* [Supervisar la disponibilidad de sus páginas web][availability]
* [Supervisar el uso de páginas web][usage]
* [Realizar el seguimiento del uso y diagnosticar los problemas en las aplicaciones de su dispositivo][platforms]
* [Escribir código para realizar un seguimiento del uso de la aplicación][track]
* [Captura de registros de diagnóstico][javalogs]


## Obtener ayuda

* [Desbordamiento de la pila](http://stackoverflow.com/questions/tagged/ms-application-insights)

<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[data]: app-insights-data-retention-privacy.md
[javalogs]: app-insights-java-trace-logs.md
[platforms]: app-insights-platforms.md
[track]: app-insights-api-custom-events-metrics.md
[universal]: app-insights-windows-get-started.md#universal
[usage]: app-insights-web-track-usage.md
[windows]: app-insights-windows-get-started.md

 

<!---HONumber=AcomDC_0128_2016-->