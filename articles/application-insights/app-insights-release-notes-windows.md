<properties 
	pageTitle="Notas de la versión de Application Insights para Windows" 
	description="Las actualizaciones más recientes del SDK de la Tienda Windows." 
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
	ms.date="02/12/2016" 
	ms.author="joshweb"/>
 
# Notas de la versión del SDK de Application Insights para la Tienda Windows Phone

El [SDK de Application Insights](app-insights-windows-get-started.md) envía telemetría acerca de la aplicación activa a [Application Insights](https://azure.microsoft.com/services/application-insights/), donde puede analizar su uso y el rendimiento.


#### Para instalar el SDK en su aplicación

Consulte [Introducción a Application Insights para aplicaciones de la Tienda Windows Phone](app-insights-windows-get-started.md)

#### Para actualizar al SDK más reciente 

* Realice una copia de ApplicationInsights.config para mantener las personalizaciones realizadas.
* En el Explorador de soluciones, haga clic con el botón derecho en el proyecto y seleccione **Administrar paquetes de NuGet**.
* Establezca el filtro para mostrar los paquetes instalados. 
* Seleccione los paquetes de Application Insights instalados y elija Actualizar.
* Compare las versiones antigua y nueva de ApplicationInsights.config. Vuelva a combinar las personalizaciones realizadas en la versión anterior.
* Vuelva a generar la solución.

## Versión 1.1.1

### Windows SDK

- Corrija la situación cuando un equipo deja de responder durante un bloqueo mientras se usa el SDK de Silverlight de Windows Phone. Después de este cambio, se conservará en el disco cualquier bloqueo que se produzca unos 2 segundos después de la llamada a WindowsAppInitialier.InitializeAsync(...) y se enviará la próxima vez que se inicie la aplicación. Si se produce un bloqueo antes de unos 2 segundos después de la llamada, se omitirá.  
- Establezca las dependencias de NuGet en una versión específica de Core y Microsoft.ApplicationInsights.PersistenceChannel (v1.2.3).   

### SDK básico

- Core se administra en github. Encontrará las notas de la futura versión del SDK de Core [en github](http://github.com/Microsoft/ApplicationInsights-dotnet/releases)

## Versión 1.1

### SDK básico

- El SDK ahora incluye el nuevo tipo de telemetría ```DependencyTelemetry```, que contiene información sobre la llamada de dependencia de la aplicación
- El nuevo método ```TelemetryClient.TrackDependency``` permite enviar información sobre las llamadas de dependencia de aplicación

## Versión 1.0.0

### SDK para aplicaciones de Windows

- Nueva inicialización para aplicaciones de Windows. La nueva clase `WindowsAppInitializer`, con el método `InitializeAsync()`, permite la inicialización de arranque de la colección de SDK. Este cambio permite un control más preciso y mejoras considerables del rendimiento de la inicialización de aplicaciones respecto a la técnica de ApplicationInsights.config anterior.
- DeveloperMode ya no se establece automáticamente. Para cambiar el comportamiento de DeveloperMode, debe especificarlo en el código.
- El paquete de NuGet ya no inyecta ApplicationInsights.config. Se recomienda usar el nuevo WindowsAppInitializer al agregar manualmente el paquete de NuGet.
- ApplicationInsights.config solo lee `<InstrumentationKey>`; el resto de opciones de configuración se ignoran y se da prioridad a la configuración de WindowsAppInitializer.
- El SDK recopilará automáticamente Store Market.
- Muchas correcciones de errores, mejoras de estabilidad y mejoras de rendimiento.

### SDK básico

- El archivo ApplicationInsights.config ya no es necesario y no lo agregó el paquete de NuGet. La configuración se puede especificar totalmente en el código.
- El paquete de NuGet ya no agregará un archivo de destinos a la solución. Esto quita la configuración automática de DeveloperMode durante una compilación de depuración. DeveloperMode debe establecerse manualmente en el código.

## Versión 0.17

### SDK para aplicaciones de Windows

- El SDK para aplicaciones de Windows ahora admite aplicaciones universales de Windows creadas en Windows 10 Technical Preview y VS 2015 RC.

### SDK básico

- Los valores predeterminados de TelemetryClient se inicializarán con InMemoryChannel.
- Se agregó una API nueva, `TelemetryClient.Flush()`. Este método Flush desencadenará una carga inmediata de bloqueo de toda la telemetría registrada en el cliente. Esto permite la activación manual de la carga antes del cierre del proceso.
- El paquete de NuGet agregó un destino de .NET 4.5. Este destino no tiene dependencias externas (se quitaron las dependencias BCL y EventSource).

## Versión 0.16 

Vista previa del 28-04-2015

- El SDK es ahora compatible con la plataforma de destino DNX para permitir la supervisión de aplicaciones del [marco de trabajo principal .NET](http://www.dotnetfoundation.org/NETCore5).
- Las instancias de ```TelemetryClient``` ya no almacenan en caché la clave de instrumentación. Ahora, si no estableció la clave de instrumentación explícitamente en ```TelemetryClient```, ```InstrumentationKey``` devolverá null. Corrige un problema en el que cuando establece ```TelemetryConfiguration.Active.InstrumentationKey``` después de que ya se recopilaran algunos datos de telemetría, los módulos de telemetría, como el recopilador de dependencias, la recopilación de datos de solicitudes web y el recolector de contadores de rendimiento usarán la nueva clave de instrumentación.

## Version 0.15

- Nueva propiedad ```Operation.SyntheticSource``` ahora disponible en ```TelemetryContext```. Ahora puede marcar los elementos de telemetría como "no es un tráfico de usuario real" y especificar cómo se generó este tráfico. Por ejemplo, si define esta propiedad, puede distinguir el tráfico de la automatización de pruebas del tráfico de prueba de carga.
- La lógica de canal se movió al NuGet independiente denominado Microsoft.ApplicationInsights.PersistenceChannel. El canal predeterminado se denomina ahora InMemoryChannel.
- El nuevo método ```TelemetryClient.Flush``` permite vaciar elementos de telemetría del búfer de forma sincrónica.

## Versión 0.13

No existen notas de la versión para versiones anteriores.

<!---HONumber=AcomDC_0218_2016-->